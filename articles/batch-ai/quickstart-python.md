---
title: Azure 빠른 시작 - Batch AI로 CNTK 교육 - Python | Microsoft Docs
description: Python SDK를 사용하여 Batch AI로 CNTK 교육 작업을 실행하는 방법을 신속하게 알아봅니다
services: batch-ai
documentationcenter: na
author: lliimsft
manager: Vaman.Bedekar
editor: tysonn
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: Python
ms.topic: quickstart
ms.date: 10/06/2017
ms.author: lili
ms.openlocfilehash: da5c1181f9c4d311bdeabe837435ae4e0eb3dc1a
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="run-a-cntk-training-job-using-the-azure-python-sdk"></a>Azure Python SDK를 사용하여 CNTK 교육 작업 실행

이 빠른 시작에서는 Azure Python SDK를 사용하여 Batch AI 서비스를 통해 Microsoft CNTK(Cognitive Toolkit) 교육 작업을 실행하는 방법을 자세히 알아봅니다.

이 예제에서는 손으로 그린 이미지의 MNIST 데이터베이스를 사용하여 단일 노드 GPU 클러스터에서 나선형 신경망을 교육합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독 - Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

* Azure Python SDK - [설치 지침](/python/azure/python-sdk-azure-install) 참조

* Azure 저장소 계정 - [Azure 저장소 계정을 만드는 방법](../storage/common/storage-create-storage-account.md) 참조

* Azure Active Directory 서비스 사용자 자격 증명 - [CLI를 사용하여 서비스 사용자를 만드는 방법](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md) 참조

* Azure Cloud Shell 또는 Azure CLI를 사용하여 Batch AI 리소스 공급자를 구독에 한 번 등록해야 합니다. 공급자 등록에 최대 15분이 걸릴 수 있습니다.

```azurecli
az provider register -n Microsoft.BatchAI
```

## <a name="configure-credentials"></a>자격 증명 구성
스크립트 파일에 다음 코드를 추가하고 `FILL-IN-HERE`를 적절한 값으로 바꿉니다.

```Python
# credentials used for authentication
aad_client_id = 'FILL-IN-HERE'
aad_secret = 'FILL-IN-HERE'
aad_tenant = 'FILL-IN-HERE'
subscription_id = 'FILL-IN-HERE'

# credentials used for storage
storage_account_name = 'FILL-IN-HERE'
storage_account_key = 'FILL-IN-HERE'

# specify the credentials used to remote login your GPU node
admin_user_name = 'FILL-IN-HERE'
admin_user_password = 'FILL-IN-HERE'
```

소스 코드에 자격 증명을 배치하는 것은 좋은 방법이 아니지만, 여기서는 빠른 시작을 더 간단히 만들기 위해 이렇게 배치했습니다.
환경 변수 또는 별도의 구성 파일을 대신 사용하는 것이 좋습니다.

## <a name="create-batch-ai-client"></a>Batch AI 클라이언트 만들기

다음 코드는 서비스 사용자 자격 증명 개체와 Batch AI 클라이언트를 만듭니다.

```Python
from azure.common.credentials import ServicePrincipalCredentials
import azure.mgmt.batchai as batchai
import azure.mgmt.batchai.models as models

creds = ServicePrincipalCredentials(
        client_id=aad_client_id, secret=aad_secret, tenant=aad_tenant)

batchai_client = batchai.BatchAIManagementClient(
    credentials=creds, subscription_id=subscription_id)
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Batch AI 및 작업은 Azure 리소스이므로 Azure 리소스 그룹에 배치해야 합니다. 다음 코드 조각은 리소스 그룹을 만듭니다.

```Python
from azure.mgmt.resource import ResourceManagementClient

resource_group_name = 'myresourcegroup'
resource_management_client = ResourceManagementClient(
        credentials=creds, subscription_id=subscription_id)
resource = resource_management_client.resource_groups.create_or_update(
        resource_group_name, {'location': 'eastus'})
