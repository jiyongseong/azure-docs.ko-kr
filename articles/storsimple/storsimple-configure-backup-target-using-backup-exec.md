---
title: Backup Exec에서 백업 대상으로 StorSimple 8000 시리즈 구성 | Microsoft Docs
description: Veritas Backup Exec을 사용한 StorSimple 백업 대상 구성에 대해 설명합니다.
services: storsimple
documentationcenter: ''
author: harshakirank
manager: matd
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/05/2016
ms.author: hkanna
ms.openlocfilehash: a28b46e10bbdd5331cc665fad3f80523b3aa8a58
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="storsimple-as-a-backup-target-with-backup-exec"></a>Backup Exec에서 백업 대상으로 StorSimple 구성

## <a name="overview"></a>개요

Azure StorSimple은 Microsoft의 하이브리드 클라우드 저장소 솔루션입니다. StorSimple은 Azure Storage 계정을 온-프레미스 솔루션의 확장으로 사용하고 온-프레미스 저장소 및 클라우드 저장소 전반에 걸쳐 데이터를 자동으로 계층화하여 지수 데이터 증가의 복잡성을 해결합니다.

이 문서에서는 Veritas Backup Exec과 StorSimple의 통합 및 두 솔루션을 통합하는 모범 사례에 대해 설명합니다. 또한 StorSimple과 가장 잘 통합되도록 Backup Exec을 설정하는 방법에 대한 권장 사항을 제공합니다. 개별 백업 요구 사항 및 SLA(서비스 수준 약정)를 충족하도록 Backup Exec을 설정하는 가장 좋은 방법은 Veritas 모범 사례, 백업 설계자 및 관리자에게 맡기는 것입니다.

이 문서는 구성 단계와 주요 개념을 설명하지만 단계별 구성 또는 설치 가이드가 아닙니다. 기본 구성 요소 및 인프라가 작업 순서대로 배치되고 설명하는 개념을 지원할 준비가 되었다고 가정합니다.

### <a name="who-should-read-this"></a>이 문서의 대상

이 문서의 정보는 저장소, Windows Server 2012 R2, 이더넷, 클라우드 서비스 및 Backup Exec에 대한 지식이 있는 백업 관리자, 저장소 관리자 및 저장소 설계자에게 가장 유용합니다.

## <a name="supported-versions"></a>지원되는 버전

