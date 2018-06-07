---
title: Visual Studio용 Azure Stream Analytics 도구 사용
description: 이 아티클에서는 Azure Stream Analytics Tools for Visual Studio를 사용하여 시작하는 방법을 설명합니다.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: fe9998e3b1f5e2a3376f833c3dd8212e4a639274
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33941839"
---
# <a name="use-azure-stream-analytics-tools-for-visual-studio"></a>Visual Studio용 Azure Stream Analytics 도구 사용
Visual Studio용 Azure Stream Analytics 도구는 현재 일반 공급 상태입니다. Stream Analytics 사용자는 이러한 도구를 통해 풍부한 경험을 얻고 문제를 해결할 수 있을 뿐만 아니라 복잡한 쿼리를 작성하고 로컬에서도 쿼리를 작성할 수 있습니다. 또한 Stream Analytics 작업을 Visual Studio 프로젝트로 내보낼 수 있습니다.

## <a name="introduction"></a>소개
이 자습서에서는 Visual Studio용 Stream Analytics 도구를 사용하여 Stream Analytics 작업을 만들고, 작성하며, 로컬에서 테스트하고, 관리 및 디버그하는 방법에 대해 알아봅니다. 작업을 만든 후 CI/CD Nuget 패키지를 사용하여 Azure에 대해 연속 통합 및 배포 프로세스를 설정할 수 있습니다. 자세한 내용은 [CI/CD 파이프라인을 설정하기 위한 Stream Analytics VS 도구](stream-analytics-tools-for-visual-studio-cicd.md) 문서를 참조하세요.

이 자습서를 완료하고 나면 다음을 수행할 수 있습니다.

* Visual Studio용 Azure Stream Analytics 도구 학습 및 이해
* Stream Analytics 작업 구성 및 배포
* 로컬 샘플 데이터로 로컬에서 작업 테스트
* 모니터링을 사용하여 문제 해결
* 기존 작업을 프로젝트로 내보내기

