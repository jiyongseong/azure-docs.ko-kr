---
title: "Azure Event Hubs 미리 보기를 사용하는 관리 서비스 ID | Microsoft Docs"
description: "Azure Event Hubs에서 관리 서비스 ID 사용"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/18/2017
ms.author: sethm
ms.openlocfilehash: dd50e4f6ebc5fdf5496a5127fde20bd052087b59
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/20/2017
---
# <a name="managed-service-identity-preview"></a>관리 서비스 ID(미리 보기)

MSI(관리 서비스 ID)는 응용 프로그램 코드가 실행되는 배포와 관련된 보안 ID를 만들 수 있도록 Azure 간 기능입니다. 그런 다음 응용 프로그램에 필요한 특정 Azure 리소스에 액세스하기 위한 사용자 지정 권한을 부여하는 액세스 제어 역할에 해당 ID를 연결할 수 있습니다. 

MSI를 사용하여 Azure 플랫폼은 이 런타임 ID를 관리합니다. ID 자체 또는 액세스해야 하는 리소스에 대한 액세스 키를 응용 프로그램 코드나 구성에 저장하고 보호할 필요가 없습니다. MSI 지원이 활성화된 Azure App Service 응용 프로그램 또는 가상 머신에서 실행되는 Event Hubs 클라이언트 앱이 SAS 규칙 및 키나 다른 액세스 토큰을 처리할 필요가 없습니다. 클라이언트 앱은 Event Hubs 네임스페이스의 끝점 주소만 있으면 됩니다. 이 문서 뒷부분에 나오는 예제의 작업에서, 앱이 연결되면 Event Hubs는 MSI 컨텍스트를 클라이언트에 바인딩합니다.

관리 서비스 ID에 일단 연결되면, Event Hubs 클라이언트는 권한 있는 모든 작업을 수행할 수 있습니다. MSI를 Event Hubs 역할에 연결하면 권한이 부여됩니다. 

## <a name="event-hubs-roles-and-permissions"></a>Event Hubs 역할 및 사용 권한

초기 공개 미리 보기 릴리스에서는 관리 서비스 ID를 Event Hubs 네임스페이스의 "소유자" 또는 "참가자" 역할에만 추가할 수 있습니다. 그러면 네임스페이스의 모든 엔터티에 대한 모든 권한이 해당 ID에 부여됩니다. 그러나 네임스페이스 토폴로지를 변경하는 관리 작업은 처음에는 Azure Resource Manager를 통해서만 지원되고 네이티브 Event Hubs REST 관리 인터페이스를 통해서는 지원되지 않습니다. 이 지원은 관리 서비스 ID 내에서 .NET Framework 클라이언트 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) 개체를 사용할 수 없음을 의미하기도 합니다. 
 
## <a name="use-event-hubs-with-a-managed-service-identity"></a>관리 서비스 ID와 Event Hubs 사용

다음 섹션에서는 관리 서비스 ID에서 실행되는 샘플 응용 프로그램을 만들고 배포하는 데 필요한 단계, 해당 ID에 Event Hubs 네임스페이스에 대한 액세스 권한을 부여하는 방법, 응용 프로그램이 해당 ID를 사용하여 이벤트 허브와 상호 작용하는 방식을 설명합니다.

