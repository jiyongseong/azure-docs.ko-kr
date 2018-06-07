---
title: Azure Migrate 문제 해결 | Microsoft Docs
description: Azure Migrate 서비스의 알려진 문제에 대한 개요와 일반적인 오류 해결 방법을 설명합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: troubleshooting
ms.date: 05/15/2018
ms.author: raynew
ms.openlocfilehash: a878bab2bef31ff853dbad503a706e1a8d5803fe
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="troubleshoot-azure-migrate"></a>Azure Migrate 문제 해결

## <a name="troubleshoot-common-errors"></a>일반적인 오류 문제 해결

[Azure Migrate](migrate-overview.md)는 Azure로 마이그레이션하는 온-프레미스 워크로드를 평가합니다. Azure Migrate 배포 및 사용과 관련하여 문제가 발생하면 이 문서를 사용하여 해결하세요.


**수집기에서 인터넷에 연결할 수 없음**

이 문제는 사용 중인 컴퓨터가 프록시 뒤에 있는 경우 발생할 수 있습니다. 프록시에 필요한 경우 권한 부여 자격 증명을 제공해야 합니다.
URL 기반 방화벽 프록시를 사용하여 아웃바운드 연결을 제어하는 경우 다음과 같은 필수 URL을 허용 목록에 포함해야 합니다.

**URL** | **목적**  
--- | ---
*.portal.azure.com | Azure 서비스와의 연결을 확인하고 시간 동기화 문제의 유효성을 검사하는 데 필요합니다.
*.oneget.org | powershell 기반 vCenter PowerCLI 모듈을 다운로드하는 데 필요합니다.

**포털에서 복사한 프로젝트 ID 및 키를 사용하여 수집기로 프로젝트에 연결할 수 없습니다.**

올바른 정보를 복사하여 붙여넣었는지 확인합니다. 문제를 해결하려면 다음과 같이 MMA(Microsoft Monitoring Agent)를 설치하고 MMA에서 프로젝트에 연결할 수 있는지 확인합니다.

