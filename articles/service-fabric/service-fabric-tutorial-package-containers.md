---
title: Service Fabric 컨테이너 응용 프로그램 패키징 및 배포 | Microsoft Docs
description: 이 자습서에서는 Yeoman을 사용하여 Azure Service Fabric 응용 프로그램 정의를 생성하고 응용 프로그램을 패키지하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: ''
author: suhuruli
manager: timlt
editor: suhuruli
tags: servicefabric
keywords: Docker, 컨테이너, 마이크로 서비스, Service Fabric, Azure
ms.assetid: ''
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: fc589b79cf91dcbe24e6d99da44aeee883b58e5f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365388"
---
# <a name="tutorial-package-and-deploy-containers-as-a-service-fabric-application"></a>자습서: Service Fabric 응용 프로그램으로 컨테이너 패키징 및 배포

이 자습서는 시리즈의 2부입니다. 이 자습서에서는 Service Fabric 응용 프로그램 정의를 생성하는 데 템플릿 생성기 도구(Yeoman)를 사용합니다. 그런 다음 이 응용 프로그램을 사용하여 Service Fabric에 컨테이너를 배포할 수 있습니다. 이 자습서에서는 다음 방법에 대해 알아봅니다. 

> [!div class="checklist"]
> * Yeoman 설치  
> * Yeoman을 사용하여 응용 프로그램 패키지 만들기
> * 응용 프로그램 패키지에서 컨테이너에서 사용할 설정 구성
> * 응용 프로그램 빌드  
> * 응용 프로그램 배포 및 실행 
> * 응용 프로그램 정리

## <a name="prerequisites"></a>필수 조건

- 이 자습서의 [1부](service-fabric-tutorial-create-container-images.md)에서 만든 Azure Container Registry에 푸시된 컨테이너 이미지가 사용됩니다.
- Linux 개발 환경을 [설정합니다](service-fabric-tutorial-create-container-images.md).

## <a name="install-yeoman"></a>Yeoman 설치
Service Fabric은 Yeoman 템플릿 생성기를 사용하여 터미널에서 응용 프로그램을 만들 수 있는 스캐폴딩 도구를 제공합니다. 아래 단계에 따라 Yeoman 템플릿 생성기가 설치되어 있는지 확인합니다. 

1. 컴퓨터에서 Node.js 및 NPM을 설치합니다. Mac OSX 사용자는 Homebrew 패키지 관리자를 사용해야 합니다

    ```bash
    curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash –
    sudo apt-get install -y nodejs 
    ```
2. NPM의 컴퓨터에 Yeoman 템플릿 생성기 설치 

    ```bash
    sudo npm install -g yo
    ```
3. Service Fabric Yeoman 컨테이너 생성기 설치

    ```bash 
    sudo npm install -g generator-azuresfcontainer
    ```

## <a name="package-a-docker-image-container-with-yeoman"></a>Yeoman에서 Docker 이미지 컨테이너 패키징

1. Service Fabric 컨테이너 응용 프로그램을 만들려면 복제된 리포지토리의 'container-tutorial' 디렉터리에서 다음 명령을 실행합니다.

    ```bash
    yo azuresfcontainer
    ```
2. "TestContainer"를 입력하여 응용 프로그램의 이름을 지정하세요.
3. "azurevotefront"를 입력하여 응용 프로그램 서비스의 이름을 지정하세요.
4. 예를 들어 '\<acrName>.azurecr.io/azure-vote-front:v1'과 같이 프런트 엔드 리포지토리에 대해 ACR의 컨테이너 이미지 경로를 제공합니다. \<acrName> 필드는 이전 자습서에서 사용된 값과 동일해야 합니다.
5. 명령 섹션을 비어 두려면 Enter 키를 누릅니다.
6. 1이라는 인스턴스 수를 지정합니다.

다음은 yo 명령을 실행하는 입력 및 출력을 보여줍니다.

