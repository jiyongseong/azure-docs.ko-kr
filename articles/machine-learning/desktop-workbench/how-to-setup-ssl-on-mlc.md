---
title: Azure MLC(Machine Learning Compute) 클러스터에서 SSL 사용 | Microsoft Docs
description: Azure Machine Learning Compute (MLC) 클러스터에서 점수 매기기 호출에 대한 SSL 설정 지침 확인하기
services: machine-learning
author: SerinaKaye
ms.author: serinak
manager: hjerez
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/24/2018
ms.openlocfilehash: 81649277c0cb2f4b00ce856be5efcce6121b1be4
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="enable-ssl-on-an-azure-machine-learning-compute-mlc-cluster"></a>Azure MLC(Machine Learning Compute) 클러스터에서 SSL 사용 

이 지침을 통해 MLC(Machine Learning Compute ) 클러스터에서 점수 매기기 호출에 대한 SSL을 설정할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건 

1. 실시간 점수 매기기 호출 시 사용할 CNAME(DNS 이름)을 결정합니다.

2. 해당 CNAME으로 *암호가 없는* 인증서를 만듭니다.

3. 인증서가 PEM 형식이 아닌 경우에는 *openssl*과 같은 도구를 사용하여 PEM으로 변환합니다.

필수 조건을 완료하면 두 가지 파일을 갖게 됩니다.

* 인증서 파일(예: `cert.pem`) 파일에 전체 인증서 체인이 있는지 확인합니다.
* 키 파일(예: `key.pem`)



## <a name="set-up-an-ssl-certificate-on-a-new-acs-cluster"></a>새 ACS 클러스터에 SSL 인증서 설정

Azure Machine Learning CLI를 사용하여 `-c` 스위치와 함께 다음 명령을 실행하여 SSL 인증서가 첨부된 ACS 클러스터를 만듭니다.

```
az ml env create -c -g <resource group name> -n <cluster name> --cert-cname <CNAME> --cert-pem <path to cert.pem file> --key-pem <path to key.pem file>
```


## <a name="set-up-an-ssl-certificate-on-an-existing-acs-cluster"></a>기존 ACS 클러스터에 SSL 인증서 설정

SSL 없이 생성된 클러스터를 대상으로 하는 경우 Azure PowerShell cmdlet을 사용하여 인증서를 추가할 수 있습니다.

원시 PEM 형식으로 키와 인증서를 제공해야 합니다. 이들을 PowerShell 변수로 읽어들일 수 있습니다.

```
$keyValueInPemFormat = [IO.File]::ReadAllText('<path to key.pem file>')
$certValueInPemFormat = [IO.File]::ReadAllText('<path to cert.pem file>')
```
클러스터에 인증서를 추가합니다. 

```
Set-AzureRmMlOpCluster -ResourceGroupName my-rg -Name my-cluster -SslStatus Enabled -SslCertificate $certValueInPemFormat -SslKey $keyValueInPemFormat -SslCName foo.mycompany.com
```

## <a name="map-the-cname-and-the-ip-address"></a>CNAME과 IP 주소 매핑

필수 조건에서 선택한 CNAME과 실시간 FE(프런트 엔드)의 IP 주소 사이에 매핑을 만듭니다. FE의 IP 주소를 찾으려면 아래 명령을 실행합니다. 실시간 클러스터 프런트 엔드의 IP 주소가 포함된 "publicIpAddress"라는 필드가 출력에 표시됩니다. CNAME에서 사용된 FQDN에서 공용 IP 주소로 레코드를 설정하려면 DNS 공급자의 지침을 참조합니다.



## <a name="auto-detection-of-a-certificate"></a>인증서 자동 검색 

"`az ml service create realtime`" 명령을 사용하여 실시간 웹 서비스를 만들 때 Azure Machine Learning은 클러스터에 설정된 SSL을 자동 검색하고 지정된 인증서로 점수 매기기 URL을 자동으로 설정합니다. 

인증서 상태를 보려면 다음 명령을 실행합니다. 점수 매기기 URI의 "https" 접두사와 호스트 이름의 CNAME을 확인하십시오. 

``` 
az ml service show realtime -n <service name>
{
                "id" : "<your service id>",
                "name" : "<your service name >",
                "state" : "Succeeded",
                "createdAt" : "<datetime>”,
                "updatedAt" : "< datetime>",
                "scoringUri" : "https://<your CNAME>/api/v1/service/<service name>/score"
}
```
