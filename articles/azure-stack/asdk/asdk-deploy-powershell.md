---
title: "Azure 스택-PowerShell 배포 | Microsoft Docs"
description: "이 자습서에서는 ASDK 명령줄에서 설치합니다."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 48ccccaba6b7f5780f1d42dfbe5d9747c5e30292
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-deploy-the-asdk-from-the-command-line"></a>자습서: 명령줄에서 ASDK 배포
이 자습서는 Azure 스택 개발 키트 (ASDK) 비-프로덕션 환경에서 명령줄에서 배포할 수 있습니다. 

ASDK는 평가 하 고 Azure 스택 기능 및 서비스를 보여 줍니다. 배포할 수 있는 테스트 및 개발 환경입니다. 실행 해야 환경 하드웨어를 준비 하 고 (몇 시간 걸릴 됩니다) 일부 스크립트를 실행 합니다. 그 후 수에 로그인 할 관리자 및 사용자 포털에 Azure 스택을 사용 하 여 시작 합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 다운로드 하 고 배포 패키지를 압축 합니다.
> * 개발 키트 호스트 컴퓨터를 준비 합니다. 
> * 배포 후 구성을 수행합니다
> * Azure에 등록

## <a name="prerequisites"></a>필수 조건 
개발 키트 호스트 컴퓨터를 준비 합니다. 하드웨어, 소프트웨어 및 네트워크를 계획 합니다. 개발 키트 (개발 키트 호스트)를 호스트 하는 컴퓨터 하드웨어, 소프트웨어 및 네트워크 요구 사항을 충족 해야 합니다. 또한 Azure Active Directory (Azure AD) 또는 Active Directory Federation Services (AD FS)을 사용 하 여 간에 선택 해야 합니다. 설치 프로세스를 원활 하 게 실행 하 여 배포를 시작 하기 전에 이러한 필수 구성이 요소를 준수 해야 합니다. 

ASDK를 배포 하기 전에 계획 된 개발 키트 호스트 컴퓨터의 하드웨어, 운영 체제, 계정 및 네트워크 구성에는 ASDK를 설치 하기 위한 최소 요구 사항을 충족 하는지 확인 합니다.

**[ASDK 배포 계획 고려 사항 검토](asdk-deploy-considerations.md)**

> [!TIP]
> 사용할 수는 [Azure 스택 배포 요구 사항 확인 도구](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) 하드웨어 요구 사항을 모두 충족 하는지 확인 하려면 운영 체제를 설치한 후입니다.

## <a name="download-and-extract-the-deployment-package"></a>다운로드 하 고 배포 패키지를 압축 합니다.
을 개발 키트 호스트 컴퓨터가 ASDK를 설치 하기 위한 기본 요구 사항을 충족 하는지 확인 한 후 다음 단계를 다운로드 하 여 ASDK 배포 패키지를 추출할입니다. 배포 패키지에는 부팅 가능한 운영 체제 및 Azure 스택 설치 파일을 포함 하는 가상 하드 드라이브 Cloudbuilder.vhdx 파일을 포함 합니다.

개발 키트 호스트 또는 다른 컴퓨터에 배포 패키지를 다운로드할 수 있습니다. 추출 된 배포 파일 다른 컴퓨터를 사용 하 여 개발 키트 호스트에 대 한 하드웨어 요구 사항을 줄일 수 있으므로 60GB의 사용 가능한 디스크 공간을 차지 합니다.

**[다운로드 하 고 Azure 스택 개발 키트 (ASDK) 추출](asdk-download.md)**

## <a name="prepare-the-development-kit-host-computer"></a>개발 키트 호스트 컴퓨터를 준비 합니다.
ASDK 호스트 컴퓨터에 설치할 수 전에 환경을 준비 해야 하 고 시스템이 VHD에서 부팅 하도록 구성 합니다. 이 단계를 개발 키트 호스트 (가상 하드 드라이브를 부팅 가능한 운영 체제와 Azure 스택 설치 파일 포함) Cloudbuilder.vhdx 부팅 됩니다.

