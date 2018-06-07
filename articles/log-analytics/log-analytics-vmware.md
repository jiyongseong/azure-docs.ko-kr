---
title: Log Analytics의 VMware 모니터링 솔루션 | Microsoft Docs
description: VMware 모니터링 솔루션으로 로그를 관리하고 ESXi 호스트를 모니터링하는 방법을 알아봅니다.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: 16516639-cc1e-465c-a22f-022f3be297f1
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: magoedte
ms.openlocfilehash: 77326832f42cc1ef74ae7a380f4e38d3c67d17b7
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="vmware-monitoring-preview-solution-in-log-analytics"></a>Log Analytics의 VMware 모니터링(미리 보기) 솔루션

![VMware 기호](./media/log-analytics-vmware/vmware-symbol.png)

> [!NOTE]
> VMware 모니터링 솔루션은 더 이상 사용되지 않습니다.  솔루션이 이미 설치되어 있는 고객은 계속해서 사용할 수 있지만 VMware 모니터링을 모든 새 작업 영역에 추가할 수 없습니다.

Log Analytics VMware 모니터링 솔루션은 대규모 VMware 로그에 적합한 중앙 집중식 로깅 및 모니터링 접근 방식을 만들 수 있는 솔루션입니다. 이 문서에서는 단일 위치에서 이 솔루션을 사용하여 ESXi 호스트의 문제를 해결, 캡처 및 관리하는 방법을 설명합니다. 솔루션을 사용하면 단일 위치에서 모든 ESXi 호스트에 대한 데이터를 자세히 볼 수 있습니다. ESXi 호스트 로그를 통해 제공되는 상위 이벤트 수, 상태, VM 및 ESXi 호스트의 추세를 볼 수 있습니다. 중앙 집중식 ESXi 호스트 로그를 보고 검색하여 문제를 해결할 수 있습니다. 그리고 로그 검색 쿼리에 기반한 경고를 만들 수 있습니다.

이 솔루션은 ESXi 호스트의 기본 syslog 기능을 사용하여 OMS 에이전트가 있는 대상 VM에 데이터를 푸시합니다. 그러나 대상 VM 내의 syslog에 파일을 작성하지는 않습니다. OM 에이전트는 포트 1514를 열고 수신 대기합니다. 데이터를 받으면 이를 Log Analytics로 푸시합니다.

## <a name="install-and-configure-the-solution"></a>솔루션 설치 및 구성
다음 정보를 사용하여 솔루션을 설치하고 구성합니다.

