
<!--
includes/sql-database-include-connection-string-20-portalshots.md

Latest Freshness check:  2015-09-02 , GeneMi.

## Connection string
-->


### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Azure 포털에서 연결 문자열 가져오기
[Azure Portal](https://portal.azure.com/)에서 클라이언트 프로그램이 Azure SQL Database와 상호 작용하는 데 필요한 연결 문자열을 가져올 수 있습니다. 

1. **모든 서비스** > **SQL 데이터베이스**를 클릭합니다.

2. **SQL 데이터베이스** 블레이드 왼쪽 위에 있는 필터 텍스트 상자에 데이터베이스 이름을 입력합니다.

3. 해당 데이터베이스에 대한 행을 선택합니다.

4. 데이터베이스에 대한 블레이드가 표시된 후 시각적 편의를 위해 **최소화** 단추를 선택하여 검색 및 데이터베이스 필터링에 사용한 블레이드를 축소합니다. 
   
5. 데이터베이스 블레이드에서 **데이터베이스 연결 문자열 표시**를 선택합니다.

6. ADO.NET 연결 라이브러리를 사용하려는 경우 **ADO**라는 레이블이 지정된 문자열을 복사합니다. 
   
    ![데이터베이스에 대한 ADO 연결 문자열 복사][20-CopyAdoConnectionString]
7. 한 가지 형식이나 다른 형식으로 연결 문자열 정보를 클라이언트 프로그램 코드에 붙여넣습니다.

자세한 내용은 [연결 문자열 및 구성 파일](http://msdn.microsoft.com/library/ms254494.aspx)을 참조하세요.

<!-- Image references. -->



[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
