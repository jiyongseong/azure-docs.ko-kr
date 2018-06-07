---
title: Azure AD 액세스 검토를 사용하여 게스트 액세스 관리 | Microsoft Docs
description: Azure Active Directory 액세스 검토를 사용하여 게스트 사용자를 그룹 구성원으로 또는 응용 프로그램에 할당된 사용자로 관리합니다.
services: active-directory
documentationcenter: ''
author: markwahl-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: billmath
ms.openlocfilehash: a96e856b55f118b74c7cb43ead925e10e4b74f1c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34192441"
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Azure AD 액세스 검토를 사용하여 게스트 액세스 관리


Azure AD(Azure Active Directory)를 사용하면 [Azure AD B2B 기능](active-directory-b2b-what-is-azure-ad-b2b.md)을 통해 조직의 경계를 넘나들며 공동 작업을 쉽게 수행할 수 있습니다. 다른 테넌트의 게스트 사용자는 [관리자](active-directory-b2b-admin-add-users.md) 또는 [다른 사용자](active-directory-b2b-how-it-works.md)가 초대할 수 있습니다. 이 기능은 Microsoft 계정과 같은 소셜 ID에도 적용됩니다.

또한 게스트 사용자에게 적절한 액세스 권한을 쉽게 부여할 수도 있습니다. 게스트 본인 또는 의사 결정자에게 액세스 검토에 참여하고 게스트 액세스 권한을 다시 인증(또는 증명)하도록 요청할 수 있습니다. 검토자는 Azure AD에서의 제안 사항을 기반으로 각 사용자의 지속적인 액세스 요구 사항에 대한 입력을 제공할 수 있습니다. 액세스 검토가 완료되면 변경 사항을 적용하거나 액세스가 더 이상 필요 없는 게스트의 액세스를 제거할 수 있습니다.

> [!NOTE]
> 이 문서에서는 게스트 사용자의 액세스 검토에 중점을 둡니다. 게스트 뿐 아니라 모든 사용자의 액세스를 검토하려는 경우 [액세스 검토를 사용하여 사용자 액세스 관리](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)를 참조하세요. 전역 관리자와 같은 관리 역할의 사용자의 구성원 자격을 검토하려면 [Azure AD Privileged Identity Management에서 액세스 검토를 시작하는 방법](active-directory-privileged-identity-management-how-to-start-security-review.md)을 참조하세요. 
>
>

## <a name="prerequisites"></a>필수 조건 


액세스 검토는 Microsoft Enterprise Mobility + Security, E5에 포함된 Azure AD의 Premium P2 버전에서 사용할 수 있습니다. 자세한 내용은 [Azure Active Directory 버전](active-directory-editions.md)을 참조하세요. 이 기능과 상호 작용하여 검토를 만들거나 검토를 채우거나 검토를 확인하는 각 사용자에게는 라이선스가 필요합니다. 

액세스 검토는 Microsoft Enterprise Mobility + Security, E5에 포함된 Azure AD의 Premium P2 버전에서 사용할 수 있습니다. 자세한 내용은 [Azure Active Directory 버전](active-directory-whatis.md)을 참조하세요. 이 기능과 상호 작용하여 검토를 만들거나 검토에 액세스하거나 검토를 적용하는 각 사용자에게는 라이선스가 필요합니다.


게스트 사용자에게 자신의 액세스 검토를 요청하려는 경우 게스트 사용자 라이선스에 대해 알아보세요. 자세한 내용은 [Azure AD B2B 공동 작업 라이선스](active-directory-b2b-licensing.md)를 참조하세요.

## <a name="create-and-perform-an-access-review-for-guests"></a>게스트에 대한 액세스 검토 만들기 및 수행

먼저 검토자의 액세스 패널에 표시되도록 액세스 검토를 활성화합니다. 전역 관리자로서 [액세스 검토 페이지](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)로 이동합니다. 

Azure AD는 게스트 사용자를 검토하기 위한 몇 가지 시나리오를 지원합니다.

다음 중 하나를 선택합니다.

 - Azure AD에서 하나 이상의 게스트 구성원이 있는 그룹
 - Azure AD에 연결되어 한 명 이상의 게스트 사용자가 할당된 응용 프로그램 

각 게스트에게 자신의 액세스를 검토하도록 할지 또는 한 명 이상의 사용자에게 모든 게스트 액세스를 검토하도록 요청할지를 결정할 수 있습니다.

 이러한 시나리오에 대해서는 다음 섹션에서 설명됩니다.

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>게스트에게 자신의 그룹 구성원 자격을 검토하도록 요청

액세스 검토를 사용하여 그룹에 초대되고 추가된 사용자가 계속 액세스해야 하는지 확인할 수 있습니다. 게스트에게 해당 그룹에서 자신의 구성원 자격을 검토하도록 쉽게 요청할 수 있습니다.

