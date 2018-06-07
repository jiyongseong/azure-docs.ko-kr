---
title: Azure Backup용 Log Analytics 데이터 모델
description: 이 문서에서는 Azure Backup 데이터에 대한 Log Analytics 데이터 모델 세부 정보에 대해 설명합니다.
services: backup
documentationcenter: ''
author: JPallavi
manager: vijayts
editor: ''
ms.assetid: dfd5c73d-0d34-4d48-959e-1936986f9fc0
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/24/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d55ec8ac4416fe0a082812584552462292b6dbb7
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Azure Backup 데이터용 Log Analytics 데이터 모델
이 문서에서는 보고 데이터를 Log Analytics로 푸시하는 데 사용되는 데이터 모델에 대해 설명합니다. 이 데이터 모델을 사용하면 사용자 지정 쿼리, 대시보드를 만들고 Log Analytics에서 이를 활용할 수 있습니다. 

## <a name="using-azure-backup-data-model"></a>Azure Backup 데이터 모델 사용
데이터 모델의 일부로 제공되는 다음 필드를 사용하여 요구 사항에 따라 시각적 개체, 사용자 지정 쿼리 및 대시보드를 만들 수 있습니다.

### <a name="alert"></a>경고
이 표에서는 경고 관련 필드에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| AlertUniqueId_s |텍스트 |생성된 경고의 고유 ID |
| AlertType_s |텍스트 |생성된 경고의 형식(예: Backup) |
| AlertStatus_s |텍스트 |경고의 상태(예: Active) |
| AlertOccurenceDateTime_s |날짜/시간 |경고를 만든 날짜 및 시간 |
| AlertSeverity_s |텍스트 |경고의 심각도(예: Critical) |
| EventName_s |텍스트 |이 이벤트의 이름을 나타내며, 항상 AzureBackupCentralReport입니다. |
| BackupItemUniqueId_s |텍스트 |이 경고가 속한 백업 항목의 고유 ID |
| SchemaVersion_s |텍스트 |스키마의 현재 버전을 나타내며, **V1**입니다. |
| State_s |텍스트 |경고 개체의 현재 상태(예: Active, Deleted) |
| BackupManagementType_s |텍스트 |백업 수행에 대한 공급자 유형(예: IaaSVM, 이 경고가 속한 FileFolder) |
| OperationName |텍스트 |현재 작업의 이름(Alert)을 나타냅니다. |
| Category |텍스트 |Log Analytics에 푸시된 진단 데이터의 범주를 나타내며, AzureBackupReport입니다. |
| 리소스 |텍스트 |데이터가 수집되는 리소스이며, Recovery Services 자격 증명 모음 이름이 표시됩니다. |
| ProtectedServerUniqueId_s |텍스트 |이 경고가 속한 보호 대상의 고유 ID |
| VaultUniqueId_s |텍스트 |이 경고가 속한 보호 대상의 고유 ID |
| SourceSystem |텍스트 |현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 |데이터가 수집되는 리소스 ID를 나타내며, Recovery Services 자격 증명 모음 리소스 ID가 표시됩니다. |
| SubscriptionId |텍스트 |데이터가 수집되는 리소스(RS 자격 증명 모음)의 구독 ID를 나타냅니다. |
| ResourceGroup |텍스트 |데이터가 수집되는 리소스(RS 자격 증명 모음)의 리소스 그룹을 나타냅니다. |
| ResourceProvider |텍스트 |데이터가 수집되는 리소스 공급자(Microsoft.RecoveryServices)를 나타냅니다. |
| ResourceType |텍스트 |데이터가 수집되는 리소스의 종류(Vaults)를 나타냅니다. |

