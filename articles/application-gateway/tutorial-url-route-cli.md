---
title: 자습서 - URL을 기반으로 웹 트래픽 라우팅 - Azure CLI
description: Azure CLI를 사용하여 URL을 기반으로 웹 트래픽을 확장 가능한 특정 서버 풀로 라우팅하는 방법을 알아봅니다.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 4/27/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 411baa24cc796a40f83e8530ab797f3e377ee5f6
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34356242"
---
# <a name="tutorial-route-web-traffic-based-on-the-url-using-the-azure-cli"></a>자습서: Azure CLI를 사용하여 URL을 기반으로 웹 트래픽 라우팅

Azure CLI를 사용하여 응용 프로그램 액세스에 사용되는 URL을 기반으로 확장 가능한 특정 서버 풀로 웹 트래픽 라우팅을 구성할 수 있습니다. 이 자습서에서는 [가상 머신 확장 집합](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)을 사용하여 백 엔드 풀이 3개 있는 [Azure Application Gateway](application-gateway-introduction.md)를 만듭니다. 각 백 엔드 풀은 공통 데이터, 이미지 및 비디오와 같은 특정 목적을 갖습니다.  트래픽을 별도의 풀에 라우팅하면 고객이 필요할 때 필요한 정보를 얻을 수 있습니다.

트래픽을 라우팅하려면 웹 트래픽이 풀의 올바른 서버에 도착하도록 특정 포트에서 수신 대기하는 수신기에 할당되는 [라우팅 규칙](application-gateway-url-route-overview.md)을 만듭니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 네트워크 설정
> * 수신기, URL 경로 맵 및 규칙 만들기
> * 확장 가능한 백 엔드 풀 만들기


![URL 라우팅 예제](./media/tutorial-url-route-cli/scenario.png)

