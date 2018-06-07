---
title: 네트워크 트래픽 라우팅 - 자습서 - Azure Portal | Microsoft Docs
description: 이 자습서에서는 Azure Portal을 사용하여 경로 테이블이 포함된 네트워크 트래픽을 라우팅하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 7254e9336fca14daee2021d5bde4c5538509fe35
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>자습서: Azure Portal을 사용하여 경로 테이블이 포함된 네트워크 트래픽 라우팅

기본적으로 Azure에서는 가상 네트워크 내의 모든 서브넷 간에 트래픽을 자동으로 라우팅합니다. 고유의 라우팅을 만들어 Azure의 기본 라우팅을 재정의할 수 있습니다. 사용자 지정 경로를 만드는 기능은 예를 들어 NVA(네트워크 가상 어플라이언스)를 통해 서브넷 간 트래픽을 라우팅하려는 경우에 유용합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 경로 테이블 만들기
> * 경로 만들기
> * 여러 서브넷이 있는 가상 네트워크 만들기
> * 서브넷에 경로 테이블 연결
> * 트래픽을 라우팅하는 NVA 만들기
> * 다른 서브넷에 VM(가상 머신) 배포
> * NVA를 통해 한 서브넷에서 다른 서브넷으로 트래픽 라우팅

원하는 경우 [Azure CLI](tutorial-create-route-table-cli.md) 또는 [Azure PowerShell](tutorial-create-route-table-powershell.md)을 사용하여 이 자습서를 완료할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="log-in-to-azure"></a>Azure에 로그인 

