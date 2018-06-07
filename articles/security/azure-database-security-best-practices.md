---
title: Azure 데이터베이스 보안 모범 사례 | Microsoft Docs
description: 이 문서에서는 Azure 데이터베이스 보안을 위한 일단의 모범 사례를 제공합니다.
services: security
documentationcenter: na
author: unifycloud
manager: mbaldwin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: 81acf90f0d600c0c3dafa2a4ccd2f8564fd18c9a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="azure-database-security-best-practices"></a>Azure 데이터베이스 보안 모범 사례

보안은 데이터베이스 관리에서 가장 중요하며 항상 Azure SQL Database의 최우선 순위였습니다. HIPAA, ISO 27001/27002, PCI DSS 수준 1 등을 포함한 대부분의 규정 또는 보안 요구 사항을 충족할 수 있도록 데이터베이스를 철저히 보호할 수 있습니다. 보안 규정 준수 인증의 최신 목록은 [Microsoft Azure 보안 센터 사이트](http://azure.microsoft.com/support/trust-center/services/)에서 제공합니다. 또한 규정 요구 사항에 따라 특정 Azure 데이터 센터에 데이터베이스를 배치하도록 선택할 수도 있습니다.

이 문서에서는 Azure 네트워크 보안 모범 사례 모음에 대해 설명합니다. 이러한 모범 사례는 Azure 데이터베이스 보안 경험과 여러분과 같은 고객의 경험에서 얻은 것입니다.

각 모범 사례에 대해 다음과 같이 설명합니다.

-   각 모범 사례
-   해당 모범 사례를 사용해야 하는 이유
-   해당 모범 사례를 사용하지 않을 경우에 발생할 수 있는 결과
-   해당 모범 사례를 사용하는 방법을 알아보는 방법

이 Azure 데이터베이스 보안 모범 사례 문서는 이 문서가 작성된 당시에 있었던 합의된 의견과 Azure 플랫폼 기능 및 특징 집합을 기반으로 합니다. 이 문서는 시간이 지남에 따라 변화하는 의견 및 기술을 반영하도록 주기적으로 업데이트 됩니다.

이 문서에서 다루는 Azure 데이터베이스 보안 모범 사례는 다음과 같습니다.

-   방화벽 규칙을 사용하여 데이터베이스 액세스 제한
-   데이터베이스 인증 사용
-   암호화를 사용하여 데이터 보호
-   전송 중인 데이터 보호
-   데이터베이스 감사 사용
-   데이터베이스 위협 검색 사용


## <a name="use-firewall-rules-to-restrict-database-access"></a>방화벽 규칙을 사용하여 데이터베이스 액세스 제한

Microsoft Azure SQL Database는 Azure 및 기타 인터넷 기반 응용 프로그램의 관계형 데이터베이스 서비스를 제공합니다. 액세스 보안을 제공하기 위해 SQL Database는 IP 주소로 연결을 제한하는 방화벽 규칙, 사용자가 자신의 ID를 증명하도록 요구하는 인증 메커니즘 및 특정 작업과 데이터에 대한 사용자를 제한하는 권한 부여 메커니즘을 사용하여 액세스를 제어합니다. 방화벽은 권한이 있는 컴퓨터를 지정할 때까지 데이터베이스 서버에 대한 모든 액세스를 금지합니다. 방화벽은 각 요청이 시작된 IP 주소의 데이터베이스에 대한 액세스를 허용합니다.

![방화벽 규칙](./media/azure-database-security-best-practices/azure-database-security-best-practices-Fig1.png)

Azure SQL Database 서비스는 TCP 포트 1433을 통해서만 사용할 수 있습니다. 사용자의 컴퓨터에서 SQL Database에 액세스하려면 클라이언트 컴퓨터 방화벽이 TCP 포트 1433을 통해 나가는 TCP 통신을 허용해야 합니다. 다른 응용 프로그램에 필요하지 않은 경우 방화벽 규칙을 사용하여 1433 TCP 포트에서 인바운드 연결을 차단합니다.

연결 프로세스의 일부로 Azure 가상 머신에서 연결은 각 작업자 역할에 대한 고유한 다른 IP 주소 및 포트에 리디렉션됩니다. 포트 번호의 범위는 11000~11999입니다. TCP 포트에 대한 자세한 내용은 [ADO.NET 4.5 및 SQL Database2에 대한 1433 이외의 포트](https://docs.microsoft.com/azure/sql-database/sql-database-develop-direct-route-ports-adonet-v12)를 참조하세요.

> [!Note]
> SQL Database의 방화벽 규칙에 대한 자세한 내용은 [SQL Database 방화벽 규칙](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)을 참조하세요.

## <a name="enable-database-authentication"></a>데이터베이스 인증 사용
SQL Database는 인증을 위해 두 가지 인증 유형, 즉 SQL 인증 및 Azure AD 인증(Azure Active Directory 인증)을 지원합니다.

**SQL 인증**은 다음과 같은 경우에 권장됩니다.

-   SQL Azure에서 모든 사용자가 Windows 도메인에서 인증되지 않는 혼합된 운영 체제 환경을 지원합니다.
-   SQL Azure에서 SQL Server 인증이 필요한 이전 응용 프로그램 및 타사 제공 응용 프로그램을 지원합니다.
-   사용자가 알 수 없거나 신뢰할 수 없는 도메인에서 연결합니다. 예를 들어 기존 고객이 할당된 SQL Server 로그인과 연결하여 주문 상태를 수신하는 응용 프로그램이 있습니다.
-   SQL Azure에서 사용자가 자신의 ID를 만드는 웹 기반 응용 프로그램을 지원합니다.
-   소프트웨어 개발자가 미리 설정된 SQL Server 로그인을 기반으로 복잡한 권한 계층 구조를 사용하여 응용 프로그램을 배포합니다.

> [!Note]
> 그러나 SQL Server 인증에서는 Kerberos 보안 프로토콜을 사용할 수 없습니다.

**SQL 인증**을 사용하는 경우 다음을 수행해야 합니다.

-   강력한 자격 증명을 직접 관리합니다.
-   연결 문자열에서 자격 증명을 보호합니다.
-   (잠재적으로) 네트워크를 통해 웹 서버에서 데이터베이스로 전달되는 자격 증명을 보호합니다. 자세한 내용은 [방법: ASP.NET 2.0에서 SQL 인증을 사용하여 SQL Server에 연결(영문)](https://msdn.microsoft.com/library/ms998300.aspx)을 참조하세요.

**Azure Active Directory 인증**은 Azure AD(Azure Active Directory)의 ID를 사용하여 Microsoft Azure SQL Database 및 [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is)에 연결하는 메커니즘입니다. Azure AD 인증을 사용하면 데이터베이스 사용자 및 다른 Microsoft 서비스의 ID를 하나의 중앙 위치에서 관리할 수 있습니다. 중앙 ID 관리는 데이터베이스 사용자 관리를 위한 단일 위치를 제공하며 권한 관리를 간소화합니다. 이점은 다음과 같습니다.

-   SQL Server 인증에 대한 대안을 제공합니다.
-   데이터베이스 서버 전체에서 사용자 ID의 확산을 중지합니다.
-   한 곳에서 암호를 회전할 수 있습니다.
-   고객이 외부(AAD) 그룹을 사용하여 데이터베이스 사용 권한을 관리할 수 있습니다.
-   Windows 통합 인증 또는 Azure Active Directory에서 지원하는 기타 인증을 사용하여 암호 저장을 제거할 수 있습니다.
-   Azure AD 인증에서는 포함된 데이터베이스 사용자를 통해 데이터베이스 수준에서 ID를 인증합니다.
-   Azure AD는 SQL Database에 연결되는 응용 프로그램에 대한 토큰 기반 인증을 지원합니다.
-   Azure AD 인증은 도메인 동기화 없이 로컬 Azure Active Directory에 대해 ADFS(도메인 페더레이션) 또는 기본 사용자/암호 인증을 지원합니다.
-   Azure AD는 MFA(Multi-Factor Authentication)를 포함하는 Active Directory 유니버설 인증을 사용하는 SQL Server Management Studio를 통해 연결하도록 지원합니다. MFA는 전화 통화, 문자 메시지, 모바일 앱 알림 등의 여러 가지 간편한 검증 옵션을 제공하는 강력한 인증을 포함합니다. 자세한 내용은 [SQL Database 및 SQL Data Warehouse를 사용한 Azure AD MFA에 대한 SSMS 지원](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication)을 참조하세요.

구성 단계에는 Azure Active Directory 인증의 구성 및 사용을 위한 다음 절차가 포함됩니다.

-   Azure AD를 만들고 채웁니다.
-   선택 사항: 현재 Azure 구독과 연결된 Active Directory를 연결하거나 변경합니다.
-   Azure SQL Server 또는 [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/)에 대한 Azure Active Directory 관리자를 만듭니다.
- 클라이언트 컴퓨터를 구성합니다.
-   Azure AD ID에 매핑된 데이터베이스에서 포함된 데이터베이스 사용자를 만듭니다.
-   Azure AD ID를 사용하여 데이터베이스에 연결합니다.

자세한 내용은 [여기](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)서 찾을 수 있습니다.

## <a name="protect-your-data-using-encryption"></a>암호화를 사용하여 데이터 보호

[Azure SQL Database TDE(투명한 데이터 암호화)](https://msdn.microsoft.com/library/dn948096.aspx)는 응용 프로그램을 변경하지 않고도 데이터베이스, 연결된 백업 및 미사용 트랜잭션 로그 파일에 대한 실시간 암호화 및 암호 해독을 수행하여 악의적인 활동의 위협으로부터 보호합니다. TDE는 데이터베이스 암호화 키라는 대칭 키를 사용하여 전체 데이터베이스의 저장소를 암호화합니다.

저장소 전체를 암호화하는 경우에도 데이터베이스 자체까지 암호화해야 합니다. 이렇게 하면 데이터를 철저하게 보호할 수 있습니다. Azure SQL Database를 사용하고 있고 신용 카드 또는 주민 등록 번호와 같은 중요한 데이터를 보호하려는 경우, 다양한 업계 표준(예: HIPAA, PCI)의 요구 사항을 충족하는 FIPS 140-2 검증 256비트 AES 암호화를 사용하여 데이터베이스를 암호화할 수 있습니다.

TDE를 사용하여 데이터베이스를 암호화하는 경우 [BPE(버퍼 풀 확장)](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) 관련 파일은 암호화되지 않는다는 것을 이해해야 합니다. BPE 관련 파일에는 [BitLocker](https://technet.microsoft.com/library/cc732774) 또는 [EFS(파일 시스템 암호화)](https://technet.microsoft.com/library/cc700811.aspx)와 같은 파일 시스템 수준 암호화 도구를 사용해야 합니다.

보안 관리자나 데이터베이스 관리자처럼 권한이 부여된 사용자는 TDE를 사용하여 데이터베이스를 암호화된 경우에도 데이터에 액세스할 수 있으므로 아래 권장 사항을 준수해야 합니다.

-   데이터베이스 수준 SQL 인증을 사용해야 합니다.
-   [RBAC 역할](https://docs.microsoft.com/azure/role-based-access-control/overview)을 사용하는 Azure AD 인증을 사용해야 합니다.
-   사용자와 응용 프로그램이 인증에 별도의 계정을 사용해야 합니다. 이렇게 하면 사용자와 응용 프로그램에 부여되는 사용 권한을 제한하고 악의적인 활동의 위험을 줄일 수 있습니다.
-   고정된 데이터베이스 역할(예: db_datareader 또는 db_datawriter)을 사용하여 데이터베이스 수준 보안을 구현하거나 응용 프로그램에 대한 사용자 지정 역할을 만들어 선택한 데이터베이스 개체에 명시적 권한을 부여할 수 있습니다.

데이터를 암호화하는 다른 방법으로 다음을 고려해 보세요.

-   [셀 수준 암호화](https://msdn.microsoft.com/library/ms179331.aspx) 는 특정 열 또는 서로 다른 암호화 키를 가진 데이터의 셀도 암호화합니다.
-   Always Encrypted를 통한 사용 중 암호화: [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx)를 사용하면 클라이언트에서 클라이언트 응용 프로그램 내의 중요한 데이터를 암호화하고 데이터베이스 엔진(SQL Database 또는 SQL Server)에 암호화 키를 공개하지 않을 수 있습니다. 따라서 Always Encrypted는 데이터를 소유하고 볼 수 있는 사용자와 데이터를 관리하지만 액세스할 수 없는 사용자를 구별합니다.
-   행 수준 보안 사용: 행 수준 보안을 사용하면 고객이 쿼리를 실행하는 사용자의 특성(예: 그룹 멤버 자격 또는 실행 컨텍스트)에 따라 데이터베이스 테이블의 행에 대한 액세스를 제어할 수 있습니다. 자세한 내용은 [행 수준 보안](https://msdn.microsoft.com/library/dn765131)을 참조하세요.

## <a name="protect-data-in-transit"></a>전송 중인 데이터 보호
전송 중인 데이터 보호는 데이터 보호 전략에서 절대 빠질 수 없는 핵심입니다. 데이터는 여러 위치 사이를 이동하므로 항상 SSL/TLS 프로토콜을 사용하여 여러 위치 간에 데이터를 교환하는 것이 일반적인 권장 사항입니다. VPN(가상 사설망)을 사용하여 온-프레미스와 클라우드 인프라 간에 전체 통신 채널을 격리하려는 경우가 있습니다.

온-프레미스 인프라와 Azure 사이를 이동하는 데이터의 경우 HTTPS 또는 VPN처럼 적절한 안전 장치를 고려해야 합니다.

온-프레미스에 있는 여러 워크스테이션에서 Azure로의 액세스를 보호해야 하는 조직의 경우 [Azure 사이트 간 VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-site-to-site-create)을 사용합니다.

온-프레미스 또는 오프-프레미스에 있는 개별 워크스테이션에서 Azure로의 액세스를 보호해야 하는 조직의 경우 [지점 및 사이트 간 VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-point-to-site-create)을 사용하는 것이 좋습니다.

대용량 데이터 집합은 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 같은 전용 고속 WAN 링크를 통해 이동할 수 있습니다. ExpressRoute를 사용하기로 선택하는 경우 [SSL/TLS](https://support.microsoft.com/kb/257591) 또는 기타 프로토콜을 사용하여 응용 프로그램 수준에서 데이터를 암호화하면 보안 수준을 더욱 높일 수 있습니다.

Azure Portal을 통해 Azure Storage와 상호 작용하는 경우 모든 트랜잭션이 HTTPS를 통해 발생합니다. 또한 HTTPS를 통해 [저장소 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)를 사용하여 [Azure Storage](https://azure.microsoft.com/services/storage/) 및 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)와 상호 작용할 수 있습니다.

전송 중인 데이터 보호에 실패하는 조직은 [가로채기(man-in-the-middle) 공격](https://technet.microsoft.com/library/gg195821.aspx), [도청](https://technet.microsoft.com/library/gg195641.aspx) 및 세션 하이재킹에 좀 더 취약합니다. 이러한 공격은 기밀 데이터에 대한 액세스 권한을 획득하기 위한 첫 번째 단계일 수 있습니다.

Azure VPN 옵션에 대한 자세한 내용은 [VPN Gateway 계획 및 설계](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design) 문서를 참조하세요.

## <a name="enable-database-auditing"></a>데이터베이스 감사 사용
SQL Server 데이터베이스 엔진 또는 개별 데이터베이스의 인스턴스를 감사하는 작업에는 데이터베이스 엔진에서 발생하는 이벤트를 추적하고 기록하는 작업이 포함됩니다. SQL Server 감사를 사용하면 서버 수준 이벤트에 대한 서버 감사 사양과 데이터베이스 수준 이벤트에 대한 데이터베이스 감사 사양을 포함할 수 있는 서버 감사를 만들 수 있습니다. 감사 이벤트는 이벤트 로그 또는 감사 파일에 기록될 수 있습니다.

설치에 대한 정부 또는 표준 요구 사항에 따라 SQL Server에 대한 여러 수준의 감사가 있습니다. SQL Server 감사는 다양한 서버 및 데이터베이스 개체에 대한 감사를 사용, 저장 및 확인하는 데 필요한 도구와 프로세스를 제공합니다.

[Azure SQL Database 감사](https://docs.microsoft.com/azure/sql-database/sql-database-auditing)는 데이터베이스 이벤트를 추적하고 Azure Storage 계정의 감사 로그에 이벤트를 기록합니다.

감사는 규정 준수를 유지 관리하고, 데이터베이스 작업을 이해하고, 비즈니스 문제나 의심스러운 보안 위반을 나타낼 수 있는 불일치 및 이상 활동을 파악하는 데 도움이 될 수 있습니다.

감사를 사용하면 규정 표준을 보다 쉽게 준수할 수 있지만 규정을 완전히 준수한다고 보장할 수는 없습니다.

데이터베이스 감사 및 사용하도록 설정하는 방법에 대한 자세한 내용은 [Azure Security Center에서 SQL 서버에 대한 감사 및 위협 검색 사용](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-servers) 문서를 참조하세요.

## <a name="enable-database-threat-detection"></a>데이터베이스 위협 검색 사용
SQL 위협 검색을 사용하면 비정상적인 활동에 대한 보안 경고를 제공하여 발생할 수 있는 잠재적인 위협을 검색하고 대응할 수 있습니다. 의심스러운 데이터베이스 활동, 잠재적 취약성 및 SQL 삽입 공격뿐만 아니라 비정상적인 데이터베이스 액세스 패턴에 대한 경고도 받을 수 있습니다. SQL 위협 감지 경고는 의심스러운 활동에 대한 세부 정보 제공하고 위협을 조사하고 완화하는 방법에 대한 조치를 권장합니다.

예를 들어 SQL 삽입은 데이터 기반 응용 프로그램 공격에 사용되는 인터넷 상의 일반적인 웹 응용 프로그램 보안 문제 중 하나입니다. 공격자는 응용 프로그램의 취약성을 이용하여 악의적인 SQL 문을 응용 프로그램 항목 필드에 삽입하고 데이터베이스의 데이터를 침범하거나 수정합니다.

Azure Portal에서 데이터베이스에 대한 위협 검색을 설정하는 방법에 대한 자세한 내용은 [SQL Database 위협 검색](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)을 참조하세요.

또한 SQL 위협 검색에서는 경고를 [Azure Security Center](https://azure.microsoft.com/services/security-center/)와 통합합니다. 이 서비스를 60일 동안 체험해 보세요.

데이터베이스 위협 검색 및 사용하도록 설정하는 방법에 대한 자세한 내용은 [Azure Security Center에서 SQL 서버에 대한 감사 및 위협 검색 사용](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-servers) 문서를 참조하세요.

## <a name="conclusion"></a>결론
Azure 데이터베이스는 다양한 조직 및 규정 준수 요구 사항을 충족하는 모든 보안 기능을 갖춘 강력한 데이터베이스 플랫폼입니다. 데이터에 대한 물리적 액세스를 제어하고 투명한 데이터 암호화, 셀 수준 암호화 또는 행 수준 보안이 포함된 다양한 데이터 보안 옵션을 파일, 열 또는 행 수준에서 사용하여 데이터를 보호할 수 있습니다. 또한 Always Encrypted를 사용하면 암호화된 데이터에 대한 작업을 수행하여 응용 프로그램 업데이트 프로세스를 간소화할 수 있습니다. 따라서 SQL Database 활동의 감사 로그에 액세스하면 필요한 정보를 얻을 수 있으므로 데이터에 액세스하는 방법과 시기를 알 수 있습니다.

## <a name="next-steps"></a>다음 단계
- 방화벽 규칙에 대해 자세히 알아보려면 [방화벽 규칙](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)을 참조하세요.
- 사용자와 로그인에 대해 알아보려면 [로그인 관리](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins)를 참조하세요.
- 자습서는 [Azure SQL Database 보안](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial)을 참조하세요.
