---
title: Azure CLI 2.0을 사용하여 Azure DNS 사설 영역 시작 | Microsoft Docs
description: Azure DNS에 사설 DNS 영역 및 레코드를 만드는 방법을 알아봅니다. Azure CLI 2.0을 사용하여 첫 번째 DNS 사설 영역 및 레코드를 만들고 관리하는 방법에 대한 단계별 가이드입니다.
services: dns
documentationcenter: na
author: KumuD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: kumud
ms.openlocfilehash: d10f3201adb972468d8de7e66a940232ed19562d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="get-started-with-azure-dns-private-zones-using-azure-cli-20"></a>Azure CLI 2.0을 사용하여 Azure DNS 사설 영역 시작

> [!div class="op_single_selector"]
> * [PowerShell](private-dns-getstarted-powershell.md)
> * [Azure CLI 2.0](private-dns-getstarted-cli.md)

이 문서에서는 Windows, Mac 및 Linux용 플랫폼 간 Azure CLI 2.0을 사용하여 첫 번째 DNS 사설 영역 및 레코드를 만드는 단계를 안내합니다. Azure PowerShell을 사용하여 단계를 수행할 수도 있습니다.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

DNS 영역은 특정 도메인에 대한 DNS 레코드를 호스트하는 데 사용됩니다. Azure DNS에서 도메인 호스팅을 시작하려면 해당 도메인 이름의 DNS 영역을 만들어야 합니다. 그러면 이 DNS 영역 안에 도메인의 각 DNS 레코드가 생성됩니다. 가상 네트워크에 사설 DNS 영역에 게시하려면 영역 내에서 레코드를 확인하도록 허용된 가상 네트워크 목록을 지정합니다.  이것을 ‘확인 가상 네트워크’라고 합니다.  또한 VM이 생성되고, IP가 변경되고, 제거될 때마다 Azure DNS가 호스트 이름 레코드를 유지 관리하게 되는 가상 네트워크를 지정할 수 있습니다.  이것을 '등록 가상 네트워크'라고 합니다.

이러한 지침에서는 이미 Azure CLI 2.0을 설치하여 로그인하고, 사설 영역을 지원하는 필수 CLI 확장을 설치한 것으로 가정합니다. 도움말은 [Azure CLI 2.0을 사용하여 DNS 영역을 관리하는 방법](dns-operations-dnszones-cli.md)을 참조하세요.

## <a name="to-installuse-azure-dns-private-zones-feature-public-preview"></a>Azure DNS 사설 영역 기능(공개 미리 보기)을 설치/사용하려면
Azure DNS 사설 영역 기능은 Azure CLI 확장을 통해 공개 미리 보기로 출시되었습니다. “dns” Azure CLI 확장 설치 

```
az extension add --name dns
``` 

## <a name="create-the-resource-group"></a>리소스 그룹 만들기

DNS 영역을 만들기 전에 DNS 영역을 포함할 리소스 그룹이 생성됩니다. 다음은 명령을 표시합니다.

```azurecli
az group create --name MyResourceGroup --location "West US"
```

## <a name="create-a-dns-private-zone"></a>DNS 사설 영역 만들기

`az network dns zone create` 명령을 사용하여 DNS 사설 영역을 만듭니다. 이 명령에 대한 도움말을 보려면 `az network dns zone create --help`을 입력합니다.

다음 예제에서는 *MyResourceGroup*이라는 리소스 그룹에 *contoso.local*이라는 DNS 사설 영역을 만들고, resolution-vnets 매개 변수를 사용하여 *MyAzureVnet*이라는 가상 네트워크에서 사용할 수 있게(링크 제공) 합니다. 예제를 사용하여 DNS 영역을 만들고 사용자 고유 값으로 대체합니다.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.local --zone-type Private --resolution-vnets MyAzureVnet
```

참고: 위의 예제에서 가상 네트워크 "MyAzureVnet"은 사설 영역과 동일한 리소스 그룹 및 구독에 소속됩니다. 다른 리소스 그룹 또는 구독에 속한 가상 네트워크를 연결해야 하는 경우 단순한 가상 네트워크 이름 대신 --resolution-vnets 매개 변수에 대한 전체 Azure Resource Manager ID를 지정해야 합니다. 

Azure가 자동으로 영역에 호스트 이름 레코드를 만들게 하려면 *resolution-vnets* 대신 *registration-vnets* 매개 변수를 사용합니다.  가상 네트워크 등록은 자동으로 해상도에 대해 사용이 설정됩니다.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.local --zone-type Private --registration-vnets MyAzureVnet
```

## <a name="create-a-dns-record"></a>DNS 레코드 만들기

DNS 레코드를 만들려면 `az network dns record-set [record type] add-record` 명령을 사용합니다. 도움말과 A 레코드에 대한 예제는 `azure network dns record-set A add-record --help`을 참조하세요.