Azure Portal ( http://portal.azure.com ) 에 로그인합니다.

## <a name="create-a-route-table"></a>경로 테이블 만들기

1. Azure Portal의 왼쪽 위에서 **+ 리소스 만들기**를 선택합니다.
2. **네트워킹**을 선택한 다음, **경로 테이블**을 선택합니다.
3. 다음 정보를 입력하거나 선택하고, 나머지 설정에 대한 기본값을 그대로 적용한 다음, **만들기**를 선택합니다.

    |설정|값|
    |---|---|
    |Name|myRouteTablePublic|
    |구독| 사용 중인 구독을 선택합니다.|
    |리소스 그룹 | **새로 만들기**를 선택하고 *myResourceGroup*을 입력합니다.|
    |위치|미국 동부|
 
    ![경로 테이블 만들기](./media/tutorial-create-route-table-portal/create-route-table.png) 

## <a name="create-a-route"></a>경로 만들기

1. 포털 맨 위에 있는 *리소스, 서비스 및 문서 검색* 상자에 *myRouteTablePublic*을 입력하기 시작합니다. 검색 결과에 **myRouteTablePublic**이 표시되면 선택합니다.
2. **설정**에서 **경로**를 선택한 다음, 다음 그림에 표시된 것처럼 **+ 추가**를 선택합니다.

    ![경로 추가](./media/tutorial-create-route-table-portal/add-route.png) 
 
3. **경로 추가**에서 다음 정보를 입력하거나 선택하고, 나머지 설정에 대한 기본값을 그대로 적용한 다음, **만들기**를 선택합니다.

    |설정|값|
    |---|---|
    |경로 이름|ToPrivateSubnet|
    |주소 접두사| 10.0.1.0/24|
    |다음 홉 유형 | **가상 어플라이언스**를 선택합니다.|
    |다음 홉 주소| 10.0.2.4|

## <a name="associate-a-route-table-to-a-subnet"></a>서브넷에 경로 테이블 연결

서브넷에 경로 테이블을 연결하려면 먼저 가상 네트워크 및 서브넷을 만들어야 합니다. 그 후 서브넷에 경로 테이블을 연결할 수 있습니다.

1. Azure Portal의 왼쪽 위에서 **+ 리소스 만들기**를 선택합니다.
2. **네트워킹**을 선택한 다음 **가상 네트워크**를 선택합니다.
3. **가상 네트워크 만들기**에서 다음 정보를 입력하거나 선택하고, 나머지 설정에 대한 기본값을 그대로 적용한 다음, **만들기**를 선택합니다.

    |설정|값|
    |---|---|
    |Name|myVirtualNetwork|
    |주소 공간| 10.0.0.0/16|
    |구독 | 사용 중인 구독을 선택합니다.|
    |리소스 그룹|**기존 항목 사용**을 선택한 다음, **myResourceGroup**을 선택합니다.|
    |위치|*미국 동부*를 선택합니다.|
    |서브넷 이름|공용|
    |주소 범위|10.0.0.0/24|
    
4. 포털 맨 위에 있는 **리소스, 서비스 및 문서 검색** 상자에 *myVirtualNetwork*를 입력하기 시작합니다. 검색 결과에 **myVirtualNetwork**가 표시되면 선택합니다.
5. 다음 그림과 같이 **설정**에서 **서브넷**을 선택한 다음, **+ 서브넷**을 선택합니다.

    ![서브넷 추가](./media/tutorial-create-route-table-portal/add-subnet.png) 

6. 다음 정보를 선택하거나 입력한 다음, **확인**을 선택합니다.

    |설정|값|
    |---|---|
    |Name|개인|
    |주소 공간| 10.0.1.0/24|

7. 다음 정보를 입력하여 5단계와 6단계를 다시 완료합니다.

    |설정|값|
    |---|---|
    |Name|DMZ|
    |주소 공간| 10.0.2.0/24|

8. 이전 단계를 완료하면 **myVirtualNetwork - 서브넷** 상자가 표시됩니다. **설정**에서 **서브넷**을 선택한 다음, **공용**을 선택합니다.
9. 다음 그림에 나와 있는 것처럼 **경로 테이블**을 선택하고 **MyRouteTablePublic**을 선택한 다음, **저장**을 선택합니다.

    ![경로 테이블 연결](./media/tutorial-create-route-table-portal/associate-route-table.png) 

## <a name="create-an-nva"></a>NVA 만들기

NVA는 라우팅, 방화벽 또는 WAN 최적화와 같은 네트워크 기능을 수행하는 VM입니다.

1. Azure Portal의 왼쪽 위에서 **+ 리소스 만들기**를 선택합니다.
2. **Compute**를 선택한 후 **Windows Server 2016 Datacenter**를 선택합니다. 다른 운영 체제를 선택할 수 있지만 나머지 단계에서는 **Windows Server 2016 Datacenter**를 선택했다고 가정합니다. 
3. **기본**에 다음 정보를 선택하거나 입력한 다음, **확인**을 선택합니다.

    |설정|값|
    |---|---|
    |Name|myVmNva|
    |사용자 이름|선택한 사용자 이름을 입력합니다.|
    |암호|선택한 암호를 입력합니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다.|
    |리소스 그룹| **기존 항목 사용**을 선택한 다음, *myResourceGroup*을 선택합니다.|
    |위치|**미국 동부**를 선택합니다.|
4. **크기 선택**에서 VM 크기를 선택합니다.
5. **설정**에 다음 정보를 선택하거나 입력한 다음, **확인**을 선택합니다.

    |설정|값|
    |---|---|
    |가상 네트워크|myVirtualNetwork - 선택되지 않은 경우에는 **가상 네트워크**를 선택한 다음, **가상 네트워크 선택**에서 **myVirtualNetwork**를 선택합니다.|
    |서브넷|**서브넷**을 선택한 다음, **서브넷 선택**에서 **DMZ**를 선택합니다. |
    |공용 IP 주소| **공용 IP 주소**를 선택하고 **공용 IP 주소 선택**에서 **없음**을 선택합니다. 인터넷에서 연결되어 있지 않아 이 VM에 할당된 공용 IP 주소가 없습니다.
6. **요약**의 **만들기** 아래에서 **만들기**를 선택하여 VM 배포를 시작합니다.

    VM을 만드는 데 몇 분이 걸립니다. Azure가 VM 만들기를 완료하고 VM에 대한 정보가 포함된 상자를 열 때까지 다음 단계로 이동하지 마세요.

7. VM이 생성된 후 이에 대해 열리는 상자에서 다음 그림과 같이 **설정**에 있는 **네트워킹**을 선택한 다음, **myvmnva158**(VM에 대해 Azure가 만든 네트워크 인터페이스에는 **myvmnva** 뒤에 여러 다른 숫자가 옴)을 선택합니다.

    ![VM 네트워킹](./media/tutorial-create-route-table-portal/virtual-machine-networking.png) 

8. 네트워크 인터페이스가 자체 IP 주소를 향하지 않고 수신된 네트워크 트래픽을 전달할 수 있으려면 네트워크 인터페이스에 대해 IP 전달을 사용 가능으로 설정해야 합니다. 다음 그림에 표시된 것처럼 **설정**에서 **IP 구성을**을 선택하고 **IP 전달**에 **사용**을 선택한 다음, **저장**을 선택합니다.

    ![IP 전달을 사용하도록 설정](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png) 

## <a name="create-virtual-machines"></a>가상 머신 만들기

이후 단계에서 *공용* 서브넷의 해당 트래픽이 NVA를 통해 *개인* 서브넷에 라우팅되는지 유효성을 검사할 수 있도록 가상 네트워크에 두 개의 VM을 만듭니다. [NVA 만들기](#create-a-network-virtual-appliance)의 1-6 단계를 완료합니다. 다음과 같은 변경 사항을 제외하고 3-5단계에서 동일한 설정을 사용합니다.

|가상 머신 이름      |서브넷      | 공용 IP 주소     |
|--------- | -----------|---------              |
| myVmPublic  | 공용     | 포털 기본값 수락 |
| myVmPrivate | 개인    | 포털 기본값 수락 |

Azure가 *myVmPublic* VM을 만드는 동안 *myVmPrivate* VM을 만들 수 있습니다. Azure가 두 VM 만들기를 완료할 때까지 다음 단계를 진행하지 마세요.

## <a name="route-traffic-through-an-nva"></a>WAF를 통한 트래픽 라우팅

1. 포털 맨 위에 있는 *검색* 상자에 *myVmPrivate*을 입력하기 시작합니다. 검색 결과에 **myVmPrivate** VM이 나타나면 선택합니다.
2. 다음 그림과 같이 **연결**을 선택하여 *myVmPrivate* VM에 대한 원격 데스크톱 연결을 만듭니다.

    ![VM에 연결 ](./media/tutorial-create-route-table-portal/connect-to-virtual-machine.png)  

3. VM에 연결하려면 다운로드한 RDP 파일을 엽니다. 메시지가 표시되면 **연결**을 선택합니다.
4. VM을 만들 때 지정한 사용자 이름과 암호를 입력(VM을 만들 때 입력한 자격 증명을 지정하기 위해 **다른 옵션 선택**을 선택한 다음, **다른 계정 사용**을 선택해야 할 수도 있음)한 다음, **확인**을 선택합니다.
5. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. **예**를 선택하여 연결을 진행합니다.
6. 이후 단계에서 추적 경로 도구는 라우팅을 테스트하는 데 사용됩니다. 추적 경로는 ICMP(Internet Control Message Protocol)를 사용하는데, 해당 기능은 Windows 방화벽에서 거부됩니다. *myVmPrivate* VM의 PowerShell에서 다음 명령을 입력하여 Windows 방화벽을 통해 ICMP를 사용하도록 설정합니다.

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

    이 자습서에서는 라우팅을 테스트하는 데 경로 추적이 사용되지만, 프로덕션 배포에 Windows 방화벽을 통한 ICMP를 허용하지 않는 것이 좋습니다.
7. [IP 전달을 사용하도록 설정](#enable-ip-forwarding)에서 VM의 네트워크 인터페이스에 대해 Azure 내에서 IP 전달을 사용하도록 설정했습니다. VM 내 운영 체제 또는 VM 내에서 실행 중인 응용 프로그램도 네트워크 트래픽을 전달할 수 있어야 합니다. *myVmNva* VM의 운영 체제 내에서 IP 전달을 사용하도록 설정합니다.

    *myVmPrivate* VM의 명령 프롬프트에서 다음과 같이 *myVmNva* VM에 대한 원격 데스크톱 연결을 만듭니다.

    ``` 
    mstsc /v:myvmnva
    ```
    
    운영 체제 내에서 IP 전달을 사용하도록 설정하려면 *myVmNva* VM의 PowerShell에서 다음 명령을 입력합니다.

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```
    
    *myVmNva* VM을 다시 시작합니다. 그러면 원격 데스크톱 세션의 연결도 끊어집니다.
8. 아직 *myVmPrivate* VM에 연결된 상태에서, *myVmNva* VM이 다시 시작된 후 *myVmPublic* VM에 대한 원격 데스크톱 세션을 만듭니다.

    ``` 
    mstsc /v:myVmPublic
    ```
    
    *myVmPublic* VM의 PowerShell에서 다음 명령을 입력하여 Windows 방화벽을 통해 ICMP를 사용하도록 설정합니다.

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

9. *myVmPublic* VM에서 *myVmPrivate* VM으로의 네트워크 트래픽 라우팅을 테스트하려면 *myVmPublic* VM의 PowerShell에서 다음 명령을 입력합니다.

    ```
    tracert myVmPrivate
    ```

    응답은 다음 예제와 유사합니다.
    
    ```
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:
        
    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4
        
    Trace complete.
    ```
      
    첫 번째 홉이 NVA의 개인 IP 주소인 10.0.2.4인 것을 확인할 수 있습니다. 두 번째 홉은 *myVmPrivate* VM의 개인 IP 주소인 10.0.1.4입니다. 경로가 *myRouteTablePublic* 경로 테이블에 추가되고 *공용* 서브넷에 연결되었으므로 Azure는 트래픽을 직접 *개인* 서브넷에 라우팅하는 대신 NVA를 통해 트래픽을 라우팅합니다.
10.  *myVmPublic* VM에 대한 원격 데스크톱 세션을 닫습니다. 그러면 *myVmPrivate* VM에 연결된 상태가 유지됩니다.
11. *myVmPrivate* VM에서 *myVmPublic* VM으로의 네트워크 트래픽 라우팅을 테스트하려면 *myVmPrivate* VM의 명령 프롬프트에서 다음 명령을 입력합니다.

    ```
    tracert myVmPublic
    ```

    응답은 다음 예제와 유사합니다.

    ```
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:
    
    1     1 ms     1 ms     1 ms  10.0.0.4
    
    Trace complete.
    ```

    트래픽이 *myVmPrivate* VM에서 *myVmPublic* VM으로 직접 라우팅되는지 볼 수 있습니다. 기본적으로 Azure는 트래픽을 서브넷 간에 직접 라우팅합니다.
12. *myVmPrivate* VM에 대한 원격 데스크톱 세션을 닫습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우, 리소스 그룹 및 이 그룹에 포함된 리소스를 모두 삭제합니다. 

1. 포털 맨 위에 있는 **검색** 상자에 *myResourceGroup*을 입력합니다. 검색 결과에 **myResourceGroup**이 보이면 선택합니다.
2. **리소스 그룹 삭제**를 선택합니다.
3. **리소스 그룹 이름 입력:**에 *myResourceGroup*을 입력하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 경로 테이블을 만들고 서브넷에 연결했습니다. 공용 서브넷에서 개인 서브넷으로 트래픽을 라우팅하는 간단한 NVA를 만들었습니다. 이제 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking)에서 방화벽 및 WAN 최적화 같은 네트워크 기능을 수행하는 다양한 미리 구성된 NVA를 배포합니다. 라우팅에 대한 자세한 내용은 [라우팅 개요](virtual-networks-udr-overview.md) 및 [경로 테이블 관리](manage-route-table.md)를 참조하세요.


가상 네트워크 내에서 여러 Azure 리소스를 배포할 수 있는 반면, 일부 Azure PaaS 서비스에 대한 리소스는 가상 네트워크에 배포할 수 없습니다. 하지만 일부 Azure PaaS 서비스의 리소스에 대한 액세스를 가상 네트워크 서브넷의 트래픽만으로 제한할 수 있습니다. Azure PaaS 리소스에 대한 네트워크 액세스를 제한하는 방법에 대해 알아보려면 다음 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [PaaS 리소스에 대한 네트워크 액세스 제한](tutorial-restrict-network-access-to-resources.md)
