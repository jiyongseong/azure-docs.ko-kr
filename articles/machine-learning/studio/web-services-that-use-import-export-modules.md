---
title: Azure Machine Learning 웹 서비스에서 Import/Export 데이터 사용 | Microsoft Docs
description: 데이터 가져오기 및 내보내기 데이터 모듈을 사용하여 웹 서비스에서 데이터를 보내고 받는 방법을 알아봅니다.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 3a7ac351-ebd3-43a1-8c5d-18223903d08e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.openlocfilehash: 61143302b21f93326f9f81885d845cd388203eb3
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="deploying-azure-ml-web-services-that-use-data-import-and-data-export-modules"></a>데이터 가져오기 및 데이터 내보내기 모듈을 사용하는 Azure ML 웹 서비스 배포

예측 실험을 만들 때 일반적으로 웹 서비스 입력 및 출력을 추가합니다. 실험을 배포하면 소비자는 입력 및 출력을 통해 웹 서비스에서 데이터를 보내고 받을 수 있습니다. 일부 응용 프로그램에서는 소비자 데이터를 데이터 피드에서 사용할 수 있거나 해당 데이터가 Azure Blob 저장소와 같은 외부 데이터 원본에 이미 있을 수 있습니다. 이러한 경우 웹 서비스 입력 및 출력을 사용하여 데이터를 읽고 쓸 필요가 없습니다. 대신, BES(Batch 실행 서비스)를 사용하여 데이터 가져오기 모듈을 통해 데이터 원본에서 데이터를 읽고, 데이터 내보내기 모듈을 통해 다른 데이터 위치에 점수 매기기 결과를 쓸 수 있습니다.

데이터 가져오기 및 내보내기 데이터 모듈은 HTTP를 통한 웹 URL, Hive 쿼리, Azure SQL Database, Azure Table Storage, Azure Blob Storage, 데이터 피드 또는 온-프레미스 SQL Database 등의 다양한 데이터 위치에서 읽고 쓸 수 있습니다.

이 항목에서는 "샘플 5: 이진 분류에 대한 학습, 테스트, 평가: 성인 데이터 집합" 샘플을 사용하고 데이터 집합이 censusdata라는 Azure SQL 테이블에 이미 로드되었다고 가정합니다.

## <a name="create-the-training-experiment"></a>학습 실험 만들기
"샘플 5: 이진 분류에 대한 학습, 테스트, 평가: 성인 데이터 집합" 샘플을 열면 성인 인구 조사 소득 이진 분류 데이터 집합이 사용됩니다. 또한 캔버스의 실험은 다음 이미지와 비슷합니다.

