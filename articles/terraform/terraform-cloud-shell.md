---
title: "Azure Cloud Shell에서 Terraform 사용"
description: "Azure Cloud Shell로 Terraform을 사용하여 인증 및 템플릿 구성을 간소합니다."
keywords: "terraform, devops, 확장 집합, 가상 머신, 네트워크, 저장소, 모듈"
ms.service: virtual-machines-linux
author: dcaro
ms.author: dcaro
ms.date: 10/19/2017
ms.topic: article
ms.openlocfilehash: 5157066086f1bdfa580c1946942bda4505e48935
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2018
---
# <a name="terraform-cloud-shell-development"></a>Terraform Cloud Shell 개발 

Terraform은 macOS 터미널이나 Windows 또는 Linux의 Bash와 같은 Bash 명령줄에서 원활하게 작동합니다. [Azure Cloud Shell](/azure/cloud-shell/overview)의 Bash 환경에서 Terraform 구성을 실행하면 개발 주기를 단축할 수 있는 몇 가지 고유한 장점이 있습니다.

이 개념 문서에서는 Azure에 배포하는 Terraform 스크립트를 작성하는 데 도움이 되는 Cloud Shell 기능에 대해 설명합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>자동 자격 증명 구성

Terraform이 설치되면 Cloud Shell에서 즉시 사용할 수 있습니다. Terraform 스크립트는 추가 구성없이 인프라를 관리하기 위해 Cloud Shell에 로그인할 때 Azure로 인증합니다. 자동 인증을 사용하면 수동으로 Active Directory 서비스 주체를 만들고 Azure Terraform 공급자 변수를 구성할 필요가 없습니다.


## <a name="using-modules-and-providers"></a>모듈 및 공급자 사용

Azure Terraform 모듈이 Azure 구독에 있는 리소스에 액세스하고 변경하려면 자격 증명이 필요합니다. Cloud Shell에서 작업할 때 다음 코드를 스크립트에 추가하여 Cloud Shell에서 Azure Terraform 모듈을 사용하십시오.

```tf
# Configure the Microsoft Azure Provider
provider "azurerm" {
}
```

Cloud Shell은 임의의 `terraform` CLI 명령을 사용할 때 환경 변수를 통해 `azurerm` 공급자에 필요한 값을 전달합니다.

## <a name="other-cloud-shell-developer-tools"></a>다른 Cloud Shell 개발자 도구

파일 및 셸 상태는 Cloud Shell 세션 간에 Azure Storage에 유지됩니다. [Azure Storage 탐색기](/azure/vs-azure-tools-storage-manage-with-storage-explorer)를 사용하여 로컬 컴퓨터에서 Cloud Shell에 파일을 복사하고 업로드합니다.

Azure CLI 2.0은 Cloud Shell에서 사용할 수 있으며 `terraform apply` 또는 `terraform destroy`가 완료된 후 구성을 테스트하고 작업을 확인하는 데 유용한 도구입니다.


## <a name="next-steps"></a>다음 단계

[모듈 레지스트리를 사용하여 작은 VM 클러스터 만들기](terraform-create-vm-cluster-module.md)
[사용자 지정 HCL 사용하여 작은 VM 클러스터 만들기](terraform-create-vm-cluster-with-infrastructure.md)
