---
title: "Azure App Service 하이브리드 연결 | Microsoft Docs"
description: "하이브리드 연결을 만들고 사용하여 서로 다른 네트워크의 리소스에 액세스하는 방법"
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: ccompy
ms.openlocfilehash: 677642e4e97523ed71ff5857ae27263743dca535
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2017
---
# <a name="azure-app-service-hybrid-connections"></a>Azure App Service 하이브리드 연결 #

하이브리드 연결은 Azure의 서비스인 동시에 Azure App Service의 기능입니다. 서비스로서, App Service에서 사용되는 것 이상의 기능과 용도가 있습니다. 하이브리드 연결 및 App Service 외부에서의 사용법을 자세히 알아보려면 [Azure Relay 하이브리드 연결][HCService]을 참조하세요.

App Service 내에서 하이브리드 연결은 다른 네트워크의 응용 프로그램 리소스에 액세스하는 데 사용될 수 있습니다. 이를 통해 앱에서 응용 프로그램 엔드포인트에 액세스할 수 있습니다. 응용 프로그램에 액세스하는 대체 기능으로는 사용되지 않습니다. App Service에서 사용되는 것처럼 각 하이브리드 연결은 단일 TCP 호스트 및 포트 조합에 상호 연결됩니다. 즉, TCP 수신 대기 포트에 액세스하는 한, 하이브리드 연결 엔드포인트는 모든 운영 체제 및 모든 응용 프로그램에 있을 수 있습니다. 하이브리드 연결 기능은 응용 프로그램 프로토콜이 무엇인지 또는 사용자가 무엇에 액세스하고 있는지 인식하거나 상관하지 않습니다. 단지 네트워크 액세스를 제공합니다.  


## <a name="how-it-works"></a>작동 방법 ##
하이브리드 연결 기능은 Azure Service Bus Relay에 대한 두 개의 아웃바운드 호출로 구성됩니다. 앱이 App Service에서 실행 중인 호스트의 라이브러리와 연결되어 있습니다. HCM(하이브리드 연결 관리자)과 Service Bus Relay 간에도 연결이 있습니다. HCM은 액세스하려는 리소스를 호스팅하는 네트워크 내에서 배포하는 릴레이 서비스입니다. 

앱에는 이 두 개의 결합된 연결을 통해 HCM의 다른 쪽에 고정 호스트:포트 조합에 대한 TCP 터널이 포함됩니다. 연결은 인증 및 권한 부여를 위한 보안 및 SAS(공유 액세스 서명) 키에 TLS 1.2를 사용합니다.    

![하이브리드 연결 대략적인 흐름의 다이어그램][1]

앱이 구성된 하이브리드 연결 끝점과 일치하는 DNS 요청을 실행하면 아웃바운드 TCP 트래픽이 하이브리드 연결을 통해 리디렉션됩니다.  

> [!NOTE]
> 이는 항상 하이브리드 연결에 DNS 이름을 사용해야 함을 의미합니다. 일부 클라이언트 소프트웨어는 끝점에서 IP 주소를 사용하는 경우 DNS 조회를 수행하지 않습니다.
>
>

하이브리드 연결 기능에는 두 가지 유형이 있습니다. Service Bus Relay에서 서비스로 제공되는 하이브리드 연결과 이전 Azure BizTalk Services 하이브리드 연결이 그것입니다. 후자는 포털에서 클래스 하이브리드 연결이라고 합니다. 이 문서의 뒷부분에 이에 대한 더 많은 정보가 있습니다.

### <a name="app-service-hybrid-connection-benefits"></a>App Service 하이브리드 연결의 장점 ###

하이브리드 연결 기능에는 다음과 같은 많은 장점이 있습니다.

- 앱이 온-프레미스 시스템 및 서비스에 안전하게 액세스할 수 있습니다.
- 이 기능에는 인터넷에 액세스할 수 있는 엔드포인트가 필요하지 않습니다.
- 빠르고 쉽게 설정할 수 있습니다. 
- 각 하이브리드 연결이 단일 호스트:포트 조합과 일치하므로 보안에 도움이 됩니다.
- 일반적으로 방화벽 구멍이 필요하지 않습니다. 연결은 모두 표준 웹 포트를 통한 아웃바운드입니다.
- 기능이 네트워크 수준이므로 이는 앱에서 사용되는 언어 및 끝점에서 사용되는 기술과 관계없음을 의미합니다.
- 단일 앱에서 여러 네트워크에 액세스하는 데 사용될 수 있습니다. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>하이브리드 연결로 할 수 없는 작업 ###

