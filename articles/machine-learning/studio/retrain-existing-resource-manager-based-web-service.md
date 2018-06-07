---
title: 기존 예측 웹 서비스 재학습 | Microsoft Docs
description: Azure Machine Learning에서 모델을 다시 학습하고 새로 학습된 모델을 사용하도록 웹 서비스를 업데이트하는 방법을 알아봅니다.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: cc4c26a2-5672-4255-a767-cfd971e46775
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2017
ms.openlocfilehash: f870bf1a282d7a044bb876e0015962b4f520a15f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="retrain-an-existing-predictive-web-service"></a>기존 예측 웹 서비스 재학습
이 문서에서는 다음 시나리오에 대한 재학습 프로세스를 설명합니다.

* 조작 가능한 웹 서비스로 배포한 학습 실험 및 예측 실험이 있습니다.
* 예측 웹 서비스가 점수 매기기를 수행하도록 하기 위해 사용할 새 데이터가 있습니다.

> [!NOTE]
> 새 웹 서비스를 배포하려면 웹 서비스를 배포하려는 구독에 충분한 권한이 있어야 합니다. 자세한 내용은 [Azure Machine Learning 웹 서비스 포털에서 웹 서비스 관리](manage-new-webservice.md)를 참조하세요.

기존 웹 서비스 및 실험을 시작으로 다음 단계를 수행해야 합니다.

1. 모델을 업데이트합니다.
   1. 웹 서비스 입력 및 출력을 감안하도록 학습 실험을 수정합니다.
   2. 학습 실험을 재학습 웹 서비스로 배포합니다.
   3. 학습 실험의 BES(일괄 처리 실행 서비스)를 사용하여 모델을 다시 학습합니다.
2. Azure Machine Learning PowerShell cmdlet을 사용하여 예측 실험을 업데이트합니다.
   1. Azure Resource Manager 계정으로 로그인합니다.
   2. 웹 서비스 정의를 가져옵니다.
   3. JSON으로 웹 서비스 정의를 내보냅니다.
   4. JSON에서 ilearner blob에 대한 참조를 업데이트합니다.
   5. JSON을 웹 서비스 정의로 가져옵니다.
   6. 새 웹 서비스 정의를 사용해 웹 서비스를 업데이트합니다.

## <a name="deploy-the-training-experiment"></a>학습 실험 배포
학습 실험을 재학습 웹 서비스로 배포하려면 웹 서비스 입력 및 출력을 모델에 추가해야 합니다. *웹 서비스 출력* 모듈을 실험의 *[모델 학습][train-model]* 모듈에 연결하여 학습 실험이 예측 실험에 사용할 수 있는 학습된 모델을 새로 생성하도록 설정할 수 있습니다. *모델 평가* 모듈이 있다면 또한 웹 서비스 출력을 연결하여 평가 결과를 출력으로 얻을 수 있습니다.

학습 실험을 업데이트하려면:

1. *웹 서비스 입력* 모듈을 데이터 입력(예를 들어 *누락 데이터 정리* 모듈)에 연결합니다. 일반적으로 이 입력 데이터가 원래 학습 데이터와 동일한 방식으로 처리해야 합니다.
2. *웹 서비스 출력* 모듈을 *모델 학습* 모듈의 출력에 연결합니다.
3. *모델 평가* 모듈이 있고 평가 결과를 출력하고자 할 경우, *웹 서비스 출력* 모듈을 *모델 평가* 모듈의 출력에 연결합니다.

실험을 실행합니다.

다음으로 학습 실험을 학습된 모델 및 모델 평가 결과를 생성하는 웹 서비스로 배포해야 합니다.

실험 캔버스 맨 아래에서 **웹 서비스 설정**을 클릭한 다음 **웹 서비스[New] 배포**를 선택합니다. Azure Machine Learning Web Services 포털은 **웹 서비스 배포** 페이지에 열립니다. 웹 서비스의 이름을 입력하고 결제 방식을 선택한 다음 **배포**를 클릭합니다. 학습된 모델을 만들 때는 Batch 실행 방법만 사용할 수 있습니다.

