---
title: "Azure Redis Cache를 Node.js와 함께 사용하는 방법 | Microsoft 문서"
description: "Node.js 및 node_redis를 사용하여 Azure Redis Cache를 시작합니다."
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: v-lincan
ms.assetid: 06fddc95-8029-4a8d-83f5-ebd5016891d9
ms.service: cache
ms.devlang: nodejs
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 02/10/2017
ms.author: wesmc
ms.openlocfilehash: e8b4818ad2b876ce4d55cf8aa2aa0e0162dea963
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-use-azure-redis-cache-with-nodejs"></a>Azure Redis Cache를 Node.js와 함께 사용하는 방법
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.JS](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Azure Redis Cache는 Microsoft에서 관리하는 안전한 전용 Redis Cache에 액세스할 수 있게 합니다. Microsoft Azure 내의 모든 응용 프로그램에서 캐시에 액세스할 수 있습니다.

이 항목에서는 Node.js를 사용하여 Azure Redis Cache를 시작하는 방법을 설명합니다. 

## <a name="prerequisites"></a>필수 조건
[node_redis](https://github.com/mranney/node_redis) 설치:

    npm install redis

이 자습서에서는 [node_redis](https://github.com/mranney/node_redis)를 사용합니다. 다른 Node.js 클라이언트를 사용한 예는 [Node.js Redis 클라이언트](http://redis.io/clients#nodejs)에 나열된 Node.js 클라이언트의 개별 설명서를 참조하세요.

## <a name="create-a-redis-cache-on-azure"></a>Azure에 Redis 캐시 만들기
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>호스트 이름 및 액세스 키 검색
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="connect-to-the-cache-securely-using-ssl"></a>SSL을 사용하여 안전하게 캐시에 연결
[node_redis](https://github.com/mranney/node_redis)의 최신 빌드는 SSL을 사용하여 Azure Redis Cache에 연결을 지원합니다. 다음 예제에서는 6380 SSL 끝점을 사용하여 Azure Redis Cache에 연결하는 방법을 보여줍니다. 이전의 [호스트 이름 및 액세스 키 검색](#retrieve-the-host-name-and-access-keys) 섹션에서 설명된 대로 `<name>`을(를) 캐시의 이름으로 `<key>`을(를) 기본 또는 보조 키로 교체합니다.

     var redis = require("redis");

      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});

> [!NOTE]
> 비 SSL 포트는 새 Azure Redis Cache 인스턴스에 대해 사용하지 않도록 설정되어 있습니다. SSL을 지원하지 않는 다른 클라이언트를 사용하는 경우 [비 SSL 포트를 사용하도록 설정하는 방법](cache-configure.md#access-ports)을 참조하세요.
> 
> 

## <a name="add-something-to-the-cache-and-retrieve-it"></a>캐시에 항목 추가 및 검색
다음 예제에서는 Azure Redis Cache 인스턴스에 연결하여 캐시의 항목을 저장하고 검색하는 방법을 보여줍니다. [node_redis](https://github.com/mranney/node_redis) 클라이언트가 포함된 Redis 사용에 관한 더 많은 예는 [http://redis.js.org/](http://redis.js.org/)를 참조하세요.

     var redis = require("redis");

      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});

    client.set("key1", "value", function(err, reply) {
            console.log(reply);
        });

    client.get("key1",  function(err, reply) {
            console.log(reply);
        });

출력

    OK
    value


## <a name="next-steps"></a>다음 단계
* [캐시 진단을 사용](cache-how-to-monitor.md#enable-cache-diagnostics)하도록 설정하면 캐시의 상태를 [모니터링](cache-how-to-monitor.md)할 수 있습니다.
* 공식 [Redis 설명서](http://redis.io/documentation)를 읽어보세요.

