---
title: Advanced Threat Protection - Azure SQL Database | Microsoft Docs
description: 중요한 데이터 검색 및 분류, 데이터베이스 취약성 관리 및 Azure SQL 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동 검색 기능에 대해 알아봅니다.
services: sql-database
author: ronitr
manager: craigg
ms.service: sql-database
ms.topic: conceptual
ms.date: 5/17/2018
ms.author: ronitr
ms.reviewer: carlrab
ms.openlocfilehash: da21a0b66d86b4cc3e2dc59bdd972d4e24d7e5ec
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34305720"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Azure SQL Database의 Advanced Threat Protection

SQL Advanced Threat Protection은 SQL 고급 보안 기능에 대한 통합 패키지입니다. 여기에는 중요한 데이터 검색 및 분류, 데이터베이스 취약성 관리 및 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동 검색 기능이 포함됩니다. 이러한 기능을 설정하고 관리하는 단일 준비 위치를 제공합니다. 

## <a name="overview"></a>개요

SQL ATP(Advanced Threat Protection)은 데이터 검색 및 분류, 취약성 평가 및 위협 검색을 포함하여 SQL 고급 보안 기능을 제공합니다. 

- [데이터 검색 및 분류](sql-database-data-discovery-and-classification.md)(현재 미리 보기)는 데이터베이스에 있는 중요한 데이터의 검색, 분류, 레이블 지정 및 보호를 위해 Azure SQL Database에 기본 제공된 기능을 제공합니다. 데이터베이스 분류 상태에 대한 가시성을 제공하고, 데이터베이스 내 및 해당 경계 외부의 중요한 데이터에 대한 액세스를 추적하는 데 사용할 수 있습니다.
- [취약성 평가](sql-vulnerability-assessment.md)는 잠재적인 데이터베이스 취약성을 검색, 추적 및 수정할 수 있는 서비스를 간편하게 구성합니다. 보안 상태에 대한 가시성을 제공하고, 보안 문제를 해결하고 데이터베이스 보안을 강화하기 위한 실행 가능한 단계를 포함합니다.
- [위협 검색](sql-database-threat-detection.md)은 비정상적이며 잠재적으로 유해할 수 있는 데이터베이스 액세스 또는 악용 시도를 나타내는 비정상적인 활동을 검색합니다. 지속적으로 의심스러운 활동에 대한 데이터베이스를 모니터링하고, 잠재적인 취약점, SQL 삽입 공격 및 비정상 데이터베이스 액세스 패턴에 대한 보안 경고를 즉시 제공합니다. 위협 감지 경고는 의심스러운 활동에 대한 세부 정보 제공하고 위협을 조사하고 완화하는 방법에 대한 조치를 권장합니다.

한 번 SQL ATP를 활성화하면 포함된 기능을 모두 사용할 수 있습니다. 한 번의 클릭으로 전체 데이터베이스 서버에 대해 ATP를 사용하도록 설정하여 서버의 모든 데이터베이스에 적용할 수 있습니다. 

