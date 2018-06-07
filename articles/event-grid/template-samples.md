---
title: Azure Resource Manager 템플릿 샘플 - Event Grid | Microsoft Docs
description: Event Grid를 위한 Azure Resource Manager 템플릿 샘플
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 03/27/2018
ms.author: tomfitz
ms.openlocfilehash: f4d6a663b4e0d2c2166028051e713668534b20bc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Event Grid를 위한 Azure Resource Manager 템플릿

다음 표는 Event Grid를 위한 Azure Resource Manager 템플릿 링크를 포함합니다.

| | |
|-|-|
|**Event Grid 구독**||
| [웹후크 엔드포인트를 사용한 사용자 지정 항목 및 구독](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| Event Grid 사용자 지정 항목을 배포합니다. 웹후크 엔드포인트를 사용하여 해당 사용자 지정 항목에 대한 구독을 만듭니다. |
| [EventHub 엔드포인트를 사용한 사용자 지정 항목 구독](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/subscribeCustomTopicToEventHub.json)| 이미 존재하는 사용자 지정 항목에 대한 Event Grid 구독을 만듭니다. 구독은 엔드포인트에 대해 Event Hub를 사용합니다. |
| [리소스 그룹 구독](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/subscribeResourceGroupToWebHook.json)| 리소스 그룹에 대한 이벤트를 구독합니다. 배포 중 대상으로 지정하는 리소스 그룹은 이벤트의 원본입니다. 구독은 엔드포인트에 대해 Event Hub를 사용합니다. |
| [Blob Storage 계정 및 구독](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/createBlobAndSubscribe.json)| Azure Blob Storage 계정을 배포하고 해당 저장소 계정에 대한 이벤트를 구독합니다. |
| | |
