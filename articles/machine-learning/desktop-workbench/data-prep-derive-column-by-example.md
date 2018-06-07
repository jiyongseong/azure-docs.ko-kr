---
title: Azure Machine Learning Workbench를 사용하여 예제별 열 파생 변환
description: "'예제별 열 파생' 변환에 대한 참조 문서"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 201492f033ad3e2508bc75c0865036e7c83a652a
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="derive-column-by-example-transformation"></a>예제별 열 파생 변환

**예제별 열 파생** 변환을 통해 사용자는 하나 이상의 기존 열의 파생 항목을 사용자가 제공한 파생 결과 예제를 사용하여 만들도록 설정할 수 있습니다. 파생은 지원되는 문자열, 날짜 및 숫자 변환의 어떤 조합이든 될 수 있습니다. 

다음 문자열, 날짜 및 숫자 변환이 지원됩니다.

**문자열 변환:** 

숫자 및 날짜의 지능형 추출, 연결, 대/소문자 조작, 상수 값 매핑을 포함하는 부분 문자열.

**날짜 변환:** 

날짜 형식 변경, 날짜 부분 추출, 시간을 시간 Bin에 매핑.

날짜 변환은 상당히 일반적이며 몇 가지 주목할 만한 제한 사항이 있습니다.
* 표준 시간대는 지원되지 않습니다.
* 지원되지 않는 몇 가지 일반적인 형식:
    * ISO 8601 연중주차 형식(예: “2009-W53-7”) 
    * Unix Epoch 시간.
* 모든 형식은 대 소문자를 구분합니다.("4AM"은 시간으로 인식되지만 "4am"은 시간으로 인식되지 않습니다).

**숫자 변환:** 

Round, Floor, Ceiling, 범주화, 0이나 공백인 안쪽 여백, 또는 1000의 거듭제곱으로 나누기 또는 곱하기.

**날짜 변환:** 

문자열, 숫자 또는 날짜 변환의 모든 조합.

## <a name="how-to-use-this-transformation"></a>이 변환을 사용하는 방법

이 변환을 수행하려면 다음 단계를 따르세요.
1. 값을 파생하고자 하는 하나 이상의 열을 선택합니다. 
2. **변환** 메뉴에서 **예제별 열 파생**을 선택합니다. 또는 선택한 열의 헤더를 마우스 오른쪽 단추로 클릭 하거나 상황에 맞는 메뉴에서 **예제별 열 파생**을 선택합니다. 변환 편집기가 열리고 새 열이 가장 많이 선택된 열 옆에 추가됩니다. 열 머리글의 확인란을 선택하여 선택한 열을 식별할 수 있습니다. 열 머리글의 확인란을 사용하여 선택 영역에서 열을 추가 및 제거할 수 있습니다.
3. 행에 대한 *출력*의 예를 입력하고 Enter 키를 누릅니다. 이 시점에서 워크벤치는 주어진 입력을 출력으로 변환할 수 있는 프로그램을 합성하기 위해 입력 열은 물론 제공된 출력을 분석합니다. 합성된 프로그램은 데이터 눈금의 모든 행에 대해 실행됩니다. 모호하고 복잡한 경우, 많은 예제가 필요할 수 있습니다. 기본 모드인지 고급 모드인지에 따라, 많은 예제가 다양한 방식으로 제공될 수 있습니다.
4. 출력을 검토하고 **확인**을 클릭하여 변환을 동의합니다.

### <a name="transform-editor-basic-mode"></a>변환 편집기: 기본 모드

기본 모드는 데이터 눈금에서 인라인 편집 환경을 제공합니다. 해당 셀로 이동하여 값을 입력하면 출력의 예를 제공할 수 있습니다. 

워크벤치는 데이터를 분석하고 사용자가 검토해야 할 극단 사례를 식별하고자 시도합니다. 데이터가 분석되는 동안 **데이터 분석 중**이 변환 편집기의 헤더에 표시됩니다. 분석이 완료되고 나면 **제안 없음** 또는 **다음 제안 행 검토**가 헤더에 표시됩니다. **다음 제안 행 검토** 행을 클릭하여 극단 사례를 탐색할 수 있습니다. 행에 대한 값이 정확하지 않은 경우 정확한 값을 추가 예제로 입력해야 합니다. 

