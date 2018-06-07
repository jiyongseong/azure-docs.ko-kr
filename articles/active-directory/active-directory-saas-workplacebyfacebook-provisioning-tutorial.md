---
title: '자습서: Azure Active Directory로 자동 사용자 프로비전을 위한 Workplace by Facebook 구성 | Microsoft Docs'
description: Azure Active Directory와 Workplace by Facebook 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: c25acd00aa55bb8d8c222f440ddfc951bfdf7970
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>자습서: 사용자 프로비전을 위한 Workplace by Facebook 구성

이 자습서의 목적은 사용자 계정을 Azure AD에서 Workplace by Facebook으로 자동으로 프로비전 및 프로비전 해제하도록 Workplace by Facebook 및 Azure AD에서 수행해야 하는 단계를 설명하는 것입니다.

## <a name="prerequisites"></a>필수 조건

Workplace by Facebook과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Workplace by Facebook Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="assigning-users-to-workplace-by-facebook"></a>Workplace by Facebook에 사용자 할당

Azure Active Directory는 "할당"이라는 개념을 사용하여 어떤 사용자가 선택한 앱에 대한 액세스를 받아야 하는지를 판단합니다. 자동 사용자 계정 프로비전의 컨텍스트에서는 Azure AD의 응용 프로그램에 "할당된" 사용자 및 그룹만 동기화됩니다.

프로비전 서비스를 구성하고 사용하도록 설정하기 전에 Workplace by Facebook 앱에 액세스해야 하는 사용자를 나타내는 Azure AD의 사용자 및/또는 그룹을 결정해야 합니다. 결정했으면 다음 지시에 따라 이러한 사용자를 Workplace by Facebook 앱에 할당할 수 있습니다.

[엔터프라이즈 앱에 사용자 또는 그룹 할당](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-workplace-by-facebook"></a>Workplace by Facebook에 사용자를 할당하기 위한 주요 팁

*   단일 Azure AD 사용자를 Workplace by Facebook에 할당하여 프로비전 구성을 테스트하는 것이 좋습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

*   Workplace by Facebook에 사용자를 할당할 때 유효한 사용자 역할을 선택해야 합니다. "기본 액세스" 역할은 프로비전에 작동하지 않습니다.

## <a name="enable-user-provisioning"></a>사용자 프로비저닝 사용

이 섹션에서는 사용자의 Azure AD를 Workplace by Facebook의 사용자 계정 프로비전 API에 연결하고, Azure AD의 사용자 및 그룹 할당을 기반으로 Workplace by Facebook에서 할당된 사용자 계정을 만들고 업데이트하고 비활성화하도록 프로비전 서비스를 구성하는 방법을 안내합니다.

>[!Tip]
>[Azure Portal](https://portal.azure.com)에 제공된 지침에 따라 Workplace by Facebook에 SAML 기반 Single Sign-On을 사용하도록 설정할 수도 있습니다. Single Sign-On은 자동 프로비전과 별개로 구성할 수 있습니다. 하지만 이 두 가지 기능은 서로 보완적입니다.

### <a name="to-configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>Azure AD에서 Workplace by Facebook에 사용자 계정 프로비전을 구성하려면

이 섹션의 목적은 Workplace by Facebook에 Active Directory 사용자 계정을 프로비전할 수 있도록 설정하는 방법을 간략하게 설명하는 것입니다.

Azure AD는 할당된 사용자의 계정 세부 정보를 Workplace by Facebook에 자동으로 동기화하는 기능을 지원합니다. 이 자동 동기화를 사용하면 Workplace by Facebook에서 사용자가 처음으로 로그인하기 전에 사용자에게 액세스 권한을 부여하는 데 필요한 데이터를 가져올 수 있습니다. 또한 Azure AD에서 액세스가 취소되면 Workplace by Facebook에서 사용자 프로비전을 취소합니다.

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory** > **엔터프라이즈 앱** > **모든 응용 프로그램** 섹션으로 이동합니다.

2. 이미 Workplace by Facebook에 Single Sign-On을 구성한 경우 검색 필드를 사용하여 Workplace by Facebook의 인스턴스를 검색합니다. 그러지 않은 경우 **추가**를 선택하고 응용 프로그램 갤러리에서 **Workplace by Facebook**을 검색합니다. 검색 결과에서 Workplace by Facebook을 선택하고 응용 프로그램 목록에 추가합니다.

3. Workplace by Facebook의 인스턴스를 선택한 다음 **프로비전** 탭을 선택합니다.

4. **프로비전 모드**를 **자동**으로 설정합니다. 

    ![프로비전](./media/active-directory-saas-workplacebyfacebook-provisioning-tutorial/provisioning.png)

5. **관리자 자격 증명** 섹션 아래에 Workplace by Facebook 관리자에서 액세스 토큰을 입력하고 테넌트 URL을 `https://www.facebook.com/scim/v1/`로 설정합니다. 작업 공간에 대한 액세스 토큰을 만드는 방법은 이러한 [지침](https://developers.facebook.com/docs/workplace/integrations/custom-integrations/apps)을 참조하세요. 

6. Azure Portal에서 **연결 테스트**를 클릭하여 Azure AD가 Workplace by Facebook 앱에 연결할 수 있는지 확인합니다. 연결에 실패하면 Workplace by Facebook 계정에 팀 관리자 권한이 있는지 확인합니다.

7. 프로비전 오류 알림을 받을 개인 또는 그룹의 이메일 주소를 **알림 메일** 필드에 입력하고 확인란을 선택합니다.

8. **저장**을 클릭합니다.

9. [매핑] 섹션에서 **Synchronize Azure Active Directory Users to Workplace by Facebook**(Azure Active Directory 사용자를 Workplace by Facebook에 동기화)을 선택합니다.

10. **특성 매핑** 섹션에서 Azure AD에서 Workplace by Facebook으로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 Workplace by Facebook의 사용자 계정을 일치시키는 데 사용됩니다. 저장 단추를 선택하여 변경 내용을 커밋합니다.

11. Workplace by Facebook에 대한 Azure AD 프로비전 서비스를 사용하도록 설정하려면 **설정** 섹션에서 **프로비전 상태**를 **켜기**로 변경합니다.

12. **저장**을 클릭합니다.

자동 프로비전을 구성하는 방법에 대한 자세한 내용은 [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)를 참조하세요.

이제 테스트 계정을 만들 수 있습니다. 이제 최대 20분 동안 기다린 후 계정이 Workplace by Facebook에 동기화되었는지 확인합니다.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)
* [Single Sign-On 구성](active-directory-saas-workplacebyfacebook-tutorial.md)
