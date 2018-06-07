---
title: Azure Service Fabric 지원 옵션 살펴보기 | Microsoft Docs
description: 지원되는 Azure Service Fabric 클러스터 버전 및 파일 지원 티켓에 대한 링크
services: service-fabric
documentationcenter: .net
author: pkcsf
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/14/2018
ms.author: pkc
ms.openlocfilehash: 7a00b72f0b8d52ae0ced5f44784c2a61d40c8ac2
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="azure-service-fabric-support-options"></a>Azure Service Fabric 지원 옵션

응용 프로그램 워크로드가 실행되는 Service Fabric 클러스터에 대한 적절한 지원을 제공하기 위해 다양한 옵션을 설정했습니다. 필요한 지원 수준 및 문제의 심각도에 따라, 올바른 옵션을 선택해야 합니다. 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>프로덕션 문제 보고 또는 Azure에 대한 유료 지원 요청

Azure에 배포된 Service Fabric 클러스터의 문제를 보고하여 [Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) 또는 [Microsoft 지원 포털](http://support.microsoft.com/oas/default.aspx?prid=16146)에서 지원에 대한 티켓을 엽니다.

다음에 대해 자세히 알아봅니다.
 
- [Azure에 대한 Microsoft의 지원](https://azure.microsoft.com/support/plans/?b=16.44)
- [Microsoft 프리미어 지원](https://support.microsoft.com/en-us/premier)

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>프로덕션 문제 보고 또는 독립 실행형 Service Fabric 클러스터에 대한 유료 지원 요청

프리미어 또는 기타 클라우드에 배포된 Service Fabric 클러스터의 문제를 보고하려면 [Microsoft 지원 포털](http://support.microsoft.com/oas/default.aspx?prid=16146)에서 전문 지원에 대한 티켓을 여세요.

다음에 대해 자세히 알아봅니다.

- [온-프레미스에 대한 Microsoft의 전문 지원](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0)
- [Microsoft 프리미어 지원](https://support.microsoft.com/en-us/premier)

## <a name="report-azure-service-fabric-issues"></a>Azure Service Fabric 문제 보고 
Service Fabric 문제를 보고하기 위해 GitHub 리포지토리를 설정했습니다.  다음과 같은 포럼을 적극적으로 모니터링하고 있습니다.

### <a name="github-repo"></a>GitHub 리포지토리 
[Service-Fabric-issues git repo](https://github.com/Azure/service-fabric-issues)에서 Azure Service Fabric 문제를 보고합니다. 이 리포지토리는 Azure Service Fabric의 문제를 보고 및 추적하고 사소한 기능 요청을 만드는 데 사용됩니다. **라이브 사이트 문제를 보고할 때는 이 리포지토리를 사용하지 마세요**.

### <a name="stackoverflow-and-msdn-forums"></a>StackOverflow 및 MSDN 포럼
[StackOverflow에서 Service Fabric 태그][stackoverflow] 및 [MSDN의 Service Fabric 포럼][msdn-forum]은 플랫폼의 작동 방식 및 특정 작업을 수행하는 방법에 대한 질문을 제공하는 데 가장 적합합니다.

### <a name="azure-feedback-forum"></a>Azure 피드백 포럼
[Service Fabric을 위한 Azure 피드백 포럼][uservoice-forum]은 중장기적 계획의 일환으로 가장 인기 있는 요청을 검토하면서 제품에 대해 갖게 된 포괄적인 기능 아이디어를 제출할 수 있는 유용한 장소입니다. 커뮤니티 내에서 제안하신 요구가 충분히 지원될 수 있게 최선을 다하고 있습니다.


## <a name="supported-service-fabric-versions"></a>지원되는 Service Fabric 버전.

클러스터가 지원되는 Service Fabric 버전을 항상 실행하도록 해야 합니다. 새로운 버전의 Service Fabric 릴리스를 발표하면 이전 버전은 해당 날짜부터 최소 60일 후 지원 종료되는 것으로 표시됩니다. 새로운 릴리스는 [Service Fabric 팀 블로그](https://blogs.msdn.microsoft.com/azureservicefabric/)에서 발표됩니다.

클러스터에서 지원되는 Service Fabric 버전을 계속 실행하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 클러스터에서 Service Fabric 버전 업그레이드](service-fabric-cluster-upgrade.md)
- [독립 실행형 Windows 서버 클러스터에서 Service Fabric 버전 업그레이드](service-fabric-cluster-upgrade-windows-server.md)
 
다음은 지원되는 Service Fabric 버전과 지원 종료 날짜 목록입니다.

| **클러스터의 Service Fabric 런타임** | **클러스터 버전에서 직접 업그레이드할 수 있습니다.** |**호환 가능한 SDK / NuGet 패키지 버전** | **지원 종료 날짜** |
| --- | --- |--- | --- |
| 5.3.121 이전의 모든 클러스터 버전 | 5.1.158* |버전 2.3보다 작거나 같음 |2017년 1월 20일 |
| 5.3.* | 5.1.158.* |버전 2.3보다 작거나 같음 |2017년 2월 24일 |
| 5.4.* | 5.1.158.* |버전 2.4보다 작거나 같음 |2017년 5월 10일       |
| 5.5.* | 5.4.164.* |버전 2.5보다 작거나 같음 |2017년 8월 10일    |
| 5.6.* | 5.4.164.* |버전 2.6보다 작거나 같음 |2017년 10월 13일   |
| 5.7.* | 5.4.164.* |버전 2.7보다 작거나 같음 |2017년 12월 15일  |
| 6.0.* | 5.6.205.* |버전 2.8보다 작거나 같음 |2018년 3월 30일     | 
| 6.1.* | 5.7.221.* |버전 3.0보다 작거나 같음 |2018년 7월15일      |
| 6.2.* | 6.0.232.* |버전 3.1보다 작거나 같음 |현재 버전 및 종료 날짜 |

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Service Fabric 미리 보기 버전 - 프로덕션 사용이 지원되지 않습니다.
종종 피드백이 필요한 중요한 기능이 포함된 버전이 미리 보기로 릴리스됩니다. 이러한 미리 보기 버전은 테스트 용도로만 사용해야 합니다. 프로덕션 클러스터는 항상 지원되는 안정적인 Service Fabric 버전으로 실행해야 합니다. 미리 보기 버전은 항상 주 버전과 부 버전 번호가 255로 시작합니다. 예를 들어 Service Fabric 버전 255.255.5703.949가 표시되면 해당 릴리스 버전은 테스트 클러스터에서만 미리 보기로 사용됩니다. 이러한 미리 보기 릴리스는 포함된 기능에 관한 자세한 내용과 함께 [Service Fabric 팀 블로그](https://blogs.msdn.microsoft.com/azureservicefabric)에서 발표됩니다.

이러한 미리 보기 릴리스를 위한 무료 지원 옵션이 있습니다. [Azure Service Fabric 문제 보고서](https://docs.microsoft.com/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues)에 나열된 옵션 중 하나를 사용하여 질문을 하거나 피드백을 제공하세요.

## <a name="next-steps"></a>다음 단계

- [Azure 클러스터에서 Service Fabric 버전 업그레이드](service-fabric-cluster-upgrade.md)
- [독립 실행형 Windows 서버 클러스터에서 Service Fabric 버전 업그레이드](service-fabric-cluster-upgrade-windows-server.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: http://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: http://aka.ms/servicefabricdocs
[sample-repos]: http://aka.ms/servicefabricsamples
