---
title: Azure 스택 1712 업데이트 | Microsoft Docs
description: Azure 스택 1712 업데이트에 포함 된 내용에 대 한 자세한 내용은 시스템, 알려진된 문제 및 업데이트를 다운로드 위치에 통합 합니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: b14f79ad-025f-45d8-9e1d-e53d2b420bb1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: brenduns
ms.openlocfilehash: b4c5b53a46792e31316f752f8902d7a05554b57d
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604151"
---
# <a name="azure-stack-1712-update"></a>Azure 스택 1712 업데이트

*적용 대상: Azure 스택 시스템 통합*

이 문서는 향상 된 기능에 설명 하 고 알려진 문제가이 릴리스 및 업데이트를 다운로드 하는 위치에 대 한이 업데이트 패키지에 수정 합니다. 알려진 문제는 알려진된 문제 나뉩니다 직접 업데이트 프로세스와 관련 하 고 알려진 문제가 빌드 (설치 후).

> [!IMPORTANT]
> 이 업데이트 패키지에만 통합 하는 Azure 스택 시스템에 대 한 적용 됩니다. Azure 스택 개발 키트를이 업데이트 패키지를 적용 되지 않습니다.

## <a name="build-reference"></a>빌드 참조

Azure 스택 1712 업데이트 빌드 번호는 **180106.1**합니다. 고객이 배포한 경우 **180103.2** 적용할 필요가 없습니다 이전에 **180106.1**합니다.

## <a name="before-you-begin"></a>시작하기 전에

