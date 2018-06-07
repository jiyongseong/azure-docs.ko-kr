---
title: "MySQL용 Azure 데이터베이스의 서버 개념"
description: "이 항목에서는 MySQL용 Azure 데이터베이스 서버를 사용할 때의 고려 사항 및 지침을 제공합니다."
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 0cf35efa7b8b4c6f78a8821c6d10e606813b7848
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2018
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>MySQL용 Azure 데이터베이스의 서버 개념
이 문서에서는 Azure Database for MySQL 서버를 사용할 때의 고려 사항 및 지침을 제공합니다.

## <a name="what-is-an-azure-database-for-mysql-server"></a>MySQL용 Azure 데이터베이스 서버란?

MySQL용 Azure 데이터베이스 서버는 여러 데이터베이스에 대한 중앙 관리 지점입니다. 온-프레미스 환경에서도 익숙할 수 있는 동일한 MySQL 서버 구문입니다. 특히, MySQL용 Azure Database 서비스는 관리되며, 성능 보장을 제공하고, 서버 수준에서 액세스 권한 및 기능을 노출합니다.

MySQL용 Azure 데이터베이스 서버:

- Azure 구독 내에서 만들어집니다.
- 데이터베이스에 대한 부모 리소스입니다.
- 데이터베이스에 대한 네임스페이스를 제공합니다.
- 강력한 수명 의미 체계를 가진 컨테이너로서 서버를 삭제하고 포함된 데이터베이스를 삭제합니다.
- 하위 지역에 리소스를 배치합니다.
- 서버 및 데이터베이스 액세스에 대한 연결 끝점을 제공합니다.
- 로그인, 방화벽, 사용자, 역할 구성 등 해당 데이터베이스에 적용되는 관리 정책에 대한 범위를 제공합니다.
- 여러 버전으로 제공됩니다. 자세한 내용은 [지원되는 MySQL용 Azure 데이터베이스 버전](./concepts-supported-versions.md)을 참조하세요.

Azure Database for MySQL 서버 내에서 하나 이상의 데이터베이스를 만들 수 있습니다. 서버당 단일 데이터베이스를 만들어 모든 리소스를 활용하도록 하거나 여러 데이터베이스를 만들어 리소스를 공유하도록 할 수 있습니다. 가격은 가격 책정 계층, vCore, 저장소(GB)의 구성에 따라 서버별로 구성됩니다. 자세한 내용은 [가격 책정 계층](./concepts-service-tiers.md)을 참조하세요.

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>MySQL용 Azure 데이터베이스 서버에 연결하고 인증을 받으려면 어떻게 해야 하나요?

다음과 같은 요소가 데이터베이스에 안전하게 액세스할 수 있도록 도와줍니다.
|||
| :-- | :-- |
| **인증 및 권한 부여** | MySQL용 Azure 데이터베이스 서버는 네이티브 MySQL 인증을 지원합니다. 서버의 관리자 로그인을 사용하여 서버에 연결하고 인증을 받을 수 있습니다. |
| **프로토콜** | 이 서비스는 MySQL에서 사용되는 메시지 기반 프로토콜을 지원합니다. |
| **TCP/IP** | 이 프로토콜은 TCP/IP 및 Unix 도메인 소켓을 통해 지원됩니다. |
| **방화벽** | 데이터를 보호하기 위해, 방화벽 규칙은 사용자가 권한 있는 컴퓨터를 지정하기 전에는 데이터베이스 서버에 대한 모든 액세스를 차단합니다. [MySQL용 Azure 데이터베이스 서버 방화벽 규칙](./concepts-firewall-rules.md)을 참조하세요. |
| **SSL** | 이 서비스는 응용 프로그램 및 데이터베이스 서버 간의 SSL 연결 적용을 지원합니다.  [MySQL용 Azure 데이터베이스에 안전하게 연결하기 위한 사용자 응용 프로그램의 SSL 연결 구성](./howto-configure-ssl.md)을 참조하세요. |

## <a name="how-do-i-manage-a-server"></a>서버는 어떻게 관리해야 하나요?
Azure Portal 또는 Azure CLI를 사용하여 MySQL용 Azure 데이터베이스 서버를 관리할 수 있습니다.

## <a name="next-steps"></a>다음 단계
- 서비스 개요를 보려면 [MySQL용 Azure 데이터베이스 개요](./overview.md)를 참조하세요.
- **서비스 계층**에 따른 특정 리소스 할당량 및 제한 사항에 대한 자세한 내용은 [서비스 계층](./concepts-service-tiers.md)을 참조하세요.
- 서비스 연결에 대한 자세한 내용은 [MySQL용 Azure 데이터베이스에 대한 연결 라이브러리](./concepts-connection-libraries.md)를 참조하세요.
