---
title: "Azure 게스트 OS에 대한 지원 가능성 및 사용 중지 정책 가이드 | Microsoft Docs"
description: "클라우드 서비스에서 사용하는 Azure 게스트 OS에 대해 Microsoft가 지원하는 내용에 대한 정보를 제공합니다."
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: 
ms.assetid: 919dd781-4dc6-4e50-bda8-9632966c5458
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 9/20/2017
ms.author: raiye
ms.openlocfilehash: 4bc2d57cf4c7d6e0981aa1a5c7c989860600b897
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Azure 게스트 OS 지원 가능성 및 사용 중지 정책
이 페이지의 정보는 클라우드 서비스 작업자 및 웹 역할(PaaS)에 대해 Azure 게스트 운영 체제([게스트 OS](cloud-services-guestos-update-matrix.md))와 관련됩니다. 가상 컴퓨터(IaaS)에 적용되지 않습니다.

Microsoft는 게시된 게스트 OS에 대한 [지원 정책을 제공합니다](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq). 지금 읽고 있는 페이지는 정책 구현 방식을 설명합니다.

정책은 다음과 같습니다.

1. Microsoft는 **둘 이상의 게스트 OS 제품군**을 지원합니다. 제품군이 사용 중지되면 고객은 공식 사용 중지 날짜로부터 12개월 내에 지원되는 새 게스트 OS 제품군으로 업데이트해야 합니다.
2. Microsoft는 지원되는 게스트 OS 제품군의 **최신 두 버전 이상을 지원합니다**.
3. Microsoft는 Azure SDK의 **최신 두 버전 이상을 지원합니다**. SDK의 버전이 사용 중지되면 고객은 공식 사용 중지 날짜로부터 12개월 내에 최신 버전으로 업데이트해야 합니다.

세 개 이상의 제품군 또는 릴리스가 지원되는 경우도 있습니다. 공식 게스트 OS 지원 정보가 [Azure 게스트 OS 릴리스 및 SDK 호환성 매트릭스(cloud-services-guestos-update-matrix.md)에](cloud-services-guestos-update-matrix.md)표시됩니다.

## <a name="when-a-guest-os-family-or-version-is-retired"></a>게스트 OS 제품군 또는 버전 사용 중지된 경우
Windows Server 운영 체제의 새 공식 버전 릴리스 이후에 새 게스트 OS **&gt;제품군** 이 도입되었습니다. 새 게스트 OS 제품군이 도입될 때마다 Microsoft는 가장 오래된 게스트 OS 제품군을 사용 중지합니다.

새 게스트 OS **버전은** 최신 MSRC 업데이트를 통합하기 위해 매달 도입됩니다. 정기적인 매월 업데이트로 인해 게스트 OS 버전은 보통 출시 후 60일 즈음에 비활성화됩니다. 사용할 수 있는 각 제품군에 대해 최소 두 버전의 게스트 OS를 유지합니다.

### <a name="process-during-a-guest-os-family-retirement"></a>게스트 OS 제품군 사용 중지 시 프로세스
사용 중지가 발표되면 서비스에서 이전 제품군을 공식적으로 제거하기 전에 12개월 "전환" 기간을 고객에게 제공합니다. 이 전환 기간은 Microsoft의 판단에 따라 연장될 수 있습니다. 업데이트는 [Azure 게스트 OS 릴리스 및 SDK 호환성 매트릭스](cloud-services-guestos-update-matrix.md)에 게시됩니다.

단계적 사용 중지 프로세스는 전환 기간 6개월 후에 시작합니다. 이 기간 동안:

1. Microsoft는 고객에게 사용 중지를 알립니다.
2. Azure SDK의 최신 버전은 사용 중지된 게스트 OS 제품군을 지원하지 않습니다.
3. 클라우드 서비스의 새로운 배포 및 재배포가 사용 중지된 제품군에서 허용되지 않습니다.

Microsoft는 "만료 날짜"라고 하는 전환 기간의 마지막날까지 최신 MSRC 업데이트를 통합하는 새 게스트 OS 버전을 계속 도입합니다. 만료일에도 여전히 실행 중인 Cloud Services는 Azure SLA에 따라 지원되지 않습니다. Microsoft의 판단에 따라 해당 날짜 이후에 이 서비스를 강제로 업그레이드, 삭제 또는 중지할 수 있습니다.

