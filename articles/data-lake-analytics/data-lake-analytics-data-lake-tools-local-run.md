---
title: "로컬 실행 및 Azure Data Lake U-SQL SDK를 사용하여 U-SQL 작업 테스트 및 디버그 | Microsoft Docs"
description: "Azure Data Lake Tools for Visual Studio 및 Azure Data Lake U-SQL SDK를 사용하여 로컬 워크스테이션에서 U-SQL 작업을 테스트하고 디버그하는 방법에 대해 알아봅니다."
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: yanacai
ms.openlocfilehash: 771a96df5cc66bac46e7144785be8cc072b57b31
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="test-and-debug-u-sql-jobs-by-using-local-run-and-the-azure-data-lake-u-sql-sdk"></a>로컬 실행 및 Azure Data Lake U-SQL SDK를 사용하여 U-SQL 작업 테스트 및 디버그

Azure Data Lake 서비스의 경우처럼 Visual Studio와 Azure Data Lake U-SQL SDK에 대해 Azure Data Lake 도구를 사용하여 워크스테이션에서 U-SQL 작업을 실행할 수 있습니다. 이들 두 가지 로컬 실행 기능으로 U-SQL 작업을 테스트하고 디버그하는 시간을 절약할 수 있습니다.

## <a name="understand-the-data-root-folder-and-the-file-path"></a>데이터 루트 폴더 및 파일 경로 이해

로컬 실행 및 U-SQL SDK에는 모두 데이터 루트 폴더가 필요합니다. 데이터 루트 폴더는 로컬 계산 계정의 "로컬 저장소"입니다. 이는 Data Lake Analytics 계정의 Azure Data Lake Store 계정과 동일합니다. 다른 데이터 루트 폴더로 전환하는 것은 다른 저장소 계정으로 전환하는 것과 같습니다. 다른 데이터 루트 폴더와 공유된 데이터에 액세스하려는 경우 스크립트에서 절대 경로를 사용해야 합니다. 또는 데이터 루트 폴더 아래에 파일 시스템 심볼 링크를 만들어 공유 데이터를 가리키도록 합니다(예, NTFS의 **mklink**).

데이터 루트 폴더는 다음 용도로 사용됩니다.

- 데이터베이스, 테이블, TVF(테이블 반환 함수), 어셈블리 등의 메타데이터를 저장합니다.
- U-SQL에서 상대 경로로 정의된 입력 및 출력 경로 조회 - 상대 경로를 사용하면 U-SQL 프로젝트를 Azure에 보다 쉽게 배포할 수 있습니다.

U-SQL 스크립트의 상대 경로 및 로컬 절대 경로를 사용할 수 있습니다. 상대 경로는 지정된 데이터-루트 폴더 경로 기준으로 하는 것입니다. 스크립트를 서버 쪽과 호환되게 하려면 경로 구분 기호로 "/"를 사용하는 것이 좋습니다. 다음은 상대 경로 및 이와 대등한 절대 경로의 몇 가지 예입니다. 이 예에서 C:\LocalRunDataRoot는 데이터 루트 폴더입니다.

|상대 경로|절대 경로|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

## <a name="use-local-run-from-visual-studio"></a>Visual Studio에서 로컬 실행 사용

Data Lake Tools for Visual Studio는 Visual Studio에서 U-SQL 로컬 실행 환경을 제공합니다. 이 기능을 사용하면 다음을 수행할 수 있습니다.

- C# 어셈블리와 함께 U-SQL 스크립트를 로컬로 실행합니다.
- C# 어셈블리를 로컬로 디버그합니다.
- [서버 탐색기]에서 U-SQL 카탈로그(로컬 데이터베이스, 어셈블리, 스키마 및 테이블)를 만들고, 보고, 삭제합니다. 또 서버 탐색기에서 로컬 카탈로그도 찾을 수 있습니다.

    ![Data Lake Tools for Visual Studio의 로컬 카탈로그 로컬 실행](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-local-catalog.png)

Data Lake Tools 설치 관리자는 기본 데이터 루트 폴더로 사용할 C:\LocalRunRoot 폴더를 만듭니다. 기본 병렬 처리 로컬 실행은 1입니다.