> [!IMPORTANT]
> 1712 업데이트 설치 과정에서 가상 컴퓨터를 만들 하려고 하지 마십시오. 참조 [Azure 스택 개요에서 업데이트를 관리](https://docs.microsoft.com/azure/azure-stack/azure-stack-updates#plan-for-updates) 내용을 확인 합니다.

### <a name="prerequisites"></a>필수 조건

Azure 스택을 먼저 설치 해야 [1711 업데이트](https://docs.microsoft.com/azure/azure-stack/azure-stack-update-1711) 이 업데이트를 적용 하기 전에 합니다.

### <a name="post-update-steps"></a>업데이트 후 단계
- 1712 설치가 끝나면 모든 적용 가능한 핫픽스를 설치 합니다. 자세한 내용은 다음 기술 자료 문서를 보려면 뿐 우리 [서비스 정책](azure-stack-servicing-policy.md)합니다.  
  - [Azure 스택 업데이트를 설치 하려고 할 때 KB 4103348-네트워크 컨트롤러 API 서비스가 충돌](https://support.microsoft.com/help/4103348)


- 업데이트 1712 1712 Azure 스택 업데이트 설치를 완료 한 후 OEM 업체 로부터 펌웨어 업데이트를 설치 하도 필요 합니다.

  > [!NOTE]
  > 업데이트를 다운로드 하려면 OEM 파트너 웹 사이트를 참조 하십시오.

### <a name="new-features-and-fixes"></a>새 기능 및 수정

이 업데이트 Azure 스택에 대 한 다음과 같은 향상 된 기능 및 수정 프로그램을 포함합니다.

#### <a name="new-features"></a>새로운 기능

- Azure 스택 클라우드의 권한 있는 끝점을 통해 사용할 수 있는 유효성 검사 테스트 AzureStack cmdlet
- Azure 스택의 연결이 끊긴된 배포를 등록할 수 있는 기능
- 인증서 및 사용자 계정 만료 기간에 대 한 모니터링 경고
- PEP BMC 암호 회전에 대 한 추가 집합 BmcPassword cmdlet
- 요청 시 로깅을 지 원하는 네트워크 로깅 업데이트
- 가상 컴퓨터 크기 집합 (VMSS)에 대 한 이미지로 다시 설치 작업을 지원
- ERCS VM에서 CloudAdmin 로그인에 대 한 키오스크 모드를 사용 하도록 설정
- 테 넌 트가 Windows Vm을 자동으로 활성화할 수 있습니다.

#### <a name="fixes"></a>수정 프로그램

- Repair를 실행 하는 동안 유지 관리 중인 노드 작동 상태를 표시 하는 수정
- 올바른 공용 IP 사용 레코드 날짜/시간 스탬프를 수정 합니다.
- 다른 다양 한 성능, 안정성 및 보안 수정
- 시간 원본 및 Defender privileged 끝점 모듈 버그 수정

#### <a name="windows-server-2016-new-features-and-fixes"></a>Windows Server 2016의 새로운 기능 및 수정

- [1 월 3-2018-KB4056890 (OS 빌드 14393.2007)](https://support.microsoft.com/help/4056890/windows-10-update-kb4056890)
    - 이 업데이트 포함 하 여 설명 하는 산업 전반 보안 문제에 대 한 소프트웨어 수정 [MSRC 보안 권고 고급 180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002)합니다.

### <a name="known-issues-with-the-update-process"></a>업데이트 프로세스의 알려진된 문제

이 섹션 1712 업데이트 설치 중 발생할 수 있는 알려진된 문제가 포함 되어 있습니다.

1. **증상:** Azure 스택 연산자는 업데이트 과정에서 다음과 같은 오류가 표시 될 수: *"에 대 한-ACS01 다음과 같은 오류가 생성 예외: \n\nVirtual 컴퓨터 상태 확인 'VirtualMachines' 역할의 'CheckHealth' 형식 발생 합니다. \nThere는 호스트에서 VM 정보를 가져오는 동안 오류가 발생 했습니다. 예외 세부 정보: \nGet-VM: 'Node03' 하지 못했습니다 컴퓨터에서 작업에: WS-관리 서비스 요청을 처리할 수 없습니다. WMI \nservice 또는 WMI 공급자에 알 수 없는 오류가 반환 되었습니다: 0x8004106c HRESULT입니다. "*
    1. **원인:** 이 문제는 후속 창 서버 업데이트에서 해결 하려고 하는 Windows Server 문제로 인해 발생 합니다.
    2. **해결 방법:** 연락처 Microsoft 고객 서비스 및 지원에 대 한 지원 (CSS).
<br><br>
2. **증상:** Azure 스택 연산자는 업데이트 과정에서 다음과 같은 오류가 표시 될 수:*"오류가 발생 하 여 호스트 Node03 노드에서 VM 하지 못했습니다. 초기값 링을 사용 하도록 설정: 호스트 Node03 때문에 실패 했습니다. 원격 서버에 연결 하는 [호스트 Node03] 중에서 다음과 같은 오류 메시지가: WinRM 클라이언트 HTTP 서버 오류 상태 (500) 받았지만 원격 서비스 오류의 원인에 대 한 다른 정보는 포함 되지 않았습니다. "*
    1. **원인:** 이 문제는 후속 창 서버 업데이트에서 해결 하려고 하는 Windows Server 문제로 인해 발생 합니다. 
    2. **해결 방법:** 연락처 Microsoft 고객 서비스 및 지원에 대 한 지원 (CSS).
<br><br>

### <a name="known-issues-post-installation"></a>알려진된 문제 (설치 후)

이 섹션에서는 사후 설치의 알려진된 문제는 빌드에 **180106.1**합니다.

#### <a name="portal"></a>포털

- 관리자 포털에서 계산 또는 저장소 리소스를 볼 수 있습니다 수 없습니다. 이 업데이트를 잘못 보고 성공으로 하 고 업데이트를 설치 하는 동안 오류가 발생 했음을 나타냅니다. 이 문제가 발생 한 경우 Microsoft CSS에 문의 하십시오.
- 포털에서 빈 대시보드를 볼 수 있습니다. 대시보드를 복구 하려면 포털의 오른쪽 위 모서리에서 톱니 바퀴형 아이콘을 선택 하 고 다음 선택 **기본 설정을 복원**합니다.
- 리소스 그룹의 속성을 볼 때의 **이동** 단추가 비활성화 됩니다. 이 동작은 사용할 수 있습니다. 구독 간 리소스 그룹 이동 현재 지원 되지 않습니다.
- 모든 워크플로에 드롭 다운 목록에서 구독, 리소스 그룹 또는 위치 선택 되는 위치에 대 한 다음 문제 중 하나 이상이 발생할 수 있습니다.

   - 목록 맨 위에 있는 빈 행을 볼 수 있습니다. 여전히 예상 대로 항목을 선택할 수 있습니다.
   - 드롭다운 목록에 있는 항목의 목록이 긴 경우 수 항목 이름 중 하나를 볼 수 없습니다.
   - 구독이 여러 개인 사용자, 리소스 그룹 드롭 다운 목록 비어 있을 수 있습니다.

        > [!NOTE]
        > 마지막 두 개의 문제를 해결 하려면 구독 또는 리소스 그룹 (경우 것)의 이름을 입력할 수 있습니다 또는 PowerShell을 대신 사용할 수 있습니다.

- 분리 된 리소스에서 사용자 구독 결과 삭제 합니다. 이 문제를 해결 먼저 사용자 리소스 또는 전체 리소스 그룹을 삭제 하 고 사용자 구독을 삭제 합니다.
- Azure 스택 포털을 사용 하 여 구독에 대 한 사용 권한을 확인할 수 없는 합니다. 이 문제를 해결 PowerShell을 사용 하 여 사용 권한을 확인할 수 있습니다.
- **서비스 상태** 블레이드 로드 되지 않습니다. 관리자 또는 사용자 포털에서 Azure 스택 서비스 상태 블레이드를 열 때 오류가 표시 되 고 정보를 로드 하지 않습니다. 이는 정상적인 동작입니다. 선택 하 고 서비스 상태를 열 수 있지만이 기능은 아직 제공 되지 않습니다 되지만 Azure 스택의 이후 버전에서 구현 됩니다.

#### <a name="health-and-monitoring"></a>상태 및 모니터링

- <!-- 1264761 - IS ASDK -->  You might see alerts for the *Health controller* component that have the following details:  

   #1 경고:
   - 이름: 인프라 역할 비정상
   - 심각도: 경고
   - 구성 요소: 상태 컨트롤러
   - 설명: 상태 컨트롤러 하트 비트 스캐너를 사용할 수 없습니다. 상태 보고서 및 메트릭에 영향을 줄 수 있습니다.  

  경고 # 2:
   - 이름: 인프라 역할 비정상
   - 심각도: 경고
   - 구성 요소: 상태 컨트롤러
   - 설명: 상태 컨트롤러 오류 스캐너를 사용할 수 없습니다. 상태 보고서 및 메트릭에 영향을 줄 수 있습니다.

  두 가지 경고는 무시 해도 됩니다. 시간에 따라 자동으로 종료 됩니다.  

- 인프라 역할 인스턴스를 다시 부팅 하는 경우 다시 부팅 실패 했음을 나타내는 메시지가 나타날 수 있습니다. 그러나 다시 부팅 실제로 성공 했습니다.



#### <a name="marketplace"></a>Marketplace
- 이 릴리스의 호환성 문제로 인해 일부 마켓플레이스 항목을 제거 되 고 됩니다. 이러한 다시 활성화 됩니다 추가 유효성 검사 후 합니다.
- 사용자 구독 하지 않고 전체 마켓플레이스를 찾아볼 수 있으며, 계획 및 제안 같은 관리 항목을 볼 수 있습니다. 이러한 항목은 사용자에 게 기능입니다.

#### <a name="compute"></a>컴퓨팅
- 사용자에는 지역 중복 저장소를 사용 하 여 가상 컴퓨터를 만들려면 옵션이 제공 됩니다. 이 구성을 사용 하면 가상 컴퓨터 만들기 실패 합니다.
- 가상 컴퓨터 가용성,의 장애 도메인 및 하나의 업데이트 도메인만 집합을 구성할 수 있습니다.
- 가상 컴퓨터 크기 집합을 만들려는 마켓플레이스 본 경험이 없는 경우 크기는 템플릿을 사용 하 여 집합을 만들 수 있습니다.
- 크기 조정 설정을 가상 컴퓨터 크기 집합에 대 한 포털에서 사용할 수 없는 경우 한 대 안으로 사용할 수 있습니다 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)합니다. PowerShell 버전 차이 때문에 사용 해야 합니다는 `-Name` 매개 변수 대신 `-VMScaleSetName`합니다.

#### <a name="networking"></a>네트워킹
- 포털을 사용 하 여 공용 IP 주소와 부하 분산 장치를 만들 수 없습니다. 이 문제를 해결를 부하 분산 장치를 만드는 PowerShell을 사용할 수 있습니다.
- 네트워크 부하 분산 장치를 만들 때 네트워크 주소 변환 (NAT) 규칙을 만들어야 합니다. 이렇게 하지 않으면 부하 분산 장치를 만들면 NAT 규칙을 추가 하려고 할 때 오류가 받게 합니다.
- VM이 되어 해당 IP 주소와 연결 된 후 가상 컴퓨터 (VM)에서 공용 IP 주소를 분리할 수 없습니다. Disassociation 작동으로 나타나지만 이전에 할당 된 공용 IP 주소에 연결 되어 있는 원본 VM입니다. 새 VM에 IP 주소를 다시 할당 하는 경우에이 문제가 발생 (일반적으로 라고는 *VIP 교체*). 앞으로의 모든 새 아니라 원래 연결 되어 있던 VM에 대 한 연결에서이 IP 주소 결과 통해 연결을 시도 합니다. 현재, 새 VM 만들기에 대 한 새 공용 IP 주소를만 사용 해야 합니다.
- Azure 스택 운영자를 배포, 삭제, Vnet 또는 네트워크 보안 그룹을 수정 못할 수 있습니다. 이 문제는 동일한 패키지의 후속 업데이트 시도에 주로 나타납니다. 이 현재 조사 중인 상태인 업데이트와 패키징 문제로 인해 발생 합니다.
- 부하 분산 ILB (내부) Linux 인스턴스를 중단 시키는 백 엔드 Vm에 대 한 MAC 주소를 잘못 처리 합니다.

#### <a name="sqlmysql"></a>SQL/MySQL
- 테 넌 트가 새 SQL 또는 MySQL SKU에 데이터베이스를 만들 수는 1 시간까지 걸릴 수 있으므로 합니다.
- SQL 및 MySQL 리소스 공급자가 수행 되지 않은 서버 호스팅에 직접 항목의 생성은 지원 되지 않으며 일치 하지 않는 상태가 될 수 있습니다.

    > [!NOTE]
    > 없어야 영향 기존 SQL 또는 MySQL 리소스 공급자 사용자에 게 Azure 스택 통합형 시스템 1712 버전으로 업데이트할 때. 현재 SQL 또는 MySQL 리소스 공급자 빌드를 사용 하 여 새 Azure 스택 업데이트를 사용할 수 있는 계속 수 있습니다.

#### <a name="app-service"></a>App Service
- 사용자는 구독에 해당 첫 번째 Azure 기능을 만들기 전에 저장소 리소스 공급자를 등록 해야 합니다.

#### <a name="identity"></a>ID

배포 된 환경에서 Azure Active Directory 페더레이션 서비스 (ADFS)는 **azurestack\azurestackadmin** 계정이 기본 공급자 구독 소유자가 더 이상. 에 로그인 하는 대신는 **관리 포털 / adminmanagement 끝점** 와 **azurestack\azurestackadmin**를 사용할 수 있습니다는 **azurestack\cloudadmin** 하므로 계정 관리 하 고 기본 공급자 구독을 사용 하 여 수 있습니다.

> [!IMPORTANT]
> 경우에는 **azurestack\cloudadmin** 계정이 ADFS 배포 된 환경에서 기본 공급자 구독 소유자가, RDP에 사용 권한을 호스트도가 없습니다. 계속 사용 하는 **azurestack\azurestackadmin** 계정이 나 로컬 관리자 계정 로그인, 액세스 및 필요에 따라 호스트를 관리 합니다.

## <a name="download-the-update"></a>업데이트 다운로드

Azure 스택 1712 업데이트 패키지를 다운로드할 수 있습니다 [여기](https://aka.ms/azurestackupdatedownload)합니다.

## <a name="more-information"></a>자세한 정보

Microsoft는 모니터링 하 고는 권한 있는 끝점 (PEP) 1712 업데이트와 함께 설치를 사용 하 여 업데이트를 다시 시작 하는 방법을 제공 합니다.

- 참조는 [업데이트 권한 있는 끝점 설명서를 사용 하 여 Azure 스택의 모니터링](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update)합니다. 

## <a name="see-also"></a>참고 항목

- 참조 [Azure 스택 개요에서 업데이트를 관리](azure-stack-updates.md) Azure 스택에서 업데이트 관리에 대 한 개요입니다.
- 참조 [Azure 스택에서 업데이트 적용](azure-stack-apply-updates.md) Azure 스택을 사용 하 여 업데이트를 적용 하는 방법에 대 한 자세한 내용은 합니다.
