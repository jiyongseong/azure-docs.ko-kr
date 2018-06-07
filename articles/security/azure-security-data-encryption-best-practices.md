---
title: 데이터 보안 및 암호화 모범 사례 | Microsoft Docs
description: 이 문서에서는 기본 제공 Azure 기능을 사용한 데이터 보안 및 암호화 모범 사례를 제공합니다.
services: security
documentationcenter: na
author: barclayn
manager: mbalwin
editor: TomSh
ms.assetid: 17ba67ad-e5cd-4a8f-b435-5218df753ca4
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: 574ca8a68bf6e532331a4b6f1106e472c8ab0449
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Azure 데이터 보안 및 암호화 모범 사례

클라우드의 데이터를 보호하기 위한 핵심 중 하나는 데이터에서 발생 가능한 상태 그리고 해당 상태에 어떤 컨트롤을 제공할 것인지 설명하는 것입니다. Azure 데이터 보안 및 암호화 모범 사례를 위해 다음과 같은 데이터 상태와 관련된 권장 사항이 제공됩니다.

* 저장: 모든 정보 저장 개체, 컨테이너 및 물리적 미디어(자기 또는 광 디스크)에 정적으로 존재하는 유형이 여기에 포함됩니다.
* 전송 중: 데이터가 ExpressRoute 같은 하이브리드 연결을 포함하여 서비스 버스에서(온-프레미스에서 클라우드로 또는 그 반대로) 네트워크를 통해 구성 요소, 위치 또는 프로그램 간에 전송 중이거나 입력/출력 프로세스 중인 경우 데이터가 동작 중인 것으로 간주됩니다.

이 문서에서는 Azure 데이터 보안 및 암호화 모범 사례 컬렉션에 대해 설명합니다. 이러한 모범 사례는 Azure 데이터 보안 및 암호화에 대한 Microsoft의 경험 그리고 여러분 같은 고객의 경험에서 얻은 것입니다.

각 모범 사례에 대해 다음 사항을 설명하겠습니다.

* 각 모범 사례
* 해당 모범 사례를 사용해야 하는 이유
* 해당 모범 사례를 사용하지 않을 경우에 발생할 수 있는 결과
* 해당 모범 사례를 대체할 수 있는 대안
* 해당 모범 사례를 사용하는 방법을 알아보는 방법

이 Azure 데이터 보안 및 암호화 모범 사례 문서는 이 문서가 작성될 당시의 합의된 의견과 Azure 플랫폼 기능 및 특징 집합을 기반으로 합니다. 이 문서는 시간이 지남에 따라 변화하는 의견 및 기술을 반영하도록 주기적으로 업데이트 됩니다.

이 문서에서 다루는 Azure 데이터 보안 및 암호화 모범 사례에는 다음이 포함됩니다.

* Multi-Factor Authentication 적용
* RBAC(역할 기반 액세스 제어) 사용
* Azure 가상 머신 암호화
* 하드웨어 보안 모델 사용
* 보안 워크스테이션을 사용하여 관리
* SQL 데이터 암호화 활성화
* 전송 중인 데이터 보호
* 파일 수준 데이터 암호화 적용

## <a name="enforce-multi-factor-authentication"></a>Multi-Factor Authentication 적용

Microsoft Azure의 데이터 액세스 및 제어에서 첫 번째 단계는 사용자 인증입니다. [Azure MFA(Multi-Factor Authentication)](../active-directory/authentication/multi-factor-authentication.md)는 사용자 이름 및 암호 이외의 다른 수단을 사용하여 사용자의 ID를 확인하는 방법입니다. 이 인증 방법은 간단한 로그인 프로세스에 대한 사용자 요구를 충족하는 동시에 데이터와 응용 프로그램에 대한 액세스를 보호합니다.

사용자에 대한 Azure MFA를 활성화하면 사용자 로그인 및 트랜잭션에 두 번째 보안 계층이 추가됩니다. 이 경우 트랜잭션이 파일 서버 또는 SharePoint Online에 있는 문서에 액세스할 수 있습니다. 또한 Azure MFA를 사용하면 IT는 손상된 자격 증명이 조직의 데이터에 액세스하는 가능성을 줄일 수 있습니다.

