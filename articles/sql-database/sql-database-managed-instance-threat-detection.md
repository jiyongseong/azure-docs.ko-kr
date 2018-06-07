---
title: 위협 검색 - Azure SQL Database 관리되는 인스턴스 | Microsoft Docs
description: 위협 감지는 데이터베이스에 대한 잠재적인 보안 위협을 나타내는 비정상적인 데이터베이스 활동을 감지합니다.
services: sql-database
author: rmatchoro
manager: craigg
ms.service: sql-database
ms.custom: security, managed instance
ms.topic: article
ms.date: 03/07/2018
ms.author: ronmat
ms.reviewer: carlrab
ms.openlocfilehash: 2112a0a3997af478de6b8c80abcf7924a66302f0
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="azure-sql-database-managed-instance-threat-detection"></a>Azure SQL Database 관리되는 인스턴스 위협 검색

SQL 위협 검색은 Azure SQL Database 관리되는 인스턴스(미리 보기)에서 데이터베이스를 액세스하거나 악용하려는 비정상적이고 잠재적으로 해로운 시도를 나타내는 비정상적인 활동을 감지합니다.

## <a name="overview"></a>개요

위협 검색은 관리되는 인스턴스에 대한 잠재적인 보안 위협을 나타내는 비정상적인 데이터베이스 활동을 검색합니다. 관리되는 인스턴스의 경우에는 위협 검색이 미리 보기에 포함되어 있습니다.

위협 검색은 비정상적인 데이터베이스 활동에 대한 보안 경고를 제공하여 잠재적인 위협이 발생하면 고객이 이를 감지하고 대응할 수 있도록 하는 새로운 차원의 보안을 제공합니다. 위협 검색은 보안 전문가가 되거나 고급 보안 모니터링 시스템을 관리할 필요 없이 관리되는 인스턴스에 대한 잠재적인 위협에 간단하게 대처할 수 있도록 합니다. 완전한 조사 환경을 위해 Azure 관리되는 인스턴스 감사를 사용하도록 설정하여, Azure Storage 계정의 감사 로그에 데이터베이스 이벤트를 씁니다. 

