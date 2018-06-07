---
title: Azure Stack의 진단
description: Azure 스택에서 진단 로그 파일을 수집 하는 방법
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 04/27/2018
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: 28e1939d3c9cb5a9b9080e60230ad5600ad8a6a3
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="azure-stack-diagnostics-tools"></a>Azure 스택 진단 도구

Azure 스택은 큰 컬렉션 함께 작동 하 고 서로 상호 작용 하는 구성 요소입니다. 이러한 모든 구성이 요소가 자신의 고유한 로그가 생성 됩니다. 진단 문제 여러 Azure 스택 구성 요소 상호 작용에서 발생 하는 오류에 대 한 특히 까다로운 작업 크기 수 있습니다. 

로그 수집 메커니즘은 쉽고 효율적으로 우리의 진단 도구 도움이 됩니다. 다음 다이어그램에 표시 된 Azure 스택 작업에서 수집 도구를 어떻게 로그:

![Azure 스택 진단 도구](media/azure-stack-diagnostics/get-azslogs.png)
 
 
## <a name="trace-collector"></a>추적 수집기
 
추적 수집기는 기본적으로 사용 하 고 Azure 스택 구성 요소 서비스에서 모든 이벤트가 추적에 대 한 ETW (Windows) 로그를 수집 하는 백그라운드에서 계속 실행 합니다. ETW 로그 5 일 사용 기간 제한으로 일반적인 로컬 공유에 저장 됩니다. 이 한도 도달 하면 가장 오래 된 파일은 새로 만들어지는 삭제 됩니다. 각 파일에 대해 허용 되는 기본 최대 크기 사항은 200MB입니다. 크기 검사 2 분 마다 발생 하 고 현재 파일 경우 > = 200mb로 저장 되 고 새 파일이 생성 됩니다. 이벤트 세션 별로 생성 된 총 파일 크기에도 8GB로 제한이 됩니다. 

## <a name="log-collection-tool"></a>로그 수집 도구
 
PowerShell cmdlet **Get AzureStackLog** 는 Azure 스택 환경에서 모든 구성 요소에서 로그를 수집 하는 데 사용할 수 있습니다. 사용자 정의 위치에 있는 zip 파일에 저장 합니다. Azure 스택 기술 지원 팀이 문제를 해결 하려면 로그를 필요한 경우이 도구를 실행 하도록 요청 받을 수 있습니다.

> [!CAUTION]
> 이러한 로그 파일에는 개인 식별이 가능한 정보 (PII) 포함할 수 있습니다. 모든 로그 파일을 공개적으로 게시 하기 전에이를 고려 합니다.
 
다음은 수집 하는 몇 가지 예제 로그 유형입니다.
*   **Azure 스택 배포 로그**
*   **Windows 이벤트 로그**
*   **Panther 로그**
*   **클러스터 로그**
*   **진단 로그 저장소**
*   **ETW 로그**

이러한 파일에 수집 되 고 추적 수집기에서 공유에 저장 됩니다. **Get AzureStackLog** PowerShell cmdlet 다 필요한 경우 사용할 수 있습니다.
 
### <a name="to-run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>Azure 스택 개발 키트 (ASDK) 시스템에서 AzureStackLog Get을 실행 하려면
1. 로 로그인 **AzureStack\CloudAdmin** 호스트 합니다.
2. 관리자 권한으로 PowerShell 창을 엽니다.
3. 실행 된 **Get AzureStackLog** PowerShell cmdlet.

**예제:**

  모든 역할에 대 한 모든 로그를 수집 합니다.

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs
  ```

  VirtualMachines 및 BareMetal 역할에서 로그를 수집 합니다.

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal
  ```

  날짜가 지난 8 시간에 대 한 로그 파일에 대 한 필터링 VirtualMachines 및 BareMetal 역할에서 로그를 수집 합니다.
    
  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

  8 시간 전 및 2 시간 전 사이 시간 간격에 대 한 로그 파일에 대 한 필터링 된 날짜와 VirtualMachines 및 BareMetal 역할에서 로그를 수집 합니다.

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

### <a name="to-run-get-azurestacklog-on-azure-stack-integrated-systems-version-1804-and-later"></a>통합 시스템 버전 1804 이상 Azure 스택에 AzureStackLog Get을 실행 하려면

통합된 된 시스템에서 로그 수집 도구를 실행 하려면 액세스 권한을 가진 끝점 (PEP)가 필요 합니다. 다음은 예제 스크립트는 PEP 통합된 된 시스템에 로그를 수집 하려면 사용 하 여 실행할 수 있습니다.

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
 
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
 
$shareCred = Get-Credential
 
$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue
 
