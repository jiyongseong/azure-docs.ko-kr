---
title: 차량 상태 및 주행 습관에 대한 Power BI 대시보드 - Azure | Microsoft Docs
description: Cortana Intelligence의 기능을 사용하여 차량 상태 및 주행 습관에 대한 예측 가능한 통찰력 및 실시간 정보를 얻습니다.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: aaeb29a5-4a13-4eab-bbf1-885690d86c56
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: deguhath
ms.openlocfilehash: 2689e87dd41aa4494cf63c9ead1d1ca011b0b720
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="vehicle-telemetry-analytics-solution-template-power-bi-dashboard-setup-instructions"></a>차량 원격 분석 솔루션 템플릿 Power BI 대시보드 설정 지침
이 메뉴는 이 플레이북 장에 연결됩니다. 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

이 차량 원격 분석 솔루션은 자동차 대리점, 자동차 제조업체 및 보험 회사가 Cortana Intelligence의 기능을 사용하는 방법을 보여줍니다. 이들은 고객 경험, 연구 및 개발, 마케팅 캠페인을 개선하기 위한 차량 상태 및 주행 습관에 대한 실시간 및 예측 통찰력을 얻을 수 있습니다. 이러한 단계별 지침은 구독에 솔루션을 배포한 후 Power BI 보고서 및 대시보드를 구성하는 방법을 보여 줍니다. 

