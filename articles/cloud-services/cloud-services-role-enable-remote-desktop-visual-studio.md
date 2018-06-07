---
title: Azure Cloud Services의 역할에 대해 원격 데스크톱 연결 사용
description: Azure 클라우드 서비스 응용 프로그램을 구성하여 원격 데스크톱 연결을 허용하는 방법입니다.
services: cloud-services
author: ghogen
manager: douge
ms.assetid: f5727ebe-9f57-4d7d-aff1-58761e8de8c1
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.topic: conceptual
ms.workload: azure
ms.date: 03/06/2018
ms.author: ghogen
ms.openlocfilehash: fe8b2b59616246743b38aa3b7a7972c092529b5d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-visual-studio"></a>Visual Studio를 사용하여 Azure Cloud Services에서 역할에 대한 원격 데스크톱 연결 사용

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

원격 데스크톱을 사용하면 Azure에서 실행 중인 역할의 데스크톱에 액세스할 수 있습니다. 원격 데스크톱 연결을 사용하여 응용 프로그램 실행 중에 응용 프로그램 문제를 진단하고 해결할 수 있습니다.

Visual Studio에서 클라우드 서비스에 제공하는 게시 마법사에는 사용자가 제공하는 자격 증명을 사용하여 게시 프로세스 중에 원격 데스크톱을 사용하도록 설정하는 옵션이 있습니다. Visual Studio 2017 버전 15.4 및 이전 버전을 사용하는 경우 이 옵션을 사용하는 것이 적합합니다.

그러나 Visual Studio 2017 버전 15.5 이상에서는 단일 개발자로만 작업하지 않는 한 게시 마법사를 통해 원격 데스크톱을 사용하지 않는 것이 좋습니다. 다른 개발자가 프로젝트를 열 수 있는 상황에서는 Azure Portal, PowerShell 또는 지속적인 배포 워크플로의 릴리스 정의를 통해 원격 데스크톱을 대신 사용할 수 있습니다. 이 권장 사항은 이 문서에서 설명한 대로 Visual Studio에서 클라우드 서비스 VM의 원격 데스크톱과 통신하는 방식이 변경 되었기 때문입니다.

## <a name="configure-remote-desktop-through-visual-studio-2017-version-154-and-earlier"></a>Visual Studio 2017 버전 15.4 및 이전 버전을 통해 원격 데스크톱 구성

Visual Studio 2017 버전 15.4 및 이전 버전을 사용하는 경우 게시 마법사에서 **모든 역할에 원격 데스크톱 사용** 옵션을 사용할 수 있습니다. Visual Studio 2017 버전 15.5 이상에서는 마법사를 계속 사용할 수 있지만 원격 데스크톱 옵션은 사용하지 않습니다.

1. Visual Studio의 [솔루션 탐색기]에서 클라우드 서비스 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택하여 게시 마법사를 시작합니다.

2. 필요한 경우 Azure 구독에 로그인하고 **다음**을 선택합니다.

3. **설정** 페이지에서 **모든 역할에 원격 데스크톱 사용**을 선택한 다음, **설정...** 링크를 선택하여 **원격 데스크톱 구성** 대화 상자를 엽니다.

4. 대화 상자의 아래쪽에서 **기타 옵션**을 선택합니다. 이 명령은 원격 데스크톱을 통해 연결할 때 자격 증명 정보를 암호화할 수 있도록 인증서를 만들거나 선택하는 드롭다운 목록을 표시합니다.

   > [!Note]
   > 원격 데스크톱 연결에 필요한 인증서는 다른 Azure 작업에 사용하는 인증서와 다릅니다. 원격 액세스 인증서에는 개인 키가 있어야 합니다.

5. 목록에서 인증서를 선택하거나 **&lt;만들기...&gt;** 를 선택합니다. 새 인증서를 만들 때 메시지가 표시되면 새 인증서에 대한 이름을 입력하고 **확인**을 선택합니다. 새 인증서가 드롭다운 목록 상자에 표시됩니다.

6. 사용자 이름과 암호를 제공합니다. 기존 계정을 사용할 수 없습니다. "관리자"는 새 계정에 대한 사용자 이름으로 사용하지 않습니다.

7. 계정이 만료되고 이후에 원격 데스크톱 연결이 차단될 날짜를 선택합니다.

8. 필요한 모든 정보를 제공한 후 **확인**을 클릭합니다. Visual Studio에서 선택한 인증서를 사용하여 암호화된 암호를 포함한 원격 데스크톱 설정을 프로젝트의 `.cscfg` 및 `.csdef` 파일에 추가합니다.

9. **다음** 단추를 사용하여 나머지 단계를 완료한 다음, 클라우드 서비스를 게시할 준비가 되면 **게시**를 선택합니다. 게시할 준비가 되지 않은 경우 변경 내용을 저장할지 묻는 메시지가 표시되면 **취소**를 선택하고 **예**로 대답합니다. 나중에 이러한 설정을 사용하여 클라우드 서비스를 게시할 수 있습니다.

## <a name="configure-remote-desktop-when-using-visual-studio-2017-version-155-and-later"></a>Visual Studio 2017 버전 15.5 이상을 사용하는 경우의 원격 데스크톱 구성

Visual Studio 2017 버전 15.5 이상에서는 클라우드 서비스 프로젝트를 통해 게시 마법사를 계속 사용할 수 있습니다. 또한 단일 개발자로만 작업하는 경우 **모든 역할에 원격 데스크톱 사용** 옵션을 사용할 수 있습니다.

팀의 구성원으로 작업하는 경우 [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md) 또는 [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)을 대신 사용하여 Azure 클라우드 서비스에서 원격 데스크톱을 사용하도록 설정해야 합니다.

