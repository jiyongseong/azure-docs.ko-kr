---
title: Azure Active Directory B2C 테넌트 만들기 | Microsoft Docs
description: Azure Active Directory B2C 테넌트를 만드는 방법에 대한 항목
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 06/07/2017
ms.author: davidmu
ms.openlocfilehash: 56e0ae7454e86911c894da88b5aa8ccc03a08af3
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="create-an-azure-active-directory-b2c-tenant-in-the-azure-portal"></a>Azure Portal에서 Azure Active Directory B2C 테넌트 만들기

이 Quickstart를 통해 몇 분 이내에 Microsoft Azure AD(Azure Active Directory) B2C 테넌트를 만들 수 있습니다. 완료되면 B2C 응용 프로그램 등록에 사용할 B2C 테넌트(디렉터리라고도 함)를 갖습니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C 테넌트 만들기

B2C 기능은 기존 테넌트에 사용할 수 없습니다. Azure AD B2C 테넌트를 만들어야 합니다.

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

축하합니다. Azure Active Directory B2C 테넌트를 만들었습니다. 테넌트의 전역 관리자입니다. 필요에 따라 다른 전역 관리자를 추가할 수 있습니다. 새 테넌트로 전환하려면 *새 테넌트 연결 관리*를 클릭합니다.

![새 테넌트 연결 관리](./media/active-directory-b2c-get-started/manage-new-b2c-tenant-link.png)

> [!IMPORTANT]
> 프로덕션 앱에 대해 B2C 테넌트를 사용하려는 경우 [프로덕션 규모와 B2C 테넌트 미리 보기 비교](active-directory-b2c-reference-tenant-type.md)의 문서를 참조하세요. 기존 B2C 테넌트를 삭제하고 동일한 도메인 이름으로 다시 만들어야 하는 경우 알려진 문제가 발생합니다. 다른 도메인 이름으로 B2C 테넌트를 만들어야 합니다.
>
>

## <a name="link-your-tenant-to-your-subscription"></a>구독에 테넌트 연결

모든 B2C 기능을 활성화하고 사용 요금을 지불하기 위해 Azure 구독에 Azure AD B2C 테넌트를 연결해야 합니다. 자세한 내용은 [이 문서](active-directory-b2c-how-to-enable-billing.md)를 참조하세요. Azure AD B2C 테넌트를 Azure 구독에 연결하지 않으면 일부 기능이 차단되고 B2C 설정에 경고 메시지("이 B2C 테넌트에 연결된 구독이 없거나 구독에 유의해야 합니다.")가 표시됩니다. 프로덕션에 앱을 전달하기 전에 이 단계를 수행해야 합니다.

## <a name="easy-access-to-settings"></a>설정에 대한 쉬운 액세스

[!INCLUDE [active-directory-b2c-find-service-settings](../../includes/active-directory-b2c-find-service-settings.md)]

포털 위쪽에 있는 **리소스 검색**에 `Azure AD B2C`를 입력하여 블레이드에 액세스할 수도 있습니다. 결과 목록에서 **Azure AD B2C**를 선택하여 B2C 설정 블레이드에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [B2C 테넌트에 B2C 응용 프로그램 등록](active-directory-b2c-app-registration.md)