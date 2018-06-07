---
title: '빠른 시작: 공용 표준 부하 분산 장치 만들기 - Azure CLI | Microsoft Docs'
description: 이 빠른 시작은 Azure CLI를 사용하여 공용 부하 분산 장치를 만드는 방법을 보여줍니다.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a Standard Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: a8bcdd88-f94c-4537-8143-c710eaa86818
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 48ed4270838a32c8060bf96a63fcfdd8c65f9689
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34305075"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-using-azure-cli-20"></a>빠른 시작: Azure CLI 2.0을 사용하여 VM 부하를 분산하는 표준 부하 분산 장치 만들기

이 빠른 시작에서는 표준 부하 분산 장치를 만드는 방법을 보여줍니다. 부하 분산 장치를 테스트하려면 Ubuntu 서버를 실행하는 두 VM(가상 머신)을 배포하고 두 VM 사이에 있는 웹앱의 부하를 분산합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

CLI를 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에서는 Azure CLI 버전 2.0.28 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](https://docs.microsoft.com/cli/azure/group#create)를 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

다음 예제에서는 *eastus* 위치에 *myResourceGroupSLB*라는 리소스 그룹을 만듭니다.

```azurecli-interactive
  az group create \
    --name myResourceGroupSLB \
    --location eastus
```

## <a name="create-a-public-standard-ip-address"></a>공용 표준 IP 주소 만들기

인터넷에서 웹앱에 액세스하려면 부하 분산 장치에 대한 공용 IP 주소가 필요합니다. 표준 부하 분산 장치는 표준 공용 IP 주소만 지원합니다. [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#create) 명령을 사용하여 *myResourceGroupLB*에 *myPublicIP*라는 표준 공용 IP 주소를 만듭니다.

```azurecli-interactive
  az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku standard
```

## <a name="create-azure-load-balancer"></a>Azure Load Balancer 만들기

이 섹션에서는 다음과 같은 부하 분산 장치 구성 요소를 만들고 구성하는 방법에 대해 자세히 설명합니다.
  - 부하 분산 장치에서 들어오는 네트워크 트래픽을 수신하는 프런트 엔드 IP 풀.
  - 프런트 엔드 풀에서 부하 분산된 네트워크 트래픽을 전송하는 백 엔드 IP 풀.
  - 백 엔드 VM 인스턴스의 상태를 확인하는 상태 프로브
  - 트래픽이 VM에 분산되는 방법을 정의하는 부하 분산 장치 규칙

### <a name="create-the-load-balancer"></a>부하 분산 장치 만들기

[az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#create) 명령을 사용하여 **myFrontEnd**라는 프런트 엔드 풀, 이전 단계에서 만든 공용 IP 주소 **myPublicIP**와 연결된 **myBackEndPool**이라는 백 엔드 풀을 포함하는 **myLoadBalancer**라는 공용 Azure Load Balancer를 만듭니다.

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupSLB \
    --name myLoadBalancer \
    --sku standard
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
  ```

### <a name="create-the-health-probe"></a>상태 프로브 만들기

상태 프로브는 네트워크 트래픽을 보낼 수 있도록 모든 가상 컴퓨터 인스턴스를 검사합니다. 프로브 검사에 실패한 가상 머신 인스턴스는 다시 온라인 상태가 되어 프로브 검사가 정상으로 나올 때까지 부하 분산 장치에서 제거됩니다. [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#create)를 사용하여 가상 머신의 상태를 모니터링하는 상태 프로브를 만듭니다. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupSLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>부하 분산 장치 규칙 만들기

부하 분산 장치 규칙은 들어오는 트래픽에 대한 프런트 엔드 IP 구성 및 트래픽을 받을 백 엔드 IP 풀과 필요한 원본 및 대상 포트를 함께 정의합니다. [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#create) 명령을 사용하여 프런트 엔드 풀 *myFrontEnd*의 포트 80에서 수신 대기하고 역시 포트 80을 사용하여 백 엔드 주소 풀 *myBackEndPool*에 부하 분산된 네트워크 트래픽을 보내는 *myLoadBalancerRuleWeb*이라는 부하 분산 장치 규칙을 만듭니다. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupSLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```

## <a name="configure-virtual-network"></a>가상 네트워크 구성

일부 VM을 배포하고 부하 분산 장치를 테스트하려면, 먼저 지원되는 가상 네트워크 리소스를 만듭니다.

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기

[az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#create)를 사용하여 *myResourceGroup*에 *mySubnet*이라는 서브넷이 있는 *myVnet* 가상 네트워크를 만듭니다.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupSLB \
    --location eastus \
    --name myVnet \
    --subnet-name mySubnet
```
###  <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기

표준 부하 분산 장치의 경우 네트워크 보안 그룹에 속한 NIC가 백 엔드 주소의 VM에 있어야 합니다. 가상 네트워크에 대한 인바운드 연결을 정의하는 네트워크 보안 그룹을 만듭니다.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupSLB \
    --name myNetworkSecurityGroup
```

### <a name="create-a-network-security-group-rule"></a>네트워크 보안 그룹 규칙 만들기

포트 80을 통한 인바운드 연결을 허용하는 네트워크 보안 그룹 규칙을 만듭니다.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupSLB \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleHTTP \
    --protocol tcp \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```
### <a name="create-nics"></a>NIC 만들기

[az network nic create](/cli/azure/network/nic#az_network_nic_create) 명령을 사용하여 3개의 네트워크 인터페이스를 만들고 공용 IP 주소 및 네트워크 보안 그룹에 연결합니다. 

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNic$i \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
done
```


## <a name="create-backend-servers"></a>백 엔드 서버 만들기

이 예제에서는 부하 분산 장치의 백 엔드 서버로 사용될 세 개의 가상 머신을 만듭니다. 또한 부하 분산 장치가 성공적으로 만들어졌는지 확인하기 위해 가상 머신에 NGINX를 설치합니다.

### <a name="create-an-availability-set"></a>가용성 집합 만들기

[az vm availabilityset create](/cli/azure/network/nic#az_network_availabilityset_create)를 사용하여 가용성 집합을 만듭니다.

 ```azurecli-interactive
  az vm availability-set create \
    --resource-group myResourceGroupSLB \
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
    --resource-group myResourceGroupSLB \
    --name myVM$i \
    --availability-set myAvailabilitySet \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
    --no-wait
    done
```
VM을 배포하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="test-the-load-balancer"></a>부하 분산 장치 테스트

부하 분산 장치의 공용 IP 주소를 가져오려면 [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) 명령을 사용합니다. 공용 IP 주소를 복사하여 브라우저의 주소 표시줄에 붙여넣습니다.

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
   ![부하 분산 장치 테스트](./media/load-balancer-standard-public-cli/running-nodejs-app.png)

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az_group_delete) 명령을 사용하여 리소스 그룹, 부하 분산 장치 및 모든 관련 리소스를 제거할 수 있습니다.

```azurecli-interactive 
  az group delete --name myResourceGroupSLB
```
## <a name="next-step"></a>다음 단계
이 빠른 시작에서는 표준 Load Balancer를 만들고, 거기에 VM을 연결하고, 부하 분산 장치 트래픽 규칙 및 상태 프로브를 구성한 다음, 부하 분산 장치를 테스트했습니다. Azure Load Balancer에 대해 자세히 알아보려면 Azure Load Balancer에 대한 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [Azure Load Balancer 자습서](tutorial-load-balancer-standard-public-zone-redundant-portal.md)

