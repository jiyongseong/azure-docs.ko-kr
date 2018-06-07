---
title: "Traffic Manager 끝점 유형 | Microsoft Docs"
description: "이 문서에서는 Azure Traffic Manager와 함께 사용할 수 있는 다양한 유형의 끝점을 설명합니다."
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 4e506986-f78d-41d1-becf-56c8516e4d21
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: kumud
ms.openlocfilehash: 792712e3e529d77ff20a7603b5fbf028ca60f8c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="traffic-manager-endpoints"></a>Traffic Manager 끝점
Microsoft Azure Traffic Manager를 사용하면 다른 데이터 센터에서 실행 중인 응용 프로그램 배포에 네트워크 트래픽을 분산하는 방법을 제어할 수 있습니다. 각 응용 프로그램 배포를 Traffic Manager에서 '끝점'으로 구성합니다. Traffic Manager는 DNS 요청을 받으면 DNS 응답에서 반환할 사용 가능한 끝점을 선택합니다. Traffic Manager는 현재 끝점 상태와 트래픽 라우팅 메서드에 근거하여 선택합니다. 자세한 내용은 [Traffic Manager 작동 방식](traffic-manager-how-traffic-manager-works.md)을 참조하세요.

Traffic Manager에서 지원되는 끝점에는 세 가지 종류가 있습니다.
* **Azure 끝점**은 Azure에서 호스팅되는 서비스에 사용됩니다.
* **외부 끝점**은 온-프레미스 또는 다른 호스팅 공급자 중 하나로 Azure 외부에서 호스팅되는 서비스에 사용됩니다.
* **중첩 끝점** 은 더 크고 복잡한 배포에 대한 요구 사항을 지원하는 더 유연한 트래픽 라우팅 체계를 만들도록 Traffic Manager 프로필을 결합하는 데 사용됩니다.

단일 Traffic Manager 프로필에서 서로 다른 유형의 끝점을 결합하는 방법에는 제한이 없습니다. 각 프로필은 모든 끝점 유형의 혼합을 포함할 수 있습니다.

다음 섹션에서는 각 끝점 유형을 자세히 설명합니다.

## <a name="azure-endpoints"></a>Azure 끝점

Azure 끝점은 Traffic Manager에서 Azure 기반 서비스에 사용됩니다. 다음과 같은 Azure 리소스 유형이 지원됩니다.

* '클래식' IaaS VM 및 PaaS 클라우드 서비스.
* 웹앱
* PublicIPAddress 리소스(직접 또는 Azure Load Balancer를 통해 VM에 연결할 수 있음) publicIpAddress에는 Traffic Manager 프로필에서 사용되도록 지정된 DNS 이름이 있어야 합니다.

PublicIPAddress 리소스는 Azure Resource Manager 리소스입니다. 클래식 배포 모델에는 존재하지 않습니다. 따라서 Traffic Manager의 Azure Resource Manager 환경에서만 지원됩니다. 다른 끝점 유형은 리소스 관리자 및 클래식 배포 모델을 통해 지원됩니다.

