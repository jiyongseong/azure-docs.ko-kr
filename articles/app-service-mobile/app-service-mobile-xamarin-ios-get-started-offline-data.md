---
title: "Azure 모바일 앱(Xamarin iOS)에 대해 오프라인 동기화 사용"
description: "App Service 모바일 앱을 사용하여 Xamarin iOS 응용 프로그램에서 오프라인 데이터를 캐시 및 동기화하는 방법을 알아봅니다."
documentationcenter: xamarin
author: conceptdev
manager: cfowler
editor: 
services: app-service\mobile
ms.assetid: 828a287c-5d58-4540-9527-1309ebb0f32b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 287977d55656a4b2bc42d90730d16f522fae9b9e
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2018
---
# <a name="enable-offline-sync-for-your-xamarinios-mobile-app"></a>Xamarin iOS 모바일 앱에 대해 오프라인 동기화 사용
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>개요
이 자습서에서는 Xamarin.iOS용 Azure Mobile Apps의 오프라인 동기화 기능을 소개합니다. 오프라인 동기화를 사용하면 최종 사용자는 네트워크에 연결되어 있지 않을 때도 모바일 앱과 데이터 보기, 추가 또는 수정과 같은 상호 작용을 수행할 수 있습니다. 변경 내용은 로컬 데이터베이스에 저장됩니다. 장치가 다시 온라인 상태가 되면 이러한 변경 내용이 원격 서비스와 동기화됩니다.

이 자습서에서는 [Xamarin iOS 앱 만들기] 에서 Xamarin.iOS 앱 프로젝트를 업데이트하여 Azure Mobile Apps의 오프라인 기능을 지원합니다. 다운로드한 빠른 시작 서버 프로젝트를 사용하지 않는 경우 프로젝트에 데이터 액세스 확장 패키지를 추가해야 합니다. 서버 확장 패키지에 대한 자세한 내용은 [Azure Mobile Apps용 .NET 백 엔드 서버 SDK 사용](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)을 참조하세요.

오프라인 동기화 기능에 대한 자세한 내용은 [증분 동기화]항목을 참조하세요.

## <a name="update-the-client-app-to-support-offline-features"></a>오프라인 기능을 지원하도록 클라이언트 앱 업데이트
Azure 모바일 앱 오프라인 기능을 사용하면 오프라인 시나리오에서 로컬 데이터베이스를 조작할 수 있습니다. 앱에서 이러한 기능을 사용하려면 로컬 저장소에서 [SyncContext]를 초기화합니다. [IMobileServiceSyncTable] 인터페이스를 통해 테이블을 참조합니다. SQLite는 장치의 로컬 저장소로 사용됩니다.

1. [Xamarin iOS 앱 만들기] 자습서에서 완료한 프로젝트의 NuGet 패키지 관리자를 열고 **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet 패키지를 검색한 후 설치합니다.
2. QSTodoService.cs 파일을 열고 `#define OFFLINE_SYNC_ENABLED` 정의의 주석 처리를 제거합니다.
3. 클라이언트 앱을 다시 빌드하고 실행합니다. 오프라인 동기화를 활성화하기 전에 수행한 것과 동일하게 앱이 작동합니다. 그러나 로컬 데이터베이스는 이제 오프라인 시나리오에서 사용할 수 있는 데이터로 채워집니다.

## <a name="update-sync"></a>앱을 업데이트하여 백 엔드에서 분리
이 섹션에서는 모바일 앱 백 엔드에 대한 연결을 끊고 오프라인 상황을 시뮬레이션합니다. 데이터 항목을 추가하면 예외 처리기는 앱이 오프라인 모드임을 사용자에게 알립니다. 이 상태에서 로컬 저장소에 추가된 새 항목은 푸시가 연결된 상태에서 다음에 실행될 경우 모바일 앱 백 엔드에 동기화됩니다.

1. 공유 프로젝트에서 QSToDoService.cs를 편집합니다. 잘못된 URL을 가리키도록 **applicationURL**을 변경합니다.

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    장치에서 Wi-Fi 및 셀룰러 네트워크를 사용하지 않도록 설정하여 오프라인 동작을 시연하거나 비행기 모드를 사용할 수 있습니다.
2. 앱을 빌드 및 실행합니다. 앱을 시작하는 경우 동기화는 새로 고침에 실패합니다.
3. 새 항목을 입력하고 **저장** 을 클릭할 때마다 푸시가 [CancelledByNetworkError] 상태로 실패하는지 확인합니다. 그러나 새 todo 항목은 모바일 앱 백 엔드에 푸시할 수 있을 때까지 로컬 저장소에 위치합니다.  프로덕션 앱에서 이러한 예외를 무시하는 경우 클라이언트 앱은 모바일 앱 백 엔드에 연결된 것처럼 작동합니다.
4. 앱을 닫았다가 다시 시작하여 만든 새 항목이 로컬 저장소에 유지되는지 확인합니다.
5. (선택 사항) PC에 Visual Studio가 설치되어 있으면 **서버 탐색기**를 엽니다. **Azure**-> **SQL Databases**에 있는 데이터베이스로 이동합니다. 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **SQL Server 개체 탐색기에서 열기**를 선택합니다. 이제 SQL 데이터베이스 테이블 및 콘텐츠를 찾아볼 수 있습니다. 백 엔드 데이터베이스의 데이터가 변경되지 않은 것을 확인합니다.
6. (옵션) Fiddler 또는 Postman과 같은 REST 도구를 사용하여 `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`형식의 GET 쿼리를 통해 모바일 백 엔드를 쿼리합니다.

