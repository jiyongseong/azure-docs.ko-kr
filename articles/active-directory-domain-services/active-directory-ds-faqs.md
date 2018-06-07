---
title: FAQ - Azure Active Directory Domain Services | Microsoft Docs
description: Azure Active Directory Domain Services에 대해 자주 묻는 질문과 대답입니다.
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: maheshu
ms.openlocfilehash: 1cfd0570315d5a1c6587ade164edf0a837453406
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2018
---
# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Azure Active Directory Domain Services: 자주 묻는 질문과 대답(FAQ)
이 페이지는 Azure Active Directory Domain Services에 대한 자주 묻는 질문을 응답합니다. 업데이트를 계속 확인합니다.

## <a name="troubleshooting-guide"></a>문제 해결 가이드
Azure AD Domain Services를 구성 또는 관리할 때 나타날 수 있는 일반적인 문제에 대한 해결책은 [문제 해결 가이드](active-directory-ds-troubleshooting.md)를 참조합니다.

## <a name="configuration"></a>구성
### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>단일 Azure AD 디렉터리에 여러 관리되는 도메인을 만들 수 있나요?
번호 단일 Azure AD 디렉터리에 Azure AD Domain Services에서 제공하는 단일 관리되는 도메인만 만들 수 있습니다.  

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Azure Resource Manager 가상 네트워크에서 Azure AD Domain Services를 사용할 수 있습니까?
예. Azure Resource Manager 가상 네트워크에서 Azure AD Domain Services를 사용할 수 있습니다. 클래식 Azure 가상 네트워크는 새 관리되는 도메인을 만드는 데 더 이상 지원되지 않습니다.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>기존 관리되는 도메인을 클래식 가상 네트워크에서 Resource Manager 가상 네트워크로 마이그레이션할 수 있나요?
현재는 아닙니다. Microsoft는 앞으로 클래식 가상 네트워크에서 Resource Manager 가상 네트워크로 기존 관리되는 도메인을 마이그레이션하는 데 관한 메커니즘을 제공할 예정입니다.

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Azure CSP(클라우드 솔루션 공급자) 구독에서 Azure AD Domain Services를 사용할 수 있나요?
예. [Azure CSP 구독에서 Azure AD Domain Services](active-directory-ds-csp.md)를 활성화할 수 있는 방법을 참조합니다.

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>페더레이션된 Azure AD 디렉터리에서 Azure AD Domain Services를 사용할 수 있습니까? 암호 해시를 Azure AD와 동기화하지 않았습니다. 이 디렉터리에 Azure AD Domain Services를 사용할 수 있습니까?
번호 Azure AD Domain Services에서는 NTLM 또는 Kerberos를 통해 사용자를 인증하기 위해 사용자 계정의 암호 해시에 액세스해야 합니다. 페더레이션된 디렉터리에서는 Azure AD 디렉터리에 암호 해시가 저장되지 않습니다. 따라서 Azure AD Domain Services는 이러한 Azure AD 디렉터리와 함께 작동하지 않습니다.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Azure AD Domain Services를 내 구독 내의 여러 가상 네트워크에서 사용할 수 있나요?
서비스 자체는 이 시나리오를 직접 지원하지 않습니다. 관리되는 도메인은 한 번에 하나의 가상 네트워크에서만 사용할 수 있습니다. 그러나 다른 가상 네트워크에 Azure AD Domain Services를 노출하기 위해 여러 가상 네트워크 간의 연결을 구성할 수 있습니다. [Azure에서 가상 네트워크를 연결](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)하는 방법을 확인합니다.

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>PowerShell을 사용하여 Azure AD 도메인 서비스를 사용할 수 있나요?
예. [PowerShell을 사용하여 Azure AD Domain Services를 사용하기 위한 방법](active-directory-ds-enable-using-powershell.md)을 참조하세요.

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Resource Manager 템플릿을 사용하여 Azure AD Domain Services를 사용할 수 있나요?
예. [PowerShell을 사용하여 Azure AD Domain Services를 사용하기 위한 방법](active-directory-ds-enable-using-powershell.md)을 참조하세요.

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Azure AD 도메인 서비스 관리되는 도메인에 도메인 컨트롤러를 추가할 수 있나요?
번호 Azure AD 도메인 서비스에서 제공하는 도메인은 관리되는 도메인입니다. 이 도메인에 대해 도메인 컨트롤러를 프로비전, 구성 또는 다른 방식으로 관리하지 않아도 됩니다. 이러한 관리 작업은 Microsoft에서 서비스로 제공합니다. 따라서 관리되는 도메인에 대해 추가 도메인 컨트롤러(읽기-쓰기 또는 읽기 전용)를 추가할 수 없습니다.

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>내 디렉터리에 초대된 게스트 사용자가 Azure AD Domain Services를 사용할 수 있나요?
번호 [Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) 초대 프로세스를 사용하여 Azure AD 디렉터리에 초대된 게스트 사용자는 Azure AD Domain Services 관리되는 도메인과 동기화됩니다. 그러나 이러한 사용자에 대한 암호는 Azure AD 디렉터리에 저장되지 않습니다. 따라서 Azure AD Domain Services는 이러한 사용자를 위한 NTLM 및 Kerberos 해시를 관리되는 도메인과 동기화할 방법이 없습니다. 결과적으로, 이러한 사용자는 관리되는 도메인에 로그인하거나 관리되는 도메인에 컴퓨터를 가입할 수 없습니다.

