---
title: Team Data Science Process란 무엇인가요? | Microsoft Docs
description: 예측 분석 솔루션 및 지능형 응용 프로그램을 제공하는 데이터 과학 방법론을 제공합니다.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: b1f677bb-eef5-4acb-9b3b-8a5819fb0e78
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: deguhath
ms.openlocfilehash: 4a73b988863a27f872c695cf209d2c46c6bb5f89
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="what-is-the-team-data-science-process"></a>Team Data Science Process란 무엇인가요?

TDSP(Team Data Science Process)는 예측 분석 솔루션 및 지능형 응용 프로그램을 효율적으로 제공하는 기민한 반복 데이터 과학 방법론입니다. TDSP는 팀 공동 작업 및 학습을 향상하도록 돕습니다. 데이터 과학 이니셔티브의 성공적인 구현을 용이하게 하는 산업의 Microsoft 및 기타 업계에서 모범 사례 및 구조의 추출을 포함합니다. 목표는 회사가 해당 분석 프로그램의 이점을 완전히 이해하도록 돕는 것입니다.

이 문서에서는 TDSP 및 주요 구성 요소의 개요를 제공합니다. 다양한 도구와 구현될 수 있는 프로세스의 일반적인 설명을 제공합니다. 프로젝트 작업 및 프로세스의 수명 주기와 관련된 역할의 더 자세한 설명은 추가 연결된 항목에서 제공됩니다. 팀에서 TDSP를 구현하는 데 사용하는 Microsoft 도구 및 인프라의 특정 집합을 사용하여 TDSP를 구현하는 방법에 대한 지침도 제공됩니다.

## <a name="key-components-of-the-tdsp"></a>TDSP의 주요 구성 요소

TDSP는 다음 주요 구성 요소로 구성됩니다.

- **데이터 과학 수명 주기** 정의
- **표준화된 프로젝트 구조**
- 데이터 과학 프로젝트에 대한 **인프라 및 리소스**
- 프로젝트 실행에 대한 **도구 및 유틸리티**


## <a name="data-science-lifecycle"></a>데이터 과학 수명 주기

TDSP(Team Data Science Process)는 데이터 과학 프로젝트의 개발을 구조화하는 수명 주기를 제공합니다. 수명 주기는 일반적으로 프로젝트가 실행될 때 시작부터 끝까지 따라야 하는 단계를 간략하게 설명합니다.