### <a name="transform-editor-advanced-mode"></a>변환 편집기: 고급 모드

고급 모드는 예제별 열 파생에 대한 보다 풍부한 환경을 제공합니다. 모든 예는 한 곳에 표시됩니다. **제안 예 표시**를 클릭하여 모든 극단 사례를 한 곳에서 검토할 수 있습니다 

고급 모드에서 눈금의 행을 두 번 클릭하여 어떤 행이든 예제 행으로 추가할 수 있습니다. 행이 예제 행으로 복사되면, 원본 열의 데이터를 편집하여 가상 예제를 만들 수 있습니다. 이렇게 하면 현재 샘플 데이터에 존재하지 않는 사례를 추가할 수 있습니다.

사용자는 변환 편집기에 있는 링크를 클릭하여 **기본 모드**와 **고급 모드** 간을 전환할 수 있습니다.

### <a name="transform-editor-send-feedback"></a>변환 편집기: 피드백 보내기

**피드백 보내기** 링크를 클릭하면 사용자가 제공한 예제로 주석 상자가 미리 채워진 **피드백** 대화 상자가 열립니다. 사용자는 주석 상자의 내용을 검토하고 문제를 이해하는 데 필요한 자세한 정보를 제공해야 합니다. Microsoft와 데이터를 공유하지 않으려면 **피드백 보내기** 단추를 클릭하기 전에 미리 채워진 예제 데이터를 삭제해야 합니다. 

### <a name="editing-existing-transformation"></a>기존 변환 편집

사용자는 변환 단계의 **편집** 옵션을 선택하여 기존 **예제별 열 파생** 변환을 편집할 수 있습니다. **편집**을 클릭하면 변환 편집기가 **고급 모드**로 열리고, 변환 생성 중에 제공된 모든 예가 표시됩니다.

## <a name="examples-of-string-transformations-by-example"></a>예제별 열 파생의 예


>[!NOTE] 
>**볼드체** 값은 표시된 데이터 집합의 변환을 완료하기 위해 제공된 예제를 나타냅니다.


### <a name="s1-extracting-file-names-from-file-paths"></a>S1. 파일 경로에서 파일 이름 추출

이 경우 필요한 예제 수: 2

|입력|출력|
|:-----|:-----|
|C:\Python35\Tools\pynche\TypeinViewer.py|**TypeinViewer.py**|
|C:\Python35\Tools\pynche\webcolors.txt|webcolors.txt|
|C:\Python35\Tools\pynche\websafe.txt|websafe.txt|
|C:\Python35\Tools\pynche\X\rgb.txt|rgb.txt|
|C:\Python35\Tools\pynche\X\xlicense.txt|xlicense.txt|
|C:\Python35\Tools\Scripts\2to3.py|2to3.py|
|C:\Python35\Tools\Scripts\analyze_dxp.py|**analyze_dxp.py**|
|C:\Python35\Tools\Scripts\byext.py|byext.py|
|C:\Python35\Tools\Scripts\byteyears.py|byteyears.py|
|C:\Python35\Tools\Scripts\checkappend.py|checkappend.py|

### <a name="s2-case-manipulation-during-string-extraction"></a>S2. 문자열 추출 중 대/소문자 조작

이 경우 필요한 예제 수: 3

