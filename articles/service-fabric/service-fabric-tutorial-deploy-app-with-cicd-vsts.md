---
title: 연속 통합으로 Azure Service Fabric 응용 프로그램 배포(Team Services) | Microsoft Docs
description: 이 자습서에서는 Visual Studio Team Services를 사용하여 Service Fabric 응용 프로그램에 대한 지속적인 통합 및 배포를 설정하는 방법을 알아봅니다.  Azure에서 Service Fabric 클러스터에 응용 프로그램을 배포합니다.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/13/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 5b61b7f89c127b297f058082d86952f2a45d766a
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="tutorial-deploy-an-application-with-cicd-to-a-service-fabric-cluster"></a>자습서: Service Fabric 클러스터에 CI/CD로 응용 프로그램 배포
이 자습서는 시리즈의 4부로, Visual Studio Team Services를 사용하여 Azure Service Fabric 응용 프로그램에 대한 연속 통합 및 배포를 설정하는 방법을 설명합니다.  기존 Service Fabric 응용 프로그램이 필요하며 [.NET 응용 프로그램 빌드](service-fabric-tutorial-create-dotnet-app.md)에서 만든 응용 프로그램을 예제로 사용합니다.

시리즈 3부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 프로젝트에 소스 제어 추가
> * Team Services에서 빌드 정의 만들기
> * Team Services에서 릴리스 정의 만들기
> * 응용 프로그램 자동 배포 및 업그레이드