## <a name="retrain-the-model-with-new-data-by-using-bes"></a>BES를 사용하여 새 데이터로 모델 다시 학습
이 예에서는 재학습 응용 프로그램을 만드는 데 C#을 사용합니다. 또한 Python 또는 R 샘플 코드를 사용하여 이 작업을 수행할 수 있습니다.

재학습 API를 호출하려면:

1. Visual Studio(**새로 만들기** > **프로젝트** > **Visual C#** > **Windows 클래식 바탕 화면** > **콘솔 앱(.NET Framework)**)에서 C# 콘솔 응용 프로그램을 만듭니다.
2. Machine Learning Web Service 포털에 로그인합니다.
3. 현재 작업 중인 웹 서비스를 클릭합니다.
4. **사용**을 클릭합니다.
5. **사용** 페이지의 맨 아래에 있는 **샘플 코드** 섹션에서 **Batch**를 클릭합니다.
6. 배치 실행을 위해 샘플 C# 코드를 복사한 다음 Program.cs 파일에 붙여 넣습니다. 네임스페이스를 그대로 유지하도록 합니다.

설명에서 지정된 대로 NuGet 패키지인 Microsoft.AspNet.WebApi.Client를 추가합니다. Microsoft.WindowsAzure.Storage.dll에 참조를 추가하려면 우선 [Azure Storage 서비스용 클라이언트 라이브러리](https://www.nuget.org/packages/WindowsAzure.Storage)를 설치해야 합니다.

다음 스크린 샷은 Azure Machine Learning Web Services 포털의 **사용** 페이지를 보여줍니다.

![사용 페이지][1]

### <a name="update-the-apikey-declaration"></a>apikey 선언 업데이트
**apikey** 선언을 찾습니다.

    const string apiKey = "abc123"; // Replace this with the API key for the web service

**사용** 페이지의 **기본 사용량 정보** 섹션에서 기본 키를 찾아 **apikey** 선언으로 복사합니다.

### <a name="update-the-azure-storage-information"></a>Azure Storage 정보 업데이트
BES 샘플 코드는 로컬 드라이브에서(예: "C:\temp\CensusIpnput.csv") Azure Storage로 파일을 업로드하고 이를 처리하고 결과를 Azure Storage에 다시 작성합니다.

실험을 실행한 후 결과 워크플로는 다음과 비슷합니다.

![실행 후 결과 워크플로][4]

1. Azure 포털에 로그인합니다.
2. 왼쪽 탐색 열에서 **추가 서비스**를 클릭하고, **저장소 계정**을 검색하고 선택합니다.
3. 저장소 계정 목록에서 하나를 선택하여 재학습된 모델을 저장합니다.
4. 왼쪽 탐색 열에서 **선택키**를 클릭합니다.
5. **기본 선택키**를 복사하고 저장합니다.
6. 왼쪽 탐색 열에서 **컨테이너**를 클릭합니다.
7. 기존 컨테이너를 선택하거나 새 컨테이너를 만들어 이름을 저장합니다.

*StorageAccountName*, *StorageAccountKey* 및 *StorageContainerName* 선언을 찾아 포털에서 저장한 값을 업데이트합니다.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

또한 코드에 지정한 위치에서 입력 파일을 사용할 수 있는지 확인해야 합니다.

### <a name="specify-the-output-location"></a>출력 위치를 지정합니다.
요청 페이로드에서 출력 위치를 지정할 때 *RelativeLocation* 에서 지정된 파일의 확장명을 csv에서 `ilearner`으로 지정해야 합니다. 다음 예제를 참조하세요.

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

다음은 재학습 출력 예제입니다.

![재학습 출력][6]

## <a name="evaluate-the-retraining-results"></a>재학습 결과 평가
응용 프로그램을 실행할 때 출력은 평가 결과를 액세스하는 데 필요한 URL 및 공유 액세스 서명 토큰을 포함합니다.

*output2*에 대한 출력 결과의 *BaseLocation*, *RelativeLocation* 및 *SasBlobToken*을 조합하고(앞서 재학습 출력 이미지에 표시된 것처럼) 브라우저 주소 표시줄에 전체 URL을 붙여넣어 다시 학습된 모델의 성능 결과를 확인할 수 있습니다.

새로 학습된 모델이 기존 모델을 대체할 만큼 성능이 뛰어난지 여부를 확인하도록 결과를 검사합니다.

출력 결과에서 *BaseLocation*, *RelativeLocation* 및 *SasBlobToken*을 복사합니다.

## <a name="retrain-the-web-service"></a>웹 서비스 재학습
새 웹 서비스를 다시 교육하는 경우 새로 학습된 모델을 참조하여 예측 웹 서비스 정의를 업데이트합니다. 웹 서비스 정의는 웹 서비스 학습된 모델의 내부 표현이며 직접 수정할 수는 없습니다. 학습 실험이 아닌 예측 실험에 대한 웹 서비스 정의를 검색하고 있는지 확인합니다.

## <a name="sign-in-to-azure-resource-manager"></a>Azure Resource Manager로 로그인
먼저 [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) cmdlet을 사용하여 PowerShell 환경 내에서 Azure 계정에 로그인해야 합니다.

## <a name="get-the-web-service-definition-object"></a>웹 서비스 정의 개체 가져오기
다음으로 [Get AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) cmdlet을 호출하여 웹 서비스 정의 개체를 가져옵니다.

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

기존 웹 서비스의 리소스 그룹 이름을 결정하려면 구독 중인 웹 서비스를 표시하도록 매개 변수 없이 Get-AzureRmMlWebService cmdlet을 실행합니다. 웹 서비스를 찾은 다음 웹 서비스 ID를 살펴봅니다. 리소스 그룹 이름은 ID의 네 번째 요소로 *resourceGroups* 요소 바로 뒤에 있습니다. 다음 예제에서 리소스 그룹 이름은 Default-MachineLearning-SouthCentralUS입니다.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

또는 기존 웹 서비스의 리소스 그룹 이름을 결정하려면 Azure Machine Learning Web Services 포털에 로그인합니다. 웹 서비스를 선택합니다. 리소스 그룹 이름은 웹 서비스 URL의 다섯 번째 요소로 *resourceGroups* 요소 바로 뒤에 있습니다. 다음 예제에서 리소스 그룹 이름은 Default-MachineLearning-SouthCentralUS입니다.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-object-as-json"></a>JSON으로 웹 서비스 정의 개체 내보내기
새로 학습된 모델을 사용하여 새로 학습된 모델에 대한 정의를 수정하려면 먼저 [Export-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767935.aspx) cmdlet을 사용하여 JSON 형식 파일에 내보내기해야 합니다.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob"></a>ilearner blob에 대한 참조 업데이트
자산에서 [학습된 모델]을 찾아, ilearner Blob의 URI와 함께 *locationInfo* 노드의 *uri* 값을 업데이트합니다. URI는 BES 재학습 호출의 출력에서 *BaseLocation* 및 *RelativeLocation*을 조합하여 만듭니다.

     "asset3": {
        "name": "Retrain Sample [trained model]",
        "type": "Resource",
        "locationInfo": {
          "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
        },
        "outputPorts": {
          "Results dataset": {
            "type": "Dataset"
          }
        }
      },

## <a name="import-the-json-into-a-web-service-definition-object"></a>JSON을 웹 서비스 정의 개체로 가져오기
수정된 JSON 파일을 예측 실험을 업데이트하는 데 사용할 수 있는 웹 서비스 정의 개체로 변환하려면 [Import-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx) cmdlet을 사용해야 합니다.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service"></a>웹 서비스 업데이트
마지막으로, [Update-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx) cmdlet를 사용하여 예측 실험을 업데이트합니다.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

[1]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-consume-page.png
[4]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE04.png
[6]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE06.png

<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
