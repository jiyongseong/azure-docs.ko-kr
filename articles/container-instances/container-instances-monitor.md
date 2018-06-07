---
title: Azure Container Instances에서 컨테이너 모니터링
description: Azure Container Instances에서 컨테이너의 CPU 및 메모리와 같은 계산 리소스의 소비를 모니터링하는 방법에 대한 세부 정보입니다.
services: container-instances
author: neilpeterson
manager: jeconnoc
ms.service: container-instances
ms.topic: overview
ms.date: 04/24/2018
ms.author: nepeters
ms.openlocfilehash: 814346bd8021b996b64cd7f0311f31b13b32a8c8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Azure Container Instances에서 컨테이너 리소스 모니터링

Azure Monitor는 컨테이너 인스턴스에서 사용되는 계산 리소스에 대한 정보를 제공합니다. Azure Monitor를 사용하여 컨테이너 그룹 및 해당 컨테이너의 CPU 및 메모리 사용률을 추적합니다. 이 리소스 사용량 데이터를 통해 컨테이너 그룹에 대한 최상의 CPU 및 메모리 설정을 결정할 수 있습니다.

이 문서는 Azure Portal 및 Azure CLI를 사용하여 컨테이너 인스턴스에 대한 CPU 및 메모리 사용량 수집에 대해 자세히 설명합니다.

> [!IMPORTANT]
> 이 때 리소스 사용량 메트릭은 Linux 컨테이너에만 사용할 수 있습니다.
>

## <a name="available-metrics"></a>사용 가능한 메트릭

Azure Monitor는 Azure Container Instances의 **CPU** 및 **메모리** 사용량 모두에 대한 메트릭을 제공합니다. 두 메트릭은 컨테이너 그룹 및 개별 컨테이너에 사용할 수 있습니다.

CPU 메트릭은 **밀리코어**로 표현됩니다. 1 밀리코어는 CPU 코어의 1/1000이므로 500 밀리코어(또는 500m)는 CPU 코어의 50% 사용을 나타냅니다.

메모리 메트릭은 **바이트**로 표현됩니다.

## <a name="get-metrics---azure-portal"></a>메트릭 가져오기 - Azure Portal

컨테이너 그룹을 만들 때 Azure Portal에서 Azure Monitor 데이터를 사용할 수 있습니다. 컨테이너 그룹에 대한 메트릭을 보려면 리소스 그룹 및 컨테이너 그룹을 차례로 선택합니다. 여기에서 CPU 및 메모리 사용량에 대한 미리 생성된 차트를 볼 수 있습니다.

![이중 차트][dual-chart]

여러 컨테이너를 포함하는 컨테이너 그룹이 있는 경우 [차원][monitor-dimension]을 사용하여 각 개별 컨테이너에 대한 메트릭을 제공합니다. 개별 컨테이너 메트릭이 있는 차트를 만들려면 다음 단계를 수행합니다.

1. 왼쪽 탐색 메뉴에서 **모니터**를 선택합니다.
2. 컨테이너 그룹 및 메트릭(CPU 또는 메모리)을 선택합니다.
3. 녹색 차원 단추를 선택하고, **컨테이너 이름**을 선택합니다.

![차원][dimension]

## <a name="get-metrics---azure-cli"></a>메트릭 가져오기 - Azure CLI

Azure CLI를 사용하여 컨테이너 인스턴스 CPU 및 메모리 사용량을 수집할 수도 있습니다. 먼저 다음 명령을 사용하여 컨테이너 그룹의 ID를 가져옵니다. `<resource-group>`을 리소스 그룹 이름으로 바꾸고, `<container-group>`을 컨테이너 그룹의 이름으로 바꿉니다.


```console
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

다음 명령을 사용하여 **CPU** 사용량 메트릭을 가져옵니다.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table

Timestamp            Name              Average
-------------------  ------------  -----------
2018-04-22 04:39:00  CPU Usage
2018-04-22 04:40:00  CPU Usage
2018-04-22 04:41:00  CPU Usage
2018-04-22 04:42:00  CPU Usage
2018-04-22 04:43:00  CPU Usage      0.375
2018-04-22 04:44:00  CPU Usage      0.875
2018-04-22 04:45:00  CPU Usage      1
2018-04-22 04:46:00  CPU Usage      3.625
2018-04-22 04:47:00  CPU Usage      1.5
2018-04-22 04:48:00  CPU Usage      2.75
2018-04-22 04:49:00  CPU Usage      1.625
2018-04-22 04:50:00  CPU Usage      0.625
2018-04-22 04:51:00  CPU Usage      0.5
2018-04-22 04:52:00  CPU Usage      0.5
2018-04-22 04:53:00  CPU Usage      0.5
```

