---
title: 지리적으로 분산된 Azure SQL Database 솔루션 구현 | Microsoft Docs
description: Azure SQL Database와 응용 프로그램을 복제된 데이터베이스로 장애 조치하도록 구성하고 장애 조치를 테스트합니다.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,business continuity
ms.topic: tutorial
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: fbd239c3c8c11b1907a6d28eb95d2c0ad26cfe61
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="implement-a-geo-distributed-database"></a>지리적으로 분산된 데이터베이스 구현

이 자습서에서는 Azure SQL Database와 응용 프로그램을 원격 지역으로 장애 조치하도록 구성한 다음 장애 조치 계획을 테스트합니다. 다음 방법에 대해 알아봅니다. 

> [!div class="checklist"]
> * 데이터베이스 사용자를 만들고 권한 부여
> * 데이터베이스 수준 방화벽 규칙 설정
> * [지역에서 복제 장애 조치(failover) 그룹](sql-database-geo-replication-overview.md)
> * Azure SQL Database를 쿼리하기 위한 Java 응용 프로그램 만들기 및 컴파일
> * 재해 복구 훈련 수행

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.


## <a name="prerequisites"></a>필수 조건

이 자습서를 수행하려면 다음 필수 조건이 완료되었는지 확인합니다.

- 최신 [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs)을 설치했습니다. 
- Azure SQL Database를 설치했습니다. 이 자습서에서는 다음 빠른 시작 중 하나에서 **mySampleDatabase** 이름의 AdventureWorksLT 샘플 데이터베이스를 사용합니다.

   - [DB 만들기 - 포털](sql-database-get-started-portal.md)
   - [DB 만들기 - CLI](sql-database-get-started-cli.md)
   - [DB 만들기 - PowerShell](sql-database-get-started-powershell.md)

