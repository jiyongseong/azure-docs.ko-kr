---
title: "사용자가 Azure AD 갤러리 응용 프로그램에 프로비전되지 않음 | Microsoft Docs"
description: "Azure AD로 사용자 프로비전을 구성한 Azure AD 갤러리 응용 프로그램에 사용자가 표시되지 않는 일반적인 문제를 해결하는 방법"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: asteen
ms.openlocfilehash: a36d60b8915ae0b46226bb2127829a8f1767daba
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>사용자가 Azure AD 갤러리 응용 프로그램에 프로비전되지 않음

앱에 연결할 Azure AD에 제공된 앱 자격 증명이 유효한지 확인을 비롯하여 응용 프로그램에 대해 자동 프로비전이 구성됩니다. 사용자 및/또는 그룹은 앱에 프로비전되고 다음 사항으로 결정됩니다.

-   응용 프로그램에 **할당된** 사용자 및 그룹. 할당에 대한 자세한 내용은 [Azure Active Directory에서 엔터프라이즈 앱에 사용자 또는 그룹 할당](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)을 참조하세요.

-   **특성 매핑**을 사용하도록 설정했으며 Azure AD의 유효한 특성을 앱에 동기화하도록 구성했는지 여부. 특성 매핑에 대한 자세한 내용은 [Azure Active Directory에서 SaaS 응용 프로그램에 대한 사용자 프로비전 특성 매핑 사용자 지정](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)을 참조하세요.

-   특정 특성 값을 기반으로 사용자를 필터링하는 **범위 지정 필터**가 있는지 여부. 범위 지정 필터에 대한 자세한 내용은 [범위 지정 필터를 사용한 특성 기반 응용 프로그램 프로비전](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters)을 참조하세요.

사용자가 프로비전되지 않으면 Azure AD의 감사 로그를 참조하고 특정 사용자에 대한 로그 항목을 검색하세요.

프로비전 감사 로그는 Azure Portal의 **Azure Active Directory&gt;엔터프라이즈 응용 프로그램&gt; \[응용 프로그램 이름\] &gt;감사 로그** 탭에서 액세스할 수 있습니다. **계정 프로비저닝** 범주의 로그를 필터링하여 해당 앱의 프로비저닝 이벤트만 볼 수 있습니다. 특성 매핑에서 사용자에 대해 구성된 “일치 ID”를 기반으로 사용자를 검색할 수 있습니다. 예를 들어 Azure AD 측에서 “사용자 계정 이름” 또는 “메일 주소”를 일치하는 특성으로 구성하고 프로비전하지 않는 사용자의 값이 “audrey@contoso.com”인 경우 감사 로그에서 “audrey@contoso.com”을 검색한 후 반환된 항목을 검토합니다.

프로비전 감사 로그는 프로비전 범위에 있는 할당된 사용자에 대해 Azure AD 쿼리, 해당 사용자의 존재에 대해 대상 앱 쿼리, 시스템 간의 사용자 객체 비교를 비롯하여 프로비전 서비스에서 수행한 모든 작업을 기록합니다. 그런 다음 비교를 기반으로 대상 시스템에서 사용자 계정을 추가, 업데이트 또는 비활성화합니다.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>고려해야 할 프로비전 관련 일반적인 문제 영역

다음은 시작 위치를 파악한 경우 검색할 수 있는 일반적인 문제 영역 목록입니다.

* [프로비전 서비스가 시작하지 않는 것 같음](#provisioning-service-does-not-appear-to-start)
* [사용자가 할당된 경우에도 감사 로그에 사용자가 생략되고 프로비전되지 않았다고 표시됨](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>프로비전 서비스가 시작하지 않는 것 같음

Azure Portal의 **Azure Active Directory &gt; 엔터프라이즈 응용 프로그램 &gt; \[응용 프로그램 이름\] &gt;프로비전** 섹션에서 **프로비전 상태**를 **켜기**로 설정하는 경우 이후 다시 로드 후 기타 상태 세부 정보가 해당 페이지에 표시되지 않으면 서비스가 실행 중이지만 아직 초기 동기화를 완료하지 않았을 수 있습니다. 위에 설명된 **감사 로그**를 확인하여 서비스에서 수행 중인 작업과 오류가 있는지 확인합니다.

>[!NOTE]
>초기 동기화는 Azure AD 디렉터리의 크기와 프로비전 범위에 있는 사용자 수에 따라 20분에서 수 시간이 걸릴 수 있습니다. 프로비전 서비스는 초기 동기화 후에 두 시스템의 상태를 나타내는 워터마크를 저장하므로, 초기 동기화 이후의 후속 동기화가 빨라집니다. 이를 통해 후속 동기화의 성능이 향상됩니다.
>
>

## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>사용자가 할당된 경우에도 감사 로그에 사용자가 생략되고 프로비전되지 않았다고 표시됨

감사 로그에 사용자가 “생략”으로 표시되면 로그 메시지의 확장되는 세부 정보를 읽고 이유를 확인해야 합니다. 다음은 일반적인 원인과 해결 방법입니다.

-   **특성 값에 따라 사용자를 필터링하는** **범위 지정 필터가 구성되었습니다**. 범위 지정 필터에 대한 자세한 내용은 <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>를 참조하세요.

-   **사용자가 “실질적으로 권한을 부여받지 않았습니다.”** 이 특정 오류 메시지가 표시되는 경우 Azure AD에 저장된 사용자 할당 레코드에 문제가 있기 때문입니다. 이 문제를 해결하려면 앱에서 사용자(또는 그룹)의 할당을 취소하고 다시 할당하세요. 할당에 대한 자세한 내용은 <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>을 참조하세요.

-   **사용자에게 필요한 특성이 누락되었거나 입력되지 않았습니다.** 프로비전을 설정할 때 고려해야 할 중요한 사항은 Azure AD에서 응용 프로그램으로 이동하는 사용자(또는 그룹)를 정의하는 특성 매핑 및 워크플로를 검토하고 구성하는 것입니다. 여기에는 두 시스템 간 사용자/그룹을 고유하게 식별하고 일치하는 데 사용되는 “일치하는 속성” 설정이 포함됩니다. 이 중요한 프로세스에 대한 자세한 내용은 [Azure Active Directory에서 SaaS 응용 프로그램에 대한 사용자 프로비전 특성 매핑 사용자 지정](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)을 참조하세요.

  * **그룹에 대한 특성 매핑:** 일부 응용 프로그램에 대해 지원되는 경우, 구성원 외에 그룹 이름 및 그룹 세부 정보 프로비전. **프로비전** 탭에 표시된 그룹 개체의 **매핑**을 사용하거나 사용하지 않도록 설정하여 이 기능을 사용하거나 사용하지 않을 수 있습니다. 그룹 프로비전을 사용하는 경우 특성 매핑을 검토하여 “일치하는 ID”에 적절한 필드를 사용하는지 확인하세요. 이는 표시 이름이나 메일 별칭일 수 있습니다. Azure AD에 일치하는 속성이 비어 있거나 그룹에 대해 입력되어 있지 않은 경우 그룹 및 구성원이 프로비전되지 않습니다.

## <a name="next-steps"></a>다음 단계
[Azure AD Connect 동기화: 선언적 프로비전 이해](active-directory-aadconnectsync-understanding-declarative-provisioning.md)

