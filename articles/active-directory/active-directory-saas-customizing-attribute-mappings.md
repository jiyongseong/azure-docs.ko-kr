---
title: Azure AD 특성 매핑 사용자 지정 | Microsoft Docs
description: Azure Active Directory의 Saas 앱에 대한 어떤 특성 매핑이 있고 어떻게 비즈니스 요구 사항에 맞게 수정하는지를 알아봅니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 549e0b8c-87ce-4c9b-b487-b7bf0155dc77
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2018
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 78d971b47ffceb8d845f21a731176834f004f12c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Azure Active Directory에서 SaaS 응용 프로그램에 대한 사용자 프로비전 특성 매핑 사용자 지정
Microsoft Azure AD는 Salesforce, Google Apps 등과 같은 타사 SaaS 응용 프로그램에 프로비전을 하는 사용자에 대한 지원을 제공합니다. 타사 SaaS 응용 프로그램을 프로비전하는 사용자가 있을 경우 Azure Portal은 그 특성 값을 “특성 매핑”이라고 하는 구성 형태로 제어합니다.

Azure AD 사용자 개체와 각 SaaS 앱의 사용자 개체 사이에는 미리 구성된 특성 및 특성 매핑 세트가 있습니다. 일부 앱은 사용자 외에도 그룹 같은 다른 유형의 개체를 관리합니다. <br> 
 비즈니스 요구 사항에 따라 기본 특성 매핑을 사용자 지정할 수 있습니다. 즉, 기존의 특성 매핑을 변경 또는 삭제하거나 새 특성 매핑을 만들 수 있습니다.
 
## <a name="editing-user-attribute-mappings"></a>사용자 특성 매핑 편집

Azure AD 포털에서 **엔터프라이즈 응용 프로그램**의 **관리** 섹션에 있는 **프로비전** 아래 **매핑** 구성을 클릭하여 이 기능에 액세스할 수 있습니다.


![Salesforce][5] 

**매핑** 구성을 클릭하면 관련 **특성 매핑** 화면이 열립니다. SaaS 응용 프로그램에서 올바르게 작동하기 위해 요구되는 특성 매핑이 있습니다. 필수 특성인 경우 **삭제** 기능을 사용할 수 없습니다.


![Salesforce][6]  

위의 예제를 통해 Salesforce에서 관리된 개체의 **Username** 특성이 그 연결된 Azure Active Directory 개체의 **userPrincipalName** 값으로 채워지는 것을 볼 수 있습니다.

매핑을 클릭하여 기존 **특성 매핑**을 사용자 지정할 수 있습니다. 그러면 **특성 편집** 화면이 열립니다.

![Salesforce][7]  


### <a name="understanding-attribute-mapping-types"></a>특성 매핑 유형 이해
특성 매핑으로 타사 SaaS 응용 프로그램에서 특성이 채워지는 법을 제어합니다. 4 가지의 다른 매핑 형식이 지원됩니다.

* **직접** – 대상 특성이 Azure AD에서 연결된 개체의 특성 값으로 채워집니다.
* **상수** – 대상 특성이 지정된 특정 문자열로 채워집니다.
* **식** - 대상 특성이 스크립트 방식의 식의 결과에 따라 채워집니다. 
  자세한 내용은 [Azure Active Directory의 특성 매핑에 대한 식 작성](active-directory-saas-writing-expressions-for-attribute-mappings.md)을 참조하세요.
* **None** - 대상 특성이 수정되지 않고 남아있습니다. 그러나 대상 특성이 비어 있으면 지정된 기본 값으로 채워집니다.

이 네 가지 기본 특성 맵 형식 외에도 사용자 지정 특성 매핑은 **기본** 값 할당(선택 사항)의 개념을 지원합니다. 기본 값 할당은 Azure AD에나 대상 개체에 값이 없을 경우 대상 특성이 값으로 채워지는지 확인해줍니다. 이 값을 비워두는 것이 가장 일반적인 구성입니다.


### <a name="understanding-attribute-mapping-properties"></a>특성 매핑 속성 이해

이전 섹션에서 특성 매핑 유형 속성에 대해 이미 소개한 바 있습니다.
이 속성 외에도, 특성 매핑은 다음 특성도 지원합니다.

