---
title: "방화벽 뒤에 있는 Key Vault에 액세스 | Microsoft Docs"
description: "방화벽 뒤에 있는 응용 프로그램에서 Azure Key Vault에 액세스하는 방법을 알아봅니다"
services: key-vault
documentationcenter: 
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 50d21774-2ee1-4212-8995-570c9de603c5
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/07/2017
ms.author: ambapat
ms.openlocfilehash: ad31e869d998d29d403ff97c17150c5078ce856d
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2018
---
# <a name="access-azure-key-vault-behind-a-firewall"></a>방화벽 뒤에 있는 Azure Key Vault 액세스
### <a name="q-my-key-vault-client-application-needs-to-be-behind-a-firewall-what-ports-hosts-or-ip-addresses-should-i-open-to-enable-access-to-a-key-vault"></a>Q: 내 주요 자격 증명 모음 클라이언트 응용 프로그램은 방화벽 뒤에 있어야 합니다. 주요 자격 증명 모음에 대한 액세스를 활성화하려면 어떤 포트, 호스트 또는 IP 주소를 열어야 합니까?
주요 자격 증명 모음에 액세스하려면 주요 자격 증명 모음 클라이언트 응용 프로그램은 다양한 기능에 대한 여러 끝점에 액세스해야 합니다.

* Azure AD(Azure Active Directory)를 통한 인증.
* Azure Key Vault의 관리. Azure Resource Manager를 통한 액세스 정책 만들기, 읽기, 업데이트, 삭제 및 설정을 포함합니다.
* Key Vault 자체에 저장된 개체(키와 암호)를 액세스하고 관리하는 작업은 Key Vault 특정 끝점을 통과합니다(예: [https://yourvaultname.vault.azure.net](https://yourvaultname.vault.azure.net)).  

구성 및 환경에 따라 일부의 변형이 있습니다.   

## <a name="ports"></a>포트
모든 3가지 함수(인증, 관리 및 데이터 평면 액세스)의 주요 자격 증명 모음에 대한 모든 트래픽은 HTTPS: 포트 443을 통해 이동합니다. 그러나 CRL의 경우 가끔 HTTP(포트 80) 트래픽이 있습니다. OCSP를 지원하는 클라이언트는 CRL에 도달하지 않아야 하지만 경우에 따라 [http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl)에 도달할 수 있습니다.  

## <a name="authentication"></a>인증
주요 자격 증명 모음 클라이언트 응용 프로그램은 인증을 위해 Azure Active Directory 끝점에 액세스해야 합니다. 사용되는 끝점은 Azure AD 테넌트 구성 및 주체의 형식(사용자 계정, 서비스 주체) 및 계정의 형식, 즉, Microsoft 계정 또는 회사 또는 학교 계정에 따라 달라집니다.  

| 주체 유형 | 끝점:포트 |
| --- | --- |
| Microsoft 계정을 사용하는 사용자<br> (예: user@hotmail.com) |**전역:**<br> login.microsoftonline.com:443<br><br> **Azure 중국:**<br> login.chinacloudapi.cn:443<br><br>**Azure 미국 정부:**<br> login.microsoftonline.us:443<br><br>**Azure 독일:**<br> login.microsoftonline.de:443<br><br> and <br>login.live.com:443 |
| Azure AD로 회사 또는 학교 계정을 사용하는 사용자 또는 서비스 주체(예: user@contoso.com) |**전역:**<br> login.microsoftonline.com:443<br><br> **Azure 중국:**<br> login.chinacloudapi.cn:443<br><br>**Azure 미국 정부:**<br> login.microsoftonline.us:443<br><br>**Azure 독일:**<br> login.microsoftonline.de:443 |
| 회사 또는 학교 계정을 사용하는 사용자 또는 서비스 주체 및 AD FS(Active Directory Federation Services) 또는 다른 페더레이션된 끝점(예: user@contoso.com) |회사 또는 학교 계정에 대한 모든 끝점 및 AD FS 또는 다른 페더레이션된 끝점 |

다른 복잡한 시나리오도 가능합니다. 추가 정보는 [Azure Active Directory 인증 흐름](/documentation/articles/active-directory-authentication-scenarios/), [Azure Active Directory와 응용 프로그램 통합](/documentation/articles/active-directory-integrating-applications/) 및 [Active Directory 인증 프로토콜](https://msdn.microsoft.com/library/azure/dn151124.aspx)을 참조하세요.  

## <a name="key-vault-management"></a>Key Vault 관리
Key Vault 관리(CRUD 및 액세스 정책 설정)의 경우 주요 자격 증명 모음 클라이언트 응용 프로그램은 Azure Resource Manager 끝점에 액세스해야 합니다.  

| 작업의 유형 | 끝점:포트 |
| --- | --- |
| Key Vault 제어 평면 작업<br> - Azure Resource Manager 사용 |**전역:**<br> management.azure.com:443<br><br> **Azure 중국:**<br> management.chinacloudapi.cn:443<br><br> **Azure 미국 정부:**<br> management.usgovcloudapi.net:443<br><br> **Azure 독일:**<br> management.microsoftazure.de:443 |
| Azure Active Directory Graph API |**전역:**<br> graph.windows.net:443<br><br> **Azure 중국:**<br> graph.chinacloudapi.cn:443<br><br> **Azure 미국 정부:**<br> graph.windows.net:443<br><br> **Azure 독일:**<br> graph.cloudapi.de:443 |

## <a name="key-vault-operations"></a>Key Vault 작업
모든 주요 자격 증명 모음 개체(키와 암호) 관리 및 암호화 작업의 경우 주요 자격 증명 모음 클라이언트는 주요 자격 증명 모음 끝점에 액세스해야 합니다. 끝점 DNS 접미사는 주요 자격 증명 모음의 위치에 따라 다릅니다. 주요 자격 증명 모음 끝점은 다음 테이블에 설명된 대로 *vault-name*.*region-specific-dns-suffix* 형식입니다.  

| 작업의 유형 | 끝점:포트 |
| --- | --- |
| 키에 대한 암호화 작업을 포함하는 작업, 키 및 암호 만들기, 읽기, 업데이트 및 삭제, 키 자격 증명 모음 개체(키 또는 암호)의 태그 및 기타 특성 설정 또는 가져오기 |**전역:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure 중국:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure 미국 정부:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure 독일:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 |

## <a name="ip-address-ranges"></a>IP 주소 범위
Key Vault 서비스는 PaaS 인프라와 같은 다른 Azure 리소스를 사용합니다. 따라서 Key Vault 서비스 끝점은 특정 시간에 가지므로 특정 범위의 IP 주소를 제공할 수 없습니다. 방화벽이 IP 주소 범위만을 지원하는 경우 [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653) 문서를 참조하세요. 인증 및 ID(Azure Active Directory)의 경우 응용 프로그램은 [인증 및 ID 주소](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)에서 설명한 끝점에 연결할 수 있어야 합니다.

## <a name="next-steps"></a>다음 단계
Key Vault에 대한 질문이 있으면 [Azure Key Vault 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)을 방문하세요.

