---
title: Linux에서 Azure Service Fabric 컨테이너 응용 프로그램 만들기 | Microsoft Docs
description: Azure Service Fabric에서 첫 번째 Linux 컨테이너 응용 프로그램을 만듭니다. 응용 프로그램을 사용하여 Docker 이미지를 빌드하고, 이미지를 컨테이너 레지스트리로 푸시하고, Service Fabric 컨테이너 응용 프로그램을 빌드하고 배포합니다.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/09/2018
ms.author: ryanwi
ms.openlocfilehash: ba4e5996a87596c88822d96faf3e80e8243ad78b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="create-your-first-service-fabric-container-application-on-linux"></a>Linux에서 첫 번째 Service Fabric 컨테이너 응용 프로그램 만들기
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

Service Fabric 클러스터의 Linux 컨테이너에서 기존 응용 프로그램을 실행하더라도 응용 프로그램을 변경할 필요가 없습니다. 이 문서에서는 Python [Flask](http://flask.pocoo.org/) 웹 응용 프로그램을 포함하는 Docker 이미지를 만들어 Service Fabric 클러스터에 배포하는 과정을 안내합니다. 또한 [Azure Container Registry](/azure/container-registry/)를 통해 컨테이너화된 응용 프로그램을 공유할 수도 있습니다. 이 문서에서는 Docker에 대한 기본적으로 이해하고 있다고 가정합니다. [Docker 개요](https://docs.docker.com/engine/understanding-docker/)를 참고하여 Docker에 대해 알아볼 수 있습니다.

## <a name="prerequisites"></a>필수 조건
* 다음을 실행하는 개발 컴퓨터
  * [Service Fabric SDK 및 도구](service-fabric-get-started-linux.md)
  * [Linux용 Docker CE](https://docs.docker.com/engine/installation/#prior-releases) 
  * [Service Fabric CLI](service-fabric-cli.md)

* Azure Container Registry의 레지스트리 - Azure 구독 내에서 [컨테이너 레지스트리를 만듭니다](../container-registry/container-registry-get-started-portal.md). 

## <a name="define-the-docker-container"></a>Docker 컨테이너 정의
Docker 허브에 있는 [Python 이미지](https://hub.docker.com/_/python/)를 기반으로 하는 이미지를 빌드합니다. 

Dockerfile에서 Docker 컨테이너를 지정합니다. Dockerfile은 컨테이너 내부 환경 설정, 실행하려는 응용 프로그램 로드, 포트 매핑에 대한 지침으로 구성됩니다. Dockerfile는 `docker build` 명령에 대한 입력이며 이미지를 만듭니다. 

빈 디렉터리를 만들고 *Dockerfile* 파일(파일 확장명 없음)을 만듭니다. *Dockerfile*에 다음을 추가하고 변경 내용을 저장합니다.

```
# Use an official Python runtime as a base image
FROM python:2.7-slim

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

자세한 내용은 [Dockerfile 참조](https://docs.docker.com/engine/reference/builder/)를 참고하세요.

## <a name="create-a-basic-web-application"></a>기본 웹 응용 프로그램 만들기
"Hello World!"를 반환하는 포트 80에서 수신 대기하는 Flask 웹 응용 프로그램을 만듭니다. 동일한 디렉터리에서 *requirements.txt* 파일을 만듭니다. 다음을 추가하고 변경 내용을 저장합니다.
```
Flask
```

또한 *app.py* 파일을 만들고 다음 코드 조각을 추가합니다.

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello():
    
    return 'Hello World!'

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```

## <a name="build-the-image"></a>이미지 빌드
`docker build` 명령을 실행하여 웹 응용 프로그램을 실행하는 이미지를 만듭니다. PowerShell 창을 열고 *c:\temp\helloworldapp*으로 이동합니다. 다음 명령 실행:

```bash
docker build -t helloworldapp .
```

이 명령은 Dockerfile에 있는 지침을 사용하여 새 이미지를 빌드하며 이미지의 이름을 `helloworldapp`으로 지정(-t 태그 지정)합니다. 컨테이너 이미지를 작성하기 위해 먼저 기본 이미지가 응용 프로그램이 추가된 Docker 허브에서 다운로드됩니다. 

빌드 명령이 완료되면 `docker images` 명령을 실행하여 새 이미지에 대한 정보를 확인합니다.

```bash
$ docker images
    
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              86838648aab6        2 minutes ago       194 MB
```

## <a name="run-the-application-locally"></a>로컬에서 응용 프로그램 실행
컨테이너화된 응용 프로그램이 컨테이너 레지스트리에 푸시하기 전에 로컬로 실행되는지 확인합니다. 

응용 프로그램을 실행하고 컨테이너의 노출되는 포트 80에 컴퓨터의 포트 4000을 매핑합니다.

```bash
docker run -d -p 4000:80 --name my-web-site helloworldapp
```

*name*은 (컨테이너 ID가 아닌) 실행 중인 컨테이너에 이름을 지정합니다.

실행 중인 컨테이너에 연결합니다. 포트 4000에서 반환되는 IP 주소(예: "http://localhost:4000")를 가리키는 웹 브라우저를 엽니다. 제목인 "Hello World!"가 브라우저에 표시됩니다.

![Hello World!][hello-world]

컨테이너를 중지하려면 다음을 실행합니다.

```bash
docker stop my-web-site
```

개발 컴퓨터에서 컨테이너를 삭제합니다.

```bash
docker rm my-web-site
```

## <a name="push-the-image-to-the-container-registry"></a>컨테이너 레지스트리에 이미지를 푸시합니다.
Docker에서 응용 프로그램이 실행되는지 확인한 후에 Azure Container Registry에서 이미지를 레지스트리에 푸시합니다.

[레지스트리 자격 증명](../container-registry/container-registry-authentication.md)을 사용하여 컨테이너 레지스트리에 로그인하려면 `docker login`을 실행합니다.

다음 예제는 Azure Active Directory [서비스 주체](../active-directory/active-directory-application-objects.md)의 ID와 암호를 전달합니다. 예를 들어 자동화 시나리오를 위해 레지스트리에 서비스 주체를 할당할 수 있습니다. 또는 레지스트리 사용자 이름과 암호를 사용하여 로그인할 수 있습니다.

```bash
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

다음 명령은 레지스트리에 대한 정규화된 경로를 사용하여 이미지의 태그 또는 별칭을 만듭니다. 이 예제는 레지스트리의 루트에서 혼잡을 방지하기 위해 `samples` 네임스페이스에 이미지를 배치합니다.

```bash
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

이미지를 컨테이너 레지스트리에 푸시합니다.

```bash
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="package-the-docker-image-with-yeoman"></a>Yeoman을 사용하여 Docker 이미지 패키징
Linux용 Service Fabric SDK는 쉽게 응용 프로그램을 만들고 컨테이너 이미지를 추가할 수 있는 [Yeoman](http://yeoman.io/) 생성기를 포함합니다. Yeoman을 사용하여 *SimpleContainerApp*이라는 단일 Docker 컨테이너가 있는 응용 프로그램을 만들어 보겠습니다.

Service Fabric 컨테이너 응용 프로그램을 만들려면 터미널 창을 열고 `yo azuresfcontainer`을 실행합니다. 

응용 프로그램 이름(예: `mycontainer`)을 지정하고 응용 프로그램 서비스 이름(예: `myservice`)을 지정합니다.

이미지 이름에 컨테이너 레지스트리에서 컨테이너 이미지의 URL을 제공합니다(예: "myregistry.azurecr.io/samples/helloworldapp"). 

이 이미지가 워크로드 진입점을 정의하지 않으므로 입력 명령을 명시적으로 지정하지 않아도 됩니다(컨테이너 내에서 명령을 실행하면 시작된 후에 컨테이너가 실행되도록 유지함). 

"1"이라는 인스턴스 수를 지정합니다.

![컨테이너용 Service Fabric Yeoman 생성기][sf-yeoman]

## <a name="configure-port-mapping-and-container-repository-authentication"></a>포트 매핑 및 컨테이너 리포지토리 인증 구성
컨테이너화된 서비스에는 통신을 위한 끝점이 필요합니다. 이제 'Resources' 태그 아래의 ServiceManifest.xml 파일에서 프로토콜, 포트 및 형식을 `Endpoint`에 추가할 수 있습니다. 이 문서의 경우 컨테이너화된 서비스는 포트 4000에서 수신 대기합니다. 

```xml

<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myServiceTypeEndpoint" UriScheme="http" Port="4000" Protocol="http"/>
    </Endpoints>
  </Resources>
 ```
 
`UriScheme`을 입력하면 이 컨테이너 끝점이 검색될 수 있도록 Service Fabric Naming 서비스에 자동으로 등록됩니다. 이 문서의 끝에서 전체 ServiceManifest.xml 예제 파일을 제공합니다. 

ApplicationManifest.xml 파일의 `ContainerHostPolicies`에서 `PortBinding` 정책을 지정하여 컨테이너 포트 및 호스트 간 포트 매핑을 구성합니다. 이 문서에서 `ContainerPort`은 80(컨테이너가 Dockerfile에서 지정된 대로 포트 80을 노출함)이고 `EndpointRef`는 "myServiceTypeEndpoint"(서비스 매니페스트에서 정의된 끝점임)입니다. 포트 4000에서 서비스에 들어오는 요청은 컨테이너에 있는 포트 80에 매핑됩니다. 컨테이너가 개인 리포지토리에 인증해야 하는 경우 `RepositoryCredentials`를 추가합니다. 이 문서에서는 myregistry.azurecr.io 컨테이너 레지스트리의 계정 이름 및 암호를 추가합니다. 정책이 올바른 서비스 패키지에 해당하는 'ServiceManifestImport' 태그 아래에 추가되었는지 확인합니다.

```xml
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0" />
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="myServiceTypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
   </ServiceManifestImport>
``` 
## <a name="configure-docker-healthcheck"></a>Docker HEALTHCHECK 구성 
v6.1을 시작하면 Service Fabric에서 자동으로 [Docker HEALTHCHECK](https://docs.docker.com/engine/reference/builder/#healthcheck) 이벤트를 시스템 상태 보고서에 통합합니다. 즉 컨테이너에 **HEALTHCHECK**를 사용하도록 설정된 경우, Docker에서 보고한 대로 컨테이너의 상태가 변경될 때마다 Service Fabric에서 상태를 보고합니다. *health_status*가 *healthy*이면 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)에서 **OK** 상태 보고서가 표시되고, *health_status*가 *unhealthy*이면 **경고**가 표시됩니다. 컨테이너 상태를 모니터링하기 위해 수행되는 실제 검사를 가리키는 **HEALTHCHECK** 명령은 컨테이너 이미지를 생성하는 동안 사용되는 Dockerfile에 있어야 합니다. 

![HealthCheckHealthy][1]

![HealthCheckUnealthyApp][2]

![HealthCheckUnhealthyDsp][3]

ApplicationManifest에서 **ContainerHostPolicies**의 일부로 **HealthConfig** 옵션을 지정하여 각 컨테이너에 대한 **HEALTHCHECK** 동작을 구성할 수 있습니다.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="ContainerServicePkg" ServiceManifestVersion="2.0.0" />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <HealthConfig IncludeDockerHealthStatusInSystemHealthReport="true" RestartContainerOnUnhealthyDockerHealthStatus="false" />
      </ContainerHostPolicies>
    </Policies>
</ServiceManifestImport>
```
기본적으로 *IncludeDockerHealthStatusInSystemHealthReport*는 **true**로 설정되고, *RestartContainerOnUnhealthyDockerHealthStatus*는 **false**로 설정됩니다. *RestartContainerOnUnhealthyDockerHealthStatus*가 **true**로 설정된 경우, 반복적으로 비정상으로 보고하는 컨테이너가 다시 시작됩니다(다른 노드에서도 가능).

전체 Service Fabric 클러스터에 대해 **HEALTHCHECK** 통합을 사용하지 않도록 설정하려면 [EnableDockerHealthCheckIntegration](service-fabric-cluster-fabric-settings.md)을 **false**로 설정해야 합니다.

## <a name="build-and-package-the-service-fabric-application"></a>Service Fabric 응용 프로그램 빌드 및 패키징
Service Fabric Yeoman 템플릿은 [Gradle](https://gradle.org/)에 대한 빌드 스크립트를 포함하며 이것을 사용하여 터미널에서 응용 프로그램을 빌드할 수 있습니다. 응용 프로그램을 빌드하고 패키징하려면 다음을 실행합니다.

```bash
cd mycontainer
gradle
```

## <a name="deploy-the-application"></a>응용 프로그램 배포
응용 프로그램이 빌드되면 Service Fabric CLI를 사용하여 로컬 클러스터에 배포할 수 있습니다.

로컬 Service Fabric 클러스터에 연결합니다.

```bash
sfctl cluster select --endpoint http://localhost:19080
```

템플릿에 제공된 설치 스크립트를 사용하여 클러스터의 이미지 저장소에 응용 프로그램 패키지를 복사하고 응용 프로그램 유형을 등록하며 응용 프로그램의 인스턴스를 만듭니다.

```bash
./install.sh
```

브라우저를 열고 http://localhost:19080/Explorer에서 Service Fabric Explorer로 이동합니다(Mac OS X에서 Vagrant를 사용하는 경우 localhost를 VM의 개인 IP로 바꿈). 응용 프로그램 노드를 확장하면 응용 프로그램 유형에 대한 항목 및 해당 유형의 첫 번째 인스턴스에 대한 다른 항목이 만들어집니다.

실행 중인 컨테이너에 연결합니다. 포트 4000에서 반환되는 IP 주소(예: "http://localhost:4000")를 가리키는 웹 브라우저를 엽니다. 제목인 "Hello World!"가 브라우저에 표시됩니다.

![Hello World!][hello-world]


## <a name="clean-up"></a>정리
템플릿에 제공된 제거 스크립트를 사용하여 로컬 배포 클러스터에서 응용 프로그램 인스턴스를 삭제하고 응용 프로그램 유형을 등록 해제합니다.

```bash
./uninstall.sh
```

이미지를 컨테이너 레지스트리에 푸시한 후에 개발 컴퓨터에서 로컬 이미지를 삭제할 수 있습니다.

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Service Fabric 응용 프로그램 및 서비스 매니페스트의 전체 예제
이 문서에서 사용한 전체 서비스 및 응용 프로그램 매니페스트는 다음과 같습니다.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="myservicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="myserviceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying containers 
      to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
        <!-- Pass comma delimited commands to your container: dotnet, myproc.dll, 5" -->
        <!--Commands> dotnet, myproc.dll, 5 </Commands-->
        <Commands></Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->
    
    <EnvironmentVariables>
      <!--
      <EnvironmentVariable Name="VariableName" Value="VariableValue"/>
      -->
    </EnvironmentVariables>
    
  </CodePackage>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myServiceTypeEndpoint" UriScheme="http" Port="4000" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="mycontainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="myservicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="myServiceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="myservice">
      <!-- On a local development cluster, set InstanceCount to 1. On a multi-node production 
      cluster, set InstanceCount to -1 for the container service to run on every node in 
      the cluster.
      -->
      <StatelessService ServiceTypeName="myserviceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="adding-more-services-to-an-existing-application"></a>기존 응용 프로그램에 더 많은 서비스 추가

yeoman을 사용하여 다른 컨테이너 서비스를 이미 만든 응용 프로그램에 추가하려면 다음 단계를 수행합니다.

1. 기존 응용 프로그램의 루트로 디렉터리를 변경합니다. 예를 들어 `MyApplication`이 Yeoman에서 만든 응용 프로그램인 경우 `cd ~/YeomanSamples/MyApplication`입니다.
2. `yo azuresfcontainer:AddService` 실행

<a id="manually"></a>


## <a name="configure-time-interval-before-container-is-force-terminated"></a>컨테이너를 강제로 종료하기 전 시간 간격 구성

서비스 삭제(또는 다른 노드로 이동)가 시작된 후 컨테이너가 제거되기 전에 대기할 런타임 시간 간격을 구성할 수 있습니다. `docker stop <time in seconds>` 명령을 컨테이너로 보내는 시간 간격 구성.  자세한 내용은 [docker stop](https://docs.docker.com/engine/reference/commandline/stop/)을 참조하세요. 대기할 시간 간격은 `Hosting` 섹션 아래에 지정됩니다. 다음 클러스터 매니페스트 코드 조각은 대기 간격을 설정하는 방법을 보여 줍니다.


```json
{
        "name": "Hosting",
        "parameters": [
          {
                "name": "ContainerDeactivationTimeout",
                "value" : "10"
          },
          ...
        ]
}
```

기본 시간 간격은 10초로 설정됩니다. 이 구성은 동적이므로 클러스터에 대한 구성 전용 업그레이드만 시간 제한을 업데이트합니다. 

## <a name="configure-the-runtime-to-remove-unused-container-images"></a>사용하지 않는 컨테이너 이미지를 제거할 런타임 구성

노드에서 사용하지 않는 컨테이너 이미지를 제거하기 위해 Service Fabric 클러스터를 구성할 수 있습니다. 이 구성을 통해 노드에 너무 많은 컨테이너 이미지가 있는 경우 디스크 공간을 다시 캡처할 수 있습니다. 이 기능을 사용하려면 다음 코드 조각에 표시된 것처럼 클러스터 매니페스트에서 `Hosting` 섹션을 업데이트합니다. 


```json
{
        "name": "Hosting",
        "parameters": [
          {
                "name": "PruneContainerImages",
                "value": "True"
          },
          {
                "name": "ContainerImagesToSkip",
                "value": "microsoft/windowsservercore|microsoft/nanoserver|microsoft/dotnet-frameworku|..."
          }
          ...
          }
        ]
} 
```

삭제하지 말아야 하는 이미지의 경우 `ContainerImagesToSkip` 매개 변수 아래에 지정할 수 있습니다. 

## <a name="configure-container-image-download-time"></a>컨테이너 이미지 다운로드 시간 구성

Service Fabric 런타임은 대부분의 컨테이너 이미지에 대해 작동하는 컨테이너 이미지를 다운로드하고 추출하는 데 20분을 할당합니다. 큰 이미지의 경우 또는 네트워크 연결이 느린 경우, 이미지 다운로드 및 추출을 중단하기 전에 기다리는 시간을 늘려야 할 수 있습니다. 이 시간 제한은 다음 코드 조각과 같이 클러스터 매니페스트의 **Hosting** 섹션에 있는 **ContainerImageDownloadTimeout** 특성을 사용하여 설정합니다.

```json
{
"name": "Hosting",
        "parameters": [
          {
              "name": " ContainerImageDownloadTimeout ",
              "value": "1200"
          }
]
}
```


## <a name="set-container-retention-policy"></a>컨테이너 보존 정책 설정

컨테이너 시작 오류 진단을 지원하기 위해 Service Fabric(버전 6.1 이상)은 종료되었거나 시작하지 못한 컨테이너를 보존하도록 지원합니다. 이 정책은 다음 코드 조각과 같이 **ApplicationManifest.xml** 파일에서 설정할 수 있습니다.

```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
```

**ContainersRetentionCount** 설정은 실패할 때 유지할 컨테이너의 수를 지정합니다. 음수 값을 지정하면 실패한 모든 컨테이너가 유지됩니다. **ContainersRetentionCount** 특성을 지정하지 않으면 컨테이너는 유지되지 않습니다. 또한 **ContainersRetentionCount** 특성은 응용 프로그램 매개 변수도 지원하므로 사용자는 테스트 및 프로덕션 클러스터에 대해 다른 값을 지정할 수 있습니다. 이 기능을 사용하여 컨테이너 서비스가 다른 노드로 이동하지 않도록 방지하려면 배치 제약 조건을 사용하여 특정 노드에 대한 컨테이너 서비스를 대상으로 지정합니다. 이 기능을 사용하여 유지된 컨테이너는 수동으로 제거해야 합니다.

## <a name="start-the-docker-daemon-with-custom-arguments"></a>사용자 지정 인수로 Docker 디먼 시작

6.2 버전 이상의 Service Fabric 런타임에서는 사용자 지정 인수로 Docker 디먼을 시작할 수 있습니다. 사용자 지정 인수가 지정되면 Service Fabric은 Docker 엔진에 `--pidfile` 인수를 제외한 다른 인수를 전달하지 않습니다. 따라서, `--pidfile`을 인수로 전달하지 말아야 합니다. 또한, 인수는 Service Fabric이 디먼과 통신할 수 있도록 Docker 디먼이 Windows의 기본 이름 파이프(또는 Linux의 UNIX 도메인 소켓)를 수신 대기하도록 해야 합니다. 사용자 지정 인수는 **ContainerServiceArguments**의 **Hosting** 섹션 아래에 있는 클러스터 매니페스트에 지정됩니다. 다음 코드 조각에 예제가 표시됩니다. 
 

```json
{ 
   "name": "Hosting", 
        "parameters": [ 
          { 
            "name": "ContainerServiceArguments", 
            "value": "-H localhost:1234 -H unix:///var/run/docker.sock" 
          } 
        ] 
} 

```

## <a name="next-steps"></a>다음 단계
* [Service Fabric의 컨테이너](service-fabric-containers-overview.md)를 실행하는 방법에 대해 자세히 알아봅니다.
* [컨테이너에서 .NET 응용 프로그램 배포](service-fabric-host-app-in-a-container.md) 자습서를 참조합니다.
* Service Fabric [응용 프로그램 수명 주기](service-fabric-application-lifecycle.md)에 대해 자세히 알아봅니다.
* GitHub에서 [Service Fabric 컨테이너 코드 샘플](https://github.com/Azure-Samples/service-fabric-containers)을 확인합니다.

[hello-world]: ./media/service-fabric-get-started-containers-linux/HelloWorld.png
[sf-yeoman]: ./media/service-fabric-get-started-containers-linux/YoSF.png

[1]: ./media/service-fabric-get-started-containers/HealthCheckHealthy.png
[2]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_App.png
[3]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_Dsp.png
