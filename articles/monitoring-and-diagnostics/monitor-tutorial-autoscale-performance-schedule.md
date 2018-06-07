---
title: "성능 데이터 또는 일정에 따라 Azure 리소스 자동 크기 조정 | Microsoft Docs"
description: "메트릭 데이터 및 일정을 사용하여 앱 서비스 계획에 대한 자동 크기 조정 설정 만들기"
author: anirudhcavale
manager: orenr
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.topic: tutorial
ms.date: 12/11/2017
ms.author: ancav
ms.custom: mvc
ms.openlocfilehash: f956a9436126c2a46d80b20770d7d86309e5b3af
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="create-an-autoscale-setting-for--azure-resources-based-on-performance-data-or-a-schedule"></a>성능 데이터 또는 일정에 따라 Azure 리소스에 대한 자동 크기 조정 설정 만들기

자동 크기 조정 설정을 사용하면 미리 설정된 조건에 따라 서비스의 인스턴스를 추가/제거할 수 있습니다. 이러한 설정은 포털을 통해 만들 수 있습니다. 이 메서드는 자동 크기 조정 설정을 만들고 구성하기 위한 브라우저 기반 사용자 인터페이스를 제공합니다. 

이 자습서에서는 다음을 수행합니다. 
> [!div class="checklist"]
> * Web App 및 App Service 계획 만들기
> * 웹앱이 수신하는 요청 수에 따라 규모 확장 및 규모 감축에 대한 자동 크기 조정 규칙 구성
> * 규모 확장 작업을 트리거하고 인스턴스 수가 증가하는지 확인
> * 규모 감축 작업을 트리거하고 인스턴스 수가 감소하는지 확인
> * 리소스 정리

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="log-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-a-web-app-and-app-service-plan"></a>Web App 및 App Service 계획 만들기
1. 왼쪽 탐색 창에서 **리소스 만들기** 옵션을 클릭합니다.
2. *Web App* 항목을 검색하고 선택하여 **만들기**를 클릭합니다.
3. *MyTestScaleWebApp*과 같은 앱 이름을 선택합니다. *myResourceGroup'이라는 새 리소스 그룹을 만들고 선택한 리소스 그룹에 배치합니다.

몇 분 내에 리소스를 프로비전해야 합니다. 이 자습서의 나머지 부분에서는 웹앱 및 해당 App Service 계획을 사용합니다.

   ![포털에서 새 앱 서비스 만들기](./media/monitor-tutorial-autoscale-performance-schedule/Web-App-Create.png)

## <a name="navigate-to-autoscale-settings"></a>자동 크기 조정 설정으로 이동합니다.
1. 왼쪽 탐색 창에서 **모니터** 옵션을 선택합니다. 페이지가 로드되면 **자동 크기 조정** 탭을 선택합니다.
2. 자동 크기 조정을 지원하는 구독에 있는 리소스 목록이 여기에 나열됩니다. 자습서의 앞부분에서 만든 App Service 계획을 식별하고 클릭합니다.

    ![자동 크기 조정 설정으로 이동합니다.](./media/monitor-tutorial-autoscale-performance-schedule/monitor-blade-autoscale.png)

3. 자동 크기 조정 설정에서 **자동 크기 조정 설정** 단추를 클릭합니다.

다음 몇 가지 단계를 통해 다음 그림과 같이 자동 크기 조정 화면을 채울 수 있습니다.

   ![자동 크기 조정 설정 저장](./media/monitor-tutorial-autoscale-performance-schedule/Autoscale-Setting-Save.png)

 ## <a name="configure-default-profile"></a>기본 프로필 구성
1. 자동 크기 조정 설정의 **이름**을 입력합니다.
2. 기본 프로필에서 **크기 조정 모드**가 '특정 인스턴스 수로 크기 조정'으로 설정되었는지 확인합니다.
3. 인스턴스 수를 **1**로 설정합니다. 이 설정을 사용하면 다른 프로필이 활성화되거나 적용될 때 기본 프로필이 인스턴스 수를 1로 반환하게 됩니다.

  ![자동 크기 조정 설정으로 이동합니다.](./media/monitor-tutorial-autoscale-performance-schedule/autoscale-setting-profile.png)


