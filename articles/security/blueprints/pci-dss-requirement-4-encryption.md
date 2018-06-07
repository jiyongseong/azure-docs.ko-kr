---
title: Azure Payment Processing Blueprint - 암호화 요구 사항
description: PCI DSS 요구 사항 4
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 43f75ba9-cb4e-49ab-b3f4-09e48310bc18
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 6de3290fc2147e3c8ed63642b6e8470093898ef6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="encryption-requirements-for-pci-dss-compliant-environments"></a>PCI DSS 규격 환경에 대한 암호화 요구 사항 
## <a name="pci-dss-requirement-4"></a>PCI DSS 요구 사항 4

**오픈 공용 네트워크를 통한 카드 소유자 데이터 전송 암호화**

> [!NOTE]
> 이러한 요구 사항은 [PCI DSS(데이터 보안 표준) 버전 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss)의 일부로 [PCI(결제 카드 산업) 보안 표준 위원회](https://www.pcisecuritystandards.org/pci_security/)에 의해 정의됩니다. 각 요구 사항에 대한 테스트 절차 및 지침에 대한 정보는 PCI DSS를 참조하세요.

악의적인 사용자가 쉽게 액세스할 수 있는 네트워크를 통해 전송할 때는 중요 정보가 암호화되어야 합니다. 레거시 암호화 및 인증 프로토콜에서 잘못 구성된 무선 네트워크 및 취약성은 취약성을 악용하여 카드 소유자 데이터 환경에 대한 권한 있는 액세스를 얻으려는 악의적인 개인의 지속적인 목표가 되고 있습니다.

## <a name="pci-dss-requirement-41"></a>PCI DSS 요구 사항 4.1

**4.1** 다음과 같이 강력한 암호화 및 보안 프로토콜(예: TLS, IPSEC, SSH 등)을 사용하여 개방된 공용 네트워크를 통한 전송 중에 중요한 카드 소유자 데이터를 보호합니다.
- 신뢰할 수 있는 키와 인증서만 허용합니다.
- 사용 중인 프로토콜은 안전한 버전 또는 구성만 지원합니다.
- 암호화 강도는 사용 중인 암호화 방법에 적합합니다. 

> [!NOTE]
> SSL/초기 TLS를 사용할 경우 부록 A2의 요구 사항을 완료해야 합니다.
>
> 열린 공용 네트워크에는 다음을 망라한 여러 네트워크가 포함됩니다.
> - 인터넷 
> - 무선 기술(802.11 및 Bluetooth 포함)
> - 셀룰러 기술(예: GSM(Global System for Mobile communications), CDMA(Code division multiple access))
> - GPRS(일반 패킷 라디오 서비스)
> - 위성 통신


**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 다음과 같이 배포에 강력한 암호화를 구현하는 PaaS 솔루션입니다.<br /><br />암호화된 미사용 데이터 요구 사항에 부합하기 위해 [Azure Storage](https://azure.microsoft.com/services/storage/)는 다음을 사용합니다.<br /><br /><ul><li>[미사용 데이터에 대한 Azure SSE(Storage Service Encryption)](/azure/storage/storage-service-encryption)</li><li>SQL Database: PaaS SQL Database 인스턴스는 데이터베이스 보안 방법을 표시하기 위해 사용됩니다. 자세한 내용은 [PCI 지침 - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database)를 참조하세요.</li><li>[Azure Disk Encryption(Bitlocker)](/azure/security/azure-security-disk-encryption)</li></ul>Azure Key Vault를 사용하여 Azure Government, PCI DSS 및 HIPAA 요구 사항에 따릅니다.|



### <a name="pci-dss-requirement-411"></a>PCI DSS 요구 사항 4.1.1

**4.1.1** 카드 소유자 데이터를 전송하거나 카드 소유자 데이터 환경에 연결된 무선 네트워크가 업계 모범 사례(예: IEEE 802.11i)를 사용하여 인증 및 전송에 강력한 암호화를 구현하도록 합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 무선 및 SNMP는 솔루션에서 구현되지 않습니다.|



## <a name="pci-dss-requirement-42"></a>PCI DSS 요구 사항 4.2

**4.2** 최종 사용자 메시징 기술(예: 이메일, 인스턴트 메신저, SMS, 채팅 등)을 사용하여 보호되지 않는 PAN을 보내지 않습니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore에는 보호되지 않은 PAN(기본 계정 번호) 데이터를 보낼 수 있게 구현된 메시징 솔루션이 없습니다.|



## <a name="pci-dss-requirement-43"></a>PCI DSS 요구 사항 4.3

**4.3** 저장된 카드 소유자 데이터전송을 암호화하기 위해 보안 정책과 운영 절차가 문서화되고 사용 중이며 모든 해당 당사자가 알고 있어야 합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 고객은 카드 소유자 데이터가 포함된 전송을 문서화 및 암호화할 책임이 있습니다.|




