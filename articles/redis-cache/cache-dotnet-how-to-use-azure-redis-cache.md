---
title: "Azure Redis Cache를 사용하는 방법 | Microsoft 문서"
description: "Azure Redis Cache를 사용하여 Azure 응용 프로그램의 성능을 향상시키는 방법을 알아봅니다."
services: redis-cache,app-service
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: c502f74c-44de-4087-8303-1b1f43da12d5
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 07/27/2017
ms.author: wesmc
ms.openlocfilehash: a9276eaa4c7d8b11891d7dfade475316ffac5f71
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-use-azure-redis-cache"></a>Azure Redis Cache 사용 방법
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.JS](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

이 가이드에서는 **Azure Redis Cache**를 사용하는 방법을 설명합니다. Microsoft Azure Redis Cache는 많이 사용되는 오픈 소스 Redis Cache를 기반으로 합니다. Microsoft에서 관리하는 안전한 전용 Redis Cache에 액세스할 수 있게 합니다. Azure Redis 캐시를 사용하여 만들어진 캐시는 Microsoft Azure 내의 모든 응용 프로그램에서 액세스할 수 있습니다.

Microsoft Azure Redis 캐시는 다음 계층에서 사용할 수 있습니다.

* **기본** – 단일 노드. 최대 53GB까지 여러 개의 크기
* **표준** – 2노드 주/복제본. 최대 53GB까지 여러 개의 크기 99.9% SLA
* **프리미엄** – 최대 10개 분할 데이터베이스와 2노드 주/복제본. 6GB ~ 530GB에 이르는 여러 개의 크기 모든 표준 계층 기능과 추가적인 [Redis 클러스터](cache-how-to-premium-clustering.md), [Redis 지속성](cache-how-to-premium-persistence.md) 및 [Azure Virtual Network](cache-how-to-premium-vnet.md) 지원이 포함됩니다. 99.9% SLA

각 계층은 기능과 가격이 다릅니다. 가격 책정에 대한 내용은 [캐시 가격 책정 정보][Cache Pricing Details]를 참조하세요.

이 가이드는 C\# 코드를 사용하는 [StackExchange.Redis][StackExchange.Redis] 클라이언트 사용 방법을 보여줍니다. 적용되는 시나리오에는 **캐시 만들기 및 구성**, **캐시 클라이언트 구성** 및 **캐시에서 개체 추가 및 제거** 등이 포함됩니다. Azure Redis Cache 사용에 대한 자세한 내용은 [다음 단계][Next Steps]를 참조하세요. Redis Cache를 사용하여 ASP.NET MVC 웹앱을 만드는 단계별 자습서는 [Redis Cache를 사용하여 웹앱을 만드는 방법](cache-web-app-howto.md)을 참조하세요.

<a name="getting-started-cache-service"></a>

## <a name="get-started-with-azure-redis-cache"></a>Azure Redis Cache 시작
Azure Redis 캐시를 시작하기는 쉽습니다. 먼저 캐시를 프로비전하고 구성합니다. 그런 다음 캐시에 액세스할 수 있도록 캐시 클라이언트를 구성합니다. 캐시 클라이언트를 구성하면 작업을 시작할 수 있습니다.

* [캐시 만들기][Create the cache]
* [캐시 클라이언트 구성][Configure the cache clients]

<a name="create-cache"></a>

## <a name="create-a-cache"></a>캐시 만들기
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

### <a name="to-access-your-cache-after-its-created"></a>캐시를 만든 후 액세스하려면
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

캐시 구성에 대한 자세한 내용은 [Azure Redis Cache를 구성하는 방법을](cache-configure.md)을 참조하세요.

<a name="NuGet"></a>

## <a name="configure-the-cache-clients"></a>캐시 클라이언트 구성
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

클라이언트 프로젝트의 캐싱을 구성했으면 캐시 작업에 대해 다음 섹션에서 설명하는 기술을 사용할 수 있습니다.

<a name="working-with-caches"></a>

## <a name="working-with-caches"></a>캐시 작업
이 섹션의 각 단계에서는 일반적인 캐시 작업 수행 방법에 대해 설명합니다.

* [캐시에 연결][Connect to the cache]
* [캐시에서 개체 추가 및 검색][Add and retrieve objects from the cache]
* [캐시의 .NET 개체 사용](#work-with-net-objects-in-the-cache)

<a name="connect-to-cache"></a>

## <a name="connect-to-the-cache"></a>캐시에 연결
프로그래밍 방식으로 캐시 작업을 하려면 캐시에 대한 참조가 필요합니다. StackExchange.Redis 클라이언트를 사용하여 Azure Redis Cache에 액세스하는 위치가 되는 임의의 파일 맨 위에 다음을 추가합니다.

    using StackExchange.Redis;

> [!NOTE]
> StackExchange.Redis 클라이언트를 사용하려면 .NET Framework 4 이상이 필요합니다.
> 
> 

Azure Redis Cache 연결은 `ConnectionMultiplexer` 클래스로 관리됩니다. 이 클래스는 클라이언트 응용 프로그램 전체에서 공유되고 다시 사용되어야 하며 작업별로 만들 필요가 없습니다. 

Azure Redis Cache에 연결하고 연결된 `ConnectionMultiplexer` 인스턴스가 반환되도록 하려면 정적 `Connect` 메서드를 호출하여 캐시 끝점 및 키에 전달합니다. Azure Portal에서 생성된 키를 암호 매개 변수로 사용합니다.

    ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

> [!IMPORTANT]
> 경고: 소스 코드에 자격 증명을 저장해서는 안 됩니다. 이 샘플을 단순하게 유지하기 위해 소스 코드로 표시합니다. [응용 프로그램 설정 및 연결 문자열 작동 방식][How Application Strings and Connection Strings Work](영문)에서 자격 증명 저장 방법에 대해 자세히 알아보세요.
> 
> 

SSL을 사용하지 않으려면 `ssl=false`를 설정하거나 `ssl` 매개 변수를 생략합니다.

> [!NOTE]
> 비 SSL 포트는 기본적으로 새 캐시에 대해 사용하지 않도록 설정됩니다. 비 SSL 포트를 사용하도록 설정하는 지침은 [포트 액세스](cache-configure.md#access-ports)를 참조하세요.
> 
> 

응용 프로그램의 `ConnectionMultiplexer` 인스턴스를 공유하는 방법은 다음 예제와 비슷하게 연결된 인스턴스를 반환하는 정적 속성을 갖는 것입니다. 이 방법은 스레드가 안전하도록 단일 연결된 `ConnectionMultiplexer` 인스턴스를 초기화하는 방법을 제공합니다. 이 예에서 `abortConnect`는 false로 설정되며 이것은 Azure Redis Cache에 연결이 설정되지 않은 경우에도 호출이 성공한다는 것을 의미합니다. `ConnectionMultiplexer`의 한 가지 주요 기능은 네트워크 문제 또는 다른 원인이 해결되면 캐시에 연결이 자동으로 복원된다는 점입니다.

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

고급 연결 구성 옵션에 대한 자세한 내용은 [StackExchange.Redis 구성 모델][StackExchange.Redis configuration model](영문)을 참조하세요.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

연결이 설정되고 나면 `ConnectionMultiplexer.GetDatabase` 메서드를 호출하여 Redis Cache 데이터베이스에 대한 참조를 반환합니다. `GetDatabase` 메서드에서 반환된 개체는 경량의 통과 개체이며 저장할 필요가 없습니다.

    // Connection refers to a property that returns a ConnectionMultiplexer
    // as shown in the previous example.
    IDatabase cache = Connection.GetDatabase();

    // Perform cache operations using the cache object...
    // Simple put of integral data types into the cache
    cache.StringSet("key1", "value");
    cache.StringSet("key2", 25);

    // Simple get of data types from the cache
    string key1 = cache.StringGet("key1");
    int key2 = (int)cache.StringGet("key2");

Azure Redis Cache에는 Redis 캐시 내에서 데이터를 논리적으로 구분하는 데 사용할 수 있는 여러 개의 구성 가능한 데이터베이스(기본 16개)가 있습니다. 자세한 내용은 [Redis 데이터베이스란?](cache-faq.md#what-are-redis-databases) 및 [기본 Redis 서버 구성](cache-configure.md#default-redis-server-configuration)을 참조하세요.

Azure Redis Cache 인스턴스에 연결하고 캐시 데이터베이스에 대한 참조를 반환하는 방법을 알아보았으며, 이제 캐시 작업에 대해 살펴보겠습니다.

<a name="add-object"></a>

## <a name="add-and-retrieve-objects-from-the-cache"></a>캐시에서 개체 추가 및 검색
`StringSet` 및 `StringGet` 메서드를 사용하여 캐시에 항목을 저장하고 캐시에서 항목을 검색할 수 있습니다.

    // If key1 exists, it is overwritten.
    cache.StringSet("key1", "value1");

    string value = cache.StringGet("key1");

Redis는 대부분의 데이터를 Redis 문자열로 저장하지만, 이 문자열은 캐시에 .NET 개체를 저장할 때 사용할 수 있는 직렬화된 이진 데이터를 포함하여 다양한 데이터 유형을 포함할 수 있습니다.

`StringGet` 호출 시 개체가 있으면 반환되고 없으면 `null`이 반환됩니다. `null`이 반환되면 원하는 데이터 원본에서 값을 검색하여 이후에 사용할 수 있게 캐시에 저장할 수 있습니다. 이런 사용 패턴을 캐시 배제 패턴이라고 합니다.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

다음 예제에 나온 것처럼 `RedisValue`를 사용할 수도 있습니다. `RedisValue`는 정수 데이터 형식을 작업하기 위한 암시적 연산자를 갖고 있으며, 캐시된 항목의 값으로 `null`이 예상되는 경우에 유용하게 사용할 수 있습니다.


    RedisValue value = cache.StringGet("key1");
    if (!value.HasValue)
    {
        value = GetValueFromDataSource();
        cache.StringSet("key1", value);
    }


캐시에서 항목의 만료를 지정하려면 `StringSet`의 `TimeSpan` 매개 변수를 사용합니다.

    cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

## <a name="work-with-net-objects-in-the-cache"></a>캐시의 .NET 개체 사용
Azure Redis Cache는 .NET 개체 및 기본 데이터 유형을 캐시할 수 있지만 .NET 개체를 캐시하려면 먼저 직렬화해야 합니다. .NET 개체 직렬화는 응용 프로그램 개발자의 책임이며 개발자는 유연하게 직렬 변환기를 선택할 수 있습니다.

개체를 직렬화하는 간단한 방법은 [Newtonsoft.Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/8.0.1-beta1)에서 `JsonConvert` 직렬화 방법을 사용하고 JSON 간에 직렬화하는 것입니다. 다음 예제에서는 `Employee` 개체 인스턴스를 사용하는 가져오기 및 설정을 보여줍니다.

    class Employee
    {
        public int Id { get; set; }
        public string Name { get; set; }

        public Employee(int EmployeeId, string Name)
        {
            this.Id = EmployeeId;
            this.Name = Name;
        }
    }

    // Store to cache
    cache.StringSet("e25", JsonConvert.SerializeObject(new Employee(25, "Clayton Gragg")));

    // Retrieve from cache
    Employee e25 = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e25"));

<a name="next-steps"></a>

## <a name="next-steps"></a>다음 단계
이제 기본 사항을 배웠으므로 다음 링크를 따라 Azure Redis Cache에 대해 알아보세요.

* Azure Redis Cache에 대한 ASP.NET 공급자를 확인합니다.
  * [Azure Redis 세션 상태 공급자](cache-aspnet-session-state-provider.md)
  * [Azure Redis Cache ASP.NET 출력 캐시 공급자](cache-aspnet-output-cache-provider.md)
* [캐시 진단을 사용](cache-how-to-monitor.md#enable-cache-diagnostics)하도록 설정하면 캐시의 상태를 [모니터링](cache-how-to-monitor.md)할 수 있습니다. Azure Portal에서 메트릭을 볼 수 있으며 선택한 도구를 사용하여 메트릭을 [다운로드 및 검토](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring)할 수도 있습니다.
* [StackExchange.Redis 캐시 클라이언트 설명서][StackExchange.Redis cache client documentation](영문)를 확인하세요.
  * Azure Redis Cache는 다양한 Redis 클라이언트와 개발 언어에서 액세스할 수 있습니다. 자세한 내용은 [http://redis.io/clients][http://redis.io/clients]를 참조하세요.
* Redsmin 및 Redis Desktop Manager와 같은 타사 서비스 및 도구와 함께 Azure Redis Cache를 사용할 수도 있습니다.
  * Redsmin에 대한 자세한 내용은 [Azure Redis 연결 문자열을 검색하고 Redsmin과 함께 사용하는 방법][How to retrieve an Azure Redis connection string and use it with Redsmin]을 참조하세요.
  * [RedisDesktopManager](https://github.com/uglide/RedisDesktopManager)를 사용하여 GUI가 포함된 Azure Redis Cache의 데이터에 액세스하고 해당 데이터를 검사합니다.
* [redis][redis](영문) 설명서를 참조하고 [redis 데이터 형식][redis data types](영문) 및 [Redis 데이터 형식에 대한 15분 소개][a fifteen minute introduction to Redis data types](영문)에 대해 읽어 보세요.

<!-- INTRA-TOPIC LINKS -->
[Next Steps]: #next-steps
[Introduction to Azure Redis Cache (Video)]: #video
[What is Azure Redis Cache?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Prepare Your Visual Studio Project to Use Azure Caching]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Get Started with Azure Redis Cache]: #getting-started-cache-service
[Create the cache]: #create-cache
[Configure the cache]: #enable-caching
[Configure the cache clients]: #NuGet
[Working with Caches]: #working-with-caches
[Connect to the cache]: #connect-to-cache
[Add and retrieve objects from the cache]: #add-object
[Specify the expiration of an object in the cache]: #specify-expiration
[Store ASP.NET session state in the cache]: #store-session


<!-- IMAGES -->


[StackExchangeNuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png

[NuGetMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png

[CacheProperties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png

[ManageKeys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png

[SessionStateNuGet]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png

[BrowseCaches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png

[Caches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png







<!-- LINKS -->
[http://redis.io/clients]: http://redis.io/clients
[Develop in other languages for Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[How to retrieve an Azure Redis connection string and use it with Redsmin]: https://redsmin.uservoice.com/knowledgebase/articles/485711-how-to-connect-redsmin-to-azure-redis-cache
[Azure Redis Session State Provider]: http://go.microsoft.com/fwlink/?LinkId=398249
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Session State Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Output Cache Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[Azure Caching]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[How to Configure Virtual Machine Sizes]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure Caching Capacity Planning Considerations]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure Caching]: http://go.microsoft.com/fwlink/?LinkId=252658
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Configure a cache in Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[StackExchange.Redis configuration model]: https://stackexchange.github.io/StackExchange.Redis/Configuration

[Work with .NET objects in the cache]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Cache Pricing Details]: http://www.windowsazure.com/pricing/details/cache/
[Azure portal]: https://portal.azure.com/

[Overview of Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=320830
[Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=398247

[Migrate to Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=317347
[Azure Redis Cache Samples]: http://go.microsoft.com/fwlink/?LinkId=320840
[Using Resource groups to manage your Azure resources]: ../azure-resource-manager/resource-group-overview.md

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[StackExchange.Redis cache client documentation]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[Redis data types]: http://redis.io/topics/data-types
[a fifteen minute introduction to Redis data types]: http://redis.io/topics/data-types-intro

[How Application Strings and Connection Strings Work]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/


