---
title: "macOS에서 SMB를 통해 Azure 파일 공유 탑재 | Microsoft Docs"
description: "macOS를 사용하여 SMB를 통해 Azure 파일 공유를 탑재하는 방법을 알아봅니다."
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: renash
ms.openlocfilehash: 6e71a13f99160fdd310be1e9a59717c9fecbf35d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="mount-azure-file-share-over-smb-with-macos"></a>macOS에서 SMB를 통해 Azure 파일 공유 탑재
[Azure Files](storage-files-introduction.md)는 업계 표준을 사용하여 Azure에서 네트워크 파일 공유를 만들고 사용할 수 있게 해주는 Microsoft 서비스입니다. Azure 파일 공유는 macOS Sierra(10.12) 및 El Capitan(10.11)에서 탑재할 수 있습니다. 이 문서에서는 두 가지 방법, 즉 찾기(Finder) UI 및 터미널을 사용하여 macOS에 Azure 파일 공유를 탑재합니다.

> [!Note]  
> SMB를 통해 Azure 파일 공유를 탑재하기 전에 SMB 패킷 서명을 사용하지 않도록 설정하는 것이 좋습니다. 이렇게 하지 않으면 macOS에서 Azure 파일 공유에 액세스할 때 성능이 저하될 수 있습니다. SMB 연결이 암호화되므로 연결 보안에는 영향을 주지 않습니다. [SMB 패킷 서명 비활성화 대한 Apple 지원 문서](https://support.apple.com/HT205926)에서 설명한 대로 다음 명령은 터미널에서 SMB 패킷 서명을 사용하지 않도록 설정합니다.  
>    ```
>    sudo -s
>    echo "[default]" >> /etc/nsmb.conf
>    echo "signing_required=no" >> /etc/nsmb.conf
>    exit
>    ```

## <a name="prerequisites-for-mounting-an-azure-file-share-on-macos"></a>macOS에 Azure 파일 공유를 탑재하기 위한 필수 구성 요소
* **저장소 계정 이름**: Azure 파일 공유를 탑재하려면 저장소 계정의 이름이 필요합니다.

* **저장소 계정 키**: Azure File 공유를 탑재하려면 기본(또는 보조) 저장소 키가 필요합니다. SAS 키는 현재 탑재를 지원하지 않습니다.

* **445 포트가 열려 있는지 확인**: SMB는 445 TCP 포트를 통해 통신합니다. 클라이언트 컴퓨터(Mac)에서 방화벽이 445 TCP 포트를 차단하고 있지 않은지 확인합니다.

## <a name="mount-an-azure-file-share-via-finder"></a>찾기를 통해 Azure 파일 공유 탑재
1. **찾기를 엽니다**. 찾기는 기본적으로 macOS에서 열리지만 도킹 스테이션에서 "macOS 얼굴 아이콘"을 클릭하여 현재 선택된 응용 프로그램인지 확인할 수 있습니다.  
    ![macOS 얼굴 아이콘](./media/storage-how-to-use-files-mac/mount-via-finder-1.png)

2. **"이동" 메뉴에서 "서버에 연결"을 선택합니다**. [필수 구성 요소](#preq)의 UNC 경로를 사용하여 시작 이중 백슬래시(`\\`)는 `smb://`로 변환하고, 다른 모든 백슬래시(`\`)는 슬래시(`/`)로 변환합니다. 링크는 다음과 같습니다. !["서버에 연결" 대화 상자](./media/storage-how-to-use-files-mac/mount-via-finder-2.png)

3. **사용자 이름과 암호를 묻는 메시지가 표시되면 공유 이름과 저장소 계정 키를 사용합니다**. "서버에 연결" 대화 상자에서 "연결"을 클릭하면 사용자 이름과 암호를 입력하라는 메시지가 표시됩니다. (이 경우 macOS 사용자 이름이 자동으로 채워져 있습니다.) macOS 키 집합에 공유 이름/저장소 계정 키를 배치할 수 있는 옵션이 있습니다.

4. **Azure 파일 공유를 원하는 대로 사용합니다**. 공유 이름과 저장소 계정 키를 사용자 이름과 암호로 대체하면 해당 공유가 탑재됩니다. 일반적으로 파일 공유로 파일 끌어 놓기를 포함하여 로컬 폴더/파일 공유를 사용하는 것처럼 사용할 수 있습니다.

    ![탑재된 Azure 파일 공유의 스냅숏](./media/storage-how-to-use-files-mac/mount-via-finder-3.png)

## <a name="mount-an-azure-file-share-via-terminal"></a>터미널을 통해 Azure 파일 공유 탑재
1. `<storage-account-name>`을 저장소 계정 이름으로 바꿉니다. 메시지가 표시되면 저장소 계정 키를 암호로 제공합니다. 

    ```
    mount_smbfs //<storage-account-name>@<storage-account-name>.file.core.windows.net/<share-name> <desired-mount-point>
    ```

2. **원하는 대로 Azure 파일 공유를 사용합니다**. Azure 파일 공유는 이전 명령으로 지정된 탑재 지점에 탑재됩니다.  

    ![탑재된 Azure 파일 공유의 스냅숏](./media/storage-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>다음 단계
Azure Files에 대한 자세한 내용은 다음 링크를 참조합니다.

* [Apple 지원 문서 - Mac에서 파일 공유를 사용하여 연결하는 방법](https://support.apple.com/HT204445)
* [FAQ](../storage-files-faq.md)
* [Windows에서 문제 해결](storage-troubleshoot-windows-file-connection-problems.md)      
* [Linux에서 문제 해결](storage-troubleshoot-linux-file-connection-problems.md)    