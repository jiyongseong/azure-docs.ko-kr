---
title: "Azure Log Analytics를 사용하여 System Center Operations Manager 환경 최적화 | Microsoft Docs"
description: "System Center Operations Manager Health Check 솔루션을 사용하여 일정한 간격으로 환경의 위험 및 상태를 평가할 수 있습니다."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: tysonn
ms.assetid: 49aad8b1-3e05-4588-956c-6fdd7715cda1
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 86484ca2bc7dc14035f48b8f7b1514a4fc471b74
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2018
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-health-check-preview-solution"></a>System Center Operations Manager Health Check(미리 보기) 솔루션을 사용하여 환경 최적화

![System Center Operations Manager Health Check 평가 기호](./media/log-analytics-scom-assessment/scom-assessment-symbol.png)

System Center Operations Manager Health Check 솔루션을 사용하여 일정한 간격으로 System Center Operations Manager 관리 그룹의 위험 및 상태를 평가할 수 있습니다. 이 문서에서는 잠재적인 문제에 대해 올바른 조치를 취할 수 있도록 솔루션을 설치하고, 구성하고, 사용하도록 도와줍니다.

이 솔루션은 배포된 서버 인프라 관련 우선순위가 지정된 권장 사항 목록을 제공합니다. 권장 사항은 신속하게 위험을 이해하고 수정 조치를 취할 수 있도록 네 가지 주요 영역으로 분류되어 있습니다.

권장 사항은 수천 번의 고객 방문에서 Microsoft 엔지니어가 얻은 지식과 경험을 기반으로 합니다. 각 권장 사항은 문제가 중요할 수 있는 이유 및 제안된 변경 내용을 구현하는 방법에 대한 지침을 제공합니다.

조직에 가장 중요한 주요 영역을 선택하여 위험이 없는 정상 환경을 실행 중인 프로그램의 진행을 추적할 수 있습니다.

솔루션을 추가하고 평가가 수행되면 주요 영역의 요약 정보가 인프라에 대한 **System Center Operations Manager Health Check** 대시보드에 표시됩니다. 다음 섹션에서는 **System Center Operations Manager Health Check** 대시보드의 정보를 사용하는 방법을 설명합니다. 여기서는 이 대시보드의 정보를 보고 Operations Manager 환경에 대한 권장 조치를 취할 수 있습니다.

![System Center Operations Manager 솔루션 타일](./media/log-analytics-scom-assessment/log-analytics-scom-healthcheck-tile.png)

![System Center Operations Manager Health Check 대시보드](./media/log-analytics-scom-assessment/log-analytics-scom-healthcheck-dashboard-01.png)

## <a name="installing-and-configuring-the-solution"></a>솔루션 설치 및 구성

이 솔루션은 Microsoft System Operations Manager 2012 서비스 팩(SP) 1 및 2012 R2와 작동합니다.

다음 정보를 사용하여 솔루션을 설치하고 구성합니다.

 - Log Analytics에서 Health Check 솔루션을 사용하려면 먼저 솔루션이 설치되어 있어야 합니다. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview)에서 솔루션을 설치합니다.

 - 작업 영역에 솔루션을 추가한 후에 대시보드의 **System Center Operations Manager Health Check** 타일은 추가 구성이 필요하다는 메시지를 표시합니다. 타일을 클릭하고 페이지에 언급한 구성 단계를 따릅니다.

 ![System Center Operations Manager 대시보드 타일](./media/log-analytics-scom-assessment/scom-configrequired-tile.png)

> [!NOTE]
> Log Analytics의 솔루션 구성 페이지에 설명한 단계를 수행하여 스크립트를 통해 System Center Operations Manager를 구성할 수 있습니다.

 Operations Manager Operations 콘솔을 통해 평가를 구성하려면 아래 단계를 다음 순서대로 수행합니다.
