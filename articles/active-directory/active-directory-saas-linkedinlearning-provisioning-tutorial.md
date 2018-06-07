---
title: '자습서: Azure Active Directory로 자동 사용자 프로비전을 위한 LinkedIn Learning 구성 | Microsoft Docs'
description: 사용자 계정을 LinkedIn Learning로 자동으로 프로비전 및 프로비전 해제하도록 Azure Active Directory를 구성하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: mtillman
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser-msft
ms.openlocfilehash: da39a31777323535c90b4d57e316741295aa873a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34344935"
---
# <a name="tutorial-configure-linkedin-learning-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비전을 위한 LinkedIn Learning 구성


이 자습서의 목적은 사용자 계정을 Azure AD에서 LinkedIn Learning로 자동으로 프로비전 및 프로비전 해제하도록 LinkedIn Learning 및 Azure AD에서 수행해야 하는 단계를 설명하는 것입니다. 

## <a name="prerequisites"></a>필수 조건

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

*   Azure Active Directory 테넌트
*   LinkedIn Learning 테넌트 
*   LinkedIn 계정 센터에 액세스할 수 있는 LinkedIn Learning의 관리자 계정

> [!NOTE]
> Azure Active Directory는 [SCIM](http://www.simplecloud.info/) 프로토콜을 사용하여 LinkedIn Learning과 통합됩니다.

## <a name="assigning-users-to-linkedin-learning"></a>LinkedIn Learning에 사용자 할당

Azure Active Directory는 "할당"이라는 개념을 사용하여 어떤 사용자가 선택한 앱에 대한 액세스를 받아야 하는지를 판단합니다. 자동 사용자 계정 프로비전의 컨텍스트에서는 Azure AD의 응용 프로그램에 "할당된" 사용자 및 그룹만 동기화됩니다. 

프로비전 서비스를 구성하고 사용하도록 설정하기 전에 LinkedIn Learning에 대한 액세스가 필요한 사용자를 대표하는 Azure AD의 사용자 및/또는 그룹을 결정해야 합니다. 결정했으면 다음 지시에 따라 이러한 사용자를 LinkedIn Learning에 할당할 수 있습니다.

[엔터프라이즈 앱에 사용자 또는 그룹 할당](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-learning"></a>LinkedIn Learning에 사용자 할당을 위한 주요 팁

*   프로비전 구성을 테스트하기 위해 단일 Azure AD 사용자를 LinkedIn Learning에 할당하는 것이 좋습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

*   사용자를 LinkedIn Learning에 할당할 때 할당 대화 상자에서 **사용자** 역할을 선택해야 합니다. "기본 액세스" 역할은 프로비전에 작동하지 않습니다.


## <a name="configuring-user-provisioning-to-linkedin-learning"></a>LinkedIn Learning에 사용자 프로비전 구성

이 섹션에서는 사용자의 Azure AD를 LinkedIn Learning의 SCIM 사용자 계정 프로비전 API에 연결하고, Azure AD의 사용자 및 그룹 할당을 기반으로 LinkedIn Learning에서 할당된 사용자 계정을 만들고, 업데이트하고 비활성화하도록 프로비전 서비스를 구성하는 방법을 안내합니다.

> [!TIP]
> [Azure Portal](https://portal.azure.com)에 제공된 지침에 따라 LinkedIn Learning에 대해 SAML 기반 Single Sign-On을 사용하도록 선택할 수도 있습니다. Single Sign-On은 자동 프로비전과 별개로 구성할 수 있습니다. 하지만 이 두 가지 기능은 서로 보완적입니다.


### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-learning-in-azure-ad"></a>Azure AD에서 LinkedIn Learning에 자동 사용자 계정 프로비전을 구성하려면 다음을 수행합니다.


첫 번째 단계는 LinkedIn 액세스 토큰을 검색하는 것입니다. 엔터프라이즈 관리자인 경우 액세스 토큰을 자체 프로비전할 수 있습니다. 사용자 계정 센터에서 **설정 &gt; 전역 설정**으로 이동하고 **SCIM 설치** 패널을 엽니다.

> [!NOTE]
> 링크를 통하지 않고 계정 센터에 직접 액세스하는 경우 다음 단계를 사용하여 도달할 수 있습니다.

1)  계정 센터에 로그인합니다.

2)  **관리 &gt; 관리 설정**을 선택합니다.

3)  왼쪽 세로 막대에서 **고급 통합**을 클릭합니다. 계정 센터로 이동합니다.

