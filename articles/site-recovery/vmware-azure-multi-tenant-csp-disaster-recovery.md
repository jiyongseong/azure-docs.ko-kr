---
title: Site Recovery와 CSP(클라우드 솔루션 공급자) 프로그램을 사용하여 다중 테넌트 환경에서 Azure에 대한 VMware 복제 설정 | Microsoft Docs
description: CSP를 통해 테넌트 구독을 만들고 관리하며 다중 테넌트 설정에서 Azure Site Recovery를 배포하는 방법을 설명합니다
services: site-recovery
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: 58ea2e7c387137f974425464ef2c9d17f438ba7c
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
ms.locfileid: "34071862"
---
# <a name="set-up-vmware-replication-in-a-multi-tenancy-environment-with-the-cloud-solution-provider-csp-program"></a>CSP(클라우드 솔루션 공급자) 프로그램을 사용하여 다중 테넌트 환경에서 VMware 복제 설정

[CSP 프로그램](https://partner.microsoft.com/en-US/cloud-solution-provider)은 Office 365, Enterprise Mobility Suite 및 Microsoft Azure 등 모든 Microsoft 클라우드 서비스에 대한 더 나은 협력 사례를 발전시키고 있습니다. CSP를 통해 파트너는 고객과 종단 간 관계를 보유하게 되고 기본 관계 접점이 될 수 있습니다. 파트너는 고객을 위해 Azure 구독을 배포하고 이러한 구독과 고객의 부가 가치 사용자 지정 옵션을 결합할 수 있습니다.

파트너로써 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 CSP를 통해 고객을 위한 재해 복구를 직접 관리할 수 있습니다. 또는 CSP를 사용하여 Site Recovery 환경을 설정할 수 있으며 고객이 셀프 서비스 방식으로 고유한 재해 복구 요구 사항을 관리하도록 할 수 있습니다. 두 시나리오 모두에서 파트너는 Site Recovery와 고객 간의 매개체입니다. 파트너가 고객 관계 서비스를 제공하고 Site Recovery 사용에 대해 고객에게 비용을 청구합니다.

이 아티클에서는 파트너로써 다중 테넌트 VMware 복제 시나리오에서 CSP를 통해 테넌트 구독을 만들고 관리하는 방법에 대해 설명합니다.

## <a name="prerequisites"></a>필수 조건

VMware 복제를 설정하려면 다음을 수행해야 합니다.

- Azure 구독, Azure Virtual Network 및 저장소 계정을 포함하는 Azure 리소스를 [준비](tutorial-prepare-azure.md)합니다.
- 온-프레미스 VMware 서버 및 VM을 [준비](vmware-azure-tutorial-prepare-on-premises.md)합니다.
- 각 테넌트에서 테넌트 VM 및 vCenter server와 통신할 수 있는 별도의 관리 서버를 만듭니다. 파트너로써만 이 관리 서버에 대한 액세스 권한을 가집니다. [다중 테넌트 환경](vmware-azure-multi-tenant-overview.md)에 대해 자세히 알아봅니다.

## <a name="create-a-tenant-account"></a>테넌트 계정 만들기

1. [Microsoft 파트너 센터](https://partnercenter.microsoft.com/)를 통해 CSP 계정에 로그인합니다.
2. **대시보드** 메뉴에서 **고객**을 선택합니다.
3. 열린 페이지에서 **고객 추가** 단추를 클릭합니다.
4. **새 고객** 페이지에서 테넌트에 대한 계정 세부 정보를 모두 입력합니다.

    ![[계정 정보] 페이지](./media/vmware-azure-multi-tenant-csp-disaster-recovery/customer-add-filled.png)

5. 그런 다음, **다음: 구독**을 클릭합니다.
6. 구독 선택 페이지에서 **Microsoft Azure** 확인란을 선택합니다. 지금 또는 나중에 다른 구독을 추가할 수 있습니다.
7. **검토** 페이지에서 테넌트 세부 정보를 확인한 후 **제출**을 클릭합니다.
8. 테넌트 계정을 만들면 확인 페이지가 나타나며 여기에 구독에 대한 기본 계정 및 암호 정보가 표시됩니다. 정보를 저장하고 나중에 필요하면 Azure Portal 로그인 페이지를 통해 암호를 변경합니다.

이 정보를 테넌트와 있는 그대로 공유하거나 필요할 경우 별도의 계정을 만들고 공유할 수 있습니다.

## <a name="access-the-tenant-account"></a>테넌트 계정 액세스

Microsoft 파트너 센터 대시보드를 통해 테넌트 구독에 액세스할 수 있습니다.

1. **고객** 페이지에서 테넌트 계정의 이름을 클릭합니다.
2. 테넌트 계정의 **구독** 페이지에서 기존 계정 구독을 모니터링하고 필요에 따라 구독을 더 추가할 수 있습니다.
3. 테넌트의 재해 복구 작업을 관리하려면 **모든 리소스(Azure Portal)** 를 선택합니다. 그러면 테넌트의 Azure 구독에 대한 액세스 권한이 부여됩니다.

    ![모든 리소스 링크](./media/vmware-azure-multi-tenant-csp-disaster-recovery/all-resources-select.png)  

4. Azure Portal의 맨 위 오른쪽에 있는 Azure Active Directory 링크를 클릭하여 액세스를 확인할 수 있습니다.

    ![Azure Active Directory 링크](./media/vmware-azure-multi-tenant-csp-disaster-recovery/aad-admin-display.png)

이제 Azure Portal에서 테넌트에 대한 모든 Site Recovery 작업을 수행하고 관리할 수 있습니다. 관리 재해 복구를 위해 CSP를 통해 테넌트 구독에 액세스하려면 이전에 설명된 과정을 따릅니다.

## <a name="assign-tenant-access-to-the-subscription"></a>구독에 테넌트 액세스 할당

1. 재해 복구 인프라가 설정되어 있는지 확인합니다. 파트너는 재해 복구가 관리형 또는 셀프 서비스 형태인지에 관계없이 CSP 포털을 통해 테넌트 구독에 액세스합니다. 자격 증명 모음을 설정하고 테넌트 구독에 인프라를 등록합니다.
2. [만든 계정](#create-a-tenant-account)을 사용하여 테넌트를 제공합니다.
3. 다음과 같이 CSP 포털을 통해 새 사용자를 테넌트 구독에 추가할 수도 있습니다.

    a) 테넌트의 CSP 구독 페이지로 이동하고 **사용자 및 라이선스** 옵션을 선택합니다.

      ![테넌트의 CSP 구독 페이지](./media/vmware-azure-multi-tenant-csp-disaster-recovery/users-and-licences.png)

      b) 이제 관련 세부 정보를 입력하거나 권한을 선택하거나 사용자 목록을 CSV 파일로 업로드하여 새 사용자를 만듭니다.
    c) 새 사용자를 만든 후에 Azure Portal로 다시 이동합니다. **구독** 페이지에서 관련 구독을 선택합니다.
    d) **Access Control(IAM)** 을 선택한 다음, **추가**를 클릭하여 관련 액세스 수준을 가진 사용자를 추가합니다. CSP 포털을 통해 만들어진 사용자는 액세스 수준을 클릭하면 열리는 페이지에 자동으로 표시됩니다.

      ![사용자 추가](./media/vmware-azure-multi-tenant-csp-disaster-recovery/add-user-subscription.png)

