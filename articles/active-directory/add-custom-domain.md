---
title: Azure AD에 사용자 지정 도메인 추가 | Microsoft Docs
description: Azure Active Directory에서 사용자 지정 도메인을 추가하는 방법을 설명합니다.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 11/14/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: 37890ac856a6b6bd89acc17973327b239a583997
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="quickstart-add-a-custom-domain-name-to-azure-active-directory"></a>빠른 시작: Azure Active Directory에 사용자 지정 도메인 이름 추가

모든 Azure AD 디렉터리는 *domainname*.onmicrosoft.com 양식의 초기 도메인 이름으로 제공됩니다. 초기 도메인 이름을 변경하거나 삭제할 수 없지만 Azure AD에 회사 도메인 이름을 추가할 수 있습니다. 예를 들어, 조직에는 비즈니스를 하는 데 사용하는 다른 도메인 이름 및 회사 도메인 이름을 사용하여 로그인하는 사용자가 있을 수 있습니다. Azure AD에 사용자 지정 도메인 이름을 추가하면 사용자에게 ‘alice@contoso.com’ 같은 친숙한 사용자 이름을 디렉터리에 할당할 수 있습니다. (‘alice@*domain name*.onmicrosoft.com’ 대신) 프로세스는 간단합니다.

1. 디렉터리에 사용자 지정 도메인 이름 추가
2. 도메인 이름 등록 기관의 도메인 이름에 대한 DNS 항목 추가
3. Azure AD에서 사용자 지정 도메인 이름 확인

## <a name="add-the-custom-domain-name-to-your-directory"></a>디렉터리에 사용자 지정 도메인 이름 추가
1. 디렉터리에 대한 전역 관리자인 계정으로 [Azure Portal](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)에 로그인합니다.
2. 왼쪽에서 **사용자 지정 도메인 이름**을 선택합니다.
3. **사용자 지정 도메인 추가**를 선택합니다.
   
   ![[추가] 명령 선택](./media/add-custom-domain/add-custom-domain.png)
5. **사용자 지정 도메인 이름**에서 상자에 사용자 지정 도메인 이름(예: ‘contoso.com’)을 입력하고 **도메인 추가**를 선택합니다. .com, .net 또는 그 외 최상위 확장명을 포함해야 합니다.
6. ***domainname***(즉, 새 도메인 이름은 제목)에서 Azure AD에서 사용자 지정 도메인 이름을 확인하기 위해 나중에 사용할 DNS 항목 정보를 수집합니다.
   
   ![DNS 항목 정보 가져오기](./media/active-directory-domains-add-azure-portal/get-dns-info.png)

