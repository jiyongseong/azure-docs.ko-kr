---
title: Azure 공급자 배포 슬롯과 Terraform
description: Azure 공급자 배포 슬롯에서 Terraform을 사용하는 방법에 대한 자습서
keywords: terraform, devops, 가상 머신, Azure, 배포 슬롯
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 4/05/2018
ms.topic: article
ms.openlocfilehash: 3a018dbaf90801604b13efcf8bd7afb6dbc68659
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="use-terraform-to-provision-infrastructure-with-azure-deployment-slots"></a>Terraform을 사용하여 Azure 배포 슬롯으로 인프라 프로비전

[Azure 배포 슬롯](/azure/app-service/web-sites-staged-publishing)을 사용하여 여러 다른 앱 버전 간을 전환할 수 있습니다. 이 기능을 사용하면 손상된 배포의 영향을 최소화할 수 있습니다. 

이 문서에서는 GitHub 및 Azure를 통해 두 개의 앱을 배포하는 과정을 안내하면서 배포 슬롯을 사용하는 예를 보여줍니다. 하나의 앱이 프로덕션 슬롯에 호스트됩니다. 두 번째 앱은 스테이징 슬롯에 호스트됩니다. ("프로덕션" 및 "스테이징"은 임의의 이름이며 시나리오를 나타내는 이름을 원하는 대로 사용할 수 있습니다.) 배포 슬롯을 구성한 후, 필요에 따라 Terraform을 사용하여 두 슬롯 사이를 전환할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

- **Azure 구독**: Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.

