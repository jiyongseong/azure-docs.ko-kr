---
title: Azure IoT Hub 장치-클라우드 옵션 | Microsoft Docs
description: 개발자 가이드 - 장치-클라우드 메시지, reported 속성 또는 클라우드-장치 통신을 위한 파일 업로드를 사용하는 경우에 대한 지침입니다.
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: ''
ms.assetid: 979136db-c92d-4288-870c-f305e8777bdd
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: elioda
ms.openlocfilehash: a870852d937732bb38f08420a07bfe696860e08b
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="device-to-cloud-communications-guidance"></a>장치-클라우드 통신 지침
장치 앱에서 솔루션 백 엔드로 정보를 전송할 때 IoT Hub은 다음 세 가지 옵션을 공개합니다.

* [장치-클라우드 메시지][lnk-d2c] 시계열 원격 분석 및 경고의 경우
* [장치 쌍의 보고된 속성][lnk-twins] 장기 실행 워크플로의 사용 가능한 기능, 조건 또는 상태와 같은 장치 상태 정보를 보고하는 경우 예를 들어 구성 및 소프트웨어 업데이트입니다.
* [파일 업로드][lnk-fileupload] 간헐적으로 연결된 장치로 업로드되거나 대역폭을 절약하기 위해 압축된 미디어 파일 및 대형 원격 분석 배치의 경우

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

다음은 다양한 장치-클라우드 통신 옵션에 대해 자세히 비교하고 있습니다.

|  | 장치-클라우드 메시지 | 장치 쌍의 보고된 속성 | 파일 업로드 |
| ---- | ------- | ---------- | ---- |
| 시나리오 | 원격 분석 시계열 및 알림 예를 들어 256KB의 센서 데이터 배치는 5분마다 전송합니다. | 사용할 수 있는 기능 및 조건 예를 들어 셀룰러 또는 WiFi와 같은 현재 장치 연결 모드입니다. 구성 및 소프트웨어 업데이트와 같이 장기 실행 워크플로 동기화 | 미디어 파일. 대형(일반적으로 압축됨) 원격 분석 일괄 처리 |
| 저장 및 검색 | IoT Hub에서 일시적으로 최대 7일 동안 저장합니다. 순차 읽기만 | IoT Hub에서 장치 쌍에 저장합니다. [IoT Hub 쿼리 언어][lnk-query]를 사용하여 검색할 수 있습니다. | 사용자 제공 Azure Storage 계정에 저장됩니다. |
| 크기 | 최대 256KB 메시지입니다. | 최대 reported 속성 크기는 8KB입니다. | Azure Blob Storage에서 지원하는 최대 파일 크기 |
| Frequency(빈도) | 높음. 자세한 내용은 [IoT Hub 제한][lnk-quotas]을 참조하세요. | 중간. 자세한 내용은 [IoT Hub 제한][lnk-quotas]을 참조하세요. | 낮음. 자세한 내용은 [IoT Hub 제한][lnk-quotas]을 참조하세요. |
| 프로토콜 | 모든 프로토콜에서 사용할 수 있습니다. | MQTT 또는 AMQP를 통해 사용 가능합니다. | 프로토콜을 사용할 때 사용할 수 있지만 장치에 HTTPS가 필요합니다. |

응용 프로그램이 원격 분석 시계열 또는 경고로 정보를 보내고 장치 쌍에도 제공해야 할 수 있습니다. 이 시나리오에서는 다음 옵션 중 하나를 선택할 수 있습니다.

* 장치 앱이 장치-클라우드 메시지를 보내고 속성 변경 사항을 보고합니다.
* 메시지를 받을 때 솔루션 백 엔드가 장치 쌍의 태그에 정보를 저장할 수 있습니다.

장치-클라우드 메시지를 사용하면 장치 쌍 업데이트보다 훨씬 높은 처리량이 가능하므로 때로는 모든 장치-클라우드 메시지에 대해 장치 쌍을 업데이트하지 않는 것이 바람직합니다.


[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-fileupload]: iot-hub-devguide-file-upload.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
