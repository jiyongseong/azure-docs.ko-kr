---
title: "Azure CLI Redis Cache 샘플 | Microsoft Docs"
description: "Azure Redis Cache에 대한 Azure CLI 샘플입니다."
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 8d2de145-50c0-4f76-bf8f-fdf679f03698
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.date: 04/14/2017
ms.author: wesmc
ms.openlocfilehash: 4450a8f7f16e3503626c9ee0ca125fc2b1ed9052
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2018
---
# <a name="azure-cli-samples-for-azure-redis-cache"></a>Azure Redis Cache에 대한 Azure CLI 샘플

다음 테이블은 Azure CLI를 사용하여 빌드된 bash 셸에 대한 링크를 포함합니다.

| | |
|---|---|
|**캐시 만들기**||
| [캐시 만들기](./scripts/create-cache.md) | 리소스 그룹 및 기본 계층 Azure Redis Cache를 만듭니다. |
| [클러스터링을 사용하여 프리미엄 캐시 만들기](./scripts/create-premium-cache-cluster.md) | 클러스터링을 사용하여 리소스 그룹 및 프리미엄 계층 캐시를 만듭니다.|
| [캐시 세부 정보 가져오기](./scripts/show-cache.md) | 프로비전 상태를 포함한 Azure Redis Cache 인스턴스의 세부 정보를 가져옵니다. |
| [호스트 이름, 포트 및 키 가져오기](./scripts/cache-keys-ports.md) | Azure Redis Cache 인스턴스의 호스트 이름, 포트 및 키를 가져옵니다. |
|**웹앱과 캐시**||
| [Redis Cache에 웹앱 연결](./../app-service/scripts/app-service-cli-app-service-redis.md) | Azure 웹앱 및 Redis Cache를 만들고 앱 설정에 Redis 연결 세부 정보를 추가합니다. |
|**캐시 삭제**||
| [캐시 삭제](./scripts/delete-cache.md) | Azure Redis Cache 인스턴스를 삭제합니다.  |
| | |

Azure CLI 2.0에 대한 자세한 내용은 [Azure CLI 2.0 설치](https://docs.microsoft.com/cli/azure/install-azure-cli) 및 [Azure CLI 2.0 시작](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)을 참조하세요.