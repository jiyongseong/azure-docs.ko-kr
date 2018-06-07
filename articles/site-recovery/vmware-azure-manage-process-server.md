---
title: Azure Site Recovery에서 프로세스 서버 관리 | Microsoft Docs
description: 이 아티클에서는 Azure Site Recovery에서 VMware VM 및 실제 서버를 복제하기 위한 프로세스 서버 설정을 관리하는 방법에 대해 설명합니다.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
editor: ''
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 096b2890d41402448809ae87759fcd6b67bee2fe
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2018
---
# <a name="manage-process-servers"></a>프로세스 서버 관리

기본적으로 VMware VM 또는 실제 서버를 Azure에 복제할 때 사용되는 프로세스 서버는 온-프레미스 구성 서버 컴퓨터에 설치됩니다. 별도 프로세스 서버를 설정해야 하는 두 가지 인스턴스가 있습니다.

- 대규모 배포의 경우 용량 크기를 조정하기 위해 추가 온-프레미스 프로세스 서버가 필요할 수 있습니다.
- 장애 복구의 경우 Azure에서 임시 프로세스 서버 설정이 필요합니다. 장애 복구가 완료되면 이 VM을 삭제할 수 있습니다. 

이 아티클에서는 이러한 추가 프로세스 서버에 대한 일반적인 관리 작업을 요약합니다.

## <a name="upgrade-a-process-server"></a>프로세스 서버 업그레이드

다음과 같이 온-프레미스 또는 Azure에서 실행 하는 프로세스 서버를 업그레이드합니다(장애 복구 목적).

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

> [!NOTE]
  일반적으로 장애 복구를 목적으로 Azure 갤러리 이미지를 사용하여 Azure에서 프로세스 서버를 만들 때 사용 가능한 최신 버전을 실행합니다. Site Recovery 팀은 정기적으로 해결책 및 향상된 기능을 릴리스합니다. 따라서 프로세스 서버를 최신 상태로 유지하는 것이 좋습니다.



## <a name="reregister-a-process-server"></a>프로세스 서버 등록

온-프레미스에서 실행하는 프로세스 서버를 등록하거나 Azure의 구성 서버에서 등록해야 하는 경우 다음을 수행합니다.

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

설정을 저장한 후에 다음을 수행합니다.

1. 프로세스 서버에서 관리자 명령 프롬프트를 엽니다.
2. **%PROGRAMDATA%\ASR\Agent** 폴더로 이동하고 다음 명령을 실행합니다.

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>온-프레미스 프로세스 서버에 대한 프록시 설정 수정

프로세스 서버가 프록시를 사용하여 Azure의 Site Recovery에 연결할 때 기존 프록시 설정을 수정해야 하는 경우 이 절차를 사용합니다.

1. 프로세스 서버 컴퓨터에 로그온합니다. 
2. 관리자 PowerShell 명령 창을 열고 다음 명령을 실행합니다.
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```
2. **%PROGRAMDATA%\ASR\Agent** 폴더로 이동하고 다음 명령을 실행합니다.
  ```
  cmd
  cdpcli.exe --registermt

  net stop obengine

  net start obengine

  exit
  ```


## <a name="remove-a-process-server"></a>프로세스 서버 제거

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]


