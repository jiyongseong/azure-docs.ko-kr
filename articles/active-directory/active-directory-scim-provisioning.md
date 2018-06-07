---
title: Azure Active Directory에서 SCIM을 사용하여 앱 프로비전 자동화 | Microsoft Docs
description: Azure Active Directory는 SCIM 프로토콜 사양에 정의된 인터페이스를 가진 웹 서비스가 향하는 응용 프로그램 또는 ID 저장소에 사용자 및 그룹을 자동으로 프로비전할 수 있습니다.
services: active-directory
documentationcenter: ''
author: asmalser-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.custom: aaddev;it-pro;seohack1
ms.openlocfilehash: 19a1ae7ae7acc6fe09a529dd174363735343027e
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33932016"
---
# <a name="using-system-for-cross-domain-identity-management-to-automatically-provision-users-and-groups-from-azure-active-directory-to-applications"></a>도메인 간 ID 관리용 시스템을 사용하여 사용자 및 그룹을 Azure Active Directory에서 응용 프로그램으로 자동 프로비전

## <a name="overview"></a>개요
Azure AD(Active Directory)는 [SCIM(System for Cross-Domain Identity Management) 2.0 프로토콜 사양](https://tools.ietf.org/html/draft-ietf-scim-api-19)에 정의된 인터페이스를 가진 웹 서비스가 향하는 응용 프로그램 또는 ID 저장소에 사용자 및 그룹을 자동으로 프로비전할 수 있습니다. Azure Active Directory는 웹 서비스에 할당된 사용자 및 그룹을 만들고 수정하고 삭제하는 요청을 보낼 수 있습니다. 그러면 웹 서비스에서 이러한 요청을 대상 ID 저장소의 작업으로 변환할 수 있습니다. 

![][0]
*그림 1: 웹 서비스를 통해 Azure Active Directory에서 ID 저장소에 프로비전*

이 기능은 Azure AD에서 “고유한 앱 가져오기” 기능과 함께 사용하여 제공하거나 SCIM 웹 서비스가 앞서는 응용 프로그램에 대해 Single Sign-On 및 자동 사용자 프로비저닝을 사용할 수 있습니다.

Azure Active Directory에서 SCIM을 사용하는 방법에 대한 두 가지 사용 사례가 있습니다.

* **SCIM을 지원하는 응용 프로그램에 사용자 및 그룹 프로비전** SCIM 2.0을 지원하며 인증에 OAuth 전달자 토큰을 사용하는 응용 프로그램은 별도의 구성 없이 Azure AD에서 바로 작동합니다.
* **다른 API 기반 프로비전을 지원하는 응용 프로그램에 대한 프로비전 솔루션 빌드** 비 SCIM 응용 프로그램의 경우 Azure AD SCIM 엔드포인트와 응용 프로그램이 사용자 프로비저닝에 대해 지원하는 API 간에 변환하는 SCIM 엔드포인트를 만들 수 있습니다. SCIM 엔드포인트 개발을 지원하기 위해 SCIM 엔드포인트를 제공하고 SCIM 메시지를 변환하는 방법을 보여주는 코드 샘플과 함께 CLI(공용 언어 인프라) 라이브러리가 있습니다.  

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>SCIM을 지원하는 응용 프로그램에 사용자 및 그룹 프로비전
Azure AD는 [SCIM(System for Cross-domain Identity Management) 2](https://tools.ietf.org/html/draft-ietf-scim-api-19) 웹 서비스를 구현하고 인증에 대한 OAuth 전달자 토큰을 수락하는 응용 프로그램에 자동으로 할당된 사용자 및 그룹을 프로비전하도록 구성할 수 있습니다. SCIM 2.0 사양 내에서 응용 프로그램은 다음의 요구 사항을 충족해야 합니다.

* SCIM 프로토콜의 섹션 3.3에 따라 사용자 및/또는 그룹 만들기를 지원합니다.  
* SCIM 프로토콜의 섹션 3.5.2에 따라 패치를 사용하여 사용자 및/또는 그룹 수정을 지원합니다.  
* SCIM 프로토콜의 섹션 3.4.1에 따라 알려진 리소스 검색을 지원합니다.  
* SCIM 프로토콜의 섹션 3.4.2에 따라 사용자 및/또는 그룹 쿼리를 지원합니다.  기본적으로 사용자는 externalId에서 쿼리되고 그룹은 displayName에서 쿼리됩니다.  
* SCIM 프로토콜의 섹션 3.4.2에 따라 ID 및 관리자에 의한 사용자 쿼리를 지원합니다.  
* SCIM 프로토콜의 섹션 3.4.2에 따라 ID 및 멤버에 의한 그룹 쿼리를 지원합니다.  
* SCIM 프로토콜의 섹션 2.1에 따라 권한 부여에 대한 OAuth 전달자 토큰을 수락합니다.

응용 프로그램 공급자 또는 응용 프로그램 공급자의 설명서를 통해 이러한 요구 사항과의 호환성을 확인합니다.

### <a name="getting-started"></a>시작
Azure AD 응용 프로그램 갤러리에 있는 "비-갤러리 응용 프로그램" 기능을 사용하여 이 문서에서 설명한 SCIM 프로필을 지원하는 응용 프로그램을 Azure Active Directory에 연결할 수 있습니다. 연결되면 Azure AD는 할당된 사용자 및 그룹에 응용 프로그램의 SCIM 끝점을 쿼리하고 할당 정보에 따라 사용자 및 그룹을 만들거나 수정하는 동기화 프로세스를 20분마다 실행합니다.

**SCIM을 지원하는 응용 프로그램을 연결하려면:**

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. **Azure Active Directory > Enterprise 응용 프로그램**을 찾아서 **새 응용 프로그램 > 모두 > 비-갤러리 응용 프로그램**을 선택합니다.
3. 응용 프로그램의 이름을 입력하고 **추가** 아이콘을 클릭하여 앱 개체를 만듭니다.
    
  ![][1]
  *그림 2: Azure AD 응용 프로그램 갤러리*
    
4. 결과 화면에서 왼쪽 열의 **프로비전** 탭을 선택합니다.
5. **프로비전 모드** 메뉴에서 **자동**을 선택합니다.
    
  ![][2]
  *그림 3: Azure Portal에서 프로비전 구성*
    
6. **테넌트 URL** 필드에 응용 프로그램의 SCIM 끝점 URL을 입력합니다. 예제: https://api.contoso.com/scim/v2/
7. SCIM 끝점에 Azure AD가 아닌 다른 발급자의 OAuth 전달자 토큰이 필요한 경우 필요한 OAuth 전달자 토큰을 **비밀 토큰** 필드(선택 사항)에 복사합니다. 이 필드를 비워 두면 Azure AD에 각 요청에 따라 Azure AD에서 발급한 OAuth 전달자 토큰이 포함됩니다. ID 공급자로 Azure AD를 사용하는 앱은 Azure AD에서 발급한 토큰의 유효성을 검사할 수 있습니다.
8. **연결 테스트** 단추를 클릭하여 Azure Active Directory에서 SCIM 끝점에 연결을 시도합니다. 시도가 실패하면 오류 정보가 표시됩니다.  
9. 응용 프로그램에 연결 시도가 성공하면 **저장**을 클릭하여 관리자 자격 증명을 저장합니다.
10. **매핑** 섹션에는 선택 가능한 특성 매핑 집합이 두 개 있는데, 하나는 사용자 개체용이고 다른 하나는 그룹 개체용입니다. 각 특성 매핑을 선택하여 Azure Active Directory에서 앱으로 동기화되는 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 앱의 사용자와 그룹을 일치시키는 데 사용됩니다. 저장 단추를 선택하여 변경 내용을 커밋합니다.

    >[!NOTE]
    >필요에 따라 "그룹" 매핑을 사용하지 않도록 설정하여 그룹 개체의 동기화를 비활성화할 수 있습니다. 

11. **설정** 아래의 **범위** 필드는 동기화되는 사용자 및 그룹을 정의합니다. "할당된 사용자 및 그룹만 동기화"(권장)를 선택하면 **사용자 및 그룹** 탭에서 할당된 사용자 및 그룹만 동기화됩니다.
12. 구성이 완료되면 **프로비전 상태**를 **켜기**로 변경합니다.
13. **저장**을 클릭하여 Azure AD 프로비전 서비스를 시작합니다. 
14. 할당된 사용자 및 그룹만 동기화하는 경우(권장) **사용자 및 그룹** 탭을 선택하고 동기화하려는 사용자 및/또는 그룹을 할당합니다.

초기 동기화가 시작되면 **감사 로그** 탭을 사용하여 진행 상황을 모니터링할 수 있습니다. 이 탭에는 앱의 프로비전 서비스에서 수행하는 모든 작업이 표시됩니다. Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting)를 참조하세요.

>[!NOTE]
>초기 동기화는 서비스가 실행되는 동안 약 20분마다 발생하는 차후 동기화보다 더 많은 시간이 걸립니다. 


## <a name="building-your-own-provisioning-solution-for-any-application"></a>응용 프로그램에 대한 고유한 프로비전 솔루션 구축
Azure Active Directory와 상호 작용하는 SCIM 웹 서비스를 만들어서 REST 또는 SOAP 사용자 프로비전 API를 제공하는 거의 모든 응용 프로그램에 대한 Single Sign-On 및 자동 사용자 프로비전을 사용하도록 설정할 수 있습니다.

방법은 다음과 같습니다.

1. Azure AD는 [Microsoft.SystemForCrossDomainIdentityManagement](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/)로 명명된 공용 언어 인프라 라이브러리를 제공합니다. 시스템 통합 업체 및 개발자는 이 라이브러리를 사용하여 응용 프로그램 ID 저장소에 Azure AD를 연결할 수 있는 SCIM 기반 웹 서비스 끝점을 만들고 배포할 수 있습니다.
2. 매핑은 웹 서비스에서 구현되어 스키마에 응용 프로그램에서 필요한 사용자 스키마 및 프로토콜에 표준화된 사용자 스키마를 매핑합니다.
3. 끝점 URL은 응용 프로그램 갤러리에서 사용자 지정 응용 프로그램의 일부로 Azure AD에 등록됩니다.
4. 사용자 및 그룹은 Azure AD에서 이 응용 프로그램에 할당됩니다. 할당 시 큐에 저장하여 대상 응용 프로그램에 동기화됩니다. 큐를 처리하는 동기화 프로세스는 20분마다 실행됩니다.

### <a name="code-samples"></a>코드 샘플
이 과정을 보다 쉽게 하려면 [코드 샘플](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) 의 집합이 SCIM 웹 서비스 끝점을 만들고 자동 프로비전을 보여주도록 제공됩니다. 한 가지 샘플은 사용자 및 그룹을 나타내는 쉼표로 구분된 값의 행이 있는 파일을 유지 관리하는 공급자입니다.  다른 샘플은 Amazon 웹 서비스 ID 및 액세스 관리 서비스에서 작동하는 공급자입니다.  

**필수 구성 요소**

* Visual Studio 2013 이상
* [Azure SDK for .NET](https://azure.microsoft.com/downloads/)
* ASP.NET framework 4.5를 SCIM 끝점으로 사용하도록 지원하는 Windows 컴퓨터입니다. 이 컴퓨터는 클라우드에서 액세스할 수 있어야 합니다.
* [Azure AD Premium의 평가판 또는 사용이 허가된 버전의 Azure 구독](https://azure.microsoft.com/services/active-directory/)
* Amazon AWS 샘플에는 [Visual Studio용 AWS Toolkit](http://docs.aws.amazon.com/AWSToolkitVS/latest/UserGuide/tkv_setup.html)의 라이브러리가 필요합니다. 자세한 내용은 샘플에 포함된 README 파일을 참조하세요.

### <a name="getting-started"></a>시작하기
Azure AD에서 프로비전 요청을 수락할 수 있는 SCIM 끝점을 구현하는 가장 쉬운 방법은 쉼표로 구분된 값(CSV) 파일에 프로비전된 사용자를 출력하는 코드 샘플을 빌드하고 배포하는 것입니다.

**샘플 SCIM 끝점을 만들려면:**

1. [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)에서 코드 샘플 패키지를 다운로드합니다.
2. 패키지의 압축을 풀고 C:\AzureAD-BYOA-Provisioning-Samples와 같은 위치에서 Windows 컴퓨터에 넣습니다.
3. 이 폴더에 Visual Studio에 있는 FileProvisioningAgent 솔루션을 시작합니다.
4. **도구 > 라이브러리 패키지 관리자 > 패키지 관리자 콘솔**을 선택하고, FileProvisioningAgent 프로젝트에 대해 다음 명령을 실행하여 솔루션 참조를 확인합니다.
  ```` 
   Install-Package Microsoft.SystemForCrossDomainIdentityManagement
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   Install-Package Microsoft.Owin.Diagnostics
   Install-Package Microsoft.Owin.Host.SystemWeb
  ````
5. FileProvisioningAgent 프로젝트를 빌드합니다.
6. 관리자 권한으로 Windows에서 명령 프롬프트 응용 프로그램을 시작하고 **cd** 명령을 사용하여 디렉터리를 **\AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug** 폴더로 변경합니다.
7. 다음 명령을 실행하고, <ip-address>를 Windows 컴퓨터의 IP 주소 또는 도메인 이름으로 바꿉니다.
  ````   
   FileAgnt.exe http://<ip-address>:9000 TargetFile.csv
  ````
8. Windows의 **Windows 설정 > 네트워크 및 인터넷 설정**에서 **Windows 방화벽 > 고급 설정**을 선택하고 포트 9000에 인바운드 액세스를 허용하는 **인바운드 규칙**을 만듭니다.
9. Windows 컴퓨터가 라우터 뒤에 있는 경우 라우터는 인터넷에 노출되는 포트 9000과 Windows 컴퓨터에 있는 포트 9000 사이의 네트워크 액세스 변환을 수행하도록 구성되어야 합니다. Azure AD의 경우 클라우드에서 이 엔드포인트에 액세스할 수 있으려면 이 구성이 필요합니다.

**Azure AD에서 샘플 SCIM 끝점을 등록하려면:**

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. **Azure Active Directory > Enterprise 응용 프로그램**을 찾아서 **새 응용 프로그램 > 모두 > 비-갤러리 응용 프로그램**을 선택합니다.
3. 응용 프로그램의 이름을 입력하고 **추가** 아이콘을 클릭하여 앱 개체를 만듭니다. 만든 응용 프로그램 개체는 SCIM 끝점뿐 아니라 Single Sign-On을 프로비전하고 구현하려는 대상 앱을 나타내는 데 사용됩니다.
4. 결과 화면에서 왼쪽 열의 **프로비전** 탭을 선택합니다.
5. **프로비전 모드** 메뉴에서 **자동**을 선택합니다.
    
  ![][2]
  *그림 4: Azure Portal에서 프로비전 구성*
    
6. **테넌트 URL** 필드에 인터넷에 노출된 URL 및 SCIM 끝점의 포트를 입력합니다. 이 항목은 http://testmachine.contoso.com:9000 또는 http://<ip-address>:9000/과 유사합니다. 여기서 <ip-address>는 인터넷에 노출된 IP 주소입니다.  
7. SCIM 끝점에 Azure AD가 아닌 다른 발급자의 OAuth 전달자 토큰이 필요한 경우 필요한 OAuth 전달자 토큰을 **비밀 토큰** 필드(선택 사항)에 복사합니다. 이 필드를 비워 두면 Azure AD에 각 요청에 따라 Azure AD에서 발급한 OAuth 전달자 토큰이 포함됩니다. ID 공급자로 Azure AD를 사용하는 앱은 Azure AD에서 발급한 토큰의 유효성을 검사할 수 있습니다.
8. **연결 테스트** 단추를 클릭하여 Azure Active Directory에서 SCIM 끝점에 연결을 시도합니다. 시도가 실패하면 오류 정보가 표시됩니다.  
9. 응용 프로그램에 연결 시도가 성공하면 **저장**을 클릭하여 관리자 자격 증명을 저장합니다.
10. **매핑** 섹션에는 선택 가능한 특성 매핑 집합이 두 개 있는데, 하나는 사용자 개체용이고 다른 하나는 그룹 개체용입니다. 각 특성 매핑을 선택하여 Azure Active Directory에서 앱으로 동기화되는 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 앱의 사용자와 그룹을 일치시키는 데 사용됩니다. 저장 단추를 선택하여 변경 내용을 커밋합니다.
11. **설정** 아래의 **범위** 필드는 동기화되는 사용자 및 그룹을 정의합니다. "할당된 사용자 및 그룹만 동기화"(권장)를 선택하면 **사용자 및 그룹** 탭에서 할당된 사용자 및 그룹만 동기화됩니다.
12. 구성이 완료되면 **프로비전 상태**를 **켜기**로 변경합니다.
13. **저장**을 클릭하여 Azure AD 프로비전 서비스를 시작합니다. 
14. 할당된 사용자 및 그룹만 동기화하는 경우(권장) **사용자 및 그룹** 탭을 선택하고 동기화하려는 사용자 및/또는 그룹을 할당합니다.

초기 동기화가 시작되면 **감사 로그** 탭을 사용하여 진행 상황을 모니터링할 수 있습니다. 이 탭에는 앱의 프로비전 서비스에서 수행하는 모든 작업이 표시됩니다. Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting)를 참조하세요.

이 샘플을 확인하는 마지막 단계는 Windows 컴퓨터에서 \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug 폴더에 TargetFile.csv 파일을 여는 것입니다. 프로비전 프로세스가 실행되면 이 파일은 할당되고 프로비전된 모든 사용자 및 그룹의 세부 사항을 표시합니다.

### <a name="development-libraries"></a>개발 라이브러리
SCIM 사양을 준수하는 웹 서비스를 개발하려면 먼저 개발 프로세스를 가속화하기 위해 Microsoft에서 제공하는 다음 라이브러리를 숙지합니다. 

1. CLI(공용 언어 인프라) 라이브러리는 C#과 같은 해당 인프라에 따라 언어와 함께 사용하기 위해 제공됩니다. 이러한 라이브러리 중 하나인 [Microsoft.SystemForCrossDomainIdentityManagement.Service](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/)는 다음 일러스트레이션에 표시된 것처럼 Microsoft.SystemForCrossDomainIdentityManagement.IProvider 인터페이스를 선언합니다. 이 라이브러리를 사용하는 개발자는 일반적으로 공급자로 참조되는 클래스를 사용하여 해당 인터페이스를 구현합니다. 이 라이브러리를 사용하면 개발자는 SCIM 사양을 준수하는 웹 서비스를 배포할 수 있습니다. 웹 서비스는 인터넷 정보 서비스 또는 실행 가능한 모든 공용 언어 인프라 어셈블리 내에 호스트할 수 있습니다. 요청은 공급자의 메서드에 대한 호출로 변환되며, 개발자에 의해 일부 ID 저장소에서 작동하도록 프로그래밍됩니다.
  
  ![][3]
  
2. [기본 경로 처리기](http://expressjs.com/guide/routing.html)는 node.js 웹 서비스에 수행된(SCIM 사양에 정의된 대로) 호출을 나타내는 node.js 요청 개체를 구문 분석하는 데 사용할 수 있습니다.   

### <a name="building-a-custom-scim-endpoint"></a>사용자 지정 SCIM 끝점 빌드
CLI 라이브러리를 사용하는 개발자는 실행 가능한 공용 언어 인프라 어셈블리 또는 인터넷 정보 서비스 내에서 해당 서비스를 호스트할 수 있습니다. 다음은 http://localhost:9000: 주소에 있는 실행 가능한 어셈블리 내에서 서비스를 호스트하기 위한 샘플 코드입니다. 

    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }

이 서비스에는 루트 인증 기관의 이름이 다음 중 하나인 HTTP 주소 및 서버 인증 인증서가 있어야 합니다. 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* Verisign
* WoSign

서버 인증 인증서는 네트워크 셸 유틸리티를 사용하여 다음과 같이 Windows 호스트의 포트에 바인딩될 수 있습니다. 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  

여기서 appid 인수에 제공된 값은 임의의 GUID(Globally Unique Identifier)인 반면 certhash 인수에 제공된 값은 인증서의 지문입니다.  

인터넷 정보 서비스 내에서 서비스를 호스트하려면 개발자는 어셈블리의 기본 네임스페이스에 있는 Startup이라는 클래스를 사용하여 CLA 코드 라이브러리 어셈블리를 빌드합니다.  이러한 클래스의 샘플은 다음과 같습니다. 

    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }

### <a name="handling-endpoint-authentication"></a>끝점 인증 처리
Azure Active Directory에서 요청은 OAuth 2.0 전달자 토큰을 포함합니다.   요청을 받는 모든 서비스는 예상된 Azure Active Directory 테넌트 대신 Azure Active Directory의 Graph 웹 서비스에 액세스하는 데 대해 발급자를 Azure Active Directory로 인증해야 합니다.  토큰에서 발급자는 "iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"와 같은 iss 클레임으로 식별됩니다.  이 예제에서 상대 주소 세그먼트인 cbb1a5ac-f33b-45fa-9bf5-f37db0fed422가 토큰이 발급된 대신 Azure Active Directory 테넌트의 고유한 발급자인 반면 클레임 값의 기본 주소인 https://sts.windows.net은 Azure Active Directory를 발급자로 식별합니다.  토큰이 Azure Active Directory Graph 웹 서비스에 액세스하기 위해 발급되었다면 해당 서비스의 식별자인 00000002-0000-0000-c000-000000000000는 토큰의 aud 클레임의 값에 있어야 합니다.  

SCIM 서비스 구축을 위해 Microsoft에서 제공하는 CLA 라이브러리를 사용하는 개발자는 Microsoft.Owin.Security.ActiveDirectory 패키지로 다음 단계를 수행하여 Azure Active Directory에서 요청을 인증할 수 있습니다. 

1. 공급자에서 서비스가 시작할 때 마다 호출되는 메서드를 반환함으로써 Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior 속성을 구현합니다. 

  ````
    public override Action\<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration\> StartupBehavior
    {
      get
      {
        return this.OnServiceStartup;
      }
    }

    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
      System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
    {
    }
  ````

2. 해당 메서드에 다음 코드를 추가하여 지정된 테넌트 대신 인증된 서비스의 끝점 중 하나가 Azure AD Graph 웹 서비스에 액세스하는 데 대해 Azure Active Directory에서 발급한 토큰을 갖도록 요청합니다. 

  ````
    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
      System.Web.Http.HttpConfiguration HttpConfiguration configuration)
    {
      // IFilter is defined in System.Web.Http.dll.  
      System.Web.Http.Filters.IFilter authorizationFilter = 
        new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

      // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
      // System.IdentityModel.Token.Jwt.dll.
      SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
        new TokenValidationParameters()
        {
          ValidAudience = "00000002-0000-0000-c000-000000000000"
        };

      // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
      // Microsoft.Owin.Security.ActiveDirectory.dll
      Microsoft.Owin.Security.ActiveDirectory.
      WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
        TokenValidationParameters = tokenValidationParameters,
        Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                      // identifier for this one.  
      };

      applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
    }
  ````


