---
title: Azure AD B2C에서 나이 게이팅 사용 | Microsoft Docs
description: 응용 프로그램을 사용하여 미성년자를 식별하는 방법을 알아봅니다.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/29/2018
ms.author: davidmu
ms.openlocfilehash: 9186579126525cc269f7e3f9e778e06902b30eb4
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34261285"
---
#<a name="using-age-gating-in-azure-ad-b2c"></a>Azure AD B2C에서 나이 게이팅 사용

>[!IMPORTANT]
>이 기능은 현재 비공개 미리 보기입니다.  새로 제공되는 [서비스 블로그](https://blogs.msdn.microsoft.com/azureadb2c/)를 참조하거나 AADB2CFeedback@microsoft.com에 문의하세요.  프로덕션 디렉터리에서는 사용하지 마세요. 일반 공급으로 전환되기 전에는 이 새 기능을 사용하면 데이터가 손실되거나 동작이 예상치 않게 변경될 수 있습니다.  
>

##<a name="age-gating"></a>나이 게이팅
나이 게이팅을 통해 Azure AD B2C를 사용하여 응용 프로그램에서 미성년자를 식별할 수 있습니다.  사용자가 응용 프로그램에 로그인하지 못하게 차단할 수도 있고 또는 사용자의 연령 그룹 및 보호자 동의 상태를 식별하는 추가 클레임을 가지고 응용 프로그램으로 돌아오는 것을 허용할 수도 있습니다.  

>[!NOTE]
>보호자 동의는 `consentProvidedForMinor`라고 하는 사용자 특성에서 추적됩니다.  Graph API를 통해 이 속성을 업데이트할 수 있으며 `legalAgeGroupClassification`을 업데이트할 때 이 필드를 사용합니다.
>

##<a name="setting-up-your-directory-for-age-gating"></a>디렉터리에 나이 게이팅 설정
사용자 흐름에서 나이 게이팅을 사용하려면 추가 속성을 갖도록 디렉터리를 구성해야 합니다. 이 작업은 메뉴의 `Properties`를 통해 수행할 수 있습니다(비공개 미리 보기를 사용하는 경우에만 사용 가능).  
1. Azure AD B2C 확장에서 왼쪽 메뉴의 테넌트 **속성**을 클릭합니다.
2. **나이 게이팅** 섹션에서 **구성** 단추를 클릭합니다.
3. 작업이 완료될 때까지 기다리면 디렉터리에 나이 게이팅이 설정됩니다.

##<a name="enabling-age-gating-in-your-user-flow"></a>사용자 흐름에서 나이 게이팅 사용
나이 게이팅을 사용하도록 디렉터리가 설정되면 미리 보기 버전의 사용자 흐름에서 이 기능을 사용할 수 있습니다.  이 기능을 사용하려면 기존 형식의 사용자 흐름과 호환되지 않는 변경 작업이 필요합니다.  다음 단계에 따라 나이 게이팅을 사용하도록 설정합니다.
1. 미리 보기 사용자 흐름을 만듭니다.
2. 만들었으면 메뉴에서 **속성** 으로 이동합니다.
3. **나이 게이팅** 섹션에서 토글을 눌러 이 기능을 사용하도록 설정합니다.
4. 그런 다음, 미성년자로 확인된 사용자를 어떻게 관리할 것인지 선택할 수 있습니다.

##<a name="what-does-enabling-age-gating-do"></a>나이 게이팅을 사용하면 어떻게 되나요?
사용자 흐름에 나이 게이팅을 사용하면 사용자 경험이 바뀝니다.  이제 사용자는 등록할 때 사용자 흐름에 대해 구성된 사용자 특성과 함께 생년월일 및 거주 국가를 입력해야 합니다.  이전에 생년월일 및 거주 국가를 입력하지 않은 사용자는 다음에 로그인할 때 이 정보를 입력해야 합니다.  Azure AD B2C는 이 두 값을 사용하여 사용자가 미성년자인지 식별하고 `ageGroup` 필드를 업데이트하며, 가능한 값은 `null`, `Undefined`, `Minor`, `Adult` 및 `NotAdult`입니다.  그리고 `ageGroup` 및 `consentProvidedForMinor` 필드를 사용하여 `legalAgeGroupClassification`을 계산합니다. 

##<a name="age-gating-options"></a>나이 게이팅 옵션
Azure AD B2C가 보호자 동의 없는 미성년자를 차단하도록 선택할 수도 있고, 허용하되 이들이 할 수 있는 작업을 응용 프로그램에서 결정하게 할 수도 있습니다.  

###<a name="allowing-minors-without-parental-consent"></a>보호자 동의서 없는 미성년자 허용
등록, 로그인 또는 둘 다 사용하는 사용자 흐름의 경우 보호자 동의 없는 미성년자가 응용 프로그램에 들어오는 것을 허용 수 있습니다.  `legalAgeGroupClassification` 클레임을 사용하여 보호자 동의 없는 미성년자가 정상적으로 로그인 또는 등록하고 Azure AD B2C에서 ID 토큰을 발급하도록 허용할 수 있습니다.  이 클레임을 사용하면 보호자 동의서를 받는(그리고 `consentProvidedForMinor` 필드를 업데이트하는) 경험처럼 이러한 사용자가 하게 될 경험을 선택할 수 있습니다.

###<a name="blocking-minors-without-parental-consent"></a>보호자 동의서 없는 미성년자 차단
등록, 로그인 또는 둘 다 사용하는 사용자 흐름의 경우 보호자 동의 없는 미성년자를 응용 프로그램에서 차단할 수 있습니다.  Azure AD B2C에는 차단된 사용자를 처리하는 두 가지 옵션이 있습니다.
* 응용 프로그램에 다시 JSON 보내기 - 이 옵션은 미성년자가 차단한 응용 프로그램에 다시 응답을 보냅니다.
* 오류 페이지 표시 - 응용 프로그램에 액세스할 수 없다는 정보를 제공하는 페이지가 사용자에게 표시됩니다.

##<a name="known-issues"></a>알려진 문제
###<a name="format-for-the-response-when-a-minor-is-blocked"></a>미성년자 차단 시 응답 형식.
현재는 응답 형식이 올바르게 구성되지 않으며, 이 버그는 향후 업데이트에서 해결될 예정입니다.

###<a name="deleting-specific-attributes-that-were-added-during-setup-can-make-your-directory-unable-to-use-age-gating"></a>설정 과정에서 추가된 특정 특성을 삭제하면 디렉터리에 나이 게이팅을 사용할 수 없습니다.
나이 게이팅 설정 시 `Properties`의 옵션을 통해 디렉터리를 구성했습니다.  Graph를 통해 `legalCountry` 또는 `dateOfBirth`를 삭제하면 디렉터리에서 더 이상 나이 게이팅을 사용할 수 없고 이러한 속성을 다시 만들 수 없습니다.

###<a name="list-of-countries-is-incomplete"></a>국가 목록은 아직 미완성
현재 legalCountry의 국가 목록은 아직 미완성이며, 향후 업데이트에서 나머지 국가가 추가될 예정입니다.