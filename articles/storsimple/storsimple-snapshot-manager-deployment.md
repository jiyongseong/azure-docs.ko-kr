---
title: "StorSimple Snapshot Manager 배포 | Microsoft Docs"
description: "StorSimple 데이터 보호 및 백업 기능을 관리하기 위한 MMC 스냅인인 StorSimple 스냅숏 관리자를 다운로드 및 설치하는 방법을 알아봅니다."
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: f0128f57-519e-49ec-9187-23575809cdbe
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: cde355381b0d726a1ab340bc4230b2dc8f6e2c56
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-the-storsimple-snapshot-manager-mmc-snap-in"></a>StorSimple 스냅숏 관리자 MMC 스냅인 배포

## <a name="overview"></a>개요
StorSimple 스냅숏 관리자는 Microsoft Azure StorSimple 환경에서 데이터 보호 및 백업 관리를 간소화하는 MMC(Microsoft Management Console) 스냅인입니다. StorSimple 스냅숏 관리자를 사용하면 완전히 통합된 저장소 시스템인 것처럼 Microsoft Azure StorSimple 온-프레미스 및 클라우드 저장소를 관리할 수 있으므로 백업 및 복원 프로세스가 간소화되고 비용이 절감됩니다. 

이 자습서에서는 StorSimple 스냅숏 관리자 설치, 제거 및 업그레이드에 대한 절차와 더불어 구성 요구 사항도 설명합니다.

> [!NOTE]
> * StorSimple 스냅숏 관리자를 사용하여 Microsoft Azure StorSimple 가상 배열 (StorSimple 온-프레미스 가상 장치라고도 함)를 관리할 수 없습니다.
> * StorSimple 장치에 StorSimple 업데이트 2를 설치하려는 경우 StorSimple Snapshot Manager의 최신 버전을 다운로드하여 **StorSimple 업데이트 2를 설치하기 전에**설치해야 합니다. StorSimple Snapshot Manager의 최신 버전은 이전 버전과 호환되며 릴리스된 모든 버전의 Microsoft Azure StorSimple에서 작동합니다. StorSimple Snapshot Manager의 이전 버전을 사용하고 있다면 업데이트해야 합니다(새 버전을 설치하기 전에 이전 버전을 제거할 필요 없음).


## <a name="storsimple-snapshot-manager-installation"></a>StorSimple 스냅숏 관리자 설치
StorSimple Snapshot Manager는 Windows Server 2008 R2 SP1, Windows Server 2012 또는 Windows Server 2012 R2 운영 체제를 실행하는 컴퓨터에 설치할 수 있습니다. Windows 2008 R2를 실행하는 서버에는 Windows Server 2008 SP1 및 Windows Management Framework 3.0도 설치해야 합니다.

Microsoft Management Console(MMC)용 StorSimple 스냅숏 관리자 스냅인을 설치하거나 업그레이드하기 전에 Microsoft Azure StorSimple 장치 및 호스트 서버가 올바르게 구성되었는지 확인합니다.

## <a name="configure-prerequisites"></a>필수 조건 구성
다음 단계에서는 StorSimple 스냅숏 관리자를 설치하기 전에 완료해야 하는 구성 작업의 대략적인 개요를 제공합니다. 시스템 요구 사항 및 단계별 지침을 포함한 전체 Microsoft Azure StorSimple 구성 및 설치 정보는 [온-프레미스 StorSimple 장치 배포](storsimple-8000-deployment-walkthrough-u2.md)를 참조하세요.

> [!IMPORTANT]
> 시작하기 전에 [온-프레미스 StorSimple 장치 배포](storsimple-8000-deployment-walkthrough-u2.md)에서 [배포 구성 검사 목록](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist) 및 [배포 필수 조건](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites)을 검토하세요.
> <br>
> 
> 

