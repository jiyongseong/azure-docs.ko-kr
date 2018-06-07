---
title: Azure에서 Linux Service Fabric 클러스터 만들기 | Microsoft Docs
description: 이 자습서에서는 Azure CLI를 사용하여 기존 Azure 가상 네트워크에 Linux Service Fabric 클러스터를 배포하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/22/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: ff57aec76171b45dbebff928f2898bd5f91ec1c2
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365545"
---
# <a name="tutorial-deploy-a-service-fabric-linux-cluster-into-an-azure-virtual-network"></a>자습서: Azure 가상 네트워크에 Service Fabric Linux 클러스터 배포
이 자습서는 시리즈의 1부입니다. Azure CLI 및 템플릿을 사용하여 [Azure VNET(가상 네트워크)](../virtual-network/virtual-networks-overview.md) 및 [NSG(네트워크 보안 그룹)](../virtual-network/security-overview.md)에 Linux Service Fabric 클러스터를 배포하는 방법을 알아봅니다. 작업이 완료되면 응용 프로그램을 배포할 수 있는, 클라우드에서 실행되는 클러스터가 생깁니다. PowerShell을 사용하여 Windows 클러스터를 만들려면 [Azure에서 보안 Windows 클러스터 만들기](service-fabric-tutorial-create-vnet-and-windows-cluster.md)를 참조하세요.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure CLI를 사용하여 Azure에서 VNET 만들기
> * Azure CLI를 사용하여 Azure에서 보안 Service Fabric 클러스터 만들기
> * X.509 인증서를 사용하여 클러스터 보호
> * Service Fabric CLI를 사용하여 클러스터에 연결
> * 클러스터 제거

