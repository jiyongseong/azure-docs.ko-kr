---
title: "StorSimple 8000 시리즈 장치에 대한 웹 프록시 설정 | Microsoft Docs"
description: "StorSimple용 Windows PowerShell을 사용하여 StorSimple 장치에 대한 웹 프록시 설정을 구성하는 방법을 알아봅니다."
services: storsimple
documentationcenter: 
author: alkohli
manager: 
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: alkohli
ms.openlocfilehash: 1109e44ed9c6aa8a0f7305b8a50410316711589c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-web-proxy-for-your-storsimple-device"></a>StorSimple 장치에 대한 웹 프록시 구성

## <a name="overview"></a>개요

이 자습서에서는 StorSimple용 Windows PowerShell을 사용하여 StorSimple 장치에 대한 웹 프록시 설정을 구성하고 보는 방법을 설명합니다. 클라우드와 통신할 때 StorSimple 장치에서 웹 프록시 설정을 사용합니다. 웹 프록시 서버를 사용하여 보안, 필터 콘텐츠, 캐시의 다른 계층을 추가함으로써 대역폭 요구 사항 또는 분석을 돕습니다.

이 자습서의 지침은 StorSimple 8000 시리즈 물리적 장치에만 적용됩니다. 웹 프록시 구성은 StorSimple Cloud Appliance(8010 및 8020)에서 지원되지 않습니다.

웹 프록시는 StorSimple 장치에 대한 _선택적_ 구성입니다. StorSimple용 Windows PowerShell을 통해서만 웹 프록시를 구성할 수 있습니다. 구성은 다음과 같은 2단계 프로세스입니다.

1. 먼저 StorSimple cmdlet용 설치 마법사 또는 Windows PowerShell을 통해 웹 프록시 설정을 구성합니다.
2. 그런 다음 StorSimple cmdlet용 Windows PowerShell을 통해 구성된 웹 프록시 설정을 사용합니다.

웹 프록시 구성을 완료한 후에 Microsoft Azure StorSimple 장치 관리자 서비스 및 StorSimple용 Windows PowerShell에서 구성된 웹 프록시 설정을 볼 수 있습니다.

이 자습서를 읽은 후에 다음을 수행할 수 있습니다.

* 설치 마법사 및 cmdlet을 사용하여 웹 프록시를 구성합니다.
* Cmdlet을 사용하여 웹 프록시를 사용하도록 설정합니다.
* Azure Portal에서 웹 프록시 설정을 봅니다.
* 웹 프록시를 구성하는 동안 오류 문제를 해결합니다.


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>StorSimple용 Windows PowerShell을 통해 웹 프록시 구성

웹 프록시 설정을 구성하려면 다음 중 하나를 사용합니다.

* 설치 마법사는 구성 단계를 안내합니다.
* StorSimple용 Windows PowerShell에서 Cmdlet

이러한 각 메서드는 다음 섹션에서 설명합니다.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>설치 마법사를 통해 웹 프록시 구성

웹 프록시를 구성하기 위한 단계를 안내하는 설치 마법사를 사용합니다. 다음 단계를 수행하여 장치에 웹 프록시를 구성합니다.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>설치 마법사를 통해 웹 프록시를 구성하려면

1. 직렬 콘솔 메뉴에서 옵션 1, **모든 권한으로 로그인**을 선택하고 **장치 관리자 암호**를 제공합니다. 설치 마법사 세션을 시작하려면 다음 명령을 입력합니다.
   
    `Invoke-HcsSetupWizard`
