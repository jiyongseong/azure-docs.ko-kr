---
title: Azure Log Analytics에서 사용자 지정 로그 수집 | Microsoft Docs
description: Log Analytics는 Windows와 Linux 컴퓨터의 텍스트 파일에서 이벤트를 수집할 수 있습니다.  이 문서는 새 사용자 지정 로그를 정의하는 방법을 설명하고 Log Analytics 작업 영역에 만드는 레코드에 대한 자세한 정보를 제공합니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: aca7f6bb-6f53-4fd4-a45c-93f12ead4ae1
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2018
ms.author: bwren
ms.openlocfilehash: e4e2edeb6703e8c55a16b488175fbcdb0dfe56a9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361892"
---
# <a name="custom-logs-in-log-analytics"></a>Log Analytics의 사용자 지정 로그
Log Analytics의 사용자 지정 로그 데이터 원본을 통해 Windows 및 Linux 컴퓨터의 텍스트 파일에서 이벤트를 수집할 수 있습니다. 많은 응용 프로그램이 Windows 이벤트 로그 또는 Syslog 같은 표준 로깅 서비스 대신 텍스트 파일에 정보를 기록합니다.  수집된 후에는, Log Analytics의 [사용자 지정 필드](log-analytics-custom-fields.md) 기능을 사용하여 로그의 각 레코드를 별도의 필드로 구문 분석할 수 있습니다.

![사용자 지정 로그 수집](media/log-analytics-data-sources-custom-logs/overview.png)

수집할 로그 파일은 다음 조건과 일치해야 합니다.

- 로그는 줄 당 항목이 하나이거나 각 항목의 시작 지점에 다음 형식 중 하나와 일치하는 타임스탬프를 사용해야 합니다.

    YYYY-MM-DD HH:MM:SS <br>M/D/YYYY HH:MM:SS AM/PM<br>Mon DD, YYYY HH:MM:SS<br />yyMMdd HH:mm:ss<br />ddMMyy HH:mm:ss<br />MMM d hh:mm:ss<br />dd/MMM/yyyy:HH:mm:ss zzz<br />yyyy-MM-ddTHH:mm:ssK

- 로그 파일은 새 항목으로 파일을 덮어쓰는 순환 로깅 또는 로그 회전을 허용하지 말아야 합니다.
- 로그 파일은 ASCII 또는 UTF-8 인코딩을 사용해야 합니다.  UTF-16 등의 다른 형식은 지원되지 않습니다.

>[!NOTE]
>로그 파일에 중복된 항목이 있는 경우 Log Analytics에서 수집합니다.  그러나 검색 결과는 필터 결과가 결과 개수보다 더 많은 이벤트를 표시하는 위치에서 일치하지 않습니다.  이를 만드는 응용 프로그램에서 이 문제를 일으키는지 확인하도록 로그의 유효성을 검사하고 가능한 경우 사용자 지정 로그 컬렉션 정의를 만들기 전에 해결하는 것이 중요합니다.  
>
  
## <a name="defining-a-custom-log"></a>사용자 지정 로그 정의
다음 절차에 따라 사용자 지정 로그 파일을 정의합니다.  사용자 지정 로그를 추가하는 샘플에 대한 연습을 보려면 이 문서의 끝으로 스크롤합니다.

### <a name="step-1-open-the-custom-log-wizard"></a>1단계. Custom Log Wizard(사용자 지정 로그 마법사) 열기
사용자 지정 로그 마법사는 Azure Portal에서 실행되며 수집할 새 사용자 지정 로그를 정의할 수 있습니다.

1. Azure Portal에서 **Log Analytics** > 작업 영역 > **고급 설정**을 선택합니다.
2. **데이터** > **사용자 지정 로그**를 클릭합니다.
3. 기본적으로, 모든 구성 변경은 모든 에이전트로 자동 푸시됩니다.  Linux 에이전트에서, 구성 파일은 Fluentd 데이터 수집기로 전송됩니다.  각 Linux 에이전트에서 이 파일을 수동으로 수정하려면, *Apply below configuration to my Linux machines*(아래 구성을 내 Linux 컴퓨터에 적용) 확인란 선택을 해제합니다.
4. **추가+** 를 클릭하여 Custom Log Wizard(사용자 지정 로그 마법사)를 엽니다.

