---
title: "StorSimple 8000 시리즈 장치 컨트롤러 관리 | Microsoft Docs"
description: "StorSimple 장치 컨트롤러를 중지, 다시 시작, 종료 또는 다시 설정하는 방법을 알아봅니다."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/19/2017
ms.author: alkohli
ms.openlocfilehash: 75c1bdb570967b6d1902697597f0b5bf3f4ffb7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-your-storsimple-device-controllers"></a>StorSimple 장치 컨트롤러 관리

## <a name="overview"></a>개요

이 자습서에서는 StorSimple 장치 컨트롤러에서 수행할 수 있는 다양한 작업을 설명합니다. StorSimple 장치의 컨트롤러는 능동-수동 구성에서 중복(피어) 컨트롤러입니다. 지정된 시간에 컨트롤러는 하나만 활성화되어 모든 디스크 및 네트워크 작업을 처리합니다. 다른 컨트롤러는 수동 모드입니다. 활성 컨트롤러에 실패하면 수동 컨트롤러가 자동으로 활성화됩니다.

이 자습서는 다음을 사용하여 장치 컨트롤러를 관리하도록 단계별 지침을 제공합니다.

* StorSimple 장치 관리자 서비스에서 장치의 **컨트롤러** 블레이드입니다.
* StorSimple용 Windows PowerShell입니다.

사용자가 StorSimple 장치 관리자 서비스를 통해 장치 컨트롤러를 관리하는 것이 좋습니다. StorSimple용 Windows PowerShell을 사용해야 동작을 수행할 수 있는 경우에는 자습서에 관련 메모가 표시됩니다.

이 자습서를 읽은 후에 다음을 수행할 수 있습니다.

* StorSimple 장치 컨트롤러를 다시 시작 또는 종료
* StorSimple 장치 종료
* StorSimple 장치를 공장 기본값으로 재설정

## <a name="restart-or-shut-down-a-single-controller"></a>단일 컨트롤러 다시 시작 또는 종료
컨트롤러 다시 시작 또는 종료는 정상적인 시스템 작업의 일환으로 필요하지 않습니다. 단일 장치 컨트롤러에 대한 종료 작업은 실패한 장치 하드웨어 구성 요소가 교체를 필요로 하는 경우에 흔하게 발생합니다. 과도한 메모리 사용 또는 작동하지 않는 컨트롤러 때문에 성능이 영향을 받는 경우에도 컨트롤러를 다시 시작할 필요가 있습니다. 또한 대체 컨트롤러를 사용하도록 설정하고 테스트하려면 컨트롤러를 성공적으로 교체한 후에 컨트롤러를 다시 시작해야 합니다.

수동 컨트롤러를 사용할 수 있다면 장치를 다시 시작해도 연결된 개시 장치를 중단하지 않습니다. 수동 컨트롤러를 사용할 수 없거나 꺼져있는 경우 활성 컨트롤러를 다시 시작하면 서비스 및 가동 중지 시간이 중단될 수 있습니다.

