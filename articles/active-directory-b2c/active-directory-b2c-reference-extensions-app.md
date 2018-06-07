---
title: 확장 앱 - Azure AD B2C | Microsoft Docs
description: b2c-extensions-app 복원
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 9/06/2017
ms.author: davidmu
ms.openlocfilehash: c07aba797118af2cc8283509944eda8b41d499b3
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C: 확장 앱

Azure AD B2C 디렉터리를 만든 경우 `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.`라는 앱이 새 디렉터리 안에 자동으로 만들어집니다. 따라서 이 앱은 **b2c-extensions-app**이라고 하며 *앱 등록*에 표시됩니다. 이 앱은 사용자 및 사용자 지정 특성에 대한 정보를 저장하기 위해 Azure AD B2C 서비스에 의해 사용됩니다. 앱을 삭제하는 경우 Azure AD B2C가 제대로 작동하지 않으며 프로덕션 환경에 영향을 주게 됩니다.

> [!IMPORTANT]
> 테넌트를 즉시 삭제할 계획이 아니라면 b2c-extensions-app을 삭제하지 마세요. 앱이 30일 넘도록 삭제된 상태로 유지되면 사용자 정보는 영구적으로 손실됩니다.

## <a name="verifying-that-the-extensions-app-is-present"></a>확장 앱이 있는지 확인

b2c-extensions-app이 있는지 확인하려면

1. Azure AD B2C 테넌트 내의 왼쪽 탐색 메뉴에서 **모든 서비스**를 클릭합니다.
1. **앱 등록**을 검색하여 엽니다.
1. **b2c-extensions-app**으로 시작하는 앱을 찾습니다.

## <a name="recover-the-extensions-app"></a>확장 앱 복구

b2c-extensions-app을 실수로 삭제한 경우 30일 이내에는 복구할 수 있습니다. Graph API를 사용하여 앱을 복원할 수 있습니다.

1. [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/)으로 이동합니다.
1. 삭제된 앱을 복원하려는 Azure AD B2C 디렉터리에 대한 전역 관리자로 사이트에 로그인합니다. 이 전역 관리자는 다음과 비슷한 이메일 주소가 있어야 합니다. `username@{yourTenant}.onmicrosoft.com`
1. api-version=1.6인 URL `https://graph.windows.net/myorganization/deletedApplications`에 대해 HTTP GET을 실행합니다. 이 작업은 지난 30일 내에서 삭제된 모든 응용 프로그램을 나열합니다.
1. 목록에서 이름이 ‘b2c-extension-app’으로 시작하는 응용 프로그램을 찾고 해당 `objectid` 속성 값을 복사합니다.
1. URL `https://graph.windows.net/myorganization/deletedApplications/{OBJECTID}/restore`에 대해 HTTP POST를 실행합니다. URL의 `{OBJECTID}` 부분을 이전 단계의 `objectid`로 바꿉니다. 

이제 Azure Portal에서 [복원된 앱을 볼 수 있습니다](#verifying-that-the-extensions-app-is-present).

> [!NOTE]
> 응용 프로그램이 지난 30일 이내 삭제된 경우에만 복원 가능합니다. 30일이 지난 경우 데이터는 영구적으로 손실됩니다. 추가 지원이 필요한 경우 지원 티켓을 파일로 저장합니다.