```


## <a name="prepare-azure-file-share"></a>Azure 파일 공유 준비
이해를 돕기 위해 이 빠른 시작에서는 Azure 파일 공유를 사용하여 학습 작업을 위한 학습 데이터 및 스크립트를 호스팅합니다.

1. `batchaiquickstart`이라는 파일 공유를 만듭니다.

```Python
from azure.storage.file import FileService
azure_file_share_name = 'batchaiquickstart'
service = FileService(storage_account_name, storage_account_key)
service.create_share(azure_file_share_name, fail_on_exist=False)
```

2. 공유에 `mnistcntksample`이라는 디렉터리를 만듭니다.

```Python
mnist_dataset_directory = 'mnistcntksample'
service.create_directory(azure_file_share_name, mnist_dataset_directory,
                         fail_on_exist=False)
```
3. [샘플 패키지](https://batchaisamples.blob.core.windows.net/samples/BatchAIQuickStart.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=b&sig=hrAZfbZC%2BQ%2FKccFQZ7OC4b%2FXSzCF5Myi4Cj%2BW3sVZDo%3D)를 다운로드하고 현재 디렉터리에 압축을 풉니다. 다음 코드는 필요한 파일을 Azure 파일 공유에 업로드합니다.

```Python
for f in ['Train-28x28_cntk_text.txt', 'Test-28x28_cntk_text.txt',
          'ConvNet_MNIST.py']:
     service.create_file_from_path(
             azure_file_share_name, mnist_dataset_directory, f, f)
```

## <a name="create-gpu-cluster"></a>GPU 클러스터 만들기

Batch AI 클러스터를 만듭니다. 이 예제에서는 클러스터가 단일 STANDARD_NC6 VM 노드로 구성됩니다. 이 VM의 크기는 NVIDIA K80 GPU 하나입니다. `azurefileshare`이라는 폴더에 파일 공유를 탑재합니다. GPU 계산 노드에서 이 폴더의 전체 경로는 `$AZ_BATCHAI_MOUNT_ROOT/azurefileshare`입니다.

```Python
cluster_name = 'mycluster'

relative_mount_point = 'azurefileshare'

parameters = models.ClusterCreateParameters(
    # Location where the cluster will physically be deployed
    location='eastus',
    # VM size. Use NC or NV series for GPU
    vm_size='STANDARD_NC6',
    # Configure the ssh users
    user_account_settings=models.UserAccountSettings(
        admin_user_name=admin_user_name,
        admin_user_password=admin_user_password),
    # Number of VMs in the cluster
    scale_settings=models.ScaleSettings(
        manual=models.ManualScaleSettings(target_node_count=1)
    ),
    # Configure each node in the cluster
    node_setup=models.NodeSetup(
        # Mount shared volumes to the host
        mount_volumes=models.MountVolumes(
            azure_file_shares=[
                models.AzureFileShareReference(
                    account_name=storage_account_name,
                    credentials=models.AzureStorageCredentialsInfo(
                        account_key=storage_account_key),
                    azure_file_url='https://{0}/{1}'.format(
                        service.primary_endpoint, azure_file_share_name),
                    relative_mount_path=relative_mount_point)],
        ),
    ),
)
batchai_client.clusters.create(resource_group_name, cluster_name,
                               parameters).result()
```

## <a name="get-cluster-status"></a>클러스터 상태 가져오기

다음 명령을 사용하여 클러스터 상태를 모니터링합니다.

```Python
cluster = batchai_client.clusters.get(resource_group_name, cluster_name)
print('Cluster state: {0} Target: {1}; Allocated: {2}; Idle: {3}; '
      'Unusable: {4}; Running: {5}; Preparing: {6}; Leaving: {7}'.format(
    cluster.allocation_state,
    cluster.scale_settings.manual.target_node_count,
    cluster.current_node_count,
    cluster.node_state_counts.idle_node_count,
    cluster.node_state_counts.unusable_node_count,
    cluster.node_state_counts.running_node_count,
    cluster.node_state_counts.preparing_node_count,
    cluster.node_state_counts.leaving_node_count))
