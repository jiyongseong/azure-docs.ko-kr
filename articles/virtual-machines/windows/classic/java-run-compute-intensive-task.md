---
title: VM의 계산 집약적 Java 응용 프로그램
description: 다른 Java 응용 프로그램에 의해 모니터링될 수 있는 계산 집약적인 Java 응용 프로그램을 실행하는 가상 머신을 만드는 방법에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: java
author: rmcmurray
manager: mbaldwin
tags: azure-service-management,azure-resource-manager
ms.assetid: ae6f2737-94c7-4569-9913-d871450c2827
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: Java
ms.topic: article
ms.date: 04/11/2018
ms.author: robmcm
ms.openlocfilehash: e8da296c30f1d2be3c637e456e90d2f93da67548
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="how-to-run-a-compute-intensive-task-in-java-on-a-virtual-machine"></a>가상 머신에서 Java로 계산 집약적인 작업을 실행하는 방법
> [!IMPORTANT] 
> Azure에는 리소스를 만들고 작업하기 위한 [리소스 관리자 및 클래식](../../../resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Azure에서 가상 머신을 사용하여 계산 집약적인 작업을 처리할 수 있습니다. 예를 들어 가상 머신은 작업을 처리하고 그 결과를 클라이언트 컴퓨터 또는 모바일 응용 프로그램에 제공할 수 있습니다. 이 문서를 읽고 나면 다른 Java 응용 프로그램에 의해 모니터링될 수 있는 계산 집약적인 Java 응용 프로그램을 실행하는 가상 머신을 만드는 방법을 이해할 수 있게 됩니다.

이 자습서에서는 사용자가 Java 콘솔 응용 프로그램을 만드는 방법을  알고 있으며 라이브러리를 Java 응용 프로그램으로 가져오고 Java 아카이브(JAR)를 생성할 수 있다고 가정합니다. Microsoft Azure에 대한 지식은 없는 것으로 가정합니다.

다음 내용을 배웁니다.

* Java 개발 키트(JDK)가 이미 설치된 가상 컴퓨터를 만드는 방법
* 가상 머신에 원격으로 로그인하는 방법
* 서비스 버스 네임스페이스를 만드는 방법
* 계산 집약적인 작업을 수행하는 Java 응용 프로그램을 만드는 방법
* 계산 집약적인 작업의 진행 상황을 모니터링하는 Java 응용 프로그램을 만드는 방법
* Java 응용 프로그램을 실행하는 방법
* Java 응용 프로그램을 중지하는 방법

이 자습서에서는 계산 집약적인 작업으로 순회 외판원 문제를 사용합니다. 다음은 계산 집약적인 작업을 실행하는 Java 응용 프로그램의 예제입니다.

![순회 외판원 문제 해 찾기][solver_output]

다음은 계산 집약적인 작업을 모니터링하는 Java 응용 프로그램의 예제입니다.

![순회 외판원 문제 클라이언트][client_output]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>가상 머신을 만드는 방법
1. [Azure 포털](https://portal.azure.com) 에 로그인합니다.
2. **리소스 만들기**를 클릭하고 **Compute**, **가상 머신**, **갤러리에서**를 차례로 클릭합니다.
3. **가상 머신 이미지 선택** 대화 상자에서 **JDK 7 Windows Server 2012**를 선택합니다.
   **JDK 6 Windows Server 2012** 는 아직 JDK 7에서 실행할 준비가 되지 않은 레거시 응용 프로그램이 있는 경우에 사용 가능합니다.
4. **다음**을 클릭합니다.
5. **가상 머신 구성** 대화 상자에서 다음을 수행합니다.
   1. 가상 머신의 이름을 지정합니다.
   2. 가상 머신에 사용할 크기를 지정합니다.
   3. **사용자 이름** 필드에 관리자의 이름을 입력합니다. 입력하는 이름 및 암호는 나중에 가상 머신에 원격으로 로그인할 때 사용합니다.
   4. **새 암호** 필드에 암호를 입력하고 **확인** 필드에 다시 입력합니다. 이 암호는 관리자 계정 암호입니다.
   5. **다음**을 클릭합니다.
6. 다음 **가상 머신 구성** 대화 상자에서 다음을 수행합니다.
   1. **클라우드 서비스**의 경우 기본값인 **새 클라우드 서비스 만들기**를 사용합니다.
   2. **클라우드 서비스 DNS 이름** 값은 cloudapp.net에서 고유해야 합니다. 필요한 경우 Azure에서 고유한 이름이 되도록 수정합니다.
   3. 지역, 선호도 그룹 또는 가상 네트워크를 지정합니다. 이 자습서에서는 지역(예: **미국 서부**)을 지정합니다.
   4. **Storage 계정** 상자에서 **자동으로 생성된 Storage 계정 사용**을 선택합니다.
   5. **가용성 집합**에서 **(없음)** 을 선택합니다.
   6. **다음**을 클릭합니다.
7. 마지막 **가상 머신 구성** 대화 상자에서 다음을 수행합니다.
   1. 기본 끝점 항목을 그대로 사용합니다.
   2. 페이지 맨 아래에 있는 **완료**을 참조하세요.

## <a name="to-remotely-log-in-to-your-virtual-machine"></a>가상 머신에 원격으로 로그인하는 방법
1. [Azure 포털](https://portal.azure.com)에 로그온합니다.
2. **가상 머신**을 클릭합니다.
3. 로그인할 가상 머신의 이름을 클릭합니다.
4. **연결**을 클릭합니다.
5. 가상 컴퓨터에 연결해야 한다는 메시지에 응답합니다. 관리자 이름 및 암호를 묻는 메시지가 표시되면 가상 머신을 만들 때 제공한 값을 사용하십시오.

Azure Service Bus 기능을 사용하려면 Baltimore CyberTrust 루트 인증서가 JRE의 **cacerts** 저장소의 일부로 설치되어야 합니다. 이 인증서는 본 자습서에서 사용하는 JRE(Java Runtime Environment)에 자동으로 포함되어 있습니다. 이 인증서가 JRE **cacerts** 저장소에 없는 경우, [Java CA 인증서 저장소에 인증서 추가][add_ca_cert]에서 인증서 추가에 대한 내용 및 cacerts 저장소의 인증서 보기에 대한 정보를 참조하십시오.

## <a name="how-to-create-a-service-bus-namespace"></a>서비스 버스 네임스페이스를 만드는 방법
Azure에서 Service Bus 큐 사용을 시작하려면 먼저 서비스 네임스페이스를 만들어야 합니다. 서비스 네임스페이스는 응용 프로그램 내에서 Service Bus 리소스의 주소를 지정하기 위한 범위 컨테이너를 제공합니다.

서비스 네임스페이스를 만들려면

1. [Azure 포털](https://portal.azure.com)에 로그온합니다.
2. Azure Portal의 왼쪽 하단 탐색 창에서 **Service Bus, Access Control 및 Caching**을 클릭합니다.
3. Azure Portal의 왼쪽 상단 창에서 **Service Bus** 노드 및 **새로 만들기** 단추를 차례로 클릭합니다.  
   ![Service Bus 노드 스크린샷][svc_bus_node]
4. **새 서비스 네임스페이스 만들기** 대화 상자에서 **네임스페이스**를 입력한 후 네임스페이스가 중복되지 않는지 확인하기 위해 **중복 확인** 단추를 클릭합니다.  
   ![새 네임스페이스 만들기 스크린샷][create_namespace]
5. 네임스페이스 이름이 사용 가능한지 확인한 후 해당 네임스페이스를 호스트할 국가 또는 지역을 선택한 다음, **Create Namespace** 단추를 클릭합니다.  
   
   만든 네임스페이스는 Azure Portal에 나타나며, 잠시 후에 활성화됩니다. 다음 단계를 계속하기 전에 **활성** 상태가 될 때까지 기다리십시오.

## <a name="obtain-the-default-management-credentials-for-the-namespace"></a>네임스페이스에 대한 기본 관리 자격 증명 얻기
새 네임스페이스에 대해 큐 만들기 등의 관리 작업을 수행하려면 네임스페이스에 대한 관리 자격 증명을 받아야 합니다.

1. 왼쪽 탐색 창에서 **Service Bus** 노드를 클릭하여 사용 가능한 네임스페이스 목록을 표시합니다.
   ![사용 가능한 네임스페이스 스크린샷][avail_namespaces]
2. 표시된 목록에서 방금 만든 네임스페이스를 선택합니다.
   ![네임스페이스 목록 스크린샷][namespace_list]
3. 오른쪽 **속성** 창에 새 네임스페이스의 속성이 나열됩니다.
   ![속성 창 스크린샷][properties_pane]
4. **기본 키** 가 숨겨져 있습니다. **보기** 단추를 클릭하여 보안 자격 증명을 표시합니다.
   ![기본 키 스크린샷][default_key]
5. **기본 발급자** 및 **기본 키**를 기록해 둡니다. 이 정보는 아래에서 네임스페이스 관련 작업을 수행하는 데 사용됩니다.

## <a name="how-to-create-a-java-application-that-performs-a-compute-intensive-task"></a>계산 집약적인 작업을 수행하는 Java 응용 프로그램을 만드는 방법
1. 개발 컴퓨터(직접 생성한 가상 머신일 필요는 없음)에서 [Java용 Azure SDK](https://azure.microsoft.com/develop/java/)를 다운로드합니다.
2. 이 섹션의 끝부분에 있는 예제 코드를 사용하여 Java 콘솔 응용 프로그램을 만듭니다. 이 자습서에서는 Java 파일 이름으로 **TSPSolver.java** 를 사용합니다. **your\_service\_bus\_namespace**, **your\_service\_bus\_owner** 및 **your\_service\_bus\_key** 자리 표시자를 각각 Service Bus **네임스페이스**, **기본 발급자** 및 **기본 키** 값을 사용하도록 수정합니다.
3. 코딩 후에 응용 프로그램을 실행 가능한 Java 아카이브(JAR)로 내보내고 필요한 라이브러리를 생성된 JAR 안에 패키징합니다. 이 자습서에서는 생성된 JAR 이름으로 **TSPSolver.jar** 을 사용합니다.

<p/>

    // TSPSolver.java

    import com.microsoft.windowsazure.services.core.Configuration;
    import com.microsoft.windowsazure.services.core.ServiceException;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import java.io.*;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import java.util.ArrayList;
    import java.util.Date;
    import java.util.List;

    public class TSPSolver {

        //  Value specifying how often to provide an update to the console.
        private static long loopCheck = 100000000;  

        private static long nTimes = 0, nLoops=0;

        private static double[][] distances;
        private static String[] cityNames;
        private static int[] bestOrder;
        private static double minDistance;
        private static ServiceBusContract service;

        private static void buildDistances(String fileLocation, int numCities) throws Exception{
            try{
                BufferedReader file = new BufferedReader(new InputStreamReader(new DataInputStream(new FileInputStream(new File(fileLocation)))));
                double[][] cityLocs = new double[numCities][2];
                for (int i = 0; i<numCities; i++){
                    String[] line = file.readLine().split(", ");
                    cityNames[i] = line[0];
                    cityLocs[i][0] = Double.parseDouble(line[1]);
                    cityLocs[i][1] = Double.parseDouble(line[2]);
                }
                for (int i = 0; i<numCities; i++){
                    for (int j = i; j<numCities; j++){
                        distances[i][j] = Math.hypot(Math.abs(cityLocs[i][0] - cityLocs[j][0]), Math.abs(cityLocs[i][1] - cityLocs[j][1]));
                        distances[j][i] = distances[i][j];
                    }
                }
            } catch (Exception e){
                throw e;
            }
        }

        private static void permutation(List<Integer> startCities, double distSoFar, List<Integer> restCities) throws Exception {

            try
            {
                nTimes++;
                if (nTimes == loopCheck)
                {
                    nLoops++;
                    nTimes = 0;
                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.print("Current time is " + dateFormat.format(date) + ". ");
                    System.out.println(  "Completed " + nLoops + " iterations of size of " + loopCheck + ".");
                }

                if ((restCities.size() == 1) && ((minDistance == -1) || (distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-1)] < minDistance))){
                    startCities.add(restCities.get(0));
                    newBestDistance(startCities, distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-2)]);
                    startCities.remove(startCities.size()-1);
                }
                else{
                    for (int i=0; i<restCities.size(); i++){
                        startCities.add(restCities.get(0));
                        restCities.remove(0);
                        permutation(startCities, distSoFar + distances[startCities.get(startCities.size()-1)][startCities.get(startCities.size()-2)],restCities);
                        restCities.add(startCities.get(startCities.size()-1));
                        startCities.remove(startCities.size()-1);
                    }
                }
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        private static void newBestDistance(List<Integer> cities, double distance) throws ServiceException, Exception {
            try
            {
                minDistance = distance;
                String cityList = "Shortest distance is "+minDistance+", with route: ";
                for (int i = 0; i<bestOrder.length; i++){
                    bestOrder[i] = cities.get(i);
                    cityList += cityNames[bestOrder[i]];
                    if (i != bestOrder.length -1)
                        cityList += ", ";
                }
                System.out.println(cityList);
                service.sendQueueMessage("TSPQueue", new BrokeredMessage(cityList));
            }
            catch (ServiceException se)
            {
                throw se;
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        public static void main(String args[]){

            try {

                Configuration config = ServiceBusConfiguration.configureWithWrapAuthentication(
                        "your_service_bus_namespace", "your_service_bus_owner",
                        "your_service_bus_key",
                        ".servicebus.windows.net",
                        "-sb.accesscontrol.windows.net/WRAPv0.9");

                service = ServiceBusService.create(config);

                int numCities = 10;  // Use as the default, if no value is specified at command line.
                if (args.length != 0)
                {
                    if (args[0].toLowerCase().compareTo("createqueue")==0)
                    {
                        // No processing to occur other than creating the queue.
                        QueueInfo queueInfo = new QueueInfo("TSPQueue");

                        service.createQueue(queueInfo);

                        System.out.println("Queue named TSPQueue was created.");

                        System.exit(0);
                    }

                    if (args[0].toLowerCase().compareTo("deletequeue")==0)
                    {
                        // No processing to occur other than deleting the queue.
                        service.deleteQueue("TSPQueue");

                        System.out.println("Queue named TSPQueue was deleted.");

                        System.exit(0);
                    }

                    // Neither creating or deleting a queue.
                    // Assume the value passed in is the number of cities to solve.
                    numCities = Integer.valueOf(args[0]);  
                }

                System.out.println("Running for " + numCities + " cities.");

                List<Integer> startCities = new ArrayList<Integer>();
                List<Integer> restCities = new ArrayList<Integer>();
                startCities.add(0);
                for(int i = 1; i<numCities; i++)
                    restCities.add(i);
                distances = new double[numCities][numCities];
                cityNames = new String[numCities];
                buildDistances("c:\\TSP\\cities.txt", numCities);
                minDistance = -1;
                bestOrder = new int[numCities];
                permutation(startCities, 0, restCities);
                System.out.println("Final solution found!");
                service.sendQueueMessage("TSPQueue", new BrokeredMessage("Complete"));
            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }
        }

    }



## <a name="how-to-create-a-java-application-that-monitors-the-progress-of-the-compute-intensive-task"></a>계산 집약적인 작업의 진행 상황을 모니터링하는 Java 응용 프로그램을 만드는 방법
1. 개발 컴퓨터에서 이 섹션의 끝부분에 있는 예제 코드를 사용하여 Java 콘솔 응용 프로그램을 만듭니다. 이 자습서에서는 Java 파일 이름으로 **TSPClient.java** 를 사용합니다. **your\_service\_bus\_namespace**, **your\_service\_bus\_owner** 및 **your\_service\_bus\_key** 자리 표시자를 각각 Service Bus **네임스페이스**, **기본 발급자** 및 **기본 키** 값을 사용하도록 수정합니다.
2. 응용 프로그램을 실행 가능한 JAR로 내보내고 필요한 라이브러리를 생성된 JAR 안에 패키징합니다. 이 자습서에서는 생성된 JAR 이름으로 **TSPClient.jar** 을 사용합니다.

<p/>

    // TSPClient.java

    import java.util.Date;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;

    public class TSPClient
    {

        public static void main(String[] args)
        {
                try
                {

                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.println("Starting at " + dateFormat.format(date) + ".");

                    String namespace = "your_service_bus_namespace";
                    String issuer = "your_service_bus_owner";
                    String key = "your_service_bus_key";

                    Configuration config;
                    config = ServiceBusConfiguration.configureWithWrapAuthentication(
                            namespace, issuer, key,
                            ".servicebus.windows.net",
                            "-sb.accesscontrol.windows.net/WRAPv0.9");

                    ServiceBusContract service = ServiceBusService.create(config);

                    BrokeredMessage message;

                    int waitMinutes = 3;  // Use as the default, if no value is specified at command line.
                    if (args.length != 0)
                    {
                        waitMinutes = Integer.valueOf(args[0]);  
                    }

                    String waitString;

                    waitString = (waitMinutes == 1) ? "minute." : waitMinutes + " minutes.";

                    // This queue must have previously been created.
                    service.getQueue("TSPQueue");

                    int numRead;

                    String s = null;

                    while (true)
                    {

                        ReceiveQueueMessageResult resultQM = service.receiveQueueMessage("TSPQueue");
                        message = resultQM.getValue();

                        if (null != message && null != message.getMessageId())
                        {

                            // Display the queue message.
                            byte[] b = new byte[200];

                            System.out.print("From queue: ");

                            s = null;
                            numRead = message.getBody().read(b);
                            while (-1 != numRead)
                            {
                                s = new String(b);
                                s = s.trim();
                                System.out.print(s);
                                numRead = message.getBody().read(b);
                            }
                            System.out.println();
                            if (s.compareTo("Complete") == 0)
                            {
                                // No more processing to occur.
                                date = new Date();
                                System.out.println("Finished at " + dateFormat.format(date) + ".");
                                break;
                            }
                        }
                        else
                        {
                            // The queue is empty.
                            System.out.println("Queue is empty. Sleeping for another " + waitString);
                            Thread.sleep(60000 * waitMinutes);
                        }
                    }

            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }

        }

    }

## <a name="how-to-run-the-java-applications"></a>Java 응용 프로그램을 실행하는 방법
계산 집약적인 응용 프로그램을 실행하여 먼저 큐를 만든 후에 순회 외판원 문제를 해결합니다. 그러면 현 시점에서의 최상의 경로가 서비스 버스 큐에 추가됩니다. 계산 집약적인 응용 프로그램이 실행 중인 동안 또는 실행된 이후에 클라이언트를 실행하여 서비스 버스 큐에서 가져온 결과를 표시합니다.

### <a name="to-run-the-compute-intensive-application"></a>계산 집약적인 응용 프로그램을 실행하려면
1. 가상 컴퓨터에 로그온합니다.
2. 응용 프로그램을 실행할 폴더(예: **c:\TSP**를 만듭니다.
3. **TSPSolver.jar**를 **c:\TSP**에 복사합니다.
4. 다음과 같은 콘텐츠가 포함된 **c:\TSP\cities.txt** 파일을 만듭니다.
   
        City_1, 1002.81, -1841.35
        City_2, -953.55, -229.6
        City_3, -1363.11, -1027.72
        City_4, -1884.47, -1616.16
        City_5, 1603.08, -1030.03
        City_6, -1555.58, 218.58
        City_7, 578.8, -12.87
        City_8, 1350.76, 77.79
        City_9, 293.36, -1820.01
        City_10, 1883.14, 1637.28
        City_11, -1271.41, -1670.5
        City_12, 1475.99, 225.35
        City_13, 1250.78, 379.98
        City_14, 1305.77, 569.75
        City_15, 230.77, 231.58
        City_16, -822.63, -544.68
        City_17, -817.54, -81.92
        City_18, 303.99, -1823.43
        City_19, 239.95, 1007.91
        City_20, -1302.92, 150.39
        City_21, -116.11, 1933.01
        City_22, 382.64, 835.09
        City_23, -580.28, 1040.04
        City_24, 205.55, -264.23
        City_25, -238.81, -576.48
        City_26, -1722.9, -909.65
        City_27, 445.22, 1427.28
        City_28, 513.17, 1828.72
        City_29, 1750.68, -1668.1
        City_30, 1705.09, -309.35
        City_31, -167.34, 1003.76
        City_32, -1162.85, -1674.33
        City_33, 1490.32, 821.04
        City_34, 1208.32, 1523.3
        City_35, 18.04, 1857.11
        City_36, 1852.46, 1647.75
        City_37, -167.44, -336.39
        City_38, 115.4, 0.2
        City_39, -66.96, 917.73
        City_40, 915.96, 474.1
        City_41, 140.03, 725.22
        City_42, -1582.68, 1608.88
        City_43, -567.51, 1253.83
        City_44, 1956.36, 830.92
        City_45, -233.38, 909.93
        City_46, -1750.45, 1940.76
        City_47, 405.81, 421.84
        City_48, 363.68, 768.21
        City_49, -120.3, -463.13
        City_50, 588.51, 679.33
5. 명령 프롬프트에서 디렉터리를 c:\TSP로 변경합니다.
6. JRE의 bin 폴더가 PATH 환경 변수에 포함되어 있는지 확인합니다.
7. TSP 해 찾기 순열을 실행하기 전에 서비스 버스 큐를 먼저 만들어야 합니다. 다음 명령을 실행하여 서비스 버스 큐를 만듭니다.
   
        java -jar TSPSolver.jar createqueue
8. 큐가 만들어졌으므로 이제 TSP 해 찾기 순열을 실행할 수 있습니다. 예를 들어 다음 명령을 실행하여 8개 도시에 대해 해 찾기를 실행합니다.
   
        java -jar TSPSolver.jar 8
   
   숫자를 지정하지 않으면 10개 도시에 대해 실행됩니다. 해 찾기에서 현재의 최단 경로를 찾으면 해당 경로가 큐에 추가됩니다.

> [!NOTE]
> 지정한 숫자가 클수록 해 찾기 실행 시간이 길어집니다. 예를 들어 14개 도시에 대해 실행하면 몇 분이 걸릴 수 있고, 15개 도시에 대해 실행하면 몇 시간이 소요될 수 있습니다. 도시를 16개 이상으로 늘리면 며칠 동안 더 나아가 수주, 수개월, 수년에 걸쳐 실행될 수도 있습니다. 이는 도시의 수가 증가함에 따라 해 찾기에 의해 평가되는 순열의 수가 급증하기 때문입니다.
> 
> 

### <a name="how-to-run-the-monitoring-client-application"></a>모니터링하는 클라이언트 응용 프로그램을 실행하는 방법
1. 클라이언트 응용 프로그램을 실행할 컴퓨터에 로그온합니다. 이 컴퓨터가 **TSPSolver** 응용 프로그램을 실행하는 컴퓨터와 같을 수도 있지만 반드시 같아야 하는 것은 아닙니다.
2. 응용 프로그램을 실행할 폴더(예: **c:\TSP**를 만듭니다.
3. **TSPClient.jar**를 **c:\TSP**에 복사합니다.
4. JRE의 bin 폴더가 PATH 환경 변수에 포함되어 있는지 확인합니다.
5. 명령 프롬프트에서 디렉터리를 c:\TSP로 변경합니다.
6. 다음 명령을 실행합니다.
   
        java -jar TSPClient.jar
   
    큐를 점검하는 시점 사이의 대기 시간(분)을 명령줄 인수로 전달하여 지정할 수도 있습니다. 큐 점검을 위한 기본 대기 기간은 3분이며, **TSPClient**로 명령줄 인수가 전달되지 않을 경우 이 값이 사용됩니다. 대기 간격으로 다른 값(예: 1분)을 사용하고 싶으면 다음 명령을 실행합니다.
   
        java -jar TSPClient.jar 1
   
    클라이언트는 "완료"라는 큐 메시지가 확인될 때까지 실행됩니다. 클라이언트를 실행하지 않은 상태로 해 찾기를 여러 번 실행하는 경우, 큐를 완전히 비우기 위해 클라이언트를 여러 번 실행해야 할 수도 있습니다. 또는 큐를 삭제한 후 큐를 다시 만들 수도 있습니다. 큐를 삭제하려면 다음 **TSPSolver**(**TSPClient** 아님) 명령을 실행합니다.
   
        java -jar TSPSolver.jar deletequeue
   
    모든 경로에 대한 조사를 마칠 때까지 해 찾기가 실행됩니다.

## <a name="how-to-stop-the-java-applications"></a>Java 응용 프로그램을 중지하는 방법
해 찾기 및 클라이언트 응용 프로그램을 정상적인 완료 이전에 종료하고 싶으면 **Ctrl+C** 를 누르면 됩니다.

[solver_output]:media/java-run-compute-intensive-task/WA_JavaTSPSolver.png
[client_output]:media/java-run-compute-intensive-task/WA_JavaTSPClient.png
[svc_bus_node]:media/java-run-compute-intensive-task/SvcBusQueues_02_SvcBusNode.jpg
[create_namespace]:media/java-run-compute-intensive-task/SvcBusQueues_03_CreateNewSvcNamespace.jpg
[avail_namespaces]:media/java-run-compute-intensive-task/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[namespace_list]:media/java-run-compute-intensive-task/SvcBusQueues_05_NamespaceList.jpg
[properties_pane]:media/java-run-compute-intensive-task/SvcBusQueues_06_PropertiesPane.jpg
[default_key]:media/java-run-compute-intensive-task/SvcBusQueues_07_DefaultKey.jpg
[add_ca_cert]: ../../../java-add-certificate-ca-store.md
