---
title: Azure Logic Apps에 대한 액세스 보호 | Microsoft Docs
description: Azure Logic Apps에서 워크플로와 함께 사용되는 트리거, 입력 및 출력, 작업 매개 변수 및 서비스에 대한 액세스를 보호하기 위한 보안을 추가합니다.
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: ''
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/22/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 3831c44a52fe4bb428021acac46188966087fdfe
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="secure-access-to-your-logic-apps"></a>논리 앱에 대한 액세스 보호

논리 앱을 보호하는 데 사용할 수 있는 많은 도구가 있습니다.

* 논리 앱(HTTP 요청 트리거)을 트리거하는 액세스 보안
* 논리 앱을 관리, 편집 또는 읽기 위한 액세스 보안
* 실행에 대한 입력 및 출력의 콘텐츠에 대한 액세스 보안
* 워크플로에서 작업 내에서 매개 변수 또는 입력 보안
* 워크플로에서 요청을 수신하는 서비스에 대한 액세스 보안

## <a name="secure-access-to-trigger"></a>트리거에 대한 보안 액세스

HTTP 요청([요청](../connectors/connectors-native-reqres.md) 또는 [WebHook](../connectors/connectors-native-webhook.md))을 발생시키는 논리 앱으로 작업하는 경우, 인증된 클라이언트만 논리 앱을 발생시킬 수 있도록 액세스를 제한할 수 있습니다. 논리 앱에 대한 모든 요청은 SSL을 통해 암호화되고 보호됩니다.

### <a name="shared-access-signature"></a>공유 액세스 서명

논리 앱에 대한 모든 요청 끝점은 URL의 일부로서 [SAS(공유 액세스 서명](../storage/common/storage-dotnet-shared-access-signature-part-1.md))를 포함합니다. 각 URL은 `sp`, `sv` 및 `sig` 쿼리 매개 변수를 포함합니다. 권한은 `sp`에 의해 지정되고 허용된 HTTP 메서드에 해당하며, `sv`는 생성하는 데 사용된 버전이며, `sig`는 트리거하는 액세스를 인증하는 데 사용됩니다. 서명은 모든 URL 경로 및 속성에 관한 암호 키를 포함한 SHA256 알고리즘을 사용하여 생성됩니다. 비밀 키 암호는 절대 노출되거나 공개되지 않으며 논리 앱의 일부로서 암호화되고 저장됩니다. 논리 앱은 암호 키로 만들어진 유효한 서명을 포함하는 트리거에 권한을 부여합니다.

#### <a name="regenerate-access-keys"></a>액세스 키 다시 생성

REST API 또는 Azure Portal을 통해 언제든지 새 보안 키를 다시 생성할 수 있습니다. 이전 키를 사용하여 이전에 생성된 모든 현재 URL은 무효화되고 논리 앱을 실행하는 데 권한이 더 이상 부여되지 않습니다.

1. Azure Portal에서 논리 앱을 열어 키를 다시 생성합니다.
1. **설정** 아래에 있는 **액세스 키** 메뉴 항목을 클릭합니다.
1. 다시 생성할 키를 선택하고 프로세스를 완료합니다.

다시 생성한 후 검색한 URL은 새 액세스 키로 서명됩니다.

#### <a name="creating-callback-urls-with-an-expiration-date"></a>만료 날짜가 있는 콜백 URL 만들기

다른 상대방과 URL을 공유하는 경우 필요에 따라 특정 키와 만료 날짜로 URL을 생성할 수 있습니다. 그러면 원활하게 키를 배포하거나 특정 시간대에 앱 실행 액세스를 제한할 수 있습니다. 다음과 같이 [논리 앱 REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers)를 통해 URL의 만료 날짜를 지정할 수 있습니다.

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

본문에서는 속성 `NotAfter`를 `NotAfter` 날짜 및 시간까지만 유효한 콜백 URL을 반환하는 JSON 날짜 문자열로서 포함합니다.

#### <a name="creating-urls-with-primary-or-secondary-secret-key"></a>기본 또는 보조 비밀 키로 URL 만들기

요청 기반 트리거에 대한 콜백 URL을 생성하거나 나열하면, URL을 서명하기 위해 사용할 키를 지정할 수도 있습니다.  다음과 같이 [논리 앱 REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers)을 통해 특정 키로 서명된 URL을 생성할 수 있습니다.

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

