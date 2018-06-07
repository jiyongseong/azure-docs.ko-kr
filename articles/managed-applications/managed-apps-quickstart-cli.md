---
title: Azure CLI를 사용하여 Azure 관리되는 응용 프로그램 만들기 | Microsoft Docs
description: 조직의 구성원을 위한 Azure 관리되는 응용 프로그램을 만드는 방법이 나와 있습니다.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.date: 04/13/2018
ms.author: tomfitz
ms.openlocfilehash: f84fdd421ec6857cd940108546a16eb47770c766
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34305296"
---
# <a name="create-and-deploy-an-azure-managed-application-with-azure-cli"></a>Azure CLI를 사용하여 Azure 관리되는 응용 프로그램 만들기 및 배포

이 문서에서는 관리되는 응용 프로그램의 작업에 대해 소개합니다. 조직의 사용자에 대한 내부 카탈로그에 관리되는 응용 프로그램 정의를 추가합니다. 그런 다음 관리되는 해당 응용 프로그램을 구독에 배포합니다. 간략히 소개하기 위해 이미 관리되는 응용 프로그램용 파일을 빌드했습니다. 한 파일에서는 솔루션에 대한 인프라를 정의합니다. 다른 하나의 파일에서는 포털을 통해 관리되는 응용 프로그램을 배포하기 위한 사용자 인터페이스를 정의합니다. 이러한 파일은 GitHub를 통해 사용할 수 있습니다. [서비스 카탈로그 응용 프로그램 만들기](publish-service-catalog-app.md) 자습서에서 이러한 파일을 빌드하는 방법을 알아볼 수 있습니다.

작업이 완료되면 관리되는 응용 프로그램의 서로 다른 부분이 포함된 세 개의 리소스 그룹이 있습니다.

| 리소스 그룹 | contains | 설명 |
| -------------- | -------- | ----------- |
| appDefinitionGroup | 관리되는 응용 프로그램 정의 | 게시자가 이 리소스 그룹 및 관리되는 응용 프로그램 정의를 만듭니다. 관리되는 응용 프로그램 정의에 액세스할 수 있는 모든 사용자는 해당 응용 프로그램을 배포할 수 있습니다. |
| applicationGroup | 관리되는 응용 프로그램 인스턴스 | 소비자가 이 리소스 그룹 및 관리되는 응용 프로그램 인스턴스를 만듭니다. 소비자는 이 인스턴스를 통해 관리되는 응용 프로그램을 업데이트할 수 있습니다. |
| infrastructureGroup | 관리되는 응용 프로그램에 대한 리소스 | 이 리소스 그룹은 관리되는 응용 프로그램을 만들 때 자동으로 만들어집니다. 게시자가 이 리소스 그룹에 액세스하여 응용 프로그램을 관리합니다. 소비자는 리소스 그룹에 대한 제한된 액세스 권한을 가집니다. |

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group-for-definition"></a>정의에 대한 리소스 그룹 만들기

관리되는 응용 프로그램 정의는 리소스 그룹에 있습니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컬렉션입니다.

리소스 그룹을 만들려면 다음 명령을 사용합니다.

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

## <a name="create-the-managed-application-definition"></a>관리되는 응용 프로그램 정의 만들기

관리되는 응용 프로그램을 정의하는 경우 소비자를 대신하여 리소스를 관리하는 사용자, 그룹 또는 응용 프로그램을 선택합니다. 이 ID에는 할당된 역할에 따라 관리되는 리소스 그룹에 대한 권한이 있습니다. 일반적으로 Azure Active Directory 그룹을 만들어 리소스를 관리합니다. 그러나 이 문서에서는 자신의 ID를 사용합니다.

ID의 개체 ID를 가져오려면 다음 명령에서 UPN(사용자 계정 이름)을 입력합니다.

```azurecli-interactive
userid=$(az ad user show --upn-or-object-id example@contoso.org --query objectId --output tsv)
```

다음으로 사용자에게 액세스 권한을 부여하려는 RBAC 기본 제공 역할에 대한 역할 정의 ID가 필요합니다. 다음 명령은 소유자 역할에 대한 역할 정의 ID를 가져오는 방법을 보여 줍니다.

```azurecli-interactive
roleid=$(az role definition list --name Owner --query [].name --output tsv)
```

이제 관리되는 응용 프로그램 정의 리소스를 만듭니다. 관리되는 응용 프로그램에는 하나의 저장소 계정만 포함됩니다.

```azurecli-interactive
az managedapp definition create \
  --name "ManagedStorage" \
  --location "westcentralus" \
  --resource-group appDefinitionGroup \
  --lock-level ReadOnly \
  --display-name "Managed Storage Account" \
  --description "Managed Azure Storage Account" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "https://raw.githubusercontent.com/Azure/azure-managedapp-samples/master/samples/201-managed-storage-account/managedstorage.zip"
```

