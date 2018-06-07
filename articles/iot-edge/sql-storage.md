---
title: Azure IoT Edge SQL 모듈 | Microsoft Docs
description: Microsoft SQL 모듈로 에지에 데이터를 저장하고 Azure Functions를 사용하여 데이터 형식을 지정합니다.
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: ebertrams
ms.date: 02/21/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: de75a7ab907655d84873949460692a921c8d3186
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166100"
---
# <a name="store-data-at-the-edge-with-sql-server-databases"></a>SQL Server 데이터베이스로 에지에 데이터 저장

Azure IoT Edge 장치를 사용하여 에지에서 생성된 데이터를 저장합니다. 인터넷 연결이 간헐적인 장치는 자체 데이터베이스를 유지 관리할 수 있고 연결되었을 때만 클라우드로 변경 내용을 보고할 수 있습니다. 중요한 데이터만 클라우드에 보내도록 프로그래밍된 장치는 일괄 대량 업로드를 위해 나머지 데이터를 저장할 수 있습니다. 데이터가 클라우드에 들어가면 구조화된 데이터를 다른 Azure 서비스와 공유하여 예를 들면 기계 학습 모델을 구축할 수 있습니다. 

이 문서에서는 SQL Server 데이터베이스를 IoT Edge 장치에 배포하는 지침을 제공합니다. Azure Functions는 IoT Edge 장치에서 실행되며, 들어오는 데이터를 구조화한 다음, 데이터베이스로 보냅니다. 이 문서의 단계는 컨테이너에서 작동하는 다른 데이터베이스(예: MySQL 또는 PostgreSQL)에도 적용할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건 

이 문서의 지침을 시작하기 전에 다음 자습서를 완료해야 합니다.
* [Windows](tutorial-simulate-device-windows.md) 또는 [Linux](tutorial-simulate-device-linux.md)에서 시뮬레이트된 장치에 Azure IoT Edge 배포
* [Azure 함수를 IoT Edge 모듈로 배포](tutorial-deploy-function.md)

다음 문서는 이 자습서를 완료하는 데 필요하지는 않지만 유용한 컨텍스트를 제공합니다.
* [Docker로 SQL Server 2017 컨테이너 이미지 실행](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker)
* [Visual Studio Code를 사용하여 Azure Functions 개발 및 Azure IoT Edge에 배포](how-to-vscode-develop-azure-function.md)