원하는 경우 [Azure PowerShell](tutorial-url-route-powershell.md)을 사용하여 이 자습서를 완료할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하기로 선택할 경우 이 자습서에서 Azure CLI 버전 2.0.4 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. [az group create](/cli/azure/group#create)를 사용하여 리소스 그룹을 만듭니다.

다음 예제에서는 *eastus* 위치에 *myResourceGroupAG*라는 리소스 그룹을 만듭니다.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>네트워크 리소스 만들기 

[az network vnet create](/cli/azure/network/vnet#az_net)를 사용하여 *myVNet*이라는 가상 네트워크와 *myAGSubnet*이라는 서브넷을 만듭니다. 그런 후 [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)를 사용하여 백 엔드 서버에 필요한 *myBackendSubnet*이라는 서브넷을 추가합니다. [az network public-ip create](/cli/azure/public-ip#az_network_public_ip_create)를 사용하여 *myAGPublicIPAddress*라는 IP 주소를 만듭니다.

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
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24

az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-the-application-gateway-with-url-map"></a>URL 맵을 사용하여 응용 프로그램 게이트웨이 만들기

[az network application-gateway create](/cli/azure/application-gateway#create)를 사용하여 *myAppGateway*라는 응용 프로그램 게이트웨이를 만듭니다. Azure CLI를 사용하여 응용 프로그램 게이트웨이를 만들 때 용량, sku, HTTP 설정 등의 구성 정보를 지정합니다. 응용 프로그램 게이트웨이는 앞에서 만든 *myAGSubnet* 및 *myAGPublicIPAddress*에 할당됩니다. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

 응용 프로그램 게이트웨이를 만들 때까지 몇 분 정도 걸릴 수 있습니다. 응용 프로그램 게이트웨이가 생성되면 다음과 같은 새 기능을 볼 수 있습니다.

- *appGatewayBackendPool* - 응용 프로그램 게이트웨이에 백 엔드 주소 풀이 하나 이상 있어야 합니다.
- *appGatewayBackendHttpSettings* - 포트 80 및 HTTP 프로토콜을 통신에 사용하도록 지정합니다.
- *appGatewayHttpListener* - *appGatewayBackendPool*에 연결되는 기본 수신기입니다.
- *appGatewayFrontendIP* - *myAGPublicIPAddress*를 *appGatewayHttpListener*에 할당합니다.
- *rule1* - *appGatewayHttpListener*에 연결되는 기본 라우팅 규칙입니다.


### <a name="add-image-and-video-backend-pools-and-port"></a>이미지와 비디오 백 엔드 풀 및 포트 추가

[az network application-gateway address-pool create](/cli/azure/application-gateway#az_network_application_gateway_address-pool_create)를 사용하여 응용 프로그램 게이트웨이에 *imagesBackendPool* 및 *videoBackendPool*이라는 백 엔드 풀을 추가합니다. [az network application-gateway frontend-port create](/cli/azure/application-gateway#az_network_application_gateway_frontend_port_create)를 사용하여 풀에 프런트 엔드 포트를 추가합니다. 

```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name imagesBackendPool

az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name videoBackendPool

az network application-gateway frontend-port create \
  --port 8080 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name port8080
```

### <a name="add-backend-listener"></a>백 엔드 수신기 추가

[az network application-gateway http-listener create](/cli/azure/application-gateway#az_network_application_gateway_http_listener_create)를 사용하여 트래픽을 라우팅하는 데 필요한 *backendListener*라는 백 엔드 수신기를 추가합니다.


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port port8080 \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-url-path-map"></a>URL 경로 맵 추가

URL 경로 맵은 특정 URL이 특정 백 엔드 풀로 라우팅되도록 보장합니다. [az network application-gateway url-path-map create](/cli/azure/application-gateway#az_network_application_gateway_url_path_map_create) 및 [az network application-gateway url-path-map rule create](/cli/azure/application-gateway#az_network_application_gateway_url_path_map_rule_create)를 사용하여 *imagePathRule* 및 *videoPathRule*이라는 URL 경로 맵을 만듭니다.

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name myPathMap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --address-pool imagesBackendPool \
  --default-address-pool appGatewayBackendPool \
  --default-http-settings appGatewayBackendHttpSettings \
  --http-settings appGatewayBackendHttpSettings \
  --rule-name imagePathRule

az network application-gateway url-path-map rule create \
  --gateway-name myAppGateway \
  --name videoPathRule \
  --resource-group myResourceGroupAG \
  --path-map-name myPathMap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-routing-rule"></a>라우팅 규칙 추가

라우팅 규칙은 URL 맵을 만든 수신기에 연결합니다. [az network application-gateway rule create](/cli/azure/application-gateway#az_network_application_gateway_rule_create)를 사용하여 *rule2*라는 규칙을 추가합니다.

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name rule2 \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map myPathMap \
  --address-pool appGatewayBackendPool
```

## <a name="create-virtual-machine-scale-sets"></a>가상 머신 확장 집합 만들기

이 자습서에서는 사용자가 만든 세 백 엔드 풀을 지원하는 세 개의 가상 머신 확장 집합을 만듭니다. 사용자가 만든 확장 집합의 이름은 *myvmss1*, *myvmss2*, *myvmss3*입니다. 각 확장 집합에는 NGINX를 설치하는 두 개의 가상 머신 인스턴스가 포함됩니다.

```azurecli-interactive
for i in `seq 1 3`; do

  if [ $i -eq 1 ]
  then
    poolName="appGatewayBackendPool" 
  fi

  if [ $i -eq 2 ]
  then
    poolName="imagesBackendPool"
  fi

  if [ $i -eq 3 ]
  then
    poolName="videoBackendPool"
  fi

  az vmss create \
    --name myvmss$i \
    --resource-group myResourceGroupAG \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password Azure123456! \
    --instance-count 2 \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --vm-sku Standard_DS2 \
    --upgrade-policy-mode Automatic \
    --app-gateway myAppGateway \
    --backend-pool-name $poolName
done
```

### <a name="install-nginx"></a>NGINX 설치

```azurecli-interactive
for i in `seq 1 3`; do
  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/davidmu1/samplescripts/master/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="test-the-application-gateway"></a>응용 프로그램 게이트웨이 테스트

응용 프로그램 게이트웨이의 공용 IP 주소를 가져오려면 [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show)를 사용합니다. 공용 IP 주소를 복사하여 브라우저의 주소 표시줄에 붙여넣습니다. 예: *http://40.121.222.19*, *http://40.121.222.19:8080/images/test.htm* 또는 *http://40.121.222.19:8080/video/test.htm*.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![응용 프로그램 게이트웨이의 기준 URL 테스트](./media/tutorial-url-route-cli/application-gateway-nginx.png)

URL을 http://&lt;ip-address&gt;:8080/video/test.html로 변경하고 &lt;ip-address&gt;를 사용자의 IP 주소로 대체하면 다음 예제와 같은 내용이 표시됩니다.

![응용 프로그램 게이트웨이의 이미지 URL 테스트](./media/tutorial-url-route-cli/application-gateway-nginx-images.png)

URL을 http://&lt;ip-address&gt;:8080/video/test.html로 변경하고 &lt;ip-address&gt;를 사용자의 IP 주소로 대체하면 다음 예제와 같은 내용이 표시됩니다.

![응용 프로그램 게이트웨이의 비디오 URL 테스트](./media/tutorial-url-route-cli/application-gateway-nginx-video.png)

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 없는 리소스 그룹, 응용 프로그램 게이트웨이 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name myResourceGroupAG --location eastus
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 네트워크 설정
> * 수신기, URL 경로 맵 및 규칙 만들기
> * 확장 가능한 백 엔드 풀 만들기

> [!div class="nextstepaction"]
> [URL 경로 기반 리디렉션으로 응용 프로그램 게이트웨이 만들기](./tutorial-url-redirect-cli.md)
