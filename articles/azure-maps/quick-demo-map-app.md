---
title: Azure Maps를 사용한 대화형 지도 검색 | Microsoft Docs
description: Azure 빠른 시작 - Azure Maps를 사용하여 데모 대화형 맵 검색 시작
services: azure-maps
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: quickstart
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 8dedaf95289d9637f5f3d1e80a763b5fb400c617
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="launch-an-interactive-search-map-using-azure-maps"></a>Azure Maps를 사용하여 대화형 검색 맵 시작

이 문서에서는 사용자에게 대화형 검색 환경을 제공하는 지도를 만드는 Azure Maps의 기능을 보여 줍니다. 자체 Maps 계정을 만들고 데모 웹 응용 프로그램에 사용할 계정 키를 가져오는 방법도 안내합니다. 

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.


## <a name="log-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-an-account-and-get-your-key"></a>계정 생성 및 키 가져오기

1. [Azure Portal](https://portal.azure.com)의 왼쪽 위 모서리에서 **리소스 만들기**를 클릭합니다.
2. *마켓플레이스 검색* 상자에서 **Maps**를 입력합니다.
3. *결과*에서 **Maps**를 선택합니다. 맵 아래에 나타나는 **만들기** 단추를 클릭합니다. 
4. **Maps 계정 만들기** 페이지에서 다음 값을 입력합니다.
    - 새 계정의 *이름*. 
    - 이 계정에 사용하려는 *구독*.
    - 이 계정에 대한 *리소스 그룹*. *새로 만들기* 또는 *기존* 리소스 그룹 사용을 선택할 수도 있습니다.
    - *리소스 그룹 위치*를 선택합니다.
    - *라이선스* 및 *개인정보처리방침*을 읽고 조건에 동의하는 확인란을 선택합니다. 
    - 마지막으로 **만들기** 단추를 클릭합니다.

    ![포털에서 Maps 계정 만들기](./media/quick-demo-map-app/create-account.png)

5. 계정을 만든 후에는 해당 계정을 열고 계정 메뉴의 설정 섹션을 찾습니다. **키**를 클릭하여 Azure Maps 계정에 대한 기본 및 보조 키를 확인합니다. **기본 키** 값은 로컬 클립보드에 복사하여 다음 섹션에서 사용합니다. 

## <a name="download-the-application"></a>응용 프로그램 다운로드

1. [interactiveSearch.html](https://github.com/Azure-Samples/azure-maps-samples/blob/master/src/interactiveSearch.html) 파일의 콘텐츠를 다운로드하거나 복사합니다.
2. 이 파일의 콘텐츠를 **AzureMapDemo.html**에 로컬로 저장하고 텍스트 편집기에서 엽니다.
3. `<insert-key>` 문자열을 검색하여 이전 섹션에서 구한 **기본 키** 값으로 바꿉니다. 


## <a name="launch-the-application"></a>응용 프로그램 시작

1. 원하는 브라우저에서 **AzureMapDemo.html** 파일을 엽니다.
2. 로스앤젤레스의 지도를 관찰합니다. 확대 및 축소하여 확대/축소 수준에 따라 지도가 더 많거나 더 적은 정보로 자동으로 렌더링되도록 하는 방법을 확인합니다. 
3. 지도의 기본 센터를 변경합니다. **AzureMapDemo.html** 파일에서 **center**라는 변수를 검색합니다. 이 변수에 대한 위도, 경도 쌍 값을 새 값인 **[-74.0060, 40.7128]** 로 바꿉니다. 파일을 저장하고 브라우저를 새로 고칩니다. 
3. 대화형 검색 환경을 사용해 봅니다. 데모 웹 응용 프로그램의 왼쪽 위 모서리에 있는 검색 상자에서 **식당**을 검색합니다. 
4. 검색 상자 아래 표시되는 주소/위치 목록 위에 마우스를 가져가 지도 위의 해당 핀에 해당 위치에 관한 정보가 어떻게 팝업 표시되는지 확인합니다. 개인 회사의 정보 보호를 위해 가상의 이름과 주소가 표시됩니다. 

    ![대화형 검색 웹 응용 프로그램](./media/quick-demo-map-app/interactive-search.png)


## <a name="clean-up-resources"></a>리소스 정리

이 자습서는 Maps를 사용하고 사용자 계정으로 구성하는 방법을 자세히 설명합니다. 자습서를 계속 사용하려면 이 빠른 시작에서 만든 리소스를 정리하지 마세요. 계속하지 않으려는 경우 다음 단계를 사용하여 이 빠른 시작에서 만든 모든 리소스를 삭제합니다.

1. **AzureMapDemo.html** 웹 응용 프로그램을 실행 중인 브라우저를 닫습니다.
2. Azure Portal의 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음 해당 Maps 계정을 선택합니다. **모든 리소스** 블레이드 위쪽에서 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Maps 계정을 만들고 데모 앱을 시작했습니다. Maps API를 사용하여 자체 응용 프로그램을 만드는 방법은 다음 자습서에서 알아보세요.

> [!div class="nextstepaction"]
> [Maps를 사용하여 관심 지점 검색](./tutorial-search-location.md)
