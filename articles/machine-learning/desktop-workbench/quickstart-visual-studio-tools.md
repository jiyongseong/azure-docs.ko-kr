---
title: Azure의 Visual Studio Tools for Machine Learning에 대한 빠른 시작 문서 | Microsoft Docs
description: 이 문서에서는 Visual Studio Tools for Machine Learning을 사용하여 실험 만들기, 모델 교육 및 웹 서비스 운영 가능화를 시작하는 방법을 설명합니다.
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
ms.openlocfilehash: bbcb2ea5a7ceeb976f590393608cc29c67d9a49e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="visual-studio-tools-for-ai"></a>Visual Studio Tools for AI
Visual Studio Tools for AI는 딥 러닝/AI 솔루션을 빌드, 테스트 및 배포하기 위한 개발 확장입니다. Azure Machine Learning과 원활한 통합, 특히 이전 교육 및 사용자 지정 메트릭의 성능을 자세히 기술한 실행 기록 보기가 특징입니다. 또한 [Microsoft Cognitive Toolkit(이전의 CNTK)](http://www.microsoft.com/en-us/cognitive-toolkit), [Google TensorFlow](https://www.tensorflow.org) 및 기타 심층 학습 프레임워크를 사용하여 새 프로젝트를 탐색하고 부트스트랩할 수 있는 샘플 탐색기 보기를 제공합니다. 끝으로 Azure Virtual Machines 또는 GPU 포함 Linux 서버 같은 원격 환경에 관한 모델을 교육하는 작업을 제출할 수 있는 컴퓨터 대상에 대한 탐색기를 제공합니다. 또한 [Azure Batch AI(미리 보기)](https://docs.microsoft.com/azure/batch-ai/)에 수월하게 액세스할 수 있습니다.
 
## <a name="getting-started"></a>시작 
시작하려면 먼저 [Visual Studio](https://www.visualstudio.com/downloads/)를 다운로드하여 설치해야 합니다. Visual Studio를 연 후 다음 단계를 수행합니다.
1. Visual Studio에서 메뉴 모음을 클릭하고 "확장 및 업데이트..."를 선택합니다.
2. "온라인" 탭을 클릭하고 "Visual Studio Marketplace 검색"을 선택합니다.
3. "Visual Studio for AI"를 검색합니다. 
3. **다운로드** 단추를 클릭합니다. 
4. 설치 후 Visual Studio를 다시 시작합니다. 

Visual Studio가 다시 로드되면 확장이 활성화됩니다. [확장 찾기에 대해 자세히 알아보세요](hhttps://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions).

> [!NOTE]
> Visual Studio Tools for AI를 사용하려면 Visual Studio 2015 또는 2017, Professional 또는 Enterprise 버전이 필요합니다. Apple OSX 버전은 지원되지 않습니다. 


## <a name="exploring-project-samples"></a>프로젝트 샘플 탐색
Visual Studio Tools for AI는 샘플 탐색기가 함께 제공됩니다. 샘플 탐색기를 사용하면 클릭 몇 번만으로 쉽게 샘플을 검색하고 실행해 볼 수 있습니다. 탐색기를 열려면 다음 단계를 수행합니다.   
1. 메뉴 모음에서 **AI 도구**를 클릭합니다.
2. "Azure Machine Learning 갤러리"를 클릭합니다.

모든 Azure ML 샘플이 포함된 탭이 열립니다.

## <a name="creating-a-new-project-from-the-sample-explorer"></a>샘플 탐색기에서 새 프로젝트 만들기 
다른 샘플을 탐색하여 그에 관한 자세한 정보를 얻을 수 있습니다. "아이리스 분류" 샘플을 찾을 때까지 탐색해 보겠습니다. 이 샘플을 기반으로 새 프로젝트를 만들려면 다음 단계를 수행합니다.
1. 프로젝트 샘플에서 **설치**를 클릭하면 새 대화 상자가 열립니다. 
2. 리소스 그룹, 계정 및 작업 영역을 선택합니다.
3. 프로젝트 형식은 일반으로 두어도 됩니다.
4. 프로젝트 경로 및 프로젝트 이름을 입력하고 Enter 키를 누릅니다. 
5. 솔루션을 저장하라는 메시지가 표시되면 [저장]을 클릭합니다. 

여기까지 완료하면 새로운 Visual Studio 인스턴스에서 새 프로젝트가 열립니다. 

> [!TIP]
> Azure 리소스에 액세스하려면 로그인해야 합니다. 포함된 터미널에서 "az login"을 입력하고 지시를 따릅니다. 

## <a name="submitting-experiment-with-the-new-project"></a>새 프로젝트를 사용한 실험 제출
Visual Studio에서 새 프로젝트를 열어 놓은 상태로, 계산 대상(로컬 또는 Docker 포함 VM)에 작업을 제출합니다.
작업을 제출하려면 다음 단계를 수행합니다. 
1. 솔루션 탐색기에서 제출할 파일을 마우스 오른쪽 단추로 클릭하고 **시작 파일로 설정**을 선택합니다.
2. 프로젝트 이름을 선택하고 마우스 오른쪽 단추로 클릭한 후 **작업 제출...**을 선택합니다.
3. 스크립트를 실행할 클러스터(또는 대상 계산)를 선택할 수 있는 새로운 대화 상자가 열립니다.
4. **제출**을 클릭합니다.

작업이 제출된 후 포함된 터미널에 실행 진행률이 표시됩니다.

## <a name="view-list-of-jobs"></a>작업 목록 보기
작업이 제출된 후 실행 기록에서 해당 작업을 나열할 수 있습니다.
1. **서버 탐색기**에서 **AI 도구**를 클릭합니다.
2. 그런 다음 **Azure Machine Learning**을 선택합니다.
3. **작업** 메뉴를 클릭합니다.

작업 탐색기에는 이 프로젝트에 대해 제출된 모든 실험이 나열됩니다. 

## <a name="view-job-details"></a>작업 세부 정보 보기
작업 탐색기 보기를 열어 놓은 상태로, 목록의 첫 번째 실행을 클릭합니다.
그러면 작업 요약 패널과 로그 및 출력 패널이 로드됩니다.

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [IDE와 함께 실행되도록 Azure Machine Learning을 구성하는 방법](./how-to-configure-your-IDE.md)
