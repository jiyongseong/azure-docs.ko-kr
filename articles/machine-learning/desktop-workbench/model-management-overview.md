---
title: Azure Machine Learning 모델 관리에 대한 개념적 개요 | Microsoft Docs
description: 이 문서에서는 Azure Machine Learning 모델 관리에 대한 개념을 설명합니다.
services: machine-learning
author: nk773
ms.author: padou
manager: mwinkle
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 89f95753248f74c7f6cb9ca1f680a01b07dd43d1
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="azure-machine-learning-model-management"></a>Azure Machine Learning 모델 관리

Azure Machine Learning 모델 관리를 사용하면 기계 학습 워크플로 및 모델을 관리하고 배포할 수 있습니다. 

모델 관리에서 제공하는 기능은 다음과 같습니다.
- 모델 버전 관리
- 프로덕션 환경에서 모델 추적
- [Azure Container Service](https://azure.microsoft.com/services/container-service/) 및 [Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)를 사용하여 AzureML 계산 환경을 통해 프로덕션 환경에 모델 배포
- 모델을 사용하여 Docker 컨테이너 만들기 및 로컬로 테스트
- 자동화된 모델 재학습
- 실행 가능한 정보에 대한 모델 원격 분석 캡처 

Azure Machine Learning 모델 관리는 모델 버전의 레지스트리를 제공합니다. 또한 Machine Learning 컨테이너를 REST API로 패키지하고 배치하도록 자동화된 워크플로를 제공합니다. 모델 및 해당 런타임 종속성은 예측 API를 사용하여 Linux 기반 Docker 컨테이너에 패키지됩니다. 

Azure Machine Learning 계산 환경은 모델을 호스팅하는 확장 가능한 클러스터를 설정하고 관리하는 데 도움이 됩니다. 계산 환경은 Azure Container Services를 기반으로 합니다. Azure Container Services는 다음과 같은 기능이 있는 Machine Learning API를 REST API 끝점으로 자동으로 제공합니다.

- 인증
- 부하 분산
- 자동 확장
- 암호화

Azure Machine Learning 모델 관리는 CLI, API 및 Azure Portal을 통해 이러한 기능을 제공합니다. 

Azure Machine Learning 모델 관리에서 사용하는 정보는 다음과 같습니다.

 - 모델 파일 또는 모델 파일이 있는 디렉터리
 - 모델 점수 매기기 함수를 구현하는 Python 파일을 만든 사용자
 - 런타임 종속성을 나열하는 Conda 종속성 파일
 - 런타임 환경 선택 항목 및 
 - API 매개 변수에 대한 스키마 파일 

이러한 정보는 다음 작업을 수행할 때 사용됩니다.

- 모델 등록
- 컨테이너를 작성할 때 사용되는 매니페스트 만들기
- Docker 컨테이너 이미지 빌드
- Azure Container Service에 컨테이너 배포
 
다음 그림에서는 모델을 등록하고 클러스터에 배포하는 방법에 대한 개요를 보여 줍니다. 

![](media/model-management-overview/modelmanagement.png)

## <a name="create-and-manage-models"></a>모델 만들기 및 관리 
프로덕션 환경에서 모델 버전을 추적하기 위해 Azure Machine Learning 모델 관리를 사용하여 모델을 등록 할 수 있습니다. 재현성과 거버넌스를 쉽게 하기 위해 서비스에서 모든 종속성과 관련 정보를 수집합니다. 성능에 대한 심층적인 정보를 얻기 위해 제공된 SDK를 사용하여 모델 원격 분석을 캡처할 수 있습니다. 모델 원격 분석은 사용자가 제공한 저장소에 보관되며, 나중에 모델 성능을 분석하고, 다시 학습하고, 비즈니스 정보를 가져오는 데 사용될 수 있습니다.

## <a name="create-and-manage-manifests"></a>매니페스트 만들기 및 관리 
모델에는 프로덕션 환경에 배포할 추가 아티팩트가 필요합니다. 시스템에서는 모델, 종속성, 유추 스크립트(점수 매기기 스크립트라고도 함), 샘플 데이터, 스키마 등을 포함하는 매니페스트를 만드는 기능을 제공합니다. 이 매니페스트는 Docker 컨테이너 이미지를 만드는 수단으로 작동합니다. 기업에서는 매니페스트를 자동 생성하고, 다양한 버전을 만들고, 해당 매니페스트를 관리할 수 있습니다. 

## <a name="create-and-manage-docker-container-images"></a>Docker 컨테이너 이미지 만들기 및 관리 
이전 단계의 매니페스트를 사용하여 해당 환경에서 Docker 기반 컨테이너 이미지를 빌드할 수 있습니다. 컨테이너화된 Docker 기반 이미지는 다음과 같은 계산 환경에서 이러한 이미지를 실행할 수 있는 유연성을 기업에 제공합니다.

- [Kubernetes 기반 Azure Container Service](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
- 온-프레미스 컨테이너 서비스
- 개발 환경
- IoT 장치

이러한 Docker 기반 컨테이너 이미지는 예측을 생성하는 데 필요한 모든 종속성을 자체적으로 포함하고 있습니다. 

## <a name="deploy-docker-container-images"></a>Docker 컨테이너 이미지 배포 
Azure Machine Learning 모델 관리를 사용하면 단일 명령으로 ML 계산 환경에서 관리되는 Azure Container Service에 Docker 기반 컨테이너 이미지를 배포할 수 있습니다. 이러한 배포는 다음과 같은 기능을 제공하는 프런트 엔드 서버에서 만들어집니다.

- 규모에서 짧은 대기 시간 예측
- 부하 분산
- ML 끝점 자동 크기 조정
- API 키 권한 부여
- API Swagger 문서

다음 구성 설정을 통해 배포 크기 조정과 원격 분석을 제어할 수 있습니다.

- 각 웹 서비스 수준에 대한 시스템 로깅 및 모델 원격 분석 - 사용하도록 설정되는 경우 모든 표준 출력(stdout) 로그가 [Azure Application Insights](https://azure.microsoft.com/services/application-insights/)로 스트림됩니다. 모델 원격 분석은 사용자가 제공한 저장소에 보관됩니다. 
- 자동 크기 조정 및 동시성 제한 - 이러한 설정은 기존 클러스터 내의 부하에 따라 배포되는 컨테이너의 수를 자동으로 늘립니다. 또한 예측 대기 시간의 처리량과 일관성도 제어합니다.

## <a name="consumption"></a>Consumption 
Azure Machine Learning 모델 관리는 Swagger 문서와 함께 배포된 모델에 대한 REST API를 만듭니다. LOB(기간 업무) 응용 프로그램의 일부로 예측을 가져오기 위해 API 키 및 모델 입력으로 REST API를 호출하여 배포된 모델을 사용할 수 있습니다. REST API를 호출하는 샘플 코드는 Java, [Python](https://github.com/CortanaAnalyticsGallery-Int/digit-recognition-cnn-tf/blob/master/client.py) 및 C# 언어용 GitHub에서 사용할 수 있습니다. Azure Machine Learning 모델 관리 CLI는 이러한 REST API를 쉽게 사용할 수 있는 방법을 제공합니다. 단일 CLI 명령, Swagger 사용 가능한 응용 프로그램 또는 curl을 사용하여 API를 사용할 수 있습니다. 

## <a name="retraining"></a>재학습 
Azure Machine Learning 모델 관리는 모델을 다시 학습하는 데 사용할 수 있는 API를 제공합니다. 또한 이 API를 사용하여 기존 배포를 업데이트된 버전의 모델로 업데이트할 수도 있습니다. 데이터 과학 워크플로의 일부로 실험 환경에서 모델을 다시 만듭니다. 그런 다음 모델 관리에 모델을 등록하고 기존 배포를 업데이트합니다. 업데이트는 단일 UPDATE CLI 명령을 사용하여 수행됩니다. UPDATE 명령은 API URL 또는 키를 변경하지 않고 기존 배포를 업데이트합니다. 모델을 사용하는 응용 프로그램은 코드를 변경하지 않고도 계속 작동하며, 새 모델을 사용하여 더 나은 예측을 가져오기 시작합니다.

이러한 개념을 설명하는 전체 워크플로는 다음 그림과 같이 캡처됩니다.

![](media/model-management-overview/modelmanagementworkflow.png)

## <a name="frequently-asked-questions-faq"></a>질문과 대답(FAQ) 
- **어떤 데이터 형식이 지원되나요? NumPy 배열을 웹 서비스에 대한 입력으로 직접 전달할 수 있나요?**

   generate_schema SDK를 사용하여 만든 스키마 파일을 제공하는 경우 NumPy 및/또는 Pandas DF를 전달할 수 있습니다. 또한 모든 JSON 직렬화 가능 입력도 전달할 수 있습니다. 이미지를 이진 인코딩 문자열로 전달할 수도 있습니다.

- **웹 서비스에서 여러 입력을 지원하거나 서로 다른 입력을 구문 분석하나요?**

   예, 하나의 JSON 요청에 패키지된 여러 입력을 사전으로 사용할 수 있습니다. 각 입력은 하나의 고유한 사전 키에 해당합니다.

- **웹 서비스에 대한 요청으로 활성화된 호출이 차단 호출 또는 비동기 호출인가요?**

   서비스가 CLI 또는 API의 일부로 실시간 옵션을 사용하여 만들어진 경우 차단/동기 호출입니다. 빠른 실시간이 될 것으로 예상됩니다. 클라이언트 쪽에서 비동기 HTTP 라이브러리를 사용하여 호출할 수 있지만, 클라이언트 스레드를 차단할 수는 없습니다.

- **웹 서비스에서 동시에 처리할 수 있는 요청 수는 얼마인가요?**

   클러스터 및 웹 서비스 크기 조정에 따라 다릅니다. 서비스를 복제본 수의 100배로 확장한 다음, 많은 요청을 동시에 처리할 수 있습니다. 또한 복제본당 최대 동시 요청 수를 구성하여 서비스 처리량을 높일 수 있습니다.

- **웹 서비스 큐에 대기할 수 있는 최대 요청 수는 얼마인가요?**

   구성할 수 있습니다. 기본적으로 단일 복제본당 최대 10개로 설정되지만, 응용 프로그램 요구 사항에 따라 늘리거나 줄일 수 있습니다. 일반적으로 큐에 대기하는 요청 수를 늘리면, 서비스 처리량이 늘어나지만 더 높은 백분위수의 대기 시간도 늘어납니다. 대기 시간을 일관되게 유지하려면 큐를 낮은 값(1-5)으로 설정하고 복제본의 수를 늘려서 처리량을 처리하는 것이 좋습니다. 또한 자동 크기 조정을 사용하도록 설정하여 복제본 수가 부하에 따라 자동으로 조정되도록 할 수 있습니다. 

- **동일한 컴퓨터 또는 클러스터를 여러 웹 서비스 끝점에 사용할 수 있나요?**

   그렇습니다. 동일한 클러스터에서 서비스/끝점 수의 100배까지 실행할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
모델 관리를 시작하려면 [모델 관리 구성](deployment-setup-configuration.md)을 참조하세요.