1. 그룹에 대한 액세스 검토를 시작하려면 게스트 사용자 구성원만 포함하도록 검토를 선택하고, 해당 구성원이 직접 검토하도록 합니다. 자세한 내용은 [액세스 검토 만들기](active-directory-azure-ad-controls-create-access-review.md)를 참조하세요.

2. 각 게스트에게 자신의 구성원 자격을 검토하도록 요청합니다. 기본적으로 초대를 수락한 각 게스트는 Azure AD에서 액세스 검토에 대한 링크가 포함된 전자 메일을 받게 됩니다. Azure AD에는 게스트에게 [자신의 액세스를 검토하는 방법](active-directory-azure-ad-controls-perform-access-review.md)에 대한 지침을 제공합니다.

3. 검토자가 입력을 제공하면 액세스 검토를 중지하고 변경 내용을 적용합니다. 자세한 내용은 [액세스 검토 완료](active-directory-azure-ad-controls-complete-access-review.md)를 참조하세요.

4. 지속적인 액세스에 대한 자신의 요구 사항을 거부한 사용자 외에도 응답하지 않은 사용자를 제거할 수 있습니다. 응답하지 않은 사용자는 더 이상 메일을 수신하지 못할 수 있습니다.

5. 그룹이 액세스 관리에 사용되지 않는 경우 초대를 수락하지 않았기 때문에 검토에 참여하도록 선택되지 않은 사용자도 제거할 수 있습니다. 수락되지 않은 경우 초대된 사용자의 전자 메일 주소에 오타가 있는 것일 수 있습니다. 그룹이 메일 그룹으로 사용되면 일부 게스트 사용자가 연락처 개체이므로 참여하도록 선택되지 않았을 수 있습니다.

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>스폰서에게 게스트의 그룹 구성원 자격을 검토하도록 요청

그룹 소유자와 같은 스폰서에게 지속적인 그룹 구성원 자격에 대한 게스트의 요구 사항을 검토하도록 요청할 수 있습니다.

1. 그룹에 대한 액세스 검토를 시작하려면 게스트 사용자 구성원만 포함하도록 검토를 선택합니다. 그런 후 한 명 이상의 검토자를 지정합니다. 자세한 내용은 [액세스 검토 만들기](active-directory-azure-ad-controls-create-access-review.md)를 참조하세요.

2. 입력을 제공하도록 검토자에게 요청합니다. 기본적으로 각 사용자는 Azure AD로부터 [자신의 액세스 검토를 수행](active-directory-azure-ad-controls-perform-access-review.md)하는 액세스 패널에 대한 링크가 포함된 전자 메일을 받습니다.

3. 검토자가 입력을 제공하면 액세스 검토를 중지하고 변경 내용을 적용합니다. 자세한 내용은 [액세스 검토 완료](active-directory-azure-ad-controls-complete-access-review.md)를 참조하세요.

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>게스트에게 응용 프로그램에 대한 자신의 액세스를 검토하도록 요청

액세스 검토를 사용하여 특정 응용 프로그램에 초대된 사용자가 계속 액세스해야 하는지 확인할 수 있습니다. 게스트에게 자신의 액세스 요구 사항을 검토하도록 쉽게 요청할 수 있습니다.

1. 응용 프로그램에 대한 액세스 검토를 시작하려면 게스트만 포함하도록 검토를 선택하고, 해당 사용자가 자신의 액세스를 검토하도록 합니다. 자세한 내용은 [액세스 검토 만들기](active-directory-azure-ad-controls-create-access-review.md)를 참조하세요.

2. 각 게스트에게 응용 프로그램에 대한 자신의 액세스를 검토하도록 요청 기본적으로 초대를 수락한 각 게스트는 Azure AD에서 조직의 액세스 패널에 있는 액세스 검토에 대한 링크가 포함된 전자 메일을 받게 됩니다. Azure AD에는 게스트에게 [자신의 액세스를 검토하는 방법](active-directory-azure-ad-controls-perform-access-review.md)에 대한 지침을 제공합니다.

3. 검토자가 입력을 제공하면 액세스 검토를 중지하고 변경 내용을 적용합니다. 자세한 내용은 [액세스 검토 완료](active-directory-azure-ad-controls-complete-access-review.md)를 참조하세요.

4. 지속적인 액세스에 대한 자신의 요구 사항을 거부한 사용자 외에도 응답하지 않은 게스트 사용자를 제거할 수 있습니다. 응답하지 않은 사용자는 더 이상 메일을 수신하지 못할 수 있습니다. 참여하도록 선택되지 않은 게스트 사용자, 특히 최근에 초대되지 않은 사용자도 제거할 수 있습니다. 해당 사용자는 초대를 수락하지 않았으므로 응용 프로그램에 대한 액세스 권한이 없습니다. 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>스폰서에게 응용 프로그램에 대한 게스트의 액세스를 검토하도록 요청

