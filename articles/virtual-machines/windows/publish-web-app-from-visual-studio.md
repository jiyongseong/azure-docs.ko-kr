---
title: Visual Studio에서 Azure VM에 웹앱 게시
description: Visual Studio에서 Azure Virtual Machine으로 ASP.NET 웹 응용 프로그램 게시
services: virtual-machines-windows
author: ghogen
manager: douge
tags: azure-service-management
ms.assetid: 70267837-3629-41e0-bb58-2167ac4932b3
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: aspnet
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: ghogen
ms.openlocfilehash: d9e2c1a941c25aedd9048f2784350106b8739dba
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2018
ms.locfileid: "31797197"
---
# <a name="publish-an-aspnet-web-app-to-an-azure-vm-from-visual-studio"></a>Visual Studio에서 Azure Virtual Machine으로 ASP.NET 웹앱 게시

이 문서에서는 Visual Studio 2017에서 **Microsoft Azure Virtual Machines** 게시 기능을 사용하여 ASP.NET 웹 응용 프로그램을 Azure VM(Virtual Machine)에 게시하는 방법을 설명합니다.  

## <a name="prerequisites"></a>필수 조건
Visual Studio를 사용하여 Azure VM에 ASP.NET 프로젝트를 게시하려면 VM 을 올바르게 설정해야 합니다.

- 컴퓨터를 ASP.NET 웹 응용 프로그램을 실행하도록 구성하고 WebDeploy를 컴퓨터에 설치해야 합니다.

- VM에 구성된 DNS 이름이 있어야 합니다. 자세한 내용은 [Azure Portal에서 Windows VM에 대한 정규화된 도메인 이름 만들기](portal-create-fqdn.md)를 참조하세요.

## <a name="publish-your-aspnet-web-app-to-the-azure-vm-using-visual-studio"></a>Visual Studio를 사용하여 Azure VM에 ASP.NET 웹앱 게시
다음 섹션에서는 Azure Virtual Machine에 기존 ASP.NET 웹 응용 프로그램을 게시하는 방법을 설명합니다.

1. Visual Studio 2017에서 웹앱 솔루션을 엽니다.
2. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시...** 를 선택합니다.
3. 페이지 오른쪽의 화살표를 사용하여 **Microsoft Azure Virtual Machines**가 나올 때까지 게시 옵션을 탐색합니다.  

   ![게시 페이지 - 오른쪽 화살표]

4. **Microsoft Azure Virtual Machines** 아이콘을 선택하고 **게시**를 선택합니다.

   ![게시 페이지 - Microsoft Azure Virtual Machine 아이콘]

5. 적합한 계정(가상 머신에 연결된 Azure 구독 있음)을 선택합니다.  
   - Visual Studio에 로그인한 경우 계정 목록에 인증된 모든 계정이 입력됩니다.  
   - 로그인하지 않았거나 필요한 계정이 목록에 없으면 “계정 추가...”를 선택하고 지시에 따라 로그인합니다.  
   ![Azure Account Selector]  

6. 기존 Virtual Machines 목록에서 적합한 VM을 선택합니다.

   > [!Note]
   > 이 목록이 채워지는 데는 다소 시간이 걸립니다.

   ![Azure VM Selector]

7. 확인을 클릭하여 게시를 시작합니다.

8. 자격 증명 대화 상자가 나타나면 게시 권한이 있게 구성된 대상 VM의 사용자 계정 이름과 암호를 입력합니다(일반적으로 VM을 만들 때 사용한 관리자 사용자 이름과 암호).  

   ![WebDeploy 로그인]

9. 보안 인증서를 수락합니다.

   ![인증서 오류]

10. 출력 창에서 게시 작업의 진행 상황을 확인합니다.

    ![출력 창]

11. 게시에 성공하면 브라우저가 실행되어 새로 게시한 사이트의 URL이 열립니다.

**성공!**

이제 Azure Virtual Machine에 웹앱을 게시했습니다.

## <a name="publish-page-options"></a>게시 페이지 옵션

게시 마법사 완료 후 게시 페이지가 선택한 새 게시 프로필이 있는 문서 모음에서 열립니다.

### <a name="re-publish"></a>다시 게시

웹 응용 프로그램에 대한 업데이트를 게시하려면 게시 페이지에서 **게시** 단추를 선택합니다.  
- 메시지가 표시되면 사용자 이름 및 암호를 입력합니다.  
- 게시는 즉시 시작됩니다.

![게시 페이지 - 게시 단추]

### <a name="modify-publish-profile-settings"></a>게시 프로필 설정 수정

게시 프로필 설정을 보고 수정하려면 **설정...** 을 선택합니다.  

![게시 페이지 - 설정 단추]

설정은 다음과 같이 표시됩니다.  

![게시 설정 - 연결 페이지]

#### <a name="save-user-name-and-password"></a>사용자 이름 및 암호 저장
- 게시할 때마다 매번 인증 정보를 입력하지 않으려면 **사용자 이름** 및 **암호** 필드를 입력하고 **암호 저장** 상자를 선택할 수 있습니다.
- **연결 유효성 검사** 단추를 사용하여 올바른 정보를 입력했는지 확인합니다.

#### <a name="deploy-to-clean-web-server"></a>깨끗한 웹 서버에 배포

- 각각의 업로드 후 웹 응용 프로그램의 깨끗한 사본이 웹 서버에 있는지 확인하려면(이전 배포와 관련하여 다른 파일이 남아 있지 않음) **설정** 탭에서 **대상의 추가 파일 제거** 탭을 선택합니다.

- 경고: 이 설정으로 게시하면 웹 서버의 모든 파일이 삭제됩니다(wwwroot 디렉터리). 이 옵션을 사용하여 게시하기 전에 컴퓨터의 상태를 알고 있어야 합니다. 

![게시 설정 - 설정 페이지]

## <a name="next-steps"></a>다음 단계

### <a name="set-up-cicd-for-automated-deployment-to-azure-vm"></a>Azure VM에 자동 배포를 위한 CI/CD 설정

Visual Studio Team Service를 통해 연속 전달 파이프라인을 설정하려면 [Windows Virtual Machine에 배포](https://docs.microsoft.com/vsts/build-release/apps/cd/deploy-webdeploy-iis-deploygroups)를 참조하세요.

[VM Overview - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSName.png
[IP Address Config - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/IPAddressConfigDNSName.png
[VM Overview - DNS Configured]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSConfigured.png
[게시 페이지 - 오른쪽 화살표]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageRightArrow.png
[게시 페이지 - Microsoft Azure Virtual Machine 아이콘]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageMicrosoftAzureVirtualMachineIcon.png
[Azure Account Selector]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectAccount.png
[Azure VM Selector]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectVM.png
[WebDeploy 로그인]: ../../../includes/media/publish-web-app-from-visual-studio/WebDeployLogin.png
[인증서 오류]: ../../../includes/media/publish-web-app-from-visual-studio/CertificateError.png
[출력 창]: ../../../includes/media/publish-web-app-from-visual-studio/OutputWindow.png
[게시 페이지 - 게시 단추]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPagePublishButton.png
[게시 페이지 - 설정 단추]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageSettingsButton.png
[게시 설정 - 연결 페이지]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsConnectionPage.png
[게시 설정 - 설정 페이지]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsSettingsPage.png