## <a name="user-and-group-schema"></a>사용자 및 그룹 스키마
Azure Active Directory는 두 형식의 리소스를 SCIM 웹 서비스에 프로비전할 수 있습니다.  이러한 형식의 리소스는 사용자 및 그룹입니다.  

사용자 리소스는 http://tools.ietf.org/html/draft-ietf-scim-core-schema 프로토콜 사양에 포함된 스키마 식별자 "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"로 식별됩니다.  "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" 리소스의 특성에 Azure Active Directory에서 사용자의 특성을 기본 매핑한 작업은 아래 테이블 1에 제공됩니다.  

그룹 리소스는 스키마 식별자 http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group으로 식별됩니다.  아래 표 2에서는 Azure Active Directory의 그룹 특성과 http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group 리소스 특성 간의 기본 매핑을 보여 줍니다.  

### <a name="table-1-default-user-attribute-mapping"></a>테이블 1: 기본 사용자 특성 매핑
| Azure Active Directory 사용자 | "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |활성 |
| displayName |displayName |
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |title |
| mail |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |manager |
| mobile |phoneNumbers[type eq "mobile"].value |
| objectId |id |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |

### <a name="table-2-default-group-attribute-mapping"></a>테이블 2: 기본 그룹 특성 매핑
| Azure Active Directory 그룹 | http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group |
| --- | --- |
| displayName |externalId |
| mail |emails[type eq "work"].value |
| mailNickname |displayName |
| members |members |
| objectId |id |
| proxyAddresses |emails[type eq "other"].Value |