```bash
? Name your application TestContainer
? Name of the application service: azurevotefront
? Input the Image Name: <acrName>.azurecr.io/azure-vote-front:v1
? Commands: 
? Number of instances of guest container application: 1
   create TestContainer/TestContainer/ApplicationManifest.xml
   create TestContainer/TestContainer/azurevotefrontPkg/ServiceManifest.xml
   create TestContainer/TestContainer/azurevotefrontPkg/config/Settings.xml
   create TestContainer/TestContainer/azurevotefrontPkg/code/Dummy.txt
   create TestContainer/install.sh
   create TestContainer/uninstall.sh
```

Yeoman을 사용하여 다른 컨테이너 서비스를 이미 만든 응용 프로그램에 추가하려면 다음 단계를 수행합니다.

1. 디렉터리를 **TestContainer** 디렉터리로 한 수준 변경(예: *./TestContainer*)
2. `yo azuresfcontainer:AddService` 실행 
3. 'azurevoteback' 서비스 이름 지정
4. Redis - 'alpine:redis'에 대한 컨테이너 이미지 경로 제공
5. 명령 섹션을 비워 두려면 Enter 키 누름
6. "1"이라는 인스턴스 수를 지정합니다.

사용된 서비스를 추가하는 항목이 모두 표시됩니다.

```bash
? Name of the application service: azurevoteback
? Input the Image Name: alpine:redis
? Commands: 
? Number of instances of guest container application: 1
   create TestContainer/azurevotebackPkg/ServiceManifest.xml
   create TestContainer/azurevotebackPkg/config/Settings.xml
   create TestContainer/azurevotebackPkg/code/Dummy.txt
```

이 자습서의 나머지 부분은 **TestContainer** 디렉터리에서 작업합니다. 예: *./TestContainer/TestContainer* 이 디렉터리의 콘텐츠는 다음과 같아야 합니다.
```bash
$ ls
ApplicationManifest.xml azurevotefrontPkg azurevotebackPkg
```

## <a name="configure-the-application-manifest-with-credentials-for-azure-container-registry"></a>Azure Container Registry의 자격 증명으로 응용 프로그램 매니페스트 구성
Service Fabric이 Azure Container Registry에서 컨테이너 이미지를 끌어오려면 **ApplicationManifest.xml**에서 자격 증명을 제공해야 합니다. 

ACR 인스턴스에 로그인합니다. **az acr login** 명령을 사용하여 작업을 완료합니다. 컨테이너 레지스트리가 생성될 때 지정된 고유한 이름을 입력합니다.

```bash
az acr login --name <acrName>
```

이 명령이 완료되면 **로그인했습니다.** 라는 메시지를 반환합니다.

다음으로 컨테이너 레지스트리 암호를 가져오는 다음 명령을 실행합니다. 이 암호는 Service Fabric에서 컨테이너 이미지를 끌어오기 위해 ACR를 사용하여 인증하는 데 사용됩니다.

```bash
az acr credential show -n <acrName> --query passwords[0].value
```

**ApplicationManifest.xml**에서 프런트 엔드 서비스에 대한 **ServiceManifestImport** 요소 아래에 있는 코드 조각을 추가합니다. **acrName**을 **AccountName** 필드에 삽입하고 이전 명령에서 반환된 암호를 **암호** 필드에 사용합니다. 이 문서의 끝에서 전체 **ApplicationManifest.xml**을 제공합니다. 

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <RepositoryCredentials AccountName="<acrName>" Password="<password>" PasswordEncrypted="false"/>
  </ContainerHostPolicies>
</Policies>
```
## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>통신 및 컨테이너 포트와 호스트 포트 간 매핑 구성

### <a name="configure-communication-port"></a>통신 포트 구성

클라이언트가 서비스와 통신할 수 있도록 HTTP 끝점을 구성합니다. *./TestContainer/azurevotefrontPkg/ServiceManifest.xml* 파일을 열고 **ServiceManifest** 요소에서 끝점 리소스를 선언합니다.  프로토콜, 포트 및 이름을 추가합니다. 이 자습서에서 서비스는 포트 80에서 수신 대기합니다. 다음 코드 조각은 리소스의 *ServiceManifest* 태그 아래에 배치됩니다.
  
```xml
<Resources>
  <Endpoints>
    <!-- This endpoint is used by the communication listener to obtain the port on which to 
            listen. Please note that if your service is partitioned, this port is shared with 
            replicas of different partitions that are placed in your code. -->
    <Endpoint Name="azurevotefrontTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
  </Endpoints>
