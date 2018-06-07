---
title: '최종 사용자 인증: Azure Active Directory를 사용하여 Java로 Data Lake Store 인증 | Microsoft Docs'
description: Azure Active Directory와 Java를 사용하여 Data Lake Store로 최종 사용자 인증을 수행하는 방법을 알아봅니다.
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
ms.openlocfilehash: b638860dbdab7e3b5a747a4ddd82e7247f24845f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="end-user-authentication-with-data-lake-store-using-java"></a>Java를 사용하여 Data Lake Store로 최종 사용자 인증
> [!div class="op_single_selector"]
> * [Java 사용](data-lake-store-end-user-authenticate-java-sdk.md)
> * [.NET SDK 사용](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Python 사용](data-lake-store-end-user-authenticate-python.md)
> * [REST API 사용](data-lake-store-end-user-authenticate-rest-api.md)
> 
>   

이 문서에서는 Java SDK를 사용하여 Azure Data Lake Store로 최종 사용자 인증을 수행하는 방법을 배웁니다. Java SDK를 사용하는 Data Lake Store 서비스 간 인증의 경우 [Java를 사용한 Data Lake Store의 서비스 간 인증](data-lake-store-service-to-service-authenticate-java.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

* **Azure Active Directory "네이티브" 응용 프로그램을 만듭니다**. [Azure Active Directory를 사용하여 Data Lake Store로 최종 사용자 인증](data-lake-store-end-user-authenticate-using-active-directory.md)의 단계를 완료해야 합니다.

* [Maven](https://maven.apache.org/install.html) 이 자습서에서는 빌드 및 프로젝트 종속성을 위해 Maven을 사용합니다. Maven 또는 Gradle과 같은 빌드 시스템을 사용하지 않고 빌드할 수 있지만 이러한 시스템 생성은 종속성을 훨씬 쉽게 관리할 수 있습니다.

* (선택 사항)[IntelliJ IDEA](https://www.jetbrains.com/idea/download/) 또는 [Eclipse](https://www.eclipse.org/downloads/)나 유사한 IDE

## <a name="end-user-authentication"></a>최종 사용자 인증
1. 명령줄에서 [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html)을 사용하거나 IDE에서 Maven 프로젝트를 만듭니다. IntelliJ를 사용하여 Java 프로젝트를 만드는 방법에 대한 자세한 내용은 [여기](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html)를 참조하세요. Eclipse를 사용하여 프로젝트를 만드는 방법에 대한 자세한 내용은 [여기](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm)를 참조하세요.

2. Maven **pom.xml** 파일에 다음 종속성을 추가합니다. **\</project>** 태그 앞에 다음 코드 조각을 추가합니다.
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.2.3</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>
   
    첫 번째 종속성은 Maven 리포지토리에서 Data Lake Store SDK(`azure-data-lake-store-sdk`)를 사용하는 것입니다. 두 번째 종속성은 이 응용 프로그램에 사용하는 로깅 프레임워크(`slf4j-nop`)를 지정하는 것입니다. Data Lake Store SDK는 [slf4j](http://www.slf4j.org/) 로깅 외관을 사용하며 이로 인해 log4j, Java 로깅, logback 혹은 로깅 없음과 같은 다양하고 인기 있는 로깅 프레임워크를 선택할 수 있습니다. 이 예제에서 로깅을 비활성화하므로 **slf4j-nop** 바인딩을 사용합니다. 앱에서 다른 로깅 옵션을 사용하려면 [여기](http://www.slf4j.org/manual.html#projectDep)를 참조하세요.

3. 응용 프로그램에 다음 import 문을 추가합니다.

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.DeviceCodeTokenProvider;

4. Java 응용 프로그램에서 다음 코드 조각을 사용하여 앞에서 `DeviceCodeTokenProvider` 명령으로 만든 Active Directory 네이티브 응용 프로그램에 대한 토큰을 가져옵니다. **FILL-IN-HERE**를 Azure Active Directory 네이티브 응용 프로그램의 실제 값으로 바꿉니다.

        private static String nativeAppId = "FILL-IN-HERE";
            
        AccessTokenProvider provider = new DeviceCodeTokenProvider(nativeAppId);   

Data Lake Store SDK는 Data Lake Store 계정에 대해 이야기하는 데 필요한 보안 토큰을 관리할 수 있는 편리한 방법을 제공합니다. 그러나 SDK에서는 이러한 방법만 필수로 사용해야 하는 것은 아닙니다. [Azure Active Directory SDK](https://github.com/AzureAD/azure-activedirectory-library-for-java) 또는 사용자 지정 코드를 사용할 뿐만 아니라 토큰을 가져오는 다른 방법을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 최종 사용자 인증을 사용하여 Java SDK로 Azure Data Lake Store를 인증하는 방법을 배웠습니다. 이제 다음 문서를 통해 Java SDK를 Azure Data Lake Store와 함께 사용하는 방법을 살펴볼 수 있습니다.

* [Java SDK를 사용한 Data Lake Store에서의 데이터 작업](data-lake-store-get-started-java-sdk.md)


