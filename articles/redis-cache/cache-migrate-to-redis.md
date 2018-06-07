---
title: "Redis로 Managed Cache Service 응용 프로그램 마이그레이션 - Azure | Microsoft Docs"
description: "Managed Cache Service 및 In-Role Cache 응용 프로그램을 Azure Redis Cache로 마이그레이션하는 방법에 대해 알아봅니다."
services: redis-cache
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: tysonn
ms.assetid: 041f077b-8c8e-4d7c-a3fc-89d334ed70d6
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 05/30/2017
ms.author: wesmc
ms.openlocfilehash: 0d52454ae1c2159814d4601d07259aba319e8598
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2018
---
# <a name="migrate-from-managed-cache-service-to-azure-redis-cache"></a>Managed Cache Service에서 Azure Redis Cache로 마이그레이션
Azure Managed Cache Service를 Azure Redis Cache에 사용하는 응용 프로그램을 마이그레이션하는 작업은 캐싱 응용 프로그램에서 사용하는 Managed Cache Service 기능에 따라 응용 프로그램을 최소한으로 변경하여 수행할 수 있습니다. API가 정확히 동일하지 않고 유사하며 캐시에 액세스하는 데 Managed Cache Service를 사용하는 기존 코드의 대부분은 변경을 최소화하면서 다시 사용할 수 있습니다. 이 항목에서는 필요한 구성을 만들고 응용 프로그램을 변경하여 Managed Cache Service 응용 프로그램이 Azure Redis Cache를 사용하도록 마이그레이션하는 방법을 보여 줍니다. 그리고 Azure Redis Cache의 기능 일부가 Managed Cache Service 캐시의 기능을 구현하는 데 사용될 수 있는 방법을 보여 줍니다.

>[!NOTE]
>Managed Cache Service와 In-Role Cache는 2016년 11월 30일에 [사용 중지](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)되었습니다. Azure Redis Cache로 마이그레이션하려는 In-Role Cache 배포가 있는 경우 이 문서의 단계를 따를 수 있습니다.

## <a name="migration-steps"></a>마이그레이션 단계
다음 단계에서는 Managed Cache Service 응용 프로그램을 마이그레이션하여 Azure Redis Cache를 사용해야 합니다.

* Managed Cache Service 기능에서 Azure Redis Cache로 매핑
* 캐시 제품 선택
* 캐시 만들기
* 캐시 클라이언트 구성
  * Managed Cache Service 구성 제거
  * StackExchange.Redis NuGet 패키지를 사용하여 캐시 클라이언트 구성
* Managed Cache Service 코드 마이그레이션
  * ConnectionMultiplexer 클래스를 사용하여 캐시에 연결
  * 캐시에 기본 데이터 형식 액세스
  * 캐시의 .NET 개체 사용
* Azure Redis Cache로 ASP.NET 세션 상태 및 출력 캐싱 마이그레이션 

## <a name="map-managed-cache-service-features-to-azure-redis-cache"></a>Managed Cache Service 기능에서 Azure Redis Cache로 매핑
Azure Managed Cache Service 및 Azure Redis Cache는 유사하지만 다른 방법으로 해당 기능 일부를 구현합니다. 이 섹션은 차이점 중 일부를 설명하고 Azure Redis Cache에서 Managed Cache Service의 기능을 구현하는 데 대한 지침을 제공합니다.

