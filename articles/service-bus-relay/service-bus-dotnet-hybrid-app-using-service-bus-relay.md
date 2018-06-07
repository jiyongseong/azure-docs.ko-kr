---
title: "Azure WCF Relay 하이브리드 온-프레미스/클라우드 응용 프로그램(.NET) | Microsoft Docs"
description: "Azure WCF 릴레이를 사용하여 .NET 온-프레미스/클라우드 하이브리드 응용 프로그램을 만드는 방법에 대해 알아봅니다."
services: service-bus-relay
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 9ed02f7c-ebfb-4f39-9c97-b7dc15bcb4c1
ms.service: service-bus-relay
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 11/02/2017
ms.author: sethm
ms.openlocfilehash: fe13de5f83fe18a85f8d46d0eee039159e1a60e9
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2018
---
# <a name="net-on-premisescloud-hybrid-application-using-azure-wcf-relay"></a>Azure WCF 릴레이를 사용하는 .NET 온-프레미스/클라우드 하이브리드 응용 프로그램

이 문서에서는 Microsoft Azure 및 Visual Studio로 하이브리드 클라우드 응용 프로그램을 구축하는 방법을 보여줍니다. 이 자습서에서는 이전에 Azure를 사용한 경험이 없다고 가정합니다. 30분 이내에 여러 Azure 리소스를 사용하는 응용 프로그램을 클라우드에서 실행할 수 있습니다.

다음 내용을 배웁니다.

* 웹 서비스를 만들거나 기존 웹 서비스를 웹 솔루션에서 사용할 수 있도록 변경하는 방법
* Azure WCF 릴레이 서비스를 사용하여 Azure 응용 프로그램과 다른 위치에서 호스트되는 웹 서비스 사이에 데이터를 공유하는 방법

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="how-azure-relay-helps-with-hybrid-solutions"></a>하이브리드 솔루션에 유용한 Azure 릴레이

비즈니스 솔루션은 일반적으로 새로운 고유 비즈니스 요구 사항에 부합하도록 작성된 사용자 지정 코드와 이미 있는 솔루션 및 시스템에서 제공하는 기존 기능의 조합으로 구성됩니다.

솔루션 설계자는 확장 요구 사항을 더 간편하게 처리하고 운영 비용을 낮추기 위해 클라우드를 사용하기 시작했습니다. 클라우드를 사용하면 솔루션의 구성 요소로 활용할 기존 서비스 자산이 회사 방화벽 내부에 위치하게 되며 클라우드 솔루션에서 이 자산에 쉽게 액세스할 수 없게 됩니다. 다수의 내부 서비스는 회사 네트워크 가장자리에서 쉽게 노출될 수 있는 방식으로 빌드되거나 호스트되지 않습니다.

