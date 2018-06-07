---
title: Azure Site Recovery를 사용하여 VMware VM 및 실제 서버 장애 복구에 대해 Azure의 프로세스 서버 설정 | Microsoft Docs
description: 이 아티클에서는 Azure VM을 VMware에 장애 복구하도록 Azure에서 프로세스 서버를 설정하는 방법을 설명합니다.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 7bbe690e749680edde08facadf6d5910d7896f7e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="set-up-a-process-server-in-azure-for-failback"></a>장애 복구를 위해 Azure에서 프로세스 서버 설정

[Site Recovery](site-recovery-overview.md)를 사용하여 Azure에 대한 VMware VM 또는 실제 서버를 장애 복구한 후에 다시 실행되었을 때 온-프레미스 사이트로 다시 장애 조치할 수 있습니다. 장애 복구하기 위해 Azure에서 온-프레미스로 복제를 처리하도록 Azure에서 임시 프로세스 서버를 설정해야 합니다. 장애 복구가 완료되면 이 VM을 삭제할 수 있습니다.

## <a name="before-you-start"></a>시작하기 전에

[다시 보호](vmware-azure-reprotect.md) 및 [장애 복구](vmware-azure-failback.md) 프로세스에 대해 자세히 알아봅니다.

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]

## <a name="deploy-a-process-server-in-azure"></a>Azure에서 프로세스 서버 배포

1. 자격 증명 모음 > **Site Recovery 인프라**> **관리** > **구성 서버**에서 구성 서버를 선택합니다.
2. 서버 페이지에서 **+프로세스 서버**를 클릭합니다.
3. **프로세스 서버 추가** 페이지에서 Azure의 프로세스 서버를 배포하도록 선택합니다.
4. 장애 조치에 사용되는 구독, 리소스 그룹, 장애 조치에 사용되는 Azure 지역 및 Azure VM이 위치한 가상 네트워크를 비롯한 Azure 설정을 지정합니다. 여러 Azure 네트워크를 사용하는 경우 각각에 프로세스 서버가 필요합니다.
5. **서버 이름**, **사용자 이름** 및 **암호**에서 프로세스 서버의 이름과 서버에 관리자 권한이 할당된 자격 증명을 지정합니다.
6. 서버 VM 디스크에 사용할 저장소 계정, 프로세스 서버 VM이 위치한 서브넷 및 VM이 시작될 때 할당된 서버 IP 주소를 지정합니다.
7. **확인** 단추를 클릭하여 프로세스 서버 VM을 배포하기 시작합니다.

>

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>프로세스 서버(Azure에서 실행)를 구성 서버(온-프레미스에서 실행)에 등록

프로세스 서버 VM을 실행한 후에 다음과 같이 온-프레미스 구성 서버에 등록해야 합니다.

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]


