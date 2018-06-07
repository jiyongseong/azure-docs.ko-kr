---
title: "Azure Log Analytics에서 로그 쿼리를 만들고 편집하기 위한 포털 | Microsoft Docs"
description: "이 문서에서는 로그 분석을 만들고 편집하는 데 Azure Log Analytics에서 사용할 수 있는 포털을 설명합니다."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: magoedte; bwren
ms.openlocfilehash: b205f226d95d94b938a70a834ac0147e76d459ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="portals-for-creating-and-editing-log-queries-in-azure-log-analytics"></a>Azure Log Analytics에서 로그 쿼리를 만들고 편집하기 위한 포털 | Microsoft Docs

Log Analytics를 통해 다양한 장소에서 로그 검색을 사용하여 작업 영역에서 데이터를 검색합니다.  실제 쿼리 만들기 및 편집의 경우 반환되는 데이터와 대화형으로 작업하는 것 외에 아래에서 설명하는 두 가지 옵션이 있습니다.  

## <a name="log-search"></a>로그 검색 
로그 검색 페이지는 Azure Portal에서 액세스할 수 있습니다.  한 줄에 만들 수 있는 기본 쿼리를 만들기에 적합합니다.  로그 검색은 외부 포털을 시작하지 않고도 사용할 수 있으며, 로그 검색을 통해 경고 규칙 생성, 컴퓨터 그룹 생성 및 쿼리 결과 내보내기와 같은 다양한 기능을 수행할 수 있습니다.  

로그 검색은 쿼리 언어에 대한 완전한 지식을 갖추지 않고도 사용할 수 있는 쿼리 편집을 위한 여러 기능을 제공합니다.  [로그 검색을 사용하는 Azure Log Analytics에서 로그 검색 만들기](log-analytics-log-search-log-search-portal.md)에서 이러한 기능에 대한 요약을 확인할 수 있습니다.


![로그 검색 페이지](media/log-analytics-log-search-portals/log-search-portal.png)

## <a name="advanced-analytics-portal"></a>고급 분석 포털
고급 분석 포털은 Azure Portal의 로그 검색에서 사용할 수 없는 고급 기능을 제공하는 전용 포털입니다.  여기에는 여러 줄, 선택적인 실행 코드, 상황에 맞는 중요한 Intellisense 및 스마트 분석에서 쿼리를 편집하는 기능이 포함됩니다.  고급 분석 포털은 로그 검색으로 저장되거나, 복사되어 Log Analytics 요소로 붙여넣기되는 복잡한 쿼리를 설계하는 데 가장 적합합니다.  로그 검색 페이지의 링크에서 고급 분석 포털을 시작합니다.

![고급 분석 포털](media/log-analytics-log-search-portals/advanced-analytics-portal.png)


고급 기능 덕분에 일반적으로 쿼리를 만들고 편집하는 기본 도구로써 고급 분석 포털을 사용하게 될 것입니다.  쿼리가 예상대로 작동하는 것으로 판단되면, 복사하여 로그 검색 페이지 또는 뷰 디자이너와 같은 다른 곳에 붙여넣습니다.  고급 분석 포털은 여러 줄이 포함된 쿼리를 지원하므로 이 포털에서 쿼리를 복사하는 경우 다음 사항을 고려해야 합니다.

- 복사하여 다른 위치에 붙여넣기 전에 쿼리에서 주석을 제거해야 합니다.  줄 앞에 슬래시(//) 두 개를 삽입하면 주석을 달 수 있습니다.  여러 줄 쿼리를 한 줄로 붙여넣는 경우 줄 바꿈이 제거됩니다.  주석이 포함되는 경우 첫 번째 주석 이후의 모든 문자는 주석의 일부로 간주됩니다.


## <a name="next-steps"></a>다음 단계

- 쿼리 언어를 사용하여 쿼리를 작성하는 방법은 [로그 검색](log-analytics-tutorial-viewdata.md) 사용에 대한 자습서를 살펴보세요.
- 복잡한 쿼리를 작성하고 로그 검색을 위한 개발 환경으로 사용하려면 [고급 분석 포털](https://go.microsoft.com/fwlink/?linkid=856587)을 확인하세요.

