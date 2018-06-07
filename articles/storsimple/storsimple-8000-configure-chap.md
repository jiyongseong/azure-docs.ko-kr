---
title: StorSimple 8000 시리즈 장치에 대한 CHAP 구성 | Microsoft Docs
description: StorSimple 장치에 CHAP(Challenge Handshake 인증 프로토콜)를 구성하는 방법을 설명합니다.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: TBD
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: c1a558769ffaa52ed2e996a2b537a5ea409101bd
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012661"
---
# <a name="configure-chap-for-your-storsimple-device"></a>StorSimple 장치에 대한 CHAP 구성

이 자습서에서는 StorSimple 장치에 대한 CHAP를 구성하는 방법에 대해 설명합니다. 이 문서에서 설명하는 절차는 StorSimple 8000 시리즈 장치에 적용됩니다.

CHAP는 Challenge Handshake Authentication Protocol의 약어입니다. CHAP는 서버에서 원격 클라이언트의 ID를 확인하는데 사용하는 인증 체계입니다. 확인은 공유 암호 또는 암호를 기반으로 합니다. CHAP는 일방(단방향)이거나 상호적(양방향)일 수 있습니다. 단방향 CHAP는 대상이 초기자를 인증하는 경우입니다. 상호 또는 역방향 CHAP는 대상이 초기자를 인증한 다음 초기자가 대상을 인증해야 합니다. 대상 인증 없이 초기자 인증을 구현할 수 있습니다. 그러나 초기자 인증도 구현하는 경우 대상 인증을 구현할 수 있습니다.

모범 사례로 CHAP를 사용하여 iSCSI 보안을 강화하는 것이 좋습니다.

> [!NOTE]
> IPSEC는 StorSimple 장치에서 현재 지원되지 않습니다.

StorSimple 장치에서 CHAP 설정은 다음과 같은 방법으로 구성할 수 있습니다.

* 단방향 또는 일방 인증
* 양방향 또는 상호 또는 역방향 인증

각 경우에서 장치 포털 및 서버 iSCSI 초기자 소프트웨어를 구성해야 합니다. 이 구성에 대한 자세한 단계는 다음 자습서에 설명되어있습니다.

## <a name="unidirectional-or-one-way-authentication"></a>단방향 또는 일방 인증

단방향 인증에서 대상이 초기자를 인증합니다. 이 인증은 StorSimple 장치의 CHAP 초기자 설정 및 호스트의 iSCSI 초기자 소프트웨어를 구성해야 합니다. StorSimple 장치 및 Windows 호스트에 대한 자세한 절차는 다음과 같습니다.

#### <a name="to-configure-your-device-for-one-way-authentication"></a>단방향 인증에 대한 장치를 구성하려면