## <a name="administration-and-operations"></a>관리 및 운영
### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>원격 데스크톱을 사용하여 관리되는 도메인의 도메인 컨트롤러에 연결할 수 있습니까?
번호 사용자에게는 원격 데스크톱을 통해 관리되는 도메인의 도메인 컨트롤러에 연결할 사용 권한이 없습니다. 'AAD DC Administrators' 그룹의 멤버는ADAC(Active Directory 관리 센터) 또는 AD PowerShell 같은 AD 관리 도구를 사용하여 관리되는 도메인을 관리할 수 있습니다. 이러한 도구는 관리되는 도메인에 가입된 Windows 서버의 '원격 서버 관리 도구' 기능을 사용하여 설치할 수 있습니다.

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Azure AD 도메인 서비스를 사용한 적이 있습니다. 이 도메인에 도메인 가입 컴퓨터를 사용하려면 사용자 계정은 무엇입니까?
관리 그룹 ‘AAD DC Administrators’의 멤버는 컴퓨터를 도메인에 가입시킬 수 있습니다. 또한 이 그룹의 멤버에게는 도메인에 가입 된 컴퓨터에 대한 원격 데스크톱 액세스가 부여됩니다.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Azure AD Domain Services에서 제공하는 관리되는 도메인에 대한 도메인 관리자 권한이 부여됩니까?
번호 관리되는 도메인에 대한 관리 권한이 부여되지 않았습니다. 사용자는 도메인 내에서 '도메인 관리자' 및 '엔터프라이즈 관리자' 권한을 모두 사용할 수 없습니다. 또한 Azure AD 디렉터리 내에서 기존 도메인 관리자 또는 엔터프라이즈 관리자 그룹에는 도메인에서 도메인/엔터프라이즈 관리자 권한이 부여되지 않습니다.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>관리되는 도메인에서 LDAP 또는 다른 AD 관리 도구를 사용하여 그룹 멤버 자격을 수정할 수 있습니까?
번호 Azure AD 도메인 서비스에서 서비스를 제공하는 도메인에 그룹 멤버 자격을 수정할 수 없습니다. 사용자 특성에 대해서도 동일하게 적용됩니다. 그러나 Azure AD 또는 온-프레미스 도메인에서 그룹 구성원 자격 또는 사용자 특성을 변경할 수도 있습니다. 이러한 변경 내용은 Azure AD Domain Services에 자동으로 동기화됩니다.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Azure AD 디렉터리에 적용한 변경 사항이 내 관리되는 도메인에 표시되는 데 얼마나 걸리나요?
Azure AD UI 또는 PowerShell을 사용하여 Azure AD 디렉터리에 적용된 변경 사항은 관리되는 도메인에 동기화되어 있습니다. 이 동기화 프로세스는 백그라운드에서 실행됩니다. 디렉터리의 일회성 초기 동기화가 완료되면 Azure AD에 적용된 변경 사항이 관리되는 도메인에 반영되는 데 일반적으로 약 20분이 소요됩니다.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Azure AD Domain Services에서 제공하는 관리되는 도메인의 스키마를 확장할 수 있습니까?
번호 스키마는 관리되는 도메인에 대해 Microsoft에서 관리합니다. 스키마 확장은 Azure AD 도메인 서비스에서 지원되지 않습니다.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>관리되는 도메인에서 DNS 레코드를 수정하거나 추가할 수 있습니까?
예. 관리되는 도메인에서 DNS 레코드를 수정하기 위해 'AAD DC Administrators' 그룹의 멤버에게 'DNS 관리자' 권한이 부여됩니다. 이러한 멤버는 DNS를 관리하기 위해 관리되는 도메인에 가입한 Windows Server를 실행하는 컴퓨터에서 DNS 관리자 콘솔을 사용할 수 있습니다. DNS 관리자 콘솔을 사용하려면 서버의 '원격 서버 관리 도구' 선택적 기능의 일부인 'DNS 서버 도구'를 설치합니다. [DNS를 관리하고, 모니터링하고 문제를 해결하는 유틸리티](https://technet.microsoft.com/library/cc753579.aspx) 에 대한 자세한 정보는 TechNet를 참조하세요.

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>관리되는 도메인에 대한 암호 수명 정책은 무엇인가요?
Azure AD Domain Services 관리되는 도메인의 기본 암호 수명은 90일입니다. 이 암호 수명은 Azure AD에 구성된 암호 수명과 동기화되지 않습니다. 따라서 사용자의 암호가 관리되는 도메인에는 만료되지만 Azure AD에서는 여전히 유효한 상황이 발생할 수 있습니다. 이러한 시나리오에서 사용자는 Azure AD에서 자신의 암호를 변경해야 하며, 새 암호는 관리되는 도메인과 동기화됩니다. 또한 사용자 계정에 대한 'password-does-not-expire' 및 'user-must-change-password-at-next-logon' 특성은 관리되는 도메인과 동기화되지 않습니다.

## <a name="billing-and-availability"></a>요금 청구 및 가용성
### <a name="is-azure-ad-domain-services-a-paid-service"></a>Azure AD Domain Services는 유료 서비스인가요?
예. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/active-directory-ds/)를 참조하세요.

