---
title: Azure Active Directory에서 사용자 지정 도메인 이름 관리 | Microsoft Docs
description: Azure Active Directory에서 도메인 이름 관리에 대한 관리 개념 및 방법
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 11/14/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.openlocfilehash: 81c2371d5dbb17399071c80ff4e8b81813ed014c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Azure Active Directory에서 사용자 지정 도메인 이름 관리
도메인 이름은 많은 디렉터리 리소스에 대한 식별자의 중요한 부분입니다. 사용자의 경우 사용자 이름 또는 메일 주소 부분이고 그룹의 경우 주소 부분이며 응용 프로그램의 경우 앱 ID URI 부분일 수 있습니다. Azure AD(Azure Active Directory)의 리소스는 리소스를 포함하는 디렉터리가 소유하는 이미 확인된 도메인 이름을 포함할 수 있습니다. 전역 관리자만 Azure AD에서 도메인 관리 작업을 수행할 수 있습니다.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Azure AD 디렉터리에 대한 주 도메인 이름 설정
디렉터리를 만든 경우 'contoso.onmicrosoft.com'과 같은 초기 도메인 이름은 주 도메인 이름이기도 합니다. 주 도메인은 새 사용자를 만들 때 새 사용자에 대한 기본 도메인 이름입니다. 주 도메인 이름을 설정하면 관리자에 대한 프로세스를 간소화하여 포털에 새 사용자를 만듭니다. 주 도메인 이름을 변경하려면:

1. 디렉터리에 대한 전역 관리자인 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory**를 선택합니다.
3. **사용자 지정 도메인 이름**을 선택합니다.
     
   ![사용자 관리 열기](./media/active-directory-domains-manage-azure-portal/add-custom-domain.png)
4. 기본 도메인으로 사용할 도메인의 이름을 선택합니다.
5. **Make primary** 명령을 선택합니다. 메시지가 표시되면 선택을 확인합니다.
   
   ![주 도메인 이름 만들기](./media/active-directory-domains-manage-azure-portal/make-primary-domain.png)

페더레이션되지 않은 모든 확인된 사용자 지정 도메인이 되도록 디렉터리에 대한 주 도메인 이름을 변경할 수 있습니다. 디렉터리에 대한 주 도메인을 변경해도 기존 사용자에 대한 사용자 이름은 변경되지 않습니다.

## <a name="add-custom-domain-names-to-your-azure-ad-tenant"></a>Azure AD 테넌트에 사용자 지정 도메인 이름 추가
최대 900개의 관리되는 도메인 이름을 추가할 수 있습니다. 온-프레미스 Active Directory와의 페더레이션을 위해 모든 도메인을 구성하려는 경우 각 디렉터리에서 최대 450개의 도메인 이름을 추가할 수 있습니다. 자세한 내용은 [페더레이션 및 관리되는 도메인 이름](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain-concepts#federated-and-managed-domain-names)을 참조하세요.

## <a name="add-subdomains-of-a-custom-domain"></a>사용자 지정 도메인의 하위 도메인 추가
디렉터리에 'europe.contoso.com'과 같은 세 번째 수준 도메인 이름을 추가하려면 먼저 contoso.com과 같은 두 번째 수준 도메인을 추가 및 확인해야 합니다. Azure AD에서 자동으로 하위 도메인을 확인합니다. 방금 추가한 하위 도메인이 확인되었는지 보려면 도메인을 나열하는 브라우저의 페이지를 새로 고칩니다.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>사용자 지정 도메인 이름의 DNS 등록 기관을 변경하는 경우 수행할 작업
사용자 지정 도메인 이름을 위해 DNS 등록 기관을 변경할 경우 추가 구성 작업 및 중단 없이 Azure AD 자체로 사용자 지정 도메인 이름을 계속 사용할 수 있습니다. Office 365, Intune 또는 Azure AD의 사용자 지정 도메인 이름을 사용하는 다른 서비스에서 사용자 지정 도메인 이름을 사용하는 경우 해당 서비스에 대한 설명서를 참조하세요.

## <a name="delete-a-custom-domain-name"></a>사용자 지정 도메인 이름 삭제
조직이 더 이상 해당 도메인 이름을 사용하지 않는 경우 또는 다른 Azure AD와 해당 도메인 이름을 사용해야 하는 경우, Azure AD에서 사용자 지정 도메인 이름을 삭제할 수 있습니다.

사용자 지정 도메인 이름을 삭제하려면 먼저 디렉터리에 도메인 이름을 사용하는 리소스가 없는지 확인해야 합니다. 다음의 경우 디렉터리에서 도메인 이름을 삭제할 수 없습니다.

* 임의 사용자가 도메인 이름을 포함하는 사용자 이름, 메일 주소 또는 프록시 주소 사용
* 임의 그룹이 도메인 이름을 포함하는 메일 주소 또는 프록시 주소 사용
* Azure AD의 임의 응용 프로그램이 도메인 이름을 포함하는 앱 ID URI 사용

사용자 지정 도메인 이름을 삭제하려면 먼저 Azure AD 디렉터리에서 이러한 리소스를 변경 또는 삭제해야 합니다.

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>PowerShell 또는 Graph API를 사용하여 도메인 이름 관리
Azure Active Directory의 도메인 이름에 대한 대부분의 관리 작업은 Microsoft PowerShell을 사용하거나 프로그래밍 방식으로 Azure AD Graph API를 사용하여 완료할 수도 있습니다.

* [PowerShell을 사용하여 Azure AD에서 도메인 이름 관리](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Graph API를 사용하여 Azure AD에서 도메인 이름 관리](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>다음 단계
* [사용자 지정 도메인 이름 추가](add-custom-domain.md)

