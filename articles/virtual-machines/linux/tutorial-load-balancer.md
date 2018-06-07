---
title: 자습서 - Azure의 Linux 가상 머신 부하 분산 | Microsoft Docs
description: 이 자습서에서는 Azure CLI 2.0을 사용하여 세 Linux 가상 머신에서 고가용성의 안전한 응용 프로그램을 위한 부하 분산 장치를 만드는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/13/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: baae0ee72056d2f7437a865b11f738ef0a2e6934
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-load-balance-linux-virtual-machines-in-azure-to-create-a-highly-available-application-with-the-azure-cli-20"></a>자습서: Azure CLI 2.0으로 Azure의 Linux 가상 머신 부하를 분산하여 고가용성 응용 프로그램 만들기

부하 분산은 들어오는 요청을 여러 가상 머신에 분산하여 높은 수준의 가용성을 제공합니다. 이 자습서에서는 트래픽을 분산하고 고가용성을 제공하는 Azure Load Balancer의 여러 다른 구성 요소에 대해 알아봅니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Load Balancer 만들기
> * 부하 분산 장치 상태 프로브 만들기
> * 부하 분산 장치 트래픽 규칙 만들기
> * cloud-init를 사용하여 기본 Node.js 앱 만들기
> * 가상 머신 만들기 및 부하 분산 장치에 연결
> * 부하 분산 장치의 실제 동작 보기
> * 부하 분산 장치에서 VM 추가 및 제거

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에서는 Azure CLI 버전 2.0.30 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="azure-load-balancer-overview"></a>Azure Load Balancer 개요
Azure Load Balancer는 들어오는 트래픽을 정상 VM 간에 분산하여 고가용성을 제공하는 계층 4(TCP, UDP) 부하 분산 장치입니다. 부하 분산 장치 상태 프로브가 각 VM에서 지정된 포트를 모니터링하고 작동하는 VM으로만 트래픽을 분산합니다.

하나 이상의 공용 IP 주소를 포함하는 프런트 엔드 IP 구성을 정의할 수 있습니다. 이 프런트 엔드 IP 구성을 사용하여 인터넷을 통해 부하 분산 장치 및 응용 프로그램에 액세스하도록 할 수 있습니다. 

가상 머신은 가상 NIC(네트워크 인터페이스 카드)를 사용하여 부하 분산 장치에 연결합니다. VM으로 트래픽을 분산하기 위해 백 엔드 주소 풀에 부하 분산 장치에 연결된 가상(NIC)의 주소가 포함됩니다.

트래픽 흐름을 제어하려면 VM에 매핑되는 특정 포트 및 프로토콜에 대해 부하 분산 장치 규칙을 정의합니다.

이전 자습서를 따라 [가상 머신 확장 집합을 만든 경우](tutorial-create-vmss.md) 부하 분산 장치가 생성되었을 것입니다. 이러한 모든 구성 요소는 확장 집합의 일부로 구성되었습니다.


