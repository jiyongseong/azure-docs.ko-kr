---
title: 키, 비밀 및 인증서 정보
description: REST 인터페이스 및 KV 개발자 세부 정보에 대한 개요입니다.
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: abd1b743-1d58-413f-afc1-d08ebf93828a
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: 2c7dd89d9c2e5d50f2533101499a6e50e52047b3
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="about-keys-secrets-and-certificates"></a>키, 비밀 및 인증서 정보
Azure Key Vault를 사용하면 Microsoft Azure 환경 내에서 암호화 키를 저장하고 사용할 수 있습니다. Key Vault는 여러 키 유형과 알고리즘을 지원하며, 고부가 가치 키에 HSM(하드웨어 보안 모듈)을 사용할 수 있습니다. 또한 Key Vault를 통해 사용자는 비밀을 안전하게 저장할 수 있습니다. 비밀은 특정 의미 체계가 없는 제한된 크기의 옥텟 개체입니다. Key Vault는 키와 비밀을 기반으로 하는 인증서를 지원하고 자동 갱신 기능을 추가합니다.

Azure Key Vault에 대한 일반적 내용은 [Azure Key Vault란?](/azure/key-vault/key-vault-whatis)을 참조하세요.

**Key Vault 일반 세부 정보**

-   [지원 표준](about-keys-secrets-and-certificates.md#BKMK_Standards)
-   [데이터 형식](about-keys-secrets-and-certificates.md#BKMK_DataTypes)  
-   [개체, 식별자 및 버전 관리](about-keys-secrets-and-certificates.md#BKMK_ObjId)  

**키 정보**

-   [키 및 키 유형](about-keys-secrets-and-certificates.md#BKMK_KeyTypes)  
-   [RSA 알고리즘](about-keys-secrets-and-certificates.md#BKMK_RSAAlgorithms)  
-   [RSA-HSM 알고리즘](about-keys-secrets-and-certificates.md#BKMK_RSA-HSMAlgorithms)  
-   [암호화 보호](about-keys-secrets-and-certificates.md#BKMK_Cryptographic)
-   [키 작업](about-keys-secrets-and-certificates.md#BKMK_KeyOperations)  
-   [키 특성](about-keys-secrets-and-certificates.md#BKMK_KeyAttributes)  
-   [키 태그](about-keys-secrets-and-certificates.md#BKMK_Keytags)  

**비밀 정보** 

-   [비밀 사용](about-keys-secrets-and-certificates.md#BKMK_WorkingWithSecrets)  
-   [비밀 특성](about-keys-secrets-and-certificates.md#BKMK_SecretAttrs)  
-   [비밀 태그](about-keys-secrets-and-certificates.md#BKMK_SecretTags)  
-   [비밀 액세스 제어](about-keys-secrets-and-certificates.md#BKMK_SecretAccessControl)  

**인증서 정보**

-   [인증서 작성](#BKMK_CompositionOfCertificate)  
-   [인증서 특성과 태그](#BKMK_CertificateAttributesAndTags)  
-   [인증서 정책](#BKMK_CertificatePolicy)  
-   [인증서 발급자](#BKMK_CertificateIssuer)  
-   [인증서 연락처](#BKMK_CertificateContacts)  
-   [인증서 액세스 제어](#BKMK_CertificateAccessControl)  

--

## <a name="key-vault-general-details"></a>Key Vault 일반 세부 정보

다음 섹션에서는 Azure Key Vault 서비스의 구현에 적용할 수 있는 일반 정보를 제공합니다.

###  <a name="BKMK_Standards"></a> 지원 표준

JSON(JavaScript Object Notation) 및 JOSE(JavaScript Object Signing and Encryption) 사양은 중요한 배경 정보입니다.  

-   [JWK(JSON 웹 키)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JWE(JSON 웹 암호화)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JWA(JSON 웹 알고리즘)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JWS(JSON 웹 서명)](http://tools.ietf.org/html/draft-ietf-jose-json-web-signature)  

### <a name="BKMK_DataTypes"></a> 데이터 형식

키, 암호화 및 서명에 대한 관련 데이터 형식은 [JOSE 사양](#BKMK_Standards)을 참조하세요.  

-   **알고리즘** - 키 작업에 지원되는 알고리즘(예: RSA1_5)  
-   **암호 텍스트 값** - Base64URL을 사용하여 인코딩된 암호 텍스트 옥텟  
-   **다이제스트 값** - Base64URL을 사용하여 인코딩된 해시 알고리즘의 출력  
-   **키 유형** - 지원되는 키 유형 중 하나(예: RSA)  
-   **일반 텍스트 값** - Base64URL을 사용하여 인코딩된 일반 텍스트 옥텟  
-   **서명 값** - Base64URL을 사용하여 인코딩된 서명 알고리즘의 출력  
-   **base64URL** - Base64URL[RFC4648]을 사용하여 인코딩된 이진 값  
-   **부울** - true 또는 false 중 하나  
-   **ID** - AAD(Azure Active Directory)의 ID  
-   **IntDate** - 1970-01-01T0:0:0Z UTC에서 지정된 UTC 날짜/시간까지의 초 수를 나타내는 JSON 10진수 값입니다. 일반적 날짜/시간, 특히 UTC에 대한 자세한 내용은 [RFC3339]를 참조하세요.  

###  <a name="BKMK_ObjId"></a> 개체, 식별자 및 버전 관리

Azure Key Vault에 저장된 개체는 개체의 새 인스턴스가 만들어 질 때마다 버전을 유지하며, 각 버전에는 고유 식별자와 URL이 있습니다. 개체가 처음 만들어지면 고유한 버전 식별자가 지정되며 개체의 현재 버전으로 표시됩니다. 개체 이름이 동일한 새 인스턴스를 만들면 새 개체에 고유한 버전 식별자가 제공되어 현재 버전이 됩니다.  

Azure Key Vault의 개체는 현재 식별자 또는 버전별 식별자를 사용하여 처리할 수 있습니다. 예를 들어 "MasterKey"라는 이름의 키가 지정되는 경우 현재 식별자를 사용하여 작업을 수행하면 시스템에서 사용 가능한 최신 버전을 사용하게 됩니다. 버전별 식별자를 사용하여 작업을 수행하면 시스템에서 해당 특정 버전의 개체를 사용하게 됩니다.  

개체는 Azure Key Vault 내에서 URL을 사용하여 고유하게 식별되므로 지리적 위치에 관계없이 시스템의 두 개체가 동일한 URL을 갖지 않습니다. 개체에 대한 전체 URL은 개체 식별자라고 하며, Key Vault, 개체 형식, 사용자 제공 개체 이름 및 개체 버전을 식별하는 접두사 부분으로 구성됩니다. 개체 이름은 대/소문자를 구분하지 않으며 변경할 수 없습니다. 개체 버전이 포함되지 않은 식별자를 기본 식별자라고 합니다.  

자세한 내용은 [인증, 요청 및 응답](authentication-requests-and-responses.md)을 참조하세요.

개체 식별자의 일반적인 형식은 다음과 같습니다.  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

위치:  

|||  
|-|-|  
|`keyvault-name`|Microsoft Azure Key Vault 서비스의 키 자격 증명 모음에 대한 이름입니다.<br /><br /> Key Vault 이름은 사용자가 선택하며 전역적으로 고유합니다.<br /><br /> Key Vault 이름은 0~9, a~z, A~Z 및 -만 포함된 3-24자 길이의 문자열이어야 합니다.|  
|`object-type`|"keys" 또는 "secrets" 개체의 형식입니다.|  
|`object-name`|`object-name`은 사용자가 제공한 이름이며 Key Vault 내에서 고유해야 합니다. 이름은 0~9, a~z, A~Z 및 -만 포함된 1~127자 길이의 문자열이어야 합니다.|  
|`object-version`|`object-version`은 시스템에서 생성된 32자의 문자열 식별자이며, 필요에 따라 고유한 버전의 개체를 처리하는 데 사용됩니다.|  

## <a name="key-vault-keys"></a>Key Vault 키

###  <a name="BKMK_KeyTypes"></a> 키 및 키 유형

Azure Key Vault의 암호화 키는 JWK[JSON 웹 키] 개체로 표현됩니다. 기본 JWK/JWA 사양도 Azure Key Vault 구현에 고유한 키 유형을 사용할 수 있도록 확장되었습니다. 예를 들어 HSM 공급자(Thales) 특정 패키징을 사용하여 Azure Key Vault에 키를 가져와서 Azure Key Vault HSM에서만 사용할 수 있도록 안전하게 전송할 수 있게 합니다.  

초기 Azure Key Vault 릴리스는 RSA 키만 지원합니다. 이후 릴리스에서는 대칭 및 타원 곡선과 같은 다른 키 유형을 지원할 수 있습니다.  

-   **RSA**: 2,048비트 RSA 키입니다. Key Vault에서 소프트웨어로 처리되지만 사용되지 않을 때 HSM에 있는 시스템 키를 사용하여 암호화되어 저장되는 "소프트" 키입니다. 클라이언트는 기존 RSA 키를 가져오거나 Azure Key Vault에서 생성하도록 요청할 수 있습니다.  
-   **RSA-HSM**: HSM에서 처리되는 RSA 키입니다. RSA-HSM 키는 Azure Key Vault HSM 보안 권역 중 하나에서 보호됩니다(격리를 유지하기 위해 지역별로 보안 권역이 있음). 클라이언트는 소프트 형식으로 또는 호환되는 HSM 장치에서 내보내는 방식으로 RSA 키를 가져오거나 Azure Key Vault에서 생성하도록 요청할 수 있습니다. 이 키 유형은 HSM 키 자료를 전송하기 위해 가져오는 JWK에 T 특성을 추가합니다.  

     지리적 경계에 대한 자세한 내용은 [Microsoft Azure 보안 센터](https://azure.microsoft.com/en-us/support/trust-center/privacy/)를 참조하세요.  

###  <a name="BKMK_RSAAlgorithms"></a> RSA 알고리즘  
 Azure Key Vault의 RSA 키에서 지원되는 알고리즘 식별자는 다음과 같습니다.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, ENCRYPT/DECRYPT

-   **RSA1_5** - RSAES-PKCS1-V1_5[RFC3447] 키 암호화입니다.  
-   **RSA-OAEP** - OAEP(Optimal Asymmetric Encryption Padding)[RFC3447]를 사용하는 RSAES입니다(A.2.1 섹션의 RFC 3447에 명시된 기본 매개 변수 포함). 이러한 기본 매개 변수는 SHA-1의 해시 함수와 SHA-1이 포함된 MGF1의 마스크 생성 함수를 사용합니다.  

#### <a name="signverify"></a>SIGN/VERIFY

-   **RS256** - SHA-256을 사용하는 RSASSA-PKCS-v1_5입니다. 응용 프로그램에서 제공하는 다이제스트 값은 SHA-256을 사용하여 계산되어야 하며, 길이는 32바이트여야 합니다.  
-   **RS384** - SHA-384를 사용하는 RSASSA-PKCS-v1_5입니다. 응용 프로그램에서 제공하는 다이제스트 값은 SHA-384를 사용하여 계산되어야 하며, 길이는 48바이트여야 합니다.  
-   **RS512** - SHA-512를 사용하는 RSASSA-PKCS-v1_5입니다. 응용 프로그램에서 제공하는 다이제스트 값은 SHA-512를 사용하여 계산되어야 하며, 길이는 64바이트여야 합니다.  
-   **RSNULL** - 특정 TLS 시나리오를 사용하려면 특수 사용 사례인 [RFC2437]을 참조하세요.  

###  <a name="BKMK_RSA-HSMAlgorithms"></a> RSA-HSM 알고리즘  
Azure Key Vault의 RSA-HSM 키에서 지원되는 알고리즘 식별자는 다음과 같습니다.  

### <a name="BKMK_Cryptographic"></a> 암호화 보호

Azure Key Vault에서 사용하는 암호화 모듈(HSM 또는 소프트웨어)을 통해 FIPS의 유효성을 검사합니다. FIPS 모드에서 실행되는 어떤 특별한 작업도 수행할 필요가 없습니다. 키를 HSM 보호 모드로 **만들거나** **가져오는** 경우 FIPS 140-2 수준 2 이상으로 유효성이 검사된 HSM 내부에서 해당 키를 처리하도록 보장됩니다. 키를 소프트웨어 보호 모드로 **만들거나** **가져오는** 경우 FIPS 140-2 수준 1 이상으로 유효성이 검사된 암호화 모듈 내부에서 해당 키를 처리합니다. 자세한 내용은 [키 및 키 유형](about-keys-secrets-and-certificates.md#BKMK_KeyTypes)을 참조하세요.

#### <a name="wrapunwrap-encryptdecrypt"></a>WRAP/UNWRAP, ENCRYPT/DECRYPT

-   **RSA1_5** - RSAES-PKCS1-V1_5[RFC3447] 키 암호화입니다.  
-   **RSA-OAEP** - OAEP(Optimal Asymmetric Encryption Padding)[RFC3447]를 사용하는 RSAES입니다(A.2.1 섹션의 RFC 3447에 명시된 기본 매개 변수 포함). 이러한 기본 매개 변수는 SHA-1의 해시 함수와 SHA-1이 포함된 MGF1의 마스크 생성 함수를 사용합니다.  

 #### <a name="signverify"></a>SIGN/VERIFY  

-   **RS256** - SHA-256을 사용하는 RSASSA-PKCS-v1_5입니다. 응용 프로그램에서 제공하는 다이제스트 값은 SHA-256을 사용하여 계산되어야 하며, 길이는 32바이트여야 합니다.  
-   **RS384** - SHA-384를 사용하는 RSASSA-PKCS-v1_5입니다. 응용 프로그램에서 제공하는 다이제스트 값은 SHA-384를 사용하여 계산되어야 하며, 길이는 48바이트여야 합니다.  
-   **RS512** - SHA-512를 사용하는 RSASSA-PKCS-v1_5입니다. 응용 프로그램에서 제공하는 다이제스트 값은 SHA-512를 사용하여 계산되어야 하며, 길이는 64바이트여야 합니다.  
-   RSNULL: 특정 TLS 시나리오를 사용하려면 특수 사용 사례인 [RFC2437]을 참조하세요.  

###  <a name="BKMK_KeyOperations"></a> 키 작업

Azure Key Vault에서 키 개체에 대해 지원하는 작업은 다음과 같습니다.  

-   **만들기**: 클라이언트에서 Azure Key Vault에 키를 만들 수 있습니다. 키 값은 Azure Key Vault에서 생성되고 저장되며 클라이언트에 릴리스되지 않습니다. Azure Key Vault에서 비대칭(및 앞으로는 타원 곡선 및 대칭) 키를 만들 수 있습니다.  
-   **가져오기**: 클라이언트에서 기존 키를 Azure Key Vault로 가져올 수 있습니다. JWK 구조 내에서 다양한 패키징 메서드를 사용하여 비대칭(및 앞으로는 타원 곡선 및 대칭) 키를 Azure Key Vault로 가져올 수 있습니다.  
-   **업데이트**: 충분한 권한이 있는 클라이언트에서 이전에 Azure Key Vault에 저장된 키와 연결된 메타데이터(키 특성)를 수정할 수 있습니다.  
-   **삭제**: Azure Key Vault에서 키를 삭제할 수 있는 충분한 권한을 가진 클라이언트를 허용합니다.  
-   **나열**: 클라이언트에서 지정된 Azure Key Vault에 있는 모든 키를 나열할 수 있습니다.  
-   **버전 나열**: 클라이언트에서 지정된 Azure Key Vault에 있는 지정된 키의 모든 버전을 나열할 수 있습니다.  
-   **가져오기**: 클라이언트에서 Azure Key Vault에 있는 지정된 키의 공개 부분을 검색할 수 있습니다.  
-   **백업**: 키를 보호된 형식으로 내보냅니다.  
-   **복원**: 이전에 백업한 키를 가져옵니다.  

자세한 내용은 [키 작업](/rest/api/keyvault/key-operations.md)을 참조하세요.  

키가 Azure Key Vault에 만들어지면 해당 키를 사용하여 수행할 수 있는 암호화 작업은 다음과 같습니다.  

-   **서명 및 확인**: 엄격히 말해 Azure Key Vault는 서명 만들기의 일부로 콘텐츠의 해싱을 지원하지 않으므로 이 작업은 "해시 서명" 또는 "해시 확인"입니다. 응용 프로그램은 로컬로 서명할 데이터를 해시한 다음, Azure Key Vault에서 이 해시에 서명하도록 요청해야 합니다. 서명된 해시의 확인 작업은 [공개] 키 자료에 액세스할 수 없는 응용 프로그램의 편의를 위해 지원됩니다. 최적의 응용 프로그램 성능을 위해 확인 작업을 로컬로 수행하는 것이 좋습니다.  
-   **키 암호화/래핑**: Azure Key Vault에 저장된 키는 다른 키, 일반적으로 대칭 CEK(콘텐츠 암호화 키)를 보호하는 데 사용할 수 있습니다. Azure Key Vault의 키가 비대칭이면 키 암호화가 사용됩니다(예: RSA-OAEP 및 WRAPKEY/UNWRAPKEY 작업은 ENCRYPT/DECRYPT와 동일함). Azure Key Vault의 키가 대칭이면 키 래핑이 사용됩니다(예: AES-KW). WRAPKEY 작업은 [공개] 키 자료에 액세스할 수 없는 응용 프로그램의 편의를 위해 지원됩니다. 최적의 응용 프로그램 성능을 위해 WRAPKEY 작업을 로컬로 수행하는 것이 좋습니다.  
-   **암호화 및 암호 해독**: Azure Key Vault에 저장된 키는 단일 데이터 블록을 암호화하거나 해독하는 데 사용할 수 있습니다. 이 블록의 크기는 키 유형 및 선택한 암호화 알고리즘에 따라 결정됩니다. 암호화 작업은 [공개] 키 자료에 액세스할 수 없는 응용 프로그램의 편의를 위해 제공됩니다. 최적의 응용 프로그램 성능을 위해 암호화 작업을 로컬로 수행하는 것이 좋습니다.  

비대칭 키를 사용하는 WRAPKEY/UNWRAPKEY 작업이 ENCRYPT/DECRYPT와 같으므로 필요하지 않은 것처럼 보일 수 있지만, 서비스에서 다른 키 유형을 지원하는 경우 이러한 작업의 의미 체계 및 권한 부여 분리와 일관성을 제공하기 위해 별도의 고유한 작업을 사용하는 것이 중요합니다.  

Azure Key Vault는 EXPORT(내보내기) 작업을 지원하지 않습니다. 일단 키가 시스템에 프로비전되면 추출할 수 없거나 키 자료가 수정됩니다.  그러나 다른 사용 사례에 대한 키가 필요할 수 있거나 Azure Key Vault에서 키를 삭제한 Azure Key Vault 사용자는 BACKUP(백업) 및 RESTORE(복원) 작업을 사용하여 키를 보호된 형식으로 내보내거나 가져올 수 있습니다. BACKUP 작업으로 만든 키는 Azure Key Vault 외부에서 사용할 수 없습니다. 또는 IMPORT 작업을 여러 Azure Key Vault 인스턴스에 대해 사용할 수 있습니다.  

사용자는 Azure Key Vault에서 JWK 개체의 key_ops 속성을 사용하여 키별로 지원하는 암호화 작업을 제한할 수 있습니다.  

JWK 개체에 대한 자세한 내용은 [JWK(JSON 웹 키)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key)를 참조하세요.  

###  <a name="BKMK_KeyAttributes"></a> 키 특성

키 자료 외에도 지정할 수 있는 특성은 다음과 같습니다. JSON 요청에서 특성이 지정되지 않은 경우에도 특성 키워드 및 중괄호('{' '}')가 필요합니다.  

- *enabled*: 부울, 선택 사항, 기본값은 **true**입니다. 암호화 작업을 위해 사용 설정 및 사용 가능한 키인지 여부를 지정합니다. *enabled* 특성은 *nbf* 및 *exp*와 함께 사용됩니다. *nbf* 및 *exp* 간에 작업이 발생하면 *enabled*가 **true**로 설정된 경우에만 해당 작업이 허용됩니다. [특정 조건](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops)에 따른 특정 작업 유형을 제외하고는 *nbf* / *exp* 시간 범위에 속하지 않은 작업이 자동으로 허용되지 않습니다.
- *nbf*: IntDate, 선택 사항, 기본값은 now입니다. *nbf*(이전이 아님) 특성은 [특정 조건](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops)에 따른 특정 작업 유형을 제외하고는 암호화 작업에 키를 사용하지 않아야 하는 시간을 식별합니다. *nbf* 특성을 처리하려면 현재 날짜/시간이 *nbf* 특성에 나열된 날짜/시간 이전이 아닌 시간 이후이거나 같아야 합니다. Azure Key Vault는 클록 오차를 처리하기 위해 일반적으로 약간(몇 분 이내)의 여유를 제공해야 합니다. 이 값은 IntDate 값이 포함된 숫자여야 합니다.  
- *exp*: IntDate, 선택 사항, 기본값은 "forever"입니다. *exp*(만료 시간) 특성은 [특정 조건](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops)에 따른 특정 작업 유형을 제외하고는 암호화 작업에 키를 사용하지 않아야 하는 만료 시간 또는 그 이후를 식별합니다. *exp* 특성을 처리하려면 현재 날짜/시간이 *exp* 특성에 나열된 만료 날짜/시간 이전이어야 합니다. Azure Key Vault는 클록 오차를 처리하기 위해 일반적으로 약간(몇 분 이내)의 여유를 제공해야 합니다. 이 값은 IntDate 값이 포함된 숫자여야 합니다.  

키 특성이 포함된 모든 응답에 포함되는 추가 읽기 전용 특성이 있습니다.  

- *created*: IntDate, 선택 사항입니다. *created* 특성은 이 버전의 키를 만든 시점을 나타냅니다. 이 특성을 추가하기 전에 만든 키에 대한 값은 null입니다. 이 값은 IntDate 값이 포함된 숫자여야 합니다.  
- *updated*: IntDate, 선택 사항입니다. *updated* 특성은 이 버전의 키를 업데이트한 시점을 나타냅니다. 이 특성을 추가하기 전에 마지막으로 업데이트한 키에 대한 값은 null입니다. 이 값은 IntDate 값이 포함된 숫자여야 합니다.  

IntDate 및 기타 데이터 형식에 대한 자세한 내용은 [데이터 형식](about-keys-secrets-and-certificates.md#BKMK_DataTypes)을 참조하세요.  

#### <a name="BKMK_key-date-time-ctrld-ops"></a> 날짜 및 시간 제어 작업

*nbf* / *exp* 시간 범위에 속하지 않은 아직 유효하지 않은 키와 만료된 키는 **decrypt**, **unwrap** 및 **verify** 작업에 대해 작동합니다(403, 금지됨 오류를 반환하지 않음). 아직 유효하지 않은 상태를 사용하는 이유는 프로덕션에서 사용하기 전에 키를 테스트할 수 있도록 하기 위한 것입니다. 만료된 상태를 사용하는 이유는 키가 유효할 때 만들어진 데이터에 대한 복구 작업을 허용하기 위한 것입니다. 또한 Key Vault 정책을 사용하거나 *enabled* 키 특성을 **false**로 업데이트하여 키에 대한 액세스를 사용하지 않도록 설정할 수 있습니다.

데이터 형식에 대한 자세한 내용은 [데이터 형식](about-keys-secrets-and-certificates.md#BKMK_DataTypes)을 참조하세요.

사용 가능한 다른 특성에 대한 자세한 내용은 [JWK(JSON 웹 키)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key)를 참조하세요.

### <a name="BKMK_Keytags"></a> 키 태그

응용 프로그램 관련 메타데이터를 태그 형식으로 추가로 지정할 수 있습니다. Azure Key Vault는 최대 15개의 태그를 지원하며, 각 태그는 256자 이름과 256자의 값을 가질 수 있습니다.  

>[!Note]
>해당 개체 형식(키, 비밀 또는 인증서)에 대한 *list* 또는 *get* 권한이 태그에 있는 경우 호출자에서 해당 태그를 읽을 수 있습니다.

###  <a name="BKMK_KeyAccessControl"></a> 키 액세스 제어

Key Vault에서 관리하는 키에 대한 액세스 제어는 키 컨테이너 역할을 하는 Key Vault 수준에서 제공됩니다. 동일한 Key Vault의 비밀에 대한 액세스 제어 정책과는 완전히 다른 키 액세스 제어 정책이 있습니다. 사용자는 키를 보관할 하나 이상의 자격 증명 모음을 만들 수 있으며, 시나리오에 따라 키를 적절하게 세분화하고 관리해야 합니다. 키 액세스 제어는 비밀 액세스 제어와 무관합니다.  

자격 증명 모음의 키 액세스 제어 항목에 대해 사용자/서비스 사용자별로 부여할 수 있는 권한은 다음과 같습니다. 이러한 권한은 키 개체에 허용되는 작업과 매우 비슷합니다.  

-   *create*: 새 키 만들기
-   *get*: 키의 공개 부분 및 해당 특성 읽기
-   *list*: 키 자격 증명 모음에 저장된 키 또는 키 버전 나열
-   *update*: 키 특성 업데이트
-   *delete*: 키 개체 삭제
-   *sign*: 키를 사용하여 다이제스트에 서명
-   *verify*: 키를 사용하여 다이제스트 확인
-   *wrapKey*: 키를 사용하여 대칭 키 보호
-   *unwrapKey*: 키를 사용하여 래핑된 대칭 키 보호 해제
-   *encrypt*: 키를 사용하여 임의의 바이트 시퀀스 보호
-   *decrypt*: 키를 사용하여 바이트 시퀀스 보호 해제
-   *import*: 키 자격 증명 모음으로 키 가져오기
-   *backup*: 키 자격 증명 모음의 키 백업
-   *restore*: 키 자격 증명 모음에 백업된 키 복원
-   *all*: 모든 권한

## <a name="key-vault-secrets"></a>Key Vault 비밀 

###  <a name="BKMK_WorkingWithSecrets"></a> 비밀 사용

Azure Key Vault의 비밀은 각각 최대 크기가 25,000바이트인 옥텟 시퀀스입니다. Azure Key Vault 서비스는 비밀에 대한 어떠한 의미 체계도 제공하지 않습니다. 단지 데이터를 허용, 암호화 및 저장하고, 나중에 비밀을 검색하는 데 사용할 수 있는 비밀 식별자("id")를 반환합니다.  

매우 중요한 데이터의 경우, 클라이언트에서 Azure Key Vault에 저장된 데이터에 대한 추가 보호 계층을 고려해야 합니다. 예를 들어 별도의 보호 키를 사용하여 데이터를 미리 암호화합니다.  

또한 Azure Key Vault에서 비밀에 대한 contentType 필드도 지원합니다. 클라이언트는 비밀 데이터에 대해 검색 시 해당 데이터를 해석하는 데 도움이 되는 "contentType" 콘텐츠 형식을 지정할 수 있습니다. 이 필드의 최대 길이는 255자이며, 미리 정의된 값이 없습니다. 비밀 데이터를 해석하기 위한 힌트로 사용하는 것이 좋습니다. 예를 들어 구현에서는 암호와 인증서를 모두 비밀로 저장한 다음, 이 필드를 사용하여 해당 항목을 나타낼 수 있습니다. 미리 정의된 값이 없습니다.  

###  <a name="BKMK_SecretAttrs"></a> 비밀 특성

비밀 데이터 외에도 지정할 수 있는 특성은 다음과 같습니다.  

- *exp*: IntDate, 선택 사항, 기본값은 **forever**입니다. *exp*(만료 시간) 특성은 [특정 조건](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops)에 있는 경우를 제외하고는 비밀 데이터를 검색하지 않아야 하는 만료 시간 또는 그 이후를 식별합니다. *exp* 특성을 처리하려면 현재 날짜/시간이 *exp* 특성에 나열된 만료 날짜/시간 이전이어야 합니다. Azure Key Vault는 클록 오차를 처리하기 위해 일반적으로 약간(몇 분 이내)의 여유를 제공해야 합니다. 이 값은 IntDate 값이 포함된 숫자여야 합니다.  
- *nbf*: IntDate, 선택 사항, 기본값은 **now**입니다. *nbf*(이전이 아님) 특성은 [특정 조건](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops)에 있는 경우를 제외하고는 비밀 데이터를 검색하지 않아야 하는 시간 이전을 식별합니다. *nbf* 특성을 처리하려면 현재 날짜/시간이 *nbf* 특성에 나열된 날짜/시간 이전이 아닌 시간 이후이거나 같아야 합니다. Azure Key Vault는 클록 오차를 처리하기 위해 일반적으로 약간(몇 분 이내)의 여유를 제공해야 합니다. 이 값은 IntDate 값이 포함된 숫자여야 합니다.  
- *enabled*: 부울, 선택 사항, 기본값은 **true**입니다. 이 특성은 비밀 데이터를 검색할 수 있는지 여부를 지정합니다. enabled 특성은 nbf와 exp 사이에 작업이 발생할 때 nbf 및 *exp*와 함께 사용되며, enabled가 **true**로 설정된 경우에만 허용됩니다. [특정 조건](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops)에 있는 경우를 제외하고는 *nbf* 및 *exp* 시간 범위에 속하지 않은 작업이 자동으로 허용되지 않습니다.  

비밀 특성이 포함된 모든 응답에 포함되는 추가 읽기 전용 특성이 있습니다.  

- *created*: IntDate, 선택 사항입니다. created 특성은 이 버전의 비밀을 만든 시점을 나타냅니다. 이 특성을 추가하기 전에 만든 비밀에 대한 값은 null입니다. 이 값은 IntDate 값이 포함된 숫자여야 합니다.  
- *updated*: IntDate, 선택 사항입니다. updated 특성은 이 버전의 비밀을 업데이트한 시점을 나타냅니다. 이 특성을 추가하기 전에 마지막으로 업데이트한 비밀에 대한 값은 null입니다. 이 값은 IntDate 값이 포함된 숫자여야 합니다.

#### <a name="BKMK_secret-date-time-ctrld-ops"></a> 날짜 및 시간 제어 작업

*nbf* / *exp* 시간 범위에 속하지 않은 비밀의 **get** 작업은 아직 유효하지 않은 비밀과 만료된 비밀에 대해 작동합니다. 테스트를 위해 비밀의 **get** 작업 호출을 아직 유효하지 않은 비밀에 사용할 수 있습니다. 복구 작업을 위해 만료된 비밀을 검색(**get**)할 수 있습니다.

데이터 형식에 대한 자세한 내용은 [데이터 형식](about-keys-secrets-and-certificates.md#BKMK_DataTypes)을 참조하세요.  

###  <a name="BKMK_SecretAccessControl"></a> 비밀 액세스 제어

Azure Key Vault에서 관리되는 비밀에 대한 액세스 제어는 해당 비밀의 컨테이너 역할을 하는 Key Vault 수준에서 제공됩니다. 동일한 Key Vault의 키에 대한 액세스 제어 정책과는 완전히 다른 비밀 액세스 제어 정책이 있습니다. 사용자는 비밀을 보관할 하나 이상의 자격 증명 모음을 만들 수 있으며, 시나리오에 따라 비밀을 적절하게 세분화하고 관리해야 합니다. 비밀 액세스 제어는 키 액세스 제어와 무관합니다.  

자격 증명 모음의 비밀 액세스 제어 항목에서 보안 주체별로 사용할 수 있고 비밀 개체에 허용되는 작업과 매우 비슷한 권한은 다음과 같습니다.  

-   *set*: 새 비밀 만들기  
-   *get*: 비밀 읽기  
-   *list*: Key Vault에 저장된 비밀 또는 비밀 버전 나열  
-   *delete*: 비밀 삭제  
-   *all*: 모든 권한  

비밀을 사용하는 방법에 대한 자세한 내용은 [비밀 작업](/rest/api/keyvault/secret-operations.md)을 참조하세요.  

###  <a name="BKMK_SecretTags"></a> 비밀 태그  
응용 프로그램 관련 메타데이터를 태그 형식으로 추가로 지정할 수 있습니다. Azure Key Vault는 최대 15개의 태그를 지원하며, 각 태그는 256자 이름과 256자의 값을 가질 수 있습니다.  

>[!Note]
>해당 개체 형식(키, 비밀 또는 인증서)에 대한 *list* 또는 *get* 권한이 태그에 있는 경우 호출자에서 해당 태그를 읽을 수 있습니다.

## <a name="key-vault-certificates"></a>Key Vault 인증서

Key Vault 인증서 지원은 x509 인증서 및 다음 동작의 관리를 위해 제공됩니다.  

-   인증서 소유자는 Key Vault 만들기 프로세스 또는 기존 인증서의 가져오기를 통해 인증서를 만들 수 있습니다. 여기에는 자체 서명된 인증서 및 인증 기관 생성 인증서가 모두 포함됩니다.
-   Key Vault 인증서 소유자는 개인 키 자료와 상호 작용하지 않고 X509 인증서의 안전한 저장 및 관리를 구현할 수 있습니다.  
-   인증서 소유자는 인증서의 수명 주기를 관리하도록 Key Vault를 지시하는 정책을 만들 수 있습니다.  
-   인증서 소유자는 인증서의 만료 및 갱신이라는 수명 주기 이벤트에 대한 알림을 위해 연락처 정보를 제공할 수 있습니다.  
-   선택한 발급자 - Key Vault 파트너 X509 인증서 공급자/인증서 기관을 통한 자동 갱신을 지원합니다.

>[!Note]
>파트너가 아닌 공급자/기관도 허용되지만 자동 갱신 기능은 지원하지 않습니다.

###  <a name="BKMK_CompositionOfCertificate"></a> 인증서 작성

Key Vault 인증서가 만들어지면 주소 지정 가능한 키와 암호도 동일한 이름으로 만들어집니다. Key Vault 키는 키 작업을 허용하고, Key Vault 비밀은 인증서 값을 비밀로 검색할 수 있게 합니다. Key Vault 인증서에는 공용 x509 인증서 메타데이터도 포함됩니다.  

인증서의 식별자 및 버전은 키 및 암호의 식별자 및 버전과 비슷합니다. Key Vault 인증서 버전으로 만든 특정 버전의 주소 지정 가능한 키와 암호는 Key Vault 인증서 응답에서 사용할 수 있습니다.
 
![인증서는 복합 개체입니다.](media/azure-key-vault.png)

###  <a name="BKMK_CertificateExportableOrNonExportableKey"></a> 내보내기 가능/불가능 키

Key Vault 인증서가 만들어지면 인증서를 만드는 데 사용된 정책에서 키를 내보낼 수 있다고 나타내는 경우 PFX 또는 PEM 형식의 개인 키를 사용하여 주소 지정 가능한 비밀에서 해당 인증서를 검색할 수 있습니다. Key Vault 인증서를 만드는 데 사용된 정책에서 키를 내보낼 수 없다고 나타내는 경우 개인 키는 비밀로 검색될 때 값의 일부가 아닙니다.  

주소 지정이 가능한 키는 내보낼 수 없는 KV 인증서와 더 관련이 있습니다. 주소 지정 가능한 KV 키의 작업은 KV 인증서를 만드는 데 사용되는 KV 인증서 정책의 *keyusage* 필드에서 매핑됩니다.  

두 가지 유형의 키, 즉 *RSA* 또는 인증서가 있는 *RSA HSM*이 지원됩니다. 내보내기 가능한 키는 RSA에서만 허용되며, RSA HSM에서는 지원되지 않습니다.  

###  <a name="BKMK_CertificateAttributesAndTags"></a> 인증서 특성과 태그

Key Vault 인증서에는 인증서 메타데이터, 주소 지정 가능한 키 및 주소 지정 가능한 비밀 외에도 특성 및 태그가 포함됩니다.  

#### <a name="attributes"></a>특성

인증서 특성은 KV 인증서를 만들 때 만들어지는 주소 지정 가능한 키와 암호의 특성으로 미러됩니다.  

Key Vault 인증서에 포함되는 특성은 다음과 같습니다.  

-   *enabled*: 부울, 선택 사항, 기본값은 **true**입니다. 이 특성은 인증서 데이터를 비밀로 검색할 수 있는지 또는 키로 작동할 수 있는지 여부를 나타내기 위해 지정할 수 있습니다. *nbf*과 exp 사이에 작업이 발생할 때 *nbf* 및 *exp*와 함께 사용되며, enabled가 true로 설정된 경우에만 허용됩니다. *nbf* 및 *exp* 시간 범위에 속하지 않은 작업은 자동으로 허용되지 않습니다.  

응답에 포함되는 추가 읽기 전용 특성은 다음과 같습니다.

-   *created*: IntDate: 이 버전의 인증서를 만든 시점을 나타냅니다.  
-   *updated*: IntDate: 이 버전의 인증서를 업데이트한 시점을 나타냅니다.  
-   *exp*: IntDate: x509 인증서의 만료 날짜 값을 포함합니다.  
-   *nbf*: IntDate: x509 인증서의 날짜 값을 포함합니다.  

> [!Note] 
> Key Vault 인증서가 만료되면 주소 지정 가능한 해당 키와 비밀이 작동하지 않게 됩니다.  

#### <a name="tags"></a>태그들

 클라이언트에서 지정하는 키 값 쌍의 사전이며, 키와 비밀의 태그와 비슷합니다.  

 > [!Note]
> 해당 개체 형식(키, 비밀 또는 인증서)에 대한 *list* 또는 *get* 권한이 태그에 있는 경우 호출자에서 해당 태그를 읽을 수 있습니다.

###  <a name="BKMK_CertificatePolicy"></a> 인증서 정책

인증서 정책에는 KV 인증서의 수명 주기를 만들고 관리하는 방법에 대한 정보가 포함됩니다. 개인 키가 있는 인증서를 키 자격 증명 모음에 가져오면 x509 인증서를 읽어 기본 정책을 만듭니다.  

KV 인증서를 처음부터 만드는 경우 이 KV 인증서 버전 또는 다음 KV 인증서 버전을 만드는 방법을 알리는 정책을 Key Vault에 제공해야 합니다. 일단 정책이 설정되면 다음 KV 인증서 버전을 만들기 위한 연속 만들기 작업에는 해당 정책이 필요하지 않습니다.  

KV 인증서의 모든 버전에 대한 정책 인스턴스는 하나만 있습니다.  

높은 수준의 인증서 정책에 포함되는 항목은 다음과 같습니다.  

-   X509 인증서 속성: 주체 이름, 주체 대체 이름 등을 포함하며, x509 인증서 요청을 만드는 데 사용됩니다.  
-   키 속성: 키 유형, 키 길이, 내보내기 가능 및 재사용 키 필드를 포함합니다. 이러한 필드는 키를 생성하는 방법을 키 자격 증명 모음에 지시합니다.  
-   비밀 속성: 인증서를 비밀 번호로 검색하기 위해 비밀 값을 생성하기 위한 주소 지정 가능한 비밀의 콘텐츠 형식과 같은 비밀 속성을 포함합니다.  
-   수명 작업: KV 인증서에 대한 수명 작업을 포함합니다. 각 수명 작업에 포함되는 항목은 다음과 같습니다.  

     - 트리거: 만료 이전 잔존 일 수 또는 수명 범위 백분율을 통해 지정됩니다.  

     - 작업: *emailContacts* 또는 *autoRenew* 작업 유형이 지정됩니다.  

-   발급자: x509 인증서를 발급하는 데 사용할 인증서 발급자에 대한 매개 변수입니다.  
-   정책 특성: 정책과 관련된 특성을 포함합니다.  

#### <a name="x509-to-key-vault-usage-mapping"></a>X509 및 Key Vault 사용 매핑

다음 표에서는 x509 키 사용 정책과 Key Vault 인증서 만들기의 일부로 만들어진 키의 효과적인 키 작업을 매핑하고 있습니다.

|**X509 키 사용 플래그**|**Key Vault 키 작업**|**기본 동작**|
|----------|--------|--------|
|DataEncipherment|encrypt, decrypt| 해당 없음 |
|DecipherOnly|decrypt| 해당 없음  |
|DigitalSignature|sign, verify| 인증서를 만들 때 사용하도록 지정하지 않은 Key Vault 기본값 | 
|EncipherOnly|encrypt| 해당 없음 |
|KeyCertSign|sign, verify|해당 없음|
|KeyEncipherment|wrapKey, unwrapKey| 인증서를 만들 때 사용하도록 지정하지 않은 Key Vault 기본값 | 
|NonRepudiation|sign, verify| 해당 없음 |
|crlsign|sign, verify| 해당 없음 |

###  <a name="BKMK_CertificateIssuer"></a> 인증서 발급자

Key Vault 인증서 개체에는 선택한 인증서 발급자 공급자와 통신하여 x509 인증서를 요청하는 데 사용되는 구성이 있습니다.  

-   Key Vault와 제휴 관계에 있는 SSL 인증서에 대한 인증서 발급자 공급자는 다음과 같습니다.

|**공급자 이름**|**위치**|
|----------|--------|
|DigiCert|공용 클라우드 및 Azure Government의 모든 키 자격 증명 모음 서비스 위치에서 지원됩니다.|
|GlobalSign|공용 클라우드 및 Azure Government의 모든 키 자격 증명 모음 서비스 위치에서 지원됩니다.|

Key Vault에 인증서 발급자를 만들려면 먼저 다음 필수 조건 1 및 2의 단계를 수행해야 합니다.  

1. CA(인증 기관) 공급자에 등록  

    -   조직 관리자는 하나 이상의 CA 공급자와 함께 자신의 회사(예: Contoso)를 등록해야 합니다.  

2. 관리자는 Key Vault에 대한 요청자 자격 증명을 만들어 SSL 인증서를 등록(및 갱신)합니다.  

    -   공급자의 발급자 개체를 만드는 데 사용할 구성을 키 자격 증명 모음에 제공합니다.  

인증서 포털에서 발급자 개체를 만드는 방법에 대한 자세한 내용은 [Key Vault 인증서 블로그](http://aka.ms/kvcertsblog)를 참조하세요.  

Key Vault를 사용하면 서로 다른 발급자 구성으로 여러 발급자 개체를 만들 수 있습니다. 발급자 개체가 만들어지면 하나 또는 여러 개의 증명서 정책에서 해당 이름을 참조할 수 있습니다. 발행자 개체를 참조하면 인증서를 만들고 갱신하는 동안 CA 공급자로부터 x509 인증서를 요청할 때 발행자 개체에서 지정한 대로 구성을 사용하도록 Key Vault에 지시합니다.  

발급자 개체는 자격 증명 모음에 만들어지며, 동일한 자격 증명 모음의 KV 인증서에만 사용할 수 있습니다.  

###  <a name="BKMK_CertificateContacts"></a> 인증서 연락처

인증서 연락처에는 인증서 수명 이벤트에서 트리거된 알림을 보내도록 연락처 정보가 포함됩니다. 연락처 정보는 키 자격 증명 모음의 모든 인증서에서 공유합니다. 알림은 키 자격 증명 모음의 모든 인증서에 대한 이벤트에 대해 지정한 모든 연락처로 보내집니다.  

인증서를 자동으로 갱신하도록 정책이 설정되는 경우 알림을 보내는 이벤트는 다음과 같습니다.  

-   인증서 갱신 전
-   인증서 갱신 후 - 인증서가 성공적으로 갱신된 경우에 시작되거나, 오류가 있는 경우 인증서를 수동으로 갱신해야 합니다.  

 인증서를 수동으로 갱신하는 정책이 설정되는 경우(이메일 전용) 인증서를 갱신할 시간이 되면 알림을 보냅니다.  

###  <a name="BKMK_CertificateAccessControl"></a> 인증서 액세스 제어

 인증서에 대한 액세스 제어는 Key Vault에서 관리하고 해당 인증서의 컨테이너 역할을 하는 Key Vault 수준에서 제공됩니다. 동일한 Key Vault의 키 및 비밀에 대한 액세스 제어 정책과는 완전히 다른 인증서 액세스 제어 정책이 있습니다. 사용자는 인증서를 보관할 하나 이상의 자격 증명 모음을 만들 수 있으며, 시나리오에 따라 인증서를 적절하게 세분화하고 관리해야 합니다.  

 키 자격 증명 모음의 비밀 액세스 제어 항목에서 보안 주체별로 사용할 수 있고 비밀 개체에 허용되는 작업과 매우 비슷한 권한은 다음과 같습니다.  

-   *get*: 현재 인증서 버전 또는 모든 인증서 버전을 가져올 수 있습니다. 
-   *list*: 현재 인증서 또는 인증서 버전을 나열할 수 있습니다.  
-   *delete*: 인증서, 해당 정책 및 모든 해당 버전을 삭제할 수 있습니다.  
-   *create*: Key Vault 인증서를 만들 수 있습니다.  
-   *import*: 인증서 자료를 Key Vault 인증서로 가져올 수 있습니다.  
-   *update*: 인증서를 업데이트할 수 있습니다.  
-   *manageconnects*: Key Vault 인증서 연락처를 관리할 수 있습니다.  
-   *getissuers*: 인증서의 발급자를 가져올 수 있습니다.  
-   *listissuers*: 인증서의 발급자를 나열할 수 있습니다.  
-   *setissuers*: Key Vault 인증서 발급자를 만들거나 업데이트할 수 있습니다.  
-   *deleteissuers*: Key Vault 인증서 발급자를 삭제할 수 있습니다.  
-   *all*: 모든 권한을 부여합니다.  

## <a name="additional-information-for-certificates"></a>인증서에 대한 추가 정보

- [인증서 및 정책](/rest/api/keyvault/certificates-and-policies.md)
- [인증서 발급자](/rest/api/keyvault/certificate-issuers.md)
- [인증서 연락처](/rest/api/keyvault/certificate-contacts.md)

## <a name="see-also"></a>참고 항목

- [인증, 요청 및 응답](authentication-requests-and-responses.md)
- [Key Vault 버전](key-vault-versions.md)
- [Key Vault 개발자 가이드](/azure/key-vault/key-vault-developers-guide)
