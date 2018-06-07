---
title: Linux에서 Azure Files 문제 해결 | Microsoft Docs
description: Linux에서 Azure Files 문제 해결
services: storage
documentationcenter: ''
author: genlin
manager: willchen
editor: na
tags: storage
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 7b5567359e7ca87d26e05d336337b55af364031e
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="troubleshoot-azure-files-problems-in-linux"></a>Linux에서 Azure Files 문제 해결

이 문서에서는 Linux 클라이언트에서 연결할 때 Microsoft Azure Files와 관련하여 발생하는 일반적인 문제를 보여 줍니다. 또한 이러한 문제의 가능한 원인과 해결 방법을 제공합니다.

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>파일을 열려고 할 때 “[사용 권한 거부됨] 디스크 할당량이 초과됨”

Linux에서는 다음과 같은 오류 메시지가 수신됩니다.

**<filename> [사용 권한 거부됨] 디스크 할당량이 초과됨**

### <a name="cause"></a>원인

파일에 허용되는 동시 열린 핸들의 상한에 도달했습니다.

### <a name="solution"></a>해결 방법

일부 핸들을 닫아 동시 열린 핸들 수를 줄인 후 작업을 다시 시도하세요. 자세한 내용은 [Microsoft Azure Storage 성능 및 확장성 검사 목록](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)을 참조하세요.

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Linux에서 Azure Files와 서로 파일을 복사하는 속도 느림

-   최소 I/O 크기에 대한 특정 요구 사항이 없을 경우 최적 성능을 위해 I/O 크기로 1MB를 사용하는 것이 좋습니다.
-   쓰기를 사용하여 확장 중인 파일의 최종 크기를 알고 파일에 아직 기록되지 않은 꼬리에 0이 포함될 때 소프트웨어에 호환성 문제가 없다면 모든 쓰기를 확장 쓰기로 설정하는 대신 파일 크기를 미리 설정합니다.
-   copy 메서드를 다음과 같이 올바르게 사용합니다.
    -   두 파일 공유 간의 전송에는 [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)를 사용합니다.
    -   온-프레미스 컴퓨터와 파일 공유 간에는 [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/)를 사용합니다.

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>다시 연결 시간 제한으로 인한 "탑재 오류(112): 호스트가 중단됨"

Linux 클라이언트에서 클라이언트가 장시간 유휴 상태일 경우 "112" 탑재 오류가 발생합니다. 오랫동안 유휴 상태일 경우 클라이언트 연결이 끊어지고 연결 시간이 초과됩니다.  

### <a name="cause"></a>원인

다음과 같은 이유로 연결이 유휴 상태일 수 있습니다.

-   기본 “소프트” 탑재 옵션을 사용하는 경우 서버에 TCP 연결을 다시 설정하지 않는 네트워크 통신 오류입니다.
-   이전 커널에 존재하지 않는 최근 재연결 수정

### <a name="solution"></a>해결 방법

Linux 커널의 이러한 재연결 문제는 현재 다음 변경의 일부로 수정되었습니다.

- [소켓 재연결 후에 오랫동안 smb3 세션 재연결이 지연되지 않도록 재연결 수정](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93)
-   [소켓 재연결 직후 에코 서비스 호출](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7)
-   [CIFS: 재연결 동안 가능한 메모리 손상 수정](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)
-   [CIFS: 재연결 동안 가능한 뮤텍스 이중 잠금 해결(커널 v4.9 이상)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183)

그러나 이 변경 내용이 모든 Linux 배포판에 아직 이식되지 않은 것일 수 있습니다. 이 수정 및 기타 재연결 수정은 다음 널리 사용되는 Linux 커널 4.4.40, 4.8.16 및 4.9.1에 적용됩니다. 이러한 권장된 커널 버전 중 하나로 업그레이드하여 이 수정을 가져올 수 있습니다.

### <a name="workaround"></a>해결 방법

하드 탑재를 지정하여 이 문제를 해결할 수 있습니다. 클라이언트는 연결될 때까지 또는 명시적으로 중단될 때까지 대기하게 되어 네트워크 시간 제한으로 인해 오류가 발생하지 않도록 할 수 있습니다. 그러나 이 해결 방법은 무한 대기를 일으킬 수 있습니다. 필요에 따라 연결을 중지할 준비가 되어 있어야 합니다.

최신 커널 버전으로 업그레이드할 수 없는 경우 30초보다 짧은 간격으로 쓰는 Azure 파일 공유에 파일을 보관하여 이 문제를 해결할 수 있습니다. 이 작업은 만든 또는 수정된 날짜를 파일에 다시 쓰는 등의 쓰기 작업이어야 합니다. 그렇지 않으면 캐시된 결과를 얻을 수 있고 작업이 재연결을 트리거하지 않을 수 있습니다.

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>SMB 3.0을 사용하여 Azure Files를 탑재할 때 “Mount 오류(115): 작업이 진행되고 있습니다”

### <a name="cause"></a>원인

일부 Linux 배포에서는 SMB 3.0의 암호화 기능이 아직 지원되지 않으므로 사용자가 SMB 3.0을 사용하여 Azure Files를 탑재하려고 하면 기능 누락으로 인해 “115” 오류 메시지가 표시될 수 있습니다. 전체 암호화가 적용된 SMB 3.0은 현재 Ubuntu 16.04 이상을 사용할 때만 지원됩니다.