1. Azure Portal에서 StorSimple 장치 관리자 서비스로 이동합니다. **장치**를 클릭하고 CHAP를 구성하려는 장치를 클릭하여 선택합니다. **장치 설정 > 보안**으로 이동합니다. **보안 설정** 블레이드에서 **CHAP**를 클릭합니다.
   
    ![CHAP 초기자](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. **CHAP** 블레이드의 **CHAP 초기자** 섹션에서 다음을 수행합니다.
   
   1. CHAP 초기자에 대한 사용자 이름을 입력합니다.
   2. CHAP 초기자에 대한 암호를 입력합니다.
      
    > [!IMPORTANT]
    > CHAP 사용자 이름은 233 미만의 문자를 포함해야 합니다. CHAP 암호는 12 ~ 16 자 사이여야 합니다. 더 긴 사용자 이름이나 암호를 사용하면 Windows 호스트에서 인증 오류가 발생합니다.
   
   3. 암호를 확인합니다.

       ![CHAP 초기자](./media/storsimple-8000-configure-chap/configure-chap6.png)
3. **저장**을 클릭합니다. 확인 메시지가 표시됩니다. **확인** 을 클릭하여 변경 내용을 저장합니다.

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>Windows 호스트 서버에서 일방 인증을 구성하려면
1. Windows 호스트 서버에서 iSCSI 초기자를 시작합니다.
2. **iSCSI 초기자 속성** 창에서 다음 단계를 수행합니다.
   
   1. **검색** 탭을 클릭합니다.
      
       ![iSCSI 초기자 속성](./media/storsimple-configure-chap/IC740944.png)
   2. **포털 검색**을 클릭합니다.
3. **대상 포털 검색** 대화 상자에서 다음을 수행합니다.
   
   1. 장치의 IP 주소를 지정합니다.
   2. **고급**을 클릭합니다.
      
       ![대상 포털 검색](./media/storsimple-configure-chap/IC740945.png)
4. **고급 설정** 대화 상자에서:
   
   1. **CHAP 로그온 사용** 확인란을 선택합니다.
   2. **이름** 필드에는 Azure Portal에서 CHAP 초기자에 지정한 사용자 이름을 입력합니다.
   3. **대상 암호** 필드에는 Azure Portal에서 CHAP 초기자에 지정한 암호를 입력합니다.
   4. **확인**을 클릭합니다.
      
       ![고급 설정 일반](./media/storsimple-configure-chap/IC740946.png)
5. **iSCSI 초기자 속성** 창의 **대상** 탭에서 장치 상태는 **Connected**로 표시되어야 합니다. StorSimple 1200 장치를 사용하는 경우 각 볼륨은 iSCSI 대상으로 탑재됩니다. 따라서 3-4단계는 각 볼륨에 대해 반복해야 할 수 있습니다.
   
    ![별도 대상으로 탑재된 볼륨](./media/storsimple-configure-chap/chap4.png)
   
   > [!IMPORTANT]
   > iSCSI 이름을 변경하는 경우 새 iSCSI 세션에 대한 새 이름이 사용됩니다. 새 설정은 로그오프하고 다시 로그온할 때까지 기존 세션에 대해 다시 사용되지 않습니다.

Windows 호스트 서버에서 CHAP를 구성하는 방법에 대한 자세한 내용을 보려면 [추가 고려 사항](#additional-considerations)으로 이동하세요.

## <a name="bidirectional-or-mutual-authentication"></a>양방향 또는 상호 인증

양방향 인증에서 대상은 초기자를 인증한 다음 초기자는 대상을 인증합니다. 이 절차를 수행하려면 사용자가 CHAP 초기자 설정과 장치의 역방향 CHAP 설정 및 호스트의 iSCSI 초기자 소프트웨어를 구성해야 합니다. 다음 절차는 장치와 Windows 호스트에서 상호 인증을 구성하는 단계를 설명합니다.

#### <a name="to-configure-your-device-for-mutual-authentication"></a>상호 인증에 대한 장치를 구성하려면

1. Azure Portal에서 StorSimple 장치 관리자 서비스로 이동합니다. **장치**를 클릭하고 CHAP를 구성하려는 장치를 클릭하여 선택합니다. **장치 설정 > 보안**으로 이동합니다. **보안 설정** 블레이드에서 **CHAP**를 클릭합니다.
   
    ![CHAP 대상](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. 이 페이지에서 아래로 스크롤하고 **CHAP 대상** 섹션에서 다음을 수행합니다.
   
   1. 장치에 대한 **역방향 CHAP 사용자 이름** 을 입력합니다.
   2. 장치에 대한 **역방향 CHAP 암호** 를 입력합니다.
   3. 암호를 확인합니다.
3. **CHAP 초기자** 섹션에서 다음을 수행합니다.
   
   1. 장치에 대한 **사용자 이름** 을 입력합니다.
   2. 장치에 대한 **암호** 를 입력합니다.
   3. 암호를 확인합니다.

       ![CHAP 초기자](./media/storsimple-8000-configure-chap/configure-chap11.png)
4. **저장**을 클릭합니다. 확인 메시지가 표시됩니다. **확인** 을 클릭하여 변경 내용을 저장합니다.

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>Windows 호스트 서버에서 양방향 인증을 구성하려면

1. Windows 호스트 서버에서 iSCSI 초기자를 시작합니다.
2. **iSCSI 초기자 속성** 창에서 **구성** 탭을 클릭합니다.
3. **CHAP**를 클릭합니다.
4. **iSCSI 초기자 상호 CHAP 암호** 대화 상자에서 다음을 수행합니다.
   
   1. Azure Portal에서 구성한 **역방향 CHAP 암호**를 입력합니다.
   2. **확인**을 클릭합니다.
      
       ![iSCSI 초기자 상호 CHAP 암호](./media/storsimple-configure-chap/IC740949.png)
5. **대상** 탭을 클릭합니다.
6. **연결** 단추를 클릭합니다. 
7. **대상에 연결** 대화 상자에서 **고급**을 클릭합니다.
8. **고급 속성** 대화 상자에서 다음을 수행합니다.
   
   1. **CHAP 로그온 사용** 확인란을 선택합니다.
   2. **이름** 필드에는 Azure Portal에서 CHAP 초기자에 지정한 사용자 이름을 입력합니다.
   3. **대상 암호** 필드에는 Azure Portal에서 CHAP 초기자에 지정한 암호를 입력합니다.
   4. **상호 인증 수행** 확인란을 선택합니다.
      
       ![고급 설정 상호 인증](./media/storsimple-configure-chap/IC740950.png)
   5. **확인** 을 클릭하여 CHAP 구성을 완료합니다.

Windows 호스트 서버에서 CHAP를 구성하는 방법에 대한 자세한 내용을 보려면 [추가 고려 사항](#additional-considerations)으로 이동하세요.

## <a name="additional-considerations"></a>추가 고려 사항

**빠른 연결** 기능은 CHAP를 사용할 수 있는 연결을 지원하지 않습니다. CHAP를 사용하도록 설정한 경우 **대상** 탭에 있는 **연결** 단추를 사용하여 대상에 연결해야 합니다.

![대상에 연결](./media/storsimple-configure-chap/IC740947.png)

표시되는 **대상에 연결** 대화 상자에서 **즐겨찾는 대상 목록에 이 연결 추가** 확인란을 선택합니다. 이렇게 선택하면 컴퓨터를 다시 시작할 때마다 iSCSI 즐겨찾는 대상에 연결을 복원하는 시도가 수행됩니다.

## <a name="errors-during-configuration"></a>구성 중 오류

CHAP 구성이 올바르지 않은 경우 **인증 실패** 오류 메시지가 표시될 수 있습니다.

## <a name="verification-of-chap-configuration"></a>CHAP 구성 확인

다음 단계를 완료하여 CHAP가 사용되고 있는지 확인할 수 있습니다.

#### <a name="to-verify-your-chap-configuration"></a>CHAP 구성을 확인하려면
1. **즐겨찾는 대상**을 클릭합니다.
2. 인증을 사용하도록 설정한 대상을 선택합니다.
3. **세부 정보**를 클릭합니다.
   
    ![iSCSI 초기자 속성 즐겨찾는 대상](./media/storsimple-configure-chap/IC740951.png)
4. **즐겨찾는 대상 세부 정보** 대화 상자에서 **인증** 필드에 항목을 입력합니다. 구성이 성공하면 **CHAP**라는 텍스트가 표시되어야 합니다.
   
    ![즐겨찾는 대상 세부 정보](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>다음 단계

* [StorSimple 보안](storsimple-8000-security.md)에 대해 자세히 알아봅니다.
* [StorSimple 장치 관리자 서비스를 사용하여 StorSimple 장치를 관리하는 방법](storsimple-8000-manager-service-administration.md)에 대해 자세히 알아봅니다.

