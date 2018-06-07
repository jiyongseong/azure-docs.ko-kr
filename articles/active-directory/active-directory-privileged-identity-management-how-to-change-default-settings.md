---
title: 역할 활성화 설정을 관리하는 방법 | Microsoft Docs
description: Azure Active Directory Privileged Identity Management 확장을 사용하여 권한 있는 ID에 대한 기본 설정을 변경하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 06/06/2017
ms.author: curtand
ms.custom: pim
ms.openlocfilehash: 972fd1e322e578516073307d01548132473bc52c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-manage-role-activation-settings-in-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management에서 역할 활성화 설정을 관리하는 방법
권한 있는 역할 관리자는 적격 역할 할당을 활성화한 사용자의 환경을 변경하는 등, 해당 조직에서 Azure AD PIM(Privileged Identity Management)을 사용자 지정할 수 있습니다.

## <a name="manage-the-role-activation-settings"></a>역할 활성화 설정 관리
1. [Azure 포털](https://portal.azure.com) 로 이동하여 대시보드에서 **Azure AD Privileged Identity Management** 앱을 선택합니다.
2. **권한 있는 역할 관리** > **설정** > **권한 있는 역할**을 선택합니다.
3. 관리하려는 설정의 역할을 선택합니다.

각 역할에 대한 설정 페이지에서 여러 설정을 구성할 수 있습니다. 이러한 설정은 영구 관리자가 아닌, 적격 관리자인 사용자에게만 영향을 미칩니다.

**활성화**: 역할이 만료되기 전에 활성 상태로 지속되는 시간(시 단위). 이는 1 ~ 72시간 사이가 될 수 있습니다.

**알림**: 시스템에서 관리자에게 역할을 활성화했는지 확인하는 전자 메일을 전송하게 할지를 선택할 수 있습니다. 이 정보는 무단 또는 불법 활성화를 탐지하는 데 유용할 수 있습니다.

**인시던트/요청 티켓**: 적격 관리자에게 역할을 활성화할 때 티켓 번호를 포함하도록 요구할지 여부를 선택할 수 있습니다. 이 기능은 역할 액세스 감사를 수행할 때 유용할 수 있습니다.

**Multi-Factor Authentication**: 사용자에게 역할을 활성화하기 전에 MFA로 id를 확인하도록 할지 여부를 선택할 수 있습니다. 이 작업은 매번 역할을 활성화할 때마다가 아니라 세션당 한 번만 수행해야 합니다. MFA를 사용할 때 염두에 두어야 할 두 가지가 있습니다.

* 전자 메일 주소로 Microsoft 계정을 이용하는 사용자(일반적으로 @outlook.com이지만 항상 그렇지는 않음)는 Azure MFA에 등록할 수 없습니다. Microsoft 계정 가진 사용자에게 역할을 할당하려는 경우, 영구 관리자가 되도록 하거나 해당 역할에 대해 MFA를 사용하지 않도록 설정해야 합니다.
* Azure AD 및 Office365에 대해 높은 권한이 있는 역할에 대한 MFA를 사용하지 않도록 설정할 수 없습니다. 이런 안전 기능을 둔 것은 이러한 역할을 신중하게 보호해야 하기 때문입니다.  
  
  * 응용 프로그램 관리자
  * 응용 프로그램 프록시 서버 관리자
  * 대금 청구 관리자  
  * 규정 준수 관리자  
  * CRM 서비스 관리자
  * 고객 LockBox 액세스 승인자
  * 디렉터리 기록기  
  * Exchange 관리자  
  * 전역 관리자
  * Intune 서비스 관리자
  * 사서함 관리자  
  * 파트너 계층1 지원  
  * 파트너 계층2 지원  
  * 권한 있는 역할 관리자   
  * 보안 관리자  
  * SharePoint 관리자  
  * 비즈니스용 Skype 관리자  
  * 사용자 계정 관리자  

PIM과 함께 MFA를 사용하는 방법에 대한 자세한 내용은 [MFA를 요구하는 방법](active-directory-privileged-identity-management-how-to-require-mfa.md)을 참조하세요.

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>다음 단계
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

