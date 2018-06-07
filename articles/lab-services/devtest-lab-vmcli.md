---
title: Azure CLI를 사용하여 DevTest Labs에서 가상 머신 만들기 및 관리 | Microsoft Docs
description: Azure DevTest Labs를 사용하여 Azure CLI 2.0에서 가상 머신을 만들고 관리하는 방법을 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 0f6713b9b8704e813ab1fd77ab1cf4e71e7f6670
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Azure CLI를 사용하여 DevTest Labs에서 가상 머신 만들기 및 관리
이 빠른 시작은 랩에서 개발 컴퓨터를 만들고, 시작하고, 연결하고, 업데이트하고, 정리하는 과정을 안내합니다. 

시작하기 전에

* 랩을 만들지 않은 경우 지침은 [여기](devtest-lab-create-lab.md)에서 찾을 수 있습니다.

* [CLI 2.0 설치](https://docs.microsoft.com/cli/azure/install-azure-cli). 시작하려면 az login을 실행하여 Azure와 연결합니다. 

## <a name="create-and-verify-the-virtual-machine"></a>가상 머신 만들기 및 확인 
ssh 인증을 사용하여 Marketplace 이미지에서 VM을 만듭니다.
```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```
> [!NOTE]
> **랩의 리소스 그룹** 이름을 --resource-group 매개 변수에 지정합니다.
>

수식을 사용하여 VM을 만들려는 경우 [az lab vm create](https://docs.microsoft.com/cli/azure/lab/vm#az_lab_vm_create)에서 --formula 매개 변수를 사용합니다.


VM을 사용할 수 있는지 확인합니다.
```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand 'properties($expand=ComputeVm,NetworkInterface)' --query '{status: computeVm.statuses[0].displayStatus, fqdn: fqdn, ipAddress: networkInterface.publicIpAddress}'
```
```json
{
  "fqdn": "lisalabvm.southcentralus.cloudapp.azure.com",
  "ipAddress": "13.85.228.112",
  "status": "Provisioning succeeded"
}
```

## <a name="start-and-connect-to-the-virtual-machine"></a>가상 머신 시작 및 연결
VM을 시작합니다.
```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```
> [!NOTE]
> **랩의 리소스 그룹** 이름을 --resource-group 매개 변수에 지정합니다.
>

VM: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) 또는 [원격 데스크톱](../virtual-machines/windows/connect-logon.md)에 연결합니다.
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>가상 머신 업데이트
VM에 아티팩트를 적용합니다.
```azurecli
az lab vm apply-artifacts --lab-name  sampleLabName --name sampleVMName  --resource-group sampleResourceGroup  --artifacts @/artifacts.json
```

```json
[
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-java",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-install-nodejs",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-apt-package",
    "parameters": [
      {
        "name": "packages",
        "value": "abcd"
      },
      {
        "name": "update",
        "value": "true"
      },
      {
        "name": "options",
        "value": ""
      }
    ]
  } 
]
```

랩에서 사용할 수 있는 아티팩트를 나열합니다.
```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand "properties(\$expand=artifacts)" --query 'artifacts[].{artifactId: artifactId, status: status}'
```
```json
{
  "artifactId": "/subscriptions/abcdeftgh1213123/resourceGroups/lisalab123RG822645/providers/Microsoft.DevTestLab/labs/lisalab123/artifactSources/public repo/artifacts/linux-install-nodejs",
  "status": "Succeeded"
}
```

## <a name="stop-and-delete-the-virtual-machine"></a>가상 머신 중지 및 삭제    
VM을 중지합니다.
```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

VM을 삭제합니다.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]