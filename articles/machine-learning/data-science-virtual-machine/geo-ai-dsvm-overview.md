---
title: 지리적 인공 지능 데이터 과학 가상 머신 소개 - Azure | Microsoft Docs
description: 지리적 AI 데이터 과학 VM 및 구성 요소에 대해 설명합니다.
keywords: 심화 학습, AI, 데이터 과학 도구, 데이터 과학 가상 머신, 지리 공간적 분석
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
ms.date: 03/05/2018
ms.author: gokuma
ms.openlocfilehash: 2fb49212b8480fa550558e14e890cbf7395406da
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="introduction-to-the-geo-artificial-intelligence-data-science-virtual-machine"></a>지리적 인공 지능 데이터 과학 가상 머신 소개

Azure **지리적 AI 데이터 과학 VM**(지리적 DSVM)은 Microsoft 데이터 과학 VM에서 지리 공간적 분석 기능을 제공합니다. 특히, 이 VM은 ESRI의 시장 선도적 [ArcGIS Pro](https://www.esri.com/arcgis/products/arcgis-pro/overview) 지리 정보 시스템을 추가하여 [데이터 과학 VM](overview.md)에서 AI 및 데이터 과학 도구 키트를 확장합니다.

지리적 AI 데이터 과학 VM은 ESRI와 Microsoft 간의 공동 작업을 통해 구현되는 제품입니다. 결과적으로 다음과 같은 기능을 제공하는 강력한 환경이 제공됩니다.

- 가장 인기 있는 AI 및 데이터 과학 도구를 사용하여 데이터 탐색
- 지리적 또는 공간적 측면을 사용하여 데이터에 통계 분석 적용
- 기계 학습 및 심화 학습 알고리즘을 사용하여 예측 분석을 위한 모델 학습
- 풍부한 시각화 창출

Windows 2016 데이터 과학 가상 머신에서 지원됩니다. ArcGIS에 대한 Python 및 R 인터페이스는 지리적 DSVM에 미리 구성되어, AI 응용 프로그램 내의 지리 공간 분석에 프로그래밍 방식으로 액세스할 수 있도록 합니다. 지리 공간 정보가 주입된 AI 응용 프로그램을 빌드하는 데 도움을 주기 위해 Jupyter 노트북 샘플도 제공됩니다.


## <a name="why-geo-ai-data-science-vm"></a>지리적 AI 데이터 과학 VM이어야 하는 이유 

지리 및 위치 정보를 데이터와 통합하면 새로운 차원에 대한 이해를 바탕으로, 전 세계를 IOT 장치로 점점 더 많이 계측화할 수 있습니다. 상업적이든, 정부용이든, 교육용이든, 비영리 활동이든, 거의 모든 문명 사회 부문에서는 다양한 응용 프로그램이 사용되고 있습니다. 지리 공간적 분석은 지리적 및 위치 기반 데이터를 수집, 관리 및 분석하기 위한 강력한 도구를 제공하고, 이러한 데이터의 의미를 독특하게 분석할 수 있는 강력한 시각화 기능도 제공합니다. 

Microsoft의 [DSVM(데이터 과학 가상 머신)](overview.md)은 Azure에 제공되는 인기 있는 실험 및 모델링 환경으로, AI, Machine Learning 및 데이터 과학 도구 모음을 제공합니다. 이러한 플랫폼 및 도구는 모두 편리하게 미리 구성되어 있으므로 생산성을 바로 높이는 데 도움이 됩니다. 

이제 데이터 과학자와 지리 공간적 분석가들은 데이터 과학 VM의 예측 기능을 Azure 클라우드의 확장성 및 [ESRI](http://www.esri.com)의 지리 공간적 분석 소프트웨어와 결합하여, 데이터에서 새로운 정보를 얻고 새로운 기회를 찾아낼 수 있는 생산적인 플랫폼을 얻게 되었습니다. 


## <a name="next-steps"></a>다음 단계

다음 항목의 지침을 참조하여 지리적 AI 데이터 과학 VM을 프로비전하고 사용하세요.

* [지리적 AI 데이터 과학 VM 프로비전](provision-geo-ai-dsvm.md)
* [지리적 AI 데이터 과학 VM 사용](use-geo-ai-dsvm.md)
