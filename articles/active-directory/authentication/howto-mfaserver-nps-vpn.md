---
title: Azure MFA 및 타사 VPN 고급 시나리오
description: Azure MFA가 Cisco, Citrix 및 Juniper와 통합하기 위한 단계별 구성 가이드입니다.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 08/25/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 725f2893419b2b921f262fc4516f0f8a2ecf4c37
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="advanced-scenarios-with-azure-multi-factor-authentication-and-third-party-vpn-solutions"></a>Azure Multi-Factor Authentication 및 타사 VPN 솔루션을 사용한 고급 시나리오

Azure Multi-Factor Authentication은 다양한 타사 VPN 솔루션과 원활하게 연결하는 데 사용할 수 있습니다. 이 문서는 Cisco ® ASA VPN 어플라이언스, Citrix NetScaler SSL VPN 어플라이언스 및 Juniper Networks Secure Access/Pulse Secure Connect Secure SSL VPN 어플라이언스를 중점적으로 다룹니다. 이러한 세 가지 일반적인 어플라이언스를 해결하기 위해 구성 지침을 작성했습니다. Multi-Factor Authentication Server는 RADIUS, LDAP, IIS 또는 AD FS에 대한 클레임 기반 인증을 사용하는 다른 시스템과 통합할 수도 있습니다. [MFA 서버 구성](howto-mfaserver-deploy.md#next-steps)에서 자세한 내용을 볼 수 있습니다.

## <a name="cisco-asa-vpn-appliance-and-azure-multi-factor-authentication"></a>Cisco ASA VPN 어플라이언스 및 Azure Multi-Factor Authentication
Azure Multi-factor Authentication은 Cisco AnyConnect ® VPN 로그인 및 포털 액세스에 대한 보안을 추가하기 위해 Cisco ® ASA VPN 어플라이언스와 통합됩니다.  LDAP 또는 RADIUS 프로토콜을 사용할 수 있습니다.  자세한 단계별 구성 가이드를 다운로드하려면 다음 중 하나를 선택합니다.

| 구성 가이드 | 설명 |
| --- | --- |
| [Anyconnect VPN을 사용한 Cisco ASA 및 LDAP를 위한 Azure MFA 구성](http://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | LDAP를 사용하는 Azure MFA와 Cisco ASA VPN 어플라이언스 통합 |
| [Anyconnect VPN을 사용한 Cisco ASA 및 RADIUS를 위한 Azure MFA 구성](http://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | RADIUS를 사용하는 Azure MFA와 Cisco ASA VPN 어플라이언스 통합 |

## <a name="citrix-netscaler-ssl-vpn-and-azure-multi-factor-authentication"></a>Citrix NetScaler SSL VPN 및 Azure Multi-Factor Authentication
Azure Multi-factor Authentication은 Citrix NetScaler SSL VPN 로그인 및 포털 액세스에 대한 보안을 추가하기 위해 Citrix NetScaler SSL VPN 어플라이언스와 통합됩니다.  LDAP 또는 RADIUS 프로토콜을 사용할 수 있습니다.  자세한 단계별 구성 가이드를 다운로드하려면 다음 중 하나를 선택합니다.

| 구성 가이드 | 설명 |
| --- | --- |
| [Citrix NetScaler SSL VPN 및 LDAP를 위한 Azure MFA 구성](http://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | LDAP를 사용하는 Azure MFA와 Citrix NetScaler SSL VPN 어플라이언스 통합 |
| [Citrix NetScaler SSL VPN 및 RADIUS를 위한 Azure MFA 구성](http://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | RADIUS를 사용하는 Azure MFA와 Citrix NetScaler SSL VPN 어플라이언스 통합 |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-multi-factor-authentication"></a>Juniper/Pulse Secure SSL VPN 어플라이언스 및 Azure Multi-Factor Authentication
Azure Multi-factor Authentication은 Juniper/Pulse Secure SSL VPN 로그인 및 포털 액세스에 대한 보안을 추가하기 위해 Juniper/Pulse Secure SSL VPN 어플라이언스와 통합됩니다.  LDAP 또는 RADIUS 프로토콜을 사용할 수 있습니다.  자세한 단계별 구성 가이드를 다운로드하려면 다음 중 하나를 선택합니다.

| 구성 가이드 | 설명 |
| --- | --- |
| [Juniper/Pulse Secure SSL VPN 및 LDAP를 위한 Azure MFA 구성](http://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | LDAP를 사용하는 Azure MFA와 Juniper/Pulse Secure SSL VPN 어플라이언스 통합 |
| [Juniper/Pulse Secure SSL VPN 및 RADIUS를 위한 Azure MFA 구성](http://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | RADIUS를 사용하는 Azure MFA와 Juniper/Pulse Secure SSL VPN 어플라이언스 통합 |

## <a name="next-steps"></a>다음 단계

- [Azure Multi-Factor Authentication에 대한 NPS 확장을 사용하여 기존 인증 인프라 보강](howto-mfa-nps-extension.md)

- [Azure Multi-Factor Authentication 구성 설정](howto-mfa-mfasettings.md)