---
title: Azure Log Analytics의 DNS 분석 솔루션 | Microsoft Docs
description: Log Analytics의 DNS 분석 솔루션을 설정 및 사용하여 보안, 성능 및 작업 관련 정보를 DNS 인프라에 수집합니다.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: f44a40c4-820a-406e-8c40-70bd8dc67ae7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: magoedte
ms.openlocfilehash: 6a59cf8b9444fe7cb197501c51d10dae81acb027
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>DNS 분석 미리 보기 솔루션으로 DNS 인프라에 대한 정보 수집

![DNS 분석 기호](./media/log-analytics-dns/dns-analytics-symbol.png)

이 문서에서는 Azure Log Analytics의 Azure DNS 분석 솔루션을 설정 및 사용하여 보안, 성능 및 작업 관련 정보를 DNS 인프라에 수집하는 방법을 설명합니다.

DNS 분석은 다음을 수행하는 데 도움을 줍니다.

- 악의적인 도메인 이름을 확인하려는 클라이언트 식별
- 부실 리소스 레코드 식별
- 자주 쿼리되는 도메인 이름 및 talkative DNS 클라이언트 식별
- DNS 서버에서 요청 부하 보기
- 동적 DNS 등록 오류 보기

이 솔루션은 DNS 서버에서 Windows DNS 분석 및 감사 로그와 기타 관련 데이터를 수집 및 분석하고 상관 관계를 파악합니다.

## <a name="connected-sources"></a>연결된 소스

다음 표는 이 솔루션이 지원하는 연결된 원본을 설명합니다.

| **연결된 원본** | **지원** | **설명** |
| --- | --- | --- |
| [Windows 에이전트](log-analytics-windows-agent.md) | 예 | 솔루션이 Windows 에이전트에서 DNS 정보를 수집합니다. |
| [Linux 에이전트](log-analytics-linux-agents.md) | 아니오 | 솔루션이 직접 Linux 에이전트에서 DNS 정보를 수집하지 않습니다. |
| [System Center Operations Manager 관리 그룹](log-analytics-om-agents.md) | 예 | 솔루션이 연결된 Operations Manager 관리 그룹의 에이전트에서 DNS 정보를 수집합니다. Operations Manager 에이전트에서 Operations Management Suite로 직접 연결은 필요하지 않습니다. 데이터는 관리 그룹에서 Operations Management Suite 리포지토리로 전달됩니다. |
| [Azure 저장소 계정](log-analytics-azure-storage.md) | 아니오 | Azure Storage가 솔루션에서 사용되지 않습니다. |

### <a name="data-collection-details"></a>데이터 수집 세부 정보

