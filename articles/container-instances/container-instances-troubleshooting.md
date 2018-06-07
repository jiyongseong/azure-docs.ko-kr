---
title: Azure Container Instances 문제 해결
description: Azure Container Instances 관련 문제 해결 방법 알아보기
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/14/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: a4067db9955b804f126e889fa73641f69fef56ab
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="troubleshoot-container-and-deployment-issues-in-azure-container-instances"></a>Azure Container Instances의 컨테이너 및 배포 문제 해결

이 문서에는 컨테이너를 Azure Container Instances로 배포하는 경우 문제를 해결하는 방법을 보여줄 뿐만 아니라, 발생할 수 있는 일반적인 문제 중 일부를 설명합니다.

## <a name="view-logs-and-stream-output"></a>로그 보기 및 출력 스트리밍

오동작 컨테이너를 사용하는 경우 [az container logs][az-container-logs]에서 해당 로그를 확인하고 [az container attach][az-container-attach]에서 표준 출력 및 표준 오류를 스트리밍하기 시작합니다.

### <a name="view-logs"></a>로그 보기

컨테이너 내에서 응용 프로그램 코드에서 로그를 보려면 [az container logs][az-container-logs] 명령을 사용할 수 있습니다.

처리할 잘못된 URL을 입력한 후에 [ACI에서 컨테이너화된 작업 실행](container-instances-restart-policy.md)에서 예제 작업 기반 컨테이너의 로그 출력은 다음과 같습니다.

```console
$ az container logs --resource-group myResourceGroup --name mycontainer
Traceback (most recent call last):
  File "wordcount.py", line 11, in <module>
    urllib.request.urlretrieve (sys.argv[1], "foo.txt")
  File "/usr/local/lib/python3.6/urllib/request.py", line 248, in urlretrieve
    with contextlib.closing(urlopen(url, data)) as fp:
  File "/usr/local/lib/python3.6/urllib/request.py", line 223, in urlopen
    return opener.open(url, data, timeout)
  File "/usr/local/lib/python3.6/urllib/request.py", line 532, in open
    response = meth(req, response)
  File "/usr/local/lib/python3.6/urllib/request.py", line 642, in http_response
    'http', request, response, code, msg, hdrs)
  File "/usr/local/lib/python3.6/urllib/request.py", line 570, in error
    return self._call_chain(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 504, in _call_chain
    result = func(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 650, in http_error_default
    raise HTTPError(req.full_url, code, msg, hdrs, fp)
urllib.error.HTTPError: HTTP Error 404: Not Found
```

### <a name="attach-output-streams"></a>출력 스트림 연결

[az container attach][az-container-attach] 명령은 컨테이너를 시작하는 동안 진단 정보를 제공합니다. 컨테이너가 시작되면 로컬 콘솔에 STDOUT 및 STDERR을 스트리밍합니다.

예를 들어 처리할 대규모 텍스트 파일의 잘못된 URL을 입력한 후에 [ACI에서 컨테이너화된 작업 실행](container-instances-restart-policy.md)에서 작업 기반 컨테이너의 로그 출력은 다음과 같습니다.

```console
$ az container attach --resource-group myResourceGroup --name mycontainer
Container 'mycontainer' is in state 'Unknown'...
Container 'mycontainer' is in state 'Waiting'...
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2018-03-09 23:21:33+00:00) pulling image "microsoft/aci-wordcount:latest"
(count: 1) (last timestamp: 2018-03-09 23:21:49+00:00) Successfully pulled image "microsoft/aci-wordcount:latest"
(count: 1) (last timestamp: 2018-03-09 23:21:49+00:00) Created container with id e495ad3e411f0570e1fd37c1e73b0e0962f185aa8a7c982ebd410ad63d238618
(count: 1) (last timestamp: 2018-03-09 23:21:49+00:00) Started container with id e495ad3e411f0570e1fd37c1e73b0e0962f185aa8a7c982ebd410ad63d238618

Start streaming logs:
[('the', 22979),
 ('I', 20003),
 ('and', 18373),
 ('to', 15651),
 ('of', 15558),
 ('a', 12500),
 ('you', 11818),
 ('my', 10651),
 ('in', 9707),
 ('is', 8195)]
```

