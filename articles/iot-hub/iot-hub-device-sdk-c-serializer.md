---
title: C용 Azure IoT 장치 SDK - 직렬 변환기 | Microsoft Docs
description: C용 Azure IoT 장치 SDK에서 Serializer 라이브러리를 사용하여 IoT Hub와 통신하는 장치 앱을 만드는 방법입니다.
services: iot-hub
documentationcenter: ''
author: yzhong94
manager: timlt
editor: ''
ms.assetid: defbed34-de73-429c-8592-cd863a38e4dd
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2016
ms.author: yizhon
ms.openlocfilehash: 04b5ebf6d8c568fbfd9b291bef95af973ec5d6d7
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="azure-iot-device-sdk-for-c--more-about-serializer"></a>C용 Azure IoT 장치 SDK - 직렬 변환기에 대한 자세한 정보
이 시리즈의 [첫 번째 문서](iot-hub-device-sdk-c-intro.md)에서는 **C용 Azure IoT 장치 SDK**에 대해 소개했습니다. 다음 문서에서 [**IoTHubClient**](iot-hub-device-sdk-c-iothubclient.md)에 대해 보다 자세히 설명했습니다. 이 문서에서는 나머지 구성 요소인 **serializer** 라이브러리에 대한 보다 자세한 설명을 제공하여 SDK의 범위를 보완합니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

소개 자료에서 **serializer** 라이브러리를 사용하여 IoT Hub로 이벤트를 보내고 메시지를 받는 방법을 설명했습니다. 이 문서에서는 **serializer** 매크로 언어로 데이터를 모델링하는 방법에 대한 자세한 설명을 제공하여 논의를 확장합니다. 또한 라이브러리에서 메시지를 직렬화하고 직렬화 동작을 제어하는 방법도 자세히 설명합니다. 사용자가 만드는 모델의 크기를 결정하는 일부 매개 변수를 수정하는 방법에 대해서도 설명합니다.

마지막으로 메시지 및 속성 처리와 같은 이전 문서에서 다루는 일부 항목을 다시 상기시키는 것으로 마무리합니다. 알다시피, **IoTHubClient** 라이브러리로 작업할 때와 마찬가지로 **serializer** 라이브러리를 사용할 때 해당 기능이 동일하게 작동합니다.

이 문서에 설명된 모든 내용은 **serializer** SDK 샘플을 기반으로 합니다. 따라하려면 C용 Azure IoT 장치 SDK에 포함된 **simplesample\_amqp** 및 **simplesample\_http** 응용 프로그램을 참조하세요.

