---
title: "기존 Azure App Service를 Azure Database for MySQL에 연결"
description: "기존 Azure App Service를 Azure Database for MySQL에 적절하게 연결하는 방법에 대한 지침"
services: mysql
author: ajlam
ms.author: andrela
editor: jasonwhowell
manager: kfile
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: d8b130876e5fa0f2b2322dff82013a409ff7d30e
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2018
---
# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>기존 Azure App Service를 Azure Database for MySQL 서버에 연결
이 항목에서는 기존 Azure App Service를 Azure Database for MySQL 서버에 연결하는 방법을 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에
[Azure 포털](https://portal.azure.com) 에 로그인합니다. Azure Database for MySQL 서버를 만듭니다. 자세한 내용은 [포털에서 Azure Database for MySQL 서버 만들기](quickstart-create-mysql-server-database-using-azure-portal.md) 또는 [CLI를 사용하여 Azure Database for MySQL 서버 만들기](quickstart-create-mysql-server-database-using-azure-cli.md)를 참조하세요.

현재 Azure App Service에서 Azure Database for MySQL로의 액세스를 가능하게 하는 두 가지 솔루션이 있습니다. 두 솔루션 모두 서버 수준 방화벽 규칙 설정 작업이 포함됩니다.

## <a name="solution-1---create-a-firewall-rule-to-allow-all-ips"></a>솔루션 1 - 모든 IP를 허용하는 방화벽 규칙 만들기
Azure Database for MySQL은 데이터를 보호하는 방화벽을 사용하여 액세스 보안을 제공합니다. Azure App Service에서 Azure Database for MySQL 서버로 연결할 때 App Service의 아웃바운드 IP는 기본적으로 동적이라는 것을 염두에 두어야 합니다. 

Azure App Service의 가용성을 보장하려면 이 솔루션을 사용하여 모든 IP를 허용하는 것이 좋습니다.

> [!NOTE]
> Azure 서비스에 대한 모든 IP에서 Azure Database for MySQL에 연결하는 것을 방지하기 위해 현재 Microsoft는 장기 솔루션을 개발 중입니다.

1. MySQL 서버 블레이드의 설정 머리글에서 **연결 보안**을 클릭하여 MySQL용 Azure Database에 대한 연결 보안 블레이드를 엽니다.

   ![Azure Portal - 보안 연결 클릭](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. **규칙 이름**, **시작 IP** 및 **종료 IP**를 입력한 다음 **저장**을 클릭합니다.
   - 규칙 이름: 모든-IP-허용
   - 시작 IP: 0.0.0.0
   - 종료 IP: 255.255.255.255

   ![Azure Portal - 모든 IP 추가](./media/howto-connect-webapp/1_2-add-all-ips.png)

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>솔루션 2 - 아웃바운드 IP를 명시적으로 허용하는 방화벽 규칙 만들기
Azure App Service의 모든 아웃바운드 IP를 명시적으로 추가할 수 있습니다.

1. App Service 속성 블레이드에서 **아웃바운드 IP 주소**를 확인합니다.

   ![Azure Portal - 아웃바운드 IP 확인](./media/howto-connect-webapp/2_1-outbound-ip-address.png)

2. MySQL 연결 보안 블레이드에서 아웃바운드 IP를 하나씩 추가합니다.

   ![Azure Portal - 명시적 IP 추가](./media/howto-connect-webapp/2_2-add-explicit-ips.png)

3. 잊지 말고 방화벽 규칙을 **저장**하세요.

Azure App Service에서는 시간이 지나도 IP 주소를 동일하게 유지하려고 하지만 경우에 따라 IP 주소가 변경될 수도 있습니다. 예를 들어 수용 작업량을 늘리기 위해 앱 재활용 또는 크기 조정 작업이 발생하거나 Azure 지역 데이터 센터에 새 컴퓨터가 추가되는 경우에 발생할 수 있습니다. IP 주소가 변경되면 앱에서 더 이상 MySQL 서버에 연결할 수 없는 경우 가동 중지가 발생할 수 있습니다. 이전 솔루션 중 하나를 선택할 때 이 고려 사항을 염두에 둡니다.

## <a name="ssl-configuration"></a>SSL 구성
Azure Database for MySQL은 기본적으로 SSL을 사용하도록 설정됩니다. 응용 프로그램에서 데이터베이스에 연결할 때 SSL을 사용하지 않는 경우 MySQL 서버에서 SSL을 사용하지 않도록 설정해야 합니다. SSL을 구성하는 방법에 대한 자세한 내용은 [Azure Database for MySQL에 SSL 사용](howto-configure-ssl.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
연결 문자열에 대한 자세한 내용은 [연결 문자열](howto-connection-string.md)을 참조하세요.
