---
title: 오프 라인 환경에서 Azure 스택 앱 서비스 배포 | Microsoft Docs
description: AD FS로 보호 되는 연결이 끊어진된 Azure 스택 환경에서 응용 프로그램 서비스를 배포 하는 방법에 대 한 자세한 지침.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2018
ms.author: anwestg
ms.openlocfilehash: 7084243c0fc84429b585c3e8fd9e5c64df469ec4
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604287"
---
# <a name="add-an-app-service-resource-provider-to-a-disconnected-azure-stack-environment-secured-by-ad-fs"></a>AD FS로 보호 되는 연결이 끊어진된 Azure 스택 환경에 앱 서비스 리소스 공급자 추가

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

> [!IMPORTANT]
> Azure 스택 통합 시스템 1804 업데이트를 적용 하거나 Azure 앱 서비스 1.2를 배포 하기 전에 최신 Azure 스택 개발 키트를 배포 합니다.
>
>

이 문서의 지침에 따라 설치할 수 있습니다는 [앱 서비스 리소스 공급자](azure-stack-app-service-overview.md) 되는 Azure 스택 환경을에:

- 인터넷에 연결 되어 있지
- Active Directory Federation Services (AD FS)에 의해 보안.

앱 서비스 리소스 공급자를 오프 라인 Azure 스택 배포를 추가 하려면 이러한 최상위 작업을 수행 해야 합니다.

1. 완료 된 [사전 요구 사항 단계](azure-stack-app-service-before-you-get-started.md) (같은 인증서를 구입는 많이 걸릴 수를 받는 몇 일 동안).
2. [다운로드 및 설치 및 도우미 파일 추출](azure-stack-app-service-before-you-get-started.md) 인터넷에 연결 하는 컴퓨터에 있습니다.
3. 오프 라인 설치 패키지를 만듭니다.
4. Appservice.exe 설치 관리자 파일을 실행 합니다.

## <a name="create-an-offline-installation-package"></a>오프 라인 설치 패키지 만들기

연결이 끊어진된 환경에 응용 프로그램 서비스를 배포 하려면 먼저 만들어야 합니다 오프 라인 설치 패키지를 인터넷에 연결 하는 컴퓨터에 있습니다.

1. 인터넷에 연결 하는 컴퓨터에서 AppService.exe 설치를 실행 합니다.

2. 클릭 **고급** > **오프 라인 설치 패키지 만들기**합니다.

    ![앱 서비스 설치 관리자][1]

3. 앱 서비스 설치 관리자는 오프 라인 설치 패키지를 만들고를 경로 표시 합니다. 클릭할 수 있는 **폴더 열기** 파일 탐색기에서 폴더를 열려고 합니다.

    ![앱 서비스 설치 관리자](media/azure-stack-app-service-deploy-offline/image02.png)

4. Azure 스택 호스트 컴퓨터에 설치 관리자 (AppService.exe) 및 오프 라인 설치 패키지를 복사 합니다.

## <a name="complete-the-offline-installation-of-app-service-on-azure-stack"></a>Azure 스택 앱 서비스의 오프 라인 설치를 완료 합니다.

1. Azure 스택 Admin Azure 리소스 관리 끝점에 도달할 수 있는 컴퓨터에서 관리자 권한으로 appservice.exe를 실행 합니다.

2. 클릭 **고급** > **오프 라인 설치를 완료**합니다.

    ![앱 서비스 설치 관리자][2]

3. 이전에 만든 오프 라인 설치 패키지의 위치를 찾은 다음 클릭 **다음**합니다.

    ![앱 서비스 설치 관리자](media/azure-stack-app-service-deploy-offline/image04.png)

4. 검토 하 고 Microsoft 소프트웨어 사용 조건에 동의 하 고 클릭 **다음**합니다.

5. 검토 및 제 3 자 사용 조건에 동의 하 고 클릭 **다음**합니다.