## <a name="create-azure-load-balancer"></a>Azure Load Balancer 만들기
이 섹션에서는 부하 분산 장치의 각 구성 요소를 만들고 구성하는 방법을 자세히 설명합니다. 부하 분산 장치를 만들려면 먼저 [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroupLoadBalancer*라는 리소스 그룹을 만듭니다.

```azurecli-interactive 
az group create --name myResourceGroupLoadBalancer --location eastus
```

### <a name="create-a-public-ip-address"></a>공용 IP 주소 만들기
인터넷에서 앱에 액세스하려면 부하 분산 장치에 대한 공용 IP 주소가 필요합니다. [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)를 사용하여 공용 IP 주소를 만듭니다. 다음 예제에서는 *myResourceGroupLoadBalancer* 리소스 그룹에 *myPublicIP*라는 공용 IP 주소를 만듭니다.

```azurecli-interactive 
az network public-ip create \
    --resource-group myResourceGroupLoadBalancer \
    --name myPublicIP
```

### <a name="create-a-load-balancer"></a>부하 분산 장치 만들기
[az network lb create](/cli/azure/network/lb#az_network_lb_create)를 사용하여 부하 분산 장치를 만듭니다. 다음 예제에서는 *myLoadBalancer*라는 부하 분산 장치를 만들고 *myPublicIP* 주소를 프런트 엔드 IP 구성에 할당합니다.

```azurecli-interactive 
az network lb create \
    --resource-group myResourceGroupLoadBalancer \
    --name myLoadBalancer \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --public-ip-address myPublicIP
```

### <a name="create-a-health-probe"></a>상태 프로브 만들기
부하 분산 장치가 앱의 상태를 모니터링하도록 하려면 상태 프로브를 사용합니다. 상태 프로브는 상태 검사에 따라 부하 분산 장치 순환에서 VM을 동적으로 추가하거나 제거합니다. 기본적으로 VM은 15초 간격으로 두 번의 연속 실패 후에 부하 분산 장치 분산에서 제거됩니다. 앱의 프로토콜 또는 특정 상태 확인 페이지에 따라 상태 프로브를 만듭니다. 

다음 예제에서는 TCP 프로브를 만듭니다. 좀 더 미세 조정된 상태 검사를 위해 사용자 지정 HTTP 프로브를 만들 수도 있습니다. 사용자 지정 HTTP 프로브를 사용할 경우 *healthcheck.js*와 같은 상태 확인 페이지를 만들어야 합니다. 부하 분산 상태가 호스트를 순환 상태를 유지하려면 프로브는 **HTTP 200 정상** 응답을 반환해야 합니다.

TCP 상태 프로브를 만들려면 [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create)를 사용합니다. 다음 예제에서는 *myHealthProbe*라는 상태 프로브를 만듭니다.

```azurecli-interactive 
az network lb probe create \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-a-load-balancer-rule"></a>부하 분산 장치 규칙 만들기
부하 분산 장치 규칙은 VM으로 트래픽이 분산되는 방법을 정의하는 데 사용됩니다. 들어오는 트래픽에 대한 프런트 엔드 IP 구성 및 트래픽을 수신할 백 엔드 IP 풀과 필요한 원본 및 대상 포트를 함께 정의합니다. 정상 VM만 트래픽을 수신하도록 하려면 사용할 상태 프로브도 정의합니다.

[az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create)를 사용하여 부하 분산 장치 규칙을 만듭니다. 다음 예제에서는 *myLoadBalancerRule*이라는 규칙을 만들고, *myHealthProbe* 상태 프로브를 사용하고, 포트 *80*에서 트래픽 부하를 분산합니다.

```azurecli-interactive 
az network lb rule create \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myLoadBalancerRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```


## <a name="configure-virtual-network"></a>가상 네트워크 구성
일부 VM을 배포하고 부하 분산 장치를 테스트하려면 지원하는 가상 네트워크 리소스를 만듭니다. 가상 네트워크에 대한 자세한 내용은 [Azure Virtual Network 관리](tutorial-virtual-network.md) 자습서를 참조하세요.

### <a name="create-network-resources"></a>네트워크 리소스 만들기
[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create)를 사용하여 가상 네트워크를 만듭니다. 다음 예제에서는 *myVnet*이라는 가상 네트워크와 *mySubnet*이라는 서브넷을 만듭니다.

```azurecli-interactive 
az network vnet create \
    --resource-group myResourceGroupLoadBalancer \
    --name myVnet \
    --subnet-name mySubnet
```

네트워크 보안 그룹을 추가하려면 [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create)를 사용합니다. 다음 예제에서는 *myNetworkSecurityGroup*이라는 네트워크 보안 그룹을 만듭니다.

```azurecli-interactive 
az network nsg create \
    --resource-group myResourceGroupLoadBalancer \
    --name myNetworkSecurityGroup
```

[az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create)를 사용하여 네트워크 보안 그룹 규칙을 만듭니다. 다음 예제에서는 *myNetworkSecurityGroupRule*이라는 네트워크 보안 그룹 규칙을 만듭니다.

```azurecli-interactive 
az network nsg rule create \
    --resource-group myResourceGroupLoadBalancer \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --priority 1001 \
    --protocol tcp \
    --destination-port-range 80
```

가상 NIC는 [az network nic create](/cli/azure/network/nic#az_network_nic_create)를 사용하여 만듭니다. 다음 예제에서는 3개의 가상 NIC를 만듭니다. (다음 단계에서 앱에 대해 만드는 각 VM에 대해 가상 NIC 하나씩) 언제든지 추가 가상 NIC 및 VM을 만든 후 부하 분산 장치에 추가할 수 있습니다.

```bash
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupLoadBalancer \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```

세 가상 NIC를 모두 만들었으면 다음 단계를 진행합니다.


## <a name="create-virtual-machines"></a>가상 머신 만들기

### <a name="create-cloud-init-config"></a>cloud-init 구성 만들기
[처음 부팅 시 Linux 가상 머신을 사용자 지정하는 방법](tutorial-automate-vm-deployment.md)에 대한 이전 자습서에서 cloud-init를 사용하여 VM 사용자 지정을 자동화하는 방법을 배웠습니다. 다음 단계에서는 동일한 cloud-init 구성 파일을 사용하여 NGINX를 설치하고 간단한 'Hello World' Node.js 앱을 실행할 수 있습니다. 작동 중인 부하 분산 장치를 보기 위해 이 자습서의 끝 부분에서 웹 브라우저를 통해 이 간단한 앱에 액세스하게 됩니다.

현재 셸에서 *cloud-init.txt*라는 파일을 만들고 다음 구성을 붙여 넣습니다. 예를 들어 로컬 컴퓨터에 없는 Cloud Shell에서 파일을 만듭니다. `sensible-editor cloud-init.txt`를 입력하여 파일을 만들고 사용할 수 있는 편집기의 목록을 봅니다. 전체 cloud-init 파일, 특히 첫 줄이 올바르게 복사되었는지 확인합니다.

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

### <a name="create-virtual-machines"></a>가상 머신 만들기
앱의 고가용성을 향상시키려면 VM을 가용성 집합에 배치합니다. 가용성 집합에 대한 자세한 내용은 이전 [고가용성 가상 머신을 만드는 방법](tutorial-availability-sets.md) 자습서를 참조하세요.

[az vm availability-set create](/cli/azure/vm/availability-set#az_vm_availability_set_create)를 사용하여 가용성 집합을 만듭니다. 다음 예제는 *myAvailabilitySet*이라는 가용성 집합을 만듭니다.

```azurecli-interactive 
az vm availability-set create \
    --resource-group myResourceGroupLoadBalancer \
    --name myAvailabilitySet
```

이제 [az vm create](/cli/azure/vm#az_vm_create)로 VM을 만들 수 있습니다. 다음 예제에서는 3개의 VM을 만들고 SSH 키가 아직 없으면 생성합니다.

```bash
for i in `seq 1 3`; do
    az vm create \
        --resource-group myResourceGroupLoadBalancer \
        --name myVM$i \
        --availability-set myAvailabilitySet \
        --nics myNic$i \
        --image UbuntuLTS \
        --admin-username azureuser \
        --generate-ssh-keys \
        --custom-data cloud-init.txt \
        --no-wait
done
```

Azure CLI에서 프롬프트로 반환한 후 실행을 계속하는 백그라운드 작업이 있습니다. `--no-wait` 매개 변수는 모든 작업이 완료되기를 기다리지 않습니다. 앱에 액세스하려면 몇 분이 걸릴 수 있습니다. 부하 분산 장치 상태 프로브는 각 VM에서 앱을 실행될 경우를 자동으로 검색합니다. 앱이 실행되면 부하 분산 장치 규칙은 트래픽을 분산하기 시작합니다.


## <a name="test-load-balancer"></a>부하 분산 장치 테스트
[az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show)를 사용하여 부하 분산 장치의 공용 IP 주소를 가져옵니다. 다음 예제에서는 앞서 만든 *myPublicIP*의 IP 주소를 가져옵니다.

```azurecli-interactive 
az network public-ip show \
    --resource-group myResourceGroupLoadBalancer \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```

그런 다음 웹 브라우저에 공용 IP 주소를 입력할 수 있습니다. 부하 분산 장치가 트래픽을 분산하도록 시작하기 전에 VM이 준비하는 데 몇 분 정도 걸립니다. 다음 예제와 같이 부하 분산 장치가 트래픽을 분산한 VM의 호스트 이름을 포함하여 앱이 표시됩니다.

![Node.js 앱 실행](./media/tutorial-load-balancer/running-nodejs-app.png)

앱이 실행되는 3개의 모든 VM에서 부하 분산 장치가 트래픽을 분산하는 것을 확인하기 위해 웹 브라우저를 강제로 새로 고칠 수 있습니다.


## <a name="add-and-remove-vms"></a>VM 추가 및 제거
앱이 실행되는 동안 OS 업데이트와 같은 유지 관리 작업을 VM에서 수행해야 할 수 있습니다. 앱에 대해 증가된 트래픽을 처리하기 위해 VM을 더 추가해야 할 수 있습니다. 이 섹션에서는 부하 분산 장치에서 VM을 추가 또는 제거하는 방법을 보여 줍니다.

### <a name="remove-a-vm-from-the-load-balancer"></a>부하 분산 장치에서 VM 제거
[az network nic ip-config address-pool remove](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_remove)를 사용하여 백 엔드 주소 풀에서 VM을 제거할 수 있습니다. 다음 예제에서는 *myLoadBalancer*에서 **myVM2**용 가상 NIC를 제거합니다.

```azurecli-interactive 
az network nic ip-config address-pool remove \
    --resource-group myResourceGroupLoadBalancer \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool 
```

앱이 실행되는 나머지 2개의 VM에서 부하 분산 장치가 트래픽을 분산하는 것을 확인하기 위해 웹 브라우저를 강제로 새로 고칠 수 있습니다. 이제 OS 업데이트 설치 또는 VM 다시 부팅을 수행 등의 유지 관리 작업을 VM에서 수행할 수 있습니다.

부하 분산 장치에 연결된 가상 NIC가 있는 VM 목록을 보기 위해 [az network lb address-pool show](/cli/azure/network/lb/address-pool#az_network_lb_address_pool_show)를 사용합니다. 가상 NIC의 ID에 대해 다음과 같이 쿼리 및 필터링합니다.

```azurecli-interactive
az network lb address-pool show \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myBackEndPool \
    --query backendIpConfigurations \
    --output tsv | cut -f4
```

출력은 다음 예제와 유사합니다. 여기서 VM 2의 가상 NIC는 더 이상 백엔드 주소 풀의 일부가 아닙니다.

```bash
/subscriptions/<guid>/resourceGroups/myResourceGroupLoadBalancer/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/ipconfig1
/subscriptions/<guid>/resourceGroups/myResourceGroupLoadBalancer/providers/Microsoft.Network/networkInterfaces/myNic3/ipConfigurations/ipconfig1
```

### <a name="add-a-vm-to-the-load-balancer"></a>부하 분산 장치에 VM 추가
VM 유지 관리를 수행한 이후 또는 용량을 확장해야 할 경우 [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add)를 사용하여 백 엔드 주소 풀에 VM을 추가할 수 있습니다. 다음 예제에서는 *myLoadBalancer*에서 **myVM2**용 가상 NIC를 제거합니다.

```azurecli-interactive 
az network nic ip-config address-pool add \
    --resource-group myResourceGroupLoadBalancer \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool
```

가상 NIC가 백엔드 주소 풀에 연결되었는지 확인하려면 앞 단계의 [az network lb address-pool show](/cli/azure/network/lb/address-pool#az_network_lb_address_pool_show)를 다시 사용합니다. 


## <a name="next-steps"></a>다음 단계
이 자습서에서는 부하 분산 장치를 만들고 VM에 연결했습니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure Load Balancer 만들기
> * 부하 분산 장치 상태 프로브 만들기
> * 부하 분산 장치 트래픽 규칙 만들기
> * cloud-init를 사용하여 기본 Node.js 앱 만들기
> * 가상 머신 만들기 및 부하 분산 장치에 연결
> * 부하 분산 장치의 실제 동작 보기
> * 부하 분산 장치에서 VM 추가 및 제거

다음 자습서에서는 Azure Virtual Network 구성 요소에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [VM 및 가상 네트워크 관리](tutorial-virtual-network.md)
