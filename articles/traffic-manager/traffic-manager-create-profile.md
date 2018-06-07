---
title: "Azure에서 Traffic Manager 프로필 만들기 | Microsoft Docs"
description: "이 문서는 Traffic Manager 프로필을 만드는 방법을 설명합니다."
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/21/2017
ms.author: kumud
ms.openlocfilehash: 1994c8374244b62e65b1a54234775d9a39f72bb3
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-traffic-manager-profile"></a>Traffic Manager 프로필 만들기

이 문서에서는 사용자를 두 Azure Web Apps 끝점으로 라우팅하도록 **우선 순위** 라우팅 형식을 사용하는 프로필을 만드는 방법을 설명합니다. **우선 순위** 라우팅 형식을 사용하면 두 번째 끝점은 백업으로 유지되는 동안 첫 번째 끝점으로 모든 트래픽이 라우팅됩니다. 결과적으로, 첫 번째 끝점이 비정상 상태가 되면 사용자는 두 번째 끝점으로 라우팅될 수 있습니다.

이 문서에서는 이전에 만든 두 개의 Azure Web Apps 끝점이 새로 만든 이 Traffic Manager 프로필에 연결되어 있습니다. Azure Web Apps 끝점을 만드는 방법에 대한 자세한 정보는 [Azure Web Apps 설명서 페이지](https://docs.microsoft.com/azure/app-service/)를 참조하세요. 예를 들어 DNS 이름이 있고 공용 인터넷에 연결할 수 있으며 Azure Web Apps 끝점을 사용하는 경우 모든 끝점을 추가할 수 있습니다.

### <a name="create-a-traffic-manager-profile"></a>Traffic Manager 프로필 만들기
1. 브라우저에서 [Azure Portal](http://portal.azure.com)에 로그인합니다. 계정이 아직 없는 경우 [1개월 무료 평가판](https://azure.microsoft.com/free/)에 등록할 수 있습니다. 
2. **리소스 만들기** > **네트워킹** > **Traffic Manager 프로필** > **만들기**를 클릭합니다.
4. **Traffic Manager 프로필 만들기**에 다음과 같이 입력합니다.
    1. **이름**에서 사용자의 프로필에 사용할 이름을 제공합니다. 이 이름은 trafficmanager.net 내에서 고유해야 하며 DNS 이름 <name>,trafficmanager.net 형식으로 나타나고, Traffic Manager 프로필에 액세스하는 데 사용됩니다.
    2. **라우팅 정책**에서 **우선 순위** 라우팅 방법을 선택합니다.
    3. **구독**에서 이 프로필을 만들 구독을 선택합니다.
    4. **리소스 그룹**에서 이 프로필을 배치할 새 리소스 그룹을 만듭니다.
    5. **리소스 그룹 위치**에서 리소스 그룹의 위치를 선택합니다. 이 설정은 리소스 그룹의 위치를 나타내며 전역적으로 배포되는 Traffic Manager 프로필에는 영향을 미치지 않습니다.
    6. **만들기**를 클릭합니다.
    7. Traffic Manager 프로필의 전역 배포가 완료되면 리소스 중 하나로 해당 리소스 그룹에 나열됩니다.

    ![Traffic Manager 프로필 만들기](./media/traffic-manager-create-profile/Create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager 끝점 추가

1. 포털의 검색 창에서 이전 섹션에서 만들었던 **Traffic Manager 프로필** 이름을 검색하고 표시되는 결과에서 해당 Traffic Manager 프로필을 클릭합니다.
2. **Traffic Manager 프로필**의 **설정** 섹션에서 **엔드포인트**를 클릭합니다.
3. **추가**를 클릭합니다.
4. 다음과 같이 완료합니다.
    1. **형식**의 경우 **Azure 끝점**을 클릭합니다.
    2. 이 끝점을 인식하는 기준으로 사용할 **이름**을 제공합니다.
    3. **대상 리소스 형식**의 경우 **App Service**를 클릭합니다.
    4. **대상 리소스**의 경우 **앱 서비스 선택**을 클릭하여 동일한 구독에서 Web Apps의 목록을 표시합니다. **리소스**에서 첫 번째 엔드포인트로 추가할 앱 서비스를 선택합니다.
    5. **우선 순위**의 경우 **1**로 선택합니다. 이제 모든 트래픽이 정상일 경우 이 끝점으로 전송됩니다.
    6. **사용 안 함으로 추가**를 선택 취소 상태로 유지합니다.
    7. **확인**
5.  다음 Azure Web Apps 끝점에 대해 3, 4단계를 반복합니다. **우선 순위** 값을 **2**로 설정한 상태에서 추가해야 합니다.
6.  두 엔드포인트 추가가 완료되면 **온라인**인 모니터링 상태와 함께 **Traffic Manager 프로필**에 표시됩니다.

    ![Traffic Manager 끝점 추가](./media/traffic-manager-create-profile/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Traffic Manager 프로필 사용
1.  포털의 검색 창에서 이전 섹션에서 만든 **Traffic Manager 프로필** 이름을 검색합니다. 표시되는 결과에서 Traffic Manager 프로필을 클릭합니다.
2. **개요**를 클릭합니다.
3. **Traffic Manager 프로필**에 새로 만든 Traffic Manager 프로필의 DNS 이름이 표시됩니다. 이는 라우팅 형식에서 결정된 대로 올바른 끝점으로 라우팅되도록 모든 클라이언트가 사용할 수 있습니다(예를 들어 웹 브라우저를 사용하여 이동). 이 경우 모든 요청이 첫 번째 끝점으로 라우팅되고 Traffic Manager에서 이를 비정상으로 감지하면 트래픽은 자동으로 다음 끝점으로 장애 조치됩니다.

## <a name="delete-the-traffic-manager-profile"></a>Traffic Manager 프로필 삭제
더 이상 필요하지 않으면 사용자가 만든 리소스 그룹 및 Traffic Manager 프로필을 삭제합니다. 이렇게 하려면 **Traffic Manager 프로필**에서 리소스 그룹을 선택하고 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

- [라우팅 형식](traffic-manager-routing-methods.md)에 대해 자세히 알아보세요.
- [끝점 형식](traffic-manager-endpoint-types.md)에 대해 자세히 알아보세요.
- [끝점 모니터링](traffic-manager-monitoring.md)에 대해 자세히 알아보세요.



