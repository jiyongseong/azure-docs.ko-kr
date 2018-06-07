---
title: "Visual Studio에서 연결된 서비스를 사용하여 모바일 서비스 추가 | Microsoft Docs"
description: "Visual Studio 연결된 서비스 추가 대화 상자를 사용하여 모바일 서비스 추가"
services: visual-studio-online
documentationcenter: na
author: mlhoop
manager: douge
editor: 
ms.assetid: 75c3cb93-88e1-476d-a416-f34caa3608e3
ms.service: visual-studio-online
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: mobile
ms.date: 12/16/2015
ms.author: mlearned
ms.openlocfilehash: d185fdafebad56f8970e390b2a0672c3fb84df8f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="adding-mobile-services-by-using-visual-studio-connected-services"></a>Visual Studio 연결된 서비스를 사용하여 모바일 서비스 추가
Visual Studio 2015에서 **연결된 서비스 추가** 대화 상자를 사용하여 Azure 모바일 서비스에 연결할 수 있습니다. 모든 C# 클라이언트 앱, JavaScript 앱 또는 크로스 플랫폼 Cordova 앱에서 연결할 수 있습니다. 연결되면 데이터를 만들고 액세스하고, 사용자 지정 API 및 예약된 작업을 만들거나 푸시 알림에 대한 지원을 추가합니다.  연결된 서비스 작업은 적절한 참조와 연결 코드를 모두 추가합니다. Azure AD, Facebook, Twitter 및 Microsoft 계정과 같이 여러 인기 있는 ID 스키마로 인증용 기본 제공 지원을 활용할 수도 있습니다.

## <a name="supported-project-types"></a>지원되는 프로젝트 형식
> [!NOTE]
> Visual Studio 2015에서는 Windows Universal(Windows 10) 프로젝트에 Azure 모바일 서비스 추가가 지원되지 않습니다. 프로젝트에 대해 NuGet 패키지 관리자에서 적절한 패키지를 설치하여 Azure 모바일 서비스를 추가할 수 있습니다.
> 
> 

연결된 서비스 대화 상자에서 다음 프로젝트 형식으로 Azure 모바일 서비스에 연결할 수 있습니다.

* .NET Windows 8.1 Store, Phone 및 Universal 앱 프로젝트
* JavaScript Windows 8.1 Store, Phone 및 Universal 앱 프로젝트
* Apache Cordova용 Visual Studio Tools를 사용하여 만든 프로젝트

