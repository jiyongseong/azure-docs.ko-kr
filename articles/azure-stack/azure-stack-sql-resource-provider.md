---
title: SQL 데이터베이스를 사용 하 여 Azure 스택에 | Microsoft Docs
description: SQL 데이터베이스에서 SQL Server 리소스 공급자 어댑터를 배포 하는 Azure 스택 및 빠른 단계 서비스로 배포 하는 방법을 알아봅니다.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: b1cc1fad6b0831bcf0bab5ba4f37b753c3cf33ca
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>SQL 데이터베이스를 사용 하 여 Microsoft Azure 스택
SQL Server 리소스 공급자 어댑터를 사용 하 여 SQL 데이터베이스의 서비스로 노출할 [Azure 스택](azure-stack-poc.md)합니다. 리소스 공급자를 설치 하 고 하나 이상의 SQL Server 인스턴스에 연결 하 고 사용자가 만들 수 있습니다.:
- 클라우드-네이티브 앱에 대 한 데이터베이스입니다.
- SQL에 기반 하는 웹 사이트입니다.
- SQL에 기반 하는 작업입니다.
가상 컴퓨터를 프로비저닝 (VM) SQL Server를 호스팅하는 각 시간 필요가 없습니다.

리소스 공급자의 모든 데이터베이스 관리 기능을 지원 하지 않습니다 [Azure SQL 데이터베이스](https://azure.microsoft.com/services/sql-database/)합니다. 예를 들어, 탄력적 데이터베이스 풀과 하는 기능이 데이터베이스 성능 아래로 자동으로 사용할 수 없습니다. 그러나 유사한 리소스 공급자가 지원 만들기, 읽기, 업데이트 및 삭제 (CRUD) 작업. API는 SQL 데이터베이스와 호환 되지 않습니다.

## <a name="sql-resource-provider-adapter-architecture"></a>SQL 리소스 공급자 어댑터 아키텍처
리소스 공급자 세 가지 구성 요소로 이루어져 있습니다.

- **SQL 리소스 공급자 어댑터 VM**, 공급자 서비스를 실행 하는 Windows 가상 컴퓨터는입니다.
- **리소스 공급자 자체**준비 요청을 처리 하는, 및 데이터베이스 리소스를 노출 합니다.
- **SQL Server를 호스트 하는 서버**, 데이터베이스에 대 한 용량을 제공 하는 호스팅 서버를 호출 합니다.

하나 이상의 SQL Server 인스턴스를 만들 또는 외부 SQL Server 인스턴스에 대 한 액세스를 제공 해야 합니다.

> [!NOTE]
> Azure 스택에 설치 되어 있는 서버를 호스팅 통합형된 시스템 테 넌 트 구독에서 생성 되어야 합니다. 기본 공급자 구독에서 만들 수 없습니다. 적절 한 로그인을 사용 하 여 PowerShell 세션을 또는 테 넌 트 포털에서 만들 수 있어야 합니다. 모든 호스팅 서버가 유료 대의 vm 및 적절 한 라이선스가 있어야 합니다. 서비스 관리자의 테 넌 트 구독 소유자가 될 수 있습니다.


## <a name="next-steps"></a>다음 단계

[SQL Server 리소스 공급자를 배포 합니다.](azure-stack-sql-resource-provider-deploy.md)
