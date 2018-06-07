---
title: 데이터 과학 가상 머신 기반 팀 환경 소개 - Azure | Microsoft Docs
description: 엔터프라이즈 팀 환경으로 데이터 과학 VM을 개발하기 위한 패턴입니다.
keywords: 딥 러닝, AI, 데이터 과학 도구, 데이터 과학 가상 머신, 지리 공간적 분석, 팀 데이터 과학 프로세스
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 6a755ef4d933046377a6a25be76655b44f4bf508
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34361378"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>데이터 과학 가상 머신 기반 팀 분석 및 AI 환경 
DSVM([데이터 과학 가상 머신](overview.md))은 Azure 클라우드에 이미 만들어진 AI 및 데이터 분석용 소프트웨어를 포함한 풍부한 환경을 제공합니다. 일반적으로 DSVM은 개별 분석 바탕 화면으로 사용해 왔으며 개인 데이터 과학자는 미리 만들어진 자신의 분석 환경에 대한 이 공유 표기법으로 생산성을 높입니다. 대규모 분석 팀이 자신의 데이터 과학자와 AI 개발자를 위한 자체의 분석 환경을 계획할 때 늘 되풀이되는 주제 중 하나는 전체 데이터 과학 / 분석 팀에 걸쳐 공동 작업 및 일관성을 촉진하는 엔터프라이즈 IT 정책과 일치하는 공유 분석 개발 및 실험 인프라에 대한 것입니다. 또한 IT는 공유 인프라를 사용하여 분석 환경을 더 잘 활용할 수 있습니다. 팀 기반 데이터 과학 / 분석 인프라를 사용하면 데이터 과학자가 다양한 데이터 자산에 액세스하면서도 프로덕션 환경에 영향을 주지 않고 빠르게 데이터를 이해하고 실험을 실행하고 가설의 유효성을 검사하고 예측 모델을 안전한 방법으로 만들 수 있으므로 어떤 조직에서는 이를 일명 "분석 샌드박스"라고도 합니다. 

DSVM은 Azure 인프라 수준에서 작동하므로 IT 관리자는 DSVM을 엔터프라이즈의 IT 정책을 준수하여 작동하도록 즉시 구성할 수 있으며 회사 데이터 자산에 제어된 방법으로 액세스할 수 있는 다양한 공유 아키텍처 구현에서 완전한 유연성을 제공할 수 있습니다. 

이 섹션에서는 DSVM을 팀 기반 데이터 과학 인프라로 배포하는 데 사용할 수 있는 몇 가지 패턴 및 지침을 설명합니다.  이러한 패턴의 구성 요소는 Azure IaaS(서비스 인프라)에서 직접 활용되므로 어떤 Azure VM에도 적용할 수 있습니다. 이 문서 시리즈의 구체적 초점은 이러한 표준 Azure 인프라 기능을 데이터 과학 VM에 적용하는 것입니다. 

엔터프라이즈 팀 분석 환경의 몇 가지 주요 구성 요소는 다음과 같습니다.

* [데이터 과학 가상 머신의 자동 크기 조정 풀](dsvm-pools.md)
* [공통 ID 및 풀의 아무 DSVM에서나 작업 영역에 액세스](dsvm-common-identity.md)
* [데이터 원본에 안전하게 액세스](dsvm-secure-access-keys.md)


이 문서 시리즈에서는 위의 특성과 관련된 지침 및 조언을 제공합니다. 물론, 아직 이 문서 시리즈에서 직접 다루지 않은 대기업 구성에 DSVM을 배포할 때 생각해야 할 여러 가지 추가 고려 사항과 요구 사항이 있습니다. 다음은 기업의 DSVM 인스턴스에 구현할 때 즉시 사용할 수 있는 일반적인 Azure 설명서에 대한 다른 고려 사항 및 권장 사항입니다. 

* [네트워크 보안](https://docs.microsoft.com/azure/security/azure-network-security)
* [모니터링](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) 및 [관리](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [로깅 및 감사](https://docs.microsoft.com/azure/security/azure-log-audit)
* [역할 기반 액세스 제어](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [정책 설정 및 적용](https://docs.microsoft.com/azure/azure-policy/azure-policy-introduction)
* [맬웨어 방지](https://docs.microsoft.com/azure/security/azure-security-antimalware)
* [암호화](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [데이터 검색 및 거버넌스](https://docs.microsoft.com/azure/data-catalog/)

[Azure 아키텍처 센터](https://docs.microsoft.com/en-us/azure/architecture/)는 클라우드 기반 분석 인프라를 빌드하고 관리하는 구체적인 종단 간 아키텍처와 패턴을 제공하는 훌륭한 리소스이기도 합니다. 
