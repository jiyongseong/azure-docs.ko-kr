---
title: Marathon REST API를 사용하여 Azure DC/OS 클러스터 관리
description: Marathon REST API를 사용하여 컨테이너를 Azure Container Service DC/OS 클러스터에 배포합니다.
services: container-service
author: dlepow
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/04/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: edd66be25bf2571a7315372898300476fec101ca
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32165608"
---
# <a name="dcos-container-management-through-the-marathon-rest-api"></a>Marathon REST API를 통해 DC/OS 컨테이너 관리

DC/OS는 기본 하드웨어를 추상화하는 동안 클러스터형 워크로드를 배포 및 확장하기 위한 환경을 제공합니다. DC/OS의 상단에 계산 워크로드의 예약 및 실행을 관리하는 프레임워크가 있습니다. 프레임워크는 인기 많은 수많은 워크로드에 사용할 수 있지만 이 문서에서는 Marathon REST API를 사용하여 컨테이너 배포를 만들고 확장할 수 있는 방법을 설명합니다. 

## <a name="prerequisites"></a>필수 조건

이러한 예제를 통해 작업하기 전에 Azure 컨테이너 서비스에 구성된 DC/OS 클러스터가 필요합니다. 또한 이 클러스터에 원격으로 연결해야 합니다. 이러한 항목에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure 컨테이너 서비스 클러스터 배포](container-service-deployment.md)
* [Azure 컨테이너 서비스 클러스터에 연결](../container-service-connect.md)

## <a name="access-the-dcos-apis"></a>DC/OS API 액세스
Azure Container Service 클러스터에 연결한 후에 http://localhost:local-port를 통해 DC/OS 및 관련된 REST API에 액세스할 수 있습니다. 이 문서의 예제에서는 포트 80에서 터널링하는 것을 가정합니다. 예를 들어, `http://localhost/marathon/v2/`로 시작하는 URI에서 Marathon 끝점에 도달할 수 있습니다. 

