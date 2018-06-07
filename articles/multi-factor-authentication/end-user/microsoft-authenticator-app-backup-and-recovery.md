---
title: Microsoft Authenticator를 사용하여 정보 백업 및 복구 - Azure | Microsoft Docs
description: Microsoft Authenticator 앱을 사용하여 계정 자격 증명을 백업 및 복구하는 방법을 알아봅니다.
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: lizross
ms.reviewer: olhaun
ms.custom: end-user
ms.openlocfilehash: e25ccdad5285bfaa96f538aca415746942523d85
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33896268"
---
# <a name="backup-and-recover-account-credentials-with-the-microsoft-authenticator-app"></a>Microsoft Authenticator 앱을 사용하여 계정 자격 증명 백업 및 복구
**적용 대상:**

- iOS 장치

Microsoft Authenticator 앱은 계정 자격 증명과 계정 순서 같은 관련 앱 설정을 클라우드에 백업합니다. 백업 후에는 앱을 사용하여 정보를 새 장치에 복구하면 계정이 잠기거나 계정을 다시 만들어야 하는 문제를 피할 수 있습니다.

>[!IMPORTANT]
> 각 백업 저장소 위치에 개인 Microsoft 계정 하나와 iCloud 계정 하나가 필요합니다. 하지만 해당 저장 위치 내에서 여러 계정을 백업할 수 있습니다. 예를 들어 개인 계정, 학교 계정 그리고 Facebook, Google 등의 타사 계정을 사용할 수 있습니다.<br><br>사용자의 개인 및 타사 계정 자격 증명만 저장되며, 여기에는 신원을 증명하는 데 필요한 사용자 이름 및 계정 확인 코드가 포함됩니다. 이메일 또는 파일을 포함하여 계정에 연결된 기타 정보는 저장되지 않습니다. 또한 계정을 어떤 방식으로든, 어떤 제품 또는 서비스에도 연결하거나 공유하지 않습니다. 마지막으로, IT 관리자는 이러한 계정에 대한 정보를 얻을 수 없습니다.

## <a name="back-up-your-account-credentials"></a>계정 자격 증명 백업
자격 증명을 백업하려면 다음 두 가지가 필요합니다.