### <a name="before-you-install-storsimple-snapshot-manager"></a>StorSimple 스냅숏 관리자를 설치하기 전에
1. [StorSimple 8100 장치 설치](storsimple-8100-hardware-installation.md) 또는 [StorSimple 8600 장치 설치](storsimple-8600-hardware-installation.md)에 설명된 대로 Microsoft Azure StorSimple 장치를 개봉하고 탑재한 후 연결합니다.
2. 호스트 컴퓨터에서 다음 운영 체제 중 하나를 실행 중인지 확인합니다.
   
   * Windows Server 2008 R2(Windows 2008 R2를 실행하는 서버에는 Windows Server 2008 SP1 및 Windows Management Framework 3.0도 설치해야 합니다.)
   * Windows Server 2012
   * Windows Server 2012 R2
     
     StorSimple 가상 장치의 경우 호스트는 Microsoft Azure 가상 컴퓨터여야 합니다.
3. Microsoft Azure StorSimple 구성 요구 사항이 모두 충족되었는지 확인합니다. 자세한 내용은 [배포 필수 조건](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites)을 참조하세요.
4. 호스트에 장치를 연결하고 초기 구성을 수행합니다. 자세한 내용은 [배포 단계](storsimple-8000-deployment-walkthrough-u2.md#deployment-steps)를 참조하세요.
5. 장치에서 볼륨을 만들어 호스트에 할당한 후 호스트에 탑재하여 사용할 수 있는지 확인합니다. StorSimple 스냅숏 관리자는 다음과 같은 유형의 볼륨을 지원합니다.
   
   * 기본 볼륨
   * 단순 볼륨
   * 동적 볼륨
   * 미러된 동적 볼륨(RAID 1)
   * 클러스터 공유 볼륨
     
     StorSimple 장치 또는 StorSimple 가상 장치에서 볼륨을 만드는 방법에 대한 자세한 내용은 [온-프레미스 StorSimple 장치 배포](storsimple-8000-deployment-walkthrough-u2.md)에서 [6단계: 볼륨 만들기](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume)를 참조하세요.

## <a name="install-a-new-storsimple-snapshot-manager"></a>새 StorSimple 스냅숏 관리자 설치
StorSimple 스냅숏 관리자를 설치하기 전에 StorSimple 장치 또는 StorSimple 가상 장치에서 만든 볼륨이 [필수 조건 구성](#configure-prerequisites)에서 설명한 대로 탑재, 초기화 및 포맷되어 있는지 확인합니다.

> [!IMPORTANT]
> * StorSimple 가상 장치의 경우 호스트는 Microsoft Azure 가상 컴퓨터여야 합니다. 
> * 호스트에서 Windows 2008 R2, Windows Server 2012 또는 Windows Server 2012 R2가 실행되고 있어야 합니다. 서버에서 Windows Server 2008 R2를 실행하는 경우에는 Windows Server 2008 SP1 및 Windows Management Framework 3.0도 설치해야 합니다.
> * StorSimple 스냅숏 관리자에 장치를 연결하려면 먼저 호스트에서 StorSimple 장치로 iSCSI 연결을 구성해야 합니다.

StorSimple 스냅숏 관리자의 새로운 설치를 완료하려면 다음 단계를 따릅니다. 업그레이드를 설치하는 경우는 [StorSimple 스냅숏 관리자 업그레이드 또는 다시 설치](#upgrade-or-reinstall-storsimple-snapshot-manager)를 참조하세요.

* 1단계: StorSimple 스냅숏 관리자 설치 
* 2단계: 장치에 StorSimple 스냅숏 관리자 연결 
* 3단계: 장치에 대한 연결 확인 

### <a name="step-1-install-storsimple-snapshot-manager"></a>1단계: StorSimple 스냅숏 관리자 설치
다음 단계를 사용하여 StorSimple 스냅숏 관리자를 설치합니다.

#### <a name="to-install-storsimple-snapshot-manager"></a>StorSimple 스냅숏 관리자를 설치하려면
1. StorSimple 스냅숏 관리자 소프트웨어를 다운로드(Microsoft 다운로드 센터에서 [StorSimple 스냅숏 관리자](https://www.microsoft.com/download/details.aspx?id=44220) 로 이동)하고 호스트에 로컬로 저장합니다.
2. 파일 탐색기에서 압축된 폴더를 마우스 오른쪽 단추로 클릭하고 **모두 추출**을 클릭합니다.
3. **압축(Zip) 폴더 풀기** 창의 **대상을 선택하고 압축 파일을 푸십시오.** 상자에서 파일을 추출할 경로를 입력하거나 찾습니다.
   
    > [!IMPORTANT]
    > C: 드라이브에 StorSimple 스냅숏 관리자를 설치해야 합니다.
    
4. **완료되면 압축을 푼 파일 표시** 확인란을 선택한 다음 **추출**을 클릭합니다.
   
    ![파일 추출 대화 상자](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png) 
5. 추출이 완료되면 대상 폴더가 열립니다. 대상 폴더에 표시되는 응용 프로그램 설치 아이콘을 두 번 클릭합니다.
6. **설치 완료** 메시지가 나타나면 **닫기**를 클릭합니다. 바탕 화면에 StorSimple 스냅숏 관리자 아이콘이 표시됩니다.
   
    ![바탕 화면 아이콘](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png) 

### <a name="step-2-connect-storsimple-snapshot-manager-to-a-device"></a>2단계: 장치에 StorSimple 스냅숏 관리자 연결
다음 단계를 사용하여 StorSimple 스냅숏 관리자를 StorSimple 장치에 연결합니다.

#### <a name="to-connect-storsimple-snapshot-manager-to-a-device"></a>장치에 StorSimple 스냅숏 관리자를 연결하려면
1. 바탕 화면에서 StorSimple 스냅숏 관리자 아이콘을 클릭합니다. StorSimple 스냅숏 관리자 창이 나타납니다. 창에는 **범위** 창, **결과** 창 및 **작업** 창이 있습니다. 
   
    ![StorSimple 스냅숏 관리자 사용자 인터페이스](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png)
   
   * **범위** 창(왼쪽 창)에는 트리 구조로 정리된 노드 목록이 포함되어 있습니다. 일부 노드를 확장하면 해당 노드와 관련된 보기 또는 특정 데이터를 선택할 수 있습니다. 노드를 확장하거나 축소하려면 화살표 아이콘을 클릭합니다. **범위** 창에서 항목을 마우스 오른쪽 단추로 클릭하면 해당 항목에 대해 사용할 수 있는 작업 목록을 볼 수 있습니다.
   * **결과** 창(가운데 창)에는 **범위** 창에서 선택한 노드, 보기 또는 데이터에 대한 자세한 상태 정보가 포함됩니다.
   * **작업** 창에는 **범위** 창에서 선택한 노드, 보기 또는 데이터에서 수행할 수 있는 작업이 나열됩니다.
     
     StorSimple 스냅숏 관리자 사용자 인터페이스에 대한 자세한 설명은 [StorSimple 스냅숏 관리자 사용자 인터페이스](storsimple-use-snapshot-manager.md)를 참조하세요.
2. **범위** 창에서 **장치** 노드를 마우스 오른쪽 단추로 클릭한 다음 **장치 구성**을 클릭합니다. **장치 구성** 대화 상자가 나타납니다.
   
    ![장치 구성](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png) 
3. **장치** 목록 상자에서 Microsoft Azure StorSimple 장치 또는 가상 장치의 IP 주소를 선택합니다. 암호 **텍스트 상자** 에 Azure Portal에서 장치에 대한 StorSimple 스냅숏 관리자 암호를 입력합니다. **확인**을 클릭합니다.
4. StorSimple 스냅숏 관리자에서 사용자가 지정한 장치를 검색합니다. 장치를 사용할 수 있으면 StorSimple 스냅숏 관리자가 연결을 추가합니다. [장치에 대한 연결 확인](#to-verify-the-connection) 을 통해 연결이 성공적으로 추가되었는지 확인할 수 있습니다.
   
    어떤 이유로든 장치를 사용할 수 없으면 StorSimple 스냅숏 관리자에서 오류 메시지를 반환합니다. **확인**을 클릭하여 오류 메시지를 닫은 다음 **취소**를 클릭하여 **장치 구성** 대화 상자를 닫습니다.
5. 장치에 연결할 때 볼륨 그룹에 연결된 백업이 있는 경우 StorSimple 스냅숏 관리자는 해당 장치에 대해 구성된 각 볼륨 그룹을 가져옵니다. 연결된 백업이 없는 볼륨 그룹은 가져오지 않습니다. 또한 볼륨 그룹에 대해 만든 백업 정책도 가져오지 않습니다. 가져온 그룹을 보려면 **범위** 창의 최상위 **볼륨 그룹** 노드를 마우스 오른쪽 단추로 클릭하고 **가져온 그룹 설정/해제**를 클릭합니다.

### <a name="step-3-verify-the-connection-to-the-device"></a>3단계: 장치에 대한 연결 확인
다음 단계를 사용하여 StorSimple 스냅숏 관리자가 StorSimple 장치에 연결되어 있는지 확인합니다.

#### <a name="to-verify-the-connection"></a>연결을 확인하려면
1. **범위** 창에서 **장치** 노드를 클릭합니다.
   
    ![StorSimple 스냅숏 관리자 장치 상태](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png) 
2. **결과** 창에서 다음을 확인합니다. 
   
   * 장치 아이콘에 녹색 표시기가 나타나고 **상태** 열에 **사용 가능**이 나타나면 해당 장치는 연결되어 있습니다. 
   * 장치 아이콘에 빨간색 표시기가 나타나고 **상태** 열에 사용할 수 없음이 나타나면 해당 장치는 연결되어 있지 않습니다. 
   * **상태** 열에 **새로 고치는 중**이 표시되면 StorSimple Snapshot Manager가 연결된 장치에 대해 연결된 백업 및 볼륨 그룹을 검색하는 중입니다.

## <a name="upgrade-or-reinstall-storsimple-snapshot-manager"></a>StorSimple 스냅숏 관리자 업그레이드 또는 다시 설치
StorSimple 스냅숏 관리자를 업그레이드하거나 다시 설치하기 전에 해당 소프트웨어를 완전히 제거해야 합니다. 

StorSimple 스냅숏 관리자를 다시 설치하기 전에 호스트 컴퓨터에 있는 기존 StorSimple 스냅숏 관리자 데이터베이스를 백업합니다. 이렇게 하면 백업 정책 및 구성 정보도 저장되므로 백업에서 이 데이터를 쉽게 복원할 수 있습니다.

StorSimple 스냅숏 관리자를 업그레이드하거나 다시 설치하는 경우 다음 단계를 따르세요.

* 1단계: StorSimple 스냅숏 관리자 제거 
* 2단계: StorSimple 스냅숏 관리자 데이터베이스 백업 
* 3단계: StorSimple 스냅숏 관리자 다시 설치 및 데이터베이스 복원 

### <a name="step-1-uninstall-storsimple-snapshot-manager"></a>1단계: StorSimple 스냅숏 관리자 제거
다음 단계를 사용하여 StorSimple 스냅숏 관리자를 제거합니다.

#### <a name="to-uninstall-storsimple-snapshot-manager"></a>StorSimple 스냅숏 관리자를 제거하려면
1. 호스트 컴퓨터에서 **제어판**을 열고 **프로그램**을 클릭한 다음 **프로그램 및 기능**을 클릭합니다.
2. 왼쪽 창에서 **프로그램 제거 또는 변경**을 클릭합니다.
3. **StorSimple Snapshot Manager**를 마우스 오른쪽 단추로 클릭하고 **제거**를 클릭합니다.
4. 그러면 StorSimple 스냅숏 관리자 설치 프로그램이 시작됩니다. **설치 수정**을 클릭하고 **제거**를 클릭합니다.
   
   > [!NOTE]
   > StorSimple 스냅숏 관리자 또는 디스크 관리 등과 같은 MMC 프로세스가 백그라운드로 실행되고 있으면 제거에 실패하게 되고 프로그램을 제거하려면 먼저 모든 MMC 인스턴스를 닫으라는 메시지를 받게 됩니다. **설치가 완료되면 자동으로 응용 프로그램을 닫고 다시 시작합니다**를 선택하고 **확인**을 클릭합니다.
   > 
   > 
5. 제거 프로세스가 완료되면 **설치 완료** 메시지가 표시됩니다. **닫기**를 클릭합니다.

### <a name="step-2-back-up-the-storsimple-snapshot-manager-database"></a>2단계: StorSimple 스냅숏 관리자 데이터베이스 백업
다음 단계를 사용하여 StorSimple 스냅숏 관리자 데이터베이스의 복사본을 만들어 저장합니다.

#### <a name="to-back-up-the-database"></a>데이터베이스를 백업하려면
1. Microsoft StorSimple 관리 서비스를 중지합니다.
   
   1. 서버 관리자를 시작합니다.
   2. 서버 관리자 대시보드의 **도구** 메뉴에서 **서비스**를 선택합니다.
   3. **서비스** 페이지에서 **Microsoft StorSimple 관리 서비스**를 선택합니다.
   4. 오른쪽 창의 **Microsoft StorSimple 관리 서비스** 아래에서 **서비스 중지**를 클릭합니다.
      
        ![StorSimple 장치 관리자 서비스 중지](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)
2. C:\ProgramData\Microsoft\StorSimple\BACatalog로 이동합니다. 
   
   > [!NOTE]
   > ProgramData는 숨겨진 폴더입니다.
  
3. 카탈로그 XML 파일을 찾아 파일을 복사하고 안전한 위치 또는 클라우드에 복사본을 저장합니다.
   
    ![StorSimple 백업 카탈로그 파일](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)
4. Microsoft StorSimple 관리 서비스를 다시 시작합니다. 
   
   1. 서버 관리자 대시보드의 **도구** 메뉴에서 **서비스**를 선택합니다.
   2. **서비스** 페이지에서 **Microsoft StorSimple 관리 서비스**를 선택합니다.
   3. 오른쪽 창의 **Microsoft StorSimple 관리 서비스** 아래에서 **서비스 다시 시작**을 클릭합니다. 

### <a name="step-3-reinstall-storsimple-snapshot-manager-and-restore-the-database"></a>3단계: StorSimple 스냅숏 관리자 다시 설치 및 데이터베이스 복원
StorSimple 스냅숏 관리자를 다시 설치하려면 [새 StorSimple 스냅숏 관리자 설치](#install-a-new-storsimple-snapshot-manager)의 단계를 따릅니다. 그런 다음 아래의 절차를 사용하여 StorSimple 스냅숏 관리자 데이터베이스를 복원합니다.

#### <a name="to-restore-the-database"></a>데이터베이스를 복원하려면
1. Microsoft StorSimple 관리 서비스를 중지합니다.
   
   1. 서버 관리자를 시작합니다.
   2. 서버 관리자 대시보드의 **도구** 메뉴에서 **서비스**를 선택합니다.
   3. **서비스** 페이지에서 **Microsoft StorSimple 관리 서비스**를 선택합니다.
   4. 오른쪽 창의 **Microsoft StorSimple 관리 서비스** 아래에서 **서비스 중지**를 클릭합니다.
2. C:\ProgramData\Microsoft\StorSimple\BACatalog로 이동합니다.
   
   > [!NOTE]
   > ProgramData는 숨겨진 폴더입니다.
   > 
   > 
3. 카탈로그 XML 파일을 삭제하고 이전에 저장한 버전으로 바꿉니다.
4. Microsoft StorSimple 관리 서비스를 다시 시작합니다. 
   
   1. 서버 관리자 대시보드의 **도구** 메뉴에서 **서비스**를 선택합니다.
   2. **서비스** 페이지에서 **Microsoft StorSimple 관리 서비스**를 선택합니다.
   3. 오른쪽 창의 **Microsoft StorSimple 관리 서비스** 아래에서 **서비스 다시 시작**을 클릭합니다.

## <a name="next-steps"></a>다음 단계
* StorSimple 스냅숏 관리자에 대해 자세히 알아보려면 [StorSimple 스냅숏 관리자란?](storsimple-what-is-snapshot-manager.md)으로 이동하세요.
* StorSimple 스냅숏 관리자 사용자 인터페이스에 대해 자세히 알아보려면 [StorSimple 스냅숏 관리자 사용자 인터페이스](storsimple-use-snapshot-manager.md)로 이동하세요.
* StorSimple 스냅숏 관리자를 사용하는 방법에 대해 자세히 알아보려면 [StorSimple 스냅숏 관리자를 사용하여 StorSimple 솔루션 관리](storsimple-snapshot-manager-admin.md)로 이동하세요.

