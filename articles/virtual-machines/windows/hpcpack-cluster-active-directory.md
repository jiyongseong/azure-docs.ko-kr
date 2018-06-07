---
title: "Azure Active Directory와 HPC 팩 클러스터 | Microsoft Docs"
description: "Azure에서 Microsoft HPC Pack 2016 클러스터를 Azure Active Directory와 통합하는 방법 알아보기"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: jeconnoc
ms.assetid: 9edf9559-db02-438b-8268-a6cba7b5c8b7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 11/16/2017
ms.author: danlep
ms.openlocfilehash: bb0e878c4e987d111a535603cede25c639087ca7
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2017
---
# <a name="manage-an-hpc-pack-cluster-in-azure-using-azure-active-directory"></a>Azure Active Directory를 사용하여 Azure에서 HPC 팩 클러스터 관리
[Microsoft HPC 팩 2016](https://technet.microsoft.com/library/cc514029)은 Azure에서 HPC 팩 클러스터를 배포하는 관리자에 대해 Azure AD([Azure Active Directory](../../active-directory/index.md))와의 통합을 지원합니다.



다음의 고급 태스크에 대해 이 문서의 단계를 따릅니다. 
* 수동으로 HPC 팩 클러스터를 Azure AD 테넌트와 통합
* Azure의 HPC 팩 클러스터에서 작업 관리 및 예약 

HPC 팩 클러스터 솔루션을 Azure AD와 통합하려면 기타 응용 프로그램과 서비스를 통합하는 표준 단계를 따릅니다. 이 문서에서는 Azure AD의 기본 사용자 관리에 익숙하다고 가정합니다. 자세한 내용 및 백그라운드는 [Azure Active Directory 설명서](../../active-directory/index.md) 및 다음 섹션을 참조하세요.

## <a name="benefits-of-integration"></a>통합의 이점


Azure Active Directory(Azure AD)는 클라우드 솔루션에 대한 SSO(Single Sign-On) 액세스를 제공하는 Microsoft의 다중 테넌트 클라우드 기반 디렉터리 및 ID 관리 서비스입니다.

HPC 팩 클러스터를 Azure AD와 통합하면 다음 목표를 달성할 수 있습니다.

* HPC 팩 클러스터에서 기존 Active Directory 도메인 컨트롤러를 제거합니다. 배포 프로세스 속도를 높일 수 있고 비즈니스에 필요 없는 경우 클러스터의 유지 관리 비용을 줄일 수 있습니다.
* Azure AD에서 가져온 다음 혜택을 활용합니다.
    *   SSO(Single sign-on) 
    *   Azure에서 HPC 팩 클러스터에 대한 로컬 AD ID 사용 

    ![Azure Active Directory 환경](./media/hpcpack-cluster-active-directory/aad.png)


## <a name="prerequisites"></a>필수 조건
* **Azure 가상 컴퓨터에 배포된 HPC 팩 2016 클러스터** - 단계는 [Azure에서 HPC 팩 2016 클러스터 배포](hpcpack-2016-cluster.md)를 참조하세요. 이 문서의 단계를 완료하려면 헤드 노드의 DNS 이름 및 클러스터 관리자 자격 증명이 필요합니다.

  > [!NOTE]
  > Azure Active Directory 통합은 HPC 팩 2016 이전에 HPC 팩의 버전에서 지원되지 않습니다.



* **클라이언트 컴퓨터** - HPC 팩 클라이언트 유틸리티를 실행하는 Windows 또는 Windows Server 클라이언트 컴퓨터가 필요합니다. HPC 팩 웹 포털 또는 REST API를 사용하여 작업을 제출하려는 경우 사용자가 선택한 모든 클라이언트 컴퓨터를 사용할 수 있습니다.

* **HPC 팩 클라이언트 유틸리티** - Microsoft 다운로드 센터에서 사용할 수 있는 무료 설치 패키지를 사용하여 클라이언트 컴퓨터에서 HPC 팩 클라이언트 유틸리티를 설치합니다.


## <a name="step-1-register-the-hpc-cluster-server-with-your-azure-ad-tenant"></a>1단계: Azure AD 테넌트를 사용하여 HPC 클러스터 서버 등록
1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 사용자의 계정으로 둘 이상의 Azure AD 테넌트에 액세스할 수 있는 경우 오른쪽 위 모서리에서 계정을 클릭합니다. 그런 다음 포털 세션을 원하는 테넌트로 설정합니다. 디렉터리에 있는 리소스에 액세스할 수 있는 권한이 있어야 합니다. 
3. 왼쪽 서비스 탐색 창에서 **Azure Active Directory**를 클릭하고 **사용자 및 그룹**을 클릭하여 이미 생성되거나 구성된 사용자 계정이 있는지 확인합니다.
4. **Azure Active Directory**에서 **앱 등록** > **새 응용 프로그램 등록**을 클릭합니다. 다음 정보를 입력합니다.
    * **이름** - HPCPackClusterServer
    * **응용 프로그램 유형** - **웹앱/API**를 선택합니다.
    * **로그온 URL**- 샘플의 기준 URL로 기본적으로 `https://hpcserver`입니다.
    * **만들기**를 클릭합니다.
5. 앱이 추가된 후에 **앱 등록** 목록에서 선택합니다. 그런 다음 **설정** > **속성**을 클릭합니다. 다음 정보를 입력합니다.
    * **다중 테넌트**에 **예**를 클릭합니다.
    * **앱 ID URI**를 `https://<Directory_name>/<application_name>`로 변경합니다. `<Directory_name`>을 Azure AD 테넌트의 전체 이름(예: `hpclocal.onmicrosoft.com`)으로 바꾸고 `<application_name>`을 이전에 선택한 이름으로 바꿉니다.
6. **Save**를 클릭합니다. 저장이 완료되면 앱 페이지에서 **매니페스트**를 클릭합니다. `appRoles` 설정을 배치하고 다음 응용 프로그램 역할을 추가하여 매니페스트를 편집한 다음 **저장**을 클릭합니다.

  ```json
  "appRoles": [
     {
     "allowedMemberTypes": [
         "User",
         "Application"
     ],
     "displayName": "HpcAdminMirror",
     "id": "61e10148-16a8-432a-b86d-ef620c3e48ef",
     "isEnabled": true,
     "description": "HpcAdminMirror",
     "value": "HpcAdminMirror"
     },
     {
     "allowedMemberTypes": [
         "User",
         "Application"
     ],
     "description": "HpcUsers",
     "displayName": "HpcUsers",
     "id": "91e10148-16a8-432a-b86d-ef620c3e48ef",
     "isEnabled": true,
     "value": "HpcUsers"
     }
  ],
  ```
7. **Azure Active Directory**에서 **Enterprise 응용 프로그램** > **모든 응용 프로그램**을 클릭합니다. 목록에서 **HPCPackClusterServer**를 선택합니다.
8. **속성**을 선택하고 **사용자 할당 필요**를 **예**로 변경합니다. **Save**를 클릭합니다.
9. **사용자 및 그룹** > **사용자 추가**를 클릭합니다. 사용자를 선택하고 역할을 선택한 다음 **할당**을 클릭합니다. 사용자에게 사용 가능한 역할(HpcUsers 또는 HpcAdminMirror) 중 하나를 할당합니다. 디렉터리에 추가 사용자가 있으면 이 단계를 반복합니다. 클러스터 사용자에 대한 배경 정보는 [클러스터 사용자 관리](https://technet.microsoft.com/library/ff919335(v=ws.11).aspx)를 참조하세요.


## <a name="step-2-register-the-hpc-cluster-client-with-your-azure-ad-tenant"></a>2단계: Azure AD 테넌트를 사용하여 HPC 클러스터 클라이언트 등록

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 사용자의 계정으로 둘 이상의 Azure AD 테넌트에 액세스할 수 있는 경우 오른쪽 위 모서리에서 계정을 클릭합니다. 그런 다음 포털 세션을 원하는 테넌트로 설정합니다. 디렉터리에 있는 리소스에 액세스할 수 있는 권한이 있어야 합니다. 
3. **Azure Active Directory**에서 **앱 등록** > **새 응용 프로그램 등록**을 클릭합니다. 다음 정보를 입력합니다.

    * **이름** - HPCPackClusterClient    
    * **응용 프로그램 유형** - **네이티브**를 선택합니다.
    * **URI 리디렉션** - `http://hpcclient`
    * **만들기**

4. 앱이 추가된 후에 **앱 등록** 목록에서 선택합니다. **응용 프로그램 ID** 값을 복사하여 저장해 둡니다. 나중에 이 응용 프로그램을 구성하는 경우에 필요합니다.

5. **설정** > **필요한 권한** > **추가** > **API 선택**을 클릭합니다. 1단계에서 만든 HpcPackClusterServer 응용 프로그램을 검색하여 선택합니다.

6. **액세스 사용** 페이지에서 **HpcClusterServer 액세스**를 선택합니다. **완료**를 클릭합니다.


## <a name="step-3-configure-the-hpc-cluster"></a>3단계 - HPC 클러스터 구성

1. Azure에서 HPC 팩 2016 헤드 노드에 연결합니다.

2. HPC PowerShell을 시작합니다.

3. 다음 명령을 실행합니다.

    ```powershell

    Set-HpcClusterRegistry -SupportAAD true -AADInstance https://login.microsoftonline.com/ -AADAppName HpcPackClusterServer -AADTenant <your AAD tenant name> -AADClientAppId <client ID> -AADClientAppRedirectUri http://hpcclient
    ```
    여기서,

    * `AADTenant`은 `hpclocal.onmicrosoft.com`와 같은 Azure AD 테넌트 이름을 지정합니다.
    * `AADClientAppId`은 2단계에서 만든 앱의 응용 프로그램 ID를 지정합니다.

4. 헤드 노드 구성에 따라 다음 중 하나를 수행합니다.

    * 단일 헤드 노드 HPC Pack 클러스터에서 HpcScheduler 서비스를 다시 시작합니다.

    * 여러 헤드 노드가 있는 HPC Pack 클러스터의 헤드 노드에서 다음 PowerShell 명령을 실행하여 HpcSchedulerStateful 서비스를 다시 시작합니다.

    ```powershell
    Connect-ServiceFabricCluster

    Move-ServiceFabricPrimaryReplica –ServiceName "fabric:/HpcApplication/SchedulerStatefulService"

    ```

## <a name="step-4-manage-and-submit-jobs-from-the-client"></a>4 단계: 클라이언트에서 작업 관리 및 제출

컴퓨터에 HPC 팩 클라이언트 유틸리티를 설치하려면 Microsoft 다운로드 센터에서 HPC 팩 2016 설치 파일(전체 설치)을 다운로드합니다. 설치를 시작할 때 **HPC Pack 클라이언트 유틸리티**에 대한 설정 옵션을 선택합니다.

클라이언트 컴퓨터를 준비하려면 클라이언트 컴퓨터에 대한 [HPC 클러스터 설정](hpcpack-2016-cluster.md) 중에 사용되는 인증서를 설치합니다. 표준 Windows 인증서 관리 절차를 사용하여 **인증서 – 현재 사용자** > **신뢰할 수 있는 루트 인증 기관** 저장소에 공용 인증서를 설치합니다. 

이제 Azure AD 계정을 사용하여 클러스터 작업을 제출하고 관리하도록 HPC 팩 명령을 실행하거나 HPC 팩 작업 관리자 GUI를 사용할 수 있습니다. 작업 제출 옵션은 [Azure에서 HPC 팩 클러스터에 HPC 작업 제출](hpcpack-cluster-submit-jobs.md#step-3-run-test-jobs-on-the-cluster)을 참조하세요.

> [!NOTE]
> 처음으로 Azure의 HPC 팩 클러스터에 연결하려는 경우 팝업 창이 나타납니다. Azure AD 자격 증명을 입력하여 로그인합니다. 그러면 토큰이 캐시됩니다. 인증이 변경되거나 캐시가 지워지지 않는 한 나중에 Azure에서 클러스터에 연결하면 캐시된 토큰이 사용됩니다.
>
  
예를 들어 이전 단계를 완료한 후에 온-프레미스 클라이언트에서 작업을 다음과 같이 쿼리할 수 있습니다.

```powershell 
Get-HpcJob –State All –Scheduler https://<Azure load balancer DNS name> -Owner <Azure AD account>
```

## <a name="useful-cmdlets-for-job-submission-with-azure-ad-integration"></a>Azure AD 통합을 사용하는 작업 제출에 유용한 cmdlet 

### <a name="manage-the-local-token-cache"></a>로컬 토큰 캐시 관리

HPC Pack 2016은 다음과 같은 HPC PowerShell cmdlet을 제공하여 로컬 토큰 캐시를 관리합니다. 이러한 cmdlet은 비대화형으로 작업을 제출하는 데 유용합니다. 다음 예제를 참조하세요.

```powershell
Remove-HpcTokenCache

$SecurePassword = "<password>" | ConvertTo-SecureString -AsPlainText -Force

Set-HpcTokenCache -UserName <AADUsername> -Password $SecurePassword -scheduler https://<Azure load balancer DNS name> 
```

### <a name="set-the-credentials-for-submitting-jobs-using-the-azure-ad-account"></a>Azure AD 계정을 사용하여 작업을 제출하는 자격 증명 설정 

때때로 HPC 클러스터 사용자(도메인에 가입된 HPC 클러스터의 경우 도메인 사용자로 실행, 도메인에 가입되지 않은 HPC 클러스터의 경우 헤드 노드에서 로컬 사용자로 실행) 실행하고자 할 수 있습니다.

1. 다음 명령을 실행하여 자격 증명을 설정합니다.

    ```powershell
    $localUser = "<username>"

    $localUserPassword="<password>"

    $secpasswd = ConvertTo-SecureString $localUserPassword -AsPlainText -Force

    $mycreds = New-Object System.Management.Automation.PSCredential ($localUser, $secpasswd)

    Set-HpcJobCredential -Credential $mycreds -Scheduler https://<Azure load balancer DNS name>
    ```

2. 그리고 다음과 같이 작업을 제출합니다. 작업/태스크는 계산 노드의 $localUser 아래에서 실행됩니다.

    ```powershell
    $emptycreds = New-Object System.Management.Automation.PSCredential ($localUser, (new-object System.Security.SecureString))
    ...
    $job = New-HpcJob –Scheduler https://<Azure load balancer DNS name>

    Add-HpcTask -Job $job -CommandLine "ping localhost" -Scheduler https://<Azure load balancer DNS name>

    Submit-HpcJob -Job $job -Scheduler https://<Azure load balancer DNS name> -Credential $emptycreds
    ```
    
   `–Credential`이 `Submit-HpcJob`으로 지정되지 않으면 작업 또는 태스크는 Azure AD 계정으로 로컬 매핑된 사용자에서 실행됩니다. HPC 클러스터는 태스크를 실행하는 Azure AD 계정과 동일한 이름을 가진 로컬 사용자를 만듭니다.
    
3. Azure AD 계정에 대해 확장된 데이터를 설정합니다. Azure AD 계정을 사용하여 Linux 노드에서 MPI 작업을 실행하는 경우에 유용합니다.

   * Azure AD 계정 자체에 대해 확장된 데이터를 설정합니다.

      ```powershell
      Set-HpcJobCredential -Scheduler https://<Azure load balancer DNS name> -ExtendedData <data> -AadUser
      ```
      
   * 확장된 데이터를 설정하고 HPC 클러스터 사용자로 실행합니다.
   
      ```powershell
      Set-HpcJobCredential -Credential $mycreds -Scheduler https://<Azure load balancer DNS name> -ExtendedData <data>
      ```

