---
title: Azure IoT Hub를 사용하여 장치 펌웨어 업데이트(Java/Java) | Microsoft Docs
description: 장치 펌웨어 업데이트를 시작하려면 Azure IoT Hub에서 장치 관리를 사용하는 방법입니다. Java용 Azure IoT 장치 SDK를 사용하여 시뮬레이션된 장치 앱을 구현하고 펌웨어 업데이트를 트리거하는 서비스 앱을 구현합니다.
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: v-masebo;dobett
ms.openlocfilehash: ff8fc5cc1623c570f79a8acfa0afbcf95450c1d2
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-javajava"></a>장치 관리를 사용하여 장치 펌웨어 업데이트(Java/Java)를 시작합니다.
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

[장치 관리 시작][lnk-dm-getstarted] 자습서에서 [장치 쌍][lnk-devtwin] 및 [직접 메서드][lnk-c2dmethod] 기본 형식을 사용하여 장치를 원격으로 다시 부팅하는 방법을 살펴보았습니다. 이 자습서에서는 동일한 IoT Hub 기본 형식을 사용하고 시뮬레이션된 종단 간 펌웨어 업데이트를 수행하는 방법을 보여 줍니다.  이 패턴은 [Raspberry Pi 장치 구현 샘플][lnk-rpi-implementation]에 대한 펌웨어 업데이트 구현에 사용됩니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

* IoT Hub를 통해 시뮬레이션된 장치 앱에서 **firmwareUpdate** 직접 메서드를 호출하는 Java 콘솔 앱을 만듭니다.
* 장치를 시뮬레이트하고 **firmwareUpdate** 직접 메서드를 구현하는 Java 콘솔 앱을 만듭니다. 이 메서드는 펌웨어 이미지를 다운로드하기 위해 대기했다가 펌웨어 이미지를 다운로드하고, 마지막으로 펌웨어 이미지를 적용하는 다단계 프로세스를 시작합니다. 업데이트의 각 단계 동안, 장치는 보고된 속성을 사용하여 진행 상황을 보고합니다.

이 자습서를 마치면 다음 두 가지 Java 콘솔 앱이 만들어집니다.

**firmware-update**, 시뮬레이션된 장치에서 직접 메서드를 호출하고 응답을 표시하며 보고된 속성 업데이트를 주기적으로 표시합니다.

**simulated-device**, 이전에 만든 장치 ID로 IoT Hub에 연결하고 firmwareUpdate 직접 메서드 호출을 수신하며 펌웨어 업데이트 시뮬레이션을 실행합니다.

이 자습서를 완료하려면 다음이 필요합니다.

