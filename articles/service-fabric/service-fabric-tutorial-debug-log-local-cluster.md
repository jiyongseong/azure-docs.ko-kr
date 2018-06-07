---
title: 로컬 Azure Service Fabric 클러스터에서 Java 응용 프로그램 디버그 | Microsoft Docs
description: 이 자습서에서는 로컬 클러스터에서 실행되는 Service Fabric Java 응용 프로그램에서 로그를 가져오고 디버그하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: mfussell
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
ms.openlocfilehash: 7e06048da4db121136bbbb7cd155532f86015da1
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
#  <a name="tutorial-debug-a-java-application-deployed-on-a-local-service-fabric-cluster"></a>자습서: 로컬 Service Fabric 클러스터에 배포된 Java 응용 프로그램 디버그 
이 자습서는 시리즈의 2부입니다. Eclipse를 사용하여 Service Fabric 응용 프로그램에 원격 디버거를 연결하는 방법을 알아봅니다. 또한 개발자를 위해 편리한 위치에서 실행 중인 응용 프로그램에서 로그를 리디렉션하는 방법을 알아봅니다.

시리즈 2부에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * Eclipse를 사용하여 Java 응용 프로그램 디버그
> * 구성 가능한 위치로 로그 리디렉션

이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> *  [Java Service Fabric Reliable Services 응용 프로그램 빌드](service-fabric-tutorial-create-java-app.md)
> * 로컬 클러스터에서 응용 프로그램 배포 및 디버그
> * [Azure 클러스터에 응용 프로그램 배포](service-fabric-tutorial-java-deploy-azure.md)
> * [응용 프로그램에 대한 모니터링 및 진단 설정](service-fabric-tutorial-java-elk.md)
> * [CI/CD를 설정합니다](service-fabric-tutorial-java-jenkins.md).

## <a name="prerequisites"></a>필수 조건
이 자습서를 시작하기 전에:
- [Mac](service-fabric-get-started-mac.md) 또는 [Linux](service-fabric-get-started-linux.md)용 개발 환경을 설정합니다. 지침에 따라 Eclipse 플러그 인, Gradle, Service Fabric SDK 및 Service Fabric CLI(sfctl)를 설치합니다.

