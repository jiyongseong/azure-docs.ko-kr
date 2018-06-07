---
title: Azure Redis Cache에 redis-cli를 사용하는 방법 | Microsoft Docs
description: Azure Redis Cache에 redis-cli를 사용하는 방법에 대해 알아봅니다.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: wesmc
ms.openlocfilehash: facc3e2079c8f5e19266f5379762d71754a6ed84
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32182283"
---
# <a name="how-to-use-the-redis-command-line-tool-with-azure-redis-cache"></a>Azure Redis Cache에 Redis 명령줄 도구를 사용하는 방법

*redis-cli.exe*는 클라이언트로서 Redis Cache와 상호 작용하는 데 널리 사용되는 명령줄 도구입니다. 이 도구는 Azure Redis Cache에도 사용할 수 있습니다.

이 도구는 [Windows용 Redis 명령줄 도구](https://github.com/MSOpenTech/redis/releases/)를 다운로드하여 Windows 플랫폼에 대해 사용할 수 있습니다. 

다른 플랫폼에서 이 명령줄 도구를 실행하려면 [ http://redis.io/download ](https://redis.io/download)에서 Redis Cache 다운로드하세요.

## <a name="gather-cache-access-information"></a>캐시 액세스 정보 수집

캐시에 액세스하는 데 필요한 정보를 다음 세 가지 방법으로 수집할 수 있습니다.

1. Azure CLI([az redis list-keys](https://docs.microsoft.com/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys) 사용)
2. Azure PowerShell([Get-AzureRmRedisCacheKey](https://docs.microsoft.com/powershell/module/azurerm.rediscache/Get-AzureRmRedisCacheKey?view=azurermps-4.4.1) 사용)
3. Azure Portal 사용

이 섹션에서는 Azure Portal에서 키를 검색합니다.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>redis-cli.exe에 대한 액세스 사용

Azure Redis Cache를 사용하면 기본적으로 SSL 포트(6380)만 사용할 수 있습니다. `redis-cli.exe` 명령줄 도구는 SSL을 지원하지 않습니다. 두 가지 구성 옵션을 사용할 수 있습니다.

1. [비 SSL 포트(6379) 사용](cache-configure.md#access-ports) - **권장되지 않는 구성입니다**. 이 구성에서는 액세스 키가 일반 텍스트로 TCP를 통해 전송되기 문입니다. 이 변경으로 인해 캐시에 대한 액세스가 손상될 수 있습니다. 테스트 캐시에 액세스하는 경우에만 이 시나리오를 고려할 수 있습니다.

2. [stunnel](https://www.stunnel.org/downloads.html) 다운로드 및 설치 -

    **stunnel GUI Start**를 실행하여 서버를 시작합니다.

    작업 표시줄에서 stunnel 서버의 아이콘을 마우스 오른쪽 단추로 클릭하고 **로그 창 표시**를 클릭합니다.

    stunnel 로그 창 메뉴에서 **구성** > **구성 편집** 클릭하여 현재 구성 파일을 엽니다.

    **서비스 정의** 섹션 아래에서 *redis-cli.exe*에 대해 다음 항목을 추가합니다. `yourcachename` 대신 실제 캐시 이름을 삽입합니다. 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    구성 파일을 저장하고 닫습니다. 
  
    stunnel 로그 창 메뉴에서 **구성** > **구성 재로드**를 클릭합니다.


## <a name="connect-using-the-redis-command-line-tool"></a>Redis 명령줄 도구를 사용하여 연결합니다.

stunnel을 사용하는 경우 *redis-cli.exe*를 실행하고 *포트* 및 *액세스 키*(기본 또는 보조)만 전달하여 캐시에 연결합니다.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![stunnel과 redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

**보안되지 않는** 비 SSL 포트에 테스트 캐시를 사용하는 경우 `redis-cli.exe`를 실행하고 *호스트 이름*, *포트* 및 *액세스 키*(기본 또는 보조)를 전달하여 테스트 캐시에 연결합니다.

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnel과 redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>다음 단계

[Redis 콘솔](cache-configure.md#redis-console)을 사용하여 명령을 실행하는 방법에 대해 자세히 알아봅니다.