응용 프로그램 소유자와 같은 스폰서에게 지속적인 응용 프로그램 액세스에 대한 게스트의 요구 사항을 검토하도록 요청할 수 있습니다.

1. 응용 프로그램에 대한 액세스 검토를 시작하려면 게스트만 포함하도록 검토를 선택합니다. 그런 후 한 명 이상의 사용자를 검토자로 지정합니다. 자세한 내용은 [액세스 검토 만들기](active-directory-azure-ad-controls-create-access-review.md)를 참조하세요.

2. 입력을 제공하도록 검토자에게 요청합니다. 기본적으로 각 사용자는 Azure AD로부터 [자신의 액세스 검토를 수행](active-directory-azure-ad-controls-perform-access-review.md)하는 액세스 패널에 대한 링크가 포함된 전자 메일을 받습니다.

3. 검토자가 입력을 제공하면 액세스 검토를 중지하고 변경 내용을 적용합니다. 자세한 내용은 [액세스 검토 완료](active-directory-azure-ad-controls-complete-access-review.md)를 참조하세요.

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>게스트에게 자신의 일반적 액세스 요구 사항을 검토하도록 요청

일부 조직에서는 게스트가 그룹 구성원 자격을 인식하지 못할 수 있습니다.

> [!NOTE]
> 이전 버전의 Azure Portal은 UserType이 Guest인 사용자의 관리 액세스를 허용하지 않았습니다. 경우에 따라 디렉터리의 관리자가 PowerShell을 사용하여 게스트의 UserType 값을 Member로 변경했을 수 있습니다. 이전에 이러한 변경 사항이 디렉터리에서 발생한 경우, 이전에 관리자 액세스 권한이 있던 모든 게스트 사용자가 이전 쿼리에 포함되지 않을 수도 있습니다. 이 경우 게스트의 UserType을 변경하거나 그룹 구성원 자격에 해당 게스트를 수동으로 포함해야 합니다.

1. 적합한 그룹이 아직 없는 경우 Azure AD에 해당 게스트를 구성원으로 하는 보안 그룹을 만듭니다. 예를 들어 수동으로 유지 관리되는 게스트의 구성원 자격으로 그룹을 만들 수 있습니다. 또는 UserType 특성 값이 Guest인 Contoso 테넌트의 사용자에 대해 "Contoso 게스트"와 같은 이름으로 동적 그룹을 만들 수도 있습니다.  효율성을 위해 그룹이 영구적인 게스트인지 확인합니다. 검토할 필요가 없는 사용자가 있는 그룹은 선택하지 마십시오.

2. 해당 그룹에 대한 액세스 검토를 시작하려면 검토자가 직접 구성원이 되도록 선택합니다. 자세한 내용은 [액세스 검토 만들기](active-directory-azure-ad-controls-create-access-review.md)를 참조하세요.

3. 각 게스트에게 자신의 구성원 자격을 검토하도록 요청합니다. 기본적으로 초대를 수락한 각 게스트는 Azure AD에서 조직의 액세스 패널에 있는 액세스 검토에 대한 링크가 포함된 전자 메일을 받게 됩니다. Azure AD에는 게스트에게 [자신의 액세스를 검토하는 방법](active-directory-azure-ad-controls-perform-access-review.md)에 대한 지침을 제공합니다.  자신의 초대를 수락하지 않은 게스트는 검토 결과에서 “알림을 받지 않음”으로 표시됩니다.

4. 검토자가 입력을 제공하면 액세스 검토를 중지합니다. 자세한 내용은 [액세스 검토 완료](active-directory-azure-ad-controls-complete-access-review.md)를 참조하세요.

5. 거부되었거나 검토를 완료하지 않았거나 이전에 초대를 수락하지 않은 게스트에 대한 게스트 액세스를 제거합니다. 게스트 중 일부가 검토에 참여하도록 선택되었거나 이전에 초대를 수락하지 않았던 연락처인 경우, Azure Portal 또는 PowerShell을 사용하여 해당 계정을 사용하지 않도록 설정할 수 있습니다. 게스트가 더 이상 액세스할 필요가 없고 연락처가 아닌 경우, Azure Portal 또는 PowerShell을 사용하여 사용자 개체를 디렉터리에서 제거하여 게스트 사용자 개체를 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[그룹의 멤버 또는 응용 프로그램에 액세스에 대한 액세스 검토 만들기](active-directory-azure-ad-controls-create-access-review.md)







