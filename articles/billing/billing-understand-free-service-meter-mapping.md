---
title: 무료 계정에 대한 미터 매핑 서비스 - Azure | Microsoft Docs
description: 무료 계정에 포함된 서비스에 대한 미터 매핑 서비스를 이해합니다.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: amberb
ms.openlocfilehash: 683a94f25e94faf0eee7c6aa5fbae52132d58f34
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
---
# <a name="understand-free-service-to-meter-mapping"></a>매핑을 계산하는 무료 서비스 이해

모든 Azure 서비스는 Azure 청구 시스템에서 사용자에게 서비스 요금을 청구하는 데 활용하는 미터에 대해 사용량을 내보냅니다. 무료 서비스의 사용량을 보다 잘 이해하기 위해 이러한 서비스에 대한 미터 매핑 서비스를 살펴보겠습니다. 무료 서비스를 만드는 방법에 대한 자세한 내용은 [Azure 무료 계정으로 무료 서비스 만들기](billing-create-free-services-included-free-account.md)를 참조하세요.

## <a name="service-to-meter-mapping-for-free-account-eligible-services"></a>무료 계정에 적합한 서비스에 대한 미터 매핑 서비스 

|    서비스   | Azure Portal에서 미터 이름 | 사용량 파일/API에서 미터 이름 | 측정기 ID |
| ------------ | -------------------------- | -------------------------| -------- |
| B1S Linux VM | 계산 시간 - Standard_B1 VM | 계산 시간 - 무료 | 8260cba2-4437-47d1-a31e-2561cd370f50
| B1S Windows VM | 계산 시간 - Standard_B1 VM(Windows) | 계산 시간 - 무료 | ff3e6fa5-ee46-478e-8d0e-b629f4f8a8ac
| B1S VM - 공용 IP 주소  | IP 주소 시간 - 공용 IP 주소 | IP 주소 시간 - 무료 | ae56b367-2708-4454-a3d9-2be7b2364ea1
| CosmosDB | 저장소(GB) - Cosmos DB | 저장소(GB) - 무료 | 59c78b09-08e2-466a-9f3b-57a94c9e2f31
| CosmosDB | 100 요청 단위(시간) - Cosmos DB | 100 요청 단위(시간) - 무료 | 5d638a6f-e221-41cf-ae3f-0f81d368cef6 
| File Storage | 표준 IO - 파일(GB) - 로컬 중복 | 표준 IO - 파일(GB) - 무료 | a7f2aa67-b9a2-4593-a413-6ec86d6c8e5b
| File Storage | 표준 IO - 파일 읽기 작업 단위(10,000초 기준) | 표준 IO - 파일 읽기 작업 단위(10,000초 기준) - 무료 | 6207404d-3389-4d20-9087-cc078ddc3fd9
| File Storage | 표준 IO - 파일 쓰기 작업 단위(10,000초 기준) | 표준 IO - 파일 쓰기 작업 단위(10,000초 기준) - 무료 | 223d8004-d29a-46cf-b4f4-d2d34b12548b
| File Storage | 표준 IO - 파일 프로토콜 작업 단위(10,000초 기준) | 표준 IO - 파일 프로토콜 작업 단위(10,000초 기준) - 무료 | a347d8cc-51d1-4a0e-b9eb-76f67566c3f5
| File Storage | 표준 IO - 파일 목록 작업 단위(10,000초 기준) | 표준 IO - 파일 목록 작업 단위(10,000초 기준) - 무료 | e8ae79ad-c2ab-4d82-b226-dd3c33dfd40c
| 핫 블록 Blob Storage | 표준 IO - 핫 블록 Blob 읽기 작업(10,000초 기준) | 표준 IO - 핫 블록 Blob 읽기 작업(10,000초 기준) - 무료 |fd7cfa1e-026e-4be1-871b-1c2386e8902e
| 핫 블록 Blob Storage | 표준 IO - 핫 블록 Blob(GB) - 로컬 중복 | 표준 IO - 핫 블록 Blob(GB) - 무료 | 67a3a3fd-826f-42c1-8843-bffa14f0da13
| 핫 블록 Blob Storage | 표준 IO - 핫 블록 Blob 쓰기 작업(10,000초 기준) | 표준 IO - 핫 블록 Blob 쓰기 작업(10,000초 기준) - 무료 | b34bbb76-edce-4c2d-a288-81a2db1fea53
| 핫 블록 Blob Storage  | 표준 IO - 핫 블록 Blob 쓰기/목록 작업(10,000초 기준) | 표준 IO - 핫 블록 Blob 쓰기/목록 작업(10,000초 기준) - 무료 | 7e68cf36-1198-4d3b-baa7-86a74c5b3079
| Managed Disk *  | 표준 관리 디스크/스냅숏(GB) - 로컬 중복 | 표준 관리 디스크/스냅숏(GB) - 무료 | ad94c237-52a5-4804-ae65-38c5bf85ef42
| Managed Disk *  | 표준 관리 디스크 작업(10,000초 기준) | 표준 관리 디스크 작업(10,000초 기준) - 무료 | 82cc6ea4-0abd-43ac-acc0-ec34edf0f14c
| Managed Disk *  | Premium Storag - 페이지 Blob/P6(단위) - 로컬 중복 | Premium Storag - 페이지 Blob/P6(단위) - 무료 | 2b98c168-27ca-4cc1-b509-e887dec87657
| SQL Database | 표준 S0 데이터베이스 일 수 - SQL Database | 표준 S0 데이터베이스 일 수 - 무료 | dd6b69d3-9be0-4a91-abff-2c58bbcafd1d
| 공유 - 대역폭 ** | 데이터 송신(GB) | 데이터 송신(GB) - 무료 | 0fc067a1-65d2-46da-b24b-7a9cbe2c69bd

\* Windows 가상 머신을 만들고 관리 디스크를 선택하면 가상 머신의 일부로 관리 디스크 미터를 사용합니다.

\** 공유 미터는 여러 서비스에서 사용할 수 있습니다. 예를 들어 가상 머신과 저장소는 모두 데이터 송신(GB) 미터에 대해 사용량을 내보냅니다.





## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.
