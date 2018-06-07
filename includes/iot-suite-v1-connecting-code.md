## <a name="specify-the-behavior-of-the-iot-device"></a>IoT 장치의 동작 지정

IoT Hub serializer 클라이언트 라이브러리는 모델을 사용하여 장치가 IoT Hub와 교환하는 메시지의 형식을 지정합니다.

1. 다음 변수 선언을 `#include` 문 뒤에 추가합니다. 원격 모니터링 솔루션 대시보드에서 자리 표시자 값 [Device ID] 및 [Device Key]를 장치에 대해 기록한 값으로 바꿉니다. 솔루션 대시보드에서 IoT Hub 호스트 이름을 사용하여 [IoTHub Name]을 바꿉니다. 예를 들어 IoT Hub 호스트 이름이 **contoso.azure-devices.net**인 경우 [IoTHub Name]을 **contoso**로 바꿉니다.
   
    ```c
    static const char* deviceId = "[Device Id]";
    static const char* connectionString = "HostName=[IoTHub Name].azure-devices.net;DeviceId=[Device Id];SharedAccessKey=[Device Key]";
    ```

1. 다음 코드를 추가하여 장치에서 IoT Hub와 통신하도록 지원하는 모델을 정의합니다. 이 모델은 장치에서 다음이 가능하도록 지정합니다.

   - 온도, 외부 온도, 습도 및 장치 ID를 원격 분석으로 보낼 수 있습니다.
   - 장치에 대한 메타데이터를 IoT Hub에 보낼 수 있습니다. 장치는 시작 시 **DeviceInfo** 개체에서 기본 메타데이터를 보냅니다.
   - reported 속성을 IoT Hub의 장치 쌍에 보낼 수 있습니다. 이러한 reported 속성은 구성, 장치 및 시스템 속성으로 그룹화됩니다.
   - IoT Hub의 장치 쌍에서 설정된 desired 속성을 수신하고 작업을 수행할 수 있습니다.
   - 솔루션 포털을 통해 호출된 **Reboot** 및 **InitiateFirmwareUpdate** 직접 메서드에 응답할 수 있습니다. 장치는 reported 속성을 사용하여 지원하는 직접 메서드에 대한 정보를 보냅니다.
   
    ```c
    // Define the Model
    BEGIN_NAMESPACE(Contoso);

    /* Reported properties */
    DECLARE_STRUCT(SystemProperties,
      ascii_char_ptr, Manufacturer,
      ascii_char_ptr, FirmwareVersion,
      ascii_char_ptr, InstalledRAM,
      ascii_char_ptr, ModelNumber,
      ascii_char_ptr, Platform,
      ascii_char_ptr, Processor,
      ascii_char_ptr, SerialNumber
    );

    DECLARE_STRUCT(LocationProperties,
      double, Latitude,
      double, Longitude
    );

    DECLARE_STRUCT(ReportedDeviceProperties,
      ascii_char_ptr, DeviceState,
      LocationProperties, Location
    );

    DECLARE_MODEL(ConfigProperties,
      WITH_REPORTED_PROPERTY(double, TemperatureMeanValue),
      WITH_REPORTED_PROPERTY(uint8_t, TelemetryInterval)
    );

    /* Part of DeviceInfo */
    DECLARE_STRUCT(DeviceProperties,
      ascii_char_ptr, DeviceID,
      _Bool, HubEnabledState
    );

    DECLARE_DEVICETWIN_MODEL(Thermostat,
      /* Telemetry (temperature, external temperature and humidity) */
      WITH_DATA(double, Temperature),
      WITH_DATA(double, ExternalTemperature),
      WITH_DATA(double, Humidity),
      WITH_DATA(ascii_char_ptr, DeviceId),

      /* DeviceInfo */
      WITH_DATA(ascii_char_ptr, ObjectType),
      WITH_DATA(_Bool, IsSimulatedDevice),
      WITH_DATA(ascii_char_ptr, Version),
      WITH_DATA(DeviceProperties, DeviceProperties),

      /* Device twin properties */
      WITH_REPORTED_PROPERTY(ReportedDeviceProperties, Device),
      WITH_REPORTED_PROPERTY(ConfigProperties, Config),
      WITH_REPORTED_PROPERTY(SystemProperties, System),

      WITH_DESIRED_PROPERTY(double, TemperatureMeanValue, onDesiredTemperatureMeanValue),
      WITH_DESIRED_PROPERTY(uint8_t, TelemetryInterval, onDesiredTelemetryInterval),

      /* Direct methods implemented by the device */
      WITH_METHOD(Reboot),
      WITH_METHOD(InitiateFirmwareUpdate, ascii_char_ptr, FwPackageURI),

      /* Register direct methods with solution portal */
      WITH_REPORTED_PROPERTY(ascii_char_ptr_no_quotes, SupportedMethods)
    );

    END_NAMESPACE(Contoso);
    ```

