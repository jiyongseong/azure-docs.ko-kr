---
title: "Azure 스택에서 Windows Azure Pack 가상 컴퓨터를 관리 합니다. | Microsoft Docs"
description: "Azure 스택에서 사용자 포털에서 Windows Azure 팩 WAP () Vm을 관리 하는 방법에 알아봅니다."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 213c2792-d404-4b44-8340-235adf3f8f0b
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: a7e4896c84938b392a86f4d9609c4932324c785d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2018
---
# <a name="manage-windows-azure-pack-virtual-machines-from-azure-stack"></a>Azure 스택에서 Windows Azure Pack 가상 컴퓨터를 관리 합니다.

*적용 대상: Azure 스택 개발 키트*

Azure 스택 개발 키트에서 Windows Azure Pack에서 실행 중인 가상 컴퓨터 테 넌 트 Azure 스택 사용자 포털에서 액세스를 활성화할 수 있습니다. 사용자가 기존 IaaS 가상 컴퓨터와 가상 네트워크를 관리할 Azure 스택 포털을 사용할 수 있습니다. 이러한 리소스는 통해 사용할 수 있는 Windows Azure Pack에서 기본 SPF Service Provider Foundation () 및 Virtual Machine Manager (VMM) 구성 요소입니다. 특히 사용자가 다음과 같은 작업을 수행할 수 있습니다.

* 리소스 찾아보기
* 구성 값을 검사
* 가상 컴퓨터를 시작 또는 중지
* 가상 컴퓨터를 통해 프로토콜 RDP (원격 데스크톱)에 연결
* 검사점 만들기 및 관리
* 가상 컴퓨터와 가상 네트워크 삭제

이 기능은 Azure 스택 (미리 보기)에 대 한 Windows Azure 팩 커넥터에서 제공 됩니다. 이 문서에 단일 노드 Azure 스택 환경에 대 한 커넥터를 구성 하는 방법을 보여 줍니다.

연결선의이 미리 보기 릴리스에 대 한 다음 고려해 야 합니다.
* 프로덕션 배포 및 테스트 환경 (모두 Azure 스택 및 Windows Azure 팩)에 Windows Azure 팩 커넥터를 사용 합니다.
* Windows Azure 팩 UR (업데이트 롤업) 9.1 이상 및 System Center SPF 및 VMM UR 9 이상이 있어야 합니다.
* VMM 및 SPF 구성 요소는 System Center 2012 R2 또는 System Center 2016 될 수 있습니다.
* Windows Azure Pack 및 Azure 스택 모두에서 구성 단계를 수행 해야 합니다.
* 이 지침은 비-CPS 클라우드 플랫폼 시스템 환경에 적용 합니다.
* 알려진된 문제를 검토 하려면 참조 [Microsoft Azure 스택 문제 해결](azure-stack-troubleshooting.md)합니다.


## <a name="architecture"></a>건축
다음 다이어그램에서는 Windows Azure 팩 커넥터의 주요 구성 요소를 보여 줍니다.

![Windows Azure 팩 커넥터 구성 요소](media/azure-stack-manage-wap/image1.png)

다음 세부 정보를 확인 합니다.
* Azure 스택 사용자 포털 (Azure 스택 및 Windows Azure Pack) 두 클라우드 모두에서 리소스 정보에 액세스합니다.
* 사용자에 두 환경 모두에서 유효한 계정이 있어야 합니다.
* Azure 스택 사용자 포털을 Windows Azure Pack에서 실행 중인 구성 요소에 대 한 네트워크 액세스가 있어야 합니다.
* 에 **WAP** 섹션 다이어그램의 새로운 Windows Azure 팩 커넥터 모듈 (WAP 확장 및 커넥터)와 기존 Windows Azure 팩 테 넌 트 API SPF 및 VMM 구성 요소와 함께 볼 수 있습니다.


## <a name="identity-management"></a>ID 관리
Windows Azure 팩 테 넌 트 API는 Azure 스택 보안 토큰 서비스 (STS)를 신뢰 해야 합니다.

사용자는 Windows Azure 팩 리소스를 대상으로 하는 스택 Azure 포털을 통해 동작을 수행할 때 포털은 Windows Azure 팩 테 넌 트 API를 사용 합니다. 따라서 제공 된 사용자 인증 토큰은 신뢰할 수 있는 STS에서 가져와야 합니다. 다음 다이어그램을 참조 하세요.

