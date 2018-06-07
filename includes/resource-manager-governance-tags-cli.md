---
title: "포함 파일"
description: "포함 파일"
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/20/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: b9484336add0719749e9f0af56bdd70fa3906ef5
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/23/2018
---
리소스 그룹에 두 개의 태그를 추가하려면 [az group update](/cli/azure/group#az_group_update) 명령을 사용합니다.

```azurecli-interactive
az group update -n myResourceGroup --set tags.Environment=Test tags.Dept=IT
```

세 번째 태그를 추가하려 한다고 가정해보겠습니다. 새로운 태그로 명령을 다시 실행합니다. 기존 태그에 추가됩니다.

```azurecli-interactive
az group update -n myResourceGroup --set tags.Project=Documentation
```

리소스는 리소스 그룹에서 태그를 상속하지 않습니다. 현재, 리소스 그룹에 3개의 태그가 있지만 리소스에는 태그가 없습니다. 리소스 그룹의 모든 태그를 리소스에 적용하고 리소스의 기존 태그를 유지하려면 다음 스크립트를 사용합니다.

```azurecli-interactive
# Get the tags for the resource group
jsontag=$(az group show -n myResourceGroup --query tags)

# Reformat from JSON to space-delimited and equals sign
t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')

# Get the resource IDs for all resources in the resource group
r=$(az resource list -g myResourceGroup --query [].id --output tsv)

# Loop through each resource ID
for resid in $r
do
  # Get the tags for this resource
  jsonrtag=$(az resource show --id $resid --query tags)
  
  # Reformat from JSON to space-delimited and equals sign
  rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
  
  # Reapply the updated tags to this resource
  az resource tag --tags $t$rt --id $resid
done
```

또는 기존 태그를 유지하지 않고 리소스 그룹의 태그를 리소스에 적용할 수도 있습니다.

```azurecli-interactive
# Get the tags for the resource group
jsontag=$(az group show -n myResourceGroup --query tags)

# Reformat from JSON to space-delimited and equals sign
t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')

# Get the resource IDs for all resources in the resource group
r=$(az resource list -g myResourceGroup --query [].id --output tsv)

# Loop through each resource ID
for resid in $r
do
  # Apply tags from resource group to this resource
  az resource tag --tags $t --id $resid
done
```

여러 값을 단일 태그에 결합하려면 JSON 문자열을 사용합니다.

```azurecli-interactive
az group update -n myResourceGroup --set tags.CostCenter='{"Dept":"IT","Environment":"Test"}'
```

리소스 그룹의 모든 태그를 제거 하려면 다음을 사용합니다.

```azurecli-interactive
az group update -n myResourceGroup --remove tags
```
