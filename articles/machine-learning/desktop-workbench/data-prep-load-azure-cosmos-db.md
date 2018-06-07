---
title: Azure Machine Learning Workbench에서 Azure Cosmos DB에 데이터 원본으로 연결 | Microsoft Docs
description: 이 문서에서는 Azure Machine Learning Workbench를 통해 Azure Cosmos DB에 연결하는 방법의 예제를 제공합니다.
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: d36b394a528dc4bc1b6e0a9e0e5dbde728cbee1b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="connecting-to-azure-cosmos-db-as-a-data-source"></a>Azure Cosmos DB에 데이터 원본으로 연결
이 문서에는 Azure Machine Learning Workbench에서 Cosmos DB에 연결할 수 있는 python 샘플이 포함되어 있습니다.

## <a name="load-azure-cosmos-db-data-into-data-preparation"></a>데이터 준비로 Azure Cosmos DB 데이터 로드

새 스크립트 기반 데이터 흐름을 만들고, 다음 스크립트를 사용하여 Azure Cosmos DB에서 데이터를 로드합니다. 

```python
import pydocumentdb
import pydocumentdb.document_client as document_client

import pandas as pd

config = { 
    'ENDPOINT': '<Endpoint>',
    'MASTERKEY': '<Key>',
    'DOCUMENTDB_DATABASE': '<DBName>',
    'DOCUMENTDB_COLLECTION': '<collectionname>'
};

# Initialize the Python DocumentDB client.
client = document_client.DocumentClient(config['ENDPOINT'], {'masterKey': config['MASTERKEY']})

# Read databases and take first since id should not be duplicated.
db = next((data for data in client.ReadDatabases() if data['id'] == config['DOCUMENTDB_DATABASE']))

# Read collections and take first since id should not be duplicated.
coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config['DOCUMENTDB_COLLECTION']))

docs = client.ReadDocuments(coll['_self'])

df = pd.DataFrame(list(docs))
```

## <a name="other-data-source-connections"></a>기타 데이터 원본 연결
기타 샘플은 [추가 데이터 원본 연결 예제](data-prep-appendix8-sample-source-connections-python.md)를 참조하세요.
