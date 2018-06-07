---
title: OMS 게이트웨이를 사용하여 컴퓨터 연결 | Microsoft Docs
description: Azure Automation 및 Log Analytics 서비스가 인터넷에 연결되어 있지 않을 때 Azure Automation 및 Log Analytics 서비스에 데이터를 보내기 위해 장치 및 Operations Manager 모니터링 컴퓨터를 OMS 게이트웨이와 연결합니다.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: magoedte
ms.openlocfilehash: b3055e6b22e3f391c0bc3f321cd8117d55a95cf5
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34271652"
---
# <a name="connect-computers-without-internet-access-using-the-oms-gateway"></a>OMS 게이트웨이를 사용하여 인터넷 액세스 없이 컴퓨터 연결
이 문서에서는 직접 연결되거나 Operations Manager 모니터링 컴퓨터가 인터넷에 액세스할 수 없는 경우, OMS 게이트웨이를 사용하여 Azure Automation 및 Log Analytics와의 통신을 구성하는 방법을 설명합니다.  HTTP CONNECT 명령을 사용하여 HTTP 터널링을 지원하는 HTTP 전달 프록시인 OMS 게이트웨이에서 데이터를 수집하고 대신하여 Azure Automation 및 Log Analytics로 보낼 수 있습니다.  

OMS 게이트웨이는 다음을 지원합니다.

* Azure Automation Hybrid Runbook Worker  
* Log Analytics 작업 영역에 직접 연결된 Microsoft Monitoring Agent가 있는 Windows 컴퓨터
* Log Analytics 작업 영역에 직접 연결된 Linux용 OMS 에이전트가 있는 Linux 컴퓨터  
* Log Analytics와 통합된 System Center Operations Manager 2012 SP1(UR7 포함), Operations Manager 2012 R2(UR3 포함), Operations Manager 2016 및 Operations Manager 버전 1801 관리 그룹.  

IT 보안 정책에 따라 네트워크상의 컴퓨터가 POS(Point of Sale) 장치 또는 IT 서비스를 지원하는 서버처럼 인터넷에 연결할 수 없지만 Azure Automation 또는 Log Analytics에 연결하여 관리하고 모니터링해야 하는 경우에는, OMS 게이트웨이와 직접 통신하여 구성을 수신하고 대신 데이터를 전달하도록 구성할 수 있습니다.  이러한 컴퓨터가 OMS 에이전트와 함께 구성되어 Log Analytics 작업 영역에 직접 연결하면 모든 컴퓨터에서 OMS 에이전트를 통해 OMS 게이트웨이와 대신 통신합니다.  게이트웨이는 데이터를 에이전트에서 서비스로 직접 전송하며, 전송되는 어떠한 데이터도 분석하지 않습니다.

Operations Manager 관리 그룹이 Log Analytics와 통합되면, OMS 게이트웨이에 연결하여 구성 정보를 받고 사용하도록 설정한 솔루션에 따라 수집된 데이터를 보내도록 관리 서버를 구성할 수 있습니다.  Operations Manager 에이전트는 Operations Manager 경고, 구성 평가, 인스턴스 공간, 용량 데이터와 같은 일부 데이터를 관리 서버로 보냅니다. IIS 로그, 성능, 보안 이벤트와 같은 기타 대용량 데이터는 OMS 게이트웨이로 직접 보냅니다.  신뢰할 수 없는 시스템을 모니터링하기 위해 하나 이상의 Operations Manager 게이트웨이 서버를 DMZ 또는 격리된 다른 네트워크에 배포한 경우에는 OMS 게이트웨이와 통신할 수 없습니다.  Operations Manager 게이트웨이 서버는 관리 서버에만 보고할 수 있습니다.  OMS 게이트웨이와 통신하도록 Operations Manager 관리 그룹을 구성하면, 설정이 비어 있더라도 프록시 구성 정보가 Log Analytics를 위해 데이터를 수집하도록 구성된 모든 에이전트 관리 컴퓨터에 자동으로 배포됩니다.    