[Azure Relay](https://azure.microsoft.com/services/service-bus/)는 회사 네트워크 인프라에 대한 침입적인 변경 없이도 회사 범위 외부에 있는 솔루션에서 기존 WCF(Windows Communication Foundation) 웹 서비스에 안전하게 액세스할 수 있게 만드는 사용 사례를 위해 고안되었습니다. 이러한 릴레이 서비스는 기존 환경 내에 계속 호스트되지만 클라우드 호스트 릴레이 서비스에 들어오는 세션 및 요청에 대한 수신을 위임합니다. 또한 Azure Relay는 [SAS(공유 액세스 서명](../service-bus-messaging/service-bus-sas.md)) 인증을 사용하여 이러한 서비스에 무단으로 액세스하지 못하도록 보호합니다.

## <a name="solution-scenario"></a>솔루션 시나리오
이 자습서에서는 제품 재고 페이지에 제품 목록을 표시할 수 있는 ASP.NET 웹 사이트를 만듭니다.

![][0]

이 자습서는 기존 온-프레미스 시스템에 제품 정보가 있으며 해당 시스템에 도달하는 데 Azure 릴레이를 사용하는 개발자를 대상으로 합니다. 간단한 콘솔 응용 프로그램에서 실행되며 메모리 내 제품 집합에서 지원되는 웹 서비스에서 이를 시뮬레이션합니다. 개발자는 자신의 컴퓨터에서 이 콘솔 응용 프로그램을 실행하여 Azure에 웹 역할을 배포할 수 있습니다. 이렇게 함으로써, 개발자의 컴퓨터가 하나 이상의 방화벽 및 NAT(Network Address Translation) 계층 뒤에 위치하는 것이 거의 확실한 경우에도 Azure 데이터 센터에서 실행 중인 웹 역할이 실제로 개발자의 컴퓨터에 호출되는 것을 확인하게 됩니다.

## <a name="set-up-the-development-environment"></a>개발 환경 설정

Azure 응용 프로그램 개발을 시작하려면 먼저 도구를 다운로드하고 개발 환경을 설정해야 합니다.

1. SDK [다운로드 페이지](https://azure.microsoft.com/downloads/)에서 .NET용 Azure SDK를 설치합니다.
2. **.NET** 열에서 사용 중인 [Visual Studio](http://www.visualstudio.com) 버전을 클릭합니다. 이 자습서의 단계에서는 Visual Studio 2017을 사용합니다.
3. 설치 관리자를 실행할지 또는 저장할지를 묻는 메시지가 표시되면 **실행**을 클릭합니다.
4. **웹 플랫폼 설치 관리자**에서 **설치**를 클릭하여 설치를 계속합니다.
5. 설치가 완료되면 앱을 개발하기 시작하는 데 필요한 내용이 모두 준비된 것입니다. SDK에는 Visual Studio에서 Azure 응용 프로그램을 쉽게 개발할 수 있는 도구가 포함되어 있습니다.

## <a name="create-a-namespace"></a>네임스페이스 만들기

Azure에서 릴레이 기능 사용을 시작하려면 먼저 서비스 네임스페이스를 만들어야 합니다. 네임스페이스는 응용 프로그램 내에서 Azure 리소스의 주소를 지정하기 위한 범위 컨테이너를 제공합니다. [여기의 지침](relay-create-namespace-portal.md)을 따라 릴레이 네임스페이스를 만듭니다.

## <a name="create-an-on-premises-server"></a>온-프레미스 서버 만들기

먼저, (모의) 온-프레미스 제품 카탈로그 시스템을 빌드합니다. 매우 간단합니다. 통합하려는 전체 서비스 표면이 있는 실제 온-프레미스 제품 카탈로그 시스템을 나타내는 것으로 생각하면 됩니다.

이 프로젝트는 Visual Studio 콘솔 응용 프로그램으로, [Azure Service Bus NuGet 패키지](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)를 사용하여 Service Bus 라이브러리 및 구성 설정을 포함합니다.

### <a name="create-the-project"></a>프로젝트 만들기

1. 관리자 권한을 사용하여 Microsoft Visual Studio를 시작합니다. 이렇게 하려면 Visual Studio 프로그램 아이콘을 마우스 오른쪽 단추로 클릭하고 **관리자 권한으로 실행**을 클릭합니다.
2. Visual Studio의 **파일** 메뉴에서 **새로 만들기**를 클릭한 다음 **프로젝트**를 클릭합니다.
3. **설치된 템플릿**의 **Visual C#**에 있는 **콘솔 앱(.NET Framework)**을 클릭합니다. **이름** 상자에서 이름을 **ProductsServer**로 입력합니다.

   ![][11]
4. **확인**을 클릭하여 **ProductsServer** 프로젝트를 만듭니다.
5. Visual Studio용 NuGet 패키지 관리자를 이미 설치한 경우 다음 단계로 건너뜁니다. 그렇지 않으면 [NuGet][NuGet]을 방문하여 [NuGet 설치](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)를 클릭합니다. 나타나는 메시지에 따라 NuGet 패키지 관리자를 설치한 후 Visual Studio를 다시 시작합니다.
6. 솔루션 탐색기에서 **ProductsServer** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리**를 클릭합니다.
7. **찾아보기** 탭을 클릭한 다음 **WindowsAzure.ServiceBus**를 검색합니다. **WindowsAzure.ServiceBus** 패키지를 선택합니다.
8. **설치**를 클릭하고 사용 약관에 동의합니다.

   ![][13]

   필요한 클라이언트 어셈블리가 이제 참조됩니다.
8. 제품 계약의 새 클래스를 추가합니다. 솔루션 탐색기에서 **ProductsServer** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 다음 **클래스**를 클릭합니다.
9. **이름** 상자에서 이름을 **ProductsContract.cs**로 입력합니다. 그런 다음 **추가**를 클릭합니다.
10. **ProductsContract.cs**에서 네임스페이스 정의를 다음 코드로 바꿉니다. 이 코드는 서비스의 계약을 정의합니다.

    ```csharp
    namespace ProductsServer
    {
        using System.Collections.Generic;
        using System.Runtime.Serialization;
        using System.ServiceModel;

        // Define the data contract for the service
        [DataContract]
        // Declare the serializable properties.
        public class ProductData
        {
            [DataMember]
            public string Id { get; set; }
            [DataMember]
            public string Name { get; set; }
            [DataMember]
            public string Quantity { get; set; }
        }

        // Define the service contract.
        [ServiceContract]
        interface IProducts
        {
            [OperationContract]
            IList<ProductData> GetProducts();

        }

        interface IProductsChannel : IProducts, IClientChannel
        {
        }
    }
    ```
11. Program.cs에서 네임스페이스 정의를 다음 코드로 바꿉니다. 이 코드는 프로필 서비스 및 그에 대한 호스트를 추가합니다.

    ```csharp
    namespace ProductsServer
    {
        using System;
        using System.Linq;
        using System.Collections.Generic;
        using System.ServiceModel;

        // Implement the IProducts interface.
        class ProductsService : IProducts
        {

            // Populate array of products for display on website
            ProductData[] products =
                new []
                    {
                        new ProductData{ Id = "1", Name = "Rock",
                                         Quantity = "1"},
                        new ProductData{ Id = "2", Name = "Paper",
                                         Quantity = "3"},
                        new ProductData{ Id = "3", Name = "Scissors",
                                         Quantity = "5"},
                        new ProductData{ Id = "4", Name = "Well",
                                         Quantity = "2500"},
                    };

            // Display a message in the service console application
            // when the list of products is retrieved.
            public IList<ProductData> GetProducts()
            {
                Console.WriteLine("GetProducts called.");
                return products;
            }

        }

        class Program
        {
            // Define the Main() function in the service application.
            static void Main(string[] args)
            {
                var sh = new ServiceHost(typeof(ProductsService));
                sh.Open();

                Console.WriteLine("Press ENTER to close");
                Console.ReadLine();

                sh.Close();
            }
        }
    }
    ```
12. 솔루션 탐색기에서 **App.config** 파일을 두 번 클릭하여 Visual Studio 편집기에서 엽니다. `<system.ServiceModel>` 요소의 아래쪽에서(여전히 `<system.ServiceModel>` 내에 있음) 다음 XML 코드를 추가합니다. *yourServiceNamespace*를 네임스페이스의 이름으로 바꾸고 *yourKey*를 앞에서 포털에서 검색한 SAS 키로 바꿔야 합니다.

    ```xml
    <system.serviceModel>
    ...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```
    "transportClientEndpointBehavior"에서 발생한 오류는 단지 경고이며 이 샘플에 대한 차단 문제가 아닙니다.
    
13. 계속 App.config의 `<appSettings>` 요소에서 연결 문자열 값을 포털에서 이전에 얻은 연결 문자열로 바꿉니다.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```
14. **Ctrl+Shift+B** 키를 누르거나 **빌드** 메뉴에서 **솔루션 빌드**를 클릭하여 응용 프로그램을 빌드한 후 지금까지 진행한 작업의 정확도를 확인합니다.

## <a name="create-an-aspnet-application"></a>ASP.NET 응용 프로그램 만들기

이 섹션에서는 제품 서비스에서 검색한 데이터를 표시하는 간단한 ASP.NET 응용 프로그램을 빌드합니다.

### <a name="create-the-project"></a>프로젝트 만들기

1. 관리자 권한으로 Visual Studio 2013을 실행 중인지 확인합니다.
2. Visual Studio의 **파일** 메뉴에서 **새로 만들기**를 클릭한 다음 **프로젝트**를 클릭합니다.
3. **설치된 템플릿**에서 **Visual C#** 아래에 있는 **ASP.NET 웹 응용 프로그램(.NET Framework)**을 클릭합니다. 프로젝트의 이름을 **ProductsPortal**로 지정합니다. 그런 후 **OK**를 클릭합니다.

   ![][15]

4. **새 ASP.NET 웹 응용 프로그램** 대화 상자의 **ASP.NET 템플릿** 목록에서 **MVC**를 클릭합니다.

   ![][16]

6. **인증 변경** 단추를 클릭합니다. **인증 변경** 대화 상자에서 **인증 없음**을 선택했는지 확인한 다음 **확인**을 클릭합니다. 이 자습서의 경우 사용자 로그인이 필요하지 않은 앱을 배포하고 있습니다.

    ![][18]

7. **새 ASP.NET 웹 응용 프로그램** 대화 상자로 돌아와서 **확인**을 클릭하여 MVC 앱을 만듭니다.
8. 이제 새 웹앱에 대한 Azure 리소스를 구성해야 합니다. [이 문서의 Azure 섹션에 게시](../app-service/app-service-web-get-started-dotnet-framework.md#publish-to-azure)의 단계에 따릅니다. 그런 다음, 이 자습서로 돌아가 다음 단계를 진행합니다.
10. 솔루션 탐색기에서 **모델**을 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 다음 **클래스**를 클릭합니다. **이름** 상자에서 이름을 **Product.cs**로 입력합니다. 그런 다음 **추가**를 클릭합니다.

    ![][17]

### <a name="modify-the-web-application"></a>웹 응용 프로그램 수정

1. Visual Studio의 Product.cs 파일에서 기존 네임스페이스 정의를 다음 코드로 바꿉니다.

   ```csharp
    // Declare properties for the products inventory.
    namespace ProductsWeb.Models
    {
       public class Product
       {
           public string Id { get; set; }
           public string Name { get; set; }
           public string Quantity { get; set; }
       }
    }
    ```
2. 솔루션 탐색기에서 **컨트롤러** 폴더를 확장하고 **HomeController.cs**를 두 번 클릭하여 Visual Studio에서 엽니다.
3. **HomeController.cs**에서 기존 네임스페이스 정의를 다음 코드로 바꿉니다.

    ```csharp
    namespace ProductsWeb.Controllers
    {
        using System.Collections.Generic;
        using System.Web.Mvc;
        using Models;

        public class HomeController : Controller
        {
            // Return a view of the products inventory.
            public ActionResult Index(string Identifier, string ProductName)
            {
                var products = new List<Product>
                    {new Product {Id = Identifier, Name = ProductName}};
                return View(products);
            }
         }
    }
    ```
4. 솔루션 탐색기에서 Views\Shared 폴더를 확장하고 **_Layout.cshtml**을 두 번 클릭하여 Visual Studio 편집기에서 엽니다.
5. **내 ASP.NET 응용 프로그램**과 일치하는 모든 항목을 **Northwind Traders 제품**으로 변경합니다.
6. **홈**, **정보** 및 **연락처** 링크를 제거합니다. 다음 예제에서 강조 표시된 코드를 삭제합니다.

    ![][41]

7. 솔루션 탐색기에서 Views\Home 폴더를 확장하고 **Index.cshtml**을 두 번 클릭하여 Visual Studio 편집기에서 엽니다. 파일의 전체 내용을 다음 코드로 바꿉니다.

   ```html
   @model IEnumerable<ProductsWeb.Models.Product>

   @{
            ViewBag.Title = "Index";
   }

   <h2>Prod Inventory</h2>

   <table>
             <tr>
                 <th>
                     @Html.DisplayNameFor(model => model.Name)
                 </th>
                 <th></th>
                 <th>
                     @Html.DisplayNameFor(model => model.Quantity)
                 </th>
             </tr>

   @foreach (var item in Model) {
             <tr>
                 <td>
                     @Html.DisplayFor(modelItem => item.Name)
                 </td>
                 <td>
                     @Html.DisplayFor(modelItem => item.Quantity)
                 </td>
             </tr>
   }

   </table>
   ```
8. 지금까지 진행한 작업의 정확도를 확인하려면 **Ctrl+Shift+B**를 눌러 프로젝트를 빌드하면 됩니다.

### <a name="run-the-app-locally"></a>로컬에서 앱 실행

응용 프로그램을 실행하여 작동하는지 확인합니다.

1. **ProductsPortal**이 활성 프로젝트인지 확인합니다. 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 선택합니다.
2. Visual Studio에서 **F5** 키를 누릅니다.
3. 응용 프로그램이 브라우저에 실행되는 것으로 나타나야 합니다.

   ![][21]

## <a name="put-the-pieces-together"></a>부분 연결

다음 단계는 온-프레미스 제품 서버를 ASP.NET 응용 프로그램과 연결하는 것입니다.

1. 아직 열려 있지 않은 경우 [ASP.NET 응용 프로그램 만들기](#create-an-aspnet-application) 섹션에서 만든 **ProductsPortal** 프로젝트를 Visual Studio에서 다시 엽니다.
2. "온-프레미스 서버 만들기" 섹션의 단계와 비슷하게 프로젝트 참조에 NuGet 패키지를 추가합니다. 솔루션 탐색기에서 **ProductsPortal** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음, **NuGet 패키지 관리**를 클릭합니다.
3. **WindowsAzure.ServiceBus**를 검색하고 **WindowsAzure.ServiceBus** 항목을 선택합니다. 그런 다음 설치를 완료하고 이 대화 상자를 닫습니다.
4. 솔루션 탐색기에서 **ProductsPortal** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 후 **기존 항목**을 클릭합니다.
5. **ProductsServer** 콘솔 프로젝트에서 **ProductsContract.cs** 파일로 이동합니다. ProductsContract.cs를 클릭하여 강조 표시합니다. **추가** 옆의 아래쪽 화살표를 클릭한 다음 **링크로 추가**를 클릭합니다.

   ![][24]

6. 이제 Visual Studio 편집기에서 **HomeController.cs** 파일을 열고 네임스페이스 정의를 다음 코드로 바꿉니다. *yourServiceNamespace*를 서비스 네임스페이스의 이름으로 바꾸고 *yourKey*를 SAS 키로 바꿔야 합니다. 이렇게 하면 클라이언트에서 온-프레미스 서비스를 호출하여 호출 결과를 반환합니다.

   ```csharp
   namespace ProductsWeb.Controllers
   {
       using System.Linq;
       using System.ServiceModel;
       using System.Web.Mvc;
       using Microsoft.ServiceBus;
       using Models;
       using ProductsServer;

       public class HomeController : Controller
       {
           // Declare the channel factory.
           static ChannelFactory<IProductsChannel> channelFactory;

           static HomeController()
           {
               // Create shared access signature token credentials for authentication.
               channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                   "sb://yourServiceNamespace.servicebus.windows.net/products");
               channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                   TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                       "RootManageSharedAccessKey", "yourKey") });
           }

           public ActionResult Index()
           {
               using (IProductsChannel channel = channelFactory.CreateChannel())
               {
                   // Return a view of the products inventory.
                   return this.View(from prod in channel.GetProducts()
                                    select
                                        new Product { Id = prod.Id, Name = prod.Name,
                                            Quantity = prod.Quantity });
               }
           }
       }
   }
   ```
7. 솔루션 탐색기에서 **ProductsPortal** 솔루션을 마우스 오른쪽 단추로 클릭합니다(프로젝트가 아닌 솔루션을 마우스 오른쪽 단추로 클릭해야 함). **추가**를 클릭한 후 **기존 프로젝트**를 클릭합니다.
8. **ProductsServer** 프로젝트로 이동한 후 **ProductsServer.csproj** 솔루션 파일을 두 번 클릭하여 추가합니다.
9. **ProductsPortal**에 데이터를 표시하기 위해서는 **ProductsServer**가 실행 중이어야 합니다. 솔루션 탐색기에서 **ProductsPortal** 솔루션을 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다. **속성 페이지** 대화 상자가 표시됩니다.
10. 왼쪽에서 **시작 프로젝트**를 클릭합니다. 오른쪽에서 **여러 시작 프로젝트**를 클릭합니다. **ProductsServer** 및 **ProductsPortal** 모두에 대한 작업으로 **시작**이 설정되어 순서대로 나타나는지 확인합니다.

      ![][25]

11. **속성** 대화 상자에서 왼쪽에 있는 **프로젝트 종속성**을 클릭합니다.
12. **프로젝트** 목록에서 **ProductsServer**를 클릭합니다. **ProductsPortal**이 선택되지 않았는지 확인합니다.
13. **프로젝트** 목록에서 **ProductsPortal**을 클릭합니다. **ProductsServer**가 선택되어 있는지 확인합니다.

    ![][26]

14. **속성 페이지** 대화 상자에서 **확인**을 클릭합니다.

## <a name="run-the-project-locally"></a>로컬로 프로젝트 실행

응용 프로그램을 로컬로 테스트하려면 Visual Studio에서 **F5** 키를 누릅니다. 온-프레미스 서버(**ProductsServer**)가 먼저 시작된 후 브라우저 창에서 **ProductsPortal** 응용 프로그램이 시작되어야 합니다. 이제 제품 서비스 온-프레미스 시스템에서 검색된 제품 재고 목록 데이터가 표시됩니다.

![][10]

**ProductsPortal** 페이지에서 **새로 고침**을 누릅니다. 페이지를 새로 고칠 때마다 **ProductsServer**에서 `GetProducts()`가 호출되면 서버 앱에 메시지가 표시되는 것을 확인하게 됩니다.

다음 단계를 진행하기 전에 응용 프로그램을 모두 닫습니다.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>Azure 웹앱에 ProductsPortal 프로젝트 배포

다음 단계는 **ProductsPortal** 프런트 엔드를 Azure 웹앱으로 다시 게시하는 것입니다. 다음을 수행합니다.

1. 솔루션 탐색기에서 **ProductsPortal** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다. **게시** 페이지에서 **게시**를 클릭합니다.

  > [!NOTE]
  > 배포 후 **ProductsPortal** 웹 프로젝트가 자동으로 시작되면 브라우저 창에 오류 메시지가 표시될 수 있습니다. 예상된 동작이며 **ProductsServer** 응용 프로그램이 아직 실행되지 않기 때문에 발생합니다.
>
>

2. 다음 단계에서 URL이 필요하므로 배포된 웹앱의 URL을 복사합니다. 또한 Visual Studio의 Azure App Service 활동 창에서 이 URL을 가져올 수도 있습니다.

  ![][9]

3. 실행 중인 응용 프로그램을 중지하려면 브라우저 창을 닫습니다.

### <a name="set-productsportal-as-web-app"></a>웹앱으로 ProductsPortal 설정

클라우드에서 응용 프로그램을 실행하기 전에 Visual Studio 내에서 **ProductsPortal**이 웹앱으로 시작되는지 확인해야 합니다.

1. Visual Studio에서 **ProductsPortal** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **속성**을 클릭합니다.
2. 왼쪽 열에서 **웹**을 클릭합니다.
3. **작업 시작** 섹션에서 **시작 URL** 단추를 클릭하고 텍스트 상자에 이전에 배포한 웹앱의 URL을 입력합니다(예: `http://productsportal1234567890.azurewebsites.net/`).

    ![][27]