2. 처음으로 장치 등록을 위해 설치 마법사를 사용하면 웹 프록시를 구성할 때까지 모든 필요한 네트워크 설정을 구성해야 합니다. 장치가 이미 등록되어 있다면 웹 프록시를 구성할 때까지 모든 구성된 네트워크 설정을 적용합니다. 설치 마법사에서 웹 프록시 설정을 구성하는 메시지가 표시되면 **예**를 입력합니다.
3. **웹 프록시 URL**에 대해 웹 프록시 서버의 IP 주소 또는 정규화된 도메인 이름(FQDN) 및 클라우드와 통신할 때 사용하려는 장치인 TCP 포트 번호 장치를 지정합니다. 이때 다음 형식을 사용합니다.
   
    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`
   
    기본적으로 TCP 포트 번호 8080가 지정됩니다.
4. 인증 유형으로 **NTLM**, **기본** 또는 **없음**을 선택합니다. 기본은 프록시 서버 구성에 대한 최소한의 보안 인증입니다. NT LAN 관리자(NTLM)는 3방향 메시징 시스템을 사용(추가 무결성이 필요하면 4방향)하여 사용자를 인증하는 안전하고 복잡한 인증 프로토콜입니다. 기본 인증은 NTLM입니다. 자세한 내용은 [기본](http://hc.apache.org/httpclient-3.x/authentication.html) 및 [NTLM 인증](http://hc.apache.org/httpclient-3.x/authentication.html)을 참조하세요. 
   
   > [!IMPORTANT]
   > **StorSimple 장치 관리자 서비스에서는 해당 장치에 대한 프록시 서버 구성에서 기본 또는 NTLM 인증이 사용되면 장치 모니터링 차트가 실행되지 않습니다. 작업할 모니터링 차트의 경우 인증이 NONE으로 설정되어 있는지 확인해야 합니다.**
  
5. 인증을 사용하도록 설정한 경우 **웹 프록시 사용자 이름** 및 **웹 프록시 암호**를 제공합니다. 또한 암호를 확인해야 합니다.
   
    ![StorSimple 장치1에서 웹 프록시 구성](./media/storsimple-configure-web-proxy/IC751830.png)

처음으로 장치를 등록하는 경우 등록을 계속합니다. 이미 장치가 등록된 경우 마법사가 종료됩니다. 구성된 설정은 저장됩니다.

이제 웹 프록시를 사용하도록 설정되었습니다. [웹 프록시를 사용하도록 설정](#enable-web-proxy) 단계를 건너뛰고 [Azure Portal에서 웹 프록시 설정 보기](#view-web-proxy-settings-in-the-azure-portal)로 바로 이동할 수 있습니다.

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>StorSimple cmdlet용 Windows PowerShell을 통해 웹 프록시 구성

웹 프록시 설정을 구성하는 다른 방법은 StorSimple cmdlet용 Windows PowerShell을 통한 방법입니다. 다음 단계를 수행하여 웹 프록시를 구성합니다.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>cmdlet를 통해 웹 프록시를 구성하려면
1. 직렬 콘솔 메뉴에서 옵션 1, **모든 권한으로 로그인**을 선택합니다. 메시지가 표시되면 **장치 관리자 암호**를 제공합니다. 기본 암호는 `Password1`입니다.
2. 명령 프롬프트에 다음을 입력합니다.
   
    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`
   
    메시지가 표시되면 암호를 제공하고 확인합니다.
   
    ![StorSimple 장치3에서 웹 프록시 구성](./media/storsimple-configure-web-proxy/IC751831.png)

웹 프록시를 구성하고 사용할 수 있어야 합니다.

## <a name="enable-web-proxy"></a>웹 프록시 활성화

웹 프록시는 기본적으로 사용하지 않도록 설정되어 있습니다. StorSimple 장치에서 웹 프록시 설정을 구성한 후에 StorSimple용 Windows PowerShell을 사용하여 웹 프록시 설정을 사용하도록 설정합니다.

> [!NOTE]
> **설치 마법사를 사용하여 웹 프록시를 구성하는 경우 이 단계가 필요하지 않습니다. 웹 프록시는 설치 마법사 세션 후 기본적으로 자동으로 사용하도록 설정됩니다.**


장치에서 웹 프록시를 사용하려면 StorSimple용 Windows PowerShell에서 다음 단계를 수행합니다.

