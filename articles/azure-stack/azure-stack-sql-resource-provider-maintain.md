---
title: SQL 데이터베이스를 사용 하 여 Azure 스택에 | Microsoft Docs
description: SQL 데이터베이스에서 SQL Server 리소스 공급자 어댑터를 배포 하는 Azure 스택 및 빠른 단계 서비스로 배포 하는 방법을 알아봅니다.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 53436d131672622ae1a72a1bb84d5aa83fdbdc0c
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
---
# <a name="maintenance-operations"></a>유지 관리 작업 
SQL 리소스 공급자를 통해 가상 컴퓨터는 잠금 되어 있습니다. PowerShell 관리 JEA (Just Enough) 끝점을 통해 리소스 공급자 가상 컴퓨터의 보안 업데이트를 수행할 수 있습니다 _DBAdapterMaintenance_합니다. 스크립트는 이러한 작업을 용이 하 게 하려면 RP의 설치 패키지와 함께 제공 됩니다.

## <a name="patching-and-updating"></a>패치 및 업데이트
SQL 리소스 공급자는 추가 구성 요소 이므로 Azure 스택의 일환으로 서비스 되지 있습니다. Microsoft 업데이트 필요에 따라 SQL 리소스 공급자에 제공 됩니다. SQL 리소스 공급자를 인스턴스화할는 _사용자_ 공급자 기본 구독에서 가상 컴퓨터. 따라서은 Windows 패치, 바이러스 백신 서명 등을 제공 해야 합니다. Windows는 Windows VM에 업데이트를 적용할 패치 업데이트 주기의 일부로 사용할 수 있습니다에 제공 되는 패키지를 업데이트 합니다. 업데이트 된 어댑터를 놓을 때 업데이트를 적용 하는 스크립트 제공 됩니다. 이 스크립트는 새 RP VM 만들고 이미 있는 모든 상태를 마이그레이션할 합니다.

 ## <a name="backuprestoredisaster-recovery"></a>백업/복원/장애 복구
 SQL 리소스 공급자가 백업 되지 Azure 스택 BC DR 프로세스의 일부로 추가 구성 요소 이므로. 용이 하 게 스크립트를 제공 합니다.
- 필요한 상태 정보 (Azure 스택 저장소 계정에 저장)를 백업 합니다.
- 전체 스택 복구를 수행 해야 경우에 RP를 복원 합니다.
데이터베이스 서버를 복구 해야 먼저 (필요한 경우), 리소스 하기 전에 공급자 복원 됩니다.

## <a name="updating-sql-credentials"></a>SQL 자격 증명 업데이트
사용자는 생성 및 SQL server에서 시스템 관리자 계정 유지 관리에 대 한입니다. 리소스 공급자가 데이터베이스 사용자를 대신 하 여 관리 하려면 이러한 권한이 있는 계정을-해당 데이터베이스의 데이터에 액세스할 필요는 없습니다. SQL server의 sa 암호를 업데이트 해야 할 경우에 리소스 공급자에서 사용 하는 저장 된 암호를 변경 하려면 리소스 공급자의 관리자 인터페이스의 업데이트 기능을 사용할 수 있습니다. 이 암호는 Azure 스택 인스턴스에서 키 자격 증명 모음에 저장 됩니다.

설정을 수정 하려면 **찾아보기** &gt; **관리 리소스** &gt; **SQL 호스팅 서버** &gt; **SQL 로그인** 로그인 이름을 선택 합니다. 변경 내용을 SQL 인스턴스에서 먼저 수행 되어야 합니다 (및 필요한 경우 모든 복제본). 에 **설정** 패널에서 클릭 **암호**합니다.

![관리자 암호를 업데이트 합니다.](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)

## <a name="update-the-virtual-machine-operating-system"></a>가상 컴퓨터 운영 체제를 업데이트 합니다.
Windows Server VM을 업데이트 하는 방법은 여러 가지가 있습니다.
* 현재 패치가 적용 된 Windows Server 2016 Core 이미지를 사용 하 여 최신 리소스 공급자 패키지 설치
* 설치 또는 RP 업데이트 하는 동안 Windows Update 패키지를 설치 합니다.

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>가상 컴퓨터의 Windows Defender 정의 업데이트
Defender 정의 업데이트 하려면 다음이 단계를 수행 합니다.

