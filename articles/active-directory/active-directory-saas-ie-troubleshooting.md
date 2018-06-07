---
title: IE에 대한 액세스 패널 확장 문제 해결| Microsoft Azure
description: 그룹 정책을 사용하여 My Apps 포털용 Internet Explorer 추가 기능을 배포하는 방법
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: f56b3230-26fd-42ec-9e3d-2c12daf15479
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/31/2017
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a4f1538cf598da8b5b9aa19def2d5f86ceaca0a0
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="troubleshooting-the-access-panel-extension-for-internet-explorer"></a>Internet Explorer용 액세스 패널 확장 문제 해결
이 문서는 다음과 같은 문제를 해결하는 데 도움이 됩니다.

* Internet Explorer를 사용하는 중에 My Apps 포털을 통해 응용 프로그램에 액세스할 수 없습니다.
* 소프트웨어를 이미 설치했는데도 "소프트웨어 설치" 메시지가 나타납니다.

관리자인 경우 [Internet Explorer용 액세스 패널 확장을 배포하는 방법](active-directory-saas-ie-group-policy.md)

## <a name="run-the-diagnostic-tool"></a>진단 도구 실행
액세스 패널 진단 도구를 다운로드 및 실행하여 액세스 패널 확장의 설치 문제를 진단할 수 있습니다.

1. [진단 도구를 다운로드하려면 여기를 클릭합니다.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
2. 파일을 열고 **모두 추출** 단추를 누릅니다.
   
    ![모두 추출 누르기](./media/active-directory-saas-ie-troubleshooting/extract1.png)
3. 그런 다음 **추출** 단추를 눌러 계속합니다.
   
    ![추출 누르기](./media/active-directory-saas-ie-troubleshooting/extract2.png)
4. 도구를 실행하려면 이름이 **AccessPanelExtensionDiagnosticTool**인 파일을 마우스 오른쪽 단추로 클릭한 다음 **열기 > Microsoft Windows 기반 스크립트 호스트**를 선택합니다.
   
    ![열기 > Microsoft Windows 기반 스크립트 호스트](./media/active-directory-saas-ie-troubleshooting/open_tool.png)
5. 그러면 다음 진단 창이 표시되어 가능한 설치 문제를 설명합니다.
   
    ![진단 창 샘플](./media/active-directory-saas-ie-troubleshooting/tool_preview.png)
6. "**예**"를 클릭하여 프로그램이 발견한 문제를 수정하게 합니다.
7. 이러한 변경 내용을 저장하려면 모든 Internet Explorer 창을 닫은 다음 Internet Explorer를 다시 엽니다.<br />여전히 앱에 액세스할 수 없으면 다음 단계를 수행해 보세요.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>액세스 패널 확장을 사용할 수 있는지 확인
액세스 패널 확장이 Internet Explorer에서 사용할 수 있는지 확인합니다.

1. Internet Explorer에서 창 오른쪽 위 모퉁이에 있는 **기어 아이콘**을 클릭합니다. 그런 다음 **인터넷 옵션**을 선택합니다.<br />이전 버전의 Internet Explorer에서는 **도구 > 인터넷 옵션** 아래에서 찾을 수 있습니다.
   
    ![도구 > 인터넷 옵션으로 이동](./media/active-directory-saas-ie-troubleshooting/internetoptions.png)
2. **프로그램** 탭을 클릭한 다음 **추가 기능 관리** 단추를 클릭합니다.
   
    ![추가 기능 관리 클릭](./media/active-directory-saas-ie-troubleshooting/internetoptions_programs.png)
3. 이 대화 상자에서 **액세스 패널 확장**을 클릭하고 **사용** 단추를 클릭합니다.
   
    ![사용 클릭](./media/active-directory-saas-ie-troubleshooting/enableaddon.png)
4. 이러한 변경 내용을 저장하려면 모든 Internet Explorer 창을 닫은 다음 Internet Explorer를 다시 엽니다.

## <a name="enable-extensions-for-inprivate-browsing"></a>InPrivate 브라우징에 확장 사용
InPrivate 브라우징 모드를 사용 중인 경우:

1. Internet Explorer에서 창 오른쪽 위 모퉁이에 있는 **기어 아이콘**을 클릭합니다. 그런 다음 **인터넷 옵션**을 선택합니다.<br />이전 버전의 Internet Explorer에서는 **도구 > 인터넷 옵션** 아래에서 찾을 수 있습니다.
   
    ![진단 창 샘플](./media/active-directory-saas-ie-troubleshooting/inprivateoptions.png)
2. **개인정보** 탭으로 이동한 다음 이름이 **InPrivate 브라우징 시작 시 도구 모음 및 확장 프로그램 사용 안 함** 확인란을 **선택 취소**합니다.</p>
   
    ![InPrivate 브라우징 시작 시 도구 모음 및 확장 프로그램 사용 안 함](./media/active-directory-saas-ie-troubleshooting/enabletoolbars.png)
3. 이러한 변경 내용을 저장하려면 모든 Internet Explorer 창을 닫은 다음 Internet Explorer를 다시 엽니다.

## <a name="uninstall-the-access-panel-extension"></a>액세스 패널 확장 제거
컴퓨터에서 액세스 패널 확장을 제거합니다.

1. 키보드에서 **Windows 키** 를 눌러 시작 메뉴를 엽니다. 메뉴를 열면 검색 작업에 아무 항목이나 입력할 수 있습니다. "제어판"을 입력한 다음 검색 결과에 표시되면 **제어판** 을 엽니다.
   
    ![제어판 검색](./media/active-directory-saas-ie-troubleshooting/search_sm.png)
2. 제어판의 오른쪽 위 모퉁이에서 **보기** 옵션을 **큰 아이콘**으로 변경합니다. 그런 다음 **프로그램 및 기능** 단추를 찾아 클릭합니다.
   
    ![큰 아이콘을 표시하도록 보기 변경](./media/active-directory-saas-ie-troubleshooting/control_panel.png)
3. 목록에서 **액세스 패널 확장**을 선택하고 **제거** 단추를 클릭합니다.
   
    ![제거 클릭](./media/active-directory-saas-ie-troubleshooting/uninstall.png)
4. 그런 다음 확장을 다시 설치하여 문제가 해결되었는지 확인합니다.

확장을 제거하는 데 문제가 있으면 [Microsoft Fix It](https://go.microsoft.com/?linkid=9779673) 도구를 사용하여 제거할 수도 있습니다.

## <a name="related-articles"></a>관련 문서
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On](manage-apps/what-is-single-sign-on.md)
* [그룹 정책을 사용하여 Internet Explorer용 액세스 패널 확장을 배포하는 방법](active-directory-saas-ie-group-policy.md)