## <a name="user-provisioning-and-de-provisioning"></a>사용자 프로비저닝 및 프로비전 해제
다음 일러스트레이션은 다른 ID 저장소에 사용자의 수명 주기를 관리하도록 Azure Active Directory가 SCIM 서비스를 보낸다는 메시지를 보여줍니다. 또한 다이어그램은 이러한 서비스 구축을 위해 Microsoft에서 제공하는 CLI 라이브러리를 사용하여 구현된 SCIM 서비스가 이러한 요청을 어떻게 공급자의 메서드에 호출로 번역하는지를 보여줍니다.  

![][4]
*그림 5: 사용자 프로비저닝 및 시퀀스 프로비전 해제*

1. Azure Active Directory는 externalId 특성 값이 Azure AD의 사용자 mailNickname 특성 값과 일치하는 사용자를 서비스에 쿼리합니다. 쿼리는 이 예제처럼 HTTP(Hypertext Transfer Protocol) 요청으로 표현되며, 여기서 jyoung은 Azure Active Directory에서 사용자의 mailNickname 샘플입니다. 
  ````
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
  ````
  서비스가 SCIM 서비스 구현에 대해 Microsoft에서 제공하는 공용 언어 인프라 라이브러리를 사용하여 작성되면 요청이 서비스 공급자의 쿼리 메서드 호출로 번역됩니다.  해당 메서드의 서명은 다음과 같습니다. 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);
  ````
  Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters 인터페이스의 정의는 다음과 같습니다. 
  ````
    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
      { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
          { get; set; }
        string AttributePath 
          { get; } 
        Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
          { get; }
        string ComparisonValue 
          { get; }
    }

    public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
    {
        Equals
    }
  ````
  externalId 특성의 값이 지정된 사용자에 대한 다음 쿼리 샘플에서 쿼리 메서드에 전달된 인수의 값은 다음과 같습니다. 
  * parameters.AlternateFilters.Count: 1
  * parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
  * parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
  * parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"
  * correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

