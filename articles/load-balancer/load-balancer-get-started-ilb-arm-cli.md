---
title: 내부 기본 부하 분산 장치 만들기 - Azure CLI 2.0 | Microsoft Docs
description: Azure CLI 2.0을 사용하여 내부 부하 분산 장치를 만드는 방법을 알아봅니다.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2017
ms.author: kumud
ms.openlocfilehash: d90a4e74b6ad3bb95e91ad3a5327c887a87784bd
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2018
---
# <a name="create-an-internal-load-balancer-to-load-balance-vms-using-azure-cli-20"></a>Azure CLI 2.0을 사용하여 VM 부하를 분산하는 내부 부하 분산 장치 만들기

이 문서에서는 VM 부하를 분산하는 내부 부하 분산 장치를 만드는 방법을 보여 줍니다. 부하 분산 장치를 테스트하려면 Ubuntu 서버를 실행하는 두 개의 VM(가상 머신)을 배포하여 웹 응용 프로그램의 부하를 분산합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

CLI를 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에서는 Azure CLI 버전 2.0.28 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](https://docs.microsoft.com/cli/azure/group#create)를 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

다음 예제에서는 *eastus* 위치에 *myResourceGroupILB*라는 리소스 그룹을 만듭니다.

```azurecli-interactive
  az group create \
    --name myResourceGroupILB \
    --location eastus
```
## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

[az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#create)를 사용하여 *myResourceGroup*에 *mySubnet*이라는 서브넷이 있는 *myVnet* 가상 네트워크를 만듭니다.

```azurecli-interactive
  az network vnet create \
    --name myVnet
    --resource-group myResourceGroupILB \
    --location eastus \
    --subnet-name mySubnet
```
## <a name="create-basic-load-balancer"></a>기본 부하 분산 장치 만들기

이 섹션에서는 다음과 같은 부하 분산 장치 구성 요소를 만들고 구성하는 방법에 대해 자세히 설명합니다.
  - 부하 분산 장치에서 들어오는 네트워크 트래픽을 받는 프런트 엔드 IP 구성
  - 프런트 엔드 풀에서 부하 분산된 네트워크 트래픽을 보내는 백 엔드 IP 풀
  - 백 엔드 VM 인스턴스의 상태를 확인하는 상태 프로브
  - 트래픽이 VM에 분산되는 방법을 정의하는 부하 분산 장치 규칙

### <a name="create-the-load-balancer"></a>부하 분산 장치 만들기

[az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#create)를 사용하여 **myFrontEnd**라는 프런트 엔드 IP 구성 및 **10.0.0.7 개인 IP 주소와 연결되는 **myBackEndPool**이라는 백 엔드 풀이 포함된 **myLoadBalancer**라는 공용 기본 부하 분산 장치를 만듭니다.

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupILB \
    --name myLoadBalancer \
    --frontend-ip-name myFrontEnd \
    --private-ip-address 10.0.0.7 \
    --backend-pool-name myBackEndPool \
    --vnet-name myVnet \
    --subnet mySubnet      
  ```
### <a name="create-the-health-probe"></a>상태 프로브 만들기

상태 프로브는 모든 가상 머신 인스턴스를 검사하여 네트워크 트래픽을 받을 수 있는지 확인합니다. 프로브 검사에 실패한 가상 머신 인스턴스는 다시 온라인 상태가 되어 프로브 검사가 정상으로 나올 때까지 부하 분산 장치에서 제거됩니다. [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#create)를 사용하여 가상 머신의 상태를 모니터링하는 상태 프로브를 만듭니다. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupILB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>부하 분산 장치 규칙 만들기

부하 분산 장치 규칙은 들어오는 트래픽에 대한 프런트 엔드 IP 구성 및 트래픽을 받을 백 엔드 IP 풀과 필요한 원본 및 대상 포트를 함께 정의합니다. [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#create)를 사용하여 *myFrontEndPool* 프런트 엔드 풀에서 80 포트를 수신 대기하고, 마찬가지로 80 포트를 통해 *myBackEndPool* 백 엔드 주소 풀에 부하 분산된 네트워크 트래픽을 보내는 *myLoadBalancerRuleWeb* 부하 분산 장치 규칙을 만듭니다. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupILB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```

## <a name="create-servers-for-the-backend-address-pool"></a>백 엔드 주소 풀용 서버 만들기

일부 VM을 배포하고 부하 분산 장치를 테스트하려면, 먼저 지원되는 가상 네트워크 리소스를 만듭니다.

###  <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기
가상 네트워크에 대한 인바운드 연결을 정의하는 네트워크 보안 그룹을 만듭니다. 네트워크 보안 그룹을 만들어 가상 네트워크에 대한 인바운드 연결을 정의합니다.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupILB \
    --name myNetworkSecurityGroup
```

### <a name="create-a-network-security-group-rule"></a>네트워크 보안 그룹 규칙 만들기

포트 80을 통한 인바운드 연결을 허용하는 네트워크 보안 그룹 규칙을 만듭니다.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupILB \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleHTTP \
    --protocol tcp \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow \
    --priority 300
```
### <a name="create-nics"></a>NIC 만들기

[az network nic create](/cli/azure/network/nic#az_network_nic_create)를 사용하여 두 개의 네트워크 인터페이스를 만들고, 개인 IP 주소 및 네트워크 보안 그룹에 연결합니다. 

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupILB \
    --name myNic$i \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
done
```

## <a name="create-backend-servers"></a>백 엔드 서버 만들기

이 예제에서는 부하 분산 장치에 대한 백 엔드 서버로 사용할 두 개의 가상 머신을 만듭니다. 또한 부하 분산 장치가 성공적으로 만들어졌는지 확인하려면 가상 머신에 NGINX도 설치합니다.

### <a name="create-an-availability-set"></a>가용성 집합 만들기

[az vm availabilityset create](/cli/azure/network/nic#az_network_availabilityset_create)를 사용하여 가용성 집합을 만듭니다.

 ```azurecli-interactive
  az vm availability-set create \
    --resource-group myResourceGroupILB \
    --name myAvailabilitySet
```

### <a name="create-two-virtual-machines"></a>두 개의 가상 머신 만들기

cloud-init 구성 파일을 사용하여 NGINX를 설치하고 Linux 가상 머신에서 'Hello World' Node.js 앱을 실행할 수 있습니다. 현재 셸에서 cloud-init.txt라는 파일을 만들고 다음 구성을 복사하여 셸에 붙여넣습니다. 전체 cloud-init 파일, 특히 첫 줄이 올바르게 복사해야 합니다.

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
``` 
 
[az vm create](/cli/azure/vm#az_vm_create)를 사용하여 가상 머신을 만듭니다.

 ```azurecli-interactive
for i in `seq 1 2`; do
  az vm create \
    --resource-group myResourceGroupILB \
    --name myVM$i \
    --availability-set myAvailabilitySet \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
    done
```
VM을 배포하는 데 몇 분 정도 걸릴 수 있습니다.

### <a name="create-a-vm-for-testing-the-load-balancer"></a>부하 분산 장치를 테스트할 VM 만들기

부하 분산 장치를 테스트하려면 *myVMTest* 가상 머신을 만들고 *myNic3*에 연결합니다.

```azurecli-interactive
 az vm create \
    --resource-group myResourceGroupILB \
    --name myVMTest \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword123456!
```

## <a name="test-the-internal-load-balancer"></a>내부 부하 분산 장치 테스트

부하 분산 장치를 테스트하려면 먼저 부하 분산 장치의 개인 IP 주소를 가져와야 합니다. 그런 다음, myVMTest 가상 머신에 로그인하고, 해당 웹 브라우저의 주소 표시줄에 개인 IP 주소를 입력합니다.

부하 분산 장치의 개인 IP 주소를 가져오려면 [az network lb show](/cli/azure/network/public-ip##az-network-lb-show)를 사용합니다. 개인 IP 주소를 복사한 다음, 가상 머신(*myVMTest*)의 웹 브라우저에 있는 주소 표시줄에 붙여넣습니다.

```azurecli-interactive
  az network lb show \
    --name myLoadBalancer
    --resource-group myResourceGroupILB
``` 
![부하 분산 장치 테스트](./media/load-balancer-get-started-ilb-arm-cli/load-balancer-test.png)

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az_group_delete) 명령을 사용하여 리소스 그룹, 부하 분산 장치 및 모든 관련 리소스를 제거할 수 있습니다.

```azurecli-interactive 
  az group delete --name myResourceGroupILB
```


## <a name="next-steps"></a>다음 단계
이 문서에서는 내부 기본 부하 분산 장치를 만들고, 여기에 VM을 연결하고, 부하 분산 장치 트래픽 규칙 및 상태 프로브를 구성한 다음, 부하 분산 장치를 테스트했습니다. 부하 분산 장치 및 관련 리소스에 대해 자세히 알아보려면 방법 문서를 참조하세요.