>[!IMPORTANT]
>
>2018년 5월 25일에 발효될 새 GDPR(일반 데이터 보호 규정)에 대비하여 Azure Stream Analytics Tools for Visual Studio 사용자는 버전 2.3.3000.5 이상으로 업그레이드하는 것이 좋습니다. 이 버전에는 최신 데이터 보호 요구 사항에 따른 변경 내용이 포함되어 있습니다. 이전 버전은 다운로드할 수 없으며 더 이상 사용되지 않습니다. 
>
>**무엇을 해야 하나요?**
>
>1. 사용 중인 Azure Stream Analytics Tools for Visual Studio 버전이 2.3.3000.5 이전인지 확인합니다. 
>   
>   ![도구 버전 확인](./media/stream-analytics-tools-for-vs/about-data-lake.png)
> 
>2. 버전이 2.3.3000.5 이전이면 다운로드 센터를 방문하여 Azure Data Lake Tools for Visual Studio를 업데이트합니다. 
>    - [Visual Studio 2017의 경우](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools)
>    - [Visual Studio 2013 및 2015의 경우](https://www.microsoft.com/en-us/download/details.aspx?id=54630)

## <a name="prerequisites"></a>필수 조건
이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

* [Stream Analytics를 사용하여 IoT 솔루션 빌드](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics) 자습서에서 "Stream Analytics 작업 만들기"의 단계를 완료합니다. 
* Visual Studio 2017, Visual Studio 2015 또는 Visual Studio 2013 업데이트 4를 설치합니다. Enterprise(Ultimate/Premium), Professional 및 Community Edition이 지원됩니다. Express Edition은 지원되지 않습니다. 
* [설치 지침](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install)에 따라 Visual Studio용 Stream Analytics 도구를 설치합니다.

## <a name="create-a-stream-analytics-project"></a>Stream Analytics 프로젝트 만들기
Visual Studio에서 **파일** > **새 프로젝트**를 차례로 선택합니다. 왼쪽의 템플릿 목록에서 **Stream Analytics**를 선택한 다음 **Azure Stream Analytics 응용 프로그램**을 선택합니다.
다른 프로젝트와 마찬가지로 페이지 아래쪽에서 프로젝트 **이름**, **위치** 및 **솔루션 이름**을 입력합니다.

![새 프로젝트 만들기](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-01.png)

**솔루션 탐색기**에서 **Toll** 프로젝트가 생성되었습니다.

![솔루션 탐색기의 Toll 프로젝트](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-02.png)

## <a name="choose-the-correct-subscription"></a>올바른 구독 선택
1. **보기** 메뉴에서 Visual Studio의 **서버 탐색기**를 선택합니다.

2. Azure 계정으로 로그인합니다. 

## <a name="define-input-sources"></a>입력 원본 정의
1. **솔루션 탐색기**에서 **입력** 노드를 확장하고 이름을 **Input.json**에서 **EntryStream.json**으로 바꿉니다. **EntryStream.json**을 두 번 클릭합니다.

2. **입력 별칭**에서 **EntryStream**을 입력합니다. 입력 별칭은 쿼리 스크립트에서 사용됩니다.

3. **원본 형식**에서 **데이터 스트림**을 선택합니다.

4. **원본**에서 **이벤트 허브**를 선택합니다.

5. **Service Bus 네임스페이스**의 드롭다운 목록에서 **TollData** 옵션을 선택합니다.

6. **이벤트 허브 이름**에서 **entry**를 선택합니다.

7. **이벤트 허브 정책 이름**에서 **RootManageSharedAccessKey**(기본값)를 선택합니다.

8. **이벤트 직렬화 형식**에서 **Json**을, **인코딩**에서 **UTF8**을 선택합니다.
   
   설정은 다음과 같습니다.
   
   ![입력 설정](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-01.png)
   
9. 페이지 아래쪽에서 **저장**을 선택하여 마법사를 완료합니다. 이제 다른 입력 원본을 추가하여 진출 스트림을 만들 수 있습니다. **입력** 노드를 마우스 오른쪽 단추로 클릭하고 **새 항목**을 선택합니다.
   
   ![새 항목](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-02.png)
   
10. 팝업 창에서 **Stream Analytics 입력**을 선택하고 **이름**을 **ExitStream.json**으로 변경합니다. **추가**를 선택합니다.
   
    ![새 항목 추가](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-03.png)
   
11. 프로젝트에서 **ExitStream.json**을 두 번 클릭하고 진입 스트림과 동일한 단계에 따라 필드를 채웁니다. **이벤트 허브 이름**에서 다음 스크린샷과 같이 **exit**를 입력해야 합니다.
   
    ![ExitStream 설정](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-04.png)
   
   이제 두 입력 스트림을 정의했습니다.
   
   ![두 입력 스트림](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-05.png)
   
   다음으로 차량 등록 데이터가 있는 Blob 파일에 대한 참조 데이터 입력을 추가합니다.
   
12. 프로젝트에서 **입력** 노드를 마우스 오른쪽 단추로 클릭한 다음 스트림 입력에 대해 동일한 프로세스를 수행합니다. **원본 형식**에서 **참조 데이터**를 선택하고, **입력 별칭**에서 **Registration**을 입력합니다.
   
    ![등록 설정](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-06.png)
   
13. **TollData**가 있는 옵션이 포함된 **저장소** 계정을 선택합니다. 컨테이너 이름은 **TollData**이고, **경로 패턴**은 **registration.json**입니다. 이 파일 이름은 대/소문자를 구분하므로 소문자여야 합니다.

14. **저장**을 선택하여 마법사를 마칩니다.

이제 모든 입력이 정의되었습니다.

## <a name="define-output"></a>출력 정의
1. **솔루션 탐색기**에서 **입력** 노드를 확장하고 **Output.json**을 두 번 클릭합니다.

2. **출력 별칭**에서 **output**을 입력합니다. **싱크**에서 **SQL Database**를 선택합니다.

3. **데이터베이스** 이름에서 **TollDataDB**를 입력합니다.

4. **사용자 이름**에서 **tolladmin**을 입력합니다. **암호**에서 **123toll!** 을 입력합니다. **테이블**에서 **TollDataRefJoin**을 입력합니다.

5. **저장**을 선택합니다.

   ![출력 설정](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-output-01.png)
 
## <a name="stream-analytics-query"></a>Stream Analytics 쿼리
이 자습서에서는 요금 데이터와 관련된 여러 가지 비즈니스 질문에 답변합니다. Stream Analytics에서 관련 답변을 제공하는 데 사용할 수 있는 쿼리를 생성했습니다. 첫 번째 Stream Analytics 작업을 시작하기 전에 간단한 시나리오 및 쿼리 구문을 살펴보겠습니다.

### <a name="introduction-to-stream-analytics-query-language"></a>Stream Analytics 쿼리 언어 소개
요금소 창구에 진입하는 차량 수를 계산해야 한다고 가정해 보겠습니다. 이 이벤트 스트림은 연속적이므로 일정 "기간"을 정의해야 합니다. 질문을 "3분 간격으로 요금소 창구에 진입하는 차량은 몇 대입니까?"로 수정해 보겠습니다. 이 측정값은 일반적으로 연속 개수(tumbling count)라고 합니다.

이 질문에 답변하는 Stream Analytics 쿼리를 살펴보겠습니다.

        SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count 
        FROM EntryStream TIMESTAMP BY EntryTime 
        GROUP BY TUMBLINGWINDOW(minute, 3), TollId 

여기서 볼 수 있듯이 Stream Analytics에서는 SQL과 같은 쿼리 언어를 사용합니다. 쿼리 시간과 관련된 측면을 지정하기 위한 몇 가지 확장을 추가합니다.

자세한 내용은 MSDN에서 쿼리에 사용된 [시간 관리](https://msdn.microsoft.com/library/azure/mt582045.aspx) 및 [창 작업](https://msdn.microsoft.com/library/azure/dn835019.aspx) 구문을 참조하세요.

이제 첫 번째 Stream Analytics 쿼리를 작성했으므로 다음 경로의 TollApp 폴더에 있는 샘플 데이터 파일을 사용하여 테스트합니다.

**..\TollApp\TollApp\Data**

이 폴더에는 다음 파일이 포함되어 있습니다.

* Entry.json
* Exit.json
* registration.json

## <a name="question-number-of-vehicles-entering-a-toll-booth"></a>질문: 요금 창구에 들어가는 차량 수
프로젝트에서 **Script.asaql**을 두 번 클릭하여 편집기에서 스크립트를 엽니다. 이전 섹션의 스크립트를 편집기에 붙여넣습니다. 쿼리 편집기는 IntelliSense, 구문 색 지정 및 오류 마커를 지원합니다.

![쿼리 편집기](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="test-stream-analytics-queries-locally"></a>로컬에서 Stream Analytics 쿼리 테스트
먼저 쿼리를 컴파일하여 구문 오류가 있는지 확인할 수 있습니다.

1. 샘플 데이터에 대해 이 쿼리의 유효성을 검사하려면 입력을 마우스 오른쪽 단추로 클릭하고 **로컬 입력 추가**를 선택하여 로컬 샘플 데이터를 사용합니다.
   
   ![로컬 입력 추가](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-01.png)
   
2. 팝업 창에서 로컬 경로의 샘플 데이터를 선택합니다. **저장**을 선택합니다.
   
   ![로컬 입력 추가](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-02.png)
   
   **local_EntryStream.json** 파일이 입력 폴더에 자동으로 추가됩니다.
   
   ![로컬 입력 폴더의 파일 목록](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-03.png)
   
3. 쿼리 편집기에서 **로컬로 실행**을 선택합니다. 또는 F5 키를 눌러도 됩니다.
   
   ![로컬로 실행](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-01.png)
   
   콘솔 출력에서 출력 경로를 찾을 수 있습니다. 아무 키를 눌러 결과 폴더를 엽니다.
   
   ![로컬 실행](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-02.png)
   
4. 로컬 폴더에서 결과를 확인합니다.
   
   ![로컬 폴더 결과](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-03.png)
   
   
### <a name="sample-input"></a>샘플 입력
입력 원본에서 로컬 파일로 입력 데이터를 샘플링할 수도 있습니다. 입력 구성 파일을 마우스 오른쪽 단추로 클릭하고 **샘플 데이터**를 선택합니다. 

![샘플 데이터](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-01.png)

지금은 이벤트 허브 또는 IoT 허브만 샘플링할 수 있습니다. 다른 입력 원본은 지원되지 않습니다. 팝업 대화 상자에서 로컬 경로를 입력하여 샘플 데이터를 저장합니다. **샘플**을 선택합니다.

![샘플 데이터 구성](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-02.png)
 
**출력** 창에서 진행률을 볼 수 있습니다. 

![샘플 데이터 출력](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-03.png)
 
### <a name="submit-a-stream-analytics-query-to-azure"></a>Stream Analytics 쿼리를 Azure에 제출
1. **쿼리 편집기**의 스크립트 편집기에서 **Azure에 제출**을 선택합니다.

   ![Azure에 제출](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-01.png)
 
2. **새 Azure Stream Analytics 작업 만들기**를 선택합니다. **작업 이름**에서 **TollApp**을 입력합니다. 드롭다운 목록에서 올바른 **구독**을 선택합니다. **제출**을 선택합니다.

   ![작업 제출](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-02.png)

 
### <a name="start-the-job"></a>작업 시작
이제 작업이 만들어졌고 작업 보기가 자동으로 열립니다. 
1. 녹색 화살표 단추를 선택하여 작업을 시작합니다.

   ![작업 시작 단추](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-01.png)
 
2. 기본 설정을 선택하고 **시작**을 선택합니다.
 
   ![작업 시작](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-02.png)

   작업 상태가 **실행 중**으로 변경되고 입력/출력 이벤트가 있음을 확인할 수 있습니다.

   ![작업 요약 및 메트릭](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-03.png)

## <a name="check-results-in-visual-studio"></a>Visual Studio에서 결과 확인
1. Visual Studio 서버 탐색기를 열고 **TollDataRefJoin** 테이블을 마우스 오른쪽 단추로 클릭합니다.

2. **테이블 데이터 표시**를 선택하면 작업의 출력을 볼 수 있습니다.
   
   ![테이블 데이터 표시](media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-check-results.jpg)
   

### <a name="view-job-metrics"></a>작업 메트릭 보기
**작업 메트릭**에서 일부 기본 작업 통계가 표시됩니다. 

![작업 메트릭](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-metrics-01.png)

 
## <a name="list-the-job-in-server-explorer"></a>서버 탐색기에 작업 나열
**서버 탐색기**에서 **Stream Analytics 작업**을 선택한 다음 **새로 고침**을 선택합니다. **Stream Analytics 작업** 아래에 해당 작업이 표시됩니다.

![작업 목록](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-list-jobs-01.png)


## <a name="open-the-job-view"></a>작업 보기 열기
작업 노드를 펼치고 **작업 보기** 노드를 두 번 클릭하여 작업 보기를 엽니다.

![작업 보기](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-view-01.png)


## <a name="export-an-existing-job-to-a-project"></a>기존 작업을 프로젝트로 내보내기
두 가지 방법으로 기존 작업을 프로젝트로 내보낼 수 있습니다.
* **서버 탐색기**의 **Stream Analytics 작업** 노드 아래에서 작업 노드를 마우스 오른쪽 단추로 클릭합니다. **새 Stream Analytics 프로젝트로 내보내기**를 선택합니다.
   
   ![새 Stream Analytics 프로젝트로 내보내기](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-01.png)
   
   **솔루션 탐색기**에서 생성된 프로젝트가 표시됩니다.
   
    ![솔루션 탐색기 작업](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-02.png)
   
* 작업 보기에서 **프로젝트 생성**을 선택합니다.
   
   ![프로젝트 생성](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-03.png)
   
## <a name="known-issues-and-limitations"></a>알려진 문제 및 제한 사항
 
* 쿼리에 지리적 공간 함수가 있으면 로컬 테스트가 작동하지 않습니다.
* 편집기에서 JavaScript UDF 추가 또는 변경을 지원하지 않습니다.
* 로컬 테스트에서 JSON 형식의 출력 저장을 지원하지 않습니다. 
* Power BI 출력 및 ADLS 출력을 지원하지 않습니다.



## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-get-started.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)


