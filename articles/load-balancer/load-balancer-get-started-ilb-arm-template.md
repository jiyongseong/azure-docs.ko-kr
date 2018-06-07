---
title: 내부 부하 분산 장치 만들기 - Azure 템플릿 | Microsoft Docs
description: 리소스 관리자에서 템플릿을 사용하여 내부 부하 분산 장치를 만드는 방법에 대해 알아봅니다.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
ms.assetid: 64150862-6ced-42de-85dc-89d323257d7c
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: f92430cad52edc60a0ab69caec61cc6f20515f61
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2018
---
# <a name="create-an-internal-load-balancer-using-a-template"></a>템플릿을 사용하여 내부 부하 분산 장치 만들기

> [!div class="op_single_selector"]
> * [Azure Portal](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [템플릿](../load-balancer/load-balancer-get-started-ilb-arm-template.md)


[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>클릭하여 배포하는 방식으로 템플릿 배포

공용 저장소에서 사용할 수 있는 샘플 템플릿은 위에 설명된 시나리오를 생성하는 데 사용된 기본값을 포함하는 매개 변수 파일을 사용합니다. 클릭하여 배포하는 방식으로 이 템플릿을 배포하려면 [이 링크](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer)에 따라 **Azure에 배포**를 클릭하고 필요한 경우 기본 매개 변수 값을 대체하고 포털의 지침을 따릅니다.

## <a name="deploy-the-template-by-using-powershell"></a>PowerShell을 사용하여 템플릿 배포

PowerShell을 사용하여 다운로드한 템플릿을 배포하려면 다음 단계를 수행합니다.

1. Azure PowerShell을 처음 사용하는 경우 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/overview) 을 참조하고 지침을 끝까지 따르면서 Azure에 로그인하고 구독을 선택합니다.
2. 매개 변수 파일을 로컬 디스크에 다운로드합니다.
3. 파일을 편집하고 저장합니다.
4. **New-AzureRmResourceGroupDeployment** cmdlet을 실행하고 템플릿을 사용하여 리소스 그룹을 만듭니다.

    ```azurecli
    New-AzureRmResourceGroupDeployment -Name TestRG -Location westus `
        -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json' `
        -TemplateParameterFile 'C:\temp\azuredeploy.parameters.json'
    ```

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Azure CLI를 사용하여 템플릿 배포

Azure CLI를 사용하여 템플릿을 배포하려면 아래 단계를 따르세요.

1. Azure CLI를 처음 사용하는 경우 [Azure CLI 설치 및 구성](../cli-install-nodejs.md) 을 참조하고 Azure 계정 및 구독을 선택하는 부분까지 관련 지침을 따릅니다.
2. 아래와 같이 **azure config mode** 명령을 실행하여 Resource Manager 모드로 전환합니다.

    ```azurecli
    azure config mode arm
    ```

    다음은 위의 명령에 대해 예상된 출력입니다.

        info:    New mode is arm

3. 매개 변수 파일을 열고 해당 내용을 선택한 후 컴퓨터의 파일에 저장합니다. 이 예에서는 매개 변수 파일을 *parameters.json*에 저장했습니다.
4. 위에서 다운로드하여 수정한 템플릿과 매개 변수 파일로 **azure group deployment create** 명령을 실행하여 새 내부 부하 분산 장치를 배포합니다. 출력 다음에 표시되는 목록은 사용되는 매개 변수를 설명합니다.

    ```azurecli
    azure group create --name TestRG --location westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json --parameters-file parameters.json
    ```

## <a name="next-steps"></a>다음 단계

[원본 IP 선호도를 사용하여 부하 분산 장치 배포 모드 구성](load-balancer-distribution-mode.md)

[부하 분산 장치에 대한 유휴 TCP 시간 제한 설정 구성](load-balancer-tcp-idle-timeout.md)

