---
title: '자습서: Azure Active Directory로 자동 사용자 프로비전을 위한 ServiceNow 구성 | Microsoft Docs'
description: 사용자 계정을 Azure AD에서 ServiceNow로 자동으로 프로비전 및 프로비전 해제하는 방법을 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 88505126389f51f59cf0538da8b72139f86e58d5
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning-with-azure-active-directory"></a>자습서: Azure Active Directory로 자동 사용자 프로비전을 위한 ServiceNow 구성

이 자습서에서는 사용자 계정을 Azure AD에서 ServiceNow로 자동으로 프로비전 및 프로비전 해제하도록 ServiceNow 및 Azure AD에서 수행해야 하는 단계를 설명합니다.

## <a name="prerequisites"></a>필수 조건

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

*   Azure Active Directory 테넌트.
*   유효한 ServiceNow for Work 또는 ServiceNow for Education용 테넌트가 있어야 합니다. 어느 서비스에나 평가판 계정을 사용할 수 있습니다.
*   팀 관리자 권한있는 ServiceNow의 사용자 계정입니다.

## <a name="assigning-users-to-servicenow"></a>ServiceNow에 사용자 할당

Azure Active Directory는 "할당"이라는 개념을 사용하여 어떤 사용자가 선택한 앱에 대한 액세스를 받아야 하는지를 판단합니다. 자동 사용자 계정 프로비전의 컨텍스트에서는 Azure AD의 응용 프로그램에 "할당된" 사용자 및 그룹만 동기화됩니다.

프로비전 서비스를 구성하고 사용하도록 설정하기 전에 ServiceNow 앱 액세스 권한이 필요한 사용자를 나타내는 Azure AD의 사용자 및/또는 그룹을 결정해야 합니다. 결정되면 [엔터프라이즈 앱에 사용자 또는 그룹 할당](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)의 지침에 따라 해당 사용자를 ServiceNow 앱에 할당할 수 있습니다.


> [!IMPORTANT]
>*   단일 Azure AD 사용자를 ServiceNow에 할당하여 프로비저닝 구성을 테스트하는 것이 좋습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.
>*   ServiceNow에 사용자를 할당할 때는 유효한 사용자 역할을 선택해야 합니다. "기본 액세스" 역할은 프로비전에 작동하지 않습니다.

## <a name="enable-automated-user-provisioning"></a>자동 사용자 프로비전 사용

이 섹션에서는 Azure AD를 ServiceNow의 사용자 계정 프로비전 API에 연결하고, Azure AD의 사용자 및 그룹 할당을 기반으로 ServiceNow에서 할당된 사용자 계정을 만들고 업데이트하고 비활성화하도록 프로비전 서비스를 구성하는 방법을 안내합니다.

> [!TIP]
>[Azure Portal](https://portal.azure.com)에 제공된 지침에 따라 ServiceNow에 SAML 기반 Single Sign-On을 사용하도록 설정할 수도 있습니다. Single Sign-On은 자동 프로비전과 별개로 구성할 수 있습니다. 하지만 이 두 가지 기능은 서로 보완적입니다.

### <a name="configure-automatic-user-account-provisioning"></a>자동 사용자 계정 프로비전 구성

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory > 엔터프라이즈 앱 > 모든 응용 프로그램** 섹션으로 이동합니다.

2. 이미 ServiceNow에 Single Sign-On을 구성한 경우 검색 필드를 사용하여 ServiceNow의 인스턴스를 검색합니다. 그러지 않은 경우 **추가**를 선택하고 응용 프로그램 갤러리에서 **ServiceNow**를 검색합니다. 검색 결과에서 ServiceNow를 선택하고 응용 프로그램 목록에 추가합니다.

3. ServiceNow의 인스턴스를 선택한 다음 **프로비전** 탭을 선택합니다.

4. **프로비전 모드**를 **자동**으로 설정합니다. 

    ![프로비전](./media/active-directory-saas-servicenow-provisioning-tutorial/provisioning.png)

5. 관리자 자격 증명 섹션에서 다음 단계를 수행합니다.
   
    a. **ServiceNow 인스턴스 이름** 텍스트 상자에 ServiceNow 인스턴스 이름을 입력합니다.

    나. **ServiceNow 관리자 사용자 이름** 텍스트 상자에 관리자의 사용자 이름을 입력합니다.

    다. **ServiceNow 관리자 암호** 텍스트 상자에 관리자의 암호를 입력합니다.

6. Azure Portal에서 **연결 테스트**를 클릭하여 Azure AD가 ServiceNow 앱에 연결할 수 있는지 확인합니다. 연결에 실패하면 ServiceNow 계정에 팀 관리자 권한이 있는지 확인하고 **"관리자 자격 증명"** 단계를 다시 시도합니다.

7. 프로비전 오류 알림을 받을 개인 또는 그룹의 이메일 주소를 **알림 메일** 필드에 입력하고 확인란을 선택합니다.

8. **저장**을 클릭합니다.

9. 매핑 섹션에서 **Azure Active Directory 사용자를 ServiceNow에 동기화**를 선택합니다.

10. **특성 매핑** 섹션에서 Azure AD에서 ServiceNow로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 ServiceNow의 사용자 계정을 일치시키는 데 사용됩니다. 저장 단추를 선택하여 변경 내용을 커밋합니다.

11. ServiceNow에 대한 Azure AD 프로비전 서비스를 사용하도록 설정하려면 설정 섹션에서 **프로비전 상태**를 **켜기**로 변경합니다.

12. **저장**을 클릭합니다.

사용자 및 그룹 섹션에서 Provisioning Status에 할당된 모든 사용자 및/또는 그룹의 초기 동기화가 시작됩니다. 초기 동기화는 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다. **동기화 세부 정보** 섹션을 사용하여 진행 상태를 모니터링하고 링크를 클릭하여 ServiceNow 앱의 프로비전 서비스에서 수행한 모든 작업을 설명하는 프로비전 작업 로그를 확인할 수 있습니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](active-directory-saas-provisioning-reporting.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)
* [Single Sign-On 구성](active-directory-saas-servicenow-tutorial.md)


