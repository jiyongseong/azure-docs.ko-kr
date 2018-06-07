---
title: "Azure Import/Export 가져오기 작업을 위해 하드 드라이브를 준비하는 샘플 워크플로 - v1 | Microsoft Docs"
description: "Azure Import/Export 서비스에서 가져오기 작업을 위해 드라이브를 준비하는 전체 과정에 대한 연습을 참조하세요."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 6eb1b1b7-c69f-4365-b5ef-3cd5e05eb72a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 66e85bd3e9e43ae360d0507f5bdf3596abbeb7d1
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2017
---
# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>가져오기 작업을 위해 하드 드라이브를 준비하는 샘플 워크플로
이 항목에서는 가져오기 작업을 위해 드라이브를 준비하는 전체 과정을 설명합니다.  
  
이 예제에서는 `mystorageaccount`라는 Window Azure Storage 계정에 다음 데이터를 가져옵니다.  
  
|위치|설명|  
|--------------|-----------------|  
|H:\Video|비디오 컬렉션, 총 5TB|  
|H:\Photo|사진 컬렉션, 총 30GB|  
|K:\Temp\FavoriteMovie.ISO|Blu-Ray™ 디스크 이미지, 25GB|  
|\\\bigshare\john\music|네트워크 공유에서 음악 파일 컬렉션, 총 10GB|  
  
가져오기 작업은 저장소 계정의 다음 대상으로 이 데이터를 가져옵니다.  
  
|원본|대상 가상 디렉터리 또는 Blob|  
|------------|-------------------------------------------|  
|H:\Video|https://mystorageaccount.blob.core.windows.net/video|  
|H:\Photo|https://mystorageaccount.blob.core.windows.net/photo|  
|K:\Temp\FavoriteMovie.ISO|https://mystorageaccount.blob.core.windows.net/favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|https://mystorageaccount.blob.core.windows.net/music|  
  
이 매핑을 사용하여 파일을 `H:\Video\Drama\GreatMovie.mov`Blob으로 가져오게 됩니다`https://mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov`.  
  
다음으로 필요한 하드 드라이브 수를 확인하려면 데이터의 크기를 계산합니다.  
  
`5TB + 30GB + 25GB + 10GB = 5TB + 65GB`  
  
이 예제에서는 두 개의 3TB 하드 드라이브면 충분합니다. 그러나 원본 디렉터리 `H:\Video`에 5TB의 데이터가 있고 단일 하드 드라이브의 용량이 3TB인 경우 Microsoft Azure Import/Export 도구를 실행하기 전에 `H:\Video`를 두 개의 작은 디렉터리인, `H:\Video1` 및 `H:\Video2`로 나누어야 합니다. 이 단계에서는 다음과 같은 원본 디렉터리를 생성합니다.  
  
|위치|크기|대상 가상 디렉터리 또는 Blob|  
|--------------|----------|-------------------------------------------|  
|H:\Video1|2.5TB|https://mystorageaccount.blob.core.windows.net/video|  
|H:\Video2|2.5TB|https://mystorageaccount.blob.core.windows.net/video|  
|H:\Photo|30GB|https://mystorageaccount.blob.core.windows.net/photo|  
|K:\Temp\FavoriteMovies.ISO|25GB|https://mystorageaccount.blob.core.windows.net/favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|10 GB|https://mystorageaccount.blob.core.windows.net/music|  
  
 `H:\Video` 디렉터리가 두 개의 디렉터리로 분할되었더라도 저장소 계정에서 동일한 대상 가상 디렉터리를 가리키도록 합니다. 이러한 방식으로 모든 비디오 파일을 저장소 계정의 `video` 단일 컨테이너에 유지합니다.  
  
 다음으로 이전 원본 디렉터리가 두 하드 드라이브에 고르게 분산됩니다.  
  
||||  
|-|-|-|  
|하드 드라이브|원본 디렉터리|총 크기|  
|첫 번째 드라이브|H:\Video1|2.5TB + 30GB|  
||H:\Photo||  
|두 번째 드라이브|H:\Video2|2.5TB + 35GB|  
||K:\Temp\BlueRay.ISO||  
||\\\bigshare\john\music||  
  
또한 모든 파일에 다음 메타데이터를 설정할 수 있습니다.  
  
-   **UploadMethod:** Windows Azure Import/Export 서비스  
  
-   **DataSetName:** SampleData  
  