PowerShell을 사용 하 여 CloudBuilder.vhdx에서 부팅 하도록 ASDK 호스트 컴퓨터를 구성 합니다. 이 명령은 경우 다운로드 하 고 추출 Azure 스택 가상 하드 디스크 (CloudBuilder.vhdx)에서 부팅 하도록 ASDK 호스트 컴퓨터를 구성 합니다. 다음이 단계를 완료 한 후 ASDK 호스트 컴퓨터를 다시 시작 합니다.

CloudBuilder.vhdx에서 부팅 하도록 ASDK 호스트 컴퓨터를 구성 합니다.

  1. 관리자 권한으로 명령 프롬프트를 시작 합니다.
  2. `bcdedit /copy {current} /d "Azure Stack"` 실행
  3. 복사 (CTRL + C) CLSID 값 반환 필요한 {}를 포함 하 여 ' s입니다. 이 값은 {CLSID} 라고 하 고 나머지 단계에 (CTRL + V 또는 마우스 오른쪽 단추 클릭)에 붙여 넣이 필요 합니다.
  4. `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx` 실행 
  5. `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx` 실행 
  6. `bcdedit /set {CLSID} detecthal on` 실행 
  7. `bcdedit /default {CLSID}` 실행
  8. 부팅 설정을 확인 하려면 실행 `bcdedit`합니다. 
  9. CloudBuilder.vhdx 파일 (C:\CloudBuilder.vhdx) C:\ 드라이브의 루트로 이동 되었는지와 개발 키트 호스트 컴퓨터를 다시 시작 되도록 합니다. ASDK 호스트 컴퓨터를 다시 시작할 때 ASDK 배포를 시작 하는 CloudBuilder.vhdx 가상 컴퓨터 하드 드라이브에서 부팅 해야 합니다. 

> [!IMPORTANT]
> 다시 시작 하기 전에 직접 물리적 컴퓨터 또는 개발 키트 호스트 컴퓨터에 대 한 KVM 액세스 가졌는지 확인 합니다. VM을 처음 시작 하는 경우 Windows Server 설치를 완료 하 라는 메시지가 표시 됩니다. 개발 키트 호스트 컴퓨터에 로그온 할 때 사용한 동일한 관리자 자격 증명을 제공 합니다. 

### <a name="prepare-the-development-kit-host-using-powershell"></a>PowerShell을 사용 하 여 개발 키트 호스트 준비 
개발 키트 후 호스트 컴퓨터가 정상적으로 부팅 CloudBuilder.vhdx 이미지로 개발 키트 호스트 컴퓨터에 로그온 할 때 사용한 (및 Windows Server를 완료 하는 중의 일부분으로 제공 하는 동일한 로컬 관리자 자격 증명으로 로그인 설치는 호스트 컴퓨터는 VHD에서 부팅할 때). 

