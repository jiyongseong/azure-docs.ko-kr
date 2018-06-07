---
title: Azure Machine Learning 모델 관리 웹 서비스 사용 | Microsoft Docs
description: 이 문서에서는 Azure Machine Learning 모델 관리를 통해 배포된 웹 서비스를 사용하는 것과 관련된 단계와 개념을 설명합니다.
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/06/2017
ms.openlocfilehash: 93df053bf62b5a69f74d816fc05520d040d73bd7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="consuming-web-services"></a>웹 서비스 사용
모델을 실시간 웹 서비스로 배포하면 다양한 플랫폼과 응용 프로그램에서 데이터를 보내고 예측을 가져올 수 있습니다. 실시간 웹 서비스는 예측을 가져오기 위한 REST API를 공개합니다. 여러 예측을 한 번에 가져오기 위해 웹 서비스에 데이터를 단일 또는 다중 행 형식으로 보낼 수 있습니다.

[Azure Machine Learning 웹 서비스](model-management-service-deploy.md)를 사용하면 외부 응용 프로그램에서 서비스 URL에 대한 HTTP POST 호출을 통해 예측 모델과 동기적으로 통신합니다. 웹 서비스 호출을 만들려면 클라이언트 응용 프로그램에서 예측을 배포할 때 만드는 API 키를 지정하고 요청 데이터를 POST 요청 본문에 넣어야 합니다.

API 키는 클러스터 배포 모드에서만 사용할 수 있습니다. 로컬 웹 서비스에는 키가 없습니다.

## <a name="service-deployment-options"></a>서비스 배포 옵션
Azure Machine Learning 웹 서비스는 프로덕션 및 테스트 시나리오 모두에 대해 클라우드 기반 클러스터 및 Docker 엔진을 사용하는 로컬 워크스테이션에 배포할 수 있습니다. 두 경우 모두 예측 모델의 기능을 그대로 유지합니다. 클러스터 기반 배포는 확장 가능한 고성능 Azure Container Services 기반 솔루션을 제공하고, 로컬 배포는 디버깅에 사용할 수 있습니다. 

Azure Machine Learning CLI 및 API는 ```az ml env``` 옵션을 사용하여 서비스 배포를 위한 계산 환경을 만들고 관리할 수 있는 편리한 명령을 제공합니다. 

## <a name="list-deployed-services-and-images"></a>배포된 서비스 및 이미지 나열
```az ml service list realtime -o table``` CLI 명령을 사용하여 현재 배포된 서비스와 Docker 이미지를 나열할 수 있습니다. 이 명령은 현재 계산 환경의 컨텍스트에서 항상 작동합니다. 현재로 설정되지 않은 환경에 배포되는 서비스를 표시하지 않습니다. 환경을 설정하려면 ```az ml env set```을 사용합니다. 

## <a name="get-service-information"></a>서비스 정보 가져오기
웹 서비스가 성공적으로 배포되면 다음 명령을 사용하여 서비스 끝점 호출에 대한 서비스 URL 및 기타 세부 정보를 가져옵니다. 

```
az ml service usage realtime -i <web service id>
```

배포 시 서비스 API 스키마가 제공된 경우 이 명령은 서비스 호출에 대한 서비스 URL, 필요한 요청 헤더, Swagger URL 및 샘플 데이터를 출력합니다.

입력 데이터가 포함된 샘플 CLI 명령을 입력하여 HTTP 요청을 작성하지 않고 CLI에서 서비스를 직접 테스트할 수 있습니다.

```
az ml service run realtime -i <web service id> -d "Your input data"
```

## <a name="get-the-service-api-key"></a>서비스 API 키 가져오기
웹 서비스 키를 가져오려면 다음 명령을 사용합니다.

```
az ml service keys realtime -i <web service id>
```
HTTP 요청을 만들 때 인증 헤더("Authorization": "Bearer <key>")의 키를 사용합니다.

## <a name="get-the-service-swagger-description"></a>서비스 Swagger 설명 가져오기
서비스 API 스키마가 제공된 경우 서비스 끝점은 ```http://<ip>/api/v1/service/<service name>/swagger.json```에 있는 Swagger 문서를 공개합니다. 이 Swagger 문서를 사용하여 서비스 클라이언트를 자동으로 생성하고 예상되는 입력 데이터 및 서비스에 대한 기타 세부 정보를 탐색할 수 있습니다.

