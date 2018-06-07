---
title: Azure에서 데이터 과학 환경 설정 | Microsoft Docs
description: 팀 데이터 과학 프로세스에서 사용되는 Azure의 데이터 과학 환경을 설정합니다.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 481cfa6a-7ea3-46ac-b0f9-2e3982c37153
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: deguhath
ms.openlocfilehash: 9bc5b3f830f2eee533c24d26710cc6b4e8745586
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>팀 데이터 과학 프로세스에서 사용되는 데이터 과학 환경 설정
팀 데이터 과학 프로세스는 데이터의 저장, 처리 및 분석을 위해 다양한 데이터 과학 환경을 사용합니다. 여기에는 Azure Blob Storage, 여러 유형의 Azure 가상 머신, HDInsight(Hadoop) 클러스터 및 Azure Machine Learning 작업 영역이 포함됩니다. 사용할 환경에 대한 결정은 모델링할 데이터의 유형 및 양과 클라우드에서 해당 데이터의 대상에 따라 달라집니다. 

* 이러한 결정을 내릴 때 고려할 질문에 대한 지침은 [Azure Machine Learning 데이터 과학 환경 계획](plan-your-environment.md)을 참조하세요. 
* 고급 분석을 수행할 때 발생할 수 있는 일부 시나리오의 카탈로그는 [팀 데이터 과학 프로세스 시나리오](plan-sample-scenarios.md)

이 메뉴는 팀 데이터 과학 프로세스에서 사용되는 다양한 데이터 과학 환경의 설정 방법을 설명하는 토픽에 연결됩니다.

[!INCLUDE [data-science-environment-setup](../../../includes/cap-setup-environments.md)]

**Microsoft DSVM(데이터 과학 Virtual Machine)** 은 Azure VM(가상 머신) 이미지로 사용할 수 있습니다. 이 VM은 데이터 분석 및 Machine Learning에 흔히 사용되는 몇 가지 인기 있는 도구로 사전 설치되고 구성됩니다. Windows 및 Linux에는 DSVM를 사용할 수 있습니다. 자세한 내용은 [Linux 및 Windows용 클라우드 기반 데이터 과학 Virtual Machine에 대한 소개](../data-science-virtual-machine/overview.md)를 참조하세요.

