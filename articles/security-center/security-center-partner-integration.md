---
title: Azure Security Center에서 보안 솔루션 통합 | Microsoft Docs
description: Azure Security Center를 파트너와 통합하여 Azure 리소스의 전반적인 보안을 강화하는 방법에 대해 알아봅니다.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/16/2018
ms.author: terrylan
ms.openlocfilehash: 28bb900a3281becc7bb243e6bd7bf2beedf1a5c1
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34261634"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Azure Security Center에서 보안 솔루션 통합
이 문서를 통해 이미 Azure Security Center에 연결된 보안 솔루션을 관리하고 새로 추가할 수 있습니다.

## <a name="integrated-azure-security-solutions"></a>통합된 Azure 보안 솔루션
Security Center를 사용하면 Azure에서 통합된 보안 솔루션을 쉽게 사용할 수 있습니다. 이점은 다음과 같습니다.

- **배포 간소화**: Security Center에서는 통합된 파트너 솔루션의 간결한 프로비전을 제공합니다. 맬웨어 방지 및 취약성 평가와 같은 솔루션의 경우 Security Center는 가상 머신에 필요한 에이전트를 프로비전할 수 있습니다. 또한 방화벽 어플라이언스의 경우 Security Center는 필요한 네트워크 구성을 충분히 고려할 수 있습니다.
- **통합된 감지**: 파트너 솔루션의 보안 이벤트는 자동으로 수집, 집계되며 Security Center 알림 및 사고의 일부로 표시됩니다. 또한 이러한 이벤트는 다른 원본의 감지를 결합하여 고급 위협 감지 기능을 제공합니다.
- **통합된 상태 모니터링 및 관리**: 고객은 통합된 상태 이벤트를 사용하여 한 눈에 모든 파트너 솔루션을 모니터링할 수 있습니다. 기본 관리는 파트너 솔루션을 사용하여 고급 설정에 쉽게 액세스하여 사용할 수 있습니다.

현재 통합 보안 솔루션에는 다음이 포함됩니다.

- 엔드포인트 보호([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), [Symantec](https://www.symantec.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://www.microsoft.com/search/result.aspx?q=Windows+defender+endpoint+protection) 및 [System Center Endpoint Protection](https://www.microsoft.com/search/result.aspx?q=System+Center+endpoint+protection))
- 웹 응용 프로그램 방화벽([Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/products.html) 및 [Azure Application Gateway](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/))
- 차세대 방화벽([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2), [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html) 및 [Palo Alto Networks](https://www.paloaltonetworks.com/products))
- 취약성 평가([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/))  

끝점 보호 통합 환경은 솔루션에 따라 다를 수 있습니다. 다음 표에는 각 솔루션의 환경에 대한 자세한 정보가 나와 있습니다.

| 끝점 보호               | 플랫폼                             | Security Center 설치 | Security Center 검색 |
|-----------------------------------|---------------------------------------|------------------------------|---------------------------|
| Windows Defender(Microsoft 맬웨어 방지 프로그램)                  | Windows Server 2016                   | 아니오, OS에 기본 제공           | 예                       |
| System Center Endpoint Protection(Microsoft 맬웨어 방지 프로그램) | Windows Server 2012 R2, 2012, 2008 R2 | 확장을 통해                | 예                       |
| Trend Micro - 모든 버전         | Windows Server 제품군                 | 확장을 통해                | 예                       |
| Symantec v12.1.1100+              | Windows Server 제품군                 | 아니오                           | 예                       |
| McAfee v10+                       | Windows Server 제품군                 | 아니오                           | 예                       |
| Kaspersky                         | Windows Server 제품군                 | 아니오                           | 아니오                        |
| Sophos                            | Windows Server 제품군                 | 아니오                           | 아니오                        |



## <a name="how-security-solutions-are-integrated"></a>보안 솔루션을 통합하는 방법
Security Center에서 배포된 Azure 보안 솔루션은 자동으로 연결됩니다. 또한 다음을 비롯한 다른 보안 데이터 원본에 연결할 수 있습니다.

- Azure AD ID 보호
- 온-프레미스 또는 기타 클라우드에서 실행되는 컴퓨터
- CEF(공통 이벤트 형식)을 지원하는 보안 솔루션
- Microsoft Advanced Threat Analytics

![파트너 솔루션 통합](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>통합된 Azure 보안 솔루션 및 다른 데이터 원본 관리

1. [Azure Portal](https://azure.microsoft.com/features/azure-portal/)에 로그인합니다.

2. **Microsoft Azure 메뉴**에서 **Security Center**를 선택합니다. **Security Center - 개요**가 열립니다.

  ![Security Center 개요](./media/security-center-partner-integration/overview.png)

3. **개요**에서 **보안 솔루션**을 선택합니다.

**보안 솔루션**에서 통합된 Azure 보안 솔루션의 상태에 대한 정보를 보고 기본 관리 작업을 수행할 수 있습니다. 또한 CEF(공통 이벤트 형식)에서 Azure Active Directory Identity Protection 알림 및 방화벽 로그와 같은 다른 형식의 보안 데이터 원본을 연결할 수 있습니다.

### <a name="connected-solutions"></a>연결된 솔루션

**연결된 솔루션** 섹션은 Security Center에 현재 연결된 보안 솔루션 및 각 솔루션의 상태에 대한 정보를 포함합니다.  

![연결된 솔루션](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

자세히 알아보려면 [연결된 파트너 솔루션 관리](security-center-partner-solutions.md)를 참조하세요.

### <a name="discovered-solutions"></a>검색된 솔루션

Security Center는 Azure에서 실행되는 보안 솔루션을 자동으로 검색하지만 Security Center에 연결되지 않고 **검색된 솔루션** 섹션에 솔루션을 표시합니다. 여기에는 [Azure AD ID 보호](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)와 같은 Azure 솔루션뿐만 아니라 파트너 솔루션도 포함됩니다.

> [!NOTE]
> Security Center의 표준 계층은 검색된 솔루션 기능의 구독 수준에서 필요합니다. Security Center의 가격 책정 계층에 대한 자세한 내용은 [가격 책정](security-center-pricing.md)을 참조하세요.
>
>

솔루션 아래의 **연결**을 선택하여 Security Center와 통합하고 보안 경고에서 알림을 받습니다.

![검색된 솔루션](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

Security Center는 CEF(일반 이벤트 형식) 로그를 전달할 수 있는 구독에 배포되는 솔루션도 검색합니다. CEF 로그를 사용하여 Security Center에 [보안 솔루션을 연결](quick-security-solutions.md)하는 방법을 알아봅니다.

### <a name="add-data-sources"></a>데이터 원본 추가

**데이터 원본 추가** 섹션에는 연결할 수 있는 다른 사용 가능한 데이터 원본이 포함됩니다. 이러한 원본의 데이터를 추가하는 방법에 대한 지침은 **추가**를 클릭합니다.

![데이터 원본](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)


## <a name="next-steps"></a>다음 단계

이 문서에서는 Security Center에서 파트너 솔루션을 통합하는 방법을 살펴보았습니다. Security Center에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Security Center에 Microsoft Advanced Threat Analytics 연결](security-center-ata-integration.md)
* [Azure Security Center에 Azure Active Directory Identity Protection 연결](security-center-aadip-integration.md)
* [Security Center에서 보안 상태 모니터링](security-center-monitoring.md) Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Security Center를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md) 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md) Security Center 사용에 관한 질문과 대답에 대한 답을 가져옵니다.
* [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/) Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.