-   **CreationDate:** 2013/10/1  
  
가져온 파일의 메타데이터를 설정하려면 다음과 같은 내용으로 텍스트 파일인 `c:\WAImportExport\SampleMetadata.txt`를 만듭니다.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>  
    <DataSetName>SampleData</DataSetName>  
    <CreationDate>10/1/2013</CreationDate>  
</Metadata>  
```
  
`FavoriteMovie.ISO` Blob의 일부 속성을 설정할 수도 있습니다.  
  
-   **Content-Type:** application/octet-stream  
  
-   **Content-MD5:** Q2hlY2sgSW50ZWdyaXR5IQ==  
  
-   **Cache-Control:** no-cache  
  
이러한 속성을 설정하려면 텍스트 파일인 `c:\WAImportExport\SampleProperties.txt`을 만듭니다.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
    <Content-Type>application/octet-stream</Content-Type>  
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>  
    <Cache-Control>no-cache</Cache-Control>  
</Properties>  
```
  
이제 Azure Import/Export 도구를 실행하여 두 하드 드라이브를 준비할 수 있습니다. 다음 사항에 유의하세요.  
  
-   첫 번째 드라이브는 드라이브 X로 탑재됩니다.  
  
-   두 번째 드라이브는 드라이브 Y로 탑재됩니다.  
  
-   저장소 계정 `mystorageaccount`에 대한 키는 `8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg==`입니다.  

## <a name="preparing-disk-for-import-when-data-is-pre-loaded"></a>데이터를 미리 로드하는 경우 가져오기에 대한 디스크 준비
 
 가져올 데이터가 이미 디스크에 있으면 /skipwrite 플래그를 사용합니다. /t와 /srcdir의 값은 모두 가져오기에 대해 준비된 디스크를 가리켜야 합니다. 가져올 데이터 전부가 동일한 대상 가상 디렉터리 또는 저장소 계정의 루트로 이동되지는 않을 경우 각 대상 디렉터리에 대해 별도로 동일한 명령을 실행하고 모든 실행에서 /id 값을 동일하게 유지합니다.

>[!NOTE] 
>디스크에서 데이터를 초기화하기 때문에 /format을 지정하지 않습니다. 디스크가 암호화되었는지 여부에 따라 /encrypt 또는 /bk를 지정할 수 있습니다. 
>

```
    When data is already present on the disk for each drive run the following command.
    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:x:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt /skipwrite
```

## <a name="copy-sessions---first-drive"></a>복사 세션 - 첫 번째 드라이브

첫 번째 드라이브의 경우 Azure Import/Export 도구를 두 번 실행하여 두 원본 디렉터리를 복사합니다.  

**첫 번째 복사 세션**
  
```
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:H:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```

**두 번째 복사 세션**

```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Photo /srcdir:H:\Photo /dstdir:photo/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt
```

## <a name="copy-sessions---second-drive"></a>복사 세션 - 두 번째 드라이브
 
두 번째 드라이브의 경우 Azure Import/Export 도구를 원본 디렉터리에 대해 각각 한 번씩, 독립 실행형 Blu-Ray™ 이미지 파일에 대해 한 번, 총 세 번 실행합니다.  
  
**첫 번째 복사 세션** 

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:Video2 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:y /format /encrypt /srcdir:H:\Video2 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```
  
**두 번째 복사 세션**

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:Music /srcdir:\\bigshare\john\music /dstdir:music/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```  
  
**세 번째 복사 세션**  

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:BlueRayIso /srcfile:K:\Temp\BlueRay.ISO /dstblob:favorite/BlueRay.ISO /MetadataFile:c:\WAImportExport\SampleMetadata.txt /PropertyFile:c:\WAImportExport\SampleProperties.txt  
```

## <a name="copy-session-completion"></a>복사 세션 완료

복사 세션을 완료하면 복사 컴퓨터에서 두 개의 드라이브 연결을 끊고 적절한 Windows Azure 데이터 센터로 이동할 수 있습니다. [Azure Portal](https://portal.azure.com)에서 가져오기 작업을 만들 때 두 개의 저널 파일인 `FirstDrive.jrn` 및 `SecondDrive.jrn`을 업로드합니다.  
  
## <a name="next-steps"></a>다음 단계

* [가져오기 작업을 위한 하드 드라이브 준비](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [자주 사용 되는 명령에 대한 빠른 참조](../storage-import-export-tool-quick-reference-v1.md) 