다음 명령을 사용하여 **메모리** 사용량 메트릭을 가져옵니다.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table

Timestamp            Name              Average
-------------------  ------------  -----------
2018-04-22 04:38:00  Memory Usage
2018-04-22 04:39:00  Memory Usage
2018-04-22 04:40:00  Memory Usage
2018-04-22 04:41:00  Memory Usage
2018-04-22 04:42:00  Memory Usage  6.76915e+06
2018-04-22 04:43:00  Memory Usage  9.22061e+06
2018-04-22 04:44:00  Memory Usage  9.83552e+06
2018-04-22 04:45:00  Memory Usage  8.42906e+06
2018-04-22 04:46:00  Memory Usage  8.39526e+06
2018-04-22 04:47:00  Memory Usage  8.88013e+06
2018-04-22 04:48:00  Memory Usage  8.89293e+06
2018-04-22 04:49:00  Memory Usage  9.2073e+06
2018-04-22 04:50:00  Memory Usage  9.36243e+06
2018-04-22 04:51:00  Memory Usage  9.30509e+06
2018-04-22 04:52:00  Memory Usage  9.2416e+06
2018-04-22 04:53:00  Memory Usage  9.1008e+06
```

다중 컨테이너 그룹의 경우 컨테이너당 이 데이터를 반환하도록 `containerName` 차원을 추가할 수 있습니다.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --dimension containerName --output table

Timestamp            Name          Containername             Average
-------------------  ------------  --------------------  -----------
2018-04-22 17:03:00  Memory Usage  aci-tutorial-app      1.95338e+07
2018-04-22 17:04:00  Memory Usage  aci-tutorial-app      1.93096e+07
2018-04-22 17:05:00  Memory Usage  aci-tutorial-app      1.91488e+07
2018-04-22 17:06:00  Memory Usage  aci-tutorial-app      1.94335e+07
2018-04-22 17:07:00  Memory Usage  aci-tutorial-app      1.97714e+07
2018-04-22 17:08:00  Memory Usage  aci-tutorial-app      1.96178e+07
2018-04-22 17:09:00  Memory Usage  aci-tutorial-app      1.93434e+07
2018-04-22 17:10:00  Memory Usage  aci-tutorial-app      1.92614e+07
2018-04-22 17:11:00  Memory Usage  aci-tutorial-app      1.90659e+07
2018-04-22 16:12:00  Memory Usage  aci-tutorial-sidecar  1.35373e+06
2018-04-22 16:13:00  Memory Usage  aci-tutorial-sidecar  1.28614e+06
2018-04-22 16:14:00  Memory Usage  aci-tutorial-sidecar  1.31379e+06
2018-04-22 16:15:00  Memory Usage  aci-tutorial-sidecar  1.29536e+06
2018-04-22 16:16:00  Memory Usage  aci-tutorial-sidecar  1.38138e+06
2018-04-22 16:17:00  Memory Usage  aci-tutorial-sidecar  1.41312e+06
2018-04-22 16:18:00  Memory Usage  aci-tutorial-sidecar  1.49914e+06
2018-04-22 16:19:00  Memory Usage  aci-tutorial-sidecar  1.43565e+06
2018-04-22 16:20:00  Memory Usage  aci-tutorial-sidecar  1.408e+06
```

## <a name="next-steps"></a>다음 단계

[Azure 모니터링 개요][azure-monitoring]에서 Azure 모니터링에 대해 자세히 알아봅니다.

<!-- IMAGES -->
[cpu-chart]: ./media/container-instances-monitor/cpu-multi.png
[dimension]: ./media/container-instances-monitor/dimension.png
[dual-chart]: ./media/container-instances-monitor/metrics.png
[memory-chart]: ./media/container-instances-monitor/memory-multi.png

<!-- LINKS - Internal -->
[azure-monitoring]: ../monitoring-and-diagnostics/monitoring-overview.md
[monitor-dimension]: ../monitoring-and-diagnostics/monitoring-metric-charts.md#what-are-multi-dimensional-metrics
