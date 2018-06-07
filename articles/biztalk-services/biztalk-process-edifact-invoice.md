---
title: "자습서: Azure BizTalk Services를 사용하여 EDIFACT 송장 처리 | Microsoft Docs"
description: "Box 커넥터 또는 API 앱을 만들어서 구성하고 Azure App Service의 논리 앱에서 사용하는 방법"
services: biztalk-services
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
ms.assetid: 7e0b93fa-3e2b-4a9c-89ef-abf1d3aa8fa9
ms.service: biztalk-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/31/2016
ms.author: deonhe
ms.openlocfilehash: 2ebd6a8cb70f218c3b56bc78c9b853dbf51ab468
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-process-edifact-invoices-using-azure-biztalk-services"></a>자습서: Azure BizTalk Services를 사용하여 EDIFACT 송장 처리

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

BizTalk Services 포털을 사용하여 X12 및 EDIFACT 규약을 구성 및 배포할 수 있습니다. 이 자습서에서는 거래 파트너 간에 송장 교환을 위해 EDIFACT 규약을 만드는 방법에 대해 살펴봅니다. 이 자습서는 EDIFACT 메시지를 교환하는 두 거래 업체 Northwind와 Contoso를 포함하는 종단 간 비즈니스 솔루션에 대해 작성되었습니다.  