![Windows Azure 팩 커넥터 인증의 다이어그램](media/azure-stack-manage-wap/image2.png)

개발 키트 환경에서는 Windows Azure Pack 및 Azure 스택 독립 id 공급자를 갖고 있습니다. 두 환경 모두 Azure 스택 포털에서 액세스 하는 사용자는 동일한 사용자 계정 이름 (UPN) 이름 두 id 공급자에 있어야 합니다. 예를 들어 계정  *azurestackadmin@azurestack.local*  Windows Azure Pack 용 STS에에서도 있어야 합니다. 아웃 바운드 트러스트 관계를 지원 하도록 AD FS를 설정 하지는, 여기서 Azure 스택 인스턴스의 AD FS Windows Azure 팩 구성 요소 (테 넌 트 API)에서 트러스트를 설정 합니다.

## <a name="prerequisites"></a>필수 조건

### <a name="download-the-windows-azure-pack-connector"></a>Windows Azure 팩 커넥터를 다운로드 합니다.
에 [Microsoft 다운로드 센터](https://aka.ms/wapconnectorazurestackdlc).exe 파일을 다운로드 하 고 로컬 컴퓨터에 압축을 풉니다. 이상에서는 Windows Azure Pack 환경에 액세스할 수 있는 컴퓨터에 콘텐츠를 복사 합니다.

### <a name="deployment-option-requirement"></a>배포 옵션 요구 사항
Windows Azure 팩와 통합 하려면 AD FS 옵션 또는 Azure Active Directory 옵션을 사용 하 여 Azure 스택을 배포할 수 있습니다.

### <a name="connectivity-requirements"></a>연결 요구 사항
1. Azure 스택 사용자 포털에 액세스할 수 있는 컴퓨터에서 웹 브라우저를 통해 Windows Azure 팩 테 넌 트 포털에 액세스할 수 있는지 확인 합니다.
2. Azure 스택 AzS WASP01 가상 컴퓨터는 Windows Azure 팩 테 넌 트 포털 컴퓨터에 연결 하려면 있어야 합니다. Ping.exe를 사용 하 여 네트워크 연결을 확인 합니다. 
3.  새 커넥터 서비스에 대 한 유효한 인증서가 있어야 합니다. 이 SSL 인증서는 신뢰할 수 있는 인증 기관 (CA)에서 발급 합니다. 자체 서명 된 인증서를 사용할 수 없습니다. Azure 스택 (특히 AzS WASP01 VM) 및 Azure 스택 사용자 포털에 액세스 하는 테 넌 트를 사용할 수 있는 다른 컴퓨터에서 SSL 인증서를 신뢰할 수 있어야 합니다.
 
    >[!NOTE]
    Server Core 설치 옵션으로 Windows Server를 실행 하는 AzS WASP01을 하기 때문에 인증서를 가져오려면 Certutil.ext 같은 명령줄 도구를 사용할 수 있습니다. .Cer 파일에 AzS-WASP01 c:\temp을 복사 하 고 다음 명령을 실행 수 예를 들어 **"CA" "c:\temp\certname.cer" certutil-addstore**합니다.

4.  Azure 스택 포털을 통해 Windows Azure 팩 테 넌 트 가상 컴퓨터에 대 한 RDP 연결을 설정 하려면 Windows Azure Pack 환경에는 테 넌 트 Vm에 원격 데스크톱 트래픽을 허용 해야 합니다.
5.  Azure 스택 테 넌 트 가상 컴퓨터와 Windows Azure 팩 테 넌 트 가상 컴퓨터 간의 연결을 위한 두 환경 모두에서 외부 IP 주소 라우팅 가능 해야 합니다. 이 연결 환경 간의 가상 컴퓨터 이름을 확인 하도록 DNS 서버 연결도 포함 될 수 있습니다.

### <a name="iis-requirements"></a>IIS 요구 사항
Windows Azure 팩 테 넌 트 포털 (실제 호스트, 가상 컴퓨터 또는 여러 가상 컴퓨터를 수 있음)를 호스트 하는 컴퓨터에는 URL 재작성 IIS 확장이 설치 되어 있어야 합니다. 아직 설치 되지 않은 경우에서 다운로드할 수 있습니다 [여기](https://www.iis.net/downloads/microsoft/url-rewrite)합니다. 여러 가상 컴퓨터 테 넌 트 포털을 호스트 하는 경우 각 가상 컴퓨터에 확장을 설치 합니다.

## <a name="configure-azure-stack"></a>Azure 스택 구성
Windows Azure 팩 커넥터를 구성 하기 전에 다중 클라우드 모드 Azure 스택 사용자 포털에서 사용 하도록 설정 해야 합니다. 이 모드에서는 리소스에 액세스할 수 있는 클라우드에서 선택할 수 있습니다.

다중 클라우드 모드를 사용 하려면 Azure 스택 배포 후 추가 AzurePackConnector.ps1 스크립트를 실행 해야 합니다. 다음 표에서 스크립트 매개 변수를 설명합니다.


|  매개 변수 | 설명 | 예 |   
| -------- | ------------- | ------- |  
| AzurePackClouds | Windows Azure 팩 커넥터의 Uri입니다. 이러한 Uri는 Windows Azure 팩 테 넌 트 포털 일치 해야 합니다. | @{CloudName = "AzurePack1"; CloudEndpoint = "https://waptenantportal1:40005"},@{CloudName = "AzurePack2"; CloudEndpoint = "https://waptenantportal2:40005"}<br><br>  (기본적으로 포트 값이 40005.) |  
| AzureStackCloudName | 로컬 Azure 스택 클라우드 나타내는 레이블을 지정 합니다.| "AzureStack" |
| DisableMultiCloud | 다중 클라우드 모드를 해제 스위치입니다.| N/A |
| | |

배포 후 즉시 또는 이후 추가 AzurePackConnector.ps1 스크립트를 실행할 수 있습니다. 배포 후 즉시 스크립트를 실행 하려면 Azure 스택 배포 완료 된 동일한 Windows PowerShell 세션을 사용 합니다. 그렇지 않은 경우 (azurestackadmin 계정으로 로그인)를 관리자로 새 Windows PowerShell 세션을 열 수 있습니다.

1. (사용자 환경에만 사용 되는 값)으로 다음 명령을 사용 하 여 추가 AzurePackConnector.ps1 스크립트를 실행 합니다. 알림 추가 AzurePackConnector 스크립트에 둘 이상의 Windows Azure 팩 커넥터 끝점을 추가할 수 있습니다.
 
 ```powershell
    $cred = New-Object System.Management.Automation.PSCredential("cloudadmin@azurestack.local", `
    (ConvertTo-SecureString -String "<password>" -AsPlainText -Force))
    $session = New-PSSession -ComputerName 'azs-ercs01' `
     -Credential $cred `
     -ConfigurationName PrivilegedEndpoint `
     -Authentication Credssp

    # Enable Multicloud
    Invoke-Command -Session $session -ScriptBlock { Add-AzurePackConnector -AzurePackClouds `
    @{CloudName = "AzurePack_1"; CloudEndpoint = "https://waptenantportal1:40005"},`
    @{CloudName = "AzurePack_2"; CloudEndpoint = "https://waptenantportal2:40005" } `
    -AzureStackCloudName "AzureStack" }

 ```
> [!NOTE]
> 현재 빌드에 문제가 있습니다 추가 AzurePackConnector 스크립트 종료 된 후에 남아 폴링 루프 오랜 시간 (몇 분) 될 때까지 여기서 끊어지도록 합니다. 메시지를 확인 한 후 **VERBOSE: ' Azure 팩 커넥터 구성 ' 단계 상태: '성공'**, 스크립트 실행을 중단 하거나 자체적으로 중지 될 때까지 대기할 수 있습니다. 것으로 구성에 이미이 성공 했으므로 차이 확인 하지 않습니다.

2. 지정한 각 Windows Azure Pack 환경에 대 한이 스크립트에 의해 생성 되는 출력 파일을 기록해 둡니다. 파일은: \\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput 합니다. 이러한 파일은 대상 Windows Azure 팩 환경을 구성 하는 데 필요한 정보를 포함 합니다. 이 파일이이 지침의 뒷부분에 나오는 스크립트 매개 변수로 전달합니다. 이 파일에는 다음과 같은 정보가 포함 되어 있습니다.

    * **AzurePackConnectorEndpoint**: Windows Azure 팩 커넥터 서비스에 끝점을 포함 합니다.
    * **AuthenticationIdentityProviderPartner**: 다음 값 쌍을 포함 합니다.
        * 인증 토큰 서명 인증서를 Windows Azure 팩 테 넌 트 API 호출 스택 Azure 포털 확장을 허용 하도록 신뢰할 수 있어야 합니다.

        * "Realm"은 서명 인증서와 연결 합니다. 예를 들어: https://adfs.local.azurestack.global.external/adfs/c1d72562-534e-4aa5-92aa-d65df289a107/ 합니다.

3.  출력 파일이 있는 폴더를 찾습니다 (\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput), 로컬 컴퓨터에 파일을 복사 합니다. 파일은 다음과 유사: AzurePack-06-27-15-50.txt 합니다.

4.  구성을 테스트 합니다.

    a. 브라우저를 열고 Azure 스택 사용자 포털 (https://portal.local.azurestack.external/)에 로그인 합니다.
    
    나. 테 넌 트와 포털 부하로 로그인 한 후 Azure 팩 클라우드에서 구독 또는 확장을 가져올 수 없게 하는 방법에 대 한 오류가 표시 됩니다. 클릭 **확인** 를 이러한 메시지를 닫습니다. (이러한 오류 메시지는 사라집니다 Windows Azure Pack을 구성한 후.)

    다. 공지는 **클라우드** 포털의 왼쪽 위 모서리에 있는 드롭 다운 목록입니다.

    ![Azure 스택 사용자 인터페이스에서 클라우드 선택기](media/azure-stack-manage-wap/image3.png)

## <a name="configure-windows-azure-pack"></a>구성할 Windows Azure 팩
이 커넥터 미리 보기 릴리스만,에 대 한 Windows Azure 팩을 수동으로 구성을 해야 합니다.

>[!IMPORTANT]
프로덕션 배포 및 테스트 환경에만이 미리 보기 릴리스에 대 한 Windows Azure 팩 커넥터를 사용 합니다.

1.  Windows Azure 팩 테 넌 트 포털 가상 컴퓨터에 커넥터 MSI 파일을 설치 하 고 인증서를 설치 합니다. (여러 테 넌 트 포털 가상 컴퓨터를 설정한 경우이 단계를 완료 해야이 각 가상 컴퓨터에 있습니다.)

    a. 파일 탐색기에서 복사는 **WAPConnector** 폴더 (작업 이전에 다운로드 한) 라는 폴더 **c:\temp** 테 넌 트 포털의 가상 컴퓨터에 있습니다.

    나. 테 넌 트 포털의 가상 컴퓨터에 콘솔 또는 RDP 연결을 엽니다.

    다. 디렉터리 **c:\temp\wapconnector\setup\scripts**, 실행 및는 **설치 Connector.ps1** 세 MSI 파일을 설치 하는 스크립트입니다. 매개 변수가 필요 하며

     ```powershell
    cd C:\temp\wapconnector\setup\scripts\

    .\Install-Connector.ps1
    ```
     d. 디렉터리 **c:\inetpub** 3 개의 새 사이트 설치 되어 있는지 확인 합니다.

       * MgmtSvc-Connector

       * MgmtSvc-ConnectorExtension

       * MgmtSvc-ConnectorController

    e. 동일한 **c:\temp\wapconnector\setup\scripts** 실행 폴더는 **구성 Certificates.ps1** 인증서를 설치 하는 스크립트입니다. 기본적으로 Windows Azure 팩에서 테 넌 트 포털 사이트에 사용할 수 있는 동일한 인증서를 사용 합니다 것입니다. 유효한 인증서 (Azure 스택 AzS WASP01 가상 컴퓨터와 Azure 스택 포털에 액세스 하는 모든 클라이언트 컴퓨터에서 신뢰할 수 있음) 인지 확인 합니다. 그렇지 않은 경우 통신 작동 하지 않습니다. (또는 있습니다 명시적으로 전달할 수 인증서 지문을 매개 변수로 사용 하 여-지문 매개 변수.)

     ```powershell
        cd C:\temp\wapconnector\setup\scripts\

        .\Configure-Certificates.ps1
    ```

    f. 이러한 세 가지 서비스의 구성을 완료 하려면 실행는 **구성 WapConnector.ps1** Web.config 파일 매개 변수를 업데이트 하는 스크립트입니다.

    |  매개 변수 | 설명 | 예 |   
    | -------- | ------------- | ------- |  
    | TenantPortalFQDN | Windows Azure 팩 테 넌 트 포털 FQDN입니다. | tenant.contoso.com | 
    | TenantAPIFQDN | Windows Azure 팩 테 넌 트 API FQDN입니다. | tenantapi.contoso.com  |
    | AzureStackPortalFQDN | Azure 스택 사용자 포털 FQDN입니다. | portal.local.azurestack.external |
    | | |
    
     ```powershell
    .\Configure-WapConnector.ps1 -TenantPortalFQDN "tenant.contoso.com" `
         -TenantAPIFQDN "tenantapi.contoso.com" `
         -AzureStackPortalFQDN "portal.local.azurestack.external"
    ```
    g. 여러 테 넌 트 포털 가상 컴퓨터를 설정한 경우 각 이러한 가상 컴퓨터에 대해 1 단계를 반복 합니다.

2. 각 Windows Azure 팩 테 넌 트 API 가상 컴퓨터에 새 테 넌 트 API MSI를 설치 합니다.

    a. 부하 분산 장치를 사용 하는 경우에 가상 컴퓨터를 오프 라인으로 표시 하는 것이 좋습니다.

    나. 파일 탐색기에서 복사 된 **WAPConnector** 폴더 라는 폴더를 **c:\temp** 각 테 넌 트 API 컴퓨터입니다.

    다. 이전에 저장 AzurePackConnectorOutput.txt 파일에 복사 하려면 **c:\temp\WAPConnector**합니다.

    d. 파일을 복사 하는 테 넌 트 API VM에 콘솔 또는 RDP 연결을 엽니다.
    
    e. 디렉터리 **c:\temp\wapconnector\setup\scripts**, 실행 및 **업데이트 TenantAPI.ps1**합니다. 이 새로운 버전 WAP 테 넌 트 API의 현재 STS와 뿐만 아니라 Azure 스택에서 AD FS의 인스턴스와 트러스트 관계를 사용할 수 있도록 변경을 포함 합니다.

     ```powershell
    cd C:\temp\wapconnector\setup\packages\

    .\Update-TenantAPI.ps1
    ```

    f.  테 넌 트 API를 실행 중인 다른 가상 컴퓨터에서 2 단계를 반복 합니다.
3. **하나만** Azure 스택에 테 넌 트 API 및 AD FS 인스턴스 간의 트러스트 관계를 추가 하려면 TrustAzureStack.ps1 구성 스크립트를 실행 하는 테 넌 트 API Vm의 합니다. Microsoft.MgmtSvc.Store 데이터베이스 sysadmin 권한이 있는 계정을 사용 해야 합니다. 이 스크립트에는 다음 매개 변수가 있습니다.

    | 매개 변수 | 설명 | 예 |
    | --------- | ------------| ------- |
   | SqlServer | Microsoft.MgmtSvc.Store 데이터베이스를 포함 하는 SQL Server의 이름입니다. 이 매개 변수는 필수입니다. | SQLServer | 
   | 데이터 파일 | 이전 Azure 스택 클라우드 다중 모드 구성 하는 동안 생성 된 출력 파일입니다. 이 매개 변수는 필수입니다. | AzurePack-06-27-15-50.txt | 
   | PromptForSqlCredential | SQL Server 인스턴스에 연결할 때 사용 하는 SQL 인증 자격 증명에 대 한 대화형으로 있습니다 스크립트 라는 해야 나타냅니다. 지정 된 자격 증명은 사용자 로그인을 삭제 및 데이터베이스, 스키마, 제거할 수 있는 충분 한 권한이 있어야 합니다. 아무 것도 제공 하는 경우 스크립트 권한이 현재 사용자 컨텍스트를 가정 합니다. | 값이 없는 필요 합니다. |
   |  |  |

    사용 하도록 SQL Server를 모르는 경우에 검색할 수 있습니다. 테 넌 트 API 컴퓨터에 연결 하, Unprotect 경우 명령을 사용 하 여 테 넌 트 API Web.config 파일의 보호를 해제 하 고 연결 문자열에서 서버 이름을 찾습니다. 보호 경우 테 넌 트 API Web.config 파일을 보호 하는 다시 실행 해야 합니다.

  ```powershell
  cd C:\temp\wapconnector\setup\scripts\

 .\Configure-TrustAzureStack.ps1 -SqlServer "SQLServer" `
       -DataFile "C:\temp\wapconnector\AzurePackConnectorOutput.txt"
  ```

