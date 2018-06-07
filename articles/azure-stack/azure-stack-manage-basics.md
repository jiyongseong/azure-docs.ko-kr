---
title: Azure 스택 관리 기본 사항 | Microsoft Docs
description: Azure 스택을 관리 하기 위해 알아야 필요한에 대해 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 856738a7-1510-442a-88a8-d316c67c757c
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: mabrigg
ms.openlocfilehash: df4a5a17ad034ae5d6ab82791c020634a8758b71
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="azure-stack-administration-basics"></a>Azure 스택 관리 기본 사항
처음 사용 Azure 스택 관리 하는 경우 알아야 할 몇 가지 있습니다. 이 지침은 Azure 스택 운영자 사용자 역할에 대 한 개요 및 생산성을 높이려는 신속 하 게 해당 항목에 대 한 사용자에 게 알릴 사항 제공 합니다.

## <a name="understand-the-builds"></a>빌드 이해

### <a name="integrated-systems"></a>통합 시스템

Azure 스택 통합 시스템을 사용 하는 경우 업데이트 된 버전의 Azure 스택 업데이트 패키지를 통해 배포 됩니다. 이러한 패키지를 가져오고 업데이트 타일 관리자 포털을 사용 하 여 적용할 수 있습니다.
 
### <a name="development-kit"></a>개발 키트

Azure 스택 개발 키트를 사용 하는 경우 참조는 [Azure 스택 란?](.\asdk\asdk-what-is.md) 문서를 개발 키트 및 제한 사항의 용도 이해 하 고 있는지 확인 합니다. "샌드박스" 수 계산 Azure 스택 및 개발 하 고 있는 비-프로덕션 환경에서 앱을 테스트 개발 키트를 사용 해야 합니다. (배포 정보에 대 한 참조는 [Azure 스택 개발 키트 배포](.\asdk\asdk-deploy.md) 자습서입니다.)

Azure와 같은 우리 혁신 신속 하 게 합니다. 새 빌드를 정기적으로 릴리스 합니다 했습니다. 개발 키트를 실행 하는 경우 수행 해야 최신 빌드를 이동 하려면 [Azure 스택 재배포](.\asdk\asdk-redeploy.md)합니다. 업데이트 패키지를 적용할 수 없습니다. 이 프로세스는 시간이 소요 되지만 기능은 최신 기능을 사용해 시도할 수 있습니다. 개발 키트 설명서 웹 사이트에서 최신 릴리스 빌드를 반영합니다.

## <a name="learn-about-available-services"></a>사용 가능한 서비스에 대 한 자세한 내용은

인식 되는 서비스의 있습니다 사용할 수 있도록 사용자에 게 필요 합니다. Azure 스택 Azure 서비스의 하위 집합을 지원 합니다. 지원 되는 서비스의 목록 개선 될 것입니다.

**기본 서비스**

기본적으로 Azure 스택에 다음 "기본 서비스" Azure 스택을 배포 하는 경우:

- 컴퓨팅
- Storage
- 네트워킹
- Key Vault

이러한 기본 서비스와 인프라-as a Service (IaaS) 최소 구성으로 사용자에 게 제공할 수 있습니다.

**추가 서비스**

현재 다음과 같은 추가 플랫폼으로-서비스 (PaaS) 서비스가 지원 됩니다.

- App Service
- Azure 기능
- SQL 및 MySQL 데이터베이스

이러한 서비스 있습니다 있습니다 사용할 수 있도록 사용자에 게 전에 추가 구성이 필요 합니다. 자세한 내용은 "자습서" 및 Azure 스택 연산자 설명서의 "방법 guides\Offer 서비스" 섹션을 참조 하십시오.

**서비스 로드맵**

Azure 서비스에 대 한 지원을 추가 하려면 azure 스택 계속 됩니다. 프로젝션 된 로드맵에 대 한 참조는 [Azure 스택: Azure의 확장](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409) 백서입니다. 모니터링할 수도 있습니다는 [Azure 스택 블로그 게시물](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview) 새 공지에 대 한 합니다.

## <a name="what-account-should-i-use"></a>사용할 계정은?
Azure 스택을 관리할 때 알고 있어야 하는 몇 가지 계정 고려 사항이 있습니다. 특히 배포에서는 Windows Server Active Directory Federation Services (AD FS)를 사용 하 여 Azure Active Directory (Azure AD) 대신 id 공급자로 합니다. 다음 계정 고려 사항 통합 Azure 스택 시스템과 ASDK 배포 모두에 적용 됩니다.