-   [Backup Exec 16 이상 버전](http://backupexec.com/compatibility)
-   [StorSimple 업데이트 3 이상 버전](storsimple-overview.md#storsimple-workload-summary)


## <a name="why-storsimple-as-a-backup-target"></a>StorSimple이 백업 대상인 이유는 무엇인가요?

StorSimple이 백업 대상으로 적합한 이유는 다음과 같습니다.

-   변경 없이 빠른 백업 대상으로 사용할 백업 응용 프로그램용 표준 로컬 저장소를 제공합니다. StorSimple을 사용하여 최근 백업을 빠르게 복원할 수도 있습니다.
-   클라우드 계층화는 비용 효율적인 Azure Storage를 사용할 수 있도록 Azure 클라우드 저장소 계정과 완벽하게 통합됩니다.
-   재해 복구를 위해 오프사이트 저장소를 자동으로 제공합니다.

## <a name="key-concepts"></a>주요 개념

모든 저장소 솔루션과 마찬가지로 중요한 성공 요소로서 솔루션의 저장소 성능, SLA, 변경률 및 용량 증가 요구 사항을 신중하게 평가해야 합니다. 기본 아이디어는 해당 작업을 수행하기 위해 클라우드 계층, 액세스 시간 및 처리량을 클라우드에 도입하여 StorSimple의 기능에서 중심적인 역할을 수행하는 것입니다.

StorSimple은 잘 정의된 데이터(핫 데이터)의 작업 집합에서 작동하는 응용 프로그램에 대한 저장소를 제공하도록 설계되었습니다. 이 모델에서 데이터의 작업 집합은 로컬 계층에 저장되고 데이터의 나머지 비작업/콜드/보관 집합은 클라우드에서 계층화됩니다. 다음 그림에서 이 모델을 나타냅니다. 평편한 녹색선은 StorSimple 장치의 로컬 계층에 저장된 데이터를 나타냅니다. 빨간색 선은 모든 계층에서 StorSimple 솔루션에 저장된 총 데이터 양을 나타냅니다. 평편한 녹색선과 빨강색 지수 곡선 사이의 간격은 클라우드에 저장된 데이터의 총량을 나타냅니다.

**StorSimple 계층화**
![StorSimple 계층화 다이어그램](./media/storsimple-configure-backup-target-using-backup-exec/image1.jpg)

이 아키텍처를 염두에 두면 StorSimple이 백업 대상으로 작동하는 데 적합하다는 점을 알 수 있습니다. StorSimple을 사용하면 다음 작업을 수행할 수 있습니다.
-   데이터의 로컬 작업 집합에서 가장 빈번하게 복원을 수행합니다.
-   복원이 빈번하게 수행되지 않는 오프사이트 재해 복구 및 오래된 데이터에 대해 클라우드를 사용합니다.

## <a name="storsimple-benefits"></a>StorSimple 이점

StorSimple은 온-프레미스 및 클라우드 저장소에 대한 원활한 액세스를 활용하여 Microsoft Azure와 원활하게 통합된 온-프레미스 솔루션을 제공합니다.

StorSimple은 SSD(Solid-State Device) 및 SAS(Serial-Attached SCSI) 저장소가 있는 온-프레미스 장치와 Azure Storage 간에 자동 계층화를 사용합니다. 자동 계층화는 자주 액세스하는 데이터를 SSD 및 SAS 계층에서 로컬로 유지하지만, 그렇지 않은 데이터는 Azure Storage로 이동합니다.

StorSimple은 다음과 같은 이점을 제공합니다.

-   전례 없는 중복 제거 수준을 달성하기 위해 클라우드를 사용하는 고유한 중복 제거 및 압축 알고리즘
-   고가용성
-   Azure 지역에서 복제를 사용하여 지역에서 복제
-   Azure 통합
-   클라우드의 데이터 암호화
-   향상된 재해 복구 및 규정 준수

StorSimple은 기본 백업 대상과 보조 백업 대상이라는 두 가지 주요 배포 시나리오를 제공하지만 기본적으로는 일반 블록 저장소 장치입니다. StorSimple은 모든 압축 및 중복 제거를 수행합니다. 클라우드와 응용 프로그램 및 파일 시스템 간에 데이터를 원활하게 전송하고 검색합니다.

StorSimple에 대한 자세한 내용은 [StorSimple 8000 시리즈: 하이브리드 클라우드 저장소 솔루션](storsimple-overview.md)을 참조하세요. 또한 [StorSimple 8000 시리즈 기술 사양](storsimple-technical-specifications-and-compliance.md)도 검토할 수 있습니다.

> [!IMPORTANT]
> StorSimple 장치를 백업 대상으로 사용하는 것은 StorSimple 8000 업데이트 3 이상 버전에서만 지원됩니다.

## <a name="architecture-overview"></a>아키텍처 개요

다음 표에서는 장치 모델-아키텍처 초기 지침을 보여 줍니다.

**로컬 및 클라우드 저장소의 StorSimple 용량**

| 저장소 용량       | 8100          | 8600            |
|------------------------|---------------|-----------------|
| 로컬 저장소 용량 | &lt; 10TiB\*  | &lt; 20TiB\*  |
| 클라우드 저장소 용량 | &gt; 200TiB\* | &gt; 500TiB\* |
\*저장소 크기는 중복 제거 또는 압축을 사용한다고 가정하지 않습니다.

**기본 및 보조 백업의 StorSimple 용량**

| Backup 시나리오  | 로컬 저장소 용량  | 클라우드 저장소 용량  |
|---|---|---|
| 기본 백업  | RPO(복구 지점 목표)를 충족하기 위해 빠른 복구용 로컬 저장소에 최근 백업 저장 | 클라우드 용량에 적합한 Backup 기록(RPO) |
| 보조 백업 | 클라우드 용량에 백업 데이터의 보조 복사본을 저장할 수 있습니다.  | 해당 없음  |

## <a name="storsimple-as-a-primary-backup-target"></a>기본 백업 대상인 StorSimple

이 시나리오에서 StorSimple 볼륨은 백업을 위한 유일한 리포지토리로서 백업 응용 프로그램에 제공됩니다. 다음 그림에서는 모든 백업이 백업 및 복원을 위해 계층화된 StorSimple 볼륨을 사용하는 솔루션 아키텍처를 보여 줍니다.

![기본 백업 대상 논리 다이어그램인 StorSimple](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>기본 대상 백업 논리 단계

1.  백업 서버에서 대상 백업 에이전트에 연결하고, 백업 에이전트는 백업 서버에 데이터를 전송합니다.
2.  백업 서버에서 계층화된 StorSimple 볼륨에 데이터를 씁니다.
3.  백업 서버에서 카탈로그 데이터베이스를 업데이트한 다음 백업 작업을 완료합니다.
4.  스냅숏 스크립트에서 StorSimple 클라우드 스냅숏 관리자를 트리거합니다(시작 또는 삭제).
5.  백업 서버에서 보존 정책에 따라 만료된 백업을 삭제합니다.


### <a name="primary-target-restore-logical-steps"></a>기본 대상 복원 논리 단계

1.  백업 서버에서 저장소 리포지토리로부터 해당 데이터를 복원하기 시작합니다.
2.  백업 에이전트에서 백업 서버로부터 데이터를 수신합니다.
3.  백업 서버에서 복원 작업을 완료합니다.

## <a name="storsimple-as-a-secondary-backup-target"></a>보조 백업 대상인 StorSimple

이 시나리오에서 StorSimple 볼륨은 주로 장기 보존 또는 보관에 사용됩니다.

다음 그림에서는 초기 백업 및 복원이 고성능 볼륨을 대상으로 하는 아키텍처를 보여 줍니다. 이러한 백업은 설정된 일정에 따라 계층화된 StorSimple 볼륨에 복사 및 보관됩니다.

보존 정책, 용량 및 성능 요구 사항을 처리할 수 있도록 고성능 볼륨의 크기를 조정하는 것이 중요합니다.

![보조 백업 대상 논리 다이어그램인 StorSimple](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>보조 대상 백업 논리 단계

1.  백업 서버에서 대상 백업 에이전트에 연결하고, 백업 에이전트는 백업 서버에 데이터를 전송합니다.
2.  백업 서버에서 고성능 저장소에 데이터를 씁니다.
3.  백업 서버에서 카탈로그 데이터베이스를 업데이트한 다음 백업 작업을 완료합니다.
4.  백업 서버에서 보존 정책에 따라 StorSimple에 백업을 복사합니다.
5.  스냅숏 스크립트에서 StorSimple 클라우드 스냅숏 관리자를 트리거합니다(시작 또는 삭제).
6.  백업 서버에서 보존 정책에 따라 만료된 백업을 삭제합니다.

### <a name="secondary-target-restore-logical-steps"></a>보조 대상 복원 논리 단계

1.  백업 서버에서 저장소 리포지토리로부터 해당 데이터를 복원하기 시작합니다.
2.  백업 에이전트에서 백업 서버로부터 데이터를 수신합니다.
3.  백업 서버에서 복원 작업을 완료합니다.

## <a name="deploy-the-solution"></a>솔루션 배포

솔루션을 배포하려면 다음 세 단계가 필요합니다.
1. 네트워크 인프라를 준비합니다.
2. 백업 대상으로 StorSimple 장치를 배포합니다.
3. Backup Exec을 배포합니다.

각 단계에 대해 다음 섹션에서 자세히 설명합니다.

### <a name="set-up-the-network"></a>네트워크 설정

StorSimple은 Azure 클라우드와 통합된 솔루션이기 때문에 StorSimple에는 Azure 클라우드에 대한 활성 연결이 필요합니다. 이 연결은 클라우드 스냅숏, 관리 및 메타데이터 전송과 같은 작업에서 오래되고 자주 액세스되지 않는 데이터를 Azure 클라우드 저장소에 계층화하는 데 사용됩니다.

솔루션을 최적으로 수행하려면 다음과 같은 네트워킹 모범 사례를 따르는 것이 좋습니다.

-   StorSimple 계층화를 Azure에 연결하는 링크는 대역폭 요구 사항을 충족해야 합니다. 이렇게 하려면 RPO 및 RTO(복구 시간 목표) SLA와 일치하도록 인프라 스위치에 필요한 QoS(서비스 품질) 수준을 적용합니다.
-   최대 Azure Blob 저장소 액세스 대기 시간은 약 80ms여야 합니다.

### <a name="deploy-storsimple"></a>StorSimple 배포

단계별 StorSimple 배포 지침은 [온-프레미스 StorSimple 장치 배포](storsimple-deployment-walkthrough-u2.md)를 참조하세요.

### <a name="deploy-backup-exec"></a>Backup Exec 배포

Backup Exec 설치 모범 사례는 [Backup Exec 설치에 대한 모범 사례](https://www.veritas.com/support/en_US/article.000068207)(영문)를 참조하세요.

## <a name="set-up-the-solution"></a>솔루션 설정

이 섹션에서는 몇 가지 구성 예를 보여 줍니다. 다음 예제 및 권장 사항에서는 가장 기본적인 구현을 보여 줍니다. 이 구현은 특정 백업 요구 사항에 직접 적용되지 않을 수도 있습니다.

### <a name="set-up-storsimple"></a>StorSimple 설정

| StorSimple 배포 작업  | 추가 설명 |
|---|---|
| 온-프레미스 StorSimple 장치를 배포합니다. | 지원되는 버전: 업데이트 3 이상 버전 |
| 백업 대상을 켭니다. | 다음 명령을 사용하여 백업 대상 모드를 설정하거나 해제하고 상태를 가져옵니다. 자세한 내용은 [StorSimple 장치에 원격으로 연결](storsimple-remote-connect.md)을 참조하세요.</br> 백업 모드 설정: `Set-HCSBackupApplianceMode -enable` </br> 백업 모드 해제: `Set-HCSBackupApplianceMode -disable` </br> 백업 모드 설정의 현재 상태 가져오기: `Get-HCSBackupApplianceMode` |
| 백업 데이터를 저장하는 볼륨에 대한 일반적인 볼륨 컨테이너를 만듭니다. 볼륨 컨테이너에 있는 모든 데이터의 중복을 제거합니다. | StorSimple 볼륨 컨테이너는 중복 제거 도메인을 정의합니다.  |
| StorSimple 볼륨을 만듭니다. | 볼륨 크기가 클라우드 스냅숏 기간에 영향을 주기 때문에 가능하면 예상되는 사용량에 가까운 크기로 볼륨을 만듭니다. 볼륨 크기를 조정하는 방법에 대한 내용은 [보존 정책](#retention-policies)을 참조하세요.</br> </br> 계층화된 StorSimple 볼륨을 사용하고 **자주 액세스하지 않는 보관 데이터에 이 볼륨 사용** 확인란을 선택합니다. </br> 로컬로 고정된 볼륨만 사용하는 것은 지원되지 않습니다. |
| 모든 백업 대상 볼륨에 대한 고유한 StorSimple 백업 정책을 만듭니다. | StorSimple 백업 정책은 볼륨 일관성 그룹을 정의합니다. |
| 스냅숏이 만료될 때 일정을 사용하지 않도록 설정합니다. | 스냅숏은 후처리 작업으로 트리거됩니다. |

### <a name="set-up-the-host-backup-server-storage"></a>호스트 백업 서버 저장소 설정

다음 지침에 따라 호스트 백업 서버 저장소를 설정합니다.  

- [Windows 디스크 관리]에서 만든 스팬 볼륨은 사용하지 않습니다. 스팬 디스크는 지원하지 않습니다.
- 64KB 할당 크기의 NTFS를 사용하여 볼륨을 포맷합니다.
- StorSimple 볼륨을 Backup Exec 서버에 직접 매핑합니다.
    - 물리적 서버에 대한 iSCSI를 사용합니다.
    - 가상 서버에 대한 통과 디스크를 사용합니다.

## <a name="best-practices-for-storsimple-and-backup-exec"></a>StorSimple 및 Backup Exec에 대한 모범 사례

다음 섹션의 지침에 따라 솔루션을 설정합니다.

### <a name="operating-system-best-practices"></a>운영 체제 모범 사례

-   NTFS 파일 시스템에 대한 Windows Server 암호화 및 중복 제거를 사용하지 않도록 설정합니다.
-   StorSimple 볼륨에 Windows Server 조각 모음을 사용하지 않도록 설정합니다.
-   StorSimple 볼륨에 Windows Server 인덱싱을 사용하지 않도록 설정합니다.
-   StorSimple 볼륨에서가 아니라 원본 호스트에서 바이러스 백신 검사를 실행합니다.
-   [작업 관리자]에서 기본 [Windows Server 유지 관리](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx)를 해제합니다. 다음 방법 중 하나로 이 작업을 수행합니다.
   - [Windows 작업 Scheduler]에서 [유지 관리 구성 도구]를 해제합니다.
   - Windows Sysinternals에서 [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx)을 다운로드합니다. PsExec을 다운로드한 후 관리자 권한으로 Azure PowerShell을 실행하고 다음을 입력합니다.
      ```powershell
      psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
      ```

### <a name="storsimple-best-practices"></a>StorSimple 모범 사례

  -   StorSimple 장치가 [업데이트 3 이상](storsimple-install-update-3.md)으로 업데이트되었는지 확인합니다.
  -   iSCSI 및 클라우드 트래픽을 격리합니다. StorSimple과 백업 서버 간의 트래픽에 전용 iSCSI 연결을 사용합니다.
  -   StorSimple 장치가 전용 백업 대상인지 확인합니다. 혼합 워크로드는 RTO 및 RPO에 영향을 주기 때문에 지원하지 않습니다.

### <a name="backup-exec-best-practices"></a>Backup Exec 모범 사례

-   Backup Exec은 StorSimple 볼륨이 아니라 서버의 로컬 드라이브에 설치해야 합니다.
-   Backup Exec 저장소 **동시 쓰기 작업**은 허용되는 최대값으로 설정합니다.
    -   Backup Exec 저장소 **블록 및 버퍼 크기**는 512KB로 설정합니다.
    -   Backup Exec 저장소 **버퍼링된 읽기 및 쓰기**로 설정합니다.
-   StorSimple은 Backup Exec 전체 백업과 증분 백업을 지원합니다. 가상 백업과 차등 백업은 사용하지 않는 것이 좋습니다.
-   Backup 데이터 파일에는 특정 작업에 대한 데이터만 포함되어야 합니다. 예를 들어 다른 작업에 미디어 추가가 허용되지 않습니다.
-   작업 검증을 사용하지 않도록 설정합니다. 필요한 경우 최신 백업 작업 후에 검증을 예약해야 합니다. 이 작업이 백업 창에 주는 영향을 이해하는 것이 중요합니다.
-   **저장소** > **사용자 디스크** > **세부 정보** > **속성**을 차례로 선택합니다. **디스크 공간 미리 할당**을 해제합니다.

최신 Backup Exec 설정 및 이러한 요구 사항을 구현하는 모범 사례에 대해서는 [Veritas 사이트](https://www.veritas.com)를 참조하세요.

## <a name="retention-policies"></a>보존 정책

가장 일반적인 백업 보존 정책 유형 중 하나는 GFS(Grandfather, Father, and Son) 정책입니다. GFS 정책에서는 증분 백업이 매일 수행되고, 전체 백업은 매주 및 매월 수행됩니다. 이 정책을 적용하면 6개의 계층화된 StorSimple 볼륨이 만들어집니다. 하나의 볼륨에는 매주, 매월 및 매년 전체 백업이 포함됩니다. 나머지 5개의 볼륨에는 매일 증분 백업이 저장됩니다.

다음 예제에서는 GFS 회전을 사용합니다. 이 예제에서는 다음을 가정합니다.

-   중복 제거되지 않거나 압축되지 않은 데이터를 사용합니다.
-   전체 백업은 각각 1TiB입니다.
-   매일 증분 백업은 각각 500GiB입니다.
-   4개의 매주 백업이 1개월 동안 보관됩니다.
-   12개의 매월 백업이 1년 동안 보관됩니다.
-   1개의 매년 백업이 10년 동안 보관됩니다.

앞서의 가정에 따라 매월 및 매년 전체 백업에 대해 26TiB의 계층화된 StorSimple 볼륨을 만듭니다. 매일 증분 백업 각각에 대해 5TiB의 계층화된 StorSimple 볼륨을 만듭니다.

| Backup 유형 보존 | 크기(TiB) | GFS 승수\* | 총 용량(TiB)  |
|---|---|---|---|
| 매주 전체 | 1 | 4  | 4 |
| 매일 증분 | 0.5 | 20(주기는 월별 주 수와 동일함) | 12(추가 할당량의 경우 2) |
| 매월 전체 | 1 | 12 | 12 |
| 매년 전체 | 1  | 10 | 10 |
| GFS 요구 사항 |   | 38 |   |
| 추가 할당량  | 4  |   | 42개의 총 GFS 요구 사항  |
\* GFS 승수는 백업 정책 요구 사항을 충족하기 위해 보호하고 유지해야 하는 복사본의 수입니다.

## <a name="set-up-backup-exec-storage"></a>Backup Exec 저장소 설정

### <a name="to-set-up-backup-exec-storage"></a>Backup Exec 저장소를 설정하려면

1.  Backup Exec 관리 콘솔에서 **저장소** > **저장소 구성** > **디스크 기반 저장소** > **다음**을 차례로 선택합니다.

    ![Backup Exec 관리 콘솔 - 저장소 구성 페이지](./media/storsimple-configure-backup-target-using-backup-exec/image4.png)

2.  **디스크 저장소**, **다음**을 차례로 선택합니다.

    ![Backup Exec 관리 콘솔 - 저장소 선택 페이지](./media/storsimple-configure-backup-target-using-backup-exec/image5.png)

3.  대표 이름을 입력합니다(예: **토요일 전체** 및 설명). **다음**을 선택합니다.

    ![Backup Exec 관리 콘솔 - 이름 및 설명 페이지](./media/storsimple-configure-backup-target-using-backup-exec/image7.png)

4.  디스크 저장소 장치를 만들 디스크를 선택한 후 **다음**을 선택합니다.

    ![Backup Exec 관리 콘솔 - 저장소 디스크 선택 페이지](./media/storsimple-configure-backup-target-using-backup-exec/image9.png)

5.  쓰기 작업 수를 **16**으로 늘린 후 **다음**을 선택합니다.

    ![Backup Exec 관리 콘솔 - 동시 쓰기 작업 설정 페이지](./media/storsimple-configure-backup-target-using-backup-exec/image10.png)

6.  설정을 검토한 다음 **마침**을 선택합니다.

    ![Backup Exec 관리 콘솔 - 저장소 구성 요약 페이지](./media/storsimple-configure-backup-target-using-backup-exec/image11.png)

7.  각 볼륨 할당의 끝에서 [StorSimple 및 Backup Exec에 대한 모범 사례](#best-practices-for-storsimple-and-backup-exec)에서 권장하는 설정과 일치하도록 저장소 장치 설정을 변경합니다.

    ![Backup Exec 관리 콘솔 - 저장소 장치 설정 페이지](./media/storsimple-configure-backup-target-using-backup-exec/image12.png)

8.  Backup Exec에 대한 StorSimple 볼륨 할당을 마칠 때까지 1-7단계를 반복합니다.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>StorSimple을 기본 백업 대상으로 설정

> [!NOTE]
> 클라우드에 계층화된 백업에서 데이터를 복원하는 경우 클라우드 속도로 수행됩니다.

다음 그림에서는 일반 볼륨을 백업 작업에 매핑하는 방식을 보여 줍니다. 이 경우 모든 매주 백업은 토요일 전체 디스크에 매핑되고, 증분 백업은 월요일-금요일 증분 디스크에 매핑됩니다. 모든 백업 및 복원은 계층화된 StorSimple 볼륨에서 수행됩니다.

![기본 백업 대상 구성 논리 다이어그램](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>기본 백업 대상인 StorSimple에 대한 GFS 일정 예

다음은 GFS 회전 일정(4주, 매월 및 매년)의 예입니다.

| 빈도/백업 유형 | 전체 | 증분(1-5일)  |   
|---|---|---|
| 매주(1-4주) | 토요일 | 월요일-금요일 |
| 매월  | 토요일  |   |
| 매년 | 토요일  |   |   |


### <a name="assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>Backup Exec 백업 작업에 StorSimple 볼륨 할당

다음 시퀀스에서는 대상 호스트인 Veritas Backup Exec이 Backup Exec 에이전트 지침에 따라 구성되어 있다고 가정합니다.

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>Backup Exec 백업 작업에 StorSimple 볼륨을 할당하려면

1.  Backup Exec 관리 콘솔에서 **호스트** > **백업** > **디스크에 백업**을 차례로 선택합니다.

    ![Backup Exec 관리 콘솔 - 호스트, 백업 및 디스크에 백업 선택](./media/storsimple-configure-backup-target-using-backup-exec/image14.png)

2.  **Backup 정의 속성** 대화 상자의 **Backup**에서 **편집**을 선택합니다.

    ![Backup Exec 관리 콘솔 - 백업 정의 속성 대화 상자](./media/storsimple-configure-backup-target-using-backup-exec/image15.png)

3.  RPO 및 RTO 요구 사항을 충족하고 Veritas 모범 사례를 준수하도록 전체 백업 및 증분 백업을 설정합니다.

4.  **Backup 옵션** 대화 상자에서 **Storage**를 선택합니다.

    ![Backup Exec 관리 콘솔 - 백업 옵션 저장소 대화 상자](./media/storsimple-configure-backup-target-using-backup-exec/image16.png)

5.  백업 일정에 해당하는 StorSimple 볼륨을 할당합니다.

    > [!NOTE]
    > **압축** 및 **암호화 유형**은 **없음**으로 설정합니다.

6.  **확인**에서 **이 작업에 대한 데이터를 확인하지 않습니다** 확인란을 선택합니다. 이 옵션을 사용하면 StorSimple 계층화에 영향을 줄 수 있습니다.

    > [!NOTE]
    > 조각 모음, 인덱싱 및 배경 검증은 StorSimple 계층에 부정적인 영향을 줍니다.

    ![Backup Exec 관리 콘솔 - 백업 옵션 설정 확인](./media/storsimple-configure-backup-target-using-backup-exec/image17.png)

7.  요구 사항을 충족하도록 나머지 백업 옵션을 설정한 후에 **확인**을 선택하여 완료합니다.

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>StorSimple을 보조 백업 대상으로 설정

> [!NOTE]
>클라우드에 계층화된 백업에서 데이터를 복원하는 경우 클라우드 속도로 수행됩니다.

이 모델에서 임시 캐시의 역할을 하는 StorSimple 이외의 저장소 미디어가 있어야 합니다. 예를 들어 RAID(Redundant Array of Independent Disks) 볼륨을 사용하여 공간, I/O(입출력) 및 대역폭을 수용할 수 있습니다. RAID 5, 50 및 10을 사용하는 것이 좋습니다.

다음 그림에서는 일반적인 단기 보존 로컬(서버 쪽) 볼륨 및 장기 보존 보관 볼륨을 보여 줍니다. 이 시나리오에서 모든 백업은 서버 쪽 로컬 RAID 볼륨에서 실행됩니다. 이러한 백업은 정기적으로 복제되고 보관 볼륨에 보관됩니다. 단기 보존 용량 및 성능 요구 사항을 처리할 수 있도록 서버 쪽 로컬 RAID 볼륨의 크기를 조정하는 것이 중요합니다.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>보조 백업 대상 GFS 예제인 StorSimple

![보조 백업 대상인 StorSimple 논리 다이어그램](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetdiagram.png)

다음 표에서는 로컬 디스크 및 StorSimple 디스크에서 백업을 실행하도록 설정하는 방법을 보여 줍니다. 여기에는 개별 용량 및 전체 용량에 대한 요구 사항이 있습니다.

### <a name="backup-configuration-and-capacity-requirements"></a>Backup 구성 및 용량 요구 사항

| Backup 유형 및 보존 | 구성된 저장소 | 크기(TiB) | GFS 승수 | 총 용량\*(TiB) |
|---|---|---|---|---|
| 1주차(전체 및 증분) |로컬 디스크(단기)| 1 | 1 | 1 |
| StorSimple 2-4주 |StorSimple 디스크(장기) | 1 | 4 | 4 |
| 매월 전체 |StorSimple 디스크(장기) | 1 | 12 | 12 |
| 매년 전체 |StorSimple 디스크(장기) | 1 | 1 | 1 |
|GFS 볼륨 크기 요구 사항 |  |  |  | 18*|
\* 총 용량에는 17TiB의 StorSimple 디스크 및 1TiB 로컬 RAID 볼륨이 포함됩니다.


### <a name="gfs-example-schedule-gfs-rotation-weekly-monthly-and-yearly-schedule"></a>GFS 예제 일정: GFS 회전 매주, 매월 및 매년 일정

| 주 | 전체 | 증분 1일차 | 증분 2일차 | 증분 3일차 | 증분 4일차 | 증분 5일차 |
|---|---|---|---|---|---|---|
| 1주차 | 로컬 RAID 볼륨  | 로컬 RAID 볼륨 | 로컬 RAID 볼륨 | 로컬 RAID 볼륨 | 로컬 RAID 볼륨 | 로컬 RAID 볼륨 |
| 2주차 | StorSimple 2-4주 |   |   |   |   |   |
| 3주차 | StorSimple 2-4주 |   |   |   |   |   |
| 4주차 | StorSimple 2-4주 |   |   |   |   |   |
| 매월 | StorSimple 매월 |   |   |   |   |   |
| 매년 | StorSimple 매년  |   |   |   |   |   |   |


### <a name="assign-storsimple-volumes-to-a-backup-exec-archive-and-deduplication-job"></a>Backup Exec 보관/중복 제거 작업에 StorSimple 볼륨 할당

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-archive-and-duplication-job"></a>Backup Exec 보관 및 중복 제거 작업에 StorSimple 볼륨을 할당하려면

1.  Backup Exec 관리 콘솔에서 StorSimple 볼륨에 보관할 작업을 마우스 오른쪽 단추로 클릭한 다음 **백업 정의 속성** > **편집**을 차례로 선택합니다.

    ![Backup Exec 관리 콘솔 - 백업 정의 속성 탭](./media/storsimple-configure-backup-target-using-backup-exec/image19.png)

2.  **스테이지 추가** > **디스크에 복제** > **편집**을 차례로 선택합니다.

    ![Backup Exec 관리 콘솔 - 스테이지 추가](./media/storsimple-configure-backup-target-using-backup-exec/image20.png)

3.  **복제 옵션** 대화 상자에서 **원본** 및 **일정**에 사용할 값을 선택합니다.

    ![Backup Exec 관리 콘솔 - 백업 정의 속성 및 복제 옵션](./media/storsimple-configure-backup-target-using-backup-exec/image21.png)

4.  **저장소** 드롭다운 목록에서 데이터를 저장할 보관 작업을 배치할 StorSimple 볼륨을 선택합니다.

    ![Backup Exec 관리 콘솔 - 백업 정의 속성 및 복제 옵션](./media/storsimple-configure-backup-target-using-backup-exec/image22.png)

5.  **확인**을 선택한 다음 **이 작업에 대한 데이터를 확인하지 않습니다** 확인란을 선택합니다.

    ![Backup Exec 관리 콘솔 - 백업 정의 속성 및 복제 옵션](./media/storsimple-configure-backup-target-using-backup-exec/image23.png)

6.  **확인**을 선택합니다.

    ![Backup Exec 관리 콘솔 - 백업 정의 속성 및 복제 옵션](./media/storsimple-configure-backup-target-using-backup-exec/image24.png)

7.  **Backup** 열에서 새 단계를 추가합니다. 원본에 대해 **증분**을 사용합니다. 대상에 대해 증분 백업 작업이 보관되는 StorSimple 볼륨을 선택합니다. 1-6단계를 반복합니다.

## <a name="storsimple-cloud-snapshots"></a>StorSimple 클라우드 스냅숏

StorSimple 클라우드 스냅숏은 StorSimple 장치에 있는 데이터를 보호합니다. 클라우드 스냅숏을 만드는 것은 로컬 백업 테이프를 오프 사이트 시설로 전달하는 것과 같습니다. Azure 지역 중복 저장소를 사용하는 경우 클라우드 스냅숏을 만드는 것은 백업 테이프를 여러 사이트로 전달하는 것과 같습니다. 재해 발생 후 장치를 복원해야 하는 경우 다른 StorSimple 장치를 온라인 상태로 전환하여 장애 조치를 수행할 수 있습니다. 장애 조치 후에 최근의 클라우드 스냅숏에서 클라우드 속도로 데이터에 액세스할 수 있습니다.

다음 섹션에서는 백업 후처리 중에 StorSimple 클라우드 스냅숏을 시작하고 삭제하는 간단한 스크립트를 만드는 방법에 대해 설명합니다.

> [!NOTE]
> 수동 또는 프로그래밍 방식으로 만든 스냅숏은 StorSimple 스냅숏 만료 정책을 따르지 않습니다. 이러한 스냅숏은 수동 또는 프로그래밍 방식으로 삭제되어야 합니다.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>스크립트를 사용하여 클라우드 스냅숏 시작 및 삭제

> [!NOTE]
> StorSimple 스냅숏을 삭제하기 전에 규정 준수 및 데이터 보존 영향을 신중하게 평가합니다. 사후 백업 스크립트를 실행하는 방법에 대한 자세한 내용은 [Backup Exec 설명서](https://www.veritas.com/support/en_US/15047.html)(영문)를 참조하세요.

### <a name="backup-lifecycle"></a>Backup 주기

![Backup 주기 다이어그램](./media/storsimple-configure-backup-target-using-backup-exec/backuplifecycle.png)

### <a name="requirements"></a>요구 사항

-   스크립트를 실행하는 서버에는 Azure 클라우드 리소스에 대한 액세스 권한이 있어야 합니다.
-   사용자 계정에는 필요한 사용 권한이 있어야 합니다.
-   StorSimple 볼륨과 연관된 StorSimple 백업 정책은 설정해야 하지만 사용하도록 설정되면 안 됩니다.
-   StorSimple 리소스 이름, 등록 키, 장치 이름 및 백업 정책 ID가 필요합니다.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>클라우드 스냅숏을 시작하거나 삭제하려면

1.  [Azure PowerShell 설치](/powershell/azure/overview)
2. [Manage-CloudSnapshots.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1) PowerShell 스크립트를 다운로드 및 설치합니다.
3. 스크립트를 실행하는 서버에서 관리자 권한으로 PowerShell을 실행합니다. `-WhatIf $true`를 포함하는 스크립트를 실행하여 스크립트가 어떻게 변경되는지 확인합니다. 유효성 검사가 완료되면 `-WhatIf $false`를 전달합니다. 아래 명령을 실행합니다.
```powershell
.\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
```
4.  Backup Exec 작업 옵션의 전처리 및 후처리 명령을 편집하여 Backup Exec의 백업 작업에 스크립트를 추가합니다.

    ![Backup Exec 콘솔 - 백업 옵션, 전처리 및 후처리 명령 탭](./media/storsimple-configure-backup-target-using-backup-exec/image25.png)

> [!NOTE]
> 매일 백업 작업의 끝에서 StorSimple 클라우드 스냅숏 백업 정책을 후처리 스크립트로 실행하는 것이 좋습니다. RPO 및 RTO를 충족할 수 있도록 백업 응용 프로그램 환경을 백업 및 복원하는 방법에 대한 자세한 내용은 백업 설계자에게 문의하세요.

## <a name="storsimple-as-a-restore-source"></a>복원 원본인 StorSimple

StorSimple 장치에서 복원하면 모든 블록 저장소 장치에서 복원하는 것처럼 작동합니다. 클라우드에 계층화된 데이터를 복원하는 경우 복원은 클라우드 속도로 수행됩니다. 로컬 데이터의 경우 복원은 장치의 로컬 디스크 속도로 수행됩니다. 복원을 수행하는 방법에 대한 자세한 내용은 Backup Exec 설명서를 참조하세요. Backup Exec 복원 모범 사례를 따르는 것이 좋습니다.

## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple 장애 조치(failover) 및 재해 복구

> [!NOTE]
> 백업 대상 시나리오의 경우 StorSimple 클라우드 어플라이언스는 복원 대상으로 지원되지 않습니다.

재해는 다양한 요인으로 발생할 수 있습니다. 다음 표에서는 일반적인 재해 복구 시나리오를 나열합니다.

| 시나리오 | 영향 | 복구 방법 | 메모 |
|---|---|---|---|
| StorSimple 장치 오류 | Backup 및 복원 작업이 중단됩니다. | 실패한 장치를 교체하고 [StorSimple 장애 조치 및 재해 복구](storsimple-device-failover-disaster-recovery.md)를 수행합니다. | 장치 복구 후에 복원을 수행해야 하는 경우 전체 데이터 작업 집합이 클라우드에서 새 장치로 검색됩니다. 모든 작업이 클라우드 속도로 수행됩니다. 인덱싱 및 카탈로그 작업 재검색 프로세스로 인해 모든 백업 세트를 검색하고 클라우드 계층에서 로컬 장치 계층으로 가져오므로 많은 시간이 소요될 수 있습니다. |
| Backup Exec 서버 오류 | Backup 및 복원 작업이 중단됩니다. | [BEDB(Backup Exec 데이터베이스)의 수동 백업 및 복원을 수행하는 방법](http://www.veritas.com/docs/000041083)에 설명된 대로 백업 서버를 다시 빌드하고 데이터베이스 복원을 수행합니다. | 재해 복구 사이트에서 Backup Exec 서버를 다시 빌드하거나 복원해야 합니다. 데이터베이스를 가장 최근의 지점으로 복원합니다. 복원된 Backup Exec 데이터베이스가 최신 백업 작업과 동기화되지 않은 경우 인덱싱 및 카탈로그 작업이 필요합니다. 이 인덱스 및 카탈로그 재검색 프로세스로 인해 모든 백업 세트를 검색하고 클라우드 계층에서 로컬 장치 계층으로 가져올 수 있습니다. 그러면 더욱 시간이 많이 걸립니다. |
| 백업 서버와 StorSimple이 모두 손실되는 사이트 오류 | Backup 및 복원 작업이 중단됩니다. | 먼저 StorSimple을 복원한 다음 Backup Exec을 복원합니다. | 먼저 StorSimple을 복원한 다음 Backup Exec을 복원합니다. 장치 복구 후에 복원을 수행해야 하는 경우 전체 데이터 작업 집합이 클라우드에서 새 장치로 검색됩니다. 모든 작업이 클라우드 속도로 수행됩니다. |

## <a name="references"></a>참조

이 문서에서는 다음 문서를 참조했습니다.

- [StorSimple 다중 경로 I/O 설정](storsimple-configure-mpio-windows-server.md)
- [저장소 시나리오: 씬 프로비전](http://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [GPT 드라이브 사용](http://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [공유 폴더의 섀도 복사본 설정](http://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>다음 단계

- [백업 세트에서 복원](storsimple-restore-from-backup-set-u2.md)하는 방법에 대해 자세히 알아보세요.
- [장치 장애 조치 및 재해 복구](storsimple-device-failover-disaster-recovery.md)를 수행하는 방법에 대해 자세히 알아보세요.