## <a name="get-diagnostic-events"></a>진단 이벤트 가져오기

컨테이너가 성공적으로 배포되지 않을 경우 Azure Container Instances 리소스 공급자가 제공하는 진단 정보를 검토해야 합니다. 컨테이너에 대한 이벤트를 보려면 [az container show][az-container-show] 명령을 실행합니다.

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

출력에는 배포 이벤트와 함께 컨테이너의 핵심 속성이 포함되어 있습니다(여기에서 잘려서 표시됨).

```JSON
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "microsoft/aci-helloworld",
      ...
        "events": [
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:49+00:00",
            "lastTimestamp": "2017-12-21T22:50:49+00:00",
            "message": "pulling image \"microsoft/aci-helloworld\"",
            "name": "Pulling",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Successfully pulled image \"microsoft/aci-helloworld\"",
            "name": "Pulled",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Created container with id 2677c7fd54478e5adf6f07e48fb71357d9d18bccebd4a91486113da7b863f91f",
            "name": "Created",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Started container with id 2677c7fd54478e5adf6f07e48fb71357d9d18bccebd4a91486113da7b863f91f",
            "name": "Started",
            "type": "Normal"
          }
        ],
        "previousState": null,
        "restartCount": 0
      },
      "name": "mycontainer",
      "ports": [
        {
          "port": 80,
          "protocol": null
        }
      ],
      ...
    }
  ],
  ...
}
```

## <a name="common-deployment-issues"></a>일반 배포 문제

다음 섹션에서는 컨테이너 배포에서 대부분의 오류를 발생시키는 일반적인 문제를 설명합니다.

