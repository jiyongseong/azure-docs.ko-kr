---
title: Azure에서 Azure 재해 복구로 Mobility Service 자동 업데이트 | Microsoft Docs
description: Azure Site Recovery를 사용하여 Azure VM을 복제할 때 Mobility Service의 자동 업데이트에 대한 개요를 제공합니다.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/16/2018
ms.author: rajanaki
ms.openlocfilehash: add80b17c76e7262f55e50cd07d4e9b053cfa1ff
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34209834"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Azure에서 Azure 복제로 Mobility Service 자동 업데이트

Azure Site Recovery는 매달 릴리스를 출시하여 기존 기능을 개선하고, 새 기능을 추가하고, 알려진 문제를 해결합니다. 즉, 서비스를 최신 상태로 유지하려면 매달 이러한 패치를 배포하도록 계획을 세워야 합니다. 업그레이드와 관련된 오버헤드를 방지하기 위해 사용자는 Site Recovery가 알아서 구성 요소 업데이트를 관리하도록 선택할 수 있습니다. Azure에서 Azure 재해 복구에 대한 [아키텍처 참조](azure-to-azure-architecture.md)에 자세히 설명되어 있듯이, 한 Azure 지역에서 다른 지역으로 가상 머신을 복제할 때 복제를 사용하도록 설정된 모든 Azure 가상 머신에 Mobility Service가 설치됩니다. 자동 업데이트를 사용하도록 설정한 경우 Mobility Service 확장은 모든 새 릴리스로 업데이트됩니다. 이 문서에서는 다음 내용을 자세히 다룹니다.

- 자동 업데이트의 작동 원리
- 자동 업데이트 사용
- 일반적인 문제 및 문제 해결
 
## <a name="how-does-automatic-update-work"></a>자동 업데이트의 작동 원리

Site Recovery에서 업데이트를 관리하도록 허용하면 자격 증명 모음과 동일한 구독에 생성된 자동화 계정을 통해 글로벌 Runbook(Azure 서비스에서 사용하는)이 배포됩니다. 한 자동화 계정은 특정 자격 증명 모음에 사용됩니다. Runbook은 자격 증명 모음에서 자동 업데이트가 설정된 각 VM을 확인하여 새 버전이 있으면 Mobility Service 확장의 업그레이드를 시작합니다. Runbook의 기본 일정은 복제된 가상 머신이 있는 지역의 표준 시간대에 따라 매일 오전 12시에 반복됩니다. 필요한 경우 사용자가 자동화 계정을 통해 Runbook 일정을 수정할 수 있습니다. 

> [!NOTE]
> 자동 업데이트를 설정해도 Azure VM을 다시 부팅할 필요가 없으며 진행 중인 복제가 영향을 받지도 않습니다.

## <a name="enable-automatic-updates"></a>자동 업데이트 사용

다음과 같은 방법으로 Site Recovery가 업데이트를 관리하도록 선택할 수 있습니다.

- [복제 설정 단계의 일부](#as-part-of-the-enable-replication-step)
- [자격 증명 모음 내에서 확장 업데이트 설정 전환](#toggle-the-extension-update-settings-inside-the-vault)

### <a name="as-part-of-the-enable-replication-step"></a>복제 설정 단계의 일부:

[가상 머신 보기에서](azure-to-azure-quickstart.md) 또는 [복구 서비스 자격 증명 모음에서](azure-to-azure-how-to-enable-replication.md) 가상 머신에 복제를 사용하도록 설정하면 Site Recovery 확장 업데이트를 Site Recovery에게 맡길 것인지 아니면 수동으로 관리할 것인지 선택하는 옵션이 제공됩니다.

![복제 자동 업데이트 사용](./media/azure-to-azure-autoupdate/enable-rep.png)

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>자격 증명 모음 내에서 확장 업데이트 설정 전환

1. 자격 증명 모음 내에서 **관리**-> **Site Recovery 인프라**로 이동
2. **Azure 가상 머신**-> **확장 업데이트 설정** 아래에서 토글을 클릭하여 *ASR에서 업데이트를 관리하도록 허용*하거나 *수동으로 관리* 중에 선택합니다. **저장**을 클릭합니다.

![자격 증명 모음 자동 업데이트 전환](./media/azure-to-azure-autoupdate/vault-toggle.png)

> [!Important] 
> *ASR에서 업데이트를 관리하도록 허용*을 선택하면 해당 자격 증명 모음에 있는 모든 가상 머신에 설정이 적용됩니다.


> [!Note] 
> 두 옵션 모두 업데이트 관리에 사용되는 자동화 계정에 대한 알림을 제공합니다. 자격 증명 모음에서 이 기능을 처음으로 사용하는 경우 새 자동화 계정이 만들어집니다. 동일한 자격 증명 모음에서 이루어지는 모든 후속 복제에는 이전에 생성된 계정이 사용됩니다.

## <a name="common-issues--troubleshooting"></a>일반적인 문제 및 문제 해결

자동 업데이트에는 문제가 있는 경우 자격 증명 모음 대시보드의 '구성 문제'에 동일한 알림이 제공됩니다. 

자동 업데이트를 사용하려고 했으나 실패한 경우 아래를 참조하여 문제를 해결할 수 있습니다.

**오류**: Azure 실행 계정(서비스 사용자)을 만들고 서비스 사용자에게 기여자 역할을 부여할 권한이 없습니다. 
- 권장 조치: 로그인한 계정에 '기여자' 역할이 할당되었는지 확인한 후 작업을 다시 시도합니다.
 
자동 업데이트가 켜져 있으면 Site Recovery 서비스가 대부분의 문제를 해결하며 사용자는 '**복구**' 단추를 클릭해야 합니다.

![복구 단추](./media/azure-to-azure-autoupdate/repair.png)

복구 단추를 사용할 수 없는 경우 확장 설정 창에 표시되는 오류 메시지를 참조하세요.

 - **오류**: 복구 서비스 리소스에 액세스할 수 있는 권한이 실행 계정에 없습니다.

    **권장 조치**: 실행 계정을 삭제하고 [다시 만들거나](https://docs.microsoft.com/en-us/azure/automation/automation-create-runas-account) Automation 실행 계정의 Azure Active Directory 응용 프로그램이 복구 서비스 리소스에 액세스할 수 있는지 확인합니다.

- **오류**: 실행 계정을 찾을 수 없습니다. Azure Active Directory 응용 프로그램, 서비스 사용자, 역할, Automation 인증서 자산, Automation 연결 자산 중 하나가 삭제되었거나 생성되지 않았습니다. 또는 인증서와 연결 사이에서 지문이 일치하지 않습니다. 

    **권장 조치**: 실행 계정을 삭제하고 [다시 만듭니다](https://docs.microsoft.com/en-us/azure/automation/automation-create-runas-account).