|입력|출력|
|:-----|:-----|
|REINDEER CT & DEAD END;  NEW HANOVER; Station 332; 2015-12-10 @ 17:10:52;|**New Hanover**|
|BRIAR PATH & WHITEMARSH LN;  HATFIELD TOWNSHIP; Station 345; 2015-12-10 @ 17:29:21;|Hatfield Township|
|HAWS AVE; NORRISTOWN; 2015-12-10 @ 14:39:21-Station:STA27;|**Norristown**|
|AIRY ST & SWEDE ST;  NORRISTOWN; Station 308A; 2015-12-10 @ 16:47:36;|**Norristown**|
|CHERRYWOOD CT & DEAD END;  LOWER POTTSGROVE; Station 329; 2015-12-10 @ 16:56:52;|Lower Pottsgrove|
|CANNON AVE & W 9TH ST;  LANSDALE; Station 345; 2015-12-10 @ 15:39:04;|Lansdale|
|LAUREL AVE & OAKDALE AVE;  HORSHAM; Station 352; 2015-12-10 @ 16:46:48;|Horsham|
|COLLEGEVILLE RD & LYWISKI RD;  SKIPPACK; Station 336; 2015-12-10 @ 16:17:05;|Skippack|
|MAIN ST & OLD SUMNEYTOWN PIKE;  LOWER SALFORD; Station 344; 2015-12-10 @ 16:51:42;|Lower Salford|
|BLUEROUTE  & RAMP I476 NB TO CHEMICAL RD; PLYMOUTH; 2015-12-10 @ 17:35:41;|Plymouth|
|RT202 PKWY & KNAPP RD; MONTGOMERY; 2015-12-10 @ 17:33:50;|Montgomery|
|BROOK RD & COLWELL LN; PLYMOUTH; 2015-12-10 @ 16:32:10;|Plymouth|

### <a name="s3-date-format-manipulation-during-string-extraction"></a>S3. 문자열 추출 중 날짜 형식 조작

이 경우 필요한 예제 수: 1

|입력|출력|
|:-----|:-----|
|MONTGOMERY AVE & WOODSIDE RD;  LOWER MERION; Station 313; 2015-12-11 @ 04:11:35;|**12 Nov 2015 4AM**|
|DREYCOTT LN & W LANCASTER AVE;  LOWER MERION; Station 313; 2015-12-11 @ 01:29:52;|12 Nov 2015 1AM|
|E LEVERING MILL RD & CONSHOHOCKEN STATE RD; LOWER MERION; 2015-12-11 @ 07:29:58;|12 Nov 2015 7AM|
|PENN VALLEY RD & MANOR RD;  LOWER MERION; Station 313; 2015-12-10 @ 20:53:30;|12 Oct 2015 8PM|
|BELMONT AVE & OVERHILL RD; LOWER MERION; 2015-12-10 @ 23:02:27;|12 Oct 2015 11PM|
|W MONTGOMERY AVE & PENNSWOOD RD; LOWER MERION; 2015-12-10 @ 19:25:22;|12 Oct 2015 7PM|
|ROSEMONT AVE & DEAD END;  LOWER MERION; Station 313; 2015-12-10 @ 18:43:07;|12 Oct 2015 6PM|
|AVIGNON DR & DEAD END; LOWER MERION; 2015-12-10 @ 20:01:29-Station:STA24;|12 Oct 2015 8PM|

### <a name="s4-concatenating-strings"></a>S4. 문자열 연결

이 경우 필요한 예제 수: 1

>[!NOTE] 
>이 예제에서 특수 문자 · 는 출력 열에서 공백을 나타냅니다.

|이름|중간 이니셜|성|출력|
|:-----|:-----|:-----|:-----|
|Laquanda||Lohmann|Laquanda··Lohmann|
|Claudio|A|Chew|**Claudio·A·Chew**|
|Sarah-Jane|S|Smith|Sarah-Jane·S·Smith|
|Brandi||Blumenthal|Brandi··Blumenthal|
|Jesusita|R|Journey|Jesusita·R·Journey|
|Hermina||Hults|Hermina··Hults|
|Anne-Marie|W|Jones|Anne-Marie·W·Jones|
|Rico||Ropp|Rico··Ropp|
|Lauren-May||Fullmer|Lauren-May··Fullmer|
|Marc|T|Maine|Marc·T·Maine|
|Angie||Adelman|Angie··Adelman|
|John-Paul||Smith|John-Paul··Smith|
|Song|W|Staller|Song·W·Staller|
|Jill||Jefferies|Jill··Jefferies|
|Ruby-Grace|M|Simmons|Ruby-Grace·M·Simmons|

