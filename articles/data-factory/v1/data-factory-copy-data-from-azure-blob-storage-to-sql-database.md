---
title: "Blob Storage에서 SQL Database로 데이터 복사 - Azure | Microsoft Docs"
description: "이 자습서에서는 Azure Data Factory 파이프라인에서 복사 작업을 사용하여 Blob 저장소에서 SQL 데이터베이스로 데이터를 복사하는 방법을 보여 줍니다."
services: data-factory
documentationcenter: 
author: linda33wj
manager: 
editor: 
ms.assetid: e4035060-93bf-4e8d-bf35-35e2d15c51e0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 3135c8bf6316125a164c06630bc7607a92621b26
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2018
---
# <a name="tutorial-copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>자습서: 데이터 팩터리를 사용하여 Blob Storage에서 SQL Database로 데이터 복사
> [!div class="op_single_selector"]
> * [개요 및 필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [복사 마법사](data-factory-copy-data-wizard-tutorial.md)
> * [Azure 포털](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager 템플릿](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> 이 문서는 GA(일반 공급) 상태인 Data Factory 버전 1에 적용됩니다. 미리 보기에 있는 Data Factory 서비스 버전 2를 사용하는 경우 [버전 2 설명서의 복사 작업 자습서](../quickstart-create-data-factory-dot-net.md)를 참조하세요. 

이 자습서에서는 파이프라인을 포함한 데이터 팩터리를 만들어서 Blob 저장소에서 SQL 데이터베이스로 데이터를 복사합니다.

복사 작업은 Azure Data Factory에서 데이터 이동을 수행합니다. 다양한 데이터 저장소 간에 데이터를 안전하고 안정적이며 확장성 있는 방법으로 복사할 수 있는 전역적으로 사용 가능한 서비스를 통해 이루어집니다. 복사 작업에 대한 자세한 내용은 [데이터 이동 작업](data-factory-data-movement-activities.md) 문서를 참조하세요.  

> [!NOTE]
> 데이터 팩터리 서비스에 대한 자세한 개요는 [Azure Data Factory 소개](data-factory-introduction.md) 문서를 참조하세요.
>
>

## <a name="prerequisites-for-the-tutorial"></a>자습서의 필수 조건
이 자습서를 시작하기 전에 다음 필수 조건이 있어야 합니다.

* **Azure 구독**.  구독이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [무료 평가판](http://azure.microsoft.com/pricing/free-trial/) 문서를 참조하세요.
* **Azure Storage 계정**. 이 자습서에서는 Blob 저장소를 **원본** 데이터 저장소로 사용합니다. Azure Storage 계정이 없는 경우 새로 만드는 단계는 [저장소 계정 만들기](../../storage/common/storage-create-storage-account.md#create-a-storage-account) 문서를 참조하세요.
* **Azure SQL Database**. 이 자습서에서는 Azure SQL 데이터베이스를 **대상** 데이터 저장소로 사용합니다. 자습서에서 사용할 수 있는 Azure SQL Database가 없는 경우 [Azure SQL Database를 만들고 구성하는 방법](../../sql-database/sql-database-get-started.md) 을 참조하여 새로 만드세요.
* **SQL Server 2012/2014 또는 Visual Studio 2013**. SQL Server Management Studio 또는 Visual Studio를 사용하여 샘플 데이터베이스를 만들고 데이터베이스에서 결과 데이터를 확인합니다.  

## <a name="collect-blob-storage-account-name-and-key"></a>Blob 저장소 계정 이름 및 키 수집
이 자습서를 수행하려면 Azure Storage 계정의 계정 이름과 계정 키가 필요합니다. Azure Storage 계정의 **계정 이름**과 **계정 키**를 적어둡니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 왼쪽 메뉴의 **모든 서비스**를 클릭하고 **Storage 계정**을 선택합니다.

    ![찾아보기 - Storage 계정](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/browse-storage-accounts.png)
3. **Storage 계정** 블레이드에서, 이 자습서에서 사용하려는 **Azure Storage 계정**을 선택합니다.
4. **설정** 아래에 있는 **액세스 키** 링크를 선택합니다.
5. **Storage 계정 이름** 텍스트 상자 옆에 있는 **복사**(이미지) 단추를 클릭하고 텍스트 파일 등에 저장/붙여넣습니다.
6. 이전 단계를 반복하여 복사하거나 **key1**을 적어둡니다.

    ![저장소 액세스 키](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/storage-access-key.png)
7. **X**를 클릭하여 모든 블레이드를 닫습니다.

## <a name="collect-sql-server-database-user-names"></a>SQL server, 데이터베이스, 사용자 이름 수집
이 자습서를 수행하려면 Azure SQL Server, 데이터베이스 및 사용자의 이름이 필요합니다. Azure SQL 데이터베이스의 **서버**, **데이터베이스** 및 **사용자**의 이름을 적어둡니다.

1. **Azure 포털**에서 왼쪽의 **모든 서비스**를 클릭하고 **SQL 데이터베이스**를 선택합니다.
2. **SQL 데이터베이스 블레이드**에서, 이 자습서에서 사용하려는 **데이터베이스**를 선택합니다. **데이터베이스 이름**을 적어둡니다.  
3. **SQL 데이터베이스** 블레이드에서 **설정** 아래의 **속성**을 클릭합니다.
4. **서버 이름** 및 **서버 관리자 로그인**의 값을 적어둡니다.
5. **X**를 클릭하여 모든 블레이드를 닫습니다.

## <a name="allow-azure-services-to-access-sql-server"></a>Azure 서비스가 SQL server에 액세스하도록 허용
데이터 팩터리 서비스가 Azure SQL Server에 액세스할 수 있도록 Azure SQL Server에 대해 **Azure 서비스에 대한 액세스 허용** 설정이 **ON**인지 확인합니다. 이 설정을 확인하고 켜려면 다음 단계를 수행합니다.

1. 왼쪽의 **모든 서비스** 허브를 클릭하고 **SQL 서버**를 클릭합니다.
2. 서버를 선택하고 **설정** 아래의 **방화벽**을 클릭합니다.
3. **방화벽 설정** 블레이드에서 **Azure 서비스에 대한 액세스 허용**에 대해 **켜기**를 클릭합니다.
4. **X**를 클릭하여 모든 블레이드를 닫습니다.

## <a name="prepare-blob-storage-and-sql-database"></a>Blob Storage 및 SQL Database 준비
이제 다음 단계를 수행하여 자습서에서 사용할 Azure Blob 저장소 및 Azure SQL 데이터베이스를 준비합니다.  

1. 메모장을 시작합니다. 다음 텍스트를 복사하여 **emp.txt**로 하드 드라이브의 **C:\ADFGetStarted** 폴더에 저장합니다.

    ```
    John, Doe
    Jane, Doe
    ```
2. [Azure Storage Explorer](http://storageexplorer.com/)와 같은 도구를 사용하여 **adftutorial** 컨테이너를 만들고 **emp.txt** 파일을 이 컨테이너에 업로드합니다.

    ![Azure Storage 탐색기. Blob 저장소에서 SQL 데이터베이스로 데이터 복사](./media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/getstarted-storage-explorer.png)
3. 다음 SQL 스크립트를 사용하여 **emp** 테이블을 Azure SQL Database에 만듭니다.  

    ```SQL
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

    **컴퓨터에 SQL Server 2012/2014가 설치된 경우:** [SQL Server Management Studio를 사용하여 Azure SQL Database 관리](../../sql-database/sql-database-manage-azure-ssms.md)의 지침에 따라 Azure SQL 서버에 연결하고 SQL 스크립트를 실행합니다. 

    클라이언트가 Azure SQL Server에 액세스할 수 없는 경우 컴퓨터(IP 주소)의 액세스를 허용하도록 Azure SQL Server의 방화벽을 구성해야 합니다. Azure SQL Server의 방화벽을 구성하는 단계는 [이 문서](../../sql-database/sql-database-configure-firewall-settings.md)를 참조하세요.

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.
필수 조건을 완료했습니다. 다음 방법 중 하나를 사용하여 데이터 팩터리를 만들 수 있습니다. 위쪽의 드롭다운 목록에 있는 옵션 또는 다음 링크 중 하나를 클릭하여 자습서를 수행합니다.     

* [복사 마법사](data-factory-copy-data-wizard-tutorial.md)
* [Azure 포털](data-factory-copy-activity-tutorial-using-azure-portal.md)
* [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
* [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
* [Azure Resource Manager 템플릿](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
* [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> 이 자습서에서 데이터 파이프라인은 원본 데이터 저장소의 데이터를 대상 데이터 저장소로 복사합니다. 출력 데이터를 생성하기 위해 입력 데이터를 변환하지 않습니다. Azure Data Factory를 사용하여 데이터를 변환하는 방법에 대한 자습서는 [자습서: Hadoop 클러스터를 사용하여 데이터를 변환하도록 첫 번째 파이프라인 빌드](data-factory-build-your-first-pipeline.md)를 참조하세요.
> 
> 한 활동의 출력 데이터 집합을 다른 활동의 입력 데이터 집합으로 설정하여 두 활동을 연결하면 해당 활동을 차례로 실행할 수 있습니다. 자세한 정보는 [데이터 팩터리의 예약 및 실행](data-factory-scheduling-and-execution.md)을 참조하세요. 
