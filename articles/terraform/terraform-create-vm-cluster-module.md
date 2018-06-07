---
title: Terraform 모듈을 사용하여 Azure에서 VM 클러스터 만들기
description: Terraform 모듈을 사용하여 Azure에서 Windows 가상 머신 클러스터를 만드는 방법 알아보기
keywords: terraform, devops, 가상 컴퓨터, 네트워크, 모듈
author: rloutlaw
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure
ms.date: 10/19/2017
ms.custom: devops
ms.author: routlaw
ms.openlocfilehash: e33aef252413eeb243b03543f171d5f1e2385b48
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="create-a-vm-cluster-with-terraform-using-the-module-registry"></a>모듈 레지스트리를 사용하여 Terraform으로 VM 클러스터 만들기

이 문서에서는 Terraform [Azure 계산 모듈](https://registry.terraform.io/modules/Azure/compute/azurerm/1.0.2)로 소형 VM 클러스터를 만드는 과정을 안내합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다. 

> [!div class="checklist"]
> * Azure로 인증 설정
> * Terraform 템플릿 만들기
> * plan으로 변경 내용 시각화
> * 구성을 적용하여 VM 클러스터 만들기

Terraform에 대한 자세한 내용은 [Terraform 설명서](https://www.terraform.io/docs/index.html)를 참조하세요.

## <a name="set-up-authentication-with-azure"></a>Azure로 인증 설정

> [!TIP]
> [Terraform 환경 변수를 사용](/azure/virtual-machines/linux/terraform-install-configure#set-environment-variables)하거나 [Azure Cloud Shell](/azure/cloud-shell/overview)에서 자습서를 실행하는 경우 이 단계를 건너뜁니다.

 Azure 서비스 주체를 만들려면 [Terraform을 설치하고 Azure에 대한 액세스 구성](/azure/virtual-machines/linux/terraform-install-configure)을 검토하세요. 이 서비스 주체를 사용하여 빈 디렉터리에 있는 새 파일 `azureProviderAndCreds.tf`를 다음 코드로 채웁니다.

```tf
variable subscription_id {}
variable tenant_id {}
variable client_id {}
variable client_secret {}

provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

## <a name="create-the-template"></a>템플릿 만들기

다음 코드로 `main.tf`라는 새 Terraform 템플릿을 만듭니다.

```tf
module mycompute {
    source = "Azure/compute/azurerm"
    resource_group_name = "myResourceGroup"
    location = "East US 2"
    admin_password = "ComplxP@assw0rd!"
    vm_os_simple = "WindowsServer"
    remote_port = "3389"
    nb_instances = 2
    public_ip_dns = ["unique_dns_name"]
    vnet_subnet_id = "${module.network.vnet_subnets[0]}"
}

module "network" {
    source = "Azure/network/azurerm"
    location = "East US 2"
    resource_group_name = "myResourceGroup"
}

output "vm_public_name" {
    value = "${module.mycompute.public_ip_dns_name}"
}

output "vm_public_ip" {
    value = "${module.mycompute.public_ip_address}"
}

output "vm_private_ips" {
    value = "${module.mycompute.network_interface_private_ip}"
}
```

구성 디렉터리에서 `terraform init`을 실행합니다. 0.10.6이상의 Terraform 버전을 사용하면 다음과 같은 출력이 표시됩니다.

![Terraform Init](media/terraformInitWithModules.png)

## <a name="visualize-the-changes-with-plan"></a>plan으로 변경 내용 시각화

`terraform plan`을 실행하여 템플릿으로 만든 가상 머신 인프라를 미리 봅니다.

![Terraform Plan](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)


## <a name="create-the-virtual-machines-with-apply"></a>apply로 가상 머신 만들기

`terraform apply`를 실행하여 Azure에서 VM을 프로비전합니다.

![Terraform Apply](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>다음 단계

- [Azure Terraform 모듈](https://registry.terraform.io/modules/Azure) 목록 찾아보기
- [Terraform으로 가상 머신 확장 집합](terraform-create-vm-scaleset-network-disks-hcl.md) 만들기
