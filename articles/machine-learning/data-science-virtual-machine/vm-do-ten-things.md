---
title: Azure의 데이터 과학 Virtual Machine에서 수행할 수 있는 10가지 작업 | Microsoft 문서
description: 데이터 과학 Virtual Machine에서 다양한 데이터 탐색 및 모델링 작업을 수행합니다.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.assetid: 145dfe3e-2bd2-478f-9b6e-99d97d789c62
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/04/2017
ms.author: gokuma
ms.openlocfilehash: a5f0961a99eac805e82cbc5e5d61c485f8661ba0
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="ten-things-you-can-do-on-the-windows-data-science-virtual-machine"></a>Windows 데이터 과학 Virtual Machine으로 할 수 있는 10가지 작업

Windows DSVM(데이터 과학 Virtual Machine)은 다양한 데이터 탐색 및 모델링 작업을 수행할 수 있는 강력한 데이터 과학 개발 환경입니다. 이 환경에는 온-프레미스, 클라우드 또는 하이브리드 배포에 대한 간편하고 신속하게 분석을 시작할 수 있는 여러 대중적인 분석 도구가 기본적으로 내장되고 번들로 제공됩니다. DSVM은 여러 Azure 서비스와 긴밀하게 연동하며 Azure의 Azure SQL Data Warehouse, Azure Data Lake, Azure Storage 또는 Azure Cosmos DB에 저장된 데이터를 읽고 처리할 수 있습니다. 또한 Azure Machine Learning 및 Azure Data Factory와 같은 기타 분석 도구를 활용할 수 있습니다.

이 문서에서는 DSVM을 사용하여 다양한 데이터 과학 작업을 수행하고 다른 Azure 서비스와 상호 작용하는 방법을 배울 수 있습니다. 다음은 DSVM에서 수행할 수 있는 작업 중 일부입니다.

1. Microsoft ML 서버, Python을 사용하여 DSVM에서 로컬로 데이터 탐색 및 모델 개발
2. Jupyter Notebook에서 Python 2, Python 3 그리고 성능 위주로 설계된 R의 엔터프라이즈급 버전인 Microsoft R을 사용하여 브라우저에서 데이터 실험
3. 클라이언트 응용 프로그램이 간단한 웹 서비스 인터페이스를 사용하여 모델에 액세스할 수 있도록 Azure Machine Learning에서 R 및 Python을 사용하여 구축된 모델 배포
4. Azure Portal 또는 Powershell을 사용하여 Azure 리소스 관리
5. Azure File Storage를 DSVM에 탑재 가능한 드라이브로 만들어 저장소 공간을 확장하고 전체 팀에서 대규모 데이터 집합/코드 공유
6. GitHub를 사용하여 팀과 코드를 공유하고 사전 설치된 Git 클라이언트(Git Bash Git GUI)를 사용하여 리포지토리에 액세스
7. Azure Blob Storage, Azure Data Lake, Azure HDInsight(Hadoop), Azure Cosmos DB, Azure SQL Data Warehouse, 데이터베이스 등의 다양한 Azure 데이터 및 분석 서비스에 액세스
8. DSVM에 사전 설치된 Power BI Desktop을 사용하여 보고서 및 대시보드를 구축하여 클라우드에 배포
9. 프로젝트 요구 사항에 맞게 DSVM을 동적으로 확장
10. 가상 컴퓨터에 추가 도구 설치   

