---
title: Eclipse용 Azure Service Fabric 플러그 인 | Microsoft Docs
description: Eclipse용 Azure Service Fabric 플러그 인을 시작합니다.
services: service-fabric
documentationcenter: java
author: rapatchi
manager: timlt
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/06/2018
ms.author: rapatchi
ms.openlocfilehash: f0c97fdec07a0b882c1c67d4278a0c3fb7b1a5cd
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207396"
---
# <a name="service-fabric-plug-in-for-eclipse-java-application-development"></a>Eclipse Java 응용 프로그램 배포를 위한 Azure Service Fabric 플러그 인
Eclipse는 가장 널리 사용되는 Java 개발자를 위한 IDE(통합 개발 환경) 중 하나입니다. 이 문서에서는 Azure Service Fabric 작업을 수행하기 위해 Eclipse 개발 환경을 설정하는 방법에 대해 설명합니다. Service Fabric 플러그 인을 설치하고 Service Fabric 응용 프로그램을 만들며 Service Fabric 응용 프로그램을 Eclipse의 로컬 또는 원격 Service Fabric 클러스터에 배포하는 방법을 알아봅니다. 

> [!NOTE]
> Eclipse 플러그 인은 현재 Windows에서 지원되지 않습니다. 

## <a name="install-or-update-the-service-fabric-plug-in-in-eclipse"></a>Eclipse에서 Service Fabric 플러그 인 설치 또는 업데이트
Eclipse에서 Service Fabric 플러그 인을 설치할 수 있습니다. 플러그 인은 Java 서비스를 빌드하고 배포하는 프로세스를 간소화할 수 있습니다.

