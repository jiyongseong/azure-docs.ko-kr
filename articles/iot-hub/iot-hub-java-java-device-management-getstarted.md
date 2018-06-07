---
title: "Azure IoT Hub 장치 관리 시작(Java) | Microsoft Docs"
description: "Azure IoT Hub 장치 관리를 사용하여 원격 장치 재부팅을 시작하는 방법입니다. Java용 Azure IoT 장치 SDK를 사용하여 직접 메서드를 포함한 시뮬레이션된 장치 앱을 구현하며 Java용 Azure IoT service SDK를 사용하여 직접 메서드를 호출하는 서비스 앱을 구현합니다."
services: iot-hub
documentationcenter: .java
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 7e3837582e2020dc560a2b624352f7326ea87c3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-device-management-java"></a>장치 관리 시작(Java)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

* Azure Portal을 사용하여 IoT Hub를 만들고 IoT Hub에 장치 ID를 만듭니다.
* 장치를 다시 부팅하는 직접 메서드를 구현하는 시뮬레이트된 장치 앱을 만듭니다. 직접 메서드는 클라우드에서 호출됩니다.
* IoT Hub를 통해 시뮬레이트된 장치 앱에서 재부팅 직접 메서드를 호출하는 앱을 만듭니다. 그러면 이 앱이 장치에서 보고된 속성을 모니터링하여 재부팅 작업이 완료되는 시간을 확인합니다.

이 자습서를 마치면 다음 두 가지 Java 콘솔 앱이 만들어집니다.

**simulated-device**. 이 앱의 기능:

* IoT Hub를 앞에서 만든 장치 ID에 연결합니다.
* 재부팅 직접 메서드 호출을 수신합니다.
* 물리적 재부팅을 시뮬레이트합니다.
* 보고된 속성을 통해 마지막 재부팅 시간을 보고합니다.

**trigger-reboot**. 이 앱의 기능:

* 시뮬레이트된 장치 앱에서 직접 메서드를 호출합니다.
* 시뮬레이트된 장치에서 보낸 직접 메서드 호출에 대한 응답을 표시합니다.
* 업데이트된 보고된 속성을 표시합니다.

> [!NOTE]
> 장치와 솔루션 백 엔드에서 실행하기 위해 응용 프로그램을 빌드하는 데 사용할 수 있는 SDK에 관한 정보는 [Azure IoT SDK][lnk-hub-sdks]를 참조하세요.

이 자습서를 완료하려면 다음이 필요합니다.

