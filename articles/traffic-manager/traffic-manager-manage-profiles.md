---
title: "Microsoft Azure Traffic Manager 프로필 | Microsoft Docs"
description: "이 문서는 Azure Traffic Manager 프로필을 만들고, 사용하거나 사용하지 않도록 설정하며, 삭제하는 데 도움이 됩니다."
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: f06e0365-0a20-4d08-b7e1-e56025e64f66
ms.service: traffic-manager
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: kumud
ms.openlocfilehash: e19f8f373fed8c7e9ab64f2fc9e34eba182af717
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="manage-an-azure-traffic-manager-profile"></a>Azure Traffic Manager 프로필 관리

Traffic Manager 프로필에서는 트래픽 라우팅 방법을 사용하여 클라우드 서비스나 웹 사이트 끝점에 대한 트래픽의 배포를 제어합니다. 이 문서에서는 이러한 프로필을 만들고 관리하는 방법을 설명합니다.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager 프로필 만들기

Azure Portal을 사용하여 Traffic Manager 프로필을 만들 수 있습니다. 프로필을 만든 후 Azure Portal에서 끝점, 모니터링 및 기타 설정을 구성할 수 있습니다. Traffic Manager는 프로필당 최대 200개의 끝점을 지원합니다. 하지만 대부분의 사용 시나리오에는 적은 수의 끝점만 필요합니다.

### <a name="to-create-a-traffic-manager-profile"></a>Traffic Manager 프로필을 만들려면

1. 브라우저에서 [Azure Portal](http://portal.azure.com)에 로그인합니다. 아직 계정이 없는 경우 [1개월 무료 평가판](https://azure.microsoft.com/free/)을 등록할 수 있습니다. 
2. **리소스 만들기** > **네트워킹** > **Traffic Manager 프로필** > **만들기**를 클릭합니다.
4. **Traffic Manager 프로필 만들기**에서 다음과 같이 완료합니다.
    1. **이름**에서 사용자의 프로필에 사용할 이름을 제공합니다. 이 이름은 trafficmanager.net 내에서 고유해야 하며 DNS 이름 <name>,trafficmanager.net 형식으로 나타나고, Traffic Manager 프로필에 액세스하는 데 사용됩니다.
    2. **라우팅 정책**에서 **우선 순위** 라우팅 방법을 선택합니다.
    3. **구독**에서 이 프로필을 만들 구독을 선택합니다.
    4. **리소스 그룹**에서 이 프로필을 배치할 새 리소스 그룹을 만듭니다.
    5. **리소스 그룹 위치**에서 리소스 그룹의 위치를 선택합니다. 이 설정은 리소스 그룹의 위치를 나타내며 전역적으로 배포되는 Traffic Manager 프로필에는 영향을 미치지 않습니다.
    6. **만들기**를 클릭합니다.
    7. Traffic Manager 프로필의 전역 배포가 완료되면 리소스 중 하나로 해당 리소스 그룹에 나열됩니다.

## <a name="disable-enable-or-delete-a-profile"></a>프로필 사용 안 함, 사용 또는 삭제

기존 프로필을 사용하지 않도록 설정하면 Traffic Manager에서 구성된 끝점의 사용자 요청을 참조하지 않을 수 있습니다. Traffic Manager 프로필을 사용하지 않도록 설정하면 프로필 및 프로필에 포함된 정보는 그대로 유지되며 Traffic Manager 인터페이스에서 편집할 수 있습니다.  프로필을 다시 사용하도록 설정하는 경우 조회가 다시 시작됩니다. Azure Portal에서 Traffic Manager 프로필을 만들면 자동으로 활성화됩니다. 프로필이 더 이상 필요하지 않으면 해당 프로필을 삭제할 수 있습니다.

### <a name="to-disable-a-profile"></a>프로필을 사용하지 않도록 설정하려면

1. 사용자 지정 도메인 이름을 사용하고 있는 경우 Traffic Manager 프로필을 더 이상 가리키지 않도록 인터넷 DNS 서버에서 CNAME 레코드를 변경합니다.
2. Traffic Manager 프로필 설정을 통해 끝점에 보내는 트래픽이 중지됩니다.
3. 브라우저에서 [Azure Portal](http://portal.azure.com)에 로그인합니다.
2. 포털의 검색 창에서 수정하려는 **Traffic Manager 프로필** 이름을 검색한 다음 표시되는 결과에서 Traffic Manager 프로필을 클릭합니다.
3. **개요** > **사용 안 함**을 클릭합니다.
4. Traffic Manager 프로필을 사용하지 않는 것으로 확인합니다.

### <a name="to-enable-a-profile"></a>프로필을 사용하려면

1. 브라우저에서 [Azure Portal](http://portal.azure.com)에 로그인합니다.
2. 포털의 검색 창에서 수정하려는 **Traffic Manager 프로필** 이름을 검색한 다음 표시되는 결과에서 Traffic Manager 프로필을 클릭합니다.
3. **개요** > **사용**을 클릭합니다.
1. 사용자 지정 도메인 이름을 사용하고 있는 경우 Traffic Manager 프로필의 도메인 이름을 가리키도록 인터넷 DNS 서버에 CNAME 리소스 레코드를 만듭니다.
2. 트래픽이 끝점에 다시 전달됩니다.

### <a name="to-delete-a-profile"></a>프로필을 삭제하려면

1. 인터넷 DNS 서버의 DNS 리소스 레코드에서 Traffic Manager 프로필의 도메인 이름을 가리키는 CNAME 리소스 레코드를 더 이상 사용하지 않는지 확인합니다.
2. 포털의 검색 창에서 수정하려는 **Traffic Manager 프로필** 이름을 검색한 다음 표시되는 결과에서 Traffic Manager 프로필을 클릭합니다.
3. **개요** > **삭제**를 클릭합니다.
4. Traffic Manager 프로필 삭제를 확인합니다.

## <a name="next-steps"></a>다음 단계

* [끝점 추가](traffic-manager-endpoints.md)
* [우선 순위 라우팅 메서드 구성](traffic-manager-configure-priority-routing-method.md)
* [지리적 라우팅 메서드 구성](traffic-manager-configure-geographic-routing-method.md) 
* [가중 라우팅 메서드 구성](traffic-manager-configure-weighted-routing-method.md)
* [성능 라우팅 메서드 구성](traffic-manager-configure-performance-routing-method.md)