### <a name="backupitem"></a>BackupItem
이 표에서는 백업 항목 관련 필드에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| EventName_s |텍스트 |이 이벤트의 이름을 나타내며, 항상 AzureBackupCentralReport입니다. |  
| BackupItemUniqueId_s |텍스트 |백업 항목의 고유 ID |
| BackupItemId_s |텍스트 |백업 항목의 ID |
| BackupItemName_s |텍스트 |백업 항목의 이름 |
| BackupItemFriendlyName_s |텍스트 |백업 항목의 친숙한 이름 |
| BackupItemType_s |텍스트 |백업 항목의 형식(예: VM, FileFolder) |
| ProtectedServerName_s |텍스트 |백업 항목이 속한 보호된 서버의 이름 |
| ProtectionState_s |텍스트 |백업 항목의 현재 보호 상태(예: Protected, ProtectionStopped) |
| SchemaVersion_s |텍스트 |스키마의 현재 버전을 나타내며, **V1**입니다. |
| State_s |텍스트 |백업 항목 개체의 현재 상태(예: Active, Deleted) |
| BackupManagementType_s |텍스트 |백업 수행에 대한 공급자 유형(예: IaaSVM, 이 백업 항목이 속한 FileFolder) |
| OperationName |텍스트 |현재 작업의 이름(BackupItem)을 나타냅니다. |
| Category |텍스트 |Log Analytics에 푸시된 진단 데이터의 범주를 나타내며, AzureBackupReport입니다. |
| 리소스 |텍스트 |데이터가 수집되는 리소스이며, Recovery Services 자격 증명 모음 이름이 표시됩니다. |
| SourceSystem |텍스트 |현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 |데이터가 수집되는 리소스 ID를 나타내며, Recovery Services 자격 증명 모음 리소스 ID가 표시됩니다. |
| SubscriptionId |텍스트 |데이터가 수집되는 리소스(RS 자격 증명 모음)의 구독 ID를 나타냅니다. |
| ResourceGroup |텍스트 |데이터가 수집되는 리소스(RS 자격 증명 모음)의 리소스 그룹을 나타냅니다. |
| ResourceProvider |텍스트 |데이터가 수집되는 리소스 공급자(Microsoft.RecoveryServices)를 나타냅니다. |
| ResourceType |텍스트 |데이터가 수집되는 리소스의 종류(Vaults)를 나타냅니다. |

### <a name="backupitemassociation"></a>BackupItemAssociation
이 표에서는 다양한 엔터티와의 백업 항목 연결에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| EventName_s |텍스트 |이 이벤트의 이름을 나타내며, 항상 AzureBackupCentralReport입니다. |  
| BackupItemUniqueId_s |텍스트 |백업 항목의 고유 ID |
| SchemaVersion_s |텍스트 |스키마의 현재 버전을 나타내며, **V1**입니다. |
| State_s |텍스트 |백업 항목 연결 개체의 현재 상태(예: Active, Deleted) |
| BackupManagementType_s |텍스트 |백업 수행에 대한 공급자 유형(예: IaaSVM, 이 백업 항목이 속한 FileFolder) |
| OperationName |텍스트 |현재 작업의 이름(BackupItemAssociation)을 나타냅니다. |
| Category |텍스트 |Log Analytics에 푸시된 진단 데이터의 범주를 나타내며, AzureBackupReport입니다. |
| 리소스 |텍스트 |데이터가 수집되는 리소스이며, Recovery Services 자격 증명 모음 이름이 표시됩니다. |
| PolicyUniqueId_g |텍스트 |백업 항목이 연결된 정책을 식별하는 고유 ID |
| ProtectedServerUniqueId_s |텍스트 |이 백업 항목이 속한 보호된 서버의 고유 ID |
| VaultUniqueId_s |텍스트 |이 백업 항목이 속한 자격 증명 모음의 고유 ID |
| SourceSystem |텍스트 |현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 |데이터가 수집되는 리소스 ID를 나타내며, Recovery Services 자격 증명 모음 리소스 ID가 표시됩니다. |
| SubscriptionId |텍스트 |데이터가 수집되는 리소스(RS 자격 증명 모음)의 구독 ID를 나타냅니다. |
| ResourceGroup |텍스트 |데이터가 수집되는 리소스(RS 자격 증명 모음)의 리소스 그룹을 나타냅니다. |
| ResourceProvider |텍스트 |데이터가 수집되는 리소스 공급자(Microsoft.RecoveryServices)를 나타냅니다. |
| ResourceType |텍스트 |데이터가 수집되는 리소스의 종류(Vaults)를 나타냅니다. |

