---
title: Amazon Web Services로 인증 구성
description: 이 문서에서는 Azure Automation에서 AWS 리소스를 관리하는 Runbook에 대한 AWS 자격 증명을 만들고 유효성을 검사하는 방법을 설명합니다.
keywords: aws 인증, aws 구성
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6159fbcdf60683426a2524dcda78c8ff7f4d8e00
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34192458"
---
# <a name="authenticate-runbooks-with-amazon-web-services"></a>Amazon 웹 서비스로 Runbook 인증

AWS(Amazon 웹 서비스)의 리소스를 사용하여 일반 작업을 자동화하는 일은 Azure의 Automation Runbook으로 할 수 있습니다. Azure의 리소스와 마찬가지로 Automation Runbook을 사용하여 AWS에서 많은 작업을 자동화할 수 있습니다. 다음 두 가지만 있으면 됩니다.

* AWS 구독 및 일련의 자격 증명. 특히 AWS 액세스 키 및 비밀 키가 필요합니다. 자세한 내용은 [AWS 자격 증명 사용](http://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html) 문서를 검토하세요.
* Azure 구독 및 Automation 계정.

AWS에 인증하려면 Azure Automation에서 실행 중인 Runbook을 인증하도록 AWS 자격 증명을 지정해야 합니다. Automation 계정이 이미 생성되고 AWS에서 인증하는 데 사용하려는 경우 다음 섹션의 단계를 수행할 수 있습니다. AWS 리소스를 대상으로 하는 Runbook에 계정을 전용으로 지정하려는 경우 먼저 새 [ 자동화 계정](automation-offering-get-started.md)을 만들고, (서비스 주체를 만드는 옵션을 건너뛰고) 다음 단계를 수행합니다.

## <a name="configure-automation-account"></a>Automation 계정 구성

Azure Automation가 AWS와 통신하려면 먼저 AWS 자격 증명을 검색하여 Azure Automation에 자산으로 저장해야 합니다. AWS 문서 [AWS 계정에 대한 선택키 관리](http://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html)에 설명된 다음 단계를 수행하여 선택키를 만들고 **액세스 키 ID** 및 **암호 선택키**를 복사합니다(필요에 따라 키 파일을 다운로드하여 안전한 곳에 저장).

AWS 보안 키를 만들어서 복사한 후에는 보안 키를 안전하게 보관하고 Runbook에서 참조할 수 있도록 Azure Automation 계정으로 자격 증명 자산을 만들어야 합니다. [Azure Automation의 자격 증명 자산](automation-credentials.md#to-create-a-new-credential-asset-with-the-azure-portal) 아티클에 있는 **새 자격 증명을 만들려면** 섹션의 단계에서 다음 정보를 입력합니다.

1. **이름** 상자에 **AWScred**를 입력하거나 본인의 명명 기준에 따라 적합한 값을 입력합니다.
2. **사용자 이름** 상자에 **액세스 ID**를 입력하고 **암호** 및 **암호 확인** 상자에 **비밀 선택키**를 입력합니다.

## <a name="next-steps"></a>다음 단계

* 솔루션 문서 [Amazon Web Services에서 VM 배포 자동화](automation-scenario-aws-deployment.md)를 검토하여 AWS에서 작업을 자동화하는 Runbook을 만드는 방법에 대해 알아봅니다.