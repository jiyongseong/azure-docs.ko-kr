---
title: 프로그래밍 방식으로 Machine Learning 모델 다시 학습 | Microsoft Docs
description: Azure Machine Learning에서 프로그래밍 방식으로 모델을 다시 학습하고 새로 학습된 모델을 사용하도록 웹 서비스를 업데이트하는 방법을 알아봅니다.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 7ae4f977-e6bf-4d04-9dde-28a66ce7b664
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: 276ee051cf927197c026b0fb0850239a048ace41
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="retrain-machine-learning-models-programmatically"></a>프로그래밍 방식으로 Machine Learning 모델 다시 학습
이 연습에서는 C# 및 Machine Learning Batch Execution 서비스를 사용하여 Azure Machine Learning 웹 서비스를 프로그래밍 방식으로 다시 학습하는 방법을 배웁니다.

모델을 다시 학습한 후 다음 연습에서는 예측 웹 서비스에서 모델을 업데이트하는 방법을 보여 줍니다.

* Machine Learning 웹 서비스 포털에서 기존 웹 서비스를 배포한 경우 [기존 웹 서비스 재학습](retrain-a-classic-web-service.md)을 참조하세요. 
* 새 웹 서비스를 배포한 경우 [Machine Learning Management cmdlet을 사용하여 새 웹 서비스 재학습](retrain-new-web-service-using-powershell.md)을 참조하세요.

재학습 프로세스의 개요의 경우 [Machine Learning 모델 재학습](retrain-machine-learning-model.md)을 참조하세요.

기존의 새 Azure Resource Manager 기반 웹 서비스로 시작하려는 경우 [기존 예측 웹 서비스 재학습](retrain-existing-resource-manager-based-web-service.md)을 참조하세요.

## <a name="create-a-training-experiment"></a>학습 실험 만들기
이 예의 경우 Microsoft Azure Machine Learning 샘플의 "샘플 5: 이진 분류에 대한 학습, 테스트, 평가: 성인 데이터 집합"을 사용합니다. 

실험을 만들려면:

1. Microsoft Azure Machine Learning Studio에 로그인합니다. 
2. 대시보드의 오른쪽 아래 모서리에서 **새로 만들기**를 클릭합니다.
3. Microsoft 샘플에서 샘플 5를 선택합니다.
4. 실험의 이름을 바꾸려면 실험 캔버스의 위쪽에서 실험 이름 "샘플 5: 이진 분류에 대한 학습, 테스트, 평가: 성인 데이터 집합"을 선택합니다.
5. Census Model을 입력합니다.
6. 실험 캔버스 맨 아래에서 **실행**을 클릭합니다.
7. **웹 서비스 설정**을 클릭하고 **재학습 웹 서비스**를 선택합니다. 

다음은 초기 실험을 보여 줍니다.
   
   ![초기 실험.][2]


## <a name="create-a-predictive-experiment-and-publish-as-a-web-service"></a>예측 실험을 만들고 웹 서비스로 게시
다음으로 서술적 실험을 만듭니다.

1. 실험 캔버스 맨 아래에서 **웹 서비스 설정**을 클릭하고 **예측 웹 서비스**를 선택합니다. 모델을 학습된 모델로 저장한 다음 웹 서비스 입력 및 출력 모듈을 추가합니다. 
2. **실행**을 클릭합니다. 
3. 실험 실행이 완료된 후 **웹 서비스 배포[기존]**을 클릭하고 **웹 서비스 배포[신규]**를 선택합니다.

> [!NOTE] 
> 새 웹 서비스를 배포하려면 웹 서비스를 배포하려는 구독에 충분한 권한이 있어야 합니다. 자세한 내용은 [Azure Machine Learning 웹 서비스 포털에서 웹 서비스 관리](manage-new-webservice.md)를 참조하세요. 

## <a name="deploy-the-training-experiment-as-a-training-web-service"></a>학습 실험을 학습 웹 서비스로 배포
학습된 모델을 다시 학습하려면 재학습 웹 서비스로 만든 학습 실험을 배포해야 합니다. 이 웹 서비스가 학습된 모델을 새로 생성할 수 있으려면 *웹 서비스 출력* 모듈이 *[학습 모델][train-model]* 모듈에 연결되어야 합니다.