게이트웨이를 통해 Log Analytics와 통신하는 직접 연결 또는 Operations Management 그룹에 고가용성을 제공하려면 네트워크 부하 분산을 사용하여 트래픽을 여러 게이트웨이 서버로 리디렉션하고 배포할 수 있습니다.  한 게이트웨이 서버가 다운되면 트래픽이 사용 가능한 다른 노드로 리디렉션됩니다.  

OMS 에이전트가 통신에 필요한 서비스 엔드포인트를 식별하고 성능 또는 이벤트 데이터 분석을 위한 OMS 게이트웨이를 모니터링하기 위해 OMS 게이트웨이를 실행하는 컴퓨터에 필요합니다.

각 에이전트는 게이트웨이에 네트워크로 연결되어 있어야 게이트웨이와 데이터를 자동으로 송수신할 수 있습니다. 도메인 컨트롤러에 게이트웨이를 설치하는 것은 권장되지 않습니다.

다음 다이어그램에서는 게이트웨이 서버를 사용하여 에이전트에서 Azure Automation 및 Log Analytics로의 직접적인 데이터 흐름을 보여 줍니다.  OMS 게이트웨이가 OMS와 통신하도록 구성된 동일한 포트와 에이전트의 프록시 구성이 일치해야 합니다.  

![직접 에이전트와 서비스의 통신 다이어그램](./media/log-analytics-oms-gateway/oms-omsgateway-agentdirectconnect.png)

다음 다이어그램에서는 Operations Manager 관리 그룹에서 Log Analytics로의 데이터 흐름을 보여 줍니다.   

