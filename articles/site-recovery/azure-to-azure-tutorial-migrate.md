---
title: Azure Site Recovery를 사용하여 Azure 지역 간에 Azure VM 마이그레이션 | Microsoft Docs
description: Azure Site Recovery를 사용하여 Azure 지역 간에 Azure IaaS VM을 마이그레이션합니다.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/16/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 0460d595bc70ec09d492221485749ece32ec07df
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34209711"
---
# <a name="migrate-azure-vms-to-another-region"></a>다른 지역으로 Azure VM 마이그레이션

BCDR(비즈니스 지속성 및 재해 복구)을 위해 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 온-프레미스 컴퓨터 및 Azure VM의 재해 복구를 관리하고 오케스트레이션하는 것은 물론, Site Recovery를 사용하여 Azure VM을 보조 지역으로 마이그레이션하는 작업을 관리할 수도 있습니다. Azure VM을 마이그레이션하려면 해당 VM에 복제를 사용하도록 설정하고 주 지역에서 선택한 보조 지역으로 장애 조치(failover)합니다.

이 자습서에서는 Azure VM을 다른 지역으로 마이그레이션하는 방법을 보여 줍니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Recovery Services 자격 증명 모음 만들기
> * VM에 대한 복제 사용
> * 장애 조치(failover)를 실행하여 VM 마이그레이션

이 자습서에서는 이미 Azure 구독이 있다고 가정합니다. Azure 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.

>[!NOTE]
>
> Azure VM에 대한 Site Recovery 복제는 현재 미리 보기로 제공됩니다.



## <a name="prerequisites"></a>필수 조건

- 마이그레이션하려는 Azure 지역에서 Azure VM이 있는지 확인합니다.
- [시나리오 아키텍처 및 구성 요소](azure-to-azure-architecture.md)를 이해해야 합니다.
- [제한 사항 및 요구 사항 지원](azure-to-azure-support-matrix.md)을 검토합니다.



## <a name="before-you-start"></a>시작하기 전에

복제를 설정하기 전에 다음 단계를 완료합니다.


### <a name="verify-target-resources"></a>대상 리소스 확인

1. Azure 구독에서 재해 복구에 사용되는 대상 지역에 VM을 만들 수 있도록 허용하는지 확인합니다. 필요한 할당량을 사용하려면 지원 팀에 문의하세요.

2. 구독에 원본 VM과 동일한 크기의 VM을 지원할 수 있을 만큼 충분한 리소스가 있는지 확인합니다. Site Recovery는 대상 VM에 대해 동일한 크기 또는 가장 가깝게 가능한 크기를 선택합니다.


### <a name="verify-account-permissions"></a>계정 권한 확인

Azure 체험 계정을 방금 만든 경우 자신이 구독에 대한 관리자가 됩니다. 구독 관리자가 아닌 경우에는 필요한 권한을 할당해 줄 것을 관리자에게 요청합니다. 새 VM에 복제를 사용하려면 다음 권한이 있어야 합니다.

1. Azure 리소스에서 VM을 만들 수 있는 권한. 기본 제공되는 ‘Virtual Machine 참가자’ 역할에는 다음을 포함하여 이러한 권한이 있습니다.
    - 선택한 리소스 그룹에서 VM을 만들 수 있는 권한
    - 선택한 가상 네트워크에서 VM을 만들 수 있는 권한
    - 선택한 저장소 계정에 대한 쓰기 권한

2. Azure Site Recovery 작업을 관리할 수 있는 권한도 있어야 합니다. ‘Site Recovery 참가자’ 역할에는 Recovery Services 자격 증명 모음에서 Site Recovery 작업을 관리하는 데 필요한 모든 사용 권한이 있습니다.


### <a name="verify-vm-outbound-access"></a>VM 아웃바운드 액세스 확인

1. 마이그레이션할 VM의 네트워크 연결을 제어하기 위해 인증 프록시를 사용하지 않도록 합니다. 
2. 이 자습서에서는 마이그레이션할 VM이 인터넷에 액세스할 수 있고 방화벽 프록시를 사용하여 아웃바운드 액세스를 제어하지 않는다고 가정합니다. 액세스를 제어하는 경우 [여기](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity)서 요구 사항을 확인합니다.

### <a name="verify-vm-certificates"></a>VM 인증서 확인

마이그레이션할 Azure VM에 모든 최신 루트 인증서가 있는지 확인합니다. 최신 루트 인증서가 없는 경우 보안 제약 조건으로 인해 Site Recovery에 VM을 등록할 수 없습니다.

