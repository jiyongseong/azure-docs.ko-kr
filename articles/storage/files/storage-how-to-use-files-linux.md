---
title: "Linux에서 Azure Files 사용 | Microsoft Docs"
description: "Linux에서 SMB를 통해 Azure 파일 공유를 탑재하는 방법을 알아봅니다."
services: storage
documentationcenter: na
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 6edc37ce-698f-4d50-8fc1-591ad456175d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2017
ms.author: renash
ms.openlocfilehash: cca0d315a815faca5db07099b8e8e451ef55fad5
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2018
---
# <a name="use-azure-files-with-linux"></a>Linux에서 Azure Files 사용
[Azure Files](storage-files-introduction.md)는 사용하기 쉬운 Microsoft 클라우드 파일 시스템입니다. Azure 파일 공유는 [CIFS 커널 클라이언트](https://wiki.samba.org/index.php/LinuxCIFS)를 사용하여 Linux 배포판에 탑재할 수 있습니다. 이 문서에서는 Azure 파일 공유를 탑재하는 두 가지 방법을 보여 줍니다. 하나는 요청 시 `mount` 명령을 사용하여 탑재하고, 다른 하나는 `/etc/fstab`에 항목을 만들어 부팅 시 탑재하는 방법입니다.

> [!NOTE]  
> 온-프레미스 또는 다른 Azure 지역과 같이 호스트되는 Azure 지역 외부에 Azure 파일 공유를 탑재하려면 OS에서 SMB 3.0 암호화 기능을 지원해야 합니다.

## <a name="prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Linux 및 cifs-utils 패키지와 함께 Azure 파일 공유를 탑재하기 위한 필수 조건
* **설치된 cifs-utils 패키지를 사용할 수 있는 Linux 배포판을 선택합니다.**  
    다음 Linux 배포판을 Azure 갤러리에서 사용할 수 있습니다.

    * Ubuntu Server 14.04+
    * RHEL 7+
    * CentOS 7+
    * Debian 8+
    * openSUSE 13.2+
    * SUSE Linux Enterprise Server 12

* <a id="install-cifs-utils"></a>**cifs-utils 패키지가 설치됩니다.**  
    cifs-utils는 원하는 Linux 배포판의 패키지 관리자를 사용하여 설치할 수 있습니다. 

    **Ubuntu** 및 **Debian 기반** 배포판에서는 `apt-get` 패키지 관리자를 사용합니다.

    ```
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    **RHEL** 및 **CentOS**에서는 `yum` 패키지 관리자를 사용합니다.

    ```
    sudo yum install samba-client samba-common cifs-utils
    ```

    **openSUSE**에서는 `zypper` 패키지 관리자를 사용합니다.

    ```
    sudo zypper install samba*
    ```

    다른 배포판에서는 적절한 패키지 관리자를 사용하거나 [소스에서 컴파일합니다](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download).

* <a id="smb-client-reqs"></a>**SMB 클라이언트 요구 사항을 이해합니다.**  
    SMB 2.1 및 SMB 3.0을 통해 Azure Files를 탑재할 수 있습니다. 클라이언트 온-프레미스 또는 다른 Azure 지역의 연결에서 Azure Files는 SMB 2.1(또는 암호화되지 않은 SMB 3.0)을 거부합니다. 저장소 계정에 *보안 전송 필요*가 사용하도록 설정된 경우 Azure Files는 암호화된 SMB 3.0을 사용한 연결만 허용됩니다.
    
    SMB 3.0 암호화 지원은 Linux 커널 버전 4.11에서 도입되었으며 널리 사용되는 Linux 배포판의 이전 커널 버전에 백포트되었습니다. 이 문서를 게시하는 시점에 Azure 갤러리에서 다음 배포판이 이 기능을 지원합니다.

    - Ubuntu Server 16.04+
    - openSUSE 42.3+
    - SUSE Linux Enterprise Server 12 SP3+
    
    Linux 배포판이 여기 나열되지 않은 경우 다음 명령을 사용하여 Linux 커널 버전을 확인할 수 있습니다.

    ```
    uname -r
    ```

* **탑재된 공유의 디렉터리/파일 권한을 결정합니다**. 아래 예제에서는 모든 사용자에게 읽기, 쓰기 및 실행 권한을 부여하기 위해 권한 `0777`을 사용합니다. 다른 [chmod 권한](https://en.wikipedia.org/wiki/Chmod)으로 바꿀 수 있습니다. 

* **Storage 계정 이름**: Azure 파일 공유를 탑재하려면 Storage 계정의 이름이 필요합니다.

* **Storage 계정 키**: Azure 파일 공유를 탑재하려면 기본(또는 보조) 저장소 키가 필요합니다. SAS 키는 현재 탑재를 지원하지 않습니다.

* **445 포트가 열려 있는지 확인합니다**. SMB는 445 TCP 포트를 통해 통신합니다. 방화벽이 클라이언트 컴퓨터에서 445 TCP 포트를 차단하고 있지 않은지 확인합니다.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>요청 시 `mount`를 사용하여 Azure 파일 공유 탑재
1. **[Linux 배포판에 cifs-utils 패키지를 설치합니다](#install-cifs-utils)**.

2. **탑재 지점에 대한 폴더를 만듭니다.** 탑재 지점에 대한 폴더는 파일 시스템의 어디에나 만들 수 있지만 `/mnt` 폴더 아래 만드는 것이 일반적인 규칙입니다. 예: 

    ```
    mkdir /mnt/MyAzureFileShare
    ```

3. **mount 명령을 사용하여 Azure 파일 공유를 탑재합니다.** `<storage-account-name>`, `<share-name>`, `<smb-version>`, `<storage-account-key>` 및 `<mount-point>`를 사용자 환경에 대한 적절한 정보로 바꿉니다. Linux 배포판이 암호화된 SMB 3.0을 지원하는 경우(자세한 내용은 [SMB 클라이언트 요구 사항 이해](#smb-client-reqs) 참조) `<smb-version>`에 대해 `3.0`을 사용합니다. Linux 배포판이 암호화된 SMB 3.0을 지원하지 않는 경우 `<smb-version>`에 대해 `2.1`을 사용합니다. Azure 파일 공유는 SMB 3.0을 사용하여 Azure 지역 외에만 탑재할 수 있습니다(온-프레미스 또는 다른 Azure 지역 포함). 

    ```
    sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Azure 파일 공유를 사용하여 작업을 완료하면 `sudo umount <mount-point>`를 사용하여 공유를 탑재 해제할 수 있습니다.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>`/etc/fstab`을 사용하여 Azure 파일 공유에 대한 영구 탑재 지점 만들기
1. **[Linux 배포판에 cifs-utils 패키지를 설치합니다](#install-cifs-utils)**.

2. **탑재 지점에 대한 폴더를 만듭니다.** 탑재 지점에 대한 폴더는 파일 시스템의 어디에나 만들 수 있지만 `/mnt` 폴더 아래 만드는 것이 일반적인 규칙입니다. 이 폴더를 어디에 만들든 폴더의 절대 경로를 기록해 둡니다. 예를 들어 다음 명령은 `/mnt` 아래에 새 폴더를 만듭니다(경로는 절대 경로).

    ```
    sudo mkdir /mnt/MyAzureFileShare
    ```

3. **다음 명령을 사용하여 `/etc/fstab`에 다음 줄을 추가합니다.** `<storage-account-name>`, `<share-name>`, `<smb-version>` `<storage-account-key>` 및 `<mount-point>`를 사용자 환경에 대한 적절한 정보로 바꿉니다. Linux 배포판이 암호화된 SMB 3.0을 지원하는 경우(자세한 내용은 [SMB 클라이언트 요구 사항 이해](#smb-client-reqs) 참조) `<smb-version>`에 대해 `3.0`을 사용합니다. Linux 배포판이 암호화된 SMB 3.0을 지원하지 않는 경우 `<smb-version>`에 대해 `2.1`을 사용합니다. Azure 파일 공유는 SMB 3.0을 사용하여 Azure 지역 외에만 탑재할 수 있습니다(온-프레미스 또는 다른 Azure 지역 포함). 

    ```
    sudo bash -c 'echo "//<storage-account-name>.file.core.windows.net/<share-name> <mount-point> cifs nofail,vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'
    ```

> [!Note]  
> 다시 부팅하는 대신 `/etc/fstab`을 편집한 후에 `sudo mount -a`를 사용하여 Azure 파일 공유를 탑재할 수 있습니다.

## <a name="feedback"></a>사용자 의견
Linux 사용자 여러분의 의견을 듣고 싶습니다!

Linux 사용자 그룹용 Azure Files는 Linux에서 파일 저장소를 평가하고 채택할 때 피드백을 공유할 수 있도록 포럼을 제공합니다. 사용자 그룹에 참가하려면 [Azure Files Linux 사용자](mailto:azurefileslinuxusers@microsoft.com)에게 메일을 보내세요.

## <a name="next-steps"></a>다음 단계
Azure Files에 대한 자세한 내용은 다음 링크를 참조합니다.
* [Azure Files 소개](storage-files-introduction.md)
* [Azure 파일 배포에 대한 계획](storage-files-planning.md)
* [FAQ](../storage-files-faq.md)
* [문제 해결](storage-troubleshoot-linux-file-connection-problems.md)