---
title: "Azure Import/Export 도구 문제 해결 | Microsoft Docs"
description: "Azure Import/Export 도구를 사용할 때 발생하는 일반적인 문제와 이를 처리하는 방법을 알아봅니다."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: b91ca5eb-c557-460a-9afc-0590b38471f9
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: 7bfda602dbc0ea47828a7c9243b8b9b09ec78432
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-the-azure-importexport-tool"></a>Azure Import/Export 도구 문제 해결
Microsoft Azure Import/Export 도구는 문제가 발생하는 경우 오류 메시지를 반환합니다. 이 항목에는 사용자가 실행할 수 있는 몇 가지 일반적인 문제가 나와 있습니다.  
  
## <a name="a-copy-session-fails-what-i-should-do"></a>복사 세션에 실패하면 어떻게 해야 하나요?  
 복사 세션이 실패하면 두 가지 옵션이 있습니다.  
  
 네트워크 공유가 잠시 오프라인 상태였다가 다시 온라인 상태가 된 경우와 같이 오류를 다시 시도할 수 있는 경우 복사 세션을 다시 시작할 수 있습니다. 명령줄 매개 변수에서 잘못된 원본 파일 디렉터리를 지정한 경우와 같이 오류를 다시 시도할 수 없는 경우 복사 세션을 중단해야 합니다. 복사 세션을 다시 시작하고 중단하는 방법에 대한 자세한 내용은 [가져오기 작업을 위한 하드 드라이브 준비](../storage-import-export-tool-preparing-hard-drives-import-v1.md)를 참조하세요.  
  
## <a name="i-cant-resume-or-abort-a-copy-session"></a>복사 세션을 중단하거나 다시 시작할 수 없습니다.  
 복사 세션이 드라이브에 대한 첫 번째 복사 세션인 경우 "첫 번째 복사 세션은 다시 시작하거나 중단할 수 없습니다."라는 오류 메시지가 표시됩니다. 이 경우 이전 저널 파일을 삭제하고 명령을 다시 실행하면 됩니다.  
  
 복사 세션이 드라이브에 대한 첫 번째 세션이 아닌 경우 언제든지 다시 시작하거나 중단될 수 있습니다.  
  
## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>저널 파일이 손실되었습니다. 여전히 작업을 만들 수 있나요?  
 드라이브의 저널 파일에는 이 드라이브에 데이터를 복사하는 데 필요한 전체 정보가 포함되어 있으며 드라이브에 파일을 더 추가해야 하고 가져오기 작업을 만드는 데 사용됩니다. 저널 파일이 손실된 경우 드라이브에 대한 모든 복사 세션을 다시 실행해야 합니다.  
  
## <a name="next-steps"></a>다음 단계
 
* [Azure Import/Export 도구 설정](../storage-import-export-tool-setup-v1.md)   
* [가져오기 작업을 위한 하드 드라이브 준비](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [복사 로그 파일을 사용하여 작업 상태 검토](../storage-import-export-tool-reviewing-job-status-v1.md)   
* [가져오기 작업 복구](../storage-import-export-tool-repairing-an-import-job-v1.md)   
* [내보내기 작업 복구](../storage-import-export-tool-repairing-an-export-job-v1.md)