> [!NOTE]
> 이 문서에 나열된 추가 데이터 저장소 및 분석 서비스의 대부분에 대해서는 추가 사용 요금이 적용됩니다. 자세한 내용은 [Azure 가격 책정](https://azure.microsoft.com/pricing/) 페이지를 참조하세요.
> 
> 

**필수 구성 요소**

* Azure 구독이 필요합니다. [여기](https://azure.microsoft.com/free/)서 평가판에 등록할 수 있습니다.
* Azure 포털에서 데이터 과학 Virtual Machine을 프로비전하는 방법에 대한 지침은 [가상 머신 만들기](https://portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm)에서 확인할 수 있습니다.

## <a name="1-explore-data-and-develop-models-using-microsoft-ml-server-or-python"></a>1. Microsoft ML 서버 또는 Python을 사용하여 데이터 탐색 및 모델 개발
R 및 Python 같은 언어를 사용하여 DSVM에서 바로 데이터를 분석할 수 있습니다.

R의 경우 시작 메뉴나 바탕 화면에서, 또는 Visual Studio용 R 도구에서 찾을 수 있는 RStudio 같은 IDE를 사용할 수 있습니다. Microsoft는 병렬로 청크된 분석을 수행하여 확장 가능한 분석을 지원하고 허용된 메모리 크기를 초과하는 데이터를 분석할 수 있도록 오픈 소스/CRAN-R 기반의 추가 라이브러리를 제공합니다. 

Python의 경우 PTVS(Python Tools for Visual Studio) 확장 기능이 사전 설치된 Visual Studio Community Edition 같은 IDE를 사용할 수 있습니다. 기본적으로 Python 3.6에서만 루트 conda 환경이 PTVS에서 구성됩니다. Anaconda Python 2.7을 사용하려면 다음 단계를 수행해야 합니다.

* **도구** -> **Python 도구** -> **Python 환경**으로 이동한 다음 Visual Studio 2015 Community Edition에서 "**+ 사용자 지정**"을 클릭하여 각 버전에 대해 사용자 지정 환경을 만듭니다.
* 설명을 입력하고 Anaconda Python 2.7에 대해 환경 접두사 경로를 *c:\anaconda\envs\python2*로 설정합니다.
* **자동 감지**를 클릭한 다음 **적용**을 클릭하여 환경을 저장합니다.

Visual Studio에서 사용자 지정 환경 설정이 다음과 같이 표시됩니다.

![PTVS 설치](./media/vm-do-ten-things/PTVSSetup.png)

Python 환경을 만드는 방법에 대한 자세한 내용은 [PTVS 설명서](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it) 를 참조하세요.

이제 새 Python 프로젝트를 만들 수 있도록 설정되어 있습니다. **파일** -> **새로 만들기** -> **프로젝트** -> **Python**으로 이동하고 작성하는 Python 응용 프로그램의 종류를 선택합니다. 현재 프로젝트에 대한 Python 환경을 원하는 버전(Anaconda 2.7 또는 3.6)으로 설정할 수 있습니다. **Python 환경**을 마우스 오른쪽 단추로 클릭하고 **Python 환경 추가/제거**를 선택한 다음, 원하는 환경을 선택합니다. 제품 [설명서](https://github.com/Microsoft/PTVS/wiki) 페이지에서 PTVS 작업에 대한 자세한 정보를 찾을 수 있습니다.

## <a name="2-using-a-jupyter-notebook-to-explore-and-model-your-data-with-python-or-r"></a>2. Jupyter Notebook에서 Python 또는 R을 사용하여 데이터 탐색 및 모델링
Jupyter Notebook은 데이터 탐색 및 모델링을 위한 브라우저 기반 "IDE"를 제공하는 강력한 환경입니다. Jupyter Notebook에서 Python 2, Python 3 또는 R(오픈 소스 및 Microsoft R 서버 모두)을 사용할 수 있습니다.

Jupyter 노트북을 시작하려면 **Jupyter 노트북**이라는 시작 메뉴 아이콘/바탕 화면 아이콘을 클릭합니다. DSVM 명령 프롬프트에서 기존 노트북이 있거나 새 노트북을 만들려는 디렉터리에서 ```jupyter notebook``` 명령을 실행할 수도 있습니다.  

Jupyter를 시작하면 DSVM에 사전 패키지된 예제 노트북 몇 개가 포함된 디렉터리가 보일 것입니다. 이제 다음을 수행할 수 있습니다.

* 해당 노트북을 클릭하면 코드가 보입니다.
* **Shift+Enter**를 눌러 각 셀을 실행합니다.
* **셀** -> **실행**을 클릭하여 전체 노트북을 실행합니다.
* Jupyter 아이콘(왼쪽 상단 모서리)을 클릭하고 오른쪽에 있는 **새로 만들기** 단추를 클릭한 다음 노트북 언어(커널이라고도 함)를 선택하여 새 노트북을 만들 수 있습니다.   

> [!NOTE]
> 현재, Jupyter에서 Python 2.7, Python 3.6, R, Julia 및 PySpark 커널이 지원됩니다. R 커널은 오픈 소스 R 뿐만 아니라, 높은 성능의 Microsoft R에서 프로그래밍할 수 있도록 지원합니다.   
> 
> 

Notebook에 액세스한 다음에는 데이터를 탐색하고, 모델을 구축하고, 선택한 라이브러리로 모델을 테스트할 수 있습니다.

## <a name="3-build-models-using-r-or-python-and-operationalize-them-using-azure-machine-learning"></a>3. R 또는 Python을 사용하여 모델을 구축하고 Azure Machine Learning을 사용하여 운영
모델을 구축하고 유효성을 검사한 후에는 일반적으로 프로덕션 환경에 모델을 배포합니다. 이렇게 하면 클라이언트 응용 프로그램에서 실시간으로 또는 배치 모드 기준으로 예측 모델을 호출할 수 있습니다. Azure Machine Learning은 R 또는 Python에서 구축된 모델을 운영하는 메커니즘을 제공합니다.

Azure Machine Learning에서 모델을 운영하면 클라이언트가 입력 매개 변수를 전달하고 그에 대한 출력으로 모델로부터 예측을 수신하는 REST 호출을 만들 수 있도록 웹 서비스가 노출됩니다.   

> [!NOTE]
> 아직 Azure Machine Learning에 등록하지 않은 경우 [Azure Machine Learning Studio](https://studio.azureml.net/) 홈페이지에서 "시작"을 클릭하여 무료 작업 영역 또는 표준 작업 영역을 얻을 수 있습니다.   
> 
> 

### <a name="build-and-operationalize-python-models"></a>Python 모델 구축 및 운영
SciKit-학습 라이브러리를 사용하여 간단한 모델을 작성하는 Python Jupyter Notebook에서 개발된 코드의 조각은 다음과 같습니다.

    #IRIS classification
    from sklearn import datasets
    from sklearn import svm
    clf = svm.SVC()
    iris = datasets.load_iris()
    X, y = iris.data, iris.target
    clf.fit(X, y)

Azure Machine Learning에 python 모델을 배포하는 데 사용되는 메서드는 함수로 모델의 예측을 래핑하고 Azure Machine Learning 작업 영역 ID, API 키 및 입력을 표시하고 매개 변수를 반환하는 사전 설치된 Azure Machine Learning python 라이브러리에서 제공하는 특성으로 데코레이트합니다.  

    from azureml import services
    @services.publish(workspaceid, auth_token)
    @services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
    @services.returns(int) #0, or 1, or 2
    def predictIris(sep_l, sep_w, pet_l, pet_w):
     inputArray = [sep_l, sep_w, pet_l, pet_w]
    return clf.predict(inputArray)

이제 클라이언트에서 웹 서비스에 대한 호출을 만들 수 있습니다. REST API 요청을 작성하는 간편한 래퍼가 있습니다. 다음은 웹 서비스를 사용하는 샘플 코드입니다.

    # Consume through web service URL and keys
    from azureml import services
    @services.service(url, api_key)
    @services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
    @services.returns(float)
    def IrisPredictor(sep_l, sep_w, pet_l, pet_w):
    pass

    IrisPredictor(3,2,3,4)


> [!NOTE]
> Azure Machine Learning 라이브러리는 현재 Python 2.7에서만 지원됩니다.   
> 
> 

### <a name="build-and-operationalize-r-models"></a>R 모델 구축 및 운영
구축된 R 모델을 Python에서 사용한 방법과 비슷한 방법으로 데이터 과학 Virtual Machine 또는 Azure Machine Learning의 다른 위치에 배포할 수 있습니다. 단계는 다음과 같습니다.

* 작업 영역 ID 및 인증 토큰을 제공하는 settings.json 파일을 만듭니다. 
* 모델의 예측 함수에 대한 래퍼를 작성합니다.
* Azure Machine Learning 라이브러리에서 ```publishWebService``` 를 호출하여 함수 래퍼에 전달합니다.  

Azure Machine Learning에서 모델을 설정, 작성, 게시하고 웹 서비스로 사용하는 데 활용할 수 있는 절차와 코드 조각은 다음과 같습니다.

#### <a name="setup"></a>설정

* 홈 디렉터리 아래의 ```.azureml```이라는 디렉터리 아래에서 settings.json 파일을 만들고 Azure Machine Learning 작업 영역에서 매개 변수를 입력합니다.

settings.json 파일 구조:

    {"workspace":{
    "id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
    "authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
    }}


#### <a name="build-a-model-in-r-and-publish-it-in-azure-machine-learning"></a>R에서 모델을 구축하고 Azure Machine Learning에 게시
    library(AzureML)
    ws <- workspace(config="~/.azureml/settings.json")

    if(!require("lme4")) install.packages("lme4")
    library(lme4)
    set.seed(1)
    train <- sleepstudy[sample(nrow(sleepstudy), 120),]
    m <- lm(Reaction ~ Days + Subject, data = train)

    # Define a prediction function to publish based on the model:
    sleepyPredict <- function(newdata){
          predict(m, newdata=newdata)
    }

    ep <- publishWebService(ws, fun = sleepyPredict, name="sleepy lm", inputSchema = sleepstudy, data.frame=TRUE)

#### <a name="consume-the-model-deployed-in-azure-machine-learning"></a>Azure Machine Learning에 배포된 모델 사용
클라이언트 응용 프로그램의 모델을 사용하기 위해 Azure Machine Learning 라이브러리를 사용하여 끝점을 확인하는 `services` API 호출을 통해 게시된 웹 서비스를 이름으로 조회합니다. 그런 다음 `consume` 함수를 호출하여 예측할 데이터 프레임을 전달합니다.
다음 코드는 모델을 Azure Machine Learning 웹 서비스로 게시하는 데 사용됩니다.

    library(AzureML)
    library(lme4)
    ws <- workspace(config="~/.azureml/settings.json")

    s <-  services(ws, name = "sleepy lm")
    s <- tail(s, 1) # use the last published function, in case of duplicate function names

    ep <- endpoints(ws, s)

    # OK, try this out, and compare with raw data
    ans = consume(ep, sleepstudy)$ans

Azure Machine Learning R 라이브러리에 대한 자세한 정보는 [여기](https://cran.r-project.org/web/packages/AzureML/AzureML.pdf)에서 찾을 수 있습니다.

## <a name="4-administer-your-azure-resources-using-azure-portal-or-powershell"></a>4. Azure 포털 또는 Powershell을 사용하여 Azure 리소스 관리
DSVM을 사용하면 가상 컴퓨터에서 로컬로 분석 솔루션을 구축할 수 있을 뿐만 아니라 Microsoft의 Azure 클라우드 서비스에 액세스할 수 있습니다. Azure는 DSVM에서 관리 및 액세스할 수 있는 여러 가지 계산, 저장소, 데이터 분석 서비스 및 기타 서비스를 제공합니다.

Azure 구독 및 클라우드 리소스를 관리하려는 경우 브라우저에서 [Azure 포털](https://portal.azure.com)로 이동하세요. Azure Powershell을 사용하여 스크립트를 통해 Azure 구독 및 리소스를 관리할 수도 있습니다.
바탕 화면의 바로 가기 또는 시작 메뉴의 "Microsoft Azure Powershell"을 사용하여 Azure Powershell을 실행할 수 있습니다. Windows Powershell 스크립트를 사용하여 Azure 구독 및 리소스를 관리하는 방법에 대한 자세한 내용은 [Microsoft Azure Powershell 설명서](../../powershell-azure-resource-manager.md) 를 참조하세요.

## <a name="5-extend-your-storage-space-with-a-shared-file-system"></a>5. 공유 파일 시스템으로 저장소 공간 확장
데이터 과학자는 팀 내에서 대용량 데이터 집합, 코드 또는 기타 리소스를 공유할 수 있습니다. DSVM 자체에는 약 45GB의 사용 가능한 공간이 있습니다. 저장소를 확장하려면 Azure 파일 서비스를 사용하여 하나 이상의 DSVM 인스턴스에 파일을 탑재하거나 REST API를 통해 액세스하면 됩니다.  [Azure Portal](../../virtual-machines/windows/attach-managed-disk-portal.md) 또는 [Azure Powershell](../../virtual-machines/windows/attach-disk-ps.md)을 사용하여 추가 전용 데이터 디스크를 추가할 수도 있습니다. 

> [!NOTE]
> Azure 파일 서비스 공유의 최대 공간은 5TB이고 개별 파일 크기 제한은 1TB입니다. 
> 
> 

Azure Powershell을 사용하여 Azure 파일 서비스 공유를 만들 수 있습니다. 다음은 Azure PowerShell에서 실행하여 Azure 파일 서비스 공유를 만들 수 있는 스크립트입니다.

    # Authenticate to Azure.
    Connect-AzureRmAccount
    # Select your subscription
    Get-AzureRmSubscription –SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
    # Create a new resource group.
    New-AzureRmResourceGroup -Name <dsvmdatarg>
    # Create a new storage account. You can reuse existing storage account if you wish.
    New-AzureRmStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
    # Set your current working storage account
    Set-AzureRmCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>

    # Create a Azure File Service Share
    $s = New-AzureStorageShare <<teamsharename>>
    # Create a directory under the FIle share. You can give it any name
    New-AzureStorageDirectory -Share $s -Path <directory name>
    # List the share to confirm that everything worked
    Get-AzureStorageFile -Share $s


Azure 파일 공유를 만들었으니 이제 Azure의 모든 가상 머신에 파일을 마운트할 수 있습니다. 대기 시간을 줄이고 데이터 전송 요금이 부과되지 않도록 VM과 저장소 계정을 동일한 Azure 데이터 센터에 둘 것을 강력하게 권장합니다. 다음은 Azure Powershell에서 실행할 수 있는 드라이브를 DSVM에 마운트하는 명령입니다.

    # Get storage key of the storage account that has the Azure file share from Azure portal. Store it securely on the VM to avoid prompted in next command.
    cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

    # Mount the Azure file share as Z: drive on the VM. You can chose another drive letter if you wish
    net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>


이제 VM의 일반적인 드라이브처럼 이 드라이브에 액세스할 수 있습니다.

## <a name="6-share-code-with-your-team-using-github"></a>6. GitHub를 사용하여 팀과 코드 공유
GitHub는 개발자 커뮤니티에서 공유하는 다양한 기술을 사용하는 여러 도구를 위한 수많은 샘플 코드 및 소스를 찾을 수 있는 코드 리포지토리입니다. Github는 코드 파일 버전을 추적하고 저장하는 기술로 Git를 사용합니다. GitHub는 팀의 공유 코드 및 문서를 저장하는 고유의 리포지토리를 만들고, 버전 제어를 구현하고, 코드를 보고 의견을 제시하는 액세스 권한을 제어할 수 있는 플랫폼이기도 합니다. Git 사용에 대한 자세한 내용을 보려면 [GitHub 도움말 페이지](https://help.github.com/)를 방문하세요. 팀과 협력하고, 커뮤니티에서 개발한 코드를 사용하고, 다시 커뮤니티에 코드에 대한 의견을 제시하는 방법 중 하나로 GitHub를 사용할 수 있습니다.

DSVM은 GitHub 리포지토리에 액세스할 수 있는 클라이언트 도구가 이미 GUI와 명령줄에 내장되어 있습니다. Git 및 GitHub를 사용하는 명령줄 도구는 Git Bash입니다. DSVM에 설치된 Visual Studio에는 Git 확장 기능이 있습니다. 시작 메뉴 및 바탕 화면에서 이러한 도구의 시작 아이콘을 찾을 수 있습니다.

GitHub 리포지토리에서 코드를 다운로드하려면 ```git clone``` 명령을 사용합니다. 예를 들어 Microsoft가 현재 디렉터리에 게시한 데이터 과학 리포지토리를 다운로드하려면 ```git-bash```에 액세스한 후 다음 명령을 사용합니다.

    git clone https://github.com/Azure/DataScienceVM.git

Visual Studio에서 동일한 복제 작업을 수행할 수 있습니다. 아래 스크린샷은 Visual Studio에서 Git 및 GitHub 도구에 액세스하는 방법을 보여 줍니다.

![Visual Studio의 Git](./media/vm-do-ten-things/VSGit.PNG)

github.com에서 제공하는 여러 리소스를 통해 Git를 사용하여 GitHub 리포지토리를 작업하는 방법에 대한 자세한 내용을 찾을 수 있습니다. [참고 자료](https://training.github.com/kit/downloads/github-git-cheat-sheet.pdf) 를 보시면 많은 도움이 될 것입니다.

## <a name="7-access-various-azure-data-and-analytics-services"></a>7. 다양한 Azure 데이터 및 분석 서비스에 액세스
### <a name="azure-blob"></a>Azure Blob
Azure Blob은 크고 작은 데이터를 위한 경제적이면서 안정적인 클라우드 저장소입니다. 이 섹션에서는 Azure Blob으로 데이터를 이동하고 Azure Blob에 저장된 데이터에 액세스하는 방법을 설명합니다.

**필수 구성 요소**

* **[Azure 포털](https://portal.azure.com)에서 고유한 Azure Blob 저장소 계정을 만듭니다.**

![Create_Azure_Blob](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* 사전 설치된 명령줄 AzCopy 도구를 ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```에서 찾을 수 있는지 확인합니다. 이 도구를 실행할 때 전체 명령 경로 입력하지 않아도 PATH 환경 변수에 azcopy.exe를 포함하는 디렉터리가 이미 있습니다. AzCopy 도구에 대한 자세한 내용은 [AzCopy 설명서](../../storage/common/storage-use-azcopy.md)를 참조하세요.
* Azure Storage 탐색기 도구를 시작합니다. 이 도구는 [Microsoft Azure Storage 탐색기](http://storageexplorer.com/)에서 다운로드할 수 있습니다. 

![AzureStorageExplorer_v4](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

**VM에서 Azure Blob로 데이터 이동: AzCopy**

로컬 파일과 Blob Storage 간에 데이터를 이동하려면 명령줄 또는 PowerShell에서 AzCopy를 사용합니다.

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

파일이 저장되는 경로를 **C:\myfolder**로, Blob Store 계정 이름을 **mystorageaccount**로, 컨테이너 이름을 **mycontainer**로, Blob Store 액세스 키를 **저장소 계정 키**로 바꿉니다. 저장소 계정 자격 증명은 [Azure 포털](https://portal.azure.com)에서 찾을 수 있습니다.

![StorageAccountCredential_v2](./media/vm-do-ten-things/StorageAccountCredential_v2.png)

PowerShell 또는 명령 프롬프트에서 AzCopy 명령을 실행합니다. 다음은 AzCopy 명령을 사용하는 예입니다.

    # Copy *.sql from local machine to a Azure Blob
    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

    # Copy back all files from Azure Blob container to Local machine

    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S



Azure 명령을 실행하여 AzCopy Blob을 복사하면 잠시 후 Azure Storage 탐색기에 해당 파일이 표시될 것입니다.

![AzCopy_run_finshed_Storage_Explorer_v3](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

**VM에서 Azure Blob로 데이터 이동: Azure Storage 탐색기**

Azure Storage 탐색기를 사용하여 VM의 로컬 파일에서 데이터를 업로드할 수도 있습니다.

* 컨테이너에 데이터를 업로드하려면 대상 컨테이너를 선택하고 **업로드** 단추를 클릭합니다.![Storage 탐색기에서 업로드](./media/vm-do-ten-things/storage-accounts.png)
* **파일** 상자 오른쪽의 **...** 을 클릭하고 파일 시스템에서 업로드할 파일을 하나 이상 선택한 후에 **업로드**를 클릭하여 파일 업로드를 시작합니다.![Blob에 파일 업로드](./media/vm-do-ten-things/upload-files-to-blob.png)

**Azure Blob에서 데이터 읽기: Machine Learning 판독기 모듈**

Azure Machine Learning Studio에서 **데이터 가져오기 모듈**을 사용하여 Blob에서 데이터를 읽을 수 있습니다.

![AML_ReaderBlob_Module_v3](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

**Azure Blob에서 데이터 읽기: Python ODBC**

**BlobService** 라이브러리를 사용하여 Jupyter Notebook 또는 Python 프로그램의 Blob에서 직접 데이터를 읽을 수 있습니다.

먼저 필요한 패키지를 가져옵니다.

    import pandas as pd
    from pandas import Series, DataFrame
    import numpy as np
    import matplotlib.pyplot as plt
    from time import time
    import pyodbc
    import os
    from azure.storage.blob import BlobService
    import tables
    import time
    import zipfile
    import random

그런 다음 Azure Blob 계정 자격 증명을 연결하고 Blob에서 데이터를 읽습니다.

    CONTAINERNAME = 'xxx'
    STORAGEACCOUNTNAME = 'xxxx'
    STORAGEACCOUNTKEY = 'xxxxxxxxxxxxxxxx'
    BLOBNAME = 'nyctaxidataset/nyctaxitrip/trip_data_1.csv'
    localfilename = 'trip_data_1.csv'
    LOCALDIRECTORY = os.getcwd()
    LOCALFILE =  os.path.join(LOCALDIRECTORY, localfilename)

    #download from blob
    t1 = time.time()
    blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILE)
    t2 = time.time()
    print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))

    #unzipping downloaded files if needed
    #with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
    #    z.extractall(LOCALDIRECTORY)

    df1 = pd.read_csv(LOCALFILE, header=0)
    df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
    print 'the size of the data is: %d rows and  %d columns' % df1.shape

데이터 프레임으로 데이터를 읽어 들입니다.

![IPNB_data_readin](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
빅 데이터 분석 작업용의 초대형 리포지토리인 Azure Data Lake Storage는 HDFS(Hadoop 분산 파일 시스템)와 호환됩니다. Hadoop, Spark 및 Azure Data Lake Analytics와 작동합니다. 이 섹션에서는 Azure Data Lake 저장소에 데이터를 이동하고 Azure Data Lake 분석을 사용하여 분석을 실행하는 방법을 알아봅니다.

**필수 구성 요소**

* [Azure 포털](https://portal.azure.com)에서 Azure Data Lake Analytics를 만듭니다.

![Azure_Data_Lake_Create_v2](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* 이 [링크](https://www.microsoft.com/download/details.aspx?id=49504)에서 찾을 수 있는 **Visual Studio**의 **Azure Data Lake 도구**는 이미 가상 머신의 Visual Studio Community Edition에 설치되어 있습니다. Visual Studio를 시작하고 Azure 구독에 로그인하면 Visual Studio의 왼쪽 패널에 Azure Data Lake Analytics 계정 및 저장소가 표시됩니다.

![Azure_Data_Lake_PlugIn_v2](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

**VM에서 Data Lake로 데이터 이동: Azure Data Lake 탐색기**

**Azure Data Lake Explorer** 를 사용하여 Virtual Machine의 로컬 파일에서 Data Lake Storage로 데이터를 업로드할 수 있습니다.

![Azure_Data_Lake_UploadData](./media/vm-do-ten-things/Azure_Data_Lake_UploadData.PNG)

[ADF(Azure Data Factory)](https://azure.microsoft.com/services/data-factory/)를 사용하여 Azure Data Lake와의 데이터 이동을 운용하는 데이터 파이프라인을 구축할 수도 있습니다. 데이터 파이프라인을 구축하는 단계를 안내하는 이 [문서](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/)를 참조하세요.

**Azure Blob에서 Data Lake로 데이터 읽기: U-SQL**

데이터가 Azure Blob 저장소에 상주하는 경우 U SQL 쿼리를 사용하여 Azure 저장소 Blob에서 직접 데이터를 읽을 수 있습니다. U-SQL 쿼리를 작성하기 전에 Blob 저장소 계정이 Azure Data Lake에 연결되었는지 확인합니다. **Azure Portal**로 이동하여 Azure Data Lake Analytics 대시보드를 찾은 다음 **데이터 원본 추가**를 클릭하고 저장소 유형을 **Azure Storage**로 선택한 후에 Azure Storage 계정 이름 및 키를 연결합니다. 그러면 저장소 계정에 저장된 데이터를 참조할 수 있습니다.

![저장소 계정 및 키 입력](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

Visual Studio에서 Blob Storage의 데이터를 읽고, 데이터를 조작하고, 기능을 엔지니어링하고, Azure Data Lake 또는 Azure Blob Storage에 결과 데이터를 출력할 수 있습니다. Blob Storage의 데이터를 참조할 때는 **wasb://** 를 사용하고, Azure Data Lake의 데이터를 참조할 때는 **swbhdfs://** 를 사용합니다.

![데이터 프레임](./media/vm-do-ten-things/USQL_Read_Blob_v2.PNG)

Visual Studio에서 다음 U-SQL 쿼리를 사용할 수 있습니다.

    @a =
        EXTRACT medallion string,
                hack_license string,
                vendor_id string,
                rate_code string,
                store_and_fwd_flag string,
                pickup_datetime string,
                dropoff_datetime string,
                passenger_count int,
                trip_time_in_secs double,
                trip_distance double,
                pickup_longitude string,
                pickup_latitude string,
                dropoff_longitude string,
                dropoff_latitude string

        FROM "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Input Data File Name>"
        USING Extractors.Csv();

    @b =
        SELECT vendor_id,
        COUNT(medallion) AS cnt_medallion,
        SUM(passenger_count) AS cnt_passenger,
        AVG(trip_distance) AS avg_trip_dist,
        MIN(trip_distance) AS min_trip_dist,
        MAX(trip_distance) AS max_trip_dist,
        AVG(trip_time_in_secs) AS avg_trip_time
        FROM @a
        GROUP BY vendor_id;

    OUTPUT @b   
    TO "swebhdfs://<Azure Data Lake Storage Account Name>.azuredatalakestore.net/<Folder Name>/<Output Data File Name>"
    USING Outputters.Csv();

    OUTPUT @b   
    TO "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Output Data File Name>"
    USING Outputters.Csv();



서버에 쿼리가 제출되면 작업 상태를 표시하는 다이어그램이 표시됩니다.

![작업 상태 다이어그램](./media/vm-do-ten-things/USQL_Job_Status.PNG)

**Data Lake의 데이터 쿼리: U SQL**

Azure Data Lake에 데이터 집합이 수집되면 [U-SQL 언어](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md)를 사용하여 데이터를 쿼리하고 탐색할 수 있습니다. U-SQL 언어는 T-SQL과 비슷하지만 C#의 일부 기능이 결합되어 사용자가 사용자 지정 모듈, 사용자 정의 함수 등을 작성할 수 있습니다. 이전 단계의 스크립트를 사용할 수 있습니다.

서버에 쿼리가 제출되면 잠시 후 **Azure Data Lake 탐색기**에서 tripdata_summary.CSV 파일이 표시되며, 이 파일을 마우스 오른쪽 단추로 클릭하여 데이터를 미리 볼 수 있습니다.

![Azure Data Lake Explorer의 파일](./media/vm-do-ten-things/USQL_create_summary.png)

파일 정보를 보려면:

![파일 요약](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>HDInsight Hadoop 클러스터
Azure HDInsight는 클라우드에서 관리되는 Apache Hadoop, Spark, HBase 및 Storm 서비스입니다. 데이터 과학 가상 머신에서 Azure HDInsight 클러스터를 쉽게 사용할 수 있습니다.

**필수 구성 요소**

* [Azure 포털](https://portal.azure.com)에서 고유한 Azure Blob 저장소 계정을 만듭니다. 이 저장소 계정은 HDInsight 클러스터에 대한 데이터를 저장하는 데 사용됩니다.

![Azure Blob Storage 계정 만들기](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* [Azure 포털](../team-data-science-process/customize-hadoop-cluster.md)
  
  * HDInsight 클러스터를 만들 때 만든 저장소 계정을 HDInsight 클러스터와 연결합니다. 이 저장소 계정은 클러스터 내에서 처리할 수 있는 데이터에 액세스하는 데 사용됩니다.

![HDInsight 클러스터로 만든 저장소 계정에 연결](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* 클러스터의 헤드 노드에 대한 **원격 액세스**를 사용하도록 설정합니다. 여기에서 지정한 원격 액세스 자격 증명은 이후 절차에서 필요하므로 기억해둡니다.

![원격 액세스 사용](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* Azure Machine Learning 작업 영역을 만듭니다. Machine Learning 실험이 이 Machine Learning 작업 영역에 저장됩니다. 아래 스크린샷에 표시된 것과 같이 포털에서 강조 표시된 옵션을 선택합니다.

![Azure Machine Learning 작업 영역 만들기](./media/vm-do-ten-things/Create_ML_Space.PNG)

* 그런 다음 작업 영역에 대한 매개 변수를 입력합니다.

![Machine Learning 작업 영역 매개 변수 입력](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* IPython Notebook을 사용하여 데이터를 업로드합니다. 먼저 필요한 패키지를 가져오고 자격 증명에 연결하고 저장소 계정에 db를 만든 다음 HDI 클러스터에 데이터를 로드합니다.

        #Import required Packages
        import pyodbc
        import time as time
        import json
        import os
        import urllib
        import urllib2
        import warnings
        import re
        import pandas as pd
        import matplotlib.pyplot as plt
        from azure.storage.blob import BlobService
        warnings.filterwarnings("ignore", category=UserWarning, module='urllib2')


        #Create the connection to Hive using ODBC
        SERVER_NAME='xxx.azurehdinsight.net'
        DATABASE_NAME='nyctaxidb'
        USERID='xxx'
        PASSWORD='xxxx'
        DB_DRIVER='Microsoft Hive ODBC Driver'
        driver = 'DRIVER={' + DB_DRIVER + '}'
        server = 'Host=' + SERVER_NAME + ';Port=443'
        database = 'Schema=' + DATABASE_NAME
        hiveserv = 'HiveServerType=2'
        auth = 'AuthMech=6'
        uid = 'UID=' + USERID
        pwd = 'PWD=' + PASSWORD
        CONNECTION_STRING = ';'.join([driver,server,database,hiveserv,auth,uid,pwd])
        connection = pyodbc.connect(CONNECTION_STRING, autocommit=True)
        cursor=connection.cursor()


        #Create Hive database and tables
        queryString = "create database if not exists nyctaxidb;"
        cursor.execute(queryString)

        queryString = """
                        create external table if not exists nyctaxidb.trip
                        (
                            medallion string,
                            hack_license string,
                            vendor_id string,
                            rate_code string,
                            store_and_fwd_flag string,
                            pickup_datetime string,
                            dropoff_datetime string,
                            passenger_count int,
                            trip_time_in_secs double,
                            trip_distance double,
                            pickup_longitude double,
                            pickup_latitude double,
                            dropoff_longitude double,
                            dropoff_latitude double)  
                        PARTITIONED BY (month int)
                        ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                        STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');
                    """
        cursor.execute(queryString)

        queryString = """
                        create external table if not exists nyctaxidb.fare
                        (
                            medallion string,
                            hack_license string,
                            vendor_id string,
                            pickup_datetime string,
                            payment_type string,
                            fare_amount double,
                            surcharge double,
                            mta_tax double,
                            tip_amount double,
                            tolls_amount double,
                            total_amount double)
                        PARTITIONED BY (month int)
                        ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                        STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');
                    """
        cursor.execute(queryString)


        #Upload data from blob storage to HDI cluster
        for i in range(1,13):
            queryString = "LOAD DATA INPATH 'wasb:///nyctaxitripraw2/trip_data_%d.csv' INTO TABLE nyctaxidb2.trip PARTITION (month=%d);"%(i,i)
            cursor.execute(queryString)
            queryString = "LOAD DATA INPATH 'wasb:///nyctaxifareraw2/trip_fare_%d.csv' INTO TABLE nyctaxidb2.fare PARTITION (month=%d);"%(i,i)  
            cursor.execute(queryString)


* 또는 이 [연습](../team-data-science-process/hive-walkthrough.md)에 따라 NYC Taxi 데이터를 HDI 클러스터에 업로드할 수 있습니다. 주요 단계는 다음과 같습니다.
  
  * AzCopy: 압축된 CSV를 공용 Blob에서 로컬 폴더로 다운로드
  * AzCopy: 압축을 푼 CSV를 로컬 폴더에서 HDI 클러스터로 업로드
  * Hadoop 클러스터의 헤드 노드에 로그인하여 예비 데이터 분석 준비

데이터가 HDI 클러스터에 로드되면 Azure Storage를 탐색기에서 데이터를 검사할 수 있습니다. 그리고 HDI 클러스터에서 만든 데이터베이스 nyctaxidb가 있습니다.

**데이터 탐색: Python에서 Hive 쿼리**

데이터가 Hadoop 클러스터에 있기 때문에 pyodbc 패키지를 사용하여 Hadoop 클러스터에 연결한 후 Hive를 사용하여 데이터베이스를 쿼리하여 데이터 탐색 및 기능 엔지니어링을 수행할 수 있습니다. 필수 조건 단계에서 만든 기존 테이블을 볼 수 있습니다.

    queryString = """
        show tables in nyctaxidb2;
        """
    pd.read_sql(queryString,connection)


![기존 테이블 보기](./media/vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

각 월의 레코드 수와 trip 테이블에서 팁을 받은 여정과 팁을 받지 않은 여정의 빈도를 살펴보겠습니다.

    queryString = """
        select month, count(*) from nyctaxidb.trip group by month;
        """
    results = pd.read_sql(queryString,connection)

    %matplotlib inline

    results.columns = ['month', 'trip_count']
    df = results.copy()
    df.index = df['month']
    df['trip_count'].plot(kind='bar')


![월별 레코드 수에 대한 도표](./media/vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)

    queryString = """
        SELECT tipped, COUNT(*) AS tip_freq
        FROM
        (
            SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
            FROM nyctaxidb.fare
        )tc
        GROUP BY tipped;
        """
    results = pd.read_sql(queryString,connection)

    results.columns = ['tipped', 'trip_count']
    df = results.copy()
    df.index = df['tipped']
    df['trip_count'].plot(kind='bar')


![팁 빈도 도표](./media/vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

또한 승차 위치와 하차 위치 사이의 거리를 계산한 다음 주행 거리와 비교할 수 있습니다.

    queryString = """
                    select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
                        3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
                        *radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
                        *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
                        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
                        +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*
                        pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance
                        from nyctaxidb.trip
                        where month=1
                            and pickup_longitude between -90 and -30
                            and pickup_latitude between 30 and 90
                            and dropoff_longitude between -90 and -30
                            and dropoff_latitude between 30 and 90;
                """
    results = pd.read_sql(queryString,connection)
    results.head(5)


![승차 및 하차 테이블](./media/vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

    results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                       'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
    df = results.loc[results['trip_distance']<=100] #remove outliers
    df = df.loc[df['direct_distance']<=100] #remove outliers
    plt.scatter(df['direct_distance'], df['trip_distance'])


![주행 거리에 대한 승차/하차 거리 도표](./media/vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

이제 모델링을 위한 다운샘플링(1%) 데이터 집합을 준비하겠습니다. Machine Learning 판독기 모듈에서 이 데이터를 사용할 수 있습니다.

        queryString = """
        create  table if not exists nyctaxi_downsampled_dataset_testNEW (
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        pickup_hour string,
        pickup_week string,
        weekday string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double,
        direct_distance double,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double,
        tipped string,
        tip_class string
        )
        row format delimited fields terminated by ','
        lines terminated by '\\n'
        stored as textfile;
        """
        cursor.execute(queryString)

        --- now insert contents of the join into the preceding internal table

        queryString = """
        insert overwrite table nyctaxi_downsampled_dataset_testNEW
        select
        t.medallion,
        t.hack_license,
        t.vendor_id,
        t.rate_code,
        t.store_and_fwd_flag,
        t.pickup_datetime,
        t.dropoff_datetime,
        hour(t.pickup_datetime) as pickup_hour,
        weekofyear(t.pickup_datetime) as pickup_week,
        from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
        t.passenger_count,
        t.trip_time_in_secs,
        t.trip_distance,
        t.pickup_longitude,
        t.pickup_latitude,
        t.dropoff_longitude,
        t.dropoff_latitude,
        t.direct_distance,
        f.payment_type,
        f.fare_amount,
        f.surcharge,
        f.mta_tax,
        f.tip_amount,
        f.tolls_amount,
        f.total_amount,
        if(tip_amount>0,1,0) as tipped,
        if(tip_amount=0,0,
        if(tip_amount>0 and tip_amount<=5,1,
        if(tip_amount>5 and tip_amount<=10,2,
        if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class
        from
        (
        select
        medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        pickup_datetime,
        dropoff_datetime,
        passenger_count,
        trip_time_in_secs,
        trip_distance,
        pickup_longitude,
        pickup_latitude,
        dropoff_longitude,
        dropoff_latitude,
        3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
        radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
        *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
        +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance,
        rand() as sample_key

        from trip
        where pickup_latitude between 30 and 90
            and pickup_longitude between -90 and -30
            and dropoff_latitude between 30 and 90
            and dropoff_longitude between -90 and -30
        )t
        join
        (
        select
        medallion,
        hack_license,
        vendor_id,
        pickup_datetime,
        payment_type,
        fare_amount,
        surcharge,
        mta_tax,
        tip_amount,
        tolls_amount,
        total_amount
        from fare
        )f
        on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
        where t.sample_key<=0.01
        """
        cursor.execute(queryString)

잠시 후 Hadoop 클러스터에 데이터가 로드된 것을 볼 수 있습니다.

    queryString = """
        select * from nyctaxi_downsampled_dataset limit 10;
        """
    cursor.execute(queryString)
    pd.read_sql(queryString,connection)


![데이터 테이블](./media/vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

**Machine Learning을 사용하여 HDI에서 데이터 읽기: 판독기 모듈**

Machine Learning Studio의 **판독기** 모듈을 사용하여 Hadoop 클러스터의 데이터베이스에 액세스할 수도 있습니다. HDI 클러스터의 자격 증명과 Azure Storage 계정을 연결하여 HDI 클러스터의 데이터베이스를 사용한 기계 학습 모델 빌드를 가능하도록 합니다.

![판독기 모듈 속성](./media/vm-do-ten-things/AML_Reader_Hive.PNG)

그러면 채점된 데이터 집합을 볼 수 있습니다.

![채점된 데이터 집합 보기](./media/vm-do-ten-things/AML_Model_Results.PNG)

### <a name="azure-sql-data-warehouse--databases"></a>Azure SQL Data Warehouse 및 데이터베이스
Azure SQL Data Warehouse는 엔터프라이즈급 SQL Server 환경의 서비스로 탄력적인 데이터 웨어하우스입니다.

이 [문서](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)에 제공된 지침에 따라 Azure SQL Data Warehouse를 프로비전할 수 있습니다. Azure SQL Data Warehouse를 프로비전하면 이 [연습](../team-data-science-process/sqldw-walkthrough.md) 을 사용하여 SQL Data Warehouse 내의 데이터를 사용해 데이터 업로드, 탐색 및 모델링을 수행할 수 있습니다.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB는 클라우드의 NoSQL 데이터베이스입니다. JSON과 같은 문서를 작업할 수 있으며 문서를 저장 및 쿼리할 수 있습니다.

DSVM에서 Azure Cosmos DB에 액세스하기 위한 다음과 같은 필수 조건 단계가 있습니다.

1. Azure Cosmos DB Python SDK는 DSVM에 이미 설치되어 있습니다(업데이트하려면 명령 프롬프트에서 ```pip install pydocumentdb --upgrade``` 실행).
2. [Azure Portal](https://portal.azure.com)에서 Azure Cosmos DB 계정과 데이터베이스를 만듭니다.
3. [여기](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d)서 "Azure Cosmos DB 마이그레이션 도구"를 다운로드하여 원하는 디렉터리에 추출합니다.
4. 마이그레이션 도구(Cosmos DB 마이그레이션 도구를 설치한 디렉터리 dtui.exe)에 다음 명령 매개 변수를 사용하여 [공용 Blob](https://cahandson.blob.core.windows.net/samples/volcano.json)에 저장된 JSON 데이터(화산 데이터)를 Cosmos DB로 가져옵니다. 아래의 원본 및 대상 위치를 다음 매개 변수와 함께 입력합니다.
   
    /s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1

데이터를 가져온 후에는 Jupyter로 이동하여 *DocumentDBSample*이라는 제목의 Notebook을 열 수 있습니다. 이 Notebook에는 Azure Cosmos DB에 액세스하여 몇 가지 기본 쿼리를 수행할 수 있는 Python 코드가 포함되어 있습니다. 서비스 [설명서 페이지](https://docs.microsoft.com/azure/cosmos-db/)를 방문하여 Cosmos DB에 대해 자세히 알아볼 수 있습니다.

## <a name="8-build-reports-and-dashboard-using-the-power-bi-desktop"></a>8. Power BI Desktop을 사용하여 보고서 및 대시보드 작성
위의 Cosmos DB 예제에서 확인한 Volcano JSON 파일을 Power BI에서 시각화하여 데이터를 시각적으로 살펴보겠습니다. 자세한 단계는 [Power BI 문서](../../cosmos-db/powerbi-visualize.md)에서 확인할 수 있습니다. 대략적인 단계는 다음과 같습니다.

1. Power BI Desktop을 열고 "Get Data"를 수행합니다. URL을 https://cahandson.blob.core.windows.net/samples/volcano.json으로 지정합니다.
2. 목록으로 가져온 JSON 레코드가 보일 것입니다.
3. Power BI가 동일한 항목을 작업할 수 있도록 목록을 테이블로 변환합니다.
4. 확장 아이콘(열 오른쪽에 "왼쪽 화살표와 오른쪽 화살표" 아이콘이 있는 아이콘)을 클릭하여 열을 확장합니다.
5. 위치가 "레코드" 필드인 것을 볼 수 있습니다. 레코드를 확장하고 좌표만 선택합니다. 좌표는 목록 열입니다.
6. 목록 좌표 열을 쉼표로 구분된 LatLong 열로 변환하는 새 열을 추가하고 ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```수식을 사용하여 좌표 목록 필드의 두 요소를 연결합니다.
7. 마지막으로 ```Elevation``` 열을 10진수로 변환하고 **닫기** 및 **적용**을 선택합니다.

위의 단계를 수행하는 대신, 위의 단계를 스크립팅하는 다음 코드를 Power BI 고급 편집기에 붙여넣어서 데이터 변환을 쿼리 언어로 작성할 수 있습니다.

    let
        Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
        #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
        #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
        #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
        #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
        #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
    in
        #"Changed Type"



이제 Power BI 데이터 모델에 데이터가 있습니다. Power BI Desktop이 다음과 같이 표시됩니다.

![Power BI 데스크톱](./media/vm-do-ten-things/PowerBIVolcanoData.png)

데이터 모델을 사용하여 보고서를 작성하고 시각화할 수 있습니다. 이 [Power BI 문서](../../cosmos-db/powerbi-visualize.md#build-the-reports) 의 단계에 따라 보고서를 작성할 수 있습니다. 출력은 다음과 같이 표시되는 보고서입니다.

![Power BI 데스크톱 보고서 보기 - Power BI 커넥터](./media/vm-do-ten-things/power_bi_connector_pbireportview2.png)

## <a name="9-dynamically-scale-your-dsvm-to-meet-your-project-needs"></a>9. 프로젝트 요구 사항에 맞게 DSVM을 동적으로 확장
프로젝트 요구 사항에 맞게 DSVM을 확장 및 축소할 수 있습니다. 저녁이나 주말에 VM이 필요 없으면 [Azure 포털](https://portal.azure.com)에서 VM을 종료하면 됩니다.

> [!NOTE]
> VM의 운영 체제만 종료하면 계산 요금이 청구됩니다.  
> 
> 

대규모 분석을 처리하고 더 많은 CPU, 메모리 및/또는 디스크 용량이 필요한 경우 CPU 코어, 심화 학습을 위한 GPU 기반 인스턴스, 메모리 용량 및 디스크 유형(반도체 드라이브 포함)을 기준으로 계산 성능과 예산 조건에 맞는 VM 크기를 선택할 수 있습니다. 시간당 계산 가격을 비롯한 전체 VM 목록은 [Azure Virtual Machines 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/) 페이지에서 확인할 수 있습니다.

마찬가지로, 필요한 VM 처리 용량이 감소할 경우(예: 주요 워크로드를 Hadoop 또는 Spark 클러스터로 이동) [Azure 포털](https://portal.azure.com) 에서 VM 인스턴스 설정으로 이동하여 클러스터 규모를 축소할 수 있습니다. 다음은 스크린샷입니다.

![VM 인스턴스 설정](./media/vm-do-ten-things/VMScaling.PNG)

## <a name="10-install-additional-tools-on-your-virtual-machine"></a>10. 가상 머신에 추가 도구 설치
많은 일반적인 데이터 분석 요구를 해결할 수 있는 여러 도구가 DSVM에 미리 제공되어 있습니다. 따라서 환경을 하나씩 설치 및 구성할 필요가 없으므로 시간이 절약되고, 사용하는 리소스에 대해서만 지불하면 되므로 비용도 절감할 수 있습니다.

사용자는 이 문서에서 프로파일링한 다른 Azure 데이터 및 분석 서비스를 활용하여 분석 환경을 강화할 수 있습니다. 하지만 경우에 따라 독점적 타사 도구를 비롯한 추가 도구가 필요할 수도 있습니다. 사용자에게는 가상 머신에 필요한 새 도구를 설치할 수 있는 모든 관리 액세스 권한이 있습니다. 또한 사전 설치되지 않은 추가 패키지를 Python 및 R에 설치할 수 있습니다. Python의 경우 ```conda``` 또는 ```pip```를 사용할 수 있습니다. R의 경우 R 콘솔에서 ```install.packages()```를 사용하거나 IDE를 사용한 다음 "**패키지** -> **패키지 설치...**"를 선택할 수 있습니다.

## <a name="summary"></a>요약
이는 Microsoft 데이터 과학 Virtual Machine에서 할 수 있는 여러 가지 일 중의 극히 일부에 불과합니다. 그 외에도 다양한 방법으로 효과적인 분석 환경을 만들 수 있습니다.