## <a name="update-online-app"></a>모바일 앱 백 엔드를 다시 연결하도록 앱 업데이트
이 섹션에서는 앱을 Mobile App 백 엔드에 다시 연결합니다. 여기서는 모바일 앱 백 엔드를 사용하여 오프라인 상태에서 온라인 상태로 전환되는 앱을 시뮬레이트합니다.   네트워크 연결을 해제하여 네트워크 손상을 시뮬레이트하는 경우에는 코드를 변경할 필요가 없습니다.
네트워크 다시 켭니다.  응용 프로그램을 처음 실행하는 경우 `RefreshDataAsync` 메서드가 호출됩니다. 차례로 `SyncAsync`가 호출되고 백 엔드 데이터베이스와 로컬 저장소가 동기화됩니다.

1. 공유 프로젝트에서 QSToDoService.cs를 열고 **applicationURL**의 변경 내용을 되돌립니다.
2. 앱을 다시 빌드하고 실행합니다. 앱은 푸시 및 끌어오기 작업을 사용하여 `OnRefreshItemsSelected` 메서드가 실행되면 로컬 변경 내용을 Azure Mobile App 백 엔드와 동기화합니다.
3. (선택 사항) SQL Server 개체 탐색기 또는 Fiddler와 같은 REST 도구를 사용하여 업데이트된 데이터를 봅니다. Azure 모바일 앱 백 엔드 데이터베이스와 로컬 저장소 간에 데이터는 동기화되었습니다.
4. 앱에서 로컬 저장소에서 완료할 몇 개 항목 옆의 확인란을 클릭합니다.

   `CompleteItemAsync`은 `SyncAsync`를 호출하여 Mobile App 백 엔드와 전체 항목을 동기화합니다. `SyncAsync` 는 푸시와 끌어오기를 둘 다 호출합니다.
   **클라이언트가 변경한 테이블에 끌어오기를 실행할 때마다 클라이언트 동기화 컨텍스트에 푸시가 항상 먼저 자동으로 실행됩니다**. 이러한 암시적 푸시를 통해 로컬 저장소의 모든 테이블 및 관계가 동기화된 상태로 유지될 수 있습니다. 이 동작에 대한 자세한 내용은 [증분 동기화]를 참조하세요.

## <a name="review-the-client-sync-code"></a>클라이언트 동기화 코드 검토
자습서 [Xamarin iOS 앱 만들기] 를 완료한 경우 다운로드한 Xamarin 클라이언트 프로젝트는 로컬 SQLite 데이터베이스를 사용하여 오프라인 동기화를 지원하는 코드를 포함합니다. 이미 자습서 코드에 포함된 내용에 대한 간략한 개요입니다. 기능의 개념적 개요는 [증분 동기화]를 참조하세요.

* 모든 테이블 작업을 수행하려면 먼저 로컬 저장소를 초기화해야 합니다. `QSTodoListViewController.ViewDidLoad()`가 `QSTodoService.InitializeStoreAsync()`를 실행하는 경우 로컬 저장소 데이터베이스를 초기화합니다. 이 메서드는 Azure Mobile App 클라이언트 SDK에서 제공하는 `MobileServiceSQLiteStore` 클래스를 사용하여 새 로컬 SQLite 데이터베이스를 만듭니다.

    `DefineTable` 메서드는 제공된 형식(이 경우 `ToDoItem`)의 필드와 일치하는 테이블을 로컬 저장소에 만듭니다. 이 형식은 원격 데이터베이스에 있는 열을 모두 포함하지 않아도 됩니다. 열의 하위 집합 저장은 불가능합니다.

        // QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }
* `QSTodoService`의 `todoTable` 멤버는 `IMobileServiceTable` 대신 `IMobileServiceSyncTable` 형식입니다. IMobileServiceSyncTable은 모든 만들기, 읽기, 업데이트 및 삭제(CRUD) 테이블 작업을 로컬 저장소 데이터베이스로 보냅니다.

    `IMobileServiceSyncContext.PushAsync()`를 호출하여 변경 내용이 Azure Mobile App 백 엔드로 푸시될 때를 결정합니다. 동기화 컨텍스트를 사용하면 모든 테이블의 변경 내용을 추적하고 밀어넣어서 테이블 관계를 보존할 수 있습니다. `PushAsync`를 호출하는 경우 클라이언트 앱을 수정합니다.

    제공되는 코드는 `QSTodoService.SyncAsync()` 를 호출하여 todoitem목록이 새로 고쳐지거나 todoitem이 더해지거나 완료될 때마다 동기화합니다. 로컬 변경이 수행될 때마다 앱이 동기화됩니다. 끌어오기가 컨텍스트에 의해 추적되는 로컬 업데이트를 보류 중인 테이블에 대해 실행되는 경우 끌어오기 작업은 먼저 자동으로 컨텍스트 푸시를 트리거합니다.

    제공된 코드에서 원격 `TodoItem` 테이블에 있는 모든 레코드를 쿼리하지만 쿼리 ID 및 쿼리를 `PushAsync`로 전달하여 레코드를 필터링할 수도 있습니다. 자세한 내용은 *Azure Mobile Apps에서 오프라인 데이터 동기화* 에서 [증분 동기화]섹션을 참조하세요.

        // QSTodoService.cs
        public async Task SyncAsync()
        {
            try
            {
                await client.SyncContext.PushAsync();
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery()); // query ID is used for incremental sync
            }

            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
        }

## <a name="additional-resources"></a>추가 리소스
* [증분 동기화]
* [Azure Mobile Apps .NET SDK 사용 방법][8]

<!-- Images -->

<!-- URLs. -->
[Xamarin iOS 앱 만들기]: app-service-mobile-xamarin-ios-get-started.md
[증분 동기화]: app-service-mobile-offline-data-sync.md
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
