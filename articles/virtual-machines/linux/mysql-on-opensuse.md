---
title: "Azure에서 OpenSUSE VM에 MySQL 설치 | Microsoft Docs"
description: "Azure에서 OpenSUSE Linux 가상 컴퓨터에 MySQL을 설치하는 방법을 알아봅니다."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 1594e10e-c314-455a-9efb-a89441de364b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: cynthn
ms.openlocfilehash: 88bd895cb3a384f1ada0394fe2da206aca86b981
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2018
---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Azure에서 OpenSUSE Linux를 실행하는 가상 머신에 MySQL 설치

[MySQL](http://www.mysql.com)은 인기 있는 오픈 소스 SQL 데이터베이스입니다. 이 자습서에서는 OpenSUSE Linux를 실행하는 가상 머신을 만든 다음 MySQL을 설치하는 방법을 보여 줍니다.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 Azure CLI 버전 2.0 이상이 필요합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>OpenSUSE Linux를 실행하는 가상 머신 만들기

먼저 리소스 그룹을 만듭니다. 다음 예제에서는 *mySQSUSEResourceGroup* 리소스 그룹의 이름을 지정하고 *미국 동부* 지역에 해당 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name mySQLSUSEResourceGroup --location eastus
```

VM을 만듭니다. 다음 예제에서는 *myVM* VM의 이름을 지정합니다. 또한*Standard_D2s_v3* VM 크기를 사용하지만, 워크로드에 가장 적합한 것으로 생각되는 [VM 크기](sizes.md)를 선택해야 합니다.

```azurecli-interactive
az vm create --resource-group mySQLSUSEResourceGroup \
   --name myVM \
   --image openSUSE-Leap \
   --size Standard_D2s_v3 \
   --generate-ssh-keys
```

또한 MySQL용 3306 포트를 통한 트래픽을 허용하려면 네트워크 보안 그룹에 규칙을 추가해야 합니다.

```azurecli-interactive
az vm open-port --port 3306 --resource-group mySQLSUSEResourceGroup --name myVM
```

## <a name="connect-to-the-vm"></a>VM에 연결

SSH를 사용하여 VM에 연결합니다. 이 예제에서 VM의 공용 IP 주소는 *10.111.112.113*입니다. VM을 만들 때 출력에서 IP 주소를 볼 수 있습니다.

```azurecli-interactive  
ssh 10.111.112.113
```

 
## <a name="update-the-vm"></a>VM 업데이트
 
VM에 연결되면 시스템 업데이트와 패치를 설치합니다. 
   
```bash
sudo zypper update
```

프롬프트에 따라 VM을 업데이트합니다.

## <a name="install-mysql"></a>MySQL 설치 


SSH를 통해 VM에 MySQL을 설치합니다. 프롬프트에 적절하게 회신합니다.

```bash
sudo zypper install mysql
```
 
시스템을 부팅할 때 MySQL을 시작하도록 설정합니다. 

```bash
sudo systemctl enable mysql
```
MySQL을 사용할 수 있는지 확인 합니다.

```bash
systemctl is-enabled mysql
```

이 경우 반환되는 값은 enabled입니다.


## <a name="mysql-password"></a>MySQL 암호

설치한 후 MySQL 루트 암호는 기본적으로 비어있습니다. **mysql\_secure\_installation** 스크립트를 실행하여 MySQL을 보호합니다. 스크립트에서 MySQL 루트 암호 변경, 익명 사용자 계정 삭제, 원격 루트 로그인 비활성화, 테스트 데이터베이스 제거, 권한 테이블 다시 로드 등의 작업을 수행하라는 메시지를 표시합니다. 


```bash
mysql_secure_installation
```

## <a name="log-in-to-mysql"></a>MySQL에 로그인

이제 로그인하여 MySQL 프롬프트에 진입할 수 있습니다.

```bash  
mysql -u root -p
```
그러면 SQL 문을 발행하여 데이터베이스와 상호 작용할 수 있는 MySQL 프롬프트로 전환됩니다.

이제 새로운 MySQL 사용자를 만듭니다.

```   
CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
줄 끝에 있는 세미콜론(;)은 명령을 끝내는 데 중요한 역할을 합니다.


## <a name="create-a-database"></a>데이터베이스 만들기


데이터베이스를 만들고 `mysqluser` 사용자 권한을 부여합니다.

```   
CREATE DATABASE testdatabase;
GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
데이터베이스 사용자 이름과 암호는 데이터베이스에 연결하는 스크립트에서만 사용됩니다.  데이터베이스 사용자 계정 이름에 시스템에 있는 실제 사용자 계정을 반영할 필요는 없습니다.

다른 컴퓨터에서 로그인을 사용하도록 설정합니다. 다음 예제에서 로그인하려는 컴퓨터의 IP 주소는 *10.112.113.114*입니다.

```   
GRANT ALL ON testdatabase.* TO 'mysqluser'@'10.112.113.114' IDENTIFIED BY 'password';
```
   
MySQL 데이터베이스 관리 유틸리티에서 나오려면 다음을 입력합니다.

```    
quit
```


## <a name="next-steps"></a>다음 단계
MySQL에 대한 자세한 내용은 [MySQL 설명서](http://dev.mysql.com/doc/index-topic.html)를 참조하세요.




