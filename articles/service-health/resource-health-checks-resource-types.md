---
title: Azure Resource Health를 통해 지원되는 리소스 유형 | Microsoft Docs
description: Azure Resource Health를 통해 지원되는 리소스 유형
services: Resource health
documentationcenter: ''
author: BernardoAMunoz
manager: ''
editor: ''
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 10/09/2017
ms.author: BernardoAMunoz
ms.openlocfilehash: 3b4d99fe883cf52ca7f1ef98e70b7f3a1bccd5ae
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Azure Resource Health에서 리소스 유형 및 상태 검사
다음은 리소스 유형별 리소스 상태를 통해 실행되는 모든 검사 목록입니다.

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|실행된 검사|
|---|
|<ul><li>API Management 서비스가 가동되고 실행 중인가?</li></ul>|

## <a name="microsoftcacheredisredis"></a>Microsoft.CacheRedis/Redis
|실행된 검사|
|---|
|<ul><li>모든 캐시 노드가 작동 중인가?</li><li>데이터 센터 내에서 캐시에 도달할 수 있는가?</li><li>캐시가 최대 연결 수에 도달했는가?</li><li> 캐시가 사용 가능한 메모리를 모두 소모했는가? </li><li>캐시에 많은 수의 페이지 폴트가 있는가?</li><li>캐시에 부하가 큰가?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profile
|실행된 검사|
|---|
|<ul> <li>끝점이 중지, 제거 또는 잘못 구성되었는가?</li><li>CDN 구성 작업을 위해 보조 포털에 액세스할 수 있는가?</li><li>CDN 끝점에서 진행 중인 배달 문제가 있는가?</li><li>사용자가 CDN 리소스의 구성을 변경할 수 있는가?</li><li>구성 변경 내용이 예상된 속도로 전파되는가?</li><li>사용자가 Azure Portal, PowerShell 또는 API를 사용하여 CDN 구성을 관리할 수 있는가?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|실행된 검사|
|---|
|<ul><li>호스트 서버가 작동 중인가?</li><li>호스트 OS 부팅이 완료되었는가?</li><li>가상 컴퓨터 컨테이너가 프로비전되고 전원이 공급되는가?</li><li>호스트와 저장소 계정 간에 네트워크 연결이 되어 있는가?</li><li>게스트 OS의 부팅이 완료되었는가?</li><li>진행 중인 계획된 유지 관리가 있는가?</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/accounts
|실행된 검사|
|---|
|<ul><li>데이터 센터 내에서 계정에 도달할 수 있는가?</li><li>Cognitive Services 리소스 공급자를 사용할 수 있는가?</li><li>해당 지역에서 Cognitive Service를 사용할 수 있는가?</li><li>리소스 메타데이터가 있는 저장소 계정에서 읽기 작업을 수행할 수 있는가?</li><li>API 호출 할당량에 도달했는가?</li><li>API 호출 읽기 제한에 도달했는가?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.compute/virtualmachines
|실행된 검사|
|---|
|<ul><li>가상 머신을 호스팅하는 서버가 작동 중인가?</li><li>호스트 OS 부팅이 완료되었는가?</li><li>가상 컴퓨터 컨테이너가 프로비전되고 전원이 공급되는가?</li><li>호스트와 저장소 계정 간에 네트워크 연결이 되어 있는가?</li><li>게스트 OS의 부팅이 완료되었는가?</li><li>진행 중인 계획된 유지 관리가 있는가?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/accounts
|실행된 검사|
|---|
|<ul><li>사용자가 지역의 Data Lake Analytics에 작업을 제출할 수 있는가?</li><li>지역에서 기본 작업을 실행하여 성공적으로 완료되는가?</li><li>사용자가 지역에 카탈로그 항목을 나열할 수 있는가?</li>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/accounts
|실행된 검사|
|---|
|<ul><li>사용자가 지역의 Data Lake Store에 데이터를 업로드할 수 있는가?</li><li>사용자가 지역의 Data Lake Store에서 데이터를 다운로드할 수 있는가?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs

|실행된 검사|
|---|
|<ul><li>IoT Hub가 실행되고 있는가?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|실행된 검사|
|---|
|<ul><li>Azure Cosmos DB 서비스를 사용할 수 없어서 데이터베이스 또는 컬렉션 요청이 처리되지 않았는가?</li><li>Azure Cosmos DB 서비스를 사용할 수 없어서 문서 요청이 처리되지 않았는가?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.network/connections
|실행된 검사|
|---|
|<ul><li>VPN 터널이 연결되어 있는가?</li><li>연결에 구성 충돌이 있는가?</li><li>미리 공유한 키를 적절히 구성하였는가?</li><li>VPN 온-프레미스 장치에 연결할 수 있는가?</li><li>IPSec/IKE 보안 정책에 일치하지 않는 사항이 있는가?</li><li>S2S VPN 연결이 적절히 프로비전되었는가 아니면 실패한 상태인가?</li><li>VNET-VNET 연결이 적절히 프로비전되었는가 아니면 실패한 상태인가?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|실행된 검사|
|---|
|<ul><li>인터넷에서 VPN Gateway에 연결할 수 있는가?</li><li>VPN Gateway가 대기 모드인가?</li><li>VPN 서비스가 게이트웨이에서 실행 중인가?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/namespace
|실행된 검사|
|---|
|<ul><li> 네임스페이스에서 등록, 설치 또는 전송과 같은 런타임 작업을 수행 할 수 있는가?</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft.PowerBI/workspaceCollections
|실행된 검사|
|---|
|<ul><li>호스트 OS가 작동 중인가?</li><li>데이터 센터 외부에서 workspaceCollection에 연결할 수 있는가?</li><li>PowerBI 리소스 공급자를 사용할 수 있는가?</li><li>해당 지역에서 PowerBI 서비스를 사용할 수 있는가?</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|실행된 검사|
|---|
|<ul><li>클러스터에서 진단 작업을 수행할 수 있는가?</li></ul>|

## <a name="microsoftsqlserverdatabase"></a>Microsoft.SQL/Server/database
|실행된 검사|
|---|
|<ul><li> 데이터베이스에 로그인했는가?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|실행된 검사|
|---|
|<ul><li>작업이 실행 중인 모든 호스트가 작동 중인가?</li><li>작업을 시작할 수 없는가?</li><li>진행 중인 런타임 업데이트가 있는가?</li><li>작업이 예상된 상태인가(예: 실행 중 또는 고객에 의해 중지)?</li><li>작업에 메모리 부족 예외가 발생했는가?</li><li>진행 중인 예약된 계산 업데이트가 있는가?</li><li>실행 관리자(제어 계획)를 사용할 수 있는가?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|실행된 검사|
|---|
|<ul><li>호스트 서버가 작동 중인가?</li><li>인터넷 정보 서비스를 실행 중인가?</li><li>부하 분산 장치를 실행 중인가?</li><li>데이터 센터 내에서 App Service 계획에 도달할 수 있는가?</li><li>serverFarm에 대한 사이트 콘텐츠를 호스팅하는 저장소 계정이 제공되는가?</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.web/sites
|실행된 검사|
|---|
|<ul><li>호스트 서버가 작동 중인가?</li><li>인터넷 정보 서버를 실행 중인가?</li><li>부하 분산 장치를 실행 중인가?</li><li>데이터 센터 내에서 Web App에 도달할 수 있는가?</li><li>사이트 콘텐츠를 호스팅하는 저장소 계정이 제공되는가?</li></ul>|

# <a name="next-steps"></a>다음 단계
-  [Azure Service Health 대시보드 소개](service-health-overview.md) 및 [Azure Resource Health 소개](resource-health-overview.md)를 참조하여 자세한 내용을 확인합니다. 
-  [Azure Resource Health에 대한 질문과 대답](resource-health-faq.md)
- 상태 문제 알림을 받도록 경고를 설정합니다. 자세한 내용은 [Service Health 이벤트에 대한 경고 구성](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md)을 참조하세요. 