GitHub 리포지토리에서 [**C용 Azure IoT 장치 SDK**](https://github.com/Azure/azure-iot-sdk-c)를 찾고 [C API 참조](https://azure.github.io/azure-iot-sdk-c/index.html)에서 API의 세부 정보를 볼 수 있습니다.

## <a name="the-modeling-language"></a>모델링 언어
이 시리즈의 [소개 자료](iot-hub-device-sdk-c-intro.md)에서는 **simplesample\_amqp** 응용 프로그램에 제공된 예제를 통해 **C용 Azure IoT 장치 SDK** 모델링 언어를 소개했습니다.

```
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(double, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

여기에서 볼 수 있듯이 모델링 언어는 C 매크로를 기반으로 합니다. 항상 **BEGIN\_NAMESPACE**로 사용자 정의를 시작하고 **END\_NAMESPACE**로 끝냅니다. 회사에 해당하는 네임스페이스로 이름을 지정하는 것이 일반적입니다(이 예제에서는 작업 중인 프로젝트에 해당하는 이름을 지정).

네임스페이스 내부에는 모델 정의가 나옵니다. 이 경우 풍력계에 대한 단일 모델이 있습니다. 다시 한 번 언급하지만 모델에 아무 이름이나 지정할 수 있지만 일반적으로 장치 또는 IoT Hub와 교환하려는 데이터의 형식에 해당하는 이름으로 지정합니다.  

모델에는 IoT Hub로 들어오는 이벤트(*데이터*)와 IoT Hub로부터 수신할 수 있는 메시지(*동작*)에 대한 정의가 포합됩니다. 예제에서 볼 수 있듯이, 이벤트에는 형식 및 이름이, 동작에는 이름 및 선택적 매개 변수(각각 형식 포함)가 포함됩니다.

이 샘플에서는 SDK에서 지원되는 추가 데이터 형식은 다루지 않습니다. 다음에 설명합니다.

> [!NOTE]
> IoT Hub는 장치에서 보내는 데이터를 *이벤트*로 참조하는 반면 모델링 언어는 *데이터*(**WITH_DATA**를 사용하여 정의됨)로 참조합니다. 마찬가지로 IoT Hub는 사용자가 장치로 보내는 데이터를 *메시지*로 참조하는 반면 모델링 언어는 *동작*(**WITH_ACTION**을 사용하여 정의됨)으로 참조합니다. 이 문서에서는 이러한 용어가 같은 의미로 사용될 수 있습니다.
> 
> 

### <a name="supported-data-types"></a>지원되는 데이터 원본
**serializer** 라이브러리로 만든 모델에 다음 데이터 형식이 지원됩니다.

| 유형 | 설명 |
| --- | --- |
| double |배정밀도 부동 소수점 숫자 |
| int |32비트 정수 |
| float |단정밀도 부동 소수점 숫자 |
| long |정수(Long) |
| int8\_t |8비트 정수 |
| int16\_t |16비트 정수 |
| int32\_t |32비트 정수 |
| int64\_t |64비트 정수 |
| bool |부울 |
| ascii\_char\_ptr |ASCII 문자열 |
| EDM\_DATE\_TIME\_OFFSET |날짜 시간 오프셋 |
| EDM\_GUID |GUID |
| EDM\_BINARY |binary |
| DECLARE\_STRUCT |복합 데이터 형식 |

마지막 데이터 형식부터 살펴보겠습니다. **DECLARE\_STRUCT**로 기타 기본 형식의 그룹인 복합 데이터 형식을 정의할 수 있습니다. 이러한 그룹을 통해 다음과 같은 모델을 정의할 수 있습니다.

```
DECLARE_STRUCT(TestType,
double, aDouble,
int, aInt,
float, aFloat,
long, aLong,
int8_t, aInt8,
uint8_t, auInt8,
int16_t, aInt16,
int32_t, aInt32,
int64_t, aInt64,
bool, aBool,
ascii_char_ptr, aAsciiCharPtr,
EDM_DATE_TIME_OFFSET, aDateTimeOffset,
EDM_GUID, aGuid,
EDM_BINARY, aBinary
);

DECLARE_MODEL(TestModel,
WITH_DATA(TestType, Test)
);
```

이 모델은 **TestType**형식의 단일 데이터 이벤트를 포함합니다. **TestType**은 여러 멤버를 포함하는 복합 형식이며 **serializer** 모델링 언어에서 지원되는 기본 형식을 총칭합니다.

이와 같은 모델로 다음과 같이 데이터를 IoT Hub로 보내는 코드를 작성할 수 있습니다.

```
TestModel* testModel = CREATE_MODEL_INSTANCE(MyThermostat, TestModel);

testModel->Test.aDouble = 1.1;
testModel->Test.aInt = 2;
testModel->Test.aFloat = 3.0f;
testModel->Test.aLong = 4;
testModel->Test.aInt8 = 5;
testModel->Test.auInt8 = 6;
testModel->Test.aInt16 = 7;
testModel->Test.aInt32 = 8;
testModel->Test.aInt64 = 9;
testModel->Test.aBool = true;
testModel->Test.aAsciiCharPtr = "ascii string 1";

time_t now;
time(&now);
testModel->Test.aDateTimeOffset = GetDateTimeOffset(now);

EDM_GUID guid = { { 0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09, 0x0A, 0x0B, 0x0C, 0x0D, 0x0E, 0x0F } };
testModel->Test.aGuid = guid;

unsigned char binaryArray[3] = { 0x01, 0x02, 0x03 };
EDM_BINARY binaryData = { sizeof(binaryArray), &binaryArray };
testModel->Test.aBinary = binaryData;

SendAsync(iotHubClientHandle, (const void*)&(testModel->Test));
```

기본적으로 **Test** 구조체의 모든 멤버에 값을 할당한 후 **SendAsync**를 호출하여 **Test** 데이터 이벤트를 클라우드로 보냅니다. **SendAsync** 는 단일 데이터 이벤트를 IoT Hub로 보내는 도우미 함수입니다.

```
void SendAsync(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const void *dataEvent)
{
    unsigned char* destination;
    size_t destinationSize;
    if (SERIALIZE(&destination, &destinationSize, *(const unsigned char*)dataEvent) ==
    {
        // null terminate the string
        char* destinationAsString = (char*)malloc(destinationSize + 1);
        if (destinationAsString != NULL)
        {
            memcpy(destinationAsString, destination, destinationSize);
            destinationAsString[destinationSize] = '\0';
            IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromString(destinationAsString);
            if (messageHandle != NULL)
            {
                IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)0);

                IoTHubMessage_Destroy(messageHandle);
            }
            free(destinationAsString);
        }
        free(destination);
    }
}
```

이 함수는 지정된 데이터 이벤트를 직렬화하고 **IoTHubClient\_SendEventAsync**를 사용하여 IoT Hub에 보냅니다. 이전 문서에서 설명했던 것과 동일한 코드입니다(**SendAsync** 는 논리를 편리한 함수로 캡슐화함).

이전 코드에 사용된 다른 도우미 함수는 **GetDateTimeOffset**입니다. 이 함수는 지정된 시간을 **EDM\_DATE\_TIME\_OFFSET** 형식 값으로 변환합니다.

```
EDM_DATE_TIME_OFFSET GetDateTimeOffset(time_t time)
{
    struct tm newTime;
    gmtime_s(&newTime, &time);
    EDM_DATE_TIME_OFFSET dateTimeOffset;
    dateTimeOffset.dateTime = newTime;
    dateTimeOffset.fractionalSecond = 0;
    dateTimeOffset.hasFractionalSecond = 0;
    dateTimeOffset.hasTimeZone = 0;
    dateTimeOffset.timeZoneHour = 0;
    dateTimeOffset.timeZoneMinute = 0;
    return dateTimeOffset;
}
```

이 코드를 실행하는 경우 다음과 같은 메시지가 IoT Hub로 전송됩니다.

```
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

