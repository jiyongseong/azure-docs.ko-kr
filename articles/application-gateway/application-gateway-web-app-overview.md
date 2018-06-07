---
title: Azure Application Gateway를 사용하는 다중 테넌트 백 엔드 개요 | Microsoft Docs
description: 이 페이지에서는 다중 테넌트 백 엔드에 대한 Application Gateway 지원의 개요를 제공합니다.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: ''
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: victorh
ms.openlocfilehash: 7c00369737d05f414f9ac23d9d3e8918b908fc80
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
---
# <a name="application-gateway-support-for-multi-tenant-back-ends"></a>Application Gateway는 다중 테넌트 백 엔드를 지원합니다.

Azure Application Gateway는 가상 머신 확장 집합, 네트워크 인터페이스, 공개/개인 IP 또는 FQDN(정규화된 도메인 이름)을 백 엔드 풀의 일부로 지원합니다. 기본적으로 응용 프로그램 게이트웨이는 클라이언트에서 들어오는 HTTP 호스트 헤더를 변경하지 않으며 변경되지 않은 헤더를 백 엔드로 보냅니다. [Azure Web Apps](../app-service/app-service-web-overview.md)처럼 기본적으로 다중 테넌트이고 특정 호스트 헤더 또는 SNI 확장을 사용하여 올바른 끝점을 확인하는 여러 서비스가 있습니다. 이제 Application Gateway는 사용자가 백 엔드 HTTP 설정에 따라 들어오는 HTTP 호스트 헤더를 덮어쓰는 기능을 지원합니다. 이 기능을 사용하면 다중 테넌트 백 엔드 Azure 웹앱 및 API 관리를 지원할 수 있습니다. 이 기능은 표준 및 WAF SKU에 모두 제공됩니다. 다중 테넌트 백 엔드 지원은 SSL 종료 및 종단 간 SSL 시나리오에서도 작동합니다.

![웹앱 시나리오](./media/application-gateway-web-app-overview/scenario.png)

호스트 재정의를 지정하는 기능은 HTTP 설정에서 정의되며 규칙을 만드는 동안 모든 백 엔드 풀에 적용할 수 있습니다. 다중 테넌트 백 엔드는 호스트 헤더 및 SNI 확장을 재정의하는 다음과 같은 두 가지 방법을 지원합니다.

1. 호스트 이름을 HTTP 설정에서 고정된 값으로 설정하는 기능. 이 기능을 사용하면 HTTP 설정이 적용되는 백 엔드 풀의 모든 트래픽에서 호스트 헤더가 이 값으로 재정의됩니다. 종단 간 SSL을 사용하면 이 재정의된 호스트 이름이 SNI 확장에 사용됩니다. 이 기능은 백 엔드 풀 팜이 들어오는 고객 호스트 헤더와 다른 호스트 헤더를 예상하는 시나리오를 지원할 수 있습니다.

2. 백 엔드 풀 멤버의 IP 또는 FQDN에서 호스트 이름을 파생하는 기능. HTTP 설정은 개별 백 엔드 풀 멤버에서 호스트 이름을 파생하는 옵션을 사용하여 구성할 경우 백 엔드 풀 멤버의 FQDN에서 호스트 이름을 선택하는 옵션도 제공합니다. 종단 간 SSL을 사용하면 이 호스트 이름이 FQDN에서 파생되며 SNI 확장에 사용됩니다. 이 기능을 사용하면 백 엔드 풀이 Azure 웹앱 같은 다중 테넌트 PaaS 서비스를 두 개 이상 가질 수 있고 각 멤버에 대한 요청의 호스트 헤더에 FQDN에서 파생된 호스트 이름이 포함되는 시나리오를 지원할 수 있습니다.

> [!NOTE]
> 위의 두 사례 모두에서 설정은 실시간 트래픽 동작에만 영향을 주고 상태 프로브 동작에는 영향을 주지 않습니다. 사용자 지정 프로브는 이미 프로브 구성에서 호스트 헤더를 지정하는 기능을 지원합니다. 이제 사용자 지정 프로브는 현재 구성된 HTTP 설정에서 호스트 헤더 동작을 파생하는 기능도 지원합니다. 이 구성은 프로브 구성의 `PickHostNameFromBackendHttpSettings` 매개 변수를 사용하여 지정할 수 있습니다. 종단 간 기능이 작동하려면 올바른 구성을 반영하도록 프로브와 HTTP 설정을 모두 수정해야 합니다.

이 기능을 사용하여 고객이 HTTP 설정 및 사용자 지정 프로브의 옵션을 적절한 구성으로 지정합니다. 그러면 규칙을 사용하여 이 설정이 수신기와 백 엔드 풀에 연결됩니다.

## <a name="next-steps"></a>다음 단계

[Application Gateway를 사용하여 App Service Web Apps 구성](application-gateway-web-app-powershell.md)을 방문하여 웹앱을 백 엔드 풀 멤버로 사용하여 응용 프로그램 게이트웨이를 설정하는 방법을 알아봅니다.
