---
title: 여러 사이트를 호스팅하는 응용 프로그램 게이트웨이 만들기 - Azure CLI | Microsoft Docs
description: Azure CLI를 사용하여 여러 사이트를 호스팅하는 응용 프로그램 게이트웨이를 만드는 방법을 알아봅니다.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: victorh
ms.openlocfilehash: 7728ade5f84cd97f3ff0cebd0d5545d68d0ebd79
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="create-an-application-gateway-with-multiple-site-hosting-using-the-azure-cli"></a>Azure CLI를 사용하여 여러 사이트를 호스팅하는 응용 프로그램 게이트웨이 만들기

Azure CLI를 사용하여 [응용 프로그램 게이트웨이](application-gateway-multi-site-overview.md)를 만들 때 [여러 웹 사이트의 호스팅](application-gateway-introduction.md)을 구성할 수 있습니다. 이 자습서에서는 가상 머신 확장 집합을 사용하여 백 엔드 풀을 만듭니다. 그런 다음, 웹 트래픽이 풀에서 적절한 서버에 도착하도록 소유한 도메인을 기준으로 수신기와 규칙을 구성합니다. 이 자습서에서는 여러 도메인을 소유하고 있으며 *www.contoso.com* 및 *www.fabrikam.com*의 예를 사용한다고 가정합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 네트워크 설정
> * 응용 프로그램 게이트웨이 만들기
> * 수신기 및 라우팅 규칙 만들기
> * 백 엔드 풀을 사용하여 가상 머신 확장 집합 만들기
> * 도메인에서 CNAME 레코드 만들기