직렬화는 **serializer** 라이브러리로 생성되는 형식의 JSON입니다. 또한 직렬화된 JSON 개체의 각 멤버가 모델에 정의한 **TestType** 의 멤버와 일치하는 것을 확인할 수 있습니다. 이 값은 코드에 사용된 것과도 정확히 일치합니다. 그러나 이진 데이터는 base64 인코드되며 "AQID"는 {0x01, 0x02, 0x03}의 base64 인코딩입니다.

이 예제에서는 **serializer** 라이브러리 사용 시의 이점을 보여 줍니다. 응용 프로그램에서 직렬화를 명시적으로 처리하지 않고도 JSON을 클라우드로 전송할 수 있습니다. 모델에서 데이터 이벤트 값을 설정한 후 간단한 API를 호출하여 해당 이벤트를 클라우드로 전송할 수 있으므로 세세한 사항을 걱정할 필요가 없습니다.

이 정보로 복합 형식을 비롯하여 지원되는 데이터 형식의 범위를 포함하는 모델을 정의할 수 있습니다(다른 복합 형식 내에 복합 형식을 포함할 수도 있음). 하지만 위의 예제에서 생성된 직렬화된 JSON에 중요한 사항이 있습니다. *serializer* 라이브러리로 데이터를 전송하는 **방식** 에 따라 JSON이 형성되는 방식이 정확히 결정된다는 것입니다. 이러한 특별한 내용은 다음에 다루겠습니다.

## <a name="more-about-serialization"></a>직렬화에 대한 자세한 정보
이전 섹션에서는 **serializer** 라이브러리에서 생성되는 출력의 예에 대해 살펴보았습니다. 이 섹션에서는 라이브러리가 데이터를 직렬화하는 방식과 직렬화 API를 사용하여 동작을 제어하는 방법에 대해 설명합니다.

직렬화에 대한 논의를 진행하기 위해 자동 온도 조절기를 기반으로 하는 새 모델로 작업합니다. 먼저, 해결하려는 시나리오에 대한 몇 가지 배경 지식을 제공하겠습니다.

우리는 온도 및 습도를 측정하는 자동 온도 조절기를 모델링하려고 합니다. 각 데이터 조각은 IoT Hub로 다르게 전송될 예정입니다. 기본적으로 자동 온도 조절기는 온도 이벤트는 2분에 한 번, 습도 이벤트는 15분에 한 번 수신합니다. 이벤트를 수신할 때는 해당 온도 또는 습도가 측정된 시간을 보여 주는 타임스탬프를 포함해야 합니다.

이 시나리오에서는 데이터를 모델링하는 두 가지 다른 방법을 시연하며 모델링이 직렬화된 출력에 어떤 영향을 주는지를 설명합니다.

### <a name="model-1"></a>모델 1
다음은 이전 시나리오를 지원하는 모델의 첫 번째 버전입니다.

