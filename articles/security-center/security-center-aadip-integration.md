---
title: Azure Security Center에 Azure Active Directory Identity Protection 연결 | Microsoft Docs
description: Azure Security Center를 Azure Active Directory Identity Protection과 통합하는 방법을 알아봅니다.
services: security-center
documentationcenter: na
author: terrylan
manager: MBaldwin
editor: ''
ms.assetid: 0d4b77c2-dba4-4e46-8f55-ab04ddd92496
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: b15508acea92c165cba85e39ab907fa524088164
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="connecting-azure-active-directory-identity-protection-to-azure-security-center"></a>Azure Security Center에 Azure Active Directory Identity Protection 연결
이 문서는 Azure AD(Active Directory) Identity Protection과 Azure Security Center 간에 통합을 구성하는 데 도움이 됩니다.

## <a name="why-connect-azure-ad-identity-protection"></a>Azure AD Identity Protection을 연결하는 이유는 무엇인가요?
[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)는 조직의 ID에 영향을 미치는 잠재적인 취약점을 검색하는 데 도움이 됩니다. 연결되면 Security Center에서 Azure AD Identity Protection 경고를 볼 수 있습니다. 이러한 통합을 사용하여 Security Center의 하이브리드 클라우드 워크로드와 관련된 모든 보안 경고를 보고 상관 관계를 파악하고 조사할 수 있습니다. 

## <a name="how-do-i-configure-this-integration"></a>이 통합을 구성하려면 어떻게 해야 하나요?
조직에서 Azure AD Identity Protection을 이미 사용하는 경우 아래의 단계에 따라 통합을 구성합니다.

1. **Security Center** 대시보드를 엽니다.
2. 왼쪽 창에서 **보안 솔루션**을 클릭합니다. Security Center는 조직에 Azure AD Identity Protection이 사용되도록 설정되어 있는지를 자동으로 검색합니다.

    ![AADIP](./media/security-center-aadip-integration/security-center-aadip-integration-fig1.png)

3. **연결**을 클릭합니다.
4. **Azure AD Identity Protection 통합** 페이지에서 아래로 스크롤한 후 해당 작업 영역을 선택합니다.

    ![작업 영역](./media/security-center-aadip-integration/security-center-aadip-integration-fig2.png)

5. **Connect**를 클릭합니다.

이 구성을 완료되면 **보안 솔루션** 페이지의 **연결된 솔루션** 아래에 Azure AD Identity Protection 솔루션이 표시됩니다. 

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure AD Identity Protection을 Security Center에 연결하는 방법을 알아보았습니다. Security Center에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Security Center에 Microsoft Advanced Threat Analytics 연결](security-center-ata-integration.md)
* [Azure Security Center에서 보안 정책 설정](security-center-policies.md) - Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 권장 사항 관리](security-center-recommendations.md) - 권장 사항이 Azure 리소스 보호에 어떤 도움이 되는지를 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md) — Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure Security Center를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md) - 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
- [Azure Security Center 데이터 보안](security-center-data-security.md) - Security Center에서 데이터를 관리하고 보호하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/) - 최신 Azure 보안 뉴스 및 정보를 가져옵니다.


