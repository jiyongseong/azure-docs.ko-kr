---
title: Azure Portal을 사용하여 VM으로 포트 열기 | Microsoft Docs
description: Azure Portal의 Resource Manager 배포 모델을 사용하여 Windows VM에 대한 포트를 열고 끝점을 만드는 방법 알아보기
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: f7cf0319-5ee7-435e-8f94-c484bf5ee6f1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: iainfou
ms.openlocfilehash: a64e2bbe1bb784f0b6032980d6f212470549cdf4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34366922"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Azure Portal을 사용하여 가상 머신에 대한 포털을 여는 방법
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>빠른 명령
[Azure PowerShell을 사용하여 수행할 수도 있습니다](nsg-quickstart-powershell.md).

먼저 네트워크 보안 그룹을 만듭니다. 포털에서 리소스 그룹을 선택하고 **추가**를 선택한 후 **네트워크 보안 그룹**을 검색하여 선택합니다.

![네트워크 보안 그룹 추가](./media/nsg-quickstart-portal/add-nsg.png)

네트워크 보안 그룹의 이름을 입력하고, 리소스 그룹을 선택하거나 만들고, 위치를 선택합니다. 작업을 완료하면 **만들기**를 선택합니다.

![네트워크 보안 그룹 만들기](./media/nsg-quickstart-portal/create-nsg.png)

새 네트워크 보안 그룹을 선택합니다. '인바운드 보안 규칙'을 선택한 다음 **추가** 단추를 선택하여 규칙을 만듭니다.

![인바운드 규칙 추가](./media/nsg-quickstart-portal/add-inbound-rule.png)

트래픽을 허용하는 규칙을 작성하려면:

- **기본** 단추를 선택합니다. 기본적으로 **고급** 창은 특정 원본 IP 블록 또는 포트 범위를 정의하는 것과 같은 몇 가지 추가 구성 옵션을 제공합니다.
- 드롭다운 메뉴에서 공통 **서비스**를 선택합니다(예: *HTTP*). *사용자 지정*을 선택하여 사용할 특정 포트를 제공할 수도 있습니다. 
- 원하는 경우 우선 순위 또는 이름을 변경합니다. 이 우선 순위는 규칙이 적용되는 순서에 영향을 줍니다. 숫자 값이 적을수록 규칙이 먼저 적용됩니다.
- 준비되면 **확인**을 선택하여 규칙을 만듭니다.

![인바운드 규칙 만들기](./media/nsg-quickstart-portal/create-inbound-rule.png)

마지막 단계는 네트워크 보안 그룹을 서브넷 또는 특정 네트워크 인터페이스에 연결하는 것입니다. 서브넷에 네트워크 보안 그룹을 연결합니다. **서브넷**을 선택한 후 **연결**을 선택합니다.

![서브넷에 네트워크 보안 그룹 연결](./media/nsg-quickstart-portal/associate-subnet.png)

가상 네트워크를 선택하고 적절한 서브넷을 선택합니다.

![가상 네트워킹에 네트워크 보안 그룹 연결](./media/nsg-quickstart-portal/select-vnet-subnet.png)

이제 네트워크 보안 그룹을 만들고, 포트 80에서 트래픽을 허용하는 인바운드 규칙을 만들었으며 해당 규칙을 서브넷과 연결했습니다. 해당 서브넷에 연결하는 모든 VM은 포트 80에 연결할 수 있게 됩니다.

## <a name="more-information-on-network-security-groups"></a>네트워크 보안 그룹에 대한 자세한 정보
여기서 빠른 명령을 사용하면 VM으로 트래픽이 이동되도록 할 수 있습니다. 네트워크 보안 그룹은 리소스에 대한 액세스를 제어하는 많은 기능과 세분성을 제공합니다. [여기서 네트워크 보안 그룹 및 ACL 규칙 만들기](../../virtual-network/tutorial-filter-network-traffic.md)에 대해 자세히 읽어보세요.

고가용성 웹 응용 프로그램인 경우 VM을 Azure Load Balancer 뒤에 배치해야 합니다. 부하 분산 장치는 트래픽 필터링을 제공하는 네트워크 보안 그룹으로 트래픽을 VM에 분산시킵니다. 자세한 내용은 [Azure의 Linux 가상 머신 부하를 분산하여 고가용성 응용 프로그램을 만드는 방법](tutorial-load-balancer.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
이 예제에서는 HTTP 트래픽을 허용하는 간단한 규칙을 만들었습니다. 다음 문서에서 보다 자세한 환경을 만들기 위한 정보를 찾을 수 있습니다.

* [Azure Resource Manager 개요](../../azure-resource-manager/resource-group-overview.md)
* [네트워크 보안 그룹이란?](../../virtual-network/security-overview.md)