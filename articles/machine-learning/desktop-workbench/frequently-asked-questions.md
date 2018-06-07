---
title: Azure Machine Learning 2017 미리 보기 FAQ | Microsoft Docs
description: 이 문서는 Azure Machine Learning 미리 보기 기능에 대해 일반적으로 묻는 질문과 대답을 포함합니다
services: machine-learning
author: serinakaye
ms.author: serinak
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 08/30/2017
ms.openlocfilehash: 864e9ff5551637a93b957e5011a8857072c6eaaf
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="azure-machine-learning-frequently-asked-questions"></a>Azure Machine Learning 질문과 대답

Azure Machine Learning은 기계 학습 및 AI 모델을 만들고 테스트, 관리 및 배포할 수 있는 완전히 관리되는 Azure 서비스입니다. 서비스 및 다운로드 가능 응용 프로그램은 클라우드, 온-프레미스 및 Edge를 이용하여 처리 능력, 속도 및 유연성을 가진 학습, 배포, 관리 및 모니터링을 제공하는 코드 우선 방식을 제공합니다. 또는 Azure Machine Learning Studio는 코딩이 필요하지 않은 브라우저 기반의 시각적 끌어서 놓기 저작 환경을 제공합니다. 

## <a name="general-product-questions"></a>일반적인 제품 질문

**다른 Azure 서비스가 필요합니까?**

