---
title: "Azure에 OpenShift Container Platform 배포 | Microsoft Docs"
description: "Azure에서 OpenShift Container Platform을 배포합니다."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: f1ba6a3d3b9e576d513b55beac4e9365102433e9
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2018
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Azure에서 OpenShift Container Platform 배포

Azure에서 OpenShift Container Platform을 배포하는 몇 가지 방법 중 하나를 사용할 수 있습니다.

- 필요한 Azure 인프라 구성 요소를 수동으로 배포한 다음 OpenShift Container Platform [설명서](https://docs.openshift.com/container-platform/3.6/welcome/index.html)를 따르는 방법이 있습니다.
- OpenShift Container Platform 클러스터의 배포를 간소화하는 기존 [Resource Manager 템플릿](https://github.com/Microsoft/openshift-container-platform/)을 사용할 수도 있습니다.
- 다른 방법은 [Azure Marketplace 제품](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview)을 사용하는 것입니다.

모든 옵션에 Red Hat 구독이 필요합니다. 배포하는 동안 Red Hat Enterprise Linux 인스턴스는 Red Hat 구독에 등록되고 OpenShift Container Platform에 대한 자격이 포함된 풀 ID에 연결됩니다.
유효한 RHSM(Red Hat Subscription Manager) 사용자 이름, 암호 및 풀 ID가 있어야 합니다. 이 정보는 https://access.redhat.com에 로그인하여 확인할 수 있습니다.

## <a name="deploy-by-using-the-openshift-container-platform-resource-manager-template"></a>OpenShift Container Platform Resource Manager 템플릿을 사용하여 배포

Resource Manager 템플릿을 사용하여 배포하려면 매개 변수 파일을 사용하여 입력 매개 변수를 제공합니다. 입력 매개 변수를 사용하여 처리할 수 없는 배포 항목을 사용자 지정하려면 GitHub 리포지토리를 포크하고 적절한 항목을 변경합니다.

일반적인 사용자 지정 옵션에는 다음이 포함됩니다(이에 국한되지 않음).

- 가상 네트워크 CIDR(azuredeploy.json의 변수)
- 요새 VM 크기(azuredeploy.json의 변수)
- 명명 규칙(azuredeploy.json의 변수)
- OpenShift 클러스터 세부 정보, 호스트 파일을 통해 수정 됨(deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>매개 변수 파일 구성

`aadClientId` 매개 변수에 대해 이전에 만든 서비스 주체에서 `appId` 값을 사용합니다. 

다음 예제에서는 모든 필수 입력이 포함된 azuredeploy.parameters.json이라는 매개 변수 파일을 만듭니다.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_E2s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_E2s_v3"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "true"
        },
        "enableCockpit": {
            "value": "false"
        },
        "rhsmUsernamePasswordOrActivationKey": {
            "value": "usernamepassword"
        },
        "rhsmUsernameOrOrgId": {
            "value": "{RHSM Username}"
        },
        "rhsmPasswordOrActivationKey": {
            "value": "{RHSM Password}"
        },
        "rhsmPoolId": {
            "value": "{Pool ID}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

괄호 안의 항목은 사용자의 특정 정보로 대체합니다.

### <a name="deploy-by-using-azure-cli"></a>Azure CLI를 사용하여 배포

> [!NOTE] 
> 다음 명령은 Azure CLI 2.0.8 이상이 필요합니다. CLI 버전은 `az --version` 명령으로 확인할 수 있습니다. CLI 버전을 업데이트하려면 [Azure CLI 2.0 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti)를 참조하세요.

다음 예제에서는 myResourceGroup이라는 리소스 그룹에 myOpenShiftCluster라는 배포 이름을 사용하여 OpenShift 클러스터 및 모든 관련 리소스를 배포합니다. 템플릿은 GitHub 리포지토리에서 직접 참조되며, azuredeploy.parameters.json이라는 로컬 매개 변수 파일이 사용됩니다.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

배포를 완료하려면 배포되는 전체 노드 수에 따라 30분 이상이 걸립니다. 배포를 마치면 OpenShift 콘솔의 URL과 OpenShift 마스터의 DNS 이름이 터미널에 출력됩니다.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="deploy-by-using-the-openshift-container-platform-azure-marketplace-offer"></a>OpenShift Container Platform Azure Marketplace 제품을 사용하여 배포

OpenShift Container Platform을 Azure에 배포하는 가장 간단한 방법은 [Azure Marketplace 제품](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview)을 사용하는 것입니다.

가장 단순한 옵션이긴 하지만 사용자 지정 기능이 제한됩니다. 이 제품에는 세 가지 구성 옵션이 있습니다.

- **소형**: 1개의 마스터 노드, 1개의 인프라 노드, 2개의 응용 프로그램 노드 및 1개의 요새 노드로 비 HA(고가용성) 클러스터를 배포합니다. 모든 노드는 표준 DS2v2 VM 크기입니다. 이 클러스터에는 총 10개의 코어가 필요하며 소규모 테스트에 이상적입니다.
- **중형**: 3개의 마스터 노드, 2개의 인프라 노드, 4개의 응용 프로그램 노드 및 1개의 요새 노드가 있는 HA 클러스터를 배포합니다. 요새 노드를 제외한 모든 노드는 표준 DS3v2 VM 크기입니다. 요새 노드는 표준 DS2v2입니다. 이 클러스터에는 38개의 코어가 필요합니다.
- **대형**: 3개의 마스터 노드, 2개의 인프라 노드, 6개의 응용 프로그램 노드 및 1개의 요새 노드가 있는 HA 클러스터를 배포합니다. 마스터와 인프라 노드는 표준 DS3v2 VM 크기입니다. 응용 프로그램 노드는 표준 DS4v2 VM 크기이며 요새 노드는 표준 DS2v2입니다. 이 클러스터에는 70개의 코어가 필요합니다.

Azure 클라우드 솔루션 공급자 구성은 중형 및 대형 클러스터 크기의 경우 선택 사항입니다. 소형 클러스터 크기에는 Azure 클라우드 솔루션 공급자를 구성하는 옵션이 제공되지 않습니다.

## <a name="connect-to-the-openshift-cluster"></a>OpenShift 클러스터에 연결

배포를 마치면 브라우저에서 `OpenShift Console Uri`를 사용하여 OpenShift 콘솔에 연결합니다. 또는 다음 명령을 사용하여 OpenShift 마스터에 연결할 수 있습니다.

```bash
$ ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200
```

## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹, OpenShift 클러스터 및 모든 관련된 리소스가 더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az_group_delete) 명령을 사용하여 제거합니다.

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

- [배포 후 작업](./openshift-post-deployment.md)
- [Azure에서 OpenShift 배포 문제 해결](./openshift-troubleshooting.md)
- [OpenShift Container Platform 시작](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
