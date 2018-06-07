---
title: Azure에서 Office 365 관리 솔루션 | Microsoft Docs
description: 이 문서에서는 Azure에서 Office 365 솔루션을 구성하고 사용하는 방법에 대해 자세히 설명합니다.  또한 Log Analytics에서 작성되는 Office 365 레코드에 대해서도 자세히 설명합니다.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: bwren
ms.openlocfilehash: 8797e08ad942687b7d2defd765f4fe3f9765812f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33777851"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Azure에서 Office 365 관리 솔루션(미리 보기)

![Office 365 로고](media/oms-solution-office-365/icon.png)

Office 365 관리 솔루션을 사용하면 Log Analytics에서 Office 365 환경을 모니터링할 수 있습니다.

- Office 365 계정에서 사용자 활동을 모니터링하면 사용량 패턴을 분석하고 동작 경향을 식별할 수 있습니다. 예를 들어 조직 외부나 인기 SharePoint 사이트에서 공유하는 파일과 같은 특정 사용 시나리오를 추출할 수 있습니다.
- 관리자 활동을 모니터링하면 구성 변경 내용이나 높은 권한이 필요한 작업을 추적할 수 있습니다.
- 조직 요구 사항에 따라 사용자 지정할 수 있는 부적절한 사용자 행동을 검색하고 조사할 수 있습니다.
- 감사 및 규정 준수 방식을 제시할 수 있습니다. 예를 들어 기밀 파일에 대한 파일 액세스 작업을 모니터링하여 감사 및 규정 준수 프로세스를 보다 원활하게 진행할 수 있습니다.
- 조직의 Office 365 활동 데이터를 토대로 [log searches](../log-analytics/log-analytics-log-search.md) 기능을 사용해 운영상의 문제를 해결할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
이 솔루션을 설치하고 구성하려면 다음 항목이 필요합니다.