![다중 사이트 라우팅 예](./media/tutorial-multisite-cli/scenario.png)

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 빠른 시작에서 Azure CLI 버전 2.0.4 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. [az group create](/cli/azure/group#create)를 사용하여 리소스 그룹을 만듭니다.

다음 예제에서는 *eastus* 위치에 *myResourceGroupAG*라는 리소스 그룹을 만듭니다.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>네트워크 리소스 만들기 

[az network vnet create](/cli/azure/network/vnet#az_net)를 사용하여 *myVNet*이라는 가상 네트워크와 *myAGSubnet*이라는 서브넷을 만듭니다. 그런 후 [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)를 사용하여 백 엔드 서버에 필요한 *myBackendSubnet*이라는 서브넷을 추가할 수 있습니다. [az network public-ip create](/cli/azure/public-ip#az_network_public_ip_create)를 사용하여 *myAGPublicIPAddress*라는 IP 주소를 만듭니다.

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

## <a name="create-the-application-gateway"></a>Application Gateway 만들기

[az network application-gateway create](/cli/azure/application-gateway#create)를 사용하여 *myAppGateway*라는 응용 프로그램 게이트웨이를 만들 수 있습니다. Azure CLI를 사용하여 응용 프로그램 게이트웨이를 만들 때 용량, sku, HTTP 설정 등의 구성 정보를 지정합니다. 응용 프로그램 게이트웨이는 앞에서 만든 *myAGSubnet* 및 *myAGPublicIPAddress*에 할당됩니다. 

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

응용 프로그램 게이트웨이가 생성될 때까지 몇 분 정도 걸릴 수 있습니다. 응용 프로그램 게이트웨이가 생성되면 다음과 같은 새 기능을 볼 수 있습니다.

- *appGatewayBackendPool* - 응용 프로그램 게이트웨이에 백 엔드 주소 풀이 하나 이상 있어야 합니다.
- *appGatewayBackendHttpSettings* - 포트 80 및 HTTP 프로토콜을 통신에 사용하도록 지정합니다.
- *appGatewayHttpListener* - *appGatewayBackendPool*에 연결되는 기본 수신기입니다.
- *appGatewayFrontendIP* - *myAGPublicIPAddress*를 *appGatewayHttpListener*에 할당합니다.
- *rule1* - *appGatewayHttpListener*에 연결되는 기본 라우팅 규칙입니다.

### <a name="add-the-backend-pools"></a>백 엔드 풀 추가

[az network application-gateway address-pool create](/cli/azure/application-gateway#az_network_application_gateway_address_pool_create) 명령을 사용하여 백 엔드 서버를 포함하는 데 필요한 *contosoPool* 및 *fabrikamPool*이라는 백 엔드 풀을 추가합니다.

```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name contosoPool
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name fabrikamPool
```

### <a name="add-listeners"></a>수신기 추가

응용 프로그램 게이트웨이에서 트래픽을 백 엔드 풀로 적절히 라우팅할 수 있는 수신기가 필요합니다. 이 자습서에서는 두 도메인에 대해 두 개의 수신기를 만듭니다. 이 예제에서는 *www.contoso.com* 및 *www.fabrikam.com*의 도메인에 대해 수신기가 생성됩니다. 

[az network application-gateway http-listener create](/cli/azure/application-gateway#az_network_application_gateway_http_listener_create)를 사용하여 트래픽을 라우팅하는 데 필요한 *contosoListener* 및 *fabrikamListener*라는 백 엔드 수신기를 추가합니다.

```azurecli-interactive
az network application-gateway http-listener create \
  --name contosoListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.com
az network application-gateway http-listener create \
  --name fabrikamListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.fabrikam.com   
  ```

### <a name="add-routing-rules"></a>라우팅 규칙 추가

규칙은 생성된 순서대로 처리되며, 응용 프로그램 게이트웨이로 전송된 URL과 일치하는 첫 번째 규칙을 사용하여 트래픽이 리디렉션됩니다. 예를 들어 기본 수신기를 사용하는 규칙과 다중 사이트 수신기를 사용하는 규칙이 둘 다 같은 포트에 있는 경우 다중 사이트 규칙이 예상대로 작동하려면 다중 사이트 수신기를 사용하는 규칙은 기본 수신기를 사용하는 규칙 앞에 나열되어야 합니다. 

이 예제에서는 응용 프로그램 게이트웨이가 생성되었을 때 두 개의 새 규칙을 만들고 생성된 기본 규칙을 삭제합니다. [az network application-gateway rule create](/cli/azure/application-gateway#az_network_application_gateway_rule_create)를 사용하여 규칙을 추가할 수 있습니다.

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoListener \
  --rule-type Basic \
  --address-pool contosoPool
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name fabrikamRule \
  --resource-group myResourceGroupAG \
  --http-listener fabrikamListener \
  --rule-type Basic \
  --address-pool fabrikamPool
az network application-gateway rule delete \
  --gateway-name myAppGateway \
  --name rule1 \
  --resource-group myResourceGroupAG
```

## <a name="create-virtual-machine-scale-sets"></a>가상 머신 확장 집합 만들기

이 예제에서는 응용 프로그램에서 세 개의 백 엔드 풀을 지원하는 세 개의 가상 머신 확장 집합을 만듭니다. 사용자가 만든 확장 집합의 이름은 *myvmss1*, *myvmss2* 및 *myvmss3*입니다. 각 확장 집합에는 NGINX를 설치하는 두 개의 가상 머신 인스턴스가 포함됩니다.

```azurecli-interactive
for i in `seq 1 2`; do
  if [ $i -eq 1 ]
  then
    poolName="contosoPool"
  fi
  if [ $i -eq 2 ]
  then
    poolName="fabrikamPool"
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
for i in `seq 1 2`; do
  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{
  "fileUris": ["https://raw.githubusercontent.com/davidmu1/samplescripts/master/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="create-a-cname-record-in-your-domain"></a>도메인에서 CNAME 레코드 만들기

응용 프로그램 게이트웨이가 해당 공용 IP 주소로 생성된 후 DNS 주소를 가져와 도메인에서 CNAME 레코드를 만드는 데 사용할 수 있습니다. [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show)를 사용하여 응용 프로그램 게이트웨이의 DNS 주소를 가져올 수 있습니다. DNSSettings의 *fqdn* 값을 복사하여 만드는 CNAME 레코드의 값으로 사용합니다. 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

A 레코드를 사용할 경우 응용 프로그램 게이트웨이를 다시 시작할 때 VIP가 변경될 수 있으므로 권장되지 않습니다.

## <a name="test-the-application-gateway"></a>응용 프로그램 게이트웨이 테스트

브라우저의 주소 표시줄에 도메인 이름을 입력합니다. 예: http://www.contoso.com

![응용 프로그램 게이트웨이에서 contoso 사이트 테스트](./media/tutorial-multisite-cli/application-gateway-nginxtest1.png)

주소를 다른 도메인으로 변경하면 다음 예제와 같은 내용이 표시됩니다.

![응용 프로그램 게이트웨이에서 fabrikam 사이트 테스트](./media/tutorial-multisite-cli/application-gateway-nginxtest2.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 네트워크 설정
> * 응용 프로그램 게이트웨이 만들기
> * 수신기 및 라우팅 규칙 만들기
> * 백 엔드 풀을 사용하여 가상 머신 확장 집합 만들기
> * 도메인에서 CNAME 레코드 만들기

> [!div class="nextstepaction"]
> [응용 프로그램 게이트웨이 통해 수행할 수 있는 작업에 대한 자세한 정보](application-gateway-introduction.md)