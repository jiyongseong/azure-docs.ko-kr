---
title: Azure Site Recovery를 사용하여 Azure에 온-프레미스 VMware VM 재해 복구 설정 | Microsoft Docs
description: Azure Site Recovery를 사용하여 Azure에 온-프레미스 VMware VM에 대한 재해 복구를 설정하는 방법을 알아봅니다.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/16/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 2a96655c26e2df2534f420239b56ef0c3959319a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212877"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Azure에 온-프레미스 VMware VM 재해 복구 설정

이 자습서에서는 Windows를 실행하는 온-프레미스 VMware VM에 대한 재해 복구를 Azure에 설정하는 방법을 보여 줍니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 복제 원본 및 대상을 입력합니다.
> * 온-프레미스 Azure Site Recovery 구성 요소 및 대상 복제 환경을 포함하여 원본 복제 환경을 설정합니다.
> * 복제 정책을 만듭니다.
> * VM의 복제를 사용하도록 설정합니다.

이 자습서는 시리즈의 세 번째입니다. 이 자습서에서는 이전 자습서의 작업을 완료했다고 가정합니다.

* [Azure 준비](tutorial-prepare-azure.md) 이 자습서에서는 Azure 저장소 계정 및 네트워크를 설정하고, Azure 계정에 적절한 권한이 있는지 확인하고, Recovery Services 자격 증명 모음을 만드는 방법을 설명합니다.
* [온-프레미스 VMware 준비](vmware-azure-tutorial-prepare-on-premises.md) 이 자습서에서는 Site Recovery에서 VM을 검색하고 필요에 따라 VM에 대한 복제를 사용하도록 설정할 때 Site Recovery Mobility 서비스 구성 요소의 강제 설치를 실행하기 위해 VMware 서버에 액세스할 수 있도록 계정을 준비합니다. 또한 VMware 서버 및 VM이 Site Recovery 요구 사항을 준수하는지 확인합니다.

시작하기 전에 재해 복구 시나리오에 대한 [아키텍처를 검토](vmware-azure-architecture.md)하는 것이 좋습니다.


## <a name="select-a-replication-goal"></a>복제 목표 선택

1. **Recovery Services 자격 증명 모음**에서 자격 증명 모음 이름을 선택합니다. 이 시나리오에서는 **ContosoVMVault**를 사용합니다.
2. **시작**에서 Site Recovery를 선택합니다. 그런 다음, **인프라 준비**를 선택합니다.
3. **보호 목표** > **컴퓨터가 있는 위치**에서 **온-프레미스**를 선택합니다.
4. **컴퓨터를 복제할 위치를 선택하세요.** 에서 **Azure**를 선택합니다.
5. **컴퓨터가 가상화된 경우**에서 **예, VMware vSphere 하이퍼바이저 사용**을 선택합니다. 그런 다음 **확인**을 선택합니다.

## <a name="set-up-the-source-environment"></a>원본 환경 설정


원본 환경을 설정하려면 온-프레미스 Site Recovery 구성 요소를 호스트할 단일 고가용성 온-프레미스 컴퓨터가 필요합니다. 구성 요소에는 구성 서버, 프로세스 서버 및 마스터 대상 서버가 포함됩니다.

- 구성 서버는 온-프레미스와 Azure 간의 통신을 조정하여 데이터 복제를 관리합니다.
- 프로세스 서버는 복제 게이트웨이의 역할을 합니다. 복제 데이터를 수신하고 캐싱, 압축 및 암호화를 사용하여 최적화하며 복제 데이터를 Azure 저장소로 전송합니다. 또한 프로세스 서버는 복제하려는 VM에 모바일 서비스를 설치하고 온-프레미스 VMware VM의 자동 검색을 수행합니다.
- 마스터 대상 서버는 Azure에서 장애 복구 중 복제 데이터를 처리합니다.

고가용성 VMware VM으로 구성 서버를 설정하려면 준비된 OVF(Open Virtualization Format) 템플릿을 다운로드하고 템플릿을 VMware로 가져와서 VM을 만듭니다. 구성 서버를 설정 한 후 자격 증명 모음에 등록합니다. 등록이 완료되면 Site Recovery가 온-프레미스 VMware VM을 검색합니다.

