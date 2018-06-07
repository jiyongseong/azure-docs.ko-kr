---
title: Azure ExpressRoute 회로의 네트워크 성능 모니터 구성 | Microsoft Docs
description: Azure ExpressRoute 회로에 대해 클라우드 기반 네트워크 모니터링을 구성합니다.
documentationcenter: na
services: expressroute
author: ajaycode
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: agummadi
ms.openlocfilehash: 0d8bee936717a5668e16fbd66d416fcc4e738814
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="configure-network-performance-monitor-for-expressroute"></a>ExpressRoute에 대한 네트워크 성능 모니터 구성

NPM(네트워크 성능 모니터)은 Azure 클라우드 배포 및 온-프레미스 위치(지점 등) 간의 연결을 모니터링하는 클라우드 기반 네트워크 모니터링 솔루션입니다. NPM은 Log Analytics의 일부입니다. 현재 NPM은 개인 피어링을 사용하도록 구성된 ExpressRoute 회로에 대한 네트워크 성능을 모니터링할 수 있는 ExpressRoute 확장을 제공합니다. ExpressRoute에 대한 NPM을 구성하면 네트워크 문제를 감지하여 파악하고 제거할 수 있습니다.

다음을 수행할 수 있습니다.

* 다양한 VNet에서 손실 및 지연 모니터링, 경고 설정

* 네트워크의 모든 경로(중복 경로 포함) 모니터링

* 복제하기 어려운 일시적 및 특정 시점의 네트워크 문제 해결

* 성능 저하를 야기한 네트워크의 특정 세그먼트 확인

* 가상 네트워크당 처리량 가져오기(각 VNet에 에이전트가 설치된 경우)

* 이전 특정 시점의 ExpressRoute 시스템 상태 확인

## <a name="regions"></a>지원되는 지역

다음 지역 중 하나에 호스트된 작업 영역을 사용하여 전 세계 어디서나 ExpressRoute 회로를 모니터링할 수 있습니다.

* 서유럽
* 미국 중서부
* 미국 동부 
* 동남아시아 
* 오스트레일리아 동남부

>[!NOTE]
>Azure Government 클라우드의 VNet에 연결된 ExpressRoute 회로 모니터링은 2018년 Q2부터 지원될 예정입니다.   
>

## <a name="workflow"></a>워크플로

모니터링 에이전트는 온-프레미스 및 Azure의 여러 서버에 설치됩니다. 에이전트는 서로 통신하지만 데이터를 전송하지 않으며 TCP 핸드셰이크 패킷을 전송합니다. 에이전트 간 통신을 위해 Azure는 트래픽에 적용될 수 있는 네트워크 토폴로지 및 경로를 매핑할 수 있습니다.

