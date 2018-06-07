---
title: "Azure에서 StorSimple 8000 장치 관리자 서비스에 새 인증 사용 | Microsoft Docs"
description: "서비스에 AAD 기반 인증을 사용하고, 새 등록 키를 생성하고, 장치의 수동 등록을 수행하는 방법을 설명합니다."
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
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: 37f44538d94ed78509bbcb09e726dc34a9e92e95
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2018
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>StorSimple에 새 인증 사용

## <a name="overview"></a>개요

StorSimple Device Manager 서비스는 Microsoft Azure에서 실행되며 여러 StorSimple 장치에 연결됩니다. 현재까지 StorSimple 장치 관리자 서비스는 ACS(Access Control Service)를 사용하여 StorSimple 장치에 대한 서비스를 인증했습니다. ACS 메커니즘은 조만간 사용되지 않고 AAD(Azure Active Directory) 인증으로 대체될 예정입니다. 자세한 내용은 ACS 사용 중단 및 AAD 인증 사용에 대한 다음 공지 사항을 참조하세요.

- [Azure ACS의 미래는 Azure Active Directory입니다.](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/12/the-future-of-azure-acs-is-azure-active-directory/)
- [Microsoft Access Control Service의 향후 변경 사항](https://azure.microsoft.com/en-in/blog/acs-access-control-service-namespace-creation-restriction/)

이 문서에서는 StorSimple 장치에 적용할 수 있는 방화벽 규칙에 대한 수정과 AAD 인증 및 연결된 신규 서비스 등록 키에 대해 자세히 설명합니다. 이 문서에 포함된 정보는 StorSimple 8000 시리즈 장치에만 적용됩니다.

AAD 인증은 업데이트 5 이상을 실행하는 StorSimple 8000 시리즈 장치에서 발생합니다. AAD 인증 도입으로 인해 다음과 항목이 변경됩니다.

- 방화벽 규칙에 대한 URL 패턴
- 서비스 등록 키

이러한 변경 사항은 다음 섹션에서 자세히 설명합니다.

## <a name="url-changes-for-aad-authentication"></a>AAD 인증에 대한 URL 변경

서비스에서 AAD 기반 인증을 사용하도록 보장하려면 모든 사용자가 새 인증 URL을 방화벽 규칙에 포함시켜야 합니다.

StorSimple 8000 시리즈를 사용하는 경우에는 방화벽 규칙에 다음 URL이 포함되어 있는지 확인합니다.

| URL 패턴                         | 클라우드 | 구성 요소/기능         |
|------------------------------------|-------|----------------------------------|
| `https://login.windows.net`        | Azure 공용 |AAD 인증 서비스      |
| `https://login.microsoftonline.us` | 미국 정부 |AAD 인증 서비스      |

StorSimple 8000 시리즈 장치 URL 패턴의 전체 목록은 [방화벽 규칙에 대한 URL 패턴](storsimple-8000-system-requirements.md#url-patterns-for-firewall-rules)을 참조하세요.

지원 중단 날짜가 지나도 인증 URL이 방화벽 규칙에 포함되지 않고 장치에서 업데이트 5를 실행하면 사용자에게 URL 경고가 표시됩니다. 사용자는 새 인증 URL을 포함시켜야 합니다. 장치에서 업데이트 5 이전 버전을 실행하는 경우 사용자에게 하트비트 경고가 표시됩니다. 각각의 경우 StorSimple 장치는 서비스로 인증할 수 없으며 서비스는 장치와 통신할 수 없습니다.

## <a name="device-version-and-authentication-changes"></a>장치 버전 및 인증 변경

StorSimple 8000 시리즈 장치를 사용하는 경우 다음 테이블을 사용하여 실행 중인 장치 소프트웨어 버전을 기반으로 수행할 작업을 결정합니다.

| 실행 중인 장치| 수행할 작업                                    |
|--------------------------|------------------------|--------------------|--------------------------------------------------------------|
| 업데이트 5 이상을 실행 중이고 장치가 오프라인 상태입니다. <br> URL이 허용 목록에 없다는 경고가 표시됩니다.| 인증 URL을 포함하도록 방화벽 규칙을 수정합니다.<br> [인증 URL](#url-changes-for-aad-authentication)을 참조하세요. |
| 업데이트 5 이상을 실행 중이고 장치가 온라인 상태입니다.| 별도의 작업이 필요하지 않습니다.                                       |
| 업데이트 4 이상을 실행 중이고 장치가 오프라인 상태입니다. | 인증 URL을 포함하도록 방화벽 규칙을 수정합니다.<br>[카탈로그 서버를 통해 업데이트 5를 다운로드합니다](storsimple-8000-install-update-5.md#download-updates-for-your-device).<br>[핫픽스 메서드를 통해 업데이트 5를 적용합니다](storsimple-8000-install-update-5.md#install-update-5-as-a-hotfix). <br> [서비스에서 AAD 등록 키를 받습니다](#aad-based-registration-keys). <br> [StorSimple 8000 시리즈 장치의 Windows PowerShell 인터페이스에 연결합니다](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). <br>`Redo-DeviceRegistration` cmdlet을 사용하여 Windows PowerShell을 통해 장치를 등록합니다. 이전 단계에서 얻은 키를 제공합니다.|
| 업데이트 4 이상을 실행 중이고 장치가 온라인 상태입니다. |인증 URL을 포함하도록 방화벽 규칙을 수정합니다.<br> Azure Portal을 통해 업데이트 5를 설치합니다.              |
| 업데이트 5 이전 버전으로 공장 재설정.      |장치에서 이전 소프트웨어를 실행하는 동안 포털에 AAD 기반 등록 키가 표시됩니다. 장치에서 업데이트 4 또는 이전 버전을 실행하는 경우 이전 시나리오의 단계를 따릅니다.              |

## <a name="aad-based-registration-keys"></a>AAD 기반 등록 키

StorSimple 8000 시리즈 장치에 대한 업데이트 5부터는 새로운 AAD 기반 등록 키가 사용됩니다. 이 등록 키를 사용하여 StorSimple 장치 관리자 서비스를 장치에 등록합니다.

업데이트 4 또는 이전 버전(현재 활성화된 이전 장치 포함)이 실행되는 StorSimple 8000 시리즈 장치를 사용하는 경우에는 새 AAD 서비스 등록 키를 사용할 수 없습니다.
이 시나리오에서는 서비스 등록 키를 다시 생성해야 합니다. 키를 다시 생성하면 모든 후속 장치를 등록하는 데 새 키가 사용됩니다. 이전 키는 더 이상 유효하지 않습니다.

- 새 AAD 등록 키는 3일 후에 만료됩니다.
- AAD 등록 키는 업데이트 5 이상을 실행하는 StorSimple 8000 시리즈 장치에서만 작동합니다.
- AAD 등록 키는 해당 ACS 등록 키보다 깁니다.

AAD 서비스 등록 키를 생성하려면 다음 단계를 수행합니다.

#### <a name="to-generate-the-aad-service-registration-key"></a>AAD 서비스 등록 키를 생성하려면

1. **StorSimple 장치 관리자**에서 **관리 &gt;** **키**로 이동합니다. 검색 창을 사용하여 _키_를 검색할 수도 있습니다.
    
2. **키 생성**을 클릭합니다.

    ![다시 생성 클릭](./media/storsimple-8000-aad-registration-key/aad-click-generate-registration-key.png)

3. 새 키를 복사합니다. 이전 키는 더 이상 작동하지 않습니다.

    ![다시 생성 확인](./media/storsimple-8000-aad-registration-key/aad-registration-key2.png)

    > [!NOTE] 
    > StorSimple 8000 시리즈 장치에 등록된 서비스에 StorSimple Cloud Appliance를 만드는 경우 만들기를 진행하는 동안 등록 키를 생성하지 마십시오. 만들기가 완료 될 때까지 기다린 다음 등록 키를 생성하십시오.

## <a name="next-steps"></a>다음 단계

* [StorSimple 8000 시리즈 장치](storsimple-8000-deployment-walkthrough-u2.md)를 배포하는 방법을 알아봅니다.