하이브리드 연결로 수행할 수 없는 몇 가지 작업은 다음과 같습니다.

- 드라이브 탑재
- UDP 사용
- FTP 수동 모드 또는 확장 수동 모드 같은 동적 포트를 사용하는 TCP 기반 서비스에 액세스
- LDAP 지원(때때로 UDP가 필요하기 때문)
- Active Directory 지원

## <a name="add-and-create-hybrid-connections-in-your-app"></a>앱에 하이브리드 연결 추가 및 만들기 ##

하이브리드 연결은 Azure Portal의 App Service 앱이나 Azure Portal의 Azure Relay를 통해 만들 수 있습니다. 하이브리드 연결과 함께 사용하려는 App Service 앱을 통해 하이브리드 연결을 만드는 것이 좋습니다. 하이브리드 연결을 만들려면 [Azure Portal][portal]로 이동하고 해당 앱을 선택합니다. **네트워킹** > **하이브리드 연결 끝점 구성**을 선택합니다. 여기에서 앱에 구성된 하이브리드 연결을 볼 수 있습니다.  

![하이브리드 연결 목록 스크린샷][2]

새 하이브리드 연결을 추가하려면 **하이브리드 연결 추가**를 선택합니다.  이미 만든 하이브리드 연결 목록이 표시됩니다. 앱에 하나 이상을 추가하려면 원하는 항목을 선택한 다음 **선택한 하이브리드 연결 추가**를 선택합니다.  

![하이브리드 연결 포털 스크린샷][3]

새 하이브리드 연결을 만들려면 **새 하이브리드 연결 만들기**를 선택합니다. 다음을 지정합니다. 

- 엔드포인트 이름
- 엔드포인트 호스트 이름
- 엔드포인트 포트
- 사용할 Service Bus 네임스페이스

![새 하이브리드 연결 만들기 대화 상자 스크린샷][4]

모든 하이브리드 연결은 Service Bus 네임스페이스에 연결되고 각 Service Bus 네임스페이스는 Azure 지역에 있습니다. 네트워크로 인한 대기 시간을 피하려면 앱과 같은 지역에서 Service Bus 네임스페이스 사용을 시도하는 것이 중요합니다.

앱에서 하이브리드 연결을 제거하려면 해당 항목을 마우스 오른쪽 단추로 클릭하고 **연결 끊기**를 선택합니다.  

하이브리드 연결이 앱에 추가되면 간단히 해당 항목을 선택하여 세부 정보를 볼 수 있습니다. 

