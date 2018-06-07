---
title: Linux의 Azure App Service에 대한 SSH 지원 | Microsoft Docs
description: Linux의 Azure App Service에서 SSH 사용에 대해 자세히 알아봅니다.
keywords: azure app service, 웹앱, linux, oss
services: app-service
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: wesmc
ms.openlocfilehash: c2beb67a27b667d31402b903f38dbf116e9425d0
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301078"
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>Linux의 Azure App Service에 대한 SSH 지원

[SSH(Secure Shell)](https://wikipedia.org/wiki/Secure_Shell)는 주로 명령줄 터미널에서 원격으로 관리 명령을 실행하는 데 사용합니다. Linux의 App Service는 새 웹앱의 런타임 스택에 사용되는 기본 제공 Docker 이미지가 있는 앱 컨테이너에 SSH 지원을 제공합니다. 

![런타임 스택](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

사용자 지정 Docker 이미지의 경우 사용자 지정 이미지에서 SSH 서버를 구성하여 지원합니다.

SSH 및 SFTP를 사용하여 로컬 개발 컴퓨터에서 직접 컨테이너에 연결할 수도 있습니다.

## <a name="open-ssh-session-in-browser"></a>브라우저에서 SSH 세션 열기

컨테이너와 SSH 클라이언트를 연결하려면 응용 프로그램을 실행해야 합니다.

브라우저에 다음 URL을 붙여넣고 앱 이름으로 \<app_name>을 바꿉니다.

```
https://<app_name>.scm.azurewebsites.net/webssh/host
```

아직 인증을 받지 못한 경우 연결하기 위해서는 Azure 구독에서 인증을 받아야 합니다. 인증되면 컨테이너 내부에서 명령을 실행할 수 있는 브라우저 내부 셸을 확인합니다.

![SSH 연결](./media/app-service-linux-ssh-support/app-service-linux-ssh-connection.png)

## <a name="use-ssh-support-with-custom-docker-images"></a>사용자 지정 Docker 이미지를 사용한 SSH 지원 사용

사용자 지정 Docker 이미지로 Azure Portal에서 컨테이너 및 클라이언트 간 SSH 통신을 지원하려면 Docker 이미지에 대해 다음 단계를 수행합니다.

이 단계는 Azure App Service 리포지토리에 [예제](https://github.com/Azure-App-Service/node/blob/master/6.9.3/)로 나와 있습니다.

1. 이미지에 대한 Dockerfile의 [`RUN` 명령](https://docs.docker.com/engine/reference/builder/#run)에 `openssh-server` 설치를 포함하고 루트 계정에 대한 암호를 `"Docker!"`로 설정합니다.

    > [!NOTE]
    > 이 구성을 사용하면 컨테이너에 대한 외부 연결이 허용되지 않습니다. SSH는 게시 자격 증명을 사용하여 인증되는 Kudu/SCM 사이트를 통해서만 액세스할 수 있습니다.

    ```docker
    # ------------------------
    # SSH Server support
    # ------------------------
    RUN apt-get update \
        && apt-get install -y --no-install-recommends openssh-server \
        && echo "root:Docker!" | chpasswd
    ```

1. [`COPY` 명령](https://docs.docker.com/engine/reference/builder/#copy)을 Dockerfile에 추가하여 [sshd_config](http://man.openbsd.org/sshd_config) 파일을 */etc/ssh/* 디렉터리에 복사합니다. 구성 파일은 Azure-App-Service GitHub 리포지토리([여기](https://github.com/Azure-App-Service/node/blob/master/8.2.1/sshd_config) 참조)의 sshd_config 파일을 기준으로 해야 합니다.

    > [!NOTE]
    > *sshd_config* 파일에 다음이 포함되어야 하며 이러한 항목이 없으면 연결이 실패합니다. 
    > * `Ciphers`에는 다음 중 하나 이상이 포함되어야 합니다. `aes128-cbc,3des-cbc,aes256-cbc`
    > * `MACs`에는 다음 중 하나 이상이 포함되어야 합니다. `hmac-sha1,hmac-sha1-96`

    ```docker
    COPY sshd_config /etc/ssh/
    ```

1. Dockerfile에 대한 [`EXPOSE` 명령](https://docs.docker.com/engine/reference/builder/#expose)에 포트 2222를 포함합니다. 루트 암호를 알고 있더라도 인터넷에서 포트 2222에 액세스할 수 없습니다. 개인 가상 네트워크의 브리지 네트워크 내에 있는 컨테이너에서만 액세스할 수 있는 내부 전용 포트입니다.

    ```docker
    EXPOSE 2222 80
    ```

1. 셸 스크립트를 사용하여 SSH 서비스를 시작해야 합니다([init_container.sh](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh)의 예제 참조).

    ```bash
    #!/bin/bash
    service ssh start
    ```

Dockerfile은 [`ENTRYPOINT` 명령](https://docs.docker.com/engine/reference/builder/#entrypoint)을 사용하여 스크립트를 실행합니다.

    ```docker
    COPY startup /opt/startup
    ...
    RUN chmod 755 /opt/startup/init_container.sh
    ...
    ENTRYPOINT ["/opt/startup/init_container.sh"]
    ```

## <a name="open-ssh-session-from-remote-shell"></a>원격 셸에서 SSH 세션 열기

> [!NOTE]
> 이 기능은 현재 미리 보기로 제공됩니다.
>

TCP 터널링을 사용하여 인증된 WebSocket 연결을 통해 개발 컴퓨터와 Web App for Containers 간의 네트워크 연결을 만들 수 있습니다. 선택한 클라이언트의 App Service에서 실행 중인 컨테이너를 사용하여 SSH 세션을 열 수 있습니다.

시작하려면 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)를 설치해야 합니다. Azure CLI를 설치하지 않고 작동 방식을 확인하려면 [Azure Cloud Shell](../../cloud-shell/overview.md)을 엽니다. 

[az 확장 추가](/cli/azure/extension?view=azure-cli-latest#az-extension-add)를 실행하여 최신 App Service 확장을 추가합니다.

```azurecli-interactive
az extension add -–name webapp
```

이미 `az extension add`을 실행한 경우 대신 [az 확장 업데이트](/cli/azure/extension?view=azure-cli-latest#az-extension-update)을 실행합니다.

```azurecli-interactive
az extension update -–name webapp
```

[az webapp 원격 연결 만들기](/cli/azure/ext/webapp/webapp/remote-connection?view=azure-cli-latest#ext-webapp-az-webapp-remote-connection-create) 명령을 사용하여 앱에 원격 연결을 엽니다. 앱에 대한 _\<그룹\_이름>_ 및 \_<앱\_이름>_을 지정하고 로컬 포트 번호로 \<포트>를 바꿉니다.

```azurecli-interactive
az webapp remote-connection create --resource-group <group_name> -n <app_name> -p <port> &
```

> [!TIP]
> 명령의 끝에 `&`은 Cloud Shell을 사용하는 경우 단지 편의를 위해 제공됩니다. 동일한 셸에서 다음 명령을 실행할 수 있도록 백그라운드에서 프로세스를 실행합니다.

명령 출력은 SSH 세션을 여는 데 필요한 정보를 제공합니다.

```
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

로컬 포트를 사용하여 사용자가 선택한 클라이언트를 통해 컨테이너가 있는 SSH 세션을 엽니다. 다음 예제에서는 기본 [ssh](https://ss64.com/bash/ssh.html) 명령을 사용합니다.

```azurecli-interactive
ssh root@127.0.0.1 -p <port>
```

메시지가 표시되면 `yes`을 입력하여 연결을 계속합니다. 그러면 암호를 입력하라는 메시지가 나타납니다. 앞부분에 표시된 `Docker!`을 사용합니다.

```
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
```

인증되면 세션 시작 화면이 표시되어야 합니다.

```
  _____
  /  _  \ __________ _________   ____
 /  /_\  \___   /  |  \_  __ \_/ __ \
/    |    \/    /|  |  /|  | \/\  ___/
\____|__  /_____ \____/ |__|    \___  >
        \/      \/                  \/
A P P   S E R V I C E   O N   L I N U X

0e690efa93e2:~#
```

이제 커넥터에 연결되어 있습니다. 

[top](https://ss64.com/bash/top.html) 명령을 다시 실행합니다. 프로세스 목록에서 앱의 프로세스를 확인할 수 있어야 합니다. 아래 예제 출력에서는 `PID 263`이 있는 출력입니다.

```
Mem: 1578756K used, 127032K free, 8744K shrd, 201592K buff, 341348K cached
CPU:   3% usr   3% sys   0% nic  92% idle   0% io   0% irq   0% sirq
Load average: 0.07 0.04 0.08 4/765 45738
  PID  PPID USER     STAT   VSZ %VSZ CPU %CPU COMMAND
    1     0 root     S     1528   0%   0   0% /sbin/init
  235     1 root     S     632m  38%   0   0% PM2 v2.10.3: God Daemon (/root/.pm2)
  263   235 root     S     630m  38%   0   0% node /home/site/wwwroot/app.js
  482   291 root     S     7368   0%   0   0% sshd: root@pts/0
45513   291 root     S     7356   0%   0   0% sshd: root@pts/1
  291     1 root     S     7324   0%   0   0% /usr/sbin/sshd
  490   482 root     S     1540   0%   0   0% -ash
45539 45513 root     S     1540   0%   0   0% -ash
45678 45539 root     R     1536   0%   0   0% top
45733     1 root     Z        0   0%   0   0% [init]
45734     1 root     Z        0   0%   0   0% [init]
45735     1 root     Z        0   0%   0   0% [init]
45736     1 root     Z        0   0%   0   0% [init]
45737     1 root     Z        0   0%   0   0% [init]
45738     1 root     Z        0   0%   0   0% [init]
```

## <a name="next-steps"></a>다음 단계

[Azure 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)에 질문 및 문제를 게시할 수 있습니다.

컨테이너용 웹앱에 대한 자세한 내용은 다음을 참조하세요.

* [VS Code의 Azure App Service에서 Node.js 앱의 원격 디버깅 소개](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)
* [Web App for Containers에 사용자 지정 Docker 이미지를 사용하는 방법](quickstart-docker-go.md)
* [Linux의 Azure App Service에서 .NET Core 사용](quickstart-dotnetcore.md)
* [Linux의 Azure App Service에서 Ruby 사용](quickstart-ruby.md)
* [Containers용 Azure App Service Web App 관련 FAQ](app-service-linux-faq.md)