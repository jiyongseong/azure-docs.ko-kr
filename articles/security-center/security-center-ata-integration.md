---
title: Azure Security Center에 Microsoft Advanced Threat Analytics 연결 | Microsoft Docs
description: Azure Security Center를 Microsoft Advanced Threat Analytics와 통합하는 방법을 알아봅니다.
services: security-center
documentationcenter: na
author: terrylan
manager: MBaldwin
editor: ''
ms.assetid: 5d80bf91-16c3-40b3-82fc-e0805e6708db
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: yurid
ms.openlocfilehash: a3444b9d42ffdd5f81568f0e9e09557096b4415f
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="connecting-microsoft-advanced-threat-analytics-to-azure-security-center"></a>Azure Security Center에 Microsoft Advanced Threat Analytics 연결
이 문서는 Microsoft Advanced Threat Analytics와 Azure Security Center 간에 통합을 구성하는 데 도움이 됩니다.

## <a name="why-add-advanced-threat-analytics-data"></a>Advanced Threat Analytics 데이터를 추가하는 이유는 무엇인가요?
[ATA(Advanced Threat Analytics)](https://docs.microsoft.com/advanced-threat-analytics/what-is-ata)는 의심스러운 사용자 동작을 검색하는 데 도움이 되는 온-프레미스 플랫폼입니다. 연결되면 ATA에서 검색된 의심스러운 동작을 Security Center에서 볼 수 있습니다. 이러한 통합을 사용하여 Security Center의 하이브리드 클라우드 워크로드와 관련된 모든 보안 경고를 보고 상관 관계를 파악하고 조사할 수 있습니다. 

## <a name="how-do-i-configure-this-integration"></a>이 통합을 구성하려면 어떻게 해야 하나요?
ATA을 이미 설치했으며 온-프레미스에서 제대로 작동된다고 가정하고 다음 단계를 수행하여 이 통합을 구성합니다.

1. ATA 센터에 로그인하고 ATA 포털에 액세스합니다.
2. 왼쪽 창에서 **Syslog 서버**를 클릭합니다.

    ![Syslog 서버](./media/security-center-ata-integration/security-center-ata-integration-fig1.png)

3. **Syslog 서버 끝점** 필드에 127.0.0.7(이 주소여야 함)을 입력하고 포트에 5114(권장)를 입력합니다. 포트 번호는 권장 사항이지만 고유한 모든 포트가 작동해야 합니다. 다른 모든 옵션은 현재 상태로 두고 **저장**을 클릭합니다.
4. 왼쪽 창에서 **알림**을 클릭하고 다음 이미지와 같이 모든 Syslog 알림을 사용하도록 설정합니다(권장).

    ![공지](./media/security-center-ata-integration/security-center-ata-integration-fig2.png)

5. **저장**을 클릭합니다.
6. **Security Center** 대시보드를 엽니다.
7. 왼쪽 창에서 **보안 솔루션**을 클릭합니다.
8. **Advanced Threat Analytics**에서 **ADD**를 클릭합니다.

    ![ATA](./media/security-center-ata-integration/security-center-ata-integration-fig3.png)
    
9. 마지막 단계로 이동한 후 **에이전트 다운로드**를 클릭합니다.

    ![ATA](./media/security-center-ata-integration/security-center-ata-integration-fig4.png)

10. **비 Azure 컴퓨터 새로 추가** 페이지에서 작업 영역을 선택합니다.

    ![비 Azure](./media/security-center-ata-integration/security-center-ata-integration-fig5.png)

11. **직접 에이전트** 페이지에서 해당 Windows 에이전트를 다운로드하고 **작업 영역 ID** 및 **기본 키**를 적어둡니다.

    ![직접 에이전트](./media/security-center-ata-integration/security-center-ata-integration-fig6.png)

12. ATA 센터에 이 에이전트를 설치합니다. 설치하는 동안 **Azure Log Analytics에 에이전트 연결** 옵션을 선택하고 요청 시 *작업 영역 ID* 및 *기본 키*를 제공합니다.


설치를 완료하면 통합이 완료되며 ATA에서 Security Center로 전송된 새 경고를 **검색** 결과에서 볼 수 있습니다. 해당 솔루션은 **보안 솔루션** 페이지의 **연결된 솔루션**에 표시됩니다. 

## <a name="next-steps"></a>다음 단계
이 문서에서는 Microsoft ATA를 Security Center에 연결하는 방법을 살펴보았습니다. Security Center에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Security Center에 Azure Active Directory Identity Protection 연결](security-center-aadip-integration.md)
* [Azure Security Center에서 보안 정책 설정](security-center-policies.md) - Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 권장 사항 관리](security-center-recommendations.md) - 권장 사항이 Azure 리소스 보호에 어떤 도움이 되는지를 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md) — Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure Security Center를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md) - 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
- [Azure Security Center 데이터 보안](security-center-data-security.md) - Security Center에서 데이터를 관리하고 보호하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/) - 최신 Azure 보안 뉴스 및 정보를 가져옵니다.


