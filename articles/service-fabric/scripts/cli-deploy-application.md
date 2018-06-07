---
title: "Azure Service Fabric CLI 스크립트 배포 샘플"
description: "Azure Service Fabric CLI를 사용하여 Azure Service Fabric 클러스터에 응용 프로그램 배포"
services: service-fabric
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 12/06/2017
ms.author: adegeo
ms.custom: mvc
ms.openlocfilehash: a9b7b2e3a8355aa0da0069bd27d2f61d8b5b8028
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2017
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Service Fabric 클러스터에 응용 프로그램 배포

이 샘플 스크립트는 클러스터 이미지 저장소에 응용 프로그램 패키지를 복사하고, 응용 프로그램 유형을 클러스터에 등록하고, 해당 응용 프로그램 유형에서 응용 프로그램 인스턴스를 만듭니다. 이때 기본 서비스도 만들어집니다.

필요한 경우 [Service Fabric CLI](../service-fabric-cli.md)를 설치합니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-sh[main](../../../cli_scripts/service-fabric/deploy-application/deploy-application.sh "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>배포 정리

완료했으면 [제거](cli-remove-application.md) 스크립트를 사용하여 응용 프로그램을 제거할 수 있습니다. 제거 스크립트는 응용 프로그램 인스턴스를 삭제하고 응용 프로그램 형식을 등록 취소하며 이미지 저장소에서 응용 프로그램 패키지를 삭제합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Service Fabric CLI 설명서](../service-fabric-cli.md)를 참조하세요.

Azure Service Fabric에 대한 추가 Service Fabric CLI 샘플은 [Service Fabric CLI 샘플](../samples-cli.md)에서 확인할 수 있습니다.
