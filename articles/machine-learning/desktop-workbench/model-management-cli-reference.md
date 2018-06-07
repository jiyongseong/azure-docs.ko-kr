---
title: Azure Machine Learning 모델 관리 명령줄 인터페이스 참조 | Microsoft Docs
description: Azure Machine Learning 모델 관리 명령줄 인터페이스 참조 문서입니다.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 11/08/2017
ms.openlocfilehash: 27361c5b92a8748a026d457875fadfc1f3529076
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="model-management-command-line-interface-reference"></a>모델 관리 명령줄 인터페이스 참조

## <a name="base-cli-concepts"></a>기본 CLI 개념

    account : Manage model management accounts. 
    env     : Manage compute environments.
    image   : Manage operationalization images.
    manifest: Manage operationalization manifests.
    model   : Manage operationalization models.
    service : Manage operationalized services.

## <a name="account-commands"></a>계정 명령
모델 관리 계정은 모델을 배포하고 관리할 수 있는 서비스를 사용하는 데 필요합니다. 다음 목록을 표시하려면 `az ml account modelmanagement -h`를 사용합니다.

    create: Create a Model Management Account.
    delete: Delete a specified Model Management Account.
    list  : Gets the Model Management Accounts in the current subscriptiong.
    set   : Set the active Model Management Account.
    show  : Show a Model Management Account.
    update: Update an existing Model Management Account.

**모델 관리 계정 만들기**

다음 명령을 사용하여 청구용 모델 관리 계정을 만듭니다.

`az ml account modelmanagement create --location [Azure region e.g. eastus2] --name [new account name] --resource-group [resource group name to store the account in]`

로컬 인수:

    --location -l       [Required]: Resource location.
    --name -n           [Required]: Name of the model management account.
    --resource-group -g [Required]: Resource group to create the model management account in.
    --description -d              : Description of the model management account.
    --sku-instances               : Number of instances of the selected SKU. Must be between 1 and
                                    16 inclusive.  Default: 1.
    --sku-name                    : SKU name. Valid names are S1|S2|S3|DevTest.  Default: S1.
    --tags -t                     : Tags for the model management account.  Default: {}.
    -v                            : Verbosity flag.

## <a name="environment-commands"></a>환경 명령

    cluster        : Switch the current execution context to 'cluster'.
    delete         : Delete an MLCRP-provisioned resource.
    get-credentials: List the keys for an environment.
    list           : List all environments in the current subscription.
    local          : Switch the current execution context to 'local'.
    set            : Set the active MLC environment.
    setup          : Sets up an MLC environment.
    show           : Show an MLC resource; if resource_group or cluster_name are not provided, shows
                     the active MLC env.

**배포 환경 설정**

설정 명령을 사용하려면 구독에 대한 참가자 액세스 권한이 있어야 합니다. 해당 권한이 없는 경우 적어도 배포하는 리소스 그룹에 대한 참가자 액세스 권한이 필요합니다. 후자를 수행하려면 `-g` 플래그를 사용하여 설치 명령의 일부로 리소스 그룹 이름을 지정해야 합니다. 

배포에는 *로컬* 및 *클러스터*의 두 가지 옵션이 있습니다. `--cluster`(또는 `-c`) 플래그를 설정하면 ACS 클러스터를 프로비전하는 클러스터 배포를 사용할 수 있습니다. 기본 설정 구문은 다음과 같습니다.

`az ml env setup [-c] --location [location of environment resources] --name[name of environment]`

이 명령은 구독에 만든 저장소 계정, ACR 레지스트리 및 Application Insights 서비스가 있는 Azure 기계 학습 환경을 초기화합니다. 플래그가 지정되지 않으면 기본적으로 로컬 배포를 위한 환경(ACS 없음)만 초기화됩니다. 서비스를 크기 조정해야 하는 경우 `--cluster`(또는 `-c`) 플래그를 지정하여 ACS 클러스터를 만듭니다.

명령 세부 정보:

    --location -l        [Required]: Location for environment resources; an Azure region, e.g. eastus2.
    --name -n            [Required]: Name of environment to provision.
    --acr -r                       : ARM ID of ACR to associate with this environment.
    --agent-count -z               : Number of agents to provision in the ACS cluster. Default: 3.
    --cert-cname                   : CNAME of certificate.
    --cert-pem                     : Path to .pem file with certificate bytes.
    --cluster -c                   : Flag to provision ACS cluster. Off by default; specify this to force an ACS cluster deployment.
    --key-pem                      : Path to .pem file with certificate key.
    --master-count -m              : Number of master nodes to provision in the ACS cluster. Acceptable values: 1, 3, 5. Default: 1.
    --resource-group -g            : Resource group in which to create compute resource. Is created if it does not exist.
                                     If not provided, resource group is created with 'rg' appended to 'name.'.
    --service-principal-app-id -a  : App ID of service principal to use for configuring ML compute.
    --service-principal-password -p: Password associated with service principal.
    --storage -s                   : ARM ID of storage account to associate with this environment.
    --yes -y                       : Flag to answer 'yes' to any prompts. Command fails if user is not logged in.

