---
title: "Azure 스택 개발 키트 (ASDK)에 대 한 소개 | Microsoft Docs"
description: "기능에 대해 설명 된 ASDK 및 Microsoft Azure 스택 평가 하기 위한 일반적인 사용 사례입니다."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 5a268a29c7a767084049bf56270aa8bc9d2ccc3f
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/17/2018
---
# <a name="what-is-the-azure-stack-development-kit"></a>Azure 스택 개발 키트는 무엇입니까?
[Microsoft Azure 스택 통합 시스템](.\.\azure-stack-poc.md) 4-12 노드에서 크기 및 하드웨어 파트너와 Microsoft 공동으로 지원 됩니다. 프로덕션 작업에 대 한 새로운 시나리오를 사용 하도록 설정 하려면 Azure 스택 통합 시스템을 사용 합니다. 을 사용 하는 Azure 스택 운영자에 게는 통합 된 시스템 인프라를 관리 하 고 서비스를 제공 하는 경우 참조 우리의 [연산자 설명서](https://docs.microsoft.com/azure/azure-stack)합니다.

Azure 스택 개발 키트 (ASDK)은 단일 노드 배포를 다운로드 하 고 사용할 수 있는 Azure 스택의 **무료로**합니다. 모든 ASDK 구성 요소가 충족 하거나 초과 해야 하는 단일 호스트 서버 컴퓨터에서 실행 되는 가상 컴퓨터에 설치 된 [최소 하드웨어 요구 사항을](asdk-deploy-considerations.md#hardware)합니다. Azure 스택 평가 하 고 도구에서 Azure와 일치 및 Api를 사용 하 여 최신 응용 프로그램을 개발할 수 있는 환경을 제공 하려는 ASDK는 *비-프로덕션* 환경입니다. 

> [!IMPORTANT]
> 사용 하거나 프로덕션 환경에서 지원 되는 ASDK 적합 하지 않습니다.

없기 때문에 단일 개발 키트 호스트 컴퓨터에 배포 된 모든 ASDK 구성 요소, 제한 된 물리적 리소스를 사용할 수 있습니다. ASDK 배포와 Azure 스택 인프라 Vm와 테 넌 트 Vm에 공존 같은 서버 컴퓨터입니다. 눈금 또는 성능 평가 위해이 구성은 적합 하지 않습니다.

ASDK는에 대 한 Azure에 일관 된 하이브리드 클라우드 환경을 제공할 수 있도록 설계 되었습니다.
- **관리자** (Azure 스택 연산자). ASDK를 평가 하 여 사용 가능한 Azure 스택을 서비스에 대 한 자세한 내용은 훌륭한 리소스입니다.
- **개발자**합니다. 하이브리드 또는 최신 응용 프로그램 온-프레미스 (개발/테스트 환경)를 개발 하는 ASDK은 사용할 수 있습니다. Azure 스택 프로덕션 배포와 함께 또는 그 이전에 개발 환경의 반복성을 제공 합니다. 

ASDK에 대 한 자세한 내용을 보려면이 짧은 비디오 보기:

> [!VIDEO https://www.youtube.com/embed/dbVWDrl00MM]


## <a name="asdk-and-multi-node-azure-stack-differences"></a>Azure 스택 차이점 ASDK 및 다중 노드
단일 노드 ASDK 배포 알고 있어야 하는 몇 가지 중요 한 방법에서 Azure 스택 다중 노드 배포와 다릅니다.

|설명|ASDK|Multi-node Azure Stack|
|-----|-----|-----|
|**규모**|모든 구성 요소는 단일 노드 서버 컴퓨터에 설치 됩니다.|4-12 노드의 크기가 범위 수 있습니다.|
|**복원력**|단일 노드 구성은 고가용성을 제공 하지 않습니다.|[고가용성](.\.\azure-stack-key-features.md#high-availability-for-azure-stack) 기능을 지원 합니다.|
|**네트워킹**|ASDK AzS BGPNAT01 라는 VM을 사용 하 여 모든 ASDK 네트워크 트래픽을 라우팅합니다. 요구 사항은 없습니다 스위치를 추가 합니다.|AzS BGPNAT01 VM 다중 노드 배포에 존재 하지 않습니다. 더 복잡 한 [네트워크 라우팅 인프라](.\.\azure-stack-network.md#network-infrastructure) 랙 위쪽 TOR (), 베이스 보드 관리 컨트롤러 (BMC) 및 테두리 (데이터 센터 네트워크) 스위치를 포함 하 여 필요 합니다.|
|**패치 및 업데이트 프로세스**|ASDK의 새 버전으로 이동 하려면 ASDK 개발 키트 호스트 컴퓨터에 다시 배포 해야 합니다.|[패치 및 업데이트](.\.\azure-stack-updates.md) 설치 된 Azure 스택 버전을 업데이트 하는 데 사용 하는 프로세스입니다.|
|**지원**|Azure 스택 MSDN 포럼입니다. Microsoft 고객 서비스 및 지원 (CSS) 지원은 *하지* 비-프로덕션 환경에 사용할 수 있습니다.|[Azure 스택 MSDN 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack) 전체 CSS를 지원 합니다.|
| | |

## <a name="learn-about-available-services"></a>사용 가능한 서비스에 대 한 자세한 내용은
Azure 스택 연산자로 서비스 있습니다 사용할 수 있도록 사용자에 게 확인 해야 합니다. Azure 스택 Azure 서비스의 하위 집합을 지원 하 고 지원 되는 서비스 목록이 시간이 지남에 따라 개선 될 것입니다.

### <a name="foundational-services"></a>기본 서비스
기본적으로 Azure 스택에 다음 "기본 서비스"는 ASDK를 배포 하는 경우:
- 컴퓨팅
- Storage
- 네트워킹
- Key Vault

이러한 기본 서비스와 인프라-as a Service (IaaS) 최소 구성으로 사용자에 게 제공할 수 있습니다.

### <a name="additional-services"></a>서비스
현재 다음과 같은 추가 플랫폼으로-서비스 (PaaS) 서비스가 지원 됩니다.
- App Service
- Azure 기능
- SQL 및 MySQL 데이터베이스

> [!NOTE]
> 이러한 서비스 있습니다 있습니다 사용할 수 있도록 사용자에 게 하려면 추가 구성이 필요 하 고는 ASDK를 설치할 때 기본적으로 사용할 수 없는 합니다.

## <a name="service-roadmap"></a>서비스 로드맵
Azure 스택 추가 Azure 서비스에 대 한 지원을 추가 하도록 계속 됩니다. 향후 예정 사항 Azure 스택와 다음에 대 한 자세한 내용은 [Azure 스택 로드맵](https://azure.microsoft.com/roadmap/?tag=azure-stack)합니다. 


## <a name="next-steps"></a>다음 단계
개발 키트 호스트 서버 컴퓨터를 준비 해야 Azure 스택 평가 시작 하려면 다음 [설치는 ASDK](asdk-deploy.md)합니다. 그 후 수에 로그인 할 관리자 및 사용자 포털에 Azure 스택을 사용 하 여 시작 합니다.