---
title: Azure IoT Hub 장치 쌍 시작(Java) | Microsoft Docs
description: Azure IoT Hub 장치 쌍을 사용하여 태그를 추가한 다음 IoT Hub 쿼리를 사용하는 방법입니다. Java용 Azure IoT 장치 SDK를 사용하여 장치 앱을 구현하고 Java용 Azure IoT 서비스 SDK를 사용하여 태그를 추가하고 IoT Hub 쿼리를 실행하는 서비스 앱을 구현합니다.
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2017
ms.author: dobett
ms.openlocfilehash: 8611b55f6e4f33f8946d8c7c481163166d24049c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="get-started-with-device-twins-java"></a>장치 쌍 시작(Java)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

이 자습서에서는 다음 두 개의 Java 콘솔 앱을 만듭니다.

* **add-tags-query**, 태그를 추가하고 장치 쌍을 쿼리하는 Java 백 엔드 앱입니다.
* **simulated-device**, IoT 허브에 연결하고 reported 속성을 사용하여 해당 연결 조건을 보고하는 Java 장치 앱입니다.

> [!NOTE]
> [Azure IoT SDK](iot-hub-devguide-sdks.md) 문서는 장치 및 백 엔드 앱을 빌드하는 데 사용할 수 있는 Azure IoT SDK에 대한 정보를 제공합니다.

이 자습서를 완료하려면 다음이 필요합니다.

