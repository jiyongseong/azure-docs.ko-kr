---
title: 인증서 만들기 모니터링 및 관리
description: Key Vault를 사용하여 인증서 만들기 프로세스를 만들고, 모니터링하고, 상호 작용하기 위한 다양한 옵션을 설명하는 시나리오입니다.
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 0d0995aa-b60d-4811-be12-ba0a45390197
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: e1ea77304fa59b67e0e28a4c7e0b13633eeeff6f
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="monitor-and-manage-certificate-creation"></a>인증서 만들기 모니터링 및 관리
Azure에 적용합니다.  

다음 

이 문서에 설명된 시나리오는 / 작업은 다음과 같습니다.

- 지원되는 발급자로 KV 인증서 요청
- 보류 중인 요청 가져오기 - 요청 상태는 "진행 중"
- 보류 중인 요청 가져오기 - 요청 상태는 "완료"
- 보류 중인 요청 가져오기 - 보류 중인 요청 상태는 "취소" 또는 "실패"
- 보류 중인 요청 가져오기 - 보류 중인 요청 상태는 "삭제" 또는 "덮어쓰기"
- 보류 중인 요청이 있을 때 만들기(또는 가져오기) - 상태는 "진행 중"
- 보류 중인 요청이 발급자(예: DigiCert)로 만들어질 때 병합
- 보류 중인 요청 상태가 "진행 중"인 동안 취소 요청
- 보류 중인 요청 개체 삭제
- 수동으로 KV 인증서 만들기
- 보류 중인 요청이 만들어질 때 병합 - 수동 인증서 만들기

## <a name="request-a-kv-certificate-with-a-supported-issuer"></a>지원되는 발급자로 KV 인증서 요청 

|방법|요청 URI|  
|------------|-----------------|  
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|  

다음 예제에는 DigiCert로 발급자 공급자와 함께 키 자격 증명 모음에서 이미 사용할 수 있는 "mydigicert"라는 개체가 필요합니다. 발급자와 작업에 대한 자세한 내용은 [인증서 발급자](/rest/api/keyvault/certificate-issuers.md)를 참조하세요.  

### <a name="request"></a>요청  

```json
{  
  "policy": {  
    "x509_props": {  
      "subject": "CN=MyCertSubject1"  
    },  
  "issuer": {  
       "name": "mydigicert",  
    "cty": "OV-SSL",  
    }  
  }  
}  

```  

### <a name="response"></a>response  

```  
StatusCode: 202, ReasonPhrase: 'Accepted'  
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"  
{  
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",  
  "issuer": {  
    "name": "mydigicert"  
  },  
  "csr": "MIICq......DD5Lp5cqXg==",  
  "cancellation_requested": false,  
  "status": "InProgress",  
  "status_details": "Pending certificate created. Certificate request is in progress. This may take some time based on the issuer provider. Please check again later",  
  "request_id": "a76827a18b63421c917da80f28e9913d"  
}  

```  

## <a name="get-pending-request---request-status-is-inprogress"></a>보류 중인 요청 가져오기 - 요청 상태는 "진행 중"

|방법|요청 URI|  
|------------|-----------------|  
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

### <a name="request"></a>요청  
 GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 또는  

 GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

> [!NOTE]
>  *request_id*가 쿼리에서 지정된 경우 필터처럼 작동합니다. 쿼리와 보류 중인 개체의 *request_id*가 다른 경우 404의 http 상태 코드가 반환됩니다.  

### <a name="response"></a>response  

```  
StatusCode: 200, ReasonPhrase: 'OK'  
{  
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",  
  "issuer": {  
    "name": "{issuer-name}"  
  },  
  "csr": "MIICq......DD5Lp5cqXg==",  
  "cancellation_requested": false,  
  "status": "inProgress",  
  "status_details": "…",  
  "request_id": "a76827a18b63421c917da80f28e9913d"  
}  

```  

## <a name="get-pending-request---request-status-is-complete"></a>보류 중인 요청 가져오기 - 요청 상태는 "완료"

### <a name="request"></a>요청  

|방법|요청 URI|  
|------------|-----------------|  
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

 GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 또는  

 GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

### <a name="response"></a>response  

```  
StatusCode: 200, ReasonPhrase: 'OK'  
{  
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",  
  "issuer": {  
    "name": "{issuer-name}"  
  },  
  "csr": "MIICq......DD5Lp5cqXg==",  
  "cancellation_requested": false,  
  "status": "completed",  
  "request_id": "a76827a18b63421c917da80f28e9913d",  
   "target": “https://mykeyvault.vault.azure.net/certificates/mycert1?api-version={api-version}"  
}  

```  

## <a name="get-pending-request---pending-request-status-is-canceled-or-failed"></a>보류 중인 요청 가져오기 - 보류 중인 요청 상태는 "취소" 또는 "실패"  

### <a name="request"></a>요청  

