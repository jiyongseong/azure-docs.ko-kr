---
title: Azure DevTest Labs에서 Azure Marketplace 이미지 설정 구성 | Microsoft 문서
description: Azure DevTest Labs에서 VM을 만들 때 사용할 수 있는 Azure Marketplace 이미지 구성
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 804c6af2-17e9-4320-af3a-f454bd398379
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 90df853eeca89e61fa69e6967218c4c18274ac1b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Azure DevTest Labs에서 Azure Marketplace 이미지 설정 구성
DevTest Lab에서는 Azure Marketplace 이미지를 랩에서 사용하도록 구성한 방법에 따라 Azure Marketplace 이미지를 기준으로 VM을 만들 수 있습니다. 이 문서에서는 랩에서 VM을 만들 때 사용할 수 있는 Azure Marketplace 이미지(있는 경우)를 지정하는 방법을 보여 줍니다. 이 경우 팀은 필요한 Marketplace 이미지에만 액세스할 수 있습니다. 

## <a name="select-which-azure-marketplace-images-are-allowed-when-creating-a-vm"></a>VM을 만들 때 허용되는 Azure Marketplace 이미지를 선택합니다.
1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
2. **모든 서비스**를 선택한 다음, 목록에서 **DevTest Labs**를 선택합니다.
3. 랩 목록에서 원하는 랩을 탭합니다. 
4. 랩의 블레이드에서 **구성 및 정책**을 선택합니다.
5. 랩의 **구성 및 정책** 블레이드에 있는 **Virtual Machine 기본**에서 **Marketplace 이미지**를 선택합니다.
6. 모든 정규화된 Azure Marketplace 이미지를 새 VM의 기반으로 사용할 수 있게 할지 여부를 지정합니다. **예**를 선택하는 경우 다음 조건을 모두 충족하는 모든 Azure Marketplace 이미지가 랩에서 허용됩니다.
   
   * 이 이미지는 단일 VM을 만듭니다. **그리고**
   * 이 이미지는 Azure Resource Manager를 사용하여 VM을 프로비전합니다. **그리고**
   * 이 이미지는 추가 라이선스 계획을 구입하지 않아도 사용할 수 있습니다.
     
    이미지를 허용하지 않거나 사용할 수 있는 이미지를 지정하려면 **아니요**를 선택합니다.
     
     ![모든 Marketplace 이미지를 VM에 대한 기본 이미지로 사용할 수 있도록 허용하는 옵션](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. 이전 단계에서 **아니요**를 선택하는 경우 **허용된 이미지/모두 선택** 확인란이 활성화됩니다. 
   검색 상자와 함께 이 옵션을 사용하면 목록에 표시된 모든 항목을 빠르게 선택하거나 선택 취소할 수 있습니다.
   * 각 이미지의 해당 확인란을 선택하여 VM 만들기를 개별적으로 허용하려는 Azure Marketplace 이미지를 선택합니다.
   * 랩에서 Azure Marketplace 이미지를 사용하도록 허용하지 않으려면 목록에서 아무 것도 선택하지 않습니다.
   
    ![VM에 대한 기본 이미지로 사용할 수 있는 Azure Marketplace 이미지를 지정할 수 있습니다.](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>다음 단계
VM을 만들 때 Azure Marketplace 이미지를 허용하는 방법을 구성했으면 다음 단계는 [VM을 랩에 추가](devtest-lab-add-vm.md)하는 것입니다.

