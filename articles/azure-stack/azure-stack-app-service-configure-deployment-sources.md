---
title: Azure Stack에서 App Services에 대한 배포 원본 구성 | Microsoft Docs
description: 서비스 관리자가 Azure Stack에서 App Services에 대해 배포 원본(Git, GitHub, BitBucket, DropBox 및 OneDrive)을 구성하는 방법
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: brenduns
ms.reviewer: anwestg
ms.openlocfilehash: 4ee333fcc50937679c4bc25b83c2d6aa389ba194
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="configure-deployment-sources"></a>배포 원본 구성
*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*


Azure Stack에서 App Service는 여러 소스 제어 공급자로부터 주문형 배포를 지원합니다. 이 기능에는 응용 프로그램 개발자가 해당 소스 제어 저장소에서 직접 배포할 수 있습니다. 사용자가 해당 저장소에 연결 하도록 응용 프로그램 서비스를 구성 하려면 클라우드 운영자는 Azure 스택 앱 서비스와 소스 제어 공급자 간의 통합을 먼저 구성 해야 합니다.  

로컬 Git 외에도 다음과 같은 소스 제어 공급자 지원 됩니다.

* GitHub
* BitBucket
* OneDrive
* DropBox

## <a name="view-deployment-sources-in-app-service-administration"></a>앱 서비스 관리에 배포 원본 보기

