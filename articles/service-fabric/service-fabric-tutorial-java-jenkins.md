---
title: Azure Service Fabric Java 응용 프로그램에 대한 CI/CD 설정 | Microsoft Docs
description: 이 자습서에서는 Java Service Fabric 응용 프로그램을 배포하기 위해 Jenkins를 사용하여 연속 통합을 설정하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/26/2018
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: bd986b8741b55a10018f7400c9415e7aedfbf119
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="tutorial-set-up-a-jenkins-environment-with-service-fabric"></a>자습서: Service Fabric을 사용하여 Jenkins 환경 설정
이 자습서는 시리즈의 5부입니다. 여기서는 Jenkins를 사용하여 응용 프로그램에 업그레이드를 배포하는 방법을 설명합니다. 이 자습서에서는 Service Fabric Jenkins 플러그 인이 클러스터에 응답 응용 프로그램을 배포하기 위해 Voting 응용프로그램을 호스팅하는 Github 리포지토리와 결합하는 데 사용됩니다. 

시리즈 5부에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * 컴퓨터에 Service Fabric Jenkins 컨테이너 배포
> * Service Fabric에 배포하기 위한 Jenkins 환경 설정
> * 응용 프로그램 업그레이드

이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * [Java Service Fabric Reliable Services 응용 프로그램 빌드](service-fabric-tutorial-create-java-app.md)
> * [로컬 클러스터에서 응용 프로그램 배포 및 디버그](service-fabric-tutorial-debug-log-local-cluster.md)
> * [Azure 클러스터에 응용 프로그램 배포](service-fabric-tutorial-java-deploy-azure.md)
> * [응용 프로그램에 대한 모니터링 및 진단 설정](service-fabric-tutorial-java-elk.md)
> * CI/CD 설정


