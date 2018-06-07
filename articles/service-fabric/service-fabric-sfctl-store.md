---
title: Azure Service Fabric CLI- sfctl store| Microsoft Docs
description: "Service Fabric CLI sfctl store 명령을 설명합니다."
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/22/2017
ms.author: ryanwi
ms.openlocfilehash: d4ca3c35c34736c3b4824f956a6a72002c891877
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2018
---
# <a name="sfctl-store"></a>sfctl store
클러스터 이미지 저장소에서 기본 파일 수준 작업을 수행합니다.

## <a name="commands"></a>명령

|명령|설명|
| --- | --- |
|    delete| 기존 이미지 저장소 콘텐츠를 삭제합니다.|
|    root-info| 이미지 저장소의 루트에서 콘텐츠 정보를 가져옵니다.|
|    stat  | 이미지 저장소 콘텐츠 정보를 가져옵니다.|


## <a name="sfctl-store-delete"></a>sfctl store delete
기존 이미지 저장소 콘텐츠를 삭제합니다.

지정된 이미지 저장소 상대 경로 내에서 발견되는 기존 이미지 저장소 콘텐츠를 삭제합니다. 이 명령은 업로드된 응용 프로그램 패키지가 프로비저닝된 후에 이를 삭제하는 데 사용할 수 있습니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --content-path [필수]| 루트에서 이미지 저장소의 파일 또는 폴더에 대한 상대 경로입니다.|
| --timeout -t          | 서버 시간 제한(초).  기본값: 60.|

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug               | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
| --help -h             | 이 도움말 메시지 및 종료를 표시합니다.|
| --output -o           | 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv.  기본값:           json.|
| --query               | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http://jmespath.org/를 참조하세요.|
| --verbose             | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다.|

## <a name="sfctl-store-stat"></a>sfctl store stat
이미지 저장소 콘텐츠 정보를 가져옵니다.

이미지 저장소의 루트를 기준으로 지정된 contentPath에 있는 이미지 저장소 콘텐츠에 대한 정보를 반환합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --content-path [필수]| 루트에서 이미지 저장소의 파일 또는 폴더에 대한 상대 경로입니다.|
| --timeout -t          | 서버 시간 제한(초).  기본값: 60.|

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug               | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
| --help -h             | 이 도움말 메시지 및 종료를 표시합니다.|
| --output -o           | 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv.  기본값:           json.|
| --query               | JMESPath 쿼리 문자열. 자세한           내용 및 예제는 http://jmespath.org/를 참조하십시오.|
| --verbose             | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|

## <a name="next-steps"></a>다음 단계
- Service Fabric CLI [설정](service-fabric-cli.md)
- [샘플 스크립트](/azure/service-fabric/scripts/sfctl-upgrade-application)를 사용하여 Microsoft Azure Service Fabric CLI를 사용하는 방법에 대해 알아봅니다.