---
title: Azure 스택에 대 한 서비스 사용자를 만들 | Microsoft Docs
description: Azure 리소스 관리자의 역할 기반 액세스 제어를 사용한 리소스에 대 한 액세스 관리를 사용할 수 있는 서비스 사용자를 만드는 방법을 설명 합니다.
services: azure-resource-manager
documentationcenter: na
author: mattbriggs
manager: femila
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2018
ms.author: mabrigg
ms.openlocfilehash: de5712fd7b48a759b366f5b9808bbbefc6e305cd
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="give-applications-access-to-azure-stack-resources-by-creating-service-principals"></a>서비스 사용자를 만들어 응용 프로그램 액세스 권한을 Azure 스택 리소스에 부여

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure 리소스 관리자를 사용 하 여 보안 주체는 서비스를 만들어 Azure 스택 리소스를 응용 프로그램 액세스를 제공할 수 있습니다. 서비스 사용자 수를 사용 하 여 특정 권한 위임 [역할 기반 액세스 제어](azure-stack-manage-permissions.md)합니다.

모범 사례로, 응용 프로그램에 대 한 서비스 사용자를 사용 해야 합니다. 서비스 보안 주체는 다음과 같은 이유로 사용자 자신의 자격 증명을 사용 하 여 응용 프로그램을 실행 하는 것이 좋습니다.

* 서비스 계정 사용 권한을 보다 다른 사용자에 권한을 할당할 수 있습니다. 일반적으로 서비스 사용자의 사용 권한을 정확히 어떤 응용 프로그램 작업을 수행 해야 제한 됩니다.
* 역할 또는 책임을 변경 하는 경우 응용 프로그램의 자격 증명을 변경할 필요가 없습니다.
* 무인 설치 스크립트를 실행할 때 인증을 자동화 하는 인증서를 사용할 수 있습니다.

## <a name="example-scenario"></a>예제 시나리오 

Azure 리소스 관리자를 사용 하 여 Azure 리소스를 인벤토리 하는 구성 관리 응용 프로그램을 해야 합니다. 서비스 보안 주체를 만들 수 있으며 읽기 역할에 할당할 수 없습니다. 이 역할은 Azure 리소스에 읽기 전용 액세스 권한을 앱을 제공 합니다.

## <a name="getting-started"></a>시작

이 문서의 단계를 사용 하 여 도움을 줄:

* 앱에 대 한 서비스 사용자를 만듭니다.
* 앱을 등록 하 고 인증 키를 만듭니다.
* 응용 프로그램 역할에 할당 합니다.

Azure 스택에 대 한 Active Directory를 구성 하는 방법은 서비스 사용자를 만드는 방법을 결정 합니다. 다음 옵션 중 하나를 선택 합니다.