4)  **+ 새 SCIM 구성 추가**를 클릭하고 각 필드를 입력하여 절차를 따릅니다.

> 자동 할당 라이선스가 활성화되지 않으면 사용자 데이터만 동기화된 것입니다.

![LinkedIn Learning 프로비저닝](./media/active-directory-saas-linkedinlearning-provisioning-tutorial/linkedin_1.PNG)

> 자동 라이선스 할당을 사용하는 경우 응용 프로그램 인스턴스 및 라이선스 유형을 메모해 두어야 합니다. 라이선스는 모든 라이선스가 취득될 때까지 선착순으로 할당됩니다.

![LinkedIn Learning 프로비저닝](./media/active-directory-saas-linkedinlearning-provisioning-tutorial/linkedin_2.PNG)

5)  **토큰 생성**을 클릭합니다. **액세스 토큰** 필드 아래에 액세스 토큰이 표시됩니다.

6)  페이지를 나가기 전에 클립보드 또는 컴퓨터에 액세스 토큰을 저장합니다.

7) 다음으로, [Azure Portal](https://portal.azure.com)에 로그인하고 **Azure Active Directory > 엔터프라이즈 앱 > 모든 응용 프로그램** 섹션으로 이동합니다.

8) Single Sign-On에 대한 LinkedIn Learning을 이미 구성한 경우 검색 필드를 사용하여 LinkedIn Learning의 인스턴스를 검색합니다. 그렇지 않은 경우 **추가**를 선택하고 응용 프로그램 갤러리에서 **LinkedIn Learning**을 검색합니다. 검색 결과에서 LinkedIn Learning을 선택하고 응용 프로그램의 목록에 추가합니다.

9)  LinkedIn Learning의 인스턴스를 선택한 다음, **프로비전** 탭을 선택합니다.

10) **프로비전 모드**를 **자동**으로 설정합니다.

![LinkedIn Learning 프로비저닝](./media/active-directory-saas-linkedinlearning-provisioning-tutorial/linkedin_3.PNG)

11)  **관리자 자격 증명** 아래에서 다음 필드를 입력합니다.

* **테넌트 URL** 필드에 https://api.linkedin.com을 입력합니다.

* **비밀 토큰** 필드에 1단계에서 생성한 액세스 토큰을 입력하고 **연결 테스트**를 클릭합니다.

* 포털의 오른쪽 맨 위에 성공 알림이 표시됩니다.

12) 프로비전 오류 알림을 받을 개인 또는 그룹의 전자 메일 주소를 **알림 전자 메일** 필드에 입력하고 아래 확인란을 선택합니다.

13) **저장**을 클릭합니다. 

14) **특성 매핑** 섹션에서 Azure AD에서 LinkedIn Learning으로 동기화할 사용자 및 그룹 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 LinkedIn Learning에서 사용자 계정 또는 그룹을 일치시키는 데 사용됩니다. 저장 단추를 선택하여 변경 내용을 커밋합니다.

![LinkedIn Learning 프로비저닝](./media/active-directory-saas-linkedinlearning-provisioning-tutorial/linkedin_4.PNG)

15) LinkedIn Learning에 대한 Azure AD 프로비전 서비스를 사용하도록 설정하려면 **설정** 섹션에서 **프로비전 상태**를 **켜기**로 변경합니다.

16) **저장**을 클릭합니다. 

사용자 및 그룹 섹션에서 LinkedIn Learning에 할당된 모든 사용자 및/또는 그룹의 초기 동기화가 시작됩니다. 초기 동기화는 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 수행하는 데 더 많은 시간이 걸립니다. **동기화 세부 정보** 섹션을 사용하여 진행 상태를 모니터링하고 LinkedIn Learning 앱의 프로비전 서비스에서 수행하는 모든 작업을 설명하는 프로비전 활동 로그에 연결된 링크를 따를 수 있습니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](active-directory-saas-provisioning-reporting.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](active-directory-enterprise-apps-manage-provisioning.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)
