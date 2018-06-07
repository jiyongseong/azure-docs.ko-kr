---
title: "하이브리드 연결 만들기 및 관리 | Microsoft Docs"
description: "하이브리드 연결을 만들고, 연결을 관리하며, 하이브리드 연결 관리자를 설치하는 방법에 대해 알아봅니다. MABS, WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: erikre
editor: 
ms.assetid: aac0546b-3bae-41e0-b874-583491a395ea
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: ccompy
ms.openlocfilehash: 1751d33b5f6f6a506654daedd15bbd75ae271483
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="create-and-manage-hybrid-connections"></a>하이브리드 연결 만들기 및 관리

> [!IMPORTANT]
> BizTalk 하이브리드 연결은 사용 중지되고 App Service 하이브리드 연결로 대체됩니다. 기존 BizTalk 하이브리드 연결 관리 방법을 비롯한 자세한 내용은 [Azure App Service 하이브리드 연결](../app-service/app-service-hybrid-connections.md)을 참조하세요.

>[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="overview-of-the-steps"></a>단계의 개요
1. 개인 네트워크에 있는 온-프레미스 리소스의 **host name** 또는 **FQDN** of the on-premises resource in your private netw또는k.
2. 하이브리드 연결에 Azure 웹앱 또는 Azure 모바일 앱을 연결합니다.
3. 온-프레미스 리소스에 하이브리드 연결 관리자를 설치하고 특정 하이브리드 연결에 연결합니다. Azure 포털은 한 번 클릭으로 설치하고 연결할 수 있도록 합니다.
4. 하이브리드 연결을 관리하고 연결 키를 관리합니다.

이 항목에서는 이러한 단계를 나열합니다. 

> [!IMPORTANT]
> 하이브리드 연결 끝점을 IP 주소로 설정할 수 있습니다. IP 주소를 사용하는 경우 클라이언트에 따라 온-프레미스 리소스에 도달하지 못할 수 있습니다. 하이브리드 연결은 DNS 조회를 수행하는 클라이언트에 따라 달라집니다. 대부분의 경우에는 **클라이언트** 는 응용 프로그램 코드입니다. 클라이언트가 DNS 조회를 수행하지 않는 경우(도메인 이름(x.x.x.x)인 것처럼 보이면 IP 주소를 확인하지 않음), 하이브리드 연결을 통해 트래픽을 보내지 않습니다.
> 
> 예를 들어, **10.4.5.6** (의사 코드)을 온-프레미스 호스트로 정의합니다.
> 
> **다음 시나리오는 다음과 같은 경우 적용됩니다**.  
> `Application code -> GetHostByName("10.4.5.6") -> Resolves to 127.0.0.3 -> Connect("127.0.0.3") -> Hybrid Connection -> on-prem host`
> 
> **다음 시나리오는 다음과 같은 경우 적용되지 않습니다**.  
> `Application code -> Connect("10.4.5.6") -> ?? -> No route to host`
> 
> 

## <a name="CreateHybridConnection"></a>하이브리드 연결 만들기
[Azure App Service 하이브리드 연결](../app-service/app-service-hybrid-connections.md) **또는** [BizTalk Services REST API](https://msdn.microsoft.com/library/azure/dn232347.aspx)를 사용하여 하이브리드 연결을 만들 수 있습니다. 

<!-- **To create Hybrid Connections using Web Apps**, see [Connect Azure Web Apps to an On-Premises Resource](../app-service-web/web-sites-hybrid-connection-get-started.md). You can also install the Hybrid Connection Manager (HCM) from your web app, which is the preferred method.  -->

#### <a name="additional"></a>추가 항목
* 하이브리드 연결을 여러 개 만들 수 있습니다. 허용되는 연결 수는 [BizTalk Services: Editions 차트](biztalk-editions-feature-chart.md) 를 참조하세요. 
* 각 하이브리드 연결은 송신하는 응용 프로그램 키와 수신하는 온-프레미스 키, 연결 문자열 쌍으로 생성됩니다. 각 쌍에는 기본 및 보조 키가 있습니다. 

## <a name="LinkWebSite"></a>Azure App Service Web App 또는 Mobile App 연결
Azure App Service에서 Web App 또는 Mobile App을 기존 하이브리드 연결에 연결하려면 하이브리드 연결 블레이드에서 **기존 하이브리드 연결 사용**을 선택합니다. 
<!-- See [Access on-premises resources using hybrid connections in Azure App Service](../app-service-web/web-sites-hybrid-connection-get-started.md). -->

## <a name="InstallHCM"></a>온-프레미스에 하이브리드 연결 관리자 설치
하이브리드 연결이 만들어진 후에는 온-프레미스 리소스에 하이브리드 연결 관리자를 설치합니다. 이 관리자는 Azure 웹앱이나 BizTalk 서비스에서 다운로드할 수 있습니다. 

[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]
 
[Azure App Service 하이브리드 연결](../app-service/app-service-hybrid-connections.md)도 좋은 리소스입니다.

<!--
You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:

1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps.
2. Download the Hybrid Connection Manager MSI file. 
3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file. 
4. Using Windows PowerShell, type: 
> Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*” 
--> 

#### <a name="additional"></a>추가 항목
* 하이브리드 연결 관리자는 다음 운영 체제에 설치할 수 있습니다.
  
  * Windows Server 2008 R2(.NET Framework 4.5+ 및 Windows Management Framework 4.0+ 필수)
  * Windows Server 2012 (Windows Management Framework 4.0+ 필수)
  * Windows Server 2012 R2
* 하이브리드 연결 관리자를 설치한 후에 다음이 진행됩니다. 
  
  * Azure에 호스트된 하이브리드 연결은 기본 응용 프로그램 연결 문자열을 사용하도록 자동으로 구성됩니다. 
  * 온-프레미스 리소스는 기본 온-프레미스 연결 문자열을 사용하도록 자동으로 구성됩니다.
* 하이브리드 연결 관리자는 권한 부여를 위해 유효한 온-프레미스 연결 문자열을 사용해야 합니다. Azure Web Apps 또는 Mobile Apps는 권한 부여를 위해 유효한 응용 프로그램 연결 문자열을 사용해야 합니다.
* 다른 서버에 하이브리드 연결 관리자의 다른 인스턴스를 설치하여 하이브리드 연결을 확장할 수 있습니다. 첫 번째 온-프레미스 수신기와 동일한 주소를 사용하도록 온-프레미스 수신기를 구성합니다. 이 상황에서 트래픽이 활성 온-프레미스 수신기 사이에서 임의로 분산됩니다(라운드 로빈). 

## <a name="ManageHybridConnection"></a>하이브리드 연결 관리

[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)] 

[Azure App Service 하이브리드 연결](../app-service/app-service-hybrid-connections.md)도 좋은 리소스입니다.

#### <a name="copyregenerate-the-hybrid-connection-strings"></a>하이브리드 연결 문자열 복사/다시 생성

[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)] 

