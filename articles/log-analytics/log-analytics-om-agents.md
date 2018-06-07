---
title: Log Analytics에 Operations Manager 연결 | Microsoft Docs
description: System Center Operations Manager의 기존 투자를 유지 관리하고 Log Analytics로 확장된 기능을 사용하려면 작업 영역으로 Operations Manager를 통합할 수 있습니다.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: 245ef71e-15a2-4be8-81a1-60101ee2f6e6
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: magoedte
ms.openlocfilehash: b11cffcb006ba4f0598bd7f5cf6ed13daad2db42
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="connect-operations-manager-to-log-analytics"></a>Log Analytics에 Operations Manager 연결
System Center Operations Manager의 기존 투자를 유지 관리하고 Log Analytics로 확장된 기능을 사용하려면 Log Analytics 작업 영역으로 Operations Manager를 통합할 수 있습니다.  이렇게 하면 Log Analytics의 기회를 활용하면서도 Operations Manager를 계속 사용하여 다음 작업을 할 수 있습니다.

* Operations Manager를 사용하여 IT 서비스의 상태 모니터링
* 인시던트 및 문제 관리를 지원하는 ITSM 솔루션과 통합 유지 관리
* 온-프레미스 및 Operations Manager로 모니터링하는 공용 클라우드 IaaS 가상 머신에 배포된 에이전트의 수명 주기 관리

System Center Operations Manager와 통합하면 Operations Manager에서 데이터 수집, 저장 및 분석에 Log Analytics의 속도 및 효율성을 사용하여 서비스 작업 전략에 값이 추가됩니다.  Log Analytics를 통해 기존 문제 관리 프로세스를 지원하기 위해 오류 문제를 식별하고 재발을 표시하도록 상호 연결하고 작업할 수 있습니다.  의미 있는 방식으로 이 데이터를 노출하는 다양한 대시보드 및 보고 기능으로 성능, 이벤트 및 경고 데이터를 검사하는 검색 엔진의 유연성은 Operations Manager를 제공하는 Log Analytics의 강점을 보여 줍니다.

Operations Manager 관리 그룹에 대한 에이전트 보고는 Log Analytics 데이터 원본 및 작업 영역에서 활성화한 솔루션에 기반한 서버에서 데이터를 수집합니다.  활성화한 솔루션에 따라 이러한 데이터는 Operations Manager 관리 서버에서 서비스로 직접 전송되거나, 에이전트 관리 시스템에서 수집된 데이터의 양으로 인해 에이전트에서 Log Analytics로 직접 전송됩니다. 관리 서버는 데이터를 서비스에 직접 전달하며, 운영 또는 데이터 웨어하우스 데이터베이스에 기록되지 않습니다.  관리 서버는 Log Analytics와의 연결이 끊어지더라도 Log Analytics와의 통신이 다시 설정될 때까지 데이터를 로컬로 캐시합니다.  관리 서버가 계획된 유지 관리 또는 계획되지 않은 중단으로 인해 오프라인 상태인 경우 관리 그룹의 다른 관리 서버에서 Log Analytics와의 연결을 다시 시작합니다.  

다음 다이어그램은 방향, 포트를 포함하여 System Center Operations Manager 관리 그룹과 Log Analytics에서 관리 서버와 에이전트 간 연결을 보여줍니다.   

![oms-operations-manager-integration-diagram](./media/log-analytics-om-agents/oms-operations-manager-connection.png)

IT 보안 정책이 네트워크의 컴퓨터가 인터넷에 연결하도록 허용하지 않을 경우 OMS 게이트웨이에 연결하여 구성 정보를 받고 사용하도록 설정한 솔루션에 따라 수집된 데이터를 보내도록 관리 서버를 구성할 수 있습니다.  Operations Manager 관리 그룹이 OMS 게이트웨이를 통해 Log Analytics 서비스와 통신할 수 있도록 구성하는 방법에 대한 자세한 정보 및 단계에 대해서는 [OMS 게이트웨이 사용하여 OMS에 컴퓨터 연결](log-analytics-oms-gateway.md)을 참조하세요.  

