---
title: Azure Active Directory 사용 약관 | Microsoft Docs
description: Azure AD 사용 약관은 귀하와 귀하의 회사가 Azure AD 서비스 사용자에게 사용 약관을 제공할 수 있도록 허용합니다.
services: active-directory
author: billmath
manager: mtillman
editor: ''
ms.assetid: d55872ef-7e45-4de5-a9a0-3298e3de3565
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/15/2018
ms.author: billmath
ms.openlocfilehash: 428d70474ba928a9e0c774aeb16395ef6a4cea2e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34192958"
---
# <a name="azure-active-directory-terms-of-use-feature"></a>Azure Active Directory 사용 약관 기능
Azure AD 사용 약관은 조직이 최종 사용자에게 정보를 제공하는 데 사용할 수 있는 간단한 방법을 제공합니다.  이 프레젠테이션은 사용자가 법률 또는 규정 준수 요구 사항에 대한 관련 고지 사항을 볼 수 있게 해줍니다.

Azure AD 사용 약관은 pdf 형식을 사용하여 콘텐츠를 제공합니다.   이 pdf는 기존 계약서와 같이 사용자가 로그인하는 동안 최종 사용자 계약을 수집할 수 있는 모든 콘텐츠가 될 수 있습니다.  응용 프로그램, 사용자 그룹에 대해 또는 다른 용도로 여러 사용 약관이 있는 경우 본 사용 약관을 사용할 수 있습니다.

이 문서의 나머지 부분에서는 Azure AD 사용 약관을 사용하는 방법을 설명합니다.  

## <a name="why-use-azure-ad-terms-of-use"></a>Azure AD 사용 약관을 사용하는 이유
직원 또는 게스트가 서비스에 액세스하기 전에 사용 약관에 동의하도록 하는 데 어려움이 있나요? 회사 사용 약관에 누가 동의 또는 동의하지 않았는지 파악하는 데 도움이 필요한가요?  Azure AD 사용 약관은 조직이 최종 사용자에게 정보를 제공하는 데 사용할 수 있는 간단한 방법을 제공합니다.  이 프레젠테이션은 최종 사용자가 법률 또는 규정 준수 요구 사항에 대한 관련 고지 사항을 볼 수 있게 해줍니다.