### <a name="step-2-upload-and-parse-a-sample-log"></a>2단계. 샘플 로그 업로드 및 구문 분석
사용자 지정 로그 샘플을 업로드하는 것부터 시작합니다.  마법사는 사용자가 유효성을 검사할 수 있도록 이 파일의 항목을 구문 분석하고 표시합니다.  Log Analytics는 사용자가 지정하는 구분 기호를 사용하여 각 레코드를 식별합니다.

**새 줄** 은 기본적인 구분 기호이며 줄당 하나의 항목을 포함하는 로그 파일에 사용됩니다.  줄이 사용 가능한 형식 중 한 가지의 날짜와 시간으로 시작되는 경우에는, 두 줄 이상에 걸쳐있는 항목을 지원하는 **타임스탬프** 구분 기호를 지정할 수 있습니다.

타임스탬프 구분 기호가 사용되면, Log Analytics에 저장된 각 레코드의 TimeGenerated 속성이 로그 파일의 해당 항목에 대해 지정된 날짜/시간으로 채워집니다.  새 줄 구분 기호가 사용되는 경우에는, TimeGenerated가 Log Analytics 항목을 수집한 날짜와 시간으로 채워집니다.


1. **찾아보기** 를 클릭하고 샘플 파일로 이동합니다.  일부 브라우저에서는 이 단추의 레이블이 **파일 선택** 인 경우도 있습니다.
2. **다음**을 클릭합니다.
3. Custom Log Wizard(사용자 지정 로그 마법사)가 파일을 업로드하고 식별된 레코드를 기록합니다.
4. 새 레코드 식별에 사용된 구분 기호를 변경하고 로그 파일의 레코드를 가장 잘 식별하는 구분 기호를 선택합니다.
5. **다음**을 클릭합니다.

### <a name="step-3-add-log-collection-paths"></a>3단계. 로그 수집 경로 추가
사용자 지정 로그를 찾을 수 있는 에이전트의 경로를 하나 이상의 지정해야 합니다.  로그 파일의 특정 경로 및 이름을 제공하거나 이름의 와일드카드를 포함하는 경로를 지정할 수 있습니다.  이렇게 하면 매일 새 파일을 만드는 응용 프로그램을 지원하거나 하나의 파일이 일정한 크기에 도달하는 경우를 지원합니다.  하나의 로그 파일에 여러 경로를 제공할 수도 있습니다.

예를 들어, 응용 프로그램이 이름에 날짜가 포함된 로그 파일(예: log20100316.txt)을 매일 만들 수 있습니다. 이런 로그의 패턴으로 *log\*.txt*를 사용할 수 있으며, 이것은 응용 프로그램의 명명 체계에 따르는 모든 로그 파일에 적용할 수 있습니다.

다음 테이블은 다른 로그 파일을 지정하는 데 유효한 패턴의 예를 제공합니다.

| 설명 | path |
|:--- |:--- |
| Windows 에이전트에서 확장명이 .txt인 *C:\Logs* 내 모든 파일 |C:\Logs\\\*.txt |
| Windows 에이전트에서 이름이 log로 시작되고 확장명이 .txt인 *C:\Logs* 내 모든 파일 |C:\Logs\log\*.txt |
| Linux 에이전트에서 확장명이 .txt인 */var/log/audit* 내 모든 파일 |/var/log/audit/*.txt |
| Linux 에이전트에서 이름이 log로 시작되고 확장명이 .txt인 */var/log/audit* 내 모든 파일 |/var/log/audit/log\*.txt |

1. Windows 또는 Linux를 선택하여 추가하는 경로 형식을 지정합니다.
2. 경로를 입력하고 **+** 단추를 클릭합니다.
3. 경로가 더 있으면 이 프로세스를 반복합니다.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>4단계. 로그의 이름과 설명을 제공합니다.
지정한 이름은 위의 설명처럼 로그 유형에 사용됩니다.  파일을 사용자 지정 로그로 구분하기 위해 항상_CL로 끝납니다.