### <a name="s5-generating-initials"></a>S5. 이니셜 생성

이 경우 필요한 예제 수: 2

|전체 이름|출력|
|:-----|:-----|
|Laquanda Lohmann|**L.L.**|
|Claudio Chew|C.C.|
|Sarah-Jane Smith|S.S.|
|Brandi Blumenthal|B.B.|
|Jesusita Journey|J.J.|
|Hermina Hults|H.H.|
|Anne-Marie Jones|A.J.|
|Rico Ropp|R.R.|
|Lauren-May Fullmer|L.F.|
|Marc Maine|M.M.|
|Angie Adelman|A.A.|
|John-Paul Smith|**J.S.**|
|Song Staller|S.S.|
|Jill Jefferies|J.J.|
|Ruby-Grace Simmons|R.S.|


### <a name="s6-mapping-constant-values"></a>S6. 상수 값 매핑

이 경우 필요한 예제 수: 3

|관리 성별|출력|
|:-----|:-----:|
|Male|**0**|
|Female|**1**|
|알 수 없음|**2**|
|Female|1|
|Female|1|
|Male|0|
|알 수 없음|2|
|Male|0|
|Female|1|

## <a name="examples-of-number-transformations-by-example"></a>예제별 숫자 파생의 예

>[!NOTE] 
>**볼드체** 값은 표시된 데이터 집합의 변환을 완료하기 위해 제공된 예제를 나타냅니다.


### <a name="n1-rounding-to-nearest-10"></a>N1. 가장 가까운 10으로 반올림

이 경우 필요한 예제 수: 1

|입력|출력|
|-----:|-----:|
|112|**110**|
|117|120|
|11112|11110|
|11119|11120|
|548|550|

### <a name="n2-rounding-down-to-nearest-10"></a>N2. 가장 가까운 10으로 반내림

이 경우 필요한 예제 수: 2

|입력|출력|
|-----:|-----:|
|112|**110**|
|117|**110**|
|11112|11110|
|11119|11110|
|548|540|

### <a name="n3-rounding-to-nearest-005"></a>N3. 가장 가까운 0.05으로 반올림

이 경우 필요한 예제 수: 2

|입력|출력|
|-----:|-----:|
|-75.5812935|**-75.60**|
|-75.2646799|-75.25|
|-75.3519752|-75.35|
|-75.343513|**-75.35**|
|-75.6033497|-75.60|
|-75.283245|-75.30|

### <a name="n4-binning"></a>N4. 범주화

이 경우 필요한 예제 수: 1

|입력|출력|
|-----:|:-----:|
|20.16|**20-25**|
|14.32|10-15|
|5.44|5-10|
|3.84|0-5|
|3.73|0-5|
|7.36|5-10|

### <a name="n5-scaling-by-1000"></a>N5. 1000으로 크기 조정

이 경우 필요한 예제 수: 1

|입력|출력|
|-----:|-----:|
|-243|**-243000**|
|-12.5|-12500|
|-2345.23292|-2345232.92|
|-1202.3433|-1202343.3|
|1202.3433|1202343.3|

### <a name="n6-padding"></a>N6. 안쪽 여백

이 경우 필요한 예제 수: 1

|코드|출력|
|-----:|-----:|
|5828|**05828**|
|44130|44130|
|49007|49007|
|29682|29682|
|4759|04759|
|10029|10029|
|7204|07204|

## <a name="examples-of-date-transformations-by-example"></a>예제별 날짜 파생의 예

>[!NOTE] 
>**볼드체** 값은 표시된 데이터 집합의 변환을 완료하기 위해 제공된 예제를 나타냅니다.


### <a name="d1-extracting-date-parts"></a>D1. 날짜 부분 추출

이러한 날짜 부분은 동일한 데이터 집합에 서로 다른 예제별 변환을 사용하여 추출되었습니다. 볼드체 문자열은 각 해당 변환에서 제공된 예제를 나타냅니다.

