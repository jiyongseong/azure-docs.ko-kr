---
title: Azure Service Fabric 패턴 및 시나리오 | Microsoft Docs
description: Service Fabric에서 마이크로 서비스를 개발, 디자인 및 운영하기 위한 모범 사례와 검증된 재사용 가능한 패턴에 대해 알아봅니다.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: d5aa75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: ryanwi
ms.openlocfilehash: 5c2cd38001a9645011bbab84cff3b9366aa64b1d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-patterns-and-scenarios"></a>Service Fabric 패턴 및 시나리오
Azure Service Fabric을 사용하여 대규모 마이크로 서비스를 빌드하려면 PaaS(platform as a service)를 디자인하고 빌드한 전문가에게 알아봅니다. 적절한 아키텍처를 시작한 다음 응용 프로그램에 대한 리소스를 최적화하는 방법을 알아봅니다. [Service Fabric 패턴 및 사례](https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=mudwqISGD_6005167344) 과정에서는 Service Fabric 시나리오 및 응용 프로그램 영역에 대해 실제 고객이 가장 자주 묻는 질문에 답변합니다.
 
모범 사례 및 검증된 재사용 가능한 패턴을 사용하여 Service Fabric에서 마이크로 서비스를 디자인, 개발 및 운영하는 방법을 알아봅니다. Service Fabric의 개요를 살펴본 다음 클러스터 최적화 및 보안, 마이그레이션 레거시 앱, 대규모 IoT, 호스팅 게임 엔진 등을 다루는 항목에 대해 자세히 알아봅니다. 다양한 워크로드에 대한 지속적인 업데이트와 Linux Support 및 컨테이너에 대한 자세한 내용도 살펴봅니다. 

## <a name="introduction"></a>소개
모범 사례를 탐색하고 IaaS(서비스 제공 인프라)를 통한 PaaS(Platform as a Service) over 선택에 대해 알아봅니다. 다음과 같은 검증된 응용 프로그램 디자인 원칙에 대한 자세한 내용을 알아봅니다.

<table><tr><th>비디오</th><th>PowerPoint 데크</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=N2KwbbSGD_6405167344">
<img src="./media/service-fabric-patterns-and-scenarios/intro.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=mudwqISGD_6005167344">Service Fabric 소개</a></td></tr>
</table>

## <a name="cluster-planning-and-management"></a>클러스터 계획 및 관리
Azure Service Fabric에 있어서 용량 계획, 클러스터 최적화 및 클러스터 보안에 대해 알아봅니다.

<table><tr><th>비디오</th><th>PowerPoint 데크</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=cyDYZcSGD_2805167344">
<img src="./media/service-fabric-patterns-and-scenarios/cluster.png" WIDTH="360" HEIGHT="244">
</a></td><td> <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=E5B3nJSGD_805167344">클러스터 계획 및 관리</a></td></tr>
</table>

## <a name="hyper-scale-web"></a>초대형 웹
가용성과 안정성, 초대형 및 상태 관리를 비롯한 초대형 웹 관련 개념을 검토합니다.

<table><tr><th>비디오</th><th>PowerPoint 데크</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=NgldAdSGD_405167344">
<img src="./media/service-fabric-patterns-and-scenarios/hyperscaleweb.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=CPMLBLSGD_7705167344">하이퍼스케일(hyper-scale) 웹</a></td></tr>
</table>

## <a name="iot"></a>IoT
Azure IoT 파이프라인, 다중 테넌트 지원 및 대규모 IoT를 비롯하여 Azure Service Fabric의 컨텍스트에서 IoT(사물 인터넷)를 살펴봅니다.

<table><tr><th>비디오</th><th>PowerPoint 데크</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=naFUVeSGD_1505167344">
<img src="./media/service-fabric-patterns-and-scenarios/iot.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=kfqFWMSGD_6205167344">IoT</a></td></tr>
</table>

## <a name="gaming"></a>게임
턴 기반 게임, 대화형 게임 및 기존 게임 엔진 호스팅에 대해 살펴봅니다.

<table><tr><th>비디오</th><th>PowerPoint 데크</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=6wECzeSGD_3805167344">
<img src="./media/service-fabric-patterns-and-scenarios/gaming.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=kfqFWMSGD_6205167344">게임</a></td></tr>
</table>

## <a name="continuous-delivery"></a>지속적인 업데이트
Visual Studio Team Services를 통한 지속적인 통합/지속적인 업데이트, 워크플로 빌드/패키지/게시, 다중 환경 설치 및 서비스 패키지/공유를 포함한 개념을 살펴봅니다.

<table><tr><th>비디오</th><th>PowerPoint 데크</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=78h5ofSGD_305167344">
<img src="./media/service-fabric-patterns-and-scenarios/cd.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=VlENvOSGD_105167344">지속적인 업데이트</a></td></tr>
</table>

## <a name="migration"></a>마이그레이션
레거시 앱의 마이그레이션 및 클라우드 서비스에서의 마이그레이션에 대해 알아봅니다.

<table><tr><th>비디오</th><th>PowerPoint 데크</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=hd1cMgSGD_5605167344">
<img src="./media/service-fabric-patterns-and-scenarios/migration.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=GQAq4QSGD_8305167344">마이그레이션</a></td></tr>
</table>

## <a name="containers-and-linux-support"></a>컨테이너 및 Linux Support
"컨테이너의 필요성"에 대해 알아봅니다. Windows 컨테이너, Linux Support 및 Linux 컨테이너 오케스트레이션에 대한 미리 보기에 대해 알아봅니다. .NET Core 앱을 Linux로 마이그레이션하는 방법도 알아봅니다.

<table><tr><th>비디오</th><th>PowerPoint 데크</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=V1ERJhSGD_305167344">
<img src="./media/service-fabric-patterns-and-scenarios/containers.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=mlYsZRSGD_2105167344">컨테이너 및 Linux 지원</a></td></tr>
</table>

## <a name="next-steps"></a>다음 단계
지금까지 Service Fabric 패턴 및 시나리오에 대해 배웠습니다. 이제 [클러스터 만들기 및 관리](service-fabric-deploy-anywhere.md), [Cloud Services 앱을 Service Fabric으로 마이그레이션](service-fabric-cloud-services-migration-worker-role-stateless-service.md), [지속적인 업데이트 설정](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) 및 [컨테이너 배포](service-fabric-containers-overview.md) 방법에 대해 자세히 알아봅니다.
