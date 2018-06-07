---
title: Azure 스택 개발 키트 (ASDK) 호스트 컴퓨터를 준비 | Microsoft Docs
description: ASDK 설치에 대 한 Azure 스택 개발 키트 (ASDK) 호스트 컴퓨터를 준비 하는 방법에 설명 합니다.
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
ms.openlocfilehash: 5de25f574cb876701ffce74f1dca8c4bb9764157
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="prepare-the-asdk-host-computer"></a>ASDK 호스트 컴퓨터를 준비 합니다.
호스트 컴퓨터에는 ASDK를 설치할 수 있습니다, 전에 ASDK 환경 설치에 대 한 준비 되어야 합니다. 개발 키트 호스트 컴퓨터를 준비 하는 경우 ASDK 배포를 시작 하는 CloudBuilder.vhdx 가상 컴퓨터 하드 드라이브에서 부팅 됩니다.

## <a name="prepare-the-development-kit-host-computer"></a>개발 키트 호스트 컴퓨터를 준비 합니다.
호스트 컴퓨터에는 ASDK를 설치할 수 있습니다, 전에 ASDK 호스트 컴퓨터 환경이 준비 되어야 합니다.
1. 개발 키트 호스트 컴퓨터에 로컬 관리자로 로그인 합니다.
2. CloudBuilder.vhdx 파일 (C:\CloudBuilder.vhdx) C:\ 드라이브의 루트에 옮겨 졌음을 있는지 확인 합니다.
3. 개발 키트 설치 관리자 파일 (asdk installer.ps1)를 다운로드 하려면 다음 스크립트를 실행 하는 [Azure 스택 GitHub 도구 저장소](https://github.com/Azure/AzureStack-Tools) 에 **C:\AzureStack_Installer** 폴더에 사용자 개발 키트 호스트 컴퓨터의 경우:

  ```powershell
  # Variables
  $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
  $LocalPath = 'C:\AzureStack_Installer'
  # Create folder
  New-Item $LocalPath -Type directory
  # Enforce usage of TLSv1.2 to download from GitHub
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  # Download file
  Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
  ```

4. 관리자 권한 PowerShell 콘솔에서 시작 된 **C:\AzureStack_Installer\asdk-installer.ps1** 클릭 및 스크립트에 **환경 준비**합니다.

    ![](media/asdk-prepare-host/1.PNG) 

5. 에 **선택 Cloudbuilder vhdx** 페이지는 설치 관리자를 선택 합니다는 **cloudbuilder.vhdx** 파일을 다운로드 하 고에서 추출한 [이전 단계](asdk-download.md)합니다. 이 페이지에서는 또한 사용할 수 있습니다, 필요에 따라는 **드라이버 추가** 확인란 개발 키트 호스트 컴퓨터에 추가 드라이버를 추가 해야 합니다. **다음**을 클릭합니다.  

    ![](media/asdk-prepare-host/2.PNG)

6. 에 **설정 (옵션)** 페이지에서 로컬 관리자를 제공 합니다. 계정 개발 키트 호스트 컴퓨터에 대 한 정보 및 클릭 **다음**합니다. 또한 다음과 같은 선택적 설정에 대 한 값을 제공할 수 있습니다.
  - **Computername**:이 옵션은 개발 키트 호스트의 이름을 설정 합니다. 이름은은 FQDN 요구 사항을 준수 해야 하며 길이가 또는 15 자 여야 합니다. 기본값은 Windows에 의해 생성 된 임의의 컴퓨터 이름입니다.
  - **표준 시간대**: 개발 키트 호스트에 대 한 표준 시간대를 설정 합니다. 기본값은 (UTC-8:00) 태평양 표준시 (미국과 캐나다).
  - **고정 IP 구성을**: 고정 IP 주소를 사용 하도록 배포를 설정 합니다. 그렇지 않은 경우 설치 관리자는 cloudbuilder.vhx 다시 부팅, 네트워크 인터페이스 DHCP로 구성 됩니다.

    ![](media/asdk-prepare-host/3.PNG)

  > [!IMPORTANT]
  > 이 단계에서는 로컬 관리자 자격 증명을 제공 하지 않으면, 해야 직접 또는 호스트에 대 한 KVM 액세스 개발 키트는 설정의 일부분으로 컴퓨터를 다시 시작 합니다.

7. 이전 단계에서 고정 IP 구성을 선택한 경우 이제 해야 합니다.
    - 네트워크 어댑터를 선택 합니다. 클릭 하기 전에 어댑터에 연결할 수 있는지 확인 **다음**합니다.
    - 다음 사항을 확인는 **IP 주소**, **게이트웨이**, 및 **DNS** 값 올바른지, 그리고 클릭 **다음**합니다.
13. 클릭 **다음** 준비 프로세스를 시작 합니다.
14. 시점을 나타내는 준비 **Completed**, 클릭 **다음**합니다.
15. 클릭 **지금 다시 부팅** 는 cloudbuilder.vhdx를 개발 키트 호스트 컴퓨터를 부팅 하 고 [배포 프로세스를 계속](asdk-install.md)합니다.

    ![](media/asdk-prepare-host/4.PNG)


## <a name="next-steps"></a>다음 단계
[ASDK 설치](asdk-install.md)