## <a name="example"></a>예
다음 예제에서는 단일 노드 Azure 스택 구성에서 완전 한 Windows Azure 팩 커넥터 배포 및 두 개의 Windows Azure 팩 Express 설치를 보여 줍니다. (예제 이름으로 단일 컴퓨터에 각 빠른 설치를 *wapcomputer1* 및*wapcomputer2*.)

```powershell
# Run the following script on the Azure Stack host
$cred = New-Object System.Management.Automation.PSCredential("cloudadmin@azurestack.local",`
     (ConvertTo-SecureString -String "p@ssw0rd" -AsPlainText -Force))
$session = New-PSSession -ComputerName 'azs-ercs01' -Credential $cred `
     -ConfigurationName PrivilegedEndpoint -Authentication Credssp
 
# Enable Multicloud
invoke-command -Session $session -ScriptBlock { Add-AzurePackConnector -AzurePackClouds `
     @{CloudName = "AzurePack_1"; CloudEndpoint = "https://wapcomputer1.contoso.com:40005"},`
     @{CloudName = "AzurePack_2"; CloudEndpoint = "https://wapcomputer2.contoso.com:40005"}`
     -AzureStackCloudName "AzureStack" }  

```
.exe 파일을 다운로드는 [Microsoft 다운로드 센터](https://aka.ms/wapconnectorazurestackdlc), 압축을 푼 및 WAPConnector 폴더에 복사는 **c:\temp** Windows Azure 팩 컴퓨터의 폴더에 있습니다. 앞의 스크립트에서 출력으로 생성 된 파일을 복사 (에 있는 \\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput)에 **c:\temp\WAPConnector** 폴더입니다. (파일은 다음과 유사 하 게 찾습니다: AzurePack-06-27-15-50.txt.) Windows Azure Pack의 인스턴스마다 한 번씩, 다음 스크립트를 실행 합니다.

 ```powershell