> [!IMPORTANT]
> * **이렇게 하면 중복 손실과 가동 중지 시간 위험 가능성이 높아져서 실행 중인 컨트롤러를 물리적으로 제거하지 말아야 합니다.**
> * 다음 절차는 물리적 StorSimple 장치에만 적용됩니다. StorSimple Cloud Appliance를 시작, 중지 및 다시 시작하는 방법에 대한 자세한 내용은 [클라우드 어플라이언스로 작업](storsimple-8000-cloud-appliance-u2.md##work-with-the-storsimple-cloud-appliance)을 참조하세요.

StorSimple용 Windows PowerShell 또는 StorSimple 장치 관리자 서비스의 Azure Portal을 통해 단일 장치 컨트롤러를 다시 시작하거나 종료할 수 있습니다.

Azure Portal에서 장치 컨트롤러를 관리하려면 다음 단계를 수행합니다.

#### <a name="to-restart-or-shut-down-a-controller-in-azure-portal"></a>Azure Portal에서 컨트롤러를 다시 시작하거나 종료하려면
1. StorSimple 장치 관리자 서비스에서 **장치**로 이동합니다. 장치 목록에서 장치를 선택합니다. 

    ![장치 선택](./media/storsimple-8000-manage-device-controller/manage-controller1.png)

2. **설정 > 컨트롤러**로 이동합니다.
   
    ![StorSimple 장치 컨트롤러가 정상임을 확인](./media/storsimple-8000-manage-device-controller/manage-controller2.png)
3. **컨트롤러** 블레이드에서 장치에 대한 컨트롤러의 상태가 **정상**인지 확인합니다. 컨트롤러를 선택하고 마우스 오른쪽 단추로 클릭한 다음 **다시 시작** 또는 **종료**를 선택합니다.

    ![StorSimple 장치 컨트롤러를 다시 시작 또는 종료 선택](./media/storsimple-8000-manage-device-controller/manage-controller3.png)

4. 컨트롤러를 다시 시작하거나 종료하기 위해 작업을 만들고 적용 가능한 경고가 있는 경우 함께 표시됩니다. 다시 시작 또는 종료를 모니터링하려면 **서비스 > 활동 로그**로 이동한 다음 서비스에 지정된 매개 변수에서 필터링합니다. 컨트롤러를 종료하는 경우 전원 단추를 눌러서 설정하려는 컨트롤러를 켜야 합니다.

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>StorSimple용 Windows PowerShell에서 컨트롤러를 다시 시작하거나 종료합니다.
다음 단계를 수행하여 StorSimple용 Windows PowerShell에서 StorSimple 장치에 단일 컨트롤러를 종료하거나 다시 시작합니다.

1. 직렬 콘솔 또는 원격 컴퓨터에서 텔넷 세션을 통해 장치에 액세스합니다. 컨트롤러 0 또는 컨트롤러 1에 연결하려면 [장치 직렬 콘솔 연결에 PuTTY 사용](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)의 단계를 수행합니다.
2. 직렬 콘솔 메뉴에서 옵션 1, **모든 권한으로 로그인**을 선택합니다.
3. 배너 메시지에서 연결된 컨트롤러(컨트롤러 0 또는 컨트롤러 1)와 활성 또는 수동(대기) 컨트롤러 여부를 기록합니다.
   
   * 프롬프트에서 단일 컨트롤러를 종료하려면 다음을 입력합니다.
     
       `Stop-HcsController`
     
       연결된 컨트롤러를 종료합니다. 활성 컨트롤러를 중지하면 장치를 수동 컨트롤러에 장애 조치합니다.

   * 컨트롤러를 다시 시작하려면 프롬프트에 다음을 입력합니다.
     
       `Restart-HcsController`
     
       연결된 컨트롤러를 다시 시작합니다. 활성 컨트롤러를 다시 시작하면 수동 컨트롤러를 다시 시작하기 전에 장애 조치합니다.

## <a name="shut-down-a-storsimple-device"></a>StorSimple 장치 종료

이 섹션에서는 원격 컴퓨터에서 실행 중이거나 실패한 StorSimple 장치를 종료하는 방법을 설명합니다. 장치 컨트롤러를 모두 종료한 후에 장치는 꺼집니다. 장치를 물리적으로 이동하는 경우 장치가 종료되거나 서비스에서 중단됩니다.

> [!IMPORTANT]
> 장치를 종료하기 전에 장치 구성 요소의 상태를 확인합니다. 사용자의 장치로 이동한 다음 **설정 > 하드웨어 상태**를 클릭합니다. **상태 및 하드웨어 상태** 블레이드에서 모든 구성 요소의 LED 상태가 녹색인지 확인합니다. 정상 장치만이 녹색 상태가 됩니다. 장치가 종료되어 작동하지 않는 구성 요소를 교체하는 경우 해당 구성 요소에 실패(빨간색) 또는 성능 저하(노란색) 상태가 표시됩니다.


#### <a name="to-shut-down-a-storsimple-device"></a>StorSimple 장치 종료하려면

1. [컨트롤러 다시 시작 또는 종료](#restart-or-shut-down-a-single-controller) 프로시저를 사용하여 장치에서 수동 컨트롤러를 식별하고 종료합니다. Azure Portal 또는 StorSimple용 Windows PowerShell에서 이 작업을 수행할 수 있습니다.
2. 활성 컨트롤러를 종료하려면 위의 단계를 반복합니다.
3. 이제 장치의 뒤쪽 평면을 살펴봐야 합니다. 두 컨트롤러를 완전히 종료한 후에 두 컨트롤러에서 상태 LED가 빨간색으로 깜박여야 합니다. 이번에 장치를 완전히 해제해야 하면 전원 및 냉각 모듈(PCM) 모두에서 전원 스위치를 끄기 위치에 둡니다. 이 장치를 해제해야 합니다.

## <a name="reset-the-device-to-factory-default-settings"></a>장치를 공장 기본 설정으로 재설정

> [!IMPORTANT]
> 장치를 공장 기본 설정으로 재설정해야 할 경우 Microsoft 지원 서비스에 문의합니다. 아래에 설명된 절차는 Microsoft 기술 지원 서비스에 따라서만 사용해야 합니다.

이 절차는 StorSimple용 Windows PowerShell을 사용하여 Microsoft Azure StorSimple 장치를 공장 기본 설정으로 다시 설정하는 방법을 설명합니다.
장치를 다시 설정하면 기본적으로 전체 클러스터에서 모든 데이터 및 설정을 제거합니다.

Microsoft Azure StorSimple 장치를 공장 기본 설정으로 다시 설정하려면 다음 단계를 수행합니다.

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>StorSimple용 Windows PowerShell의 기본 설정으로 장치를 재설정하려면
1. 직렬 콘솔을 통해 장치에 액세스합니다. **활성** 컨트롤러에 연결하려면 배너 메시지를 확인하세요.
2. 직렬 콘솔 메뉴에서 옵션 1, **모든 권한으로 로그인**을 선택합니다.
3. 프롬프트에서 전체 클러스터를 다시 설정하는 다음 명령을 입력하면 모든 데이터, 메타데이터 및 컨트롤러 설정을 제거합니다.
   
    `Reset-HcsFactoryDefault`
   
    대신 단일 컨트롤러를 재설정하려면 `-scope` 매개 변수가 있는 [Reset-HcsFactoryDefault](http://technet.microsoft.com/library/dn688132.aspx) cmdlet을 사용합니다.
   
    시스템을 여러 번 다시 부팅합니다. 재설정이 성공적으로 완료되면 알림이 표시됩니다. 시스템 모델에 따라 이 프로세스를 완료 하는 데 8100 장치로 45-60분이 걸리고 8600 장치로 60-90분이 걸릴 수 있습니다.
   
## <a name="questions-and-answers-about-managing-device-controllers"></a>장치 컨트롤러를 관리하는 방법에 대한 질문 및 답변
이 섹션에서는 StorSimple 장치 컨트롤러의 관리와 관련하여 자주 묻는 질문 중 일부를 요약합니다.

**Q.** 장치에서 컨트롤러가 모두 정상이고 켜져 있으며 활성 컨트롤러를 다시 시작 또는 종료하는 경우 무엇이 발생합니까?

**A.** 장치의 컨트롤러가 모두 정상이고 켜져 있는 경우 확인을 위한 메시지가 표시됩니다. 선택할 수 있습니다.

* **활성 컨트롤러 다시 시작** – 활성 컨트롤러를 다시 시작하면 장치가 수동 컨트롤러로 장애 조치(failover)된다는 알림이 표시됩니다. 컨트롤러가 다시 시작합니다.
* **활성 컨트롤러 종료** – 활성 컨트롤러를 종료하면 가동이 중지된다는 알림이 표시됩니다. 컨트롤러를 켜기 위해 장치에서 전원 단추를 눌러야 합니다.

**Q.** 장치에서 수동 컨트롤러가 모두 정상이고 켜져 있으며 활성 컨트롤러를 다시 시작 또는 종료하는 경우 무엇이 발생합니까?

**A.** 장치에서 수동 컨트롤러를 사용할 수 없거나 해제된 경우 다음을 선택합니다.

* **활성 컨트롤러 다시 시작** – 작업을 계속하면 서비스의 임시 중단이 발생하고 확인 메시지가 표시된다는 내용의 알림이 표시됩니다.
* **활성 컨트롤러 종료** – 작업을 계속하면 가동 중지 시간이 발생한다는 알림이 표시됩니다. 하나 또는 두 컨트롤러에서 전원 단추를 눌러서 장치를 켜야 합니다. 확인하라는 메시지가 표시됩니다.

**Q.** 어떤 경우에 컨트롤러를 다시 시작 또는 종료를 진행하는 데 실패합니까?

**A.** 다음 경우 롤러를 다시 시작하거나 종료하는 데 실패할 수 있습니다.

* 장치 업데이트를 진행 중입니다.
* 컨트롤러 다시 시작이 이미 진행 중입니다.
* 컨트롤러 종료가 이미 진행 중입니다.

**Q.** 컨트롤러를 다시 시작하거나 종료하는 경우 어떻게 확인합니까?

**A.** 컨트롤러 블레이드에서 컨트롤러의 상태를 확인할 수 있습니다. 컨트롤러 상태는 컨트롤러를 다시 시작하는 과정인지, 아니면 종료하는 과정인지를 알려줍니다. 또한 컨트롤러가 다시 시작하거나 종료된 경우 **경고** 블레이드는 정보 경고를 포함합니다. 컨트롤러 다시 시작 및 종료 작업은 활동 로그에 기록됩니다. 활동 로그에 대한 자세한 내용을 보려면 [활동 로그 보기](storsimple-8000-service-dashboard.md#view-the-activity-logs)로 이동합니다.

**Q.** 컨트롤러 장애 조치의 결과로써 I/O에 영향이 있나요?

**A.** 초기자 및 활성 컨트롤러 간의 TCP 연결은 컨트롤러 장애 조치의 결과로 재설정되지만 수동 컨트롤러가 작업을 가정하는 경우 다시 설정할 수 있습니다. 이 작업의 과정에서 초기자와 장치 간의 I/O 작업에서 임시(30초 미만) 일시 정지가 있을 수 있습니다.

**Q.** 컨트롤러를 서비스에서 종료하고 제거한 후에 서비스에 반환하는 방법은 무엇인가요?

**A.** 컨트롤러를 서비스에 반환하려면 [StorSimple 장치의 컨트롤러 모듈 교체](storsimple-8000-controller-replacement.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* StorSimple 장치 컨트롤러에서 이 자습서에 나열된 절차를 사용하여 해결할 수 없는 문제가 발생할 경우 [Microsoft 지원에 문의](storsimple-8000-contact-microsoft-support.md)하세요.
* StorSimple 장치 관리자 서비스를 사용하는 방법을 자세히 알아보려면 [StorSimple 장치 관리자 서비스를 사용하여 StorSimple 장치 관리](storsimple-8000-manager-service-administration.md)로 이동하세요.