### <a name="process-during-a-guest-os-version-retirement"></a>게스트 OS 버전 사용 중지 시 프로세스
고객이 자신의 게스트 OS를 자동으로 업데이트하도록 설정한 경우 게스트 OS 버전에 대한 처리를 걱정하지 않아도 됩니다. 항상 최신 게스트 OS 버전을 사용합니다.

게스트 OS 버전은 매달 출시됩니다. 정기적인 릴리스로 인해 각 버전의 수명은 고정됩니다.

수명이 지나고 60일 후에 버전은 "*사용 안함*"으로 설정됩니다. "사용 안함"은 포털에서 해당 버전이 제거되었음을 의미합니다. 이 버전을 CSCFG 구성 파일에서 더 이상 설정할 수도 없습니다. 기존 배포는 계속 실행됩니다. 그렇지만 새로운 배포 및 기존 배포에 대한 코드 및 구성 업데이트는 허용되지 않습니다.

“사용 안 함”으로 설정된 이후에 게스트 OS 버전이 "*만료*"되고 해당 버전을 실행 중인 모든 설치가 강제로 업그레이드되고 나중에 게스트 OS를 자동으로 업데이트하도록 설정됩니다. 만료가 일괄 처리로 수행되므로 사용할 수 없는 상태에서 만료까지의 기간은 다를 수 있습니다.

Microsoft의 판단에 따라 고객이 전환하기에 용이하도록 이 기간이 연장될 수 있습니다. 모든 변경 사항은 [Azure 게스트 OS 릴리스 및 SDK 호환성 매트릭스](cloud-services-guestos-update-matrix.md)에 게시됩니다.

### <a name="notifications-during-retirement"></a>사용 중지 시 알림
* **제품군 사용 중지** <br>Microsoft는 블로그 게시물 및 포털 알림을 사용합니다. 할당된 서비스 관리자와의 직접적인 통신(전자 메일, 포털 메시지, 전화 통화)를 통해 사용 중지된 게스트 OS 제품군을 여전히 사용 중인 고객에게 알립니다. 모든 변경 내용은 [Azure 게스트 OS 릴리스 및 SDK 호환성 매트릭스](cloud-services-guestos-update-matrix.md)에 게시됩니다.
* **버전 사용 중지** <br>모든 변경 내용 및 발생 날짜는 릴리스, 사용 안 함 및 만료를 포함하여 [Azure 게스트 OS 릴리스 및 SDK 호환성 매트릭스](cloud-services-guestos-update-matrix.md)에 게시됩니다. 서비스 관리자는 비활성화된 게스트 OS 버전 또는 제품군에서 실행 되는 배포가 있는 경우 전자 메일을 받게 됩니다. 이 전자 메일의 타이밍은 달라질 수 있습니다. 일반적으로 비활성화되기 최소 1개월 전이지만 이 시기는 공식 SLA는 없습니다.

## <a name="frequently-asked-questions"></a>질문과 대답
**마이그레이션의 영향을 줄일 수 있는 방법**

Cloud Services를 디자인하기 위한 최신 게스트 OS 제품군을 사용하는 것이 좋습니다.

1. 최신 제품군으로 마이그레이션 계획을 시작합니다.
2. 새 제품군에서 실행 중인 클라우드 서비스를 테스트하려면 임시 테스트 배포를 설정합니다.
3. 새 게스트 OS 버전으로 자동으로 마이그레이션되도록 게스트 OS 버전을 **자동** 으로 설정합니다( [.cscfg](cloud-services-model-and-package.md#cscfg) 파일에서 osVersion=*).

**내 웹 응용 프로그램을 사용하려면 OS와 밀접하게 통합되어야 하는 경우 어떻게 하나요?**

웹 응용 프로그램 아키텍처가 운영 체제의 기본 기능에 의존할 경우 [시작 작업](cloud-services-startup-tasks.md) 또는 기타 확장성 메커니즘과 같은 플랫폼 지원 기능을 사용합니다. 또는 [Azure 가상 컴퓨터](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS – 서비스로서의 인프라)를 사용할 수도 있으며, 기본 운영 체제를 유지 관리하게 됩니다.

## <a name="next-steps"></a>다음 단계
최신 [게스트 OS 릴리스](cloud-services-guestos-update-matrix.md)를 검토합니다.