|Datetime|요일|Date|월|Year|Hour|분|초|
|-----:|-----:|-----:|-----:|-----:|-----:|-----:|-----:|
|31-Jan-2031 05:54:18|**Fri**|**31**|**Jan**|**2031**|**5**|**54**|**18**|
|17-Jan-1990 13:32:01|Wed|17|Jan|1990|13|32|01|
|14-Feb-2034 05:36:07|Tue|14|Feb|2034|5|36|07|
|14-Mar-2002 13:16:16|Thu|14|Mar|2002|13|16|16|
|21-Jan-1985 05:44:43|Mon|21|Jan|1985|5|44|**43**|
|16-Aug-1985 01:11:56|Fri|16|Aug|1985|1|11|56|
|20-Dec-2033 18:36:29|Tue|20|Dec|2033|18|36|29|
|16-Jul-1984 10:21:59|Mon|16|Jul|1984|10|21|59|
|13-Jan-2038 10:59:36|Wed|13|Jan|2038|10|59|36|
|14-Aug-1982 15:13:54|Sat|14|Aug|1982|15|13|54|
|22-Nov-2030 08:18:08|Fri|22|Nov|2030|8|18|08|
|21-Oct-1997 08:42:58|Tue|21|Oct|1997|8|42|58|
|28-Nov-2006 14:19:15|Tue|28|Nov|2006|14|19|15|
|29-Apr-2031 04:59:45|Tue|29|Apr|2031|4|59|45|
|29-Jan-2032 02:38:36|Thu|29|Jan|2032|2|38|36|
|11-May-2028 15:31:52|Thu|11|May|2028|15|31|52|
|15-Jul-1977 12:45:39|Fri|15|Jul|1977|12|45|39|
|27-Jan-2029 05:55:41|Sat|27|Jan|2029|5|55|41|
|03-Mar-2024 10:17:49|Sun|3|Mar|2024|10|17|49|
|14-Apr-2010 00:23:13|Wed|14|Apr|2010|0|23|13|

### <a name="d2-formatting-dates"></a>D2. 날짜 형식 지정

이러한 날짜 형식 지정은 동일한 데이터 집합에 서로 다른 예제별 변환을 사용하여 실행되었습니다. 볼드체 문자열은 각 해당 변환에서 제공된 예제를 나타냅니다.

|Datetime|Format1|Format2|Format3|Format4|Format5|
|-----:|-----:|-----:|-----:|-----:|-----:|
|31-Jan-2031 05:54:18|**1/31/2031**|**Friday, January 31, 2031**|**01312031 5:54**|**31/1/2031 5:54 AM**|**Q1 2031**|
|17-Jan-1990 13:32:01|1/17/1990|Wednesday, January 17, 1990|01171990 13:32|17/1/1990 1:32 PM|Q1 1990|
|14-Feb-2034 05:36:07|2/14/2034|Tuesday, February 14, 2034|02142034 5:36|14/2/2034 5:36 AM|Q1 2034
|14-Mar-2002 13:16:16|3/14/2002|Thursday, March 14, 2002|03142002 13:16|14/3/2002 1:16 PM|Q1 2002
|21-Jan-1985 05:44:43|1/21/1985|Monday, January 21, 1985|01211985 5:44|21/1/1985 5:44 AM|Q1 1985
|16-Aug-1985 01:11:56|8/16/1985|Friday, August 16, 1985|08161985 1:11|16/8/1985 1:11 AM|Q3 1985
|20-Dec-2033 18:36:29|12/20/2033|Tuesday, December 20, 2033|12202033 18:36|20/12/2033 6:36 PM|Q4 2033
|16-Jul-1984 10:21:59|7/16/1984|Monday, July 16, 1984|07161984 10:21|16/7/1984 10:21 AM|Q3 1984
|13-Jan-2038 10:59:36|1/13/2038|Wednesday, January 13, 2038|01132038 10:59|13/1/2038 10:59 AM|Q1 2038
|14-Aug-1982 15:13:54|8/14/1982|Saturday, August 14, 1982|08141982 15:13|14/8/1982 3:13 PM|Q3 1982
|22-Nov-2030 08:18:08|11/22/2030|Friday, November 22, 2030|11222030 8:18|22/11/2030 8:18 AM|Q4 2030
|21-Oct-1997 08:42:58|10/21/1997|Tuesday, October 21, 1997|10211997 8:42|21/10/1997 8:42 AM|Q4 1997
|28-Nov-2006 14:19:15|11/28/2006|Tuesday, November 28, 2006|11282006 14:19|28/11/2006 2:19 PM|Q4 2006
|29-Apr-2031 04:59:45|4/29/2031|Tuesday, April 29, 2031|04292031 4:59|29/4/2031 4:59 AM|Q2 2031
|29-Jan-2032 02:38:36|1/29/2032|Thursday, January 29, 2032|01292032 2:38|29/1/2032 2:38 AM|Q1 2032
|11-May-2028 15:31:52|5/11/2028|Thursday, May 11, 2028|05112028 15:31|11/5/2028 3:31 PM|Q2 2028
|15-Jul-1977 12:45:39|7/15/1977|Friday, July 15, 1977|07151977 12:45|15/7/1977 12:45 PM|Q3 1977
|27-Jan-2029 05:55:41|1/27/2029|Saturday, January 27, 2029|01272029 5:55|27/1/2029 5:55 AM|Q1 2029
|03-Mar-2024 10:17:49|3/3/2024|Sunday, March 3, 2024|03032024 10:17|3/3/2024 10:17 AM|Q1 2024
|14-Apr-2010 00:23:13|4/14/2010|Wednesday, April 14, 2010|04142010 0:23|14/4/2010 12:23 AM|Q2 2010