* [관리 솔루션 추가](log-analytics-add-solutions.md#add-a-management-solution)에 설명된 프로세스를 사용하여 구독에 VMware 모니터링 솔루션을 추가합니다.

#### <a name="supported-vmware-esxi-hosts"></a>지원되는 VMware ESXi 호스트
vSphere ESXi 호스트 5.5, 6.0 및 6.5

#### <a name="prepare-a-linux-server"></a>Linux 서버 준비
ESXi 호스트로부터 모든 syslog 데이터를 수신하는 Linux 운영 체제 VM을 만듭니다. [OMS Linux 에이전트](log-analytics-linux-agents.md)는 모든 ESXi 호스트 syslog 데이터를 수집하는 지점입니다. 아래 예에서 보여 주듯이 여러 ESXi 호스트에서 단일 Linux 서버로 로그를 전달할 수 있습니다.  

   ![syslog 흐름](./media/log-analytics-vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Syslog 수집 구성
1. vSphere에 syslog를 전달하도록 설정합니다. Syslog 전달을 설정하는 데 도움이 되는 자세한 내용은 [ESXi 5.0 이상(2003322)에서 syslog 구성](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322)을 참조하세요. **ESXi 호스트 구성** > **소프트웨어** > **고급 설정** > **Syslog**로 이동합니다.
   ![vsphereconfig](./media/log-analytics-vmware/vsphere1.png)  
2. *Syslog.global.logHost* 필드에서 Linux 서버 및 *1514* 포트 번호를 추가합니다. 예를 들어 `tcp://hostname:1514` 또는 `tcp://123.456.789.101:1514`와 같습니다.
3. Syslog의 ESXi 호스트 방화벽을 엽니다. **ESXi 호스트 구성** > **소프트웨어** > **보안 프로필** > **방화벽**으로 이동한 다음 **속성**을 엽니다.  

    ![vspherefw](./media/log-analytics-vmware/vsphere2.png)  

    ![vspherefwproperties](./media/log-analytics-vmware/vsphere3.png)  
4. vSphere 콘솔에서 해당 syslog가 제대로 설정되어 있는지 확인합니다. ESXi 호스트에서 포트가 **1514**로 구성되어 있는지 확인합니다.
5. Linux용 OMS 에이전트를 다운로드하여 Linux 서버에 설치합니다. 자세한 내용은 [Linux용 OMS 에이전트 설명서](https://github.com/Microsoft/OMS-Agent-for-Linux)를 참조하세요.
6. Linux용 OMS 에이전트를 설치한 후 /etc/opt/microsoft/omsagent/sysconf/omsagent.d 디렉터리로 이동하고, vmware_esxi.conf 파일을  /etc/opt/microsoft/omsagent/conf/omsagent.d 디렉터리에 복사한 다음 해당 파일의 소유자/그룹 및 사용 권한을 변경합니다. 예: 

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
7. `sudo /opt/microsoft/omsagent/bin/service_control restart`를 실행하여 Linux용 OMS 에이전트를 다시 시작합니다.
8. ESXi 호스트에서 `nc` 명령을 사용하여 Linux 서버와 ESXi 호스트 간의 연결을 테스트합니다. 예: 

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

9. Azure Portal에서 `VMware_CL` 로그 검색을 수행합니다. Log Analytics에서 syslog 데이터를 수집할 때는 syslog 형식을 그대로 유지합니다. 포털에서는 *Hostname* 및 *ProcessName*과 같은 일부 특정 필드가 캡처됩니다.  

    ![형식](./media/log-analytics-vmware/type.png)  

    로그 검색 결과 보기가 위 이미지와 비슷한 경우 VMware 모니터링 솔루션 대시보드를 사용하도록 설정된 것입니다.  

## <a name="vmware-data-collection-details"></a>VMware 데이터 수집 세부 정보
VMware 모니터링 솔루션에서는 사용 설정된 Linux용 OMS 에이전트를 통해 ESXi 호스트로부터 다양한 성능 메트릭과 로그 데이터를 수집합니다.

다음 테이블은 데이터 수집 방법 및 데이터가 수집되는 방식에 대한 기타 세부 정보를 보여 줍니다.

| 플랫폼 | Linux 용 OMS 에이전트 | SCOM 에이전트 | Azure Storage | SCOM 필요? | 관리 그룹을 통해 전송되는 SCOM 에이전트 데이터 | 수집 빈도 |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |&#8226; |  |  |  |  |매 3분 |

다음 표에서는 VMware 모니터링 솔루션에서 수집한 데이터 형식의 예를 보여 줍니다.

| 필드 이름 | 설명 |
| --- | --- |
| Device_s |VMware 저장소 장치 |
| ESXIFailure_s |오류 유형 |
| EventTime_t |이벤트가 발생한 시간 |
| HostName_s |ESXi 호스트 이름 |
| Operation_s |VM 만들기 또는 삭제 |
| ProcessName_s |이벤트 이름 |
| ResourceId_s |VMware 호스트 이름 |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Hyper-V |
| SCSIStatus_s |VMware SCSI 상태 |
| SyslogMessage_s |Syslog 데이터 |
| UserName_s |VM을 만들거나 삭제한 사용자 |
| VMName_s |VM 이름 |
| Computer |호스트 컴퓨터 |
| TimeGenerated |데이터가 생성된 시간 |
| DataCenter_s |VMware 데이터 센터 |
| StorageLatency_s |저장소 대기 시간(밀리초) |

## <a name="vmware-monitoring-solution-overview"></a>VMware 모니터링 솔루션 개요
VMware 타일이 Log Analytics 작업 영역에 나타납니다. 발생한 모든 오류에 대한 상위 수준 보기를 제공합니다. 타일을 클릭하면 대시보드 보기로 이동합니다.

![타일](./media/log-analytics-vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>대시보드 보기 탐색
**VMware** 대시보드 보기에 구성되는 블레이드는 다음과 같습니다.

* 오류 상태 수
* 이벤트 수 기준 상위 호스트
* 상위 이벤트 수
* Virtual Machine 활동
* ESXi 호스트 디스크 이벤트

![solution1](./media/log-analytics-vmware/solutionview1-1.png)

![solution2](./media/log-analytics-vmware/solutionview1-2.png)

블레이드를 클릭하면 해당 블레이드의 특정 세부 정보를 보여 주는 Log Analytics 검색 창이 열립니다.

여기서는 특정 항목을 위해 검색 쿼리를 수정하도록 편집할 수 있습니다. 로그 검색을 만드는 방법에 대한 자세한 내용은 [로Log Analytics에서 로그 검색을 사용하여 데이터 찾기](log-analytics-log-searches.md)를 참조하세요.

#### <a name="find-esxi-host-events"></a>ESXi 호스트 이벤트 찾기
단일 ESXi 호스트에서는 프로세스에 따라 여러 로그를 생성합니다. VMware 모니터링 솔루션은 이러한 로그를 중앙 집중식으로 관리하고 이벤트 수를 요약 합니다. 이처럼 중앙 집중화된 보기를 사용하면 대량의 이벤트가 발생한 ESXi 호스트 및 사용자 환경에서 가장 빈번하게 발생한 이벤트를 손쉽게 파악할 수 있습니다.

![event](./media/log-analytics-vmware/events.png)

ESXi 호스트 또는 이벤트 유형을 클릭하면 관련 정보를 자세히 파악할 수 있습니다.

ESXi 호스트 이름을 클릭하여 해당 ESXi 호스트의 정보를 살펴봅니다. 이벤트 유형으로 한정된 결과를 원하는 경우 검색 쿼리에 `“ProcessName_s=EVENT TYPE”`을 추가합니다. 검색 필터에서 **ProcessName**을 선택할 수도 있습니다. 그러면 사용자가 원하는 정보로 맞추어집니다.

![연습](./media/log-analytics-vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>빈번한 VM 활동 찾기
ESXi 호스트마다 가상 머신을 만들고 삭제할 수 있습니다. 이렇게 하면 관리자가 ESXi 호스트에서 만드는 VM 수를 식별하는 데 도움이 됩니다. 또한 성능과 용량 계획을 파악하는 데에도 도움이 됩니다. 사용자 환경을 관리할 때는 VM 활동 이벤트를 추적하는 것이 중요합니다.

![연습](./media/log-analytics-vmware/vmactivities1.png)

또한 ESXi 호스트 VM 생성 데이터도 확인하려는 경우에는 ESXi 호스트 이름을 클릭합니다.

![연습](./media/log-analytics-vmware/createvm.png)

#### <a name="common-search-queries"></a>일반적 검색 쿼리
솔루션에는 대규모 저장소 공간, 저장소 대기 시간, 경로 오류 등 ESXi 호스트를 관리하는 데 유용한 쿼리들이 포함되어 있습니다.

[!INCLUDE[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

![쿼리](./media/log-analytics-vmware/queries.png)


#### <a name="save-queries"></a>쿼리 저장
Log Analytics 표준 기능인 검색 쿼리 저장은 유용한 것으로 확인된 쿼리를 유지하는 데 도움이 됩니다. 만든 쿼리가 유용하다고 생각되면 **즐겨찾기**를 클릭하여 해당 쿼리를 저장합니다. 저장된 쿼리를 사용하면 나중에 사용자 지정 대시보드를 만들 수 있는 [내 대시보드](log-analytics-dashboards.md) 페이지에서 해당 쿼리를 손쉽게 다시 활용할 수 있습니다.

![DockerDashboardView](./media/log-analytics-vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>쿼리에서 경고 만들기
쿼리를 만든 후에는 특정 이벤트가 발생할 때 경고하도록 이 쿼리를 사용하는 것이 좋습니다. 경고를 생성하는 방법에 대한 내용은 [Log Analytics의 경고](log-analytics-alerts.md)를 참조하세요. 경고 쿼리 및 기타 쿼리의 예제에 대해서는 [Monitor VMware using Log Analytics](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics)(Log Analytics를 사용하여 VMware 모니터링) 블로그 게시물을 참조하세요.

## <a name="frequently-asked-questions"></a>질문과 대답
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>ESXi 호스트 설정에서 수행해야 하는 작업은 무엇입니까? 현재 환경에 미치는 영향은 무엇입니까?
이 솔루션은 기본 ESXi 호스트 Syslog 전달 메커니즘을 사용합니다. ESXi 호스트에 추가 Microsoft 소프트웨어가 없어도 로그를 캡처할 수 있습니다. 기존 환경에 미치는 영향이 적습니다. 그러나 ESXI 기능인 syslog 전달을 설정해야 합니다.

### <a name="do-i-need-to-restart-my-esxi-host"></a>ESXi 호스트를 다시 시작해야 합니까?
번호 이 프로세스는 호스트를 다시 시작하지 않아도 됩니다. vSphere에서 syslog을 제대로 업데이트하지 못하는 경우가 있습니다. 이 경우 ESXi 호스트에 로그온하여 syslog를 다시 로드하세요. 호스트를 다시 시작할 필요가 없으므로 이 프로세스는 사용자 환경에 방해가 되지 않습니다.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>Log Analytics로 전송되는 로그 데이터의 양을 늘리거나 줄일 수 있나요?
예. vsphere에서 ESXi 호스트 로그 수준 설정을 사용하면 됩니다. 로그 수집은 *정보* 수준을 기반으로 합니다. 따라서 VM 만들기 또는 삭제를 감사하려면 호스트에서 *정보* 수준을 유지해야 합니다. 자세한 내용은 [VMware 기술 자료](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658)를 참조하세요.

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>호스트에서 Log Analytics에 데이터를 제공하지 않는 이유는 무엇인가요? 로그는 정보로 설정되어 있습니다.
Syslog 타임스탬프에 대한 ESXi 호스트 버그가 있었습니다. 자세한 내용은 [VMware 기술 자료](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202)를 참조하세요. 해결 방법을 적용하면 호스트가 정상적으로 작동할 것입니다.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>여러 ESXi 호스트에서 omsagent를 실행하는 단일 VM에 syslog 데이터를 전달하도록 할 수 있습니까?
예. 여러 ESXi 호스트에서 omsagent를 실행하는 단일 VM에 전달하도록 할 수 있습니다.

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>Log Analytics로 이동하는 데이터가 보이지 않는 이유는 무엇인가요?
여러 가지 이유가 있을 수 있습니다.

* ESXi 호스트가 omsagent를 실행하는 VM에 데이터를 올바르게 푸시하지 않습니다. 테스트하려면 다음 단계를 수행합니다.

  1. 확인하려면 ssh를 사용하여 ESXi 호스트에 로그인하고 다음 명령을 실행합니다. `nc -z ipaddressofVM 1514`

      실행에 실패한 경우 고급 구성의 vSphere 설정이 올바르지 않을 수 있습니다. syslog 전달을 위해 ESXi 호스트를 설정하는 방법에 대한 자세한 내용은 [syslog 수집 구성](#configure-syslog-collection)을 참조하세요.
  2. Syslog 포트 연결에 성공했지만 여전히 데이터가 보이지 않는 경우 ssh를 통해 다음 명령을 실행하여 ESXi 호스트에서 syslog를 다시 로드하세요. ` esxcli system syslog reload`
* OMS 에이전트가 설치된 VM이 올바르게 설정되지 않았습니다. 이를 테스트하려면 다음 단계를 수행합니다.

  1. Log Analytics는 1514 포트에서 수신 대기합니다. 이 포트가 열려 있는지 확인하려면 다음 명령을 실행합니다. `netstat -a | grep 1514`
  2. 포트 `1514/tcp`가 열려 있는지 확인해야 합니다. 그렇지 않으면 omsagent가 제대로 설치되어 있는지 확인합니다. 포트 정보가 보이지 않으면 syslog 포트가 VM에서 열려 있지 않은 것입니다.

    a. `ps -ef | grep oms`를 사용하여 OM 에이전트가 실행 중인지 확인합니다. 실행되고 있지 않은 경우 ` sudo /opt/microsoft/omsagent/bin/service_control start` 명령을 실행하여 프로세스를 시작합니다.

    나. `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf` 파일을 엽니다.

    다. 적절한 사용자 및 그룹 설정이 유효한지 확인합니다(`-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`와 유사함).

    d. 파일이 없거나 사용자 및 그룹 정책 설정이 잘못된 경우 [Linux 서버를 준비](#prepare-a-linux-server)하여 정정 작업을 수행합니다.

## <a name="next-steps"></a>다음 단계
* Log Analytics의 [로그 검색](log-analytics-log-searches.md)을 통한 자세한 VMware 호스트 데이터 보기
* VMware 호스트 데이터를 보여 주는 [사용자 고유의 대시보드 만들기](log-analytics-dashboards.md)
* 특정 VMware 호스트 이벤트가 발생하는 경우의 [경고 만들기](log-analytics-alerts.md)
