---
title: MySQL 데이터베이스를 사용 하 여 Azure 스택에 PaaS로 | Microsoft Docs
description: MySQL 리소스 공급자를 배포 Azure 스택에 서비스로 MySQL 데이터베이스를 제공 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: ef4d34870cce7d2a149b2592e341956419272c92
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604202"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>MySQL 데이터베이스를 사용 하 여 Microsoft Azure 스택 
Azure 스택 MySQL 리소스 공급자를 배포할 수 있습니다. 리소스 공급자를 배포한 후에 MySQL server 및 Azure 리소스 관리자 배포 템플릿을 통해 데이터베이스를 만들 수 있습니다. 또한 서비스로 MySQL 데이터베이스를 제공할 수 있습니다.  

웹 사이트에 공통적으로 적용 되는 MySQL 데이터베이스는 많은 웹 사이트 플랫폼 지원. 예를 들어 리소스 공급자를 배포 하면 만들면 WordPress 웹 사이트는 웹 응용 프로그램 플랫폼에서 서비스 (PaaS) 추가 기능으로 Azure 스택에 대 한 됩니다. 
 
인터넷에 연결 되지 않은 시스템에서 MySQL 공급자를 배포 하려면 파일을 복사 [mysql 커넥터-net 6.10.5.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.10.5.msi) 로컬 공유에 있습니다. 에 대 한 메시지가 표시 되는 경우 해당 공유 이름을 제공 합니다. Azure 및 Azure 스택 PowerShell 모듈을 설치 해야 합니다. 

## <a name="mysql-server-resource-provider-adapter-architecture"></a>MySQL Server 리소스 공급자 어댑터 아키텍처 
리소스 공급자는 세 가지 구성 요소가 구성 됩니다. 
- **MySQL 리소스 공급자 어댑터 VM**, 공급자 서비스를 실행 하는 Windows 가상 컴퓨터는입니다. 
- **리소스 공급자 자체**준비 요청을 처리 하는, 및 데이터베이스 리소스를 노출 합니다. 
- **MySQL 서버를 호스팅하는 서버**, 호스팅 서버 라고 하는 데이터베이스에 대 한 용량을 제공 하는 합니다. MySQL 인스턴스를 직접 만들 하거나 외부 SQL 인스턴스에 대 한 액세스를 제공 해야 합니다. 방문는 [Azure 스택 퀵 스타트 갤러리](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) 수 있는 예제 서식 파일에 대 한: 
    - MySQL 서버를 만듭니다. 
    - 다운로드 하 고 Azure Marketplace에서 MySQL 서버를 배포 합니다. 


> [!NOTE] 
> Azure 스택에 설치 되어 있는 서버를 호스팅 통합형된 시스템 테 넌 트 구독에서 생성 되어야 합니다. 기본 공급자 구독에서 만들 수 없습니다. 적절 한 로그인을 사용 하 여 PowerShell 세션을 또는 테 넌 트 포털에서 만들 수 있어야 합니다. 모든 호스팅 서버가 유료 대의 vm 및 적절 한 라이선스가 있어야 합니다. 서비스 관리자의 테 넌 트 구독 소유자가 될 수 있습니다. 

### <a name="required-privileges"></a>필요한 권한 
시스템 계정에는 다음 권한이 있어야 합니다. 
- 데이터베이스: 만들기, 삭제 
- 로그인: 만들기, 설정, 삭제, 권한을 부여, 취소 
 
## <a name="deploy-the-resource-provider"></a>리소스 공급자를 배포 
1. 아직 수행 경우 개발 키트를 등록 하 고 Marketplace 관리를 통해 다운로드할 수 있는 Windows Server 2016 데이터 센터 Core 이미지를 다운로드 합니다. Windows Server 2016 Core 이미지를 사용 해야 합니다. 만드는 스크립트를 사용할 수도 있습니다는 [Windows Server 2016 이미지](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image)합니다. (해야 코어 옵션을 선택 합니다.) 

