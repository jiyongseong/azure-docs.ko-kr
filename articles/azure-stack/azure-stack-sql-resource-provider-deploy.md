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
ms.date: 05/24/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 8643e75a24ff7840b71dfaceae9934cdda566d30
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604423"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>SQL 데이터베이스를 사용 하 여 Microsoft Azure 스택
Azure 스택 SQL Server 리소스 공급자를 사용 하 여 SQL 데이터베이스를 Azure 스택의 서비스로 노출 합니다. SQL 리소스 공급자는 Windows Server 코어 가상 컴퓨터는 VM에서 SQL 리소스 공급자 서비스 실행 됩니다.

## <a name="prerequisites"></a>필수 조건
Azure 스택 SQL 리소스 공급자를 배포 하기 전에 준비 되어야 하는 몇 가지 필수 구성 요소가 있습니다. VM 권한 있는 끝점에 액세스할 수 있는 컴퓨터에서 다음 단계를 수행 합니다.

- 하면 아직 수행 하지 않은, 경우 [레지스터 Azure 스택](.\azure-stack-registration.md) Azure와 함께 Azure 마켓플레이스 항목을 다운로드할 수 있도록 합니다.
- 다운로드 하 여 Azure 스택 marketplace에 필요한 Windows Server 코어 VM 추가 **Windows Server 2016 Server core** 이미지입니다. 업데이트를 설치 해야 할 경우에 단일을 배치할 수 있습니다. 로컬 종속성 경로에 MSU 패키지입니다. 둘 이상 있습니다. MSU 파일이, SQL 리소스 공급자 설치에 실패 합니다.
- 이진 SQL 리소스 공급자를 다운로드 하 고 임시 디렉터리에 콘텐츠를 추출 자동 압축 풀기를 실행 하십시오. 리소스 공급자에는 빌드는 최소 해당 Azure 스택 합니다. 실행 중인 Azure 스택 버전에 대 한 올바른 이진을 다운로드 해야 합니다.

    |Azure 스택 버전|SQL RP 버전|
    |-----|-----|
    |버전 1804 (1.0.180513.1)|[SQL RP 1.1.24.0 버전](https://aka.ms/azurestacksqlrp1804)
    |버전 1802 (1.0.180302.1)|[SQL RP 1.1.18.0 버전](https://aka.ms/azurestacksqlrp1802)|
    |버전 1712 (1.0.180102.3, 1.0.180103.2 또는 1.0.180106.1 (통합된 시스템))|[SQL RP 1.1.14.0 버전](https://aka.ms/azurestacksqlrp1712)|
    |     |     |
- 통합된 시스템 설치에 대해 제공 해야의 선택적 PaaS 인증서 섹션에 설명 된 대로 SQL PaaS PKI 인증서 [Azure 스택 배포에 대 한 PKI 요구 사항](.\azure-stack-pki-certs.md#optional-paas-certificates)를 위치에.pfx 파일을 배치 하 여 에 지정 된는 **DependencyFilesLocalPath** 매개 변수입니다.

## <a name="deploy-the-sql-resource-provider"></a>SQL 리소스 공급자를 배포 합니다.
성공적으로 준비 하는 모든 필수 구성 요소를 충족 하 여 SQL 리소스 공급자를 설치 하려면 후 실행할 수 있습니다는 **DeploySqlProvider.ps1** SQL 리소스 공급자를 배포 하는 스크립트입니다. Azure 스택 버전에 해당 하는 다운로드 이진 SQL 리소스 공급자의 일부로 DeploySqlProvider.ps1 스크립트 추출 됩니다. 

> [!IMPORTANT]
> 스크립트를 실행 하는 시스템에 설치 된.NET 런타임의 최신 버전으로 Windows 10 또는 Windows Server 2016 시스템 이어야 합니다.


SQL 리소스 공급자를 배포 하려면 관리자 권한으로 새 (관리) PowerShell 콘솔을 열고 SQL 리소스 공급자 이진 파일의 압축을 푼 디렉터리로 변경 합니다.

> [!NOTE]
> 새 PowerShell 콘솔 창을 사용 하 여 시스템에 이미 로드 되어 있는 잘못 된 PowerShell 모듈에서 발생할 수 있는 문제를 방지 합니다.

다음 단계를 수행 하는 DeploySqlProvider.ps1 스크립트를 실행 합니다.
- Azure 스택의 저장소 계정에는 인증서와 다른 아티팩트를 업로드합니다.
- 갤러리를 통해 SQL 데이터베이스를 배포할 수 있도록 갤러리 패키지를 게시 합니다.
- 호스팅 서버를 배포에 대 한 갤러리 패키지를 게시 합니다.
- 1 단계에서 만든 하 고 다음 리소스 공급자를 설치 하는 Windows Server 2016 이미지를 사용 하 여 VM을 배포 합니다.
- VM 리소스 공급자에 매핑하는 로컬 DNS 레코드를 등록 합니다.
- 리소스 공급자는 로컬 Azure 리소스 관리자와 (사용자 / 관리자)을 등록합니다.
- 필요에 따라 RP 설치 하는 동안 단일 Windows 업데이트를 설치합니다.

SQL 리소스 공급자 배포 시작 되 고 system.local.sqladapter 리소스 그룹을 만듭니다. 이 리소스 그룹에 4 개의 필수 배포를 완료 하려면 최대 75 분 정도 걸릴 수 있습니다.

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 parameters
명령줄에서 이러한 매개 변수를 지정할 수 있습니다. 그렇지 않고 또는 모든 매개 변수 유효성 검사에 실패 하는 경우 필수 매개 변수를 제공 하 라는 메시지가 표시 됩니다.

| 매개 변수 이름 | 설명 | 주석 또는 default 값 |
| --- | --- | --- |
| **CloudAdminCredential** | 권한 있는 끝점에 액세스 하는 데 필요한 클라우드 관리자에 대 한 자격 증명입니다. | _필수_ |
| **AzCredential** | Azure 스택 서비스 관리자 계정에 대 한 자격 증명입니다. Azure 스택 배포에 사용한 것과 동일한 자격 증명을 사용 합니다. | _필수_ |
| **VMLocalCredential** | VM의 SQL 리소스 공급자의 로컬 관리자 계정에 대 한 자격 증명입니다. | _필수_ |
| **PrivilegedEndpoint** | IP 주소 또는 권한 있는 끝점의 DNS 이름입니다. |  _필수_ |
| **DependencyFilesLocalPath** | 인증서.pfx 파일에이 디렉터리에 배치 되어야 합니다. | _선택적_ (_필수_ 통합 시스템용) |
| **DefaultSSLCertificatePassword** | .Pfx 인증서에 대 한 암호입니다. | _필수_ |
| **MaxRetryCount** | 오류가 발생 하는 경우 각 작업을 다시 시도 하는 횟수입니다.| 2 |
| **RetryDuration** | 초 후에 다시 시도 사이의 시간 제한 간격입니다. | 120 |
| **제거** | 리소스 공급자와 관련 된 모든 리소스 (아래 참고 참조)를 제거 합니다. | 아니요 |
| **DebugMode** | 실패 한 경우 자동 정리를 방지합니다. | 아니요 |

>[!NOTE]
> Sku 포털에 표시 되도록 최대 한 시간이 걸릴 수 있습니다. SKU 만들어질 때까지 데이터베이스를 만들 수 없습니다.


## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>사용자 지정 스크립트를 사용 하 여 SQL 리소스 공급자를 배포 합니다.
DeploySqlProvider.ps1 스크립트를 실행할 때 필요한 정보를 수동으로 입력을 방지 하려면 기본 계정 정보 및 필요에 따라 암호를 변경 하 여 다음 스크립트 예제를 사용자 지정할 수 있습니다.

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

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local administrator account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
$tempDir\DeploySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert
 ```

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Azure 스택 포털을 사용 하 여 배포 확인
SQL 리소스 공급자가 성공적으로 배포 되는지 확인 하려면이 섹션의 단계를 사용할 수 있습니다.

> [!NOTE]
>  설치 스크립트 실행을 완료 한 후 관리자 블레이드에 대 한 갤러리 항목을 보려면 포털을 새로 고치려면 해야 합니다.

1. 서비스 관리자는 관리 포털에 로그인 합니다.

2. 배포에 성공 했는지 확인 합니다. 로 이동 **리소스 그룹**합니다. 다음을 선택 된 **시스템.\< 위치\>.sqladapter** 리소스 그룹입니다. 모든 4 개의 배포에 성공 했는지 확인 합니다.

      ![SQL 리소스 공급자의 배포를 확인 합니다.](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="next-steps"></a>다음 단계

[호스팅 서버 추가](azure-stack-sql-resource-provider-hosting-servers.md)