## <a name="system-requirements"></a>시스템 요구 사항
시작하기 전에 다음 세부 정보를 검토하여 필수 구성 요소를 충족하는지 확인합니다.

* Log Analytics는 System Center Operations Manager 1801, Operations Manager 2016, Operations Manager 2012 SP1 UR6 이상, Operations Manager 2012 R2 UR2 이상만을 지원합니다.  프록시 지원은 Operations Manager 2012 SP1 UR7 및 Operations Manager 2012 R2 UR3에 추가되었습니다.
* 모든 Operations Manager 에이전트는 최소 지원 요구 사항을 만족해야 합니다. 에이전트가 최소 업데이트를 따르고 있는지 확인하고, 그렇지 않은 경우 Windows 에이전트 트래픽이 실패하고 많은 오류가 Operations Manager 이벤트 로그를 채울 수 있습니다.
* Log Analytics 작업 영역.  자세한 내용은 [Log Analytics 시작](log-analytics-get-started.md)을 검토합니다.

### <a name="network"></a>네트워크
아래 정보는 Operations Manager 에이전트, 관리 서버 및 운영 콘솔이 Log Analytics와 통신하는 데 필요한 프록시 및 방화벽 구성 정보를 나열합니다.  각 구성 요소의 트래픽은 네트워크에서 Log Analytics 서비스로 아웃바운드됩니다.     

|리소스 | 포트 번호| HTTP 검사 무시|  
|---------|------|-----------------------|  
|**에이전트**|||  
|\*.ods.opinsights.azure.com| 443 |예|  
|\*.oms.opinsights.azure.com| 443|예|  
|\*.blob.core.windows.net| 443|예|  
|\*.azure-automation.net| 443|예|  
|**관리 서버**|||  
|\*.service.opinsights.azure.com| 443||  
|\*.blob.core.windows.net| 443| 예|  
|\*.ods.opinsights.azure.com| 443| 예|  
|*.azure-automation.net | 443| 예|  
|**OMS에 대한 Operations Manager 콘솔**|||  
|service.systemcenteradvisor.com| 443||  
|\*.service.opinsights.azure.com| 443||  
|\*.live.com| 80 및 443||  
|\*.microsoft.com| 80 및 443||  
|\*.microsoftonline.com| 80 및 443||  
|\*.mms.microsoft.com| 80 및 443||  
|login.windows.net| 80 및 443||  
|portal.loganalytics.io| 80 및 443||
|api.loganalytics.io| 80 및 443||
|docs.loganalytics.io| 80 및 443||  

## <a name="connecting-operations-manager-to-log-analytics"></a>Log Analytics에 Operations Manager 연결
Operations Manager 관리 그룹을 구성하도록 다음과 같은 일련의 단계를 수행하여 Log Analytics 작업 영역 중 하나에 연결합니다.

Operations Manager 관리 그룹이 Log Analytics 작업 영역에 처음으로 등록하고 관리 서버가 프록시 또는 OMS 게이트웨이 서버를 통해 서비스와 통신해야 하는 경우에는 관리 그룹에 대한 프록시 구성을 지정하는 옵션을 운영 콘솔에서 사용할 수 없습니다.  이 옵션을 사용하려면 관리 그룹이 서비스에 등록되어 있어야 합니다.  운영 콘솔을 실행하는 시스템에서 Netsh를 사용하여 관리 그룹의 통합 및 모든 관리 서버를 구성하여 시스템 프록시 구성을 업데이트해야 합니다.  

1. 관리자 권한 명령 프롬프트를 엽니다.
1. 다음 명령을 입력하고 **Enter** 키를 누릅니다.

    `netsh winhttp set proxy <proxy>:<port>`

다음 단계에 따라 Log Analytics와 통합을 완료한 후에는 `netsh winhttp reset proxy`를 실행하여 구성을 제거하고, 운영 콘솔에서 **프록시 서버 구성** 옵션을 사용하여 프록시 또는 OMS 게이트웨이 서버를 지정할 수 있습니다. 

