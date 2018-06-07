---
title: Azure Security Center에서 보안 경고 관리| Microsoft Docs
description: 이 문서는 Azure Security Center 기능을 사용하여 보안 경고를 관리하고 대응하는 데 도움이 됩니다.
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/30/2017
ms.author: yurid
ms.openlocfilehash: d088223aa2ea40d5bb9cf0e492e87ef054d86348
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365374"
---
# <a name="managing-and-responding-to-security-alerts-in-azure-security-center"></a>Azure Security Center에서 보안 경고 관리 및 대응
이 문서는 Azure Security Center를 사용하여 보안 경고를 관리하고 대응하는 데 도움이 됩니다.

> [!NOTE]
> 고급 감지를 사용하도록 설정하려면 Azure Security Center 표준으로 업그레이드합니다. 무료 60일 평가판을 사용할 수 있습니다. 업그레이드하려면 [보안 정책](security-center-policies.md)에서 가격 책정 계층을 선택합니다. 자세한 내용은 [Azure Security Center 가격 책정](security-center-pricing.md)을 참조하세요.
>
>

## <a name="what-are-security-alerts"></a>보안 경고란?
보안 센터는 방화벽 및 끝점 보호 솔루션과 같은 Azure 리소스, 네트워크 및 연결된 파트너 솔루션의 로그 데이터를 자동으로 수집하고 분석하며 통합하여 실제 위협을 감지하고 가양성을 줄입니다. 우선 순위가 지정된 보안 경고의 목록은 문제를 신속하게 조사해야 하는 정보 및 공격을 해결하는 방법에 대한 권장 사항과 함께 보안 센터에 표시됩니다.


> [!NOTE]
> 보안 센터 감지 기능이 작동하는 방법에 대한 자세한 내용은 [Azure Security Center 감지 기능](security-center-detection-capabilities.md)을 참고하세요.
>
>

## <a name="managing-security-alerts"></a>보안 경고 관리
**보안 경고** 타일을 확인하여 현재 경고를 검토할 수 있습니다. 다음 단계를 수행하여 각 경고에 대한 세부 정보를 확인합니다.

1. Security Center 대시보드에서 **보안 경고** 타일을 확인합니다.

    ![보안 센터의 보안 경고 타일](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1-ga.png)

2. 경고에 대한 세부 정보를 확인하려면 타일을 클릭하여 **보안 경고**를 엽니다.

   ![Security Center의 보안 경고](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2-ga.png)

이 페이지의 아래 부분에 각 경고에 대한 세부 정보가 있습니다. 정렬하려면 정렬 기준으로 사용할 열을 클릭합니다. 각 열에 대한 정의는 다음과 같습니다.

* **설명**: 경고에 대해 간략히 설명합니다.
* **개수**: 특정 날짜에서 감지한 이 특정 형식의 모든 경고 목록입니다.
* **감지 기준**: 경고를 트리거하는 일을 담당하는 서비스입니다.
* **날짜**: 이벤트가 발생한 날짜입니다.
* **상태**: 해당 경고에 대한 현재 상태입니다. 다음과 같은 두 가지 종류의 상태가 있습니다.
  * **활성**: 보안 경고가 감지되었습니다.
* **심각도**: 심각도 수준(높음, 중간 또는 낮음)입니다.

> [!NOTE]
> Security Center에서 생성된 보안 경고는 Azure 활동 로그에도 나타납니다. Azure 활동 로그에 액세스하는 방법에 대한 자세한 내용은 [리소스에 대한 작업을 감사하기 위해 활동 로그 보기](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)를 참조하세요.
>

### <a name="filtering-alerts"></a>경고 필터링
날짜, 상태 및 심각도를 기반으로 경고를 필터링할 수 있습니다. 경고의 필터링은 보안 경고 보기의 범위를 좁혀야 하는 시나리오에 유용할 수 있습니다. 예를 들어 시스템에서 잠재적 위반을 조사하고 있기 때문에 최근 24시간 동안 발생한 보안 경고를 해결하려고 할 수 있습니다.

1. **보안 경고**에서 **필터**를 클릭합니다. **필터**가 열리면 확인하려는 날짜, 상태 및 심각도 값을 선택합니다.

    ![보안 센터에서 경고 필터링](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3-2017.png)

### <a name="respond-to-security-alerts"></a>보안 경고에 대응
보안 경고를 선택하여 해당 경고를 트리거하는 이벤트 및 공격을 완화하기 위해 수행해야 하는 단계(있는 경우)에 대해 자세히 알아봅니다. 보안 경고는 형식 및 날짜별로 그룹화됩니다. 보안 경고를 클릭하면 그룹화된 경고의 목록이 포함된 페이지가 열립니다.

![Azure Security Center에서 보안 경고에 대한 대응](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-ga.png)

이 경우 트리거된 경고는 의심스러운 RDP(원격 데스크톱 프로토콜) 활동을 참조합니다. 첫 번째 열은 공격 받은 리소스를 보여 줍니다. 두 번째 열은 리소스가 공격받은 빈도를 보여 줍니다. 세 번째 열은 공격 시간을 보여 줍니다. 네 번째 열은 경고의 상태를 보여 주고 다섯 번째 열은 공격의 심각도를 보여 줍니다. 이 정보를 검토한 후에 공격을 받은 리소스를 클릭합니다.

![Azure Security Center에서 보안 경고에 대해 수행할 작업 제안](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-ga.png)

**설명** 필드에 이 이벤트에 대한 자세한 내용이 있습니다. 이러한 추가 세부 사항은 수정 방법에 대한 원본 IP 주소 및 권장사항을 적용할 수 있는 경우 보안 경고인 대상 리소스를 트리거한 항목에 대한 정보를 제공합니다.  일부 경우에 일부 Windows 보안 이벤트 로그가 IP 주소를 포함하지 않기 때문에 원본 IP 주소가 비어 있습니다(사용할 수 없음).

Security Center에서 제안하는 수정은 보안 경고에 따라 다릅니다. 경우에 따라 권장된 수정을 구현하기 위해 기타 Azure 기능을 사용해야 할 수 있습니다. 예를 들어 이 공격에 대한 수정은 [네트워크 ACL](../virtual-network/virtual-networks-acl.md) 또는 [네트워크 보안 그룹](../virtual-network/security-overview.md#security-rules) 규칙을 사용하여 이 공격을 발생시키고 있는 IP 주소를 블랙리스트에 추가하는 것입니다. 다양한 유형의 경고에 대한 자세한 내용은 [Azure Security Center에서 보안 경고](security-center-alerts-type.md)를 참고하세요.

> [!NOTE]
> Security Center는 Linux 컴퓨터에서 악의적인 행동을 감지하는 감사된 레코드인 공통 감사 프레임워크를 활용하는 새로운 집합의 감지에 대한 제한된 미리 보기를 출시했습니다. 구독 ID를 [당사](mailto:ASC_linuxdetections@microsoft.com)로 전자 메일을 보내어 미리 보기에 가입하세요.


## <a name="see-also"></a>참고 항목
이 문서에서는 보안 센터에서 보안 정책을 구성하는 방법을 배웠습니다. 보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Security Center에서 보안 인시던트 처리](security-center-incident.md)
* [Azure Security Center 감지 기능](security-center-detection-capabilities.md)
* [Azure Security Center 계획 및 작업 가이드](security-center-planning-and-operations-guide.md)
* [Azure Security Center FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/) - Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.
