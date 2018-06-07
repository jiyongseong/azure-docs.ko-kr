---
title: 고정 공용 IP 주소를 사용하는 VM 만들기 - Azure Portal | Microsoft Docs
description: Azure Portal을 사용하여 고정 공용 IP 주소를 사용하는 VM을 만드는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: e9546bcc-f300-428f-b94a-056c5bd29035
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 50ae4d6e8c275db16f811a2a1a063eda441f150b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-portal"></a>Azure Portal을 사용하여 고정 공용 IP 주소를 사용하는 VM 만들기

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
> * [PowerShell(클래식)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Azure에는 리소스를 만들고 작업하는 [Resource Manager와 클래식](../resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 Resource Manager 배포 모델 사용을 설명하며 Microsoft에서는 대부분의 새로운 배포에 대해 클래식 배포 모델 대신 이 모델을 사용하도록 권장합니다.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="create-a-vm-with-a-static-public-ip"></a>고정 공용 IP를 사용하여 VM 만들기

Azure Portal에 고정 공용 IP 주소가 있는 VM을 만들려면 다음 단계를 완료합니다.

1. 브라우저에서 [Azure Portal](https://portal.azure.com)로 이동하고 필요한 경우 Azure 계정으로 로그인합니다.
2. 포털의 왼쪽 맨 위에서 **리소스 만들기**>>**Compute**>**Windows Server 2012 R2 Datacenter**를 차례로 클릭합니다.
3. **배포 모델 선택** 목록에서 **리소스 관리자**를 선택하고 **만들기**를 클릭합니다.
4. **기본** 창에 아래와 같이 VM 정보를 입력한 다음, **확인**을 클릭합니다.
   
    ![Azure 포털 - 기본 사항](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)
5. **크기 선택** 창에서 아래와 같이 **A1 표준**을 클릭한 다음, **선택**을 클릭합니다.
   
    ![Azure 포털 - 크기 선택](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)
6. **설정** 창에서 **공용 IP 주소**를 클릭한 다음, **공용 IP 주소 만들기** 창의 **할당**에서 아래와 같이 **고정**을 클릭합니다. 그런 다음, **확인**을 클릭합니다.
   
    ![Azure 포털 - 공용 IP 주소 만들기](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)
7. **설정** 창에서 **확인**을 클릭합니다.
8. **요약** 창을 검토한 다음, 아래와 같이 **확인**을 클릭합니다.
   
    ![Azure 포털 - 공용 IP 주소 만들기](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)
9. 대시보드에서 새 타일을 확인합니다.
   
    ![Azure 포털 - 공용 IP 주소 만들기](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)
10. VM이 만들어지면 **설정** 창이 다음과 같이 표시됩니다.
    
    ![Azure 포털 - 공용 IP 주소 만들기](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)

## <a name="set-ip-addresses-within-the-operating-system"></a>운영 체제 내에서 IP 주소 설정

가상 머신의 운영 체제 내에서 Azure Virtual Machine에 할당된 공용 IP 주소는 절대 수동으로 할당하면 안 됩니다. [Windows VM에 여러 IP 주소를 할당](virtual-network-multiple-ip-addresses-portal.md)할 때처럼 반드시 필요한 경우가 아니면, VM의 운영 체제 내에서 Azure Virtual Machine에 할당된 개인 IP를 고정적으로 할당하는 것은 바람직하지 않습니다. 운영 체제 내에서 개인 IP 주소를 수동으로 설정하는 경우 Azure [네트워크 인터페이스](virtual-network-network-interface-addresses.md#change-ip-address-settings)에 할당된 개인 IP 주소와 동일한 주소인지 확인합니다. 두 주소가 같지 않으면 가상 머신에 대한 연결이 끊어질 수 있습니다. [개인 IP 주소](virtual-network-network-interface-addresses.md#private) 설정에 대해 자세히 알아봅니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 만든 VM 간에 네트워크 트래픽을 전달할 수 있습니다. 네트워크 인터페이스, 서브넷 또는 둘 간에 전달할 수 있는 트래픽을 제한하는 네트워크 보안 그룹 내에서 인바운드 및 아웃바운드 보안 규칙을 정의할 수 있습니다. 네트워크 보안 그룹에 대한 자세한 내용은 [네트워크 보안 그룹 개요](security-overview.md)를 참조하세요.