### <a name="job"></a>작업
이 표에서는 작업 관련 필드에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| EventName_s |텍스트 |이 이벤트의 이름을 나타내며, 항상 AzureBackupCentralReport입니다. |
| BackupItemUniqueId_s |텍스트 |이 작업이 속한 백업 항목의 고유 ID |
| SchemaVersion_s |텍스트 |스키마의 현재 버전을 나타내며, **V1**입니다. |
| State_s |텍스트 |작업 개체의 현재 상태(예: Active, Deleted) |
| BackupManagementType_s |텍스트 |백업 수행에 대한 공급자 유형(예: IaaSVM, 이 작업이 속한 FileFolder) |
| OperationName |텍스트 |현재 작업의 이름(Job)을 나타냅니다. |
| Category |텍스트 |Log Analytics에 푸시된 진단 데이터의 범주를 나타내며, AzureBackupReport입니다. |
| 리소스 |텍스트 |데이터가 수집되는 리소스이며, Recovery Services 자격 증명 모음 이름이 표시됩니다. |
| ProtectedServerUniqueId_s |텍스트 |이 작업이 속한 보호 대상의 고유 ID |
| VaultUniqueId_s |텍스트 |이 작업이 속한 보호 대상의 고유 ID |
| JobOperation_s |텍스트 |실행되는 작업에 대한 동작(예: Backup, 복원, Backup 구성) |
| JobStatus_s |텍스트 |완료된 작업의 상태(예: Completed, Failed) |
| JobFailureCode_s |텍스트 |발생한 작업 실패로 인한 오류 코드 문자열 |
| JobStartDateTime_s |날짜/시간 |작업 실행이 시작된 날짜 및 시간 |
| BackupStorageDestination_s |텍스트 |백업 저장소의 대상(예: Cloud, Disk)  |
| JobDurationInSecs_s | Number |총 작업 기간(초) |
| DataTransferredInMB_s | Number |이 작업에 대해 전송되는 데이터 크기(MB 단위)|
| JobUniqueId_g |텍스트 |작업을 식별하는 고유 ID |
| SourceSystem |텍스트 |현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 |데이터가 수집되는 리소스 ID를 나타내며, Recovery Services 자격 증명 모음 리소스 ID가 표시됩니다. |
| SubscriptionId |텍스트 |데이터가 수집되는 리소스(RS 자격 증명 모음)의 구독 ID를 나타냅니다. |
| ResourceGroup |텍스트 |데이터가 수집되는 리소스(RS 자격 증명 모음)의 리소스 그룹을 나타냅니다. |
| ResourceProvider |텍스트 |데이터가 수집되는 리소스 공급자(Microsoft.RecoveryServices)를 나타냅니다. |
| ResourceType |텍스트 |데이터가 수집되는 리소스의 종류(Vaults)를 나타냅니다. |