| Managed Cache Service 기능 | Managed Cache Service 지원 | Azure Redis Cache 지원 |
| --- | --- | --- |
| 이름이 지정된 캐시 |기본 캐시는 표준 및 프리미엄 캐시 제품에서 구성됩니다. 원하는 경우 최대 9개의 추가 명명된 캐시가 구성될 수 있습니다. |Azure Redis Cache에는 명명된 캐시와 유사한 기능을 구현하는 데 사용할 수 있는 여러 개의 구성 가능한 데이터베이스(기본 16개)가 있습니다. 자세한 내용은 [Redis 데이터베이스란?](cache-faq.md#what-are-redis-databases) 및 [기본 Redis 서버 구성](cache-configure.md#default-redis-server-configuration)을 참조하세요. |
| 고가용성 |표준 및 프리미엄 캐시 제품의 캐시에서 항목에 고가용성을 제공합니다. 항목이 오류로 인해 손실된 경우 여전히 캐시에서 항목의 백업 복사본을 사용할 수 있습니다. 보조 캐시에 대한 쓰기는 동기적으로 수행됩니다. |고가용성은 두 개의 노드 기본/복제본 구성(프리미엄 캐시의 각 분할에는 하나의 기본/복제본 쌍이 있음)이 있는 표준 및 프리미엄 캐시 제품에서 사용할 수 있습니다. 복제본에 대한 쓰기는 비동기적으로 수행됩니다. 자세한 내용은 [Azure Redis Cache 가격 책정](https://azure.microsoft.com/pricing/details/cache/)을 참조하세요. |
| 공지 |명명된 캐시에서 다양한 캐시 작업이 발생할 때 클라이언트가 비동기 알림을 받을 수 있습니다. |클라이언트 응용 프로그램은 Redis 게시/구독 또는 [Keyspace 알림](cache-configure.md#keyspace-notifications-advanced-settings) 을 사용하여 알림에 유사한 기능을 수행할 수 있습니다. |
| 로컬 캐시 |매우 빠른 액세스를 위해 클라이언트에서 캐시된 개체의 복사본을 로컬로 저장합니다. |클라이언트 응용 프로그램은 사전 또는 유사한 데이터 구조를 사용하여 이 기능을 구현해야 합니다. |
| 제거 정책 |없음 또는 LRU입니다. 기본 정책이 LRU입니다. |Azure Redis Cache는 다음의 제거 정책을 지원합니다. volatile-lru, allkeys-lru, volatile-random, allkeys-random, volatile-ttl, noeviction. 기본 정책이 volatile-lru입니다. 자세한 내용은 [기본 Redis 서버 구성](cache-configure.md#default-redis-server-configuration)을 참조하세요. |
| 만료 정책 |기본 만료 정책은 절대이며 기본 만료 시간은 10분입니다. 또한 슬라이딩 및 없음 정책을 사용할 수 있습니다. |기본적으로 캐시의 항목이 만료되지 않지만 만료는 캐시 집합 오버로드를 사용하여 쓰기 단위로 구성할 수 있습니다. 자세한 내용은 [캐시에서 개체 추가 및 검색](cache-dotnet-how-to-use-azure-redis-cache.md#add-and-retrieve-objects-from-the-cache)을 참조하세요. |
| 지역 및 태깅 |지역은 캐시된 항목에 대한 하위 그룹입니다. 또한 지역은 태그라는 추가 설명 문자열을 사용하여 캐시된 항목의 주석을 지원합니다. 지역은 해당 지역에서 태그가 지정된 항목에 검색 작업을 수행하는 기능을 지원합니다. 지역 내의 모든 항목은 캐시 클러스터의 단일 노드 내에 위치합니다. |Redis Cache는 단일 노드로 이루어지므로(Redis 클러스터를 사용하지 않는 한) Managed Cache Service 지역의 개념이 적용되지 않습니다. Redis는 키를 검색할 때 검색 및 와일드카드 작업을 지원하므로 설명 태그를 키 이름 내에 포함하고 나중에 항목을 검색하는 데 사용할 수 있습니다. Redis를 사용하는 태그 지정 솔루션을 구현하는 예는 [Redis로 태그를 지정하는 캐시 구현](http://stackify.com/implementing-cache-tagging-redis/)을 참조하세요. |
| 직렬화 |관리된 캐시는 NetDataContractSerializer, BinaryFormatter 및 사용자 지정 직렬 변환기의 사용을 지원합니다. 기본값은 NetDataContractSerializer입니다. |직렬 변환기의 선택은 클라이언트 응용 프로그램 개발자에게 맡겨지며 캐시에 두기 전에 .NET 개체를 직렬화하는 것은 클라이언트 응용 프로그램의 책임입니다. 자세한 내용 및 샘플 코드는 [캐시의 .NET 개체 작업](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache)을 참조하세요. |
| 캐시 에뮬레이터 |관리되는 캐시는 로컬 캐시 에뮬레이터를 제공합니다. |Azure Redis Cache에는 에뮬레이터가 없지만 [redis-server.exe의 MSOpenTech 빌드를 로컬로 실행](cache-faq.md#cache-emulator) 하여 에뮬레이터 환경을 제공할 수 있습니다. |

## <a name="choose-a-cache-offering"></a>캐시 제품 선택
Microsoft Azure Redis 캐시는 다음 계층에서 사용할 수 있습니다.

* **기본** – 단일 노드. 최대 53GB까지 여러 개의 크기
* **표준** – 2노드 주/복제본. 최대 53GB까지 여러 개의 크기 99.9% SLA
* **프리미엄** – 최대 10개 분할 데이터베이스와 2노드 주/복제본. 6GB ~ 530GB에 이르는 여러 개의 크기 모든 표준 계층 기능과 추가적인 [Redis 클러스터](cache-how-to-premium-clustering.md), [Redis 지속성](cache-how-to-premium-persistence.md) 및 [Azure Virtual Network](cache-how-to-premium-vnet.md) 지원이 포함됩니다. 99.9% SLA

각 계층은 기능과 가격이 다릅니다. 기능에 대해서는 이 가이드의 뒷부분에서 다룹니다. 가격에 대한 자세한 내용은 [캐시 가격 정보](https://azure.microsoft.com/pricing/details/cache/)를 참조하세요.

마이그레이션을 위한 출발점은 이전의 Managed Cache Service 캐시와 일치하는 크기를 선택하는 것입니다. 그런 다음 응용 프로그램의 요구 사항에 따라 크기를 확장하거나 축소합니다. 올바른 Azure Redis Cache 제품을 선택하는 데 대한 자세한 가이드는 [어떤 Redis Cache 제품 및 크기를 사용해야 하나요?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)를 참조하세요.

## <a name="create-a-cache"></a>캐시 만들기
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>캐시 클라이언트 구성
캐시를 만들고 구성하면 다음 단계에서는 Managed Cache Service 구성을 제거하고 추가 Azure Redis Cache 및 참조를 추가하므로 캐시 클라이언트가 캐시에 액세스할 수 있습니다.

* Managed Cache Service 구성 제거
* StackExchange.Redis NuGet 패키지를 사용하여 캐시 클라이언트 구성

### <a name="remove-the-managed-cache-service-configuration"></a>Managed Cache Service 구성 제거
클라이언트 응용 프로그램이 Azure Redis Cache에 대해 구성되기 전에 기존 Managed Cache Service 구성 및 어셈블리 참조는 Managed Cache Service NuGet 패키지의 설치를 취소하여 제거해야 합니다.

Managed Cache Service NuGet 패키지의 설치를 취소하려면 **솔루션 탐색기**에서 클라이언트 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다. **설치된 패키지**를 선택하고 검색 설치된 패키지 상자에 **WindowsAzure.Caching**을 입력합니다. **Windows** **Azure 캐시**(또는 NuGet 패키지의 버전에 따라 **Windows** **Azure 캐싱**)을 선택하고 **제거** 및 **닫기**를 차례로 클릭합니다.

![Azure Managed Cache Service NuGet 패키지 제거](./media/cache-migrate-to-redis/IC757666.jpg)

Managed Cache Service NuGet 패키지의 설치를 취소하면 클라이언트 응용 프로그램의 app.config 또는 web.config에서 Managed Cache Service 어셈블리 및 Managed Cache Service 항목을 제거합니다. NuGet 패키지를 제거하는 경우 일부 사용자 지정된 설정을 제거할 수 없기 때문에 web.config 또는 app.config를 열고 다음 요소가 완전히 제거되었는지 확인합니다.

`dataCacheClients` 항목이 `configSections` 요소에서 제거되도록 합니다. 전체 `configSections` 요소를 제거하지 마세요. 표시된 `dataCacheClients` 항목만 제거합니다.

```xml
<configSections>
  <!-- Existing sections omitted for clarity. -->
  <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
</configSections>
```

`dataCacheClients` 섹션이 제거되도록 합니다. `dataCacheClients` 섹션은 다음 예제와 비슷합니다.

```xml
<dataCacheClients>
  <dataCacheClientname="default">
    <!--To use the in-role flavor of Azure Cache, set identifier to be the cache cluster role name -->
    <!--To use the Azure Managed Cache Service, set identifier to be the endpoint of the cache cluster -->
    <autoDiscoverisEnabled="true"identifier="[Cache role name or Service Endpoint]"/>

    <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
    <!--Use this section to specify security settings for connecting to your cache. This section is not required if your cache is hosted on a role that is a part of your cloud service. -->
    <!--<securityProperties mode="Message" sslEnabled="true">
      <messageSecurity authorizationInfo="[Authentication Key]" />
    </securityProperties>-->
  </dataCacheClient>
</dataCacheClients>
```

Managed Cache Service 구성이 제거되면 다음 섹션에서 설명한 대로 캐시 클라이언트를 구성할 수 있습니다.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>StackExchange.Redis NuGet 패키지를 사용하여 캐시 클라이언트 구성
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Managed Cache Service 코드 마이그레이션
StackExchange.Redis 캐시 클라이언트에 대한 API는 Managed Cache Service와 유사합니다. 이 섹션에서는 차이점의 개요를 제공합니다.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>ConnectionMultiplexer 클래스를 사용하여 캐시에 연결
Managed Cache Service에서 캐시에 대한 연결은 `DataCacheFactory` 및 `DataCache` 클래스에서 처리됩니다. Azure Redis Cache에서 이러한 연결은 `ConnectionMultiplexer` 클래스로 관리됩니다.

캐시에 액세스하려는 파일의 상단에 다음 using 문을 추가합니다.

```csharp
using StackExchange.Redis
```

이 네임스페이스가 해결되지 않으면 [캐시 클라이언트 구성](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)에서 설명한 대로 StackExchange.Redis NuGet 패키지를 추가했는지 확인합니다.

> [!NOTE]
> StackExchange.Redis 클라이언트를 사용하려면 .NET Framework 4 이상이 필요합니다.
> 
> 

Azure Redis Cache 인스턴스에 연결하려면 정적 `ConnectionMultiplexer.Connect` 메서드를 호출하고 끝점 및 키에 전달합니다. 응용 프로그램의 `ConnectionMultiplexer` 인스턴스를 공유하는 방법은 다음 예제와 비슷하게 연결된 인스턴스를 반환하는 정적 속성을 갖는 것입니다. 스레드가 안전하도록 단일 연결된 `ConnectionMultiplexer` 인스턴스를 초기화하는 방법을 제공합니다. 이러한 예에서 `abortConnect` 은 false로 설정되며 이는 캐시에 연결이 설정되지 않은 경우에도 호출이 성공한다는 사실을 의미합니다. `ConnectionMultiplexer` 의 한 가지 주요 기능은 연결 네트워크 문제 또는 다른 원인이 해결되면 캐시에 연결이 자동으로 복원된다는 점입니다.

```csharp
private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
{
    return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
});

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

캐시 끝점, 키 및 포트는 캐시 인스턴스에 대한 **Redis Cache** 블레이드에서 가져올 수 있습니다. 자세한 내용은 [Redis Cache 속성](cache-configure.md#properties)을 참조하세요.

연결이 설정되고 나면 `ConnectionMultiplexer.GetDatabase` 메서드를 호출하여 Redis Cache 데이터베이스에 대한 참조를 반환합니다. `GetDatabase` 메서드에서 반환된 개체는 경량의 통과 개체이며 저장할 필요가 없습니다.

```csharp
IDatabase cache = Connection.GetDatabase();

// Perform cache operations using the cache object...
// Simple put of integral data types into the cache
cache.StringSet("key1", "value");
cache.StringSet("key2", 25);

// Simple get of data types from the cache
string key1 = cache.StringGet("key1");
int key2 = (int)cache.StringGet("key2");
```

StackExchange.Redis 클라이언트는 캐시의 항목에 액세스하고 저장하는 데 `RedisKey` 및 `RedisValue` 형식을 사용합니다. 이러한 형식은 문자열을 포함하여 가장 기본적인 언어 형식에 매핑되며 직접 사용되는 경우가 드뭅니다. Redis 문자열은 가장 기본적인 종류의 Redis 값이며 직렬화된 이진 스트림을 비롯한 다양한 형식의 데이터를 포함할 수 있습니다. 이 형식을 직접 사용하지 않는 반면 이름에 `String`을 포함하는 메서드를 사용합니다. 가장 기본적인 데이터 형식의 경우 캐시에 컬렉션 또는 기타 Redis 데이터 형식을 저장하지 않는 한 `StringSet` 및 `StringGet` 메서드를 사용하여 캐시에서 항목을 저장하고 검색합니다. 

`StringSet` 및 `StringGet`는 Managed Cache Service `Put` 및 `Get`와 매우 유사합니다. 이러한 결과를 가져오는 한 가지 주요 차이점은 캐시에 .NET 개체를 설정하고 가져오기 전에 먼저 직렬화해야 한다는 것입니다. 

`StringGet` 호출 시 개체가 있으면 반환되고 없으면 null이 반환됩니다. 이 경우에는 원하는 데이터 소스에서 값을 검색하여 이후에 사용할 수 있게 캐시에 저장할 수 있습니다. 이를 캐시 배제 패턴이라고 합니다.

캐시에서 항목의 만료를 지정하려면 `StringSet`의 `TimeSpan` 매개 변수를 사용합니다.

```csharp
cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));
```

Azure Redis Cache는 .NET 개체 및 기본 데이터 형식으로 작업할 수 있지만 .NET 개체를 캐시하려면 먼저 직렬화해야 합니다. 이것은 응용 프로그램 개발자의 책임입니다. 직렬 변환기를 선택할 때 개발자에게 융통성을 제공합니다. 자세한 내용 및 샘플 코드는 [캐시의 .NET 개체 작업](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache)을 참조하세요.

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-redis-cache"></a>Azure Redis Cache로 ASP.NET 세션 상태 및 출력 캐싱 마이그레이션
Azure Redis Cache에는 ASP.NET 세션 상태 및 페이지 출력 캐싱 모두에 공급자가 있습니다. Managed Cache Service 버전의 이러한 공급자를 사용하는 응용 프로그램을 마이그레이션하려면 먼저 web.config에서 기존 섹션을 제거하고 Azure Redis Cache 버전의 공급자를 구성합니다. Azure Redis Cache ASP.NET 공급자를 사용하는 데 대한 지침은 [Azure Redis Cache에 대한 ASP.NET 세션 상태 제공자](cache-aspnet-session-state-provider.md) 및 [Azure Redis Cache에 대한 ASP.NET 출력 캐시 공급자](cache-aspnet-output-cache-provider.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
자습서, 샘플, 비디오, 등은 [Azure Redis Cache 설명서](https://azure.microsoft.com/documentation/services/cache/) 를 탐색합니다.

