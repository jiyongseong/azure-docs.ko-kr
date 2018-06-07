---
title: Azure의 IaaS 작업에 대한 보안 모범 사례 | Microsoft Docs
description: " Azure IaaS로 작업을 마이그레이션하면 설계를 다시 평가할 기회를 얻게 됩니다. "
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: 1a6ff01274c4a47730ffe45275aed9d122994260
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Azure의 IaaS 작업에 대한 보안 모범 사례

작업을 Azure IaaS(Infrastructure as a Service)로 이동할 생각이라면 몇 가지 고려 사항에 대해 이미 잘 알고 있다는 사실을 인식하게 될 것입니다. 이미 가상 환경 보안을 설정해본 적이 있을 수 있습니다. Azure IaaS로 이동하면 가상 환경 보안 유지에 기존 전문 지식을 활용할 수 있으며 자산 보호에 도움이 되는 몇 가지 새로운 옵션도 사용할 수 있습니다.

온-프레미스 리소스를 일대일로 Azure로 가져오지 않는다는 가정 하에 시작해보겠습니다. 새로운 해결 과제 및 새로운 옵션 때문에 기존 설계, 도구 및 프로세스를 다시 평가할 기회를 얻게 됩니다.

보안에 대한 사용자의 책임은 클라우드 서비스의 형식에 기반합니다. 다음 차트에는 Microsoft와 여러분 모두에 대한 책임의 균형이 요약되어 있습니다.


![책임 영역](./media/azure-security-iaas/sec-cloudstack-new.png)


조직의 보안 요구를 충족하는 데 도움을 주기 위해 Azure에서 사용할 수 있는 옵션 일부를 살펴보겠습니다. 다양한 유형의 작업에 대한 보안 요구 사항이 다를 수 있는 점을 염두에 두십시오. 이러한 모범 사례 중 어떤 것도 혼자서는 시스템 보안을 유지할 수는 없습니다. 보안의 다른 요소와 마찬가지로, 적절한 옵션을 선택하고 각 솔루션의 간극을 해소하면서 서로 어떻게 보완할 수 있는지 파악해야 합니다.

## <a name="use-privileged-access-workstations"></a>권한 있는 액세스 워크스테이션 사용

조직에서 관리자는 권한이 상승된 계정을 사용하여 작업을 수행하므로 사이버 공격을 받기 쉽습니다. 일반적으로 이러한 공격은 악의적이기 보다는 기존 구성 및 프로세스에서 허용하기 때문에 초래됩니다. 이러한 사용자 대부분은 개념적 관점에서 위험을 이해하면서도 해당 단계를 여전히 선택하게 됩니다.

전자 메일을 확인하고 인터넷을 검색하는 등의 작업은 위험하지 않은 것처럼 보이지만 악의적인 행위자에 의해 손상되도록 상승된 계정을 노출할 수 있습니다. 검색 활동, 특수하게 작성된 전자 메일 또는 기타 기술은 기업에 대한 액세스 권한을 얻는 데 사용될 수 있습니다. 모든 Azure 관리 작업을 수행할 때 SAW(보안 관리 워크스테이션)를 사용하는 것을 강력하게 권장합니다. SAW는 실수로 인한 손상에 대한 노출을 줄이는 방법입니다.

PAW(권한 있는 액세스 워크스테이션)는 인터넷 공격 및 위협 벡터로부터 보호되는 전용 운영 체제를 중요한 작업을 위해 제공합니다. 이러한 중요한 작업 및 계정을 매일 사용하는 워크스테이션 및 장치에서 분리하면 매우 강력한 보호 효과를 얻을 수 있습니다. 이 분리는 피싱 공격, 응용 프로그램 및 OS 취약점, 다양한 가장 공격 및 자격 증명 도난 공격의 영향으로 제한됩니다. (키 입력 로깅, Pass-the-Hash 및 Pass-The-Ticket)

PAW 방법은 개별적으로 할당된 관리 계정을 사용하는 체계적이고 권장되는 방법의 확장입니다. 관리 계정은 표준 사용자 계정과는 별도입니다. PAW는 이러한 중요한 계정에 대해 신뢰할 수 있는 워크스테이션을 제공합니다.

