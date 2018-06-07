---
title: "Azure MFA에서 앱 암호를 사용하는 방법 | Microsoft Docs"
description: "이 페이지는 사용자가 앱 암호란 무엇이며 Azure MFA와 관련해서 암호가 어떤 용도로 사용되는지를 이해하는 데 도움이 됩니다."
services: multi-factor-authentication
documentationcenter: 
author: barlanmsft
manager: mtillman
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: 0812719ddee8c0ff0c2fa9256c2819611692dfe5
ms.sourcegitcommit: 7d4b3cf1fc9883c945a63270d3af1f86e3bfb22a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2018
---
# <a name="what-are-app-passwords-in-azure-multi-factor-authentication"></a>Azure Multi-factor Authentication에서 앱 암호란 무엇인가요?
Exchange Active Sync를 사용하는 Apple 네이티브 메일 클라이언트와 같은 특정 비브라우저 앱은 현재 다단계 인증을 지원하지 않습니다. 다단계 인증은 사용자 기준으로 사용되도록 설정됩니다.  즉, 다음과 같은 경우 사용자가 다단계 인증을 사용할 수 없습니다.

- 사용자가 다단계 인증을 사용하도록 설정되어 있습니다.
- 사용자가 비브라우저 앱을 사용하려고 시도합니다.

사용자는 앱 암호를 통해 앱을 사용할 수 있습니다.

앱 암호를 만든 후 이러한 비브라우저 앱에서 원래 암호 대신 사용할 수 있습니다. 2단계 인증에 등록하면 Microsoft는 누군가가 두 번째 인증을 수행할 수 없으면 사용자의 암호로도 로그인하지 못하도록 합니다. 휴대폰의 Apple 네이티브 메일 클라이언트는 2단계 인증을 요청할 수 없기 때문에 로그인할 수 없습니다. 이 문제에 해결책은 일상적으로 사용하지 않는 좀 더 안전한 앱 암호를 만드는 것입니다. 앱 암호는 2단계 인증을 지원하지 않는 그러한 앱을 위한 것입니다. 앱에서 다단계 인증을 우회하여 계속 작동하도록 앱 암호를 사용합니다.


> [!NOTE]
> Outlook을 포함한 Office 2013 클라이언트는 새로운 인증 프로토콜을 지원하며 2단계 인증과 함께 사용할 수 있습니다. Office 2013 클라이언트는 앱 암호를 사용할 필요가 없습니다.  자세한 내용은 [발표된 Office 2013 최신 인증 공개 미리 보기](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)를 참조하세요.


## <a name="how-to-use-app-passwords"></a>앱 암호를 사용하는 방법
다음은 앱 암호에 대해 알아야 할 중요 사항입니다.

* 사용자 고유의 앱 암호를 만들지 않습니다. 자동으로 생성됩니다.
* 현재 사용자 당 40개의 암호로 제한되어 있습니다.
* 앱 암호를 제한에 도달한 후에 만들려고 하면 기존 앱 암호 중 하나를 삭제해야 새로 만들 수 있습니다.
* 응용 프로그램 단위가 아니라 장치별로 앱 암호 하나만 사용합니다. 예를 들어 노트북에 사용할 앱 암호를 하나 만든 다음 노트북의 모든 응용 프로그램에 이 앱 암호를 사용할 수 있습니다. 그런 다음 바탕 화면에 있는 모든 앱에 사용할 두 번째 앱 암호를 만듭니다.
* 처음으로 2단계 인증에 등록하면 앱 암호가 하나만 부여됩니다.  추가 암호가 필요한 경우 만들 수 있습니다.



## <a name="creating-and-deleting-app-passwords"></a>앱 암호 만들기 및 삭제
초기 로그인 중에 사용할 수 있는 앱 암호가 제공됩니다.  또한 나중에 앱 암호를 만들고 삭제할 수도 있습니다. 앱 암호 삭제 방법은 다단계 인증을 사용하는 방법에 따라 달라집니다. 다음 질문에 대답하여 앱 암호를 관리해야 하는 위치를 결정합니다.

1. 개인 Microsoft 계정에 대해 2단계 인증을 사용합니까? 그렇다면 [앱 암호 및 2단계 인증](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-two-step-verification) 문서를 참조하여 도움을 받습니다. 그렇지 않은 경우 2번 질문으로 계속합니다.

