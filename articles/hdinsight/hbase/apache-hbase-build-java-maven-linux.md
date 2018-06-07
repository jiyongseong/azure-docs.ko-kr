---
title: Java HBase 클라이언트 - Azure HDInsight | Microsoft Docs
description: Apache Maven을 사용하여 Java 기반 Apache HBase 응용 프로그램을 빌드한 다음 Azure HDInsight의 HBase에 배포하는 방법에 대해 알아봅니다.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: ''
ms.assetid: 1d1ed180-e0f4-4d1c-b5ea-72e0eda643bc
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: larryfr
ms.openlocfilehash: 564bfb9c80fe835bd5defb4321607e0970c9f0aa
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2018
---
# <a name="build-java-applications-for-apache-hbase"></a>Apache HBase에 대한 Java 응용 프로그램 빌드

Java에서 [Apache HBase](http://hbase.apache.org/) 응용 프로그램을 만드는 방법을 알아봅니다. 그런 다음 Azure HDInsight의 HBase에서 응용 프로그램을 사용합니다.

이 문서에 나온 단계는 [Maven](http://maven.apache.org/)을 사용하여 프로젝트를 만들고 빌드합니다. Maven은 Java 프로젝트용 소프트웨어, 문서화 및 보고를 빌드할 수 있는 소프트웨어 프로젝트 관리 및 종합 도구입니다.

> [!NOTE]
> 이 문서의 단계는 HDInsight 3.6에서 가장 최근에 테스트되었습니다.

> [!IMPORTANT]
> 이 문서의 단계에는 Linux를 사용하는 HDInsight 클러스터가 필요합니다. Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](../hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

## <a name="requirements"></a>요구 사항

* [Java 플랫폼 JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 8 이상.

    > [!NOTE]
    > HDInsight 3.5 이상에는 Java 8이 필요합니다. 이전 버전의 HDInsight를 사용하려면 Java 7이 필요합니다.

* [Maven](http://maven.apache.org/)

* [Linux 기반 Azure HDInsight 클러스터 및 HBase](apache-hbase-tutorial-get-started-linux.md#create-hbase-cluster)

## <a name="create-the-project"></a>프로젝트 만들기

1. 개발 환경의 명령줄에서 프로젝트를 만들 위치(예: `cd code\hbase`)로 디렉터리를 변경합니다.

2. Maven과 함께 설치되는 **mvn** 명령을 사용하여 프로젝트용 스캐폴딩을 생성합니다.

    ```bash
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    > [!NOTE]
    > PowerShell을 사용하는 경우 큰 따옴표로 `-D` 매개 변수를 묶어야 합니다.
    >
    > `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=hbaseapp" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`

    이 명령은 **artifactID** 매개 변수와 동일한 이름으로 디렉터리를 만듭니다(이 예제에서는 **hbaseapp**). 이 디렉터리에는 다음과 같은 항목이 포함됩니다.

   * **pom.xml**: [프로젝트 개체 모델(POM)](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)은 프로젝트를 빌드하는 데 사용된 정보 및 구성 세부 정보를 포함합니다.
   * **src**: **main/java/com/microsoft/examples** 디렉터리를 포함하는 디렉터리이며 여기서 응용 프로그램을 작성합니다.

3. `src/test/java/com/microsoft/examples/apptest.java` 파일을 삭제합니다. 이 예제에서는 사용되지 않았습니다.

## <a name="update-the-project-object-model"></a>프로젝트 개체 모델 업데이트

1. `pom.xml` 파일을 편집하고 다음 코드를 `<dependencies>` 섹션에 추가합니다.

   ```xml
    <dependency>
        <groupId>org.apache.hbase</groupId>
        <artifactId>hbase-client</artifactId>
        <version>1.1.2</version>
    </dependency>
    <dependency>
        <groupId>org.apache.phoenix</groupId>
        <artifactId>phoenix-core</artifactId>
        <version>4.4.0-HBase-1.1</version>
    </dependency>
   ```

    이 섹션에서는 프로젝트에 **hbase-client** 및 **phoenix-core** 구성 요소가 필요하다는 점을 나타냅니다. 컴파일 시 이러한 종속성이 기본 Maven 리포지토리에서 다운로드됩니다. [Maven 중앙 리포지토리 검색](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar)을 사용하여 이 종속성에 대한 자세한 정보를 확인할 수 있습니다.

   > [!IMPORTANT]
   > hbase-client의 버전 번호는 HDInsight 클러스터와 함께 제공되는 HBase 버전과 일치해야 합니다. 다음 표를 사용하여 올바른 버전 번호를 찾으세요.

   | HDInsight 클러스터 버전 | 사용할 HBase 버전 |
   | --- | --- |
   | 3.2 |0.98.4-hadoop2 |
   | 3.3, 3.4, 3.5 및 3.6 |1.1.2 |

    HDInsight 버전 및 구성 요소에 대한 자세한 내용은 [HDInsight에서 사용할 수 있는 다양한 Hadoop 구성 요소](../hdinsight-component-versioning.md)를 참조하세요.

3. **pom.xml** 파일에 다음 코드를 추가합니다. 이 텍스트는 파일의 `<project>...</project>` 태그 내에 있어야 합니다. 예를 들어 `</dependencies>`와 `</project>` 사이에 있어야 합니다.

   ```xml
    <build>
        <sourceDirectory>src</sourceDirectory>
        <resources>
        <resource>
            <directory>${basedir}/conf</directory>
            <filtering>false</filtering>
            <includes>
            <include>hbase-site.xml</include>
            </includes>
        </resource>
        </resources>
        <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.3</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
            </configuration>
            </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>2.3</version>
            <configuration>
            <transformers>
                <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                </transformer>
            </transformers>
            </configuration>
            <executions>
            <execution>
                <phase>package</phase>
                <goals>
                <goal>shade</goal>
                </goals>
            </execution>
            </executions>
        </plugin>
        </plugins>
    </build>
   ```

    이 섹션은 HBase에 대한 구성 정보를 포함하는 리소스(`conf/hbase-site.xml`)를 구성합니다.

   > [!NOTE]
   > 또한 코드를 통해 구성 값을 설정할 수도 있습니다. `CreateTable` 예제에서 주석을 참조하세요.

    또한 이 섹션에서는 [Maven 컴파일러 플러그 인](http://maven.apache.org/plugins/maven-compiler-plugin/) 및 [Maven 음영 플러그 인](http://maven.apache.org/plugins/maven-shade-plugin/)도 구성합니다. 컴파일러 플러그 인은 토폴로지를 컴파일하는 데 사용됩니다. 음영 플러그 인은 Maven으로 빌드된 JAR 패키지에서 라이선스 중복을 방지하는 데 사용됩니다. 이 플러그 인은 HDInsight 클러스터에서 런타임에 "중복 라이선스 파일" 오류가 발생하지 않도록 하는 데 사용됩니다. `ApacheLicenseResourceTransformer` 구현에서 maven-shade-plugin을 사용하면 이 오류가 방지됩니다.

    또한 maven-shade-plugin은 응용 프로그램에 필요한 모든 종속성을 포함하는 uber jar도 생성합니다.

4. `pom.xml` 파일을 저장합니다.

5. `hbaseapp` 디렉터리에 `conf`라는 디렉터리를 만듭니다. 이 디렉터리는 HBase에 연결하기 위한 구성 정보를 저장하는 데 사용됩니다.

6. 다음 명령을 사용하여 HBase 클러스터에서 `conf` 디렉터리로 HBase 구성을 복사합니다. `USERNAME`을 SSH 로그인 이름으로 바꿉니다. `CLUSTERNAME`을 HDInsight 클러스터 이름으로 바꿉니다.

    ```bash
    scp USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml
    ```

   `ssh` 및 `scp` 사용에 관한 자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

## <a name="create-the-application"></a>응용 프로그램 만들기

1. `hbaseapp/src/main/java/com/microsoft/examples` 디렉터리로 이동하여 app.java 파일 이름을 `CreateTable.java`로 변경합니다.

2. `CreateTable.java` 파일을 열고 기존 콘텐츠를 다음 텍스트로 바꿉니다.

   ```java
    package com.microsoft.examples;
    import java.io.IOException;

    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.hbase.HBaseConfiguration;
    import org.apache.hadoop.hbase.client.HBaseAdmin;
    import org.apache.hadoop.hbase.HTableDescriptor;
    import org.apache.hadoop.hbase.TableName;
    import org.apache.hadoop.hbase.HColumnDescriptor;
    import org.apache.hadoop.hbase.client.HTable;
    import org.apache.hadoop.hbase.client.Put;
    import org.apache.hadoop.hbase.util.Bytes;

    public class CreateTable {
        public static void main(String[] args) throws IOException {
        Configuration config = HBaseConfiguration.create();

        // Example of setting zookeeper values for HDInsight
        // in code instead of an hbase-site.xml file
        //
        // config.set("hbase.zookeeper.quorum",
        //            "zookeepernode0,zookeepernode1,zookeepernode2");
        //config.set("hbase.zookeeper.property.clientPort", "2181");
        //config.set("hbase.cluster.distributed", "true");
        //
        //NOTE: Actual zookeeper host names can be found using Ambari:
        //curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts"

        //Linux-based HDInsight clusters use /hbase-unsecure as the znode parent
        config.set("zookeeper.znode.parent","/hbase-unsecure");

        // create an admin object using the config
        HBaseAdmin admin = new HBaseAdmin(config);

        // create the table...
        HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
        // ... with two column families
        tableDescriptor.addFamily(new HColumnDescriptor("name"));
        tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
        admin.createTable(tableDescriptor);

        // define some people
        String[][] people = {
            { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
            { "2", "Franklin", "Holtz", "franklin@contoso.com" },
            { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
            { "4", "Rae", "Schroeder", "rae@contoso.com" },
            { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
            { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };

        HTable table = new HTable(config, "people");

        // Add each person to the table
        //   Use the `name` column family for the name
        //   Use the `contactinfo` column family for the email
        for (int i = 0; i< people.length; i++) {
            Put person = new Put(Bytes.toBytes(people[i][0]));
            person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
            person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
            person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
            table.put(person);
        }
        // flush commits and close the table
        table.flushCommits();
        table.close();
        }
    }
   ```

    이 코드는 **CreateTable** 클래스이며, **people**이라는 테이블을 만들고 미리 정의된 사용자로 채웁니다.

3. `CreateTable.java` 파일을 저장합니다.

4. `hbaseapp/src/main/java/com/microsoft/examples` 디렉터리에서 `SearchByEmail.java`라는 파일을 만듭니다. 이 파일의 내용으로 다음 텍스트를 사용합니다.

   ```java
    package com.microsoft.examples;
    import java.io.IOException;

    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.hbase.HBaseConfiguration;
    import org.apache.hadoop.hbase.client.HTable;
    import org.apache.hadoop.hbase.client.Scan;
    import org.apache.hadoop.hbase.client.ResultScanner;
    import org.apache.hadoop.hbase.client.Result;
    import org.apache.hadoop.hbase.filter.RegexStringComparator;
    import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
    import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
    import org.apache.hadoop.hbase.util.Bytes;
    import org.apache.hadoop.util.GenericOptionsParser;

    public class SearchByEmail {
        public static void main(String[] args) throws IOException {
        Configuration config = HBaseConfiguration.create();

        // Use GenericOptionsParser to get only the parameters to the class
        // and not all the parameters passed (when using WebHCat for example)
        String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
        if (otherArgs.length != 1) {
            System.out.println("usage: [regular expression]");
            System.exit(-1);
        }

        // Open the table
        HTable table = new HTable(config, "people");

        // Define the family and qualifiers to be used
        byte[] contactFamily = Bytes.toBytes("contactinfo");
        byte[] emailQualifier = Bytes.toBytes("email");
        byte[] nameFamily = Bytes.toBytes("name");
        byte[] firstNameQualifier = Bytes.toBytes("first");
        byte[] lastNameQualifier = Bytes.toBytes("last");

        // Create a regex filter
        RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
        // Attach the regex filter to a filter
        //   for the email column
        SingleColumnValueFilter filter = new SingleColumnValueFilter(
            contactFamily,
            emailQualifier,
            CompareOp.EQUAL,
            emailFilter
        );

        // Create a scan and set the filter
        Scan scan = new Scan();
        scan.setFilter(filter);

        // Get the results
        ResultScanner results = table.getScanner(scan);
        // Iterate over results and print  values
        for (Result result : results ) {
            String id = new String(result.getRow());
            byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
            String firstName = new String(firstNameObj);
            byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
            String lastName = new String(lastNameObj);
            System.out.println(firstName + " " + lastName + " - ID: " + id);
            byte[] emailObj = result.getValue(contactFamily, emailQualifier);
            String email = new String(emailObj);
            System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
        }
        results.close();
        table.close();
        }
    }
   ```

    **SearchByEmail** 클래스를 사용하여 메일 주소로 행을 쿼리할 수 있습니다. 정규식 필터를 사용하므로, 이 클래스를 사용할 때 문자열 또는 정규식을 제공할 수 있습니다.

5. `SearchByEmail.java` 파일을 저장합니다.

6. `hbaseapp/src/main/hava/com/microsoft/examples` 디렉터리에서 `DeleteTable.java`라는 파일을 만듭니다. 이 파일의 내용으로 다음 텍스트를 사용합니다.

   ```java
    package com.microsoft.examples;
    import java.io.IOException;

    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.hbase.HBaseConfiguration;
    import org.apache.hadoop.hbase.client.HBaseAdmin;

    public class DeleteTable {
        public static void main(String[] args) throws IOException {
        Configuration config = HBaseConfiguration.create();

        // Create an admin object using the config
        HBaseAdmin admin = new HBaseAdmin(config);

        // Disable, and then delete the table
        admin.disableTable("people");
        admin.deleteTable("people");
        }
    }
   ```

    이 클래스는 `CreateTable` 클래스에서 생성된 테이블을 비활성화하고 제거하여 이 예제에서 만든 HBase 테이블을 정리합니다.

7. `DeleteTable.java` 파일을 저장합니다.

## <a name="build-and-package-the-application"></a>응용 프로그램 빌드 및 패키지화

1. `hbaseapp` 디렉터리에서 다음 명령을 사용하여 응용 프로그램을 포함하는 JAR 파일을 빌드합니다.

    ```bash
    mvn clean package
    ```

    이 명령은 .jar 파일에 응용 프로그램을 빌드하고 패키지합니다.

2. 명령이 완료되면 `hbaseapp/target` 디렉터리는 `hbaseapp-1.0-SNAPSHOT.jar`라는 파일을 포함합니다.

   > [!NOTE]
   > `hbaseapp-1.0-SNAPSHOT.jar` 파일은 uber jar입니다. 응용 프로그램을 실행 는 데 필요한 모든 종속성을 포함합니다.


## <a name="upload-the-jar-and-run-jobs-ssh"></a>JAR 업로드 및 작업 실행(SSH)

다음 단계에서는 `scp`를 사용하여 HDInsight 클러스터에 있는 HBase의 기본 헤드 노드에 JAR을 복사합니다. 그런 후 `ssh` 명령은 클러스터에 연결하고 헤드 노드에서 직접 예제를 실행하는 데 사용됩니다.

1. jar을 클러스터에 업로드하려면 다음 명령을 사용합니다.

    ```bash
    scp ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:hbaseapp-1.0-SNAPSHOT.jar
    ```

    `USERNAME`을 SSH 로그인 이름으로 바꿉니다. `CLUSTERNAME`을 HDInsight 클러스터 이름으로 바꿉니다.

2. HBase 클러스터에 연결하려면 다음 명령을 사용합니다.

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    `USERNAME`을 SSH 로그인 이름으로 바꿉니다. `CLUSTERNAME`을 HDInsight 클러스터 이름으로 바꿉니다.

3. Java 응용 프로그램을 사용하는 HBase 테이블을 만들려면 다음 명령을 사용합니다.

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable
    ```

    이 명령은 **people**이라는 HBase 테이블을 만들고 데이터로 채웁니다.

4. 테이블에 저장된 이메일 주소를 검색하려면 다음 명령을 사용합니다.

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com
    ```

    다음과 같은 결과가 표시됩니다.

        Franklin Holtz - ID: 2
        Franklin Holtz - franklin@contoso.com - ID: 2
        Rae Schroeder - ID: 4
        Rae Schroeder - rae@contoso.com - ID: 4
        Gabriela Ingram - ID: 6
        Gabriela Ingram - gabriela@contoso.com - ID: 6

5. 테이블을 삭제하려면 다음 명령을 사용합니다.

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable
    ```

## <a name="upload-the-jar-and-run-jobs-powershell"></a>JAR 업로드 및 작업 실행(PowerShell)

다음 단계는 Azure PowerShell을 사용하여 HBase 클러스터용 기본 저장소에 JAR을 업로드합니다. HDInsight cmdlet은 예제를 원격으로 실행하는 데 사용됩니다.

1. Azure PowerShell을 설치 및 구성한 후 `hbase-runner.psm1`이라는 파일을 만듭니다. 이 파일의 내용으로 다음 텍스트를 사용합니다.

   ```powershell
    <#
    .SYNOPSIS
    Copies a file to the primary storage of an HDInsight cluster.
    .DESCRIPTION
    Copies a file from a local directory to the blob container for
    the HDInsight cluster.
    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.CreateTable"
    -clusterName "MyHDInsightCluster"

    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
    -clusterName "MyHDInsightCluster"
    -emailRegex "contoso.com"

    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
    -clusterName "MyHDInsightCluster"
    -emailRegex "^r" -showErr
    #>

    function Start-HBaseExample {
    [CmdletBinding(SupportsShouldProcess = $true)]
    param(
    #The class to run
    [Parameter(Mandatory = $true)]
    [String]$className,

    #The name of the HDInsight cluster
    [Parameter(Mandatory = $true)]
    [String]$clusterName,

    #Only used when using SearchByEmail
    [Parameter(Mandatory = $false)]
    [String]$emailRegex,

    #Use if you want to see stderr output
    [Parameter(Mandatory = $false)]
    [Switch]$showErr
    )

    Set-StrictMode -Version 3

    # Is the Azure module installed?
    FindAzure

    # Get the login for the HDInsight cluster
    $creds=Get-Credential -Message "Enter the login for the cluster" -UserName "admin"

    # The JAR
    $jarFile = "wasb:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"

    # The job definition
    $jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
        -JarFile $jarFile `
        -ClassName $className `
        -Arguments $emailRegex

    # Get the job output
    $job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds
    if($showErr)
    {
    Write-Host "STDERR"
    Get-AzureRmHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds `
                -DisplayOutputType StandardError
    }
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds
    }

    <#
    .SYNOPSIS
    Copies a file to the primary storage of an HDInsight cluster.
    .DESCRIPTION
    Copies a file from a local directory to the blob container for
    the HDInsight cluster.
    .EXAMPLE
    Add-HDInsightFile -localPath "C:\temp\data.txt"
    -destinationPath "example/data/data.txt"
    -ClusterName "MyHDInsightCluster"
    .EXAMPLE
    Add-HDInsightFile -localPath "C:\temp\data.txt"
    -destinationPath "example/data/data.txt"
    -ClusterName "MyHDInsightCluster"
    -Container "MyContainer"
    #>

    function Add-HDInsightFile {
        [CmdletBinding(SupportsShouldProcess = $true)]
        param(
            #The path to the local file.
            [Parameter(Mandatory = $true)]
            [String]$localPath,

            #The destination path and file name, relative to the root of the container.
            [Parameter(Mandatory = $true)]
            [String]$destinationPath,

            #The name of the HDInsight cluster
            [Parameter(Mandatory = $true)]
            [String]$clusterName,

            #If specified, overwrites existing files without prompting
            [Parameter(Mandatory = $false)]
            [Switch]$force
        )

        Set-StrictMode -Version 3

        # Is the Azure module installed?
        FindAzure

        # Get authentication for the cluster
        $creds=Get-Credential

        # Does the local path exist?
        if (-not (Test-Path $localPath))
        {
            throw "Source path '$localPath' does not exist."
        }

        # Get the primary storage container
        $storage = GetStorage -clusterName $clusterName

        # Upload file to storage, overwriting existing files if -force was used.
        Set-AzureStorageBlobContent -File $localPath `
            -Blob $destinationPath `
            -force:$force `
            -Container $storage.container `
            -Context $storage.context
    }

    function FindAzure {
        # Is there an active Azure subscription?
        $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            throw "No active Azure subscription found! If you have a subscription, use the Connect-AzureRmAccount cmdlet to login to your subscription."
        }
    }

    function GetStorage {
        param(
            [Parameter(Mandatory = $true)]
            [String]$clusterName
        )
        $hdi = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        # Does the cluster exist?
        if (!$hdi)
        {
            throw "HDInsight cluster '$clusterName' does not exist."
        }
        # Create a return object for context & container
        $return = @{}
        $storageAccounts = @{}

        # Get storage information
        $resourceGroup = $hdi.ResourceGroup
        $storageAccountName=$hdi.DefaultStorageAccount.split('.')[0]
        $container=$hdi.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        # Get the resource group, in case we need that
        $return.resourceGroup = $resourceGroup
        # Get the storage context, as we can't depend
        # on using the default storage context
        $return.context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        # Get the container, so we know where to
        # find/store blobs
        $return.container = $container
        # Return storage accounts to support finding all accounts for
        # a cluster
        $return.storageAccount = $storageAccountName
        $return.storageAccountKey = $storageAccountKey

        return $return
    }
    # Only export the verb-phrase things
    export-modulemember *-*
   ```

    이 파일에는 다음 두 모듈이 포함됩니다.

   * **Add-HDInsightFile** - 클러스터에 파일을 업로드하는 데 사용합니다.
   * **Start-HBaseExample** - 이전에 생성한 클래스를 실행하는 데 사용합니다.

2. `hbase-runner.psm1` 파일을 저장합니다.

3. 새 Azure PowerShell 창을 열고, 디렉터리를 `hbaseapp` 디렉터리로 변경한 후 다음 명령을 실행합니다.

    ```powershell
    PS C:\ Import-Module c:\path\to\hbase-runner.psm1
    ```

    이전에 만든 `hbase-runner.psm1` 파일의 위치로 경로를 변경합니다. 이 명령은 Azure PowerShell에 모듈을 등록합니다.

4. 다음 명령을 사용하여 클러스터에 `hbaseapp-1.0-SNAPSHOT.jar`을 업로드합니다.

    ```powershell
    Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername
    ```

    `hdinsightclustername`을 클러스터의 이름으로 바꿉니다. 메시지가 표시되면 클러스터 로그인(관리자) 이름 및 암호를 입력합니다. 이 명령은 `hbaseapp-1.0-SNAPSHOT.jar`을 클러스터용 기본 저장소에서 `example/jars` 위치에 업로드합니다.

5. `hbaseapp`을 사용하는 테이블을 만들려면 다음 명령을 사용합니다.

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername
    ```

    `hdinsightclustername`을 클러스터의 이름으로 바꿉니다. 메시지가 표시되면 클러스터 로그인(관리자) 이름 및 암호를 입력합니다.

    이 명령은 HDInsight 클러스터에 HBase의 **people**이라는 테이블을 만듭니다. 이 명령은 콘솔 창에 출력을 표시하지 않습니다.

6. 테이블에서 항목을 검색하려면 다음 명령을 사용합니다.

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com
    ```

    `hdinsightclustername`을 클러스터의 이름으로 바꿉니다. 메시지가 표시되면 클러스터 로그인(관리자) 이름 및 암호를 입력합니다.

    이 명령은 `SearchByEmail` 클래스를 사용하여 `contactinformation` 열 패밀리 및 `email` 열에 `contoso.com` 문자열이 포함된 모든 행을 검색합니다. 다음과 같은 결과가 표시됩니다.

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    `-emailRegex` 값에 **fabrikam.com**을 사용하면 메일 필드에 **fabrikam.com**을 포함하는 사용자가 반환됩니다. 검색 용어로 정규식을 사용할 수도 있습니다. 예를 들어, **^r**은 'r' 문자로 시작하는 전자 메일 주소를 반환합니다.

### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Start-HBaseExample을 사용할 경우 결과가 없거나 예기치 않은 결과가 표시됨

`-showErr` 매개 변수를 사용하여 작업을 실행하는 동안 생성된 표준 오류(STDERR)을 확인합니다.

## <a name="delete-the-table"></a>테이블 삭제

예제를 완료하면 Azure PowerShell 세션에서 다음을 사용하여 이 예제에 사용된 **people** 테이블을 삭제합니다.

__`ssh` 세션에서__:

`yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable`

__Azure PowerShell에서__:

`Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername`

## <a name="next-steps"></a>다음 단계

[HBase를 통해 SQuirreL SQL 사용 방법 알아보기](apache-hbase-phoenix-squirrel-linux.md)