2. externalId 특성 값이 사용자의 mailNickname 특성 값과 일치하는 사용자를 웹 서비스에 쿼리할 때 응답으로 사용자가 반환되지 않는 경우 Azure Active Directory는 서비스가 Azure Active Directory의 사용자에 해당하는 사용자를 프로비전하도록 요청합니다.  다음은 그러한 요청의 예제입니다. 
  ````
    POST https://.../scim/Users HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas":
      [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
      "externalId":"jyoung",
      "userName":"jyoung",
      "active":true,
      "addresses":null,
      "displayName":"Joy Young",
      "emails": [
        {
          "type":"work",
          "value":"jyoung@Contoso.com",
          "primary":true}],
      "meta": {
        "resourceType":"User"},
       "name":{
        "familyName":"Young",
        "givenName":"Joy"},
      "phoneNumbers":null,
      "preferredLanguage":null,
      "title":null,
      "department":null,
      "manager":null}
  ````
  SCIM 서비스 구현에 대해 Microsoft에서 제공하는 공용 언어 인프라 라이브러리는 요청이 서비스 공급자의 만들기 메서드에 호출을 요청하도록 번역됩니다.  만들기 메서드에는 다음 서명이 있습니다. 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);
  ````
  사용자를 프로비전하는 요청에서 리소스 인수의 값은 Microsoft.SystemForCrossDomainIdentityManagement의 인스턴스입니다. Microsoft.SystemForCrossDomainIdentityManagement.Schemas 라이브러리에 정의된 Core2EnterpriseUser 클래스입니다.  사용자를 프로비전하는 요청이 성공하는 경우 메서드의 구현은 Microsoft.SystemForCrossDomainIdentityManagement의 인스턴스를 반환할 것으로 예상됩니다. 새로 프로비전된 사용자의 고유 식별자에 설정된 식별자 속성의 값을 가진 Core2EnterpriseUser 클래스입니다.  