![하이브리드 연결 세부 정보 스크린샷][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Azure Relay 포털에서 하이브리드 연결 만들기 ###

앱 내의 포털 환경 외에도 Azure Relay 포털에서 하이브리드 연결을 만들 수 있습니다. 하이브리드 연결을 App Service에서 사용하려면:

* 클라이언트 권한 부여가 필요합니다.
* 호스트:포트 조합을 값으로 포함하는 endpoint라는 메타데이터 항목이 있어야 합니다.

## <a name="hybrid-connections-and-app-service-plans"></a>하이브리드 연결 및 App Service 계획 ##

하이브리드 연결 기능은 기본, 표준, 프리미엄 및 격리 요금제 SKU에서만 사용할 수 있습니다. 요금제에는 연결된 제한이 있습니다.  

> [!NOTE] 
> Azure Relay를 기반으로 하는 새로운 하이브리드 연결만 생성할 수 있습니다. 새 BizTalk 하이브리드 연결을 만들 수 없습니다.
>

| 요금제 | 요금제에서 사용 가능한 하이브리드 연결 수 |
|----|----|
| Basic | 5 |
| Standard | 25 |
| Premium | 200 |
| 격리 | 200 |

App Service 계획은 어떤 앱에서 얼마나 많은 하이브리드 연결을 사용하고 있는지 보여줍니다.  

![App Service 계획 속성 스크린샷][6]

세부 정보를 확인할 하이브리드 연결을 선택합니다. 앱 보기에서 확인한 모든 정보를 볼 수 있습니다. 동일한 계획에서 해당 하이브리드 연결을 사용하고 있는 다른 앱의 수도 확인할 수 있습니다.

App Service 계획에 사용할 수 있는 하이브리드 연결 엔드포인트 수에는 제한이 있습니다. 하지만 사용된 각 하이브리드 연결은 해당 계획의 여러 앱에서 사용할 수 있습니다. 예를 들어 App Service 계획에서 5개의 개별 앱에 사용되는 단일 하이브리드 연결은 하나의 하이브리드 연결로 간주됩니다.

하이브리드 연결을 사용하려면 추가 비용이 필요합니다. 자세한 내용은 [Service Bus 가격 책정][sbpricing]을 참조하세요.

## <a name="hybrid-connection-manager"></a>하이브리드 연결 관리자 ##

하이브리드 연결 기능에는 하이브리드 연결 엔드포인트를 호스트하는 네트워크의 릴레이 에이전트가 필요합니다. 이 릴레이 에이전트를 HCM(하이브리드 연결 관리자)이라고 합니다. HCM을 다운로드하려면 [Azure Portal][portal]의 앱에서 **네트워킹** > **하이브리드 연결 끝점 구성**을 선택합니다.  

이 도구는 Windows Server 2012 이상에서 실행됩니다. 설치되면, HCM은 구성된 엔드포인트를 기반으로 Service Bus Relay에 연결하는 서비스로 실행됩니다. HCM의 연결은 포트 443을 통해 Azure로 아웃바운드됩니다.    

HCM을 설치한 후 HybridConnectionManagerUi.exe를 실행하여 도구의 UI를 사용할 수 있습니다. 이 파일은 하이브리드 연결 관리자 설치 디렉터리에 있습니다. Windows 10에서는 검색 상자에서 *하이브리드 연결 관리자 UI*를 검색할 수도 있습니다.  

![하이브리드 연결 관리자 스크린샷][7]

HCM UI를 시작하면 가장 먼저 이 HCM 인스턴스로 구성된 하이브리드 연결을 모두 나열한 테이블이 표시됩니다. 변경하려는 경우 먼저 Azure로 인증합니다. 

HCM에 하나 이상의 하이브리드 연결을 추가하려면 다음을 수행합니다.

1. HCM UI를 시작합니다.
1. **Configure another Hybrid Connection**(다른 하이브리드 연결 구성)을 클릭합니다.
![새 하이브리드 연결 구성 스크린샷][8]

1. Azure 계정으로 로그인합니다.
1. 구독을 선택합니다.
1. HCM이 릴레이할 하이브리드 연결을 선택합니다.
![하이브리드 연결 스크린샷][9]

1. **저장**을 선택합니다.

이제 추가한 하이브리드 연결을 볼 수 있습니다. 구성된 하이브리드 연결을 선택하여 세부 정보를 볼 수도 있습니다.

![하이브리드 연결 세부 정보 스크린샷][10]

구성된 하이브리드 연결을 지원하려면 HCM에 다음이 필요합니다.

- 포트 80 및 443을 통한 Azure에 대한 TCP 액세스
- 하이브리드 연결 엔드포인트에 대한 TCP 액세스
- 엔드포인트 호스트 및 Service Bus 네임스페이스에서 DNS 조회를 수행하는 기능

HCM은 새로운 하이브리드 연결과 BizTalk 하이브리드 연결을 모두 지원합니다.

> [!NOTE]
> Azure Relay는 연결을 위해 웹 소켓을 사용합니다. 이 기능은 Windows Server 2012 이상에서만 사용할 수 있습니다. 따라서 HCM은 Windows Server 2012 이전 버전에서 지원되지 않습니다.
>

### <a name="redundancy"></a>중복 ###

각 HCM은 여러 하이브리드 연결을 지원할 수 있습니다. 특정 하이브리드 연결이 여러 HCM에서 지원될 수도 있습니다. 기본 동작은 특정 엔드포인트에 대해 구성된 HCM을 통해 트래픽을 라우팅하는 것입니다. 네트워크에서 하이브리드 연결에 대한 고가용성이 필요한 경우에는 개별 컴퓨터에서 여러 HCM을 실행합니다. 

### <a name="manually-add-a-hybrid-connection"></a>수동으로 하이브리드 연결 추가 ###

구독 외부에서 사용자가 특정 하이브리드 연결에 대한 HCM 인스턴스를 호스트할 수 있도록 하려면 해당 하이브리드 연결에 대한 게이트웨이 연결 문자열을 공유합니다. [Azure Portal][portal]의 하이브리드 연결 속성에서 이 문자열을 확인할 수 있습니다. 이 문자열을 사용하려면 HCM에서 **수동으로 입력**을 선택하고 게이트웨이 연결 문자열에 붙여넣습니다.


## <a name="troubleshooting"></a>문제 해결 ##

"연결됨" 상태는 하나 이상의 HCM이 해당 하이브리드 연결로 구성되어 Azure에 연결할 수 있음을 의미합니다. 하이브리드 연결의 상태가 **연결됨**으로 표시되지 않으면 Azure에 액세스할 수 있는 HCM에 하이브리드 연결이 구성되어 있지 않은 것입니다.

클라이언트가 엔드포인트에 연결할 수 없는 주된 이유는 엔드포인트가 DNS 이름이 아닌 IP 주소를 사용하여 지정되었기 때문입니다. 앱이 원하는 끝점에 도달할 수 없고 IP 주소가 사용되었으면 HCM이 실행 중인 호스트에서 유효한 DNS 이름을 사용하도록 전환합니다. 또한 HCM을 실행 중인 호스트에서 DNS 이름이 제대로 확인되는지 확인합니다. HCM을 실행 중인 호스트에서 하이브리드 연결 엔드포인트까지 연결이 있는지 확인합니다.  

App Service에서 tcpping 도구는 고급 도구(Kudu) 콘솔에서 호출할 수 있습니다. 이 도구를 통해 TCP 끝점에 액세스할 수 있는지 알 수 있지만 하이브리드 연결 끝점에 액세스할 수 있는지는 알 수 없습니다. 하이브리드 연결 엔드포인트에 대해 콘솔의 도구를 사용하면 호스트:포트 조합을 사용한다는 것만 확인하게 됩니다.  

## <a name="biztalk-hybrid-connections"></a>BizTalk 하이브리드 연결 ##

이전 BizTalk 하이브리드 연결 기능은 새 BizTalk 하이브리드 연결에 대해 중단되었습니다. 앱에 있는 기존 BizTalk 하이브리드 연결은 계속 사용할 수 있지만 Azure Relay를 사용하는 새 하이브리드 연결로 마이그레이션해야 합니다. BizTalk 버전에 비해 새 서비스의 몇 가지 장점은 다음과 같습니다.

- 추가 BizTalk 계정이 필요하지 않습니다.
- TLS는 버전 1.0 대신 버전 1.2입니다.
- 통신은 포트 80 및 443을 통해 이루어지며 IP 주소 및 추가적인 포트 범위 대신 DNS 이름을 사용하여 Azure에 도달합니다.  

기존 BizTalk 하이브리드 연결을 앱에 추가하려면 [Azure Portal][portal]에서 해당 앱으로 이동하고 **네트워킹** > **하이브리드 연결 끝점 구성**을 선택합니다. 클래식 하이브리드 연결 테이블에서 **클래식 하이브리드 연결 추가**를 선택합니다. 그런 다음 BizTalk 하이브리드 연결 목록을 볼 수 있습니다.  


<!--Image references-->
[1]: ./media/app-service-hybrid-connections/hybridconn-connectiondiagram.png
[2]: ./media/app-service-hybrid-connections/hybridconn-portal.png
[3]: ./media/app-service-hybrid-connections/hybridconn-addhc.png
[4]: ./media/app-service-hybrid-connections/hybridconn-createhc.png
[5]: ./media/app-service-hybrid-connections/hybridconn-properties.png
[6]: ./media/app-service-hybrid-connections/hybridconn-aspproperties.png
[7]: ./media/app-service-hybrid-connections/hybridconn-hcm.png
[8]: ./media/app-service-hybrid-connections/hybridconn-hcmadd.png
[9]: ./media/app-service-hybrid-connections/hybridconn-hcmadded.png
[10]: ./media/app-service-hybrid-connections/hybridconn-hcmdetails.png

<!--Links-->
[HCService]: http://docs.microsoft.com/azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: http://portal.azure.com/
[oldhc]: http://docs.microsoft.com/azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: http://azure.microsoft.com/pricing/details/service-bus/