|계좌|Azure AD|AD FS|
|-----|-----|-----|
|로컬 관리자 (. \Administrator)|ASDK 호스트 관리자|ASDK 호스트 관리자|
|AzureStack\AzureStackAdmin|ASDK 호스트 관리자<br><br>Azure 스택 관리 포털에 로그인 할 수 있습니다.<br><br>보기 및 관리 서비스 패브릭 링에 대 한 액세스|ASDK 호스트 관리자<br><br>Azure 스택 관리 포털에 액세스할 수 없습니다<br><br>보기 및 관리 서비스 패브릭 링에 대 한 액세스<br><br>더 이상 기본 공급자 구독 (DP)의 소유자|
|AzureStack\CloudAdmin|에 액세스 하 고 권한 있는 끝점 내에서 허용 된 명령을 실행할 수 있습니다.|에 액세스 하 고 권한 있는 끝점 내에서 허용 된 명령을 실행할 수 있습니다.<br><br>ASDK 호스트에 로그인 할 수 없습니다.<br><br>기본 공급자 구독 (DP)의 소유자|
|Azure AD 전역 관리자|설치 중에 사용<br><br>기본 공급자 구독 (DP)의 소유자|해당 없음|
|

## <a name="what-tools-do-i-use-to-manage"></a>관리 도구에 대해 사용 합니까?
 
사용할 수는 [관리자 포털](azure-stack-manage-portals.md) 또는 PowerShell을 Azure 스택을 관리 합니다. 기본 개념을 파악 하는 가장 쉬운 방법은 포털을 통해 됩니다. PowerShell을 사용 하려는 경우에 준비 단계입니다. 수행 해야 [설치](azure-stack-powershell-install.md) PowerShell [다운로드](azure-stack-powershell-download.md) 추가 모듈 및 [구성](azure-stack-powershell-configure-admin.md) PowerShell 합니다.

Azure 스택 하는 기본 배포, 관리 및 조직 메커니즘으로 Azure 리소스 관리자를 사용합니다. Azure 스택을 관리 하 고 사용자가 지원할 수 있도록 할 리소스 관리자에 대해 알아야 합니다. 참조는 [Azure 리소스 관리자 시작](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) 백서입니다.

## <a name="your-typical-responsibilities"></a>일반적인 사용자의 책임

사용자가 서비스를 사용 하려고 합니다. 자신의 관점에서 이러한 서비스를 사용할 수 있도록 주 역할은 합니다. 서비스를 제공 하 고 계획과 제안, 할당량을 만들어 이러한 서비스를 사용할 수 있도록 해야 합니다. 자세한 내용은 참조 [Azure 스택에서 서비스 제공의 개요](azure-stack-offer-services-overview.md)합니다. 

항목을 추가 해야 또한 [마켓플레이스에](azure-stack-marketplace.md), 가상 컴퓨터 이미지와 같은 합니다. 가장 쉬운 방법은 [스택에 Azure에서 Azure 마켓플레이스 항목을 다운로드](azure-stack-download-azure-marketplace-item.md)합니다.

> [!NOTE]
> 계획, 혜택 및 서비스를 테스트 하려는 경우 사용 해야는 [사용자 포털](azure-stack-manage-portals.md)되지 않으면 관리자 포털.

서비스를 제공할 뿐만 아니라 모든 일반 임무 연산자의 실행 되도록 유지 Azure 스택을 수행 해야 합니다. 이러한 의무는 다음과 같습니다.

- 사용자 계정 추가 (에 대 한 [Azure Active Directory](azure-stack-add-new-user-aad.md) 배포 또는 [Active Directory Federation Services](azure-stack-add-users-adfs.md) 배포)
- [역할 기반 액세스 제어 (RBAC) 역할을 할당](azure-stack-manage-permissions.md) (이 제한 되지 않습니다 관리자에 게.)
- [인프라 상태 모니터](azure-stack-monitor-health.md)
- 관리 [네트워크](azure-stack-viewing-public-ip-address-consumption.md) 및 [저장소](azure-stack-manage-storage-accounts.md) 리소스
- 예를 들어 잘못 된 하드웨어를 교체 [실패 한 디스크를 교체](azure-stack-replace-disk.md)합니다.

## <a name="what-to-tell-your-users"></a>사용자에 게 알릴 내용

Azure 스택 서비스와 작동 하는 방법, 환경에 연결 하는 방법 및 제품을 구독할 하는 방법을 알고 있는 사용자에 게 해야 합니다. 사용자에 게 제공 하려는 경우에 모든 사용자 지정 문서 외에도 Azure 스택 사용자 설명서 사이트에 사용자를 보낼 수 있습니다.

