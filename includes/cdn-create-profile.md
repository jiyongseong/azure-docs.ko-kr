---
title: 포함 파일
description: 포함 파일
services: cdn
author: SyntaxC4
ms.service: cdn
ms.topic: include
ms.date: 04/13/2018
ms.author: cfowler
ms.custom: include file
ms.openlocfilehash: 0db5f571324694f0518ffc4e92af985e5125d755
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
## <a name="create-a-new-cdn-profile"></a>새 CDN 프로필 만들기

CDN 프로필은 CDN 끝점에 대한 컨테이너이며 가격 책정 계층을 지정합니다.

1. Azure Portal의 왼쪽 위에서 **리소스 만들기**를 선택합니다.
    
    **새로 만들기** 창이 나타납니다.
   
2. **웹 + 모바일**을 선택한 후 **CDN**을 선택합니다.
   
    ![CDN 리소스 선택](./media/cdn-create-profile/cdn-new-resource.png)

    **CDN 프로필** 창이 나타납니다.

    이미지를 따라 표에 지정된 설정을 사용합니다.
   
    ![새 CDN 프로필](./media/cdn-create-profile/cdn-new-profile.png)

    | 설정  | 값 |
    | -------- | ----- |
    | **Name** | 프로필 이름에 *my-cdn-profile-123*을 입력합니다. 이 이름은 전역적으로 고유해야 합니다. 이미 사용 중인 경우 다른 이름을 입력합니다. |
    | **구독** | 드롭다운 목록에서 Azure 구독을 선택합니다.|
    | **리소스 그룹** | **새로 만들기**를 선택하고, 리소스 그룹 이름에 *my-resource-group-123*을 입력합니다. 이 이름은 전역적으로 고유해야 합니다. 이미 사용 중인 경우 다른 이름을 입력합니다. | 
    | **리소스 그룹 위치** | 드롭다운 목록에서 **미국 중부**를 선택합니다. |
    | **가격 책정 계층** | 드롭다운 목록에서 **Standard Verizon**을 선택합니다. |
    | **지금 새 CDN 끝점 만들기** | 선택하지 않고 그대로 둡니다. |  
   
3. **대시보드에 고정**을 선택하여 프로필을 만든 후 대시보드에 저장합니다.
    
4. **만들기**를 선택하여 프로필을 만듭니다. 

