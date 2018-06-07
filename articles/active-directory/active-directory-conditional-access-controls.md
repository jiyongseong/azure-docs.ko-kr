---
title: Azure Active Directory 조건부 액세스의 액세스 제어 | Microsoft Docs
description: Azure Active Directory 조건부 액세스의 액세스 제어가 작동하는 방식을 알아봅니다.
services: active-directory
keywords: 앱에 조건부 액세스, Azure AD로 조건부 액세스, 회사 리소스에 대한 액세스 보호, 조건부 액세스 정책
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/01/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 8271c4c88245e98fd3709c7279904d36ad009682
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
ms.locfileid: "32770726"
---
# <a name="access-controls-in-azure-active-directory-conditional-access"></a>Azure Active Directory 조건부 액세스의 액세스 제어 

[Azure AD(Azure Active Directory) 조건부 액세스](active-directory-conditional-access-azure-portal.md)를 사용하여 권한 있는 사용자가 클라우드 앱에 액세스하는 방법을 제어할 수 있습니다. 조건부 액세스 정책에서 정책을 트리거하는 이유(“when this happens”)에 대한 응답(“do this”)을 정의합니다. 

![제어](./media/active-directory-conditional-access-controls/10.png)


조건부 액세스의 컨텍스트에서, 

- “**When this happens**”는 **조건**이라고 합니다.

- “**Then do this**”는 **액세스 제어**라고 합니다.


조건 문과 제어 문의 조합은 조건부 액세스 정책을 나타냅니다.

![제어](./media/active-directory-conditional-access-controls/61.png)

각 컨트롤은 로그인하는 사용자 또는 시스템에 의해 충족되어야 하는 요구 사항 또는 사용자가 로그인한 후 수행할 수 있는 작업에 대한 제한입니다. 

두 가지 형식의 컨트롤이 있습니다. 

- **권한 부여 컨트롤** - 액세스를 제어하기 위해

- **세션 컨트롤** - 세션 내에서 액세스를 제한하기 위해

이 항목에서는 Azure AD 조건부 액세스에서 사용할 수 있는 다양한 컨트롤을 설명합니다. 

## <a name="grant-controls"></a>권한 부여 컨트롤

권한 부여 컨트롤을 사용하여 액세스를 완전히 차단하거나 원하는 컨트롤을 선택하여 추가 요구 사항으로 액세스를 허용할 수 있습니다. 여러 컨트롤의 경우 다음이 필요할 수 있습니다.

- 충족되도록 선택한 모든 컨트롤(*AND*) 
- 충족되도록 선택한 하나의 컨트롤(*OR*)

![제어](./media/active-directory-conditional-access-controls/17.png)



### <a name="multi-factor-authentication"></a>Multi-Factor Authentication

이 컨트롤을 사용하여 지정된 클라우드 앱에 액세스하기 위해 다단계 인증을 요구할 수 있습니다. 이 컨트롤은 다음 다단계 공급자를 지원합니다. 

- Azure Multi-Factor Authentication 

- AD FS(Active Directory Federation Service)와 결합된 온-프레미스 다단계 인증 공급자.
 
다단계 인증을 사용하면 유효한 사용자의 주 자격 증명에 액세스 할 수 있는 권한이 없는 사용자가 리소스에 액세스하지 못하도록 방지할 수 있습니다.



### <a name="compliant-device"></a>규정 준수 장치