- 대부분의 관리 작업은 *참여자* 역할로 충분합니다. 이 액세스 수준을 가진 사용자는 액세스 수준 변경(*소유자* 수준 액세스가 필요)을 제외하고 구독에 관한 모든 작업을 수행할 수 있습니다.
- 또한 Site Recovery에는 필요에 따라 액세스 수준을 더 제한하는 데 사용할 수 있는 3개의 [미리 정의된 사용자 역할](site-recovery-role-based-linked-access-control.md)이 있습니다.

## <a name="multi-tenant-environments"></a>다중 테넌트 환경

세 가지 주요 다중 테넌트 모델이 있습니다.

* **HSP(공유 호스팅 서비스 공급자)**: 파트너는 물리적 인프라를 소유하고 공유 리소스(vCenter, 데이터 센터, 실제 저장소 등)를 사용하여 동일한 인프라에서 다중 테넌트 VM을 호스팅합니다. 파트너는 재해 복구 관리를 관리 서비스로 제공할 수 있으며 테넌트는 재해 복구를 셀프 서비스 솔루션으로 소유할 수 있습니다.

* **전용 호스팅 서비스 공급자**: 파트너는 물리적 인프라를 소유하지만 전용 리소스(여러 vCenter, 실제 데이터 저장소 등)를 사용하여 별도의 인프라에서 각 테넌트의 VM을 호스팅합니다. 파트너는 재해 복구 관리를 관리 서비스로 제공할 수 있으며 테넌트는 이를 셀프 서비스 솔루션으로 소유할 수 있습니다.

* **MSP(관리형 서비스 공급자)**: 고객은 VM을 호스팅하는 실제 인프라를 소유하고 파트너는 재해 복구를 사용하고 관리합니다.

이 아티클에 설명된 대로 테넌트 구독을 설정하여 고객을 신속하게 관련 다중 테넌트 모델에서 사용하도록 설정하기 시작할 수 있습니다. [여기](vmware-azure-multi-tenant-overview.md)에서 다양한 다중 테넌트 모델 및 온-프레미스 액세스를 사용하는 방법에 대해 자세히 알아볼 수 있습니다.

## <a name="next-steps"></a>다음 단계
- Azure Site Recovery 배포를 관리하는 [역할 기반 액세스 제어](site-recovery-role-based-linked-access-control.md)에 대해 자세히 알아봅니다.
- VMware에서 Azure로 [복제 아키텍처](vmware-azure-architecture.md)에 대해 자세히 알아봅니다.
- VMware VM을 Azure에 복제하는 [자습서를 검토합니다](vmware-azure-tutorial.md).
VMware VM을 Azure에 복제하는 방법은 [다중 테넌트 환경](vmware-azure-multi-tenant-overview.md)에 대해 알아봅니다.
