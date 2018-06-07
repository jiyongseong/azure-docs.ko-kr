---
title: "Azure BizTalk Services에 대한 릴리스 정보 | Microsoft Docs"
description: "Azure BizTalk Services에 대한 알려진 문제를 나열합니다."
services: biztalk-services
documentationcenter: 
author: msftman
manager: erikre
editor: 
ms.assetid: f4906fdc-4cd9-4a57-a007-a88c2e51a18f
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: deonhe
ms.openlocfilehash: 0f3662ed386c3a3d2d132a483e7192bddc26de06
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="release-notes-for-azure-biztalk-services"></a>Azure BizTalk Services에 대한 릴리스 정보

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

Microsoft Azure BizTalk Services에 대한 릴리스 정보는 이 릴리스의 알려진 문제를 포함합니다.

## <a name="whats-new-in-the-november-update-of-biztalk-services"></a>BizTalk Services 11월 업데이트의 새로운 기능
* BizTalk Services 포털에서 휴지 상태의 암호화를 사용할 수 있습니다. [BizTalk Services 포털에서 휴지 상태의 암호화 사용](https://msdn.microsoft.com/library/azure/dn874052.aspx)을 참조하세요.

## <a name="update-history"></a>업데이트 내역
### <a name="october-update"></a>10월 업데이트
* 조직 계정이 지원됩니다.  
  * **시나리오**: Microsoft 계정(예: user@live.com)을 사용하여 BizTalk 서비스 배포를 등록했습니다. 이 시나리오에서는 Microsoft 계정 사용자만 BizTalk Services 포털을 사용하여 BizTalk 서비스를 관리할 수 있습니다. 조직 계정을 사용할 수 없습니다.  
  * **시나리오**: Azure Active Directory의 조직 계정(예: user@fabrikam.com 또는 user@contoso.com)을 사용하여 BizTalk 서비스 배포를 등록했습니다. 이 시나리오에서는 동일한 조직 내의 Azure Active Directory 사용자만 BizTalk Services 포털을 사용하여 BizTalk 서비스를 관리할 수 있습니다. Microsoft 계정을 사용할 수 없습니다.  
* BizTalk 서비스를 만드는 경우 BizTalk Services 포털에 자동으로 등록됩니다.
  * **시나리오**: Azure에 로그인하고 BizTalk 서비스를 만든 후 제일 먼저 **관리**를 선택합니다. BizTalk Services 포털이 열리면 BizTalk Services가 자동으로 등록되고 배포할 준비가 됩니다.  
    [BizTalk Services 포털에서 BizTalk Services 배포 등록 및 업데이트](https://msdn.microsoft.com/library/azure/hh689837.aspx)를 참조하세요.  

### <a name="august-14-update"></a>8월 14일 업데이트
* 규약 및 브리지 분리 – 이제 BizTalk Services 포털에서 거래 업체 규약 및 브리지가 분리됩니다. 이제 규약 및 브리지를 별도로 만들고 런타임에 브리지가 EDI 메시지의 값에 따라 규약으로 확인됩니다. [Azure BizTalk Services에서 규약 만들기](https://msdn.microsoft.com/library/azure/hh689908.aspx), [BizTalk Services 포털을 사용하여 EDI 브리지 만들기](https://msdn.microsoft.com/library/azure/dn793986.aspx), [BizTalk Services 포털을 사용하여 AS2 브리지 만들기](https://msdn.microsoft.com/library/azure/dn793993.aspx) 및 [런타임에 브리지를 규약으로 확인하는 방법](https://msdn.microsoft.com/library/azure/dn794001.aspx)을 참조하세요.  
* 규약에 대한 템플릿을 만드는 옵션이 중단되었습니다.  
* 송신 측 규약의 경우 각 스키마에 대해 서로 다른 구분 기호 집합을 지정할 수 있습니다. 이 구성은 송신 측 규약의 프로토콜 설정 아래에 지정됩니다. 자세한 내용은 [Azure BizTalk Services에 X12 규약 만들기](https://msdn.microsoft.com/library/azure/hh689847.aspx) 및 [Azure BizTalk Services에 EDIFACT 규약 만들기](https://msdn.microsoft.com/library/azure/dn606267.aspx)를 참조하세요. 두 개의 새 엔터티가 동일한 목적을 위해 TPM OM API에 추가됩니다. [X12DelimiterOverrides](https://msdn.microsoft.com/library/azure/dn798749.aspx) 및 [EDIFACTDelimiterOverride](https://msdn.microsoft.com/library/azure/dn798748.aspx)를 참조하세요.  
* 파생 형식을 포함한 표준 XSD 항목이 이제 지원됩니다. [맵에 표준 XSD 항목 사용](https://msdn.microsoft.com/library/azure/dn793987.aspx) 및 [매핑 시나리오 및 예제에서 파생 형식 사용](https://msdn.microsoft.com/library/azure/dn793997.aspx)을 참조하세요.  
* AS2는 메시지 서명을 위한 새 MIC 알고리즘 및 새로운 암호화 알고리즘을 지원합니다. [Azure BizTalk Services에서 AS2 규약 만들기](https://msdn.microsoft.com/library/azure/hh689890.aspx)를 참조하세요.  
  ## <a name="know-issues"></a>알려진 문제

### <a name="connectivity-issues-after-biztalk-services-portal-update"></a>BizTalk Services 포털 업데이트 후의 연결 문제
  서비스에 대한 변경 내용을 롤인하도록 BizTalk Services를 업그레이드하는 동안 BizTalk Services 포털이 열려 있으면 BizTalk Services 포털에 연결 문제가 발생할 수 있습니다.  
  해결하려면 브라우저를 다시 시작하고 브라우저 캐시를 삭제하거나 포털을 개인 모드에서 시작합니다.  

### <a name="visual-studio-ide-cannot-locate-the-artifact-if-you-click-an-error-or-warning-in-a-biztalk-services-project"></a>BizTalk Services 프로젝트에서 오류 또는 경고를 클릭하면 Visual Studio IDE에서 아티팩트를 찾을 수 없음
문제를 해결하려면 Visual Studio 2012 업데이트 3 RC 1을 설치합니다.  

### <a name="custom-binding-project-reference"></a>사용자 지정 바인딩 프로젝트 참조
Visual Studio 솔루션의 BizTalk Services 프로젝트에서 다음과 같은 상황을 고려합니다.  

* 동일한 Visual Studio 솔루션에 BizTalk Services 프로젝트와 사용자 지정 바인딩 프로젝트가 있습니다. BizTalk 서비스 프로젝트에 이 사용자 지정 바인딩 프로젝트 파일에 대한 참조가 있습니다.
* BizTalk 서비스 프로젝트에 사용자 지정 바인딩/동작 DLL에 대한 참조가 있습니다.

Visual Studio에서 솔루션을 성공적으로 '빌드'합니다. 그런 다음 솔루션을 '다시 빌드'하거나 '정리'합니다. 그 후에 한 번 더 다시 빌드 또는 정리하면 다음과 같은 오류가 발생합니다.  
  파일 <Path to DLL>을(를) "bin\Debug\FileName.dll"에 복사할 수 없습니다. 'bin\Debug\FileName.dll' 파일은 다른 프로세스에서 사용 중이므로 프로세스에서 액세스할 수 없습니다.  

#### <a name="workaround"></a>해결 방법
* [Visual Studio 2012 업데이트 3](https://www.microsoft.com/download/details.aspx?id=39305) 이 설치된 경우 다음 두 가지 옵션이 있습니다.
  
  * Visual Studio를 다시 시작하거나
  * 솔루션을 다시 시작합니다. 그런 다음 솔루션에서 빌드만 수행합니다.  
* [Visual Studio 2012 업데이트 3](https://www.microsoft.com/download/details.aspx?id=39305) 이 설치되지 않은 경우 작업 관리자를 열어 프로세스 탭을 클릭하고 MSBuild.exe 프로세스를 클릭한 후 프로세스 종료 단추를 클릭합니다.  

### <a name="routing-to-basichttprelay-endpoints-is-not-supported-from-bridges-and-biztalk-services-portal-if-non-printable-characters-are-promoted-as-http-headers"></a>HTTP 헤더로 인쇄할 수 없는 문자가 승격된 경우 브리지 및 BizTalk Services 포털에서 BasicHttpRelay 끝점으로 라우팅이 지원되지 않음
인쇄할 수 없는 문자를 메시지의 승격된 속성의 일부로 사용하는 경우 해당 메시지를 BasicHttpRelay 바인딩을 사용하는 릴레이 대상으로 라우팅할 수 없습니다. 또한 추적의 일부로 제공되는 승격된 속성은 BLOB에 대해 URL로 인코딩되고 대상에 대해서는 인코딩되지 않습니다.  

### <a name="mdn-is-sent-asynchronously-even-if-the-send-asynchronous-mdn-option-is-unchecked"></a>비동기 MDN 보내기 옵션을 선택하지 않아도 MDN이 비동기로 전송됨
다음 시나리오를 고려해 보세요. **비동기 MDN 보내기** 확인란을 선택하고 비동기 MDN을 보내는 URL을 지정한 후 **비동기 MDN 보내기** 확인란을 다시 선택 취소하면 비동기 MDN을 보내는 옵션을 선택하지 않아도 MDN이 지정된 URL로 계속 전송됩니다.  
해결하려면 **비동기 MDN 보내기** 확인란을 선택 취소하기 전에 지정된 URL을 지운 후 AS2 규약을 배포해야 합니다.  

### <a name="whitespace-characters-beyond-a-valid-interchange-cause-an-empty-message-to-be-sent-to-the-suspend-endpoint"></a>유효한 교환을 벗어나는 공백 문자로 인해 비어 있는 메시지가 일시 중단된 끝점으로 전송됩니다.
IEA 규약을 벗어나는 공백이 있는 경우 디스어셈블러는 현재 교환의 끝으로 처리하고 다음 공백 집합을 다음 메시지로 인식합니다. 이는 유효하지 않은 교환이므로 하나의 성공적인 메시지가 경로 대상에 전송되고 하나의 비어 있는 메시지가 일시 중단된 끝점으로 전송된 것을 확인할 수 있습니다.  

### <a name="tracking-in-biztalk-services-portal"></a>Azure BizTalk Services 포털에서 추적
추적 이벤트는 EDI 메시지 처리 및 모든 상관관계까지 캡처됩니다. 메시지가 프로토콜 단계를 벗어나 실패하는 경우 추적은 성공한 것으로 표시됩니다. 이 경우 **추적**의 **세부 정보** 열 아래 로그 섹션에서 오류에 대한 자세한 정보를 참조하세요.
X12 수신 및 송신 설정([Azure BizTalk Services에 X12 규약 만들기](https://msdn.microsoft.com/library/azure/hh689847.aspx))에서 프로토콜 단계에 대한 정보를 제공합니다.  

### <a name="update-agreement"></a>업데이트 규약
BizTalk Services 포털을 사용하면 규약을 구성할 때 ID의 한정자를 수정할 수 있습니다. 이로 인해 속성이 일치하지 않을 수 있습니다. 예를 들어, ZZ:1234567 및 ZZ:7654321 한정자를 사용하는 규약이 있습니다. BizTalk Services 포털 프로필 설정에서 ZZ:1234567을 01:ChangedValue로 변경합니다. 규약을 열면 ZZ:1234567 대신 01:ChangedValue가 표시됩니다.
ID의 한정자를 수정하려면 규약을 삭제하고 파트너 프로필에서 **ID** 를 업데이트한 후 규약을 다시 만듭니다.  

> AZURE.WARNING 이 동작은 X12 및 AS2에 영향을 줍니다.  
> 
> 

### <a name="as2-attachments"></a>AS2 첨부 파일
AS2 메시지의 첨부 파일의 송신 및 수신은 지원되지 않습니다. 특히, 첨부 파일은 자동으로 무시되고 메시지 본문은 일반 AS2 메시지로 처리됩니다.  

### <a name="resources-remembering-path"></a>리소스: 경로 기억
**리소스**를 추가하는 경우 대화 상자 창에서 리소스를 추가하기 위해 이전에 사용한 경로를 기억하지 못할 수 있습니다. 이전에 사용한 경로를 기억하려면 Internet Explorer에서 BizTalk Services 포털 웹 사이트를 **신뢰할 수 있는 사이트** 에 추가해 보세요.  

### <a name="if-you-rename-the-entity-name-of-a-bridge-and-close-the-project-without-saving-changes-opening-the-entity-again-results-in-an-error"></a>브리지의 엔터티 이름을 바꾸고 변경 내용을 저장하지 않고 프로젝트를 닫은 경우 엔터티를 다시 열면 오류가 발생
다음과 같은 순서로 시나리오를 고려합니다.  

* BizTalk 서비스 프로젝트에 브리지(예: XML 단방향 브리지)를 추가합니다.  
* 엔터티 이름 속성에 대한 값을 지정하여 브리지 이름을 바꿉니다. 그러면 지정된 이름으로 연결된.bridgeconfig 파일의 이름이 변경됩니다.  
* 변경 내용을 저장하지 않고 .bcs 파일(Visual Studio에서 탭 닫기)을 닫습니다.  
* 솔루션 탐색기에서 .bcs 파일을 다시 엽니다.  
  연결된 .bridgeconfig 파일에 지정된 새 이름이 있는 반면, 디자인 화면의 엔터티 이름은 여전이 이전 이름인 것을 확인할 수 있습니다. 브리지 구성 요소를 두 번 클릭하여 브리지 구성을 열려고 하면 다음 오류가 발생합니다.  
  `‘<old name>’ Entity’s associated file ‘<old name>.bridgeconfig’ does not exist` 이 시나리오가 발생하지 않도록 하려면 BizTalk 서비스 프로젝트에서 엔터티 이름을 변경한 후 변경 내용을 저장해야 합니다.  
  
### <a name="biztalk-service-project-builds-successfully-even-if-an-artifact-has-been-excluded-from-a-visual-studio-project"></a>Visual Studio 프로젝트에서 아티팩트가 제외되어도 BizTalk 서비스 프로젝트가 성공적으로 빌드됩니다.
BizTalk 서비스 프로젝트에 아티팩트(예를 들어 XSD 파일)를 추가하고 해당 아티팩트를 브리지 구성에 포함(예를 들어 요청 메시지 형식으로 지정)한 후 Visual Studio 프로젝트에서 제외하는 시나리오를 고려해 보세요. 이러한 경우 프로젝트를 빌드하면 삭제된 아티팩트가 Visual Studio 프로젝트에 포함된 것과 동일한 위치의 디스크에 제공되는 한 오류가 발생하지 않습니다.
  
### <a name="the-biztalk-service-project-does-not-check-for-schema-availability-while-configuring-the-bridges"></a>BizTalk 서비스 프로젝트는 브리지를 구성하는 동안 스키마 가용성을 확인하지 않습니다.
BizTalk 서비스 프로젝트에서 프로젝트에 추가된 스키마가 다른 스키마를 가져오는 경우 BizTalk 서비스 프로젝트는 가져온 스키마가 프로젝트에 추가되는지 여부를 확인하지 않습니다. 이러한 프로젝트를 빌드하려고 시도하면 빌드 오류가 발생하지 않습니다.
  
### <a name="the-response-message-for-a-xml-request-reply-bridge-is-always-of-charset-utf-8"></a>XML 요청-회신 브리지에 대한 응답 메시지는 항상 UTF-8 문자 집합임
이 릴리스의 경우 XML 요청-회신 브리지의 응답 메시지에 대한 문자 집합은 항상 UTF-8로 설정됩니다.
  
### <a name="user-defined-datatypes"></a>사용자 정의 데이터 형식
BizTalk 어댑터 서비스 기능 내의 BizTalk Adapter Pack 어댑터는 어댑터 작업을 위한 사용자 정의 데이터 형식을 활용할 수 있습니다.
사용자 정의 데이터 형식을 사용하는 경우 파일을 드라이브:\Program Files\Microsoft BizTalk Adapter Service\BAServiceRuntime\bin\ 또는 BizTalk 어댑터 서비스를 호스팅하는 서버의 전역 어셈블리 캐시(GAC)에 복사합니다. 그러지 않으면 클라이언트에서 다음과 같은 오류가 발생할 수 있습니다.  
```
<s:Fault xmlns:s="http://schemas.xmlsoap.org/soap/envelope/">
<faultcode>s:Client</faultcode>
<faultstring xml:lang="en-US">The UDT with FullName "File, FileUDT, Version=Value, Culture=Value, PublicKeyToken=Value" could not be loaded. Try placing the assembly containing the UDT definition in the Global Assembly Cache.</faultstring>
<detail>
  <AFConnectRuntimeFault xmlns="http://Microsoft.ApplicationServer.Integration.AFConnect/2011" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
    <ExceptionCode>ERROR_IN_SENDING_MESSAGE</ExceptionCode>
  </AFConnectRuntimeFault>
</detail>
</s:Fault>
```  
  
> [!IMPORTANT]
> GACUtil.exe를 사용하여 전역 어셈블리 캐시에 파일을 설치하는 것이 좋습니다. GACUtil.exe는 이 도구와 Visual Studio 명령줄 옵션을 사용하는 방법을 설명합니다.  
> 
> 

### <a name="restarting-the-biztalk-adapter-service-web-site"></a>BizTalk 어댑터 서비스 웹 사이트 다시 시작
**BizTalk 어댑터 서비스 런타임**\*을 설치하면 **BizTalk 어댑터 서비스** 웹 사이트가 **BAService** 응용 프로그램을 포함하는 IIS에 만들어집니다. **BAService** 응용 프로그램은 내부적으로 릴레이 바인딩을 사용하여 온-프레미스 서비스 끝점의 도달률을 클라우드로 확장합니다. 온-프레미스에 호스팅된 서비스의 경우 해당 릴레이 끝점은 온-프레미스 서비스가 시작될 때만 Service Bus에 등록됩니다.  

응용 프로그램을 중지했다가 시작하는 경우 응용 프로그램을 자동 시작하기 위한 구성은 적용되지 않습니다. 따라서 **BAService**가 중지되면 대신, **BizTalk 어댑터 서비스** 웹 사이트를 항상 다시 시작해야 합니다. **BAService** 응용 프로그램을 시작하거나 중지하지 마십시오.

### <a name="special-characters-should-not-be-used-for-address-and-entity-names-of-lob-components"></a>LOB 구성 요소의 주소 및 엔터티 이름에 특수 문자를 사용할 수 없음
LOB 구성 요소의 주소 및 엔터티 이름에 특수 문자를 사용하지 않아야 합니다. 이렇게 하면 BizTalk 서비스 프로젝트를 배포하는 동안 오류가 발생합니다. '%'와 같은 특수 문자의 경우 BizTalk 어댑터 서비스 웹 사이트가 중지됨 상태로 들어가고 수동으로 시작해야 합니다.

### <a name="test-map-with-get-context-property"></a>컨텍스트 속성 가져오기로 맵 테스트
변환에 **컨텍스트 속성 가져오기** 매핑 작업이 포함된 경우 **맵 테스트**는 실패합니다. 임시 방편으로 **컨텍스트 속성 가져오기** 매핑 작업을 더미 데이터가 포함된 매핑 작업을 연결하는 문자열로 바꿉니다. 그러면 대상 스키마가 채워지고 다른 변환 기능을 테스트할 수 있습니다.

### <a name="test-map-property-does-not-display"></a>TestMap 속성이 표시되지 않음
**TestMap** 속성이 Visual Studio에 표시되지 않습니다. **속성** 창 및 **솔루션 탐색기** 창이 동시에 도킹되지 않는 경우 이러한 현상이 발생할 수 있습니다. 이 문제를 해결하려면 **속성** 및 **솔루션 탐색기** 창을 도킹합니다.  

### <a name="datetime-reformat-drop-down-is-grayed-out"></a>DateTime 서식 다시 지정 드롭다운이 회색 표시됨
DateTime 서식 다시 지정 매핑 작업이 디자인 화면에 추가되고 구성된 경우 서식 드롭다운 목록이 회색 표시될 수 있습니다. 컴퓨터 디스플레이가 **보통 – 125%** 또는 **크게 – 150%**로 설정된 경우 이러한 현상이 발생할 수 있습니다. 해결하려면 디스플레이를 아래 단계에 따라 **작게 – 100%(기본값)** 로 설정합니다.  

1. **제어판**을 열고 **모양 및 개인 설정**을 클릭합니다.
2. **디스플레이**를 클릭합니다.
3. **작게 – 100%(기본값)**를 클릭하고 **적용**을 클릭합니다.

이제 **서식** 드롭다운 목록이 예상대로 작동합니다.

### <a name="duplicate-agreements-in-the-biztalk-services-portal"></a>BizTalk Services 포털의 중복 규약
다음과 같은 시나리오를 고려해 보세요.

1. 거래 업체 관리 OM API를 사용하여 규약을 만듭니다.
2. BizTalk Services 포털에서 서로 다른 두 탭의 규약을 엽니다.
3. 두 탭에서 규약을 배포합니다.
4. 결과적으로 두 규약이 배포되고 BizTalk Services 포털에 중복 항목이 발생합니다.

**해결 방법**. BizTalk Services 포털에서 중복 규약 중 하나를 열고 배포 취소합니다.  

### <a name="bridges-do-not-use-updated-certificate-even-after-a-certificate-has-been-updated-in-the-artifact-store"></a>아티팩트 저장소에서 인증서를 업데이트 한 후에도 브리지가 업데이트된 인증서를 사용하지 않음
다음과 같은 시나리오를 고려해 보세요.  

**시나리오 1: 브리지에서 서비스 끝점으로 전송되는 메시지를 보호하기 위해 지문 기반 인증서 사용**  
BizTalk 서비스 프로젝트에서 지문 기반 인증서를 사용하는 시나리오를 고려해 보세요. BizTalk Services 포털에서 이름은 같지만 다른 지문으로 인증서를 업데이트하지만 이에 따라 BizTalk 서비스 프로젝트가 업데이트되지 않습니다. 이러한 시나리오에서는 이전 인증서 데이터가 채널 캐시에 계속 있을 수 있으므로 브리지가 계속 메시지를 처리할 수 있습니다. 그 후에는 메시지 처리에 실패합니다.  

**해결 방법**: BizTalk 서비스 프로젝트에서 인증서를 업데이트하고 프로젝트를 다시 배포합니다.  

**시나리오 2: 브리지에서 서비스 끝점으로 전송되는 메시지를 보호하기 위해 이름 기반 동작을 사용하여 인증서 식별**

BizTalk 서비스 프로젝트에서 이름 기반 동작을 사용하여 인증서 식별하는 시나리오를 고려해 보세요. BizTalk Services 포털에서 인증서를 업데이트하지만 이에 따라 BizTalk 서비스 프로젝트가 업데이트되지 않습니다. 이러한 시나리오에서는 이전 인증서 데이터가 채널 캐시에 계속 있을 수 있으므로 브리지가 계속 메시지를 처리할 수 있습니다. 그 후에는 메시지 처리에 실패합니다.  

**해결 방법**: BizTalk 서비스 프로젝트에서 인증서를 업데이트하고 프로젝트를 다시 배포합니다.  

### <a name="bridges-continue-to-process-messages-even-when-the-sql-database-is-offline"></a>SQL 데이터베이스가 오프라인 상태인 경우에도 브리지가 계속해서 메시지를 처리함
Microsoft Azure SQL Database(배포된 아티팩트 및 파이프라인과 같은 실행 중인 정보 저장)가 오프라인 상태인 경우에도 BizTalk Services 브리지가 잠시 동안 계속해서 메시지를 처리합니다. BizTalk Services가 캐시된 아티팩트 및 브리지 구성을 사용하기 때문입니다.
SQL Database가 오프라인 상태일 때 브리지가 메시지를 처리하지 않도록 하려면 BizTalk Services PowerShell cmdlet을 사용하여 BizTalk Services를 중지하거나 일시 중단할 수 있습니다. 작업을 관리하는 Windows PowerShell cmdlet에 대한 [Azure BizTalk 서비스 관리 샘플](http://go.microsoft.com/fwlink/p/?LinkID=329019) 을 참조하세요.  

### <a name="reading-the-xml-message-within-a-bridges-custom-code-component-includes-an-extra-bom-character"></a>브리지의 사용자 지정 코드 구성 요소 내에서 XML 메시지를 읽을 때 추가 BOM 문자가 포함됨
브리지의 사용자 지정 코드 내에서 XML 메시지를 읽고자 하는 시나리오를 고려해 보세요. .NET API System.Text.Encoding.UTF8.GetString(bytes)을 사용하는 경우 메시지의 시작 부분에 있는 출력에 추가 BOM 문자가 포함됩니다. 따라서 추가 BOM 문자를 포함하여 출력하지 않으려면 ```System.IO.StreamReader().ReadToEnd()```를 사용해야 합니다.

### <a name="sending-messages-to-a-bridge-using-wcf-does-not-scale"></a>WCF를 사용하여 브리지에 메시지 전송할 때 확장되지 않음
WCF를 사용하여 브리지에 전송되는 메시지가 확장되지 않습니다. 확장성 있는 클라이언트를 원하는 경우 대신 HttpWebRequest를 사용해야 합니다.

### <a name="upgrade-token-provider-error-after-upgrading-from-biztalk-services-preview-to-general-availability-ga"></a>업그레이드: BizTalk Services 미리 보기에서 GA(일반 공급)로 업그레이드한 후 토큰 공급자 오류
활성 배치가 있는 EDI 또는 AS2 규약이 있습니다. BizTalk 서비스를 미리 보기에서 GA로 업그레이드하는 경우 다음이 발생할 수 있습니다.

* 오류: 토큰 공급자가 보안 토큰을 제공할 수 없습니다. 토큰 공급자 반환 메시지: 원격 이름을 확인할 수 없습니다.
* Batch 작업이 취소됩니다.

**해결 방법**: BizTalk 서비스를 GA(일반 공급)로 업데이트한 후 규약을 다시 배포합니다.  

### <a name="upgrade-toolbox-shows-the-old-bridge-icons-after-upgrading-the-biztalk-services-sdk"></a>업그레이드: BizTalk Services SDK를 업그레이드한 후 도구 상자에 이전 브리지 아이콘이 표시됨
브리지를 나타내는 이전 아이콘이 있던 BizTalk Services SDK의 이전 버전을 업그레이드한 후 도구 상자에 브리지에 대한 이전 아이콘이 계속 표시됩니다. 그러나 BizTalk 서비스 프로젝트 디자이너 화면에 브리지를 추가하면 화면에 새 아이콘이 표시됩니다.  

**해결 방법**. <system drive>:\Users\<user>\AppData\Local\Microsoft\VisualStudio\11.0에서 .tbd 파일을 삭제하여 이 문제를 해결할 수 있습니다.  

### <a name="upgrade-biztalk-portal-update-from-preview-to-ga-might-show-an-error-indicating-that-the-edi-capability-is-not-available"></a>업그레이드: 미리 보기에서 GA(일반 공급)로 BizTalk 포털 업데이트 시 EDI 기능을 사용할 수 없다는 오류가 표시될 수 있음
BizTalk Services가 미리 보기에서 GA로 업그레이드되는 동안 BizTalk Services 포털에 로그인하면 포털에 다음과 같은 오류가 발생할 수 있습니다.  

이 기능은 이 버전의 Microsoft Azure BizTalk Services의 일부로 사용할 수 없습니다. 이러한 기능을 사용하려면 해당 버전으로 전환하세요.  

**해상도**: 포털에서 로그아웃하고 브라우저를 닫았다 연 후 포털에 로그인합니다.  

### <a name="upgrade-new-tracking-data-does-not-show-up-after-biztalk-services-is-upgraded-to-ga"></a>업그레이드: BizTalk Services를 GA로 업그레이드 한 후 새 추적 데이터가 표시되지 않음
BizTalk Services 미리 보기 구독에 배포된 XML 브리지가 있는 시나리오를 가정해 봅니다. 브리지에 메시지를 보내면 해당 추적 데이터를 BizTalk Services 포털에서 사용할 수 있습니다. 이제 BizTalk Services 포털 및 BizTalk Services 런타임 비트가 GA로 업그레이드된 경우 메시지를 이전에 배포된 것과 동일한 브리지 끝점에 보내면 업그레이드 후 전송된 메시지에 대해 추적 데이터가 표시되지 않습니다.  

### <a name="pipelines-versus-bridges"></a>파이프라인과 브리지
이 문서 전체에서 '파이프라인'과 '브리지'라는 용어는 같은 의미로 사용됩니다. 모두 BizTalk Services에 배포된 메시지 처리 단위로 근본적으로 의미가 같습니다.  

### <a name="concepts"></a>개념
[BizTalk Services](https://msdn.microsoft.com/library/azure/hh689864.aspx)   

