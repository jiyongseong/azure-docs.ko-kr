---
title: Azure DevTest Labs에서 첫 번째 VM 만들기 | Microsoft Docs
description: Azure DevTest Labs에서 랩에 첫 번째 가상 머신을 만드는 방법 알아보기
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: fbc5a438-6e02-4952-b654-b8fa7322ae5f
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 0240bbdf2055ac44f8836adfaad3bf6c44893b77
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="create-your-first-vm-in-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs에서 첫 번째 VM 만들기

DevTest Labs에 처음에 액세스하고 첫 번째 VM을 만드는 경우 미리 로드된 [기본 Marketplace 이미지](devtest-lab-configure-marketplace-images.md)를 사용하여 수행할 것입니다. 나중에 VM을 더 만들 때 [사용자 지정 이미지와 수식](devtest-lab-add-vm.md) 중에서 선택할 수도 있습니다. 

이 자습서에서는 DevTest Labs에서 랩에 첫 번째 VM을 추가하기 위해 Azure Portal을 사용하는 방법을 설명합니다.

## <a name="steps-to-add-your-first-vm-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs에서 랩에 첫 번째 VM을 추가하는 단계
1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
1. **모든 서비스**를 선택한 다음, 목록에서 **DevTest Labs**를 선택합니다.
1. 랩 목록에서 VM을 만들려는 랩을 선택합니다.  
1. 랩의 **개요** 블레이드에서 **+ 추가**를 선택합니다.  

    ![VM 단추 추가](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. **기본 선택** 블레이드에서 VM의 Marketplace 이미지를 선택합니다.
1. **가상 머신** 블레이드의 **가상 머신 이름** 텍스트 상자에 새 가상 머신의 이름을 입력합니다.

    ![랩 VM 블레이드](./media/devtest-lab-add-vm/devtestlab-lab-add-first-vm.png)

1. 가상 머신에서 관리자 권한이 부여된 **사용자 이름**을 입력합니다.  
1. **값 입력** 텍스트 필드에 암호를 입력합니다.
1. **가상 머신 디스크 유형**은 랩에서 가상 머신의 저장소 디스크 유형을 허용하는지 확인합니다.
1. **가상 머신 크기**를 선택하고 만들려는 프로세서 코어, RAM 크기 및 VM의 하드 드라이브 크기를 지정하는 미리 정의된 항목 중 하나를 선택합니다.
1. **아티팩트** 를 선택하고 아티팩트 목록에서 기본 이미지에 추가하려는 아티팩트를 선택하고 구성합니다.
    **참고:** DevTest Lab을 처음 접하거나 아티팩트를 구성 중인 경우 [VM에 기존 아티팩트 추가](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) 섹션을 참조한 다음 완료되면 여기로 돌아옵니다.
1. **만들기** 를 누르고 지정된 VM을 랩에 추가합니다.

   랩 블레이드는 VM의 만들기 상태를 표시합니다. 처음에는 **생성 중**이 표시되고 VM이 시작하면 **실행 중**으로 표시됩니다.

## <a name="next-steps"></a>다음 단계
* VM을 만든 후에는 해당 VM의 블레이드에서 **연결** 을 선택하여 VM에 연결할 수 있습니다.
* 이후의 VM을 랩에 추가하는 방법에 대한 자세한 정보는 [랩에 VM 추가](devtest-lab-add-vm.md)를 확인하세요.
* [DevTest Labs Azure Resource Manager 빠른 시작 템플릿 갤러리](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)를 탐색합니다.
