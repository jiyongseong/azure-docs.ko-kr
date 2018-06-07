---
title: Azure API Management를 사용하여 API 버전 게시 | Microsoft Docs
description: 이 자습서의 단계에 따라 API Management에서 여러 버전을 게시하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 1cbe63184578f7d1e72992577a11c58b9b83a002
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="publish-multiple-versions-of-your-api"></a>여러 버전의 API 게시 

모든 API 호출자가 정확히 같은 버전을 사용하기가 어려운 경우가 있습니다. 최신 버전으로 업그레이드하려는 호출자는 이해하기 쉬운 방식을 사용하여 업그레이드를 수행하기를 원할 것입니다. Azure API Management에서 **버전**을 사용하면 이러한 작업을 수행할 수 있습니다. 자세한 내용은 [버전 및 수정 버전](https://blogs.msdn.microsoft.com/apimanagement/2017/09/14/versions-revisions/)을 참조하세요.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 기존 API에 새 버전 추가
> * 버전 관리 체계 선택
> * 제품에 버전 추가
> * 개발자 포털을 검색하여 버전 확인

![개발자 포털에 표시되는 버전](media/api-management-getstarted-publish-versions/azure_portal.PNG)

## <a name="prerequisites"></a>필수 조건

* 다음 빠른 시작 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 완료합니다.
* 또한, 다음 자습서 [첫 번째 API 가져오기 및 게시](import-and-publish.md)를 완료합니다.

## <a name="add-a-new-version"></a>새 버전 추가

![API 상황에 맞는 메뉴 - 버전 추가](media/api-management-getstarted-publish-versions/AddVersionMenu.png)

1. API 목록에서 **회의 API**를 선택합니다.
2. 옆에 있는 상황에 맞는 메뉴(**...**)를 선택합니다.
3. **+ 버전 추가**를 선택합니다.

    > [!TIP]
    > 새 API를 처음 만들 때 버전을 사용하도록 설정할 수도 있습니다. 이렇게 하려면 **API 추가** 화면에서 **이 API를 버전 관리하시겠습니까?** 를 선택합니다.

## <a name="choose-a-versioning-scheme"></a>버전 관리 체계 선택

Azure API Management에서는 호출자가 원하는 API 버전을 지정하도록 허용할 방식을 선택할 수 있습니다. **버전 관리 체계**를 선택하여 사용할 API 버전을 지정합니다. 이 체계는 **경로, 헤더 또는 쿼리 문자열**일 수 있습니다. 다음 예에서 경로는 버전 관리 체계를 선택하는 데 사용됩니다.

![버전 추가 화면](media/api-management-getstarted-publish-versions/AddVersion.PNG)

1. **버전 관리 체계**에서 선택된 **경로**를 그대로 유지합니다.
2. **버전 식별자**로 **v1**을 추가합니다.

    > [!TIP]
    > 버전 관리 체계로 **헤더** 또는 **쿼리 문자열**을 선택하는 경우에는 추가 값(헤더의 이름 또는 쿼리 문자열 매개 변수)을 제공해야 합니다.

3. 원하는 경우 설명을 입력합니다.
4. **만들기**를 선택하여 새 버전을 설정합니다.
5. 이제 API 목록의 **Big Conference API** 아래에 고유 API 두 개(**원본** 및 **v1**)가 표시됩니다.

    ![Azure Portal에서 API 아래에 나열된 버전](media/api-management-getstarted-publish-versions/VersionList.PNG)

    > [!Note]
    > 버전이 없는 API에 버전을 추가하는 경우에는 기본 URL에서 응답하는 **원본**이 자동으로 만들어집니다. 따라서 버전 추가 프로세스로 인해 기존 호출자의 호출이 중단되지 않습니다. 시작 시 버전을 사용하도록 설정하여 새 API를 만들면 원본이 작성되지 않습니다.

6. 이제 **원본**과는 별개의 API로 **v1**을 편집하고 구성할 수 있습니다. 즉, 버전 하나를 변경해도 다른 버전에는 영향을 주지 않습니다.

## <a name="add-the-version-to-a-product"></a>제품에 버전 추가

호출자가 새 버전을 보게 하려면 **제품**에 추가해야 합니다.

1. 클래식 배포 모델 페이지에서 **제품**을 선택합니다.
2. **무제한**을 선택합니다.
3. **API**를 선택합니다.
4. **추가**를 선택합니다.
5. **Conference API, 버전 v1**을 선택합니다.
6. 서비스 관리 페이지로 이동하여 **API**를 선택합니다.

## <a name="browse-the-developer-portal-to-see-the-version"></a>개발자 포털을 검색하여 버전 확인

1. 상단 메뉴에서 **개발자 포털**을 선택합니다.
2. **API**를 선택합니다. **Conference API**에 **원본** 및 **v1** 버전이 표시됩니다.
3. **v1**을 선택합니다.
4. 목록에서 첫 번째 작업의 **요청 URL**을 확인합니다. API URL 경로에 **v1**이 포함되어 있습니다.

    ![API 상황에 맞는 메뉴 - 버전 추가](media/api-management-getstarted-publish-versions/developer_portal.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 기존 API에 새 버전 추가
> * 버전 관리 체계 선택 
> * 제품에 버전 추가
> * 개발자 포털을 검색하여 버전 확인

다음 자습서를 진행합니다.

> [!div class="nextstepaction"]
> [업그레이드 및 비율 크기 조정](upgrade-and-scale.md)