본문에서는 속성 `KeyType`을 `Primary` 또는 `Secondary`로서 포함합니다.  이는 지정된 보안 키로 서명된 URL을 반환합니다.

### <a name="restrict-incoming-ip-addresses"></a>받는 IP 주소 제한

공유 액세스 서명 외에도 특정 클라이언트의 논리 앱 호출만 제한하려는 경우가 있습니다.  예를 들어 Azure API Management를 통해 끝점을 관리하는 경우 API Management 인스턴스 IP 주소에서 요청이 들어오면 요청을 수락하도록 논리 앱을 제한할 수 있습니다.

이 설정은 논리 앱 설정 내에서 구성할 수 있습니다.

1. Azure Portal에서 IP 주소 제한을 추가하려는 논리 앱을 엽니다.
1. **설정**에서 **워크플로 설정** 메뉴 항목을 클릭합니다.
1. 트리거에 의해 허용되는 IP 주소 범위 목록을 지정합니다.

유효한 IP 범위 형식은 `192.168.1.1/255`입니다. 논리 앱을 중첩된 논리 앱으로서 실행하려는 경우 **다른 논리 앱만** 옵션을 선택합니다. 이 옵션은 빈 배열을 리소스에 작성할 수 있으며, 이는 서비스 자체(부모 논리 앱)의 호출만 성공적으로 실행된다는 의미입니다.

> [!NOTE]
> 요청 트리거가 있는 논리 앱은 IP에 관계 없이 REST API/Management `/triggers/{triggerName}/run`을 통해 실행될 수 있습니다. 이 시나리오에는 Azure REST API에 대한 인증이 필요하며 모든 이벤트는 Azure Audit Log에 나타납니다. 그에 따라 액세스 제어 정책을 설정합니다.

#### <a name="setting-ip-ranges-on-the-resource-definition"></a>리소스 정의에서 IP 범위 설정

배포를 자동화하기 위해 [배포 템플릿](logic-apps-create-deploy-template.md)을 사용하는 경우 리소스 템플릿에서 IP 범위 설정을 구성할 수 있습니다.  

``` json
{
    "properties": {
        "definition": {
        },
        "parameters": {},
        "accessControl": {
            "triggers": {
                "allowedCallerIpAddresses": [
                    {
                        "addressRange": "192.168.12.0/23"
                    },
                    {
                        "addressRange": "2001:0db8::/64"
                    }
                ]
            }
        }
    },
    "type": "Microsoft.Logic/workflows"
}

```

### <a name="adding-azure-active-directory-oauth-or-other-security"></a>Azure Active Directory, OAuth 또는 기타 보안 추가

