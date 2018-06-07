---
title: "캐시 ASP.NET 출력 캐시 공급자"
description: "Azure Redis Cache를 사용하여 ASP.NET 페이지 출력 캐시하는 방법을 알아봅니다."
services: redis-cache
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: tysonn
ms.assetid: 78469a66-0829-484f-8660-b2598ec60fbf
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 02/14/2017
ms.author: wesmc
ms.openlocfilehash: 81c95949971d54833ca7a15ec5148116c94767f7
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2018
---
# <a name="aspnet-output-cache-provider-for-azure-redis-cache"></a>Azure Redis Cache에 대한 ASP.NET 출력 캐시 공급자
Redis 출력 캐시 공급자는 출력 캐시 데이터에 대한 out-of-process 저장소 메커니즘입니다. 이 데이터는 완전한 HTTP 응답(페이지 출력 캐싱)에 특별히 사용됩니다. 공급자가 ASP.NET 4에 도입된 새로운 출력 캐시 공급자 확장 포인트에 연결됩니다.

Redis 출력 캐시 공급자를 사용 하려면 먼저 캐시를 구성하고 Redis 출력 개시 공급자 NuGet 패키지를 사용하여 사용자의 ASP.NET 응용 프로그램을 구성합니다. 이 항목에서는 Redis 출력 캐시 공급자를 사용할 수 있도록 응용 프로그램을 구성하는 지침을 제공합니다. Azure Redis Cache 인스턴스 생성 및 구성에 대한 자세한 내용은 [캐시 생성하기](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)을 참조하세요.

## <a name="store-aspnet-page-output-in-the-cache"></a>ASP.NET 페이지 출력을 캐시에 저장
Redis Cache 세션 상태 NuGet 패키지를 사용하여 Visual Studio에서 클라이언트 응용 프로그램을 구성하려면 **도구** 메뉴에서 **NuGet 패키지 관리자**, **패키지 관리자 콘솔**을 클릭합니다.

`Package Manager Console` 창에서 다음 명령을 실행합니다.
    
```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Reids Output 캐시 공급자 NuGet 패키지는 StackExchange.Redis.StrongName 패키지에 종속성을 갖습니다. StackExchange.Redis.StrongName 패키지가 프로젝트에 나타나지 않는 경우 설치됩니다. Redis 출력 캐시 공급자 NuGet 패키지에 대한 자세한 내용은 [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) NuGet 패키지를 참조하세요.

>[!NOTE]
>강력한 이름의 StackExchange.Redis.StrongName 패키지 외에도 StackExchange.Redis 강력하지 않은 이름의 버전이 있습니다. 프로젝트가 StackExchange.Redis 강력하지 않은 이름의 버전을 사용하는 경우 해당 버전을 제거해야 합니다. 그렇지 않으면 프로젝트에서 이름이 충돌합니다. 이 패키지에 대한 자세한 내용은 [.NET 캐시 클라이언트 구성](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)을 참조하세요.
>
>

NuGet 패키지에서는 필수 어셈블리 참조를 다운로드하고 추가하며 web.config 파일에 다음 섹션을 추가합니다. 이 섹션에서는 Redis 출력 캐시 공급자를 사용하기 위해 ASP.NET 응용 프로그램에 필수 구성을 포함합니다.

```xml
<caching>
  <outputCachedefault Provider="MyRedisOutputCache">
    <providers>
      <!--
      <add name="MyRedisOutputCache"
        host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        databaseId = "0" [number]
        applicationName = "" [String]
        connectionTimeoutInMilliseconds = "5000" [number]
        operationTimeoutInMilliseconds = "5000" [number]
      />
      -->
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider" host="127.0.0.1" accessKey="" ssl="false"/>
    </providers>
  </outputCache>
</caching>
```

주석 처리된 섹션은 특성의 예와 각 특성의 샘플 설정을 제공합니다.

Microsoft Azure 포털의 캐시 블레이드에서 값으로 특성을 구성하고, 필요에 따라 다른 값을 구성합니다. 캐시 속성에 액세스 하는 방법은 [Redis 캐시 설정 구성](cache-configure.md#configure-redis-cache-settings)을 참조하세요.

* **호스트** – 캐시 끝점을 지정합니다.
* **포트** – ssl 설정에 따라 비-SSL 포트 또는 SSL 포트를 사용합니다.
* **선택키** – 캐시에 적합한 기본 또는 보조 키를 사용합니다.
* **ssl** – ssl로 캐시/클라이언트 통신을 보호하려는 경우 true가 되고, 그 외의 경우 false입니다. 올바른 포트를 지정해야 합니다.
  * 비 SSL 포트는 기본적으로 새 캐시에 대해 사용하지 않도록 설정됩니다. SSL 포트를 사용하여 설정에 대한 true를 지정합니다. 비-SSL 포트 사용 방법에 대한 자세한 내용은 [캐시 구성](cache-configure.md) 토픽의 [액세스 포트](cache-configure.md#access-ports) 섹션을 참조하세요.
* **databaseId** – 캐시 출력 데이터에 사용할 데이터베이스를 지정합니다. 지정하지 않으면 기본값 0이 사용됩니다.
* **applicationName** – `<AppName>_<SessionId>_Data`로 redis에 저장된 키입니다. 이 이름 지정 체계를 사용하면 여러 응용 프로그램이 동일한 키를 공유할 수 있습니다. 이 매개변수는 선택적이며 사용자가 제공하지 않으면 기본값이 사용됩니다.
* **connectionTimeoutInMilliseconds** – 이 설정은 StackExchange.Redis 클라이언트의 connectTimeout 설정을 무시할 수 있게 해줍니다. 지정하지 않으면 기본 connectTimeout 설정인 5000이 사용됩니다. 더 자세한 내용은 [StackExchange.Redis 구성 모델](http://go.microsoft.com/fwlink/?LinkId=398705)을 참조하세요.
* **operationTimeoutInMilliseconds** – 이 설정은 StackExchange.Redis 클라이언트의 syncTimeout 설정을 무시할 수 있게 해 줍니다. 지정하지 않으면 기본 syncTimeout 설정인 1000이 사용됩니다. 더 자세한 내용은 [StackExchange.Redis 구성 모델](http://go.microsoft.com/fwlink/?LinkId=398705)을 참조하세요.

출력을 캐시하고자 하는 각 페이지에 OutputCache 지시문을 추가합니다.

```
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

이전 예제에서는 캐시된 페이지 데이터가 캐시에 60초 동안 머물게 되며, 각 매개 변수 조합에 따라 페이지의 다른 버전이 캐시됩니다. 출력 캐시 지시문에 대한 자세한 내용은 [@OutputCache](http://go.microsoft.com/fwlink/?linkid=320837)을 참조하세요.

이 단계를 수행하면, 응용 프로그램은 Redis 출력 캐시 공급자를 사용하도록 구성됩니다.

## <a name="next-steps"></a>다음 단계
[Azure Redis Cache에 대한 ASP.NET 세션 상태 제공자](cache-aspnet-session-state-provider.md)를 확인하세요.

