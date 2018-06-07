---
title: '자습서: SSMS를 사용하여 첫 번째 Azure SQL Database 디자인 | Microsoft Docs'
description: SQL Server Management Studio를 사용하여 첫 번째 Azure SQL Database를 디자인하는 방법에 대해 알아봅니다.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,develop databases
ms.topic: tutorial
ms.date: 04/23/2018
ms.author: carlrab
ms.openlocfilehash: ba14208e971d712184052e7470757ce48ac26879
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-design-your-first-azure-sql-database-using-ssms"></a>자습서: SSMS를 사용하여 첫 번째 Azure SQL Database 디자인

Azure SQL Database는 Microsoft Cloud(Azure)의 관계형 DBaaS(Database-As-A-Service)입니다. 이 자습서에서는 Azure Portal 및 SSMS[(SQL Server Management Studio)](https://msdn.microsoft.com/library/ms174173.aspx)를 사용하는 방법을 알아봅니다. 

> [!div class="checklist"]
> * Azure Portal에서 데이터베이스 만들기*
> * Azure Portal에서 서버 수준 방화벽 규칙 설정
> * SSMS로 데이터베이스에 연결
> * SSMS를 사용하여 테이블 만들기
> * BCP를 사용하여 데이터 대량 로드
> * SSMS를 사용하여 해당 데이터 쿼리

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

   >[!NOTE]
   > 이 자습서를 사용하기 위해 여기서는 [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md)을 사용하지만, 사용자는 [vCore 기반 구매 모델(미리 보기)](sql-database-service-tiers-vcore.md) 선택 옵션을 사용할 수도 있습니다. 

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음을 설치했어야 합니다.
- 최신 버전의 SSMS([SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx))를 설치합니다.
- 최신 버전의 [BCP 및 SQLCMD](https://www.microsoft.com/download/details.aspx?id=36433).

## <a name="log-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-a-blank-sql-database"></a>빈 SQL 데이터베이스 만들기

Azure SQL Database는 일련의 정의된 [계산 및 저장소 리소스](sql-database-service-tiers-dtu.md)를 사용하여 만들어집니다. 데이터베이스는 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md) 및 [Azure SQL Database 논리 서버](sql-database-features.md)에서 만들어집니다. 

빈 SQL Database를 만들려면 다음 단계를 수행합니다. 

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 클릭합니다.

2. **새로 만들기** 페이지의 Azure Marketplace 섹션에서 **데이터베이스**를 선택한 다음, **추천** 섹션에서 **SQL Database**를 클릭합니다.

   ![빈 데이터베이스 만들기](./media/sql-database-design-first-database/create-empty-database.png)

3. 위의 이미지에 표시된 대로 다음과 같은 정보를 사용하여 SQL Database 형식을 작성합니다.   

   | 설정       | 제안 값 | 설명 | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **데이터베이스 이름** | mySampleDatabase | 유효한 데이터베이스 이름은 [데이터베이스 식별자](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers)를 참조하세요. | 
   | **구독** | 사용자의 구독  | 구독에 대한 자세한 내용은 [구독](https://account.windowsazure.com/Subscriptions)을 참조하세요. |
   | **리소스 그룹** | myResourceGroup | 유효한 리소스 그룹 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요. |
   | **원본 선택** | 빈 데이터베이스 | 빈 데이터베이스를 만들도록 지정합니다. |

4. **서버**를 클릭하여 새 데이터베이스에 새 서버를 만들고 구성합니다. 다음 정보로 **새 서버 양식**을 작성합니다. 

   | 설정       | 제안 값 | 설명 | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **서버 이름** | 전역적으로 고유한 이름 | 유효한 서버 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요. | 
   | **서버 관리자 로그인** | 모든 유효한 이름 | 유효한 로그인 이름은 [데이터베이스 식별자](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers)를 참조하세요.|
   | **암호** | 유효한 암호 | 암호는 8자 이상이어야 하며 대문자, 소문자, 숫자 및 영숫자가 아닌 문자 범주 중 세 가지 범주의 문자를 포함해야 합니다. |
   | **위치**: | 모든 유효한 위치 | 지역에 대한 자세한 내용은 [Azure 지역](https://azure.microsoft.com/regions/)을 참조하세요. |

   ![create database-server](./media/sql-database-design-first-database/create-database-server.png)

5. **선택**을 클릭합니다.

6. **가격 책정 계층**을 클릭하여 서비스 계층, DTU나 vCore 개수 및 저장소 크기를 지정합니다. 각 서비스 계층에 대해 사용할 수 있는 DTU/vCore 및 저장소 수에 대한 옵션을 살펴봅니다. 이 자습서를 사용하기 위해 여기서는 [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md)을 사용하지만, 사용자는 [vCore 기반 구매 모델(미리 보기)](sql-database-service-tiers-vcore.md) 선택 옵션을 사용할 수도 있습니다. 

7. 이 자습서에서는 **표준** 서비스 계층을 선택한 다음 슬라이더를 사용하여 **100DTU(S3)** 및 **400**GB 저장소를 선택합니다.

   ![create database-s1](./media/sql-database-design-first-database/create-empty-database-pricing-tier.png)

8. **추가 기능 저장소** 옵션을 사용하려면 미리 보기 약관에 동의합니다. 

   > [!IMPORTANT]
   > -  포함된 저장소보다 큰 저장소 크기는 미리 보기로 있으며 추가 비용이 적용됩니다. 자세한 내용은 [SQL Database 가격](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요. 
   >-  Premium 계층의 경우 현재 오스트레일리아 동부, 오스트레일리아 남동부, 브라질 남부, 캐나다 중부, 캐나다 동부, 미국 중부, 프랑스 중부, 독일 중부, 일본 동부, 일본 서부, 한국 중부, 미국 중북부, 북유럽, 미국 중남부, 동남 아시아, 영국 남부, 영국 서부, 미국 동부 2, 미국 서부, 미국 버지니아 주 정부 및 유럽 서부에서 1TB 이상의 저장소를 사용할 수 있습니다. [P11-P15 현재 제한 사항](sql-database-dtu-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)을 참조하세요.  


9. 서버 계층, DTU 수 및 저장소 크기를 선택한 후에 **적용**을 클릭합니다.  

10. 빈 데이터베이스에 대한 **데이터 정렬**을 선택합니다(이 자습서의 경우 기본값 사용). 데이터 정렬에 대한 자세한 내용은 [데이터 정렬](https://docs.microsoft.com/sql/t-sql/statements/collations)을 참조하세요.

11. 이제 SQL Database 양식을 완료했으므로 **만들기**를 클릭하여 데이터베이스를 프로비전합니다. 프로비전하는 데 몇 분이 걸립니다. 

12. 도구 모음에서 **알림**을 클릭하여 배포 프로세스를 모니터링합니다.
    
     ![알림](./media/sql-database-get-started-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>서버 수준 방화벽 규칙 만들기

방화벽 규칙을 만들어서 특정 IP 주소에 대한 방화벽을 열지 않으면 SQL Database 서비스는 외부 응용 프로그램 및 도구가 서버 또는 서버의 데이터베이스에 연결되지 않도록 방지하는 서버 수준에 방화벽을 만듭니다. 다음 단계에 따라 클라이언트의 IP 주소에 대한 [SQL Database 서버 수준 방화벽 규칙](sql-database-firewall-configure.md)을 만들고 IP 주소에만 SQL Database 방화벽을 통해 외부 연결을 사용하도록 설정합니다. 

> [!NOTE]
> SQL Database는 포트 1433을 통해 통신합니다. 회사 네트워크 내에서 연결을 시도하는 경우 포트 1433을 통한 아웃바운드 트래픽이 네트워크 방화벽에서 허용되지 않을 수 있습니다. 이 경우 IT 부서에서 포트 1433을 열지 않으면 Azure SQL Database 서버에 연결할 수 없습니다.
>

1. 배포가 완료되면 왼쪽 메뉴에서 **SQL Database**를 클릭한 다음 **SQL Database** 페이지에서 **mySampleDatabase**를 클릭합니다. 데이터베이스에 대한 개요 페이지가 열려 정규화된 서버 이름(예: **mynewserver-20170824.database.windows.net**)을 표시하고 추가 구성을 위한 옵션을 제공합니다. 

2. 후속 자습서 및 빠른 시작에서 서버 및 해당 데이터베이스에 연결하는 데 사용하기 위해 이 정규화된 서버 이름을 복사합니다. 

   ![서버 이름](./media/sql-database-get-started-portal/server-name.png) 

3. 도구 모음에서 **서버 방화벽 설정**을 클릭합니다. SQL Database 서버에 대한 **방화벽 설정** 페이지가 열립니다. 

   ![서버 방화벽 규칙](./media/sql-database-get-started-portal/server-firewall-rule.png) 

4. 도구 모음에서 **클라이언트 IP 추가**를 클릭하여 현재 IP 주소를 새 방화벽 규칙에 추가합니다. 방화벽 규칙은 단일 IP 주소 또는 IP 주소의 범위에 1433 포트를 열 수 있습니다.

5. **저장**을 클릭합니다. 논리 서버의 1433 포트를 여는 현재 IP 주소에 서버 수준 방화벽 규칙이 생성됩니다.

6. **확인**을 클릭한 후 **방화벽 설정** 페이지를 닫습니다.

이제 SQL Server Management Studio 또는 이전에 만든 서버 관리자 계정을 사용하여 이 IP 주소에서 원하는 다른 도구를 사용하여 SQL Database 서버 및 해당 데이터베이스에 연결할 수 있습니다.

> [!IMPORTANT]
> SQL Database 방화벽을 통한 액세스는 기본적으로 모든 Azure 서비스에 대해 사용됩니다. 이 페이지에서 **끄기**를 클릭하여 모든 Azure 서비스에 대해 사용하지 않도록 설정합니다.

## <a name="sql-server-connection-information"></a>SQL 서버 연결 정보

Azure Portal에 있는 Azure SQL Database 서버의 정규화된 서버 이름을 가져옵니다. 정규화된 서버 이름을 사용하여 SQL Server Management Studio를 사용하는 서버에 연결합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 왼쪽 메뉴에서 **SQL Database**를 선택하고, **SQL Database** 페이지에서 데이터베이스를 클릭합니다. 
3. 데이터베이스의 경우 Azure Portal의 **Essentials** 창에서 **서버 이름**을 찾고 복사합니다.

   ![연결 정보](./media/sql-database-get-started-portal/server-name.png)

## <a name="connect-to-the-database-with-ssms"></a>SSMS로 데이터베이스에 연결

[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)를 사용하여 Azure SQL Database 서버에 연결합니다.

1. SQL Server Management Studio를 엽니다.

2. **서버에 연결** 대화 상자에서 다음 정보를 입력합니다.

   | 설정       | 제안 값 | 설명 | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | 서버 유형 | 데이터베이스 엔진 | 이 값은 필수입니다. |
   | 서버 이름 | 정규화된 서버 이름 | 이름은 **mynewserver20170824.database.windows.net**과 비슷해야 합니다. |
   | 인증 | 공개 | SQL 인증은 이 자습서에서 구성한 유일한 인증 유형입니다. |
   | 로그인 | 서버 관리자 계정 | 서버를 만들 때 지정한 계정입니다. |
   | 암호 | 서버 관리자 계정의 암호 | 서버를 만들 때 지정한 암호입니다. |

   ![서버 연결](./media/sql-database-connect-query-ssms/connect.png)

3. **서버에 연결** 대화 상자에서 **옵션**을 클릭합니다. **데이터베이스에 연결** 섹션에서 **mySampleDatabase**를 입력하여 이 데이터베이스에 연결합니다.

   ![서버에서 db에 연결](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. **Connect**를 클릭합니다. SSMS에서 개체 탐색기 창이 열립니다. 

5. 개체 탐색기에서 **데이터베이스**를 확장한 다음 **mySampleDatabase**를 확장하여 샘플 데이터베이스에 있는 개체를 봅니다.

   ![데이터베이스 개체](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="create-tables-in-the-database"></a>데이터베이스에서 테이블 만들기 

[Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)을 사용하여 대학의 학생 관리 시스템을 모델링하는 네 개의 테이블이 있는 데이터베이스 스키마 만들기

- 사람
- 과정
- 학생
- 대학의 학생 관리 시스템을 모델링하는 크레딧

다음 다이어그램에서는 이러한 테이블 간의 관계를 보여 줍니다. 이러한 테이블 중 일부는 다른 테이블의 열을 참조합니다. 예를 들어 Student 테이블은 **Person** 테이블의 **PersonId** 열을 참조합니다. 다이어그램에 대해 학습하여 이 자습서에서 테이블 간의 관계를 이해합니다. 효과적인 데이터베이스 테이블을 만드는 방법에 대한 자세한 내용은 [효과적인 데이터베이스 테이블 만들기](https://msdn.microsoft.com/library/cc505842.aspx)를 참조하세요. 데이터 형식을 선택하는 방법은 [데이터 형식](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql)을 참조하세요.

> [!NOTE]
> 또한 [SQL Server Management Studio의 테이블 디자이너](https://msdn.microsoft.com/library/hh272695.aspx)를 사용하여 테이블을 만들고 디자인할 수도 있습니다. 

![테이블 관계](./media/sql-database-design-first-database/tutorial-database-tables.png)

1. 개체 탐색기에서 **mySampleDatabase**를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 클릭합니다. 데이터베이스에 연결된 비어 있는 쿼리 창이 열립니다.

2. 쿼리 창에서 다음 쿼리를 실행하여 데이터베이스에 4개의 테이블을 만듭니다. 

   ```sql 
   -- Create Person table

   CREATE TABLE Person
   (
   PersonId   INT IDENTITY PRIMARY KEY,
   FirstName   NVARCHAR(128) NOT NULL,
   MiddelInitial NVARCHAR(10),
   LastName   NVARCHAR(128) NOT NULL,
   DateOfBirth   DATE NOT NULL
   )
   
   -- Create Student table
 
   CREATE TABLE Student
   (
   StudentId INT IDENTITY PRIMARY KEY,
   PersonId  INT REFERENCES Person (PersonId),
   Email   NVARCHAR(256)
   )
   
   -- Create Course table
 
   CREATE TABLE Course
   (
   CourseId  INT IDENTITY PRIMARY KEY,
   Name   NVARCHAR(50) NOT NULL,
   Teacher   NVARCHAR(256) NOT NULL
   ) 

   -- Create Credit table
 
   CREATE TABLE Credit
   (
   StudentId   INT REFERENCES Student (StudentId),
   CourseId   INT REFERENCES Course (CourseId),
   Grade   DECIMAL(5,2) CHECK (Grade <= 100.00),
   Attempt   TINYINT,
   CONSTRAINT  [UQ_studentgrades] UNIQUE CLUSTERED
   (
   StudentId, CourseId, Grade, Attempt
   )
   )
   ```

   ![테이블 만들기](./media/sql-database-design-first-database/create-tables.png)

3. 사용자가 만든 테이블을 보려면 SQL Server Management Studio 개체 탐색기에서 '테이블' 노드를 확장합니다.

   ![ssms 테이블 생성](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>테이블에 데이터 로드

1. 다운로드 폴더에 **SampleTableData**라는 폴더를 만들어 데이터베이스의 샘플 데이터를 저장합니다. 

2. 다음 링크를 마우스 오른쪽 단추로 클릭하고 **SampleTableData** 폴더에 샘플 데이터를 저장합니다. 

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. 명령 프롬프트 창을 열고 SampleTableData 폴더로 이동합니다.

4. 다음 명령을 실행하여 테이블에 샘플 데이터를 삽입합니다. **ServerName**, **DatabaseName**, **UserName** 및 **Password** 값은 사용자 환경에 대한 값으로 바꿉니다.
  
   ```bcp
   bcp Course in SampleCourseData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   ```

이제 앞에서 만든 테이블에 샘플 데이터가 로드되었습니다.

## <a name="query-data"></a>쿼리 데이터

다음 쿼리를 실행하여 데이터베이스 테이블에서 정보를 검색합니다. SQL 쿼리 작성에 대한 자세한 내용은 [SQL 쿼리 작성](https://technet.microsoft.com/library/bb264565.aspx)을 참조하세요. 첫 번째 쿼리는 4개의 테이블을 모두 조인하여 'Dominick Pope' 선생님의 학생 중에 성적이 75%보다 높은 모든 학생을 찾습니다. 두 번째 쿼리는 4개의 테이블을 모두 조인하여 'Noe Coleman'이 등록한 적 있는 모든 과정을 찾습니다.

1. SQL Server Management Studio 쿼리 창에서 다음 쿼리를 실행합니다.

   ```sql 
   -- Find the students taught by Dominick Pope who have a grade higher than 75%

   SELECT  person.FirstName,
   person.LastName,
   course.Name,
   credit.Grade
   FROM  Person AS person
   INNER JOIN Student AS student ON person.PersonId = student.PersonId
   INNER JOIN Credit AS credit ON student.StudentId = credit.StudentId
   INNER JOIN Course AS course ON credit.CourseId = course.courseId
   WHERE course.Teacher = 'Dominick Pope' 
   AND Grade > 75
   ```

2. SQL Server Management Studio 쿼리 창에서 다음 쿼리를 실행합니다.

   ```sql
   -- Find all the courses in which Noe Coleman has ever enrolled

   SELECT  course.Name,
   course.Teacher,
   credit.Grade
   FROM  Course AS course
   INNER JOIN Credit AS credit ON credit.CourseId = course.CourseId
   INNER JOIN Student AS student ON student.StudentId = credit.StudentId
   INNER JOIN Person AS person ON person.PersonId = student.PersonId
   WHERE person.FirstName = 'Noe'
   AND person.LastName = 'Coleman'
   ```

## <a name="next-steps"></a>다음 단계 
이 자습서에서는 데이터베이스 및 테이블 만들기, 데이터 로드 및 쿼리, 데이터베이스를 이전 시점으로 복원과 같은 기본적인 데이터베이스 작업에 대해 배웁니다. 다음 방법에 대해 알아보았습니다.
> [!div class="checklist"]
> * 데이터베이스 만들기
> * 방화벽 규칙 설정
> * SQL Server Management Studio[(SSMS)](https://msdn.microsoft.com/library/ms174173.aspx)를 사용하여 데이터베이스에 연결
> * 테이블 만들기
> * 데이터 대량 로드
> * 해당 데이터 쿼리

Visual Studio 및 C#을 사용하여 데이터베이스를 설계하는 방법에 대한 자세한 내용을 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
>[Azure SQL Database 설계 및 C#과 ADO.NET에 연결T](sql-database-design-first-database-csharp.md)