* Java SE 8. <br/> [개발 환경 준비][lnk-dev-setup]는 Windows 또는 Linux에서 이 자습서에 대한 Java를 설치하는 방법을 설명합니다.
* Maven 3.  <br/> [개발 환경 준비][lnk-dev-setup]는 Windows 또는 Linux에서 이 자습서에 대한 [Maven][lnk-maven]을 설치하는 방법을 설명합니다.
* [Node.js 버전 0.10.0 이상](http://nodejs.org)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>직접 메서드를 사용하여 장치에서 원격 재부팅 트리거

이 섹션에서는 다음을 수행하는 Java 콘솔 앱을 만듭니다.

1. 시뮬레이트된 장치 앱에서 재부팅 직접 메서드를 호출합니다.
1. 응답을 표시합니다.
1. 장치에서 보낸 보고된 속성을 폴링하여 재부팅이 완료되는 시간을 확인합니다.

이 콘솔 앱은 IoT Hub에 연결하여 직접 메서드를 호출하고 보고된 속성을 읽습니다.

1. dm-get-started라는 빈 폴더를 만듭니다.

1. 명령 프롬프트에서 다음 명령을 사용하여 dm-get-started folder 폴더에 **trigger-reboot**라는 Maven 프로젝트를 만듭니다. 다음은 긴 단일 명령입니다.

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. 명령 프롬프트에서 trigger-reboot 폴더로 이동합니다.

1. 텍스트 편집기를 사용하여 trigger-reboot 폴더에서 pom.xml 파일을 열고 **종속성** 노드에 다음 종속성을 추가합니다. 이러한 종속성을 통해 IoT Hub와 통신하도록 앱에서 iot-service-client 패키지를 사용할 수 있습니다.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > [Maven 검색][lnk-maven-service-search]을 사용하여 **iot-service-client**의 최신 버전을 확인할 수 있습니다.

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

1. 텍스트 편집기를 사용하여 trigger-reboot\src\main\java\com\mycompany\app\App.java 원본 파일을 엽니다.

1. 파일에 다음 **import** 문을 추가합니다.

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwin;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

1. 다음 클래스 수준 변수를 **App** 클래스에 추가합니다. `{youriothubconnectionstring}`은 *IoT Hub 만들기* 섹션에서 기록한 IoT Hub 연결 문자열로 바꿉니다.

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

1. 장치 쌍에서 10초마다 보고되는 속성을 읽는 스레드를 구현하려면 **App** 클래스에 다음 중첩 클래스를 추가합니다.

    ```java
    private static class ShowReportedProperties implements Runnable {
      public void run() {
        try {
          DeviceTwin deviceTwins = DeviceTwin.createFromConnectionString(iotHubConnectionString);
          DeviceTwinDevice twinDevice = new DeviceTwinDevice(deviceId);
          while (true) {
            System.out.println("Get reported properties from device twin");
            deviceTwins.getTwin(twinDevice);
            System.out.println(twinDevice.reportedPropertiesToString());
            Thread.sleep(10000);
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

1. 다음 예외를 throw하도록 **main** 메서드의 서명을 수정합니다.

    ```java
    public static void main(String[] args) throws IOException
    ```

1. 시뮬레이트된 장치에서 재부팅 직접 메서드를 호출하려면 **main** 메서드에 다음 코드를 추가합니다.

    ```java
    System.out.println("Starting sample...");
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
      System.out.println("Invoke reboot direct method");
      MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, null);

      if(result == null)
      {
        throw new IOException("Invoke direct method reboot returns null");
      }
      System.out.println("Invoked reboot on device");
      System.out.println("Status for device:   " + result.getStatus());
      System.out.println("Message from device: " + result.getPayload());
    }
    catch (IotHubException e)
    {
        System.out.println(e.getMessage());
    }
    ```

1. 시뮬레이트된 장치에서 보고된 속성을 스레드가 폴링을 시작하도록 하려면 **main** 메서드에 다음 코드를 추가합니다.

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

1. 앱을 중지할 수 있도록 하려면 **main** 메서드에 다음 코드를 추가합니다.

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

1. trigger-reboot\src\main\java\com\mycompany\app\App.java 파일을 저장한 후 닫습니다.

1. **trigger-reboot** 백 엔드 앱을 빌드하고 오류를 수정합니다. 명령 프롬프트에서 trigger-reboot 폴더로 이동한 후 다음 명령을 실행합니다.

    `mvn clean package -DskipTests`

## <a name="create-a-simulated-device-app"></a>시뮬레이션된 장치 앱 만들기

이 섹션에서는 장치를 시뮬레이트하는 Java 콘솔 앱을 만듭니다. 이 앱은 IoT Hub의 재부팅 직접 메서드 호출을 수신하고 그 즉시 해당 호출에 응답합니다. 그런 다음 잠시 유휴 상태로 전환하여 재부팅 프로세스를 시뮬레이트한 후 보고된 속성을 사용하여 **trigger-reboot** 백 엔드 앱에 재부팅이 완료되었음을 알립니다.

1. 명령 프롬프트에서 다음 명령을 사용하여 dm-get-started 폴더에 **simulated-device**라는 Maven 프로젝트를 만듭니다. 다음은 긴 단일 명령입니다.

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. 명령 프롬프트에서 simulated-device 폴더로 이동합니다.

1. 텍스트 편집기를 사용하여 simulated-device 폴더에서 pom.xml 파일을 열고 **종속성** 노드에 다음 종속성을 추가합니다. 이러한 종속성을 통해 IoT Hub와 통신하도록 앱에서 iot-service-client 패키지를 사용할 수 있습니다.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > [Maven 검색][lnk-maven-device-search]을 사용하여 **iot-device-client**의 최신 버전을 확인할 수 있습니다.

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
    ```

1. 다음 클래스 수준 변수를 **App** 클래스에 추가합니다. `{yourdeviceconnectionstring}`을 *장치 ID 만들기* 섹션에서 기록해 둔 장치 연결 문자열로 바꿉니다.

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

1. 직접 메서드 상태 이벤트에 대한 콜백 처리기를 구현하려면 **App** 클래스에 다음 중첩 클래스를 추가합니다.

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

1. 장치 쌍 상태 이벤트에 대한 콜백 처리기를 구현하려면 **App** 클래스에 다음 중첩 클래스를 추가합니다.

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

