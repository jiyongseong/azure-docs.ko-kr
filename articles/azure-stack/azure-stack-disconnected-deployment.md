---
title: Azure 스택에 대 한 azure 연결이 끊긴된 배포 결정 통합 시스템 | Microsoft Docs
description: 배포 계획 다중 노드 배포 Azure 스택 Azure 연결에 대 한 결정을 확인 합니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 49697a57e59b652fed4997d57bc7ae15cc596cf7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="azure-disconnected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Azure 스택에 대 한 결정을 계획 하는 azure 연결이 끊긴된 배포 시스템을 통합
결정 한 다음 [Azure 스택 하이브리드 클라우드 환경으로 통합 됩니다는 어떻게](azure-stack-connection-models.md), Azure 스택 배포 결정 사항 마무리 다음 수 있습니다.

연결 되지 않은 Azure 배포 옵션을 하면 배포 하 고 인터넷에 연결 하지 않고도 Azure 스택을 사용 합니다. 그러나 연결이 끊긴된 배포는 AD FS id 저장소 용량 기반 청구 모델을 제한 됩니다. 

경우이 옵션을 선택 하면:
- 보안 또는 Azure 스택 인터넷에 연결 되지 않은 환경에 배포 해야 하는 기타 제한 했습니다.
- 하려고 (사용 현황 데이터를 포함 하 여) 데이터 차단 Azure로 전송 되지 않도록 합니다.
- Azure 스택 순수 하 게 하는 회사 인트라넷에 배포 되며 하이브리드 시나리오에 원하지 않는 사설 클라우드 솔루션으로 사용 하려고 합니다.

> [!TIP]
> 경우에 따라 이러한 유형의 환경도 라고 "잠수함 시나리오"입니다.

연결이 끊긴된 배포 엄격 하 게 의미가 있는지 연결할 수 없으면 나중에 Azure 스택 인스턴스 Azure 테 넌 트 VM 하이브리드 시나리오에 대 한 합니다. 배포 하는 동안 Azure에 대 한 연결 없는 또는 Azure Active Directory를 id 저장소로 사용 하지 않음 의미 합니다.

## <a name="features-that-are-impaired-or-unavailable-in-disconnected-deployments"></a>장애가 있는 사용자 또는에서 사용할 수 없는 연결 되지 않은 배포 않는 기능 
Azure 스택은 장애가 있는 사용자 또는 완전히 연결이 끊어진된 모드에서 사용할 수 없는 일부 기능 및 기능은 해야 하므로 Azure에 연결 된 경우 가장 잘 작동 하도록 설계 되었습니다. 

|기능|연결이 끊긴된 모드의 영향|
|-----|-----|
|DSC 확장 VM 배포 후 구성 된 VM 배포|장애가 있는-DSC 확장을 찾고 인터넷에 최신 WMF 합니다.|
|Docker 확장 Docker 명령을 실행할 수 있는 VM 배포|장애가 있는 – Docker는 인터넷에서 최신 버전을 확인 하 고이 검사 되지 것입니다.|
|Azure 스택 포털에 대 한 설명서 링크|사용할 수 없음 – 링크 같은 의견 제공, 도움말, 빠른 시작 등을 사용 하는 인터넷 URL이 작동 하지 않습니다.|
|온라인 업데이트 관리 가이드를 참조 하는 경고 수정/완화|사용할 수 없음 – 경고 치료 인터넷 URL이 작동 하지 않습니다 사용 하는 링크입니다.|
|마켓플레이스 배포 – 선택 하 고 Azure Marketplace에서 직접 갤러리 패키지를 추가 하는 기능|장애가 있는 – 모든 인터넷 연결) (없이 연결이 끊어진된 모드에서 Azure 스택을 배포할 때 Azure 스택 포털을 사용 하 여 마켓플레이스 항목을 다운로드할 수 없습니다. 사용할 수 있습니다는 [마켓플레이스 배포 도구](https://docs.microsoft.com/azure/azure-stack/azure-stack-download-azure-marketplace-item#download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity) 마켓플레이스 항목 인터넷에 연결 하는 컴퓨터에 다운로드 한 다음 Azure 스택 환경에 전송 합니다.|
|Azure 스택 배포를 관리 하도록 Azure Active Directory 페더레이션 계정을 사용 하 여|사용할 수 없음 –이 기능은 Azure에 연결 해야 합니다. 로컬 Active Directory 인스턴스와 AD FS는 대신 사용 해야 합니다.|
|App Services|장애가 있는-업데이트 된 내용에 대 한 WebApps 인터넷 액세스가 필요할 수 있습니다.|
|CLI(Command Line Interface)|CLI 장애가 – 인증 및 구축 서비스 조항의 측면에서 기능을 감소 되었습니다.|
|Visual Studio-클라우드 검색|장애가 있는 – 클라우드 검색은 서로 다른 클라우드를 검색 하거나 또는 전혀 작동 하지 않습니다.|
|Visual Studio-AD FS|장애가 있는 –만 Visual Studio Enterprise에서는 AD FS를 지원 합니다.
원격 분석|사용할 수 없음-원격 분석 데이터에 따라 뿐 아니라 타사 갤러리 패키지와 Azure 스택에 대 한 원격 분석 데이터.|
|인증서|사용할 수 없음-인터넷 연결이 HTTPS의 컨텍스트에서 인증서 해지 목록 (CRL) 및 온라인 인증서 상태 프로토콜 (OSCP) 서비스에 대해 필요 합니다.|
|키 자격 증명 모음|장애가 있는-주요 자격 증명 모음에 대 한 일반적인 사용 사례는 런타임 시 비밀 정보를 읽는 응용 프로그램입니다. 이 응용 프로그램 디렉터리에 서비스 사용자를 필요합니다. Azure Active Directory에서 일반 사용자 (관리자가 아닌 사용자)는 서비스 사용자를 추가할 수는 기본적으로 됩니다. (ADFS를 사용 하 여) ad에서 하지 않습니다. 이 모든 응용 프로그램을 추가 하면 디렉터리 관리자를 항상 통과 해야 하나 때문에 종단 간 환경에 한 장애물을 배치 합니다.| 

## <a name="learn-more"></a>자세한 정보
- 사용 사례, 구매, 파트너 및 OEM 하드웨어 공급 업체에 대 한 정보를 참조 하십시오.는 [Azure 스택](https://azure.microsoft.com/overview/azure-stack/) 제품 페이지.
- Azure 스택에 대 한 로드맵 및 지리적 가용성에 대 한 정보에 대 한 통합된 시스템 백서를 참조: [Azure 스택: Azure의 확장](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)합니다. 
- Microsoft Azure 스택 패키징 및 가격에 대 한 자세한 내용을 보려면 [다운로드는.pdf](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf)합니다. 

## <a name="next-steps"></a>다음 단계
[데이터 센터 네트워크 통합](azure-stack-network.md)