1. Operations Manager 콘솔에서 **관리** 작업 영역을 선택합니다.
2. Operations Management Suite 노드를 확장하고 **연결**을 클릭합니다.
3. **Operations Management Suite에 등록** 링크를 클릭합니다.
4. **Operations Management Suite 등록 마법사: 인증** 페이지에서 OMS 구독과 연결된 관리자 계정의 전자 메일 주소 또는 전화 번호와 암호를 입력하고 **로그인**을 클릭합니다.
5. 성공적으로 인증된 후에 **Operations Management Suite 등록 마법사: 작업 영역 선택** 페이지에 Log Analytics 작업 영역을 선택하라는 메시지가 나타납니다.  둘 이상의 작업 영역이 있는 경우 드롭다운 목록에서 Operations Manager 관리 그룹으로 등록하려는 작업 영역을 선택한 후 **다음**을 클릭합니다.
   
   > [!NOTE]
   > Operations Manager는 한 번에 하나의 Log Analytics 작업 영역을 지원합니다. 이전 작업 영역으로 Log Analytics에 등록된 연결 및 컴퓨터는 Log Analytics에서 제거됩니다.
   > 
   > 
6. **Operations Management Suite 등록 마법사: 요약** 페이지에서 설정을 확인하고 올바른 경우 **만들기**를 클릭합니다.
7. **Operations Management Suite 등록 마법사: 마침** 페이지에서 **닫기**를 클릭합니다.

### <a name="add-agent-managed-computers"></a>에이전트 관리 컴퓨터 추가
Log Analytics 작업 영역과 통합을 구성한 후 Log Analytics와의 연결을 설정하고 에이전트 보고에서 관리 그룹으로 데이터가 수집되지 않습니다. 이는 Log Analytics에 대한 데이터를 수집할 특정 에이전트 관리 컴퓨터를 구성할 때까지 일어나지 않습니다. 컴퓨터 개체를 개별적으로 선택하거나 Windows 컴퓨터 개체를 포함하는 그룹을 선택할 수 있습니다. 논리 디스크 또는 SQL 데이터베이스와 같은 다른 클래스의 인스턴스를 포함하는 그룹을 선택할 수 없습니다.

1. Operations Manager 콘솔을 열고 **관리** 작업 영역을 선택합니다.
2. Operations Management Suite 노드를 확장하고 **연결**을 클릭합니다.
3. 창 오른쪽 작업 제목 아래의 **컴퓨터/그룹 추가** 링크를 클릭합니다.
4. **컴퓨터 검색** 대화 상자에서, Operations Manager에서 모니터링하는 컴퓨터 또는 그룹을 검색할 수 있습니다. Log Analytics에 등록할 컴퓨터 또는 그룹을 선택하고 **추가**를 클릭한 다음, **확인**을 클릭합니다.

운영 콘솔의 **관리** 작업 영역에서 Operations Management Suite 아래의 관리되는 컴퓨터 노드에서 데이터를 수집하도록 구성된 컴퓨터 및 그룹을 볼 수 있습니다.  여기에서 필요에 따라 컴퓨터 및 그룹을 추가하거나 제거할 수 있습니다.

### <a name="configure-proxy-settings-in-the-operations-console"></a>운영 콘솔에서 프록시 설정 구성
내부 프록시 서버가 관리 그룹 및 Log Analytics 서비스 사이에 있는 경우 다음 단계를 수행합니다.  이러한 설정은 관리 그룹에서 중앙 집중식으로 관리되며 Log Analytics에 대한 데이터를 수집하는 범위에 포함된 에이전트 관리 시스템에 배포됩니다.  이는 특정 솔루션이 관리 서버를 우회하고 데이터를 Log Analytics 서비스에 직접 보낼 때 효과적입니다.

