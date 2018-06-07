---
title: "Azure에서 VM 및 기타 인프라를 프로비전하기 위해 Terraform 설치 및 구성 | Microsoft Docs"
description: "Terraform을 설치하고 구성하여 Azure 리소스를 만드는 방법을 알아봅니다."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jtalkar
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/23/2017
ms.author: echuvyrov
ms.openlocfilehash: dada9c70eef2adb2704e276a5401509581e37538
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>Azure에 VM 및 기타 인프라를 프로비전하기 위해 Terraform 설치 및 구성
 
Terraform은 [간단한 템플릿 언어](https://www.terraform.io/docs/configuration/syntax.html)를 사용하여 클라우드 인프라를 정의, 미리 보기 및 배포하는 쉬운 방법을 제공합니다. 이 문서에서는 Terraform을 사용하여 Azure의 리소스를 프로비전하는 데 필요한 단계를 설명합니다. 

> [!TIP]
Azure에서 Terraform을 사용하는 방법에 대한 자세한 내용은 [Terraform 허브](/azure/terraform)를 참조하세요. Terraform은 기본적으로 [Cloud Shell](/azure/terraform/terraform-cloud-shell)에 설치됩니다. Cloud Shell을 사용하여 이 문서의 설치/설정 부분을 건너뛸 수 있습니다.

## <a name="install-terraform"></a>Terraform 설치

Terraform를 설치하려면 운영 체제에 적합한 패키지를 별도의 설치 디렉터리에 [다운로드](https://www.terraform.io/downloads.html)합니다. 다운로드에는 전역 경로를 정의해야 하는 단일 실행 파일이 포함됩니다. Linux 및 Mac에서 경로를 설정하는 방법에 대한 지침은 [이 웹 페이지](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux)로 이동합니다. Windows에서 경로를 설정하는 방법에 대한 지침은 [이 웹 페이지](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows)로 이동합니다. 

`terraform` 명령을 사용하여 경로 구성을 확인합니다. 출력으로 사용할 수 있는 Terraform 옵션 목록이 표시되어야 합니다.

```bash
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Azure에 대한 Terraform 액세스 설정

Terraform에서 Azure로 리소스를 프로비전하도록 [Azure AD 서비스 사용자](/cli/azure/create-an-azure-service-principal-azure-cli)를 구성합니다. 서비스 사용자는 자격 증명을 사용하여 Terraform 스크립트에 Azure 구독에서 리소스를 프로비전하는 권한을 부여합니다.

여러 가지 방법으로 Azure AD 응용 프로그램 및 Azure AD 서비스 주체를 만들 수 있습니다. 현재 가장 쉽고 빠른 방법은 Azure CLI 2.0을 사용하는 것이며 [Windows, Linux 또는 Mac에 다운로드하여 설치](/cli/azure/install-azure-cli)할 수 있습니다.

다음 명령을 실행하여 Azure 구독을 관리하기 위해 로그인합니다.

   `az login`

여러 Azure 구독이 있는 경우 `az login` 명령을 사용하면 세부 정보가 반환됩니다. 사용하려는 구독에서 반환된 `id` 필드 값을 보유하도록 `SUBSCRIPTION_ID` 환경 변수를 설정합니다. 

이 세션에 사용할 구독을 설정합니다.

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

계정을 쿼리하여 구독 ID 및 테넌트 ID 값을 가져옵니다.

```azurecli-interactive
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

다음으로 Terraform에 대한 별도의 자격 증명을 만듭니다.

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

appId, 암호, sp_name, 및 테넌트가 반환됩니다. appId 및 암호를 기록해 둡니다.

자격 증명을 테스트하려면 새 셸을 열고 sp_name, 암호 및 테넌트에 대해 반환된 값을 사용하여 다음 명령을 실행합니다.

```azurecli-interactive
az login --service-principal -u SP_NAME -p PASSWORD --tenant TENANT
az vm list-sizes --location westus
```

## <a name="configure-terraform-environment-variables"></a>Terraform 환경 변수 구성

Azure 리소스를 만들 때 서비스 사용자에서 테넌트 ID, 구독 ID, 클라이언트 ID 및 클라이언트 비밀을 사용하도록 Terraform을 구성합니다. Azure 공용이 아닌 Azure 클라우드에서 작업하는 경우 환경을 설정할 수도 있습니다. [Azure Terraform 모듈](https://registry.terraform.io/modules/Azure)에서 자동으로 사용되는 다음 환경 변수를 설정합니다.

- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID
- ARM_ENVIRONMENT

이 샘플 셸 스크립트를 사용하여 해당 변수를 설정할 수 있습니다.

```bash
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id

# Not needed for public, required for usgovernment, german, china
export ARM_ENVIRONMENT=public
```

## <a name="run-a-sample-script"></a>샘플 스크립트 실행

빈 디렉터리에 `test.tf` 파일을 만들고 다음 스크립트에 붙여넣습니다. 

```tf
provider "azurerm" {
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

파일을 저장한 다음 `terraform init`를 실행합니다. 이 명령은 Azure 리소스 그룹을 만드는 데 필요한 Azure 모듈을 다운로드합니다. 다음 출력이 표시됩니다.

```
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

`terraform plan`을 포함하는 스크립트를 미리 본 다음 `terraform apply`를 사용하여 `testResouceGroup` 리소스 그룹을 만듭니다.

```
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.rg
      id:       <computed>
      location: "westus"
      name:     "testResourceGroup"
      tags.%:   <computed>

azurerm_resource_group.rg: Creating...
  location: "" => "westus"
  name:     "" => "testResourceGroup"
  tags.%:   "" => "<computed>"
azurerm_resource_group.rg: Creation complete after 1s
```

## <a name="next-steps"></a>다음 단계

Terraform을 설치했고 Azure 자격 증명을 구성했으므로 Azure 구독에 인프라를 배포하기 시작할 수 있습니다. 그런 다음 빈 Azure 리소스 그룹을 만들어 설치를 테스트했습니다.

> [!div class="nextstepaction"]
> [Terraform으로 Azure VM 만들기](terraform-create-complete-vm.md)

