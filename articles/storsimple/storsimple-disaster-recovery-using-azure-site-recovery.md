---
title: "Azure Site Recovery로 StorSimple 파일 공유 DR 자동화 | Microsoft Docs"
description: "Microsoft Azure StorSimple 저장소에서 호스팅되는 파일 공유에 대한 재해 복구 솔루션을 만들기 위한 단계와 모범 사례에 대해 설명합니다."
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 23049a2c-055e-4d0e-b8f5-af2a87ecf53f
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2017
ms.author: vidarmsft
ms.openlocfilehash: e60cc83f49f9e0d0f878d7f49333f1be34ce54a6
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2017
---
# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>StorSimple에서 호스트되는 파일 공유에 Azure Site Recovery를 사용하는 자동화된 재해 복구 솔루션
## <a name="overview"></a>개요
Microsoft Azure StorSimple은 일반적으로 파일 공유와 연결된 구조화되지 않은 데이터의 복잡성을 해결하는 하이브리드 클라우드 저장소 솔루션입니다. StorSimple은 온-프레미스 솔루션의 확장으로 클라우드 저장소를 사용하여 자동으로 온-프레미스 솔루션 및 클라우드 저장소에서 데이터를 계층화합니다. 로컬 및 클라우드 스냅숏과 함께 통합 데이터 보호 기능은 제멋대로 늘어나는 저장소 인프라의 필요성을 없애줍니다.

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) 는 가상 컴퓨터의 복제, 장애 조치(failover) 및 복구를 오케스트레이션하여 DR(재해 복구) 기능을 제공하는 Azure 기반 서비스입니다. Azure Site Recovery는 가상 컴퓨터 및 응용 프로그램을 일관되게 복제하고 보호하며 사설/공용 클라우드 또는 호스트된 클라우드로 원활하게 장애 조치(failover)하기 위한 다양한 복제 기술을 지원합니다.

Azure Site Recovery, 가상 컴퓨터 복제 및 StorSimple 클라우드 스냅숏 기능을 사용하여 전체 파일 서버 환경을 보호할 수 있습니다. 중단이 발생할 경우 클릭 한 번으로 단 몇 분 만에 Azure에서 파일 공유를 온라인 상태로 만들 수 있습니다.

이 문서에서는 StorSimple 저장소에서 호스트되는 파일 공유를 위한 재해 복구 솔루션을 만들고 원클릭 복구 계획을 사용하여 계획된 장애 조치(failover), 계획되지 않은 장애 조치(failover) 및 테스트 장애 조치(failover)를 수행하는 방법을 자세히 설명합니다. 기본적으로 Azure Site Recovery 자격 증명 모음에서 복구 계획을 수정하여 재해 시나리오 중에 StorSimple 장애 조치(failover)를 사용하도록 설정하는 방법을 보여 줍니다. 또한 지원되는 구성 및 필수 조건을 설명합니다. 이 문서에서는 사용자가 Azure Site Recovery 및 StorSimple 아키텍처의 기본 사항을 잘 알고 있다고 가정합니다.

## <a name="supported-azure-site-recovery-deployment-options"></a>지원되는 Azure Site Recovery 배포 옵션
고객은 파일 서버를 Hyper-V 또는 VMware에서 실행되는 물리적 서버 또는 VM(가상 컴퓨터)으로 배포한 다음 StorSimple 저장소의 잘라낸 볼륨에서 파일 공유를 만들 수 있습니다. Azure Site Recovery는 보조 사이트 또는 Azure에 대한 실제 배포와 가상 배포를 모두 보호할 수 있습니다. 이 문서에서는 Hyper-V에서 호스트되는 파일 서버 VM에 대한 복구 사이트로 Azure를 사용하고 StorSimple 저장소의 파일 공유를 사용하는 DR 솔루션의 세부 정보를 다룹니다. 파일 서버 VM이 VMware VM 또는 물리적 컴퓨터에 있는 다른 시나리오도 유사하게 구현할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
StorSimple 저장소에서 호스트되는 파일 공유에 Azure Site Recovery를 사용하는 원클릭 재해 복구 솔루션을 구현할 경우 다음과 같은 필수 조건이 있습니다.

* Hyper-V 또는 VMware나 물리적 컴퓨터에서 호스트되는 온-프레미스 Windows Server 2012 R2 파일 서버 VM
* Azure StorSimple Manager에 등록된 온-프레미스 StorSimple 저장소 장치
* Azure StorSimple Manager에서 만들어진 StorSimple Cloud Appliance 어플라이언스를 종료 상태로 유지할 수 있습니다.
* StorSimple 저장소 장치에 구성된 볼륨에서 호스트되는 파일 공유
* [Azure Site Recovery 서비스 자격 증명 모음](../site-recovery/site-recovery-vmm-to-vmm.md) 