### <a name="is-there-a-free-trial-for-the-service"></a>서비스에 대한 무료 평가판이 있습니까?
이 서비스는 Azure을 위해 무료 평가판에 포함됩니다. [Azure의 1개월 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services 관리되는 도메인을 일시 중지할 수 있나요? 
번호 Azure AD Domain Services 관리되는 도메인을 사용하도록 설정했으면 관리되는 도메인을 사용하지 않거나/삭제할 때까지 선택한 가상 네트워크에서 해당 서비스가 제공됩니다. 서비스를 일시 중지할 방법은 없습니다. 관리되는 도메인을 삭제할 때까지 시간 기준으로 계속 청구됩니다.

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Enterprise Mobility Suite(EMS)의 일부로 Azure AD 도메인 서비스를 가져올 수 있습니까? Azure AD Domain Services를 사용하려면 Azure AD Premium이 필요합니까?
번호 Azure AD Domain Services는 종량제 Azure 서비스이며 EMS의 일부가 아닙니다. Azure AD Domain Services는 모든 에디션의 Azure AD(무료, 기본 및 프리미엄)에 사용할 수 있습니다. 사용 방식에 따라 시간 단위로 청구됩니다.

### <a name="what-azure-regions-is-the-service-available-in"></a>어떤 Azure 지역에서 서비스를 사용할 수 있습니까?
Azure AD Domain Services를 사용할 수 있는 Azure 지역의 목록을 알아보려면 [지역별 Azure 서비스](https://azure.microsoft.com/regions/#services/) 페이지를 참조하세요.
