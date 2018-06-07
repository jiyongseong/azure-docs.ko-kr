---
title: '자습서: Service Fabric 독립 실행형 클라이언트 설치 - Azure Service Fabric | Microsoft Docs'
description: 이 자습서에서는 이전 자습서 문서에서 만든 클러스터에 Service Fabric 독립 실행형 클라이언트를 설치하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: david-stanford
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: dastanfo
ms.custom: mvc
ms.openlocfilehash: b9b9e08676228ddbdea8be91253b41dac8ef8fb8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211738"
---
# <a name="tutorial-install-and-create-service-fabric-cluster"></a>자습서: Service Fabric 클러스터 설치 및 만들기

Service Fabric 독립 실행형 클러스터는 사용자 자신의 환경을 선택하고 Service Fabric이 수행하는 "모든 OS 및 모든 클라우드" 접근 방법의 일부로서 클러스터를 만드는 옵션을 제공합니다. 이 자습서 시리즈에서는 AWS에서 호스팅되는 독립 실행형 클러스터를 만들고 여기에 응용 프로그램을 설치합니다.

이 자습서는 시리즈의 2부입니다. 이 자습서에서는 Service Fabric 독립 실행형 클러스터를 만들기 위한 단계를 안내합니다.

시리즈 2부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Service Fabric 독립 실행형 패키지 다운로드 및 설치
> * Service Fabric 클러스터 만들기
> * Service Fabric 클러스터에 연결

## <a name="download-the-service-fabric-for-windows-server-package"></a>Windows Server용 Service Fabric 패키지 다운로드

