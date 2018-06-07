---
title: Azure Kubernetes Service의 Azure Container Registry를 사용하여 인증
description: Azure Active Directory 서비스 주체를 사용하여 Azure Kubernetes Service에서 개인 컨테이너 레지스트리에 있는 이미지에 대한 액세스 권한을 제공하는 방법에 대해 알아봅니다.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/24/2018
ms.author: nepeters
ms.openlocfilehash: 0888afbb9087251e2c9219e2eb32fbf0d5600304
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Azure Kubernetes Service의 Azure Container Registry를 사용하여 인증

AKS(Azure Kubernetes Service)에서 ACR(Azure Container Registry)을 사용할 때는 인증 메커니즘을 설정해야 합니다. 이 문서에서는 이러한 두 가지 Azure 서비스 간 인증을 위해 권장되는 구성에 대해 설명합니다.

## <a name="grant-aks-access-to-acr"></a>AKS에 ACR에 대한 액세스 권한 부여

AKS 클러스터가 생성될 때는 Azure 리소스에 대한 클러스터 운용성을 관리할 수 있도록 서비스 주체도 생성됩니다. 이 서비스 주체는 ACR 레지스트리를 사용한 인증에도 사용할 수 있습니다. 이렇게 하려면 ACR 리소스에 대한 읽기 액세스 권한을 서비스 주체에 부여하도록 역할 할당을 생성해야 합니다.

다음 샘플을 사용해서 이 작업을 완료할 수 있습니다.

```bash
#!/bin/bash

AKS_RESOURCE_GROUP=myAKSResourceGroup
AKS_CLUSTER_NAME=myAKSCluster
ACR_RESOURCE_GROUP=myACRResourceGroup
ACR_NAME=myACRRegistry

# Get the id of the service principal configured for AKS
CLIENT_ID=$(az aks show --resource-group $AKS_RESOURCE_GROUP --name $AKS_CLUSTER_NAME --query "servicePrincipalProfile.clientId" --output tsv)

# Get the ACR registry resource id
ACR_ID=$(az acr show --name $ACR_NAME --resource-group $ACR_RESOURCE_GROUP --query "id" --output tsv)

# Create role assignment
az role assignment create --assignee $CLIENT_ID --role Reader --scope $ACR_ID
```

## <a name="access-with-kubernetes-secret"></a>Kubernetes 비밀을 사용하여 액세스

일부 경우에는 AKS에서 사용되는 서비스 주체의 범위를 ACR 레지스트리로 지정할 수 없습니다. 이러한 경우, 고유한 서비스 주체를 만들고 ACR 레지스트리로만 범위를 지정할 수 있습니다.

다음 스크립트를 사용해서 서비스 주체를 만들 수 있습니다.

```bash
#!/bin/bash

ACR_NAME=myacrinstance
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate the ACR login server and resource id.
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create a contributor role assignment with a scope of the ACR resource.
SP_PASSWD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME --role Reader --scopes $ACR_REGISTRY_ID --query password --output tsv)

# Get the service principle client id.
CLIENT_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output used when creating Kubernetes secret.
echo "Service principal ID: $CLIENT_ID"
echo "Service principal password: $SP_PASSWD"
```

이제 서비스 주체 자격 증명을 Kubernetes [이미지 끌어오기 비밀][image-pull-secret]에 저장하고 AKS 클러스터에서 컨테이너를 실행할 때 참조할 수 있습니다.

다음 명령은 Kubernetes 비밀을 만듭니다. 서버 이름을 ACR 로그인 서버로 바꾸고, 사용자 이름을 서비스 주체 ID로 바꾸고, 암호를 서비스 주체 암호로 바꿉니다.

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> --docker-email <email-address>
```

Kubernetes 비밀은 Pod 배포에서 `ImagePullSecrets` 매개 변수를 사용하여 사용할 수 있습니다.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: acr-auth-example
spec:
  template:
    metadata:
      labels:
        app: acr-auth-example
    spec:
      containers:
      - name: acr-auth-example
        image: myacrregistry.azurecr.io/acr-auth-example
      imagePullSecrets:
      - name: acr-auth
```

<!-- LINKS - external -->
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[image-pull-secret]: https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets
