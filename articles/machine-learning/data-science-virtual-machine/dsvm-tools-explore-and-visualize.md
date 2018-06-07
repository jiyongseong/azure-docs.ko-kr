---
title: 데이터 탐색 및 시각화 도구 - Azure | Microsoft Docs
description: 데이터 과학 Virtual Machine용 데이터 탐색 및 시각화 도구
keywords: 데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학
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
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: df29d0a55317d06d656d8444c6bd7754c6c955eb
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="data-exploration-and-visualization-tools-on-the-data-science-virtual-machine"></a>데이터 과학 Virtual Machine용 데이터 탐색 및 시각화 도구

데이터 과학에서 핵심적인 단계는 데이터를 이해하는 것입니다. 시각화 및 데이터 탐색 도구는 데이터 이해를 가속화합니다. 다음은 이 핵심 단계를 용이하게 진행하도록 하기 위해 DSVM에 제공된 몇 가지 도구입니다. 

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| 어떤 도구인가요?   | 빅 데이터에 대한 오픈 소스 SQL 쿼리 엔진    |
| 지원되는 DSVM 버전      | Windows, Linux  |
| DSVM에 구성/설치 방법      |  `/dsvm/tools/drill*`에 포함 모드 전용으로만 설치됩니다.   |
| 일반적인 사용 용도      |  ETL을 요구하지 않는 현재 상태의 데이터 탐색입니다. CSV, JSON, 관계형 테이블, Hadoop을 비롯한 여러 다른 데이터 원본 및 형식 쿼리     |
| 사용/실행 방법      | 바탕 화면 바로 가기  <br/> [10분 내에 드릴 시작](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| DSVM의 관련 도구      |   Rattle, Weka, SQL Server Management Studio      |

## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| 무엇인가요?   |  Weka는 데이터 마이닝 작업에 대한 기계 학습 알고리즘 컬렉션입니다. 알고리즘은 데이터 집합에 직접 적용하거나 사용자 고유의 Java 코드에서 호출할 수 있습니다. Weka는 데이터 전처리 작업, 분류, 회귀, 클러스터링, 연결 규칙 및 시각화를 위한 도구를 포함합니다. |
| 지원되는 DSVM 버전     | Windows, Linux     |
| 일반적인 사용 용도      | 범용 ML 도구     |
| 사용/실행 방법      | Windows인 경우 Weka를 시작 메뉴에서 검색합니다. Linux인 경우 X2Go에 로그인한 후 Applications -> Development -> Weka로 이동합니다. |
| 샘플에 대한 링크      | [Weka 샘플](http://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| DSVM의 관련 도구      |LightGBM, Rattle, Xgboost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| 무엇인가요?   |   R을 사용한 데이터 마이닝에 대한 그래픽 사용자 인터페이스   |
| 지원되는 DSVM 버전     | Windows, Linux     |
| 일반적인 사용 용도      | R에 대한 일반 UI 데이터 마이닝 도구    |
| 사용/실행 방법      | UI 도구. Windows에서 명령 프롬프트를 시작하고, R을 실행한 후, R 내부에서 `rattle()`을 실행합니다. Linux에서 X2Go와 연결하여 터미널을 시작하고, R을 실행한 후, R 내부에서 `rattle()`을 실행합니다. |
| 샘플에 대한 링크      | [Rattle](https://togaware.com/onepager/) |
| DSVM의 관련 도구      |LightGBM, Weka, Xgboost   |

## <a name="powerbi-desktop"></a>PowerBI Desktop 
|    |           |
| ------------- | ------------- |
| 무엇인가요?   | 대화형 데이터 시각화 및 BI 도구    |
| 지원되는 DSVM 버전      | Windows  |
| 일반적인 사용 용도      |  데이터 시각화 및 대시보드 작성   |
| 사용/실행 방법      | 바탕 화면 바로 가기(`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| DSVM의 관련 도구      |   Visual Studio 2017, Visual Studio Code, Juno      |

