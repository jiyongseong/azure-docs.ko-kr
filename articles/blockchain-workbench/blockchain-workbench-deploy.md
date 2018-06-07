---
title: Azure Blockchain Workbench 배포
description: Azure Blockchain Workbench 배포 방법
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/17/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 484c7a17fec4ee94e3170e93eb1438af688d101e
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34303946"
---
# <a name="deploy-azure-blockchain-workbench"></a>Azure Blockchain Workbench 배포

Azure Blockchain Workbench는 Azure Marketplace에서 솔루션 템플릿을 사용하여 배포됩니다. 템플릿은 블록체인 응용 프로그램을 만드는 데 필요한 구성 요소의 배포를 간소화합니다. 배포되면 Blockchain Workbench는 사용자와 블록체인 응용 프로그램을 만들고 관리할 수 있는 클라이언트 앱에 대한 액세스를 제공합니다.

Blockchain Workbench의 구성 요소에 대한 자세한 내용은 [Azure Blockchain Workbench 아키텍처](blockchain-workbench-architecture.md)를 참조하세요.

## <a name="prepare-for-deployment"></a>배포 준비

블록체인 Workbench를 사용하면 블록체인 기반 응용 프로그램을 빌드하는 데 주로 사용되는 일련의 관련 Azure 서비스와 함께 블록체인 원장을 배포할 수 있습니다. 블록체인 Workbench를 배포하면 Azure 구독의 리소스 그룹 내에서 다음과 같은 Azure 서비스가 프로비전됩니다.

* 1 Event Grid 항목
* 1 Service Bus 네임스페이스
* 1 Application Insights
* 1 SQL Database(표준 S0)
* 2 App Services(표준)
* 2 Azure Key Vaults
* 2 Azure Storage 계정(표준 LRS)
* 2 가상 머신 확장 집합(유효성 검사기 및 작업자 노드용)
* 2 Virtual Networks(각 가상 네트워크에 대한 부하 분산 장치, 네트워크 보안 그룹 및 공용 IP 주소 포함)
* 선택 사항: Azure Monitor

다음은 **myblockchain** 리소스 그룹에서 만든 예제 배포입니다.

![예제 배포](media/blockchain-workbench-deploy/example-deployment.png)

