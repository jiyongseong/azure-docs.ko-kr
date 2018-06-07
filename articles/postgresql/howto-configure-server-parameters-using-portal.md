---
title: "Azure Portal을 통해 Azure Database for PostgreSQL에서 서버 매개 변수 구성"
description: "이 문서에서는 Azure Portal을 통해 Azure Database for PostgreSQL에서 서버 매개 변수를 구성하는 방법을 설명합니다."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 6d43cac79c19e117385549b1678a464dc5731bd7
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2018
---
# <a name="configure-server-parameters-in-azure-portal"></a>Azure Portal에서 서버 매개 변수 구성
Azure Portal을 통해 Azure Database for PostgreSQL에 대한 구성 매개 변수를 나열하고, 표시하며, 업데이트할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.
- [Azure Database for PostgreSQL 서버](quickstart-create-server-database-portal.md)

## <a name="viewing-and-editing-parameters"></a>매개 변수 보기 및 편집
1. [Azure 포털](https://portal.azure.com)을 엽니다.

2. Azure Database for PostgreSQL 서버를 선택합니다.

3. **설정** 섹션 아래에서 **서버 매개 변수**를 선택합니다. 페이지에서는 매개 변수, 해당 값 및 설명 목록을 표시합니다.
![매개 변수의 개요 페이지](./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png)

4. **드롭다운** 단추를 선택하여 client_min_messages와 같이 열거 형식 매개 변수에 사용할 수 있는 값을 확인합니다.
![드롭다운 열거](./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png)

5. **i**(정보) 단추를 선택하거나 위로 마우스를 가져가서 cpu_index_tuple_cost와 같은 숫자 매개 변수에 사용할 수 있는 값의 범위를 확인합니다.
![정보 단추](./media/howto-configure-server-parameters-in-portal/4-information-button.png)

6. 필요한 경우 **검색 상자**를 사용하여 특정 매개 변수로 범위를 좁힙니다. 매개 변수의 이름 및 설명으로 검색할 수 있습니다.
![검색 결과](./media/howto-configure-server-parameters-in-portal/5-search.png)

7. 조정하려는 매개 변수 값을 변경합니다. 세션에서 변경할 내용은 모두 자주색으로 강조 표시되어 있습니다. 값을 변경하면 **저장**을 선택할 수 있습니다. 또는 변경 사항을 **취소**할 수 있습니다.
![변경 내용 저장 또는 제거](./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png)

8. 매개 변수에 새 값을 저장한 경우 언제든지 **모두 기본값으로 다시 설정**을 선택하여 모든 항목을 기본값으로 되돌릴 수있습니다.
![모두 기본값으로 다시 설정](./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png)

## <a name="next-steps"></a>다음 단계
자세한 정보:
- [Azure Database for PostgreSQL에서 서버 매개 변수 개요](concepts-servers.md)
- [Azure CLI를 사용하여 매개 변수 구성](howto-configure-server-parameters-using-cli.md)
