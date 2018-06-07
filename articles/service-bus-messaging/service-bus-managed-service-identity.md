---
title: "Azure Service Bus 미리 보기를 사용하는 관리 서비스 ID | Microsoft Docs"
description: "Azure Service Bus에서 관리 서비스 ID 사용"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 7b9901ee3478cb193c808b65d2dbbcf8b596a3c1
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2018
---
# <a name="managed-service-identity-preview"></a>관리 서비스 ID(미리 보기)

MSI(관리 서비스 ID)는 응용 프로그램 코드가 실행되는 배포와 관련된 보안 ID를 만들 수 있도록 하는 Azure 간 기능입니다. 그런 다음 응용 프로그램에 필요한 특정 Azure 리소스에 액세스하기 위한 사용자 지정 권한을 부여하는 액세스 제어 역할에 해당 ID를 연결할 수 있습니다.

Azure 플랫폼은 MSI를 통해 이 런타임 ID를 관리합니다. ID 자체 또는 액세스해야 하는 리소스에 대한 액세스 키를 응용 프로그램 코드나 구성에 저장하고 보호할 필요가 없습니다. MSI 지원이 활성화된 Azure App Service 응용 프로그램 또는 가상 머신에서 실행되는 Service Bus 클라이언트 앱이 SAS 규칙 및 키나 다른 액세스 토큰을 처리할 필요가 없습니다. 클라이언트 앱은 Service Bus 메시지 네임스페이스의 엔드포인트 주소만 있으면 됩니다. 이 문서 뒷부분에 나오는 예제의 작업에서, 앱이 연결되면 Service Bus는 MSI 컨텍스트를 클라이언트에 바인딩합니다. 

관리 서비스 ID에 일단 연결되면, Service Bus 클라이언트는 권한 있는 모든 작업을 수행할 수 있습니다. MSI를 Service Bus 역할에 연결하면 권한이 부여됩니다. 

## <a name="service-bus-roles-and-permissions"></a>Service Bus 역할 및 사용 권한

초기 공개 미리 보기 릴리스에서는 관리 서비스 ID를 Service Bus 네임스페이스의 “소유자” 또는 “참가자” 역할에만 추가할 수 있습니다. 그러면 네임스페이스의 모든 엔터티에 대한 모든 권한이 해당 ID에 부여됩니다. 그러나 네임스페이스 토폴로지를 변경하는 관리 작업은 처음에는 Azure Resource Manager를 통해서만 지원되고 네이티브 Service Bus REST 관리 인터페이스를 통해서는 지원되지 않습니다. 이 지원은 관리 서비스 ID 내에서 .NET Framework 클라이언트 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) 개체를 사용할 수 없음을 의미하기도 합니다.

## <a name="use-service-bus-with-a-managed-service-identity"></a>관리 서비스 ID와 Service Bus 사용

다음 섹션에서는 관리 서비스 ID에서 실행되는 응용 프로그램 예제를 만들고 배포하는 데 필요한 단계, 해당 ID에 Service Bus 메시징 네임스페이스에 대한 액세스 권한을 부여하는 방법, 응용 프로그램이 해당 ID를 사용하여 Service Bus와 상호 작용하는 방식을 설명합니다.