### <a name="solution"></a>해결 방법

Linux용 SMB 3.0에 대한 암호화 기능이 4.11 커널에 도입되었습니다. 이 기능을 사용하면 온-프레미스 또는 다른 Azure 지역에서 Azure 파일 공유를 탑재할 수 있습니다. 게시 시점에서 이 기능은 Ubuntu 17.04 및 Ubuntu 16.10으로 백포팅되었습니다. Linux SMB 클라이언트가 암호화를 지원하지 않을 경우 File Storage 계정과 같은 데이터 센터의 Azure Linux VM에서 SMB 2.1을 사용하여 Azure Files를 탑재합니다.

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Linux VM에 탑재된 Azure 파일 공유의 성능 저하

### <a name="cause"></a>원인

성능 저하의 한 가지 가능한 원인은 캐싱 비활성화입니다.

### <a name="solution"></a>해결 방법

캐싱의 비활성화 여부를 확인하려면 **cache=** 항목을 찾습니다. 

**cache=none**은 캐싱이 비활성화되었음을 나타냅니다.  기본 캐싱이나 “엄격한” 캐싱 모드를 활성화하는 명령을 탑재하기 위해 기본 탑재 명령을 사용하거나 명시적으로 **cache=strict** 옵션을 추가하여 공유를 다시 탑재하세요.

일부 시나리오에서는 **serverino** 탑재 옵션으로 **ls** 명령을 유도하여 모든 디렉터리 항목에 대해 stat를 실행할 수 있습니다. 이러한 동작은 큰 디렉터리를 나열하는 경우 성능 저하를 일으킬 수 있습니다. **/etc/fstab** 항목에서 탑재 옵션을 확인할 수 있습니다.

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

**sudo mount | grep cifs** 명령을 실행하고 다음 출력 예와 같은 해당 출력을 확인하여 올바른 옵션이 사용되는지 확인할 수도 있습니다.

`//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)`

**cache=strict** 또는 **serverino** 옵션이 없는 경우 [설명서](../storage-how-to-use-files-linux.md)의 mount 명령을 실행하여 Azure Files를 분리했다가 다시 탑재합니다. 그런 다음 **/etc/fstab** 항목에 올바른 옵션이 있는지 다시 확인합니다.

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>Windows에서 Linux로 파일을 복사할 때 타임 스탬프 손실됨

Linux/Unix 플랫폼에서 파일 1 및 파일 2의 소유자가 다른 경우 **cp-p** 명령이 실패합니다.

### <a name="cause"></a>원인

COPYFILE에서 force 플래그 **f**로 인해 Unix에서 **cp -p -f**가 실행됩니다. 이 명령은 또한 소유하지 않은 파일의 타임 스탬프를 유지하지 않습니다.

### <a name="workaround"></a>해결 방법

파일 복사를 위해 저장소 계정 사용자를 사용합니다.

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="cannot-connect-or-mount-an-azure-file-share"></a>Azure 파일 공유에 연결하거나 탑재할 수 없음

### <a name="cause"></a>원인

이 문제에 대한 일반적인 원인은 다음과 같습니다.


- 호환되지 않는 Linux 배포 클라이언트를 사용하고 있습니다. 다음 Linux 배포를 사용하여 Azure 파일 공유에 연결하는 것이 좋습니다.

    - Ubuntu Server 14.04+ 
    - RHEL 7+ 
    - CentOS 7+ 
    - Debian 8 
    - openSUSE 13.2+ 
    - SUSE Linux Enterprise Server 12

- CIFS 유틸리티는 클라이언트에 설치되지 않았습니다.
- 최소 SMB/CIFS 버전 2.1은 클라이언트에 설치되지 않았습니다.
- SMB 3.0 암호화는 클라이언트에서 지원되지 않습니다. SMB 3.0 암호화는 Ubuntu 16.4 이상 버전, SUSE 12.3 이상 버전에서 사용할 수 있습니다. 다른 배포에는 커널 4.11 이상 버전이 필요합니다.
- 지원되지 않는 TCP 포트 445를 통해 저장소 계정에 연결하려고 합니다.
- Azure VM에서 Azure 파일 공유에 연결하려고 하며 VM은 저장소 계정과 동일한 지역에 있지 않습니다.

### <a name="solution"></a>해결 방법

이 문제를 해결하려면 [Linux에서 Azure 파일 탑재 오류에 대한 문제 해결 도구](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089)를 사용합니다. 이 도구를 사용하여 클라이언트 실행 환경의 유효성을 검사하고, Azure 파일에 대한 액세스 오류를 발생시키는 호환되지 않는 클라이언트 구성을 검색할 수 있으며, 자체 수정에 대한 규범적인 지침을 제공하고, 진단 추적을 수집합니다.

## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls: '&lt;path&gt;'에 액세스할 수 없음: 입력/출력 오류

ls 명령을 사용하여 Azure 파일 공유의 파일을 나열하려고 시도하면 받은 파일을 나열할 때 ls 명령이 중지되고 다음과 같은 오류가 나타납니다.

**ls: '&lt;path&gt;'에 액세스할 수 없음: 입력/출력 오류**


### <a name="solution"></a>해결 방법
이 문제가 수정된 다음 버전으로 Linux 커널을 업그레이드합니다.

- 4.4.87+
- 4.9.48+
- 4.12.11+
- 4.13 이상 모든 버전

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.