장치 기반인 조건부 액세스 정책을 구성할 수 있습니다. 장치 기반 조건부 액세스 정책의 목적은 [관리 장치](active-directory-conditional-access-policy-connected-applications.md#managed-devices)에서 구성된 리소스에 대한 액세스 권한만을 부여하는 것입니다. 관리 장치를 정의해야 하는 한 가지 옵션은 준수 장치가 필요합니다. 이 옵션을 선택하면 조건부 액세스 정책에서는 Azure Active Directory에 [등록](device-management-introduction.md)되어 있고 MDM 솔루션 규격으로 표시되는 장치의 액세스 시도에 액세스할 수 있는 권한을 부여합니다.

자세한 내용은 [Azure Active Directory 장치 기반 조건부 액세스 정책 설정](active-directory-conditional-access-policy-connected-applications.md)을 참조하세요.

### <a name="hybrid-azure-ad-joined-device"></a>하이브리드 Azure AD 조인 장치

장치 기반 조건부 액세스 정책을 구성할 때 또 다른 옵션은 하이브리드 Azure AD 조인 장치를 요구하는 것입니다. 즉, Windows 데스크톱, 랩톱 및 엔터프라이즈 태블릿을 온-프레미스 Active Directory에 조인해야 합니다. 이 옵션을 선택하면 조건부 액세스 정책에서는 온-프레미스 Active Directory 및 Azure Active Directory에 조인되어 있는 장치의 액세스 시도에 액세스할 수 있는 권한을 부여합니다.  

자세한 내용은 [Azure Active Directory 장치 기반 조건부 액세스 정책 설정](active-directory-conditional-access-policy-connected-applications.md)을 참조하세요.





### <a name="approved-client-app"></a>승인된 클라이언트 앱

직원이 개인 및 회사 작업 모두에 모바일 장치를 사용하므로 관리되지 않는 경우에도 장치를 사용하여 액세스되는 회사 데이터를 보호하는 기능이 있어야 합니다.
[Intune 앱 보호 정책](https://docs.microsoft.com/intune/app-protection-policy)을 사용하여 모든 MDM(모바일 장치 관리) 솔루션과는 별도로 회사의 데이터를 보호할 수 있습니다.


승인된 클라이언트 앱을 사용하여 [Intune 앱 보호 정책](https://docs.microsoft.com/intune/app-protection-policy)을 지원하도록 클라우드 앱에 액세스를 시도하는 클라이언트 앱을 요구할 수 있습니다. 예를 들어 Exchange Online에 대한 액세스를 Outlook 앱으로 제한할 수 있습니다. 승인된 클라이언트 앱을 요구하는 조건부 액세스 정책을 [앱 기반 조건부 액세스 정책](active-directory-conditional-access-mam.md)이라고도 합니다. 지원되는 승인된 클라이언트 앱의 목록은 [승인된 클라이언트 앱 요구 사항](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)을 참조하세요.


### <a name="terms-of-use"></a>사용 약관

테넌트의 사용자가 리소스에 대한 액세스 권한을 부여 받기 전에 사용 조건에 동의하도록 요구할 수 있습니다. 관리자는 PDF 문서를 업로드하여 사용 조건을 구성하고 사용자 지정할 수 있습니다. 사용자가 이 제어 범위에 해당하는 경우 사용 조건에 동의한 경우에만 응용 프로그램에 대한 액세스가 부여됩니다. 


### <a name="custom-controls"></a>사용자 지정 컨트롤 

Azure Active Directory 외부의 추가 요구 사항을 충족시키기 위해 사용자를 호환 가능한 서비스로 리디렉션하는 조건부 액세스의 사용자 지정 컨트롤을 만들 수 있습니다. 이를 통해 특정 외부 다단계 인증 및 검증 공급자를 사용하여 조건부 액세스 규칙을 적용하거나 직접 사용자 지정 서비스를 빌드할 수 있습니다. 이 제어를 충족시키기 위해 사용자의 브라우저는 외부 서비스로 리디렉션되고 필요한 인증 또는 유효성 검사 활동을 수행한 다음 Azure Active Directory로 다시 리디렉션됩니다. 사용자가 성공적으로 인증되거나 유효성이 확인되면 사용자는 조건부 액세스 흐름을 계속합니다. 

## <a name="custom-controls"></a>사용자 지정 컨트롤

사용자 지정 컨트롤은 Azure Active Directory Premium P2 버전의 기능입니다. 사용자 지정 컨트롤을 사용하는 경우 Azure Active Directory 외부의 추가 요구 사항을 충족시키기 위해 사용자를 호환 가능한 서비스로 리디렉션합니다. 이 제어를 충족시키기 위해 사용자의 브라우저는 외부 서비스로 리디렉션되고 필요한 인증 또는 유효성 검사 활동을 수행한 다음 Azure Active Directory로 다시 리디렉션됩니다. Azure Active Directory가 응답을 확인하고 사용자가 성공적으로 인증되거나 유효성이 확인되면 사용자는 조건부 액세스 흐름을 계속합니다.

이러한 컨트롤을 사용하면 특정 외부 또는 사용자 지정 서비스를 조건부 액세스 컨트롤로 사용할 수 있으며 조건부 액세스 기능을 일반적으로 확장할 수 있습니다.

현재 호환 서비스를 제공하는 공급자는 다음과 같습니다.

- [Duo Security](https://duo.com/docs/azure-ca)

- RSA

- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

이러한 서비스에 대한 자세한 내용은 공급자에게 직접 문의하세요.

### <a name="creating-custom-controls"></a>사용자 지정 컨트롤 만들기

사용자 지정 컨트롤을 만들려면 먼저 사용할 공급자에게 문의해야 합니다. Microsoft가 아닌 각 공급자는 등록, 구독 또는 기타 서비스의 일부가 되는 데 필요한 자체 프로세스 및 요구 사항을 가지고 있습니다. 또한 이들은 조건부 액세스와 통합하고자 함을 나타냅니다. 이 시점에서 공급자는 JSON 형식의 데이터 블록을 제공합니다. 이 데이터는 테넌트가 공급자 및 조건부 액세스와 함께 작동할 수 있게 하고, 새 컨트롤을 만들고, 사용자가 공급자와의 검증을 성공적으로 수행했는지를 조건부 액세스가 확인할 수 있는 방법을 정의합니다.

JSON 데이터를 복사한 다음 관련 텍스트 상자에 붙여넣습니다. 변경 사항을 명시적으로 이해하지 못한 경우 JSON을 변경하지 마세요. 변경하면 공급자와 Microsoft 간의 연결이 끊어져 잠재적으로 사용자가 사용자 계정 밖에서 잠길 수 있습니다.

사용자 지정 컨트롤을 만드는 옵션은 **조건부 액세스** 페이지의 **관리** 섹션에 있습니다.

![제어](./media/active-directory-conditional-access-controls/82.png)

**새 사용자 지정 컨트롤**을 클릭하면 컨트롤의 JSON 데이터에 대한 텍스트 상자가 있는 블레이드가 열립니다.  


![제어](./media/active-directory-conditional-access-controls/81.png)


### <a name="deleting-custom-controls"></a>사용자 지정 컨트롤 삭제

사용자 지정 컨트롤을 삭제하려면 먼저 조건부 액세스 정책에서 사용되지 않는지 확인해야 합니다. 작업이 완료되면 다음을 수행합니다.

1. 사용자 지정 컨트롤 목록으로 이동합니다.

2. …를 클릭합니다.  

3. **삭제**를 선택합니다.

### <a name="editing-custom-controls"></a>사용자 지정 컨트롤 편집

사용자 지정 컨트롤을 편집하려면 현재 컨트롤을 삭제하고 업데이트된 정보로 새 컨트롤을 만들어야 합니다.




## <a name="session-controls"></a>세션 컨트롤

세션 컨트롤은 클라우드 앱 내에서 제한된 환경을 활성화합니다. 세션 컨트롤은 클라우드 앱에서 적용되고 Azure AD가 앱에 제공한 세션에 대한 추가 정보에 의존합니다.

![제어](./media/active-directory-conditional-access-controls/31.png)

### <a name="use-app-enforced-restrictions"></a>앱에서 적용된 제한 사항 사용

이 컨트롤을 사용하여 Azure AD가 장치 정보를 클라우드 앱에 전달하도록 할 수 있습니다. 클라우드 앱은 이 컨트롤을 통해 사용자가 규격 장치 또는 도메인 가입 장치에서 들어오는지 확인합니다. 이 컨트롤은 현재 SharePoint를 통해 클라우드 앱으로만 지원됩니다. SharePoint에서는 장치 정보를 사용하여 사용자에게 장치 상태에 따라 제한된 환경이나 전체 환경을 제공합니다.
SharePoint를 통해 제한된 액세스를 요구하는 방법을 알아보려면 [관리되지 않은 장치에서 액세스 제어](https://aka.ms/spolimitedaccessdocs)를 참조하세요.



## <a name="next-steps"></a>다음 단계

- 조건부 액세스 정책을 구성하는 방법을 알아보려면 [Azure Active Directory에서 조건부 액세스 시작](active-directory-conditional-access-azure-portal-get-started.md)을 참조하세요.

- 사용자 환경에 대한 조건부 액세스 정책을 구성할 준비가 완료된 경우 [Azure Active Directory의 조건부 액세스 모범 사례](active-directory-conditional-access-best-practices.md)를 참조하세요. 
