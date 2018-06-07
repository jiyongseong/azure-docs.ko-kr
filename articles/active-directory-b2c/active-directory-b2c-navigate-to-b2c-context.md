---
title: 'Azure Active Directory B2C: B2C 테넌트로 전환 | Microsoft Docs'
description: Active Directory B2C 테넌트의 컨텍스트로 전환하는 방법
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 4/13/2017
ms.author: davidmu
ms.openlocfilehash: 6469636ee05c2b28a24b1d559f0ee7569e784393
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="switching-to-your-azure-ad-b2c-tenant"></a>Azure AD B2C 테넌트로 전환

Azure AD B2C을 구성하려면 Azure AD B2C 테넌트의 컨텍스트에 있어야 합니다.

## <a name="log-into-azure-ad-b2c-tenant"></a>Azure AD B2C 테넌트에 로그인

Azure AD B2C 테넌트를 탐색하려면 Azure AD B2C 테넌트의 전역 관리자로 Azure Portal에 로그인해야 합니다.

1. [Azure 포털](http://portal.azure.com)에 로그인합니다.
1. 전자 메일 주소 또는 오른쪽 위 모서리의 그림을 클릭하여 테넌트를 전환합니다.
1. 표시되는 `Directory` 목록에서, 관리하려는 Azure AD B2C 테넌트를 선택합니다.

Azure Portal이 새로 고침됩니다.  이제 Azure AD B2C 테넌트의 컨텍스트에서 Azure Portal에 로그인되었습니다.

## <a name="navigate-to-the-b2c-features-pane"></a>B2C 기능 창으로 이동

1. 왼쪽 탐색 창에서 **찾아보기**를 클릭합니다.
1. 왼쪽 탐색 창에서 **모든 서비스**를 클릭하고 `Azure AD B2C`를 검색합니다.  (왼쪽 시작 보드에 고정하려면 Azure AD B2C 왼쪽에 있는 별을 클릭)
1. B2C 기능 창에 액세스하려면 **Azure AD B2C**를 클릭합니다.
   
    ![B2C 기능 창으로 이동 스크린샷](./media/active-directory-b2c-get-started/b2c-browse.png)

> [!IMPORTANT]
> B2C 기능 창에 액세스하려면 B2C 테넌트의 전역 관리자가 되어야 합니다. 다른 테넌트의 전역 관리자 또는 사용자는 액세스할 수 없습니다.  Azure Portal의 오른쪽 위 모서리에 있는 테넌트 전환기를 사용하여 B2C 테넌트로 전환할 수 있습니다.
