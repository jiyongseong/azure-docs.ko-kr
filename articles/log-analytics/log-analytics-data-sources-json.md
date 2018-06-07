---
title: "OMS Log Analytics에서 사용자 지정 JSON 데이터 수집 | Microsoft Docs"
description: "Linux용 OMS 에이전트를 사용하여 Log Analytics로 사용자 지정 JSON 데이터 원본을 수집할 수 있습니다.  이러한 사용자 지정 데이터 원본은 curl 또는 FluentD의 300+ 플러그 인의 하나와 같은 JSON을 반환하는 간단한 스크립트일 수 있습니다. 이 문서에서는 이 데이터 수집에 필요한 구성을 설명합니다."
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.openlocfilehash: 800ee1269556e7c2d56fbbf2b497c10509b5c78c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="collecting-custom-json-data-sources-with-the-oms-agent-for-linux-in-log-analytics"></a>Log Analytics에서 Linux용 OMS 에이전트를 사용하여 사용자 지정 JSON 데이터 원본 수집
Linux용 OMS 에이전트를 사용하여 Log Analytics로 사용자 지정 JSON 데이터 원본을 수집할 수 있습니다.  이러한 사용자 지정 데이터 원본은 [curl](https://curl.haxx.se/) 또는 [FluentD의 300+ 플러그 인](http://www.fluentd.org/plugins/all)의 하나와 같은 JSON을 반환하는 간단한 스크립트일 수 있습니다. 이 문서에서는 이 데이터 수집에 필요한 구성을 설명합니다.

> [!NOTE]
> 사용자 지정 JSON 데이터에 Linux용 OMS 에이전트 v1.1.0-217+가 필요합니다.

## <a name="configuration"></a>구성

### <a name="configure-input-plugin"></a>입력 플러그 인 구성

Log Analytics에서 JSON 데이터를 수집하려면 `oms.api.`를 입력 플러그 인에서 FluentD 태그의 시작 부분에 추가합니다.

예를 들어 다음은 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`에서 별도 구성 파일 `exec-json.conf`입니다.  FluentD 플러그 인 `exec`를 사용하여 30초마다 curl 명령을 실행합니다.  이 명령의 출력은 JSON 출력 플러그 인을 통해 수집됩니다.

```
<source>
  type exec
  command 'curl localhost/json.output'
  format json
  tag oms.api.httpresponse
  run_interval 30s
</source>

<match oms.api.httpresponse>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api_httpresponse*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```
`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` 아래에 추가된 구성 파일은 다음 명령을 사용하여 변경된 소유권을 필요로 합니다.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>출력 플러그 인 구성 
다음 출력 플러그 인 구성을 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`의 기본 구성에 추가하거나 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`에 배치된 별도 구성 파일로 추가합니다.

```
<match oms.api.**>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

### <a name="restart-oms-agent-for-linux"></a>Linux용 OMS 에이전트 다시 시작
다음 명령을 사용하여 Linux용 OMS 에이전트 서비스를 다시 시작합니다.

    sudo /opt/microsoft/omsagent/bin/service_control restart 

## <a name="output"></a>출력
`<FLUENTD_TAG>_CL`의 레코드 형식으로 Log Analytics에 데이터가 수집됩니다.

예를 들어 `tomcat_CL`의 레코드 형식으로 Log Analytics에 사용자 지정 태그 `tag oms.api.tomcat`이 수집됩니다.  다음 로그 검색으로 이 형식의 모든 레코드를 검색할 수 있습니다.

    Type=tomcat_CL

중첩된 JSON 데이터 원본이 지원되지만 부모 필드를 기반으로 인덱싱됩니다. 예를 들어 다음 JSON 데이터가 `tag_s : "[{ "a":"1", "b":"2" }]`로 Log Analytics 검색에서 반환됩니다.

```
{
    "tag": [{
        "a":"1",
        "b":"2"
    }]
}
```


## <a name="next-steps"></a>다음 단계
* 데이터 원본 및 솔루션에서 수집한 데이터를 분석하기 위해 [로그 검색](log-analytics-log-searches.md) 에 대해 알아봅니다. 
 