</Resources>

```
  
마찬가지로 백 엔드 서비스에 대한 서비스 매니페스트를 수정합니다. *./TestContainer/azurevotebackPkg/ServiceManifest.xml*을 열고 **ServiceManifest** 요소에서 엔드포인트 리소스를 선언합니다. 이 자습서에서 6379라는 Redis 기본값이 유지 관리됩니다. 다음 코드 조각은 리소스의 *ServiceManifest* 태그 아래에 배치됩니다.

```xml
<Resources>
  <Endpoints>
    <!-- This endpoint is used by the communication listener to obtain the port on which to 
            listen. Please note that if your service is partitioned, this port is shared with 
            replicas of different partitions that are placed in your code. -->
    <Endpoint Name="azurevotebackTypeEndpoint" Port="6379" Protocol="tcp"/>
  </Endpoints>
</Resources>
```
**UriScheme**을 입력하면 컨테이너 끝점이 검색될 수 있도록 Service Fabric Naming 서비스에 자동으로 등록됩니다. 백 엔드 서비스의 전체 ServiceManifest.xml 예제 파일을 이 문서의 끝에서 예제로 제공합니다. 

### <a name="map-container-ports-to-a-service"></a>서비스에 컨테이너 포트 매핑
또한 클러스터에서 컨테이너를 노출하려면 'ApplicationManifest.xml'에서 포트 바인딩을 만들어야 합니다. **PortBinding** 정책은 **ServiceManifest.xml** 파일에서 정의한 **끝점**을 참조합니다. 이러한 끝점에 들어오는 요청은 여기에서 열리고 바인딩된 컨테이너 포트에 매핑됩니다. **ApplicationManifest.xml** 파일에서 다음 코드를 추가하여 끝점에 포트 80 및 6379를 바인딩합니다. 이 문서의 끝에서 전체 **ApplicationManifest.xml**을 사용할 수 있습니다. 
  
```xml
<ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="80" EndpointRef="azurevotefrontTypeEndpoint"/>
</ContainerHostPolicies>
```

```xml
<ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="6379" EndpointRef="azurevotebackTypeEndpoint"/>
</ContainerHostPolicies>
```

### <a name="add-a-dns-name-to-the-backend-service"></a>백 엔드 서비스에 DNS 이름 추가
  
Service Fabric이 백 엔드 서비스에 이 DNS 이름을 할당하려면 **ApplicationManifest.xml**에서 이름을 지정해야 합니다. 표시된 대로 **ServiceDnsName** 특성을 **서비스** 요소에 추가합니다. 
  
```xml
<Service Name="azurevoteback" ServiceDnsName="redisbackend.testapp">
  <StatelessService ServiceTypeName="azurevotebackType" InstanceCount="1">
    <SingletonPartition/>
  </StatelessService>
</Service>
```

프런트 엔드 서비스는 Redis 인스턴스의 DNS 이름을 가져오기 위해 환경 변수를 참고합니다. 이 환경 변수는 Docker 이미지를 생성하는 데 사용된 Dockerfile에서 이미 정의되었으며 아무 작업도 수행할 필요가 없습니다.
  
```Dockerfile
ENV REDIS redisbackend.testapp
```
  
다음 코드 조각은 프런트 엔드 Python 코드가 Dockerfile에 설명된 환경 변수를 선택하는 방법을 보여 줍니다. 여기에서 아무 작업도 수행할 필요가 없습니다. 

```python
# Get DNS Name
redis_server = os.environ['REDIS'] 

