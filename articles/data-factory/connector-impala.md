---
title: Azure Data Factory를 사용하여 Impala에서 데이터 복사(베타) | Microsoft Docs
description: 데이터 팩터리 파이프라인에서 복사 작업을 사용하여 Impala에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 22e1ddbd9f621a3ef270c4b769032e6456b54fc9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-impala-by-using-azure-data-factory-beta"></a>Azure Data Factory를 사용하여 Impala에서 데이터 복사(베타)

이 문서에서는 Azure Data Factory에서 복사 작업을 사용하여 Impala에서 데이터를 복사하는 방법에 대해 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. 일반 공급 상태인 Data Factory 버전 1을 사용하는 경우 [버전 1의 복사 작업](v1/data-factory-data-movement-activities.md)을 참조하세요.

> [!IMPORTANT]
> 이 커넥터는 현재 베타 버전입니다. 사용해 보고 피드백을 제공할 수 있습니다. 프로덕션 환경에서는 사용하지 마세요.

## <a name="supported-capabilities"></a>지원되는 기능

Impala에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본 또는 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

 데이터 팩터리는 연결을 허용하는 기본 제공 드라이버를 제공합니다. 따라서 이 커넥터를 사용하기 위해 드라이버를 수동으로 설치할 필요가 없습니다.

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

다음 섹션에서는 Impala 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

다음은 Impala 연결된 서비스에 대해 지원되는 속성입니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 형식 속성은 **Impala**로 설정됩니다. | 예 |
| host | Impala 서버의 IP 주소 또는 호스트 이름입니다(즉, 192.168.222.160).  | 예 |
| 포트 | Impala 서버가 클라이언트 연결을 수신하는 데 사용하는 TCP 포트입니다. 기본값은 21050입니다.  | 아니오 |
| authenticationType | 사용할 인증 유형입니다. <br/>허용되는 값은 **Anonymous**, **SASLUsername** 및 **UsernameAndPassword**입니다. | 예 |
| 사용자 이름 | Impala 서버에 액세스하는 데 사용되는 사용자 이름입니다. SASLUsername을 사용하는 경우 기본값은 익명입니다.  | 아니오 |
| 암호 | UsernameAndPassword를 사용할 때 사용자 이름에 해당하는 암호입니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 아니요 |
| enableSsl | 서버에 대한 연결이 SSL을 사용하여 암호화되는지 여부를 지정합니다. 기본값은 **false**입니다.  | 아니오 |
| trustedCertPath | SSL을 통해 연결할 때 서버를 확인하는 데 사용되는 신뢰할 수 있는 CA 인증서를 포함하는 .pem 파일의 전체 경로입니다. 자체 호스팅 Integration Runtime에서 SSL을 사용할 때만 이 속성을 설정할 수 있습니다. 기본값은 통합 런타임과 함께 설치된 cacerts.pem 파일입니다.  | 아니오 |
| useSystemTrustStore | 시스템 신뢰 저장소 또는 지정된 PEM 파일의 CA 인증서를 사용할지 여부를 지정합니다. 기본값은 **false**입니다.  | 아니오 |
| allowHostNameCNMismatch | SSL을 통해 연결할 때 CA 발급 SSL 인증서 이름이 서버의 호스트 이름과 일치하도록 할지 여부를 지정합니다. 기본값은 **false**입니다.  | 아니오 |
| allowSelfSignedServerCert | 서버의 자체 서명된 인증서를 허용할지 여부를 지정합니다. 기본값은 **false**입니다.  | 아니오 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. 자체 호스팅 Integration Runtime 또는 Azure Integration Runtime을 사용할 수 있습니다(데이터 저장소를 공개적으로 액세스할 수 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아니오 |

**예제:**

```json
{
    "name": "ImpalaLinkedService",
    "properties": {
        "type": "Impala",
        "typeProperties": {
            "host" : "<host>",
            "port" : "<port>",
            "authenticationType" : "UsernameAndPassword",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
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

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Impala 데이터 집합에서 지원하는 속성의 목록을 제공합니다.

Impala에서 데이터를 복사하려면 데이터 집합의 type 속성을 **ImpalaObject**로 설정합니다. 이 형식의 데이터 집합에는 추가적인 형식별 속성이 없습니다.

**예제**

```json
{
    "name": "ImpalaDataset",
    "properties": {
        "type": "ImpalaObject",
        "linkedServiceName": {
            "referenceName": "<Impala linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Impala 원본 형식에서 지원하는 속성의 목록을 제공합니다.

### <a name="impala-as-a-source-type"></a>원본 유형인 Impala

Impala에서 데이터를 복사하려면 복사 작업의 원본 형식을 **ImpalaSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 작업 원본의 형식 속성은 **ImpalaSource**로 설정되어야 합니다. | 예 |
| 쿼리 | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예는 `"SELECT * FROM MyTable"`입니다. | 예 |

**예제:**

```json
"activities":[
    {
        "name": "CopyFromImpala",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Impala input dataset name>",
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
                "type": "ImpalaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>다음 단계
Data Factory에서 복사 활동을 통해 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
