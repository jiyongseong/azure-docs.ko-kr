---
title: Deep Learning Virtual Machine 소개 - Azure | Microsoft Docs
description: Deep Learning Virtual Machines에 대한 주요 분석 시나리오 및 구성 요소.
keywords: 심층 학습, AI, 데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 35370d3d143fdc154728aa8b31b9874227e46168
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="introduction-to-the-deep-learning-virtual-machine"></a>Deep Learning Virtual Machine 소개

## <a name="why-deep-learning-virtual-machine"></a>Deep Learning Virtual Machine을 사용하는 이유 

심층 학습 알고리즘 / 심층 신경망은 갈수록 더 여러 기계 학습 문제에 사용되는 인기 있는 방법이 되고 있습니다. 이러한 방법은 모델을 훈련하는 고급 심층 신경망 아키텍처 및 큰 데이터 집합에 액세스를 통해 특정 도메인에서 흔히 인간의 인지 수준에 근접하는 이미지, 텍스트, 오디오/비디오 이해와 같은 기계 인지 작업에 특히 유효합니다. 심층 학습은 이러한 큰 데이터 집합을 사용하여 모델을 훈련하기 위해 다량의 계산력을 필요로 합니다. 클라우드 및 그래픽 처리 유닛(GPU)의 가용성 때문에 정교한 심층 신경망 아키텍처를 빌드해 클라우드의 강력한 컴퓨팅 인프라에서 큰 데이터 집합으로 훈련하는 것이 가능해지고 있습니다.  [데이터 과학 Virtual Machine](overview.md)는 데이터 준비, 기계 학습 및 심층 학습을 위한 다양한 도구 및 라이브러리를 제공해 왔습니다. 그런데 사용자가 직면한 문제는 심층 학습 같은 특정 시나리오에 대한 도구와 샘플을 쉽게 찾고 또한 GPU 기반 VM 인스턴스를 더욱 쉽게 프로비전하는 것입니다. 이 Deep Learning Virtual Machine(DLVM)은 이러한 문제를 처리합니다. 

## <a name="what-is-deep-learning-virtual-machine"></a>Deep Learning Virtual Machine이란? 
Deep Learning Virtual Machine은 심층 학습 모델을 훈련하고자 GPU 기반 VM 인스턴스를 사용하도록 더욱 간단하게 만들기 위해 특별히 구성된 [데이터 과학 Virtual Machine](overview.md)(DSVM)변형입니다. Windows 2016 및 Ubuntu 데이터 과학 Virtual Machine에서 지원됩니다.  동일한 코어 VM 이미지 (및 모든 다양한 도구 집합)를 DSVM으로 공유하지만 심층 학습을 보다 쉽게 수행할 수 있도록 구성됩니다. 또한 여러 실생활 AI 시나리오에 광범위하게 적용될 수 있는 이미지 및 텍스트 이해에 대한 종단 간 샘플을 제공합니다. 심층 학습 가상 머신은 또한 DSVM에 다양한 도구 집합 및 샘플을 만들어 가상 머신에서 도구 및 샘플 카탈로그를 노출하여 보다 쉽게 찾을 수 있게 하고자 합니다. 도구라는 관점에서 Deep Learning Virtual Machine은 많이 사용되는 여러 심층 학습 프레임워크, 이미지, 텍스트 데이터를 획득하고 전처리하는 도구를 제공합니다. 포괄적인 도구 목록은 [데이터 과학 Virtual Machine 개요 페이지](overview.md#whats-included-in-the-data-science-vm)를 참조할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

Deep Learning Virtual Machine을 시작하려면 다음 단계를 따릅니다.

* [Deep Learning Virtual Machine 프로비전](provision-deep-learning-dsvm.md)
* [Deep Learning Virtual Machine 사용](use-deep-learning-dsvm.md)
* [ 도구 참조](dsvm-deep-learning-ai-frameworks.md)
* [샘플](dsvm-samples-and-walkthroughs.md)