다양한 API에 대한 자세한 내용은 [Marathon API](https://mesosphere.github.io/marathon/docs/rest-api.html) 및 [Chronos API](https://mesos.github.io/chronos/docs/api.html)에 대한 Mesosphere 문서와 [Mesos Scheduler API](http://mesos.apache.org/documentation/latest/scheduler-http-api/)에 대한 Apache 문서를 참조하세요.

## <a name="gather-information-from-dcos-and-marathon"></a>DC/OS 및 Marathon에서 정보 수집
DC/OS 클러스터에 컨테이너를 배포하기 전에 이름, DC/OS 에이전트의 상태와 같은 DC/OS 클러스터에 대한 정보를 수집합니다. 이렇게 하려면 DC/OS REST API에서 `master/slaves` 끝점을 쿼리합니다. 모든 작업이 제대로 진행되었다면 쿼리는 DC/OS 에이전트와 각각에 대한 여러 속성 목록을 반환합니다.

```bash
curl http://localhost/mesos/master/slaves
```

이제는 Marathon `/apps` 끝점을 사용하여 DC/OS 클러스터에 대한 현재 응용 프로그램 배포를 확인합니다. 새 클러스터인 경우에 앱에 대한 빈 배열이 표시됩니다.

```bash
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Docker로 포맷된 컨테이너 배포
Marathon REST API를 통해 원하는 배포를 설명하는 JSON 파일을 사용하여 Docker로 포맷된 컨테이너를 배포합니다. 다음 샘플은 클러스터의 개인 에이전트에 Nginx 컨테이너를 배포합니다. 

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 32.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Docker 형식 컨테이너를 배포하려면 액세스 가능한 위치에 JSON 파일을 저장합니다. 다음으로 컨테이너를 배포하려면 다음 명령을 실행합니다. JSON 파일의 이름(이 예제의 `marathon.json`)을 지정합니다.

```bash
curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

다음과 유사하게 출력됩니다.

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

이제 응용 프로그램에 대해 Marathon을 쿼리하면 이 새 응용 프로그램이 출력에 나타납니다.

```bash
curl localhost/marathon/v2/apps
```

## <a name="reach-the-container"></a>컨테이너에 연결

클러스터의 사용자 에이전트 중 하나에 있는 컨테이너에서 Nginx가 실행되고 있는지 확인할 수 있습니다. 컨테이너가 실행되고 있는 호스트 및 포트를 찾으려면 Marathon에서 실행 중인 작업을 쿼리합니다. 

```bash
curl localhost/marathon/v2/tasks
```

출력에서 `host` 값(`10.32.0.x`와 비슷한 IP 주소) 및 `ports` 값을 찾습니다.


이제 클러스터의 관리 FQDN에 대한 SSH 터미널 연결(터널링된 연결 아님)을 확인합니다. 일단 연결되면 다음 요청을 수행하고 `host` 및 `ports`에 대해 올바른 값을 입력합니다.

```bash
curl http://host:ports
```

Nginx 서버 출력은 다음과 유사합니다.

![컨테이너의 Nginx](./media/container-service-mesos-marathon-rest/nginx.png)




## <a name="scale-your-containers"></a>컨테이너 확장
Marathon API를 사용하여 응용 프로그램 배포의 규모를 확장 또는 감축할 수 있습니다. 앞의 예제에서 응용 프로그램의 인스턴스를 하나 배포했습니다. 응용 프로그램의 세 인스턴스를 확장해 보겠습니다. 이렇게 하려면 다음 JSON 텍스트를 사용하여 JSON 파일을 만들고 액세스 가능한 위치에 저장합니다.

```json
{ "instances": 3 }
```

터널링된 연결에서 응용 프로그램의 규모를 확장하려면 다음 명령을 실행합니다.

> [!NOTE]
> URI은 http://localhost/marathon/v2/apps/이고 그 다음에 크기를 조정할 응용 프로그램의 ID가 표시됩니다. 여기에 제공된 Nginx 샘플을 사용하는 경우 URI는 http://localhost/marathon/v2/apps/nginx입니다.
> 
> 

```bash
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

마지막으로, 응용 프로그램에 대해 Marathon 끝점을 쿼리합니다. 이제 세 가지 Nginx 컨테이너가 표시됩니다.

```bash
curl localhost/marathon/v2/apps
```

## <a name="equivalent-powershell-commands"></a>동일한 PowerShell 명령
Windows 시스템에서 PowerShell 명령을 사용하여 이러한 동일한 작업을 수행할 수 있습니다.

에이전트 이름 및 에이전트 상태와 같은 DC/OS 클러스터에 대한 정보를 수집하려면 다음 명령을 실행합니다.

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Marathon을 통해 원하는 배포를 설명하는 JSON 파일을 사용하여 Docker로 포맷된 컨테이너를 배포합니다. 다음 샘플은 Nginx 컨테이너를 배포하며 DC/OS 에이전트의 포트 80을 컨테이너의 포트 80에 바인딩합니다.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 32.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Docker 형식 컨테이너를 배포하려면 액세스 가능한 위치에 JSON 파일을 저장합니다. 다음으로 컨테이너를 배포하려면 다음 명령을 실행합니다. JSON 파일의 경로(이 예제의 `marathon.json`)를 지정합니다.

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

Marathon API를 사용하여 응용 프로그램 배포의 규모를 확장 또는 감축할 수도 있습니다. 앞의 예제에서 응용 프로그램의 인스턴스를 하나 배포했습니다. 응용 프로그램의 세 인스턴스를 확장해 보겠습니다. 이렇게 하려면 다음 JSON 텍스트를 사용하여 JSON 파일을 만들고 액세스 가능한 위치에 저장합니다.

```json
{ "instances": 3 }
```

응용 프로그램의 규모를 확장하려면 다음 명령을 실행합니다.

> [!NOTE]
> URI은 http://localhost/marathon/v2/apps/이고 그 다음에 크기를 조정할 응용 프로그램의 ID가 표시됩니다. 여기에 제공된 Nginx 샘플을 사용하는 경우 URI는 http://localhost/marathon/v2/apps/nginx입니다.
> 
> 

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>다음 단계
* [Mesos HTTP 끝점에 대해 자세히 알아보기](http://mesos.apache.org/documentation/latest/endpoints/)
* [Marathon REST API에 대해 자세히 알아보기](https://mesosphere.github.io/marathon/docs/rest-api.html)

