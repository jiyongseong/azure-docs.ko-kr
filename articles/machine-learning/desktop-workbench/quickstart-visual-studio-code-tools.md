---
title: Azure에서 Machine Learning용 Visual Studio Code 도구 빠른 시작 문서 | Microsoft Docs
description: 이 문서에서는 Machine Learning용 Visual Studio Code 도구를 사용하여 실험 만들기, 모델 교육 및 웹 서비스 운영 가능화를 시작하는 방법을 설명합니다.
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/15/2017
ms.openlocfilehash: 00bf5c1405e5a37c7f72c8e78a76907987e8d2a2
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="visual-studio-code-tools-for-ai"></a>AI용 Visual Studio Code 도구
AI용 Visual Studio Code 도구는 심층 학습 / AI 솔루션을 빌드, 테스트 및 배포하기 위한 개발 확장입니다. Azure Machine Learning과 원활한 통합, 특히 이전 교육 및 사용자 지정 메트릭의 성능을 자세히 기술한 실행 기록 보기가 특징입니다. 또한 [Microsoft Cognitive Toolkit(이전의 CNTK)](http://www.microsoft.com/cognitive-toolkit), [Google TensorFlow](https://www.tensorflow.org) 및 기타 심층 학습 프레임워크를 사용하여 새 프로젝트를 탐색하고 부트스트랩할 수 있는 샘플 탐색기 보기를 제공합니다. 끝으로 Azure Virtual Machines 또는 GPU 포함 Linux 서버 같은 원격 환경에 관한 모델을 교육하는 작업을 제출할 수 있는 컴퓨터 대상에 대한 탐색기를 제공합니다. 
 
## <a name="getting-started"></a>시작 
시작하려면 먼저 [Visual Studio Code](https://code.visualstudio.com/Download)를 다운로드해서 설치해야 합니다. Visual Studio Code를 연 후 다음 단계를 수행합니다.
1. 작업 표시줄에서 확장 아이콘을 클릭합니다. 
2. "AI용 Visual Studio Code 도구"를 검색합니다. 
3. **설치** 단추를 클릭합니다. 
4. 설치 후 **다시 로드** 단추를 클릭합니다. 

Visual Studio Code가 다시 로드된 후 확장이 활성화됩니다. [확장 설치에 대해 자세히 알아봅니다](https://code.visualstudio.com/docs/editor/extension-gallery).

## <a name="exploring-project-samples"></a>프로젝트 샘플 탐색
AI용 Visual Studio Code 도구는 샘플 탐색기와 함께 제공됩니다. 샘플 탐색기를 사용하면 클릭 몇 번만으로 쉽게 샘플을 검색하고 실행해 볼 수 있습니다. 탐색기를 열려면 다음을 수행합니다.   
1. 명령 팔레트를 엽니다(보기 > **명령 팔레트** 또는 **Ctrl+Shift+P**).
2. "AI 샘플"을 입력합니다. 
3. "AI: Azure ML 샘플 탐색기 열기"에 대한 권장 사항을 찾아서 선택하고 Enter를 누릅니다. 

또는 샘플 탐색기 아이콘을 클릭할 수 있습니다.

## <a name="creating-a-new-project-from-the-sample-explorer"></a>샘플 탐색기에서 새 프로젝트 만들기 
다른 샘플을 탐색하여 그에 관한 자세한 정보를 얻을 수 있습니다. "아이리스 분류" 샘플을 찾을 때까지 탐색해 보겠습니다. 이 샘플을 기반으로 새 프로젝트를 만들려면 다음을 수행합니다.
1. 프로젝트 샘플에 대한 설치 단추를 클릭하고 메시지에 표시되는 명령을 확인하면서 새 프로젝트 만들기의 단계를 차례로 수행합니다. 
2. 프로젝트에 대한 이름, 예를 들어 "아이리스"를 선택합니다.
3. 프로젝트를 만들 폴더 경로를 선택하고 Enter를 누릅니다. 
4. 기존 작업 영역을 선택하고 Enter를 누릅니다.

프로젝트가 만들어집니다.

> [!TIP]
> Azure 리소스에 액세스하기 위해 로그인해야 합니다. 포함된 터미널에서 "az login"을 입력하고 지시를 따릅니다. 

## <a name="submitting-experiment-with-the-new-project"></a>새 프로젝트를 사용한 실험 제출
새 프로젝트가 Visual Studio Code에 열린 상태에서 다른 계산 대상(로컬 및 Docker 포함 VM)에 작업을 제출합니다.
AI용 Visual Studio Code 도구는 실험을 제출하는 여러 가지 방법을 제공합니다. 
1. 상황에 맞는 메뉴(마우스 오른쪽 단추 클릭) - **AI: 작업 제출**.
2. 명령 팔레트에서: "AI: 작업 제출".
3. 또는 포함된 터미널을 사용하면 Azure CLI, Machine Learning 명령을 사용하여 직접 명령을 실행할 수 있습니다.

iris_sklearn.py를 열고 마우스 오른쪽 단추를 클릭하고 **AI: 작업 제출**을 선택합니다.
1. 플랫폼: "Azure Machine Learning"을 선택합니다.
2. 실행 구성: "Docker-Python"을 선택합니다.

> [!NOTE]
> 작업을 처음 제출하는 경우 "Machine Learning 구성이 없습니다. 만드는 중..." 메시지가 표시됩니다. JSON 파일이 열리면 저장합니다(**Ctrl+S**).

작업이 제출된 후 포함된 터미널에 실행 진행률이 표시됩니다. 

## <a name="view-list-of-jobs"></a>작업 목록 보기
작업이 제출된 후 실행 기록에서 해당 작업을 나열할 수 있습니다.
1. 명령 팔레트를 엽니다(보기 > **명령 팔레트** 또는 **Ctrl+Shift+P**).
2. "AI 목록"을 입력합니다.
3. "AI: 작업 나열"에 대한 권장 사항을 찾아서 선택하고 Enter를 누릅니다.

작업 목록 보기가 열리고 모든 실행 및 몇몇 관련 정보가 표시됩니다.

## <a name="view-job-details"></a>작업 세부 정보 보기
작업 목록 보기가 여전히 열려 있는 상태에서 목록의 첫 번째 실행을 클릭합니다.
작업 결과를 심층 분석하려면 맨 위의 **작업 ID**를 클릭하고 자세한 정보를 봅니다. 

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [IDE와 함께 실행되도록 Azure Machine Learning을 구성하는 방법](./how-to-configure-your-IDE.md)
