---
title: Azure CLI 1.0을 사용하여 Linux VM에 대한 Key Vault 설정 | Microsoft Docs
description: Azure CLI 1.0을 사용하여 Azure Resource Manager에서 사용할 Key Vault를 설정하는 방법
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: bccdd5ab-5ccf-4760-9039-92c6eafb15bd
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: singhkay
ms.openlocfilehash: a9225429e878415334b0c8a66777902395606d63
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager-with-the-azure-cli-10"></a>Azure CLI 1.0을 사용하여 Azure Resource Manager에서 가상 머신에 대한 Key Vault 설정
Azure Resource Manager 스택에서 암호/인증서는 Key Vault의 리소스 공급자가 제공하는 리소스로 모델링됩니다. Azure Key Vault에 대한 자세한 내용을 보려면 [Azure Key Vault란?](../../key-vault/key-vault-whatis.md) 주요 자격 증명 모음을 Azure Resource Manager 가상 머신에서 사용하려면 주요 자격 증명에 대한 *EnabledForDeployment* 속성을 true로 설정해야 합니다. 다양한 클라이언트에서 이 작업을 수행할 수 있습니다. 이 문서에서는 Azure Virtual Machines에서 사용할 Key Vault를 설정하는 방법을 보여 줍니다.

## <a name="cli-versions-to-complete-the-task"></a>태스크를 완료하기 위한 CLI 버전
다음 CLI 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다.

- [Azure CLI 1.0](#quick-commands) - 클래식 및 리소스 관리 배포 모델용 CLI(이 문서)
- [Azure CLI 2.0](../windows/key-vault-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 리소스 관리 배포 모델용 차세대 CLI

## <a name="use-cli-10-to-set-up-key-vault"></a>CLI 1.0을 사용하여 Key Vault 설정
CLI(명령줄 인터페이스)를 사용하여 주요 자격 증명 모음을 만들려면 [CLI를 사용하여 주요 자격 증명 모음 관리](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault)를 참조하세요.

CLI 1.0의 경우 먼저 Key Vault를 만든 다음 배포 정책을 할당해야 합니다. 다음 명령을 사용하여 정책을 할당할 수 있습니다.

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>템플릿을 사용하여 주요 자격 증명 모음 설정
템플릿을 사용하는 경우 `enabledForDeployment` 속성을 주요 자격 증명 모음 리소스에 대한 `true`로 설정해야 합니다.

    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "ContosoKeyVault",
      "apiVersion": "2015-06-01",
      "location": "<location-of-key-vault>",
      "properties": {
        "enabledForDeployment": "true",
        ....
        ....
      }
    }

템플릿을 사용하여 주요 자격 증명 모음을 만들 때 구성할 수 있는 다른 옵션에 대해서는 [주요 자격 증명 모음 만들기](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)를 참조하세요.