2. VM 권한 있는 끝점에 액세스할 수 있는 호스트에 로그인 합니다.
    - Azure SDK 설치에서 실제 호스트에 로그인 합니다.  
    - 통합된 시스템에 호스트 권한 있는 끝점에 액세스할 수 있는 시스템 이어야 합니다. 
    >[!NOTE] 
    > 스크립트가 실행 되 고 시스템 *해야* 최신 버전의.NET 런타임 설치 된 Windows 10 또는 Windows Server 2016 시스템 이어야 합니다. 그렇지 않으면 설치에 실패 합니다. Azure 스택 ASDK 호스트는이 조건을 충족합니다. 
3. 이진 MySQL 리소스 공급자를 다운로드 합니다. 그런 다음 실행을 임시 디렉터리에 콘텐츠를 추출 자동 압축 풀기. 
    >[!NOTE]  
    > 리소스 공급자에는 빌드는 최소 해당 Azure 스택 합니다. 실행 하는 Azure 스택 버전에 대 한 올바른 이진을 다운로드 해야 합니다.

    | Azure 스택 버전 | MySQL RP 버전| 
    | --- | --- | 
    | 버전 1804 (1.0.180513.1)|[MySQL RP 1.1.24.0 버전](https://aka.ms/azurestackmysqlrp1804) | 
    | 버전 1802 (1.0.180302.1) | [MySQL RP 1.1.18.0 버전](https://aka.ms/azurestackmysqlrp1802) | 
    | 버전 1712 (1.0.180102.3 또는 1.0.180106.1 (통합된 시스템)) | [MySQL RP 1.1.14.0 버전](https://aka.ms/azurestackmysqlrp1712) | 

4.  ASDK에 대 한 자체 서명 된 인증서는이 프로세스의 일부로 생성 됩니다. 통합된 시스템에 대 한 적절 한 인증서를 제공 해야 합니다. 고유한 인증서를 제공 해야 하는 경우에.pfx 파일을 배치는 **DependencyFilesLocalPath** 다음 조건을 만족 하 합니다. 
    - 에 대 한 와일드 카드 인증서 \*.dbadapter.\< 지역\>.\< 외부 fqdn\> 또는 일반 mysqladapter.dbadapter 이름의 단일 사이트 인증서.\< 지역\>.\< 외부 fqdn\>합니다. 
    - 이 인증서는 신뢰할 수 있어야 합니다. 즉, 중간 인증서를 요구 하지 않고 신뢰 체인 존재 해야 합니다. 
    - 단일 인증서 파일이는 DependencyFilesLocalPath에 있습니다. 
    - 파일 이름은 특수 문자 또는 공백을 사용할 수 없습니다. 

5. 열기는 **새** 관리자 권한 (관리자) PowerShell 콘솔. 다음 파일의 압축을 푼 디렉터리로 변경 합니다. 시스템에 이미 로드 되어 있는 잘못 된 PowerShell 모듈에서 발생할 수 있는 문제를 방지 하기 위해 새 창을 사용 합니다. 

6. 실행 된 **DeployMySqlProvider.ps1** 스크립트입니다. 스크립트는 다음이 단계를 수행합니다. 
    - (이 제공 될 수 있습니다 오프 라인)는 MySQL 커넥터 이진을 다운로드 합니다. 
    - Azure 스택의 저장소 계정에는 인증서와 다른 아티팩트를 업로드합니다. 
    - 갤러리를 통해 SQL 데이터베이스를 배포할 수 있도록 갤러리 패키지를 게시 합니다. 
    - 호스팅 서버를 배포에 대 한 갤러리 패키지를 게시 합니다. 
    - VM 녀석 Windows Server 2016 Azure 스택 마켓플레이스 이미지를 배포 하 고 리소스 공급자를 설치 합니다. 
    - VM 리소스 공급자에 매핑하는 로컬 DNS 레코드를 등록 합니다. 
    - 로컬 Azure 리소스 관리자 (테 넌 트 및 관리자) 리소스 공급자를 등록합니다. 

    명령줄에 필요한 매개 변수 지정 일 수 있습니다, 매개 변수 없이 스크립트를 실행 하 고 메시지가 표시 되 면 해당를 입력 합니다. 

    다음은 예제 PowerShell 프롬프트에서 실행할 수 있습니다. 계정 정보 및 필요에 따라 암호를 변경 해야 합니다. 

    ```powershell 
    # Install the AzureRM.Bootstrapper module and set the profile. 
    Install-Module -Name AzureRm.BootStrapper -Force 
    Use-AzureRmProfile -Profile 2017-03-09-profile 

    # Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time. 
    $domain = "AzureStack"  

    # For integrated systems, use the IP address of one of the ERCS virtual machines 
    $privilegedEndpoint = "AzS-ERCS01" 

    # Point to the directory where the resource provider installation files were extracted. 
    $tempDir = 'C:\TEMP\MYSQLRP' 

    # The service admin account (can be Azure Active Directory or Active Directory Federation Services). 
    $serviceAdmin = "admin@mydomain.onmicrosoft.com" 
    $AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
    $AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass) 

    # Set the credentials for the new resource provider VM local administrator account 
    $vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
    $vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass) 

    # And the cloudadmin credential required for privileged endpoint access. 
    $CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
    $CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass) 

    # Change the following as appropriate. 
    $PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 

    # Run the installation script from the folder where you extracted the installation files. 
    # Find the ERCS01 IP address first, and make sure the certificate 
    # file is in the specified directory. 
    $tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds ` 
    VMLocalCredential $vmLocalAdminCreds ` 
    CloudAdminCredential $cloudAdminCreds ` 
    PrivilegedEndpoint $privilegedEndpoint ` 
    DefaultSSLCertificatePassword $PfxPass ` 
    DependencyFilesLocalPath $tempDir\cert ` 
    AcceptLicense 
    ``` 

### <a name="deploymysqlproviderps1-parameters"></a>DeployMySqlProvider.ps1 parameters 
명령줄에서 이러한 매개 변수를 지정할 수 있습니다. 그렇지 않고 또는 모든 매개 변수 유효성 검사에 실패 하는 경우 필수 매개 변수를 제공 하 라는 메시지가 표시 됩니다. 

| 매개 변수 이름 | 설명 | 주석 또는 default 값 | 
| --- | --- | --- | 
| **CloudAdminCredential** | 권한 있는 끝점에 액세스 하는 데 필요한 클라우드 관리자에 대 한 자격 증명입니다. | _필수_ | 
| **AzCredential** | Azure 스택 서비스 관리자 계정에 대 한 자격 증명입니다. Azure 스택을 배포 하는 데 사용한 것과 동일한 자격 증명을 사용 합니다. | _필수_ | 
| **VMLocalCredential** | VM의 MySQL 리소스 공급자의 로컬 관리자 계정에 대 한 자격 증명입니다. | _필수_ | 
| **PrivilegedEndpoint** | IP 주소 또는 권한 있는 끝점의 DNS 이름입니다. |  _필수_ | 
| **DependencyFilesLocalPath** | 경로를 포함 하는 로컬 공유 [mysql 커넥터-net 6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi)합니다. 이러한 경로 중 하나를 제공 하는 경우 인증서 파일을이 디렉터리에 배치 되어야 합니다. | _선택적_ (_필수_ 다중 노드) | 
| **DefaultSSLCertificatePassword** | .Pfx 인증서에 대 한 암호입니다. | _필수_ | 
| **MaxRetryCount** | 오류가 발생 하는 경우 각 작업을 다시 시도 하는 횟수입니다.| 2 | 
| **RetryDuration** | 초 후에 다시 시도 사이의 시간 제한 간격입니다. | 120 | 
| **제거** | 리소스 공급자와 관련 된 모든 리소스 (아래 참고 참조)를 제거 합니다. | 아니요 | 
| **DebugMode** | 실패 한 경우 자동 정리를 방지합니다. | 아니요 | 
| **AcceptLicense** | GPL 라이선스를 수락 하 라는 메시지가 건너뜁니다.  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 

> [!NOTE] 
> Sku 포털에 표시 되도록 최대 한 시간이 걸릴 수 있습니다. SKU 만들어질 때까지 데이터베이스를 만들 수 없습니다. 

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Azure 스택 포털을 사용 하 여 배포를 확인 합니다. 
> [!NOTE] 
>  설치 스크립트 실행을 완료 한 후 관리 블레이드를 확인 하기 위해 포털을 새로 고쳐야 할 수 있습니다. 

1. 서비스 관리자는 관리 포털에 로그인 합니다. 
2. 배포에 성공 했는지 확인 합니다. 로 이동 **리소스 그룹**를 선택한 후는 **시스템.\< 위치\>.mysqladapter** 리소스 그룹입니다. 모든 4 개의 배포에 성공 했는지 확인 합니다.

      ![MySQL RP의 배포를 확인 합니다.](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png) 

## <a name="provide-capacity-by-connecting-to-a-mysql-hosting-server"></a>MySQL 호스팅 서버에 연결 하 여 용량을 제공 합니다. 
1. 서비스 관리자로 Azure 스택 포털에 로그인 
2. 선택 **관리 리소스** > **MySQL 호스팅 서버** > **+ 추가**합니다. 에 **MySQL 호스팅 서버** 블레이드에서 MySQL server 리소스 공급자의 백 엔드 응용 프로그램으로 처리 하는 실제 인스턴스에서에 MySQL Server 리소스 공급자를 연결할 수 있습니다. 

![호스팅 서버](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png) 

3. MySQL Server 인스턴스의 연결 세부 정보를 제공 합니다. 정규화 된 도메인 이름 (FQDN) 또는 올바른 IPv4 주소 및 짧은 VM 이름을 제공 해야 합니다. 이 설치를 더 이상 기본 MySQL 인스턴스를 제공 합니다. 제공 되는 크기에는 리소스 공급자 데이터베이스 용량을 관리할 수 있습니다. 데이터베이스 서버의 실제 용량 가까이 있어야 합니다. 

    > [!NOTE] 
    > 테 넌 트 및 관리 Azure 리소스 관리자에서 MySQL 인스턴스를 액세스할 수 있는, 경우에 리소스 공급자에 의해 제어 배치할 수 있습니다. MySQL 인스턴스가 *해야* 리소스 공급자에만 할당할 수 있습니다. 

4. 서버를 추가 하면 서비스 제공의 차이 허용 하는 새로운 또는 기존 SKU에 할당 해야 합니다. 예를 들어 제공 하는 엔터프라이즈 인스턴스를 가질 수 있습니다. 
    - 데이터베이스 용량
    - 자동 백업
    - 개별 부서에 대 한 고성능 서버 예약 

    > [!IMPORTANT] 
    > Always On의 인스턴스와 SKU가 같은 독립 실행형 서버를 혼합할 수 없습니다. 오류가 첫 번째 호스팅 서버 결과 추가한 후 유형을 혼합 하려고 합니다. 

    테 넌 트 데이터베이스를 적절 하 게 배치할 수 있도록 SKU 이름 속성을 반영 해야 합니다. SKU의 모든 호스팅 서버와 동일한 기능 있어야 합니다. 

    ![MySQL SKU 만들기](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png) 

## <a name="test-your-deployment-by-creating-your-first-mysql-database"></a>첫 번째 MySQL 데이터베이스를 만들어 배포 테스트 
1. 서비스 관리자로 Azure 스택 포털에 로그인 
2. 선택 **새 +** > **데이터 + 저장소** > **MySQL 데이터베이스**합니다. 
3. 데이터베이스 정보를 제공 합니다. 

    ![테스트 MySQL 데이터베이스 만들기](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)
 
4. SKU를 선택 합니다. 

    ![SKU를 선택 합니다.](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png) 

5. 로그인 설정을 만듭니다. 기존 로그인 설정을 다시 사용 하거나 새로 만들 수 있습니다. 이 설정은 사용자 이름 및 데이터베이스에 대 한 암호를 포함합니다. 

    ![새 데이터베이스 로그인 생성](./media/azure-stack-mysql-rp-deploy/create-new-login.png) 

    연결 문자열에는 실제 데이터베이스 서버 이름을 포함 합니다. 포털에서 복사 합니다. 

    ![MySQL 데이터베이스에 대 한 연결 문자열을 가져옵니다.](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png) 

    > [!NOTE] 
    > 사용자 이름의 길이 MySQL 5.7 32 자를 초과할 수 없습니다. 이전 버전에서 16 자를 초과할 수는 없습니다. 

## <a name="add-capacity"></a>용량 추가 
Azure 스택 포털에서 MySQL 서버를 추가 하 여 용량을 추가 합니다. 추가 서버를 기존 또는 새 SKU로 추가할 수 있습니다. 서버 특성은 동일 해야 합니다. 
 
## <a name="make-mysql-databases-available-to-tenants"></a>테 넌 트에 MySQL 데이터베이스를 사용할 수 있도록 
계획 및 제안을 테 넌 트에 대 한 MySQL 데이터베이스를 사용할 수 있게 만듭니다. 예를 들어 Microsoft.MySqlAdapter 서비스를 추가, 할당량, 추가 등에입니다. 

![계획 및 데이터베이스를 포함 하도록 제안 만들기](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png) 

## <a name="update-the-administrative-password"></a>관리자 암호를 업데이트 합니다. 
첫 번째 MySQL 서버 인스턴스에서 변경 하 여 암호를 수정할 수 있습니다. 선택 **관리 리소스** > **MySQL 호스팅 서버**합니다. 다음 호스팅 서버를 선택 합니다. 에 **설정** 패널에서 **암호**합니다. 

![관리자 암호를 업데이트 합니다.](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png) 

## <a name="update-the-mysql-resource-provider-adapter-integrated-systems-only"></a>MySQL 리소스 공급자 어댑터 (통합 된 시스템에만 해당) 업데이트
Azure 스택 빌드를 업데이트할 때 새 SQL 리소스 공급자 어댑터를 해제 될 수 있습니다. 기존 어댑터를 작동 하는 동안에 최대한 빨리 최신 빌드를 업데이트 하는 것이 좋습니다.  
 
사용 하는 리소스 공급자의 업데이트는 **UpdateMySQLProvider.ps1** 스크립트입니다. 프로세스에 설명 된 대로 리소스 공급자를 설치 하는 데 사용 하는 프로세스와 비슷합니다는 [리소스 공급자를 배포](#deploy-the-resource-provider) 이 문서의 섹션. 이 스크립트는 리소스 공급자의 다운로드에 포함 합니다. 

**UpdateMySQLProvider.ps1** 스크립트 최신 리소스 공급자 코드를 사용 하 여 새 VM를 만들고는 이전 VM 설정을 새 VM을 마이그레이션합니다. 마이그레이션되는 설정에는 데이터베이스 및 호스팅 서버 정보를 포함 하 고 필요한 DNS 레코드입니다. 

DeployMySqlProvider.ps1 스크립트에 대 한 설명 하는 동일한 인수를 사용을 해야 하는 스크립트입니다. 여기에 인증서도 제공 합니다.  

다음은의 예는 *UpdateMySQLProvider.ps1* PowerShell 프롬프트에서 실행할 수 있는 스크립트입니다. 계정 정보 및 필요에 따라 암호를 변경 해야 합니다.  

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
$tempDir = 'C:\TEMP\MYSQLRP' 

# The service admin account (can be Azure Active Directory or Active Directory Federation Services). 
$serviceAdmin = "admin@mydomain.onmicrosoft.com" 
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass) 
 
# Set credentials for the new resource provider VM. 
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass) 
 
# And the cloudadmin credential required for privileged endpoint access. 
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass) 

# Change the following as appropriate. 
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
 
# Change directory to the folder where you extracted the installation files. 
# Then adjust the endpoints. 
$tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds ` 
-VMLocalCredential $vmLocalAdminCreds ` 
-CloudAdminCredential $cloudAdminCreds ` 
-PrivilegedEndpoint $privilegedEndpoint ` 
-DefaultSSLCertificatePassword $PfxPass ` 
-DependencyFilesLocalPath $tempDir\cert ` 
-AcceptLicense 
``` 
 
### <a name="updatemysqlproviderps1-parameters"></a>UpdateMySQLProvider.ps1 매개 변수 
명령줄에서 이러한 매개 변수를 지정할 수 있습니다. 이렇게 하지 않으면 또는 모든 매개 변수 유효성 검사에 실패 하는 경우 필수 매개 변수를 제공 하 라는 메시지가 표시 됩니다. 

| 매개 변수 이름 | 설명 | 주석 또는 default 값 | 
| --- | --- | --- | 
| **CloudAdminCredential** | 권한 있는 끝점에 액세스 하는 데 필요한 클라우드 관리자에 대 한 자격 증명입니다. | _필수_ | 
| **AzCredential** | Azure 스택 서비스 관리자 계정에 대 한 자격 증명입니다. Azure 스택 배포에 사용한 것과 동일한 자격 증명을 사용 합니다. | _필수_ | 
| **VMLocalCredential** |VM의 SQL 리소스 공급자의 로컬 관리자 계정에 대 한 자격 증명입니다. | _필수_ | 
| **PrivilegedEndpoint** | IP 주소 또는 권한 있는 끝점의 DNS 이름입니다. |  _필수_ | 
| **DependencyFilesLocalPath** | 인증서.pfx 파일에이 디렉터리에 배치 되어야 합니다. | _선택적_ (_필수_ 다중 노드) | 
| **DefaultSSLCertificatePassword** | .Pfx 인증서에 대 한 암호입니다. | _필수_ | 
| **MaxRetryCount** | 오류가 발생 하는 경우 각 작업을 다시 시도 하는 횟수입니다.| 2 | 
| **RetryDuration** | 초 후에 다시 시도 사이의 시간 제한 간격입니다. | 120 | 
| **제거** | 리소스 공급자와 관련 된 모든 리소스 (아래 참고 참조)를 제거 합니다. | 아니요 | 
| **DebugMode** | 실패 한 경우 자동 정리를 방지합니다. | 아니요 | 
| **AcceptLicense** | GPL 라이선스를 수락 하 라는 메시지가 건너뜁니다.  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 
 
## <a name="collect-diagnostic-logs"></a>진단 로그를 수집 합니다. 
MySQL 리소스 공급자를 통해 가상 컴퓨터는 잠금 되어 있습니다. PowerShell 관리 JEA (Just Enough) 끝점은 가상 컴퓨터에서 로그를 수집 해야 하는 경우 _DBAdapterDiagnostics_ 목적으로 제공 됩니다. 이 끝점을 통해 사용할 수 있는 두 개의 명령입니다. 

- **Get AzsDBAdapterLog**합니다. RP 진단 로그를 포함 하는 zip 패키지를 준비 하 고 세션 사용자 드라이브에 넣습니다. 명령 매개 변수 없이 호출 될 수 있으며 로그의 마지막 4 시간을 수집 합니다. 

- **제거 AzsDBAdapterLog**합니다. 리소스 공급자 VM에서 기존 로그 패키지를 정리 

사용자 계정 이라는 _dbadapterdiag_ RP 배포 또는 RP 로그를 추출 하기 위한 진단 끝점에 연결 하기 위한 업데이트 중에 만들어집니다. 이 계정은 암호가 배포/업데이트 중에 로컬 관리자 계정에 대해 제공 된 암호와 동일 합니다. 

이러한 명령을 사용 하려면 리소스 공급자 가상 컴퓨터에 원격 PowerShell 세션을 만들고 명령을 호출 해야 합니다. 필요에 따라 FromDate 및 ToDate 매개 변수를 제공할 수 있습니다. 하나 또는 둘 다 지정 하지 않으면의 FromDate을 현재 시간 전 4 시간 되며는 ToDate 현재 시간이 됩니다. 

이 샘플 스크립트 이러한 명령의 사용을 보여 줍니다. 

```powershell 
# Create a new diagnostics endpoint session. 
$databaseRPMachineIP = '<RP VM IP address>' 
$diagnosticsUserName = 'dbadapterdiag' 
$diagnosticsUserPassword = '<Enter Diagnostic password>' 
$diagCreds = New-Object System.Management.Automation.PSCredential ` 
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force)) 
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds 
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

## <a name="maintenance-operations-integrated-systems"></a>유지 관리 작업 (통합된 시스템) 
MySQL 리소스 공급자를 통해 가상 컴퓨터는 잠금 되어 있습니다. PowerShell 관리 JEA (Just Enough) 끝점을 통해 리소스 공급자 가상 컴퓨터의 보안 업데이트를 수행할 수 있습니다 _DBAdapterMaintenance_합니다. 스크립트는 이러한 작업을 용이 하 게 하려면 RP의 설치 패키지와 함께 제공 됩니다. 

### <a name="update-the-virtual-machine-operating-system"></a>가상 컴퓨터 운영 체제를 업데이트 합니다. 
Windows Server VM을 업데이트 하는 방법은 여러 가지가 있습니다. 
- 현재 패치가 적용 된 Windows Server 2016 Core 이미지를 사용 하 여 최신 리소스 공급자 패키지 설치 
- 설치 또는 RP 업데이트 하는 동안 Windows Update 패키지를 설치 합니다. 

### <a name="update-the-virtual-machine-windows-defender-definitions"></a>가상 컴퓨터의 Windows Defender 정의 업데이트 
Defender 정의 업데이트 하려면 다음이 단계를 수행 합니다. 
1. Windows Defender 정의 업데이트에서 다운로드 [Windows Defender 정의](https://www.microsoft.com/en-us/wdsi/definitions)합니다.

    이 페이지에서 다운로드에 "수동으로 다운로드 및 설치 정의"에서 "Windows 10 및 Windows 8.1" 64 비트 파일에 대 한 Windows Defender 바이러스 백신 합니다.
    
    직접 링크: https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64합니다. 

2. MySQL RP 어댑터 가상 컴퓨터의 유지 관리 끝점에 대 한 PowerShell 세션을 만듭니다. 

3. 유지 관리 끝점 세션을 사용 하 여 DB 어댑터 컴퓨터로 정의 업데이트 파일을 복사 합니다. 

4. 세션 호출 PowerShell 유지 관리에는 _업데이트 DBAdapterWindowsDefenderDefinitions_ 명령입니다. 

5. 설치 후 사용 되는 정의 업데이트 파일을 제거 하는 것이 좋습니다. 사용 하 여 유지 관리 세션에서 제거할 수 있습니다는 _제거 ItemOnUserDrive)_ 명령입니다. 

(주소 또는 실제 값으로 가상 컴퓨터의 이름으로 대체) Defender 정의 업데이트 하는 샘플 스크립트는 다음과 같습니다. 

```powershell 
# Set credentials for the RP VM local admin user 
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force 
$vmLocalAdminUser = "<local admin user name>" 
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ` 
    ($vmLocalAdminUser, $vmLocalAdminPass) 

# Public IP Address of the DB adapter machine 
$databaseRPMachine  = "<RP VM IP address>" 
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe" 
 
# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions.  
Invoke-WebRequest -Uri 'https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64' ` 
    -Outfile $localPathToDefenderUpdate  

# Create session to the maintenance endpoint 
$session = New-PSSession -ComputerName $databaseRPMachine ` 
    -Credential $vmLocalAdminCreds -ConfigurationName DBAdapterMaintenance 

# Copy defender update file to the db adapter machine 
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate ` 
     -Destination "User:\" 

# Install the update file 
Invoke-Command -Session $session -ScriptBlock ` 
    {Update-AzSDBAdapterWindowsDefenderDefinition -DefinitionsUpdatePackageFile "User:\"} 

# Cleanup the definitions package file and session 
Invoke-Command -Session $session -ScriptBlock ` 
    {Remove-AzSItemOnUserDrive -ItemPath "User:\"} 
$session | Remove-PSSession  
``` 
### <a name="secrets-rotation"></a>비밀 회전  
*이러한 지침은 Azure 스택 통합 시스템 버전 1804 및 나중에만 적용 됩니다. Pre 1804 Azure 스택 버전에 대 한 비밀 회전을 하지 마십시오.* 
 
SQL 및 MySQL 리소스 공급자를 사용 하 여 Azure 스택이 있는 시스템에 통합 하는 경우 다음과 같은 인프라 (배포) 암호를 회전할 수 있습니다. 
- 외부 SSL 인증서 [배포 중에 제공 된](azure-stack-pki-certs.md)합니다. 
- 리소스 공급자 VM 로컬 관리자 계정 암호 배포 중에 제공 합니다. 
- 리소스 공급자 (dbadapterdiag) 진단 사용자 암호입니다. 
#### <a name="powershell-examples-for-rotating-secrets"></a>암호를 회전 하기 위한 PowerShell 예제 
 
**동시에 모든 암호 변경** 
```powershell 
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd `  
    -VMLocalCredential $localCreds 
``` 
**만 진단 사용자 암호 변경** 
```powershell 
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword  $passwd  
``` 

**VM 로컬 관리자 계정 암호를 변경 합니다.** 
```powershell 
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds 
``` 
**SSL 인증서 변경** 
```powershell 
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd  
``` 

### <a name="secretrotationmysqlproviderps1-parameters"></a>SecretRotationMySQLProvider.ps1 매개 변수 
|매개 변수|설명| 
|-----|-----| 
|AzCredential|Azure 스택 서비스 관리자 계정 자격 증명입니다.| 
|CloudAdminCredential|Azure 스택 클라우드 관리 도메인 계정 자격 증명입니다.| 
|PrivilegedEndpoint|Get AzureStackStampInformation 액세스 하는 권한 있는 끝점입니다.| 
|DiagnosticsUserPassword|진단 사용자 암호입니다.| 
|VMLocalCredential|MySQLAdapter VM의 로컬 관리자 계정입니다.| 
|DefaultSSLCertificatePassword|기본 SSL 인증서 (* pfx) 암호입니다.| 
|DependencyFilesLocalPath|종속성 파일 로컬 경로입니다.| 
|     |     | 

### <a name="known-issues"></a>알려진 문제 
문제: 비밀 회전에 대 한 로그가 수집 되지 않습니다 자동으로 실행 될 때 스크립트가 실패할 경우. 
 
해결 방법: C:\Logs 아래 AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log를 포함 하 여 모든 리소스 공급자 로그를 수집 하는 Get AzsDBAdapterLogs cmdlet을 사용 합니다. 

## <a name="remove-the-mysql-resource-provider-adapter"></a>MySQL 리소스 공급자 어댑터 제거 
리소스 공급자를 제거 하려면 먼저 모든 종속성을 제거에 필수적입니다. 
1. 이 버전의 리소스 공급자에 대 한 다운로드 하는 원래 배포 패키지가 있는지 확인 합니다. 

2. 리소스 공급자에서 모든 테 넌 트 데이터베이스를 삭제 해야 합니다. (삭제 테 넌 트 데이터베이스 하지 않는 데이터는 삭제 됩니다.) 자체 테 넌 트에서이 작업을 수행 되어야 합니다. 

3. 네임 스페이스에서 테 넌 트가 등록을 취소 해야 합니다. 

4. 관리자는 MySQL 어댑터에서 호스팅 서버를 삭제 해야 합니다. 

5. 관리자는 MySQL 어댑터 참조 하는 모든 계획을 삭제 해야 합니다. 

6. 관리자는 MySQL 어댑터와 관련 된 할당량을 삭제 해야 합니다. 

7. 다음 매개 변수를 사용 하 여 배포 스크립트를 다시 실행 하십시오. 
    - -매개 변수를 제거 합니다. 
    - Azure 리소스 관리자 끝점 
    - DirectoryTenantID 
    - 서비스 관리자 계정에 대 한 자격 증명 

### <a name="next-steps"></a>다음 단계
[PaaS로 응용 프로그램 서비스를 제공 합니다.](azure-stack-app-service-overview.md)
