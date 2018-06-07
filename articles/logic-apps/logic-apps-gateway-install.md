---
title: 온-프레미스 데이터 게이트웨이 설치 - Azure Logic Apps | Microsoft Docs
description: 온-프레미스 데이터 원본에 액세스하기 전에 빠른 데이터 전송 및 암호화를 위해 온-프레미스 데이터 원본과 논리 앱 간에 온-프레미스 데이터 게이트웨이를 설치합니다.
keywords: 데이터에 액세스, 온-프레미스, 데이터 전송, 암호화, 데이터 원본
services: logic-apps
documentationcenter: ''
author: jeffhollan
manager: anneta
editor: ''
ms.assetid: 47e3024e-88a0-4017-8484-8f392faec89d
ms.service: logic-apps
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/14/2017
ms.author: LADocs; millopis; estfan
ms.openlocfilehash: 6c1c954209c08c4ce1262bdc1620755e4a3a6d7e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32179013"
---
# <a name="install-the-on-premises-data-gateway-for-azure-logic-apps"></a>Azure Logic Apps에 온-프레미스 데이터 게이트웨이 설치

논리 앱에서 온-프레미스 데이터 원본에 액세스하기 전에 온-프레미스 데이터 게이트웨이를 설치하고 설정해야 합니다. 게이트웨이는 온-프레미스 시스템과 논리 앱 간에 빠른 데이터 전송 및 암호화를 제공하는 브리지 역할을 합니다. 게이트웨이는 Azure Service Bus를 통해 암호화된 채널의 온-프레미스 원본에서 데이터를 릴레이합니다. 보안으로 시작하는 모든 트래픽은 게이트웨이 에이전트에서 트래픽을 아웃바운드합니다. [데이터 게이트웨이 작동 원리](#gateway-cloud-service)에 대해 자세히 알아봅니다.

게이트웨이는 다음과 같이 온-프레미스 데이터 원본에 대한 연결을 지원합니다.

*   BizTalk Server 2016
*   DB2  
*   파일 시스템
*   Informix
*   MQ
*   MySQL
*   Oracle 데이터베이스
*   PostgreSQL
*   SAP 응용 프로그램 서버 
*   SAP 메시지 서버
*   SharePoint
*   SQL Server
*   Teradata

다음 단계에서는 [게이트웨이와 논리 앱 간의 연결을 설정](./logic-apps-gateway-connection.md)하기 전에 먼저 온-프레미스 데이터 게이트웨이를 설치하는 방법을 보여줍니다. 지원되는 커넥터에 대한 자세한 내용은 [Azure Logic Apps용 커넥터](https://docs.microsoft.com/azure/connectors/apis-list)를 참조하세요. 

다른 서비스에서 게이트웨이를 사용하는 방법에 대한 내용은 다음 문서를 참조하세요.

*   [Microsoft Power BI 온-프레미스 데이터 게이트웨이](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
*   [Azure Analysis Services 온-프레미스 데이터 게이트웨이](../analysis-services/analysis-services-gateway.md)
*   [Microsoft Flow 온-프레미스 데이터 게이트웨이](https://flow.microsoft.com/documentation/gateway-manage/)
*   [Microsoft PowerApps 온-프레미스 데이터 게이트웨이](https://powerapps.microsoft.com/tutorials/gateway-management/)

<a name="requirements"></a>

## <a name="requirements"></a>요구 사항

**최소**

* .NET 4.5 Framework
* 64비트 버전의 Windows 7 또는 Windows Server 2008 R2 이상

**권장**

* 8 코어 CPU
* 8GB 메모리
* 64비트 버전의 Windows 2012 R2 이상

**중요 고려 사항**

* 온-프레미스 데이터 게이트웨이를 로컬 컴퓨터에 설치합니다.
도메인 컨트롤러에 게이트웨이를 설치할 수 없습니다.

   > [!TIP]
   > 데이터 원본과 동일한 컴퓨터에 게이트웨이를 설치할 필요가 없습니다. 대기 시간을 최소화하려면 권한이 있다고 가정하여 가능하면 데이터 원본과 가깝게 또는 동일한 컴퓨터에 게이트웨이를 설치하면 됩니다.

* 게이트웨이가 이러한 상황에서 실행되지 않을 수 있기 때문에 꺼지거나, 절전 또는 인터넷에 연결되지 않은 컴퓨터에서 게이트웨이를 설치하지 않습니다. 또한 무선 네트워크에서는 게이트웨이 성능이 저하될 수 있습니다.

* 설치하는 동안 Microsoft 계정이 아니라 Azure Active Directory(Azure AD)에서 관리하는 [회사 또는 학교 계정](https://docs.microsoft.com/azure/active-directory/sign-up-organization)으로 로그인해야 합니다.

  > [!TIP]
  > MSDN 구독에 Visual Studio가 있는 Microsoft 계정을 사용하려는 경우 Microsoft 계정을 사용하여 [Azure Active Directory에서 디렉터리(테넌트)를 만들거나](../active-directory/develop/active-directory-howto-tenant.md) 기본 디렉터리를 사용합니다. 암호가 있는 사용자를 디렉터리를 추가한 다음 해당 사용자에게 구독에 대한 액세스 권한을 제공합니다. 그런 다음 이 사용자 이름 및 암호를 사용하여 게이트웨이 설치 중에 로그인할 수 있습니다.

  나중에 Azure Portal에서 게이트웨이 설치를 통해 게이트웨이 리소스를 만들고 연결할 때 동일한 회사 또는 학교 계정을 사용해야 합니다. 그런 다음 논리 앱과 온-프레미스 데이터 원본 간의 연결을 만들 때 이 게이트웨이 리소스를 선택합니다. [Azure AD 회사 또는 학교 계정을 사용해야 하는 이유는 무엇인가요?](#why-azure-work-school-account)

  > [!TIP]
  > Office 365 서비스에 등록하고 실제 회사 전자 메일을 제공하지 않은 경우 로그인 주소는 jeff@contoso.onmicrosoft.com과 같을 수 있습니다. 

* 기존 게이트웨이를 14.16.6317.4보다 이전 버전인 설치 관리자를 사용하여 설정한 경우 최신 설치 관리자를 실행하여 게이트웨이 위치를 변경할 수 없습니다. 그러나 최신 설치 관리자를 사용하여 새 게이트웨이를 원하는 위치로 설정할 수 있습니다.
  
  14.16.6317.4보다 이전 버전인 게이트웨이 설치 관리자가 있지만 게이트웨이를 아직 설치하지 않은 경우 최신 설치 관리자를 다운로드하여 사용할 수 있습니다.

<a name="install-gateway"></a>

## <a name="install-the-data-gateway"></a>데이터 게이트웨이 설치

1. [로컬 컴퓨터에서 게이트웨이 설치 관리자를 다운로드하고 실행합니다](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409).

2. 개인정보처리방침 및 사용 약관을 검토하고 동의합니다.

3. 게이트웨이를 설치하려는 로컬 컴퓨터의 경로를 지정합니다.

4. 메시지가 표시되면 Microsoft 계정이 아닌 Azure 회사 또는 학교 계정을 사용하여 로그인합니다.

   ![Azure 회사 또는 학교 계정으로 로그인](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

5. 이제 설치된 게이트웨이를 [게이트웨이 클라우드 서비스](#gateway-cloud-service)에 등록합니다. **이 컴퓨터에 새 게이트웨이 등록**을 선택합니다.

   게이트웨이 클라우드 서비스는 데이터 원본 자격 증명 및 게이트웨이 세부 정보를 암호화하고 저장합니다. 
   또한 서비스는 논리 앱, 온-프레미스 데이터 게이트웨이 및 온-프레미스 데이터 원본 간에 쿼리 및 해당 결과를 라우팅합니다.

6. 게이트웨이 설치에 대한 이름을 제공합니다. 복구 키를 만든 다음 복구 키를 확인합니다. 

   > [!IMPORTANT] 
   > 복구 키는 8자 이상이어야 합니다. 키를 안전한 장소에 저장하고 보관해야 합니다. 기존 게이트웨이를 마이그레이션, 복원 또는 사용하려는 경우에도 이 키가 필요합니다.

   1. 게이트웨이 설치에서 사용하는 클라우드 서비스 게이트웨이 및 Azure Service Bus에 대한 기본 지역을 변경하려면 **지역 변경**을 선택합니다.

      ![지역 변경](./media/logic-apps-gateway-install/change-region-gateway-install.png)

      기본 지역은 Azure AD 테넌트와 연결된 지역입니다.

   2. 다음 창에서 **지역 선택**을 열고 다른 지역을 선택합니다.

      ![다른 지역 선택](./media/logic-apps-gateway-install/select-region-gateway-install.png)

      예를 들어, 대기 시간을 줄일 수 있도록 논리 앱과 동일한 지역을 선택하거나 온-프레미스 데이터 원본에 가장 가까운 지역을 선택할 수 있습니다. 게이트웨이 리소스와 논리 앱은 서로 다른 위치에 있을 수 있습니다.

      > [!IMPORTANT]
      > 설치 후에 이 지역을 변경할 수 없습니다. 또한 이 지역은 게이트웨이에 대한 Azure 리소스를 만들 수 있는 위치를 결정하고 제한합니다. 따라서 Azure에서 게이트웨이 리소스를 만들 때 리소스 위치가 게이트웨이를 설치할 때 선택한 지역과 일치하는지 확인합니다.
      > 
      > 나중에 게이트웨이에 다른 지역을 사용하려는 경우 새 게이트웨이를 설정해야 합니다.

   3. 준비가 되면 **완료**를 선택합니다.

7. 이제 [게이트웨이에 대한 Azure 리소스를 만들](../logic-apps/logic-apps-gateway-connection.md) 수 있도록 Azure Portal에서 이러한 단계에 따릅니다. 

[데이터 게이트웨이 작동 원리](#gateway-cloud-service)에 대해 자세히 알아봅니다.

## <a name="migrate-restore-or-take-over-an-existing-gateway"></a>기존 게이트웨이 마이그레이션, 복원 또는 사용

이러한 작업을 수행하려면 게이트웨이가 설치될 때 지정된 복구 키가 있어야 합니다.

1. 컴퓨터의 시작 메뉴에서 **온-프레미스 데이터 게이트웨이**를 선택합니다.

2. 설치 관리자가 열리면 이전에 게이트웨이 설치에 사용한 것과 동일한 Azure 회사 또는 학교 계정으로 로그인합니다.

3. **기존 게이트웨이 마이그레이션, 복원 또는 사용**을 선택합니다.

4. 마이그레이션, 복원 또는 사용하려는 게이트웨이에 대한 이름과 복구 키를 제공합니다.

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Windows 서비스 계정

온-프레미스 데이터 게이트웨이는 Windows 서비스로 실행되고 Windows 서비스 로그인 자격 증명에 `NT SERVICE\PBIEgwService`를 사용하도록 설정됩니다. 게이트웨이는 기본적으로 게이트웨이를 설치한 컴퓨터에서 "서비스로 로그온(Log on as a service)"할 권한을 가집니다. Azure Portal에서 게이트웨이를 만들고 유지 관리하려면 Windows 서비스 계정에 적어도 **참가자** 권한이 있어야 합니다. 

> [!NOTE]
> Windows 서비스 계정은 온-프레미스 데이터 원본에 연결하는 데 사용되는 동일한 계정 및 클라우드 서비스에 로그인하는 데 사용되는 Azure 회사 또는 학교 계정과 다릅니다.

<a name="restart-gateway"></a>

## <a name="restart-the-gateway"></a>게이트웨이 다시 시작

다른 Windows 서비스와 마찬가지로 여러 가지 방법으로 서비스를 시작하고 중지할 수 있습니다. 예를 들어 게이트웨이가 실행 중인 컴퓨터에서 관리자 권한으로 명령 프롬프트를 열고 다음 명령 중 하나를 실행할 수 있습니다.

* 서비스를 중지하려면 다음 명령을 실행합니다.
  
    `net stop PBIEgwService`

* 서비스를 시작하려면 이 명령을 실행 합니다.
  
    `net start PBIEgwService`

## <a name="configure-a-firewall-or-proxy"></a>방화벽 또는 프록시 구성

게이트웨이는 [Azure Service Bus](https://azure.microsoft.com/services/service-bus/)에 대한 아웃바운드 연결을 만듭니다. 게이트웨이에 대한 프록시 정보를 제공하려면 [프록시 설정 구성](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy/)을 참조하세요.

방화벽 또는 프록시가 연결을 차단할 수 있는지를 확인하려면 컴퓨터가 인터넷 및 [Azure Service Bus](https://azure.microsoft.com/services/service-bus/)에 실제로 연결할 수 있는지 여부를 확인합니다. PowerShell 창에서 다음 명령을 실행합니다.

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

> [!NOTE]
> 이 명령은 Azure Service Bus에 대한 네트워크 연결 및 연결만을 테스트합니다. 따라서 이 명령은 게이트웨이 또는 게이트웨이 클라우드 서비스에서 자격 증명 및 게이트웨이 세부 정보를 암호화하고 저장하는 작업을 수행하는 것과 아무 관련이 없습니다. 
>
> 또한 이 명령은 Windows Server 2012 R2 이상 및 Windows 8.1 이상에서만 사용할 수 있습니다. 이전 OS 버전에서 연결을 테스트하는 데 텔넷을 사용할 수 있습니다. [Azure Service Bus 및 하이브리드 솔루션](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)에 대해 자세히 알아봅니다.

결과는 다음 예제와 비슷합니다.

```text
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

**TcpTestSucceeded**가 **True**로 설정되지 않으면 방화벽에 의해 차단될 수 있습니다. 포괄적으로 설정하려면 **ComputerName** 및 **포트** 값을 이 문서의 [포트 구성](#configure-ports) 아래에 나열된 값으로 바꿉니다.

또한 방화벽이 Azure Service Bus와 Azure 데이터 센터 간에 형성된 연결을 차단할 수도 있습니다. 이 시나리오를 실행할 경우 지역에서 해당 데이터 센터에 대한 모든 IP 주소를 승인(차단 해제)합니다. 해당 IP 주소는 [여기에서 Azure IP 주소 목록을 가져옵니다](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="configure-ports"></a>포트 구성

게이트웨이는 [Azure Service Bus](https://azure.microsoft.com/services/service-bus/)에 대한 아웃바운드 연결을 만들고 아웃바운드 포트인 TCP 443(기본값), 5671, 5672, 9350~9354에서 통신합니다. 게이트웨이에 인바운드 포트가 필요하지 않습니다. [Azure Service Bus 및 하이브리드 솔루션](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)에 대해 자세히 알아봅니다.

| 도메인 이름 | 아웃바운드 포트 | 설명 |
| ------------ | -------------- | ----------- |
| *.analysis.windows.net | 443 | HTTPS | 
| *.login.windows.net | 443 | HTTPS | 
| \*.servicebus.windows.net | 5671-5672 | AMQP(고급 메시지 큐 프로토콜) | 
| \*.servicebus.windows.net | 443, 9350-9354 | TCP의 Service Bus Relay에 대한 수신기(Access Control 토큰 획득에 443 필요) | 
| *.frontend.clouddatahub.net | 443 | HTTPS | 
| *.core.windows.net | 443 | HTTPS | 
| login.microsoftonline.com | 443 | HTTPS | 
| *.msftncsi.com | 443 | Power BI 서비스에서 게이트웨이에 연결할 수 없는 경우 인터넷 연결을 테스트하는 데 사용합니다. | 
||||

도메인 대신 IP 주소를 승인해야 하는 경우 [Microsoft Azure 데이터 센터 IP 범위 목록](https://www.microsoft.com/download/details.aspx?id=41653)을 다운로드하여 사용할 수 있습니다. 일부 경우에는 정규화된 도메인 이름이 아닌 IP 주소를 사용해서 Azure Service Bus를 연결합니다.

<a name="gateway-cloud-service"></a>

## <a name="how-does-the-data-gateway-work"></a>데이터 게이트웨이는 어떻게 작동하나요?

데이터 게이트웨이는 논리 앱, 게이트웨이 클라우드 서비스 및 온-프레미스 데이터 원본 간의 빠르고 안전한 통신을 지원합니다. 

![diagram-for-on-premises-data-gateway-flow](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

따라서 클라우드의 사용자가 온-프레미스 데이터 원본에 연결된 요소와 상호 작용하는 경우:

1. 게이트웨이 클라우드 서비스가 데이터 원본에 대해 암호화된 자격 증명과 함께 쿼리를 만들고, 게이트웨이가 처리할 수 있게 쿼리를 큐에 전송합니다.

2. 게이트웨이 클라우드 서비스에서 쿼리를 분석하고 Azure Service Bus에 대한 요청을 푸시합니다.

3. 온-프레미스 데이터 게이트웨이가 보류 중인 요청을 위해 Azure Service Bus를 폴링합니다.

4. 게이트웨이가 쿼리를 가져오고, 자격 증명의 암호를 해독하고, 해당 자격 증명으로 데이터 원본에 연결합니다.

5. 게이트웨이가 실행을 위해 데이터 원본에 쿼리를 보냅니다.

6. 결과가 데이터 원본에서 게이트웨이로 다시 전송된 후 게이트웨이 클라우드 서비스로 전송됩니다. 게이트웨이 클라우드 서비스에서 해당 결과를 사용합니다.

<a name="faq"></a>

## <a name="tenant-level-administration"></a>테넌트 수준 관리 

현재 테넌트 관리자가 다른 사용자가 설치 및 구성한 모든 게이트웨이를 관리할 수 있는 단일 위치가 없습니다.  테넌트 관리자인 경우 설치한 모든 게이트웨이에 대한 관리자로 추가하도록 조직의 사용자에게 요청하는 것이 좋습니다. 이렇게 하면 게이트웨이 설정 페이지 또는 [PowerShell 명령](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters#powershell-support-for-gateway-clusters)을 통해 조직에서 모든 게이트웨이를 관리할 수 있습니다. 


## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="general"></a>일반

**Q**: SQL Azure와 같은 클라우드에서 데이터 원본에 대한 게이트웨이가 필요하나요? <br/>
**A**: 아니요. 게이트웨이는 온-프레미스 데이터 원본에만 연결됩니다.

**Q**: 데이터 원본과 동일한 컴퓨터에 게이트웨이를 설치해야 하나요? <br/>
**A**: 아니요. 게이트웨이는 제공된 연결 정보를 사용하여 데이터 원본에 연결됩니다. 이러한 의미에서 게이트웨이를 클라이언트 응용 프로그램이라고 생각할 수 있습니다. 게이트웨이는 제공된 서버 이름에 연결할 수 있는 기능이 필요합니다.

<a name="why-azure-work-school-account"></a>

**Q:**: Azure 회사 또는 학교 계정을 사용하여 로그인해야 하는 이유는 무엇인가요? <br/>
**A**: 온-프레미스 데이터 게이트웨이를 설치할 때만 Azure 회사 또는 학교 계정을 사용할 수 있습니다. 로그인 계정은 Azure AD(Azure Active Directory)에서 관리하는 테넌트에 저장됩니다. 일반적으로 Azure AD 계정의 UPN(사용자 계정 이름)은 이메일 주소와 일치합니다.

**Q**: 자격 증명은 어디에 저장되나요? <br/>
**A**: 데이터 원본에 입력한 자격 증명은 게이트웨이 클라우드 서비스에 암호화되어 저장됩니다. 자격 증명은 온-프레미스 데이터 게이트웨이에서 해독됩니다.

**Q**: 네트워크 대역폭에 대한 요구 사항이 있나요? <br/>
**A**: 네트워크 연결의 처리량이 높은 것이 좋습니다. 모든 환경은 다르며 전송되는 데이터의 양은 결과에 영향을 미칩니다. ExpressRoute를 사용하면 온-프레미스 및 Azure 데이터 센터 간의 처리량 수준을 보장하는 데 도움이 될 수 있습니다.
타사 도구인 Azure Speed Test 앱을 사용하면 처리량을 측정하는 데 유용할 수 있습니다.

**Q**: 게이트웨이에서 데이터 원본으로의 쿼리를 실행할 때 나타나는 대기 시간은 얼마나 되나요? 최선의 아키텍처는 무엇인가요? <br/>
**A**: 네트워크 대기 시간을 줄이려면 게이트웨이를 데이터 원본에 최대한 가깝게 설치합니다. 실제 데이터 원본에 게이트웨이를 설치할 수 있는 경우 이 근접성으로 인해 발생하는 대기 시간이 최소화됩니다. 데이터 센터도 고려해야 합니다. 예를 들어 서비스에서 미국 서부 데이터 센터를 사용하고 Azure VM에서 SQL Server가 호스트되는 경우 Azure VM도 미국 서부에 있어야 합니다. 이 근접성으로 인해 대기 시간이 최소화되고 Azure VM에서 송신 요금이 발생하지 않습니다.

**Q**: 결과가 클라우드로 어떻게 다시 전송되나요? <br/>
**A**: Azure Service Bus를 통해 결과가 전송됩니다.

**Q**: 클라우드에서 게이트웨이로의 인바운드 연결이 있나요? <br/>
**A**: 아니요. 게이트웨이는 Azure Service Bus에 대해 아웃바운드 연결을 사용합니다.

**Q**: 아웃바운드 연결을 차단하면 어떻게 되나요? 이러한 연결을 열려면 무엇이 필요한가요? <br/>
**A**: 게이트웨이가 사용하는 포트 및 호스트를 참조하세요.

**Q**: 실제 Windows 서비스를 어떻게 지칭하나요?<br/>
**A**: 서비스에서 게이트웨이를 Power BI 엔터프라이즈 게이트웨이 서비스라고 합니다.

**Q**: Azure Active Directory 계정으로 게이트웨이 Windows 서비스를 실행할 수 있나요? <br/>
**A**: 아니요. Windows 서비스에는 유효한 Windows 계정이 있어야 합니다. 기본적으로 서비스는 서비스 SID, NT SERVICE\PBIEgwService를 사용하여 실행됩니다.

### <a name="high-availability-and-disaster-recovery"></a>고가용성 및 재해 복구

**Q**: 재해 복구를 위해 어떤 옵션을 사용할 수 있나요? <br/>
**A**: 복구 키를 사용하여 게이트웨이를 복원하거나 이동할 수 있습니다. 게이트웨이를 설치할 때 복구 키를 지정합니다.

**Q**: 복구 키를 사용할 경우의 이점은 무엇인가요? <br/>
**A**: 복구 키는 재해 발생 후 게이트웨이 설정을 마이그레이션하거나 복구할 수 있는 방법을 제공합니다.

**Q**: 게이트웨이에 고가용성 시나리오를 사용하기 위한 계획이 있나요? <br/>
**A**: 일부 커넥터는 방식으로 파일 시스템 커넥터 등과 같은 고가용성 시나리오를 지원합니다. 자세한 내용은 참조 [온-프레미스 데이터 게이트웨이에 대한 고가용성 클러스터](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters)를 참조하세요.

## <a name="troubleshooting"></a>문제 해결

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

**Q**: 온-프레미스 데이터 원본으로 어떤 쿼리가 전송되고 있는지를 어떻게 알 수 있나요? <br/>
**A**: 전송 중인 쿼리를 포함하는 쿼리 추적을 사용하도록 설정할 수 있습니다. 문제 해결이 끝나면 쿼리 추적을 원래 값으로 다시 변경해야 합니다. 쿼리 추적 기능을 그대로 두면 큰 로그가 만들어집니다.

또한 추적 쿼리를 위해 데이터 원본이 포함하는 도구를 살펴볼 수도 있습니다. 예를 들어 SQL Server 및 Analysis Services의 경우 확장 이벤트 또는 SQL 프로파일러를 사용할 수 있습니다.

**Q**: 게이트웨이 로그는 어디에 있나요? <br/>
**A**: 이 문서 뒷부분에 나오는 도구를 참조하세요.

### <a name="update-to-the-latest-version"></a>최신 버전으로 업데이트

게이트웨이 버전이 오래되면 여러 가지 문제가 발생할 수 있습니다. 일반적으로는 최신 버전을 사용해야 합니다. 한 달 동안 게이트웨이를 업데이트하지 않은 경우 최신 버전의 게이트웨이를 설치하고 문제가 재현되는지 확인할 수 있습니다.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>오류: 그룹에 사용자를 추가하지 못했습니다. (-2147463168 PBIEgwService 성능 로그 사용자)

지원되지 않는 도메인 컨트롤러에 게이트웨이 설치하려고 하면 이 오류가 표시될 수 있습니다. 도메인 컨트롤러가 아닌 컴퓨터에 게이트웨이를 배포해야 합니다.

## <a name="tools"></a>도구

### <a name="collect-logs-from-the-gateway-configurer"></a>게이트웨이 구성기에서 로그 수집

게이트웨이에 대한 여러 로그를 수집할 수 있습니다. 항상 로그부터 확인하세요.

#### <a name="installer-logs"></a>설치 관리자 로그

`%localappdata%\Temp\Power_BI_Gateway_–Enterprise.log`

#### <a name="configuration-logs"></a>구성 로그

`%localappdata%\Microsoft\Power BI Enterprise Gateway\GatewayConfigurator.log`

#### <a name="enterprise-gateway-service-logs"></a>엔터프라이즈 게이트웨이 서비스 로그

`C:\Users\PBIEgwService\AppData\Local\Microsoft\Power BI Enterprise Gateway\EnterpriseGateway.log`

#### <a name="event-logs"></a>이벤트 로그

데이터 관리 게이트웨이 및 PowerBIGateway 로그를 **응용 프로그램 및 서비스 로그** 아래에서 찾을 수 있습니다.

### <a name="fiddler-trace"></a>Fiddler 추적

[Fiddler](http://www.telerik.com/fiddler) 는 HTTP 트래픽을 모니터링하는 Telerik의 무료 도구입니다. 클라이언트 컴퓨터에서 Power BI를 사용하여 트래픽을 볼 수 있습니다. 이 서비스는 오류 및 기타 관련된 정보를 표시할 수 있습니다.

## <a name="next-steps"></a>다음 단계
    
* [논리 앱에서 온-프레미스 데이터에 연결](../logic-apps/logic-apps-gateway-connection.md)
* [엔터프라이즈 통합 기능](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Azure Logic Apps용 커넥터](../connectors/apis-list.md)
