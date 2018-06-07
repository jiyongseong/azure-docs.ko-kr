---
title: Azure 스택 1710 업데이트 (빌드 20171020.1) | Microsoft Docs
description: Azure 스택 1710 업데이트에 포함 된 내용에 대 한 자세한 내용은 시스템, 알려진된 문제 및 업데이트를 다운로드 위치에 통합 합니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 135314fd-7add-4c8c-b02a-b03de93ee196
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: mabrigg
ms.openlocfilehash: 8c7c39ecdc332c994e5c00f8415462f208e7d20b
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="azure-stack-1710-update-build-201710201"></a>Azure 스택 1710 업데이트 (빌드 20171020.1)

*적용 대상: Azure 스택 시스템 통합*

이 문서는 향상 된 기능에 설명 하 고 알려진 문제가이 릴리스 및 업데이트를 다운로드 하는 위치에 대 한이 업데이트 패키지에 수정 합니다. 알려진 문제는 알려진된 문제 나뉩니다 직접 업데이트 프로세스와 관련 하 고 알려진 문제가 빌드 (설치 후).

> [!IMPORTANT]
> 이 업데이트 패키지는 Azure 스택 통합 시스템에만. Azure 스택 개발 키트를이 업데이트 패키지를 적용 되지 않습니다.

## <a name="improvements-and-fixes"></a>향상된 기능 및 수정 프로그램

이 업데이트 Azure 스택에 대 한 개선 된 품질 आ स ा 수정 프로그램을 포함합니다.
 
### <a name="windows-server-2016-improvements-and-fixes"></a>Windows Server 2016 개선 사항 및 수정

