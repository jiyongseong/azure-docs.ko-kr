---
title: Azure Media Services의 저장소 계정 | Microsoft Docs
description: 이 문서에서는 Media Services에서 저장소 계정을 사용하는 방법을 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 6d4c21867b0b46508f348300ae2b9553a75d23b2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="storage-accounts"></a>Storage 계정

Media Services 계정을 만들려면 Azure Storage 계정 리소스의 이름을 제공해야 합니다. 지정된 저장소 계정은 Media Services 계정에 연결됩니다. 

**기본** 저장소 계정은 하나 있어야 하며 Media Services 계정과 연결된 **보조** 저장소 계정은 여러 개 사용할 수 있습니다. Media Services는 **범용 v2**(GPv2) 또는 **범용 v1**(GPv1) 계정을 지원합니다. 

>[!NOTE]
> Blob 전용 계정은 **기본**으로 허용되지 않습니다. 

핫 저장소 계층과 쿨 저장소 계층 중 하나를 선택할 수 있도록 GPv2를 사용하는 것이 좋습니다. 저장소 계정에 대해 자세히 알아보려면 [Azure Storage 계정 옵션](../../storage/common/storage-account-options.md)을 참조하세요. 

## <a name="assets-in-a-storage-account"></a>저장소 계정의 자산

Media Services v3에서는 Storage API를 사용하여 파일을 업로드합니다. Media Services에서 Storage API를 사용하여 입력 파일을 업로드하는 방법을 보려면 [로컬 파일에서 작업 입력 만들기](job-input-from-local-file-how-to.md)를 참조하세요. 

> [!Note]
> Media Service API를 사용하지 않고 Media Services SDK에서 생성된 Blob 컨테이너의 콘텐츠를 변경하려고 하면 안 됩니다.

## <a name="next-steps"></a>다음 단계

Media Services 계정에 저장소 계정을 연결하는 방법을 알아보려면 [계정 만들기](create-account-cli-quickstart.md)를 참조하세요.
