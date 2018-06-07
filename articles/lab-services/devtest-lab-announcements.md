---
title: Azure DevTest Labs에서 랩에 공지 게시 | Microsoft Docs
description: Azure DevTest Labs에서 랩에 공지를 추가하는 방법을 알아봅니다.
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 67a09946-4584-425e-a94c-abe57c9cbb82
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 3282a90069ecd119154df492ac6dc366d26b5300
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="post-an-announcement-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs에서 랩에 공지 게시

랩 관리자는 기존 랩에 사용자 지정 공지를 게시하여 랩에 대한 최근 변경 내용 또는 추가 내용을 사용자에게 알릴 수 있습니다. 예를 들어 다음에 대해 사용자에게 알릴 수 있습니다.

- 사용할 수 있는 새 VM 크기
- 현재 사용할 수 없는 이미지
- 랩 정책에 대한 업데이트

일단 게시되면 랩의 개요 페이지에 공지가 표시되고 사용자는 해당 공지를 선택하여 자세한 내용을 확인할 수 있습니다.

이 공지 기능은 임시 알림에 사용해야 합니다.  더 이상 필요 없는 공지는 쉽게 사용하지 않도록 설정할 수 있습니다.

## <a name="steps-to-post-an-announcement-in-an-existing-lab"></a>기존 랩에 공지를 게시하는 단계

1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
1. 필요한 경우 **모든 서비스**를 선택하고 목록에서 **DevTest Labs**를 선택합니다. 대시보드의 **모든 리소스** 아래에 랩이 이미 표시되어 있을 수 있습니다.
1. 랩 목록에서 공지를 게시하려는 랩을 선택합니다.  
1. 랩의 **개요** 영역에서 **구성 및 정책**을 선택합니다.  

    ![구성 및 정책 단추](./media/devtest-lab-announcements/devtestlab-config-and-policies.png)

1. **설정** 아래의 왼쪽에서 **랩 공지**를 선택합니다.

    ![랩 공지 단추](./media/devtest-lab-announcements/devtestlab-announcements.png)

1. 이 랩에서 사용자를 위한 메시지를 만들려면 **사용**을 **예**로 설정합니다.

1. **만료 날짜**를 입력하여 공지가 사용자에게 더 이상 표시되지 않는 이후 시간 및 날짜를 지정할 수 있습니다. 만료 날짜를 입력하지 않으면 사용하지 않을 때까지 공지가 유지됩니다.

   > [!NOTE]
   > 공지가 만료된 후에 사용자에게는 더 이상 표시되지 않지만 여전히 **랩 공지** 창에 존재합니다. 이를 편집하고 재사용하여 다시 활성화할 수 있습니다.
   >
   >

1. **공지 제목** 및 **공지 텍스트**를 입력합니다.

   제목은 100자까지 가능하고 랩의 개요 페이지에 표시됩니다. 사용자가 제목을 선택하면 공지 텍스트가 표시됩니다.

   공지 텍스트는 마크다운을 허용합니다. 공지 텍스트를 입력하면 화면 맨 아래의 미리 보기 영역에서 메시지를 볼 수 있습니다.

    ![메시지를 만들 랩 공지 화면.](./media/devtest-lab-announcements/devtestlab-post-announcement.png)


1. 공지를 게시할 준비가 되면 **저장**을 선택합니다.

이 공지를 랩 사용자에게 더 이상 표시하지 않으려는 경우 **랩 공지** 페이지로 돌아가서 **사용**을 **아니요**로 설정합니다. 만료 날짜를 지정하는 경우 공지는 해당 날짜 및 시간에 자동으로 비활성화됩니다.

## <a name="steps-for-users-to-view-an-announcement"></a>알림을 보기 위한 단계

1. [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040)에서 랩을 선택합니다.

1. 랩에 게시된 공지가 있으면 랩 개요 페이지 위쪽에 정보 알림이 표시됩니다. 이 정보 공지는 공지를 만들었을 때 지정된 공지 제목입니다.

    ![개요 페이지의 랩 공지](./media/devtest-lab-announcements/devtestlab-user-announcement.png)

1. 메시지를 선택하여 전체 공지를 볼 수 있습니다.

    ![랩 공지에 대한 자세한 정보](./media/devtest-lab-announcements/devtestlab-user-announcement-text.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>다음 단계
* 랩 정책을 변경하거나 설정하는 경우 사용자에게 알리기 위해 공지를 게시할 수 있습니다. [정책 및 일정 설정](devtest-lab-set-lab-policy.md)은 사용자 지정된 정책을 사용하여 구독에 제한 사항 및 규칙을 적용하는 방법에 대한 정보를 제공합니다.
* [DevTest Labs Azure Resource Manager 빠른 시작 템플릿 갤러리](https://github.com/Azure/azure-devtestlab/tree/master/Samples)를 탐색합니다.
