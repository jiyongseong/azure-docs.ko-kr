---
title: Azure Portal을 사용하여 Azure DDoS Protection 표준 관리 | Microsoft Docs
description: Azure Monitor에서 Azure DDoS Protection 표준 원격 분석을 사용하여 공격을 완화하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2018
ms.author: jdial
ms.openlocfilehash: dd094f2b9cdb9b5eb164dda2925d094cafa7cd89
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895615"
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure DDoS Protection 표준 관리

DDoS(배포된 서비스 거) 보호를 사용 및 사용하지 않도록 설정하는 방법과 원격 분석을 사용하여 Azure DDoS Protection 표준으로 DDoS 공격을 완화하는 방법에 대해 알아봅니다. DDoS Protection 표준은 할당된 Azure [공용 IP 주소](virtual-network-public-ip-address.md)가 있는 Application Gateway, 부하 분산 장치 및 가상 머신과 같은 Azure 리소스를 보호합니다. DDoS Protection 표준 및 해당 기능에 대한 자세한 내용은 [DDoS Protection 표준 개요](ddos-protection-overview.md)를 참조하세요.

이 자습서의 단계를 완료하기 전에 [네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할에 할당된 계정 또는 [사용 권한](#permissions)에 나열된 적절한 작업이 할당된 [사용자 지정 역할](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)로 Azure Portal(https://portal.azure.com)에 로그인합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-a-ddos-protection-plan"></a>DDoS 보호 계획 만들기

DDoS 보호 계획은 구독 전반에 걸쳐 DDoS 보호 표준을 사용하도록 설정된 일단의 가상 네트워크를 정의합니다. 조직에 대해 하나의 DDoS 보호 계획을 구성하고 여러 구독의 가상 네트워크를 동일한 계획에 연결할 수 있습니다. 또한 DDoS 보호 계획 자체는 계획을 만드는 중에 선택한 구독과도 연결됩니다. 보호된 공용 IP 주소의 수가 100개를 초과하는 경우 계획이 연결된 구독에 따라 계획에 대한 월별 정기 요금과 초과분 요금이 부과됩니다. DDoS 가격 책정에 대한 자세한 내용은 [가격 정보](https://azure.microsoft.com/pricing/details/ddos-protection/)를 참조하세요.

대부분의 조직에서는 둘 이상의 계획을 만들 필요가 없습니다. 계획은 구독 간에 이동할 수 없습니다. 계획이 속한 구독을 변경하려면 [기존 계획을 삭제](#work-with-ddos-protection-plans)하고 새 계획을 만들어야 합니다.

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 선택합니다.
2. *DDoS*를 검색합니다. **DDos 보호 계획**이 검색 결과에 표시되면 선택합니다.
3. **만들기**를 선택합니다.
4. 값을 직접 입력 또는 선택하거나 다음 예의 값을 입력 또는 선택한 다음, **만들기**를 선택합니다.

    |설정        |값                                              |
    |---------      |---------                                          |
    |Name           | myDdosProtectionPlan                              |
    |구독   | 사용 중인 구독을 선택합니다.                         |
    |리소스 그룹 | **새로 만들기**를 선택하고, *myResourceGroup*을 입력합니다. |
    |위치       | 미국 동부                                           |

## <a name="enable-ddos-for-a-new-virtual-network"></a>새 가상 네트워크에 대한 DDoS 사용

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 선택합니다.
2. **네트워킹**을 선택한 다음 **가상 네트워크**를 선택합니다.
3. 값을 직접 입력 또는 선택하거나 다음 예의 값을 입력 또는 선택하고, 나머지 기본값을 적용한 다음, **만들기**를 선택합니다.

    | 설정         | 값                                                        |
    | ---------       | ---------                                                    |
    | Name            | myVirtualNetwork                                             |
    | 구독    | 사용 중인 구독을 선택합니다.                                    |
    | 리소스 그룹  | **기존 항목 사용**을 선택한 다음, **myResourceGroup**을 선택합니다. |
    | 위치        | 미국 동부                                                      |
    | DDoS 보호 | **표준**을 선택한 다음, **DDoS 보호** 아래에서 **myDdosProtectionPlan**을 선택합니다. 선택한 계획은 가상 네트워크와 동일하거나 다른 구독에 있을 수 있지만, 두 구독은 모두 동일한 Azure Active Directory 테넌트에 연결되어야 합니다.|

가상 네트워크에 대해 DDoS 표준을 사용하도록 설정하면 가상 네트워크를 다른 리소스 그룹 또는 구독으로 이동할 수 없습니다. DDoS 표준을 사용하도록 설정된 가상 네트워크를 이동해야 하는 경우 먼저 DDoS 표준을 사용하지 않도록 설정하고, 가상 네트워크를 이동한 다음, DDoS 표준을 사용하도록 설정합니다. 이동 후에는 가상 네트워크의 모든 보호된 공용 IP 주소에 대한 자동 조정된 정책 임계값이 다시 설정됩니다.

## <a name="enable-ddos-for-an-existing-virtual-network"></a>기존 가상 네트워크에 DDoS 사용

1. 기존 DDoS 보호 계획이 없는 경우 [DDoS 보호 계획 만들기](#create-a-ddos-protection-plan)의 단계를 완료하여 DDoS 보호 계획을 만듭니다.
2. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 선택합니다.
3. 포털 위쪽의 **리소스, 서비스 및 문서 검색 상자**에서 DDoS 보호 표준을 사용하도록 설정하려는 가상 네트워크의 이름을 입력합니다. 가상 네트워크의 이름이 검색 결과에 표시되면 선택합니다.
4. **설정** 아래에서 **DDoS 보호**를 선택합니다.
5. **표준**을 선택합니다. **DDoS 보호 계획** 아래에서 기존 DDoS 보호 계획 또는 1단계에서 만든 계획을 선택한 다음, **저장**을 선택합니다. 선택한 계획은 가상 네트워크와 동일하거나 다른 구독에 있을 수 있지만, 두 구독은 모두 동일한 Azure Active Directory 테넌트에 연결되어야 합니다.

## <a name="disable-ddos-for-a-virtual-network"></a>가상 네트워크에 DDoS 사용 안 함

1. 포털 위쪽의 **리소스, 서비스 및 문서 검색 상자**에서 DDoS 보호 표준을 사용하지 않도록 설정하려는 가상 네트워크의 이름을 입력합니다. 가상 네트워크의 이름이 검색 결과에 표시되면 선택합니다.
2. **설정** 아래에서 **DDoS 보호**를 선택합니다.
3. **DDoS 보호 계획** 아래에서 **기본**을 선택한 다음, **저장**을 선택합니다.

## <a name="work-with-ddos-protection-plans"></a>DDoS 보호 계획 작업

1. 포털의 왼쪽 위에서 **모든 서비스**를 선택합니다.
2. **필터** 상자에서 *DDoS*를 입력합니다. **DDoS 보호 계획**이 결과에 표시되면 선택합니다.
3. 목록에서 보려는 보호 계획을 선택합니다.
4. 계획과 연결된 모든 가상 네트워크가 나열됩니다.
5. 계획을 삭제하려면 먼저 모든 가상 네트워크와의 연결을 끊어야 합니다. 가상 네트워크에서 계획을 분리하려면 [가상 네트워크에 DDoS 사용 안 함](#disable-ddos-for-a-virtual-network)을 참조하세요.

## <a name="configure-alerts-for-ddos-protection-metrics"></a>DDoS 보호 메트릭에 대한 경고 구성

Azure Monitor 경고 구성을 사용하면 공격 중에 활성 완화가 있는 경우 알리는 데 사용할 수 있는 DDoS 보호 메트릭 중 하나를 선택할 수 있습니다. 조건이 충족되면 지정된 주소에서 경고 메일을 받습니다.

1. 포털의 왼쪽 위에서 **모든 서비스**를 선택합니다.
2. **필터** 상자에서 *모니터*를 입력합니다. 결과에 **모니터**가 표시되면 해당 모니터를 선택합니다.
3. **공유 서비스** 아래에서 **메트릭**을 선택합니다.
4. 값을 직접 입력 또는 선택하거나 다음 예의 값을 입력하고, 나머지 기본값을 적용한 다음, **확인**을 선택합니다.

    |설정                  |값                                                                                               |
    |---------                |---------                                                                                           |
    |Name                     | myDdosAlert                                                                                        |
    |구독             | 경고를 받으려는 공용 IP 주소가 포함된 구독을 선택합니다.        |
    |리소스 그룹           | 경고를 받으려는 공용 IP 주소가 포함된 리소스 그룹을 선택합니다.      |
    |리소스                 | 경고를 받으려는 공용 IP 주소가 포함된 공용 IP 주소를 선택합니다. DDoS는 가상 네트워크 내의 리소스에 할당된 공용 IP 주소를 모니터링합니다. 가상 네트워크에 공용 IP 주소가 있는 리소스가 없으면 먼저 공용 IP 주소를 사용하여 리소스를 만들어야 합니다. Azure App Service 환경 및 Azure VPN Gateway를 제외하고 [Azure 서비스에 대한 가상 네트워크](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)에 나열된 Resource Manager(클래식이 아님)를 통해 배포된 모든 리소스의 공용 IP 주소를 모니터링할 수 있습니다. 이 자습서를 계속 진행하려면 [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 가상 머신을 빠르게 만들면 됩니다.                   |
    |메트릭                   | DDoS 공격 진행 여부                                                                            |
    |임계값                | 1 - **1**은 공격을 받고 있음을 나타냅니다. **0**은 공격을 받고 있지 않음을 나타냅니다.                         |
    |기간                   | 선택한 값을 선택합니다.                                                                   |
    |이메일을 통해 알림         | 확인란을 선택합니다.                                                                                  |
    |추가 관리자 | 구독에 대한 이메일 소유자, 기여자 또는 읽기 권한자가 아닌 경우 이메일 주소를 입력합니다. |

    공격 탐지 후 몇 분 내에 다음 그림과 비슷한 Azure Monitor 메트릭에서 이메일을 받게 됩니다.

    ![공격 경고](./media/manage-ddos-protection/ddos-alert.png)


DDoS 공격을 시뮬레이션하여 경고의 유효성을 검사하려면 [DDoS 탐지 유효성 검사](#validate-ddos-detection)를 참조하세요.

경고를 만들기 위해 [웹후크 구성](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 및 [Logic Apps](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)에 대해 자세히 알아볼 수도 있습니다.

## <a name="configure-logging-for-ddos-protection-metrics"></a>DDoS 보호 메트릭에 대한 로깅 구성

1. 포털의 왼쪽 위에서 **모든 서비스**를 선택합니다.
2. **필터** 상자에서 *모니터*를 입력합니다. 결과에 **모니터**가 표시되면 해당 모니터를 선택합니다.
3. **설정** 아래에서 **진단 설정**을 선택합니다.
4. 로깅하려는 공용 IP 주소가 포함된 **구독** 및 **리소스 그룹**을 선택합니다.
5. **리소스 종류**에 대해 **공용 IP 주소**를 선택한 다음, 메트릭을 로깅하려는 특정 공용 IP 주소를 선택합니다.
6. **Turn on diagnostics to collect the following data**(다음 데이터를 수집하도록 진단 켜기)를 선택하고, 다음 옵션 중에서 필요한 만큼 상당 수를 선택합니다.

    - **저장소 계정에 보관**: 데이터가 Azure Storage 계정에 기록됩니다. 이 옵션에 대한 자세한 내용은 [진단 로그 보관](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요.
    - **이벤트 허브로의 스트림**: 로그 수신기에서 Azure Event Hub를 사용하여 로그를 선택할 수 있도록 합니다. 이벤트 허브는 Splunk 또는 기타 SIEM 시스템과 통합할 수 있습니다. 이 옵션에 대한 자세한 내용은 [이벤트 허브로 진단 로그 스트림](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요.
    - **Log Analytics에 보내기**: Azure OMS Log Analytics 서비스에 로그를 기록합니다. 이 옵션에 대한 자세한 내용은 [Log Analytics에서 사용할 로그 수집](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요.

DDoS 공격을 시뮬레이션하여 로깅의 유효성을 검사하려면 [DDoS 탐지 유효성 검사](#validate-ddos-detection)를 참조하세요.

## <a name="use-ddos-protection-telemetry"></a>DDoS Protection 원격 분석 사용

공격에 대한 원격 분석이 Azure Monitor를 통해 실시간으로 제공됩니다. 이러한 원격 분석 기능은 공용 IP 주소의 위험이 완화되는 동안에만 사용할 수 있습니다. 공격이 완화되기 전 또는 후에는 원격 분석이 표시되지 않습니다.

1. 포털의 왼쪽 위에서 **모든 서비스**를 선택합니다.
2. **필터** 상자에서 *모니터*를 입력합니다. 결과에 **모니터**가 표시되면 해당 모니터를 선택합니다.
3. **공유 서비스** 아래에서 **메트릭**을 선택합니다.
4. 원격 분석하려는 공용 IP 주소가 포함된 **구독** 및 **리소스 그룹**을 선택합니다.
5. **리소스 종류**에 대해 **공용 IP 주소**를 선택한 다음, 원격 분석하려는 특정 공용 IP 주소를 선택합니다.
6. 일련의 **사용 가능한 메트릭**이 화면 왼쪽에 표시됩니다. 이러한 메트릭을 선택할 경우 개요 화면의 **Azure Monitor 메트릭 차트**에 그래프로 표시됩니다.

메트릭 이름은 다양한 패킷 유형과 바이트 및 패킷을 나타내며, 각 메트릭에서 태그 이름의 기본 구조는 다음과 같습니다.

- **Dropped 태그 이름**(예: **Inbound Packets Dropped DDoS**): DDoS 보호 시스템에서 삭제/스크럽한 패킷의 수입니다.
- **Forwarded 태그 이름**(예: **Inbound Packets Forwarded DDoS**): DDoS 시스템에서 대상 VIP로 전달한 패킷의 수입니다(필터링되지 않은 트래픽).
- **태그 이름 없음**(예: **Inbound Packets DDoS**): 스크럽 시스템으로 들어온 패킷의 총 수입니다(삭제 및 전달된 패킷의 합계를 나타냄).

DDoS 공격을 시뮬레이션하여 원격 분석의 유효성을 검사하려면 [DDoS 탐지 유효성 검사](#validate-ddos-detection)를 참조하세요.

## <a name="view-ddos-mitigation-policies"></a>DDoS 완화 정책 보기

DDoS 보호 표준은 DDoS를 사용하도록 설정된 가상 네트워크에서 보호되는 리소스의 각 공용 IP 주소에 대해 자동 조정된 세 가지 완화 정책(TCP SYN, TCP 및 UDP)을 적용합니다. 다음 그림과 같이 **DDoS 완화를 트리거하는 인바운드 TCP 패킷** 및 **DDoS 완화를 트리거하는 인바운드 UDP 패킷** 메트릭을 선택하여 정책 임계값을 볼 수 있습니다.

![완화 정책 보기](./media/manage-ddos-protection/view-mitigation-policies.png)

정책 임계값은 Azure 기계 학습 기반의 네트워크 트래픽 프로파일링을 통해 자동으로 구성됩니다. 정책 임계값을 위반한 경우에만 공격을 받고 있는 IP 주소에 대해 DDoS 완화가 발생합니다.

## <a name="validate-ddos-detection"></a>DDoS 탐지 유효성 검사

Microsoft는 [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud)와 협력하여 시뮬레이션을 위해 DDoS Protection을 사용하도록 설정된 공용 IP 주소에 대한 트래픽을 생성할 수 있는 인터페이스를 구축했습니다. BreakPoint Cloud 시뮬레이션을 사용하여 수행할 수 있는 작업은 다음과 같습니다.

- Microsoft Azure DDoS Protection에서 DDoS 공격으로부터 Azure 리소스를 보호하는 방법을 확인합니다.
- DDoS 공격을 받는 동안 인시던트 응답 프로세스를 최적화합니다.
- DDoS 규정 준수 문서화
- 네트워크 보안 팀을 교육합니다.

## <a name="permissions"></a>권한

DDoS 보호 계획을 사용하려면 다음 표에 나열된 적절한 작업이 할당된 [네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할 또는 [사용자 지정](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 역할에 계정을 할당해야 합니다.

| 조치                                            | Name                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | DDoS 보호 계획 읽기              |
| Microsoft.Network/ddosProtectionPlans/write       | DDoS 보호 계획 만들기 또는 업데이트  |
| Microsoft.Network/ddosProtectionPlans/delete      | DDoS 보호 계획 삭제            |
| Microsoft.Network/ddosProtectionPlans/join/action | DDoS 보호 계획에 조인              |

가상 네트워크에 DDoS 보호를 사용하도록 설정하려면 계정에도 [가상 네트워크에 적절한 작업](manage-virtual-network.md#permissions)을 할당해야 합니다.

## <a name="next-steps"></a>다음 단계

- 가상 네트워크에 대한 [Azure 정책](policy-samples.md) 만들기 및 적용