- 데이터베이스에서 SQL 스크립트를 실행할 메서드를 식별한 경우 다음 쿼리 도구 중 하나를 사용할 수 있습니다.
   - [Azure Portal](https://portal.azure.com)의 쿼리 편집기. Azure Portal에서 쿼리 편집기를 사용하는 방법에 대한 자세한 내용은 [쿼리 편집기를 사용하여 연결 및 쿼리](sql-database-get-started-portal.md#query-the-sql-database)를 참조하세요.
   - Microsoft Windows의 SQL Server에서 SQL Database에 이르는 모든 SQL 인프라를 관리할 수 있는 통합된 환경인 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)의 최신 버전.
   - Microsoft SQL Server, Azure SQL Database 및 SQL Data Warehouse를 쿼리하는 [mssql 확장](https://aka.ms/mssql-marketplace)을 비롯한 여러 확장을 지원하는 Linux, macOS, Windows용 그래픽 코드 편집기인 [Visual Studio Code](https://code.visualstudio.com/docs)의 최신 버전. Azure SQL Database에서 이 도구를 사용하는 방법에 대한 자세한 내용은 [VS Code를 사용하여 연결 및 쿼리](sql-database-connect-query-vscode.md)를 참조하세요. 

## <a name="create-database-users-and-grant-permissions"></a>데이터베이스 사용자를 만들고 권한 부여

다음 쿼리 도구 중 하나를 사용하여 데이터베이스에 연결하고 사용자 계정을 만듭니다.

- Azure Portal의 쿼리 편집기
- SQL Server Management Studio
- Visual Studio Code

이러한 사용자 계정은 보조 서버에 자동으로 복제되고 계속 동기화됩니다. SQL Server Management Studio 또는 Visual Studio Code를 사용하려면 아직 방화벽을 구성하지 않은 IP 주소의 클라이언트에서 연결하는 경우 방화벽 규칙을 구성해야 할 수도 있습니다. 자세한 단계에 대해서는 [서버 수준 방화벽 규칙 만들기](sql-database-get-started-portal.md#create-a-server-level-firewall-rule)를 참조하세요.

- 쿼리 창에서 다음 쿼리를 실행하여 데이터베이스에 두 개의 사용자 계정을 만듭니다. 이 스크립트는 **db_owner** 권한을 **app_admin** 계정에 부여하고 **SELECT** 및 **UPDATE** 권한을 **app_user** 계정에 부여합니다. 

   ```sql
   CREATE USER app_admin WITH PASSWORD = 'ChangeYourPassword1';
   --Add SQL user to db_owner role
   ALTER ROLE db_owner ADD MEMBER app_admin; 
   --Create additional SQL user
   CREATE USER app_user WITH PASSWORD = 'ChangeYourPassword1';
   --grant permission to SalesLT schema
   GRANT SELECT, INSERT, DELETE, UPDATE ON SalesLT.Product TO app_user;
   ```

## <a name="create-database-level-firewall"></a>데이터베이스 수준 방화벽 만들기

SQL Database에 대한 [데이터베이스 수준 방화벽 규칙](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)을 만듭니다. 이 데이터베이스 수준 방화벽 규칙은 이 자습서에서 만드는 보조 서버에 자동으로 복제됩니다. 이 자습서에서는 간단하게 이 자습서의 단계를 수행하는 컴퓨터의 공용 IP 주소를 사용합니다. 현재 컴퓨터에 대한 서버 수준 방화벽 규칙에 사용되는 IP 주소를 확인하려면 [서버 수준 방화벽 만들기](sql-database-get-started-portal.md#create-a-server-level-firewall-rule)를 참조하세요.  

- 열린 쿼리 창에서 이전 쿼리를 다음 쿼리로 바꾸고, IP 주소를 해당 환경의 적절한 IP 주소로 바꿉니다.  

   ```sql
   -- Create database-level firewall setting for your public IP address
   EXECUTE sp_set_database_firewall_rule @name = N'myGeoReplicationFirewallRule',@start_ip_address = '0.0.0.0', @end_ip_address = '0.0.0.0';
   ```

## <a name="create-an-active-geo-replication-auto-failover-group"></a>활성 지역 복제 자동 장애 조치(failover) 그룹 만들기 

Azure PowerShell을 사용하여 Azure 지역의 기존 Azure SQL Server와 새로운 빈 Azure SQL Server 사이에 [활성 지역 복제 자동 장애 조치(failover) 그룹](sql-database-geo-replication-overview.md)을 만들고 나서 장애 조치(failover) 그룹에 샘플 데이터베이스를 추가합니다.

> [!IMPORTANT]
> 이러한 cmdlet에는 Azure PowerShell 4.0이 필요합니다. [!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install-no-ssh.md)]
>

1. 기존 서버 및 샘플 데이터베이스의 값을 사용하여 PowerShell 스크립트의 변수를 채우고 장애 조치(failover) 그룹 이름에 전역적으로 고유한 값을 제공합니다.

   ```powershell
   $adminlogin = "ServerAdmin"
   $password = "ChangeYourAdminPassword1"
   $myresourcegroupname = "<your resource group name>"
   $mylocation = "<your resource group location>"
   $myservername = "<your existing server name>"
   $mydatabasename = "mySampleDatabase"
   $mydrlocation = "<your disaster recovery location>"
   $mydrservername = "<your disaster recovery server name>"
   $myfailovergroupname = "<your unique failover group name>"
   ```

2. 장애 조치 지역에 빈 백업 서버를 만듭니다.

   ```powershell
   $mydrserver = New-AzureRmSqlServer -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername `
      -Location $mydrlocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $mydrserver   
   ```

3. 두 서버 사이에 장애 조치(failover) 그룹을 만듭니다.

   ```powershell
   $myfailovergroup = New-AzureRMSqlDatabaseFailoverGroup `
      –ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -PartnerServerName $mydrservername  `
      –FailoverGroupName $myfailovergroupname `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $myfailovergroup   
   ```

4. 장애 조치(failover) 그룹에 데이터베이스를 추가합니다.

   ```powershell
   $myfailovergroup = Get-AzureRmSqlDatabase `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -DatabaseName $mydatabasename | `
    Add-AzureRmSqlDatabaseToFailoverGroup `
      -ResourceGroupName $myresourcegroupname ` `
      -ServerName $myservername `
      -FailoverGroupName $myfailovergroupname
   $myfailovergroup   
   ```

## <a name="install-java-software"></a>Java 소프트웨어 설치

이 섹션의 단계에서는 Java를 사용하여 개발하는 것에 익숙하고 Azure SQL Database 작업에 익숙하지 않다고 가정합니다. 

### <a name="mac-os"></a>**Mac OS**
터미널을 열고 Java 프로젝트를 만들려는 디렉터리로 이동합니다. 다음 명령을 입력하여 **brew** 및 **Maven**을 설치합니다. 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install maven
```

Java 및 Maven 환경을 설치하고 구성하는 방법에 대한 자세한 지침을 보려면 [SQL Server를 사용하여 앱 빌드](https://www.microsoft.com/sql-server/developer-get-started/)로 이동하여 **Java**, **MacOS**를 차례로 선택하고 1.2 및 1.3단계에서 Java 및 Maven 구성에 대한 자세한 지침을 따르세요.

### <a name="linux-ubuntu"></a>**Linux(Ubuntu)**
터미널을 열고 Java 프로젝트를 만들려는 디렉터리로 이동합니다. 다음 명령을 입력하여 **Maven**을 설치합니다.

```bash
sudo apt-get install maven
```

Java 및 Maven 환경을 설치하고 구성하는 방법에 대한 자세한 지침을 보려면 [SQL Server를 사용하여 앱 빌드](https://www.microsoft.com/sql-server/developer-get-started/)로 이동하여 **Java**, **Ubuntu**를 차례로 선택하고 1.2, 1.3 및 1.4단계에서 Java 및 Maven 구성에 대한 자세한 지침을 따르세요.

### <a name="windows"></a>**Windows**
공식 설치 관리자를 사용하여 [Maven](https://maven.apache.org/download.cgi)을 설치합니다. Maven을 사용하여 종속성을 관리하고 Java 프로젝트를 빌드, 테스트 및 실행합니다. Java 및 Maven 환경을 설치하고 구성하는 방법에 대한 자세한 지침을 보려면 [SQL Server를 사용하여 앱 빌드](https://www.microsoft.com/sql-server/developer-get-started/)로 이동하여 **Java**, Windows를 차례로 선택하고 1.2 및 1.3단계에서 Java 및 Maven 구성에 대한 자세한 지침을 따르세요.

## <a name="create-sqldbsample-project"></a>SqlDbSample 프로젝트 만들기

1. 명령 콘솔(예: Bash)에서 Maven 프로젝트를 만듭니다. 
   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```
2. **Y**를 입력하고 **Enter**를 클릭합니다.
3. 디렉터리를 새로 만든 프로젝트로 변경합니다.

   ```bash
   cd SqlDbSamples
   ```

4. 원하는 편집기를 사용하여 프로젝트 폴더에서 pom.xml 파일을 엽니다. 

5. 원하는 텍스트 편집기를 열고 다음 줄을 복사하여 pom.xml 파일에 붙여넣는 방식으로 SQL Server용 Microsoft JDBC Driver 종속성을 Maven 프로젝트에 추가합니다. 파일에 미리 입력된 기존 값을 덮어쓰지 마세요. JDBC 종속성은 더 큰 “dependencies” 섹션( )에 붙여넣어야 합니다.   

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

6. 다음 “properties” 섹션을 pom.xml 파일의 "dependencies" 섹션 뒤에 추가하여 프로젝트를 컴파일할 대상 Java 버전을 지정합니다. 

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```
7. 다음 "build" 섹션을 pom.xml 파일의 "properties" 섹션 뒤에 추가하여 JAR에서 매니페스트 파일을 지원합니다.       

   ```xml
   <build>
     <plugins>
       <plugin>
         <groupId>org.apache.maven.plugins</groupId>
         <artifactId>maven-jar-plugin</artifactId>
         <version>3.0.0</version>
         <configuration>
           <archive>
             <manifest>
               <mainClass>com.sqldbsamples.App</mainClass>
             </manifest>
           </archive>
        </configuration>
       </plugin>
     </plugins>
   </build>
   ```
8. pom.xml 파일을 저장하고 닫습니다.
9. App.java 파일(C:\apache-maven-3.5.0\SqlDbSample\src\main\java\com\sqldbsamples\App.java)을 열고 기존 콘텐츠를 다음 콘텐츠로 바꿉니다. 장애 조치(failover) 그룹 이름을 사용할 장애 조치(failover) 그룹 이름으로 바꿉니다. 데이터베이스 이름, 사용자 또는 암호의 값을 변경한 경우 해당 값도 변경하세요.

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.Timestamp;
   import java.sql.DriverManager;
   import java.util.Date;
   import java.util.concurrent.TimeUnit;

   public class App {

      private static final String FAILOVER_GROUP_NAME = "myfailovergroupname";
  
      private static final String DB_NAME = "mySampleDatabase";
      private static final String USER = "app_user";
      private static final String PASSWORD = "ChangeYourPassword1";

      private static final String READ_WRITE_URL = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println(""); 

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " + (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " + (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name that we can use to find the product again later
      String sql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL); 
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         pstmt.setInt(2, 200989 + id + 10000);
         pstmt.setString(3, "Blue");
         pstmt.setDouble(4, 75.00);
         pstmt.setDouble(5, 89.99);
         pstmt.setTimestamp(6, new Timestamp(new Date().getTime()));
         return (1 == pstmt.executeUpdate());
      } catch (Exception e) {
         return false;
      }
   }

   private static boolean selectData(int id) {
      // Query the data that was previously inserted into the primary database from the geo replicated database
      String sql = "SELECT Name, Color, ListPrice FROM SalesLT.Product WHERE Name = ?";

      try (Connection connection = DriverManager.getConnection(READ_ONLY_URL); 
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         try (ResultSet resultSet = pstmt.executeQuery()) {
            return resultSet.next();
         }
      } catch (Exception e) {
         return false;
      }
   }

   private static int getHighWaterMarkId() {
      // Query the high water mark id that is stored in the table to be able to make unique inserts 
      String sql = "SELECT MAX(ProductId) FROM SalesLT.Product";
      int result = 1;
        
      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL); 
              Statement stmt = connection.createStatement();
              ResultSet resultSet = stmt.executeQuery(sql)) {
         if (resultSet.next()) {
             result =  resultSet.getInt(1);
            }
         } catch (Exception e) {
          e.printStackTrace();
         }
         return result;
      }
   }
   ```
6. App.java 파일을 저장하고 닫습니다.

## <a name="compile-and-run-the-sqldbsample-project"></a>SqlDbSample 프로젝트 컴파일 및 실행

1. 명령 콘솔에서 다음 명령을 실행합니다.

   ```bash
   mvn package
   ```
2. 완료되면 다음 명령을 실행하여 응용 프로그램을 실행합니다(수동으로 중지하지 않으면 약 1시간 동안 실행됨).

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ```

## <a name="perform-disaster-recovery-drill"></a>재해 복구 훈련 수행

1. 장애 조치(failover) 그룹의 수동 장애 조치(failover)를 호출합니다. 

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
   -ResourceGroupName $myresourcegroupname  `
   -ServerName $mydrservername `
   -FailoverGroupName $myfailovergroupname
   ```

2. 장애 조치(failover) 중에 응용 프로그램 결과를 관찰합니다. DNS 캐시를 새로 고치는 동안 일부 삽입이 실패합니다.     

3. 재해 복구 서버가 수행하는 역할을 파악합니다.

   ```powershell
   $mydrserver.ReplicationRole
   ```

4. 장애 복구(failback)합니다.

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
   -ResourceGroupName $myresourcegroupname  `
   -ServerName $myservername `
   -FailoverGroupName $myfailovergroupname
   ```

5. 장애 복구(failback) 중에 응용 프로그램 결과를 관찰합니다. DNS 캐시를 새로 고치는 동안 일부 삽입이 실패합니다.     

6. 재해 복구 서버가 수행하는 역할을 파악합니다.

   ```powershell
   $fileovergroup = Get-AzureRMSqlDatabaseFailoverGroup `
      -FailoverGroupName $myfailovergroupname `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername
   $fileovergroup.ReplicationRole
   ```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure SQL 데이터베이스와 응용 프로그램을 원격 지역으로 장애 조치(failover)하도록 구성한 다음, 장애 조치(failover) 계획을 테스트하는 것을 알아봤습니다.  다음 방법에 대해 알아보았습니다. 

> [!div class="checklist"]
> * 데이터베이스 사용자를 만들고 권한 부여
> * 데이터베이스 수준 방화벽 규칙 설정
> * 지역에서 복제 장애 조치(failover) 그룹 만들기
> * Azure SQL Database를 쿼리하기 위한 Java 응용 프로그램 만들기 및 컴파일
> * 재해 복구 훈련 수행

관리되는 인스턴스를 만드는 방법에 대해 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
>[관리되는 인스턴스 만들기](sql-database-managed-instance-create-tutorial-portal.md)