### <a name="policy"></a>정책
이 표에서는 정책 관련 필드에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| EventName_s |텍스트 |이 이벤트의 이름을 나타내며, 항상 AzureBackupCentralReport입니다. |
| SchemaVersion_s |텍스트 |스키마의 현재 버전을 나타내며, **V1**입니다. |
| State_s |텍스트 |정책 개체의 현재 상태(예: Active, Deleted) |
| BackupManagementType_s |텍스트 |백업 수행에 대한 공급자 유형(예: IaaSVM, 이 정책이 속한 FileFolder) |
| OperationName |텍스트 |현재 작업의 이름(Policy)을 나타냅니다. |
| Category |텍스트 |Log Analytics에 푸시된 진단 데이터의 범주를 나타내며, AzureBackupReport입니다. |
| 리소스 |텍스트 |데이터가 수집되는 리소스이며, Recovery Services 자격 증명 모음 이름이 표시됩니다. |
| PolicyUniqueId_g |텍스트 |정책을 식별하는 고유 ID |
| PolicyName_s |텍스트 |지정된 정책의 이름 |
| BackupFrequency_s |텍스트 |백업이 실행되는 빈도(예: 매일, 매주) |
| BackupTimes_s |텍스트 |백업이 예약된 날짜 및 시간 |
| BackupDaysOfTheWeek_s |텍스트 |백업이 예약된 주의 요일 |
| RetentionDuration_s |정수 |구성된 백업에 대한 보존 기간 |
| DailyRetentionDuration_s |정수 |구성된 백업에 대한 총 보존 기간(일) |
| DailyRetentionTimes_s |텍스트 |매일 보존이 구성된 날짜 및 시간 |
| WeeklyRetentionDuration_s |10진수 |구성된 백업에 대한 총 매주 보존 기간 |
| WeeklyRetentionTimes_s |텍스트 |매주 보존이 구성된 날짜 및 시간 |
| WeeklyRetentionDaysOfTheWeek_s |텍스트 |매주 보존에 대해 선택한 주의 요일 |
| MonthlyRetentionDuration_s |10진수 |구성된 백업에 대한 총 보존 기간(월) |
| MonthlyRetentionTimes_s |텍스트 |매월 보존이 구성된 날짜 및 시간 |
| MonthlyRetentionFormat_s |텍스트 |월별 보존에 대한 구성 형식(예: 일 기준 매일, 주 기준 매주) |
| MonthlyRetentionDaysOfTheWeek_s |텍스트 |매월 보존에 대해 선택한 주의 요일 |
| MonthlyRetentionWeeksOfTheMonth_s |텍스트 |월별 보존을 구성한 경우 월의 주(예: First, Last 등) |
| YearlyRetentionDuration_s |10진수 |구성된 백업에 대한 총 보존 기간(연) |
| YearlyRetentionTimes_s |텍스트 |매년 보존이 구성된 날짜 및 시간 |
| YearlyRetentionMonthsOfTheYear_s |텍스트 |매년 보존에 대해 선택한 연도의 달 |
| YearlyRetentionFormat_s |텍스트 |연별 보존에 대한 구성 형식(예: 일 기준 매일, 주 기준 매주) |
| YearlyRetentionDaysOfTheMonth_s |텍스트 |매년 보존에 대해 선택한 월의 날짜 |
| SourceSystem |텍스트 |현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 |데이터가 수집되는 리소스 ID를 나타내며, Recovery Services 자격 증명 모음 리소스 ID가 표시됩니다. |
| SubscriptionId |텍스트 |데이터가 수집되는 리소스(RS 자격 증명 모음)의 구독 ID를 나타냅니다. |
| ResourceGroup |텍스트 |데이터가 수집되는 리소스(RS 자격 증명 모음)의 리소스 그룹을 나타냅니다. |
| ResourceProvider |텍스트 |데이터가 수집되는 리소스 공급자(Microsoft.RecoveryServices)를 나타냅니다. |
| ResourceType |텍스트 |데이터가 수집되는 리소스의 종류(Vaults)를 나타냅니다. |

### <a name="policyassociation"></a>PolicyAssociation
이 표에서는 다양한 엔터티와의 정책 연결에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| EventName_s |텍스트 |이 이벤트의 이름을 나타내며, 항상 AzureBackupCentralReport입니다. |
| SchemaVersion_s |텍스트 |스키마의 현재 버전을 나타내며, **V1**입니다. |
| State_s |텍스트 |정책 개체의 현재 상태(예: Active, Deleted) |
| BackupManagementType_s |텍스트 |백업 수행에 대한 공급자 유형(예: IaaSVM, 이 정책이 속한 FileFolder) |
| OperationName |텍스트 |현재 작업의 이름(PolicyAssociation)을 나타냅니다. |
| Category |텍스트 |Log Analytics에 푸시된 진단 데이터의 범주를 나타내며, AzureBackupReport입니다. |
| 리소스 |텍스트 |데이터가 수집되는 리소스이며, Recovery Services 자격 증명 모음 이름이 표시됩니다. |
| PolicyUniqueId_g |텍스트 |정책을 식별하는 고유 ID |
| VaultUniqueId_s |텍스트 |이 정책이 속한 자격 증명 모음의 고유 ID |
| SourceSystem |텍스트 |현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 |데이터가 수집되는 리소스 ID를 나타내며, Recovery Services 자격 증명 모음 리소스 ID가 표시됩니다. |
| SubscriptionId |텍스트 |데이터가 수집되는 리소스(RS 자격 증명 모음)의 구독 ID를 나타냅니다. |
| ResourceGroup |텍스트 |데이터가 수집되는 리소스(RS 자격 증명 모음)의 리소스 그룹을 나타냅니다. |
| ResourceProvider |텍스트 |데이터가 수집되는 리소스 공급자(Microsoft.RecoveryServices)를 나타냅니다. |
| ResourceType |텍스트 |데이터가 수집되는 리소스의 종류(Vaults)를 나타냅니다. |

