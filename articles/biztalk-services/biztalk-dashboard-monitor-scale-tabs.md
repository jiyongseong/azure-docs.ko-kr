---
title: "BizTalk Services의 대시보드, 모니터, 크기 조정, 구성 및 하이브리드 연결 | Microsoft Docs"
description: "컨트롤에 대해 알아보고 BizTalk Services의 성능을 모니터링합니다."
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 7a1815db-0de2-4274-8be0-198c1b077324
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 351809cd5f165a863dc02bfadf78fa59cbaabfd7
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2017
---
# <a name="review-the-dashboard-monitor-scale-configure-and-hybrid-connection-tabs"></a>대시보드, 모니터, 확장, 구성 및 하이브리드 연결 탭 검토

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

BizTalk 서비스를 만들고 응용 프로그램을 배포한 후 BizTalk 서비스 설정 중 일부를 변경하고 응용 프로그램 성능을 모니터링할 수 있습니다. 

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

그러면 다음 탭이 포함된 새 창이 열립니다. 이 항목에서는 이러한 탭에 대해 설명합니다.

## <a name="quickstart-quickstartquickstart"></a>빠른 시작(![빠른 시작][Quickstart])
BizTalk Services 버전에 따라 나열된 일부 옵션을 사용하지 못할 수도 있습니다. 

<table border="1">
    <tr>
        <td><strong>도구 얻기</strong></td>
        <td>BizTalk Services SDK를 다운로드하여 온-프레미스 개발 컴퓨터에 Visual Studio 프로젝트 템플릿을 설치합니다. 이러한 템플릿은 BizTalk Service에 배포되는 <strong>BizTalk Services</strong>(브리지) 및 <strong>BizTalk Service Artifacts</strong>(변형) Visual Studio 프로젝트를 만듭니다.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302335">Azure BizTalk Services SDK로 시작하는 방법</a> 및 <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=241589">Azure BizTalk Services SDK 설치</a>에서 시작 단계를 나열합니다.
        </td>
    </tr>
    <tr>
        <td><strong>파트너 규약 만들기</strong></td>
        <td>Azure에서 호스트되는 Azure BizTalk Services 포털을 엽니다. 이 포털에서 파트너를 추가하고 X12, AS2 및 EDIFACT EDI 계약을 생성할 수 있습니다.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">BizTalk Services Portal에서 EDI 메시징 구성 요소 구성</a>에서 시작 단계를 나열합니다.
        </td>
    </tr>

<tr>
        <td><strong>BizTalk Services에 대한 자세한 정보</strong></td>
        <td>Azure BizTalk Services에 대해 자세히 알아보려면 <a HREF="http://azure.microsoft.com/documentation/services/biztalk-services/">학습 센터</a>로 이동합니다.</td>
</tr>
</table>


맨 아래에 있는 작업 표시줄에서 다음을 수행할 수 있습니다.

<table border="1">

<tr>
<td>응용 프로그램 배포 <strong>관리</strong></td>
<td>Azure BizTalk Services 포털을 엽니다. BizTalk Services 포털에서 파트너를 추가하거나 X12, AS2 및 EDIFACT 계약을 만드는 등의 EDI 구성을 시작할 수 있습니다.
<br/><br/>
이 메뉴는 <strong>빠른 시작</strong> 탭의 <strong>파트너 계약 만들기</strong>와 동일합니다.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">BizTalk Services Portal에서 EDI 메시징 구성 요소 구성</a>에서 BizTalk Services Portal에 대한 자세한 정보를 제공합니다.</td>
</tr>

<tr>
<td>[Access Control 네임스페이스]의 <strong>연결 정보</strong></td>
<td>연결 정보를 선택하면 Access Control 네임스페이스, 기본 발급자 및 기본 키가 표시됩니다. 이러한 값은 복사할 수 있습니다.
<br/><br/>
또한 Access Control 포털을 열 수 있습니다. <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">액세스 제어 네임 스페이스 만들기</a>에서 Access Control Portal에 대한 자세한 정보를 제공합니다.</td>
</tr>

