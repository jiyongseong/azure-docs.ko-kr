---
title: Azure Machine Learning의 ALM | Microsoft Docs
description: Azure Machine Learning 스튜디오에서 응용 프로그램 수명 주기 관리 모범 사례 적용
keywords: AML, ALM, Azure ML, 응용 프로그램 수명 주기 관리, 버전 제어
services: machine-learning
documentationcenter: ''
author: hning86
ms.author: haining
manager: mwinkle
editor: cgronlun
ms.assetid: 1be6577d-f2c7-425b-b6b9-d5038e52b395
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2016
ms.openlocfilehash: 50a93d439f6d6815113d93e0dece7b512b9defe7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="application-lifecycle-management-in-azure-machine-learning-studio"></a>Azure Machine Learning 스튜디오에서 응용 프로그램 수명 주기 관리
Azure Machine Learning 스튜디오는 Machine Learning 실험을 개발하기 위한 도구로, Azure 클라우드 플랫폼에서 작동합니다. 단일 플랫폼으로 병합된 Visual Studio IDE 및 확장 가능한 서비스와 같습니다. 다양한 자산 버전 관리에서 자동화된 실행 및 배포에 이르는 표준 ALM(응용 프로그램 수명 주기 관리) 사례를 Azure Machine Learning 스튜디오에 통합할 수 있습니다. 이 문서는 몇 가지 옵션과 접근 방법에 대해 다룹니다.

## <a name="versioning-experiment"></a>실험 버전 관리
실험의 버전을 관리하는 두 가지 권장 방법이 있습니다. 기본 제공 실행 기록에 의존하거나 외부에서 관리하도록 JSON 형식으로 실험을 내보낼 수 있습니다. 각 접근 방식에는 장단점이 있습니다.

### <a name="experiment-snapshots-using-run-history"></a>실행 기록을 사용하는 실험 스냅숏
Azure Machine Learning Studio 학습 실험의 실행 모델에서 실험 편집기의 **실행**을 클릭할 때마다 변경할 수 없는 실험의 스냅숏이 작업 스케줄러에 전송됩니다. 이 스냅숏 목록을 보려면 실험 편집기 보기의 명령 모음에서 **실행 기록**을 클릭합니다.

![실행 기록 단추](./media/version-control/runhistory.png)

실험이 실행되기 위해 제출되고 스냅숏이 만들어진 시점에서 실험의 이름을 클릭하여 잠금 모드로 스냅숏을 열 수 있습니다. 현재 실험을 나타내는 목록에서 첫 번째 항목만이 편집 가능한 상태입니다. 또한 각 스냅숏은 완료(부분 실행), 실패, 실패(부분 실행) 또는 초안을 비롯한 다양한 상태라는 점을 기억합니다.

![실행 기록 목록](./media/version-control/runhistorylist.png)

목록이 열리면 스냅숏 실험을 새 실험으로 저장하고 수정할 수 있습니다. 실험 스냅숏에 업데이트된 버전인 학습된 모델, 변환, 데이터 집합 등과 같은 자산이 포함된 경우 스냅숏은 해당 스냅숏을 만든 원래 버전에 대한 참조를 유지합니다. 잠긴 스냅숏을 새 실험으로 저장하면 Azure Machine Learning Studio는 이러한 자산의 최신 버전이 있는지 검색하고 새 실험에서 자동으로 업데이트합니다.

또한 실험을 삭제하면 해당 실험의 모든 스냅숏이 삭제됩니다.

