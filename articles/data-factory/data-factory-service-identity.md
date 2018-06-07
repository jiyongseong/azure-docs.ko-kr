---
title: Azure Data Factory ACS 서비스 ID | Microsoft Docs
description: Azure Data Factory에서 데이터 팩터리 서비스 ID에 대해 알아봅니다.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: jingwang
ms.openlocfilehash: 06e07d566afe64b55470f73e232cf45feccd47fb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-data-factory-service-identity"></a>Azure Data Factory 서비스 ID

이 문서를 통해 데이터 팩터리 서비스 ID의 정의 및 작동 방식을 이해할 수 있습니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. 일반 공급(GA)되는 Data Factory 버전 1 서비스를 사용하는 경우 [Data Factory 버전 1 설명서](v1/data-factory-introduction.md)를 참조하세요.

## <a name="overview"></a>개요

데이터 팩터리를 만들 때 팩터리가 만들어지면서 서비스 ID도 생성할 수 있습니다. 서비스 ID는 Azure Activity Directory에 등록된 관리되는 응용 프로그램이고 이 특정 데이터 팩터리를 나타냅니다.

데이터 팩터리 서비스 ID는 다음과 같은 두 가지 기능을 제공합니다.

- [Azure Key Vault에서 자격 증명을 저장](store-credentials-in-key-vault.md)하는 경우에 데이터 팩터리 서비스 ID를 Azure Key Vault 인증에 사용합니다.
- [Azure Data Lake Store 간에 데이터를 복사](connector-azure-data-lake-store.md)하는 경우 지원되는 Data Lake Store 인증 형식 중 하나로 데이터 팩터리 서비스 ID를 사용할 수 있습니다.

## <a name="generate-service-identity"></a>서비스 ID 생성

데이터 팩터리 서비스 ID는 다음과 같이 생성됩니다.

- **Azure Portal 또는 PowerShell**을 통해 데이터 팩터리를 만들 때 서비스 ID가 ADF V2 공개 미리 보기이므로 항상 자동으로 생성됩니다.
- **SDK**를 통해 데이터 팩터리를 만들 때, 생성할 팩터리 개체에서 "Identity = new FactoryIdentity()"를 지정하는 경우에만 서비스 ID가 생성됩니다. [.NET 빠른 시작 - 데이터 팩터리 만들기](quickstart-create-data-factory-dot-net.md#create-a-data-factory)에서 예제를 참조하세요.
- **REST API**를 통해 데이터 팩터리를 만들 때, 요청 본문에 "identity" 섹션을 지정하는 경우에만 서비스 ID가 생성됩니다. [REST 빠른 시작 - 데이터 팩터리 만들기](quickstart-create-data-factory-rest-api.md#create-a-data-factory)에서 예제를 참조하세요.

데이터 팩터리에 다음 [서비스 ID 검색](#retrieve-service-identity) 지침과 관련된 서비스 ID가 없는 것이 확인되면 데이터 팩터리를 프로그래밍 방식으로 ID 초기자로 업데이트하여 명시적으로 생성할 수 있습니다.

- [PowerShell을 사용하여 서비스 ID 생성](#generate-service-identity-using-powershell)
- [REST API를 사용하여 서비스 ID 생성](#generate-service-identity-using-rest-api)
- [SDK를 사용하여 서비스 ID 생성](#generate-service-identity-using-sdk)

>[!NOTE]
>- 서비스 ID를 수정할 수 없습니다. 이미 서비스 ID가 있는 데이터 팩터리를 업데이트해도 영향은 없으며 서비스 ID는 변경되지 않습니다.
>- 팩터리 개체에 "identity" 매개 변수를 지정하지 않거나 REST 요청 본문에 "identity" 섹션을 지정하지 않고 서비스 ID가 이미 있는 데이터 팩터리를 업데이트하면 오류가 발생합니다.
>- 데이터 팩터리를 삭제하면 연결된 서비스 ID가 함께 삭제됩니다.

### <a name="generate-service-identity-using-powershell"></a>PowerShell을 사용하여 서비스 ID 생성

**Set-AzureRmDataFactoryV2** 명령을 다시 호출하면 새로 생성된 "Identity" 필드가 표시됩니다.

```powershell
PS C:\WINDOWS\system32> Set-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-service-identity-using-rest-api"></a>REST API를 사용하여 서비스 ID 생성

요청 본문의 "identity" 섹션에서 아래 API를 호출합니다.

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2017-09-01-preview
```

**요청 본문**: "identity": { "type": "SystemAssigned" }를 추가합니다.

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**응답**: 서비스 ID가 자동으로 생성되고 "identity" 섹션이 그에 따라 채워집니다.

```json
{
    "name": "ADFV2DemoFactory",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2017-09-01-preview"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "EastUS"
}
```

### <a name="generate-service-identity-using-sdk"></a>SDK를 사용하여 서비스 ID 생성

Identity=new FactoryIdentity()를 사용하여 데이터 팩터리 create_or_update 함수를 호출합니다. .NET을 사용하는 샘플 코드:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-service-identity"></a>서비스 ID 검색

Azure Portal에서 또는 프로그래밍 방식으로 서비스 ID를 검색할 수 있습니다. 다음 섹션에서는 몇 가지 샘플을 보여 줍니다.

>[!TIP]
> 서비스 ID가 표시되지 않으면 팩터리를 업데이트하여 [서비스 ID를 생성](#generate-service-identity)합니다.

### <a name="retrieve-service-identity-using-azure-portal"></a>Azure Portal을 사용하여 서비스 ID 검색

Azure Portal -> 데이터 팩터리 -> 설정 -> 속성에서 서비스 ID 정보를 찾을 수 있습니다.

- 서비스 ID ID
- 서비스 ID 테넌트
- **서비스 ID 응용 프로그램 ID** > 이 값 복사

![서비스 ID 검색](media/data-factory-service-identity/retrieve-service-identity-portal.png)

### <a name="retrieve-service-identity-using-powershell"></a>PowerShell을 사용하여 서비스 ID 검색

다음과 같이 특정 데이터 팩터리를 가져오는 경우 서비스 ID의 보안 주체 ID 및 테넌트 ID가 반환됩니다.

```powershell
PS C:\WINDOWS\system32> (Get-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

보안 주체 ID를 복사하고 보안 주체 ID를 매개 변수로 사용하여 아래 Azure Active Directory 명령을 실행합니다. 그러면 액세스 권한 부여하는 데 사용할 **ApplicationId**를 가져올 수 있습니다.

```powershell
PS C:\WINDOWS\system32> Get-AzureRmADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>다음 단계
데이터 팩터리 서비스 ID를 사용하는 시기 및 방법을 소개하는 다음 항목을 참조하세요.

- [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md)
- [관리 서비스 ID 인증을 사용하여 Azure Data Lake Store 간에 데이터 복사](connector-azure-data-lake-store.md)

관리 서비스 ID에 대한 자세한 배경은 [MSI 개요](~/articles/active-directory/msi-overview.md)를 참조하세요. 이 문서는 데이터 팩터리 서비스 ID를 기반으로 합니다. 