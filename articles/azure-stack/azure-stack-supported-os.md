---
title: Azure 스택에 대 한 지원 되는 게스트 운영 체제 | Microsoft Docs
description: Azure 스택 이러한 게스트 운영 체제를 사용할 수 있습니다.
services: azure-stack
documentationcenter: ''
author: Brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: Brenduns
ms.reviewer: JeffGoldner
ms.openlocfilehash: d90ac726ae689cf299ee41d4e7ff4c17769e7455
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2018
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Azure 스택 지 원하는 게스트 운영 체제

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

## <a name="windows"></a>Windows

Azure 스택 다음 표에 나열 된 Windows 게스트 운영 체제를 지원 합니다.

| 운영 체제 | 설명 | Marketplace에서 사용할 수 |
| --- | --- | --- | --- | --- | --- |
| Windows Server, 버전 1709 | 64비트 | 컨테이너를 사용한 코어 |
| Windows Server 2016 | 64비트 |  Datacenter, 데이터 센터 코어, 컨테이너와 데이터 센터 |
| Windows Server 2012 R2 | 64비트 |  데이터 센터 |
| Windows Server 2012 | 64비트 |  데이터 센터 |
| Windows Server 2008 R2 SP1 | 64비트 |  데이터 센터 |
| Windows Server 2008 SP2 | 64비트 |  사용자 고유의 이미지 가져오기 |
| Windows 10 *(참고 1 참조)* | 64 비트, Pro 및 Enterprise | 사용자 고유의 이미지 가져오기 |

***참고 1:*** *Azure 스택에 Windows 10 클라이언트 운영 체제를 배포 하려면 있어야 [사용자 라이선스 Windows](https://www.microsoft.com/Licensing/product-licensing/windows10.aspx) 또는 정규화 된 다중 테 넌 트 호스팅 서비스 공급자를 통해 구매 ([QMTH](https://www.microsoft.com/CloudandHosting/licensing_sca.aspx)).*

마켓플레이스 이미지는 사용량 기준 과금-으로--사용 또는 라이선스 BYOL EA/SPLA ()에 사용할 수입니다. 단일 Azure 스택 인스턴스에서 모두 사용 하 여 지원 되지 않습니다. 배포 하는 동안 Azure 스택 이미지에 적합 한 버전의 게스트 에이전트를 삽입합니다.

 Datacenter edition;를 다운로드 하기 위한 시장에서 사용할 수 있습니다 고객이 다른 버전을 포함 하 여 자신의 서버 이미지를 가져올 수 있습니다. Windows 클라이언트 이미지 시장에서 사용할 수 없습니다.

## <a name="linux"></a>Linux

필요한 Windows Azure Linux 에이전트 (WALA) 포함 하는 Linux 배포판 시장에서 사용 가능한로 나열 됩니다. Azure 스택 사용자 고유의 이미지를 가져올 경우의 지침에 따라 [Azure 스택에 추가 Linux 이미지](azure-stack-linux.md)합니다.

> [!NOTE]
> 최신 공용 WALA 버전으로 사용자 지정 이미지를 구축 합니다. Azure 스택 2.2.18 보다 오래 된 버전 제대로 작동 하지 않을 수 있습니다.
>
> [클라우드 init](https://cloud-init.io/) Azure 스택에이 이번에 지원 되지 않습니다.

| 배포 | 설명 | 게시자 | Marketplace |
| --- | --- | --- | --- | --- | --- |
| CentOS 기반 6.9 | 64비트 | Rogue 웨이브 | 예 |
| 7.4 centOS 기반 | 64비트 | Rogue 웨이브 | 예 |
| ClearLinux | 64비트 | ClearLinux.org | 예 |
| 컨테이너 Linux |  64비트 | CoreOS | Stable |
| Debian 8 "Jessie" | 64비트 | credativ |  예 |
| Debian 9 "Stretch" | 64비트 | credativ | 예 |
| Red Hat Enterprise Linux 7.x | 64비트 | Red Hat |사용자 고유의 이미지 가져오기 |
| SLES 11SP4 | 64비트 | SUSE | 예 |
| SLES 12SP3 | 64비트 | SUSE | 예 |
| Ubuntu 14.04 LTS | 64비트 | Canonical | 예 |
| Ubuntu 16.04-LTS | 64비트 | Canonical | 예 |
| Ubuntu 18.04 LTS | 64비트 | Canonical | 예 |

다른 Linux 배포판을 나중에 지원할 수 있습니다.

Red Hat Enterprise Linux 지원 정보를 참조 하십시오 [Red Hat 및 Azure 스택: 질문과 대답](https://access.redhat.com/articles/3413531)합니다.