# Install Connector components
cd C:\temp\WAPConnector\Setup\Scripts
.\Install-Connector.ps1

# Configure Certificates for the new Connector services
.\Configure-Certificates.ps1

# Configure the Connector services
.\Configure-WapConnector.ps1 -TenantPortalFQDN "wapcomputer1.contoso.com" `
     -TenantAPIFQDN "wapcomputer1.contoso.com" `
     -AzureStackPortalFQDN "portal.local.azurestack.external"

# Install the updated TenantAPI
.\Update-TenantAPI.ps1

# Establish trust with the Azure Stack AD FS
.\Configure-TrustAzureStack.ps1 -SqlServer "wapcomputer1" `
     -DataFile "C:\temp\wapconnector\AzurePack-06-27-15-50.txt" 

```
## <a name="troubleshooting-tips"></a>문제 해결 팁
1.  Azure 스택 및 Windows Azure 팩 간의 네트워크 연결 되어 있는지 확인 합니다. 이 연결은 새 커넥터 서비스가 실행 되 고 있는 Windows Azure 팩 테 넌 트 포털 가상 컴퓨터와 Azure 스택 포털에 액세스 하는 테 넌 트 컴퓨터 사이 여야 합니다.
2.  지정한 모든 Fqdn이 올바른지 확인 합니다.
3.  Azure 스택 (특히 AzS WASP01 VM)에서 새로운 커넥터 서비스에 사용 되는 SSL 인증서는 신뢰 하는지 확인 하 고 다른 컴퓨터의 테 넌 트 Azure 스택 사용자 포털에 액세스를 사용할 수 있습니다.
4. 알려진된 문제에 대 한 참조 [Microsoft Azure 스택 문제 해결](azure-stack-troubleshooting.md)합니다.


## <a name="next-steps"></a>다음 단계
[관리자 및 사용자 포털을 사용 하 여 Azure 스택](azure-stack-manage-portals.md)
