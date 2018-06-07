---
title: 포함 파일
description: 포함 파일
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 05/17/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: cfe675ca269a69c7c2bfa67638acd0afbcd1c8ea
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
각 끝점에는 *공용 포트*와 *개인 포트*가 있습니다.

* 공용 포트는 Azure 부하 분산 장치가 인터넷에서 가상 컴퓨터로 들어오는 트래픽을 수신 대기하는 데 사용됩니다.
* 개인 포트는 가상 머신이 일반적으로 해당 가상 머신에서 실행되는 응용 프로그램 또는 서비스를 대상으로 하는 들어오는 트래픽을 수신 대기하는 데 사용됩니다.

Azure Portal을 사용하여 끝점을 만드는 경우 잘 알려진 네트워크 프로토콜의 TCP 또는 UDP 포트 및 IP 프로토콜 기본값이 제공됩니다. 사용자 지정 끝점의 경우 올바른 IP 프로토콜(TCP 또는 UDP)과 공용 및 개인 포트를 직접 지정해야 합니다. 들어오는 트래픽을 여러 가상 머신에 임의로 배포하려면 여러 끝점으로 구성된 부하 분산된 집합을 만들어야 합니다.

끝점을 만든 후 ACL(액세스 제어 목록)을 사용하여 해당 원본 IP 주소에 따라 끝점의 공용 포트로 들어오는 트래픽을 허용하거나 거부하는 규칙을 정의할 수 있습니다. 그러나 가상 머신이 Azure 가상 네트워크에 있는 경우에는 대신 네트워크 보안 그룹을 사용해야 합니다. 자세한 내용은 [네트워크 보안 그룹 정보](../articles/virtual-network/security-overview.md)를 참조하세요.

> [!NOTE]
> Azure가 자동으로 설정하는 원격 연결 끝점과 연결된 포트에 대해서는 Azure 가상 머신의 방화벽 구성이 자동으로 수행됩니다. 다른 모든 끝점에 지정된 포트의 경우 가상 머신의 방화벽에 대한 구성이 자동으로 수행되지 않습니다. 가상 머신에 대한 끝점을 만들 때 가상 머신의 방화벽에서 끝점 구성에 해당하는 프로토콜 및 개인 포트에 대한 트래픽도 허용하도록 해야 합니다. 방화벽을 구성하려면 가상 컴퓨터에서 실행 중인 운영 체제의 설명서 또는 온라인 도움말을 참조하십시오.
>
>

## <a name="create-an-endpoint"></a>끝점 만들기
1. 아직 로그인하지 않은 경우 [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. **Virtual Machines**를 클릭하고 구성하려는 Virtual Machines의 이름을 클릭합니다.
3. **설정** 그룹에서 **끝점**을 클릭합니다. **끝점** 페이지에는 가상 머신에 대한 모든 현재 끝점이 나열됩니다. (이 예제는 Windows VM입니다. Linux VM에는 기본적으로 SSH용 끝점이 표시됩니다.)

   <!-- ![Endpoints](./media/virtual-machines-common-classic-setup-endpoints/endpointswindows.png) -->
   ![Endpoints](./media/virtual-machines-common-classic-setup-endpoints/endpointsblade.png)

4. 끝점 항목 위에 명령 모음에서 **추가**를 클릭합니다.
5. **끝점 추가** 페이지에서 **이름**에 끝점의 이름을 입력합니다.
6. **프로토콜**에서 **TCP** 또는 **UDP**를 선택합니다.
7. **공용 포트**에 인터넷에서 들어오는 트래픽에 대한 포트 번호를 입력합니다. **개인 포트**에 가상 머신에서 수신 대기할 포트 번호를 입력합니다. 이러한 포트 번호는 다를 수 있습니다. 가상 머신의 방화벽이 프로토콜(6단계) 및 개인 포트에 해당하는 트래픽을 허용하도록 구성되었는지 확인합니다.
10. **Ok**를 클릭합니다.

새 끝점은 **끝점** 페이지에 나열됩니다.

![끝점 만들기 성공](./media/virtual-machines-common-classic-setup-endpoints/endpointcreated.png)

## <a name="manage-the-acl-on-an-endpoint"></a>끝점에 대한 ACL 관리
트래픽을 보낼 수 있는 컴퓨터 집합을 정의하기 위해 끝점의 ACL이 원본 IP 주소에 따라 트래픽을 제한할 수 있습니다. 끝점에 대한 ACL을 추가, 수정 또는 제거하려면 다음 단계를 따르십시오.

> [!NOTE]
> 끝점이 부하 분산 집합의 일부인 경우 끝점에 대한 ACL의 변경 내용이 집합의 모든 끝점에 적용됩니다.
>
>

가상 머신이 Azure 가상 네트워크에 있는 경우에는 ACL 대신 네트워크 보안 그룹을 사용하는 것이 좋습니다. 자세한 내용은 [네트워크 보안 그룹 정보](../articles/virtual-network/security-overview.md)를 참조하세요.

1. 아직 로그인하지 않은 경우 Azure 포털에 로그인합니다.
2. **Virtual Machines**를 클릭하고 구성하려는 Virtual Machines의 이름을 클릭합니다.
3. **Endpoints**를 클릭합니다. 목록에서 해당 끝점을 선택합니다. ACL 목록은 페이지 맨 아래에 있습니다.

   ![ACL 세부 정보 지정](./media/virtual-machines-common-classic-setup-endpoints/aclpreentry.png)

4. 목록의 행을 사용하여 ACL에 대한 규칙을 추가, 삭제 또는 편집하고 해당 순서를 변경합니다. **원격 서브넷** 값은 인터넷에서 들어오는 트래픽에 대한 IP 주소 범위로서, Azure 부하 분산 장치는 해당 원본 IP 주소에 따라 트래픽을 허용하거나 거부하도록 사용합니다. 주소 접두사 형식이라고도 하는 CIDR 형식으로 IP 주소 범위를 지정해야 합니다. 예는 `10.1.0.0/8`입니다.

 ![새 ACL 항목](./media/virtual-machines-common-classic-setup-endpoints/newaclentry.png)


규칙을 사용하여 인터넷의 컴퓨터에 해당하는 특정 컴퓨터에서 들어오는 트래픽만 허용하고 알려진 특정 주소 범위에서 들어오는 트래픽을 거부할 수 있습니다.

규칙은 첫 번째 규칙에서 시작하여 마지막 규칙까지 순서대로 평가됩니다. 따라서 가장 제한적인 규칙까지 오름차순으로 규칙의 순서를 지정해야 합니다. 예제 및 자세한 내용은 [네트워크 Access Control 목록이란](../articles/virtual-network/virtual-networks-acl.md)을 참조하세요.
