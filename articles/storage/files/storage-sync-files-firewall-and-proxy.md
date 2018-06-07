---
title: Azure File Sync 온-프레미스 방화벽 및 프록시 설정 | Microsoft Docs
description: Azure File Sync 온-프레미스 네트워크 구성
services: storage
documentationcenter: ''
author: fauhse
manager: klaasl
editor: tamram
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: fauhse
ms.openlocfilehash: 979897e3cb703b36a46e96848a9176d6d4c6cc6a
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
ms.locfileid: "34072269"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Azure File Sync 프록시 및 방화벽 설정
Azure File Sync는 온-프레미스 서버를 Azure Files에 연결하여, 다중 사이트 동기화 및 클라우드 계층화 기능을 사용하도록 설정합니다. 따라서 온-프레미스 서버가 인터넷에 연결되어야 합니다. IT 관리자는 서버가 Azure 클라우드 서비스에 연결하는 최상의 경로를 결정해야 합니다.

이 문서에서는 서버를 Azure File Sync에 성공적이면서 안전하게 연결하기 위해 사용할 수 있는 특정 요구 사항 및 옵션에 대해 설명합니다.

> [!Important]
> Azure File Sync에서는 저장소 계정에 대한 방화벽 및 가상 네트워크를 아직 지원하지 않습니다. 

## <a name="overview"></a>개요
Azure File Sync는 Windows Server, Azure 파일 공유 및 일부 기타 Azure 서비스 간에 오케스트레이션 서비스로 작동하여, 동기화 그룹에 설명된 대로 데이터를 동기화합니다. Azure File Sync가 제대로 작동하려면 다음 Azure 서비스와 통신하도록 서버를 구성해야 합니다.

- Azure Storage
- Azure 파일 동기화
- Azure 리소스 관리자
- 인증 서비스

> [!Note]  
> Windows Server의 Azure File Sync 에이전트는 클라우드 서비스에 대한 모든 요청을 시작하므로, 방화벽 관점에서 아웃바운드 트래픽만 고려하면 됩니다. <br /> Azure File Sync 에이전트와 연결을 시작하는 Azure 서비스는 없습니다.


## <a name="ports"></a>포트
Azure File Sync는 파일 데이터 및 메타데이터를 HTTPS를 통해서만 이동하며 아웃바운드로 포트 443이 열려 있도록 요구합니다.
따라서 모든 트래픽이 암호화됩니다.

## <a name="networks-and-special-connections-to-azure"></a>네트워크 및 Azure에 대한 특수 연결
Azure File Sync 에이전트는 Azure에 대해 [ExpressRoute](../../expressroute/expressroute-introduction.md) 등과 같은 특수한 채널을 요구하지 않습니다.

Azure File Sync는 Azure로 연결될 수 있는 모든 방식에 작동하며, 대역폭, 대기 시간과 같은 다양한 네트워크 특성에 맞게 자동으로 조정되고 미세 조정을 위한 관리 제어 기능을 제공합니다. 현재는 일부 기능을 사용할 수 없습니다. 특정 동작을 구성하려는 경우 [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670)를 통해 알려주세요.

## <a name="proxy"></a>Proxy
Azure File Sync는 현재 머신 차원의 프록시 설정을 지원합니다. 전체 서버 트래픽이 이 프록시를 통해 라우팅되므로 이 프록시 설정은 Azure File Sync 에이전트에 투명합니다.

앱별 프록시 설정은 현재 개발 중이며, 향후 Azure File Sync 에이전트 릴리스에서 지원될 예정입니다. 따라서 Azure File Sync 트래픽에 적절하게 프록시를 구성할 수 있습니다.

## <a name="firewall"></a>방화벽
이전 섹션에서 설명한 것처럼 포트 443을 아웃바운드로 열어 두어야 합니다. 데이터 센터, 분기 또는 지역의 정책에 따라, 이 포트를 통한 트래픽을 특정 도메인으로 추가로 제한하는 것이 바람직하거나 필요할 수 있습니다.

다음 표에서는 통신에 필요한 도메인에 대해 설명합니다.