예를 들어 사용자에 대해 Azure MFA를 적용하고 전화 통화 또는 문자 메시지를 통해 확인하도록 구성하면 사용자의 자격 증명이 손상된 경우 공격자가 어떤 리소스에도 액세스할 수 없습니다. 공격자가 사용자의 전화기에 대한 액세스 권한을 갖고 있지 않기 때문입니다. 이 ID 보호 추가 계층을 추가하지 않는 조직은 자격 증명 도난 공격에 취약하며, 이로 인해 데이터가 손상될 수 있습니다.

인증 제어를 온-프레미스에 유지하려는 조직이 선택할 수 있는 대안 중 하나는 MFA 온-프레미스라고도 하는 [Azure Multi-factor Authentication 서버](../active-directory/authentication/howto-mfaserver-deploy.md)를 사용하는 것입니다. 이 방법을 사용하면 여전히 Multi-factor Authentication을 적용하면서도 MFA 서버를 온-프레미스에 유지할 수 있습니다.

Azure MFA에 대한 자세한 내용은 [클라우드에서 Azure Multi-Factor Authentication 시작](../active-directory/authentication/howto-mfa-getstarted.md)을 참조하세요.

## <a name="use-role-based-access-control-rbac"></a>RBAC(역할 기반 Access Control) 사용

[알아야 할 필요성](https://en.wikipedia.org/wiki/Need_to_know) 및 [최소 권한](https://en.wikipedia.org/wiki/Principle_of_least_privilege) 보안 원칙에 따라 액세스를 제한합니다. 이는 데이터 액세스에 대한 보안 정책을 적용하려는 조직에 필수적입니다. Azure 역할 기반 Access Control(RBAC)을 사용하여 특정 범위에서 사용자, 그룹 및 응용 프로그램에 권한을 할당할 수 있습니다. 역할 할당의 범위는 구독, 리소스 그룹 또는 단일 리소스일 수 있습니다.

Azure의 [기본 제공 RBAC 역할](../role-based-access-control/built-in-roles.md)을 활용하여 사용자에게 권한을 할당할 수 있습니다. Storage 계정을 관리해야 하는 클라우드 운영자를 위한 *Storage 계정 참여자* 및 *클래식 Storage 계정 참여자* 역할을 사용하여 클래식 Storage 계정을 관리하는 방법을 고려해 볼 수 있습니다. VM 및 저장소 계정을 관리해야 하는 클라우드 운영자의 경우 *Virtual Machine 참여자* 역할에 계정을 추가하는 방법을 고려해 보세요.

RBAC 같은 기능을 활용하여 데이터 액세스 제어를 적용하지 않는 조직은 사용자에게 필요 이상으로 많은 권한을 부여하게 될 수 있습니다. 그로 인해 일부 사용자가 원래는 액세스할 수 없어야 하는 데이터에 액세스할 수 있게 되어 데이터 손상이 발생할 수 있습니다.

Azure RBAC에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어](../role-based-access-control/role-assignments-portal.md) 문서를 참조하세요.

## <a name="encrypt-azure-virtual-machines"></a>Azure Virtual Machines 암호화

여러 조직에서 [미사용 데이터 암호화](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/)는 데이터 프라이버시, 규정 준수 및 데이터 주권을 위한 필수 단계입니다. Azure 디스크 암호화를 사용하면 IT 관리자가 Windows 및 Linux IaaS Virtual Machine(VM) 디스크를 암호화할 수 있습니다. Azure 디스크 암호화는 업계 표준인 Windows의 BitLocker 기능과 Linux의 DM-Crypt 기능을 활용하여 OS 및 데이터 디스크를 위한 볼륨 암호화를 제공합니다.

Azure 디스크 암호화를 활용하여 데이터를 보호하면 조직의 보안 및 규정 준수 요구 사항을 충족하는 데 도움이 됩니다. 조직에서는 암호화를 사용하여 데이터 무단 액세스와 관련된 위험을 완화하는 방법도 고려해야 합니다. 또한 민감한 데이터를 드라이브에 기록하기 전에 드라이브를 암호화하는 것이 좋습니다.

Azure 저장소 계정의 미사용 데이터를 보호할 수 있도록 VM의 데이터 볼륨 및 부팅 볼륨을 암호화해야 합니다. [Azure Key Vault](../key-vault/key-vault-whatis.md)를 활용하여 암호화 키 및 암호를 보호하세요.