전역 인수
```
    --debug                        : Increase logging verbosity to show all debug logs.
    --help -h                      : Show this help message and exit.
    --output -o                    : Output format.  Allowed values: json, jsonc, table, tsv. Default: json.
    --query                        : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose                      : Increase logging verbosity. Use --debug for full debug logs.
```
## <a name="model-commands"></a>모델 명령

    list
    register
    show

**모델 등록**

다음은 모델을 등록하는 명령입니다.

`az ml model register --model [path to model file] --name [model name]`

명령 세부 정보:

    --model -m [Required]: Model to register.
    --name -n  [Required]: Name of model to register.
    --description -d     : Description of the model.
    --tag -t             : Tags for the model. Multiple tags can be specified with additional -t arguments.
    -v                   : Verbosity flag.

전역 인수

    --debug              : Increase logging verbosity to show all debug logs.
    --help -h            : Show this help message and exit.
    --output -o          : Output format.  Allowed values: json, jsonc, table, tsv.  Default: json.
    --query              : JMESPath query string. See http://jmespath.org/ for more information and
                           examples.
    --verbose            : Increase logging verbosity. Use --debug for full debug logs.

## <a name="manifest-commands"></a>매니페스트 명령

    create: Create an Operationalization Manifest. This command has two different
            sets of required arguments, depending on if you want to use previously registered
            model/s.
    list
    show

**매니페스트 만들기**

다음 명령은 모델에 대한 매니페스트 파일을 만듭니다. 

`az ml manifest create --manifest-name [your new manifest name] -f [path to code file] -r [runtime for the image, e.g. spark-py]`

명령 세부 정보:

    --manifest-name -n [Required]: Name of the manifest to create.
    -f                 [Required]: The code file to be deployed.
    -r                 [Required]: Runtime of the web service. Valid runtimes are spark-py|python.
    --conda-file -c              : Path to Conda Environment file.
    --dependency -d              : Files and directories required by the service. Multiple
                                   dependencies can be specified with additional -d arguments.
    --manifest-description       : Description of the manifest.
    --schema-file -s             : Schema file to add to the manifest.
    -p                           : A pip requirements.txt file needed by the code file.
    -v                           : Verbosity flag.

등록된 모델 인수

    --model-id -i                : [Required] Id of previously registered model to add to manifest.
                                   Multiple models can be specified with additional -i arguments.

등록되지 않은 모델 인수

    --model-file -m              : [Required] Model file to register. If used, must be combined with
                                   model name.

전역 인수

    --debug                      : Increase logging verbosity to show all debug logs.
    --help -h                    : Show this help message and exit.
    --output -o                  : Output format.  Allowed values: json, jsonc, table, tsv.
                                   Default: json.
    --query                      : JMESPath query string. See http://jmespath.org/ for more
                                   information and examples.
    --verbose                    : Increase logging verbosity. Use --debug for full debug logs.


## <a name="image-commands"></a>이미지 명령

    create: Creates a docker image with the model and its dependencies. This command has two different sets of
            required arguments, depending on if you want to use a previously created manifest.
    list
    show
    usage

**이미지 만들기**

이미지는 이전에 매니페스트를 만든 옵션을 사용하여 만들 수 있습니다. 

`az ml image create -n [image name] --manifest-id [the manifest ID]`

또는 단일 명령을 사용하여 매니페스트와 이미지를 만들 수 있습니다. 

`az ml image create -n [image name] --model-file [model file or folder path] -f [code file, e.g. the score.py file] -r [the runtime eg.g. spark-py which is the Docker container image base]`

명령 세부 정보:

    --image-name -n [Required]: The name of the image being created.
    --image-description       : Description of the image.
    --image-type              : The image type to create. Defaults to "Docker".
    -v                        : Verbosity flag.

등록된 매니페스트 인수

    --manifest-id             : [Required] Id of previously registered manifest to use in image creation.

등록되지 않은 매니페스트 인수

    --conda-file -c           : Path to Conda Environment file.
    --dependency -d           : Files and directories required by the service. Multiple dependencies can
                                be specified with additional -d arguments.
    --model-file -m           : [Required] Model file to register.
    --schema-file -s          : Schema file to add to the manifest.
    -f                        : [Required] The code file to be deployed.
    -p                        : A pip requirements.txt file needed by the code file.
    -r                        : [Required] Runtime of the web service. Valid runtimes are python|spark-py.


## <a name="service-commands"></a>서비스 명령
서비스에서 지원되는 명령은 다음과 같습니다. 각 명령에 대한 매개 변수를 확인하려면 -h 옵션을 사용합니다. 예를 들어 `az ml service create realtime -h`를 사용하여 create 명령 세부 정보를 확인합니다.

    create
    delete
    keys
    list
    logs
    run
    show
    update
    usage