### <a name="to-configure-local-run-in-visual-studio"></a>Visual Studio에서 로컬 실행을 구성하려면

1. Visual Studio를 엽니다.
2. **서버 탐색기**를 엽니다.
3. **Azure** > **Data Lake Analytics**를 차례로 확장합니다.
4. **Data Lake** 메뉴, **옵션 및 설정**을 차례로 클릭합니다.
5. 왼쪽 트리에서 **Azure Data Lake**, **일반**을 차례로 확장합니다.

    ![Data Lake Tools for Visual Studio의 로컬 실행 구성 설정](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-configure.png)

로컬 실행을 수행하려면 Visual Studio U-SQL 프로젝트가 필요합니다. 이 점이 Azure에서 실행하는 U-SQL 스크립트와 다릅니다.

### <a name="to-run-a-u-sql-script-locally"></a>로컬로 U-SQL 스크립트를 실행하려면
1. Visual Studio에서 U-SQL 프로젝트를 엽니다.   
2. [솔루션 탐색기]에서 U-SQL 스크립트를 마우스 오른쪽 단추로 클릭한 다음 **스크립트 제출**을 클릭합니다.
3. 로컬로 스크립트를 실행하려면 분석 계정을 **(로컬)**로 선택합니다.
또는 스크립트 창 위쪽에서 **(로컬)** 계정을 클릭한 다음 **제출**을 클릭하거나 Ctrl+F5 바로 가기를 사용할 수도 있습니다.

    ![Data Lake Tools for Visual Studio의 로컬 실행 작업 제출](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-submit-job.png)

### <a name="debug-scripts-and-c-assemblies-locally"></a>로컬에서 스크립트 및 C# 어셈블리 디버그

Azure Data Lake Analytics 서비스에 C# 어셈블리를 제출하고 등록하지 않아도 C# 어셈블리를 디버그할 수 있습니다. 코드 숨김 파일 및 참조된 C# 프로젝트 양쪽 모두에 중단점을 설정할 수 있습니다.

#### <a name="to-debug-local-code-in-code-behind-file"></a>코드 숨김 파일의 로컬 코드를 디버그하려면

1. 코드 숨김 파일에 중단점을 설정합니다.
2. F5 키를 눌러서 스크립트를 로컬에서 디버그합니다.

> [!NOTE]
   > 다음 프로시저는 Visual Studio 2015에만 해당됩니다. 이전 버전의 Visual Studio에서는 pdb 파일을 수동으로 추가해야 합니다.  
   >
   >

#### <a name="to-debug-local-code-in-a-referenced-c-project"></a>참조된 C# 프로젝트의 로컬 코드를 디버그하려면

1. C# 어셈블리 프로젝트를 만들고 빌드하여 출력 dll을 생성합니다.
2. U-SQL 문을 사용하여 dll을 등록합니다.

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. C# 코드에 중단점을 설정합니다.
4. F5 키를 눌러서 C# dll을 로컬에서 참조하는 스크립트를 디버그합니다.

## <a name="use-local-run-from-the-data-lake-u-sql-sdk"></a>Data Lake U-SQL SDK에서 로컬 실행 사용

Visual Studio를 사용하여 U-SQL 스크립트를 로컬로 실행하는 것 외에도 Azure Data Lake U-SQL SDK를 사용하여 명령줄 및 프로그래밍 인터페이스로 U-SQL 스크립트를 로컬로 실행할 수 있습니다. 이를 통해 U-SQL 로컬 테스트를 확장할 수 있습니다.

[Azure Data Lake U-SQL SDK](data-lake-analytics-u-sql-sdk.md)에 대해 자세히 알아보세요.


## <a name="next-steps"></a>다음 단계

* 더 복잡한 쿼리를 보려면 [Azure Data Lake Analytics을 사용하여 웹 사이트 로그 분석](data-lake-analytics-analyze-weblogs.md)을 참조하세요.
* 작업 세부 정보를 보려면, [Azure Data lake Analytics 작업에 대한 작업 브라우저 및 작업 보기 사용하기](data-lake-analytics-data-lake-tools-view-jobs.md)를 참조하세요.
* 꼭짓점 실행 보기를 사용하려면 [Data Lake Tools for Visual Studio에서 Vertex Execution View 사용](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)을 참조하세요.
