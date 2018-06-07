---
title: 'PowerShell 스크립트: Data Factory를 사용하여 온-프레미스에서 Azure로 데이터 복사 | Microsoft Docs'
description: 이 PowerShell 스크립트는 온-프레미스 SQL Server 데이터베이스의 데이터를 다른 Azure Blob Storage로 복사합니다.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: jingwang
ms.openlocfilehash: 5c0252e42dd3dd53a0a95684553b193b983e95ee
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-from-on-premises-to-azure"></a>PowerShell에서 온-프레미스의 데이터를 Azure로 복사하는 Data Factory 파이프라인 만들기

이 샘플 PowerShell 스크립트는 온-프레미스 SQL Server 데이터베이스에서 Azure Blob Storage로 데이터를 복사하는 파이프라인을 Azure Data Factory에서 만듭니다.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>필수 조건

- **SQL Server**. 이 샘플에서는 온-프레미스 SQL Server 데이터베이스를 **원본** 데이터 저장소로 사용합니다.
- **Azure Storage 계정**. 이 샘플에서는 Azure Blob Storage를 **대상/싱크** 데이터 저장소로 사용합니다. Azure Storage 계정이 없는 경우 새로 만드는 단계는 [저장소 계정 만들기](../../storage/common/storage-create-storage-account.md#create-a-storage-account) 문서를 참조하세요.
- **자체 호스팅 통합 런타임**. [다운로드 센터](https://www.microsoft.com/download/details.aspx?id=39717)에서 MSI 파일을 다운로드하고 실행하여 컴퓨터에 자체 호스팅 통합 런타임을 설치합니다.  

### <a name="create-sample-database-in-sql-server"></a>SQL Server에서 샘플 데이터베이스 만들기
1. 다음 SQL 스크립트를 사용하여 온-프레미스 SQL Server 데이터베이스에 **emp**라는 테이블을 만듭니다. 

   ```sql   
     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
         CONSTRAINT PK_emp PRIMARY KEY (ID)
     )
     GO
   ```

2. 테이블에 일부 샘플 데이터를 삽입합니다.

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```

## <a name="sample-script"></a>샘플 스크립트

> [!IMPORTANT]
> 이 스크립트는 하드 드라이브의 c:\ 폴더에 Data Factory 엔터티(연결된 서비스, 데이터 집합 및 파이프라인)를 정의하는 JSON 파일을 만듭니다.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-onprem-sql-server-to-azure-blob/copy-from-onprem-sql-server-to-azure-blob.ps1 "Copy from on-premises SQL Server -> Azure Blob Storage")]


## <a name="clean-up-deployment"></a>배포 정리

샘플 스크립트를 실행한 후에는 다음 명령을 사용하여 리소스 그룹 및 해당 그룹에 연결된 모든 리소스를 제거할 수 있습니다.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourceGroupName
```
리소스 그룹에서 Data Factory를 제거하려면 다음 명령을 실행합니다. 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 

| 명령 | 메모 |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [Set-AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2) | 데이터 팩터리를 만듭니다. |
| [New-AzureRmDataFactoryV2LinkedServiceEncryptCredential](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactoryv2linkedserviceencryptedcredential) | 연결된 서비스에서 자격 증명을 암호화하고 암호화된 자격 증명으로 새 연결된 서비스 정의를 생성합니다. 
| [Set-AzureRmDataFactoryV2LinkedService](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2linkedservice) | 데이터 팩터리에서 연결된 서비스를 만듭니다. 연결된 서비스는 데이터 저장소 또는 계산을 데이터 팩터리에 연결합니다. |
| [Set-AzureRmDataFactoryV2Dataset](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2dataset) | 데이터 팩터리에서 데이터 집합을 만듭니다. 데이터 집합은 파이프라인의 작업에 대한 입/출력을 나타냅니다. | 
| [Set-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactorv2ypipeline) | 데이터 팩터리에서 파이프라인을 만듭니다. 파이프라인에는 특정 작업을 수행하는 하나 이상의 작업이 있습니다. 이 파이프라인에서 복사 작업은 Azure Blob Storage의 한 위치에서 다른 위치로 데이터를 복사합니다. |
| [Invoke-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Invoke-azurermdatafactoryv2pipelinerun) | 파이프라인에 대한 실행을 만듭니다. 즉, 파이프라인을 실행합니다. |
| [Get-AzureRmDataFactoryV2ActivityRun](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2activityrun) | 파이프라인의 작업 실행 방법에 대한 세부 정보를 가져옵니다. 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/)를 참조하세요.

추가 Azure Data Factory PowerShell 스크립트 샘플은 [Azure Data Factory PowerShell 샘플](../samples-powershell.md)에 있습니다.