[Azure App Service 하이브리드 연결](../app-service/app-service-hybrid-connections.md)도 좋은 리소스입니다.

#### <a name="use-group-policy-to-control-the-on-premises-resources-used-by-a-hybrid-connection"></a>그룹 정책을 사용하여 하이브리드 연결에 사용되는 온-프레미스 리소스를 제어합니다.
1. [하이브리드 연결 관리자 관리 템플릿](http://www.microsoft.com/download/details.aspx?id=42963)을 다운로드합니다.
2. 파일의 압축을 풉니다.
3. 그룹 정책을 수정하는 컴퓨터에서 다음을 수행합니다.  
   
   * .ADMX 파일을 *%WINROOT%\PolicyDefinitions* 폴더에 복사합니다.
   * .ADML 파일을 *%WINROOT%\PolicyDefinitions\en-us* 폴더에 복사합니다.

복사되면 그룹 정책 편집기를 사용하여 정책을 변경할 수 있습니다.

## <a name="next"></a>다음
[하이브리드 연결 개요](integration-hybrid-connection-overview.md)

## <a name="see-also"></a>참고 항목
[Microsoft Azure의 BizTalk Services를 관리하기 위한 REST API](http://msdn.microsoft.com/library/azure/dn232347.aspx)  
[BizTalk Services: Editions 차트](biztalk-editions-feature-chart.md)  
[BizTalk Service 만들기](biztalk-provision-services.md)  
[BizTalk Services: 대시보드, 모니터 및 크기 조정 탭](biztalk-dashboard-monitor-scale-tabs.md)

[HybridConnectionTab]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionManageConn.png 