```

앞의 코드는 다음 예제와 같은 기본 클러스터 할당 정보를 출력합니다.

```
Cluster state: AllocationState.steady Target: 1; Allocated: 1; Idle: 0; Unusable: 0; Running: 0; Preparing: 1; Leaving: 0
```

노드가 할당되고 준비가 완료되면 클러스터를 사용할 수 있습니다(`nodeStateCounts` 특성 참조). 잘못된 부분이 있으면 `errors` 특성에 오류 설명이 포함됩니다.

## <a name="create-training-job"></a>교육 작업 만들기

클러스터가 만들어지면 학습 작업을 구성하고 제출합니다.

```Python
job_name = 'myjob'

parameters = models.job_create_parameters.JobCreateParameters(
    # The job and cluster must be created in the same location
    location=cluster.location,
    # The cluster this job will run on
    cluster=models.ResourceId(id=cluster.id),
    # The number of VMs in the cluster to use
    node_count=1,
    # Write job's standard output and execution log to Azure File Share
    std_out_err_path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(
        relative_mount_point),
    # Configure location of the training script and MNIST dataset
    input_directories=[models.InputDirectory(
        id='SAMPLE',
        path='$AZ_BATCHAI_MOUNT_ROOT/{0}/{1}'.format(
            relative_mount_point, mnist_dataset_directory))],
    # Specify location where generated model will be stored
    output_directories=[models.OutputDirectory(
        id='MODEL',
        path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(relative_mount_point),
        path_suffix="Models")],
    # Container configuration
    container_settings=models.ContainerSettings(
        image_source_registry=models.ImageSourceRegistry(
            image='microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0')),
    # Toolkit specific settings
    cntk_settings=models.CNTKsettings(
        python_script_file_path='$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py',
        command_line_args='$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL')
)

# Create the job
batchai_client.jobs.create(resource_group_name, job_name, parameters).result()
```

## <a name="monitor-job"></a>작업 모니터링
다음 코드를 사용하여 작업 상태를 검사할 수 있습니다.

```Python
job = batchai_client.jobs.get(resource_group_name, job_name)

print('Job state: {0} '.format(job.execution_state.name))
```

출력은 `Job state: running`과 비슷합니다.

`executionState`에는 작업의 현재 실행 상태가 포함되어 있습니다.
* `queued`: 클러스터 노드를 사용할 수 있을 때까지 작업이 대기 중
* `running`: 작업이 실행 중
* `succeeded`(또는 `failed`): 작업이 완료되고, 결과에 대한 세부 정보가 `executionInfo`에 포함됨

## <a name="list-stdout-and-stderr-output"></a>stdout 및 stderr 출력 나열
생성된 stdout, stderr 및 로그 파일을 나열하려면 다음 코드를 사용합니다.

```Python
files = batchai_client.jobs.list_output_files(
    resource_group_name, job_name,
    models.JobsListOutputFilesOptions(outputdirectoryid="stdouterr"))

for file in (f for f in files if f.download_url):
    print('file: {0}, download url: {1}'.format(file.name, file.download_url))
```

## <a name="list-generated-model-files"></a>생성된 모델 파일 나열
생성된 모델 파일을 나열하려면 다음 코드를 사용합니다.
```Python
files = batchai_client.jobs.list_output_files(
    resource_group_name, job_name,
    models.JobsListOutputFilesOptions(outputdirectoryid="MODEL"))

for file in (f for f in files if f.download_url):
    print('file: {0}, download url: {1}'.format(file.name, file.download_url))
```

## <a name="delete-resources"></a>리소스 삭제

작업을 삭제하려면 다음 코드를 사용합니다.
```Python
batchai_client.jobs.delete(resource_group_name, job_name)
```

클러스터를 삭제하려면 다음 코드를 사용합니다.
```Python
batchai_client.clusters.delete(resource_group_name, cluster_name)
```

할당된 모든 리소스를 삭제하려면 다음 코드를 사용합니다.
```Python
resource_management_client.resource_groups.delete('myresourcegroup')
```
## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Python SDK를 사용하여 Batch AI 클러스터에서 CNTK 교육 작업을 실행하는 방법을 배웠습니다. 여러 도구에서 Batch AI를 사용하는 자세한 방법은 [교육 레시피](https://github.com/Azure/BatchAI)를 참조하세요.
