---
title: '자습서: Service Fabric 독립 실행형 클러스터 정리 - Azure Service Fabric | Microsoft Docs'
description: 이 자습서에서는 독립 실행형 클러스터를 정리하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: david-stanford
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: dastanfo
ms.custom: mvc
ms.openlocfilehash: d92712200920f135e362e15e1d9536dc2342ebaa
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211092"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>자습서: 독립 실행형 클러스터 정리

Service Fabric 독립 실행형 클러스터는 사용자 자신의 환경을 선택하고 Service Fabric이 수행하는 "모든 OS 및 모든 클라우드" 접근 방법의 일부로서 클러스터를 만드는 옵션을 제공합니다. 이 자습서 시리즈에서는 AWS에서 호스팅되는 독립 실행형 클러스터를 만들고 여기에 응용 프로그램을 설치합니다.

이 자습서는 시리즈의 4부입니다. 이 자습서의 부분에서는 사용자가 만든 AWS 리소스를 정리하여 Service Fabric 클러스터에 호스트하는 방법을 보여줍니다.

시리즈 4부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Service Fabric 클러스터 정리
> * AWS 리소스 정리

## <a name="clean-up-service-fabric-cluster"></a>Service Fabric 클러스터 정리

* Service Fabric을 설치하는 데 사용한 EC2 인스턴스로 RDP 수행
* PowerShell 열기
* 디렉터리를 두 번째 자습서에서 추출된 폴더로 변경합니다.
* 다음 명령을 실행하여 Service Fabric 클러스터를 제거합니다.

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
```

* `Y` 대화 상자가 나타나면 성공한 경우 출력은 대체된 고유한 IP 주소와 함께 다음과 같이 표시됩니다.

```powershell
Best Practices Analyzer completed successfully.
Removing configuration from machine 172.31.21.141
Removing configuration from machine 172.31.27.1
Removing configuration from machine 172.31.20.163
Configuration removed from machine 172.31.21.141
Configuration removed from machine 172.31.27.1
Configuration removed from machine 172.31.20.163
The cluster is successfully removed.
```

## <a name="clean-up-aws-resources"></a>AWS 리소스 정리

* AWS 계정에 로그인
* EC2 콘솔로 이동합니다.
* 자습서의 1부에서 만든 세 개의 노드를 선택합니다.
* **작업** > **인스턴스 상태** > **종료**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

시리즈의 4부에서는 이전 단계에서 생성된 리소스를 정리하는 방법을 알아보았습니다.

> [!div class="checklist"]
> * 리소스 정리

> [!div class="nextstepaction"]
> [처음으로](service-fabric-tutorial-standalone-create-infrastructure.md)