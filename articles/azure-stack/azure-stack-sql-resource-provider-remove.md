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
ms.openlocfilehash: c2686a2d5241af46e70263d1827028aa7e9b2138
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
---
# <a name="remove-the-sql-resource-provider"></a>SQL 리소스 공급자를 제거 합니다.

SQL 리소스 공급자를 제거 하려면 먼저 모든 종속성을 제거 하는 데 필수적인 됩니다.

1. 이 버전의 SQL 리소스 공급자 어댑터에 대 한 다운로드 하는 원래 배포 패키지가 있는지 확인 합니다.

2. 리소스 공급자에서 모든 사용자 데이터베이스를 삭제 해야 합니다. (사용자 데이터베이스를 삭제 하지 않는 삭제 데이터입니다.) 이 태스크는 사용자가 직접 수행 되어야 합니다.

3. 관리자는 SQL 리소스 공급자 어댑터에서 호스팅 서버를 삭제 해야 합니다.

4. 관리자는 SQL 리소스 공급자 어댑터 참조 하는 모든 계획을 삭제 해야 합니다.

5. 관리자는 모든 Sku 및 SQL 리소스 공급자 어댑터와 관련 된 할당량 삭제 해야 합니다.

6. 다음 요소를 사용 하 여 배포 스크립트를 다시 실행 하십시오.
    - -매개 변수를 제거 합니다.
    - Azure 리소스 관리자 끝점
    - DirectoryTenantID
    - 서비스 관리자 계정에 대 한 자격 증명