1. [System Center Operations Manager Health Check 평가를 위한 실행 계정 설정](#operations-manager-run-as-accounts-for-log-analytics)  
2. [System Center Operations Manager Health Check 규칙 구성](#configure-the-assessment-rule)

## <a name="system-center-operations-manager-assessment-data-collection-details"></a>System Center Operations Manager 평가 데이터 수집 세부 정보

System Center Operations Manager 평가는 다음과 같은 원본에서 데이터를 수집합니다.

* 레지스트리
* WMI(Windows Management Instrumentation)
* 이벤트 로그
* 파일 데이터
* 지정한 관리 서버에서 Operations Manager에서 직접 PowerShell 및 SQL 쿼리를 사용합니다.  

데이터는 관리 서버에서 수집되고 7일마다 Log Analytics에 전달됩니다.  

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Log Analytics용 Operations Manager 실행 계정

Log Analytics는 부가 가치 서비스를 제공하는 작업을 위해 관리 팩을 빌드합니다. 도메인 사용자 계정과 같은 다른 보안 컨텍스트에서 관리 팩을 실행하려면 각 작업에 워크로드 관련 권한이 필요합니다. 권한이 있는 자격 증명을 통해 Operations Manager 실행 계정을 구성합니다. 자세한 내용은 Operations Manager 설명서의 [실행 계정을 만드는 방법](https://technet.microsoft.com/library/hh321655(v=sc.12).aspx)을 참조하세요. 

다음 정보를 사용하여 System Center Operations Manager Health Check를 위한 Operations Manager 실행 계정을 설정할 수 있습니다.

### <a name="set-the-run-as-account"></a>실행 계정 설정

진행하기 전에 실행 계정은 다음 요구 사항을 충족해야 합니다.

* 모든 Operations Manager 역할 - 관리 서버, 운영, 데이터 웨어하우스 및 ACS 데이터베이스를 호스팅하는 SQL Server, 보고, 웹 콘솔 및 게이트웨이 서버를 지원하는 모든 서버의 로컬 관리자 그룹의 구성원인 도메인 사용자 계정
* 평가 중인 관리 그룹에 대한 Operation Manager 관리자 역할
* 계정에 SQL sysadmin 권한이 없는 경우, [스크립트](#sql-script-to-grant-granular-permissions-to-the-run-as-account)를 실행하여 하나 또는 모든 Operations Manager 데이터베이스를 호스팅하는 각각의 SQL Server 인스턴스에 대해 세부 권한을 계정에 부여합니다.

1. Operations Manager 콘솔에서 **관리** 탐색 버튼을 선택합니다.
2. **실행 구성**에서 **계정**을 클릭합니다.
3. **실행 계정 만들기** 마법사의 **소개** 페이지에서 **다음**을 클릭합니다.
4. **일반 속성** 페이지의 **실행 계정 형식:** 목록에서 **Windows**를 선택합니다.
5. **표시 이름** 텍스트 상자에 표시 이름을 입력하고 선택적으로 **설명** 상자에 설명을 입력한 다음 **다음**을 클릭합니다.
6. **배포 보안** 페이지에서 **더 안전**을 선택합니다.
7. **만들기**를 클릭합니다.  

실행 계정을 만들었으므로 관리 그룹에서 관리 서버를 대상으로 지정하고 워크플로가 자격 증명을 사용하여 실행되도록 미리 정의된 실행 프로필과 연결되어야 합니다.  

1. **실행 구성**, **계정**의 결과 창에서 이전에 만든 계정을 두 번 클릭합니다.
2. **배포** 탭에서 **선택된 컴퓨터** 상자에 대해 **추가**를 클릭하고 계정을 배포할 관리 서버를 추가합니다.  **확인**을 두 번 클릭하여 변경 내용을 저장합니다.
3. **실행 구성**에서 **프로필**을 클릭합니다.
4. *SCOM 평가 프로필*을 검색합니다.
5. 프로필 이름은 *Microsoft System Center Advisor SCOM 평가 실행 프로필*이어야 합니다.
6. 오른쪽 클릭하여 속성을 업데이트하고 앞에서 만든 실행 계정을 추가합니다.

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>실행 계정에 대한 세부적인 사용 권한을 부여하는 SQL 스크립트

운영, 데이터 웨어하우스, ACS 데이터베이스를 호스팅하는 Operations Manager에 사용되는 SQL Server 인스턴스에서 다음 SQL 스크립트를 실행하여 실행 계정에 필요한 권한을 부여합니다.

```
-- Replace <UserName> with the actual user name being used as Run As Account.
USE master

-- Create login for the user, comment this line if login is already created.
CREATE LOGIN [UserName] FROM WINDOWS


--GRANT permissions to user.
GRANT VIEW SERVER STATE TO [UserName]
GRANT VIEW ANY DEFINITION TO [UserName]
GRANT VIEW ANY DATABASE TO [UserName]

-- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
-- NOTE: This command must be run anytime new databases are added to SQL Server instances.
EXEC sp_msforeachdb N'USE [?]; CREATE USER [UserName] FOR LOGIN [UserName];'

Use msdb
GRANT SELECT To [UserName]
Go

--Give SELECT permission on all Operations Manager related Databases

--Replace the Operations Manager database name with the one in your environment
Use [OperationsManager];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager DatawareHouse database name with the one in your environment
Use [OperationsManagerDW];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager Audit Collection database name with the one in your environment
Use [OperationsManagerAC];
GRANT SELECT To [UserName]
GO

--Give db_owner on [OperationsManager] DB
--Replace the Operations Manager database name with the one in your environment
USE [OperationsManager]
GO
ALTER ROLE [db_owner] ADD MEMBER [UserName]

```

### <a name="configure-the-health-check-rule"></a>상태 검사 규칙 구성

System Center Operations Manager Health Check 솔루션의 관리 팩에는 *Microsoft System Center Advisor SCOM 평가 실행 평가 규칙*이라는 규칙이 포함됩니다. 이 규칙은 상태 검사의 실행을 담당합니다. 규칙을 사용하도록 설정하고 빈도를 구성하려면 아래 절차를 사용합니다.

기본적으로 Microsoft System Center Advisor SCOM 평가 실행 평가 규칙은 사용하지 않도록 설정됩니다. 상태 검사를 실행하려면 관리 서버에서 규칙을 사용하도록 설정해야 합니다. 다음 단계를 사용하세요.

#### <a name="enable-the-rule-for-a-specific-management-server"></a>특정 관리 서버에 대한 규칙을 사용하도록 설정

1. Operations Manager Operations 콘솔의 **제작** 작업 영역에서 **규칙** 창의 *Microsoft System Center Advisor SCOM 평가 실행 평가 규칙*이라는 규칙을 검색합니다.
2. 검색 결과에서 *유형: 관리 서버*라는 텍스트를 포함하는 항목을 선택합니다.
3. 규칙을 오른쪽 클릭한 다음 **재정의** > **다음 클래스의 특정 개체: 관리 서버**를 클릭합니다.
4.  사용 가능한 관리 서버 목록에서 규칙을 실행할 관리 서버를 선택합니다.  앞에서 실행 계정에 연결하도록 구성한 것과 같은 관리 서버여야 합니다.
5.  **사용** 매개 변수 값에 대한 재정의 값을 **참**으로 변경해야 합니다.<br><br> ![재정의 매개 변수](./media/log-analytics-scom-assessment/rule.png)

    이 창에 있는 동안 다음 절차를 사용하여 실행 빈도를 구성합니다.

#### <a name="configure-the-run-frequency"></a>실행 빈도 구성

평가는 기본적으로 10,080분(또는 7일) 주기로 실행되도록 구성됩니다. 값을 최소값인 1440분(또는 1일)으로 재정의할 수 있습니다. 이 값은 연속적인 평가 실행 사이에 필요한 최소 시간 간격을 나타냅니다. 간격을 재정의하려면 아래 단계를 사용합니다.

1. Operations Manager 콘솔의 **제작** 작업 영역에서 **규칙** 섹션의 *Microsoft System Center Advisor SCOM 평가 실행 평가 규칙*이라는 규칙을 검색합니다.
2. 검색 결과에서 *유형: 관리 서버*라는 텍스트를 포함하는 항목을 선택합니다.
3. 규칙을 오른쪽 클릭한 다음 **Override the Rule**(규칙 재정의) > **다음 클래스의 모든 개체: 관리 서버**를 클릭합니다.
4. **간격** 매개 변수 값을 원하는 간격 값으로 변경합니다. 아래 예의 경우 값이 1440분(1일)으로 설정되어 있습니다.<br><br> ![간격 매개 변수](./media/log-analytics-scom-assessment/interval.png)<br>  

    값이 1440분 미만으로 설정되면 규칙이 하루 간격으로 실행됩니다. 이 예의 경우 규칙이 간격 값을 무시하고 하루 빈도로 실행됩니다.


## <a name="understanding-how-recommendations-are-prioritized"></a>권장 사항 우선 순위 이해

작성된 모든 권장 구성은 권장 사항의 상대적 중요도를 식별하는 가중치 값을 제공합니다. 10개의 가장 중요한 권장 사항만 표시됩니다.

### <a name="how-weights-are-calculated"></a>가중치 계산 방법

가중치는 3개의 주요 요인을 기반으로 하는 집계 값입니다.

- 식별된 문제로 인해 문제가 발생될 수 있는 *확률* 입니다. 확률이 높을수록 권장 사항에 대한 전체 점수가 커집니다.
- 문제가 발생된 경우 조직에 대한 문제의 *영향* 입니다. 영향이 높을수록 권장 사항에 대한 전체 점수가 커집니다.
- 권장 구성을 구현하는 데 필요한 *노력* 입니다. 노력이 높을수록 권장 사항에 대한 전체 점수가 작아집니다.

각 권장 사항에 대한 가중치는 각 주요 영역에 사용할 수 있는 총 점수에 대한 백분율로 표현됩니다. 예를 들어 가용성 및 비즈니스 연속성 주요 영역의 권장 사항 점수가 5%이면 해당 권장 사항을 구현하면 전반적인 가용성 및 비즈니스 연속성 점수가 5% 상승합니다.

### <a name="focus-areas"></a>주요 영역

**가용성 및 비즈니스 연속성** - 이 주요 영역은 서비스 가용성, 인프라 복원성 및 비즈니스 보호에 대한 권장 사항을 보여 줍니다.

**성능 및 확장성** - 이 주요 영역은 조직의 IT 인프라를 확장하고, IT 환경이 현재 성능 요구 사항을 충족하며, 변화하는 인프라 요구 사항에 대응할 수 있도록 하는 데 도움이 되는 권장 사항을 보여 줍니다.

**업그레이드, 마이그레이션 및 배포** - 기존 인프라에서 SQL Server를 업그레이드, 마이그레이션 및 배포하는 데 도움이 되는 권장 사항을 보여 주는 주요 영역입니다.

**운영 및 모니터링** - IT 운영을 간소화하며, 예방 유지 관리를 구현하고, 성능을 최대화하는 데 도움이 되는 권장 사항을 보여 주는 주요 영역입니다.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>모든 주요 영역에서 100%의 점수를 목표로 해야 하나요?

그럴 필요는 없습니다. 권장 사항은 수천 번의 고객 방문에서 Microsoft 엔지니어가 얻은 지식과 경험을 기반으로 합니다. 그러나 두 서버 인프라는 동일하지 않으며 특정 권장 사항은 거의 사용자와 관련 될 수 있습니다. 예를 들어, 가상 머신이 인터넷에 노출되지 않는 경우 일부 보안 권장 사항의 관련성은 떨어질 수 있습니다. 일부 가용성 권장 사항은 우선순위가 낮은 임시 데이터 수집 및 보고를 제공하는 서비스와는 관련성이 떨어질 수 있습니다. 성숙한 비즈니스에 중요한 문제는 시작에 덜 중요할 수 있습니다. 우선하는 주요 영역을 식별하고 시간이 지남에 따라 다음 점수가 어떻게 변경되는지 확인할 수 있습니다.

모든 권장 사항에는 중요한 이유에 대한 지침이 포함됩니다. IT 서비스의 특성 및 조직의 비즈니스 요구를 고려해 볼 때, 이 가이드를 사용하여 권장 사항 구현이 사용자에 적절한지 여부를 평가해야 합니다.

## <a name="use-health-check-focus-area-recommendations"></a>상태 검사 사용 초점 영역 권장 사항

Log Analytics에서 상태 검사 솔루션을 사용하려면 먼저 솔루션이 설치되어 있어야 합니다. 솔루션 설치에 대한 자세한 내용은 [솔루션 갤러리에서 Log Analytics 솔루션 추가](log-analytics-add-solutions.md)를 참조하세요. 설치 후 OMS 포털의 개요 페이지에서 Operations Manager Health Check 타일을 사용하여 권장 사항의 요약을 볼 수 있습니다.

인프라에 대한 요약된 규정 준수 평가를 본 다음 세부 권장 사항을 확인합니다.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>주요 영역에 대한 권장 사항을 보고 수정 작업을 수행하려면
1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.
2. Azure Portal의 왼쪽 아래 모서리에 있는 **추가 서비스**를 클릭합니다. 리소스 목록에서 **Log Analytics**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Log Analytics**를 선택합니다.
3. Log Analytics 구독 창에서 작업 영역을 선택한 다음 **OMS 포털** 타일을 선택합니다.  
4. **개요** 페이지에서 **System Center Operations Manager Health Check** 타일을 클릭합니다.
5. **System Center Operations Manager Health Check** 페이지에서 주요 영역 블레이드 중 하나의 요약 정보를 검토한 다음 주요 영역 하나를 클릭하여 해당 주요 영역에 대한 권장 사항을 봅니다.
6. 주요 영역 페이지에서 사용자 환경에 대해 우선순위가 지정된 권장 사항을 볼 수 있습니다. 권장하는 이유에 대한 세부 정보를 보려면 **영향을 받는 개체** 아래에서 해당 권장 사항을 클릭합니다.<br><br> ![주요 영역](./media/log-analytics-scom-assessment/log-analytics-scom-healthcheck-dashboard-02.png)<br>
7. **권장 조치**에 제안된 올바른 조치를 수행할 수 있습니다. 항목의 주소가 지정되면, 이후 평가는 수행된 권장 조치 및 늘어난 규정 준수 점수를 기록합니다. 수정된 항목은 **전달된 개체**로 나타납니다.

## <a name="ignore-recommendations"></a>권장 사항 무시

무시하려는 권장 사항이 있는 경우 Log Analytics에서 평가 결과에 권장 사항이 표시되는 것을 방지하는 데 사용할 텍스트 파일을 만들 수 있습니다.

[!INCLUDE[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>무시할 권장 사항을 식별하려면
1. Azure Portal에서 선택한 작업 영역에 대한 Log Analytics 작업 영역 페이지에서 **로그 검색** 타일을 클릭합니다.
2. 다음 쿼리를 사용하여 사용자 환경의 컴퓨터에 대해 실패한 권장 사항을 나열합니다.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > 작업 영역을 [새 Log Analytics 쿼리 언어](log-analytics-log-search-upgrade.md)로 업그레이드한 경우에는 위 쿼리가 다음과 같이 변경됩니다.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    로그 검색 쿼리를 보여 주는 스크린샷은 다음과 같습니다.<br><br> ![로그 검색](./media/log-analytics-scom-assessment/scom-log-search.png)<br>

3. 무시할 권장 사항을 선택합니다. RecommendationId 값은 다음 절차에서 사용됩니다.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>IgnoreRecommendations.txt 텍스트 파일을 만들고 사용하려면

1. IgnoreRecommendations.txt라는 파일을 만듭니다.
2. Log Analytics에서 무시할 각 권장 사항에 대한 RecommendationId를 별도의 줄에 붙여 넣거나 입력한 다음 파일을 저장하고 닫습니다.
3. Log Analytics에서 권장 사항을 무시할 각 컴퓨터의 다음 폴더에 파일을 둡니다.
4. Operations Manager 관리 서버 - *SystemDrive*:\Program Files\Microsoft System Center 2012 R2\Operations Manager\Server.

### <a name="to-verify-that-recommendations-are-ignored"></a>권장 사항이 무시되었는지 확인하려면

1. 예약된 다음 평가가 실행된 후(기본적으로 7일마다) 지정된 권장 사항이 상태 검사 대시보드에 무시됨으로 표시됩니다.
2. 다음 로그 검색 쿼리를 사용하여 무시된 모든 권장 사항을 나열할 수 있습니다.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    >[!NOTE]
    > 작업 영역을 [새 Log Analytics 쿼리 언어](log-analytics-log-search-upgrade.md)로 업그레이드한 경우에는 위 쿼리가 다음과 같이 변경됩니다.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Ignore" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. 무시된 권장 사항을 나중에 보려면 IgnoreRecommendations.txt 파일을 제거합니다. 또는 파일에서 RecommendationID를 제거할 수도 있습니다.

## <a name="system-center-operations-manager-health-check-solution-faq"></a>System Center Operations Manager Health Check 솔루션 FAQ

*내 Log Analytics 작업 영역에 Health Check 솔루션을 추가했습니다. 하지만 권장 사항이 표시되지 않습니다. 이유* 솔루션을 추가한 후, 다음 단계 보기를 사용하여 Log Analytics 대시보드의 권장 사항을 봅니다.  

- [System Center Operations Manager Health Check 평가를 위한 실행 계정 설정](#operations-manager-run-as-accounts-for-log-analytics)  
- [System Center Operations Manager Health Check 규칙 구성](#configure-the-health-check-rule)


*검사 실행 주기를 구성하는 방법이 있나요?* 예. [실행 빈도 구성](#configure-the-run-frequency)을 참조하세요.

*System Center Operations Manager Assessment 솔루션을 추가한 후 다른 서버가 발견되면 이 서버를 검사하나요?* 예, 검색된 이후 기본적으로 7일마다 검사됩니다.

*데이터 수집을 수행하는 프로세스의 이름은 무엇인가요?* AdvisorAssessment.exe

*AdvisorAssessment.exe 프로세스가 왜 실행되나요?* AdvisorAssessment.exe는 상태 검사 규칙을 사용하도록 설정된 관리 서버의 HealthService 프로세스에서 실행됩니다. 이 프로세스를 사용하는 경우 전체 환경에 대한 검색은 원격 데이터 수집을 통해 수행됩니다.

*데이터 수집에 시간이 얼마나 걸리나요?* 서버의 데이터 수집은 약 1시간이 걸립니다. Operations Manager 인스턴스 또는 데이터베이스가 많은 환경에서는 더 오래 걸릴 수 있습니다.

*평가 간격을 1440분 미만으로 설정하면 어떻게 되나요?* 평가는 최다 하루에 한번 실행하도록 미리 구성됩니다. 간격 값을 1440분 미만의 값으로 재정의하면 평가는 1440분을 간격 값으로 사용합니다.

*필수 구성 요소 오류가 있는지 어떻게 알 수 있나요?* 상태 검사가 실행되었는데 결과가 보이지 않는다면 검사의 일부 필수 구성 요소에 오류가 있을 가능성이 있습니다. 로그 검색에서 `Operation Solution=SCOMAssessment` 및 `SCOMAssessmentRecommendation FocusArea=Prerequisites`를 실행하여 실패한 필수 구성 요소를 볼 수 있습니다.

*필수 구성 요소 오류에 `Failed to connect to the SQL Instance (….).` 메시지가 있습니다. 문제가 무엇인가요?* 데이터를 수집하는 프로세스 AdvisorAssessment.exe는 관리 서버의 HealthService 프로세스에서 실행됩니다. 상태 검사의 일환으로 이 프로세스는 Operations Manager 데이터베이스가 있는 SQL Server에 연결을 시도합니다. 이 오류는 방화벽 규칙이 SQL Server 인스턴스에 대한 연결을 차단하는 경우 발생할 수 있습니다.

*어떤 유형의 데이터를 수집하나요?* 다음 유형의 데이터를 수집합니다. Windows PowerShell, SQL 쿼리, 파일 정보 수집기를 통해 - WMI 데이터 - Registry 데이터 - EventLog 데이터 - Operations Manager 데이터.

*왜 실행 계정을 구성해야 하나요?* Operations Manager의 경우 다양한 SQL 쿼리가 실행됩니다. 이러한 항목이 실행되려면 필요한 권한이 있는 실행 계정을 사용해야 합니다. 또한 WMI를 쿼리하려면 로컬 관리자 자격 증명이 필요합니다.

*왜 상위 10개의 권장 사항만을 표시하나요?* 엄청나게 포괄적인 작업을 나열하는 대신, 먼저 우선 순위가 지정된 권장 사항 해결에 주의를 기울이는 것이 좋습니다. 권장 사항을 해결한 후에 추가 권장 사항을 사용할 수 있습니다. 자세한 목록을 참조하는 것을 선호하는 경우 로그 검색을 사용하여 모든 권장 사항을 볼 수 있습니다.

*권장 사항을 무시하는 방법이 있나요?* 예, [권장 사항 무시](#Ignore-recommendations)를 참조하세요.


## <a name="next-steps"></a>다음 단계

- [로그를 검색하여](log-analytics-log-searches.md) 상세 System Center Operations Manager Health Check 데이터 및 권장 사항의 분석 방법을 알아봅니다.