자세한 내용 및 구현 지침에 대해서는 [권한 있는 액세스 워크스테이션](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations)을 참조하세요.

## <a name="use-multi-factor-authentication"></a>Multi-Factor Authentication 사용

이전에는 회사 데이터에 대한 액세스를 제어하기 위해 네트워크 경계가 사용되었습니다. 클라우드 전용, 모바일 전용 환경에서 ID는 제어 평면에 있습니다. 즉, 이를 사용하여 모든 장치에서 IaaS 서비스에 대한 액세스를 제어할 수 있습니다. 또한 이를 통해 데이터가 사용되는 위치 및 방식을 깊이 있게 이해할 수 있습니다. Azure 사용자의 디지털 ID를 보호하는 것은 신원 도용 및 기타 사이버 범죄로부터 구독을 보호하는 데 있어서 핵심적인 과정입니다.

계정을 보호하기 위해 수행할 수 있는 가장 효율적인 단계 중 하나는 2단계 인증을 사용하도록 설정하는 것입니다. 2단계 인증을 사용하면 암호 이외의 기능을 사용하여 인증을 할 수 있습니다. 이 단계는 다른 사람의 암호를 얻은 누군가가 액세스할 위험을 완화하는 데 도움이 됩니다.

간단한 로그인 프로세스에 대한 사용자 요구를 충족하는 동안 [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)을 사용하면 데이터와 응용 프로그램에 대한 액세스를 보호합니다. 전화 통화, 문자 메시지 또는 모바일 앱 확인과 같은 다양한 손쉬운 확인 옵션을 통해 강력한 인증을 전달합니다. 사용자는 선호하는 방법을 선택하면 됩니다.

Multi-Factor Authentication을 사용하는 가장 쉬운 방법은 Windows, IOS 및 Android를 실행하는 모바일 장치에서 사용할 수 있는 Microsoft Authenticator 모바일 앱입니다. 최신 버전의 Windows 10과 Azure AD(Azure Active Directory)와의 온-프레미스 Active Directory 통합을 사용하는 경우 Azure 리소스에 대한 원활한 Single Sign-On을 위해 [비즈니스용 Windows Hello](../active-directory/active-directory-azureadjoin-passport-deployment.md)를 사용할 수 있습니다. 이 경우 Windows 10 장치는 인증을 위한 2단계 방법으로 사용됩니다.

Azure 구독을 관리하는 계정과 가상 머신에 로그인할 수 있는 계정의 경우 Multi-Factor Authentication을 사용하면 암호만 사용하는 것보다 훨씬 높은 수준의 보안이 보장됩니다. 다른 형식의 2단계 인증을 사용하는 것도 가능하지만 아직 프로덕션 환경이 아니라면 배포하기가 복잡할 수 있습니다.

다음 스크린샷은 Azure Multi-Factor Authentication에 사용할 수 있는 일부 옵션을 보여 줍니다.

![Multi-Factor Authentication 옵션](./media/azure-security-iaas/mfa-options.png)

## <a name="limit-and-constrain-administrative-access"></a>관리 액세스 제한

