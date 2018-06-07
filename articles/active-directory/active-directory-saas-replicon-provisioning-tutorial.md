---
title: '자습서: Azure Active Directory로 자동 사용자 프로비전을 위한 Replicon 구성 | Microsoft Docs'
description: 사용자 계정을 Replicon에 자동으로 프로비전 및 프로비전 해제하도록 Azure Active Directory를 구성하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: v-ant
ms.openlocfilehash: b6f536ba1711076eab0eb9dcbea7d357e9cebf3d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34345598"
---
# <a name="tutorial-configure-replicon-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비전을 위한 Replicon 구성

이 자습서에서는 사용자 및/또는 그룹을 Replicon으로 자동으로 프로비전하고 프로비전을 해제하도록 Azure AD(Azure Active Directory)를 구성하기 위해 Replicon 및 Azure AD에서 수행할 단계를 보여줍니다.

> [!NOTE]
> 이 자습서에서는 Azure AD 사용자 프로비저닝 서비스에 기반하여 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 응용 프로그램의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](./active-directory-saas-app-provisioning.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

*   Azure AD 테넌트
*   [추가](https://www.replicon.com/time-bill-pricing/) 플랜이 포함되거나 개선된 Replicon 테넌트
*   관리자 권한이 있는 Replicon의 사용자 계정

> [!NOTE]
> Azure AD 프로비전 통합은 [Replicon API](https://www.replicon.com/help/developers)를 사용합니다. 이 기능은 추가 플랜 또는 개선된 기능에서 Replicon 팀에 제공됩니다.

## <a name="adding-replicon-from-the-gallery"></a>갤러리에서 Replicon 추가
Azure AD를 사용하여 자동 사용자 프로비전을 위해 Replicon을 구성하기 전에 Replicon을 Azure AD 응용 프로그램 갤러리에서 관리되는 SaaS 응용 프로그램 목록으로 추가해야 합니다.

**Azure AD 응용 프로그램 갤러리에서 Replicon을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 패널에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램** > **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 섹션][2]
    
3. Replicon을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![새 응용 프로그램 단추][3]

4. 검색 상자에 **Replicon**을 입력합니다.

    ![Replicon 응용 프로그램 검색](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconAppSearch.png)

5. 결과 패널에서 **Replicon**을 선택한 다음, **추가** 단추를 클릭하여 SaaS 응용 프로그램 목록에 Replicon을 추가합니다.

    ![Replicon 검색 결과](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconAppSearchResults.png)

    ![Replicon 만들기 응용 프로그램](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconAppCreate.png)


## <a name="assigning-users-to-replicon"></a>Replicon에 사용자 할당

Azure Active Directory는 "할당"이라는 개념을 사용하여 어떤 사용자가 선택한 앱에 대한 액세스를 받아야 하는지를 판단합니다. 자동 사용자 프로비저닝의 컨텍스트에서 Azure AD의 응용 프로그램에 "할당된" 사용자 및/또는 그룹만 동기화됩니다.

자동 사용자 프로비전을 구성하고 사용하도록 설정하기 전에 Replicon에 액세스해야 하는 Azure AD의 사용자 및/또는 그룹을 결정해야 합니다. 일단 결정되면 다음 지침에 따라 이러한 사용자 및/또는 그룹을 Replicon에 할당할 수 있습니다.

*   [엔터프라이즈 앱에 사용자 또는 그룹 할당](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-replicon"></a>Replicon에 사용자를 할당하는 주요 팁

*   자동 사용자 프로비전 구성을 테스트하려면 단일 Azure AD 사용자를 Replicon에 할당하는 것이 좋습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

*   사용자를 Replicon에 할당할 때 할당 대화 상자에서 유효한 응용 프로그램 특정 역할(사용 가능한 경우)을 선택해야 합니다. **기본 액세스** 역할이 있는 사용자는 프로비전에서 제외됩니다.

## <a name="configuring-automatic-user-provisioning-to-replicon"></a>Replicon에 대한 자동 사용자 프로비전 구성 

이 섹션에서는 Azure AD의 사용자 및/또는 그룹 할당에 따라 Replicon에서 사용자 및/또는 그룹을 만들고, 업데이트하고, 사용 해제하도록 Azure AD 프로비전 서비스를 구성하는 단계를 설명합니다.

> [!TIP]
> [Replicon Single Sign-On 자습서](active-directory-saas-replicon-tutorial.md)에 제공된 지침에 따라 Replicon에 SAML 기반 Single Sign-On을 사용하도록 선택할 수도 있습니다. Single Sign-On은 자동 사용자 프로비전과 별개로 구성할 수 있습니다. 하지만 이 두 가지 기능은 서로 보완적입니다.

### <a name="to-configure-automatic-user-provisioning-for-replicon-in-azure-ad"></a>Azure AD에서 Replicon에 대한 자동 사용자 프로비전을 구성하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고, **Azure Active Directory > 엔터프라이즈 응용 프로그램 > 모든 응용 프로그램**으로 차례로 이동합니다.

2. SaaS 응용 프로그램 목록에서 Replicon을 선택합니다.

    ![Replicon 프로비전](./media/active-directory-saas-replicon-provisioning-tutorial/Replicon2.png)

3. **프로비전** 탭을 선택합니다.

    ![Replicon 프로비전](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconProvisioningTab.png)

4. **프로비전 모드**를 **자동**으로 설정합니다.

    ![Replicon 프로비전](./media/active-directory-saas-replicon-provisioning-tutorial/Replicon1.png)

5. **관리자 자격 증명** 섹션 아래에서 Replicon 계정의 **관리 사용자 이름**, **관리자 암호**, **CompanyId** 및 **도메인**을 입력합니다. 이러한 값의 예는 다음과 같습니다.

    *   **관리 사용자 이름** 필드에 Replicon 테넌트에서 관리자 계정의 사용자 이름을 채웁니다. 예: contosoadmin

    *   **관리자 암호** 필드에 관리자 사용자 이름에 해당하는 암호를 채웁니다.

    *   **CompanyId** 필드에서 Replicon 테넌트의 CompanyId를 채웁니다. 예: 아래 로그인에 따른 CompanyID는 Contoso입니다.

    ![Replicon 로그인](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconLogin.png)

    *   **도메인** 필드에서 6단계에 설명된 대로 도메인을 채웁니다.
    
6. [Replicon 서비스 도움말](https://www.replicon.com/help/determining-the-url-for-your-service-calls)에서 언급한 단계에 따라 Replicon 테넌트 계정의 **serviceEndpointRootURL**을 가져옵니다. URL을 가져올 때 강조 표시된 대로 **도메인**은 **serviceEndpointRootURL**의 하위 도메인입니다. 

    ![Replicon 프로비전](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconEndpoint.png)

7. 5단계에 표시된 필드를 채우면 **연결 테스트**를 클릭하여 Azure AD에서 Replicon에 연결할 수 있는지 확인합니다. 연결에 실패하면 Replicon 계정에 관리자 권한이 있는지 확인하고 다시 시도합니다.

    ![Replicon 프로비전](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconTestConnection.png)

8. **알림 이메일** 필드에 프로비전 오류 알림을 받을 개인 또는 그룹의 이메일 주소를 입력하고, **오류가 발생할 경우 이메일 알림 보내기** 확인란을 선택합니다.

    ![Replicon 프로비전](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconNotificationEmail.png)

9. **저장**을 클릭합니다.

10. **매핑** 섹션에서 **Azure Active Directory 사용자를 Replicon에 동기화**를 선택합니다.
    
    ![Replicon 프로비전](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconUserMapping.png)

11. **특성 매핑** 섹션에서는 Azure AD에서 Replicon으로 동기화된 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성을 사용하여 업데이트 작업에서 Replicon의 사용자 계정과 일치시킵니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

    ![Replicon 프로비전](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconUserMappingAtrributes.png)

12. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](./active-directory-saas-scoping-filters.md)에서 제공하는 다음 지침을 참조합니다.

13. Replicon에 Azure AD 프로비전 서비스를 사용하도록 설정하려면 **설정** 섹션에서 **프로비전 상태**를 **켜기**로 변경합니다.

    ![Replicon 프로비전](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconProvisioningStatus.png)

14. **설정** 섹션의 **범위**에서 원하는 값을 선택하여 Replicon에 프로비전하려는 사용자 및/또는 그룹을 정의합니다.

    ![Replicon 프로비전](./media/active-directory-saas-replicon-provisioning-tutorial/UserGroupSelection.png)

15. 프로비전할 준비가 되면 **저장**을 클릭합니다.

    ![Replicon 프로비전](./media/active-directory-saas-replicon-provisioning-tutorial/SaveProvisioning.png)

이 작업은 **설정**의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 Azure AD 프로비전 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다. **동기화 세부 정보** 섹션을 사용하여 진행 상태를 모니터링하고, Replicon에서 Azure AD 프로비전 서비스가 수행하는 모든 작업을 설명하는 프로비전 활동 보고서에 대한 링크를 따를 수 있습니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](./active-directory-saas-provisioning-reporting.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](active-directory-enterprise-apps-manage-provisioning.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비전 활동에 대한 로그를 검토하고 보고서를 받아 보는 방법을 살펴봅니다](active-directory-saas-provisioning-reporting.md).

<!--Image references-->
[1]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_03.png