### <a name="d3-mapping-time-to-time-periods"></a>D3. 시간을 기간으로 매핑

이러한 날짜/시간의 기간 매핑은 동일한 데이터 집합에 서로 다른 예제별 변환을 사용하여 실행되었습니다. 볼드체 문자열은 각 해당 변환에서 제공된 예제를 나타냅니다.

|Datetime|기간(초)|기간(분)|기간(2시간)|기간(30분)|
|-----:|-----:|-----:|-----:|-----:|
|31-Jan-2031 05:54:18|**0-20**|**45-60**|**5AM-7AM**|**5:30-6:00**|
|17-Jan-1990 13:32:01|**0-20**|30-45|1PM-3PM|13:30-14:00|
|14-Feb-2034 05:36:07|0-20|30-45|5AM-7AM|5:30-6:00|
|14-Mar-2002 13:16:16|0-20|15-30|1PM-3PM|13:00-13:30|
|21-Jan-1985 05:44:43|40-60|30-45|5AM-7AM|5:30-6:00|
|16-Aug-1985 01:11:56|40-60|0-15|1AM-3AM|1:00-1:30|
|20-Dec-2033 18:36:29|20-40|30-45|5PM-7PM|18:30-19:00|
|16-Jul-1984 10:21:59|40-60|15-30|9AM-11AM|10:00-10:30|
|13-Jan-2038 10:59:36|20-40|45-60|9AM-11AM|10:30-11:00|
|14-Aug-1982 15:13:54|40-60|0-15|3PM-5PM|15:00-15:30|
|22-Nov-2030 08:18:08|0-20|15-30|7AM-9AM|8:00-8:30|
|21-Oct-1997 08:42:58|40-60|30-45|7AM-9AM|8:30-9:00|
|28-Nov-2006 14:19:15|0-20|15-30|1PM-3PM|14:00-14:30|
|29-Apr-2031 04:59:45|40-60|45-60|3AM-5AM|4:30-5:00|
|29-Jan-2032 02:38:36|20-40|30-45|1AM-3AM|2:30-3:00|
|11-May-2028 15:31:52|40-60|30-45|3PM-5PM|15:30-16:00|
|15-Jul-1977 12:45:39|20-40|45-60|11AM-1PM|12:30-13:00|
|27-Jan-2029 05:55:41|40-60|45-60|5AM-7AM|5:30-6:00|
|03-Mar-2024 10:17:49|40-60|15-30|9AM-11AM|10:00-10:30|
|14-Apr-2010 00:23:13|0-20|15-30|11PM-1AM|0:00-0:30|

