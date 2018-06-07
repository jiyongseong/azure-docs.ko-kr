---
title: 데이터 과학 Virtual Machine 데이터 수집 도구-Azure | Microsoft Docs
description: 데이터 과학 Virtual Machine 데이터 수집 도구
keywords: 데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 7aeb0476fffb8c9e5cf2b0b5d89a2a387bd6364a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>데이터 과학 Virtual Machine 데이터 수집 도구

데이터 과학 또는 AI 프로젝트에서 첫 번째 기술적 단계 중 하나는 사용할 데이터 집합을 식별하고 분석 환경으로 가져오는 것입니다. 데이터 과학 Virtual Machine(DSVM)은 서로 다른 원본에서 데이터를 로컬로 DSVM이나 클라우드 또는 온-프레미스의 데이터 플랫폼에 있는 분석 데이터 저장소로 가져올 도구 및 라이브러리를 제공합니다. 

다음은 DSVM에 제공한 일부 데이터 이동 도구입니다. 

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | Azure Storage Blob에서 Azure Data Lake Store로 데이터를 복사하는 도구. 또한 두 Azure Data Lake Store 계정 간에 데이터를 복사할 수도 있습니다.      |
| 지원되는 DSVM 버전      | Windows      |
| 일반적인 사용 용도      | Azure Storage에서 Azure Data Lake Store로 여러 BLOB 가져오기.      |
|  사용/실행 방법    |   명령 프롬프트를 연 다음 `adlcopy`을 입력하여 도움말을 봅니다.    |
| 샘플에 대한 링크      | [AdlCopy 사용](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| DSVM의 관련 도구      | AzCopy, Azure 명령줄     |

## <a name="azure-command-line"></a>Azure 명령줄

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | Azure용 관리 도구. Azure Storage Blob, Azure Data Lake storage와 같은 Azure 데이터 플랫폼에서 데이터를 이동하기 위한 동사형 명령을 포함합니다.     |
| 지원되는 DSVM 버전      | Windows, Linux     |
| 일반적인 사용 용도      | Azure Storage, Azure Data Lake Store 간 데이터 가져오기, 내보내기      |
|  사용/실행 방법    |   명령 프롬프트를 연 다음 `az`을 입력하여 도움말을 봅니다.    |
| 샘플에 대한 링크      | [Azure CLI 사용](https://docs.microsoft.com/cli/azure)     |
| DSVM의 관련 도구      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | 로컬 파일, Azure Storage Blob, 파일 및 테이블 간 데이터를 복사하는 도구.      |
| 지원되는 DSVM 버전      | Windows      |
| 일반적인 사용 용도      | 파일을 Blob Storage에 복사, 계정 간에 BLOB 복사.      |
|  사용/실행 방법    |   명령 프롬프트를 연 다음 `azcopy`을 입력하여 도움말을 봅니다.    |
| 샘플에 대한 링크      | [Windows에서 AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| DSVM의 관련 도구      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Azure Cosmos DB 데이터 마이그레이션 도구

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | JSON 파일, CSV 파일, SQL, MongoDB, Azure Table Storage, Amazon DynamoDB 및 Azure Cosmos DB SQL API 컬렉션을 비롯한 다양한 원본에서 데이터를 Azure Cosmos DB로 가져오는 도구입니다.      |
| 지원되는 DSVM 버전      | Windows      |
| 일반적인 사용 용도      | VM에서 CosmosDB로 파일 가져오기, Azure 테이블 저장소에서 CosmosDB로 데이터 가져오기 또는 SQL Server 데이터베이스에서 CosmosDB로 데이터 가져오기.     |
|  사용/실행 방법    |   명령줄을 버전을 사용하려면, 명령 프롬프트를 연 다음 `dt`을 입력합니다. GUI 도구를 사용하려면, 명령 프롬프트를 연 다음 `dtui`을 입력합니다.    |
| 샘플에 대한 링크      | [CosmosDB 데이터 가져오기](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| DSVM의 관련 도구      | AzCopy, AdlCopy      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | SQL Server와 데이터 파일 간에 데이터를 복사하는 SQL Server 도구입니다.      |
| 지원되는 DSVM 버전      | Windows      |
| 일반적인 사용 용도      | CSV 파일을 SQL Server 표로 가져오고, SQL Server 표를 파일로 내보냅니다.      |
|  사용/실행 방법    |   명령 프롬프트를 연 다음 `bcp`을 입력하여 도움말을 봅니다.    |
| 샘플에 대한 링크      | [대량 복사 유틸리티](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| DSVM의 관련 도구      | SQL Server, sqlcmd      |

## <a name="blobfuse"></a>blobfuse

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | Linux 파일 시스템에 Azure Blob 컨테이너를 마운트하는 도구      |
| 지원되는 DSVM 버전      | Linux      |
| 일반적인 사용 용도      | 컨테이너에서 Blob에 대한 읽기 및 쓰기      |
|  사용/실행 방법    |   터미널에서 _blobfuse_를 실행합니다.    |
| 샘플에 대한 링크      | [GitHub의 blobfuse](https://github.com/Azure/azure-storage-fuse)      |
| DSVM의 관련 도구      | Azure 명령줄      |


## <a name="microsoft-data-management-gateway"></a>Microsoft 데이터 관리 게이트웨이

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | 온-프레미스 데이터 원본을 사용하기 위해 클라우드 서비스에 연결하는 도구.      |
| 지원되는 DSVM 버전      | Windows      |
| 일반적인 사용 용도      | VM을 온-프레미스 데이터 원본에 연결합니다.      |
|  사용/실행 방법    |   시작 메뉴에서 "Microsoft 데이터 관리 게이트웨이"를 시작합니다.    |
| 샘플에 대한 링크      | [데이터 관리 게이트웨이](https://msdn.microsoft.com/library/dn879362.aspx)      |
| DSVM의 관련 도구      | AzCopy AdlCopy, bcp    |
