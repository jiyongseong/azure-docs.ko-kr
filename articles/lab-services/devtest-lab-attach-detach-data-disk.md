---
title: Azure DevTest Labs에서 데이터 디스크를 가상 머신에 연결 또는 분리 | Microsoft Docs
description: Azure DevTest Labs에서 데이터 디스크를 가상 머신에 연결하거나 분리하는 방법 알아보기
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 9616bf38-7db8-4915-a32a-e4f40a7a56ad
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 193b66cf8bdaaefed5f073bec3ecb9050d076f19
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="attach-or-detach-a-data-disk-to-a-virtual-machine-in-azure-devtest-labs"></a>Azure DevTest Labs에서 데이터 디스크를 가상 머신에 연결 또는 분리
[Azure Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)는 가상 머신 데이터 디스크와 연결된 저장소 계정을 관리합니다. 사용자가 새 데이터 디스크를 VM에 연결하고 필요한 디스크의 유형과 크기를 지정하면, Azure에서 디스크가 자동으로 만들어지고 관리됩니다. 그런 다음 데이터 디스크를 VM에서 분리하고 동일한 VM에 나중에 다시 연결하거나 동일한 사용자에 속하는 다른 VM에 연결할 수 있습니다.

이 기능은 개별 가상 머신 외부에서 저장소나 소프트웨어를 관리하는 데 편리합니다. 저장소나 소프트웨어가 데이터 디스크 내에 이미 존재하는 경우 해당 데이터 디스크를 소유하는 사용자가 소유한 모든 VM에 쉽게 연결, 분리 및 다시 연결할 수 있습니다.

## <a name="attach-a-data-disk"></a>데이터 디스크 연결
데이터 디스크를 VM에 연결하기 전에 다음 팁을 검토하세요.

- VM의 크기는 연결할 수 있는 데이터 디스크 수를 제어합니다. 자세한 내용은 [가상 머신의 크기](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)를 참조하세요.
- 데이터 디스크는 실행 중인 VM에만 연결할 수 있습니다. 데이터 디스크 연결을 시도하기 전에 VM이 실행 중인지 확인하세요.

### <a name="attach-a-new-disk"></a>새 디스크 연결
다음 단계에 따라 새 관리되는 데이터 디스크를 Azure DevTest Labs의 VM에 연결합니다.

1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
1. **모든 서비스**를 선택한 다음, 목록에서 **DevTest Labs**를 선택합니다.
1. 랩 목록에서 원하는 랩을 탭합니다. 
1. **내 가상 머신** 목록에서 실행 중인 VM을 선택합니다.
1. 왼쪽 메뉴에서 **디스크**를 선택합니다.

    ![가상 머신용 데이터 디스크 선택](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-data-disk.png)
1. **새 디스크 연결**을 선택하여 새 데이터 디스크를 만들어서 VM에 연결합니다.

    ![새 데이터 디스크를 가상 머신에 연결](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new.png)
1. 데이터 디스크 이름, 유형 및 크기를 입력하여 **새 디스크 연결** 창을 모두 채웁니다.

    !["새 디스크 연결" 양식 채우기](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new-form.png)
1. **확인**을 선택합니다.

잠시 후 새 데이터 디스크가 만들어져서 VM에 연결되고 해당 VM의 **데이터 디스크** 목록에 나타납니다.

### <a name="attach-an-existing-disk"></a>기존 디스크 연결
다음 단계에 따라 기존의 사용 가능한 데이터 디스크를 실행 중인 VM에 다시 연결합니다. 

1. 데이터 디스크를 다시 연결할 실행 중인 VM을 선택합니다.
1. 왼쪽 메뉴에서 **디스크**를 선택합니다.
1. **기존 디스크 연결**을 선택하여 사용 가능한 데이터 디스크를 VM에 연결합니다.

    ![기존 데이터 디스크를 가상 머신에 연결](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing2.png)

1. **기존 디스크 연결** 창에서 확인을 선택합니다.

    ![기존 데이터 디스크를 가상 머신에 연결](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing.png)

잠시 후 데이터 디스크가 VM에 연결되고 해당 VM의 **데이터 디스크** 목록에 나타납니다.

## <a name="detach-a-data-disk"></a>데이터 디스크 분리
VM에 연결된 데이터 디스크가 더 이상 필요하지 않은 경우 쉽게 분리할 수 있습니다. 분리하면 디스크가 VM에서 제거되지만 나중에 사용할 수 있도록 저장소에 보관됩니다.

디스크에 있는 기존 데이터를 다시 사용하려는 경우 동일한 가상 머신이나 다른 가상 머신에 다시 연결할 수 있습니다.

### <a name="detach-from-the-vms-management-pane"></a>VM의 관리 창에서 분리
1. 가상 머신 목록에서 데이터 디스크가 연결된 VM을 선택합니다.
1. 왼쪽 메뉴에서 **디스크**를 선택합니다.

    ![가상 머신용 데이터 디스크 선택](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-data-disk.png) 
1. **데이터 디스크** 목록에서 분리할 데이터 디스크를 선택합니다.
1. 디스크의 세부 정보 창 위쪽에서 **분리**를 선택합니다.

    ![데이터 디스크 분리](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk2.png)
1. **예**를 선택하여 데이터 디스크를 분리할 것인지 확인합니다.

디스크가 분리되어 다른 VM에 연결할 수 있습니다. 
### <a name="detach-from-the-labs-main-pane"></a>랩의 기본 창에서 분리
1. 랩의 기본 창에서 **내 데이터 디스크**를 선택합니다.

    ![랩의 데이터 디스크에 액세스](./media/devtest-lab-attach-detach-data-disk/devtest-lab-my-data-disks.png)
1. 분리할 데이터 디스크를 마우스 오른쪽 단추로 클릭하거나 줄임표(...)를 선택하고 **분리**를 선택합니다.

    ![데이터 디스크 분리](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk.png)
1. **예**를 선택하여 데이터 디스크를 분리할 것인지 확인합니다.

   > [!NOTE]
   > 데이터 디스크가 이미 분리되어 있는 경우 **삭제**를 선택하여 사용 가능한 데이터 디스크 목록에서 제거하도록 선택할 수 있습니다.
   >
   >

## <a name="upgrade-an-unmanaged-data-disk"></a>관리되지 않는 데이터 디스크 업그레이드
관리되지 않는 데이터 디스크를 사용하는 기존 VM이 있는 경우 관리 디스크를 사용하도록 VM을 쉽게 변환할 수 있습니다. 이 프로세스는 OS 디스크와 연결된 데이터 디스크를 변환합니다.

관리되지 않는 데이터 디스크를 업그레이드하려면 이 문서에 설명된 단계에 따라 관리되지 않는 VM에서 [데이터 디스크를 분리](#detach-a-data-disk)합니다. 그런 다음 관리되는 VM에 [디스크를 다시 연결](#attach-an-existing-disk)하여 데이터 디스크를 관리되지 않는 데이터 디스크에서 관리되는 데이터 디스크로 자동으로 업그레이드합니다.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>다음 단계
[클레임할 수 있는 가상 머신](devtest-lab-add-claimable-vm.md#unclaim-a-vm)의 데이터 디스크를 관리하는 방법을 알아봅니다.