* 서비스에 대 한 주 만들기 [Azure Active Directory (Azure AD)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad)합니다.
* 서비스에 대 한 주 만들기 [Active Directory Federation Services (AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs)합니다.

단계를 할당 하기 위한 서비스 사용자 역할에 동일한 azure AD와 AD FS 합니다. 서비스 사용자를 만든 후 다음을 할 수 있습니다 [권한을 위임할](azure-stack-create-service-principals.md#assign-role-to-service-principal) 역할에 할당 하 여 합니다.

## <a name="create-a-service-principal-for-azure-ad"></a>Azure AD에 대 한 서비스 사용자 만들기

Azure 스택 Azure AD id 저장소로을 사용 하는 서비스 Azure 포털을 사용 하 여 Azure와 같은 단계를 사용 하 여 주 만들 수 있습니다.

>[!NOTE]
확인을 해야 하는 [필요한 Azure AD 권한](../../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions) 서비스 사용자 만들기를 시작 하기 전에.

### <a name="create-service-principal"></a>서비스 주체 만들기

응용 프로그램에 대 한 서비스 사용자를 만들려면:

1. [Azure Portal](https://portal.azure.com)을 통해 Azure 계정에 로그인합니다.
2. 선택 **Azure Active Directory** > **앱 등록** > **추가**합니다.
3. 응용 프로그램에 대한 이름 및 URL을 제공합니다. 만들려는 응용 프로그램 유형으로 **웹앱/API** 또는 **네이티브**를 선택합니다. 값을 설정한 후 **만들기**를 선택합니다.

### <a name="get-credentials"></a>자격 증명 가져오기

프로그래밍 방식으로 로그인 할 때 응용 프로그램 및 인증 키에 대 한 ID를 사용 합니다. 이러한 값을 가져오려면 다음을 수행합니다.

1. Active Directory의 **앱 등록**에서 응용 프로그램을 선택합니다.

2. **응용 프로그램 ID**를 복사하고 응용 프로그램 코드에 저장합니다. 응용 프로그램에는 [샘플 응용 프로그램](#sample-applications) 사용 **클라이언트 id** 를 참조할 때는 **응용 프로그램 ID**합니다.

     ![응용 프로그램에 대 한 응용 프로그램 ID](./media/azure-stack-create-service-principal/image12.png)
3. 인증 키를 생성하려면 **키**를 선택합니다.

4. 키에 대한 설명 및 키의 기간을 제공합니다. 완료되면 **저장**을 선택합니다.

>[!IMPORTANT]
키의 키를 저장 한 후 **값** 표시 됩니다. 나중에 키를 검색할 수 없으므로이 값을 기록 합니다. 응용 프로그램에서 검색할 수 있는 위치에 키 값을 저장합니다.

![저장 된 키에 대 한 경고를 키 값입니다.](./media/azure-stack-create-service-principal/image15.png)

마지막 단계는 [응용 프로그램 역할을 할당](azure-stack-create-service-principals.md#assign-role-to-service-principal)합니다.

## <a name="create-service-principal-for-ad-fs"></a>AD FS에 대 한 서비스 사용자 만들기

AD FS를 사용 하 여 id 저장소로 Azure 스택을 배포한 경우에 다음 작업에 대 한 PowerShell을 사용할 수 있습니다.

* 서비스 사용자를 만듭니다.
* 서비스 사용자 역할에 할당 합니다.
* 서비스 보안 주체 id를 사용 하 여 로그인 합니다.

### <a name="before-you-begin"></a>시작하기 전에

[로컬 컴퓨터에 필요한 Azure 스택 도구를 다운로드 합니다.](azure-stack-powershell-download.md)

### <a name="import-the-identity-powershell-module"></a>Identity PowerShell 모듈을 가져옵니다

Azure 스택 도구에 대 한 다운로드 폴더로 이동한 다음 명령을 사용 하 여 Identity PowerShell 모듈을 가져옵니다.

```PowerShell
Import-Module .\Identity\AzureStack.Identity.psm1
```

Identity 모듈을 가져올 때이 오류 메시지가 표시 될 수 있습니다: "AzureStack.Connect.psm1 디지털 서명이 없습니다. 스크립트가 실행 되지 않습니다는 시스템에서 ".

이 문제를 해결 하려면 스크립트 실행을 허용 하도록 실행 정책을 구성 해야 합니다. 실행 정책을 설정 하려면 관리자 권한 PowerShell 세션에서에서 다음 명령을 실행 합니다.

```PowerShell
Set-ExecutionPolicy Unrestricted
```

### <a name="create-the-service-principal"></a>서비스 주체 만들기

업데이트를 확인 하 여 다음 명령을 실행 하 여 서비스 사용자를 만들 수 있습니다는 **DisplayName** 매개 변수:

```powershell
$servicePrincipal = New-AzSADGraphServicePrincipal `
 -DisplayName "<YourServicePrincipalName>" `
 -AdminCredential $(Get-Credential) `
 -AdfsMachineName "AZS-ADFS01" `
 -Verbose

```

### <a name="assign-a-role"></a>역할 할당

서비스 사용자를 만든 다음을 수행 해야 [역할에 할당](azure-stack-create-service-principals.md#assign-role-to-service-principal)합니다.

### <a name="sign-in-using-powershell"></a>PowerShell을 사용 하 여 로그인

로그인 하려면 Azure 스택 다음 명령을 실행 하 여 업데이트 하는 **EnvironmentName** 응용 프로그램의 이름으로 매개 변수:

```powershell
Add-AzureRmAccount -EnvironmentName "<AzureStackEnvironmentName>" `
 -ServicePrincipal `
 -CertificateThumbprint $servicePrincipal.Thumbprint `
 -ApplicationId $servicePrincipal.ApplicationId `
 -TenantId $directoryTenantId
```

## <a name="assign-the-service-principal-to-a-role"></a>서비스 사용자 역할에 할당

구독의 리소스에 액세스하려면 역할에 응용 프로그램을 할당해야 합니다. 응용 프로그램에 적합한 사용 권한을 나타내는 역할을 결정합니다. 사용 가능한 역할에 대해 알아보려면 [RBAC: 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 참조하세요.

>[!NOTE]
구독, 리소스 그룹 또는 리소스의 수준에서 역할의 범위를 설정할 수 있습니다. 권한은 하위 수준의 범위로 상속됩니다. 예를 들어, 리소스 그룹에 대 한 읽기 역할을 사용 하 여 앱 응용 프로그램의 리소스 그룹에 리소스를 읽을 수를 의미 합니다.

서비스 사용자 역할을 할당 하기 위한 지침으로 다음 단계를 사용 합니다.

1. Azure 스택 포털에서 응용 프로그램을 할당 하려는 범위 수준으로 이동 합니다. 예를 들어 구독 범위에서 역할을 할당하려면 **구독**을 선택합니다.

2. 응용 프로그램을 할당 하려면 구독을 선택 합니다. 이 예제에서는 구독이 Visual Studio Enterprise를입니다.

     ![할당에 대 한 Visual Studio Enterprise 구독 선택](./media/azure-stack-create-service-principal/image16.png)

3. 선택 **액세스 제어 (IAM)** 구독에 대 한 합니다.

     ![액세스 제어를 선택 합니다.](./media/azure-stack-create-service-principal/image17.png)

4. **추가**를 선택합니다.

5. 응용 프로그램에 할당할 역할을 선택합니다.

6. 응용 프로그램을 검색하고 선택합니다.

7. **확인**을 선택하여 역할 할당을 완료합니다. 해당 범위에 대 한 역할에 할당 된 사용자의 목록에서 응용 프로그램을 볼 수 있습니다.

서비스 사용자를 생성 하 고 역할에 할당 한 Azure 스택 리소스 응용 프로그램에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[사용자 권한 관리](azure-stack-manage-permissions.md)
