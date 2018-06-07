---
title: "Azure Security Center에서 연결된 파트너 솔루션 관리 | Microsoft Docs"
description: "이 문서에서는 Azure 보안 센터에서, Azure 구독에 통합된 파트너 솔ㄹ션의 보안 상태를 한 눈에 모니터링하는 방법을 살펴봅니다."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2017
ms.author: terrylan
ms.openlocfilehash: 181e1e00716987732ee809df6171c2f71087f3e1
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2017
---
# <a name="managing-connected-partner-solutions-with-azure-security-center"></a>Azure Security Center에서 연결된 파트너 솔루션 관리
이 문서에서는 Azure Security Center에서 연결된 보안 솔루션을 관리 및 모니터링하는 방법을 안내합니다.

## <a name="monitoring-partner-solutions"></a>파트너 솔루션 모니터링
연결된 보안 솔루션의 상태를 모니터링하고 기본 관리를 수행하려면

1. **Security Center - 개요**에서 **보안 솔루션**을 선택합니다.

  ![보안 솔루션 선택][1]

  **연결된 솔루션** 섹션은 Security Center에 연결된 보안 솔루션 및 각 솔루션의 상태에 대한 정보를 포함합니다.

  ![파트너 솔루션][2]

   파트너 솔루션의 상태는 다음과 같을 수 있습니다.

   * 정상(녹색) - 상태 문제가 없습니다.
   * 비정상(빨강) - 즉각적인 주의가 필요한 상태 문제가 있습니다.
   * 상태 문제(주황색) - 솔루션이 상태의 보고를 중지했습니다.
   * 보고되지 않음(회색) - 솔루션이 아무 것도 보고하지 않았습니다. 솔루션이 최근에 연결되었고 여전히 배포 중이거나 상태 데이터를 사용할 수 없는 경우 솔루션의 상태를 보고하지 않을 수 있습니다.

   > [!NOTE]
   > 상태 데이터를 사용할 수 없는 경우 Security Center에 마지막으로 수신된 이벤트의 날짜와 시간을 통해 솔루션이 보고 중인지 여부가 표시됩니다. 상태 데이터를 사용할 수 있으며 지난 14일 이내에 경고가 수신되지 않은 경우 Security Center는 솔루션이 비정상 상태이거나 보고하지 않음을 나타냅니다.
   >
   >

2. 다음을 비롯한 추가 정보 및 옵션에 대해서는 **보기**를 참조하세요.

  - **솔루션 콘솔**. 이 솔루션에 대한 관리 환경을 엽니다.
  - **VM 연결**. 응용 프로그램 연결 블레이드를 엽니다. 여기서 리소스를 파트너 솔루션에 연결할 수 있습니다.
  - **솔루션 삭제**
  - **구성**

   ![파트너 솔루션 세부 정보][3]

## <a name="next-steps"></a>다음 단계
이 문서에서는 Security Center에서 연결된 보안 솔루션을 관리 및 모니터링하는 방법을 알아보았습니다. 보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [보안 솔루션 개요](security-center-partner-integration.md) - 보안 솔루션을 연결 및 관리하는 방법을 알아봅니다.
* [Microsoft ATA(Advanced Threat Analytics) 연결](security-center-ata-integration.md) - ATA에서 경고에 연결하는 방법을 알아봅니다.
* [Azure AD(Active Directory) Identity Protection 연결 ](security-center-aadip-integration.md) - Azure AD Identity Protection에서 경고에 연결하는 방법을 알아봅니다.
* [파트너 및 솔루션 통합](security-center-partner-integration.md) - 다른 보안 솔루션 통합을 대략적으로 설명합니다.
* [보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/) - Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/partner-solutions.png
[3]: ./media/security-center-partner-solutions/partner-solutions-detail.png
