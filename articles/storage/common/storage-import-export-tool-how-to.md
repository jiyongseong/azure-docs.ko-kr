---
title: "Azure Import-Export 도구 사용 | Microsoft Docs"
description: "Import/Export 도구를 사용하여 가져오기 작업을 위한 하드 드라이브 준비, 가져오기 작업 복구 또는 내보내기 작업 복구 방법을 알아봅니다."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: f77535bb-d577-438a-bdd3-e15a82e0c543
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: 20a720833842f9579fd4fccaa39e964def48197e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-azure-importexport-tool"></a>Azure Import/Export 도구 사용 

Azure Import/Export 도구(WAImportExport.exe)는 Azure Import/Export 서비스의 작업을 만들고 관리하는 데 사용되어 Azure Blob Storage 내부 또는 외부로 대량의 데이터를 전송할 수 있게 해줍니다.

이 설명서는 Azure Import/Export 도구의 최신 버전에 대한 것입니다. 클래식 배포 모델을 사용하는 방법에 대한 내용은 [Azure Import/Export 도구 V1 사용](storage-import-export-tool-how-to-v1.md)을 참조하세요.

다음 문서에서는 해당 방법을 보여 줍니다.  

- Azure Import/Export 도구를 설치 및 설정합니다.
- 드라이브에서 Azure Blob Storage로 데이터를 가져오는 작업을 위한 하드 드라이브를 준비합니다.
- 로그 파일 복사를 사용하여 작업 상태를 검토합니다. 
- 가져오기 작업을 복구합니다. 
- 내보내기 작업을 복구합니다. 
- Azure Import/Export 도구 문제 해결 

## <a name="next-steps"></a>다음 단계

* [WAImportExport 도구 설정](storage-import-export-tool-setup.md)