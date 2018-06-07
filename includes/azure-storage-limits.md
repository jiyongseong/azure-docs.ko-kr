---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/03/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 6381f8f0e68853183fc3e17e76b4ab93b152b48b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
| 리소스 | 기본 제한 |
| --- | --- |
| 지역당 저장소 계정 수 | 200<sup>1</sup> |
| 최대 저장소 계정 용량 | 500TiB<sup>2</sup> |
| 저장소 계정당 Blob 컨테이너, Blob, 파일 공유, 테이블, 큐, 엔터티 또는 메시지의 최대 수 | 제한 없음 |
| 저장소 계정당 최대 요청 속도 | 초당 20,000개 요청<sup>2</sup> |
| 저장소 계정당 최대 수신<sup>3</sup> 속도(미국 지역) | RA-GRS/GRS를 사용하는 경우 10Gbps, LRS/ZRS의 경우 20Gbps<sup>4</sup> |
| 저장소 계정당 최대 송신<sup>3</sup> 속도(미국 지역) | RA-GRS/GRS를 사용하는 경우 20Gbps, LRS/ZRS의 경우 30Gbps<sup>4</sup> |
| 저장소 계정당 최대 수신<sup>3</sup> 속도(미국 이외 지역) | RA-GRS/GRS를 사용하는 경우 5Gbps, LRS/ZRS의 경우 10Gbps<sup>4</sup> |
| 저장소 계정당 최대 송신<sup>3</sup> 속도(미국 이외 지역) | RA-GRS/GRS를 사용하는 경우 10Gbps, LRS/ZRS의 경우 15Gbps<sup>4</sup> |

<sup>1</sup> 표준 및 프리미엄 저장소 계정이 모두 포함됩니다. 저장소 계정이 200개 이상 필요한 경우 [Azure 지원](https://azure.microsoft.com/support/faq/)에 요청합니다. Azure Storage 팀이 비즈니스 사례를 검토하고 Storage 계정을 250개까지 승인할 수 있습니다. 

<sup>2</sup> 저장소 계정에 대해 한도 확장이 필요한 경우 [Azure Support](https://azure.microsoft.com/support/faq/)에 문의하세요. Azure Storage 팀에서 해당 요청을 검토하여 상황별로 더 높은 제한을 승인할 수 있습니다. 범용 및 Blob 저장소 계정은 요청에 따라 증가된 용량, 수신/송신 및 요청 속도가 지원됩니다. Blob 저장소 계정의 새로운 최댓값은 [용량 및 확장성이 향상된 저장소 계정 발표](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)를 참조하세요.

<sup>3</sup> 계정의 입/출력 제한만으로 한정됩니다. *수신*은 저장소 계정에 보내는 모든 데이터(요청)를 나타냅니다. *송신* 은 저장소 계정에서 수신되는 모든 데이터(응답)를 가리킵니다.  

<sup>4</sup> Azure Storage 중복 옵션은 다음과 같습니다.
* **RA-GRS**: 읽기 액세스 지역 중복 저장소. RA-GRS를 사용하는 경우 보조 위치에 대한 송신 대상은 기본 위치에 대한 송신 대상과 동일합니다.
* **GRS**: 지역 중복 저장소 
* **ZRS**: 영역 중복 저장소.
* **LRS**: 로컬 중복 저장소 