Azure 구독을 관리할 수 있는 계정을 보호하는 것은 매우 중요합니다. 이러한 계정이 노출되면 데이터의 기밀성 및 무결성을 보장하기 위해 수행할 수 있는 다른 모든 단계의 가치도 떨어지게 됩니다. 최근에 [Edward Snowden](https://en.wikipedia.org/wiki/Edward_Snowden)에 따르면, 내부 공격이 조직의 전반적인 보안에 큰 위협을 초래합니다.

다음과 유사한 기준으로 개인에 대한 관리 권한을 평가합니다.

- 관리자 권한이 필요한 작업을 수행하고 있나요?
- 해당 작업을 얼마나 자주 수행하나요?
- 본인을 대신해서 다른 관리자가 작업을 수행할 수 없는 특별한 이유가 있나요?

권한을 부여하는 알려진 다른 모든 대안과 각 대안이 허용되지 않는 이유를 기록하세요.

적시 관리 방법을 사용하면 상승된 권한이 필요하지 않은 기간 동안 이러한 권한이 있는 계정이 불필요하게 존재하는 경우를 방지할 수 있습니다. 관리자가 해당 작업을 수행할 수 있게 제한된 시간 동안 계정 권한이 상승됩니다. 그런 다음 교대 근무가 끝나거나 작업이 완료되면 해당 권한이 제거됩니다.

[Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md)를 사용하여 조직의 액세스를 관리, 모니터링 및 제어할 수 있습니다. 이 기능은 조직에서 개인이 수행하는 작업을 파악하는 데 도움이 됩니다. 또한 적격 관리자 개념을 도입하여 Azure AD에 Just-in-Time 관리가 적용됩니다. 이러한 개별 사용자는 관리자 권한을 부여 받을 수 있는 계정이 있습니다. 이러한 유형의 사용자는 활성화 프로세스를 진행할 수 있으며 제한된 시간 동안 관리 권한이 부여됩니다.


## <a name="use-devtest-labs"></a>DevTest Lab 사용

실습 및 개발 환경에서 Azure를 사용하면 조직은 하드웨어 조달로 인한 시간 지연을 해소하여 테스트 및 개발을 민첩하게 진행할 수 있습니다. 아쉽게도 Azure에 익숙하지 못할 수도 있고 빠르게 채택하려고 하지 않을 수도 있으므로 관리자는 별 제한 없이 권한을 할당받을 수 있습니다. 이러한 위험으로 인해 조직은 의도치 않게 내부 공격에 노출될 수 있습니다. 일부 사용자는 필요한 것보다 더 많은 권한을 부여받을 수 있습니다.

[Azure DevTest Labs](../devtest-lab/devtest-lab-overview.md) 서비스는 [Azure 역할 기반 Access Control(RBAC)](../role-based-access-control/overview.md)을 사용합니다. RBAC를 사용하면 팀 내의 책임을 사용자가 작업을 수행하는 데 필요한 액세스 권한 수준만 부여되는 역할로 구분할 수 있습니다. RBAC에는 미리 정의된 역할(소유자, 실습 사용자 및 참가자)이 제공됩니다. 이러한 역할을 사용하여 외부 파트너에게 권한을 할당하고 공동 작업을 간소화할 수도 있습니다.

DevTest Lab에서는 RBAC를 사용하므로 추가 [사용자 지정 역할](../lab-services/devtest-lab-grant-user-permissions-to-specific-lab-policies.md)을 만들 수도 있습니다. DevTest Lab은 권한 관리를 간소화할 뿐만 아니라 환경이 프로비전되는 프로세스도 간소화합니다. 또한 개발 및 테스트 환경에서 작업하는 팀의 전형적인 문제점들을 처리하는 데도 도움이 됩니다. 몇 가지 준비가 필요하지만 장기적으로는 팀에 훨씬 편리한 결과를 가져옵니다.

Azure DevTest Lab의 기능은 다음과 같습니다.

- 사용자가 사용할 수 있는 옵션에 대한 관리 제어. 허용되는 VM 크기, 최대 VM 수 및 VM을 시작 및 종료할 수 있는 시기 등을 관리자가 중앙에서 관리할 수 있습니다.
- 실습 환경 생성 Automation
- 비용 추적
- 임시 공동 작업을 위한 간편한 VM 배포
- 셀프 서비스 기능을 통해 사용자가 템플릿을 사용하여 실습을 프로비전할 수 있음
- 사용량 관리 및 제한

![DevTest Lab을 사용하여 실습 만들기](./media/azure-security-iaas/devtestlabs.png)

DevTest Lab 사용과 관련된 추가 비용은 없습니다. 실습, 정책, 템플릿 및 아티팩트를 생성하는 것은 무료입니다. 가상 머신, 저장소 계정 및 가상 네트워크와 같은 실험 내에서 사용하는 Azure 리소스에 대해서만 비용을 지불합니다.



## <a name="control-and-limit-endpoint-access"></a>끝점 액세스 제어 및 제한

Azure에서 실습 또는 프로덕션 시스템을 호스트할 경우 인터넷에서 시스템에 액세스할 필요가 없습니다. 기본적으로 새 Windows 가상 머신에는 인터넷에서 액세스할 수 있는 RDP 포트가 있으며 Linux 가상 머신에는 SSH 포트가 열려 있습니다. 무단 액세스 위험을 최소화하려면 '노출된 끝점을 제한'하는 단계를 수행해야 합니다.

Azure에는 관리 끝점에 대한 액세스를 제한하는 데 도움이 되는 기술이 있습니다. Azure에서는 [NSG](../virtual-network/security-overview.md)(네트워크 보안 그룹)을 사용할 수 있습니다. 배포에 Azure Resource Manager를 사용할 경우 NSG가 모든 네트워크에서 관리 끝점(RDP 또는 SSH)으로의 액세스를 제한합니다. NSG와 관련해서는 라우터 ACL을 떠올려보세요. 라우터 ACL을 사용하여 Azure 네트워크의 다양한 세그먼트 간에 진행되는 네트워크 통신을 엄격히 제어할 수 있습니다. 이러한 방식은 경계 네트워크 또는 기타 격리된 네트워크에서 네트워크를 만드는 것과 비슷합니다. 트래픽을 검사하지는 않지만 네트워크 구분에 도움이 됩니다.


Azure에서는 온-프레미스 네트워크에서 [사이트 간 VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)을 구성할 수 있습니다. 사이트 간 VPN은 클라우드로 온-프레미스 네트워크를 확장합니다. 이를 통해 로컬 네트워크 이외 다른 위치에서의 액세스를 허용하지 않도록 NSG를 수정할 수도 있으므로 NSG를 사용할 또 다른 기회를 얻게 됩니다. 그런 후 먼저 VPN 통해 Azure 네트워크에 연결하여 관리가 수행되도록 요구할 수 있습니다.

사이트 간 VPN 옵션은 Azure에서 온-프레미스 리소스와 밀접하게 통합되는 프로덕션 시스템을 호스트하는 경우에 가장 유용할 수 있습니다.

또는 온-프레미스 리소스에 액세스할 필요가 없는 시스템을 관리하려는 경우 [지점 및 사이트 간](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md) 옵션을 사용할 수도 있습니다. 이러한 시스템은 자체 Azure Virtual Network에 격리될 수 있습니다. 관리자는 관리 워크스테이션에서 Azure 호스트 환경으로 VPN을 수행할 수 있습니다.

>[!NOTE]
>VPN 옵션 중 하나를 사용하여 인터넷에서 관리 끝점에 대한 액세스를 허용하지 않도록 NSG의 ACL을 다시 구성할 수 있습니다.

고려해야 할 또 다른 옵션은 [원격 데스크톱 게이트웨이](../active-directory/authentication/howto-mfaserver-nps-rdg.md) 배포입니다. 이러한 배포를 사용하여 HTTPS를 통해 원격 데스크톱 서버에 안전하게 연결하면서 이러한 연결을 보다 세부적으로 제어할 수 있습니다.

액세스해야 하는 기능에는 다음이 포함됩니다.

- 연결을 특정 시스템의 요청으로 제한하기 위한 관리자 옵션
- 스마트 카드 인증 또는 Azure Multi-Factor Authentication
- 다른 사용자가 게이트웨이를 통해 연결할 수 있는 시스템 제어
- 장치 및 디스크 리디렉션 제어

## <a name="use-a-key-management-solution"></a>키 관리 솔루션 사용

안전한 키 관리는 클라우드에서 데이터를 보호하기 위해 아주 중요합니다. [Azure Key Vault](../key-vault/key-vault-whatis.md)을 사용하면 HSM(하드웨어 보안 모듈)에서 암호와 같은 작은 비밀과 암호화 키를 안전하게 저장할 수 있습니다. 추가된 보증을 위해, HSM에서 키를 생성하거나 가져올 수 있습니다.

Microsoft는 FIPS 140-2 Level 2 유효성 검사가 적용된 HSM(하드웨어 및 펌웨어)에서 키를 처리합니다. Azure 로깅으로 키 사용 모니터링 및 감사: 로그를 Azure 또는 SIEM(보안 정보 및 이벤트 관리) 시스템으로 파이프하여 추가 분석 및 위협 검색을 수행합니다.

Azure를 구독하는 사용자는 Key Vault를 만들고 사용할 수 있습니다. Key Vault에는 개발자와 보안 관리자의 혜택이 있지만, 조직에서 Azure 서비스를 관리하는 관리자가 구현하고 관리할 수 있습니다.


## <a name="encrypt-virtual-disks-and-disk-storage"></a>가상 디스크 및 디스크 저장소 암호화

[Azure Disk Encryption](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)는 디스크를 이동하여 얻어지는 무단 액세스로부터 발생하는 데이터 도난 위협 또는 노출을 해결합니다. 다른 보안 제어 방법을 우회하기 위해 디스크를 다른 시스템에 추가할 수도 있습니다. 디스크 암호화는 Windows에서는 [BitLocker](https://technet.microsoft.com/library/hh831713)를, Linux에서는 DM-Crypt를 사용하여 운영 체제 및 데이터 드라이브를 암호화합니다. Azure Disk Encryption은 Key Vault와 통합되어 암호화 키를 제어 및 관리할 수 있도록 합니다. 표준 VM 및 Premium Storage가 있는 VM에 이 기능을 사용할 수 있습니다.

자세한 내용은 [Windows 및 Linux IaaS VM용 Azure Disk Encryption](azure-security-disk-encryption.md)을 참조하세요.

[Azure Storage 서비스 암호화](../storage/common/storage-service-encryption.md)는 미사용 데이터를 보호합니다. 저장소 계정 수준에서 사용되도록 설정됩니다. 데이터 센터에 기록될 때 데이터를 암호화하고, 사용자가 액세스할 때 자동으로 암호가 해독됩니다. 다음과 같은 시나리오가 지원됩니다.

- 블록 Blob, 추가 Blob 및 페이지 Blob의 암호화
- 온-프레미스에서 Azure로 가져온 보관된 VHD 및 템플릿의 암호화
- VHD를 사용하여 만든 IaaS VM에 대한 데이터 디스크 및 기본 OS 암호화

Azure Storage 암호화를 계속 진행하기 전에 다음 두 가지 제한 사항에 유의해야 합니다.

- 클래식 저장소 계정에서는 사용할 수 없습니다.
- 암호화가 사용되도록 설정한 후에 기록된 데이터만 암호화합니다.

## <a name="use-a-centralized-security-management-system"></a>중앙 집중식 보안 관리 시스템 사용

서버에서 보안 위험으로 간주될 수 있는 패치, 구성, 이벤트 및 활동을 모니터링해야 합니다. 이러한 문제를 해결하기 위해 [Security Center](https://azure.microsoft.com/services/security-center/) 및 [Operations Management Suite 보안 및 규정 준수](https://azure.microsoft.com/services/security-center/)를 사용할 수 있습니다. 이러한 두 옵션 모두 운영 체제에서 구성 이상의 결과를 가져옵니다. 또한 네트워크 구성 및 사가상 어플라이언스 사용과 같은 기본 인프라의 구성도 모니터링합니다.

## <a name="manage-operating-systems"></a>운영 체제 관리

IaaS 배포에서는 작업 환경의 기타 서버 또는 워크스테이션과 마찬가지로, 배포하는 시스템의 관리도 사용자가 책임져야 합니다. 패치, 보안 강화, 권한 할당 및 시스템 유지 관리와 관련된 기타 작업도 모두 사용자의 책임입니다. 온-프레미스 리소스와 긴밀하게 통합되는 시스템의 경우 바이러스 백신, 맬웨어 방지, 패치 및 백업 등을 위해 온-프레미스에서 사용하는 것과 동일한 도구 및 절차를 사용할 수도 있습니다.

### <a name="harden-systems"></a>시스템 보안 강화
Azure IaaS의 모든 가상 머신은 설치된 응용 프로그램에 필요한 서비스 끝점만 노출되도록 보안을 강화해야 합니다. Windows 가상 머신의 경우 [Security Compliance Manager](https://technet.microsoft.com/solutionaccelerators/cc835245.aspx) 솔루션에 대한 기준으로 Microsoft에서 게시하는 권장 지침을 따르세요.

Security Compliance Manager는 무료 도구입니다. 그룹 정책 및 System Center Configuration Manager를 사용하여 데스크톱, 기존 데이터 센터, 사설 및 공용 클라우드를 빠르게 구성하고 관리할 수 있습니다.

Security Compliance Manager는 테스트를 마친 배포 준비가 완료된 정책 및 Desired Configuration Management 구성 팩을 제공합니다. 이러한 기준은 [Microsoft 보안 지침](https://technet.microsoft.com/library/cc184906.aspx) 권장 사항 및 업계 모범 사례를 토대로 합니다. 또한 구성 드리프트를 관리하고, 규정 준수 요구 사항을 해결하고, 보안 위험을 줄이는 데 도움이 됩니다.

Security Compliance Manager를 사용하여 두 가지 방법으로 컴퓨터의 현재 구성을 가져올 수 있습니다. 먼저 Active Directory 기반 그룹 정책을 가져올 수 있습니다. 둘째로 [LocalGPO 도구](https://blogs.technet.microsoft.com/secguide/2016/01/21/lgpo-exe-local-group-policy-object-utility-v1-0/)를 사용하여 로컬 그룹 정책을 백업함으로써 "골든 마스터" 참조 컴퓨터의 구성을 가져올 수 있습니다. 그런 다음 로컬 그룹 정책을 Security Compliance Manager로 가져올 수 있습니다.

표준을 업계 모범 사례와 비교하고, 사용자 지정하고, 새 정책 및 Desired Configuration Management 구성 팩을 만듭니다. Windows 10 1주년 업데이트 및 Windows Server 2016을 포함하여 지원되는 모든 운영 체제에 대한 기준이 게시되었습니다.


### <a name="install-and-manage-antimalware"></a>맬웨어 설치 및 관리

프로덕션 환경과는 별도로 호스트되는 환경의 경우 가상 머신 및 클라우드 서비스를 보호하는 데 사용할 수 있는 맬웨어 방지 확장이 있습니다. 이 확장은 [Azure Security Center](../security-center/security-center-intro.md)에 통합됩니다.


[Microsoft 맬웨어 방지 프로그램](azure-security-antimalware.md)에는 실시간 보호, 예약된 검색, 맬웨어 치료, 서명 업데이트, 엔진 업데이트, 샘플 보고, 제외 이벤트 컬렉션 및 [PowerShell 지원](https://msdn.microsoft.com/library/dn771715.aspx)과 같은 기능이 포함됩니다.

![Azure 맬웨어 방지](./media/azure-security-iaas/azantimalware.png)

### <a name="install-the-latest-security-updates"></a>최신 보안 업데이트 설치
고객이 Azure로 이동하는 첫 번째 작업 일부는 실습 및 외부 연결 시스템입니다. Azure 호스트 가상 머신이 인터넷에 액세스할 수 있어야 하는 응용 프로그램 또는 서비스를 호스트하는 경우 패치 적용에 유의해야 합니다. 운영 체제에 대해 패치를 설치합니다. 타사 응용 프로그램의 취약점을 패치하지 않을 경우 적절한 패치 관리가 진행되어도 우회할 수 있는 문제가 야기될 수도 있습니다.

### <a name="deploy-and-test-a-backup-solution"></a>백업 솔루션 배포 및 테스트

보안 업데이트와 마찬가지로 다른 작업을 처리하는 것과 동일한 방식으로 백업을 처리해야 합니다. 클라우드로 확장되는 프로덕션 환경에 속하는 시스템의 경우도 마찬가지입니다. 테스트 및 개발 시스템은 온-프레미스 환경에 대한 경험을 토대로 사용자에게 친숙한 비슷한 복원 기능을 제공하는 백업 전략을 따라야 합니다.

Azure로 이동되는 프로덕션 작업은 가능한 경우 기존 백업 솔루션과 통합되어야 합니다. 또는 [Azure Backup](../backup/backup-azure-arm-vms.md)을 사용하여 백업 요구를 보다 쉽게 처리할 수 있습니다.


## <a name="monitor"></a>모니터

[Security Center](../security-center/security-center-intro.md)는 Azure 리소스의 보안 상태를 지속적으로 평가하여 잠재적인 보안 취약성을 식별합니다. 권장 사항 목록은 필요한 컨트롤 구성 과정을 안내합니다.

이러한 예로 다음이 포함됩니다.

- 맬웨어 방지 프로그램을 프로비전하여 악성 소프트웨어 식별 및 제거 지원
- 네트워크 보안 그룹 및 가상 머신에 대한 트래픽 제어 규칙 구성
- 웹 응용 프로그램 방화벽을 프로비전하여 웹 응용 프로그램의 대상이 되는 공격에 대한 방어 지원
- 누락된 시스템 업데이트 배포
- 권장 기준과 일치하지 않는 OS 구성 해결

다음 이미지에서는 Security Center에서 사용할 수 있는 옵션 중 일부를 보여 줍니다.

![Azure Security Center 정책](./media/azure-security-iaas/security-center-policies.png)

[Operations Management Suite](../operations-management-suite/operations-management-suite-overview.md)란 온-프레미스 및 클라우드 인프라를 관리 및 보호하도록 도와주는 Microsoft의 클라우드 기반 IT 관리 솔루션입니다. Operations Management Suite는 클라우드 기반 서비스로 구현되므로 인프라 서비스에 대한 최소한의 투자로 빠르게 배포할 수 있습니다.

새로운 기능이 자동으로 제공되므로 지속적 유지 관리 및 업그레이드 비용을 절감할 수 있습니다. Operations Management Suite는 System Center Operations Manager와도 통합됩니다. 이 솔루션은 [보안 및 규정 준수](../operations-management-suite/oms-security-getting-started.md)를 포함하여 Azure 작업을 더 잘 관리하는 데 도움이 되는 다양한 구성 요소를 포함합니다.

Operations Management Suite에서 보안 및 규정 준수 기능을 사용하여 리소스에 대한 정보를 볼 수 있습니다. 이 정보는 다음 네 가지 주요 범주로 구성되어 있습니다.

- **보안 도메인**: 시간별 보안 기록을 추가로 탐색합니다. 보안 이벤트를 사용하여 맬웨어 평가, 업데이트 평가, 네트워크 보안 정보, ID 및 액세스 정보, 컴퓨터에 액세스합니다. Azure Security Center 대시보드에 빠르게 액세스할 수 있습니다.
- **주목할 만한 문제**: 해결되지 않은 문제의 수와 이러한 문제의 심각도를 빠르게 식별할 수 있습니다.
- **감지(미리 보기)**: 리소스에 대해 수행한 대로 보안 경고를 시각화하여 공격 패턴을 식별할 수 있습니다.
- **위협 인텔리전스**: 아웃바운드 악성 IP 트래픽이 있는 서버의 총 수, 악성 위협의 유형, 이러한 IP가 발생한 위치를 보여주는 맵을 시각화하여 공격 패턴을 식별할 수 있습니다.
- **일반적 보안 쿼리**: 환경을 모니터링하는 데 사용할 수 있는 가장 일반적인 보안 쿼리의 목록을 확인할 수 있습니다. 이러한 쿼리 중 하나를 클릭하면 해당 쿼리의 결과가 포함된 **Search** 블레이드가 열립니다.

다음 스크린샷에서는 Operations Management Suite에서 표시할 수 있는 정보의 예를 보여 줍니다.

![Operations Management Suite 보안 기준](./media/azure-security-iaas/oms-security-baseline.png)

## <a name="next-steps"></a>다음 단계

* [Azure 보안 팀 블로그](https://blogs.msdn.microsoft.com/azuresecurity/)
* [Microsoft 보안 대응 센터](https://technet.microsoft.com/library/dn440717.aspx)
* [Azure 보안 모범 사례 및 패턴](security-best-practices-and-patterns.md)
