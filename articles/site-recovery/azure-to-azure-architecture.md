---
title: Azure Site Recovery의 Azure 간 복제 아키텍처 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery 서비스를 사용하여 Azure 지역 간에 Azure VM을 복제하는 경우 사용되는 구성 요소와 아키텍처에 대해 간략히 설명합니다.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 05/15/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: f6ade346305ee6f18fb41b93bc8a1dc8543fcdb7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34209504"
---
# <a name="azure-to-azure-replication-architecture"></a>Azure 간 복제 아키텍처


이 문서에서는 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 Azure 지역 간에 Azure VM(가상 머신)을 복제, 장애 조치(Failover) 및 복구할 때 사용되는 아키텍처에 대해 설명합니다.

>[!NOTE]
>Site Recovery 서비스를 사용하는 Azure VM 복제는 현재 미리 보기로 제공됩니다.



## <a name="architectural-components"></a>아키텍처 구성 요소

다음 그림은 특정 지역(이 예제에서는 미국 동부 지역)의 Azure VM 환경을 개략적으로 보여 줍니다. Azure VM 환경은 다음과 같습니다.
- 앱은 저장소 계정에 분산된 관리 디스크 또는 관리되지 않는 디스크를 사용하여 VM에서 실행될 수 있습니다.
- VM은 가상 네트워크 내에서 하나 이상의 서브넷에 포함될 수 있습니다.


**Azure 간 복제**

![고객 환경](./media/concepts-azure-to-azure-architecture/source-environment.png)

## <a name="replication-process"></a>복제 프로세스

### <a name="step-1"></a>1단계

Azure VM 복제를 사용하도록 설정하면 다음 리소스가 원본 지역 설정에 따라 대상 지역에 자동으로 생성됩니다. 필요에 따라 대상 리소스 설정을 사용자 지정할 수 있습니다.

![복제를 사용하도록 설정하는 프로세스, 1단계](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

**리소스** | **세부 정보**
--- | ---
**대상 리소스 그룹** | 장애 조치(failover) 후 복제된 VM이 속하게 되는 리소스 그룹입니다. 원본 가상 머신이 호스트되는 Azure 지역을 제외한 모든 Azure 지역이 이 리소스 그룹의 위치가 될 수 있습니다.
**대상 가상 네트워크** | 장애 조치(failover) 후 복제된 VM이 있는 가상 네트워크입니다. 네트워크 매핑은 원본 및 대상 가상 네트워크 간에 만들어집니다.
**캐시 저장소 계정** | 원본 VM 변경 내용이 대상 저장소 계정에 복제되기 전에 추적되어 원본 위치의 캐시 저장소 계정으로 전송됩니다. 이 단계를 통해 VM에서 실행 중인 프로덕션 애플리케이션에 미치는 영향이 최소화됩니다.
**대상 저장소 계정(원본 VM이 관리 디스크를 사용하지 않는 경우)**  | 데이터가 복제되는 대상 위치의 Storage 계정입니다.
**복제본 관리 디스크(원본 VM이 관리 디스크에 있는 경우)**  | 데이터가 복제되는 대상 위치의 관리 디스크입니다.
**대상 가용성 집합**  | 장애 조치(failover) 후 복제된 VM이 있는 가용성 집합입니다.

### <a name="step-2"></a>2단계

복제를 사용하도록 설정하면 Site Recovery 확장 이동성 서비스가 VM에 자동으로 설치됩니다.

1. VM이 Site Recovery에 등록됩니다.

2. VM에 대해 연속 복제가 구성됩니다. VM 디스크에서의 데이터 쓰기가 원본 위치의 캐시 저장소 계정에 지속적으로 전송됩니다.

   ![복제를 사용하도록 설정하는 프로세스, 2단계](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)


 Site Recovery에는 VM에 대한 인바운드 연결이 필요하지 않습니다. 다음에 대해서는 아웃바운드 연결만 필요합니다.

 - Site Recovery 서비스 URL/IP 주소
 - Office 365 인증 URL/IP 주소
 - 캐시 저장소 계정 IP 주소

다중 VM 일관성을 사용하도록 설정하면 복제 그룹의 컴퓨터는 20004 포트를 통해 서로 통신하게 됩니다. 20004 포트를 통한 VM 간의 내부 통신을 차단하는 방화벽 어플라이언스가 없는지 확인합니다.

> [!IMPORTANT]
Linux VM을 복제 그룹에 포함하고 싶다면 특정 Linux 버전의 지침에 따라 20004 포트의 아웃바운드 트래픽을 수동으로 열어야 합니다.

### <a name="step-3"></a>3단계

연속 복제가 진행된 후에는 디스크 쓰기가 캐시 저장소 계정으로 즉시 전송됩니다. Site Recovery는 데이터를 처리하여 대상 저장소 계정 또는 복제본 관리 디스크로 보냅니다. 데이터가 처리된 후에는 몇 분마다 대상 저장소 계정에 복구 지점이 생성됩니다.

## <a name="failover-process"></a>장애 조치(failover) 프로세스

장애 조치(failover)를 시작하면 대상 리소스 그룹, 대상 가상 네트워크, 대상 서브넷 및 대상 가용성 집합에 VM이 생성됩니다. 장애 조치(failover) 중에는 모든 복구 지점을 사용할 수 있습니다.

![장애 조치(failover) 프로세스](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>다음 단계

Azure VM을 보조 지역에 [신속하게 복제](azure-to-azure-quickstart.md)합니다.
