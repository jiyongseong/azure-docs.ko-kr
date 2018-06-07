---
title: 데이터 과학 Virtual Machine에 대한 샘플 및 연습 - Azure | Microsoft Docs
description: 데이터 과학 Virtual Machine에 대한 샘플.및 연습
keywords: 데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 3e3ee232b6342601e44d728148d32e70e6f3f00b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="samples-on-the-data-science-virtual-machines-dsvm"></a>데이터 과학 Virtual Machine(DSVM)의 샘플

DSVM은 Jupyter 노트북 형식의 아주 잘 작동하는 샘플과 Jupyter 기반이 아닌 샘플을 포함하여 제공합니다. 바탕화면 또는 응용 프로그램 메뉴에서 `Jupyter` 아이콘을 클릭하여 Jupyter를 액세스할 수 있습니다.  
> [!NOTE]
> DSVM에 Jupyter 노트북을 사용할 수 있도록 설정하려면 [액세스 Jupyter](#access-jupyter) 섹션을 참조합니다.

## <a name="quick-reference-of-samples"></a>샘플의 빠른 참조
| 샘플 범주 | 설명 | 위치 |
| ------------- | ------------- | ------------- |
| **R** 언어  | Azure 클라우드 데이터 저장소와 연결, 개방형 소스 R과 Microsoft R 비교 및 Microsoft R Server 또는 SQL Server에서 모델 운영과 같은 시나리오를 설명하는 **R**의 예제. <br/> [스크린샷](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| **Python** 언어  | Azure 클라우드 데이터 저장소와 연결 및 **Azure Machine Learning** 작업과 같은 시나리오를 설명하는 **Python** 예제.  <br/> [스크린샷](#python-language) | <br/>`~notebooks` <br/><br/>|
| **Julia** 언어  | Julia에서 그리기, Julia에서 심층 학습, Julia에서 C 및 Python 호출 등을 자세히 설명하는 **Julia** 샘플. <br/> [스크린샷](#julia-language) |<br/> **Windows**:<br/> `~notebooks/Julia_notebooks`<br/><br/>`~notebooks`<br/><br/> **Linux**:<br/> `~notebooks/julia`<br/><br/> |
| **CNTK** <br/> (Microsoft Cognitive 도구 키트)  | Microsoft에서 Cognitive Toolkit 팀에서 게시한 심층 학습 샘플.  <br/> [스크린샷](#cntk) | <br/>**Windows**:<br/> `~notebooks/CNTK/Tutorials`<br/><br/>`~/samples/CNTK-Samples-2-0/Examples`<br/><br/> **Linux**:<br/> `~notebooks/CNTK`<br/> <br/>|
| **MXNet** Notebooks  | 신경망 기반 **MXNet**을 활용하는 심층 학습 샘플 초보자용부터 고급 시나리오에 이르기까지 다양한 노트북이 있습니다.  <br/> [스크린샷](#mxnet) | <br/>`~notebooks/mxnet`<br/> <br/>|
| **Azure Machine Learning** AzureML  | 클라우드 기반 점수 매기기 워크플로에 대한 **Azure Machine Learning** Studio와 상호 작용 및 로컬에서 훈련된 모델에서 웹 서비스 끝점 생성. <br/> [스크린샷](#azureml) | <br/>`~notebooks/azureml`<br/> <br/>|
| **caffe2** | 신경망 기반 **caffe2**를 활용하는 심층 학습 샘플. 사용자가 이미지 전처리, 데이터 집합 만들기, 회귀, 미리 학습된 모델 사용과 같은 예제를 포함하여 caffe2 및 그것을 효과적으로 사용하는 방법을 익히도록 디자인된 여러 노트북이 있습니다. <br/> [스크린샷](#caffe2) | <br/>`~notebooks/caffe2`<br/><br/> |
| **H2O**   | 많은 실제 시나리오 문제에 대해 **H2O**를 활용하는 Python 기반 샘플. <br/> [스크린샷](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| **SparkML** 언어  | **Apache Spark 2.0**에서 **pySpark 2.0**을 통해 Spark의 **MLlib** 도구 키트의 특징 및 기능을 활용 하는 샘플.  <br/> [스크린샷](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/><br/> |
| **MMLSpark** 언어  | **Apache Spark**에 대한 여러 심층 학습 및 데이터 과학 도구를 제공하는 프레임워크인 **MMLSpark-Apache Spark에 대한 Microsoft Machine Learning**을 활용하는 다양한 샘플. <br/> [스크린샷](#sparkml) | <br/>`~notebooks/MMLSpark`<br/><br/> |
| **Tensorflow**  | **TensorFlow** 프레임워크를 사용하여 구현된 서로 다른 여러 신경망 샘플 및 기법 <br/> [스크린샷](#tensorflow) | <br/>`~notebooks/tensorflow`<br/><br/> |
| **XGBoost** | 분류, 회귀 등과 같은 시나리오에 대해 **XGBoost**에 들어 있는 Standard Machine Learning 샘플. <br/> [스크린샷](#xgboost) | <br/>`~samples/xgboost/demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>액세스 Jupyter 

Windows에서 **`https://localhost:9999`**로 또는 Ubuntu에서 **`https://localhost:8000`**로 이동하여 Jupyter 홈을 방문합니다.


### <a name="enabling-jupyter-access-from-browser"></a>브라우저에서 Jupyter를 액세스할 수 있도록 설정

**Windows DSVM**

바탕 화면 바로 가기에서 **`Jupyter SetPassword`**을 실행하고 프롬프트에 따라 Jupyter에 대한 암호를 설정/재설정하고 Jupyter 프로세스를 시작합니다. 
<br/>![Jupyter 예외 활성화](./media/jupyter-setpassword.png)<br/>
브라우저에서 **`https://localhost:9999`**을 방문하여 VM에서 Jupyter 프로세스를 성공적으로 시작하면 Jupyter 홈에 액세스할 수 있습니다. 브라우저를 통해 예외를 추가하고 Jupyter 액세스를 활성화하려면 스크린샷을 참조하세요.
<br/>![Jupyter 예외 활성화](./media/windows-jupyter-exception.png)<br/>
방금 설정한 새 암호를 사용하여 로그인합니다.
<br/>
**Linux DSVM**

브라우저에서 **`https://localhost:8000`**을 방문하여 VM에서 Jupyter 홈에 액세스할 수 있습니다. 브라우저를 통해 예외를 추가하고 Jupyter 액세스를 활성화하려면 스크린샷을 참조하세요.
<br/>![Jupyter 예외 활성화](./media/ubuntu-jupyter-exception.png)<br/>
DSVM에 로그인할 때와 동일한 암호를 사용하여 로그인합니다.
<br/>

**Jupyter 홈**
<br/>![Jupyter 홈](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>R 언어 
<br/>![R 샘플](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Python 언어
<br/>![Python 샘플](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Julia 언어 
<br/>![Julia 샘플](./media/julia-samples.png)<br/>

## <a name="cntk"></a>CNTK 
<br/>![CNTK 샘플](./media/cntk-samples2.png)<br/>
<br/>![CNTK 샘플](./media/cntk-samples.png)<br/>

## <a name="mxnet"></a>MXNet
<br/>![MXnet 샘플](./media/mxnet-samples.png)<br/>

## <a name="azureml"></a>AzureML 
<br/>![AzurekML 샘플](./media/azureml-samples.png)<br/>

## <a name="caffe2"></a>caffe2 
<br/>![caffe2 샘플](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![H2O 샘플](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![SparkML 샘플](./media/sparkml-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![TensorFlow 샘플](./media/tensorflow-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![XGBoost 샘플](./media/xgboost-samples.png)<br/>