1. Azure 스택 관리자 포털에 로그인 (https://adminportal.local.azurestack.external) 서비스 관리자.
2. **리소스 공급자**로 이동하여 **App Service 리소스 공급자 관리**를 선택합니다.  ![App Service 리소스 공급자 관리][1]
3. **소스 제어 구성**을 클릭합니다.  여기에 구성된 모든 배포 원본 목록이 표시됩니다.
    ![App Service 리소스 공급자 관리 소스 제어 구성][2]

## <a name="configure-github"></a>GitHub 구성

이 작업을 완료 하려면 GitHub 계정이 있어야 합니다. 개인 계정 보다는 조직 계정을 사용 수 있습니다.

1. GitHub에 로그인로 이동 https://www.github.com/settings/developers 클릭 **새 응용 프로그램 등록**합니다.
    ![GitHub-새 응용 프로그램 등록][3]
2. 입력 한 **응용 프로그램 이름** Azure 스택 앱 서비스-예.
3. **홈 페이지 URL**을 입력합니다. 홈 페이지 URL에는 Azure 스택 포털 주소 여야 합니다. 예: https://portal.local.azurestack.external
4. 입력 한 **응용 프로그램 설명**합니다.
5. **권한 부여 콜백 URL**을 입력합니다.  기본 Azure 스택 배포 Url은 형태로 https://portal.local.azurestack.external/TokenAuthorizelocal.azurestack.external에 대 한 도메인 의존해 서는 다른 도메인에서 실행 중인 경우,
6. **응용 프로그램 등록**을 클릭합니다.  이제 응용 프로그램에 대한 **클라이언트 ID** 및 **클라이언트 비밀**이 나열된 페이지가 표시됩니다.
    ![GitHub - 완료된 응용 프로그램 등록][5]
7.  새 브라우저 탭 또는 창에서 Azure 스택 관리자 포털에 로그인 (https://adminportal.local.azurestack.external) 서비스 관리자.
8.  **리소스 공급자**로 이동하여 **App Service 리소스 공급자 관리**를 선택합니다.
9. **소스 제어 구성**을 클릭합니다.
10. 복사 및 붙여넣기의 **클라이언트 ID** 및 **클라이언트 암호** GitHub에 대 한 상자에 해당 입력 합니다.
11. **저장**을 클릭합니다.

## <a name="configure-bitbucket"></a>BitBucket 구성

이 작업을 완료 BitBucket 계정이 있어야 합니다. 개인 계정 보다는 조직 계정을 사용 수 있습니다.

1. BitBucket에 로그인 하 고 찾아보기 **통합** 사용자의 계정입니다.
    ![BitBucket 대시보드-통합][7]
2. 클릭 **OAuth** 액세스 관리 및 **추가 소비자**합니다.
    ![BitBucket은 OAuth 소비자를 추가 합니다.][8]
3. 입력 한 **이름** 예를 들어 Azure 스택 앱 서비스 소비자에 대 한 합니다.
4. 입력 한 **설명** 응용 프로그램에 대 한 합니다.
5. **콜백 URL**을 입력합니다.  콜백 Url이 기본 Azure 스택 배포에서 형태로 https://portal.local.azurestack.external/TokenAuthorizeazurestack.local에 대 한 도메인 의존해 서는 다른 도메인에서 실행 되는 경우.  Url은 BitBucket 통합에 성공하려면 여기에 나열된 대로 대문자를 따라야 합니다.
6. 입력은 **URL** -이 Url 예를 들어 Azure 스택 포털 URL은 이어야 https://portal.local.azurestack.external합니다.
7. 선택 된 **권한을** 필요:
    - **저장소**: *읽기*
    - **또한 Webhook**: *읽기 및 쓰기*
8. **저장**을 클릭합니다.  이제 새 응용 프로그램이 **OAuth 소비자** 아래 **키** 및 **비밀**과 함께 표시됩니다.
    ![BitBucket 응용 프로그램 목록][9]
9.  새 브라우저 탭 또는 창에서 Azure 스택 관리자 포털에 로그인 (https://adminportal.local.azurestack.external) 서비스 관리자.
10.  **리소스 공급자**로 이동하여 **App Service 리소스 공급자 관리**를 선택합니다.
11. **소스 제어 구성**을 클릭합니다.
12. 복사 및 붙여넣기의 **키** 에 **클라이언트 ID** 입력된 상자 및 **비밀** 에 **클라이언트 암호** BitBucket에 대해 입력된 상자입니다.
13. **저장**을 클릭합니다.


## <a name="configure-onedrive"></a>OneDrive 구성

이 작업을 완료 하려면 OneDrive 계정에 연결 된 Microsoft 계정이 있어야 합니다.  개인 계정 보다는 조직 계정을 사용 수 있습니다.

> [!NOTE]
> 비즈니스용 OneDrive 계정은 현재 지원되지 않습니다.

1. 찾아 https://apps.dev.microsoft.com/?referrer=https%3A%2F%2Fdev.onedrive.com%2Fapp-registration.htm Microsoft 계정을 사용 하 여 로그인 합니다.
2. 아래 **내 응용 프로그램이**, 클릭 **앱 추가**합니다.
![OneDrive 응용 프로그램][10]
3. 새 응용 프로그램 등록을 위한 **이름**을 입력하고 **App Service on Azure Stack**을 입력한 후 **응용 프로그램 만들기**를 클릭합니다.
4. 다음 화면에서 새 응용 프로그램의 속성이 나열됩니다. 레코드는 **응용 프로그램 ID**합니다.
![OneDrive 응용 프로그램 속성][11]
5. 아래 **응용 프로그램 암호**, 클릭 **새 암호를 생성할**합니다. 메모 **생성 된 새 암호가**합니다. 응용 프로그램 암호 이므로 클릭 한 후 가져올 수 없습니다 **확인** 이 단계에서 합니다.
6. 아래 **플랫폼** 클릭 **추가 플랫폼** 선택 **웹**합니다.
7. **리디렉션 URI**를 입력합니다.  리디렉션 URI는 기본 Azure 스택 배포에서 형태로 https://portal.local.azurestack.external/TokenAuthorizeazurestack.local에 대 한 도메인 의존해 서는 다른 도메인에서 실행 중인 경우, ![OneDrive 응용 프로그램-웹 플랫폼 추가][12]
8. 추가 **Microsoft Graph 권한을** - **위임 된 사용 권한**
    - **Files.ReadWrite.AppFolder**
    - **User.Read**  
      ![OneDrive 응용 프로그램 - Graph 권한][13]
9. **저장**을 클릭합니다.
10.  새 브라우저 탭 또는 창에서 Azure 스택 관리자 포털에 로그인 (https://adminportal.local.azurestack.external) 서비스 관리자.
11.  **리소스 공급자**로 이동하여 **App Service 리소스 공급자 관리**를 선택합니다.
12. **소스 제어 구성**을 클릭합니다.
13. 복사 및 붙여넣기의 **응용 프로그램 ID** 에 **클라이언트 ID** 입력된 상자 및 **암호** 에 **클라이언트 암호** OneDrive에 대 한 입력된 상자입니다.
14. **저장**을 클릭합니다.

## <a name="configure-dropbox"></a>DropBox 구성

> [!NOTE]
> 이 작업을 완료 하려면 DropBox 계정이 있어야 합니다.  개인 계정보다는 조직에 대해 계정을 사용하고 싶을 수 있습니다.

1. 찾아 https://www.dropbox.com/developers/apps DropBox 계정에 사용 하 여 로그인 합니다.
2. **앱 만들기**를 클릭합니다.

    ![Dropbox 응용 프로그램][14]

3. 선택 **DropBox API**합니다.
4. 액세스 수준을 설정 **응용 프로그램 폴더가**합니다.
5. 응용 프로그램의 **이름**을 입력합니다.
![Dropbox 응용 프로그램 등록][15]
6. **앱 만들기**를 클릭합니다.  이제 **앱 키** 및 **앱 비밀**을 포함한 앱에 대한 설정이 나열된 페이지가 표시됩니다.
7. 확인 된 **앱 폴더 이름** 로 설정 된 **Azure 스택 앱 서비스**합니다.
8. **OAuth 2 리디렉션 URI**를 설정하고 **추가**를 클릭합니다.  리디렉션 URI는 기본 Azure 스택 배포에서 형태로 https://portal.local.azurestack.external/TokenAuthorizeazurestack.local에 대 한 도메인 의존해 서는 다른 도메인에서 실행 되는 경우.
![Dropbox 응용 프로그램 구성][16]
9.  새 브라우저 탭 또는 창에서 Azure 스택 관리자 포털에 로그인 (https://adminportal.local.azurestack.external) 서비스 관리자.
10.  **리소스 공급자**로 이동하여 **App Service 리소스 공급자 관리**를 선택합니다.
11. **소스 제어 구성**을 클릭합니다.
12. 복사 및 붙여넣기의 **응용 프로그램 키** 에 **클라이언트 ID** 입력된 상자 및 **응용 프로그램 암호** 에 **클라이언트 암호** DropBox에 대 한 입력된 상자입니다.
13. **저장**을 클릭합니다.


<!--Image references-->
[1]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin.png
[2]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-source-control-configuration.png
[3]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-developer-applications.png
[4]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-populated.png
[5]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-complete.png
[6]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-roles-management-server-repair-all.png
[7]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-dashboard.png
[8]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer.png
[9]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer-complete.png
[10]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-applications.png
[11]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-registration.png
[12]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-platform.png
[13]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-graph-permissions.png
[14]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-applications.png
[15]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-registration.png
[16]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-configuration.png

## <a name="next-steps"></a>다음 단계

등에 대 한 사용자의 배포 원본을 이제 사용할 수 [연속 배포](https://docs.microsoft.com/azure/app-service-web/app-service-continuous-deployment), [로컬 Git 배포](https://docs.microsoft.com/azure/app-service-web/app-service-deploy-local-git), 및 [클라우드 폴더가 동기화](https://docs.microsoft.com/azure/app-service-web/app-service-deploy-content-sync)합니다.
