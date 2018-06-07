---
title: VM을 만드는 Azure DevTest Labs 수식 관리 | Microsoft 문서
description: Azure DevTest Labs 수식을 업데이트하고 제거하는 방법 알아보기
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 841dd95a-657f-4d80-ba26-59a9b5104fe4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 40c30958c5e0e24f2b35475848c1828df4a40d0c
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2018
ms.locfileid: "33781922"
---
# <a name="manage-azure-devtest-labs-formulas"></a>Azure DevTest Labs 수식 관리

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

이 문서에서는 기본(사용자 지정 이미지, Marketplace 이미지 또는 다른 수식) 또는 기존 VM에서 수식을 만드는 방법을 보여 줍니다. 이 문서에서 기존 수식을 관리하는 방법을 안내합니다.

## <a name="create-a-formula"></a>수식 만들기
DevTest Lab *사용자* 권한이 있으면 수식을 기준으로 사용하여 VM을 만들 수 있습니다. 수식을 만드는 방법은 두 가지입니다. 

* 기준에서 - 수식의 모든 특성을 정의하려는 경우에 사용합니다.
* 기존 랩 VM을 통해 - 기존 VM의 설정을 기반으로 수식을 만들려는 경우에 사용합니다.

사용자 및 사용 권한을 추가하는 방법에 대한 자세한 내용은 [Azure DevTest Labs에 소유자 및 사용자 추가](./devtest-lab-add-devtest-user.md)를 참조하세요.

### <a name="create-a-formula-from-a-base"></a>기준에서 수식 만들기
다음 단계에서는 사용자 지정 이미지, Marketplace 이미지 또는 다른 수식에서 수식을 만드는 과정을 안내합니다.

1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.

2. **모든 서비스**를 선택한 다음, 목록에서 **DevTest Labs**를 선택합니다.

3. 랩 목록에서 원하는 랩을 탭합니다.  

4. 랩의 블레이드에서 **수식(재사용 가능 기준)** 을 선택합니다.
   
    ![수식 메뉴](./media/devtest-lab-create-formulas/lab-settings-formulas.png)

5. **수식** 블레이드에서 **+ 추가**를 선택합니다.
   
    ![수식 추가](./media/devtest-lab-create-formulas/add-formula.png)

6. **기준 선택** 블레이드에서 수식을 만들 때 사용할 기준(사용자 지정 이미지, Marketplace 이미지 또는 수식)을 선택합니다.
   
    ![기본 목록](./media/devtest-lab-create-formulas/base-list.png)

7. **Create formula** (수식 만들기) 블레이드에서 다음 값을 지정합니다.
   
    * **수식 이름** - 수식의 이름을 입력합니다. 이 값은 VM을 만들 때 기본 이미지 목록에 표시됩니다. 이름 입력 시에 유효성이 검사되고, 유효하지 않으면, 유효한 이름에 필요한 사항을 알려주는 메시지가 표시됩니다.
    * **설명** - 수식에 대한 의미 있는 설명을 입력합니다. 이 값은 VM을 만들 때 수식의 상황에 맞는 메뉴에서 볼 수 있습니다.
    * **사용자 이름** - 관리자 권한이 부여된 사용자 이름을 입력합니다.
    * **암호** - 지정된 사용자에 대해 사용하려는 암호와 연결된 값을 입력하거나 드롭다운 목록에서 선택합니다. 비밀에 대한 자세한 내용은 [Azure DevTest Labs: 개인 비밀 저장소](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store/)를 참조하세요.
    * **가상 머신 디스크 유형** - HDD(하드 디스크 드라이브) 또는 SSD(반도체 드라이브)를 지정하여 이 기본 이미지를 사용하여 프로비전된 가상 머신에 허용된 저장소 디스크 유형을 나타냅니다.
    * **가상 머신 크기** - 만들려는 VM의 프로세서 코어, RAM 크기 및 하드 드라이브 크기를 지정하는 미리 정의된 항목 중 하나를 선택합니다. 
    * **아티팩트** - **아티팩트 추가** 블레이드를 선택하여 열고 여기에서 기본 이미지에 추가하려는 아티팩트를 선택하고 구성합니다. 아티팩트에 대한 자세한 내용은 [Azure DevTest Labs 가상 머신에 대한 사용자 지정 아티팩트 만들기](devtest-lab-artifact-author.md)를 참조하세요.
    * **고급 설정** - **고급** 블레이드를 선택하여 열고 다음 설정을 구성합니다.
        * **가상 네트워크** - 원하는 가상 네트워크를 선택합니다.
        * **서브넷** - 원하는 서브넷을 지정합니다.    
        * **IP 주소 구성** - 공용, 개인 또는 공유 IP 주소를 원하는 경우 지정합니다. 공유 IP 주소에 대한 자세한 내용은 [Azure DevTest Labs에서 공유 IP 주소 이해](./devtest-lab-shared-ip.md)를 참조하세요.
        * **Make this machine claimable**(이 컴퓨터를 클레임 가능하도록 지정) - 컴퓨터를 "클레임 가능"하도록 지정하는 것은 생성 시 소유권을 할당하지 않는다는 것을 의미합니다. 대신 랩 사용자가 랩의 블레이드에서 컴퓨터에 대한 소유권("클레임")을 가질 수 있습니다.     
    * **이미지** - 이 필드는 이전 블레이드에서 선택한 기본 이미지의 이름을 표시합니다. 
     
       ![Create formula](./media/devtest-lab-create-formulas/create-formula.png)

