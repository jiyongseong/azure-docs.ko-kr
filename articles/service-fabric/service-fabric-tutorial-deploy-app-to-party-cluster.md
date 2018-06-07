---
title: 클러스터에 Azure Service Fabric 응용 프로그램 배포 | Microsoft Docs
description: Visual Studio에서 응용 프로그램을 클러스터에 배포하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: ryanwi,mikhegn
ms.custom: mvc
ms.openlocfilehash: 4716cacf840dcf7a372923e29f758dbdc82fbf51
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208882"
---
# <a name="tutorial-deploy-an-application-to-a-service-fabric-cluster-in-azure"></a>자습서: Azure에서 Service Fabric 클러스터에 응용 프로그램 배포
이 자습서는 시리즈의 2부이며, Visual Studio에서 Azure Service Fabric 응용 프로그램을 Azure의 새 클러스터에 직접 배포하는 방법을 보여 줍니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * Visual Studio에서 클러스터 만들기
> * Visual Studio를 사용하여 원격 클러스터에 응용 프로그램 배포


이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * [.NET Service Fabric 응용 프로그램 빌드](service-fabric-tutorial-create-dotnet-app.md)
> * 응용 프로그램을 원격 클러스터에 배포
> * [ASP.NET Core 프런트 엔드 서비스에 HTTPS 엔드포인트 추가](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [Visual Studio Team Services를 사용하여 CI/CD 구성](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [응용 프로그램에 대한 모니터링 및 진단 설정](service-fabric-tutorial-monitoring-aspnet.md)


## <a name="prerequisites"></a>필수 조건
이 자습서를 시작하기 전에:
- Azure 구독이 없는 경우 [평가판 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- [Visual Studio 2017을 설치](https://www.visualstudio.com/)하고 **Azure 개발**과 **ASP.NET 및 웹 개발** 워크로드를 설치합니다.
- [Service Fabric SDK를 설치](service-fabric-get-started.md)합니다.

## <a name="download-the-voting-sample-application"></a>투표 응용 프로그램 샘플 다운로드
[이 자습서 시리즈의 1부](service-fabric-tutorial-create-dotnet-app.md)에서 투표 응용 프로그램 샘플을 빌드하지 않은 경우 다운로드할 수 있습니다. 명령 창에서 다음 명령을 실행하여 로컬 컴퓨터에 샘플 앱 리포지토리를 복제합니다.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="create-a-service-fabric-cluster"></a>Service Fabric 클러스터 만들기
응용 프로그램이 준비되면 Visual Studio에서 클러스터에 직접 배포할 수 있습니다. [Service Fabric 클러스터](/service-fabric/service-fabric-deploy-anywhere.md)는 마이크로 서비스가 배포되고 관리되는 네트워크로 연결된 가상 또는 실제 컴퓨터 집합입니다.

Visual Studio 내 배포 옵션에는 다음 두 가지 옵션이 있습니다.
- Visual Studio의 Azure에서 클러스터를 만듭니다. 이 옵션을 사용하면 기본 설 구성으로 Visual Studio에서 직접 보안 클러스터정를 만들 수 있습니다. 이 유형의 클러스터는 클러스터를 만든 다음, Visual Studio 내에서 해당 클러스터에 직접 게시할 수 있는 테스트 시나리오에 적합합니다.
- 기존 클러스터를 구독에 게시합니다.  [Azure Portal](https://portal.azure.com)을 통해, [PowerShel](./scripts/service-fabric-powershell-create-secure-cluster-cert.md) 또는 [Azure CLI](./scripts/cli-create-cluster.md) 스크립트를 사용하여 또는 [Azure Resource Manager 템플릿](service-fabric-tutorial-create-vnet-and-windows-cluster.md)에서 Service Fabric 클러스터를 만들 수 있습니다.

이 자습서에서는 Visual Studio에서 클러스터를 만듭니다. 이미 클러스터를 배포한 경우 연결 엔드포인트를 복사하여 붙여넣거나 구독에서 선택할 수 있습니다.
> [!NOTE]
> 대부분의 서비스에서 역방향 프록시를 사용하여 서로 통신합니다. Visual Studio 및 Party 클러스터에서 만든 클러스터는 기본적으로 역방향 프록시를 사용하도록 설정됩니다.  기존 클러스터를 사용하는 경우 [클러스터에서 역방향 프록시를 사용하도록 설정](service-fabric-reverseproxy.md#setup-and-configuration)해야 합니다.

### <a name="find-the-votingweb-service-endpoint"></a>VotingWeb 서비스 엔드포인트 찾기
첫째, 프런트 엔드 웹 서비스의 엔드포인트를 찾습니다.  프런트 엔드 웹 서비스는 특정 포트에 대해 수신 대기합니다.  응용 프로그램이 Azure에서 클러스터를 배포하는 경우 클러스터와 응용 프로그램 모두 Azure 부하 분산 장치 뒤에서 실행됩니다.  응용 프로그램 포트는 인바운드 트래픽이 웹 서비스에 도달할 수 있도록 Azure 부하 분산 장치에서 열려 있어야 합니다.  포트(예: 8080)는 **엔드포인트** 요소의 *VotingWeb/PackageRoot/ServiceManifest.xml* 파일에 있습니다.

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

다음 단계에서 **클러스터 만들기** 대화 상자의 **고급** 탭에서 이 포트를 지정합니다.  기존 클러스터에 응용 프로그램을 배포하는 경우 [PowerShell 스크립트](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) 또는 [Azure Portal](https://portal.azure.com)을 사용하여 Azure 부하 분산 장치에서 이 포트를 열 수 있습니다.

### <a name="create-a-cluster-in-azure-through-visual-studio"></a>Visual Studio를 통해 Azure에서 클러스터 만들기
[솔루션 탐색기]에서 응용 프로그램 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

Azure 계정을 사용하여 로그인하면 구독 정보에 액세스할 수 있습니다. Party 클러스터를 사용하는 경우 이 단계는 선택 사항입니다.

**연결 엔드포인트**에 대한 드롭다운을 선택하고 **<Create New Cluster...>** 옵션을 선택합니다.
    
![[게시] 대화 상자](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)
    
**클러스터 만들기** 대화 상자에서 다음 설정을 수정합니다.

1. **클러스터 이름** 필드에서 클러스터의 이름뿐만 아니라 사용하려는 구독과 위치도 지정합니다.
2. 선택 사항: 노드 수를 수정할 수 있습니다. 기본적으로 Service Fabric 시나리오를 테스트하는 데 필요한 최소 세 개의 노드가 있습니다.
3. **인증서** 탭을 선택합니다. 이 탭에서 클러스터의 인증서를 보호하는 데 사용할 암호를 입력합니다. 이 인증서는 클러스터를 안전하게 보호하는 데 도움이 됩니다. 인증서를 저장하려는 위치의 경로를 수정할 수도 있습니다. 또한 응용 프로그램을 클러스터에 게시하는 데 필요한 단계이므로 Visual Studio에서 사용자에 대한 인증서를 가져올 수도 있습니다.
4. **VM 세부 정보** 탭을 선택합니다. 클러스터를 구성하는 VM(Virtual Machines)에 사용할 암호를 지정합니다. 사용자 이름과 암호를 사용하여 VM에 원격으로 연결할 수 있습니다. 또한 VM 컴퓨터 크기를 선택해야 하며, 필요한 경우 VM 이미지를 변경할 수도 있습니다.
5. **고급** 탭에서 클러스터와 함께 만든 Azure 부하 분산 장치에서 열려는 포트 목록을 수정할 수 있습니다.  이전 단계에서 발견한 VotingWeb 서비스 엔드포인트를 추가합니다. 또한 응용 프로그램 로그 파일을 라우팅하려면 기존 Application Insights 키를 추가할 수도 있습니다.
6. 설정 수정을 마쳤으면 **만들기** 단추를 선택합니다. 만들기가 완료되는 데 몇 분이 걸립니다. 클러스터가 완전히 만들어지면 출력 창에 표시됩니다.

![클러스터 만들기 대화 상자](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

## <a name="deploy-the-sample-application"></a>샘플 응용 프로그램 배포
사용하려는 클러스터가 준비되면 응용 프로그램 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

게시가 완료되면 브라우저를 통해 응용 프로그램에 요청을 보낼 수 있습니다.

기본 설정 브라우저를 열고, 클러스터 주소를 입력합니다(포트 정보가 없는 연결 끝점. 예: win1kw5649s.westus.cloudapp.azure.com).

이제 응용 프로그램을 로컬로 실행할 때와 동일한 결과가 표시됩니다.

![클러스터에서 API 응답](./media/service-fabric-tutorial-deploy-app-to-party-cluster/response-from-cluster.png)

## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Visual Studio에서 클러스터 만들기
> * Visual Studio를 사용하여 원격 클러스터에 응용 프로그램 배포

다음 자습서를 진행합니다.
> [!div class="nextstepaction"]
> [HTTPS 사용](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
