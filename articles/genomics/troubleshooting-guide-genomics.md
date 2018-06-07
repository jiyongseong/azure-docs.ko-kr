---
title: 'Microsoft Genomics: 문제 해결 가이드 | Microsoft Docs'
titleSuffix: Azure
description: 문제 해결 전략에 대해 자세히 알아봅니다.
keywords: 문제 해결, 오류, 디버깅
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: article
ms.date: 04/13/2018
ms.openlocfilehash: 18761c02cc423affe7b1050700e560b1f0b0594d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="troubleshooting-guide"></a>문제 해결 가이드
이 개요에서는 Microsoft Genomics 서비스를 사용할 때 일반적인 문제를 해결하기 위한 전략에 대해 설명합니다. 일반 FAQ는 [일반적인 질문](frequently-asked-questions-genomics.md)을 참조하세요. 


## <a name="how-do-i-check-my-job-status"></a>작업 상태를 확인하려면 어떻게 할까요?
아래와 같이 명령줄에서 `msgen status`를 호출하여 워크플로의 상태를 확인할 수 있습니다. 

```
msgen status -u URL -k KEY -w ID [-f CONFIG] 
```

다음과 같은 세 가지 필수 인수가 있습니다.
* URL - API에 대한 기본 URI
* KEY - Genomics 계정에 대한 액세스 키 
* ID - 워크플로 ID

URL과 KEY를 찾으려면 Azure Portal로 이동하여 Genomics 계정 페이지를 엽니다. **관리** 제목 아래에서 **액세스 키**를 선택하세요. 거기에서 API URL과 액세스 키를 모두 찾을 수 있습니다.

또는 URL과 KEY를 직접 입력하는 대신 구성 파일의 경로를 포함할 수 있습니다. 이러한 인수를 구성 파일뿐만 아니라 명령줄에 포함하면 명령줄 인수가 우선적으로 적용됩니다. 

`msgen status`가 호출되면 워크플로가 성공했는지 여부 또는 작업 실패 이유를 설명하는 사용자에게 친숙한 메시지가 표시됩니다. 


## <a name="get-more-information-about-my-workflow-status"></a>내 워크플로 상태에 대한 추가 정보 얻기

작업이 성공하지 못한 이유에 대한 자세한 정보를 얻으려면 워크플로 중에 생성된 로그 파일을 살펴보면 됩니다. 출력 컨테이너에 `[youroutputfilename].logs.zip` 폴더가 있어야 합니다.  이 폴더의 압축을 풀면 다음 항목이 표시됩니다.

* outputFileList.txt - 워크플로 중에 생성된 출력 파일의 목록입니다.
* standarderror.txt - 이 파일은 비어 있습니다.
* standardoutput.txt - 워크플로의 최상위 로깅이 포함되어 있습니다. 
* GATK 로그 파일 - `logs` 폴더에 있는 다른 모든 파일입니다.

`standardoutput.txt` 파일은 워크플로에 대한 더 낮은 수준의 정보가 포함되어있어 워크플로가 성공하지 못한 이유를 확인하기 위한 좋은 위치입니다. 

## <a name="common-issues-and-how-to-resolve-them"></a>일반적인 문제 및 해결 방법
이 섹션에서는 일반적인 문제와 이를 해결하는 방법에 대해 간략히 설명합니다.

### <a name="fastq-files-are-unmatched"></a>Fastq 파일이 일치하지 않습니다.
Fastq 파일은 샘플 식별자 뒤에 오는 /1 또는 /2만 달라야 합니다. 일치하지 않는 FASTQ 파일을 실수로 제출한 경우 `msgen status`를 호출할 때 다음과 같은 오류 메시지가 표시될 수 있습니다.
* `Encountered an unmatched read`
* `Error reading a FASTQ file, make sure the input files are valid and paired correctly` 

이 문제를 해결하려면 워크플로에 제출된 fastq 파일이 실제로 일치하는 집합인지 검토합니다. 


### <a name="error-uploading-bam-file-output-blob-already-exists-and-the-overwrite-option-was-set-to-false"></a>.bam 파일을 업로드하는 동안 오류가 발생했습니다. 출력 Blob이 이미 있고 덮어쓰기 옵션이 False로 설정되었습니다.
`Error uploading .bam file. Output blob already exists and the overwrite option was set to False`와 같은 오류 메시지가 표시되면 출력 폴더에 이미 이름이 같은 출력 파일이 있습니다.  기존 출력 파일을 삭제하거나 구성 파일에서 덮어쓰기 옵션을 설정합니다. 그런 다음, 워크플로를 다시 제출합니다.

### <a name="when-to-contact-microsoft-genomics-support"></a>Microsoft Genomics 지원에 문의해야 하는 경우
다음 오류 메시지가 표시되면 내부 오류가 발생했습니다. 

* `Error locating input files on worker machine`
* `Process management failure`

워크플로를 다시 제출해 보세요. 작업이 계속 실패하거나 다른 질문이 있는 경우 Azure Portal의 Microsoft Genomics 지원에 문의하세요.

![Azure Portal에서 지원 문의](./media/troubleshooting-guide/genomics-contact-support.png "Azure Portal에서 지원 문의")

## <a name="next-steps"></a>다음 단계
이 문서에서는 Microsoft Genomics 서비스와 관련된 일반적인 문제를 해결하는 방법을 알아보았습니다. 자세한 내용 및 일반적인 FAQ는 [일반적인 질문](frequently-asked-questions-genomics.md)을 참조하세요. 