- 조직 Office 365 구독
- 전역 관리자 사용자 계정의 자격 증명
- 감사 데이터를 수신하려면 Office 365 구독에서 [감사를 구성](https://support.office.com/en-us/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin)해야 합니다.  [사서함 감사](https://technet.microsoft.com/library/dn879651.aspx)는 별도로 구성합니다.  감사를 구성하지 않아도 솔루션을 설치하고 기타 데이터를 수집할 수는 있습니다.
 


## <a name="management-packs"></a>관리 팩
이 솔루션은 연결된 관리 그룹에서 관리 팩을 설치하지 않습니다.
  

## <a name="configuration"></a>구성
[구독에 Office 365 솔루션을 추가](../log-analytics/log-analytics-add-solutions.md)한 후에는 Office 365 구독에 해당 솔루션을 연결해야 합니다.

1. [솔루션 추가](../log-analytics/log-analytics-add-solutions.md)에서 설명하는 프로세스를 사용하여 경고 관리 솔루션을 Log Analytics 작업 영역에 추가합니다.
2. OMS 포털에서 **설정**으로 이동합니다.
3. **연결된 원본** 아래에서 **Office 365**를 선택합니다.
4. **Office 365 연결**을 클릭합니다.<br>![Office 365 연결](media/oms-solution-office-365/configure.png)
5. 구독의 전역 관리자인 계정으로 Office 365에 로그인합니다. 
6. 솔루션이 모니터링할 작업과 함께 구독이 나열됩니다.<br>![Office 365 연결](media/oms-solution-office-365/connected.png) 


## <a name="data-collection"></a>데이터 수집
### <a name="supported-agents"></a>지원되는 에이전트
Office 365 솔루션은 [OMS 에이전트](../log-analytics/log-analytics-data-sources.md)에서 데이터를 검색하지 않습니다.  즉, Office 365에서 직접 데이터를 검색합니다.

### <a name="collection-frequency"></a>수집 빈도
Office 365는 레코드가 생성될 때마다 상세 데이터가 포함된 [웹후크 알림](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications)을 Log Analytics로 보냅니다.

## <a name="using-the-solution"></a>솔루션 사용
Office 365 솔루션을 Log Analytics 작업 영역에 추가하면 대시보드에 **Office 365** 타일이 추가됩니다. 이 타일은 현재 환경의 컴퓨터 수 및 그래픽 표현과 업데이트 준수를 표시합니다.<br><br>
![Office 365 요약 타일](media/oms-solution-office-365/tile.png)  

**Office 365** 타일을 클릭하여 **Office 365** 대시보드를 엽니다.

![Office 365 대시보드](media/oms-solution-office-365/dashboard.png)  

대시보드는 다음 표의 열을 포함하고 있습니다. 각 열은 지정된 범위 및 시간 범위에 대한 열의 기준과 일치하는 카운트별로 상위 열 개의 경고를 나열합니다. 열 아래쪽의 모두 보기를 클릭하거나 열 머리글을 클릭하여 전체 목록을 제공하는 로그 검색을 실행할 수 있습니다.

| 열 | 설명 |
|:--|:--|
| 작업 | 모니터링되는 모든 Office 365 구독의 활성 사용자에 대한 정보를 제공합니다. 시간에 따라 발생하는 활동의 수도 확인할 수 있습니다.
| Exchange | Add-MailboxPermission, Set-Mailbox 등의 Exchange Server 활동이 자세히 구분되어 표시됩니다. |
| SharePoint | 사용자가 SharePoint 문서에 대해 가장 많이 수행하는 활동이 표시됩니다. 이 타일에서 드릴다운하면 검색 페이지에 이 활동의 위치와 대상 문서 등 이러한 활동의 세부 정보가 표시됩니다. 예를 들어 파일 액세스 이벤트의 경우 액세스한 문서, 연결된 계정 이름 및 IP 주소를 확인할 수 있습니다. |
| Azure Active Directory | 사용자 암호 다시 설정, 로그인 시도 등 자주 수행하는 사용자 활동이 포함됩니다. 드릴다운하면 결과 상태와 같은 이러한 활동의 세부 정보를 확인할 수 있습니다. 이러한 정보는 대개 Azure Active Directory에서 의심스러운 활동을 모니터링하려는 경우에 유용합니다. |




## <a name="log-analytics-records"></a>Log Analytics 레코드

Office 365 솔루션이 Log Analytics 작업 영역에서 생성하는 모든 레코드의 **Type**은 **OfficeActivity**입니다.  **OfficeWorkload** 속성에 따라 해당 레코드가 참조하는 Office 365 서비스(Exchange, AzureActiveDirectory, SharePoint 또는 OneDrive)가 결정됩니다.  **RecordType** 속성은 작업의 유형을 지정합니다.  속성은 각 작업 유형에 따라 달라지며, 아래 표에 나와 있습니다.

### <a name="common-properties"></a>공용 속성
다음 속성은 모든 Office 365 레코드에 공통적으로 적용됩니다.

| 자산 | 설명 |
|:--- |:--- |
| type | *OfficeActivity* |
| ClientIP | 활동이 기록될 때 사용된 장치의 IP 주소입니다. IP 주소는 IPv4 또는 IPv6 주소 형식으로 표시됩니다. |
| OfficeWorkload | 레코드가 참조하는 office 365 서비스입니다.<br><br>AzureActiveDirectory<br>Exchange<br>SharePoint|
| 작업 | 사용자 또는 관리자 활동의 이름입니다.  |
| OrganizationId | 조직의 Office 365 테넌트 GUID입니다. 이 값은 값이 나타나는 Office 365 서비스에 관계없이 조직에서 항상 동일하게 유지됩니다. |
| RecordType | 수행한 작업의 유형입니다. |
| ResultStatus | Operation 속성에 지정된 작업이 성공했는지 여부를 나타냅니다. 가능한 값은 Succeeded, PartiallySucceded 또는 Failed입니다. Exchange 관리자 활동의 경우 값은 True 또는 False입니다. |
| UserId | 레코드가 기록된 원인인 작업을 수행한 사용자의 UPN(사용자 계정 이름)입니다. 예를 들면 my_name@my_domain_name과 같습니다. SHAREPOINT\system 또는 NTAUTHORITY\SYSTEM과 같은 시스템 계정이 수행한 활동에 대한 레코드도 포함됩니다. | 
| UserKey | UserId 속성에 나와 있는 사용자의 대체 ID입니다.  예를 들어 SharePoint, 비즈니스용 OneDrive 및 Exchange에서 사용자가 수행한 이벤트의 경우에는 이 속성에 PUID(Passport 고유 ID)가 입력됩니다. 시스템 계정이 수행한 이벤트와 기타 서비스에서 발생하는 이벤트의 경우 이 속성이 UserID 속성과 같은 값을 지정할 수도 있습니다.|
| UserType | 작업을 수행한 사용자의 유형입니다.<br><br>관리자<br>응용 프로그램<br>DcAdmin<br>일반 <br>Reserved<br>ServicePrincipal<br>시스템 |


### <a name="azure-active-directory-base"></a>Azure Active Directory 기본 속성
다음 속성은 모든 Azure Active Directory 레코드에 공통적으로 적용됩니다.

| 자산 | 설명 |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Azure AD 이벤트의 유형입니다. |
| ExtendedProperties | Azure AD 이벤트의 확장 속성입니다. |


### <a name="azure-active-directory-account-logon"></a>Azure Active Directory 계정 로그온
다음 레코드는 Active Directory 사용자가 로그온을 시도할 때 생성됩니다.

| 자산 | 설명 |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectoryAccountLogon |
| 응용 프로그램 | Office 15 등의 계정 로그인 이벤트를 트리거하는 응용 프로그램입니다. |
| 클라이언트 | 계정 로그인 이벤트에 사용된 클라이언트 장치, 장치 OS 및 장치 브라우저에 대한 세부 정보입니다. |
| LoginStatus | OrgIdLogon.LoginStatus에서 직접 생성되는 속성입니다. 알림 알고리즘을 사용하면 로그온 실패를 다양한 방식으로 매핑할 수 있습니다. |
| UserDomain | TII(테넌트 ID 정보)입니다. | 


### <a name="azure-active-directory"></a>Azure Active Directory
이러한 레코드는 Azure Active Directory 개체를 추가하거나 변경할 때 생성됩니다.

| 자산 | 설명 |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | Operation 속성에서 식별할 수 있는 작업이 수행된 대상 사용자입니다. |
| 행위자 | 작업을 수행한 사용자 또는 서비스 주체입니다. |
| ActorContextId | 작업자가 속한 조직의 GUID입니다. |
| ActorIpAddress | 작업자의 IP 주소(IPV4 또는 IPV6 주소 형식)입니다. |
| InterSystemsId | Office 365 서비스 내에서 구성 요소에 대한 작업을 추적하는 GUID입니다. |
| IntraSystemId |   작업을 추적하기 위해 Azure Active Directory에서 생성하는 GUID입니다. |
| SupportTicketId | "대신 작업"을 수행하는 상황에서 작업의 고객 지원 티켓 ID입니다. |
| TargetContextId | 대상 사용자가 속한 조직의 GUID입니다. |


### <a name="data-center-security"></a>데이터 센터 보안
다음 레코드는 데이터 센터 보안 감사 데이터에서 생성됩니다.  

| 자산 | 설명 |
|:--- |:--- |
| EffectiveOrganization | 권한 상승/cmdlet의 대상 테넌트 이름입니다. |
| ElevationApprovedTime | 권한 상승이 승인된 시간에 해당하는 타임스탬프입니다. |
| ElevationApprover | Microsoft 관리자의 이름입니다. |
| ElevationDuration | 권한 상승이 활성 상태였던 기간입니다. |
| ElevationRequestId |  권한 상승 요청의 고유 식별자입니다. |
| ElevationRole | 권한 상승이 요청된 역할입니다. |
| ElevationTime | 권한 상승의 시작 시간입니다. |
| Start_Time | cmdlet 실행의 시작 시간입니다. |


### <a name="exchange-admin"></a>Exchange 관리
다음 레코드는 Exchange 구성을 변경하면 생성됩니다.

| 자산 | 설명 |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeAdmin |
| ExternalAccess |  cmdlet을 실행한 주체(조직 내 사용자, Microsoft 데이터 센터 담당자 또는 데이터 센터 서비스 계정, 위임된 관리자)를 지정합니다. 값이 False이면 조직 내 사용자가 cmdlet을 실행한 것입니다. 값이 True이면 데이터 센터 담당자, 데이터 센터 서비스 계정 또는 위임된 관리자가 cmdlet을 실행한 것입니다. |
| ModifiedObjectResolvedName |  cmdlet에 의해 수정된 개체의 사용자에게 친숙한 이름입니다. cmdlet이 개체를 수정하는 경우에만 이 속성이 기록됩니다. |
| OrganizationName | 테넌트의 이름입니다. |
| OriginatingServer | cmdlet이 실행된 서버의 이름입니다. |
| 매개 변수 | Operations 속성에 나와 있는 cmdlet과 함께 사용된 모든 매개 변수의 이름과 값입니다. |


### <a name="exchange-mailbox"></a>Exchange 사서함
다음 레코드는 Exchange 사서함을 변경하거나 추가하면 생성됩니다.

| 자산 | 설명 |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| ClientInfoString | 브라우저 버전, Outlook 버전, 모바일 장치 정보 등 작업을 수행하는 데 사용된 전자 메일 클라이언트에 대한 정보입니다. |
| Client_IPAddress | 작업이 기록될 때 사용된 장치의 IP 주소입니다. IP 주소는 IPv4 또는 IPv6 주소 형식으로 표시됩니다. |
| ClientMachineName | Outlook 클라이언트를 호스트하는 컴퓨터 이름입니다. |
| ClientProcessName | 사서함에 액세스하는 데 사용된 전자 메일 클라이언트입니다. |
| ClientVersion | 전자 메일 클라이언트의 버전입니다. |
| InternalLogonType | 내부용으로 예약된 속성입니다. |
| Logon_Type | 사서함에 액세스하여 기록된 작업을 수행한 사용자의 유형을 나타냅니다. |
| LogonUserDisplayName |    작업을 수행한 사용자의 사용자에게 친숙한 이름입니다. |
| LogonUserSid | 작업을 수행한 사용자의 SID입니다. |
| MailboxGuid | 액세스한 사서함의 Exchange GUID입니다. |
| MailboxOwnerMasterAccountSid | 사서함 소유자 계정의 마스터 계정 SID입니다. |
| MailboxOwnerSid | 사서함 소유자의 SID입니다. |
| MailboxOwnerUPN | 액세스한 사서함의 소유자 전자 메일 주소입니다. |


### <a name="exchange-mailbox-audit"></a>Exchange 사서함 감사
다음 레코드는 사서함 감사 항목을 만들면 생성됩니다.

| 자산 | 설명 |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| 항목 | 작업을 수행한 항목을 나타냅니다. | 
| SendAsUserMailboxGuid | 전자 메일을 보내기 위해 액세스한 사서함의 Exchange GUID입니다. |
| SendAsUserSmtp | 가장하는 사용자의 SMTP 주소입니다. |
| SendonBehalfOfUserMailboxGuid | 대신 메일을 보내기 위해 액세스한 사서함의 Exchange GUID입니다. |
| SendOnBehalfOfUserSmtp | 대신 전자 메일을 보낸 사용자의 SMTP 주소입니다. |


### <a name="exchange-mailbox-audit-group"></a>Exchange 사서함 감사 그룹
다음 레코드는 Exchange 그룹을 변경하거나 추가하면 생성됩니다.

| 자산 | 설명 |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | 그룹의 각 항목에 대한 정보입니다. |
| CrossMailboxOperations | 두 개 이상의 사서함에 대해 작업이 수행되었는지를 나타냅니다. |
| DestMailboxId | CrossMailboxOperations 매개 변수가 True인 경우에만 설정됩니다. 대상 사서함 GUID를 지정합니다. |
| DestMailboxOwnerMasterAccountSid | CrossMailboxOperations 매개 변수가 True인 경우에만 설정됩니다. 대상 사서함 소유자의 마스터 계정 SID에 대한 SID를 지정합니다. |
| DestMailboxOwnerSid | CrossMailboxOperations 매개 변수가 True인 경우에만 설정됩니다. 대상 사서함의 SID를 지정합니다. |
| DestMailboxOwnerUPN | CrossMailboxOperations 매개 변수가 True인 경우에만 설정됩니다. 대상 사서함 소유자의 UPN을 지정합니다. |
| DestFolder | 이동 등의 작업에 대한 대상 폴더입니다. |
| 폴더 | 항목 그룹이 있는 폴더입니다. |
| 폴더 |     폴더를 선택한 후 삭제하는 등의 작업에서 사용된 원본 폴더에 대한 정보입니다. |


### <a name="sharepoint-base"></a>Sharepoint 기본 속성
다음 속성은 모든 SharePoint 레코드에 공통적으로 적용됩니다.

| 자산 | 설명 |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | SharePoint에서 이벤트가 발생했음을 나타냅니다. 가능한 값은 SharePoint 또는 ObjectModel입니다. |
| ItemType | 액세스했거나 수정한 개체의 형식입니다. 개체 형식에 대한 자세한 내용은 ItemType 표를 참조하세요. |
| MachineDomainInfo | 장치 동기화 작업에 대한 정보입니다. 이 정보는 요청에 있는 경우에만 보고됩니다. |
| MachineId |   장치 동기화 작업에 대한 정보입니다. 이 정보는 요청에 있는 경우에만 보고됩니다. |
| Site_ | 사용자가 액세스한 파일 또는 폴더가 있는 사이트의 GUID입니다. |
| Source_Name | 감사된 작업을 트리거한 엔터티입니다. 가능한 값은 SharePoint 또는 ObjectModel입니다. |
| UserAgent | 사용자의 클라이언트 또는 브라우저에 대한 정보입니다. 이 정보는 클라이언트 또는 브라우저에서 제공됩니다. |


### <a name="sharepoint-schema"></a>SharePoint 스키마
다음 레코드는 SharePoint 구성을 변경하면 생성됩니다.

| 자산 | 설명 |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | 사용자 지정 이벤트에 대한 선택적 문자열입니다. |
| Event_Data |  사용자 지정 이벤트에 대한 선택적 페이로드입니다. |
| ModifiedProperties | 이 속성은 사이트 또는 사이트 모음 관리 그룹의 멤버로 사용자를 추가하는 등의 관리 이벤트에 대해 포함됩니다. 이 속성은 수정된 속성의 이름(예: 사이트 관리자 그룹), 수정된 속성의 새 값(예: 사이트 관리자로 추가된 사용자), 그리고 수정된 개체의 이전 값을 포함합니다. |


### <a name="sharepoint-file-operations"></a>SharePoint 파일 작업
다음 레코드는 SharePoint의 파일 작업에 대한 응답으로 생성됩니다.

| 자산 | 설명 |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | 복사하거나 이동한 파일의 파일 확장명입니다. 이 속성은 FileCopied 및 FileMoved 이벤트에 대해서만 표시됩니다. |
| DestinationFileName | 복사하거나 이동한 파일의 이름입니다. 이 속성은 FileCopied 및 FileMoved 이벤트에 대해서만 표시됩니다. |
| DestinationRelativeUrl | 파일을 복사하거나 이동한 대상 폴더의 URL입니다. SiteURL, DestinationRelativeURL 및 DestinationFileName 매개 변수의 값 조합은 복사한 파일의 전체 경로 이름인 ObjectID 속성의 값과 같습니다. 이 속성은 FileCopied 및 FileMoved 이벤트에 대해서만 표시됩니다. |
| SharingType | 리소스를 공유한 사용자에게 할당된 공유 권한 유형입니다. UserSharedWith 매개 변수를 통해 이 사용자를 식별합니다. |
| Site_Url | 사용자가 액세스한 파일 또는 폴더가 있는 사이트의 URL입니다. |
| SourceFileExtension | 사용자가 액세스한 파일의 파일 확장명입니다. 액세스한 개체가 폴더이면 이 속성은 비어 있습니다. |
| SourceFileName |  사용자가 액세스한 파일 또는 폴더의 이름입니다. |
| SourceRelativeUrl | 사용자가 액세스한 파일이 포함된 폴더의 URL입니다. SiteURL, SourceRelativeURL 및 SourceFileName 매개 변수의 값 조합은 사용자가 액세스한 파일의 전체 경로 이름인 ObjectID 속성의 값과 같습니다. |
| UserSharedWith |  리소스를 공유한 사용자입니다. |




## <a name="sample-log-searches"></a>샘플 로그 검색
다음 테이블은 이 솔루션에 의해 수집된 업데이트 레코드에 대한 샘플 로그 검색을 제공합니다.

| 쿼리 | 설명 |
| --- | --- |
|Office 365 구독의 모든 작업 수 |Type = OfficeActivity &#124; measure count() by Operation |
|SharePoint 사이트 사용량|Type=OfficeActivity OfficeWorkload=sharepoint &#124; measure count() as Count by SiteUrl &#124; sort Count asc|
|사용자 유형별 파일 액세스 작업|Type=OfficeActivity OfficeWorkload=sharepoint Operation=FileAccessed &#124; measure count() by UserType|
|특정 키워드를 사용한 검색|Type=OfficeActivity OfficeWorkload=azureactivedirectory "MyTest"|
|Exchange에서 외부 작업 모니터링|Type=OfficeActivity OfficeWorkload=exchange ExternalAccess = true|



## <a name="troubleshooting"></a>문제 해결

Office 365 솔루션이 데이터를 정상적으로 수집하지 않는 경우 OMS 포털의 **설정** -> **연결된 원본** -> **Office 365**에서 솔루션의 상태를 확인합니다. 아래 표에 각 상태에 대한 설명이 나와 있습니다.

| 상태 | 설명 |
|:--|:--|
| Active | Office 365 구독이 활성 상태이며 작업이 Log Analytics 작업 영역에 올바르게 연결되어 있습니다. |
| Pending | Office 365 구독이 활성 상태이지만 작업이 Log Analytics 작업 영역에 아직 정상적으로 연결되지 않았습니다. Office 365 구독을 처음으로 연결하면 모든 작업은 정상적으로 연결될 때까지 이 상태입니다. 모든 작업이 활성 상태로 전환되려면 24시간 정도 걸립니다. |
| 비활성 | Office 365 구독이 비활성 상태입니다. 자세한 내용은 Office 365 관리 페이지를 확인하세요. Office 365 구독을 활성화한 후 Log Analytics 작업 영역에서 구독의 연결을 해제했다가 다시 연결하면 데이터가 수신되기 시작합니다. |



## <a name="next-steps"></a>다음 단계
* [Log Analytics](../log-analytics/log-analytics-log-searches.md)의 로그 검색을 사용하여 자세한 업데이트 데이터 보기
* [고유한 대시보드 만들기](../log-analytics/log-analytics-dashboards.md)를 수행하여 자주 사용하는 Office 365 검색 쿼리를 표시합니다.
* 중요한 Office 365 활동에 대해 미리 알림을 받을 수 있도록 [경고 만들기](../log-analytics/log-analytics-alerts.md)를 수행합니다.  