또한 Azure가 복구 사이트인 경우 VM에서 [Azure 가상 컴퓨터 준비 평가 도구](http://azure.microsoft.com/downloads/vm-readiness-assessment/) 를 실행하여 Azure VM 및 Azure Site Recovery 서비스와 호환되는지 확인합니다.

대기 시간 문제(많은 비용을 유발할 수 있음)를 방지하려면 StorSimple Cloud Appliance, 자동화 계정 및 저장소 계정을 같은 지역에서 만들도록 합니다.

## <a name="enable-dr-for-storsimple-file-shares"></a>StorSimple 파일 공유에 DR 사용
완전한 복제 및 복구를 위해서는 온-프레미스 환경의 각 구성 요소를 보호해야 합니다. 이 섹션에서는 다음 방법을 설명합니다.

* Active Directory 및 DNS 복제 설정(선택 사항)
* Azure Site Recovery를 사용하여 파일 서버 VM의 보호 사용
* StorSimple 볼륨의 보호 사용
* 네트워크 구성

### <a name="set-up-active-directory-and-dns-replication-optional"></a>Active Directory 및 DNS 복제 설정(선택 사항)
DR 사이트에서 사용할 수 있도록 Active Directory 및 DNS를 실행하는 컴퓨터를 보호하려는 경우 인증을 통한 장애 조치(failover) 후 파일 서버에 액세스할 수 있도록 이러한 컴퓨터를 명시적으로 보호해야 합니다. 고객의 온-프레미스 환경 복잡도에 따라 권장되는 두 가지 옵션이 있습니다.

#### <a name="option-1"></a>옵션 1
고객이 적은 수의 응용 프로그램과 전체 온-프레미스 사이트에 대한 단일 도메인 컨트롤러를 보유하고 있고 전체 사이트를 장애 조치(failover)하려는 경우 Azure Site Recovery 복제를 사용하여 도메인 컨트롤러 컴퓨터를 보조 사이트로 복제하는 것이 좋습니다(사이트 간 및 사이트-Azure에 적용 가능).

#### <a name="option-2"></a>옵션 2
고객이 많은 수의 응용 프로그램을 보유하고 있고 Active Directory 포리스트를 실행 중이며 한 번에 소수의 응용 프로그램을 장애 조치(failover)하려는 경우 DR 사이트(보조 사이트 또는 Azure)에 추가 도메인 컨트롤러를 설정하는 것이 좋습니다.

DR 사이트에서 도메인 컨트롤러를 사용할 수 있도록 설정하는 경우의 지침은 [Azure Site Recovery를 사용한 Active Directory 및 DNS용 자동화된 DR 솔루션](../site-recovery/site-recovery-active-directory.md) 을 참조하세요. 이 문서의 나머지 부분에서는 DR 사이트에서 도메인 컨트롤러를 사용할 수 있다고 가정합니다.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Azure Site Recovery를 사용하여 파일 서버 VM의 보호 사용
이 단계를 수행하려면 온-프레미스 파일 서버 환경을 준비하고, Azure Site Recovery 자격 증명 모음을 만들고 준비하며, VM의 파일 보호를 사용하도록 설정해야 합니다.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>온-프레미스 파일 서버 환경을 준비하려면
1. **사용자 계정 컨트롤**을 **알리지 않음**으로 설정합니다. Azure Site Recovery로 장애 조치(failover) 후 Azure 자동화 스크립트를 사용하여 iSCSI 대상을 연결할 수 있도록 이 작업이 필요합니다.

   1. Windows 키+Q를 누르고 **UAC**를 검색합니다.
   2. **사용자 계정 컨트롤 설정 변경**을 선택합니다.
   3. 막대를 **알리지 않음**을 향해 아래쪽으로 끕니다.
   4. **확인**을 클릭한 다음 메시지가 표시되면 **예**를 선택합니다.

      ![사용자 계정 컨트롤 설정 ](./media/storsimple-disaster-recovery-using-azure-site-recovery/image1.png)
2. 각 파일 서버 VM에 VM 에이전트를 설치합니다. 장애 조치(failover)된 VM에 대해 Azure 자동화 스크립트를 실행할 수 있도록 이 작업이 필요합니다.

   1. `C:\\Users\\<username>\\Downloads`에 [에이전트를 다운로드](http://aka.ms/vmagentwin)합니다.
   2. 관리자 모드(관리자 권한으로 실행)에서 Windows PowerShell을 열고 다음 명령을 입력하여 다운로드 위치로 이동합니다.

      `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`

      > [!NOTE]
      > 파일 이름은 버전에 따라 변경될 수 있습니다.
      >
      >
3. **다음**을 누릅니다.
4. **Terms of Agreement(계약 조건)**에 동의하고 **다음**을 클릭합니다.
5. **Finish**를 클릭합니다.
6. StorSimple 저장소의 잘라낸 볼륨을 사용하여 파일 공유를 만듭니다. 자세한 내용은 [StorSimple Manager 서비스를 사용하여 볼륨 관리](storsimple-manage-volumes.md)를 참조하세요.

   1. 온-프레미스 VM에서 Windows 키+Q를 누르고 **iSCSI**를 검색합니다.
   2. **iSCSI 초기자**를 선택합니다.
   3. **구성** 탭을 선택하고 초기자 이름을 복사합니다.
   4. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
   5. **StorSimple** 탭을 선택한 다음 물리적 장치를 포함하는 StorSimple Manager 서비스를 선택합니다.
   6. 볼륨 컨테이너를 만든 다음 볼륨을 만듭니다. (이러한 볼륨은 파일 서버 VM의 파일 공유용입니다.) 초기자 이름을 복사하고 볼륨을 만들 때 액세스 제어 레코드에 적절한 이름을 지정합니다.
   7. **구성** 탭을 선택하고 장치의 IP 주소를 적어둡니다.
   8. 온-프레미스 VM에서 다시 **iSCSI 초기자** 로 이동하여 빠른 연결 섹션에 IP를 입력합니다. **빠른 연결** 을 클릭합니다(이제 장치가 연결됨).
   9. Azure 포털을 열고 **볼륨 및 장치** 탭을 선택합니다. **자동 구성**을 클릭합니다. 만든 볼륨이 표시됩니다.
   10. 포털에서 **장치** 탭을 선택한 다음 **Create a New Virtual Device(새 가상 장치 만들기)**를 선택합니다. 를 선택합니다. (이 가상 장치는 장애 조치(failover)가 발생하는 경우 사용됩니다.) 이 새 가상 장치는 추가 비용을 방지하기 위해 오프라인 상태로 유지될 수 있습니다. 가상 장치를 오프라인 상태로 전환하려면 포털에서 **가상 컴퓨터** 섹션으로 이동하여 해당 가상 장치를 종료합니다.
   11. 온-프레미스 VM으로 돌아가 디스크 관리를 엽니다(Windows 키+X를 누르고 **디스크 관리**를 선택).
   12. 만든 볼륨 수에 따라 몇 가지 추가 디스크가 표시될 수 있습니다. 첫 번째 디스크를 마우스 오른쪽 단추로 클릭하고 **디스크 초기화**를 선택한 다음 **확인**을 선택합니다. **할당되지 않음** 섹션에서 마우스 오른쪽 단추를 클릭하고 **새 단순 볼륨**을 선택한 다음 드라이브 문자를 할당하고 마법사를 마칩니다.
   13. 모든 디스크에 대해 l단계를 반복합니다. 이제 Windows 탐색기에서 **내 PC** 의 모든 디스크를 볼 수 있습니다.
   14. 파일 및 저장소 서비스 역할을 사용하여 이러한 볼륨에서 파일 공유를 만듭니다.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>Azure Site Recovery 자격 증명 모음을 만들고 준비하려면
파일 서버 VM을 보호하기 전에 [Azure Site Recovery 설명서](../site-recovery/site-recovery-hyper-v-site-to-azure.md) 를 참조하여 Azure Site Recovery를 시작합니다.

#### <a name="to-enable-protection"></a>보호를 사용하도록 설정하려면
1. Azure Site Recovery를 통해 보호하려는 온-프레미스 VM에서 iSCSI 대상의 연결을 끊습니다.

   1. Windows 키+Q를 누르고 **iSCSI**를 검색합니다.
   2. **iSCSI 초기자 설정**을 선택합니다.
   3. 이전에 연결한 StorSimple 장치의 연결을 끊습니다. 또는 보호를 사용하도록 설정할 때 몇 분간 파일 서버를 끌 수 있습니다.

   > [!NOTE]
   > 이렇게 하면 파일 공유를 일시적으로 사용할 수 없게 됩니다.
   >
   >
2. Azure Site Recovery 포털에서 파일 서버 VM의 [가상 컴퓨터 보호를 사용](../site-recovery/site-recovery-hyper-v-site-to-azure.md)하도록 설정합니다.
3. 초기 동기화가 시작되면 대상을 다시 연결할 수 있습니다. iSCSI 초기자로 이동하여 StorSimple 장치를 선택한 다음 **연결**을 클릭합니다.
4. 동기화가 완료되고 VM 상태가 **보호됨**이면 VM을 선택하고 **구성** 탭을 선택한 다음 VM 네트워크(장애 조치(failover)된 VM이 속할 네트워크)를 적절하게 업데이트합니다. 네트워크가 표시되지 않으면 동기화가 아직 진행되고 있는 것입니다.

### <a name="enable-protection-of-storsimple-volumes"></a>StorSimple 볼륨의 보호 사용
StorSimple 볼륨에 대해 **이 볼륨에 대한 기본 백업 사용** 옵션을 선택하지 않은 경우 StorSimple Manager 서비스의 **백업 정책**으로 이동하여 모든 볼륨에 적합한 백업 정책을 만듭니다. 응용 프로그램에 대해 확인할 RPO(복구 지점 목표)의 백업 빈도를 설정하는 것이 좋습니다.

### <a name="configure-the-network"></a>네트워크 구성
파일 서버 VM의 경우 VM 네트워크가 장애 조치(failover) 후 올바른 DR 네트워크에 연결되도록 Azure Site Recovery에서 네트워크 설정을 구성합니다.

다음 그림과 같이 **복제 항목** VM을 선택하여 네트워크 설정을 구성할 수 있습니다.

![계산 및 네트워크](./media/storsimple-disaster-recovery-using-azure-site-recovery/image2.png)

## <a name="create-a-recovery-plan"></a>복구 계획 만들기
ASR에서 복구 계획을 만들어 파일 공유의 장애 조치(failover) 프로세스를 자동화할 수 있습니다. 중단이 발생할 경우 클릭 한 번으로 몇 분 만에 파일 공유를 온라인 상태로 만들 수 있습니다. 이 자동화를 사용하도록 설정하려면 Azure 자동화 계정이 필요합니다.

#### <a name="to-create-an-automation-account"></a>Automation 계정을 만들려면
1. Azure 포털의 &gt; **자동화** 섹션으로 이동합니다.
2. **+ 추가** 단추를 클릭하면 아래에 블레이드가 열립니다.

   ![Automation 계정 추가](./media/storsimple-disaster-recovery-using-azure-site-recovery/image11.png)

   * 이름 - 새 Automation 계정을 입력합니다.
   * 구독 - 구독을 선택합니다.
   * 리소스 그룹 - 새 리소스 그룹을 만들거나 기존 리소스 그룹을 선택합니다.
   * 위치 - 위치를 선택하고 이 위치를 StorSimple Cloud Appliance 및 저장소 계정을 만든 것과 같은 지역/하위 지역에 유지합니다.
   * Azure 실행 계정 만들기 - **예** 옵션을 선택합니다.

3. 자동화 계정에서 **Runbooks** &gt; **갤러리 찾아보기**를 클릭하여 필요한 모든 Runbook을 자동화 계정으로 가져옵니다.
4. 갤러리의 **재해 복구** 태그를 찾아서 다음 Runbook을 추가합니다.

   * TFO(테스트 장애 조치(failover)) 후 StorSimple 볼륨 정리
   * StorSimple 볼륨 컨테이너 장애 조치(failover)
   * 장애 조치(failover) 후 StorSimple 장치에서 볼륨 탑재
   * Azure VM에서 사용자 지정 스크립트 확장 제거
   * StorSimple 가상 어플라이언스 시작

     ![갤러리 찾아보기](./media/storsimple-disaster-recovery-using-azure-site-recovery/image3.png)

5. 자동화 계정에서 Runbook을 선택하여 모든 스크립트를 게시하고 **편집** &gt; **게시**를 차례로 클릭한 다음 확인 메시지에 **예**를 클릭합니다. 이 단계 후에 **Runbook** 탭은 다음과 같이 표시됩니다.

    ![Runbook](./media/storsimple-disaster-recovery-using-azure-site-recovery/image4.png)

6. 자동화 계정에서 **변수** &gt; **변수 추가**를 클릭하고 다음 변수를 추가합니다. 이러한 자산을 암호화하도록 선택할 수 있습니다. 이러한 변수는 복구 계획과 관련됩니다. 복구 계획(다음 단계에서 만듦) 이름이 TestPlan이면 변수는 TestPlan-StorSimRegKey, TestPlan-AzureSubscriptionName 등과 같아야 합니다.

   * **BaseUrl**: Azure Cloud에 대한 Resource Manager URL. **Get-AzureRmEnvironment | Select-Object Name, ResourceManagerUrl** cmdlet를 사용하여 가져옵니다.
   * *RecoveryPlanName***-ResourceGroupName**: StorSimple 리소스가 포함된 Resource Manager 그룹
   * *RecoveryPlanName***-ManagerName**: StorSimple 장치가 있는 StorSimple 리소스
   * *RecoveryPlanName***-DeviceName**: 장애 조치(failover)되어야 하는 StorSimple 장치
   * *RecoveryPlanName***-DeviceIpAddress**: 장치의 IP 주소(StorSimple Device Manager 섹션 &gt; **설정** &gt; **네트워크** &gt; **DNS 설정** 그룹의 **장치** 탭에서 확인할 수 있음) 
   * *RecoveryPlanName***-VolumeContainers**: 장애 조치(failover)해야 할 장치에 있는 볼륨 컨테이너의 쉼표로 구분된 문자열입니다(예: volcon1,volcon2, volcon3).
   * *RecoveryPlanName***-TargetDeviceName**: 컨테이너를 장애 조치(failover)할 StorSimple Cloud Appliance입니다.
   * *RecoveryPlanName***-TargetDeviceIpAddress**: 대상 장치의 IP 주소(**가상 컴퓨터** 섹션 &gt; **설정** 그룹 &gt; **네트워크** 탭에서 확인할 수 있음)
   * *RecoveryPlanName***-StorageAccountName**: 스크립트(장애 조치(failover)된 VM에서 실행해야 함)를 저장할 저장소 계정 이름입니다. 이 계정 이름은 스크립트를 임시로 저장할 공간이 있는 모든 저장소 계정일 수 있습니다.
   * *RecoveryPlanName***-StorageAccountKey**: 위 저장소 계정의 액세스 키입니다.
   * *RecoveryPlanName***-VMGUIDS**: VM을 보호할 때 Azure Site Recovery에서는 모든 VM에 장애 조치(failover)된 VM의 세부 정보를 제공하는 고유 ID를 할당합니다. VMGUID를 가져오려면 **Recovery Services** 탭을 선택하고 **보호된 항목** &gt; **보호 그룹** &gt; **컴퓨터** &gt; **속성**을 클릭합니다. 여러 VM이 있는 경우 GUID를 쉼표로 구분된 문자열로 추가합니다.

    예를 들어 복구 계획 이름이 fileServerpredayRP인 경우 모든 자산을 추가한 후에 **변수**, **연결** 및 **인증서** 탭이 다음과 같이 표시됩니다.

    ![자산](./media/storsimple-disaster-recovery-using-azure-site-recovery/image5.png)

7. Automation 계정에 StorSimple 8000 시리즈 Runbook 모듈을 업로드합니다. 다음 단계를 사용하여 모듈을 추가합니다.

   a. Powershell을 열고 새 폴더를 만들고 디렉터리를 해당 새 폴더로 변경합니다.
    
    ```
         mkdir C:\scripts\StorSimpleSDKTools
         cd C:\scripts\StorSimpleSDKTools
    ```
   b. 1단계와 같은 폴더에 nuget CLI를 다운로드합니다.
      nuget.exe의 여러 버전은 [nuget 다운로드](https://www.nuget.org/downloads)에서 제공됩니다. 각 다운로드 링크는 .exe 파일에 직접 연결되므로 브라우저에서 실행하지 말고, 마우스 오른쪽 단추를 클릭하여 컴퓨터에 파일을 저장해야 합니다.

    ```
         wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

   c. 종속 SDK 다운로드

    ```
         C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
         C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
         C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

   d. StorSimple 8000 Series 장치 관리를 위한 Azure Automation Runbook 모듈을 만듭니다. 아래 명령을 사용하여 Automation 모듈 zip 파일을 만듭니다.

    ```
         # set path variables
         $downloadDir = "C:\scripts\StorSimpleSDKTools"
         $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

         #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
         mkdir "$moduleDir"

         copy "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
         copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
         copy "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
         copy "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
         copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
         copy "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

         #Don't change the name of the Archive
         compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
    ```

     e. 앞의 단계에서 만든 Azure Automation 모듈 zip 파일(Microsoft.Azure.Management.StorSimple8000Series.zip)을 가져옵니다. 이 작업은 Automation 계정을 선택하고 SHARED RESOURCES에서 **모듈**을 클릭한 다음 **모듈 추가**를 클릭하여 수행할 수 있습니다.

    StorSimple 8000 시리즈 모듈을 가져온 후의 **모듈** 탭은 다음과 같아야 합니다.

    ![모듈](./media/storsimple-disaster-recovery-using-azure-site-recovery/image12.png)

8. **복구 서비스** 섹션으로 이동하여 앞서 만든 Azure Site Recovery 자격 증명 모음을 선택합니다.
9. **관리** 그룹에서 **복구 계획 (사이트 복구)** 옵션을 선택하여 다음과 같이 새로운 복구 계획을 만듭니다.

   a.  **+ 복구 계획** 단추를 클릭하여 아래의 블레이드를 엽니다.

      ![복구 계획 만들기](./media/storsimple-disaster-recovery-using-azure-site-recovery/image6.png)

   b.  복구 계획 이름을 입력하고 원본, 대상 및 배포 모델 값을 선택합니다.

   c.  보호 그룹에서 복구 계획에 포함할 VM을 선택하고 **확인** 단추를 클릭합니다.

   d.  이전에 만든 복구 계획을 선택하고 **사용자 지정** 단추를 클릭하여 복구 계획 사용자 지정 보기를 엽니다.

   e.  마우스 오른쪽 단추로 **모든 그룹 종료**를 클릭하고 **사전 작업 추가**를 클릭합니다.

   f.  삽입 작업 블레이드를 열고 이름을 입력한 후 옵션을 실행할 곳에서 **기본 측** 옵션을 선택하고 자동화 계정(Runbook을 추가한)을 선택한 다음 **Failover-StorSimple-Volume-Containers** Runbook을 선택합니다.

   g.  마우스 오른쪽 단추로 **그룹 1: 시작**을 클릭하고 **보호된 항목 추가** 옵션을 클릭한 다음 복구 계획에서 보호할 VM을 선택하고 **확인** 단추를 클릭합니다. 이미 선택된 VM인 경우 선택 사항입니다.

   h.  마우스 오른쪽 단추로 **그룹 1: 시작**을 클릭하고 **사후 작업** 옵션을 클릭한 후 다음의 모든 스크립트를 추가합니다.

   * Start-StorSimple-Virtual-Appliance Runbook
   * Fail over-StorSimple-volume-containers Runbook
   * Mount-volumes-after-failover Runbook
   * Uninstall-custom-script-extension Runbook

   i.  같은 **그룹 1: 사후 단계** 섹션에서 위의 4개 스크립트 뒤에 수동 작업을 추가합니다. 이 작업은 모든 항목이 제대로 작동하는지 확인할 수 있는 지점입니다. 이 작업은 테스트 장애 조치(failover)의 일부로만 추가해야 합니다(따라서 **테스트 장애 조치(failover)** 확인란만 선택).

   j.  수동 작업 뒤에 다른 Runbook에 사용한 것과 동일한 절차를 사용하여 **정리** 스크립트를 추가합니다. 복구 계획을 **저장**합니다.

    > [!NOTE]
    > 테스트 장애 조치(failover)를 실행할 때, 대상 장치에 복제된 StorSimple 볼륨이 수동 작업이 완료된 후 정리의 일부로 삭제되므로 수동 작업 단계에서 모든 항목을 확인해야 합니다.
    >

    ![복구 계획](./media/storsimple-disaster-recovery-using-azure-site-recovery/image7.png)

## <a name="perform-a-test-failover"></a>테스트 장애 조치(failover) 수행
테스트 장애 조치(failover) 중 Active Directory 관련 고려 사항은 [Active Directory DR 솔루션](../site-recovery/site-recovery-active-directory.md) 도우미 가이드를 참조하세요. 테스트 장애 조치(failover) 발생 시 온-프레미스 설치는 전혀 방해받지 않습니다. 온-프레미스 VM에 연결된 StorSimple 볼륨은 Azure에서 StorSimple Cloud Appliance에 복제됩니다. 테스트 목적의 VM은 Azure에서 온라인 상태가 되며 복제된 볼륨은 VM에 연결됩니다.

#### <a name="to-perform-the-test-failover"></a>테스트 장애 조치(failover)를 수행하려면
1. Azure 포털에서 Site Recovery 자격 증명 모음을 선택합니다.
2. 파일 서버 VM에 대해 만든 복구 계획을 클릭합니다.
3. **테스트 장애 조치(failover)**를 클릭합니다.
4. 장애 조치(Failover)가 발생한 후에 Azure VM이 연결될 Azure 가상 네트워크를 선택합니다.

   ![장애 조치(Failover) 시작](./media/storsimple-disaster-recovery-using-azure-site-recovery/image8.png)
5. **확인**을 클릭하여 장애 조치(failover)를 시작합니다. VM을 클릭하여 속성을 열거나 자격 증명 모음 이름 &gt; **작업** &gt; **Site Recovery 작업**에서 **테스트 장애 조치(failover)**에서 진행률을 추적할 수 있습니다.
6. 또한 장애 조치(failover)가 완료된 후 Azure 포털 &gt; **Virtual Machines**에 Azure 컴퓨터 복제본이 나타나는 것을 확인할 수 있습니다. 유효성 검사를 수행할 수 있습니다.
7. 유효성 검사가 완료된 후 **유효성 검사 완료**를 클릭합니다. 이렇게 하여 StorSimple 볼륨을 정리하고 StorSimple Cloud Appliance를 제거합니다.
8. 작업이 완료되면 복구 계획에서 **테스트 장애 조치 정리**를 클릭합니다. 참고에서 테스트 장애 조치와 연관된 모든 관측 내용을 기록하고 저장합니다. 그러면 테스트 장애 조치 중에 생성된 가상 컴퓨터가 삭제됩니다.

## <a name="perform-a-planned-failover"></a>계획된 장애 조치(failover) 수행
   계획된 장애 조치(failover) 중에 온-프레미스 파일 서버 VM은 정상적으로 종료되고 StorSimple 장치에서 볼륨의 클라우드 백업 스냅숏이 만들어집니다. StorSimple 볼륨이 가상 장치에 장애 조치(failover)되고, 복제본 VM이 Azure에서 온라인 상태가 되며, 볼륨이 VM에 연결됩니다.

#### <a name="to-perform-a-planned-failover"></a>계획된 장애 조치(failover)를 수행하려면
1. Azure 포털에서 파일 서버 VM에 대해 만들어진 **복구 서비스** 자격 증명 모음 &gt; **복구 계획(사이트 복구)** &gt; **recoveryplan_name**을 선택합니다.
2. 복구 계획 블레이드에서 **추가 정보** &gt;  **계획된 장애 조치**를 클릭합니다.

   ![복구 계획](./media/storsimple-disaster-recovery-using-azure-site-recovery/image9.png)
3. **계획된 장애 조치 확인** 블레이드에서 소스 및 대상 위치를 선택하고 대상 네트워크를 선택한 다음 ✓ 아이콘을 선택하여 장애 조치 프로세스를 시작합니다.
4. 복제본 가상 컴퓨터가 만들어진 후에는 커밋 대기중 상태입니다. **커밋** 을 클릭하여 장애 조치를 커밋합니다.
5. 복제가 완료되면 보조 위치에서 가상 컴퓨터가 시작됩니다.

## <a name="perform-a-failover"></a>장애 조치 수행
계획되지 않은 장애 조치(failover) 중에 StorSimple 볼륨이 가상 장치에 장애 조치(failover)되고, 복제본 VM이 Azure에서 온라인 상태가 되며, 볼륨이 VM에 연결됩니다.

#### <a name="to-perform-a-failover"></a>장애 조치를 수행하려면
1. Azure 포털에서 파일 서버 VM에 대해 만들어진 **복구 서비스** 자격 증명 모음 &gt; **복구 계획(사이트 복구)** &gt; **recoveryplan_name**을 선택합니다.
2. 복구 계획 블레이드에서 **추가 정보** &gt;  **장애 조치**를 클릭합니다.
3. **장애 조치 확인** 블레이드에서 원본 및 대상 위치를 선택합니다.
4. **가상 컴퓨터 종료 및 최신 데이터 동기화**를 선택하여 사이트 복구가 보호된 가상 컴퓨터를 종료하고 데이터를 동기화하여 최신 버전의 데이터가 장애 조치되도록 지정해야 합니다.
5. 가상 컴퓨터는 장애 조치 후에 커밋 보류 중 상태입니다. **커밋** 을 클릭하여 장애 조치를 커밋합니다.


## <a name="perform-a-failback"></a>장애 복구(failback) 수행
장애 복구(failback) 중에 StorSimple 볼륨 컨테이너는 백업이 만들어진 후 물리적 장치에 장애 복구(failback)됩니다.

#### <a name="to-perform-a-failback"></a>장애 복구(failback)를 수행하려면
1. Azure 포털에서 파일 서버 VM에 대해 만들어진 **복구 서비스** 자격 증명 모음 &gt; **복구 계획(사이트 복구)** &gt; **recoveryplan_name**을 선택합니다.
2. 복구 계획 블레이드에서 **추가 정보** &gt;  **계획된 장애 조치**를 클릭합니다.
3. 원본 및 대상 위치를 선택하고 적절한 데이터 동기화 및 VM 만들기 옵션을 선택합니다.
4. **확인** 단추를 클릭하여 장애 조치 프로세스를 시작합니다.

   ![장애 복구(failback) 시작](./media/storsimple-disaster-recovery-using-azure-site-recovery/image10.png)

## <a name="best-practices"></a>모범 사례
### <a name="capacity-planning-and-readiness-assessment"></a>용량 계획 및 준비 평가
#### <a name="hyper-v-site"></a>Hyper-V 사이트
[User Capacity planner tool(사용자 Capacity Planner 도구)](http://www.microsoft.com/download/details.aspx?id=39057) 을 사용하여 Hyper-V 복제본 환경에 대한 서버, 저장소 및 네트워크 인프라를 디자인합니다.

#### <a name="azure"></a>Azure
VM에서 [Azure 가상 컴퓨터 준비 평가 도구](http://azure.microsoft.com/downloads/vm-readiness-assessment/) 를 실행하여 VM이 Azure VM 및 Azure Site Recovery 서비스와 호환되는지 확인할 수 있습니다. 준비 평가 도구는 VM 구성을 확인하고 구성이 Azure와 호환되지 않는 경우 경고합니다. 예를 들어 C: 드라이브가 127GB 보다 큰 경우 경고가 발생합니다.

용량 계획은 다음과 같은 두 개 이상의 중요 프로세스로 구성됩니다.

* 온-프레미스 Hyper-V VM을 Azure VM 크기(예: A6, A7, A8, A9)에 매핑
* 필요한 인터넷 대역폭 확인

## <a name="limitations"></a>제한 사항
* .현재는 StorSimple 장치 1개만 장애 조치(failover)할 수 있습니다(단일 StorSimple Cloud Appliance에). 여러 StorSimple 장치에 걸쳐 있는 파일 서버 시나리오는 아직 지원되지 않습니다.
* VM에 대한 보호를 사용하도록 설정하는 동안 오류가 발생하는 경우 iSCSI 대상의 연결을 끊었는지 확인합니다.
* 볼륨 컨테이너에 걸쳐 있는 백업 정책으로 인해 함께 그룹화된 모든 볼륨 컨테이너는 함께 장애 조치(failover)됩니다.
* 선택한 볼륨 컨테이너에 있는 모든 볼륨은 장애 조치(failover)됩니다.
* 64TB 이상까지 추가되는 볼륨은 단일 StorSimple Cloud Appliance의 최대 용량이 64TB이므로 장애 조치(failover)될 수 없습니다.
* 계획된/계획되지 않은 장애 조치(failover)가 실패하고 Azure에서 VM이 만들어지는 경우 VM을 정리하지 마세요. 대신 장애 복구(failback)를 수행하세요. VM을 삭제하는 경우 온-프레미스 VM을 다시 켤 수 없습니다.
* 장애 조치(failover) 후 볼륨이 표시되지 않는 경우 VM으로 이동하여 디스크 관리를 열고 디스크를 다시 검색한 다음 온라인 상태로 만듭니다.
* 일부 경우 DR 사이트의 드라이브 문자가 온-프레미스의 드라이브 문자와 다를 수 있습니다. 이 문제가 발생할 경우 장애 조치(failover)가 완료된 후 수동으로 문제를 해결해야 합니다.
* 장애 조치(failover) 작업 시간 제한: 볼륨 컨테이너의 장애 조치(failover)에 스크립트당 Azure Site Recovery 제한(현재 120분)보다 많은 시간이 걸리는 경우 StorSimple 스크립트 시간 제한이 초과합니다.
* 백업 작업 시간 제한: 볼륨의 백업에 스크립트당 Azure Site Recovery 제한(현재 120분)보다 많은 시간이 걸리는 경우 StorSimple 스크립트 시간 제한이 초과합니다.

  > [!IMPORTANT]
  > Azure 포털에서 수동으로 백업을 실행한 다음 다시 복구 계획을 실행합니다.

* 복제 작업 시간 제한: 볼륨의 복제에 스크립트당 Azure Site Recovery 제한(현재 120분)보다 많은 시간이 걸리는 경우 StorSimple 스크립트 시간 제한이 초과합니다.
* 시간 동기화 오류: 포털에서 백업에 성공한 경우에도 백업에 실패했다는 StorSimple 스크립트 오류가 발생합니다. 이 오류의 가능한 원인은 StorSimple 어플라이언스의 시간이 표준 시간대의 현재 시간과 동기화되지 않았기 때문일 수 있습니다.

  > [!IMPORTANT]
  > 어플라이언스 시간을 표준 시간대의 현재 시간과 동기화합니다.

* 어플라이언스 장애 조치(failover) 오류: 복구 계획이 실행 중일 때 어플라이언스 장애 조치(failover)가 있는 경우 StorSimple 스크립트가 실패할 수 있습니다.

  > [!IMPORTANT]
  > 어플라이언스 장애 조치(failover)가 완료된 후 복구 계획을 다시 실행합니다.


## <a name="summary"></a>요약
Azure Site Recovery를 사용하여 StorSimple 저장소에 호스트된 파일 공유가 있는 파일 서버 VM에 대한 전체 자동화된 재해 복구 계획을 만들 수 있습니다. 중단이 발생할 경우 어디서나 몇 초 이내에 장애 조치(failover)를 시작하고 몇 분 만에 응용 프로그램을 가동할 수 있습니다.
