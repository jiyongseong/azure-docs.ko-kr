---
title: Azure Stream Analytics 작업의 호환성 수준 이해
description: Azure Stream Analytics 작업의 호환성 수준을 설정하는 방법과 최신 호환성 수준의 주요 변경 내용을 알아봅니다.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/03/2018
ms.openlocfilehash: 32e73918b2dd98822d42d74002b705ff730145d9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Azure Stream Analytics 작업의 호환성 수준
 
호환성 수준이란 Azure Stream Analytics 서비스의 릴리스 관련 동작을 말합니다. Azure Stream Analytics는 관리되는 서비스로, 정기적으로 기능이 업데이트되고 성능이 향상됩니다. 일반적으로 업데이트는 최종 사용자에게 자동으로 제공됩니다. 그러나 일부 새 기능은 기존 작업의 동작 변경, 이러한 작업의 데이터를 사용하는 프로세스 변경 등과 같은 주요 변화를 가져올 수 있습니다. 호환성 수준은 Stream Analytics에 적용된 주요 변경을 나타내는 데 사용됩니다. 주요 변경 내용은 항상 새로운 호환성 수준과 함께 적용됩니다. 

호환성 수준은 기존 작업이 오류 없이 실행되도록 보장합니다. 새로운 Stream Analytics 작업을 만들 때 사용 가능한 가장 최신 호환성 수준을 사용하여 만드는 것이 좋습니다. 
 
## <a name="set-a-compatibility-level"></a>호환성 수준 설정 

호환성 수준은 스트림 분석 작업의 런타임 동작을 제어합니다. 포털을 사용하거나 [create job REST API call](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job)을 사용하여 Stream Analytics 작업의 호환성 수준을 설정할 수 있습니다 Azure Stream Analytics는 현재 “1.0” 및 “1.1”의 두 가지 호환성 수준을 지원합니다. 기본적으로 호환성 수준은 Azure Stream Analytics 일반 공급 때 도입된 “1.0”으로 설정됩니다. 기본값을 업데이트하려면 **구성** 섹션에서 기존 Stream Analytics 작업으로 이동하여 **호환성 수준** 옵션을 선택한 후 값을 변경합니다. 

호환성 수준을 업데이트하기 전에 작업을 중지해야 합니다. 작업이 실행 중인 상태에서는 호환성 수준을 업데이트할 수 없습니다. 

![포털의 호환성 수준](media\stream-analytics-compatibility-level/image1.png)

 
호환성 수준을 업데이트하면 T-SQL 컴파일러는 선택된 호환성 수준에 해당하는 구문을 사용하여 작업의 유효성을 검사합니다. 

## <a name="major-changes-in-the-latest-compatibility-level-11"></a>최신 호환성 수준(1.1)의 주요 변경 내용

호환성 수준 1.1의 주요 변경 내용은 다음과 같습니다.

* **Service Bus XML 형식**  

  * **이전 버전:** Azure Stream Analytics에서 DataContractSerializer를 사용했기 때문에 메시지 내용에 XML 태그가 포함되었습니다. 예: 
    
   @\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ “SensorId”:”1”, “Temperature”:64\}\u0001 

  * **현재 버전:** 추가 태그 없이 메시지 내용에 스트림이 직접 포함됩니다. 예: 
  
   { “SensorId”:”1”, “Temperature”:64} 
 
* **필드 이름의 대/소문자 구분 유지**  

  * **이전 버전:** Azure Stream Analytics 엔진에서 필드 이름이 처리될 때 소문자로 변경되었습니다. 

  * **현재 버전:** Azure Stream Analytics 엔진에서 필드 이름이 처리될 때 대/소문자 구분이 유지됩니다. 

  > [!NOTE] 
  > 지속적인 대/소문자 구분은 에지 환경을 사용하여 호스팅되는 Stream Analytic 작업에 아직 사용할 수 없습니다. 결과적으로, 작업이 에지에서 호스팅되는 경우 모든 필드 이름은 소문자로 변환됩니다. 

* **FloatNaNDeserializationDisabled**  

  * **이전 버전:** CREATE TABLE 명령은 FLOAT 열 형식이 NaN(숫자가 아님. 예: 무한대, -무한대)인 이벤트를 필터링하지 않습니다. 왜냐하면 이러한 이벤트는 이러한 숫자에 대해 문서화된 범위를 벗어나기 때문입니다.

  * **현재 버전:** CREATE TABLE 명령을 사용하여 강력한 스키마를 지정할 수 있습니다. Stream Analytics 엔진은 데이터가 이 스키마를 준수하는지 확인합니다. 이 모델을 사용하면 이 명령으로 NaN 값을 사용하는 이벤트를 필터링할 수 있습니다. 

* **JSON에서 날짜/시간 문자열에 자동 업캐스트를 사용하지 않도록 설정합니다.**  

  * **이전 버전:** JSON 파서가 자동으로 표준 시간대 정보를 사용하여 문자열 값을 날짜/시간 형식으로 업캐스트한 후 UTC로 변환합니다. 이로 인해 표준 시간대 정보가 손실되었습니다.

  * **현재 버전:** 더 이상 자동으로 표준 시간대 정보를 사용하여 문자열 값을 날짜/시간 형식으로 업캐스트하지 않습니다. 따라서 표준 시간대 정보가 유지됩니다. 

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 문제 해결 가이드](stream-analytics-troubleshooting-guide.md)
* [Stream Analytics Resource 상태 블레이드](stream-analytics-resource-health.md)