명령이 완료되면 리소스 그룹에 관리되는 응용 프로그램 정의가 있습니다. 

앞의 예제에서 사용된 몇 가지 매개 변수는 다음과 같습니다.

* **resource-group**: 관리되는 응용 프로그램 정의를 만든 리소스 그룹의 이름입니다.
* **lock-level**: 관리되는 리소스 그룹에 배치하는 잠금 유형입니다. 고객이 이 리소스 그룹에서 바람직하지 않은 작업을 수행할 수 없게 합니다. 현재 지원되는 유일한 잠금 수준은 ReadOnly입니다. ReadOnly를 지정하는 경우 고객은 관리되는 리소스 그룹에 있는 리소스만 읽을 수 있습니다. 관리되는 리소스 그룹에 대한 액세스 권한이 부여된 게시자 ID는 잠금에서 제외됩니다.
* **authorizations**: 관리되는 리소스 그룹에 권한을 부여하는 데 사용되는 보안 주체 ID 및 역할 정의 ID를 설명합니다. `<principalId>:<roleDefinitionId>` 형식으로 지정됩니다. 이 속성에는 여러 값을 지정할 수도 있습니다. 여러 값이 필요하면 `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>` 형식으로 지정해야 합니다. 여러 값은 공백으로 구분됩니다.
* **package-file-uri**: 필요한 파일이 포함되어 있는 .zip 패키지의 위치입니다. 패키지에는 적어도 **mainTemplate.json** 및 **createUiDefinition.json** 파일이 포함되어 있습니다. **mainTemplate.json**은 관리되는 응용 프로그램의 일부로 프로비전되는 Azure 리소스를 정의합니다. 템플릿은 일반 Resource Manager 템플릿과 차이가 없습니다. **createUiDefinition.json**은 포털을 통해 관리되는 응용 프로그램을 만드는 사용자를 위한 사용자 인터페이스를 생성합니다.

## <a name="create-resource-group-for-managed-application"></a>관리되는 응용 프로그램에 대한 리소스 그룹 만들기

이제 관리되는 응용 프로그램을 배포할 준비가 되었습니다. 

배포된 관리되는 응용 프로그램을 포함하려면 리소스 그룹이 필요합니다. 위치에 대해 **westcentralus**를 사용합니다.

```azurecli-interactive
az group create --name applicationGroup --location westcentralus
```

## <a name="deploy-the-managed-application"></a>관리되는 응용 프로그램 배포

다음 명령을 사용하여 응용 프로그램을 배포합니다.

```azurecli-interactive
appid=$(az managedapp definition show --name ManagedStorage --resource-group appDefinitionGroup --query id --output tsv)
subid=$(az account show --query id --output tsv)
managedGroupId=/subscriptions/$subid/resourceGroups/infrastructureGroup

az managedapp create \
  --name storageApp \
  --location "westcentralus" \
  --kind "Servicecatalog" \
  --resource-group applicationGroup \
  --managedapp-definition-id $appid \
  --managed-rg-id $managedGroupId \
  --parameters "{\"storageAccountNamePrefix\": {\"value\": \"storage\"}, \"storageAccountType\": {\"value\": \"Standard_LRS\"}}"
```

앞의 예제에서 사용된 몇 가지 매개 변수는 다음과 같습니다.

* **managedapp-definition-id**: 이 문서 앞부분에서 만든 정의의 ID입니다.
* **managed-rg-id**: 관리되는 응용 프로그램과 관련된 리소스에 대한 리소스 그룹의 ID. 명령은 이 리소스 그룹을 만듭니다. **명령을 실행하기 전에 리소스 그룹이 존재하지 않아야 합니다**. 이 리소스 그룹은 게시자에서 관리됩니다. 
* **resource-group**: 관리되는 응용 프로그램 리소스가 생성되는 리소스 그룹입니다.
* **parameters**: 관리되는 응용 프로그램과 관련된 리소스에 필요한 매개 변수입니다.

배포가 성공적으로 완료되면 관리되는 응용 프로그램이 applicationGroup에 만들어졌음을 알 수 있습니다. storageAccount 리소스는 infrastructureGroup에 만들어집니다.

## <a name="next-steps"></a>다음 단계

* 관리되는 응용 프로그램에 대한 소개는 [관리되는 응용 프로그램 개요](overview.md)를 참조하세요.
* 파일의 예제는 [관리되는 응용 프로그램 샘플](https://github.com/Azure/azure-managedapp-samples/tree/master/samples)을 참조하세요.
* 관리되는 응용 프로그램에 대한 UI 정의 파일을 만드는 방법은 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