![Log Analytics와 Operations Manager의 통신 다이어그램](./media/log-analytics-oms-gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="prerequisites"></a>필수 조건

OMS 게이트웨이를 실행하는 컴퓨터를 지정할 때 이 컴퓨터에는 다음 항목이 있어야 합니다.

* Windows 10, Windows 8.1, Windows 7
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2, Windows Server 2008
* .Net Framework 4.5
* 최소 4코어 프로세서 및 8GB 메모리 
* Windows용 OMS 에이전트 

### <a name="language-availability"></a>사용 가능한 언어

OMS 게이트웨이는 다음 언어로 제공됩니다.

- 중국어 (간체)
- 중국어 (번체)
- 체코어
- 네덜란드어
- 영어
- 프랑스어
- 독일어
- 헝가리어
- 이탈리아어
- 일본어
- 한국어
- 폴란드어
- 포르투갈어(브라질)
- 포르투갈어(포르투갈)
- 러시아어
- 스페인어 (국제)

### <a name="supported-encryption-protocols"></a>지원되는 암호화 프로토콜
OMS 게이트웨이는 TLS(전송 계층 보안) 1.0, 1.1 및 1.2만 지원합니다.  SSL(Secure Sockets Layer)은 지원하지 않습니다.

### <a name="supported-number-of-agent-connections"></a>지원되는 에이전트 연결 수
다음 표에는 게이트웨이 서버와 통신하는 지원되는 에이전트 수가 강조 표시되어 있습니다.  이 지원은 6초마다 200KB 이하의 데이터를 업로드하는 에이전트를 기반으로 합니다. 테스트되는 에이전트별 데이터 볼륨은 하루에 약 2.7GB입니다.

|게이트웨이 |지원되는 에이전트의 근사값 수|  
|--------|----------------------------------|  
|- CPU: Intel XEON CPU E5-2660 v3, 2.6GHz 2 코어<br> - 메모리: 4GB<br> - 네트워크 대역폭: 1Gbps| 600|  
|- CPU: Intel XEON CPU E5-2660 v3, 2.6GHz 4 코어<br> - 메모리: 8GB<br> - 네트워크 대역폭: 1Gbps| 1000|  

## <a name="download-the-oms-gateway"></a>OMS 게이트웨이 다운로드

최신 버전의 OMS 게이트웨이 설치 파일을 얻는 방법은 두 가지입니다.

1. [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=54443)에서 다운로드합니다.

2. Azure Portal에서 다운로드합니다.  Azure Portal에 로그인한 후:  

   1. 서비스 목록을 찾아본 다음 **Log Analytics**를 선택합니다.  
   2. 작업 영역을 선택합니다.
   3. **일반**의 작업 영역 블레이드에서 **빠른 시작**을 클릭합니다.
   4. **작업 영역에 연결하는 데이터 원본 선택**에서 **컴퓨터**를 클릭합니다.
   5. **직접 에이전트** 블레이드에서 **OMS 게이트웨이 다운로드**를 클릭합니다.<br><br> ![OMS Gateway 다운로드](./media/log-analytics-oms-gateway/download-gateway.png)

또는 

   1. **설정** 아래 작업 영역 블레이드에서 **고급 설정**을 클릭합니다.
   2. **연결된 원본** > **Windows 서버**로 이동하여 **OMS 게이트웨이 다운로드**를 클릭합니다.

## <a name="install-the-oms-gateway"></a>OMS 게이트웨이 설치

게이트웨이를 설치하려면 다음 단계를 수행합니다.  이전 버전인 *Log Analytics 전달자*를 설치한 경우 이 버전으로 업그레이드됩니다.  

1. 대상 폴더에서 **OMS Gateway.msi**를 두 번 클릭합니다.
2. **Welcome** 페이지에서 **다음**을 클릭합니다.<br><br> ![게이트웨이 설치 마법사](./media/log-analytics-oms-gateway/gateway-wizard01.png)<br> 
3. **사용권 계약** 페이지에서 **동의함**을 선택하여 EULA에 동의하고 **다음**을 클릭합니다.
4. **포트 및 프록시 주소** 페이지에서 다음 단계를 수행합니다.
   1. 게이트웨이에 사용될 TCP 포트 번호를 입력합니다. 설치 프로그램에서 Windows 방화벽의 인바운드 규칙을 이 포트 번호로 구성합니다.  기본값은 8080입니다.
      유효한 포트 번호 범위는 1 ~ 65535입니다. 입력한 내용이 이 범위를 벗어나면 오류 메시지가 표시됩니다.
   2. 필요에 따라 게이트웨이가 설치되어 있는 서버에서 프록시를 통해 통신해야 하는 경우 게이트웨이에서 연결해야 하는 프록시 주소를 입력합니다. 예: `http://myorgname.corp.contoso.com:80`  비워 두면 게이트웨이에서 인터넷에 직접 연결을 시도합니다.  프록시 서버에 인증이 필요한 경우 사용자 이름과 암호를 입력합니다.<br><br> ![게이트웨이 마법사 프록시 구성](./media/log-analytics-oms-gateway/gateway-wizard02.png)<br>   
   3. **다음**을 클릭합니다.
5. Microsoft 업데이트를 사용할 수 없는 경우 이를 사용하도록 설정할 수 있는 [Microsoft 업데이트] 페이지가 표시됩니다. 선택한 후에 **다음**을 클릭합니다. 그렇지 않은 경우 다음 단계를 계속 진행합니다.
6. **대상 폴더** 페이지에서 C:\Program Files\OMS Gateway 기본 폴더를 그대로 유지하거나 게이트웨이를 설치할 위치를 입력하고 **다음**을 클릭합니다.
7. **설치 준비 완료** 페이지에서 **설치**를 클릭합니다. 사용자 계정 컨트롤이 표시되어 설치 권한을 요청할 수 있습니다. 그런 경우에는 **예**를 클릭합니다.
8. 설치가 완료된 후에 **마침**을 클릭합니다. services.msc 스냅인을 열어 서비스가 실행 중인지 확인하고, 서비스 목록에서 **실행 중** 상태의 **OMS 게이트웨이**가 표시되는지 확인할 수 있습니다.<br><br> ![서비스 – OMS 게이트웨이](./media/log-analytics-oms-gateway/gateway-service.png)  

## <a name="configure-network-load-balancing"></a>네트워크 부하 분산 구성 
Microsoft NLB(네트워크 부하 분산) 또는 하드웨어 기반 부하 분산 장치를 사용하여 고가용성 게이트웨이를 구성할 수 있습니다.  부하 분산 장치는 노드 전반에 걸쳐 OMS 에이전트 또는 Operations Manager 관리 서버에서 요청된 연결을 리디렉션하여 트래픽을 관리합니다. 게이트웨이 서버가 하나 다운되면 트래픽은 다른 노드로 리디렉션됩니다.

Windows Server 2016 네트워크 부하 분산 클러스터를 설계하고 배포하는 방법을 알아보려면 [네트워크 부하 분산](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing)을 참조하세요.  다음 단계에서는 Microsoft 네트워크 부하 분산 클러스터를 구성하는 방법에 대해 설명합니다.  

1. NLB 클러스터의 구성원인 Windows 서버에 관리 계정으로 로그인합니다.  
2. [서버 관리자]에서 [네트워크 부하 분산 관리자]를 열고, **도구**를 클릭한 다음 **네트워크 부하 분산 관리자**를 클릭합니다.
3. OMS 게이트웨이 서버를 설치되어 있는 Microsoft Monitoring Agent와 연결하려면 클러스터의 IP 주소를 마우스 오른쪽 단추로 클릭한 후 **클러스터에 호스트 추가**를 클릭합니다.<br><br> ![네트워크 부하 분산 관리자 – 클러스터에 호스트 추가](./media/log-analytics-oms-gateway/nlb02.png)<br> 
4. 연결하려는 게이트웨이 서버의 IP 주소를 입력합니다.<br><br> ![네트워크 부하 분산 관리자 – 클러스터에 호스트 추가: 연결](./media/log-analytics-oms-gateway/nlb03.png) 
    
## <a name="configure-oms-agent-and-operations-manager-management-group"></a>OMS 에이전트 및 Operations Manager 관리 그룹 구성
다음 섹션에는 OMS 게이트웨이와 직접 연결되는 OMS 에이전트, Operations Manager 관리 그룹 또는 Azure Automation Hybrid Runbook Workers를 구성하여 Azure Automation 또는 Log Analytics와 통신하는 방법에 대한 단계가 포함되어 있습니다.  

### <a name="configure-standalone-oms-agent"></a>독립 실행형 OMS 에이전트 구성
Log Analytics에 직접 연결하는 Windows 컴퓨터에 OMS 에이전트를 설치하는 방법에 대한 요구 사항과 단계를 이해하려면 [Log Analytics에 Windows 컴퓨터 연결](log-analytics-windows-agents.md) 또는 Linux 컴퓨터의 경우 [Log Analytics에 Linux 컴퓨터 연결](log-analytics-quick-collect-linux-computer.md)을 참조하세요. 에이전트를 구성하는 동안 프록시 서버를 지정하지 않고 OMS 게이트웨이 서버와 해당 포트 번호의 IP 주소로 해당 값을 바꿉니다.  네트워크 부하 분산 장치 뒤에 여러 개의 게이트웨이 서버를 배포한 경우 OMS 에이전트 프록시 구성은 NLB의 가상 IP 주소입니다.  

Automation Hybrid Runbook Worker와 관련된 내용은 [Hybrid Runbook Worker 배포](../automation/automation-hybrid-runbook-worker.md)를 참조하세요.

### <a name="configure-operations-manager---all-agents-use-the-same-proxy-server"></a>Operations Manager 구성 - 모든 에이전트에서 동일한 프록시 서버 사용
게이트웨이를 추가하도록 Operations Manager를 구성합니다.  설정이 비어 있더라도 Operations Manager 프록시 구성은 Operations Manager에 보고하는 모든 에이전트에 자동으로 적용됩니다.  

게이트웨이를 사용하여 Operations Manager를 지원하려면 다음이 필요합니다.

* Microsoft Monitoring Agent(에이전트 버전 – **8.0.10900.0** 이상)가 게이트웨이 서버에 설치되어 있고 통신할 Log Analytics 작업 영역에 대해 구성되어 있어야 합니다.
* 게이트웨이가 인터넷에 연결되어 있거나 인터넷에 연결되어 있는 프록시 서버에 연결되어 있어야 합니다.

> [!NOTE]
> 게이트웨이 값을 지정하지 않으면 빈 값이 모든 에이전트에 푸시됩니다.
> 

Operations Manager 관리 그룹이 Log Analytics 작업 영역에 처음으로 등록하는 경우에는 관리 그룹에 대한 프록시 구성을 지정하는 옵션을 운영 콘솔에서 사용할 수 없습니다.  이 옵션을 사용하려면 관리 그룹이 서비스에 등록되어 있어야 합니다.  운영 콘솔을 실행하는 시스템에서 Netsh를 사용하여 관리 그룹의 통합 및 모든 관리 서버를 구성하여 시스템 프록시 구성을 업데이트해야 합니다.  

1. 관리자 권한 명령 프롬프트를 엽니다.
   a. **시작**으로 이동하여 **cmd**를 입력합니다.
   나. **명령 프롬프트**를 마우스 오른쪽 단추로 클릭하고 관리자 권한으로 실행**을 선택합니다.
2. 다음 명령을 입력하고 **Enter** 키를 누릅니다.

    `netsh winhttp set proxy <proxy>:<port>`

Log Analytics와 통합을 완료한 후 `netsh winhttp reset proxy`를 실행하여 변경 사항을 제거한 다음, 운영 콘솔에서 **프록시 서버 구성** 옵션을 사용하여 OMS 게이트웨이 서버를 지정할 수 있습니다. 

1. Operations Manager 콘솔을 열고, **Operations Management Suite** 아래에서 **연결**을 클릭한 다음, **프록시 서버 구성**을 클릭합니다.<br><br> ![Operations Manager – 프록시 서버 구성](./media/log-analytics-oms-gateway/scom01.png)<br> 
2. **프록시 서버를 사용하여 Operations Management Suite에 액세스**를 선택한 다음, OMS 게이트웨이 서버의 IP 주소 또는 NLB의 가상 IP 주소를 입력합니다. `http://` 접두사로 시작해야 합니다.<br><br> ![Operations Manager – 프록시 서버 주소](./media/log-analytics-oms-gateway/scom02.png)<br> 
3. **Finish**를 클릭합니다. 이제 Operations Manager 관리 그룹이 게이트웨이 서버를 통해 Log Analytics 서비스와 통신하도록 구성되었습니다.

### <a name="configure-operations-manager---specific-agents-use-proxy-server"></a>Operations Manager 구성 - 특정 에이전트에서 프록시 서버 사용
대규모이거나 복잡한 환경의 경우 특정 서버(또는 그룹) 만 OMS 게이트웨이 서버를 사용할 수 있습니다.  이러한 서버의 경우 관리 그룹에 대한 전역 값으로 이 값을 덮어쓰므로 Operations Manager 에이전트를 직접 업데이트할 수 없습니다.  대신 이러한 값을 푸시하는 데 사용된 규칙을 재정의해야 합니다.  

> [!NOTE] 
> 이와 동일한 구성 기술을 통해 사용자 환경에서 여러 개의 OMS 게이트웨이 서버를 사용할 수 있습니다.  예를 들어 특정 OMS 게이트웨이 서버를 지역별로 지정해야 할 수도 있습니다.
>  

1. Operations Manager 콘솔을 열고 **작성** 작업 영역을 선택합니다.  
2. [작성] 작업 영역에서 **규칙**을 선택하고, Operations Manager 도구 모음에서 **범위** 단추를 클릭합니다. 이 단추를 사용할 수 없는 경우 [모니터링] 창에서 선택한 폴더가 아니라 개체가 있는지 확인합니다. **관리 팩 개체 범위 지정** 대화 상자에서 일반적인 대상 클래스, 그룹 또는 개체의 목록을 표시합니다. 
3. **찾을 대상** 필드에서 **상태 관리 서비스**를 입력하고 목록에서 선택합니다.  **확인**을 클릭합니다.  
4. **Advisor 프록시 설정 규칙**을 검색하고 [운영] 콘솔 도구 모음에서 **재정의**를 클릭한 다음, **규칙 재정의\다음 클래스의 특정 개체: 상태 관리 서비스**를 가리키고, 목록에서 특정 개체를 선택합니다.  필요에 따라 이 재정의를 적용하려는 서버의 상태 관리 서비스 개체를 포함하는 사용자 지정 그룹을 만든 다음 해당 그룹에 재정의를 적용할 수 있습니다.
5. **속성 재정의** 대화 상자에서 **WebProxyAddress** 매개 변수 옆에 있는 **재정의** 열의 확인 표시를 클릭합니다.  **재정의 값** 필드에서 `http://` 접두사로 시작하는 OMS 게이트웨이 서버의 URL을 입력합니다.  

    >[!NOTE]
    > 이미 Microsoft System Center Advisor 모니터링 서버 그룹을 대상으로 하는 Microsoft System Center Advisor 보안 참조 재정의 관리 팩에 포함된 재정의로 자동 관리되므로 이 규칙을 사용할 필요가 없습니다.
    >   

6. **대상 관리 팩 선택** 목록에서 관리 팩을 선택하거나 **새로 만들기**를 클릭하여 봉인되지 않은 새 관리 팩을 만듭니다. 
7. 변경을 완료하면 **확인**을 클릭합니다. 

### <a name="configure-for-automation-hybrid-workers"></a>Automation Hybrid Worker에 대한 구성
사용자 환경에 Automation Hybrid Worker가 있는 경우 다음 단계는 다음 단계는 수동 임시 해결 방법을 제공하여 이를 지원하도록 게이트웨이를 구성합니다.

다음 단계에서 Automation 계정이 있는 Azure 지역을 알아야 합니다. 지역을 확인하려면:

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. Azure Automation 서비스를 선택합니다.
3. 적절한 Azure Automation 계정을 선택합니다.
4. **위치** 아래에서 해당 지역을 확인합니다.<br><br> ![Azure Portal – Automation 계정 위치](./media/log-analytics-oms-gateway/location.png)  

다음 테이블을 사용하여 각 위치에 대한 URL을 확인합니다.

**작업 런타임 데이터 서비스 URL**

| **위치** | **URL** |
| --- | --- |
| 미국 중북부 |ncus-jobruntimedata-prod-su1.azure-automation.net |
| 서유럽 |we-jobruntimedata-prod-su1.azure-automation.net |
| 미국 중남부 |scus-jobruntimedata-prod-su1.azure-automation.net |
| 미국 동부 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| 캐나다 중부 |cc-jobruntimedata-prod-su1.azure-automation.net |
| 북유럽 |ne-jobruntimedata-prod-su1.azure-automation.net |
| 동남아시아 |sea-jobruntimedata-prod-su1.azure-automation.net |
| 인도 중부 |cid-jobruntimedata-prod-su1.azure-automation.net |
| 일본 |jpe-jobruntimedata-prod-su1.azure-automation.net |
| 오스트레일리아 |ase-jobruntimedata-prod-su1.azure-automation.net |

**에이전트 서비스 URL**

| **위치** | **URL** |
| --- | --- |
| 미국 중북부 |ncus-agentservice-prod-1.azure-automation.net |
| 서유럽 |we-agentservice-prod-1.azure-automation.net |
| 미국 중남부 |scus-agentservice-prod-1.azure-automation.net |
| 미국 동부 2 |eus2-agentservice-prod-1.azure-automation.net |
| 캐나다 중부 |cc-agentservice-prod-1.azure-automation.net |
| 북유럽 |ne-agentservice-prod-1.azure-automation.net |
| 동남아시아 |sea-agentservice-prod-1.azure-automation.net |
| 인도 중부 |cid-agentservice-prod-1.azure-automation.net |
| 일본 |jpe-agentservice-prod-1.azure-automation.net |
| 오스트레일리아 |ase-agentservice-prod-1.azure-automation.net |

컴퓨터에서 업데이트 관리 솔루션을 사용하여 패치를 위해 Hybrid Runbook Worker로 자동으로 등록된 경우 다음 단계를 수행합니다.

1. 작업 런타임 데이터 서비스 URL을 OMS 게이트웨이의 허용된 호스트 목록에 추가합니다. 예: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
2. `Restart-Service OMSGatewayService` PowerShell cmdlet을 사용하여 OMS 게이트웨이 서비스를 다시 시작합니다.

컴퓨터가 Hybrid Runbook Worker 등록 cmdlet을 사용하여 Azure Automation에 등록된 경우 다음 단계를 수행합니다.

1. 에이전트 서비스 등록 URL을 OMS 게이트웨이의 허용된 호스트 목록에 추가합니다. 예: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
2. 작업 런타임 데이터 서비스 URL을 OMS 게이트웨이의 허용된 호스트 목록에 추가합니다. 예: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
3. OMS 게이트웨이 서비스를 다시 시작합니다.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>유용한 PowerShell cmdlet
cmdlet은 OMS 게이트웨이 구성 설정을 업데이트하는 데 필요한 작업을 완료하는 데 도움을 줄 수 있습니다. 사용하기 전에 다음을 수행해야 합니다.

1. OMS 게이트웨이를 설치합니다(MSI).
2. PowerShell 콘솔 창을 엽니다.
3. 모듈을 가져오기 위해 `Import-Module OMSGateway` 명령을 입력합니다.
4. 이전 단계에서 오류가 발생하지 않은 경우 모듈을 성공적으로 가져왔으며 cmdlet을 사용할 수 있습니다. `Get-Module OMSGateway`를 입력합니다.
5. cmdlet을 사용하여 변경한 후에는 게이트웨이 서비스를 다시 시작해야 합니다.

3단계에서 오류가 발생하면 모듈을 가져오기가 완료되지 못한 것입니다. PowerShell이 모듈을 찾을 수 없는 경우 오류가 발생할 수 있습니다. 게이트웨이 설치 경로 *C:\Program Files\Microsoft OMS Gateway\PowerShell\OmsGateway*에서 해당 모듈을 찾을 수 있습니다.

| **Cmdlet** | **매개 변수** | **설명** | **예제** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |키 |서비스 구성 가져오기 |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |키(필수) <br> 값 |서비스 구성 변경 |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |릴레이(업스트림) 프록시 주소 가져오기 |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |주소<br> 사용자 이름<br> 암호 |릴레이(업스트림) 프록시 주소(및 자격 증명) 설정 |1. 릴레이 프록시 및 자격 증명 설정:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. 인증이 필요 없는 릴레이 프록시 설정: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. 릴레이 프록시 설정 지우기:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |현재 허용된 호스트 가져오기(로컬로 구성되어 허용된 호스트만, 자동으로 다운로드되어 허용된 호스트는 포함되지 않음) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |호스트(필수) |호스트를 허용 목록에 추가 |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |호스트(필수) |호스트를 허용 목록에서 제거 |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |주체(필수) |클라이언트 인증서 주체를 허용 목록에 추가 |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |주체(필수) |클라이언트 인증서 주체를 허용 목록에서 제거 |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |현재 허용된 클라이언트 인증서 주체 가져오기(로컬로 구성되어 허용된 주체만, 자동으로 다운로드되어 허용된 주체는 포함되지 않음) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>문제 해결
게이트웨이에서 기록한 이벤트를 수집하려면 OMS 에이전트도 설치해야 합니다.<br><br> ![이벤트 뷰어 – OMS 게이트웨이 로그](./media/log-analytics-oms-gateway/event-viewer.png)

**OMS 게이트웨이 이벤트 ID 및 설명**

다음 테이블은 OMS 게이트웨이 로그 이벤트의 이벤트 ID 및 설명을 보여줍니다.

| **ID** | **설명** |
| --- | --- |
| 400 |특정 ID가 없는 모든 응용 프로그램 오류 |
| 401 |잘못된 구성. 예: listenPort = 정수가 아닌 “텍스트" |
| 402 |TLS 핸드셰이크 메시지 구문 분석 중 예외 |
| 403 |네트워킹 오류. 예: 대상 서버에 연결 할 수 없음 |
| 100 |일반 정보 |
| 101 |서비스가 시작됨 |
| 102 |서비스가 중지됨 |
| 103 |클라이언트로부터 HTTP CONNECT 명령을 수신함 |
| 104 |HTTP CONNECT 명령이 아님 |
| 105 |대상 서버가 허용 목록에 없거나 대상 포트가 보안 포트(443)가 아님 <br> <br> 게이트웨이 서버의 MMA 에이전트 및 게이트웨이와 통신하는 에이전트는 동일한 Log Analytics 작업 영역에 연결되어야 합니다. |
| 105 |ERROR TcpConnection – 클라이언트 인증서가 잘못됨: CN=Gateway <br><br> 다음 사항을 확인합니다. <br>    <br> &#149; 버전 번호가 1.0.395.0 이상인 게이트웨이를 사용 중입니다. <br> &#149; 게이트웨이 서버의 MMA 에이전트 및 게이트웨이와 통신하는 에이전트가 동일한 Log Analytics 작업 영역에 연결되어 있습니다. |
| 106 |OMS 게이트웨이는 TLS 1.0, TLS 1.1 및 1.2만 지원합니다.  SSL은 지원하지 않습니다. 지원되지 않는 TLS/SSL 프로토콜 버전의 경우 OMS 게이트웨이는 이벤트 ID 106을 생성합니다.|
| 107 |TLS 세션이 확인됨 |

**수집할 성능 카운터**

다음 테이블은 OMS 게이트웨이에 사용할 수 있는 성능 카운터를 보여줍니다. 카운터는 성능 모니터를 사용하여 추가할 수 있습니다.

| **Name** | **설명** |
| --- | --- |
| OMS 게이트웨이/활성 클라이언트 연결 |활성 클라이언트 네트워크(TCP) 연결의 수 |
| OMS 게이트웨이/오류 수 |오류 수 |
| OMS 게이트웨이/연결된 클라이언트 |연결된 클라이언트 수 |
| OMS 게이트웨이/거부 횟수 |TLS 유효성 검사 오류로 인한 거부 횟수 |

![OMS 게이트웨이 성능 카운터](./media/log-analytics-oms-gateway/counters.png)

## <a name="get-assistance"></a>지원 받기
Azure Portal에 로그인되어 있으면 OMS 게이트웨이 또는 다른 Azure 서비스나 서비스의 기능에 대한 지원 요청을 작성할 수 있습니다.
지원을 요청하려면 포털의 오른쪽 위 모서리에 있는 물음표 기호를 클릭한 후 **새 지원 요청**을 클릭합니다. 그런 다음 새 지원 요청 양식을 입력합니다.

![새 지원 요청](./media/log-analytics-oms-gateway/support.png)

## <a name="next-steps"></a>다음 단계
[데이터 원본을 추가](log-analytics-data-sources.md)하여 연결된 원본에서 데이터를 수집하고 Log Analytics 작업 영역에 저장합니다.