> [!TIP]
> Azure AD에서 온-프레미스 Windows Server AD를 페더레이션하려는 경우 Azure AD Connect 도구를 실행하여 디렉터리 동기화를 실행할 때 **내 로컬 Active Directory에서 Single Sign-On하도록 이 도메인을 구성하려고 합니다.** 확인란을 선택해야 합니다. 마법사의 **Azure AD 도메인** 단계에서 온-프레미스 디렉터리와 페더레이션하기 위해 선택한 동일한 도메인 이름을 등록해야 합니다. 마법사의 어떤 단계가 [이 지침에서](./connect/active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation)와 유사한지를 확인할 수 있습니다. Azure AD Connect 도구가 없는 경우 [여기서 다운로드](http://go.microsoft.com/fwlink/?LinkId=615771)할 수 있습니다.

## <a name="add-a-dns-entry-for-the-domain-name-at-the-domain-name-registrar"></a>도메인 이름 등록 기관의 도메인 이름에 대한 DNS 항목 추가
사용자 지정 도메인 이름을 Azure AD로 사용하는 다음 단계에서는 도메인에 대한 DNS 영역 파일을 업데이트합니다. Azure AD에서는 조직이 사용자 지정 도메인 이름을 소유하는지를 확인할 수 있습니다. Azure 내에서 Azure/Office 365/외부 DNS 레코드에 대해 [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal)를 사용하거나 [서로 다른 DNS 등록 기관](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)에서 DNS 항목을 추가할 수 있습니다.

1. 도메인에 대한 도메인 이름 등록 기관에 로그인합니다. DNS 항목을 업데이트하기 위해 액세스할 수 없는 경우 액세스 권한이 있는 사용자 또는 팀에게 2단계를 완료하고 완료될 때를 알려주도록 요청합니다.
2. Azure AD에서 제공되는 DNS 항목을 추가하여 도메인에 대한 DNS 영역 파일을 업데이트합니다. DNS 항목은 메일 라우팅이나 웹 호스팅 같은 어떠한 동작도 변경하지 않습니다.

## <a name="verify-the-custom-domain-name-in-azure-ad"></a>Azure AD에서 사용자 지정 도메인 이름 확인
DNS 항목을 추가하고 나면, Azure AD에서 도메인 이름을 확인할 준비가 되었습니다. DNS 레코드가 전파된 후에 도메인 이름을 확인할 수 있습니다. 이 전파는 보통 몇 초 밖에 걸리지 않지만 한 시간 이상이 걸릴 경우도 있습니다. 확인이 처음에 작동하지 않으면 나중에 재시도하세요.

1. 테넌트의 전역 관리자 계정으로 [Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)에 로그인합니다.
2. **사용자 지정 도메인 이름**을 선택합니다.
3. 확인하려는 확인되지 않은 도메인 이름을 선택합니다.
4. 입력한 내용을 확인하고 **확인** 을 선택하여 확인을 완료합니다.

이제 [사용자 지정 도메인 이름을 포함하는 사용자 이름을 할당](active-directory-users-create-azure-portal.md)할 수 있습니다. 사용자 지정 도메인 이름을 사용하여 클라우드 기반 사용자 계정을 만들거나 이전에 동기화된 온-프레미스 사용자 계정 정보를 업데이트합니다. [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) 또는 [Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)를 사용하여 동기화된 사용자 계정 도메인 접미사 정보를 변경할 수도 있습니다.

> [!TIP]
> 최대 900개의 관리되는 도메인 이름을 추가할 수 있습니다. 온-프레미스 Active Directory와의 페더레이션을 위해 모든 도메인을 구성하려는 경우 각 디렉터리에서 최대 450개의 도메인 이름을 추가할 수 있습니다. 자세한 내용은 [페더레이션 및 관리되는 도메인 이름](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain-concepts#federated-and-managed-domain-names)을 참조하세요.

## <a name="troubleshooting"></a>문제 해결
사용자 지정 도메인 이름을 확인할 수 없는 경우 다음 문제 해결 단계를 사용해 보세요.

1. **한 시간을 대기합니다**. Azure AD가 도메인을 확인할 수 있기 전에 DNS 레코드를 전파해야 합니다. 이 프로세스는 한 시간 이상 걸릴 수 있습니다.
2. **DNS 레코드를 입력하고 정확한지 확인합니다**. 도메인에 대한 도메인 이름 등록 기관의 웹 사이트에서 이 단계를 완료합니다. 다음과 같은 경우 Azure AD에서 도메인 이름을 확인할 수 없음 
  * DNS 항목이 DNS 영역 파일에 나타나지 않음
  * Azure AD가 제공한 DNS 항목과 정확하게 일치하지 않음 
  
  도메인 이름 등록 기관에서 도메인에 대한 DNS 레코드를 업데이트하기 위한 액세스 권한이 없는 경우 조직에서 이 액세스 권한이 있는 사람이나 팀과 DNS 항목을 공유하고 DNS 항목을 추가하도록 요청합니다.
3. **Azure AD에서 도메인 이름을 다른 디렉터리에서 삭제합니다**. 단일 디렉터리에서 도메인 이름을 확인할 수 있습니다. 현재 다른 디렉터리에서 도메인 이름이 확인되지 않은 경우 다른 디렉터리에서 삭제될 때까지 새 디렉터리에서 확인할 수 없습니다. 도메인 이름을 삭제하는 방법에 대해 자세히 알아보려면 [사용자 지정 도메인 이름 관리](active-directory-domains-manage-azure-portal.md)를 참고합니다.    

이 문서의 단계를 반복하여 각 도메인 이름을 추가합니다.

## <a name="learn-more"></a>자세한 정보
[Azure AD에서 사용자 지정 도메인 이름의 개념적 개요](active-directory-domains-manage-azure-portal.md)

[사용자 지정 도메인 이름 관리](active-directory-domains-manage-azure-portal.md)

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서 Azure AD에 사용자 지정 도메인을 추가하는 방법을 알아보았습니다. 

다음 링크를 사용하여 Azure Portal에서 Azure AD에 새로운 사용자 지정 도메인을 추가할 수 있습니다.

> [!div class="nextstepaction"]
> [사용자 지정 도메인 추가](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/QuickStart) 