논리 앱을 기반으로 권한 부여 프로토콜을 추가하기 위해 [Azure API Management](https://azure.microsoft.com/services/api-management/)에서는 논리 앱을 API로 노출하는 기능을 사용하여 모든 끝점에 대한 다양한 모니터링, 보안, 정책 및 설명서를 제공합니다. Azure API Management는 Azure Active Directory, 인증서, OAuth 또는 기타 보안 표준을 사용할 수 있는 논리 앱에 대한 공용 또는 개인 끝점을 노출할 수 있습니다. 요청을 받으면 Azure API Management는 해당 요청을 논리 앱에 전달합니다(필요한 모든 변환 또는 진행 중인 제한 수행). API Management에서만 논리 앱이 트리거되도록 논리 앱에서 받는 IP 범위 설정을 사용할 수 있습니다.

## <a name="secure-access-to-manage-or-edit-logic-apps"></a>논리 앱을 관리하거나 편집하기 위한 액세스 보호

특정 사용자 또는 그룹만 리소스에서 작업을 수행할 수 있도록 논리 앱에서 관리 작업을 위한 액세스를 제한할 수 있습니다. 논리 앱은 Azure [RBAC(역할 기반 Access Control)](../role-based-access-control/role-assignments-portal.md) 기능을 사용하고 동일한 도구로 사용자 지정할 수 있습니다.  또한 구독 구성원들에게 할당할 수 있는 몇 가지 기본 제공 역할이 있습니다.

* **논리 앱 참가자** - 논리 앱을 보고, 편집하고 업데이트하기 위한 액세스를 제공합니다.  리소스를 제거하거나 관리 작업을 수행할 수 없습니다.
* **논리 앱 연산자** - 논리 앱과 실행 기록을 보고 활성화/비활성화할 수 있습니다.  정의를 편집하거나 업데이트할 수 없습니다.

[Azure 리소스 잠금](../azure-resource-manager/resource-group-lock-resources.md)을 사용하여 논리 앱을 변경하거나 삭제하지 않도록 방지할 수도 있습니다. 이 기능은 프로덕션 리소스가 변경되거나 삭제되는 것을 방지하는 데 유용합니다.

## <a name="secure-access-to-contents-of-the-run-history"></a>실행 기록 콘텐츠에 대한 보안 액세스

이전 실행에서 특정 IP 주소 범위까지 입력 또는 출력 콘텐츠에 대한 액세스를 제한할 수 있습니다.  

워크플로 실행 내에 있는 모든 데이터는 전송 중 그리고 미사용 시 암호화됩니다. 실행 기록을 호출하면 서비스가 요청을 인증하고 요청 및 응답 입출력에 대한 링크를 제공합니다. 지정된 IP 주소 범위에서 콘텐츠를 보기 위한 요청만 콘텐츠를 반환하도록 하여 이 링크를 보호할 수 있습니다. 추가 액세스 제어를 위해 이 기능을 사용할 수 있습니다. 누구도 입/출력에 액세스할 수 없도록 `0.0.0.0`과 같이 IP 주소를 지정할 수도 있습니다. 관리자 권한을 가진 사용자만 이러한 제한을 제거하여 워크플로 콘텐츠에 'Just-In-Time' 액세스를 제공할 수 있습니다.

Azure Portal의 리소스 설정 내에서 이 설정을 구성할 수 있습니다.

1. Azure Portal에서 IP 주소 제한을 추가하려는 논리 앱을 엽니다.
2. **설정** 아래에 있는 **액세스 제어 구성** 메뉴 항목을 클릭합니다.
3. 콘텐츠에 액세스하기 위해 IP 주소 범위 목록을 지정합니다.

#### <a name="setting-ip-ranges-on-the-resource-definition"></a>리소스 정의에서 IP 범위 설정

배포를 자동화하기 위해 [배포 템플릿](logic-apps-create-deploy-template.md)을 사용하는 경우 리소스 템플릿에서 IP 범위 설정을 구성할 수 있습니다.  

``` json
{
    "properties": {
        "definition": {
        },
        "parameters": {},
        "accessControl": {
            "contents": {
                "allowedCallerIpAddresses": [
                    {
                        "addressRange": "192.168.12.0/23"
                    },
                    {
                        "addressRange": "2001:0db8::/64"
                    }
                ]
            }
        }
    },
    "type": "Microsoft.Logic/workflows"
}
```

## <a name="secure-parameters-and-inputs-within-a-workflow"></a>워크플로 내에서 매개 변수 및 입력 보안

환경에 배포하기 위해 워크플로 정의의 몇 가지 측면을 매개 변수화할 수 있습니다. 또한 일부 매개 변수는 워크플로 편집 시 표시하지 않으려는 보안 매개 변수일 수 있습니다. 예를 들어, HTTP 작업의 [Azure Active Directory 인증](../connectors/connectors-native-http.md#authentication)에 대한 클라이언트 ID 및 클라이언트 암호 등이 있습니다.

### <a name="using-parameters-and-secure-parameters"></a>매개 변수 및 보안 매개 변수 사용

런타임 시 리소스 매개 변수의 값에 액세스하기 위해 [워크플로 정의 언어](http://aka.ms/logicappsdocs)는 `@parameters()` 연산을 제공합니다. 또한 [리소스 배포 템플릿에서 매개 변수를 지정](../azure-resource-manager/resource-group-authoring-templates.md#parameters)할 수 있습니다. 하지만 매개 변수 형식을 `securestring`로 지정하면 해당 매개 변수는 리소스 정의의 나머지를 반환하지 않고 배포 후에 리소스를 확인하여 액세스할 수 없게 됩니다.

> [!NOTE]
> 요청의 머리글이나 본문에 매개 변수를 사용하는 경우 해당 매개 변수에서는 실행 기록과 나가는 HTTP 요청에 액세스하여 볼 수 있습니다. 해당하는 콘텐츠 액세스 정책을 설정해야 합니다.
> 권한 부여 헤더는 입력 또는 출력을 통해 볼 수 없습니다. 여기에서 암호를 사용하는 경우 암호를 검색할 수 없습니다.

#### <a name="resource-deployment-template-with-secrets"></a>암호가 있는 리소스 배포 템플릿

다음 예제에서는 런타임 시 `secret`의 보안 매개 변수를 참조하는 배포를 보여 줍니다. 별도 매개 변수 파일에서 `secret`에 대한 환경 값을 지정하거나 [Azure Resource Manager KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md)를 사용하여 배포 시 암호를 검색할 수 있습니다.

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "secretDeploymentParam": {
         "type": "securestring"
      }
   },
   "variables": {},
   "resources": [ {
      "name": "secret-deploy",
      "type": "Microsoft.Logic/workflows",
      "location": "westus",
      "tags": {
         "displayName": "LogicApp"
      },
      "apiVersion": "2016-06-01",
      "properties": {
         "definition": {
            "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
            "actions": {
               "Call_External_API": {
                  "type": "Http",
                  "inputs": {
                     "headers": {
                        "Authorization": "@parameters('secret')"
                     },
                     "body": "This is the request"
                  },
                  "runAfter": {}
               }
            },
            "parameters": {
               "secret": {
                  "type": "SecureString"
               }
            },
            "triggers": {
               "manual": {
                  "type": "Request",
                  "kind": "Http",
                  "inputs": {
                     "schema": {}
                  }
               }
            },
            "contentVersion": "1.0.0.0",
            "outputs": {}
         },
         "parameters": {
            "secret": {
               "value": "[parameters('secretDeploymentParam')]"
            }
         }
      }
   } ],
   "outputs": {}
}
```

## <a name="secure-access-to-services-receiving-requests-from-a-workflow"></a>워크플로에서 요청을 수신하는 서비스에 대한 액세스 보안

논리 앱이 액세스해야 하는 모든 끝점을 보호하는 데는 여러 가지 방법이 있습니다.

### <a name="using-authentication-on-outbound-requests"></a>아웃바운드 요청에 대한 인증 사용

HTTP, HTTP + Swagger(개방형 API) 또는 웹후크 동작으로 작업할 경우 전송되는 요청에 인증을 추가할 수 있습니다. 추가할 수 있는 인증으로는 기본 인증, 인증서 인증 또는 Azure Active Directory 인증이 있습니다. 이 인증을 구성하는 방법에 대한 자세한 내용은 [이 문서에서](../connectors/connectors-native-http.md#authentication) 찾아볼 수 있습니다.

### <a name="restricting-access-to-logic-app-ip-addresses"></a>논리 앱 IP 주소에 대한 액세스 제한

논리 앱의 모든 호출은 영역당 특정 IP 주소 집합에서 나옵니다. 지정된 IP 주소의 요청만 수락하도록 필터링을 추가할 수 있습니다. 해당 IP 주소의 목록은 [논리 앱 제한 및 구성](logic-apps-limits-and-config.md#configuration)을 참조하세요.

### <a name="on-premises-connectivity"></a>온-프레미스 연결

논리 앱은 여러 서비스를 통합하여 안전하고 신뢰할 수 있는 온-프레미스 통신을 제공합니다.

#### <a name="on-premises-data-gateway"></a>온-프레미스 데이터 게이트웨이

논리 앱의 많은 관리형 커넥터는 파일 시스템, SQL, SharePoint, DB2 등의 온-프레미스 시스템에 보안 연결을 제공합니다. 게이트웨이는 Azure Service Bus를 통해 암호화된 채널의 온-프레미스 원본에서 데이터를 릴레이합니다. 보안으로 시작하는 모든 트래픽은 게이트웨이 에이전트에서 트래픽을 아웃바운드합니다. [데이터 게이트웨이 작동 원리](logic-apps-gateway-install.md#gateway-cloud-service)에 대해 자세히 알아봅니다.

#### <a name="azure-api-management"></a>Azure API Management

[Azure API Management](https://azure.microsoft.com/services/api-management/)에는 보안 프록시를 위한 사이트 간 VPN 및 ExpressRoute 통합과 온-프레미스 시스템에 대한 통신 등 온-프레미스 연결 옵션이 있습니다. Logic App Designer에서 워크플로 내의 Azure API Management에서 노출되는 API를 빠르게 선택하여 온-프레미스 시스템에 빠르게 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[배포 템플릿 만들기](logic-apps-create-deploy-template.md)  
[예외 처리](logic-apps-exception-handling.md)  
[논리 앱 모니터링](logic-apps-monitor-your-logic-apps.md)  
[논리 앱 오류 진단 및 문제](logic-apps-diagnosing-failures.md)  