Azure 끝점을 사용하는 경우 Traffic Manager는 '클래식' IaaS VM, 클라우드 서비스 또는 웹앱이 중지되고 시작되는 시기를 감지합니다. 이 상태는 끝점 상태에 반영됩니다. 자세한 내용은 [Traffic Manager 끝점 모니터링](traffic-manager-monitoring.md#endpoint-and-profile-status)을 참조하세요. 기본 서비스 중지되면 Traffic Manager는 끝점 상태 검사를 수행하지 않거나 끝점에 트래픽을 보내지 않습니다. 중단된 인스턴스에 대해서는 Traffic Manager 청구 이벤트가 발생하지 않습니다. 서비스가 다시 시작되면 청구를 다시 시작하고 끝점은 트래픽을 받을 수 있게 됩니다. 이 감지는 PublicIpAddress 끝점에는 적용되지 않습니다.

## <a name="external-endpoints"></a>외부 끝점

외부 끝점은 Azure 외부 서비스에 대해 사용됩니다. 예: 온-프레미스에서 호스팅되는 서비스 또는 다른 공급자로 호스팅되는 서비스. 외부 끝점은 개별적으로 사용되거나 동일한 Traffic Manager 프로필에서 Azure 끝점과 결합될 수 있습니다. 외부 끝점과 Azure 끝점을 결합하여 다양한 시나리오를 사용할 수 있습니다.

* 활성-활성 또는 활성-수동 장애 조치(Failover) 모델에서 기존 온-프레미스 응용 프로그램에 대한 향상된 중복성을 제공하는 데 Azure를 사용합니다.
* 전 세계 사용자를 위해 응용 프로그램 대기 시간을 줄이려면 Azure에서 추가 지리적 위치에 대한 기존 온-프레미스 응용 프로그램을 확장합니다. 자세한 내용은 [Traffic Manager '성능' 트래픽 라우팅](traffic-manager-routing-methods.md#performance)을 참조하세요.
* 지속적으로 또는 스파이크 수요를 충족하기 위해 '클라우드로 버스트'로 기존 온-프레미스 응용 프로그램에 대한 추가 용량을 제공하는 데 Azure를 사용합니다.

경우에 따라 Azure 서비스를 참조하는 데 외부 끝점을 사용하는 것이 유용합니다.(예는 [FAQ](traffic-manager-faqs.md#traffic-manager-endpoints) 참조). 이 경우 상태 검사는 외부 끝점 비율이 아닌 Azure 끝점 비율로 청구됩니다. 그러나 Azure 끝점과 달리 기본 서비스를 중지하거나 삭제하는 경우 Traffic Manager에서 끝점을 비활성화하거나 삭제하기 전까지 상태 검사 요금 청구는 계속됩니다.

## <a name="nested-endpoints"></a>중첩 끝점

중첩 끝점은 유연한 트래픽 라우팅 체계를 만들고 더 크고, 복잡한 배포에 대한 요구 사항을 지원하기 위해 Traffic Manager 프로필을 결합합니다. 중첩 끝점을 사용하면 'child' 프로필이 'parent' 프로필에 끝점으로 추가됩니다. 자식 및 부모 프로필 모두 다른 중첩 프로필을 포함하여 모든 유형의 다른 끝점을 포함할 수 있습니다. 자세한 내용은 [중첩 Traffic Manager 프로필](traffic-manager-nested-profiles.md)을 참조하세요.

## <a name="web-apps-as-endpoints"></a>끝점으로 웹앱

Traffic Manager에서 끝점으로 웹앱을 구성하는 경우 몇 가지 추가 고려 사항이 적용됩니다.

1. '표준' SKU 이상에서의 Web Apps만 Traffic Manager와 함께 사용할 수 있습니다. 더 낮은 SKU의 웹앱을 추가하려는 시도는 실패합니다. 기존 웹앱의 SKU를 다운그레이드하면 Traffic Manager는 해당 웹앱에 더 이상 트래픽을 전송하지 않게 됩니다.
2. 끝점에서 HTTP 요청을 받으면 요청의 '호스트' 헤더를 사용하여 요청을 처리해야 하는 웹앱을 결정합니다. 호스트 헤더는 요청을 시작하는 데 사용된 DNS 이름을 포함합니다(예: 'contosoapp.azurewebsites.net'). 웹앱에 다른 DNS 이름을 사용하려면 DNS 이름은 앱에 대한 사용자 지정 도메인 이름으로 등록되어야 합니다. Azure 끝점으로 Web App 끝점을 추가하면 Traffic Manager 프로필 DNS 이름은 앱에 대해 자동으로 등록됩니다. 이 등록은 끝점이 삭제될 때 자동으로 제거됩니다.
3. 각 Traffic Manager 프로필은 각 Azure 지역에서 최대 하나의 웹앱 끝점을 가질 수 있습니다. 이 제약 조건을 해결하려면 웹앱을 외부 끝점으로 구성할 수 있습니다. 자세한 내용은 [FAQ](traffic-manager-faqs.md#traffic-manager-endpoints)을 참조하세요.

## <a name="enabling-and-disabling-endpoints"></a>끝점 활성화 및 비활성화

Traffic Manager에서 끝점 비활성화는 유지 관리 모드이거나 다시 배포할 예정인 끝점에서 트래픽을 일시적으로 제거하는 데 매우 유용합니다. 끝점이 다시 작동하면 다시 활성화할 수 있습니다.

끝점은 리소스 관리자 및 클래식 배포 모델 모두에서 지원되는 Traffic Manager 포털, PowerShell, CLI 또는 REST API를 통해 활성화 및 비활성화될 수 있습니다.

> [!NOTE]
> Azure 끝점을 해제해도 Azure의 배포 상태에는 영향을 주지 않습니다. Azure 서비스(예: VM 또는 웹앱)는 실행 상태를 유지하고 Traffic Manager에서 비활성화되었을 때에도 트래픽을 수신할 수 있습니다. 트래픽은 Traffic Manager 프로필 DNS 이름을 통해서가 아닌 해당 서비스 인스턴스로 직접 보내질 수 있습니다. 자세한 내용은 [Traffic Manager 작동 방식](traffic-manager-how-traffic-manager-works.md)을 참조하세요.

트래픽을 받는 각 끝점의 현재 자격은 다음 요인에 따라 다릅니다.

* 프로필 상태(사용/사용 안 함)
* 끝점 상태(사용/사용 안 함)
* 해당 끝점에 대한 상태 검사 결과

자세한 내용은 [Traffic Manager 끝점 모니터링](traffic-manager-monitoring.md#endpoint-and-profile-status)을 참조하세요.

> [!NOTE]
> Traffic Manager는 DNS 수준에서 작동하므로 모든 끝점에 대한 기존 연결에 영향을 미칠 수 없습니다. 끝점을 사용할 수 없는 경우 Traffic Manager는 사용 가능한 끝점에 새 연결을 전달합니다. 그러나 비활성화 또는 비정상 끝점 뒤의 호스트는 해당 세션이 종료될 때까지 기존 연결을 통해 트래픽을 계속 수신할 수 있습니다. 트래픽이 기존 연결에서 비워지도록 하기 위해 응용 프로그램은 각 끝점에 사용된 세션 기간을 제한해야 합니다.

프로필의 모든 끝점이 비활성화되거나 프로필 자체가 비활성화된 경우 Traffic Manager은 'NXDOMAIN' 응답을 새 DNS 쿼리에 보냅니다.


## <a name="next-steps"></a>다음 단계

* [Traffic Manager 작동 방식](traffic-manager-how-traffic-manager-works.md)에 대해 알아봅니다.
* Traffic Manager [끝점 모니터링 및 자동 장애 조치(failover)](traffic-manager-monitoring.md)에 대해 알아봅니다.
* Traffic Manager [트래픽 라우팅 방법](traffic-manager-routing-methods.md)에 대해 알아봅니다.
