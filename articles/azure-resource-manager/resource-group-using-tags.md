---
title: 논리적 조직을 위한 Azure 리소스에 태그 지정 | Microsoft Docs
description: 태그를 적용하여 대금 청구 및 관리를 위해 Azure 리소스를 구성하는 방법을 보여 줍니다.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 003a78e5-2ff8-4685-93b4-e94d6fb8ed5b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: AzurePortal
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: tomfitz
ms.openlocfilehash: 6f9b2b04c3bdfc02065e2a01e1975d734a5f53ac
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358985"
---
# <a name="use-tags-to-organize-your-azure-resources"></a>태그를 사용하여 Azure 리소스 구성

[!INCLUDE [resource-manager-governance-tags](../../includes/resource-manager-governance-tags.md)]

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="powershell"></a>PowerShell

이 문서의 예제에는 Azure PowerShell 6.0 이상이 필요합니다. 버전 6.0 이상이 없는 경우 [버전을 업데이트](/powershell/azure/install-azurerm-ps)합니다.

*리소스 그룹*에 대한 기존 태그를 보려면 다음을 사용합니다.

```powershell
(Get-AzureRmResourceGroup -Name examplegroup).Tags
```

그러면 스크립트가 다음 형식을 반환합니다.

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

*지정된 리소스 ID를 포함한 리소스*에 대한 기존 태그를 보려면 다음을 사용합니다.

```powershell
(Get-AzureRmResource -ResourceId /subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>).Tags
```

또는 *지정된 이름 및 리소스 그룹을 포함한 리소스*에 대한 기존 태그를 보려면 다음을 사용합니다.

```powershell
(Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

*특정 태그가 있는 리소스 그룹*을 가져오려면 다음을 사용합니다.

```powershell
(Get-AzureRmResourceGroup -Tag @{ Dept="Finance" }).ResourceGroupName
```

*특정 태그가 있는 리소스*를 가져오려면 다음을 사용합니다.

```powershell
(Get-AzureRmResource -Tag @{ Dept="Finance"}).Name
```

*특정 태그 이름이 있는 리소스*를 가져오려면 다음을 사용합니다.

```powershell
(Get-AzureRmResource -TagName Dept).Name
```

리소스 또는 리소스 그룹에 태그를 적용할 때마다 해당 리소스 또는 리소스 그룹의 기존 태그가 덮어써집니다. 따라서 리소스 또는 리소스 그룹에 기존 태그가 있는지에 따라 다른 방법을 사용해야 합니다.

*기존 태그를 포함하지 않는 리소스 그룹*에 태그를 추가하려면 다음을 사용합니다.

```powershell
Set-AzureRmResourceGroup -Name examplegroup -Tag @{ Dept="IT"; Environment="Test" }
```

*기존 태그를 포함하는 리소스 그룹*에 태그를 추가하려면 기존 태그를 검색하고, 새 태그를 추가하고, 태그를 다시 적용합니다.

```powershell
$tags = (Get-AzureRmResourceGroup -Name examplegroup).Tags
$tags.Add("Status", "Approved")
Set-AzureRmResourceGroup -Tag $tags -Name examplegroup
```

*기존 태그를 포함하지 않는 리소스*에 태그를 추가하려면 다음을 사용합니다.

```powershell
$r = Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId $r.ResourceId -Force
```

*기존 태그를 포함하는 리소스*에 태그를 추가하려면 다음을 사용합니다.

```powershell
$r = Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup
$r.Tags.Add("Status", "Approved") 
Set-AzureRmResource -Tag $r.Tags -ResourceId $r.ResourceId -Force
```

*리소스의 기존 태그를 유지하지 않고* 리소스 그룹의 모든 태그를 리소스에 적용하려면 다음 스크립트를 사용합니다.

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups)
{
    Get-AzureRmResource -ResourceGroupName $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
}
```

*리소스의 중복되지 않는 기존 태그를 유지하지 않고* 리소스 그룹의 모든 태그를 리소스에 적용하려면 다음 스크립트를 사용합니다.

```powershell
$group = Get-AzureRmResourceGroup "examplegroup"
if ($group.Tags -ne $null) {
    $resources = Get-AzureRmResource -ResourceGroupName $group.ResourceGroupName
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzureRmResource -ResourceId $r.ResourceId).Tags
        if ($resourcetags)
        {
            foreach ($key in $group.Tags.Keys)
            {
                if (-not($resourcetags.ContainsKey($key)))
                {
                    $resourcetags.Add($key, $group.Tags[$key])
                }
            }
            Set-AzureRmResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
    }
}
```

모든 태그를 제거하려면 빈 해시 테이블을 전달합니다.

```powershell
Set-AzureRmResourceGroup -Tag @{} -Name examplegroup
```

## <a name="azure-cli"></a>Azure CLI

*리소스 그룹*에 대한 기존 태그를 보려면 다음을 사용합니다.

```azurecli
az group show -n examplegroup --query tags
```

그러면 스크립트가 다음 형식을 반환합니다.

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

또는 *지정된 이름, 형식 및 리소스 그룹을 포함한 리소스*에 대한 기존 태그를 보려면 다음을 사용합니다.