1. [지원되는 지역](#regions) 중 하나에 NPM 작업 영역을 만듭니다.
2. 소프트웨어 에이전트를 설치 및 구성합니다. 
    * 온-프레미스 서버와 Azure VM에서 모니터링 에이전트를 설치합니다.
    * 모니터링 에이전트가 통신할 수 있도록 모니터링 에이전트 서버의 설정을 구성합니다. (방화벽 포트 등을 엽니다.)
3. Azure VM에 설치된 모니터링 에이전트가 온-프레미스 모니터링 에이전트와 통신하도록 NSG(네트워크 보안 그룹) 규칙을 구성합니다.
4. 모니터링 설정: NPM에서 볼 수 있는 네트워크를 자동으로 검색하고 관리합니다.

이미 네트워크 성능 모니터를 사용하여 다른 개체 또는 서비스를 모니터링하며 지원되는 지역 중 하나에 작업 영역이 이미 있는 경우 1단계와 2단계를 건너뛰고 3단계에서 구성을 시작할 수 있습니다.

## <a name="configure"></a>1단계: 작업 영역 만들기

ExpressRoute 회로에 대한 VNets 링크가 있는 구독에 작업 영역을 만듭니다.

1. [Azure Portal](https://portal.azure.com)에서 Vnet이 ExpressRoute 회로에 연결된 구독을 선택합니다. 그런 후 **Marketplace**의 서비스 목록에서 ‘네트워크 성능 모니터’를 검색합니다. 반환된 결과에서 클릭하여 **네트워크 성능 모니터** 페이지를 엽니다.

   >[!NOTE]
   >새 작업 영역을 만들거나 기존 작업 영역을 사용할 수 있습니다.  기존 작업 영역을 사용하려면 작업 영역이 새 쿼리 언어로 마이그레이션되었는지 확인해야 합니다. [자세한 정보...](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-upgrade)
   >

   ![portal](.\media\how-to-npm\3.png)<br><br>
2. 주 **네트워크 성능 모니터** 페이지 아래쪽에서 **만들기**를 클릭하여 **네트워크 성능 모니터 - 새 솔루션 만들기** 페이지를 엽니다. **OMS 작업 영역 - 작업 영역 선택**을 클릭하여 작업 영역 페이지를 엽니다. **+ 새 작업 영역 만들기**를 클릭하여 작업 영역 페이지를 엽니다.
3. **OMS 작업 영역** 페이지에서 **새로 만들기**를 선택하고 다음 설정을 구성합니다.

  * OMS 작업 영역 - 작업 영역의 이름을 입력합니다.
  * 구독 - 여러 구독이 있는 경우 새 작업 영역에 연결할 구독을 선택합니다.
  * 리소스 그룹 - 리소스 그룹을 만들거나 기존 리소스 그룹을 사용합니다.
  * 위치 - [지원되는 지역](#regions)을 선택해야 합니다.
  * 가격 책정 계층 - '무료'를 선택합니다.
  
    >[!NOTE]
    >ExpressRoute 회로는 전 세계 어디에나 둘 수 있으며, 작업 영역과 동일한 지역에 있지 않아도 됩니다.
    >
  
    ![작업 영역](.\media\how-to-npm\4.png)<br><br>
4. **확인**을 클릭하여 설정 템플릿을 저장 및 배포합니다. 템플릿의 유효성이 확인되면 **만들기**를 클릭하여 작업 영역을 배포합니다.
5. 작업 영역이 배포된 후 만든 **NetworkMonitoring(name)** 리소스로 이동합니다. 설정이 유효한지 확인한 후 **솔루션에 추가 구성이 필요합니다.** 를 클릭합니다.

   ![추가 구성](.\media\how-to-npm\5.png)

## <a name="agents"></a>2단계: 에이전트 설치 및 구성

### <a name="download"></a>2.1: 에이전트 설치 파일 다운로드

1. 리소스에 대한 **네트워크 성능 모니터 구성** 페이지의 **일반 설정** 탭으로 이동합니다. **OMS 에이전트 설치** 섹션에서 서버 프로세서에 해당하는 에이전트를 클릭하고 설치 파일을 다운로드합니다.

 
2. 이제 **작업 영역 ID** 및 **기본 키**를 메모장에 복사합니다.
3. **TCP 프로토콜을 사용하여 모니터링할 OMS 에이전트 구성** 섹션에서 Powershell 스크립트를 다운로드합니다. PowerShell 스크립트는 TCP 트랜잭션에 대한 관련 방화벽 포트를 여는 데 도움이 됩니다.

  ![PowerShell 스크립트](.\media\how-to-npm\7.png)

### <a name="installagent"></a>2.2: 각 모니터링 서버에 모니터링 에이전트 설치(모니터링하려는 각 VNET)

중복성을 위해 ExpressRoute 연결의 각 끝(즉, 온-프레미스, Azure VNET)에 두 개 이상의 에이전트를 설치하는 것이 좋습니다. 다음 단계를 사용하여 에이전트를 설치합니다.
  
  >[!NOTE]
  >Windows Server(2008 SP1 이상)에 에이전트를 설치해야 합니다. Windows Desktop OS 및 Linux OS를 사용한 ExpressRoute 회로 모니터링은 지원되지 않습니다. 
  >
  >
  
  >[!NOTE]
  >SCOM([MMA](https://technet.microsoft.com/library/dn465154(v=sc.12).aspx) 포함)에서 푸시한 에이전트가 Azure에서 호스팅되는 경우 자체 위치를 일관되게 검색하지 못할 수 있습니다.  ExpressRoute를 모니터링할 때는 Azure VNET에서 이러한 에이전트를 사용하지 않는 것이 좋습니다.
  >
  >

1. **설치**를 실행하여 ExpressRoute 모니터링에 사용하려는 각 서버에 에이전트를 설치합니다. 모니터링에 사용하는 서버는 VM 또는 온-프레미스일 수 있으며 인터넷에 액세스할 수 있어야 합니다. 온-프레미스에 하나 이상의 에이전트를 설치하고, Azure에서 모니터링하려는 각 네트워크 세그먼트에 하나의 에이전트를 설치해야 합니다.
2. **Welcome** 페이지에서 **다음**을 클릭합니다.
3. **사용 조건** 페이지에서 라이선스를 읽고 **동의함**을 클릭합니다.
4. **대상 폴더** 페이지에서 기본 설치 폴더를 변경 또는 유지하고 **다음**을 클릭합니다.
5. **에이전트 설치 옵션** 페이지에서 Azure Log Analytics 또는 Operations Manager에 에이전트를 연결하도록 선택할 수 있습니다. 또는 에이전트를 나중에 구성하려는 경우 선택 항목을 비워 둘 수 있습니다. 선택한 후 **다음**을 클릭합니다.

  * **Azure Log Analytics**에 연결하려는 경우 이전 절차에서 메모장에 복사해 둔 **작업 영역 ID**와 **작업 영역 키**(기본 키)를 붙여넣습니다. 그런 후 **다음**을 클릭합니다.

    ![ID 및 키](.\media\how-to-npm\8.png)
  * **Operations Manager**에 연결할 경우 **관리 그룹 구성** 페이지에서 **관리 그룹 이름**, **관리 서버** 및 **관리 서버 포트**를 입력합니다. 그런 후 **다음**을 클릭합니다.

    ![Operations Manager](.\media\how-to-npm\9.png)
  * **에이전트 작업 계정** 페이지에서 **로컬 시스템** 계정 또는 **도메인 또는 로컬 컴퓨터 계정**을 선택합니다. 그런 후 **다음**을 클릭합니다.

    ![계좌](.\media\how-to-npm\10.png)
6. **설치 준비** 페이지에서 선택 항목을 검토한 다음 **설치**를 클릭합니다.
7. **구성 완료** 페이지에서 **마침**을 클릭합니다.
8. 완료되면 제어판에 Microsoft Monitoring Agent가 나타납니다. 여기에서 구성을 검토하고 에이전트가 Azure Log Analytics(OMS)에 연결되었는지 확인합니다. 연결되면 에이전트에 **Microsoft Monitoring Agent가 Microsoft Operations Management Suite 서비스에 성공적으로 연결되었습니다.** 와 같은 메시지가 표시됩니다.

9. 모니터링해야 하는 각 VNET에 대해 이 작업을 반복합니다.

### <a name="proxy"></a>2.3: 프록시 설정 구성(선택 사항)

웹 프록시를 사용하여 인터넷에 액세스하는 경우 다음 단계에 따라 Microsoft Monitoring Agent에 대한 프록시 설정을 구성합니다. 각 서버에 대해 다음 단계를 수행하세요. 많은 서버를 구성해야 하는 경우, 스크립트를 사용하여 보다 쉽게 이 프로세스를 자동화할 수 있습니다. 서버가 많은 경우 [스크립트를 사용하여 Microsoft Monitoring Agent에 대한 프록시 설정을 구성하려면](../log-analytics/log-analytics-windows-agent.md)을 참조하세요.

제어판을 사용하여 Microsoft Monitoring Agent에 대한 프록시 설정을 구성하려면

1. **제어판**을 엽니다.
2. **Microsoft Monitoring Agent**를 엽니다.
3. **프록시 설정** 탭을 클릭합니다.
4. **프록시 서버 사용**을 선택하고 필요한 경우 URL 및 포트 번호를 입력합니다. 프록시 서버에 인증이 필요한 경우 프록시 서버에 액세스 하려면 사용자 이름과 암호를 입력합니다.

  ![proxy](.\media\how-to-npm\11.png)

### <a name="verifyagent"></a>2.4: 에이전트 연결 확인

에이전트가 OMS와 통신하고 있는지 여부를 쉽게 확인할 수 있습니다.

1. 모니터링 에이전트가 있는 서버에서 **제어판**을 엽니다.
2. **Microsoft Monitoring Agent**를 엽니다.
3. **Azure Log Analytics** 탭을 클릭합니다.
4. **상태** 열에는 Log Analytics에 성공적으로 연결된 에이전트가 표시됩니다.

  ![status](.\media\how-to-npm\12.png)

### <a name="firewall"></a>2.5: 모니터링 에이전트 서버에서 방화벽 포트 열기

TCP 프로토콜을 사용하려면 모니터링 에이전트가 통신할 수 있도록 방화벽 포트를 열어야 합니다.

네트워크 성능 모니터에 필요한 레지스트리 키를 만드는 PowerShell 스크립트를 실행하고, 모니터링 에이전트가 서로 간에 TCP 연결을 만들 수 있도록 하는 Windows 방화벽 규칙을 만들 수 있습니다. 또한 이 스크립트로 생성된 레지스트리 키는 디버그 로그와 로그 파일의 패스를 로깅해야 하는지 여부를 지정하며 통신에 사용되는 에이전트 TCP 포트도 정의합니다. 이러한 키 값은 스크립트에서 자동으로 설정하므로 해당 키를 수동으로 변경하지 않아야 합니다.

기본적으로 포트 8084가 열립니다. 스크립트에 매개 변수 'portNumber'를 지정하여 사용자 지정 포트를 사용할 수 있습니다. 그러나 이렇게 하면 스크립트를 실행하는 모든 서버에 대해 동일한 포트를 지정해야 합니다.

>[!NOTE]
>'EnableRules' PowerShell 스크립트는 스크립트가 실행되는 서버에서만 Windows 방화벽 규칙을 구성합니다. 네트워크 방화벽이 있는 경우 네트워크 성능 모니터에서 사용하는 TCP 포트로 트래픽이 전송될 수 있도록 해야 합니다.
>
>

에이전트 서버에서 관리자 권한으로 PowerShell 창을 엽니다. [EnableRules](https://aka.ms/npmpowershellscript) PowerShell 스크립트를 실행합니다(이전에 다운로드한). 매개 변수는 사용하지 마세요.

  ![PowerShell_Script](.\media\how-to-npm\script.png)

## <a name="opennsg"></a>3단계: 네트워크 보안 그룹 규칙 구성

Azure에 있는 모니터링 에이전트 서버의 경우, 가상 트랜잭션을 위해 NPM에서 사용하는 포트의 TCP 트래픽을 허용하도록 NSG(네트워크 보안 그룹) 규칙을 구성해야 합니다. 기본 포트는 8084입니다. 이렇게 하면 Azure VM에 설치된 모니터링 에이전트가 온-프레미스 모니터링 에이전트와 통신할 수 있게 됩니다.

NSG에 대한 자세한 내용은 [네트워크 보안 그룹](../virtual-network/virtual-networks-create-nsg-arm-portal.md)을 참조하세요.

>[!NOTE]
>이 단계를 진행하기 전에 에이전트(온-프레미스 서버 에이전트 및 Azure 서버 에이전트)를 설치했는지와 PowerShell 스크립트를 실행했는지 확인합니다.
>
>


## <a name="setupmonitor"></a>4단계: ExpressRoute 모니터링용 NPM 구성

이전 섹션을 완료한 후 모니터링을 설정할 수 있습니다.

1. **모든 리소스** 페이지로 이동한 후 허용 목록에 포함된 NPM 작업 영역을 클릭하여 네트워크 성능 모니터 개요 타일로 이동합니다.

  ![npm 작업 영역](.\media\how-to-npm\npm.png)
2. **네트워크 성능 모니터** 개요 타일을 클릭하여 대시보드를 불러옵니다. 대시보드의 ExpressRoute 페이지에는 ExpressRoute가 '구성되지 않은 상태'로 표시됩니다. **기능 설치**를 클릭하여 네트워크 성능 모니터 구성 페이지를 엽니다.

  ![기능 설치](.\media\how-to-npm\npm2.png)
3. 구성 페이지에서 왼쪽 패널에 있는 'ExpressRoute 피어링' 탭으로 이동합니다. **지금 검색**을 클릭합니다.

  ![검색](.\media\how-to-npm\13.png)
4. 검색이 완료되면 고유한 회로 이름과 VNet 이름에 대한 규칙이 표시됩니다. 처음에는 이러한 규칙이 사용되지 않도록 설정됩니다. 규칙을 사용하도록 설정한 후 모니터링 에이전트와 임계값을 선택합니다.

  ![규칙](.\media\how-to-npm\14.png)
5. 규칙을 사용하도록 설정하고 모니터링할 값 및 에이전트를 선택하면 약 30~60분 후에 값이 채워지기 시작하고 **ExpressRoute 모니터링** 타일을 사용할 수 있게 됩니다. 모니터링 타일이 표시되면 ExpressRoute 회로 및 연결 리소스가 NPM에서 모니터링되는 것입니다.

  ![모니터링 타일](.\media\how-to-npm\15.png)

## <a name="explore"></a>5단계: 모니터링 타일 보기

### <a name="dashboard"></a>네트워크 성능 모니터 페이지

NPM 페이지에는 ExpressRoute 회로 및 피어링의 상태 개요를 보여 주는 ExpressRoute 페이지가 표시됩니다.

  ![대시보드](.\media\how-to-npm\dashboard.png)

### <a name="circuits"></a>회로 목록

모니터링되는 모든 ExpressRoute 회로의 목록을 보려면 **ExpressRoute 회로** 타일을 클릭합니다. 회로를 선택하고 해당 상태, 패킷 손실, 대역폭 사용률 및 대기 시간에 대한 추세 차트를 볼 수 있습니다. 차트는 대화형으로 작동합니다. 차트를 그리기 위한 사용자 지정 기간을 선택할 수 있습니다. 차트의 영역 위로 마우스를 끌어 확대하고 세분화된 데이터 요소를 확인할 수 있습니다.

  ![circuit_list](.\media\how-to-npm\circuits.png)

#### <a name="trend"></a>손실, 대기 시간 및 처리량 추세

대역폭, 대기 시간, 손실 차트는 대화형으로 작동합니다. 마우스 컨트롤을 사용하여 이러한 차트의 섹션을 확대할 수 있습니다. 또한 왼쪽 위에 있는 작업 단추 아래의 **날짜/시간**을 클릭하여 다른 간격으로 대역폭, 대기 시간 및 손실 데이터를 볼 수도 있습니다.

![추세](.\media\how-to-npm\16.png)

### <a name="peerings"></a>피어링 목록

대시보드의 **개인 피어링** 타일을 클릭하여 개인 피어링을 통한 가상 네트워크의 모든 연결 목록을 불러옵니다. 여기서 가상 네트워크 연결을 선택하고 해당 상태, 패킷 손실, 대역폭 사용률 및 대기 시간에 대한 추세 차트를 볼 수 있습니다.

  ![회로 목록](.\media\how-to-npm\peerings.png)

### <a name="topology"></a>회로 토폴로지

회로 토폴로지를 보려면 **토폴로지** 타일을 클릭합니다. 이렇게 하면 선택된 회로 또는 피어링의 토폴로지 보기로 이동됩니다. 토폴로지 다이어그램은 네트워크에 있는 각 세그먼트에 대한 대기 시간을 제공하며 각 계층 3 홉은 다이어그램의 노드로 표시됩니다. 홉을 클릭하면 홉에 대한 자세한 내용이 표시됩니다.
**필터** 아래의 슬라이더 막대를 이동하여 온-프레미스 홉을 포함하도록 표시 수준을 늘릴 수 있습니다. 슬라이더 막대를 왼쪽 또는 오른쪽으로 이동하면 토폴로지 그래프의 홉 수가 증가/감소합니다. 각 세그먼트 간의 대기 시간이 표시되므로 네트워크에서 대기 시간이 긴 세그먼트를 더 빠르게 구분할 수 있습니다.

![filters](.\media\how-to-npm\topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>회로의 자세한 토폴로지 보기

이 보기는 VNet 연결을 표시합니다.
![자세한 토폴로지](.\media\how-to-npm\17.png)
