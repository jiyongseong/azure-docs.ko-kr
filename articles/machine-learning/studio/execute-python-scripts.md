---
title: Python Machine Learning 스크립트 실행 | Microsoft Docs
description: Azure Machine Learning에서 Python 스크립트를 지원하는 데 기본이 되는 디자인 원칙 및 기본 사용 시나리오, 기능 및 제한 사항을 간략히 설명합니다.
keywords: python 기계 학습, pandas, python pandas, python 스크립트, python 스크립트 실행
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: ee9eb764-0d3e-4104-a797-19fc29345d39
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.openlocfilehash: 0efa2a7604b41b791ed3717b7f2b667b7181085d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio"></a>Azure Machine Learning Studio에서 Python 기계 학습 스크립트 실행

이 항목에서는 Azure Machine Learning에서 현재 Python 스크립트를 지원하는 데 기본이 되는 디자인 원칙을 설명합니다. 다음을 비롯하여 제공되는 기본 기능도 요약되어 있습니다.

- 기본 사용 시나리오 실행
- 웹 서비스에서 실험 점수 매기기
- 기존 코드 가져오기 지원
- 시각화 내보내기
- 감독 모드 기능 선택 수행
- 일부 제한 사항 파악

[Python](https://www.python.org/) 은 여러 데이터 과학자들의 도구 상자에 필수적인 도구입니다. 여기에는:

* 세련되고 간결한 구문, 
* 플랫폼 간 지원, 
* 방대한 양의 강력한 라이브러리 컬렉션 및 
* 완성도 있는 개발 도구가 포함되어 있습니다. 

Python은 기계 학습 모델링에서 일반적으로 사용되는 워크플로의 다음과 같은 모든 단계에서 사용되고 있습니다.

- 데이터 수집 및 처리 
- 기능 생성
- 모델 학습 
- 모델 유효성 검사
- 모델 배포

Azure Machine Learning Studio에서는 기계 학습 실험의 다양한 부분에 Python 스크립트를 포함할 수 있으며, 해당 스크립트를 Microsoft Azure의 웹 서비스로 원활하게 게시할 수도 있습니다.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="design-principles-of-python-scripts-in-machine-learning"></a>Machine Learning에서 Python 스크립트의 디자인 원칙

Azure Machine Learning Studio에서 Python의 기본 인터페이스는 그림 1에 표시된 [Python 스크립트 실행][execute-python-script] 모듈을 통해 실행됩니다.

![image1](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![image2](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

그림 1. **Python 스크립트 실행** 모듈입니다.

Azure ML Studio의 [Python 스크립트 실행][execute-python-script] 모듈에서는 R의 동일 모듈인 [R 스크립트 실행][execute-r-script] 모듈과 마찬가지로 최대 세 개의 입력을 허용하고 최대 두 개의 출력을 생성합니다(다음 섹션에서 설명함). 실행할 Python 코드는 `azureml_main`이라는 특별하게 명명된 진입점 함수로 매개 변수 상자에 입력됩니다. 다음은 이 모듈을 구현하는 데 사용하는 핵심 디자인 원칙입니다.

1. *Python 사용자에게 자연스러워야 합니다.* 대부분의 Python 사용자는 코드를 모듈 내의 함수로 고려합니다. 따라서 최상위 모듈에 실행 가능 문을 많이 포함하는 경우는 거의 없습니다. 따라서 스크립트 상자도 일련의 문장이 아니라 특별하게 명명된 Python 함수를 사용합니다. 함수에 노출된 개체는 [Pandas](http://pandas.pydata.org/) 데이터 프레임 및 [NumPy](http://www.numpy.org/) 배열과 같은 표준 Python 라이브러리입니다.
2. *로컬 및 클라우드 실행 간의 충실도가 높아야 합니다.* Python 코드를 실행하는 데 사용하는 백 엔드는 널리 사용되는 플랫폼 간 과학 Python 배포인 [Anaconda](https://store.continuum.io/cshop/anaconda/)를 기반으로 합니다. 가장 일반적인 Python 패키지가 거의 200개 포함되어 있습니다. 따라서 데이터 과학자는 자신의 로컬 Azure Machine Learning 호환 Anaconda 환경에서 코드를 디버그하고 한정할 수 있습니다. 그런 후에 [IPython](http://ipython.org/) Notebook 또는 [Visual Studio용 Python 도구](http://aka.ms/ptvs)와 같은 기존 개발 환경을 사용하여 코드를 Azure ML 실험의 일부분으로 실행할 수 있습니다. `azureml_main` 진입점은 바닐라 Python 함수이며 Azure ML 관련 코드 또는 SDK가 설치되어 있지 않아도 ****작성할 수 있습니다.
3. *다른 Azure Machine Learning 모듈로 원활하게 구성할 수 있어야 합니다.* [Python 스크립트 실행][execute-python-script] 모듈이 입력 및 출력으로 표준 Azure Machine Learning 데이터 집합을 허용합니다. 기본 프레임워크는 Azure ML 및 Python 런타임을 효율적이며 투명한 방식으로 연결합니다. 따라서 Python을 기존 Azure ML 워크플로(R 및 SQLite를 호출하는 워크플로 포함)와 함께 사용할 수 있습니다. 이를 통해 데이터 과학자는 다음과 같은 워크플로를 작성할 수 있습니다.
   * 데이터 전처리 및 정리에 Python 및 Pandas를 사용하는 워크플로
   * SQL 변환으로 데이터를 공급해 여러 데이터 집합을 연결하여 기능을 생성하는 워크플로
   * Azure Machine Learning의 알고리즘을 사용하여 모델 학습을 수행하는 워크플로 
   * R을 사용하여 결과 평가 및 사후 처리.


## <a name="basic-usage-scenarios-in-ml-for-python-scripts"></a>ML의 Python 스크립트 기본 사용 시나리오

이 섹션에서는 [Python 스크립트 실행][execute-python-script] 모듈의 몇 가지 기본 사용에 대해 살펴봅니다. Python 모듈에 입력하는 내용은 Pandas 데이터 프레임으로 표시됩니다. 이 함수는 튜플, 목록 또는 Numpy 배열과 같이 Python [시퀀스](https://docs.python.org/2/c-api/sequence.html) 내부에 패키지된 단일 Pandas 데이터 프레임을 반환해야 합니다. 그러면 이 시퀀스의 첫 번째 요소가 모듈의 첫 번째 출력 포트에 반환됩니다. 이 스키마는 그림 2에 표시됩니다.

![image3](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

그림 2. 입력 포트를 매개 변수에 매핑 및 출력 포트에 값 반환.

입력 포트가 `azureml_main` 함수에 매핑되는 방법에 대한 자세한 의미 체계는 표 1에 표시되어 있습니다.

![image1T](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

표 1. 입력 포트를 함수 매개 변수에 매핑.

입력 포트와 함수 매개 변수 간의 매핑은 위치와 관련됩니다.

- 첫 번째로 연결된 입력 포트는 함수의 첫 번째 매개 변수에 매핑됩니다. 
- 두 번째 입력(연결된 경우)은 함수의 두 번째 매개 변수에 매핑됩니다.

Python Pandas에 대한 자세한 내용 및 Python Pandas를 사용하여 효율적으로 데이터를 조작하는 방법은 W. McKinney의 저서 *Python for Data Analysis*(O'Reilly, 2012)에서 찾을 수 있습니다. 


## <a name="translation-of-input-and-output-types"></a>입력 및 출력 유형 변환 
Azure ML의 입력 데이터 집합은 Pandas에서 데이터 프레임으로 변환됩니다. 출력 데이터 프레임은 Azure ML 데이터 집합으로 다시 변환됩니다. 다음과 같은 변환이 수행됩니다.

1. 문자열과 숫자 열은 현상태 그대로 변환되고, 데이터 집합의 누락된 값은 Pandas에서 ‘NA’ 값으로 변환됩니다. 그 반대의 경우에도 마찬가지로 변환됩니다(Pandas의 NA 값은 Azure ML의 누락된 값으로 변환됨).
2. Pandas의 인덱스 벡터는 Azure ML에서 지원되지 않습니다. Python 함수의 모든 입력 데이터 프레임에는 항상 0부터 시작하여 행 수에서 1을 뺀 인덱스까지 64비트의 숫자 인덱스가 있습니다. 
3. Azure ML 데이터 집합에는 중복된 열 이름과 문자열이 아닌 열 이름이 있을 수 없습니다. 출력 데이터 프레임에 숫자가 아닌 열이 포함된 경우 프레임워크를 통해 해당 열에서 `str` 을 호출합니다. 마찬가지로 중복된 모든 열 이름은 손상된 것으로 자동 처리하여 이름을 고유하게 유지합니다. 접미사(2)는 첫 번째 중복 항목에 추가되고 (3)은 두 번째 중복 항목에 추가되는 식입니다.


## <a name="operationalizing-python-scripts"></a>Python 스크립트 운영 가능화

점수 매기기 실험에서 모든 [Python 스크립트 실행][execute-python-script] 모듈은 웹 서비스로 게시될 때 호출됩니다. 예를 들어 그림 3에서는 단일 Python 식을 평가하는 코드가 포함된 점수 매기기 실험을 보여줍니다. 

![image4](./media/execute-python-scripts/figure3a.png)

![image5](./media/execute-python-scripts/python-script-with-python-pandas.png)

그림 3. Python 식을 평가하기 위한 웹 서비스입니다.

이 실험에서 작성된 웹 서비스는 다음을 수행합니다.

- Python 식(문자열)을 입력으로 사용
- Python 인터프리터로 식 전송 
- 식과 계산된 결과가 모두 포함된 테이블 반환


## <a name="importing-existing-python-script-modules"></a>기존 Python 스크립트 모듈 가져오기

대부분의 데이터 과학자들이 일반적으로 사용하는 사례는 기존 Python 스크립트를 Azure ML 실험으로 통합하는 것입니다. 모든 코드를 연결하여 단일 스크립트 상자에 붙여넣는 대신 [Python 스크립트 실행][execute-python-script] 모듈에서는 세 번째 입력 포트에서 Python 모듈을 포함하는 zip 파일을 허용합니다. 이 파일은 런타임에 입력 프레임워크에서 압축이 풀리며, 파일의 내용은 Python 인터프리터의 라이브러리 경로에 추가됩니다. 그러면 `azureml_main` 진입점 함수가 이러한 모듈을 직접 가져올 수 있습니다.

예를 들어, 간단한 “Hello, World” 함수를 포함하는 Hello.py라는 파일을 살펴보겠습니다.

![image6](./media/execute-python-scripts/figure4.png)

그림 4. Hello.py 파일의 사용자 정의 함수

다음으로 Hello.py을 포함하는 Hello.zip이라는 파일을 만듭니다.

![image7](./media/execute-python-scripts/figure5.png)

그림 5. 사용자 정의 Python 코드가 포함된 Zip 파일.

zip 파일을 데이터 집합으로 Azure Machine Learning Studio에 업로드합니다. 그런 다음 이 그림에 표시된 대로 **Python 스크립트 실행** 모듈의 세 번째 입력 포트에 Hello.zip 파일을 연결하여 해당 파일에서 Python 코드를 사용하는 실험을 만들어서 실행합니다.

![image8](./media/execute-python-scripts/figure6a.png)

![image9](./media/execute-python-scripts/figure6b.png)

그림 6. zip 파일로 업로드된 사용자 정의 Python 코드를 사용하는 샘플 실험.

모듈 출력에는 zip 파일의 패키지가 해제되었으며 `print_hello` 함수가 실행되었음이 표시됩니다.
 
![image10](./media/execute-python-scripts/figure7.png)

그림 7. [Python 스크립트 실행][execute-python-script] 모듈에서 사용 중인 사용자 정의 함수.


## <a name="working-with-visualizations"></a>시각화 작업

브라우저에서 시각화할 수 있는 MatplotLib를 사용하여 만든 그림은 [Python 스크립트 실행][execute-python-script]을 통해 반환할 수 있습니다. 그러나 R을 사용할 때 그림은 현상태 그대로 이미지로 자동 리디렉션되므로, Azure Machine Learning으로 다시 반환하려는 경우 명시적으로 모든 그림을 PNG 파일로 저장해야 합니다. 

MatplotLib에서 이미지를 생성하려면 다음 절차를 완료해야 합니다.

* 기본 Qt 기반 렌더러에서 “AGG”로 백 엔드 전환 
* 새 그림 개체 만들기 
* 축을 가져오고 축에 모든 그림을 생성 
* PNG 파일에 그림 저장 

Pandas에서 scatter_matrix 함수를 사용하여 산점도 행렬을 생성하는 이 프로세스는 다음 그림 8에 설명되어 있습니다.

![image1v](./media/execute-python-scripts/figure-v1-8.png)

그림 8. MatplotLib 그림을 이미지에 저장하기 위한 코드

그림 9에서는 이전에 표시된 스크립트를 사용하여 두 번째 출력 포트를 통해 그림을 반환하는 실험을 보여줍니다.

![image2v](./media/execute-python-scripts/figure-v2-9a.png) 

![image2v](./media/execute-python-scripts/figure-v2-9b.png) 

그림 9. Python 코드에서 생성된 그림 시각화.

여러 수치를 서로 다른 이미지에 저장하여 반환하고, Azure Machine Learning 런타임 시 모든 이미지를 선택한 다음, 시각화를 위해 연결할 수 있습니다.


## <a name="advanced-examples"></a>고급 예제

Azure Machine Learning에서 설치되는 Anaconda 환경에는 NumPy, SciPy, Scikits-Learn 등의 일반 패키지가 포함되어 있습니다. 이러한 패키지는 기계 학습 파이프라인의 여러 데이터 처리 작업에 효율적으로 사용할 수 있습니다. 예를 들어 다음 실험과 스크립트에서는 데이터 집합의 기능 중요도 점수를 계산하기 위한 Scikits-Learn의 앙상블 학습자 사용에 대해 설명합니다. 점수는 다른 ML 모델에 공급하기 전에 감독 모드 기능 선택을 수행하는 데 사용할 수 있습니다.

중요도 점수를 계산한 다음 이 점수에 따라 기능의 순서를 지정하는 데 사용되는 Python 함수는 다음과 같습니다.

![image11](./media/execute-python-scripts/figure8.png)

그림 10. 점수별로 기능의 순위를 지정하는 기능.
  다음 실험에서는 Azure Machine Learning의 “Pima Indian Diabetes” 데이터 집합에 있는 기능의 중요도 점수를 계산하여 반환합니다.

![image12](./media/execute-python-scripts/figure9a.png)
![image13](./media/execute-python-scripts/figure9b.png)    

그림 11. Pima Indian Diabetes 데이터 집합에서 기능의 순위를 지정하는 실험.

## <a name="limitations"></a>제한 사항
[Python 스크립트 실행][execute-python-script]에는 현재 다음과 같은 제한 사항이 있습니다.

1. *샌드박스 실행.* 현재 Python 런타임은 샌드박스로 작동하므로, 지속적인 방식으로 네트워크나 로컬 파일 시스템에 액세스할 수 없습니다. 모듈이 완료되면 로컬에 저장된 모든 파일이 격리되어 삭제됩니다. Python 코드는 현재 디렉터리와 하위 디렉터리를 제외하고, 코드가 실행되는 컴퓨터에 있는 대부분의 디렉터리에 액세스할 수 없습니다.
2. *정교한 개발 및 디버깅 지원이 부족합니다.* 현재 Python 모듈에서는 Intellisense와 디버깅 같은 IDE 기능을 지원하지 않습니다. 또한 모듈이 런타임에서 실패하는 경우 전체 Python 스택 추적을 사용할 수 있습니다. 그러나 스택 추적은 모듈의 출력 로그에서 확인해야 합니다. 현재로서는 IPython과 같은 환경에서 Python 스크립트를 개발하고 디버그한 다음 모듈에 코드를 가져오는 것이 좋습니다.
3. *단일 데이터 프레임 출력.* Python 진입점은 단일 데이터 프레임을 출력으로 반환할 수만 있습니다. 현재로서는 학습된 모델과 같은 임의의 Python 개체를 Azure Machine Learning 런타임에 직접 반환할 수 없습니다. 동일한 제한 사항이 있는 [R 스크립트 실행][execute-r-script]과 마찬가지로, 대부분의 경우 개체를 바이트 배열로 만든 다음 데이터 프레임 내부에서 반환할 수 있습니다.
4. *Python 설치를 사용자 지정할 수 없음*. 현재 사용자 지정 Python 모듈을 추가하는 유일한 방법은 이전에 설명한 zip 파일 메커니즘을 통한 것입니다. 이 방법은 작은 모듈의 경우 가능하지만, 모듈이 크거나(특히 네이티브 DLL이 있는 모듈) 모듈 수가 많은 경우 번거로울 수 있습니다. 

## <a name="conclusions"></a>결론
데이터 과학자가 [Python 스크립트 실행][execute-python-script] 모듈을 사용하여 Azure Machine Learning의 클라우드 호스팅 기계 학습 워크플로에 기존 Python 코드를 통합하고 웹 서비스의 일부로 원활하게 운영할 수 있습니다. Python 스크립트 모듈은 Azure Machine Learning의 다른 모듈과 자연스럽게 통합됩니다. 이 모듈은 데이터 탐색, 전처리, 기능 추출에서 결과 평가 및 후처리에 이르기까지 폭넓은 작업에 사용할 수 있습니다. 실행에 사용되는 백 엔드 런타임은 충분히 테스트되어 널리 사용되는 Python 배포인 Anaconda를 기반으로 합니다. 이 백 엔드를 사용하면 기존 코드 자산을 클라우드에 간편하게 등록할 수 있습니다.

[Python 스크립트 실행][execute-python-script] 모듈에 추가 기능(예: Python의 모듈을 학습하고 운영화하는 기능 및 Azure Machine Learning Studio에서의 코드 개발 및 디버깅 지원 향상)을 제공할 것으로 기대합니다.

## <a name="next-steps"></a>다음 단계
자세한 내용은 [Python 개발자 센터](https://azure.microsoft.com/develop/python/)를 참조하세요.

<!-- Module References -->
[execute-python-script]: https://msdn.microsoft.com/library/azure/cdb56f95-7f4c-404d-bde7-5bb972e6f232/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
