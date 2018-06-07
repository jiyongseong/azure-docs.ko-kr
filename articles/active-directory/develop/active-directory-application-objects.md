---
title: Azure Active Directory 응용 프로그램 및 서비스 주체 개체
description: Azure Active Directory의 응용 프로그램 및 서비스 주체 개체 간의 관계에 대한 설명
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
services: active-directory
editor: ''
ms.assetid: adfc0569-dc91-48fe-92c3-b5b4833703de
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/19/2017
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: e8e693355fb9b30e1a69b49f20d5044c531e2fcd
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="application-and-service-principal-objects-in-azure-active-directory-azure-ad"></a>Azure Active Directory(Azure AD)의 응용 프로그램 및 서비스 주체 개체
경우에 따라 “응용 프로그램”이 Azure AD의 문맥에서 사용될 때 용어의 의미를 잘못 이해할 수 있습니다. 이 문서의 목표는 [다중 테넌트 응용 프로그램](active-directory-dev-glossary.md#multi-tenant-application)에 대한 등록 및 동의에 대한 그림을 통해 Azure AD 응용 프로그램 통합의 개념 및 구체적인 측면을 명확히 설명하는 것입니다.

## <a name="overview"></a>개요
Azure AD와 통합된 응용 프로그램은 소프트웨어 측면을 넘어서는 의미를 지닙니다. “응용 프로그램”은 개념적 용어로 응용 프로그램 소프트웨어를 나타낼 뿐 아니라, 런타임 시 인증 및 권한 부여 “대화”에서 Azure AD 등록 및 역할을 나타내기도 합니다. 기본적으로 응용 프로그램은 [클라이언트](active-directory-dev-glossary.md#client-application) 역할(리소스 사용)이나 [리소스 서버](active-directory-dev-glossary.md#resource-server) 역할(클라이언트에게 API 노출) 또는 두 역할 모두에서 작동할 수 있습니다. 대화 프로토콜은 클라이언트/리소스가 각기 리소스 데이터를 액세스/보호할 수 있도록 하려는 [OAuth 2.0 권한 부여 흐름](active-directory-dev-glossary.md#authorization-grant)에 의해 정의됩니다. 이제 한 단계 더 깊이 들어가, Azure AD 응용 프로그램 모델이 디자인 및 런타임 시 응용 프로그램을 내부적으로 나타내는 방법을 알아봅니다. 

## <a name="application-registration"></a>응용 프로그램 등록
Azure AD 응용 프로그램을 [Azure Portal][AZURE-Portal]에 등록할 때, Azure AD 테넌트에 응용 프로그램 개체와 서비스 주체 개체라는 두 개체가 만들어집니다.

#### <a name="application-object"></a>응용 프로그램 개체
Azure AD 응용 프로그램은 응용 프로그램의 "홈" 테넌트라고도 알려진, 응용 프로그램이 등록된 Azure AD 테넌트에 상주하는 하나의 응용 프로그램 개체에 의해서만 정의됩니다. Azure AD Graph [응용 프로그램 엔터티][AAD-Graph-App-Entity]는 응용 프로그램 개체의 속성에 대한 스키마를 정의합니다. 

#### <a name="service-principal-object"></a>서비스 주체 개체
Azure AD 테넌트에 의해 보안이 유지되는 리소스에 액세스하려면 액세스해야 하는 엔터티를 보안 주체를 통해 나타내야 합니다. 사용자(사용자 주체) 및 응용 프로그램(서비스 주체) 둘 다 마찬가지입니다. 보안 주체는 해당 테넌트의 사용자/응용 프로그램에 대한 액세스 정책 및 권한을 정의합니다. 이를 통해 로그인 동안의 사용자/응용 프로그램의 인증 및 리소스 액세스 동안의 권한 부여 같은 핵심 기능이 허용됩니다.

응용 프로그램이 테넌트의 리소스에 액세스하기 위한 권한을 받으면(등록 또는 [동의](active-directory-dev-glossary.md#consent) 시) 서비스 주체 개체가 만들어집니다. Azure AD Graph [서비스 주체 엔터티][AAD-Graph-Sp-Entity]는 서비스 주체 개체의 속성에 대한 스키마를 정의합니다. 

#### <a name="application-and-service-principal-relationship"></a>응용 프로그램 및 서비스 주체 관계
응용 프로그램 개체는 모든 테넌트에서 사용하기 위한 응용 프로그램의 *글로벌* 표현으로 그리고 서비스 주체는 특정 테넌트에서 사용하기 위한 *로컬* 표현으로 생각할 수 있습니다. 응용 프로그램 개체는 해당 서비스 주체 개체 만들기에 사용하기 위해 공통의 기본 속성이 *파생*되는 템플릿으로 제공됩니다. 따라서 응용 프로그램 개체는 소프트웨어 응용 프로그램과 1:1 관계가 있으며 해당 서비스 주체 개체와 1:다 관계가 있습니다.

응용 프로그램이 사용될 각 테넌트에 서비스 주체를 만들어서 테넌트가 보호하는 리소스에 대한 로그인 및/또는 액세스를 위한 ID를 설정할 수 있어야 합니다. 단일 테넌트 응용 프로그램에는 해당 홈 테넌트에 하나의 서비스 주체만 있으며 응용 프로그램 등록 중에 생성하고 동의합니다. 다중 테넌트 웹 응용 프로그램/API에도 해당 테넌트의 사용자가 사용을 동의한 각 테넌트에 생성된 하나의 서비스 주체가 있습니다. 

> [!NOTE]
> 응용 프로그램 개체에 대해 이뤄진 모든 변경은 또한 응용 프로그램의 홈 테넌트(그것이 등록된 테넌트)에만 있는 해당 서비스 주체 개체에도 반영됩니다. 다중 테넌트 응용 프로그램의 경우 [응용 프로그램 액세스 패널](https://myapps.microsoft.com)을 통해 액세스를 제거하고 다시 액세스 권한이 부여될 때까지 응용 프로그램 개체의 변경 내용은 모든 소비자 테넌트의 서비스 주체 개체에 반영되지 않습니다.
><br>  
> 또한 기본적으로 네이티브 응용 프로그램이 다중 테넌트로 등록됩니다.
> 
> 

## <a name="example"></a>예
다음 다이어그램은 **HR 앱**이라는 샘플 다중 테넌트 응용 프로그램의 컨텍스트에서 응용 프로그램의 응용 프로그램 개체와 해당 서비스 주체 개체를 보여 줍니다. 이 시나리오에는 세 가지 Azure AD 테넌트가 있습니다. 

* **Adatum** - **HR 앱**을 개발한 회사에서 사용하는 테넌트
* **Contoso** - **HR 앱**의 소비자인 Contoso 조직에서 사용하는 테넌트
* **Fabrikam** - **HR 앱**의 또 다른 소비자인 Fabrikam 조직에서 사용하는 테넌트

![응용 프로그램 개체 및 서비스 주체 개체 간의 관계](./media/active-directory-application-objects/application-objects-relationship.png)

위 다이어그램에서 1단계는 응용 프로그램의 홈 테넌트에서 응용 프로그램 및 서비스 주체 개체를 만드는 과정입니다.

2단계에서 Contoso 관리자와 Fabrikam 관리자가 전적으로 동의한 경우 서비스 주체 개체가 회사의 Azure AD 테넌트에 생성되고 관리자가 부여한 사용 권한이 할당됩니다. 또한 사용자가 개별 사용에 대한 동의를 할 수 있게 HR 앱이 구성/설계될 수 있습니다.

3단계에서는 HR 응용 프로그램의 각 소비자 테넌트(예: Contoso 및 Fabrikam)에 고유한 서비스 주체 개체가 제공됩니다. 각각은 런타임에 응용 프로그램 인스턴스의 사용을 나타내며, 이는 해당 관리자가 동의한 사용 권한으로 관리됩니다.

## <a name="next-steps"></a>다음 단계
응용 프로그램의 응용 프로그램 개체는 OData [응용 프로그램 엔터티][AAD-Graph-App-Entity]에 의해 표시된 대로 Azure AD Graph API, [Azure Portal의][AZURE-Portal] 응용 프로그램 매니페스트 편집기 또는 [Azure AD PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0)을 통해 액세스할 수 있습니다.

응용 프로그램의 서비스 주체 개체는 OData [서비스 주체 엔터티][AAD-Graph-Sp-Entity]에 의해 표시된 대로 Azure AD Graph API 또는 [Azure AD PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0)을 통해 액세스할 수 있습니다.

[Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/)는 응용 프로그램 및 서비스 주체 개체 둘 다를 쿼리하는 데 유용합니다.

<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AZURE-Portal]: https://portal.azure.com
