---
title: MySQL을 실행하는 클래식 Azure VM 만들기 | Microsoft Docs
description: 클래식 배포 모델을 사용하여 Windows Server 2012 R2와 MySQL 데이터베이스를 실행하는 Azure 가상 머신을 만듭니다.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: 98fa06d2-9b92-4d05-ac16-3f8e9fd4feaa
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: cynthn
ms.openlocfilehash: be2a4232fdb59deb87d6d9340fd22909707294b8
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="install-mysql-on-a-virtual-machine-created-with-the-classic-deployment-model-running-windows-server-2016"></a>클래식 배포 모델을 사용하여 만든, Windows Server 2016을 실행하는 가상 머신에 MySQL 설치
[MySQL](https://www.mysql.com) 은 인기 있는 오픈 소스 SQL 데이터베이스입니다. 이 자습서에서는 **Windows Server 2016**을 실행하는 가상 컴퓨터에 MySQL Server로 **커뮤니티 버전의 MySQL 5.7.18**을 설치하고 실행하는 방법을 보여 줍니다. 다른 버전의 MySQL 또는 Windows Server에서는 작업 단계가 약간 다를 수 있습니다.

Linux에서 MySQL을 설치하는 방법에 대한 지침은 [Azure에 MySQL을 설치하는 방법](../../linux/mysql-install.md)을 참조하세요.

> [!IMPORTANT]
> Azure에는 리소스를 만들고 작업하기 위한 [리소스 관리자 및 클래식](../../../resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="create-a-virtual-machine-running-windows-server-2016"></a>Windows Server 2016을 실행하는 가상 머신 만들기
Windows Server 2016을 실행하는 VM이 아직 없는 경우 이 [자습서](./tutorial.md)를 사용하여 가상 머신을 만들 수 있습니다.

## <a name="attach-a-data-disk"></a>데이터 디스크 연결
가상 컴퓨터가 만들어진 후에 선택적으로 데이터 디스크를 연결할 수 있습니다. 데이터 디스크 추가는 프로덕션 작업에 권장되며, 운영 체제를 포함하는 OS 드라이브(C:)의 공간 부족을 방지합니다.

[Windows 가상 머신에 데이터 디스크를 연결하는 방법](../attach-managed-disk-portal.md) 을 참조하고 빈 디스크를 연결하는 방법에 대한 지침을 따르세요. 호스트 캐시 설정을 **없음** 또는 **읽기 전용**으로 설정합니다.

## <a name="log-on-to-the-virtual-machine"></a>가상 머신에 로그온
다음으로, MySQL을 설치할 수 있도록 [가상 머신에 로그온](./connect-logon.md)합니다.

## <a name="install-and-run-mysql-community-server-on-the-virtual-machine"></a>가상 머신에서 MySQL Community Server 설치 및 실행
커뮤니티 버전의 MySQL Server를 설치, 구성 및 실행하려면 다음 단계를 따르세요.

> [!NOTE]
> Internet Explorer를 사용하여 항목을 다운로드할 때 IE **강화된 보안 구성**을 Off로 설정하고 다운로드 프로세스를 간소화할 수 있습니다. 시작 메뉴에서 관리 도구/서버 관리자/로컬 서버를 클릭한 다음 IE **강화된 보안 구성**을 클릭하고 구성을 Off로 설정합니다.
>
>

1. 원격 데스크톱을 사용하여 가상 머신에 연결한 후 시작 화면에서 **Internet Explorer** 를 클릭합니다.
2. 오른쪽 위에 있는 **도구** 단추(톱니바퀴 아이콘)를 선택하고 **인터넷 옵션**을 클릭합니다. **보안** 탭을 클릭하고 **신뢰할 수 있는 사이트** 아이콘, **사이트** 단추를 차례로 클릭합니다. 신뢰할 수 있는 사이트 목록에 http://*.mysql.com을 추가합니다. **닫기**를 클릭한 후 **확인**을 클릭합니다.
3. Internet Explorer의 주소 표시줄에 https://dev.mysql.com/downloads/mysql/을 입력합니다.
4. MySQL 사이트를 사용하여 최신 버전의 Windows용 MySQL Installer를 찾아서 다운로드합니다. MySQL Installer를 선택한 경우 전체 파일 집합이 있는 버전(예: 파일 크기가 352.8MB인 mysql-installer-community-5.7.18.0.msi)을 다운로드하고 설치 관리자를 저장합니다.
5. 설치 관리자가 다운로드를 마치면 **실행** 을 클릭하여 설정을 시작합니다.
6. **사용권 계약** 페이지에서 사용권 계약에 동의하고 **다음**을 클릭합니다.
7. **설치 유형 선택** 페이지에서 원하는 설치 유형을 클릭한 후 **다음**을 클릭합니다. 다음 단계에서는 **서버만** 설치 유형을 선택한 것으로 가정합니다.
8. **요구 사항 확인** 페이지가 표시되는 경우 **실행**을 클릭하여 설치 관리자가 누락된 구성 요소를 설치하도록 합니다. C++ 재배포 가능 패키지 런타임과 같이 표시되는 지침을 따릅니다.
9. **설치** 페이지에서 **실행**을 클릭합니다. 설치가 완료되면 **다음**을 클릭합니다.

10. **제품 구성** 페이지에서 **다음**을 클릭합니다.

11. **유형 및 네트워킹** 페이지에서 원하는 구성 유형 및 연결 옵션을 지정합니다(필요한 경우 TCP 포트 포함). **고급 옵션 표시**를 선택하고 **다음**을 클릭합니다.
    ![](./media/mysql-2008r2/MySQL_TypeNetworking.png)

12. **계정 및 역할** 페이지에서 강력한 MySQL 루트 암호를 지정합니다. 필요에 따라 추가 MySQL 사용자 계정을 추가하고 **다음**을 클릭합니다.

    ![](./media/mysql-2008r2/MySQL_AccountsRoles_Filled.png)
13. **Windows 서비스** 페이지에서 필요에 따라 Windows 서비스로 실행 중인 MySQL Server에 대한 기본 설정을 변경하고 **다음**을 클릭합니다.

    ![](./media/mysql-2008r2/MySQL_WindowsService.png)
14. **플러그 인 및 확장** 페이지에서의 선택 항목은 옵션입니다. **다음**을 클릭하여 계속합니다.
15. **고급 옵션** 페이지에서 필요에 따라 로깅 옵션을 변경하고 **다음**을 클릭합니다.

    ![](./media/mysql-2008r2/MySQL_AdvOptions.png)
16. **서버 구성 적용** 페이지에서 **실행**을 클릭합니다. 구성 단계가 완료되면 **마침**을 클릭합니다.
17. **제품 구성** 페이지에서 **다음**을 클릭합니다.
18. 나중에 검사하려는 경우 **설치 완료** 페이지에서 **클립보드에 로그 복사**를 클릭한 다음 **마침**을 클릭합니다.
19. 시작 화면에서 **mysql**을 입력하고 **MySQL 5.7 명령줄 클라이언트**를 클릭합니다.
20. 12단계에서 지정한 루트 암호를 입력하면 명령을 실행하여 MySQL을 구성할 수 있는 프롬프트가 표시됩니다. 명령 및 구문에 대한 자세한 내용은 [MySQL 참조 설명서](https://dev.mysql.com/doc/refman/5.7/en/server-configuration.html)를 참조하세요.

    ![](./media/mysql-2008r2/MySQL_CommandPrompt.png)
21. 기본/데이터 디렉터리 및 드라이브 같은 서버 구성 기본 설정을 구성할 수도 있습니다. 자세한 내용은 [6.1.2 서버 구성 기본값](https://dev.mysql.com/doc/refman/5.7/en/server-configuration-defaults.html)을 참조하세요.

## <a name="configure-endpoints"></a>끝점 구성

인터넷상의 클라이언트 컴퓨터에 사용 가능한 MySQL 서비스의 경우 TCP 포트에 대한 끝점을 구성하고 Windows 방화벽 규칙을 만들어야 합니다. MySQL Server 서비스가 MySQL 클라이언트에 대해 수신하는 기본 포트 값은 3306입니다. **유형 및 네트워킹** 페이지(이전 절차의 11단계)에서 제공된 값과 일치하는 한 다른 포트를 지정할 수 있습니다.

> [!NOTE]
> 프로덕션 사용의 경우 인터넷상의 모든 컴퓨터가 MySQL Server 서비스를 사용할 수 있으므로 보안 영향을 고려합니다. ACL(Access Control 목록)에서 끝점을 사용할 수 있는 원본 IP 주소 집합을 정의할 수 있습니다. 자세한 내용은 [Virtual Machine으로 끝점을 설정하는 방법](setup-endpoints.md)을 참조하세요.
>
>

MySQL Server 서비스의 끝점을 구성하려면

1. Azure Portal에서 **Virtual Machines(클래식)**를 클릭하고 MySQL 가상 머신의 이름을 클릭한 다음 **끝점**을 클릭합니다.
2. 명령 모음에서 **추가**를 클릭합니다.
3. **끝점 추가** 페이지에서 **이름**에 고유 이름을 입력합니다.
4. 프로토콜로 **TCP**를 선택합니다.
5. **공용 포트** 및 **개인 포트** 둘 다에 **3306**과 같은 포트 번호를 입력한 후 **확인**을 클릭합니다.

## <a name="add-a-windows-firewall-rule-to-allow-mysql-traffic"></a>MySQL 트래픽을 허용하도록 Windows 방화벽 규칙 추가
인터넷에서의 MySQL 트래픽을 허용하는 Windows 방화벽 규칙을 추가하려면 MySQL 서버 가상 머신의 _관리자 권한 Windows PowerShell 명령 프롬프트_에서 다음 명령을 실행합니다.

    New-NetFirewallRule -DisplayName "MySQL57" -Direction Inbound –Protocol TCP –LocalPort 3306 -Action Allow -Profile Public

## <a name="test-your-remote-connection"></a>원격 연결 테스트
MySQL Server 서비스를 실행하는 Azure VM에 원격 연결을 테스트하려면 VN를 포함하는 클라우드 서비스의 DNS 이름을 제공해야 합니다.

1. Azure 포털에서 **Virtual Machines(클래식)**을 클릭하고 MySQL 서버 가상 컴퓨터의 이름을 클릭한 다음 **개요**를 클릭합니다.
2. 가상 머신 대시보드에서 **DNS 이름** 값을 적어 둡니다. 다음은 예제입니다.

   ![](media/mysql-2008r2/MySQL_DNSName.png)
3. MySQL을 실행 중인 로컬 컴퓨터 또는 MySQL 클라이언트에서 다음 명령을 실행하여 MySQL 사용자로 로그인합니다.

     mysql -u <yourMysqlUsername> -p -h <yourDNSname>

   예를 들어 MySQL 사용자 이름이 _dbadmin3_이고 가상 머신의 DNS 이름이 _testmysql.cloudapp.net_인 경우 다음의 명령을 사용하여 MySQL을 시작할 수 있습니다.

     mysql -u dbadmin3 -p -h testmysql.cloudapp.net

## <a name="next-steps"></a>다음 단계
MySQL 실행에 대한 자세한 내용은 [MySQL 설명서](http://dev.mysql.com/doc/)를 참조하세요.