* 최신 [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 [무료 계정](http://azure.microsoft.com/pricing/free-trial/)을 만들 수 있습니다.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-the-service-app"></a>서비스 응용 프로그램 만들기

이 섹션에서는 **myDeviceId**와 연결된 IoT Hub의 장치 쌍에 태그로 위치 메타데이터를 추가하는 Java 앱을 만듭니다. 먼저 앱은 미국에 있는 장치에 대한 IoT 허브를 쿼리한 다음 셀룰러 네트워크 연결을 보고하는 장치에 대한 IoT 허브를 쿼리합니다.

1. 개발 컴퓨터에서 `iot-java-twin-getstarted`라는 빈 폴더를 만듭니다.

1. `iot-java-twin-getstarted` 폴더에서 명령 프롬프트에서 다음 명령을 사용하여 **add-tags-query**라는 Maven 프로젝트를 만듭니다. 긴 단일 명령입니다.

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=add-tags-query -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. 명령 프롬프트에서 `add-tags-query` 폴더로 이동합니다.

1. 텍스트 편집기를 사용하여 `add-tags-query` 폴더에서 `pom.xml` 파일을 열고 **종속성** 노드에 다음 종속성을 추가합니다. 이러한 종속성을 통해 IoT Hub와 통신하도록 앱에서 **iot-service-client** 패키지를 사용할 수 있습니다.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
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

1. `pom.xml` 파일을 저장하고 닫습니다.

1. 텍스트 편집기를 사용하여 `add-tags-query\src\main\java\com\mycompany\app\App.java` 파일을 엽니다.

1. 파일에 다음 **import** 문을 추가합니다.

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

1. 다음 클래스 수준 변수를 **App** 클래스에 추가합니다. `{youriothubconnectionstring}`을 *IoT Hub 만들기* 섹션에서 기록한 IoT 허브 연결 문자열로 바꿉니다.

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String region = "US";
    public static final String plant = "Redmond43";
    ```

1. 다음 `throws` 절을 포함하도록 **main** 메서드 서명을 업데이트합니다.

    ```java
    public static void main( String[] args ) throws IOException
    ```

1. 다음 코드를 **main** 메서드에 추가하여 **DeviceTwin** 및 **DeviceTwinDevice** 개체를 만듭니다. **DeviceTwin** 개체는 IoT 허브와의 통신을 처리합니다. **DeviceTwinDevice** 개체는 해당 속성 및 태그로 장치 쌍을 나타냅니다.

    ```java
    // Get the DeviceTwin and DeviceTwinDevice objects
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);
    ```

1. 다음 `try/catch` 블록을 **main** 메서드에 추가합니다.

    ```java
    try {
      // Code goes here
    } catch (IotHubException e) {
      System.out.println(e.getMessage());
    } catch (IOException e) {
      System.out.println(e.getMessage());
    }
    ```

1. 장치 쌍에서 **region** 및 **plant** 장치 쌍 태그를 업데이트하려면 `try` 블록에 다음 코드를 추가합니다.

    ```java
    // Get the device twin from IoT Hub
    System.out.println("Device twin before update:");
    twinClient.getTwin(device);
    System.out.println(device);

    // Update device twin tags if they are different
    // from the existing values
    String currentTags = device.tagsToString();
    if ((!currentTags.contains("region=" + region) && !currentTags.contains("plant=" + plant))) {
      // Create the tags and attach them to the DeviceTwinDevice object
      Set<Pair> tags = new HashSet<Pair>();
      tags.add(new Pair("region", region));
      tags.add(new Pair("plant", plant));
      device.setTags(tags);

      // Update the device twin in IoT Hub
      System.out.println("Updating device twin");
      twinClient.updateTwin(device);
    }

    // Retrieve the device twin with the tag values from IoT Hub
    System.out.println("Device twin after update:");
    twinClient.getTwin(device);
    System.out.println(device);
    ```

1. IoT 허브에서 장치 쌍을 쿼리하려면 이전 단계에서 추가한 코드 뒤의 `try` 블록에 다음 코드를 추가합니다. 코드는 두 개의 쿼리를 실행합니다. 각 쿼리는 최대 100대의 장치를 반환합니다.

    ```java
    // Query the device twins in IoT Hub
    System.out.println("Devices in Redmond:");

    // Construct the query
    SqlQuery sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43'", null);

    // Run the query, returning a maximum of 100 devices
    Query twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 100);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }

    System.out.println("Devices in Redmond using a cellular network:");

    // Construct the query
    sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43' AND properties.reported.connectivityType = 'cellular'", null);

    // Run the query, returning a maximum of 100 devices
    twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 3);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }
    ```

1. `add-tags-query\src\main\java\com\mycompany\app\App.java` 파일을 저장하고 닫습니다.

1. **add-tags-query** 앱을 빌드하고 오류를 수정합니다. 명령 프롬프트에서 `add-tags-query` 폴더로 이동하고 다음 명령을 실행합니다.

    `mvn clean package -DskipTests`

## <a name="create-a-device-app"></a>장치 앱 만들기

이 섹션에서는 IoT Hub로 전송되는 reported 속성 값을 설정하는 Java 콘솔 앱을 만듭니다.

1. 명령 프롬프트에서 다음 명령을 사용하여 `iot-java-twin-getstarted` 폴더에 **simulated-device**라는 Maven 프로젝트를 만듭니다. 긴 단일 명령입니다.

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. 명령 프롬프트에서 `simulated-device` 폴더로 이동합니다.

1. 텍스트 편집기를 사용하여 `simulated-device` 폴더에서 `pom.xml` 파일을 열고 **종속성** 노드에 다음 종속성을 추가합니다. 이러한 종속성을 통해 IoT 허브와 통신하도록 앱에서 **iot-device-client** 패키지를 사용할 수 있습니다.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
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

1. `pom.xml` 파일을 저장하고 닫습니다.

1. 텍스트 편집기를 사용하여 `simulated-device\src\main\java\com\mycompany\app\App.java` 파일을 엽니다.

1. 파일에 다음 **import** 문을 추가합니다.

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

1. 다음 클래스 수준 변수를 **App** 클래스에 추가합니다. `{youriothubname}`을 IoT 허브 이름으로 바꾸고 `{yourdevicekey}`를 *장치 ID 만들기* 섹션에서 만든 장치 키 값으로 바꿉니다.

    ```java
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myDeviceID;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myDeviceId";
    ```

    이 샘플 앱은 **DeviceClient** 개체를 인스턴스화할 때 **프로토콜** 변수를 사용합니다. 

1. **main** 메서드에 다음 코드를 추가합니다.
    * IoT Hub와 통신하는 장치 클라이언트를 만듭니다.
    * **Device** 개체를 만들어 장치 쌍 속성을 저장합니다.

    ```java
    DeviceClient client = new DeviceClient(connString, protocol);

    // Create a Device object to store the device twin properties
    Device dataCollector = new Device() {
      // Print details when a property value changes
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context) {
        System.out.println(propertyKey + " changed to " + propertyValue);
      }
    };
    ```

1. 다음 코드를 **main** 메서드에 추가하여 **connectivityType** reported 속성을 만들고 IoT Hub로 보냅니다.

    ```java
    try {
      // Open the DeviceClient and start the device twin services.
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);

      // Create a reported property and send it to your IoT hub.
      dataCollector.setReportedProp(new Property("connectivityType", "cellular"));
      client.sendReportedProperties(dataCollector.getReportedProp());
    }
    catch (Exception e) {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. **main** 메서드의 끝에 다음 코드를 추가합니다. **Enter** 키를 기다리면 IoT Hub가 장치 쌍 작업의 상태를 보고하는 시간을 허용합니다.

    ```java
    System.out.println("Press any key to exit...");

    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    dataCollector.clean();
    client.close();
    ```

1. `simulated-device\src\main\java\com\mycompany\app\App.java` 파일을 저장하고 닫습니다.

1. **simulated-device** 앱을 빌드하고 오류를 수정합니다. 명령 프롬프트에서 `simulated-device` 폴더로 이동하고 다음 명령을 실행합니다.

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>앱 실행

이제 콘솔 앱을 실행할 준비가 되었습니다.

1. 명령 프롬프트의 `add-tags-query` 폴더에서 다음 명령을 실행하여 **add-tags-query** 서비스 앱을 실행합니다.

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![태그 값을 업데이트하고 장치 쿼리를 실행하는 Java IoT Hub 서비스 앱](media/iot-hub-java-java-twin-getstarted/service-app-1.png)

    장치 쌍에 추가된 **plant** 및 **region** 태그를 볼 수 있습니다. 첫 번째 쿼리에서 장치를 반환하지만 두 번째는 그렇지 않습니다.

1. 명령 프롬프트의 `simulated-device` 폴더에서 다음 명령을 실행하여 **connectivityType** reported 속성을 장치 쌍에 추가합니다.

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![장치 클라이언트는 **connectivityType** reported 속성을 추가합니다.](media/iot-hub-java-java-twin-getstarted/device-app-1.png)

1. 명령 프롬프트의 `add-tags-query` 폴더에서 다음 명령을 실행하여 **add-tags-query** 서비스 앱을 두 번째로 실행합니다.

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![태그 값을 업데이트하고 장치 쿼리를 실행하는 Java IoT Hub 서비스 앱](media/iot-hub-java-java-twin-getstarted/service-app-2.png)

    이제 장치는 **connectivityType** 속성을 IoT Hub에 전송했고 두 번째 쿼리에서 장치를 반환합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Portal에서 새 IoT Hub를 구성한 다음, IoT Hub의 ID 레지스트리에서 장치 ID를 만들었습니다. 백 엔드 앱에서 태그로 장치 메타데이터를 추가하고, 장치 쌍에서 장치 연결 정보를 보고하는 장치 앱을 작성했습니다. 또한 SQL과 유사한 IoT Hub 쿼리 언어를 사용하여 장치 쌍 정보를 쿼리하는 방법도 살펴보았습니다.

아래와 같이 실행할 방법을 알아보려면 다음 리소스를 참조하세요.

* [IoT Hub 시작](iot-hub-java-java-getstarted.md) 자습서를 참조하여 장치에서 원격 분석을 보냅니다.
* [직접 메서드 사용](iot-hub-java-java-direct-methods.md) 자습서를 참조하여 대화형으로(예: 사용자 제어 앱에서 팬 작동) 장치를 제어합니다.

<!-- Images. -->
[7]: ./media/iot-hub-java-java-twin-getstarted/invoke-method.png
[8]: ./media/iot-hub-java-java-twin-getstarted/device-listen.png
[9]: ./media/iot-hub-java-java-twin-getstarted/device-respond.png

<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