4. Visual Studio의 **파일** 메뉴에서 **모두 저장**을 클릭합니다.
5. Visual Studio의 빌드 메뉴에서 **솔루션 다시 빌드**를 클릭합니다.

## <a name="run-the-application"></a>응용 프로그램 실행

1. F5를 눌러 응용 프로그램을 빌드 및 실행합니다. 온-프레미스 서버(**ProductsServer** 콘솔 응용 프로그램)가 먼저 시작된 후에 아래 스크린샷과 같이 **ProductsPortal** 응용 프로그램이 브라우저 창에서 시작되어야 합니다. 다시, 제품 재고에 제품 서비스 온-프레미스 시스템에서 검색된 데이터가 나열되며 해당 데이터가 웹앱에 표시됩니다. URL을 확인하여 **ProductsPortal**이 클라우드에서 Azure 웹앱으로 실행 중인지 확인합니다.

   ![][1]

   > [!IMPORTANT]
   > **ProductsServer** 콘솔 응용 프로그램이 실행 중이어야 하며 **ProductsPortal** 응용 프로그램에 데이터를 제공할 수 있어야 합니다. 브라우저에 오류가 표시되면 **ProductsServer**가 다음 메시지를 로드하고 표시할 때까지 몇 초 기다립니다. 그런 다음 브라우저에서 **새로 고침**을 누릅니다.
   >
   >

   ![][37]
2. 브라우저로 돌아가 **ProductsPortal** 페이지에서 **새로 고침**을 누릅니다. 페이지를 새로 고칠 때마다 **ProductsServer**에서 `GetProducts()`가 호출되면 서버 앱에 메시지가 표시되는 것을 확인하게 됩니다.

    ![][38]

## <a name="next-steps"></a>다음 단계

Azure 릴레이에 대한 자세한 내용은 다음 리소스를 참조하세요.  

* [Azure 릴레이란?](relay-what-is-it.md)  
* [Azure 릴레이 사용 방법](relay-wcf-dotnet-get-started.md)  

[0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
[1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[NuGet]: http://nuget.org

[11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
[13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
[15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
[16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
[17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.png
[18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-5.png
[9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-9.png
[10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App3.png

[21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App1.png
[24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
[25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
[26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
[27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-8.png

[36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
[38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
[41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
[43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
