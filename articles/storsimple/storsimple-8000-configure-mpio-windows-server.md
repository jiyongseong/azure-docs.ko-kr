---
title: StorSimple 장치에 대한 MPIO 구성 | Microsoft Docs
description: Windows Server 2012 R2를 실행하는 호스트에 연결된 StorSimple 장치에 대해 MPIO(다중 경로 I/O)를 구성하는 방법을 설명합니다.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/26/2018
ms.author: alkohli
ms.openlocfilehash: 4f2b094604f486d283574f4669fcad6f72bd4431
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="configure-multipath-io-for-your-storsimple-device"></a>StorSimple 장치에 대한 다중 경로 I/O 구성

이 자습서에서는 Windows Server 2012 R2를 실행하는 호스트에서 MPIO(다중 경로 I/O) 기능을 설치 및 사용하고 StorSimple 실제 장치에 연결하기 위해 수행해야 할 단계에 대해 설명합니다. 이 문서의 지침은 StorSimple 8000 시리즈 물리적 장치에만 적용됩니다. MPIO는 현재 StorSimple Cloud Appliance에서 지원되지 않습니다.

Microsoft는 고가용성, 내결함성 iSCSI 네트워크 구성을 구축하는 데 도움을 주기 위해 Windows Server에서 MPIO(다중 경로 I/O) 기능에 대한 지원을 구축했습니다. MPIO는 중복 실제 경로 구성 요소(어댑터, 케이블 및 스위치)를 사용하여 서버 및 저장소 장치 간의 논리 경로를 만듭니다. 논리 경로에 오류를 일으키는 구성 요소 오류가 발생할 경우 응용 프로그램이 데이터에 계속 액세스할 수 있도록 다중 경로 논리가 I/O에 대한 대체 경로를 사용합니다. 또한 구성에 따라 MPIO가 이러한 모든 경로에서 부하를 다시 분산하여 성능을 향상할 수도 있습니다. 자세한 내용은 [MPIO 개요](https://technet.microsoft.com/library/cc725907.aspx "MPIO 개요 and features")을 참조하세요.

StorSimple 솔루션의 고가용성을 위해 MPIO는 StorSimple 장치에서 구성되어야 합니다. MPIO가 Windows Server 2012 R2를 실행하는 호스트 서버에 설치되면 서버가 링크, 네트워크 또는 인터페이스를 허용할 수 있습니다.

## <a name="mpio-configuration-summary"></a>MPIO 구성 요약

MPIO는 Windows Server에서 선택적 기능이며 기본적으로 설치되지 않습니다. 서버 관리자를 통해 기능으로 설치해야 합니다.

StorSimple 장치에서 MPIO를 구성하려면 다음 단계를 수행해야 합니다.

* 1단계: Windows Server 호스트에 MPIO 설치
* 2단계: StorSimple 볼륨에 대한 MPIO 구성
* 3단계: 호스트에 StorSimple 볼륨 탑재
* 4단계: 고가용성 및 부하 분산을 위해 MPIO 구성

위의 각 단계는 다음 섹션에서 설명합니다.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>1단계: Windows Server 호스트에 MPIO 설치

Windows Server 호스트에 이 기능을 설치하려면 다음 절차를 완료합니다.

#### <a name="to-install-mpio-on-the-host"></a>호스트에 MPIO를 설치하려면

1. Windows Server 호스트에서 서버 관리자를 엽니다. 기본적으로 관리자 그룹의 구성원이 Windows Server 2012 R2 또는 Windows Server 2012를 실행하는 컴퓨터에 로그온하면 서버 관리자가 시작됩니다. 서버 관리자가 아직 열려있지 않으면 **시작 > 서버 관리자**를 클릭합니다.
   
   ![서버 관리자](./media/storsimple-configure-mpio-windows-server/IC740997.png)

2. **서버 관리자 > 대시보드 > 역할 및 기능 추가**를 클릭합니다. 이렇게 하면 **역할 및 기능 추가** 마법사가 시작됩니다.
   
   ![역할 및 기능 추가 마법사 1](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. **역할 및 기능 추가** 마법사에서 다음 단계를 수행합니다.
   
   1. **시작하기 전에** 페이지에서 **다음**을 클릭합니다.
   2. **설치 유형 선택** 페이지에서 **역할 기반 또는 기능 기반** 설치의 기본 설정을 수락합니다. **다음**을 클릭합니다.
   
       ![역할 및 기능 추가 마법사 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
   3. **선택 대상 서버** 페이지에서 **서버 풀에서 서버 선택**을 선택합니다. 호스트 서버가 자동으로 검색됩니다. **다음**을 클릭합니다.
   4. **서버 역할 선택** 페이지에서 **다음**을 클릭합니다.
   5. **기능 선택** 페이지에서 **다중 경로 I/O**를 선택하고 **다음**을 클릭합니다.
   
       ![역할 및 기능 추가 마법사 5](./media/storsimple-configure-mpio-windows-server/IC741000.png)
   6. **설치 선택 확인** 페이지에서 선택 사항을 확인한 다음 아래 표시된 대로 **필요한 경우 자동으로 대상 서버 다시 시작**을 선택합니다. **Install**을 클릭합니다.
   
       ![역할 및 기능 추가 마법사 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
   7. 설치가 완료되면 알림이 표시됩니다. **닫기** 를 클릭하여 마법사를 닫습니다.
   
       ![역할 및 기능 추가 마법사 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>2단계: StorSimple 볼륨에 대한 MPIO 구성

MPIO는 StorSimple 볼륨을 식별하도록 구성해야 합니다. StorSimple 볼륨을 인식하도록 MPIO를 구성하려면 다음 단계를 수행합니다.

#### <a name="to-configure-mpio-for-storsimple-volumes"></a>StorSimple 볼륨에 대한 MPIO를 구성하려면

1. **MPIO 구성**을 엽니다. **서버 관리자 > 대시보드 > 도구 > MPIO**를 클릭합니다.
2. **MPIO 속성** 대화 상자에서 **다중 경로 찾기** 탭을 선택합니다.
3. **iSCSI 장치에 대한 지원 추가**를 선택한 다음 **추가**를 클릭합니다.  
   ![MPIO 속성 다중 경로 검색](./media/storsimple-configure-mpio-windows-server/IC741003.png)
4. 메시지가 표시되면 서버를 다시 부팅합니다.
5. **MPIO 속성** 대화 상자에서 **MPIO 장치** 탭을 클릭합니다. **추가**를 클릭합니다.
    </br>![MPIO 속성 MPIO 장치](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. **MPIO 지원 추가** 대화 상자의 **장치 하드웨어 ID**에서 장치 일련 번호를 입력합니다. 장치 일련 번호를 가져오려면 StorSimple 장치 관리자 서비스에 액세스합니다. **장치 > 대시보드**로 이동합니다. StorSimple 관리자 서비스에 액세스하고 **장치 &gt; 대시보드** 를 탐색하여 장치 일련 번호를 알 수 있습니다.
    </br>
    ![MPIO 지원 추가](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. 메시지가 표시되면 서버를 다시 부팅합니다.

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>3단계: 호스트에 StorSimple 볼륨 탑재

MPIO가 Windows Server에 구성된 후 StorSimple 장치에 생성된 볼륨이 탑재될 수 있으며 중복에 대해 MPIO를 활용할 수 있습니다. 볼륨을 탑재하려면 다음 단계를 수행합니다.

#### <a name="to-mount-volumes-on-the-host"></a>호스트에 볼륨을 탑재 하려면

1. Windows Server 호스트에서 **iSCSI 초기자 속성** 창을 엽니다. **서버 관리자 > 대시보드 > 도구 > iSCSI 초기자**를 클릭합니다.
2. **iSCSI 초기자 속성** 대화 상자에서 검색 탭을 클릭한 다음 **대상 포털 검색**을 클릭합니다.
3. **대상 포털 검색** 대화 상자에서 다음 단계를 수행합니다.
   
   1. StorSimple 장치의 데이터 포트 IP 주소를 입력합니다(예: 데이터 0 입력).
   2. **확인**을 클릭하여 **iSCSI 초기자 속성** 대화 상자로 돌아갑니다.
     
     > [!IMPORTANT]
     > **iSCSI 연결에 개인 네트워크를 사용하는 경우 개인 네트워크에 연결된 데이터 포트의 IP 주소를 입력합니다.**
    
4. 장치에서 두 번째 네트워크 인터페이스(예: 데이터 1)에 대해 2~3단계를 반복합니다. ISCSI에 대해 이러한 인터페이스를 사용해야 합니다. 자세한 내용은 [네트워크 인터페이스 수정](storsimple-8000-modify-device-config.md#modify-network-interfaces)을 참조하세요.
5. **iSCSI 초기자 속성** 대화 상자에서 **대상** 탭을 선택합니다. **검색된 대상**아래에 StorSimple 장치 대상 IQN이 보입니다.

   ![iSCSI 초기자 속성 대상 탭](./media/storsimple-configure-mpio-windows-server/IC741007.png)
   
6. **연결** 을 클릭하여 StorSimple 장치에 iSCSI 세션을 설정합니다. **대상에 연결** 대화 상자가 표시됩니다.
7. **대상에 연결** 대화 상자에서 **다중 경로 사용** 확인란을 선택합니다. **고급**을 클릭합니다.
8. **고급 설정** 대화 상자에서 다음 단계를 수행합니다.
   
   1. **로컬 어댑터** 드롭다운 목록에서 **Microsoft iSCSI 초기자**를 선택합니다.
   2. **초기자 IP** 드롭다운 목록에서 호스트의 IP 주소를 선택합니다.
   3. **대상 포털** IP 드롭다운 목록에서 장치 인터페이스의 IP를 선택합니다.
   4. **확인**을 클릭하여 **iSCSI 초기자 속성** 대화 상자로 돌아갑니다.
9. **속성**을 클릭합니다. **속성** 대화 상자에서 **세션 추가**를 클릭합니다.
10. **대상에 연결** 대화 상자에서 **다중 경로 사용** 확인란을 선택합니다. **고급**을 클릭합니다.
11. **고급 설정** 대화 상자에서:

    1. **로컬 어댑터** 드롭다운 목록에서 Microsoft iSCSI 초기자를 선택합니다.
    2. **초기자 IP** 드롭다운 목록에서 호스트에 해당하는 IP 주소를 선택합니다. 이 경우에 해당 장치의 두 네트워크 인터페이스를 호스트의 단일 네트워크 인터페이스에 연결합니다. 따라서이 인터페이스는 첫 번째 세션에 제공된 것과 동일합니다.
    3. **대상 포털 IP** 드롭다운 목록에서 해당 장치에서 사용할 수 있는 두 번째 데이터 인터페이스의 IP 주소를 선택합니다.
    4. **확인** 을 클릭하여 iSCSI 초기자 속성 대화 상자로 돌아갑니다. 두 번째 세션을 대상에 추가했습니다.
12. **서버 관리자 > 대시보드 > 컴퓨터 관리**로 이동하여 **컴퓨터 관리**를 엽니다. 왼쪽 창에서 **저장소 > 디스크 관리**를 클릭합니다. 이 호스트에 표시되는 StorSimple 장치에 만들어진 볼륨이 **디스크 관리**에 새 디스크로 나타납니다.
13. 디스크를 초기화하고 새 볼륨을 만듭니다. 포맷 프로세스 동안 64KB의 블록 크기를 선택합니다.
    
    ![디스크 관리](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. **디스크 관리**에서 **디스크**를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.
15. StorSimple 모델 #### **다중 경로 디스크 장치 속성** 대화 상자에서 **MPIO** 탭을 클릭합니다.
    
    ![StorSimple 8100 다중 경로 디스크 장치 속성입니다.](./media/storsimple-configure-mpio-windows-server/IC741009.png)
16. **DSM 이름** 섹션에서 **세부 정보**를 클릭하고 해당 매개 변수가 기본 매개 변수로 설정되었는지 확인합니다. 기본 매개 변수는 다음과 같습니다.
    
    * 경로 확인 기간 = 30
    * 재시도 횟수 = 3
    * PDO 제거 기간 = 20
    * 재시도 간격 = 1
    * 경로 확인 사용 = 선택하지 않음

> [!NOTE]
> **기본 매개 변수를 수정하지 마세요.**


## <a name="step-4-configure-mpio-for-high-availability-and-load-balancing"></a>4단계: 고가용성 및 부하 분산을 위해 MPIO 구성

다중 경로 기반 고가용성 및 부하 분산의 경우 사용할 수 있는 다른 경로 선언을 위해 다중 세션이 수동으로 추가되어야 합니다. 예를 들어 iSCSI 네트워크에 연결된 두 인터페이스가 호스트에 있고 iSCSI 네트워크에 연결된 두 인터페이스가 장치에 있는 경우, 적합한 경로 순열로 구성된 네 개의 세션만 필요합니다(각각의 데이터 인터페이스 및 호스트 인터페이스가 다른 IP 주소에 있고 라우팅할 수 없는 경우 두 세션만 필요).

**장치 및 응용 프로그램 호스트 간에 최소 8개의 활성 병렬 세션을 유지하는 것이 좋습니다.** 이를 위해 Windows Server 시스템에서 4개의 네트워크 인터페이스를 사용하도록 설정하면 됩니다. Windows Server 호스트의 하드웨어 또는 운영 체제 수준에서 실제 네트워크 인터페이스 또는 네트워크 가상화 기술을 통한 가상 인터페이스를 사용합니다. 장치에 두 개의 네트워크 인터페이스가 있을 경우 이 구성은 8개의 활성 세션을 제공합니다. 이 구성은 장치 및 클라우드 처리량을 최적화하는 데 도움이 됩니다.

> [!IMPORTANT]
> **1GbE 및 10GbE 네트워크 인터페이스는 혼용하지 않는 것이 좋습니다. 두 네트워크 인터페이스를 사용하는 경우 두 인터페이스 모두 동일한 유형이어야 합니다.**

다음 절차는 두 네트워크 인터페이스가 있는 StorSimple 장치가 두 네트워크 인터페이스가 있는 호스트에 연결된 경우 세션을 추가하는 방법에 대해 설명합니다. 이 경우 활성 세션은 4개뿐입니다. 4개의 네트워크 인터페이스가 있는 호스트에 2개의 네트워크 인터페이스가 연결된 StorSimple 장치에서 이 동일한 절차를 사용합니다. 여기에 설명된 4개의 세션 대신 8개를 구성해야 합니다.

### <a name="to-configure-mpio-for-high-availability-and-load-balancing"></a>고가용성 및 부하 분산을 위해 MPIO를 구성하려면

1. 대상 검색 수행: **iSCSI 초기자 속성** 대화 상자에서 **검색** 탭을 클릭한 다음 **포털 검색**을 클릭합니다.
2. **대상에 연결** 대화 상자에서 장치 네트워크 인터페이스 중 하나의 IP 주소를 입력합니다.
3. **확인**을 클릭하여 **iSCSI 초기자 속성** 대화 상자로 돌아갑니다.
4. **iSCSI 초기자 속성** 대화 상자에서 **대상** 탭을 선택하고 검색된 대상을 강조 표시한 다음 **연결**을 클릭합니다. **대상에 연결** 대화 상자가 표시됩니다.
5. **대상에 연결** 대화 상자에서:
   
   1. 즐겨 찾는 대상 목록에 **이 연결 추가** 에 대해 기본으로 선택된 대상 설정을 유지합니다. 이렇게 하면 이 컴퓨터를 다시 시작할 때마다 장치가 자동으로 연결을 다시 시작합니다.
   2. **다중 경로 사용** 확인란을 선택합니다.
   3. **고급**을 클릭합니다.
6. **고급 설정** 대화 상자에서:
   
   1. **로컬 어댑터** 드롭다운 목록에서 **Microsoft iSCSI 초기자**를 선택합니다.
   2. **초기자 IP** 드롭다운 목록에서 호스트의 첫 번째 인터페이스(iSCSI 인터페이스)에 해당하는 IP 주소를 선택합니다.
   3. **대상 포털 IP** 드롭다운 목록에서 해당 장치에서 사용할 수 있는 첫 번째 데이터 인터페이스의 IP 주소를 선택합니다.
   4. **확인** 을 클릭하여 iSCSI 초기자 속성 대화 상자로 돌아갑니다.
7. **속성**을 클릭하고 **속성** 대화 상자에서 **세션 추가**를 클릭합니다.
8. **대상에 연결** 대화 상자에서 **다중 경로 사용** 확인란을 선택한 다음 **고급**을 클릭합니다.
9. **고급 설정** 대화 상자에서:
   
   1. **로컬 어댑터** 드롭다운 목록에서 **Microsoft iSCSI 초기자**를 선택합니다.
   2. **초기자 IP** 드롭다운 목록에서 호스트의 두 번째 iSCSI 인터페이스에 해당하는 IP 주소를 선택합니다.
   3. **대상 포털 IP** 드롭다운 목록에서 해당 장치에서 사용할 수 있는 두 번째 데이터 인터페이스의 IP 주소를 선택합니다.
   4. **확인**을 클릭하여 **iSCSI 초기자 속성** 대화 상자로 돌아갑니다. 이제 두 번째 세션을 대상에 추가했습니다.
10. 대상에 추가 세션(경로)을 추가하려면 8~10단계를 반복합니다. 호스트의 두 인터페이스 및 장치의 두 인터페이스와 함께 총 네 개의 세션을 추가할 수 있습니다.
11. 원하는 세션(경로)을 추가한 후 **iSCSI 초기자 속성** 대화 상자에서 대상을 선택하고 **속성**을 클릭합니다. **속성** 대화 상자의 세션 탭에서 가능한 경로 순열에 해당하는 네 개의 세션 식별자를 기록해 둡니다. 세션을 취소하려면 세션 식별자를 옆에 있는 확인란을 선택하고 **연결 끊기**를 클릭합니다.
12. 세션 내에 표시되는 장치를 보려면 **장치** 탭을 선택합니다. 선택한 장치에 대한 MPIO 정책을 구성하려면 **MPIO**를 클릭합니다. **장치 세부 정보** 대화 상자가 표시됩니다. **MPIO** 탭에서 적절한 **부하 분산 정책** 설정을 선택할 수 있습니다. **활성** 또는 **대기** 경로 유형도 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

[StorSimple 장치 관리자 서비스를 사용하여 StorSimple 장치 구성 수정](storsimple-8000-modify-device-config.md)에 대해 자세히 알아봅니다.

