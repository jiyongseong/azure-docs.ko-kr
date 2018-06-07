---
title: '빠른 시작: Azure Portal을 사용하여 공용 기본 부하 분산 장치 만들기 | Microsoft Docs'
description: 이 빠른 시작에서는 Azure Portal을 사용하여 공용 기본 부하 분산 장치를 만드는 방법을 보여줍니다.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: aa9d26ca-3d8a-4a99-83b7-c410dd20b9d0
ms.service: load-balancer
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 49fa4cf9b24c432b0956f930a1429e1cdf827f1b
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34304881"
---
# <a name="quickstart-create-a-public-basic-load-balancer-by-using-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 공용 기본 부하 분산 장치 만들기

부하를 분산하면 들어오는 요청이 여러 VM(가상 머신)에 분산되어 가용성 및 확장성이 향상됩니다. Azure Portal을 사용하여 가상 머신의 부하를 분산하는 부하 분산 장치를 만들 수 있습니다. 이 빠른 시작에서는 네트워크 리소스, 백 엔드 서버 및 기본 가격 책정 계층에서 부하 분산 장치를 만드는 방법을 보여줍니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

이 빠른 시작에서 모든 작업의 경우 [Azure Portal](http://portal.azure.com)에 로그인합니다.

## <a name="create-a-basic-load-balancer"></a>기본 부하 분산 장치 만들기

이 섹션에서는 포털을 사용하여 공용 기본 부하 분산 장치를 만듭니다. 포털을 사용하여 부하 공용 IP 및 분산 장치 리소스를 만들 때 공용 IP 주소는 자동으로 부하 분산 장치의 프런트 엔드로 구성됩니다. 프런트 엔드의 이름은 **LoadBalancerFrontend**입니다.

1. 포털의 왼쪽 상단에서 **리소스 만들기** > **네트워킹** > **Load Balancer**를 선택합니다.
2. **부하 분산 장치 만들기** 창에서 이러한 값을 입력합니다.
   - **myLoadBalancer** - 부하 분산 장치의 이름
   - **공용** - 부하 분산 장치의 유형 
   - **myPublicIP** - **기본**으로 설정된 **SKU** 및 **동적**으로 설정된 **할당**을 사용하여 만들어야 하는 공용 IP
   - **myResourceGroupLB** - 새 리소스 그룹의 이름
3. **만들기**를 선택합니다.
   
![부하 분산 장치 만들기](./media/load-balancer-get-started-internet-portal/1-load-balancer.png)


## <a name="create-back-end-servers"></a>백 엔드 서버 만들기

이 섹션에서는 가상 네트워크를 만들고, 기본 부하 분산 장치의 백 엔드 풀에 대한 두 개의 가상 머신을 만듭니다. 그런 다음, 부하 분산 장치 테스트를 돕기 위해 가상 머신에 IIS(인터넷 정보 서비스)를 설치합니다.

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기
1. 포털의 왼쪽 상단에서 **새로 만들기** > **네트워킹** > **가상 네트워크**를 선택합니다.
2. **가상 네트워크 만들기** 창에서 이러한 값을 입력한 다음, **만들기**를 선택합니다.
   - **myVnet** - 가상 네트워크의 이름
   - **myResourceGroupLB** - 기존 리소스 그룹의 이름
   - **myBackendSubnet** - 서브넷 이름

   ![가상 네트워크 만들기](./media/load-balancer-get-started-internet-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>가상 머신 만들기

1. 포털의 왼쪽 상단에서 **새로 만들기** > **Compute** > **Windows Server 2016 Datacenter**를 선택합니다. 
2. 가상 머신에 대해 이러한 값을 입력한 다음, **확인**을 선택합니다.
   - **myVM1** - 가상 머신의 이름        
   - **azureuser** - 관리자 사용자 이름    
   - **myResourceGroupLB** - 리소스 그룹 (**리소스 그룹** 아래에서 **기존 항목 사용**을 선택한 다음, **myResourceGroupLB**를 선택합니다.)   
3. 가상 머신의 크기에 대해 **DS1_V2**를 선택하고 **선택**을 클릭합니다.
4. VM 설정에 다음 값을 입력합니다.
   - **myAvailabilitySet** - 만드는 새 가용성 집합의 이름입니다.
   - **myVNet** - 가상 네트워크의 이름입니다. (선택되어 있는지 확인합니다.)
   - **myBackendSubnet** - 서브넷 이름입니다. (선택되어 있는지 확인합니다.)
   - **myVM1-ip** - 공용 IP 주소입니다.
   - **myNetworkSecurityGroup** - 만들어야 하는 새 네트워크 보안 그룹(NSG, 방화벽 유형)의 이름입니다.
5. **비활성화됨**을 선택하여 부팅 진단을 비활성화합니다.
6. **확인**을 선택하고, 요약 페이지에서 설정을 검토한 다음, **만들기**를 선택합니다.
7. 1-6단계를 사용하여 다음으로 **VM2**라는 두 번째 VM을 만듭니다.
   - **myAvailabilityset** - 가용성 집합입니다.
   - **myVnet** - 가상 네트워크입니다.
   - **myBackendSubnet** - 서브넷입니다.
   - **myNetworkSecurityGroup** - 네트워크 보안 그룹입니다. 

### <a name="create-nsg-rules"></a>NSG 규칙 만들기

이 섹션에서는 HTTP 및 RDP를 사용하는 인바운드 연결을 허용하는 NSG 규칙을 만듭니다.

1. 왼쪽 메뉴에서 **모든 리소스**를 선택합니다. 리소스 목록의 **myResourceGroupLB** 리소스 그룹에서 **myNetworkSecurityGroup**을 선택합니다.
2. **설정** 아래에서 **인바운드 보안 규칙**을 선택한 다음, **추가**를 선택합니다.
3. 포트 80을 사용하는 인바운드 HTTP 연결을 허용하도록 **myHTTPRule**이라는 인바운드 보안 규칙에 다음 값을 입력합니다. 그런 다음 **확인**을 선택합니다.
   - **서비스 태그** - **소스**
   - **인터넷** - **원본 서비스 태그**
   - **80** - **대상 포트 범위**
   - **TCP** - **프로토콜**
   - **허용** - **작업**
   - **100** - **우선 순위**로 입력합니다.
   - **myHTTPRule** - **이름**
   - **HTTP 허용** - **설명**
 
   ![NSG 규칙 만들기](./media/load-balancer-get-started-internet-portal/8-load-balancer-nsg-rules.png)
4. 2-3단계를 반복하여 포트 3389를 통해 인바운드 RDP 연결을 허용하는 **myRDPRule**이라는 또 다른 규칙을 만듭니다. 다음 값을 사용합니다.
   - **서비스 태그** - **소스**
   - **인터넷** - **원본 서비스 태그**
   - **3389** - **대상 포트 범위**
   - **TCP** - **프로토콜**
   - **허용** - **작업**
   - **200** - **우선 순위**로 입력합니다.
   - **myRDPRule** - **이름**
   - **RDP 허용** - **설명**

   

### <a name="install-iis"></a>IIS 설치

1. 왼쪽 메뉴에서 **모든 리소스**를 선택합니다. 리소스 목록의 **myResourceGroupLB** 리소스 그룹에서 **myVM1**을 선택합니다.
2. **개요** 페이지에서 **연결**을 선택하여 VM에 RDP로 연결합니다.
3. 사용자 이름 **azureuser**, 암호 **Azure123456!** 를 사용하여 VM에 로그인합니다.
4. 서버 바탕 화면에서 **Windows 관리 도구** > **서버 관리자**로 이동합니다.
5. 서버 관리자에서 **관리**를 선택한 다음, **역할 및 기능 추가**를 선택합니다.
   ![서버 관리자 역할 추가](./media/load-balancer-get-started-internet-portal/servermanager.png)
6. 역할 및 기능 추가 마법사에서 다음 값을 사용합니다.
   - **설치 유형 선택** 페이지에서 **역할 기반 또는 기능 기반 설치**를 선택합니다.
   - **대상 서버 선택** 페이지에서 **myVM1**을 선택합니다.
   - **서버 역할 선택** 페이지에서 **웹 서버(IIS)** 를 선택합니다.
   - 지침에 따라 마법사의 나머지 과정을 완료합니다. 
7. 가상 머신 **myVM2**에 대해 1-6 단계를 반복합니다.

## <a name="create-resources-for-the-basic-load-balancer"></a>기본 부하 분산 장치에 대한 리소스 만들기

이 섹션에서는 백 엔드 주소 풀 및 상태 프로브에 대한 부하 분산 장치 설정을 구성합니다. 또한 부하 분산 장치 및 NAT 규칙을 지정합니다.


### <a name="create-a-back-end-address-pool"></a>백 엔드 주소 풀 만들기

VM으로 트래픽을 분산하기 위해 백 엔드 주소 풀에 부하 분산 장치에 연결된 가상 NIC의 주소가 포함됩니다. **VM1** 및 **VM2**를 포함하도록 백 엔드 주소 풀 **myBackendPool**을 만듭니다.

1. 왼쪽 메뉴에서 **모든 리소스**를 선택한 다음, 리소스 목록에서 **myLoadBalancer**를 선택합니다.
2. **설정**에서 **백 엔드 풀**을 선택한 다음, **추가**를 선택합니다.
3. **백 엔드 풀 추가** 페이지에서 다음을 수행한 다음, **확인**을 선택합니다.
   - **이름**에 **myBackEndPool**을 입력합니다.
   - **연결 대상**으로 드롭다운 메뉴에서 **가용성 집합**을 선택합니다.
   - **가용성 집합**에서 **myAvailabilitySet**를 선택합니다.
   - **대상 네트워크 IP 구성 추가**를 선택하여 앞에서 만든 각 가상 머신(**myVM1** 및 **myVM2**)을 백 엔드 풀에 추가합니다.

   ![백 엔드 주소 풀에 추가](./media/load-balancer-get-started-internet-portal/3-load-balancer-backend-02.png)

3. 부하 분산 장치의 백 엔드 풀 설정에 **VM1** 및 **VM2**가 모두 표시되는지 확인합니다.

### <a name="create-a-health-probe"></a>상태 프로브 만들기

기본 부하 분산 장치가 앱의 상태를 모니터링하도록 하려면 상태 프로브를 사용합니다. 상태 프로브는 상태 검사에 따라 부하 분산 장치 순환에서 VM을 동적으로 추가하거나 제거합니다. VM 상태를 모니터링할 **myHealthProbe**라는 상태 프로브를 만듭니다.

1. 왼쪽 메뉴에서 **모든 리소스**를 선택한 다음, 리소스 목록에서 **myLoadBalancer**를 선택합니다.
2. **설정**에서 **상태 프로브**를 선택한 다음, **추가**를 선택합니다.
3. 이러한 값을 사용한 다음, **확인**을 선택합니다.
   - **myHealthProbe** - 상태 프로브의 이름
   - **HTTP** - 프로토콜 유형
   - **80** - 포트 번호
   - **15** - **간격**, 프로브 시도 간의 초 수
   - **2** - **비정상 임계값**, VM을 비정상 상태로 간주하기 전에 발생해야 하는 연속 프로브 오류 횟수

   ![프로브 추가](./media/load-balancer-get-started-internet-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>부하 분산 장치 규칙 만들기

부하 분산 장치 규칙을 사용하여 VM으로 트래픽이 분산되는 방법을 정의합니다. 들어오는 트래픽에 대한 프런트 엔드 IP 구성 및 트래픽을 수신할 백 엔드 IP 풀과 필요한 원본 및 대상 포트를 함께 정의합니다. 

프런트 엔드 **LoadBalancerFrontEnd**에서 포트 80에 수신 대기를 위해 **myLoadBalancerRuleWeb**이라는 부하 분산 장치 규칙을 만듭니다. 규칙은 포트 80을 사용하여 백 엔드 주소 풀 **myBackEndPool**에 부하 분산된 네트워크 트래픽을 전송하기 위한 것입니다. 

1. 왼쪽 메뉴에서 **모든 리소스**를 선택한 다음, 리소스 목록에서 **myLoadBalancer**를 선택합니다.
2. **설정** 아래에서 **부하 분산 규칙**을 선택한 다음, **추가**를 선택합니다.
3. 이러한 값을 사용한 다음, **확인**을 선택합니다.
   - **myHTTPRule** - 부하 분산 규칙의 이름
   - **TCP** - 프로토콜 유형
   - **80** - 포트 번호
   - **80** - 백 엔드 포트
   - **myBackendPool** - 백 엔드 풀의 이름
   - **myHealthProbe** - 상태 프로브의 이름
    
   ![부하 분산 장치 규칙 추가](./media/load-balancer-get-started-internet-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>부하 분산 장치 테스트
1. **개요** 화면에서 부하 분산 장치의 공용 IP 주소를 찾습니다. **모든 리소스**를 선택한 다음, **myPublicIP**를 선택합니다.

2. 공용 IP 주소를 복사하여 브라우저의 주소 표시줄에 붙여넣습니다. IIS 웹 서버의 기본 페이지가 브라우저에 표시됩니다.

   ![IIS 웹 서버](./media/load-balancer-get-started-internet-portal/9-load-balancer-test.png)

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않으면 리소스 그룹, 부하 분산 장치 및 모든 관련 리소스를 삭제할 수 있습니다. 부하 분산 장치가 포함된 리소스 그룹을 선택하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 리소스 그룹, 네트워크 리소스 및 백 엔드 서버를 만들었습니다. 그 다음, 이러한 리소스를 사용하여 기본 Load Balancer를 만들었습니다. Azure Load Balancer에 대해 자세히 알아보려면 Azure Load Balancer에 대한 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [Azure Load Balancer 자습서](tutorial-load-balancer-basic-internal-portal.md)
