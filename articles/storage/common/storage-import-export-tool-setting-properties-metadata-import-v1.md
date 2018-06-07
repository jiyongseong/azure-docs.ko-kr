---
title: "Azure Import/Export를 사용하여 속성 및 메타데이터 설정 - v1 | Microsoft Docs"
description: "Azure Import/Export 도구를 실행하여 드라이브를 준비할 때 대상 BLOB에 설정할 속성과 메타데이터를 지정하는 방법을 알아봅니다. Import/Export 도구 v1을 나타냅니다."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: e8541695-bcfb-4bf0-84d9-72c9de32a0a8
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 77bdaa5559de86cd1de9f30e70656e47fd5719e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="setting-properties-and-metadata-during-the-import-process"></a>가져오기 프로세스 중에 속성 및 메타데이터 설정
Microsoft Azure Import/Export 도구를 실행하여 드라이브를 준비할 때 대상 BLOB에 설정할 속성과 메타데이터를 지정할 수 있습니다. 다음 단계를 수행하세요.  
  
1.  Blob 속성을 설정하려면 속성 이름 및 값을 지정하는 텍스트 파일을 로컬 컴퓨터에 만듭니다.  
  
2.  Blob 메타데이터를 설정하려면 메타데이터 이름 및 값을 지정하는 텍스트 파일을 로컬 컴퓨터에 만듭니다.  
  
3.  이러한 파일 중 하나 또는 둘 모두의 전체 경로를 `PrepImport` 작업의 일부로 Azure Import/Export 도구에 전달합니다.  
  
> [!NOTE]
>  속성 또는 메타데이터 파일을 복사 세션의 일부로 지정하면 해당 복사 세션의 일부로 가져오는 모든 Blob에 대해 해당 속성 또는 메타데이터가 설정됩니다. 가져올 일부 Blob에 대해 다른 속성 또는 메타데이터 집합을 지정하려면 다른 속성이나 메타데이터 파일을 사용하여 별도의 복사 세션을 만들어야 합니다.  
  
## <a name="specify-blob-properties-in-a-text-file"></a>텍스트 파일에서 Blob 속성 지정  
Blob 속성을 지정하려면 로컬 텍스트 파일을 만들고 속성 이름을 요소로, 속성 값을 값으로 지정하는 XML을 포함합니다. 다음은 몇 가지 속성 값을 지정하는 예입니다.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
    <Content-Type>application/octet-stream</Content-Type>  
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>  
    <Cache-Control>no-cache</Cache-Control>  
</Properties>  
```
  
`C:\WAImportExport\ImportProperties.txt` 같은 로컬 위치에 파일을 저장합니다.  
  
## <a name="specify-blob-metadata-in-a-text-file"></a>텍스트 파일에서 Blob 메타데이터를 지정합니다.  
마찬가지로 Blob 메타데이터를 지정하려면 메타데이터 이름을 요소로, 메타데이터 값을 값으로 지정하는 로컬 텍스트 파일을 만듭니다. 다음은 몇 가지 메타데이터 값을 지정하는 예입니다.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>  
    <DataSetName>SampleData</DataSetName>  
    <CreationDate>10/1/2013</CreationDate>  
</Metadata>  
```
  
`C:\WAImportExport\ImportMetadata.txt` 같은 로컬 위치에 파일을 저장합니다.  
  
## <a name="create-a-copy-session-including-the-properties-or-metadata-files"></a>속성 또는 메타데이터 파일을 포함하는 복사 세션 만들기  
Azure Import/Export 도구를 실행하여 가져오기 작업을 준비할 때 `PropertyFile` 매개 변수를 사용하여 명령줄에서 속성 파일을 지정합니다. `/MetadataFile` 매개 변수를 사용하여 명령줄에서 메타데이터 파일을 지정합니다. 다음은 두 파일을 지정하는 예입니다.  
  
```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:BlueRayIso /srcfile:K:\Temp\BlueRay.ISO /dstblob:favorite/BlueRay.ISO /MetadataFile:c:\WAImportExport\SampleMetadata.txt /PropertyFile:c:\WAImportExport\SampleProperties.txt  
```
  
## <a name="next-steps"></a>다음 단계

* [가져오기-내보내기 서비스 메타데이터 및 속성 파일 형식](../storage-import-export-file-format-metadata-and-properties.md)
