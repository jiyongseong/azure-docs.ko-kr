---
title: Azure에서 심화 학습 데이터 과학 Virtual Machine을 사용하여 데이터 과학 | Microsoft Docs
description: 심화 학습 데이터 과학 VM을 사용하여 몇 가지 일반적인 데이터 과학 작업을 수행하는 방법입니다.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: 34ef0b10-9270-474f-8800-eecb183bbce4
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 2053ed8cc420183d493097eeb2cd2ad93c82c70c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="using-the-deep-learning-virtual-machine"></a>딥 러닝 Virtual Machine 사용

DLVM(딥 러닝 가상 머신)을 프로비전하면 심층 신경망 모델을 빌드하여 도메인에 컴퓨터 비전 및 언어 이해와 같은 AI 응용 프로그램을 빌드할 수 있습니다. 

AI용 딥 러닝 VM에는 여러 가지 도구가 제공됩니다. [심화 학습 및 AI 프레임워크 페이지](dsvm-deep-learning-ai-frameworks.md)에 이러한 도구를 사용하는 자세한 방법이 나와 있습니다. 

## <a name="deep-learning-tutorials-and-walkthroughs"></a>심화 학습 자습서 및 연습

프레임워크 기반 샘플 외에도 DLVM에서 유효성이 확인된 일련의 포괄적인 연습이 제공됩니다. 이러한 연습을 통해 이미지 및 텍스트/언어 이해와 같이 도메인에서 딥 러닝 응용 프로그램의 개발을 빠르게 시작할 수 있습니다. 다양한 도메인과 기술에 대한 포괄적인 자습서가 계속 추가될 예정입니다.   


- [다양한 프레임워크에서 신경망 실행](https://github.com/ilkarman/DeepLearningFrameworks): 한 프레임워크에서 다른 프레임워크로 코드를 마이그레이션하는 방법을 보여주는 포괄적인 연습입니다. 또한 프레임워크에서 시간 성능을 실행하고 모델을 비교하는 방법을 보여 줍니다. 

- [이미지 내 제품을 검색하는 종단 간 솔루션을 빌드하는 방법 가이드](https://github.com/Azure/cortana-intelligence-product-detection-from-images): 이미지 검색은 이미지 내의 개체를 찾고 분류할 수 있는 기술입니다. 이 기술은 많은 실제 비즈니스 도메인에서 엄청난 성과를 거둘 수 있습니다. 예를 들어 소매업체는 이 기술을 사용하여 고객이 선택한 제품을 확인할 수 있습니다. 또한 이 정보는 매장의 제품 재고를 관리하는 데 도움이 됩니다. 

- [PubMed 요약에서 명명된 엔터티 추출](https://docs.microsoft.com/azure/machine-learning/preview/scenario-tdsp-biomedical-recognition) 이 자습서는 구조화되지 않은 텍스트에서 약 이름 또는 질병 이름과 같은 명명된 엔터티를 추출하는 방법을 보여줍니다. 1천 800만 개의 PubMed 요약의 텍스트 모음에서 사용자 지정 워드 포함을 학습시키고, 해당 모델을 사용하여 엔터티 추출에 대한 LSTM(Long Short-Term Memory) 반복 신경망 모델을 빌드하고, 도메인 특정 단어 포함 모델이 엔터티 추출에 대한 일반 단어 포함을 능가할 수 있음을 보여줍니다.

- [오디오에 대한 딥 러닝](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/) 이 자습서는 [도시 소리 데이터 집합](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html)에서 오디오 이벤트 검색에 대한 딥 러닝 모델을 학습하는 방법을 보여주고 오디오 데이터를 작업하는 방법의 개요를 제공합니다.

- [텍스트 문서 분류](https://github.com/anargyri/lstm_han): 이 연습에서는 Hierarchical Attention Network 및 LSTM(Long Short Term Memory)이라는 두 가지 다른 신경망 아키텍처를 구축하고 학습하는 방법을 보여줍니다. 이러한 신경망은 Keras API를 딥 러닝에 사용하여 텍스트 문서를 분류합니다. Keras는 가장 인기 있는 딥 러닝 프레임워크인 Microsoft Cognitive Toolkit, TensorFlow 및 Theano의 프런트 엔드입니다.

## <a name="next-steps"></a>다음 단계

빠르게 시작할 수 있도록 [샘플 페이지](dsvm-samples-and-walkthroughs.md)에서 각 프레임워크의 VM에 미리 로드된 코드 예제에 대한 포인터를 제공합니다. 
