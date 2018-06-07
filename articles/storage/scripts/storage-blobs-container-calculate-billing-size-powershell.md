---
title: "Azure PowerShell 스크립트 샘플 - Blob 컨테이너 총 청구 크기 계산 | Microsoft Docs"
description: "요금 청구를 위해 Azure Blob Storage에 있는 컨테이너의 총 크기를 계산합니다."
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 11/07/2017
ms.author: fryu
ms.openlocfilehash: c37b416578a76e9b12e29d68e413d851796ccc6f
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2017
---
# <a name="calculate-the-total-billing-size-of-a-blob-container"></a>Blob 컨테이너의 총 청구 크기 계산

이 스크립트는 청구 비용을 예측하기 위해 Azure Blob Storage에 있는 컨테이너의 크기를 계산합니다. 이 스크립트는 컨테이너의 blob 크기를 합산합니다.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> 이 PowerShell 스크립트는 요금 청구를 위해 컨테이너의 크기를 계산합니다. 다른 용도로 컨테이너 크기를 계산하는 경우 [Blob Storage 컨테이너의 전체 크기 계산](../scripts/storage-blobs-container-calculate-size-powershell.md)에서 예상 크기를 제공하는 간단한 스크립트를 참조하세요.

## <a name="determine-the-size-of-the-blob-container"></a>Blob 컨테이너 크기 결정

Blob 컨테이너의 전체 크기에는 컨테이너 자체의 크기와 컨테이너 아래 모든 Blob의 크기가 포함됩니다.

다음 섹션은 Blob 컨테이너 및 Blob에 대한 저장소 용량을 계산하는 방법을 설명합니다. 다음 섹션에서 Len(X)는 문자열의 문자 수를 의미합니다.

### <a name="blob-containers"></a>Blob 컨테이너

다음 계산은 Blob 컨테이너당 소비되는 저장소 양을 계산하는 방법을 설명합니다.

`
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
`

다음은 분석 정보입니다.
* 각 컨테이너의 48바이트 오버헤드에는 마지막으로 수정한 시간, 권한, 공용 설정 및 일부 시스템 메타데이터가 포함됩니다.

* 컨테이너 이름은 유니코드로 저장되므로 문자 수에 2를 곱합니다.

* 저장된 Blob 컨테이너 메타데이터의 각 블록에 대해 이름의 길이(ASCII)와 문자열 값의 길이를 저장합니다.

* 서명된 식별자당 512바이트에는 서명된 식별자 이름, 시작 시간, 만료 시간 및 권한아 포함됩니다.

### <a name="blobs"></a>Blob

다음 계산은 Blob당 소비되는 저장소 양을 계산하는 방법을 보여줍니다.

* 블록 Blob(기본 Blob 또는 스냅숏):

   `
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
   SizeInBytes(data in unique committed data blocks stored) +
   SizeInBytes(data in uncommitted data blocks)
   `

* 페이지 Blob(기본 Blob 또는 스냅숏):

   `
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   number of nonconsecutive page ranges with data * 12 bytes +
   SizeInBytes(data in unique pages stored)
   `

다음은 분석 정보입니다.

* 다음을 포함하는 BLOB에 대한 124 바이트 오버헤드:
    - 마지막으로 수정한 시간
    - 크기
    - Cache-Control
    - 콘텐츠 형식
    - Content-Language
    - Content-Encoding
    - Content-MD5
    - 권한
    - 스냅숏 정보
    - 임대
    - 일부 시스템 메타데이터

* BLOB 이름은 유니코드로 저장되므로 문자 수에 2를 곱합니다.

* 저장된 메타데이터의 각 블록에 대해 이름의 길이(ASCII로 저장됨)와 문자열 값의 길이를 저장합니다.

* 블록 Blob의 경우:
    * 블록 목록에 대한 8바이트.
    * 블록 수 곱하기 블록 ID 크기(바이트).
    * 커밋된 블록과 커밋되지 않은 블록에 있는 데이터의 크기. 
    
    >[!NOTE]
    >스냅숏이 사용되면 이 크기에 기본 또는 스냅숏 Blob에 대한 고유 데이터만 포함됩니다. 커밋되지 않은 블록이 일주일 후에 사용되지 않았다면 가비지 수집됩니다. 그 후 그것들은 청구에 포함되지 않습니다.

* 페이지 Blob의 경우:
    * 데이터 시간이 12바이트인 비연속적인 페이지 범위의 수. **GetPageRanges** API를 호출할 때 표시되는 고유한 페이지 범위의 수입니다.

    * 저장된 모든 페이지의 데이터 크기(바이트). 
    
    >[!NOTE]
    >스냅숏이 사용되면 이 크기에 기본 BLOB 또는 계산된 스냅숏 BLOB에 대한 고유 페이지만 포함됩니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>다음 단계

- 예상 컨테이너 크기를 제공하는 간단한 스크립트를 보려면 [Blob Storage 컨테이너의 전체 크기 계산](../scripts/storage-blobs-container-calculate-size-powershell.md)을 참조하세요.

- Azure Storage 청구에 대한 자세한 내용은 [Windows Azure Storage 청구 이해](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/)를 참조하세요.

- Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.4.1)를 참조하세요.

- 추가 저장소 PowerShell 스크립트 샘플은 [Microsoft Azure Storage에 대한 PowerShell 샘플](../blobs/storage-samples-blobs-powershell.md)에서 찾을 수 있습니다.