6. 앱 서비스 클라우드 구성 정보가 올바른지 확인 합니다. Azure 스택 개발 키트 배포 하는 동안 기본 설정을 사용한 경우 여기에서 기본 값을 수락할 수 있습니다. 그러나 Azure 스택 배포 하거나 통합된 된 시스템에서 배포 하는 옵션은 사용자 지정한 경우 값을 반영 하기 위해이 창에서 편집 해야 합니다. 예를 들어 도메인 접미사 mycloud.com를 사용 하는 경우 Azure 스택 테 넌 트 Azure 리소스 관리자 끝점 관리로 변경 해야 합니다. <region>. mycloud.com 합니다. 사용자의 정보를 확인 한 후 클릭 **다음**합니다.

    ![앱 서비스 설치 관리자][3]

7. 다음 페이지:
    1. 클릭는 **연결** 단추 옆에 **Azure 스택 구독** 상자입니다.
        - 관리자 계정을 제공 하십시오. 예: cloudadmin@azurestack.local 암호를 입력 하 고 클릭 **로그인**합니다.
    2. 에 **Azure 스택 구독** 상자는 **공급자 구독 기본**합니다.
    
    > [!NOTE]
    > 앱 서비스에만 배포할 수는 **공급자 구독 기본** 이 이번에 있습니다.  Azure 스택 1804에 도입 된 새 계량 구독으로 배포 응용 프로그램 서비스는 향후 업데이트에서 및 기존의 모든 배포를 새 구독에도 마이그레이션할 수 됩니다.
    >
    >
    
    3. 에 **Azure 스택 위치** 상자에 배포 하는 영역에 해당 하는 위치를 선택 합니다. 예를 들어 선택 **로컬** 경우 Azure 스택 개발 키트를 배포 합니다.
    4. **다음**을 클릭합니다.

    ![앱 서비스 설치 관리자][4]

