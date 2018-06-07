---
title: "Azure에서 Linux VM에 대한 Docker Compose 사용 | Microsoft Docs"
description: "Azure CLI와 함께 Linux 가상 머신에서 Docker 및 Compose를 사용하는 방법"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 02ab8cf9-318d-4a28-9d0c-4a31dccc2a84
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: iainfou
ms.openlocfilehash: 387f3095e8eebce3fa6c2f47ffc87995e65bfe2b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2018
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Azure에서 다중 컨테이너 응용 프로그램 정의 및 실행을 위해 Docker 및 Compose 시작
[Compose](http://github.com/docker/compose)를 사용하면 간단한 텍스트 파일을 사용하여 여러 Docker 컨테이너로 구성된 응용 프로그램을 정의할 수 있습니다. 그런 다음 정의된 환경을 배포하도록 모든 작업을 수행하는 단일 명령으로 응용 프로그램을 스핀업합니다. 그 예로, 이 문서에서는 Ubuntu VM의 백 엔드 MariaDB SQL Database로 WordPress 블로그를 신속하게 설정하는 방법을 보여주지만 Compose를 사용하여 좀더 복잡한 응용 프로그램을 설정할 수도 있습니다.


## <a name="set-up-a-linux-vm-as-a-docker-host"></a>Docker 호스트로 Linux VM 설정
다양한 Azure 절차와 Azure Markeplace에서 사용 가능한 이미지 또는 Resource Manager 템플릿을 사용하여 Linux VM을 만들고 Docker 호스트로 설정할 수 있습니다. 예를 들어 [빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)을 사용하여 Azure Docker VM 확장으로 Ubuntu VM을 빠르게 만들려면 [Docker VM 확장을 사용하여 환경 배포](dockerextension.md)를 참조하세요. 

Docker VM 확장을 사용하면 VM이 자동으로 Docker 호스트로 설정되고 Compose는 이미 설치되어 있습니다.


### <a name="create-docker-host-with-azure-cli-20"></a>Azure CLI 2.0을 사용하여 Docker 호스트 만들기
최신 [Azure CLI 2.0](/cli/azure/install-az-cli2)을 설치하고 [az login](/cli/azure/reference-index#az_login)을 사용하여 Azure 계정에 로그인합니다.

먼저 [az group create](/cli/azure/group#az_group_create)를 사용하여 Docker 환경에 대한 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
```

다음으로 [GitHub의 이 Azure Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)의 Azure Docker VM 확장을 포함하는 [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create)로 VM을 배포합니다. 메시지가 표시되면 *newStorageAccountName*, *adminUsername*, *adminPassword* 및 *dnsNameForPublicIP*에 대한 고유한 값을 제공합니다.

```azurecli
az group deployment create --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

배포를 완료하려면 몇 분 정도 소요됩니다.


## <a name="verify-that-compose-is-installed"></a>Compose 설치 여부 확인
DNS 이름을 비롯한 VM의 세부 정보를 보려면 [az vm show](/cli/azure/vm#az_vm_show)를 사용합니다.

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --show-details \
    --query [fqdns] \
    --output tsv
```

새 Docker 호스트로 SSH를 수행합니다. 이전 단계에서 자신의 사용자 이름 및 DNS 이름을 제공합니다.

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Compose가 VM에 설치되어 있는지 확인하려면 다음 명령을 실행합니다.

```bash
docker-compose --version
```

*docker-compose 1.6.2, build 4d72027*과 유사한 출력이 표시됩니다.

> [!TIP]
> 다른 방법을 사용하여 Docker 호스트를 만들었고 Compose를 직접 설치할 필요가 있다면 [Compose 설명서](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md)를 참조하세요.


## <a name="create-a-docker-composeyml-configuration-file"></a>docker-compose.yml 구성 파일 만들기
그 다음, `docker-compose.yml` 파일을 만드는데, 이 파일은 VM에서 실행할 Docker 컨테이너를 정의하기 위한 텍스트 구성 파일입니다. 파일은 각 컨테이너에서 실행되는 이미지(또는 Dockerfile에서 빌드일 수 있음), 필요한 환경 변수 및 종속성, 포트, 컨테이너 간 링크를 지정합니다. yml 파일 구문에 대한 세부 정보는 [Compose 파일 참조](https://docs.docker.com/compose/compose-file/)를 참조하세요.

*docker-compose.yml* 파일을 만듭니다. 원하는 텍스트 편집기를 사용하여 파일에 데이터를 추가합니다. 다음 예제에서는 `sensible-editor`에 대한 프롬프트를 통해 사용하려는 편집기를 선택하는 파일을 만듭니다.

```bash
sensible-editor docker-compose.yml
```

다음 예제를 Docker Compose 파일로 붙여넣습니다. 이 구성은 [DockerHub 레지스트리](https://registry.hub.docker.com/_/wordpress/) 의 이미지를 사용하여 WordPress(오픈 소스 블로깅 및 콘텐츠 관리 시스템) 및 연결된 백 엔드 MariaDB SQL 데이터베이스를 설치합니다. 다음과 같이 고유한 *MYSQL_ROOT_PASSWORD*를 입력합니다.

```sh
wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>
```

## <a name="start-the-containers-with-compose"></a>Compose를 사용하여 컨테이너 시작
*docker-compose.yml* 파일과 동일한 디렉터리에서 다음 명령을 실행합니다(환경에 따라 `sudo`를 사용하여 `docker-compose`를 실행해야 할 수 있음).

```bash
docker-compose up -d
```

이 명령은 *docker-compose.yml*에 지정된 Docker 컨테이너를 시작합니다. 이 단계를 완료하려면 1~2분 정도 걸립니다. 다음 예제와 유사한 출력이 표시됩니다.

```bash
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```

> [!NOTE]
> 백그라운드에서 계속 실행되도록 **-d** 옵션을 시작에서 사용해야 합니다.


컨테이너가 동작하는지 확인하려면 `docker-compose ps`를 입력합니다. 다음과 유사한 결과가 표시됩니다.

```bash
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

이제는 포트 80에서 VM에서 직접 WordPress에 연결할 수 있습니다. 웹 브라우저를 열고 VM의 DNS 이름을 입력합니다(예: `http://mypublicdns.eastus.cloudapp.azure.com`). 이제 WordPress 시작 화면이 표시되면 이 화면에서 설치를 완료하고 응용 프로그램을 시작할 수 있습니다.

![WordPress 시작 화면][wordpress_start]

## <a name="next-steps"></a>다음 단계
* Docker VM에서 Docker 및 Compose를 구성하는 더 많은 옵션을 보려면 [Docker VM 확장 사용자 가이드](https://github.com/Azure/azure-docker-extension/blob/master/README.md) 로 이동하세요. 예를 들어, 하나의 옵션은 Compose yml 파일(JSON으로 변환)을 직접 Docker VM 확장에 구성에 배치합니다.
* 다중 컨테이너 앱 빌드 및 배포의 추가 예제는 [Compose 명령줄 참조](http://docs.docker.com/compose/reference/) 및 [사용자 가이드](http://docs.docker.com/compose/)를 확인합니다.
* Azure 리소스 관리자 템플릿, 사용자 자신의 템플릿 또는 [커뮤니티](https://azure.microsoft.com/documentation/templates/)에서 배포된 템플릿을 사용하여, Azure VM을 Docker로 배포하고 Compose로 응용 프로그램을 설정합니다. 예를 들어 [Docker를 사용한 WordPress 블로그 배포](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) 템플릿은 Docker 및 Compose를 사용하여 Ubuntu VM에 MySQL 백 엔드와 함께 WordPress를 신속하게 배포합니다.
* Docker Swarm 클러스터와 Docker Compose 통합을 시도합니다. 시나리오의 경우 [Swarm으로 Compose 사용](https://docs.docker.com/compose/swarm/) 을 참조하세요.

<!--Image references-->

[wordpress_start]: media/docker-compose-quickstart/WordPress.png
