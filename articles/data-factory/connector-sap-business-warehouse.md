---
title: Azure Data Factory를 사용하여 SAP BW에서 데이터 복사 | Microsoft Docs
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 SAP Business Warehouse에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법을 알아봅니다.
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
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 900f6a2a8e75cc43a3cfaa0c9e7b8d91f57ea20d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Azure Data Factory를 사용하여 SAP Business Warehouse에서 데이터 복사
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1 - GA](v1/data-factory-sap-business-warehouse-connector.md)
> * [버전 2 - 미리 보기](connector-sap-business-warehouse.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 SAP BW(Business Warehouse)에서 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [V1의 SAP BW 커넥터](v1/data-factory-sap-business-warehouse-connector.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

SAP Business Warehouse에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 SAP Business Warehouse 커넥터는 다음을 지원합니다.

- SAP Business Warehouse **버전 7.x**
- MDX 쿼리를 사용하여 **InfoCubes 및 QueryCubes**(BEx 쿼리 포함)에서 데이터를 복사합니다.
- 기본 인증을 사용하여 데이터를 복사합니다.

## <a name="prerequisites"></a>필수 조건

이 SAP Business Warehouse 커넥터를 사용 하려면 다음을 수행해야 합니다.

- 자체 호스팅 Integration Runtime을 설정합니다. 자세한 내용은 [자체 호스팅 Integration Runtime](create-self-hosted-integration-runtime.md)을 참조하세요.
- 통합 런타임 컴퓨터에 **SAP NetWeaver 라이브러리**를 설치합니다. SAP Netweaver 라이브러리는 SAP 관리자를 통해 구하거나 [SAP 소프트웨어 다운로드 센터](https://support.sap.com/swdc)에서 바로 구할 수 있습니다. 최신 버전에 대한 다운로드 위치를 찾으려면 **SAP Note #1025361**을 검색합니다. 통합 런타임 설치와 일치하는 **64-비트** SAP NetWeaver 라이브러리를 선택해야 합니다. 그런 다음 SAP Note에 따라 SAP NetWeaver RFC SDK에 포함된 모든 파일을 설치합니다. SAP NetWeaver 라이브러리는 SAP Client Tools 설치에도 포함됩니다.

> [!TIP]
> NetWeaver RFC SDK에서 추출된 dll을 system32 폴더에 넣습니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 SAP Business Warehouse 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

SAP BW(Business Warehouse) 연결된 서비스에 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 형식 속성은 **SapBw**로 설정해야 합니다. | 예 |
| 서버 | SAP BW 인스턴스가 상주하는 서버의 이름. | 예 |
| systemNumber | SAP BW 시스템의 시스템 번호.<br/>허용되는 값: 문자열로 표현되는 두 자리 10진수 | 예 |
| clientId | SAP W 시스템에 있는 클라이언트의 클라이언트 ID.<br/>허용되는 값: 문자열로 표현되는 세 자리 10진수 | 예 |
| userName | SAP 서버에 대한 액세스 권한이 있는 사용자의 이름입니다. | 예 |
| 암호 | 사용자에 대한 암호입니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. [필수 조건](#prerequisites)에 설명된 대로 자체 호스팅 Integration Runtime이 필요합니다. |예 |

**예제:**

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 집합 속성

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 데이터 집합 문서를 참조하세요. 이 섹션에는 SAP BW 데이터 집합에서 지원하는 속성의 목록을 제공합니다.

SAP BW에서 데이터를 복사하려면 데이터 집합의 type 속성을 **RelationalTable**로 설정합니다. 반면에 RelationalTable 형식의 SAP BW 데이터 집합에 대해 지원되는 type별 속성은 없습니다.

**예제:**

```json
{
    "name": "SAPBWDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<SAP BW linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 SAP BW 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="sap-bw-as-source"></a>SAP BW를 원본으로

SAP BW에서 데이터를 복사하려면 복사 작업의 원본 형식을 **RelationalSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 작업 원본의 형식 속성을 **RelationalSource**로 설정해야 합니다. | 예 |
| 쿼리 | SAP BW 인스턴스에서 데이터를 읽을 MDX 쿼리를 지정합니다. | 예 |

**예제:**

```json
"activities":[
    {
        "name": "CopyFromSAPBW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW input dataset name>",
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
                "type": "RelationalSource",
                "query": "<MDX query for SAP BW>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw"></a>SAP BW에 대한 데이터 형식 매핑

SAP BW에서 데이터를 복사할 경우 SAP BW 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로 다음 매핑이 사용됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| SAP BW 데이터 형식 | Data Factory 중간 데이터 형식 |
|:--- |:--- |
| ACCP | int |
| CHAR | 문자열 |
| CLNT | 문자열 |
| CURR | 10진수 |
| CUKY | 문자열 |
| DEC | 10진수 |
| FLTP | Double |
| INT1 | Byte |
| INT2 | Int16 |
| INT4 | int |
| LANG | 문자열 |
| LCHR | 문자열 |
| LRAW | Byte[] |
| PREC | Int16 |
| QUAN | 10진수 |
| RAW | Byte[] |
| RAWSTRING | Byte[] |
| STRING | 문자열 |
| 단위 | 문자열 |
| DATS | 문자열 |
| NUMC | 문자열 |
| TIMS | 문자열 |


## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.