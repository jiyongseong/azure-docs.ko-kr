---
title: 초대 없이 Azure Active Directory에 B2B 공동 작업 사용자 추가 | Microsoft Docs
description: 게스트 사용자가 Azure Active Directory B2B 공동 작업에서 초대를 사용하지 않고 Azure AD에 다른 게스트 사용자를 추가하도록 할 수 있습니다.
services: active-directory
documentationcenter: ''
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 589d9a417dae5c40d24d25c4ef864ce903fbfbe3
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259580"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation"></a>초대 없이 B2B 공동 작업 게스트 사용자 추가

> [!NOTE]
> 이제 게스트 사용자에게는 특별한 경우를 제외하고 초대 이메일이 필요하지 않습니다. 자세한 내용은 [B2B 공동 작업 초대 상환](redemption-experience.md)을 참조하세요.  

파트너 담당자와 같은 사용자가 초대를 사용하지 않고 파트너의 사용자를 조직에 추가하도록 허용할 수 있습니다. 파트너 조직에 사용 중인 디렉터리에서 해당 사용자에게 열거형 권한을 부여하기만 하면 됩니다. 

다음과 같은 경우에 이러한 권한을 부여합니다.

1. 호스트 조직(예: WoodGrove)의 한 사용자가 파트너 조직(예: Sam@litware.com)의 한 사용자를 게스트로 초대합니다.
2. 호스트 조직의 관리자는 Sam이 파트너 조직(Litware)의 다른 사용자를 식별하고 추가할 수 있도록 하는 [정책을 설정](delegate-invitations.md)합니다.
3. 이제 Sam은 초대를 돌려받지 않아도 Litware의 다른 사용자를 WoodGrove 디렉터리, 그룹 또는 응용 프로그램에 추가할 수 있습니다. Sam에게 Litware의 적절한 열거 권한이 있으면 이러한 작업이 자동으로 수행됩니다.

### <a name="next-steps"></a>다음 단계

- [Azure AD B2B 공동 작업이란?](what-is-b2b.md)
- [정보 근로자가 B2B 공동 작업 사용자를 추가하는 방법](add-users-information-worker.md)
- [B2B 공동 작업 초대 상환](redemption-experience.md)
- [Azure Active Directory B2B 공동 작업에 대한 초대 위임](delegate-invitations.md)