- **원본 특성** - 원본 시스템의 사용자 특성(예: Azure Active Directory).
- **대상 특성** – 대상 시스템의 사용자 특성(예: ServiceNow).
- **이 특성을 사용하여 개체 일치** – 원본 및 대상 시스템 간에 사용자를 고유하게 식별하는 데 이 매핑을 사용할지 여부를 나타냅니다. 일반적으로 대상 응용 프로그램에서 username 필드에 매핑되는 Azure AD의 userPrincipalName 또는 메일 특성에서 설정됩니다.
- **일치 우선 순위** – 여러 일치 특성을 설정할 수 있습니다. 일치 특성이 여러 개 있으면 이 필드에 정의된 순서대로 평가됩니다. 일치 항목이 발견되는 즉시 더 이상 일치 특성을 평가하지 않습니다.
- **이 매핑 적용**
    - **항상** – 사용자 만들기 및 업데이트 작업 시 이 매핑을 적용합니다.
    - **만들기 작업 시에만** - 사용자 만들기 작업 시에만 이 매핑을 적용합니다.


## <a name="editing-group-attribute-mappings"></a>그룹 특성 매핑 편집

ServiceNow, Box 및 Google Apps와 같은 일부 응용 프로그램에서는 사용자 개체 외에 그룹 개체를 프로비전하는 기능도 지원합니다. 그룹 개체에는 그룹 멤버 외에도 표시 이름 및 이메일 별칭과 같은 그룹 속성이 포함될 수 있습니다.

![ServiceNow][8]  

**매핑**에서 그룹 매핑을 선택하거나 **특성 매핑** 화면에서 **사용**을 원하는 옵션으로 설정하여 그룹 프로비전을 선택적으로 사용하거나 사용하지 않도록 설정할 수 있습니다.

그룹 개체의 일부로 프로비전된 특성은 이전에 설명한 사용자 개체와 동일한 방식으로 사용자 지정할 수 있습니다. 

>[!TIP]
>그룹 개체(속성 및 멤버) 프로비전은 응용 프로그램에 [그룹을 할당](active-directory-coreapps-assign-user-azure-portal.md)하는 것과 별개의 개념입니다. 응용 프로그램에 그룹을 할당할 수 있지만 그룹에 포함된 사용자 개체만 프로비전할 수 있습니다. 할당에서 그룹을 사용하기 위해 전체 그룹 개체를 프로비전할 필요는 없습니다.


## <a name="editing-the-list-of-supported-attributes"></a>지원되는 특성 목록 편집

지정된 응용 프로그램에 지원되는 사용자 특성은 미리 구성됩니다. 응용 프로그램의 사용자 관리 API는 대부분 스키마 검색을 지원하지 않으므로 Azure AD 프로비전 서비스는 응용 프로그램에 대한 호출을 수행하여 지원되는 특성 목록을 동적으로 생성할 수 없습니다. 

그러나 일부 응용 프로그램은 사용자 지정 특성을 지원합니다. Azure AD 프로비전 서비스가 사용자 지정 특성에 대한 읽기와 쓰기를 수행하도록 하려면 **특성 매핑** 화면 하단의 **고급 옵션 표시** 확인란을 사용하여 Azure Portal에 정의를 입력해야 합니다.

특성 목록의 사용자 지정을 지원하는 응용 프로그램 및 시스템은 다음과 같습니다.

