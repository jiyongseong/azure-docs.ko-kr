---
title: Azure PowerShell을 사용하여 Azure Redis Cache 관리 | Microsoft Docs
description: Azure PowerShell을 사용하여 Azure Redis Cache에 대한 관리 작업을 수행하는 방법을 알아봅니다.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 1136efe5-1e33-4d91-bb49-c8e2a6dca475
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: wesmc
ms.openlocfilehash: 38b2f57811b0e952d3020c06d39350918f2f0391
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="manage-azure-redis-cache-with-azure-powershell"></a>Azure PowerShell을 사용하여 Azure Redis Cache 관리
> [!div class="op_single_selector"]
> * [PowerShell](cache-howto-manage-redis-cache-powershell.md)
> * [Azure CLI](cache-manage-cli.md)
> 
> 

이 항목에서는 Azure Redis Cache 인스턴스 만들기, 업데이트 및 크기 조정과 같은 일반적인 작업을 수행하고 액세스 키를 다시 생성하며 캐시에 대한 정보를 보는 방법을 보여줍니다. Azure Redis Cache PowerShell cmdlet의 전체 목록은 [Azure Redis Cache cmdlet](https://msdn.microsoft.com/library/azure/mt634513.aspx)을 참조하세요.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

두 배포 모델에 대한 자세한 내용은 [Azure Resource Manager 및 클래식 배포: 배포 모델 및 리소스 상태 이해](../azure-resource-manager/resource-manager-deployment-model.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건
Azure PowerShell을 이미 설치한 경우 Azure PowerShell 버전 1.0.0 이상이 있어야 합니다. Azure PowerShell 명령 프롬프트에서 다음 명령을 사용하여 설치한 Azure PowerShell의 버전을 확인할 수 있습니다.

    Get-Module azure | format-table version


먼저 다음 명령을 사용하여 Azure에 로그인해야 합니다.

    Connect-AzureRmAccount

Microsoft Azure 로그인 대화 상자에서 Azure 계정의 전자 메일 주소 및 해당 암호를 지정합니다.

다음으로, 여러 Azure 구독이 있는 경우 Azure 구독을 설정해야 합니다. 현재 구독 목록을 보려면 다음 명령을 실행합니다.

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

구독을 지정하려면 다음 명령을 실행합니다. 다음 예제에서 구독 이름은 `ContosoSubscription`입니다.

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

Azure 리소스 관리자에서 Windows PowerShell을 사용하려면 다음이 필요합니다.

* Windows PowerShell, 버전 3.0 또는 4.0. Windows PowerShell 버전을 확인하려면 `$PSVersionTable`을 입력하고 `PSVersion` 값이 3.0 또는 4.0인지를 확인합니다. 호환 버전을 설치하려면 [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) 또는 [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)을 참조하세요.

이 자습서에 나오는 cmdlet에 대한 자세한 도움말을 보려면 Get-Help cmdlet을 사용합니다.

    Get-Help <cmdlet-name> -Detailed

예를 들어 `New-AzureRmRedisCache` cmdlet에 대한 도움말을 보려면 다음과 같이 입력합니다.

    Get-Help New-AzureRmRedisCache -Detailed

### <a name="how-to-connect-to-other-clouds"></a>다른 클라우드에 연결하는 방법
기본적으로 Azure 환경은 글로벌 Azure 클라우드 인스턴스를 나타내는 `AzureCloud`입니다. 다른 인스턴스에 연결하려면 원하는 환경 또는 환경 이름을 사용하여 `-Environment` 또는 -`EnvironmentName` 명령줄 스위치와 함께 `Connect-AzureRmAccount` 명령을 사용합니다.

사용 가능한 환경 목록을 보려면 `Get-AzureRmEnvironment` cmdlet을 실행합니다.

### <a name="to-connect-to-the-azure-government-cloud"></a>Azure Government 클라우드를 연결하려면
Azure Government 클라우드를 연결하려면 다음 명령 중 하나를 사용합니다.

    Connect-AzureRmAccount -EnvironmentName AzureUSGovernment

또는

    Connect-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureUSGovernment)

Azure Government 클라우드 내에 캐시를 만들려면 다음 위치 중 하나를 사용합니다.

* USGov 버지니아
* 미국 정부 아이오와

Azure Government 클라우드에 대한 자세한 내용은 [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) 및 [Microsoft Azure Government 개발자 가이드](../azure-government-developer-guide.md)를 참조하세요.

### <a name="to-connect-to-the-azure-china-cloud"></a>Azure 중국 클라우드에 연결하려면
Azure 중국 클라우드에 연결하려면 다음 명령 중 하나를 사용합니다.

    Connect-AzureRmAccount -EnvironmentName AzureChinaCloud

또는

    Connect-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureChinaCloud)