1. 로그의 이름을 입력합니다.  **\_CL** 접미사가 자동으로 제공됩니다.
2. 선택적인 **설명**을 추가합니다.
3. **다음**을 클릭하여 사용자 지정 로그 정의를 저장합니다.

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>5단계. 사용자 지정 로그를 수집 중인지 확인
Log Analytics에 새 사용자 지정 로그의 초기 데이터가 나타나기 까지 최대 한 시간이 소요될 수 있습니다.  사용자 지정 로그를 정의한 시점부터, 사용자가 지정한 경로에서 찾은 로그로부터 항목을 수집하기 시작합니다.  사용자 지정 로그를 만드는 동안 업로드한 항목은 유지하지 않고, 찾는 로그 파일에 이미 존재하는 항목을 수집합니다.

Log Analytics가 사용자 지정 로그에서 수집을 시작하면, 해당 레코드를 로그 검색을 통해 사용할 수 있습니다.  사용자 지정 로그에 지정한 이름을 쿼리의 **유형**으로 사용합니다.

> [!NOTE]
> RawData 속성이 검색에 없으면, 브라우저를 닫았다가 다시 열어야 합니다.
>
>

### <a name="step-6-parse-the-custom-log-entries"></a>6단계. 사용자 지정 로그 항목 구문 분석
전체 로그 항목은 **RawData**라는 하나의 속성에 저장됩니다.  각 항목에 포함된 다양한 종류의 정보를 레코드에 저장된 개별 속성으로 분리하려는 경우가 많습니다.  이런 작업은 Log Analytics의 [사용자 지정 필드](log-analytics-custom-fields.md) 기능을 사용하여 수행합니다.

사용자 지정 로그 항목을 구문 분석하는 자세한 단계는 여기에 제공되지 않습니다.  이 정보는 [사용자 지정 필드](log-analytics-custom-fields.md) 설명서를 참조하세요.

## <a name="removing-a-custom-log"></a>사용자 지정 로그 제거
Azure Portal에서 다음 프로세스를 사용하여 이전에 정의한 사용자 지정 로그를 제거합니다.

1. 작업 영역에 대한 **고급 설정**의 **데이터** 메뉴에서 **사용자 지정 로그**를 선택하여 모든 사용자 지정 로그를 나열합니다.
2. 사용자 지정 로그 옆에 있는 **제거**를 클릭하여 제거합니다.


## <a name="data-collection"></a>데이터 수집
Log Analytics는 각 사용자 지정 로그로부터 새로운 항목을 약 5분마다 수집합니다.  에이전트는 데이터를 수집하는 각 로그 파일에서 해당 위치를 기록합니다.  에이전트가 일정 기간 동안 오프라인 상태로 전환된 경우 Log Analytics는 마지막으로 오프라인 상태가 유지된 위치에서 항목을 수집하며, 이는 에이전트가 오프라인 상태에 있는 동안 해당 항목이 생성된 경우에도 마찬가지입니다.

로그 항목의 전체 내용은 **RawData**라는 단일 속성에 기록됩니다.  이것을 사용자 지정 로그를 만든 후에 [사용자 지정 필드](log-analytics-custom-fields.md)를 정의하여 개별적으로 검색하고 분석할 수 있는 여러 개의 속성으로 구문 분석할 수 있습니다.

## <a name="custom-log-record-properties"></a>사용자 지정 로그 레코드 속성
사용자 지정 로그 레코드에는 사용자가 제공하는 로그 이름의 유형과 다음 테이블의 속성이 있습니다.

| 자산 | 설명 |
|:--- |:--- |
| TimeGenerated |Log Analytics에 의해 레코드가 수집된 날짜와 시간입니다.  로그에 시간 기반 구분 기호가 사용되는 경우, 항목에서 수집한 시간이 여기에 해당됩니다. |
| SourceSystem |레코드가 수집된 에이전트의 유형입니다. <br> OpsManager – Windows 에이전트, 직접 연결 또는 System Center Operations Manager <br> Linux – 모든 Linux 에이전트 |
| RawData |수집된 항목의 전체 텍스트. |
| ManagementGroupName |System Center Operations Manage 에이전트의 관리 그룹 이름입니다.  다른 에이전트의 경우 AOI-\<작업 영역 ID\>입니다. |