<tr>
<td>[Storage 계정]의 <strong>동기화 키</strong></td>
<td>Storage 계정을 만들면 기본 키와 보조 키가 자동으로 만들어집니다. 이러한 암호화 키는 Storage 계정에 대한 액세스를 제어합니다. BizTalk 서비스는 자동으로 기본 키를 사용합니다. 사용자는 <strong>동기화 키</strong>를 통해 BizTalk Service를 중단하지 않고 [기본 키]와 [보조 키] 간에 전환할 수 있습니다.
<br/><br/>
예를 들어 BizTalk 서비스에서 Storage 계정의 새로운 기본 키를 사용할 수 있습니다. 다음을 수행합니다.
<br/><br/>
<ol>
<li>[BizTalk Service]를 선택하고 <strong>동기화 키</strong>를 선택합니다. 보조 키를 선택합니다. 이 작업을 수행하면 BizTalk 서비스에서 보조 키를 사용합니다.</li>
<li>Storage 계정을 선택하고 기본 키를 다시 생성합니다. BizTalk 서비스에서 보조 키를 사용하고 있다는 점에 유의하십시오.</li>
<li>[BizTalk Service]를 선택하고 <strong>동기화 키</strong>를 선택합니다. 이제 기본 키를 선택합니다. 이 키는 앞서 다시 생성한 새로운 기본 키입니다.</li>
<li>Storage 계정을 선택하고 보조 키를 다시 생성합니다.</li>
</ol>
<br/>
이 프로세스를 "키 롤오버"라고 합니다. 이 프로세스의 목적은 사용자가 BizTalk 서비스를 중단하지 않고 기본 키와 보조 키 간에 전환할 수 있도록 하는 것입니다.</td>
</tr>

<tr>
<td>응용 프로그램 <strong>삭제</strong></td>
<td>삭제를 선택하면 BizTalk 서비스와 해당 서비스에 배포된 모든 항목이 제거됩니다.</td>
</tr>
</table>


## <a name="dashboard"></a>대시보드
BizTalk Services 버전에 따라 나열된 일부 옵션을 사용하지 못할 수도 있습니다. 

BizTalk 서비스 이름을 선택하면 대시보드 탭이 표시됩니다. 대시보드에서 다음을 수행할 수 있습니다.

##### <a name="usage-overview-shows-the-number-of-used-hybrid-connections"></a>사용 개요: 사용한 하이브리드 연결 수를 표시합니다.
또한 데이터 사용량(GB)도 표시합니다. 

##### <a name="metric-graph-shows-a-fixed-list-of-performance-metrics"></a>메트릭 그래프: 성능 메트릭의 고정 목록을 표시합니다.
이러한 메트릭은 BizTalk 서비스 상태와 관련된 실시간 값을 제공합니다. 또한 그래프에 표시되는 메트릭의 **상대** 또는 **절대** 값과 시간 범위 **간격**을 선택할 수 있습니다. 

