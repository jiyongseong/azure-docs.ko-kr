---
title: Azure Active Directory에서 Microsoft 앱 및 서비스에 LinkedIn 연결을 사용하도록 설정 | Microsoft Docs
description: Azure Active Directory에서 Microsoft 앱용 LinkedIn 계정 연결을 사용하거나 사용하지 않도록 설정하는 방법에 대해 설명합니다.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 05/02/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: 3d73cae0975bea0332c2d266d2745773b373b7d8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33762373"
---
# <a name="linkedin-account-connections-for-microsoft-apps-and-services"></a>Microsoft 앱 및 서비스에 대한 LinkedIn 계정 연결
이 문서에서는 Azure AD(Azure Active Directory) 관리 센터에서 테넌트에 대해 LinkedIn 계정 연결을 관리하는 방법을 알아볼 수 있습니다. 

> [!IMPORTANT]
> LinkedIn 계정 연결 기능이 현재 Azure AD 테넌트에 공개되고 있습니다. 테넌트에게 공개되면 기본적으로 사용하도록 설정됩니다. Exchange Online 사서함이 오스트레일리아, 캐나다, 중국, 프랑스, 독일, 인도, 대한민국, 영국, 일본 및 남아프리카에서 호스트되는 미국 정부 고객 및 조직은 사용할 수 없습니다. 이러한 사서함 위치에 대한 지원은 곧 제공될 예정입니다.  롤아웃 정보의 최신 상태 보기는 [Office 365 로드맵](https://products.office.com/business/office-365-roadmap?filters=%26freeformsearch=linkedin#abc) 페이지를 참조하세요.

## <a name="benefit-to-users"></a>사용자의 혜택
사용자가 자신의 LinkedIn 계정에 연결하면 LinkedIn 정보는 다양한 Microsoft 앱 또는 서비스에서 개인화된 정보 및 기능을 표시하는 데 사용됩니다. 사용자는 조직 외부 사람인 경우라도 Microsoft 프로필 카드로 작업하는 사람들에 대한 정보를 확인할 수 있습니다. 시간이 지남에 따라 LinkedIn 경험은 더 관련성이 깊어지고 해당 작업에 맞게 됩니다. 예를 들어 LinkedIn은 사용자의 작업을 기반으로 새 연결을 제안하거나 그 날의 일정에 있는 사용자에 대한 정보를 공개할 수 있습니다.

## <a name="how-linkedin-account-connections-appear-to-the-user"></a>LinkedIn 계정 연결이 사용자에게 표시되는 방식
LinkedIn 계정 연결을 통해 사용자는 일부 Microsoft 앱에서 공개 LinkedIn 프로필 정보를 볼 수 있습니다. 테넌트에 속하는 사용자는 자신의 LinkedIn 및 Microsoft 회사 또는 학교 계정을 연결하여 추가적인 LinkedIn 프로필 정보를 표시하도록 선택할 수 있습니다. 자세한 내용은 [Microsoft 앱 및 서비스의 LinkedIn 정보 및 기능](https://go.microsoft.com/fwlink/?linkid=850740)을 참조하세요.

조직 내 사용자가 자신의 LinkedIn 및 Microsoft 회사 또는 학교 계정을 연결하는 경우 두 가지 옵션이 있습니다. 
* 두 계정 간에 데이터를 공유하도록 권한을 부여합니다. 즉, Microsoft 회사 또는 학교 계정을 공유하도록 LinkedIn 계정에 권한을 부여할 뿐만 아니라 LinkedIn 계정과 데이터를 공유하도록 Microsoft 회사 또는 학교 계정에도 권한을 부여합니다. LinkedIn과 공유되는 데이터는 온라인 서비스가 종료됩니다. 
* LinkedIn 계정의 데이터만 Microsoft 회사 및 학교 계정과 데이터를 공유하도록 권한을 부여합니다.

사용자의 LinkedIn 및 Microsoft 회사 또는 학교 계정 사이에 공유되는 데이터에 대한 자세한 내용은 [회사 또는 학교 Microsoft 앱의 LinkedIn](https://www.linkedin.com/help/linkedin/answer/84077)(영문)을 참조하세요. 
* [사용자는 언제든 계정 연결을 해제](https://www.linkedin.com/help/linkedin/answer/85097)하고 데이터 공유 권한을 제거할 수 있습니다. 
* 사용자는 Microsoft 앱에 자신의 프로필 표시 여부를 비롯하여 [자신의 LinkedIn 프로필이 표시되는 방식을 제어할 수 있습니다](https://www.linkedin.com/help/linkedin/answer/83).

## <a name="privacy-considerations"></a>개인정보처리방침 고려 사항
LinkedIn 계정 연결을 사용하도록 설정하면 Microsoft 앱 및 서비스에서 일부 사용자의 LinkedIn 정보에 액세스할 수 있습니다. Azure AD에서 LinkedIn 계정 연결 사용 시 개인정보처리방침에 대한 자세한 내용은 [Microsoft 개인정보처리방침](https://privacy.microsoft.com/privacystatement/)을 참조하세요. 

## <a name="manage-linkedin-account-connections"></a>LinkedIn 계정 연결 관리
LinkedIn 계정 연결 기능은 전체 테넌트에 대해 기본적으로 사용하도록 설정되어 있습니다. 전체 테넌트에 대해 LinkedIn 계정 연결을 사용하지 않도록 설정하거나 테넌트에서 선택된 사용자에 대해 LinkedIn 계정 연결을 사용하도록 설정하게 선택할 수 있습니다. 

### <a name="enable-or-disable-linkedin-account-connection-for-your-tenant-in-the-azure-portal"></a>Azure Portal에서 테넌트에 대한 LinkedIn 계정 연결 사용 설정 또는 해제

1. Azure AD 테넌트의 전역 관리자인 계정으로 [Azure Active Directory 관리 센터](https://aad.portal.azure.com/)에 로그인합니다.
2. **사용자**를 선택합니다.
3. **사용자** 블레이드에서 **사용자 설정**을 선택합니다.
4. **LinkedIn 계정 연결**에서:
  * 테넌트의 모든 사용자에 대해 LinkedIn 계정 연결을 사용하도록 설정하려면 **예**를 선택합니다.
  * 선택한 테넌트 사용자에 대해서만 LinkedIn 계정 연결을 사용하도록 설정하려면 **선택됨**을 선택합니다.
  * 모든 사용자에 대해 LinkedIn 계정 연결을 사용하지 않도록 설정하려면 **아니요**를 선택합니다. ![LinkedIn 계정 연결을 사용하도록 설정](./media/linkedin-integration/LinkedIn-integration.png)
5. 완료되면 **저장**을 선택하여 설정을 저장합니다.

### <a name="enable-or-disable-linkedin-account-connections-for-your-organizations-office-2016-apps-using-group-policy"></a>그룹 정책을 사용하여 조직의 Office 2016 앱에 LinkedIn 계정 연결을 사용하도록 설정 또는 해제

1. [Office 2016 관리 템플릿 파일(ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030) 다운로드
2. **ADMX** 파일을 추출하고 중앙 저장소에 복사합니다.
3. 그룹 정책 관리를 엽니다.
4. **사용자 구성** > **관리 템플릿** > **Microsoft Office 2016** > **기타** > **Office 응용 프로그램에서 LinkedIn 기능 표시** 설정을 사용하여 그룹 정책 개체를 만듭니다.
5. **사용** 또는 **사용 안 함**을 선택합니다.
  * 이 정책을 **사용**으로 설정하면 Office 2016 옵션 대화 상자에 있는 **Office 응용 프로그램에서 LinkedIn 기능 표시** 설정이 사용됩니다. 즉, 조직의 사용자가 Office 응용 프로그램에서 LinkedIn 기능을 사용할 수 있습니다.
  * 이 정책을 **사용 안 함**으로 설정하면 Office 2016 옵션 대화 상자에 있는 **Office 응용 프로그램에서 LinkedIn 기능 표시** 설정이 사용 안 함 상태로 설정되며, 최종 사용자가 이 설정을 변경할 수 없습니다. 조직 내 사용자가 Office 2016 응용 프로그램에서 LinkedIn 기능을 사용할 수 있습니다. 

이 그룹 정책은 로컬 컴퓨터용 Office 2016 앱에만 영향을 줍니다. 사용자가 Office 2016 앱에서 LinkedIn을 해제하더라도 Office 365 전체의 카드 프로필에 LinkedIn 기능이 표시됩니다. 

### <a name="learn-more"></a>자세한 정보 
* [Microsoft 앱의 LinkedIn 정보 및 기능](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn 도움말 센터](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>다음 단계
다음 링크를 사용하여 Azure Portal에서 현재 LinkedIn 계정 연결 설정을 살펴봅니다.

[LinkedIn 계정 연결 구성](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 