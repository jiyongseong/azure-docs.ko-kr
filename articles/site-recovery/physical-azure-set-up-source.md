---
title: 원본 환경 설정(Azure에 대한 물리적 서버) | Microsoft Docs
description: 이 문서에서는 온-프레미스 환경을 설정하여 Windows 또는 Linux를 실행 중인 물리적 서버를 Azure에 복제하기 시작하는 방법을 설명합니다.
services: site-recovery
documentationcenter: ''
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 96004a70547c4bfb3a1a3bfadecb1304e4910b52
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2018
ms.locfileid: "29811140"
---
# <a name="set-up-the-source-environment-physical-server-to-azure"></a>원본 환경 설정(Azure에 대한 물리적 서버)

이 문서에서는 온-프레미스 환경을 설정하여 Windows 또는 Linux를 실행 중인 물리적 서버를 Azure에 복제하기 시작하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

이 문서에서는 사용자가 다음 항목을 이미 가지고 있다고 가정합니다.
1. [Azure Portal](http://portal.azure.com "Azure Portal")의 Recovery Services 자격 증명 모음
3. 구성 서버를 설치할 물리적 컴퓨터

### <a name="configuration-server-minimum-requirements"></a>구성 서버 최소 요구 사항
다음 테이블에는 구성 서버에 대한 최소 하드웨어, 소프트웨어 및 네트워크 요구 사항이 나와 있습니다.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> HTTPS 기반 프록시 서버는 구성 서버에서 지원되지 않습니다.

## <a name="choose-your-protection-goals"></a>보호 목표 선택

1. Azure Portal에서 **Recovery Services** 자격 증명 모음 블레이드로 이동한 후 사용자 자격 증명 모음을 선택합니다.
2. 자격 증명 모음의 **리소스** 메뉴에서 **시작** > **Site Recovery** > **1단계: 인프라 준비** > **보호 목표**를 클릭합니다.

    ![목표 선택](./media/physical-azure-set-up-source/choose-goals.png)
3. **보호 목표**에서 **Azure에**를 선택하고 **가상화되지 않음/기타**를 선택한 후 **확인**을 클릭합니다.

    ![목표 선택](./media/physical-azure-set-up-source/physical-protection-goal.png)

## <a name="set-up-the-source-environment"></a>원본 환경 설정

1. **원본 준비**에서 구성 서버가 없는 경우 **+구성 서버**를 클릭하여 하나를 추가합니다.

  ![원본 설정](./media/physical-azure-set-up-source/plus-config-srv.png)
2. **서버 추가** 블레이드에서 **구성 서버**가 **서버 형식**에 표시되는지 확인합니다.
4. Site Recovery 통합 설치 프로그램 설치 파일을 다운로드합니다.
5. 자격 증명 모음 등록 키를 다운로드합니다. 통합 설치 프로그램을 실행하는 경우 등록 키가 필요합니다. 이 키는 생성된 날로부터 5일간 유효합니다.

    ![원본 설정](./media/physical-azure-set-up-source/set-source2.png)
6. 구성 서버로 사용 중인 컴퓨터에서 **Azure Site Recovery 통합 설치 프로그램**을 실행하여 구성 서버, 프로세스 서버 및 마스터 대상 서버를 설치합니다.

#### <a name="run-azure-site-recovery-unified-setup"></a>Azure Site Recovery 통합 설치 프로그램 실행

> [!TIP]
> 사용자 컴퓨터의 시스템 클록에서 시간이 현지 시간보다 5분 이상 차이가 날 경우 구성 서버 등록에 실패합니다. 설치를 시작하기 전에 시스템 클록을 [시간 서버](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)와 동기화합니다.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> 명령줄을 통해 구성 서버를 설치할 수 있습니다. 자세한 내용은 [명령줄 도구를 사용하여 구성 서버 설치](http://aka.ms/installconfigsrv)를 참조하세요.


## <a name="common-issues"></a>일반적인 문제

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>다음 단계

다음 단계는 Azure에서 [대상 환경 설정](physical-azure-set-up-target.md)을 포함합니다.
