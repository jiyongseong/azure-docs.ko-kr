---
title: Azure Machine Learning을 사용하여 FPGA에 웹 서비스로 모델을 배포하는 방법
description: Azure Machine Learning을 사용하여 FPGA에서 실행되는 모델로 웹 서비스를 배포하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/07/2018
ms.openlocfilehash: f3237980a1ad1969b5cf8d42d547ddf96608dd97
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning"></a>Azure Machine Learning을 사용하여 FPGA에 웹 서비스로 모델 배포

이 문서에서는 워크스테이션 환경을 설정하고 [FPGA(Field-programmable Gate Array)](concept-accelerate-with-fpgas.md)에 웹 서비스로 모델을 배포하는 방법을 알아봅니다. 웹 서비스는 Project Brainwave를 사용하여 FPGA에서 모델을 실행합니다.

FPGA를 사용하면 단일 배치 크기로도 대기 시간이 엄청나게 짧은 추론이 가능합니다.

## <a name="create-an-azure-machine-learning-model-management-account"></a>Azure Machine Learning 모델 관리 계정 만들기

1. [Azure Portal](https://aka.ms/aml-create-mma)의 모델 관리 계정 만들기 페이지로 이동합니다.

2. 포털에서 **미국 동부 2**지역에 모델 관리 계정을 만듭니다.

   ![모델 관리 계정 만들기 화면 이미지](media/how-to-deploy-fpga-web-service/azure-portal-create-mma.PNG)

3. 모델 관리 계정에 이름을 지정하고 구독을 선택하고 리소스 그룹을 선택합니다.

   >[!IMPORTANT]
   >위치는 **미국 동부 2**를 지역으로 선택해야 합니다.  현재 다른 지역은 사용할 수 없습니다.

4. 가격 책정 계층을 선택합니다. (S1이 충분하지만 S2와 S3도 가능합니다.)  이 DevTest 계층은 지원되지 않습니다.  

5. **선택**을 클릭하여 가격 책정 계층을 확인합니다.

6. 왼쪽의 ML 모델 관리에서 **만들기**를 클릭합니다.

## <a name="get-model-management-account-information"></a>모델 관리 계정 정보 가져오기

MMA(모델 관리 계정)에 대한 정보를 얻으려면 Azure Portal에서 __모델 관리 계정__을 클릭합니다.

다음 항목의 값을 복사합니다.

+ 모델 관리 계정 이름(왼쪽 위 모서리에 있음)
+ 리소스 그룹 이름
+ 구독 ID
+ 위치("eastus2" 사용)

![모델 관리 계정 정보](media/how-to-deploy-fpga-web-service/azure-portal-mma-info.PNG)

## <a name="set-up-your-machine"></a>컴퓨터 설정

FPGA 배포용 워크스테이션을 설정하려면 다음 단계를 사용합니다.

1. 최신 버전의 [Git](https://git-scm.com/downloads)를 다운로드하여 설치합니다.

2. [Anaconda(Python 3.6)](https://conda.io/miniconda.html)를 설치합니다.

3. Anaconda 환경을 다운로드하려면 Git 프롬프트에서 아래 명령을 사용합니다.

    ```
    git clone https://aka.ms/aml-real-time-ai
    ```

4. 환경을 만들려면 **Anaconda 프롬프트**(Azure Machine Learning Workbench 프롬프트가 아님)를 열고 다음 명령을 실행합니다.

    > [!IMPORTANT]
    > `environment.yml` 파일은 이전 단계에서 복제한 Git 리포지토리에 있습니다. 워크 스테이션의 파일을 가리키도록 필요에 따라 경로를 변경합니다.

    ```
    conda env create -f environment.yml
    ```

5. 환경을 활성화하려면 아래 명령을 사용합니다.

    ```
    conda activate amlrealtimeai
    ```

6. Jupyter Notebook 서버를 시작하려면 다음 명령을 사용합니다.

    ```
    jupyter notebook
    ```

    이 명령의 출력은 다음 텍스트와 비슷합니다.

    ```text
    Copy/paste this URL into your browser when you connect for the first time, to login with a token:
        http://localhost:8888/?token=bb2ce89cc8ae931f5df50f96e3a6badfc826ff4100e78075
    ```

    > [!TIP]
    > 명령을 실행할 때마다 다른 토큰을 얻게 됩니다.

    브라우저가 Jupyter Notebook에 자동으로 열리지 않으면 이전 명령에서 반환된 HTTP URL을 사용하여 페이지를 엽니다.

    ![Jupyter Notebook 웹 페이지의 이미지](./media/how-to-deploy-fpga-web-service/jupyter-notebook.png)

## <a name="deploy-your-model"></a>모델 배포

Jupyter Notebook에서 `notebooks/resnet50` 디렉터리의 `00_QuickStart.ipynb` 노트북을 엽니다. 노트북의 지침에 따라 다음을 수행합니다.

* 서비스 정의
* 모델 배포
* 배포된 모델 사용
* 배포된 서비스 삭제

> [!IMPORTANT]
> 대기 시간과 처리량을 최적화하려면 워크스테이션이 엔드포인트와 동일한 Azure 지역에 있어야 합니다.  현재 API는 미국 동부 Azure 지역에 만들어졌습니다.

## <a name="ssltls-and-authentication"></a>SSL/TLS 및 인증

Azure Machine Learning은 SSL 지원 및 키 기반 인증을 제공합니다. 따라서 서비스에 대한 액세스를 제한할 수 있고 클라이언트가 제출한 데이터를 보호할 수 있습니다.

> [!NOTE]
> 이 섹션의 단계는 Azure Machine Learning 하드웨어 가속 모델에만 적용됩니다. 표준 Azure Machine Learning 서비스는 [Azure Machine Learning Compute에서 SSL을 설정하는 방법](https://docs.microsoft.com/azure/machine-learning/preview/how-to-setup-ssl-on-mlc) 문서를 참조하세요.

> [!IMPORTANT]
> 인증은 SSL 인증서와 키를 제공한 서비스에서만 사용할 수 있습니다. 
>
> SSL을 사용하지 않으면 인터넷상의 모든 사용자가 서비스를 호출할 수 있습니다.
>
> SSL을 사용하면 서비스에 액세스할 때 인증 키가 필요합니다.

SSL은 클라이언트와 서비스 간에 전송되는 데이터를 암호화합니다. 클라이언트가 서버의 ID를 확인하는 데도 사용됩니다.

SSL 사용 서비스를 배포하거나 이미 배포된 서비스를 SSL을 사용하도록 업데이트할 수 있습니다. 단계는 동일합니다.

1. 도메인 이름을 확보합니다.

2. SSL 인증서를 확보합니다.

3. SSL 사용 서비스를 배포하거나 업데이트합니다.

4. 서비스를 가리키도록 DNS를 업데이트합니다.

### <a name="acquire-a-domain-name"></a>도메인 이름 확보

도메인 이름을 소유하지 않은 경우 __도메인 이름 등록 기관__에서 구입할 수 있습니다. 프로세스는 등록 기관마다 다르며 비용도 그렇습니다. 등록 기관은 도메인 이름을 관리하는 도구도 제공합니다. 이러한 도구는 정규화된 도메인 이름(예: www.contoso.com)을 서비스가 호스팅되는 IP 주소에 매핑하는 데 사용됩니다.

### <a name="acquire-an-ssl-certificate"></a>SSL 인증서 확보

SSL 인증서를 확보하는 방법은 여러 가지입니다. 가장 일반적인 방법은 CA(__인증 기관__)에서 구입하는 것입니다. 인증서를 어디에서 확보하든 다음과 같은 파일이 필요합니다.

* __인증서__. 인증서는 전체 인증서 체인을 포함해야 하며 PEM으로 인코딩되어야 합니다.
* __키__. 키는 PEM으로 인코딩되어야 합니다.

> [!TIP]
> 인증 기관이 인증서와 키를 PEM으로 인코딩된 파일로 제공할 수 없는 경우 [OpenSSL](https://www.openssl.org/)과 같은 유틸리티를 사용하여 형식을 변경할 수 있습니다.

> [!IMPORTANT]
> 자체 서명된 인증서는 개발에만 사용해야 합니다. 프로덕션에 사용해서는 안됩니다.
>
> 자체 서명된 인증서를 사용하는 경우 [자체 서명된 인증서로 서비스 소비](#self-signed) 섹션에서 구체적인 지침을 참조하세요.

> [!WARNING]
> 인증서를 요청할 때는 서비스에 사용할 주소의 FQDN(정규화된 도메인 이름)을 제공해야 합니다. 예: www.contoso.com. 인증서에 스탬프가 지정된 주소와 클라이언트에서 사용되는 주소는 서비스의 ID를 확인할 때 비교됩니다.
>
> 주소가 일치하지 않으면 클라이언트로 오류가 전송됩니다. 

### <a name="deploy-or-update-the-service-with-ssl-enabled"></a>SSL 사용 서비스를 배포하거나 업데이트

SSL 사용 서비스를 배포하려면 `ssl_enabled` 매개 변수를 `True`로 설정합니다. `ssl_certificate` 매개 변수를 __인증서__ 파일의 값으로 설정하고 `ssl_key`를 __키__ 파일의 값으로 설정합니다. 다음 예제는 SSL 사용 서비스를 배포하는 방법을 보여줍니다.

```python
from amlrealtimeai import DeploymentClient

subscription_id = "<Your Azure Subscription ID>"
resource_group = "<Your Azure Resource Group Name>"
model_management_account = "<Your AzureML Model Management Account Name>"
location = "eastus2"

model_name = "resnet50-model"
service_name = "quickstart-service"

deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account, location)

with open('cert.pem','r') as cert_file:
    with open('key.pem','r') as key_file:
        cert = cert_file.read()
        key = key_file.read()
        service = deployment_client.create_service(service_name, model_id, ssl_enabled=True, ssl_certificate=cert, ssl_key=key)
```

`create_service` 작업의 응답에는 서비스의 IP 주소가 포함됩니다. IP 주소는 DNS 이름을 서비스의 IP 주소에 매핑할 때 사용됩니다.

응답에는 서비스를 소비하는 데 사용되는 __기본 키__와 __보조 키__도 포함됩니다.

### <a name="update-your-dns-to-point-to-the-service"></a>서비스를 가리키도록 DNS 업데이트

도메인 이름 등록 기관이 제공하는 도구를 사용하여 도메인 이름의 DNS 레코드를 업데이트합니다. 레코드는 서비스의 IP 주소를 가리켜야 합니다.

> [!NOTE]
> 등록 기관 및 도메인 이름에 대해 구성된 TTL(Time To Live)에 따라 클라이언트가 도메인 이름을 확인하는 데 몇 분에서 몇 시간이 걸릴 수 있습니다.

### <a name="consuming-authenticated-services"></a>인증된 서비스 사용

다음 예제는 Python과 C#을 사용하여 인증된 서비스를 사용하는 방법을 보여줍니다.

> [!NOTE]
> `authkey`를 서비스를 만들 때 반환된 기본 키 또는 보조 키로 바꿉니다.

```python
from amlrealtimeai import PredictionClient
client = PredictionClient(service.ipAddress, service.port, use_ssl=True, access_token="authKey")
image_file = R'C:\path_to_file\image.jpg'
results = client.score_image(image_file)
```

```csharp
var client = new ScoringClient(host, 50051, useSSL, "authKey");
float[,] result;
using (var content = File.OpenRead(image))
    {
        IScoringRequest request = new ImageRequest(content);
        result = client.Score<float[,]>(request);
    }
```

다른 gRPC 클라이언트는 인증 헤더를 설정하여 요청을 인증할 수 있습니다. 일반적인 방법은 `SslCredentials`와 `CallCredentials`를 결합하는 `ChannelCredentials` 개체를 생성하는 것입니다. 이것은 요청의 인증 헤더에 추가됩니다. 특정 헤더에 대한 지원 구현에 대한 자세한 내용은 [https://grpc.io/docs/guides/auth.html](https://grpc.io/docs/guides/auth.html)을 참조하세요.

다음 예제에서는 C# 및 Go에서 헤더를 설정하는 방법을 보여줍니다.

```csharp
creds = ChannelCredentials.Create(baseCreds, CallCredentials.FromInterceptor(
                      async (context, metadata) =>
                      {
                          metadata.Add(new Metadata.Entry("authorization", "authKey"));
                          await Task.CompletedTask;
                      }));

```

```go
conn, err := grpc.Dial(serverAddr, 
    grpc.WithTransportCredentials(credentials.NewClientTLSFromCert(nil, "")),
    grpc.WithPerRPCCredentials(&authCreds{
    Key: "authKey"}))

type authCreds struct {
    Key string
}

func (c *authCreds) GetRequestMetadata(context.Context, uri ...string) (map[string]string, error) {
    return map[string]string{
        "authorization": c.Key,
    }, nil
}

func (c *authCreds) RequireTransportSecurity() bool {
    return true
}
```

### <a id="self-signed"></a>자체 서명된 인증서로 서비스 사용

클라이언트가 자체 서명된 인증서로 보안이 설정된 서버를 인증할 수 있게 설정하는 방법은 두 가지입니다.

* 클라이언트 시스템에서, 클라이언트 시스템의 `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH` 환경 변수가 인증서 파일을 가리키도록 설정합니다.

* `SslCredentials` 개체를 생성할 때 인증서 파일의 콘텐츠를 생성자에게 전달합니다.

두 가지 방법 중 하나를 사용하면 gRPC가 인증서를 루트 인증서로 사용하게 됩니다.

> [!IMPORTANT]
> gRPC는 신뢰할 수 없는 인증서를 수락하지 않습니다. 신뢰할 수 없는 인증서를 사용하면 `Unavailable` 상태 코드가 발생하면서 실패합니다. 실패의 세부 정보에는 `Connection Failed`가 포함됩니다.
