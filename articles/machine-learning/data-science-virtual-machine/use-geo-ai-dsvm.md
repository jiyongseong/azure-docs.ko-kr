---
title: 지리적 인공 지능 데이터 과학 가상 머신 사용 - Azure | Microsoft Docs
description: Azure에서 지리적 AI 가상 머신을 사용하는 방법입니다.
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
ms.openlocfilehash: f346b086a0269f247d64edf9346b01849ba3d0ee
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>지리적 인공 지능 데이터 과학 가상 머신 사용

지리적 AI 데이터 과학 VM을 사용하여 분석을 위해 데이터를 가져오고, 데이터 랭글링을 수행하고, 지리 공간적 정보를 사용하는 AI 응용 프로그램에 대한 모델을 구축합니다. 지리적 AI 데이터 과학 VM을 프로비전하고 ArcGIS 계정으로 ArcGIS Pro에 로그인한 후에는 ArcGIS 데스크톱 및 ArcGis 온라인과 상호 작용할 수 있습니다. 또한 지리적 데이터 과학 VM에서 미리 구성된 Python 인터페이스 및 R 언어 브리지에서 ArcGIS에 액세스할 수도 있습니다. 다양한 AI 응용 프로그램을 빌드하려면 기계 학습 및 심화 학습 프레임워크, 데이터 과학 VM에서 사용할 수 있는 기타 데이터 과학과 결합합니다.  


## <a name="configuration-details"></a>구성 세부 정보

ArcGIS와 연결되는 Python 라이브러리 [arcpy](http://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm)는 ```c:\anaconda```에 있는 데이터 과학 VM의 전역 루트 conda 환경에 설치됩니다. 

- 명령 프롬프트에서 Python을 실행하는 경우 ```activate```를 실행하여 conda 루트 Python 환경을 활성화합니다. 
- IDE 또는 Jupyter 노트북을 사용하는 경우, 환경 또는 커널을 선택하여 올바른 conda 환경에서 작업하도록 선택할 수 있습니다. 

ArcGIS에 대한 R 브리지는 ```C:\Program Files\Microsoft\ML Server\R_SERVER```에 있는 주 Microsoft R 서버 독립 실행형 인스턴스에서 [arcgisbinding](https://github.com/R-ArcGIS/r-bridge)이라는 R 라이브러리로 설치됩니다. Visual Studio, RStudio 및 Jupyter는 이미 이 R 환경을 사용하도록 미리 구성되어 있으며 ```arcgisbinding``` R 라이브러리에 액세스할 수 있습니다. 


## <a name="geo-ai-data-science-vm-samples"></a>지리적 AI 데이터 과학 VM 샘플

기본 데이터 과학 VM의 ML 및 심화 학습 프레임워크 기반 샘플 외에도, 지리 공간적 샘플 집합이 지리적 AI 데이터 과학 VM의 일부로 제공됩니다. 이러한 샘플은 지리 공간적 데이터와 ArcGIS 소프트웨어를 사용하여 AI 응용 프로그램의 개발을 바로 시작하는 데 도움이 될 수 있습니다. 


1. [Python을 사용한 지리 공간적 분석 시작](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): ArcGIS에서 제공 하는 [arcpy](http://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) 라이브러리에 제공된 ArcGIS에 대한 Python 인터페이스를 사용하여 지리 공간적 데이터를 사용하는 방법을 보여 주는 소개 샘플입니다. 또한 기존의 기계 학습을 지리 공간적 데이터와 결합하고 ArcGIS의 맵에 결과를 시각화하는 방법도 보여 줍니다. 

2. [R을 사용한 지리 공간적 분석 시작](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): ArcGIS에서 제공 하는 [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) 라이브러리에 제공된 ArcGIS에 대한 R 인터페이스를 사용하여 지리 공간적 데이터를 사용하는 방법을 보여 주는 소개 샘플입니다. 

3. [픽셀 수준의 토지 용도 분류](https://github.com/Azure/pixel_level_land_classification): 항공 이미지를 입력으로 사용하고 토지 레이블을 반환하는 심층 신경망 모델을 만드는 방법을 보여 주는 자습서입니다. 토지 레이블의 예로는 "삼림" 또는 "물"이 있습니다. 이 모델은 이미지의 모든 픽셀에 대해 이러한 레이블을 반환합니다. 이 모델은 Microsoft의 오픈 소스 [CNTK(Cognitive Toolkit)](https://www.microsoft.com/en-us/cognitive-toolkit/) 심화 학습 프레임워크를 사용하여 빌드되었습니다. 또한 이 예제에서는 [Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/)에 대한 학습을 확장하고 ArcGIS Pro 소프트웨어의 모델 예측을 사용하는 방법도 보여 줍니다. 


## <a name="next-steps"></a>다음 단계

데이터 과학 VM을 사용하는 추가 샘플은 다음에서 사용할 수 있습니다.

* [샘플](dsvm-samples-and-walkthroughs.md)

