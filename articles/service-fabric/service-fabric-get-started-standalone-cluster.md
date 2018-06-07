---
title: "독립 실행형 Azure Service Fabric 클러스터 설정 | Microsoft Docs"
description: "동일한 컴퓨터에서 실행되는 3개의 노드가 있는 독립 실행형 개발 클러스터를 만듭니다. 이 설정을 마치면 다중 컴퓨터 클러스터를 만들 수 있습니다."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: 5438d8d366ef989d5ae29581477513f8c884c4b3
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2017
---
# <a name="create-your-first-service-fabric-standalone-cluster"></a>첫 번째 Service Fabric 독립 실행형 클러스터 만들기
온-프레미스 또는 클라우드에 Windows Server 2012 R2 또는 Windows Server 2016을 실행하는 컴퓨터나 가상 컴퓨터에 Service Fabric 독립 실행형 클러스터를 만들 수 있습니다. 이 빠른 시작을 통해 몇 분만에 독립 실행형 개발 클러스터를 만들 수 있습니다.  작업을 완료하면 앱을 배포할 수 있는 단일 컴퓨터에 3개 노드 클러스터가 실행 중입니다.

## <a name="before-you-begin"></a>시작하기 전에
Service Fabric은 독립 실행형 Service Fabric 클러스터를 만드는 설치 패키지를 제공합니다.  [설치 패키지를 다운로드합니다](http://go.microsoft.com/fwlink/?LinkId=730690).  개발 클러스터를 설정한 컴퓨터 또는 가상 컴퓨터의 폴더에 설치 패키지의 압축을 풉니다.  설치 패키지의 내용은 [여기](service-fabric-cluster-standalone-package-contents.md)에서 자세히 설명합니다.

클러스터를 배포하고 구성하는 클러스터 관리자는 컴퓨터에서 관리자 권한이 있어야 합니다. 도메인 컨트롤러에 Service Fabric을 설치할 수 없습니다.

## <a name="validate-the-environment"></a>환경 유효성 검사
독립 실행형 패키지의 *TestConfiguration.ps1* 스크립트는 지정된 환경에 클러스터를 배포할 수 있는지 여부를 확인하는 모범 사례 분석기로 사용됩니다. [배포 준비](service-fabric-cluster-standalone-deployment-preparation.md)는 필수 구성 요소 및 환경 요구 사항을 나열합니다. 개발 클러스터를 만들 수 있는지 확인하는 스크립트를 실행합니다.

```powershell
.\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
```
## <a name="create-the-cluster"></a>클러스터 만들기
몇 가지 샘플 클러스터 구성 파일은 설치 패키지와 함께 설치됩니다. *ClusterConfig.Unsecure.DevCluster.json*은 가장 간단한 클러스터 구성: 단일 컴퓨터에서 실행되는 비보안 3개 노드 클러스터입니다.  다른 구성 파일에서는 X.509 인증서 또는 Windows 보안을 사용하여 보호되는 단일 또는 다중 컴퓨터 클러스터를 설명합니다.  이 자습서에 대한 기본 구성 설정을 수정할 필요는 없지만 구성 파일을 확인하고 설정에 익숙해지도록 합니다.  **노드** 섹션에서는 클러스터에 있는 세 개의 노드(이름, IP 주소, [노드 유형, 장애 도메인 및 업그레이드 도메인](service-fabric-cluster-manifest.md#nodes-on-the-cluster))에 대해 설명합니다.  **속성** 섹션에서는 클러스터의 [보안, 안정성 수준, 진단 컬렉션 수준 및 노드의 형식](service-fabric-cluster-manifest.md#cluster-properties)을 정의합니다.

이 클러스터는 안전하지 않습니다.  누구든지 익명으로 연결하고 관리 작업을 수행할 수 있으므로 프로덕션 클러스터가 항상 X.509 인증서 또는 Windows 보안을 사용하여 보호되어야 합니다.  클러스터 생성 시에만 보안을 구성하므로 클러스터를 만든 후에 보안을 사용하도록 설정할 수 없습니다.  Service Fabric 클러스터 보안에 대한 자세한 내용은 [클러스터에 보안 적용](service-fabric-cluster-security.md)을 읽어 보세요.  

3개 노드 개발 클러스터를 만들려면 관리자 PowerShell 세션에서 *CreateServiceFabricCluster.ps1* 스크립트를 실행합니다.

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Service Fabric 런타임 패키지는 클러스터 생성 시 자동으로 다운로드되어 설치됩니다.

## <a name="connect-to-the-cluster"></a>클러스터에 연결
3개 노드 개발 클러스터가 현재 실행 중입니다. ServiceFabric PowerShell 모듈은 런타임에 설치됩니다.  Service Fabric 런타임에 클러스터가 같은 컴퓨터에서 실행되는지, 원격 컴퓨터에서 실행되는지 확인할 수 있습니다.  [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet은 클러스터에 대한 연결을 설정합니다.   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint localhost:19000
```
클러스터에 연결하는 다른 예제는 [보안 클러스터에 연결](service-fabric-connect-to-secure-cluster.md)을 참조하세요. 클러스터에 연결한 후에는 [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) cmdlet을 사용하여 클러스터의 노드 목록과 각 노드에 대한 상태 정보를 표시합니다. **HealthState**는 노드마다 *OK* 상태여야 합니다.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- -------- --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     vm2      localhost       NodeType2 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm1      localhost       NodeType1 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm0      localhost       NodeType0 5.6.220.9494 0                     Up 00:02:43   00:00:00              OK
```

## <a name="visualize-the-cluster-using-service-fabric-explorer"></a>Service Fabric Explorer를 사용하여 클러스터 시각화
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)는 클러스터를 시각화하고 응용 프로그램을 관리할 수 있는 좋은 도구입니다.  Service Fabric Explorer는 브라우저를 사용하여 [http://localhost:19080/Explorer](http://localhost:19080/Explorer)로 이동하여 액세스할 수 있는 클러스터에서 실행되는 서비스입니다. 

클러스터 대시보드는 응용 프로그램 및 노드 상태에 대한 요약을 포함하여 클러스터에 대한 개요를 제공합니다. 노드 보기는 클러스터의 물리적 레이아웃을 보여 줍니다. 지정된 노드의 경우 해당 노드에 배포된 코드를 가진 응용 프로그램을 검사할 수 있습니다.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="remove-the-cluster"></a>클러스터 제거
클러스터를 제거하려면 패키지 폴더에서 *RemoveServiceFabricCluster.ps1* Powershell 스크립트를 실행하고 JSON 구성 파일에 대한 경로를 전달합니다. 경우에 따라 삭제 로그 위치를 지정할 수도 있습니다.

```powershell
# Removes Service Fabric cluster nodes from each computer in the configuration file.
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -Force
```

컴퓨터에서 Service Fabric 런타임을 제거하려면 패키지 폴더에서 다음 PowerShell 스크립트를 실행합니다.

```powershell
# Removes Service Fabric from the current computer.
.\CleanFabric.ps1
```

## <a name="next-steps"></a>다음 단계
이제 독립 실행형 개발 클러스터를 설정했으며 다음 문서를 사용해보세요.
* [다중 컴퓨터 독립 실행형 클러스터를 설정](service-fabric-cluster-creation-for-windows-server.md)하고 보안을 사용하도록 설정합니다.
* [PowerShell을 사용하여 앱 배포](service-fabric-deploy-remove-applications.md)

[service-fabric-explorer]: ./media/service-fabric-get-started-standalone-cluster/sfx.png