## <a name="implement-the-behavior-of-the-device"></a>장치의 동작 구현
이제 모델에 정의된 동작을 구현하는 코드를 추가합니다.

1. 솔루션 대시보드에서 설정된 desired 속성을 처리하는 다음 함수를 추가합니다. 이러한 desired 속성은 모델에서 정의됩니다.

    ```c
    void onDesiredTemperatureMeanValue(void* argument)
    {
      /* By convention 'argument' is of the type of the MODEL */
      Thermostat* thermostat = argument;
      printf("Received a new desired_TemperatureMeanValue = %f\r\n", thermostat->TemperatureMeanValue);

    }

    void onDesiredTelemetryInterval(void* argument)
    {
      /* By convention 'argument' is of the type of the MODEL */
      Thermostat* thermostat = argument;
      printf("Received a new desired_TelemetryInterval = %d\r\n", thermostat->TelemetryInterval);
    }
    ```

1. IoT Hub를 통해 호출된 직접 메서드를 처리하는 다음 함수를 추가합니다. 이러한 직접 메서드는 모델에서 정의됩니다.

    ```c
    /* Handlers for direct methods */
    METHODRETURN_HANDLE Reboot(Thermostat* thermostat)
    {
      (void)(thermostat);

      METHODRETURN_HANDLE result = MethodReturn_Create(201, "\"Rebooting\"");
      printf("Received reboot request\r\n");
      return result;
    }

    METHODRETURN_HANDLE InitiateFirmwareUpdate(Thermostat* thermostat, ascii_char_ptr FwPackageURI)
    {
      (void)(thermostat);

      METHODRETURN_HANDLE result = MethodReturn_Create(201, "\"Initiating Firmware Update\"");
      printf("Recieved firmware update request. Use package at: %s\r\n", FwPackageURI);
      return result;
    }
    ```

1. 미리 구성된 솔루션으로 메시지를 보내는 다음 함수를 추가합니다.
   
    ```c
    /* Send data to IoT Hub */
    static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
    {
      IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
      if (messageHandle == NULL)
      {
        printf("unable to create a new IoTHubMessage\r\n");
      }
      else
      {
        if (IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, NULL, NULL) != IOTHUB_CLIENT_OK)
        {
          printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
          printf("IoTHubClient accepted the message for delivery\r\n");
        }

        IoTHubMessage_Destroy(messageHandle);
      }
      free((void*)buffer);
    }
    ```

1. 장치가 미리 구성된 솔루션에 새 reported 속성 값을 전송했을 때 실행되는 다음 콜백 처리기를 추가합니다.

    ```c
    /* Callback after sending reported properties */
    void deviceTwinCallback(int status_code, void* userContextCallback)
    {
      (void)(userContextCallback);
      printf("IoTHub: reported properties delivered with status_code = %u\n", status_code);
    }
    ```

