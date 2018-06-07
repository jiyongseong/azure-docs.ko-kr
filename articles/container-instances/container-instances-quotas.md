---
title: Azure Container Instances 할당량 및 지역 가용성
description: Azure Container Instances service의 기본 할당량 및 지역 가용성입니다.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: overview
ms.date: 02/27/2018
ms.author: marsma
ms.openlocfilehash: 22be921c7ab437e08d8ea5032befb8671dc90ed0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="quotas-and-region-availability-for-azure-container-instances"></a>Azure Container Instances에 대한 할당량 및 지역 가용성

모든 Azure 서비스에는 리소스와 기능에 대한 특정 기본 제한과 할당량이 있습니다. 다음 섹션에서는 몇 가지 ACI(Azure Container Instances) 리소스의 기본 리소스 제한과, Azure 지역의 ACI 서비스 가용성에 대해 자세히 설명합니다.

## <a name="service-quotas-and-limits"></a>서비스 할당량 및 제한

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

## <a name="region-availability"></a>지역 가용성

Azure Container Instances를 지정된 CPU 및 메모리 제한을 사용하여 다음 지역에서 사용할 수 있습니다.

| 위치 | OS | CPU | 메모리(GB) |
| -------- | -- | :---: | :-----------: |
| 미국 서부, 미국 동부, 유럽 서부, 북유럽 | Linux | 4 | 14 |
| 미국 서부 2, 동남 아시아 | Linux | 2 | 7 |
| 미국 서부, 미국 동부, 유럽 서부, 북유럽 | Windows | 4 | 14 |
| 미국 서부 2, 동남 아시아 | Windows | 2 | 3.5 |

이러한 리소스 제한 내에서 만든 컨테이너 인스턴스는 배포 지역 내의 사용 가능 여부의 적용을 받습니다. 영역이 과부하 상태에 있는 경우 인스턴스를 배포할 때 오류가 발생할 수 있습니다. 이러한 배포 오류를 완화하려면 낮은 CPU 및 메모리 설정을 사용하여 인스턴스를 배포하거나 나중에 배포를 시도합니다.

필요한 추가 지역 또는 늘려야 하는 CPU/메모리 제한이 있으면 [aka.ms/aci/feedback](https://aka.ms/aci/feedback)에서 지원 팀에 알려주세요.

컨테이너 인스턴스 배포 문제 해결에 대한 자세한 내용은 [Azure Container Instances로의 배포 문제 해결](container-instances-troubleshooting.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

특정 기본 제한 및 할당량은 증대가 가능합니다. 이러한 증대를 지원하는 하나 이상의 리소스에 대해 증대를 요청하려면 [Azure 지원 요청][azure-support] (**발급 요청**에 대해 "할당량" 선택)을 제출합니다.

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