## <a name="get-service-logs"></a>서비스 로그 가져오기
서비스 동작을 이해하고 문제를 진단하려면 서비스 로그를 검색하는 몇 가지 방법이 있습니다.
- CLI 명령 ```az ml service logs realtime -i <service id>```. 이 명령은 클러스터 및 로컬 모드에서 모두 작동합니다.
- 배포 시 서비스 로깅을 사용하도록 설정한 경우 서비스 로그도 AppInsight로 보내집니다. ```az ml service usage realtime -i <service id>``` CLI 명령은 AppInsight URL을 표시합니다. AppInsight 로그는 2-5분 정도 지연될 수 있습니다.
- ```az ml env set```을 사용하여 현재 클러스터 환경을 설정하면 연결된 Kubernetes 콘솔을 통해 클러스터 로그를 볼 수 있습니다.
- 서비스를 로컬로 실행하고 있으면 Docker 엔진 로그를 통해 로컬 Docker 로그를 사용할 수 있습니다.

## <a name="call-the-service-using-c"></a>C#을 사용하여 서비스 호출
서비스 URL을 사용하여 C# 콘솔 앱에서 요청을 보냅니다. 

1. Visual Studio에서 새 콘솔 앱을 만듭니다. 
    * 메뉴에서 파일 > 새로 만들기 > 프로젝트를 차례로 클릭합니다.
    * Visual Studio C#에서 [Windows 클래스 바탕 화면]을 클릭한 다음 [콘솔 앱]을 선택합니다.
2. _MyFirstService_를 프로젝트 이름으로 입력한 다음 [확인]을 클릭합니다.
3. [프로젝트 참조]에서 _System.Net_ 및 _System.Net.Http_에 대한 참조를 설정합니다.
4. 도구 -> NuGet 패키지 관리자 -> 패키지 관리자 콘솔을 차례로 클릭한 다음 Microsoft.AspNet.WebApi.Client 패키지를 설치합니다.
5. Program.cs 파일을 열고 코드를 다음 코드로 바꿉니다.
6. _SERVICE_URL_ 및 _API_KEY_ 매개 변수를 웹 서비스의 정보로 업데이트합니다.
7. 프로젝트를 실행합니다.

```csharp
using System;
using System.Collections.Generic;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MyFirstService
{
    class Program
    {
        // Web Service URL (update it with your service url)
        private const string SERVICE_URL = "http://<service ip address>:80/api/v1/service/<service name>/score";
        private const string API_KEY = "your service key";

        static void Main(string[] args)
        {
            Program.PostRequest();
            Console.ReadLine();
        }

        private static void PostRequest()
        {
            HttpClient client = new HttpClient();
            client.BaseAddress = new Uri(SERVICE_URL);
            //For local web service, comment out this line.
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", API_KEY);

            var inputJson = new List<RequestPayload>();
            RequestPayload payload = new RequestPayload();
            List<InputDf> inputDf = new List<InputDf>();
            inputDf.Add(new InputDf()
            {
                feature1 = value1,
                feature2 = value2,
            });
            payload.Input_df_list = inputDf;
            inputJson.Add(payload);

            try
            {
                var request = new HttpRequestMessage(HttpMethod.Post, string.Empty);
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;

                Console.Write(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
        public class InputDf
        {
            public double feature1 { get; set; }
            public double feature2 { get; set; }
        }
        public class RequestPayload
        {
            [JsonProperty("input_df")]
            public List<InputDf> Input_df_list { get; set; }
        }
    }
}
```

## <a name="call-the-web-service-using-python"></a>Python을 사용하여 웹 서비스 호출
Python을 사용하여 실시간 웹 서비스에 요청을 보냅니다. 

1. 새 Python 파일에 다음 코드 샘플을 복사합니다.
2. data, url 및 api_key 매개 변수를 업데이트합니다. 로컬 웹 서비스의 경우 'Authorization' 헤더를 제거합니다.
3. 코드를 실행합니다. 

```python
import requests
import json

data = "{\"input_df\": [{\"feature1\": value1, \"feature2\": value2}]}"
body = str.encode(json.dumps(data))

url = 'http://<service ip address>:80/api/v1/service/<service name>/score'
api_key = 'your service key' 
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}

resp = requests.post(url, body, headers=headers)
resp.text
```