3. SCIM에 의해 제어되는 ID 저장소에 있는 것으로 알려진 사용자를 업데이트하기 위해 Azure Active Directory는 다음과 같은 요청으로 서비스에서 해당 사용자의 현재 상태를 요청합니다. 
  ````
    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
  ````
  SCIM 서비스 구현에 대해 Microsoft에서 제공하는 공용 언어 인프라 라이브러리를 사용하여 작성된 서비스에서 요청은 서비스 공급자의 검색 메서드에 호출하도록 번역됩니다.  해당 검색 메서드의 서명은 다음과 같습니다. 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
    // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
       Retrieve(
         Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
           parameters, 
           string correlationIdentifier);

    public interface 
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters
        {
          Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
            ResourceIdentifier 
              { get; }
    }
    public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
    {
        string Identifier 
          { get; set; }
        string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
          { get; set; }
    }
  ````
  사용자의 현재 상태를 검색하는 요청 예제에서 매개 변수 인수 값으로 제공되는 개체의 속성 값은 다음과 같습니다. 
  
  * 식별자: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

4. 참조 특성을 업데이트해야 하는 경우 Azure Active Directory는 서비스를 쿼리하여 서비스에 의해 제어되는 ID 저장소에 있는 참조 특성의 현재 값이 Azure Active Directory의 해당 특성 값과 이미 일치하는지를 확인합니다. 사용자의 경우 현재 값이 이 방식으로 쿼리된 유일한 특성은 관리자 특성입니다. 특정 사용자 개체의 관리자 특성 값에 현재 특정 값이 있는지 여부를 결정하는 요청의 예는 다음과 같습니다. 
  ````
    GET ~/scim/Users?filter=id eq 54D382A4-2050-4C03-94D1-E769F1D15682 and manager eq 2819c223-7f76-453a-919d-413861904646&attributes=id HTTP/1.1
    Authorization: Bearer ...
  ````
  특성 쿼리 매개 변수인 ID의 값은 필터 쿼리 매개 변수의 값으로 제공되는 식을 만족하는 사용자 개체가 있다는 것을 의미하고 서비스는 해당 리소스 ID 특성의 값을 포함하는 "urn:ietf:params:scim:schemas:core:2.0:User" 또는 "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" 리소스를 사용하여 응답할 것으로 예상됩니다.  **id** 특성 값은 요청자에게 알려집니다. 이 값은 필터 쿼리 매개 변수의 값에 포함되며, 이 값을 묻는 목적은 실제로 이러한 개체의 존재 여부를 확인하는 필터 식을 만족하는 리소스의 최소 표현을 요청하기 위함입니다.   

  서비스가 SCIM 서비스 구현에 대해 Microsoft에서 제공하는 공용 언어 인프라 라이브러리를 사용하여 작성되면 요청이 서비스 공급자의 쿼리 메서드 호출로 번역됩니다. 매개 변수 인수의 값으로 제공되는 개체의 속성 값은 다음과 같습니다. 
  
  * parameters.AlternateFilters.Count: 2
  * parameters.AlternateFilters.ElementAt(x).AttributePath: "id"
  * parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
  * parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
  * parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
  * parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
  * parameters.RequestedAttributePaths.ElementAt(0): "id"
  * parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

  여기에서 필터 쿼리 매개 변수 식의 순서에 따라 인덱스 x의 값은 0이고 인덱스 y의 값은 1일 수 있습니다. 또는 x 값이 1이고 y 값이 0일 수 있습니다.   

