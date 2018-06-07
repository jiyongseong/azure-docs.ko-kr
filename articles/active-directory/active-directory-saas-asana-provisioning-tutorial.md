---
title: '자습서: Azure Active Directory로 자동 사용자 프로비전을 위한 Asana 구성 | Microsoft Docs'
description: 사용자 계정을 Asana에 자동으로 프로비전 및 프로비전 해제하도록 Azure Active Directory를 구성하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: sakula
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: c7e33a46940c9bf0cda5e48c07d42db9f0c7cd96
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비전을 위한 Asana 구성

이 자습서의 목적은 사용자 계정을 Azure AD에서 Asana로 자동으로 프로비전 및 프로비전 해제하기 위해 Asana 및 Azure AD(Azure Active Directory)에서 수행해야 하는 단계를 설명하는 것입니다.

## <a name="prerequisites"></a>필수 조건

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

*   Azure AD 테넌트
*   [Enterprise](https://www.asana.com/pricing) 계획 이상을 사용하도록 설정된 Asana 테넌트 
*   관리자 권한이 있는 Asana의 사용자 계정 

> [!NOTE] 
> Azure AD 프로비전 통합에는 Asana에 제공되는 [Asana API](https://app.asana.com/api/1.0/scim/Users)가 사용됩니다.

## <a name="assign-users-to-asana"></a>Asana에 사용자 할당

Azure AD는 "할당"이라는 개념을 사용하여 어떤 사용자가 선택한 앱에 대한 액세스를 받아야 하는지를 판단합니다. 자동 사용자 계정 프로비전의 컨텍스트에서는 Azure AD의 응용 프로그램에 할당된 사용자만 동기화됩니다. 

프로비전 서비스를 구성 및 사용하도록 설정하기 전에 Azure AD에서 Asana 앱에 액세스해야 하는 사용자를 결정해야 합니다. 그런 후에 다음 지침에 따라 해당 사용자를 Asana 앱에 할당할 수 있습니다.

[엔터프라이즈 앱에 사용자 할당](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>Asana에 사용자를 할당하기 위한 주요 팁

프로비전 구성을 테스트하기 위해 한 명의 Azure AD 사용자를 Asana에 할당하는 것이 좋습니다. 추가 사용자를 나중에 할당할 수 있습니다.

## <a name="configure-user-provisioning-to-asana"></a>Asana에 사용자 프로비전 구성 

이 섹션은 Azure AD를 Asana의 사용자 계정 프로비전 API에 연결하는 단계를 안내합니다. 또한 Azure AD의 사용자 할당을 기반으로 Asana에 할당된 사용자 계정을 만들고 업데이트하며 사용하지 않도록 프로비전 서비스를 구성할 수도 있습니다.

> [!TIP]
> Asana에 대해 SAML 기반 Single Sign-On을 사용하도록 설정하려면 [Azure Portal](https://portal.azure.com)에 제공된 지침을 따릅니다. Single Sign-On은 자동 프로비전과 별개로 구성할 수 있습니다. 하지만 이 두 가지 기능은 서로 보완적입니다.

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>Azure AD에서 Asana에 자동 사용자 계정 프로비전을 구성하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory** > **엔터프라이즈 앱** > **모든 응용 프로그램** 섹션으로 이동합니다.

2. Single Sign-On을 위해 Asana를 이미 구성한 경우 검색 필드를 사용하여 Asana의 인스턴스를 검색합니다. 그렇지 않은 경우 **추가**를 선택하고 응용 프로그램 갤러리에서 **Asana**를 검색합니다. 검색 결과에서 **Asana**를 선택하고 응용 프로그램의 목록에 추가합니다.

3. Asana 인스턴스를 선택한 다음, **프로비전** 탭을 선택합니다.

4. **프로비전 모드**를 **자동**으로 설정합니다.

    ![Asana 프로비전](./media/active-directory-saas-asana-provisioning-tutorial/asanaazureprovisioning.png)

5. **관리자 자격 증명** 섹션에서 다음 지침에 따라 토큰을 생성하여 **비밀 토큰**에 입력합니다.

    a. 관리자 계정을 사용하여 [Asana](https://app.asana.com)에 로그인합니다.

    나. 맨 위 막대에서 프로필 사진을 선택하고 현재 조직 이름 설정을 선택합니다.

    다. **서비스 계정** 탭으로 이동합니다.

    d. **서비스 계정 추가**를 선택합니다.

    e. **이름** 및 **정보**와 프로필 사진을 필요에 따라 업데이트합니다. **토큰**의 토큰을 복사하고 **변경 내용 저장**에서 선택합니다.

6. Azure Portal에서 **연결 테스트**를 선택하여 Azure AD가 Asana 앱에 연결할 수 있는지 확인합니다. 연결에 실패하면 Asana 계정에 관리자 권한이 있는지 확인하고 **연결 테스트** 단계를 다시 시도합니다.

7. 프로비전 오류 알림을 받을 개인 또는 그룹의 전자 메일 주소를 **알림 메일**에 입력합니다. 아래 확인란을 선택합니다.

8. **저장**을 선택합니다. 

9. **매핑** 섹션에서 **Azure Active Directory 사용자를 Asana에 동기화**를 선택합니다.

10. **특성 매핑** 섹션에서 Azure AD에서 Asana로 동기화할 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 Asana의 사용자 계정을 일치시키는 데 사용됩니다. 변경 내용을 커밋하려면 **저장**을 선택합니다. 자세한 내용은 [사용자 프로비전 특성 매핑 사용자 지정](active-directory-saas-customizing-attribute-mappings.md)을 참조하세요.

11. Asana에 대한 Azure AD 프로비전 서비스를 사용하도록 설정하려면 **설정** 섹션에서 **프로비전 상태**를 **켜기**로 변경합니다.

12. **저장**을 선택합니다. 

이제 **사용자** 섹션에서 Asana에 할당된 모든 사용자의 초기 동기화가 시작됩니다. 초기 동기화는 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다. **동기화 세부 정보** 섹션을 사용하여 진행 상태를 모니터링하고 링크를 통해 프로비전 작업 로그를 확인합니다. 감사 로그에서는 Asana 앱에서 프로비전 서비스가 수행하는 모든 작업을 설명합니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](active-directory-saas-provisioning-reporting.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](active-directory-enterprise-apps-manage-provisioning.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)
* [Single Sign-on 구성](active-directory-saas-asana-tutorial.md)