## <a name="connect-to-azure-mobile-services-using-the-add-connected-services-dialog"></a>연결된 서비스 추가 대화 상자를 사용하여 Azure 모바일 서비스에 연결
1. Azure 계정이 있어야 합니다. Azure 계정이 없으면 [무료 평가판](http://go.microsoft.com/fwlink/?LinkId=518146)에 등록할 수 있습니다.
2. **연결된 서비스 추가** 대화 상자를 엽니다.
   
   * .NET 앱의 경우, Visual Studio에서 프로젝트를 열고, 솔루션 탐색기에서 **참조** 노드의 상황에 맞는 메뉴를 연 다음 **연결된 서비스 추가**를 선택합니다.
     
        ![Azure 모바일 서비스에 연결](./media/vs-azure-tools-connected-services-add-mobile-services/IC797635.png)
   * Apache Cordova 앱의 경우, Visual Studio에서 프로젝트를 열고, 솔루션 탐색기에서 프로젝트 노드의 상황에 맞는 메뉴를 연 다음 **연결된 서비스 추가**를 선택합니다.
3. **연결된 서비스 추가** 대화 상자에서 **Azure Mobile Services**를 선택한 다음 **구성** 단추를 선택합니다. 아직 수행하지 않은 경우 Azure에 로그인하라는 메시지가 표시될 수 있습니다.
   
    ![Azure 모바일 서비스 추가](./media/vs-azure-tools-connected-services-add-mobile-services/IC797636.png)
4. **Azure 모바일 서비스** 대화 상자에서 기존 모바일 서비스가 있는 경우 이를 선택합니다. 새 Azure 모바일 서비스를 만들어야 하는 경우 이렇게 하려면 다음 절차를 따르십시오. 그렇지 않은 경우 다음 단계로 건너뜁니다.
   
    새 모바일 서비스 계정을 만들려면:
   
   1. 대화 상자의 아래쪽에서 **서비스 만들기** 링크를 선택합니다.
       ![새 모바일 연결된 서비스 추가](./media/vs-azure-tools-connected-services-add-mobile-services/IC797637.png)
   2. **모바일 서비스 만들기** 대화 상자에서 JavaScript 백 엔드 모바일 서비스 또는 .NET 백 엔드 모바일 서비스를 **런타임** 드롭다운 목록에서 선택할 수 있습니다. 
      
       ![모바일 서비스 만들기](./media/vs-azure-tools-connected-services-add-mobile-services/IC797638.png)
      
       JavaScript 백 엔드 서비스는 단순하면서도 강력합니다. JavaScript 백 엔드 모바일 서비스를 만드는 경우 서버 측 JavaScript 코드가 클라우드에서 저장되지만 서버 탐색기 또는 Azure 관리 포털을 사용하여 서버 스크립트를 편집할 수 있습니다. 
      
       .NET 백 엔드 모바일 서비스는 Web API 및 Entity Framework의 모든 기능 및 유연성을 제공합니다. .NET 백 엔드 모바일 서비스를 만드는 경우, 프로젝트가 만들어져 솔루션에 추가됩니다. 
   3. 모바일 서비스가 필요한 **지역** 을 선택한 다음 서버에 대한 사용자 이름과 암호를 입력합니다.
   4. 필요한 모든 정보를 입력한 후 **만들기** 단추를 선택하여 모바일 서비스를 만듭니다.
   5. 새 모바일 서비스는 **Azure 모바일 서비스** 대화 상자의 서비스 목록에 나타나야 합니다. 목록에서 새 모바일 서비스를 선택한 다음 **추가** 단추를 선택하여 프로젝트에 서비스를 추가합니다.
5. 표시되는 시작 페이지를 검토하고 프로젝트를 수정하는 방법에 대해 알아봅니다. 연결된 서비스를 추가할 때마다 시작 페이지가 브라우저에 나타납니다. 제안된 다음 단계 및 코드 예제를 검토하거나 변경된 내용 페이지로 전환하여 프로젝트에 추가된 참조 및 코드와 구성 파일이 수정된 방법을 볼 수 있습니다.
6. 코드 샘플을 가이드로 사용하여, 모바일 서비스에 액세스하는 코드를 작성하기 시작합니다!

## <a name="how-your-project-is-modified"></a>프로젝트를 수정하는 방법
Visual Studio가 프로젝트를 수정하는 방법은 프로젝트 형식에 따라 달라집니다. C# 클라이언트 앱의 경우 [변경된 내용 – C# 프로젝트](http://go.microsoft.com/fwlink/p/?LinkId=513119)를 참조하세요. JavaScript 클라이언트 앱의 경우 [변경된 내용 – JavaScript 프로젝트](http://go.microsoft.com/fwlink/p/?LinkId=513120)를 참조하세요. Cordova 앱의 경우 [변경된 내용 – Cordova 프로젝트](http://go.microsoft.com/fwlink/p/?LinkId=513116)를 참조하세요.

## <a name="next-steps"></a>다음 단계
질문하고 도움 받기: 

* [MSDN 포럼: Azure 모바일 서비스](https://social.msdn.microsoft.com/forums/azure/home?forum=azuremobile)
* [Microsoft Azure Team Blog의 Azure 모바일 서비스](https://azure.microsoft.com/blog/topics/mobile/)
* [azure.microsoft.com의 Azure 모바일 서비스](https://azure.microsoft.com/services/mobile-services/)
* [azure.microsoft.com의 Azure 모바일 서비스 설명서](https://azure.microsoft.com/documentation/services/mobile-services/)