* [지원되지 않는 이미지 버전](#image-version-not-supported)
* [이미지를 풀링할 수 없음](#unable-to-pull-image)
* [컨테이너가 지속적으로 종료 후 다시 시작함](#container-continually-exits-and-restarts)
* [컨테이너는 시작하는 데 오래 걸림](#container-takes-a-long-time-to-start)
* ["리소스 사용할 수 없음" 오류](#resource-not-available-error)

## <a name="image-version-not-supported"></a>지원되지 않는 이미지 버전

Azure Container Instances에서 지원할 수 없는 이미지를 지정하면 `ImageVersionNotSupported` 오류가 반환됩니다. 오류 값이 `The version of image '{0}' is not supported.`이면 현재 Windows 1709 이미지에 적용됩니다. 이 문제를 해결하려면 LTS Windows 이미지를 사용합니다. Windows 1709 이미지에 대한 지원은 준비 중입니다.

## <a name="unable-to-pull-image"></a>이미지를 풀링할 수 없음

Azure Container Instances가 초기에 이미지를 풀링할 수 없는 경우 결과적으로 실패하기 전까지 당분간 재시도합니다. 이미지를 풀링할 수 없는 경우에는 다음과 같은 이미지가 [az container show][az-container-show]의 출력에 표시됩니다

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"microsoft/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"microsoft/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"microsoft/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```

해결하려면 해당 컨테이너를 삭제하고 이미지 이름을 정확하게 입력했는 세심하게 주의하면서 배포를 다시 시도하세요.

## <a name="container-continually-exits-and-restarts"></a>컨테이너가 지속적으로 종료 후 다시 시작함

컨테이너가 완료되었다가 자동으로 다시 시작되면 [다시 시작 정책](container-instances-restart-policy.md)을 **OnFailure** 또는 **Never**로 설정해야 합니다. **OnFailure**를 지정해도 컨테이너가 계속 다시 시작되면 컨테이너에서 실행된 응용 프로그램이나 스크립트에 문제가 있을 수 있습니다.

컨테이너 인스턴스 API는 `restartCount` 속성을 포함합니다. 컨테이너에 대한 다시 시작 횟수를 확인하기 위해 Azure CLI 2.0에서 [az container show][az-container-show] 명령을 사용할 수 있습니다. 다음 예제 출력(간단히 하기 위해 일부 자름)에서는 출력 마지막에 `restartCount` 속성이 있습니다.

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> Linux 배포판에 대한 대부분 컨테이너 이미지는 기본 명령으로 Bash와 같은 셸을 설정합니다. 자체 셸은 장기 실행 서비스이므로, 기본값 **Always** 다시 시작 정책이 구성되었을 때 이러한 컨테이너는 즉시 종료된 후 다시 시작 루프를 시작합니다.

## <a name="container-takes-a-long-time-to-start"></a>컨테이너는 시작하는 데 오래 걸림

Azure Container Instances에서 컨테이너 시작 시간에 영향을 주는 두 가지 주요 요인은 다음과 같습니다.

* [이미지 크기](#image-size)
* [이미지 위치](#image-location)

Windows 이미지에는 [추가 고려 사항](#use-recent-windows-images)이 있습니다.

### <a name="image-size"></a>이미지 크기

컨테이너가 시작하는 데 오래 걸리지만, 결과적으로 성공할 경우 먼저 컨테이너 이미지의 크기를 살펴 보세요. Azure Container Instances가 요청 시 컨테이너 이미지를 풀링하므로, 경험하는 시작 시간은 크기와 직접 관련이 있습니다.

Docker CLI에서 `docker images` 명령을 사용하여 컨테이너 이미지의 크기를 볼 수 있습니다.

```console
$ docker images
REPOSITORY                  TAG       IMAGE ID        CREATED        SIZE
microsoft/aci-helloworld    latest    7f78509b568e    13 days ago    68.1MB
```

이미지 크기를 작게 유지하는 핵심 요소는 최종 이미지에 런타임 시 필요하지 않은 것은 아무 것도 포함하지 않는 것입니다. 이 작업을 수행하는 한 가지 방법은 [다단계 빌드][docker-multi-stage-builds]를 사용하는 것입니다. 다단계 빌드를 통해 간편하게 최종 이미지에 빌드 시간에 필요한 추가 콘텐츠가 아닌 응용 프로그램에 필요한 아티팩트만 포함하도록 할 수 있습니다.

### <a name="image-location"></a>이미지 위치

컨테이너 시작 시간에 이미지 풀의 영향을 줄이는 다른 방법은 Container Instances를 배포하려는 곳과 동일한 지역의 [Azure Container Registry](/azure/container-registry/)에서 컨테이너 이미지를 호스팅하는 것입니다. 이를 통해 컨테이너 이미지가 이동해야 하는 네트워크 경를 단축하여 다운로드 시간을 크게 줄여 줍니다.

### <a name="use-recent-windows-images"></a>최근 Windows 이미지 사용

Azure Container Instances는 특정 Windows 이미지를 기반으로 하는 이미지에서 컨테이너 시작 시간을 신속하게 수행할 수 있는 캐싱 메커니즘을 사용합니다.

Windows 컨테이너 시작 시간을 단축하려면 다음과 같은 **두 개의 이미지**에서 **세 개의 최근** 버전 중 하나를 기본 이미지로 사용합니다.

* [Windows Server 2016][docker-hub-windows-core](LTS만 해당)
* [Windows Server 2016 Nano Server][docker-hub-windows-nano]

## <a name="resource-not-available-error"></a>리소스 사용할 수 없음 오류

Azure에서 다양한 지역별 리소스 부하로 인해 컨테이너 인스턴스 배포를 시도할 때 다음 오류가 발생할 수 있습니다.

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

이 오류는 배포하려는 지역에서 과부하로 인해 컨테이너에 대해 지정된 리소스를 해당 시간에 할당할 수 없음을 나타냅니다. 다음 위험 완화 단계 중 하나 이상을 사용하여 문제를 해결합니다.

* [Azure Container Instances에 대한 할당량 및 지역 가용성](container-instances-quotas.md#region-availability)에 정의된 매개 변수 내에 컨테이너 배포 설정이 속하는지 확인합니다.
* 컨테이너에 대해 낮은 CPU 및 메모리 설정 지정
* 다른 Azure 지역에 배포
* 나중에 배포

<!-- LINKS - External -->
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/r/microsoft/windowsservercore/
[docker-hub-windows-nano]: https://hub.docker.com/r/microsoft/nanoserver/

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az_container_attach
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show