**Azure 스택 서비스와 작동 하는 방법을 이해합니다**

사용자가 서비스를 사용 하 고 Azure 스택으로 앱을 빌드하려면 먼저 이해 해야 하는 정보는. 예를 들어 특정 PowerShell 및 API 버전 요구 사항이 있습니다. 또한 Azure에서 서비스와 Azure 스택에 해당 하는 서비스 간의 몇 가지 기능 델타 있습니다. 사용자가 다음 문서를 검토 하 고 있는지 확인 합니다.

- [주요 고려 사항: Azure 스택에 대 한 앱을 빌드 또는 서비스를 사용 하 여](user/azure-stack-considerations.md)
- [Azure 스택의 가상 컴퓨터에 대 한 고려 사항](user/azure-stack-vm-considerations.md)
- [저장소: 차이점과 고려 사항](user/azure-stack-acs-differences.md)

이 문서의 정보를에서 Azure에 서비스 및 Azure 스택 차이 보여줍니다. 전역 Azure 설명서에서 Azure 서비스에 사용할 수 있는 정보를 보충 합니다.

**사용자로 Azure 스택에 연결 합니다.**

개발 키트 환경에서 사용자 개발 키트 호스트에 대 한 원격 데스크톱 액세스 없는 경우 이러한 연결을 구성 해야 가상 사설망 (VPN) Azure 스택 액세스할 수 있습니다. 참조 [Azure 스택 연결할](azure-stack-connect-azure-stack.md)합니다. 

사용자가 알아 두어야 할 하는 방법을 [사용자 포털에 액세스해보십시오 ](user/azure-stack-use-portal.md) 또는 PowerShell을 통해 연결 하는 방법입니다. 통합된 시스템 환경에서 사용자 포털 주소 배포 마다 다릅니다. 올바른 URL을 사용 하면 사용자에 게 제공 해야 합니다.

PowerShell을 사용 하는 경우 사용자가 서비스를 사용 하려면 먼저 리소스 공급자를 등록 해야 합니다. (리소스 공급자 서비스를 관리 하는 데 사용 합니다. 네트워킹 리소스 공급자 가상 네트워크, 네트워크 인터페이스 부하 분산 장치 등 리소스를 관리 하는 예를 들어.) 해야 [설치](user/azure-stack-powershell-install.md) PowerShell [다운로드](user/azure-stack-powershell-download.md) 추가 모듈 및 [구성](user/azure-stack-powershell-configure-user.md) PowerShell (포함 하는 리소스 공급자 등록).

**제품 구독**

해야 사용자가 서비스에 액세스 하기 전에 [프로그램 제품 구독](azure-stack-subscribe-plan-provision-vm.md) 연산자로 만든 합니다.

## <a name="where-to-get-support"></a>지원을 받을 수 있는 위치

### <a name="integrated-systems"></a>통합 시스템

통합된 된 시스템에 대 한는 조정 된 에스컬레이션 및 Microsoft와 (oem) 하드웨어 파트너 사이의 확인 프로세스.

클라우드 서비스 문제 라면를 통해 Microsoft 지원 CSS (고객 서비스) 제공 됩니다. 관리 포털의 오른쪽 위 모서리에 있는 도움말 및 지원 아이콘 (물음표)를 클릭 한 다음 클릭 하는 경우 **새로운 지원 요청**, 지원 요청을 직접 열 수 있는 사이트 열립니다.

배포에는 문제가 있는 경우 패치 및 업데이트, (필드 교체 장치 포함), 하드웨어 및 하드웨어 수명 주기 호스트에서 실행 되는 소프트웨어와 같은 하드웨어 브랜드 소프트웨어를 먼저에 문의 OEM 하드웨어 공급 업체에 문의 합니다.

다른 작업에 대 한 Microsoft CSS에 문의 합니다.

### <a name="development-kit"></a>개발 키트

개발 키트에 대 한 지원 관련 질문에 요청할 수 있습니다는 [Microsoft 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)합니다. 관리 포털의 오른쪽 위 모서리에 있는 도움말 및 지원 아이콘 (물음표)를 클릭 한 다음 클릭 하는 경우 **새로운 지원 요청**, 직접 포럼 사이트 열립니다. 이러한 포럼 정기적으로 모니터링 됩니다. 개발 키트 평가 환경 이기 때문에 Microsoft CSS 통해 제공 되는 공식 지원 되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [Azure 스택의 영역 관리](azure-stack-region-management.md)