8. **만들기** 를 탭하여 수식을 만듭니다.

9. 수식이 만들어지면, **수식** 블레이드 목록에 표시됩니다.

### <a name="create-a-formula-from-a-vm"></a>VM에서 수식 만들기
다음 단계는 기존 VM을 기반으로 수식을 만드는 과정을 안내합니다. 

> [!NOTE]
> VM에서 수식을 만들려면 2016년 3월 30일 이후에 VM을 만들었어야 합니다. 
> 
> 

1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
2. **모든 서비스**를 선택한 다음, 목록에서 **DevTest Labs**를 선택합니다.
3. 랩 목록에서 원하는 랩을 탭합니다.  
4. 랩의 **개요** 블레이드에서 수식을 만들 VM을 선택합니다.
   
    ![랩 VM](./media/devtest-lab-create-formulas/my-vms.png)
5. VM 블레이드에서 **수식 만들기(재사용 가능 기준)** 를 선택합니다.
   
    ![Create formula](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. **수식 만들기** 블레이드에서 새 수식의 **이름** 및 **설명** 을 입력합니다.
   
    ![수식 만들기 블레이드](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. **확인** 을 선택하여 수식을 만듭니다.

## <a name="modify-a-formula"></a>수식 수정
수식을 수정하려면 다음 단계를 수행합니다.

1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
2. **모든 서비스**를 선택한 다음, 목록에서 **DevTest Labs**를 선택합니다.
3. 랩 목록에서 원하는 랩을 탭합니다.  
4. 랩의 블레이드에서 **수식(재사용 가능 기준)** 을 선택합니다.
   
    ![수식 메뉴](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. **Lab formulas** (랩 수식) 블레이드에서 수정할 수식을 탭합니다.
6. **수식 업데이트** 블레이드에서 원하는 내용을 편집하고 **업데이트**를 선택합니다.

## <a name="delete-a-formula"></a>수식 삭제
수식을 삭제하려면 다음 단계를 수행합니다.

1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
2. **모든 서비스**를 선택한 다음, 목록에서 **DevTest Labs**를 선택합니다.
3. 랩 목록에서 원하는 랩을 탭합니다.  
4. 랩의 **설정** 블레이드에서 **수식**을 선택합니다.
   
    ![수식 메뉴](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. **Lab formulas** (랩 수식) 블레이드에서 삭제할 수식 오른쪽의 줄임표를 클릭합니다.
   
    ![수식 메뉴](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. 수식의 상황에 맞는 메뉴에서 **삭제**를 선택합니다.
   
    ![수식 상황에 맞는 메뉴](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. 삭제 확인 대화 상자에서 **예** 를 탭합니다.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>관련 블로그 게시물
* [사용자 지정 이미지 또는 수식?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>다음 단계
VM을 만들 때 사용할 수식을 만들었으면 다음 단계는 [VM을 랩에 추가](devtest-lab-add-vm.md)하는 것입니다.

