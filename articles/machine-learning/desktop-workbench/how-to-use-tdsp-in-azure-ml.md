---
title: Team Data Science Process 템플릿으로 프로젝트 구조화 | Microsoft Docs
description: Azure Machine Learning에서 공동 작업용 프로젝트를 구조화하는 TDSP(Team Data Science Process) 템플릿을 인스턴스화하는 방법
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: bradsev
ms.openlocfilehash: 0f7a6d0a58e2dcfd94e20a81ae12b42f238016c8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="structure-projects-with-the-team-data-science-process-template"></a>Team Data Science Process 템플릿으로 프로젝트 구조화

이 문서는 Azure Machine Learning에서 TDSP(Team Data Science Process) 템플릿을 사용하여 데이터 과학 프로젝트를 만드는 방법에 대한 지침을 제공합니다. 이러한 템플릿은 공동 작업 및 재현성 프로젝트를 구조화하는 데 도움이 됩니다. 


## <a name="what-is-the-team-data-science-process"></a>Team Data Science Process란 무엇인가요?
TDSP란 고급 분석 솔루션을 실행 및 제공하기 위한 민첩하고 반복적인 데이터 과학 프로세스입니다. 엔터프라이즈 조직 내 데이터 과학 팀의 공동 작업과 효율성을 개선하기 위해 설계되었습니다. 4개 핵심 구성 요소를 통해 다음과 같은 목표를 지원합니다.

   * 표준 [데이터 과학 수명 주기](../team-data-science-process/lifecycle.md) 정의.
   * 표준화된 프로젝트 구조체, [프로젝트 문서화 및 보고 템플릿](https://github.com/Azure/Azure-TDSP-ProjectTemplate).
   * 계산 및 저장소 인프라, 코드 리포지토리 같은 각 프로젝트 실행을 위한 인프라 및 리소스.
   * 다음과 같은 데이터 과학 프로젝트 작업을 위한 [도구 및 유틸리티](https://github.com/Azure/Azure-TDSP-Utilities):
      - 공동 작업 버전 제어
      - 코드 검토
      - 데이터 탐색 및 모델링
      - 작업 계획

TDSP에 대한 자세한 내용은 [Team Data Science Process 개요](../team-data-science-process/overview.md)를 참조하세요.

## <a name="why-should-you-use-the-tdsp-structure-and-templates"></a>TDSP 구조체 및 템플릿을 사용해야 하는 이유
데이터 과학 팀의 공동 작업 효율성을 높이기 위한 핵심은 구조체, 수명 주기 및 데이터 과학 프로젝트 문서화를 표준화하는 것입니다. TDSP 템플릿을 사용하여 Machine Learning 프로젝트를 만들면 원활한 팀워크를 위한 프레임워크가 제공됩니다.

이전에 이러한 목표 달성에 도움이 되는 [TDSP 프로젝트 구조체 및 템플릿에 대한 GitHub 리포지토리](https://github.com/Azure/Azure-TDSP-ProjectTemplate)가 릴리스되었습니다. 하지만 지금까지는 데이터 과학 도구 내에서 TDSP 구조체 및 템플릿을 인스턴스화할 수 없었습니다. 이제는 TDSP 구조체 및 문서화 템플릿을 인스턴스화하는 Machine Learning 프로젝트를 만들 수 있습니다. 

## <a name="things-to-note-before-creating-a-new-project"></a>새 프로젝트를 만들기 전에 알아두어야 할 사항
새로운 프로젝트를 만들기 *전에* 다음 항목을 검토합니다.
* TDSP Machine Learning [템플릿](https://aka.ms/tdspamlgithubrepo)을 검토합니다.
* 콘텐츠("docs" 폴더에 있는 콘텐츠 제외) 크기는 25MB 미만이어야 합니다. 이 목록 뒤에 나오는 사항을 참조합니다.
* sample\_data 폴더는 코드를 테스트하거나 초기 개발을 시작할 수 있는 작은 데이터 파일(5MB 미만)에만 사용됩니다.
* Word 및 PowerPoint 파일과 같은 파일을 저장하면 "docs"폴더의 크기가 상당히 커질 수 있습니다. 공동 작업 Wiki, [SharePoint](https://products.office.com/en-us/sharepoint/collaboration) 또는 이러한 파일을 저장할 다른 공동 작업 리소스를 찾아보시기 바랍니다.
* Machine Learning에서 큰 파일 및 출력을 처리하는 방법을 알아보려면 [변경 사항 유지 및 대용량 파일 처리](http://aka.ms/aml-largefiles)를 참조하세요.

> [!NOTE]
> readme.md 파일을 제외하고, 프로젝트가 실행되는 동안 사용되지 *않는* 모든 문서화 관련 콘텐츠(텍스트, markdown, 이미지, 기타 문서 파일)는 반드시 "docs"(모두 소문자)라는 폴더에 상주해야 합니다. 이 폴더는 대상을 계산하기 위해 폴더 내 콘텐츠가 불필요하게 복사되는 일이 없도록 Azure Machine Learning 실행 시 무시되는 특수 폴더입니다. 이 폴더의 개체는 프로젝트 크기에 대한 25MB 제한에 포함되지 않습니다. 예를 들어, "docs"폴더는 문서화에 필요한 큰 이미지 파일을 저장할 장소입니다. 이러한 파일은 실행 기록을 통해 Git에서 여전히 추적합니다. 

## <a name="instantiate-the-tdsp-structure-and-templates-from-the-machine-learning-template-gallery"></a>Machine Learning 템플릿 갤러리에서 TDSP 구조체 및 템플릿 인스턴스화
TDSP 구조체 및 문서화 템플릿으로 새 프로젝트를 만들려면 다음 절차를 완료합니다.

### <a name="create-a-new-project"></a>새 프로젝트 만들기
새 프로젝트를 만들려면 Azure Machine Learning을 엽니다. 왼쪽 상단 창의 **프로젝트**에서 더하기 기호(**+**)를 선택한 다음 **새 프로젝트**를 선택합니다.

![새 프로젝트](./media/how-to-use-tdsp-in-azure-ml/instantiation-1.png)


### <a name="create-a-new-tdsp-structured-project"></a>새 TDSP 구조 프로젝트 만들기
   1. 관련 상자 또는 목록에 매개 변수 및 정보를 지정합니다.

      - 프로젝트 이름
      - 프로젝트 디렉터리
      - 프로젝트 설명
      - 빈 Git 리포지토리 경로
      - 작업 영역 이름

   2. 그런 다음 **검색** 상자에 **TDSP**를 입력합니다. 
   3. **TDSP를 사용하여 프로젝트 구조화** 옵션이 표시되면 해당 템플릿을 선택합니다. 
   4. **만들기** 단추를 선택하고 TDSP 구조체를 사용하여 새 프로젝트를 만듭니다. 해당 텍스트 상자에 프로젝트를 만들 때 빈 Git 리포지토리를 입력하면 프로젝트 생성 후 해당 리포지토리가 프로젝트 구조체 및 콘텐츠로 채워집니다.

![TDSP 프로젝트 만들기](./media/how-to-use-tdsp-in-azure-ml/instantiation-2.png)


## <a name="examine-the-tdsp-project-structure"></a>TDSP 프로젝트 구조체 검사
새 프로젝트를 만든 후에는 구조체를 검사할 수 있습니다(아래 그림의 왼쪽 패널). 여기에는 비즈니스 이해에 대한 표준화된 문서화의 모든 측면이 포함됩니다. 이러한 항목에는 TDSP 수명 주기의 단계, 데이터 위치, 정의 및 이 문서화 템플릿의 아키텍처가 포함됩니다. 

표시된 구조체는 [TDSP 프로젝트 구조체, 설명서 및 아티팩트 템플릿](https://github.com/Azure/Azure-TDSP-ProjectTemplate)에 게시된 TDSP 구조체에서 파생되었으며 일부 수정되었습니다. 예를 들어 여러 문서 템플릿이 하나의 markdown, 즉 [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport)에 병합되었습니다. 

### <a name="project-folder-structure"></a>프로젝트 폴더 구조체
TDSP 프로젝트 템플릿에는 다음과 같은 최상위 폴더가 포함되어 있습니다.
   - **code**: 코드를 포함합니다.
   - **docs**: 프로젝트에 대한 필수 문서화(예: Markdown 파일, 관련 미디어 등)를 포함합니다.
   - **sample_data**: 초기 개발 또는 테스트에 사용할 수 있는 **샘플(소용량)** 데이터를 포함합니다. 일반적으로 이러한 집합은 몇(5) MB보다 크지 않습니다. 이 폴더는 전체 또는 큰 데이터 집합에는 사용하지 않습니다.

![샘플 데이터](./media/how-to-use-tdsp-in-azure-ml/instantiation-3.png)


## <a name="use-the-tdsp-structure-and-templates"></a>TDSP 구조체 및 템플릿 사용
구조체 및 템플릿에 프로젝트 관련 정보를 추가해야 합니다. 프로젝트를 실행하고 전달하는 데 필요한 코드와 정보로 채워야 합니다. [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) 파일은 프로젝트 관련 정보를 사용하여 수정해야 하는 템플릿입니다. 여기에는 [TDSP 수명 주기](../team-data-science-process/lifecycle.md)의 네 단계 각각에 대한 정보를 입력하는 데 도움이 되는 일련의 질문이 제공됩니다.

실행 중 또는 완료 후 프로젝트 구조체가 어떻게 보이는지에 대한 예가 다음 그림의 왼쪽 패널에 표시됩니다. 이 프로젝트는 [Team Data Science Process 샘플 프로젝트: Azure Machine Learning에서 미국 인구 조사 데이터의 소득 분류](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) 샘플 프로젝트의 프로젝트입니다.

![예제 프로젝트 구조체](./media/how-to-use-tdsp-in-azure-ml/instantiation-4.png)

## <a name="document-your-project"></a>프로젝트 문서화
프로젝트를 문서화하는 방법에 대한 정보는 [TDSP 문서화 템플릿](https://github.com/Azure/Azure-TDSP-ProjectTemplate)을 참조합니다. 현재 Machine Learning TDSP 문서화 템플릿에서는 모든 정보를 [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) 파일에 포함하는 것이 좋습니다. 이 템플릿을 프로젝트 관련 정보로 채워야 합니다. 

[ProjectLearnings](https://aka.ms/tdspamlgithubrepoprojectlearnings) 템플릿도 제공됩니다. 이 템플릿을 사용하여 기본 프로젝트 문서에 포함되어 있지 않지만 문서화에 유용한 정보를 포함할 수 있습니다. 

### <a name="example-project-report"></a>예제 프로젝트 보고
[예제 프로젝트 보고](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md)를 사용할 수 있습니다. [미국 소득 분류 샘플 프로젝트](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome)에 대한 이 프로젝트 보고서는 데이터 과학 프로젝트에 TDSP 템플릿을 인스턴스화하고 사용하는 방법을 보여줍니다.

## <a name="next-steps"></a>다음 단계
Machine Learning 프로젝트에서 TDSP 구조체 및 템플릿을 사용하는 방법에 대한 이해를 돕기 위해 Machine Learning에 대한 설명서에 몇 가지 완성된 프로젝트 예제가 제공됩니다.

- Machine Learning에서 TDSP 프로젝트를 만드는 방법을 보여주는 샘플은 [Team Data Science Process 샘플 프로젝트: Azure Machine Learning에서 미국 인구 조사 데이터의 소득 분류](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome)를 참조하세요.
- Machine Learning에서 TDSP 인스턴스화 프로젝트의 NLP(자연어 처리)에 심층 학습을 사용하는 샘플은 [심층 학습에 자연어 처리를 사용하는 생체 의학 엔터티 인식](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction)을 참조하세요.

