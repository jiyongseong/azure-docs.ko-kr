---
title: Azure API Management에서 수정 버전을 사용하여 작업을 중단하지 않는 변경을 안전하게 수행 | Microsoft Docs
description: 이 자습서의 단계에 따라 API Management에서 수정 버전을 사용하여 작업을 중단하지 않는 변경을 수행하는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 864c269da61bcea885249021a44fe0259ca83e90
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="use-revisions-to-make-non-breaking-changes-safely"></a>수정 버전을 사용하여 작업을 중단하지 않는 변경을 안전하게 수행
API가 준비되어 개발자들이 사용하기 시작하면 대개 API 호출자의 작업을 중단하지 않으면서 해당 API를 변경해야 합니다. 이렇게 하면 개발자에게 변경 내용을 알릴 때도 유용합니다. Azure API Management에서 **수정 버전**을 사용하면 이러한 작업을 수행할 수 있습니다. 자세한 내용은 [버전 및 수정 버전](https://blogs.msdn.microsoft.com/apimanagement/2017/09/14/versions-revisions/) 및 [Azure API Management를 사용한 API 버전 관리](https://blogs.msdn.microsoft.com/apimanagement/2017/09/13/api-versioning-with-azure-api-management/)를 참조하세요.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 새 수정 버전 추가
> * 작업을 중단하지 않는 방식으로 수정 버전 변경
> * 수정 버전을 현재 항목으로 설정하고 변경 로그 항목 추가
> * 개발자 포털로 이동하여 변경 내용과 변경 로그 확인

![개발자 포털에서 로그온 변경](media/api-management-getstarted-revise-api/azure_portal.PNG)

## <a name="prerequisites"></a>필수 조건

+ 다음 빠른 시작 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 완료합니다.
+ 또한, 다음 자습서 [첫 번째 API 가져오기 및 게시](import-and-publish.md)를 완료합니다.

## <a name="add-a-new-revision"></a>새 수정 버전 추가

1. **API** 페이지를 선택합니다.
2. API 목록에서 **회의 API**(또는 수정 버전을 추가하려는 다른 API)를 선택합니다.
3. 페이지 상단 근처의 메뉴에서 **수정 버전** 탭을 클릭합니다.
4. **+ 수정 버전 추가**를 선택합니다.

    > [!TIP]
    > API의 상황에 맞는 메뉴(**...**)에서 **수정 버전 추가**를 선택할 수도 있습니다.
    
    ![화면 상단 근처의 수정 버전 메뉴](media/api-management-getstarted-revise-api/TopMenu.PNG)

5. 새 수정 버전의 용도를 쉽게 기억할 수 있도록 해당 버전의 설명을 입력합니다.
6. **만들기**
7. 이제 새 수정 버전이 작성되었습니다.

    > [!NOTE]
    > 원본 API는 **수정 버전 1**로 유지됩니다. 다른 수정 버전을 현재 항목으로 설정할 때까지는 사용자가 이 수정 버전을 계속 호출합니다.

## <a name="make-non-breaking-changes-to-your-revision"></a>작업을 중단하지 않는 방식으로 수정 버전 변경

1. API 목록에서 **회의 API**를 선택합니다.
2. 화면 상단 근처에서 **디자인** 탭을 선택합니다.
3. 디자인 탭 바로 위의 **수정 버전 선택기**에 현재 수정 버전이 **수정 버전 2**로 표시됩니다.

    > [!TIP]
    > 수정 버전 선택기를 사용하여 작업하려는 수정 버전 간을 전환합니다.

4. **+ 작업 추가**를 선택합니다.
5. 새 작업을 **POST**로 설정하고 작업의 이름 및 표시 이름을 **테스트**로 설정합니다.
6. 새 작업을 **저장**합니다.
7. 이제 **수정 버전 2**가 변경되었습니다. 페이지 상단 근처의 **수정 버전 선택기**를 사용하여 **수정 버전 1**로 다시 전환합니다.
8. **수정 버전 1**에는 새 작업이 표시되지 않습니다. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>수정 버전을 현재 항목으로 설정하고 변경 로그 항목 추가
1. 페이지 상단 근처의 메뉴에서 **수정 버전** 탭을 선택합니다.

    ![수정 버전 화면의 수정 버전 메뉴](media/api-management-getstarted-revise-api/RevisionsMenu.PNG)
1. **수정 버전 2**의 상황에 맞는 메뉴(**...**)를 엽니다.
2. **현재로 설정**을 선택합니다. 이 변경 내용에 대한 정보를 게시하려는 경우 **이 API에 대한 공용 변경 로그에 게시**를 선택합니다.
3. **이 API에 대한 공용 변경 로그에 게시**를 선택합니다.
4. **수정 버전을 테스트하는 중입니다. 세 “테스트” 작업을 추가했습니다.** 와 같이 개발자에게 표시할 변경 내용에 대한 설명을 입력합니다.
5. 이제 **수정 버전 2**가 현재 항목으로 설정되었습니다.

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>개발자 포털로 이동하여 변경 내용과 변경 로그 확인
1. Azure Portal에서 **API**를 선택합니다.
2. 상단 메뉴에서 **개발자 포털**을 선택합니다.
3. **API**, **Conference API**를 차례로 선택합니다.
4. 이제 새 **테스트** 작업을 사용할 수 있습니다.
5. API 이름 아래에서 **API 변경 기록**을 선택합니다.
6. 이 목록에 변경 로그 항목이 표시됩니다.

    ![개발자 포털](media/api-management-getstarted-revise-api/developer_portal.PNG)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 새 수정 버전 추가
> * 작업을 중단하지 않는 방식으로 수정 버전 변경
> * 수정 버전을 현재 항목으로 설정하고 변경 로그 항목 추가
> * 개발자 포털로 이동하여 변경 내용과 변경 로그 확인

다음 자습서를 진행합니다.

> [!div class="nextstepaction"]
> [여러 버전의 API 게시](api-management-get-started-publish-versions.md)