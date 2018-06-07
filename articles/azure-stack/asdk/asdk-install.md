---
title: Azure 스택 Development kit (ASDK) | Microsoft Docs
description: Azure 스택 개발 키트 (ASDK)를 설치 하는 방법에 설명 합니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 7b8fe61731a9412c61152bc58e55deebb611d011
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="install-the-azure-stack-development-kit-asdk"></a>Azure 스택 개발 키트 (ASDK) 설치
후 [ASDK 호스트 컴퓨터를 준비 하 고](asdk-prepare-host.md),이 문서의 다음 단계를 사용 하 여 CloudBuilder.vhdx 이미지에는 ASDK를 배포할 수 있습니다.

## <a name="install-the-asdk"></a>ASDK 설치
이 문서의 단계에서는 다운로드 및 실행 하 여 제공 하는 그래픽 사용자 인터페이스 (GUI)를 사용 하 여 ASDK를 배포 하는 방법을 보여 줍니다.는 **asdk installer.ps1** PowerShell 스크립트입니다.

> [!NOTE]
> Azure 스택 개발 키트에 대 한 설치 관리자 사용자 인터페이스는 WCF 및 PowerShell에 기반 하는 오픈 소스 스크립트입니다.


1. 관리자 자격 증명을 사용 하 여 로그인 때 지정 된 호스트 컴퓨터 성공적으로 CloudBuilder.vhdx 이미지로 부팅 한 후 있습니다 [개발 키트 호스트 컴퓨터를 준비](asdk-prepare-host.md) ASDK 설치에 대 한 합니다. 이 개발 키트 호스트의 로컬 관리자 자격 증명으로 동일 해야 합니다.
2. 관리자 권한 PowerShell 콘솔을 열고 실행는  **&lt;드라이브 문자 > \AzureStack_Installer\asdk-installer.ps1** 스크립트 (이 문서의 이제 다른 CloudBuilder.vhdx 이미지에는 C:\ 드라이브에). **Install**을 클릭합니다.

    ![](media/asdk-install/1.PNG) 

3. Id 공급자에서 **형식** 드롭다운 상자 **Azure 클라우드** 또는 **AD FS**합니다. 아래 **로컬 Administrator 암호** 에 (현재 구성 된 로컬 관리자 암호와 일치 해야 합니다)는 로컬 관리자 암호를 입력의 **암호** 상자를 선택한 다음 클릭  **다음**합니다.
    - **Azure 클라우드**: 구성 Azure Active Directory (Azure AD) id 공급자로 합니다. 이 옵션을 사용 하려면 인터넷에 연결 된 Azure AD의 전체 이름을 디렉터리 테 넌 트의 형태로 *domainname*. onmicrosoft.com 또는 Azure AD 사용자 지정 도메인 이름 및 지정 된 항목에 대 한 전역 관리자 자격 증명 확인 디렉터리입니다. 
    - **AD FS**: 기본 스탬프 디렉터리 서비스를 id 공급자로 사용 됩니다. 기본 계정은 로그인 하는 데 azurestackadmin@azurestack.local, 사용 하는 암호는 설정의 일부분으로 제공 된 것입니다.

    ![](media/asdk-install/2.PNG) 
    
    > [!NOTE]
    > 최상의 결과 id 공급자로 AD FS를 사용 하 여 연결이 끊긴된 Azure 스택 환경 사용 하려는 경우에 것이 좋습니다를 인터넷에 연결 되어 있는 동안 ASDK 설치. 이런 방식으로 배포 시 개발 키트 설치에 포함 된 Windows Server 2016 평가판을 활성화할 수 있습니다.
4. 네트워크 어댑터를 개발 키트를 사용 하 고 클릭 한 다음 선택 **다음**합니다.

    ![](media/asdk-install/3.PNG)

