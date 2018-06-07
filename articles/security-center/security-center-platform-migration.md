---
title: Azure Security Center 플랫폼 마이그레이션 | Microsoft Docs
description: 이 문서에서는 Azure Security Center 데이터를 수집하는 방법에 대한 일부 변경 내용을 설명합니다.
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: 80246b00-bdb8-4bbc-af54-06b7d12acf58
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: yurid
ms.openlocfilehash: 67cb532b6c8a5d00923bc3b41709956971ead5c3
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="azure-security-center-platform-migration"></a>Azure Security Center 플랫폼 마이그레이션

2017년 6월 초를 시작으로 Azure Security Center에서는 보안 데이터를 수집하고 저장하는 방법에 대한 중요한 변경 사항을 공개합니다.  이러한 변경 사항으로 쉽게 보안 데이터를 검색하는 등 새로운 기능의 잠금을 해제하고 다른 Azure Management 및 모니터링 서비스와 연계합니다.

> [!NOTE]
> 플랫폼 마이그레이션은 프로덕션 리소스에 영향을 주지 않고 사용자 측에서 작업을 수행하지 않아야 합니다.


## <a name="whats-happening-during-this-platform-migration"></a>이 플랫폼 마이그레이션 도중 어떤 상황이 발생합니까?

이전에 Security Center는 Azure Monitoring Agent를 사용하여 VM에서 보안 데이터를 수집했습니다. 이 기능은 취약점 및 보안 이벤트를 식별하는 데 사용되는 보안 구성에 대한 정보를 포함하며 위협을 검색하는 데 사용됩니다. 이 데이터는 Azure의 Storage 계정에 저장되었습니다.

또한 Security Center는 Microsoft Monitoring Agent를 사용하며 이는 Log Analytics 서비스에서 사용하는 동일한 에이전트입니다. 이 에이전트에서 수집된 데이터는 VM의 지리적 위치를 고려하여 Azure 구독 또는 새 작업 영역에 연결된 기존 *Log Analytics* [작업 영역](../log-analytics/log-analytics-manage-access.md) 중 하나에 저장됩니다.

## <a name="agent"></a>에이전트

전환의 일부로 Microsoft Monitoring Agent([Windows](../log-analytics/log-analytics-windows-agent.md)용 또는 [Linux](../log-analytics/log-analytics-linux-agents.md)용)가 현재 데이터를 수집하는 모든 Azure VM에 설치됩니다.  VM이 Microsoft Monitoring Agent에 이미 설치된 경우 Security Center는 현재 설치된 에이전트를 활용합니다.

일정 기간(일반적으로 몇 일) 동안 두 에이전트는 데이터의 손실 없이 원활하게 전환하기 위해 함께 실행됩니다. 그러면 Microsoft가 현재 파이프라인의 사용을 중단하기 전에 새 데이터 파이프라인이 작동하는지 유효성을 검사할 수 있습니다. 확인이 완료되면 Azure Monitoring Agent는 VM에서 제거됩니다. 사용자 측에서는 아무 작업도 필요하지 않습니다. 모든 고객 마이그레이션되는 경우 전자 메일로 알려줍니다.
 
보안 데이터에 간격이 발생할 수 있으므로 마이그레이션하는 동안 Azure Monitoring Agent를 수동으로 제거하지 않는 것이 좋습니다. 추가 지원이 필요한 경우 [Microsoft 고객 서비스 및 지원 센터](https://support.microsoft.com/contactus/)를 참조하세요. 

Windows용 Microsoft Monitoring Agent는 TCP 포트 443을 사용해야 합니다. 자세한 정보는 [Azure Security Center 문제 해결 가이드](security-center-troubleshooting-guide.md)를 참조하세요.


> [!NOTE] 
> 다른 Azure Management 및 모니터링 서비스에서 Microsoft Monitoring Agent를 사용할 수 있기 때문에 Security Center에서 데이터 수집을 끄는 경우에도 자동으로 에이전트를 제거하지 않습니다. 그러나 필요한 경우 에이전트를 수동으로 제거할 수 있습니다.

## <a name="workspace"></a>작업 영역

위에서 설명한 대로 (Security Center 대신) Microsoft Monitoring Agent에서 수집된 데이터는 VM의 지리적 위치를 고려하여 Azure 구독 또는 새 작업 영역에 연결된 기존 Log Analytics 작업 영역 중 하나에 저장됩니다.

Azure Portal에서 Security Center에서 만든 항목을 포함하여 Log Analytics 작업 영역 목록을 찾아볼 수 있습니다. 새 작업 영역에 관련된 리소스 그룹이 만들어질 수 있습니다. 둘 다 다음과 같은 명명 규칙을 따릅니다.

- 작업 영역: *DefaultWorkspace-[subscription-ID]-[geo]*
- 리소스 그룹: *DefaultResouceGroup-[geo]* 
 
Security Center에서 만든 작업 영역의 경우 데이터는 30일 동안 보존됩니다. 기존 작업 영역의 경우 작업 영역 가격 책정 계층에 따라 보존됩니다.

> [!NOTE]
> Security Center에서 이전에 수집된 데이터는 Storage 계정에 남아 있습니다. 마이그레이션이 완료된 후에 이러한 Storage 계정을 삭제할 수 있습니다.

### <a name="security-management-solution"></a>보안 관리 솔루션 

Log Analytics에 보안 관리 솔루션이 설치되지 않은 기존 고객의 경우, Microsoft에서 해당 작업 영역에 설치하고 있지만 Azure VM만을 대상으로 합니다. 이 솔루션을 제거하지 마십시오. 관리 콘솔에서 이 작업을 수행하면 자동 수정이 없기 때문입니다.


## <a name="other-updates"></a>다른 업데이트

플랫폼 마이그레이션과 함께 몇 가지 부분적인 추가 업데이트를 공개합니다.

- OS 버전이 추가로 지원됩니다. [여기](security-center-faq.md#virtual-machines)에서 목록을 참조하세요.
- OS 취약점 목록이 확장됩니다. [여기](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)에서 목록을 참조하세요.
- [가격 책정](https://azure.microsoft.com/pricing/details/security-center/)은 매시간(이전에는 매일) 비례 배분되며, 이로 인해 일부 고객의 비용이 절감될 수 있습니다.
- 데이터 수집이 필요하며 표준 가격 책정 계층의 고객에게 자동으로 설정됩니다.
- Azure Security Center는 Azure 확장을 통해 배포되지 않은 맬웨어 방지 솔루션을 검색하기 시작합니다. Symantec Endpoint Protection 검색 및 Windows 2016용 Defender를 먼저 사용할 수 있습니다.
- 방지 정책 및 알림은 *구독* 수준에서만 구성 가능하지만 가격은 여전히 *리소스 그룹* 수준에서 설정 가능합니다.

