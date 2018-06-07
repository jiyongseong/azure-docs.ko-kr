---
title: "Stretch Database에 대해 투명한 데이터 암호화를 사용하도록 설정 - Azure | Microsoft Docs"
description: "Azure에서 SQL Server Stretch Database에 대해 TDE(투명한 데이터 암호화)를 사용하도록 설정"
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: barbkess
editor: 
ms.assetid: a44ed8f5-b416-4c41-9b1e-b7271f10bdc3
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: douglasl
ms.openlocfilehash: ceb355d2ba872ed5d3886c6dc82ca75b1854db0a
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2017
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Azure에서 Stretch Database에 대해 TDE(투명한 데이터 암호화)를 사용하도록 설정
> [!div class="op_single_selector"]
> * [Azure 포털](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

TDE(투명한 데이터 암호화)는 응용 프로그램에 대한 변경 요구 없이 데이터베이스, 연결된 백업 및 저장된 트랜잭션 로그 파일에 대한 실시간 암호화 및 암호 해독을 수행하여 악의적인 활동의 위협으로부터 보호합니다.

TDE는 데이터베이스 암호화 키라는 대칭 키를 사용하여 전체 데이터베이스의 저장소를 암호화합니다. 데이터베이스 암호화 키는 기본 제공 서버 인증서에 의해 보호됩니다. 기본 제공 서버 인증서는 각 Azure 서버에 대해 고유합니다. Microsoft는 적어도 90일마다 이러한 인증서를 자동으로 회전합니다. TDE에 대한 일반적인 설명은 [투명한 데이터 암호화(TDE)]를 참조하세요.

## <a name="enabling-encryption"></a>암호화 설정
스트레치 사용 SQL Server 데이터베이스에서 마이그레이션된 데이터를 저장하는 Azure 데이터베이스에 대해 TDE를 사용하도록 설정하려면 다음을 수행합니다.

1. [Azure 포털](https://portal.azure.com)
2. 데이터베이스 블레이드에서 **설정** 단추 클릭
3. **투명한 데이터 암호화** 옵션 선택 ![][1]
4. **켜기** 설정을 선택한 다음 **저장**
   ![][2] 선택

## <a name="disabling-encryption"></a>암호화 비활성화
스트레치 사용 SQL Server 데이터베이스에서 마이그레이션된 데이터를 저장하는 Azure 데이터베이스에 대해 TDE를 사용하지 않도록 설정하려면 다음을 수행합니다.

1. [Azure 포털](https://portal.azure.com)
2. 데이터베이스 블레이드에서 **설정** 단추 클릭
3. **투명한 데이터 암호화** 옵션 선택
4. **끄기** 설정을 선택한 다음 **저장** 선택

<!--Anchors-->
[투명한 데이터 암호화(TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->