## <a name="log-searches-with-custom-log-records"></a>사용자 지정 로그 레코드를 사용한 로그 검색
사용자 지정 로그의 레코드는 다른 데이터 소스의 레코드처럼 Log Analytics 작업 영역에 저장됩니다.  Type이 로그를 정의할 때 제공한 이름과 일치하기 때문에 검색에 Type 속성을 이용하여 특정 로그에서 수집한 레코드를 검색할 수 있습니다.

다음 테이블은 사용자 지정 로그로부터 레코드를 검색하는 로그 검색의 다양한 예를 제공합니다.

| 쿼리 | 설명 |
|:--- |:--- |
| MyApp_CL |이름 MyApp_CL인 사용자 지정 로그의 모든 이벤트. |
| MyApp_CL &#124; where Severity_CF=="error" |이름이 *Severity_CF*인 사용자 지정 필드의 값이 *error*이고 이름이 MyApp_CL인 사용자 지정 로그의 모든 이벤트. |


## <a name="sample-walkthrough-of-adding-a-custom-log"></a>사용자 지정 로그 추가 샘플 연습
다음 섹션은 사용자 지정 로그를 만드는 예제를 안내합니다.  수집되는 샘플 로그에는 각 줄에 하나의 항목이 포함되며, 각 줄은 날짜와 시간으로 시작되고 코드, 상태 및 메시지에 대해 쉼표로 구분된 필드가 있습니다.  몇 가지 샘플 항목이 아래에 표시되어 있습니다.

    2016-03-10 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
    2016-03-10 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
    2016-03-10 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
    2016-03-10 01:38:22 302,Error,Application could not connect to database
    2016-03-10 01:31:34 303,Error,Application lost connection to database

### <a name="upload-and-parse-a-sample-log"></a>샘플 로그 업로드 및 구문 분석
로그 파일 중 하나를 제공하며 수집할 이벤트를 볼 수 있습니다.  이 경우 New Line(새 줄)은 충분한 구분 기호입니다.  하지만 로그의 단일 항목이 여러 줄에 걸치는 경우 타임스탬프 구분 기호가 사용되어야 합니다.

![샘플 로그 업로드 및 구문 분석](media/log-analytics-data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>로그 수집 경로 추가
로그 파일은 *C:\MyApp\Logs*에 배치됩니다.  *appYYYYMMDD.log*패턴의 날짜를 포함하는 이름으로 매일 새 파일이 생성됩니다.  이 로그에 충분한 패턴은 *C:\MyApp\Logs\\\*.log*입니다.

![로그 수집 경로](media/log-analytics-data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>로그의 이름과 설명을 제공합니다.
*MyApp_CL*이라는 이름을 사용하여 **설명**을 입력합니다.

![로그 이름](media/log-analytics-data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>사용자 지정 로그를 수집 중인지 확인
수집된 로그의 모든 레코드를 반환하는 *Type=MyApp_CL* 쿼리를 사용합니다.

![사용자 지정 필드가 없는 로그 쿼리](media/log-analytics-data-sources-custom-logs/query-01.png)

### <a name="parse-the-custom-log-entries"></a>사용자 지정 로그 항목 구문 분석
사용자 지정 필드를 사용하여 *EventTime*, *Code*, *Status*, *Message* 필드를 정의하며, 쿼리에 의해 반환되는 레코드의 차이를 볼 수 있습니다.

![사용자 지정 필드가 있는 로그 쿼리](media/log-analytics-data-sources-custom-logs/query-02.png)

## <a name="next-steps"></a>다음 단계
* [사용자 지정 필드](log-analytics-custom-fields.md)를 사용하여 사용자 지정 로그의 항목을 개별적인 필드로 구문 분석합니다.
* 데이터 원본 및 솔루션에서 수집한 데이터를 분석하기 위해 [로그 검색](log-analytics-log-searches.md)에 대해 알아봅니다.
