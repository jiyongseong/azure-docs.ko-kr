---
title: "Azure 스택 개요에서 업데이트를 관리 | Microsoft Docs"
description: "Azure 스택 통합 시스템에 대 한 업데이트 관리에 알아봅니다."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 9b0781f4-2cd5-4619-a9b1-59182b4a6e43
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: 23b05909bda7785b45aeaeed0bd75a90de9ffe50
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/06/2018
---
# <a name="manage-updates-in-azure-stack-overview"></a>Azure 스택 개요에 대 한 업데이트 관리

*적용 대상: Azure 스택 시스템 통합*

Microsoft 일반 공급 시 시작 통합 Azure 스택 시스템에 속하는 일반적으로 매월, 네 번째 화요일에 일반 흐름에 대 한 업데이트 패키지를 해제 합니다. OEM 자신의 특정 알림 수 있도록 프로세스를 업데이트 알림 reach 조직에 대해 질문을 하거나 여기 Overview\Release Notes\Integrated 시스템에서 특정 버전에 대 한 자세한 내용은 릴리스 정보.

Microsoft 소프트웨어 업데이트의 각 릴리스에 단일 업데이트 패키지로 제공 됩니다. Azure 스택 연산자로 쉽게 가져올 수 있습니다, 설치 및 이러한 설치 진행률을 모니터링 관리자 포털에서 패키지를 업데이트 합니다. 

(Oem) 하드웨어 공급 업체는 또한 드라이버 및 펌웨어 업데이트 등의 업데이트를 릴리스 했습니다. 이러한 업데이트 OEM 하드웨어 공급 업체에서 별도 패키지로 전달 되 고 Microsoft 업데이트에서 별도로 관리 됩니다.

지원에는 시스템을 유지 하기 위해 Azure 스택 특정 버전 수준으로 업데이트를 유지 해야 합니다. 검토 하 고 있는지 확인은 [정책 서비스는 Azure 스택](azure-stack-servicing-policy.md)합니다.

> [!NOTE]
> Azure 스택 개발 키트를 Azure 스택 업데이트 패키지를 적용할 수 없습니다. 통합된 시스템에 대 한 업데이트 패키지를 설계 되었습니다.

## <a name="the-update-resource-provider"></a>리소스 공급자 업데이트

Azure 스택 Microsoft 소프트웨어 업데이트의 적용을 오케스트레이션 하는 업데이트 리소스 공급자가 포함 되어 있습니다. 이 리소스 공급자 업데이트 모든 물리적 호스트, 서비스 패브릭 응용 프로그램 및 런타임, 및 모든 인프라 가상 컴퓨터 및 해당 관련된 서비스 간에 적용 되도록 합니다.

업데이트를 설치 하는 대로 업데이트 프로세스 대상 Azure 스택 (예: 물리적 호스트 및 가상 컴퓨터 인프라)의 다양 한 하위 시스템으로의 개략적인 상태를 쉽게 볼 수 있습니다.

## <a name="plan-for-updates"></a>업데이트에 대 한 계획

모든 유지 관리 작업의 사용자에 게 알림 및 예약 하 일반 유지 관리 기간 동안 업무 외 시간을 가능한 것이 좋습니다. 유지 관리 작업 테 넌 트 작업 및 포털 작업이 모두에 영향을 줄 수 있습니다.

## <a name="using-the-update-tile-to-manage-updates"></a>업데이트 타일을 사용 하 여 업데이트를 관리
관리자 포털에서 업데이트를 관리 하는 것은 간단 합니다. Azure 스택 연산자 업데이트 타일을 대시보드에 이동 수 있습니다.

- 현재 버전 등 중요 한 정보를 봅니다.
- 업데이트를 설치 하 고 진행률을 모니터링 합니다.
- 이전에 설치 된 업데이트에 대 한 업데이트 기록을 검토 합니다.
 
## <a name="determine-the-current-version"></a>현재 버전 확인

업데이트 타일에는 현재 버전의 Azure 스택을 표시 합니다. 관리자 포털에서 다음 방법 중 하나를 사용 하 여 업데이트 되 고 타일을 얻을 수 있습니다.

- 현재 버전을 보려면 대시보드에서 **업데이트** 바둑판식으로 배열입니다.
 
   ![기본 대시보드 타일 업데이트](./media/azure-stack-updates/image1.png)
 
- 에 **지역 관리** 타일에서 영역 이름을 클릭 합니다. 현재 버전을 볼는 **업데이트** 바둑판식으로 배열입니다.

## <a name="next-steps"></a>다음 단계

- [정책 서비스는 azure 스택](azure-stack-servicing-policy.md) 
- [Azure 스택의 영역 관리](azure-stack-region-management.md)     


