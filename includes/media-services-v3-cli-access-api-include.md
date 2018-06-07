---
title: 포함 파일
description: 포함 파일
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 04/13/2018
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: acb9bdf294dd66005df203f957c155540b658698
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33830105"
---
## <a name="access-the-media-services-api"></a>Media Services API 액세스

Azure Media Services API에 연결하려면 Azure AD 서비스 주체 인증을 사용합니다. 다음 명령은 Azure AD 응용 프로그램을 만들고 계정에 서비스 주체를 연결합니다. 다음 단계와 같이 반환된 값을 사용하여 .NET 앱을 구성합니다.

스크립트를 실행하기 전에 `amsaccount` 및 `amsResourceGroup`을 이러한 리소스로 만들 때 선택한 이름으로 바꿀 수 있습니다. `amsaccount`는 서비스 주체를 연결하는 Azure Media Services 계정의 이름입니다. <br/>다음 명령은 app.config에 붙여넣을 수 있는 xml을 반환하는 `xml` 옵션을 사용합니다. `xml` 옵션을 생략할 경우 응답 형식은 `json`이 됩니다.

```azurecli-interactive
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```

이 명령은 다음과 유사한 응답을 생성합니다.

```xml
<add key="Region" value="West US 2" />
<add key="ResourceGroup" value="amsResourceGroup" />
<add key="AadEndpoint" value="https://login.microsoftonline.com" />
<add key="AccountName" value="amsaccount" />
<add key="SubscriptionId" value="111111111-0000-2222-3333-55555555555" />
<add key="ArmAadAudience" value="https://management.core.windows.net/" />
<add key="AadTenantId" value="2222222222-0000-2222-3333-6666666666666" />
<add key="AadSecret" value="33333333-0000-2222-3333-55555555555" />
<add key="AadClientId" value="44444444-0000-2222-3333-55555555555" />
<add key="ArmEndpoint" value="https://management.azure.com/" />
```

### <a name="configure-the-sample-app"></a>샘플 앱 구성

앱을 실행하고 Media Services API에 액세스하려면 App.config에서 올바른 액세스 값을 지정해야 합니다. 

1. Visual Studio를 엽니다.
2. 복제한 솔루션으로 이동합니다.
3. 솔루션 탐색기에서 *EncodeAndStreamFiles* 프로젝트를 펼칩니다.
4. 이 프로젝트를 시작 프로젝트로 설정합니다.
5. App.config를 엽니다.
6. appSettings 값을 이전 단계에서 얻은 값으로 바꿉니다.

 ```xml
 <add key="Region" value="value" />
 <add key="ResourceGroup" value="value" />
 <add key="AadEndpoint" value="value" />
 <add key="AccountName" value="value" />
 <add key="SubscriptionId" value="value" />
 <add key="ArmAadAudience" value="value" />
 <add key="AadTenantId" value="value" />
 <add key="AadSecret" value="value" />
 <add key="AadClientId" value="value" />
 <add key="ArmEndpoint" value="value" />
 ```    
 
7. Ctrl+Shift+B를 눌러 솔루션을 빌드합니다.
