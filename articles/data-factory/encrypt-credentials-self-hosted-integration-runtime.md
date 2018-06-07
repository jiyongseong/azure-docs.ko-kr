---
title: Azure Data Factory에서 자격 증명 암호화 | Microsoft Docs
description: 자체 호스팅 통합 런타임을 사용하는 컴퓨터에서 온-프레미스 데이터 저장소의 자격 증명을 암호화하고 저장하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: 570ebe99066ecdaf2531751abb2e3fb821f29f64
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Azure Data Factory에서 온-프레미스 데이터 저장소에 대한 자격 증명 암호화
자체 호스팅 통합 런타임을 사용하는 컴퓨터에서 온-프레미스 데이터 저장소(중요한 정보가 있는 연결된 서비스)의 자격 증명을 암호화하고 저장할 수 있습니다. 

자격 증명이 있는 JSON 정의 파일을 <br/>[**New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential**](https://docs.microsoft.com/powershell/module/azurerm.datafactoryv2/New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential?view=azurermps-4.4.0) cmdlet에 전달하여 암호화된 자격 증명이 있는 출력 JSON 정의 파일을 생성합니다. 그런 다음 업데이트된 JSON 정의를 사용하여 연결된 서비스를 만듭니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [Data Factory 버전 1 설명서](v1/data-factory-introduction.md)를 참조하세요.

## <a name="author-sql-server-linked-service"></a>SQL Server 연결된 서비스 작성
다음 콘텐츠로 원하는 폴더에 **SqlServerLinkedService.json**이라는 JSON 파일을 만듭니다.  

파일을 저장하기 전에 `<servername>`, `<databasename>`, `<username>` 및 `<password>`를 SQL Server 값으로 바꿉니다. 그리고 `<integration runtime name>`을 통합 런타임의 이름으로 바꿉니다. 

```json
{
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
            }
        },
        "connectVia": {
            "type": "integrationRuntimeReference",
            "referenceName": "<integration runtime name>"
        },
        "name": "SqlServerLinkedService"
    }
}
```

## <a name="encrypt-credentials"></a>자격 증명 암호화
온-프레미스 자체 호스팅 통합 런타임에서 JSON 페이로드의 중요한 데이터를 암호화하기 위해 **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential**을 실행하고 JSON 페이로드를 전달합니다. 이 cmdlet을 사용하면 자격 증명이 DPAPI를 사용하여 암호화되고 자체 호스팅 통합 런타임 노드에 로컬로 저장됩니다. 출력 페이로드는 암호화된 자격 증명을 포함하는 다른 JSON 파일(이 경우 'encryptedLinkedService.json')로 리디렉션될 수 있습니다.

```powershell
New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>암호화된 자격 증명이 있는 JSON 사용
이제 암호화된 자격 증명이 포함된 이전 명령의 출력 JSON 파일을 사용하여 **SqlServerLinkedService**를 설정합니다.

```powershell
Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>다음 단계
데이터 이동에 대한 보안 고려 사항에 대한 정보는 [ 데이터 이동 보안 고려 사항 ](data-movement-security-considerations.md)을 참조하십시오.