이 소개에서는 [Azure App Service](https://azure.microsoft.com/services/app-service/)에 호스트되는 웹 응용 프로그램에 대해 설명합니다. VM 호스트 응용 프로그램에 필요한 단계는 비슷합니다.

### <a name="create-an-app-service-web-application"></a>App Service 웹 응용 프로그램 만들기

첫 번째 단계는 App Service ASP.NET 응용 프로그램을 만드는 것입니다. Azure에서 이 작업을 수행하는 방법을 잘 모를 경우 [이 방법 가이드](../app-service/app-service-web-get-started-dotnet-framework.md)를 따릅니다. 단, 자습서에 나와 있는 MVC 응용 프로그램 대신, Web Forms 응용 프로그램을 만듭니다.

### <a name="set-up-the-managed-service-identity"></a>관리 서비스 ID 설정

응용 프로그램을 만든 후 Azure Portal에서 새로 만든 웹앱으로 이동한 후(방법에 제공된 설명 참조) **관리 서비스 ID** 페이지로 이동하여 해당 기능을 사용하도록 설정합니다. 

![](./media/service-bus-managed-service-identity/msi1.png)

기능을 사용하도록 설정하면 새 서비스 ID가 Azure Active Directory에서 생성되고 App Service 호스트로 구성됩니다.

### <a name="create-a-new-service-bus-messaging-namespace"></a>새 Service Bus 메시지 네임스페이스 만들기

그런 다음, RBAC에 대한 미리 보기 지원이 있는 Azure 지역인 **미국 동부**, **미국 동부 2** 또는 **유럽 서부** 중 한 곳에 [Service Bus 메시지 네임스페이스를 만듭니다](service-bus-create-namespace-portal.md). 

포털의 네임스페이스 **액세스 제어(IAM)** 페이지로 이동한 후 **추가**를 클릭하여 관리 서비스 ID를 **소유자** 역할에 추가합니다. 이렇게 하려면 **권한 추가** 패널의 **선택** 필드에서 웹 응용 프로그램의 이름을 검색한 다음, 해당 항목을 클릭합니다. 그런 다음 **Save**를 클릭합니다.

![](./media/service-bus-managed-service-identity/msi2.png)
 
이제 웹 응용 프로그램의 관리 서비스 ID가 Service Bus 네임스페이스 및 이전에 만든 큐에 액세스할 수 있습니다. 

### <a name="run-the-app"></a>앱 실행

이제 만든 ASP.NET 응용 프로그램의 기본 페이지를 수정합니다. [이 GitHub 리포지토리](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ManagedServiceIdentity)에서 웹 응용 프로그램 코드를 사용할 수도 있습니다.

Default.aspx 페이지가 방문 페이지입니다. 코드는 Default.aspx.cs 파일에서 찾을 수 있습니다. 그 결과 몇 가지 입력 필드와 메시지를 보내거나 받기 위해 Service Bus에 연결되는 **전송** 및 **수신** 단추가 있는 최소 웹 응용 프로그램이 생성됩니다.

[MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) 개체가 어떻게 초기화되는지에 유의합니다. 이 코드는 SAS(공유 액세스 토큰) 토큰 공급자를 사용하는 대신, `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.ServiceBusAudience)` 호출을 사용하여 관리 서비스 ID에 대한 토큰 공급자를 만듭니다. 이와 같이 유지하고 사용하는 데 특별한 비결은 없습니다. Service Bus 및 권한 부여 핸드셰이크에 대한 관리 서비스 ID 컨텍스트의 흐름은 토큰 공급자가 자동으로 처리합니다. 이것은 SAS를 사용하는 것보다 좀 더 간단한 모델입니다.

이와 같이 변경한 후에는 응용 프로그램을 게시하고 실행합니다. 올바른 게시 데이터를 가져오는 쉬운 방법은 Visual Studio에서 게시 프로필을 다운로드한 다음 가져오는 것입니다.

![](./media/service-bus-managed-service-identity/msi3.png)
 
메시지를 보내거나 받으려면 만든 엔터티의 이름과 네임스페이스의 이름을 입력한 후 **전송** 또는 **수신**을 클릭합니다.
 
관리 서비스 ID는 Azure 환경 및 구성한 App Service 배포에서만 작동합니다. 또한 관리 서비스 ID는 현재 App Service 배포 슬롯에서 작동하지 않습니다.

## <a name="next-steps"></a>다음 단계

Service Bus 메시징에 대해 자세히 알아보려면 다음 항목을 참조하세요.

* [Service Bus 기본 사항](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus 토픽 및 구독을 사용하는 방법](service-bus-dotnet-how-to-use-topics-subscriptions.md)