### <a name="protectedserver"></a>ProtectedServer
이 표에서는 보호된 서버 관련 필드에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| EventName_s |텍스트 |이 이벤트의 이름을 나타내며, 항상 AzureBackupCentralReport입니다. |
| ProtectedServerName_s |텍스트 |보호된 서버의 이름 |
| SchemaVersion_s |텍스트 |스키마의 현재 버전을 나타내며, **V1**입니다. |
| State_s |텍스트 |보호된 서버 개체의 현재 상태(예: Active, Deleted) |
| BackupManagementType_s |텍스트 |백업 수행에 대한 공급자 유형(예: IaaSVM, 이 보호된 서버가 속한 FileFolder) |
| OperationName |텍스트 |현재 작업의 이름(ProtectedServer)을 나타냅니다. |
| Category |텍스트 |Log Analytics에 푸시된 진단 데이터의 범주를 나타내며, AzureBackupReport입니다. |
| 리소스 |텍스트 |데이터가 수집되는 리소스이며, Recovery Services 자격 증명 모음 이름이 표시됩니다. |
| ProtectedServerUniqueId_s |텍스트 |보호된 서버의 고유 ID |
| RegisteredContainerId_s |텍스트 |백업에 대해 등록된 컨테이너의 ID |
| ProtectedServerType_s |텍스트 |보호된 서버 백업 유형(예: Windows) |
| ProtectedServerFriendlyName_s |텍스트 |보호된 서버의 친숙한 이름 |
| AzureBackupAgentVersion_s |텍스트 |Azure Backup 에이전트의 버전 번호 |
| SourceSystem |텍스트 |현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 |데이터가 수집되는 리소스 ID를 나타내며, Recovery Services 자격 증명 모음 리소스 ID가 표시됩니다. |
| SubscriptionId |텍스트 |데이터가 수집되는 리소스(RS 자격 증명 모음)의 구독 ID를 나타냅니다. |
| ResourceGroup |텍스트 |데이터가 수집되는 리소스(RS 자격 증명 모음)의 리소스 그룹을 나타냅니다. |
| ResourceProvider |텍스트 |데이터가 수집되는 리소스 공급자(Microsoft.RecoveryServices)를 나타냅니다. |
| ResourceType |텍스트 |데이터가 수집되는 리소스의 종류(Vaults)를 나타냅니다. |

### <a name="protectedserverassociation"></a>ProtectedServerAssociation
이 표에서는 다른 엔터티와의 보호된 서버 연결에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| EventName_s |텍스트 |이 이벤트의 이름을 나타내며, 항상 AzureBackupCentralReport입니다. |
| SchemaVersion_s |텍스트 |스키마의 현재 버전을 나타내며, **V1**입니다. |
| State_s |텍스트 |보호된 서버 연결 개체의 현재 상태(예: Active, Deleted) |
| BackupManagementType_s |텍스트 |백업 수행에 대한 공급자 유형(예: IaaSVM, 이 보호된 서버가 속한 FileFolder) |
| OperationName |텍스트 |현재 작업의 이름(ProtectedServerAssociation)을 나타냅니다. |
| Category |텍스트 |Log Analytics에 푸시된 진단 데이터의 범주를 나타내며, AzureBackupReport입니다. |
| 리소스 |텍스트 |데이터가 수집되는 리소스이며, Recovery Services 자격 증명 모음 이름이 표시됩니다. |
| ProtectedServerUniqueId_s |텍스트 |보호된 서버의 고유 ID |
| VaultUniqueId_s |텍스트 |이 보호된 서버가 속한 자격 증명 모음의 고유 ID |
| SourceSystem |텍스트 |현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 |데이터가 수집되는 리소스 ID를 나타내며, Recovery Services 자격 증명 모음 리소스 ID가 표시됩니다. |
| SubscriptionId |텍스트 |데이터가 수집되는 리소스(RS 자격 증명 모음)의 구독 ID를 나타냅니다. |
| ResourceGroup |텍스트 |데이터가 수집되는 리소스(RS 자격 증명 모음)의 리소스 그룹을 나타냅니다. |
| ResourceProvider |텍스트 |데이터가 수집되는 리소스 공급자(Microsoft.RecoveryServices)를 나타냅니다. |
| ResourceType |텍스트 |데이터가 수집되는 리소스의 종류(Vaults)를 나타냅니다. |