Azure Blob Storage 및 Azure Container Registry는 Azure Machine Learning에서 사용됩니다. 또한 데이터 과학 VM 또는 HDInsight 클러스터와 같은 계산 리소스를 프로비전해야 합니다. 또한 [Azure Container Service](https://docs.microsoft.com/azure/aks)와 같은 웹 서비스를 배포할 때 계산 및 호스팅이 필요합니다.

**Azure Machine Learning은 SQL Server 2017의 Microsoft Machine Learning 서비스와 어떤 관계가 있습니까?**   

SQL Server 2017의 Machine Learning 서비스는 기계 학습 작업을 데이터베이스 워크플로에 통합하도록 확장 및 크기 조정할 수 있는 플랫폼입니다. 온-프레미스 솔루션이 필요한, 예를 들어 데이터 이동이 많은 비용을 초래하고 부적합한 시나리오에 최적입니다. 반면에 클라우드 또는 하이브리드 워크로드는 새 Azure 서비스에 매우 적합합니다. 

**Python 및 R을 모두 지원합니까? C++ 등의 다른 프로그래밍 언어의 경우 어떻습니까?**

현재 지원 Python만 지원합니다. R 통합에 대해 연구 중이며 곧 사용할 수 있게 될 것으로 예상합니다. 

**Azure Machine Learning은 Spark용 Microsoft Machine Learning과 어떤 관계가 있습니까?**

MMLSpark는 생산성, 간편한 실험 및 최신 알고리즘을 강조한 Apache Spark용 심층 학습 및 데이터 과학 도구를 제공합니다. MMLSpark는 Spark Machine Learning 파이프라인과 Microsoft Cognitive Toolkit 및 OpenCV의 통합을 제공합니다. 이미지 및 텍스트 데이터에 대해 강력하고 확장성이 뛰어난 예측 및 분석 모델을 만들 수 있습니다. MMLSpark는 오픈 소스 라이선스에 따라 사용할 수 있으며 AML Workbench에 사용 가능 모델 및 알고리즘 집합으로 포함되어 있습니다. MMLSpark에 대한 자세한 내용은 제품 설명서를 참조하세요. 

**새로운 도구와 서비스에서 지원되는 Spark의 버전은 무엇입니까?**

Workbench는 현재 Apache Spark 2.1과 호환되는 MMLSpark 버전 0.8을 포함하고 지원합니다. 또한 Linux 가상 머신에서 MMLSpark 0.8의 GPU 지원 Docker 이미지를 사용하는 옵션이 있습니다.

## <a name="experimentation-service"></a>실험 서비스

**Azure Machine Learning 실험 서비스란 무엇입니까?**

실험 서비스는 기계 학습 실험을 다음 수준으로 높여 주는 관리되는 Azure 서비스입니다. 로컬 컴퓨터에서 또는 클라우드에서 실험을 만들 수 있습니다. 데스크톱에서 프로토타입을 빨리 만든 다음 가상 머신 또는 Spark 클러스터로 확장합니다. 최신 GPU 기술을 탑재한 Azure VM을 사용하면 심층 학습에 빠르게 효율적으로 참여할 수 있습니다. 또한 코드 추적, 구성 및 공동 작업을 위해 기존 워크플로에 쉽게 통합할 수 있도록 Git에 심층 통합을 포함했습니다. 

**실험 서비스에 대한 수수료를 지불하는 방법**

Azure Machine Learning 실험 서비스와 연결된 처음 두 사용자는 무료입니다. 추가 사용자는 $50/월의 공개 미리 보기 요율로 청구됩니다. 가격 책정 및 청구에 대해 자세한 내용을 보려면 가격 책정 페이지를 방문하십시오.

**실행하는 실험 수에 따라 비용이 청구되나요?**

아니요. 실험 서비스에서는 원하는 수만큼 많은 실험을 수행할 수 있으며, 비용은 사용자 수에 따라서만 청구됩니다. 실험 계산 리소스의 비용은 개별적으로 청구됩니다. 솔루션에 가장 적합한 모델을 찾을 수 있도록 여러 실험을 수행하는 것이 좋습니다.   

**내가 사용할 수 있는 계산 및 저장소 리소스의 구체적인 종류는 무엇인가요?**

실험 서비스는 로컬 컴퓨터(직접 또는 Docker 기반), [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) 및 [HDInsight](https://azure.microsoft.com/services/hdinsight/)에서 실험을 실행할 수 있습니다. 이 서비스는 실행 출력을 저장하기 위해 [Azure Storage](https://azure.microsoft.com/services/storage/) 계정에 액세스하며 버전 관리 및 Git 저장소를 위해 [Visual Studio Team Service](https://azure.microsoft.com/services/visual-studio-team-services/) 계정을 이용할 수 있습니다. 참고로 사용한 계산 및 저장소 리소스는 각각의 가격에 따라 개별적으로 비용이 청구됩니다.  


## <a name="model-management"></a>모델 관리

**Azure Machine Learning 모델 관리란 무엇인가요?**

Azure Machine Learning 모델 관리는 데이터 과학자와 개발 운영팀이 모델 관리 서비스를 사용하여 다양한 환경에 예측 모델을 배포할 수 있는 관리되는 Azure 서비스입니다. Git 리포지토리 및 Docker 컨테이너는 추적 기능 및 반복성을 제공합니다. 모델은 클라우드, 온-프레미스 또는 Edge에 안정되게 배포할 수 있습니다. 프로덕션 단계에 도달한 후 모델 성능을 관리한 다음 성능이 저하되면 전향적으로 다시 학습할 수 있습니다. 로컬 컴퓨터의 모델을 [Azure VM](https://azure.microsoft.com/services/virtual-machines/), [HDInsight](https://azure.microsoft.com/services/hdinsight/)의 Spark 또는 Kubernetes 조율 [Azure Container Service](https://azure.microsoft.com/services/container-service/) 클러스터에 배포할 수 있습니다.  

**"모델"이란 무엇인가요?**

모델은 모델 관리를 위해 메시지가 표시된 실험 실행의 출력입니다. 호스팅 계정에 등록된 모델은 다시 학습 또는 버전 반복을 통해 업데이트된 모델을 포함하여 사용자의 계획에 비추어 계산됩니다.

**“관리되는 모델”이란 무엇인가요?**

이 모델은 교육 프로세스의 결과이며 교육 데이터에 기계 학습 알고리즘을 적용하는 것입니다. 모델 관리 서비스를 사용하면 모델을 웹 서비스로 배포하고, 다양한 버전의 모델을 관리하며, 모델의 성능 및 메트릭을 모니터링할 수 있습니다. "관리되는" 모델은 Azure Machine Learning 모델 관리 계정에 등록된 모델입니다. 판매를 예측하려는 시나리오를 예로 들어 보겠습니다. 실험 단계 중에 여러 다른 데이터 집합 또는 알고리즘을 사용하여 여러 모델을 생성합니다. 정확도가 서로 다른 4개의 모델을 생성하고 정확도가 가장 높은 모델만 등록하도록 선택했습니다. 이때 등록된 모델은 첫 번째 관리되는 모델이 됩니다.
 
**"배포"란 무엇인가?**

모델 관리를 사용하면 Azure에서 모델을 패키지에 포함된 웹 서비스 컨테이너로 배포할 수 있습니다. 이러한 웹 서비스는 REST API를 사용하여 호출할 수 있습니다. 각 웹 서비스는 단일 배포로 계산되며 활성 배포의 총 수가 요금제 계산에 포함됩니다. 판매 예측 예제에서는 성능이 최고인 모델을 배포하여 요금제를 한 배포만큼 증가시킵니다. 그런 다음 다시 학습하고 다른 버전을 배포하면 두 개의 배포를 갖게 됩니다. 새 모델이 더 우수한 것으로 판별하여 원래 모델을 삭제하면 배포 수가 하나만큼 줄어듭니다.  

**내 배포에 사용할 수 있는 구체적 계산 리소스는 무엇입니까?** 

모델 관리는 사용자의 배포를 [Azure Container Service](https://azure.microsoft.com/services/container-service/)에 등록된 Docker 컨테이너로, [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/)로, 또는 로컬 컴퓨터에서 실행할 수 있습니다. 추가 배포 대상은 곧 추가됩니다. 참고로 사용한 계산 리소스는 각각의 가격에 따라 개별적으로 비용이 청구됩니다.     

**Azure Machine Learning Model Management를 사용하면 실험 서비스 이외의 도구로 빌드한 모델을 배포할 수 있나요?**

실험 서비스를 사용하여 만든 모델을 배포할 때 최상의 환경을 가져옵니다. 그러나 다른 프레임워크 및 도구를 사용하여 작성된 모델을 배포할 수 있습니다. MMLSpark, TensorFlow, Microsoft Cognitive Toolkit, scikit-learn, Keras 등 다양한 모델을 지원합니다. 

**나만의 Azure 리소스를 사용할 수 있나요?**

예, 실험 서비스 및 모델 관리는 여러 Azure 데이터 저장소, 계산 워크로드 및 기타 서비스와 함께 작동합니다. 필수 Azure 서비스에 대한 자세한 내용은 기술 설명서를 참조하세요.

**온-프레미스와 클라우드 배포 시나리오를 모두 지원합니까?**

예. Docker 컨테이너를 통해 온-프레미스 및 클라우드 배포 시나리오를 지원합니다. 로컬 실행 대상은 단일 노드 Docker 배포, [ML 서비스 포함 Microsoft SQL Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-services), Hadoop 또는 Spark를 포함합니다. 또한 Azure Container Service와 Kubernetes, HDInsight 또는 Spark를 통한 클러스터형 배포를 포함하여 Docker를 통한 클라우드 배포도 지원합니다. Edge 시나리오는 Docker 컨테이너 및 Azure IOT Edge를 통해 지원됩니다. 

**다른 호스트에서 Azure Machine Learning CLI를 사용하여 만든 Docker 이미지를 실행할 수 있나요?**

예. 호스트에 Docker 이미지를 호스팅하기에 충분한 계산 리소스가 있다면 어떤 Docker 호스트에서도 이미지를 웹 서비스로 사용할 수 있습니다.

**배포 된 모델의 재학습을 지원하나요?**

예, 동일한 모델의 여러 버전을 배포할 수 있습니다. 모델 관리는 모든 업데이트된 모델 및 이미지에 대한 서비스 업데이트를 지원합니다.

## <a name="workbench"></a>워크벤치

**Azure Machine Learning Workbench란 무엇인가요?**

Azure Machine Learning Workbench는 전문 데이터 과학자를 위해 만든 자매 응용 프로그램입니다. Windows 및 Mac에서 사용할 수 있는 Machine Learning Workbench는 기계 학습 솔루션에 대한 개요, 관리 및 제어를 제공합니다. Machine Learning Workbench는 Microsoft 및 오픈 소스 커뮤니티 둘 다에서 나온 첨단 AI 프레임워크에 대한 액세스 권한을 포함합니다. TensorFlow, Microsoft Cognitive Toolkit, Spark ML, scikit-learn 등 가장 인기 있는 데이터 과학 도구 키트를 포함시켰습니다. 또한 Jupyter 노트북, PyCharm 및 Visual Studio Code 같은 인기 있는 데이터 과학 IDE와 통합할 수 있도록 했습니다. Machine Learning Workbench는 구조화되었는지 여부와 상관없이 데이터를 신속하게 샘플링, 이해 및 준비하는 데이터 준비 기능을 기본 제공합니다. [PROSE](https://microsoft.github.io/prose/)라는 새 데이터 준비 도구는 Microsoft Research의 첨단 기술을 기반으로 합니다.  

**Workbench는 IDE인가요?**

번호 Machine Learning Workbench는 Jupyter Notebooks, Visual Studio Code 및 PyCharm 같은 인기 있는 IDE에 대한 자매 제품으로 설계되었지만 완전한 기능을 하는 IDE가 아닙니다. Machine Learning Workbench는 몇 가지 기본적인 텍스트 편집 기능을 제공하지만 디버깅, IntelliSense 및 기타 널리 사용되는 IDE 기능을 지원하지 않습니다. 코드 배포, 편집 및 디버깅을 위해서는 사용자가 즐겨 쓰는 IDE를 사용하는 것이 좋습니다. 또한 [AI용 Visual Studio Code 도구](https://www.visualstudio.com/downloads/ai-tools-vscode)를 사용해 볼 수 있습니다.

**Azure Machine Learning Workbench 사용은 유료입니까?**

번호 Azure Machine Learning Workbench는 무료 응용 프로그램입니다. 필요한 만큼 많은 컴퓨터에 많은 사용자를 위해 다운로드할 수 있습니다. Azure Machine Learning Workbench를 사용하려면 실험 계정이 있어야 합니다. 에서도 확인할 수 있습니다.  

**명령줄 기능을 지원하나요?**

예, Azure Machine Learning은 완전한 CLI 인터페이스를 제공합니다. Machine Learning CLI는 기본적으로 Azure Machine Learning Workbench와 함께 설치됩니다. 또한 Azure용 Linux Data Science 가상 머신의 일부로도 제공되며 [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)에 통합됩니다.


**내 Jupyter 노트북을 Workbench에서 사용할 수 있나요?**

예! 브라우저를 클라이언트로 사용할 수 있는 것처럼 Workbench를 클라이언트 호스팅 응용 프로그램으로 하여 Workbench에서 Jupyter 노트북을 실행할 수 있습니다. 

**어떤 Jupyter 노트북 커널이 지원되나요?**

Workbench에 포함된 Jupyter의 현재 버전은 Python 3 커널 및 aml_config 폴더의 각 "runconfig" 파일에 대한 추가 커널을 시작합니다. 지원되는 구성은 다음을 포함합니다.
- 로컬 Python
- 로컬 또는 원격 Docker의 Python

## <a name="data-formats-and-capabilities"></a>데이터 형식 및 기능

**Workbench에서 현재 데이터 수집을 위해 지원되는 파일 형식은 무엇인가요?**

Workbench의 데이터 준비 도구는 현재 다음 형식에서의 수집을 지원합니다. 
- CSV, TSV 등의 구분 기호로 분리된 파일.  
- 고정 폭 파일
- 일반 텍스트 파일
- Excel(.xls/xlsx)
- JSON 파일
- Parquet 파일 
- 사용자 지정 파일(스크립트) 사용자의 솔루션에 추가 소스에서의 데이터 수집을 필요한 경우 Python 코드를 사용하여 다음을 수행할 수 있습니다... 

**현재 어떤 데이터 저장 위치가 지원되나요?**

공용 미리 보기의 경우 Workbench는 다음 위치에서의 데이터 수집을 지원합니다. 
- 로컬 하드 드라이브 또는 매핑된 네트워크 저장소 위치
- Azure BLOB 또는 Azure Storage(Azure 구독 필요)
- Azure SQL Server
- Microsoft SQL Server에 대한 연결 문자열


**어떤 종류의 데이터 wrangling, 준비 및 변환을 사용할 수 있나요?**

공개 미리 보기의 경우 Workbench는 “예제에 의한 열 유도”, “예제 의한 분할 열”, “텍스트 클러스터링”, “값 없는 핸들” 등 다수를 지원합니다.  또한 Workbench는 데이터 형식 변환, 데이터 집계(COUNT, MEAN, VARIANCE 등) 및 복합 데이터 조인도 지원합니다. 지원되는 기능의 전체 목록은 제품 설명서를 참조하세요. 

**Azure Machine Learning Workbench, 실험 또는 모델 관리가 적용하는 데이터 크기 한계가 있나요?**

아니요. 새 서비스는 데이터 제한을 적용하지 않습니다. 단, 데이터 준비, 모델 학습, 실험 또는 배포를 수행하는 환경에서 도입되는 제한이 있습니다. 예를 들어 로컬 환경을 학습의 대상으로 하는 경우 하드 드라이브의 사용 가능한 공간에 의해 제한을 받습니다. 또는 HDInsight 대상으로 하는 경우 모든 관련된 크기 또는 계산 제약에 의해 제한을 받습니다. 

## <a name="algorithms-and-libraries"></a>알고리즘 및 라이브러리

**Azure Machine Learning Workbench에서 지원되는 알고리즘은 무엇인가요?**

당사의 미리 보기 제품 및 서비스는 오픈 소스 커뮤니티의 최고 알고리즘 지원을 포함합니다. TensorFlow, scikit-learn, Apache Spark 및 Microsoft Cognitive Toolkit 등 광범위한 알고리즘 및 라이브러리를 지원합니다. 또한 Azure Machine Learning Workbench는 [Microsoft revoscalepy](https://docs.microsoft.com/sql/advanced-analytics/python/what-is-revoscalepy) 패키지도 패키징합니다.

**Azure Machine Learning은 Microsoft Cognitive Toolkit와 어떤 관계가 있나요?**

[Microsoft Cognitive Toolkit](https://www.microsoft.com/cognitive-toolkit/)는 새 도구 및 서비스에서 지원하는 많은 프레임워크 중 하나입니다. Cognitive Toolkit은 Feed-Forward Deep Neural Networks, Convolutional Nets, Sequence-to-Sequence 및 Recurrent Networks 등 인기 있는 기계 학습 모델을 사용하고 조합할 수 있는 통합된 심층 학습 도구 키트입니다. Microsoft Cognitive Toolkit에 대한 자세한 내용을 확인하려면 당사 [제품 설명서](https://docs.microsoft.com/cognitive-toolkit/)를 참조하세요. 