## <a name="create-recurrance-profile"></a>되풀이 프로필 만들기

1. 기본 프로필 아래에서 **크기 조정 조건 추가** 링크를 클릭합니다.

2. 이 프로필의 **이름**을 '월요일부터 금요일까지 프로필'로 편집합니다.

3. **크기 조정 모드**를 '메트릭에 따라 크기 조정'으로 설정했는지 확인합니다.

4. **인스턴스 제한**을 **최소** '1'로 설정하고 **최대** '2'로 설정하며 **기본** '1'로 설정합니다. 이 설정은 이 프로필이 서비스 계획에 1개 미만 또는 2개 초과 인스턴스가 있지 않도록 합니다. 프로필에 결정을 내리는 데 충분한 데이터가 없는 경우 기본 인스턴스 수를 사용합니다(이 경우에 1).

5. **일정**으로 '특정 일 반복'을 선택합니다.

6. 월요일부터 금요일까지 9시(PST)에서 18(PST)까지 반복하도록 프로필을 설정합니다. 이 설정은 이 프로필이 활성 상태로 오전 9시~오후 6시, 월요일부터 금요일까지 적용 가능하게 됩니다. 다른 시간 동안 '기본값' 프로필은 자동 크기 조정 설정에서 사용하는 프로필입니다.

## <a name="create-a-scale-out-rule"></a>확장 규칙 만들기

1. '월요일부터 금요일까지 프로필'에서

2. **규칙 추가** 링크를 클릭합니다.

3. **메트릭 원본**을 '다른 리소스'로 설정합니다. **리소스 종류**를 'App Services'로 설정하고 **리소스**를 이 자습서의 앞부분에서 만든 Web App으로 설정합니다.

4. **시간 집계**를 '총합'으로 설정하고 **메트릭 이름**을 '요청'으로 설정하고 **시간 조직 통계**를 '합계'로 설정합니다.

5. **연산자** '초과'로 설정하고 **임계값**을 '10'으로 설정하고 **기간**을 '5'분으로 설정합니다.

6. **작업**을 '증가 수'로 선택하고 **인스턴스 수**를 '1'로 선택하고 **쿨 다운**을 '5'분으로 선택합니다.

7. **추가** 단추를 클릭합니다.

이 규칙을 통해 Web App 이 5분 이내에 10개 이상의 요청을 수신하는 경우 하나의 추가 인스턴스가 App Service 계획에 추가되어 부하를 관리하게 됩니다.

   ![확장 규칙 만들기](./media/monitor-tutorial-autoscale-performance-schedule/Scale-Out-Rule.png)

## <a name="create-a-scale-in-rule"></a>축소 규칙 만들기
항상 축소 규칙을 확장 규칙과 함께 사용하는 것이 좋습니다. 둘 다 있으면 이 리소스가 초과 프로비전되지 않도록 합니다. 오버프로비전은 현재 부하를 처리하는 데 필요한 것보다 많은 인스턴스를 실행하는 것을 의미합니다. 

1. '월요일부터 금요일까지 프로필'에서

2. **규칙 추가** 링크를 클릭합니다.

3. **메트릭 원본**을 '다른 리소스'로 설정합니다. **리소스 종류**를 'App Services'로 설정하고 **리소스**를 이 자습서의 앞부분에서 만든 Web App으로 설정합니다.

4. **시간 집계**를 '총합'으로 설정하고 **메트릭 이름**을 '요청'으로 설정하고 **시간 조직 통계**를 '평균'으로 설정합니다.

5. **연산자** '미만'으로 설정하고 **임계값**을 '5'로 설정하고 **기간**을 '5'분으로 설정합니다.

6. **작업**을 '감소 수'로 선택하고 **인스턴스 수**를 '1'로 선택하고 **쿨 다운**을 '5'분으로 선택합니다.

7. **추가** 단추를 클릭합니다.

    ![축소 규칙 만들기](./media/monitor-tutorial-autoscale-performance-schedule/Scale-In-Rule.png)