온-프레미스 Windows Server의 경우 다음 암호화 모범 사례를 고려해 보세요.

* [BitLocker](https://technet.microsoft.com/library/dn306081.aspx)를 사용하여 데이터 암호화
* AD DS에 복구 정보를 저장합니다.
* BitLocker 키가 손상된 것으로 의심되는 경우 드라이브를 포맷하여 드라이브에서 BitLocker 메타데이터의 모든 인스턴스를 제거하거나 드라이브 전체를 암호 해독했다가 다시 암호화하는 것이 좋습니다.

데이터 암호화를 적용하지 않는 조직은 데이터 무결성 문제에 노출될 가능성이 높습니다. 예를 들어 악의적인 사용자가 데이터 및 손상된 계정을 가로채서 일반 형식 데이터에 무단으로 액세스하게 될 수 있습니다. 이러한 위험 요소 외에도 업계 규정을 준수해야 하는 회사는 데이터 보안에 충실하고 있으며 올바른 보안 컨트롤을 사용하여 데이터 보안을 강화하고 있다는 사실을 증명해야 합니다.

Azure 디스크 암호화에 대한 자세한 내용은 [Windows 및 Linux IaaS VM용 Azure 디스크 암호화](azure-security-disk-encryption.md) 문서를 참조하세요.

## <a name="use-hardware-security-modules"></a>하드웨어 보안 모듈 사용
산업 암호화 솔루션은 비밀 키를 사용하여 데이터를 암호화합니다. 따라서 이러한 키를 안전하게 보관해야 합니다. 키 관리는 데이터 보호의 필수 요소입니다. 왜냐하면 키는 데이터 암호화에 사용되는 비밀 키를 저장하는 데 활용되기 때문입니다.

Azure Disk Encryption은 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)를 사용하여 가상 머신 디스크의 모든 데이터가 Azure Storage에서 안전하게 암호화되도록 하는 동시에 Key Vault 구독의 암호 및 디스크 암호화 키를 제어하고 관리하도록 도와줍니다. Azure Key Vault를 사용하여 키 및 정책 사용을 감사해야 합니다.

데이터 암호화에 사용된 비밀 키를 보호하는 적절한 보안 컨트롤이 없으면 여러 가지 위험에 노출됩니다. 공격자가 비밀 키에 대한 액세스 권한을 획득하면 데이터를 해독하여 잠재적으로 기밀 정보에 액세스할 수 있게 됩니다.