| 서비스 | 도메인 | 사용 현황 |
|---------|----------------|------------------------------|
| **Azure 리소스 관리자** | https://management.azure.com | 초기 서버 등록 호출을 포함하는 모든 사용자 호출(예: PowerShell)은 이 URL로 이동되거나 이 URL을 통해 이동됩니다. |
| **Azure Active Directory** | https://login.windows.net | Azure Resource Manager 호출은 인증된 사용자가 수행해야 합니다. 성공하기 위해 이 URL이 사용자 인증에 사용됩니다. |
| **Azure Active Directory** | https://graph.windows.net/ | Azure File Sync 배포의 일부로, 구독의 Azure Active Directory에 서비스 주체가 만들어집니다. 이 URL이 해당 작업에 사용됩니다. 이 보안 주체는 Azure File Sync 서비스에 대한 최소한의 권한 집합을 위임하는 데 사용됩니다. Azure File Sync의 초기 설정을 수행하는 사용자는 구독 소유자 권한이 있는 인증된 사용자여야 합니다. |
| **Azure Storage** | &ast;.core.windows.net | 서버는 파일을 다운로드할 때 저장소 계정의 Azure 파일 공유와 직접 소통하면서 데이터 이동을 보다 효율적으로 수행합니다. 서버에는 대상으로 지정된 파일 공유 액세스만 허용하는 SAS 키가 있습니다. |
| **Azure File Sync** | &ast;.one.microsoft.com | 초기 서버 등록 후 서버는 해당 지역에서 Azure File Sync 서비스 인스턴스에 대한 지역별 URL을 수신합니다. 서버는 이 URL을 사용하여 동기화를 처리하는 인스턴스와 직접 효율적으로 통신할 수 있습니다. |

> [!Important]
> &ast;.one.microsoft.com에 대한 트래픽을 허용할 때 서버에서 동기화 서비스 이외의 위치로 트래픽이 전달될 수 있습니다. 하위 도메인에서 더 많은 Microsoft 서비스를 사용할 수 있습니다.

&ast;.one.microsoft.com이 너무 광범위한 경우 Azure File Sync 인스턴스의 명시적인 지역별 인스턴스만 허용하여 서버 통신을 제한할 수 있습니다. 선택할 인스턴스는 서버를 배포 및 등록한 저장소 동기화 서비스의 지역에 따라 다릅니다. 이 지역은 바로 서버에 대해 허용해야 하는 지역입니다. 곧 새 비즈니스 연속성 기능을 위해 추가 URL이 지원될 예정입니다. 

| 지역 | Azure File Sync 지역별 끝점 URL |
|--------|---------------------------------------|
| 오스트레일리아 동부 | https://kailani-aue.one.microsoft.com |
| 캐나다 중부 | https://kailani-cac.one.microsoft.com |
| 미국 동부 | https://kailani1.one.microsoft.com |
| 동남아시아 | https://kailani10.one.microsoft.com |
| 영국 남부 | https://kailani-uks.one.microsoft.com |
| 서유럽 | https://kailani6.one.microsoft.com |
| 미국 서부 | https://kailani.one.microsoft.com |

> [!Important]
> 이러한 자세한 방화벽 규칙을 정의하는 경우 이 문서를 자주 확인하고 방화벽 규칙을 업데이트하여, 방화벽 설정에서 오래되었거나 불완전한 URL 목록으로 인한 서비스 중단을 방지하세요.

## <a name="summary-and-risk-limitation"></a>요약 및 위험 제한
이 문서의 앞부분에 나오는 목록에는 Azure File Sync가 현재 통신하는 URL이 포함되어 있습니다. 방화벽은 이러한 도메인의 응답 뿐만 아니라 아웃바운드 트래픽도 허용할 수 있어야 합니다. Microsoft는 이 목록을 업데이트 상태로 유지하려고 합니다.

방화벽 규칙을 제한하는 도메인을 설정하는 것도 보안을 강화하는 방법이 될 수 있습니다. 이러한 방화벽 구성이 사용될 경우 시간이 지남에 따라 URL이 추가되거나 변경될 수 있다는 점에 유의해야 합니다. 따라서 최신 에이전트의 테스트 배포에서 한 Azure File Sync 에이전트 버전에서 다른 버전으로 변경 관리 프로세스를 진행하면서 이 문서의 표를 확인하는 것도 유용한 방법입니다. 이러한 방식으로, 가장 최근 에이전트가 요구한 도메인에 대한 트래픽을 허용하도록 방화벽을 구성할 수 있습니다.

## <a name="next-steps"></a>다음 단계
- [Azure 파일 동기화 배포에 대한 계획](storage-sync-files-planning.md)
- [Azure File Sync(미리 보기) 배포](storage-sync-files-deployment-guide.md)
