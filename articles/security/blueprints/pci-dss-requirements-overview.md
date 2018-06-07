---
title: Azure 지불 처리 청사진 - 대략적인 개요
description: Azure 지불 처리 청사진 - 고객 책임 매트릭스(개요)
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 23cf68d8-bebd-4ac4-a194-39e052281c0e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 4c36f50b5c4ceba911003ec7a633dcab8724c6e0
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="pci-dss-requirements---high-level-overview"></a>PCI DSS 요구 사항 - 대략적인 개요

PCI DSS(Payment Card Industry Data Security Standard)는 카드 소유자 데이터 보안을 강화하고 일관된 데이터 보안 조치를 전세계적으로 광범위하게 적용할 수 있도록 하기 위해 개발되었습니다. PCI DSS는 계정 데이터를 보호하기 위해 디자인된 기술 및 작업 요구 사항의 기준을 제공합니다. PCI DSS는 사업자, 처리자, 인수자, 발급자 및 서비스 공급자를 포함하여 지불 카드 처리와 관련된 모든 엔터티에 적용됩니다. PCI DSS는 CHD(카드 소유자 데이터) 및/또는 SAD(중요 인증 데이터)를 저장, 처리 또는 전송하는 기타 모든 엔터티에도 적용됩니다. 12개의 PCI DSS 요구 사항에 대한 대략적인 개요는 다음과 같습니다.

> [!NOTE]
> 이러한 요구 사항은 [PCI DSS(데이터 보안 표준) 버전 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss)의 일부로 [PCI(결제 카드 산업) 보안 표준 위원회](https://www.pcisecuritystandards.org/pci_security/)에 의해 정의됩니다. 각 요구 사항에 대한 테스트 절차 및 지침에 대한 정보는 PCI DSS를 참조하세요.

|   |   |
|---|---|
| **보안 네트워크 및 시스템<br/>빌드 및 유지 관리** | 1. [카드 소유자 데이터를 보호하기 위한 방화벽 구성 설치 및 유지 관리](pci-dss-requirement-1-firewall.md)<br/><br/> 2. [시스템 암호 및 기타 보안 매개 변수에 공급 업체에서 제공하는 기본값을 사용하지 마십시오.](pci-dss-requirement-2-password.md) |  
| **카드 소유자 데이터 보호** | 3. [저장된 카드 소유자 데이터 보호](pci-dss-requirement-3-chd.md)<br/><br/> 4. [오픈 공용 네트워크를 통한 카드 소유자 데이터 전송 암호화](pci-dss-requirement-4-encryption.md) |
| **취약점 관리<br/>프로그램 유지 관리** | 5. [맬웨어에 대한 시스템 보호 및 정기적인 바이러스 백신 소프트웨어 또는 프로그램 업데이트.](pci-dss-requirement-5-malware.md)<br/><br/> 6. [보안 시스템 및 응용 프로그램 개발 및 유지 관리](pci-dss-requirement-6-secure-system.md) |
| **강력한 액세스<br/>제어 측정값 구현** | 7. [비즈니스에서 알아야 할 카드 소유자 데이터에 대한 액세스 제한](pci-dss-requirement-7-access.md)<br/><br/> 8. [시스템 구성 요소에 대한 액세스 식별 및 인증](pci-dss-requirement-8-identity.md) <br/><br/> 9. [카드 소유자 데이터에 대한 물리적 액세스 제한](pci-dss-requirement-9-physical-access.md) |
| **정기적인 네트워크<br/>모니터링 및 테스트** | 10. [네트워크 리소스 및 카드 소유자 데이터에 대한 모든 액세스 추적 및 모니터링](pci-dss-requirement-10-monitoring.md) <br/><br/> 11. [정기적인 보안 시스템 및 프로세스 테스트](pci-dss-requirement-11-testing.md) |
| **정보 보안 정책<br/>유지 관리** | 12. [모든 직원에 대한 정보 보안을 해결하는 정책 유지 관리](pci-dss-requirement-12-policy.md) |