## <a name="prerequisites"></a>필수 조건
* [차량 원격 분석](https://gallery.cortanaintelligence.com/Solution/5bdb23f3abb448268b7402ab8907cc90) 솔루션을 배포합니다. 
* [Power BI Desktop을 설치](http://www.microsoft.com/download/details.aspx?id=45331)합니다.
* [Azure 구독](https://azure.microsoft.com/pricing/free-trial/)을 구매합니다. Azure 구독이 없으면 Azure 무료 구독을 시작합니다.
* Power BI 계정을 엽니다.

## <a name="cortana-intelligence-suite-components"></a>Cortana Intelligence Suite 구성 요소
차량 원격 분석 솔루션 템플릿의 일부로 다음 Cortana Intelligence 서비스가 구독에 배포됩니다.

* **Azure Event Hubs**는 수백만 개의 차량 원격 분석 이벤트를 Azure에 수집합니다.
* **Azure Stream Analytics**는 차량 상태에 대한 실시간 통찰력을 제공하고 다양한 배치 분석을 위해 해당 데이터를 장기간용 저장소에 보관합니다.
* **Azure Machine Learning**은 이상적으로 이상을 검색하고 일괄 처리를 사용하여 예측적인 통찰력을 제공합니다.
* **Azure HDInsight**는 확장 시 데이터를 변환합니다.
* **Azure Data Factory**는 일괄 처리 파이프라인의 오케스트레이션, 예약, 리소스 관리 및 모니터링을 처리합니다.

**Power BI** - 데이터 및 예측 분석 시각화를 위해 이 솔루션을 다양한 대시보드에 제공합니다. 

이 솔루션은 서로 다른 두 개의 데이터 원본을 사용합니다.

* 시뮬레이트된 차량 신호 및 진단 데이터 집합
* 차량 카탈로그

차량 텔레매틱스 시뮬레이터가 이 솔루션의 일부로 포함되어 있습니다. 이 기능은 지정된 시기에 차량 상태 및 운전 패턴에 해당하는 신호를 내보냅니다. 

차량 카탈로그는 모델에 VIN을 매핑하는 참조 데이터 집합입니다.

## <a name="power-bi-dashboard-preparation"></a>Power BI 대시보드 준비
### <a name="set-up-the-power-bi-real-time-dashboard"></a>Power BI 실시간 대시보드 설정

#### <a name="start-the-real-time-dashboard-application"></a>실시간 대시보드 응용 프로그램 시작
배포가 완료되면 수동 작업 지침을 따릅니다.

1. 실시간 대시보드 응용 프로그램 RealtimeDashboardApp.zip을 다운로드한 다음 압축을 풉니다.

2.  압축을 푼 폴더에서 앱 구성 파일 RealtimeDashboardApp.exe.config를 엽니다. Event Hubs, Azure Blob Storage 및 Azure Machine Learning 서비스 연결의 appSettings를 수동 작업 지침의 값으로 바꿉니다. 변경 내용을 저장합니다.

3. RealtimeDashboardApp.exe 응용 프로그램을 실행합니다. 로그인 창에서 유효한 Power BI 인증서를 입력합니다. 

   ![Power BI 로그인 창](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
   
4. **수락**을 선택합니다. 앱이 실행되기 시작합니다.

   ![Power BI 대시보드 권한](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)

5. Power BI 웹 사이트에 로그인하고 실시간 대시보드를 만듭니다.

Power BI 대시보드를 구성할 준비가 되었습니다.  

### <a name="configure-power-bi-reports"></a>Power BI 보고서 구성
실시간 보고서 및 대시보드를 완료하려면 약 30~45분이 걸립니다. 

1. [Power BI](http://powerbi.com) 웹 페이지로 이동하고 로그인합니다.

    ![Power BI 로그인 페이지](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-1-powerbi-signin.png)

2. 새 데이터 집합이 Power BI에 생성됩니다. **ConnectedCarsRealtime** 데이터 집합을 선택합니다.

    ![ConnectedCarsRealtime 데이터 집합](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

3. 빈 보고서를 저장하려면 Ctrl+S를 누릅니다.

    ![빈 보고서](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

4. 보고서 이름 **차량 원격 분석 실시간 - 보고서**를 입력합니다.

    ![보고서 이름](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)

## <a name="real-time-reports"></a>실시간 보고서
이 솔루션에는 다음과 같은 세 가지 실시간 보고서가 있습니다.

* 운행 중인 차량
* 유지보수가 필요한 차량
* 차량 상태 통계

세 가지 보고서를 모두 구성하거나 임의 단계 후에 중지할 수 있습니다. 그런 다음 배치 보고서를 구성하는 방법에 대한 다음 섹션으로 이동할 수 있습니다. 솔루션의 실시간 경로 전체 통찰력을 시각화하는 세 가지 보고서를 모두 만드는 것이 좋습니다.  

### <a name="vehicles-in-operation-report"></a>운행 중인 차량 보고서
1. **1페이지**를 두 번 클릭하고 **운행 중인 차량**으로 이름을 변경합니다.

    ![운행 중인 차량](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)  

2. **필드** 탭에서 **vin**을 선택합니다. **시각화** 탭에서 **카드** 시각화를 선택합니다.  

    **카드** 시각화는 다음 그림에 나와 있는 것처럼 생성됩니다.

    ![vin 선택](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

3. 새 시각화를 추가할 빈 영역을 선택합니다.  

4. **필드** 탭에서 **city** 및 **vin**을 선택합니다. **시각화** 탭에서 **맵** 시각화를 선택합니다. **vin**을 **값** 영역으로 끕니다. **city**를 **범례** 영역으로 끕니다. 

    ![카드 시각화](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)

5. **시각화** 탭에서 **형식** 시각화를 선택합니다. **제목**을 선택하고 **텍스트**를 **시/군/구별 운행 중인 차량**으로 변경합니다.

    ![시/군/구별로 운행 중인 차량](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)   

    최종 시각화는 다음 예제와 같이 표시됩니다.

    ![최종 시각화](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

6. 새 시각화를 추가할 빈 영역을 선택합니다.  

7. **필드** 탭에서 **city** 및 **vin**을 선택합니다. **시각화** 탭에서 **묶은 세로 막대형 차트** 시각화를 선택합니다. **city**를 **축** 영역으로 끕니다. **vin**을 **값** 영역으로 끕니다.

8. **vin 개수**별로 차트를 정렬합니다.

    ![vin 개수](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)  

9. 차트 **제목**을 **시/군/구별 운행 중인 차량**으로 변경합니다. 

10. **형식** 섹션을 선택한 다음 **데이터 색**을 선택합니다. **모두 표시**를 **켜기**로 변경합니다.

    ![데이터 색](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)  

11. 색 기호를 선택하여 개별 도시의 색을 변경합니다.

    ![색 변경](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)  

12. 새 시각화를 추가할 빈 영역을 선택합니다.  

13. **시각화** 탭에서 **묶은 세로 막대형 차트** 시각화를 선택합니다. **필드** 탭에서 **city**를 **축** 영역으로 끕니다. **Model**을 **범례** 영역으로 끕니다. **vin**을 **값** 영역으로 끕니다.

    ![묶은 세로 막대형 차트](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png)

    차트는 다음 이미지와 같이 표시됩니다.

    ![렌더링](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)

14. 페이지가 다음 예제와 같이 표시되도록 모든 시각화를 다시 정렬합니다.

    ![시각화 요소가 있는 대시보드](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

“운행 중인 차량” 보고서를 성공적으로 구성했습니다. 다음 실시간 보고서를 만들거나 여기서 작업을 중지하고 대시보드를 구성할 수 있습니다. 

### <a name="vehicles-requiring-maintenance-report"></a>유지보수가 필요한 차량 보고서

1. ![추가](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png)를 선택하여 새 보고서를 추가합니다. 이름을 **유지보수가 필요한 차량**으로 바꿉니다.

    ![유지보수가 필요한 차량](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)  

2. **필드** 탭에서 **vin**을 선택합니다. **시각화** 탭에서 **카드** 시각화를 선택합니다.

    ![Vin 카드 시각화](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)  

    데이터 집합에 **MaintenanceLabel** 필드가 포함됩니다. 이 필드 값은 “0” 또는 “1”일 수 있습니다. 값은 솔루션의 일부로 프로비전된 기계 학습 모델에 의해 설정됩니다. 실시간 경로와 통합됩니다. “1” 값은 차량에 유지 관리가 필요함을 나타냅니다. 

3. **페이지 수준 필터**를 추가하여 유지 관리가 필요한 차량에 대한 데이터를 표시하려면: 

   a. **MaintenanceLabel** 필드를 **페이지 수준 필터**로 끕니다.
  
      ![페이지 수준 필터](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)

    나. **페이지 수준 필터 MaintenanceLabel** 아래쪽에 있는 **기본 필터링**을 선택합니다.

      ![기본 필터링](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png) 

    다. 필터 값을 **1**로 설정합니다.

      ![필터 값](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)  

4. 새 시각화를 추가할 빈 영역을 선택합니다.  

5. **시각화** 탭에서 **묶은 세로 막대형 차트** 시각화를 선택합니다. 

    ![Vin 카드](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png)

    ![묶은 세로 막대형 차트](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

6. **필드** 탭에서 **Model**을 **축** 영역으로 끕니다. **vin**을 **값** 영역으로 끕니다. 그런 다음 시각화를 **vin 개수**별로 정렬합니다. 차트 **제목**을 **모델별 유지보수가 필요한 차량**으로 변경합니다. 

7. **시각화** 탭의 **필드** ![필드-이미지](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) 섹션에서 **vin**을 **색 채도**로 끕니다.

    ![색 채도](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)  

8. **형식** 섹션의 시각화에서 **데이터 색**을 변경합니다. 

    a. **최소** 색을 **F2C812**로 변경합니다.

    나. **최대** 색을 **FF6300**으로 변경합니다.

    ![새 데이터 색](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png)

    새 시각화 색은 다음 예제와 같이 표시됩니다.

    ![새 시각화 색](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)  

9. 새 시각화를 추가할 빈 영역을 선택합니다.  

10. **시각화** 탭에서 **묶은 세로 막대형 차트**를 선택합니다. **vin**을 **값** 영역으로 끕니다. **city**를 **축** 영역으로 끕니다. **vin 개수**별로 차트를 정렬합니다. 차트 **제목**을 **도시별 유지보수가 필요한 차량**으로 변경합니다.

    ![도시별 유지보수가 필요한 차량](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)  

11. 새 시각화를 추가할 빈 영역을 선택합니다.  

12. **시각화** 탭에서 **여러 행 카드** 시각화를 선택합니다. **Model** 및 **vin**을 **필드** 영역으로 끕니다.

    ![여러 행 카드](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)    

13. 최종 보고서가 다음 예제와 같이 표시되도록 모든 시각화를 다시 정렬합니다. 

    ![다시 정렬된 최종 보고서](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)  

“유지보수가 필요한 차량” 실시간 보고서를 성공적으로 구성했습니다. 다음 실시간 보고서를 만들거나 여기서 작업을 중지하고 대시보드를 구성할 수 있습니다. 

### <a name="vehicle-health-statistics-report"></a>차량 상태 통계 보고서

1. ![추가](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png)를 선택하여 새 보고서를 추가합니다. 이름을 **차량 상태 통계**로 바꿉니다. 

2. **시각화** 탭에서 **계기** 시각화를 선택합니다. **speed**를 **값**, **최소값** 및 **최대값** 영역으로 끕니다.

   ![차량 상태 통계](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)  

3. **값** 영역에서 **속도**의 기본 집계를 **평균**으로 변경합니다.

4. **최소값** 영역에서 **속도**의 기본 집계를 **최소**로 변경합니다.

5. **최대값** 영역에서 **속도**의 기본 집계를 **최대**로 변경합니다.

   ![속도 값](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)  

6. **계기 제목**의 이름을 **평균 속도**로 바꿉니다.

   ![계기](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)  

7. 새 시각화를 추가할 빈 영역을 선택합니다.  

    마찬가지로 **평균 엔진 오일**, **평균 연료** 및 **평균 엔진 온도**에 대한 **계기**를 추가합니다.  

8. **평균 속도** 계기에서, 이전 각 단계에서 수행한 것처럼 각 계기에서 필드의 기본 집계를 변경합니다.

    ![추가 계기](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

9. 새 시각화를 추가할 빈 영역을 선택합니다.

10. **시각화** 탭에서 **꺽은선형 및 묶은 세로 막대형 차트** 시각화를 선택합니다. **city**를 **공유 축**으로 끕니다. **tirepressure**, **engineoil** 및 **speed**를 **열 값** 영역으로 끕니다. 해당 집계 유형을 **평균**으로 변경합니다. 

11. **engineTemperature**를 **선 값** 영역으로 끕니다. 해당 집계 유형을 **평균**으로 변경합니다. 

    ![열 및 줄 값](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

12. 차트 **제목**을 **평균 속도, 타이어 압력, 엔진 오일 및 엔진 온도**로 변경합니다.  

    ![꺽은선형 및 묶은 세로 막대형 차트](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

13. 새 시각화를 추가할 빈 영역을 선택합니다.

14. **시각화** 탭에서 **트리맵** 시각화를 선택합니다. **Model**을 **그룹** 영역으로 끕니다. **MaintenanceProbability**를 **값** 영역으로 끕니다.

15. 차트 **제목**을 **유지보수가 필요한 차량 모델**로 변경합니다.

    ![트리맵 제목](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

16. 새 시각화를 추가할 빈 영역을 선택합니다.

17. **시각화** 탭에서 **100% 누적 가로 막대형 차트** 시각화를 선택합니다. **city**를 **축** 영역으로 끕니다. **MaintenanceProbability** 및 **RecallProbability**를 **값** 영역으로 끕니다.

    ![축 및 값 영역](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

18. **형식** 섹션에서 **데이터 색**을 선택합니다. **MaintenanceProbability** 색을 **F2C80F** 값으로 설정합니다.

19. 차트 **제목**을 **시/군/구별 차량 유지보수 및 리콜 확률**로 변경합니다.

    ![100% 누적 가로 막대형 차트 제목](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

20. 새 시각화를 추가할 빈 영역을 선택합니다.

21. **시각화** 탭에서 **영역 차트** 시각화를 선택합니다. **Model**을 **축** 영역으로 끕니다. **engineOil**, **tirepressure**, **speed** 및 **MaintenanceProbability**를 **값** 영역으로 끕니다. 해당 집계 유형을 **평균**으로 변경합니다. 

    ![집계 유형](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

22. 차트 **제목**을 **모델별 엔진 오일, 타이어 압력, 속도 및 유지보수 확률 평균**으로 변경합니다.

    ![영역 차트 제목](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

23. 새 시각화를 추가할 빈 영역을 선택합니다.

24. **시각화** 탭에서 **분산형 차트** 시각화를 선택합니다. **Model**을 **세부 정보** 및 **범례** 영역으로 끕니다. **fuel**을 **X축** 영역으로 끕니다. 해당 집계를 **평균**으로 변경합니다. **engineTemperature**를 **Y축** 영역으로 끕니다. 해당 집계를 **평균**으로 변경합니다. **vin**을 **크기** 영역으로 끕니다.

    ![세부사항, 범례, 축 및 크기 영역](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

25. 차트 **제목**을 **모델별 연료 평균, 엔진 온도 평균 및 vin 개수**로 변경합니다.

    ![분산형 차트 제목](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

    최종 보고서는 다음 예제와 같이 표시됩니다.

    ![최종 보고서](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### <a name="pin-visualizations-from-the-reports-to-the-real-time-dashboard"></a>보고서의 시각화를 실시간 대시보드에 고정
1. **대시보드** 옆의 더하기 기호를 선택하여 빈 대시보드를 만듭니다. 이름을 **차량 원격 분석 대시보드**로 입력합니다.

    ![대시보드 더하기 기호](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.5.png)

2. 이전 보고서의 시각화를 대시보드에 고정합니다. 

    ![대시보드 고정 기호](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.6.png)

    세 가지 보고서가 모두 대시보드에 고정되면 다음 예제와 같이 표시됩니다. 일부 보고서를 작성하지 않은 경우 대시보드가 다르게 보일 수 있습니다. 

    ![보고서가 있는 대시보드](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)

실시간 대시보드를 성공적으로 만들었습니다. CarEventGenerator.exe 및 RealtimeDashboardApp.exe 실행을 계속하면서 라이브 업데이트가 대시보드에 표시됩니다. 다음 단계를 완료하려면 약 10~15분이 걸립니다.

## <a name="set-up-the-power-bi-batch-processing-dashboard"></a>Power BI 일괄 처리 대시보드 설정
> [!NOTE]
> 종단 간 일괄 처리 파이프라인의 경우 실행을 완료하고 1년 분량의 생성된 데이터를 처리하는 데는 성공적인 배포 완료 시부터 약 두 시간이 걸립니다. 다음 단계를 진행하기 전에 처리가 완료될 때까지 기다립니다.
> 
> 

### <a name="download-the-power-bi-designer-file"></a>Power BI Designer 파일 다운로드

1. 미리 구성된 Power BI Designer 파일이 배포 수동 작업 지침의 일부로 포함되어 있습니다. “2. Power BI 일괄 처리 대시보드를 설정합니다.”를 찾아보세요.

2. 여기서 **ConnectedCarsPbiReport.pbix**라는 일괄 처리 대시보드용 Power BI 템플릿을 다운로드합니다.

3. 로컬로 저장합니다.

### <a name="configure-power-bi-reports"></a>Power BI 보고서 구성

1. Power BI Desktop을 사용하여 디자이너 파일 **ConnectedCarsPbiReport.pbix**를 엽니다. 아직 없는 경우 [Power BI Desktop 설치](http://www.microsoft.com/download/details.aspx?id=45331) 웹 사이트에서 Power BI Desktop을 설치합니다.

2. **쿼리 편집**을 선택합니다.

    ![쿼리 편집](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

3. **원본**을 두 번 클릭합니다.

    ![원본](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

4. 배포의 일부로 프로비전된 Azure SQL Server를 통해 서버 연결 문자열을 업데이트합니다. Azure SQL Database 아래에서 수동 작업 지침을 확인하세요.

    * 서버: somethingsrv.database.windows.net
    * 데이터베이스: connectedcar
    * 사용자 이름: username
    * 암호: Azure Portal에서 SQL Server 암호를 관리할 수 있습니다.

5. **데이터베이스** 를 **connectedcar**로 둡니다.

    ![데이터베이스](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

6. **확인**을 선택합니다.

7. **Windows 인증서** 탭은 기본적으로 선택됩니다. 오른쪽에서 **데이터베이스** 탭을 선택하여 **데이터베이스 자격 증명**으로 변경합니다.

8. 배포 설치 중 지정된 Azure SQL Database의 **사용자 이름** 및 **암호**를 입력합니다.

    ![데이터베이스 자격 증명](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

9. **연결**을 선택합니다.

10. 오른쪽 창에 표시된 세 개의 나머지 쿼리 각각에 대해 이전 단계를 반복합니다. 그런 다음 데이터 원본 연결 세부 정보를 업데이트합니다.

11. **닫기 및 로드**를 선택합니다. Power BI Desktop 파일 데이터 집합은 SQL Database 테이블에 연결됩니다.

12. **닫기**를 선택하여 Power BI Desktop 파일을 닫습니다.

    ![닫습니다](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

13. **저장**을 선택하여 변경 내용을 저장합니다. 

이제 솔루션의 일괄 처리 경로에 해당하는 모든 보고서를 구성했습니다. 

## <a name="upload-to-powerbicom"></a>powerbi.com에 업로드
1. [Power BI 웹 포털](http://powerbi.com)로 이동하여 로그인합니다.

2. **데이터 가져오기**를 선택합니다.

3. Power BI Desktop 파일을 업로드합니다. **데이터 가져오기** > **파일 가져오기** > **로컬 파일**을 선택합니다.

4. **ConnectedCarsPbiReport.pbix**로 이동합니다.

5. 파일을 업로드한 후 Power BI 작업 영역으로 돌아갑니다. 데이터 집합, 보고서 및 빈 대시보드가 생성됩니다.  

6. Power BI에서 새 대시보드인 **차량 원격 분석 대시보드**에 차트를 고정합니다. 이전에 생성된 빈 대시보드를 선택한 후 **보고서** 섹션으로 이동합니다. 새로 업로드된 보고서를 선택합니다.  

    ![새 Power BI 대시보드](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png) 

    보고서에는 다음 6개 페이지가 있습니다.

    1페이지: 차량 밀도  
    2페이지: 실시간 차량 상태  
    3페이지: 적극적으로 운전한 차량   
    4페이지: 리콜된 차량  
    5페이지: 연료 효율이 높게 운전한 차량  
    6페이지: Contoso Motors 로고  

    ![6개 페이지가 있는 Power BI 보고서](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)

7. **3페이지에서** 다음 콘텐츠를 고정합니다.  

    a. **vin 개수**  

   ![3페이지 vin 개수](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png)

    나. **모델별 적극적으로 운전한 차량 - 폭포 차트** 

   ![3페이지 차트 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

8. **5페이지에서** 다음 콘텐츠를 고정합니다. 

    a. **vin 개수**

   ![5페이지 차트 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png)

    나. **모델별 연료 효율이 좋은 차량: 묶은 세로 막대형 차트**

   ![5페이지 차트 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

9. **4페이지에서** 다음 콘텐츠를 고정합니다.  

    a. **vin 개수** 

   ![4페이지 차트 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 

    나. **시/군/구, 모델별 리콜된 차량: 트리맵**

   ![4페이지 차트 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)  

10. **6페이지에서** 다음 콘텐츠를 고정합니다.  

    * **Contoso Motors 로고**

    ![Contoso 모터 로고 ](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

### <a name="organize-the-dashboard"></a>대시보드 구성  

1. 대시보드로 이동합니다.

2. 각 차트를 마우스로 가리킵니다. 다음 완료된 대시보드 예제에 제공된 이름을 기반으로 각 차트의 이름을 바꿉니다.

   ![대시보드 구성](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png) 
   
3. 차트를 이리저리 움직여 다음 대시보드 예제처럼 표시합니다.

    ![다시 정렬된 대시보드](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)

4. 이 문서에 설명된 모든 보고서를 만든 후 최종 완료된 대시보드는 다음 예제와 같이 표시됩니다. 

   ![최종 대시보드](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard3.png)

차량 상태 및 운전 습관을 실시간으로 예측 가능하며 배치 통찰력을 얻을 수 있는 보고서 및 대시보드를 성공적으로 만들었습니다.  