```azurecli
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

리소스 컬렉션을 반복할 때 리소스 ID별로 리소스를 표시하려고 할 수 있습니다. 전체 예제는 이 문서의 뒷부분에 나와 있습니다. *지정된 리소스 ID를 포함한 리소스*에 대한 기존 태그를 보려면 다음을 사용합니다.

```azurecli
az resource show --id <resource-id> --query tags
```

특정 태그가 있는 리소스 그룹을 가져오려면 `az group list`를 사용합니다.

```azurecli
az group list --tag Dept=IT
```

특정 태그 및 값이 있는 모든 리소스를 가져오려면 `az resource list`를 사용합니다.

```azurecli
az resource list --tag Dept=Finance
```

리소스 또는 리소스 그룹에 태그를 적용할 때마다 해당 리소스 또는 리소스 그룹의 기존 태그가 덮어써집니다. 따라서 리소스 또는 리소스 그룹에 기존 태그가 있는지에 따라 다른 방법을 사용해야 합니다.

*기존 태그를 포함하지 않는 리소스 그룹*에 태그를 추가하려면 다음을 사용합니다.

```azurecli
az group update -n examplegroup --set tags.Environment=Test tags.Dept=IT
```

*기존 태그를 포함하지 않는 리소스*에 태그를 추가하려면 다음을 사용합니다.

```azurecli
az resource tag --tags Dept=IT Environment=Test -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

태그가 이미 있는 리소스에 태그를 추가하려면 기존 태그를 검색하고, 해당 값의 서식을 다시 지정한 후 기존 태그와 새 태그를 다시 적용합니다. 

```azurecli
jsonrtag=$(az resource show -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

*리소스의 기존 태그를 유지하지 않고* 리소스 그룹의 모든 태그를 리소스에 적용하려면 다음 스크립트를 사용합니다.

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    az resource tag --tags $t --id $resid
  done
done
```

리소스 그룹의 모든 태그를 리소스에 적용하고 *리소스의 기존 태그를 유지*하려면 다음 스크립트를 사용합니다.

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    jsonrtag=$(az resource show --id $resid --query tags)
    rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
    az resource tag --tags $t$rt --id $resid
  done
done
```

## <a name="templates"></a>템플릿

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="portal"></a>포털

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>REST API

Azure Portal 및 PowerShell 둘 다 백그라운드에서 [Resource Manager REST API](https://docs.microsoft.com/rest/api/resources/) 를 사용합니다. 태그를 다른 환경으로 통합해야 하는 경우 리소스 ID에 대해 **GET**을 사용하여 태그를 가져온 다음 **PATCH** 호출을 사용하여 태그 집합을 업데이트할 수 있습니다.

## <a name="tags-and-billing"></a>태그 및 청구

태그를 사용하여 청구 데이터를 그룹화할 수 있습니다. 예를 들어 서로 다른 조직에 여러 VM을 실행하는 경우 태그를 사용하여 비용 센터별로 사용량을 그룹화합니다. 또한 프로덕션 환경에서 실행 중인 VM에 대한 청구 사용량과 같이 런타임 환경별로 비용을 분류하는 데 태그를 사용할 수도 있습니다.

[Azure 리소스 사용 및 RateCard API](../billing/billing-usage-rate-card-overview.md) 또는 사용 CSV(쉼표로 구분된 값) 파일을 통해 태그에 대한 정보를 검색할 수 있습니다. [Azure 계정 포털](https://account.windowsazure.com/) 또는 [EA 포털](https://ea.azure.com)에서 사용 현황 파일을 다운로드할 수 있습니다. 대금 청구 정보에 프로그래밍 방식으로 액세스하는 방법은 [Microsoft Azure 리소스 소비에 대한 통찰력 얻기](../billing/billing-usage-rate-card-overview.md)를 참조하세요. REST API 작업에 대한 내용은 [Azure 청구 REST API 참조](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)를 참조하세요.

대금 청구에 태그를 지원하는 서비스용 사용 CSV를 다운로드하면 **태그** 열에 태그가 나타납니다. 자세한 내용은 [Microsoft Azure 청구서 이해](../billing/billing-understand-your-bill.md)를 참조하세요.

![요금 청구에 대한 태그를 참조하십시오.](./media/resource-group-using-tags/billing_csv.png)

## <a name="next-steps"></a>다음 단계

* 사용자 지정된 정책을 사용하여 구독 전체에 제한 사항 및 규칙을 적용할 수 있습니다. 정의한 정책을 사용하려면 모든 리소스에 특정 태그 값이 있어야 할 수도 있습니다. 자세한 내용은 [Azure Policy란?](../azure-policy/azure-policy-introduction.md)을 참조하세요.
* 리소스 배포 시 Azure PowerShell 사용에 대한 소개는 [Azure Resource Manager에서 Azure PowerShell 사용](powershell-azure-resource-manager.md)을 참조하세요.
* 리소스 배포 시 Azure CLI 사용에 대한 소개는 [Azure Resource Manager에서 Mac, Linux 및 Windows용 Azure CLI 사용](xplat-cli-azure-resource-manager.md)을 참조하세요.
* 포털 사용에 대한 소개는 [Azure Portal을 사용하여 Azure 리소스 관리](resource-group-portal.md)를 참조하세요.  
* 엔터프라이즈에서 리소스 관리자를 사용하여 구독을 효과적으로 관리할 수 있는 방법에 대한 지침은 [Azure 엔터프라이즈 스캐폴드 - 규범적 구독 거버넌스](resource-manager-subscription-governance.md)를 참조하세요.