1. 다음 함수를 추가하여 클라우드의 미리 구성된 솔루션에 장치를 연결하고 데이터를 교환합니다. 이 함수는 다음 단계를 수행합니다.

    - 플랫폼을 초기화합니다.
    - serialization 라이브러리와 함께 Contoso 네임스페이스를 등록합니다.
    - 장치 연결 문자열을 사용하여 클라이언트를 초기화합니다.
    - **자동 온도 조절기** 모델의 인스턴스를 만듭니다.
    - reported 속성 값을 만들고 보냅니다.
    - **DeviceInfo** 개체를 보냅니다.
    - 1초마다 원격 분석을 보내는 루프를 만듭니다.
    - 모든 리소스의 초기화를 취소합니다.

    ```c
    void remote_monitoring_run(void)
    {
      if (platform_init() != 0)
      {
        printf("Failed to initialize the platform.\n");
      }
      else
      {
        if (SERIALIZER_REGISTER_NAMESPACE(Contoso) == NULL)
        {
          printf("Unable to SERIALIZER_REGISTER_NAMESPACE\n");
        }
        else
        {
          IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, MQTT_Protocol);
          if (iotHubClientHandle == NULL)
          {
            printf("Failure in IoTHubClient_CreateFromConnectionString\n");
          }
          else
          {
    #ifdef MBED_BUILD_TIMESTAMP
            // For mbed add the certificate information
            if (IoTHubClient_SetOption(iotHubClientHandle, "TrustedCerts", certificates) != IOTHUB_CLIENT_OK)
            {
                printf("Failed to set option \"TrustedCerts\"\n");
            }
    #endif // MBED_BUILD_TIMESTAMP
            Thermostat* thermostat = IoTHubDeviceTwin_CreateThermostat(iotHubClientHandle);
            if (thermostat == NULL)
            {
              printf("Failure in IoTHubDeviceTwin_CreateThermostat\n");
            }
            else
            {
              /* Set values for reported properties */
              thermostat->Config.TemperatureMeanValue = 55.5;
              thermostat->Config.TelemetryInterval = 3;
              thermostat->Device.DeviceState = "normal";
              thermostat->Device.Location.Latitude = 47.642877;
              thermostat->Device.Location.Longitude = -122.125497;
              thermostat->System.Manufacturer = "Contoso Inc.";
              thermostat->System.FirmwareVersion = "2.22";
              thermostat->System.InstalledRAM = "8 MB";
              thermostat->System.ModelNumber = "DB-14";
              thermostat->System.Platform = "Plat 9.75";
              thermostat->System.Processor = "i3-7";
              thermostat->System.SerialNumber = "SER21";
              /* Specify the signatures of the supported direct methods */
              thermostat->SupportedMethods = "{\"Reboot\": \"Reboot the device\", \"InitiateFirmwareUpdate--FwPackageURI-string\": \"Updates device Firmware. Use parameter FwPackageURI to specify the URI of the firmware file\"}";

              /* Send reported properties to IoT Hub */
              if (IoTHubDeviceTwin_SendReportedStateThermostat(thermostat, deviceTwinCallback, NULL) != IOTHUB_CLIENT_OK)
              {
                printf("Failed sending serialized reported state\n");
              }
              else
              {
                printf("Send DeviceInfo object to IoT Hub at startup\n");
    
                thermostat->ObjectType = "DeviceInfo";
                thermostat->IsSimulatedDevice = 0;
                thermostat->Version = "1.0";
                thermostat->DeviceProperties.HubEnabledState = 1;
                thermostat->DeviceProperties.DeviceID = (char*)deviceId;

                unsigned char* buffer;
                size_t bufferSize;

                if (SERIALIZE(&buffer, &bufferSize, thermostat->ObjectType, thermostat->Version, thermostat->IsSimulatedDevice, thermostat->DeviceProperties) != CODEFIRST_OK)
                {
                  (void)printf("Failed serializing DeviceInfo\n");
                }
                else
                {
                  sendMessage(iotHubClientHandle, buffer, bufferSize);
                }

                /* Send telemetry */
                thermostat->Temperature = 50;
                thermostat->ExternalTemperature = 55;
                thermostat->Humidity = 50;
                thermostat->DeviceId = (char*)deviceId;

                while (1)
                {
                  unsigned char*buffer;
                  size_t bufferSize;

                  (void)printf("Sending sensor value Temperature = %f, Humidity = %f\n", thermostat->Temperature, thermostat->Humidity);

                  if (SERIALIZE(&buffer, &bufferSize, thermostat->DeviceId, thermostat->Temperature, thermostat->Humidity, thermostat->ExternalTemperature) != CODEFIRST_OK)
                  {
                    (void)printf("Failed sending sensor value\r\n");
                  }
                  else
                  {
                    sendMessage(iotHubClientHandle, buffer, bufferSize);
                  }

                  ThreadAPI_Sleep(1000);
                }

                IoTHubDeviceTwin_DestroyThermostat(thermostat);
              }
            }
            IoTHubClient_Destroy(iotHubClientHandle);
          }
          serializer_deinit();
        }
      }
      platform_deinit();
    }
    ```
   
    참고로, 미리 구성된 솔루션으로 전송되는 샘플 **원격 분석** 메시지는 다음과 같습니다.
   
    ```
    {"DeviceId":"mydevice01", "Temperature":50, "Humidity":50, "ExternalTemperature":55}
    ```