- Windows Server 2016에 대 한 업데이트: 2017 년 10 월 10-KB4041691 (OS 빌드 14393.1770 합니다. 참조 [ https://support.microsoft.com/help/4041691 ](https://support.microsoft.com/help/4041691) 자세한 정보에 대 한 합니다.

### <a name="additional-quality-improvements-and-fixes"></a>추가 품질 개선 사항 및 수정

- 문제를 해결 하 고 시간 NTP (Network Protocol) 서버를 업데이트 하는 권한 있는 끝점 PowerShell cmdlet을 추가 합니다.
- 권한 있는 끝점 관리 JEA (Just Enough) 끝점 모듈 및 cmdlet 화이트 리스트 업데이트에 대 한 지원이 추가 되었습니다. 
- 권한 있는 끝점에서 해당 언어로 오류를 수정 합니다.
- 게이트웨이 자격 회전 하는 기능을 추가 합니다.
- CBLocalAdmin 로컬 관리자 계정을 제거 합니다. 
- 하트 비트 경고 템플릿 콘텐츠 고정 있는지 하트 비트 수 있도록 경고 작업 올바르게 업데이트 후 합니다.
- FRU 작업 중 시간 초과 다루는 데 패브릭 리소스 공급자를 수정 했습니다. 
- Azure 스택에서 Azure 리소스 관리자 API 프로필을 사용 하는 클라우드 개발자를 위한 기능이 추가 되었습니다.
- 배포 가상 컴퓨터 (DVM)에서 Windows Update 서비스를 사용할 수 없습니다. 
- 사용자 인터페이스에서 노드 전원 켜기/끄기 작업을 제거 합니다.
- 다양 한 기타 성능 및 안정성 수정 합니다. 
 
## <a name="known-issues-with-the-update-process"></a>업데이트 프로세스의 알려진된 문제

이 섹션 1710 업데이트 설치 중 발생할 수 있는 알려진된 문제가 포함 되어 있습니다.

> [!IMPORTANT]
> 업데이트가 실패 하는 경우 나중에 시도할 때 사용 해야 하는 업데이트를 다시 시작은 `Resume-AzureStackUpdate` 권한 있는 끝점에서 cmdlet. 관리자 포털을 사용 하 여 업데이트를 다시 시작 되지 않습니다. (이 릴리스의 알려진된 문제입니다.) 자세한 내용은 참조 [업데이트 권한이 있는 끝점을 사용 하 여 Azure 스택의 모니터링](azure-stack-monitor-update.md)합니다.

| 증상  | 원인  | 해결 방법 |
|---------|---------|---------|
|다음과 유사한 오류가 한 업데이트를 수행 하는 경우<br>"저장소 호스트를 다시 시작 저장소 노드" 단계에서 발생할 수 있습니다.<br> 업데이트 작업 계획 합니다.<br><br>**{"name": "다시 시작 저장소 호스트", "설명": "다시 시작<br> 저장소 호스트 합니다.","errorMessage": "'다시 시작' 역할의 입력<br> 예외: \n\n 컴퓨터 발생 하는 'BareMetal'<br> 호스트 이름-05를 건너뜁니다. 해당 마지막 부팅 시간을 검색 하지 못했습니다<br> 다음 오류 메시지와 함께 WMI 서비스를 통해:<br> RPC 서버를 사용할 수 없습니다.<br> (HRESULT의 예외: 0x800706BA). \nat: 다시 시작을 호스트** | 이 문제는 일부 구성에 잠재적인 문제가 드라이버에 의해 발생 합니다. | 1. 베이스 보드 관리 컨트롤러 (BMC) 웹 인터페이스에 로그인 하 고 오류 메시지에서 식별 되는 호스트를 다시 시작 합니다.<br><br>2. 권한 있는 끝점을 사용 하 여 업데이트를 다시 시작 합니다. |
| 업데이트 프로세스 표시 지연 업데이트를 수행 하는 경우<br> 단계를 수행한 후 진행 상황을 확인 하지 "단계: 단계 2.4-설치를 실행 합니다.<br> 업데이트 작업 계획의 "업데이트 합니다.<br><br>이 단계 그 다음으로 일련의.nupkg의 복사 프로세스<br> 내부 인프라 파일 공유에 파일입니다. 예: <br><br>**1 개의 파일을 content\PerfCollector\VirtualMachines에서 복사 <br> \VirtualMachineName-ERCS03\C$\TraceCollectorUpdate\ <br>PerfCounterConfiguration**<br><br>또는 메시지를 참조 하세요.<br><br>**WarningMessage:Task: 'LiveUpdate' 인터페이스의 호출<br> 못했습니다 ' Cloud\Fabric\VirtualMachines' 역할의:<br> 'VirtualMachines'가 발생 하는 역할의 'LiveUpdate'를 입력 한<br> 예외:는 디스크에 공간이 부족 .**  | 이 문제는 로그 파일 꽉 찬 디스크 인프라 가상 컴퓨터 및에서 Windows 서버 스케일 아웃 파일 서버 (SOFS) 후속 업데이트에 전달 되는 문제를 발생 합니다. | Microsoft 고객 서비스 및 지원 (CSS)에 문의 하십시오. | 
| 다음과 유사한 오류가 한 업데이트를 수행 하는 경우<br> 단계에서 발생할 수 있습니다 "단계: 단계 2.13.2-업데이트 실행 중<br> *VM_Name*"의 업데이트 작업 계획 합니다. (가상 컴퓨터<br> 이름이 다를 수 있습니다.)<br><br>**ActionPlanInstanceWarning ece/MachineName:<br> WarningMessage:Task: 호출의 'LiveUpdate' 인터페이스<br> 역할 'Cloud\Fabric\WAS' 못했습니다: 'LiveUpdate' 역할의 입력<br> '가'에서 예외가 발생 했습니다: 중 오류가 발생 했습니다 저장소<br> 초기화: API를 만드는 동안 오류가 발생 했습니다.<br> Microsoft 저장소 서비스에 대 한 호출: {"Message": "시간 초과<br> 서비스 패브릭와 통신 하는 동안 발생 했습니다.<br> 예외 유형: TimeoutException 합니다.<br> 예외 메시지: 작업 시간이 초과 되었습니다. "}**  | 이 문제는 이후 업데이트에서 수정 하는 Windows Server의 경우 I/O 시간 초과 발생 합니다. | Microsoft CSS에 문의 하십시오.
| 다음과 유사한 오류가 한 업데이트를 수행 하는 경우<br> 단계 중 "단계 21 다시 시작 SQL server Vm." 발생할 수 있습니다.<br><br>**발생 하는 ' VirtualMachines' 역할의 'LiveUpdateRestart'를 입력 한<br> 예외: VerboseMessage: [VirtualMachines:LiveUpdateRestart]<br> MachineName VM에 대 한 쿼리-Sql01.-10/13/2017 오후 5시 11분: 50 VerboseMessage: [VirtualMachines: LiveUpdateRestart]<br> VM-10/13/2017 HighlyAvailable.으로 표시 되어 오후 5시 11분: 50<br> VerboseMessage: [VirtualMachines:LiveUpdateRestart]에서<br>MS. Internal.ServerClusters.ExceptionHelp.Build<br>MS. Internal.ServerClusters.ClusterResource.BeginTakeOffline<br>(부울 force) Microsoft.FailoverClusters.PowerShell에.<br> StopClusterResourceCommand.BeginTimedOperation() <br>Microsoft.FailoverClusters.PowerShell.TimedCmdlet.Wrapped<br>Microsoft.FailoverClusters.PowerShell에서 ProcessRecord().<br> FCCmdlet.ProcessRecord()-10/13/2017 오후 5시 11분: 50 경고<br>메시지: 작업: 호출의 'LiveUpdateRestart' 인터페이스<br> 역할 'Cloud\Fabric\VirtualMachines'가 실패 했습니다.** | 가상 컴퓨터를 다시 시작 하지 못했습니다.이 문제가 발생할 수 있습니다. | Microsoft CSS에 문의 하십시오.
| 업데이트를 수행 하는 경우 다음과 유사한 오류가 발생할 수 있습니다.<br><br>**2017-10-22T01:37:37.5369944Z 'SQL' 역할의 유형 '종료'<br> 에서 예외가 발생 했습니다: 오류가 발생 일시 중지 노드<br> ' s45r1004 Sql01' 사이트 중지 SQL C:\ProgramData\SF\ErcsClusterNode2 <br>\Fabric\work\ Applications\ EnterpriseCloud <br>EngineApplicationType&#95;App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\ <br> 에서 542 CloudDeployment\Roles\SQL\SQL.psm1:line<br> 종료, C:\ProgramData\SF\ErcsClusterNode2\Fabric\work\ <br>응용 프로그램 \EnterpriseCloudEngineApplicationType&#95;App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\Cloud<br> Deployment\Classes\SQL\SQL.psm1: 줄에 50 < ScriptBlock&#62;,<br> <No file>: 줄에 18 < ScriptBlock&#62;, < 파일 없음&#62;: 16 줄** | 가상 컴퓨터 역할을 드레이닝 하도록 일시 중단된 상태로 넣을 수 없습니다이 문제가 발생할 수 있습니다. | Microsoft CSS에 문의 하십시오.
| 업데이트를 수행 하는 경우 다음 오류 중 하나가 발생할 수 있습니다.<br><br>**역할 'ADFS' 유형의 '유효성 검사'에서 예외가 발생 했습니다: 유효성 검사<br> ADFS/그래프 역할 실패 했습니다 오류에 대 한: 오류 검사 ADFS<br> 끝점 프로브 *endpoint_URI*: 예외 호출<br> " "0" 인수를 GetResponse":" 원격 서버<br> 오류를 반환 했습니다: (503) 서버 사용할 수 없습니다. "에서 Invoke-<br>ADFSGraphValidation**<br><br>**역할 'ADFS' 유형의 '유효성 검사'에서 예외가 발생 했습니다: 유효성 검사<br> ADFS/그래프 역할 실패 했습니다 오류에 대 한: 오류 인출<br> ADFS 속성:에 연결 하지 못했습니다 <br>net.tcp: //localhost: 1500/정책입니다. 연결 시도 동안 지속<br> 00:00:02.0498923의 시간 범위에 대 한 합니다. TCP 오류 코드<br> 10061: 없음 수 때문에 연결할 수는 대상<br> 컴퓨터 거부 했으므로 127.0.0.1:1500.<br> Invoke ADFSGraphValidation에서** | 업데이트 작업 계획 Active Directory Federation Services (AD FS) 상태를 확인할 수 없습니다. | Microsoft CSS에 문의 하십시오.

## <a name="known-issues-post-installation"></a>알려진된 문제 (설치 후)

이 섹션에는 알려진 문제가 20171020.1 빌드 설치 후 포함 되어 있습니다.

### <a name="portal"></a>포털

- 관리자 포털에서 계산 또는 저장소 리소스를 볼 수 있습니다 수 없습니다. 이 업데이트를 잘못 보고 성공으로 하 고 업데이트를 설치 하는 동안 오류가 발생 했음을 나타냅니다. 이 문제가 발생 한 경우 Microsoft CSS에 문의 하십시오.
- 포털에서 빈 대시보드를 볼 수 있습니다. 대시보드를 복구 하려면 포털의 오른쪽 위 모서리에서 톱니 바퀴형 아이콘을 선택 하 고 다음 선택 **기본 설정을 복원**합니다.
- 리소스 그룹의 속성을 볼 때의 **이동** 단추가 비활성화 됩니다. 이 동작은 사용할 수 있습니다. 구독 간 리소스 그룹 이동 현재 지원 되지 않습니다.
- 모든 워크플로에 드롭 다운 목록에서 구독, 리소스 그룹 또는 위치 선택 되는 위치에 대 한 다음 문제 중 하나 이상이 발생할 수 있습니다.

   - 목록 맨 위에 있는 빈 행을 볼 수 있습니다. 여전히 예상 대로 항목을 선택할 수 있습니다.
   - 드롭다운 목록에 있는 항목의 목록이 긴 경우 수 항목 이름 중 하나를 볼 수 없습니다.
   - 구독이 여러 개인 사용자, 리소스 그룹 드롭 다운 목록 비어 있을 수 있습니다. 

   마지막 두 개의 문제를 해결 하려면 구독 또는 리소스 그룹 (경우 것)의 이름을 입력할 수 있습니다 또는 PowerShell을 대신 사용할 수 있습니다.
- 분리 된 리소스에서 사용자 구독 결과 삭제 합니다. 이 문제를 해결 먼저 사용자 리소스 또는 전체 리소스 그룹을 삭제 하 고 사용자 구독을 삭제 합니다.
- Azure 스택 포털을 사용 하 여 구독에 대 한 사용 권한을 확인할 수 없는 합니다. 이 문제를 해결 PowerShell을 사용 하 여 사용 권한을 확인할 수 있습니다.
- **서비스 상태** 블레이드 로드 되지 않습니다. 관리자 또는 사용자 포털에서 Azure 스택 서비스 상태 블레이드를 열 때 오류가 표시 되 고 정보를 로드 하지 않습니다. 이는 정상적인 동작입니다. 선택 하 고 서비스 상태를 열 수 있지만이 기능은 아직 제공 되지 않습니다 되지만 Azure 스택의 이후 버전에서 구현 됩니다.
 

### <a name="backup"></a>Backup

- 인프라 백업에 사용 하지 마십시오는 **인프라 백업** 블레이드입니다.

### <a name="health-and-monitoring"></a>상태 및 모니터링

- 인프라 역할 인스턴스를 다시 부팅 하는 경우 다시 부팅 실패 했음을 나타내는 메시지가 나타날 수 있습니다. 그러나 다시 부팅 실제로 성공 했습니다.

### <a name="marketplace"></a>Marketplace
- 사용 하 여 Azure 스택 마켓플레이스 항목을 추가 하려고 하면는 **Azure에서 추가** 옵션을 일부 항목 다운로드에 대 한 표시 될 수 있습니다.
- 사용자 구독 하지 않고 전체 마켓플레이스를 찾아볼 수 있으며, 계획 및 제안 같은 관리 항목을 볼 수 있습니다. 이러한 항목은 사용자에 게 기능입니다.

### <a name="compute"></a>컴퓨팅
- 사용자에는 지역 중복 저장소를 사용 하 여 가상 컴퓨터를 만들려면 옵션이 제공 됩니다. 이 구성을 사용 하면 가상 컴퓨터 만들기 실패 합니다.
- 가상 컴퓨터 가용성,의 장애 도메인 및 하나의 업데이트 도메인만 집합을 구성할 수 있습니다.
- 가상 컴퓨터 크기 집합을 만들려는 마켓플레이스 본 경험이 없는 경우 크기는 템플릿을 사용 하 여 집합을 만들 수 있습니다.
- 크기 조정 설정을 가상 컴퓨터 크기 집합에 대 한 포털에서 사용할 수 없는 경우 한 대 안으로 사용할 수 있습니다 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)합니다. PowerShell 버전 차이 때문에 사용 해야 합니다는 `-Name` 매개 변수 대신 `-VMScaleSetName`합니다.
 
### <a name="networking"></a>네트워킹
- 포털을 사용 하 여 공용 IP 주소와 부하 분산 장치를 만들 수 없습니다. 이 문제를 해결를 부하 분산 장치를 만드는 PowerShell을 사용할 수 있습니다.
- 네트워크 부하 분산 장치를 만들 때 네트워크 주소 변환 (NAT) 규칙을 만들어야 합니다. 이렇게 하지 않으면 부하 분산 장치를 만들면 NAT 규칙을 추가 하려고 할 때 오류가 받게 합니다.
- VM이 되어 해당 IP 주소와 연결 된 후 가상 컴퓨터 (VM)에서 공용 IP 주소를 분리할 수 없습니다. Disassociation 작동으로 나타나지만 이전에 할당 된 공용 IP 주소에 연결 되어 있는 원본 VM입니다. 새 VM에 IP 주소를 다시 할당 하는 경우에이 문제가 발생 (일반적으로 라고는 *VIP 교체*). 앞으로의 모든 새 아니라 원래 연결 되어 있던 VM에 대 한 연결에서이 IP 주소 결과 통해 연결을 시도 합니다. 현재, 새 VM 만들기에 대 한 새 공용 IP 주소를만 사용 해야 합니다.
 
### <a name="sqlmysql"></a>SQL/MySQL
- 테 넌 트가 새 SQL 또는 MySQL SKU에 데이터베이스를 만들 수는 1 시간까지 걸릴 수 있으므로 합니다. 
- SQL 및 MySQL 리소스 공급자가 수행 되지 않은 서버 호스팅에 직접 항목의 생성은 지원 되지 않으며 일치 하지 않는 상태가 될 수 있습니다.
 
### <a name="app-service"></a>App Service
- 사용자는 구독에 해당 첫 번째 Azure 기능을 만들기 전에 저장소 리소스 공급자를 등록 해야 합니다.
 
### <a name="field-replaceable-unit-fru-procedures"></a>필드 (FRU) 교체 장치 프로시저

- 업데이트를 실행 하는 동안 오프 라인 이미지 패치가 적용 되지 않습니다 됩니다. 배율 단위 노드를 교체 해야 하는 경우 최신 패치 수준이 대체 노드에 있는지 확인 OEM 하드웨어 공급 업체와 함께 작동 합니다.

## <a name="download-the-update"></a>업데이트 다운로드

1710 업데이트 패키지를 다운로드할 수 [여기](https://aka.ms/azurestackupdatedownload)합니다.

## <a name="next-steps"></a>다음 단계

- Azure 스택에서 업데이트 관리의 개요를 참조 하십시오. [Azure 스택 개요에서 업데이트를 관리](azure-stack-updates.md)합니다.
- 업데이트를 적용 하는 방법에 대 한 정보를 참조 하십시오. [Azure 스택에서 업데이트 적용](azure-stack-apply-updates.md)합니다.
