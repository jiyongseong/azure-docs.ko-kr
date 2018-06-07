---
title: Azure CLI 1.0을 사용하여 Linux VM에 MongoDB 설치 | Microsoft Docs
description: 리소스 관리자 배포 모델을 사용하여 Azure에서 Linux 가상 머신에 MongoDB를 설치하고 구성하는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: 3f55b546-86df-4442-9ef4-8a25fae7b96e
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: a334a0b7b3b638229c61eef086b1919b4c303338
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm-using-the-azure-cli-10"></a>Azure CLI 1.0을 사용하여 Linux VM에 MongoDB를 설치하고 구성하는 방법
[MongoDB](http://www.mongodb.org)는 인기 있는 오픈 소스 고성능 NoSQL 데이터베이스입니다. 이 문서는 리소스 관리자 배포 모델을 사용하여 Azure에서 Linux VM에 MongoDB를 설치하고 구성하는 방법을 보여줍니다. 표시된 예제는 다음과 같은 방법을 자세히 보여줍니다.

* [기본 MongoDB 인스턴스를 수동으로 설치 및 구성](#manually-install-and-configure-mongodb-on-a-vm)
* [Resource Manager 템플릿을 사용하여 기본 MongoDB 클러스터 만들기](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Resource Manager 템플릿을 사용하여 복제본 세트로 복합적인 MongoDB 분할된 클러스터 만들기](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="cli-versions-to-complete-the-task"></a>태스크를 완료하기 위한 CLI 버전
다음 CLI 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다.

- Azure CLI 1.0 - 클래식 및 리소스 관리 배포 모델용 CLI(이 문서)
- [Azure CLI 2.0](create-cli-complete-nodejs.md) - 리소스 관리 배포 모델용 차세대 CLI


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>VM에서 MongoDB 수동 설치 및 구성
MongoDB는 Red Hat/CentOS, SUSE, Ubuntu 및 Debian을 포함하는 Linux 배포판에 대한 [설치 지침을 제공](https://docs.mongodb.com/manual/administration/install-on-linux/)합니다. 다음 예제는 *~/.ssh/id_rsa.pub*에 저장된 SSH 키를 사용하여 *CentOS* VM을 만듭니다. 저장소 계정 이름, DNS 이름, 관리자 자격 증명을 묻는 프롬프트에 답변합니다.

```azurecli
azure vm quick-create \
    --image-urn CentOS \
    --ssh-publickey-file ~/.ssh/id_rsa.pub 
```

앞의 VM 생성 단계 끝에 표시된 공용 IP 주소를 사용하여 VM에 로그인합니다.

```bash
ssh azureuser@40.78.23.145
```

MongoDB에 대한 설치 원본을 추가하려면 다음과 같이 **yum** 리포지토리 파일을 만듭니다.

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.4.repo
```

편집을 위해 MongoDB 리포지토리 파일을 엽니다. 다음 줄을 추가합니다.

```sh
[mongodb-org-3.4]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.4/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.4.asc
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
GitHub의 다음과 같은 Azure 빠른 시작 템플릿을 사용하여 단일 CentOS VM에 기본 MongoDB 인스턴스를 만들 수 있습니다. 이 템플릿은 Linux용 사용자 지정 스크립트 확장을 사용하여 새로 만든 CentOS VM에 `yum` 리포지토리를 추가한 후 MongoDB를 설치합니다.

* [CentOS의 기본 MongoDB 인스턴스](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

다음 예제는 `eastus` 지역에 `myResourceGroup`이라는 리소스 그룹을 만듭니다. 다음과 같이 사용자 고유의 값을 입력합니다.

```azurecli
azure group create \
    --name myResourceGroup \
    --location eastus \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

> [!NOTE]
> Azure CLI는 배포 생성 몇 초 내에 프롬프트를 반환하지만 설치 및 구성 작업이 완료되려면 몇 분 정도 소요됩니다. `azure group deployment show myResourceGroup`을 사용하고 해당 리소스 그룹의 이름을 입력하여 배포 상태를 확인합니다. VM에 SSH를 시도하기 전에 **ProvisioningState**에 *Succeeded*가 표시될 때까지 기다립니다.

배포가 완료되면 VM에 SSH를 수행합니다. 다음 예제와 같이 `azure vm show` 명령을 사용하여 VM의 IP 주소를 불러옵니다.

```azurecli
azure vm show --resource-group myResourceGroup --name myLinuxVM
```

출력의 끝 쪽에 공용 IP 주소가 표시됩니다. VM의 IP 주소를 사용하여 VM에 SSH를 수행합니다.

```bash
ssh azureuser@138.91.149.74
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
> 이러한 복합적인 MongoDB 분할된 클러스터를 배포하려면 20개가 넘는 vCPU가 필요하며, 일반적으로 이 수치는 구독에 대한 지역당 기본 vCPU 수입니다. vCPU 수를 늘리려면 Azure 지원 요청을 생성하십시오.

다음 예제에서는 *eastus* 지역에 *myResourceGroup*이라는 리소스 그룹을 만듭니다. 다음과 같이 사용자 고유의 값을 입력합니다.

```azurecli
azure group create \
    --name myResourceGroup \
    --location eastus \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json
```

> [!NOTE]
> Azure CLI는 배포 생성 몇 초 내에 프롬프트를 반환하지만 설치 및 구성 작업이 완료되려면 한 시간이 넘게 소요될 수 있습니다. `azure group deployment show myResourceGroup`을 사용하고 리소스 그룹의 이름을 맞게 조정하여 배포 상태를 확인합니다. VM에 연결하기 전에 **ProvisioningState**에 *Succeeded*가 표시될 때까지 기다립니다.


## <a name="next-steps"></a>다음 단계
이 예는 VM을 통해 로컬에서 MongoDB 인스턴스에 연결합니다. 다른 VM 또는 네트워크에서 MongoDB 인스턴스에 연결하려면 적절한 [네트워크 보안 그룹 규칙이 생성되어있어야](nsg-quickstart.md) 합니다.

템플릿을 사용하여 만드는 방법에 대한 자세한 내용은 [Azure Resource Manager 개요](../../azure-resource-manager/resource-group-overview.md)를 참조하세요.

Azure Resource Manager 템플릿은 사용자 지정 스크립트 확장을 사용하여 VM에 스크립트를 다운로드하고 실행합니다. 자세한 내용은 [Linux Virtual Machines에서 Azure 사용자 지정 스크립트 확장 사용](extensions-customscript.md)을 참조하세요.

