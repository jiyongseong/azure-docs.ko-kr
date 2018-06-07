---
title: Azure CLI 2.0 샘플 - 앱 설치 | Microsoft Docs
description: Azure CLI 2.0 샘플
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 22c2f8d811da3dfc565ff32cb602e78b1097ffab
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="install-applications-into-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Azure CLI 2.0을 사용하여 가상 머신 확장 집합에 응용 프로그램 설치
이 스크립트는 Ubuntu를 실행하는 가상 머신 확장 집합을 만들고, 사용자 지정 스크립트 확장을 사용하여 기본 웹 응용 프로그램을 설치합니다. 스크립트가 실행되면 웹 브라우저를 통해 웹 응용 프로그램에 액세스할 수 있습니다.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/install-apps/install-apps.sh "Install apps into a scale set")]

## <a name="clean-up-deployment"></a>배포 정리
다음 명령을 실행하여 리소스 그룹, 확장 집합 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명
이 스크립트는 다음 명령을 사용하여 리소스 그룹, 가상 머신 확장 집합 및 관련된 모든 리소스를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az group create](/cli/azure/ad/group#az_ad_group_create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az vmss create](/cli/azure/vmss#az_vmss_create) | 가상 머신 확장 집합을 만들고 가상 네트워크, 서브넷 및 네트워크 보안 그룹에 연결합니다. 부하 분산 장치도 여러 VM 인스턴스에 트래픽을 분산하기 위해 만듭니다. 또한 이 명령은 사용할 VM 이미지와 관리 자격 증명도 지정합니다.  |
| [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set) | Azure 사용자 지정 스크립트 확장을 설치하여 각 VM 인스턴스에서 데이터 디스크를 준비하는 스크립트를 실행합니다. |
| [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) | 80 TCP 포트의 트래픽을 확장 집합의 VM 인스턴스에 분산하는 부하 분산 장치 규칙을 만듭니다. |
| [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) | 부하 분산 장치에서 사용하는 할당된 공용 IP 주소에 대한 정보를 가져옵니다. |
| [az group delete](/cli/azure/ad/group#delete) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계
Azure CLI 2.0에 대한 자세한 내용은 [Azure CLI 2.0 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 가상 머신 확장 집합 Azure CLI 2.0 스크립트 샘플은 [Azure 가상 머신 확장 집합 설명서](../cli-samples.md)에서 찾을 수 있습니다.
