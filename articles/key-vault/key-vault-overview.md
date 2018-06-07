---
title: Azure Key Vault 개요 | Microsoft Docs
description: Azure Key Vault는 보안 비밀 저장소로 작동하는 클라우드 서비스입니다.
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 34af20ee-3fa7-4f28-9d98-6168b1759764
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 05/08/2018
ms.author: barclayn
ms.openlocfilehash: f9648e15c720c076a65e84a95f4160f27eec598d
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="what-is-azure-key-vault"></a>Azure Key Vault란?

Azure Key Vault는 보안 비밀 저장소로 작동하는 클라우드 서비스입니다.

암호, 연결 문자열 및 응용 프로그램을 작동시키는 데 필요한 기타 정보가 있습니다. 이러한 정보가 있는데 보안을 유지하려고 하는 경우 Azure Key Vault가 효과적일 수 있습니다. Azure Key Vault는 응용 프로그램 비밀을 안전하게 저장하고 관리하는 데 도움을 줍니다.

Key Vault를 사용하면 자격 증명 모음이라는 보안 컨테이너를 여러 개 만들 수 있습니다. 이러한 자격 증명 모음은 HSM(하드웨어 보안 모듈)에 의해 백업됩니다. 자격 증명 모음은 응용 프로그램 비밀을 중앙 집중식으로 저장하여 보안 정보의 우발적인 손실 가능성을 줄이는 데 도움이 됩니다. Key Vault는 또한 저장된 모든 것에 대한 액세스를 제어하고 기록합니다. Azure Key Vault는 TLS(전송 계층 보안) 인증서의 요청 및 갱신을 처리할 수 있으므로, 강력한 인증서 수명 주기 관리 솔루션에 필요한 기능을 제공합니다.

 Azure Key Vault는 응용 프로그램 키와 비밀을 지원하도록 설계되었습니다. Key Vault는 사용자 암호 저장소로 사용할 수 없습니다.

## <a name="why-use-azure-key-vault"></a>Azure Key Vault를 사용하는 이유는?

### <a name="centralize-application-secrets"></a>응용 프로그램 비밀 중앙 집중화

Azure Key Vault에 응용 프로그램 비밀을 중앙 집중식으로 저장하면 배포를 제어할 수 있습니다. 이렇게 하면 비밀이 우발적으로 누출될 가능성이 크게 감소됩니다. Key Vault를 사용하면 응용 프로그램 개발자가 더 이상 응용 프로그램에 보안 정보를 저장할 필요가 없습니다. 이렇게 하면 이 정보를 코드의 일부로 만들 필요가 없습니다. 예를 들어 응용 프로그램이 데이터베이스에 연결해야 할 수 있습니다. 연결 문자열을 앱 코드에 저장하는 대신 Key Vault에 안전하게 저장합니다.

Azure Key Vault에 응용 프로그램의 키 또는 비밀이 저장된 후 응용 프로그램에서 특정 버전의 비밀을 검색할 수 있는 URI를 사용하여 필요한 정보에 안전하게 액세스할 수 있습니다. 비밀 정보를 보호하기 위해 사용자 지정 코드를 작성할 필요가 없습니다.

### <a name="securely-store-secrets"></a>비밀을 안전하게 저장

Azure에서는 업계 표준 알고리즘, 키 길이 및 HSM(하드웨어 보안 모듈)을 사용하여 키를 보호합니다. 사용되는 HSM은 FIPS(Federal Information Processing Standards) 140-2 수준 2 유효성 검사를 통과했습니다.

Key Vault에 액세스하려면 호출자(사용자 또는 응용 프로그램)가 액세스할 수 있도록 적절한 인증 및 권한 부여가 필요합니다. 인증은 호출자의 ID를 확인하는 반면 권한 부여는 수행할 수 있는 작업을 결정합니다.

