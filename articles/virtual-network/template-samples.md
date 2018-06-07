---
title: 가상 네트워크용 Azure Resource Manager 템플릿 샘플 | Microsoft Docs
description: Azure 가상 네트워크 배포에 사용할 수 있는 다양한 Azure Resource Manager 템플릿에 대해 알아봅니다.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/13/2018
ms.author: jdial
ms.openlocfilehash: af0affc549afd8b63fe0d566fac198de054554c1
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="azure-resource-manager-template-samples-for-virtual-network"></a>가상 네트워크용 Azure Resource Manager 템플릿 샘플

다음 표에는 Azure Resource Manager 템플릿 샘플 링크가 포함되어 있습니다. Azure [Portal](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json), Azure [CLI](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 Azure [PowerShell](../azure-resource-manager/resource-group-template-deploy.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 사용하여 템플릿을 배포할 수 있습니다. 템플릿을 직접 작성하는 방법을 알아보려면 [첫 번째 템플릿 만들기](../azure-resource-manager/resource-manager-create-first-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 및 [Azure Resource Manager 템플릿의 구조 및 구문 이해](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.


| | |
|----|----|
|[두 서브넷이 있는 가상 네트워크 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets)| 두 개의 서브넷이 있는 가상 네트워크를 만듭니다.|
|[네트워크 가상 어플라이언스를 통한 트래픽 라우팅](https://github.com/Azure/azure-quickstart-templates/tree/master/201-userdefined-routes-appliance)| 세 개의 서브넷이 있는 가상 네트워크를 만듭니다. 각 서브넷에 가상 머신을 배포합니다. 세 번째 서브넷의 가상 머신을 통해 한 서브넷에서 다른 서브넷으로 트래픽을 보내는 경로가 포함된 경로 테이블을 만듭니다. 경로 테이블을 서브넷 중 하나에 연결합니다.|
|[Azure Storage에 대한 가상 네트워크 서비스 엔드포인트 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vnet-2subnets-service-endpoints-storage-integration)|두 서브넷과 각 서브넷의 네트워크 인터페이스를 사용하여 새 가상 네트워크를 만듭니다. 서브넷 중 하나에 Azure Storage에 대한 서비스 엔드포인트를 사용하도록 설정하고 해당 서브넷에 새 저장소 계정의 보안을 설정합니다.|
|[두 가상 네트워크 연결](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vnet-to-vnet-peering)| 두 가상 네트워크를 만들고 이들 간에 가상 네트워크 피어링을 만듭니다.|
|[여러 IP 주소로 가상 머신 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-ipconfig)| 여러 IP 주소로 Windows 또는 Linux VM을 만듭니다.|
