---
title: Azure Security Center에 Microsoft Azure Application Gateway 연결 | Microsoft Docs
description: Application Gateway와 Azure Security Center를 통합하여 리소스의 전반적인 보안을 강화하는 방법을 알아봅니다.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/07/2018
ms.author: terrylan
ms.openlocfilehash: 7c15e5a86df7ff2a374aa9b62d2775b1eb035fc6
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2018
---
# <a name="connecting-microsoft-azure-application-gateway-to-azure-security-center"></a>Azure Security Center에 Microsoft Azure Application Gateway 연결
이 문서는 Application Gateway WAF(웹 응용 프로그램 방화벽)와 Security Center의 통합을 구성하는 데 도움을 줍니다.

## <a name="why-connect-application-gateway"></a>Application Gateway를 연결하는 이유
Application Gateway의 WAF는 SQL 삽입 공격, 사이트 간 스크립팅 공격, 세션 하이재킹 등의 일반적인 웹 기반 공격으로부터 웹 응용 프로그램을 보호합니다. Security Center는 Application Gateway와 통합되어 환경 내에서 보호되지 않는 웹 응용 프로그램에 대한 위험을 방지 및 감지합니다.

## <a name="how-do-i-configure-this-integration"></a>이 통합을 구성하려면 어떻게 해야 하나요?
Security Center는 구독에서 이전에 배포된 WAF 인스턴스를 검색합니다. 경고를 통합하려면 이러한 솔루션을 Security Center에 연결합니다.

> [!NOTE]
> [웹 응용 프로그램 방화벽 추가](security-center-add-web-application-firewall.md)에 설명된 대로 Security Center의 **권장 사항**에서 Application Gateway WAF를 프로비전할 수도 있습니다.
>
>

1. [Azure 포털](https://azure.microsoft.com/features/azure-portal/)에 로그인합니다.

2. **Microsoft Azure 메뉴**에서 **Security Center**를 선택합니다. **Security Center - 개요**가 열립니다.

3. **개요**에서 **보안 솔루션**을 선택합니다.

  ![Security Center 개요](./media/security-center-connect-application-gateway/overview.png)

4. **검색된 솔루션** 아래에서 Microsoft SaaS 기반 웹 응용 프로그램 방화벽을 찾은 후 **연결**을 선택합니다.

  ![검색된 솔루션](./media/security-center-connect-application-gateway/connect.png)

5. **WAF 솔루션 연결**이 열립니다.  **연결**을 선택하여 WAF와 Security Center를 통합합니다.

  ![WAF 솔루션 연결](./media/security-center-connect-application-gateway/waf-solution.png)

## <a name="next-steps"></a>다음 단계

이 문서에서는 Security Center에서 Application Gateway WAF를 통합하는 방법을 살펴보았습니다. Security Center에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Security Center에서 보안 솔루션 통합](security-center-partner-integration.md)
* [Security Center에 Microsoft Advanced Threat Analytics 연결](security-center-ata-integration.md)
* [Security Center에 Azure Active Directory Identity Protection 연결](security-center-aadip-integration.md)
* [Security Center에서 보안 상태 모니터링](security-center-monitoring.md)
* [Security Center를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md)
* [Azure Security Center FAQ](security-center-faq.md)
* [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/)
