---
title: '자습서: Azure Databricks를 사용하여 ETL 작업 수행 | Microsoft Docs'
description: Data Lake Store에서 Azure Databricks로 데이터를 추출하고 데이터를 전송한 다음, Azure SQL Data Warehouse에 데이터를 로드하는 방법을 알아봅니다.
services: azure-databricks
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.custom: mvc
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 03/23/2018
ms.author: nitinme
ms.openlocfilehash: c3aa87f2c74175d1b61a8db6a9c7a0318a408658
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="tutorial-extract-transform-and-load-data-using-azure-databricks"></a>자습서: Azure Databricks를 사용하여 데이터 추출, 변환 및 로드

이 자습서에서는 Azure Databricks를 사용하여 ETL(추출, 변환 및 데이터 로드) 작업을 수행합니다. Azure Data Lake Store에서 Azure Databricks로 데이터를 추출하고, Azure Databricks에서 데이터를 변환한 다음, Azure SQL Data Warehouse로 데이터를 로드합니다. 

이 자습서의 단계에서는 Azure Databricks용 SQL Data Warehouse 커넥터를 사용하여 Azure Databricks로 데이터를 전송합니다. 그러면 이 커넥터는 Azure Blob Storage를 Azure Databricks 클러스터와 Azure SQL Data Warehouse 간에 전송되는 데이터의 임시 저장소로 사용합니다.

다음 그림에서는 응용 프로그램 흐름을 보여줍니다.

![Azure Databricks를 Data Lake Store 및 SQL Data Warehouse와 함께 사용](./media/databricks-extract-load-sql-data-warehouse/databricks-extract-transform-load-sql-datawarehouse.png "Azure Databricks를 Data Lake Store 및 SQL Data Warehouse와 함께 사용")

이 자습서에서 다루는 작업은 다음과 같습니다. 

> [!div class="checklist"]
> * Azure Databricks 작업 영역 만들기
> * Azure Databricks에 Spark 클러스터 만들기
> * Azure 데이터 레이크 저장소 계정 만들기
> * Azure Data Lake Store에 데이터 업로드
> * Azure Databricks에 노트북 만들기
> * Data Lake Store에서 데이터 추출
> * Azure Databricks에서 데이터 변환
> * Azure SQL Data Warehouse에 데이터 로드

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 시작하기 전에 다음 요구 사항이 충족되는지 확인합니다.
- Azure SQL Data Warehouse를 만들고, 서버 수준 방화벽 규칙을 만들고, 서버 관리자로 서버에 연결합니다. [빠른 시작: Azure SQL Data Warehouse 만들기](../sql-data-warehouse/create-data-warehouse-portal.md)의 지침을 따르세요.
- Azure SQL Data Warehouse에 대한 데이터베이스 마스터 키를 만듭니다. [데이터베이스 마스터 키 만들기](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key)의 지침을 따르세요.
- Azure Blob Storage 계정을 만들고, 그 안에 컨테이너를 만듭니다. 또한 저장소 계정에 액세스하는 데 사용되는 액세스 키를 검색합니다. [빠른 시작: Azure Blob Storage 계정 만들기](../storage/blobs/storage-quickstart-blobs-portal.md)의 지침을 따르세요.

## <a name="log-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks 작업 영역 만들기

이 섹션에서는 Azure Portal을 사용하여 Azure Databricks 작업 영역을 만듭니다. 

1. Azure Portal에서 **리소스 만들기** > **데이터 + 분석** > **Azure Databricks**를 차례로 선택합니다. 

    ![Azure Portal의 Databricks](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-on-portal.png "Azure Portal의 Databricks")

