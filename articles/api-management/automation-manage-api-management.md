---
title: Azure Automation을 사용하여 Azure API Management를 관리
description: Azure Automation 서비스를 사용하여 Azure API Management를 관리하는 방법에 대해 알아봅니다.
services: api-management, automation
documentationcenter: ''
author: vladvino
manager: eamono
editor: ''
ms.assetid: 2e53c9af-f738-47f8-b1b6-593050a7c51b
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 10b483c70f7b5a3d767815306d8a690b1b9a5faf
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="managing-azure-api-management-using-azure-automation"></a>Azure Automation을 사용하여 Azure API Management를 관리
이 가이드에서는 Azure Automation 서비스 및 이를 사용하여 Azure API Management의 관리를 간소화하는 방법에 대해 소개합니다.

## <a name="what-is-azure-automation"></a>Azure Automation 정의
[Azure Automation](https://azure.microsoft.com/services/automation/)은 프로세스 자동화를 통해 클라우드 관리를 간소화하기 위한 Azure 서비스입니다. Azure Automation, 수동, 반복되는, 장기 실행 및 오류가 발생하기 쉬운 작업을 사용하면 조직의 안정성, 효율성 및 가치 창출 시간을 개선하도록 자동화할 수 있습니다.

Azure Automation은 요구 사항에 맞게 크기가 조정되는 매우 안정적이며 가용성이 높은 워크플로 실행 엔진을 제공합니다. Azure Automation에서는 작업이 정확히 필요한 시간에 수행되도록 수동, 타사 시스템 또는 예약된 간격에 의해 프로세스를 시작할 수 있습니다.

Azure Automation에서 자동으로 실행되도록 클라우드 관리 작업을 이동하여 작업 오버헤드를 줄이고 IT 및 DevOps 직원들이 비즈니스 가치를 추가하는 작업에 집중할 수 있게 합니다.

## <a name="how-can-azure-automation-help-manage-azure-api-management"></a>Azure Automation을 통해 Azure API Management를 쉽게 관리하려면 어떻게 해야 하나요?
API Management는 [Azure API Management API용 Windows PowerShell cmdlet](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/?view=azurermps-5.5.0#api_management/)을 사용하여 Azure Automation에서 관리할 수 있습니다. Azure Automation 내에서 cmdlet을 사용하여 API Management 작업을 수행하도록 PowerShell 워크플로 스크립트를 작성할 수 있습니다. Azure Automation에서 이러한 cmdlet을 다른 Azure 서비스용 cmdlet과 연결하여 Azure 서비스와 타사 시스템 간의 복잡한 작업을 자동화할 수도 있습니다.

다음은 Automation을 통해 API Management를 사용하는 몇 가지 예입니다.

* [Azure API Management – 백업 및 복원에 PowerShell 사용](https://blogs.msdn.microsoft.com/katriend/2015/10/02/azure-api-management-using-powershell-for-backup-and-restore/)

## <a name="next-steps"></a>다음 단계
Azure Automation의 기본 사항과 Azure Automation을 사용하여 Azure API Management를 관리하는 방법을 알아보았으므로, 이제 다음 링크에 따라 더 자세히 알아보세요.

* Azure Automation [시작 자습서](../automation/automation-first-runbook-graphical.md)를 참조하세요.