8. 자동 크기 조정 설정을 **저장**합니다.

    ![자동 크기 조정 설정 저장](./media/monitor-tutorial-autoscale-performance-schedule/Autoscale-Setting-Save.png)

## <a name="trigger-scale-out-action"></a>확장 작업 트리거
방금 만든 자동 크기 조정 설정에서 확장 조건을 트리거하려면 5분 이내에 Web App에 10개 이상의 요청이 있어야 합니다.

1. 브라우저 창을 열고 이 자습서의 앞부분에서 만든 Web App으로 이동합니다. Web App 리소스를 탐색하고 '개요' 탭에서 **찾아보기** 단추를 클릭하여 Azure Portal에서 Web App의 URL을 찾을 수 있습니다.

2. 연속으로 빠르게 페이지를 10번 이상 다시 로드합니다.

3. 왼쪽 탐색 창에서 **모니터** 옵션을 선택합니다. 페이지가 로드되면 **자동 크기 조정** 탭을 선택합니다.

4. 목록에서 이 자습서 전체에 사용되는 App Service 계획을 선택합니다.

5. 자동 크기 조정 설정에서 **실행 기록** 탭을 클릭합니다.

6. 시간에 따라 App Service 계획의 인스턴스 수를 반영하는 차트가 표시됩니다.

7. 잠시 후에 인스턴스 수가 1에서 2로 증가해야 합니다.

8. 차트에는 이 자동 크기 조정 설정에서 수행한 각 크기 조정 작업에 대한 활동 로그 항목이 표시됩니다.

## <a name="trigger-scale-in-action"></a>축소 작업 트리거
10분 동안 Web App에 5개 미만의 요청이 발생하는 경우 자동 크기 조정 설정의 축소 조건이 트리거됩니다. 

1. Web App에 전송된 요청이 없는지 확인합니다.

2. Azure Portal을 로드합니다.

3. 왼쪽 탐색 창에서 **모니터** 옵션을 선택합니다. 페이지가 로드되면 **자동 크기 조정** 탭을 선택합니다.

4. 목록에서 이 자습서 전체에 사용되는 App Service 계획을 선택합니다.

5. 자동 크기 조정 설정에서 **실행 기록** 탭을 클릭합니다.

6. 시간에 따라 App Service 계획의 인스턴스 수를 반영하는 차트가 표시됩니다.

7. 잠시 후에 인스턴스 수가 2에서 1로 감소해야 합니다. 이 프로세스는 적어도 100분이 걸립니다.  

8. 차트에는 이 자동 크기 조정 설정에서 수행한 각 크기 조정 작업에 해당하는 활동 로그 항목 집합이 표시됩니다.

    ![축소 작업 보기](./media/monitor-tutorial-autoscale-performance-schedule/Scale-In-Chart.png)

## <a name="clean-up-resources"></a>리소스 정리

1. Azure Portal의 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음 이 자습서에서 만든 Web App을 선택합니다.

2. 리소스 페이지에서 **삭제**를 클릭하고 텍스트 상자에 **예**를 입력하여 삭제를 확인하고 **삭제**를 클릭합니다.

3. 그런 다음, App Service 계획 리소스를 선택하고 **삭제**를 클릭합니다.

4. 텍스트 상자에 **예**를 입력하여 삭제를 확인하고 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행했습니다.  
> [!div class="checklist"]
> * 웹앱 및 App Service 계획 만들기
> * 웹앱이 수신하는 요청 수에 따라 규모 확장 및 규모 감축에 대한 자동 크기 조정 규칙 구성
> * 규모 확장 작업을 트리거하고 인스턴스 수가 증가하는지 확인
> * 규모 확장 작업을 트리거하고 인스턴스 수가 증가하는지 확인
> * 리소스 정리


자동 크기 조정 설정에 대한 자세한 내용을 보려면 [자동 크기 조정 개요](monitoring-overview-autoscale.md)를 계속 진행합니다.

> [!div class="nextstepaction"]
> [모니터링 데이터 보관](monitor-tutorial-archive-monitoring-data.md)
