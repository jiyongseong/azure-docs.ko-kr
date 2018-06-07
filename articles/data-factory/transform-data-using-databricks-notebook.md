---
title: Azure Data Factory에서 Databricks Notebook 활동으로 Databricks Notebook 실행
description: Azure Data Factory에서 Databricks Notebook 작업을 사용하여 databricks 작업 클러스터에 대해 Databricks Notebook을 실행하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/12/2018
ms.author: abnarain
ms.reviewer: douglasl
ms.openlocfilehash: 78954e2dd00e425d2dfdd81d2c3e386f199f4f8f
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2018
---
# <a name="run-a-databricks-notebook-with-the-databricks-notebook-activity-in-azure-data-factory"></a>Azure Data Factory에서 Databricks Notebook 작업으로 Databricks Notebook 실행

이 자습서에서는 Azure Portal을 사용하여 databricks 작업 클러스터에 대해 Databricks Notebook을 실행하는 Azure Data Factory 파이프라인을 만듭니다. 또한 실행 중에 Azure Data Factory 매개 변수를 Databricks Notebook에 전달합니다.

이 자습서에서 수행하는 단계는 다음과 같습니다.

  - 데이터 팩터리를 만듭니다.

  - Databricks Notebook 작업을 사용하는 파이프라인을 만듭니다.

  - 파이프라인 실행 트리거

  - 파이프라인 실행을 모니터링합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/)을 만듭니다.

11분 동안 이 기능의 소개 및 데모에 대한 다음 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/ingest-prepare-and-transform-using-azure-databricks-and-data-factory/player]

## <a name="prerequisites"></a>필수 조건

  - **Azure Databricks 작업 영역**. [databricks 작업 영역을 만들거나](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) 기존 작업 영역을 사용합니다. Azure Databricks 작업 영역에 Python Notebook을 만듭니다. 그런 다음, Azure Data Factory를 사용하여 Notebook을 실행하고 매개 변수를 전달합니다.

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.

1.  **Microsoft Edge** 또는 **Google Chrome** 웹 브라우저를 시작합니다. 현재 Data Factory UI는 Microsoft Edge 및 Google Chrome 웹 브라우저에서만 지원됩니다.

2.  왼쪽 메뉴에서 **새로 만들기**를 선택하고 **데이터 + 분석**을 선택한 다음 **데이터 팩터리**를 선택합니다.

    ![새 데이터 팩터리 만들기](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image1.png)

3.  **새 데이터 팩터리** 창에서 **이름** 아래에 **ADFTutorialDataFactory**를 입력합니다.

    Azure Data Factory의 이름은 *전역적으로 고유*해야 합니다. 다음과 같은 오류가 표시되면 데이터 팩터리의 이름을 변경합니다. (예를 들어 **\<yourname\>ADFTutorialDataFactory** 사용). 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](https://docs.microsoft.com/azure/data-factory/naming-rules) 문서를 참조하세요.

    ![새 데이터 팩터리에 대한 이름을 제공합니다.](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image2.png)

4.  **구독**에 대해 데이터 팩터리를 만들려는 위치에 Azure 구독을 선택합니다.

5.  **리소스 그룹**에 대해 다음 단계 중 하나를 사용합니다.
    
    - **기존 항목 사용**을 선택하고, 드롭다운 목록에서 기존 리소스 그룹을 선택합니다.
    
    - **새로 만들기**를 선택하고, 리소스 그룹의 이름을 입력합니다.

    이 빠른 시작의 일부 단계에서는 리소스 그룹에 **ADFTutorialResourceGroup**이라는 이름을 사용한다고 가정합니다. 리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)를 참조하세요.

1.  **버전**에 **V2(미리 보기)** 를 선택합니다.

2.  **위치**에 데이터 팩터리의 위치를 선택합니다.

    현재 미국 동부, 미국 동부 2 및 유럽 서부 지역에서만 Data Factory V2를 사용하여 데이터 팩터리를 만들 수 있습니다. 데이터 팩터리에서 사용하는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 계산(HDInsight 등)은 다른 지역에 있을 수 있습니다.

3.  **대시보드에 고정**을 선택합니다.

4.  **만들기**를 선택합니다.

