---
title: 네트워크 간 통신 문제 진단 - 자습서 - Azure Portal | Microsoft Docs
description: Network Watcher의 VPN 진단 기능을 사용하여 Azure 가상 네트워크를 통해 온-프레미스 또는 다른 가상 네트워크에 연결된 Azure 가상 네트워크 간 통신 문제를 진단하는 방법을 알아봅니다.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
Customer intent: I need to determine why resources in a virtual network can't communicate with resources in a different network.
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: d89c5a3f2545edd7c02b67fa9d2e2b78937a9791
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="tutorial-diagnose-a-communication-problem-between-networks-using-the-azure-portal"></a>자습서: Azure Portal을 사용하여 네트워크 간 통신 문제 진단

가상 네트워크 게이트웨이는 Azure 가상 네트워크를 온-프레미스 또는 다른 가상 네트워크에 연결합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Network Watcher의 VPN 진단 기능을 사용하여 가상 네트워크 게이트웨이 문제 진단
> * 게이트웨이 연결 문제 진단
> * 게이트웨이 문제 해결

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

VPN진단을 사용하려면 실행 중인 기존 VPN 게이트웨이가 있어야 합니다. 진단할 기존 VPN 게이트웨이가 없는 경우 [PowerShell 스크립트](../vpn-gateway/scripts/vpn-gateway-sample-site-to-site-powershell.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)를 사용하여 배포할 수 있습니다. 다음 위치에서 PowerShell 스크립트를 실행할 수 있습니다.
    - **로컬에 설치된 PowerShell**: 이 스크립트에는 AzureRM PowerShell 모듈 버전 5.7.0 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable AzureRM`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Login-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다.
    - **Azure Cloud Shell**: [Azure Cloud Shell](https://shell.azure.com/powershell)은 최신 버전의 PowerShell이 설치 및 구성되어 있으며 사용자를 Azure에 로그인합니다.

이 스크립트는 VPN 게이트웨이를 만드는 데 약 1시간 정도 걸립니다. 나머지 단계에서는 진단하려는 게이트웨이가 이 스크립트로 배포된 것이라고 가정합니다. 사용자의 기존 게이트웨이를 진단하는 경우 결과가 달라집니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="enable-network-watcher"></a>Network Watcher 사용

미국 동부 지역에서 이미 Network Watcher를 사용하도록 설정한 경우 [게이트웨이 진단](#diagnose-a-gateway)으로 건너뜁니다.

1. 포털에서 **모든 서비스**를 선택합니다. **필터 상자**에 *Network Watcher*를 입력합니다. 검색 결과에 **Network Watcher**가 나타나면 이를 선택합니다.
2. **지역**을 선택하여 확장하고, 다음 그림처럼 **미국 동부** 오른쪽에서 **...** 를 선택합니다.

    ![Network Watcher 사용](./media/diagnose-communication-problem-between-networks/enable-network-watcher.png)

3. **Network Watcher 사용**을 선택합니다.

## <a name="diagnose-a-gateway"></a>게이트웨이 진단

1. 포털의 왼쪽에서 **모든 서비스**를 선택합니다.
2. **필터** 상자에 *Network Watcher*를 입력합니다. 검색 결과에 **Network Watcher**가 나타나면 이를 선택합니다.
3. **네트워크 진단 도구**에서 **VPN 진단**을 선택합니다.
4. **저장소 계정**을 선택한 다음, 진단 정보를 기록할 저장소 계정을 선택합니다.
5. **저장소 계정** 목록에서 사용할 저장소 계정을 선택합니다. 기존 저장소 계정이 없는 경우 **+ 저장소 계정**을 선택하고, 필요한 정보를 입력하거나 선택하고, **만들기**를 선택하여 저장소 계정을 만듭니다. [필수 구성 요소](#prerequisites)의 스크립트를 사용하여 VPN 게이트웨이를 만든 경우 게이트웨이와 동일한 리소스 그룹 *TestRG1*에 저장소 계정을 만듭니다.
6. **컨테이너** 목록에서 사용할 컨테이너를 사용하고 **선택**을 누릅니다. 컨테이너가 없는 경우 **+ 컨테이너**를 선택하고, 컨테이너 이름을 입력하고, **확인**을 선택합니다.
7. 게이트웨이를 선택하고 **문제 해결 시작**을 선택합니다. 다음 그림처럼 **Vnet1GW**라는 게이트웨이에 대해 테스트가 실행됩니다.

    ![VPN 진단](./media/diagnose-communication-problem-between-networks/vpn-diagnostics.png)

8. 이전 그림에서 테스트가 실행되는 동안 **문제 해결 상태** 열에 **실행 중**이 표시되고 **시작되지 않음**으로 나타납니다. 테스트가 실행될 때까지 몇 분 정도 걸릴 수 있습니다.
9. 완료된 테스트의 상태를 봅니다. 다음 그림은 완료된 진단 테스트의 상태 결과를 보여줍니다.

    ![상태](./media/diagnose-communication-problem-between-networks/status.png)

    **문제 해결 상태**가 **비정상**인 것을 볼 수 있으며, **상태** 탭에서 문제의 **요약** 및 **상세 정보**를 볼 수 있습니다.
10. **작업** 탭을 선택하면 VPN 진단에서 추가 정보를 제공합니다. 다음 그림의 예제에서는 VPN 진단을 통해 각 연결의 상태를 확인해야 한다는 것을 알 수 있습니다.

  ![조치](./media/diagnose-communication-problem-between-networks/action.png)

## <a name="diagnose-a-gateway-connection"></a>게이트웨이 연결 진단

게이트웨이는 게이트웨이 연결을 통해 다른 네트워크에 연결됩니다. 가상 네트워크 및 연결된 네트워크 간의 통신이 성공하려면 게이트웨이 및 게이트웨이 연결 상태가 모두 정상이어야 합니다.

1. [게이트웨이 진단](#diagnose-a-gateway)의 7단계를 다시 완료하되, 이번에는 연결을 선택합니다. 다음 예제에서는 **VNet1toSite1**이라는 연결을 테스트합니다.

    ![연결](./media/diagnose-communication-problem-between-networks/connection.png)

    테스트는 몇 분 동안 실행됩니다.
2. 연결 테스트가 완료되면 **상태** 및 **작업** 탭에 다음 그림의 결과와 비슷한 결과가 표시됩니다.

    ![연결 상태](./media/diagnose-communication-problem-between-networks/connection-status.png)

    ![연결 작업](./media/diagnose-communication-problem-between-networks/connection-action.png)

    VPN 진단은 **상태** 탭에서 잘못된 부분을 알려주고, **작업** 탭에서 문제를 일으킬 수 있는 부분에 대한 여러 가지 권장 사항을 제공합니다.

    테스트한 게이트웨이가 [필수 구성 요소](#prerequisites)의 [스크립트](../vpn-gateway/scripts/vpn-gateway-sample-site-to-site-powershell.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)를 통해 배포된 게이트웨이인 경우 **상태** 탭의 문제와  **작업** 탭의 처음 두 항목이 바로 정확한 문제입니다. 스크립트가 온-프레미스 VPN 게이트웨이 장치에 대한 자리 표시자 IP 주소 23.99.221.164를 구성합니다.

    이 문제를 해결하려면 온-프레미스 VPN 게이트웨이가 [올바르게 구성](../vpn-gateway/vpn-gateway-about-vpn-devices.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)되었는지 확인하고, 스크립트를 통해 로컬 네트워크 게이트웨이에 구성된 IP 주소를 온-프레미스 VPN 게이트웨이의 실제 공용 주소로 변경해야 합니다.

## <a name="clean-up-resources"></a>리소스 정리

[필수 구성 요소](#prerequisites)의 스크립트만을 사용하여 VPN 게이트웨이를 만들고 이 자습서를 완료했으며 더 이상 필요 없는 경우 리소스 그룹 및 리소스 그룹에 포함된 모든 리소스를 삭제합니다.

1. 포털 맨 위에 있는 **검색** 상자에 *TestRG1*을 입력합니다. 검색 결과에 **TestRG1**이 보이면 선택합니다.
2. **리소스 그룹 삭제**를 선택합니다.
3. **리소스 그룹 이름 입력:** 에 *TestRG1*을 입력하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 가상 네트워크 게이트웨이 문제를 진단하는 방법을 알아보았습니다. 비정상 동작에 대한 로그를 검토할 수 있도록 VM과 주고 받는 네트워크 통신을 기록할 수도 있습니다. 자세한 방법을 보려면 그 다음 자습서를 진행하세요.

> [!div class="nextstepaction"]
> [VM과 주고 받는 네트워크 트래픽 로깅](network-watcher-nsg-flow-logging-portal.md)