1. 속성 이벤트에 대한 콜백 처리기를 구현하려면 **App** 클래스에 다음 중첩 클래스를 추가합니다.

    ```java
    protected static class PropertyCallback implements PropertyCallBack<String, String>
    {
      public void PropertyCall(String propertyKey, String propertyValue, Object context)
      {
        System.out.println("PropertyKey:     " + propertyKey);
        System.out.println("PropertyKvalue:  " + propertyKey);
      }
    }
    ```

1. 장치 재부팅을 시뮬레이트하는 스레드를 구현하려면 **App** 클래스에 다음 중첩 클래스를 추가합니다. 이 스레드는 5초 동안 유휴 상태를 유지한 후 **lastReboot** 보고된 속성을 설정합니다.

    ```java
    protected static class RebootDeviceThread implements Runnable {
      public void run() {
        try {
          System.out.println("Rebooting...");
          Thread.sleep(5000);
          Property property = new Property("lastReboot", LocalDateTime.now());
          Set<Property> properties = new HashSet<Property>();
          properties.add(property);
          client.sendReportedProperties(properties);
          System.out.println("Rebooted");
        }
        catch (Exception ex) {
          System.out.println("Exception in reboot thread: " + ex.getMessage());
        }
      }
    }
    ```

1. 장치에서 직접 메서드를 구현하려면 **App** 클래스에 다음 중첩 클래스를 추가합니다. 시뮬레이트된 앱은 **재부팅** 직접 메서드에 대한 호출을 수신하면 호출자에게 수신 확인을 반환한 후 재부팅을 처리하기 위한 스레드를 시작합니다.

    ```java
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "reboot" :
          {
            int status = METHOD_SUCCESS;
            System.out.println("Received reboot request");
            deviceMethodData = new DeviceMethodData(status, "Started reboot");
            RebootDeviceThread rebootThread = new RebootDeviceThread();
            Thread t = new Thread(rebootThread);
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

1. 다음 예외를 throw하도록 **main** 메서드의 서명을 수정합니다.

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. **DeviceClient**를 인스턴스화하려면 **main** 메서드에 다음 코드를 추가합니다.

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

1. 직접 메서드 호출 수신을 시작하려면 **main** 메서드에 다음 코드를 추가합니다.

    ```java
    try
    {
      client.open();
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
      client.startDeviceTwin(new DeviceTwinStatusCallback(), null, new PropertyCallback(), null);
      System.out.println("Subscribed to direct methods and polling for reported properties. Waiting...");
    }
    catch (Exception e)
    {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. 장치 시뮬레이터를 종료하려면 **main** 메서드에 다음 코드를 추가합니다.

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

1. simulated-device\src\main\java\com\mycompany\app\App.java 파일을 저장한 후 닫습니다.

1. **simulated-device** 백 엔드 앱을 빌드하고 오류를 수정합니다. 명령 프롬프트에서 simulated-device 폴더로 이동한 후 다음 명령을 실행합니다.

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>앱 실행

이제 앱을 실행할 준비가 되었습니다.

1. simulated-device 폴더의 명령 프롬프트에서 다음 명령을 실행하여 IoT Hub의 재부팅 메서드 호출에 대한 수신 대기를 시작합니다.

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![재부팅 직접 메서드 호출을 수신 대기할 Java IoT Hub 시뮬레이트된 장치 앱][1]

1. trigger-reboot 폴더의 명령 프롬프트에서 다음 명령을 실행하여 IoT Hub의 시뮬레이트된 장치에 대해 재부팅 메서드를 호출합니다.

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![재부팅 직접 메서드를 호출할 Java IoT Hub 서비스 앱][2]

1. 시뮬레이트된 장치는 재부팅 직접 메서드 호출에 응답합니다.

    ![Java IoT Hub 시뮬레이트된 장치 앱은 직접 메서드 호출에 응답합니다.][3]

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

<!-- images and links -->
[1]: ./media/iot-hub-java-java-device-management-getstarted/launchsimulator.png
[2]: ./media/iot-hub-java-java-device-management-getstarted/triggerreboot.png
[3]: ./media/iot-hub-java-java-device-management-getstarted/respondtoreboot.png
<!-- Links -->

[lnk-maven]: https://maven.apache.org/what-is-maven.html

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md

[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
[lnk-maven-device-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22