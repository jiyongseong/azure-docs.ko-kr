---
title: Azure DevTest Labs에서 사용자 지정 랩에 액세스 | Microsoft Docs
description: 이 자습서에서는 Azure DevTest Labs를 사용하여 만들어진 랩에 액세스하고 가상 머신을 클레임하고, 사용한 후 클레임 취소합니다.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: be4bde6bd320e8af7cd3119ff4ccdabd942963ca
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361841"
---
# <a name="tutorial-access-a-custom-lab-in-azure-devtest-labs"></a>자습서: Azure DevTest Labs에서 사용자 지정 랩에 액세스
이 자습서에서는 [ 자습서: 사용자 지정 랩 만들기](tutorial-create-custom-lab.md)에서 만들어진 사용자 지정 랩을 사용합니다.

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 사용자 지정 랩에서 VM(가상 머신) 클레임
> * VM에 연결
> * VM 클레임 취소

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="access-the-lab"></a>랩 액세스

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **모든 리소스**를 선택합니다. 
3. 리소스 유형에 대해 **DevTest Labs**를 선택합니다. 
4. 랩을 선택합니다. 

    ![랩 선택](./media/tutorial-use-custom-lab/search-for-select-custom-lab.png)

## <a name="claim-a-vm"></a>VM 클레임

1. **클레임할 수 있는 가상 머신** 목록에서 **...**(줄임표)를 선택하고 **머신 클레임**을 선택합니다.

    ![가상 머신 클레임](./media/tutorial-use-custom-lab/claim-virtual-machine.png)
1. **내 가상 머신** 목록에 VM이 있는지 확인합니다.

    ![내 가상 머신](./media/tutorial-use-custom-lab/my-virtual-machines.png)

## <a name="connect-to-the-vm"></a>VM에 연결

1. 목록에서 VM을 선택합니다. VM에 대한 **가상 머신 페이지**가 표시됩니다. 도구 모음에서 **연결**을 선택합니다.

    ![가상 머신에 연결](./media/tutorial-use-custom-lab/connect-button.png)
2. 다운로드한 **RDP** 파일을 하드 디스크에 저장하고 그것을 사용하여 가상 머신에 연결합니다. 이전 섹션에서 VM을 만들 때 언급한 사용자 이름과 암호를 지정합니다. 

## <a name="unclaim-the-vm"></a>VM 클레임 취소
VM 사용을 마친 후에는 다음 단계에 따라 VM을 클레임 취소합니다. 

1. 가상 머신 페이지에서 도구 모음의 **Unclaim**을 선택합니다. 

    ![VM 클레임 취소](./media/tutorial-use-custom-lab/unclaim-vm-menu.png)
1. VM은 클레임 취소되기 전에 종료됩니다. 

    ![클레임 취소 상태](./media/tutorial-use-custom-lab/unclaim-status.png) 
1. 클레임 취소 작업이 완료되면 하단의 **Claimable virtual machines** 목록에 VM이 표시됩니다. 
    
## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure DevTest Labs를 사용하여 작성된 사용자 지정 랩에 액세스하여 사용하는 방법을 배웠습니다. 사용자 지정 랩에서 VM 액세스 및 사용에 대한 자세한 내용은 다음을 참조하세요. 

> [!div class="nextstepaction"]
> [방법: 사용자 지정 랩에서 VM 사용](devtest-lab-add-vm.md)

