---
title: Azure App Service를 사용하여 PaaS 웹 및 모바일 응용 프로그램 보안 | Microsoft Docs
description: " PaaS 웹 및 모바일 응용 프로그램 보안을 위한 Azure App Service 보안 모범 사례에 대해 알아봅니다. "
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: d2e606fe23a3a6eb9d2310b0932ccec8fcfc518c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Azure App Service를 사용하여 PaaS 웹 및 모바일 응용 프로그램 보안

이 문서에서는 PaaS 웹 및 모바일 응용 프로그램 보안을 위한 [Azure App Services](https://azure.microsoft.com/services/app-service/) 보안 모범 사례에 대해 설명합니다. 이러한 모범 사례는 Azure에 대한 Microsoft와 고객의 경험에서 비롯된 것입니다.

## <a name="azure-app-service"></a>Azure App Service
[Azure App Service](../app-service/app-service-web-overview.md)는 플랫폼이나 장치를 위한 웹 및 모바일 앱을 만들고 어디서나 클라우드 또는 온-프레미스에 있는 데이터에 연결할 수 있는 PaaS 서비스 제품군입니다. 앱 서비스는 이전에 개별적으로 Azure 웹 사이트 및 Azure Mobile Services로 전달한 웹 및 모바일 기능을 포함합니다. 또한 비즈니스 프로세스를 자동화하고 클라우드 API를 호스팅하는 새 기능을 포함합니다. App Service는 단일 통합 서비스로서 웹, 모바일 및 통합 시나리오에 풍부한 기능 집합을 제공합니다.

## <a name="best-practices"></a>모범 사례

App Service를 사용하는 경우 다음 모범 사례를 따릅니다.

- [Azure AD(Active Directory)를 통해 인증](../app-service/app-service-authentication-overview.md)합니다. App Service는 ID 공급자를 위한 OAuth 2.0 서비스를 제공합니다. OAuth 2.0은 클라이언트 개발자의 단순성에 기반하여 웹 응용 프로그램, 데스크톱 응용 프로그램 및 휴대폰에 대한 특정 권한 부여 흐름을 제공합니다. Azure AD는 OAuth 2.0을 사용하여 모바일 및 웹 응용 프로그램에 대한 액세스 권한을 부여합니다.
- 알아야 할 필요성과 최소 권한 보안 원칙에 따라 액세스를 제한합니다. 데이터 액세스를 위한 보안 정책을 시행하려는 조직에서는 액세스를 반드시 제한해야 합니다. RBAC(역할 기반 Access Control)를 사용하여 특정 범위에서 사용자, 그룹 및 응용 프로그램에 권한을 할당할 수 있습니다. 사용자에게 응용 프로그램 액세스 권한을 부여하는 방법에 대한 자세한 내용은 [액세스 관리 시작](../role-based-access-control/overview.md)을 참조하세요.
- 키를 보호합니다. 보안이 아무리 훌륭하더라도 구독 키를 분실하면 안됩니다. Azure Key Vault는 클라우드 응용 프로그램 및 서비스에서 사용되는 암호화 키 및 비밀을 보호하는데 도움이 됩니다. Key Vault를 사용하여, 키와 비밀(예: 인증 키, 저장소 계정 키, 데이터 암호화 키, PFX 파일 및 암호)을 암호화에 하드웨어 보안 모듈(HSM)로 보호된 키를 사용합니다. 추가된 보증을 위해, HSM에서 키를 생성하거나 가져올 수 있습니다. 자세한 내용은 [Azure Key Vault](../key-vault/key-vault-whatis.md)를 참조하세요. 또한 Key Vault를 사용하여 자동 갱신으로 TLS 인증서를 관리할 수도 있습니다.
- 들어오는 원본 IP 주소를 제한합니다. [App Service 환경](../app-service/environment/intro.md)에는 NSG(네트워크 보안 그룹)를 통해 들어오는 원본 IP 주소를 제한하는 데 도움이 되는 가상 네트워크 통합 기능이 있습니다. Azure VNets(Virtual Networks)에 익숙하지 않은 사용자는 라우팅할 수 있는 비인터넷 네트워크에 대다수의 Azure 리소스를 배치하여 액세스를 제어할 수 있습니다. 자세한 내용은 [Azure Virtual Network에 앱 통합](../app-service/web-sites-integrate-with-vnet.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
이 문서에서는 PaaS 웹 및 모바일 응용 프로그램 보안을 위한 App Service 보안 모범 사례 모음을 소개했습니다. PaaS 배포 보안 유지에 대한 자세한 내용은 다음을 참조하세요.

- [PaaS 배포 보안](security-paas-deployments.md)
- [Azure SQL Database 및 SQL Data Warehouse를 사용하여 PaaS 웹 및 모바일 응용 프로그램 보안](security-paas-applications-using-sql.md)