```
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(TemperatureEvent,
int, Temperature,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_STRUCT(HumidityEvent,
int, Humidity,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureEvent, Temperature),
WITH_DATA(HumidityEvent, Humidity)
);

END_NAMESPACE(Contoso);
```

이 모델은 **온도** 및 **습도**라는 두 가지 데이터 이벤트를 포함합니다. 이전 예와 달리 각 이벤트의 형식은 **DECLARE\_STRUCT**를 사용하여 정의된 구조체입니다. **TemperatureEvent**는 온도 측정 및 타임스탬프를 포함하고 **HumidityEvent**는 습도 측정 및 타임스탬프를 포함합니다. 이 모델은 위에 설명된 시나리오에 대한 데이터를 모델링하는 자연스러운 방법을 제공합니다. 이벤트를 클라우드로 전송할 때 온도/타임스탬프 또는 습도/타임스탬프 쌍으로 전송합니다.

다음과 같은 코드를 사용하여 클라우드에 온도 이벤트를 전송할 수 있습니다.

```
time_t now;
time(&now);
thermostat->Temperature.Temperature = 75;
thermostat->Temperature.Time = GetDateTimeOffset(now);

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

여기서는 샘플 코드에 하드 코드된 온도 및 습도 값을 사용하지만 자동 온도 조절기의 해당 센서를 샘플링하여 이러한 값을 실제로 가져온다고 상상해 보세요.

위의 코드는 이전에 소개된 **GetDateTimeOffset** 도우미를 사용합니다. 나중에 이해가 쉽도록 이 코드에서는 직렬화와 이벤트 전송 작업을 명시적으로 구분합니다. 이전 코드는 온도 이벤트를 버퍼로 직렬화합니다. 다음으로 이벤트를 IoT Hub로 보내는 **sendMessage** 도우미 함수(**simplesample\_amqp**에 포함됨)입니다.

```
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId);

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
}
```

이 코드는 이전 섹션에 설명된 **SendAsync** 도우미의 하위 집합으로 여기에서 다시 다루지 않겠습니다.

이전 코드를 실행하여 온도 이벤트를 전송하면 이 직렬화된 이벤트 양식이 IoT Hub로 전송됩니다.

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

**TemperatureEvent** 형식의 온도를 전송하고 해당 구조체에는 **Temperature** 및 **Time** 멤버가 포함됩니다. 직렬화된 데이터에 이 내용이 직접 반영됩니다.

마찬가지로 이 코드로 습도 이벤트도 전송할 수 있습니다.

```
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

IoT Hub로 전송되는 직렬화된 양식은 다음과 같습니다.

```
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

역시, 예상대로 입니다.

이 모델로 추가 이벤트를 어떻게 쉽게 추가할 수 있는지 생각해볼 수 있습니다. **DECLARE\_STRUCT**를 사용하여 보다 많은 구조체를 정의하고 **WITH\_DATA**를 사용하여 해당 이벤트를 모델에 포함합니다.

이제 동일한 데이터를 포함하지만 다른 구조체를 사용하도록 모델을 수정해 보겠습니다.

### <a name="model-2"></a>모델 2
위에 대해 다음 대체 모델을 고려해보세요.

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

이 경우 **DECLARE\_STRUCT** 매크로를 제거했고 시나리오에서 모델링 언어의 단순 형식을 사용해서 데이터 항목을 정의하면 됩니다.

잠시 **시간** 이벤트를 무시하겠습니다. 이와 별개로 다음은 **온도**를 수신하는 코드입니다.

```
time_t now;
time(&now);
thermostat->Temperature = 75;

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

이 코드는 다음 직렬화된 이벤트를 IoT Hub로 전송합니다.

```
{"Temperature":75}
```

습도 이벤트를 전송하는 코드는 다음과 같습니다.

```
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

이 코드는 다음을 IoT Hub로 전송합니다.

```
{"Humidity":45}
```

지금까지 특별한 사항은 없습니다. 이제 SERIALIZE 매크로를 사용하는 방법을 변경해보겠습니다.

**SERIALIZE** 매크로는 여러 데이터 이벤트를 인수로 사용할 수 있습니다. 이를 통해 **온도** 및 **습도** 이벤트를 함께 직렬화하고 한 번의 호출로 IoT Hub로 이를 전송할 수 있습니다.

```
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

이 코드의 결과, 두 데이터 이벤트가 IoT Hub로 전송된다고 추측할 수 있습니다.

[

{"Temperature":75},

{"Humidity":45}

]