- 복구 계정 역할을 할 개인 [Microsoft 계정](https://account.microsoft.com/account).

- 실제 저장소 위치로 사용할 [iCloud 계정](https://www.icloud.com/). 

두 계정에 모두 로그인하도록 요구하면 백업 정보의 보안이 더욱 강화됩니다.

**클라우드 백업을 켜려면**
-   iOS 장치에서 **설정**을 선택하고, **백업**을 선택하고, **자동 백업**을 켭니다.

    계정 자격 증명이 iCloud 계정에 백업됩니다.

    ![자동 백업 위치 설정을 보여주는 iOS 설정 화면](./media/authenticator-app-backup-and-recovery/backup-and-recovery-turn-on.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>계정 자격 증명을 새 장치에 복구
정보를 백업할 때 사용한 것과 동일한 Microsoft 복구 계정을 사용하여 iCloud 계정으로 계정 자격 증명을 복구할 수 있습니다.

**정보를 복구하려면**
1.  iOS 장치에서 Microsoft Authenticator 앱을 열고 화면 아래쪽에서 **복구 시작**을 선택합니다.

    ![복구 시작을 클릭하는 위치를 보여주는 Microsoft Authenticator 앱](./media/authenticator-app-backup-and-recovery/backup-and-recovery-begin-recovery.png)

2.  백업 프로세스에서 사용한 것과 동일한 개인 Microsoft 계정을 사용하여 복구 계정으로 로그인합니다.

    계정 자격 증명이 새 장치에 복구됩니다.

복구를 마친 후 Microsoft Authenticator 앱의 개인 Microsoft 계정 확인 코드가 기존 장치와 새 장치 간에 다를 수 있습니다. 장치마다 고유의 자격 증명이 있기 때문에 코드가 다르지만, 연결된 전화를 사용하여 로그인할 때 둘 다 유효하며 정상적으로 작동합니다.

## <a name="recover-additional-accounts-requiring-more-verification"></a>추가 계정을 복구하려면 추가 확인이 필요
개인, 회사 또는 학교 계정에 푸시 알림을 사용하는 경우 정보를 복구하려면 추가 확인을 제공해야 한다는 내용의 경고가 화면에 표시됩니다. 푸시 알림은 사용자의 특정 장치에 연결된 자격 증명을 사용해야 하고 어떤 경우에도 네트워크를 통해 전송되지 않으므로 장치에 자격 증명을 만들려면 사용자의 신분을 증명해야 합니다.

개인 Microsoft 계정의 경우 대체 이메일 또는 전화 번호와 함께 암호를 입력하여 신분을 증명할 수 있습니다. 회사 또는 학교 계정의 경우 계정 공급자가 제공한 QR 코드를 검사해야 합니다.

**개인 계정에 대한 추가 확인을 제공하려면**
1.  Microsoft Authenticator 앱의 **계정** 화면에서 복구하려는 계정 옆에 있는 드롭다운 화살표를 선택합니다.

    ![연결된 드롭다운 화살표가 있는 사용 가능한 계정을 보여주는 Microsoft Authenticator 앱](./media/authenticator-app-backup-and-recovery/backup-and-recovery-arrow.png)

2.  **복구하려면 로그인**을 선택하고, 암호를 입력한 다음, 추가 확인으로 이메일 주소 또는 전화 번호를 확인합니다.

    ![로그인 정보를 입력할 수 있는 Microsoft Authenticator 앱](./media/authenticator-app-backup-and-recovery/backup-and-recovery-sign-in.png)

**회사 또는 학교 계정에 대한 추가 확인을 제공하려면**
1.  Microsoft Authenticator 앱의 **계정** 화면에서 복구하려는 계정 옆에 있는 드롭다운 화살표를 선택합니다.

    ![연결된 드롭다운 화살표가 있는 사용 가능한 계정을 보여주는 Microsoft Authenticator 앱](./media/authenticator-app-backup-and-recovery/backup-and-recovery-additonal-accts.png)

2.  **복구하려면 QR 코드 검사**을 선택한 다음, 관리자가 제공한 QR 코드를 검사합니다.

    ![QR 코드를 검사할 수 있는 Microsoft Authenticator 앱](./media/authenticator-app-backup-and-recovery/backup-and-recovery-scan-qr-code.png)

## <a name="troubleshooting-backup-and-recovery-problems"></a>백업 및 복구 문제 해결
백업을 사용할 수 없는 몇 가지 이유가 있습니다.

-   **운영 체제 변경.** 백업은 사용자 휴대폰의 운영 체제에서 제공하는 클라우드 저장소 옵션에 저장되며, Android에서 iOS로 또는 그 반대로 전환할 경우 백업을 사용할 수 없다는 뜻입니다. 이 경우 앱 내에서 수동으로 계정을 다시 만들어야 합니다.

-   **네트워크 또는 암호 문제.** 마지막 iPhone에서 사용한 것과 동일한 AppleID를 사용하여 네트워크에 연결하고 iCloud 계정에 로그인되었는지 확인합니다.

-   **실수로 삭제.** 이전 장치에서 또는 클라우드 저장소 계정을 관리하는 동안 백업 계정을 삭제했을 가능성이 있습니다. 이 경우 앱 내에서 수동으로 계정을 다시 만들어야 합니다.

-   **기존 Microsoft Authenticator 계정.** Microsoft Authenticator 앱에서 이미 계정을 설정한 경우 앱이 백업된 계정을 복구할 수 없습니다. 계정을 복구하지 못하게 하는 이유는 계정 세부 정보를 오래된 정보로 덮어쓰지 않게 방지하기 위함입니다. 이 경우 Authenticator 앱의 기존 계정에서 기존 계정 정보를 모두 제거해야 백업을 복구할 수 있습니다.

## <a name="next-steps"></a>다음 단계
계정 자격 증명을 백업하고 새 장치에 복구했으니, 계속해서 Microsoft Authenticator 앱을 사용하여 ID를 확인할 수 있습니다.

## <a name="related-topics"></a>관련된 항목
- [Microsoft Authenticator 앱 시작](microsoft-authenticator-app-how-to.md)  

- [휴대폰으로 로그인](microsoft-authenticator-app-phone-signin-faq.md)

- [Microsoft Authenticator 앱 FAQ](microsoft-authenticator-app-faq.md)

- [Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/)
