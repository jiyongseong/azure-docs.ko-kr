---
title: "Azure CLI를 사용하여 Linux VM에 MongoDB 설치 | Microsoft Docs"
description: "Azure CLI 2.0을 사용하여 Linux 가상 머신에 MongoDB를 설치하고 구성하는 방법 알아보기"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
ms.assetid: 3f55b546-86df-4442-9ef4-8a25fae7b96e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: iainfou
ms.openlocfilehash: b21b33a265d499136dbe3e72538923d8295e9876
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2018
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm"></a>Linux VM에 MongoDB를 설치하고 구성하는 방법
[MongoDB](http://www.mongodb.org)는 인기 있는 고성능 오픈 소스 NoSQL 데이터베이스입니다. 이 문서에서는 Azure CLI 2.0을 사용하여 Linux VM에 MongoDB를 설치하고 구성하는 방법을 보여 줍니다. [Azure CLI 1.0](install-mongodb-nodejs.md)에서 이러한 단계를 수행할 수도 있습니다. 표시된 예제는 다음과 같은 방법을 자세히 보여줍니다.

* [기본 MongoDB 인스턴스를 수동으로 설치 및 구성](#manually-install-and-configure-mongodb-on-a-vm)
* [Resource Manager 템플릿을 사용하여 기본 MongoDB 클러스터 만들기](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Resource Manager 템플릿을 사용하여 복제본 세트로 복합적인 MongoDB 분할된 클러스터 만들기](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>VM에서 MongoDB 수동 설치 및 구성
MongoDB는 Red Hat/CentOS, SUSE, Ubuntu 및 Debian을 포함하는 Linux 배포판에 대한 [설치 지침을 제공](https://docs.mongodb.com/manual/administration/install-on-linux/)합니다. 다음 예제는 *CentOS* VM을 만듭니다. 이 환경을 만들려면 최신 [Azure CLI 2.0](/cli/azure/install-az-cli2)을 설치하고 [az login](/cli/azure/reference-index#az_login)을 사용하여 Azure 계정에 로그인해야 합니다.

[az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
```

[az vm create](/cli/azure/vm#az_vm_create)로 VM을 만듭니다. 다음 예제에서는 SSH 공개 키 인증을 사용하여 *azureuser*라는 사용자로 *myVM*이라는 VM을 만듭니다.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

고유한 사용자 이름을 사용하는 VM에 대한 SSH 및 이전 단계의 출력에 나열된 `publicIpAddress`는 다음과 같습니다.

```bash
ssh azureuser@<publicIpAddress>
```

MongoDB에 대한 설치 원본을 추가하려면 다음과 같이 **yum** 리포지토리 파일을 만듭니다.

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.6.repo
```

`vi` 또는 `nano`를 사용하여 편집을 위해 MongoDB 리포지토리 파일을 엽니다. 다음 줄을 추가합니다.

```sh
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
```

다음과 같이 **yum**을 사용하여 MongoDB를 설치합니다.

```bash
sudo yum install -y mongodb-org
```

기본적으로 MongoDB 액세스를 막는 CentOS 이미지에 SELinux가 강제 적용됩니다. 다음과 같이 정책 관리 도구를 설치하고 MongoDB가 자체적인 기본 TCP 포트 27017에서 작업을 허용하도록 SELinux를 구성합니다.

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

다음과 같이 MongoDB 서비스를 시작합니다.

```bash
sudo service mongod start
```

로컬 `mongo` 클라이언트를 사용하여 연결하여 MongoDB 설치를 확인합니다.

```bash
mongo
```

이제 데이터를 추가한 후 검색하여 MongoDB 인스턴스를 테스트합니다.

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

필요하면 시스템을 다시 부팅하는 동안 MongoDB를 자동으로 시작하도록 구성합니다.

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>템플릿을 사용하여 CentOS에서 기본 MongoDB 인스턴스 만들기
GitHub의 다음과 같은 Azure 빠른 시작 템플릿을 사용하여 단일 CentOS VM에 기본 MongoDB 인스턴스를 만들 수 있습니다. 이 템플릿은 Linux용 사용자 지정 스크립트 확장을 사용하여 새로 만든 CentOS VM에 **yum** 리포지토리를 추가한 후 MongoDB를 설치합니다.

* [CentOS의 기본 MongoDB 인스턴스](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

이 환경을 만들려면 최신 [Azure CLI 2.0](/cli/azure/install-az-cli2)을 설치하고 [az login](/cli/azure/reference-index#az_login)을 사용하여 Azure 계정에 로그인해야 합니다. 먼저 [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
```

다음으로 [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create)를 사용하여 MongoDB 템플릿을 배포합니다. 메시지가 표시되면 *newStorageAccountName*, *dnsNameForPublicIP* 및 사용자 이름과 암호에 대한 고유한 값을 입력합니다.

```azurecli
az group deployment create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

VM의 공용 DNS 주소를 사용하여 VM에 로그온합니다. [az vm show](/cli/azure/vm#az_vm_show)를 사용하여 공용 DNS 주소를 볼 수 있습니다.

```azurecli
az vm show -g myResourceGroup -n myLinuxVM -d --query [fqdns] -o tsv
```

고유한 사용자 이름 및 공용 DNS 주소를 사용하여 VM에 SSH합니다.

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

다음과 같이 로컬 `mongo` 클라이언트를 사용하여 연결하여 MongoDB 설치를 확인합니다.

```bash
mongo
```

이제 다음과 같이 데이터를 추가한 후 검색하여 인스턴스를 테스트합니다.

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>템플릿을 사용하여 CentOS에서 복합적인 MongoDB 분할된 클러스터 만들기
GitHub의 다음과 같은 Azure 빠른 시작 템플릿을 사용하여 복합적인 MongoDB 분할된 클러스터를 만들 수 있습니다. 이 템플릿은 [MongoDB 분할된 클러스터 모범 사례](https://docs.mongodb.com/manual/core/sharded-cluster-components/)에 따라 중복성 및 고가용성을 제공합니다. 템플릿은 각 복제본 세트에 3개의 노드가 있는 2개의 분할 클러스터를 만듭니다. 노드가 3개 있는 구성 서버 복제본 세트 하나와 **mongos** 라우터 서버 2개가 만들어져서 분할 전반의 응용 프로그램에 대한 일관성을 제공합니다.

* [CentOS의 MongoDB 분할 클러스터](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> 이러한 복합적인 MongoDB 분할된 클러스터를 배포하려면 20개가 넘는 코어가 필요하며, 일반적으로 이 수치는 구독에 대한 지역당 기본 코어 수입니다. 코어 수를 늘리려면 Azure 지원 요청을 생성하십시오.

이 환경을 만들려면 최신 [Azure CLI 2.0](/cli/azure/install-az-cli2)을 설치하고 [az login](/cli/azure/reference-index#az_login)을 사용하여 Azure 계정에 로그인해야 합니다. 먼저 [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
```

다음으로 [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create)를 사용하여 MongoDB 템플릿을 배포합니다. *mongoAdminUsername*, *sizeOfDataDiskInGB* 및 *configNodeVmSize* 등에 필요한 대로, 고유한 리소스 이름 및 크기를 정의합니다.

```azurecli
az group deployment create --resource-group myResourceGroup \
  --parameters '{"adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "mongoAdminUsername": {"value": "mongoadmin"},
    "mongoAdminPassword": {"value": "P@ssw0rd!"},
    "dnsNamePrefix": {"value": "mypublicdns"},
    "environment": {"value": "AzureCloud"},
    "numDataDisks": {"value": "4"},
    "sizeOfDataDiskInGB": {"value": 20},
    "centOsVersion": {"value": "7.0"},
    "routerNodeVmSize": {"value": "Standard_DS3_v2"},
    "configNodeVmSize": {"value": "Standard_DS3_v2"},
    "replicaNodeVmSize": {"value": "Standard_DS3_v2"},
    "zabbixServerIPAddress": {"value": "Null"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json \
  --name myMongoDBCluster \
  --no-wait
```

이 배포는 모든 VM 인스턴스를 배포하고 구성하는 데 1시간 이상이 걸릴 수 있습니다. 템플릿 배포가 Azure 플랫폼에서 수락되면 명령 프롬프트로 제어를 반환하는 이전 명령의 끝에 `--no-wait` 플래그가 사용됩니다. 그런 다음 [az group deployment show](/cli/azure/group/deployment#az_group_deployment_show)를 사용하여 배포 상태를 볼 수 있습니다. 다음 예제에서는 *myResourceGroup* 리소스 그룹에서 *myMongoDBCluster* 배포에 대한 상태를 볼 수 있습니다.

```azurecli
az group deployment show \
    --resource-group myResourceGroup \
    --name myMongoDBCluster \
    --query [properties.provisioningState] \
    --output tsv
```

## <a name="next-steps"></a>다음 단계
이 예는 VM을 통해 로컬에서 MongoDB 인스턴스에 연결합니다. 다른 VM 또는 네트워크에서 MongoDB 인스턴스에 연결하려면 적절한 [네트워크 보안 그룹 규칙이 생성되어있어야](nsg-quickstart.md) 합니다.

이러한 예제는 개발용으로 코어 MongoDB 환경을 배포합니다. 사용자 환경에 필요한 보안 구성 옵션을 적용합니다. 자세한 내용은 [MongoDB 보안 문서](https://docs.mongodb.com/manual/security/)를 참조하세요.

템플릿을 사용하여 만드는 방법에 대한 자세한 내용은 [Azure Resource Manager 개요](../../azure-resource-manager/resource-group-overview.md)를 참조하세요.

Azure Resource Manager 템플릿은 사용자 지정 스크립트 확장을 사용하여 VM에 스크립트를 다운로드하고 실행합니다. 자세한 내용은 [Linux Virtual Machines에서 Azure 사용자 지정 스크립트 확장 사용](extensions-customscript.md)을 참조하세요.

