---
title: 위협 감지 - Azure SQL Database | Microsoft Docs
description: 위협 감지는 데이터베이스에 대한 잠재적인 보안 위협을 나타내는 비정상적인 데이터베이스 활동을 감지합니다.
services: sql-database
author: rmatchoro
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 05/17/2018
ms.author: ronmat
ms.reviewer: carlrab
ms.openlocfilehash: db10bbdd39920b05d9fd8c3907f22c3ee5d08b02
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34364151"
---
# <a name="azure-sql-database-threat-detection"></a>Azure SQL Database 위협 검색

Azure SQL Database Threat Detection은 데이터베이스를 액세스하거나 악용하려는 비정상적이고 잠재적으로 해로운 시도를 나타내는 비정상적인 활동을 감지합니다.

Threat Detection은 고급 SQL 보안 기능용 통합 패키지인 [SQL ATP(Advanced Threat Protection)](sql-advanced-threat-protection.md) 제품의 일부입니다. Threat Detection은 중앙 SQL ATP 포털을 통해 액세스하고 관리할 수 있습니다.

## <a name="what-is-threat-detection"></a>Threat Detection이란?

SQL 위협 감지는 비정상적인 활동에 대한 보안 경고를 제공하여 잠재적인 위협이 발생하면 고객이 이를 감지하고 대응할 수 있도록 하는 새로운 차원의 보안을 제공합니다. 사용자는 의심스러운 데이터베이스 활동, 잠재적 취약성 및 SQL 삽입 공격뿐만 아니라 비정상적인 데이터베이스 액세스 및 쿼리 패턴에 대한 경고도 받습니다. SQL Threat Detection은 의심스러운 활동에 대한 세부 정보와 위협을 조사하고 완화하는 방법에 대한 권장 작업을 포함한 경고를 [Azure Security Center](https://azure.microsoft.com/services/security-center/)와 통합합니다. SQL Threat Detection은 보안 전문가가 되거나 고급 보안 모니터링 시스템을 관리할 필요 없이 데이터베이스에 대한 잠재적인 위협에 간단하게 대처할 수 있도록 합니다. 

완전한 조사 환경을 위해, Azure 저장소 계정의 감사 로그에 데이터베이스 이벤트를 쓰는 [SQL Database 감사](sql-database-auditing.md)를 사용하도록 설정하는 것이 좋습니다.  

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Azure Portal에서 데이터베이스에 대한 위협 검색 설정
1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal을 시작합니다.
2. 보호할 Azure SQL Database 서버의 구성 페이지로 이동합니다. 보안 설정에서 **Advanced Threat Protection**을 선택합니다.
3. **Advanced Threat Protection** 구성 페이지에서 다음을 수행합니다.

   - 서버에서 Advanced Threat Protection을 사용하도록 설정.
   - **Threat Detection 설정**의 **경고 전송 대상** 텍스트 상자에 비정상적인 데이터베이스 활동 발견 시 보안 경고를 받을 이메일 주소 목록을 입력합니다.
  
   ![위협 감지 설정](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>PowerShell을 사용하여 위협 감지 설정

스크립트 예제는 [PowerShell을 사용하여 감사 및 위협 감지 구성](scripts/sql-database-auditing-and-threat-detection-powershell.md)을 참조하세요.

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>의심스러운 이벤트 감지 시 비정상적인 데이터베이스 활동 살펴보기

비정상적인 데이터베이스 활동이 감지되면 메일 알림을 받습니다. 메일에는 비정상적인 활동의 특징, 데이터베이스 이름, 서버 이름, 응용 프로그램 이름, 이벤트 시간을 비롯한 의심스러운 보안 이벤트에 대한 정보가 제공됩니다. 또한 전자 메일에는 가능한 원인에 대한 정보 및 데이터베이스에 대한 잠재적인 위협을 조사하고 완화시키기 위해 권장되는 조치가 제공됩니다.

![비정상적인 활동 보고서](./media/sql-database-threat-detection/anomalous_activity_report.png)
     
1. 이메일에서 **최근 SQL 경고 보기** 링크를 클릭하여 Azure Portal을 시작하고, SQL 데이터베이스에서 검색된 활성 SQL 위협을 대략적으로 보여주는 Azure Security Center 경고 페이지를 표시합니다.

   ![활성 위협](./media/sql-database-threat-detection/active_threats.png)

2. 특정 경고를 클릭하여 이 위협을 조사하고 향후 위협을 수정하기 위한 추가 세부 정보 및 조치를 확인합니다.

   예를 들어 SQL 삽입은 데이터 기반 응용 프로그램 공격에 사용되는 인터넷 상의 가장 대표적인 웹 응용 프로그램 보안 문제 중 하나입니다. 공격자는 응용 프로그램의 취약성을 이용하여 악의적인 SQL 문을 응용 프로그램 항목 필드에 삽입하고 데이터베이스의 데이터를 침범하거나 수정합니다. SQL 삽입 경고의 경우, 경고 세부 정보에 악용된 취약한 SQL 문이 포함되어 있습니다.

   ![특정 경고](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-threat-detection-alerts-for-your-database-in-the-azure-portal"></a>Azure Portal에서 데이터베이스에 대한 위협 감지 경고 탐색

SQL Database 위협 감지는 경고를 [Azure Security Center](https://azure.microsoft.com/services/security-center/)와 통합합니다. Azure Portal의 데이터베이스 및 SQL ATP 블레이드 내에 있는 라이브 SQL 위협 감지 타일은 활성 위협의 상태를 추적합니다.

**보안 위협 경고**를 클릭하면 Azure Security Center 경고 페이지가 열리고 데이터베이스에서 감지된 활성 SQL 위협에 대한 개요가 제공됩니다.

   ![위협 감지 경고](./media/sql-database-threat-detection/threat_detection_alert.png)
   
   ![위협 감지 경고 2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="azure-sql-database-threat-detection-alerts"></a>Azure SQL Database Threat Detection 경고 
Threat Detection for Azure SQL Database는 데이터베이스를 액세스하거나 악용하려는 비정상적이고 잠재적으로 해로운 시도를 나타내는 비정상적인 활동을 감지하고 다음 경고를 트리거할 수 있습니다.
- **SQL 삽입에 대한 취약점**: 이 경고는 데이터베이스에서 응용 프로그램이 잘못된 SQL문을 생성할 때 트리거됩니다. 이는 SQL 삽입 공격에 대한 가능한 취약점을 나타낼 수 있습니다. 잘못된 문 생성에 대한 두 가지 가능한 이유가 있습니다.
   - 잘못된 SQL문을 구성하는 응용 프로그램 코드의 결함
   - 응용 프로그램 코드 또는 저장 프로시저는 SQL 삽입에 악용될 수 있는 잘못된 SQL문을 생성할 때 사용자 입력을 삭제하지 않습니다.
- **잠재적인 SQL 삽입**: 활성 악용이 SQL 삽입에 대한 식별된 응용 프로그램 취약성에 대해 발생할 때 이 경고가 트리거됩니다. 즉 공격자가 취약한 응용 프로그램 코드 또는 저장 프로시저를 사용하여 악성 SQL문을 삽입하려고 하는 것을 의미합니다.
- **비정상적인 위치에서 액세스**: 비정상적인 지리적 위치에서 누군가가 SQL 서버에 로그온한 위치에 SQL 서버에 대한 액세스 패턴에 변경이 있는 경우 이 경고가 트리거됩니다. 일부 경우에서 경고는 합법적인 작업(새 응용 프로그램 또는 개발자 유지 관리)을 검색합니다. 다른 경우에서 경고는 악의적인 작업(퇴사 직원, 외부 공격자)을 검색합니다.
- **비정상적인 Azure 데이터 센터에서 액세스**: 최근 기간 동안 이 서버에 표시된 비정상적인 Azure 데이터 센터에서 누군가가 SQL 서버에 로그온한 위치에 SQL 서버에 대한 액세스 패턴에 변경이 있는 경우 이 경고가 트리거됩니다. 일부 경우에서 경고는 합법적인 작업(Azure, Power BI, Azure SQL Query Editor의 새 응용 프로그램)을 검색합니다. 다른 경우에서 경고는 Azure 리소스/서비스의 악의적인 작업(퇴사 직원, 외부 공격자)을 검색합니다.
- **잘 모르는 보안 주체에서 액세스**: 비정상적인 보안 주체(SQL 사용자)를 사용하여 누군가가 SQL 서버에 로그온한 위치에 SQL 서버에 대한 액세스 패턴에 변경이 있는 경우 이 경고가 트리거됩니다. 일부 경우에서 경고는 합법적인 작업(새 응용 프로그램, 개발자 유지 관리)을 검색합니다. 다른 경우에서 경고는 악의적인 작업(퇴사 직원, 외부 공격자)을 검색합니다.
- **잠재적으로 위험한 응용 프로그램에서 액세스**: 잠재적으로 위험한 응용 프로그램이 데이터베이스에 액세스하는 데 사용되는 경우 이 경고가 트리거됩니다. 일부 경우에서 경고는 작업의 침투 테스트를 검색합니다. 다른 경우에서 경고는 일반적인 공격 도구를 사용하여 공격을 검색합니다.
- **무차별 암호 대입 SQL 자격 증명**: 서로 다른 자격 증명을 사용하는 비정상적인 많은 수의 실패한 로그인이 있는 경우 이 경고가 트리거됩니다. 일부 경우에서 경고는 작업의 침투 테스트를 검색합니다. 다른 경우에서 경고는 무차별 암호 대입 공격(brute force attack)을 검색합니다.

## <a name="next-steps"></a>다음 단계

* [SQL Advanced Threat Protection](sql-advanced-threat-protection.md)에 대해 자세히 알아보기 
* [Azure SQL Database 감사](sql-database-auditing.md)에 대한 자세한 정보
* [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)에 대한 자세한 정보
* 가격 책정에 대한 자세한 내용은 [SQL Database 가격 책정 페이지](https://azure.microsoft.com/pricing/details/sql-database/)를 참조하세요.  