- **GitHub 계정**: 테스트 GitHub 리포지토리를 포크하고 사용하려면 [GitHub](http://www.github.com) 계정이 필요합니다.

## <a name="create-and-apply-the-terraform-plan"></a>Terraform 계획 만들기 및 적용

1. [Azure Portal](http://portal.azure.com)로 이동합니다.

1. [Azure Cloud Shell](/azure/cloud-shell/overview)을 엽니다. 이전에 환경을 선택하지 않은 경우 환경으로 **Bash**를 선택합니다.

    ![Cloud Shell 프롬프트](./media/terraform-slot-walkthru/azure-portal-cloud-shell-button-min.png)

1. 디렉터리를 `clouddrive` 디렉터리로 변경합니다.

    ```bash
    cd clouddrive
    ```

1. 이름이 `deploy`인 디렉터리를 만듭니다.

    ```bash
    mkdir deploy
    ```

1. 이름이 `swap`인 디렉터리를 만듭니다.

    ```bash
    mkdir swap
    ```

1. `ls` bash 명령을 사용하여 두 디렉터리를 성공적으로 만들었는지 확인합니다.

    ![디렉터리를 만든 후 Cloud Shell](./media/terraform-slot-walkthru/cloud-shell-after-creating-dirs.png)

1. 디렉터리를 `deploy` 디렉터리로 변경합니다.

    ```bash
    cd deploy
    ```

1. [vi 편집기](https://www.debian.org/doc/manuals/debian-tutorial/ch-editor.html)를 사용하여 이름이 `deploy.tf`인 파일을 만듭니다. 이 파일에는 [Terraform 구성](https://www.terraform.io/docs/configuration/index.html)이 포함됩니다.

    ```bash
    vi deploy.tf
    ```

1. I 키를 선택하여 삽입 모드를시작합니다.

1. 다음 코드를 편집기에 붙여 넣습니다.

    ```JSON
    # Configure the Azure provider
    provider "azurerm" { }

    resource "azurerm_resource_group" "slotDemo" {
        name = "slotDemoResourceGroup"
        location = "westus2"
    }

    resource "azurerm_app_service_plan" "slotDemo" {
        name                = "slotAppServicePlan"
        location            = "${azurerm_resource_group.slotDemo.location}"
        resource_group_name = "${azurerm_resource_group.slotDemo.name}"
        sku {
            tier = "Standard"
            size = "S1"
        }
    }

    resource "azurerm_app_service" "slotDemo" {
        name                = "slotAppService"
        location            = "${azurerm_resource_group.slotDemo.location}"
        resource_group_name = "${azurerm_resource_group.slotDemo.name}"
        app_service_plan_id = "${azurerm_app_service_plan.slotDemo.id}"
    }

    resource "azurerm_app_service_slot" "slotDemo" {
        name                = "slotAppServiceSlotOne"
        location            = "${azurerm_resource_group.slotDemo.location}"
        resource_group_name = "${azurerm_resource_group.slotDemo.name}"
        app_service_plan_id = "${azurerm_app_service_plan.slotDemo.id}"
        app_service_name    = "${azurerm_app_service.slotDemo.name}"
    }
    ```

1. Esc 키를 선택하여 삽입 모드를 종료합니다.

1. 파일을 저장하고 다음 명령을 입력하여 vi 편집기를 종료합니다.

    ```bash
    :wq
    ```

1. 파일을 만들었으므로 해당 내용을 확인합니다.

    ```bash
    cat deploy.tf
    ```

1. Terraform을 초기화합니다.

    ```bash
    terraform init
    ```

1. Terraform 계획을 만듭니다.

    ```bash
    terraform plan
    ```

1. `deploy.tf` 구성 파일에 정의된 리소스를 프로비전합니다. (프롬프트에 `yes`를 입력하여 작업을 확인합니다.)

    ```bash
    terraform apply
    ```

1. Cloud Shell 창을 닫습니다.

1. Azure Portal의 주 메뉴에서 **리소스 그룹**을 선택합니다.

    ![포털의 "리소스 그룹" 선택 항목](./media/terraform-slot-walkthru/resource-groups-menu-option.png)

1. **리소스 그룹** 탭에서 **slotDemoResourceGroup**을 선택합니다.

    ![Terraform에서 만든 리소스 그룹](./media/terraform-slot-walkthru/resource-group.png)

이제 Terraform이 만든 모든 리소스가 표시됩니다.

![Terraform에서 만든 리소스](./media/terraform-slot-walkthru/resources.png)

## <a name="fork-the-test-project"></a>테스트 프로젝트 포크

배포 슬롯 생성 및 배포 슬롯과 교환을 테스트하려면 그 전에 GitHub에서 테스트 프로젝트를 포크해야 합니다.

1. [GitHub의 awesome-terraform 리포지토리](https://github.com/Azure/awesome-terraform)로 이동합니다.

1. **awesome-terraform** 리포지토리를 포크합니다.

    ![GitHub awesome-terraform 리포지토리 포크](./media/terraform-slot-walkthru/fork-repo.png)

1. 프롬프트에 따라 환경을 포크합니다.

## <a name="deploy-from-github-to-your-deployment-slots"></a>GitHub에서 배포 슬롯으로 배포

테스트 프로젝트 리포지토리를 포크한 후에는 다음 단계를 통해 배포 슬롯을 구성합니다.

1. Azure Portal의 주 메뉴에서 **리소스 그룹**을 선택합니다.

1. **slotDemoResourceGroup**을 선택합니다.

1. **slotAppService**를 선택합니다.

1. **배포 옵션**을 선택합니다.

    ![App Service 리소스의 배포 옵션](./media/terraform-slot-walkthru/deployment-options.png)

1. **배포 옵션** 탭에서 **원본 선택**을 선택한 다음, **GitHub**를 선택합니다.

    ![배포 원본 선택](./media/terraform-slot-walkthru/select-source.png)

1. Azure가 연결을 설정하고 모든 옵션을 표시하면 **권한 부여**를 선택합니다.

1. **권한 부여** 탭에서 **권한 부여**을 선택하고 Azure가 GitHub 계정에 액세스하는 데 필요한 자격 증명을 제공합니다. 

1. Azure가 GitHub 자격 증명의 유효성을 검사한 후에는 권한 부여 절차가 완료되었다는 메시지가 표시됩니다. **확인**을 선택하여 **권한 부여** 탭을 닫습니다.

1. **조직 선택**을 선택하고 조직을 선택합니다.

1. **프로젝트 선택**을 선택합니다.

1. **프로젝트 선택** 탭에서 **awesome-terraform** 프로젝트를 선택합니다.

    ![awesome-terraform 프로젝트 선택](./media/terraform-slot-walkthru/choose-project.png)

1. **분기 선택**을 선택합니다.

1. **분기 선택** 탭에서 **마스터**를 선택합니다.

    ![마스터 분기 선택](./media/terraform-slot-walkthru/choose-branch-master.png)

1. **배포 옵션** 탭에서 **확인**을 선택합니다.

이 시점에서 프로덕션 슬롯을 배포했습니다. 스테이징 슬롯을 배포하려면 다음 수정 내용으로 이 섹션의 이전 단계를 모두 수행합니다.

- 3단계에서 **slotAppServiceSlotOne** 리소스를 선택합니다.

- 13단계에서 마스터 분기 대신 작업 중 분기를 선택합니다.

    ![작업 중 분기 선택](./media/terraform-slot-walkthru/choose-branch-working.png)

## <a name="test-the-app-deployments"></a>앱 배포 테스트

이전 섹션에서는 GitHub의 다른 분기에서 배포할 두 개의 슬롯인 **slotAppService** 및 **slotAppServiceSlotOne**을 설정했습니다. 웹앱을 미리 보면서 성공적으로 배포되었는지 확인합니다.

다음 단계를 2번 수행합니다. 3단계에서 첫 번째에서 **slotAppService**를 선택한 다음, 두 번째에서 **slotAppServiceSlotOne**을 선택합니다.

1. Azure Portal의 주 메뉴에서 **리소스 그룹**을 선택합니다.

1. **slotDemoResourceGroup**을 선택합니다.

1. **slotAppService** 또는 **slotAppServiceSlotOne**을 선택합니다.

1. 개요 페이지에서 **URL**를 선택합니다.

    ![개요 탭에서 URL을 선택하여 앱을 렌더링합니다.](./media/terraform-slot-walkthru/resource-url.png)

> [!NOTE]
> Azure가 GitHub에서 사이트를 만들고 배포하는 데 몇 분이 걸릴 수 있습니다.
>
>

**slotAppService** 웹앱의 경우 페이지 제목이 **Slot Demo App 1**인 파란색 페이지가 표시됩니다. **slotAppServiceSlotOne** 웹앱의 경우 페이지 제목이 **Slot Demo App 2**인 녹색 페이지가 표시됩니다.

![앱을 미리 보면서 앱이 올바르게 배포되었는지 테스트합니다.](./media/terraform-slot-walkthru/app-preview.png)

## <a name="swap-the-two-deployment-slots"></a>두 개의 배포 슬롯 교환

두 개의 배포 슬롯을 서로 교환하려면 다음 단계를 수행합니다.
 
1. **slotAppService**(파란색 페이지가 있는 앱)가 실행 중인 브라우저 탭으로 전환합니다. 

1. 별도의 탭에서 Azure Portal로 돌아갑니다.

1. Cloud Shell을 엽니다.

1. 디렉터리를 **clouddrive/swap** 디렉터리로 변경합니다.

    ```bash
    cd clouddrive/swap
    ```

1. vi 편집기를 사용하여 이름이 `swap.tf`인 파일을 만듭니다.

    ```bash
    vi swap.tf
    ```

1. I 키를 선택하여 삽입 모드를시작합니다.

1. 다음 코드를 편집기에 붙여 넣습니다.

    ```JSON
    # Configure the Azure provider
    provider "azurerm" { }

    # Swap the production slot and the staging slot
    resource "azurerm_app_service_active_slot" "slotDemoActiveSlot" {
        resource_group_name   = "slotDemoResourceGroup"
        app_service_name      = "slotAppService"
        app_service_slot_name = "slotappServiceSlotOne"
    }
    ```

1. Esc 키를 선택하여 삽입 모드를 종료합니다.

1. 파일을 저장하고 다음 명령을 입력하여 vi 편집기를 종료합니다.

    ```bash
    :wq
    ```

1. Terraform을 초기화합니다.

    ```bash
    terraform init
    ```

1. Terraform 계획을 만듭니다.

    ```bash
    terraform plan
    ```

1. `swap.tf` 구성 파일에 정의된 리소스를 프로비전합니다. (프롬프트에 `yes`를 입력하여 작업을 확인합니다.)

    ```bash
    terraform apply
    ```

1. Terraform에서 슬롯 교환을 마치면 **slotAppService** 웹앱을 렌더링하는 브라우저로 돌아가서 페이지를 새로 고칩니다. 

**slotAppServiceSlotOne** 스테이징 슬롯의 웹앱이 프로덕션 슬롯으로 교환되고 녹색으로 렌더링됩니다. 

![배포 슬롯이 교환되었습니다.](./media/terraform-slot-walkthru/slots-swapped.png)

앱의 원래 프로덕션 버전으로 돌아가려면 `swap.tf` 구성 파일에서 만든 Terraform 계획을 다시 적용합니다.

```bash
terraform apply
```

앱이 교환되면 원래 구성이 표시됩니다.