# Connect to the Redis store
r = redis.StrictRedis(host=redis_server, port=6379, db=0)
```

자습서의 이 시점에서 서비스 패키지 응용 프로그램의 템플릿은 클러스터에 배포하는 데 사용할 수 있습니다. 이후 자습서에서 이 응용 프로그램을 배포하고 Service Fabric 클러스터에서 실행합니다.

## <a name="create-a-service-fabric-cluster"></a>Service Fabric 클러스터 만들기
응용 프로그램을 Azure의 클러스터에 배포하려면 고유한 클러스터를 만듭니다.

파티 클러스터는 Azure에서 호스팅되는 시간이 제한된 체험용 Service Fabric 클러스터이며 누구든지 응용 프로그램을 배포하고 플랫폼에 대해 알아볼 수 있는 Service Fabric 팀에서 실행합니다. 파티 클러스터에 대한 액세스 권한을 얻으려면 [지침에 따릅니다](http://aka.ms/tryservicefabric). 

보안 파티 클러스터에서 관리 작업을 수행하기 위해 Service Fabric Explorer, CLI 또는 Powershell을 사용할 수 있습니다. Service Fabric Explorer를 사용하려면 파티 클러스터 웹 사이트에서 PFX 파일을 다운로드하고, 인증서 저장소(Windows 또는 Mac) 또는 브라우저 자체(Ubuntu)로 인증서를 가져와야 합니다. 파티 클러스터의 자체 서명된 인증서에는 암호가 없습니다. 

Powershell 또는 CLI를 사용하여 관리 작업을 수행하려면 PFX(Powershell) 또는 PEM(CLI)이 필요합니다. PFX를 PEM 파일로 변환하려면 다음 명령을 실행합니다.  

```bash
openssl pkcs12 -in party-cluster-1277863181-client-cert.pfx -out party-cluster-1277863181-client-cert.pem -nodes -passin pass:
```

자체 클러스터를 만드는 방법은 [Azure에서 Service Fabric 클러스터 만들기](service-fabric-tutorial-create-vnet-and-linux-cluster.md)를 참조하세요.

## <a name="build-and-deploy-the-application-to-the-cluster"></a>응용 프로그램 빌드 및 클러스터에 배포
Service Fabric CLI를 사용하여 Azure 클러스터에 응용 프로그램을 배포할 수 있습니다. Service Fabric CLI가 컴퓨터에 설치되어 있지 않으면 [여기](service-fabric-get-started-linux.md#set-up-the-service-fabric-cli)의 지침에 따라 설치합니다. 

Azure에서 Service Fabric 클러스터에 연결합니다. 자리 표시자 엔드포인트를 고유한 값으로 대체합니다. 엔드포인트는 아래와 비슷한 전체 URL이어야 합니다.

```bash
sfctl cluster select --endpoint https://linh1x87d1d.westus.cloudapp.azure.com:19080 --pem party-cluster-1277863181-client-cert.pem --no-verify
```

**TestContainer** 디렉터리에 제공된 설치 스크립트를 사용하여 클러스터의 이미지 저장소에 응용 프로그램 패키지를 복사하고 응용 프로그램 유형을 등록하며 응용 프로그램의 인스턴스를 만듭니다.

```bash
./install.sh
```

브라우저를 열고 http://lin4hjim3l4.westus.cloudapp.azure.com:19080/Explorer에서 Service Fabric Explorer로 이동합니다. 응용 프로그램 노드를 확장하면 응용 프로그램 유형에 대한 항목 및 인스턴스에 대한 다른 항목이 만들어집니다.

![Service Fabric Explorer][sfx]

실행 중인 응용 프로그램에 연결하려면 웹 브라우저를 열고 클러스터 URL(예: http://lin0823ryf2he.cloudapp.azure.com:80)로 이동합니다. 웹 UI에서 선택 응용 프로그램이 표시됩니다.

![votingapp][votingapp]

## <a name="clean-up"></a>정리
템플릿에 제공된 제거 스크립트를 사용하여 클러스터에서 응용 프로그램 인스턴스를 삭제하고 응용 프로그램 유형을 등록 해제합니다. 이 명령은 인스턴스를 정리하는 데 시간이 걸리고 'install.sh' 명령은 이 스크립트 직후에 실행할 수 없습니다. 

```bash
./uninstall.sh
```

## <a name="examples-of-completed-manifests"></a>완료된 매니페스트의 예제

### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ApplicationManifest ApplicationTypeName="TestContainerType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="azurevotefrontPkg" ServiceManifestVersion="1.0.0"/>
    <Policies> 
    <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myaccountname" Password="<password>" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="azurevotefrontTypeEndpoint"/>
    </ContainerHostPolicies>
        </Policies>
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="azurevotebackPkg" ServiceManifestVersion="1.0.0"/>
      <Policies> 
        <ContainerHostPolicies CodePackageRef="Code">
          <PortBinding ContainerPort="6379" EndpointRef="azurevotebackTypeEndpoint"/>
        </ContainerHostPolicies>
      </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="azurevotefront">
      <StatelessService ServiceTypeName="azurevotefrontType" InstanceCount="1">
        <SingletonPartition/>
      </StatelessService>
    </Service>
    <Service Name="azurevoteback" ServiceDnsName="redisbackend.testapp">
      <StatelessService ServiceTypeName="azurevotebackType" InstanceCount="1">
        <SingletonPartition/>
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

### <a name="front-end-servicemanifestxml"></a>Front-end ServiceManifest.xml 
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="azurevotefrontPkg" Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" >

   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="azurevotefrontType" UseImplicitHost="true">
   </StatelessServiceType>
   </ServiceTypes>
   
   <CodePackage Name="code" Version="1.0.0">
      <EntryPoint>
         <ContainerHost>
            <ImageName>acrName.azurecr.io/azure-vote-front:v1</ImageName>
            <Commands></Commands>
         </ContainerHost>
      </EntryPoint>
      <EnvironmentVariables> 
      </EnvironmentVariables> 
   </CodePackage>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="azurevotefrontTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>

 </ServiceManifest>
```

