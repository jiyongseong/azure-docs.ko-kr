---
title: Azure Data Factory를 사용하여 ServiceNow에서 데이터 복사 | Microsoft Docs
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 ServiceNow에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: jingwang
ms.openlocfilehash: 29fb74e49ad3fdca0bc54a431da40b02ef24882b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="copy-data-from-servicenow-using-azure-data-factory"></a>Azure Data Factory를 사용하여 ServiceNow에서 데이터 복사

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 ServiceNow에서 데이터를 복사하는 방법에 대해 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [V1의 복사 작업](v1/data-factory-data-movement-activities.md)을 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

ServiceNow에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

Azure Data Factory는 연결을 사용하는 기본 제공 드라이버를 제공합니다. 따라서 이 커넥터를 사용하여 드라이버를 수동으로 설치하지 않아도 됩니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

다음 섹션에서는 ServiceNow 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

다음은 ServiceNow 연결된 서비스에 대해 지원되는 속성입니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | type 속성은 **ServiceNow**로 설정해야 합니다. | 예 |
| endpoint | ServiceNow 서버(`http://<instance>.service-now.com`)의 끝점입니다.  | 예 |
| authenticationType | 사용할 인증 유형입니다. <br/>허용되는 값은 **Basic**, **OAuth2**입니다. | 예 |
| 사용자 이름 | 기본 및 OAuth2 인증을 위해 ServiceNow 서버에 연결하는 데 사용되는 사용자 이름입니다.  | 아니오 |
| 암호 | 기본 및 OAuth2 인증의 사용자 이름에 해당하는 암호입니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 아니오 |
| clientId | OAuth2 인증의 클라이언트 ID입니다.  | 아니오 |
| clientSecret | OAuth2 인증의 클라이언트 암호입니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 아니오 |
| useEncryptedEndpoints | 데이터 원본 끝점이 HTTPS를 사용하여 암호화되는지 여부를 지정합니다. 기본값은 true입니다.  | 아니오 |
| useHostVerification | SSL을 통해 연결할 때 서버 인증서의 호스트 이름이 서버의 호스트 이름과 일치하도록 할지 여부를 지정합니다. 기본값은 true입니다.  | 아니오 |
| usePeerVerification | SSL을 통해 연결할 때 서버의 ID를 확인할지 여부를 지정합니다. 기본값은 true입니다.  | 아니오 |

**예제:**

```json
{
    "name": "ServiceNowLinkedService",
    "properties": {
        "type": "ServiceNow",
        "typeProperties": {
            "endpoint" : "http://<instance>.service-now.com",
            "authenticationType" : "Basic",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 집합 속성

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 ServiceNow 데이터 집합에서 지원하는 속성의 목록을 제공합니다.

ServiceNow에서 데이터를 복사하려면 데이터 집합의 type 속성을 **ServiceNowObject**로 설정합니다. 이 형식의 데이터 집합에는 추가적인 형식별 속성이 없습니다.

**예제**

```json
{
    "name": "ServiceNowDataset",
    "properties": {
        "type": "ServiceNowObject",
        "linkedServiceName": {
            "referenceName": "<ServiceNow linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 ServiceNow 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="servicenow-as-source"></a>ServiceNow 원본

ServiceNow에서 데이터를 복사하려면 복사 작업의 원본 형식을 **ServiceNowSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 작업 원본의 type 속성은 **ServiceNowSource**로 설정해야 합니다. | 예 |
| 쿼리 | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `"SELECT * FROM Actual.alm_asset"` | 예 |

쿼리에서 ServiceNow에 대한 스키마와 열을 지정할 때 다음 사항에 유의합니다.

- **스키마:** ServiceNow 쿼리에는 [ServiceNow restful API](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET)를 호출할 때 `sysparm_display_value`의 매개 변수를 true 또는 false로 볼 수 있는 `Actual` 또는 `Display`로 스키마를 지정합니다. 
- **열:** `Actual` 스키마의 실제 값에 대한 열 이름은 `[columne name]_value`이며 `Display` 스키마의 표시 값에 대한 열 이름은 `[columne name]_display_value`입니다. 열 이름은 쿼리에 사용되는 스키마에 매핑되어야 합니다.

**샘플 쿼리:**
`SELECT col_value FROM Actual.alm_asset` 또는 `SELECT col_display_value FROM Display.alm_asset`

**예제:**

```json
"activities":[
    {
        "name": "CopyFromServiceNow",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ServiceNow input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ServiceNowSource",
                "query": "SELECT * FROM Actual.alm_asset"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
