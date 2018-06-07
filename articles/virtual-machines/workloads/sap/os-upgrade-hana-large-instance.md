---
title: SAP HANA on Azure(대형 인스턴스)에 대한 운영 체제 업그레이드 | Microsoft Docs
description: SAP HANA on Azure(대형 인스턴스)에 대한 운영 체제 업그레이드 수행
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1354b67ade78a0da1578ef9d98c3b1e8edb41cd4
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077748"
---
# <a name="operating-system-upgrade"></a>운영 체제 업그레이드
이 문서에서는 HANA 대형 인스턴스에서 운영 체제 업그레이드에 대한 세부 정보를 설명합니다.

>[!NOTE]
>OS 업그레이드는 고객 책임이며, Microsoft 운영 지원팀은 업그레이드 하는 동안 주의해야 할 주요 영역을 안내할 수 있습니다. 업그레이드에 대해 계획하기 전에도 해당 운영 체제 공급 업체와 상담해야 합니다.

HLI 단위 프로비전 시 Microsoft 운영 팀은 운영 체제를 설치합니다. 시간이 지남에 따라 HLI 단위에서 운영 체제를 유지 관리해야 합니다(예: 패치, 튜닝, 업그레이드 등).

운영 체제에 주요 변경(예: OS 업그레이드)을 수행하기 전에 **반드시** 다음 호환성 매트릭스를 고려해야 합니다. 업그레이드 같은 주요 운영 체제 활동을 시작하기 전에 참조할 지원 티켓을 열어 **반드시** Microsoft 운영 팀에 문의해야 합니다.

Linux 버전이 다른 여러 SAP HANA 버전에 대한 지원 매트릭스는 [SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581)을 참조하세요.

다음 호환성은 HLI에 대해 테스트되었습니다. HLI 서버가 호환성 매트릭스 외부에 있는 경우 Microsoft 운영 지원팀에 문의하세요.

## <a name="for-type-i-class-sku-category"></a>Type I 클래스 SKU 범주용

| 구성 | SUSE12 SP1 | SUSE12 SP2 | RHEL 7.2 | RHEL 7.3|
| --- | --- | --- | --- | --- |
| 서버 펌웨어 | 3.1(2b) | 3.1(2b) | 3.1(2b) | 3.1(2b) |
| ENIC 버전 | 2.3.0.44 | 2.3.0.44 | 2.3.0.30 | 2.3.0.44 |
| FNIC 버전 | 1.6.0.34 | 1.6.0.34 | 1.6.0.27 | 1.6.0.36 |
| EDAC | 사용 안 함 | 사용 안 함 | 사용 안 함 | 사용 안 함 |
| 커널 버전 | 4.4.21-69-default | 3.12.49-11-default | 3.10.0-327.el7.x86_64 | 3.10.0-693.17.1 |


## <a name="for-type-ii-class-sku-category"></a>Type II 클래스 SKU 범주용

| 구성 | SUSE12 SP1 | SUSE12 SP2 | RHEL 7.2 | RHEL 7.3|
| --- | --- | --- | --- | --- |
| RMC 펌웨어 버전 | 1.1.121  | 1.1.121  | 1.1.121  | 1.1.121 |
| BMC 펌웨어 버전 | 1.0.43   | 1.0.43   | 1.0.43   | 1.0.43  |
| SFS(Software Foundation Server) 버전 | 2.16    | 2.16    | 2.14/2.16   | 2.16   |
| BIOS | 5.2.6    | 5.2.6    | 5.2.6    | 5.2.6   |
| i40e 버전    | 2.0.19     | 2.0.19     | 1.5.10-k    | 1.5.10-k   |
| 커널 버전    | 3.12.49-11.1     | 4.4.21-69.1     | 3.10.0-327    | 3.10.0-693.17.1   |


## <a name="known-issues"></a>알려진 문제

다음은 업그레이드 동안 몇 가지 알려진 일반적인 문제입니다.
- SKU Type II 클래스 SKU에서 SFS(software foundation server)는 OS 업그레이드 후 제거됩니다. OS 업그레이드 후 호환 가능한 SFS를 다시 설치해야 합니다.
- 이더넷 카드 드라이버(ENIC 및 FNIC)가 이전 버전으로 롤백했습니다. 업그레이드 후 호환 가능한 버전의 드라이버를 다시 설치해야 합니다.

## <a name="next-steps"></a>다음 단계
- OS 백업 Type I SKU 클래스에 대해서는 [Backup 및 복원](hana-overview-high-availability-disaster-recovery.md)을 참조합니다.
- Type II SKU 클래스에 대해서는 [Type II SKU용 OS Backup](os-backup-type-ii-skus.md)을 참조합니다.