8. 이제는 단계를 통해 구성 된 대로 기존 가상 네트워크에 배포 하는 옵션이 제공 [여기](azure-stack-app-service-before-you-get-started.md#virtual-network), 또는 가상 네트워크 및 연결 된 서브넷을 만들려면 앱 서비스 설치 관리자를 허용 합니다.
    1. 선택 **기본 설정으로 VNet 만들기**, 기본값을 적용 하 고 클릭 **다음**, 또는;
    2. 선택 **기존 VNet 및 서브넷을 사용 하 여**합니다.
        1. 선택 된 **리소스 그룹** ; 가상 네트워크가 포함 된
        2. 올바른 선택 **가상 네트워크** ;에 배포 하려는 이름
        3. 올바른 **서브넷** 각 필요한 역할 서브넷;에 대 한 값
        4. **다음**을 누릅니다

    ![앱 서비스 설치 관리자][5]

9. 파일 공유에 대 한 정보를 입력 한 다음 클릭 **다음**합니다. 파일 서버의 IP 주소나 정규화 된 도메인 이름, 파일 공유 주소 사용 해야 합니다. 예를 들어 \\\appservicefileserver.local.cloudapp.azurestack.external\websites, 또는 \\\10.0.0.1\websites

    > [!NOTE]
    > 설치 프로그램을 계속 진행 하기 전에 파일 공유에 대 한 연결을 테스트 하려고 합니다.  그러나 기존 가상 네트워크에 배포 하려는 경우 설치 관리자 파일 공유에 연결할 수 수 있으며 계속 묻는 경고를 표시 합니다.  파일 공유 정보를 확인 하 고 올바른지를 계속 합니다.
    >
    >

   ![앱 서비스 설치 관리자][8]

10. 다음 페이지:
    1. 에 **Id 응용 프로그램 ID** 상자 (에서 Azure AD) id에 사용할 응용 프로그램에 대 한 GUID를 입력 합니다.
    2. 에 **Identity 응용 프로그램 인증서 파일** 상자 입력 (하거나로 이동) 인증서 파일의 위치입니다.
    3. 에 **Identity 응용 프로그램 인증서 암호** 상자에 인증서에 대 한 암호를 입력 합니다. 이 암호는 기록한는 인증서를 만드는 스크립트를 사용 하는 경우입니다.
    4. 에 **루트 인증서 파일을 Azure 리소스 관리자** 상자 입력 (하거나로 이동) 인증서 파일의 위치입니다.
    5. **다음**을 클릭합니다.

    ![앱 서비스 설치 관리자][10]

11. 세 개의 각에 대 한 파일 상자 인증서, **찾아보기** 적절 한 인증서 파일을 이동 합니다. 각 인증서에 대 한 암호를 제공 해야 합니다. 이 인증서는에서 만든 것과 [만들기 필요한 인증서 단계](azure-stack-app-service-before-you-get-started.md#get-certificates)합니다. 클릭 **다음** 모든 정보를 입력 한 후 합니다.

    | Box | 인증서 파일 이름 예 |
    | --- | --- |
    | **기본 SSL 인증서 파일을 앱 서비스** | \_.appservice.local.AzureStack.external.pfx |
    | **앱 서비스 API SSL 인증서 파일** | api.appservice.local.AzureStack.external.pfx |
    | **응용 프로그램 서비스 게시자 SSL 인증서 파일** | ftp.appservice.local.AzureStack.external.pfx |

    인증서 파일 이름을 사용 하지 않는 인증서를 만들 때 다른 도메인 접미사를 사용한 경우 *로컬입니다. AzureStack.external*합니다. 대신, 사용자 지정 도메인 정보를 사용 합니다.

    ![앱 서비스 설치 관리자][11]

12. 앱 서비스 리소스 공급자 데이터베이스를 호스트 한 다음 클릭 사용 되는 서버 인스턴스에 대 한 SQL Server 세부 정보를 입력 **다음**합니다. 설치 관리자는 SQL 연결 속성의 유효성을 검사 합니다. 하면 **해야** 내부 ip 주소 또는 SQL Server 이름에 대 한 정규화 된 도메인 이름을 입력 합니다.

    > [!NOTE]
    > 설치 프로그램을 계속 진행 하기 전에 SQl Server에 대 한 연결을 테스트 하려고 합니다.  그러나 기존 가상 네트워크에 배포 하려는 경우 설치 관리자는 SQL Server에 연결할 수 수 있으며 계속 묻는 경고가 표시 됩니다.  SQL Server 정보를 확인 하 고 올바른지를 계속 합니다.
    >
    >
   
   ![앱 서비스 설치 관리자][12]

13. 역할 인스턴스 및 SKU 옵션을 검토 합니다. 기본값은 인스턴스와 ASDK 배포의 각 역할에 대 한 최소 SKU의 최소 수도 채워집니다. 배포를 계획 하는 데 도움이 vCPU 및 메모리 요구 사항에 대 한 요약 제공 됩니다. 선택 항목을 변경한 후 클릭 **다음**합니다.

     > [!NOTE]
     > 프로덕션 배포에 대 한의 지침에 따라 [용량 Azure 스택에서 Azure 앱 서비스 서버 역할에 대 한 계획](azure-stack-app-service-capacity-planning.md)합니다.
     >
     >

    | 역할 | 최소 인스턴스 | 최소 SKU | 메모 |
    | --- | --- | --- | --- |
    | Controller | 1 | -Standard_A1 (1 vCPU, 1792 MB) | 클라우드 앱 서비스의 상태를 유지 관리 및 관리 합니다. |
    | 관리 | 1 | -Standard_A2 (2 개의 Vcpu, 3584 MB) | 앱 서비스 Azure 리소스 관리자 및 API 끝점, 포털 확장 (관리, 테 넌 트, 함수 포털) 및 데이터 서비스를 관리합니다. 장애 조치를 지원 하기 위해 권장 되는 인스턴스 2로 증가 합니다. |
    | 게시자 | 1 | -Standard_A1 (1 vCPU, 1792 MB) | FTP 및 웹 배포를 통해 콘텐츠를 게시합니다. |
    | FrontEnd | 1 | -Standard_A1 (1 vCPU, 1792 MB) | 앱 서비스 응용 프로그램에 요청을 라우팅합니다. |
    | 공유 작업자 | 1 | -Standard_A1 (1 vCPU, 1792 MB) | 호스트 웹 또는 응용 프로그램 API 및 Azure 함수 응용 프로그램. 더 많은 인스턴스를 추가할 수 있습니다. 운영자 제품을 정의 하 고 모든 SKU 계층을 선택 수 있습니다. 계층에 최소 하나의 vCPU 있어야 합니다. |

    ![앱 서비스 설치 관리자][14]

    > [!NOTE]
    > **Windows Server 2016 Core Azure 스택 앱 서비스를 Azure와 사용 하기 위해 지원 되는 플랫폼 이미지가 아닙니다.  프로덕션 배포에 대 한 평가 이미지를 사용 하지 마십시오.**

14. 에 **플랫폼 이미지 선택** 상자에서 응용 프로그램 서비스 집합에 대 한 계산 리소스 공급자에서 사용할 수 있는 Windows Server 2016 배포 가상 컴퓨터 이미지를 선택 합니다. **다음**을 클릭합니다.

15. 다음 페이지:
     1. 작업자 역할 가상 컴퓨터 관리자 사용자 이름 및 암호를 입력 합니다.
     2. 다른 역할 가상 컴퓨터 관리자 사용자 이름 및 암호를 입력 합니다.
     3. **다음**을 클릭합니다.

    ![앱 서비스 설치 관리자][16]

16. 요약 페이지:
    1. 선택 사항을 확인 합니다. 변경 하려면 사용 된 **이전** 이전 페이지를 방문 하는 단추입니다.
    2. 구성이 올바른 경우 확인란을 선택 합니다.
    3. 배포를 시작 하려면 **다음**합니다.

    ![앱 서비스 설치 관리자][17]

17. 다음 페이지:
    1. 설치 진행률을 추적 합니다. Azure 스택 앱 서비스 60 분 걸립니다 배포 하려면 기본 선택 항목에 따라.
    2. 설치 관리자 성공적으로 완료 되 면 클릭 **종료**합니다.

    ![앱 서비스 설치 관리자][18]

## <a name="validate-the-app-service-on-azure-stack-installation"></a>Azure 스택 설치에는 앱 서비스의 유효성을 검사합니다

1. Azure 스택 관리자 포털에서로 이동 **앱 서비스 관리-** 합니다.

2. 상태에서 개요를 볼 수 있듯이 확인는 **상태** 표시 **준비가 된 모든 역할**합니다.

    ![앱 서비스 관리](media/azure-stack-app-service-deploy/image12.png)
    
> [!NOTE]
> 기존 가상 네트워크 및 conenct 프로그램 파일 서버를 위해 내부 IP 주소에 배포 하려는 경우 추가 해야는 아웃 바운드 보안 규칙 작업자 서브넷 사이 파일 서버가 SMB 트래픽을 사용 하도록 설정 합니다.  이 작업을 수행 하려면 관리 포털에서 WorkersNsg으로 이동한 다음 속성으로 아웃 바운드 보안 규칙 추가:
> * 원본: 모든
> * 원본 포트 범위: *
> * 대상: IP 주소
> * 대상 IP 주소 범위: Ip 범위에 파일 서버에 대 한
> * 대상 포트 범위: 445
> * 프로토콜: TCP
> * 동작: 허용
> * 우선 순위: 700
> * 이름: Outbound_Allow_SMB445
>

## <a name="test-drive-app-service-on-azure-stack"></a>Azure 스택 앱 서비스 드라이브를 테스트

배포 하는 응용 프로그램 서비스 리소스 공급자를 등록 한 후에 웹 앱 및 API 앱 사용자가 배포할 수 있는지 테스트 합니다.

> [!NOTE]
> 계획 내에서 Microsoft.Web 네임 스페이스를 가진 제안 만들기 해야 합니다. 그런 다음이 제품을 구독 하는 테 넌 트 구독이 필요 합니다. 자세한 내용은 참조 [만들기 제공](azure-stack-create-offer.md) 및 [계획 만들기](azure-stack-create-plan.md)합니다.
>
하면 *해야* 테 넌 트 구독에서 Azure 스택 앱 서비스를 사용 하는 응용 프로그램을 보유 합니다. 서비스 관리 관리자 포털 내에서 완료할 수 있는 유일한 기능은 응용 프로그램 서비스의 리소스 공급자 관리 관련 되어 있습니다. 이러한 기능에는 용량을 추가, 배포 원본 구성 및 Sku 작업자 계층 추가 포함 됩니다.
>
세 번째 기술 미리 보기를 기준으로 웹, API 및 Azure를 만들려는 앱, 함수 테 넌 트 구독 및 테 넌 트 포털을 사용 해야 합니다.

1. Azure 스택 테 넌 트 포털에서 클릭 **새로** > **웹 + 모바일** > **웹 앱**합니다.

2. 에 **웹 응용 프로그램** 블레이드에서에 이름 입력 된 **웹 응용 프로그램** 상자.

3. 아래 **리소스 그룹**, 클릭 **새로**합니다. 에 이름을 입력 된 **리소스 그룹** 상자입니다.

4. **App Service 계획/위치** > **새로 만들기**를 클릭합니다.

5. 에 **앱 서비스 계획** 블레이드에서에 이름 입력 된 **앱 서비스 계획** 상자입니다.

6. 클릭 **가격 책정 계층** > **무료 Shared** 또는 **공유 공유** > **선택**  >   **정상** > **만들**합니다.

7. 1 분에서 새 웹 앱에 대 한 타일은 대시보드에 나타납니다. 타일을 클릭 합니다.

8. 에 **웹 앱** 블레이드를 클릭 **찾아보기** 이 앱에 대 한 기본 웹 사이트를 볼 수 있습니다.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>(선택 사항) WordPress, DNN, 또는 Django 웹 사이트를 배포 합니다.

1. Azure 스택 테 넌 트 포털에서 클릭 **+**, Azure 마켓플레이스로 이동, Django 웹 사이트를 배포 하 고 성공적으로 완료 될 때까지 기다립니다. Django 웹 플랫폼은 파일 시스템 기반 데이터베이스를 사용 합니다. 메서드를 SQL 또는 MySQL 같은 모든 리소스 공급자 필요 하지 않습니다.

2. MySQL 리소스 공급자에도 배포 하는 경우에 시장에서 WordPress 웹 사이트를 배포할 수 있습니다. 데이터베이스 매개 변수에 대 한 메시지가 면 사용자 이름으로 입력 *User1@Server1*, 사용자 이름 및 선택한 서버 이름을 사용 합니다.

3. SQL Server 리소스 공급자에도 배포 하는 경우에 시장에서 DNN 웹 사이트를 배포할 수 있습니다. 데이터베이스 매개 변수에 대 한 메시지가 면 리소스 공급자에 연결 된 SQL Server를 실행 하는 컴퓨터에는 데이터베이스를 선택 합니다.

## <a name="next-steps"></a>다음 단계

또한을 수행 하려면 다른 [플랫폼 서비스 (PaaS) 서비스로](azure-stack-tools-paas-services.md)합니다.

- [SQL Server 리소스 공급자](azure-stack-sql-resource-provider-deploy.md)
- [MySQL 리소스 공급자](azure-stack-mysql-resource-provider-deploy.md)

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-create-package.png
[2]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-complete-offline.png
[3]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-arm-endpoints.png
[4]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-subscription-information.png
[5]: ./media/azure-stack-app-service-deploy-offline/app-service-default-VNET-config.png
[6]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config.png
[7]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config-with-values.png
[8]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration.png
[9]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration-error.png
[10]: ./media/azure-stack-app-service-deploy-offline/app-service-identity-app.png
[11]: ./media/azure-stack-app-service-deploy-offline/app-service-certificates.png
[12]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration.png
[13]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration-error.png
[14]: ./media/azure-stack-app-service-deploy-offline/app-service-cloud-quantities.png
[15]: ./media/azure-stack-app-service-deploy-offline/app-service-windows-image-selection.png
[16]: ./media/azure-stack-app-service-deploy-offline/app-service-role-credentials.png
[17]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-deployment-summary.png
[18]: ./media/azure-stack-app-service-deploy-offline/app-service-deployment-progress.png