Azure 중국 클라우드에서 캐시를 만들려면 다음 위치 중 하나를 사용합니다.

* 중국 동부
* 중국 북부

Azure 중국 클라우드에 대한 자세한 내용은 [중국 21Vianet에서 운영하는 Azure용 AzureChinaCloud](http://www.windowsazure.cn/)를 참조하세요.

### <a name="to-connect-to-microsoft-azure-germany"></a>Microsoft Azure Germany에 연결하려면
Microsoft Azure Germany에 연결하려면 다음 명령 중 하나를 사용합니다.

    Connect-AzureRmAccount -EnvironmentName AzureGermanCloud


또는

    Connect-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureGermanCloud)

Microsoft Azure Germany에서 캐시를 만들려면 다음 위치 중 하나를 사용합니다.

* 독일 중부
* 독일 북동부

Microsoft Azure Germany에 대한 자세한 내용은 [Microsoft Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)를 참조하세요.

### <a name="properties-used-for-azure-redis-cache-powershell"></a>Azure Redis Cache PowerShell에 사용되는 속성
다음 표에서는 Azure PowerShell을 사용하여 Azure Redis Cache 인스턴스를 만들고 관리할 때 자주 사용되는 매개 변수에 대한 속성 및 설명을 포함합니다.

| 매개 변수 | 설명 | 기본값 |
| --- | --- | --- |
| Name |캐시의 이름 | |
| 위치 |캐시의 위치 | |
| ResourceGroupName |캐시를 만들 리소스 그룹 이름 | |
| 크기 |캐시의 크기. 유효한 값: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB |1GB |
| ShardCount |클러스터링을 사용하는 프리미엄 캐시를 만들 때 만들 분할된 데이터베이스 수. 유효한 값: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 | |
| SKU |캐시의 SKU를 지정합니다. 유효한 값: 기본, 표준, 프리미엄 |Standard |
| RedisConfiguration |Redis 구성 설정을 지정합니다. 각 설정에 대한 자세한 내용은 다음 [RedisConfiguration 속성](#redisconfiguration-properties) 테이블을 참조하세요. | |
| EnableNonSslPort |비 SSL 포트를 사용하는지 여부를 나타냅니다. |False |
| MaxMemoryPolicy |이 매개 변수는 더 이상 사용되지 않으며 대신 RedisConfiguration을 사용합니다. | |
| StaticIP |VNET에서 캐시를 호스팅하는 경우 서브넷에서 캐시에 대한 고유 IP 주소를 지정합니다. 제공되지 않으면 하나의 IP 주소가 서브넷에서 자동으로 선택됩니다. | |
| 서브넷 |VNET에서 캐시를 호스팅하는 경우에 캐시를 배포할 서브넷의 이름을 지정합니다. | |
| VirtualNetwork |VNET에서 캐시를 호스팅하는 경우에 캐시를 배포할 VNET의 리소스 ID를 지정합니다. | |
| KeyType |액세스 키를 갱신할 때 다시 생성할 액세스 키를 지정합니다. 유효한 값: 주, 보조 | |

### <a name="redisconfiguration-properties"></a>RedisConfiguration 속성
| 자산 | 설명 | 가격 책정 계층 |
| --- | --- | --- |
| rdb-backup-enabled |[Redis 데이터 지속성](cache-how-to-premium-persistence.md) 사용 여부 |프리미엄 전용 |
| rdb-storage-connection-string |[Redis 데이터 지속성](cache-how-to-premium-persistence.md) |프리미엄 전용 |
| rdb-backup-frequency |[Redis 데이터 지속성](cache-how-to-premium-persistence.md) |프리미엄 전용 |
| maxmemory-reserved |비 캐시 프로세스를 위해 [예약되는 메모리](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) 를 구성합니다 |표준 및 프리미엄 |
| maxmemory-policy |캐시에 대한 [제거 정책](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) 을 구성합니다 |모든 가격 책정 계층 |
| notify-keyspace-events |[Keyspace 알림](cache-configure.md#keyspace-notifications-advanced-settings) |표준 및 프리미엄 |
| hash-max-ziplist-entries |작은 집계 데이터 형식에 대한 [메모리 최적화](http://redis.io/topics/memory-optimization) 구성 |표준 및 프리미엄 |
| hash-max-ziplist-value |작은 집계 데이터 형식에 대한 [메모리 최적화](http://redis.io/topics/memory-optimization) 구성 |표준 및 프리미엄 |
| set-max-intset-entries |작은 집계 데이터 형식에 대한 [메모리 최적화](http://redis.io/topics/memory-optimization) 구성 |표준 및 프리미엄 |
| zset-max-ziplist-entries |작은 집계 데이터 형식에 대한 [메모리 최적화](http://redis.io/topics/memory-optimization) 구성 |표준 및 프리미엄 |
| zset-max-ziplist-value |작은 집계 데이터 형식에 대한 [메모리 최적화](http://redis.io/topics/memory-optimization) 구성 |표준 및 프리미엄 |
| 데이터베이스 |데이터베이스 수를 구성합니다. 이 속성은 캐시 만들기에서만 구성할 수 있습니다. |표준 및 프리미엄 |

## <a name="to-create-a-redis-cache"></a>Redis Cache를 만들려면
[New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) cmdlet을 사용하여 새 Azure Redis Cache 인스턴스를 만듭니다.

> [!IMPORTANT]
> Azure 포털을 사용하여 구독에 처음으로 Redis 캐시를 만들 때 포털은 해당 구독에 대해 `Microsoft.Cache` 네임스페이스를 등록합니다. PowerShell을 사용하여 구독에서 첫 번째 Redis 캐시를 만드는 경우, 먼저 다음 명령을 사용하여 해당 네임스페이스를 등록해야 하며 그렇지 않은 경우 `New-AzureRmRedisCache` 및 `Get-AzureRmRedisCache`의 cmdlet이 실패합니다.
> 
> `Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Cache"`
> 
> 

`New-AzureRmRedisCache`에 대해 사용 가능한 매개 변수 및 해당 설명에 대한 목록을 보려면 다음 명령을 실행합니다.

    PS C:\> Get-Help New-AzureRmRedisCache -detailed

    NAME
        New-AzureRmRedisCache

    SYNOPSIS
        Creates a new redis cache.


    SYNTAX
        New-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Location <String> [-RedisVersion <String>]
        [-Size <String>] [-Sku <String>] [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort
        <Boolean>] [-ShardCount <Integer>] [-VirtualNetwork <String>] [-Subnet <String>] [-StaticIP <String>]
        [<CommonParameters>]


    DESCRIPTION
        The New-AzureRmRedisCache cmdlet creates a new redis cache.


    PARAMETERS
        -Name <String>
            Name of the redis cache to create.

        -ResourceGroupName <String>
            Name of resource group in which to create the redis cache.

        -Location <String>
            Location in which to create the redis cache.

        -RedisVersion <String>
            RedisVersion is deprecated and will be removed in future release.

        -Size <String>
            Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value, databases.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Redis Cache. If no value is provided, the default value is false and the
            non-SSL port will be disabled. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        -VirtualNetwork <String>
            The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{
            subid}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicNetwork/VirtualNetworks/{vnetName}

        -Subnet <String>
            Required when deploying a redis cache inside an existing Azure Virtual Network.

        -StaticIP <String>
            Required when deploying a redis cache inside an existing Azure Virtual Network.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

기본 매개 변수로 캐시를 만들려면 다음 명령을 실행합니다.

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"

`ResourceGroupName`, `Name` 및 `Location`은 필수 매개 변수이지만 나머지는 선택 사항이며 기본값을 포함합니다. 이전 명령을 실행하면 지정된 이름, 위치 및 리소스 그룹으로 크기 1GB의 표준 SKU Azure Redis Cache 인스턴스가 비 SSL 포트가 비활성화된 상태로 만들어집니다. 

프리미엄 캐시를 만들려면 P1(6GB - 60GB), P2(13GB - 130GB), P3(26GB - 260GB) 또는 P4(53GB - 530GB) 크기를 지정합니다. 클러스터링을 사용하려면 `ShardCount` 매개 변수를 사용하여 분할된 데이터베이스 수를 지정합니다. 다음 예제에서는 3개의 분할된 데이터베이스가 있는 P1 프리미엄 캐시를 만듭니다. P1 프리미엄 캐시는 6GB 크기이고 3개의 분할된 데이터베이스를 지정했으므로 총 크기는 18GB(3 x 6GB)입니다.

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3

`RedisConfiguration` 매개 변수에 대한 값을 지정하려면 `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}`처럼 키/값 쌍으로 값을 `{}`로 묶습니다. 다음 예제에서는 `allkeys-random` 최대 정책을 사용하고 `KEA`의 keyspace 알림이 구성된 표준 1GB 캐시를 만듭니다. 자세한 내용은 [Keyspace 알림(고급 설정)](cache-configure.md#keyspace-notifications-advanced-settings) 및 [메모리 정책](cache-configure.md#memory-policies)을 참조하세요.

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}

<a name="databases"></a>

## <a name="to-configure-the-databases-setting-during-cache-creation"></a>캐시를 만드는 동안 데이터베이스 설정을 구성하려면
`databases` 설정은 캐시를 만드는 동안에만 구성할 수 있습니다. 다음 예제에서는 [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) cmdlet를 사용하여 48 데이터베이스로 프리미엄 P3 (26 GB) 캐시를 만듭니다.

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}

`databases` 속성에 대한 자세한 내용은 [기본 Azure Redis Cache 서버 구성](cache-configure.md#default-redis-server-configuration)을 참조하세요. [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) cmdlet를 사용하여 캐시를 만드는 방법에 대한 자세한 내용은 이전의 [Redis 캐시 만들려면](#to-create-a-redis-cache) 섹션을 참조하세요.

## <a name="to-update-a-redis-cache"></a>Redis Cache를 업데이트하려면
[Set-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634518.aspx) cmdlet을 사용하여 Azure Redis Cache 인스턴스를 업데이트합니다.

`Set-AzureRmRedisCache`에 대해 사용 가능한 매개 변수 및 해당 설명에 대한 목록을 보려면 다음 명령을 실행합니다.

    PS C:\> Get-Help Set-AzureRmRedisCache -detailed

    NAME
        Set-AzureRmRedisCache

    SYNOPSIS
        Set redis cache updatable parameters.

    SYNTAX
        Set-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Size <String>] [-Sku <String>]
        [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort <Boolean>] [-ShardCount
        <Integer>] [<CommonParameters>]

    DESCRIPTION
        The Set-AzureRmRedisCache cmdlet sets redis cache parameters.

    PARAMETERS
        -Name <String>
            Name of the redis cache to update.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -Size <String>
            Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Redis Cache. The default value is null and no change will be made to the
            currently configured value. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

`Size`, `Sku`, `EnableNonSslPort`의 속성과 `RedisConfiguration` 값을 업데이트하는 데 `Set-AzureRmRedisCache` cmdlet을 사용할 수 있습니다. 

다음 명령은 myCache라는 Redis Cache에 대한 maxmemory-policy를 업데이트합니다.

    Set-AzureRmRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}

<a name="scale"></a>

## <a name="to-scale-a-redis-cache"></a>Redis Cache의 크기를 조정하려면
`Size`, `Sku` 또는 `ShardCount` 속성이 수정될 때 Azure Redis Cache 인스턴스 크기를 조정하는 데 `Set-AzureRmRedisCache`를 사용할 수 있습니다. 

> [!NOTE]
> PowerShell을 사용하여 캐시 크기를 조정할 경우 Azure 포털에서 캐시 크기를 조정할 때와 동일한 한도 및 지침이 적용됩니다. 다른 가격 책정 계층으로 크기를 조정할 수 있지만 다음과 같은 제한 사항이 있습니다.
> 
> * 높은 가격 책정 계층에서 낮은 가격 책정 계층으로 크기를 조정할 수 없습니다.
> * **프리미엄** 캐시에서 **표준** 또는 **기본** 캐시로 축소할 수 없습니다.
> * **표준** 캐시에서 **기본** 캐시로 축소할 수 없습니다.
> * **기본** 캐시에서 **표준** 캐시로 크기를 조정할 수 있지만 동시에 크기를 변경할 수는 없습니다. 다른 크기가 필요한 경우 후속 크기 조정 작업을 통해 원하는 크기로 조정할 수 있습니다.
> * **기본** 캐시에서 바로 **프리미엄** 캐시로 확장할 수 없습니다. 크기 조정 작업을 통해 **기본**에서 **표준**으로 확장한 다음, 후속 크기 조정 작업을 통해 **표준**에서 **프리미엄**으로 확장해야 합니다.
> * 더 큰 크기에서 **C0(250MB)** 크기로 축소할 수 없습니다.
> 
> 자세한 내용은 [Azure Redis Cache 크기를 조정하는 방법](cache-how-to-scale.md)을 참조하세요.
> 
> 

다음 예제에서는 `myCache` 라는 캐시를 2.5GB 캐시로 크기를 조정하는 방법을 보여 줍니다. 이 명령은 기본 또는 표준 캐시 둘 다에 적용할 수 있습니다.

    Set-AzureRmRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

이 명령을 실행하면 캐시 상태가 반환됩니다( `Get-AzureRmRedisCache`호출과 유사). 여기서 `ProvisioningState`는 `Scaling`입니다.

    PS C:\> Set-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup -Size 2.5GB


    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/mygroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Scaling
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 150], [notify-keyspace-events, KEA],
                         [maxmemory-delta, 150]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : mygroup
    PrimaryKey         : ....
    SecondaryKey       : ....
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

크기 조정 작업이 완료되면 `ProvisioningState`는 `Succeeded`로 바뀝니다. 기본에서 표준으로 변경 후 크기 변경과 같은 후속 크기 조정 작업을 수행해야 하는 경우 이전 작업이 완료될 때까지 대기해야 하며 그렇지 않은 경우 다음과 같은 오류가 발생합니다.

    Set-AzureRmRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.

## <a name="to-get-information-about-a-redis-cache"></a>Redis Cache에 대한 정보를 가져오려면
[Get-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634514.aspx) cmdlet을 사용하여 캐시에 대한 정보를 검색할 수 있습니다.

`Get-AzureRmRedisCache`에 대해 사용 가능한 매개 변수 및 해당 설명에 대한 목록을 보려면 다음 명령을 실행합니다.

    PS C:\> Get-Help Get-AzureRmRedisCache -detailed

    NAME
        Get-AzureRmRedisCache

    SYNOPSIS
        Gets details about a single cache or all caches in the specified resource group or all caches in the current
        subscription.

    SYNTAX
        Get-AzureRmRedisCache [-Name <String>] [-ResourceGroupName <String>] [<CommonParameters>]

    DESCRIPTION
        The Get-AzureRmRedisCache cmdlet gets the details about a cache or caches depending on input parameters. If both
        ResourceGroupName and Name parameters are provided then Get-AzureRmRedisCache will return details about the
        specific cache name provided.

        If only ResourceGroupName is provided than it will return details about all caches in the specified resource group.

        If no parameters are given than it will return details about all caches the current subscription.

    PARAMETERS
        -Name <String>
            The name of the cache. When this parameter is provided along with ResourceGroupName, Get-AzureRmRedisCache
            returns the details for the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache or caches. If ResourceGroupName is provided with Name
            then Get-AzureRmRedisCache returns the details of the cache specified by Name. If only the ResourceGroup
            parameter is provided, then details for all caches in the resource group are returned.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

현재 구독의 모든 캐시에 대한 정보를 반환하려면 매개 변수 없이 `Get-AzureRmRedisCache`를 실행합니다.

    Get-AzureRmRedisCache

특정 리소스 그룹의 모든 캐시에 대한 정보를 반환하려면 `ResourceGroupName` 매개 변수와 함께 `Get-AzureRmRedisCache`를 실행합니다.

    Get-AzureRmRedisCache -ResourceGroupName myGroup

특정 캐시에 대한 정보를 반환하려면 캐시 이름을 포함하는 `Name` 매개 변수와 해당 캐시를 포함하는 리소스 그룹이 있는 `ResourceGroupName` 매개 변수와 함께 `Get-AzureRmRedisCache`를 실행합니다.

    PS C:\> Get-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup

    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/myGroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Succeeded
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 62], [notify-keyspace-events, KEA],
                         [maxclients, 1000]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : myGroup
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

## <a name="to-retrieve-the-access-keys-for-a-redis-cache"></a>Redis Cache에 대한 액세스 키를 검색하려면
캐시에 대한 액세스 키를 검색하려면 [Get-AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634516.aspx) cmdlet을 사용하면 됩니다.

`Get-AzureRmRedisCacheKey`에 대해 사용 가능한 매개 변수 및 해당 설명에 대한 목록을 보려면 다음 명령을 실행합니다.

    PS C:\> Get-Help Get-AzureRmRedisCacheKey -detailed

    NAME
        Get-AzureRmRedisCacheKey

    SYNOPSIS
        Gets the accesskeys for the specified redis cache.


    SYNTAX
        Get-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> [<CommonParameters>]

    DESCRIPTION
        The Get-AzureRmRedisCacheKey cmdlet gets the access keys for the specified cache.

    PARAMETERS
        -Name <String>
            Name of the redis cache.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

캐시에 대한 키를 검색하려면 `Get-AzureRmRedisCacheKey` cmdlet을 호출하고 캐시 이름과 해당 캐시를 포함하는 리소스 그룹 이름을 전달합니다.

    PS C:\> Get-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup

    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=

## <a name="to-regenerate-access-keys-for-your-redis-cache"></a>Redis Cache에 대한 액세스 키를 다시 생성하려면
캐시에 대한 액세스 키를 다시 생성하려면 [New-AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634512.aspx) cmdlet을 사용하면 됩니다.

`New-AzureRmRedisCacheKey`에 대해 사용 가능한 매개 변수 및 해당 설명에 대한 목록을 보려면 다음 명령을 실행합니다.

    PS C:\> Get-Help New-AzureRmRedisCacheKey -detailed

    NAME
        New-AzureRmRedisCacheKey

    SYNOPSIS
        Regenerates the access key of a redis cache.

    SYNTAX
        New-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> -KeyType <String> [-Force] [<CommonParameters>]

    DESCRIPTION
        The New-AzureRmRedisCacheKey cmdlet regenerate the access key of a redis cache.

    PARAMETERS
        -Name <String>
            Name of the redis cache.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -KeyType <String>
            Specifies whether to regenerate the primary or secondary access key. Possible values are Primary or Secondary.

        -Force
            When the Force parameter is provided, the specified access key is regenerated without any confirmation prompts.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

캐시에 대한 주 및 보조 키를 다시 생성하려면 `New-AzureRmRedisCacheKey` cmdlet을 호출하고 이름, 리소스 그룹을 전달하고 `KeyType` 매개 변수에 대해 `Primary` 또는 `Secondary`를 지정합니다. 다음 예제에서는 캐시에 대한 보조 액세스 키가 다시 생성됩니다.

    PS C:\> New-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary

    Confirm
    Are you sure you want to regenerate Secondary key for redis cache 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=

## <a name="to-delete-a-redis-cache"></a>Redis Cache를 삭제하려면
Redis Cache를 삭제하려면 [Remove-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634515.aspx) cmdlet을 사용합니다.

`Remove-AzureRmRedisCache`에 대해 사용 가능한 매개 변수 및 해당 설명에 대한 목록을 보려면 다음 명령을 실행합니다.

    PS C:\> Get-Help Remove-AzureRmRedisCache -detailed

    NAME
        Remove-AzureRmRedisCache

    SYNOPSIS
        Remove redis cache if exists.

    SYNTAX
        Remove-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Force] [-PassThru] [<CommonParameters>

    DESCRIPTION
        The Remove-AzureRmRedisCache cmdlet removes a redis cache if it exists.

    PARAMETERS
        -Name <String>
            Name of the redis cache to remove.

        -ResourceGroupName <String>
            Name of the resource group of the cache to remove.

        -Force
            When the Force parameter is provided, the cache is removed without any confirmation prompts.

        -PassThru
            By default Remove-AzureRmRedisCache removes the cache and does not return any value. If the PassThru par
            is provided then Remove-AzureRmRedisCache returns a boolean value indicating the success of the operatio

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

다음 예제에서는 캐시 이름 `myCache` 가 제거됩니다.

    PS C:\> Remove-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup

    Confirm
    Are you sure you want to remove redis cache 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## <a name="to-import-a-redis-cache"></a>Redis Cache를 가져오려면
`Import-AzureRmRedisCache` cmdlet을 사용하여 Azure Redis Cache 인스턴스에 데이터를 가져올 수 있습니다.

> [!IMPORTANT]
> Import/Export는 [프리미엄 계층](cache-premium-tier-intro.md) 캐시에만 제공됩니다. Import/Export에 대한 자세한 내용은 [Azure Redis Cache에서 데이터 가져오기 및 내보내기](cache-how-to-import-export-data.md)를 참조하세요.
> 
> 

`Import-AzureRmRedisCache`에 대해 사용 가능한 매개 변수 및 해당 설명에 대한 목록을 보려면 다음 명령을 실행합니다.

    PS C:\> Get-Help Import-AzureRmRedisCache -detailed

    NAME
        Import-AzureRmRedisCache

    SYNOPSIS
        Import data from blobs to Azure Redis Cache.


    SYNTAX
        Import-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Files <String[]> [-Format <String>] [-Force]
        [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Import-AzureRmRedisCache cmdlet imports data from the specified blobs into Azure Redis Cache.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -Files <String[]>
            SAS urls of blobs whose content should be imported into the cache.

        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.

        -Force
            When the Force parameter is provided, import will be performed without any confirmation prompts.

        -PassThru
            By default Import-AzureRmRedisCache imports data in cache and does not return any value. If the PassThru
            parameter is provided then Import-AzureRmRedisCache returns a boolean value indicating the success of the
            operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


다음 명령은 SAS URI가 지정한 Blob에서 Azure Redis Cache에 데이터를 가져옵니다.

    PS C:\>Import-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force

## <a name="to-export-a-redis-cache"></a>Redis Cache를 내보내려면
`Export-AzureRmRedisCache` cmdlet을 사용하여 Azure Redis Cache 인스턴스에서 데이터를 내보낼 수 있습니다.

> [!IMPORTANT]
> Import/Export는 [프리미엄 계층](cache-premium-tier-intro.md) 캐시에만 제공됩니다. Import/Export에 대한 자세한 내용은 [Azure Redis Cache에서 데이터 가져오기 및 내보내기](cache-how-to-import-export-data.md)를 참조하세요.
> 
> 

`Export-AzureRmRedisCache`에 대해 사용 가능한 매개 변수 및 해당 설명에 대한 목록을 보려면 다음 명령을 실행합니다.

    PS C:\> Get-Help Export-AzureRmRedisCache -detailed

    NAME
        Export-AzureRmRedisCache

    SYNOPSIS
        Exports data from Azure Redis Cache to a specified container.


    SYNTAX
        Export-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Prefix <String> -Container <String> [-Format
        <String>] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Export-AzureRmRedisCache cmdlet exports data from Azure Redis Cache to a specified container.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -Prefix <String>
            Prefix to use for blob names.

        -Container <String>
            SAS url of container where data should be exported.

        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.

        -PassThru
            By default Export-AzureRmRedisCache does not return any value. If the PassThru parameter is provided
            then Export-AzureRmRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


다음 명령은 Azure Redis Cache 인스턴스에서 SAS uri가 지정한 컨테이너로 데이터를 내보냅니다.

        PS C:\>Export-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
        -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
        pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"

## <a name="to-reboot-a-redis-cache"></a>Redis Cache를 재부팅하려면
`Reset-AzureRmRedisCache` cmdlet을 사용하여 Azure Redis Cache 인스턴스를 재부팅할 수 있습니다.

> [!IMPORTANT]
> 재부팅은 [프리미엄 계층](cache-premium-tier-intro.md) 캐시에만 사용할 수 있습니다. 캐시를 재부팅하는 방법에 대한 자세한 내용은 [캐시 관리 - 재부팅](cache-administration.md#reboot)을 참조하세요.
> 
> 

`Reset-AzureRmRedisCache`에 대해 사용 가능한 매개 변수 및 해당 설명에 대한 목록을 보려면 다음 명령을 실행합니다.

    PS C:\> Get-Help Reset-AzureRmRedisCache -detailed

    NAME
        Reset-AzureRmRedisCache

    SYNOPSIS
        Reboot specified node(s) of an Azure Redis Cache instance.


    SYNTAX
        Reset-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -RebootType <String> [-ShardId <Integer>]
        [-Force] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Reset-AzureRmRedisCache cmdlet reboots the specified node(s) of an Azure Redis Cache instance.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -RebootType <String>
            Which node to reboot. Possible values are "PrimaryNode", "SecondaryNode", "AllNodes".

        -ShardId <Integer>
            Which shard to reboot when rebooting a premium cache with clustering enabled.

        -Force
            When the Force parameter is provided, reset will be performed without any confirmation prompts.

        -PassThru
            By default Reset-AzureRmRedisCache does not return any value. If the PassThru parameter is provided
            then Reset-AzureRmRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


다음 명령은 지정된 캐시의 두 노드를 모두 재부팅합니다.

        PS C:\>Reset-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
        -Force


## <a name="next-steps"></a>다음 단계
Azure에서 Windows PowerShell 사용에 대한 자세한 내용은 다음 리소스를 참조하십시오.

* [MSDN에 있는 Azure Redis Cache cmdlet 설명서](https://msdn.microsoft.com/library/azure/mt634513.aspx)
* [Azure Resource Manager Cmdlet](http://go.microsoft.com/fwlink/?LinkID=394765): Azure Resource Manager 모듈에서 cmdlet을 사용하는 방법을 알아봅니다.
* [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/resource-group-template-deploy-portal.md): Azure 포털에서 리소스 그룹을 만들고 관리하는 방법에 대해 알아봅니다.
* [Azure 블로그](http://blogs.msdn.com/windowsazure): Azure의 새로운 기능에 대해 알아봅니다.
* [Windows PowerShell 블로그](http://blogs.msdn.com/powershell): Windows PowerShell의 새로운 기능에 대해 알아봅니다.
* ["Hey, Scripting Guy!" 블로그](http://blogs.technet.com/b/heyscriptingguy/): Windows PowerShell 커뮤니티에서 실제 팁과 요령을 확인합니다.

