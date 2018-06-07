---
title: '자습서: 가용성 영역 간 VM Load Balancer - Azure Portal | Microsoft Docs'
description: 이 자습서는 Azure Portal을 사용하여 가용성 영역 간 VM 부하 분산을 위한 영역 중복 프런트 엔드가 있는 표준 Load Balancer를 만드는 방법을 설명합니다.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines across availability zones in a region, so that the customers can still access the web service if a datacenter is unavailable.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 5ec1cc42a0c932e47c08493fa632495426abc4c7
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34304463"
---
# <a name="tutorial-load-balance-vms-across-availability-zones-with-a-standard-load-balancer-using-the-azure-portal"></a>자습서: Azure Portal을 사용하여 표준 Load Balancer를 통한 가용성 영역 간 VM 부하 분산

부하 분산은 들어오는 요청을 여러 가상 머신에 분산하여 높은 수준의 가용성을 제공합니다. 이 자습서는 가용성 영역 간 VM의 부하를 분산하는 공용 Load Balancer 표준을 만드는 단계를 설명합니다. 이를 통해 가능성이 적은 실패 또는 전체 데이터 센터의 손실로부터 앱 및 데이터를 보호합니다. 영역 중복에서 하나 이상의 가용성 영역이 실패할 수 있고 지역에서 한 영역이 정상으로 유지되는 한 데이터 경로는 유효합니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 표준 Load Balancer 만들기
> * 들어오는 트래픽 규칙을 정의하는 네트워크 보안 그룹 만들기
> * 여러 영역 간 영역 중복 VM 만들기 및 부하 분산 장치에 연결
> * 부하 분산 장치 상태 프로브 만들기
> * 부하 분산 장치 트래픽 규칙 만들기
> * 기본 IIS 사이트 만들기
> * 부하 분산 장치의 실제 동작 보기

Standard Load Balancer에서 가용성 영역 사용에 대한 자세한 내용은 [Standard Load Balancer 및 가용석 영역](load-balancer-standard-availability-zones.md)을 참조하세요.

원하는 경우 [Azure CLI](load-balancer-standard-public-zone-redundant-cli.md)를 사용하여 이 자습서를 완료할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 

## <a name="sign-in-to-azure"></a>Azure에 로그인

