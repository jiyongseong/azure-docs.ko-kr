---
title: "Azure Security Center 권장 사항을 사용하여 보안 강화 | Microsoft Docs"
description: " Azure Security Center에서 보안 정책 및 권장 사항을 사용하여 보안 공격을 완화할 수 있는 방법을 알아봅니다. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: terrylan
ms.openlocfilehash: 0616f5e501324bfd821c1455ce234602f1fcf1bd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Azure Security Center 권장 사항을 사용하여 보안 강화
보안 정책을 구성한 다음 Azure Security Center에서 제공한 권장 사항을 구현하여 중요한 보안 이벤트의 가능성을 줄일 수 있습니다. 이 문서에서는 Security Center에서 보안 정책 및 권장 사항을 사용하여 보안 공격을 완화할 수 있는 방법을 보여 줍니다.

> [!NOTE]
> 이 문서는 Security Center [계획 및 운영 가이드](security-center-planning-and-operations-guide.md)에서 소개된 역할 및 개념을 기반으로 합니다. 계속하기 전에 계획 가이드를 검토하는 것이 좋습니다.
>
>

## <a name="managing-security-recommendations"></a>보안 권장 사항 관리
보안 정책은 지정된 구독 또는 리소스 그룹 내에서 리소스에 대해 권장되는 제어 집합을 정의합니다. Security Center에서 회사의 보안 요구 사항에 따라 정책을 정의합니다. 자세한 내용은 [Security Center에서 보안 정책 설정](security-center-policies.md)을 참조하세요.

리소스 그룹에 대한 보안 정책은 구독 수준에서 상속됩니다.

![보안 정책 상속][1]

특정 리소스 그룹에서 사용자 지정 정책이 필요한 경우 리소스 그룹에서 상속을 사용하지 않도록 설정할 수 있습니다. 상속을 사용하지 않으려면 고유한 보안 정책 블레이드로 설정하고 Security Center에서 권장 사항을 표시하는 컨트롤을 사용자 지정합니다.

예를 들어 SQL Database TDE(투명한 데이터 암호화) 정책이 필요하지 않은 워크로드의 경우, 구독 수준에서 정책을 해제하고 SQL TDE가 필요한 리소스 그룹에서만 사용하도록 설정합니다.

> [!NOTE]
> 구독 수준 정책과 리소스 그룹 수준 정책 간에 충돌이 발생한 경우 리소스 그룹 수준 정책이 우선 적용됩니다.
>
>

보안 센터에서는 Azure 리소스의 보안 상태를 분석합니다. Security Center가 잠재적인 보안 취약점을 식별하는 경우 보안 정책에서 설정된 컨트롤에 따라 권장 사항을 만듭니다. 권장 사항은 필요한 보안 컨트롤을 구성하는 과정을 안내합니다.

현재 Security Center의 정책 권장 사항은 시스템 업데이트, OS 구성, 네트워크 서브넷 및 VM(가상 컴퓨터)의 네트워크 보안 그룹, SQL Database 감사, SQL Database TDE 및 웹 응용 프로그램 방화벽에 집중되어 있습니다. Security Center 권장 사항의 최신 적용 범위는 [Security Center에서 보안 권장 사항 관리](security-center-recommendations.md)를 참조하세요.