> [!TIP]
> 이 자습서는 OVF 템플릿을 사용하여 구성 서버 VMware VM을 만듭니다. 이를 수행할 수 없는 경우 [수동 설치](physical-manage-configuration-server.md)를 실행하여 이 작업을 수행할 수 있습니다. 


### <a name="download-the-vm-template"></a>VM 템플릿 다운로드

1. 자격 증명 모음에서 **인프라 준비** > **원본**으로 이동합니다.
2. **원본 준비**에서 **+구성 서버**를 선택합니다.
3. **서버 추가**에서 **VMware에 대한 구성 서버**가 **서버 형식**에 표시되는지 확인합니다.
4. 구성 서버에 대한 OVF 템플릿을 다운로드합니다.

  > [!TIP]
  [Microsoft 다운로드 센터](https://aka.ms/asrconfigurationserver)에서 최신 버전의 구성 서버 템플릿을 직접 다운로드할 수 있습니다.

## <a name="import-the-template-in-vmware"></a>VMware에서 템플릿 가져오기

1. VMWare vSphere 클라이언트를 사용하여 VMware vCenter 서버 또는 vSphere ESXi 호스트에 로그인합니다.
2. **파일** 메뉴에서 **OVF 템플릿 배포**를 선택하여 OVF 템플릿 배포 마법사를 시작합니다. 

     ![OVF 템플릿](./media/vmware-azure-tutorial/vcenter-wizard.png)

3. **원본 선택**에서 다운로드한 OVF의 위치를 입력합니다.
4. **리뷰 세부 정보**에서 **다음**을 선택합니다.
5. **이름 및 폴더 선택**과 **구성 선택**에서 기본 설정을 그대로 적용합니다.
6. 최상의 성능을 발휘할 수 있도록 **저장소 선택**의 **가상 디스크 형식 선택**에서 **씩 프로비전 Eager Zeroed**를 선택합니다.
7. 마법사 페이지의 나머지 부분에서는 기본 설정을 적용합니다.
8. **완료 준비**에서:

    * 기본 설정을 사용하여 VM을 설정하려면 **배포 후 전원 켜기** > **마침**을 선택합니다.

    * 추가 네트워크 인터페이스를 추가하려면 **배포 후 전원 켜기**의 선택을 취소합니다. 그런 다음, **마침**을 선택합니다. 기본적으로 구성 서버 템플릿은 단일 NIC를 사용하여 배포됩니다. 배포 후 NIC를 추가할 수 있습니다.

## <a name="add-an-additional-adapter"></a>어댑터 추가

구성 서버에 NIC를 추가하려면 자격 증명 모음에 서버를 등록하기 전에 추가하세요. 등록 후에는 어댑터를 추가할 수 없습니다.

1. VSphere 클라이언트 인벤토리에서 VM을 마우스 오른쪽 단추로 클릭하고 **설정 편집**을 선택합니다.
2. **하드웨어**에서 **추가** > **이더넷 어댑터**를 선택합니다. 그런 후 **다음**을 선택합니다.
3. 어댑터 유형 및 네트워크를 선택합니다. 
4. VM이 켜질 때 가상 NIC에 연결하려면 **전원이 켜지면 연결**을 선택합니다. **다음** > **마침**을 선택합니다. 그런 다음 **확인**을 선택합니다.


## <a name="register-the-configuration-server"></a>구성 서버 등록 

1. VMWare vSphere 클라이언트 콘솔에서 VM을 켭니다.
2. VM이 Windows Server 2016 설치 환경으로 부팅됩니다. 사용권 계약에 동의하고 관리자 암호를 입력합니다.
3. 설치가 완료되면 VM에 관리자 권한으로 로그인합니다.
4. 처음으로 로그인하면 Azure Site Recovery 구성 도구가 시작됩니다.
5. Site Recovery에 구성 서버를 등록하는 데 사용된 이름을 입력합니다. 그런 후 **다음**을 선택합니다.
6. VM이 Azure에 연결할 수 있는지 도구에서 확인합니다. 연결이 설정되면 **로그인**을 선택하여 Azure 구독에 로그인합니다. 구성 서버를 등록하려는 자격 증명 모음에 자격 증명이 액세스할 수 있어야 합니다.
7. 도구에서 몇 가지 구성 작업을 수행한 후 다시 부팅합니다.
8. 컴퓨터에 다시 로그인합니다. 구성 서버 관리 마법사가 자동으로 시작됩니다.

### <a name="configure-settings-and-add-the-vmware-server"></a>설정 구성 및 VMware 서버 추가

1. 구성 서버 관리 마법사에서 **연결 설정**을 선택한 다음, 프로세스 서버가 VM에서 복제 트래픽을 수신할 때 사용할 NIC를 선택합니다. 그런 다음 **저장**을 선택합니다. 구성된 후에는 이 설정을 변경할 수 없습니다.
2. **Recovery Services 자격 증명 모음 선택**에서 Azure 구독을 선택하고 관련 리소스 그룹 및 자격 증명 모음을 선택합니다.
3. **타사 소프트웨어 설치**에서 사용권 계약에 동의합니다. **다운로드 및 설치**를 선택하여 MySQL 서버를 설치합니다.
4. **VMware PowerCLI 설치**를 선택합니다. 이렇게 하기 전에 모든 브라우저 창을 닫아야 합니다. 그런 다음, **계속**을 선택합니다.
5. 계속하기 전에 **어플라이언스 구성 유효성 검사**에서 필수 구성 요소가 확인됩니다.
6. **vCenter Server/vSphere ESXi 서버 구성**에 복제하려는 VM이 있는 vCenter 서버 또는 vSphere 호스트의 FQDN 또는 IP 주소를 지정합니다. 서버가 수신 대기하는 포트를 입력합니다. 자격 증명 모음의 VMware 서버에 사용할 이름을 입력합니다.
7. 구성 서버가 VMware 서버에 연결하는 데 사용할 자격 증명을 입력합니다. Site Recovery는 이러한 자격 증명을 사용하여 복제에 사용 가능한 VMware VM을 자동으로 검색합니다. **추가**를 선택한 다음, **계속**을 선택합니다.
8. **가상 머신 자격 증명 구성**에서, 복제가 사용되면 컴퓨터에 모바일 서비스를 자동으로 설치하는 데 사용할 사용자 이름 및 암호를 입력합니다. Windows 컴퓨터의 경우 복제하려는 컴퓨터에 대한 로컬 관리자 권한이 필요합니다. Linux의 경우 루트 계정에 대한 세부 정보를 제공합니다.
9. **구성 완료**를 선택하여 등록을 완료합니다. 
10. 등록이 완료되면 Azure Portal에서 구성 서버 및 VMware 서버가 자격 증명 모음의 **원본** 페이지에 표시되는지 확인합니다. 그런 다음, **확인**을 선택하여 대상 설정을 구성합니다.


Site Recovery는 지정한 설정을 사용하여 VMware 서버에 연결하고 VM을 검색합니다.

> [!NOTE]
> 포털에 계정 이름이 표시되는 데 15분 이상 걸릴 수 있습니다. 즉시 업데이트하려면 **구성 서버** > ***서버 이름*** > **서버 새로 고침**을 선택합니다.

## <a name="set-up-the-target-environment"></a>대상 환경 설정

대상 리소스를 선택하고 확인합니다.

1. **인프라 준비** > **대상**을 선택합니다. 사용할 Azure 구독을 선택합니다.
2. 대상 배포 모델이 Azure Resource Manager 기반인지, 클래식인지 여부를 지정합니다.
3. Site Recovery가 호환되는 Azure 저장소 계정 및 네트워크가 하나 이상 있는지 확인합니다.

   ![대상 탭](./media/vmware-azure-tutorial/storage-network.png)

## <a name="create-a-replication-policy"></a>복제 정책 만들기

1. [Azure Portal](https://portal.azure.com)을 열고 **모든 리소스**를 선택합니다.
2. **ContosoVMVault**라는 Recovery Service 자격 증명 모음을 선택합니다.
3. 복제 정책을 만들려면 **Site Recovery 인프라** > **복제 정책** > **+복제 정책**을 선택합니다.
4. **복제 정책 만들기**에서 정책 이름을 입력합니다. 이 시나리오에는 **VMwareRepPolicy**를 사용합니다.
5. **RPO 임계값**에서 기본값인 60분을 사용합니다. 이 값은 복구 지점을 만드는 빈도를 지정합니다. 연속 복제가 이 제한을 초과하면 경고가 생성됩니다.
6. **복구 지점 보존**에서 각 복구 지점에 대한 보존 기간으로 기본값인 24시간을 사용합니다. 이 자습서에서는 72시간을 사용합니다. 복제된 VM은 하나의 시간대에서 임의의 시점으로 복구할 수 있습니다.
7. **앱 일치 스냅숏 빈도**에서 앱 일치 스냅숏을 만드는 빈도로 기본값인 60분을 사용합니다. **확인**을 선택하여 정책을 만듭니다.

   ![복제 정책 만들기](./media/vmware-azure-tutorial/replication-policy.png)

정책은 구성 서버와 자동으로 연결됩니다. 기본적으로 장애 복구(failback)에 대해 일치 정책이 자동으로 만들어집니다. 예를 들어 복제 정책이 **rep-policy**인 경우 장애 복구(failback) 정책은 **rep-policy-failback**이 됩니다. 이 정책은 Azure에서 장애 복구(failback)를 시작하기 전에는 사용되지 않습니다.

## <a name="enable-replication"></a>복제 사용

VM에 대한 복제를 사용하도록 설정되면 Site Recovery에서 모바일 서비스를 설치합니다. 변경 내용이 적용되어 포털에 표시되는 데 15분 이상 걸릴 수 있습니다.

다음과 같이 복제를 사용합니다.

1. **응용 프로그램 복제** > **원본**을 선택합니다.
2. **원본**에서 구성 서버를 선택합니다.
3. **컴퓨터 형식**에서 **Virtual Machines**를 선택합니다.
4. **vCenter/vSphere 하이퍼바이저**에서 vSphere 호스트를 관리하는 vCenter Server를 선택하거나 해당 호스트를 선택합니다.
5. 프로세스 서버(구성 서버)를 선택합니다. 그런 다음 **확인**을 선택합니다.
6. **대상**에서 장애 조치(Failover)된 VM을 만들려는 구독 및 리소스 그룹을 선택합니다. 장애 조치 VM에 대해 Azure(클래식 또는 Resource Manager)에서 사용할 배포 모델을 선택합니다.
7. 데이터 복제에 사용할 Azure 저장소 계정을 선택합니다.
8. 장애 조치(failover) 후 Azure VM이 생성될 때 연결될 Azure 네트워크 및 서브넷을 선택합니다.
9. 컴퓨터마다 Azure 네트워크를 선택하려면 **나중에 구성** 을 선택합니다. 네트워크가 없는 경우 **만들어야** 합니다.
10. **Virtual Machines** > **Virtual Machines 선택**에서 복제하려는 각 컴퓨터를 선택합니다. 복제를 활성화할 수 있는 컴퓨터만 선택할 수 있습니다. 그런 다음 **확인**을 선택합니다.
11. **속성** > **속성 구성**에서 프로세스 서버가 자동으로 컴퓨터에 모바일 서비스를 설치하는 데 사용할 계정을 선택합니다.
12. **복제 설정** > **복제 설정 구성**에서 올바른 복제 정책이 선택되어 있는지 확인합니다.
13. **복제 사용**을 선택합니다.

**설정** > **작업** > **Site Recovery 작업**에서 **보호 사용** 작업의 진행률을 추적할 수 있습니다. **보호 완료** 작업이 실행된 후에는 컴퓨터가 장애 조치(failover)를 수행할 준비가 되어 있습니다.

추가하는 VM을 모니터링하려면, **구성 서버** > **마지막 연락**에서 VM을 마지막으로 검색한 시간을 확인합니다. 예약된 검색을 기다리지 않고 VM을 추가하려면 구성 서버를 강조 표시하고(클릭하지 않음) **새로 고침**을 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [재해 복구 드릴 실행](site-recovery-test-failover-to-azure.md)
