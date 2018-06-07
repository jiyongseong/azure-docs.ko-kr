---
title: Azure Batch를 사용한 병렬 R 시뮬레이션
description: 자습서 - R doAzureParallel 패키지를 사용하여 Azure Batch에서 몬테카를로 재무 시뮬레이션을 실행하는 단계별 지침
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: r
ms.topic: tutorial
ms.date: 01/23/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: fb616dc95cc7dd7dbb25f2deb832b517d0747ae4
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="tutorial-run-a-parallel-r-simulation-with-azure-batch"></a>자습서: Azure Batch를 사용하여 병렬 R 시뮬레이션 실행 

R 세션에서 Azure Batch를 직접 사용할 수 있는 간단한 R 패키지인 [doAzureParallel](http://www.github.com/Azure/doAzureParallel)을 사용하여 병렬 R 작업을 규모에 맞게 실행합니다. doAzureParallel 패키지는 인기 있는 [foreach](http://cran.r-project.org/web/packages/foreach/index.html) R 패키지를 기반으로 하여 빌드되었습니다. doAzureParallel은 foreach 루프의 각 반복을 수행하여 Azure Batch 태스크로 제출합니다.

이 자습서에서는 RStudio 내에서 직접 Batch 풀을 배포하고 Azure Batch에서 병렬 R 작업을 실행하는 방법을 보여 줍니다. 다음 방법에 대해 알아봅니다.
 

> [!div class="checklist"]
> * doAzureParallel을 설치하고 배치 계정 및 저장소 계정에 액세스하도록 구성
> * R 세션에 대한 병렬 백 엔드로 Batch 풀 만들기
> * 풀에서 병렬 시뮬레이션 샘플 실행

## <a name="prerequisites"></a>필수 조건

* 설치된 [R](https://www.r-project.org/) 배포(예:[ Microsoft R Open](https://mran.microsoft.com/open)). R 버전 3.3.1 이상을 사용합니다.

* [RStudio](https://www.rstudio.com/)(상용 버전 또는 오픈 소스 [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop)) 

* Azure Batch 계정 및 Azure Storage 계정. 이러한 계정을 만들려면 [Azure Portal](quick-create-portal.md) 또는 [Azure CLI](quick-create-cli.md)를 사용하는 Batch 빠른 시작을 참조하세요. 

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)] 
## <a name="install-doazureparallel"></a>doAzureParallel 설치

RStudio 콘솔에서 [doAzureParallel Github 패키지](http://www.github.com/Azure/doAzureParallel)를 설치합니다. 다음 명령은 현재 R 세션에서 패키지 및 해당 종속성을 다운로드하고 설치합니다. 

```R
# Install the devtools package  
install.packages("devtools") 

# Install rAzureBatch package
devtools::install_github("Azure/rAzureBatch") 

# Install the doAzureParallel package 
devtools::install_github("Azure/doAzureParallel") 
 
# Load the doAzureParallel library 
library(doAzureParallel) 
```
설치하는 데 몇 분이 걸릴 수 있습니다.

이전에 얻은 계정 자격 증명으로 doAzureParallel을 구성하려면 작업 디렉터리에 *credentials.json*이라는 구성 파일을 생성합니다. 

```R
generateCredentialsConfig("credentials.json") 
``` 

이 파일을 배치 계정 및 저장소 계정의 이름과 키로 채웁니다. `githubAuthenticationToken` 설정은 변경하지 않고 그대로 둡니다.

완료되면 자격 증명 파일은 다음과 비슷합니다. 

```json
{
  "batchAccount": {
    "name": "mybatchaccount",
    "key": "xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ==",
    "url": "https://mybatchaccount.mybatchregion.batch.azure.com"
  },
  "storageAccount": {
    "name": "mystorageaccount",
    "key": "xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ=="
  },
  "githubAuthenticationToken": ""
}
```

파일을 저장합니다. 이제 다음 명령을 실행하여 현재 R 세션에 대한 자격 증명을 설정합니다. 

```R
setCredentials("credentials.json") 
```

## <a name="create-a-batch-pool"></a>Batch 풀 만들기 

doAzureParallel에는 병렬 R 작업을 실행하기 위한 Azure Batch 풀(클러스터)을 생성하는 함수가 포함되어 있습니다. 노드는 Ubuntu 기반 [Azure 데이터 과학 가상 머신](../machine-learning/data-science-virtual-machine/overview.md)을 실행합니다. Microsoft R Open 및 인기 있는 R 패키지가 이 이미지에 미리 설치되어 있습니다. 노드의 수 및 크기와 같은 특정 클러스터 설정을 보거나 사용자 지정할 수 있습니다. 

작업 디렉터리에 클러스터 구성 JSON 파일을 생성하려면 다음을 수행합니다. 
 
```R
generateClusterConfig("cluster.json")
``` 
 
파일을 열어 3개 전용 노드 및 [낮은 우선 순위](batch-low-pri-vms.md)의 3개 노드가 포함된 기본 구성을 봅니다. 이러한 설정은 실험하거나 수정할 수 있는 예일 뿐입니다. 전용 노드는 풀에 예약되어 있습니다. 우선 순위가 낮은 노드는 Azure의 잔여 VM 용량에서 할인된 가격으로 제공됩니다. Azure에 충분한 용량이 없으면 우선 순위가 낮은 노드는 사용할 수 없게 됩니다. 

이 자습서에서는 구성을 다음과 같이 변경합니다.

* 각 노드의 두 코어를 모두 활용하려면 `maxTasksPerNode`를 *2*로 늘립니다.
* `dedicatedNodes`를 *0*으로 설정하면 Batch에 사용할 수 있는 우선 순위가 낮은 VM을 시도할 수 있습니다. `lowPriorityNodes`의 `min`을 *5*로, `max`를 *10*으로 설정하거나, 필요한 경우 더 작은 숫자를 선택합니다. 

나머지 설정에 대한 기본값은 그대로 두고 파일을 저장합니다. 다음과 유사하게 나타납니다.

```json
{
  "name": "myPoolName",
  "vmSize": "Standard_D2_v2",
  "maxTasksPerNode": 4,
  "poolSize": {
    "dedicatedNodes": {
      "min": 0,
      "max": 0
    },
    "lowPriorityNodes": {
      "min": 5,
      "max": 10
    },
    "autoscaleFormula": "QUEUE"
  },
  "containerImage": "rocker/tidyverse:latest",
  "rPackages": {
    "cran": [],
    "github": [],
    "bioconductor": []
  },
  "commandLine": []
}
```

이제 클러스터를 만듭니다. Batch는 풀을 즉시 만들지만, 계산 노드를 할당하고 시작하는 데 몇 분이 걸립니다. 클러스터를 사용할 수 있게 되면 R 세션에 대한 병렬 백 엔드로 등록합니다. 

```R
# Create your cluster if it does not exist; this takes a few minutes
cluster <- makeCluster("cluster.json") 
  
# Register your parallel backend 
registerDoAzureParallel(cluster) 
  
# Check that the nodes are running 
getDoParWorkers() 
```

출력에는 doAzureParallel에 대한 "실행 작업자"의 수가 표시됩니다. 이 숫자는 노드 수에 `maxTasksPerNode` 값을 곱한 값입니다. 클러스터 구성을 앞에서 설명한 대로 수정한 경우 이 숫자는 *10*입니다. 
 
## <a name="run-a-parallel-simulation"></a>병렬 시뮬레이션 실행

이제 클러스터가 만들어졌으므로 등록된 병렬 백 엔드(Azure Batch 풀)를 사용하여 foreach 루프를 실행할 준비가 되었습니다. 예를 들어, 먼저 표준 foreach 루프를 사용하여 몬테카를로 재무 시뮬레이션을 로컬로 실행한 다음, Batch를 통해 foreach를 실행합니다. 이 예는 서로 다른 많은 결과를 시뮬레이션하여 5년 후의 주가를 예측하는 단순화된 버전입니다.

Contoso Corporation의 주식이 일일 평균 시가의 1.001배로 오르지만 0.01의 변동성(표준 편차)이 있다고 가정합니다. 시가가 $100인 경우 가격 시뮬레이션을 사용하여 5년 후의 Contoso 주가를 알아봅니다.

몬테카를로 시뮬레이션에 대한 매개 변수:

```R
mean_change = 1.001 
volatility = 0.01 
opening_price = 100 
```

종가를 시뮬레이션하려면 다음 함수를 정의합니다.

```R
getClosingPrice <- function() { 
  days <- 1825 # ~ 5 years 
  movement <- rnorm(days, mean=mean_change, sd=volatility) 
  path <- cumprod(c(opening_price, movement)) 
  closingPrice <- path[days] 
  return(closingPrice) 
} 
```

먼저 `%do%` 키워드가 있는 표준 foreach 루프를 사용하여 10,000개의 시뮬레이션을 로컬로 실행합니다.

```R
start_s <- Sys.time() 
# Run 10,000 simulations in series 
closingPrices_s <- foreach(i = 1:10, .combine='c') %do% { 
  replicate(1000, getClosingPrice()) 
} 
end_s <- Sys.time() 
```


히스토그램에 종가를 작성하여 결과의 분포를 표시합니다.

```R
hist(closingPrices_s)
``` 

다음과 유사하게 출력됩니다.

![종가 분포](media/tutorial-r-doazureparallel/closing-prices-local.png)
  
로컬 시뮬레이션은 몇 초 내에 완료됩니다.

```R
difftime(end_s, start_s) 
```

선형 근사값을 사용하여 로컬에서 1,000만 개의 결과에 대한 예상 런타임은 약 30분입니다.

```R 
1000 * difftime(end_s, start_s, unit = "min") 
```


이제 `%dopar%` 키워드가 있는 `foreach`를 사용하는 코드를 실행하여 Azure에서 1,000만 회의 시뮬레이션을 실행하는 데 걸리는 시간을 비교합니다. Batch를 사용하여 시뮬레이션을 병렬 처리하려면 10만 회의 시뮬레이션을 100회 반복합니다.

```R
# Optimize runtime. Chunking allows running multiple iterations on a single R instance.
opt <- list(chunkSize = 10) 
start_p <- Sys.time()  
closingPrices_p <- foreach(i = 1:100, .combine='c', .options.azure = opt) %dopar% { 
  replicate(100000, getClosingPrice()) 
} 
end_p <- Sys.time() 
```

시뮬레이션에서는 Batch 풀의 노드에 태스크를 배포합니다. 활동이 Azure Portal의 풀에 대한 열 지도에 표시됩니다. **배치 계정** > *myBatchAccount*로 차례로 이동합니다. **풀** > *myPoolName*을 차례로 클릭합니다. 

![병렬 R 태스크를 실행하는 풀의 열 지도](media/tutorial-r-doazureparallel/pool.png)

시뮬레이션은 몇 분 후에 완료됩니다. 패키지에서 결과를 자동으로 병합하고 노드로부터 끌어옵니다. 그러면 R 세션에서 결과를 사용할 준비가 됩니다. 

```R
hist(closingPrices_p) 
```

다음과 유사하게 출력됩니다.

![종가 분포](media/tutorial-r-doazureparallel/closing-prices.png)

병렬 시뮬레이션 소요 시간 

```R
difftime(end_p, start_p, unit = "min")  
```

Batch 풀에서 시뮬레이션을 실행하면, 시뮬레이션을 로컬로 실행하는 데 예상되는 시간에 비해 성능이 크게 향상됩니다. 

## <a name="clean-up-resources"></a>리소스 정리

작업이 완료되면 자동으로 삭제됩니다. 클러스터가 더 오래 필요한 경우 doAzureParallel 패키지의 `stopCluster` 함수를 호출하여 삭제합니다.

```R
stopCluster(cluster)
```

## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음을 수행하는 방법에 대해 알아보았습니다.

> [!div class="checklist"]
doAzureParallel을 설치하고 배치 계정 및 저장소 계정에 액세스하도록 구성
> * R 세션에 대한 병렬 백 엔드로 Batch 풀 만들기
> * 풀에서 병렬 시뮬레이션 샘플 실행


doAzureParallel에 대한 자세한 내용은 GitHub의 설명서 및 샘플을 참조하세요.

> [!div class="nextstepaction"]
> [doAzureParallel 패키지](https://github.com/Azure/doAzureParallel/)