2. 이제 직장이나 학교 계정에 대해 2단계 인증을 사용합니다. Office 365 앱에 로그인하는 데 2단계 인증을 사용합니까? 그렇다면 [Office 365용 앱 암호 만들기](https://support.office.com/article/Create-an-app-password-for-Office-365-3e7c860f-bda4-4441-a618-b53953ee1183)를 참조하여 도움을 받습니다. 그렇지 않은 경우 3번 질문으로 계속합니다.

3. Microsoft Azure에서 2단계 인증을 사용합니까? 그렇다면 이 문서의 [Azure Portal에서 앱 암호 관리](#manage-app-passwords-in-the-Azure-portal) 섹션에서 계속합니다. 그렇지 않은 경우 4번 질문으로 계속합니다.

4. 2단계 인증을 사용하는 위치를 모르십니까? 이 문서의 [MyApps 포털에서 앱 암호 관리](#manage-app-passwords-with-the-myapps-portal) 섹션에서 계속합니다.


## <a name="manage-app-passwords-in-the-azure-portal"></a>Azure Portal에서 앱 암호 관리
Azure에서 2단계 인증을 사용하는 경우 Azure Portal을 통해 앱 암호를 만들려고 합니다.

### <a name="to-create-app-passwords-in-the-azure-portal"></a>Azure 포털에서 앱 암호를 만들려면
1. Azure 포털에 로그인합니다.
2. 맨 위에 있는 사용자 이름을 클릭하고 **암호 변경**을 선택합니다.
3. 검사 페이지 위쪽에서 **앱 암호**를 선택합니다.
4. **만들기**를 선택합니다.
5. 앱 암호의 이름을 입력하고 **다음**을 선택합니다.
6. 앱 암호를 클립보드에 복사하고 앱에 붙여 넣습니다.

   ![클라우드](./media/multi-factor-authentication-end-user-app-passwords/app2.png)


## <a name="manage-app-passwords-with-the-myapps-portal"></a>MyApps 포털에서 앱 암호 관리
다단계 인증을 사용하는 방법을 잘 모르는 경우 MyApps 포털을 통해 언제든지 앱 암호를 만들고 삭제할 수 있습니다.

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>MyApps 포털을 사용하여 앱 암호를 만들려면
1. [https://myapps.microsoft.com](https://myapps.microsoft.com)에 로그인합니다.
2. 오른쪽 위에서 이름을 클릭하고 **프로필**을 선택합니다.
3. **추가 보안 인증**을 선택합니다.
   ![추가 보안 인증 선택 - 스크린샷](./media/multi-factor-authentication-end-user-manage/myapps1.png)

4. **앱 암호**를 선택합니다.
   ![앱 암호 선택 - 스크린샷](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. **만들기**를 클릭합니다.
6. 앱 암호의 이름을 입력하고 **다음**을 클릭합니다.
7. 앱 암호를 클립보드에 복사하고 앱에 붙여 넣습니다.
   ![앱 암호 만들기](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>MyApps 포털을 사용하여 앱 암호를 삭제하려면
1. [https://myapps.microsoft.com](https://myapps.microsoft.com)에 로그인합니다.
2. 위쪽에서 프로필을 선택합니다.
3. **추가 보안 인증**을 선택합니다.

   ![추가 보안 인증 선택 - 스크린샷](./media/multi-factor-authentication-end-user-manage/myapps1.png)

4. **앱 암호**를 선택합니다.

   ![앱 암호 선택 - 스크린샷](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. 제거할 앱 암호 옆에 있는 **삭제**를 선택합니다.

   ![앱 암호 삭제](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)

6. **예**를 클릭하여 암호를 삭제할 것인지 확인합니다.
7. 앱 암호가 삭제되면 **닫기**를 클릭하면 됩니다.

## <a name="next-steps"></a>다음 단계

- [2단계 인증 설정 관리](multi-factor-authentication-end-user-manage-settings.md)

- [Microsoft Authenticator 앱](microsoft-authenticator-app-how-to.md)을 사용하여 텍스트 또는 전화를 받는 대신 앱 알림으로 로그인을 확인합니다.
