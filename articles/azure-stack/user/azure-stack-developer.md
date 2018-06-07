---
title: Azure 스택에 대 한 앱을 개발 | Microsoft Docs
description: Azure 스택 프로토타입 응용 프로그램에 대 한 개발 고려 사항
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: a1b5a90ca40ce2b19186220344b22ec0ae77e34b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="develop-for-azure-stack"></a>Azure Stack에 대한 개발

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

시작할 수 있는 현재 응용 프로그램을 개발 Azure 스택 환경에 액세스할 수 없는 경우에 합니다. Azure 스택 데이터 센터에서 실행 되는 Microsoft Azure 서비스를 제공, 때문에 Azure와 마찬가지로 Azure 스택에 대해 개발 하기 비슷한 도구와 프로세스를 사용할 수 있습니다. 일부 준비 및 다음 항목의 지침을 사용 하 여 Azure 스택 환경 에뮬레이션 하기 위해 Azure를 사용할 수 있습니다.

* Azure에서 Azure 스택에 배포 가능한 Azure 리소스 관리자 템플릿을 만들 수 있습니다. 참조 [템플릿 고려 사항](azure-stack-develop-templates.md) 이동성을 보장 하려면 서식 파일 개발에 대 한 지침입니다.
* 서비스의 가용성과 서비스 버전 관리 Azure와 Azure 스택 간에 차이가 있습니다. 사용할 수는 [Azure 스택 정책 모듈](azure-stack-policy-module.md) 를 Azure 스택에서 사용할 수 있는 Azure 서비스 가용성 및 리소스 유형을 제한 합니다. Azure 스택을 사용할 수 있는 서비스 응용 프로그램을 사용 한다는 되도록 서비스를 제한 합니다.
* [Azure 스택 퀵 스타트 템플릿](https://github.com/Azure/AzureStack-QuickStart-Templates) 은 Azure 스택와 Azure에 배포할 수 있는 템플릿을 개발 하는 방법을 보여 주는 일반적인 시나리오 예입니다.
