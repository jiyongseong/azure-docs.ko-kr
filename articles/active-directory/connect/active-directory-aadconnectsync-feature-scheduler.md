---
title: "Azure AD Connect 동기화: Scheduler | Microsoft Docs"
description: "이 항목에서는 Azure AD Connect 동기화의 기본 제공 스케줄러 기능을 설명합니다."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 6b1a598f-89c0-4244-9b20-f4aaad5233cf
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: df7b16157ccb47a5463570c3ed58b9c44f0b67d0
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-connect-sync-scheduler"></a>Azure AD Connect 동기화: Scheduler
이 토픽은 Azure AD Connect 동기화(동기화 엔진이라고도 함)의 기본 제공 스케줄러를 설명합니다.

이 기능은 빌드 1.1.105.0(2016년 2월에 발표됨)에서 도입되었습니다.

## <a name="overview"></a>개요
Azure AD Connect 동기화는 스케줄러를 사용하여 온-프레미스 디렉터리에서 발생하는 변경 내용을 동기화합니다. 두 개의 스케줄러 프로세스가 있으며, 하나는 암호 동기화용이고 다른 하나는 개체/특성 동기화 및 유지 관리 작업용입니다. 이 항목에서는 후자에 대해 설명합니다.

이전 릴리스에서 개체와 특성에 대한 스케줄러는 동기화 엔진과 무관했습니다. Windows 작업 스케줄러를 사용하거나 별도의 Windows 서비스로 동기화 프로세스를 트리거했습니다. 스케줄러는 동기화 엔진에 기본 제공된 1.1 릴리스를 사용하며 일부 사용자 지정을 수행할 수 있습니다. 새 기본 동기화 빈도는 30분입니다.

스케줄러는 두 작업을 수행합니다.

* **동기화 주기**. 변경 내용 가져오기, 동기화 및 내보내기 프로세스입니다.
* **유지 관리 작업**. 암호 재설정 및 DRS(장치 등록 서비스)에 대한 키와 인증서를 갱신합니다. 작업 로그의 이전 항목을 삭제합니다.

스케줄러 자체가 항상 실행되지만 이러한 작업 중 하나만 실행되도록 구성하거나 하나도 실행되지 않도록 구성할 수 있습니다. 예를 들어 고유한 동기화 주기 프로세스가 필요한 경우 스케줄러에서 이 작업을 사용하지 않도록 설정할 수 있지만 유지 관리 작업은 여전히 실행됩니다.

## <a name="scheduler-configuration"></a>Scheduler 구성
현재 구성 설정을 보려면 PowerShell로 이동하고 `Get-ADSyncScheduler`를 실행합니다. 아래 그림과 같이 표시됩니다.

![GetSyncScheduler](./media/active-directory-aadconnectsync-feature-scheduler/getsynccyclesettings2016.png)

이 cmdlet을 실행했을 때 **사용할 수 없는 동기화 명령 또는 cmdlet** 이 나타나면 PowerShell 모듈이 로드되지 않은 것입니다. 기본 설정보다 PowerShell 제한 수준이 높은 도메인 컨트롤러 또는 서버에서 Azure AD Connect를 실행할 경우 이 문제가 발생할 수 있습니다. 이 오류가 나타나면 `Import-Module ADSync`을(를) 실행하여 cmdlet을 사용할 수 있게 합니다.

