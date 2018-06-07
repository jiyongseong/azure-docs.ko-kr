---
title: Eclipse에서 Azure Service Fabric 응용 프로그램 디버그 | Microsoft Docs
description: 로컬 개발 클러스터의 Eclipse에서 개발하고 디버그하여 서비스의 안정성과 성능을 향상시킵니다.
services: service-fabric
documentationcenter: .net
author: suhuruli
manager: timlt
editor: ''
ms.assetid: cb888532-bcdb-4e47-95e4-bfbb1f644da4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: 0e9e816fa84816b1b5d12f066dc65aee7b4930f7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="debug-your-java-service-fabric-application-using-eclipse"></a>Eclipse를 사용하여 Java Service Fabric 응용 프로그램 디버그
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
> 

1. [서비스 패브릭 개발 환경 설정](service-fabric-get-started-linux.md)의 단계를 따라 로컬 개발 클러스터를 시작합니다.

2. 디버그하려는 서비스의 entryPoint.sh를 원격 디버그 매개 변수를 사용하여 java 프로세스를 시작하도록 업데이트합니다. 이 파일은 ``ApplicationName\ServiceNamePkg\Code\entrypoint.sh`` 위치에서 찾을 수 있습니다. 이 예제에서 포트 8001은 디버깅을 위해 설정되었습니다.

    ```sh
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=y -Djava.library.path=$LD_LIBRARY_PATH -jar myapp.jar
    ```
3. 인스턴스 수 또는 디버그하는 서비스의 복제본 수를 1로 설정하여 응용 프로그램 매니페스트를 업데이트합니다. 이 설정은 디버깅에 사용되는 포트에 대한 충돌을 방지합니다. 예를 들어, 상태 비저장 서비스의 경우 ``InstanceCount="1"``을 설정하고 상태 저장 서비스의 경우 `` TargetReplicaSetSize="1" MinReplicaSetSize="1"``과 같이 대상과 최소 복제본 세트 크기를 1로 설정합니다.

4. 응용 프로그램을 배포합니다.

5. Eclipse IDE에서 **실행 -> 원격 Java 응용 프로그램 및 입력 연결 속성**을 선택하고 속성을 다음과 같이 설정합니다.

   ```
   Host: ipaddress
   Port: 8001
   ```
6.  원하는 지점에 중단점을 설정하고 응용 프로그램을 디버깅합니다.

응용 프로그램이 충돌하는 경우 coredump를 사용하도록 설정하는 것이 좋습니다. 셸에서 ``ulimit -c``를 실행했을 때 0이 반환될 경우 coredump가 사용하지 않도록 설정된 것입니다. 무제한 coredump를 사용하도록 설정하려면 ``ulimit -c unlimited`` 명령을 실행합니다. 또한 ``ulimit -a`` 명령을 사용하여 상태를 확인할 수도 있습니다.  coredump 생성 경로를 업데이트하려면 ``echo '/tmp/core_%e.%p' | sudo tee /proc/sys/kernel/core_pattern``을 실행합니다. 

### <a name="next-steps"></a>다음 단계

* [Linux Azure 진단을 사용하여 로그 수집](service-fabric-diagnostics-how-to-setup-lad.md).
* [로컬로 서비스 모니터링 및 진단](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).