이러한 성능 메트릭에 대한 설명은 이 항목의 [사용 가능한 메트릭](#Metrics) 을 참조하세요.

##### <a name="quick-glance-lists-your-biztalk-service-properties"></a>간략 상태: BizTalk 서비스 속성을 나열합니다.
<table border="1">

<tr>
<td><strong>추적 데이터베이스 자격 증명 업데이트</strong></td>
<td>추적 데이터베이스에 로그인하는 데 사용하는 사용자 이름 및 암호를 변경합니다.</td>
</tr>
<tr>
<td><strong>SSL 인증서 업데이트</strong></td>
<td>다른 SSL 인증서를 사용하도록 BizTalk 서비스를 업데이트할 수 있습니다. <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">BizTalk Service를 만들</a> 때는 자체 서명된 SSL 인증서가 자동으로 만들어집니다.</td>
</tr>
<tr>
<td><strong>인증서 다운로드</strong></td>
<td>BizTalk 서비스에서 사용하는 SSL 인증서를 로컬 컴퓨터에 다운로드할 수 있습니다.</td>
</tr>
<tr>
<td><strong>상태</strong></td>
<td>BizTalk 서비스의 현재 상태를 표시합니다. <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=329870">BizTalk Services: 서비스 상태 차트</a>를 참조합니다. </td>
</tr>
<tr>
<td><strong>서비스 URL</strong></td>
<td>BizTalk 서비스의 URL입니다. 이는 [BizTalk Service]를 만들 때 입력한 <strong>도메인 URL</strong>과 동일합니다.</td>
</tr>
<tr>
<td><strong>공용 VIP(가상 IP) 주소</strong></td>
<td>BizTalk 서비스에 할당된 IP 주소입니다. 이는 모든 입력 끝점에 사용되며 아웃바운드 트래픽의 원본 주소입니다. 이 IP 주소는 만들어진 상태에서는 BizTalk 서비스에 속합니다. BizTalk 서비스를 삭제하면 IP 주소가 다른 BizTalk 서비스에 할당됩니다.</td>
</tr>
<tr>
<td><strong>ACS 네임스페이스</strong></td>
<td>BizTalk 서비스로 인증합니다.</td>
</tr>
<tr>
<td><strong>에디션</strong></td>
<td>BizTalk 서비스를 만들 때 입력한 버전을 나열합니다.</td>
</tr>
<tr>
<td><strong>위치</strong></td>
<td>BizTalk 서비스를 호스트하는 지리적 지역을 표시합니다.</td>
</tr>
<tr>
<td><strong>생성일</strong></td>
<td>BizTalk 서비스를 만든 날짜 및 시간을 표시합니다.</td>
</tr>
<tr>
<td><strong>추적 데이터베이스</strong></td>
<td>BizTalk 서비스에 사용되는 추적 테이블을 저장하는 Azure SQL Database 이름입니다. 
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">요구 사항 설명</a>에서는 [추적 데이터베이스]에 대한 자세한 정보를 제공합니다.</td>
</tr>
<tr>
<td><strong>모니터링/보관 저장소</strong></td>
<td>BizTalk 서비스에 대한 모니터링 출력을 저장하는 Azure Storage 계정 이름입니다.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">요구 사항 설명</a>에서는 Storage 계정에 대한 자세한 정보를 제공합니다.</td>
</tr>
<tr>
<td><strong>구독 이름</strong></td>
<td>BizTalk 서비스를 호스트하는 구독을 나열합니다. 구독은 액세스를 제어합니다.</td>
</tr>
<tr>
<td><strong>구독 ID</strong></td>
<td>구독을 만들 때 구독 ID가 자동으로 생성됩니다. REST API를 사용할 경우 구독 ID를 입력해야 할 수도 있습니다.</td>
</tr>
</table>

[BizTalk Services: 프로비전](http://go.microsoft.com/fwlink/p/?LinkID=302280)은 BizTalk Service를 만드는 단계를 나열합니다.

##### <a name="manage-connection-information-sync-keys-and-delete-in-the-task-bar"></a>작업 표시줄의 관리, 연결 정보, 동기화 키 및 삭제:
<table border="1">

<tr>
<td>응용 프로그램 배포 <strong>관리</strong></td>
<td>Azure BizTalk Services 포털을 엽니다. BizTalk Services 포털에서 파트너를 추가하거나 X12, AS2 및 EDIFACT 계약을 만드는 등의 EDI 구성을 시작할 수 있습니다.
<br/><br/>
이 메뉴는 <strong>빠른 시작</strong> 탭의 <strong>파트너 계약 만들기</strong>와 동일합니다.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">BizTalk Services Portal에서 EDI 메시징 구성 요소 구성</a>에서 BizTalk Services Portal에 대한 자세한 정보를 제공합니다.</td>
</tr>
<tr>
<td>[Access Control 네임스페이스]의 <strong>연결 정보</strong></td>
<td>Access Control 네임스페이스, 기본 발급자 및 기본 키를 표시하며, 이러한 항목을 복사할 수 있습니다.
<br/><br/>
또한 Access Control 포털을 열 수 있습니다. 이 Access Control 포털은 왼쪽 탐색 창에서 Active Directory 옵션을 사용하는 경우와 동일한 기능을 제공합니다.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">ACS 네임 스페이스 관리</a>에서 Access Control Portal에 대한 자세한 정보를 제공합니다.</td>
</tr>
<tr>
<td>[Storage 계정]의 <strong>동기화 키</strong></td>
<td>Storage 계정을 만들면 기본 키와 보조 키가 자동으로 만들어집니다. 이러한 암호화 키는 Storage 계정에 대한 액세스를 제어합니다. BizTalk 서비스는 자동으로 기본 키를 사용합니다. 사용자는 <strong>동기화 키</strong>를 통해 BizTalk Service를 중단하지 않고 [기본 키]와 [보조 키] 간에 전환할 수 있습니다.
<br/><br/>
예를 들어 BizTalk 서비스에서 Storage 계정의 새로운 기본 키를 사용할 수 있습니다. 다음을 수행합니다.
<br/><br/>
<ol>
<li>[BizTalk Service]를 선택하고 <strong>동기화 키</strong>를 선택합니다. 보조 키를 선택합니다. 이 작업을 수행하면 BizTalk 서비스에서 보조 키를 사용합니다.</li>
<li>Storage 계정을 선택하고 기본 키를 다시 생성합니다. BizTalk 서비스에서 보조 키를 사용하고 있다는 점에 유의하십시오.</li>
<li>[BizTalk Service]를 선택하고 <strong>동기화 키</strong>를 선택합니다. 이제 기본 키를 선택합니다. 이 키는 앞서 다시 생성한 새로운 기본 키입니다.</li>
<li>Storage 계정을 선택하고 보조 키를 다시 생성합니다.</li>
</ol>
<br/>
이 프로세스를 "키 롤오버"라고 합니다. 이 프로세스의 목적은 사용자가 BizTalk 서비스를 중단하지 않고 기본 키와 보조 키 간에 전환할 수 있도록 하는 것입니다.</td>
</tr>

<tr>
<td>응용 프로그램 <strong>삭제</strong></td>
<td>BizTalk 서비스와 이 서비스에 배포된 모든 항목이 제거됩니다.</td>
</tr>
</table>


## <a name="monitor"></a>모니터
Free Edition에는 적용되지 않습니다.

BizTalk 서비스 이름을 선택하면 다음 항목이 표시된 모니터 탭을 사용할 수 있습니다.

##### <a name="metric-graph-displays-the-selected-performance-metrics"></a>메트릭 그래프: 선택한 성능 메트릭을 표시합니다.
이러한 메트릭은 BizTalk 서비스 상태와 관련된 실시간 값을 제공합니다. 표시할 성능 메트릭을 선택합니다. 최대 6개의 성능 메트릭을 동시에 표시할 수 있습니다. 

또한 표시되는 메트릭의 **상대** 또는 **절대** 값과 시간 범위 **간격**을 선택할 수 있습니다. 

##### <a name="to-remove-or-display-metrics-in-the-graph"></a>그래프에서 메트릭을 제거하거나 표시하려면
1. **모니터** 탭을 선택합니다.
2. 작업 표시줄에서 다음과 같은 **메트릭 추가**를 선택합니다.  
   ![메트릭 추가 선택][AddMetrics]
3. 표시할 성능 메트릭을 선택합니다.
4. 확인 표시를 선택하여 **모니터** 탭으로 돌아갑니다.
5. 메트릭 옆에 있는 원을 선택하여 그래프에 메트릭 값을 표시합니다.  
   
    예를 들어 다음과 같이 **CPU 사용량** 메트릭이 회색으로 표시되며, 이 메트릭의 출력은 그래프에 표시되지 않습니다.  
   ![CPU 사용량 메트릭이 회색으로 표시됨][GrayedMetric]  
   
    **CPU 사용량** 메트릭의 출력을 그래프에 표시하려면 다음과 같이 회색으로 표시된 원을 선택합니다.  
   ![CPU 사용량 메트릭 사용][EnabledMetric]
6. 디스플레이 그래프 및 목록에서 메트릭을 제거하려면 작업 표시줄에서 **메트릭 삭제** 를 선택합니다. 목록에 메트릭을 다시 추가하려면 작업 표시줄에서 **메트릭 추가**를 선택하고 메트릭을 선택한 후 확인 표시를 선택하여 **모니터** 탭으로 돌아갑니다. 회색으로 표시된 원을 선택하면 해당 메트릭이 활성화됩니다.

## <a name="Metrics"></a>사용 가능한 메트릭
다음 성능 카운터/메트릭을 사용할 수 있습니다.

<table border="1">

<tr>
<td><strong>왕복 대기 시간</strong></td>
<td>메시지를 받은 시간부터 BizTalk 서비스가 모든 브리지에 걸쳐 메시지를 완전히 처리할 때까지 메시지를 처리하는 데 걸린 평균 시간을 밀리초(ms) 단위로 표시합니다. 성공적으로 처리된 메시지만 계산됩니다.<br/><br/>
다음 이벤트가 발생할 경우 타임스탬프가 만들어집니다.
<ul>
<li>메시지가 게이트웨이에 들어오는 경우</li>
<li>메시지가 대상으로 라우팅되는 경우</li>
<li>대상 응답을 받은 경우</li>
<li>대상 확인 응답이 게이트웨이에 전송된 경우</li>
</ul>
<br/>
이 메트릭은 다음 계산의 결과를 표시합니다.
<br/><br/>
[대상 확인 응답이 게이트웨이에 전송된 시간] - [메시지가 게이트웨이에 들어온 시간]</td>
</tr>
<tr>
<td><strong>원본에서 실패</strong></td>
<td>원본 끝점에서 메시지를 끌어올 때 BizTalk 서비스에서 실패한 총 메시지 수를 표시합니다.</td>
</tr>
<tr>
<td><strong>CPU 사용량</strong></td>
<td>모든 역할 인스턴스의 평균 프로세서 시간 백분율을 나열합니다.</td>
</tr>
<tr>
<td><strong>처리 대기 시간</strong></td>
<td>BizTalk 서비스가 모든 브리지에 걸쳐 메시지를 처리하는 데 걸린 평균 시간을 밀리초(ms)로 표시합니다. 단, 대상에서 소요된 시간은 제외됩니다. 성공적으로 처리된 메시지만 계산됩니다.<br/><br/>
다음 이벤트가 발생할 경우 타임스탬프가 만들어집니다.

<ul>
<li>메시지가 게이트웨이에 들어오는 경우</li>
<li>메시지가 대상으로 라우팅되는 경우</li>
<li>대상 응답을 받은 경우</li>
<li>대상 확인 응답이 게이트웨이에 전송된 경우</li>
</ul>
<br/>이 메트릭은 다음 계산의 결과를 표시합니다.<br/><br/>
[대상 확인 응답이 게이트웨이에 전송된 시간] - [메시지가 게이트웨이에 들어온 시간] - [대상 응답을 받은 시간] + [메시지가 대상으로 라우팅된 시간]</td>
</tr>
<tr>
<td><strong>처리 중 실패</strong></td>
<td>BizTalk 서비스가 일정 시간 간격 내에 모든 브리지에 걸쳐 메시지를 처리하는 동안 실패한 총 메시지 수를 표시합니다.</td>
</tr>
<tr>
<td><strong>전송된 메시지</strong></td>
<td>BizTalk 서비스가 일정 시간 간격 내에 모든 브리지에 걸쳐 전송한 총 메시지 수를 표시합니다. 이 메트릭은 파이프라인에서 전송된 메시지가 경로 대상에 도달할 때 증가합니다. 이 메트릭이 메시지가 성공적으로 처리되었음을 나타내지는 않습니다.<br/><br/>
요청-회신 시나리오에서, 경로 대상이 수신 확인을 파이프라인에 다시 보낼 때 이 메트릭이 증가합니다.</td>
</tr>
<tr>
<td><strong>수신된 메시지</strong></td>
<td>BizTalk 서비스가 일정 시간 간격 내에 모든 브리지에 걸쳐 받은 총 메시지 수를 표시합니다. 이 메트릭은 파이프라인에서 새 메시지를 수신할 때 증가합니다.</td>
</tr>
<tr>
<td><strong>진행 중 메시지</strong></td>
<td>BizTalk 서비스가 일정 시간 간격 내에 처리 중인 총 메시지 수를 표시합니다.</td>
</tr>
<tr>
<td><strong>처리된 메시지</strong></td>
<td>BizTalk 서비스가 일정 시간 간격 내에 모든 브리지에 걸쳐 성공적으로 처리한 총 메시지 수를 표시합니다. 이 메트릭은 파이프라인에서 메시지를 성공적으로 수신하여 대상으로 라우팅할 때 증가합니다.</td>
</tr>
</table>


## <a name="scale"></a>확장
크기 조정 탭에서 BizTalk 서비스에 사용되는 단위 수를 추가하거나 줄일 수 있습니다. 기본적으로 한 개의 단위가 구성되어 있습니다. 단위를 더 추가하여 BizTalk 서비스를 확장할 수 있습니다. 크기를 늘리면 처리량을 늘리는 것입니다. 배포된 브리지, 계약, LOB 연결, 처리 능력 등 리소스의 양도 증가합니다. 예를 들어 1단위에서 2단위로 크기를 늘립니다. 이 경우 브리지 수, 계약, LOB 연결, 처리 기능을 두 배로 배포할 수 있습니다.

일부 BizTalk 버전에서는 크기 조정 옵션을 제공하지 않습니다. 이 경우 1 단위가 허용됩니다. 사용 중인 버전에서 크기 조정할 수 있는 단위 수를 확인하려면 [BizTalk Services: 버전 차트](biztalk-editions-feature-chart.md)를 참조하세요.

단위 수를 늘리면 가격이 달라질 수 있습니다. 단위를 늘리고 **저장** 을 선택하면 청구 금액이 달라질 수 있다는 메시지가 표시됩니다. 그런 다음 계속하도록 선택합니다. 단위 수를 늘리면 BizTalk 서비스 상태가 활성에서 업데이트 중으로 변합니다. 업데이트 중 상태에서도 BizTalk 서비스는 계속 실행됩니다.

[BizTalk Services: 버전 차트](biztalk-editions-feature-chart.md) 에 “단위"가 정의되어 있습니다.

## <a name="configure"></a>구성
하이브리드 연결에는 적용되지 않습니다.

Backup 상태를 없음 또는 자동으로 설정합니다. 없음으로 설정하면 백업이 자동으로 만들어지지 않습니다. 자동으로 설정한 경우에는 백업 위치, 백업 빈도 및 백업 파일을 유지할 기간을 구성합니다. 

[BizTalk Services: Backup 및 복원](biztalk-backup-restore.md) 에 자세한 정보가 나와 있습니다. 

## <a name="HybridConnections"></a>하이브리드 연결
[하이브리드 연결]은 Azure 응용 프로그램(예: Azure App Service의 Web Apps 또는 Mobile Apps)을 정적 TCP 포트를 사용하는 온-프레미스 리소스(예: SQL Server, MySQL, HTTP 웹 API 및 대부분의 사용자 지정 웹 서비스)에 연결합니다. 하이브리드 연결은 BizTalk Services에서 관리됩니다.

Azure BizTalk Services에서 하이브리드 연결을 만들거나 관리하려면 [하이브리드 연결](integration-hybrid-connection-overview.md)을 참조하세요.

## <a name="next"></a>다음
여러 탭을 살펴봤으므로 다음과 같은 Azure BizTalk Services 기능에 대해 자세히 알아볼 수 있습니다.

* [BizTalk Services: 제한](biztalk-throttling-thresholds.md)  
* [BizTalk Services: 발급자 이름 및 발급자 키](biztalk-issuer-name-issuer-key.md)  
* [BizTalk Services: Backup 및 복원](biztalk-backup-restore.md)

## <a name="see-also"></a>참고 항목
* [하이브리드 연결](integration-hybrid-connection-overview.md)  
* [BizTalk Services: Developer, Basic, Standard 및 Premium Editions 차트](biztalk-editions-feature-chart.md)  
* [BizTalk Services: 프로비전](biztalk-provision-services.md)  
* [BizTalk Services: BizTalk 서비스 상태 차트](biztalk-service-state-chart.md)  
* [Azure BizTalk Services SDK로 시작하는 방법](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[Quickstart]: ./media/biztalk-dashboard-monitor-scale-tabs/QuickStartIcon.png
[AddMetrics]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
[GrayedMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
[EnabledMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png