즉, 이 코드는 **온도** 및 **습도**를 별도로 전송하는 것과 같다고 예상할 수 있습니다. 두 이벤트를 **SERIALIZE** 하기 위해 동일한 호출로 전달하는 것은 단지 편의를 위해서입니다. 그러나 이 경우는 그렇지 않습니다. 대신, 위의 코드는 IoT Hub에 단일 데이터 이벤트를 전송합니다.

{"Temperature":75, "Humidity":45}

모델에서 **온도** 및 **습도**를 두 개의 *별도* 이벤트로 정의하므로 이것이 이상하게 보일 수 있습니다.

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

더 중요한 것은 **온도** 및 **습도**가 동일한 구조체에 있는 이벤트를 모델링하지 않았다는 것입니다.

```
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

이 모델을 사용한 경우에 **온도** 및 **습도**가 동일한 직렬화된 메시지로 전송되는 방식을 보다 쉽게 이해하게 됩니다. 하지만 두 데이터 이벤트를 모델 2를 사용하여 **SERIALIZE** 로 전달하는 경우 왜 그렇게 작동하는지 명확하지 않을 수 있습니다.

이 동작은 **serializer** 라이브러리의 가정 사항을 알고 있는 경우 보다 쉽게 이해할 수 있습니다. 이해를 위해 모델을 다시 살펴보겠습니다.

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

이 모델을 개체 지향 용어로 생각해봅니다. 이 경우 실제 장치(자동 온도 조절기)를 모델링하고 해당 장치가 **온도** 및 **습도**와 같은 특성을 포함합니다.

다음과 같은 코드를 사용하여 모델의 전체 상태를 전송할 수 있습니다.

```
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

온도, 습도 및 시간 값이 설정되어 있다고 가정하면 이와 같은 이벤트가 IoT Hub로 전송되는 것을 볼 수 있습니다.

```
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

*일부* 속성만 클라우드로 전송하고 싶을 때가 많습니다(모델에 대량의 데이터 이벤트가 포함된 경우 특히 그러함). 이전 예제처럼 데이터 이벤트의 하위 집합만 전송하는 것이 유용합니다.

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

그러면 **Temperature** 및 **Time** 멤버를 포함하는 **TemperatureEvent**를 정의한 것처럼 정확히 같은 직렬화된 이벤트를 생성합니다. 이것은 모델 1에서 진행했습니다. 이 경우 **직렬화**를 다른 방식으로 호출했으므로 다른 모델(모델 2)을 사용하여 정확히 같은 직렬화된 이벤트를 생성할 수 있습니다.

중요한 사항은 여러 데이터 이벤트를 **SERIALIZE** 로 전달하면 각 이벤트가 단일 JSON 개체의 속성인 것으로 가정한다는 것입니다.

최적의 접근 방식은 사용자와 사용자가 모델에 대해 어떻게 생각하는지에 따라 결정됩니다. "이벤트"를 클라우드로 전송하고 각 이벤트에 정의된 속성 집합이 있다면 첫 번째 접근 방식이 바람직합니다. 이 경우 **DECLARE\_STRUCT**를 사용하여 각 이벤트의 구조체를 정의한 후 **WITH\_DATA** 매크로를 사용하여 이를 모델에 포함합니다. 그런 다음 위의 첫 번째 예에서 했던 것처럼 각 이벤트를 전송합니다. 이 접근 방식에서는 **SERIALIZER**로 단일 데이터 이벤트만 전달합니다.

모델을 개체 지향 방식으로 생각한다면 두 번째 접근 방식이 적합할 수 있습니다. 이 경우에 **WITH\_DATA**를 사용하여 정의된 요소는 개체의 "속성"입니다. 클라우드로 전송하려는 "개체" 상태의 양에 따라 이벤트의 하위 집합을 원하는 대로 **SERIALIZE** 로 전달합니다.

어떤 접근 방식이 옳거나 그르다고 말할 수 없습니다. **serializer** 라이브러리가 작동하는 방식을 이해하고 사용자의 요구에 가장 잘 맞는 모델링 접근 방식을 선택하면 됩니다.

## <a name="message-handling"></a>메시지 처리
지금까지 이 문서에서는 이벤트를 IoT Hub에 전송하는 것에 대해서만 다루었으며 수신 메시지에 대해서는 다루지 않았습니다. 수신 메시지에 대해 알아야 하는 내용을 [이전 문서](iot-hub-device-sdk-c-intro.md)에서 폭넓게 다뤘기 때문입니다. 메시지 콜백 함수를 등록하여 메시지를 처리하는 문서를 상기해보겠습니다.

```
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