|방법|요청 URI|  
|------------|-----------------|  
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

 GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 또는  

 GET  `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

### <a name="response"></a>response  

```  
StatusCode: 200, ReasonPhrase: 'OK'  
{  
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",  
  "issuer": {  
    "name": "{issuer-name}"  
  },  
  "csr": "MIICq......DD5Lp5cqXg==",  
  "cancellation_requested": false,  
  "status": "failed",  
  "status_details": "",  
  "request_id": "a76827a18b63421c917da80f28e9913d",  
   "error":  
    {  
        "code": "<errorcode>",    
        "message": "<message>"  
    }  
}  

```  

> [!NOTE]
> *errorcode*의 값은 발급자 또는 사용자 오류에 따라 각각 "인증서 발급자 오류" 또는 "요청 거부됨"일 수 있습니다.  

## <a name="get-pending-request---pending-request-status-is-deleted-or-overwritten"></a>보류 중인 요청 가져오기 - 보류 중인 요청 상태는 "삭제" 또는 "덮어쓰기"  
 해당 상태가 "진행 중"이 아닌 경우 만들기/가져오기 작업으로 보류 중인 개체를 삭제하거나 덮어쓸 수 있습니다.

|방법|요청 URI|  
|------------|-----------------|  
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

### <a name="request"></a>요청  
 GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 또는  

 GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

### <a name="response"></a>response  

```  
StatusCode: 404, ReasonPhrase: 'Not Found'  
{  
  "error":  
    {  
         "code": "PendingCertificateNotFound",  
        "message": "…"  
    }  
}  

```  

## <a name="create-or-import-when-pending-request-exists---status-is-inprogress"></a>보류 중인 요청이 있을 때 만들기(또는 가져오기) - 상태는 "진행 중"
 보류 중인 개체에는 다음의 4가지 가능한 상태가 있습니다. "진행 중", "취소됨", "실패됨" 또는 "완료됨"

 보류 중인 요청의 상태가 "진행 중"인 경우 만들기(및 가져오기) 작업은 409(충돌)의 http 상태 코드로 실패합니다.  

 충돌을 해결하려면:  

 - 인증서가 수동으로 만들어지고 있는 경우 병합을 수행하여 KV 인증서를 완료하거나 보류 중인 개체를 삭제할 수 있습니다.  

 - 인증서가 발급자로 만들어지고 있는 경우 인증서가 완료, 실패 또는 취소될 때까지 대기할 수 있습니다. 또는 보류 중인 개체를 삭제할 수 있습니다.

> [!NOTE]
> 보류 중인 개체를 삭제하는 것은 공급자로 x509 인증서 요청을 취소하거나 취소하지 않을 수 있습니다.  

|방법|요청 URI|  
|------------|-----------------|  
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|  

### <a name="request"></a>요청  

```json
{  
  "policy": {  
    "x509_props": {  
      "subject": "CN=MyCertSubject1"  
    },  
  "issuer": {  
       "name": "mydigicert"  
    }  
  }  
}  

```  

### <a name="response"></a>response  

```  
StatusCode: 409, ReasonPhrase: 'Conflict'  
{  
  "error":  
    {  
        "code": "Forbidden",  
        "message": "A new key vault certificate can not be created or imported while a pending key vault certificate's status is inProgress."  
    }  
}  

```  

## <a name="merge-when-pending-request-is-created-with-an-issuer"></a>보류 중인 요청이 발급자로 만들어질 때 병합
 보류 중인 개체가 발급자로 만들어질 때 병합은 허용되지 않지만 해당 상태가 "진행 중"인 경우 병합은 허용됩니다. 

 x509 인증서 만들기 요청이 어떤 이유로 실패하거나 취소되는 경우, x509 인증서를 대역 외 방법으로 검색할 수 있는 경우 병합 작업은 KV 인증서를 완료하여 수행될 수 있습니다.  

|방법|요청 URI|  
|------------|-----------------|  
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|  

### <a name="request"></a>요청  

```json
{  
  "x5c": [  "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8="  
  ]  
}  

```  

### <a name="response"></a>response  

```json
StatusCode: 403, ReasonPhrase: 'Forbidden'  
{  
  "error":  
    {  
       "code": "Forbidden",  
       "message": "Merge is forbidden on pending object created with issuer : <issuer-name> while it is in progess."  
    }  
}  

```  

## <a name="request-a-cancellation-while-the-pending-request-status-is-inprogress"></a>보류 중인 요청 상태가 "진행 중"인 동안 취소 요청  
 취소만 요청할 수 있습니다.  요청이 취소되거나 취소되지 않을 수 있습니다. 요청이 "진행 중"이 아닌 경우 400(잘못된 요청)의 http 상태가 반환됩니다.  

|방법|요청 URI|  
|------------|-----------------|  
|패치|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

### <a name="request"></a>요청  
 PATCH `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 또는  

 PATCH `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

```json
{  
  "cancellation_requested": true  
}  

