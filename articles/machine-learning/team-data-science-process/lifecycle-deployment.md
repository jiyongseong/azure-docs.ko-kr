---
title: 팀 데이터 과학 프로세스 수명 주기의 배포 단계 - Azure | Microsoft Docs
description: 데이터 과학 프로젝트의 배포 단계에 대한 목표, 작업 및 결과물입니다.
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
ms.openlocfilehash: a56aa9c317703186e1ba2ab6bed9aa1875d318c2
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="deployment"></a>배포

이 문서에서는 TDSP(팀 데이터 과학 프로세스)의 배포 단계와 관련된 목표, 작업 및 결과물을 설명합니다. 이 프로세스는 데이터 과학 프로젝트를 구조화하는 데 사용할 수 있는 권장 수명 주기를 제공합니다. 수명 주기는 일반적으로 프로젝트에서 반복적으로 자주 실행하는 주요 단계를 설명합니다.

   1. **비즈니스 이해**
   2. **데이터 취득 및 이해**
   3. **모델링**
   4. **배포**
   5. **고객 승인**

다음은 TDSP 수명 주기를 시각적으로 나타낸 것입니다. 

![TDSP 수명 주기](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>목표
데이터 파이프라인이 포함된 모델을 최종 사용자 승인을 위해 프로덕션 또는 이와 유사한 환경에 배포합니다. 

## <a name="how-to-do-it"></a>수행 방법
이 단계에서 처리되는 주요 작업은 다음과 같습니다.

**모델 운영**: 모델 및 파이프라인을 프로덕션 또는 이와 유사한 환경에 응용 프로그램을 사용하도록 배포합니다.

### <a name="operationalize-a-model"></a>모델 운영
성능이 우수한 일단의 모델을 확보했으면 다른 응용 프로그램에서 사용하도록 운영할 수 있습니다. 비즈니스 요구 사항에 따라 예측을 실시간 또는 배치 방식으로 수행합니다. 모델을 배포하려면 열린 API 인터페이스를 사용하여 공개합니다. 이 인터페이스를 사용하면 다음과 같은 다양한 응용 프로그램에서 모델을 쉽게 사용할 수 있습니다.

   * 온라인 웹 사이트
   * 스프레드시트 
   * 대시보드
   * 기간 업무 응용 프로그램 
   * 백 엔드 응용 프로그램 

Azure Machine Learning 웹 서비스를 사용하는 모델 운영의 예는 [Azure Machine Learning 웹 서비스 배포](../studio/publish-a-machine-learning-web-service.md)를 참조하세요. 프로덕션 모델 및 배포되는 데이터 파이프라인에 원격 분석 및 모니터링을 구현하는 것이 좋습니다. 이 연습은 후속 시스템 상태 보고 및 문제 해결에 도움이 됩니다.  

## <a name="artifacts"></a>아티팩트

* 시스템 상태 및 주요 메트릭을 표시하는 상태 대시보드
* 배포 세부 정보가 포함된 최종 모델링 보고서
* 최종 솔루션 아키텍처 문서


## <a name="next-steps"></a>다음 단계

TDSP의 수명 주기에서 각 단계에 대한 링크는 다음과 같습니다.

   1. [비즈니스 이해](lifecycle-business-understanding.md)
   2. [데이터 취득 및 이해](lifecycle-data.md)
   3. [모델링](lifecycle-modeling.md)
   4. [배포](lifecycle-deployment.md)
   5. [고객 승인](lifecycle-acceptance.md)

특정 시나리오 프로세스의 모든 단계를 보여주는 종합적인 전체 연습도 제공됩니다. [예제 연습](walkthroughs.md) 문서는 링크 및 썸네일 설명을 포함하는 시나리오 목록을 제공합니다. 이 연습에서는 클라우드 및 온-프레미스 도구와 서비스를 워크플로 또는 파이프라인에 결합하여 지능형 응용 프로그램을 만드는 방법을 설명합니다. 

Azure Machine Learning Studio를 사용하는 TDSP의 단계 실행 방법의 예제를 보려면 [Azure Machine Learning에서 TDSP 사용](http://aka.ms/datascienceprocess)을 참조하세요.
