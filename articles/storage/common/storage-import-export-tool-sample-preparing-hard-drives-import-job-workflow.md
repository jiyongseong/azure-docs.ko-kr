---
title: "Azure Import/Export 가져오기 작업을 위해 하드 드라이브를 준비하는 샘플 워크플로 | Microsoft Docs"
description: "Azure Import/Export 서비스에서 가져오기 작업을 위해 드라이브를 준비하는 전체 과정에 대한 연습을 참조하세요."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: muralikk
ms.openlocfilehash: 60139ff36b66432620591ceaf201e046ad30217f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>가져오기 작업을 위해 하드 드라이브를 준비하는 샘플 워크플로

이 문서에서는 가져오기 작업을 위해 드라이브를 준비하는 전체 과정을 설명합니다.

## <a name="sample-data"></a>샘플 데이터

이 예제에서는 `mystorageaccount`라는 Azure Storage 계정에 다음 데이터를 가져옵니다.

|위치|설명|데이터 크기|
|--------------|-----------------|-----|
|H:\Video\ |비디오 컬렉션|12TB|
|H:\Photo\ |사진 컬렉션|30GB|
|K:\Temp\FavoriteMovie.ISO|Blu-Ray™ 디스크 이미지|25GB|
|\\\bigshare\john\music\ |네트워크 공유에서 음악 파일 컬렉션|10 GB|

## <a name="storage-account-destinations"></a>저장소 계정 대상

가져오기 작업은 저장소 계정의 다음 대상으로 데이터를 가져옵니다.

|원본|대상 가상 디렉터리 또는 Blob|
|------------|-------------------------------------------|
|H:\Video\ |video/|
|H:\Photo\ |photo/|
|K:\Temp\FavoriteMovie.ISO|favorite/FavoriteMovies.ISO|
|\\\bigshare\john\music\ |music|

이 매핑을 사용하여 파일을 `H:\Video\Drama\GreatMovie.mov`Blob으로 가져오게 됩니다`https://mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov`.

## <a name="determine-hard-drive-requirements"></a>하드 드라이브 요구 사항 확인

다음으로 필요한 하드 드라이브 수를 확인하려면 데이터의 크기를 계산합니다.

`12TB + 30GB + 25GB + 10GB = 12TB + 65GB`

이 예제에서는 두 개의 8TB 하드 드라이브면 충분합니다. 그러나 원본 디렉터리 `H:\Video`에 12TB의 데이터가 있고 단일 하드 드라이브의 용량이 8TB이기 때문에 **driveset.csv** 파일에서 다음과 같은 방식으로 지정할 수 있습니다.

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
X,Format,SilentMode,Encrypt,
Y,Format,SilentMode,Encrypt,
```
이 도구는 최적화된 방식으로 두 하드 드라이브에 데이터를 배포합니다.

## <a name="attach-drives-and-configure-the-job"></a>드라이브 연결 및 작업 구성
두 디스크를 컴퓨터에 연결하고 볼륨을 만듭니다. 그러면 작성자 **dataset.csv** 파일은 다음과 같습니다.
```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
H:\Video\,video/,BlockBlob,rename,None,H:\mydirectory\properties.xml
H:\Photo\,photo/,BlockBlob,rename,None,H:\mydirectory\properties.xml
K:\Temp\FavoriteVideo.ISO,favorite/FavoriteVideo.ISO,BlockBlob,rename,None,H:\mydirectory\properties.xml
\\myshare\john\music\,music/,BlockBlob,rename,None,H:\mydirectory\properties.xml
```

또한 모든 파일에 다음 메타데이터를 설정할 수 있습니다.

* **UploadMethod:** Windows Azure Import/Export 서비스
* **DataSetName:** SampleData
* **CreationDate:** 2013/10/1

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

* **Content-Type:** application/octet-stream
* **Content-MD5:** Q2hlY2sgSW50ZWdyaXR5IQ==
* **Cache-Control:** no-cache

이러한 속성을 설정하려면 텍스트 파일인 `c:\WAImportExport\SampleProperties.txt`을 만듭니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Properties>
    <Content-Type>application/octet-stream</Content-Type>
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>
    <Cache-Control>no-cache</Cache-Control>
</Properties>
```

## <a name="run-the-azure-importexport-tool-waimportexportexe"></a>Azure Import/Export 도구(WAImportExport.exe) 실행

이제 Azure Import/Export 도구를 실행하여 두 하드 드라이브를 준비할 수 있습니다.

**첫 번째 세션의 경우:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

더 많은 데이터를 추가해야 하는 경우 Initialdataset과 동일한 형식의 다른 데이터 집합 파일을 만듭니다.

**두 번째 세션의 경우:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

복사 세션을 완료하면 복사 컴퓨터에서 두 개의 드라이브 연결을 끊고 적절한 Azure 데이터 센터로 이동할 수 있습니다. Azure Portal에서 가져오기 작업을 만들 때 두 개의 저널 파일인 `<FirstDriveSerialNumber>.xml` 및 `<SecondDriveSerialNumber>.xml`을 업로드합니다.

## <a name="next-steps"></a>다음 단계

* [가져오기 작업을 위한 하드 드라이브 준비](../storage-import-export-tool-preparing-hard-drives-import.md)
* [자주 사용 되는 명령에 대한 빠른 참조](../storage-import-export-tool-quick-reference.md)
