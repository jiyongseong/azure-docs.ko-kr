---
title: Azure Data Factory를 사용하여 데이터 증분 복사 | Microsoft Docs
description: 이 자습서는 원본 데이터 저장소에서 대상 데이터 저장소로 데이터를 증분 복사하는 방법을 보여줍니다. 첫 번째 복사는 한 테이블에서 데이터를 복사합니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: shlo
ms.openlocfilehash: 7265e20bf89cc9dbc1c44e568e779f2d13f87685
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>원본 데이터 저장소에서 대상 데이터 저장소로 데이터 증분 로드

데이터 통합 솔루션에서 초기 전체 데이터 로드 후 증분 방식(또는 델타)으로 데이터를 로드하는 것은 널리 사용되는 시나리오입니다. 이 섹션의 자습서에서는 Azure Data Factory 버전 2를 사용하여 증분 방식으로 데이터를 로드하는 다양한 방법을 보여줍니다.

## <a name="delta-data-loading-by-using-a-watermark"></a>워터마크를 사용하여 델타 데이터 로드
이 경우 워터마크는 원본 데이터베이스에 정의합니다. 워터마크는 마지막으로 업데이트된 타임스탬프 또는 증분 키가 있는 열입니다. 델타 로딩 솔루션은 이전 워터마크와 새 워터마크 사이에 변경된 데이터를 로드합니다. 이 방식에 대한 워크플로는 다음 다이어그램과 같습니다. 

![워터마크 사용을 위한 워크플로](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

단계별 지침은 다음 자습서를 참조하세요. 

- [Azure SQL Database의 한 테이블에서 Azure Blob 저장소로 데이터 증분 복사](tutorial-incremental-copy-powershell.md)
- [온-프레미스 SQL Server의 여러 테이블에서 Azure SQL Database로 데이터 증분 복사](tutorial-incremental-copy-multiple-tables-powershell.md)


## <a name="delta-data-loading-by-using-the-change-tracking-technology"></a>변경 내용 추적 기술을 사용한 델타 데이터 로드
변경 내용 추적 기술은 응용 프로그램에 효율적인 변경 내용 추적 메커니즘을 제공하는 SQL Server 및 Azure SQL Database의 경 솔루션입니다. 이를 통해 응용 프로그램에서 삽입되거나 업데이트되거나 삭제된 데이터를 쉽게 식별할 수 있습니다. 

이 방식에 대한 워크플로는 다음 다이어그램과 같습니다.

![변경 내용 추적 사용을 위한 워크플로](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

단계별 지침은 다음 자습서를 참조하세요. <br/>
[변경 내용 추적 기술을 사용하여 Azure SQL Database에서 Azure Blob 저장소로 데이터 증분 복사](tutorial-incremental-copy-change-tracking-feature-powershell.md)


## <a name="next-steps"></a>다음 단계
다음 자습서를 진행합니다. 

> [!div class="nextstepaction"]
>[Azure SQL Database의 한 테이블에서 Azure Blob 저장소로 데이터 증분 복사](tutorial-incremental-copy-powershell.md)