Service Fabric은 독립 실행형 Service Fabric 클러스터를 만드는 설치 패키지를 제공합니다.  로컬 컴퓨터에 [설치 패키지를 다운로드](http://go.microsoft.com/fwlink/?LinkId=730690)합니다.  성공적으로 다운로드되면 EC2 인스턴스에 대한 RDP 연결을 통해 복사하고, 바탕 화면에 붙여넣습니다.

zip 파일을 선택하고 바로 가기 메뉴를 열고 **모두 추출** > **추출**을 선택합니다.  파일을 추출하면 바탕 화면에 zip 파일 이름과 동일한 폴더를 생성합니다.

[설치 패키지의 콘텐츠](service-fabric-cluster-standalone-package-contents.md)에 대해 자세히 알아보려는 경우

## <a name="set-up-your-configuration-file"></a>구성 파일 설정

3개의 노드 창 클러스터를 빌드하고 있으므로 `ClusterConfig.Unsecure.MultiMachine.json` 파일을 수정해야 합니다.

다음으로 8, 15, 22줄의 파일에서 발생하는 세 개의 ipAddress 줄을 각 인스턴스에 대한 IP 주소로 업데이트합니다.

노드를 업데이트하면 다음과 같이 나타납니다.

```json
        {
            "nodeName": "vm0",
            "ipAddress": "172.31.27.1",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        }
```

그런 다음, 몇 가지 속성을 업데이트해야 합니다.  34줄에서 진단 저장소에 대한 연결 문자열을 수정해야 합니다. 수정 후 `"connectionstring": "\\\\172.31.27.1\\c$\\DiagnosticsStore"`에서 대체된 IP 주소로 표시되어야 합니다.

연결 문자열을 업데이트한 후 폴더를 만들어야 합니다.  다음 명령은 폴더를 만듭니다. 아래 IP 주소를 연결 문자열에 삽입한 IP 주소로 바꿔야 합니다.

```powershell
mkdir \\172.31.27.1\c$\DiagnosticsStore
```

마지막으로 `nodeTypes` 섹션에서 구성을 추가하고, Windows에서 사용할 임시 포트를 매핑하는 새 섹션을 추가합니다.  구성 파일은 다음과 같이 표시됩니다.

```json
"applicationPorts": {
    "startPort": "20001",
    "endPort": "20031"
},
"ephemeralPorts": {
    "startPort": "20606",
    "endPort": "20861"
},
"isPrimary": true
```

## <a name="validate-the-environment"></a>환경 유효성 검사

독립 실행형 패키지의 *TestConfiguration.ps1* 스크립트는 지정된 환경에 클러스터를 배포할 수 있는지 여부를 확인하는 모범 사례 분석기로 사용됩니다. [배포 준비](service-fabric-cluster-standalone-deployment-preparation.md)는 필수 구성 요소 및 환경 요구 사항을 나열합니다. 개발 클러스터를 만들 수 있는지 확인하는 스크립트를 실행합니다.

```powershell
cd .\Desktop\Microsoft.Azure.ServiceFabric.WindowsServer.6.2.274.9494\
.\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
```

아래와 유사한 출력이 표시됩니다. 아래쪽 필드 "Passed"가 `True`로 반환되는 경우 온전성 검사를 통과했고 클러스터는 입력 구성에 따라 배포 가능한 것으로 보입니다.

```powershell
Trace folder already exists. Traces will be written to existing trace folder: C:\Users\Administrator\Desktop\Microsoft.Azure.ServiceFabric.WindowsServer.6.2.274.9494\DeploymentTraces
Running Best Practices Analyzer...
Best Practices Analyzer completed successfully.


LocalAdminPrivilege        : True
IsJsonValid                : True
IsCabValid                 :
RequiredPortsOpen          : True
RemoteRegistryAvailable    : True
FirewallAvailable          : True
RpcCheckPassed             : True
NoConflictingInstallations : True
FabricInstallable          : True
DataDrivesAvailable        : True
NoDomainController         : True
Passed                     : True
```

## <a name="create-the-cluster"></a>클러스터 만들기

클러스터 구성의 유효성 검사를 성공적으로 수행한 후 *CreateServiceFabricCluster.ps1* 스크립트를 실행하여 구성 파일의 가성 머신에 Service Fabric 클러스터를 배포합니다.

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -AcceptEULA
```

모든 작동하는 경우 다음과 같은 출력이 표시됩니다.

```powershell
Your cluster is successfully created! You can connect and manage your cluster using Microsoft Azure Service Fabric Explorer or PowerShell. To connect through PowerShell, run 'Connect-ServiceFabricCluster [ClusterConnectionEndpoint]'.
```

> [!NOTE]
> CreateServiceFabricCluster.ps1 PowerShell 스크립트를 실행한 VM/컴퓨터에 배포 추적이 기록됩니다. 스크립트가 실행된 디렉터리에 따라 하위 폴더 DeploymentTraces에서 찾을 수 있습니다. Service Fabric이 컴퓨터에 올바르게 배포되었는지 확인하려면 클러스터 구성 파일 FabricSettings 섹션에서 설명된 대로 FabricDataRoot 디렉터리에 설치된 파일을 찾습니다(기본적으로 c:\ProgramData\SF). 또한 작업 관리자에서 실행 중인 FabricHost.exe 및 Fabric.exe 프로세스를 볼 수 있습니다.
>
>

### <a name="bring-up-service-fabric-explorer"></a>Service Fabric Explorer 표시

이제 http://localhost:19080/Explorer/index.html을 사용하여 컴퓨터 중 하나에서 직접 또는 http://<*IPAddressofaMachine*>:19080/Explorer/index.html을 사용하여 원격으로 Service Fabric Explorer를 사용하여 클러스터에 연결할 수 있습니다.

## <a name="add-and-remove-nodes"></a>노드 추가 및 제거

비즈니스 요구가 변경됨에 따라 독립 실행형 서비스 패브릭 클러스터에 노드를 추가 또는 제거할 수 있습니다. 자세한 단계는 [서비스 패브릭 독립 실행형 클러스터에 노드 추가 또는 제거](service-fabric-cluster-windows-server-add-remove-nodes.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

시리즈 2부에서는 다음 방법을 통해 저장소 계정에 대량의 임의 데이터를 병렬로 업로드하는 방법에 대해 배웠습니다.

> [!div class="checklist"]
> * 연결 문자열 구성
> * 응용 프로그램 빌드
> * 응용 프로그램 실행
> * 연결 수의 유효성 검사

시리즈의 3부로 이동하여 사용자가 만든 클러스터로 응용 프로그램을 설치합니다.

> [!div class="nextstepaction"]
> [서비스 패브릭 클러스터에 응용 프로그램 설치](service-fabric-tutorial-standalone-install-an-application.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png