5. 다음은 Azure Active Directory에서 SCIM 서비스로 사용자를 업데이트하는 요청의 예입니다. 
  ````
    PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas": 
      [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
      "Operations":
      [
        {
          "op":"Add",
          "path":"manager",
          "value":
            [
              {
                "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
  ````
  SCIM 서비스 구현에 대한 Microsoft 공용 언어 인프라 라이브러리는 요청이 서비스 공급자의 업데이트 메서드에 호출을 요청하도록 번역됩니다. Update 메서드의 서명은 다음과 같습니다. 
  ````
    // System.Threading.Tasks.Tasks and 
    // System.Collections.Generic.IReadOnlyCollection<T>
    // are defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
    // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

    System.Threading.Tasks.Task Update(
      Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
      string correlationIdentifier);

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
    {
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
      PatchRequest 
        { get; set; }
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
      ResourceIdentifier 
        { get; set; }        
    }

    public class PatchRequest2: 
      Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
    {
    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
        Operations
        { get;}

    public void AddOperation(
      Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
    }

    public class PatchOperation
    {
    public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
      Name
      { get; set; }

    public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
      Path
      { get; set; }

    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
      { get; }

    public void AddValue(
      Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
    }

    public enum OperationName
    {
      Add,
      Remove,
      Replace
    }

    public interface IPath
    {
      string AttributePath { get; }
      System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
      Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
    }

    public class OperationValue
    {
      public string Reference
      { get; set; }

      public string Value
      { get; set; }
    }
  ````
    사용자를 업데이트하는 요청의 예에서 패치 인수의 값으로 제공되는 개체는 다음과 같은 속성 값이 적용됩니다. 
  
  * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
  * (PatchRequest as PatchRequest2).Operations.Count: 1
  * (PatchRequest as PatchRequest2).Operations.ElementAt(0).OperationName: OperationName.Add
  * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Path.AttributePath: "manager"
  * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.Count: 1
  * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Reference: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
  * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Value: 2819c223-7f76-453a-919d-413861904646

6. SCIM 서비스에 의해 제어되는 ID 저장소에서 사용자의 프로비전을 해제하기 위해 Azure AD에서 다음과 같은 요청을 보냅니다. 
  ````
    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
  ````
  서비스가 SCIM 서비스 구현에 대해 Microsoft에서 제공하는 공용 언어 인프라 라이브러리를 사용하여 작성되면 요청이 서비스 공급자의 삭제 메서드 호출로 번역됩니다.   해당 메서드에는 다음 서명이 있습니다. 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
  ````
  사용자의 프로비전을 해제하는 요청의 예에서 resourceIdentifier 인수의 값으로 제공되는 개체는 다음과 같은 속성 값이 적용됩니다. 
  
  * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="group-provisioning-and-de-provisioning"></a>그룹 프로비전 및 프로비전 해제
다음 일러스트레이션은 다른 ID 저장소에 그룹의 수명 주기를 관리하도록 Azure AD가 SCIM 서비스를 보낸다는 메시지를 보여줍니다.  이러한 메시지는 세 가지 부분에서 사용자에 관련된 메시지가 다릅니다. 

* 그룹 리소스의 스키마는 `http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group`으로 식별됩니다.  
* 그룹을 검색하는 요청은 멤버 특성이 요청에 대한 응답에서 제공된 리소스로부터 제외된다고 규정합니다.  
* 참조 특성에 특정 값이 있는지를 확인하는 요청은 멤버 특성에 대한 요청입니다.  

![][5]
*그림 6: 그룹 프로비전 및 시퀀스 프로비전 해제*

## <a name="related-articles"></a>관련 문서
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
* [SaaS 앱에 자동화된 사용자 프로비전/프로비전 해제](active-directory-saas-app-provisioning.md)
* [사용자 프로비저닝에 대한 특성 매핑 사용자 지정](active-directory-saas-customizing-attribute-mappings.md)
* [특성 매핑에 대한 식 작성](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [사용자 프로 비전에 대 한 필터 범위 지정](active-directory-saas-scoping-filters.md)
* [계정 프로비전 알림](active-directory-saas-account-provisioning-notifications.md)
* [SaaS App을 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)

<!--Image references-->
[0]: ./media/active-directory-scim-provisioning/scim-figure-1.PNG
[1]: ./media/active-directory-scim-provisioning/scim-figure-2a.PNG
[2]: ./media/active-directory-scim-provisioning/scim-figure-2b.PNG
[3]: ./media/active-directory-scim-provisioning/scim-figure-3.PNG
[4]: ./media/active-directory-scim-provisioning/scim-figure-4.PNG
[5]: ./media/active-directory-scim-provisioning/scim-figure-5.PNG
