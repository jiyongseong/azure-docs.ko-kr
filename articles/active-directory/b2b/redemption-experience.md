---
title: B2B 공동 작업의 초대 상환 - Azure Active Directory | Microsoft Docs
description: 개인 정보 보호 조건에 대한 규약을 비롯한 최종 사용자에 대한 Azure AD B2B 공동 작업 초대 상환 환경을 설명합니다.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 0a0c900bbfbb2778d8fabcbb71e339fd3dabcf47
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259749"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory B2B 공동 작업 초대 상환

Azure AD(Azure Active Directory) B2B 공동 작업을 통해 파트너 조직의 사용자와 공동 작업을 수행하기 위해 게스트 사용자가 공유 앱에 액세스하도록 초대할 수 있습니다. 사용자 인터페이스를 통해 게스트 사용자를 디렉터리에 추가하거나 PowerShell을 통해 사용자를 초대한 후에 게스트 사용자는 [개인 정보 보호 조건](#privacy-policy-agreement)에 동의하는 첫 번째 동의 프로세스를 거쳐야 합니다. 다음 중 한 가지 방법으로 이 프로세스가 수행됩니다.

- 게스트 초대자가 공유 앱에 대한 직접 링크를 보냅니다. 초대 받은 사람은 링크를 클릭하여 로그인하고, 개인 정보 보호 조건을 수락하고, 원활하게 공유 리소스에 액세스합니다. (게스트 사용자가 상환 URL을 포함한 초대 이메일을 계속 받지만 특별한 경우도 이외에는 더 이상 초대 이메일을 사용할 필요가 없습니다.)  
- 게스트 사용자는 초대 이메일을 수신하고 상환 URL을 클릭합니다. 첫 번째 로그인의 일부로 개인 정보 보호 조건에 동의하라는 메시지가 표시됩니다.

## <a name="redemption-through-a-direct-link"></a>직접 링크를 통해 상환

게스트 초대자는 공유 앱에 대한 직접 링크를 전송하여 게스트 사용자를 초대할 수 있습니다. 게스트 사용자의 경우 상환 환경은 공유 앱에 로그인하는 것만큼 쉽습니다. 앱에 대한 링크를 클릭하고, 개인 정보 보호 조건을 검토하고 동의한 다음, 원활하게 앱에 액세스할 수 있습니다. 대부분의 경우에 게스트 사용자는 더 이상 초대 이메일에서 상환 URL을 클릭할 필요가 없습니다.

사용자 인터페이스를 통해 게스트 사용자를 초대했거나 PowerShell 초대 환경의 일부로 초대 이메일을 보낸 경우 초대된 사용자는 여전히 초대 이메일을 받게 됩니다. 이 이메일은 다음과 같은 특별한 경우에 유용합니다.

- 사용자에게 Azure AD 계정 또는 MSA(Microsoft 계정)가 없는 경우 이 경우에 사용자는 해당 링크를 클릭하기 전에 MSA 만들어야 합니다. 또는 초대 이메일에서 상환 URL을 사용할 수 있습니다. 상환 프로세스는 자동으로 사용자에게 MSA를 만들라는 메시지를 표시합니다.
- 경우에 따라 연락처 개체(예: Outlook 연락처 개체)와 충돌이 발생하기 때문에 초대된 사용자 개체에는 이메일 주소가 없을 수 있습니다. 이 경우에 사용자는 초대 이메일에서 상환 URL을 클릭해야 합니다.
- 사용자는 초대된 이메일 주소의 별칭으로 로그인할 수 있습니다. (별칭은 이메일 계정과 연결된 추가 이메일 주소입니다.) 이 경우에 사용자는 초대 이메일에서 상환 URL을 클릭해야 합니다.

이러한 특수한 사례가 조직에 중요한 경우 초대 이메일을 전송하는 방법을 사용하여 사용자를 초대하는 것이 좋습니다. 또한 사용자가 이러한 특수한 사례 중 하나이지 않은 경우에도 액세스하기 위해 초대 이메일에 있는 URL을 클릭할 수 있습니다.

## <a name="redemption-through-the-invitation-email"></a>초대 이메일을 통해 상환

초대 이메일을 전송하는 메서드를 통해 초대된 경우 사용자는 초대 이메일을 통해 다시 초대될 수 있습니다. 초대된 사용자는 이메일에서 상환 URL을 클릭한 다음, 개인 정보 보호 조건을 검토하고 동의할 수 있습니다. 여기에서 프로세스를 자세히 설명합니다.

1.  초대 받은 사람은 초대된 후에 **Microsoft Invitations**에서 보낸 이메일을 통해 초대를 받습니다.
2.  초대 받은 사람은 이메일에서 **시작**을 선택합니다.
3.  초대 받은 사람에게 Azure AD 계정 또는 MSA가 없는 경우 MSA를 만들라는 메시지가 표시됩니다.
4.  초대 받은 사람은 **사용 권한 검토** 화면으로 리디렉션됩니다. 여기에서는 초대하는 조직의 개인정보처리방침을 검토하고 사용 약관에 동의할 수 있습니다.

## <a name="privacy-policy-agreement"></a>개인 정보 취급 방침 규약

게스트 사용자가 로그인하여 처음으로 파트너 조직의 리소스에 액세스하려는 경우 **사용 권한 검토** 화면이 표시됩니다. 여기에서 초대하는 조직의 개인정보처리방침을 검토할 수 있습니다. 사용자는 계속할 초대하는 조직의 개인 정보 정책에 따라 해당 정보의 사용을 수락해야 합니다.

![액세스 패널에서 사용자 설정을 보여주는 스크린샷](media/redemption-experience/ConsentScreen.png) 

테넌트 관리자 권한을 가진 사용자가 조직의 개인정보처리방침에 연결할 수 있는 방법에 대한 정보는 [방법: Azure Active Directory에 조직의 개인 정보 추가](https://aka.ms/adprivacystatement)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure AD B2B 공동 작업이란?](what-is-b2b.md)
- [Azure Portal에서 Azure Active Directory B2B 공동 작업 사용자 추가](add-users-administrator.md)
- [정보 근로자가 Azure Active Directory에 B2B 공동 작업 사용자를 추가하는 방법](add-users-information-worker.md)
- [PowerShell을 사용하여 Azure Active Directory B2B 공동 작업 사용자 추가](customize-invitation-api.md#powershell)
- [게스트 사용자로써 조직 나가기](leave-the-organization.md)