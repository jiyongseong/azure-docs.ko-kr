---
title: "BizTalk Services의 발급자 이름 및 발급자 키 | Microsoft Docs"
description: "BizTalk Services에서 Service Bus 또는 Access Control(ACS)에 대한 발급자 이름 및 발급자 키를 검색하는 방법에 대해 알아봅니다. MABS, WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 067fe356-d1aa-420f-b2f2-1a418686470a
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 18eac72d75680ab12c4a0bea9dfc5ac8a5fce566
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2017
---
# <a name="biztalk-services-issuer-name-and-issuer-key"></a>BizTalk Services: 발급자 이름 및 발급자 키

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Azure BizTalk Services는 Service Bus 발급자 이름 및 발급자 키와 Access Control 발급자 이름 및 발급자 키를 사용합니다. 구체적으로 살펴보면 다음과 같습니다.

| 작업 | 발급자 이름 및 발급자 키 |
| --- | --- |
| Visual Studio에서 응용 프로그램 배포 |Access Control 발급자 이름 및 발급자 키 |
| Azure BizTalk Services 포털 구성 |Access Control 발급자 이름 및 발급자 키 |
| Visual Studio에서 BizTalk 어댑터 서비스로 LOB 릴레이 만들기 |Service Bus 발급자 이름 및 발급자 키 |

이 토픽에서는 발급자 이름 및 발급자 키를 검색하는 단계를 나열합니다. 

## <a name="access-control-issuer-name-and-issuer-key"></a>Access Control 발급자 이름 및 발급자 키
Access Control 발급자 이름 및 발급자 키는 다음에 사용됩니다.

* Visual Studio에서 만든 Azure BizTalk 서비스 응용 프로그램: Visual Studio의 BizTalk 서비스 응용 프로그램을 Azure에 배포하려면 Access Control 발급자 이름 및 발급자 키를 입력합니다. 
* Azure BizTalk Services 포털: BizTalk 서비스를 만들고 BizTalk Services 포털에 로그인하면 배포를 위해 Access Control 발급자 이름 및 Access Control 발급자 키가 동일한 Access Control 값으로 자동으로 등록됩니다.

### <a name="get-the-access-control-issuer-name-and-issuer-key"></a>Access Control 발급자 이름 및 발급자 키 가져오기

인증을 위해 ACS를 사용하고 발급자 이름 및 발급자 키 값을 가져오는 전체 단계는 다음과 같습니다.

1. [Azure PowerShell cmdlet](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)을 설치합니다.
2. Azure 계정을 추가합니다. `Add-AzureAccount`
3. 구독 이름을 반환합니다. `get-azuresubscription`
4. 사용 중인 구독을 선택합니다. `select-azuresubscription <name of your subscription>` 
5. 새 네임스페이스를 만듭니다. `new-azuresbnamespace <name for the service bus> "Location" -CreateACSNamespace $true -NamespaceType Messaging`

    예: `new-azuresbnamespace biztalksbnamespace "South Central US" -CreateACSNamespace $true -NamespaceType Messaging`
      
5. 새 ACS 네임스페이스를 만들면(몇 분 정도 걸릴 수 있음) 발급자 이름 및 발급자 키 값이 연결 문자열에 나열됩니다. 

    ```
    Name                  : biztalksbnamespace
    Region                : South Central US
    DefaultKey            : abcdefghijklmnopqrstuvwxyz
    Status                : Active
    CreatedAt             : 10/18/2016 9:36:30 PM
    AcsManagementEndpoint : https://biztalksbnamespace-sb.accesscontrol.windows.net/
    ServiceBusEndpoint    : https://biztalksbnamespace.servicebus.windows.net/
    ConnectionString      : Endpoint=sb://biztalksbnamespace.servicebus.windows.net/;SharedSecretIssuer=owner;SharedSecretValue=abcdefghijklmnopqrstuvwxyz
    NamespaceType         : Messaging
    ```

요약하면  
발급자 이름 = SharedSecretIssuer  
발급자 키 = SharedSecretKey

[New-AzureSBNamespace](https://msdn.microsoft.com/library/dn495165.aspx) cmdlet에 대한 추가 정보 

## <a name="service-bus-issuer-name-and-issuer-key"></a>Service Bus 발급자 이름 및 발급자 키
Service Bus 발급자 이름 및 발급자 키는 BizTalk 어댑터 서비스에 사용됩니다. Visual Studio의 BizTalk Services 프로젝트에서 BizTalk 어댑터 서비스를 사용하여 온-프레미스 LOB(기간 업무) 시스템에 연결합니다. 연결하려면 LOB 릴레이를 만들고 LOB 시스템 세부 정보를 입력합니다. 이 작업을 수행할 때에도 Service Bus 발급자 이름 및 발급자 키를 입력합니다.

### <a name="to-retrieve-the-service-bus-issuer-name-and-issuer-key"></a>Service Bus 발급자 이름 및 발급자 키를 검색하려면
1. [Azure 포털](http://portal.azure.com)에 로그인합니다.
2. **Service Bus**를 검색하고 네임스페이스를 선택합니다. 
3. **공유 액세스 정책** 속성을 열고, 정책을 선택하고, 이름과 키 값에 대한 **연결 문자열**을 봅니다.  

## <a name="next"></a>다음
추가 Azure BizTalk Services 항목:

* [Azure BizTalk Services SDK 설치](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [자습서: Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [Azure BizTalk Services SDK로 시작하는 방법](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>참고 항목
* [방법: ACS 관리 서비스를 사용하여 서비스 ID 구성](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
* [BizTalk Services: Developer, Basic, Standard 및 Premium Editions 차트](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [BizTalk Services: 프로비전](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [BizTalk Services: 프로비저닝 상태 차트](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [BizTalk Services: 대시보드, 모니터 및 크기 조정 탭](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [BizTalk Services: Backup 및 복원](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [BizTalk Services: 제한](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>

