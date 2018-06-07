---
title: Azure AD iOS 시작 | Microsoft 문서
description: 로그인을 위해 Azure AD와 통합되고 OAuth를 사용하여 Azure AD로 보호되는 API를 호출하는 iOS 응용 프로그램 빌드 방법입니다.
services: active-directory
documentationcenter: ios
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 42303177-9566-48ed-8abb-279fcf1e6ddb
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/30/2018
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 1ceae59cca5790d9d74f72ce644e31fb0949cd49
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="azure-ad-ios-getting-started"></a>Azure AD iOS 시작
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

Azure AD(Azure Active Directory)는 보호된 리소스에 액세스해야 하는 iOS 클라이언트의 경우 Active Directory 인증 라이브러리 또는 ADAL을 제공합니다. ADAL은 앱에서 액세스 토큰을 가져오기 위해 사용하는 프로세스를 간소화합니다. 액세스 토큰을 얼마나 쉽게 가져올 수 있는지 보여 주기 위해 이 문서에서는 다음을 수행하는 Objective C 할 일 목록 응용 프로그램을 작성합니다.

* [OAuth 2.0 인증 프로토콜](https://msdn.microsoft.com/library/azure/dn645545.aspx)을 사용하여 Azure AD Graph API를 호출하기 위한 액세스 토큰을 가져옵니다.
* 지정된 별칭을 가진 사용자를 디렉터리에서 검색합니다.

완전하게 작동하는 응용 프로그램을 빌드하려면 다음 작업이 필요합니다.

1. Azure AD에 응용 프로그램을 등록합니다.
2. ADAL을 설치 및 구성합니다.
3. ADAL을 사용하여 Azure AD에서 토큰을 가져옵니다.

시작하려면 [앱 기본 사항을 다운로드](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip)하거나 [완성된 샘플을 다운로드](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip)하세요. 또한 사용자를 만들고 응용 프로그램을 등록할 수 있는 Azure AD 테넌트도 필요합니다. 테넌트가 아직 없는 경우 [얻는 방법을 알아보세요](active-directory-howto-tenant.md).


> [!TIP]
> 몇 분 안에 Azure AD를 실행할 수 있는 새로운 [개발자 포털](https://identity.microsoft.com/Docs/iOS)의 미리 보기를 사용해 보세요. 개발자 포털은 앱을 등록하고 코드에 Azure AD를 통합하는 과정을 안내합니다. 이 과정을 완료하면 테넌트에서 사용자를 인증할 수 있는 간단한 응용 프로그램 및 토큰을 수락하고 유효성 검사를 수행할 수 있는 백 엔드가 생성됩니다. 
> 
> 

## <a name="1-determine-what-your-redirect-uri-is-for-ios"></a>1. iOS에 대한 리디렉션 URI 결정
특정 SSO 시나리오에서 응용 프로그램을 안전하게 시작하려면, 특정 형식으로 *리디렉션 URI*를 만들어야 합니다. 리디렉션 URI는 토큰을 요청하는 올바른 응용 프로그램에 반환하는데 사용됩니다.


리디렉션 URI에 대한 iOS 형식은 다음과 같습니다.

```
<app-scheme>://<bundle-id>
```

* **app-scheme** - XCode 프로젝트에 등록됩니다. 다른 응용 프로그램이 사용자를 호출할 수 있는 방법입니다. Info.plist -> URL 형식 -> URL ID에 있을 수 있습니다. 하나 이상이 구성되어 있지 않으면 하나를 만들어야 합니다.
* **bundle-id** - XCode의 프로젝트 설정의 "ID" 아래에서 찾을 수 있는 번들 식별자입니다.

이 빠른 시작 코드는 ***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***를 예로 들 수 있습니다.

## <a name="2-register-the-directorysearcher-application"></a>2. DirectorySearcher 응용 프로그램 등록
앱에서 토큰을 가져오도록 설정하려면 먼저 Azure AD 테넌트에 앱을 등록하고 Azure AD Graph API에 액세스할 수 있는 권한을 부여해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 위쪽 막대에서 계정을 클릭합니다. **디렉터리** 목록에서 응용 프로그램을 등록할 Active Directory 테넌트를 선택합니다.
3. 왼쪽 탐색 창에서 **모든 서비스**를 클릭한 다음, **Azure Active Directory**를 선택합니다.
4. **앱 등록**을 클릭하고 **추가**를 선택합니다.
5. 표시되는 메시지에 따라 새 **네이티브 클라이언트 응용 프로그램**을 만듭니다.
  * 응용 프로그램의 **이름**은 최종 사용자에게 응용 프로그램을 설명합니다.
  * **리디렉션 Uri**는 Azure AD가 토큰 응답을 반환하는 데 사용하는 구성표 및 문자열의 조합입니다. 응용 프로그램과 관련되고 이전 리디렉션 URI 정보를 바탕으로 한 값을 입력합니다.
6. 등록이 완료되면 Azure AD가 앱에 고유한 응용 프로그램 ID를 할당합니다. 이 값은 다음 섹션에서 필요하므로 응용 프로그램 탭에서 복사해 둡니다.
7. **설정** 페이지에서 **필요한 사용 권한**, **추가**를 차례로 선택합니다. **Microsoft Graph**를 API로 선택하고 **위임된 사용 권한**에서 **디렉터리 데이터 읽기** 사용 권한을 추가합니다. 그러면 Azure AD Graph API에서 사용자를 쿼리하도록 응용 프로그램이 설정됩니다.

## <a name="3-install-and-configure-adal"></a>3. ADAL 설치 및 구성
Azure AD에서 응용 프로그램이 있으므로 ADAL을 설치하고 ID 관련 코드를 작성할 수 있습니다. ADAL이 Azura AD와 통신할 수 있게 하려면, 앱 등록에 관한 일부 정보를 제공해야 합니다.

1. 먼저 CocoaPods를 사용하여 DirectorySearcher 프로젝트에 ADAL을 추가하여 시작합니다.

    ```
    $ vi Podfile
    ```
2. 이 podfile에 다음을 추가합니다.

    ```
    source 'https://github.com/CocoaPods/Specs.git'
    link_with ['QuickStart']
    xcodeproj 'QuickStart'

    pod 'ADALiOS'
    ```

3. 이제 CocoaPods를 사용하여 podfile을 로드합니다. 이 단계는 로드하는 새 XCode 작업 영역을 만듭니다.

    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

4. 빠른 시작 프로젝트에서.plist 파일 `settings.plist`을 엽니다. Azure Portal에 입력한 값을 반영하도록 섹션의 요소 값을 바꿉니다. 코드에서 ADAL을 사용할 때마다 이러한 값을 참조합니다.
  * `tenant`는 Azure AD 테넌트의 도메인(예: contoso.onmicrosoft.com)입니다.
  * `clientId` 는 포털에서 복사한 응용 프로그램의 클라이언트 ID입니다.
  * `redirectUri`는 포털에 등록한 리디렉션 URL입니다.

## <a name="4-use-adal-to-get-tokens-from-azure-ad"></a>4. ADAL을 사용하여 Azure AD에서 토큰 가져오기
ADAL에서 확인되는 기본 원칙은 액세스 토큰이 필요할 때마다 앱이 completionBlock `+(void) getToken : `을 호출하고 나머지 작업은 ADAL이 수행한다는 것입니다. 

1. `QuickStart` 프로젝트에서 `GraphAPICaller.m`를 열고 위쪽의 `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` 주석을 찾습니다. CompletionBlock을 통해 Azure AD와 통신하는 데 필요한 좌표를 ADAL에 전달하고 토큰 캐시 방법을 알려줍니다.

    ```ObjC
    +(void) getToken : (BOOL) clearCache
               parent:(UIViewController*) parent
    completionHandler:(void (^) (NSString*, NSError*))completionBlock;
    {
        AppData* data = [AppData getInstance];
        if(data.userItem){
            completionBlock(data.userItem.accessToken, nil);
            return;
        }

        ADAuthenticationError *error;
        authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
        authContext.parentController = parent;
        NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

        [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
        [authContext acquireTokenWithResource:data.resourceId
                                     clientId:data.clientId
                                  redirectUri:redirectUri
                               promptBehavior:AD_PROMPT_AUTO
                                       userId:data.userItem.userInformation.userId
                        extraQueryParameters: @"nux=1" // if this strikes you as strange it was legacy to display the correct mobile UX. You most likely won't need it in your code.
                             completionBlock:^(ADAuthenticationResult *result) {

                                  if (result.status != AD_SUCCEEDED)
                                  {
                                     completionBlock(nil, result.error);
                                  }
                                  else
                                  {
                                      data.userItem = result.tokenCacheStoreItem;
                                      completionBlock(result.tokenCacheStoreItem.accessToken, nil);
                                  }
                             }];
    }

    ```

2. 이제 이 토큰을 사용하여 그래프에서 사용자에 대해 검색해야 합니다. `// TODO: implement SearchUsersList` 설명을 찾습니다. 이 메서드는 Azure AD Graph API에 해당 UPN이 지정된 검색어로 시작하는 사용자를 쿼리하라는 GET 요청을 만듭니다. Azure AD Graph API를 쿼리하려면 요청의 `Authorization` 헤더에 access_token을 포함해야 합니다. 여기로 ADAL이 들어옵니다.

    ```ObjC
    +(void) searchUserList:(NSString*)searchString
                    parent:(UIViewController*) parent
          completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
    {
        if (!loadedApplicationSettings)
       {
            [self readApplicationSettings];
        }
        
        AppData* data = [AppData getInstance];

        NSString *graphURL = [NSString stringWithFormat:@"%@%@/users?api-version=%@&$filter=startswith(userPrincipalName, '%@')", data.taskWebApiUrlString, data.tenant, data.apiversion, searchString];

        [self craftRequest:[self.class trimString:graphURL]
                    parent:parent
         completionHandler:^(NSMutableURLRequest *request, NSError *error) {

             if (error != nil)
             {
                 completionBlock(nil, error);
             }
             else
             {

                 NSOperationQueue *queue = [[NSOperationQueue alloc]init];

                 [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                     if (error == nil && data != nil){

                         NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

                         // We can grab the JSON node at the top to get our graph data.
                         NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                         // Don't be thrown off by the key name being "value". It really is the name of the
                         // first node. :-)

                         // Each object is a key value pair
                         NSDictionary *keyValuePairs;
                         NSMutableArray* Users = [[NSMutableArray alloc]init];

                         for(int i =0; i < graphDataArray.count; i++)
                         {
                             keyValuePairs = [graphDataArray objectAtIndex:i];

                             User *s = [[User alloc]init];
                             s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                             s.name =[keyValuePairs valueForKey:@"givenName"];

                             [Users addObject:s];
                         }

                         completionBlock(Users, nil);
                     }
                     else
                     {
                         completionBlock(nil, error);
                     }

                }];
             }
         }];

    }

    ```


3. 앱에서 `getToken(...)`을 호출하여 토큰을 요청하면 ADAL은 사용자에게 자격 증명을 요구하지 않고 토큰을 반환하려고 시도합니다. ADAL은 사용자가 토큰을 가져오기 위해 로그인해야 한다고 판단할 경우 로그인 대화 상자를 표시하고, 사용자의 자격 증명을 수집하고, 인증 성공 후 토큰을 반환합니다. 어떤 이유로든 ADAL이 토큰을 반환할 수 없는 경우 `AdalException`을 throw합니다.

> [!Note] 
> `AuthenticationResult` 개체에는 사용자 앱에 필요할 수 있는 정보를 수집하는 데 사용할 수 있는 `tokenCacheStoreItem` 개체가 포함되어 있습니다. 빠른 시작에서 `tokenCacheStoreItem`은 인증이 이미 수행되었는지를 결정하는 데 사용됩니다.
>
>

## <a name="5-build-and-run-the-application"></a>5. 응용 프로그램 빌드 및 실행
축하합니다! 이제 사용자를 인증하고 OAuth 2.0을 사용하여 Web API를 안전하게 호출하고, 사용자에 대한 기본 정보를 가져올 수 있는 작동 중인 iOS 응용 프로그램이 작성되었습니다. 아직 일부 사용자로 테넌트를 채우지 않은 경우 지금 할 수 있습니다. 빠른 시작 앱을 실행하고 해당 사용자 중 하나로 로그인합니다. 해당 UPN에 따라 다른 사용자를 검색합니다. 앱을 닫은 다음 다시 시작합니다. 사용자의 세션이 그대로 유지되는 것을 확인합니다.

ADAL은 응용 프로그램에 이러한 모든 일반적인 ID 기능을 쉽게 통합할 수 있습니다. 또한 캐시 관리, OAuth 프로토콜 지원, 사용자에게 로그인 UI 제공, 만료된 토큰 새로 고침 등의 모든 귀찮은 작업을 대신 처리합니다. 실제로 알아두어야 할 모든 항목은 단일 API 호출, `getToken`입니다.

참조를 위해 완성된 샘플(사용자 구성 값 제외)이 [GitHub](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip)에 제공됩니다. 

## <a name="next-steps"></a>다음 단계
이제 추가 시나리오로 이동할 수 있습니다. 다음 작업을 시도할 수 있습니다.

* [Azure AD를 사용하여 Node.js Web API 보안 유지](active-directory-devquickstarts-webapi-nodejs.md)
* [ADAL을 사용하여 iOS에서 앱 간 SSO를 사용하도록 설정하는 방법](active-directory-sso-ios.md) 알아보기  

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

