---
title: Azure Traffic Manager의 실제 사용자 측정 | Microsoft Docs
description: Traffic Manager의 실제 사용자 측정 소개
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: ''
tags: ''
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 4e8d808d65c9898d230455d128e3ffc50db303d6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Traffic Manager 실제 사용자 측정 개요

성능 라우팅 방법을 사용하도록 Traffic Manager 프로필을 설정할 경우 서비스는 DNS 쿼리 요청이 생성된 위치를 확인하고, 해당 요청자에게 가장 낮은 우선 순위를 부여하는 Azure 지역으로 해당 요청자를 라우팅하는 결정을 내립니다. 이를 위해 Traffic Manager가 다른 최종 사용자 네트워크에 대해 유지 관리하는 네트워크 대기 시간 인텔리전스를 활용합니다.

실제 사용자 측정 기능을 사용하면 최종 사용자가 사용하는 클라이언트 응용 프로그램에서 Azure 지역으로의 네트워크 대기 시간을 측정하고 Traffic Manager가 라우팅을 결정할 때 해당 정보도 고려하도록 할 수 있습니다. 실제 사용자 측정을 사용하도록 선택하여 최종 사용자가 위치한 네트워크에서 들어오는 요청에 대한 라우팅 정확도를 높일 수 있습니다. 

## <a name="how-real-user-measurements-work"></a>실제 사용자 측정이 작동하는 방식

실제 사용자 측정 기능은 Azure 지역에 대한 클라이언트 응용 프로그램 측정 대기 시간을 사용된 최종 사용자 네트워크에서 확인된 값과 동일하게 유지하는 방식으로 작동합니다. 예를 들어 다른 위치(예: 북미 지역)의 사용자가 액세스하는 웹 페이지가 있는 경우 성능 라우팅 방법을 사용할 때 실제 사용자 측정 기능을 활용하여 서버 응용 프로그램이 호스트되는 최선의 Azure 지역으로 이동되게 할 수 있습니다.

먼저 웹 페이지에 Azure 제공 JavaScript(고유 키 포함)를 포함하면 됩니다. 이 작업이 끝나면 사용자가 해당 웹 페이지를 방문할 때마다 JavaScript는 Traffic Manager를 쿼리하여 측정하려는 Azure 지역에 대한 정보를 가져옵니다. 이 서비스는 끝점 집합을 스크립트로 반환합니다. 이러한 끝점은 해당 Azure 지역에 호스트된 단일 픽셀 이미지를 다운로드하고 요청이 전송된 시간과 첫 번째 바이트가 수신된 시간 사이의 대기 시간을 기록하여 이러한 지역을 계속해서 측정합니다. 이러한 측정값은 Traffic Manager 서비스로 다시 보고됩니다.

시간이 지남에 따라 이 과정이 여러 네트워크 간에 여러 번 진행되면서 Traffic Manager는 최종 사용자가 상주하는 네트워크의 대기 시간 특성에 대해 보다 정확한 정보를 얻게 됩니다. 이 정보는 Traffic Manager의 라우팅 결정에 포함됩니다. 결과적으로 전송된 실제 사용자 측정을 기준으로 이러한 결정의 정확도가 높아집니다.

실제 사용자 측정을 사용하면 Traffic Manager로 전송되는 측정값 수에 따라 요금이 청구됩니다. 가격 책정에 대한 자세한 내용은 [Traffic Manager 가격 책정 페이지](https://azure.microsoft.com/pricing/details/traffic-manager/)를 참조하세요.

## <a name="next-steps"></a>다음 단계
- [웹 페이지에서 실제 사용자 측정](traffic-manager-create-rum-web-pages.md)을 사용하는 방법 알아봅니다.
- [Traffic Manager 작동 방식](traffic-manager-overview.md)
- [Mobile Center](https://docs.microsoft.com/mobile-center/)에 대해 자세히 알아봅니다.
- Traffic Manager가 지원하는 [트래픽 라우팅 방법](traffic-manager-routing-methods.md) 에 대해 자세히 알아봅니다.
- [Traffic Manager 프로필을 만드는](traffic-manager-create-profile.md)

