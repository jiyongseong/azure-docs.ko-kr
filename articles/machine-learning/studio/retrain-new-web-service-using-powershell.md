---
title: PowerShell을 사용하여 새 Azure Machine Learning 웹 서비스 다시 학습 | Microsoft Docs
description: Machine Learning Management PowerShell cmdlet를 사용하여 Azure Machine Learning에서 프로그래밍 방식으로 모델을 다시 학습하고 새로 학습된 모델을 사용하도록 웹 서비스를 업데이트하는 방법을 알아봅니다.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 3953a398-6174-4d2d-8bbd-e55cf1639415
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.openlocfilehash: 865243a10bd5043ca04c392c043b37772271f68f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="retrain-a-new-resource-manager-based-web-service-using-the-machine-learning-management-powershell-cmdlets"></a>Machine Learning 관리 PowerShell cmdlet을 사용하여 새 리소스 관리자 기반 웹 서비스를 다시 학습
새 웹 서비스를 다시 교육하는 경우 새로 학습된 모델을 참조하여 예측 웹 서비스 정의를 업데이트합니다.

## <a name="prerequisites"></a>필수 조건
학습 실험 및 예측 실험을 [프로그래밍 방식으로 Machine Learning 모델 재학습](retrain-models-programmatically.md)에서 보듯이 설정해야 합니다.

> [!IMPORTANT]
> 예측 실험을 Azure Resource Manager(신규) 기반 Machine Learning 웹 서비스로 배포해야 합니다.
> 새 웹 서비스를 배포하려면 웹 서비스를 배포하려는 구독에 충분한 권한이 있어야 합니다. 자세한 내용은 [Azure Machine Learning 웹 서비스 포털에서 웹 서비스 관리](manage-new-webservice.md)를 참조하세요.

웹 서비스 배포에 대한 자세한 내용은 [Azure Machine Learning 웹 서비스 배포](publish-a-machine-learning-web-service.md)를 참조하세요.

이 프로세스는 Azure Machine Learning Cmdlets 설치가 필요합니다. Machine Learning cmdlet 설치에 관한 정보는 MSDN의 [Azure Machine Learning Cmdlets](https://msdn.microsoft.com/library/azure/mt767952.aspx) 참조를 참조하세요.

다음 정보를 재학습 출력에서 복사했습니다.

* BaseLocation
* RelativeLocation

수행할 단계는 다음과 같습니다.

1. Azure Resource Manager 계정으로 로그인합니다.
2. 웹 서비스 정의 가져오기
3. JSON으로 웹 서비스 정의 내보내기
4. JSON에서 ilearner blob에 대한 참조를 업데이트합니다.
5. JSON을 웹 서비스 정의로 가져오기
6. 웹 서비스를 새 웹 서비스 정의로 업데이트합니다.

## <a name="sign-in-to-your-azure-resource-manager-account"></a>Azure Resource Manager 계정으로 로그인합니다.
먼저 [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) cmdlet을 사용하여 PowerShell 환경 내에서 Azure 계정에 로그인해야 합니다.

## <a name="get-the-web-service-definition"></a>웹 서비스 정의 가져오기
다음으로 [Get AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) cmdlet을 호출하여 웹 서비스를 가져옵니다. 웹 서비스 정의는 웹 서비스 학습된 모델의 내부 표현이며 직접 수정할 수는 없습니다. 학습 실험이 아닌 예측 실험에 대한 웹 서비스 정의를 검색하고 있는지 확인합니다.

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

기존 웹 서비스의 리소스 그룹 이름을 결정하려면 구독 중인 웹 서비스를 표시하도록 매개 변수 없이 Get-AzureRmMlWebService cmdlet을 실행합니다. 웹 서비스를 찾은 다음 웹 서비스 ID를 살펴봅니다. 리소스 그룹 이름은 ID의 네 번째 요소로 *resourceGroups* 요소 바로 뒤에 있습니다. 다음 예제에서 리소스 그룹 이름은 Default-MachineLearning-SouthCentralUS입니다.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

또는 기존 웹 서비스의 리소스 그룹 이름을 결정하려면 Microsoft Azure Machine Learning Web Services 포털에 로그인합니다. 웹 서비스를 선택합니다. 리소스 그룹 이름은 웹 서비스 URL의 다섯 번째 요소로 *resourceGroups* 요소 바로 뒤에 있습니다. 다음 예제에서 리소스 그룹 이름은 Default-MachineLearning-SouthCentralUS입니다.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-as-json"></a>JSON으로 웹 서비스 정의 내보내기
새로 Trained Model을 사용하여 새로 학습된 모델에 대한 정의를 수정하려면 먼저 [Export-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767935.aspx) cmdlet을 사용하여 JSON 형식 파일에 내보내기해야 합니다.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob-in-the-json"></a>JSON에서 ilearner blob에 대한 참조를 업데이트합니다.
자산에서 [학습된 모델]을 찾아, ilearner Blob의 URI와 함께 *locationInfo* 노드의 *uri* 값을 업데이트합니다. URI는 BES 재학습 호출의 출력에서 *BaseLocation* 및 *RelativeLocation*을 조합하여 만듭니다. 이렇게 새로 학습된 모델을 참조하는 경로를 업데이트합니다.

     "asset3": {
        "name": "Retrain Samp.le [trained model]",
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

## <a name="import-the-json-into-a-web-service-definition"></a>JSON을 웹 서비스 정의로 가져오기
수정된 JSON 파일을 웹 서비스 정의를 업데이트하는 데 사용할 수 있는 웹 서비스 정의로 변환하려면 [Import-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx) cmdlet을 사용해야 합니다.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service-with-new-web-service-definition"></a>웹 서비스를 새 웹 서비스 정의로 업데이트합니다.
마지막으로, [Update-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx) cmdlet를 사용하여 웹 서비스 정의를 업데이트합니다.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'  -ServiceUpdates $wsd

## <a name="summary"></a>요약
Machine Learning PowerShell Management cmdlet을 사용하여 다음과 같은 시나리오를 활성화하는 예측 웹 서비스의 학습된 모델을 업데이트할 수 있습니다.

* 새 데이터를 사용하는 주기적 모델 재학습.
* 자신의 데이터를 사용하여 모델을 다시 학습할 수 있도록 하는 것을 목표로 고객에게 모델 배포.