5.  대시보드에서 **데이터 팩터리 배포 중** 상태의 다음과 같은 타일이 표시됩니다.

    ![](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image3.png)

6.  만들기가 완료되면 **데이터 팩터리** 페이지가 표시됩니다. **작성 및 모니터링** 타일을 선택하여 별도의 탭에서 Data Factory UI 응용 프로그램을 시작합니다.

    ![데이터 팩터리 UI 응용 프로그램의 시작](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image4.png)

## <a name="create-linked-services"></a>연결된 서비스 만들기

이 섹션에서는 Databricks 연결된 서비스를 작성합니다. 이 연결된 서비스에는 Databricks 클러스터에 대한 연결 정보가 포함됩니다.

### <a name="create-an-azure-databricks-linked-service"></a>Azure Databricks 연결된 서비스 만들기

1.  **시작하기** 페이지에서 왼쪽 패널의 **편집** 탭으로 전환합니다.

    ![새 연결된 서비스 편집](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image5.png)

2.  창의 아래쪽에서 **연결**, **+ 새로 만들기**를 차례로 클릭합니다.
    
    ![새 연결 만들기](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image6.png)

3.  **새 연결된 서비스** 창에서 **데이터 저장소** \> **Azure Databricks**를 차례로 선택한 다음, **계속**을 선택합니다.
    
    ![Databricks 연결된 서비스 지정](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image7.png)

