---
title: "회사 인터넷 도메인이 Traffic Manager 도메인 이름을 가리키도록 설정 | Microsoft Docs"
description: "이 문서에서는 사용자의 회사 도메인 이름이 트래픽 관리자 도메인 이름을 가리킵니다."
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 29822946-2d45-4434-ba47-fc180a445cc3
ms.service: traffic-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
ms.openlocfilehash: 0322b3510cfd4f94031d8c1db8f1cc032b997fa8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="point-a-company-internet-domain-to-an-azure-traffic-manager-domain"></a>회사 인터넷 도메인이 Azure Traffic Manager 도메인을 가리키도록 설정

Traffic Manager 프로필을 만들 때에는 Azure에서 해당 프로필에 DNS 이름을 자동으로 할당합니다. DNS 영역의 이름을 사용하려면 Traffic Manager 프로필의 도메인 이름으로 매핑하는 CNAME DNS 레코드를 만듭니다. Traffic Manager 프로필의 구성 페이지에 있는 **일반** 섹션에서 Traffic Manager 도메인 이름을 확인할 수 있습니다.

예를 들어 www.contoso.com을 contoso.trafficmanager.net Traffic Manager DNS 이름으로 가리키도록 하려면 DNS 리소스 레코드를 다음과 같이 만듭니다.

    www.contoso.com IN CNAME contoso.trafficmanager.net

이제 *www.contoso.com*으로 요청되는 모든 트래픽이 *contoso.trafficmanager.net*으로 이동됩니다.

> [!IMPORTANT]
> *contoso.com*과 같은 두 번째 수준의 도메인이 트래픽 관리자 도메인을 가리킬 수 없습니다. DNS 프로토콜 표준에서는 두 번째 수준 도메인 이름에 대한 CNAME 레코드를 허용하지 않습니다.

## <a name="next-steps"></a>다음 단계

* [트래픽 관리자 라우팅 방법](traffic-manager-routing-methods.md)
* [트래픽 관리자 - 프로필 사용 안 함, 사용 또는 삭제](disable-enable-or-delete-a-profile.md)
* [트래픽 관리자 - 끝점 사용 안 함 또는 사용](disable-or-enable-an-endpoint.md)