3. **Azure Databricks 서비스** 아래에서 Databricks 작업 영역을 만들기 위한 값을 제공합니다.

    ![Azure Databricks 작업 영역 만들기](./media/databricks-extract-load-sql-data-warehouse/create-databricks-workspace.png "Azure Databricks 작업 영역 만들기")

    다음 값을 제공합니다. 
     
    |자산  |설명  |
    |---------|---------|
    |**작업 영역 이름**     | Databricks 작업 영역의 이름을 제공합니다.        |
    |**구독**     | 드롭다운에서 Azure 구독을 선택합니다.        |
    |**리소스 그룹**     | 새 리소스 그룹을 만들지, 아니면 기존 그룹을 사용할지 여부를 지정합니다. 리소스 그룹은 Azure 솔루션에 관련된 리소스를 보유하는 컨테이너입니다. 자세한 내용은 [Azure Resource Manager 개요](../azure-resource-manager/resource-group-overview.md)를 참조하세요. |
    |**위치**:     | **미국 동부 2**를 선택합니다. 사용 가능한 다른 영역은 [지역별 사용 가능한 Azure 서비스](https://azure.microsoft.com/regions/services/)를 참조하세요.        |
    |**가격 책정 계층**     |  **표준** 또는 **프리미엄** 중에서 선택합니다. 이러한 계층에 대한 자세한 내용은 [Databricks 가격 페이지](https://azure.microsoft.com/pricing/details/databricks/)를 참조하세요.       |

    **대시보드에 고정**을 선택한 다음, **만들기**를 선택합니다.

4. 계정 생성에는 몇 분 정도가 소요됩니다. 계정을 만드는 동안 포털의 오른쪽에 **Azure Databricks에 대한 배포 제출**이 표시됩니다. 타일을 보려면 대시보드에서 오른쪽으로 스크롤해야 할 수도 있습니다. 화면 위쪽에 진행률 표시줄이 표시되기도 합니다. 두 영역에서 진행 상태를 볼 수 있습니다.

    ![Databricks 배포 타일](./media/databricks-extract-load-sql-data-warehouse/databricks-deployment-tile.png "Databricks 배포 타일")

## <a name="create-a-spark-cluster-in-databricks"></a>Databricks에서 Spark 클러스터 만들기

1. Azure Portal에서 사용자가 만든 Databricks 작업 영역으로 이동한 다음, **작업 영역 시작**을 선택합니다.

2. Azure Databricks 포털로 리디렉션됩니다. 포털에서 **클러스터**를 선택합니다.

    ![Azure의 Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-on-azure.png "Azure의 Databricks")

3. **새 클러스터** 페이지에서 값을 제공하여 클러스터를 만듭니다.

    ![Azure에서 Databricks Spark 클러스터 만들기](./media/databricks-extract-load-sql-data-warehouse/create-databricks-spark-cluster.png "Azure에서 Databricks Spark 클러스터 만들기")

    다음 항목 이외의 다른 모든 기본값을 허용합니다.

    * 클러스터의 이름을 입력합니다.
    * 이 문서에서는 **4.0** 런타임을 사용하여 클러스터를 만듭니다. 
    * **Terminate after ____ minutes of inactivity**(비활성 ____분 후 종료) 확인란을 선택했는지 확인합니다. 클러스터를 사용하지 않는 경우 클러스터를 종료하는 기간(분)을 제공합니다.
    
    **클러스터 만들기**를 선택합니다. 클러스터가 실행되면 노트북을 클러스터에 첨부하고 Spark 작업을 실행할 수 있습니다.

## <a name="create-an-azure-data-lake-store-account"></a>Azure 데이터 레이크 저장소 계정 만들기

이 섹션에서는 Azure Data Lake Store 계정을 만들고 Azure Active Directory 서비스 사용자를 연결합니다. 이 자습서의 뒷부분에서는 Azure Databricks에서 이 서비스 사용자를 사용하여 Azure Data Lake Store에 액세스합니다. 

1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기** > **저장소** > **Data Lake Store**를 선택합니다.
3. **새 Data Lake Store** 블레이드에서 아래 스크린샷에 표시된 대로 값을 제공합니다.
   
    ![새 Azure Data Lake Store 계정 만들기](./media/databricks-extract-load-sql-data-warehouse/create-new-datalake-store.png "새 Azure Data Lake 계정 만들기")

    다음 값을 제공합니다. 
     
    |자산  |설명  |
    |---------|---------|
    |**Name**     | Data Lake Store 계정의 고유한 이름을 입력합니다.        |
    |**구독**     | 드롭다운에서 Azure 구독을 선택합니다.        |
    |**리소스 그룹**     | 이 자습서에서는 Azure Databricks 작업 영역을 만들 때 사용한 것과 동일한 리소스 그룹을 선택합니다.  |
    |**위치**:     | **미국 동부 2**를 선택합니다.  |
    |**가격 책정 패키지**     |  **종량제**를 선택합니다. |
    | **암호화 설정** | 기본 설정을 유지합니다. |

    **대시보드에 고정**을 선택한 다음, **만들기**를 선택합니다.

이제 Azure Active Directory 서비스 사용자를 만들고 앞에서 만든 Data Lake Store 계정을 연결합니다.

### <a name="create-an-azure-active-directory-service-principal"></a>Azure Active Directory 서비스 사용자 만들기
   
1. [Azure Portal](https://portal.azure.com)에서 **모든 서비스**를 선택한 다음, **Azure Active Directory**를 검색합니다.

2. **앱 등록**을 선택합니다.

   ![앱 등록 선택](./media/databricks-extract-load-sql-data-warehouse/select-app-registrations.png)

3. **새 응용 프로그램 등록**을 선택합니다.

   ![앱 추가](./media/databricks-extract-load-sql-data-warehouse/select-add-app.png)

4. 응용 프로그램에 대한 이름 및 URL을 제공합니다. 만들려는 응용 프로그램 유형으로 **웹앱/API**를 선택합니다. 로그온 URL을 입력하고 **만들기**를 선택합니다.

   ![응용 프로그램 이름 지정](./media/databricks-extract-load-sql-data-warehouse/create-app.png)

Azure Databricks에서 Data Lake Store 계정에 액세스하려면 앞에서 만든 Azure Active Directory 서비스 사용자에 대한 다음 값이 있어야 합니다.
- 응용 프로그램 UI
- 인증 키
- 테넌트 ID

다음 섹션에서는 앞에서 만든 Azure Active Directory 서비스 사용자에 대한 다음 값을 검색합니다.

### <a name="get-application-id-and-authentication-key-for-the-service-principal"></a>서비스 사용자의 응용 프로그램 ID 및 인증 키 가져오기

프로그래밍 방식으로 로그인하는 경우 응용 프로그램에 대한 ID 및 인증 키가 필요합니다. 이러한 값을 가져오려면 다음 단계를 사용합니다.

1. Azure Active Directory의 **앱 등록**에서 응용 프로그램을 선택합니다.

   ![응용 프로그램 선택](./media/databricks-extract-load-sql-data-warehouse/select-app.png)

2. **응용 프로그램 ID**를 복사하고 응용 프로그램 코드에 저장합니다. 일부 [응용 프로그램 예제](#log-in-as-the-application)에서는 이 값을 클라이언트 ID라고 합니다.

   ![클라이언트 ID](./media/databricks-extract-load-sql-data-warehouse/copy-app-id.png)

3. 인증 키를 생성하려면 **설정**을 선택합니다.

   ![설정 선택](./media/databricks-extract-load-sql-data-warehouse/select-settings.png)

4. 인증 키를 생성하려면 **키**를 선택합니다.

   ![키 선택](./media/databricks-extract-load-sql-data-warehouse/select-keys.png)

5. 키에 대한 설명 및 키의 기간을 제공합니다. 완료되면 **저장**을 선택합니다.

   ![키 저장](./media/databricks-extract-load-sql-data-warehouse/save-key.png)

   키를 저장하면 키 값이 표시됩니다. 나중에 키를 검색할 수 없으므로 이 값을 복사해둡니다. 응용 프로그램으로 로그인하려면 응용 프로그램 ID와 함께 키 값을 제공합니다. 응용 프로그램에서 검색할 수 있는 위치에 키 값을 저장합니다.

   ![공유 키](./media/databricks-extract-load-sql-data-warehouse/copy-key.png)

### <a name="get-tenant-id"></a>테넌트 ID 가져오기

프로그래밍 방식으로 로그인하는 경우 인증 요청과 함께 테넌트 ID를 전달해야 합니다.

1. **Azure Active Directory**를 선택합니다.

   ![Azure Active Directory 선택](./media/databricks-extract-load-sql-data-warehouse/select-active-directory.png)

1. 테넌트 ID를 가져오려면 Azure AD 테넌트에 대한 **속성**을 선택합니다.

   ![Azure AD 속성 선택](./media/databricks-extract-load-sql-data-warehouse/select-ad-properties.png)

1. **디렉터리 ID**를 복사합니다. 이 값은 테넌트 ID입니다.

   ![테넌트 ID](./media/databricks-extract-load-sql-data-warehouse/copy-directory-id.png) 

### <a name="associate-service-principal-with-azure-data-lake-store"></a>서비스 사용자를 Azure Data Lake Store에 연결

이 섹션에서는 Azure Data Lake Store 계정을 앞에서 만든 Azure Active Directory 서비스 사용자와 연결합니다. 이렇게 하면 Azure Databricks에서 Data Lake Store 계정에 액세스할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서, 앞에서 만든 Data Lake Store 계정을 선택합니다.

2. 왼쪽 창에서 **액세스 제어** > **추가**를 선택합니다.

    ![Data Lake Store 액세스 추가](./media/databricks-extract-load-sql-data-warehouse/add-adls-access.png "Data Lake Store 액세스 추가")

3. **권한 추가**에서 서비스 사용자에게 할당할 역할을 선택합니다. 이 자습서에서는 **소유자**를 선택합니다. **다음에 대한 액세스 할당**에서 **Azure AD, 사용자, 그룹 또는 응용 프로그램**을 선택합니다. **선택**에는 앞에서 만든 서비스 사용자의 이름을 입력하여 선택할 서비스 사용자 수를 줄입니다.

    ![서비스 사용자 선택](./media/databricks-extract-load-sql-data-warehouse/select-service-principal.png "서비스 사용자 선택")

    앞에서 만든 서비스 사용자를 선택한 다음, **저장**을 선택합니다. 이제 서비스 사용자가 Azure Data Lake Store 계정과 연결되었습니다.

## <a name="upload-data-to-data-lake-store"></a>데이터 레이크 저장소에 데이터 업로드

이 섹션에서는 Data Lake Store에 샘플 데이터 파일을 업로드합니다. 이 파일은 Azure Databricks의 뒷부분에서 변환을 수행하는 데 사용됩니다. 이 자습서에서 사용하는 샘플 데이터(**small_radio_json.json**)는 이 [Github 리포지토리](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json)에서 받을 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서, 앞에서 만든 Data Lake Store 계정을 선택합니다.

2. **개요** 탭에서 **데이터 탐색기**를 클릭합니다.

    ![데이터 탐색기 열기](./media/databricks-extract-load-sql-data-warehouse/open-data-explorer.png "데이터 탐색기 열기")

3. 데이터 탐색기에서 **업로드**를 클릭합니다.

    ![업로드 옵션](./media/databricks-extract-load-sql-data-warehouse/upload-to-data-lake-store.png "업로드 옵션")

4. **파일 업로드**에서 샘플 데이터 파일의 위치로 이동한 다음, **선택한 파일 추가**를 선택합니다.

    ![업로드 옵션](./media/databricks-extract-load-sql-data-warehouse/upload-data.png "업로드 옵션")

5. 이 자습서에서는 Data Lake Store의 루트에 데이터 파일을 업로드했습니다. 이제 `adl://<YOUR_DATA_LAKE_STORE_ACCOUNT_NAME>.azuredatalakestore.net/small_radio_json.json`에서 이 파일을 사용할 수 있습니다.

## <a name="extract-data-from-data-lake-store"></a>Data Lake Store에서 데이터 추출

이 섹션에서는 Azure Databricks 작업 영역에서 노트북을 만든 후 Data Lake Store에서 Azure Databricks로 데이터를 추출하는 코드 조각을 실행합니다.

1. [Azure Portal](https://portal.azure.com)에서, 앞에서 만든 Databricks 작업 영역으로 이동한 후 **작업 영역 시작**을 선택합니다.

2. 왼쪽 창에서 **작업 영역**을 선택합니다. **작업 영역** 드롭다운에서 **만들기** > **Notebook**을 차례로 선택합니다.

    ![Databricks에서 노트북 만들기](./media/databricks-extract-load-sql-data-warehouse/databricks-create-notebook.png "Databricks에서 노트북 만들기")

2. **노트북 만들기** 대화 상자에서 노트북 이름을 입력합니다. 언어로 **Scala**를 선택한 다음, 앞에서 만든 Spark 클러스터를 선택합니다.

    ![Databricks에서 노트북 만들기](./media/databricks-extract-load-sql-data-warehouse/databricks-notebook-details.png "Databricks에서 노트북 만들기")

    **만들기**를 선택합니다.

3. 빈 코드 셀에 다음 코드 조각을 추가하고 자리 표시자 값을 앞에서 Azure Active Directory 서비스 사용자에 대해 저장한 값으로 바꿉니다.

        spark.conf.set("dfs.adls.oauth2.access.token.provider.type", "ClientCredential")
        spark.conf.set("dfs.adls.oauth2.client.id", "<APPLICATION-ID>")
        spark.conf.set("dfs.adls.oauth2.credential", "<AUTHENTICATION-KEY>")
        spark.conf.set("dfs.adls.oauth2.refresh.url", "https://login.microsoftonline.com/<TENANT-ID>/oauth2/token")

    **Shift+Enter**를 눌러 코드 셀을 실행합니다.

4. 이제 Data Lake Store의 샘플 json 파일을 Azure Databricks에서 데이터 프레임으로 로드할 수 있습니다. 다음 코드 조각을 새 코드 셀에 붙여넣고, 자리 표시자 값을 바꾼 후 **SHIFT + ENTER**를 누릅니다.

        val df = spark.read.json("adl://<DATA LAKE STORE NAME>.azuredatalakestore.net/small_radio_json.json")

5. 데이터 프레임의 콘텐츠를 보려면 다음 코드 조각을 실행합니다.

        df.show()

    다음 코드 조각과 유사한 결과가 표시됩니다.

        +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
        |               artist|     auth|firstName|gender|itemInSession|  lastName|   length|  level|            location|method|    page| registration|sessionId|                song|status|           ts|userId|
        +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
        | El Arrebato         |Logged In| Annalyse|     F|            2|Montgomery|234.57914| free  |  Killeen-Temple, TX|   PUT|NextSong|1384448062332|     1879|Quiero Quererte Q...|   200|1409318650332|   309|
        | Creedence Clearwa...|Logged In|   Dylann|     M|            9|    Thomas|340.87138| paid  |       Anchorage, AK|   PUT|NextSong|1400723739332|       10|        Born To Move|   200|1409318653332|    11|
        | Gorillaz            |Logged In|     Liam|     M|           11|     Watts|246.17751| paid  |New York-Newark-J...|   PUT|NextSong|1406279422332|     2047|                DARE|   200|1409318685332|   201|
        ...
        ...

Azure Data Lake Store에서 Azure Databricks로 데이터를 추출했습니다.

## <a name="transform-data-in-azure-databricks"></a>Azure Databricks에서 데이터 변환

원시 샘플 데이터 **small_radio_json.json**은 라디오 방송국의 대상을 캡처하며, 다양한 열을 갖고 있습니다. 이 섹션에서는 데이터 집합의 특정 열만 검색하도록 데이터를 변환합니다. 

1. 먼저 앞에서 만든 데이터 프레임에서 *이름*, *성*, *성별*, *위치* 및 *수준* 열만 검색합니다.

        val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")

    다음 코드 조각과 같은 출력이 표시됩니다.

        +---------+----------+------+--------------------+-----+
        |firstname|  lastname|gender|            location|level|
        +---------+----------+------+--------------------+-----+
        | Annalyse|Montgomery|     F|  Killeen-Temple, TX| free|
        |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
        |     Liam|     Watts|     M|New York-Newark-J...| paid|
        |     Tess|  Townsend|     F|Nashville-Davidso...| free|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
        |     Alan|     Morse|     M|Chicago-Napervill...| paid|
        |Gabriella|   Shelton|     F|San Jose-Sunnyval...| free|
        |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
        |     Tess|  Townsend|     F|Nashville-Davidso...| free|
        |     Alan|     Morse|     M|Chicago-Napervill...| paid|
        |     Liam|     Watts|     M|New York-Newark-J...| paid|
        |     Liam|     Watts|     M|New York-Newark-J...| paid|
        |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
        |     Alan|     Morse|     M|Chicago-Napervill...| paid|
        |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
        |     Alan|     Morse|     M|Chicago-Napervill...| paid|
        |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
        +---------+----------+------+--------------------+-----+

2.  열 **수준**을 **subscription_type**으로 지정하도록 이 데이터를 추가로 변환할 수 있습니다.

        val renamedColumnsDF = specificColumnsDf.withColumnRenamed("level", "subscription_type")
        renamedColumnsDF.show()

    다음 코드 조각과 같은 출력이 표시됩니다.

        +---------+----------+------+--------------------+-----------------+
        |firstname|  lastname|gender|            location|subscription_type|
        +---------+----------+------+--------------------+-----------------+
        | Annalyse|Montgomery|     F|  Killeen-Temple, TX|             free|
        |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
        |     Liam|     Watts|     M|New York-Newark-J...|             paid|
        |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
        |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
        |Gabriella|   Shelton|     F|San Jose-Sunnyval...|             free|
        |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
        |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
        |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
        |     Liam|     Watts|     M|New York-Newark-J...|             paid|
        |     Liam|     Watts|     M|New York-Newark-J...|             paid|
        |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
        |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
        |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
        |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
        |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
        +---------+----------+------+--------------------+-----------------+

## <a name="load-data-into-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse에 데이터 로드

이 섹션에서는 변환된 데이터를 Azure SQL Data Warehouse로 업로드합니다. Azure Databricks용 Azure SQL Data Warehouse 커넥터를 사용하여 데이터 프레임을 SQL 데이터 웨어하우스의 테이블로 직접 업로드할 수 있습니다.

앞서 언급했듯이, SQL 데이터 웨어하우스 커넥터는 Azure Blob Storage를 임시 저장소로 사용하여 Azure Databricks와 Azure SQL Data Warehouse 간의 데이터를 업로드합니다. 따라서 저장소 계정에 연결하는 구성을 먼저 제공해야 합니다. 이 문서의 필수 구성 요소로 이미 계정을 만들어 두셨을 것입니다.

1. Azure Databricks에서 Azure Storage 계정에 액세스하기 위한 구성을 입력합니다.

        val blobStorage = "<STORAGE ACCOUNT NAME>.blob.core.windows.net"
        val blobContainer = "<CONTAINER NAME>"
        val blobAccessKey =  "<ACCESS KEY>"

2. Azure Databricks와 Azure SQL Data Warehouse 간에 데이터를 이동하는 데 사용되는 임시 폴더를 지정합니다.

        val tempDir = "wasbs://" + blobContainer + "@" + blobStorage +"/tempDirs"

3. 다음 코드 조각을 실행하여 Azure Blob Storage 액세스 키를 구성에 저장합니다. 이렇게 하면 액세스 키를 노트북에서 일반 텍스트로 유지할 필요가 없습니다.

        val acntInfo = "fs.azure.account.key."+ blobStorage
        sc.hadoopConfiguration.set(acntInfo, blobAccessKey)

4. Azure SQL Data Warehouse 인스턴스에 연결하기 위한 값을 입력합니다. 필수 구성 요소의 일부로 SQL 데이터 웨어하우스를 이미 만들어 두셨을 것입니다.

        //SQL Data Warehouse related settings
        val dwDatabase = "<DATABASE NAME>"
        val dwServer = "<DATABASE SERVER NAME>" 
        val dwUser = "<USER NAME>"
        val dwPass = "<PASSWORD>"
        val dwJdbcPort =  "1433"
        val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
        val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
        val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass

5. 다음 코드 조각을 실행하여 변환된 데이터 프레임, **renamedColumnsDF**를 SQL 데이터 웨어하우스에 테이블로 로드합니다. 이 코드 조각은 SQL 데이터베이스에 **SampleTable**이라는 테이블을 만듭니다.

        spark.conf.set(
          "spark.sql.parquet.writeLegacyFormat",
          "true")
        
        renamedColumnsDF.write
            .format("com.databricks.spark.sqldw")
            .option("url", sqlDwUrlSmall) 
            .option("dbtable", "SampleTable")
            .option( "forward_spark_azure_storage_credentials","True")
            .option("tempdir", tempDir)
            .mode("overwrite")
            .save()

6. SQL 데이터베이스에 연결하여 **SampleTable**이 보이는지 확인합니다.

    ![샘플 테이블 확인](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table.png "샘플 테이블 확인")

7. select 쿼리를 실행하여 테이블의 콘텐츠를 확인합니다. **renamedColumnsDF** 데이터 프레임과 똑같은 데이터가 있어야 합니다.

    ![샘플 테이블 콘텐츠 확인](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table-content.png "샘플 테이블 콘텐츠 확인")

## <a name="clean-up-resources"></a>리소스 정리

자습서 실행이 완료되면 클러스터를 종료할 수 있습니다. 이렇게 하려면 왼쪽 창의 Azure Databricks 작업 영역에서 **클러스터**를 선택합니다. 종료하려는 클러스터에서 **작업** 열 아래의 줄임표 위로 커서를 이동한 다음, **종료** 아이콘을 선택합니다.

![Databricks 클러스터 중지](./media/databricks-extract-load-sql-data-warehouse/terminate-databricks-cluster.png "Databricks 클러스터 중지")

클러스터를 수동으로 종료하지 않은 경우 클러스터를 만드는 중에 **Terminate after __ minutes of inactivity**(비활성 __분 후 종료) 확인란을 선택하면 자동으로 중지됩니다. 이 경우 지정한 시간 동안 클러스터가 비활성 상태이면 클러스터가 자동으로 중지됩니다.

## <a name="next-steps"></a>다음 단계 
이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure Databricks 작업 영역 만들기
> * Azure Databricks에 Spark 클러스터 만들기
> * Azure 데이터 레이크 저장소 계정 만들기
> * Azure Data Lake Store에 데이터 업로드
> * Azure Databricks에 노트북 만들기
> * Data Lake Store에서 데이터 추출
> * Azure Databricks에서 데이터 변환
> * Azure SQL Data Warehouse에 데이터 로드

다음 자습서로 넘어가서 Azure Event Hubs를 사용하여 Azure Databricks로 실시간 데이터를 스트리밍하는 방법을 알아보세요.

> [!div class="nextstepaction"]
>[Event Hubs를 사용하여 Azure Databricks로 데이터 스트리밍](databricks-stream-from-eventhubs.md)
