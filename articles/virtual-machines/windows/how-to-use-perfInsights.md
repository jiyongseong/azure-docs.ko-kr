---
title: Microsoft Azure에서 PerfInsights를 사용하는 방법 | Microsoft Docs
description: PerfInsights를 사용하여 Windows VM 성능 문제를 해결하는 방법을 설명합니다.
services: virtual-machines-windows'
documentationcenter: ''
author: genlin
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: cac17b5f3ee730bf1f56dbfd05b6c6d3b02c891f
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="how-to-use-perfinsights"></a>PerfInsights를 사용하는 방법 

[PerfInsights](http://aka.ms/perfinsightsdownload)는 진단 데이터를 수집 및 분석하고, Azure에서 Windows 가상 머신 성능 문제를 해결하는 데 도움이 되는 보고서를 제공하는 자체 진단 도구입니다. PerfInsights는 [Azure Performance Diagnostics VM 확장](performance-diagnostics-vm-extension.md)을 설치하여 포털에서 직접 또는 독립 실행형 스크립트로 가상 머신에서 실행될 수 있는 도구입니다.

가상 머신 성능 문제가 있는 경우 기술 지원에 문의하기 전에 이 도구를 실행합니다.

## <a name="supported-troubleshooting-scenarios"></a>지원되는 문제 해결 시나리오

PerfInsights는 여러 종류의 정보를 수집하고 분석할 수 있습니다. 다음 섹션에서 일반적인 시나리오를 설명합니다.

### <a name="collect-basic-configuration"></a>기본 구성 수집 

이 시나리오에서는 다음을 포함하여 디스크 구성 및 기타 중요한 정보를 수집합니다.

-   이벤트 로그

-   모든 들어오는 연결과 나가는 연결에 대한 네트워크 상태

-   네트워크 및 방화벽 구성 설정

-   현재 시스템에서 실행 중인 모든 응용 프로그램의 작업 목록

-   Microsoft SQL Server 데이터베이스 구성 설정(VM이 SQL Server를 실행하는 서버로 식별된 경우)

-   저장소 안정성 카운터

-   중요한 Windows 핫픽스

-   설치된 필터 드라이버

이 모음은 시스템에 영향을 주지 않아야 하는 수동적인 정보 모음입니다. 

>[!Note]
>이 시나리오는 다음과 같은 각각의 시나리오에 자동으로 포함됩니다.

### <a name="benchmarking"></a>벤치마킹

이 시나리오에서는 VM에 연결된 모든 드라이브에 대해 [Diskspd](https://github.com/Microsoft/diskspd) 벤치마크 테스트(IOPS 및 MBPS)를 실행합니다. 

> [!Note]
> 이 시나리오는 시스템에 영향을 줄 수 있으므로 라이브 프로덕션 시스템에서 실행하면 안됩니다. 필요한 경우 문제가 발생하지 않도록 전용 유지 관리 창에서 이 시나리오를 실행합니다. 추적 또는 벤치마크 테스트로 인해 워크로드가 증가하면 VM 성능이 저하될 수 있습니다.
>

### <a name="slow-vm-analysis"></a>느린 VM 분석 

이 시나리오에서는 RuleEngineConfig.json 파일에 지정된 카운터를 사용하여 [성능 카운터](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) 추적을 실행합니다. VM이 SQL Server를 실행하는 서버로 식별되면 성능 카운터 추적을 실행합니다. RuleEngineConfig.json 파일에 있는 카운터를 사용하여 이 작업을 수행합니다. 이 시나리오에는 성능 진단 데이터도 포함됩니다.

### <a name="azure-files-analysis"></a>Azure Files 분석 

이 시나리오에서는 네트워크 추적과 함께 특별한 성능 카운터 캡처를 실행합니다. 캡처에는 모든 SMB(Server Message Block) 클라이언트 공유 카운터가 포함됩니다. 다음은 캡처에 포함되는 몇 가지 주요 SMB 클라이언트 공유 성능 카운터입니다.

| **형식**     | **SMB 클라이언트 공유 카운터** |
|--------------|-------------------------------|
| IOPS         | 데이터 요청 수/초             |
|              | 읽기 요청 수/초             |
|              | 쓰기 요청 수/초            |
| 대기 시간      | 평균 초/데이터 요청         |
|              | 평균 초/읽기                 |
|              | 평균 초/쓰기                |
| IO 크기      | 평균 바이트 수/데이터 요청       |
|              | 평균 바이트 수/읽기               |
|              | 평균 바이트 수/쓰기              |
| 처리량   | 데이터 바이트 수/쓰기                |
|              | 읽기 바이트 수/초                |
|              | 쓰기 바이트 수/초               |
| 큐 길이 | 평균 읽기 큐 길이        |
|              | 평균 쓰기 큐 길이       |
|              | 평균 데이터 큐 길이        |

### <a name="custom-slow-vm-analysis"></a>사용자 지정 느린 VM 분석 

사용자 지정 느린 VM 분석을 실행할 때는 병렬 실행되도록 추적을 선택합니다. 원한다면 전체(성능 카운터, Xperf, 네트워크 및 StorPort)를 실행할 수 있습니다.  

> [!Note]
> 이 시나리오는 시스템에 영향을 줄 수 있으므로 라이브 프로덕션 시스템에서 실행하면 안됩니다. 필요한 경우 문제가 발생하지 않도록 전용 유지 관리 창에서 이 시나리오를 실행합니다. 추적 또는 벤치마크 테스트로 인해 워크로드가 증가하면 VM 성능이 저하될 수 있습니다.
>

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>PerfInsights에서 수집하는 정보 유형

Windows VM, 디스크 또는 저장소 풀 구성, 성능 카운터, 로그 및 다양한 추적과 관련된 정보가 수집됩니다. 사용하는 성능 시나리오에 따라 다릅니다. 다음 표에서 자세한 내용을 제공합니다.

|수집되는 데이터                              |  |  | 성능 시나리오 |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                               | 기본 구성 수집 | 벤치마킹 | 느린 VM 분석 | Azure Files 분석 | 사용자 지정 느린 VM 분석 |
| 이벤트 로그의 정보       | 예                        | 예                                | 예                      | 예                  | 예                  |
| 시스템 정보                | 예                        | 예                                | 예                      | 예                  | 예                  |
| 볼륨 매핑                        | 예                        | 예                                | 예                      | 예                  | 예                  |
| 디스크 매핑                          | 예                        | 예                                | 예                      | 예                  | 예                  |
| 실행 중인 작업                     | 예                        | 예                                | 예                      | 예                  | 예                  |
| 저장소 안정성 카운터      | 예                        | 예                                | 예                      | 예                  | 예                  |
| 저장소 정보               | 예                        | 예                                | 예                      | 예                  | 예                  |
| Fsutil 출력                     | 예                        | 예                                | 예                      | 예                  | 예                  |
| 필터 드라이버 정보                | 예                        | 예                                | 예                      | 예                  | 예                  |
| Netstat 출력                    | 예                        | 예                                | 예                      | 예                  | 예                  |
| 네트워크 구성             | 예                        | 예                                | 예                      | 예                  | 예                  |
| 방화벽 구성            | 예                        | 예                                | 예                      | 예                  | 예                  |
| SQL Server 구성          | 예                        | 예                                | 예                      | 예                  | 예                  |
| 성능 진단 추적 *  | 예                        | 예                                | 예                      | 예                  | 예                  |
| 성능 카운터 추적 **      |                            |                                    | 예                      |                      | 예                  |
| SMB 카운터 추적 **              |                            |                                    |                          | 예                  |                      |
| SQL Server 카운터 추적 **       |                            |                                    | 예                      |                      | 예                  |
| XPerf 추적                       |                            |                                    |                          |                      | 예                  |
| StorPort 추적                    |                            |                                    |                          |                      | 예                  |
| 네트워크 추적                     |                            |                                    |                          | 예                  | 예                  |
| Diskspd 벤치마크 추적 ***       |                            | 예                                |                          |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>성능 진단 추적 (*)

백그라운드에서 규칙 기반 엔진을 실행하여 데이터를 수집하고 지속적인 성능 문제를 진단합니다. 현재 지원되는 규칙은 다음과 같습니다.

- HighCpuUsage 규칙: 높은 CPU 사용량 기간을 검색하고 해당 기간 동안의 최고 CPU 사용량 소비자를 표시합니다.
- HighDiskUsage 규칙: 실제 디스크에 대한 높은 디스크 사용량 기간을 검색하고 해당 기간 동안의 최고 디스크 사용량 소비자를 표시합니다.
- HighResolutionDiskMetric 규칙: 각 실제 디스크에 대한 50밀리초당 IOPS, 처리량 및 IO 대기 시간 메트릭을 표시합니다. 디스크 제한 기간을 빠르게 식별하는 데 유용합니다.
- HighMemoryUsage 규칙: 높은 메모리 사용 기간을 검색하고 해당 기간 동안의 최고 메모리 사용량 소비자를 표시합니다.

> [!NOTE] 
> 현재 .NET Framework 3.5 이상 버전이 포함된 Windows 버전이 지원됩니다.

### <a name="performance-counter-trace-"></a>성능 카운터 추적 (\*\*)

다음 성능 카운터를 수집합니다.

- \Process, \Processor, \Memory, \Thread, \PhysicalDisk 및 \LogicalDisk
- \Cache\Dirty Pages, \Cache\Lazy Write Flushes/sec, \Server\Pool Nonpaged, Failures 및 \Server\Pool Paged Failures
- \Network Interface, \IPv4\Datagrams, \IPv6\Datagrams, \TCPv4\Segments, \TCPv6\Segments,  \Network Adapter, \WFPv4\Packets, \WFPv6\Packets, \UDPv4\Datagrams, \UDPv6\Datagrams, \TCPv4\Connection, \TCPv6\Connection, \Network QoS Policy\Packets, \Per Processor Network Interface Card Activity 및 \Microsoft Winsock BSP에서 카운터를 선택합니다.

#### <a name="for-sql-server-instances"></a>SQL Server 인스턴스의 경우
- \SQL Server:Buffer Manager, \SQLServer:Resource Pool Stats 및 \SQLServer:SQL Statistics\
- \SQLServer:Locks, \SQLServer:General, Statistics
- \SQLServer:Access Methods

#### <a name="for-azure-files"></a>Azure Files의 경우
\SMB Client Shares

### <a name="diskspd-benchmark-trace-"></a>Diskspd 벤치마크 추적 (***)
Diskspd IO 워크로드 테스트(OS 디스크[쓰기] 및 풀 드라이브[읽기/쓰기])

## <a name="run-the-perfinsights-tool-on-your-vm"></a>VM에서 PerfInsights 도구 실행

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>도구를 실행하기 전에 알고 있어야 하는 사항 

#### <a name="tool-requirements"></a>도구 요구 사항

-  이 도구는 성능 문제가 있는 VM에서 실행해야 합니다. 

-  지원되는 운영 체제는 Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 및 Windows Server 2016, Windows 8.1과 Windows 10입니다.

#### <a name="possible-problems-when-you-run-the-tool-on-production-vms"></a>프로덕션 VM에서 도구 실행 시 발생할 수 있는 문제

-  Xperf 또는 Diskspd를 사용하도록 구성된 벤치마킹 시나리오 또는 "사용자 지정 느린 VM 분석" 시나리오를 사용하는 경우 이 도구로 인해 VM의 성능이 저하될 수 있습니다. 이러한 시나리오는 프로덕션 환경에서 실행해서는 안 됩니다.

-  DiskSpd를 사용하도록 구성된 벤치마킹 시나리오 또는 "사용자 지정 느린 VM 분석" 시나리오의 경우, 다른 백그라운드 작업이 I/O 워크로드를 방해하지 않는지 확인합니다.

-  기본적으로 이 도구는 임시 저장소 드라이브를 사용하여 데이터를 수집합니다. 추적을 더 오랫동안 사용하도록 유지하면 수집되는 데이터의 양이 적절할 수 있습니다. 이렇게 하면 임시 디스크의 공간 가용성을 낮출 수 있으므로 이 드라이브를 사용하는 모든 응용 프로그램에 영향을 미칠 수 있습니다.

### <a name="how-do-i-run-perfinsights"></a>PerfInsights를 실행하는 방법 

PerfInsights는 [Azure Performance Diagnostics VM 확장](performance-diagnostics-vm-extension.md)을 설치하여 가상 머신에서 실행할 수 있습니다. 독립 실행형 도구로도 실행할 수 있습니다. 

**Azure Portal에서 PerfInsights 설치 및 실행**

이 옵션에 대한 자세한 내용은 [Azure Performance Diagnostics VM 확장 설치](performance-diagnostics-vm-extension.md#install-the-extension)를 참조하세요.  

**독립 실행형 모드에서 PerfInsights 실행**

PerfInsights 도구를 실행하려면 다음 단계를 수행합니다.


1. [PerfInsights.zip](http://aka.ms/perfinsightsdownload)을 다운로드합니다.

2. PerfInsights.zip 파일의 차단을 해제합니다. 이렇게 하려면 PerfInsights.zip 파일을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다. **일반** 탭에서 **차단 해제**를 선택한 다음 **확인**을 선택합니다. 이렇게 하면 추가 보안을 요구하는 메시지가 표시되지 않고 이 도구가 실행됩니다.  

    ![차단 해제가 강조 표시된 PerfInsights 스크린샷](media/how-to-use-perfInsights/unlock-file.png)

3.  압축된 PerfInsights.zip 파일을 임시 드라이브(보통은 기본적으로 D 드라이브)로 확장합니다. 

4.  관리자 권한으로 Windows 명령 프롬프트를 연 다음, PerfInsights.exe를 실행하여 사용 가능한 명령줄 매개 변수를 표시합니다.

    ```
    cd <the path of PerfInsights folder>
    PerfInsights
    ```
    ![PerfInsights 명령줄 출력 스크린샷](media/how-to-use-perfInsights/PerfInsightsCommandline.png)
    
    PerfInsights 시나리오를 실행하기 위한 기본 구문은 다음과 같습니다.
    
    ```
    PerfInsights /run <ScenarioName> [AdditionalOptions]
    ```

    아래 예제를 사용하여 VM 시나리오를 5분 동안 느리게 실행합니다.
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    다음 예제를 사용하여 5분 동안 Xperf 및 성능 카운터 추적을 사용해서 사용자 지정 시나리오를 실행할 수 있습니다.
    
    ```
    PerfInsights /run custom xp /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    **/list** 명령을 사용하여 사용 가능한 모든 시나리오와 옵션을 조회할 수 있습니다.
    
    ```
    PerfInsights /list
    ```

    >[!Note]
    >시나리오를 실행하기 전에 PerfInsights는 사용자가 진단 정보 공유 및 EULA에 동의할 것을 요청합니다. 이러한 프롬프트를 건너뛰려면 **/AcceptDisclaimerAndShareDiagnostics** 옵션을 사용합니다. 
    >
    >Microsoft에 대한 활성 지원 티켓이 있고, 함께 작업 중인 지원 엔지니어의 요청에 따라 실행 중인 PerfInsights가 있으면 **/sr** 옵션을 사용하여 지원 티켓 번호를 제공해야 합니다.
    >
    >기본적으로 PerfInsights는 사용 가능한 최신 버전으로 자체적으로 업데이트를 시도합니다. 자동 업데이트를 건너뛰려면 **/SkipAutoUpdate** 또는 **/sau** 매개 변수를 사용합니다.  
    >
    >기간 스위치 **/d**를 지정하지 않으면 PerfInsights는 vmslow, azurefiles 및 사용자 지정 시나리오를 실행하면서 해당 문제를 재현하도록 요구합니다. 

추적 또는 작업이 완료되면 PerfInsights와 동일한 폴더에 새 파일이 나타납니다. 이 파일의 이름은 **CollectedData\_yyyy-MM-dd\_hh-mm-ss-fff.zip**입니다. 분석을 위해 지원 담당자에게 이 파일을 전송하거나 zip 파일 내의 보고서를 열어 결과 및 권장 사항을 검토할 수 있습니다.

## <a name="review-the-diagnostics-report"></a>진단 보고서 검토

**CollectedData\_yyyy-MM-dd\_hh-mm-ss-fff.zip** 파일에는 PerfInsights가 찾은 결과를 자세히 보여 주는 HTML 보고서가 있습니다. 보고서를 검토하려면 **CollectedData\_yyyy-MM-dd\_hh-mm-ss-fff.zip** 파일을 펼친 다음, **PerfInsights Report.html** 파일을 엽니다.

**검색 결과** 탭을 선택합니다.

![PerfInsights 보고서 스크린샷](media/how-to-use-perfInsights/findingtab.png)
![PerfInsights 보고서 스크린샷](media/how-to-use-perfInsights/findings.PNG)

> [!NOTE] 
> 위험으로 분류된 검색 결과는 성능 문제를 일으킬 수 있는 알려진 문제입니다. 중요로 분류된 검색 결과는 반드시 성능 문제를 일으키지는 않지만 최적이 아닌 구성을 나타냅니다. 정보로 분류되는 검색 결과는 정보만 제공합니다.

권장 사항과, 모든 중대 및 중요 결과에 대한 링크를 검토합니다. 성능에 어떻게 영향을 미칠 수 있고 성능 최적화 구성을 위한 모범 사례는 무엇인지 살펴봅니다.

### <a name="storage-tab"></a>저장소 탭

**검색 결과** 섹션은 저장소와 관련된 다양한 검색 결과 및 권장 사항을 표시합니다.

**디스크 맵** 및 **볼륨 맵** 섹션에서는 논리 볼륨과 실제 디스크가 서로 관련된 방식을 설명합니다.

실제 디스크 관점(디스크 맵)에서 테이블은 디스크에서 실행 중인 모든 논리 볼륨을 표시합니다. 다음 예에서 **PhysicalDrive2**는 여러 파티션(J 및 H)에서 만든 2개의 논리 볼륨을 실행합니다.

![디스크 탭 스크린샷](media/how-to-use-perfInsights/disktab.png)

볼륨 관점(볼륨 맵)에서 테이블은 각 논리 볼륨 아래의 모든 실제 디스크를 표시합니다. RAID/동적 디스크의 경우 여러 실제 디스크에서 논리 볼륨을 실행할 수 있습니다. 다음 예에서 *C:\\mount*는 실제 디스크 2 및 3에서 *SpannedDisk*로 구성된 탑재 지점입니다.

![볼륨 탭 스크린샷](media/how-to-use-perfInsights/volumetab.png)

### <a name="sql-tab"></a>SQL 탭

대상 VM에서 SQL Server 인스턴스를 호스팅하는 경우 **SQL**이라는 보고서에 추가 탭이 표시됩니다.

![SQL 탭 스크린샷](media/how-to-use-perfInsights/sqltab.png)

이 섹션에는 VM에 호스팅된 SQL Server 인스턴스 각각에 대한 **검색 결과** 탭 및 추가적인 탭이 포함되어 있습니다.

**검색 결과** 탭에는 권장 사항과 함께 확인할 수 있는 모든 SQL 관련 성능 문제의 목록이 포함됩니다.

다음 예에서는 **PhysicalDrive0**(C 드라이브 실행)이 표시됩니다. **modeldev** 및 **modellog** 파일이 모두 C 드라이브에 있고 이 파일들은 서로 다른 형식(예: 데이터 파일 및 트랜잭션 로그)의 파일이기 때문입니다.

![로그 정보 스크린샷](media/how-to-use-perfInsights/loginfo.png)

SQL Server의 특정 인스턴스에 대한 탭에는 선택한 인스턴스에 대한 기본 정보를 표시하는 일반 섹션이 있습니다. 이 탭에는 설정, 구성, 사용자 옵션 등의 고급 정보를 포함하는 추가적인 섹션도 있습니다.

### <a name="diagnostic-tab"></a>진단 탭
**진단** 탭에는 PerfInsights 실행 기간 동안 컴퓨터에 대한 상위 CPU, 디스크 및 메모리 소비자에 대한 정보가 포함됩니다. 또한 시스템이 누락할 수 있는 위험 패치, 작업 목록 및 중요한 시스템 이벤트 등 기타 정보도 확인할 수 있습니다. 

## <a name="references-to-the-external-tools-used"></a>사용되는 외부 도구에 대한 참조

### <a name="diskspd"></a>Diskspd

Diskspd는 Microsoft에서 제공하는 저장소 로드 생성기 및 성능 테스트 도구입니다. 자세한 내용은 [Diskspd](https://github.com/Microsoft/diskspd)를 참조하세요.

### <a name="xperf"></a>XPerf

Xperf는 Windows 성능 도구 키트에서 추적을 캡처하는 명령줄 도구입니다. 자세한 내용은 [Windows 성능 도구 키트 - Xperf(영문)](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

추가 검토를 위해 Microsoft 지원 서비스에 진단 로그 및 보고서를 업로드할 수 있습니다. 지원 담당자는 문제 해결 프로세스를 지원하기 위해 PerfInsights에서 생성된 출력을 전송하도록 요청받을 수 있습니다.

다음 스크린샷과 유사한 메시지가 표시됩니다.

![Microsoft 지원 웹 사이트의 샘플 메시지 스크린샷](media/how-to-use-perfInsights/supportemail.png)

메시지의 지침에 따라 파일 전송 작업 영역에 액세스합니다. 보안을 강화하기 위해 처음 사용할 때 암호를 변경해야 합니다.

로그인하면 PerfInsights에서 수집한 **CollectedData\_yyyy-MM-dd\_hh-mm-ss-fff.zip** 파일을 업로드하는 대화 상자가 표시됩니다.