> [!NOTE]
> 선택적으로 구성할 수도 있습니다 [Azure 스택 원격 분석 설정이](asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *전에* 는 ASDK를 설치 합니다.

관리자 권한 PowerShell 콘솔을 열고 ASDK 개발 키트 호스트에 배포 하려면이 섹션의 명령을 실행 합니다.

> [!IMPORTANT] 
> ASDK 설치 네트워킹에 대 한 정확히 하나의 네트워크 인터페이스 카드 (NIC)를 지원합니다. 여러 Nic를 설정한 경우 하나만 사용 하도록 설정할 (올바르고 확인 조건은 다른 모든) 배포 스크립트를 실행 하기 전에.

Azure AD와 Azure 스택을 배포할 수 있습니다 또는 Windows Server AD FS를 id 공급자로 합니다. Azure 스택, 리소스 공급자 및 다른 응용 프로그램에는 둘 다와 함께 동일한 방식으로 작동 합니다.

> [!TIP]
> 모든 설치 매개 변수 (InstallAzureStackPOC.ps1 선택적 매개 변수 및 아래 예제 참조)을 지정 하지 않으면 필수 매개 변수에 대 한 메시지가 표시 됩니다.

### <a name="deploy-azure-stack-using-azure-ad"></a>Azure AD를 사용 하 여 Azure 스택 배포 
Azure 스택을 배포 하려면 **id 공급자로 Azure AD를 사용 하 여**, 직접 또는 투명 프록시를 통해 인터넷에 연결 되어 있어야 합니다. 

Azure AD를 사용 하 여 개발 키트를 배포 하려면 다음 PowerShell 명령을 실행 합니다.

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator     
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password
  ```

몇 분 정도 ASDK 설치에 Azure AD 자격 증명에 대 한 라는 메시지가 표시 됩니다. Azure AD 테 넌 트에 대 한 전역 관리자 자격 증명을 제공 해야 합니다. 

### <a name="deploy-azure-stack-using-ad-fs"></a>Azure 스택 AD FS를 사용 하 여 배포 
개발 키트를 배포 하려면 **AD FS를 사용 하 여 id 공급자로**, (하기만-UseADFS 매개 변수를 추가 하면 됨) 다음 PowerShell 명령을 실행: 

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator 
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -UseADFS
  ```

AD FS 배포는 기본 스탬프 디렉터리 서비스를 id 공급자로 사용 됩니다. 로그인 하는 데 기본 계정은 azurestackadmin@azurestack.local, 및 PowerShell 설치 명령의 일부로 제공 된 암호 설정 됩니다.

배포 프로세스에는 시간 동안 시스템이 자동으로 재부팅 한 번 몇 시간 걸릴 수 있습니다. 배포에 성공 하면 PowerShell 콘솔에 표시 됩니다: **완료: 작업 '배포'**합니다. 스크립트를 사용 하 여 다시 실행 해 수 배포가 실패 하는 경우 다시 실행된-매개 변수입니다. 할 수 있습니다 또는 [ASDK 재배포](asdk-redeploy.md) 처음부터 합니다.

> [!IMPORTANT]
> ASDK 호스트를 다시 부팅 한 후 배포 진행률을 모니터링 하려는 경우에 AzureStack\AzureStackAdmin로 로그인 해야 합니다. 로그인 되 면 로컬 관리자로 호스트 컴퓨터를 다시 시작 (고 후 azurestack.local 도메인에 가입)에 배포 진행률을 볼 수 없습니다. 대신 실행 중인 유효성을 검사 하려면 azurestack로 로그인, 배포 다시 실행 하지 마십시오.


#### <a name="azure-ad-deployment-script-examples"></a>Azure AD 배포 스크립트 예제
전체를 스크립팅할 수 Azure AD 배포 합니다. 다음은 몇 가지 선택적 매개 변수를 포함 하는 주석 처리 된 몇 가지 예입니다.

Azure AD id만 연결 된 경우 **하나의** Azure AD 디렉터리:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 52.168.138.145 #Example time server IP address.
```

Azure AD id와 연결 된 경우 **1 보다 큰** Azure AD 디렉터리:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" #Example: user@AADDirName.onmicrosoft.com 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -InfraAzureDirectoryTenantName "<Azure AD directory in the form of domainname.onmicrosoft.com or an Azure AD verified custom domain name>" -TimeServer 52.168.138.145 #Example time server IP address.
```

사용자 환경에 DHCP 사용 (제공 된 예제 사용) 위의 옵션 중 하나에 다음과 같은 추가 매개 변수 포함 해야 합니다. 

```powershell
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -NatIPv4Subnet 10.10.10.0/24 -NatIPv4Address 10.10.10.3 -NatIPv4DefaultGateway 10.10.10.1 -TimeServer 10.222.112.26
```

### <a name="asdk-installazurestackpocps1-optional-parameters"></a>ASDK InstallAzureStackPOC.ps1 선택적 매개 변수
|매개 변수|필수/선택|설명|
|-----|-----|-----|
|AdminPassword|필수|개발 키트 배포의 일부로 생성 되는 모든 가상 컴퓨터에서 로컬 관리자 계정 및 다른 모든 사용자 계정을 설정 합니다. 이 암호는 호스트에서 현재 로컬 관리자 암호와 일치 해야 합니다.|
|InfraAzureDirectoryTenantName|필수|테 넌 트 디렉터리를 설정합니다. AAD 계정의 사용 권한이 있는 여러 디렉터리를 관리 하는 특정 디렉터리를 지정 하려면이 매개 변수를 사용 합니다. 전체 형식의 AAD 디렉터리 테 넌 트의 이름입니다. onmicrosoft.com 또는 Azure AD에 사용자 지정 도메인 이름을 확인 합니다.|
|TimeServer|필수|이 매개 변수를 사용 하 여 특정 시간 서버를 지정 합니다. 이 매개 변수는 유효한 시간 서버 IP 주소로 제공 되어야 합니다. 서버 이름은 지원 되지 않습니다.|
|InfraAzureDirectoryTenantAdminCredential|옵션|Azure Active Directory 사용자 이름과 암호를 설정합니다. 이러한 Azure 자격 증명을 사용 하 여 조직 ID 해야 합니다.|
|InfraAzureEnvironment|옵션|이 Azure 스택 배포를 등록 하려는 Azure 환경을 선택 하십시오. 옵션에는 공용 Azure, Azure-중국 Azure-미국 정부 포함 됩니다.|
|DNSForwarder|옵션|DNS 서버는 Azure 스택 배포의 일부로 생성 됩니다. 스탬프 외부 이름을 확인 하기 위해 솔루션 내에 있는 컴퓨터를 허용 하려면 기존 DNS 서버 인프라를 제공 합니다. 스탬프에 DNS 서버는이 서버에 알 수 없는 이름 확인 요청을 전달합니다.|
|NatIPv4Address|DHCP NAT 지원에 필요한|MA BGPNAT01에 대 한 고정 IP 주소를 설정합니다. 인터넷에 액세스하기 위해 DHCP가 유효한 IP 주소를 할당할 수 없는 경우 이 매개 변수만 사용합니다.|
|NatIPv4Subnet|DHCP NAT 지원에 필요한|NAT 지원 통한 DHCP에 사용 되는 IP 서브넷 접두사입니다. 인터넷에 액세스하기 위해 DHCP가 유효한 IP 주소를 할당할 수 없는 경우 이 매개 변수만 사용합니다.|
|PublicVlanId|옵션|VLAN ID를 설정 합니다. 호스트와 MA BGPNAT01 VLAN ID 및 액세스 하는 실제 네트워크 (인터넷)를 구성 해야 하는 경우에이 매개 변수를 사용 합니다. 예를 들어.\InstallAzureStackPOC.ps1-305 PublicVLan를 자세한 정보|
|다시 실행|옵션|이 플래그를 사용 하 여 배포 다시 실행 합니다. 모든 이전 입력이 사용 됩니다. 이전에 제공 된 데이터를 다시 입력할 몇 가지 고유한 값 생성 되어 배포에 사용 하기 때문에 지원 되지 않습니다.|


## <a name="perform-post-deployment-configurations"></a>배포 후 구성을 수행합니다
ASDK를 설치한 후은 몇 가지 권장 되는 설치 후 검사 및 구성 변경 내용을입니다. 설치 유효성을 검사할 수 테스트 AzureStack cmdlet을 사용 하 여 성공적으로 설치 되었는지 그리고 Azure 스택 PowerShell 및 GitHub와 도구를 설치 합니다. 

또한 암호 만료 정책을 개발 키트 호스트에 대 한 암호 평가 기간이 종료 되기 전에 만료 되지 되도록 다시 설정 해야 합니다.

> [!NOTE]
> 선택적으로 구성할 수도 있습니다 [Azure 스택 원격 분석 설정이](asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *후* 는 ASDK를 설치 합니다.

**[후 ASDK 배포 작업](asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Azure에 등록
수 있도록 Azure와 함께 Azure 스택을 등록 해야 [Azure 마켓플레이스 항목을 다운로드](asdk-marketplace-item.md) Azure 스택에 있습니다.

**[Azure 스택 Azure 등록](asdk-register.md)**

## <a name="next-steps"></a>다음 단계
축하합니다! 다음이 단계를 완료 한 후 둘 다와 함께 개발 키트 환경 해야 합니다. [관리자](https://adminportal.local.azurestack.external) 및 [사용자](https://portal.local.azurestack.external) 포털입니다. 

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 다운로드 하 고 배포 패키지를 압축 합니다.
> * 개발 키트 호스트 컴퓨터를 준비 합니다. 
> * 배포 후 구성을 수행합니다
> * Azure에 등록

Azure 스택 마켓플레이스 항목을 추가 하는 방법에 알아보려면 다음 자습서를 진행 합니다.

> [!div class="nextstepaction"]
> [Azure 스택 마켓플레이스 항목 추가](asdk-marketplace-item.md)

