---
title: 가상 머신 네트워크 라우팅 진단 문제 - 자습서 - Azure Portal | Microsoft Docs
description: 이 자습서에서는 Azure Network Watcher의 다음 홉 기능을 사용하여 가상 머신 네트워크 라우팅 문제를 진단하는 방법에 대해 알아봅니다.
services: network-watcher
documentationcenter: network-watcher
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: ea64c93726c3bc5c5d60f35790bb337333d4d47a
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2018
---
# <a name="tutorial-diagnose-a-virtual-machine-network-routing-problem-using-the-azure-portal"></a>자습서: Azure Portal을 사용하여 가상 머신 네트워크 라우팅 진단 문제

VM(가상 머신)을 배포하는 경우 Azure는 관련된 몇 가지 기본 경로를 만듭니다. 사용자 지정 경로를 만들어 Azure의 기본 경로를 재정의할 수 있습니다. 경우에 따라 사용자 지정 경로는 VM에서 다른 리소스와 통신할 수 없게 됩니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * VM 만들기
> * Network Watcher의 다음 홉 기능을 사용하여 URL에 대한 통신 테스트
> * IP 주소에 대한 통신 테스트
> * 라우팅 문제를 진단하고 해결하는 방법에 대해 알아봅니다.

원하는 경우 [Azure CLI](diagnose-vm-network-routing-problem-cli.md) 또는 [Azure PowerShell](diagnose-vm-network-routing-problem-powershell.md)을 사용하여 가상 머신 네트워크 라우팅 문제를 진단할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

