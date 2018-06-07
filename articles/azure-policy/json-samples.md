---
title: Policy 템플릿 샘플
description: Azure Policy에 대한 JSON 샘플
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/17/2018
ms.author: dacoulte
ms.custom: mvc
ms.openlocfilehash: 4b9096c1fb0d9ee74849e259a6e0af2486c5d29b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195127"
---
# <a name="templates-for-azure-policy"></a>Azure Policy에 대한 템플릿

다음 표는 Azure Policy에 대한 json 템플릿 링크를 포함합니다. 이러한 샘플은 [Azure Policy 샘플 리포지토리](https://github.com/Azure/azure-policy)에 있습니다.

| | |
|---|---|
|**Compute**||
| [승인된 VM 이미지](scripts/allowed-custom-images.md) | 승인된 사용자 지정 이미지만 환경에 배포되어야 합니다. 승인된 이미지 ID 배열을 지정합니다. |
| [VM이 관리 디스크를 사용하지 않을 경우 감사](scripts/create-vm-managed-disk.md) | 관리 디스크를 사용하지 않는 가상 머신을 만들 때 감사합니다.|
| [확장이 존재하지 않을 경우 감사](scripts/audit-ext-not-exist.md) | 확장이 가상 머신과 함께 배포되지 않은 경우 감사합니다. 확장 게시자를 지정하고 배포되었는지 여부를 확인하기 위해 입력합니다. |
| [리소스 그룹으로부터 사용자 지정 VM 이미지 허용](scripts/allow-custom-vm-image.md) |  사용자 지정 이미지가 승인된 리소스 그룹으로부터 와야 합니다. 승인된 리소스 그룹의 이름을 지정합니다. |
| [하이브리드 사용 혜택 거부](scripts/deny-hybrid-use.md) | AHUB(Azure Hybrid Use Benefit) 사용을 금지합니다. 온-프레미스 라이선스 사용을 허용하기 원하지 않을 때 사용합니다. |
| [허용되는 않는 VM 확장](scripts/not-allowed-vm-ext.md) | 지정된 확장의 사용을 금지합니다. 금지된 확장 형식을 포함하는 배열을 지정합니다. |
| [특정 VM 플랫폼 이미지만을 허용함](scripts/allow-certain-vm-image.md) | 가상 머신이 특정 버전의 UbuntuServer를 사용해야 합니다. |
| [관리 디스크를 사용하여 VM 만들기](scripts/use-managed-disk-vm.md) | 가상 머신이 관리 디스크를 사용해야 합니다.|
|**모니터링**||
| [진단 설정 감사](scripts/audit-diag-setting.md) | 진단 설정이 지정된 리소스 형식에 대해 사용하도록 설정되지 않은 경우 감사합니다. 진단 설정이 사용되도록 설정되었는지 여부를 확인하려면 리소스 형식의 배열을 지정합니다. |
|**이름 및 텍스트 규칙**||
| [여러 이름 패턴 허용](scripts/allow-multiple-name-patterns.md) | 리소스에 여러 이름 패턴 중 하나를 사용할 수 있습니다. |
| [유사 패턴 필요](scripts/enforce-like-pattern.md) | 리소스 이름 패턴에 대한 유사 조건을 충족하는지 확인합니다. |
| [일치 패턴 필요](scripts/enforce-match-pattern.md) | 리소스 이름이 이름 지정 패턴과 일치하는지 확인합니다. |
| [태그 일치 패턴 필요](scripts/enforce-tag-match-pattern.md) | 태그 값이 텍스트 패턴과 일치하는지 확인합니다. |
|**네트워크**||
| [허용되는 가상 네트워크 게이트웨이 SKU](scripts/allowed-app-gate-sku.md) | 응용 프로그램 게이트웨이에서 승인된 SKU를 사용해야 합니다. 승인된 SKU 배열을 지정합니다. |
| [Network Watcher를 지역에 대해 사용하도록 설정되지 않은 경우 감사](scripts/net-watch-not-enabled.md) | Network Watcher이 지역에 대해 사용되도록 설정되지 않은 경우 감사합니다. Network Watcher가 사용하도록 설정되었는지 여부를 확인하려면 지역의 이름을 지정합니다. |
| [모든 NIC에서 NSG X](scripts/nsg-on-nic.md) | 특정 네트워크 보안 그룹은 모든 가상 네트워크 인터페이스 함께 사용되어야 합니다. 사용할 네트워크 보안 그룹의 ID를 지정합니다. |
| [모든 서브넷에서 NSG X](scripts/nsg-on-subnet.md) | 특정 네트워크 보안 그룹이 모든 가상 서브넷과 함께 사용되어야 합니다. 사용할 네트워크 보안 그룹의 ID를 지정합니다. |
| [허용되는 Express Route 대역폭](scripts/allowed-er-band.md) | 기본 경로는 지정된 대역폭 집합을 사용해야 합니다. 사용자는 Express Route에 대해 지정될 수 있는 SKU의 배열을 지정합니다. |
| [허용되는 Express Route 피어링 위치](scripts/allowed-peering-er.md) | Express Routes는 지정된 피어링 위치를 사용해야 합니다. 허용되는 피어링 위치 배열을 지정합니다. |
| [허용되는 Express Route SKU](scripts/allowed-er-skus.md) | Express Routes는 승인된 SKU를 사용해야 합니다. 허용되는 SKU 배열을 지정합니다. |
| [허용되는 Load Balancer SKU](scripts/allowed-lb-skus.md) | 부하 분산 장치는 승인된 SKU를 사용해야 합니다. 허용되는 SKU 배열을 지정합니다. |
| [ER 네트워크에 피어링하는 네트워크 없음](scripts/no-peering-er-net.md) | 네트워크 피어링이 지정된 리소스 그룹의 한 네트워크에 연결되는 것을 금지합니다. 중앙 관리 네트워크 인프라와의 연결을 예방하기 위해 사용합니다. 연결을 예방하기 위해 리소스 그룹의 이름을 지정합니다. |
| [사용자 정의 경로 테이블 없음](scripts/no-user-def-route-table.md)  |가상 네트워크가 사용자 정의 경로 테이블과 함께 배포되는 것을 금지합니다. |
| [허용되는 가상 네트워크 게이트웨이 SKU](scripts/allowed-vn-gate-sku.md) | 가상 네트워크 게이트웨이는 승인된 SKU 및 게이트웨이 유형을 사용해야 합니다. 승인된 SKU 배열 및 승인된 게이트웨이 유형 배열을 지정합니다. |
| [VM 네트워크 인터페이스에 승인된 서브넷 사용](scripts/use-approved-subnet-vm-nics.md) | 네트워크 인터페이스는 승인된 서브넷을 사용해야 합니다. 승인된 서브넷의 ID를 지정합니다. |
| [VM 네트워크 인터페이스에 승인된 VNet 사용](scripts/use-approved-vnet-vm-nics.md) | 네트워크 인터페이스는 승인된 가상 네트워크를 사용해야 합니다. 승인된 가상 네트워크 ID를 지정합니다. |
|**태그**||
| [청구 태그 정책 이니셔티브](scripts/billing-tags-policy-init.md) | 비용 센터 및 제품 이름에 대해 지정된 태그 값이 필요합니다. 기본 제공 정책을 사용하여 필수 태그를 적용합니다. 태그에 대한 필수 값을 지정합니다.  |
| [리소스 그룹에 태그 및 해당 값 강제 적용](scripts/enforce-tag-rg.md) | 리소스 그룹에 대한 태그 맟 값이 필요합니다. 필수 태그 이름 및 값을 지정합니다.  |
|**SQL**||
| [Microsoft SQL DB 수준 감사 설정 감사](scripts/audit-sql-db-audit-setting.md) | 해당 설정이 지정한 설정과 일치하지 않는 경우 Microsoft Azure SQL Database 감사 설정을 감사합니다. 감사 설정이 사용 또는 사용 안 함으로 설정되었는지 여부를 나타내는 값을 지정 합니다.  |
| [투명한 데이터 암호화 상태 감사](scripts/audit-trans-data-enc-status.md) | Microsoft Azure SQL Database 투명한 데이터 암호화를 사용하도록 설정되지 않은 경우 감사합니다.  |
| [DB 수준 위협 감지 설정 감사](scripts/audit-db-threat-det-setting.md) | 해당 정책이 지정된 상태로 설정되지 않은 경우 SQL 데이터베이스 보안 경고 정책을 감사합니다. 위협 감지가 사용 또는 사용 안 함으로 설정되었는지 여부를 나타내는 값을 지정 합니다.  |
| [Microsoft SQL Server 수준 감사 설정 감사](scripts/audit-sql-ser-leve-audit-setting.md) | 해당 설정이 지정한 설정과 일치하지 않는 경우 Microsoft SQL Server 감사 설정을 감사합니다. 감사 설정이 사용 또는 사용 안 함으로 설정되었는지 여부를 나타내는 값을 지정 합니다. |
| [서버 수준 위협 감지 설정 감사](scripts/audit-sql-ser-threat-det-setting.md) | 해당 정책이 지정된 상태로 설정되지 않은 경우 SQL 데이터베이스 보안 경고 정책을 감사합니다. 위협 감지가 사용 또는 사용 안 함으로 설정되었는지 여부를 나타내는 값을 지정 합니다.  |
| [Azure Active Directory 관리자가 없을 경우 감사](scripts/audit-no-aad-admin.md) | SQL Server에 할당된 Azure Active Directory 관리자가 없을 때 감사합니다. |
| [허용되는 SQL DB SKU](scripts/allowed-sql-db-skus.md) | Microsoft Azure SQL Database는 승인된 SKU를 사용합니다. 허용되는 SKU ID 배열 또는 허용되는 SKU 이름 배열을 지정합니다. |
|**Storage**||
| [저장소 계정 및 Virtual Machine에 대해 허용된 SKU](scripts/allowed-skus-storage.md) | 저장소 계정 및 가상 머신은 승인된 SKU를 사용해야 합니다. 승인된 SKU를 확보하려면 기본 제공 정책을 사용합니다. 승인된 가상 머신 SKU 배열 및 승인된 저장소 계정 SKU 배열을 지정합니다. |
| [저장소 계정에 대해서만 https 트래픽 확인](scripts/ensure-https-stor-acct.md) | 저장소 계정은 HTTPS 트래픽을 사용합니다.  |
| [저장소 계정에 대한 쿨 액세스 계층 거부](scripts/deny-cool-access-tiering.md) | BLOB 저장소 계정에 쿨 액세스 계층을 사용하는 것을 금지합니다.  |
| [저장소 파일 암호화 확인](scripts/ensure-store-file-enc.md) | 저장소 계정에 대해 파일 암호화를 사용할 수 있도록 해야 합니다.  |
|**기본 제공 정책**||
| [허용되는 위치](scripts/allowed-locs.md) | 모든 리소스는 승인된 위치에 배포되어야 합니다. 승인된 위치 배열을 지정합니다.  |
| [허용되는 리소스 유형](scripts/allowed-res-types.md) | 승인된 리소스 종류만 배포되도록 보장합니다. 허용되는 리소스 종류 배열을 지정합니다.  |
| [허용되는 저장소 계정 SKU](scripts/allowed-stor-acct-skus.md) | 저장소 계정은 승인된 SKU를 사용해야 합니다. 승인된 SKU 배열을 지정합니다. |
| [태그 및 기본값 적용](scripts/apply-tag-def-val.md) | 해당 태그를 제공하지 않으면 지정된 태그 이름 및 값을 추가합니다. 적용할 태그 이름 및 값을 지정합니다.  |
| [SQL Database 암호화 감사](scripts/sql-database-encryption-audit.md) | SQL 데이터베이스가 투명 데이터 암호화가 사용되지 않는지 감사합니다. |
| [SQL Server 감사 설정 감사](scripts/sql-server-audit.md) | 감사 설정 사용 여부에 따라 SQL Server를 감사합니다. |
| [Data Lake Store 암호화 적용](scripts/enforce-datalakestore-encryption.md) | 암호화가 사용되지 않는 Data Lake Store 계정을 거부합니다. |
| [태그 및 해당 값 강제 적용](scripts/enforce-tag-val.md) | 지정된 태그 이름 및 값이 필요합니다. 강제 적용할 태그 이름 및 값을 지정합니다.  |
| [허용되지 않는 리소스 유형](scripts/not-allowed-res-type.md) | 지정된 리소스 유형의 배포가 금지됩니다. 차단할 리소스 유형 배열을 지정합니다.  |
| [SQL Server 버전 12.0 필요](scripts/req-sql-12.md) | Microsoft SQL Server 버전 12.0을 사용해야 합니다.  |
| [저장소 계정 암호화 필요](scripts/req-store-acct-enc.md) | 저장소 계정은 BLOB 암호화를 사용해야 합니다.  |
