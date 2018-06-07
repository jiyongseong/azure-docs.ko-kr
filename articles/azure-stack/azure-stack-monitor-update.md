---
title: "업데이트 권한이 있는 끝점을 사용 하 여 Azure 스택의 모니터링 | Microsoft Docs"
description: "Azure 스택 통합 시스템에 대 한 업데이트 상태를 모니터링 하는 권한 있는 끝점을 사용 하는 방법에 알아봅니다."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: mabrigg
ms.openlocfilehash: 96eebf340f13f2f5e9e922fee8032d04fce1d130
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/06/2018
---
# <a name="monitor-updates-in-azure-stack-using-the-privileged-endpoint"></a>권한 있는 끝점을 사용 하 여 Azure 스택의 업데이트 모니터링

*적용 대상: Azure 스택 시스템 통합*

에서는 Azure 스택 업데이트 실행의 진행률을 모니터링 하려면 권한 있는 끝점을 사용할 수 있으며 실행 성공 하는 마지막 단계에서 실패 한 업데이트를 다시 시작 해야 Azure 스택 포털을 사용할 수 없습니다.  Azure 스택에서 업데이트를 관리 스택 Azure 포털을 사용 하는 것이 좋습니다.

업데이트 관리를 위한 다음과 같은 새 PowerShell cmdlet은 Azure 스택 통합 시스템에 대 한 1710 업데이트에 포함 됩니다.

| Cmdlet  | 설명  |
|---------|---------|
| `Get-AzureStackUpdateStatus` | 현재 실행 중, 완료 됨 또는 실패 한 업데이트의 상태를 반환합니다. 현재 단계 및 해당 상태를 모두 설명 하는 XML 문서는 업데이트 작업의 개략적인 상태를 제공 합니다. |
| `Get-AzureStackUpdateVerboseLog` | 업데이트에 의해 생성 되는 자세한 로그를 반환 합니다. |
| `Resume-AzureStackUpdate` | 실패 한 위치 지점에서 실패 한 업데이트를 다시 시작 합니다. 특정 시나리오에서 업데이트를 다시 시작 하기 전에 완화 단계를 완료 해야 할 수 있습니다.         |
| | |

## <a name="verify-the-cmdlets-are-available"></a>Cmdlet을 사용할 수 확인
Cmdlet은 Azure 스택에 대 한 1710 업데이트 패키지의 새로운 기능 이므로, 1710 업데이트 프로세스를 모니터링 기능을 사용할 수 있으려면 특정 시점에 도달 해야 합니다. 일반적으로 cmdlet은 관리자 포털에서 상태 1710 업데이트 언제 임을 나타내는 경우에 사용할 수는 **저장소 호스트를 다시 시작** 단계입니다. Cmdlet이 업데이트 하는 동안 발생 하는 구체적으로, **단계: 실행 단계 2.6-업데이트 PrivilegedEndpoint 화이트 리스트**합니다.

Cmdlet은 사용할 수 있는지 프로그래밍 방식으로 권한 있는 끝점에서 명령 목록을 쿼리하여 확인할 수 있습니다. 이 위해 하드웨어 수명 주기 호스트 또는 권한 있는 액세스 워크스테이션에서 다음 명령을 실행 합니다. 권한 있는 끝점에 신뢰할 수 있는 호스트 되는지 확인 합니다. 자세한 내용은 참조 ल ो 1 प [권한 있는 끝점에 액세스할](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)합니다. 

1. Azure 스택 환경에서 ERCS 가상 컴퓨터에서 PowerShell 세션을 만듭니다 (*접두사*-ERCS01, *접두사*-ERCS02, 또는 *접두사*-ERCS03). 대체 *접두사* 사용자 환경에만 적용 되는 가상 컴퓨터 접두사 문자열을 사용 합니다.

   ```powershell
   $cred = Get-Credential

   $pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
   ```
   자격 증명에 대 한 요청 시 사용 된 &lt; *Azure 스택 도메인*&gt;\cloudadmin 계정이 나 CloudAdmins 그룹의 구성원 인 계정입니다. CloudAdmin 계정에 대 한 AzureStackAdmin 도메인 관리자 계정에 대 한 설치 중에 제공 된 동일한 암호를 입력 합니다.

2. 권한 있는 끝점에서 사용할 수 있는 명령의 전체 목록을 가져옵니다. 

   ```powershell
   $commands = Invoke-Command -Session $pepSession -ScriptBlock { Get-Command } 
   ```
3. 권한 있는 끝점이 업데이트 된 경우를 결정 합니다.

   ```powershell
   $updateManagementModuleName = "Microsoft.Azurestack.UpdateManagement"
    if (($commands | ? Source -eq $updateManagementModuleName)) {
   Write-Host "Privileged endpoint was updated to support update monitoring tools."
    } else {
   Write-Host "Privileged endpoint has not been updated yet. Please try again later."
    } 
   ```

4. Microsoft.AzureStack.UpdateManagement 모듈의 고유한 명령을 보여 줍니다.

   ```powershell
   $commands | ? Source -eq $updateManagementModuleName 
   ```
   예: 
   ```powershell
   $commands | ? Source -eq $updateManagementModuleName
   
   CommandType     Name                                               Version    Source                                                  PSComputerName
    -----------     ----                                               -------    ------                                                  --------------
   Function        Get-AzureStackUpdateStatus                         0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   Function        Get-AzureStackUpdateVerboseLog                     0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   Function        Resume-AzureStackUpdate                            0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   ``` 

## <a name="use-the-update-management-cmdlets"></a>업데이트 관리 cmdlet을 사용 하 여