## <a name="download-the-voting-sample-application"></a>투표 응용 프로그램 예제 다운로드
[이 자습서 시리즈의 1부](service-fabric-tutorial-create-java-app.md)에서 투표 예제 응용 프로그램을 빌드하지 않은 경우 다운로드할 수 있습니다. 명령 창에서 다음 명령을 실행하여 로컬 컴퓨터에 샘플 앱 리포지토리를 복제합니다.

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart
```

로컬 개발 클러스터에 솔루션을 [빌드하고 배포합니다](service-fabric-tutorial-create-java-app.md#deploy-application-to-local-cluster).

## <a name="debug-java-application-using-eclipse"></a>Eclipse를 사용하여 Java 응용 프로그램 디버그

1. 컴퓨터에서 Eclipse IDE를 열고 **파일 -> 가져오기...**를 클릭합니다.

2. 팝업 창에서 **일반 -> 작업 영역에서 기존 프로젝트** 옵션을 선택하고 다음을 누릅니다. 

3. 프로젝트 가져오기 창에서 **루트 디렉터리 선택** 옵션을 선택하고 **Voting** 디렉터리를 선택합니다. 자습서 시리즈 1부를 따른 경우 **Voting** 디렉터리는 **Eclipse 작업 공간** 디렉터리 내에 있습니다. 

4. 디버그하려는 서비스의 entryPoint.sh를 원격 디버그 매개 변수를 사용하여 Java 프로세스를 시작하도록 업데이트합니다. 이 자습서의 경우 상태 비저장 프런트 엔드는 다음과 같이 사용됩니다. *Voting/VotingApplication/VotingWebPkg/Code/entryPoint.sh*. 이 예제에서 포트 8001은 디버깅을 위해 설정되었습니다.

    ```bash
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=y -Djava.library.path=$LD_LIBRARY_PATH -jar VotingWeb.jar
    ```

5. 인스턴스 수 또는 디버그하는 서비스의 복제본 수를 1로 설정하여 응용 프로그램 매니페스트를 업데이트합니다. 이 설정은 디버깅에 사용되는 포트에 대한 충돌을 방지합니다. 예를 들어, 상태 비저장 서비스의 경우 ``InstanceCount="1"``을 설정하고 상태 저장 서비스의 경우 `` TargetReplicaSetSize="1" MinReplicaSetSize="1"``과 같이 대상과 최소 복제본 세트 크기를 1로 설정합니다.

6. Eclipse IDE에서 **실행 -> 구성 디버그 -> 원격 Java 응용 프로그램**을 선택하고 **새** 단추를 누르고 속성을 다음과 같이 설정하고 **적용**을 클릭합니다.

    ```
    Name: Voting
    Project: Voting
    Connection Type: Standard
    Host: localhost
    Port: 8001
    ```

7. *Voting/VotingWeb/src/statelessservice/HttpCommunicationListener.java* 파일의109번째 줄에 중단점을 놓습니다. 

8. 패키지 탐색기에서 **Voting** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Service Fabric -> 응용 프로그램 게시...**를 클릭합니다. 

9. **응용 프로그램 게시** 창의 드롭다운 메뉴에서 **Local.json**을 선택하고 **게시**를 클릭합니다.

10. Eclipse IDE에서 **실행 -> 구성 디버그 -> 원격 Java 응용 프로그램**을 선택하고 만든 **Voting** 구성을 클릭하고 **디버그**를 클릭합니다.

11. 중단점을 적중하려면 웹 브라우저로 이동해 **localhost:8080**에 액세스하고 Eclipse에 **관점 디버그**를 입력합니다.

## <a name="redirect-application-logs-to-custom-location"></a>응용 프로그램 로그를 사용자 지정 위치로 리디렉션

다음 단계는 기본 */var/log/syslog* 위치에서 사용자 지정 위치로 응용 프로그램 로그를 리디렉션하는 방법을 설명합니다. 

1. 현재 Service Fabric Linux 클러스터에서 실행 중인 응용 프로그램은 단일 로그 파일 선택을 지원합니다. 결과적으로, 해당 로그는 항상 */tmp/mysfapp0.0.log*로 이동합니다. 다음 위치 *Voting/VotingApplication/VotingWebPkg/Code/logging.properties*에 logging.properties이라는 파일을 만들고 다음 내용을 추가합니다.

    ```
    handlers = java.util.logging.FileHandler
    
    java.util.logging.FileHandler.level = ALL
    java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
    
    # This value specifies your custom location. You will have to ensure this path has read and write access by the process running the SF Application
    java.util.logging.FileHandler.pattern = /tmp/mysfapp0.0.log
    ```

2. Java 실행 명령에 대한 다음 매개 변수를 *Voting/VotingApplication/VotingWebPkg/Code/entryPoint.sh*에 추가합니다.

    ```bash
    -Djava.util.logging.config.file=logging.properties
    ```
    
    다음 예제에서는 샘플 실행을 보여줍니다.
    
    ```bash
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=y -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=logging.properties -jar VotingWeb.jar
    ```

이 단계에서는 Service Fabric Java 응용 프로그램을 개발하는 동안 응용 프로그램 로그를 디버그하고 액세스하는 방법을 알아봅니다. 

## <a name="next-steps"></a>다음 단계
자습서의 이 부분에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Eclipse를 사용하여 Java 응용 프로그램 디버그
> * 구성 가능한 위치로 로그 리디렉션

다음 자습서를 진행합니다.
> [!div class="nextstepaction"]
> [Azure에 응용 프로그램 배포](service-fabric-tutorial-java-deploy-azure.md)