#### <a name="to-enable-web-proxy"></a>웹 프록시를 활성화하려면
1. 직렬 콘솔 메뉴에서 옵션 1, **모든 권한으로 로그인**을 선택합니다. 메시지가 표시되면 **장치 관리자 암호**를 제공합니다. 기본 암호는 `Password1`입니다.
2. 명령 프롬프트에 다음을 입력합니다.
   
    `Enable-HcsWebProxy`
   
    StorSimple 장치에서 웹 프록시 구성을 사용하도록 설정합니다.
   
    ![StorSimple 장치4에서 웹 프록시 구성](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-portal"></a>Azure Portal에서 웹 프록시 설정 보기

웹 프록시 설정은 Windows PowerShell 인터페이스를 통해 구성하며 포털에서 변경할 수 없습니다. 하지만 포털에서 이러한 구성된 설정을 볼 수는 있습니다. 다음 단계를 수행하여 웹 프록시를 봅니다.

#### <a name="to-view-web-proxy-settings"></a>웹 프록시 설정을 보려면
1. **StorSimple 장치 관리자 서비스 > 장치**로 이동합니다. 장치를 선택하여 클릭하고 **장치 설정 > 네트워크**로 이동합니다.

    ![네트워크 클릭](./media/storsimple-8000-configure-web-proxy/view-web-proxy-1.png)

2. **네트워크 설정** 블레이드에서 **웹 프록시** 타일을 클릭합니다.

    ![웹 프록시 클릭](./media/storsimple-8000-configure-web-proxy/view-web-proxy-2.png)

3. **웹 프록시** 블레이드에서 StorSimple 장치에 구성된 웹 프록시 설정을 검토합니다.
   
    ![웹 프록시 설정 보기](./media/storsimple-8000-configure-web-proxy/view-web-proxy-3.png)


## <a name="errors-during-web-proxy-configuration"></a>웹 프록시 구성하는 동안 오류

웹 프록시 설정이 제대로 구성되지 않으면 StorSimple용 Windows PowerShell에서 사용자에게 오류 메시지가 표시됩니다. 다음 테이블에서 이러한 오류 메시지, 가능한 원인 및 권장되는 작업 중 일부를 설명합니다.

| 일련 번호 | HRESULT 오류 코드 | 가능한 근본 원인 | 권장 작업 |
|:--- |:--- |:--- |:--- |
| 1. |0x80070001 |명령은 수동 컨트롤러에서 실행되고 활성 컨트롤러와 통신할 수 없습니다. |활성 컨트롤러에서 이 명령을 실행합니다. 수동 컨트롤러에서 명령을 실행하려면 수동에서 활성 컨트롤러로 연결을 수정해야 합니다. 이 연결이 끊어진 경우 Microsoft 지원과 연계해야 합니다. |
| 2. |0x800710dd - 작업 식별자가 유효하지 않습니다. |프록시 설정은 StorSimple Cloud Appliance에서 지원되지 않습니다. |프록시 설정은 StorSimple Cloud Appliance에서 지원되지 않습니다. 물리적 StorSimple 장치에만 구성할 수 있습니다. |
| 3. |0x80070057 - 잘못된 매개 변수 |프록시 설정에 대해 제공된 매개 변수 중 하나가 잘못되었습니다. |URI는 올바른 형식으로 제공되지 않습니다. 다음 형식을 사용하세요. `http://<IP address or FQDN of the web proxy server>:<TCP port number>` |
| 4. |0x800706ba - RPC 서버를 사용할 수 없음 |근본 원인은 다음 중 하나입니다.</br></br>클러스터가 켜지지 않았습니다. </br></br>데이터 경로 서비스가 실행되고 있지 않습니다.</br></br>명령은 수동 컨트롤러에서 실행되고 활성 컨트롤러와 통신할 수 없습니다. |Microsoft 지원과 연계하여 클러스터가 작동하고 데이터 경로 서비스가 실행 중인지 확인합니다.</br></br>활성 컨트롤러에서 명령을 실행합니다. 수동 컨트롤러에서 명령을 실행하려는 경우 수동 컨트롤러가 활성 컨트롤러와 통신할 수 있는지 확인해야 합니다. 이 연결이 끊어진 경우 Microsoft 지원과 연계해야 합니다. |
| 5. |0x800706be - RPC 호출 실패 |클러스터의 작동이 중단되었습니다. |Microsoft 지원과 연계하여 클러스터가 작동하는지 확인합니다. |
| 6. |0x8007138f - 클러스터 리소스를 찾을 수 없음 |플랫폼 서비스 클러스터 리소스를 찾을 수 없습니다. 설치가 올바르지 않은 경우 발생할 수 있습니다. |장치에서 공장 재설정을 수행해야 합니다. 플랫폼 리소스를 만들어야 할 수 있습니다. Microsoft 지원에 다음 단계를 문의합니다. |
| 7. |0x8007138c - 클러스터 리소스는 온라인 상태가 아님 |플랫폼 또는 데이터 경로 클러스터 리소스는 온라인 상태가 아닙니다. |Microsoft 지원에 문의하여 데이터 경로 및 플랫폼 서비스 리소스가 온라인 상태인지 확인합니다. |

> [!NOTE]
> * 위의 오류 메시지 목록은 전체 목록이 아닙니다.
> * 웹 프록시 설정에 관련된 오류는 StorSimple 장치 관리자 서비스의 Azure Portal에 나타나지 않습니다. 구성이 완료된 후에 웹 프록시에 문제가 있다면 클래식 포털에서 장치 상태가 **오프라인** 으로 변경됩니다. |

## <a name="next-steps"></a>다음 단계
* 장치를 배포하거나 웹 프록시 설정을 구성하는 동안 문제가 발생하면 [StorSimple 장치 배포 문제 해결](storsimple-troubleshoot-deployment.md)을 참조하세요.
* StorSimple 장치 관리자 서비스를 사용하는 방법을 알아보려면 [StorSimple 장치 관리자 서비스를 사용하여 StorSimple 장치 관리](storsimple-8000-manager-service-administration.md)로 이동하세요.