[http://portal.azure.com](http://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="create-a-standard-load-balancer"></a>표준 Load Balancer 만들기

표준 부하 분산 장치는 표준 공용 IP 주소만 지원합니다. 부하 분산 장치를 만드는 동안 새 공용 IP를 만들면 자동으로 Standard SKU 버전으로 구성되며 영역 중복이 됩니다.

1. 화면의 왼쪽 상단에서 **리소스 만들기** > **네트워킹** > **부하 분산 장치**를 클릭합니다.
2. **부하 분산 장치 만들기** 페이지에서 부하 분산 장치에 다음 값을 입력합니다.
    - *myLoadBalancer* - 부하 분산 장치의 이름입니다.
    - **공용** - 부하 분산 장치의 유형입니다.
     - *myPublicIP* - 만드는 새 공용 IP 주소입니다. 이를 위해 **공용 IP 주소 선택**, **새로 만들기**를 차례로 클릭합니다. 이름에 *myPublicIP*를 입력합니다. SKU는 기본적으로 Standard이며 **가용성 영역**에 **영역 중복**을 선택합니다. 
    - *myResourceGroupLBAZ* - 새로 만드는 리소스 그룹의 이름입니다.
    - **westeurope** - 위치로 입력합니다.
3. **만들기**를 클릭하여 부하 분산 장치를 만듭니다.
   
    ![부하 분산 장치 만들기](./media/load-balancer-standard-public-availability-zones-portal/1a-load-balancer.png)


## <a name="create-backend-servers"></a>백 엔드 서버 만들기

이 섹션에서는 지역에 대해 다른 영역에 가상 네트워크, 가상 머신을 만든 다음, 영역 중복 부하 분산 장치를 테스트하기 위한 IIS를 가상 머신에 설치합니다. 따라서 한 영역이 실패하면 같은 영역의 VM에 대한 상태 검색이 실패하고 트래픽은 다른 영역의 VM에서 계속하여 처리됩니다.

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기
백 엔드 서버를 배포하기 위한 가상 네트워크를 만듭니다.

1. 화면의 왼쪽 상단에서 **리소스 만들기** > **네트워킹** > **가상 네트워크**를 클릭하고 가상 네트워크에 대해 다음 값을 입력합니다.
    - *myVNet* - 가상 네트워크의 이름입니다.
    - *myResourceGroupLBAZ* - 기존 리소스 그룹의 이름입니다.
    - *myBackendSubnet* - 서브넷 이름입니다.
2. **만들기**를 클릭하여 가상 네트워크를 만듭니다.

    ![가상 네트워크 만들기](./media/load-balancer-standard-public-availability-zones-portal/2-load-balancer-virtual-network.png)

## <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기

가상 네트워크에 대한 인바운드 연결을 정의하는 네트워크 보안 그룹을 만듭니다. 네트워크 보안 그룹을 만들어 가상 네트워크에 대한 인바운드 연결을 정의합니다.

1. 화면 왼쪽에서 **리소스 만들기**를 클릭하고 검색 상자에 *네트워크 보안 그룹*을 입력한 다음, 네트워크 보안 그룹 페이지에서 **만들기**를 클릭합니다.
2. 네트워크 보안 그룹 만들기 페이지에서 다음 값을 입력합니다.
    - *myNetworkSecurityGroup*  - 네트워크 보안 그룹의 이름입니다.
    - *myResourceGroupLBAZ* - 기존 리소스 그룹의 이름입니다.
   
![가상 네트워크 만들기](./media/load-balancer-standard-public-availability-zones-portal/create-nsg.png)

### <a name="create-network-security-group-rules"></a>네트워크 보안 그룹 규칙 만들기

이 섹션에서는 Azure Portal을 사용하여 HTTP 및 RDP를 사용한 인바운드 연결을 허용하는 네트워크 보안 그룹 규칙을 만듭니다.

1. Azure Portal에서 **모든 리소스**를 클릭한 다음, **myResourceGroupLBAZ** 리소스 그룹에 있는 **myNetworkSecurityGroup**을 클릭합니다.
2. **설정**에서 **인바운드 보안 규칙**을 클릭한 다음, **추가**를 클릭합니다.
3. 포트 80을 사용하는 인바운드 HTTP 연결을 허용하도록 *myHTTPRule*이라고 하는 인바운드 보안 규칙에 다음 값을 입력합니다.
    - *서비스 태그* - **소스**로 입력합니다.
    - *인터넷* - **원본 서비스 태그**로 입력합니다.
    - *80* - **대상 포트 범위**로 입력합니다.
    - *TCP* - **프로토콜**로 입력합니다.
    - *허용* - **작업**에 대해 선택합니다.
    - *100* - **우선 순위**로 입력합니다.
    - *myHTTPRule* - 부하 분산 규칙의 이름
    - *HTTP 허용* - 부하 분산 장치 규칙의 설명
4. **확인**을 클릭합니다.
 
 ![가상 네트워크 만들기](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)
5. 다음 값으로 2-4단계를 반복하여 포트 3389를 사용하는 인바운드 RDP 연결을 허용하는 *myRDPRule*이라는 또 다른 규칙을 만듭니다.
    - *서비스 태그* - **소스**로 입력합니다.
    - *인터넷* - **원본 서비스 태그**로 입력합니다.
    - *3389* - **대상 포트 범위**로 입력합니다.
    - *TCP* - **프로토콜**로 입력합니다.
    - *허용* - **작업**에 대해 선택합니다.
    - *200* - **우선 순위**로 입력합니다.
    - *myRDPRule* - 이름으로 입력합니다.
    - *RDP 허용* - 설명으로 입력합니다.

### <a name="create-virtual-machines"></a>가상 머신 만들기

부하 분산 장치에 대해 백 엔드 서버용으로 사용할 수 있는 영역에 대해 서로 다른 영역(영역 1, 영역 2 및 영역 3)에 가상 머신을 만듭니다.

1. 화면의 왼쪽 상단에서 **리소스 만들기** > **계산** > **Windows Server 2016 Datacenter**를 클릭하고 가상 머신에 대해 다음 값을 입력합니다.
    - *myVM1* - 가상 머신의 이름        
    - *azureuser* - 관리자 사용자 이름    
    - *myResourceGroupLBAZ* - **리소스 그룹**에 대해 **기존 항목 사용**을 선택한 다음, *myResourceGroupLBAZ*를 선택합니다.
2. **확인**을 클릭합니다.
3. 가상 머신의 크기에 대해 **DS1_V2**를 선택하고 **선택**을 클릭합니다.
4. VM 설정에 다음 값을 입력합니다.
    - *영역 1* - VM을 배치하는 영역입니다.
    -  *myVNet* - 가상 네트워크로 선택합니다.
    - *myBackendSubnet* - 서브넷으로 선택합니다.
    - *myNetworkSecurityGroup* - 네트워크 보안 그룹(방화벽)의 이름입니다.
5. **사용 안 함**을 클릭하여 부팅 진단을 사용하지 않도록 설정합니다.
6. **확인**을 클릭하고 요약 페이지에서 설정을 검토한 다음, **만들기**를 클릭합니다.
  
 ![가상 머신 만들기](./media/load-balancer-standard-public-availability-zones-portal/create-vm-standard-ip.png)

7. 1-6단계를 사용하여 영역 2에서 이름이 *VM2*인 두 번째 VM, 영역 3에서 세 번째 VM, 가상 네트워크 *myVnet*, 서브넷 *myBackendSubnet*, 네트워크 보안 그룹 **myNetworkSecurityGroup*을 만듭니다.

### <a name="install-iis-on-vms"></a>VM에 IIS 설치

1. 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음, 리소스 목록에서 *myResourceGroupLBAZ* 리소스 그룹에 있는 **myVM1**을 클릭합니다.
2. **개요** 페이지에서 **연결**을 클릭하여 VM에 RDP로 연결합니다.
3. 사용자 이름으로 *azureuser*를 사용하여 VM에 로그인합니다.
4. 서버 바탕 화면에서 **Windows 관리 도구**>**Windows PowerShell**로 이동합니다.
5. PowerShell 창에서 다음 명령을 실행하여 IIS 서버를 설치하고, 기본 iisstart.htm 파일을 제거한 다음, VM 이름을 표시하는 새 iisstart.htm 파일을 추가합니다.
   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from" + $env:computername)
   ```
6. *myVM1*이 포함된 RDP 세션을 닫습니다.
7. 1~6단계를 반복하여 *myVM2* 및 *myVM3*에 IIS 및 업데이트된 iisstart.htm 파일을 설치합니다.

## <a name="create-load-balancer-resources"></a>부하 분산 장치 리소스 만들기

이 섹션에서는 백 엔드 주소 풀 및 상태 프로브에 대한 부하 분산 장치 설정을 구성하고, 부하 분산 장치 및 NAT 규칙을 지정합니다.


### <a name="create-a-backend-address-pool"></a>백 엔드 주소 풀 만들기

VM으로 트래픽을 분산하기 위해 백 엔드 주소 풀에 부하 분산 장치에 연결된 가상(NIC)의 주소가 포함됩니다. *VM1*, *VM2* 및 *VM3*을 포함하도록 백 엔드 주소 풀 *myBackendPool*을 만듭니다.

1. 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음, 리소스 목록에서 **myLoadBalancer**를 클릭합니다.
2. **설정**에서 **백 엔드 풀**을 클릭한 다음, **추가**를 클릭합니다.
3. **백 엔드 풀 추가** 페이지에서 다음을 수행합니다.
    - 이름에서, 백 엔드 풀의 이름으로 *myBackEndPool*을 입력합니다.
    - **가상 네트워크**에 대해 드롭다운 메뉴에서 **myVNet**을 클릭합니다.
    - **가상 컴퓨터**에 대해 드롭다운 메뉴에서 **myVM1**을 클릭합니다.
    - **IP 주소**에 대해 드롭다운 메뉴에서 myVM1 IP 주소를 클릭합니다.
4. **새 백엔드 리소스 추가**를 클릭하여 각 가상 머신(*myVM2* 및 *myVM3*)을 부하 분산 장치의 백엔드 풀에 추가합니다.
5. **추가**를 클릭합니다.

    ![백 엔드 주소 풀에 추가 ](./media/load-balancer-standard-public-availability-zones-portal/add-backend-pool.png)

3. 부하 분산 장치 백 엔드 풀 설정에 **myVM1**, **myVM2** 및 **myVM3** 등의 세 VM이 모두 표시되는지 확인합니다.

### <a name="create-a-health-probe"></a>상태 프로브 만들기

부하 분산 장치가 앱의 상태를 모니터링하도록 하려면 상태 프로브를 사용합니다. 상태 프로브는 상태 검사에 따라 부하 분산 장치 순환에서 VM을 동적으로 추가하거나 제거합니다. VM 상태를 모니터링할 상태 프로브 *myHealthProbe*를 만듭니다.

1. 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음, 리소스 목록에서 **myLoadBalancer**를 클릭합니다.
2. **설정**에서 **상태 프로브**를 클릭한 다음, **추가**를 클릭합니다.
3. 다음 값을 사용하여 상태 프로브를 만듭니다.
    - *myHealthProbe* - 상태 프로브의 이름으로 입력합니다.
    - **HTTP** - 프로토콜 유형으로 입력합니다.
    - *80* - 포트 번호로 입력합니다.
    - *15* - 프로브 시도 **간격**(초)으로 입력합니다.
    - *2* - **비정상 임계값** 또는 연속 프로브 오류 횟수가 이 숫자에 도달하면 VM을 비정상 상태로 간주합니다.
4. **확인**을 클릭합니다.

   ![프로브 추가](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>부하 분산 장치 규칙 만들기

부하 분산 장치 규칙은 VM으로 트래픽이 분산되는 방법을 정의하는 데 사용됩니다. 들어오는 트래픽에 대한 프런트 엔드 IP 구성 및 트래픽을 수신할 백 엔드 IP 풀과 필요한 원본 및 대상 포트를 함께 정의합니다. 프런트 엔드 *FrontendLoadBalancer*의 포트 80에서 수신 대기하고 역시 포트 80을 사용하여 백 엔드 주소 풀 *myBackEndPool*에 부하 분산된 네트워크 트래픽을 보내는 *myLoadBalancerRuleWeb*이라는 부하 분산 장치 규칙을 만듭니다. 

1. 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음, 리소스 목록에서 **myLoadBalancer**를 클릭합니다.
2. **설정**에서 **부하 분산 규칙**을 클릭한 다음, **추가**를 클릭합니다.
3. 다음 값을 사용하여 부하 분산 규칙을 구성합니다.
    - *myHTTPRule* - 부하 분산 규칙의 이름으로 입력합니다.
    - **TCP** - 프로토콜 유형으로 입력합니다.
    - *80* - 포트 번호로 입력합니다.
    - *80* - 백 엔드 포트로 입력합니다.
    - *myBackendPool* - 백 엔드 풀의 이름으로 입력합니다.
    - *myHealthProbe* - 상태 프로브의 이름으로 입력합니다.
4. **확인**을 클릭합니다.
    
    ![부하 분산 규칙 추가](./media/load-balancer-standard-public-availability-zones-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>부하 분산 장치 테스트
1. **개요** 화면에서 부하 분산 장치의 공용 IP 주소를 찾습니다. **모든 리소스**를 클릭한 다음, **myPublicIP**를 클릭합니다.

2. 공용 IP 주소를 복사하여 브라우저의 주소 표시줄에 붙여넣습니다. IIS 웹 서버의 기본 페이지가 브라우저에 표시됩니다.

      ![IIS 웹 서버](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

영역에 분산되는 VM에서 분산 장치가 트래픽을 분산하는 것을 확인하기 위해 웹 브라우저를 강제로 새로 고칠 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않으면 리소스 그룹, 부하 분산 장치 및 모든 관련 리소스를 삭제합니다. 이렇게 하려면 부하 분산 장치가 포함된 리소스 그룹을 선택하고 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

[Standard Load Balancer](load-balancer-standard-overview.md)에 대해 자세히 알아보세요.