SQL 위협 검색은 경고를 [Azure Security Center](https://azure.microsoft.com/services/security-center/)와 통합하며, 보호되는 각 관리되는 인스턴스에 대한 비용은 Azure Security Center 표준 계층과 동일한 가격($15/노드/월, 여기서 보호되는 각 SQL Database 서버를 하나의 노드로 계산)으로 청구됩니다.  

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Azure Portal에서 관리되는 인스턴스에 대한 위협 검색 설정
1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal을 시작합니다.
2. 보호할 관리되는 인스턴스의 구성 페이지로 이동합니다. **설정** 페이지에서 **위협 검색**을 선택합니다. 
3. 위협 검색 구성 페이지에서 
   - 위협 감지를 **켭니다**
   - 비정상적인 데이터베이스 활동이 감지되는 경우 보안 경고를 수신할 **메일 목록**을 구성합니다.
   - 비정상적인 위협 감사 레코드가 저장되는 **Azure Storage 계정**을 선택합니다. 
4.  **저장**을 클릭하여 새롭거나 업데이트된 위협 검색 정책을 저장합니다.

   ![위협 검색](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="explore-anomalous-managed-instance-activities-upon-detection-of-a-suspicious-event"></a>의심스러운 이벤트 감지 시 비정상적인 관리되는 인스턴스 활동 살펴보기

1. 비정상적인 데이터베이스 활동이 감지되면 메일 알림을 받습니다. 

   전자 메일에는 비정상적인 활동의 특징, 데이터베이스 이름, 서버 이름, 이벤트 시간을 포함하여 의심스러운 보안 이벤트에 대한 정보가 제공됩니다. 또한 전자 메일에는 가능한 원인에 대한 정보 및 관리되는 인스턴스에 대한 잠재적인 위협을 조사하고 완화시키기 위해 권장되는 조치가 제공됩니다.

   ![위협 검색 전자 메일](./media/sql-database-managed-instance-threat-detection/threat-detection-email.png)

2. 전자 메일에서 **최근 SQL 경고 보기** 링크를 클릭하여 Azure Portal을 시작하고 관리되는 인스턴스 데이터베이스에서 검색된 활성 SQL 위협을 대략적으로 보여 주는 Azure Security Center 경고 페이지를 표시합니다.

   ![활성 위협](./media/sql-database-managed-instance-threat-detection/active-threats.png)

3. 특정 경고를 클릭하여 이 위협을 조사하고 향후 위협을 수정하기 위한 추가 세부 정보 및 조치를 확인합니다.

   예를 들어, SQL 삽입은 인터넷 상의 일반적인 웹 응용 프로그램 보안 문제 중 하나입니다. SQL 삽입은 데이터 기반 응용 프로그램을 공격하는 데 사용됩니다. 공격자는 응용 프로그램의 취약성을 이용하여 악의적인 SQL 문을 응용 프로그램 항목 필드에 삽입하고 데이터베이스의 데이터를 침범하거나 수정합니다. SQL 삽입 경고의 경우, 경고 세부 정보에 악용된 취약한 SQL 문이 포함되어 있습니다.

   ![SQL 삽입](./media/sql-database-managed-instance-threat-detection/sql-injection.png)

## <a name="managed-instance-threat-detection-alerts"></a>관리되는 인스턴스 위협 검색 경고 

관리되는 인스턴스에 대한 위협 검색은 데이터베이스를 액세스하거나 악용하려는 비정상적이고 잠재적으로 해로운 시도를 나타내는 비정상적인 활동을 감지하고 다음 경고를 트리거할 수 있습니다.
- **SQL 삽입에 대한 취약점**: 이 경고는 데이터베이스에서 응용 프로그램이 잘못된 SQL문을 생성할 때 트리거됩니다. 이는 SQL 삽입 공격에 대한 가능한 취약점을 나타낼 수 있습니다. 잘못된 문 생성에 대한 두 가지 가능한 이유가 있습니다.
 - 잘못된 SQL문을 구성하는 응용 프로그램 코드의 결함
 - 응용 프로그램 코드 또는 저장 프로시저는 SQL 삽입에 악용될 수 있는 잘못된 SQL문을 생성할 때 사용자 입력을 삭제하지 않습니다.
- **잠재적인 SQL 삽입**: 활성 악용이 SQL 삽입에 대한 식별된 응용 프로그램 취약성에 대해 발생할 때 이 경고가 트리거됩니다. 즉, 공격자가 취약한 응용 프로그램 코드 또는 저장 프로시저를 사용하여 악성 SQL문을 삽입하려고 하는 것을 의미합니다.
- **비정상적인 위치에서 액세스**: 비정상적인 지리적 위치에서 누군가가 관리되는 인스턴스에 로그온하여 관리되는 인스턴스에 대한 액세스 패턴에 변경이 있는 경우 이 경고가 트리거됩니다. 일부 경우에서 경고는 합법적인 작업(새 응용 프로그램 또는 개발자 유지 관리 작업)을 검색합니다. 다른 경우에서 경고는 악의적인 작업(퇴사 직원, 외부 공격자 등)을 검색합니다.
- **비정상적인 Azure 데이터 센터에서 액세스**: 최근 기간 동안 이 관리되는 인스턴스에 액세스하는 것으로 확인되지 않은 누군가가 Azure 데이터 센터에서 관리되는 인스턴스에 로그인하여 관리되는 인스턴스에 대한 액세스 패턴이 변경될 경우 이 경고가 트리거됩니다. 일부 경우에서 경고는 합법적인 작업(Azure, Power BI, Azure SQL Query Editor의 새 응용 프로그램 등)을 검색합니다. 다른 경우에서 경고는 Azure 리소스/서비스의 악의적인 작업(퇴사 직원, 외부 공격자)을 검색합니다.
- **잘 모르는 보안 주체에서 액세스**: 비정상적인 보안 주체(SQL 사용자)를 사용하여 누군가가 관리되는 인스턴스에 로그온하여 관리되는 인스턴스 서버에 대한 액세스 패턴이 변경될 경우 이 경고가 트리거됩니다. 일부 경우에서 경고는 합법적인 작업(새 응용 프로그램 개발자의 유지 관리 작업)을 검색합니다. 다른 경우에서 경고는 악의적인 작업(퇴사 직원, 외부 공격자)을 검색합니다.
- **잠재적으로 위험한 응용 프로그램에서 액세스**: 잠재적으로 위험한 응용 프로그램이 데이터베이스에 액세스하는 데 사용되는 경우 이 경고가 트리거됩니다. 일부 경우에서 경고는 작업의 침투 테스트를 검색합니다. 다른 경우에서 경고는 일반적인 공격 도구를 사용하여 공격을 검색합니다.
- **무차별 암호 대입 SQL 자격 증명**: 서로 다른 자격 증명을 사용하는 비정상적인 많은 수의 실패한 로그인이 있는 경우 이 경고가 트리거됩니다. 일부 경우에서 경고는 작업의 침투 테스트를 검색합니다. 다른 경우에서 경고는 무차별 암호 대입 공격(brute force attack)을 검색합니다.

## <a name="next-steps"></a>다음 단계

- [관리되는 인스턴스란?](sql-database-managed-instance.md)에 제공되는 관리되는 인스턴스에 대한 자세한 정보
- [관리되는 인스턴스 감사](https://go.microsoft.com/fwlink/?linkid=869430)에 대한 자세한 정보 
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)에 대한 자세한 정보
