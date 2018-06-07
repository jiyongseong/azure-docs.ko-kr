---
title: Azure Active Directory B2B 공동 작업 문제 해결 | Microsoft Docs
description: Azure Active Directory B2B 공동 작업과 관련된 일반 문제를 해결하는 방법
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/25/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 4347b846b0ff91f606615abc2c94253c0ae70587
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34267209"
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Azure Active Directory B2B 공동 작업 문제 해결

Azure AD(Azure Active Directory) B2B 공동 작업과 관련된 일반적인 문제에 대한 몇 가지 해결책은 다음과 같습니다.


## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>외부 사용자를 추가했지만 [전체 주소 목록]이나 사용자 선택에서 볼 수 없습니다.

외부 사용자가 목록에 채워지지 않은 경우 개체를 복제하는 데 몇 분이 걸릴 수 있습니다.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>B2B 게스트 사용자가 SharePoint Online/OneDrive 사용자 선택에 표시되지 않습니다. 
 
SPO(SharePoint Online) 사용자 선택에서 기존 게스트 사용자를 검색하는 기능은 기존 동작과의 일치를 위해 기본적으로 꺼져 있습니다.

이 기능은 테넌트 및 사이트 모음 수준에서 설정 'ShowPeoplePickerSuggestionsForGuestUsers'를 통해 이 기능을 사용하도록 설정할 수 있습니다. 또한 멤버가 디렉터리에서 모든 기존 게스트 사용자를 검색할 수 있도록 허용하는 Set-SPOTenant 및 Set-SPOSite cmdlet을 사용하여 이 기능을 설정할 수 있습니다. 테넌트 범위에 대한 변경 내용은 이미 프로비전된 SPO 사이트에 영향을 주지 않습니다.

## <a name="invitations-have-been-disabled-for-directory"></a>디렉터리에 대한 초대가 사용하도록 설정되지 않았습니다.

사용자를 초대할 수 있는 권한이 없다는 알림이 표시되면 사용자 설정에서 사용자 계정이 외부 사용자를 초대할 권한이 있는지 확인합니다.

![](media/troubleshoot/external-user-settings.png)

최근에 이러한 설정을 수정했거나 사용자에게 [게스트 초대자] 역할을 할당한 경우 변경 내용이 적용되는 데 15-60분 정도 걸릴 수 있습니다.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>상환 중에 내가 초대한 사용자에게 오류가 발생했습니다.

일반적인 오류는 다음과 같습니다.

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>초대 대상자의 관리자가 테넌트에 EmailVerified 사용자를 만들지 못하도록 했습니다.

Azure Active Directory를 사용하는 조직의 사용자를 초대하였으나 특정 사용자의 계정이 없는(예: Azure AD contoso.com에 존재하지 않는 사용자) 경우입니다. contoso.com의 관리자가 정책을 사용하여 사용자를 만들지 못하게 할 수 있습니다. 사용자는 외부 사용자가 허용된 경우인지 해당 관리자에게 확인해야 합니다. 외부 사용자의 관리자가 자체 도메인의 전자 메일 확인 사용자를 허용해야 할 수도 있습니다(전자 메일 확인 사용자 허용은 이 [문서](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)를 확인).

![](media/troubleshoot/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>외부 사용자가 이미 페더레이션된 도메인에 존재하지 않습니다.

페더레이션 인증을 사용하려고 하며 사용자가 Azure Active Directory에 아직 없는 경우에는 사용자를 초대할 수 없습니다.

이 문제를 해결하려면 외부 사용자의 관리자가 사용자 계정을 Azure Active Directory와 동기화해야 합니다.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>일반적으로 잘못된 문자인 ‘\#’은 Azure AD와 어떻게 동기화됩니까?

초대된 계정 user@contoso.com becomes user_contoso.com#EXT#@fabrikam.onmicrosoft.com이므로 “\#”은 Azure AD B2B 공동 작업 또는 외부 사용자에 대해 예약된 UPN 문자입니다. 따라서 온-프레미스에서 가져온 UPN의 \#은 Azure Portal에 로그인할 때 허용되지 않습니다. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>동기화된 그룹에 외부 사용자를 추가할 때 오류가 발생합니다.

외부 사용자는 "할당된" 그룹 또는 "보안" 그룹에만 추가할 수 있으며 온-프레미스에 마스터된 그룹에는 추가할 수 없습니다.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>외부 사용자가 상환할 전자 메일을 받지 못했습니다.

초대 대상자는 ISP 또는 스팸 필터를 확인하여 Invites@microsoft.com 주소가 허용되는지 확인해야 합니다.

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>사용자 지정 메시지가 초대 메시지에 포함되지 않는다는 것을 확인했습니다.

개인 정보 보호 법률을 준수하기 위해 다음 경우에 API에는 전자 메일 초대의 사용자 지정 메시지가 포함되지 않습니다.

- 초대자에게 초대하는 테넌트의 전자 메일 주소가 없는 경우
- AppService 보안 주체가 초대를 보내는 경우

이 시나리오가 중요한 경우 API 초대 전자 메일을 표시하지 않으면서 선택한 전자 메일 메커니즘을 통해 전송할 수 있습니다. 조직의 법률 자문에게 문의하여 이러한 방식으로 전송하는 전자 메일이 개인 정보 보호 법률을 준수하는지도 확인하세요.

## <a name="next-steps"></a>다음 단계

- [B2B 공동 작업에 대한 지원 받기](get-support.md)