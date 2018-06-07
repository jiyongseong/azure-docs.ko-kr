---
title: '빠른 시작: Azure Application Gateway를 통해 웹 트래픽 보내기 - Azure CLI | Microsoft Docs'
description: Azure CLI를 사용하여 백 엔드 풀의 가상 머신에 웹 트래픽을 보내는 Azure Application Gateway를 만드는 방법을 알아봅니다.
services: application-gateway
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: azurecli
ms.topic: quickstart
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 99c3975c6ab2c7a20dfbab519dae575a2a61465f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>빠른 시작: Azure Application Gateway를 통해 웹 트래픽 보내기 - Azure CLI

Azure Application Gateway를 통해 수신기를 포트에 할당하고, 규칙을 만들고, 백 엔드 풀에 리소스를 추가하여 응용 프로그램 웹 트래픽을 특정 리소스로 보낼 수 있습니다.

이 빠른 시작에서는 Azure CLI를 사용하여 백 엔드 풀에 가상 머신 두 개가 있는 응용 프로그램 게이트웨이를 신속하게 만드는 방법을 보여줍니다. 그런 다음, 올바르게 작동하는지 테스트합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하려면 이 빠른 시작에서 Azure CLI 버전 2.0.4 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

항상 리소스 그룹에 리소스를 만들어야 합니다. [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다. 

다음 예제에서는 *eastus* 위치에 *myResourceGroupAG*라는 리소스 그룹을 만듭니다.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>네트워크 리소스 만들기 

응용 프로그램 게이트웨이가 다른 리소스와 통신할 수 있도록 가상 네트워크를 만들어야 합니다. 응용 프로그램 게이트웨이를 만드는 동시에 가상 네트워크를 만들 수 있습니다. 이 예제에서는 두 개의 서브넷을 만듭니다. 하나는 응용 프로그램 게이트웨이용이고, 다른 하나는 가상 머신용입니다. 

[az network vnet create](/cli/azure/vnet#az_vnet_create)를 사용하여 가상 네트워크 및 서브넷을 만듭니다. [az network public-ip create](/cli/azure/public-ip#az_public_ip_create)를 사용하여 공용 IP 주소를 만듭니다.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-backend-servers"></a>백 엔드 서버 만들기

이 예제에서는 응용 프로그램 게이트웨이에 대한 백 엔드 서버로 사용할 두 개의 가상 머신을 만듭니다. 

### <a name="create-two-virtual-machines"></a>두 개의 가상 머신 만들기

또한 응용 프로그램 게이트웨이가 성공적으로 만들어 졌는지 확인하기 위해 가상 머신에 NGINX를 설치합니다. cloud-init 구성 파일을 사용하여 NGINX를 설치하고 Linux 가상 머신에서 'Hello World' Node.js 앱을 실행할 수 있습니다. 

현재 셸에서 cloud-init.txt라는 파일을 만들고 다음 구성을 복사하여 셸에 붙여넣습니다. 전체 cloud-init 파일, 특히 첫 줄이 올바르게 복사해야 합니다.

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

[az network nic create](/cli/azure/network/nic#az_network_nic_create)를 사용하여 네트워크 인터페이스를 만듭니다. [az vm create](/cli/azure/vm#az_vm_create)를 사용하여 가상 머신을 만듭니다.

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupAG \
    --name myNic$i \
    --vnet-name myVNet \
    --subnet myBackendSubnet
  az vm create \
    --resource-group myResourceGroupAG \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
done
```

## <a name="create-the-application-gateway"></a>Application Gateway 만들기

[az network application-gateway create](/cli/azure/application-gateway#az_application_gateway_create)를 사용하여 응용 프로그램 게이트웨이를 만듭니다. Azure CLI를 사용하여 응용 프로그램 게이트웨이를 만들 때 용량, sku, HTTP 설정 등의 구성 정보를 지정합니다. 네트워크 인터페이스의 개인 IP 주소는 응용 프로그램 게이트웨이의 백 엔드 풀에 서버로 추가됩니다.

```azurecli-interactive
address1=$(az network nic show --name myNic1 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
address2=$(az network nic show --name myNic2 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Enabled \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$address1" "$address2"
```

응용 프로그램 게이트웨이가 생성될 때까지 최대 30분이 걸릴 수 있습니다. 응용 프로그램 게이트웨이가 생성되면 다음과 같은 기능을 볼 수 있습니다.

- *appGatewayBackendPool* - 응용 프로그램 게이트웨이에 백 엔드 주소 풀이 하나 이상 있어야 합니다.
- *appGatewayBackendHttpSettings* - 포트 80 및 HTTP 프로토콜을 통신에 사용하도록 지정합니다.
- *appGatewayHttpListener* - *appGatewayBackendPool*에 연결되는 기본 수신기입니다.
- *appGatewayFrontendIP* - *myAGPublicIPAddress*를 *appGatewayHttpListener*에 할당합니다.
- *rule1* - *appGatewayHttpListener*에 연결되는 기본 라우팅 규칙입니다.

## <a name="test-the-application-gateway"></a>응용 프로그램 게이트웨이 테스트

응용 프로그램 게이트웨이를 만들기 위해 반드시 NGINX를 설치해야 하는 것은 아니지만, 이 빠른 시작에서는 응용 프로그램 게이트웨이가 성공적으로 생성되었는지 확인하기 위해 설치했습니다. 응용 프로그램 게이트웨이의 공용 IP 주소를 가져오려면 [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show)를 사용합니다. 공용 IP 주소를 복사하여 브라우저의 주소 표시줄에 붙여넣습니다.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
``` 

![응용 프로그램 게이트웨이 테스트](./media/quick-create-cli/application-gateway-nginxtest.png)

브라우저를 새로 고치면 다른 VM의 이름이 표시되어야 합니다.

## <a name="clean-up-resources"></a>리소스 정리

먼저 응용 프로그램 게이트웨이로 만든 리소스를 탐색한 다음, 더 이상 필요 없으면 [az group delete](/cli/azure/group#az_group_delete) 명령을 사용하여 리소스 그룹, 응용 프로그램 게이트웨이 및 모든 관련 리소스를 제거할 수 있습니다.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```
 
## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure CLI를 사용하여 응용 프로그램 게이트웨이로 웹 트래픽 관리](./tutorial-manage-web-traffic-cli.md)

