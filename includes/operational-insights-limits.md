---
title: 포함 파일
description: 포함 파일
services: log-analytics
author: MGoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 05/16/2018
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: 66cd09df128d454973d008adf4ffc5dd1017a18f
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
---
구독당 Log Analytics 리소스에는 다음과 같은 제한이 적용됩니다.

| 리소스 | 기본 제한 | 설명
| --- | --- | --- |
| 구독당 무료 작업 영역의 수 | 10 | 이 제한은 늘릴 수 없습니다. |
| 구독당 유료 작업 영역의 수 | 해당 없음 | 리소스 그룹 내 리소스의 수와 구독당 리소스 그룹의 수에 의해 제한됩니다. | 

>[!NOTE]
>2018년 4월 2일 기준으로 새 구독의 새 작업 영역은 자동으로 *GB당* 가격 책정 요금제를 사용합니다.  4월 2일 이전의 기존 구독 또는 기존 EA 등록과 연결된 구독의 경우 새 작업 영역에 대해 세 가지 가격 책정 계층 중에서 선택할 수 있습니다. 
>

각 Log Analytics 작업 영역에는 다음 제한이 적용됩니다.

|  | 무료 | Standard | Premium | 독립 실행형 | OMS | GB당 |
| --- | --- | --- | --- | --- | --- |--- |
| 하루에 수집된 데이터 볼륨 |500MB<sup>1</sup> |없음 |없음 | 없음 | 없음 | 없음
| 데이터 보존 기간 |7 일 |1개월 |12개월 | 1개월<sup>2</sup> | 1개월 <sup>2</sup>| 1개월 <sup>2</sup>|

<sup>1</sup> 고객이 500MB 일별 데이터 전송 제한에 도달한 경우 데이터 분석이 중지되고 다음날 다시 시작됩니다. 1일은 UTC를 기준으로 합니다.

<sup>2</sup> 독립 실행형, OMS 및 GB당 가격 책정 요금제에 대한 데이터 보존 기간을 730일로 늘릴 수 있습니다.

| Category | 제한 | 설명
| --- | --- | --- |
| 데이터 수집기 API | 단일 post에 대한 최대 크기는 30MB<br>필드 값에 대한 최대 크기는 32KB | 여러 개의 게시물에 더 큰 볼륨을 분할합니다.<br>32KB보다 긴 필드는 잘립니다. |
| 검색 API | 집계되지 않은 데이터에 대한 반환된 5,000개 레코드<br>집계된 데이터에 대한 500,000개 레코드 | 집계된 데이터는 `summarize` 명령을 포함하는 검색입니다.
 
