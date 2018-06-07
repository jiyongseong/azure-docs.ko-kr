---
title: "Azure Security Center에서 보안 구성 수정 | Microsoft Docs"
description: "이 문서에서는 Azure Security Center 권장 사항 \"보안 구성 수정\"을 구현하는 방법을 보여 줍니다."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 991d41f5-1d17-468d-a66d-83ec1308ab79
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2018
ms.author: terrylan
ms.openlocfilehash: 477973298d8cc9d99da78e36274933e0bb737c4f
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/25/2018
---
# <a name="remediate-security-configurations-in-azure-security-center"></a>Azure Security Center에서 보안 구성 수정
Azure Security Center에서는 VM(Virtual Machines) 및 컴퓨터의 OS(운영 체제)를 매일 분석하여 이러한 VM과 컴퓨터를 공격에 보다 취약하게 만들 수 있는 구성을 확인합니다. Security Center에서는 OS 구성이 권장 보안 구성 규칙과 일치하지 않을 경우 취약성을 해결하도록 권장하며, 이러한 취약성 해결을 위해 구성을 변경하는 것이 좋습니다.

모니터링되는 특정 구성에 대한 자세한 내용은 [권장되는 구성 규칙 목록](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)을 참조하세요. 보안 구성 평가를 사용자 지정하는 방법을 알아보려면 [Azure Security Center에서 OS 보안 구성 사용자 지정(미리 보기)](security-center-customize-os-security-config.md)을 참조하세요.

## <a name="implement-the-recommendation"></a>권장 사항 구현
"보안 구성 수정"은 Security Center에서 권장 사항으로 표시됩니다. 권장 사항은 **권장 사항** > **Compute** 아래 표시됩니다.

이 예제에서는 **Compute** 아래의 "보안 구성 수정" 권장 사항을 살펴보겠습니다.
1. Security Center의 왼쪽 창에서 **Compute**를 선택합니다.  
  **Compute** 창이 열립니다.

   ![보안 구성 수정][1]

2. **보안 구성 수정**을 선택합니다.  
  **보안 구성** 창이 열립니다.

   !["보안 구성" 창][2]

  대시보드의 위쪽 섹션에는 다음 항목이 표시됩니다.

  - **심각도별 실패한 규칙**: 전체 VM과 컴퓨터에서 OS 구성이 실패한 총 규칙 수이며, 심각도별로 구분됩니다.
  - **유형별 실패한 규칙**: 전체 VM과 컴퓨터에서 OS 구성이 실패한 총 규칙 수이며, 유형별로 구분됩니다.
  - **Windows 규칙 실패**: Windows OS 구성에서 실패한 총 규칙 수입니다.
  - **Linux 규칙 실패**: Linux OS 구성에서 실패한 총 규칙 수입니다.

  대시보드 아래쪽 섹션에는 VM과 컴퓨터 전체에서 실패한 모든 규칙과 누락된 업데이트의 심각도가 나열됩니다. 이 목록에는 다음 요소가 포함됩니다.

  - **CCEID**: 규칙에 대한 CCE 고유 식별자입니다. Security Center는 CCE(일반 구성 열거형)를 사용하여 구성 규칙에 고유 ID를 할당합니다.
  - **이름**: 실패한 규칙의 이름입니다.
  - **규칙 유형**: *레지스트리 키*, *보안 정책* 또는 *감사 정책* 규칙 유형입니다.
  - **VM 및 컴퓨터 수**: 실패한 규칙이 적용되는 VM 및 컴퓨터의 총 수입니다.
  - **규칙 심각도**: CCE 값으로 *위험*, *중요* 또는 *경고*입니다.
  - **상태**: 권장 사항의 현재 상태입니다.

    - **열기**: 아직 해결되지 않은 권장 사항이 있습니다.
    - **진행 중**: 권장 사항이 현재 해당 리소스에 적용되고 있으며 사용자가 아무런 작업도 수행할 필요가 없습니다.
    - **해결됨**: 권장 사항이 적용되었습니다. 문제가 해결되면 해당 항목이 회색으로 표시됩니다.