### <a name="storage"></a>Storage
이 표에서는 저장소 관련 필드에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| CloudStorageInBytes_s |10진수 |백업에 사용되는 클라우드 백업 저장소 크기이며, |
| ProtectedInstances_s |10진수 |청구에서 프런트 엔드 저장소 계산에 사용된 보호된 인스턴스 수이며, 최신 값을 기준으로 하여 계산됩니다. |
| EventName_s |텍스트 |이 이벤트의 이름을 나타내며, 항상 AzureBackupCentralReport입니다. |
| SchemaVersion_s |텍스트 |스키마의 현재 버전을 나타내며, **V1**입니다. |
| State_s |텍스트 |저장소 개체의 현재 상태(예: Active, Deleted) |
| BackupManagementType_s |텍스트 |백업 수행에 대한 공급자 유형(예: IaaSVM, 이 저장소가 속한 FileFolder) |
| OperationName |텍스트 |현재 작업의 이름(Storage)을 나타냅니다. |
| Category |텍스트 |Log Analytics에 푸시된 진단 데이터의 범주를 나타내며, AzureBackupReport입니다. |
| 리소스 |텍스트 |데이터가 수집되는 리소스이며, Recovery Services 자격 증명 모음 이름이 표시됩니다. |
| ProtectedServerUniqueId_s |텍스트 |저장소가 계산되는 보호된 서버의 고유 ID |
| VaultUniqueId_s |텍스트 |저장소가 계산되는 자격 증명 모음의 고유 ID |
| SourceSystem |텍스트 |현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 |데이터가 수집되는 리소스 ID를 나타내며, Recovery Services 자격 증명 모음 리소스 ID가 표시됩니다. |
| SubscriptionId |텍스트 |데이터가 수집되는 리소스(RS 자격 증명 모음)의 구독 ID를 나타냅니다. |
| ResourceGroup |텍스트 |데이터가 수집되는 리소스(RS 자격 증명 모음)의 리소스 그룹을 나타냅니다. |
| ResourceProvider |텍스트 |데이터가 수집되는 리소스 공급자(Microsoft.RecoveryServices)를 나타냅니다. |
| ResourceType |텍스트 |데이터가 수집되는 리소스의 종류(Vaults)를 나타냅니다. |

### <a name="vault"></a>Vault
이 표에서는 자격 증명 모음 관련 필드에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| EventName_s |텍스트 |이 이벤트의 이름을 나타내며, 항상 AzureBackupCentralReport입니다. |
| SchemaVersion_s |텍스트 |스키마의 현재 버전을 나타내며, **V1**입니다. |
| State_s |텍스트 |자격 증명 모음 개체의 현재 상태(예: Active, Deleted) |
| OperationName |텍스트 |현재 작업의 이름(Vault)을 나타냅니다. |
| Category |텍스트 |Log Analytics에 푸시된 진단 데이터의 범주를 나타내며, AzureBackupReport입니다. |
| 리소스 |텍스트 |데이터가 수집되는 리소스이며, Recovery Services 자격 증명 모음 이름이 표시됩니다. |
| VaultUniqueId_s |텍스트 |자격 증명 모음의 고유 ID |
| VaultName_s |텍스트 |자격 증명 모음의 이름 |
| AzureDataCenter_s |텍스트 |자격 증명 모음이 위치한 데이터 센터 |
| StorageReplicationType_s |텍스트 |자격 증명 모음에 대한 저장소 복제 유형(예: GeoRedundant) |
| SourceSystem |텍스트 |현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 |데이터가 수집되는 리소스 ID를 나타내며, Recovery Services 자격 증명 모음 리소스 ID가 표시됩니다. |
| SubscriptionId |텍스트 |데이터가 수집되는 리소스(RS 자격 증명 모음)의 구독 ID를 나타냅니다. |
| ResourceGroup |텍스트 |데이터가 수집되는 리소스(RS 자격 증명 모음)의 리소스 그룹을 나타냅니다. |
| ResourceProvider |텍스트 |데이터가 수집되는 리소스 공급자(Microsoft.RecoveryServices)를 나타냅니다. |
| ResourceType |텍스트 |데이터가 수집되는 리소스의 종류(Vaults)를 나타냅니다. |

## <a name="next-steps"></a>다음 단계
Azure Backup 보고서를 만들기 위한 데이터 모델을 검토한 후에는 Log Analytics에서 [대시보드를 만들](../log-analytics/log-analytics-dashboards.md) 수 있습니다.
