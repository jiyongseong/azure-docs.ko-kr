---
title: Elastic Database 작업 시작 | Microsoft Docs
description: 여러 데이터베이스에 걸친 T-SQL 스크립트를 실행하려면 탄력적 데이터베이스 작업을 사용합니다.
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 2d8760476fd7b8a5a11d01a434a383ec1ba27ef1
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="getting-started-with-elastic-database-jobs"></a>Elastic Database 작업 시작
Azure SQL Database에 대한 Elastic Database job(미리 보기)을 사용하면 자동으로 다시 시도하여 최종 완료를 보장하는 동시에 여러 데이터베이스에 걸친 T-SQL 스크립트를 안정적으로 실행할 수 있습니다. Elastic Database 작업 기능에 대한 자세한 내용은 [탄력적 작업](sql-database-elastic-jobs-overview.md)을 참조하세요.

이 문서에는 [Elastic Database 도구 시작](sql-database-elastic-scale-get-started.md)의 샘플의 확장이 나와 있습니다. 완료되면 관련 데이터베이스 그룹을 관리하는 작업을 만들고 관리하는 방법을 살펴봅니다. 탄력적 작업의 이점을 활용하기 위해 탄력적 확장 도구를 사용할 필요는 없습니다.

## <a name="prerequisites"></a>필수 조건
[Elastic Database 도구 샘플 시작](sql-database-elastic-scale-get-started.md)을 다운로드하고 실행하세요.

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>샘플 응용 프로그램을 사용하여 분할된 데이터베이스 맵 관리자 만들기
분할된 데이터베이스 안의 삽입된 데이터에 따라 여느 분할된 데이터 베이스와 마찬가지로 분할된 데이터 베이스 관리자를 만들 수 있습니다. 이미 분할된 데이터가 설치되어 있는 분할된 데이터베이스가 있다면, 다음 단계들을 건너뛰고 다음 섹션으로 이동합니다.

1. **Elastic Database 도구 응용 프로그램** 을 빌드하고 실행하세요. [샘플 앱 다운로드 및 실행](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app)섹션에서 7단계까지 수행합니다. 7단계를 끝내면 다음 명령 프롬프트를 볼 수 있습니다.

   ![명령 프롬프트](./media/sql-database-elastic-query-getting-started/cmd-prompt.png)

