---
title: "Xamarin.Forms을 사용하여 Mobile Apps 시작"
description: "이 자습서에 따라 Mobile Apps를 사용하여 Xamarin.Forms 개발을 시작할 수 있습니다."
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: 
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 01a8e356649c070a5cf36ae070195abce699f85e
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2018
---
# <a name="create-a-xamarinforms-app"></a>Xamarin.Forms 앱 만들기
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>개요
이 자습서에서는 Azure App Service의 Mobile Apps 기능을 백 엔드로 사용하여 클라우드 기반 백 엔드 서비스를 Xamarin.Forms 모바일 앱에 추가하는 방법을 보여줍니다. 새 Mobile Apps 백 엔드와 앱 데이터를 Azure에 저장하는 간단한 할 일 모음 Xamarin.Forms 앱을 만듭니다.

이 자습서를 완료해야 다른 모든 Xamarin.Forms용 Mobile Apps 자습서를 진행할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소
이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 Azure 평가판을 등록하고 최대 10개의 무료 모바일 앱을 가져올 수 있습니다. 이러한 앱은 평가판 사용 기간이 끝난 후에도 계속 사용할 수 있습니다. 자세한 내용은 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

* Xamarin이 포함된 Visual Studio입니다. 자세한 내용은 [Visual Studio 및 Xamarin 설정 및 설치](https://msdn.microsoft.com/library/mt613162.aspx) 페이지를 참조하세요.

* Xcode v7.0 이상 및 Xamarin Studio Community가 설치된 Mac입니다. 자세한 내용은 [Visual Studio 및 Xamarin을 위한 설정 및 설치](https://msdn.microsoft.com/library/mt613162.aspx) 및 [Mac 사용자를 위한 설정, 설치 및 확인](https://msdn.microsoft.com/library/mt488770.aspx)(MSDN)을 참조하세요.

## <a name="create-a-new-mobile-apps-back-end"></a>새 Mobile Apps 백 엔드 만들기

새 Mobile Apps 백 엔드를 만들려면 다음을 수행합니다.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

이제 모바일 클라이언트 응용 프로그램에서 사용할 수 있는 Mobile Apps 백 엔드를 설정했습니다. 다음으로, 간단한 할 일 목록 백 엔드에 대한 서버 프로젝트를 다운로드하고 Azure에 게시합니다.

## <a name="configure-the-server-project"></a>서버 프로젝트 구성

Node.js 또는 .NET 백 엔드를 사용하도록 서버 프로젝트를 구성하려면 다음을 수행합니다.

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinforms-solution"></a>Xamarin.Forms 솔루션 다운로드 및 실행

다음과 같은 두 가지 방법 중 하나에서 솔루션을 다운로드할 수 있습니다. 솔루션을 Mac에 다운로드하고 Xamarin Studio에서 엽니다. 또는 iOS 앱을 빌드하는 네트워크에 연결된 Mac을 사용하여 솔루션을 Windows 컴퓨터에 다운로드하고 Visual Studio에서 엽니다. 자세한 내용은 [Visual Studio 및 Xamarin 설정 및 설치](https://msdn.microsoft.com/library/mt613162.aspx)를 참조하세요.

Windows 또는 Mac 컴퓨터에서 다음을 수행합니다.

1. [Azure 포털]로 이동합니다.

2. Mobile Apps에 대한 설정 블레이드에서 **빠른 시작**(배포 아래) > **Xamarin.Forms**를 차례로 클릭합니다. 3단계 아래에서 **새 앱 만들기**가 선택되어 있지 않으면 클릭합니다.  그런 다음 **다운로드** 단추를 클릭합니다.

   이 작업을 통해 모바일 앱에 연결된 클라이언트 응용 프로그램을 포함하는 프로젝트를 다운로드합니다. 압축된 프로젝트 파일을 로컬 컴퓨터에 저장하고 저장 위치를 기록해 둡니다.

3. 다운로드한 프로젝트를 추출하고 Xamarin Studio(Mac) 또는 Visual Studio(Windows)에서 엽니다.

   ![Xamarin Studio에서 추출된 프로젝트][9]

   ![Visual Studio에서 추출된 프로젝트][8]

## <a name="optional-run-the-ios-project"></a>(선택 사항) iOS 프로젝트 실행
이 섹션에서는 iOS 장치용 Xamarin iOS 프로젝트를 실행합니다. iOS 장치를 작업하지 않는 경우 이 섹션을 건너뛸 수 있습니다.

#### <a name="in-xamarin-studio"></a>Xamarin Studio에서
1. iOS 프로젝트를 마우스 오른쪽 단추로 누른 다음 **시작 프로젝트로 설정**을 선택합니다.

2. **실행** 메뉴에서 **디버깅 시작**을 선택하여 프로젝트를 빌드하고 iPhone 에뮬레이터에서 앱을 시작합니다.

#### <a name="in-visual-studio"></a>Visual Studio에서
1. iOS 프로젝트를 마우스 오른쪽 단추로 누른 다음 **시작 프로젝트로 설정**을 선택합니다.

2. **빌드** 메뉴에서 **구성 관리자**를 선택합니다.

3. **구성 관리자** 대화 상자에서 iOS 프로젝트 옆에 있는 **빌드** 및 **배포** 확인란을 선택합니다.

4. 프로젝트를 빌드하고 iPhone 에뮬레이터에서 앱을 시작하려면 **F5** 키를 누릅니다.

   > [!NOTE]
   > 프로젝트를 빌드하는 데 문제가 발생한 경우 NuGet 패키지 관리자를 실행하고 최신 버전의 Xamarin 지원 패키지로 업데이트합니다. 빠른 시작 프로젝트를 최신 버전으로 업데이트하기 위해 속도가 느려질 수 있습니다.    
   >
   >

5. 앱에서 *Learn Xamarin*과 같은 의미 있는 텍스트를 입력한 후 더하기 기호(**+**)를 선택합니다.

    ![][10]

    이 작업을 통해 Azure에서 호스트된 새 Mobile Apps 백 엔드에 게시 요청이 전송됩니다. 요청에서 데이터가 TodoItem 테이블에 삽입됩니다. 테이블에 저장된 항목이 Mobile Apps 백 엔드에서 반환되고 데이터가 목록에 표시됩니다.

    > [!NOTE]
    > 솔루션의 이식 가능한 클래스 라이브러리 프로젝트의 TodoItemManager.cs C# 파일에서 Mobile Apps 백 엔드에 액세스하는 코드를 찾을 수 있습니다.
    >
    >

## <a name="optional-run-the-android-project"></a>(선택 사항) Android 프로젝트 실행
이 섹션에서는 Android용 Xamarin droid 프로젝트를 실행합니다. Android 장치를 작업하지 않는 경우 이 섹션을 건너뛸 수 있습니다.

#### <a name="in-xamarin-studio"></a>Xamarin Studio에서

1. Android 프로젝트를 마우스 오른쪽 단추로 누른 다음 **시작 프로젝트로 설정**을 선택합니다.

2. **실행** 메뉴에서 **디버깅 시작**을 클릭하여 프로젝트를 빌드하고 Android 에뮬레이터에서 앱을 시작합니다.

#### <a name="in-visual-studio"></a>Visual Studio에서

1. Android(Droid) 프로젝트를 마우스 오른쪽 단추로 누른 다음 **시작 프로젝트로 설정**을 선택합니다.

2. **빌드** 메뉴에서 **구성 관리자**를 선택합니다.

3. **구성 관리자** 대화 상자에서 Android 프로젝트 옆에 있는 **빌드** 및 **배포** 확인란을 선택합니다.

4. **F5** 키를 눌러 프로젝트를 빌드하고 Android 에뮬레이터에서 앱을 시작합니다.

   > [!NOTE]
   > 프로젝트를 빌드하는 데 문제가 발생한 경우 NuGet 패키지 관리자를 실행하고 최신 버전의 Xamarin 지원 패키지로 업데이트합니다. 빠른 시작 프로젝트를 최신 버전으로 업데이트하기 위해 속도가 느려질 수 있습니다.    
   >
   >

5. 앱에서 *Learn Xamarin*과 같은 의미 있는 텍스트를 입력한 후 더하기 기호(**+**)를 선택합니다.

    ![][11]
    
    이 작업을 통해 Azure에서 호스트된 새 Mobile Apps 백 엔드에 게시 요청이 전송됩니다. 요청에서 데이터가 TodoItem 테이블에 삽입됩니다. 테이블에 저장된 항목이 Mobile Apps 백 엔드에서 반환되고 데이터가 목록에 표시됩니다.
    
    > [!NOTE]
    > 솔루션의 이식 가능한 클래스 라이브러리 프로젝트의 TodoItemManager.cs C# 파일에서 Mobile Apps 백 엔드에 액세스하는 코드를 찾을 수 있습니다.
    >
    >

## <a name="optional-run-the-windows-project"></a>(선택 사항) Windows 프로젝트 실행

이 섹션에서는 Windows 장치용 Xamarin WinApp 프로젝트를 실행합니다. Windows 장치를 작업하지 않는 경우 이 섹션을 건너뛸 수 있습니다.

#### <a name="in-visual-studio"></a>Visual Studio에서

1. Windows 프로젝트를 마우스 오른쪽 단추로 누른 다음 **시작 프로젝트로 설정**을 선택합니다.

2. **빌드** 메뉴에서 **구성 관리자**를 선택합니다.

3. **구성 관리자** 대화 상자에서 선택한 Windows 프로젝트 옆에 있는 **빌드** 및 **배포** 확인란을 선택합니다.

4. **F5** 키를 눌러 프로젝트를 빌드하고 Windows 에뮬레이터에서 앱을 시작합니다.

   > [!NOTE]
   > 프로젝트를 빌드하는 데 문제가 발생한 경우 NuGet 패키지 관리자를 실행하고 최신 버전의 Xamarin 지원 패키지로 업데이트합니다. 빠른 시작 프로젝트를 최신 버전으로 업데이트하기 위해 속도가 느려질 수 있습니다.    
   >
   >

5. 앱에서 *Learn Xamarin*과 같은 의미 있는 텍스트를 입력한 후 더하기 기호(**+**)를 선택합니다.

    이 작업을 통해 Azure에서 호스트된 새 Mobile Apps 백 엔드에 게시 요청이 전송됩니다. 요청에서 데이터가 TodoItem 테이블에 삽입됩니다. 테이블에 저장된 항목이 Mobile Apps 백 엔드에서 반환되고 데이터가 목록에 표시됩니다.
    
    ![][12]
    
    > [!NOTE]
    > 솔루션의 이식 가능한 클래스 라이브러리 프로젝트의 TodoItemManager.cs C# 파일에서 Mobile Apps 백 엔드에 액세스하는 코드를 찾을 수 있습니다.
    >
    >

## <a name="next-steps"></a>다음 단계

* [앱에 인증 추가](app-service-mobile-xamarin-forms-get-started-users.md)  
  ID 공급자를 사용하여 앱 사용자를 인증하는 방법을 알아봅니다.

* [앱에 푸시 알림 추가](app-service-mobile-xamarin-forms-get-started-push.md)  
  앱에 푸시 알림 지원을 추가하고 Mobile Apps 백 엔드를 구성하여 푸시 알림을 보내는 Azure Notification Hubs를 사용하는 방법을 알아봅니다.

* [앱에 오프라인 동기화 사용](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Mobile Apps 백 엔드를 사용하여 앱에 오프라인 지원을 추가하는 방법을 알아봅니다. 오프라인 동기화를 사용하면 네트워크에 연결되어 있지 않은 경우에도 모바일 앱의 데이터를 보거나, 추가하거나, 수정할 수 있습니다.

* [Mobile Apps에 관리되는 클라이언트 사용](app-service-mobile-dotnet-how-to-use-client-library.md)  
  Xamarin 앱에서 관리되는 클라이언트 SDK로 작업하는 방법에 대해 알아봅니다.

<!-- Anchors. -->
[Get started with Mobile Apps back ends]:#getting-started
[Create a new Mobile Apps back end]:#create-new-service
[Next steps]:#next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure 포털]: https://portal.azure.com/