Azure Portal ( https://portal.azure.com ) 에 로그인합니다.

## <a name="create-a-vm"></a>VM 만들기

1. Azure Portal의 왼쪽 위 모서리에 있는 **+ 리소스 만들기**를 선택합니다.
2. **계산**을 선택한 다음, **Windows Server 2016 Datacenter** 또는 **Ubuntu Server 17.10 VM**을 선택합니다.
3. 다음 정보를 입력하거나 선택하고, 나머지 설정에 대한 기본값을 그대로 적용한 다음, **확인**을 선택합니다.

    |설정|값|
    |---|---|
    |Name|myVm|
    |사용자 이름| 선택한 사용자 이름을 입력합니다.|
    |암호| 선택한 암호를 입력합니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다.|
    |구독| 사용 중인 구독을 선택합니다.|
    |리소스 그룹| **새로 만들기**를 선택하고 **myResourceGroup**을 입력합니다.|
    |위치| **미국 동부**를 선택합니다.|

4. VM에 대한 크기를 선택한 다음, **선택**을 선택합니다.
5. **설정** 아래에서 모든 기본값을 그대로 적용하고, **확인**을 선택합니다.
6. **요약**의 **만들기** 아래에서 **만들기**를 선택하여 VM 배포를 시작합니다. VM을 배포하는 데 몇 분이 걸립니다. 나머지 단계를 계속하기 전에 VM이 배포를 완료하도록 기다립니다.

## <a name="test-network-communication"></a>네트워크 통신 테스트

Network Watcher와의 네트워크 통신을 테스트하려면 먼저 하나 이상의 Azure 지역에서 Network Watcher를 사용하도록 설정하고 Network Watcher의 다음 홉 기능을 사용하여 통신을 테스트해야 합니다.

### <a name="enable-network-watcher"></a>Network Watcher 사용

하나 이상의 지역에서 이미 Network Watcher를 사용하도록 설정한 경우 [다음 홉 사용](#use-next-hop)으로 건너뜁니다.

1. 포털에서 **모든 서비스**를 선택합니다. **필터 상자**에 *Network Watcher*를 입력합니다. 검색 결과에 **Network Watcher**가 나타나면 이를 선택합니다.
2. **지역**을 선택하여 확장하고, 다음 그림처럼 **미국 동부** 오른쪽에서 **...** 를 선택합니다.

    ![Network Watcher 사용](./media/diagnose-vm-network-traffic-filtering-problem/enable-network-watcher.png)

3. **Network Watcher 사용**을 선택합니다.

### <a name="use-next-hop"></a>다음 홉 사용

Azure에서는 기본 대상에 대한 경로를 자동으로 만듭니다. 기본 경로를 재정의하는 사용자 지정 경로를 만들 수 있습니다. 경우에 따라 사용자 지정 경로로 인해 통신이 실패할 수 있습니다. Network Watcher의 다음 홉 기능을 사용하여 트래픽을 라우팅하는 데 Azure에서 사용하는 경로를 결정합니다.

1. Azure Portal의 **Network Watcher** 아래에서 **다음 홉**을 선택합니다.
2. 다음에 나오는 그림에 표시된 대로 구독을 선택하고, 다음 값을 입력하거나 선택한 다음, **다음 홉**을 선택합니다.

    |설정                  |값                                                   |
    |---------                |---------                                               |
    | 리소스 그룹          | myResourceGroup 선택                                 |
    | 가상 머신         | myVm 선택                                            |
    | Linux       | myvm - 네트워크 인터페이스 이름은 달라질 수 있습니다.   |
    | 원본 IP 주소       | 10.0.0.4                                               |
    | 대상 IP 주소   | 13.107.21.200 - www.bing.com에 대한 주소 중 하나입니다. |

    ![다음 홉](./media/diagnose-vm-network-routing-problem/next-hop.png)

    몇 초 후에 결과는 다음 홉 형식이 **인터넷**이며, **경로 테이블 ID**가 **시스템 경로**임을 알려줍니다. 이 결과를 사용하면 대상에 대한 올바른 시스템 경로가 있음을 알 수 있습니다.

3. **대상 IP 주소**를 *172.31.0.100*으로 변경하고 **다음 홉**을 다시 선택합니다. 반환된 결과는 **없음**이 **다음 홉 형식**이며, **경로 테이블 ID**가 **시스템 경로**임도 알려줍니다. 이 결과를 사용하면 대상에 대한 올바른 시스템 경로가 있지만, 대상에 트래픽을 라우팅하는 다음 홉이 없음을 알 수 있습니다.

## <a name="view-details-of-a-route"></a>경로의 세부 정보 보기

1. 추가 라우팅을 분석하려면 네트워크 인터페이스용 유효한 경로를 검토합니다. 포털 맨 위에 있는 검색 상자에서 *myvm*(또는 확인한 네트워크 인터페이스의 이름)을 입력합니다. 검색 결과에서 표시되는 **myvm**을 선택합니다.
2. 다음 그림과 같이 **지원 및 문제 해결**에서 **유효한 경로**를 선택합니다.

    ![유효한 경로](./media/diagnose-vm-network-routing-problem/effective-routes.png)

    [다음 홉 사용](#use-next-hop)에서 13.107.21.200을 사용하여 테스트를 실행할 경우 다른 어떤 경로도 주소를 포함하지 않으므로 주소 접두사 0.0.0.0/0을 포함한 경로를 사용하여 주소에 트래픽을 라우팅했습니다. 기본적으로 다른 경로의 주소 접두사 내에서 지정되지 않은 모든 주소는 인터넷으로 라우팅됩니다.

    그러나 172.31.0.100을 사용하여 테스트를 실행한 경우 결과는 다음 홉 형식이 없음을 알려주었습니다. 이전 그림에서 볼 수 있듯이 172.31.0.100 주소를 포함하는 172.16.0.0/12 접두사에 대한 기본 경로가 있더라도 **다음 홉 형식**은 **없음**입니다. Azure에서는 172.16.0.0/12에 대한 기본 경로를 만들지만 필요가 있을 때까지 다음 홉 형식을 지정하지 않습니다. 예를 들어 가상 네트워크의 주소 공간에 172.16.0.0/12 주소 범위를 추가한 경우 Azure는 **다음 홉 형식**을 경로의 **가상 네트워크**로 변경합니다. 그런 다음, 검사에서는 **가상 네트워크**를 **다음 홉 형식**으로 표시합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 리소스 그룹 및 해당 그룹에 포함된 모든 리소스를 삭제합니다.

1. 포털 맨 위에 있는 **검색** 상자에 *myResourceGroup*을 입력합니다. 검색 결과에 **myResourceGroup**이 보이면 선택합니다.
2. **리소스 그룹 삭제**를 선택합니다.
3. **리소스 그룹 이름 입력:** 에 *myResourceGroup*을 입력하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 VM을 만들고 VM에서 네트워크 라우팅을 진단했습니다. Azure가 여러 개의 기본 경로를 만들고 두 개의 다른 대상에 대한 라우팅을 테스트했음을 알아보았습니다. [Azure에서 라우팅](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) 및 [사용자 지정 경로를 만드는](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route) 방법을 알아봅니다.

아웃바운드 VM 연결의 경우 Network Watcher의 [연결 문제 해결](network-watcher-connectivity-portal.md) 기능을 사용하여 VM과 엔드포인트 간의 네트워크 트래픽 및 엔드포인트에 사용된 경로를 허용하고 거부하는 대기 시간을 결정할 수도 있습니다. Network Watcher 연결 모니터 기능을 사용하여 시간에 따라 IP 주소 또는 URL과 같은 VM과 엔드포인트 간의 통신을 모니터링하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [네트워크 연결 모니터링](connection-monitor.md)