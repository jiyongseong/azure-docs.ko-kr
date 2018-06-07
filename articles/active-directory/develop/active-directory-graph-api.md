---
title: Azure Active Directory Graph API | Microsoft Docs
description: REST API 끝점을 통해 Azure AD에 프로그래밍 방식으로 액세스할 수 있도록 해주는 Azure AD Graph API에 대한 개요 및 빠른 시작 가이드입니다.
services: active-directory
documentationcenter: ''
author: mtillman
manager: mtillman
editor: mbaldwin
ms.assetid: 5471ad74-20b3-44df-a2b5-43cde2c0a045
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: mtillman
ms.custom: aaddev
ms.openlocfilehash: 4b4f698042f6688e3db484f7d96ccfb06c5cdd4f
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34158016"
---
# <a name="azure-active-directory-graph-api"></a>Azure Active Directory Graph API
> [!IMPORTANT]
> Azure Active Directory 리소스에 액세스하려면 Azure AD Graph API 대신 [Microsoft Graph](https://graph.microsoft.io/)를 사용하는 것이 좋습니다. 이제 Microsoft는 Azure AD Graph API를 더 이상 개선하지 않을 것이며 Microsoft Graph에 주력하고 있습니다. Azure AD Graph API가 적절할 수 있는 시나리오는 매우 제한적입니다. 자세한 내용은 Office 개발자 센터에서 [Microsoft Graph 또는 Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) 블로그 게시물을 참조하세요.
> 
> 

Azure Active Directory Graph API는 REST API 끝점을 통해 Azure AD에 프로그래밍 방식으로 액세스할 수 있게 합니다. 응용 프로그램은 Azure AD Graph API를 사용하여 디렉터리 데이터 및 개체에 대한 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 수행할 수 있습니다. 예를 들어, Azure AD Graph API는 사용자 개체에 대한 다음과 같은 일반적인 작업을 지원합니다.

* 디렉터리에 새 사용자 만들기
* 소속 그룹과 같은 사용자의 자세한 속성 가져오기
* 해당 위치 및 전화번호와 같은 사용자의 속성을 업데이트 또는 암호 변경
* 역할 기반 액세스에 대한 사용자의 그룹 멤버 자격 확인
* 사용자의 계정 비활성화 또는 완전히 삭제

또한 그룹 및 응용 프로그램과 같은 다른 개체에 대해 유사한 작업을 수행할 수 있습니다. 디렉터리에서 Azure AD Graph API를 호출하려면 응용 프로그램을 Azure AD에 등록해야 합니다. 또한 응용 프로그램에 Azure AD Graph API 액세스 권한을 부여해야 합니다. 이 액세스는 대개 사용자 또는 관리자 동의 흐름을 통해 수행됩니다.

Azure Active Directory Graph API 사용을 시작하려면 [Azure AD Graph API 빠른 시작 가이드](active-directory-graph-api-quickstart.md) 또는 [대화형 Azure AD Graph API 참조 설명서](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)를 참조하세요.

## <a name="features"></a>기능
Azure AD Graph API는 다음과 같은 기능을 제공합니다.

* **REST API 끝점**: Azure AD Graph API는 표준 HTTP 요청을 사용하여 액세스하는 끝점으로 구성된 RESTful 서비스입니다. Azure AD Graph API는 요청 및 응답에 대해 XML 또는 Javascript Object Notation(JSON) 콘텐츠 형식을 지원합니다. 자세한 내용은 [Azure AD Graph REST API 참조](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)를 참조하세요.
* **Azure AD 인증**: 요청의 인증 헤더에서 JWT(JSON Web Token)을 추가하여 Azure AD Graph API에 대한 모든 요청을 인증해야 합니다. Azure AD의 토큰 끝점에 요청을 하고 유효한 자격 증명을 제공하여 이 토큰을 가져옵니다. OAuth 2.0 클라이언트 자격 증명 흐름 또는 인증 코드 부여 흐름을 사용하여 Graph를 호출하는 토큰을 획득할 수 있습니다. 자세한 내용은 [Azure AD의 OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx)을 참조하세요.
* **RBAC(역할 기반 권한 부여)**: 보안 그룹을 사용하여 Azure AD Graph API에서 RBAC를 수행합니다. 예를 들어 사용자에게 특정 리소스에 대한 액세스 권한이 있는지 확인하려는 경우, 응용 프로그램에서 true 또는 false를 반환하는 [그룹 구성원 자격(전이적) 확인](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/functions-and-actions#checkMemberGroups) 작업을 호출할 수 있습니다.
* **차등 쿼리**: Azure AD Graph API를 자주 쿼리할 필요 없이 차등 쿼리를 통해 두 기간 사이에 디렉터리에서 변경된 내용을 추적할 수 있습니다. 이 요청 유형은 이전 차등 쿼리 요청과 현재 요청 간에 발생한 변경 내용만 반환합니다. 자세한 내용은 [Azure AD Graph API 차등 쿼리](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query)를 참조하세요.
* **디렉터리 확장**: 외부 데이터 저장소를 사용할 필요 없이 디렉터리 개체에 사용자 지정 속성을 추가할 수 있습니다. 예를 들어 응용 프로그램이 각 사용자에 대해 Skype ID 속성을 요구하는 경우 디렉터리에 새 속성을 등록하면 모든 사용자 개체에 대해 사용할 수 있습니다. 자세한 내용은 [Azure AD Graph API Directory 스키마 확장](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)을 참조하세요.
* **권한 범위로 보호**: Azure AD Graph API는 OAuth 2.0을 사용하여 Azure AD 데이터에 대한 보안 액세스를 사용할 수 있는 권한 범위를 노출합니다. 다음을 포함한 다양한 클라이언트 앱 유형을 지원합니다.
  
  * 로그인한 사용자의 권한 부여를 통해 데이터에 대한 위임된 액세스 권한을 부여한 사용자 인터페이스(위임)
  * 로그인한 사용자가 없는 백그라운드에서 작동하고 응용 프로그램 정의 역할 기반 액세스 제어를 사용하는 서비스/디먼 응용 프로그램
    
    위임 및 응용 프로그램 권한은 Azure AD Graph API에서 노출하는 권한을 표시하고 [Azure Portal의 기능](https://portal.azure.com)인 응용 프로그램 등록 사용 권한을 통해 클라이언트 응용 프로그램에 의해 요청될 수 있습니다. [Azure AD Graph API 권한 범위](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)는 클라이언트 응용 프로그램에서 사용할 수 있는 기능에 대한 정보를 제공합니다.

## <a name="scenarios"></a>시나리오
Azure AD Graph API는 다양한 응용 프로그램 시나리오를 지원합니다. 다음 시나리오는 가장 일반적인 것입니다.

* **기간 업무(단일 테넌트) 응용 프로그램**: 이 시나리오에서는 엔터프라이즈 개발자는 Office 365를 구독하는 조직을 위해 업무를 수행합니다. 개발자는 사용자에게 라이선스를 할당하는 작업을 수행하기 위해 Azure AD와 상호 작용하는 웹 응용 프로그램을 구축하고 있습니다. 이 작업은 Azure AD Graph API에 대한 액세스를 필요하기 때문에, 개발자는 Azure AD에서 단일 테넌트 응용 프로그램을 등록하고 Azure AD Graph API에 대한 읽기 및 쓰기 권한을 구성합니다. 그런 다음, 응용 프로그램은 Azure AD Graph API를 호출하는 토큰을 획득하기 위해 자신의 자격 증명 또는 현재 로그인 한 사용자의 자격 증명을 사용하도록 구성됩니다.
* **서비스 응용 프로그램 같은 소프트웨어(다중 테넌트)**: 이 시나리오에서는 독립 소프트웨어 공급업체(ISV)는 Azure AD를 사용하는 다른 조직에 대한 사용자 관리 기능을 제공하는 호스팅 다중 테넌트 웹 응용 프로그램을 개발하고 있습니다. 이러한 기능은 디렉터리 개체에 액세스를 요청하고, 이에 응용 프로그램이 Azure AD Graph API를 호출해야 합니다. 개발자는 Azure AD에서 응용 프로그램을 등록하고, Azure AD Graph API에 대한 읽기 및 쓰기 권한을 요구하도록 구성한 다음, 다른 조직이 자신의 디렉터리에 있는 응용 프로그램을 사용하는 것에 동의하도록 외부 액세스를 사용합니다. 다른 조직의 사용자가 처음으로 응용 프로그램에 대해 인증하는 경우, 응용 프로그램이 요청하는 사용 권한과 함께 동의 대화 상자가 표시됩니다. 동의하면 응용 프로그램이 사용자 디렉터리의 Azure AD Graph API에 대해 사용 권한을 요청한 사람에게 부여하게 됩니다. 동의 프레임워크에 대한 자세한 내용은 [동의 프레임워크의 개요](active-directory-integrating-applications.md)를 참조하세요.

## <a name="see-also"></a>참고 항목
[Azure AD Graph API 빠른 시작 가이드](active-directory-graph-api-quickstart.md)

[Azure AD Graph REST 설명서](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)

[Azure Active Directory 개발자 가이드](active-directory-developers-guide.md)

