---
title: "Azure Security Center에서 Azure SQL 서비스 및 데이터 보호 | Microsoft Docs"
description: "이 문서에서는 데이터 및 Azure SQL 서비스를 보호하고 보안 정책을 준수하는 데 도움이 되는 Azure Security Center의 권장 사항에 대해 설명합니다."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2017
ms.author: terrylan
ms.openlocfilehash: 0c3a11e9a86767641533b16de1b96b4c59bfdf51
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>Azure Security Center에서 Azure SQL 서비스 및 데이터 보호
Azure 보안 센터에서는 Azure 리소스의 보안 상태를 분석합니다. 보안 센터가 잠재적인 보안 취약점을 식별하는 경우 필요한 컨트롤을 구성하는 과정을 안내하는 권장 사항을 만듭니다.  이러한 권장 사항은 가상 컴퓨터(VM), 네트워킹, SQL 및 데이터, 응용 프로그램 등의 Azure 리소스 유형에 적용됩니다.

이 문서에서는 Azure SQL 서비스 및 데이터에 적용되는 권장 사항에 대해 설명합니다. 권장 사항은 Azure SQL 서버 및 데이터베이스에 대한 감사 사용 및 SQL Database에 대한 암호화 설정, Azure Storage 계정의 암호화 설정에 초점을 둡니다.  아래 테이블을 참조로 사용하여 제공되는 SQL 서비스 및 데이터 권장 사항을 이해하고 각 권장 사항을 적용할 경우 어떻게 되는지 이해할 수 있습니다.

## <a name="available-sql-service-and-data-recommendations"></a>제공되는 SQL 서비스 및 데이터 권장 사항
| 권장 사항 | 설명 |
| --- | --- |
| [SQL Server에서 감사 및 위협 감지 사용](security-center-enable-auditing-on-sql-servers.md) |Azure SQL 서버(Azure SQL 서비스만 해당, 가상 컴퓨터에서 실행되는 SQL 제외됨)에 감사 및 위협 감지를 사용하는 것이 좋습니다. |
| [SQL Database에서 감사 및 위협 감지 사용](security-center-enable-auditing-on-sql-databases.md) |Azure SQL 서버(Azure SQL Database만 해당, 가상 컴퓨터에서 실행되는 SQL 제외됨)에 감사 및 위협 감지를 사용하는 것이 좋습니다. |
| [SQL 데이터베이스에서 투명한 데이터 암호화 활성화](security-center-enable-transparent-data-encryption.md) |SQL 데이터베이스(Azure SQL 서비스에만 해당)에 대해 암호화를 활성화하라는 권장 사항입니다. |

## <a name="see-also"></a>참고 항목
다른 Azure 리소스 유형에 적용되는 권장 사항에 대해 자세히 알아보려면 다음을 참조하세요.

* [Azure 보안 센터에서 가상 컴퓨터 보호](security-center-virtual-machine-recommendations.md)
* [Azure 보안 센터에서 응용 프로그램 보호](security-center-application-recommendations.md)
* [Azure 보안 센터에서 네트워크 보호](security-center-network-recommendations.md)

보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure 보안 센터에서 보안 정책 설정](security-center-policies.md) - Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure 보안 센터에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure 보안 센터 FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.
