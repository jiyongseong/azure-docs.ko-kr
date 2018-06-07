---
title: Azure Resource Manager 템플릿 샘플 - Azure Container Instances
description: Azure Container Instances용 Azure Resource Manager 템플릿 샘플
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 05/17/2018
ms.author: marsma
ms.openlocfilehash: fcc2e6c52e773d95bcdfe43d881fce036fae6513
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259743"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Azure Container Instances용 Azure Resource Manager 템플릿

다음 예제 템플릿은 다양한 구성에서 컨테이너 인스턴스를 배포합니다.

배포 옵션은 [배포](#deployment) 섹션을 참조하세요. 사용자 고유의 템플릿을 만들려는 경우 Azure Container Instances [Resource Manager 템플릿 참조][ref]는 템플릿 형식 및 사용 가능한 속성을 자세히 설명합니다.

## <a name="sample-templates"></a>샘플 템플릿

| | |
|-|-|
| **응용 프로그램** ||
| [Wordpress][app-wp] | 컨테이너 인스턴스에서 WordPress 웹 사이트 및 해당 MySQL 데이터베이스를 만듭니다. WordPress 사이트 콘텐츠 및 MySQL 데이터베이스는 Azure 파일 공유로 지속됩니다. |
| [SQL Server와 IIS를 사용한 MS NAV][app-nav] | 전체 기능이 자체 포함된 Dynamics NAV / Dynamics 365 Business Central 환경를 사용하여 단일 Windows 컨테이너를 배포합니다. |
| **볼륨** ||
| [emptyDir][vol-emptydir] | emptyDir 볼륨을 공유하는 두 개의 Linux 컨테이너를 배포합니다. |
| [gitRepo][vol-gitrepo] | GitHub 리포지토리를 복제하고 볼륨으로 탑재하는 Linux 컨테이너를 배포합니다. |
| [secret][vol-secret] | 비밀 볼륨으로 탑재된 PFX 인증서를 사용하여 Linux 컨테이너를 배포합니다. |
| **네트워킹** ||
| [UDP-공개 컨테이너][net-udp] | UDP 포트를 공개하는 Windows 또는 Linux 컨테이너를 배포합니다. |
| [공용 IP를 사용한 Linux 컨테이너][net-publicip] | 공용 IP를 통해 액세스할 수 있는 단일 Linux 컨테이너를 배포합니다. |
| **Azure 리소스** ||
| [Azure Storage 계정 및 파일 공유 만들기][az-files] | 컨테이너 인스턴스에서 Azure CLI를 사용하여 저장소 계정 및 Azure 파일 공유를 만듭니다.

## <a name="deployment"></a>배포

Resource Manager 템플릿을 사용하여 리소스를 배포하기 위한 몇 가지 옵션이 있습니다.

[Azure CLI][deploy-cli]

[Azure PowerShell][deploy-powershell]

[Azure 포털][deploy-portal]

[REST API][deploy-rest]

<!-- LINKS - External -->
[app-nav]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-dynamicsnav
[app-wp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress
[az-files]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-storage-file-share
[net-publicip]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-linuxcontainer-public-ip
[net-udp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-udp
[repo]: https://github.com/Azure/azure-quickstart-templates
[vol-emptydir]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-emptydir
[vol-gitrepo]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-gitrepo
[vol-secret]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-secret

<!-- LINKS - Internal -->
[deploy-cli]: ../azure-resource-manager/resource-group-template-deploy-cli.md
[deploy-portal]: ../azure-resource-manager/resource-group-template-deploy-portal.md
[deploy-powershell]: ../azure-resource-manager/resource-group-template-deploy.md
[deploy-rest]: ../azure-resource-manager/resource-group-template-deploy-rest.md
[ref]: /azure/templates/microsoft.containerinstance/containergroups