ATP 가격은 월별 노드당 $15로 Azure Security Center 표준 계층에 부합합니다. 여기서 보호되는 각 SQL Database 서버는 하나의 노드로 계산됩니다. 사용 후 처음 60일은 무료 평가 기간으로 요금이 부과되지 않습니다. 자세한 내용은 [Azure Security Center 가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조하세요.


## <a name="getting-started-with-atp"></a>ATP 시작 
다음 단계에서는 ATP를 시작합니다. 

## <a name="1-enable-atp"></a>1. ATP 설정

Azure SQL Database 창의 **보안** 제목 아래에 있는 **Advanced Threat Protection**으로 이동하여 ATP를 설정합니다. 서버에서 모든 데이터베이스에 ATP를 설정하려면 **서버에 Advanced Threat Protection을 사용하도록 설정**을 클릭합니다.

![ATP 설정](./media/sql-advanced-protection/enable_atp.png) 

> [!NOTE]
> ATP 비용은 월별 노드당 $15로 여기서 노드는 전체 SQL 논리 서버입니다. 따라서 ATP를 사용하여 서버의 모든 데이터베이스를 보호하기 위해 한 번만 결제합니다. 처음 60일은 체험 평가판으로 간주됩니다.

## <a name="2-configure-vulnerability-assessment"></a>2. 취약성 평가 구성

취약성 평가를 사용하기 시작하려면 검사 결과를 저장한 저장소 계정을 구성해야 합니다. 이렇게 하려면 취약성 평가 카드를 클릭합니다.

![VA 구성](./media/sql-advanced-protection/configure_va.png) 

검사 결과를 저장할 저장소 계정을 선택하거나 만듭니다. 또한 일주일에 한 번씩 자동 검사를 실행하는 취약점 평가를 구성하도록 정기 되풀이 검색을 설정할 수 있습니다. 사용자가 제공한 이메일 주소로 검색 결과 요약이 전송됩니다.

![VA 설정](./media/sql-advanced-protection/va_settings.png) 

## <a name="3-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>3. 데이터를 분류하고, 취약점을 추적하고, 위협 경고를 조사하기 시작

**데이터 검색 및 분류** 카드를 클릭하여 분류하도록 추천받은 중요한 열을 확인하고 영구 민감도 레이블을 포함한 데이터를 분류합니다. **취약성 평가** 카드를 클릭하여 취약성 검색 및 보고서를 보고 관리하고, 보안 상태를 추적합니다. 보안 경고를 받은 경우 **위협 검색** 카드를 클릭하여 Azure Security Center 보안 경고 페이지를 통해 경고의 세부 정보를 보고 Azure 구독의 모든 경고에 대한 통합 보고서를 확인합니다.

## <a name="4-manage-atp-settings-on-your-sql-server"></a>4. SQL Server에서 ATP 설정 관리

Advanced Threat Protection 설정을 보고 관리하려면 SQL Server 창에서 **보안** 제목 아래에 있는 **Advanced Threat Protection**으로 이동합니다. 이 페이지에서 ATP를 활성화하거나 비활성화하고, 전체 SQL Server에 대한 위협 검색 설정을 수정할 수 있습니다.

![서버 설정](./media/sql-advanced-protection/server_settings.png) 

## <a name="5-manage-atp-settings-for-a-sql-database"></a>5. SQL 데이터베이스에 대한 ATP 설정 관리

특정 데이터베이스에 대한 ATP 위협 검색 설정을 재정의하려면 **데이터베이스 수준에서 Advanced Threat Protection 사용** 확인란을 선택합니다. 서버에서 모든 데이터베이스에 수신된 경고 대신 또는 외에도 개별 데이터베이스에 대한 별도 위협 검색 경고를 받는 특정 요구 사항이 있는 경우에만 이 옵션을 사용합니다. 

이 확인란을 선택하면 **이 데이터베이스에 대한 위협 검색 설정**을 클릭한 다음, 이 데이터베이스에 대한 관련 설정을 구성합니다.

![데이터베이스 및 위협 검색 설정](./media/sql-advanced-protection/database_threat_detection_settings.png) 

서버에 대한 Advanced Threat Protection 설정은 ATP 데이터베이스 창에서 액세스할 수 있습니다. 기본 ATP 창에서 **설정**을 클릭한 다음, **Advanced Threat Protection 서버 설정 보기**를 클릭합니다. 

![데이터베이스 설정](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>다음 단계 

- [데이터 검색 및 분류](sql-database-data-discovery-and-classification.md)에 대해 자세히 알아보기 
- [취약성 평가](sql-vulnerability-assessment.md)에 대해 자세히 알아보기 
- [위협 검색](sql-database-threat-detection.md)에 대해 자세히 알아보기
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)에 대한 자세한 정보
