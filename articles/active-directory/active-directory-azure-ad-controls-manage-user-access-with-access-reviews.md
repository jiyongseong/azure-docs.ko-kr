---
title: Azure AD 액세스 검토를 사용하여 사용자 액세스 관리 | Microsoft Docs
description: Azure Active Directory 액세스 검토를 사용하여 응용 프로그램에 대한 그룹 또는 할당의 멤버 자격으로 사용자 액세스를 관리하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: markwahl-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/16/2018
ms.author: billmath
ms.openlocfilehash: 2b80a09bc84166b65a731f1bd544e1cb40ff2eef
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34192492"
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Azure AD 액세스 검토를 사용하여 사용자 액세스 관리

Azure AD(Azure Active Directory)를 사용하면 사용자에게 적절한 액세스 권한이 있는지 쉽게 확인할 수 있습니다. 사용자 또는 의사 결정자에게 액세스 검토에 참여하고 사용자의 액세스를 다시 인증(또는 증명)하도록 요청할 수 있습니다. 검토자는 Azure AD의 제안 사항에 따라 지속적인 액세스에 대한 각 사용자의 요구에 입력을 제공할 수 있습니다. 액세스 검토가 완료되면 더 이상 필요하지 않은 사용자의 액세스를 변경하고 제거할 수 있습니다.

> [!NOTE]
> 게스트 사용자 액세스만 검토하고 모든 종류의 사용자 액세스는 검토하지 않으려면 [액세스 검토를 사용하여 게스트 사용자 액세스 관리](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)를 참조하세요. 전역 관리자와 같은 관리 역할에서 사용자의 멤버 자격을 검토하려면 [Azure AD Privileged Identity Management에서 액세스 검토를 시작하는 방법](active-directory-privileged-identity-management-how-to-start-security-review.md)을 참조하세요. 
>
>

## <a name="prerequisites"></a>필수 조건 


액세스 검토는 Microsoft Enterprise Mobility + Security, E5에 포함된 Azure AD의 Premium P2 버전에서 사용할 수 있습니다. 자세한 내용은 [Azure Active Directory 버전](active-directory-editions.md)을 참조하세요. 이 기능과 상호 작용하여 검토를 만들거나 검토를 채우거나 검토를 확인하는 각 사용자에게는 라이선스가 필요합니다. 

액세스 검토는 Microsoft Enterprise Mobility + Security, E5에 포함된 Azure AD의 Premium P2 버전에서 사용할 수 있습니다. 자세한 내용은 [Azure Active Directory 버전](active-directory-whatis.md)을 참조하세요. 이 기능과 상호 작용하여 검토를 만들거나 검토에 액세스하거나 검토를 적용하는 각 사용자에게는 라이선스가 필요합니다.



## <a name="create-and-perform-an-access-review"></a>액세스 검토 만들기 및 수행

액세스 검토 중인 검토자로 한 명 이상의 사용자를 포함할 수 있습니다.  

1. Azure AD에서 하나 이상의 멤버가 있는 그룹을 선택합니다. 또는 Azure AD에 연결되어 하나 이상의 사용자가 할당된 응용 프로그램을 선택합니다. 

2. 각 사용자가 자신의 액세스를 검토하도록 할지 또는 하나 이상의 사용자가 모든 사용자의 액세스를 검토하도록 할지를 결정합니다.

3. 액세스 검토가 검토자의 액세스 패널에 표시되도록 합니다. 전역 관리자로서 [액세스 검토 페이지](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)로 이동합니다.

4. 액세스 검토를 시작합니다. 자세한 내용은 [액세스 검토 만들기](active-directory-azure-ad-controls-create-access-review.md)를 참조하세요.

5. 입력을 제공하도록 검토자에게 요청합니다. 기본적으로 각 사용자는 Azure AD로부터 [자신의 액세스 검토를 수행](active-directory-azure-ad-controls-perform-access-review.md)하는 액세스 패널에 대한 링크가 포함된 전자 메일을 받습니다.

6. 검토자가 입력을 제공하지 않으면 미리 알림을 보내도록 Azure AD에 요청할 수 있습니다. Azure AD에서는 기본적으로 아직 응답하지 않은 검토자에게 종료일 중간에 미리 알림을 자동으로 보냅니다.

7. 검토자가 입력을 제공하면 액세스 검토를 중지하고 변경 내용을 적용합니다. 자세한 내용은 [액세스 검토 완료](active-directory-azure-ad-controls-complete-access-review.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계

[그룹의 멤버 또는 응용 프로그램에 액세스에 대한 액세스 검토 만들기](active-directory-azure-ad-controls-create-access-review.md)




