---
title: Azure CLI 스크립트 샘플 - 네트워크 가상 어플라이언스를 통한 트래픽 라우팅 | Microsoft Docs
description: Azure CLI 스크립트 샘플 - 방화벽 네트워크 가상 어플라이언스를 통해 트래픽을 라우팅합니다.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: d0d410fd59574fe2884ae941ef3fd64600930f31
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="route-traffic-through-a-network-virtual-appliance-script-sample"></a>네트워크 가상 어플라이언스를 통한 트래픽 라우팅 스크립트 샘플

이 스크립트 샘플은 프런트 엔드 및 백 엔드 서브넷이 있는 가상 네트워크를 만듭니다. 또한 두 서브넷 간에 트래픽을 라우팅할 수 있게 하는 IP 전달을 사용하여 VM을 만듭니다. 스크립트를 실행한 후에 방화벽 응용 프로그램과 같은 네트워크 소프트웨어를 VM에 배포할 수 있습니다.

Azure [Cloud Shell](https://shell.azure.com/bash) 또는 로컬 Azure CLI 설치에서 스크립트를 실행할 수 있습니다. CLI를 로컬로 사용하는 경우 이 스크립트는 2.0.28 이상 버전을 실행해야 합니다. 설치되어 있는 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요. 또한 CLI를 로컬로 실행하는 경우 `az login`를 실행하여 Azure와 연결해야 합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.sh "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>배포 정리 

다음 명령을 실행하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹, 가상 네트워크 및 네트워크 보안 그룹을 만듭니다. 다음 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Azure 가상 네트워크 및 프런트 엔드 서브넷을 만듭니다. |
| [az network subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | 백 엔드 및 DMZ 서브넷을 만듭니다. |
| [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) | 인터넷에서 VM에 액세스하기 위한 공용 IP 주소를 만듭니다. |
| [az network nic create](/cli/azure/network/nic#az_network_nic_create) | 가상 네트워크 인터페이스를 만들고 이 인터페이스를 위해 IP 전달을 사용하도록 설정합니다. |
| [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) | NSG(네트워크 보안 그룹)을 만듭니다. |
| [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) | VM에 대한 인바운드 HTTP 및 HTTPS 포트를 허용하는 NSG 규칙을 만듭니다. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)| NSG 및 경로 테이블을 서브넷에 연결합니다. |
| [az network route-table create](/cli/azure/network/route-table#az_network_route_table_create)| 모든 경로에 대한 경로 테이블을 만듭니다. |
| [az network route-table route create](/cli/azure/network/route-table/route#az_network_route_table_route_create)| VM을 통해 서브넷과 인터넷 간 트래픽을 라우팅하는 경로를 만듭니다. |
| [az vm create](/cli/azure/vm#az_vm_create) | 가상 머신을 만들고 NIC를 이 가상 머신에 연결합니다. 또한 이 명령은 사용할 가상 머신 이미지와 관리 자격 증명을 지정합니다. |
| [az group delete](/cli/azure/group#az_group_delete) | 리소스 그룹 및 이 그룹에 속한 모든 리소스를 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 가상 네트워크 CLI 스크립트 샘플은 [가상 네트워크 CLI 샘플](../cli-samples.md)에 나와 있습니다.
