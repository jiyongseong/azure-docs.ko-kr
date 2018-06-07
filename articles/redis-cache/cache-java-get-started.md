---
title: "Azure Redis Cache를 Java와 함께 사용하는 방법 | Microsoft 문서"
description: "Java를 사용하여 Azure Redis Cache를 시작합니다."
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 29275a5e-2e39-4ef2-804f-7ecc5161eab9
ms.service: cache
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 08/31/2017
ms.author: wesmc
ms.openlocfilehash: a93dbf78c9cb94f6c20c8569a69e068ceb3b98bc
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-use-azure-redis-cache-with-java"></a>Azure Redis Cache를 Java와 함께 사용하는 방법
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.JS](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Azure Redis Cache는 Microsoft에서 관리하는 전용 Redis Cache에 액세스할 수 있게 합니다. Microsoft Azure 내의 모든 응용 프로그램에서 캐시에 액세스할 수 있습니다.

이 항목에서는 Java를 사용하여 Azure Redis Cache를 시작하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건
[Jedis](https://github.com/xetorthio/jedis) - Redis용 Java 클라이언트

이 자습서에서는 Jedis를 사용하지만 [http://redis.io/clients](http://redis.io/clients)에 나열된 모든 Java 클라이언트를 사용할 수 있습니다.

## <a name="create-a-redis-cache-on-azure"></a>Azure에 Redis 캐시 만들기
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>호스트 이름 및 액세스 키 검색
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="connect-to-the-cache-securely-using-ssl"></a>SSL을 사용하여 안전하게 캐시에 연결
[jedis](https://github.com/xetorthio/jedis)의 최신 빌드는 SSL을 사용하여 Azure Redis Cache에 연결하도록 지원합니다. 다음 예제에서는 6380 SSL 끝점을 사용하여 Azure Redis Cache에 연결하는 방법을 보여줍니다. 이전의 [호스트 이름 및 액세스 키 검색](#retrieve-the-host-name-and-access-keys) 섹션에서 설명된 대로 `<name>`을(를) 캐시의 이름으로 `<key>`을(를) 기본 또는 보조 키로 교체합니다.

    boolean useSsl = true;
    /* In this line, replace <name> with your cache name: */
    JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6380, useSsl);
    shardInfo.setPassword("<key>"); /* Use your access key. */

> [!NOTE]
> 비 SSL 포트는 새 Azure Redis Cache 인스턴스에 대해 사용하지 않도록 설정되어 있습니다. SSL을 지원하지 않는 다른 클라이언트를 사용하는 경우 [비 SSL 포트를 사용하도록 설정하는 방법](cache-configure.md#access-ports)을 참조하세요.
> 
> 

## <a name="add-something-to-the-cache-and-retrieve-it"></a>캐시에 항목 추가 및 검색
    package com.mycompany.app;
    import redis.clients.jedis.Jedis;
    import redis.clients.jedis.JedisShardInfo;

    public class App
    {
      public static void main( String[] args )
      {
        boolean useSsl = true;
        /* In this line, replace <name> with your cache name: */
        JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6380, useSsl);
        shardInfo.setPassword("<key>"); /* Use your access key. */
        Jedis jedis = new Jedis(shardInfo);
        jedis.set("foo", "bar");
        String value = jedis.get("foo");
      }
    }


## <a name="next-steps"></a>다음 단계
* [캐시 진단을 사용](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics)하도록 설정하면 캐시의 상태를 [모니터링](https://msdn.microsoft.com/library/azure/dn763945.aspx)할 수 있습니다.
* 공식 [Redis 설명서](http://redis.io/documentation)를 읽어보세요.
* [Redis Cache를 사용하도록 Spring Initializer 앱을 구성하는 방법](cache-java-spring-boot-initializer-with-redis-cache.md)을 알아보세요.