이 소개에서는 [Azure App Service](https://azure.microsoft.com/services/app-service/)에 호스트되는 웹 응용 프로그램에 대해 설명합니다. VM 호스티드 응용 프로그램에 필요한 단계와 비슷합니다.

### <a name="create-an-app-service-web-application"></a>App Service 웹 응용 프로그램 만들기

첫 번째 단계는 App Service ASP.NET 응용 프로그램을 만드는 것입니다. Azure에서 이 작업을 수행하는 방법을 잘 모를 경우 [이 방법 가이드](../app-service/app-service-web-get-started-dotnet-framework.md)를 따릅니다. 그러나 이 자습서에 나와 있는 것처럼 MVC 응용 프로그램을 만드는 대신, Web Forms 응용 프로그램을 만듭니다.

### <a name="set-up-the-managed-service-identity"></a>관리 서비스 ID 설정

응용 프로그램을 만든 후 Azure Portal에서 새로 만든 웹앱으로 이동한 후(방법에 제공된 설명 참조) **관리 서비스 ID** 페이지로 이동하여 해당 기능을 사용하도록 설정합니다. 

![](./media/event-hubs-managed-service-identity/msi1.png)
 
기능을 사용하도록 설정하면 새 서비스 ID가 Azure Active Directory에서 생성되고 App Service 호스트로 구성됩니다.

### <a name="create-a-new-event-hubs-namespace"></a>새 Event Hubs 네임스페이스 만들기

그런 다음, MSI에 대한 미리 보기 지원이 있는 Azure 지역인 **미국 동부**, **미국 동부 2** 또는 **유럽 서부** 중 한 곳에 [Event Hubs 네임스페이스를 만듭니다](event-hubs-create.md). 

포털의 네임스페이스 **액세스 제어(IAM)** 페이지로 이동한 후 **추가**를 클릭하여 관리 서비스 ID를 **소유자** 역할에 추가합니다. 이렇게 하려면 **권한 추가** 패널의 **선택** 필드에서 웹 응용 프로그램의 이름을 검색하고 해당 항목을 클릭합니다. 그런 다음 **Save**를 클릭합니다.

![](./media/event-hubs-managed-service-identity/msi2.png)
 
이제 웹 응용 프로그램의 관리 서비스 ID는 Event Hubs 네임스페이스 및 이전에 만든 이벤트 허브에 액세스할 수 있습니다. 

### <a name="run-the-app"></a>앱 실행

이제 만든 ASP.NET 응용 프로그램의 기본 페이지를 수정합니다. [이 GitHub 리포지토리](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/MSI/EventHubsMSIDemoWebApp)에서 웹 응용 프로그램 코드를 사용할 수도 있습니다. 

앱을 시작한 후 브라우저로 EventHubsMSIDemo.aspx를 가리킵니다. 또는 시작 페이지로 설정합니다. 이 코드는 EventHubsMSIDemo.aspx.cs 파일에서 찾을 수 있습니다. 그 결과 몇 가지 입력 필드와 메시지를 보내거나 받기 위해 Event Hubs에 연결되는 **전송** 및 **수신** 단추가 있는 최소 웹 응용 프로그램이 생성됩니다. 

[MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) 개체가 초기화되는 방식을 적어둡니다. 이 코드는 SAS(공유 액세스 토큰) 토큰 공급자를 사용하는 대신, `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.EventHubAudience)` 호출을 사용하여 관리 서비스 ID에 대한 토큰 공급자를 만듭니다. 이와 같이 유지하고 사용할 비밀은 없습니다. Event Hubs 및 권한 부여 핸드셰이크에 대한 관리 서비스 ID의 흐름은 토큰 공급자가 자동으로 처리합니다. 이것은 SAS를 사용하는 것보다 좀 더 간단한 모델입니다.

이와 같이 변경한 후에는 응용 프로그램을 게시하고 실행합니다. 올바른 게시 데이터를 가져오는 쉬운 방법은 Visual Studio에서 게시 프로필을 다운로드한 다음 가져옵니다.

![](./media/event-hubs-managed-service-identity/msi3.png)
 
메시지를 보내거나 받으려면 만든 엔터티의 이름과 네임스페이스의 이름을 입력한 후 **전송** 또는 **수신**을 클릭합니다. 
 
관리 서비스 ID는 Azure 환경 및 구성한 App Service 배포에서만 작동합니다. 또한 관리 서비스 ID가 현재는 App Service 배포 슬롯에서 작동하지 않습니다.

## <a name="next-steps"></a>다음 단계

Event Hubs에 대한 자세한 내용은 다음 링크를 방문하세요.

* [Event Hubs 자습서](event-hubs-dotnet-standard-getstarted-send.md) 시작
* [Event Hubs FAQ](event-hubs-faq.md)
* [Event Hubs 가격 정보](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Event Hubs를 사용하는 샘플 응용 프로그램](https://github.com/Azure/azure-event-hubs/tree/master/samples)