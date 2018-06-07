---
title: Azure Payment Processing Blueprint - 물리적 액세스 요구 사항
description: PCI DSS 요구 사항 9
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 91595a69-e9ce-4f9c-8388-10224165d9c0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: b702f3d3a06b1d47c2853ab51ccf4872441da03d
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="physical-access-requirements-for-pci-dss-compliant-environments"></a>PCI DSS 규격 환경에 대한 물리적 요구 사항 
## <a name="pci-dss-requirement-9"></a>PCI DSS 요구 사항 9

**카드 소유자 데이터에 대한 물리적 액세스 제한**

> [!NOTE]
> 이러한 요구 사항은 [PCI DSS(데이터 보안 표준) 버전 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss)의 일부로 [PCI(결제 카드 산업) 보안 표준 협의회](https://www.pcisecuritystandards.org/pci_security/)에 의해 정의됩니다. 각 요구 사항에 대한 테스트 절차 및 지침에 대한 정보는 PCI DSS를 참조하세요.

카드 소유자 데이터를 담고 있는 시스템이나 데이터에 대해 물리적으로 액세스하게 되면 개인이 장치 또는 데이터에 액세스하고 시스템 또는 하드카피를 제거할 수 있으므로 적합하게 제한해야 합니다. 요구 사항 9에서 "현장 직원"은 해당 엔터티의 시설에 실제 있는 정규 및 비상근 직원, 임시 직원, 하청 업체 및 컨설턴트를 의미합니다. "방문자"는 일반적으로 하루 미만의 짧은 기간 동안 시설에 방문해야 하는 공급업체, 현장 직원의 손님, 서비스 작업자 및 기타 사람을 의미합니다. "미디어"는 카드 소유자 데이터를 담은 모든 종이 및 전자 미디어를 의미합니다.

## <a name="pci-dss-requirement-91"></a>PCI DSS 요구 사항 9.1

**9.1** 적합한 시설 출입 제어를 사용하여 카드 소유자 데이터 환경의 시스템에 대한 물리적 액세스를 제한 및 모니터링합니다.

**책임:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure는 데이터 센터에 대한 물리적 액세스 보안을 구현, 적용 및 모니터링할 책임이 있습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 사용할 수 없습니다.|



### <a name="pci-dss-requirement-911"></a>PCI DSS 요구 사항 9.1.1

**9.1.1** 비디오 카메라나 액세스 제어 메커니즘(또는 둘 다)을 사용하여 중요 영역에 대한 개인의 물리적 액세스를 모니터링합니다. 수집된 데이터를 검토하고 다른 출입과 상관 관계를 확인합니다. 법률에서 달리 정하지 않은 한 3개월 이상 저장합니다.

> [!NOTE]
> "중요 영역"이란 카드 소유자 데이터를 저장, 처리 또는 전송하는 시스템이 있는 데이터 센터, 서버실 또는 지역을 의미합니다. 매장의 캐셔 공간 등, POS 터미널만 있는 판매 공간은 여기서 제외됩니다.

**책임:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure는 데이터 센터를 위한 생체 액세스 제어 메커니즘과 CCTV를 실행, 적용 및 모니터링할 책임이 있습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 사용할 수 없습니다.|



### <a name="pci-dss-requirement-912"></a>PCI DSS 요구 사항 9.1.2

**9.1.2** 공개적으로 액세스 가능한 네트워크 잭에 대한 액세스를 제한하는 물리적 및/또는 논리적 제어를 구현합니다. 

예를 들어 공공 영역 및 방문자 액세스가 가능한 영역의 네트워크 잭은 사용하지 않아야 하며 네트워크 액세스 권한이 명시적으로 부여된 경우에만 사용할 수 있습니다. 또는 활성 네트워크 잭이 있는 공간에서 방문자를 항시 안내하는 프로세스를 구현할 수 있습니다.

**책임:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 플랫폼에는 공개적으로 액세스 가능한 네트워크 잭이 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 사용할 수 없습니다.|



### <a name="pci-dss-requirement-913"></a>PCI DSS 요구 사항 9.1.3

**9.1.3** 무선 액세스 지점, 게이트웨이, 휴대 장치, 네트워킹/통신 하드웨어 및 이동 통신 회선에 대한 물리적 액세스를 제한합니다.

**책임:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 네트워크 하드웨어에 대한 물리적 액세스는 액세스 목록, 여러 형태의 인증, 물리적 진입 장벽, 장비 액세스 승인이 필요한 비즈니스 요구 사항을 통해 엄격히 제어됩니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 사용할 수 없습니다.|



## <a name="pci-dss-requirement-92"></a>PCI DSS 요구 사항 9.2

**9.2** 다음을 포함하여 현장 직원과 방문자를 쉽게 구분할 수 있는 프로시저를 개발합니다.
- 현장 직원 및 방문자 식별(예: 배지 할당)
- 액세스 요구 사항 변경
- 현장 직원 및 만료된 방문자 ID(예: ID 배지) 취소 또는 폐기

**책임:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure는 데이터 센터를 방문하는 직원 및 하청업체 ID와 물리적 액세스 보안을 실행, 적용 및 모니터링할 책임이 있습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 사용할 수 없습니다.|



## <a name="pci-dss-requirement-93"></a>PCI DSS 요구 사항 9.3

**9.3** 중요 영역에 대한 현장 직원의 물리적 액세스를 다음과 같이 제어합니다. 
- 액세스는 권한을 부여하고 개인 직무를 기준으로 해야 합니다.
- 퇴사 즉시 액세스 권한을 철회하고 키, 액세스 카드 등 모든 물리적 액세스 메커니즘을 회수하거나 비활성화해야 합니다.

**책임:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft 데이터 센터에 대한 액세스는 최소 권한 원칙을 바탕으로 데이터 센터 팀에서 승인한 권한 부여 액세스 목록을 통해 제어됩니다. 액세스 제어 목록은 분기별로 검토, 확인 및 업데이트합니다.<br /><br />Microsoft Azure Data Center에서는 경계 게이트, 전자 액세스 배지 판독기, 생체 인식 장치, 수동 트랩/입구, 재통과 금지 장치 등의 물리적 액세스 장치를 활용합니다. 액세스 배지 장치는 지속적으로 모니터링됩니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 사용할 수 없습니다.|



## <a name="pci-dss-requirement-94"></a>PCI DSS 요구 사항 9.4

**9.4** 방문자를 식별하고 권한을 부여하는 프로시저를 구현합니다. 프로시저는 다음을 포함해야 합니다.

**책임:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure는 정보 처리 시설과 물리적으로 격리된 보안 로드 베이에서 미리 승인된 전달물을 받고 권한이 부여된 담당자가 이를 모니터링하도록 시행할 책임이 있습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 사용할 수 없습니다.|



### <a name="pci-dss-requirement-941"></a>PCI DSS 요구 사항 9.4.1

**9.4.1** 방문자는 카드 소유자 데이터가 처리 또는 유지 관리되는 영역 안에 들어가기 전에 권한을 부여 받고, 항상 동행해야 합니다.


**책임:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure는 정보 처리 시설과 물리적으로 격리된 보안 로드 베이에서 미리 승인된 전달물을 받고 권한이 부여된 담당자가 이를 모니터링하도록 시행할 책임이 있습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 사용할 수 없습니다.|



### <a name="pci-dss-requirement-942"></a>PCI DSS 요구 사항 9.4.2

**9.4.2** 방문자를 확인하고, 현장 직원과 시각적으로 구별하고 만료 시간이 있는 배지나 기타 ID를 방문자에게 부여합니다.

**책임:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft 데이터 센터 액세스는 사전 승인이 필요하며 권한이 부여된 방문자는 도착 시점에 물리적 보안에서 체크인하고 입장 전에 유효한 ID 증명서를 제출합니다. 배지는 직원을 분명하게 식별합니다. 하청업체와 방문자는 시설을 떠나는 시점에 반납해야 하는 임시 배지를 받습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 사용할 수 없습니다.|



### <a name="pci-dss-requirement-943"></a>PCI DSS 요구 사항 9.4.3

**9.4.3** 시설을 떠나거나 만료일에 배지나 ID를 반납하도록 방문자에게 요청합니다.

**책임:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 방문자는 모든 Microsoft 시설에서 떠나는 시점에 배지를 반납해야 합니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 사용할 수 없습니다.|



### <a name="pci-dss-requirement-944"></a>PCI DSS 요구 사항 9.4.4

**9.4.4** 방문자 로그를 사용하여 카드 소유자 데이터가 저장 또는 전송되는 시설, 컴퓨터실, 데이터 센터에 대한 방문자 활동의 물리적 감사 추적을 유지 관리합니다.
방문자의 이름, 소속 회사 및 물리적 액세스 권한을 부여한 현장 직원을 로그에 문서화합니다.
법률에서 달리 제한하지 않은 한 최소 3개월 간 로그를 유지합니다.

**책임:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure는 카드 소유자 데이터가 저장 또는 전송되는 시설, 컴퓨터실, 데이터 센터에 대한 방문자 활동의 물리적 감사 추적의 형태로 방문자 로그를 유지 관리할 책임이 있습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 사용할 수 없습니다.|



## <a name="pci-dss-requirement-95"></a>PCI DSS 요구 사항 9.5

**9.5** 모든 미디어를 실제로 보호합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 모든 데이터를 Azure SQL Database에 저장합니다. PaaS SQL Database 인스턴스는 데이터베이스 보안 방법을 표시하기 위해 사용됩니다. 자세한 내용은 [PCI 지침 - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database)를 참조하세요.|



### <a name="pci-dss-requirement-951"></a>PCI DSS 요구 사항 9.5.1

**9.5.1** 미디어 백업을 안전한 위치, 가급적이면 대체 또는 백업 사이트나 상용 저장 시설 등의 현장 외 시설에 저장합니다. 적어도 매년 한 번 이상 해당 위치의 보안을 검토합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 모든 데이터를 Azure SQL Database에 저장합니다. PaaS SQL Database 인스턴스는 데이터베이스 보안 방법을 표시하기 위해 사용됩니다. 자세한 내용은 [PCI 지침 - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database)를 참조하세요.|



## <a name="pci-dss-requirement-96"></a>PCI DSS 요구 사항 9.6

**9.6** 다음을 포함하는 모든 종류의 매체의 내부 또는 외부 유포에 대해 엄격한 제어를 유지 관리합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 모든 데이터를 Azure SQL Database에 저장합니다. PaaS SQL Database 인스턴스는 데이터베이스 보안 방법을 표시하기 위해 사용됩니다. 자세한 내용은 [PCI 지침 - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database)를 참조하세요.|



### <a name="pci-dss-requirement-961"></a>PCI DSS 요구 사항 9.6.1

**9.6.1** 데이터의 중요도를 판단할 수 있게 미디어를 분류합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 모든 데이터를 Azure SQL Database에 저장합니다. PaaS SQL Database 인스턴스는 데이터베이스 보안 방법을 표시하기 위해 사용됩니다. 자세한 내용은 [PCI 지침 - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database)를 참조하세요.|



### <a name="pci-dss-requirement-962"></a>PCI DSS 요구 사항 9.6.2

**9.6.2** 정확하게 추적할 수 있는 안전한 운송사나 기타 배송 방법을 통해 미디어를 보냅니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 모든 데이터를 Azure SQL Database에 저장합니다. PaaS SQL Database 인스턴스는 데이터베이스 보안 방법을 표시하기 위해 사용됩니다. 자세한 내용은 [PCI 지침 - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database)를 참조하세요.|



### <a name="pci-dss-requirement-963"></a>PCI DSS 요구 사항 9.6.3

**9.6.3** 안전한 영역에서 옮겨지는 모든 미디어를 경영진이 승인하게 합니다(미디어가 개인에게 배포된 경우 포함).

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 모든 데이터를 Azure SQL Database에 저장합니다. PaaS SQL Database 인스턴스는 데이터베이스 보안 방법을 표시하기 위해 사용됩니다. 자세한 내용은 [PCI 지침 - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database)를 참조하세요.|



## <a name="pci-dss-requirement-97"></a>PCI DSS 요구 사항 9.7

**9.7** 미디어의 저장 및 접근성에 대해 엄격한 제어를 유지 관리합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 모든 데이터를 Azure SQL Database에 저장합니다. PaaS SQL Database 인스턴스는 데이터베이스 보안 방법을 표시하기 위해 사용됩니다. 자세한 내용은 [PCI 지침 - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database)를 참조하세요.|



### <a name="pci-dss-requirement-971"></a>PCI DSS 요구 사항 9.7.1

**9.7.1** 모든 미디어에 대한 인벤토리 로그를 적합하게 유지 관리하고 최소 매년 한 번은 미디어 인벤토리 조사를 수행합니다.


**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 모든 데이터를 Azure SQL Database에 저장합니다. PaaS SQL Database 인스턴스는 데이터베이스 보안 방법을 표시하기 위해 사용됩니다. 자세한 내용은 [PCI 지침 - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database)를 참조하세요.|



## <a name="pci-dss-requirement-98"></a>PCI DSS 요구 사항 9.8

**9.8** 다음과 같이 업무 또는 법률에 따라 필요하지 않게 된 미디어를 파기합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 모든 데이터를 Azure SQL Database에 저장합니다. PaaS SQL Database 인스턴스는 데이터베이스 보안 방법을 표시하기 위해 사용됩니다. 자세한 내용은 [PCI 지침 - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database)를 참조하세요.|



### <a name="pci-dss-requirement-981"></a>PCI DSS 요구 사항 9.8.1

**9.8.1** 하드카피 자료는 카드 소유자 데이터를 재구성할 수 없게 분할, 파기 또는 폐기합니다. 파기 예정인 자료를 담은 용기를 보호합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 모든 데이터를 Azure SQL Database에 저장합니다. PaaS SQL Database 인스턴스는 데이터베이스 보안 방법을 표시하기 위해 사용됩니다. 자세한 내용은 [PCI 지침 - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database)를 참조하세요.|



### <a name="pci-dss-requirement-982"></a>PCI DSS 요구 사항 9.8.2

**9.8.2** 카드 소유자 데이터를 재구성할 수 없게 전자 미디어의 카드 소유자 데이터를 복구 불가능하게 렌더링합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 데이터 파기 기술은 파기될 데이터 개체의 종류, 가입 여부, 저장 장치, 가상 머신 또는 데이터베이스에 따라 달라집니다. Microsoft Azure 다중 테넌트 환경에서는 고객 데이터가 다른 고객 데이터에 "유출"되지 않게 하고 고객이 데이터를 삭제하면 다른 고객(대부분의 경우 한 때 해당 데이터를 소유했던 고객을 포함)이 삭제된 데이터에 액세스하지 못하도록 주의를 기울이고 있습니다.<br /><br />Microsoft Azure는 데이터가 의도치 않게 공개되지 않도록 하는 기본 원칙을 다루는 미디어 삭제에 관한 NIST 800-88 지침을 따릅니다. 이 지침에서는 전자 및 물리적 삭제를 모두 포괄하고 있습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 배포 중 사용한 리소스 그룹을 삭제하여 완전히 삭제할 수 있습니다.|



## <a name="pci-dss-requirement-99"></a>PCI DSS 요구 사항 9.9

**9.9** 카드와의 물리적 상호 작용을 통해 결제 카드 데이터를 수집하는 장치를 변조 및 대체로부터 보호합니다.

> [!NOTE]
> 이러한 요구 사항은 판매 시점에 카드를 직접 제시하는 거래에서 카드 판독 장치(즉 카드 긁기 또는 넣기)에 적용됩니다. 이 요구 사항은 컴퓨터 키보드 및 POS 키패드 같은 수동 키 입력 요소에는 해당하지 않습니다. 

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 Azure를 사용하여 모든 시스템 변경 내용을 기록합니다.<br /><br />Log Analytics는 광범위한 변경 내용 로깅을 제공합니다. 변경을 검토하고 정확도를 확인할 수 있습니다. 보다 구체적인 지침은 [PCI 지침 - 로깅 및 감사](payment-processing-blueprint.md#logging-and-auditing)를 참조하세요.|



### <a name="pci-dss-requirement-991"></a>PCI DSS 요구 사항 9.9.1

**9.9.1** 장치의 최신 목록을 유지 관리합니다. 이 목록은 다음을 포함해야 합니다.
- 제조사, 장치 모델
- 장치 위치(예: 장치가 있는 사이트 또는 시설의 주소)
- 장치 일련 번호 또는 기타 고유 ID

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 배포 문서에 사용된 참조 아키텍처와, 모든 서비스 목록을 제공합니다.|



### <a name="pci-dss-requirement-992"></a>PCI DSS 요구 사항 9.9.2

**9.9.2** 주기적으로 장치 외관을 검토하여 변조(예: 장치에 카드 피싱 장치 부착) 또는 대체(예: 일련번호나 기타 장치 특성을 검사하여 사기 장치와 교체되지 않았는지 파악)를 점검합니다.

> [!NOTE]
> 장치가 변조 또는 대체되었을 수 있는 징후에는 예기치 않은 부착물이나 케이블, 보안 라벨의 누락 또는 변경, 컬러 케이스 파손 또는 색상 차이, 일련번호나 기타 외부 마크 변경 등이 있습니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 사용할 수 없습니다.|



### <a name="pci-dss-requirement-993"></a>PCI DSS 요구 사항 9.9.3

**9.9.3** 직원에게 장치의 변조 또는 대체 시도를 인식할 수 있는 교육을 제공합니다. 교육은 다음을 포함해야 합니다.
- 수리 또는 유지 보수 인력이라고 주장하는 제3의 개인에게 장치 수정 또는 문제 해결을 위한 액세스 권한을 부여하기 전에 ID를 확인합니다.
- 확인 없이 장치를 설치, 교체 또는 반품하지 않습니다.
- 장치와 관련한 의심스러운 행위를 인지합니다(예: 모르는 사람이 장치 플러그를 빼거나 장치를 염).
- 장치 변조 또는 대체가 의심되는 행위 및 표시를 적합한 직원에게 보고합니다(예: 상급자 또는 보안 담당자).

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 사용할 수 없습니다.|



## <a name="pci-dss-requirement-910"></a>PCI DSS 요구 사항 9.10

**9.10** 카드 소유자 데이터에 대한 물리적 액세스를 제한하기 위해 보안 정책과 운영 절차가 문서화되고 사용 중이며 모든 해당 당사자가 알고 있어야 합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 사용할 수 없습니다.|