이 권장 사항은 Visual Studio 2017 버전 15.5 이상에서 클라우드 서비스 VM과 통신하는 방식이 변경되었기 때문입니다. 게시 마법사를 통해 원격 데스크톱을 사용하도록 설정하면 이전 버전의 Visual Studio에서 "RDP 플러그 인"을 통해 VM과 통신합니다. Visual Studio 2017 버전 15.5 이상에서는 더 안전하고 유연한 "RDP 확장"을 대신 사용하여 통신합니다. 또한 이 변경은 원격 데스크톱을 사용하도록 설정하는 Azure Portal 및 PowerShell 메서드에서도 RDP 확장을 사용한다는 사실과 일치합니다.

Visual Studio에서 RDP 확장과 통신하는 경우 SSL을 통해 일반 텍스트 암호를 전송합니다. 그러나 프로젝트의 구성 파일에는 암호화된 암호만 저장되며, 이 암호는 원래 암호화하는데 사용된 로컬 인증서에서만 일반 텍스트로 해독할 수 있습니다.

매번 동일한 개발 컴퓨터에서 클라우드 서비스 프로젝트를 배포하면 해당 로컬 인증서를 사용할 수 있습니다. 이 경우에도 게시 마법사에서 **모든 역할에 원격 데스크톱 사용** 옵션을 계속 사용할 수 있습니다.

그러나 사용자 또는 다른 개발자가 다른 컴퓨터에서 클라우드 서비스 프로젝트를 배포하려는 경우 이 컴퓨터에는 암호를 해독하는 데 필요한 인증서가 없습니다. 이에 따라 다음과 같은 오류 메시지가 표시됩니다.

```output
Applying remote desktop protocol (RDP) extension.
Certificate with thumbprint [thumbprint] doesn't exist.
```

클라우드 서비스를 배포할 때마다 암호를 변경할 수 있지만, 이는 원격 데스크톱을 사용해야 하는 모든 사용자에게 불편한 작업입니다.

프로젝트를 팀과 공유하는 경우 게시 마법사에서 해당 옵션을 지우고, 대신 [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md) 또는 [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)을 통해 원격 데스크톱을 사용하도록 직접 설정하는 것이 가장 좋습니다.

### <a name="deploying-from-a-build-server-with-visual-studio-2017-version-155-and-later"></a>Visual Studio 2017 버전 15.5 이상을 사용하여 빌드 서버에서 배포

빌드 에이전트에 Visual Studio 2017 버전 15.5 이상이 설치된 빌드 서버(예: Visual Studio Team Services)에서 클라우드 서비스 프로젝트를 배포할 수 있습니다. 이렇게 배치하면 암호화 인증서를 사용할 수 있는 동일한 컴퓨터에서 배포가 수행됩니다.

Visual Studio Team Services에서 RDP 확장을 사용하려면 빌드 정의에 다음 세부 정보를 포함합니다.

1. MSBuild 인수에 `/p:ForceRDPExtensionOverPlugin=true`를 포함하여 배포가 RDP 플러그 인 대신 RDP 확장을 통해 작동하는지 확인합니다. 예: 

    ```
    msbuild AzureCloudService5.ccproj /t:Publish /p:TargetProfile=Cloud /p:DebugType=None
        /p:SkipInvalidConfigurations=true /p:ForceRDPExtensionOverPlugin=true
    ```

1. 빌드 단계가 완료되면 **Azure 클라우드 서비스 배포** 단계를 추가하고 해당 속성을 설정합니다.

1. 배포 단계가 완료되면 **Azure Powershell** 단계를 추가하고, **표시 이름** 속성을 "Azure 배포: RDP 확장 사용"(또는 다른 적절한 이름)으로 설정하고, 적절한 Azure 구독을 선택합니다.

1. **스크립트 유형**을 "인라인"으로 설정하고, 아래 코드를 **인라인 스크립트** 필드에 붙여넣습니다. (또한 이 스크립트를 사용하여 프로젝트에 `.ps1` 파일을 만들고, **스크립트 유형**을 "스크립트 파일 경로"로 설정하고, 이 파일을 가리키도록 **스크립트 경로**를 설정할 수도 있습니다.)

    ```ps
    Param(
        [Parameter(Mandatory=$True)]
        [string]$username,

        [Parameter(Mandatory=$True)]
        [string]$password,

        [Parameter(Mandatory=$True)]
        [string]$serviceName,

        [Datetime]$expiry = ($(Get-Date).AddYears(1))
    )

    Write-Host "Service Name: $serviceName"
    Write-Host "User Name: $username"
    Write-Host "Expiry: $expiry"

    $securepassword = ConvertTo-SecureString -String $password -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential $username,$securepassword

    # Try to remote existing RDP Extensions
    try
    {
        $existingRDPExtension = Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
        if ($existingRDPExtension -ne $null)
        {
            Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
        }
    }
    catch
    {
    }

    Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry -Verbose
    ```

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>원격 데스크톱을 사용하여 Azure 역할에 연결

Azure에 클라우드 서비스를 게시하고 원격 데스크톱을 사용하도록 설정한 후에는 Visual Studio 서버 탐색기를 사용하여 클라우드 서비스 VM에 로그인할 수 있습니다.

1. 서버 탐색기에서 **Azure** 노드를 확장한 다음 클라우드 서비스에 대한 노드 및 해당 역할 중 하나를 확장하여 인스턴스 목록을 표시합니다.

2. 인스턴스 노드를 마우스 오른쪽 단추로 클릭하고 **원격 데스크톱을 사용하여 연결**을 선택합니다.

3. 이전에 만든 사용자 이름 및 암호를 입력합니다. 이제 원격 세션에 로그인됩니다.

## <a name="additional-resources"></a>추가 리소스

[Cloud Services를 구성하는 방법](cloud-services-how-to-configure-portal.md)