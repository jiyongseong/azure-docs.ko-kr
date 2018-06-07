---
title: Azure 스택 저장소 차이점과 고려 사항을 | Microsoft Docs
description: Azure 스택 저장소와 함께 Azure 스택 배포 고려 사항 Azure 저장소의 차이점을 이해 합니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/21/2018
ms.author: jeffgilb
ms.reviwer: xiaofmao
ms.openlocfilehash: 2a6cb3f1a1f8009af411ba4d97a23194f6f089ae
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604463"
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Azure 스택 저장소: 차이점과 고려 사항

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure 스택 저장소는 Microsoft Azure 스택에서 저장소 클라우드 서비스의 집합입니다. Azure 스택 저장소는 blob, 테이블, 큐 및 Azure에 일관 된 의미 체계를 포함 하는 계정 관리 기능을 제공 합니다.

이 문서는 Azure 저장소 서비스에서 알려진된 Azure 스택 저장소 차이점을 요약 합니다. 또한 Azure 스택을 배포할 때 고려해 야 할 사항 나열 합니다. 전역 Azure 및 Azure 스택 간에 높은 수준의 차이점에 대 한 자세한 내용은 [고려 사항 키](azure-stack-considerations.md) 항목입니다.

## <a name="cheat-sheet-storage-differences"></a>치트 시트: 저장소의 차이

| 기능 | Azure (global) | Azure Stack |
| --- | --- | --- |
|File Storage|지원 클라우드 기반 SMB 파일 공유|아직 지원 되지 않음
|미사용 데이터에 대 한 azure 저장소 서비스 암호화|256 비트 AES 암호화|BitLocker 128 비트 AES 암호화
|Storage 계정 유형|범용 및 Azure blob 저장소 계정|범용만 합니다.
|복제 옵션|로컬 중복 저장소, 지역 중복 저장소, 읽기 액세스 지역 중복 저장소 및 영역 중복 저장소|로컬 중복 저장소입니다.
|Premium Storage|완전하게 지원|성능 제한이 있지만 제공 될 수 있습니다 또는 보장 합니다.
|관리 디스크|지 원하는 표준 및 프리미엄|아직 지원 되지 않습니다.
|Blob 이름|1, 024 자 (2, 048 바이트)|880 문자 (1,760 바이트)
|블록 blob의 최대 크기|4.75 TB (100 MB X 50, 000 블록)|1802 업데이트 또는 최신 버전에 대 한 4.75 TB (100 MB x 50, 000 블록). 이전 버전에 대 한 50, 000 X 4 MB (약 195 GB)
|페이지 blob 스냅숏 복사본|지원 되는 실행 중인 VM에 연결 된 백업 Azure 관리 되지 않는 VM 디스크|아직 지원 되지 않습니다.
|페이지 blob 증분 스냅숏 복사본|Premium 및 지원 되는 표준 Azure 페이지 blob|아직 지원 되지 않습니다.
|Blob 저장소에 대 한 저장소 계층|핫, 멋지지 하 고 저장소 계층을 보관 합니다.|아직 지원 되지 않습니다.
Blob 저장소에 대 한 일시 삭제|미리 보기|아직 지원 되지 않습니다.
|페이지 blob의 최대 크기|8 TB|1TB
|페이지 blob 페이지 크기|512바이트|4KB
|테이블 파티션 키와 행 키 크기|1, 024 자 (2, 048 바이트)|400 문자 (800 바이트)
|Blob 스냅숏|최대 하나의 blob의 스냅숏 수 제한 하지 않습니다.|한 blob의 스냅숏의 최대 수는 1, 000입니다.|

저장소 메트릭 사용 하 여 차이점 있습니다.

* 저장소 메트릭의 트랜잭션 데이터를 내부 또는 외부 네트워크 대역폭 달라 지지 않습니다.
* 저장소 메트릭에서 트랜잭션 데이터는 탑재 된 디스크에 대 한 가상 컴퓨터 액세스를 포함 되지 않습니다.

## <a name="api-version"></a>API 버전

Azure 스택 저장소와 다음과 같은 버전이 지원 됩니다.

Azure 저장소 서비스 Api:

1802 업데이트 이상 버전:

 - [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
 - [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
 - [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
 - [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
 - [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

이전 버전:

 - [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Azure 저장소 서비스 관리 Api:

 - [2015-05-01-미리 보기](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

## <a name="sdk-versions"></a>SDK 버전

Azure 스택 저장소 클라이언트 라이브러리를 지원합니다.

| 클라이언트 라이브러리 | Azure 스택 지원 되는 버전 | 링크                                                                                                                                                                                                                                                                                                                                     | 끝점 지정       |
|----------------|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET           | 8.7.0에 6.2.0 합니다.          | Nuget 패키지:<br>https://www.nuget.org/packages/WindowsAzure.Storage/<br> <br>GitHub 릴리스:<br>https://github.com/Azure/azure-storage-net/releases                                                                                                                                                                                    | app.config 파일              |
| 자바           | 6.1.0 4.1.0에서           | Maven 패키지:<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage<br> <br>GitHub 릴리스:<br>https://github.com/Azure/azure-storage-java/releases                                                                                                                                                                    | 연결 문자열 설정      |
| Node.js        | 1.1.0 2.7.0에서           | NPM 링크:<br>https://www.npmjs.com/package/azure-storage<br>(예: 실행 "npm 설치 azure-storage@2.7.0")<br> <br>Github 릴리스:<br>https://github.com/Azure/azure-storage-node/releases                                                                                                                                         | 서비스 인스턴스 선언 |
| C++            | 3.1.0 2.4.0에서           | Nuget 패키지:<br>https://www.nuget.org/packages/wastorage.v140/<br> <br>GitHub 릴리스:<br>https://github.com/Azure/azure-storage-cpp/releases                                                                                                                                                                                          | 연결 문자열 설정      |
| PHP            | 1.0.0 0.15.0에서          | GitHub 릴리스:<br>https://github.com/Azure/azure-storage-php/releases<br> <br>작성기를 통해 설치 (아래 세부 정보 참조)                                                                                                                                                                                                                  | 연결 문자열 설정      |
| 파이썬         | 1.0.0 0.30.0에서          | GitHub 릴리스:<br>https://github.com/Azure/azure-storage-python/releases                                                                                                                                                                                                                                                                | 서비스 인스턴스 선언 |
| Ruby           | 1.0.1 0.12.1에서          | RubyGems 패키지:<br>일반적인:<br>https://rubygems.org/gems/azure-storage-common/<br>Blob: https://rubygems.org/gems/azure-storage-blob/<br>큐: https://rubygems.org/gems/azure-storage-queue/<br>Table: https://rubygems.org/gems/azure-storage-table/<br> <br>GitHub 릴리스:<br>https://github.com/Azure/azure-storage-ruby/releases | 연결 문자열 설정      |

## <a name="next-steps"></a>다음 단계

* [Azure 스택 저장소 개발 도구 시작](azure-stack-storage-dev.md)
* [Azure 스택 저장소 소개](azure-stack-storage-overview.md)