* Salesforce
* ServiceNow
* Workday
* Azure Active Directory
* 온-프레미스 Active Directory(Workday 사용자 프로비전 커넥터의 일부)
* [SCIM 2.0](https://tools.ietf.org/html/rfc7643)을 지원하는 앱([코어 스키마](https://tools.ietf.org/html/rfc7643)에 정의된 특성을 추가해야 함)

>[!NOTE]
>응용 프로그램과 시스템의 스키마를 사용자 지정했으며, 사용자 지정 특성이 정의된 방식에 대한 실무 지식이 있는 관리자일 경우에만 지원되는 특성 목록을 편집하는 것이 좋습니다. 응용 프로그램이나 시스템에서 제공하는 API 및 개발자 도구를 잘 알아야 하는 경우가 많습니다. 

![편집기][9]  

지원되는 특성 목록을 편집할 때는 다음과 같은 속성이 제공됩니다.

* **이름** - 대상 개체의 스키마에 정의된 특성의 시스템 이름입니다. 
* **형식** - 대상 개체의 스키마에 정의된 대로 특성이 저장하는 데이터 형식입니다. 다음 중 하나일 수 있습니다.
   * *이진* - 특성에 이진 데이터가 포함됩니다.
   * *부울* - 특성에 True 또는 False 값이 포함됩니다.
   * *날짜/시간* - 특성에 날짜 문자열이 포함됩니다.
   * *정수* - 특성에 정수가 포함됩니다.
   * *참조* - 특성에 대상 응용 프로그램의 다른 테이블에 저장된 값을 참조하는 ID가 포함됩니다.
   * *문자열* - 특성에 텍스트 문자열이 포함됩니다. 
* **기본 키 여부** - 대상 개체의 스키마에 특성이 기본 키 필드로 정의되어 있는지 여부입니다.
* **필수 여부** - 대상 응용 프로그램 또는 시스템에서 특성을 채워야 하는지 여부입니다.
* **다중 값 여부** - 특성이 다중 값을 지원하는지 여부입니다.
* **대소문자 구분 여부** - 특성이 대/소문자 구분 방식으로 평가되는지 여부입니다.
* **API 식** - 특정 프로비전 커넥터(예: Workday)에 대한 설명서에 달리 지정된 경우 외에는 사용하지 마세요.
* **참조된 개체 특성** - 참조 형식 특성일 경우 이 메뉴를 사용하여 이 특성과 연결된 값이 포함된 대상 응용 프로그램의 테이블과 특성을 선택할 수 있습니다. 예를 들어 "Department"라는 특성의 저장된 값이 별도의 "Departments" 테이블에 있는 개체를 참조하는 경우 "Departments.Name"을 선택합니다. 지정된 응용 프로그램에 대해 지원되는 참조 테이블 및 기본 ID 필드는 미리 구성되며, 현재 Azure Portal을 사용하여 편집할 수는 없지만 [Graph API](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes)를 사용하여 편집할 수는 있습니다.

새 특성을 추가하려면 지원되는 특성 목록의 끝 부분으로 스크롤하고, 제공된 입력을 사용하여 위의 필드를 채우고, **특성 추가**를 선택합니다. 특성을 다 추가했으면 **저장**을 선택합니다. 그런 다음, **프로비전** 탭을 다시 로드해야 특성 매핑 편집기에서 새 특성을 볼 수 있습니다.

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>기본 특성 및 특성 매핑 복원

새로 시작하여 기존 매핑을 다시 기본 상태로 설정해야 하는 경우 **기본 매핑 복원** 확인란을 선택하고 구성을 선택하면 됩니다. 그러면 응용 프로그램 갤러리에서 Azure AD 테넌트에 응용 프로그램이 방금 추가된 것처럼 모든 매핑이 설정됩니다. 

이 옵션을 선택하면 프로비전 서비스가 실행 중일 때 모든 사용자의 재동기화가 효과적으로 적용됩니다. 

>[!IMPORTANT]
>이 옵션을 호출하기 전에 **프로비전 상태**를 **꺼짐**으로 설정할 것을 적극 권장합니다.


## <a name="what-you-should-know"></a>알아야 할 사항

* Microsoft Azure AD는 동기화 프로세스의 효과적인 구현을 제공합니다. 초기화된 환경에서 동기화 주기 중에는 업데이트가 필요한 개체만 처리됩니다. 

* 특성 매핑 업데이트는 동기화 주기의 성능에 영향을 줍니다. 특성 매핑 구성의 업데이트는 모든 관리된 개체를 다시 평가해야 합니다. 

* 특성 매핑에 대한 연속 변경 횟수를 최소로 유지하는 것이 가장 좋은 방법입니다.


## <a name="next-steps"></a>다음 단계

* [SaaS 앱에 자동화된 사용자 프로비전/프로비전 해제](active-directory-saas-app-provisioning.md)
* [특성 매핑에 대한 식 작성](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [사용자 프로 비전에 대 한 필터 범위 지정](active-directory-saas-scoping-filters.md)
* [SCIM를 사용하여 Azure Active Directory으로부터 응용 프로그램에 사용자 및 그룹의 자동 프로비전 사용](active-directory-scim-provisioning.md)
* [SaaS App을 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)

<!--Image references-->
[5]: ./media/active-directory-saas-customizing-attribute-mappings/21.png
[6]: ./media/active-directory-saas-customizing-attribute-mappings/22.png
[7]: ./media/active-directory-saas-customizing-attribute-mappings/23.png
[8]: ./media/active-directory-saas-customizing-attribute-mappings/24.png
[9]: ./media/active-directory-saas-customizing-attribute-mappings/25.PNG