> [!IMPORTANT]
> Service Fabric 플러그 인에는 Eclipse Neon 이상 버전이 필요합니다. Eclipse의 버전을 확인하는 방법은 이 부분 뒤에 나오는 지침을 참조하십시오. 이전 버전의 Eclipse가 설치되어있는 경우 [Eclipse 사이트](https://www.eclipse.org)에서 최신 버전을 다운로드할 수 있습니다. 기존 Eclipse 설치 위에 설치하는 것(덮어쓰기)은 좋지 않습니다. 설치 관리자를 실행하기 전에 제거하거나 다른 디렉터리에 새 버전을 설치할 수 있습니다. 
> 
> Ubuntu의 경우 패키지 설치 관리자(`apt` 또는 `apt-get`)를 사용하는 대신 Eclipse 사이트에서 직접 설치하는 것이 좋습니다. 이렇게 하면 최신 버전의 Eclipse를 확보할 수 있습니다. 

[Eclipse 사이트](https://www.eclipse.org)에서 Eclipse Neon 이상을 설치합니다.  또한 Buildship의 버전 2.2.1 이상을 설치합니다(Service Fabric 플러그 인은 이전 버전의 Buildship과 호환되지 않음).
-   설치된 구성 요소의 버전을 확인하려면 Eclipse에서 **도움말** > **Eclipse 정보** > **설치 세부 정보**로 이동합니다.
-   Buildship을 업데이트하려면 [Eclipse Buildship: Gradle용 Eclipse 플러그 인][buildship-update]을 참조하세요.
-   Eclipse에 대한 업데이트를 확인하고 설치하려면 **도움말** > **업데이트 확인**으로 이동합니다.

Service Fabric 플러그 인을 설치하고, Eclipse에서 **도움말** > **새 소프트웨어 설치**로 이동합니다.
1. **작업 대상** 상자에서 **http://dl.microsoft.com/eclipse**을 입력합니다.
2. **추가**를 클릭합니다.
    ![Eclipse용 Service Fabric 플러그 인][sf-eclipse-plugin-install]
3. Service Fabric 플러그 인을 선택한 다음 **다음**을 클릭합니다.
4. 설치 단계를 완료한 다음 Microsoft 소프트웨어 사용 조건에 동의합니다.
  
Service Fabric 플러그 인이 이미 설치된 경우 최신 버전을 설치합니다. 
1. 사용 가능한 업데이트를 확인하려면 **도움말** > **Eclipse 정보** > **설치 세부 정보**로 이동합니다. 
2. 설치된 플러그 인 목록에서 Service Fabric을 선택하고 **업데이트**를 클릭합니다. 사용 가능한 업데이트가 설치됩니다.
3. Service Fabric 플러그 인을 업데이트한 후 Gradle 프로젝트를 새로 고칩니다.  마우스 오른쪽 단추로 **build.gradle**을 클릭한 다음, **새로 고침**을 선택합니다.

> [!NOTE]
> Service Fabric 플러그 인 설치 또는 업데이트가 느려지는 경우 Eclipse 설정 때문일 수 있습니다. Eclipse는 Eclipse 인스턴스에 등록되어 있는 사이트를 업데이트하는 모든 변경 내용에 대한 메타데이터를 수집합니다. Service Fabric 플러그 인 업데이트를 확인하고 설치하는 프로세스 의 속도를 촉진하려면 **사용 가능한 소프트웨어 사이트**로 이동합니다. Service Fabric 플러그 인 위치(http://dl.microsoft.com/eclipse/azure/servicefabric))를 가리키는 확인란을 제외하고 모든 사이트에 대한 확인란의 선택을 취소합니다.

> [!NOTE]
>Eclipse가 Mac에서 예상대로 작동하지 않는 경우 또는 슈퍼 사용자로 실행해야 하는 경우 **ECLIPSE_INSTALLATION_PATH** 폴더로 이동하고 **Eclipse.app/Contents/MacOS** 하위 폴더를 탐색합니다. `./eclipse`를 실행하여 Eclipse를 시작합니다.


## <a name="create-a-service-fabric-application-in-eclipse"></a>Eclipse에서 Service Fabric 응용 프로그램 만들기

1.  Eclipse에서 **파일** > **새로 만들기** > **기타**로 이동합니다. **Service Fabric 프로젝트**를 선택하고 **다음**을 클릭합니다.

    ![Service Fabric 새 프로젝트 페이지 1][create-application/p1]

2.  프로젝트의 이름을 입력하고 **다음**을 클릭합니다.

    ![Service Fabric 새 프로젝트 페이지 2][create-application/p2]

3.  템플릿 목록에서 **서비스 템플릿**을 선택합니다. 서비스 템플릿 유형(행위자, 상태 비저장, 컨테이너 또는 게스트 이진 파일)을 선택한 다음 **다음**을 클릭합니다.

    ![Service Fabric 새 프로젝트 페이지 3][create-application/p3]

4.  서비스 이름 및 서비스 세부 정보를 입력한 다음 **마침**을 클릭합니다.

    ![Service Fabric 새 프로젝트 페이지 4][create-application/p4]

5. 첫 번째 Service Fabric 프로젝트를 만들 경우 **연결된 큐브 뷰 열기** 대화 상자에서 **예**를 클릭합니다.

    ![Service Fabric 새 프로젝트 페이지 5][create-application/p5]

6.  새 프로젝트는 다음과 같습니다.

    ![Service Fabric 새 프로젝트 페이지 6][create-application/p6]

## <a name="build-and-deploy-a-service-fabric-application-in-eclipse"></a>Eclipse에서 Service Fabric 응용 프로그램 빌드 및 배포

1.  새 Service Fabric 응용 프로그램을 마우스 오른쪽 단추로 클릭한 다음 **Service Fabric**을 선택합니다.

    ![Service Fabric 마우스 오른쪽 클릭 메뉴][publish/RightClick]

2. 하위 메뉴에서 원하는 옵션을 선택합니다.
    -   정리하지 않고 응용 프로그램을 빌드하려면 **응용 프로그램 빌드**를 클릭합니다.
    -   응용 프로그램의 정리-빌드를 수행하려면 **응용 프로그램 다시 빌드**를 클릭합니다.
    -   빌드한 아티팩트의 응용 프로그램을 정리하려면 **응용 프로그램 정리**를 클릭합니다.

3.  이 메뉴에서 응용 프로그램을 배포, 배포 취소 및 게시할 수도 있습니다.
    -   로컬 클러스터에 배포하려면 **응용 프로그램 배포**를 클릭합니다.
    -   **응용 프로그램 게시** 대화 상자에서 게시 프로필을 선택합니다.
        -  **Local.json**
        -  **Cloud.json**

     이러한 JSON(JavaScript Object Notation) 파일은 로컬 또는 클라우드 (Azure) 클러스터에 연결하는 데 필요한 정보(예: 연결 끝점 및 보안 정보)를 저장합니다.

  ![Service Fabric 게시 메뉴][publish/Publish]

Service Fabric 응용 프로그램을 배포할 수 있는 다른 방법은 Eclipse 실행 구성을 사용하는 것입니다.

  1.    **실행** > **실행 구성**으로 이동합니다.
  2.    **등급 프로젝트** 아래에서 **ServiceFabricDeployer** 실행 구성을 선택합니다.
  3.    오른쪽 창의 **인수** 탭 아래에서 **publishProfile**에 **로컬** 또는 **클라우드**를 지정합니다.  기본값은 **로컬**입니다. 원격 또는 클라우드 클러스터에 배포하려면 **클라우드**를 선택합니다.
  4.    게시 프로필에서 적절한 정보가 채워졌는지 확인하려면 필요에 따라 **Local.json** 또는 **Cloud.json**을 편집합니다. 끝점 세부 정보 및 보안 자격 증명을 추가하거나 업데이트할 수 있습니다.
  5.    **작업 디렉터리**가 배포하려는 응용 프로그램을 가리키도록 합니다. 응용 프로그램을 변경하려면 **작업 영역** 단추를 클릭한 다음 원하는 응용 프로그램을 선택합니다.
  6.    **적용**을 클릭한 다음 **실행**을 클릭합니다.

몇 분 내로 응용 프로그램을 빌드하고 배포합니다. Service Fabric Explorer에서 배포 상태를 모니터링할 수 있습니다.  

## <a name="add-a-service-fabric-service-to-your-service-fabric-application"></a>Service Fabric 응용 프로그램에 Service Fabric 서비스 추가

Service Fabric 서비스를 기존 Service Fabric 응용 프로그램에 추가하려면 다음 단계에 따릅니다.

1.  서비스를 추가하려는 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **Service Fabric**을 클릭합니다.

    ![Service Fabric 서비스 추가 페이지 1][add-service/p1]

2.  **Service Fabric 서비스 추가**를 클릭하고 서비스 프로젝트에 추가하는 일련의 단계를 완료합니다.
3.  프로젝트에 추가할 서비스 템플릿을 선택한 다음 **다음**을 클릭합니다.

    ![Service Fabric 서비스 추가 페이지 2][add-service/p2]

4.  서비스 이름(및 필요에 따라 기타 세부 정보)를 입력한 다음 **서비스 추가** 단추를 클릭합니다.  

    ![Service Fabric 서비스 추가 페이지 3][add-service/p3]

5.  서비스를 추가하면 전체 프로젝트 구조가 다음 프로젝트와 유사하게 표시됩니다.

    ![Service Fabric 서비스 추가 페이지 4][add-service/p4]

## <a name="edit-manifest-versions-of-your-service-fabric-java-application"></a>Service Fabric Java 응용 프로그램의 매니페스트 버전 편집

매니페스트 버전을 편집하려면 프로젝트를 클릭하고 **Service Fabric**으로 이동하여 드롭다운 메뉴에서 **매니페스트 버전 편집...** 을 선택합니다. 마법사에서 응용 프로그램 매니페스트, 서비스 매니페스트의 매니페스트 버전 및 **코드**, **구성** 및 **데이터** 패키지의 버전을 업데이트할 수 있습니다.

**응용 프로그램 및 서비스 버전 자동 업데이트** 옵션을 확인한 다음 버전을 업데이트하면 매니페스트 버전이 자동으로 업데이트됩니다. 예를 들어 확인란을 먼저 선택한 다음 **코드** 버전을 0.0.0에서 0.0.1로 업데이트하고 **마침**을 클릭하면 서비스 매니페스트 버전 및 응용 프로그램 매니페스트 버전이 0.0.1로 자동 업데이트됩니다.

## <a name="upgrade-your-service-fabric-java-application"></a>Service Fabric Java 응용 프로그램 업그레이드

업그레이드 시나리오의 경우 Eclipse에서 Service Fabric 플러그 인을 사용하여 **App1** 프로젝트를 만들었다고 가정합니다. **fabric:/App1Application**라는 응용 프로그램을 만드는 플러그 인을 사용하여 배포합니다. 응용 프로그램 유형은 **App1AppicationType**이고 응용 프로그램 버전은 1.0입니다. 이제 가용성을 해치지 않고 응용 프로그램을 업그레이드하려고 합니다.

먼저 응용 프로그램을 변경하고 수정된 서비스를 다시 빌드합니다. 수정된 서비스의 매니페스트 파일(ServiceManifest.xml)을 업데이트된 버전의 서비스(및 관련된 코드, 구성 또는 데이터)로 업데이트합니다. 또한 응용 프로그램의 매니페스트(ApplicationManifest.xml)를 응용 프로그램에 대한 업데이트된 버전 번호와 수정된 서비스로 수정합니다.  

Eclipse를 사용하여 응용 프로그램을 업그레이드하려면 중복 실행 구성 프로필을 만들 수 있습니다. 그런 다음 필요에 따라 응용 프로그램을 업그레이드하는 데 사용합니다.

1.  **실행** > **실행 구성**으로 이동합니다. 왼쪽 창에서 **등급 프로젝트**의 왼쪽에 있는 작은 화살표를 클릭합니다.
2.  **ServiceFabricDeployer**를 마우스 오른쪽 단추로 클릭하고 **중복**을 선택합니다. 예를 들어 **ServiceFabricUpgrader**와 같이 이 구성의 새 이름을 입력합니다.
3.  오른쪽 패널의 **인수** 탭에서 **-Pconfig='deploy'** 를 **-Pconfig='upgrade'** 로 변경한 다음 **적용**을 클릭합니다.

이 프로세스는 응용 프로그램을 업그레이드하기 위해 언제든지 사용할 수 있는 실행 구성 프로필을 만들고 저장합니다. 그러면 응용 프로그램 매니페스트 파일에서 최신 업데이트된 응용 프로그램 유형 버전을 가져옵니다.

응용 프로그램 업그레이드에는 몇 분 정도가 소요됩니다. Service Fabric Explorer에서 응용 프로그램 업그레이드를 모니터링할 수 있습니다.

## <a name="migrating-old-service-fabric-java-applications-to-be-used-with-maven"></a>이전의 Service Fabric Java 응용 프로그램을 마이그레이션하여 Maven에서 사용
최근에 Service Fabric Java 라이브러리를 Service Fabric Java SDK에서 Maven 리포지토리로 이동했습니다. Eclipse를 사용하여 생성한 새 응용 프로그램은 최신 업데이트된 프로젝트를 생성하는 반면(Maven에서 작업할 수 있음) Maven에서 Service Fabric Java 종속성을 사용하기 위해 이전에 Service Fabric Java SDK를 사용했던 기존 Service Fabric 상태 비저장 또는 작업자 Java 응용 프로그램을 업데이트할 수 있습니다. [여기](service-fabric-migrate-old-javaapp-to-use-maven.md)에서 언급한 단계에 따라 Maven에서 이전의 응용 프로그램이 작동되는지 확인합니다.

<!-- Images -->

[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-eclipse/service-fabric-eclipse-plugin.png

[create-application/p1]:./media/service-fabric-get-started-eclipse/create-application/p1.png
[create-application/p2]:./media/service-fabric-get-started-eclipse/create-application/p2.png
[create-application/p3]:./media/service-fabric-get-started-eclipse/create-application/p3.png
[create-application/p4]:./media/service-fabric-get-started-eclipse/create-application/p4.png
[create-application/p5]:./media/service-fabric-get-started-eclipse/create-application/p5.png
[create-application/p6]:./media/service-fabric-get-started-eclipse/create-application/p6.png

[publish/Publish]: ./media/service-fabric-get-started-eclipse/publish/Publish.png
[publish/RightClick]: ./media/service-fabric-get-started-eclipse/publish/RightClick.png

[add-service/p1]: ./media/service-fabric-get-started-eclipse/add-service/p1.png
[add-service/p2]: ./media/service-fabric-get-started-eclipse/add-service/p2.png
[add-service/p3]: ./media/service-fabric-get-started-eclipse/add-service/p3.png
[add-service/p4]: ./media/service-fabric-get-started-eclipse/add-service/p4.png

<!-- Links -->
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
