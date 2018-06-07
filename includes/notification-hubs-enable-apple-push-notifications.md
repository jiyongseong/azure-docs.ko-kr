---
title: 포함 파일
description: 포함 파일
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/11/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 08ff4b2190b26471d7b1ac1850ce89f889b8c256
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
## <a name="generate-the-certificate-signing-request-file"></a>인증서 서명 요청 파일 생성
APNS(Apple Push Notification Service)는 인증서를 사용하여 푸시 알림을 인증합니다. 알림을 보내고 받는 데 필요한 푸시 인증서를 만들려면 다음 지침을 따르세요. 이러한 개념에 대한 자세한 내용은 [Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?LinkId=272584) 공식 설명서를 참조하세요.

서명된 푸시 인증서를 생성하기 위해 Apple에서 사용하는 CSR(인증서 서명 요청) 파일을 생성합니다.

1. Mac에서 Keychain Access 도구를 실행합니다. **Utilities** 폴더 또는 **Other** 폴더에서 열 수 있습니다.
2. **Keychain Access**를 클릭하고 **Certificate Assistant**를 확장한 다음 **Request a Certificate from a Certificate Authority...** 를 클릭합니다.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)
3. **User Email Address**와 **Common Name**을 선택하고, **Saved to disk**가 선택되어 있는지 확인한 후 **Continue**를 클릭합니다. **CA Email Address** 필드는 필요하지 않으므로 비워둡니다.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)
4. **Save As**에 CSR(인증서 서명 요청) 파일의 이름을 입력하고, **Where**에서 위치를 선택한 후 **Save**를 클릭합니다.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)
   
      이 작업은 CSR 파일을 선택한 위치에 저장합니다. 기본 위치는 바탕 화면입니다. 이 파일에 대해 선택한 위치를 기억해 두세요.

다음으로 Apple에 앱을 등록하고, 푸시 알림을 사용하도록 설정하고, 내보낸 CSR을 업로드하여 푸시 인증서를 만듭니다.

## <a name="register-your-app-for-push-notifications"></a>푸시 알림에 대해 앱 등록
iOS 앱으로 푸시 알림을 보내려면 Apple에 응용 프로그램을 등록하고 푸시 알림도 등록해야 합니다.  

1. 아직 앱을 등록하지 않은 경우 Apple Developer Center의 <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a>로 이동하여 Apple ID로 로그온하고 **Identifiers**와 **App IDs**를 클릭한 다음에 **+** 기호를 클릭하여 새 앱을 등록합니다.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)
      
2. 새 앱에 다음과 같은 세 개의 필드를 업데이트한 다음 **Continue**를 클릭합니다.
   
   * **Name**: **App ID Description** 섹션의 **Name** 필드에서 앱에 대한 설명이 포함된 이름을 입력합니다.
   * **번들 식별자**: **명시적 앱 ID** 섹션에 [앱 배포 가이드](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8)에 설명된 대로 `<Organization Identifier>.<Product Name>` 형식으로 **번들 식별자**를 입력합니다. 사용하는 *Organization Identifier* 및 *Product Name*은 XCode 프로젝트를 만들 때 사용하는 조직 식별자 및 제품 이름과 일치해야 합니다. 다음 스크린샷에서는 조직 식별자로 *NotificationHubs*가 사용되고 제품 이름으로 *GetStarted*가 사용되었습니다. 이 값이 XCode 프로젝트에서 사용하는 값과 일치하면 XCode에서 올바른 게시 프로필을 사용할 수 있습니다. 
   * **Push Notifications**: **App Services** 섹션에서 **Push Notifications** 옵션을 선택합니다.
     
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)
     
      이 작업은 앱 ID를 생성하고 사용자에게 정보 확인을 요청합니다. **등록** 을 클릭하여 새 앱 ID를 확인합니다.
     
      **등록**을 클릭하면 다음 이미지처럼 **등록 완료** 화면이 표시됩니다. **Done**을 클릭합니다.
      
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)


1. 개발자 센터의 앱 ID에서 이전에 만든 앱 ID를 찾아 해당 행을 클릭합니다.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)
   
      앱 ID를 클릭하면 앱 세부 정보가 표시됩니다. 아래쪽에 있는 **Edit** 단추를 클릭합니다.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)
      
2. 화면 아래로 스크롤하여 **Development Push SSL Certificate** 섹션에서 **Create Certificate...** 단추를 클릭합니다.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)
   
      "Add iOS Certificate" 도우미가 표시됩니다.
   
   > [!NOTE]
   > 이 자습서에서는 개발 인증서를 사용합니다. 프로덕션 인증서를 등록할 때에도 동일한 프로세스가 사용됩니다. 알림을 보낼 때 동일한 인증서 유형을 사용하는지만 확인하면 됩니다.
   > 
   > 
3. **Choose File**을 클릭하고, 첫 번째 작업에서 만든 CSR 파일이 저장된 위치로 이동하여 파일을 선택하고, **Generate**를 클릭합니다.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)
4. 포털에서 인증서가 생성되면 **Download** 단추를 클릭하고 **Done**을 클릭합니다.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)
   
      인증서가 다운로드되어 컴퓨터의 다운로드 폴더에 저장됩니다.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)
   
   > [!NOTE]
   > 기본적으로 다운로드된 개발 인증서 파일은 이름이 **aps_development.cer**로 지정됩니다.
   > 
   > 
5. 다운로드한 푸시 인증서 **aps_development.cer**을 두 번 클릭합니다.
   
      이 작업은 다음 이미지에 보이는 것처럼 새 인증서를 Keychain에 설치합니다.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)
   
   > [!NOTE]
   > 인증서의 이름은 다를 수 있지만 **Apple Development iOS Push Services:** 가 앞에 옵니다.   
6. Keychain Access에서는 **인증서** 범주에서 만든 새 푸시 인증서를 마우스 오른쪽 단추로 클릭합니다. **내보내기**를 클릭하고 파일의 이름을 지정한 후 **.p12** 형식을 선택하고 **저장**을 클릭합니다.
   
    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)
   
    내보낸 .p12 인증서의 파일 이름과 위치를 적어둡니다. APNS을 이용한 인증을 사용하도록 설정하는 데 사용됩니다.
   
   > [!NOTE]
   > 이 자습서에서는 QuickStart.p12 파일을 만듭니다. Your file name and location might be different.
   
## <a name="create-a-provisioning-profile-for-the-app"></a>앱용 프로비저닝 프로필 만들기
1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a>로 돌아가서 **Provisioning Profiles**와 **All**을 차례로 선택하고 **+** 단추를 클릭하여 새 프로필을 만듭니다. **Add iOS Provisioning Profile** 마법사가 표시됩니다.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)
2. **Development**에서 프로비저닝 프로필 유형으로 **iOS App Development**를 선택하고 **Continue**를 클릭합니다. 
3. **App ID** 드롭다운 목록에서 이전에 만든 앱 ID를 선택하고 **Continue**를 클릭합니다.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)
4. **Select certificates** 화면에서 코드 서명에 사용되는 일반적인 개발 인증서를 선택하고 **Continue**를 클릭합니다. 이 인증서는 사용자가 만든 푸시 인증서가 아닙니다.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)
5. 테스트에 사용할 **Devices**를 선택하고 **Continue**를 클릭합니다.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)
6. 마지막으로, **Profile Name**에서 프로필의 이름을 선택하고 **Generate**를 클릭합니다.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)
7. 새 프로비전 프로필이 만들어질 때 다운로드를 클릭하고 Xcode 개발 컴퓨터에 설치합니다. **완료**를 클릭합니다.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)
