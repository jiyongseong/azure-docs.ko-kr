---
title: Azure Cosmos DB 커넥터에 대한 Power BI 자습서 | Microsoft Docs
description: 이 Power BI 자습서를 사용하여 JSON을 가져오고, 통찰력 있는 보고서를 만들고, Azure Cosmos DB 및 Power BI 커넥터를 사용하여 데이터를 시각화할 수 있습니다.
keywords: power bi 자습서, 데이터 시각화, power bi 커넥터
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: cd1b7f70-ef99-40b7-ab1c-f5f3e97641f7
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: sngun
ms.openlocfilehash: 8a0f50ad6df1135e05cd69be78e6b7f7820f90c6
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="power-bi-tutorial-for-azure-cosmos-db-visualize-data-using-the-power-bi-connector"></a>Azure Cosmos DB에 대한 Power BI 자습서: Power BI 커넥터를 사용하여 데이터 시각화
[PowerBI.com](https://powerbi.microsoft.com/) 은 사용자 및 조직에 중요한 데이터로 대시보드와 보고서를 만들어 공유할 수 있는 온라인 서비스입니다.  Power BI 데스크톱은 다양한 데이터 원본에서 데이터를 검색하고, 데이터를 병합 및 변환하며, 강력한 보고서 및 시각화를 제작하고, 보고서를 Power BI에 게시할 수 있는 전용 보고서 제작 도구입니다.  Power BI Desktop의 최신 버전에서는 이제 Power BI용 Azure Cosmos DB 커넥터를 통해 Azure Cosmos DB 계정에 연결할 수 있습니다.   

이 Power BI 자습서에서는 Power BI Desktop의 Azure Cosmos DB 계정에 연결하고, 탐색기를 사용하여 데이터를 추출할 컬렉션으로 이동하고, Power BI Desktop 쿼리 편집기를 사용하여 JSON 데이터를 테이블 형식으로 변환하고, 보고서를 빌드하여 PowerBI.com에 게시하는 단계를 안내합니다.

이 Power BI 자습서를 완료하고 나면 다음을 알게 됩니다.  

* Power BI Desktop을 사용하여 Azure Cosmos DB의 데이터로 보고서를 빌드하는 방법
* Power BI Desktop에서 Azure Cosmos DB 계정에 연결하는 방법
* Power BI 데스크톱의 컬렉션에서 데이터를 검색하는 방법
* Power BI 데스크톱에서 중첩된 JSON 데이터를 변환하는 방법
* PowerBI.com에서 내 보고서를 게시 및 공유하는 방법

> [!NOTE]
> Azure Cosmos DB용 Power BI 커넥터는 데이터 추출 및 변환을 위해 Power BI Desktop에 연결합니다. Power BI Desktop에서 만든 보고서를 PowerBI.com에 게시할 수 있습니다. Azure Cosmos DB 데이터의 직접 추출 및 변환은 PowerBI.com에서 수행할 수 없습니다. 

> [!NOTE]
> MongoDB API를 사용하여 Azure Cosmos DB를 Power BI에 연결하려면 [Simba MongoDB ODBC 드라이버](http://www.simba.com/drivers/mongodb-odbc-jdbc/)를 사용해야 합니다.

## <a name="prerequisites"></a>필수 조건
이 Power BI 자습서의 지침을 따르기 전에 다음 리소스에 액세스할 수 있는지 확인하세요.

* [최신 버전의 Power BI Desktop](https://powerbi.microsoft.com/desktop).
* 데모 계정 또는 Azure Cosmos DB 계정의 데이터에 액세스합니다.
  * 데모 계정은 이 자습서에 표시된 화산 데이터로 채워집니다. 이 데모 계정은 SLA와 연결되지 않으며 데모용으로만 의미가 있습니다.  Microsoft는 계정 종료, 키 변경, 액세스 제한, 데이터 변경 및 삭제 등을 망라하여, 언제든 사전 고지나 이유 없이 이 데모 계정을 수정할 권리가 있습니다.
    * URL: https://analytics.documents.azure.com
    * 읽기 전용 키: MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR+YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw==
  * 또는 고유 계정을 만들려면 [Azure Portal을 사용하여 Azure Cosmos DB 데이터베이스 계정 만들기](https://azure.microsoft.com/documentation/articles/create-account/)를 참조하세요. 그런 다음 이 자습서에서 사용된 것과 유사하지만 GeoJSON 블록이 포함되지 않은 샘플 화산 데이터를 가져오려면 [NOAA 사이트](https://www.ngdc.noaa.gov/nndc/struts/form?t=102557&s=5&d=5)를 참조하고, [Azure Cosmos DB 데이터 마이그레이션 도구](import-data.md)를 사용하여 데이터를 가져옵니다.

PowerBI.com에서 보고서를 공유하려면 PowerBI.com에 계정이 있어야 합니다.  무료 Power BI 및 Power BI Pro에 대한 자세한 내용은 [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing)을 참조하세요.

## <a name="lets-get-started"></a>이제 시작하겠습니다.
이 자습서에서는 전세계 화산을 연구하는 지질학자라고 보겠습니다.  화산 데이터는 Azure Cosmos DB 계정에 저장되며 JSON 문서는 다음 샘플 문서와 같습니다.

    {
        "Volcano Name": "Rainier",
           "Country": "United States",
          "Region": "US-Washington",
          "Location": {
            "type": "Point",
            "coordinates": [
              -121.758,
              46.87
            ]
          },
          "Elevation": 4392,
          "Type": "Stratovolcano",
          "Status": "Dendrochronology",
          "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
    }

Azure Cosmos DB 계정에서 화산 데이터를 검색하고 다음 보고서와 같은 대화형 Power BI 보고서에서 데이터를 시각화하려고 합니다.

![Power BI 커넥터를 사용하여 이 Power BI 자습서를 완료하여 Power BI Desktop 화산 보고서를 사용하여 데이터를 시각화할 수 있습니다.](./media/powerbi-visualize/power_bi_connector_pbireportfinal.png)

해 볼 준비가 되셨나요? 이제 시작하겠습니다.

1. 워크스테이션에서 Power BI 데스크톱을 실행합니다.
2. Power BI 데스크톱이 시작되면 *시작* 화면이 표시됩니다.
   
    ![Power BI 데스크톱 시작 화면 - Power BI 커넥터](./media/powerbi-visualize/power_bi_connector_welcome.png)
3. **시작** 화면에서 직접 데이터를 **가져오고** , 최근 **원본을 보거나** , 다른 보고서를 직접 *열 수* 있습니다.  화면을 닫으려면 오른쪽 상단 모서리의 X를 클릭합니다. Power BI 데스크톱의 **보고서** 뷰가 표시됩니다.
   
    ![Power BI 데스크톱 보고서 보기 - Power BI 커넥터](./media/powerbi-visualize/power_bi_connector_pbireportview.png)
4. **홈** 리본 메뉴를 선택한 다음 **데이터 가져오기**를 클릭합니다.  **데이터 가져오기** 창이 나타납니다.
5. **Azure**를 클릭하고 **Azure Cosmos DB(베타)** 를 선택한 다음, **연결**을 클릭합니다. 

    ![Power BI 데스크톱 데이터 가져오기 - Power BI 커넥터](./media/powerbi-visualize/power_bi_connector_pbigetdata.png)   
6. **커넥터 미리 보기** 페이지에서 **계속**을 클릭합니다. **Azure Cosmos DB** 창이 나타납니다.
7. 아래와 같이 데이터를 검색할 Azure Cosmos DB 계정 끝점 URL을 지정한 다음, **확인**을 클릭합니다. 자신의 계정을 사용하려는 경우 Azure Portal의 **[키](manage-account.md#keys)** 블레이드에 있는 URI 상자에서 URL을 검색할 수 있습니다. 데모 계정을 사용하려면 URL로 `https://analytics.documents.azure.com`을 입력합니다. 
   
    데이터베이스 이름, 컬렉션 이름 및 SQL 문을 비워 둡니다. 이러한 필드는 선택 사항입니다.  대신 데이터의 출처를 식별하기 위해 탐색기를 사용하여 데이터베이스와 컬렉션을 선택합니다.
   
    ![Azure Cosmos DB Power BI Connector에 대한 Power BI 자습서 - 데스크톱 연결 창](./media/powerbi-visualize/power_bi_connector_pbiconnectwindow.png)
8. 처음으로 이 끝점에 연결하는 경우 계정 키를 입력하라는 메시지가 표시됩니다. 자신의 계정을 사용하는 경우 Azure Portal의 **[읽기 전용 키](manage-account.md#keys)** 블레이드에 있는 **기본 키** 상자에서 키를 검색합니다. 데모 계정의 경우 키는 `MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR+YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw==`입니다. 적절한 키를 입력하고 **연결**을 클릭합니다.
   
    보고서를 작성할 때는 읽기 전용 키를 사용하는 것이 좋습니다.  이렇게 하면 불필요하게 마스터 키가 잠재적인 보안 위험에 노출되는 것을 방지할 수 있습니다. 읽기 전용 키는 Azure 포털의 [키](manage-account.md#keys) 블레이드에서 가져오거나, 위에서 제공한 데모 계정 정보를 사용할 수 있습니다.
   
    ![Azure Cosmos DB Power BI Connector에 대한 Power BI 자습서 - 계정 키](./media/powerbi-visualize/power_bi_connector_pbidocumentdbkey.png)
    
    > [!NOTE] 
    > "지정된 데이터베이스를 찾을 수 없습니다."라는 오류가 발생할 경우 이 [Power BI 문제](https://community.powerbi.com/t5/Issues/Document-DB-Power-BI/idi-p/208200) 커뮤니티의 문제 해결 단계를 참조하세요.
    
9. 계정이 성공적으로 연결되면 **탐색기** 창이 표시됩니다.  **탐색기**는 계정의 데이터베이스 목록을 표시합니다.
10. 보고서의 데이터를 가져올 데이터베이스를 클릭하여 확장합니다. 데모 계정을 사용하는 경우 **volcanodb**를 선택합니다.   
11. 이제 검색할 데이터가 들어 있는 컬렉션을 선택합니다. 데모 계정을 사용하는 경우 **volcano1**을 선택합니다.
    
    미리 보기 창에는 **레코드** 항목의 목록이 표시됩니다.  문서는 Power BI에서 **레코드** 형식으로 나타납니다. 마찬가지로, 문서 내의 중첩된 JSON 블록도 **레코드**입니다.
    
    ![Azure Cosmos DB Power BI Connector에 대한 Power BI 자습서 - 탐색기 창](./media/powerbi-visualize/power_bi_connector_pbinavigator.png)
12. **편집**을 클릭하여 새 창에서 쿼리 편집기를 시작해 데이터를 변환합니다.

## <a name="flattening-and-transforming-json-documents"></a>JSON 문서 평면화 및 변환
1. Power BI 쿼리 편집기 창으로 전환합니다. 가운데 창에 **문서** 열이 표시됩니다.
   ![Power BI 데스크톱 쿼리 편집기](./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png)
2. **문서** 열 머리글의 오른쪽에서 확장 아이콘을 클릭합니다.  필드 목록이 있는 상황에 맞는 메뉴가 표시됩니다.  화산 이름, 국가, 지역, 위치, 상승, 유형, 상태, 마지막 분출일 등 보고서에 필요한 필드를 선택합니다. **원래 열 이름을 접두사로 사용** 확인란의 선택을 취소하고 **확인**을 클릭합니다.
   
    ![Azure Cosmos DB Power BI Connector에 대한 Power BI 자습서 - 문서 확장](./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)
3. 가운데 창에는 선택한 필드와 함께 결과의 미리 보기가 표시됩니다.
   
    ![Azure Cosmos DB Power BI Connector에 대한 Power BI 자습서 - 결과 평면화](./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png)
4. 이 예제에서 위치 속성은 문서의 GeoJSON 블록입니다.  보이는 것처럼 위치는 Power BI 데스크톱에서 **레코드** 형식으로 나타납니다.  
5. Document.Location 열 헤더의 오른쪽에 있는 확장 아이콘을 클릭합니다.  유형 및 좌표 필드가 있는 상황에 맞는 메뉴가 표시됩니다.  좌표 필드를 선택하고, **원래 열 이름을 접두사로 사용**의 선택을 취소하고, **확인**을 클릭합니다.
   
    ![Azure Cosmos DB Power BI Connector에 대한 Power BI 자습서 - 위치 레코드](./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png)
6. 이제 가운데 창에는 **목록** 유형의 좌표 열이 표시됩니다.  자습서의 시작 부분에서 본 것처럼 이 자습서의 GeoJSON 데이터는 지점 유형으로, 위도와 경도 값이 좌표 배열에 기록됩니다.
   
    좌표 [0] 요소는 경도를, 좌표 [1]은 위도를 나타냅니다.
    ![Azure Cosmos DB Power BI Connector에 대한 Power BI 자습서 - 좌표 목록](./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)
7. 좌표 배열을 평면화하기 위해 이름이 LatLong이라고 하는 **사용자 지정 열**을 만듭니다.  **열 추가** 리본을 선택하고 **사용자 지정 열**을 클릭합니다.  **사용자 지정 열** 창이 나타납니다.
8. LatLong 등, 새 열에 대한 이름을 입력합니다.
9. 다음으로 새 열에 사용자 지정 수식을 지정합니다.  이 예에서는 `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})`수식을 사용하여 쉼표로 구분하여 위도와 경도 값을 연결합니다. **확인**을 클릭합니다.
   
    DAX 함수를 포함한 데이터 분석 식(DAX)에 대한 자세한 내용은 [Power BI 데스크톱의 DAX 기초](https://support.powerbi.com/knowledgebase/articles/554619-dax-basics-in-power-bi-desktop)를 참조하세요.
   
    ![Azure Cosmos DB Power BI Connector에 대한 Power BI 자습서 - 사용자 지정 열 추가](./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png)

10. 이제 가운데 창에 값이 채워진 새 LatLong 열이 표시됩니다.
    
    ![Azure Cosmos DB Power BI Connector에 대한 Power BI 자습서 - 사용자 지정 LatLong 열](./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)
    
    새 열에 오류를 수신하는 경우 쿼리 설정에서 적용된 단계가 다음 그림과 일치하는지 확인합니다.
    
    ![적용된 단계는 원본, 탐색, 확장된 문서, 확장된 Document.Location, 추가된 사용자 지정이어야 합니다.](./media/powerbi-visualize/power-bi-applied-steps.png)
    
    단계가 다른 경우 추가 단계를 삭제하고 사용자 지정 열을 다시 추가해 보십시오. 

11. **닫고 적용** 을 클릭하여 데이터 모델을 저장합니다.
    
    ![Azure Cosmos DB Power BI Connector에 대한 Power BI 자습서 - 닫기 및 적용](./media/powerbi-visualize/power_bi_connector_pbicloseapply.png)

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>보고서 작성
Power BI Desktop 보고서 보기에서는 데이터를 시각화하는 보고서 만들기를 시작할 수 있습니다.  필드를 끌어서 **보고서** 캔버스에 놓으면 보고서를 만들 수 있습니다.

![Power BI 데스크톱 보고서 보기 - Power BI 커넥터](./media/powerbi-visualize/power_bi_connector_pbireportview2.png)

보고서 보기에는 다음과 같은 항목이 있습니다.

1. **필드** 창. 보고서에 사용할 수 있는 필드와 데이터 모델 목록이 있습니다.
2. **시각화** 창. 보고서는 단일 또는 여러 시각화 요소를 포함할 수 있습니다.  **시각화** 창에서 필요에 부합하는 시각화 형식을 선택합니다.
3. **보고서** 캔버스. 보고서의 시각적 요소를 빌드할 수 있습니다.
4. **보고서** 페이지. Power BI 데스크톱에서 여러 보고서 페이지를 추가할 수 있습니다.

다음은 간단한 대화형 지도 보기 보고서를 만드는 기본적은 단계를 보여줍니다.

1. 이 예에서는 각 화산의 위치를 나타내는 지도를 만듭니다.  **시각화** 창에서 위의 스크린샷에 강조 표시된 대로 지도 시각 유형을 클릭합니다.  **보고서** 캔버스에 지도 시각 형식이 칠해져 나타납니다.  **시각화** 창에도 지도 시각 유형과 관련한 속성 집합이 표시됩니다.
2. **필드** 창에서 LatLong 필드를 끌어서 **시각화** 창의 **위치** 속성에 놓습니다.
3. 다음으로 화산 이름 필드를 끌어서 **범례** 에 놓습니다.  
4. 이제 상승 필드를 끌어서 **크기** 속성에 놓습니다.  
5. 이제 맵에 화산의 상승 위치에 따른 크기의 버블로 각 화산의 위치를 시각적으로 나타내는 버블 집합이 표시됩니다.
6. 이제 기본 보고서를 만들었습니다.  다른 시각화 요소를 추가하여 보고서를 상세히 사용자 지정할 수 있습니다.  여기서는 화산 유형 슬라이서를 추가하여 보고서를 대화형으로 구성했습니다.  
   
    ![Azure Cosmos DB에 대한 Power BI 자습서 완료 시의 최종 Power BI Desktop 보고서 스크린샷](./media/powerbi-visualize/power_bi_connector_pbireportfinal.png)
7. 파일 메뉴에서 **저장**을 클릭하고 파일을 PowerBITutorial.pbix로 저장합니다.

## <a name="publish-and-share-your-report"></a>보고서 게시 및 공유
보고서를 공유하려면 PowerBI.com에 계정이 있어야 합니다.

1. Power BI 데스크톱에서 **홈** 리본을 클릭합니다.
2. **게시**를 클릭합니다.  PowerBI.com 계정의 사용자 이름 및 암호를 입력하라는 메시지가 표시됩니다.
3. 자격 증명이 인증되면 보고서가 선택한 대상에 게시됩니다.
4. **Power BI에서 'PowerBITutorial.pbix' 열기** 를 클릭하여 PowerBI.com에서 보고서를 보고 공유합니다.
   
    ![Power BI에 게시 성공! Power BI에서 자습서 열기](./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png)

## <a name="create-a-dashboard-in-powerbicom"></a>PowerBI.com에서 대시보드 만들기
이제 보고서가 있으니 PowerBI.com에서 공유하겠습니다.

보고서를 Power BI 데스크톱에서 PowerBI.com에 게시하는 경우 PowerBI.com 테넌트에 **보고서** 및 **데이터 집합**을 생성합니다. 예를 들어 **PowerBITutorial**이라는 보고서를 PowerBI.com에 게시한 후 PowerBI.com의 **보고서** 및 **데이터 집합** 섹션 모두에서 PowerBITutorial이 표시됩니다.

   ![PowerBI.com에서 새 보고서 및 데이터 집합의 스크린샷](./media/powerbi-visualize/powerbi-reports-datasets.png)

공유할 수 있는 대시보드를 만들려면 PowerBI.com 보고서의 **라이브 페이지 고정** 단추를 클릭합니다.

   ![PowerBI.com에서 새 보고서 및 데이터 집합의 스크린샷](./media/powerbi-visualize/power-bi-pin-live-tile.png)

그런 다음 [보고서에서 타일을 고정](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) 의 지침을 따라 새 대시보드를 만듭니다. 

또한 대시보드를 만들기 전에 보고서에 대한 임시 수정을 수행할 수 있습니다. 그러나 Power BI 데스크톱을 사용하여 수정 작업을 수행하고 보고서를 PowerBI.com 다시 게시하는 것이 좋습니다.

<!-- ## Refresh data in PowerBI.com
There are two ways to refresh data, ad hoc and scheduled.

For an ad hoc refresh, simply click on the eclipses (…) by the **Dataset**, e.g. PowerBITutorial. You should see a list of actions including **Refresh Now**. Click **Refresh Now** to refresh the data.

![Screenshot of Refresh Now in PowerBI.com](./media/powerbi-visualize/power-bi-refresh-now.png)

For a scheduled refresh, do the following.

1. Click **Schedule Refresh** in the action list. 

    ![Screenshot of the Schedule Refresh in PowerBI.com](./media/powerbi-visualize/power-bi-schedule-refresh.png)
2. In the **Settings** page, expand **Data source credentials**. 
3. Click on **Edit credentials**. 
   
    The Configure popup appears. 
4. Enter the key to connect to the Azure Cosmos DB account for that data set, then click **Sign in**. 
5. Expand **Schedule Refresh** and set up the schedule you want to refresh the dataset. 
6. Click **Apply** and you are done setting up the scheduled refresh.
-->
## <a name="next-steps"></a>다음 단계
* Power BI에 대한 자세한 내용은 [Power BI 시작](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/)을 참조하세요.
* Azure Cosmos DB에 대한 자세한 내용은 [Azure Cosmos DB 설명서 방문 페이지](https://azure.microsoft.com/documentation/services/cosmos-db/)를 참조하세요.

