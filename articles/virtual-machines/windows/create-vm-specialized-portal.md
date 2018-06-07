---
title: "Azure Portal의 특수한 VHD에서 Windows VM 만들기 | Microsoft Docs"
description: "Azure Portal의 VHD에서 새 Windows VM을 만듭니다."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: cynthn
ms.openlocfilehash: 33e94777047ea8a116ae6f393439521356a53509
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2018
---
# <a name="create-a-vm-from-a-vhd-using-the-azure-portal"></a>Azure Portal을 사용하여 VHD에서 VM 만들기


Azure에서 VM을 만드는 방법에는 몇 가지가 있습니다. 사용할 VHD가 이미 있거나 사용할 기존 VM에서 VHD를 복사하려는 경우, VHD를 OS 디스크로 연결하여 새 VM을 만들 수 있습니다. 이 프로세스는 VHD를 새 VM에 OS 디스크로 *연결*합니다.

삭제된 VM의 VHD에서 새 VM을 만들 수도 있습니다. 예를 들어, 제대로 작동하지 않는 Azure VM이 있는 경우 해당 VM을 삭제하고 VHD를 사용하여 새 VM을 만들 수 있습니다. 동일한 VHD를 다시 사용하거나, 스냅숏을 만든 다음 스냅숏에서 새로운 관리 디스크를 만들어 VHD의 복사본을 만들 수 있습니다. 몇 가지 단계가 추가로 진행되지만, 원본 VHD를 유지할 수 있으며 필요한 경우 장애 복구(Failback)할 스냅숏이 제공됩니다.

Azure에서 VM을 만드는 데 사용하려는 온-프레미스 VM이 있습니다. VHD를 업로드한 후 새 VM에 연결할 수 있습니다. VHD를 업로드하려면 PowerShell 또는 다른 도구를 사용하여 저장소 계정에 업로드한 다음, VHD에서 관리 디스크를 만들어야 합니다. 자세한 내용은 [특수 VHD 업로드](create-vm-specialized.md#option-2-upload-a-specialized-vhd)를 참조하세요.

VM 또는 VHD를 사용하여 여러 VM을 만들려면 이 방법을 사용하지 않아야 합니다. 대규모 배포의 경우 [이미지를 만들고](capture-image-resource.md) [해당 이미지를 사용하여 여러 VM을 만들어야](create-vm-generalized-managed.md) 합니다.


## <a name="copy-a-disk"></a>디스크 복사

스냅숏을 생성한 후 스냅숏에서 디스크를 만듭니다. 이렇게 하면 원본 VHD를 장애 복구(Failback)용으로 유지할 수 있습니다.

1. 왼쪽 메뉴에서 **모든 리소스**를 클릭합니다.
2. **모든 형식** 드롭다운 목록에서 **모두 선택**을 선택 취소하고 아래로 스크롤하여 **디스크**를 선택한 후 사용 가능한 디스크를 찾습니다.
3. 사용하려는 디스크를 클릭합니다. 디스크에 대한 **개요** 페이지가 열립니다.
4. 개요 페이지의 맨 위에 있는 메뉴에서 **+ 스냅숏 만들기**를 클릭합니다. 
5. 스냅숏의 이름을 입력합니다.
6. 스냅숏에 대한 **리소스 그룹**을 선택합니다. 기존 리소스 그룹을 사용하거나 리소스 그룹을 새로 만들 수 있습니다.
7. 표준(HDD) 또는 프리미엄(SDD) 저장소 중 어느 것을 사용할지 선택합니다.
8. 완료되면 **만들기**를 클릭하여 스냅숏을 만듭니다.
9. 스냅숏이 만들어진 후 왼쪽 메뉴에서 **+ 리소스 만들기**를 클릭합니다.
10. 검색 표시줄에 **관리 디스크**를 입력하고 목록에서 **Managed Disks**를 선택합니다.
11. **Managed Disks** 페이지에서 **만들기**를 클릭합니다.
12. 디스크의 이름을 입력합니다.
13. 디스크에 대한 **리소스 그룹**을 선택합니다. 기존 리소스 그룹을 사용하거나 리소스 그룹을 새로 만들 수 있습니다. 이 리소스 그룹은 디스크에서 VM을 만드는 경우의 리소스 그룹이 됩니다.
14. 표준(HDD) 또는 프리미엄(SDD) 저장소 중 어느 것을 사용할지 선택합니다.
15. **원본 유형**에서 **스냅숏**을 선택했는지 확인합니다.
16. **원본 스냅숏** 드롭다운 목록에서 사용할 스냅숏을 선택합니다.
17. 필요에 따라 기타 조정을 수행한 다음 **만들기**를 클릭하여 디스크를 만듭니다.

## <a name="create-a-vm-from-a-disk"></a>디스크에서 VM 만들기

사용하려는 관리 디스크 VHD가 있으면 포털에서 VM을 만들 수 있습니다.

1. 왼쪽 메뉴에서 **모든 리소스**를 클릭합니다.
2. **모든 형식** 드롭다운 목록에서 **모두 선택**을 선택 취소하고 아래로 스크롤하여 **디스크**를 선택한 후 사용 가능한 디스크를 찾습니다.
3. 사용하려는 디스크를 클릭합니다. 디스크에 대한 **개요** 페이지가 열립니다.
개요 페이지에서 **디스크 상태**가 **연결되지 않음**으로 표시되는지 확인합니다. 그렇지 않으면 VM에서 디스크를 분리하거나 VM을 삭제하여 디스크 공간을 확보해야 합니다.
4. 창 맨 위에 있는 메뉴에서 **+ VM 만들기**를 클릭합니다.
5. 새 VM에 대한 **기본 사항** 페이지에서 이름을 입력하고 기존 리소스 그룹을 선택하거나 새 리소스 그룹을 만듭니다.
6. **크기** 페이지에서 VM 크기 페이지를 선택하고 **선택**을 클릭합니다.
7. **설정** 페이지에서 포털이 모든 새 리소스를 만들도록 하거나, 기존 **가상 네트워크** 및 **네트워크 보안 그룹**을 선택할 수 있습니다. 포털은 항상 새 VM에 대한 새 NIC와 공용 IP 주소를 만듭니다. 
8. 모니터링 옵션을 변경하고 필요에 따라 확장을 추가합니다.
9. 완료되면 **확인**을 클릭합니다. 
10. VM 구성이 유효성 검사를 통과하는 경우 **확인**을 클릭하여 배포를 시작합니다.

## <a name="next-steps"></a>다음 단계

PowerShell을 사용하여 [VHD를 Azure에 업로드하고 특수한 VM을 만들](create-vm-specialized.md) 수도 있습니다.