Invoke-Command -Session $s {    Get-AzureStackLog -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if($s)
{
    Remove-PSSession $s
}
```

- 매개 변수 **OutputSharePath** 및 **OutputShareCredential** 외부 공유 폴더에 로그를 업로드 하는 데 사용 됩니다.
- 이전 예에서 같이 **FromDate** 및 **ToDate** 특정 기간에 대 한 로그를 수집 하도록 매개 변수를 사용할 수 있습니다. 이 제공 될 수 있습니다 시나리오 같은 통합된 된 시스템에 업데이트 패키지를 적용 한 후 로그를 수집 하는 데 편리 합니다.


### <a name="to-run-get-azurestacklog-on-azure-stack-integrated-systems-version-1803-and-earlier"></a>통합 시스템 1803 이전 버전 Azure 스택에 AzureStackLog Get을 실행 하려면

통합된 된 시스템에서 로그 수집 도구를 실행 하려면 액세스 권한을 가진 끝점 (PEP)가 필요 합니다. 다음은 예제 스크립트는 PEP 통합된 된 시스템에 로그를 수집 하려면 사용 하 여 실행할 수 있습니다.

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
 
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
 
$shareCred = Get-Credential
 
$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue
 
Invoke-Command -Session $s {    Get-AzureStackLog -OutputPath "\\<HLH MACHINE ADDRESS>\c$\logs" -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if($s)
{
    Remove-PSSession $s
}
```

- PEP에서 로그를 수집 하는 경우 지정는 **OutputPath** 매개 변수를 하드웨어 수명 주기 호스트 (HLH) 컴퓨터에 있는 위치 여야 합니다. 또한 위치 암호화 되어 있는지 확인 합니다.
- 매개 변수 **OutputSharePath** 및 **OutputShareCredential** 선택 사항이 며 외부 공유 폴더에 로그를 업로드 하는 경우에 사용 됩니다. 이러한 매개 변수를 사용 하 여 *또한* 를 **OutputPath**합니다. 경우 **OutputPath** 를 지정 하지 않으면 로그 컬렉션 도구 PEP VM의 시스템 드라이브를 사용 하 여 저장소에 대 한 합니다. 드라이브 공간이 제한 되기 때문에 실패 하는 스크립트를 않을 수 있습니다.
- 이전 예에서 같이 **FromDate** 및 **ToDate** 특정 기간에 대 한 로그를 수집 하도록 매개 변수를 사용할 수 있습니다. 이 제공 될 수 있습니다 시나리오 같은 통합된 된 시스템에 업데이트 패키지를 적용 한 후 로그를 수집 하는 데 편리 합니다.


### <a name="parameter-considerations-for-both-asdk-and-integrated-systems"></a>ASDK와 통합 된 시스템에 대 한 매개 변수 고려 사항

- 경우는 **FromDate** 및 **ToDate** 매개 변수가 지정 되지 않은 경우, 기본적으로 지난 4 시간 동안 로그가 수집 됩니다.
- 사용할 수는 **TimeOutInMinutes** 매개 변수를 로그 컬렉션에 대 한 제한 시간을 설정 합니다. 기본적으로 150 (2.5 시간)에 설정 됩니다.

- 현재 사용할 수는 **FilterByRole** 매개 변수를 사용 하 여 다음과 같은 역할 필터 로그 컬렉션:

   |   |   |   |
   | - | - | - |
   | ACS                    | DeploymentMachine                | NC                         |
   | ACSBlob                | DiskRP                           | 네트워크                    |
   | ACSFabric              | 도메인                           | NonPrivilegedAppGateway    |
   | ACSFrontEnd            | ECE                              | NRP                        |
   | ACSMetrics             | ExternalDNS                      | OEM                        |
   | ACSMigrationService    | Fabric                           | PXE                        |
   | ACSMonitoringService   | FabricRing                       | SeedRing                   | 
   | ACSSettingsService     | FabricRingServices               | SeedRingServices           |
   | ACSTableMaster         | FRP                              | SLB                        |   
   | ACSTableServer         | 갤러리                          | SlbVips                    |
   | ACSWac                 | 게이트웨이                          | SQL                        |   
   | ADFS                   | HealthMonitoring                 | SRP                        |
   | ASAppGateway           | HRP                              | Storage                    |   
   | NCAzureBridge          | IBC                              | StorageAccounts            |    
   | AzurePackConnector     | IdentityProvider                 | StorageController          |  
   | AzureStackBitlocker    | Idn                             | 테넌트                     |
   | BareMetal              | InfraServiceController           | TraceCollector             |
   | BRP                    | 인프라                   | URP                        |
   | CA                     | KeyVaultAdminResourceProvider    | UsageBridge                |
   | 클라우드                  | KeyVaultControlPlane             | VirtualMachines            |
   | 프로비전                | KeyVaultDataPlane                | 했습니다.                        |
   | 컴퓨팅                | KeyVaultInternalControlPlane     | WASBootstrap               |
   | CPI                    | KeyVaultInternalDataPlane        | WASPUBLIC                  |
   | CRP                    | KeyVaultNamingService            |                            |
   | DatacenterIntegration  | MonitoringAgent                  |                            |
   |                        |                                  |                            |

### <a name="bkmk_gui"></a>그래픽 사용자 인터페이스를 사용 하 여 로그를 수집 합니다.
기본 Azure 스택 도구 GitHub 도구에서 리포지토리에 사용할 수 있는 오픈 소스 Azure 스택 도구를 활용할 수 Azure 스택 로그를 검색할 Get AzureStackLog cmdlet에 대 한 필수 매개 변수를 제공 하는 대신 http://aka.ms/AzureStackTools합니다.

**ERCS_AzureStackLogs.ps1** PowerShell 스크립트 GitHub 도구 저장소에 저장 되 고 정기적으로 업데이트 됩니다. 하는지를 확인 하기 위해 사용 가능한 최신 버전에서 직접 다운로드 해야 http://aka.ms/ERCS합니다. 관리자 PowerShell 세션에서 시작 스크립트가 권한 있는 끝점에 연결 하 고 제공 된 매개 변수를 사용 하 여 Get AzureStackLog를 실행 합니다. 매개 변수를 제공 하는 스크립트 기본적으로 그래픽 사용자 인터페이스를 통해 매개 변수에 대 한 메시지를 표시 합니다.

ERCS_AzureStackLogs.ps1 PowerShell 스크립트에 대 한 자세한 내용은 볼 수 있습니다 [짧은 동영상](https://www.youtube.com/watch?v=Utt7pLsXEBc) 스크립트의 보거나 [추가 정보 파일](https://github.com/Azure/AzureStack-Tools/blob/master/Support/ERCS_Logs/ReadMe.md) Azure 스택 도구 GitHub 리포지토리에 있는 합니다. 

### <a name="additional-considerations"></a>추가 고려 사항

* 이 명령은 다소 시간이 걸릴 수 로그를 수집 하는 어떤 역할에 따라 실행 합니다. 영향을 주는 요소는 로그 수집 및 Azure 스택 환경에 있는 노드의 숫자에 대해 지정 된 기간도 포함 됩니다.
* 컬렉션 실행 로그를 검사에서 만든 새 폴더는 **OutputSharePath** 명령에 지정 된 매개 변수입니다.
* 각 역할에는 개별 zip 파일 내의 해당 로그에 있습니다. 수집 된 로그의 크기에 따라 역할에는 해당 로그를 여러 개의 zip 파일에 분할 수 있을 수 있습니다. 이러한 역할에 대 한 단일 폴더에 압축을 푼 모든 로그 파일을 원하는 경우 (예: 7zip) 대량에서 압축을 풀 수 있는 도구를 사용 합니다. 역할의 경우 압축 된 파일을 모두 선택 하 고 선택 **여기 추출**합니다. 이 압축을 풀고 단일 병합 된 폴더에 해당 역할에 대 한 모든 로그 파일입니다.
* 이라는 파일 **Get AzureStackLog_Output.log** 압축 된 로그 파일이 포함 된 폴더에 만들어집니다. 이 파일은 로그 수집 하는 동안 문제 해결에 사용할 수 있는 명령 출력의 로그입니다.
* 특정 오류를 조사 하려면 둘 이상의 구성 요소에서 로그를 필요할 수 있습니다.
    -   시스템 및 인프라의 모든 Vm에 대 한 이벤트 로그에서 수집 되는 *VirtualMachines* 역할입니다.
    -   시스템 및 모든 호스트에 대 한 이벤트 로그에서 수집 되는 *BareMetal* 역할입니다.
    -   장애 조치 클러스터 및 Hyper-v 이벤트 로그에서 수집 되는 *저장소* 역할입니다.
    -   ACS 로그에서 수집 되는 *저장소* 및 *ACS* 역할입니다.

> [!NOTE]
> 크기 및 사용 기간 제한 로그 쇄도 얻도록 하지 않는 하기 위해 저장소 공간의 효율적인 사용률을 확인 하는 데 필수적 이므로 수집 된 로그에 적용 됩니다. 그러나 문제를 진단 하는 경우 로그는 이러한 제한 때문에 더 이상 존재 하지 않을 경우에 따라 필요 합니다. 즉, **매우 권장** (Azure의 저장소 계정, 추가 온-프레미스 저장 장치 등)는 외부 저장소 공간에 로그 오프 로드는 8-12 시간 마다 유지 있는 1 ~ 3 개월 동안에 따라 프로그램 요구 사항입니다. 또한이 저장 위치 암호화 되었는지 확인 합니다.

## <a name="next-steps"></a>다음 단계
[Microsoft Azure Stack 문제 해결](azure-stack-troubleshooting.md)