4.  **새 연결된 서비스** 창에서 다음 단계를 완료합니다.
    
    1.  **이름**에 대해 ***AzureDatabricks\_LinkedService***를 입력합니다.
    
    2.  **클러스터**에 대해 **새 클러스터**를 선택합니다.
    
    3.  **도메인/지역**에 대해 Azure Databricks 작업 영역이 있는 지역을 선택합니다.
    
    4.  이 자습서의 경우 **클러스터 노드 유형**에 대해 **Standard\_D3\_v2**를 선택합니다.
    
    5.  **액세스 토큰**에 대해 Azure Databricks 작업 공간에서 생성합니다. 단계는 [여기](https://docs.databricks.com/api/latest/authentication.html#generate-token)서 찾을 수 있습니다.
    
    6.  **클러스터 버전**에 대해 **4.0 베타**(최신 버전)를 선택합니다.
    
    7.  **작업자 노드 수**에 대해 **2**를 입력합니다.
    
    8.  **마침**을 선택합니다.

        ![연결된 서비스 만들기 완료](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image8.png)

## <a name="create-a-pipeline"></a>파이프라인을 만듭니다.

1.  **+**(더하기) 단추를 선택한 다음 메뉴에서 **파이프라인**을 선택합니다.

    ![새 파이프라인 만들기에 대한 단추](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image9.png)

2.  **파이프라인**에서 사용할 **매개 변수**를 만듭니다. 나중에 이 매개 변수를 Databricks Notebook 작업에 전달합니다. 빈 파이프라인에서 **매개 변수** 탭, **새로 만들기**를 차례로 클릭한 다음, 이름으로 '**name**'을 지정합니다.

    ![새 매개 변수 만들기](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image10.png)

    ![name 매개 변수 만들기](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image11.png)

3.  **활동** 도구 상자에서 **Databricks**를 펼칩니다. **활동** 도구 상자에서 **Notebook** 활동을 파이프라인 디자이너 화면으로 끌어서 놓습니다.

    ![Notebook을 디자이너 화면으로 끌기](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image12.png)

4.  아래쪽의 **Databricks** **Notebook** 활동 창에 대한 속성에서 다음 단계를 완료합니다.

    a. **설정** 탭으로 전환합니다.

    나. 이전 절차에서 만든 **myAzureDatabricks\_LinkedService**를 선택합니다.

    다. Databricks **Notebook 경로**를 선택합니다. 여기서는 노트북을 만들고 경로를 지정해 보겠습니다. 다음 몇 단계를 따라 Notebook 경로를 가져옵니다.

       1. Azure Databricks 작업 영역을 시작합니다.

       2. 작업 공간에서 **새 폴더**를 만들고 **adftutorial**로 호출합니다.

          ![새 폴더 만들기](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image13.png)

       3. [새 Notebook](https://docs.databricks.com/user-guide/notebooks/index.html#creating-a-notebook)(Python)을 만듭니다. **adftutorial** 폴더 아래에서 **mynotebook**을 호출하고 **,** **만들기**를 클릭해 보겠습니다.

          ![새 Notebook 만들기](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image14.png)

          ![새 Notebook의 속성 설정](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image15.png)

       4. 새로 만든 "mynotebook" Notebook에서 다음 코드를 추가합니다.

           ```
           # Creating widgets for leveraging parameters, and printing the parameters

           dbutils.widgets.text("input", "","")
           dbutils.widgets.get("input")
           y = getArgument("input")
           print "Param -\'input':"
           print y
           ```

           ![매개 변수에 대한 위젯 만들기](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image16.png)

       5. 이 경우 **Notebook 경로**는 **/adftutorial/mynotebook**입니다.

5.  **Data Factory UI 제작 도구**로 다시 전환합니다. **Notebook1 활동** 아래에서 **설정** 탭으로 이동합니다. 
    
    a.  Notebook 활동에 **매개 변수를 추가**합니다. 이전에 **파이프라인**에 추가한 것과 동일한 매개 변수를 사용합니다.

       ![매개 변수 추가](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image17.png)

    나.  매개 변수의 이름을 **input**으로 지정하고 값을 **@pipeline().parameters.name** 식으로 제공합니다.

6.  파이프라인에 대한 유효성을 검사하려면 도구 모음에서 **유효성 검사** 단추를 선택합니다. 유효성 검사 창을 닫으려면 **\>\>**(오른쪽 화살표) 단추를 선택합니다.

    ![파이프라인 유효성 검사](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image18.png)

7.  **모두 게시**를 선택합니다. Data Factory UI는 엔터티(연결된 서비스 및 파이프라인)를 Azure Data Factory 서비스에 게시합니다.

    ![새 데이터 팩터리 엔터티 게시](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image19.png)

## <a name="trigger-a-pipeline-run"></a>파이프라인 실행 트리거

도구 모음에서 **트리거**를 선택한 다음 **지금 트리거**를 선택합니다.

![지금 트리거 명령 선택](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image20.png)

**파이프라인 실행** 대화 상자에서 **name** 매개 변수를 요청합니다. 여기서는 **/path/filename**을 매개 변수로 사용합니다. **마침**을 클릭합니다.

![매개 변수 이름에 대한 값 제공](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image21.png)

## <a name="monitor-the-pipeline-run"></a>파이프라인 실행을 모니터링합니다.

1.  **모니터** 탭으로 전환합니다. 파이프라인 실행이 표시되는지 확인합니다. 노트북이 실행되는 Databricks 작업 클러스터를 만드는 데 약 5~8분이 걸립니다.

    ![파이프라인 모니터링](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image22.png)

2.  주기적으로 **새로 고침**을 선택하여 파이프라인 실행 상태를 확인합니다.

3.  파이프라인 실행과 연결된 활동 실행을 보려면 **작업** 열에서 **활동 실행 보기**를 선택합니다.

    ![활동 실행 보기](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image23.png)

위쪽의 **파이프라인** 링크를 선택하여 파이프라인 실행 보기로 다시 전환할 수 있습니다.

## <a name="verify-the-output"></a>출력 확인

**Azure Databricks 작업 영역**에 로그온하고 **클러스터**로 이동할 수 있으며, **작업** 상태가 *실행 보류 중, 실행 중 또는 종료됨*으로 표시됩니다.

![작업 클러스터 및 작업 보기](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image24.png)

**작업 이름**을 클릭하여 세부 정보를 탐색할 수 있습니다. 성공적으로 실행되면 전달된 매개 변수와 Python 노트북의 출력의 유효성을 검사할 수 있습니다.

![실행 세부 정보 및 출력 보기](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image25.png)

## <a name="next-steps"></a>다음 단계

이 샘플의 파이프라인에서 Databricks Notebook 활동을 트리거하고 여기에 매개 변수를 전달합니다. 다음 방법에 대해 알아보았습니다.

  - 데이터 팩터리를 만듭니다.

  - Databricks Notebook 활동을 사용하는 파이프라인을 만듭니다.

  - 파이프라인 실행 트리거

  - 파이프라인 실행을 모니터링합니다.
