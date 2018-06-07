---
title: PowerShell을 사용하여 탄력적 작업 만들기 및 관리 | Microsoft Docs
description: Azure SQL Database 풀을 관리하는데 사용되는 PowerShell
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 425213f66a4b9960cf68a40c935fedff12f9f75d
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="create-and-manage-sql-database-elastic-jobs-using-powershell-preview"></a>PowerShell을 사용하여 SQL Database 탄력적 작업 만들기 및 관리(미리 보기)

**Elastic Database 작업** (미리 보기)에 PowerShell API를 사용하면 스크립트를 실행할 데이터베이스 그룹을 정의할 수 있습니다. 이 문서는 PowerShell cmdlet을 사용하여 **Elastic Database 작업** 을 만들고 관리하는 방법을 보여줍니다. [탄력적 작업 개요](sql-database-elastic-jobs-overview.md)를 참조하세요. 

## <a name="prerequisites"></a>필수 조건
* Azure 구독. 무료 평가판에 대한 내용은 [무료 1개월 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* Elastic Database 도구로 만든 데이터 집합. [Elastic Database 도구 시작하기](sql-database-elastic-scale-get-started.md)를 참조하세요.
* Azure PowerShell. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](https://docs.microsoft.com/powershell/azure/overview)을 참조하세요.
* **Elastic Database 작업** PowerShell 패키지: [Installing Elastic Database 작업](sql-database-elastic-jobs-service-installation.md)

### <a name="select-your-azure-subscription"></a>Azure 구독 선택
구독을 선택하려면 구독 ID**-SubscriptionId** 또는 구독 이름(**-SubscriptionName**이 필요합니다. 구독이 여러 개일 경우 **Get-AzureRmSubscription** cmdlet을 실행하고 결과 집합에서 원하는 구독 정보를 복사할 수 있습니다. 구독 정보가 준비되면 다음 commandlet을 실행하여 이 구독을 기본값, 즉 작업을 만들고 관리하기 위한 대상으로 설정합니다.

    Select-AzureRmSubscription -SubscriptionId {SubscriptionID}

[PowerShell ISE](https://technet.microsoft.com/library/dd315244.aspx) 를 사용하여 Elastic Database 작업에 대한 PowerShell 스크립트를 개발 및 실행하는 것이 좋습니다.

## <a name="elastic-database-jobs-objects"></a>Elastic Database 작업 개체
다음 표에서는 **Elastic Database 작업** 의 모든 개체 유형과 해당 설명 및 관련된 PowerShell API를 보여줍니다.

<table style="width:100%">
  <tr>
    <th>개체 유형</th>
    <th>설명</th>
    <th>관련된 PowerShell API</th>
  </tr>
  <tr>
    <td>자격 증명</td>
    <td>PACPAC 응용 프로그램 또는 스크립트 실행을 위해 데이터베이스에 연결할 때 사용할 사용자 이름 및 암호입니다. <p>Elastic Database 작업 데이터베이스로 보내고 저장하기 전에 암호가 암호화됩니다.  설치 스크립트에서 생성 및 업로드된 자격 증명을 통해 Elastic Database 작업 서비스에서 암호를 해독합니다.</td>
    <td><p>Get-AzureSqlJobCredential</p>
    <p>New-AzureSqlJobCredential</p><p>Set-AzureSqlJobCredential</p></td></td>
  </tr>

  <tr>
    <td>스크립트</td>
    <td>데이터베이스에서 실행하는 데 사용할 TRANSACT-SQL 스크립트입니다.  실패 시 서비스에서 스크립트 실행을 다시 시도하므로 스크립트를 idempotent로 작성해야 합니다.
    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>Get-AzureSqlJobContentDefinition</p>
    <p>New-AzureSqlJobContent</p>
    <p>Set-AzureSqlJobContentDefinition</p>
    </td>
  </tr>

  <tr>
    <td>DACPAC</td>
    <td>데이터베이스에서 적용할 <a href="https://msdn.microsoft.com/library/ee210546.aspx">데이터 계층 응용 프로그램</a> 패키지입니다.
    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>New-AzureSqlJobContent</p>
    <p>Set-AzureSqlJobContentDefinition</p>
    </td>
  </tr>
  <tr>
    <td>데이터베이스 대상</td>
    <td>Azure SQL Database를 가리키는 데이터베이스 및 서버 이름입니다.
    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    </td>
  </tr>
  <tr>
    <td>분할된 데이터베이스 맵 대상</td>
    <td>Elastic Database 분할된 데이터베이스 맵 내에 저장된 정보를 확인하는 데 사용할 자격 증명 및 데이터베이스 대상의 조합입니다.
    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    <p>Set-AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>사용자 지정 컬렉션 대상</td>
    <td>전체적으로 실행하는 데 사용할 정의된 데이터베이스 그룹입니다.</td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>사용자 지정 컬렉션 자식 대상</td>
    <td>사용자 지정 컬렉션에서 참조되는 데이터베이스 대상입니다.</td>
    <td>
    <p>Add-AzureSqlJobChildTarget</p>
    <p>Remove-AzureSqlJobChildTarget</p>
    </td>
  </tr>

<tr>
    <td>작업</td>
    <td>
    <p>실행을 트리거하거나 일정을 이행하는 데 사용할 수 있는 작업에 대한 매개 변수 정의입니다.</p>
    </td>
    <td>
    <p>Get-AzureSqlJob</p>
    <p>New-AzureSqlJob</p>
    <p>Set-AzureSqlJob</p>
    </td>
  </tr>

<tr>
    <td>작업 실행</td>
    <td>
    <p>실행 정책에 따라 오류를 처리하고 데이터베이스 연결에 자격 증명을 사용하여 스크립트 실행 또는 대상에 DACPAC 적용을 이행하는 데 필요한 작업의 컨테이너입니다.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Start-AzureSqlJobExecution</p>
    <p>Stop-AzureSqlJobExecution</p>
    <p>Wait-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>작업 태스크 실행</td>
    <td>
    <p>작업을 이행하는 단일 작업 단위입니다.</p>
    <p>작업 태스크를 성공적으로 실행할 수 없는 경우 결과 예외 메시지가 기록되고 지정된 실행 정책에 따라 일치하는 새 작업 태스크가 생성 및 실행됩니다.</p></p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Start-AzureSqlJobExecution</p>
    <p>Stop-AzureSqlJobExecution</p>
    <p>Wait-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>작업 실행 정책</td>
    <td>
    <p>작업 실행 시간 제한, 재시도 제한 및 재시도 간격을 제어합니다.</p>
    <p>Elastic Database 작업에는 각 재시도 간격의 지수 백오프를 통해 작업 태스크 실패의 무기한 재시도를 발생시키는 기본 작업 실행 정책이 포함되어 있습니다.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecutionPolicy</p>
    <p>New-AzureSqlJobExecutionPolicy</p>
    <p>Set-AzureSqlJobExecutionPolicy</p>
    </td>
  </tr>

<tr>
    <td>일정</td>
    <td>
    <p>되풀이 간격 또는 한 번 수행할 실행에 대한 시간 기반 지정입니다.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobSchedule</p>
    <p>New-AzureSqlJobSchedule</p>
    <p>Set-AzureSqlJobSchedule</p>
    </td>
  </tr>

<tr>
    <td>작업 트리거</td>
    <td>
    <p>일정에 따라 작업 실행을 트리거할 작업과 일정 간의 매핑입니다.</p>
    </td>
    <td>
    <p>New-AzureSqlJobTrigger</p>
    <p>Remove-AzureSqlJobTrigger</p>
    </td>
  </tr>
</table>

## <a name="supported-elastic-database-jobs-group-types"></a>지원되는 Elastic Database 작업 그룹 유형
이 작업은 데이터베이스 그룹에 대해 DACPAC 응용 프로그램 또는 Transact-SQL(T-SQL) 스크립트를 실행합니다. 데이터베이스 그룹에 대해 실행할 작업이 제출되면 작업은 자식 작업으로 "확장"되며 여기에서 각 자식 작업은 그룹의 단일 데이터베이스에 대해 요청된 실행을 수행합니다. 

두 가지 형식의 그룹을 만들 수 있습니다. 

* [분할된 데이터베이스 맵](sql-database-elastic-scale-shard-map-management.md) 그룹: 분할된 데이터베이스 맵을 대상으로 하는 작업이 제출되면 해당 작업은 분할된 데이터베이스 맵을 쿼리하여 분할된 데이터베이스의 현재 집합을 확인한 다음 분할된 데이터베이스 맵의 각 분할된 데이터베이스에 대한 자식 작업을 만듭니다.
* 사용자 지정 컬렉션 그룹: 사용자 지정 데이터베이스 집합입니다. 작업이 사용자 지정 컬렉션을 대상으로 하는 경우 사용자 지정 컬렉션에서 현재 각 데이터베이스에 대한 자식 작업을 만듭니다.

## <a name="to-set-the-elastic-database-jobs-connection"></a>Elastic Database 작업 연결을 설정하려면
작업 API를 사용하기 전에 작업 *제어 데이터베이스* 에 대한 연결을 설정해야 합니다. 이 cmdlet을 실행하면 자격 증명 창이 트리거되어 Elastic Database 작업을 설치할 때 만든 사용자 이름 및 암호를 요청하는 팝업이 표시됩니다. 이 항목에 제공된 모든 예제에서는 이 첫 번째 단계가 이미 수행되었다고 가정합니다.

Elastic Database 작업에 대한 연결을 엽니다.

    Use-AzureSqlJobConnection -CurrentAzureSubscription 

## <a name="encrypted-credentials-within-the-elastic-database-jobs"></a>Elastic Database 작업 내의 암호화된 자격 증명
암호를 암호화하여 작업 *제어 데이터베이스*에 데이터베이스 자격 증명을 삽입할 수 있습니다. 나중에 작업을 실행할 수 있도록 자격 증명을 저장해야 합니다(작업 일정 사용).

암호화는 설치 스크립트의 일부로 생성된 인증서를 통해 작동합니다. 설치 스크립트는 저장된 암호화된 암호 해독을 위해 인증서를 만들고 Azure 클라우드 서비스에 업로드합니다. Azure 클라우드 서비스는 나중에 작업 *제어 데이터베이스* 내에 공개 키를 저장합니다. 그러면 PowerShell API 또는 Azure Portal 인터페이스에서 인증서를 로컬에 설치하도록 요구하지 않고 제공된 암호를 암호화할 수 있습니다.

자격 증명 암호가 암호화되고 Elastic Database 작업 개체에 대한 읽기 전용 액세스 권한이 있는 사용자로부터 보호됩니다. 하지만 Elastic Database 작업 개체에 대한 읽기-쓰기 액세스 권한이 있는 악의적인 사용자가 암호를 추출할 수 있습니다. 자격 증명은 작업 실행 간에 다시 사용되도록 설계되었습니다. 연결을 설정할 때 자격 증명이 대상 데이터베이스에 전달됩니다. 현재 각 자격 증명에 사용되는 대상 데이터베이스에 제한이 없으며 악의적인 사용자는 자신이 제어할 수 있는 데이터베이스에 대한 데이터베이스 대상을 추가할 수 있습니다. 사용자는 이후 이 데이터베이스를 대상으로 작업을 시작하여 자격 증명의 암호를 얻습니다.

Elastic Database 작업에 대한 보안 모범 사례는 다음과 같습니다.

* API 사용을 신뢰할 수 있는 개인으로 제한합니다.
* 자격 증명에 작업 태스크를 수행하는 데 필요한 최소한의 권한만 있어야 합니다.  자세한 내용은 이 [권한 부여 및 사용 권한](https://msdn.microsoft.com/library/bb669084.aspx) SQL Server MSDN 문서에서 확인할 수 있습니다.

### <a name="to-create-an-encrypted-credential-for-job-execution-across-databases"></a>데이터베이스에 대한 작업 실행을 위한 암호화된 자격 증명을 만들려면
새 암호화된 자격 증명을 만들기 위해 [**Get-Credential cmdlet**](https://technet.microsoft.com/library/hh849815.aspx)에서 [**New-AzureSqlJobCredential cmdlet**](/powershell/module/elasticdatabasejobs/new-azuresqljobcredential)에 전달할 수 있는 사용자 이름 및 암호를 묻는 메시지를 표시합니다.

    $credentialName = "{Credential Name}"
    $databaseCredential = Get-Credential
    $credential = New-AzureSqlJobCredential -Credential $databaseCredential -CredentialName $credentialName
    Write-Output $credential

### <a name="to-update-credentials"></a>자격 증명을 업데이트하려면
암호가 변경될 때 [**Set-AzureSqlJobCredential cmdlet**](/powershell/module/elasticdatabasejobs/set-azuresqljobcredential)을 사용하고 **CredentialName** 매개 변수를 설정합니다.

    $credentialName = "{Credential Name}"
    Set-AzureSqlJobCredential -CredentialName $credentialName -Credential $credential 

## <a name="to-define-an-elastic-database-shard-map-target"></a>Elastic Database 분할된 데이터베이스 맵 대상을 정의하려면
[Elastic Database 클라이언트 라이브러리](sql-database-elastic-database-client-library.md)를 통해 만든 분할된 데이터베이스 집합의 모든 데이터베이스에 대해 작업을 실행하려면 분할된 데이터베이스 맵을 데이터베이스 대상으로 사용합니다. 이 예제에서는 Elastic Database 클라이언트 라이브러리를 사용하여 만든 분할된 데이터베이스 응용 프로그램이 필요합니다. [Elastic Database 도구 샘플 시작](sql-database-elastic-scale-get-started.md)을 참조하세요.

분할된 데이터베이스 맵 관리자 데이터베이스를 데이터베이스 대상으로 설정한 후 분할된 특정 데이터베이스 맵을 대상으로 지정해야 합니다.

    $shardMapCredentialName = "{Credential Name}"
    $shardMapDatabaseName = "{ShardMapDatabaseName}" #example: ElasticScaleStarterKit_ShardMapManagerDb
    $shardMapDatabaseServerName = "{ShardMapServerName}"
    $shardMapName = "{MyShardMap}" #example: CustomerIDShardMap
    $shardMapDatabaseTarget = New-AzureSqlJobTarget -DatabaseName $shardMapDatabaseName -ServerName $shardMapDatabaseServerName
    $shardMapTarget = New-AzureSqlJobTarget -ShardMapManagerCredentialName $shardMapCredentialName -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapDatabaseServerName -ShardMapName $shardMapName
    Write-Output $shardMapTarget

## <a name="create-a-t-sql-script-for-execution-across-databases"></a>데이터베이스에서 실행할 T-SQL 스크립트 만들기
실행할 T-SQL 스크립트를 만드는 경우 [idempotent](https://en.wikipedia.org/wiki/Idempotence) 이고 오류로부터 복구되도록 작성하는 것이 좋습니다. Elastic Database 작업은 실행이 실패할 때마다 오류 분류에 관계없이 스크립트 실행을 다시 시도합니다.

[**New-AzureSqlJobContent cmdlet**](/powershell/module/elasticdatabasejobs/new-azuresqljobcontent)을 사용하여 실행할 스크립트를 만들고 저장한 다음 **-ContentName** 및 **-CommandText** 매개 변수를 설정합니다.

    $scriptName = "Create a TestTable"

    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
        CREATE TABLE TestTable(
            TestTableId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="create-a-new-script-from-a-file"></a>파일에서 새 스크립트 만들기
T-SQL 스크립트가 파일 내에서 정의된 경우 다음을 사용하여 스크립트를 가져옵니다.

    $scriptName = "My Script Imported from a File"
    $scriptPath = "{Path to SQL File}"
    $scriptCommandText = Get-Content -Path $scriptPath
    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="to-update-a-t-sql-script-for-execution-across-databases"></a>데이터베이스에서 실행되도록 T-SQL 스크립트를 업데이트하려면
이 PowerShell 스크립트는 기존 스크립트에 대한 T-SQL 명령 텍스트를 업데이트합니다.

설정하려는 스크립트 정의가 반영되도록 다음 변수를 설정합니다.

    $scriptName = "Create a TestTable"
    $scriptUpdateComment = "Adding AdditionalInformation column to TestTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
    CREATE TABLE TestTable(
        TestTableId INT PRIMARY KEY IDENTITY,
        InsertionTime DATETIME2
    );
    END
    GO

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN
    ALTER TABLE TestTable
    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO"

### <a name="to-update-the-definition-to-an-existing-script"></a>기존 스크립트에 대한 정의를 업데이트하려면
    Set-AzureSqlJobContentDefinition -ContentName $scriptName -CommandText $scriptCommandText -Comment $scriptUpdateComment 

## <a name="to-create-a-job-to-execute-a-script-across-a-shard-map"></a>분할된 데이터베이스 맵에서 스크립트를 실행하는 작업을 만들려면
이 PowerShell 스크립트는 탄력적 확장 분할된 데이터베이스 맵의 각 분할된 데이터베이스에서 스크립트를 실행하는 작업을 시작합니다.

원하는 스크립트 및 대상이 반영되도록 다음 변수를 설정합니다.

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $credentialName = "{Credential Name}"
    $shardMapTarget = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName 
    $job = New-AzureSqlJob -ContentName $scriptName -CredentialName $credentialName -JobName $jobName -TargetId $shardMapTarget.TargetId
    Write-Output $job

## <a name="to-execute-a-job"></a>작업을 실행하려면
이 PowerShell 스크립트는 기존 작업을 실행합니다.

실행하려는 작업 이름이 반영되도록 다음 변수를 업데이트합니다.

    $jobName = "{Job Name}"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
    Write-Output $jobExecution

## <a name="to-retrieve-the-state-of-a-single-job-execution"></a>단일 작업 실행의 상태를 검색하려면
[**Get-AzureSqlJobExecution cmdlet**](/powershell/module/elasticdatabasejobs/get-azuresqljobexecution)을 사용하고 **JobExecutionId** 매개 변수를 설정하여 작업 실행의 상태를 표시합니다.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecution = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId
    Write-Output $jobExecution

**IncludeChildren** 매개 변수와 함께 동일한 **Get-AzureSqlJobExecution cmdlet**을 사용하여 자식 작업 실행 상태, 즉 작업 대상인 각 데이터베이스에 대한 각 작업 실행의 특정 상태를 표시할 수 있습니다.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions 

## <a name="to-view-the-state-across-multiple-job-executions"></a>여러 작업 실행 간에 상태를 보려면
[**Get-AzureSqlJobExecution cmdlet**](/powershell/module/elasticdatabasejobs/new-azuresqljob)에는 제공된 매개 변수를 통해 필터링되는 여러 작업 실행을 표시하는 데 사용할 수 있는 여러 선택적 매개 변수가 있습니다. 아래에서는 Get-AzureSqlJobExecution을 사용할 수 있는 여러 방법을 보여 줍니다.

모든 활성 최상위 작업 실행을 검색합니다.

    Get-AzureSqlJobExecution

비활성 작업 실행을 포함하여 모든 최상위 작업 실행을 검색합니다.

    Get-AzureSqlJobExecution -IncludeInactive

비활성 작업 실행을 포함하여 제공된 작업 실행 ID의 모든 자식 작업 실행을 검색합니다.

    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId -IncludeInactive -IncludeChildren

비활성 작업을 포함하여 일정/작업 조합으로 만든 모든 작업 실행을 검색합니다.

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive

비활성 작업을 포함하여 지정된 분할된 데이터베이스 맵을 대상으로 하는 모든 작업을 검색합니다.

    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive

비활성 작업을 포함하여 지정된 사용자 지정 컬렉션을 대상으로 하는 모든 작업을 검색합니다.

    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive

특정 작업 실행 내의 작업 태스크 실행 목록을 검색합니다.

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions 

작업 태스크 실행 세부 정보를 검색합니다.

다음 PowerShell 스크립트를 사용하여 실행 실패를 디버그할 때 특히 유용한 작업 태스크 실행 세부 정보를 볼 수 있습니다.

    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution

## <a name="to-retrieve-failures-within-job-task-executions"></a>작업 태스크 실행 내의 오류를 검색하려면
**JobTaskExecution 개체** 에는 메시지 속성과 함께 작업의 수명 주기에 대한 속성이 포함되어 있습니다. 작업 태스크 실행에 실패한 경우 수명 주기 속성이 *Failed* 로 설정되고 메시지 속성은 결과 예외 메시지 및 해당 스택으로 설정됩니다. 작업이 수행되지 않은 경우 지정된 작업에 대해 실패한 작업 태스크 세부 정보를 보는 것이 중요합니다.

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions) 
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }

## <a name="to-wait-for-a-job-execution-to-complete"></a>작업 실행이 완료될 때까지 대기하려면
다음 PowerShell 스크립트를 사용하여 작업 태스크가 완료될 때까지 기다릴 수 있습니다.

    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## <a name="create-a-custom-execution-policy"></a>사용자 지정 실행 정책 만들기
Elastic Database 작업은 작업을 시작할 때 적용할 수 있는 사용자 지정 실행 정책 만들기를 지원합니다.

실행 정책은 현재 다음과 같은 정의를 허용합니다.

* 이름: 실행 정책의 식별자입니다.
* 작업 시간 제한: Elastic Database 작업에 의해 작업이 취소되기 전의 총 시간입니다.
* 초기 재시도 간격: 첫 번째 재시도 전에 대기할 간격입니다.
* 최대 재시도 간격: 사용할 재시도 간격의 최대값입니다.
* 재시도 간격 백오프 계수: 재시도 사이의 다음 간격을 계산하는 데 사용되는 계수입니다.  (초기 재시도 간격) * Math.pow((계수 백오프 간격), (재시도 횟수) - 2) 수식이 사용됩니다. 
* 최대 시도 횟수: 작업 내에서 수행할 최대 재시도 횟수입니다.

기본 실행 정책은 다음 값을 사용합니다.

* 이름: 기본 실행 정책
* 작업 시간 제한: 1주
* 초기 재시도 간격: 100밀리초
* 최대 재시도 간격: 30분
* 재시도 간격 계수: 2
* 최대 시도 횟수: 2,147,483,647

원하는 실행 정책을 만듭니다.

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 10
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval 
    -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $executionPolicy

### <a name="update-a-custom-execution-policy"></a>사용자 지정 실행 정책 업데이트
업데이트하려는 실행 정책을 업데이트합니다.

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 15
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $updatedExecutionPolicy

## <a name="cancel-a-job"></a>작업 취소
Elastic Database 작업은 작업 취소 요청을 지원합니다.  Elastic Database 작업이 현재 실행 중인 작업에 대한 취소 요청을 감지하는 경우 작업을 중지하려고 합니다.

Elastic Database 작업이 취소를 수행할 수 있는 방법에는 다음 두 가지가 있습니다.

1. 현재 실행 중인 태스크 취소: 태스크가 현재 실행되는 동안 취소가 감지되면 현재 실행 중인 태스크 측면 내에서 취소가 시도됩니다.  예를 들어 현재 장기 실행 쿼리를 수행하는 동안 취소가 시도되면 쿼리를 취소하려고 합니다.
2. 태스크 재시도 취소: 태스크 실행이 시작되기 전에 제어 스레드에서 취소가 감지되면 제어 스레드는 태스크를 시작하지 않고 요청을 취소된 것으로 선언합니다.

부모 작업에 대해 작업 취소가 요청된 경우 부모 작업 및 모든 자식 작업에 대해 취소 요청이 적용됩니다.

취소 요청을 제출하려면 [**Stop-AzureSqlJobExecution cmdlet**](/powershell/module/elasticdatabasejobs/stop-azuresqljobexecution)을 사용하고 **JobExecutionId** 매개 변수를 설정합니다.

    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## <a name="to-delete-a-job-and-job-history-asynchronously"></a>비동기적으로 작업 기록 및 작업을 삭제하려면
Elastic Database 작업은 비동기 작업 삭제를 지원합니다. 작업을 삭제되도록 표시할 수 있으며, 작업에 대한 모든 작업 실행이 완료된 후 작업 및 모든 작업 기록이 삭제됩니다. 활성 작업 실행은 자동으로 취소되지 않습니다.  

활성 작업 실행을 취소하려면 [**Stop-AzureSqlJobExecution**](/powershell/module/elasticdatabasejobs/stop-azuresqljobexecution)을 호출합니다.

작업 삭제를 트리거하려면 [**Remove-AzureSqlJob cmdlet**](/powershell/module/elasticdatabasejobs/remove-azuresqljob)을 사용하고 **JobName** 매개 변수를 설정합니다.

    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName

## <a name="to-create-a-custom-database-target"></a>사용자 지정 데이터베이스 대상을 만들려면
직접 실행하거나 사용자 지정 데이터베이스 그룹 내에 포함할 사용자 지정 데이터베이스 대상을 정의할 수 있습니다. 예를 들어, **탄력적 풀**은 PowerShell API를 사용하여 직접 지원되지 않으므로 풀에 있는 모든 데이터베이스를 포함하는 사용자 지정 데이터베이스 컬렉션 대상 및 사용자 지정 데이터베이스 대상을 만들 수 있습니다.

원하는 데이터베이스 정보가 반영되도록 다음 변수를 설정합니다.

    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## <a name="to-create-a-custom-database-collection-target"></a>사용자 지정 데이터베이스 컬렉션 대상을 만들려면
[**New-AzureSqlJobTarget cmdlet**](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget)을 사용하여 정의된 여러 데이터베이스 대상에서 실행할 수 있도록 사용자 지정 데이터베이스 컬렉션 대상을 정의할 수 있습니다. 데이터베이스 그룹을 만든 후 사용자 지정 컬렉션 대상에 데이터베이스를 연결할 수 있습니다.

원하는 사용자 지정 컬렉션 대상 구성이 반영되도록 다음 변수를 설정합니다.

    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### <a name="to-add-databases-to-a-custom-database-collection-target"></a>사용자 지정 데이터베이스 컬렉션 대상에 데이터베이스를 추가하려면
특정 사용자 지정 컬렉션에 데이터베이스를 추가하려면 [**Add-AzureSqlJobChildTarget**](/powershell/module/elasticdatabasejobs/add-azuresqljobchildtarget) cmdlet을 사용합니다.

    $databaseServerName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>사용자 지정 데이터베이스 컬렉션 대상 내의 데이터베이스 검토
[**Get-AzureSqlJobTarget**](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) cmdlet을 사용하여 사용자 지정 데이터베이스 컬렉션 대상 내의 자식 데이터베이스를 검색할 수 있습니다. 

    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>사용자 지정 데이터베이스 컬렉션 대상에서 스크립트를 실행하는 작업 만들기
[**New-AzureSqlJob**](/powershell/module/elasticdatabasejobs/new-azuresqljob) cmdlet을 사용하여 사용자 지정 데이터베이스 컬렉션 대상에서 정의된 데이터베이스 그룹에 대한 작업을 만들 수 있습니다. Elastic Database 작업은 각각 사용자 지정 데이터베이스 컬렉션 대상과 연결된 데이터베이스에 해당하는 여러 자식 작업으로 작업을 확장하고 각 데이터베이스에 대해 스크립트가 실행되도록 합니다. 스크립트는 재시도 복구에 대해 idempotent여야 합니다.

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job

## <a name="data-collection-across-databases"></a>데이터베이스에서 데이터 수집
작업을 사용하여 데이터베이스 그룹에서 쿼리를 실행하고 결과를 특정 테이블에 전송할 수 있습니다. 각 데이터베이스의 쿼리 결과를 볼 수 있으면 테이블을 쿼리할 수 있습니다. 이는 많은 데이터베이스에서 쿼리를 실행하는 비동기 메서드를 제공합니다. 실패한 시도는 재시도를 통해 자동으로 처리됩니다.

지정된 대상 테이블이 아직 없는 경우 자동으로 만들어집니다. 새 테이블은 반환된 결과 집합의 스키마와 일치합니다. 스크립트에서 여러 결과 집합이 반환되는 경우 Elastic Database 작업은 대상 테이블에 첫 번째 결과 집합만 보냅니다.

다음 PowerShell 스크립트는 스크립트를 실행하고 지정된 테이블에 결과를 수집합니다. 이 스크립트는 단일 결과 집합을 출력하는 T-SQL 스크립트가 생성되었으며 사용자 지정 데이터베이스 컬렉션 대상이 생성되었다고 가정합니다.

이 스크립트는 [**Get-AzureSqlJobTarget**](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) cmdlet을 사용합니다. 스크립트, 자격 증명 및 실행 대상에 대한 매개 변수를 설정합니다.

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $executionCredentialName = "{Execution Credential Name}"
    $customCollectionName = "{Custom Collection Name}"
    $destinationCredentialName = "{Destination Credential Name}"
    $destinationServerName = "{Destination Server Name}"
    $destinationDatabaseName = "{Destination Database Name}"
    $destinationSchemaName = "{Destination Schema Name}"
    $destinationTableName = "{Destination Table Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName

### <a name="to-create-and-start-a-job-for-data-collection-scenarios"></a>데이터 수집 시나리오에 대한 작업을 만들고 시작하려면
이 스크립트는 [**Start-AzureSqlJobExecution**](/powershell/module/elasticdatabasejobs/start-azuresqljobexecution) cmdlet을 사용합니다.

    $job = New-AzureSqlJob -JobName $jobName 
    -CredentialName $executionCredentialName 
    -ContentName $scriptName 
    -ResultSetDestinationServerName $destinationServerName 
    -ResultSetDestinationDatabaseName $destinationDatabaseName 
    -ResultSetDestinationSchemaName $destinationSchemaName 
    -ResultSetDestinationTableName $destinationTableName 
    -ResultSetDestinationCredentialName $destinationCredentialName 
    -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution

## <a name="to-schedule-a-job-execution-trigger"></a>작업 실행 트리거를 예약하려면
다음 PowerShell 스크립트를 사용하여 되풀이 일정을 만들 수 있습니다. 이 스크립트는 분 간격을 사용하지만 [**New-AzureSqlJobSchedule**](/powershell/module/elasticdatabasejobs/new-azuresqljobschedule)은 -DayInterval, -HourInterval, -MonthInterval 및 -WeekInterval 매개 변수도 지원합니다. -OneTime을 전달하여 한 번만 실행되는 일정을 만들 수 있습니다.

새 일정을 만듭니다.

    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule 
    -MinuteInterval $minuteInterval 
    -ScheduleName $scheduleName 
    -StartTime $startTime 
    Write-Output $schedule

### <a name="to-trigger-a-job-executed-on-a-time-schedule"></a>시간 일정에 따라 실행되는 작업을 트리거하려면
시간 일정에 따라 작업을 실행하는 작업 트리거를 정의할 수 있습니다. 다음 PowerShell 스크립트를 사용하여 작업 트리거를 만들 수 있습니다.

[New-AzureSqlJobTrigger](/powershell/module/elasticdatabasejobs/new-azuresqljobtrigger) 를 사용하고 원하는 작업 및 일정에 맞게 다음 변수를 설정합니다.

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger
    -ScheduleName $scheduleName
    -JobName $jobName
    Write-Output $jobTrigger

### <a name="to-remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>일정에 따라 작업이 실행되지 않도록 예약된 연결을 제거하려면
작업 트리거를 통한 되풀이 작업 실행을 중단하려면 작업 트리거를 제거할 수 있습니다. [**Remove-AzureSqlJobTrigger cmdlet**](/powershell/module/elasticdatabasejobs/remove-azuresqljobtrigger)을 사용하여 일정에 따라 작업이 실행되지 않도록 작업 트리거를 제거합니다.

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger 
    -ScheduleName $scheduleName 
    -JobName $jobName

### <a name="retrieve-job-triggers-bound-to-a-time-schedule"></a>시간 일정에 바인딩된 작업 트리거 검색
다음 PowerShell 스크립트를 사용하여 특정 시간 일정에 등록된 작업 트리거를 가져오고 표시할 수 있습니다.

    $scheduleName = "{Schedule Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -ScheduleName $scheduleName
    Write-Output $jobTriggers

### <a name="to-retrieve-job-triggers-bound-to-a-job"></a>작업에 바인딩된 작업 트리거를 검색하려면
[Get-AzureSqlJobTrigger](/powershell/module/elasticdatabasejobs/get-azuresqljobtrigger) 를 사용하여 등록된 작업을 포함하는 일정을 가져오고 표시합니다.

    $jobName = "{Job Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -JobName $jobName
    Write-Output $jobTriggers

## <a name="to-create-a-data-tier-application-dacpac-for-execution-across-databases"></a>데이터베이스에서 실행할 DACPAC(데이터 계층 응용 프로그램)를 만들려면
DACPAC를 만들려면 [데이터 계층 응용 프로그램](https://msdn.microsoft.com/library/ee210546.aspx)을 참조하세요. DACPAC를 배포하려면 [New-AzureSqlJobContent cmdlet](/powershell/module/elasticdatabasejobs/new-azuresqljobcontent)을 사용합니다. DACPAC는 서비스에 액세스할 수 있어야 합니다. 생성된 DACPAC를 Azure Storage에 업로드하고 DACPAC에 대한 서 [공유 액세스 서명](../storage/common/storage-dotnet-shared-access-signature-part-1.md) 을 만드는 것이 좋습니다.

    $dacpacUri = "{Uri}"
    $dacpacName = "{Dacpac Name}"
    $dacpac = New-AzureSqlJobContent -DacpacUri $dacpacUri -ContentName $dacpacName 
    Write-Output $dacpac

### <a name="to-update-a-data-tier-application-dacpac-for-execution-across-databases"></a>데이터베이스에서 실행할 DACPAC(데이터 계층 응용 프로그램)를 업데이트하려면
Elastic Database 작업 내에 등록된 기존 DACPAC를 새 URI를 가리키도록 업데이트할 수 있습니다. [**Set-AzureSqlJobContentDefinition cmdlet**](/powershell/module/elasticdatabasejobs/set-azuresqljobcontentdefinition)을 사용하여 기존에 등록된 DACPAC에서 DACPAC URI를 업데이트합니다.

    $dacpacName = "{Dacpac Name}"
    $newDacpacUri = "{Uri}"
    $updatedDacpac = Set-AzureSqlJobDacpacDefinition -ContentName $dacpacName -DacpacUri $newDacpacUri
    Write-Output $updatedDacpac

## <a name="to-create-a-job-to-apply-a-data-tier-application-dacpac-across-databases"></a>데이터베이스에 DACPAC(데이터 계층 응용 프로그램)를 적용하는 작업을 만들려면
Elastic Database 작업 내에서 DACPAC를 만든 후 데이터베이스 그룹에 DACPAC를 적용하는 작업을 만들 수 있습니다. 다음 PowerShell 스크립트를 사용하여 사용자 지정 데이터베이스 컬렉션에 대한 DACPAC 작업을 만들 수 있습니다.

    $jobName = "{Job Name}"
    $dacpacName = "{Dacpac Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget 
    -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob 
    -JobName $jobName 
    -CredentialName $credentialName 
    -ContentName $dacpacName -TargetId $target.TargetId
    Write-Output $job 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-powershell/cmd-prompt.png
[2]: ./media/sql-database-elastic-jobs-powershell/portal.png
<!--anchors-->