## <a name="sample-based-on-this-tutorial"></a>이 자습서를 기반으로 하는 샘플
이 자습서는 **MSDN 코드 갤러리**에서 다운로드 가능한 샘플 [BizTalk Services를 사용하여 EDIFACT 송장 전송](http://go.microsoft.com/fwlink/?LinkId=401005)에 대해 작성되었습니다. 샘플을 사용하고 이 자습서를 진행하여 샘플이 빌드된 방식을 이해할 수 있습니다. 또는 이 자습서를 사용하여 사용자 고유의 솔루션 처음부터 만들 수 있습니다. 이 자습서는 이 솔루션이 빌드된 방식을 이해할 수 있도록 두 번째 접근 방식을 대상으로 합니다. 또한 가능한 만큼 자습서는 샘플과 일치하고 샘플에 사용된 것과 동일한 아티팩트에 대한 이름(예: 스키마, 변환)을 사용합니다.  

> [!NOTE]
> 이 솔루션은 EAI 브리지에서 EDI 브리지로 메시지 전송을 포함하므로 [BizTalk Services 브리지 연결 샘플](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104) 샘플을 다시 사용합니다.  
> 
> 

## <a name="what-does-the-solution-do"></a>솔루션의 기능은 무엇입니까?
이 솔루션에서 Northwind는 Contoso에서 EDIFACT 송장을 받습니다. 이 송장은 표준 EDI 형식이 아닙니다. 따라서 Northwind로 송장을 보내기 전에 EDIFACT 송장(INVOIC이라고도 함) 문서로 변환해야 합니다. 수신 시 Northwind는 EDIFACT 송장을 처리하고 Contoso에 제어 메시지(CONTRL이라고도 함)를 반환해야 합니다.

![][1]  

이 비즈니스 시나리오를 획득하기 위해 Contoso는 Microsoft Azure BizTalk Services와 함께 제공되는 기능을 사용합니다.

* Contoso는 EAI 브리지를 사용하여 원래 송장을 EDIFACT INVOIC으로 변환합니다.
* 그런 다음 EAI 브리지는 BizTalk Services 포털에서 구성된 규약의 일부분으로 배포된 EDI 송신 브리지에 메시지를 보냅니다.
* EDI 송신 브리지는 EDIFACT INVOIC을 처리하고 Northwind로 라우팅합니다.
* 송장을 받은 후 Northwind는 규약의 일부로 배포된 EDI 수신 브리지에 CONTRL 메시지를 반환합니다.  

> [!NOTE]
> 필요에 따라 이 솔루션은 일괄 처리를 사용하여 각 송장을 별도로 보내지 않고 일괄 처리로 송장을 보내는 방법을 보여 줍니다.  
> 
> 

시나리오를 완료하려면 Service Bus 큐를 사용하여 Contoso에서 Northwind로 송장을 보내거나 Northwind로부터 승인을 받습니다. 이러한 큐는 다운로드로 제공되며 이 자습서의 일부로 사용 가능한 샘플 패키지에 포함된 클라이언트 응용 프로그램을 사용하여 만들 수 있습니다.  

## <a name="prerequisites"></a>필수 조건
* Service Bus 네임스페이스가 있어야 합니다. 네임스페이스를 만드는 방법에 대한 지침은 [방법: Service Bus 서비스 네임스페이스 만들기 또는 수정](https://msdn.microsoft.com/library/azure/hh674478.aspx)을 참조하세요. **edifactbts**라는 프로비전된 Service Bus 네임스페이스가 있다고 가정합니다.
* BizTalk Services 구독이 있어야 합니다. 이 자습서의 경우 **contosowabs**라는 BizTalk Services 구독을 보유했다고 가정합니다.
* BizTalk Services 포털에서 BizTalk Services 구독을 등록합니다. 자세한 내용은 [BizTalk Services 포털에서 BizTalk Services 배포 등록](https://msdn.microsoft.com/library/hh689837.aspx)
* Visual Studio가 설치되어 있어야 합니다.
* BizTalk Services SDK가 설치되어 있어야 합니다. [http://go.microsoft.com/fwlink/?LinkId=235057](http://go.microsoft.com/fwlink/?LinkId=235057)  

## <a name="step-1-create-the-service-bus-queues"></a>1단계: Service Bus 큐 만들기
이 솔루션에서는 Service Bus 큐를 사용하여 거래 업체 간에 메시지를 교환합니다. Contoso와 Northwind는 EAI 및/또는 EDI 브리지를 사용할 위치에서 큐에 메시지를 보냅니다. 이 솔루션에서는 세 개의 Service Bus 큐가 필요합니다.

* **northwindreceive** – Northwind는 이 큐를 통해 Contoso에서 송장을 받습니다.
* **contosoreceive** – Contoso는 이 큐를 통해 Northwind로부터 승인을 받습니다.
* **일시 중단** – 모든 일시 중단된 메시지가 이 큐에 라우팅됩니다. 처리 중 실패하는 경우 메시지는 일시 중단됩니다.

샘플 패키지에 포함된 클라이언트 응용 프로그램을 사용하여 이러한 Service Bus 큐를 만들 수 있습니다.  

1. 샘플을 다운로드한 위치에서 **자습서: BizTalk Services EDI Bridges.sln을 사용하여 송장 전송**을 엽니다.
2. **F5** 키를 눌러 빌드하고 **자습서 클라이언트** 응용 프로그램을 시작합니다.
3. 화면에서 Service Bus ACS 네임스페이스, 발급자 이름 및 발급자 키를 입력합니다.
   
   ![][2]  
4. Service Bus 네임스페이스에서 세 개의 큐가 만들어진다는 메시지 상자가 나타납니다. **확인**을 클릭합니다.
5. 자습서 클라이언트를 실행 상태로 둡니다. 열고 **Service Bus** > ***Service Bus 네임스페이스*** > **큐**를 클릭하고 세 개의 큐가 생성되었는지 확인합니다.  

## <a name="step-2-create-and-deploy-trading-partner-agreement"></a>2단계: 거래 업체 규약 만들기 및 배포
Contoso와 Northwind 간의 거래 업체 규약을 만듭니다. 거래 업체 규약은 사용할 메시지 스키마, 사용할 메시징 프로토콜 등과 같은 두 비즈니스 파트너 간의 거래 규약을 정의합니다. 거래 업체 규약은 거래 업체에 메시지를 보내는 브리지(**EDI 송신 브리지**)와 거래 업체로부터 메시지를 수신하는 브리지(**EDI 수신 브리지**)의 두 개의 EDI 브리지를 포함합니다.

이 솔루션의 컨텍스트에서 규약의 송신 측에 해당하는 EDI 송신 브리지는 Contoso에서 Northwind로 EDIFACT 송장을 보내는 데 사용됩니다. 마찬가지로 규약의 수신 측에 해당하는 EDI 수신 브리지는 Northwind로부터 승인을 받는 데 사용됩니다.  

### <a name="create-the-trading-partners"></a>거래 업체 만들기
먼저 Contoso와 Northwind에 대한 거래 업체를 만듭니다.  

1. BizTalk Services 포털의 **파트너** 탭에서 **추가**를 클릭합니다.
2. 새 파트너 페이지에서 파트너 이름으로 **Contoso**를 입력한 다음 **저장**을 클릭합니다.
3. 두 번째 파트너, **Northwind**를 만드는 단계를 반복합니다.  

### <a name="create-the-agreement"></a>규약 만들기
거래 업체 규약은 거래 업체의 비즈니스 프로필 간에 생성됩니다. 이 솔루션에서는 파트너를 만들 때 자동으로 만들어지는 기본 파트너 프로필을 사용합니다.  

1. BizTalk Services 포털에서 **규약** > **추가**를 클릭합니다.
2. 새 규약의 **일반 설정** 페이지에서 아래 이미지에 표시된 값을 지정한 다음 **계속**을 클릭합니다.
   
   ![][3]  
   
   **계속**을 클릭하면 두 개의 탭 **수신 설정** 및 **송신 설정**을 사용할 수 있습니다.
3. Contoso와 Northwind 간의 규약을 만듭니다. 이 규약은 Contoso에서 Northwind로 EDIFACT 송장을 보내는 방법을 제어합니다.
   
   1. **송신 설정**을 클릭합니다.
   2. **인바운드 URL**, **변환** 및 **일괄 처리** 탭의 기본값을 그대로 둡니다.
   3. **스키마** 섹션 아래의 **프로토콜** 탭에서 **EFACT_D93A_INVOIC.xsd** 스키마를 업로드합니다. 이 스키마는 샘플 패키지와 함께 사용할 수 있습니다.
      
      ![][4]  
   4. **전송** 탭에서 Service Bus 큐에 대한 세부 정보를 지정합니다. 송신 측 규약의 경우 **northwindreceive** 큐를 사용하여 Northwind에 EDIFACT 송장을 보내고 **일시 중단** 큐를 사용하여 처리하는 동안 실패 및 일시 중단된 모든 메시지를 라우팅합니다. 이 항목의 **1단계: Service Bus 큐 만들기** 에서 해당 큐를 만들었습니다.
      
      ![][5]  
      
      **전송 설정 > 전송 유형** 및 **메시지 일시 중지 설정 > 전송 유형** 아래에서 Azure Service Bus를 선택하고 이미지에 표시된 대로 값을 제공합니다.
4. Contoso와 Northwind 간의 수신 규약을 만듭니다. 이 규약은 Contoso가 Northwind로부터 승인을 받는 방법을 제어합니다.
   
   1. **수신 설정**을 클릭합니다.
   2. **전송** 및 **변환** 탭의 기본값을 그대로 둡니다.
   3. **스키마** 섹션 아래의 **프로토콜** 탭에서 **EFACT_4.1_CONTRL.xsd** 스키마를 업로드합니다. 이 스키마는 샘플 패키지와 함께 사용할 수 있습니다.
   4. **경로** 탭에서 northwind에서의 승인만 Contoso로 라우팅되도록 하는 필터를 만듭니다. **경로 설정** 아래에서 **추가**를 클릭하여 라우팅 필터를 만듭니다.
      
      ![][6]  
      
      1. 이미지와 같이 **규칙 이름**, **경로 규칙** 및 **경로 대상**에 대한 값을 제공합니다.
      2. **Save**를 클릭합니다.
   5. **경로** 탭에서 일시 중단된 승인(처리 중 실패한 승인)이 라우팅될 경로를 지정합니다. Azure Service Bus에 대한 전송 방식, **큐**에 대한 라우팅 대상 유형, **공유 액세스 서명**(SAS)에 대한 인증 유형을 설정하고 Service Bus 네임스페이스에 대한 SAS 연결 문자열을 제공한 다음 **일시 중단됨**으로 큐 이름을 입력합니다.
5. 마지막으로 **배포** 를 클릭하여 규약을 배포합니다. 송신 및 수신 규약이 배포되는 끝점을 기록해 둡니다.
   
   * **인바운드 URL** 아래의 **송신 설정** 탭에서 끝점을 확인하세요. EDI 송신 브리지를 사용하여 Contoso에서 Northwind로 메시지를 보내려면 이 끝점에 메시지를 보내야 합니다.
   * **전송** 아래의 **수신 설정** 탭에서 끝점을 확인하세요. EDI 수신 브리지를 사용하여 Northwind에서 Contoso로 메시지를 보내려면 이 끝점에 메시지를 보내야 합니다.  

## <a name="step-3-create-and-deploy-the-biztalk-services-project"></a>3단계: BizTalk Services 프로젝트 만들기 및 배포
이전 단계에서 EDIFACT 송장 및 승인을 처리하도록 EDI 송신 및 수신 규약을 배포했습니다. 이러한 규약은 표준 EDIFACT 메시지 스키마를 준수하는 메시지만을 처리할 수 있습니다. 그러나 이 솔루션에 대한 시나리오마다 Contoso는 내부 소유 스키마로 Northwind에 송장을 보냅니다. 따라서 메시지가 EDI 송신 브리지에 전송되기 전에 내부 스키마에서 표준 EDIFACT 송장 스키마로 변환되어야 합니다. BizTalk Services EAI 프로젝트는 그렇게 진행합니다.

메시지를 변환하는 BizTalk Services 프로젝트, **InvoiceProcessingBridge**는 다운로드한 샘플의 일부로 포함되어 있습니다. 프로젝트는 다음 아티팩트를 포함합니다.

* **INHOUSEINVOICE.XSD** – Northwind로 전송되는 내부 송장 스키마입니다.
* **EFACT_D93A_INVOIC.XSD** – 표준 EDIFACT 송장 스키마입니다.
* **EFACT_4.1_CONTRL.XSD** – Northwind가 Contoso에 보내는 EDIFACT 승인 스키마입니다.
* **INHOUSEINVOICE_to_D93AINVOIC.TRFM** – 내부 송장 스키마를 표준 EDIFACT 송장 스키마에 매핑하는 변환입니다.  

### <a name="create-the-biztalk-services-project"></a>BizTalk Services 프로젝트 만들기
1. Visual Studio 솔루션에서 InvoiceProcessingBridge 프로젝트를 확장한 다음 **MessageFlowItinerary.bcs** 파일을 엽니다.
2. 캔버스에서 아무 곳이나 클릭하고 속성 상자에서 **BizTalk Services URL** 을 설정하여 BizTalk Services 구독 이름을 지정합니다. 예: `https://contosowabs.biztalk.windows.net`
   
   ![][7]  
3. 도구 상자에서 **Xml 단방향 브리지** 를 캔버스로 끌어서 놓습니다. 브리지의 **엔터티 이름** 및 **상대 주소** 속성을 **ProcessInvoiceBridge**로 설정합니다. **ProcessInvoiceBridge** 를 두 번 클릭하여 브리지 구성 화면을 엽니다.
4. **메시지 유형** 상자 내에서 더하기(**+**) 단추를 클릭하여 들어오는 메시지의 스키마를 지정합니다. EAI 브리지에 대해 들어오는 메시지는 항상 내부 송장이므로 **INHOUSEINVOICE**로 설정합니다.
   
   ![][8]  
5. **Xml 변환** 셰이프를 클릭하고 속성 상자에서 **맵** 속성에 대해 줄임표(**...**) 단추를 클릭합니다. **맵 선택** 대화 상자에서 **INHOUSEINVOICE_to_D93AINVOIC** 변환 파일을 선택한 다음 **확인**을 클릭합니다.
   
   ![][9]  
6. **MessageFlowItinerary.bcs**로 돌아가고 도구 상자에서 **양방향 외부 서비스 끝점**을 **ProcessInvoiceBridge**의 오른쪽으로 끌어서 놓습니다. 해당 **엔터티 이름** 속성을 **EDIBridge**로 설정합니다.
7. 솔루션 탐색기에서 **MessageFlowItinerary.bcs**를 확장하고 **EDIBridge.config** 파일을 두 번 클릭합니다. **EDIBridge.config** 의 내용을 다음으로 바꿉니다.
   
   > [!NOTE]
   > .config 파일을 편집해야 하는 이유는 무엇입니까? 브리지 디자이너 캔버스에 추가한 외부 서비스 끝점은 이전에 배포한 EDI 브리지를 나타냅니다. EDI 브리지는 양방향 브리지이며 송신 측과 수신 측이 있습니다. 그러나 브리지 디자이너에 추가한 EAI 브리지는 단방향 브리지입니다. 그러므로 두 브리지의 서로 다른 메시지 교환 패턴을 처리하기 위해 .config 파일에 해당 구성을 포함하여 사용자 지정 브리지 동작을 사용합니다. 또한 사용자 지정 동작은 또한 EDI 송신 브리지 끝점에 대한 인증을 처리합니다. 이 사용자 지정 동작은 [BizTalk Services 브리지 연결 샘플 - EAI에서 EDI로](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104)에 별도 샘플로 제공됩니다. 이 솔루션은 샘플을 다시 사용합니다.  
   > 
   > 
   
   ```
   <?xml version="1.0" encoding="utf-8"?>
   <configuration>
     <system.serviceModel>
       <extensions>
         <behaviorExtensions>
           <add name="BridgeAuthentication" 
                 type="Microsoft.BizTalk.Bridge.Behaviour.BridgeBehaviorElement, Microsoft.BizTalk.Bridge.Behaviour, Version=1.0.0.0, Culture=neutral, PublicKeyToken=ae58f69b69495c05" />
         </behaviorExtensions>
       </extensions>
       <behaviors>
         <endpointBehaviors>
           <behavior name="BridgeAuthenticationConfiguration">
             <!-- Enter the ACS namespace, issuer name and issuer secret of the BizTalk Services deployment -->
             <BridgeAuthentication acsnamespace="[YOUR ACS NAMESPACE]" 
                                   issuername="owner" 
                                   issuersecret="[YOUR ACS SECRET]" />
             <webHttp />
           </behavior>
         </endpointBehaviors>
       </behaviors>
       <bindings>
         <webHttpBinding>
           <binding name="BridgeBindingConfiguration">
             <security mode="Transport" />
           </binding>
         </webHttpBinding>
       </bindings>
       <client>
         <clear />
         <!--
           Go BizTalk Portal > Agreement > Send Settings > Inbound URL
           Copy the Endpoint URL and paste it in the below address field
         -->
         <endpoint name="TwoWayExternalServiceEndpointReference1" 
                   address="[YOUR EDI BRIDGE SEND URI]" 
                   behaviorConfiguration="BridgeAuthenticationConfiguration" 
                   binding="webHttpBinding" 
                   bindingConfiguration="BridgeBindingConfiguration" 
                   contract="System.ServiceModel.Routing.IRequestReplyRouter" />
       </client>
     </system.serviceModel>
   </configuration>
   
   ```
8. 구성 세부 정보를 포함하도록 EDIBridge.config 파일 업데이트
   
   * *<behaviors>*아래에서 BizTalk Services 구독에 연결된 ACS 네임스페이스 및 키를 제공합니다.
   * *<client>* 아래에서 EDI 송신 규약이 배포되는 끝점을 제공합니다.
   
   변경 내용을 저장하고 구성 파일을 닫습니다.
9. 도구 상자에서 **커넥터**를 클릭하고 **ProcessInvoiceBridge**와 **EDIBridge** 구성 요소를 연결합니다. 커넥터를 선택하고 속성 상자에서 **필터 조건**을 **모두 일치**로 설정합니다. 이렇게 하면 EAI 브리지에서 처리된 모든 메시지가 EDI 브리지에 라우팅될 수 있도록 합니다.
   
   ![][10]  
10. 솔루션에 변경 내용을 저장합니다.  

### <a name="deploy-the-project"></a>프로젝트 배포
1. BizTalk Services 프로젝트를 만든 컴퓨터에서 BizTalk Services 구독에 대한 SSL 인증서를 다운로드하고 설치합니다. BizTalk Services에서 **대시보드**를 클릭한 다음 **SSL 인증서 다운로드**를 클릭합니다. 인증서를 두 번 클릭하고 설치를 완료하라는 프롬프트를 따릅니다. **신뢰할 수 있는 루트 인증 기관** 인증서 저장소에 인증서를 설치해야 합니다.
2. Visual Studio 솔루션 탐색기에서 **InvoiceProcessingBridge** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **배포**를 클릭합니다.
3. 이미지에 표시된 대로 값을 입력한 다음 **배포**를 클릭합니다. BizTalk Services 대시보드에서 **연결 정보** 를 클릭하여 BizTalk Services에 대한 ACS 자격 증명을 가져올 수 있습니다.
   
   ![][11]  
   
   출력 창에서 EAI 브리지가 배포되는 끝점(예: `https://contosowabs.biztalk.windows.net/default/ProcessInvoiceBridge`)을 복사합니다. 이 끝점 URL은 나중에 필요합니다.  

## <a name="step-4-test-the-solution"></a>4단계: 솔루션 테스트
이 항목에서는 샘플의 일부로 제공되는 **자습서 클라이언트** 응용 프로그램을 사용하여 솔루션을 테스트하는 방법을 살펴봅니다.  

1. Visual Studio에서 F5 키를 눌러 **자습서 클라이언트**를 시작합니다.
2. 화면은 Service Bus 큐를 만든 단계의 미리 채워진 값을 가져야 합니다. **다음**을 누릅니다.
3. 다음 창에서 BizTalk Services 구독에 대한 ACS 자격 증명 및 EAI 및 EDI(수신) 브리지가 배포되는 끝점을 제공합니다.
   
   이전 단계에서 EAI 브리지 끝점을 복사했습니다. EDI 수신 브리지 끝점의 경우 BizTalk Services 포털에서 규약 > 수신 설정 > 전송 > 끝점으로 이동합니다.
   
   ![][12]  
4. 다음 창의 Contoso 아래에서 **내부 송장 보내기** 단추를 클릭합니다. 파일 열기 대화 상자에서 INHOUSEINVOICE.txt 파일을 엽니다. 파일의 내용을 확인한 다음 **확인** 을 클릭하여 송장을 보냅니다.
   
   ![][13]  
5. 몇 초 후에 Northwind에서 송장을 수신합니다. **메시지 보기** 링크를 클릭하여 Northwind가 받은 송장을 봅니다. Northwind가 송장을 받은 방식은 표준 EDIFACT 스키마이며 Contoso가 보낸 방식은 내부 스키마입니다.
   
   ![][14]  
6. 송장을 선택한 다음 **승인 보내기**를 클릭합니다. 표시되는 대화 상자에서 교환 ID가 받은 송장과 보낸 승인에서 동일한지를 확인합니다. **승인 보내기** 대화 상자에서 확인을 클릭합니다.
   
   ![][15]  
7. 몇 초 후에 Contoso에서 승인이 성공적으로 수신됩니다.
   
   ![][16]  

## <a name="step-5-optional-send-edifact-invoice-in-batches"></a>5단계(선택): 일괄 처리로 EDIFACT 송장 보내기
BizTalk Services EDI 브리지는 나가는 메시지의 일괄 처리도 지원합니다. 이 기능은 개별 메시지 대신 일괄 처리 메시지(특정 조건 충족) 수신을 선호하는 수신 파트너에게 유용합니다.

일괄 처리로 작업할 때 가장 중요한 부분은 릴리스 조건이라고도 하는 실제 일괄 처리 릴리스입니다. 릴리스 조건은 수신 파트너가 원하는 메시지를 받는 방식에 따라 다를 수 있습니다. 일괄 처리가 활성화되는 경우 EDI 브리지는 릴리스 조건이 충족될 때까지 수신 파트너에게 나가는 메시지를 보내지 않습니다. 예를 들어 메시지 크기에 따른 일괄 처리 기준은 'n' 메시지가 일괄 처리된 경우에만 일괄 처리를 디스패치합니다. 일괄 처리가 매일 정해진 시간에 전송되도록 일괄 처리 조건은 시간 기반이 될 수도 있습니다. 이 솔루션에서는 조건에 따라 메시지 크기를 시도합니다.

1. BizTalk Services 포털에서 이전에 만든 규약을 클릭합니다. 송신 설정 > 일괄 처리 > Batch 추가를 클릭합니다.
2. 일괄 처리 이름으로 **InvoiceBatch**를 입력하고 설명을 제공한 다음 **다음**을 클릭합니다.
3. 일괄 처리해야 하는 메시지를 정의하는 일괄 처리 조건을 지정합니다. 이 솔루션에서는 모든 메시지를 일괄 처리합니다. 따라서 고급 정의 사용 옵션을 선택하고 **1 = 1**을 입력합니다. 이는 항상 true가 되는 조건이므로 모든 메시지가 일괄 처리됩니다. **다음**을 누릅니다.
   
   ![][17]  
4. 일괄 처리 릴리스 조건을 지정합니다. 드롭다운 상자에서 **MessageCountBased**를 선택하고 **수**에 대해 **3**을 지정합니다. 세 개의 메시지 일괄 처리가 Northwind로 전송됨을 의미합니다. **다음**을 누릅니다.
   
   ![][18]  
5. 요약을 검토한 다음 **저장**을 클릭합니다. **배포** 를 클릭하여 규약을 다시 배포합니다.
6. **자습서 클라이언트**로 돌아가 **내부 송장 보내기**를 클릭하고 송장을 보내도록 프롬프트를 따릅니다. 일괄 처리 크기가 맞지 않기 때문에 Northwind에서 받는 송장이 없음을 확인하게 됩니다. Northwind로 전송되는 세 개의 송장 메시지를 갖도록 이 단계를 두 번 더 반복합니다. 이는 3개의 메시지 일괄 처리 릴리스 조준을 충족하고 이제 Northwind에서 송장을 확인할 수 있습니다.

<!--Image references-->
[1]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-1.PNG  
[2]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-2.PNG  
[3]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-3.PNG
[4]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-4.PNG  
[5]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-5.PNG  
[6]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-6.PNG  
[7]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-7.PNG  
[8]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-8.PNG
[9]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-9.PNG  
[10]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-10.PNG  
[11]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-11.PNG  
[12]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-12.PNG  
[13]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-13.PNG
[14]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-14.PNG  
[15]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-15.PNG  
[16]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-16.PNG  
[17]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-17.PNG  
[18]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-18.PNG