1. Operations Manager 콘솔을 열고 **관리** 작업 영역을 선택합니다.
2. Operations Management Suite를 확장한 다음 **연결**을 클릭합니다.
3. OMS 연결 보기에서 **프록시 서버 구성**을 클릭합니다.
4. **Operations Management Suite 마법사: 프록시 서버** 페이지에서 **프록시 서버를 사용하여 Operations Management Suite에 액세스**를 선택하고 포트 번호와 함께 URL을 입력(예: http://corpproxy:80)한 다음, **마침**을 클릭합니다.

프록시 서버에 인증이 필요한 경우 다음 단계를 수행하여 관리 그룹에서 OMS에 보고하는 관리되는 컴퓨터에 전파해야 하는 자격 증명 및 설정을 구성합니다.

1. Operations Manager 콘솔을 열고 **관리** 작업 영역을 선택합니다.
2. **RunAs 구성**에서 **프로필**을 선택합니다.
3. **프로필 프록시로 시스템 센터 관리자 실행** 프로필을 엽니다.
4. 실행 프로필 마법사에서 추가를 클릭하여 실행 계정을 사용합니다. [실행 계정](https://technet.microsoft.com/library/hh321655.aspx)을 만들거나 기존 계정을 사용할 수 있습니다. 이 계정에는 프록시 서버를 통과할 수 있는 권한이 있어야 합니다.
5. 관리할 계정을 설정하려면 **선택된 클래스, 그룹 또는 개체**를 선택하고 **선택...** 을 클릭한 다음 **그룹...** 을 클릭하여 **그룹 검색** 상자를 엽니다.
6. **Microsoft System Center Advisor 모니터링 서버 그룹**을 검색한 다음 선택합니다.  그룹을 선택한 후 **확인**을 클릭하여 **그룹 검색** 상자를 닫습니다.
7. **확인**을 클릭하여 **실행 계정 추가** 상자를 닫습니다.
8. **저장** 을 클릭하여 마법사를 완료하고 변경 내용을 저장합니다.

연결이 만들어지고 Log Analytics에 데이터를 수집 및 보고할 에이전트를 구성한 후 다음 구성이 관리 그룹 순서에 관계 없이 적용됩니다.

* 실행 계정 **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** 가 만들어집니다.  이 계정은 실행 프로필 **Microsoft System Center Advisor Run As Profile Blob**과 연결되고 두 개의 클래스 **수집 서버** 및 **Operations Manager 관리 그룹**을 대상으로 합니다.
* 두 개의 커넥터가 생성됩니다.  첫 번째는 **Microsoft.SystemCenter.Advisor.DataConnector**로 이름이 지정되고 관리 그룹에 있는 모든 클래스의 인스턴스에서 생성된 모든 경고를 Log Analytics로 전달하는 구독으로 자동으로 구성됩니다. 두 번째 커넥터는 **Advisor Connector**이며 OMS 웹 서비스와 통신하고 데이터를 공유합니다.
* 관리 그룹에서 데이터를 수집하도록 선택한 에이전트 및 그룹은 **Microsoft System Center Advisor 모니터링 서버 그룹**에 추가됩니다.

## <a name="management-pack-updates"></a>관리 팩 업데이트
구성이 완료되면 Operations Manager 관리 그룹에서 Log Analytics 서비스와의 연결을 설정합니다.  관리 서버는 웹 서비스와 동기화하고 Operations Manager와 통합하도록 활성화한 솔루션에 대한 관리 팩의 형태로 업데이트된 구성 정보를 수신합니다.   Operations Manager는 이러한 관리 팩의 업데이트를 확인하고 사용할 수 있을 때 자동으로 다운로드하고 가져옵니다.  특히 이 동작을 제어하는 두 개의 규칙이 있습니다.

* **Microsoft.SystemCenter.Advisor.MPUpdate** - 기본 Log Analytics 관리 팩을 업데이트합니다. 기본적으로 12시간마다 실행됩니다.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** - 작업 영역에서 활성화된 솔루션 관리 팩을 업데이트합니다. 기본적으로 5분마다 실행됩니다.

비활성화하여 자동 다운로드를 방지하거나 관리 서버에서 새 관리 팩을 사용할 수 있고 다운로드해야 하는 경우를 결정하도록 OMS와 동기화하는 빈도를 수정하기 위해 이러한 두 규칙을 재정의할 수 있습니다.  [규칙 또는 모니터를 재정의하는 방법](https://technet.microsoft.com/library/hh212869.aspx) 단계를 따라 초 단위 값으로 **Frequency** 매개 변수를 수정하여 동기화 일정을 변경하거나 **Enabled** 매개 변수를 수정하여 규칙을 비활성화합니다.  Operations Manager 관리 그룹 클래스의 모든 개체에 대한 재정의를 대상으로 합니다.

프로덕션 관리 그룹에서 관리 팩 릴리스 제어를 위해 기존 변경 제어 프로세스를 계속 따르려는 경우 규칙을 비활성화하고 업데이트가 허용될 때 특정 시간 동안 활성화할 수 있습니다. 사용자 환경에 개발 또는 QA 관리 그룹이 있고 인터넷에 연결되어 있는 경우 Log Analytics 작업 영역으로 해당 관리 그룹을 구성하여 이 시나리오를 지원할 수 있습니다.  이렇게 하면 프로덕션 관리 그룹으로 릴리스하기 전에 Log Analytics 관리 팩의 반복적인 릴리스를 검토 및 평가할 수 있습니다.

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>Operations Manager 그룹을 새 Log Analytics 작업 영역으로 전환
1. Azure Portal([https://portal.azure.com](https://portal.azure.com))에 로그인합니다.
2. Azure Portal의 왼쪽 아래 모서리에 있는 **추가 서비스**를 클릭합니다. 리소스 목록에서 **Log Analytics**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Log Analytics**를 선택하고 작업 영역을 만듭니다.  
3. Operations Manager 관리자 역할의 구성원인 계정을 사용하여 Operations Manager 콘솔을 열고 **관리** 작업 영역을 선택합니다.
4. Operations Management Suite를 확장하고 **연결**을 선택합니다.
5. 창 중간의 **Operations Management Suite 다시 구성** 링크를 선택합니다.
6. **Operations Management Suite 등록 마법사**의 지시를 따라 이메일 주소 또는 전화 번호, 새 Log Analytics 작업 영역과 연결된 관리자 계정의 암호를 입력합니다.
   
   > [!NOTE]
   > **Operations Management Suite 등록 마법사: 작업 영역 선택** 페이지는 사용 중인 기존 작업 영역을 표시합니다.
   > 
   > 

## <a name="validate-operations-manager-integration-with-log-analytics"></a>Log Analytics와 Operations Manager 통합 유효성 검사
Log Analytics에서 Operations Manager 통합이 성공적인지 확인할 수 있는 몇 가지 방법이 있습니다.

### <a name="to-confirm-integration-from-the-azure-portal"></a>Azure Portal에서 통합을 확인하려면
1. Azure Portal의 왼쪽 아래 모서리에 있는 **추가 서비스**를 클릭합니다. 리소스 목록에서 **Log Analytics**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다.
2. Log Analytics 작업 영역 목록에서 적용 가능한 작업 영역을 선택합니다.  
3. **고급 설정**, **연결된 원본**, **System Center**를 차례로 선택합니다. 
4. System Center Operations Manager 섹션 아래의 테이블에 데이터를 마지막으로 받았을 때 에이전트 및 상태 수와 함께 나열된 관리 그룹의 이름이 표시됩니다.
   
   ![oms-settings-connectedsources](./media/log-analytics-om-agents/oms-settings-connectedsources.png)

### <a name="to-confirm-integration-from-the-operations-console"></a>운영 콘솔에서 통합을 확인하려면
1. Operations Manager 콘솔을 열고 **관리** 작업 영역을 선택합니다.
2. **관리 팩**을 선택하고 **찾기:** 텍스트 상자에 **관리자** 또는 **인텔리전스**를 입력합니다.
3. 활성화한 솔루션에 따라 검색 결과에 나열된 해당 관리 팩을 볼 수 있습니다.  예를 들어 경고 관리 솔루션을 활성화한 경우 관리 팩 Microsoft System Center Advisor 경고 관리가 목록에 나타납니다.
4. **모니터링** 보기에서 **Operations Management Suite\Health State** 보기로 이동합니다.  **관리 서버 상태** 창 아래에서 관리 서버를 선택하고 **상세 보기** 창에서 **인증 서비스 URI** 속성의 값이 Log Analytics 작업 영역 ID와 일치하는지 확인합니다.
   
   ![oms-opsmgr-mg-authsvcuri-property-ms](./media/log-analytics-om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)

## <a name="remove-integration-with-log-analytics"></a>Log Analytics와의 통합 제거
Operations Manager 관리 그룹과 Log Analytics 작업 영역 간의 통합이 더 이상 필요하지 않은 경우 관리 그룹에서 연결 및 구성을 올바르게 제거하는 데 필요한 여러 단계가 있습니다. 다음 절차에서는 관리 그룹 참조를 삭제하여 Log Analytics 작업 영역을 업데이트하고 Log Analytics 커넥터를 삭제한 다음, 서비스와의 통합을 지원하는 관리 팩을 삭제합니다.   

Operations Manager와 통합하도록 활성화한 솔루션용 관리 팩 및 Log Analytics 서비스와의 통합을 지원하는 데 필요한 관리 팩은 관리 그룹에서 쉽게 삭제할 수 없습니다.  일부 Log Analytics 관리 팩이 관련된 다른 관리 팩에 대해 종속성을 갖기 때문입니다.  다른 관리 팩에 대해 종속성이 있는 관리 팩을 삭제하려면 TechNet 스크립트 센터에서 [종속성이 있는 관리 팩 제거](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) 스크립트를 다운로드합니다.  

1. Operations Manager 관리자 역할의 구성원인 계정을 사용하여 Operations Manager 명령 셸을 엽니다.
   
    > [!WARNING]
    > 계속 진행하기 전에 이름에 Advisor 또는 IntelligencePack이 포함된 사용자 지정 관리 팩이 없는지 확인합니다. 그렇지 않으면 다음 단계에 관리 그룹에서 이들을 삭제합니다.
    > 

2. 명령 셸 프롬프트에서 `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
3. 그런 다음 `Get-SCOMManagementPack -name “*IntelligencePack*” | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
4. 다른 System Center Advisor 관리 팩에 대해 종속성이 있는 나머지 모든 관리 팩을 제거하려면 이전에 TechNet 스크립트 센터에서 다운로드한 *RecursiveRemove.ps1*을 사용합니다.  
 
    > [!NOTE]
    > Microsoft System Center Advisor 또는 Microsoft System Center Advisor 내부 관리 팩은 삭제하지 마세요.  
    >  

5. Operations Manager 관리자 역할의 구성원인 계정을 사용하여 Operations Manager 작업 콘솔을 엽니다.
6. **관리** 아래에서 **관리 팩** 노드를 선택하고 **찾기:** 상자에 **관리자**를 입력합니다. 이 경우에도 다음 관리 팩을 관리 그룹으로 가져올 수 있는지 확인합니다.
   
   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Internal
1. Azure Portal에서 Log Analytics 작업 영역에 대한 **고급 설정** 메뉴를 엽니다.
1. **연결된 원본**을 선택한 다음, **System Center**를 선택합니다.
1. 작업 영역에서 제거하려는 관리 그룹의 이름이 표시되어야 합니다.  **마지막 데이터** 열 아래에서 **제거**를 클릭합니다.  
   
    > [!NOTE]
    > 14일간 연결된 관리 그룹에서 감지된 활동이 없을 경우 그 후에는 **제거** 링크를 사용할 수 없습니다.  
    > 

10. 제거를 계속할지 확인하라는 창이 나타납니다.  **예** 를 클릭하여 계속 진행합니다. 

두 커넥터(Microsoft.SystemCenter.Advisor.DataConnector 및 Advisor 커넥터)를 삭제하려면 PowerShell 스크립트를 컴퓨터에 저장하고 다음 예제를 사용하여 실행합니다.

```
    .\OM2012_DeleteConnector.ps1 “Advisor Connector” <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 “Microsoft.SytemCenter.Advisor.DataConnector” <ManagementServerName>
```

> [!NOTE]
> 이 스크립트를 실행하는 컴퓨터(관리 서버가 아니라면)에 관리 그룹의 버전에 따라 Operations Manager 명령 셸을 설치해야 합니다.
> 
> 

```
    param(
    [String] $connectorName,
    [String] $msName="localhost"
    )
    $mg = new-object Microsoft.EnterpriseManagement.ManagementGroup $msName
    $admin = $mg.GetConnectorFrameworkAdministration()
    ##########################################################################################
    # Configures a connector with the specified name.
    ##########################################################################################
    function New-Connector([String] $name)
    {
         $connectorForTest = $null;
         foreach($connector in $admin.GetMonitoringConnectors())
    {
    if($connectorName.Name -eq ${name})
    {
         $connectorForTest = Get-SCOMConnector -id $connector.id
    }
    }
    if ($connectorForTest -eq $null)
    {
         $testConnector = New-Object Microsoft.EnterpriseManagement.ConnectorFramework.ConnectorInfo
         $testConnector.Name = $name
         $testConnector.Description = "${name} Description"
         $testConnector.DiscoveryDataIsManaged = $false
         $connectorForTest = $admin.Setup($testConnector)
         $connectorForTest.Initialize();
    }
    return $connectorForTest
    }
    ##########################################################################################
    # Removes a connector with the specified name.
    ##########################################################################################
    function Remove-Connector([String] $name)
    {
        $testConnector = $null
        foreach($connector in $admin.GetMonitoringConnectors())
       {
        if($connector.Name -eq ${name})
       {
         $testConnector = Get-SCOMConnector -id $connector.id
       }
      }
     if ($testConnector -ne $null)
     {
        if($testConnector.Initialized)
     {
     foreach($alert in $testConnector.GetMonitoringAlerts())
     {
       $alert.ConnectorId = $null;
       $alert.Update("Delete Connector");
     }
     $testConnector.Uninitialize()
     }
     $connectorIdForTest = $admin.Cleanup($testConnector)
     }
    }
    ##########################################################################################
    # Delete a connector's Subscription
    ##########################################################################################
    function Delete-Subscription([String] $name)
    {
      foreach($testconnector in $admin.GetMonitoringConnectors())
      {
      if($testconnector.Name -eq $name)
      {
        $connector = Get-SCOMConnector -id $testconnector.id
      }
    }
    $subs = $admin.GetConnectorSubscriptions()
    foreach($sub in $subs)
    {
      if($sub.MonitoringConnectorId -eq $connector.id)
      {
        $admin.DeleteConnectorSubscription($admin.GetConnectorSubscription($sub.Id))
      }
     }
    }
    #New-Connector $connectorName
    write-host "Delete-Subscription"
    Delete-Subscription $connectorName
    write-host "Remove-Connector"
    Remove-Connector $connectorName
```

나중에 관리 그룹을 Log Analytics 작업 영역에 다시 연결할 계획이 있는 경우 `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` 관리 팩 파일을 다시 가져와야 합니다.  환경에 배포된 System Center Operations Manager 버전에 따라 다음 위치에서 이 파일을 찾을 수 있습니다.

* System Center 2016 - Operations Manager 이상의 `\ManagementPacks` 폴더 아래에 있는 원본 미디어.
* 관리 그룹에 적용된 최신 업데이트 롤업.  Operations Manager 2012의 경우 원본 폴더는 ` %ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups`이고 2012 R2의 경우 `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups`에 있습니다.

## <a name="next-steps"></a>다음 단계
기능을 추가하고 데이터를 수집하려면 [솔루션 갤러리에서 Log Analytics 솔루션 추가](log-analytics-add-solutions.md)를 참조하세요.


