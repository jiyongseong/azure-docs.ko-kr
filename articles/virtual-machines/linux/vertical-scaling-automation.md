---
title: Azure Automation를 사용하여 Azure 가상 머신을 수직으로 확장 | Microsoft Docs
description: Azure Automation을 사용하여 모니터링 경고에 대한 응답으로 Linux Virtual Machine을 수직으로 확장하는 방법
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: dcee199e-fa25-44d5-9b25-df564cee9b45
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/29/2016
ms.author: singhkay
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 683348c907484ccd9394eb4aae18e9006ecb5c48
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="vertically-scale-azure-linux-virtual-machine-with-azure-automation"></a>Azure Automation을 사용하여 Azure Linux 가상 컴퓨터를 수직으로 확장
수직 확장은 워크로드에 응답하여 컴퓨터의 리소스를 늘리거나 줄이는 프로세스입니다. Azure에서는 Virtual Machine의 크기를 변경하여 이를 수행할 수 있습니다. 이는 다음과 같은 시나리오에 유용합니다.

* Virtual Machine이 자주 사용되지 않는 경우 크기를 줄여 월별 비용을 절감할 수 있습니다.
* Virtual Machine에서 최대 부하가 발생하는 경우 크기를 늘려 용량을 증가시킬 수 있습니다.

이 작업을 수행하는 개략적인 단계는 다음과 같습니다.

1. Virtual Machines에 액세스하도록 Azure Automation 설정
2. 구독으로 Azure Automation 수직 확장 runbook 가져오기
3. Runbook에 Webhook 추가
4. Virtual Machine에 경고 추가

> [!NOTE]
> 첫 번째 Virtual Machine의 크기로 인해 확장할 수 있는 크기가 제한될 수 있습니다. 이는 현재 Virtual Machine이 배포된 클러스터에서 다른 크기의 가용성 때문입니다. 이 문서에서 사용된 게시된 자동화 runbook에서는 이 점을 염두에 두고 VM 크기 쌍 이내에서만 확장합니다. 따라서 Standard_D1v2 Virtual Machine이 갑자기 Standard_G5로 확장되거나 Basic_A0으로 축소되지 않습니다.
> 
> | VM 크기 조정 쌍 |  |
> | --- | --- |
> | Basic_A0 |Basic_A4 |
> | Standard_A0 |Standard_A4 |
> | Standard_A5 |Standard_A7 |
> | Standard_A8 |Standard_A9 |
> | Standard_A10 |Standard_A11 |
> | Standard_D1 |Standard_D4 |
> | Standard_D11 |Standard_D14 |
> | Standard_DS1 |Standard_DS4 |
> | Standard_DS11 |Standard_DS14 |
> | Standard_D1v2 |Standard_D5v2 |
> | Standard_D11v2 |Standard_D14v2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> 
> 

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>Virtual Machines에 액세스하도록 Azure Automation 설정
가장 먼저 해야 할 일은 VM 규모 집합 인스턴스의 크기를 조정하는 데 사용하는 Runbook을 호스트할 Azure Automation 계정을 만드는 것입니다. 최근 Automation 서비스에서는 사용자 대신 Runbook을 자동으로 매우 쉽게 실행하기 위한 서비스 주체를 설정하는 "실행 계정" 기능을 도입했습니다. 이에 대한 자세한 내용은 아래 문서를 참조하세요.

* [Azure 실행 계정으로 Runbook 인증](../../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-the-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>구독으로 Azure Automation 수직 확장 runbook 가져오기
Virtual Machine의 수직 확장에 필요한 runbook은 Azure Automation Runbook 갤러리에 이미 게시되어 있습니다. 이를 구독으로 가져와야 합니다. runbook을 가져오는 방법은 다음 문서에서 확인할 수 있습니다.

* [Azure Automation용 Runbook 및 모듈 갤러리](../../automation/automation-runbook-gallery.md)

가져와야 하는 runbook이 아래 이미지에 표시되어 있습니다.

![Runbook 가져오기](./media/vertical-scaling-automation/scale-runbooks.png)

## <a name="add-a-webhook-to-your-runbook"></a>Runbook에 Webhook 추가
Runbook을 가져온 후에는 Virtual Machine에서 경고를 통해 트리거될 수 있도록 runbook에 Webhook를 추가해야 합니다. Runbook에 대한 Webhook를 만드는 자세한 방법은 여기에서 확인할 수 있습니다.

* [Azure Automation Webhook](../../automation/automation-webhooks.md)

다음 섹션에서 필요하므로 Webhook 대화 상자를 닫기 전에 Webhook를 복사해야 합니다.

## <a name="add-an-alert-to-your-virtual-machine"></a>Virtual Machine에 경고 추가
1. Virtual Machine 설정 선택
2. "경고 규칙" 선택
3. "경고 추가" 선택
4. 경고를 실행할 메트릭 선택
5. 충족된 경우 경고를 실행할 조건 선택
6. 5단계의 조건을 충족하는 임계값 선택
7. 모니터링 서비스에서 5단계와 6단계의 조건 및 임계값을 확인할 기간 선택
8. 이전 섹션에서 복사한 Webhook에 붙여넣기

![Virtual Machine 1에 경고 추가](./media/vertical-scaling-automation/add-alert-webhook-1.png)

![Virtual Machine 2에 경고 추가](./media/vertical-scaling-automation/add-alert-webhook-2.png)

