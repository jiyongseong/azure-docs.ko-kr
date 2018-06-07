---
title: Azure Virtual Machine 보안 모범 사례
description: 이 문서는 Azure의 가상 머신에 사용할 다양한 보안 모범 사례를 제공합니다.
services: security
documentationcenter: na
author: barclayn
manager: mbaldwin
editor: ''
ms.assetid: 5e757abe-16f6-41d5-b1be-e647100036d8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: e0c823982bb799e324dc6fb0fb811fd9ace37878
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34364406"
---
# <a name="best-practices-for-azure-vm-security"></a>Azure VM 보안에 대한 모범 사례

대부분 IaaS(Infrastructure as a Service) 시나리오에서 [Azure VM(가상 머신)](https://docs.microsoft.com/azure/virtual-machines/)은 클라우드 컴퓨팅을 사용하는 조직에 주요 워크로드입니다. 이 사실은 조직이 워크로드를 클라우드에 천천히 마이그레이션하고 싶은 [하이브리드 시나리오](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx)에서 특히 명백합니다. 이러한 시나리오에서 [IaaS에 대한 일반적인 보안 고려 사항](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx)을 따르고 모든 VM에 보안 모범 사례를 적용합니다.

이 문서에서는 각각 VM을 사용하여 고객과 우리의 고유한 직접 경험으로부터 파생된 다양한 VM 보안 모범 사례를 설명합니다.

모범 사례는 의견의 일치를 기반으로 하며 현재 Azure 플랫폼 기능 및 기능 집합과 함께 작동합니다. 의견 및 기술은 시간이 지남에 따라 변경될 수 있으므로 해당 변경 사항을 반영하도록 이 문서를 주기적으로 업데이트할 예정입니다.

각 모범 사례에 대해 문서는 다음을 설명합니다.

* 각 모범 사례.
* 활성화하기에 좋은 아이디어인 이유.
* 사용하는 방법을 알아보는 방법.
* 사용하도록 설정하는데 실패하는 경우 발생할 수 있는 일.
* 해당 모범 사례를 대체할 수 있는 대안.

문서는 다음 VM 보안 모범 사례를 검사합니다.

* VM 인증 및 액세스 제어
* VM 가용성 및 네트워크 액세스
* 암호화를 적용하여 VM에서 미사용 데이터 보호
* VM 업데이트 관리
* VM 보안 태세 관리
* VM 성능 모니터링

## <a name="vm-authentication-and-access-control"></a>VM 인증 및 액세스 제어

VM을 보호하는 첫 번째 단계는 승인된 사용자만 새 VM을 설정할 수 있게 하는 것입니다. [Azure 정책](../azure-policy/azure-policy-introduction.md)을 사용하여 조직의 리소스 표기 규칙을 정하고 사용자 지정된 정책을 생성하며 이러한 규칙을 [리소스 그룹](../azure-resource-manager/resource-group-overview.md) 등의 리소스에 적용할 수 있습니다.

리소스 그룹에 속한 VM에서 기본적으로 해당 정책을 상속합니다. VM을 관리하는 데 이 방법을 권장하지만 [RBAC(역할 기반 액세스 제어)](../role-based-access-control/role-assignments-portal.md)를 사용하여 개별 VM 정책에 대한 액세스를 제어할 수도 있습니다.

VM 액세스를 제어하는 데 리소스 관리자 정책 및 RBAC를 사용하면 VM의 전반적인 보안을 향상시킬 수 있습니다. VM을 동일한 수명 주기로 동일한 리소스 그룹으로 통합하는 것이 좋습니다. 리소스 그룹을 사용하여 리소스에 대한 비용 청구를 배포, 모니터링 및 롤업할 수 있습니다. 사용자가 VM에 액세스하고 VM을 설정하도록 활성화하려면 [최소 권한 접근 방식](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models)을 사용합니다. 또한 권한을 사용자에게 할당할 때 다음 기본 제공 Azure 역할을 사용하도록 계획합니다.

- [Virtual Machine 참여자](../role-based-access-control/built-in-roles.md#virtual-machine-contributor): VM을 관리할 수 있으나 해당 컴퓨터가 연결된 가상 네트워크 또는 저장소 계정은 관리할 수 없습니다.
- [클래식 Virtual Machine 참여자](../role-based-access-control/built-in-roles.md#classic-virtual-machine-contributor): 클래식 배포 모델을 사용하여 만든 VM을 관리할 수 있으나 VM이 연결된 가상 네트워크 또는 저장소 계정은 관리할 수 없습니다.
- [보안 관리자](../role-based-access-control/built-in-roles.md#security-admin): 보안 구성 요소 및 보안 정책을 관리할 수 있습니다.
- [DevTest 실습 사용자](../role-based-access-control/built-in-roles.md#devtest-labs-user): 모든 항목을 볼 수 있으며 VM을 연결, 시작, 다시 시작 및 종료할 수 있습니다.

관리자 간 계정 및 암호를 공유하거나, 여러 사용자 계정 또는 서비스, 특히 소셜 미디어 또는 기타 비 관리 작업에 암호를 다시 사용하지 마세요. 이상적으로는, VM을 안전하게 설정하기 위해 [Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) 템플릿을 사용해야 합니다. 이 접근 방식을 사용하면 배포 선택을 강화하고 배포 전체에서 보안 설정을 적용할 수 있습니다.

RBAC와 같은 기능을 활용하여 데이터 액세스 제어를 적용하지 않는 조직은 사용자에게 필요 이상으로 많은 권한을 부여하게 될 수 있습니다. 특정 데이터에 대한 부적절한 사용자 액세스는 해당 데이터를 직접 손상시킬 수 있습니다.

## <a name="vm-availability-and-network-access"></a>VM 가용성 및 네트워크 액세스

VM에서 고가용성의 중요한 응용 프로그램을 실행할 경우에는 여러 VM을 사용하는 것이 좋습니다. 가용성 향상을 위해 [가용성 집합](../virtual-machines/windows/tutorial-availability-sets.md)에서 최소 2개 이상의 VM을 생성하세요.

또한 [Azure Load Balancer](../load-balancer/load-balancer-overview.md)에서는 부하 분산된 VM이 동일한 가용성 집합에 속해야 합니다. 이러한 VM을 인터넷에서 액세스해야 할 경우 [인터넷 연결 부하 분산 장치](../load-balancer/load-balancer-internet-overview.md)를 구성해야 합니다.

VM이 인터넷에 노출되어 있으면 [NSG(네트워크 보안 그룹)를 사용하여 네트워크 트래픽 흐름을 제어](../virtual-network/security-overview.md)하는 것이 중요합니다. NSG가 서브넷에 적용될 수 있기 때문에, 서브넷에 따라서 리소스를 그룹화한 다음 서브넷에 NSG를 적용하여 NSG의 수를 최소화할 수 있습니다. Azure에서 [네트워크 보안](../best-practices-network-security.md) 기능을 적절하게 구성하여 수행할 수 있는 네트워크 격리 계층을 생성하기 위한 것입니다.

특정 VM에 대한 원격 액세스 권한을 가진 대상 및 시간을 제어하기 위해 Azure Security Center에서 JIT(Just-In-Time) VM 액세스 기능을 사용할 수도 있습니다.

인터넷 연결 VM에 대한 네트워크 액세스 제한을 적용하지 않는 조직은 RDP(원격 데스크톱 프로토콜) Brute Force 공격 등의 보안 위험에 노출되어 있습니다.

## <a name="protect-data-at-rest-in-your-vms-by-enforcing-encryption"></a>암호화를 적용하여 VM에서 미사용 데이터 보호

[미사용 데이터 암호화](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/)는 데이터 프라이버시, 규정 준수 및 데이터 주권을 위한 필수 단계입니다. [Azure Disk Encryption](../security/azure-security-disk-encryption.md)을 사용하면 IT 관리자가 Windows 및 Linux IaaS VM 디스크를 암호화할 수 있습니다. 디스크 암호화는 업계 표준인 Windows BitLocker 기능과 Linux dm-crypt 기능을 결합하여 OS 및 데이터 디스크를 위한 볼륨 암호화를 제공합니다.

디스크 암호화를 적용하여 데이터를 보호하면 조직의 보안 및 규정 준수 요구 사항을 충족하는 데 도움이 됩니다. 조직에서는 암호화를 사용하여 데이터 무단 액세스와 관련된 위험을 완화하는 방법을 고려해야 합니다. 또한 중요한 데이터를 작성하기 전에 드라이브를 암호화하는 것이 좋습니다.

Azure 저장소 계정의 미사용 데이터를 보호할 수 있도록 VM 데이터 볼륨을 암호화해야 합니다. [Azure Key Vault](https://azure.microsoft.com/documentation/articles/key-vault-whatis/)를 사용하여 암호화 키 및 암호를 보호합니다.

데이터 암호화를 적용하지 않는 조직은 데이터 무결성 문제에 더 많이 노출됩니다. 예를 들어 무단 또는 악의적인 사용자가 손상된 계정의 데이터를 훔치거나 ClearFormat으로 코딩된 데이터에 무단으로 액세스할 수도 있습니다. 이러한 위험을 감수하는 것 외에도 업계 규정을 준수하기 위해 회사는 충실하게 작업하고 올바른 보안 컨트롤을 사용하여 데이터 보안을 강화하고 있다는 사실을 증명해야 합니다.

디스크 암호화에 대해 자세히 알아보려면 [Windows 및 Linux IaaS VM용 Azure Disk Encryption](azure-security-disk-encryption.md)을 참조하세요.


## <a name="manage-your-vm-updates"></a>VM 업데이트 관리

모든 온-프레미스 VM처럼 Azure VM은 사용자 관리를 위한 것이므로 Azure는 Windows 업데이트를 푸시하지 않습니다. 그러나 고객은 자동 Windows 업데이트 설정을 활성화 상태로 유지하는 것이 좋습니다. [Windows Server Update Services(WSUS)](https://technet.microsoft.com/windowsserver/bb332157.aspx) 또는 다른 적합한 업데이트 관리 제품을 다른 VM 또는 온-프레미스에 배포하는 옵션도 있습니다. WSUS와 Windows 업데이트 모두 VM을 최신 상태로 유지합니다. 또한 모든 IaaS VM이 최신 상태인지 확인하는 데 검색 제품을 사용하는 것이 좋습니다.

Azure에서 제공하는 스톡 이미지가 정기적으로 업데이트되어 최신 회의 Windows 업데이트를 포함합니다. 그러나 이미지가 배포 당시에 최신 상태라는 것이 보장되지 않습니다. 공용 릴리스 뒤에 약간의 지연(몇 주)이 발생할 수도 있습니다. 모든 Windows 업데이트를 확인하고 설치하는 것은 모든 배포의 첫 번째 단계입니다. 이 측정값은 사용자 또는 사용자의 고유한 라이브러리에서 제공되는 이미지를 배포할 때 적용하는 것이 특히 중요합니다. Azure Marketplace의 일부로 제공되는 이미지는 기본적으로 자동으로 업데이트됩니다.

소프트웨어 업데이트 정책을 적용하지 않는 조직은 알려진, 이전에 고정 취약점을 악용하는 위협에 더 많이 노출됩니다. 이러한 위협을 감수하는 것 외에도 업계 규정을 준수하기 위해 회사는 보안에 충실하고 있으며 올바른 보안 컨트롤을 사용하여 클라우드에 있는 워크로드의 보안을 강화하고 있다는 사실을 증명해야 합니다.

기존 데이터센터 및 Azure IaaS에 대한 소프트웨어 업데이트 모범 사례에는 많은 유사점이 있다는 점을 강조하는 것이 중요합니다. 따라서 VM을 포함하도록 현재 소프트웨어 업데이트 정책을 평가하는 것이 좋습니다.

## <a name="manage-your-vm-security-posture"></a>VM 보안 태세 관리

사이버 위협은 발전하고 있으며 VM을 보호하는 데 신속하게 위협을 감지하고 리소스에 대한 무단 액세스를 방지하고 경고를 트리거하며 거짓 오류를 줄일 수 있는 다양한 모니터링 기능이 필요합니다. 이러한 작업에 대한 보안 태세는 네트워크 액세스 보호를 위해 업데이트 관리에서 VM의 모든 보안 측면을 구성합니다.

[Windows](../security-center/security-center-virtual-machine.md) 및 [Linux VM](../security-center/security-center-linux-virtual-machine.md)의 보안 태세를 모니터링하기 위해 [Azure Security Center](../security-center/security-center-intro.md)를 사용합니다. Azure Security Center에서 다음 기능을 활용하여 VM을 보호합니다.

* 권장된 구성 규칙으로 OS 보안 설정 적용
* 누락될 수 있는 시스템 보안 및 중요 업데이트 식별 및 다운로드
* 끝점 맬웨어 방지 보호 권장 사항 배포
* 디스크 암호화 유효성 검사
* 취약점 평가 및 해결
* 위협 감지

Security Center는 위협을 적극적으로 모니터링할 수 있으며 잠재적 위협은 **보안 경고**에서 공개됩니다. 상호 관련된 위협은 **보안 인시던트**라고 하는 단일 보기로 집계됩니다.

Security Center에서 어떻게 Azure에 있는 사용자의 VM에서 잠재적인 위협을 식별할 수 있게 지원하는지 이해하려면 아래의 동영상을 시청합니다.

>[!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response/player]

VM에 대해 강력한 보안 태세를 적용하지 않는 조직은 설정된 보안 제어를 피하는 권한이 없는 사용자의 잠재적인 시도를 알지 못하는 상태로 유지됩니다.

## <a name="monitor-vm-performance"></a>VM 성능 모니터링

리소스 남용은 VM 프로세스가 소비해야 하는 것보다 더 많은 리소스를 소비하는 경우 문제가 될 수 있습니다. VM의 성능 문제로 인해 가용성의 보안 원칙을 위반하는 서비스 중단이 발생할 수 있습니다. 이러한 이유로 문제가 발생하는 동안 VM 액세스를 적극적으로, 또한 정상적인 작업 중 측정된 기준 성능에 대해 사전 대처식으로 모니터링해야 합니다.

[Azure 진단 로그 파일](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/)을 분석하여 VM 리소스를 모니터링하고 성능 및 가용성을 손상시킬 수 있는 잠재적인 문제를 식별할 수 있습니다. Azure 진단 확장은 Windows 기반 VM에 모니터링 및 진단 기능을 제공합니다. 확장을 [Azure Resource Manager 템플릿](../virtual-machines/windows/extensions-diagnostics-template.md)에 속하도록 포함시켜서 이러한 기능을 사용하도록 설정할 수 있습니다.

또한 [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md)를 사용하여 리소스 상태에 대한 가시성도 얻을 수 있습니다.

VM 성능을 모니터링하지 않는 조직은 성능 패턴의 특정 변경 내용이 정상 또는 비정상인지 확인하지 못합니다. VM이 정상보다 더 많은 리소스를 소비하는 경우 이러한 비정상은 외부 리소스의 잠재적인 공격 또는 VM에서 손상된 프로세스가 실행 중임을 나타낼 수 있습니다.
