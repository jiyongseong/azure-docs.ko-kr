---
title: 온-프레미스 데이터 게이트웨이 | Microsoft Docs
description: Azure의 Analysis Services 서버가 온-프레미스 데이터 원본에 연결되는 경우 온-프레미스 게이트웨이가 필요합니다.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: abd2d2c8e220a946d08774f8e55ea968008c1757
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32156276"
---
# <a name="connecting-to-on-premises-data-sources-with-azure-on-premises-data-gateway"></a>Azure 온-프레미스 데이터 게이트웨이를 사용하여 온-프레미스 데이터 원본에 연결
온-프레미스 데이터 게이트웨이는 클라우드에서 온-프레미스 데이터 원본과 Azure Analysis Services 서버 간의 보안 데이터 전송을 제공하여 둘을 연결합니다. 동일한 지역에서 여러 Azure Analysis Services 서버를 사용하는 것 외에도 최신 버전의 게이트웨이는 Azure Logic Apps, Power BI, Power Apps, Microsoft Flow와도 작동합니다. 단일 게이트웨이 통해 동일한 지역에서 여러 서비스를 연결할 수 있습니다. 

처음으로 게이트웨이 설치하기는 네 부분으로 이루어진 프로세스입니다.

- **설치 프로그램 다운로드 및 실행** - 이 단계는 조직의 컴퓨터에 게이트웨이 서비스를 설치합니다. 또한 [테넌트](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) Azure AD의 계정을 사용하여 Azure에 로그인합니다. Azure B2B(게스트) 계정은 지원되지 않습니다.

- **게이트웨이 등록** - 이 단계에서는 게이트웨이에 대한 이름 및 복구 키를 지정하고 게이트웨이 클라우드 서비스로 게이트웨이를 등록한 지역을 선택합니다. 게이트웨이 리소스를 Analysis Services 서버와 동일한 지역에 등록할 수 있습니다. 

- **Azure에서 게이트웨이 리소스 만들기** - 이 단계에서는 Azure 구독에서 게이트웨이 리소스를 만듭니다.

- **게이트웨이 리소스에 서버 연결** - 구독에 게이트웨이 리소스가 있으면 서버 연결을 시작할 수 있습니다. 여러 서버 및 기타 리소스를 연결할 수 있습니다.

지금 바로 시작하려면 [온-프레미스 데이터 게이트웨이 설치 및 구성](analysis-services-gateway-install.md)을 참조하세요.

## <a name="how-it-works"> </a>작동 방법
조직의 컴퓨터에 설치하는 게이트웨이는 Windows 서비스인 **온-프레미스 데이터 게이트웨이**로 실행됩니다. 이 로컬 서비스는 Azure Service Bus를 통해 게이트웨이 클라우드 서비스로 등록됩니다. 그런 다음 Azure 구독에 대한 게이트웨이 리소스 게이트웨이 클라우드 서비스를 만듭니다. 그러면 Azure Analysis Services 서버는 게이트웨이 리소스에 연결됩니다. 서버의 모델을 쿼리 또는 처리를 위해 온-프레미스 데이터 원본에 연결해야 하는 경우 쿼리 및 데이터 흐름은 게이트웨이 리소스, Azure Service Bus, 로컬 온-프레미스 데이터 게이트웨이 서비스 및 데이터 원본을 통과합니다. 