1. 학습 실험으로 돌아가려면 왼쪽 창에서 실험 아이콘을 클릭한 다음 Census Model이라는 실험을 클릭합니다.  
2. 실험 항목 검색 상자에 웹 서비스를 입력합니다. 
3. *웹 서비스 입력* 모듈을 실험 캔버스로 끌어 놓고 해당 출력을 *누락된 데이터 정리* 모듈에 연결합니다.  그러면 이 재학습 데이터가 원래 학습 데이터와 동일한 방식으로 처리됩니다.
4. 두 *웹 서비스 출력* 모듈을 실험 캔버스로 끌어 놓습니다. 하나는 *모델 학습* 모듈의 출력에, 다른 하나는 *모델 평가* 모듈의 출력에 연결합니다. **학습 모델** 에 대한 웹 서비스 출력에서 새로 학습된 모델을 제공합니다. **모델 평가**에 연결된 출력은 성능 결과인 해당 모듈의 출력을 반환합니다.
5. **실행**을 클릭합니다. 

다음으로 학습 실험을 학습된 모델 및 모델 평가 결과를 생성하는 웹 서비스로 배포해야 합니다. 이를 위해 다음에 할 작업은 기존 웹 서비스 또는 새로운 웹 서비스 중 어떤 것을 사용하여 작업하는지에 따라 다릅니다.  

**기존 웹 서비스**

실험 캔버스 맨 아래에서 **웹 서비스 설정**을 클릭하고 **웹 서비스 배포[기존]**을 선택합니다. 웹 서비스 **대시보드**가 Batch 실행을 위한 API 키 및 API 도움말 페이지와 함께 표시됩니다. 학습된 모델을 만들 때는 Batch 실행 방법만 사용할 수 있습니다.

**새 웹 서비스**

실험 캔버스 맨 아래에서 **웹 서비스 설정**을 클릭하고 **웹 서비스 배포[신규]**를 선택합니다. Web Service Azure Machine Learning Web Services 포털은 웹 서비스 배포 페이지에 열립니다. 웹 서비스의 이름을 입력하고 결제 방식을 선택한 다음 **배포**를 클릭합니다. 학습된 모델을 만들 때는 Batch 실행 방법만 사용할 수 있습니다.

어느 경우든 실험 실행이 완료된 후 결과 워크플로는 다음과 같이 표시됩니다.

![실행 후 결과 워크플로.][4]



## <a name="retrain-the-model-with-new-data-using-bes"></a>BES를 사용하여 새 데이터로 모델 다시 학습
이 예에서는 재학습 응용 프로그램을 만드는 데 C#을 사용합니다. 또한 Python 또는 R 샘플 코드를 사용하여 이 작업을 수행할 수 있습니다.

재학습 API를 호출하려면:

1. Visual Studio(**새로 만들기** > **프로젝트** > **Visual C#** > **Windows 클래식 바탕 화면** > **콘솔 앱(.NET Framework)**)에서 C# 콘솔 응용 프로그램을 만듭니다.
2. Machine Learning 웹 서비스 포털에 로그인합니다.
3. 기존 웹 서비스를 사용하여 작업하는 경우 **Classic Web Services**를 클릭합니다.
   1. 현재 작업 중인 웹 서비스를 클릭합니다.
   2. "기본" 끝점을 클릭합니다.
   3. **사용**을 클릭합니다.
   4. **사용** 페이지의 맨 아래에 있는 **샘플 코드** 섹션에서 **Batch**를 클릭합니다.
   5. 이 절차의 5 단계를 계속합니다.
4. 새 웹 서비스를 사용하여 작업하는 경우 **Web Services**를 클릭합니다.
   1. 현재 작업 중인 웹 서비스를 클릭합니다.
   2. **사용**을 클릭합니다.
   3. 사용 페이지의 맨 아래에 있는 **샘플 코드** 섹션에서 **Batch**를 클릭합니다.
5. 배치 실행을 위해 샘플 C# 코드를 복사한 다음 네임스페이스를 그대로 유지하여 Program.cs 파일에 붙여 넣습니다.

설명에서 지정된 대로 Nuget 패키지인 Microsoft.AspNet.WebApi.Client를 추가합니다. Microsoft.WindowsAzure.Storage.dll에 참조를 추가하려면 우선 Microsoft Azure Storage 서비스용 클라이언트 라이브러리를 설치해야 합니다. 자세한 내용은 [Windows Storage Services](https://www.nuget.org/packages/WindowsAzure.Storage)를 참조하세요.

### <a name="update-the-apikey-declaration"></a>apikey 선언 업데이트
**apikey** 선언을 찾습니다.

    const string apiKey = "abc123"; // Replace this with the API key for the web service

**사용** 페이지의 **기본 사용량 정보** 섹션에서 기본 키를 찾아 **apikey** 선언으로 복사합니다.

### <a name="update-the-azure-storage-information"></a>Azure Storage 정보 업데이트
BES 샘플 코드는 로컬 드라이브에서(예: "C:\temp\CensusIpnput.csv") Azure Storage로 파일을 업로드하고 이를 처리하고 결과를 Azure Storage에 다시 작성합니다.  

이 작업을 수행하려면 Azure 클래식 포털에서 Storage 계정에 대한 Storage 계정 이름, 키 및 컨테이너 정보를 검색한 다음 코드에서 해당 값을 업데이트해야 합니다. 

1. 클래식 Azure 포털에 로그인합니다.
2. 왼쪽 탐색 열에서 **Storage**를 클릭합니다.
3. 저장소 계정 목록에서 하나를 선택하여 재학습된 모델을 저장합니다.
4. 페이지 맨 아래에 있는 **액세스 키 관리**를 클릭합니다.
5. **기본 액세스 키**를 복사하여 저장한 후 대화 상자를 닫습니다. 
6. 페이지 위쪽에서 **컨테이너**를 클릭합니다.
7. 기존 컨테이너를 선택하거나 새 컨테이너를 만들어 이름을 저장합니다.

*StorageAccountName*, *StorageAccountKey* 및 *StorageContainerName* 선언을 찾아 Azure Portal에서 저장한 값을 업데이트합니다.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure Storage Account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage Key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage Container name

또한 코드에 지정한 위치에서 입력 파일을 사용할 수 있는지 확인해야 합니다. 

### <a name="specify-the-output-location"></a>출력 위치를 지정합니다.
요청 페이로드에서 출력 위치를 지정할 때 *RelativeLocation* 에서 지정된 파일의 확장명을 csv에서 ilearner로 지정해야 합니다. 

다음 예제를 참조하세요.

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you would like to use for your output file, and valid file extension (usually .csv for scoring results, or .ilearner for trained models)*/
            }
        },

> [!NOTE]
> 출력 위치 이름은 웹 서비스 출력 모듈을 추가한 순서에 따라 이 연습의 출력 위치 이름과 다를 수 있습니다. 이 학습 실험을 두 개의 출력으로 설정했으므로 결과는 둘 모두에 대한 저장소 위치 정보를 포함합니다.  
> 
> 

![재학습 출력][6]

다이어그램 4: 재학습 출력.

## <a name="evaluate-the-retraining-results"></a>재학습 결과 평가
응용 프로그램을 실행할 때 출력은 평가 결과를 액세스하는 데 필요한 URL 및 SAS 토큰을 포함합니다.

*output2*에 대한 출력 결과의 *BaseLocation*, *RelativeLocation* 및 *SasBlobToken*을 조합하고(앞서 재학습 출력 이미지에 표시된 것처럼) 브라우저 주소 표시줄에 전체 URL을 붙여넣어 다시 학습된 모델의 성능 결과를 확인할 수 있습니다.  

새로 학습된 모델이 기존 모델을 대체할 만큼 성능이 뛰어난지 여부를 확인하도록 결과를 검사합니다.

출력 결과에서 *BaseLocation*, *RelativeLocation* 및 *SasBlobToken*을 복사합니다. 이는 재학습 프로세스 중에 사용하게 됩니다.

## <a name="next-steps"></a>다음 단계
**웹 서비스 배포[기존]**를 클릭하여 예측 웹 서비스를 배포한 경우 [기존 웹 서비스 재학습](retrain-a-classic-web-service.md)을 참조하세요.

**웹 서비스 배포[신규]**를 클릭하여 예측 웹 서비스를 배포한 경우 [Machine Learning Management cmdlet을 사용하여 새 웹 서비스 재학습](retrain-new-web-service-using-powershell.md)을 참조하세요.

<!-- Retrain a New web service using the Machine Learning Management REST API -->


[1]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png
[7]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE07.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
