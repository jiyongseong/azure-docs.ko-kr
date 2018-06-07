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
ms.date: 05/23/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: fd1c2241fe22dc35ceb09e0ba3650fa0000a77b1
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34603619"
---
# <a name="update-the-sql-resource-provider-adapter"></a>SQL 리소스 공급자 어댑터 업데이트
Azure 스택 빌드를 업데이트할 때 새 SQL 리소스 공급자 어댑터를 해제 될 수 있습니다. 기존 어댑터를 작동 하는 동안에 최대한 빨리 최신 빌드를 업데이트 하는 것이 좋습니다. 업데이트를 순서 대로 설치 해야: 버전을 건너뛸 수 없습니다 (에서 버전 목록을 보려면 [리소스 공급자 필수 구성 요소 배포](.\azure-stack-sql-resource-provider-deploy.md#prerequisites)).

사용 하는 리소스 공급자의 업데이트는 *UpdateSQLProvider.ps1* 스크립트입니다. 프로세스에 설명 된 대로 리소스 공급자를 설치 하는 데 사용 하는 프로세스와 비슷합니다는 [리소스 공급자를 배포](.\azure-stack-sql-resource-provider-deploy.md) 문서. 이 스크립트는 리소스 공급자의 다운로드에 포함 합니다.

*UpdateSQLProvider.ps1* 스크립트 최신 리소스 공급자 코드를 사용 하 여 새 VM를 만들고는 이전 VM 설정을 새 VM을 마이그레이션합니다. 마이그레이션되는 설정에는 데이터베이스 및 호스팅 서버 정보를 포함 하 고 필요한 DNS 레코드입니다.

DeploySqlProvider.ps1 스크립트에 대 한 설명 하는 동일한 인수를 사용을 해야 하는 스크립트입니다. 여기에 인증서도 제공 합니다. 

마켓플레이스 관리에서 최신 Windows Server 2016 Core 이미지를 다운로드 하는 것이 좋습니다. 업데이트를 설치 해야 할 경우에 단일을 배치할 수 있습니다. 로컬 종속성 경로에 MSU 패키지입니다. 둘 이상 있습니다. MSU 파일을 찾을 수, 스크립트가 실패 합니다.

다음은의 예는 *UpdateSQLProvider.ps1* PowerShell 프롬프트에서 실행할 수 있는 스크립트입니다. 계정 정보 및 필요에 따라 암호를 변경 해야 합니다. 

> [!NOTE]
> 업데이트 프로세스는 통합 된 시스템에만 적용 됩니다.

```powershell
# Install the AzureRM.Bootstrapper module and set the profile.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateSQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

## <a name="updatesqlproviderps1-parameters"></a>UpdateSQLProvider.ps1 매개 변수
명령줄에서 이러한 매개 변수를 지정할 수 있습니다. 그렇지 않고 또는 모든 매개 변수 유효성 검사에 실패 하는 경우 필수 매개 변수를 제공 하 라는 메시지가 표시 됩니다.

| 매개 변수 이름 | 설명 | 주석 또는 default 값 |
| --- | --- | --- |
| **CloudAdminCredential** | 권한 있는 끝점에 액세스 하는 데 필요한 클라우드 관리자에 대 한 자격 증명입니다. | _필수_ |
| **AzCredential** | Azure 스택 서비스 관리자 계정에 대 한 자격 증명입니다. Azure 스택을 배포 하는 데 사용한 것과 동일한 자격 증명을 사용 합니다. | _필수_ |
| **VMLocalCredential** | VM의 SQL 리소스 공급자의 로컬 관리자 계정에 대 한 자격 증명입니다. | _필수_ |
| **PrivilegedEndpoint** | IP 주소 또는 권한 있는 끝점의 DNS 이름입니다. |  _필수_ |
| **DependencyFilesLocalPath** | 인증서.pfx 파일에이 디렉터리에 배치 되어야 합니다. | _선택적_ (_필수_ 다중 노드) |
| **DefaultSSLCertificatePassword** | .Pfx 인증서에 대 한 암호입니다. | _필수_ |
| **MaxRetryCount** | 오류가 발생 하는 경우 각 작업을 다시 시도 하는 횟수입니다.| 2 |
| **RetryDuration** |초 후에 다시 시도 사이의 시간 제한 간격입니다. | 120 |
| **제거** | 리소스 공급자와 관련 된 모든 리소스 (아래 참고 참조)를 제거 합니다. | 아니요 |
| **DebugMode** | 실패 한 경우 자동 정리를 방지합니다. | 아니요 |


## <a name="next-steps"></a>다음 단계

[SQL 리소스 공급자를 유지 관리](azure-stack-sql-resource-provider-maintain.md)