3. 실패한 규칙의 세부 정보를 보려면 목록에서 해당 규칙을 선택합니다.

   ![실패한 구성 규칙의 자세한 보기][3]

   자세한 보기에는 다음 정보가 표시됩니다.

   - **이름**: 규칙의 이름입니다.
   - **CCIED**: 규칙에 대한 CCE 고유 식별자입니다.
   - **OS 버전**: VM 또는 컴퓨터의 OS 버전입니다.
   - **규칙 심각도**: CCE 값으로 *위험*, *중요* 또는 *경고*입니다.
   - **전체 설명**: 규칙에 대한 설명입니다.
   - **취약성**: 규칙이 적용되지 않은 경우 취약성 또는 위험에 대한 설명입니다.
   - **잠재적 영향**: 규칙이 적용되는 경우 비즈니스 영향입니다.
   - **대책**: 수정 단계입니다.
   - **예상 값**: Security Center에서 규칙을 기준으로 VM OS 구성을 분석할 때 예상되는 값입니다.
   - **실제 값**: 규칙을 기준으로 VM OS 구성을 분석한 후 반환되는 값입니다.
   - **규칙 작업**: 규칙을 기준으로 VM OS 구성을 분석하는 동안 Security Center가 사용하는 규칙 작업입니다.

4. 자세히 보기 창 맨 위에서 **검색**을 선택합니다.  
  검색에 선택한 보안 구성이 일치하지 않는 VM 및 컴퓨터가 포함된 작업 영역 목록이 열립니다. 선택한 규칙이 여러 작업 영역에 연결된 여러 VM에 적용되는 경우에만 이 작업 영역 선택이 표시됩니다.

   ![나열된 작업 영역][4]

5. 작업 영역을 선택합니다.  
  보안 구성 불일치가 있는 작업 영역이 표시되도록 필터링된 Log Analytics 검색 쿼리가 열립니다.

   ![OS 취약성이 있는 작업 영역][5]

6. 목록에서 컴퓨터를 선택합니다.  
  해당 컴퓨터와 관련된 정보만 표시되도록 필터링된 새로운 검색 결과가 열립니다.

   ![선택한 컴퓨터에 대한 자세한 정보][6]

## <a name="next-steps"></a>다음 단계
이 문서에서는 Security Center 권장 사항 “보안 구성 수정”을 구현하는 방법을 살펴보았습니다. 보안 구성 평가를 사용자 지정하는 방법을 알아보려면 [Azure Security Center에서 OS 보안 구성 사용자 지정(미리 보기)](security-center-customize-os-security-config.md)을 참조하세요.

모니터링 중인 구체적인 구성을 검토하려면 [권장되는 구성 규칙 목록](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)을 참조하세요. Security Center는 CCE(일반 구성 열거형)를 사용하여 구성 규칙에 고유 ID를 할당합니다. 자세한 내용은 [CCE](https://nvd.nist.gov/cce/index.cfm) 사이트를 참조하세요.

Security Center에 대해 알아보려면 다음 리소스를 참조하세요.

* 지원되는 Windows 및 Linux VM의 목록은 [Azure Security Center에서 지원되는 플랫폼](security-center-os-coverage.md)을 참조하세요. 
* Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아보려면 [Azure Security Center에서 보안 정책 설정](security-center-policies.md)을 참조하세요. 
* 권장 사항이 Azure 리소스 보호에 어떤 도움이 되는지를 알아보려면 [Azure Security Center에서 보안 권장 사항 관리](security-center-recommendations.md)를 참조하세요. 
* Azure 리소스의 상태를 모니터링하는 방법을 알아보려면 [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md)을 참조하세요. 
* 보안 경고를 관리하고 대응하는 방법을 알아보려면 [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md)을 참조하세요.
* 파트너 솔루션의 상태를 모니터링하는 방법을 알아보려면 [Azure Security Center를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md)을 참조하세요.
* 서비스 사용에 관한 질문과 대답은 [Azure Security Center FAQ](security-center-faq.md)를 참조하세요.
* Azure 보안 및 규정 준수에 관한 블로그 게시물은 [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/)를 참조하세요.

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/compute-blade.png
[2]:./media/security-center-remediate-os-vulnerabilities/os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
[4]: ./media/security-center-remediate-os-vulnerabilities/search.png
[5]: ./media/security-center-remediate-os-vulnerabilities/log-search.png
[6]: ./media/security-center-remediate-os-vulnerabilities/search-results.png