```  

### <a name="response"></a>response  

```  
StatusCode: 200, ReasonPhrase: 'OK'  
{  
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",  
  "issuer": {  
    "name": "{issuer-name}"  
  },  
  "csr": "MIICq......DD5Lp5cqXg==",  
  "cancellation_requested": true,  
  "status": "inProgress",  
  "status_details": "…",  
  "request_id": "a76827a18b63421c917da80f28e9913d"  
}  

```  

## <a name="delete-a-pending-request-object"></a>보류 중인 요청 개체 삭제  

> [!NOTE]
> 보류 중인 개체를 삭제하는 것은 공급자로 x509 인증서 요청을 취소하거나 취소하지 않을 수 있습니다.  

|방법|요청 URI|  
|------------|-----------------|  
|삭제|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

### <a name="request"></a>요청  
 DELETE `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 또는  

 DELETE `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

### <a name="response"></a>response  

```  
StatusCode: 200, ReasonPhrase: 'OK'  
{  
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",  
  "issuer": {  
    "name": "{issuer-name}"  
  },  
  "csr": "MIICq......DD5Lp5cqXg==",  
  "cancellation_requested": false,  
  "status": "inProgress",  
  "request_id": "a76827a18b63421c917da80f28e9913d",  
}  
```  

## <a name="create-a-kv-certificate-manually"></a>수동으로 KV 인증서 만들기  
 수동 만들기 프로세스를 통해 원하는 CA로 발급된 인증서를 만들 수 있습니다. 발급자의 이름을 "알 수 없음"으로 설정하거나 발급자 필드를 지정하지 마십시오.  

|방법|요청 URI|  
|------------|-----------------|  
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|  

### <a name="request"></a>요청  

```json
{  
  "policy": {  
    "x509_props": {  
      "subject": "CN=MyCertSubject1"  
    }  
  "issuer": {  
       "name": "Unknown"  
    }  
  }  
}  

```  

### <a name="response"></a>response  

```  
StatusCode: 202, ReasonPhrase: 'Accepted'  
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"  
{  
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",  
  "issuer": {  
    "name": "Unknown"  
  },  
  "csr": "MIICq......DD5Lp5cqXg==",  
  "status": "inProgress",  
  "status_details": "Pending certificate created. Please Perform Merge to complete the request.",  
  "request_id": "a76827a18b63421c917da80f28e9913d"  
}  

```  

## <a name="merge-when-a-pending-request-is-created---manual-certificate-creation"></a>보류 중인 요청이 만들어질 때 병합 - 수동 인증서 만들기  

|방법|요청 URI|  
|------------|-----------------|  
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|  

### <a name="request"></a>요청  

```json
{  
  "x5c": [  "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8="  
  ]  
}  

```  

|요소 이름|필수|type|버전|설명|  
|------------------|--------------|----------|-------------|-----------------|  
|x5c|예|array|\<소개 버전>|기본 64 문자열 배열로 X509 인증서 체인|  

### <a name="response"></a>response  

```  
StatusCode: 201, ReasonPhrase: 'Created'  
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1?api-version={api-version}"  
{  
"id": "https mykeyvault.vault.azure.net/certificates/mycert1/f366e1a9dd774288ad84a45a5f620352",  
    "kid": "https:// mykeyvault.vault.azure.net/keys/mycert1/f366e1a9dd774288ad84a45a5f620352",  
    "sid": " mykeyvault.vault.azure.net/secrets/mycert1/f366e1a9dd774288ad84a45a5f620352",  
    "cer": "……de34534……",  
    "x5t": "n14q2wbvyXr71Pcb58NivuiwJKk",  
    "attributes": {  
        "enabled": true,  
        "exp": 1530394215,  
        "nbf": 1435699215,  
        "created": 1435699919,  
        "updated": 1435699919  
    },  
    "pending": {  
        "id": "https:// mykeyvault.vault.azure.net/certificates/mycert1/pending"  
    },  
    "policy": {  
        "id": "https:// mykeyvault.vault.azure.net/certificates/mycert1/policy",  
        "key_props": {  
            "exportable": false,  
            "kty": "RSA",  
            "key_size": 2048,  
            "reuse_key": false  
        },  
        "secret_props": {  
            "contentType": "application/x-pkcs12"  
        },  
        "x509_props": {  
            "subject": "CN=Mycert1",  
            "ekus": ["1.3.6.1.5.5.7.3.1", "1.3.6.1.5.5.7.3.2"],  
                       "validity_months":12  
        },  
        "lifetime_actions": [{  
            "trigger": {  
                "lifetime_percentage": 80  
            },  
            "action": {  
                "action_type": "EmailContacts"  
            }  
        }],  
        "issuer": {  
            "name": "Unknown"  
        },  
        "attributes": {  
            "enabled": true,  
            "created": 1435699811,  
            "updated": 1435699811  
        }  
    }  
}  

```

## <a name="see-also"></a>참고 항목
- [키, 비밀 및 인증서에 대한 정보](about-keys-secrets-and-certificates.md)