다른 데이터 과학의 수명 주기(예: [CRISP-DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), [KDD](https://wikipedia.org/wiki/Data_mining#Process) 또는 조직의 사용자 지정 프로세스)를 사용하는 경우 개발 수명 주기의 맥락에서 작업 기반 TDSP를 계속 사용할 수 있습니다 . 상위 수준에서 이러한 다양한 방법에는 많은 공통점이 있습니다. 

이 수명 주기는 지능형 응용 프로그램의 일부로 제공되는 데이터 과학 프로젝트를 위해 설계되었습니다. 이러한 응용 프로그램은 예측 분석을 위해 기계 학습 또는 인공 지능 모델을 배포합니다. 예비 데이터 과학 프로젝트 또는 임시 분석 프로젝트에서 이 프로세스를 사용하여 활용할 수도 있습니다. 하지만 이러한 경우 설명된 몇 가지 단계가 필요하지 않습니다.    

수명 주기는 일반적으로 프로젝트에서 반복적으로 자주 실행하는 주요 단계를 설명합니다.

* **비즈니스 이해**
* **데이터 취득 및 이해**
* **모델링**
* **배포**
* **고객 승인**

다음은 **팀 데이터 과학 프로세스 수명 주기**의 시각적 표현입니다. 

![TDSP - 수명 주기 2](./media/overview/tdsp-lifecycle2.png) 

TDSP에서 주기의 각 단계에 대한 목표, 작업 및 설명서 아티팩트는 [Team Data Science Process 수명 주기](lifecycle.md) 항목에 설명되어 있습니다. 이러한 작업 및 아티팩트는 프로젝트 역할과 관련이 있습니다.

- 솔루션 설계자
- 프로젝트 관리자
- 데이터 과학자
- 프로젝트 책임자 

다음 다이어그램은 이러한 작업(세로 축)에 대한 수명 주기(가로 축)의 각 역할에 관련된 작업(파란색) 및 아티팩트(녹색)의 그리드 뷰를 제공합니다. 

![TDSP 역할 및 작업](./media/overview/tdsp-tasks-by-roles.png)

## <a name="standardized-project-structure"></a>표준화된 프로젝트 구조

모든 프로젝트가 디렉터리 구조를 공유하고 프로젝트의 문서에 대한 템플릿을 사용하면 팀 멤버가 회사 프로젝트에 대한 정보를 쉽게 찾을 수 있습니다. 모든 코드 및 문서는 팀 공동 작업을 활성화하도록 Git, TFS 또는 Subversion과 같은 VCS(버전 제어 시스템)에 저장됩니다. Jira, Rally, Visual Studio Team Services와 같은 기민한 프로젝트 추적 시스템의 추적 작업 및 기능을 통해 개별 기능에 대한 코드를 자세히 추적할 수 있습니다. 또한 이러한 추적을 통해 팀은 더 나은 비용 예측을 얻을 수 있습니다. TDSP는 버전 관리, 정보 보안 및 공동 작업에 대해 VCS에 각 프로젝트에 대한 별도 리포지토리를 만드는 것을 권장합니다. 모든 프로젝트에 대한 표준화된 구조를 통해 조직 전체에 걸쳐 기업 정보를 구축할 수 있습니다.

폴더 구조에 대한 템플릿 및 기본 위치에 필요한 문서를 제공합니다. 이 폴더 구조는 데이터 탐색 및 기능 추출에 대한 코드를 포함하고 모델 반복을 기록하는 파일을 구성합니다. 이러한 템플릿을 통해 팀 멤버는 다른 사용자가 수행한 작업을 이해하고 팀에 새 멤버를 추가할 수 있습니다. 마크다운 형식으로 문서 템플릿을 보고 업데이트하는 것이 쉽습니다. 템플릿을 사용하여 문제가 잘 정의되고 결과물이 예상 품질을 충족하도록 각 프로젝트에 대한 주요 질문이 있는 검사 목록을 제공합니다. 이러한 예로 다음이 포함됩니다.

- 비즈니스 문제 및 프로젝트의 범위를 문서화하는 프로젝트 헌장
- 원시 데이터의 구조 및 통계를 문서화하는 데이터 보고서
- 파생된 기능을 문서화하는 모델 보고서
- ROC 곡선 또는 MSE와 같은 모델 성능 메트릭


![TDSP 디렉터리](./media/overview/tdsp-dir-structure.png)

디렉터리 구조는 [Github](https://github.com/Azure/Azure-TDSP-ProjectTemplate)에서 복제될 수 있습니다.

## <a name="infrastructure-and-resources-for-data-science-projects"></a>데이터 과학 프로젝트에 대한 인프라 및 리소스  

TDSP는 다음과 같은 공유 분석 및 저장소 인프라를 관리하기 위한 권장 사항을 제공합니다.

- 데이터 집합을 저장하기 위한 클라우드 파일 시스템, 
- 데이터베이스
- 빅 데이터(Hadoop 또는 Spark) 클러스터 
- 기계 학습 서비스 

분석 및 저장소 인프라는 클라우드 또는 온-프레미스에 있을 수 있습니다. 이는 원시 및 처리된 데이터 집합이 저장되는 위치입니다. 이 인프라는 재현 가능한 분석을 활성화합니다. 또한 불일치 및 불필요한 인프라 비용이 발생할 수 있는 복제를 방지합니다. 도구는 공유 리소스를 프로비전하고, 이를 추적하고, 각 팀 멤버가 해당 리소스에 안전하게 연결할 수 있도록 제공됩니다. 프로젝트 멤버가 일관성 있는 계산 환경을 만들도록 하는 것도 좋은 연습입니다. 그러면 다른 팀 멤버는 실험을 복제하고 유효성을 검사할 수 있습니다.

여러 프로젝트에서 작업하고 다양한 클라우드 분석 인프라 구성 요소를 공유하는 팀의 예는 다음과 같습니다.

![TDSP 인프라](./media/overview/tdsp-analytics-infra.png)


## <a name="tools-and-utilities-for-project-execution"></a>프로젝트 실행에 대한 도구 및 유틸리티

대부분의 조직에서 프로세스를 소개하는 것은 어렵습니다. 데이터 과학 프로세스 및 수명 주기 구현을 위해 제공된 도구는 장벽을 낮추고 해당 도입의 일관성을 증가시키도록 돕습니다. TDSP는 팀 내에서 TDSP의 도입을 신속하게 시작하도록 초기 도구 집합 및 스크립트를 제공합니다. 또한 데이터 탐색 및 기준 모델링과 같은 데이터 과학 수명 주기의 일부 일반적인 작업을 자동화할 수 있습니다. 공유 도구 및 유틸리티를 팀의 공유 코드 리포지토리에 기여하도록 개인에 대해 제공되는 잘 정의된 구조가 있습니다. 그러면 이러한 리소스는 팀 또는 조직 내의 다른 프로젝트에서 활용될 수 있습니다. 또한 TDSP는 전체 커뮤니티에 대한 도구 및 유틸리티의 기여를 활성화하도록 계획합니다. TDSP 유틸리티는 [Github](https://github.com/Azure/Azure-TDSP-Utilities)에서 복제될 수 있습니다.


## <a name="next-steps"></a>다음 단계

[Team Data Science Process: 역할 및 작업](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md) 이 프로세스를 표준화하는 데이터 과학 팀에 대한 핵심 인력 역할 및 관련된 작업에 대해 간략하게 설명합니다. 