> [!NOTE]
> 하드웨어 수명 주기 호스트 또는 권한 있는 액세스 워크스테이션에서 다음 명령을 실행 합니다. 권한 있는 끝점에 신뢰할 수 있는 호스트 되는지 확인 합니다. 자세한 내용은 참조 ल ो 1 प [권한 있는 끝점에 액세스할](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)합니다.

### <a name="connect-to-the-privileged-endpoint-and-assign-session-variable"></a>권한 있는 끝점에 연결 하 고 세션 변수 할당

Azure 스택 환경에서 ERCS 가상 컴퓨터에서 PowerShell 세션을 만들려면 다음 명령을 실행 (*접두사*-ERCS01, *접두사*-ERCS02, 또는 *접두사*-ERCS03), 세션 변수를 할당할 수 있습니다.

```powershell
$cred = Get-Credential

$pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
```
 자격 증명에 대 한 요청 시 사용 된 &lt; *Azure 스택 도메인*&gt;\cloudadmin 계정이 나 CloudAdmins 그룹의 구성원 인 계정입니다. CloudAdmin 계정에 대 한 AzureStackAdmin 도메인 관리자 계정에 대 한 설치 중에 제공 된 동일한 암호를 입력 합니다.

### <a name="get-high-level-status-of-the-current-update-run"></a>현재 업데이트 실행의 개략적인 상태를 가져옵니다. 

현재 업데이트 실행의 개략적인 상태를 가져오려면 다음 명령을 실행 합니다. 

```powershell
$statusString = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus -StatusOnly }

$statusString.Value 
```

가능한 값은 다음과 같습니다.

- 실행 중
- Completed
- 실패 
- Canceled

이 명령은 최신 상태를 반복적으로 실행할 수 있습니다. 다시 검사에 대 한 연결을 다시 설정할 필요가 없습니다.

### <a name="get-the-full-update-run-status-with-details"></a>실행 상태 세부 정보로 전체 업데이트 가져오기 

XML 문자열 요약 실행 전체 업데이트를 가져올 수 있습니다. 문자열에 검토를 하기 위해 파일에 기록 하 고 또는 XML 문서로 변환 하 고, PowerShell을 사용 하 여 구문 분석 한 후 수 있습니다. 다음 명령은 현재 실행 중인 단계의 계층적 목록을 가져오려면 XML 구문 분석 합니다.

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']")
```

다음 예제에서는 최상위 단계 (클라우드 업데이트)를 업데이트 한 저장소 호스트를 다시 시작 하위 계획을 있습니다. 저장소 호스트를 다시 시작 계획 호스트 중 하나에서 Blob 저장소 서비스를 업데이트 하 고 있음을 보여 줍니다.

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']") 

    FullStepIndex : 2
    Index         : 2
    Name          : Cloud Update
    Description   : Perform cloud update.
    StartTimeUtc  : 2017-10-13T12:50:39.9020351Z
    Status        : InProgress
    Task          : Task
    
    FullStepIndex  : 2.9
    Index          : 9
    Name           : Restart Storage Hosts
    Description    : Restart Storage Hosts.
    EceErrorAction : Stop
    StartTimeUtc   : 2017-10-13T15:44:06.7431447Z
    Status         : InProgress
    Task           : Task
    
    FullStepIndex : 2.9.2
    Index         : 2
    Name          : PreUpdate ACS Blob Service
    Description   : Check function level, update deployment artifacts, configure Blob service settings
    StartTimeUtc  : 2017-10-13T15:44:26.0708525Z
    Status        : InProgress
    Task          : Task
```

### <a name="get-the-verbose-progress-log"></a>자세한 진행률 로그를 가져오려면

검사 파일에 로그를 작성할 수 있습니다. 이 업데이트에 실패를 진단 하는 데 수 있습니다.

```powershell
$log = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateVerboseLog }

$log > ".\UpdateVerboseLog.txt" 
```

### <a name="actively-view-the-verbose-logging"></a>자세한 로깅 적극적으로 보려면

적극적으로를 실행 하 고 가장 최근 항목으로 이동 하는 업데이트 하는 동안 자세한 로그 보기가 대화형 모드로 세션을 시작 하 고 로그를 표시 하는 다음 명령의 실행 합니다.

```powershell
Enter-PSSession -Session $pepSession 

Get-AzureStackUpdateVerboseLog -Wait 
```
로그 60 초 마다 업데이트 및 새 콘텐츠 (있는 경우)는 콘솔에 기록 됩니다. 

장기 실행 백그라운드 처리 동안 콘솔 출력은 쓸 수 없습니다 콘솔에 일정 시간 동안 합니다. 대화형 출력을 취소 하려면 Ctrl + C를 누릅니다. 

### <a name="resume-a-failed-update-operation"></a>실패 한 업데이트 작업을 다시 시작

업데이트가 실패 하면 업데이트 중단 된 실행을 재개할 수 있습니다.

```powershell
Invoke-Command -Session $pepSession -ScriptBlock { Resume-AzureStackUpdate } 
```

## <a name="troubleshoot"></a>문제 해결

권한 있는 끝점은 Azure 스택 환경에서 모든 ERCS 가상 컴퓨터에서 사용할 수 있습니다. 항상 사용 가능한 끝점에는 연결을 실행 하지 못하면, 때문에 가끔 중단, 경고 또는 오류 메시지가 발생할 수 있습니다. 이러한 메시지는 세션 연결이 해제 되었습니다 또는 ECE 서비스와 통신 오류가 발생 했음을 나타낼 수 있습니다. 이 동작은 사용할 수 있습니다. 잠시 후에 작업을 다시 시도 하거나 다른 ERCS 가상 컴퓨터 중 하나에서 새 권한 있는 끝점 세션을 만들 수 있습니다. 

## <a name="next-steps"></a>다음 단계

- [Azure 스택에서 업데이트 관리](azure-stack-updates.md) 


