---
title: Azure SQL Database 감사 시작 | Microsoft Docs
description: Azure SQL 데이터베이스 감사를 사용하여 데이터베이스 이벤트를 감사 로그로 추적합니다.
services: sql-database
author: giladm
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 04/01/2018
ms.author: giladm
ms.openlocfilehash: 95c5793bec228e2da8c98ea9263475f55de739d9
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
ms.locfileid: "34072170"
---
# <a name="get-started-with-sql-database-auditing"></a>SQL 데이터베이스 감사 시작
Azure SQL Database 감사는 데이터베이스 이벤트를 추적하고 Azure Storage 계정의 감사 로그에 이벤트를 기록합니다. 또한

* 감사는 규정 준수를 유지 관리하고, 데이터베이스 작업을 이해하고, 비즈니스 문제나 의심스러운 보안 위반을 나타낼 수 있는 불일치 및 이상 활동을 파악하는 데 도움이 될 수 있습니다.

* 감사를 사용하면 규정을 완전히 준수한다고 보장할 수는 없지만 규정 표준을 보다 쉽게 준수할 수 있습니다. 표준 규정 준수를 지원하는 Azure 프로그램에 대한 자세한 내용은 [Azure 보안 센터](https://azure.microsoft.com/support/trust-center/compliance/)를 참조하세요.


## <a id="subheading-1"></a>Azure SQL Database 감사 개요
SQL Database 감사를 사용하여 다음을 수행할 수 있습니다.


* **유지** 합니다. 감사할 데이터베이스 동작의 범주를 정의할 수 있습니다.
* **보고** 합니다. 미리 구성된 보고서 및 대시보드를 사용하여 활동 및 이벤트 보고를 빠르게 시작할 수 있습니다.
* **분석** 합니다. 의심스러운 이벤트, 특별한 활동 및 추세를 찾을 수 있습니다.

[데이터베이스에 대한 감사 설정](#subheading-2) 섹션에 설명된 대로 여러 이벤트 범주 유형에 대해 감사를 구성할 수 있습니다.

> [!IMPORTANT]
> 감사 로그는 Azure 구독의 Azure Blob Storage에 있는 **Blob 추가**에 기록됩니다.
>
> * **Premium Storage**는 현재 Blob 추가에서 **지원되지 않습니다**.
> * **VNet의 저장소**는 현재 **지원되지 않습니다**.

## <a id="subheading-8"></a>서버 수준 및 데이터베이스 수준 감사 정책 정의

특정 데이터베이스에 대해 또는 기본 서버 정책으로 감사 정책을 정의할 수 있습니다.

* 서버에 있는 모든 기존 데이터베이스 및 새로 만든 데이터베이스에 서버 정책이 적용됩니다.

* *서버 BLOB 감사를 사용하는 경우* *항상 데이터베이스에 적용됩니다*. 데이터베이스 감사 설정에 관계없이 데이터베이스를 감사합니다.

* 서버에서 활성화하는 것 외에도 데이터베이스에서 BLOB 감사를 활성화하는 것은 서버 BLOB 감사 설정을 재정의 또는 변경하지 *않습니다*. 두 감사는 함께 존재합니다. 즉, 데이터베이스는 동시에 두 번 감사됩니다(서버 정책에서 한 번, 데이터베이스 정책에서 한번).

   > [!NOTE]
   > 다음과 같은 경우가 아니면 서버 Blob 감사 및 데이터베이스 Blob 감사를 함께 활성화하지 않아야 합니다.
    > * 특정 데이터베이스에 대해 다른 *저장소 계정* 또는 *보존 기간*을 사용할 수 있습니다.
    > * 서버의 나머지 데이터베이스와는 다른 특정 데이터베이스에 대해 이벤트 형식이나 범주를 감사하려 합니다. 예를 들어 특정 데이터베이스에 대해서만 감사해야 하는 테이블 삽입이 있을 수 있습니다.
   >
   > 그렇지 않으면 서버 수준 Blob 감사만 활성화하고 모든 데이터베이스에 대해 데이터베이스 수준 감사를 비활성화로 유지하는 것이 좋습니다.


## <a id="subheading-2"></a>데이터베이스에 대한 감사 설정
다음 섹션에서는 Azure Portal을 사용하여 감사 구성을 설명합니다.

1. [Azure 포털](https://portal.azure.com)로 이동합니다.
2. 감사할 SQL Database/SQL Server의 **설정** 블레이드로 이동합니다. **설정** 블레이드에서 **감사 및 위협 감지**를 선택합니다.

    <a id="auditing-screenshot"></a> ![탐색 창][1]
3. 서버 감사 정책을 선호하면 데이터베이스 감사 블레이드에서 **서버 설정 보기** 링크를 선택할 수 있습니다. 그런 다음 서버 감사 설정을 보거나 수정할 수 있습니다. 서버 감사 정책은 이 서버의 모든 기존 및 새로 만든 데이터베이스에 적용됩니다.

    ![탐색 창][2]
4. 데이터베이스 수준에서의 BLOB 감사를 선호할 경우 **감사**에 대해 **설정**을 선택하고 **감사 형식**에 대해 **BLOB**을 선택합니다.

    서버 Blob 감사가 활성화되면 데이터베이스 구성 감사가 서버 Blob 감사와 나란히 존재하게 됩니다.

    ![탐색 창][3]
5. **감사 로그 저장소** 블레이드를 열려면 **저장소 세부 정보**를 선택합니다. 로그를 저장할 Azure 저장소 계정 및 보존 기간을 선택합니다. 이전 로그는 삭제됩니다. 그런 후 **OK**를 클릭합니다.
    >[!TIP]
    >감사 보고서 템플릿을 활용하려면 감사되는 모든 데이터베이스에 동일한 저장소 계정을 사용합니다.

    <a id="storage-screenshot"></a> ![탐색 창][4]
6. 감사 이벤트를 사용자 지정하려면 [PowerShell cmdlet](#subheading-7) 또는 [REST API](#subheading-9)를 통해 다음 작업을 수행합니다.
7. 감사 설정을 구성했으면 새로운 위협 감지 기능을 켜고, 보안 경고를 받을 전자 메일을 구성할 수 있습니다. 위협 감지를 사용하면 잠재적인 보안 위협을 나타낼 수 있는 비정상적인 데이터베이스 활동에 대해 사전 경고를 받을 수 있습니다. 자세한 내용은 [위협 감지 시작](sql-database-threat-detection-get-started.md)을 참조하세요.
8. **저장**을 클릭합니다.





## <a id="subheading-3"></a>감사 로그 및 보고서 분석
감사 로그는 설치 중에 선택한 Azure 저장소 계정에 집계됩니다. [Azure Storage 탐색기](http://storageexplorer.com/) 등의 도구를 사용하여 감사 로그를 탐색할 수 있습니다.

Blob 감사 로그는 **sqldbauditlogs**라는 컨테이너 내부에 Blob 파일 컬렉션으로 저장됩니다.

저장소 폴더의 계층 구조, 명명 규칙 및 로그 형식에 대한 자세한 내용은 [BLOB 감사 로그 형식 참조](https://go.microsoft.com/fwlink/?linkid=829599)를 참조하세요.

Blob 감사 로그를 볼 수 있는 여러 가지 방법이 있습니다.

* [Azure Portal을 사용](https://portal.azure.com)합니다.  관련 데이터베이스를 엽니다. 데이터베이스의 **감사 및 위협 감지** 블레이드 맨 위에서 **감사 로그 보기**를 클릭합니다.

    ![탐색 창][7]

    **감사 레코드** 블레이드가 열리고, 여기서 로그를 볼 수 있습니다.

    - **감사 레코드** 블레이드의 맨 위쪽에서 **필터**를 클릭하여 특정 날짜를 볼 수 있습니다.
    - 서버 정책 또는 데이터베이스 정책 감사에서 생성된 감사 레코드 사이를 전환할 수 있습니다.

       ![탐색 창][8]

* 시스템 함수 **sys.fn_get_audit_file**(T-SQL)을 사용하여 테이블 형식의 감사 로그 데이터를 반환할 수 있습니다. 이 함수 사용에 대한 자세한 내용은 [sys.fn_get_audit_file 설명서](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql)를 참조하세요.


* SQL Server Management Studio에서 **감사 파일 병합** 사용(SSMS 17부터 지원):
    1. SSMS 메뉴에서 **파일** > **열기** > **감사 파일 병합**을 선택합니다.

        ![탐색 창][9]
    2. **감사 파일 추가** 대화 상자가 열립니다. **추가** 옵션 중 하나를 선택하여 로컬 디스크에서 감사 파일을 병합할지 또는 Azure Storage에서 감사 파일을 가져올지 선택합니다 Azure Storage 세부 정보 및 계정 키를 제공해야 합니다.

    3. 병합할 모든 파일을 추가했으면 **확인**을 클릭하여 병합 작업을 완료합니다.

    4. 병합된 파일이 SSMS에서 열립니다. 여기에서 파일을 보고 분석할 수 있을 뿐만 아니라 XEL 또는 CSV 파일이나 테이블로 내보낼 수 있습니다.

* Microsoft에서 만든 [동기화 응용 프로그램](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration)을 사용합니다. 이 응용 프로그램은 Azure에서 실행되며 Log Analytics 공용 API를 활용하여 SQL 감사 로그를 Log Analytics에 푸시합니다. 동기화 응용 프로그램은 Log Analytics 대시보드를 통해 사용할 수 있도록 Log Analytics에 SQL 감사 로그를 푸시합니다.

* Power BI를 사용합니다. Power BI에서 감사 로그 데이터를 보고 분석할 수 있습니다. 자세한 내용은 [Power BI 및 다운로드 가능한 템플릿 액세스](https://blogs.msdn.microsoft.com/azuresqldbsupport/2017/05/26/sql-azure-blob-auditing-basic-power-bi-dashboard/)를 참조하세요.

* 포털을 통해 또는 [Azure Storage 탐색기](http://storageexplorer.com/) 같은 도구를 사용하여 Azure Storage Blob 컨테이너에서 로그 파일을 다운로드합니다.
    * 로그 파일을 로컬에 다운로드한 후에는 해당 파일을 두 번 클릭하여 열고, SSMS에서 로그를 살펴보고 분석할 수 있습니다.
    * 또한 Azure Storage 탐색기를 통해 동시에 여러 파일을 다운로드할 수 있습니다. 특정 하위 폴더를 마우스 오른쪽 단추로 클릭하고 **다른 이름으로 저장**을 선택하여 로컬 폴더에 저장합니다.

* 추가 방법:
   * 여러 파일 또는 로그 파일이 포함된 하위 폴더를 다운로드한 후 SSMS 감사 파일 병합 지침에 설명된 대로 파일을 로컬로 병합할 수 있습니다.

   * 프로그래밍 방식으로 Blob 감사 로그를 확인합니다.

     * [확장 이벤트 판독기](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/) C# 라이브러리를 사용합니다.
     * PowerShell을 사용하여 [확장 이벤트 파일을 쿼리](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/)합니다.




## <a id="subheading-5"></a>프로덕션 사례
<!--The description in this section refers to preceding screen captures.-->

### <a id="subheading-6">지역에서 복제된 데이터베이스 감사</a>
지역 복제 데이터베이스에서 주 데이터베이스에 대해 감사를 활성화하면 보조 데이터베이스에도 동일한 감사 정책이 적용됩니다. 또한 주 데이터베이스와는 별도로 **보조 서버**에서 감사를 활성화하여 보조 데이터베이스에 대한 감사를 설정할 수도 있습니다.

* 서버 수준(**권장**): **주 서버** 및 **보조 서버** 둘 다에서 감사를 켭니다. 주 데이터베이스 및 보조 데이터베이스가 해당하는 서버 수준 정책에 따라 독립적으로 감사됩니다.

* 데이터베이스 수준: 보조 데이터베이스에 대한 데이터베이스 수준 감사는 주 데이터베이스 감사 설정에서만 구성될 수 있습니다.
   * Blob 감사는 서버가 아니라 *주 데이터베이스 자체*에서 활성화해야 합니다.
   * 주 데이터베이스에서 Blob 감사가 활성화되면 보조 데이터베이스에서도 활성화됩니다.

    >[!IMPORTANT]
    >데이터베이스 수준 감사에서 보조 데이터베이스의 저장소 설정은 주 데이터베이스와 동일하기 때문에 지역 간 트래픽이 발생합니다. 서버 수준 감사만 활성화하고 모든 데이터베이스에 대해 데이터베이스 수준 감사를 비활성화로 유지하는 것이 좋습니다.
<br>

### <a id="subheading-6">저장소 키 다시 생성</a>
프로덕션에서는 저장소 키를 주기적으로 새로 고치게 됩니다. 키를 새로 고칠 때 감사 정책을 다시 저장해야 합니다. 프로세스는 다음과 같습니다.

1. **저장소 세부 정보** 블레이드를 엽니다. **저장소 액세스 키** 상자에서 **보조**를 선택하고 **확인**을 클릭합니다. 그런 다음 감사 구성 블레이드의 맨 위에서 **저장**을 클릭합니다.

    ![탐색 창][5]
2. 스토리지 구성 블레이드로 이동하고 기본 액세스 키를 다시 생성합니다.

    ![탐색 창][6]
3. 감사 구성 블레이드로 돌아가서 저장소 액세스 키를 보조에서 기본으로 전환하고 **확인**을 클릭합니다. 그런 다음 감사 구성 블레이드의 맨 위에서 **저장**을 클릭합니다.
4. 저장소 구성 블레이드로 돌아와서 보조 액세스 키를 다시 생성합니다(다음 키 새로 고침 주기를 위한 준비).

## <a name="additional-information"></a>추가 정보

* 로그 형식, 저장소 폴더의 계층 구조 및 명명 규칙에 대한 자세한 내용은 [Blob 감사 로그 형식 참조](https://go.microsoft.com/fwlink/?linkid=829599)를 참조하세요.

    > [!IMPORTANT]
    > Azure SQL 데이터베이스 감사는 감사 레코드의 문자 필드에 대해 4000자의 데이터를 저장합니다. 감사가 가능한 작업에서 반환된 **statement** 또는 **data_sensitivity_information** 값에 4000자가 넘게 포함되면 처음 4000자를 초과하는 문자는 **잘리고 감사되지 않습니다**.

* 감사 로그는 Azure 구독의 Azure Blob Storage에 있는 **Blob 추가**에 기록됩니다.
    * **Premium Storage**는 현재 Blob 추가에서 **지원되지 않습니다**.
    * **VNet의 저장소**는 현재 **지원되지 않습니다**.

* 기본 감사 정책에는 모든 작업 및 다음과 같은 일련의 작업 그룹이 포함됩니다. 여기서는 성공 및 실패한 로그인뿐만 아니라 데이터베이스에 대해 실행된 모든 쿼리 및 저장 프로시저를 감사합니다.

    BATCH_COMPLETED_GROUP<br>
    SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP<br>
    FAILED_DATABASE_AUTHENTICATION_GROUP

    [Azure PowerShell을 사용하여 SQL 데이터베이스 감사 관리](#subheading-7) 섹션에 설명된 대로 PowerShell을 사용하여 다양한 형식의 작업 및 작업 그룹에 대한 감사를 구성할 수 있습니다.

## <a id="subheading-7"></a>Azure PowerShell을 사용하여 SQL Database 감사 관리

**PowerShell cmdlet**:

* [데이터베이스 Blob 감사 정책 만들기 또는 업데이트(Set-AzureRMSqlDatabaseAuditing)][105]
* [서버 Blob 감사 정책 만들기 또는 업데이트(Set-AzureRMSqlServerAuditing)][106]
* [데이터베이스 감사 정책 가져오기(Get-AzureRMSqlDatabaseAuditing)][101]
* [서버 Blob 감사 정책 가져오기(Get-AzureRMSqlServerAuditing)][102]

스크립트 예제는 [PowerShell을 사용하여 감사 및 위협 감지 구성](scripts/sql-database-auditing-and-threat-detection-powershell.md)을 참조하세요.

## <a id="subheading-9"></a>REST API를 사용하여 SQL Database 감사 관리

**REST API - Blob 감사**

* [데이터베이스 Blob 감사 정책 만들기 또는 업데이트](https://msdn.microsoft.com/library/azure/mt695939.aspx)
* [서버 Blob 감사 정책 만들기 또는 업데이트](https://msdn.microsoft.com/library/azure/mt771861.aspx)
* [데이터베이스 Blob 감사 정책 가져오기](https://msdn.microsoft.com/library/azure/mt695938.aspx)
* [서버 Blob 감사 정책 가져오기](https://msdn.microsoft.com/library/azure/mt771860.aspx)
* [서버 Blob 감사 작업 결과 가져오기](https://msdn.microsoft.com/library/azure/mt771862.aspx)


<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Manage SQL database auditing using Azure PowerShell]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)
[Manage SQL database auditing using REST API]: #subheading-9

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_storage_key_regeneration.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_regenerate_key.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_blob_audit_records.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_ssms_1.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_ssms_2.png

[101]: /powershell/module/azurerm.sql/get-azurermsqldatabaseauditing
[102]: /powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditing
[103]: /powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing
[104]: /powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing
[105]: /powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditing
[106]: /powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditing
