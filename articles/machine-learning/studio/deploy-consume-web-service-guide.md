---
title: 'Azure Machine Learning 웹 서비스: 배포 및 사용 | Microsoft Docs'
description: 웹 서비스 배포 및 사용을 위한 리소스입니다.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 47635376-d1f4-4ea4-a6af-bd1f99f69a69
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: ac2d9620cfb6b3bc12bd3ff664d03d342efc5791
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="azure-machine-learning-web-services-deployment-and-consumption"></a>Azure Machine Learning 웹 서비스: 배포 및 사용
Azure Machine Learning을 통해 웹 서비스로 기계 학습 워크플로 및 모델을 배포할 수 있습니다. 그런 다음 이러한 웹 서비스를 실시간으로 또는 배치 모드로 예측을 수행하도록 인터넷을 통해 응용 프로그램에서 기계 학습 모델을 호출하는 데 사용할 수 있습니다. 웹 서비스는 RESTFul이므로 .NET 및 Java와 같은 다양한 프로그래밍 언어 및 플랫폼과 Excel과 같은 응용 프로그램에서 호출될 수 있습니다.

다음 섹션에서는 시작하는 데 도움이 되는 연습, 코드 및 설명서에 대한 링크를 제공합니다.

## <a name="deploy-a-web-service"></a>웹 서비스 배포

### <a name="with-azure-machine-learning-studio"></a>Azure Machine Learning Studio 사용
Machine Learning Studio 및 Microsoft Azure Machine Learning 웹 서비스 포털은 코드를 작성할 필요 없이 웹 서비스를 배포 및 관리하도록 도와줍니다.

다음 링크는 새 웹 서비스를 배포하는 방법에 대한 일반 정보를 제공합니다.

* Azure Resource Manager를 기준으로 하는 새 웹 서비스를 배포하는 방법에 대한 개요는 [새 웹 서비스 배포](publish-a-machine-learning-web-service.md)를 참조하세요.
* 웹 서비스를 배포하는 방법에 대한 연습은 [Azure Machine Learning 웹 서비스 배포](publish-a-machine-learning-web-service.md)를 참조하세요.
* 웹 서비스를 만들고 배포하는 방법에 대한 전체 연습은 [연습 1 단계: Machine Learning 작업 영역 만들기](walkthrough-1-create-ml-workspace.md)를 참조하세요.
* 웹 서비스 배포의 특정 예제는 다음을 참조하세요.

  * [연습 5단계: Azure Machine Learning 웹 서비스 배포](walkthrough-5-publish-web-service.md)
  * [여러 지역에 웹 서비스를 배포하는 방법](how-to-deploy-to-multiple-regions.md)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>웹 서비스 리소스 공급자 API(Azure Resource Manager API) 사용
웹 서비스에 대한 Azure Machine Learning 리소스 공급자는 REST API 호출을 사용하여 웹 서비스의 배포 및 관리를 활성화합니다. 자세한 내용은 MSDN의 [Machine Learning 웹 서비스(REST)](/rest/api/machinelearning/index) 참조를 참조하세요.

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->


### <a name="with-powershell-cmdlets"></a>PowerShell cmdlet 사용
웹 서비스에 대한 Azure Machine Learning 리소스 공급자는 PowerShell cmdlet을 사용하여 웹 서비스의 배포 및 관리를 활성화합니다.

cmdlet을 사용하려면 먼저 [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) cmdlet을 사용하여 PowerShell 환경 내에서 Azure 계정에 로그인해야 합니다. Resource Manager를 기준으로 하는 PowerShell 명령을 호출하는 방법에 익숙하지 않은 경우 [Azure Resource Manager로 Azure PowerShell 사용](../../azure-resource-manager/powershell-azure-resource-manager.md)을 참조하세요.

예측 실험을 내보내려면 이 [샘플 코드](https://github.com/ritwik20/AzureML-WebServices)를 사용합니다. 코드에서 .exe 파일을 만든 후 다음을 입력할 수 있습니다.

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

응용 프로그램을 실행하면 웹 서비스 JSON 템플릿이 만들어집니다. 웹 서비스를 배포하는 데 템플릿을 사용하려면 다음 정보를 추가해야 합니다.

* Storage 계정 이름 및 키

    [Azure Portal](https://portal.azure.com/)에서 저장소 계정 이름 및 키를 가져올 수 있습니다.
* 약정 계획 ID

    로그인하고 계획 이름을 클릭하여 [Azure Machine Learning 웹 서비스](https://services.azureml.net) 포털에서 계획 ID를 얻을 수 있습니다.

*MachineLearningWorkspace* 노드와 동일한 수준에서 *Properties* 노드의 자식으로 JSON 템플릿에 추가합니다.

예를 들면 다음과 같습니다.

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

추가 세부 정보는 다음 문서 및 샘플 코드를 참조하세요.

* [Azure Machine Learning Cmdlet](https://msdn.microsoft.com/library/azure/mt767952.aspx) 참조
* GitHub의 샘플 [연습](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt)

## <a name="consume-the-web-services"></a>웹 서비스 사용
### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Azure Machine Learning 웹 서비스 UI에서(테스트)
Azure Machine Learning 웹 서비스 포털에서 웹 서비스를 테스트할 수 있습니다. 여기에는 RR(요청-응답 서비스) 및 BES(일괄 처리 실행 서비스) 인터페이스가 포함됩니다.

* [새 웹 서비스 배포](publish-a-machine-learning-web-service.md)
* [Azure Machine Learning 웹 서비스 배포](publish-a-machine-learning-web-service.md)
* [연습 5단계: Azure Machine Learning 웹 서비스 배포](walkthrough-5-publish-web-service.md)

### <a name="from-excel"></a>엑셀에서
웹 서비스를 사용하는 Excel 템플릿을 다운로드할 수 있습니다.

* [Excel에서 Azure Machine Learning 웹 서비스 사용](consuming-from-excel.md)
* [Azure Machine Learning 웹 서비스용 Excel 추가 기능](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>REST 기반 클라이언트에서
Azure Machine Learning 웹 서비스는 RESTful API입니다. .NET, Python, R, Java 등과 같은 다양한 플랫폼에서 이러한 API를 사용할 수 있습니다. [Microsoft Azure Machine Learning 웹 서비스 포털](https://services.azureml.net)의 웹 서비스에 대한 **사용** 페이지에는 시작하는 데 도움을 줄 수 있는 샘플 코드가 있습니다. 자세한 내용은 [Azure Machine Learning 웹 서비스 사용 방법](consume-web-services.md)을 참조하세요.