다음과 같은 시나리오에서 Azure AD 사용 약관을 사용할 수 있습니다.
-   조직의 모든 사용자에 대한 일반 사용 약관
-   사용자 특성을 기반으로 하는 특정 사용 약관(예: 의사 및 간호사, 국내 및 국외 직원, [동적 그룹](https://azure.microsoft.com/updates/azure-active-directory-dynamic-membership-for-groups)으로 수행).
-   Salesforce와 같은 비즈니스 영향력이 높은 앱에 대한 액세스를 기반으로 하는 특정 사용 약관


## <a name="prerequisites"></a>필수 조건
다음 단계에 따라 Azure AD 사용 약관을 구성합니다.

1. Azure AD 사용 약관을 구성하려는 디렉터리에 대한 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자를 사용하여 Azure AD에 로그인합니다.
2. 디렉터리에 Azure AD Premium P1, P2, EMS E3 또는 EMS E5 구독이 있는지 확인합니다.  그렇지 않은 경우 [Azure AD Premium 다운로드](active-directory-get-started-premium.md)하거나 [평가판을 시작](https://azure.microsoft.com/trial/get-started-active-directory/)합니다.
3. [https://aka.ms/catou](https://aka.ms/catou)에서 Azure AD 사용 약관 대시보드를 확인합니다.

>[!IMPORTANT]
>조건부 액세스 정책 제어(사용 조건 포함)는 서비스 계정에 적용을 지원하지 않습니다.  우리는 조건부 액세스 정책에서 모든 서비스 계정을 배제하도록 추천합니다.

## <a name="add-company-terms-of-use"></a>회사 사용 약관 추가
사용 약관을 완성했으면 다음 절차에 따라 추가합니다.

### <a name="to-add-terms-of-use"></a>사용 약관을 추가하려면
1. [https://aka.ms/catou](https://aka.ms/catou)에서 대시보드로 이동합니다.
2. 추가를 클릭합니다.</br>
![TOU 추가](media/active-directory-tou/tou12.png)
3. 사용 약관에 대한 **이름**을 입력합니다.
4. **표시 이름**을 입력합니다.  이 헤더는 사용자가 로그인할 때 표시됩니다.
5. 완료된 사용 약관 pdf를 **찾아** 선택합니다.  권장된 글꼴 크기는 24입니다.
6. 사용 약관의 언어를 **선택**합니다.  언어 옵션을 사용하면 사용 약관을 다양한 언어로 여러 개 업로드할 수 있습니다.  최종 사용자에게 표시되는 사용 약관의 버전은 브라우저 기본 설정에 기반합니다.
7. **사용자가 사용 약관을 확장해야 함**에 대해 설정 또는 해제를 선택합니다.  이 설정을 완료하면 최종 사용자가 사용 약관에 동의하기 전에 사용 약관을 봐야 합니다.
8. **조건부 액세스**에서 드롭다운의 템플릿 또는 사용자 지정 조건부 액세스 정책을 선택하여 업로드된 사용 약관을 **강제 적용**할 수 있습니다.  사용자 지정 조건부 액세스 정책을 통해 특정 클라우드 응용 프로그램 또는 사용자 그룹에까지 세분화된 사용 약관을 설정할 수 있습니다.  자세한 내용은 [조건부 액세스 정책 구성](active-directory-conditional-access-best-practices.md)을 참조하세요.
9. **만들기**를 클릭합니다.
10. 사용자 지정 조건부 액세스 템플릿을 선택한 경우 CA 정책을 사용자 지정할 수 있는 새 화면이 나타납니다.
11. 이제 새 사용 약관이 표시됩니다.</br>

![TOU 추가](media/active-directory-tou/tou3.png)

## <a name="delete-terms-of-use"></a>사용 약관 삭제
다음 절차에 따라 이전 사용 약관을 제거 또는 삭제할 수 있습니다.

### <a name="to-delete-terms-of-use"></a>사용 약관을 삭제하려면
1. [https://aka.ms/catou](https://aka.ms/catou)에서 대시보드로 이동합니다.
2. 제거할 사용 약관을 선택합니다.
3. **삭제**를 클릭합니다.
4. 새 사용 약관이 더 이상 표시되지 않습니다.


## <a name="viewing-current-user-status"></a>현재 사용자 상태 보기
사용 약관은 동의한 사용자 및 거부한 사용자 수를 보여줍니다.

![감사 이벤트](media/active-directory-tou/tou15.png)

**동의** 또는 **거부** 아래의 숫자를 클릭하면 사용자의 현재 상태를 볼 수 있습니다.

![감사 이벤트](media/active-directory-tou/tou16.png)

## <a name="audit-terms-of-use"></a>사용 약관 감사
현재 상태 외에도 동의 및 거부 기록을 볼 수 있도록 Azure AD 사용 약관에서는 간편한 감사를 제공합니다.  이 감사를 사용하면 누가, 언제 사용 약관에 동의했는지 확인할 수 있습니다.  

현재 하려는 작업에 따라 두 가지 방법으로 감사 작업을 수행할 수 있습니다.  


감사를 시작하려면 다음 절차를 따릅니다.

### <a name="to-audit-terms-of-use"></a>사용 약관을 감사하려면
1. [https://aka.ms/catou](https://aka.ms/catou)에서 대시보드로 이동합니다.
2. [감사 로그 보기]를 클릭합니다.</br>
![감사 이벤트](media/active-directory-tou/tou8.png)
3.  Azure AD 감사 로그 화면에서 제공된 드롭 다운을 사용하여 특정 감사 로그 정보를 대상으로 하도록 정보를 필터링할 수 있습니다.
[감사 이벤트](media/active-directory-tou/tou9.png)
4.  로컬에서 사용하도록 정보를 .csv 파일로 다운로드할 수도 있습니다.

## 

## <a name="what-users-see"></a>사용자에게 표시되는 내용
범위 내에 있는 사용자는 사용 약관을 작성하여 적용한 후 다음 내용을 볼 수 있습니다.  로그인 시 다음 화면이 표시됩니다.
-   24 크기로 PDF 내에서 글꼴을 포함하는 것이 가장 좋습니다.
![감사 이벤트](media/active-directory-tou/tou10.png)
-   이 화면은 모바일에 어떻게 표시되는지를 보여 줍니다.</br></br>
![감사 이벤트](media/active-directory-tou/tou11.png)

### <a name="review-terms-of-use"></a>사용 약관 검토
사용자는 자신이 동의한 사용 약관을 검토하고 볼 수 있습니다.  사용 약관을 검토 하려면 다음 절차를 따릅니다.

1. [https://myapps.microsoft.com](https://myapps.microsoft.com)으로 이동하여 로그인합니다.
2. 오른쪽 위 모서리에서 자신의 이름을 클릭하고 드롭다운에서 **프로필**을 선택합니다.
![프로필](media/active-directory-tou/tou14.png)

3. 프로필에서 **사용 약관 검토**를 클릭합니다.
![감사 이벤트](media/active-directory-tou/tou13a.png)

4.  여기에서 사용자가 동의한 사용 약관을 검토할 수 있습니다. 

## <a name="removing-users-from-an-active-terms-of-use"></a>현재 사용 약관에서 사용자 제거

[!INCLUDE [Privacy](../../includes/gdpr-intro-sentence.md)]

기본적으로 삭제된 사용자는 30일 동안 Azure AD에서 삭제된 채로 남아 있지만 이 기간 동안 관리자는 필요한 경우 삭제된 사용자를 복원할 수 있습니다.  30일 후 해당 사용자가 영구적으로 삭제됩니다.  또한 Azure Active Directory 포털을 사용하여 전역 관리자는 해당 기간이 만료되기 전에 명시적으로 [최근에 삭제된 사용자를 영구적으로 삭제](active-directory-users-restore.md)할 수 있습니다.  사용자가 영구적으로 삭제됐으므로 추후 해당 사용자에 대한 데이터가 현재 사용 약관에서 제거됩니다.  삭제된 사용자에 대한 감사 정보는 감사 로그에 남아 있습니다.



## <a name="additional-information"></a>추가 정보
다음 정보를 알고 있어야 하며 사용 약관을 사용하는 데 도움이 될 수 있습니다.

>[!IMPORTANT]
> 범위에 있는 사용자는 다음과 같은 경우 새 정책을 충족하기 위해 로그아웃 및 로그인해야 합니다.
> - 사용 약관에 조건부 액세스 정책이 설정된 경우
> - 또는 두 번째 사용 약관이 작성된 경우
>
>조건부 액세스 정책은 즉시 적용됩니다. 이 경우 관리자에게 “sad 클라우드” 또는 “Azure AD 토큰 문제”가 표시되기 시작합니다. 관리자는 새 정책을 충족하기 위해 로그아웃했다가 다시 로그인해야 합니다.





## <a name="frequently-asked-questions"></a>질문과 대답

**Q: 사용자가 사용 약관에 동의했는지 어떻게 볼 수 있나요?**</br>
A: 사용 약관 옆의 동의 아래에 있는 숫자를 클릭할 수 있습니다.  자세한 내용은 [현재 사용자 상태 보기](#viewing-current-user-status)를 참조하세요.  또한 사용 약관에 동의한 사용자는 감사 로그에 기록됩니다. Azure AD 감사 로그를 검색하여 결과를 볼 수 있습니다.  

**Q: 사용 약관 조건을 변경한 경우 사용자가 다시 동의해야 하나요?**</br>
A: 예, 관리자는 사용 약관 조건을 변경할 수 있으며 새 조건에 대해 재동의를 요구할 수 있습니다.

**Q: 사용 약관에서 다중 언어를 지원할 수 있나요?**</br>
A: 예.  현재 관리자가 단일 사용 약관에 대해 구성할 수는 18개의 다른 언어가 있습니다. 

**Q: 사용 약관은 언제 트리거되나요?**</br>
A: 사용 약관은 로그인 환경 중에 트리거됩니다.

**Q: 사용 약관을 대상으로 지정할 수 있는 응용 프로그램은 무엇인가요?**</br>
A: 최신 인증을 사용하는 엔터프라이즈 응용 프로그램에 조건부 액세스 정책을 만들 수 있습니다.  자세한 내용은 [엔터프라이즈 응용 프로그램](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-view-azure-portal)을 참조하세요.

**Q: 특정 사용자 또는 앱에 여러 사용 약관을 추가할 수 있나요?**</br>
A: 예, 해당 그룹 또는 앱을 대상으로 하는 조건부 액세스 정책을 여러 개 작성하면 가능합니다. 사용자가 여러 사용 약관 범위에 속하는 경우 한 번에 하나의 사용 약관에 동의합니다.
 
**Q: 사용자가 사용 약관을 거부하는 경우 어떻게 되나요?**</br>
A: 사용자는 응용 프로그램에 액세스할 수 없습니다. 액세스할 수 있으려면 다시 로그인하여 약관에 동의해야 합니다.