2. 명령 창에 "1"을 입력하고 **Enter**키를 누릅니다. 이 명령은 분할된 데이터베이스 관리자를 생성 및 두 분할된 데이터베이스를 추가합니다. 그런 다음 "3"을 입력하고 **Enter** 키를 누릅니다. 이 작업을 4번 반복합니다. 이 명령은 분할된 데이터베이스에 샘플 데이터행을 삽입합니다.
3. [Azure Portal](https://portal.azure.com)에서 다음 3개의 새 데이터베이스가 표시됩니다.

   ![Visual Studio 확인](./media/sql-database-elastic-query-getting-started/portal.png)

   이 시점에서 분할된 데이터베이스 맵에 있는 모든 데이터베이스를 반영하는 사용자 지정 데이터베이스 컬렉션을 만듭니다. 그러면 분할된 데이터베이스에서 새 테이블을 추가하는 작업을 만들고 실행할 수 있습니다.

일반적으로 여기서 **New-AzureSqlJobTarget** cmdlet을 사용하여 분할된 데이터베이스 맵 대상을 만듭니다. 분할된 데이터베이스 맵 관리자 데이터베이스를 데이터베이스 대상으로 설정해야 하며, 그러면 분할된 특정 데이터베이스 맵이 대상으로 지정됩니다. 대신, 서버에 있는 모든 데이터베이스를 열거하고 master 데이터베이스 이외의 데이터베이스를 새 사용자 지정 컬렉션에 추가하겠습니다.

## <a name="creates-a-custom-collection-and-add-all-databases-in-the-server-to-the-custom-collection-target-with-the-exception-of-master"></a>사용자 지정 컬렉션을 만들고 마스터를 제외한 서버의 모든 데이터베이스를 사용자 지정 컬렉션에 추가합니다.
   ```
    $customCollectionName = "dbs_in_server"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $ResourceGroupName = "ddove_samples"
    $ServerName = "samples"
    $dbsinserver = Get-AzureRMSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName
    $dbsinserver | %{
    $currentdb = $_.DatabaseName
    $ErrorActionPreference = "Stop"
    Write-Output ""

    Try
    {
       New-AzureSqlJobTarget -ServerName $ServerName -DatabaseName $currentdb | Write-Output
    }
    Catch
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason

        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already a database target."
        }

        else
        {
            throw $_
        }

    }

    Try
    {
        if ($currentdb -eq "master")
        {
            Write-Host $currentdb "will not be added custom collection target" $CustomCollectionName "."
        }

        else
        {
            Add-AzureSqlJobChildTarget -CustomCollectionName $CustomCollectionName -ServerName $ServerName -DatabaseName $currentdb
            Write-Host $currentdb "was added to" $CustomCollectionName "."
        }

    }
    Catch
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason

        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already in the custom collection target" $CustomCollectionName"."
        }

        else
        {
            throw $_
        }
    }
    $ErrorActionPreference = "Continue"
   }
   ```
## <a name="create-a-t-sql-script-for-execution-across-databases"></a>데이터베이스에서 실행할 T-SQL 스크립트 만들기
   ```
    $scriptName = "NewTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'Test')
    BEGIN
        CREATE TABLE Test(
            TestId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO Test(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script
   ```

## <a name="create-the-job-to-execute-a-script-across-the-custom-group-of-databases"></a>사용자 지정 데이터베이스 그룹에서 스크립트를 실행하는 작업을 만듭니다.

   ```
    $jobName = "create on server dbs"
    $scriptName = "NewTable"
    $customCollectionName = "dbs_in_server"
    $credentialName = "ddove66"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job
   ```

## <a name="execute-the-job"></a>작업 실행
다음 PowerShell 스크립트를 사용하여 기존 작업을 실행할 수 있습니다.

실행하려는 작업 이름이 반영되도록 다음 변수를 업데이트합니다.

   ```
    $jobName = "create on server dbs"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution
   ```

## <a name="retrieve-the-state-of-a-single-job-execution"></a>단일 작업 실행 상태 검색
**IncludeChildren** 매개 변수와 함께 동일한 **Get-AzureSqlJobExecution cmdlet**을 사용하여 자식 작업 실행 상태, 즉 작업 대상인 각 데이터베이스에 대한 각 작업 실행의 특정 상태를 표시할 수 있습니다.

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions
   ```

## <a name="view-the-state-across-multiple-job-executions"></a>여러 작업 실행 간에 상태 보기
**Get-AzureSqlJobExecution** cmdlet에는 제공된 매개 변수를 통해 필터링되는 여러 작업 실행을 표시하는 데 사용할 수 있는 여러 선택적 매개 변수가 있습니다. 아래에서는 Get-AzureSqlJobExecution을 사용할 수 있는 여러 방법을 보여 줍니다.

모든 활성 최상위 작업 실행을 검색합니다.

   ```
    Get-AzureSqlJobExecution
   ```

비활성 작업 실행을 포함하여 모든 최상위 작업 실행을 검색합니다.

   ```
    Get-AzureSqlJobExecution -IncludeInactive
   ```

비활성 작업 실행을 포함하여 제공된 작업 실행 ID의 모든 자식 작업 실행을 검색합니다.

   ```
    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId -IncludeInactive -IncludeChildren
   ```

비활성 작업을 포함하여 일정/작업 조합으로 만든 모든 작업 실행을 검색합니다.

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive
   ```

비활성 작업을 포함하여 지정된 분할된 데이터베이스 맵을 대상으로 하는 모든 작업을 검색합니다.

   ```
    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive
   ```

비활성 작업을 포함하여 지정된 사용자 지정 컬렉션을 대상으로 하는 모든 작업을 검색합니다.

   ```
    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive
   ```

특정 작업 실행 내의 작업 태스크 실행 목록을 검색합니다.

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions
   ```

작업 태스크 실행 세부 정보를 검색합니다.

다음 PowerShell 스크립트를 사용하여 실행 실패를 디버그할 때 특히 유용한 작업 태스크 실행 세부 정보를 볼 수 있습니다.
   ```
    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution
   ```

## <a name="retrieve-failures-within-job-task-executions"></a>작업 태스크 실행 내의 오류 검색
JobTaskExecution 개체에는 Message 속성과 함께 Lifecycle 주기에 대한 속성이 포함되어 있습니다. 작업 태스크 실행에 실패한 경우 수명 주기 속성이 *Failed*로 설정되고 메시지 속성은 결과 예외 메시지 및 해당 스택으로 설정됩니다. 작업이 수행되지 않은 경우 지정된 작업에 대해 실패한 작업 태스크 세부 정보를 보는 것이 중요합니다.

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions)
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }
   ```

## <a name="waiting-for-a-job-execution-to-complete"></a>작업 실행이 완료될 때까지 대기
다음 PowerShell 스크립트를 사용하여 작업 태스크가 완료될 때까지 기다릴 수 있습니다.

   ```
    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId
   ```

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

   ```
    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 10
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $executionPolicy
   ```

### <a name="update-a-custom-execution-policy"></a>사용자 지정 실행 정책 업데이트
업데이트하려는 실행 정책을 업데이트합니다.

   ```
    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 15
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $updatedExecutionPolicy
   ```

## <a name="cancel-a-job"></a>작업 취소
Elastic Database 작업은 작업 취소 요청을 지원합니다.  Elastic Database 작업이 현재 실행 중인 작업에 대한 취소 요청을 감지하는 경우 작업을 중지하려고 합니다.

Elastic Database 작업이 취소를 수행할 수 있는 방법에는 다음 두 가지가 있습니다.

1. 현재 실행 중인 작업 취소: 작업이 현재 실행되는 동안 취소가 감지되면 현재 실행 중인 작업 측면 내에서 취소가 시도됩니다.  예를 들어 현재 장기 실행 쿼리를 수행하는 동안 취소가 시도되면 쿼리를 취소하려고 합니다.
2. 태스크 재시도 취소: 태스크 실행이 시작되기 전에 제어 스레드에서 취소가 감지되면 제어 스레드는 태스크를 시작하지 않고 요청이 취소된 것으로 선언합니다.

부모 작업에 대해 작업 취소가 요청된 경우 부모 작업 및 모든 자식 작업에 대해 취소 요청이 적용됩니다.

취소 요청을 제출하려면 **Stop-AzureSqlJobExecution** cmdlet을 사용하고 을 사용하고 **JobExecutionId** 매개 변수를 설정합니다.

   ```
    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId
   ```

## <a name="delete-a-job-by-name-and-the-jobs-history"></a>이름 및 작업 기록으로 작업 삭제
Elastic Database 작업은 비동기 작업 삭제를 지원합니다. 작업을 삭제되도록 표시할 수 있으며, 작업에 대한 모든 작업 실행이 완료된 후 작업 및 모든 작업 기록이 삭제됩니다. 활성 작업 실행은 자동으로 취소되지 않습니다.  

활성 작업 실행을 취소하려면 Stop-AzureSqlJobExecution을 호출해야 합니다.

작업 삭제를 트리거하려면 **Remove-AzureSqlJob** cmdlet을 사용하고 **JobName** 매개 변수를 설정합니다.

   ```
    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName
   ```

## <a name="create-a-custom-database-target"></a>사용자 지정 데이터베이스 대상 만들기
실행에 직접 사용하거나 사용자 지정 데이터베이스 그룹에 포함할 수 있는 Elastic Database 작업에서 사용자 지정 데이터베이스 대상을 정의할 수 있습니다. **탄력적 풀** 은 PowerShell API를 통해 직접 지원되지 않으므로 풀에 있는 모든 데이터베이스를 포함하는 사용자 지정 데이터베이스 컬렉션 대상 및 사용자 지정 데이터베이스 대상을 만듭니다.

원하는 데이터베이스 정보가 반영되도록 다음 변수를 설정합니다.

   ```
    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName
   ```

## <a name="create-a-custom-database-collection-target"></a>사용자 지정 데이터베이스 컬렉션 대상 만들기
정의된 여러 데이터베이스 대상에서 실행할 수 있도록 사용자 지정 데이터베이스 컬렉션 대상을 정의할 수 있습니다. 데이터베이스 그룹을 만든 후 사용자 지정 컬렉션 대상에 데이터베이스를 연결할 수 있습니다.

원하는 사용자 지정 컬렉션 대상 구성이 반영되도록 다음 변수를 설정합니다.

   ```
    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName
   ```

### <a name="add-databases-to-a-custom-database-collection-target"></a>사용자 지정 데이터베이스 컬렉션 대상에 데이터베이스 추가
데이터베이스 대상을 사용자 지정 데이터베이스 컬렉션 대상에 연결하여 데이터베이스 그룹을 만들 수 있습니다. 사용자 지정 데이터베이스 컬렉션 대상을 대상으로 하는 작업을 만들 때마다 실행 시 그룹에 연결된 데이터베이스를 대상으로 하도록 확장됩니다.

특정 사용자 지정 컬렉션에 원하는 데이터베이스를 추가합니다.

   ```
    $serverName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName
   ```

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>사용자 지정 데이터베이스 컬렉션 대상 내의 데이터베이스 검토
**Get-AzureSqlJobTarget** cmdlet을 사용하여 사용자 지정 데이터베이스 컬렉션 대상 내의 자식 데이터베이스를 검색할 수 있습니다.

   ```
    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets
   ```

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>사용자 지정 데이터베이스 컬렉션 대상에서 스크립트를 실행하는 작업 만들기
**New-AzureSqlJob** cmdlet을 사용하여 사용자 지정 데이터베이스 컬렉션 대상에서 정의된 데이터베이스 그룹에 대한 작업을 만들 수 있습니다. Elastic Database 작업은 각각 사용자 지정 데이터베이스 컬렉션 대상과 연결된 데이터베이스에 해당하는 여러 자식 작업으로 작업을 확장하고 각 데이터베이스에 대해 스크립트가 실행되도록 합니다. 스크립트는 재시도 복구에 대해 idempotent여야 합니다.

   ```
    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job
   ```

## <a name="data-collection-across-databases"></a>데이터베이스에서 데이터 수집
**Elastic Database 작업** 은 데이터베이스 그룹에 대한 쿼리 실행을 지원하고 지정된 데이터베이스 테이블에 결과를 보냅니다. 각 데이터베이스의 쿼리 결과를 볼 수 있으면 테이블을 쿼리할 수 있습니다. 이는 많은 데이터베이스에서 쿼리를 실행하는 비동기 메커니즘을 제공합니다. 데이터베이스 중 하나를 일시적으로 사용할 수 없는 경우와 같은 오류 사례는 재시도를 통해 자동으로 처리됩니다.

반환된 결과 집합의 스키마와 일치하는 지정된 대상 테이블이 아직 없는 경우 자동으로 만들어집니다. 스크립트 실행에서 여러 결과 집합이 반환되는 경우 Elastic Database 작업은 제공된 대상 테이블에 첫 번째 결과 집합만 보냅니다.

다음 PowerShell 스크립트를 사용하여 지정된 테이블에 결과를 수집하는 스크립트를 실행할 수 있습니다. 이 스크립트는 단일 결과 집합을 출력하는 T-SQL 스크립트가 생성되었으며 사용자 지정 데이터베이스 컬렉션 대상이 생성되었다고 가정합니다.

원하는 스크립트, 자격 증명 및 실행 대상이 반영되도록 다음을 설정합니다.

   ```
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
   ```

### <a name="create-and-start-a-job-for-data-collection-scenarios"></a>데이터 수집 시나리오에 대한 작업 만들기 및 시작
   ```
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $executionCredentialName -ContentName $scriptName -ResultSetDestinationServerName $destinationServerName -ResultSetDestinationDatabaseName $destinationDatabaseName -ResultSetDestinationSchemaName $destinationSchemaName -ResultSetDestinationTableName $destinationTableName -ResultSetDestinationCredentialName $destinationCredentialName -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution
   ```

## <a name="create-a-schedule-for-job-execution-using-a-job-trigger"></a>작업 트리거를 사용하여 작업 실행 일정 만들기
다음 PowerShell 스크립트를 사용하여 되풀이 일정을 만들 수 있습니다. 이 스크립트는 1분 간격을 사용하지만 New-AzureSqlJobSchedule은 -DayInterval, -HourInterval, -MonthInterval 및 -WeekInterval 매개 변수도 지원합니다. -OneTime을 전달하여 한 번만 실행되는 일정을 만들 수 있습니다.

새 일정을 만듭니다.
   ```
    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule -MinuteInterval $minuteInterval -ScheduleName $scheduleName -StartTime $startTime
    Write-Output $schedule
   ```

### <a name="create-a-job-trigger-to-have-a-job-executed-on-a-time-schedule"></a>시간 일정에 따라 작업을 실행하는 작업 트리거 만들기
시간 일정에 따라 작업을 실행하는 작업 트리거를 정의할 수 있습니다. 다음 PowerShell 스크립트를 사용하여 작업 트리거를 만들 수 있습니다.

원하는 작업 및 일정에 맞게 다음 변수를 설정합니다.

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName
    Write-Output $jobTrigger
   ```

### <a name="remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>일정에 따라 작업이 실행되지 않도록 예약된 연결 제거
작업 트리거를 통한 되풀이 작업 실행을 중단하려면 작업 트리거를 제거할 수 있습니다.
**Remove-AzureSqlJobTrigger** cmdlet을 사용하여 일정에 따라 작업이 실행되지 않도록 작업 트리거를 제거합니다.

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName
   ```

## <a name="import-elastic-database-query-results-to-excel"></a>탄력적 데이터베이스 쿼리 결과를 Excel로 가져오기
 쿼리의 결과를 엑셀파일로 가져올 수 있습니다.

1. Excel 2013을 실행 합니다.
2. **데이터** 리본을 탐색합니다.
3. **기타 원본에서**을 클릭하고 **SQL Server에서**를 클릭합니다.

   ![다른 원본에서 Excel 가져오기](./media/sql-database-elastic-query-getting-started/exel-sources.png)

4. **데이터 연결 마법사** 에서 서버 이름 및 로그인 자격 증명을 입력합니다. 그런 후 **Next** 를 클릭합니다.
5. 대화 상자에서 **원하는 데이터를 포함하는 데이터베이스를 선택**하고 **ElasticDBQuery** 데이터베이스를 선택합니다.
6. 목록 보기에서 **사용자**테이블을 선택하고 **다음**을 클릭합니다. **마침**을 클릭합니다.
7. **데이터 가져오기** 양식에서, **통합 문서에서 원하는 데이터를 보는 방법을 선택**하고 **테이블**을 선택하고 **확인**을 클릭합니다.

다른 분할된 데이터베이스에 저장된 **Customers** 테이블의 모든 행으로 Excel 시트를 채웁니다.

## <a name="next-steps"></a>다음 단계
이제 Excel의 데이터 함수를 사용할 수 있습니다. 탄력적 쿼리 데이터 베이스의 데이터 통합 도구 및 BI과 연결하기 위해 서버 이름, 데이터베이스 이름, 자격 증명과 연결 문자열을 사용할 수 있습니다. SQL Server 도구에 대한 데이터 소스로 지원 되는지 확인 합니다. 탄력적 쿼리 데이터베이스 및 기타 SQL Server 데이터베이스와 마찬가지로 외부 테이블 및 도구와 연결할 수 있는 SQL Server 테이블을 참조할 수 있습니다.

### <a name="cost"></a>비용
Elastic Database 쿼리 기능을 사용 하는 것은 무료입니다. 단, 현재 이 기능은 프리미엄 및 중요 비즈니스용(미리 보기) 데이터베이스 및 탄력적 풀을 엔드포인트로 사용할 수 있지만 분할은 모든 서비스 계층이 될 수 있습니다.

가격 정보는 [SQL Database 가격 정보](https://azure.microsoft.com/pricing/details/sql-database/)를 참조하세요.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
