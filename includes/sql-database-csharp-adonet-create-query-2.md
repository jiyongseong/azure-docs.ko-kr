
<a name="cs_0_csharpprogramexample_h2"/>

## <a name="c-program-example"></a>C# 프로그램 예

이 문서의 다음 섹션에는 Transact-SQL 문을 SQL 데이터베이스로 보내기 위해 ADO.NET를 사용하는 C# 프로그램이 나와 있습니다. C# 프로그램은 다음 작업을 수행합니다.

1. [ADO.NET을 사용하여 SQL 데이터베이스에 연결합니다](#cs_1_connect).
2. [테이블을 만듭니다](#cs_2_createtables).
3. [T-SQL INSERT 문을 발행하여 데이터로 테이블을 채웁니다](#cs_3_insert).
4. [조인을 사용하여 데이터를 업데이트합니다](#cs_4_updatejoin).
5. [조인을 사용하여 데이터를 삭제합니다](#cs_5_deletejoin).
6. [조인을 사용하여 데이터 행을 선택합니다](#cs_6_selectrows).
7. 연결을 닫습니다(tempdb에서 임시 테이블 삭제).

C# 프로그램은 다음을 포함합니다.

- 데이터베이스에 연결하기 위한 C# 코드.
- T-SQL 소스 코드를 반환하는 메서드.
- T-SQL을 데이터베이스에 제출하는 두 가지 메서드.

#### <a name="to-compile-and-run"></a>컴파일 및 실행

이 C# 프로그램은 하나의 논리적인 .cs 파일입니다. 하지만 여기서 프로그램은 물리적으로 여러 코드 블록으로 분할되어 각 블록을 보다 쉽게 보고 이해할 수 있습니다. 이 프로그램을 컴파일하고 실행하려면 다음을 수행합니다.

1. Visual Studio에서 C# 프로젝트를 만듭니다.
    - 프로젝트 형식은 다음과 같은 계층 구조와 같은 *콘솔* 응용 프로그램이어야 합니다. **템플릿** > **Visual C#** > **Windows 클래식 데스크톱** > **콘솔 앱(.NET Framework)**.
3. **Program.cs** 파일에서 코드의 작은 시작 줄을 지웁니다.
3. 여기에 제시된 것과 동일한 순서대로 Program.cs에 다음과 같은 블록을 복사하여 붙여 넣습니다.
4. Program.cs에서 **Main** 메서드의 다음 값을 편집합니다.

   - **cb.DataSource**
   - **cd.UserID**
   - **cb.Password**
   - **InitialCatalog**

5. **System.Data.dll** 어셈블리가 참조되는지 확인합니다. 확인하려면 **솔루션 탐색기** 창에서 **참조** 노드를 확장합니다.
6. Visual Studio에서 프로그램을 빌드하려면 **빌드** 메뉴를 클릭합니다.
7. Visual Studio의 프로그램을 실행하려면 **시작** 단추를 클릭합니다. cmd.exe 창에 보고서 출력이 표시됩니다.

> [!NOTE]
> **tempdb**에서 **#**를 테이블 이름 앞에 추가하여 임시 테이블로 만드는 T-SQL 편집 옵션이 있습니다. 이는 사용할 수 있는 테스트 데이터베이스가 없는 경우 데모용으로 유용할 수 있습니다. 연결을 닫으면 임시 테이블은 자동으로 삭제됩니다. 외래 키에 대한 모든 REFERENCES는 임시 테이블에 적용되지 않습니다.
>

<a name="cs_1_connect"/>
### <a name="c-block-1-connect-by-using-adonet"></a>C# 블록 1: ADO.NET을 사용하여 연결

- [다음](#cs_2_createtables)


```csharp
using System;
using System.Data.SqlClient;   // System.Data.dll 
//using System.Data;           // For:  SqlDbType , ParameterDirection

namespace csharp_db_test
{
   class Program
   {
      static void Main(string[] args)
      {
         try
         {
            var cb = new SqlConnectionStringBuilder();
            cb.DataSource = "your_server.database.windows.net";
            cb.UserID = "your_user";
            cb.Password = "your_password";
            cb.InitialCatalog = "your_database";

            using (var connection = new SqlConnection(cb.ConnectionString))
            {
               connection.Open();

               Submit_Tsql_NonQuery(connection, "2 - Create-Tables",
                  Build_2_Tsql_CreateTables());

               Submit_Tsql_NonQuery(connection, "3 - Inserts",
                  Build_3_Tsql_Inserts());

               Submit_Tsql_NonQuery(connection, "4 - Update-Join",
                  Build_4_Tsql_UpdateJoin(),
                  "@csharpParmDepartmentName", "Accounting");

               Submit_Tsql_NonQuery(connection, "5 - Delete-Join",
                  Build_5_Tsql_DeleteJoin(),
                  "@csharpParmDepartmentName", "Legal");

               Submit_6_Tsql_SelectEmployees(connection);
            }
         }
         catch (SqlException e)
         {
            Console.WriteLine(e.ToString());
         }
         Console.WriteLine("View the report output here, then press any key to end the program...");
         Console.ReadKey();
      }
```


<a name="cs_2_createtables"/>
### <a name="c-block-2-t-sql-to-create-tables"></a>C# 블록 2: 테이블을 만드는 T-SQL

- [이전](#cs_1_connect) &nbsp; / &nbsp; [다음](#cs_3_insert)

```csharp
      static string Build_2_Tsql_CreateTables()
      {
         return @"
DROP TABLE IF EXISTS tabEmployee;
DROP TABLE IF EXISTS tabDepartment;  -- Drop parent table last.


CREATE TABLE tabDepartment
(
   DepartmentCode  nchar(4)          not null
      PRIMARY KEY,
   DepartmentName  nvarchar(128)     not null
);

CREATE TABLE tabEmployee
(
   EmployeeGuid    uniqueIdentifier  not null  default NewId()
      PRIMARY KEY,
   EmployeeName    nvarchar(128)     not null,
   EmployeeLevel   int               not null,
   DepartmentCode  nchar(4)              null
      REFERENCES tabDepartment (DepartmentCode)  -- (REFERENCES would be disallowed on temporary tables.)
);
";
      }
```

#### <a name="entity-relationship-diagram-erd"></a>엔터티 관계 다이어그램(ERD)

이전 CREATE TABLE 문에는 두 테이블 간의 *외래 키*(FK) 관계를 만들기 위해 **REFERENCES** 키워드가 있습니다.  tempdb를 사용하는 경우 선행 대시의 쌍을 사용하여 `--REFERENCES` 키워드를 주석으로 처리합니다.

다음은 두 테이블 간의 관계를 표시하는 ERD입니다. #tabEmployee.DepartmentCode *자식* 열의 값은 #tabDepartment.Department *부모* 열에 있는 값으로 제한됩니다.

![외래 키를 표시하는 ERD](./media/sql-database-csharp-adonet-create-query-2/erd-dept-empl-fky-2.png)


<a name="cs_3_insert"/>
### <a name="c-block-3-t-sql-to-insert-data"></a>C# 블록 3: 데이터를 삽입하는 T-SQL

- [이전](#cs_2_createtables) &nbsp; / &nbsp; [다음](#cs_4_updatejoin)


```csharp
      static string Build_3_Tsql_Inserts()
      {
         return @"
-- The company has these departments.
INSERT INTO tabDepartment
   (DepartmentCode, DepartmentName)
      VALUES
   ('acct', 'Accounting'),
   ('hres', 'Human Resources'),
   ('legl', 'Legal');

-- The company has these employees, each in one department.
INSERT INTO tabEmployee
   (EmployeeName, EmployeeLevel, DepartmentCode)
      VALUES
   ('Alison'  , 19, 'acct'),
   ('Barbara' , 17, 'hres'),
   ('Carol'   , 21, 'acct'),
   ('Deborah' , 24, 'legl'),
   ('Elle'    , 15, null);
";
      }
```


<a name="cs_4_updatejoin"/>
### <a name="c-block-4-t-sql-to-update-join"></a>C# 블록 4: 업데이트 조인하는 T-SQL

- [이전](#cs_3_insert) &nbsp; / &nbsp; [다음](#cs_5_deletejoin)


```csharp
      static string Build_4_Tsql_UpdateJoin()
      {
         return @"
DECLARE @DName1  nvarchar(128) = @csharpParmDepartmentName;  --'Accounting';


-- Promote everyone in one department (see @parm...).
UPDATE empl
   SET
      empl.EmployeeLevel += 1
   FROM
      tabEmployee   as empl
      INNER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   WHERE
      dept.DepartmentName = @DName1;
";
      }
```


<a name="cs_5_deletejoin"/>
### <a name="c-block-5-t-sql-to-delete-join"></a>C# 블록 5: 삭제 조인하는 T-SQL

- [이전](#cs_4_updatejoin) &nbsp; / &nbsp; [다음](#cs_6_selectrows)


```csharp
      static string Build_5_Tsql_DeleteJoin()
      {
         return @"
DECLARE @DName2  nvarchar(128);
SET @DName2 = @csharpParmDepartmentName;  --'Legal';


-- Right size the Legal department.
DELETE empl
   FROM
      tabEmployee   as empl
      INNER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   WHERE
      dept.DepartmentName = @DName2

-- Disband the Legal department.
DELETE tabDepartment
   WHERE DepartmentName = @DName2;
";
      }
```



<a name="cs_6_selectrows"/>
### <a name="c-block-6-t-sql-to-select-rows"></a>C# 블록 6: 행을 선택하는 T-SQL

- [이전](#cs_5_deletejoin) &nbsp; / &nbsp; [다음](#cs_6b_datareader)


```csharp
      static string Build_6_Tsql_SelectEmployees()
      {
         return @"
-- Look at all the final Employees.
SELECT
      empl.EmployeeGuid,
      empl.EmployeeName,
      empl.EmployeeLevel,
      empl.DepartmentCode,
      dept.DepartmentName
   FROM
      tabEmployee   as empl
      LEFT OUTER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   ORDER BY
      EmployeeName;
";
      }
```


<a name="cs_6b_datareader"/>
### <a name="c-block-6b-executereader"></a>C# 블록 6b: ExecuteReader

- [이전](#cs_6_selectrows) &nbsp; / &nbsp; [다음](#cs_7_executenonquery)

이 메서드는 **Build_6_Tsql_SelectEmployees** 메서드에 의해 빌드된 T-SQL SELECT 문을 실행하도록 설계되었습니다.


```csharp
      static void Submit_6_Tsql_SelectEmployees(SqlConnection connection)
      {
         Console.WriteLine();
         Console.WriteLine("=================================");
         Console.WriteLine("Now, SelectEmployees (6)...");

         string tsql = Build_6_Tsql_SelectEmployees();

         using (var command = new SqlCommand(tsql, connection))
         {
            using (SqlDataReader reader = command.ExecuteReader())
            {
               while (reader.Read())
               {
                  Console.WriteLine("{0} , {1} , {2} , {3} , {4}",
                     reader.GetGuid(0),
                     reader.GetString(1),
                     reader.GetInt32(2),
                     (reader.IsDBNull(3)) ? "NULL" : reader.GetString(3),
                     (reader.IsDBNull(4)) ? "NULL" : reader.GetString(4));
               }
            }
         }
      }
```


<a name="cs_7_executenonquery"/>
### <a name="c-block-7-executenonquery"></a>C# 블록 7: ExecuteNonQuery

- [이전](#cs_6b_datareader) &nbsp; / &nbsp; [다음](#cs_8_output)

이 메서드는 어떠한 데이터 행도 반환하지 않고 테이블의 데이터 콘텐츠를 수정하는 작업에 대해 호출됩니다.


```csharp
      static void Submit_Tsql_NonQuery(
         SqlConnection connection,
         string tsqlPurpose,
         string tsqlSourceCode,
         string parameterName = null,
         string parameterValue = null
         )
      {
         Console.WriteLine();
         Console.WriteLine("=================================");
         Console.WriteLine("T-SQL to {0}...", tsqlPurpose);

         using (var command = new SqlCommand(tsqlSourceCode, connection))
         {
            if (parameterName != null)
            {
               command.Parameters.AddWithValue(  // Or, use SqlParameter class.
                  parameterName,
                  parameterValue);
            }
            int rowsAffected = command.ExecuteNonQuery();
            Console.WriteLine(rowsAffected + " = rows affected.");
         }
      }
   } // EndOfClass
}
```


<a name="cs_8_output"/>
### <a name="c-block-8-actual-test-output-to-the-console"></a>C# 블록 8: 콘솔에 대한 실제 테스트 출력

- [이전](#cs_7_executenonquery)

이 섹션에는 프로그램이 콘솔에 전송한 출력을 캡처합니다. guid 값만 테스트를 실행할 때마다 달라집니다.


```text
[C:\csharp_db_test\csharp_db_test\bin\Debug\]
>> csharp_db_test.exe

=================================
Now, CreateTables (10)...

=================================
Now, Inserts (20)...

=================================
Now, UpdateJoin (30)...
2 rows affected, by UpdateJoin.

=================================
Now, DeleteJoin (40)...

=================================
Now, SelectEmployees (50)...
0199be49-a2ed-4e35-94b7-e936acf1cd75 , Alison , 20 , acct , Accounting
f0d3d147-64cf-4420-b9f9-76e6e0a32567 , Barbara , 17 , hres , Human Resources
cf4caede-e237-42d2-b61d-72114c7e3afa , Carol , 22 , acct , Accounting
cdde7727-bcfd-4f72-a665-87199c415f8b , Elle , 15 , NULL , NULL

[C:\csharp_db_test\csharp_db_test\bin\Debug\]
>>
```