Azure의 인증서 관리에 대한 일반적인 권장 사항은 [Azure의 인증서 관리: 할 일과 하지 말아야 할 일](https://blogs.msdn.microsoft.com/azuresecurity/2015/07/13/certificate-management-in-azure-dos-and-donts/) 문서를 참조하세요.

Azure Key Vault에 대한 자세한 내용은 [Azure Key Vault 시작](../key-vault/key-vault-get-started.md)을 참조하세요.

## <a name="manage-with-secure-workstations"></a>보안 워크스테이션을 사용하여 관리
대부분의 공격이 최종 사용자를 대상으로 하기 때문에 끝점은 주요 공격 지점 중 하나입니다. 공격자가 끝점을 손상시키면 사용자의 자격 증명을 활용하여 조직의 데이터에 액세스할 수 있습니다. 대부분의 끝점 공격은 최종 사용자가 로컬 워크스테이션의 관리자라는 사실을 활용할 수 있습니다.

보안 관리 워크스테이션을 사용하면 이러한 위험을 줄일 수 있습니다. [PAW(권한 있는 액세스 워크스테이션)](https://technet.microsoft.com/library/mt634654.aspx)를 사용하여 워크스테이션의 공격 노출 영역을 줄이는 것이 좋습니다. 보안 관리 워크스테이션은 이러한 공격 위험 중 일부를 완화하여 데이터를 안전하게 보호하는 데 도움이 됩니다. PAW를 사용하여 워크스테이션 보안을 강화하고 안전하게 보호하세요. 이는 중요한 계정, 작업 및 데이터 보호를 위해 보안 수준을 높이는 중요한 단계입니다.

끝점 보호가 부족하면 데이터가 취약해질 수 있으므로 데이터 위치(클라우드 또는 온-프레미스)에 관계없이 데이터를 사용하는 모든 장치에 보안 정책을 적용해야 합니다.

권한 있는 액세스 워크스테이션에 대한 자세한 내용은 [권한 있는 액세스 보안](https://technet.microsoft.com/library/mt631194.aspx) 문서를 참조하세요.

## <a name="enable-sql-data-encryption"></a>SQL 데이터 암호화 활성화
[Azure SQL Database 투명한 데이터 암호화](https://msdn.microsoft.com/library/dn948096.aspx)(TDE)는 응용 프로그램에 대한 변경 요구 없이 데이터베이스, 연결된 백업 및 저장된 트랜잭션 로그 파일에 대한 실시간 암호화 및 암호 해독을 수행하여 악의적인 활동의 위협으로부터 보호합니다.  TDE는 데이터베이스 암호화 키라는 대칭 키를 사용하여 전체 데이터베이스의 저장소를 암호화합니다.

저장소 전체를 암호화하는 경우에도 데이터베이스 자체까지 암호화해야 합니다. 이렇게 하면 데이터를 철저하게 보호할 수 있습니다. [Azure SQL Database](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx)를 사용하며 신용 카드 또는 주민 등록 번호 같은 중요한 데이터를 보호하려는 경우 여러 업계 표준(예: HIPAA, PCI)의 요구 사항을 충족하는 FIPS 140-2 검증 256비트 AES 암호화를 사용하여 데이터베이스를 암호화할 수 있습니다.

TDE를 사용하여 데이터베이스를 암호화할 때 [BPE(버퍼 풀 확장)](https://msdn.microsoft.com/library/dn133176.aspx) 관련 파일은 암호화되지 않는다는 사실을 이해해야 합니다. BPE 관련 파일에는 BitLocker 또는 [EFS(파일 시스템 암호화)](https://technet.microsoft.com/library/cc700811.aspx) 같은 파일 시스템 수준 암호화 도구를 사용해야 합니다.

보안 관리자나 데이터베이스 관리자처럼 권한이 부여된 사용자는 TDE를 사용하여 데이터베이스를 암호화된 경우에도 데이터에 액세스할 수 있으므로 아래 권장 사항을 준수해야 합니다.

* 데이터베이스 수준에서 SQL 인증
* RBAC 역할을 사용하여 Azure AD 인증
* 사용자와 응용 프로그램이 인증에 별도의 계정을 사용해야 합니다. 이러한 방식으로 사용자와 응용 프로그램에 부여되는 사용 권한을 제한하고 악의적인 활동의 위험을 줄일 수 있습니다
* 고정된 데이터베이스 역할(예: db_datareader 또는 db_datawriter)을 사용하여 데이터베이스 수준 보안을 구현하거나 응용 프로그램에 대한 사용자 지정 역할을 만들어서 선택한 데이터베이스 개체에 명시적 권한을 부여할 수 있습니다.

데이터베이스 수준 암호화를 사용하지 않는 조직은 SQL 데이터베이스에 있는 데이터를 손상시키는 공격에 취약할 수 있습니다.

SQL TDE 암호화에 대한 자세한 내용은 [Azure SQL Database를 사용하여 투명한 데이터 암호화](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx) 문서를 참조하세요.

## <a name="protect-data-in-transit"></a>전송 중인 데이터 보호

전송 중인 데이터 보호는 데이터 보호 전략에서 절대 빠질 수 없는 핵심입니다. 데이터는 여러 위치 사이를 이동하므로 항상 SSL/TLS 프로토콜을 사용하여 여러 위치 간에 데이터를 교환하는 것이 일반적인 권장 사항입니다. VPN(가상 사설망)을 사용하여 온-프레미스와 클라우드 인프라 간에 전체 통신 채널을 격리하려는 경우가 있습니다.

온-프레미스 인프라와 Azure 사이를 이동하는 데이터의 경우 HTTPS 또는 VPN처럼 적절한 안전 장치를 고려해야 합니다.

온-프레미스에 있는 여러 워크스테이션에서 Azure로의 액세스를 보호해야 하는 조직의 경우 [Azure 사이트 간 VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md)을 사용합니다.

온-프레미스에 있는 한 워크스테이션에서 Azure로의 액세스를 보호해야 하는 조직의 경우 [지점 및 사이트 간 VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md)을 사용합니다.

대용량 데이터 집합은 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 같은 전용 고속 WAN 링크를 통해 이동할 수 있습니다. ExpressRoute를 사용하기로 선택하는 경우 [SSL/TLS](https://support.microsoft.com/kb/257591) 또는 기타 프로토콜을 사용하여 응용 프로그램 수준에서 데이터를 암호화하면 보안 수준을 더욱 높일 수 있습니다.

Azure Portal을 통해 Azure Storage와 상호 작용하는 경우 모든 트랜잭션이 HTTPS를 통해 발생합니다. 또한 HTTPS를 통해 [저장소 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)를 사용하여 [Azure Storage](https://azure.microsoft.com/services/storage/) 및 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)와 상호 작용할 수 있습니다.

전송 중인 데이터 보호에 실패하는 조직은 [가로채기(man-in-the-middle) 공격](https://technet.microsoft.com/library/gg195821.aspx), [도청](https://technet.microsoft.com/library/gg195641.aspx) 및 세션 하이재킹에 좀 더 취약합니다. 이러한 공격은 기밀 데이터에 대한 액세스 권한을 획득하기 위한 첫 번째 단계일 수 있습니다.

Azure VPN 옵션에 대한 자세한 내용은 [VPN Gateway 계획 및 설계](../vpn-gateway/vpn-gateway-plan-design.md) 문서를 참조하세요.

## <a name="enforce-file-level-data-encryption"></a>파일 수준 데이터 암호화 적용

데이터 보안 수준을 높이는 또 다른 보호 계층은 파일 위치에 관계없이 파일 자체를 암호화하는 것입니다.

[Azure RMS](https://technet.microsoft.com/library/jj585026.aspx)는 암호화, ID 및 권한 부여 정책을 사용하여 파일 및 전자 메일을 보호합니다. Azure RMS는 조직 내부와 조직 외부에서 휴대폰, 태블릿 및 PC와 같은 여러 장치를 보호합니다. Azure RMS는 데이터가 조직의 경계를 벗어나는 경우에도 데이터를 유지하는 보호 수준을 추가하기 때문에 이 기능이 가능한 것입니다.

Azure RMS를 사용하여 파일을 보호하면 [FIPS 140-2](http://csrc.nist.gov/groups/STM/cmvp/standards.html)를 완벽하게 지원하는 업계 표준 암호화를 사용하는 것입니다. Azure RMS를 활용하여 데이터를 보호하면 클라우드 저장소 서비스처럼 IT의 제어를 받지 않는 저장소로 파일이 복사되더라도 파일 보호가 유지되므로 안심할 수 있습니다. 전자 메일을 통해 공유되는 파일도 마찬가지입니다. 파일이 전자 메일 메시지의 첨부 파일로 보호되며 보호되는 첨부 파일을 여는 방법에 대한 지침이 함께 제공됩니다.

Azure RMS 도입 계획을 세울 때 다음 사항을 권장합니다.

* [RMS 공유 앱](https://technet.microsoft.com/library/dn339006.aspx) 설치. 이 앱은 사용자가 간편하게 파일을 직접 보호할 수 있도록 Office 추가 기능을 설치하여 Office와 통합됩니다.
* Azure RMS를 지원하도록 응용 프로그램 및 서비스 구성
* 비즈니스 요구 사항을 반영하는 [사용자 지정 템플릿](https://technet.microsoft.com/library/dn642472.aspx) 만들기. 예: 모든 극비 관련 전자 메일에 적용해야 하는 극비 데이터 템플릿.

[데이터 분류](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) 및 파일 보호에 약한 조직은 데이터 유출에 좀 더 취약할 수 있습니다. 적절한 파일 보호 수단이 없는 조직은 비즈니스 통찰력을 얻고, 데이터 남용을 모니터링하고, 악의적인 파일 액세스를 방지할 수 없습니다.

Azure RMS에 대한 자세한 내용은 [Azure Rights Management 시작](https://technet.microsoft.com/library/jj585016.aspx) 문서를 참조하세요.