## <a name="examples-of-composite-transformations-by-example"></a>예제별 복합 변환의 예

|대여 시간|시작 시간|시작 스테이션 id|시작 스테이션 위도|시작 스테이션 경도|사용자 유형|열|
|-----:|-----:|-----:|-----:|-----:|-----:|-----:|
|61|2016-01-08 16:09:32|107|42.3625|-71.08822|가입자|**가입자가 2016년 1월 8일 4PM 경에 스테이션 107, 위도/경도(42.363,-71.088)에서 자전거를 택하셨습니다. 대여 시간은 61분입니다**|
|61|2016-01-17 09:28:10|74|42.373268|-71.118579|고객|고객이 2016년 1월 17일 9AM 경에 스테이션 74, 위도/경도(42.373,-71.119)에서 자전거를 택하셨습니다. 대여 시간은 61분입니다|
|62|2016-01-25 08:10:26|176|42.386748020450561|-71.119018793106079|가입자|가입자가 2016년 1월 25일 8AM 경에 스테이션 176, 위도/경도(42.387,-71.119)에서 자전거를 택하셨습니다. 대여 시간은 62분입니다|
|63|2016-01-08 10:10:29|107|42.3625|-71.08822|가입자|가입자가 2016년 1월 8일 10AM 경에 스테이션 107, 위도/경도(42.363,-71.088)에서 자전거를 택하셨습니다. 대여 시간은 63분입니다|
|64|2016-01-15 19:42:08|68|42.36507|-71.1031|가입자|가입자가 2016년 1월 15일 7PM 경에 스테이션 68, 위도/경도(42.365,-71.103)에서 자전거를 택하셨습니다. 대여 시간은 64분입니다|
|64|2016-01-22 18:16:13|115|42.387995|-71.119084|가입자|가입자가 2016년 1월 22일 6PM 경에 스테이션 115, 위도/경도(42.388,-71.119)에서 자전거를 택하셨습니다. 대여 시간은 64분입니다|
|68|2016-01-18 09:51:52|178|42.359573201090441|-71.101294755935669|가입자|가입자가 2016년 1월 18일 9AM 경에 스테이션 178, 위도/경도(42.360,-71.101)에서 자전거를 택하셨습니다. 대여 시간은 68분입니다|
|69|2016-01-14 08:57:55|176|42.386748020450561|-71.119018793106079|가입자|가입자가 2016년 1월 14일 8AM 경에 스테이션 176, 위도/경도(42.387,-71.119)에서 자전거를 택하셨습니다. 대여 시간은 69분입니다|
|69|2016-01-13 22:12:55|141|42.363560158429884|-71.08216792345047|가입자|가입자가 2016년 1월 13일 10PM 경에 스테이션 141, 위도/경도(42.364,-71.082)에서 자전거를 택하셨습니다. 대여 시간은 69분입니다|
|69|2016-01-15 08:13:09|176|42.386748020450561|-71.119018793106079|가입자|가입자가 2016년 1월 15일 8AM 경에 스테이션 176, 위도/경도(42.387,-71.119)에서 자전거를 택하셨습니다. 대여 시간은 69분입니다|


## <a name="technical-notes"></a>기술 정보

### <a name="conditional-transformations"></a>조건부 변환
일부 경우 주어진 예제를 충족하는 단일 변환을 찾을 수 없습니다. 이 경우 예제별 열 파생은 몇 가지 패턴에 따라 입력을 그룹화하고 각 그룹에 대한 별도 변환을 알아보려 시도합니다. 이것을 **조건부 변환**이라고 합니다. **조건부 변환**은 입력 열이 하나인 변환에 대해서만 시도됩니다. 

### <a name="reference"></a>참고 자료
예제별 문자열 변환 기술에 관한 자세한 내용은 [이 게시물](https://www.microsoft.com/research/publication/automating-string-processing-spreadsheets-using-input-output-examples/)에서 찾을 수 있습니다.
