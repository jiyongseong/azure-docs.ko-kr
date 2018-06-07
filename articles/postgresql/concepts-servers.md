---
title: "Azure Database for PostgreSQL의 서버 개념"
description: "이 문서에서는 Azure Database for PostgreSQL 서버 구성 및 관리를 위한 고려 사항 및 지침을 제공합니다."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 2db18b014606799bdf5707c4c19f363bbc323e5c
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2018
---
# <a name="azure-database-for-postgresql-servers"></a>Azure Database for PostgreSQL 서버
이 문서에서는 Azure Database for PostgreSQL 서버를 사용할 때의 고려 사항 및 지침을 제공합니다.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>PostgreSQL용 Azure 데이터베이스 서버란?
PostgreSQL용 Azure 데이터베이스 서버는 여러 데이터베이스에 대한 중앙 관리 지점입니다. 온-프레미스 환경에서도 익숙할 수 있는 동일한 PostgreSQL 서버 구문입니다. 특히, PostgreSQL 서비스는 관리되며, 성능 보장을 제공하고, 서버 수준에서 액세스 권한 및 기능을 노출합니다.

PostgreSQL용 Azure 데이터베이스 서버:

- Azure 구독 내에서 만들어집니다.
- 데이터베이스에 대한 부모 리소스입니다.
- 데이터베이스에 대한 네임스페이스를 제공합니다.
- 강력한 수명 의미 체계를 가진 컨테이너로서 서버를 삭제하고 포함된 데이터베이스를 삭제합니다.
- 하위 지역에 리소스를 배치합니다.
- 서버 및 데이터베이스 액세스에 대한 연결 끝점(.postgresql.database.azure.com)을 제공합니다.
- 로그인, 방화벽, 사용자, 역할 구성 등 해당 데이터베이스에 적용되는 관리 정책에 대한 범위를 제공합니다.
- 여러 버전으로 제공됩니다. 자세한 내용은 [지원되는 PostgreSQL 데이터베이스 버전](concepts-supported-versions.md)을 참조하세요.
- 사용자가 확장할 수 있습니다. 자세한 내용은 [PostgreSQL 확장](concepts-extensions.md)을 참조하세요.

PostgreSQL 서버용 Azure Database 내에서 하나 이상의 데이터베이스를 만들 수 있습니다. 서버당 단일 데이터베이스를 만들어 모든 리소스를 활용하도록 하거나 여러 데이터베이스를 만들어 리소스를 공유하도록 할 수 있습니다. 가격은 가격 책정 계층, vCore, 저장소(GB)의 구성에 따라 서버별로 구성됩니다. 자세한 내용은 [가격 책정 계층](./concepts-pricing-tiers.md)을 참조하세요.

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>PostgreSQL용 Azure 데이터베이스 서버에 연결하고 인증을 받으려면 어떻게 해야 하나요?
다음과 같은 요소를 활용하면 데이터베이스에 안전하게 액세스할 수 있습니다.

|||
|:--|:--|
| **인증 및 권한 부여** | PostgreSQL용 Azure 데이터베이스 서버는 네이티브 PostgreSQL 인증을 지원합니다. 서버의 관리자 로그인을 사용하여 서버에 연결하고 인증을 받을 수 있습니다. |
| **프로토콜** | 이 서비스는 PostgreSQL에서 사용되는 메시지 기반 프로토콜을 지원합니다. |
| **TCP/IP** | 이 프로토콜은 TCP/IP 및 Unix 도메인 소켓을 통해 지원됩니다. |
| **방화벽** | 데이터를 보호하기 위해, 방화벽 규칙은 권한이 있는 컴퓨터를 지정할 때까지 서버 및 해당 데이터베이스에 대한 모든 액세스를 금지합니다. [PostgreSQL용 Azure 데이터베이스 서버 방화벽 규칙](concepts-firewall-rules.md)을 참조하세요. |

## <a name="how-do-i-manage-a-server"></a>서버는 어떻게 관리해야 하나요?
[Azure Portal](https://portal.azure.com) 또는 [Azure CLI](/cli/azure/postgres)를 사용하여 Azure Database for PostgreSQL 서버를 관리할 수 있습니다.

## <a name="server-parameters"></a>서버 매개 변수
PostgreSQL 서버 매개 변수는 서버의 구성을 확인합니다. Azure Database for PostgreSQL에서 매개 변수 목록은 Azure Portal 또는 Azure CLI를 사용하여 보고 편집할 수 있습니다. 

Azure Database for PostgreSQL에서 구성 가능한 매개 변수는 Postgres의 관리되는 서비스이며 로컬 Postgres 인스턴스에 있는 매개 변수의 하위 집합입니다(Postgres 매개 변수에 대한 자세한 내용은 [PostgreSQL 설명서](https://www.postgresql.org/docs/9.6/static/runtime-config.html)를 참조하세요). Azure Database for PostgreSQL 서버를 생성할 때 각 매개 변수의 기본값을 사용할 수 있습니다. 서버가 필요한 매개 변수가 다시 시작되거나 변경 내용이 적용될 superuser 액세스가 사용자에 의해 구성될 수 없습니다.


## <a name="next-steps"></a>다음 단계
- 서비스 개요를 보려면 [PostgreSQL용 Azure 데이터베이스 개요](overview.md)를 참조하세요.
- **서비스 계층**에 따른 특정 리소스 할당량 및 제한 사항에 대한 자세한 내용은 [서비스 계층](concepts-pricing-tiers.md)을 참조하세요.
- 서비스 연결에 대한 자세한 내용은 [PostgreSQL용 Azure 데이터베이스에 대한 연결 라이브러리](concepts-connection-libraries.md)를 참조하세요.
- [Azure Portal](howto-configure-server-parameters-using-portal.md) 또는 [Azure CLI](howto-configure-server-parameters-using-cli.md)를 통해 서버 매개 변수를 보고 편집합니다.
