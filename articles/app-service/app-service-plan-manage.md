---
title: "Azure에서 App Service 계획 관리 | Microsoft Docs"
description: "다양한 작업을 수행하여 App Service 계획을 관리하는 방법을 알아봅니다."
keywords: "App Service, Azure App Service, 규모, App Service 계획, 변경, 만들기, 관리, 운영"
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cephalin
ms.openlocfilehash: 1dfe8a903e19ff524a1c4a0228e6aefcbe9ff183
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2018
---
# <a name="manage-an-app-service-plan-in-azure"></a>Azure에서 App Service 계획 관리

[Azure App Service 계획](azure-web-sites-web-hosting-plans-in-depth-overview.md)은 App Service 앱이 실행해야 하는 리소스를 제공합니다. 이 가이드에서는 App Service 계획의 관리 방법을 보여 줍니다.

## <a name="create-an-app-service-plan"></a>App Service 계획 만들기

> [!TIP]
> App Service 환경이 있는 경우 [App Service 환경에서 App Service 계획 만들기](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan)를 참조하세요.

빈 App Service 계획을 만들거나 앱 생성의 일부로 계획을 만들 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 **새로 만들기** > **웹+모바일**을 선택하고 **웹앱** 또는 다른 App Service 앱 종류를 선택합니다.

2. 기존 App Service 계획을 선택하거나 새 앱에 대한 계획을 만듭니다.

   ![Azure Portal에서 앱을 만듭니다.][createWebApp]

   계획을 만들려면

   a. **[+] 새로 만들기**를 선택합니다.

      ![App Service 계획을 만듭니다.][createASP] 

   나. **App Service 계획**에서 계획의 이름을 입력합니다.

   다. **위치**에서 적절한 위치를 선택합니다.

   d. **가격 책정 계층**에서 서비스에 대한 적절한 가격 책정 계층을 선택합니다. **무료** 및 **공유** 등의 더 많은 가격 책정 옵션을 보려면 **모두 보기**를 선택합니다. 가격 책정 계층을 선택한 다음 **선택** 단추를 클릭합니다.

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>다른 App Service 계획으로 앱 이동

원본 계획과 대상 계획이 _동일한 리소스 그룹 및 지리적 지역_에 있으면 다른 App Service 계획으로 앱을 이동할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 이동하려는 앱을 찾습니다.

2. 메뉴에서 **App Service 계획** 섹션을 찾아봅니다.

3. **App Service 계획 변경**을 선택하면 **App Service 계획** 선택기가 열립니다.

   ![App Service 계획 선택기입니다.][change] 

4. **App Service 계획** 선택기에서 이 앱을 이동할 기존 계획을 선택합니다.   

> [!IMPORTANT]
> **App Service 계획 선택** 페이지는 다음 기준으로 필터링됩니다. 
> - 동일한 리소스 그룹에 존재 
> - 동일한 지리적 지역에 존재 
> - 동일한 웹 공간에 존재  
> 
> _웹 공간_은 서버 리소스의 그룹화를 정의하는 App Service의 논리적 구문입니다. 지리적 지역(예: 미국 서부)에는 App Service를 사용하는 고객을 할당하기 위한 여러 웹 공간이 포함되어 있습니다. 현재는 웹 공간 간에 App Service 리소스를 이동할 수 없습니다. 
> 

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

각 계획에는 고유한 가격 책정 계층이 있습니다. 예를 들어 **무료** 계층에서 **표준** 계층으로 사이트를 이동하면 **표준** 계층에 할당된 모든 앱이 해당 계층의 기능과 리소스를 사용할 수 있습니다. 그러나 더 높은 계층의 계획에서 낮은 계층의 계획으로 앱을 이동하면 특정 기능에는 더 이상 액세스할 수 없게 됩니다. 앱이 대상 계획에서 사용할 수 없는 기능을 사용하고 있는 경우, 사용할 수 없는 기능을 사용한다는 오류가 표시됩니다. 

예를 들어, 앱 중 하나가 SSL 인증서를 사용하는 경우 다음 오류 메시지가 표시될 수 있습니다.

`Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

이 경우 앱을 대상 계획에 이동하기 전에 다음을 수행해야 합니다.
- 대상 계획의 가격 책정 계층을 **기본** 이상으로 강화합니다.
- 앱에 대한 모든 SSL 연결을 제거합니다.

## <a name="move-an-app-to-a-different-region"></a>다른 영역으로 앱 이동

앱이 실행되는 영역은 App Service 계획이 있는 영역입니다. 그러나 App Service 계획의 영역은 변경할 수 없습니다. 앱을 다른 영역으로 이동하는 한 가지 대안은 앱 복제입니다. 복제를 수행하면 모든 하위 지역의 새로운 또는 기존 App Service 계획으로 앱이 복사됩니다.

메뉴의 **개발 도구** 섹션에서 **앱 복제**를 찾을 수 있습니다.

> [!IMPORTANT]
> 복제에는 몇 가지 제한 사항이 있습니다. [Azure App Service 앱 복제](app-service-web-app-cloning.md)에서 이에 대한 내용을 확인할 수 있습니다.

## <a name="scale-an-app-service-plan"></a>App Service 계획 크기 조정

App Service 계획의 가격 책정 계층을 강화하려면 [Azure에서 앱 강화](web-sites-scale.md)를 참조하세요.

앱 인스턴스 수를 규모 확장하려면 [수동 또는 자동으로 인스턴스 개수 조정](../monitoring-and-diagnostics/insights-how-to-scale.md)을 참조하세요.

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>App Service 계획 삭제

예기치 않은 요금 청구를 방지하기 위해 App Service 계획에서 마지막 앱을 삭제하면 App Service도 기본적으로 해당 계획을 삭제합니다. 그 대신 계획을 유지하려는 경우 계획을 **무료** 계층으로 변경하면 요금이 청구되지 않습니다.

> [!IMPORTANT]
> 연결된 앱이 없는 App Service 계획이더라도 구성된 VM 인스턴스가 지속적으로 유지되기 때문에 요금이 부과됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure에서 앱 강화](web-sites-scale.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
