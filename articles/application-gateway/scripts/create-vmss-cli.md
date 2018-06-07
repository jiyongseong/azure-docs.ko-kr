---
title: Azure CLI 스크립트 샘플 - 웹 트래픽 관리 | Microsoft Docs
description: Azure CLI 스크립트 샘플 - 응용 프로그램 게이트웨이 및 가상 머신 확장 집합을 사용하여 웹 트래픽을 관리합니다.
services: application-gateway
documentationcenter: networking
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: b398ff3b5f3e6fd2ea7d6e2544e7399646f4fa5c
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
---
# <a name="manage-web-traffic-using-the-azure-cli"></a>Azure CLI를 사용하여 웹 트래픽 관리

이 스크립트는 백 엔드 서버에 가상 머신 확장 집합을 사용하는 응용 프로그램 게이트웨이를 만듭니다. 그 후 웹 트래픽을 관리하도록 응용 프로그램 게이트웨이를 구성할 수 있습니다. 스크립트를 실행한 후에는 공용 IP 주소를 사용하여 응용 프로그램 게이트웨이를 테스트할 수 있습니다.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/application-gateway/create-vmss/create-vmss.sh "Create application gateway")]

## <a name="clean-up-deployment"></a>배포 정리 

다음 명령을 실행하여 리소스 그룹, 응용 프로그램 게이트웨이 및 모든 관련 리소스를 제거할 수 있습니다.

```azurecli-interactive 
az group delete --name myResourceGroupAG --yes
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 배포합니다. 테이블에 있는 각 항목은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#az_net) | 가상 네트워크를 만듭니다. |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | 가상 네트워크에 서브넷을 만듭니다. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/public-ip#az_network_public_ip_create) | 응용 프로그램 게이트웨이의 공용 IP 주소를 만듭니다. |
| [az network application-gateway create](https://docs.microsoft.com/cli/azure/application-gateway#az_application_gateway_create) | Application Gateway를 만듭니다. |
| [az vmss create](https://docs.microsoft.com/cli/azure/vmss#az_vmss_create) | 가상 머신 확장 집합을 만듭니다. |
| [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip#az_network_public_ip_show) | 응용 프로그램 게이트웨이의 공용 IP 주소를 가져옵니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 응용 프로그램 게이트웨이 CLI 스크립트 샘플은 [Azure Windows VM 설명서](../cli-samples.md)에서 찾을 수 있습니다.