이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * [.NET Service Fabric 응용 프로그램 빌드](service-fabric-tutorial-create-dotnet-app.md)
> * [응용 프로그램을 원격 클러스터에 배포](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [ASP.NET Core 프런트 엔드 서비스에 HTTPS 엔드포인트 추가](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * Visual Studio Team Services를 사용하여 CI/CD 구성
> * [응용 프로그램에 대한 모니터링 및 진단 설정](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>필수 조건
이 자습서를 시작하기 전에:
- Azure 구독이 없는 경우 [평가판 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- [Visual Studio 2017을 설치](https://www.visualstudio.com/)하고 **Azure 개발**과 **ASP.NET 및 웹 개발** 워크로드를 설치합니다.
- [Service Fabric SDK를 설치](service-fabric-get-started.md)합니다.
- 예를 들어 [이 자습서를 따라](service-fabric-tutorial-create-vnet-and-windows-cluster.md) Windows Service Fabric 클러스터를 Azure에 만듭니다.
- [Team Services 계정](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)을 만듭니다.

## <a name="download-the-voting-sample-application"></a>투표 응용 프로그램 예제 다운로드
[이 자습서 시리즈의 1부](service-fabric-tutorial-create-dotnet-app.md)에서 투표 예제 응용 프로그램을 빌드하지 않은 경우 다운로드할 수 있습니다. 명령 창에서 다음 명령을 실행하여 로컬 컴퓨터에 샘플 앱 리포지토리를 복제합니다.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="prepare-a-publish-profile"></a>게시 프로필 준비
이제 [응용 프로그램을 만들었고](service-fabric-tutorial-create-dotnet-app.md) [응용 프로그램을 Azure에 배포](service-fabric-tutorial-deploy-app-to-party-cluster.md)했으므로 연속 통합을 설정할 준비가 되었습니다.  먼저, Team Services 내에서 실행할 배포 프로세스에 사용할 게시 프로필을 응용 프로그램 내에서 준비하는 것입니다.  게시 프로필은 이전에 만든 클러스터를 대상으로 지정하도록 구성해야 합니다.  Visual Studio를 시작하고 기존 Service Fabric 응용 프로그램 프로젝트를 엽니다.  **솔루션 탐색기**에서 응용 프로그램을 마우스 오른쪽 단추로 클릭하고 **게시...** 를 선택합니다.

연속 통합 워크플로로 사용할 대상 프로필을 응용 프로그램 프로젝트 내에서 선택합니다(예: 클라우드).  클러스터 연결 끝점을 지정합니다.  **응용 프로그램 업그레이드** 확인란을 선택하여 Team Services의 각 배포에 대해 응용 프로그램이 업그레이드되도록 합니다.  **저장** 하이퍼링크를 클릭하여 설정을 게시 프로필에 저장한 후 **취소**를 클릭하여 대화 상자를 닫습니다.  

![푸시 프로필][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-team-services-git-repo"></a>새 Team Services Git 리포지토리에 Visual Studio 솔루션 공유
응용 프로그램 소스 파일을 Team Services의 팀 프로젝트에 공유하여 빌드를 생성할 수 있습니다.  

Visual Studio의 오른쪽 하단의 상태 표시줄에서 **소스 제어에 추가** -> **Git**을 선택하여 프로젝트 대한 새 로컬 Git 리포지토리를 만듭니다. 

**팀 탐색기**의 **푸시** 보기에서 **Visual Studio Team Services에 푸시** 아래에 있는 **Git 리포지토리 게시** 단추를 선택합니다.

![Git 리포지토리 푸시][push-git-repo]

사용자의 전자 메일을 확인하고 **Team Services 도메인** 드롭다운에서 계정을 선택합니다. 리포지토리 이름을 입력하고 **리포지토리 게시**를 선택합니다.

![Git 리포지토리 푸시][publish-code]

리포지토리를 게시하면 사용자 계정에 로컬 리포지토리와 같은 이름으로 새 팀 프로젝트가 만들어집니다. 기존 팀 프로젝트에서 리포지토리를 만들려면 **리포지토리** 이름 옆에서 **고급**을 클릭하고 팀 프로젝트를 선택합니다. **See it on the web(웹에서 보기)** 을 선택하여 웹에서 코드를 볼 수 있습니다.

## <a name="configure-continuous-delivery-with-vsts"></a>VSTS를 사용한 지속적인 업데이트 구성
Team Services 빌드 정의는 순차적으로 실행되는 빌드 단계 집합으로 구성된 워크플로를 설명합니다. Service Fabric 응용 프로그램 패키지 및 기타 아티팩트를 생성하는 빌드 정의를 만들어 Service Fabric 클러스터를 배포합니다. [Team Services 빌드 정의](https://www.visualstudio.com/docs/build/define/create)에 대해 자세히 알아봅니다. 

Team Services 릴리스 정의에서는 응용 프로그램 패키지를 클러스터에 배포하는 워크플로를 설명합니다. 빌드 정의와 릴리스 정의를 함께 사용할 경우 소스 파일로 시작하여 클러스터에서 실행 중인 응용 프로그램에서 종료할 때까지 전체 워크플로를 실행합니다. Team Services [릴리스 정의](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)에 대해 자세히 알아봅니다.

### <a name="create-a-build-definition"></a>빌드 정의 만들기
웹 브라우저를 열고 [https://&lt;myaccount&gt;.visualstudio.com/Voting/Voting%20Team/_git/Voting](https://myaccount.visualstudio.com/Voting/Voting%20Team/_git/Voting)에서 새 팀 프로젝트로 이동합니다. 

**빌드 및 릴리스** 탭, **빌드** 및 **+ 새로운 정의 만들기**를 차례로 선택합니다.  **템플릿 선택**에서 **Azure Service Fabric 응용 프로그램** 템플릿을 선택하고 **적용**을 클릭합니다. 

![빌드 템플릿 선택][select-build-template] 

**작업**에서 **에이전트 큐**로 "호스트된 VS2017"을 입력합니다. 

![태스크 선택][save-and-queue]

**트리거** 아래에서 **상태 트리거**를 설정하여 연속 통합을 사용합니다.  **저장 및 큐**를 선택하여 수동으로 빌드를 시작합니다.  

![트리거 선택][save-and-queue2]

푸시 또는 체크 인 시 트리거도 빌드합니다. 빌드 진행률을 확인하려면 **빌드** 탭으로 전환합니다.  빌드가 성공적으로 실행되는지 확인한 후 응용 프로그램을 클러스터에 배포하는 릴리스 정의를 정의합니다. 

### <a name="create-a-release-definition"></a>릴리스 정의 만들기  

**빌드 및 릴리스** 탭, **릴리스** 및 **+ 새로운 정의 만들기**를 차례로 선택합니다.  **템플릿 선택**에서 목록의 **Azure Service Fabric 배포** 템플릿을 선택하고 **적용**을 선택합니다.  

![릴리스 템플릿 선택][select-release-template]

**작업**->**환경 1** 및 **+새로 만들기**를 차례로 선택하여 새 클러스터 연결을 추가합니다.

![클러스터 연결 추가][add-cluster-connection]

**새 Service Fabric 연결 추가** 보기에서 **인증서 기반** 또는 **Azure Active Directory** 인증을 선택합니다.  "Mysftestcluster" 및 "tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000"의 클러스터 끝점(또는 배포 중인 클러스터의 끝점)의 연결 이름을 지정합니다. 

인증서 기반 인증의 경우 클러스터를 만드는 데 사용된 서버 인증서의 **서버 인증서 지문**을 추가합니다.  **클라이언트 인증서**에서 base-64 인코딩된 클라이언트 인증서 파일을 추가합니다. base-64 인코딩된 해당 인증서의 표현을 가져오는 방법에 대한 정보는 해당 필드에서 도움말 팝업을 참조하세요. 인증서의 **암호**도 추가합니다.  별도 클라이언트 인증서가 없는 경우 클러스터 또는 서버 인증서를 사용할 수 있습니다. 

Azure Active Directory 자격 증명의 경우 사용하려는 클러스터 및 자격 증명을 만드는 데 사용된 서버 인증서의 **서버 인증서 지문**을 추가하여 **사용자 이름** 및 **암호** 필드에서 클러스터에 연결합니다. 

**추가**를 클릭하여 클러스터 연결을 저장합니다.

다음으로 릴리스 정의가 빌드에서 출력을 찾을 수 있도록 파이프라인에 빌드 아티팩트를 추가합니다. **파이프라인** 및 **아티팩트**->**+추가**를 선택합니다.  **원본(빌드 정의)** 에서 이전에 만든 빌드 정의를 선택합니다.  **추가**를 클릭하여 빌드 아티팩트를 저장합니다.

![아티팩트 추가][add-artifact]

빌드가 완료될 때 릴리스가 자동으로 생성하도록 지속적인 배포 트리거를 사용하도록 설정합니다. 아티팩트에서 번개 아이콘을 클릭하고, 트리거를 사용하고, **저장**을 클릭하여 릴리스 정의를 저장합니다.

![트리거 사용][enable-trigger]

**+릴리스** -> **릴리스 만들기** -> **만들기**를 선택하여 릴리스를 수동으로 만듭니다.  배포에 성공했고 클러스터에서 응용 프로그램이 실행 중인지 확인합니다.  웹 브라우저를 열고 [http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/)로 이동합니다.  응용 프로그램 버전을 확인합니다. 이 예제에서는 “1.0.0.20170616.3”입니다. 

## <a name="commit-and-push-changes-trigger-a-release"></a>변경 내용 커밋 및 푸시, 릴리스 트리거
Team Services에 일부 코드 변경을 체크 인하여 연속 통합 파이프라인이 작동하는지 확인합니다.    

코드를 작성하면 Visual Studio에서 변경 내용을 자동으로 추적합니다. 오른쪽 하단의 상태 표시줄에서 보류 중인 변경 내용 아이콘(![Pending][pending])을 선택하여 로컬 Git 리포지토리로 변경 내용을 커밋합니다.

팀 탐색기의 **변경 내용** 보기에서 업데이트를 설명하는 메시지를 추가하고 변경 내용을 커밋합니다.

![모두 커밋][changes]

팀 탐색기에서 게시 취소된 변경 내용 상태 표시줄 아이콘(![게시 취소된 변경 내용][unpublished-changes]) 또는 동기화 보기를 선택합니다. **푸시**를 선택하여 Team Services/TFS에서 코드를 업데이트합니다.

![변경 내용 푸시][push]

Team Services에 변경 내용을 푸시하면 빌드가 자동으로 트리거됩니다.  빌드 정의가 성공적으로 완료되면 릴리스가 자동으로 만들어지고 클러스터에서 응용 프로그램 업그레이드가 시작됩니다.

빌드 진행률을 확인하려면 Visual Studio의 **팀 탐색기**에서 **빌드** 탭으로 전환합니다.  빌드가 성공적으로 실행되는지 확인한 후 응용 프로그램을 클러스터에 배포하는 릴리스 정의를 정의합니다.

배포에 성공했고 클러스터에서 응용 프로그램이 실행 중인지 확인합니다.  웹 브라우저를 열고 [http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/)로 이동합니다.  응용 프로그램 버전을 확인합니다. 이 예제에서는 "1.0.0.20170815.3"입니다.

![Service Fabric Explorer][sfx1]

## <a name="update-the-application"></a>응용 프로그램 업데이트
응용 프로그램에서 코드를 변경합니다.  이전 단계에 따라 변경 내용을 저장 및 커밋합니다.

응용 프로그램 업그레이드가 시작되면 Service Fabric Explorer에서 업그레이드 진행률을 확인할 수 있습니다.

![Service Fabric Explorer][sfx2]

응용 프로그램 업그레이드에는 몇 분 정도 걸릴 수 있습니다. 업그레이드가 완료되면 응용 프로그램이 다음 버전으로 실행됩니다.  이 예제에서는 "1.0.0.20170815.4"입니다.

![Service Fabric Explorer][sfx3]

## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 프로젝트에 소스 제어 추가
> * 빌드 정의 만들기
> * 릴리스 정의 만들기
> * 응용 프로그램 자동 배포 및 업그레이드

다음 자습서를 진행합니다.
> [!div class="nextstepaction"]
> [응용 프로그램에 대한 모니터링 및 진단 설정](service-fabric-tutorial-monitoring-aspnet.md) 


<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishCode.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectBuildTemplate.png
[save-and-queue]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue.png
[save-and-queue2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue2.png
[select-release-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectReleaseTemplate.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddClusterConnection.png
[add-artifact]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddArtifact.png
[enable-trigger]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/EnableTrigger.png
[sfx1]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Push.png
[continuous-delivery-with-VSTS]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpointDialog.png