### <a name="exportimport-experiment-in-json-format"></a>JSON 형식으로 실험 내보내기/가져오기
실행 기록 스냅숏은 실행을 위해 제출될 때마다 Azure Machine Learning Studio에서 변경할 수 없는 실험 버전을 유지합니다. 실험의 로컬 복사본을 저장한 후 Team Foundation Server와 같은 즐겨 찾는 소스 제어 시스템에 체크 인하거나 나중에 해당 로컬 파일에서 실험을 다시 만들 수도 있습니다. 이를 위해 [Azure Machine Learning PowerShell](http://aka.ms/amlps) commandlet인 [*Export-AmlExperimentGraph*](https://github.com/hning86/azuremlps#export-amlexperimentgraph) 및 [*Import-AmlExperimentGraph*](https://github.com/hning86/azuremlps#import-amlexperimentgraph)를 사용할 수 있습니다.

JSON 파일이 실험 그래프를 텍스트로 표현하지만 작업 영역에서 데이터 집합 또는 학습된 모델과 같은 자산에 대한 참조를 포함할 수 있습니다. 직렬화된 자산 버전은 포함되지 않습니다. JSON 문서를 작업 영역에 다시 가져오려는 경우 참조된 자산은 실험에서 참조된 것과 동일한 자산 ID로 이미 존재해야 합니다. 그렇지 않으면 가져온 실험에 액세스할 수 없습니다.

## <a name="versioning-trained-model"></a>학습된 모델 버전 관리
Azure Machine Learning에서 학습된 모델은 iLearner 파일(`.iLearner`)이라는 형식으로 직렬화되고 작업 영역과 연결된 Azure Blob Storage 계정에 저장됩니다. 재학습 API를 통해 iLearner 파일의 사본을 얻을 수 있습니다. [이 문서](retrain-models-programmatically.md)에서는 재학습 API의 작동 방법을 설명합니다. 대략적인 단계는 다음과 같습니다.

1. 학습 실험을 설정합니다.
2. 모델 학습 모듈 또는 [모델 하이퍼 매개 변수 조정] 또는 [R 모델 만들기]와 같은 학습된 모델을 생성하는 모듈에 웹 서비스 출력 포트를 추가합니다.
3. 학습 실험을 실행한 다음 모델 학습 웹 서비스로 배포합니다.
4. 학습 웹 서비스의 BES 끝점을 호출하고 원하는 iLearner 파일 이름 및 파일이 저장될 Blob Storage 계정 위치를 지정합니다.
5. BES를 호출한 후에 생성된 iLearner 파일을 수집합니다.

[*Download-AmlExperimentNodeOutput*](https://github.com/hning86/azuremlps#download-amlexperimentnodeoutput) PowerShell commandlet을 통해 iLearner 파일을 검색할 수 있습니다. 프로그래밍 방식으로 모델을 다시 학습할 필요 없이 iLearner 파일의 복사본을 가져오려면 이 방법이 더 쉬울 수 있습니다.

학습된 모델을 포함하는 iLearner 파일이 있으면 자체 버전 관리 전략을 사용할 수 있습니다. 접두사/접미사를 명명 규칙으로 적용하고, Blob Storage에 iLearner 파일을 그대로 두거나 버전 제어 시스템에 복사/가져오기 등과 같이 간단히 전략을 세울 수 있습니다.

그러면 저장된 iLearner 파일은 배포된 웹 서비스를 통해 점수 매기기에 사용될 수 있습니다.

## <a name="versioning-web-service"></a>웹 서비스 버전 관리
Azure Machine Learning 실험에서 두 종류의 웹 서비스를 배포할 수 있습니다. 클래식 웹 서비스는 작업 영역뿐만 아니라 실험과도 밀접하게 연결되어 있습니다. 새 웹 서비스는 Azure Resource Manager 프레임워크를 활용하고 더 이상 원래 실험 또는 작업 영역과 연결되지 않습니다.

### <a name="classic-web-service"></a>클래식 웹 서비스
클래식 웹 서비스의 버전을 관리하려면 웹 서비스 끝점 구문을 활용할 수 있습니다. 일반적인 흐름은 다음과 같습니다.

1. 예측면 실험에서 기본 끝점을 포함하는 새로운 클래식 웹 서비스를 배포할 수 있습니다.
2. ep2라는 새 끝점을 만들고 여기서 실험/학습된 모델의 현재 버전을 노출합니다.
3. 다시 이동하여 예측 실험 및 학습된 모델을 업데이트합니다.
4. 예측 실험을 다시 배포하여 기본 끝점을 업데이트할 수 있습니다. 하지만 이 작업은 ep2를 변경하지 않습니다.
5. ep3라는 추가 끝점을 만들고 여기서 실험 및 학습된 모델의 새 버전을 노출할 수 있습니다.
6. 필요한 경우 3단계로 돌아갑니다.

시간이 지남에 따라 여러 끝점을 동일한 웹 서비스에서 만들 수 있습니다. 각 끝점은 학습된 모델의 지정 시간 버전을 포함하는 실험의 지정 시간 복사본을 나타냅니다. 그런 다음 호출할 끝점을 결정하는 외부 논리를 사용할 수 있습니다. 즉, 사실상 점수 매기기 실행에 대한 학습된 모델의 버전을 선택하게 됩니다.

또한 여러 동일한 웹 서비스 엔드포인트를 만든 다음, 비슷한 효과를 달성하기 위해 엔드포인트에 iLearner 파일의 다른 버전을 패치할 수 있습니다. [이 문서](create-models-and-endpoints-with-powershell.md)에서는 이 작업을 수행하는 방법에 대해 자세히 설명합니다.

### <a name="new-web-service"></a>새 웹 서비스
새 Azure Resource Manager 기반 웹 서비스를 만들면 끝점 구문은 더 이상 사용할 수 없습니다. 대신 배포된 Resource Manager 기반 웹 서비스의 [Export-AmlWebServiceDefinitionFromExperiment](https://github.com/hning86/azuremlps#export-amlwebservicedefinitionfromexperiment) PowerShell commandlet 또는 [*Export-AzureRmMlWebservice*](https://msdn.microsoft.com/library/azure/mt767935.aspx) PowerShell commandlet을 사용하여 예측 실험의 WSD(웹 서비스 정의) 파일을 JSON 형식으로 생성할 수 있습니다.

내보낸 WSD 파일이 있고 버전을 제어한 다음 다른 Azure 하위 지역의 다른 웹 서비스 계획에서 WSD를 새 웹 서비스로 배포할 수 있습니다. 적절한 저장소 계정 구성뿐만 아니라 새 웹 서비스 계획 ID를 제공해야 합니다. 다른 iLearner 파일에서 패치하려면 WSD 파일을 수정하고 학습된 모델의 위치 참조를 업데이트하고 새 웹 서비스로 배포할 수 있습니다.

## <a name="automate-experiment-execution-and-deployment"></a>실험 실행 및 배포 자동화
ALM의 중요한 부분은 응용 프로그램의 실행 및 배포 프로세스를 자동화할 수 있다는 것입니다. 이 작업은 Azure Machine Learning에서 [PowerShell 모듈](http://aka.ms/amlps)을 사용하여 수행할 수 있습니다. [Azure Machine Learning Studio PowerShell 모듈](http://aka.ms/amlps)을 사용하여 표준 ALM 자동 실행/배포 프로세스에 관련된 종단 간 단계의 예는 다음과 같습니다. 각 단계는 해당 단계를 수행하는 데 사용할 수 있는 하나 이상의 PowerShell commandlet에 연결됩니다.

1. [데이터 집합을 업로드](https://github.com/hning86/azuremlps#upload-amldataset)합니다.
2. [작업 영역](https://github.com/hning86/azuremlps#copy-amlexperiment) 또는 [갤러리](https://github.com/hning86/azuremlps#copy-amlexperimentfromgallery)에서 작업 영역에 학습 실험을 복사하거나 로컬 디스크에서 [내보낸](https://github.com/hning86/azuremlps#export-amlexperimentgraph) 실험을 [가져옵니다](https://github.com/hning86/azuremlps#import-amlexperimentgraph).
3. 학습 실험에서 [데이터 집합을 업데이트](https://github.com/hning86/azuremlps#update-amlexperimentuserasset)합니다.
4. [학습 실험을 실행](https://github.com/hning86/azuremlps#start-amlexperiment)합니다.
5. [학습된 모델을 승격](https://github.com/hning86/azuremlps#promote-amltrainedmodel)합니다.
6. 작업 영역에서 [예측 실험을 복사](https://github.com/hning86/azuremlps#copy-amlexperiment)합니다.
7. 예측 실험에서 [학습 모델을 업데이트](https://github.com/hning86/azuremlps#update-amlexperimentuserasset)합니다.
8. [예측 실험을 실행](https://github.com/hning86/azuremlps#start-amlexperiment)합니다.
9. 예측 실험에서 [웹 서비스를 배포](https://github.com/hning86/azuremlps#new-amlwebservice)합니다.
10. [RRS](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) 또는 [BES](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint) 끝점 웹 서비스를 테스트합니다.

## <a name="next-steps"></a>다음 단계
* [Azure Machine Learning Studio PowerShell](http://aka.ms/amlps) 모듈을 다운로드하고 ALM 태스크를 자동화하기 시작합니다.
* PowerShell 및 재학습 API를 통해 [단일 실험을 사용하여 여러 ML 모델을 만들고 관리](create-models-and-endpoints-with-powershell.md)하는 방법에 대해 알아봅니다.
* [Azure Machine Learning 웹 서비스 배포](publish-a-machine-learning-web-service.md)에 대해 자세히 알아봅니다.