필요한 자습서를 완료한 후에는 모든 필수 구성 요소가 컴퓨터에 준비되어야 합니다. 
* 활성 Azure IoT Hub
* RAM이 2GB 이상이고 디스크 드라이브가 2GB 이상인 IoT Edge 장치
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Visual Studio Code용 Azure IoT Edge 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [C# for Visual Studio Code(OmniSharp 제공) 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Docker](https://docs.docker.com/engine/installation/)
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd) 
* [Python 2.7](https://www.python.org/downloads/)
* [IoT Edge 제어 스크립트](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
* AzureIoTEdgeFunction 템플릿(`dotnet new -i Microsoft.Azure.IoT.Edge.Function`)
* 하나 이상의 IoT Edge 장치가 있는 활성 IoT Hub.

x64 프로세서 아키텍처의 Windows 및 Linux 컨테이너는 모두 이 자습서에 적합합니다. SQL Server는 ARM 프로세서를 지원하지 않습니다.

## <a name="deploy-a-sql-server-container"></a>SQL Server 컨테이너 배포

이 섹션에서는 시뮬레이션된 IoT Edge 장치에 MS-SQL 데이터베이스를 추가합니다. SQL Server 2017 Docker 컨테이너 이미지를 사용하여 [Windows](https://hub.docker.com/r/microsoft/mssql-server-windows-developer/) 컨테이너와 [Linux](https://hub.docker.com/r/microsoft/mssql-server-linux/) 컨테이너로 제공합니다. 

### <a name="deploy-sql-server-2017"></a>SQL Server 2017 배포

기본적으로 이 섹션의 코드는 SQL Server 2017의 무료 개발자 버전으로 컨테이너를 만듭니다. 프로덕션 버전을 대신 실행하려면 [프로덕션 컨테이너 이미지 실행](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#production)에서 자세한 내용을 참조하세요. 

3단계에서 SQL Server 컨테이너에 만들기 옵션을 추가하는 데, 이것은 환경 변수 및 영구적인 저장소를 설정하는 데 중요합니다. 구성된 [환경 변수](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-environment-variables)는 최종 사용자 사용권 계약에 동의하고 암호를 정의합니다. [영구적인 저장소](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#persist)는 [mount](https://docs.docker.com/storage/volumes/)를 사용하여 구성됩니다. mount는 컨테이너가 삭제되더라도 데이터가 유지되도록 *sqlvolume* 볼륨 컨테이너가 연결된 SQL Server 2017 컨테이너를 만듭니다. 

1. Visual Studio Code에서 `deployment.json` 파일을 엽니다. 
2. 파일의 **modules** 섹션을 다음 코드로 바꿉니다. 

   ```json
   "modules": {
          "filterFunction": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "<docker registry address>/filterfunction:latest",
              "createOptions": "{}"
            }
          },
          "tempSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
              "createOptions": "{}"
            }
          },
          "sql": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "",
              "createOptions": ""
             }
          }
        }
   ```

3. `<docker registry address>`를 완료된 [IoT Edge 모듈로 Azure Functions 배포](tutorial-deploy-function.md) 자습서에서 채워진 주소로 바꿉니다.

   >[!NOTE]
   >컨테이너 레지스트리 주소는 레지스트리에서 복사한 로그인 서버와 같습니다. `<your container registry name>.azurecr.io` 형식이어야 합니다.

4. 실행 중인 운영 체제에 따라 SQL 모듈에 대한 설정을 다음 코드로 업데이트합니다. 

   * Windows:

      ```json
      "image": "microsoft/mssql-server-windows-developer",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"MSSQL_SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"C:\\\\mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}}"
      ```

   * Linux:

      ```json
      "image": "microsoft/mssql-server-linux:2017-latest",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"MSSQL_SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"/var/opt/mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}}"
      ```

5. 파일을 저장합니다. 
6. VS Code 명령 팔레트에서 **Edge: Edge 장치에 대한 배포 만들기**를 선택합니다. 
7. IoT Edge 장치 ID를 선택합니다.
8. 업데이트한 `deployment.json` 파일을 선택합니다. 출력 창에서 배포에 해당하는 출력을 볼 수 있습니다. 
9. Edge 런타임을 시작하려면 명령 팔레트에서 **Edge: Edge 시작**을 선택합니다.

>[!TIP]
>프로덕션 환경에서 SQL Server 컨테이너를 만들 때마다 [기본 시스템 관리자 암호를 변경](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker#change-the-sa-password)해야 합니다.

## <a name="create-the-sql-database"></a>SQL 데이터베이스 만들기

이 섹션에서는 IoT Edge 장치에 연결된 센서로부터 받은 온도 데이터를 저장하도록 SQL 데이터베이스를 설정하는 과정을 안내합니다. 시뮬레이션된 장치를 사용하는 경우 *tempSensor* 컨테이너에서 이 데이터를 가져옵니다. 

명령줄 도구에서 데이터베이스에 연결합니다. 

* Windows 컨테이너
   ```cmd
   docker exec -it sql cmd
   ```

* Linux 컨테이너
   ```bash
   docker exec -it sql bash
   ```

SQL 명령 도구를 엽니다. 

* Windows 컨테이너
   ```cmd
   sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

* Linux 컨테이너
   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

데이터베이스를 만듭니다. 

* Windows 컨테이너
   ```sql
   CREATE DATABASE MeasurementsDB
   ON
   (NAME = MeasurementsDB, FILENAME = 'C:\mssql\measurementsdb.mdf')
   GO
   ```

* Linux 컨테이너
   ```sql
   CREATE DATABASE MeasurementsDB
   ON
   (NAME = MeasurementsDB, FILENAME = '/var/opt/mssql/measurementsdb.mdf')
   GO
   ```

테이블을 정의합니다. 

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

SQL Server가 여러 IoT Edge 장치에 배포되도록 자동으로 설정하도록 SQL Server Docker 파일을 사용자 지정할 수 있습니다. 자세한 내용은 [Microsoft SQL Server 컨테이너 데모 프로젝트](https://github.com/twright-msft/mssql-node-docker-demo-app)를 참조하세요.

## <a name="understand-the-sql-connection"></a>SQL 연결 이해

다른 자습서에서는 서로 격리된 상태에서 컨테이너가 통신할 수 있도록 경로를 사용합니다. SQL Server 데이터베이스를 사용하는 경우에는 보다 긴밀한 관계가 필요합니다. 

IoT Edge는 시작할 때 브리지(Linux) 또는 NAT(Windows) 네트워크를 자동으로 구축합니다. 이런 네트워크를 **azure-iot-edge**라고 합니다. 이 연결을 디버그해야 하는 경우 명령줄에서 해당 속성을 찾을 수 있습니다.

* Windows

   ```cmd
   docker network inspect azure-iot-edge
   ```

* Linux

   ```bash
   sudo docker network inspect azure-iot-edge
   ```

또한 IoT Edge는 Docker를 통해 컨테이너 이름의 DNS를 확인할 수 있으므로 SQL Server 데이터베이스를 IP 주소로 참조할 필요가 없습니다. 

예를 들어, 다음 섹션에서 사용할 연결 문자열은 다음과 같습니다. 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

연결 문자열이 컨테이너를 이름인 **sql**로 참조하는 것을 확인할 수 있습니다. 모듈 이름을 다른 이름으로 변경한 경우 이 연결 문자열도 업데이트합니다. 그렇지 않으면 다음 섹션을 계속 진행합니다. 

## <a name="update-your-azure-function"></a>Azure 함수 이름 지정
데이터베이스에 데이터를 보내려면 이전 자습서에서 만든 FilterFunction Azure 함수를 업데이트합니다. 센서가 수신한 데이터를 구조화하도록 파일을 변경한 다음, SQL 테이블에 저장합니다. 

1. Visual Studio Code에서 FilterFunction 폴더를 엽니다. 
2. run.csx 파일을 이전 섹션의 SQL 연결 문자열이 포함된 다음 코드로 바꿉니다. 

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"
   #r "System.Data.SqlClient"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;
   using Sql = System.Data.SqlClient;
   using System.Threading.Tasks;

   // Filter messages based on the temperature value in the body of the message and the temperature threshold value.
   public static async Task Run(Message messageReceived, IAsyncCollector<Message> output, TraceWriter log)
   {
       const int temperatureThreshold = 25;
       byte[] messageBytes = messageReceived.GetBytes();
       var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

       if (!string.IsNullOrEmpty(messageString))
       {
           // Get the body of the message and deserialize it
           var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

           //Store the data in SQL db
           const string str = "Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;";
           using (Sql.SqlConnection conn = new Sql.SqlConnection(str))
           {
           conn.Open();
           var insertMachineTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'machine', " + messageBody.machine.temperature + ");";
           var insertAmbientTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'ambient', " + messageBody.ambient.temperature + ");"; 
               using (Sql.SqlCommand cmd = new Sql.SqlCommand(insertMachineTemperature + "\n" + insertAmbientTemperature, conn))
               {
               //Execute the command and log the # rows affected.
               var rows = await cmd.ExecuteNonQueryAsync();
               log.Info($"{rows} rows were updated");
               }
           }

           if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
           {
               // Send the message to the output as the temperature value is greater than the threshold
               var filteredMessage = new Message(messageBytes);
               // Copy the properties of the original message into the new Message object
               foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
               {
                   filteredMessage.Properties.Add(prop.Key, prop.Value);
               }
               // Add a new property to the message to indicate it is an alert
               filteredMessage.Properties.Add("MessageType", "Alert");
               // Send the message        
               await output.AddAsync(filteredMessage);
               log.Info("Received and transferred a message with temperature above the threshold");
           }
       }
   }

   //Define the expected schema for the body of incoming messages
   class MessageBody
   {
       public Machine machine {get;set;}
       public Ambient ambient {get; set;}
       public string timeCreated {get; set;}
   }
   class Machine
   {
      public double temperature {get; set;}
      public double pressure {get; set;}         
   }
   class Ambient
   {
      public double temperature {get; set;}
      public int humidity {get; set;}         
   }
   ```

## <a name="update-your-container-image"></a>컨테이너 이미지 업데이트

변경한 내용을 적용하려면 컨테이너 이미지를 업데이트하고 게시한 다음, IoT Edge를 다시 시작합니다.

1. Visual Studio Code에서 **Docker** 폴더를 확장합니다. 
2. 사용하는 플랫폼을 기반으로 **windows-nano** 또는 **linux-x64** 폴더를 확장합니다. 
3. **Dockerfile** 파일을 마우스 오른쪽 단추로 클릭하고 **IoT Edge 모듈 Docker 이미지 빌드**를 선택합니다.
4. **FilterFunction** 프로젝트 폴더로 이동하고 **EXE_DIR로 폴더 선택**을 클릭합니다.
5. VS Code 창의 맨 위에 있는 팝업 텍스트 상자에 이미지 이름을 입력합니다. 예: `<your container registry address>/filterfunction:latest` 로컬 레지스트리에 배포하는 경우 이름은 `<localhost:5000/filterfunction:latest>`여야 합니다.
6. VS Code 명령 팔레트에서 **Edge: IoT Edge 모듈 Docker 이미지 푸시**를 선택합니다. 
7. 팝업 텍스트 상자에 동일한 이미지 이름을 입력합니다. 
8. VS Code 명령 팔레트에서 **Edge: Edge 다시 시작**을 선택합니다.

## <a name="view-the-local-data"></a>로컬 데이터 보기

컨테이너가 다시 시작되면 온도 센서로부터 받은 데이터가 IoT Edge 장치의 로컬 SQL Server 2017 데이터베이스에 저장됩니다. 

명령줄 도구에서 데이터베이스에 연결합니다. 

* Windows 컨테이너
   ```cmd
   docker exec -it sql cmd
   ```

* Linux 컨테이너
   ```bash
   docker exec -it sql bash
   ```

SQL 명령 도구를 엽니다. 

* Windows 컨테이너
   ```cmd
   sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

* Linux 컨테이너
   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

데이터 보기: 

   ```sql
   SELECT * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

## <a name="next-steps"></a>다음 단계

* [Docker에서 SQL Server 2017 컨테이너 이미지를 구성](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker)하는 방법을 알아봅니다.

* [mssql-docker GitHub 리포지토리](https://github.com/Microsoft/mssql-docker)로 이동하여 리소스, 피드백 및 알려진 문제를 참조합니다.