그런 다음 메시지를 수신할 때 호출되는 호출 함수를 작성합니다.

```
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
            memcpy(temp, buffer, size);
            temp[size] = '\0';
            EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
            result =
                (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
                (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
                IOTHUBMESSAGE_REJECTED;
            free(temp);
        }
    }
    return result;
}
```

**IoTHubMessage** 구현에서는 모델의 각 작업에 대해 특정 함수를 호출합니다. 예를 들어 모델에서 다음 동작을 정의하는 경우:

```
WITH_ACTION(SetAirResistance, int, Position)
```

다음 서명과 함께 함수를 정의해야 합니다.

```
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

**SetAirResistance** 가 호출됩니다.

직렬화된 버전의 메시지가 어떤 모습인지는 아직 설명하지 않았습니다. 즉, **SetAirResistance** 메시지를 장치로 전송하는 경우 어떤 모습일까요?

메시지를 장치로 전송하는 경우 Azure IoT 서비스 SDK를 통해 수행할 것입니다. 특정 동작을 호출하기 위해 어떤 문자열을 전송하는지 여전히 알아야 합니다. 메시지 전송을 위한 일반적인 형식은 다음과 같습니다.

```
{"Name" : "", "Parameters" : "" }
```

다음 두 속성으로 직렬화된 JSON 개체를 전송합니다. **Name**은 작업(메시지)의 이름이고 **Parameters**는 해당 작업의 매개 변수를 포함합니다.

예를 들어 **SetAirResistance** 를 호출하려면 다음 메시지를 장치에 보낼 수 있습니다.

```
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

동작 이름은 모델에 정의된 동작과 정확히 일치해야 합니다. 매개 변수 이름도 일치해야 합니다. 또한 대/소문자 구분 여부도 확인합니다. **Name** 및 **Parameters**는 항상 대문자입니다. 모델에서 동작 이름 및 매개 변수의 대/소문자와 일치해야 합니다. 이 예에서 동작 이름은 "setairresistance"가 아닌, "SetAirResistance"입니다.

두 가지 작업 **TurnFanOn** 및 **TurnFanOff**는 장치에 다음 메시지를 전송하여 호출할 수 있습니다.

```
{"Name" : "TurnFanOn", "Parameters" : {}}
{"Name" : "TurnFanOff", "Parameters" : {}}
```

이 섹션에서는 **serializer** 라이브러리로 이벤트를 전송하고 메시지를 수신할 때 알아야 할 모든 내용에 대해 설명합니다. 설명을 진행하기 전에, 모델의 크기를 제어하기 위해 구성할 수 있는 몇 가지 매개 변수에 대해 살펴봅니다.

## <a name="macro-configuration"></a>매크로 구성
**Serializer** 라이브러리를 사용하는 경우 알아야 할 SDK의 중요한 내용은 azure-c-shared-utility 라이브러리에 있습니다.
--recursive 옵션을 사용하여 GitHub에서 Azure-iot-sdk-c 리포지토리를 복제한 경우 이 공유 유틸리티 라이브러리를 다음에서 찾을 수 있습니다.

```
.\\c-utility
```

