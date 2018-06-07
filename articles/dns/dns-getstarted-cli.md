---
title: Azure CLI 2.0을 사용하여 Azure DNS 시작 | Microsoft Docs
description: Azure DNS에 DNS 영역 및 레코드를 만드는 방법을 알아봅니다. Azure CLI 2.0을 사용하여 첫 번째 DNS 영역 및 레코드를 만들고 관리하는 단계별 가이드입니다.
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
ms.date: 03/10/2017
ms.author: kumud
ms.openlocfilehash: d24eaa4974f8bff09b337384e4fd139edb6ebd70
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="get-started-with-azure-dns-using-azure-cli-20"></a>Azure CLI 2.0을 사용하여 Azure DNS 시작

> [!div class="op_single_selector"]
> * [Azure Portal](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [Azure CLI 2.0](dns-getstarted-cli.md)

이 문서에서는 Windows, Mac 및 Linux용 플랫폼 간 Azure CLI 2.0을 사용하여 첫 번째 DNS 영역 및 레코드를 만드는 단계를 안내합니다. Azure Portal 또는 Azure PowerShell을 사용하여 이 단계를 수행할 수도 있습니다.

DNS 영역은 특정 도메인에 대한 DNS 레코드를 호스트하는 데 사용됩니다. Azure DNS에서 도메인 호스팅을 시작하려면 해당 도메인 이름의 DNS 영역을 만들어야 합니다. 그러면 이 DNS 영역 안에 도메인의 각 DNS 레코드가 생성됩니다. 마지막으로 DNS 영역을 인터넷에 게시하려면 도메인에 대한 이름 서버를 구성해야 합니다. 아래에서는 이러한 각 단계에 대해 설명합니다.

이 지침에서는 이미 Azure CLI 2.0을 설치했고, 로그인했다고 가정합니다. 도움말은 [Azure CLI 2.0을 사용하여 DNS 영역을 관리하는 방법](dns-operations-dnszones-cli.md)을 참조하세요.

Azure DNS는 이제 사설 DNS 영역(현재는 공개 미리 보기)도 지원합니다. 사설 DNS 영역에 대해 자세히 알아보려면 [사설 도메인에 Azure DNS 사용](private-dns-overview.md)을 참조하세요. 사설 DNS 영역을 만드는 방법은 [CLI를 사용하여 Azure DNS 사설 영역 시작](./private-dns-getstarted-cli.md)을 참조하세요.

## <a name="create-the-resource-group"></a>리소스 그룹 만들기

DNS 영역을 만들기 전에 DNS 영역을 포함할 리소스 그룹이 생성됩니다. 다음은 명령을 표시합니다.

```azurecli
az group create --name MyResourceGroup --location "West US"
```

## <a name="create-a-dns-zone"></a>DNS 영역 만들기

`az network dns zone create` 명령을 사용하여 DNS 영역을 만듭니다. 이 명령에 대한 도움말을 보려면 `az network dns zone create -h`을 입력합니다.

다음 예제에서는 *MyResourceGroup*이라는 리소스 그룹에 *contoso.com*이라는 DNS 영역을 만듭니다. 예제를 사용하여 DNS 영역을 만들고 사용자 고유 값으로 대체합니다.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.com
```

## <a name="create-a-dns-record"></a>DNS 레코드 만들기

DNS 레코드를 만들려면 `az network dns record-set [record type] add-record` 명령을 사용합니다. 도움말과 A 레코드에 대한 예제는 `azure network dns record-set A add-record -h`을 참조하세요.

다음 예제에서는 리소스 그룹 "MyResourceGroup"에서 DNS 영역 "contoso.com"에 상대적 이름 "www"가 포함된 레코드를 만듭니다. 레코드의 정규화된 이름은 “www.contoso.com”입니다. 레코드 유형은 "A"이고, IP 주소는 "1.2.3.4", 기본 TTL은 3600초(1시간)가 사용됩니다.

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.com -n www -a 1.2.3.4
```

두 개 이상의 레코드가 있는 레코드 집합, 대체 TTL 값 등 다른 레코드 유형을 알아보거나 기존 레코드를 수정하려면 [Azure CLI 2.0을 사용하여 DNS 레코드 및 레코드 집합 관리](dns-operations-recordsets-cli.md)를 참조하세요.

## <a name="view-records"></a>레코드 보기

사용자 영역에 DNS 레코드를 나열하려면 다음을 사용하세요.

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.com
```

## <a name="update-name-servers"></a>이름 서버 업데이트

DNS 영역 및 레코드가 적절히 설정되었다면 Azure DNS 이름 서버를 사용하도록 도메인 이름을 구성해야 합니다. 이렇게 하면 인터넷에 있는 다른 사용자가 DNS 레코드를 찾을 수 있습니다.

영역에 대한 이름 서버는 `az network dns zone show` 명령으로 지정됩니다. 이름 서버 이름을 보려면 다음 예에 표시된 것처럼 JSON 출력을 사용하세요.

```azurecli
az network dns zone show -g MyResourceGroup -n contoso.com -o json

{
  "etag": "00000003-0000-0000-b40d-0996b97ed101",
  "id": "/subscriptions/a385a691-bd93-41b0-8084-8213ebc5bff7/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.com",
  "nameServers": [
    "ns1-01.azure-dns.com.",
    "ns2-01.azure-dns.net.",
    "ns3-01.azure-dns.org.",
    "ns4-01.azure-dns.info."
  ],
  "numberOfRecordSets": 3,
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

이러한 이름 서버는 사용자가 도메인 이름을 구입한 도메인 이름 등록 기관에서 구성해야 합니다. 등록 기관에서 도메인에 대한 이름 서버를 설정하는 옵션을 제공합니다. 자세한 내용은 [Azure DNS에 도메인 위임](dns-domain-delegation.md)을 참조하세요.

## <a name="delete-all-resources"></a>모든 리소스 삭제
 
이 문서에서 만든 모든 리소스를 삭제하려면 다음 단계를 수행합니다.

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>다음 단계

Azure DNS에 대한 자세한 내용은 [Azure DNS 개요](dns-overview.md)를 참조하세요.

Azure DNS에서 DNS 영역 관리에 대한 자세한 내용은 [Azure CLI 2.0을 사용하여 Azure DNS에서 DNS 영역 관리](dns-operations-dnszones-cli.md)를 참조하세요.

Azure DNS에서 DNS 레코드 관리에 대한 자세한 내용은 [Azure CLI 2.0을 사용하여 Azure DNS에서 DNS 레코드 및 레코드 집합 관리](dns-operations-recordsets-cli.md)를 참조하세요.
