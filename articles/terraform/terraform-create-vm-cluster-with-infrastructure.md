---
title: "Terraform 및 HCL을 사용하여 VM 클러스터 만들기"
description: "Terraform 및 HCL(HashiCorp Configuration Language)을 사용하여 Azure에서 부하 분산 장치가 있는 Linux 가상 머신 클러스터 만들기"
keywords: "terraform, devops, 가상 컴퓨터, 네트워크, 모듈"
author: tomarcher
manager: routlaw
ms.service: virtual-machines-linux
ms.custom: devops
ms.topic: article
ms.date: 11/13/2017
ms.author: tarcher
ms.openlocfilehash: 2435d694e6a1671a234d02f90860e5cafe98c2df
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2017
---
# <a name="create-a-vm-cluster-with-terraform-and-hcl"></a>Terraform 및 HCL을 사용하여 VM 클러스터 만들기

이 자습서에서는 [HCL(Hashicorp Configuration Language)](https://www.terraform.io/docs/configuration/syntax.html)을 사용하여 작은 계산 클러스터를 만드는 방법을 보여줍니다. 구성은 부하 분산 장치, [가용성 집합](/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)의 Linux VM 두 개 및 필요한 모든 네트워킹 리소스를 만듭니다.

이 자습서에서는 다음을 수행했습니다.

> [!div class="checklist"]
> * 인증 설정
> * Terraform 구성 파일 만들기
> * Terraform 초기화
> * Terraform 실행 계획 만들기
> * Terraform 실행 계획 적용

## <a name="1-set-up-azure-authentication"></a>1. 인증 설정

> [!NOTE]
> [Terraform 환경 변수를 사용](/azure/virtual-machines/linux/terraform-install-configure#set-environment-variables)하거나 [Azure Cloud Shell](terraform-cloud-shell.md)에서 자습서를 실행하는 경우 이 섹션을 건너뜁니다.

이 섹션에서는 Azure 서비스 보안 주체와, 보안 주체의 자격 증명을 포함하는 Terraform 구성 파일을 두 개를 생성합니다.

1. Terraform에서 Azure로 리소스를 프로비전하도록 [Azure AD 서비스 사용자를 설정합니다](/azure/virtual-machines/linux/terraform-install-configure#set-up-terraform-access-to-azure). 보안 주체를 만드는 동안 구독 ID, 테넌트, 앱 ID, 암호의 값을 기록해 둡니다.

2. 명령 프롬프트를 엽니다.

3. Terraform 파일을 저장하는 빈 디렉터리를 만듭니다.

4. 변수 선언이 담긴 새 파일을 만듭니다. `.tf` 확장자를 사용하여 원하는 대로 이 파일의 이름을 지정하면 됩니다.

5. 변수 선언 파일에 다음 코드를 복사합니다.

  ```tf
  variable subscription_id {}
  variable tenant_id {}
  variable client_id {}
  variable client_secret {}
  
  provider "azurerm" {
      subscription_id = "${var.subscription_id}"
      tenant_id = "${var.tenant_id}"
      client_id = "${var.client_id}"
      client_secret = "${var.client_secret}"
  }
  ```

6. Terraform 변수에 대한 값이 담긴 새 파일을 만듭니다. Terraform이 이름이 `terraform.tfvars`인(또는 `*.auto.tfvars` 패턴을 따르는) 파일이 현재 디렉터리에 있으면 자동으로 로드하기 때문에 일반적으로 Terraform 변수 파일의 이름은 `terraform.tfvars`로 명명됩니다. 

7. 변수 파일에 다음 코드를 복사합니다. 자리 표시자를 다음과 같이 바꿉니다. `subscription_id`에는 `az account set` 실행 시 지정한 Azure 구독 ID를 사용합니다. `tenant_id`에는 `az ad sp create-for-rbac`에서 반환된 `tenant` 값을 사용합니다. `client_id`에는 `az ad sp create-for-rbac`에서 반환된 `appId` 값을 사용합니다. `client_secret`에는 `az ad sp create-for-rbac`에서 반환된 `password` 값을 사용합니다.

  ```tf
  subscription_id = "<azure-subscription-id>"
  tenant_id = "<tenant-returned-from-creating-a-service-principal>"
  client_id = "<appId-returned-from-creating-a-service-principal>"
  client_secret = "<password-returned-from-creating-a-service-principal>"
  ```

## <a name="2-create-a-terraform-configuration-file"></a>2. Terraform 구성 파일 만들기

이 섹션에서는 인프라에 대한 리소스 정의가 포함된 파일을 만듭니다.

1. 이름이 `main.tf`인 새 파일을 만듭니다. 

2. 다음 샘플 리소스 정의를 새로 만든 `main.tf` 파일에 복사합니다. 

  ```tf
  resource "azurerm_resource_group" "test" {
    name     = "acctestrg"
    location = "West US 2"
  }

  resource "azurerm_virtual_network" "test" {
    name                = "acctvn"
    address_space       = ["10.0.0.0/16"]
    location            = "${azurerm_resource_group.test.location}"
    resource_group_name = "${azurerm_resource_group.test.name}"
  }

  resource "azurerm_subnet" "test" {
    name                 = "acctsub"
    resource_group_name  = "${azurerm_resource_group.test.name}"
    virtual_network_name = "${azurerm_virtual_network.test.name}"
    address_prefix       = "10.0.2.0/24"
  }

  resource "azurerm_public_ip" "test" {
    name                         = "publicIPForLB"
    location                     = "${azurerm_resource_group.test.location}"
    resource_group_name          = "${azurerm_resource_group.test.name}"
    public_ip_address_allocation = "static"
  }

  resource "azurerm_lb" "test" {
    name                = "loadBalancer"
    location            = "${azurerm_resource_group.test.location}"
    resource_group_name = "${azurerm_resource_group.test.name}"

    frontend_ip_configuration {
      name                 = "publicIPAddress"
      public_ip_address_id = "${azurerm_public_ip.test.id}"
    }
  }

  resource "azurerm_lb_backend_address_pool" "test" {
    resource_group_name = "${azurerm_resource_group.test.name}"
    loadbalancer_id     = "${azurerm_lb.test.id}"
    name                = "BackEndAddressPool"
  }

  resource "azurerm_network_interface" "test" {
    count               = 2
    name                = "acctni${count.index}"
    location            = "${azurerm_resource_group.test.location}"
    resource_group_name = "${azurerm_resource_group.test.name}"

    ip_configuration {
      name                          = "testConfiguration"
      subnet_id                     = "${azurerm_subnet.test.id}"
      private_ip_address_allocation = "dynamic"
      load_balancer_backend_address_pools_ids = ["${azurerm_lb_backend_address_pool.test.id}"]
    }
  }

  resource "azurerm_managed_disk" "test" {
    count                = 2
    name                 = "datadisk_existing_${count.index}"
    location             = "${azurerm_resource_group.test.location}"
    resource_group_name  = "${azurerm_resource_group.test.name}"
    storage_account_type = "Standard_LRS"
    create_option        = "Empty"
    disk_size_gb         = "1023"
  }

  resource "azurerm_availability_set" "avset" {
    name                         = "avset"
    location                     = "${azurerm_resource_group.test.location}"
    resource_group_name          = "${azurerm_resource_group.test.name}"
    platform_fault_domain_count  = 2
    platform_update_domain_count = 2
    managed                      = true
  }

  resource "azurerm_virtual_machine" "test" {
    count                 = 2
    name                  = "acctvm${count.index}"
    location              = "${azurerm_resource_group.test.location}"
    availability_set_id   = "${azurerm_availability_set.avset.id}"
    resource_group_name   = "${azurerm_resource_group.test.name}"
    network_interface_ids = ["${element(azurerm_network_interface.test.*.id, count.index)}"]
    vm_size               = "Standard_DS1_v2"

    # Uncomment this line to delete the OS disk automatically when deleting the VM
    # delete_os_disk_on_termination = true

    # Uncomment this line to delete the data disks automatically when deleting the VM
    # delete_data_disks_on_termination = true

    storage_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_os_disk {
      name              = "myosdisk${count.index}"
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    # Optional data disks
    storage_data_disk {
      name              = "datadisk_new_${count.index}"
      managed_disk_type = "Standard_LRS"
      create_option     = "Empty"
      lun               = 0
      disk_size_gb      = "1023"
    }

    storage_data_disk {
      name            = "${element(azurerm_managed_disk.test.*.name, count.index)}"
      managed_disk_id = "${element(azurerm_managed_disk.test.*.id, count.index)}"
      create_option   = "Attach"
      lun             = 1
      disk_size_gb    = "${element(azurerm_managed_disk.test.*.disk_size_gb, count.index)}"
    }

    os_profile {
      computer_name  = "hostname"
      admin_username = "testadmin"
      admin_password = "Password1234!"
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags {
      environment = "staging"
    }
  }
  ```

## <a name="3-initialize-terraform"></a>3. Terraform 초기화 

[terraform init 명령](https://www.terraform.io/docs/commands/init.html)은 이전 섹션에서 만든 Terraform 구성 파일이 담긴 디렉터리를 초기화하는 데 사용됩니다. 새 Terraform 구성 파일을 작성한 후에는 항상 `terraform init` 명령을 실행해야 합니다. 

> [!TIP]
> `terraform init` 명령은 멱등으로, 동일한 결과 생성하는 동안 반복적으로 호출될 수 있습니다. 따라서 공동 작업 환경에 있으며 구성 파일의 변경 가능성이 있는 경우 계획을 실행하거나 적용하기 전에 항상 `terraform init` 명령을 호출하는 것이 좋습니다.

Terraform을 초기화하려면 다음 명령을 실행합니다.

  ```cmd
  terraform init
  ```

  ![Terraform 초기화](media/terraform-create-vm-cluster-with-infrastructure/terraform-init.png)

## <a name="4-create-a-terraform-execution-plan"></a>4. Terraform 실행 계획 만들기

[Terraform Plan 명령](https://www.terraform.io/docs/commands/plan.html)은 실행 계획을 만드는 데 사용됩니다. 실행 계획을 만들기 위해 Terraform이 현재 디렉터리에 있는 모든 `.tf` 파일을 집계합니다. 

실행 계획을 만든 시점과 적용하는 시점 사이에 구성이 변경될 수 있는 공동 작업 환경에 있는 경우 [terraform plan 명령의 -out 매개 변수](https://www.terraform.io/docs/commands/plan.html#out-path)를 사용하여 실행 계획을 파일에 저장해야 합니다. 그렇지 않고 1인 작업 환경이라면 `-out` 매개 변수를 생략할 수 있습니다.

Terraform 변수 파일의 이름이 `terraform.tfvars`가 아니고 `*.auto.tfvars` 패턴을 따르지 않는 경우, `terraform plan` 명령을 실행할 때 [terraform plan 명령의 -var-file 매개 변수](https://www.terraform.io/docs/commands/plan.html#var-file-foo)를 사용하여 파일 이름을 지정해야 합니다.

`terraform plan` 명령을 처리할 때 Terraform은 새로 고침을 수행한 다음 구성 파일에 지정된 원하는 상태를 달성하는 데 필요한 작업을 결정합니다.

실행 계획을 저장할 필요가 없으면 다음 명령을 실행합니다.

  ```cmd
  terraform plan
  ```

실행 계획을 저장해야 할 경우 다음 명령을 실행합니다( &lt;path> 자리 표시자를 원하는 출력 경로로 바꾸기).

  ```cmd
  terraform plan -out=<path>
  ```

![Terraform 실행 계획 만들기](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)

## <a name="5-apply-the-terraform-execution-plan"></a>5. Terraform 실행 계획 적용

이 자습서의 마지막 단계는[terraform apply 명령](https://www.terraform.io/docs/commands/apply.html)을 사용하여 `terraform plan` 명령에서 생성한 동작 집합을 적용하는 것입니다.

최신 실행 계획만 적용하려면 다음 명령을 실행합니다.

  ```cmd
  terraform apply
  ```

이전에 저장한 실행 계획을 적용하려면 다음 명령을 실행합니다(&lt;path> 자리 표시자를 저장된 실행 계획이 포함된 경로로 바꾸기).

  ```cmd
  terraform apply <path>
  ```

![Terraform 실행 계획 적용](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>다음 단계

- [Azure Terraform 모듈](https://registry.terraform.io/modules/Azure) 목록 찾아보기
- [Terraform으로 가상 컴퓨터 확장 집합](terraform-create-vm-scaleset-network-disks-hcl.md) 만들기