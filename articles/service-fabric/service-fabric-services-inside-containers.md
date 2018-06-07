---
title: Azure Service Fabric 마이크로 서비스(미리 보기)를 컨테이너화하는 방법
description: Azure Service Fabric은 Service Fabric 마이크로 서비스를 컨테이너화하는 새로운 기능을 추가했습니다. 이 기능은 현재 미리 보기로 제공됩니다.
services: service-fabric
documentationcenter: .net
author: anmolah
manager: anmolah
editor: anmolah
ms.assetid: 0b41efb3-4063-4600-89f5-b077ea81fa3a
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/04/2017
ms.author: anmola
ms.openlocfilehash: 3741e74e70769d186da2757b43ca60bbb1e78a1f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="how-to-containerize-your-service-fabric-reliable-services-and-reliable-actors-preview"></a>Service Fabric Reliable Services 및 Reliable Actors(미리 보기)를 컨테이너화하는 방법

Service Fabric은 Service Fabric 마이크로 서비스(Reliable Services 및 Reliable Actor 기반 서비스)를 컨테이너화하도록 지원합니다. 자세한 내용은 [Service Fabric 컨테이너](service-fabric-containers-overview.md)를 참조하세요.

이 기능은 미리 보기 상태이며 이 문서에서는 컨테이너 내에서 서비스를 실행하는 다양한 단계를 제공합니다.  

> [!NOTE]
> 이 기능은 미리 보기로 제공되며 프로덕션에서 지원되지 않습니다. 현재 이 기능은 Windows에서만 작동합니다. 컨테이너를 실행하려면 컨테이너가 포함된 Windows Server 2016에서 클러스터를 실행해야 합니다.

## <a name="steps-to-containerize-your-service-fabric-application"></a>Service Fabric 응용 프로그램을 컨테이너화하는 단계

1. Visual Studio에서 Service Fabric 응용 프로그램을 엽니다.

2. 프로젝트에 [SFBinaryLoader.cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) 클래스를 추가합니다. 이 클래스의 코드는 응용 프로그램 내에 있는 Service Fabric 런타임 이진 파일을 컨테이너 내부에서 실행할 경우 바르게 로드하는 도우미입니다.

3. 컨테이너화하려는 각 코드 패키지에 대해 프로그램 진입점에 로더를 초기화합니다. 다음 코드 조각에 표시된 정적 생성자를 프로그램 진입점 파일에 추가합니다.

  ```csharp
  namespace MyApplication
  {
      internal static class Program
      {
          static Program()
          {
              SFBinaryLoader.Initialize();
          }

          /// <summary>
          /// This is the entry point of the service host process.
          /// </summary>
          private static void Main()
          {
  ```

4. 프로젝트를 빌드 및 [패키지](service-fabric-package-apps.md#Package-App)합니다. 패키지를 빌드하고 만들려면 솔루션 탐색기에서 응용 프로그램 프로젝트를 마우스 오른쪽 단추로 클릭하고 **패키지** 명령을 선택합니다.

5. 컨테이너화해야 하는 모든 코드 패키지에서 [CreateDockerPackage.ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1) PowerShell 스크립트를 실행합니다. 사용법은 다음과 같습니다.
  ```powershell
    $codePackagePath = 'Path to the code package to containerize.'
    $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
    $applicationExeName = 'Name of the ode package executable.'
    CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -ApplicationExeName $applicationExeName
 ```
  스크립트는 $dockerPackageOutputDirectoryPath에서 Docker 아티팩트를 포함한 폴더를 만듭니다. 포트를 노출하도록 생성된 Dockerfile을 수정하고, 필요에 따라 설치 스크립트를 실행합니다.

6. 다음으로 Docker 컨테이너 패키지를 [빌드](service-fabric-get-started-containers.md#Build-Containers)하고 리포지토리에 [푸시](service-fabric-get-started-containers.md#Push-Containers)해야 합니다.

7. 컨테이너 이미지, 리포지토리 정보, 레지스트리 인증 및 포트와 호스트 간 매핑을 추가하도록 ApplicationManifest.xml 및 ServiceManifest.xml을 수정합니다. 매니페스트를 수정하기 위해 [Azure Service Fabric 컨테이너 응용 프로그램 만들기](service-fabric-get-started-containers.md)를 참조하세요. 서비스 매니페스트의 코드 패키지 정의는 해당 컨테이너 이미지로 바꿔야 합니다. EntryPoint를 ContainerHost 형식으로 변경해야 합니다.

  ```xml
<!-- Code package is your service executable. -->
<CodePackage Name="Code" Version="1.0.0">
  <EntryPoint>
    <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
    <ContainerHost>
      <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
    </ContainerHost>
  </EntryPoint>
  <!-- Pass environment variables to your container: -->    
</CodePackage>
  ```

8. 복제기 및 서비스 끝점에 포트와 호스트 간 매핑을 추가합니다. 런타임 시 Service Fabric에서 이러한 포트를 모두 할당하기 때문에 ContainerPort는 매핑에 할당된 포트를 사용하도록 0으로 설정됩니다.

 ```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
  </ContainerHostPolicies>
</Policies>
 ```

9. 이 응용 프로그램을 테스트하려면 5.7 이상 버전을 실행하는 클러스터에 배포해야 합니다. 또한 클러스터 설정을 편집하고 업데이트하여 이 미리 보기 기능을 사용하도록 설정해야 합니다. 이 [문서](service-fabric-cluster-fabric-settings.md)의 단계에 따라 다음에 표시된 설정을 추가합니다.
```
      {
        "name": "Hosting",
        "parameters": [
          {
            "name": "FabricContainerAppsEnabled",
            "value": "true"
          }
        ]
      }
```
10. 다음으로 이 클러스터에 편집된 응용 프로그램 패키지를 [배포](service-fabric-deploy-remove-applications.md)합니다.

이제 컨테이너화된 Service Fabric 응용 프로그램이 클러스터를 실행해야 합니다.

## <a name="next-steps"></a>다음 단계
* [Service Fabric의 컨테이너](service-fabric-get-started-containers.md)를 실행하는 방법에 대해 자세히 알아봅니다.
* Service Fabric [응용 프로그램 수명 주기](service-fabric-application-lifecycle.md)에 대해 자세히 알아봅니다.