- Windows VM의 경우 VM에 최신 Windows 업데이트를 모두 설치하여 신뢰할 수 있는 모든 루트 인증서가 컴퓨터에 있도록 합니다. 연결이 끊어진 환경에서 조직의 표준 Windows 업데이트 및 인증서 업데이트 프로세스를 따릅니다.
- Linux VM의 경우 Linux 배포자가 제공한 지침에 따라 신뢰할 수 있는 최신 루트 인증서 및 인증서 해지 목록을 VM에 가져옵니다.



## <a name="create-a-vault"></a>자격 증명 모음 만들기

원본 지역을 제외한 모든 지역에 자격 증명 모음을 만듭니다.

1. [Azure Portal](https://portal.azure.com) > **Recovery Services**에 로그인합니다.
2. **리소스 만들기** > **모니터링 및 관리** > **Backup 및 Site Recovery**를 클릭합니다.
3. **이름**에서 **ContosoVMVault**라는 이름을 지정합니다. 구독이 두 개 이상인 경우 적절한 구독을 선택합니다.
4. **ContosoRG** 리소스 그룹을 만듭니다.
5. Azure 지역을 지정합니다. 지원되는 지역을 확인하려면 [Azure Site Recovery 가격 정보](https://azure.microsoft.com/pricing/details/site-recovery/)에서 지리적 가용성을 참조하세요.
6. 대시보드에서 자격 증명 모음에 빠르게 액세스하려면 **대시보드에 고정**을 클릭하고 **만들기**를 클릭합니다.

   ![새 자격 증명 모음](./media/tutorial-migrate-azure-to-azure/azure-to-azure-vault.png)

**대시보드**의 **모든 리소스** 아래와 주 **Recovery Services 자격 증명 모음** 페이지에 새 자격 증명 모음이 추가됩니다.






## <a name="select-the-source"></a>원본 선택

1. Recovery Services 자격 증명 모음에서 **ConsotoVMVault** > **+복제**를 클릭합니다.
2. **원본**에서 **Azure**를 선택합니다.
3. **원본 위치**에서 VM이 현재 실행 중인 원본 Azure 지역을 선택합니다.
4. 리소스 관리자 배포 모델을 선택합니다. 그런 다음 **원본 리소스 그룹**을 선택합니다.
5. **확인**을 클릭하여 설정을 저장합니다.


## <a name="enable-replication-for-azure-vms"></a>Azure VM에 복제 사용

Site Recovery는 구독 및 리소스 그룹과 연관된 VM 목록을 검색합니다.


1. Azure Portal에서 **가상 머신**을 클릭합니다.
2. 마이그레이션할 VM을 선택합니다. 그런 후 **OK**를 클릭합니다.
3. **설정**에서 **재해 복구**를 클릭합니다.
4. **재해 복구 구성** > **대상 지역**에서 복제할 대상 지역을 선택합니다.
5. 이 자습서에서는 다른 기본 설정을 적용합니다.
6. **복제 활성화**를 클릭합니다. VM에 대해 복제를 활성화하는 작업이 시작됩니다.

    ![복제 활성화](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="run-a-failover"></a>장애 조치(Failover) 실행

1. **설정** > **복제된 항목**에서 컴퓨터를 클릭한 다음 **장애 조치(Failover)** 를 클릭합니다.
2. **장애 조치(failover)** 에서 **최신**을 선택합니다. 암호화 키 설정은 이 시나리오와 관련이 없습니다.
3. **장애 조치(failover)를 시작하기 전에 컴퓨터를 종료합니다.** 를 선택합니다. Site Recovery는 장애 조치(failover)를 트리거하기 전에 원본 VM을 종료하려고 합니다. 종료가 실패하더라도 장애 조치는 계속됩니다. **작업** 페이지에서 장애 조치 진행 상황 확인을 수행할 수 있습니다.
4. Azure VM이 예상대로 Azure에 표시되는지 확인합니다.
5. **복제된 항목**에서 VM을 마우스 오른쪽 단추로 클릭하고 **커밋**을 클릭합니다. 그러면 마이그레이션 프로세스가 완료됩니다.
6. 커밋이 완료되면 **복제 사용 안 함**을 클릭합니다.  그러면 VM에 대한 복제가 중지됩니다.



## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure VM을 다른 Azure 지역으로 마이그레이션했습니다. 이제 마이그레이션된 VM에 대해 재해 복구를 구성할 수 있습니다.

> [!div class="nextstepaction"]
> [마이그레이션 후 재해 복구 설정](azure-to-azure-quickstart.md)

