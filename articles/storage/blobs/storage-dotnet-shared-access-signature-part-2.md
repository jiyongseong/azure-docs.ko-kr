---
title: "Azure Blob Storage를 사용하여 SAS(공유 액세스 서명) 만들기 및 사용 | Microsoft Docs"
description: "이 자습서에서는 Blob Storage에서 사용할 공유 액세스 서명을 만드는 방법과 클라이언트 응용 프로그램에서 이를 사용하는 방법을 보여 줍니다."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 491e0b3c-76d4-4149-9a80-bbbd683b1f3e
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/15/2017
ms.author: tamram
ms.openlocfilehash: 9dde12acde748c48b56f9f96ee772fca49954358
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="shared-access-signatures-part-2-create-and-use-a-sas-with-blob-storage"></a>공유 액세스 서명, 2부: Blob 저장소를 사용하여 SAS 만들기 및 사용

[1부](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 에서는 SAS(공유 액세스 서명)에 대해 설명하고 SAS 사용을 위한 모범 사례를 살펴보았습니다. 2부에서는 Blob 저장소를 사용하여 공유 액세스 서명을 생성한 다음 사용하는 방법에 대해 살펴봅니다. 예제는 C#으로 작성되었으며 Azure Storage Client Library for .NET을 사용합니다. 이 자습서의 예제:

* 컨테이너에서 공유 액세스 서명 생성
* Blob에서 공유 액세스 서명 생성
* 컨테이너의 리소스에서 서명을 관리하는 저장된 액세스 정책 만들기
* 클라이언트 응용 프로그램에서 공유 액세스 서명 테스트

## <a name="about-this-tutorial"></a>이 자습서 정보
이 자습서에서는 두 개의 콘솔 응용 프로그램을 만들어 컨테이너 및 Blob에 대한 공유 액세스 서명을 만들고 사용하는 방법을 설명합니다.

**응용 프로그램 1**: 관리 응용 프로그램입니다. 컨테이너 및 Blob에 대한 공유 액세스 서명을 생성합니다. 소스 코드에 있는 저장소 계정 액세스 키를 포함합니다.

**응용 프로그램 2**: 클라이언트 응용 프로그램입니다. 첫 번째 응용 프로그램에서 만든 공유 액세스 서명을 사용하여 컨테이너 및 Blob 리소스에 액세스합니다. 공유 액세스 서명을 사용하여 컨테이너 및 Blob 리소스에 액세스합니다. 저장소 계정 액세스 키를 포함하지 *않습니다*.

## <a name="part-1-create-a-console-application-to-generate-shared-access-signatures"></a>1부: 공유 액세스 서명을 생성하는 콘솔 응용 프로그램 만들기
먼저 Azure Storage Client Library for .NET을 설치했는지 확인합니다. 클라이언트 라이브러리에 대한 최신 어셈블리가 포함된 [NuGet 패키지](http://nuget.org/packages/WindowsAzure.Storage/ "NuGet 패키지")를 설치할 수 있습니다. 최신 수정 사항이 있는지를 확인하는 권장되는 방법입니다. 클라이언트 라이브러리를 최신 버전 [.NET용 Azure SDK](https://azure.microsoft.com/downloads/)의 일부로 다운로드할 수도 있습니다.

Visual Studio에서 새 Windows 콘솔 응용 프로그램을 만들고 이름을 **GenerateSharedAccessSignatures**로 지정합니다. 다음 중 한 가지 방법을 사용하여 [Microsoft.WindowsAzure.ConfigurationManager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager) 및 [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/)에 대한 참조를 추가합니다.

* Visual Studio에서 [NuGet 패키지 관리자](https://docs.nuget.org/consume/installing-nuget)를 사용합니다. **프로젝트** > **NuGet 패키지 관리**를 선택하여 각 패키지(Microsoft.WindowsAzure.ConfigurationManager 및 WindowsAzure.Storage)를 온라인으로 검색하고 설치합니다.
* 또는 Azure SDK 설치에서 이러한 어셈블리를 찾은 다음 참조를 추가합니다.
  * Microsoft.WindowsAzure.Configuration.dll
  * Microsoft.WindowsAzure.Storage.dll

Program.cs 파일의 맨 위에 다음 **using** 지시문을 추가합니다.

```csharp
using System.IO;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
```

저장소 계정을 가리키는 연결 문자열과 함께 구성 설정을 포함하도록 app.config 파일을 편집합니다. app.config 파일은 다음과 비슷합니다.

```xml
<configuration>
  <startup>
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
  </startup>
  <appSettings>
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey"/>
  </appSettings>
</configuration>
```

### <a name="generate-a-shared-access-signature-uri-for-a-container"></a>컨테이너에 대한 공유 액세스 서명 URI 생성
먼저 새 컨테이너에서 공유 액세스 서명을 생성하는 메서드를 추가합니다. 이 경우 서명은 저장된 액세스 정책과 연결되지 않으므로, URI에서 만료 시간과 허용된 권한을 나타내는 정보를 전달합니다.

먼저 **Main()** 메서드에 저장소 계정에 대한 액세스를 인증하고 새 컨테이너를 만드는 코드를 추가합니다.

```csharp
static void Main(string[] args)
{
    //Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container to use for the sample code, and create it if it does not exist.
    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
    container.CreateIfNotExists();

    //Insert calls to the methods created below here...

    //Require user input before closing the console window.
    Console.ReadLine();
}
```

그런 다음 컨테이너에 대한 공유 액세스 서명을 생성하고 서명 URI를 반환하는 메서드를 추가합니다.

```csharp
static string GetContainerSasUri(CloudBlobContainer container)
{
    //Set the expiry time and permissions for the container.
    //In this case no start time is specified, so the shared access signature becomes valid immediately.
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
    sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Write;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

**Main()** 메서드의 맨 아래에 다음 줄을 추가하여 **Console.ReadLine()**에 대한 호출 앞에 **GetContainerSasUri()**를 호출하고 콘솔 창에 서명 URI를 작성합니다.

```csharp
//Generate a SAS URI for the container, without a stored access policy.
Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
Console.WriteLine();
```

새 컨테이너에 대한 공유 액세스 서명 URI를 출력하도록 컴파일 및 실행합니다. URI는 다음과 비슷합니다.

```
https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D
```

코드를 실행하면 컨테이너에서 만든 공유 액세스 서명은 다음 24시간동안 유효합니다. 서명은 클라이언트에게 컨테이너에 있는 Blob을 나열하고 컨테이너에 새 Blob을 쓸 수 있는 권한을 부여합니다.

### <a name="generate-a-shared-access-signature-uri-for-a-blob"></a>Blob에 대한 공유 액세스 서명 URI 생성
이제 컨테이너 내에서 새 Blob을 만들고 해당 Blob에 대한 공유 액세스 서명을 생성하는 비슷한 코드를 작성합니다. 이 공유 액세스 서명은 저장된 액세스 정책과 연결되지 않으므로, URI에 대한 시작 시간, 만료 시간 및 권한 정보를 포함합니다.

새 Blob을 만들고 이 Blob에 일부 텍스트를 쓴 다음 공유 액세스 서명을 생성하고 서명 URI를 반환하는 새 메서드를 추가합니다.

```csharp
static string GetBlobSasUri(CloudBlobContainer container)
{
    //Get a reference to a blob within the container.
    CloudBlockBlob blob = container.GetBlockBlobReference("sasblob.txt");

    //Upload text to the blob. If the blob does not yet exist, it will be created.
    //If the blob does exist, its existing content will be overwritten.
    string blobContent = "This blob will be accessible to clients via a shared access signature (SAS).";
    blob.UploadText(blobContent);

    //Set the expiry time and permissions for the blob.
    //In this case, the start time is specified as a few minutes in the past, to mitigate clock skew.
    //The shared access signature will be valid immediately.
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
    sasConstraints.SharedAccessStartTime = DateTimeOffset.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;

    //Generate the shared access signature on the blob, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

**Main()** 메서드의 맨 아래에 **GetBlobSasUri()**를 호출하는 다음 줄을 추가하여 **Console.ReadLine()**에 대한 호출 앞의 콘솔 창에 공유 액세스 서명 URI를 작성합니다.

```csharp
//Generate a SAS URI for a blob within the container, without a stored access policy.
Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
Console.WriteLine();
```

새 Blob에 대한 공유 액세스 서명 URI를 출력하도록 컴파일 및 실행합니다. URI는 다음과 비슷합니다.

```
https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D
```

### <a name="create-a-stored-access-policy-on-the-container"></a>컨테이너에 대한 저장된 액세스 정책 만들기
이제 연결된 공유 액세스 서명에 대한 제약 조건을 정의하는 컨테이너에 대한 저장된 액세스 정책을 만들어 보겠습니다.

이전 예제에서는 공유 액세스 서명 URI 자체에 대한 시작 시간(암시적 또는 명시적), 만료 시간 및 사용 권한을 지정했습니다. 다음 예제에서는 공유 액세스 서명이 아니라 저장된 액세스 정책에 대해 이러한 설정을 지정합니다. 그러면 공유 액세스 서명을 다시 실행하지 않고 제약 조건을 변경할 수 있습니다.

공유 액세스 서명에 대한 제약 조건 및 저장된 액세스 정책에 대한 나머지를 하나 이상 둘 수 있습니다. 그러나 둘 중 한 곳에서 시작 시간, 만료 시간 및 사용 권한을 지정할 수 있습니다. 예를 들어, 공유 액세스 서명에 대한 사용 권한을 지정하고 저장 액세스 정책을 지정할 수도 없습니다.

저장 액세스 정책을 컨테이너에 추가하면 컨테이너의 기존 사용 권한을 가지고 새 액세스 정책을 추가한 다음 컨테이너의 사용 권한을 설정해야 합니다.

컨테이너에 저장된 액세스 정책을 새로 만드는 새 메서드를 추가하고 정책의 이름을 반환합니다.

```csharp
static void CreateSharedAccessPolicy(CloudBlobClient blobClient, CloudBlobContainer container,
    string policyName)
{
    //Get the container's existing permissions.
    BlobContainerPermissions permissions = container.GetPermissions();

    //Create a new shared access policy and define its constraints.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
    };

    //Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    container.SetPermissions(permissions);
}
```

**Main()** 메서드의 맨 아래에 **Console.ReadLine()**에 대한 호출 앞에 다음 행을 추가하여 먼저 기존 액세스 정책을 지운 다음 **CreateSharedAccessPolicy()** 메서드를 호출합니다.

```csharp
//Clear any existing access policies on container.
BlobContainerPermissions perms = container.GetPermissions();
perms.SharedAccessPolicies.Clear();
container.SetPermissions(perms);

//Create a new access policy on the container, which may be optionally used to provide constraints for
//shared access signatures on the container and the blob.
string sharedAccessPolicyName = "tutorialpolicy";
CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);
```

컨테이너에 대한 액세스 정책을 지우면 먼저 컨테이너의 기존 사용 권한을 가지고 사용 권한을 지운 다음 사용 권한을 다시 설정합니다.

### <a name="generate-a-shared-access-signature-uri-on-the-container-that-uses-an-access-policy"></a>액세스 정책을 사용하는 컨테이너에 대한 공유 액세스 서명 URI 생성
이제 이전에 만든 컨테이너에 대한 다른 공유 액세스 서명을 만듭니다. 이번에는 이전 예제에서 만든 저장 액세스 정책에 서명을 연결합니다.

컨테이너에 대한 다른 공유 액세스 서명을 생성하는 새 메서드를 추가합니다.

```csharp
static string GetContainerSasUriWithPolicy(CloudBlobContainer container, string policyName)
{
    //Generate the shared access signature on the container. In this case, all of the constraints for the
    //shared access signature are specified on the stored access policy.
    string sasContainerToken = container.GetSharedAccessSignature(null, policyName);

    //Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

**Main()** 메서드의 맨 아래에 **Console.ReadLine()**에 대한 호출 앞에 **GetContainerSasUriWithPolicy** 메서드를 호출하는 다음 줄을 추가합니다.

```csharp
//Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
Console.WriteLine();
```

### <a name="generate-a-shared-access-signature-uri-on-the-blob-that-uses-an-access-policy"></a>액세스 정책을 사용하는 Blob에 대한 공유 액세스 서명 URI 생성
마지막으로 다른 Blob을 만들고 저장 액세스 정책에 연결되는 공유 액세스 서명을 생성하는 비슷한 메서드를 추가합니다.

Blob을 만들고 공유 액세스 서명을 생성하는 새 메서드를 추가합니다.

```csharp
static string GetBlobSasUriWithPolicy(CloudBlobContainer container, string policyName)
{
    //Get a reference to a blob within the container.
    CloudBlockBlob blob = container.GetBlockBlobReference("sasblobpolicy.txt");

    //Upload text to the blob. If the blob does not yet exist, it will be created.
    //If the blob does exist, its existing content will be overwritten.
    string blobContent = "This blob will be accessible to clients via a shared access signature. " +
    "A stored access policy defines the constraints for the signature.";
    MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
    ms.Position = 0;
    using (ms)
    {
        blob.UploadFromStream(ms);
    }

    //Generate the shared access signature on the blob.
    string sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

**Main()** 메서드의 맨 아래에 **Console.ReadLine()**에 대한 호출 앞에 **GetBlobSasUriWithPolicy** 메서드를 호출하는 다음 줄을 추가합니다.

```csharp
//Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
Console.WriteLine();
```

**Main()** 메서드는 전체적으로 다음과 비슷합니다. 이 메서드를 실행하여 콘솔 창에 공유 액세스 서명 URI를 쓴 다음 URI를 복사하여 이 자습서의 2부에서 사용할 텍스트 파일에 붙여넣습니다.

```csharp
static void Main(string[] args)
{
    //Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container to use for the sample code, and create it if it does not exist.
    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
    container.CreateIfNotExists();

    //Generate a SAS URI for the container, without a stored access policy.
    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
    Console.WriteLine();

    //Generate a SAS URI for a blob within the container, without a stored access policy.
    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
    Console.WriteLine();

    //Clear any existing access policies on container.
    BlobContainerPermissions perms = container.GetPermissions();
    perms.SharedAccessPolicies.Clear();
    container.SetPermissions(perms);

    //Create a new access policy on the container, which may be optionally used to provide constraints for
    //shared access signatures on the container and the blob.
    string sharedAccessPolicyName = "tutorialpolicy";
    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

    Console.ReadLine();
}
```

GenerateSharedAccessSignatures 콘솔 응용 프로그램을 실행하면 다음과 비슷한 출력이 표시됩니다. 이는 자습서의 2부에서 사용할 공유 액세스 서명입니다.

```
Container SAS URI: https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=pFlEZD%2F6sJTNLxD%2FQ26Hh85j%2FzYPxZav6mP1KJwnvJE%3D&se=2017-05-16T16%3A16%3A47Z&sp=wl

Blob SAS URI: https://storagesample.blob.core.windows.net/sascontainer/sasblob.txt?sv=2016-05-31&sr=b&sig=%2FiBWAZbXESzCMvRcm7JwJBK0gT0BtPSWEq4pRwmlBRI%3D&st=2017-05-15T16%3A11%3A48Z&se=2017-05-16T16%3A16%3A48Z&sp=rw

Container SAS URI using stored access policy: https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&si=tutorialpolicy&sig=aMb6rKDvvpfiGVsZI2rCmyUra6ZPpq%2BZ%2FLyTgAeec%2Bk%3D

Blob SAS URI using stored access policy: https://storagesample.blob.core.windows.net/sascontainer/sasblobpolicy.txt?sv=2016-05-31&sr=b&si=tutorialpolicy&sig=%2FkTWkT23SS45%2FoF4bK2mqXkN%2BPKs%2FyHuzkfQ4GFoZVU%3D
```

## <a name="part-2-create-a-console-application-to-test-the-shared-access-signatures"></a>2부: 공유 액세스 서명을 테스트하는 콘솔 응용 프로그램 만들기
이전 예제에서 만든 공유 액세스 서명을 테스트하려면 서명을 사용하여 컨테이너 및 Blob에서 작업을 수행하는 두 번째 콘솔 응용 프로그램을 만듭니다.

> [!NOTE]
> 자습서의 1부를 완료한 후 24시간이 초과된 경우 생성된 서명은 더 이상 유효하지 않습니다. 이 경우, 첫 번째 콘솔 응용 프로그램에서 코드를 실행하여 자습서의 2부에서 사용할 공유 액세스 서명을 다시 생성해야 합니다.
>

Visual Studio에서 새 Windows 콘솔 응용 프로그램을 만들고 이름을 **ConsumeSharedAccessSignatures**로 지정합니다. 앞에서 수행한 것과 같은 방법으로 [Microsoft.WindowsAzure.ConfigurationManager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager) 및 [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/)에 참조를 추가합니다.

Program.cs 파일의 맨 위에 다음 **using** 지시문을 추가합니다.

```csharp
using System.IO;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
```

**Main()** 메서드의 본문에 다음 문자열 상수를 추가하고 자습서의 1부에서 생성한 공유 액세스 서명으로 값을 변경합니다.

```csharp
static void Main(string[] args)
{
    const string containerSAS = "<your container SAS>";
    const string blobSAS = "<your blob SAS>";
    const string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    const string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
}
```

### <a name="add-a-method-to-try-container-operations-using-a-shared-access-signature"></a>공유 액세스 서명을 사용하여 컨테이너 작업을 수행하는 메서드 추가
이제 컨테이너에서 공유 액세스 서명을 사용하여 일부 컨테이너 작업을 테스트하는 메서드를 추가합니다. 공유 액세스 서명은 서명만을 기반으로 하여 컨테이너에 대한 액세스를 인증하여 컨테이너에 대한 참조를 반환하는 데 사용됩니다.

Program.cs에 다음 메서드를 추가합니다.

```csharp
static void UseContainerSAS(string sas)
{
    //Try performing container operations with the SAS provided.

    //Return a reference to the container using the SAS URI.
    CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));

    //Create a list to store blob URIs returned by a listing operation on the container.
    List<ICloudBlob> blobList = new List<ICloudBlob>();

    //Write operation: write a new blob to the container.
    try
    {
        CloudBlockBlob blob = container.GetBlockBlobReference("blobCreatedViaSAS.txt");
        string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
        blob.UploadText(blobContent);

        Console.WriteLine("Write operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Write operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //List operation: List the blobs in the container.
    try
    {
        foreach (ICloudBlob blob in container.ListBlobs())
        {
            blobList.Add(blob);
        }
        Console.WriteLine("List operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("List operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //Read operation: Get a reference to one of the blobs in the container and read it.
    try
    {
        CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
        MemoryStream msRead = new MemoryStream();
        msRead.Position = 0;
        using (msRead)
        {
            blob.DownloadToStream(msRead);
            Console.WriteLine(msRead.Length);
        }
        Console.WriteLine("Read operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Read operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
    Console.WriteLine();

    //Delete operation: Delete a blob in the container.
    try
    {
        CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
        blob.Delete();
        Console.WriteLine("Delete operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Delete operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
}
```

컨테이너에서 만든 공유 액세스 서명을 모두 사용하여 **UseContainerSAS()**를 호출하도록 **Main()** 메서드를 업데이트합니다.

```csharp
static void Main(string[] args)
{
    string containerSAS = "<your container SAS>";
    string blobSAS = "<your blob SAS>";
    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

    //Call the test methods with the shared access signatures created on the container, with and without the access policy.
    UseContainerSAS(containerSAS);
    UseContainerSAS(containerSASWithAccessPolicy);

    Console.ReadLine();
}
```

### <a name="add-a-method-to-try-blob-operations-using-a-shared-access-signature"></a>공유 액세스 서명을 사용하여 Blob 작업을 수행하는 메서드 추가
마지막으로 Blob에서 공유 액세스 서명을 사용하여 일부 Blob 작업을 테스트하는 메서드를 추가합니다. 이 경우 공유 액세스 서명을 전달하여 Blob에 대한 참조를 반환하는 생성자 **CloudBlockBlob(String)**을 사용합니다. 다른 인증은 필요하지 않으며 서명만을 기반으로 합니다.

Program.cs에 다음 메서드를 추가합니다.

```csharp
static void UseBlobSAS(string sas)
{
    //Try performing blob operations using the SAS provided.

    //Return a reference to the blob using the SAS URI.
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(sas));

    //Write operation: Write a new blob to the container.
    try
    {
        string blobContent = "This blob was created with a shared access signature granting write permissions to the blob. ";
        MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        msWrite.Position = 0;
        using (msWrite)
        {
            blob.UploadFromStream(msWrite);
        }
        Console.WriteLine("Write operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Write operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //Read operation: Read the contents of the blob.
    try
    {
        MemoryStream msRead = new MemoryStream();
        using (msRead)
        {
            blob.DownloadToStream(msRead);
            msRead.Position = 0;
            using (StreamReader reader = new StreamReader(msRead, true))
            {
                string line;
                while ((line = reader.ReadLine()) != null)
                {
                    Console.WriteLine(line);
                }
            }
        }
        Console.WriteLine("Read operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Read operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //Delete operation: Delete the blob.
    try
    {
        blob.Delete();
        Console.WriteLine("Delete operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Delete operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
}
```

Blob에서 만든 공유 액세스 서명을 모두 사용하여 **UseBlobSAS()**를 호출하도록 **Main()** 메서드를 업데이트합니다.

```csharp
static void Main(string[] args)
{
    string containerSAS = "<your container SAS>";
    string blobSAS = "<your blob SAS>";
    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

    //Call the test methods with the shared access signatures created on the container, with and without the access policy.
    UseContainerSAS(containerSAS);
    UseContainerSAS(containerSASWithAccessPolicy);

    //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
    UseBlobSAS(blobSAS);
    UseBlobSAS(blobSASWithAccessPolicy);

    Console.ReadLine();
}
```

콘솔 응용 프로그램을 실행하고 출력을 관찰하여 서명별로 허용되는 작업을 확인합니다. 콘솔 창의 출력은 다음과 비슷합니다.

```
Write operation succeeded for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl

List operation succeeded for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl

Read operation failed for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl
Additional error information: The remote server returned an error: (403) Forbidden.

Delete operation failed for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl
Additional error information: The remote server returned an error: (403) Forbidden.

...
```

## <a name="next-steps"></a>다음 단계

* [공유 액세서 서명, 1부: SAS 모델 이해하기](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [컨테이너 및 Blob에 대한 익명 읽기 권한 관리](storage-manage-access-to-resources.md)
* [공유 액세스 서명을 사용하여 액세스 위임(REST API)](http://msdn.microsoft.com/library/azure/ee395415.aspx)
* [테이블 및 큐 SAS 소개](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
