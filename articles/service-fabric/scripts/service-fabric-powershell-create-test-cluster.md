---
title: Azure PowerShell 스크립트 샘플 - Service Fabric 클러스터 만들기 | Microsoft Docs
description: Azure PowerShell 스크립트 샘플 - 3노드 테스트 Service Fabric 클러스터를 만듭니다.
services: service-fabric
documentationcenter: ''
author: rwike77
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: 0f9c8bc5-3789-4eb3-8deb-ae6e2200795a
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 03/19/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 169f68d179c7f895078fe649d0e2a69e58d148cb
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="create-a-three-node-test-service-fabric-cluster"></a>3노드 테스트 Service Fabric 클러스터 만들기

이 샘플 스크립트는 X.509 인증서를 사용하여 보호되는 3노드 테스트 Service Fabric 클러스터를 만듭니다. 업그레이드를 안전하게 수행하고 개별 노드 오류에도 대응 가능하므로(동시에 발생하지만 않는다면) 개발/테스트에 대해 세 개 노드 클러스터 구성이 지원됩니다. 프로덕션 클러스터에는 동시 실패에 탄력적으로 대응하기 위해 5개 이상의 노드가 필요합니다.  

이 명령은 자체 서명된 인증서를 만들고, 동일한 리소스 그룹에서 클러스터로 만들어지는 새로운 키 자격 증명 모음에 업로드합니다. 인증서는 로컬 디렉터리에도 복사됩니다.  *-OS* 매개 변수를 설정하여 클러스터 노드에서 실행되는 Windows 또는 Linux 버전을 선택합니다.  필요에 따라 매개 변수를 사용자 지정합니다.

필요한 경우 [Azure PowerShell 가이드](/powershell/azure/overview)에 있는 지침을 사용하여 Azure PowerShell을 설치한 다음, `Connect-AzureRmAccount`를 실행하여 Azure에 연결합니다. 

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/service-fabric/create-test-cluster/create-test-cluster.ps1 "Create a test Service Fabric cluster")]

## <a name="clean-up-deployment"></a>배포 정리 

스크립트 샘플을 실행한 후에는 다음 명령을 사용하여 리소스 그룹, 클러스터 및 모든 관련된 리소스를 제거할 수 있습니다.

```powershell
$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) | 새 Service Fabric 클러스터를 만듭니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

Azure Service Fabric에 대한 추가 Azure PowerShell 샘플은 [Azure PowerShell 샘플](../service-fabric-powershell-samples.md)에서 확인할 수 있습니다.