### <a name="redis-servicemanifestxml"></a>Redis ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="azurevotebackPkg" Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" >

   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="azurevotebackType" UseImplicitHost="true">
   </StatelessServiceType>
   </ServiceTypes>
   
   <CodePackage Name="code" Version="1.0.0">
      <EntryPoint>
         <ContainerHost>
            <ImageName>alpine:redis</ImageName>
            <Commands></Commands>
         </ContainerHost>
      </EntryPoint>
      <EnvironmentVariables> 
      </EnvironmentVariables> 
   </CodePackage>
     <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="azurevotebackTypeEndpoint" Port="6379" Protocol="tcp"/>
    </Endpoints>
  </Resources>
 </ServiceManifest>
```
## <a name="next-steps"></a>다음 단계

이 자습서에서는 Yeoman을 사용하여 Service Fabric 응용 프로그램에 여러 컨테이너를 패키징했습니다. 그런 다음 이 응용 프로그램을 배포하고 Service Fabric 클러스터에서 실행했습니다. 다음 단계가 완료되었습니다.

> [!div class="checklist"]
> * Yeoman 설치  
> * Yeoman을 사용하여 응용 프로그램 패키지 만들기
> * 응용 프로그램 패키지에서 컨테이너에서 사용할 설정 구성
> * 응용 프로그램 빌드  
> * 응용 프로그램 배포 및 실행 
> * 응용 프로그램 정리

Service Fabric에서 응용 프로그램의 장애 조치 및 크기 조정에 대한 자세한 내용은 다음 자습서를 진행하세요.

> [!div class="nextstepaction"]
> [응용 프로그램 장애 조치 및 크기 조정에 대한 자세한 내용](service-fabric-tutorial-containers-failover.md)

[votingapp]: ./media/service-fabric-tutorial-deploy-run-containers/votingapp.png
[sfx]: ./media/service-fabric-tutorial-deploy-run-containers/containerspackagetutorialsfx.png