블록체인 Workbench의 비용은 기본 Azure 서비스 비용의 집계입니다. Azure 서비스에 대한 가격 책정 정보는 [가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용하여 계산할 수 있습니다.

Azure Blockchain Workbench를 배포하기 전에는 몇 가지 필수 구성 요소가 필요합니다. 필수 구성 요소에는 Azure AD 구성 및 응용 프로그램 등록이 포함됩니다.

### <a name="blockchain-workbench-api-app-registration"></a>Blockchain Workbench API 앱 등록

Blockchain Workbench 배포에는 Azure AD 응용 프로그램의 등록이 필요합니다. Azure AD(Azure Active Directory) 테넌트에서 앱을 등록해야 합니다. 기존 테넌트를 사용하거나 새 테넌트를 만들 수 있습니다. 기존 Azure AD 테넌트를 사용하는 경우 Azure AD 테넌트 내에서 응용 프로그램을 등록할 수 있는 충분한 권한이 필요합니다. 응용 프로그램 등록은 Workbench가 배포되는 구독의 구독 관리자 테넌트에 있어야 합니다. Azure AD 테넌트에 대한 자세한 내용은 [Active Directory 테넌트를 가져오는 방법](../active-directory/develop/active-directory-howto-tenant.md) 및 [Azure Active Directory와 응용 프로그램 통합](../active-directory/develop/active-directory-integrating-applications.md)을 참조하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 오른쪽 위 모서리에서 사용자의 계정을 선택하고 원하는 Azure AD 테넌트로 전환합니다. 테넌트는 Workbench가 배포되는 구독의 구독 관리자 테넌트에 있어야 하며, 사용자에게 응용 프로그램을 등록할 수 있는 충분한 권한이 있어야 합니다.
3. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다. **앱 등록** > **새 응용 프로그램 등록**을 선택합니다.

    ![앱 등록](media/blockchain-workbench-deploy/app-registration.png)

4. 응용 프로그램에 사용할 **이름** 및 **로그온 URL**을 입력합니다. 배포하는 동안 값이 변경되므로 자리 표시자 값을 사용할 수 있습니다. 

    ![앱 등록 만들기](media/blockchain-workbench-deploy/app-registration-create.png)

    |설정  | 값  |
    |---------|---------|
    |Name | `Blockchain API` |
    |응용 프로그램 형식 |웹앱/API|
    |로그온 URL | `https://blockchainapi` |

5. **만들기**를 선택하여 Azure AD 응용 프로그램을 등록합니다.

### <a name="modify-application-manifest"></a>응용 프로그램 매니페스트 수정

다음으로 Blockchain Workbench 관리자를 지정하려면 Azure AD 내에서 응용 프로그램 역할을 사용하도록 응용 프로그램 매니페스트를 수정해야 합니다.  응용 프로그램 매니페스트에 대한 자세한 내용은 [Azure Active Directory 응용 프로그램 매니페스트](../active-directory/develop/active-directory-application-manifest.md)를 참조하세요.

1. 등록한 응용 프로그램에 대해 등록된 응용 프로그램 세부 정보 창에서 **매니페스트**를 선택합니다.
2. GUID를 생성합니다. PowerShell 명령 `[guid]::NewGuid()` 또는 온라인 도구를 사용하여 GUID를 생성할 수 있습니다. 
3. 매니페스트의 **appRoles** 섹션을 업데이트하려고 합니다. 매니페스트 편집 창에서 **편집**을 선택하고 `"appRoles": []`를 제공된 JSON으로 바꿉니다. **id** 필드에 대한 값을 사용자가 생성한 GUID로 바꾸었는지 확인합니다. 

    ``` json
    "appRoles": [
         {
           "allowedMemberTypes": [
             "User",
             "Application"
           ],
           "displayName": "Administrator",
           "id": "<A unique GUID>",
           "isEnabled": true,
           "description": "Blockchain Workbench administrator role allows creation of applications, user to role assignments, etc.",
           "value": "Administrator"
         }
       ],
    ```

    ![매니페스트 편집](media/blockchain-workbench-deploy/edit-manifest.png)

    > [!IMPORTANT]
    > 값 **관리자**는 Blockchain Workbench 관리자를 식별하는 데 필요합니다.

4.  **저장**을 클릭하여 응용 프로그램 매니페스트 변경 내용을 저장합니다.

### <a name="add-graph-api-required-permissions"></a>Graph API 필수 사용 권한 추가

API 응용 프로그램은 디렉터리에 액세스하는 사용자로부터 사용 권한을 요청해야 합니다. API 응용 프로그램에 대한 다음과 같은 필요한 권한을 설정합니다.

1. Blockchain API 앱 등록에서 **설정 > 필요한 권한 > API 선택 > Microsoft Graph**를 선택합니다.

    ![API 선택](media/blockchain-workbench-deploy/client-app-select-api.png)

    **선택**을 클릭합니다.

2. **응용 프로그램 사용 권한**의 **액세스 허용**에서 **모든 사용자의 전체 프로필 읽기**를 선택합니다.

    ![액세스 허용](media/blockchain-workbench-deploy/client-app-read-perms.png)

    **선택**을 클릭한 다음, **완료**를 클릭합니다.

3. **필요한 권한**에서 **사용 권한 부여**를 선택한 다음, 확인 프롬프트에서 **예**를 선택합니다.

   ![권한 부여](media/blockchain-workbench-deploy/client-app-grant-permissions.png)

   권한을 부여하면 Blockchain Workbench는 디렉터리의 사용자에 액세스할 수 있습니다. 읽기 권한은 Blockchain Workbench에서 구성원을 검색 및 추가하는 데 필요합니다.

### <a name="add-graph-api-key-to-application"></a>응용 프로그램에 Graph API 키 추가

Blockchain Workbench는 블록체인 응용 프로그램과 상호 작용하는 사용자를 위한 주 ID 관리 시스템으로 Azure AD를 사용합니다. Blockchain Workbench가 Azure AD에 액세스하고 이름 및 이메일과 같은 사용자 정보를 검색하려면 액세스 키를 추가해야 합니다. Blockchain Workbench는 키를 사용하여 Azure AD로 인증합니다.

1. 등록한 응용 프로그램의 경우 등록된 응용 프로그램 세부 정보 창에서 **설정**을 선택합니다.
2. **키**를 선택합니다.
3. 키 **설명**을 지정하고 **만료** 기간 값을 선택하여 새로운 키를 추가합니다. 

    ![키 만들기](media/blockchain-workbench-deploy/app-key-create.png)

    |설정  | 값  |
    |---------|---------|
    | 설명 | `Service` |
    | 만료 | 만료 기간 선택 |

4. **저장**을 선택합니다. 
5. 키의 값을 복사하고 나중에 사용할 수 있도록 저장합니다. 배포를 위해 필요합니다.

    > [!IMPORTANT]
    >  배포를 위해 키를 저장하지 않은 경우 새 키를 생성해야 합니다. 키 값은 나중에 포털에서 검색할 수 없습니다.

### <a name="get-application-id"></a>응용 프로그램 ID 가져오기

응용 프로그램 ID 및 테넌트 정보는 배포를 위해 필요합니다. 배포하는 동안 사용에 대한 정보를 수집하고 저장합니다.

1. 등록한 응용 프로그램의 경우 **설정** > **속성**을 선택합니다.
2.  **속성** 창에서 배포하는 동안 나중에 사용할 수 있도록 다음 값을 복사하여 저장합니다.

    ![API 앱 속성](media/blockchain-workbench-deploy/app-properties.png)

    | 저장할 설정  | 배포에서 사용 |
    |------------------|-------------------|
    | 응용 프로그램 UI | Azure Active Directory 설정 > 응용 프로그램 ID |

### <a name="get-tenant-domain-name"></a>테넌트 도메인 이름 가져오기

응용 프로그램이 등록된 Active Directory 테넌트 도메인 이름을 수집하고 저장합니다. 

왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다. **사용자 지정 도메인 이름**을 선택합니다. 도메인 이름을 복사하여 저장합니다.

![도메인 이름](media/blockchain-workbench-deploy/domain-name.png)

## <a name="deploy-blockchain-workbench"></a>Blockchain Workbench 배포

필수 단계가 완료되면 Blockchain Workbench를 배포할 준비가 되었습니다. 다음 섹션에서는 프레임워크를 배포하는 방법을 간략하게 설명합니다.

1.  [Azure Portal](https://portal.azure.com)에 로그인합니다.
2.  오른쪽 위 모서리에서 사용자의 계정을 선택하고 Azure Blockchain Workbench를 배포할 원하는 Azure AD 테넌트로 전환합니다.
3.  왼쪽 창에서 **리소스 만들기**를 선택합니다. **Marketplace 검색** 검색 창에서 `Azure Blockchain Workbench`를 검색합니다. 

    ![Marketplace 검색 창](media/blockchain-workbench-deploy/marketplace-search-bar.png)

4.  **Azure Blockchain Workbench**를 선택합니다.

    ![Marketplace 검색 결과](media/blockchain-workbench-deploy/marketplace-search-results.png)

4.  **만들기**를 선택합니다.
5.  기본 설정을 완료합니다.

    ![Azure Blockchain Workbench 만들기](media/blockchain-workbench-deploy/blockchain-workbench-settings-basic.png)

    | 설정 | 설명  |
    |---------|--------------|
    | 리소스 접두사 | 배포에 사용할 짧고 고유한 ID입니다. 이 값은 리소스 이름 지정에 대한 기준으로 사용됩니다. |
    | VM 사용자 이름 | 사용자 이름은 모든 VM(가상 머신)에 대한 관리자로 사용됩니다. |
    | 인증 유형 | VM에 연결하는 데 암호 또는 키 중에 무엇을 사용할지 선택합니다. |
    | 암호 | VM에 연결하는 데 암호가 사용됩니다. |
    | SSH | **ssh-rsa**로 시작하는 한 줄 형식의 RSA 공개 키를 사용하거나 여러 줄 PEM 형식을 사용합니다. Linux 및 OS X에서는 `ssh-keygen`을, Windows에서는 PuTTYGen을 사용하여 SSH 키를 생성할 수 있습니다. SSH 키에 대한 자세한 정보는 [Azure에서 Windows를 통해 SSH 키를 사용하는 방법](../virtual-machines/linux/ssh-from-windows.md)을 참조하세요. |
    | 데이터베이스 암호/데이터베이스 암호 확인 | 배포의 일부로 생성된 데이터베이스에 대한 액세스에 사용할 암호를 지정합니다. |
    | 배포 지역 | Blockchain Workbench 리소스를 배포할 위치를 지정합니다. 최상의 가용성을 위해 이 위치는 **위치** 설정과 일치해야 합니다. |
    | 구독 | 배포에 사용할 Azure 구독을 지정합니다. |
    | 리소스 그룹 | **새로 만들기**를 선택하여 새 리소스 그룹을 만들고 고유한 리소스 그룹 이름을 지정합니다. |
    | 위치 | 프레임워크를 배포할 영역을 지정합니다. |

6.  **확인**을 선택하여 기본 설정 구성 섹션을 완료합니다.

7.  **Azure Active Directory 설정**을 완료합니다.

    ![Azure AD 설정](media/blockchain-workbench-deploy/blockchain-workbench-settings-aad.png)

    | 설정 | 설명  |
    |---------|--------------|
    | 도메인 이름 | [테넌트 도메인 이름 가져오기](#get-tenant-domain-name) 필수 구성 요소 섹션에서 수집된 Azure AD 테넌트를 사용합니다. |
    | 응용 프로그램 UI | [응용 프로그램 ID 가져오기](#get-application-id) 필수 구성 요소 섹션에서 수집한 블록체인 클라이언트 앱 등록의 응용 프로그램 ID를 사용합니다. |
    | 응용 프로그램 키 | [응용 프로그램에 Graph API 키 추가](#add-graph-api-key-to-application) 필수 구성 요소 섹션에서 수집한 블록체인 클라이언트 앱 등록의 응용 프로그램 ID를 사용합니다. |


8.  **확인**을 클릭하여 Azure AD 매개 변수 구성 섹션을 완료합니다.

9.  **네트워크 크기 및 성능** 설정을 완료합니다.

    ![네트워크 및 성능 설정](media/blockchain-workbench-deploy/blockchain-workbench-settings-network.png)

    | 설정 | 설명  |
    |---------|--------------|
    | 블록체인 노드 수 | 네트워크에 배포할 Ethereum PoA 유효성 검사기 노드의 수를 선택합니다. |
    | 저장소 성능 | 블록체인 네트워크에 대한 기본 VM 저장소 성능을 선택합니다. |
    | 가상 머신 크기 | 블록체인 네트워크에 대한 기본 VM 크기를 선택합니다. |

10. **확인**을 선택하여 네트워크 크기와 성능 섹션을 완료합니다.

11. **Azure Monitor** 설정을 완료합니다.

    ![Azure Monitor](media/blockchain-workbench-deploy/blockchain-workbench-settings-oms.png)

    | 설정 | 설명  |
    |---------|--------------|
    | 모니터링 | Azure Monitor를 사용하여 블록 체인 네트워크를 모니터링할 수 있도록 할지 여부를 선택합니다. |
    | 기존 Log Analytics 인스턴스에 연결 | 기존 Log Analytics 인스턴스를 사용할지 아니면 새 인스턴스를 만들지 여부를 선택합니다. 기존 인스턴스를 사용하는 경우, 작업 영역 ID와 기본 키를 입력합니다. |

12. **확인**을 클릭하여 Azure Monitor 섹션을 완료합니다.

13. 매개 변수가 정확한지 확인하기 위해 요약을 검토합니다.

    ![요약](media/blockchain-workbench-deploy/blockchain-workbench-summary.png)

14. **만들기**를 선택하여 약관에 동의하고 Azure Blockchain Workbench를 배포합니다.

배포는 최대 90분까지 걸릴 수 있습니다. Azure Portal을 사용하여 진행률을 모니터링할 수 있습니다. 새로 만든 리소스 그룹에서 **배포 > 개요**를 선택하여 배포된 아티팩트의 상태를 확인합니다.

## <a name="blockchain-workbench-web-url"></a>Blockchain Workbench 웹 URL

Blockchain Workbench의 배포가 완료되면 새 리소스 그룹에 사용자의 Blockchain Workbench 리소스가 포함됩니다. Blockchain Workbench 서비스는 웹 URL을 통해 액세스됩니다. 다음 단계에서는 배포된 프레임워크의 웹 URL을 검색하는 방법을 설명합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 탐색 창에서 **리소스 그룹**을 선택합니다.
3. Blockchain Workbench를 배포할 때 지정한 리소스 그룹 이름을 선택합니다.
4. 목록을 형식 기준으로 사전순으로 정렬하려면 **형식** 열 제목을 클릭합니다.
5. 형식이 **App Service**인 리소스가 두 개 있습니다. “-api” 접미사가 *없는* 리소스 유형 **App Service**를 선택합니다.

    ![App Service 목록](media/blockchain-workbench-deploy/resource-group-list.png)

6.  App Service의 **Essentials** 섹션에서 배포된 Blockchain Workbench에 대한 웹 URL을 나타내는 **URL** 값을 복사합니다.

    ![App Service Essentials](media/blockchain-workbench-deploy/app-service.png)

사용자 지정 도메인 이름을 Blockchain Workbench와 연결하려면 [Traffic Manager를 사용하는 Azure App Service의 웹앱에 대한 사용자 지정 도메인 이름 구성](../app-service/web-sites-traffic-manager-custom-domain-name.md)을 참조하세요.

## <a name="configuring-the-reply-url"></a>회신 URL 구성

Azure Blockchain Workbench가 배포되고 나면, 다음 단계는 Azure AD(Azure Active Directory) 클라이언트 응용 프로그램이 배포된 Blockchain Workbench 웹 URL의 올바른 **회신 URL**에 등록되어 있는지 확인합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. Azure AD 클라이언트 응용 프로그램을 등록한 테넌트에 있는지 확인합니다.
3. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다. **앱 등록**을 선택합니다.
4. 필수 구성 요소 섹션에 등록한 Azure AD 클라이언트 응용 프로그램을 선택합니다.
5. **설정 > 회신 URL**을 선택합니다.
6. **Azure Blockchain Workbench 웹 URL** 섹션에서 검색한 Azure Blockchain Workbench 배포의 주 웹 URL을 지정합니다. 회신 URL은 `https://`를 접두사로 사용합니다. 예를 들어 `https://myblockchain2-7v75.azurewebsites.net`

    ![회신 URL](media/blockchain-workbench-deploy/configure-reply-url.png)

7. **저장**을 선택하여 클라이언트 등록을 업데이트합니다.

## <a name="remove-a-deployment"></a>배포 제거

배포가 더 이상 필요 없는 경우 블록체인 Workbench 리소스 그룹을 삭제하여 배포를 제거할 수 있습니다.

1. Azure Portal의 왼쪽 탐색 창에서 **리소스 그룹**으로 이동하고 삭제하려는 리소스 그룹을 선택합니다. 
2. **리소스 그룹 삭제**를 선택합니다. 리소스 그룹 이름을 입력하여 삭제를 확인하고 **삭제**를 선택합니다.

    ![리소스 그룹 삭제](media/blockchain-workbench-deploy/delete-resource-group.png)

## <a name="next-steps"></a>다음 단계

이 방법 문서에서 Azure Blockchain Workbench를 배포했습니다. 블록 체인 응용 프로그램을 만드는 방법을 알아보려면 다음 사용법 문서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench에서 블록체인 응용 프로그램 만들기](blockchain-workbench-create-app.md)