라이브러리를 복제하지 않은 경우 [여기](https://github.com/Azure/azure-c-shared-utility)에서 찾을 수 있습니다.

공유 유틸리티 라이브러리 내에서 다음 폴더를 찾을 수 있습니다.

```
azure-c-shared-utility\\macro\_utils\_h\_generator.
```

이 폴더에는 **macro\_utils\_h\_generator.sln**이라는 Visual Studio 솔루션이 포함되어 있습니다.

  ![](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.PNG)

이 솔루션의 프로그램은 **macro\_utils.h** 파일을 생성합니다. SDK에 포함된 기본 macro\_utils.h 파일이 있습니다. 이 솔루션을 사용하면 일부 매개 변수를 수정하고 이러한 매개 변수를 기반으로 헤더 파일을 다시 작성할 수 있습니다.

여기서 고려할 두 가지 핵심 매개 변수는 **nArithmetic** 및 **nMacroParameters**이며 macro\_utils.tt에 있는 다음 두 줄로 정의됩니다.

```
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>

```

이러한 값은 SDK에 포함된 기본 매개 변수입니다. 각 매개 변수는 다음과 같은 의미를 포함합니다.

* nMacroParameters – 하나의 DECLARE\_MODEL 매크로 정의에 포함할 수 있는 매개 변수 수를 제어합니다.
* nArithmetic – 모델에 허용되는 총 멤버 수를 제어합니다.

이러한 매개 변수는 모델이 얼마나 큰지를 제어하기 때문에 중요합니다. 예를 들어 다음 모델 정의를 고려해보겠습니다.

```
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

이전에 설명한 것처럼 **DECLARE\_MODEL**은 C 매크로입니다. 모델 및 **WITH\_DATA** 문(아직 다른 매크로)의 이름은 **DECLARE\_MODEL**의 매개 변수입니다. **nMacroParameters**는 **DECLARE\_MODEL**에 포함할 수 있는 매개 변수 수를 정의합니다. 이러한 매개 변수는 포함할 수 있는 데이터 이벤트 및 동작 선언 수를 효과적으로 정의합니다. 이와 같이 기본 제한 124를 사용하면 약 60개의 동작과 데이터 이벤트 조합을 포함하는 모델을 정의할 수 있습니다. 이 제한을 초과하려고 하는 경우 다음과 같은 컴파일러 오류가 발생합니다.

  ![](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.PNG)

**nArithmetic** 매개 변수는 응용 프로그램보다는 매크로 언어의 내부 작업에 대한 것입니다.  **DECLARE_STRUCT** 매크로를 비롯하여 모델에 포함할 수 있는 총 멤버 수를 제어합니다. 다음과 같은 컴파일러 오류가 표시되면 **nArithmetic**을 늘려야 합니다.

   ![](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.PNG)

이러한 매개 변수를 변경하려는 경우 macro\_utils.tt 파일의 값을 수정하고 macro\_utils\_h\_generator.sln 솔루션을 다시 컴파일하고 컴파일한 프로그램을 실행합니다. 이렇게 하면 새 macro\_utils.h 파일이 생성되고 .\\common\\inc 디렉터리에 배치됩니다.

새 버전의 macro\_utils.h를 사용하려면 솔루션에서 **serializer** NuGet 패키지를 제거하고 대신 그 자리에 **serializer** Visual Studio 프로젝트를 넣습니다. 이렇게 하면 serializer 라이브러리의 소스 코드에 대해 코드를 컴파일할 수 있습니다. 여기에는 업데이트된 macro\_utils.h가 포함됩니다. **simplesample\_amqp**에 대해 이 작업을 수행하려면 먼저 솔루션에서 serializer 라이브러리에 대해 NuGet 패키지를 제거합니다.

   ![](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.PNG)

그런 다음 Visual Studio 솔루션에 이 프로젝트를 추가합니다.

> .\\c\\serializer\\build\\windows\\serializer.vcxproj
> 
> 

완료되면 다음과 같이 솔루션이 표시됩니다.

   ![](media/iot-hub-device-sdk-c-serializer/05-serializer-project.PNG)

이제 솔루션을 컴파일할 때 업데이트된 macro\_utils.h가 라이브러리에 포함됩니다.

이 값을 너무 높게 늘리면 컴파일러 한계를 초과할 수 있음에 유의합니다. 이 시점까지 **nMacroParameters** 가 고려해야 할 주요 매개 변수입니다. C99 사양에서는 매크로 정의에서 최소 127개의 매개 변수를 허용하고 있습니다. Microsoft 컴파일러는 이 사양을 정확히 따르므로(127개의 제한) **nMacroParameters** 를 기본값을 초과하도록 늘릴 수 없습니다. 다른 컴파일러에서는 허용될 수도 있습니다(예를 들어 GNU 컴파일러는 보다 높은 제한 지원).

지금까지 **serializer** 라이브러리로 코드를 작성하는 방법에 대해 알아야 하는 모든 내용을 살펴봤습니다. 마무리하기 전에 궁금해할 수 있는 이전 문서의 일부 항목을 다시 확인해보겠습니다.

## <a name="the-lower-level-apis"></a>하위 수준 API
이 문서에서 집중적으로 다룬 응용 프로그램 예제는 **simplesample\_amqp**입니다. 이 샘플에서는 상위 수준(비-"LL") API를 사용하여 이벤트를 보내고 메시지를 수신합니다. 이러한 API를 사용하는 경우 이벤트 전송 및 메시지 수신을 모두 처리하는 백그라운드 스레드가 실행됩니다. 그러나 LL(하위 수준) API를 사용하여 이 백그라운드 스레드를 제거하고 이벤트를 전송하거나 클라우드에서 메시지를 수신하는 경우 명시적으로 제어할 수 있습니다.

설명한 대로 [이전 문서](iot-hub-device-sdk-c-iothubclient.md)에서 설명한 것처럼 상위 수준 API로 구성된 함수 집합이 제공됩니다.

* IoTHubClient\_CreateFromConnectionString
* IoTHubClient\_SendEventAsync
* IoTHubClient\_SetMessageCallback
* IoTHubClient\_Destroy

이러한 API는 **simplesample\_amqp**에 나와 있습니다.

유사한 하위 수준 API 집합도 있습니다.

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_Destroy

하위 수준 API가 이전 문서에서 설명한 것과 정확히 동일하게 작동한다는 것입니다. 백그라운드 스레드에서 이벤트 전송 및 메시지 수신을 처리하도록 하려면 첫 번째 API 집합을 사용하면 됩니다. IoT Hub에서 데이터를 전송 및 수신할 때 명시적으로 제어하려면 두 번째 API 집합을 사용합니다. 어떤 API 집합을 사용하든 **serializer** 라이브러리에서 모두 잘 작동합니다.

**serializer** 라이브러리와 하위 수준 API를 사용하는 방법에 대한 예제는 **simplesample\_http** 응용 프로그램을 참조하세요.

## <a name="additional-topics"></a>추가 항목
속성 처리, 대체 장치 자격 증명 사용 및 구성 옵션은 다시 강조할 필요가 있습니다. 이러한 모든 항목은 [이전 문서](iot-hub-device-sdk-c-iothubclient.md)에서 다뤘습니다. 중요한 점은 **IoTHubClient** 라이브러리로 작업할 때와 마찬가지로 **serializer** 라이브러리를 사용할 때 해당 기능이 동일하게 작동한다는 것입니다. 예를 들어 모델에서 이벤트에 속성을 첨부하려는 경우 **IoTHubMessage\_Properties** 및 **Map**\_**AddorUpdate**는 이전에 설명한 것과 동일한 방식으로 사용합니다.

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

이벤트가 **serializer** 라이브러리에서 생성되었든, 직접 작성되었든, **IoTHubClient** 라이브러리 사용은 문제가 되지 않습니다.

대체 장치 자격 증명의 경우 **IoTHubClient\_LL\_Create**를 사용하는 것은 **IOTHUB\_CLIENT\_HANDLE**을 할당하기 위한 **IoTHubClient\_CreateFromConnectionString** 만큼이나 잘 작동합니다.

마지막으로, **serializer** 라이브러리를 사용하는 경우 **IoTHubClient** 라이브러리를 사용할 때 했던 것처럼 **IoTHubClient\_LL\_SetOption**으로 구성 옵션을 설정할 수 있습니다.

**serializer** 라이브러리에서 기능은 초기화 API입니다. 라이브러리 작업을 시작하기 전에 **serializer\_init**를 호출해야 합니다.

```
serializer_init(NULL);
```

**IoTHubClient\_CreateFromConnectionString**을 호출하기 바로 전에 수행합니다.

마찬가지로 라이브러리 작업을 완료한 후 마지막 호출은 **serializer\_deinit**입니다.

```
serializer_deinit();
```

이 밖의 위에 나열된 기타 모든 기능은 **serializer** 라이브러리에서 **IoTHubClient** 라이브러리와 동일하게 작동합니다. 이러한 항목에 대한 자세한 내용은 이 시리즈의 [이전 문서](iot-hub-device-sdk-c-iothubclient.md) 를 참조하세요.

## <a name="next-steps"></a>다음 단계
이 문서는 **C용 Azure IoT 장치 SDK**에 포함된 **serializer** 라이브러리의 고유한 측면에 대해 자세히 설명합니다. 제공된 정보로 모델을 사용하여 이벤트를 전송하고 IoT Hub에서 메시지를 수신하는 방법을 잘 이해할 수 있습니다.

또한 **C용 Azure IoT 장치 SDK**로 응용 프로그램을 개발하는 방법에 대한 3부로 구성된 시리즈를 완료합니다. API를 시작하는 방법뿐만 아니라 API의 작동 방식을 매우 정확하게 이해할 수 있는 충분한 정보를 제공합니다. 자세한 정보를 위해 여기에서 다루지 않은 몇 가지 샘플이 SDK에 제공됩니다. 또는 [SDK 설명서](https://github.com/Azure/azure-iot-sdk-c) 가 추가 정보를 얻을 수 있는 훌륭한 리소스입니다.

IoT Hub를 개발하는 방법에 대한 자세한 내용은 [Azure IoT SDK][lnk-sdks]를 참조하세요.

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [Azure IoT Edge를 사용하여 에지 장치에 AI 배포][lnk-iotedge]

[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