인증은 Azure Active Directory를 통해 수행됩니다. 권한 부여는 RBAC(역할 기반 액세스 제어) 또는 Key Vault 액세스 정책을 통해 수행할 수 있습니다. RBAC는 자격 증명 모음 관리를 처리할 때 사용되며, Key Vault 액세스 정책은 자격 증명 모음에 저장된 데이터에 액세스하려고 할 때 사용됩니다.

Azure Key Vault는 소프트웨어 또는 하드웨어 HSM으로 보호될 수 있습니다. 추가 보증이 필요한 경우, HSM 경계를 절대로 떠나지 않는 HSM(하드웨어 보안 모듈)에서 키를 가져오거나 생성할 수 있습니다. Microsoft는 Thales 하드웨어 보안 모듈을 사용합니다. Thales 도구를 사용하여 HSM에서 Azure Key Vault로 키를 이동할 수 있습니다.

마지막으로 Azure Key Vault는 Microsoft에서 키를 보거나 추출할 수 없게 설계되어 있습니다.

### <a name="monitor-access-and-use"></a>액세스 및 사용 모니터링

Key Vault를 두 개 만들었으면 키와 비밀이 액세스되는 방법 및 시기를 모니터링해야 합니다. Key Vault에 대한 로깅을 사용하면 이 작업을 수행할 수 있습니다. Azure Key Vault를 다음과 같이 구성할 수 있습니다.

- 저장소 계정에 보관합니다.
- 이벤트 허브로 스트리밍합니다.
- 로그를 Log Analytics로 보냅니다.

로그를 제어하고 로그에 대한 액세스를 제한하여 보호할 수 있으며 더 이상 필요하지 않은 로그를 삭제할 수도 있습니다.

### <a name="simplified-administration-of-application-secrets"></a>응용 프로그램 비밀의 관리 간소화

중요한 데이터를 저장할 때 몇 가지 단계를 수행해야 합니다. 보안 정보는 보호되어야 하며, 수명 주기를 따라야 하며, 가용성이 높아야 합니다. Azure Key Vault는 다음을 통해 기능을 많이 간소화합니다.

- HSM의 사내 지식에 대한 필요성을 제거합니다.
- 조직의 사용량 급증에 맞춰 단기간에 확장합니다.
- 한 지역 전체와 보조 지역으로 Key Vault의 콘텐츠를 복제합니다. 이렇게 하면 고가용성이 보장되고 관리자가 장애 조치(failover)를 트리거할 수 있는 모든 작업이 필요하지 않습니다.
- Azure CLI 및 PowerShell을 통해 표준 Azure 관리 옵션을 제공합니다.
- 공용 CA에서 구입한 인증서에 대한 등록 및 갱신과 같은 특정 작업을 자동화합니다.

또한 Azure Key Vault를 통해 응용 프로그램 비밀을 격리할 수 있습니다. 응용 프로그램은 액세스가 허용되는 자격 증명 모음에만 액세스할 수 있으며 특정 작업만 수행하도록 제한됩니다. 응용 프로그램당 Azure Key Vault를 만들고 Key Vault에 저장된 비밀을 특정 응용 프로그램 및 개발자 팀으로 제한할 수 있습니다.

### <a name="integrate-with-other-azure-services"></a>다른 Azure 서비스와 통합

Azure의 보안 저장소인 Key Vault는 [Azure Disk Encryption](../security/azure-security-disk-encryption.md), SQL 서버 및 Azure SQL의 [항상 암호화]( https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) 기능, [Azure 웹앱]( https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site)와 같은 시나리오를 단순화하는 데 사용되었습니다. Key Vault 자체는 저장소 계정, 이벤트 허브 및 로그 분석과 통합할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [빠른 시작: CLI를 사용하여 Azure Key Vault 만들기](quick-create-cli.md)
- [Key Vault에서 비밀을 읽도록 Azure 웹 응용 프로그램 구성](tutorial-web-application-keyvault.md)