솔루션이 Log Analytics가 설치된 DNS 서버에서 DNS 인벤토리 및 DNS 이벤트 관련 데이터를 수집합니다. 이 데이터는 Log Analytics에 업로드된 후 솔루션 대시보드에 표시됩니다. DNS Powershell cmdlet을 실행하여 DNS 서버, 영역 및 리소스 레코드 수와 같은 인벤토리 관련 데이터가 수집됩니다. 데이터가 2일마다 한 번씩 업데이트됩니다. 이벤트 관련 데이터가 Windows Server 2012 R2의 향상된 DNS 로깅 및 진단이 제공하는 [분석 및 감사 로그](https://technet.microsoft.com/library/dn800669.aspx#enhanc)를 통해 거의 실시간으로 수집됩니다.

## <a name="configuration"></a>구성

다음 정보를 사용하여 솔루션을 구성합니다.

- 모니터링할 각 DNS 서버에 [Windows](log-analytics-windows-agent.md) 또는 [Operations Manager](log-analytics-om-agents.md) 에이전트가 있어야 합니다.
- [Azure Marketplace](https://aka.ms/dnsanalyticsazuremarketplace)에서 Operations Management Suite 작업 영역으로 DNS 분석 솔루션을 추가할 수 있습니다. [솔루션 갤러리에서 Log Analytics 솔루션 추가](log-analytics-add-solutions.md)에서 설명한 프로세스를 사용할 수도 있습니다.

솔루션은 추가 구성이 필요 없이 데이터 수집을 시작합니다. 그러나 다음 구성을 사용하여 데이터 수집을 사용자 지정할 수 있습니다.

### <a name="configure-the-solution"></a>솔루션 구성

솔루션 대시보드에서 **구성**을 클릭하여 DNS 분석 구성 페이지를 엽니다. 다음과 같은 2가지 유형의 구성 변경을 수행할 수 있습니다.

- **허용 목록에 포함된 도메인 이름**. 솔루션이 모든 조회 쿼리를 처리하는 것은 아닙니다. 도메인 이름 접미사의 허용 목록이 유지됩니다. 이 허용 목록의 도메인 이름 접미사와 일치하는 도메인 이름으로 확인되는 조회 쿼리는 솔루션에서 처리되지 않습니다. 허용 목록에 포함된 도메인 이름이 처리되지 않으면 Log Analytics에 전송되는 데이터를 최적화하는 데 도움이 됩니다. 기본 허용 목록에는 인기 있는 공용 도메인 이름(예: www.google.com 및 www.facebook.com 등)이 포함됩니다. 스크롤하여 전체 기본 목록을 볼 수 있습니다.

 목록을 수정하여 조회 정보를 보려는 도메인 이름 접미사를 추가할 수 있습니다. 조회 정보를 보지 않으려는 도메인 이름 접미사는 제거할 수 있습니다.

- **Talkative 클라이언트 임계값** 조회 요청 수에 대한 임계값을 초과하는 DNS 클라이언트는 **DNS 클라이언트** 블레이드에서 강조 표시됩니다. 기본 임계값은 1,000입니다. 이 임계값은 편집할 수 있습니다.

    ![허용 목록에 포함된 도메인 이름](./media/log-analytics-dns/dns-config.png)

## <a name="management-packs"></a>관리 팩

Microsoft Monitoring Agent를 사용하여 Operations Management Suite 작업 영역에 연결하는 경우 다음 관리 팩이 설치됩니다.

- Microsoft DNS 데이터 수집기 인텔리전스 팩(Microsft.IntelligencePacks.Dns)

Operations Manager 관리 그룹이 Operations Management Suite 작업 영역에 연결된 경우 이 솔루션을 추가할 때 다음 관리 팩이 System Center Operations Manager에 설치됩니다. 이 관리 팩에 대한 구성 또는 유지 관리는 필요 없습니다.

- Microsoft DNS 데이터 수집기 인텔리전스 팩(Microsft.IntelligencePacks.Dns)
- Microsoft System Center Advisor DNS 분석 구성(Microsoft.IntelligencePack.Dns.Configuration)

솔루션 관리 팩이 업데이트되는 방법에 대한 자세한 내용은 [Log Analytics에 Operations Manager 연결](log-analytics-om-agents.md)을 참조하세요.

## <a name="use-the-dns-analytics-solution"></a>DNS 분석 솔루션 사용

이 섹션에서는 모든 대시보드 기능과 사용 방법에 대해 설명합니다.

작업 영역에 이 솔루션을 추가하면 Operations Management Suite 개요 페이지의 솔루션 타일에 DNS 인프라에 대한 간단한 요약이 표시됩니다. 여기에는 데이터가 수집되는 DNS 서버 수가 포함됩니다. 지난 24시간 동안 악성 도메인을 해결하기 위해 클라이언트에서 수행한 요청 수도 포함됩니다. 타일을 클릭하면 솔루션 대시보드가 열립니다.

![DNS 분석 타일](./media/log-analytics-dns/dns-tile.png)

### <a name="solution-dashboard"></a>솔루션 대시보드

솔루션 대시보드에는 솔루션의 다양한 기능에 대한 요약 정보가 표시됩니다. 법정 분석 및 진단에 대한 자세히 보기로 연결되는 링크도 포함됩니다. 기본적으로 지난 7일 동안의 데이터가 표시됩니다. 다음 이미지와 비슷한 **날짜-시간 선택 컨트롤**을 사용하여 날짜 및 시간 범위를 변경할 수 있습니다.

![시간 선택 컨트롤](./media/log-analytics-dns/dns-time.png)

솔루션 대시보드에는 다음 블레이드가 표시됩니다.

**DNS 보안**. 악의적인 도메인과 통신하려고 하는 DNS 클라이언트를 보고합니다. DNS 분석은 Microsoft 위협 인텔리전스 피드를 사용하여 악의적인 도메인에 액세스하려고 하는 클라이언트 IP를 검색할 수 있습니다. 대부분의 경우 맬웨어 감염 장치는 맬웨어 도메인 이름을 확인하여 악의적인 도메인의 “명령 및 컨트롤” 센터로 “전화 수신 접속”을 수행합니다.

![DNS 보안 블레이드](./media/log-analytics-dns/dns-security-blade.png)

목록의 클라이언트 IP를 클릭하면 로그 검색이 열리고 해당 쿼리의 조회 세부 정보를 표시합니다. 다음 예제에서 DNS 분석은 [IRCbot](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=Win32/IRCbot)과의 통신이 수행되었음을 감지했습니다.

![ircbot을 표시하는 로그 검색 결과](./media/log-analytics-dns/ircbot.png)

이 정보는 다음을 파악하는 데 도움이 됩니다.

- 통신을 시작한 클라이언트 IP
- 악의적인 IP로 확인된 도메인 이름
- 도메인 이름으로 확인된 IP 주소
- 악의적인 IP 주소
- 문제의 심각도
- 악의적인 IP를 차단 목록에 추가한 이유
- 검색 시간

**도메인 쿼리함**. 작업 환경의 DNS 클라이언트가 가장 자주 쿼리한 도메인 이름을 제공합니다. 쿼리되는 모든 도메인 이름 목록을 볼 수 있습니다. 로그 검색에서 특정 도메인 이름에 대한 조회 요청 세부 정보를 드릴다운할 수도 있습니다.

![도메인 쿼리함 블레이드](./media/log-analytics-dns/domains-queried-blade.png)

**DNS 클라이언트**. 선택한 기간에 쿼리 수에 대한 *임계값을 위반하는* 클라이언트를 보고합니다. 로그 검색에서 모든 DNS 클라이언트의 목록과 해당 클라이언트가 수행한 쿼리 세부 정보를 볼 수 있습니다.

![DNS 클라이언트 블레이드](./media/log-analytics-dns/dns-clients-blade.png)

**동적 DNS 등록**. 이름 등록 오류를 보고합니다. 주소 [리소스 레코드](https://en.wikipedia.org/wiki/List_of_DNS_record_types)(형식 A 및 AAAA)에 대한 모든 등록 오류는 등록 요청을 수행한 클라이언트 IP와 함께 강조 표시됩니다. 그런 다음 이 정보를 사용해서 다음 단계에 따라 등록 오류의 근본 원인을 찾을 수 있습니다.

1. 클라이언트가 업데이트하려고 하는 이름에 대해 권한이 있는 영역을 찾습니다.

2. 해당 영역의 인벤토리 정보를 확인하기 위한 솔루션을 사용합니다.

3. 영역에 대한 동적 업데이트가 사용되도록 설정되어 있는지 확인합니다.

4. 영역이 보안 동적 업데이트에 대해 구성되어 있는지 여부를 확인합니다.

    ![동적 DNS 등록 블레이드](./media/log-analytics-dns/dynamic-dns-reg-blade.png)

**이름 등록 요청**. 위쪽 타일에는 성공 또는 실패한 DNS 동적 업데이트의 추세가 표시됩니다. 아래쪽 타일에는 DNS 서버로 실패한 DNS 업데이트 요청을 보낸 상위 10개 클라이언트가 실패 횟수별로 정렬되어 표시됩니다.

![이름 등록 요청 블레이드 ](./media/log-analytics-dns/name-reg-req-blade.png)

**샘플 DDI 분석 쿼리** 원시 분석 데이터를 직접 가져오는 가장 일반적인 검색 쿼리 목록이 포함되어 있습니다.


![샘플 쿼리](./media/log-analytics-dns/queries.png)

이러한 쿼리를 시작점으로 사용하여 사용자 지정 보고를 위한 쿼리를 만들 수 있습니다. 쿼리는 결과가 표시되는 DNS 분석 로그 검색 페이지에 연결됩니다.

- **DNS 서버 목록**. 연결된 FQDN, 도메인 이름, 포리스트 이름 및 서버 IP와 함께 모든 DNS 서버 목록을 표시합니다.
- **DNS 영역 목록**. 이 링크를 클릭하면 연결된 영역 이름, 동적 업데이트 상태, 이름 서버 및 DNSSEC 서명 상태와 함께 모든 DNS 영역 목록을 보여 주는 DNS 로그 검색 페이지가 열립니다.
- **사용되지 않은 리소스 레코드**. 사용되지 않은/부실 리소스 레코드의 목록을 보여 줍니다. 이 목록에는 리소스 레코드 이름, 리소스 레코드 유형, 연결된 DNS 서버, 레코드 생성 시간 및 영역 이름이 포함됩니다. 이 목록을 사용해서 더 이상 사용되지 않는 DNS 리소스 레코드를 식별할 수 있습니다. 이 정보에 따라 DNS 서버에서 해당 항목을 제거할 수 있습니다.
- **DNS 서버 쿼리 로드**. DNS 서버에서 DNS 로드 상황을 파악할 수 있도록 정보를 표시합니다. 이 정보는 서버에 대한 용량을 계획하는 데 도움이 될 수 있습니다. **메트릭** 탭으로 이동하여 보기를 그래픽 시각화로 변경할 수 있습니다. 이 보기를 사용하면 DNS 서버에서 DNS 부하가 분산되는 방식을 이해할 수 있습니다. 각 서버에 대한 DNS 쿼리 속도 추세가 표시됩니다.

    ![DNS 서버 쿼리 로그 검색 결과](./media/log-analytics-dns/dns-servers-query-load.png)

- **DNS 영역 쿼리 로드**. 솔루션에서 관리되는 DNS 서버의 모든 영역에 대한 DNS 초당 영역 쿼리 통계를 표시합니다. **메트릭** 탭을 클릭하여 상세한 레코드에서 결과의 그래픽 시각화로 보기를 변경할 수 있습니다.
- **구성 이벤트**. 모든 DNS 구성 변경 이벤트 및 연결된 메시지를 표시합니다. 그런 후 이벤트 시간, 이벤트 ID, DNS 서버 또는 작업 범주에 따라 이러한 이벤트를 필터링할 수 있습니다. 이 데이터는 특정 시간에 특정 DNS 서버에 대한 변경 내용을 감사하는 데 도움이 됩니다.
- **DNS 분석 로그**. 솔루션에서 관리되는 모든 DNS 서버의 모든 분석 이벤트를 표시합니다. 이벤트 시간, 이벤트 ID, DNS 서버, 조회 쿼리를 수행한 클라이언트 ID 및 쿼리 유형 작업 범주에 따라 이러한 이벤트를 필터링할 수 있습니다. DNS 서버 분석 이벤트는 DNS 서버의 활동 추적을 활성화합니다. 분석 이벤트는 서버가 DNS 정보를 보내거나 받을 때마다 로깅됩니다.

### <a name="search-by-using-dns-analytics-log-search"></a>DNS 분석 로그 검색을 사용하여 검색

로그 검색 페이지에서 쿼리를 만들 수 있습니다. 패싯 컨트롤을 사용하여 검색 결과를 필터링할 수 있습니다. 변환, 필터링 및 결과를 보고하는 고급 쿼리를 만들 수도 있습니다. 먼저 다음 쿼리를 사용합니다.

1. **검색 쿼리 상자**에 `DnsEvents`를 입력하여 솔루션에서 관리되는 DNS 서버에 의해 생성된 모든 DNS 이벤트를 표시합니다. 결과에는 조회 쿼리, 동적 등록 및 구성 변경과 관련된 모든 이벤트에 대한 로그 데이터가 표시됩니다.

    ![DnsEvents 로그 검색](./media/log-analytics-dns/log-search-dnsevents.png)  

    a. 조회 쿼리에 대한 로그 데이터를 보려면 왼쪽의 패싯 컨트롤에서 **하위 유형** 필터로 **LookUpQuery**를 선택합니다. 선택한 기간에 대한 모든 조회 쿼리 이벤트를 나열하는 테이블이 표시됩니다.

    나. 동적 등록에 대한 로그 데이터를 보려면 왼쪽의 패싯 컨트롤에서 **하위 유형** 필터로 **DynamicRegistration**을 선택합니다. 선택한 기간에 대한 모든 동적 등록 이벤트를 나열하는 테이블이 표시됩니다.

    다. 구성 변경에 대한 로그 데이터를 보려면 왼쪽의 패싯 컨트롤에서 **하위 유형** 필터로 **ConfigurationChange**를 선택합니다. 선택한 기간에 대한 모든 구성 변경 이벤트를 나열하는 테이블이 표시됩니다.

2. **검색 쿼리 상자**에 `DnsInventory`를 입력하여 솔루션에서 관리되는 DNS 서버에 대한 모든 DNS 인벤토리 관련 데이터를 표시합니다. 결과에는 DNS 서버, DNS 영역 및 리소스 레코드에 대한 로그 데이터가 표시됩니다.

    ![DnsInventory 로그 검색](./media/log-analytics-dns/log-search-dnsinventory.png)

## <a name="feedback"></a>사용자 의견

두 가지 방법으로 사용자 의견을 제공할 수 있습니다.

- **UserVoice**. 작업 중인 DNS 분석 기능에 대한 아이디어를 게시합니다. [Operations Management Suite UserVoice 페이지](https://aka.ms/dnsanalyticsuservoice)를 참조하세요.
- **코호트 가입**. 새로운 고객이 코호트에 가입하여 새 기능을 미리 사용해보고 DNS 분석 기능을 향상시키는 데 도움을 줄 수 있기 바랍니다. 코호트 가입에 관심이 있는 경우 [빠른 설문 조사](https://aka.ms/dnsanalyticssurvey)에 참여하세요.

## <a name="next-steps"></a>다음 단계

[로그를 검색](log-analytics-log-searches.md)하여 자세한 DNS 로그 레코드를 볼 수 있습니다.
