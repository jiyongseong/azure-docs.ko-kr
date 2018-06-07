---
title: Service Fabric Azure Files 볼륨 드라이버(미리 보기) | Microsoft Docs
description: Service Fabric은 컨테이너에서 볼륨을 백업하도록 Azure Files 사용을 지원합니다. 현재 미리 보기로 제공되고 있습니다.
services: service-fabric
documentationcenter: other
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: other
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 4/30/2018
ms.author: subramar
ms.openlocfilehash: 2d98cff1a5869091aa81097bbb34da6e525a2ad5
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-azure-files-volume-driver-preview"></a>Service Fabric Azure Files 볼륨 드라이버(미리 보기)
Azure Files 볼륨 플러그 인은 Docker 컨테이너에 대한 [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) 기반 볼륨을 제공하는 [Docker 볼륨 플러그 인](https://docs.docker.com/engine/extend/plugins_volume/)입니다. 이 Docker 볼륨 플러그 인은 Service Fabric 클러스터에 배포할 수 있는 Service Fabric 응용 프로그램으로 패키지됩니다. 용도는 클러스터에 배포되는 다른 Service Fabric 컨테이너 응용 프로그램에 대한 Azure Files 기반 볼륨을 제공하는 것입니다.

> [!NOTE]
> Azure Files 볼륨 플러그 인의 버전 6.255.389.9494는 이 문서와 함께 사용할 수 있는 미리 보기 릴리스입니다. 미리 보기 릴리스로 프로덕션 환경에서 사용할 수 있도록 지원하지 **않습니다**.
>

## <a name="prerequisites"></a>필수 조건
* Windows 버전의 Azure Files 볼륨 플러그 인은 [Windows Server 버전 1709](https://docs.microsoft.com/en-us/windows-server/get-started/whats-new-in-windows-server-1709), [Windows 10 버전 1709](https://docs.microsoft.com/en-us/windows/whats-new/whats-new-windows-10-version-1709) 이상 운영 체제에서만 작동합니다. Linux 버전의 Azure Files 볼륨 플러그 인은 Service Fabric에서 지원하는 모든 운영 체제 버전에서 작동합니다.

* [Azure Files 설명서](https://docs.microsoft.com/en-us/azure/storage/files/storage-how-to-create-file-share)의 지침을 따라 볼륨으로 사용할 Service Fabric 컨테이너 응용 프로그램에 대한 파일 공유를 만듭니다.

* [Service Fabric 모듈을 사용하는 Powershell](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started) 또는 [SFCTL](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cli) 설치가 필요합니다.

## <a name="deploy-the-service-fabric-azure-files-application"></a>Service Fabric Azure Files 응용 프로그램 배포

컨테이너에 대한 볼륨을 제공하는 Service Fabric 응용 프로그램은 다음 [링크](https://aka.ms/sfvolume)에서 다운로드할 수 있습니다. 응용 프로그램은 [PowerShell](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-deploy-remove-applications), [CLI](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-application-lifecycle-sfctl) 또는 [FabricClient API](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-deploy-remove-applications-fabricclient)를 통해 클러스터에 배포될 수 있습니다.

1. 명령줄을 사용하여 다운로드한 응용 프로그램 패키지의 루트 디렉터리로 디렉터리를 변경합니다. 

    ```powershell 
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. 이미지 저장소로 응용 프로그램 패키지를 복사합니다. [ApplicationPackagePath] 및 [ImageStoreConnectionString]에 대한 적절한 값으로 아래 명령을 실행합니다.

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath [ApplicationPackagePath] -ImageStoreConnectionString [ImageStoreConnectionString] -ApplicationPackagePathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl cluster select --endpoint https://testcluster.westus.cloudapp.azure.com:19080 --pem test.pem --no-verify
    sfctl application upload --path [ApplicationPackagePath] --show-progress
    ```

3. 응용 프로그램 형식 등록

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl application provision --application-type-build-path [ApplicationPackagePath]
    ```

4. 응용 프로그램 만들기 명령에서 아래 응용 프로그램을 만들기 위해 **ListenPort** 응용 프로그램 매개 변수를 적어 둡니다. 이 응용 프로그램 매개 변수에 대해 지정된 이 값은 Azure Files 볼륨 플러그 인이 Docker 디먼의 요청을 수신하는 포트입니다. 응용 프로그램에 제공된 포트가 클러스터 또는 응용 프로그램에서 사용하는 다른 포트와 충돌하지 않는지 확인하는 것은 중요합니다.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.255.389.9494 -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.255.389.9494 --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]

> Windows Server 2016 Datacenter는 커넥터에 SMB 마운트를 매핑하도록 지원하지 않습니다([Windows Server 버전 1709에서만 지원됨](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/container-storage)). 이러한 제약으로 인해 1709보다 오래된 버전에서 네트워크 볼륨 매핑 및 Azure Files 볼륨 드라이버를 사용할 수 없습니다. 
>   

### <a name="deploy-the-application-on-a-local-development-cluster"></a>로컬 개발 클러스터에서 응용 프로그램 배포
Azure Files 볼륨 플러그 인 응용 프로그램에 대한 기본 서비스 인스턴스 수는 -1로, 클러스터의 각 노드에 배포된 서비스의 인스턴스가 있다는 것을 의미합니다. 그러나 로컬 개발 클러스터에서 Azure Files 볼륨 플러그 인 응용 프로그램을 배포할 때 서비스 인스턴스 수는 1로 지정되어야 합니다. **InstanceCount** 응용 프로그램 매개 변수를 통해 이를 수행할 수 있습니다. 따라서 로컬 개발 클러스터에서 Azure Files 볼륨 플러그 인 응용 프로그램 배포에 대한 명령은 다음과 같습니다.

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.255.389.9494 -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.255.389.9494 --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```
## <a name="configure-your-applications-to-use-the-volume"></a>볼륨을 사용할 응용 프로그램 구성
다음 코드 조각에서는 응용 프로그램의 응용 프로그램 매니페스트에서 Azure Files 기반 볼륨을 지정하는 방법을 보여줍니다. 관심 있는 특정 요소는 **볼륨** 태그입니다.

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
      <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      <Parameter Name="MyStorageVar" DefaultValue="c:\tmp"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv"> 
            <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
            <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
                <DriverOption Name="shareName" Value="" />
                <DriverOption Name="storageAccountName" Value="" />
                <DriverOption Name="storageAccountKey" Value="" />
            </Volume>
       </ContainerHostPolicies>
   </Policies>
    </ServiceManifestImport>
    <ServiceTemplates>
        <StatelessService ServiceTypeName="StatelessNodeService" InstanceCount="5">
            <SingletonPartition></SingletonPartition>
        </StatelessService>
    </ServiceTemplates>
</ApplicationManifest>
```

Azure Files 볼륨 플러그 인에 대한 드라이버 이름은 **sfazurefile**입니다. 이 값은 응용 프로그램 매니페스트에서 **Volume** 요소의 **Driver** 특성에 대해 설정됩니다.

위의 코드 조각의 **Volume** 요소에서 Azure Files 볼륨 플러그 인에 다음 태그가 필요합니다. 
- **Source** - 컨테이너 또는 영구 원격 저장소를 호스트하는 VM의 폴더일 수 있는 원본 폴더를 나타냅니다.
- **Destination** - 이 태그는 실행 중인 컨테이너에서 **Source**가 매핑되는 위치입니다. 따라서 대상은 컨테이너 내에 이미 존재하는 위치가 될 수 없습니다.

위의 코드 조각의 **DriverOption** 요소에 나와 있는 것처럼 Azure Files 볼륨 플러그 인은 다음 드라이버 옵션을 지원합니다.

- **shareName** - 컨테이너에 대한 볼륨을 제공하는 Azure Files 파일 공유의 이름
- **storageAccountName** - Azure Files 파일 공유를 포함하는 Azure 저장소 계정의 이름
- **storageAccountKey** - Azure Files 파일 공유를 포함하는 Azure 저장소 계정에 대한 액세스 키

위의 모든 드라이버 옵션이 필요합니다. 

## <a name="using-your-own-volume-or-logging-driver"></a>사용자 고유의 볼륨 또는 로깅 드라이버 사용
Service Fabric은 사용자 고유의 사용자 지정 볼륨 또는 로깅 드라이버 사용을 허용합니다. Docker 볼륨/로깅 드라이버가 클러스터에 설치되어 있지 않으면 RDP/SSH 프로토콜을 사용하여 수동으로 설치할 수 있습니다. [가상 머신 확장 집합 시작 스크립트](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) 또는 [SetupEntryPoint 스크립트](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model#describe-a-service)를 통해 이러한 프로토콜을 사용하여 설치를 수행할 수 있습니다.

[Azure용 Docker 볼륨 드라이버](https://docs.docker.com/docker-for-azure/persistent-data-volumes/)를 설치하는 스크립트의 예는 다음과 같습니다.

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

응용 프로그램에서 설치한 볼륨 또는 로깅 드라이버를 사용하려면 응용 프로그램 매니페스트에서 **ContainerHostPolicies** 아래의 **Volume** 및 **LogConfig** 요소에 적절한 값을 지정해야 합니다. 

```xml
<ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv"> 
    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
    <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
    <LogConfig Driver="[YOUR_LOG_DRIVER]" >
        <DriverOption Name="test" Value="vale"/>
    </LogConfig>
    <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
    <Volume Source="[MyStorageVar]" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
    <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="[YOUR_VOLUME_DRIVER]" IsReadOnly="true">
        <DriverOption Name="[name]" Value="[value]"/>
    </Volume>
</ContainerHostPolicies>
```

## <a name="next-steps"></a>다음 단계
* 볼륨 드라이버를 포함하여 컨테이너 샘플을 보려면 [Service Fabric 컨테이너 샘플](https://github.com/Azure-Samples/service-fabric-containers)을 참조하세요.
* 컨테이너를 Service Fabric 클러스터에 배포하려면 [Service Fabric에 컨테이너 배포](service-fabric-deploy-container.md) 문서를 참조하세요.


