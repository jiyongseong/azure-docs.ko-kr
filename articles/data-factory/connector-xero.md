---
title: Azure Data Factory(베타)를 사용하여 Xero에서 데이터 복사 | Microsoft Docs
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 Xero에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
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
ms.date: 02/12/2018
ms.author: jingwang
ms.openlocfilehash: 131f147e2c445e8cfef12288d4d0d29f6fd7fe01
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32167554"
---
# <a name="copy-data-from-xero-using-azure-data-factory-beta"></a>Azure Data Factory(베타)를 사용하여 Xero에서 데이터 복사

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Xero에서 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [V1의 복사 작업](v1/data-factory-data-movement-activities.md)을 참조하세요.

> [!IMPORTANT]
> 이 커넥터는 현재 베타 버전입니다. 사용해 보고 피드백을 제공할 수 있습니다. 프로덕션 환경에서는 사용하지 마세요.

## <a name="supported-capabilities"></a>지원되는 기능

Xero에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 Xero 커넥터는 다음을 지원합니다.

- Xero [개인 응용 프로그램](https://developer.xero.com/documentation/getting-started/api-application-types)(공용 응용 프로그램은 제외)
- "보고서"를 제외한 모든 Xero 테이블(API 엔드포인트) 

Azure Data Factory는 연결을 사용하는 기본 제공 드라이버를 제공합니다. 따라서 이 커넥터를 사용하여 드라이버를 수동으로 설치하지 않아도 됩니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

다음 섹션에서는 Xero 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

다음은 Xero 연결된 서비스에 대해 지원되는 속성입니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | type 속성은 **Xero**로 설정해야 합니다. | 예 |
| host | Xero 서버(`api.xero.com`)의 끝점입니다.  | 예 |
| consumerKey | Xero 응용 프로그램과 연결된 소비자 키입니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| privateKey | Xero 개인 응용 프로그램에 대해 생성된 .pem 파일의 개인 키는 [공개/개인 키 쌍 만들기](https://developer.xero.com/documentation/api-guides/create-publicprivate-key)를 참조하세요. **512의 numbits로 privatekey.pem을 생성**하려면 `openssl genrsa -out privatekey.pem 512`를 사용합니다. 1024는 지원되지 않습니다. Unix 줄 끝(\n)을 포함하여 .pem 파일의 모든 텍스트를 포함합니다. 아래 샘플을 참조하세요.<br/><br/>이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| useEncryptedEndpoints | 데이터 원본 끝점이 HTTPS를 사용하여 암호화되는지 여부를 지정합니다. 기본값은 true입니다.  | 아니오 |
| useHostVerification | SSL을 통해 연결할 때 서버 인증서의 호스트 이름이 서버의 호스트 이름과 일치하도록 할지 여부를 지정합니다. 기본값은 true입니다.  | 아니오 |
| usePeerVerification | SSL을 통해 연결할 때 서버의 ID를 확인할지 여부를 지정합니다. 기본값은 true입니다.  | 아니오 |

**예제:**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "host" : "api.xero.com",
            "consumerKey": {
                 "type": "SecureString",
                 "value": "<consumerKey>"
            },
            "privateKey": {
                 "type": "SecureString",
                 "value": "<privateKey>"
            }
        }
    }
}
```

**샘플 개인 키 값:**

Unix 줄 끝(\n)을 포함하여 .pem 파일의 모든 텍스트를 포함합니다.

```
"-----BEGIN RSA PRIVATE KEY-----\nMII***************************************************P\nbu****************************************************s\nU/****************************************************B\nA*****************************************************W\njH****************************************************e\nsx*****************************************************l\nq******************************************************X\nh*****************************************************i\nd*****************************************************s\nA*****************************************************dsfb\nN*****************************************************M\np*****************************************************Ly\nK*****************************************************Y=\n-----END RSA PRIVATE KEY-----"
```

## <a name="dataset-properties"></a>데이터 집합 속성

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Xero 데이터 집합에서 지원하는 속성의 목록을 제공합니다.

Xero에서 데이터를 복사하려면 데이터 집합의 type 속성을 **XeroObject**로 설정합니다. 이 형식의 데이터 집합에는 추가적인 형식별 속성이 없습니다.

**예제**

```json
{
    "name": "XeroDataset",
    "properties": {
        "type": "XeroObject",
        "linkedServiceName": {
            "referenceName": "<Xero linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Xero 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="xero-as-source"></a>Xero 원본

Xero에서 데이터를 복사하려면 복사 작업의 원본 형식을 **XeroSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 작업 원본의 type 속성은 **XeroSource**로 설정해야 합니다. | 예 |
| 쿼리 | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `"SELECT * FROM Contacts"` | 예 |

**예제:**

```json
"activities":[
    {
        "name": "CopyFromXero",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Xero input dataset name>",
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
                "type": "XeroSource",
                "query": "SELECT * FROM Contacts"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Xero 쿼리를 지정할 때 다음 사항에 유의합니다.

- 복잡한 항목이 있는 테이블은 여러 테이블로 분할됩니다. 예를 들어 은행 거래에는 "LineItems"라는 복합 데이터 구조가 있기 때문에, 은행 거래의 데이터는 `Bank_Transaction` 및 `Bank_Transaction_Line_Items` 테이블로 매핑되고 `Bank_Transaction_ID`가 이들을 연결하는 외래 키로 사용됩니다.

- Xero 데이터는 `Minimal`(기본값) 및 `Complete`라는 두 가지 스키마를 통해 사용할 수 있습니다. Complete 스키마는 원하는 쿼리를 만들기 전에 추가 데이터(예: ID 열)가 필요한 필수 구성 요소 호출 테이블을 포함합니다.

다음 테이블에는 Minimal 및 Complete 스키마와 동일한 정보가 있습니다. API 호출 수를 줄이기 위해 Minimal 스키마(기본값)를 사용합니다.

- Bank_Transactions
- Contact_Groups 
- 연락처 
- Contacts_Sales_Tracking_Categories 
- Contacts_Phones 
- Contacts_Addresses 
- Contacts_Purchases_Tracking_Categories 
- Credit_Notes 
- Credit_Notes_Allocations 
- Expense_Claims 
- Expense_Claim_Validation_Errors
- 송장 
- Invoices_Credit_Notes
- Invoices_ Prepayments 
- Invoices_Overpayments 
- Manual_Journals 
- Overpayments 
- Overpayments_Allocations 
- Prepayments 
- Prepayments_Allocations 
- Receipts 
- Receipt_Validation_Errors 
- Tracking_Categories

다음 테이블은 complete 스키마로만 쿼리할 수 있습니다.

- Complete.Bank_Transaction_Line_Items 
- Complete.Bank_Transaction_Line_Item_Tracking 
- Complete.Contact_Group_Contacts 
- Complete.Contacts_Contact_ Persons 
- Complete.Credit_Note_Line_Items 
- Complete.Credit_Notes_Line_Items_Tracking 
- Complete.Expense_Claim_ Payments 
- Complete.Expense_Claim_Receipts 
- Complete.Invoice_Line_Items 
- Complete.Invoices_Line_Items_Tracking
- Complete.Manual_Journal_Lines 
- Complete.Manual_Journal_Line_Tracking 
- Complete.Overpayment_Line_Items 
- Complete.Overpayment_Line_Items_Tracking 
- Complete.Prepayment_Line_Items 
- Complete.Prepayment_Line_Item_Tracking 
- Complete.Receipt_Line_Items 
- Complete.Receipt_Line_Item_Tracking 
- Complete.Tracking_Category_Options

## <a name="next-steps"></a>다음 단계
복사 작업에서 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
