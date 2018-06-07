---
title: 키 자격 증명 모음 버전
description: 다양한 버전의 Azure Key Vault
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e8622dcc-59a3-4f4b-9f63-cd2232515a65
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: beb73be66f36ccf95fe27d4d8128106cd12722a8
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="key-vault-versions"></a>키 자격 증명 모음 버전

## <a name="2016-10-01---managed-storage-account-keys"></a>2016-10-01 - 관리 저장소 계정 키

2017년 여름 - Azure Storage와 더 쉽게 통합하는 저장소 계정 키 기능을 추가했습니다. 자세한 내용은 개요 토픽 [관리 저장소 계정 키 개요](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys)를 참조하세요.

## <a name="2016-10-01---soft-delete"></a>2016-10-01 - 일시 삭제

2017년 여름 - 키 자격 증명 모음 및 키 자격 증명 모음 개체의 데이터 보호가 개선된 일시 삭제 기능을 추가했습니다. 자세한 내용은 [일시 삭제 개요](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) 토픽을 참조하세요.

## <a name="2015-06-01---certificate-management"></a>2015-06-01 - 인증서 관리

2016년 9월 26일, GA 버전 2015-06-01에 대한 기능으로 인증서 관리를 추가했습니다.

## <a name="2015-06-01---general-availability"></a>2015-06-01 - 일반 공급

일반 공급 버전 2015-06-01, 2015년 6월 24일 발표.

이 릴리스에서 다음과 같은 사항이 변경되었습니다.

- 키 삭제 - "사용" 필드를 제거했습니다.
- 키에 대한 정보 가져오기 - "사용" 필드를 제거했습니다.
- 키 자격 증명 모음에 키 가져오기 - "사용" 필드를 제거했습니다.
- 키 복원 - "사용" 필드를 제거했습니다.
- RSA 알고리즘에 대한 "RSA_OAEP"를 "RSA-OAEP"로 변경했습니다. [키, 비밀 및 인증서에 대한 정보](about-keys-secrets-and-certificates.md)를 참조하세요.

## <a name="2015-02-01-preview"></a>2015-02-01-preview 

두 번째 미리보기 버전 2015-02-01-preview, 2015년 4월 20일 발표. 자세한 내용은 [REST API 업데이트](http://blogs.technet.com/b/kv/archive/2015/04/20/empty-3.aspx)를 참조하세요.

다음 작업을 업데이트했습니다.

- 자격 증명 모음에 키 나열 - 작업에 페이지 매김 지원을 추가했습니다.
- 키 버전 나열 - 키 버전을 나열하는 작업을 추가했습니다.
- 자격 증명 모음에 비밀 나열 - 페이지 매김 지원을 추가했습니다.
- 비밀 버전 나열 - 비밀 버전을 나열하는 작업을 추가합니다.
- 모든 작업 - 특성에 만든/업데이트한 타임스탬프를 추가했습니다.
- 비밀 만들기 - 비밀에 콘텐츠 형식을 추가했습니다.
- 키 만들기 - 선택 사항 정보로 태그를 추가했습니다.
- 비밀 만들기 - 선택 사항 정보로 태그를 추가했습니다.
- 키 업데이트 - 선택 사항 정보로 태그를 추가했습니다.
- 비밀 업데이트 - 선택 사항 정보로 태그를 추가했습니다.
- 비밀의 최대 크기를 10K에서 25K 바이트로 변경했습니다. [키, 비밀 및 인증서에 대한 정보](about-keys-secrets-and-certificates.md)를 참조하세요.

## <a name="2014-12-08-preview"></a>2014-12-08-preview

첫 번째 미리보기 버전 2014-12-08-preview, 2015년 1월 8일 발표.

## <a name="see-also"></a>참고 항목
- [키, 비밀 및 인증서에 대한 정보](about-keys-secrets-and-certificates.md)