1. 수집기 VM에서 [MMA](https://go.microsoft.com/fwlink/?LinkId=828603)를 다운로드합니다.
2. 설치를 시작하려면 다운로드한 파일을 두 번 클릭합니다.
3. 설치 화면의 **시작** 페이지에서 **다음**을 클릭합니다. **사용 조건** 페이지에서 **동의**를 클릭하여 라이선스를 수락합니다.
4. **대상 폴더**에서 기본 설치 폴더를 유지하거나 수정하고 **다음**을 클릭합니다.
5. **에이전트 설치 옵션**에서 **Azure Log Analytics** > **다음**을 차례로 선택합니다.
6. **추가**를 클릭하여 새로운 Log Analytics 작업 영역을 추가합니다. 복사한 프로젝트 ID와 키를 붙여넣습니다. 그런 후 **Next** 를 클릭합니다.
7. 에이전트가 프로젝트에 연결할 수 있는지 확인합니다. 연결할 수 없으면 설정을 확인합니다. 에이전트는 연결할 수는 있지만 수집기는 연결할 수 없는 경우 지원에 문의합니다.


**오류 802: 날짜 및 시간 동기화 오류가 발생합니다.**

서버 시계가 현재 시간과 동기화되지 않아 5분 이상 차이가 벌어질 수 있습니다. 다음과 같이 VM 수집기에서 시계 시간을 현재 시간과 일치하도록 변경합니다.

1. VM에서 관리자 명령 프롬프트를 엽니다.
2. 표준 시간대를 확인하려면 w32tm /tz 명령을 실행합니다.
3. 시간을 동기화하려면 w32tm /resync 명령을 실행합니다.

**프로젝트 키의 끝부분에 “==” 기호가 있습니다. 이러한 기호가 수집기에서 다른 영숫자 문자로 인코딩됩니다. 이것은 예상된 동작인가요?**

예, 모든 프로젝트 키는 "==" 기호로 끝납니다. 수집기는 프로젝트 키를 암호화한 후 처리합니다.

**디스크 및 네트워크 어댑터의 성능 데이터가 0을 표시합니다.**

vCenter server의 통계 설정 수준이 3 미만으로 설정되면 이 현상이 발생할 수 있습니다. 3 이상에서는 vCenter가 VM의 계산, 저장소 및 네트워크 성능 기록을 저장합니다. 3 미만에서는 vCenter가 저장소 및 네트워크 데이터를 저장하지 않고 CPU 및 메모리 데이터만 저장합니다. 이 시나리오에서는 Azure Migrate 에서 성능 데이터가 0으로 표시되고, Azure Migrate는 온-프레미스 컴퓨터에서 수집된 메타데이터를 기반으로 디스크 및 네트워크에 대한 권장 크기를 제공합니다.

디스크 및 네트워크 성능 데이터 수집을 사용하려면 통계 설정 수준을 3으로 변경하세요. 그런 다음 환경을 검색하고 평가를 마칠 때까지 하루 이상 기다립니다.

**에이전트를 설치하고 종속성 시각화 그룹을 사용하여 그룹을 만들었습니다. 장애 조치(failover) 이후, 컴퓨터가 "종속성 보기" 대신 "에이전트 설치" 작업을 표시합니다.**
* 계획된 또는 계획되지 않은 장애 조치(failover) 후에는 온-프레미스 컴퓨터가 꺼지고 동급 컴퓨터가 Azure에 생성됩니다. 이러한 컴퓨터는 다른 MAC 주소를 갖습니다. 사용자가 온-프레미스 IP 주소를 유지하는지 여부에 따라 다른 IP 주소를 가질 수도 있습니다. MAC 및 IP 주소가 모두 다르면 Azure Migrate는 온-프레미스 컴퓨터를 서비스 맵 종속성 데이터와 연결하지 않으며, 종속성을 보는 대신 사용자에게 에이전트를 설치하라고 요청합니다.
* 테스트 장애 조치(failover) 후에도 온-프레미스 컴퓨터는 예상대로 계속 켜져 있습니다. Azure에서 생성된 동급 컴퓨터는 다른 MAC 주소를 획득하며 다른 IP 주소를 획득할 수도 있습니다. 사용자가 이러한 컴퓨터에서 나가는 Log Analytics 트래픽을 차단하지 않는 이상, Azure Migrate는 온-프레미스 컴퓨터를 서비스 맵 종속성 데이터와 연결하지 않으며, 종속성을 보는 대신 사용자에게 에이전트를 설치하라고 요청합니다.


## <a name="troubleshoot-readiness-issues"></a>준비 관련 문제 해결

**문제** | **해결**
--- | ---
지원되지 않는 부팅 유형 | Azure는 공유 EFI 부팅 유형의 VM을 지원하지 않습니다. 마이그레이션을 실행하기 전에 부팅 유형을 BIOS로 변환하는 것이 좋습니다. <br/><br/>[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-migrate-on-premises-to-azure)를 사용하여 마이그레이션하는 동안 VM의 부팅 유형이 BIOS로 변환될 때 이러한 VM의 마이그레이션을 수행할 수 있습니다.
조건부로 지원되는 Windows OS | OS의 지원 종료 날짜가 경과되어, [Azure에서 지원되기](https://aka.ms/WSosstatement) 위해 CSA(사용자 지정 지원 계약)가 필요한 경우 Azure로 마이그레이션하기 전에 OS를 업그레이드하는 것이 좋습니다.
지원되지 않는 Windows OS | Azure는 [선택된 Windows OS 버전](https://aka.ms/WSosstatement)만 지원하므로 Azure로 마이그레이션하기 전에 컴퓨터의 OS를 업그레이드하는 것이 좋습니다.
조건부로 보증되는 Linux OS | Azure는 [선택된 Linux OS 버전](../virtual-machines/linux/endorsed-distros.md)만 보증하므로 Azure로 마이그레이션하기 전에 컴퓨터의 OS를 업그레이드하는 것이 좋습니다.
보증되지 않는 Linux OS | 컴퓨터를 Azure에서 부팅할 수 있지만 Azure에서 OS 지원을 제공하지 않습니다. Azure로 마이그레이션하기 전에 OS를 [보증된 Linux 버전](../virtual-machines/linux/endorsed-distros.md)으로 업그레이드하는 것이 좋습니다.
알 수 없는 운영 체제 | Azure Migrate가 VM의 Azure 준비 상태를 식별할 수 없으므로 VM의 운영 체제가 vCenter 서버에서 '기타'로 지정되었습니다. 컴퓨터를 마이그레이션하기 전에 컴퓨터 내에서 실행되는 OS가 Azure에서 [지원](https://aka.ms/azureoslist)되는지 확인합니다.
지원되지 않는 OS 비트 | 32비트 OS가 있는 VM은 Azure에서 부팅될 수 있지만, Azure로 마이그레이션하기 전에 VM의 OS를 32비트에서 64비트로 업그레이드하는 것이 좋습니다.
Visual Studio 구독이 필요합니다. | 컴퓨터에 Visual Studio 구독에서만 지원되는 Windows 클라이언트 OS가 실행되고 있습니다.
필요한 저장소 성능을 제공하는 VM을 찾을 수 없습니다. | 컴퓨터에 필요한 저장소 성능(IOPS/처리량)이 Azure VM 지원을 초과합니다. 마이그레이션을 시작하기 전에 컴퓨터의 저장소 요구 사항을 낮춰봅니다.
필요한 네트워크 성능을 제공하는 VM을 찾을 수 없습니다. | 컴퓨터에 필요한 네트워크 성능(입력/출력)이 Azure VM 지원을 초과합니다. 컴퓨터의 네트워킹 요구 사항을 낮춰봅니다.
지정된 가격 책정 계층에서 VM을 찾을 수 없습니다. | 가격 책정 계층을 표준으로 설정하는 경우 Azure로 마이그레이션하기 전에 VM을 다운사이징하는 것이 좋습니다. 가격 책정 계층이 기본이면 평가의 가격 책정 계층을 표준으로 변경하는 것이 좋습니다.
지정된 위치에서 VM을 찾을 수 없습니다. | 다른 대상 위치를 사용하여 마이그레이션을 실행해 봅니다.
부적합한 디스크가 하나 이상 있습니다. | VM에 연결된 하나 이상의 디스크가 Azure 요구 사항을 충족하지 않습니다. VM에 연결된 각 디스크에 대해 디스크 크기가 4TB 미만인지 확인합니다. 4TB 미만이 아니면 Azure로 마이그레이션하기 전에 디스크 크기를 축소합니다. 각 디스크에 필요한 성능(IOPS/처리량)이 Azure [관리 가상 머신 디스크](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits)에서 지원되는지 확인합니다.   
부적합한 네트워크 어댑터가 하나 이상 있습니다. | 마이그레이션을 시작하기 전에 컴퓨터에서 사용하지 않는 네트워크 어댑터를 제거합니다.
디스크 수 한도 초과 | 마이그레이션을 시작하기 전에 컴퓨터에서 사용하지 않는 디스크를 제거합니다.
디스크 크기 한도 초과 | Azure는 4TB 이하의 디스크를 지원합니다. 마이그레이션을 시작하기 전에 디스크를 4TB 미만으로 축소합니다.
지정된 위치의 디스크를 사용할 수 없음 | 마이그레이션하기 전에 디스크가 대상 위치에 있는지 확인합니다.
지정된 이중화에 디스크를 사용할 수 없음 | 디스크가 평가 설정에 정의된 중복 저장소 형식(기본적으로 LRS)을 사용해야 합니다.
내부 오류로 인해 디스크 적합성을 확인할 수 없음 | 그룹에 대한 새 평가를 만들어 봅니다.
필요한 코어 및 메모리가 포함된 VM을 찾을 수 없음 | Azure가 적합한 종류의 VM을 찾을 수 없습니다. 마이그레이션하기 전에 온-프레미스 컴퓨터의 메모리 및 코어 수를 줄여봅니다.
내부 오류로 인해 VM 적합성을 확인할 수 없습니다. | 그룹에 대한 새 평가를 만들어 봅니다.
내부 오류로 인해 하나 이상의 디스크 적합성을 확인할 수 없습니다. | 그룹에 대한 새 평가를 만들어 봅니다.
내부 오류로 인해 하나 이상의 네트워크 어댑터 적합성을 확인할 수 없습니다. | 그룹에 대한 새 평가를 만들어 봅니다.


## <a name="collect-logs"></a>로그 수집

**수집기 VM에서 로그를 수집하는 방법**

기본적으로 로깅을 사용하도록 설정됩니다. 로그는 다음과 같은 위치에 있습니다.

- C:\Profiler\ProfilerEngineDB.sqlite
- C:\Profiler\Service.log
- C:\Profiler\WebApp.log

Windows용 이벤트 추적을 수집하려면 다음 단계를 수행합니다.

1. 수집기 VM에서 PowerShell 명령 창을 엽니다.
2. **Get-EventLog -LogName Application | export-csv eventlog.csv** 명령을 실행합니다.

**포털 네트워크 트래픽 로그는 어떻게 수집하나요?**

1. 브라우저를 열고 [포털](https://portal.azure.com)을 탐색하여 로그인합니다.
2. F12 키를 눌러 개발자 도구를 시작합니다. 필요한 경우 **탐색에 대한 항목 지우기** 설정을 선택 취소합니다.
3. **네트워크** 탭을 클릭하고 네트워크 트래픽 캡처를 시작합니다.
 - 크롬에서 **Preserve log**를 클릭합니다. 자동으로 기록이 시작됩니다. 빨간색 원은 트래픽을 캡처하고 있다는 뜻입니다. 빨간색 원이 나타나지 않으면 검은색 원을 클릭하여 시작합니다.
 - Edge/IE에서는 자동으로 기록이 시작됩니다. 자동으로 시작되지 않으면 녹색 재생 단추를 클릭합니다.
4. 오류를 재현해 봅니다.
5. 기록하는 동안 오류가 발생하면 기록을 중지하고 기록된 활동의 복사본을 저장합니다.
 - 크롬에서는 마우스 오른쪽 단추로 클릭하고 **Save as HAR with content**를 클릭합니다. 그러면 로그가 .har 파일로 압축되어 내보내집니다.
 - Edge/IE에서는 **캡처된 트래픽 내보내기** 아이콘을 클릭합니다. 그러면 로그가 압축되어 내보내집니다.
6. **콘솔** 탭으로 이동하여 경고 또는 오류를 확인합니다. 콘솔 로그를 저장하려면:
 - 크롬의 경우 콘솔 로그에서 아무 위치를 마우스 오른쪽 단추로 클릭합니다. **다른 이름으로 저장**을 선택하여 로그를 내보내고 압축합니다.
 - Edge/IE의 경우 오류를 마우스 오른쪽 단추로 클릭하고 **모두 복사**를 선택합니다.
7. 개발자 도구를 닫습니다.


## <a name="vcenter-errors"></a>vCenter 오류

### <a name="error-unhandledexception-internal-error-occured-systemiofilenotfoundexception"></a>오류 UnhandledException 내부 오류가 발생했습니다. System.IO.FileNotFoundException

1.0.9.5 미만인 수집기 버전에서 표시되는 오류입니다. 수집기 버전 1.0.9.2 또는 1.0.8.59와 같은 이전 GA 버전을 사용하는 경우 이 문제가 발생합니다. [자세한 응답은 포럼에 지정된 링크](https://social.msdn.microsoft.com/Forums/azure/en-US/c1f59456-7ba1-45e7-9d96-bae18112fb52/azure-migrate-connect-to-vcenter-server-error?forum=AzureMigrate)를 따릅니다.

[문제를 해결하기 위해 수집기 업그레이드](https://aka.ms/migrate/col/checkforupdates)

### <a name="error-unabletoconnecttoserver"></a>오류 UnableToConnectToServer

오류로 인해 vCenter Server “Servername.com:9443”에 연결할 수 없습니다: 메시지를 수락할 수 있는, https://Servername.com:9443/sdk에서 수신 대기 중인 끝점이 없습니다.

수집기 컴퓨터가 지정된 vCenter Server 이름을 확인할 수 없거나 지정된 포트가 잘못된 경우에 발생합니다. 기본적으로 포트가 지정되지 않은 경우 수집기는 포트 번호 443에 연결하려고 합니다.

1. 수집기 컴퓨터에서 Servername.com를 ping해 봅니다.
2. 1단계가 실패하는 경우 IP 주소를 통해 vCenter Server에 연결해보세요.
3. vCenter에 연결할 정확한 포트 번호를 식별합니다.
4. 마지막으로 vCenter Server가 실행 중인지 확인합니다.

## <a name="collector-error-codes-and-recommended-actions"></a>수집기 오류 코드 및 권장 작업

|           |                                |                                                                               |                                                                                                       |                                                                                                                                            | 
|-----------|--------------------------------|-------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------| 
| 오류 코드 | 오류 이름                      | Message                                                                       | 가능한 원인                                                                                        | 권장 작업                                                                                                                          | 
| 601       | CollectorExpired               | 수집기가 만료되었습니다.                                                        | 수집기가 만료되었습니다.                                                                                    | 새 버전의 수집기를 다운로드하고 다시 시도하세요.                                                                                      | 
| 751       | UnableToConnectToServer        | 오류로 인해 vCenter Server ‘%Name;’에 연결할 수 없습니다. %ErrorMessage;     | 자세한 내용은 오류 메시지를 확인하세요.                                                             | 문제를 해결하고 다시 시도하세요.                                                                                                           | 
| 752       | InvalidvCenterEndpoint         | 서버 ‘%Name;’이(가) vCenter Server가 아닙니다.                                  | vCenter Server 세부 정보를 제공하세요.                                                                       | 올바른 vCenter Server 세부 정보를 사용하여 작업을 다시 시도하세요.                                                                                   | 
| 753       | InvalidLoginCredentials        | 오류로 인해 vCenter Server ‘%Name;’에 연결할 수 없습니다. %ErrorMessage; | 잘못된 로그인 자격 증명으로 인해 vCenter Server에 연결하지 못했습니다.                             | 제공된 로그인 자격 증명 정보가 올바른지 확인하세요.                                                                                    | 
| 754       | NoPerfDataAvaialable           | 성능 데이터를 사용할 수 없습니다.                                               | vCenter Server에서 통계 수준을 확인하세요. 성능 데이터를 사용하려면 3으로 설정해야 합니다. | 통계 수준을 3(5분, 30분, 2시간 동안)으로 변경하고 하루 이상 기다린 후 시도하세요.                   | 
| 756       | NullInstanceUUID               | null InstanceUUID가 있는 컴퓨터를 발견했습니다.                                  | vCenter Server에 부적절한 개체가 있을 수 있습니다.                                                      | 문제를 해결하고 다시 시도하세요.                                                                                                           | 
| 757       | VMNotFound                     | 가상 머신이 없습니다.                                                  | 가상 머신이 삭제되었을 수 있습니다. %VMID;                                                                | vCenter 인벤토리의 범위를 지정하는 동안 선택한 가상 머신이 검색 중에 있는지 확인하세요.                                      | 
| 758       | GetPerfDataTimeout             | VCenter 요청 시간이 초과되었습니다. 메시지 %Message;                                  | vCenter Server 자격 증명이 잘못되었습니다.                                                              | vCenter Server 자격 증명을 검사하고 vCenter Server에 연결할 수 있는지 확인하세요. 작업을 다시 시도하세요. 문제가 지속되면 고객 지원 팀에 문의하세요. | 
| 759       | VmwareDllNotFound              | VMWare.Vim DLL이 없습니다.                                                     | PowerCLI가 올바르게 설치되지 않았습니다.                                                                   | PowerCLI가 올바르게 설치되었는지 확인하세요. 작업을 다시 시도하세요. 문제가 지속되면 고객 지원 팀에 문의하세요.                               | 
| 800       | ServiceError                   | Azure Migrate Collector 서비스가 실행되고 있지 않습니다.                               | Azure Migrate Collector 서비스가 실행되고 있지 않습니다.                                                       | services.msc를 사용하여 서비스를 시작하고 작업을 재시도하세요.                                                                             | 
| 801       | PowerCLIError                  | VMware PowerCLI 설치에 실패했습니다.                                          | VMware PowerCLI 설치에 실패했습니다.                                                                  | 작업을 다시 시도하세요. 문제가 지속되면 수동으로 설치하고 작업을 다시 시도하세요.                                                   | 
| 802       | TimeSyncError                  | 시간이 인터넷 시간 서버와 동기화되어 있지 않습니다.                            | 시간이 인터넷 시간 서버와 동기화되어 있지 않습니다.                                                    | 컴퓨터 시간이 컴퓨터의 표준 시간대에 맞게 정확하게 설정되었는지 확인하고 작업을 다시 시도하세요.                                 | 
| 702       | OMSInvalidProjectKey           | 잘못된 프로젝트 키가 지정되었습니다.                                                | 잘못된 프로젝트 키가 지정되었습니다.                                                                        | 올바른 프로젝트 키로 작업을 다시 시도하세요.                                                                                              | 
| 703       | OMSHttpRequestException        | 요청을 보내는 동안 오류가 발생했습니다. 메시지 %Message;                                | 프로젝트 ID 및 키를 검사하고 끝점에 연결할 수 있는지 확인하세요.                                       | 작업을 다시 시도하세요. 문제가 지속되면 Microsoft 지원에 문의하세요.                                                                     | 
| 704       | OMSHttpRequestTimeoutException | HTTP 요청 시간이 초과되었습니다. 메시지 %Message;                                     | 프로젝트 ID 및 키를 검사하고 끝점에 연결할 수 있는지 확인하세요.                                       | 작업을 다시 시도하세요. 문제가 지속되면 Microsoft 지원에 문의하세요.                                                                     | 
