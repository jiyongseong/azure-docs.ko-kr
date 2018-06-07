---
title: "Azure Service Fabric 컨테이너 앱 장애 조치(failover) 및 크기 조정 | Microsoft Docs"
description: "아 자습서에서는 장애 조치(failover)가 Azure Service Fabric 컨테이너 응용 프로그램에서 처리되는 방법을 알아봅니다.  또한 클러스터에서 실행되는 컨테이너 및 서비스의 크기를 조정하는 방법을 알아봅니다."
services: service-fabric
documentationcenter: 
author: suhuruli
manager: timlt
editor: suhuruli
tags: servicefabric
keywords: "Docker, 컨테이너, 마이크로 서비스, Service Fabric, Azure"
ms.assetid: 
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 27d1918125b8c2f79f6506470ae43354e402f9af
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/24/2018
---
# <a name="tutorial-demonstrate-fail-over-and-scaling-of-container-services-with-service-fabric"></a>자습서: Service Fabric을 통한 컨테이너 서비스의 장애 조치(failover) 및 크기 조정 시연

이 자습서는 시리즈의 3부입니다. 이 자습서에서는 장애 조치(failover)가 Service Fabric 컨테이너 응용 프로그램에서 처리되는 방법을 알아봅니다. 또한 컨테이너의 크기를 조정하는 방법을 배웁니다. 이 자습서에서는 다음을 수행했습니다.

> [!div class="checklist"]
> * Service Fabric 클러스터에서 컨테이너 장애 조치(failover)에 대해 알아보기  
> * 응용 프로그램에서 웹 프런트 엔드 컨테이너 크기 조정

## <a name="prerequisites"></a>필수 조건
[2부](service-fabric-tutorial-package-containers.md)의 응용 프로그램은 활성 Service Fabric 클러스터에서 실행되고 있습니다.

## <a name="fail-over-a-container-in-a-cluster"></a>클러스터에서 컨테이너 장애 조치(failover)
Service Fabric은 컨테이너 인스턴스가 오류가 발생해야 하는 클러스터의 다른 노드로 자동으로 이동하는지 확인합니다. 또한 수동으로 컨테이너의 노드를 드레이닝하고 클러스터의 다른 노드로 정상적으로 옮길 수 있습니다. 서비스의 크기를 조정하는 여러 방법이 있습니다. 이 예제에서는 Service Fabric Explorer를 사용합니다.

프런트 엔드 컨테이너를 장애 조치(failover)하려면 다음 단계를 수행합니다.

1. 클러스터에서 Service Fabric Explorer를 엽니다(예: `http://lin4hjim3l4.westus.cloudapp.azure.com:19080`).
2. 트리 뷰에서 **fabric:/TestContainer/azurevotefront** 노드를 클릭하고 파티션 노드(GUID로 표현됨)를 확장합니다. 트리 뷰에서 노드 이름을 확인합니다. 컨테이너에서 현재 실행 중인 노드를 보여 줍니다(예: `_nodetype_1`).
3. 트리 뷰에서 **Nodes** 노드를 확장합니다. 컨테이너를 실행 중인 노드 옆에 있는 줄임표(...)를 클릭합니다.
1. **다시 시작**을 선택하여 해당 노드를 다시 시작하고 다시 시작 작업을 확인합니다. 다시 시작하면 컨테이너를 클러스터의 다른 노드로 장애 조치(failover)합니다.

![noderestart][noderestart]

프런트 엔드 컨테이너가 실행되는 위치를 표시하는 노드 이름이 클러스터의 다른 노드로 어떻게 변경되는지 확인합니다. 몇 분 후 응용 프로그램으로 다시 이동하고 다른 노드에서 실행 중인 응용 프로그램을 볼 수 있어야 합니다.

## <a name="scale-containers-and-services-in-a-cluster"></a>클러스터에서 컨테이너 및 서비스 크기 조정
Service Fabric 컨테이너는 해당 서비스에 대한 로드를 수용하도록 클러스터 간에 크기를 조정할 수 있습니다. 클러스터에서 실행되는 인스턴스 수를 변경하여 컨테이너 크기를 조정합니다.

웹 프런트 엔드의 크기를 조정하려면 다음 단계를 수행합니다.

1. 클러스터에서 Service Fabric Explorer를 엽니다(예: `http://lin4hjim3l4.westus.cloudapp.azure.com:19080`).
2. 트리 뷰에서 **fabric:/TestContainer/azurevotefront** 노드 옆에 있는 줄임표(...)를 클릭하고 **Scale Service**를 선택합니다.

![sfxscale][sfxscale]

이제 웹 프런트 엔드의 인스턴스 수를 조정하도록 선택할 수 있습니다.

3. 이 수를 **2**로 변경하고 **Scale Service**를 클릭합니다.
4. 트리 뷰에서 **fabric:/TestContainer/azurevotefront** 노드를 클릭하고 파티션 노드(GUID로 표현됨)를 확장합니다.

![sfxscaledone][sfxscaledone]

이제 서비스에 두 인스턴스가 있는 것을 볼 수 있습니다. 트리 뷰에서 인스턴스에서 실행하는 노드를 볼 수 있습니다.

이 간단한 관리 작업으로 사용자 로드를 처리하는 프런트 엔드 서비스에 사용 가능한 리소스를 두 배로 증가시킬 수 있습니다. 서비스를 안정적으로 실행하기 위해 서비스의 여러 인스턴스가 필요하지 않다는 것을 이해하는 것이 중요합니다. 서비스가 실패하면 Service Fabric은 클러스터에서 새 서비스 인스턴스가 실행되는지 확인합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 응용 프로그램의 크기 조정 뿐만 아니라 컨테이너 장애 조치(failover)를 설명했습니다. 다음 단계가 완료되었습니다.

> [!div class="checklist"]
> * Service Fabric 클러스터에서 컨테이너 장애 조치(failover)에 대해 알아보기  
> * 응용 프로그램에서 웹 프런트 엔드 컨테이너 크기 조정

이 자습서 시리즈에서는 다음 방법에 대해 알아보았습니다. 
> [!div class="checklist"]
> * 컨테이너 이미지 만들기
> * Azure Container Registry에 컨테이너 이미지 푸시
> * Yeoman을 사용하여 Service Fabric에 대한 컨테이너 패키지
> * 컨테이너를 사용하여 Service Fabric 응용 프로그램 빌드 및 실행
> * Service Fabric에서 장애 조치(failover) 및 크기 조정이 처리되는 방법

[noderestart]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialnoderestart.png
[sfxscale]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialscale.png
[sfxscaledone]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialscaledone.png
