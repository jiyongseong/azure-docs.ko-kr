---
title: SQL 어댑터 RP Azure 스택에 제공 하는 데이터베이스를 사용 하 여 | Microsoft Docs
description: 만들기 및 SQL 어댑터 리소스 공급자를 사용 하 여 사용자를 프로 비전 하는 SQL 데이터베이스를 관리 하는 방법
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
ms.openlocfilehash: 2808847642639069e60102b195ac97957c8593f0
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
---
# <a name="create-sql-databases"></a>SQL 데이터베이스 만들기
데이터베이스 셀프 서비스 포털 사용자 환경을 통해 제공 됩니다. 사용자는 데이터베이스 서비스를 포함 된 제안을 포함 된 구독이 필요 합니다.

1. 에 로그인 하 고 [Azure 스택](azure-stack-poc.md) 사용자 포털 (서비스 관리자가 관리자 에서도 사용할 수는 관리 포털).

2. 클릭 **새 +** &gt; **데이터 + 저장소 "** &gt; **SQL Server 데이터베이스** &gt; **추가**합니다.

3. 포함 한 데이터베이스 세부 정보를 양식에 입력 한 **데이터베이스 이름**, **최대 크기**, 필요에 따라 다른 매개 변수를 변경 하 고 있습니다. 데이터베이스에 대 한 SKU를 선택 하 라는 메시지가 표시 됩니다. 호스팅 서버가 추가 되는 SKU는 할당 하는 있습니다. 호스팅 서버 SKU를 구성 하는 해당 풀에서 데이터베이스가 만들어집니다.

  ![새 데이터베이스](./media/azure-stack-sql-rp-deploy/newsqldb.png)

  >[!NOTE]
  > 데이터베이스 크기는 64MB 이상 이어야 합니다. 설정을 사용 하 여이 파일을 늘릴 수 있습니다.

4. 로그인 설정을 입력: **데이터베이스 로그인**, 및 **암호**합니다. 이러한 설정은이 데이터베이스에만 액세스를 위해 만든 SQL 인증 자격 증명에 설명 합니다. 로그인 사용자 이름과 전역적으로 고유 해야 합니다. 새 로그인 설정을 만들거나 기존 템플릿을 선택 하십시오. 동일한 SKU를 사용 하 여 다른 데이터베이스에 대 한 로그인 설정을 다시 사용할 수 있습니다.

    ![새 데이터베이스 로그인 생성](./media/azure-stack-sql-rp-deploy/create-new-login.png)


5. 에서는 양식을 제출 하 고 배포를 완료 하기를 기다립니다.

    결과 블레이드에서 "연결 문자열" 필드를 확인 합니다. Azure 스택에서 (예를 들어 웹 응용 프로그램) SQL Server 액세스를 필요로 하는 응용 프로그램에서 해당 문자열을 사용할 수 있습니다.

    ![연결 문자열 검색](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="delete-sql-alwayson-databases"></a>SQL AlwaysOn 데이터베이스 삭제
리소스 공급자에서 SQL AlwaysOn 데이터베이스를 삭제 하는 경우 주 파일 그룹 및 AlwaysOn 가용성 그룹에서 성공적으로 삭제 하지만 AG 디자인, SQL으로 데이터베이스를 복원 상태로 모든 복제본에 배치 하 고 발생 하지 않는 한 데이터베이스를 삭제 하지 않습니다 합니다. 데이터베이스는 삭제 되지 경우 보조 복제본 상태를 동기화 하지 않는로 이동 합니다. RP 통해 동일한 AG에 새 데이터베이스를 다시 추가 계속 작동 합니다.

## <a name="verify-sql-alwayson-databases"></a>SQL AlwaysOn 데이터베이스를 확인 합니다.
AlwaysOn 데이터베이스의 동기화를 표시할지 및 가용성 그룹 및 모든 인스턴스에서 사용할 수 있습니다. 장애 조치 후 데이터베이스가 완벽 하 게 연결 해야 합니다. 데이터베이스 동기화를 확인 하려면 SQL Server Management Studio를 사용할 수 있습니다.

![AlwaysOn 확인](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)


## <a name="next-steps"></a>다음 단계

[SQL Server 리소스 공급자를 유지 관리](azure-stack-sql-resource-provider-maintain.md)