**서비스 만들기**

이전에 만든 이미지를 사용하여 서비스를 만들려면 다음 명령을 사용합니다.

`az ml service create realtime --image-id [image to deploy] -n [service name]`

단일 명령으로 서비스, 매니페스트 및 이미지를 만들려면 다음 명령을 사용합니다.

`az ml service create realtime --model-file [path to model file(s)] -f [path to model scoring file, e.g. score.py] -n [service name] -r [run time included in the image, e.g. spark-py]`

명령 세부 정보:

    -n                                : [Required] Webservice name.
    --autoscale-enabled               : Enable automatic scaling of service replicas based on request demand.
                                        Allowed values: true, false. False if omitted.  Default: false.
    --autoscale-max-replicas          : If autoscale is enabled - sets the maximum number of replicas.
    --autoscale-min-replicas          : If autoscale is enabled - sets the minimum number of replicas.
    --autoscale-refresh-period-seconds: If autoscale is enabled - the interval of evaluating scaling demand.
    --autoscale-target-utilization    : If autoscale is enabled - target utilization of replicas time.
    --collect-model-data              : Enable model data collection. Allowed values: true, false. False if omitted.  Default: false.
    --cpu                             : Reserved number of CPU cores per service replica (can be fraction).
    --enable-app-insights -l          : Enable app insights. Allowed values: true, false. False if omitted.  Default: false.
    --memory                          : Reserved amount of memory per service replica, in M or G. (ex. 1G, 300M).
    --replica-max-concurrent-requests : Maximum number of concurrent requests that can be routed to a service replica.
    -v                                : Verbosity flag.
    -z                                : Number of replicas for a Kubernetes service.  Default: 1.

등록된 이미지 인수

    --image-id                        : [Required] Image to deploy to the service.

등록되지 않은 이미지 인수

    --conda-file -c                   : Path to Conda Environment file.
    --image-type                      : The image type to create. Defaults to "Docker".
    --model-file -m                   : [Required] The model to be deployed.
    -d                                : Files and directories required by the service. Multiple dependencies can be specified
                                        with additional -d arguments.
    -f                                : [Required] The code file to be deployed.
    -p                                : A pip requirements.txt file of package needed by the code file.
    -r                                : [Required] Runtime of the web service. Valid runtimes are python|spark-py.
    -s                                : Input and output schema of the web service.

전역 인수

    --debug                           : Increase logging verbosity to show all debug logs.
    --help -h                         : Show this help message and exit.
    --output -o                       : Output format.  Allowed values: json, jsonc, table, tsv. Default: json.
    --query                           : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose                         : Increase logging verbosity. Use --debug for full debug logs.


종속성 연결용 `-d` 플래그에 대한 참고: 아직 번들로 포함되지 않은 디렉터리의 이름(zip, tar 등)을 전달하면 해당 디렉터리를 자동으로 tar로 압축하여 전달한 다음, 다른 쪽에서 자동으로 번들로부터 압축을 풉니다. 

이미 번들로 포함된 디렉터리를 전달하면 디렉터리는 파일로 처리되고 그대로 전달됩니다. 자동으로 번들이 풀리며, 이것은 코드에서 처리되어야 합니다.

**서비스 세부 정보 가져오기**

URL, 사용량을 포함한 서비스 세부 정보(스키마를 만든 경우 샘플 데이터 포함)를 가져옵니다.

`az ml service show realtime --name [service name]`

명령 세부 정보:

    --id -i    : The service id to show.
    --name -n  : Webservice name.
    -v         : Verbosity flag.

전역 인수

    --debug    : Increase logging verbosity to show all debug logs.
    --help -h  : Show this help message and exit.
    --output -o: Output format.  Allowed values: json, jsonc, table, tsv.  Default: json.
    --query    : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose  : Increase logging verbosity. Use --debug for full debug logs.

**서비스 실행**

`az ml service run realtime -n [service name] -d [input_data]`

명령 세부 정보:

    --id -i    : The service id to show.
    --name -n  : Webservice name.
    -v         : Verbosity flag.

전역 인수

    --debug    : Increase logging verbosity to show all debug logs.
    --help -h  : Show this help message and exit.
    --output -o: Output format.  Allowed values: json, jsonc, table, tsv.  Default: json.
    --query    : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose  : Increase logging verbosity. Use --debug for full debug logs.

명령

    az ml service run realtime

인수 --id -i    : [필수] 점수를 매기는 대상의 서비스 ID입니다.
-d         : 웹 서비스 호출에 사용할 데이터입니다.
-v         : 세부 정보 표시 플래그입니다.

전역 인수

    --debug    : Increase logging verbosity to show all debug logs.
    --help -h  : Show this help message and exit.
    --output -o: Output format.  Allowed values: json, jsonc, table, tsv. Default: json.
    --query    : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose  : Increase logging verbosity. Use --debug for full debug logs.