![실험의 초기 구성입니다.](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

Azure SQL 테이블에서 데이터를 읽으려면

1. 데이터 집합 모듈을 삭제합니다.
2. 구성 요소 검색 상자에 가져오기를 입력합니다.
3. 결과 목록에서 *데이터 가져오기* 모듈을 실험 캔버스에 추가합니다.
4. *데이터 가져오기* 모듈의 출력을 *누락 데이터 정리* 모듈의 입력에 연결합니다.
5. 속성 패널의 **Azure SQL Database** in the **Azure SQL Database** 를 선택합니다.
6. **데이터베이스 서버 이름**, **데이터베이스 이름**, **사용자 이름** 및 **암호** 필드에 데이터베이스에 대한 적절한 정보를 입력합니다.
7. 데이터베이스 쿼리 필드에 다음 쿼리를 입력합니다.
   
     select [age],
   
        [workclass],
        [fnlwgt],
        [education],
        [education-num],
        [marital-status],
        [occupation],
        [relationship],
        [race],
        [sex],
        [capital-gain],
        [capital-loss],
        [hours-per-week],
        [native-country],
        [income]
     from dbo.censusdata;
8. 실험 캔버스 맨 아래에서 **실행**을 클릭합니다.

## <a name="create-the-predictive-experiment"></a>예측 실험 만들기
다음에는 웹 서비스를 배포할 예측 실험을 설정합니다.

1. 실험 캔버스 맨 아래에서 **웹 서비스 설정**을 클릭하고 **예측 웹 서비스[권장]**를 선택합니다.
2. 예측 실험에서 *웹 서비스 입력* 및 *웹 서비스 출력 모듈*을 제거합니다. 
3. 구성 요소 검색 상자에 내보내기를 입력합니다.
4. 결과 목록에서 *데이터 내보내기* 모듈을 실험 캔버스에 추가합니다.
5. *모델 점수 매기기* 모듈의 출력을 *데이터 내보내기* 모듈의 입력에 연결합니다. 
6. 속성 패널의 데이터 대상 드롭다운에서 **Azure SQL Database** 를 선택합니다.
7. **데이터베이스 서버 이름**, **데이터베이스 이름**, **서버 사용자 계정 이름** 및 **서버 사용자 계정 암호** 필드에 데이터베이스에 대한 적절한 정보를 입력합니다.
8. **쉼표로 구분된 저장할 열 목록** 필드에 점수가 매겨진 레이블을 입력합니다.
9. **데이터 테이블 이름 필드**에 dbo.ScoredLabels를 입력합니다. 이 테이블이 없으면 실험을 실행하거나 웹 서비스를 호출할 때 만들어집니다.
10. **쉼표로 구분된 데이터베이스 열 목록** 필드에 점수가 매겨진 레이블을 입력합니다.

최종 웹 서비스를 호출하는 응용 프로그램을 작성하는 경우 런타임에 다른 입력 쿼리 또는 대상 테이블을 지정할 수 있습니다. 이러한 입력 및 출력을 구성하려면 웹 서비스 매개 변수 기능을 사용하여 *데이터 가져오기* 모듈 *데이터 원본* 속성 및 *데이터 내보내기* 모드 데이터 대상 속성을 설정합니다.  웹 서비스 매개 변수에 대한 자세한 내용은 Cortana Intelligence 및 Machine Learning 블로그에서 [AzureML 웹 서비스 매개 변수 항목](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) 을 참조하세요.

가져오기 쿼리 및 대상 테이블에 대한 웹 서비스 매개 변수를 구성하려면

1. *데이터 가져오기* 모듈에 대한 속성 창에서 **데이터베이스 쿼리** 필드 오른쪽 위에 있는 아이콘을 클릭하고 **웹 서비스 매개 변수로 설정**을 선택합니다.
2. *데이터 내보내기* 모듈에 대한 속성 창에서 **데이터 테이블 이름** 필드 오른쪽 위에 있는 아이콘을 클릭하고 **웹 서비스 매개 변수로 설정**을 선택합니다.
3. *데이터 내보내기* 모듈 속성 창 아래쪽의 **웹 서비스 매개 변수** 섹션에서 데이터베이스 쿼리를 클릭하고 이름을 쿼리로 바꿉니다.
4. **데이터 테이블 이름**을 클릭하고 이름을 **테이블**로 바꿉니다.

완료되면 실험은 다음 이미지와 비슷하게 표시됩니다.

![실험의 최종 모습입니다.](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

이제 실험을 웹 서비스로 배포할 수 있습니다.

## <a name="deploy-the-web-service"></a>웹 서비스 배포
기존 또는 새 웹 서비스로 배포할 수 있습니다.

### <a name="deploy-a-classic-web-service"></a>기존 웹 서비스 배포
기존 웹 서비스로 배포하고 해당 서비스를 사용하기 위한 응용 프로그램을 만들려면

1. 실험 캔버스 맨 아래에서 실행을 클릭합니다.
2. 실행이 완료되면 **웹 서비스 배포**를 클릭하고 **웹 서비스 배포[클래식]**를 선택합니다.
3. 웹 서비스 대시보드에서 API 키를 찾습니다. 나중에 사용할 수 있게 복사한 다음 저장합니다.
4. **기본 끝점** 테이블에서 **Batch 실행** 링크를 클릭하여 API 도움말 페이지를 엽니다.
5. Visual Studio(**새로 만들기** > **프로젝트** > **Visual C#** > **Windows 클래식 바탕 화면** > **콘솔 앱(.NET Framework)**)에서 C# 콘솔 응용 프로그램을 만듭니다.
6. API 도움말 페이지 맨 아래에서 **샘플 코드** 섹션을 찾습니다.
7. C# 샘플 코드를 복사하고 Program.cs 파일에 붙여 넣은 후 blob 저장소에 대한 모든 참조를 제거합니다.
8. *apiKey* 변수의 값을 이전에 저장된 API 키로 업데이트합니다.
9. 요청 선언을 찾고 *데이터 가져오기* 및 *데이터 내보내기* 모듈에 전달되는 웹 서비스 매개 변수 값을 업데이트합니다. 이 경우 원래 쿼리를 사용하지만 새 테이블 이름을 정의합니다.
   
        var request = new BatchExecutionRequest() 
        {           
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable2" },
            }
        };
10. 응용 프로그램을 실행합니다. 

실행이 완료되면 새 테이블이 점수 매기기 결과를 포함하는 데이터베이스에 추가됩니다.

### <a name="deploy-a-new-web-service"></a>새 웹 서비스 배포

> [!NOTE] 
> 새 웹 서비스를 배포하려면 웹 서비스를 배포하려는 구독에 충분한 권한이 있어야 합니다. 자세한 내용은 [Azure Machine Learning 웹 서비스 포털에서 웹 서비스 관리](manage-new-webservice.md)를 참조하세요. 

새 웹 서비스로 배포하고 해당 서비스를 사용하기 위한 응용 프로그램을 만들려면

1. 실험 캔버스 맨 아래에서 **실행**을 클릭합니다.
2. 실행이 완료되면 **웹 서비스 배포**를 클릭하고 **웹 서비스 배포[신규]**를 선택합니다.
3. 실험 배포 페이지에서 웹 서비스의 이름을 입력하고 가격 책정 계획을 선택한 후 **배포**를 클릭합니다.
4. **빠른 시작** 페이지에서 **사용**을 클릭합니다.
5. **샘플 코드** 섹션에서 **Batch**를 클릭합니다.
6. Visual Studio(**새로 만들기** > **프로젝트** > **Visual C#** > **Windows 클래식 바탕 화면** > **콘솔 앱(.NET Framework)**)에서 C# 콘솔 응용 프로그램을 만듭니다.
7. C# 샘플 코드를 복사하고 Program.cs 파일에 붙여 넣습니다.
8. *apiKey* 변수 값을 **기본 사용량 정보** 섹션에 있는 **기본 키**로 업데이트합니다.
9. *scoreRequest* 선언을 찾고 *데이터 가져오기* 및 *데이터 내보내기* 모듈에 전달되는 웹 서비스 매개 변수 값을 업데이트합니다. 이 경우 원래 쿼리를 사용하지만 새 테이블 이름을 정의합니다.
   
        var scoreRequest = new
        {       
            Inputs = new Dictionary<string, StringTable>()
            {
            },
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable3" },
            }
        };
10. 응용 프로그램을 실행합니다. 