## <a name="prerequisites"></a>필수 조건
- [Git 다운로드 페이지](https://git-scm.com/downloads)에서 로컬 컴퓨터에 Git를 설치합니다. Git에 대한 자세한 내용은 [Git 설명서](https://git-scm.com/docs)를 참조합니다.
- [Jenkins](https://jenkins.io/)에 대한 작업 정보를 소유합니다.
- [GitHub](https://github.com/) 계정을 만들고 GitHub 사용 방법을 알아봅니다.
- 사용자 컴퓨터에 [Docker](https://www.docker.com/community-edition)를 설치합니다.

## <a name="pull-and-deploy-service-fabric-jenkins-container-image"></a>Service Fabric Jenkins 컨테이너 이미지 끌어오기 및 배포
Service Fabric 클러스터 내부 또는 외부에서 Jenkins를 설정할 수 있습니다. 다음 지침에서는 제공된 Docker 이미지를 사용하여 클러스터 외부에 설정하는 방법을 설명합니다. 그러나 미리 구성된 Jenkins 빌드 환경이 사용될 수도 있습니다. 다음 컨테이너 이미지는 Service Fabric 플러그 인과 함께 설치되며 즉시 Service Fabric을 사용할 준비가 돼 있습니다. 

1. Service Fabric Jenkins 컨테이너 이미지를 끌어옵니다. ``docker pull rapatchi/jenkins:v10`` 이 이미지는 미리 설치된 Service Fabric Jenkins 플러그 인과 함께 제공됩니다.

2. 탑재된 로컬 컴퓨터에 인증서가 있는 위치를 사용하여 컨테이너 이미지 실행

    ```bash
    docker run -itd -p 8080:8080 -v /Users/suhuruli/Documents/Work/Samples/service-fabric-java-quickstart/AzureCluster:/tmp/myCerts rapatchi/jenkins:v10
    ```

3. 컨테이너 이미지 인스턴스 ID를 가져옵니다. 명령 ``docker ps –a``를 사용하여 모든 Docker 컨테이너를 나열할 수 있습니다.

4. 다음 명령을 실행하여 Jenkins 인스턴스의 암호를 검색합니다.

    ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```

    컨테이너 ID가 2d24a73b5964이면 2d24를 사용합니다.
    * 이 암호는 ``http://<HOST-IP>:8080``인 포털에서 Jenkins 대시보드에 로그인하는 데 필요합니다.
    * 처음으로 로그인한 후 고유의 사용자 계정을 만들거나 관리자 계정을 사용할 수 있습니다.
    
5. [새 SSH 키 생성 및 SSH 에이전트에 추가](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)에서 설명한 단계를 사용하여 Jenkins를 사용하도록 GitHub을 설정합니다. 명령은 Docker 컨테이너에서 실행되기 때문에 Linux 환경에 대한 지침을 따릅니다. 
    * GitHub에서 제공한 지침을 사용하여 SSH 키를 생성합니다. 다음으로 리포지토리를 호스팅하는 GitHub 계정에 SSH 키를 추가합니다.
    * (호스트가 아닌) Jenkins Docker 셸에서 이전 링크에 언급된 명령을 실행합니다.
    * 호스트에서 Jenkins 셸에 로그인하려면 다음 명령을 사용합니다.

    ```sh
    docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
    ```

    Jenkins 컨테이너 이미지가 호스팅되는 클러스터 또는 컴퓨터에 공용 연결 IP가 있는지 확인합니다. 공용 IP를 가진 경우 Jenkins 인스턴스가 GitHub에서 알림을 수신하게 할 수 있습니다.    

## <a name="create-and-configure-a-jenkins-job"></a>Jenkins 작업 만들기 및 구성

1. 첫째, Github에서 Voting 프로젝트를 호스팅하는 데 사용할 수 있는 리포지토리가 없는 경우 만드세요. 리포지토리는 이 자습서의 나머지에 대해 **dev_test**라고 합니다.

2. Jenkins 대시보드에 **새 항목**을 만듭니다.

3. 항목 이름을 입력합니다(예: **MyJob**). **자유로운 프로젝트**를 선택하고 **확인**을 클릭합니다.

4. 작업 페이지로 이동하여 **구성**을 클릭합니다.

   a. 일반 섹션에서 **GitHub 프로젝트**에 대한 확인란을 선택하고 GitHub 프로젝트 URL을 지정합니다. 이 URL은 Jenkins CI/CD(연속 통합, 연속 배포) 흐름과 통합하려는 Service Fabric Java 응용 프로그램을 호스트합니다(예: ``https://github.com/testaccount/dev_test``).

   나. **소스 코드 관리** 섹션 아래에서 **Git**를 선택합니다. Jenkins CI/CD 흐름과 통합하려는 Service Fabric Java 응용 프로그램을 호스트하는 리포지토리 URL을 지정합니다(예: *https://github.com/testaccount/dev_test.git*). 또한 여기에서 빌드할 분기를 지정할 수 있습니다(예: **/master**).

5. Jenkins와 통신할 수 있도록 리포지토리를 호스팅하는 *GitHub*을 구성합니다. 다음 단계를 사용하세요.

   a. GitHub 리포지토리 페이지로 이동합니다. **설정** > **통합 및 서비스**로 이동합니다.

   나. **서비스 추가**를 선택하고 **Jenkins**를 입력하고 **Jenkins-GitHub 플러그 인**을 선택합니다.

   다. Jenkins Webhook URL을 입력합니다(기본적으로 ``http://<PublicIPorFQDN>:8081/github-webhook/``이여야 함). **서비스 추가/업데이트**를 클릭합니다.

   d. 테스트 이벤트가 사용자의 Jenkins 인스턴스로 전송됩니다. GitHub의 웹후크에서 녹색 확인 표시가 나타나고 프로젝트가 빌드됩니다.

   ![Service Fabric Jenkins 구성](./media/service-fabric-tutorial-java-jenkins/jenkinsconfiguration.png)

6. **트리거 빌드** 섹션 아래에서 원하는 빌드 옵션을 선택합니다. 이 예에서는 리포지토리에 일부 푸시가 발생할 때마다 빌드를 트리거하려고 합니다. 따라서 **GITScm 폴링에 대한 GitHub 후크 트리거**를 선택합니다.

7. **빌드 섹션** 아래 **빌드 단계 추가** 드롭다운에서 **Gradle 스크립트 호출** 옵션을 선택합니다. 고급 메뉴가 열리는 위젯에서 응용 프로그램에 대한 **루트 빌드 스크립트**의 경로를 지정합니다. 지정된 경로에서 build.gradle을 선택하면 이에 따라 적절하게 작동합니다.

    ![Service Fabric Jenkins 빌드 작업](./media/service-fabric-tutorial-java-jenkins/jenkinsbuildscreenshot.png)
  
8. **빌드 후 작업** 드롭다운에서 **Service Fabric 프로젝트 배포**를 선택합니다. 여기에서 Jenkins가 컴파일한 Service Fabric 응용 프로그램을 배포한 클러스터 세부 정보를 제공해야 합니다. 인증서 경로가 볼륨이 탑재(/tmp/myCerts)됐던 위치에 있습니다. 
   
    또한 응용 프로그램을 배포하는 데 사용된 추가 세부 정보를 제공할 수 있습니다. 응용프로그램 세부 정보에 대한 예제는 다음 스크린샷을 참조하세요.

    ![Service Fabric Jenkins 빌드 작업](./media/service-fabric-tutorial-java-jenkins/sfjenkins.png)

    > [!NOTE]
    > 이 클러스터는 Service Fabric을 사용하여 Jenkins 컨테이너 이미지를 배포하는 경우에 Jenkins 컨테이너 응용 프로그램을 호스팅하는 것과 동일할 수 있습니다.
    >

## <a name="update-your-existing-application"></a>기존 응용 프로그램 업데이트 

1. **Service Fabric Voting 샘플 V2**를 사용하여 *VotingApplication/VotingWebPkg/Code/wwwroot/index.html* 파일의 HTML의 제목을 업데이트합니다. 

    ```html 
    <div ng-app="VotingApp" ng-controller="VotingAppController" ng-init="refresh()">
        <div class="container-fluid">
            <div class="row">
                <div class="col-xs-8 col-xs-offset-2 text-center">
                    <h2>Service Fabric Voting Sample V2</h2>
                </div>
            </div>
        </div>
    </div>
    ```

2. **ApplicationTypeVersion** 및 **ServiceManifestVersion** 버전을 *Voting/VotingApplication/ApplicationManifest.xml* 파일에서 **2.0.0**으로 업데이트합니다. 

    ```xml
    <?xml version="1.0" encoding="utf-8" standalone="no"?>
    <ApplicationManifest xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingApplicationType" ApplicationTypeVersion="2.0.0">
      <Description>Voting Application</Description>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="2.0.0"/>
      </ServiceManifestImport>
      <ServiceManifestImport>
            <ServiceManifestRef ServiceManifestName="VotingDataServicePkg" ServiceManifestVersion="1.0.0"/>
        </ServiceManifestImport>
        <DefaultServices>
          <Service Name="VotingWeb">
             <StatelessService InstanceCount="1" ServiceTypeName="VotingWebType">
                <SingletonPartition/>
             </StatelessService>
          </Service>      
       <Service Name="VotingDataService">
                <StatefulService MinReplicaSetSize="3" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="3">
                    <UniformInt64Partition HighKey="9223372036854775807" LowKey="-9223372036854775808" PartitionCount="1"/>
                </StatefulService>
            </Service>
        </DefaultServices>      
    </ApplicationManifest>
    ```

3. **ServiceManifest**에서 **버전** 필드 및 *Voting/VotingApplication/VotingWebPkg/ServiceManifest.xml* 파일의 **CodePackage** 태그에서 **버전** 필드를 **2.0.0**으로 업데이트합니다.

    ```xml
    <CodePackage Name="Code" Version="2.0.0">
    <EntryPoint>
        <ExeHost>
        <Program>entryPoint.sh</Program>
        </ExeHost>
    </EntryPoint>
    </CodePackage>
    ```

4. 응용 프로그램 업그레이드를 수행하는 Jenkins 작업을 초기화하려면 Github 리포지토리에 새 변경 내용을 푸시하십시오. 

5. Service Fabric Explorer에서 **응용 프로그램** 드롭다운을 클릭합니다. 업그레이드의 상태를 확인하려면 **진행 중인 업그레이드** 탭을 클릭합니다.

    ![업그레이드 진행 중](./media/service-fabric-tutorial-create-java-app/upgradejava.png)

6. **http://\<Host-IP>:8080**에 액세스하는 경우 완벽한 기능으로 Voting 응용 프로그램이 작동되고 실행됩니다. 

    ![로컬 Voting 앱](./media/service-fabric-tutorial-java-jenkins/votingv2.png)

## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 컴퓨터에 Service Fabric Jenkins 컨테이너 배포
> * Service Fabric에 배포하기 위한 Jenkins 환경 설정
> * 응용 프로그램 업그레이드

* 다른 [Java 샘플](https://github.com/Azure-Samples/service-fabric-java-getting-started) 체크 아웃