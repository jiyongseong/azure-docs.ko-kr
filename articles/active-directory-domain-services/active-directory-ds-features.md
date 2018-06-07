---
title: "Azure Active Directory Domain Services: 기능 | Microsoft Docs"
description: "Azure Active Directory Domain Services의 기능"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 8d1c3eb3-1022-4add-a919-c98cc6584af1
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.openlocfilehash: af0670a44937bb14df9afc206ea4601c0b9289a2
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2018
---
# <a name="azure-ad-domain-services"></a>Azure AD Domain Services
## <a name="features"></a>기능
다음 기능은 Azure AD Domain Services 관리되는 도메인에서 사용할 수 있습니다.

* **간단한 배포 환경:** 몇 번의 클릭만으로 Azure AD 테넌트에 Azure AD 도메인 서비스를 사용하도록 설정할 수 있습니다. Azure AD 테넌트가 클라우드 테넌트이든 온-프레미스 디렉터리와 동기화되든 간에 관리되는 도메인을 신속하게 프로비전할 수 있습니다.
* **도메인 가입 지원:** 관리되는 도메인을 사용할 수 있는 Azure 가상 네트워크에서 컴퓨터를 쉽게 도메인에 가입시킬 수 있습니다. Windows 클라이언트 및 서버 운영 체제의 도메인 가입 환경이 Azure AD Domain Services에서 처리하는 도메인을 대상으로 원활하게 작동합니다. 이러한 도메인을 대상으로 자동화된 도메인 가입 도구도 사용할 수 있습니다.
* **Azure AD 디렉터리당 하나의 도메인 인스턴스:** Azure AD 디렉터리마다 단일 Active Directory 도메인을 만들 수 있습니다.
* **사용자 지정 이름으로 도메인 만들기:** Azure AD Domain Services를 사용하여 사용자 지정 이름(예: 'contoso100.com')으로 도메인을 만들 수 있습니다. 확인된 또는 확인되지 않은 도메인 이름 중 하나를 사용할 수 있습니다. 필요에 따라 Azure AD 디렉터리에서 제공하는 기본 제공 도메인 접미사(예: '*.onmicrosoft.com')로 도메인을 만들 수도 있습니다.
* **Azure AD와 통합:** Azure AD 도메인 서비스에 대한 복제를 구성하거나 관리할 필요가 없습니다. Azure AD 디렉터리의 사용자 계정, 그룹 멤버 자격 및 사용자 자격 증명(암호)을 Azure AD Domain Services에서 자동으로 사용할 수 있습니다. Azure AD 테넌트 또는 온-프레미스 디렉터리에서 새 사용자, 그룹 또는 특성 변경 사항은 Azure AD Domain Services에 자동으로 동기화됩니다.
* **NTLM 및 Kerberos 인증:** NTLM 및 Kerberos 인증 지원 기능을 사용하여 Windows 통합 인증을 사용하는 응용 프로그램을 배포할 수 있습니다.
* **회사 자격 증명/암호 사용:** Azure AD 테넌트의 사용자 암호가 Azure AD Domain Services에서 작동합니다. 사용자는 도메인 가입 컴퓨터에 대해 회사 자격 증명을 사용할 수 있고 대화형으로 또는 원격 데스크톱을 통해 로그인할 수 있으며 관리되는 도메인에 대해 인증할 수 있습니다.
* **LDAP 바인딩 및 LDAP 읽기 지원:** Azure AD Domain Services에서 처리하는 도메인에서 사용자를 인증하기 위해 LDAP 바인딩에 의존하는 응용 프로그램을 사용할 수 있습니다. 또한 LDAP 읽기 작업을 사용하여 디렉터리에서 사용자/컴퓨터 특성을 쿼리하는 응용 프로그램도 Azure AD 도메인 서비스에서 작동할 수 있습니다.
* **보안 LDAP (LDAPS):** 보안 LDAP (LDAPS)를 통해 디렉터리에 대한 액세스를 사용하도록 설정할 수 있습니다. 보안 LDAP 액세스는 기본적으로 가상 네트워크 내에서 사용할 수 있습니다. 하지만 인터넷을 통한 보안 LDAP 액세스 또한 필요에 따라 사용할 수 있습니다.
* **그룹 정책:** 사용자 계정과 도메인 가입 컴퓨터에 대해 필수 보안 정책을 준수하도록 하기 위해 사용자 및 컴퓨터 컨테이너마다 기본 제공 단일 GPO를 사용할 수 있습니다. [그룹 정책을 관리](active-directory-ds-admin-guide-administer-group-policy.md)하기 위해 자체 사용자 지정 GPO를 만든 후 사용자 지정 조직 구성 단위에 할당할 수도 있습니다.
* **DNS 관리:** 'AAD DC 관리자' 그룹 구성원은 DNS 관리 MMC 스냅인과 같은 친숙한 DNS 관리 도구를 사용하여 관리되는 도메인에 대한 DNS를 관리할 수 있습니다.
* **사용자 지정 조직 구성 단위(OU) 만들기:** 'AAD DC 관리자' 그룹의 구성원은 관리되는 도메인에서 사용자 지정 OU를 만들 수 있습니다. 이러한 사용자에게 사용자 지정 OU를 통해 모든 관리 권한이 부여되므로, 이러한 사용자 지정 OU 내에서 서비스 계정, 컴퓨터, 그룹 등을 추가/제거할 수 있습니다.
* **여러 Azure 지역에서 사용 가능:** Azure AD Domain Services를 사용할 수 있는 Azure 지역의 목록을 알아보려면 [지역별 Azure 서비스](https://azure.microsoft.com/regions/#services/) 페이지를 참조하세요.
* **고가용성:** Azure AD Domain Services는 도메인에 고가용성을 제공합니다. 이 기능은 향상된 서비스 가동 시간과 오류 복원을 보장합니다. 기본 제공 상태 모니터링은 새 인스턴스를 생성하여 실패한 인스턴스를 대체하고 도메인에 연속 서비스를 제공하는 방법으로 오류에 대한 자동 수정 기능을 제공합니다.
* **익숙한 관리 도구 사용:** 관리되는 도메인을 관리하기 위해 Active Directory 관리 센터 또는 Active Directory PowerShell과 같은 익숙한 Windows Server Active Directory 관리 도구를 사용할 수 있습니다.