5. DHCP 또는 BGPNAT01 가상 컴퓨터에 대 한 정적 네트워크 구성을 선택 합니다.
    > [!TIP]
    > BGPNAT01 VM이 Azure 스택에 대 한 NAT 및 VPN 기능을 제공 하는 지 라우터 합니다.

    - **DHCP** (기본값): 가상 컴퓨터는 DHCP 서버에서 IP 네트워크 구성을 가져옵니다.
    - **정적**: DHCP는 인터넷에 액세스할 수 있는 Azure 스택에 대 한 유효한 IP 주소를 할당할 수 없습니다 하는 경우에이 옵션을 사용 합니다. **고정 IP 주소 (예를 들어 10.0.0.5/24) CIDR 형식의 서브넷 마스크 길이 함께 지정 해야**합니다.
    - 유효한 입력 **서버 IP를 시간** 주소입니다. 이 필드는 개발 키트에서 사용할 시간 서버를 설정 해야 합니다. 이 매개 변수는 유효한 시간 서버 IP 주소로 제공 되어야 합니다. 서버 이름은 지원 되지 않습니다.

      > [!TIP]
      > 시간 서버 IP 주소를 확인 하려면 다음을 방문 [pool.ntp.org](http:\\pool.ntp.org) 또는 time.windows.com ping 합니다. 

    - **필요에 따라**, 다음 값을 설정 합니다.
        - **VLAN ID**: VLAN ID를 설정 합니다 호스트와 AzS BGPNAT01 VLAN ID 및 액세스 하는 실제 네트워크 (인터넷)를 구성 해야 하는 경우에이 옵션을 사용 합니다. 
        - **DNS 전달자**: DNS 서버가 Azure 스택 배포의 일부로 생성 됩니다. 스탬프 외부 이름을 확인 하기 위해 솔루션 내에 있는 컴퓨터를 허용 하려면 기존 DNS 서버 인프라를 제공 합니다. 스탬프에 DNS 서버는이 서버에 알 수 없는 이름 확인 요청을 전달합니다.

    ![](media/asdk-install/4.PNG)

6. 에 **네트워크 인터페이스 카드 속성 확인** 페이지에서 진행률 표시줄이 표시 됩니다. 확인 완료 되 면 클릭 **다음**합니다.

    ![](media/asdk-install/5.PNG)

9. **요약** 페이지 **배포** 설치를 시작 하려면 ASDK 개발 키트 호스트 컴퓨터에 있습니다.

    ![](media/asdk-install/6.PNG)

    > [!TIP]
    > 여기 개발 키트를 설치 하는 데 사용 됩니다 하는 PowerShell 설치 명령이 복사할 수 있습니다. 해야 하는 경우에 유용 [ASDK PowerShell을 사용 하 여 호스트 컴퓨터에 다시 배포](asdk-deploy-powershell.md)합니다.

10. Azure AD 배포를 수행 하는 경우 설치 프로그램이 시작 된 후 몇 분이 Azure AD 전역 관리자 계정 자격 증명을 입력 하 라는 메시지가 표시 됩니다.

    ![](media/asdk-install/7.PNG)

11. 배포 프로세스는 몇 시간,이 기간 동안 호스트 컴퓨터는 자동으로 다시 부팅 한 번 걸립니다. 배포 진행률을 모니터링 하려는 경우 로그인 azurestack\AzureStackAdmin로 개발 키트 호스트를 다시 시작 합니다. 배포에 성공 하면 PowerShell 콘솔에 표시 됩니다: **완료: 작업 '배포'**합니다. 
    > [!IMPORTANT]
    > 로그인 되 면 로컬 관리자로 컴퓨터를 도메인에 가입 된 후에 배포 진행률을 볼 수 없습니다. 대신 실행 중인 유효성을 검사 하려면 azurestack\AzureStackAdmin로 로그인, 배포 다시 실행 하지 마십시오.

    ![](media/asdk-install/8.PNG)

축 하 합니다는 ASDK 성공적으로 설치한 다음!

배포가 실패 하는 몇 가지 이유로 하는 경우 다음을 할 수 있습니다 [재배포](asdk-redeploy.md) 처음부터 새로 만들거나 사용에서 다음 PowerShell 명령을, 동일한 관리자 권한 PowerShell 창에서 성공 하는 마지막 단계에서 배포를 다시 시작 하려면:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

## <a name="next-steps"></a>다음 단계
[사후 배포 구성](asdk-post-deploy.md)