다음 예제에서는 리소스 그룹 “MyResourceGroup”의 DNS 영역 “contoso.com”에 상대적 이름 “ip1”이 포함된 레코드를 만듭니다. 레코드 집합의 정규화된 이름은 “ip1.contoso.local”입니다. 레코드 형식은 “A”이고, IP 주소는 “10.0.0.1”입니다.

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.local -n ip1 -a 10.0.0.1
```

두 개 이상의 레코드가 있는 레코드 집합, 대체 TTL 값 등 다른 레코드 유형을 알아보거나 기존 레코드를 수정하려면 [Azure CLI 2.0을 사용하여 DNS 레코드 및 레코드 집합 관리](dns-operations-recordsets-cli.md)를 참조하세요.

## <a name="view-records"></a>레코드 보기

사용자 영역에 DNS 레코드를 나열하려면 다음을 사용하세요.

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.com
```

## <a name="get-a-dns-private-zone"></a>DNS 사설 영역 가져오기

DNS 사설 영역을 검색하려면 `az network dns zone show` 명령을 사용합니다. 도움말을 보려면 `az network dns zone show --help`을 참조하세요.

다음 예제는 리소스 그룹 *MyResourceGroup*에서 DNS 영역 *contoso.local* 및 관련 데이터를 반환합니다. 

```azurecli
az network dns zone show --resource-group MyResourceGroup --name contoso.local
```

다음 예제는 응답입니다.

```json
{
  "etag": "00000002-0000-0000-3d4d-64aa3689d201",
  "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/MyResourceGroup/providers/Microsoft.Network/dnszones/contoso.local",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.local",
  "nameServers": null,
  "numberOfRecordSets": 1,
  "registrationVirtualNetworks": [],
  "resolutionVirtualNetworks": [
    {
      "additionalProperties": {},
      "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyAzureVnet",
      "resourceGroup": "MyResourceGroup"
    }
  ]
  "resourceGroup": "MyResourceGroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones",
  "zoneType": "Private"
}
```

DNS 레코드는 `az network dns zone show`에서 반환되지 않습니다. DNS 레코드를 나열하려면 `az network dns record-set list`를 사용합니다.


## <a name="list-dns-zones"></a>DNS 영역 나열

DNS 영역을 열거하려면 `az network dns zone list`를 사용합니다. 도움말을 보려면 `az network dns zone list --help`을 참조하세요.

리소스 그룹을 지정하면 리소스 그룹 내의 해당 영역만 나열합니다.

```azurecli
az network dns zone list --resource-group MyResourceGroup
```

리소스 그룹을 생략하면 구독의 모든 영역이 나열됩니다.

```azurecli
az network dns zone list 
```

## <a name="update-a-dns-zone"></a>DNS 영역 업데이트

`az network dns zone update`를 사용하여 DNS 영역 리소스를 변경할 수 있습니다. 도움말을 보려면 `az network dns zone update --help`을 참조하세요.

이 명령은 영역 내의 DNS 레코드 집합을 업데이트하지 않습니다([DNS 레코드를 관리하는 방법](dns-operations-recordsets-cli.md) 참조). 영역 리소스 자체의 속성을 업데이트하는 데만 사용됩니다. 사설 영역의 경우 영역에 연결된 등록 또는 확인 가상 네트워크를 업데이트할 수 있습니다. 

다음 예제에서는 DNS 사설 영역에 연결된 등록 가상 네트워크를 업데이트하는 방법을 보여줍니다. 연결된 기존 확인 가상 네트워크는 새로 지정된 가상 네트워크로 대체됩니다.

```azurecli
az network dns zone update --resource-group MyResourceGroup --name contoso.local --zone-type Private --resolution-vnets MyNewAzureVnet
```

## <a name="delete-a-dns-zone"></a>DNS 영역 삭제

`az network dns zone delete`를 사용하여 DNS 영역을 삭제할 수 있습니다. 도움말을 보려면 `az network dns zone delete --help`을 참조하세요.

> [!NOTE]
> DNS 영역을 삭제하면 영역 내의 모든 DNS 레코드도 삭제됩니다. 이 작업은 취소할 수 없습니다. DNS 영역을 사용 중인 경우 영역이 삭제되면 영역을 사용하는 서비스가 실패합니다.
>
>실수로 영역이 삭제되는 것을 방지하려면 [DNS 영역 및 레코드를 보호하는 방법](dns-protect-zones-recordsets.md)을 참조하세요.

이 명령은 확인 메시지를 표시합니다. 선택적 `--yes` 스위치는 이 프롬프트를 표시하지 않습니다.

다음 예제에서는 리소스 그룹 *MyResourceGroup*에서 *contoso.local* 영역을 삭제하는 방법을 보여줍니다.

```azurecli
az network dns zone delete --resource-group myresourcegroup --name contoso.local
```

## <a name="delete-all-resources"></a>모든 리소스 삭제
 
이 문서에서 만든 모든 리소스를 삭제하려면 다음 단계를 수행합니다.

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>다음 단계

Azure DNS에 대한 자세한 내용은 [Azure DNS 개요](dns-overview.md)를 참조하세요.

Azure DNS에서 DNS 영역 관리에 대한 자세한 내용은 [Azure CLI 2.0을 사용하여 Azure DNS에서 DNS 영역 관리](dns-operations-dnszones-cli.md)를 참조하세요.

Azure DNS에서 DNS 레코드 관리에 대한 자세한 내용은 [Azure CLI 2.0을 사용하여 Azure DNS에서 DNS 레코드 및 레코드 집합 관리](dns-operations-recordsets-cli.md)를 참조하세요.
