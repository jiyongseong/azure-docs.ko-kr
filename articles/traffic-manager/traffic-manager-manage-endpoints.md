---
title: "Azure Traffic Manager에서 끝점 관리 | Microsoft Docs"
description: "이 문서는 Azure Traffic Manager에서 끝점을 추가, 제거 및 사용하거나 사용하지 않도록 설정하는 데 도움이 됩니다."
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: ade2bbc2-35a7-43c5-8001-4698f7254526
ms.service: traffic-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: kumud
ms.openlocfilehash: c80d104fc456849f8bfd5169dd8ce1361d906a65
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2017
---
# <a name="add-disable-enable-or-delete-endpoints"></a>끝점 추가, 사용 안 함, 사용 또는 삭제

Azure App Service의 Web Apps는 웹 사이트 모드에 관계없이 데이터 센터 내의 웹 사이트에 대해 이미 장애 조치(Failover) 및 라운드 로빈 트래픽 라우팅 기능을 제공합니다. Azure Traffic Manager를 통해 다른 데이터 센터의 웹 사이트와 클라우드 서비스에 대해 장애 조치(Failover) 및 라운드 로빈 트래픽 라우팅을 지정할 수 있습니다. 해당 기능을 제공하는 데 필요한 첫 번째 단계는 Traffic Manager에 클라우드 서비스 또는 웹 사이트 끝점을 추가하는 것입니다.

Traffic Manager 프로필의 일부인 개별 끝점을 사용하지 않도록 설정할 수도 있습니다. 끝점을 사용하지 않도록 설정하는 경우 프로필의 일부로 유지되지만 끝점이 없는 것처럼 프로필이 작동합니다. 이 작업은 유지 관리 모드에 있거나 다시 배포할 끝점을 일시적으로 제거하는 데 유용합니다. 끝점이 다시 작동하여 실행되면 사용하도록 설정할 수 있습니다.

> [!NOTE]
> 끝점을 사용하지 않도록 설정하는 경우 Azure의 끝점 배포 상태에는 영향을 주지 않습니다. 정상 끝점은 실행 상태로 유지되며 Traffic Manager에서 사용하지 않도록 설정된 경우에도 트래픽을 수신할 수 있습니다. 또한 한 프로필에서 끝점을 사용하지 않도록 설정해도 다른 프로필의 해당 끝점 상태에는 영향을 주지 않습니다.

## <a name="to-add-a-cloud-service-or-an-app-service-endpoint-to-a-traffic-manager-profile"></a>Traffic Manager 프로필에 클라우드 서비스 또는 App Service 끝점을 추가하려면

