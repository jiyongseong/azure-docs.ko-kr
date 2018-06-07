---
title: "Azure Portal에서 Azure BizTalk Services 만들기 | Microsoft 문서"
description: "Azure Portal에서 Azure BizTalk Services(MABS, WABS)를 프로비전하거나 만드는 방법인 대해 알아봅니다."
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 3ad18876-a649-40d6-9aa0-1509c1d62c43
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 61776b19ba0ee273b78e3b0a6f610e5701251dd0
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="create-biztalk-services-using-the-azure-portal"></a>Azure Portal을 사용하여 BizTalk Services 만들기

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

> [!TIP]
> Azure 포털에 로그인하려면 Azure 계정과 Azure 구독이 있어야 합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. [Azure 무료 평가판](http://go.microsoft.com/fwlink/p/?LinkID=239738)을 참조하세요.


## <a name="CreateService"></a>BizTalk 서비스 만들기

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

BizTalk 서비스 상태에 따라 완료할 수 없는 작업도 일부 있습니다. 이러한 작업 목록은 [BizTalk Services 상태 차트](biztalk-service-state-chart.md)를 참조하세요.

## <a name="post-provisioning-steps"></a>프로비전 후 단계
* [로컬 컴퓨터에 인증서 설치](#InstallCert)
* [프로덕션이 준비된 인증서 추가](#AddCert)
* [Access Control 네임스페이스 가져오기](#ACS)

#### <a name="InstallCert"></a>로컬 컴퓨터에 인증서 설치

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

#### <a name="AddCert"></a>프로덕션이 준비된 인증서 추가

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

#### <a name="ACS"></a>Access Control 네임스페이스 가져오기

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

Visual Studio에서 BizTalk 서비스를 배포할 때 이 Access Control 네임스페이스를 입력합니다. BizTalk 서비스에 대해 Access Control 네임스페이스가 자동으로 만들어집니다.

Access Control 값은 모든 응용 프로그램에서 사용할 수 있습니다. Azure BizTalk Services가 만들어지면 이 Access Control 네임스페이스는 BizTalk 서비스 배포 인증을 제어합니다. 구독을 변경하거나 네임스페이스를 관리하려면 왼쪽 탐색 창에서 **ACTIVE DIRECTORY** 를 선택한 다음 해당 네임스페이스를 선택합니다. 작업 표시줄에 옵션이 나열됩니다.

**관리** 를 클릭하면 Access Control 관리 포털이 열립니다. Access Control 관리 포털에서 BizTalk 서비스는 **서비스 ID**를 사용합니다.  
![Access Control 관리 포털의 ACS 서비스 ID][ACSServiceIdentities]

Access Control Service ID는 응용 프로그램이나 클라이언트가 Access Control을 직접 인증하고 토큰을 받을 수 있도록 하는 자격 증명 집합입니다.

> [!IMPORTANT]
> BizTalk 서비스는 **암호** 값 및 기본 서비스 ID의 **소유자**를 사용합니다. 암호 값 대신 대칭 키 값을 사용하면 다음 오류가 발생할 수 있습니다<br/><br/>*지정한 자격 증명으로 Access Control 관리 서비스 계정에 연결할 수 없습니다*
> 
> 

[ACS 네임스페이스 관리](https://msdn.microsoft.com/library/azure/hh674478.aspx) 에는 일부 지침 및 권장 사항이 나열되어 있습니다.

## <a name="requirements-explained"></a>요구 사항 설명
다음 요구 사항은 무료 버전에는 적용되지 않습니다.

<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>필요한 항목</strong></td>
        <td><strong>필요한 이유</strong></td>
</tr>
<tr>
<td>Azure 구독</td>
<td>구독은 Azure에 로그인할 수 있는 사용자를 결정합니다. 계정 소유자는 <a HREF="https://account.windowsazure.com/Subscriptions">Azure 구독</a>에서 구독을 만듭니다.
<br/><br/>
Azure 계정에는 여러 구독이 있을 수 있으며, 허용된 모든 사용자가 관리할 수 있습니다. 예를 들어 Azure 계정 소유자는 <em>BizTalkServiceSubscription</em>이라는 구독을 만들고, 회사 내의 BizTalk 관리자(예: ContosoBTSAdmins@live.com)에게 이 구독에 대한 액세스를 제공합니다. 이 시나리오에서 BizTalk 관리자는 Azure에 로그인하고 구독의 모든 호스팅 서비스(Azure BizTalk Services 포함)에 대해 전체 관리자 권한을 가집니다. BizTalk 관리자는 Azure 계정 소유자가 아니므로 대금 청구 정보에 대한 액세스 권한이 없습니다.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=267577">Azure에서 구독 및 Storage 계정 관리</a>에 자세한 내용이 나와 있습니다.
</td>
</tr>
<tr>
<td>Azure SQL Database</td>
<td>추적 데이터를 포함하여 BizTalk 서비스에서 사용되는 테이블, 보기 및 저장 프로시저를 저장합니다.
<br/><br/>
BizTalk 서비스를 만들 때 기존 Azure SQL Server, Azure SQL Database를 사용하거나 새로운 서버 또는 데이터베이스를 자동으로 만들 수 있습니다.
<br/><br/>
SQL Database 크기는 자동으로 구성됩니다.  일반적으로 기본 크기는 BizTalk 서비스에 사용하기에 충분합니다. 크기를 변경하면 가격이 영향을 받습니다. <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=234930">Azure SQL Database의 계정 및 대금 청구</a>
 참조<br/><br/>
<strong>참고</strong>
<br/>
<ul>
<li> 새 Azure SQL Server 및 데이터베이스를 만들면 Azure 서비스가 자동으로 사용하도록 설정됩니다. BizTalk 서비스의 경우에는 Azure 서비스를 사용하도록 설정해야 합니다.</li>
<li>기존 Azure SQL Server에서 새 Azure SQL Database를 만들면 서버의 방화벽 규칙이 변경되지 않습니다. 따라서 다른 Azure 서비스가 서버의 데이터베이스에 액세스하지 못할 수 있습니다.</li>
</ul>
</td>
</tr>
<tr>
<td>Azure Access Control 네임스페이스</td>
<td>Azure BizTalk Services로 인증합니다. Visual Studio에서 BizTalk 서비스를 배포할 때 이 Access Control 네임스페이스를 입력합니다. BizTalk 서비스를 만들면 Access Control 네임스페이스가 자동으로 만들어집니다.</td>
</tr>

<tr>
<td>Azure Storage 계정</td>
<td>BizTalk 서비스에서 다음을 저장하는 데 사용되는 테이블, Blob 및 큐에 대한 액세스를 제공합니다.

<ul>
<li>BizTalk 서비스를 모니터링하는 로그 파일. </li>
<li>파트너 사이에 X12 또는 AS2 계약을 생성할 때 보관 기능을 사용하도록 설정하여 메시지 속성을 저장할 수 있습니다. 이 데이터는 Storage 계정에 저장됩니다.</li>
</ul>
<br/>
BizTalk 서비스를 만들 때 기존 Storage 계정을 사용하거나 새 Storage 계정을 자동으로 만들 수 있습니다.
<br/><br/>
기본 저장소 설정은 BizTalk 서비스에 사용하기에 충분합니다.
<br/><br/>
Storage 계정을 만들면 기본 키와 보조 키가 자동으로 만들어집니다. 이러한 키는 Storage 계정에 대한 액세스를 제어합니다. BizTalk 서비스는 자동으로 기본 키를 사용합니다.
<br/><br/>
자세한 내용은 <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285671">저장소</a>를 참조하세요.
</td>
</tr>

<tr>
<td>SSL 개인 인증서</td>
<td>
Azure BizTalk 서비스가 만들어지면 BizTalk 서비스 이름이 포함된 HTTPS URL도 만들어집니다. 이 URL은 자체 서명된 개발 전용 인증서를 사용하도록 자동으로 구성됩니다. 프로덕션의 경우 개인 SSL 인증서가 필요합니다.
<br/><br/>
<strong>중요 SSL 인증서 정보</strong>

<ul>
<li>인증서 만료 날짜는 5년 미만이어야 합니다.</li>
<li>모든 개인 인증서에는 암호가 필요합니다. 이 암호를 확인하고 관리자와 공유하는 것이 좋습니다.</li>
<li>자체 서명된 인증서는 테스트/개발 환경에서 사용됩니다. 자체 서명된 인증서를 사용하는 경우 인증서를 개인 인증서 저장소 및 신뢰할 수 있는 루트 인증 기관 인증서 저장소로 가져옵니다.</li>
</ul>
<br/>프로덕션 인증서 요청을 인증 기관에 보내려면 다음 인증서 속성을 제공합니다.
<br/>

<ul>
<li><strong>확장된 키 사용</strong>: Azure BizTalk Services는 최소한 서버 인증이 필요합니다.</li>
<li><strong>일반 이름</strong>: Azure BizTalk 서비스 URL의 FQDN(정규화된 도메인 이름)을 입력합니다. 이 문서의 <a HREF="#CreateService">BizTalk 서비스 만들기</a>를 참조하세요.</li>
</ul>
<br/>
BizTalk 서비스를 만든 후 새 인증서나 다른 인증서를 추가할 수 있습니다.
</td>
</tr>
</table>
<!---Loc Comment: Please, check link [Create a BizTalk Service] since it is not redirecting to any location.--->



## <a name="hybrid-connections"></a>하이브리드 연결
Azure BizTalk 서비스를 만들면 **하이브리드 연결** 탭을 사용할 수 있습니다.

![하이브리드 연결 탭][HybridConnectionTab]

하이브리드 연결은 Azure 웹 사이트 또는 Azure Mobile Services를 정적 TCP 포트를 사용하는 온-프레미스 리소스(예: SQL Server, MySQL, HTTP 웹 API, Mobile Services 및 대부분의 사용자 지정 웹 서비스)에 연결하는 데 사용됩니다.  하이브리드 연결은 BizTalk 어댑터 서비스와 다릅니다. BizTalk 어댑터 서비스는 Azure BizTalk Services를 온-프레미스 LOB(기간 업무) 시스템에 연결하는 데 사용됩니다.

 하이브리드 연결을 만들고 관리하는 방법을 비롯한 자세한 내용은 [하이브리드 연결](integration-hybrid-connection-overview.md) 을 참조하세요.

## <a name="next-steps"></a>다음 단계
BizTalk 서비스를 만들었으므로 이제 다른 [BizTalk Services: 대시보드, 모니터 및 크기 조정 탭](biztalk-dashboard-monitor-scale-tabs.md)도 익숙해질 수 있습니다. 응용 프로그램에 BizTalk 서비스를 사용할 준비가 되었습니다. 응용 프로그램을 만들려면 [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=235197)로 이동하십시오.

## <a name="see-also"></a>참고 항목
* [BizTalk Services: Editions 차트](biztalk-editions-feature-chart.md)<br/>
* [BizTalk Services: 상태 차트](biztalk-service-state-chart.md)<br/>
* [BizTalk Services: Backup 및 복원](biztalk-backup-restore.md)<br/>
* [BizTalk Services: 제한](biztalk-throttling-thresholds.md)<br/>
* [BizTalk Services: 발급자 이름 및 발급자 키](biztalk-issuer-name-issuer-key.md)<br/>
* [Azure BizTalk Services SDK로 시작하는 방법](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [하이브리드 연결](integration-hybrid-connection-overview.md)

[NewBizTalkService]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
[NEWButton]: ./media/biztalk-provision-services/WABS_New.png
[ProgressComplete]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
[ACSConnectInfo]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
[QuickGlance]: ./media/biztalk-provision-services/WABS_QuickGlance.png
[ACSServiceIdentities]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
[HybridConnectionTab]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png
