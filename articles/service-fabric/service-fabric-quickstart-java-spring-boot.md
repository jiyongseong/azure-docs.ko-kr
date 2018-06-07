---
title: Azure Service Fabric에 Spring Boot 응용 프로그램 배포 | Microsoft Docs
description: 이 자습서에서는 Spring Boot 응용 프로그램 예제를 사용하여 Azure Service Fabric에 Spring Boot 응용 프로그램을 배포합니다.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/23/2017
ms.author: suhuruli
ms.custom: mvc, devcenter
ms.openlocfilehash: 6c84b60018ec03b7f9bc572db9181b8a47a0c595
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365409"
---
# <a name="quickstart-deploy-a-java-spring-boot-application-to-azure"></a>빠른 시작 Azure에 Java Spring Boot 응용 프로그램 배포
Azure Service Fabric은 마이크로 서비스 및 컨테이너를 배포 및 관리하기 위한 분산 시스템 플랫폼입니다. 

이 빠른 시작에서는 Mac 또는 Linux 개발자 컴퓨터를 사용하여 Service Fabric에 Spring Boot 응용 프로그램을 배포하는 방법을 보여줍니다. 이 빠른 시작에서는 Spring 웹 사이트의 [시작](https://spring.io/guides/gs/spring-boot/) 샘플을 사용합니다. 이 빠른 시작에서는 친숙한 명령줄 도구를 사용하여 Spring Boot 샘플을 Service Fabric 응용 프로그램으로 배포하는 방법을 안내합니다. 작업이 완료되면 Service Fabric에서 Spring Boot 시작 샘플을 사용합니다. 

![응용 프로그램 스크린샷](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)

이 빠른 시작에서 다음을 수행하는 방법을 알아봅니다.

* Service Fabric에 Spring Boot 응용 프로그램 배포
* 로컬 클러스터에 응용 프로그램 배포 
* Azure에서 응용 프로그램을 클러스터에 배포
* 응용 프로그램을 여러 노드에 걸쳐 스케일 아웃
* 가용성에 아무런 영향을 주지 않고 서비스에 대한 장애 조치 수행

## <a name="prerequisites"></a>필수 조건
이 빠른 시작을 완료하려면 다음이 필요합니다.
1. Service Fabric SDK 및 Service Fabric 명령줄 인터페이스(CLI) 설치

    a. [Mac](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cli#cli-mac)
    
    나. [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)

2. [Git 설치](https://git-scm.com/)
3. Yeoman 설치

    a. [Mac](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started-mac#create-your-application-on-your-mac-by-using-yeoman)

    나. [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
4. Java 환경 설정

    a. [Mac](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started-mac#create-your-application-on-your-mac-by-using-yeoman)
    
    나.  [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development)

## <a name="download-the-sample"></a>샘플 다운로드
터미널 창에서 다음 명령을 실행하여 로컬 컴퓨터에 Spring Boot 시작 샘플 앱을 복제합니다.
```bash
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="build-the-spring-boot-application"></a>Spring Boot 응용 프로그램 빌드 
1. `gs-spring-boot/complete` 디렉터리 내에서 아래 명령을 실행하여 응용 프로그램을 빌드합니다. 

    ```bash
    ./gradlew build
    ``` 

## <a name="package-the-spring-boot-application"></a>Spring Boot 응용 프로그램 패키지 
1. 클론의 `gs-spring-boot` 디렉터리 내에서 `yo azuresfguest` 명령을 실행합니다. 

2. 각 프롬프트에 다음 세부 정보를 입력합니다. 

    ![Yeoman 항목](./media/service-fabric-quickstart-java-spring-boot/yeomanspringboot.png)

3. `SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code` 폴더에 `entryPoint.sh`라는 파일을 만듭니다. `entryPoint.sh` 파일에 다음을 추가합니다. 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar gs-spring-boot-0.1.0.jar
    ```

4. `gs-spring-boot/SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/ServiceManifest.xml` 파일의 **엔드포인트** 리소스

    ```xml 
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
    ```

    이제 **ServiceManifest.xml**은 다음과 같습니다. 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceManifest Name="SpringGettingStartedPkg" Version="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" >

       <ServiceTypes>
          <StatelessServiceType ServiceTypeName="SpringGettingStartedType" UseImplicitHost="true">
       </StatelessServiceType>
       </ServiceTypes>

       <CodePackage Name="code" Version="1.0.0">
          <EntryPoint>
             <ExeHost>
                <Program>entryPoint.sh</Program>
                <Arguments></Arguments>
                <WorkingFolder>CodePackage</WorkingFolder>
             </ExeHost>
          </EntryPoint>
       </CodePackage>
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
     </ServiceManifest>
    ```

이 단계에서는 Service Fabric에 배포할 수 있는 Spring Boot 시작 샘플에 Service Fabric 응용 프로그램을 만들었습니다.

## <a name="run-the-application-locally"></a>로컬에서 응용 프로그램 실행
1. 다음 명령을 실행하여 Ubuntu 컴퓨터에서 로컬 클러스터를 시작합니다.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

    Mac을 사용하는 경우 Docker 이미지에서 로컬 클러스터를 시작합니다([필수 조건](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started-mac#create-a-local-container-and-set-up-service-fabric)을 따라 Mac용 로컬 클러스터를 설정한 것으로 가정함). 

    ```bash
    docker run --name sftestcluster -d -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 -p 8080:8080 mysfcluster
    ```

    로컬 클러스터를 시작하는 데 몇 시간이 걸립니다. 클러스터가 완전히 작동 중인지 확인하려면 **http://localhost:19080**에서 Service Fabric Explorer에 액세스합니다. 5개의 정상 노드는 로컬 클러스터가 작동되어 실행 중임을 나타냅니다. 
    
    ![로컬 클러스터 정상](./media/service-fabric-quickstart-java-spring-boot/sfxlocalhost.png)

2. `gs-spring-boot/SpringServiceFabric` 폴더로 이동합니다.
3. 다음 명령을 실행하여 로컬 클러스터에 연결합니다. 

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
4. `install.sh` 스크립트를 실행합니다. 

    ```bash
    ./install.sh
    ```

5. 즐겨찾는 웹 브라우저를 열고 **http://localhost:8080**에 액세스하여 응용 프로그램에 액세스합니다. 

    ![응용 프로그램 프런트 엔드](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)
    
이제 Service Fabric 클러스터에 배포된 Spring Boot 응용 프로그램에 액세스할 수 있습니다.  

## <a name="deploy-the-application-to-azure"></a>Azure에 응용 프로그램 배포

### <a name="set-up-your-azure-service-fabric-cluster"></a>Azure Service Fabric Cluster 설정
응용 프로그램을 Azure의 클러스터에 배포하려면 고유한 클러스터를 만듭니다.

파티 클러스터는 Azure에서 호스팅되는 시간이 제한된 체험용 Service Fabric 클러스터이며 Service Fabric 팀에서 운영합니다. 파티 클러스터를 사용하여 응용 프로그램을 배포하고 플랫폼에 대해 알아볼 수 있습니다. 클러스터는 노드-노드 및 클라이언트-노드 보안에 단일 자체 서명 인증서를 사용합니다.

[Linux 클러스터](http://aka.ms/tryservicefabric)에 로그인하고 조인합니다. **PFX** 링크를 클릭하여 PFX 인증서를 컴퓨터에 다운로드합니다. **추가 정보** 링크를 클릭하여 인증서 암호 및 다양한 환경에서 인증서를 사용하도록 구성하는 방법에 대한 지침을 찾습니다. **시작** 페이지와 **추가 정보** 페이지를 모두 열어두고 다음 단계에서 일부 지침을 사용합니다. 

> [!Note]
> 시간당 사용 가능한 파티 클러스터의 수는 제한되어 있습니다. 파티 클러스터에 등록하려고 할 때 오류가 발생하면, 일정 기간 동안 기다린 후 다시 시도하거나, [Azure에서 Service Fabric 클러스터 만들기](service-fabric-tutorial-create-vnet-and-linux-cluster.md)의 단계에 따라 구독에 클러스터를 만들 수 있습니다. 
>
> Spring Boot 서비스는 들어오는 트래픽에 대해 포트 8080에서 수신 대기하도록 구성됩니다. 클러스터에 대해 포트가 열려 있는지 확인합니다. Party 클러스터를 사용하는 경우 이 포트가 열려 있습니다.
>

Service Fabric은 클러스터 및 해당 응용 프로그램을 관리하는 데 사용할 수 있는 몇 가지 도구를 제공합니다.

- Service Fabric Explorer, 브라우저 기반 도구입니다.
- Service Fabric CLI(명령줄 인터페이스), Azure CLI 2.0을 기반으로 실행됩니다.
- PowerShell 명령 

이 빠른 시작에서는 Service Fabric CLI 및 Service Fabric Explorer를 사용합니다. 

CLI를 사용하려면 다운로드한 PFX 파일을 기반으로 PEM 파일을 만들어야 합니다. 파일을 변환하려면 다음 명령을 사용합니다. (파티 클러스터의 경우 **추가 정보** 페이지의 지침에서 PFX 파일에 해당하는 명령을 복사할 수 있습니다.)

```bash
openssl pkcs12 -in party-cluster-1486790479-client-cert.pfx -out party-cluster-1486790479-client-cert.pem -nodes -passin pass:1486790479
``` 

Service Fabric Explorer를 사용하려면 파티 클러스터 웹 사이트에서 다운로드한 인증서 PFX 파일을 인증서 저장소(Windows 또는 Mac) 또는 브라우저 자체(Ubuntu)로 가져와야 합니다. PFX 개인 키 암호가 필요하며 **추가 정보** 페이지에서 얻을 수 있습니다.

가장 편리한 방법을 사용하여 시스템에서 인증서를 가져옵니다. 예: 

- Windows: PFX 파일을 두 번 클릭하고 프롬프트에 따라 개인 저장소 `Certificates - Current User\Personal\Certificates`에 인증서를 설치합니다. 또는 **추가 정보** 지침의 PowerShell 명령을 사용할 수 있습니다.
- Mac: PFX 파일을 두 번 클릭하고 프롬프트에 따라 키 집합에 인증서를 설치합니다.
- Ubuntu: Mozilla Firefox는 Ubuntu 16.04의 기본 브라우저입니다. 인증서를 Firefox로 가져 오려면 브라우저의 오른쪽 위 모서리에 있는 메뉴 단추를 클릭한 다음 **옵션**을 클릭합니다. **기본 설정** 페이지에서 검색 상자를 사용하여 "인증서"를 검색합니다. **인증서 보기**를 클릭하고 **Your Certificates**(인증서) 탭을 선택하고 **가져오기**를 클릭하여 프롬프트에 따라 인증서를 가져옵니다.
 
   ![Firefox에서 인증서 설치](./media/service-fabric-quickstart-java-spring-boot/install-cert-firefox.png) 


### <a name="deploy-the-application-using-cli"></a>CLI를 사용하여 응용 프로그램 배포
응용 프로그램과 클러스터가 준비되면 명령줄에서 클러스터에 직접 배포할 수 있습니다.

1. `gs-spring-boot/SpringServiceFabric` 폴더로 이동합니다.
2. 다음 명령을 실행하여 Azure 클러스터에 연결합니다. 

    ```bash
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify
    ```
3. `install.sh` 스크립트를 실행합니다. 

    ```bash
    ./install.sh
    ```

4. 웹 브라우저를 열고 **http://\<ConnectionIPOrUrl>:8080**에 액세스하여 응용 프로그램에 액세스합니다. 

    ![응용 프로그램 프런트 엔드](./media/service-fabric-quickstart-java-spring-boot/springbootsfazure.png)
    
이제 Azure의 Service Fabric 클러스터에서 실행 중인 Spring Boot 응용 프로그램에 액세스할 수 있습니다.  
    
## <a name="scale-applications-and-services-in-a-cluster"></a>클러스터에서 응용 프로그램 및 서비스 크기 조정
서비스는 해당 서비스에 대한 로드 변동량을 수용하도록 클러스터 간에 크기를 조정할 수 있습니다. 클러스터에서 실행되는 인스턴스 수를 변경하여 서비스 크기를 조정합니다. 서비스의 크기를 조정하는 여러 가지 방법이 있으며 예를 들어, Service Fabric CLI(sfctl)의 스크립트 또는 명령을 사용할 수 있습니다. 다음 단계에서는 Service Fabric Explorer를 사용합니다.

Service Fabric Explorer는 모든 Service Fabric 클러스터에서 실행되고 클러스터 HTTP 관리 포트(19080)로 이동하여 브라우저에서 액세스할 수 있습니다(예: `http://localhost:19080`).

웹 프런트 엔드 서비스의 크기를 조정하려면 다음을 수행합니다.

1. 클러스터에서 Service Fabric Explorer를 엽니다. 예: `http://localhost:19080`
2. 트리 뷰에서 **fabric:/SpringServiceFabric/SpringGettingStarted** 노드 옆에 있는 줄임표(...)를 클릭하고 **서비스 크기 조정**을 선택합니다.

    ![Service Fabric Explorer Scale Service](./media/service-fabric-quickstart-java-spring-boot/sfxscaleservicehowto.png)

    이제 서비스의 인스턴스 수를 조정하도록 선택할 수 있습니다.

3. 숫자를 **3**으로 변경하고 **Scale Service**를 클릭합니다.

    명령줄을 사용하여 서비스의 크기를 조정하는 다른 방법은 다음과 같습니다.

    ```bash 
    # Connect to your local cluster
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted` --instance-count 3 --stateless 
    ``` 

4. 트리 뷰에서 **fabric:/SpringServiceFabric/SpringGettingStarted** 노드를 클릭하고 파티션 노드(GUID로 표현됨)를 확장합니다.

    ![Service Fabric Explorer Scale Service 완료](./media/service-fabric-quickstart-java-spring-boot/sfxscaledservice.png)

    서비스에는 3개의 인스턴스가 있고, 트리 뷰에는 인스턴스가 실행되는 노드가 표시됩니다.

이 간단한 관리 작업을 통해 프런트 엔드 서비스에서 사용 가능한 리소스를 두 배로 늘려서 사용자 부하를 처리했습니다. 서비스를 안정적으로 실행하기 위해 여러 인스턴스가 필요하지 않다는 것을 이해하는 것이 중요합니다. 서비스가 실패하면 Service Fabric은 클러스터에서 새 서비스 인스턴스가 실행되는지 확인합니다.

## <a name="fail-over-services-in-a-cluster"></a>클러스터의 서비스 장애 조치 
서비스 장애 조치를 보여 주기 위해 Service Fabric Explorer를 사용하여 노드 다시 시작을 시뮬레이션합니다. 서비스의 인스턴스 하나만 실행되고 있는지 확인합니다. 

1. 클러스터에서 Service Fabric Explorer를 엽니다. 예: `http://localhost:19080`
2. 서비스의 인스턴스를 실행하는 노드 옆에 있는 줄임표(...)를 클릭하고 노드를 다시 시작합니다. 

    ![Service Fabric Explorer 다시 시작 노드](./media/service-fabric-quickstart-java-spring-boot/sfxhowtofailover.png)
3. 서비스의 인스턴스가 다른 노드로 이동되고, 응용 프로그램에서 가동 중지 시간이 발생하지 않습니다. 

    ![Service Fabric Explorer 다시 시작 노드 성공](./media/service-fabric-quickstart-java-spring-boot/sfxfailedover.png)

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 다음을 수행하는 방법을 알아보았습니다.

* Service Fabric에 Spring Boot 응용 프로그램 배포
* 로컬 클러스터에 응용 프로그램 배포 
* Azure에서 응용 프로그램을 클러스터에 배포
* 응용 프로그램을 여러 노드에 걸쳐 스케일 아웃
* 가용성에 아무런 영향을 주지 않고 서비스에 대한 장애 조치 수행

Service Fabric에서 Java 앱 작업에 대해 자세히 알아보려면 Java 앱에 대한 자습서를 계속 진행하십시오.

> [!div class="nextstepaction"]
> [Java 앱 배포](./service-fabric-tutorial-create-java-app.md)
