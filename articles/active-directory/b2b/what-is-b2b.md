---
title: Azure Active Directory B2B 공동 작업이란? | Microsoft Docs
description: Azure Active Directory B2B 공동 작업은 비즈니스 파트너가 선택적으로 회사 응용 프로그램에 액세스할 수 있게 함으로써 회사 간 관계를 지원합니다.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/26/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 2bc405e6356113e0423f833868c86890c0c3d5d2
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259716"
---
# <a name="what-is-azure-ad-b2b-collaboration"></a>Azure AD B2B 공동 작업이란?

Azure AD(Azure Active Directory) B2B(Business-to-Business)공동 작업 기능을 통해 Azure AD를 사용하는 모든 조직은 크든 작든, 다른 조직의 사용자와 안전하게 작업할 수 있습니다. 이러한 조직은 Azure AD를 사용하거나 사용하지 않을 수 있고 심지어 IT 부서가 없어도 됩니다.

Azure AD를 사용하는 조직은 회사 데이터를 완전하게 제어하면서 파트너가 문서, 리소스 및 응용 프로그램에 액세스하도록 할 수 있습니다. 개발자는 Azure AD B2B API를 사용하여 두 조직을 안전한 방식으로 결합하는 응용 프로그램을 작성할 수 있습니다. 또한 최종 사용자가 탐색하기도 편리합니다.

다음 비디오는 유용한 개요를 제공합니다.
>[!VIDEO https://www.youtube.com/embed/AhwrweCBdsc]

## <a name="key-benefits-of-azure-ad-b2b-collaboration"></a>Azure AD B2B 공동 작업의 주요 이점

### <a name="work-with-any-user-from-any-partner"></a>파트너의 사용자와 함께 작업

- 파트너가 자체 자격 증명을 사용함
- 파트너가 Azure AD를 사용할 필요가 없음
- 외부 디렉터리 또는 복잡한 설정이 필요하지 않음

### <a name="simple-and-secure-collaboration"></a>간단하고 안전한 공동 작업

- 정교한 Azure AD 기반 권한 부여 정책을 적용하면서 회사 앱 및 데이터에 액세스하도록 함
- 사용자에게 편리
- 앱 및 데이터에 대한 엔터프라이즈급 보안

### <a name="no-management-overhead"></a>관리 오버헤드 없음

- 외부 계정 또는 암호 관리 없음
- 동기화 또는 수동 계정 수명 주기 관리 없음
- 외부 관리 오버헤드 없음

## <a name="easily-add-b2b-collaboration-users"></a>B2B 공동 작업 사용자를 편리하게 추가

관리자는 B2B 공동 작업 (게스트) 사용자를 [Azure Portal](https://portal.azure.com)의 조직에 손쉽게 추가할 수 있습니다.

![게스트 사용자 추가](media/what-is-b2b/adding-b2b-users-admin.png)

### <a name="enable-your-collaborators-to-bring-their-own-identity"></a>공동 작업자가 자신의 ID를 사용하도록 할 수 있음

B2B 공동 작업자는 자신이 선택한 ID로 로그인할 수 있습니다. 사용자에게 Microsoft 계정 또는 Azure AD 계정이 없는 경우 제품 사용 시 계정이 원활하게 생성됩니다.

### <a name="delegate-to-application-and-group-owners"></a>응용 프로그램 및 그룹 소유자에게 위임

응용 프로그램 및 그룹 소유자는 Microsoft 응용 프로그램이든 아니든 관계없이 관리하는 응용 프로그램에 직접 B2B 사용자를 추가할 수 있습니다. 관리자는 B2B 사용자를 추가할 권한을 관리자가 아닌 사용자에게 위임할 수 있습니다. 관리자가 아닌 사용자는 [Azure AD 응용 프로그램 액세스 패널](https://myapps.microsoft.com)을 사용하여 B2B 공동 작업 사용자를 응용 프로그램이나 그룹에 추가할 수 있습니다.

![액세스 패널](media/what-is-b2b/access-panel.png)

![구성원 추가](media/what-is-b2b/add-member.png)

### <a name="authorization-policies-protect-your-corporate-content"></a>권한 부여 정책으로 회사 콘텐츠 보호

다단계 인증 등의 조건부 액세스 정책을 적용할 수 있습니다.
- 테넌트 수준에서 적용
- 응용 프로그램 수준에서 적용
- 회사 앱과 데이터를 보호하기 위해 특정 사용자에 대해 적용

### <a name="use-apis-and-sample-code-to-easily-build-applications-to-onboard"></a>API 및 샘플 코드를 사용하여 등록할 응용 프로그램을 쉽게 빌드할 수 있음

조직의 요구에 맞게 사용자 지정된 방식으로 외부 파트너를 등록하세요.

[B2B 공동 작업 초대 API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)를 사용하여 셀프 서비스 등록 포털 생성을 포함하여 등록 환경을 사용자 지정할 수 있습니다. 셀프 서비스 포털에 대한 샘플 코드는 [Github](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web)를 참조하세요.

![등록 포털](media/what-is-b2b/sign-up-portal.png)

Azure AD B2B 공동 작업에서는 Azure AD를 완전히 활용하여 최종 사용자가 간편하고 직관적으로 찾는 방식으로 파트너 관계를 보호할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory 관리자가 B2B 공동 작업 사용자를 추가하는 방법](add-users-administrator.md)
- [정보 작업자가 B2B 공동 작업 사용자를 추가하는 방법은 무엇입니까?](add-users-information-worker.md)
- [B2B 공동 작업 초대 상환](redemption-experience.md)
- [Azure AD B2B 공동 작업 라이선스](licensing-guidance.md)
- 또한 피드백, 논의 사항 및 제안이 있는 경우 언제나처럼 [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b)(Microsoft 기술 커뮤니티)를 통해 제품 팀에 연결할 수 있습니다.