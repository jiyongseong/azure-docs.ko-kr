---
title: Azure Container Registry의 이미지 저장소
description: 보안, 중복성 및 용량을 포함하여 Docker 컨테이너 이미지가 Azure Container Registry에 저장되는 방식에 대한 세부 정보입니다.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 03/21/2018
ms.author: marsma
ms.openlocfilehash: 92e60b4213cb80d193a7c35f68b8f9fd099481d7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="container-image-storage-in-azure-container-registry"></a>Azure Container Registry의 컨테이너 이미지 저장소

모든 [기본, 표준 및 프리미엄](container-registry-skus.md) Azure Container Registry는 이미지 데이터 보안을 위한 미사용 시 암호화 및 이미지 데이터 보호를 위한 지역 중복과 같은 고급 Azure Storage 기능을 활용합니다. 다음 섹션에서는 ACR(Azure Container Registry)의 이미지 저장소 기능과 제한을 둘 다 설명합니다.

## <a name="encryption-at-rest"></a>미사용 시 암호화

레지스트리의 모든 컨테이너 이미지는 미사용 시 암호화됩니다. Azure는 저장하기 전에 이미지를 자동으로 암호화하고, 응용 프로그램 및 서비스가 이미지를 끌어올 때 즉석에서 이미지의 암호를 해독합니다.

## <a name="geo-redundant-storage"></a>지역 중복 저장소

Azure는 지역 중복 저장소 체계를 사용하여 컨테이너 이미지의 손실을 방지합니다. Azure Container Registry는 컨테이너 이미지를 지리적으로 떨어진 여러 데이터 센터에 자동으로 복제하여 지역 저장소 오류가 발생할 경우, 손실을 방지합니다.

## <a name="geo-replication"></a>지역에서 복제

고가용성 보장이 필요한 시나리오의 경우 프리미엄 레지스트리의 [지역에서 복제](container-registry-geo-replication.md) 기능을 사용하는 것이 좋습니다. 지역에서 복제는 저장소 오류뿐만 아니라 *전체* 지역 오류가 발생할 경우, 레지스트리에 대한 액세스 손실을 방지합니다. 지역에서 복제는 분산 개발 또는 배포 시나리오에서 빠른 푸시 및 풀을 위한 네트워크 근접 이미지 저장소와 같은 다른 이점도 제공합니다.

## <a name="image-limits"></a>이미지 제한

다음 표에서는 Azure Container Registry에 적용되는 컨테이너 이미지 및 저장소 제한에 대해 설명합니다.

| 리소스 | 제한 |
| -------- | :---- |
| 리포지토리 | 제한 없음 |
| 이미지 | 제한 없음 |
| 레이어 | 제한 없음 |
| 태그들 | 제한 없음|
| Storage | 5TB |

저장소 및 태그 수가 매우 많으면 레지스트리 성능에 영향을 줄 수 있습니다. [Azure CLI](/cli/azure/acr), ACR [REST API](/rest/api/containerregistry/) 또는 [Azure Portal][portal]을 레지스트리 유지 관리 루틴의 일부로 활용하여 사용하지 않는 리포지토리, 태그 및 이미지를 주기적으로 삭제합니다. 리포지토리, 이미지 및 태그와 같은 삭제된 레지스트리 리소스는 삭제 후 복구할 수 *없습니다*.

## <a name="storage-cost"></a>저장소 비용

가격 책정에 대한 자세한 내용은 [Azure Container Registry 가격 책정][pricing]을 참조하세요.

## <a name="next-steps"></a>다음 단계

여러 Azure Container Registry SKU(기본, 표준, 프리미엄)에 대한 자세한 내용은 [Azure Container Registry SKU](container-registry-skus.md)를 참조하세요.

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: http://aka.ms/acr/pricing

<!-- LINKS - Internal -->
