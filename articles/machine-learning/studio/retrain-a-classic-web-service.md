---
title: 클래식 웹 서비스 재학습 | Microsoft Docs
description: Azure Machine Learning에서 프로그래밍 방식으로 모델을 다시 학습하고 새로 학습된 모델을 사용하도록 웹 서비스를 업데이트하는 방법을 알아봅니다.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: e36e1961-9e8b-4801-80ef-46d80b140452
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: d0c40d952008f58f9b99c1c44d06e971a1e60c3b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="retrain-a-classic-web-service"></a>기존 웹 서비스 재학습
배포한 예측 웹 서비스는 기본 점수 매기기 끝점입니다. 기본 끝점은 원래 학습 및 점수 매기기 실험과 동기화 상태를 유지하므로 기본 끝점에 대한 학습된 모델을 바꿀 수 없습니다. 웹 서비스를 다시 학습하려면 웹 서비스에 새 끝점을 추가해야 합니다. 

## <a name="prerequisites"></a>필수 조건
학습 실험 및 예측 실험을 [프로그래밍 방식으로 Machine Learning 모델 재학습](retrain-models-programmatically.md)에서 보듯이 설정해야 합니다. 

> [!IMPORTANT]
> 예측 실험을 기존 Machine Learning 웹 서비스로 배포해야 합니다. 
> 
> 

웹 서비스 배포에 대한 자세한 내용은 [Azure Machine Learning 웹 서비스 배포](publish-a-machine-learning-web-service.md)를 참조하세요.

## <a name="add-a-new-endpoint"></a>새 끝점 추가
배포한 예측 웹 서비스는 원래의 학습 및 점수 매기기 실험 학습된 모델과 동기화를 유지하는 기본 점수 매기기 끝점을 포함합니다. 새로 학습된 모델을 사용하여 웹 서비스를 업데이트하려면 새 점수 매기기 끝점을 만들어야 합니다. 

학습된 모델과 함께 업데이트될 수 있는 예측 웹 서비스에서 새 점수 매기기 끝점을 만들려면:

> [!NOTE]
> 끝점을 학습 웹 서비스가 아닌 예측 웹 서비스에 추가해야 합니다. 학습 및 예측 웹 서비스를 모두 올바르게 배포한 경우 나열된 두 개의 별도 웹 서비스가 표시됩니다. 예측 웹 서비스는 "[예측 exp.]"로 끝나야 합니다.
> 
> 

웹 서비스에 새로 끝점을 추가할 수 있는 두 가지 방법이 있습니다.

1. 프로그래밍 방식
2. Microsoft Azure 웹 서비스 포털을 사용합니다.

### <a name="programmatically-add-an-endpoint"></a>프로그래밍 방식으로 끝점을 추가합니다.
이 [GitHub 리포지토리](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs)에서 제공된 샘플 코드를 사용하여 점수 매기기 끝점을 추가할 수 있습니다.

### <a name="use-the-microsoft-azure-web-services-portal-to-add-an-endpoint"></a>Microsoft Azure 웹 서비스 포털을 사용하여 끝점을 추가합니다.
1. Machine Learning Studio의 왼쪽 탐색 열에서 Web Services를 클릭합니다.
2. 웹 서비스 대시보드 아래쪽에서 **끝점 관리 미리 보기**를 클릭합니다.
3. **추가**를 클릭합니다.
4. 새 끝점에 대한 이름 및 설명을 입력합니다. 로깅 수준 및 예제 데이터 사용 여부를 선택합니다. 자세한 내용은 [Machine Learning 웹 서비스에 대해 로깅 사용](web-services-logging.md)을 참조하세요.

## <a name="update-the-added-endpoints-trained-model"></a>추가된 끝점의 학습된 모델 업데이트
재학습 프로세스를 완료하려면 추가한 새 끝점의 학습된 모델을 업데이트해야 합니다.

샘플 코드를 사용하여 끝점을 추가한 경우 출력에서 *HelpLocationURL* 값으로 식별되는 도움말 URL의 위치를 포함합니다.

경로 URL을 검색하려면:

1. URL을 복사하여 브라우저에 붙여 넣습니다.
2. 업데이트 리소스 링크를 클릭합니다.
3. PATCH 요청의 POST URL을 복사합니다. 예: 
   
     패치 URL: https://management.azureml.net/workspaces/00bf70534500b34rebfa1843d6/webservices/af3er32ad393852f9b30ac9a35b/endpoints/newendpoint2

이제 학습된 모델을 사용하여 이전에 만든 점수 매기기 끝점을 업데이트할 수 있습니다.

다음 샘플 코드는 *BaseLocation*, *RelativeLocation*, *SasBlobToken* 및 PATCH URL을 사용하여 끝점을 업데이트하는 방법을 보여 줍니다.

    private async Task OverwriteModel()
    {
        var resourceLocations = new
        {
            Resources = new[]
            {
                new
                {
                    Name = "Census Model [trained model]",
                    Location = new AzureBlobDataReference()
                    {
                        BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
                        RelativeLocation = "your endpoint relative location", //from the output, for example: “experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner”
                        SasBlobToken = "your endpoint SAS blob token" //from the output, for example: “?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl”
                    }
                }
            }
        };

        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

            using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
            {
                request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
                HttpResponseMessage response = await client.SendAsync(request);

                if (!response.IsSuccessStatusCode)
                {
                    await WriteFailedResponse(response);
                }

                // Do what you want with a successful response here.
            }
        }
    }

호출에 대한 *apiKey* 및 *endpointUrl*은 끝점 대시보드에서 가져올 수 있습니다.

*리소스*의 *Name* 매개 변수의 값은 예측 실험의 저장된 학습된 모델의 리소스 이름과 일치해야 합니다. 리소스 이름을 가져오려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **Machine Learning**을 클릭합니다.
3. 이름 아래에서 작업 영역을 클릭한 다음 **웹 서비스**를 클릭합니다.
4. 이름 아래에서 **Census Model[예측 exp.]**을 클릭합니다.
5. 추가한 새 끝점을 클릭합니다.
6. 끝점 대시보드에서 **업데이트 리소스**를 클릭합니다.
7. 웹 서비스에 대한 업데이트 리소스 API 설명서 페이지에서 **업데이트할 수 있는 리소스** 아래에 **리소스 이름**이 있습니다.

끝점 업데이트를 완료하기 전에 SAS 토큰이 만료되는 경우 작업 ID로 GET을 수행하여 새 토큰을 가져와야 합니다.

코드가 성공적으로 실행된 경우 새 끝점은 다시 학습된 모델을 사용하여 약 30초 내에 시작해야 합니다.

## <a name="summary"></a>요약
재학습 API를 사용하여 다음과 같은 시나리오를 활성화하는 예측 웹 서비스의 학습된 모델을 업데이트할 수 있습니다.

* 새 데이터를 사용하는 주기적 모델 재학습.
* 자신의 데이터를 사용하여 모델을 다시 학습할 수 있도록 하는 것을 목표로 고객에게 모델 배포.

## <a name="next-steps"></a>다음 단계
[Azure Machine Learning 기존 웹 서비스의 재학습 문제 해결](troubleshooting-retraining-models.md)

