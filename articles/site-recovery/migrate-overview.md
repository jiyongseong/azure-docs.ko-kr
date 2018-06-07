---
title: "Azure Site Recovery의 마이그레이션 정보 | Microsoft Docs"
description: "이 문서에서는 Azure Site Recovery 서비스를 사용하여 온-프레미스 VM 및 Azure VM을 마이그레이션하는 방법을 설명합니다."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: raynew
ms.openlocfilehash: edf6ffe1cd55884f1c18498213df290cb19bb246
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2018
---
# <a name="about-migration"></a>마이그레이션 정보

[Azure Site Recovery](site-recovery-overview.md) 서비스가 컴퓨터 마이그레이션을 지원하는 방법을 빠르게 살펴보려면 이 문서를 읽어보세요. 

Site Recovery를 사용하여 마이그레이션할 수 있는 항목은 다음과 같습니다.

- **온-프레미스에서 Azure로 마이그레이션**: 온 프레미스 Hyper-V VM, VMware VM 및 물리적 서버를 Azure로 마이그레이션합니다. 마이그레이션한 후에는 온-프레미스 컴퓨터에서 실행되는 워크로드가 Azure VM에서 실행됩니다. 
- **Azure 내에서 마이그레이션**: Azure 지역 간에 Azure VM을 마이그레이션합니다. 
- **AWS 마이그레이션**: AWS Windows 인스턴스를 Azure IaaS VM으로 마이그레이션합니다. 


## <a name="what-do-we-mean-by-migration"></a>마이그레이션 기준은 어떤 의미인가요?

온-프레미스 및 Azure VM의 재해 복구를 위해 Site Recovery를 사용하는 것 외에도, Site Recovery 서비스를 사용하여 마이그레이션을 수행할 수 있습니다. 차이점은 무엇입니까?

- 재해 복구를 위해서는 컴퓨터를 Azure에 정기적으로 복제합니다. 중단이 발생하면 컴퓨터를 기본 사이트에서 보조 Azure 사이트로 장애 조치(Failover)하고 여기에서 액세스합니다. 기본 사이트를 다시 사용할 수 있게 되면 Azure에서 장애 복구(Failback)합니다.
- 마이그레이션의 경우에는 온-프레미스 컴퓨터를 Azure로 복제하거나 Azure VM을 보조 지역으로 복제합니다. 그런 다음 기본 사이트의 VM을 보조 사이트로 장애 조치(Failover)하고 마이그레이션 프로세스를 완료합니다. 장애 복구는 필요하지 않습니다.  


## <a name="migration-scenarios"></a>마이그레이션 시나리오

**시나리오** | **세부 정보**
--- | ---
**온-프레미스에서 Azure로 마이그레이션** | 온-프레미스 VMware VM, Hyper-V VM 및 물리적 서버를 Azure로 마이그레이션할 수 있습니다. 이렇게 하려면 전체 재해 복구와 거의 동일한 단계를 완료합니다. 단순히 Azure에서 온-프레미스 사이트로 컴퓨터를 장애 복구(Failback)하는 것이 아닙니다.
**Azure 지역 간 마이그레이션** | Azure 지역 간에 Azure VM을 마이그레이션할 수 있습니다. 마이그레이션이 완료된 후에는 이제 마이그레이션한 보조 지역에서 Azure VM에 대한 재해 복구를 구성할 수 있습니다.
**Azure에 AWS 마이그레이션** | Azure VM에 AWS 인스턴스 마이그레이션할 수 있습니다. Site Recovery는 마이그레이션하기 위해 AWS 인스턴스를 물리적 서버와 동일한 방법으로 처리합니다. 

## <a name="next-steps"></a>다음 단계

- [Azure로 온-프레미스 컴퓨터 마이그레이션](migrate-tutorial-on-premises-azure.md)
- [Azure 지역 간 VM 마이그레이션](azure-to-azure-tutorial-migrate.md)
- [Azure에 AWS 마이그레이션](migrate-tutorial-aws-azure.md)
