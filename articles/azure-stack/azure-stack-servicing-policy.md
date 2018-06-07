---
title: Azure 스택 서비스 정책 | Microsoft Docs
description: Azure 스택 서비스 정책 및 지원 되는 상태가 통합된 시스템을 유지 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: brenduns
ms.reviewer: harik
ms.openlocfilehash: 77cc2f80588a104880e8149daccc6debd1ec43bc
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="azure-stack-servicing-policy"></a>정책 서비스는 azure 스택
이 문서에서는 Azure 스택 통합 시스템 및 지원 되는 상태 시스템을 유지 하기 위해 수행 해야 하는 것에 대 한 서비스 정책을 설명 합니다. 

## <a name="update-package-types"></a>업데이트 패키지 형식

통합된 시스템에 대 한 업데이트 패키지는 다음과 같은 두 종류가 있습니다. 

- **Microsoft 소프트웨어 업데이트**합니다. Microsoft는 Microsoft 소프트웨어 업데이트 패키지에 대 한 종단 간 서비스 주기 담당 합니다. 이러한 패키지는 최신 Windows Server 보안 업데이트, 비보안 업데이트 및 Azure 스택 기능 업데이트를 포함할 수 있습니다. Microsoft에서 직접 이러한 업데이트 패키지를 다운로드할 수 있습니다.

- **OEM 하드웨어 공급 업체에서 제공한 업데이트**합니다. Azure 스택 하드웨어 파트너는 종단 간 서비스 수명 주기 (지침 포함) 하드웨어 관련 펌웨어 및 드라이버 업데이트 패키지에 대 한 책임이 있습니다. 또한 Azure 스택 하드웨어 파트너는 소유 하 고 모든 소프트웨어 및 하드웨어 수명 주기 호스트 하드웨어에 대 한 지침 유지 관리 합니다. OEM 하드웨어 공급 업체를 이러한 호스트 자체 다운로드 사이트에서 패키지를 업데이트 합니다.


## <a name="update-package-release-cadence"></a>업데이트 패키지 릴리스 일정
Microsoft는 매월 주기로 소프트웨어 업데이트 패키지를 해제 하기 위해 필요 합니다. 그러나는 한 달에 있거나 전혀 업데이트 릴리스 있을 수 있습니다. OEM 하드웨어 공급 업체에 필요할 때 해당 업데이트를 출시 합니다. 

계획 하 고 업데이트를 관리 하는 방법과에서 현재 버전을 확인 하는 방법에 대 한 설명서 찾기 [관리 업데이트 개요](azure-stack-updates.md)합니다. 를 다운로드 하는 방법을 포함 한 특정 업데이트에 대 한 정보에 대 한 참조를 업데이트 하는 작업에 대 한 릴리스 정보: 
- [Azure 스택 1803 업데이트](azure-stack-update-1803.md)
- [Azure 스택 1802 업데이트](azure-stack-update-1802.md)
- [Azure 스택 1712 업데이트](azure-stack-update-1712.md)



## <a name="hotfixes"></a>핫픽스
경우에 따라서는 Microsoft 제공 핫픽스 Azure 스택에 대 한 해당 주소는 예방 또는 시간이 중요 한 특정 문제를 합니다.  각 핫픽스 문제, 원인 및 해결 방법에 자세히 설명 하는 해당 Microsoft 기술 자료 문서가 릴리스됩니다. 

핫픽스 다운로드 하 고 Azure 스택에 대 한 일반 전체 업데이트 패키지와 마찬가지로 설치 됩니다. 그러나 전체 업데이트와는 달리 핫픽스 분 후에 설치할 수 있습니다. Azure 스택 연산자 핫픽스를 설치 하는 경우 유지 관리 기간을 설정 하는 것이 좋습니다. 핫픽스는 핫픽스가 적용 된 경우를 쉽게 확인할 수 있도록 Azure 스택 클라우드의 버전을 업데이트 합니다. 지원 되는 Azure 스택의 각 버전에 대해 별도 핫픽스를 제공 합니다. 특정 반복에 대 한 각 수정 누적 되며 동일한 버전에 대 한 이전 업데이트가 포함 되어 있습니다. 자세한 내용은 기술 자료에 해당 하는 수정 사항에 특정 핫픽스의 적용 여부에 대 한 문서입니다.  


## <a name="keep-your-system-under-support"></a>지원에는 시스템 상태 유지
지원을 받으려면를 계속 하려면 유지 해야 Azure 스택 배포 현재. 업데이트에 대 한 지연 정책은: 지원을 유지 하려면 Azure 스택 배포에 가장 최근에 릴리스된 업데이트 버전을 실행 하거나 두 개의 이전 업데이트 버전 중 하나를 실행 해야 합니다. 핫픽스 주요 업데이트 이며 버전 간주 되지 않습니다. Azure 스택 클라우드 뒤에 있는 경우 여 *두 개 이상의 업데이트*를 준수 하지 않는 것으로 간주 하 고 업데이트 해야 이상 지원을 받으려면 지원 되는 최소 버전입니다. 

예를 들어 경우 가장 최근에 사용 가능한 업데이트 버전이 1805, 이전 두 업데이트 패키지 다 1804 및 1803 버전, 1803와 1804 유지 지원 합니다. 그러나 1802 지원 되지 않습니다. 정책 두 개 또는 한 달에 대 한 릴리스 없는 경우에 마찬가지입니다. 예를 들어 현재 릴리스에서 1805 없는 1804 릴리스 했습니다 경우 1803 및 1802 이전 두 업데이트 패키지 지원 유지 합니다.

Microsoft 소프트웨어 업데이트 패키지가 아닌 누적 되며이 필수 구성 요소로 이전 업데이트 패키지 해야 합니다. 하나 이상의 업데이트를 연기 하려는 경우를 최신 버전으로 가져올 경우 전체 런타임을 고려 합니다. 

## <a name="get-support"></a>지원 받기
Azure 스택 Azure와 같은 지원 프로세스를 따릅니다. 기업 고객에서 설명한 프로세스를 따를 수 [Azure 지원 요청을 만드는 방법을](/azure/azure-supportability/how-to-create-azure-support-request)합니다. 클라우드 서비스 공급자 (CSP)의 고객 인 경우 CSP을 지원에 문의 하십시오.  자세한 내용은 [Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요. 


## <a name="next-steps"></a>다음 단계

- [Azure 스택에서 업데이트를 관리](azure-stack-updates.md)