* 최신 [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 
* [Maven 3](https://maven.apache.org/install.html) 
* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>직접 메서드를 사용하여 장치에서 원격 펌웨어 업데이트 트리거
이 섹션에서는 장치에서 원격 펌웨어 업데이트를 시작하는 Java 콘솔 앱을 만듭니다. 앱은 직접 메서드를 사용하여 업데이트를 시작하고 장치 쌍 쿼리를 사용하여 정기적으로 활성 펌웨어 업데이트의 상태를 가져옵니다.

1. fw-get-started라는 빈 폴더를 만듭니다.

1. 명령 프롬프트에서 다음 명령을 사용하여 fw-get-started 폴더에 **firmware-update**라는 Maven 프로젝트를 만듭니다. 긴 단일 명령입니다.

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=firmware-update -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. 명령 프롬프트에서 firmware-update 폴더로 이동합니다.

1. 텍스트 편집기를 사용하여 firmware-update 폴더에서 pom.xml 파일을 열고 **종속성** 노드에 다음 종속성을 추가합니다. 이러한 종속성을 통해 IoT Hub와 통신하도록 앱에서 iot-service-client 패키지를 사용할 수 있습니다.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.5.22</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > [Maven 검색](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)을 사용하여 **iot-service-client**의 최신 버전을 확인할 수 있습니다.

1. **종속성** 노드 뒤에 다음 **빌드** 노드를 추가합니다. 이 구성에서는 Maven에 Java 1.8을 사용하여 앱을 빌드하도록 지시합니다.

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

1. pom.xml 파일을 저장하고 닫습니다.

1. 텍스트 편집기를 사용하여 firmware-update\src\main\java\com\mycompany\app\App.java 원본 파일을 엽니다.

1. 파일에 다음 **import** 문을 추가합니다.

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwin;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    ```

1. 다음 클래스 수준 변수를 **App** 클래스에 추가합니다. **{youriothubconnectionstring}**을 *IoT Hub 만들기* 섹션에서 기록한 IoT Hub 연결 문자열로 대체합니다.

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "firmwareUpdate";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

1. 장치 쌍에서 보고되는 속성을 읽는 메서드를 구현하려면 **App** 클래스에 다음을 추가합니다.

    ```java
    public static void ShowReportedProperties() 
    {
        try 
        {
          DeviceTwin deviceTwins = DeviceTwin.createFromConnectionString(iotHubConnectionString);
          DeviceTwinDevice twinDevice = new DeviceTwinDevice(deviceId);
          
          Boolean firmwareUpdated = false;
          Integer timeoutCycle = 0;
    
          while (!firmwareUpdated)
          {
            if (timeoutCycle > 5)
            {
              System.out.println("Operation timed out");
              break;
            }
    
            Thread.sleep(1000);
              
            deviceTwins.getTwin(twinDevice);
    
            String reportedProperties = twinDevice.reportedPropertiesToString();
              
            if(reportedProperties.contains("status=waiting"))
            {
              System.out.println("Waiting on device...");
            }
            else if(reportedProperties.contains("status=downloadComplete"))
            {
              System.out.println("Download complete, applying firmware...");
            }
            else if (reportedProperties.contains("status=applyComplete"))
            {
              System.out.println("Firmware applied");
              System.out.println("Get reported properties from device twin");
              System.out.println(twinDevice.reportedPropertiesToString());
              firmwareUpdated = true;
            }
            else
            {
              timeoutCycle++;
            }
          }
        } catch (Exception ex) {
            System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
    }
    ```

1. 다음 예외를 throw하도록 **main** 메서드의 서명을 수정합니다.

    ```java
    public static void main( String[] args ) throws IOException
    ```

1. 시뮬레이션된 장치에서 firmwareUpdate 직접 메서드를 호출하려면 **main** 메서드에 다음 코드를 추가합니다.

    ```java
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
      String payload = "https://someurl";
      
      System.out.println("Invoked firmware update on device.");
      System.out.println("Sent URL: " + payload);
      
      MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, payload);

      if(result == null)
      {
        throw new IOException("Invoke direct method reboot returns null");
      }

      System.out.println("Status for device:   " + result.getStatus());
      System.out.println("Message from device: " + result.getPayload());
    }
    catch (IotHubException e)
    {
      System.out.println(e.getMessage());
    }
    ```

1. 시뮬레이션된 장치에서 보고된 속성을 폴링하려면 **main** 메서드에 다음 코드를 추가합니다.

    ```java
    ShowReportedProperties();
    ```

1. 앱을 중지할 수 있도록 하려면 **main** 메서드에 다음 코드를 추가합니다.

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    System.out.println("Shutting down sample...");
    ```

1. firmware-update\src\main\java\com\mycompany\app\App.java 파일을 저장하고 닫습니다.

1. **firmware-update** 백 엔드 앱을 빌드하고 오류를 수정합니다. 명령 프롬프트에서 firmware-update 폴더로 이동하고 다음 명령을 실행합니다.

    `mvn clean package -DskipTests`

## <a name="simulate-a-device-to-handle-direct-method-calls"></a>직접 메서드 호출을 처리할 장치 시뮬레이트
이 섹션에서는 firmwareUpdate 직접 메서드를 수신할 수 있는 Java 콘솔 시뮬레이션 장치 앱을 만듭니다. 이 앱은 다중 상태 프로세스를 실행하고 reportedProperties를 사용하여 펌웨어 업데이트를 시뮬레이트하여 상태를 전달합니다.

1. 명령 프롬프트에서 다음 명령을 사용하여 fw-get-started 폴더에 **simulated-device**라는 Maven 프로젝트를 만듭니다. 긴 단일 명령입니다.

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. 명령 프롬프트에서 simulated-device 폴더로 이동합니다.

1. 텍스트 편집기를 사용하여 firmware-update 폴더에서 pom.xml 파일을 열고 **종속성** 노드에 다음 종속성을 추가합니다. 이러한 종속성을 통해 IoT Hub와 통신하도록 앱에서 iot-service-client 패키지를 사용할 수 있습니다.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > [Maven 검색](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)을 사용하여 **iot-device-client**의 최신 버전을 확인할 수 있습니다.

1. **종속성** 노드 뒤에 다음 **빌드** 노드를 추가합니다. 이 구성에서는 Maven에 Java 1.8을 사용하여 앱을 빌드하도록 지시합니다.

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

1. pom.xml 파일을 저장하고 닫습니다.

1. 텍스트 편집기를 사용하여 simulated-device\src\main\java\com\mycompany\app\App.java 원본 파일을 엽니다.

1. 파일에 다음 **import** 문을 추가합니다.

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.time.LocalDateTime;
    import java.util.Scanner;
    import java.util.Set;
    import java.util.HashSet;
    import java.util.HashMap;
    ```

1. 다음 클래스 수준 변수를 **App** 클래스에 추가합니다. **{yourdeviceconnectionstring}**을 *장치 ID 만들기* 섹션에서 기록해 둔 장치 연결 문자열로 바꿉니다.

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring";
    private static DeviceClient client;

    private static String downloadURL = "unknown";
    ```

1. 직접 메서드 기능을 구현하려면 **App** 클래스에 다음 중첩 클래스를 추가하여 콜백을 제공합니다.

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "firmwareUpdate" :
          {
            System.out.println("Response to method '" + methodName + "' sent successfully");
    
            downloadURL = new String((byte[])methodData);
    
            int status = METHOD_SUCCESS;
            System.out.println("Starting firmware update");
            deviceMethodData = new DeviceMethodData(status, "Started firmware update");
            FirmwareUpdateThread firmwareUpdateThread = new FirmwareUpdateThread();
            Thread t = new Thread(firmwareUpdateThread);
            t.start();
            break;
          }
          default:
          {
            int status = METHOD_NOT_DEFINED;
            deviceMethodData = new DeviceMethodData(status, "Not defined direct method " + methodName);
          }
        }
        return deviceMethodData;
      }
    }
    ```

1. 장치 쌍 기능을 구현하려면 다음 중첩 클래스를 **App** 클래스에 추가하여 콜백을 제공합니다.

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    protected static class PropertyCallback implements PropertyCallBack<String, String>
    {
      public void PropertyCall(String propertyKey, String propertyValue, Object context)
      {
        System.out.println("PropertyKey:     " + propertyKey);
        System.out.println("PropertyKvalue:  " + propertyKey);
      }
    }
    ```

