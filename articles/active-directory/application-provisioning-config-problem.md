---
title: Azure AD 갤러리 응용 프로그램에 대해 사용자 프로비전 구성 문제 | Microsoft Docs
description: Azure AD 응용 프로그램 갤러리에 이미 나열된 응용 프로그램에 대한 사용자 프로비전을 구성할 때 발생하는 일반적인 문제를 해결하는 방법
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 9fcfee9cbbeb01422e7d9e92d90b908e054f96da
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>Azure AD 갤러리 응용 프로그램에 대해 사용자 프로비전 구성 문제

앱에 대해 [자동 사용자 프로비전](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)을 구성하려면(지원되는 경우) 자동 프로비전에 대해 응용 프로그램을 준비하는 특정 지침을 따라야 합니다. 그런 다음 Azure Portal에서 프로비전 서비스를 구성하여 사용자 계정을 응용 프로그램에 동기화할 수 있습니다.

항상 응용 프로그램의 프로비전 설정에 대한 설정 자습서를 찾는 것부터 시작해야 합니다. 그런 다음 해당 단계에 따라 앱과 Azure AD를 구성하여 프로비전 연결을 만듭니다. 앱 자습서 목록은 [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)에서 찾을 수 있습니다.

## <a name="how-to-see-if-provisioning-is-working"></a>프로비전이 작동하는지 확인하는 방법 

서비스가 구성되면 서비스 작업에 대한 대부분의 정보는 다음 두 곳에서 확인할 수 있습니다.

-   **감사 로그** – 프로비전 감사 로그는 프로비전 범위에 있는 할당된 사용자에 대해 Azure AD 쿼리를 비롯하여 프로비전 서비스에서 수행한 모든 작업을 기록합니다. 시스템 간 사용자 개체를 비교하여 해당 사용자의 존재에 대해 대상 앱을 쿼리합니다. 그런 다음 비교를 기반으로 대상 시스템에서 사용자 계정을 추가, 업데이트 또는 비활성화합니다. 프로비전 감사 로그는 Azure Portal의 **Azure Active Directory&gt;엔터프라이즈 응용 프로그램&gt; \[응용 프로그램 이름\] &gt;감사 로그** 탭에서 액세스할 수 있습니다. **계정 프로비전** 범주의 로그를 필터링하여 해당 앱의 프로비전 이벤트만 볼 수 있습니다.

-   **프로비전 상태 –** 해당 앱에 대해 마지막 프로비전 실행에 대한 요약은 서비스 설정의 화면 맨 아래에 있는 **Azure Active Directory &gt; 엔터프라이즈 응용 프로그램 &gt; \[응용 프로그램 이름\] &gt;프로비전** 섹션에서 확인할 수 있습니다. 이 섹션에서는 얼마나 많은 사용자 (및/또는 그룹)가 두 시스템 간에 동기화되고 있으며 오류가 있는지 요약합니다. 오류 세부 정보는 감사 로그에 표시됩니다. 프로비전 상태는 Azure AD와 앱 간에 전체 초기 동기화가 완료될 때까지 표시되지 않습니다.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>고려해야 할 프로비전 관련 일반적인 문제 영역

다음은 시작 위치를 파악한 경우 검색할 수 있는 일반적인 문제 영역 목록입니다.

* [프로비전 서비스가 시작하지 않는 것 같음](#provisioning-service-does-not-appear-to-start)
* [앱 자격 증명이 작동하지 않아 구성을 저장할 수 없음](#can’t-save-configuration-due-to-app-credentials-not-working)
* [사용자가 할당된 경우에도 감사 로그에 사용자가 “생략”되고 프로비전되지 않았다고 표시됨](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>프로비전 서비스가 시작하지 않는 것 같음

Azure Portal의 **Azure Active Directory &gt; 엔터프라이즈 응용 프로그램 &gt; \[응용 프로그램 이름\] &gt;프로비전** 섹션에서 **프로비전 상태**를 **켜기**로 설정하는 경우 그러나 이후 다시 로드 후 기타 상태 세부 정보가 표시되지 않습니다. 서비스가 실행되고 있지만 아직 초기 동기화가 완료되지 않았을 수 있습니다. 위에 설명된 **감사 로그**를 확인하여 서비스에서 수행 중인 작업과 오류가 있는지 확인합니다.

>[!NOTE]
>초기 동기화는 Azure AD 디렉터리의 크기와 프로비전 범위에 있는 사용자 수에 따라 20분에서 수 시간이 걸릴 수 있습니다. 프로비전 서비스는 초기 동기화 후에 두 시스템의 상태를 나타내는 워터마크를 저장하므로, 초기 동기화 이후의 후속 동기화가 빨라져 후속 동기화의 성능이 향상됩니다.
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>앱 자격 증명이 작동하지 않아 구성을 저장할 수 없음

프로비전을 작동하도록 하려면 Azure AD에서 해당 앱에서 제공한 사용자 관리 API에 연결할 수 있도록 유효한 자격 증명이 필요합니다. 이러한 자격 증명이 작동하지 않거나 자격 증명이 무엇인지 모르는 경우 이전에 설명한 이 앱 설정에 대한 자습서를 검토하세요.

## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>사용자가 할당된 경우에도 감사 로그에 사용자가 생략되고 프로비전되지 않았다고 표시됨

감사 로그에 사용자가 “생략”으로 표시되면 로그 메시지의 확장되는 세부 정보를 읽고 이유를 확인해야 합니다. 다음은 일반적인 원인과 해결 방법입니다.

-   **특성 값에 따라 사용자를 필터링하는** **범위 지정 필터가 구성되었습니다**. 필터를 범위 지정하는 방법에 대한 자세한 내용은 <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>를 참조하세요.

-   **사용자가 “실질적으로 권한을 부여받지 않았습니다.”** 이 특정 오류 메시지가 표시되는 경우 Azure AD에 저장된 사용자 할당 레코드에 문제가 있기 때문입니다. 이 문제를 해결하려면 앱에서 사용자(또는 그룹)의 할당을 취소하고 다시 할당하세요. 할당에 대한 자세한 내용은 <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>을 참조하세요.

-   **사용자에게 필요한 특성이 누락되었거나 입력되지 않았습니다.** 프로비전을 설정할 때 고려해야 할 중요한 사항은 Azure AD에서 응용 프로그램으로 이동하는 사용자(또는 그룹)를 정의하는 특성 매핑 및 워크플로를 검토하고 구성하는 것입니다. 여기에는 두 시스템 간 사용자/그룹을 고유하게 식별하고 일치하는 데 사용되는 “일치하는 속성” 설정이 포함됩니다. 이 중요한 프로세스에 대한 자세한 정보는 <https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings>을 참조하세요.

   * **그룹에 대한 특성 매핑:** 일부 응용 프로그램에 대해 지원되는 경우, 구성원 외에 그룹 이름 및 그룹 세부 정보 프로비전. **프로비전** 탭에 표시된 그룹 개체의 **매핑**을 사용하거나 사용하지 않도록 설정하여 이 기능을 사용하거나 사용하지 않을 수 있습니다. 그룹 프로비전을 사용하는 경우 특성 매핑을 검토하여 “일치하는 ID”에 적절한 필드를 사용하는지 확인하세요. 이는 표시 이름이나 메일 별칭일 수 있습니다. Azure AD에 일치하는 속성이 비어 있거나 그룹에 대해 입력되어 있지 않은 경우 그룹 및 구성원이 프로비전되지 않습니다.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory를 사용하여 SaaS 응용 프로그램의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](active-directory-saas-app-provisioning.md)
