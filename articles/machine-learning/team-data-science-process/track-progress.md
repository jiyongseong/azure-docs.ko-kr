---
title: 데이터 과학 프로젝트 실행 - Azure Machine Learning | Microsoft Docs
description: 데이터 과학자가 데이터 과학 프로젝트의 진행률을 추적할 수 있는 방법입니다.
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
ms.date: 11/28/2017
ms.author: deguhath
ms.openlocfilehash: ae0a32e15d86e8c2a9f8359a8caf1d205bd019f3
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="track-progress-of-data-science-projects"></a>데이터 과학 프로젝트의 진행률 추적

데이터 과학 그룹 관리자, 팀 리더 및 프로젝트 리더는 팀 프로젝트의 진행 상황, 수행된 작업, 작업 수행자 및 할 일 목록에 남아 있는 작업을 추적해야 합니다. 

## <a name="vsts-dashboards"></a>VSTS 대시보드
VSTS(Visual Studio Team Services)를 사용하는 경우 대시보드를 작성하여 지정된 Agile 프로젝트와 연결된 작업 및 작업 항목을 추적할 수 있습니다. 

Visual Studio Team Services에서 사용자 지정 대시보드 및 위젯을 만드는 방법에 대한 자세한 내용은 다음 지침 세트를 참조하세요.

- [대시보드 추가 및 관리](https://docs.microsoft.com/vsts/report/dashboards/dashboards)
- [대시보드에 위젯을 추가](https://docs.microsoft.com/vsts/report/dashboards/add-widget-to-dashboard)합니다.

## <a name="example-dashboard"></a>예제 대시보드

다음은 Agile 데이터 과학 프로젝트의 스프린트 작업 및 연결된 리포지토리에 대한 커밋 수를 추적하기 위해 만든 간단한 예제 대시보드입니다. **왼쪽 상단** 패널에 다음 내용이 표시됩니다.

- 현재 스프린트의 카운트다운, 
- 지난 7 일 이내에 각 리포지토리에 대한 커밋 수
- 특정 사용자에 대한 작업 항목. 

나머지 패널은 누적 흐름 다이어그램(CFD), 번다운(Burndown) 및 프로젝트에 대한 번업(Burnup)을 보여 줍니다.

- **왼쪽 아래**: 지정한 상태에 있는 작업의 양에 대한 CFD - 승인됨을 회색, 커밋됨을 파란색, 완료됨을 녹색으로 표시합니다.
- **오른쪽 위**: 완료까지 남은 작업과 남은 시간을 비교하여 보여 주는 번다운 차트입니다).
- **오른쪽 아래**: 완료된 작업과 총 작업량을 비교하여 보여 주는 번업 차트입니다.

![dashboard](./media/track-progress/dashboard.png)

이러한 차트를 작성하는 방법에 대한 자세한 내용은 [대시보드](https://docs.microsoft.com/vsts/report/dashboards/)에서 퀵 스타트 및 자습서를 참조하세요.
 
## <a name="next-steps"></a>다음 단계

**특정 시나리오**에 대한 프로세스의 모든 단계를 보여 주는 연습도 제공됩니다. 이러한 단계는 [예제 연습](walkthroughs.md) 자료에서 미리 보기 설명과 함께 나열되고 연결되어 있습니다. 이 연습에서는 클라우드 및 온-프레미스 도구와 서비스를 워크플로 또는 파이프라인에 결합하여 지능형 응용 프로그램을 만드는 방법을 보여 줍니다. 