1. Windows Defender 정의 업데이트에서 다운로드 [Windows Defender 정의](https://www.microsoft.com/en-us/wdsi/definitions)

    이 페이지에서 다운로드에 "수동으로 다운로드 및 설치 정의"에서 "Windows 10 및 Windows 8.1" 64 비트 파일에 대 한 Windows Defender 바이러스 백신 합니다. 
    
    직접 링크: https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64

2. SQL RP 어댑터 가상 컴퓨터의 유지 관리 끝점에 대 한 PowerShell 세션 만들기
3. 유지 관리 끝점 세션을 사용 하 여 DB 어댑터 컴퓨터로 정의 업데이트 파일을 복사 합니다.
4. 세션에서 유지 관리 PowerShell 호출의 _업데이트 DBAdapterWindowsDefenderDefinitions_ 명령
5. 설치 후 사용 되는 정의 업데이트 파일을 제거 하는 것이 좋습니다. 사용 하 여 유지 관리 세션에서 제거할 수 있습니다는 _제거 ItemOnUserDrive)_ 명령입니다.


(주소 또는 실제 값으로 가상 컴퓨터의 이름으로 대체) Defender 정의 업데이트 하는 샘플 스크립트는 다음과 같습니다.

```powershell
# Set credentials for the diagnostic user
$diagPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$diagCreds = New-Object System.Management.Automation.PSCredential `
    ("dbadapterdiag", $vmLocalAdminPass)$diagCreds = Get-Credential

# Public IP Address of the DB adapter machine
$databaseRPMachine  = "XX.XX.XX.XX"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"
 
# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions. 
Invoke-WebRequest -Uri https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64 `
    -Outfile $localPathToDefenderUpdate 

# Create session to the maintenance endpoint
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $diagCreds -ConfigurationName DBAdapterMaintenance
# Copy defender update file to the db adapter machine
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\mpam-fe.exe"
# Install the update file
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinitions -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession
```


## <a name="collect-diagnostic-logs"></a>진단 로그를 수집 합니다.
SQL 리소스 공급자를 통해 가상 컴퓨터는 잠금 되어 있습니다. PowerShell 관리 JEA (Just Enough) 끝점은 가상 컴퓨터에서 로그를 수집 해야 하는 경우 _DBAdapterDiagnostics_ 목적으로 제공 됩니다. 이 끝점을 통해 사용할 수 있는 두 개의 명령입니다.

* Get AzsDBAdapterLog-RP 진단 로그를 포함 하는 zip 패키지를 준비 하 고 세션 사용자 드라이브에 저장 합니다. 명령 매개 변수 없이 호출 될 수 있으며 로그의 마지막 4 시간을 수집 합니다.
* 리소스 공급자 VM에서 기존 로그 패키지를 제거 AzsDBAdapterLog-정리

사용자 계정 이라는 _dbadapterdiag_ RP 배포 또는 RP 로그를 추출 하기 위한 진단 끝점에 연결 하기 위한 업데이트 중에 만들어집니다. 이 계정은 암호가 배포/업데이트 중에 로컬 관리자 계정에 대해 제공 된 암호와 동일 합니다.

이러한 명령을 사용 하 여 리소스 공급자 가상 컴퓨터에 원격 PowerShell 세션을 만들고 명령을 호출 해야 합니다. 필요에 따라 FromDate 및 ToDate 매개 변수를 제공할 수 있습니다. 하나 또는 둘 다 지정 하지 않으면의 FromDate을 현재 시간 전 4 시간 되며는 ToDate 현재 시간이 됩니다.

이 샘플 스크립트 이러한 명령의 사용을 보여 줍니다.

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<see above>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, $diagnosticsUserPassword)
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
        -ConfigurationName DBAdapterDiagnostics

# Sample captures logs from the previous one hour
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup logs
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove- AzsDBAdapterLog }
# Close the session
$session | Remove-PSSession
```

## <a name="next-steps"></a>다음 단계
[SQL Server 호스팅 서버를 추가 합니다.](azure-stack-sql-resource-provider-hosting-servers.md)