이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * Azure에서 보안 클러스터 만들기
> * [클러스터 규모 확장 또는 규모 감축](service-fabric-tutorial-scale-cluster.md)
> * [클러스터의 런타임 업그레이드](service-fabric-tutorial-upgrade-cluster.md)
> * [Service Fabric을 사용하여 API Management 배포](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>필수 조건
이 자습서를 시작하기 전에:
- Azure 구독이 없는 경우 [평가판 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- [Service Fabric CLI](service-fabric-cli.md)를 설치합니다.
- [Azure CLI 2.0](/cli/azure/install-azure-cli)을 설치합니다.

다음 절차에서는 5노드 Service Fabric 클러스터를 만듭니다. Azure에서 Service Fabric 클러스터를 실행할 때 발생하는 비용을 계산하려면 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용합니다.

## <a name="key-concepts"></a>주요 개념
[Service Fabric 클러스터](service-fabric-deploy-anywhere.md): 마이크로 서비스가 배포되고 관리되는 네트워크로 연결된 가상 또는 실제 컴퓨터 집합입니다. 클러스터의 규모를 컴퓨터 수천 대로 확장할 수 있습니다. 클러스터의 일부인 컴퓨터나 VM을 노드라고 합니다. 각 노드는 노드 이름(문자열)에 할당됩니다. 노드는 배치 속성과 같은 특징이 있습니다.

클러스터의 가상 머신 집합에 대한 크기, 번호 및 속성이 노드 형식에 정의됩니다. 모든 정의된 노드 형식은 [가상 머신 확장 집합](/azure/virtual-machine-scale-sets/)으로 설정되며, 이는 가상 머신의 컬렉션을 집합으로 배포하고 관리하는 데 사용하는 Azure 계산 리소스입니다. 각 노드 형식은 독립적으로 확장 또는 축소되고, 다른 포트의 집합을 열며 다른 용량 메트릭을 가질 수 있습니다. 노드 형식은 "프런트 엔드" 또는 "백 엔드"와 같은 클러스터 노드 집합에 대한 역할을 정의하는 데 사용됩니다.  클러스터에 둘 이상의 노드 형식이 있을 수 있지만 주 노드 형식에는 프로덕션 클러스터에 대한 최소 5개의 VM(또는 테스트 클러스터에 대한 최소 3개의 VM)이 있어야 합니다.  [Service Fabric 시스템 서비스](service-fabric-technical-overview.md#system-services)는 주 노드 형식의 노드에 배치됩니다.

클러스터는 클러스터 인증서로 보호됩니다. 클러스터 인증서는 노드 간 통신을 보호하고 관리 클라이언트에 클러스터 관리 끝점을 인증하는 데 사용되는 X.509 인증서입니다.  클러스터 인증서는 HTTPS 관리 API 및 HTTPS를 통한 Service Fabric Explorer용 SSL도 제공합니다. 자체 서명된 인증서는 테스트 클러스터에 유용합니다.  프로덕션 클러스터의 경우 클러스터 인증서로 CA(인증 기관)의 인증서를 사용합니다.

클러스터 인증서는 다음 조건을 충족해야 합니다.

- 개인 키를 포함해야 합니다.
- 키 교환을 위해 만들어야 합니다. 이 인증서는 개인 정보 교환(.pfx) 파일로 내보낼 수 있습니다.
- 인증서의 주체 이름이 Service Fabric 클러스터 액세스에 사용되는 도메인과 일치해야 합니다. 클러스터의 HTTPS 관리 끝점 및 Service Fabric Explorer에 대해 SSL을 제공하려면 이렇게 일치해야 합니다. .cloudapp.azure.com 도메인에 사용되는 SSL 인증서는 CA(인증 기관)에서 얻을 수 없습니다.  클러스터에 대한 사용자 지정 도메인 이름을 획득해야 합니다. CA에서 인증서를 요청하는 경우 인증서의 주체 이름이 클러스터에 사용되는 사용자 지정 도메인 이름과 일치해야 합니다.

Azure Key Vault는 Azure에서 서비스 패브릭 클러스터에 대한 인증서를 관리하는 데 사용됩니다.  클러스터를 Azure에 배포할 때 서비스 패브릭 클러스터 생성을 담당하는 Azure 리소스 공급자는 주요 자격 증명 모음에서 인증서를 가져와 클러스터 VM에 설치합니다.

이 자습서에서는 단일 노드 형식인 5개 노드로 클러스터를 배포합니다. 그러나 프로덕션 클러스터 배포의 경우 [용량 계획](service-fabric-cluster-capacity.md)은 중요한 단계입니다. 다음은 해당 프로세스의 일부로 고려해야 할 몇 가지 사항입니다.

- 클러스터에 필요한 노드 및 노드 형식 수 
- 각 노드 유형의 속성(예: 크기, 기본, 인터넷 연결 및 VM 수)
- 클러스터의 안정성 및 지속성 특성

## <a name="download-and-explore-the-template"></a>템플릿 다운로드 및 탐색
다음 Resource Manager 템플릿 파일을 다운로드합니다.
- [vnet-linuxcluster.json][template]
- [vnet-linuxcluster.parameters.json][parameters]

[vnet-linuxcluster.json][template]은 다음을 포함한 숫자 리소스를 배포합니다.

### <a name="service-fabric-cluster"></a>Service Fabric 클러스터
Linux 클러스터는 다음과 같은 특성으로 배포됩니다.
- 단일 노드 형식 
- 기본 노드 형식에서 5개의 노드(템플릿 매개 변수에서 구성 가능)
- OS: Ubuntu 16.04 LTS(템플릿 매개 변수에서 구성 가능)
- 보안된 인증서(템플릿 매개 변수에서 구성 가능)
- [DNS 서비스](service-fabric-dnsservice.md) 사용함
- 브론즈의 [내구성 수준](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)(템플릿 매개 변수에서 구성 가능)
- 실버의 [안정성 수준](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster)(템플릿 매개 변수에서 구성 가능)
- 클라이언트 연결 엔드포인트: 19000(템플릿 매개 변수에서 구성 가능)
- HTTP 게이트웨이 엔드포인트: 19080(템플릿 매개 변수에서 구성 가능)

### <a name="azure-load-balancer"></a>Azure Load Balancer
부하 분산 장치가 배포되고 다음 포트에 대해 프로브 및 규칙을 설정합니다.
- 클라이언트 연결 엔드포인트: 19000
- HTTP 게이트웨이 엔드포인트 19080 
- 응용 프로그램 포트: 80
- 응용 프로그램 포트: 443

### <a name="virtual-network-subnet-and-network-security-group"></a>가상 네트워크, 서브넷 및 네트워크 보안 그룹
가상 네트워크, 서브넷 및 네트워크 보안 그룹의 이름은 템플릿 매개 변수에서 선언됩니다.  가상 네트워크 및 서브넷의 주소 공간은 템플릿 매개 변수에서 선언됩니다.
- 가상 네트워크 주소 공간: 10.0.0.0/16
- Service Fabric 서브넷 주소 공간: 10.0.2.0/24

네트워크 보안 그룹에서 다음과 같은 인바운드 트래픽 규칙이 활성화됩니다. 템플릿 변수를 변경하여 포트 값을 변경할 수 있습니다.
- ClientConnectionEndpoint(TCP): 19000
- HttpGatewayEndpoint(HTTP/TCP): 19080
- SMB: 445
- Internodecommunication - 1025, 1026, 1027
- 임시 포트 범위 – 49152~65534(최소 256 포트 필요)
- 응용 프로그램 사용에 대한 포트: 80 및 443
- 응용 프로그램 포트 범위 – 49152~65534(서비스 간 통신에 사용되며 부하 분산 장치에서 열리지 않음)
- 다른 모든 포트 차단

다른 응용 프로그램 포트가 필요한 경우 트래픽을 허용하도록 Microsoft.Network/loadBalancers 리소스 및 Microsoft.Network/networkSecurityGroups 리소스를 조정해야 합니다.

## <a name="set-template-parameters"></a>템플릿 매개 변수 설정
[vnet-cluster.parameters.json][parameters] 매개 변수 파일은 클러스터 및 연결된 리소스를 배포하는 데 사용되는 많은 값을 선언합니다. 배포에 대해 수정이 필요할 수도 있는 매개 변수 중 일부는 다음과 같습니다.

|매개 변수|예제 값|메모|
|---|---||
|adminUserName|vmadmin| 클러스터 VM에 대한 관리자 사용자 이름입니다. |
|adminPassword|Password#1234| 클러스터 VM에 대한 관리자 암호입니다.|
|clusterName|mysfcluster123| 클러스터의 이름입니다. |
|location|southcentralus| 클러스터의 위치입니다. |
|certificateThumbprint|| <p>자체 서명된 인증서를 만들거나 인증서 파일을 제공하는 경우 값은 비워두어야 합니다.</p><p>이전에 키 자격 증명 모음에 업로드된 기존 인증서를 사용하려면 인증서 지문 값을 입력합니다. 예를 들면 "6190390162C988701DB5676EB81083EA608DCCF3"과 같습니다. </p>| 
|certificateUrlValue|| <p>자체 서명된 인증서를 만들거나 인증서 파일을 제공하는 경우 값은 비워두어야 합니다.</p><p>이전에 키 자격 증명 모음에 업로드된 기존 인증서를 사용하려면 인증서 URL을 입력합니다. 예: "https://mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>자체 서명된 인증서를 만들거나 인증서 파일을 제공하는 경우 값은 비워두어야 합니다.</p><p>이전에 키 자격 증명 모음에 업로드된 기존 인증서를 사용하려면 원본 자격 증명 모음 값을 입력합니다. 예를 들면 “/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT”와 같습니다.</p>|


<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>가상 네트워크 및 클러스터 배포
다음으로 네트워크 토폴로지를 설정하고 Service Fabric 클러스터를 배포합니다. [vnet-linuxcluster.json][template] Resource Manager 템플릿은 VNET(가상 네트워크)과 Service Fabric에 대한 서브넷 및 NSG(네트워크 보안 그룹)를 만듭니다. 템플릿은 활성화된 인증서 보안으로 클러스터도 배포합니다.  프로덕션 클러스터의 경우 클러스터 인증서로 CA(인증 기관)의 인증서를 사용합니다. 테스트 클러스터를 보호하는 데 자체 서명된 인증서를 사용할 수 있습니다.

다음 스크립트는 [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) 명령 및 템플릿을 사용하여 기존 인증서로 보호된 새 클러스터를 배포합니다. 또한 명령은 Azure에서 새 키 자격 증명 모음을 만들고 인증서를 업로드합니다.

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"  
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates\MyCertificate.pem"

# sign in to your Azure account and select your subscription
az login
az account set --subscription <guid>

# Create a new resource group for your deployment and give it a name and a location.
az group create --name $ResourceGroupName --location $Location

# Create the Service Fabric cluster.
az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-password $Password --certificate-file $CertPath \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file vnet-linuxcluster.json --parameter-file vnet-linuxcluster.parameters.json
```

## <a name="connect-to-the-secure-cluster"></a>보안 클러스터에 연결
키를 사용하여 Service Fabric CLI `sfctl cluster select` 명령을 통해 클러스터에 연결합니다.  자체 서명된 인증서에만 **--no-verify** 옵션을 사용합니다.

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

`sfctl cluster health` 명령을 사용하여 연결되어 있고 클러스터 상태가 정상인지 확인합니다.

```azurecli
sfctl cluster health
```

## <a name="clean-up-resources"></a>리소스 정리
이 자습서 시리즈의 다른 문서에서는 방금 만든 클러스터를 사용합니다. 다음 문서로 바로 이동하지 않는 경우 요금이 발생하지 않도록 클러스터를 삭제하는 것이 좋습니다. 클러스터 및 클러스터에서 사용하는 모든 리소스를 삭제하는 가장 간단한 방법은 리소스 그룹을 삭제하는 것입니다.

Azure에 로그인하고 클러스터를 제거할 구독 ID를 선택합니다.  [Azure Portal](http://portal.azure.com)에 로그인하여 구독 ID를 찾을 수 있습니다. [az group delete](/cli/azure/group?view=azure-cli-latest#az_group_delete) 명령을 사용하여 리소스 그룹 및 모든 클러스터 리소스를 삭제합니다.

```azurecli
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure CLI를 사용하여 Azure에서 VNET 만들기
> * Azure CLI를 사용하여 Azure에서 보안 Service Fabric 클러스터 만들기
> * X.509 인증서를 사용하여 클러스터 보호
> * Service Fabric CLI를 사용하여 클러스터에 연결
> * 클러스터 제거

이제 다음 자습서로 넘어가서 클러스터 규모를 조정하는 방법을 알아보겠습니다.
> [!div class="nextstepaction"]
> [클러스터 규모 조정](service-fabric-tutorial-scale-cluster.md)


[template]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/cluster-tutorial/vnet-linuxcluster.json
[parameters]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/cluster-tutorial/vnet-linuxcluster.parameters.json
