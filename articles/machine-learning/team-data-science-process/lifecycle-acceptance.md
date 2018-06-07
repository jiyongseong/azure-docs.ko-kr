---
title: 팀 데이터 과학 프로세스 수명 주기의 고객 승인 단계 - Azure | Microsoft Docs
description: 데이터 과학 프로젝트의 고객 승인 단계에 대한 목표, 작업 및 결과물입니다.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: cba2255cc38c9d5b40628f30c5500f8078e31355
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="customer-acceptance"></a>고객 승인

이 문서에서는 TDSP(팀 데이터 과학 프로세스)의 고객 승인 단계와 관련된 목표, 작업 및 결과물을 설명합니다. 이 프로세스는 데이터 과학 프로젝트를 구조화하는 데 사용할 수 있는 권장 수명 주기를 제공합니다. 수명 주기는 일반적으로 프로젝트에서 반복적으로 자주 실행하는 주요 단계를 설명합니다.

   1. **비즈니스 이해**
   2. **데이터 취득 및 이해**
   3. **모델링**
   4. **배포**
   5. **고객 승인**

다음은 TDSP 수명 주기를 시각적으로 나타낸 것입니다. 

![TDSP 수명 주기](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>목표
**프로젝트 결과물 최종 확정**: 프로덕션 환경의 파이프라인, 모델 및 배포가 고객 목표를 충족하는지 확인합니다.

## <a name="how-to-do-it"></a>수행 방법
이 단계에서 설명하는 두 가지 기본 작업은 다음과 같습니다.

   * **시스템 유효성 검사**: 배포된 모델과 파이프라인이 고객 요구 사항을 충족하는지 확인합니다.
   * **프로젝트 이전**: 프로덕션에서 시스템을 실행할 엔터티로 프로젝트를 이전합니다.

고객이 시스템에서 비즈니스 요구 사항을 충족하고 허용 가능한 정확도로 질문에 대답하는지 확인한 다음 클라이언트 응용 프로그램에서 사용할 수 있도록 시스템을 프로덕션에 배포합니다. 모든 문서를 완성하여 검토합니다. 프로젝트는 운영 담당 엔터티로 이전됩니다. 예를 들어 이 엔터티는 IT 팀, 고객 데이터 과학 팀 또는 운영 시스템을 운영할 책임이 있는 고객 담당자일 수 있습니다. 

## <a name="artifacts"></a>아티팩트
이 최종 단계에서 생성되는 주요 아티팩트는 **고객을 위한 프로젝트 종료 보고서**입니다. 이 기술 보고서는 시스템 작동 방법을 학습하는 데 유용한 프로젝트의 모든 세부 정보를 포함합니다. TDSP는 [보고서 끝내기](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) 템플릿을 제공합니다. 이 템플릿을 있는 그대로 사용하거나 특정 클라이언트 요구에 따라 사용자 지정할 수 있습니다. 


## <a name="next-steps"></a>다음 단계

TDSP의 수명 주기에서 각 단계에 대한 링크는 다음과 같습니다.

   1. [비즈니스 이해](lifecycle-business-understanding.md)
   2. [데이터 취득 및 이해](lifecycle-data.md)
   3. [모델링](lifecycle-modeling.md)
   4. [배포](lifecycle-deployment.md)
   5. [고객 승인](lifecycle-acceptance.md)

특정 시나리오 프로세스의 모든 단계를 보여주는 종합적인 전체 연습도 제공됩니다. [예제 연습](walkthroughs.md) 문서는 링크 및 썸네일 설명을 포함하는 시나리오 목록을 제공합니다. 이 연습에서는 클라우드 및 온-프레미스 도구와 서비스를 워크플로 또는 파이프라인에 결합하여 지능형 응용 프로그램을 만드는 방법을 설명합니다. 

Azure Machine Learning Studio를 사용하는 TDSP의 단계 실행 방법의 예제를 보려면 [Azure Machine Learning에서 TDSP 사용](http://aka.ms/datascienceprocess)을 참조하세요.
