---
title: "Terraform을 사용하여 Packer 사용자 지정 이미지에서 Azure 가상 컴퓨터 확장 집합 만들기"
description: "Terraform을 사용하여 Packer에서 생성한 사용자 지정 이미지에서 Azure 가상 컴퓨터 확장 집합을 구성하고 버전을 지정합니다(가상 네트워크 및 관리되는 연결 디스크로 완성)."
keywords: "terraform, devops, 확장 집합, 가상 컴퓨터, 네트워크, 저장소, 모듈, 사용자 지정 이미지, packer"
author: VaijanathB
ms.author: tarcher
ms.date: 10/29/2017
ms.topic: article
ms.openlocfilehash: 284eae93de36986e41ba80f98f86495d8f34f57b
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2017
---
# <a name="use-terraform-to-create-an-azure-virtual-machine-scale-set-from-a-packer-custom-image"></a>Terraform을 사용하여 Packer 사용자 지정 이미지에서 Azure 가상 컴퓨터 확장 집합 만들기

이 자습서에서는 HCL([HashiCorp Configuration Language](https://www.terraform.io/docs/configuration/syntax.html))을 사용하는 Managed Disks에서 [Packer](https://www.packer.io/intro/index.html)를 사용하여 생성된 사용자 지정 이미지로 만든 [Azure 가상 컴퓨터 확장 집합](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview)을 만들고 배포하는 데 [Terraform](https://www.terraform.io/)을 사용합니다.  

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Terraform 배포 설정
> * Terraform 배포용 변수 및 출력 사용 
> * 네트워크 인프라 만들기 및 배포
> * Packer를 사용하여 사용자 지정 가상 컴퓨터 이미지 만들기
> * 사용자 지정 이미지를 사용하여 가상 컴퓨터 확장 집합 만들기 및 배포
> * jumpbox 만들기 및 배포 

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="before-you-begin"></a>시작하기 전에
> * [Terraform을 설치하고 Azure에 대한 액세스 구성](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure)
> * [SSH 키 쌍 만들기](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)(아직 없는 경우)
> * 로컬 컴퓨터에 Packer를 아직 설치하지 않은 경우 [Packer 설치](https://www.packer.io/docs/install/index.html)


## <a name="create-the-file-structure"></a>파일 구조 만들기

빈 디렉터리에 다음과 같은 이름으로 3개의 새 파일을 만듭니다.

- ```variables.tf``` 이 파일은 템플릿에 사용되는 변수 값을 포함합니다.
- ```output.tf``` 이 파일은 배포 후에 표시될 설정을 설명합니다.
- ```vmss.tf``` 이 파일은 배포 중인 인프라의 코드를 포함합니다.

##  <a name="create-the-variables"></a>변수 만들기 

이 단계에서는 Terraform에서 만든 리소스를 사용자 지정하는 변수를 정의합니다.

`variables.tf` 파일을 편집하고 다음 코드를 복사한 다음 변경 내용을 저장합니다.

```tf 
variable "location" {
  description = "The location where resources are created"
  default     = "East US"
}

variable "resource_group_name" {
  description = "The name of the resource group in which the resources are created"
  default     = ""
}

```

> [!NOTE]
> resource_group_name 변수의 기본값을 설정하지 않았으므로 고유한 값을 정의합니다.

파일을 저장합니다.

Terraform 템플릿을 배포하는 경우 응용 프로그램에 액세스하는 데 사용되는 정규화된 도메인 이름을 가져오려고 합니다. Terraform의 ```output``` 리소스 형식을 사용하고 리소스의 ```fqdn``` 속성을 가져옵니다. 

`output.tf` 파일을 편집하고 다음 코드를 복사하여 가상 컴퓨터의 정규화된 도메인 이름을 노출합니다. 

```hcl 
output "vmss_public_ip" {
    value = "${azurerm_public_ip.vmss.fqdn}"
}
```

## <a name="define-the-network-infrastructure-in-a-template"></a>템플릿에 네트워크 인프라 정의 

이 단계에서는 새 Azure 리소스 그룹에 다음과 같은 네트워크 인프라를 만듭니다. 
  - 주소 공간이 10.0.0.0/16인 VNET 1개 
  - 주소 공간이 10.0.2.0/24인 서브넷 1개
  - 2개의 공용 IP 주소. 하나는 가상 컴퓨터 확장 집합 부하 분산 장치에서 사용되고 다른 하나는 SSH jumpbox에 연결하는 데 사용됩니다.

모든 리소스를 만드는 리소스 그룹이 필요합니다. 

```vmss.tf``` 파일에서 다음 코드를 편집하고 복사합니다. 

```tf 

resource "azurerm_resource_group" "vmss" {
  name     = "${var.resource_group_name}"
  location = "${var.location}"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_network" "vmss" {
  name                = "vmss-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_subnet" "vmss" {
  name                 = "vmss-subnet"
  resource_group_name  = "${azurerm_resource_group.vmss.name}"
  virtual_network_name = "${azurerm_virtual_network.vmss.name}"
  address_prefix       = "10.0.2.0/24"
}

resource "azurerm_public_ip" "vmss" {
  name                         = "vmss-public-ip"
  location                     = "${var.location}"
  resource_group_name          = "${azurerm_resource_group.vmss.name}"
  public_ip_address_allocation = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}"

  tags {
    environment = "codelab"
  }
}

``` 

> [!NOTE]
> 나중에 쉽게 식별할 수 있도록 Azure에 배포되는 리소스에 태그를 지정하는 것이 좋습니다.

## <a name="create-the-network-infrastructure"></a>네트워크 인프라 만들기

`.tf` 파일을 만든 디렉터리에서 다음 명령을 실행하여 Terraform 환경을 초기화합니다.

```bash
terraform init 
```
 
공급자 플러그 인은 Terraform 레지스트리에서 명령을 실행하는 디렉터리의 ```.terraform``` 폴더로 다운로드합니다.

다음 명령을 실행하여 Azure에 인프라를 배포합니다.

```bash
terraform apply
```

공용 IP 주소의 정규화된 도메인 이름이 구성에 해당하는지 확인합니다.

![공용 IP 주소에 대한 가상 컴퓨터 확장 집합 Terraform 정규화된 도메인 이름](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-fqdn.png)

리소스 그룹에는 다음 리소스가 포함됩니다.

![가상 컴퓨터 확장 집합 Terraform 네트워크 리소스](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-rg.png)


## <a name="create-an-azure-image-using-packer"></a>Packer를 사용하여 Azure 이미지 만들기
[Packer를 사용하여 Azure에서 Linux 가상 컴퓨터 이미지를 만드는 방법](https://docs.microsoft.com/azure/virtual-machines/linux/build-image-with-packer) 자습서에 설명된 단계를 사용하여 사용자 지정 Linux 이미지를 만듭니다.
 
자습서를 수행하여 NGINX를 설치한 프로비전 해제된 Ubuntu 이미지를 만듭니다.

![Packer 이미지를 만들면 이미지가 생성됩니다.](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/packerimagecreated.png)

> [!NOTE]
> 이 자습서의 목표대로 Packer 이미지에서 명령을 실행하여 nginx를 설치합니다. 만드는 동안 고유한 스크립트를 실행할 수도 있습니다.

## <a name="edit-the-infrastructure-to-add-the-virtual-machine-scale-set"></a>인프라를 편집하여 가상 컴퓨터 확장 집합 추가

이 단계에서는 이전에 배포한 네트워크에서 다음 리소스를 만듭니다.
- Azure Load Balancer를 응용 프로그램에 제공 및 4단계에서 배포된 공용 IP 주소에 연결
- 하나의 Azure Load Balancer 및 응용 프로그램을 제공하여 이전에 구성된 공용 IP 주소에 연결하는 규칙
- Azure 백 엔드 주소 풀 및 이를 부하 분산 장치에 할당 
- 응용 프로그램에서 사용되고 부하 분산 장치에 구성된 상태 프로브 포트 
- 이전에 배포한 vnet에서 실행 중인, 부하 분산 장치 뒤에 있는 가상 컴퓨터 확장 집합
- 사용자 지정 이미지에서 설치된 가상 컴퓨터 확장 노드의 [Nginx](http://nginx.org/)


`vmss.tf` 파일의 끝에 다음 코드를 추가합니다.

```tf


resource "azurerm_lb" "vmss" {
  name                = "vmss-lb"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  frontend_ip_configuration {
    name                 = "PublicIPAddress"
    public_ip_address_id = "${azurerm_public_ip.vmss.id}"
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_lb_backend_address_pool" "bpepool" {
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id     = "${azurerm_lb.vmss.id}"
  name                = "BackEndAddressPool"
}

resource "azurerm_lb_probe" "vmss" {
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id     = "${azurerm_lb.vmss.id}"
  name                = "ssh-running-probe"
  port                = "${var.application_port}"
}

resource "azurerm_lb_rule" "lbnatrule" {
  resource_group_name            = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id                = "${azurerm_lb.vmss.id}"
  name                           = "http"
  protocol                       = "Tcp"
  frontend_port                  = "${var.application_port}"
  backend_port                   = "${var.application_port}"
  backend_address_pool_id        = "${azurerm_lb_backend_address_pool.bpepool.id}"
  frontend_ip_configuration_name = "PublicIPAddress"
  probe_id                       = "${azurerm_lb_probe.vmss.id}"
}

data "azurerm_resource_group" "image" {
  name = "myResourceGroup"
}

data "azurerm_image" "image" {
  name                = "myPackerImage"
  resource_group_name = "${data.azurerm_resource_group.image.name}"
}

resource "azurerm_virtual_machine_scale_set" "vmss" {
  name                = "vmscaleset"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  upgrade_policy_mode = "Manual"

  sku {
    name     = "Standard_DS1_v2"
    tier     = "Standard"
    capacity = 2
  }

  storage_profile_image_reference {
    id="${data.azurerm_image.image.id}"
  }

  storage_profile_os_disk {
    name              = ""
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  storage_profile_data_disk {
    lun          = 0
    caching        = "ReadWrite"
    create_option  = "Empty"
    disk_size_gb   = 10
  }

  os_profile {
    computer_name_prefix = "vmlab"
    admin_username       = "azureuser"
    admin_password       = "Passwword1234"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = "${file("~/.ssh/id_rsa.pub")}"
    }
  }

  network_profile {
    name    = "terraformnetworkprofile"
    primary = true

    ip_configuration {
      name                                   = "IPConfiguration"
      subnet_id                              = "${azurerm_subnet.vmss.id}"
      load_balancer_backend_address_pool_ids = ["${azurerm_lb_backend_address_pool.bpepool.id}"]
    }
  }
  
  tags {
    environment = "codelab"
  }
}

```

`variables.tf`에 다음 코드를 추가하여 배포를 사용자 지정합니다.

```tf 
variable "application_port" {
    description = "The port that you want to expose to the external load balancer"
    default     = 80
}

variable "admin_password" {
    description = "Default password for admin"
    default = "Passwwoord11223344"
}
``` 


## <a name="deploy-the-virtual-machine-scale-set-in-azure"></a>Azure에서 가상 컴퓨터 확장 집합 배포

다음 명령을 실행하여 가상 컴퓨터 확장 집합 배포를 시각화합니다.

```bash
terraform plan
```

명령 출력은 다음 이미지와 같습니다.

![Terraform 가상 컴퓨터 확장 집합 계획 추가](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-plan.png)

Azure에서 추가 리소스를 배포합니다. 

```bash
terraform apply 
```

리소스 그룹의 내용은 다음 이미지와 같습니다.

![Terraform 가상 컴퓨터 확장 집합 리소스 그룹](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-apply.png)

브라우저를 열고 명령에 의해 반환된 정규화된 도메인 이름에 연결합니다. 


## <a name="add-a-jumpbox-to-the-existing-network"></a>기존 네트워크에 jumpbox 추가 

이 선택적 단계를 사용하면 SSH에서 jumpbox를 사용하여 가상 컴퓨터 확장 집합의 인스턴스에 액세스할 수 있습니다.

기존 배포에 다음 리소스를 추가합니다.
- 가상 컴퓨터 확장 집합과 동일한 서브넷에 연결된 네트워크 인터페이스
- 이 네트워크 인터페이스와 가상 컴퓨터

`vmss.tf` 파일의 끝에 다음 코드를 추가합니다.

```hcl 
resource "azurerm_public_ip" "jumpbox" {
  name                         = "jumpbox-public-ip"
  location                     = "${var.location}"
  resource_group_name          = "${azurerm_resource_group.vmss.name}"
  public_ip_address_allocation = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}-ssh"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_network_interface" "jumpbox" {
  name                = "jumpbox-nic"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  ip_configuration {
    name                          = "IPConfiguration"
    subnet_id                     = "${azurerm_subnet.vmss.id}"
    private_ip_address_allocation = "dynamic"
    public_ip_address_id          = "${azurerm_public_ip.jumpbox.id}"
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_machine" "jumpbox" {
  name                  = "jumpbox"
  location              = "${var.location}"
  resource_group_name   = "${azurerm_resource_group.vmss.name}"
  network_interface_ids = ["${azurerm_network_interface.jumpbox.id}"]
  vm_size               = "Standard_DS1_v2"

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }

  storage_os_disk {
    name              = "jumpbox-osdisk"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  os_profile {
    computer_name  = "jumpbox"
    admin_username = "azureuser"
    admin_password = "Password1234!"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = "${file("~/.ssh/id_rsa.pub")}"
    }
  }

  tags {
    environment = "codelab"
  }
}
```

`outputs.tf`를 편집하고 다음 코드를 추가하여 배포가 완료될 때 jumpbox의 호스트 이름을 표시합니다.

```
output "jumpbox_public_ip" {
    value = "${azurerm_public_ip.jumpbox.fqdn}"
}
```

## <a name="deploy-the-jumpbox"></a>jumpbox 배포

jumpbox를 배포합니다.

```bash
terraform apply 
```

배포가 완료된 후 리소스 그룹의 콘텐츠는 다음 이미지와 같습니다.

![Terraform 가상 컴퓨터 확장 집합 리소스 그룹](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-create-vmss-step8.png)

> [!NOTE]
> 배포한 가상 컴퓨터 확장 집합 및 jumpbox에서 암호를 사용하여 로그인할 수 없습니다. VM에 액세스하려면 SSH를 사용하여 로그인합니다.

## <a name="clean-up-the-environment"></a>환경 정리

다음은 이 자습서에서 만든 리소스를 삭제하는 명령입니다.

```bash
terraform destroy
```

리소스를 삭제할 것인지 확인하는 메시지가 표시되면 `yes`를 입력합니다. 소멸 프로세스를 완료하는 데 몇 분 정도가 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Terraform을 사용하여 Azure에서 가상 컴퓨터 확장 집합 및 jumpbox를 배포했습니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Terraform 배포 초기화
> * Terraform 배포용 변수 및 출력 사용 
> * 네트워크 인프라 만들기 및 배포
> * Packer를 사용하여 사용자 지정 가상 컴퓨터 이미지 만들기
> * 사용자 지정 이미지를 사용하여 가상 컴퓨터 확장 집합 만들기 및 배포
> * jumpbox 만들기 및 배포 