1. 브라우저에서 [Azure Portal](http://portal.azure.com)에 로그인합니다.
2. 포털의 검색 창에서 수정하려는 **Traffic Manager 프로필** 이름을 검색한 다음 표시되는 결과에서 Traffic Manager 프로필을 클릭합니다.
3. **Traffic Manager 프로필** 블레이드에서 **설정** 섹션의 **끝점**을 클릭합니다.
4. 표시되는 **끝점** 블레이드에서 **추가**를 클릭합니다.
5. **끝점 추가** 블레이드에서 다음과 같이 완료합니다.
    1. **형식**의 경우 **Azure 끝점**을 클릭합니다.
    2. 이 끝점을 인식하는 기준으로 사용할 **이름**을 제공합니다.
    3. **대상 리소스 형식**의 경우 드롭다운 목록에서 적절한 리소스 형식을 선택합니다.
    4. **대상 리소스**에서 **선택...**  선택기를 클릭하여 **리소스 블레이드**에서 동일한 구독 아래의 리소스에 나열합니다. 표시되는 **리소스** 블레이드에서 첫 번째 끝점으로 추가할 서비스를 선택합니다.
    5. **우선 순위**의 경우 **1**로 선택합니다. 이제 모든 트래픽이 정상일 경우 이 끝점으로 전송됩니다.
    6. **사용 안 함으로 추가**를 선택 취소 상태로 유지합니다.
    7. **확인**
6.  다음 Azure 끝점을 추가하려면 4, 5 단계를 반복합니다. **우선 순위** 값을 **2**로 설정한 상태에서 추가해야 합니다.
7.  두 끝점 추가가 완료되면 **온라인**인 모니터링 상태와 함께 **Traffic Manager 프로필** 블레이드에 표시됩니다.

> [!NOTE]
> *장애 조치* 트래픽 라우팅 방법을 사용하여 프로필에서 끝점을 추가하거나 제거한 후에는 장애 조치 우선 순위 목록을 원하는 방식으로 정렬할 수 없습니다. 대신 구성 페이지에서 장애 조치 우선 순위 목록의 순서를 조정할 수 있습니다. 자세한 내용은 [장애 조치(Failover) 트래픽 라우팅 구성](traffic-manager-configure-failover-routing-method.md)을 참조하세요.

## <a name="to-disable-an-endpoint"></a>끝점을 사용하지 않도록 설정하려면

1. 브라우저에서 [Azure Portal](http://portal.azure.com)에 로그인합니다.
2. 포털의 검색 창에서 수정하려는 **Traffic Manager 프로필** 이름을 검색한 다음 표시되는 결과에서 Traffic Manager 프로필을 클릭합니다.
3. **Traffic Manager 프로필** 블레이드에서 **설정** 섹션의 **끝점**을 클릭합니다. 
4. 사용하지 않으려는 끝점을 클릭한 다음 표시되는 **끝점** 블레이드에서 **편집**을 클릭합니다.
5. **끝점** 블레이드에서 끝점 상태를 **비활성화**로 변경하고 **저장**을 클릭합니다.
6. 클라이언트에서 트래픽을 TTL(활성 시간) 기간의 끝점으로 계속 보냅니다. Traffic Manager 프로필의 구성 페이지에서 TTL을 변경할 수 있습니다.

## <a name="to-enable-an-endpoint"></a>끝점을 사용하도록 설정하려면

1. 브라우저에서 [Azure Portal](http://portal.azure.com)에 로그인합니다.
2. 포털의 검색 창에서 수정하려는 **Traffic Manager 프로필** 이름을 검색한 다음 표시되는 결과에서 Traffic Manager 프로필을 클릭합니다.
3. **Traffic Manager 프로필** 블레이드에서 **설정** 섹션의 **끝점**을 클릭합니다. 
4. 사용하지 않으려는 끝점을 클릭한 다음 표시되는 **끝점** 블레이드에서 **편집**을 클릭합니다.
5. **끝점** 블레이드에서 끝점 상태를 **활성화**로 변경하고 **저장**을 클릭합니다.
6. 클라이언트에서 트래픽을 TTL(활성 시간) 기간의 끝점으로 계속 보냅니다. Traffic Manager 프로필의 구성 페이지에서 TTL을 변경할 수 있습니다.

## <a name="to-delete-an-endpoint"></a>끝점을 삭제하려면

1. 브라우저에서 [Azure Portal](http://portal.azure.com)에 로그인합니다.
2. 포털의 검색 창에서 수정하려는 **Traffic Manager 프로필** 이름을 검색한 다음 표시되는 결과에서 Traffic Manager 프로필을 클릭합니다.
3. **Traffic Manager 프로필** 블레이드에서 **설정** 섹션의 **끝점**을 클릭합니다. 
4. 사용하지 않으려는 끝점을 클릭한 다음 표시되는 **끝점** 블레이드에서 **편집**을 클릭합니다.
5. **끝점** 블레이드에서 끝점 상태를 **활성화**로 변경하고 **저장**을 클릭합니다.


## <a name="next-steps"></a>다음 단계

* [Traffic Manager 프로필 관리](traffic-manager-manage-profiles.md)
* [라우팅 방법 구성](traffic-manager-configure-routing-method.md)
* [Traffic Manager 성능 저하 상태 문제 해결](traffic-manager-troubleshooting-degraded.md)
* [Traffic Manager 성능 고려 사항](traffic-manager-performance-considerations.md)
* [Traffic Manager 작업(REST API 참조)](http://go.microsoft.com/fwlink/p/?LinkID=313584)

