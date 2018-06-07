---
title: Azure Active Directory를 사용하여 Data Lake Store 인증 | Microsoft Docs
description: Azure Active Directory를 사용하여 Data Lake Store를 인증하는 방법에 대해 알아봅니다.
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 061a44540cb618074764ae02fecb3f43d47fe5dd
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="authentication-with-data-lake-store-using-azure-active-directory"></a>Azure Active Directory를 사용하여 Data Lake Store 인증

Azure Data Lake Store는 인증을 위해 Azure Active Directory를 사용합니다. Azure Data Lake Store와 함께 작동하는 응용 프로그램을 제작하기 전에 Azure AD(Azure Active Directory)를 사용하여 응용 프로그램을 인증하는 방법을 결정해야 합니다. 

## <a name="authentication-options"></a>인증 옵션

* **최종 사용자 인증** - 최종 사용자의 Azure 자격 증명은 Data Lake Store를 인증하는 데 사용됩니다. Data Lake Store와 함께 사용하기 위해 만드는 응용 프로그램은 이러한 사용자 자격 증명을 요구합니다. 결과적으로 이 인증 메커니즘은 *대화형*이고 응용 프로그램은 로그인된 사용자의 컨텍스트에서 실행됩니다. 자세한 내용 및 지침은 [Data Lake Store에 대한 최종 사용자 인증](data-lake-store-end-user-authenticate-using-active-directory.md)을 참조하세요.

* **서비스 간 인증** - 응용 프로그램이 스스로 Data Lake Store를 인증하기를 원하는 경우 이 옵션을 사용하세요. 이 경우 Azure AD(Active Directory) 응용 프로그램 만들고 Azure AD 응용 프로그램의 키를 사용하여 Data Lake Store를 인증합니다. 결과적으로 이 인증 메커니즘은 *비 대화형*입니다. 자세한 내용 및 지침은 [Data Lake Store에 대한 서비스 간 인증](data-lake-store-service-to-service-authenticate-using-active-directory.md)을 참조하세요.

다음 표에는 Data Lake Store에서 최종 사용자 및 서비스 간 인증 메커니즘을 지원하는 방식이 정리되어 있습니다. 표를 읽는 방법은 다음과 같습니다.

* ✔ * 기호는 인증 옵션이 지원된다는 의미이며 인증 옵션 사용 방법을 보여주는 문서와 연결됩니다. 
* ✔ 기호는 인증 옵션이 지원된다는 의미입니다. 
* 빈 셀은 인증 옵션이 지원되지 않는다는 의미입니다.


|다음에서 이 인증 옵션 사용...                   |.NET         |자바     |PowerShell |CLI 2.0 | 파이썬   |REST (영문)     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|최종 사용자(MFA 없이**)                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)**(사용되지 않음)     |    **[✔*](data-lake-store-end-user-authenticate-rest-api.md)**    |
|최종 사용자(MFA 사용)                           |    **[✔*](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔*](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔*](data-lake-store-get-started-cli-2.0.md)**      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|서비스 간(클라이언트 키 사용)         |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔*](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔*](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔*](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|서비스 간(클라이언트 인증서 사용) |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    ✔     |    ✔    |

<i>* <b>✔\*</b> 기호를 클릭합니다. 이 기호는 연결입니다.</i><br>
<i>** MFA는 Multi-Factor Authentication을 의미</i>

인증에 Azure Active Directory를 사용하는 방법에 대한 자세한 내용은 [Azure Active Directory의 인증 시나리오](../active-directory/develop/active-directory-authentication-scenarios.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [최종 사용자 인증](data-lake-store-end-user-authenticate-using-active-directory.md)
* [서비스 간 인증](data-lake-store-service-to-service-authenticate-using-active-directory.md)


