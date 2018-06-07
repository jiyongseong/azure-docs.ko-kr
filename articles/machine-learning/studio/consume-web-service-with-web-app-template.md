---
title: 웹앱 템플릿을 사용한 Machine Learning 웹 서비스 사용 | Microsoft Docs
description: Azure Marketplace에서 웹 앱을 사용하여 Azure Machine Learning의 예측 웹 서비스를 사용합니다.
keywords: 웹 서비스, 운영, REST API, 기계 학습
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: e0d71683-61b9-4675-8df5-09ddc2f0d92d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 81b89a1f8a053fd3b1d0d6a0a1bcc5c67d2ba728
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="consume-an-azure-machine-learning-web-service-by-using-a-web-app-template"></a>웹앱 템플릿을 사용한 Azure Machine Learning 웹 서비스 사용

다음을 사용하여 예측 모델을 개발하고 Azure 웹 서비스로 배포할 수 있습니다.
- Azure Machine Learning Studio
- Python 또는 R 등의 도구 

그런 후 REST API를 사용하여 조작 가능한 모델에 액세스할 수 있습니다.

REST API를 사용하고 웹 서비스에 액세스하는 방법은 많습니다. 예를 들어, 웹 서비스를 배포할 때 생성된 샘플 코드를 사용하여 C#, R 또는 Python에서 응용 프로그램을 작성할 수 있습니다. (샘플 코드는 [Machine Learning 웹 서비스 포털](https://services.azureml.net/quickstart) 또는 Machine Learning Studio의 웹 서비스 대시보드에서 사용 가능합니다.) 또는 동시에 생성된 샘플 Microsoft Excel 통합 문서를 사용할 수 있습니다.

그러나 웹 서비스를 액세스하는 가장 빠르고 쉬운 방법은 [Azure Marketplace](https://azure.microsoft.com/marketplace/web-applications/all/)에서 사용 가능한 웹앱 템플릿을 통한 것입니다.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="azure-machine-learning-web-app-templates"></a>Azure Machine Learning 웹앱 템플릿
Azure Marketplace에서 사용할 수 있는 웹 앱 템플릿은 웹 서비스의 입력 데이터 및 예상 결과를 알고 있는 사용자 지정 웹 앱을 구축할 수 있습니다. 따라서 웹 앱이 웹 서비스 및 데이터에 액세스하도록 하기만 하면 나머지 작업은 템플릿이 수행합니다.

두 가지 템플릿을 사용할 수 있습니다.

* [Azure ML 요청-응답 서비스 웹 앱 템플릿](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
* [Azure ML 일괄 처리 실행 서비스 웹 앱 템플릿](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

각 템플릿은 웹 서비스용 API URI 및 키를 사용하여 샘플 ASP.NET 응용 프로그램을 만듭니다. 그런 후 응용 프로그램을 Azure에 웹 사이트로 배포합니다. 

RRS(요청-응답 서비스) 템플릿은 단일 결과를 가져오기 위해 데이터의 단일 행을 웹 서비스에 보낼 수 있도록 웹앱을 만듭니다. BES(일괄 처리 실행 서비스) 템플릿은 여러 결과를 가져오기 위해 많은 데이터의 행을 보낼 수 있도록 웹앱을 만듭니다.

이러한 템플릿을 사용하기 위해 코딩 작업이 필요하지 않습니다. API 키 및 URI를 제공하면 템플릿이 응용 프로그램을 빌드합니다.

웹 서비스에 대한 API 키 및 요청 URI를 가져오려면

1. [웹 서비스 포털](https://services.azureml.net/quickstart)에서 위쪽의 **웹 서비스**를 선택합니다. 또는 기존 웹 서비스의 경우 **기존 웹 서비스**를 선택합니다.
2. 액세스하려는 웹 서비스를 선택합니다.
3. 기존 웹 서비스의 경우 액세스하려는 끝점을 선택합니다.
4. 위쪽의 **소비자**를 선택합니다.
5. 기본 또는 보조 키를 복사하고 저장합니다.
6. RRS 템플릿을 만드는 경우 **요청-응답** URI를 복사하고 저장합니다. BES 템플릿을 만드는 경우 **Batch 요청** URI를 복사하고 저장합니다.


## <a name="how-to-use-the-request-response-service-template"></a>요청-응답 서비스 템플릿을 사용하는 방법
다음 다이어그램에 표시된 것처럼 이 단계를 따라 RRS 웹앱 템플릿을 사용합니다.

![RRS 웹 템플릿을 사용하는 프로세스][image1]


<!--    ![API Key][image3] -->

<!-- This value will look like this:
   
        https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true
   
    ![Request URI][image4] -->

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **새로 만들기**를 선택하고 **Azure ML 요청-응답 서비스 웹앱**을 선택한 후 **만들기**를 선택합니다. 
3. **만들기** 창에서 다음을 수행합니다.
   
   * 웹 앱에 고유한 이름을 지정합니다. 웹앱의 URL은 이 이름과 **.azurewebsites.net**으로 구성됩니다. 예는 **http://carprediction.azurewebsites.net**입니다.
   * Azure 구독과 웹 서비스가 실행되는 서비스를 선택합니다.
   * **만들기**를 선택합니다.
     
   ![웹앱 만들기][image5]

4. Azure가 웹앱 배포를 완료하면 Azure의 웹앱 설정 페이지의 **URL**을 선택하거나 웹 브라우저에 URL을 입력합니다. 예를 들어 **http://carprediction.azurewebsites.net**을 입력합니다.
5. 웹앱을 처음 실행할 때 **API 게시 URL** 및 **API 키**를 요청합니다. 이전에 저장한 값을 입력합니다(요청 URI 및 API 키 각각). **제출**을 선택합니다.
     
   ![Post URI 및 API 키 입력][image6]

6. 웹앱은 현재 웹 서비스 설정과 함께 **웹앱 구성** 페이지를 표시합니다. 여기서 웹앱에서 사용되는 설정을 변경할 수 있습니다.
   
   > [!NOTE]
   > 여기서 설정을 변경하면 이 웹 앱에 대한 설정만 변경됩니다. 웹 서비스의 기본 설정을 변경되지 않습니다. 예를 들어, 여기에서 **설명**의 텍스트를 변경하면 Machine Learning Studio의 웹 서비스 대시보드에 표시되는 설명은 변경되지 않습니다.
   > 
   > 
   
    완료되면 **변경 내용 저장**을 선택한 후 **홈페이지로 이동**을 선택합니다.

7. 홈페이지에서 웹 서비스에 전송하는 값을 입력할 수 있습니다. 완료되면 **전송**을 선택하고 결과가 반환됩니다.

**구성** 페이지로 반환하려는 경우, 웹앱의 **setting.aspx** 페이지로 이동합니다. 예를 들어 **http://carprediction.azurewebsites.net/setting.aspx**로 이동합니다. API 키를 다시 입력하라는 메시지가 나타납니다. 페이지에 액세스하고 설정을 업데이트하려면 이 키를 다시 입력해야 합니다.

웹앱처럼 Azure 포털에서 웹앱을 중지하거나, 다시 시작하거나, 삭제할 수 있습니다. 실행되는 동안, 홈 웹 주소로 이동하여 새 값을 입력할 수 있습니다.

## <a name="how-to-use-the-batch-execution-service-template"></a>Batch 실행 서비스 템플릿을 사용하는 방법
RR 템플릿과 동일한 방식으로 BES 웹앱 템플릿을 사용할 수 있습니다. 차이점은 만든 웹앱을 사용해서 여러 행의 데이터를 제출하고 여러 결과를 수신할 수 있다는 것입니다.

일괄 처리 실행 웹 서비스에 대한 입력 값은 Azure Storage 또는 로컬 파일에서 가져올 수 있습니다. 결과는 Azure Storage 컨테이너에 저장됩니다. 따라서 웹앱이 반환하는 결과를 포함하려면 Azure Storage 컨테이너가 필요합니다. 입력 데이터를 사용할 준비가 되어야 합니다.

![BES 웹 템플릿을 사용하는 프로세스][image2]

1. RRS 템플릿의 경우와 동일한 절차를 따라 BES 웹앱을 만듭니다. 그렇지만 이 경우에는 [Azure ML 일괄 처리 실행 서비스 웹앱 템플릿](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)으로 이동하여 Azure Marketplace의 BES 템플릿을 엽니다. **웹앱 만들기**를 선택합니다.

2. 결과를 저장하려는 위치를 지정하려면 웹앱 홈페이지에 대상 컨테이너 정보를 입력합니다. 또한 웹앱이 로컬 파일 또는 Azure Storage 컨테이너에 있는 입력 값을 가져올 수 있는 위치를 지정합니다.
   **제출**을 선택합니다.
   
   ![저장소 정보][image7]

웹앱은 작업 상태가 포함된 페이지를 표시합니다. 작업이 완료되면 Azure Blob 저장소에서 결과의 위치를 알게 됩니다. 또한 결과를 로컬 파일에 다운로드할 옵션이 있습니다.

## <a name="for-more-information"></a>Blob에 대한 자세한 내용은
다음에 대한 자세한 정보:

* Machine Learning Studio로 기계 학습 실험 만들기는 [Azure Machine Learning Studio에서 첫 번째 실험 만들기](create-experiment.md)를 참조하세요.
* 웹 서비스로서 기계 학습 실험을 배포하는 방법은 [Azure Machine Learning 웹 서비스 배포](publish-a-machine-learning-web-service.md)를 참조하세요.
* 웹 서비스에 액세스하는 다른 방법은 [Azure Machine Learning 웹 서비스 사용 방법](consume-web-services.md)을 참조하세요.

[image1]: media/consume-web-service-with-web-app-template/rrs-web-template-flow.png
[image2]: media/consume-web-service-with-web-app-template/bes-web-template-flow.png
[image3]: media/consume-web-service-with-web-app-template/api-key.png
[image4]: media/consume-web-service-with-web-app-template/post-uri.png
[image5]: media/consume-web-service-with-web-app-template/create-web-app.png
[image6]: media/consume-web-service-with-web-app-template/web-service-info.png
[image7]: media/consume-web-service-with-web-app-template/storage.png
