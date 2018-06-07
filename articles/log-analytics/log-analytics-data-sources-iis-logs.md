---
title: "Azure Log Analytics의 IIS 로그 | Microsoft Docs"
description: "IIS(인터넷 정보 서비스)는 Log Analytics에서 수집할 수 있는 로그 파일에 사용자 활동을 저장합니다.  이 문서에서는 IIS 로그 수집을 구성하는 방법을 설명하고, Log Analytics 작업 영역에 생성되는 레코드에 대한 자세한 정보를 제공합니다."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: cec5ff0a-01f5-4262-b2e8-e3db7b7467d2
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2018
ms.author: bwren
ms.openlocfilehash: b8ce4e6fe6e12aa3edb81abad1589924e3e121e4
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2018
---
# <a name="iis-logs-in-log-analytics"></a>Log Analytics의 IIS 로그
IIS(인터넷 정보 서비스)는 Log Analytics에서 수집할 수 있는 로그 파일에 사용자 활동을 저장합니다.  

![IIS 로그](media/log-analytics-data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>IIS 로그 구성
Log Analytics는 IIS에서 생성된 로그 파일의 항목을 수집하므로 [로깅에 대해 IIS를 구성](https://technet.microsoft.com/library/hh831775.aspx)해야 합니다.

Log Analytics는 W3C 형식으로 저장된 IIS 로그 파일만 지원하며 사용자 지정 필드 또는 IIS 고급 로깅을 지원하지 않습니다.  
Log Analytics는 NCSA 또는 IIS 네이티브 형식의 로그를 수집하지 않습니다.

[Log Analytics 설정의 데이터 메뉴](log-analytics-data-sources.md#configuring-data-sources)에서 Log Analytics의 IIS 로그를 구성합니다.  **W3C 형식 IIS 로그 파일 수집**을 선택하는 것 외에 다른 구성은 필요 없습니다.

IIS 로그 수집을 사용하도록 설정하는 경우 각 서버에서 IIS 로그 롤오버 설정을 구성하는 것이 좋습니다.

## <a name="data-collection"></a>데이터 수집
Log Analytics는 연결된 각 원본에서 대략 15분마다 IIS 로그 항목을 수집합니다.  에이전트는 수집하는 각 이벤트 로그에 해당 위치를 기록합니다.  에이전트가 오프라인 상태로 전환된 경우 Log Analytics는 마지막으로 오프라인 상태가 유지된 위치에서 이벤트를 수집하며, 이는 에이전트가 오프라인 상태에 있는 동안 해당 이벤트가 생성된 경우에도 마찬가지입니다.

## <a name="iis-log-record-properties"></a>IIS 로그 레코드 속성
IIS 로그 레코드는 **W3CIISLog** 형식이며, 다음 표의 속성이 있습니다.

| 자산 | 설명 |
|:--- |:--- |
| Computer |이벤트가 수집된 컴퓨터의 이름입니다. |
| cIP |클라이언트의 IP 주소입니다. |
| csMethod |GET 또는 POST와 같은 요청 메서드입니다. |
| csReferer |사용자가 현재 사이트로 이동하는 데 사용된 링크가 있던 사이트입니다. |
| csUserAgent |클라이언트의 브라우저 유형입니다. |
| csUserName |서버에 액세스한 인증된 사용자의 이름입니다. 익명 사용자는 하이픈으로 표시됩니다. |
| csUriStem |웹 페이지와 같은 요청의 대상입니다. |
| csUriQuery |클라이언트가 수행하려고 한 쿼리입니다(있는 경우). |
| ManagementGroupName |Operations Manager 에이전트의 관리 그룹 이름.  다른 에이전트의 경우 AOI-\<작업 영역 ID\>입니다. |
| RemoteIPCountry |클라이언트의 IP 주소 국가입니다. |
| RemoteIPLatitude |클라이언트 IP 주소의 위도입니다. |
| RemoteIPLongitude |클라이언트 IP 주소의 경도입니다. |
| scStatus |HTTP 상태 코드입니다. |
| scSubStatus |하위 상태 오류 코드입니다. |
| scWin32Status |Windows 상태 코드입니다. |
| sIP |웹 서버의 IP 주소입니다. |
| SourceSystem |OpsMgr |
| sPort |클라이언트가 연결된 서버의 포트입니다. |
| sSiteName |IIS 사이트의 이름입니다. |
| TimeGenerated |항목이 로깅된 날짜 및 시간입니다. |
| TimeTaken |요청을 처리할 시간의 길이(밀리초)입니다. |

## <a name="log-searches-with-iis-logs"></a>IIS 로그로 로그 검색
다음 표에는 IIS 로그 레코드를 검색하는 로그 쿼리의 여러 예제가 나와 있습니다.

| 쿼리 | 설명 |
|:--- |:--- |
| W3CIISLog |모든 IIS 로그 레코드 |
| W3CIISLog &#124; where scStatus==500 |반환 상태가 500인 모든 IIS 로그 레코드입니다. |
| W3CIISLog &#124; summarize count() by cIP |클라이언트 IP 주소별 IIS 로그 항목 수 |
| W3CIISLog &#124; where csHost=="www.contoso.com" &#124; summarize count() by csUriStem |호스트 www.contoso.com의 URL별 IIS 로그 항목 수 |
| W3CIISLog &#124; summarize sum(csBytes) by Computer &#124; take 500000 |각 IIS 컴퓨터에서 받은 총 바이트 수 |

## <a name="next-steps"></a>다음 단계
* 분석을 위해 다른 [데이터 원본](log-analytics-data-sources.md) 을 수집하도록 Log Analytics를 구성합니다.
* 데이터 원본 및 솔루션에서 수집한 데이터를 분석하기 위해 [로그 검색](log-analytics-log-searches.md)에 대해 알아봅니다.
* IIS 로그에서 발견된 중요한 조건을 사전에 알리도록 Log Analytics의 경고를 구성합니다.
