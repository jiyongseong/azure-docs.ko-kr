---
title: "StorSimple 8000 시리즈용 지원 티켓 또는 사례 만들기 | Microsoft 문서"
description: "StorSimple 8000 시리즈 장치에서 지원 요청을 로깅하고 지원 세션을 시작하는 방법을 알아봅니다."
services: storsimple
documentationcenter: 
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli;
ms.openlocfilehash: e0df86e40d0dc1092ad7ff04f01bbc3e5e3d1c4e
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/10/2018
---
# <a name="contact-microsoft-support"></a>Microsoft 지원에 문의

StorSimple 장치 관리자는 서비스 요약 블레이드 내에서 **새로운 지원 요청을 기록**하는 기능을 제공합니다. StorSimple 솔루션에 문제가 발생하는 경우 기술 지원을 위해 서비스 요청을 할 수 있습니다. 지원 엔지니어와 함께 온라인 세션에서 StorSimple 장치에 지원 세션을 시작해야 할 수도 있습니다. 이 문서에서는 다음을 안내합니다.

* 지원 요청을 만드는 방법
* 포털 내에서 지원 요청 주기를 관리하는 방법
* StorSimple 장치의 Windows PowerShell 인터페이스에서 지원 세션을 시작하는 방법

지원 요청을 만들기 전에 [StorSimple 8000 시리즈 지원 SLA 및 정보](https://msdn.microsoft.com/library/mt433077.aspx) 를 검토하십시오.

## <a name="create-a-support-request"></a>지원 요청 만들기

[지원 계획](https://azure.microsoft.com/support/plans/)에 따라 StorSimple 장치 관리자 서비스 요약 블레이드에서 직접 StorSimple 장치 문제에 대한 지원 티켓을 만들 수 있습니다. 지원 요청을 만들려면 다음 단계를 수행합니다.

#### <a name="to-create-a-support-request"></a>지원 요청을 만들려면

1. StorSimple 장치 관리자 서비스로 이동합니다. 서비스 요약 블레이드 설정에서 **지원 + 문제 해결** 섹션으로 이동한 다음 **새 지원 요청**을 클릭합니다.
     
    ![새 포털을 통한 MS 지원 문의](./media/storsimple-8000-contact-microsoft-support/contactsupport1.png)
   
2. **새 지원 요청 블레이드**에서 **기본**을 선택합니다. **기본** 블레이드에서 다음 단계를 수행합니다.
   1. **문제점 유형** 드롭다운 목록에서 **기술**을 선택합니다.
   2. 현재 **구독**, **서비스** 유형 및 **리소스**(StorSimple 장치 관리자 서비스)가 자동으로 선택됩니다. 
   3. 구독과 관련된 여러 계획이 있는 경우 드롭다운에서 **지원 계획**을 선택합니다. 기술 지원을 사용하도록 설정하기 위해 유료 지원 계획이 필요합니다.
   4. **다음**을 클릭합니다.

       ![새 포털을 통한 MS 지원 문의](./media/storsimple-8000-contact-microsoft-support/contactsupport2.png)

3. **새 지원 요청** 블레이드에서 **2단계 문제**를 선택합니다. **문제** 블레이드에서 다음 단계를 수행합니다.
    
    1. **심각도**를 선택합니다.
    2. 문제가 어플라이언스 또는 StorSimple 장치 관리자 서비스에 관련되어 있는지를 지정합니다.
    3. 이 문제의 **범주**를 선택하고 문제에 대한 추가 **세부 정보**를 제공합니다.
    4. 문제의 시작 날짜 및 시간을 제공합니다.
    5. **파일 업로드**에서 폴더 아이콘을 클릭하여 지원 패키지를 찾아봅니다.
    6. **진단 정보 공유**를 선택합니다.
    7. **다음**을 클릭합니다.

       ![새 포털을 통한 MS 지원 문의](./media/storsimple-8000-contact-microsoft-support/contactsupport3.png) 

4. **새 지원 요청** 블레이드에서 **3단계 연락처 정보**를 클릭합니다. **연락처 정보** 블레이드에서 다음 단계를 수행합니다.

    1. **연락처 옵션**에서 기본 연락 방법(전화 또는 전자 메일) 및 언어를 지정합니다. 응답 시간은 구독 계획에 따라 자동으로 선택됩니다.
    2. 연락처 정보에 이름, 전자 메일, 선택적 연락처, 국가를 지정합니다. **향후 지원 요청에 대한 연락처 변경 내용 저장** 확인란을 선택합니다.
    3. **만들기**를 클릭합니다.
   
        ![새 포털을 통한 MS 지원 문의](./media/storsimple-8000-contact-microsoft-support/contactsupport5.png)   

    Microsoft 기술 지원 서비스는 이 정보를 사용하여 사용자에게 추가 정보, 진단 및 솔루션을 알립니다.
요청을 제출한 후에 지원 엔지니어가 요청을 계속 진행하기 위해 가능한 한 빨리 연락할 것입니다.

## <a name="manage-a-support-request"></a>지원 요청 관리

지원 티켓을 만든 후에는 포털 내에서 티켓의 수명 주기를 관리할 수 있습니다.

#### <a name="to-manage-your-support-requests"></a>지원 요청을 관리하려면

1. 도움말 및 지원 페이지로 이동하려면 **찾아보기 > 도움말 + 지원**으로 이동합니다.

    ![지원 요청 관리](./media/storsimple-8000-contact-microsoft-support/managesupport1.png)

2. 모든 지원 요청을 표시하는 테이블이 **도움말 + 지원** 블레이드에 표시됩니다.

    ![지원 요청 관리](./media/storsimple-8000-contact-microsoft-support/managesupport2.png)

3. 지원 요청을 클릭하여 선택합니다. 이 요청의 상태 및 세부 정보를 볼 수 있습니다. 이 요청에 대해 후속 작업을 수행하려는 경우 **+ 새 메시지**를 클릭합니다.

    ![지원 요청 관리](./media/storsimple-8000-contact-microsoft-support/managesupport3.png)

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>StorSimple용 Windows PowerShell에서 지원 세션 시작

StorSimple 장치에서 발생할 수 있는 문제를 해결하려면 Microsoft 기술 지원 서비스 팀과 연계해야 합니다. Microsoft 기술 지원 서비스 팀은 장치에 로그온하기 위해 지원 세션을 사용해야 합니다.

지원 세션을 시작하려면 다음 단계를 수행합니다.

#### <a name="to-start-a-support-session"></a>지원 세션을 시작하려면

1. 직렬 콘솔을 사용하여 직접 또는 원격 컴퓨터에서 텔넷 세션을 통해 장치에 액세스합니다. 이렇게 하려면 [PuTTY를 사용하여 장치 직렬 콘솔에 연결](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)의 단계를 따르세요.
2. 열린 세션에서 **Enter** 키를 눌러 명령 프롬프트를 엽니다.
3. 직렬 콘솔 메뉴에서 옵션 1, **모든 권한으로 로그인**을 선택합니다.
4. 프롬프트에 다음 암호를 입력합니다.
   
    `Password1`
5. 프롬프트에 다음 명령을 입력합니다.
   
    `Enable-HcsSupportAccess`
6. 암호화된 문자열을 표시합니다. 메모장과 같은 텍스트 편집기에 이 문자열을 복사합니다.
7. 이 문자열을 저장하고 Microsoft 기술 지원 서비스에 전자 메일 메시지로 보냅니다.

> [!IMPORTANT]
> `Disable-HcsSupportAccess`를 실행하여 지원 액세스를 비활성화할 수 있습니다. StorSimple 장치는 세션이 시작된 8시간 후에 지원 액세스를 비활성화하려고 합니다. 지원 세션을 시작한 후에 StorSimple 장치 자격 증명을 변경하는 것이 좋습니다.


## <a name="next-steps"></a>다음 단계

[StorSimple 8000 시리즈 장치와 관련된 문제를 진단하고 해결](storsimple-8000-troubleshoot-deployment.md)하는 방법 알아보기