1. 펌웨어 업데이트를 구현하려면 **App** 클래스에 다음 중첩 클래스를 추가합니다.

    ```java
    protected static class FirmwareUpdateThread implements Runnable {
      public void run() {
        try {      
          HashMap initialUpdate = new HashMap();
          Property sentProperty = new Property("firmwareUpdate", initialUpdate);
          Set<Property> sentPackage = new HashSet<Property>();
          
          System.out.println("Downloading from " + downloadURL);
    
          initialUpdate.put("status", "waiting");
          initialUpdate.put("fwPackageUri", downloadURL);
          initialUpdate.put("startedWaitingTime", LocalDateTime.now().toString());   
          sentPackage.add(sentProperty);
    
          client.sendReportedProperties(sentPackage);
    
          Thread.sleep(5000);
    
          System.out.println("Download complete");
    
          HashMap downloadUpdate = new HashMap();
          downloadUpdate.put("status","downloadComplete");
          downloadUpdate.put("downloadCompleteTime", LocalDateTime.now().toString());
          downloadUpdate.put("startedApplyingImage", LocalDateTime.now().toString());
          sentProperty.setValue(downloadUpdate);
    
          client.sendReportedProperties(sentPackage);
    
          Thread.sleep(5000);
    
          System.out.println("Apply complete");
    
          HashMap applyUpdate = new HashMap();
          applyUpdate.put("status","applyComplete");
          applyUpdate.put("lastFirmwareUpdate", LocalDateTime.now().toString());
          sentProperty.setValue(applyUpdate);
    
          client.sendReportedProperties(sentPackage);
    
          Thread.sleep(5000);
    
          HashMap resetUpdate = new HashMap();
          applyUpdate.put("status","reset");
          sentProperty.setValue(resetUpdate);
    
          client.sendReportedProperties(sentPackage);
        }
        catch (Exception ex) {
          System.out.println("Exception in reboot thread: " + ex.getMessage());
        }
      }
    }
    ```

1. 다음 예외를 throw하도록 **main** 메서드의 서명을 수정합니다.

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. 직접 메서드와 장치 쌍 루틴을 시작하려면 **main** 메서드에 다음 코드를 추가합니다.

    ```java
    client = new DeviceClient(connString, protocol);

    try
    {
      client.open();
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
      client.startDeviceTwin(new DeviceTwinStatusCallback(), null, new PropertyCallback(), null);
      System.out.println("Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.");
    }
    catch (Exception e)
    {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. 앱을 중지할 수 있도록 하려면 **main** 메서드의 끝에 다음 코드를 추가합니다.

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

1. simulated-device\src\main\java\com\mycompany\app\App.java 파일을 저장한 후 닫습니다.

1. **simulated-device** 앱을 빌드하고 오류를 수정합니다. 명령 프롬프트에서 simulated-device 폴더로 이동한 후 다음 명령을 실행합니다.

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>앱 실행
이제 앱을 실행할 준비가 되었습니다.

1. **simulated-device** 폴더의 명령 프롬프트에서 펌웨어 업데이트 직접 메서드에 대한 수신 대기를 시작합니다.
   
    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. **firmware-update** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 IoT Hub의 시뮬레이트된 장치에서 펌웨어 업데이트를 호출하고 장치 쌍을 쿼리합니다.

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

3. 콘솔에서 직접 메서드에 응답하는 시뮬레이션된 장치를 볼 수 있습니다.

    ![펌웨어가 성공적으로 업데이트됨][img-fwupdate]

## <a name="next-steps"></a>다음 단계
이 자습서에서는 직접 메서드를 사용하여 장치에서 원격 펌웨어 업데이트를 트리거하고, 보고된 속성을 사용하여 펌웨어 업데이트 프로세스의 진행 상황을 이해했습니다.

IoT 솔루션을 확장하고 여러 장치에서 메서드 호출을 예약하는 방법을 알아보려면 [jobs 예약 및 브로드캐스트][lnk-tutorial-jobs] 자습서를 참조하세요.

<!-- images -->
[img-fwupdate]: media/iot-hub-java-java-firmware-update/firmwareUpdated.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-java-java-device-management-getstarted.md
[lnk-tutorial-jobs]: iot-hub-java-java-schedule-jobs.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device