* **AllowedSyncCycleInterval**. Azure AD에서 허용되는 동기화 주기 간의 가장 짧은 시간 간격입니다. 이 설정보다 더 자주 동기화할 수 없으며 계속 지원됩니다.
* **CurrentlyEffectiveSyncCycleInterval**. 현재 적용 중인 일정입니다. AllowedSyncInterval보다 낮은 경우 CustomizedSyncInterval(설정된 경우)과 동일한 값을 갖습니다. 1.1.281 이전 빌드를 사용하고 CustomizedSyncCycleInterval을 변경한 경우 이 변경 내용은 다음 동기화 주기 후에 적용됩니다. 1.1.281 빌드의 경우 변경 내용은 즉시 적용됩니다.
* **CustomizedSyncCycleInterval**. 스케줄러를 기본값 30분이 아닌 다른 빈도로 실행하려면 이 설정을 구성합니다. 위의 그림에서는 스케줄러가 1시간마다 실행되도록 설정되었습니다. 이 설정을 AllowedSyncInterval보다 낮은 값으로 설정하면 후자가 사용됩니다.
* **NextSyncCyclePolicyType**. 델타 또는 초기입니다. 다음 실행 시 델타 변경만 처리할지 또는 전체 가져오기 및 동기화를 수행할지 여부를 정의합니다. 후자는 새 규칙 또는 변경된 규칙을 다시 처리하기도 합니다.
* **NextSyncCycleStartTimeInUTC**. 스케줄러가 다음 동기화 주기를 시작하는 다음 시간입니다.
* **PurgeRunHistoryInterval**. 작업 로그가 유지되어야 하는 시간입니다. 이러한 로그는 Synchronization Service Manager에서 검토할 수 있습니다. 기본값은 7일 동안 로그를 유지하는 것입니다.
* **SyncCycleEnabled**. 스케줄러가 작업의 일부로 가져오기, 동기화 및 내보내기 프로세스를 실행 중인지 나타냅니다.
* **MaintenanceEnabled**. 유지 관리 프로세스를 사용할 수 있는지 표시합니다. 인증서/키를 업데이트하고 작업 로그를 삭제합니다.
* **StagingModeEnabled**. [스테이징 모드](active-directory-aadconnectsync-operations.md#staging-mode)를 사용할 수 있는지 표시합니다. 이 설정을 사용하는 경우 내보내기는 무시되지만 가져오기 및 동기화는 계속 실행합니다.
* **SchedulerSuspended**. 업그레이드 중에 스케줄러 실행을 일시적으로 차단하기 위해 Connect에 의해 설정됩니다.

`Set-ADSyncScheduler`(으)로 이러한 모든 설정 중 일부를 변경할 수 있습니다. 다음 매개 변수를 수정할 수 있습니다.

* CustomizedSyncCycleInterval
* NextSyncCyclePolicyType
* PurgeRunHistoryInterval
* SyncCycleEnabled
* MaintenanceEnabled

Azure AD Connect의 이전 빌드에서 **isStagingModeEnabled**는 Set-ADSyncScheduler에서 노출되었습니다. 이 속성을 설정하는 것은 **지원되지 않습니다**. 속성 **SchedulerSuspended**는 Connect에서만 수정되어야 합니다. PowerShell에서 직접 설정하는 것은 **지원되지 않습니다**.

스케줄러 구성은 Azure AD에 저장됩니다. 스테이징 서버가 있는 경우 주 서버를 변경하면 스테이징 서버에도 영향을 줍니다(IsStagingModeEnabled 제외).

### <a name="customizedsynccycleinterval"></a>CustomizedSyncCycleInterval
구문: `Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d -일, HH - 시간, mm - 분, ss - 초

예제: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
3시간마다 실행되도록 스케줄러를 변경합니다.

예제: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
매일 실행되도록 스케줄러를 변경합니다.

### <a name="disable-the-scheduler"></a>스케줄러 사용 안 함  
구성을 변경해야 할 경우 스케줄러를 사용하지 않도록 설정합니다. 예를 들어 [필터링 구성](active-directory-aadconnectsync-configure-filtering.md) 또는 [동기화 규칙을 변경](active-directory-aadconnectsync-change-the-configuration.md)하는 경우입니다.

스케줄러를 비활성화하려면 `Set-ADSyncScheduler -SyncCycleEnabled $false`를 실행합니다.

![스케줄러 사용 안 함](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)

변경했으면 잊지 말고 `Set-ADSyncScheduler -SyncCycleEnabled $true`로 스케줄러를 다시 사용하도록 설정해야 합니다.

## <a name="start-the-scheduler"></a>스케줄러 시작
스케줄러는 기본적으로 30분마다 실행됩니다. 경우에 따라 예약된 주기 사이에서 동기화 주기를 실행하려고 하거나 다른 유형을 실행해야 할 수도 있습니다.

**델타 동기화 주기**  
델타 동기화 주기에는 다음 단계가 포함됩니다.

* 모든 커넥터에서 델타 가져오기
* 모든 커넥터에서 델타 동기화
* 모든 커넥터에서 내보내기

즉시 동기화되어야 하는 긴급한 변경 사항이 있을 수 있습니다. 이것이 주기를 수동으로 실행해야 하는 이유입니다. 수동으로 주기를 실행해야 하는 경우 PowerShell에서 `Start-ADSyncSyncCycle -PolicyType Delta`를 실행합니다.

**전체 동기화 주기**  
다음 구성 변경 사항 중 하나를 수행한 경우 전체 동기화 주기(초기화라고도 함)를 실행해야 합니다.

* 소스 디렉터리에서 가져올 더 많은 개체 및 특성 추가
* 동기화 규칙 변경
* 다른 수의 개체가 포함되도록 [필터링](active-directory-aadconnectsync-configure-filtering.md) 변경

이러한 변경 사항 중 하나를 수행한 경우 전체 동기화 주기를 실행하여 동기화 엔진이 커넥터 공간을 다시 병합할 수 있도록 해야 합니다. 전체 동기화 주기에는 다음 단계가 포함됩니다.

* 모든 커넥터에서 전체 가져오기
* 모든 커넥터에서 전체 동기화
* 모든 커넥터에서 내보내기

전체 동기화 주기를 시작하려면 PowerShell 프롬프트에서 `Start-ADSyncSyncCycle -PolicyType Initial`을 실행합니다. 이 명령은 전체 동기화 주기를 시작합니다.

## <a name="stop-the-scheduler"></a>스케줄러 중지
스케줄러가 현재 동기화 주기를 실행 중인 경우 중지해야 합니다. 예를 들어 설치 마법사를 시작하는 경우 이 오류가 발생합니다.

![SyncCycleRunningError](./media/active-directory-aadconnectsync-feature-scheduler/synccyclerunningerror.png)

동기화 주기를 실행 중일 때 구성을 변경할 수 없습니다. 스케줄러에서 프로세스를 완료할 때까지 기다릴 수 있지만 이를 중지하여 즉시 변경할 수도 있습니다. 현재 주기를 중지해도 나쁜 영향을 주지 않으며 변경 사항은 다음 실행 시 처리됩니다.

1. 먼저 PowerShell cmdlet `Stop-ADSyncSyncCycle`을 사용하여 스케줄러가 현재 주기를 중지하도록 합니다.
2. 1.1.281 이전 빌드를 사용 중인 경우 스케줄러를 중지해도 현재 작업에서 현재 커넥터는 중지되지 않습니다. 커넥터를 강제로 중지하려면 ![StopAConnector](./media/active-directory-aadconnectsync-feature-scheduler/stopaconnector.png) 작업을 수행합니다.
   * 시작 메뉴에서 **동기화 서비스**를 시작합니다. **커넥터**로 이동하여 **실행** 상태인 커넥터를 강조 표시하고 작업에서 **중지**를 선택합니다.

스케줄러가 아직 활성화되어 있으며 다음에 다시 시작합니다.

## <a name="custom-scheduler"></a>사용자 지정 스케줄러
이 섹션에서 설명하는 cmdlet은 빌드 [1.1.130.0](active-directory-aadconnect-version-history.md#111300) 이상에서만 사용할 수 있습니다.

기본 제공 스케줄러가 요구 사항을 충족하지 않으면 PowerShell을 사용하여 커넥터를 예약할 수 있습니다.

### <a name="invoke-adsyncrunprofile"></a>Invoke-ADSyncRunProfile
다음과 같이 커넥터에 대한 프로필을 시작할 수 있습니다.

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

[커넥터 이름](active-directory-aadconnectsync-service-manager-ui-connectors.md)과 [실행 프로필 이름](active-directory-aadconnectsync-service-manager-ui-connectors.md#configure-run-profiles)에 사용할 이름은 [Synchronization Service Manager UI](active-directory-aadconnectsync-service-manager-ui.md)에서 찾을 수 있습니다.

![실행 프로필 호출](./media/active-directory-aadconnectsync-feature-scheduler/invokerunprofile.png)  

`Invoke-ADSyncRunProfile` cmdlet은 동기화됩니다. 즉 커넥터가 성공적으로든 오류가 발생한 상태로든 작업을 완료할 때까지는 컨트롤을 반환하지 않습니다.

커넥터를 예약할 경우 다음 순서대로 예약하는 것이 좋습니다.

1. (전체/델타) Active Directory와 같은 온-프레미스 디렉터리에서 가져오기
2. (전체/델타) Azure AD에서 가져오기
3. (전체/델타) Active Directory와 같은 온-프레미스 디렉터리에서 동기화
4. (전체/델타) Azure AD에서 동기화
5. Azure AD로 내보내기
6. Active Directory와 같은 온-프레미스 디렉터리로 내보내기

이 순서는 기본 제공 스케줄러에서 커넥터를 실행 하는 방법입니다.

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorRunStatus
동기화 엔진이 사용 중인지 또는 유휴 상태인지 모니터링할 수도 있습니다. 이 cmdlet은 동기화 엔진이 유휴 상태이고 커넥터를 실행하고 있지 않은 경우 빈 결과를 반환합니다. 커넥터가 실행 중이면 커넥터의 이름을 반환합니다.

```
Get-ADSyncConnectorRunStatus
```

![커넥터 실행 상태](./media/active-directory-aadconnectsync-feature-scheduler/getconnectorrunstatus.png)  
위의 그림에서 첫 번째 줄은 동기화 엔진이 유휴 상태인 경우이고, 두 번째 줄은 Azure AD 커넥터가 실행 중인 경우입니다.

## <a name="scheduler-and-installation-wizard"></a>Scheduler 및 설치 마법사
설치 마법사를 시작하면 스케줄러가 일시 중단됩니다. 이는 구성을 변경했다고 가정하기 때문이며 동기화 엔진이 실행 중인 경우에는 적용되지 않습니다. 이러한 이유로 동기화 엔진이 동기화 작업을 수행하지 못하므로 설치 마법사를 연 상태로 두지 마세요.

## <a name="next-steps"></a>다음 단계
[Azure AD Connect 동기화](active-directory-aadconnectsync-whatis.md) 구성에 대해 자세히 알아봅니다.

[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.