## <a name="scenario"></a>시나리오
이 시나리오에서는 Security Center 권장 사항을 모니터링하고 조치를 취하여 중요한 보안 인시던트가 발생할 가능성을 줄일 수 있도록 Security Center를 사용하는 방법을 보여 줍니다. 시나리오는 Security Center [계획 및 운영 가이드](security-center-planning-and-operations-guide.md#security-roles-and-access-controls)에서 제공된 가상의 회사, Contoso 및 역할을 사용합니다. 역할은 Security Center를 사용하여 다른 보안 관련 작업을 수행할 수 있는 개인 및 팀을 나타냅니다. 역할은 다음과 같습니다.

![시나리오 역할][2]

Contoso는 최근 해당 온-프레미스 리소스의 일부를 Azure로 마이그레이션했습니다. Contoso는 클라우드에서 해당 리소스의 보안 공격에 대한 해당 취약점을 줄이는 보호 방법을 구현 및 유지 관리하려고 합니다.

## <a name="recommended-solution"></a>권장된 솔루션
솔루션은 Security Center를 사용하여 보안 취약점을 방지하고 감지하는 것입니다. Contoso는 Azure 구독을 통해 Security Center에 액세스할 수 있습니다. Security Center의 [무료 계층](security-center-pricing.md)은 모든 Azure 구독에서 자동으로 활성화되고 해당 구독의 모든 VM에서 데이터 수집을 활성화합니다.

Contoso의 IT 보안에서 David는 Security Center를 사용하여 **보안 정책**을 구성합니다. Security Center에서는 Contoso의 Azure 리소스 보안 상태를 분석합니다. Security Center가 잠재적인 보안 취약점을 식별하는 경우 보안 정책에서 설정된 컨트롤에 따라 **권장 사항**을 만듭니다.

클라우드 워크로드 소유자인 Jeff는 Contoso의 보안 정책에 따라 보호 방법을 구현하고 유지 관리하는 업무를 담당합니다. Jeff는 Security Center에서 만든 권장 사항을 모니터링하여 보호를 적용할 수 있습니다. 권장 사항은 Jeff가 필요한 보안 컨트롤을 구성하는 과정을 안내합니다.

Jeff가 보호를 구현 및 유지하고 보안 취약점을 제거하기 위해 다음을 수행해야 합니다.

- Security Center에서 제공한 보안 권장 사항 모니터링
- 보안 권장 사항을 평가하고 적용하거나 해제해야 할지를 결정합니다.
- 보안 권장 사항 적용

Jeff가 Security Center 권장 사항을 사용하여 보안 취약점을 제거하는 컨트롤을 구성하는 과정을 보기 위해 Jeff가 수행한 단계를 따라가겠습니다.

## <a name="how-to-implement-this-solution"></a>이 솔루션을 구현하는 방법
Jeff는 [Azure Portal](https://azure.microsoft.com/features/azure-portal/)에 로그인하고 Security Center 콘솔을 엽니다. 일상적인 모니터링 작업의 일환으로 다음 단계를 수행하여 보안 권장 사항이 있는지를 확인합니다.

1. Jeff는 **권장 사항** 타일을 선택하여 **권장 사항**을 엽니다.
   ![권장 사항 타일 선택][3]
2. Jeff는 권장 사항 목록을 검토합니다. 또한 Security Center에서 우선 순위에 따라 우선 순위가 가장 높은 것부터 낮은 순으로 권장 사항 목록이 표시됩니다. 목록에서 우선 순위가 높은 항목을 해결하기로 결정합니다. **권장 사항**에서 **Endpoint Protection 설치**를 선택합니다.
3. 맬웨어 방지 프로그램을 사용하지 않는 VM의 목록을 표시하는 **Endpoint Protection 설치**가 열립니다. Jeff는 VM 목록을 검토하고 모든 VM을 선택한 다음 **3개의 VM에 설치**를 선택합니다.
   ![Endpoint Protection 설치][4]
4. **Endpoint Protection 선택**이 열리고 Jeff에게 두 가지 맬웨어 방지 솔루션을 제공합니다. Jeff는 **Microsoft 맬웨어 방지** 솔루션을 선택합니다.
5. 맬웨어 방지 솔루션에 대한 추가 정보가 표시됩니다. Jeff는 **만들기**를 선택합니다.
   ![Microsoft 맬웨어 방지][5]
6. Jeff는 **설치**에서 필수 구성 설정을 입력하고 **확인**을 선택합니다.

선택한 VM에서 [Microsoft 맬웨어 방지 프로그램](../security/azure-security-antimalware.md)이 활성화됩니다.

Jeff는 계속해서 높은 우선 순위 및 보통 우선 순위 권장 사항을 살펴보고 구현할지 결정합니다. Jeff는 [보안 권장 사항 관리](security-center-recommendations.md) 문서를 참조하여 권장 사항 및 적용 시 역할을 이해해야 합니다.

Jeff는 [MSRC(Microsoft Security Response Center)](../security/azure-security-response-center.md)가 Azure 네트워크 및 인프라의 선택 보안 모니터링을 수행하고 타사에서 위협 인텔리전스 및 남용 불만 사항을 받는다는 것을 알았습니다. Jeff가 Contoso의 Azure 구독에 대한 보안 연락처 세부 정보를 제공하는 경우 MSRC가 Contoso의 고객 데이터에 불법적이거나 권한이 없는 당사자에 의한 액세스를 검색하게 되면 Microsoft는 Contoso에 이를 알립니다. Jeff가 **보안 연락처 세부 정보 제공** 권장 사항(위의 권장 사항 목록 중 보통 심각도인 권장 사항)을 적용하는 과정을 따라서 살펴보겠습니다.

1. Jeff가 **권장 사항**에서 **보안 연락처 세부 정보 제공**을 선택하면 **보안 연락처 세부 정보 제공**이 열립니다.
2. Jeff는 연락처 정보를 제공할 Azure 구독을 선택합니다. 두 번째 **보안 연락처 세부 정보 제공** 블레이드가 열립니다.
   ![보안 연락처 세부 정보][6]
3. **보안 연락처 세부 정보 제공**에서 Jeff는 다음을 입력합니다.

  - 쉼표로 구분된 보안 연락처 전자 메일 주소(입력할 수 있는 전자 메일 주소 수에 제한이 없음)
  - 보안 연락처 전화 번호

4. 또한 Jeff는 **경고에 대한 전자 메일 받기**옵션을 설정하여 심각도 높은 경고에 대한 전자 메일을 받습니다.
5. 그리고 **확인**을 선택하여 보안 연락처 정보를 Contoso의 구독에 적용합니다.

Jeff는 마지막으로 우선 순위가 낮은 권장 사항 **OS 취약점 재구성**을 검토하고 이 권장 사항을 적용할 수 없다고 결정합니다. 권장 구성을 해제하고자 합니다. Jeff는 오른쪽에 표시되는 점 세 개를 선택한 다음 **해제**를 선택합니다.
   ![권장 사항 해제][7]

## <a name="conclusion"></a>결론
Security Center에서 권장 사항을 모니터링하면 공격이 발생하기 전에 보안 취약점을 제거할 수 있습니다. Security Center에서 보안 정책을 사용하여 보호를 구현하고 유지 관리하여 보안 인시던트를 방지할 수 있습니다.

## <a name="next-steps"></a>다음 단계
이 시나리오에서는 Security Center에서 보안 정책 및 권장 사항을 사용하여 보안 공격을 완화할 수 있는 방법을 보여 줍니다. [사고 대응 시나리오](security-center-incident-response.md)에서 공격이 발생하기 전에 사고 대응 계획을 적절히 수립하는 방법을 참조하세요.

Security Center에 대해 자세히 알아보려면 다음 항목을 참조하세요.

* [보안 상태 모니터링](security-center-monitoring.md) - Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [보안 이벤트 모니터링 및 처리](security-center-events-dashboard.md) - 시간별로 수집된 보안 이벤트를 모니터링하고 처리하는 방법을 알아봅니다.
* [파트너 솔루션 모니터링](security-center-partner-solutions.md) - 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/) - 최신 Azure 보안 뉴스 및 정보를 가져옵니다.

<!--Image references-->
[1]: ./media/security-center-using-recommendations/security-center-policy-inheritance.png
[2]: ./media/security-center-using-recommendations/scenario-roles.png
[3]: ./media/security-center-using-recommendations/select-recommendations-tile.png
[4]: ./media/security-center-using-recommendations/install-endpoint-protection.png
[5]:./media/security-center-using-recommendations/microsoft-antimalware.png
[6]: ./media/security-center-using-recommendations/provide-security-contact-details.png
[7]: ./media/security-center-using-recommendations/dismiss-recommendation.png
