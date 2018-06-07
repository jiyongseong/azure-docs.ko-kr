---
title: Azure 스택 배포 네트워크 트래픽 | Microsoft Docs
description: 이 문서에서는 Azure 스택 배포 네트워킹 프로세스에 대 한 기대 하는 설명 합니다.
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
ms.date: 05/21/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: b808875e66e867b84e2971c6a5bd031d108d003b
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34656143"
---
# <a name="about-deployment-network-traffic"></a>배포 네트워크 트래픽에 대 한
Azure 스택 하는 동안 네트워크 트래픽 흐름 방식을 이해 배포는 배포 성공을 확인 하려면. 이 문서에서는 기대 하는 이해를 제공 하는 배포 프로세스 중 네트워크 트래픽이 예상된를 안내 합니다.

이 그림과 모든 구성 요소와 연결 배포 프로세스에 참여:

![Azure 스택 배포 네트워크 토폴로지](media/deployment-networking/figure1.png)

> [!NOTE]
> 이 문서에서는 연결 된 배포에 대 한 요구 사항 설명, 다른 배포 방법에 대 한 자세한 내용은 [Azure 스택 배포 연결 모델](azure-stack-connection-models.md)합니다.

### <a name="the-deployment-vm"></a>VM 배포
Azure 스택 솔루션 Azure 스택 구성 요소를 호스트 하는 데 사용 되는 서버 및 하드웨어 수명 주기 호스트 (HLH) 라고 하는 추가 서버 그룹을 포함 합니다. 이 서버 배포 하 고 솔루션의 수명 주기 관리 사용 되 고 배포 하는 동안 배포 VM (DVM)를 호스팅합니다.

## <a name="deployment-requirements"></a>배포 요구 사항
배포를 시작 하기 전에 배포를 성공적으로 완료 되도록 oem 업체를 통해 확인할 수 있는 몇 가지 최소 요구 사항이 있습니다. 이러한 요구 사항의 환경을 준비 하 고 유효성 검사는 성공 해야 하는 데 도움이 됩니다 이해은 다음과 같습니다.

-   [Certificates](azure-stack-pki-certs.md)
-   [Azure 구독](https://azure.microsoft.com/free/?b=17.06)
-   인터넷 액세스
-   DNS
-   NTP

> [!NOTE]
> 이 문서 마지막 세 요구 사항에 중점을 둡니다. 처음 두 개에 대 한 자세한 내용은 위의 링크를 참조 합니다.

## <a name="deployment-network-traffic"></a>배포 네트워크 트래픽
DVM BMC 네트워크에서 ip 구성 되어 있고 인터넷에 대 한 네트워크 액세스가 필요 합니다. BMC 네트워크 구성 요소 중 일부만 필요한 외부 수신 라우팅 서버 또는 인터넷에 대 한 액세스를이 네트워크에서 Ip를 사용 하 여 일부 OEM 관련 구성 요소 또한 해야 있습니다.

DVM를 배포 하는 동안 Azure Active Directory (Azure AD) 구독에서 Azure 계정에 대해 인증 합니다. 와 같은 작업에서 DVM 특정 포트 및 Url의 목록에 대 한 인터넷 액세스가 필요 합니다. 전체 목록을 찾을 수 있습니다는 [게시 끝점](azure-stack-integrate-endpoints.md) 설명서입니다. DVM는 내부 구성 요소에서 외부 Url에 대 한 DNS 요청을 전달 하도록 DNS 서버를 사용 합니다. 내부 DNS를 배포 하기 전에 OEM 제공 하는 DNS 전달자 주소에 대 한 이러한 요청을 전달 합니다. NTP 서버에도 마찬가지입니다, 그리고 신뢰할 수 있는 시간 서버는 모든 Azure 스택 구성 요소에 대 한 일관성 및 시간 동기화를 유지 하기 위해 필요 합니다.

배포 중의 DVM에 필요한 인터넷 액세스는 아웃 바운드, 인바운드 호출 없음은 배포 중에 만들어집니다. 원본으로 해당 IP를 사용 하 고 해당 Azure 스택 프록시 구성을 지원 하지 않고 염두에 둬야 합니다. 따라서 필요한 경우 투명 프록시 또는 NAT 인터넷에 액세스할 수를 제공 해야 합니다. 배포가 완료 된 후 Azure 및 Azure 스택 간의 모든 통신이 외부 네트워크를 통해 사용 하 여 공용 Vip 합니다.

Azure 스택 스위치에서 네트워크 구성 액세스 제어 목록 (Acl) 특정 네트워크 원본과 대상 간에 트래픽을 제한 하는 포함 되어 있습니다. DVM는; 무제한 액세스할 수 있는 유일한 구성 요소 HLH도 매우 제한 되어 있습니다. 네트워크에서 액세스 및 관리를 보다 쉽게 사용자 지정 옵션에 대 한 OEM를 요청할 수 있습니다. 이러한 Acl 때문에 배포 시에는 DNS 및 NTP 서버 주소가 변경 되지 않도록 해야 합니다. 이렇게 하면 모든 솔루션에 대 한 스위치를 다시 구성 해야 합니다.

배포가 완료 된 후 제공 된 DNS 및 NTP 서버 주소는 시스템의 구성 요소에서 직접 사용할 계속 됩니다. 예를 들어 배포를 완료 한 후 DNS 요청을 확인 하는 경우 소스 DVM IP에서 외부 네트워크 범위에서 주소를 변경 됩니다.

Azure 스택 성공적으로 배포 된 후 OEM 파트너 추가 배포 후 작업을 위한는 DVM를 사용할 수 있습니다. 그러나 모든 배포 작업 및 배포 후 구성 완료 되 면 OEM 제거 되 고는 DVM는 HLH에서 삭제 합니다.

## <a name="next-steps"></a>다음 단계
[Azure 등록 유효성 검사](azure-stack-validate-registration.md)