![작동 방법](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

쿼리 및 데이터 흐름:

1. 쿼리는 온-프레미스 데이터 원본의 암호화된 자격 증명을 사용하여 클라우드 서비스에서 생성합니다. 그런 다음 처리할 게이트웨이의 큐로 전송됩니다.
2. 게이트웨이 클라우드 서비스에서 쿼리를 분석하고 [Azure Service Bus](https://azure.microsoft.com/documentation/services/service-bus/)에 대한 요청을 보냅니다.
3. 온-프레미스 데이터 게이트웨이가 보류 중인 요청을 위해 Azure Service Bus를 폴링합니다.
4. 게이트웨이가 쿼리를 가져오고, 자격 증명의 암호를 해독하고, 해당 자격 증명으로 데이터 원본에 연결합니다.
5. 게이트웨이가 실행을 위해 데이터 원본에 쿼리를 보냅니다.
6. 결과가 데이터 원본에서 게이트웨이로 다시 전송된 후 클라우드 서비스 및 서버로 전송됩니다.

## <a name="windows-service-account"> </a>Windows 서비스 계정
온-프레미스 데이터 게이트웨이는 Windows 서비스 로그온 자격 증명에 대해 *NT SERVICE\PBIEgwService*를 사용하도록 구성됩니다. 기본적으로 게이트웨이를 설치하는 컴퓨터의 컨텍스트에서 서비스로 로그온할 권리가 있습니다. 이 자격 증명은 온-프레미스 데이터 원본 또는 Azure 계정에 연결하는 데 사용되는 동일한 계정이 아닙니다.  

인증으로 인해 프록시 서버와 함께 문제가 발생하는 경우 Windows 서비스 계정을 도메인 사용자 또는 관리되는 서비스 계정으로 변경할 수도 있습니다.

## <a name="ports"> </a>포트
게이트웨이는 Azure Service Bus에 대한 아웃바운드 연결을 만듭니다. 아웃바운드 포트 TCP 443(기본값), 5671, 5672, 9350-9354에서 통신합니다.  게이트웨이에는 인바운드 포트가 필요하지 않습니다.

방화벽에서 사용자 데이터 영역에 대한 IP 주소를 허용 목록에 추가하는 것이 좋습니다. [Microsoft Azure 데이터 센터 IP 목록](https://www.microsoft.com/download/details.aspx?id=41653)을 다운로드할 수 있습니다. 이 목록은 매주 업데이트됩니다.

> [!NOTE]
> Azure 데이터 센터 IP 목록에 나열된 IP 주소는 CIDR 표기법으로 작성됩니다. 자세한 내용은 [CIDR(Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)을 참조하세요.
>
>

다음은 게이트웨이에서 사용하는 정규화된 도메인 이름입니다.

| 도메인 이름 | 아웃바운드 포트 | 설명 |
| --- | --- | --- |
| *.powerbi.com |80 |설치 프로그램을 다운로드하는 데 사용되는 HTTP입니다. |
| *.powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *.login.windows.net |443 |HTTPS |
| \*.servicebus.windows.net |5671-5672 |AMQP(고급 메시지 큐 프로토콜) |
| \*.servicebus.windows.net |443, 9350-9354 |TCP의 Service Bus Relay에 대한 수신기(Access Control 토큰 획득에 443 필요) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Power BI 서비스에서 게이트웨이에 연결할 수 없는 경우 인터넷 연결을 테스트하는 데 사용합니다. |
| *.microsoftonline-p.com |443 |구성에 따라 인증에 사용합니다. |

### <a name="force-https"></a>Azure Service Bus와의 HTTPS 통신 강제 적용
직접 TCP 대신 HTTPS를 사용하여 Azure Service Bus와 통신하도록 게이트웨이 강제할 수 있지만 성능이 크게 저하될 수 있습니다. 값을 `AutoDetect`에서 `Https`로 변경하여 *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* 파일을 수정할 수 있습니다. 이 파일은 기본적으로 *C:\Program Files\On-premises data gateway*에 위치합니다.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="tenant-level-administration"></a>테넌트 수준 관리 

현재 테넌트 관리자가 다른 사용자가 설치 및 구성한 모든 게이트웨이를 관리할 수 있는 단일 위치가 없습니다.  테넌트 관리자인 경우 설치한 모든 게이트웨이에 대한 관리자로 추가하도록 조직의 사용자에게 요청하는 것이 좋습니다. 이렇게 하면 게이트웨이 설정 페이지 또는 [PowerShell 명령](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters#powershell-support-for-gateway-clusters)을 통해 조직에서 모든 게이트웨이를 관리할 수 있습니다. 


## <a name="faq"></a>질문과 대답

### <a name="general"></a>일반

**Q**: Azure SQL Database와 같은 클라우드에서 데이터 원본에 대한 게이트웨이가 필요하나요? <br/>
**A**: 아니요. 게이트웨이는 온-프레미스 데이터 원본에 연결하는 데만 필요합니다.

**Q**: 데이터 원본과 동일한 컴퓨터에 게이트웨이를 설치해야 하나요? <br/>
**A**: 아니요. 게이트웨이는 일반적으로 동일한 네트워크에서 서버에 연결할 수 있는 기능이 필요합니다.

<a name="why-azure-work-school-account"></a>

**Q:**: 회사 또는 학교 계정을 사용하여 로그인해야 하는 이유는 무엇인가요? <br/>
**A**: 온-프레미스 데이터 게이트웨이를 설치할 때만 회사 또는 학교 계정을 사용할 수 있습니다. 그리고 이 계정은 게이트웨이 리소스를 구성하는 구독과 동일한 테넌트에 있어야 합니다. 로그인 계정은 Azure AD(Azure Active Directory)에서 관리하는 테넌트에 저장됩니다. 일반적으로 Azure AD 계정의 UPN(사용자 계정 이름)은 이메일 주소와 일치합니다.

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
**A**: 서비스에서 게이트웨이를 온-프레미스 게이트웨이 서비스라고 합니다.

**Q**: Azure Active Directory 계정으로 게이트웨이 Windows 서비스를 실행할 수 있나요? <br/>
**A**: 아니요. Windows 서비스에는 유효한 Windows 계정이 있어야 합니다. 기본적으로 서비스는 서비스 SID, NT SERVICE\PBIEgwService를 사용하여 실행됩니다.

**Q:**: 게이트웨이를 인수하려면 어떻게 해야 하나요? <br/>
**A**: 게이트웨이를 인수하려면(제어판 > 프로그램에서 설치/변경 실행) Azure에서 게이트웨이 리소스의 소유자여야 하고 복구 키가 필요합니다. 게이트웨이 리소스 소유자는 액세스 제어에서 구성할 수 있습니다.

### <a name="high-availability"></a>고가용성 및 재해 복구

**Q**: 재해 복구를 위해 어떤 옵션을 사용할 수 있나요? <br/>
**A**: 복구 키를 사용하여 게이트웨이를 복원하거나 이동할 수 있습니다. 게이트웨이를 설치할 때 복구 키를 지정합니다.

**Q**: 복구 키를 사용할 경우의 이점은 무엇인가요? <br/>
**A**: 복구 키는 재해 발생 후 게이트웨이 설정을 마이그레이션하거나 복구할 수 있는 방법을 제공합니다.

## <a name="troubleshooting"> </a>문제 해결

**Q:**: Azure에서 게이트웨이 리소스를 만들려고 할 때 게이트웨이 인스턴스 목록에서 내 게이트웨이를 볼 수 없는 이유는 무엇인가요? <br/>
**A**: 가능한 2가지 이유가 있습니다. 첫째, 현재 또는 다른 구독에서 게이트웨이에 대한 리소스가 이미 만들어진 것일 수 있습니다. 이 가능성을 없애려면 포털에서 **온-프레미스 데이터 게이트웨이** 형식의 리소스를 나열합니다. 모든 리소스를 열거할 때 모든 구독을 선택해야 합니다. 리소스가 만들어지면 게이트웨이 리소스 만들기 포털 환경의 게이트웨이 인스턴스 목록에 해당 게이트웨이가 나타나지 않습니다. 두 번째 가능성은 게이트웨이 설치한 사용자의 Azure AD ID가 Azure Portal에 로그인한 사용자의 ID와 다른 경우입니다. 이를 해결하려면 게이트웨이 설치한 사용자와 동일한 계정을 사용하여 포털에 로그인합니다.

**Q**: 온-프레미스 데이터 원본으로 어떤 쿼리가 전송되고 있는지를 어떻게 알 수 있나요? <br/>
**A**: 전송 중인 쿼리를 포함하는 쿼리 추적을 사용하도록 설정할 수 있습니다. 문제 해결이 끝나면 쿼리 추적을 원래 값으로 다시 변경해야 합니다. 쿼리 추적 기능을 그대로 두면 큰 로그가 만들어집니다.

또한 추적 쿼리를 위해 데이터 원본이 포함하는 도구를 살펴볼 수도 있습니다. 예를 들어 SQL Server 및 Analysis Services의 경우 확장 이벤트 또는 SQL 프로파일러를 사용할 수 있습니다.

**Q**: 게이트웨이 로그는 어디에 있나요? <br/>
**A**: 이 문서 뒷부분에 나오는 로그를 참조하세요.

### <a name="update"></a>최신 버전으로 업데이트

게이트웨이 버전이 오래되면 여러 가지 문제가 발생할 수 있습니다. 일반적으로는 최신 버전을 사용해야 합니다. 한 달 동안 게이트웨이를 업데이트하지 않은 경우 최신 버전의 게이트웨이를 설치하고 문제가 재현되는지 확인할 수 있습니다.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>오류: 그룹에 사용자를 추가하지 못했습니다. (-2147463168 PBIEgwService 성능 로그 사용자)

지원되지 않는 도메인 컨트롤러에 게이트웨이 설치하려고 하면 이 오류가 표시될 수 있습니다. 도메인 컨트롤러가 아닌 컴퓨터에 게이트웨이를 배포해야 합니다.

## <a name="logs"></a>로그

로그 파일은 문제를 해결할 때 중요한 리소스입니다.

#### <a name="enterprise-gateway-service-logs"></a>엔터프라이즈 게이트웨이 서비스 로그

`C:\Users\PBIEgwService\AppData\Local\Microsoft\On-premises data gateway\<yyyyymmdd>.<Number>.log`

#### <a name="configuration-logs"></a>구성 로그

`C:\Users\<username>\AppData\Local\Microsoft\On-premises data gateway\GatewayConfigurator.log`




#### <a name="event-logs"></a>이벤트 로그

데이터 관리 게이트웨이 및 PowerBIGateway 로그를 **응용 프로그램 및 서비스 로그** 아래에서 찾을 수 있습니다.


## <a name="telemetry"></a>원격 분석
원격 분석은 모니터링 및 문제 해결에 사용할 수 있습니다. 기본적으로

**원격 분석을 켜려면**

1.  컴퓨터에서 온-프레미스 데이터 게이트웨이 클라이언트 디렉터리를 확인합니다. 일반적으로 **%systemdrive%\Program Files\On-premises data gateway**입니다. 또는 서비스 콘솔을 열고 실행 파일 경로 즉, 온-프레미스 데이터 게이트웨이 서비스의 속성을 확인합니다.
2.  클라이언트 디렉터리의 Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config 파일에서 SendTelemetry 설정을 true로 변경합니다.
        
    ```
        <setting name="SendTelemetry" serializeAs="String">
                    <value>true</value>
        </setting>
    ```

3.  변경 내용을 저장하고 Windows 서비스 온-프레미스 데이터 게이트웨이 서비스를 다시 시작합니다.




## <a name="next-steps"></a>다음 단계
* [온-프레미스 데이터 게이트웨이 설치 및 구성](analysis-services-gateway-install.md)   
* [Analysis Services 관리](analysis-services-manage.md)
* [Azure Analysis Services에서 데이터 가져오기](analysis-services-connect.md)
