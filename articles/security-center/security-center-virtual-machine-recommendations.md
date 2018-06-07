---
title: "Azure Security Center에서 가상 머신 보호 | Microsoft Docs"
description: "이 문서에서는 가상 머신을 보호하고 보안 정책을 준수하는 데 도움이 되는 Azure Security Center의 권장 사항에 대해 설명합니다."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2018
ms.author: terrylan
ms.openlocfilehash: 54375f6f98b4989a7af8bcde649d967f77c6c862
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2018
---
# <a name="protecting-your-virtual-machines-in-azure-security-center"></a>Azure Security Center에서 가상 머신 보호
Azure Security Center에서는 Azure 리소스의 보안 상태를 분석합니다. 보안 센터가 잠재적인 보안 취약점을 식별하는 경우 필요한 컨트롤을 구성하는 과정을 안내하는 권장 사항을 만듭니다.  이러한 권장 사항은 가상 머신(VM), 네트워킹, SQL, 응용 프로그램 등의 Azure 리소스 유형에 적용됩니다.

이 문서에서는 VM에 적용되는 권장 사항에 대해 설명합니다.  VM 권장 사항은 데이터 수집, 시스템 업데이트 적용, 맬웨어 방지 프로그램 프로비전, VM 디스크 암호화 등에 초점을 둡니다.  아래 테이블을 참조로 사용하여 제공되는 VM 권장 사항을 이해하고 각 권장 사항을 적용할 경우 어떻게 되는지 이해할 수 있습니다.

## <a name="available-vm-recommendations"></a>제공되는 VM 권장 사항
| 권장 사항 | 설명 |
| --- | --- |
| [구독에 대해 데이터 수집 활성화](security-center-enable-data-collection.md) |구독 또는 구독의 VM(가상 머신) 각각에 대해 보안 정책에서 데이터 수집을 켜는 것이 좋습니다. |
| [Azure Storage 계정에 암호화 사용](security-center-enable-encryption-for-storage-account.md) | 미사용 데이터에 대한 Azure Storage 서비스 암호화를 사용하도록 권장합니다. SSE(Storage 서비스 암호화)는 Azure Storage에 기록되는 데이터를 암호화하고 검색 전 해독하는 방식으로 작동합니다. SSE는 현재 Azure Blob service에만 사용할 수 있으며 블록 blob, 페이지 blob 및 추가 blob에 사용할 수 있습니다. 자세한 내용을 알아보려면 [미사용 데이터에 대한 Storage 서비스 암호화](../storage/common/storage-service-encryption.md)를 참조하세요.</br>SSE는 Resource Manager 저장소 계정에만 지원됩니다. 클래식 저장소 계정은 현재 지원되지 않습니다. 클래식 및 Resource Manager 배포 모델을 이해하려면 [Azure 배포 모델](../azure-classic-rm.md)을 참조하세요. |
| [보안 구성 수정](security-center-remediate-os-vulnerabilities.md) |OS 구성을 권장 보안 구성 규칙에 맞추도록 권장합니다. 예를 들어 암호 저장을 허용하지 않습니다. |
| [시스템 업데이트 적용](security-center-apply-system-updates.md) |누락된 시스템 보안 및 중요 업데이트를 VM에 배포하는 것이 좋습니다. |
| [Just-In-Time 네트워크 액세스 제어 적용](security-center-just-in-time.md) | Just-In-Time VM 액세스만 적용해야 합니다. Just-In-Time 기능은 미리 보기로 제공되며 Security Center의 표준 계층에서 사용할 수 있습니다. Security Center의 가격 책정 계층에 대해 자세히 알아보려면 [가격 책정](security-center-pricing.md)을 참조하세요. |
| [시스템 업데이트 후 다시 부팅](security-center-apply-system-updates.md#reboot-after-system-updates) |시스템 업데이트 적용 프로세스를 완료하려면 VM을 다시 부팅하는 것이 좋습니다. |
| [Endpoint Protection 설치](security-center-install-endpoint-protection.md) |VM(Windows VM만 해당)에 맬웨어 방지 프로그램을 프로비전하는 것이 좋습니다. |
| [VM 에이전트 사용](security-center-enable-vm-agent.md) |VM 에이전트가 필요한 VM을 확인할 수 있습니다. 패치 검색, 기준 검색 및 맬웨어 방지 프로그램을 프로비전하려면 VM에 VM 에이전트가 설치되어 있어야 합니다. Azure Marketplace에서 배포된 VM에 VM 에이전트가 기본적으로 설치됩니다. [VM 에이전트 및 확장 - 2부](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) 문서에 VM 에이전트 설치 방법이 설명되어 있습니다. |
| [디스크 암호화 적용](security-center-apply-disk-encryption.md) |Azure 디스크 암호화(Windows 및 Linux VM)를 사용하여 VM 디스크를 암호화하는 것이 좋습니다. VM에서 OS 및 데이터 볼륨에 암호화를 사용하는 것이 좋습니다. |
| [OS 버전 업데이트](security-center-update-os-version.md) |클라우드 서비스의 OS(운영 체제) 버전을 OS 제품군에 대해 사용할 수 있는 가장 최신 버전으로 업데이트하는 것이 좋습니다.  Cloud Services에 대한 자세한 내용은 [Cloud Services 개요](../cloud-services/cloud-services-choose-me.md)를 참조하세요. |
| [취약점 평가 설치되지 않음](security-center-vulnerability-assessment-recommendations.md) |VM에 취약점 평가 솔루션을 설치하는 것이 좋습니다. |
| [취약점 해결](security-center-vulnerability-assessment-recommendations.md#review-the-recommendation) |VM에 설치된 취약점 평가 솔루션에 감지되는 시스템 및 응용 프로그램 취약점을 볼 수 있습니다. |

## <a name="see-also"></a>참고 항목
다른 Azure 리소스 유형에 적용되는 권장 사항에 대해 자세히 알아보려면 다음을 참조하세요.

* [Azure Security Center에서 응용 프로그램 보호](security-center-application-recommendations.md)
* [Azure Security Center에서 네트워크 보호](security-center-network-recommendations.md)
* [Azure Security Center에서 Azure SQL 서비스 보호](security-center-sql-service-recommendations.md)

보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Security Center에서 보안 정책 설정](security-center-policies.md) -- Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.
