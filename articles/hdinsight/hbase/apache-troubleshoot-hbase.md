---
title: Azure HDInsight를 사용한 HBase 문제 해결 | Microsoft Docs
description: HBase 및 Azure HDInsight 작업에 대한 일반적인 질문에 답합니다.
services: hdinsight
documentationcenter: ''
author: nitinver
manager: ashitg
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: nitinver
ms.openlocfilehash: d5d50121cd0375af1b57baadeb40efb237aaea11
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="troubleshoot-hbase-by-using-azure-hdinsight"></a>Azure HDInsight를 사용한 HBase 문제 해결

Apache Ambari에서 Apache HBase 페이로드를 사용할 때의 주요 문제 및 해결 방법을 알아봅니다.

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>할당되지 않은 여러 영역이 있는 hbck 명령 보고서를 실행하는 방법

`hbase hbck` 명령을 실행할 때 볼 수 있는 일반적인 오류 메시지는 “할당되지 않은 여러 영역이 있거나 영역 체인에 빈 영역이 있습니다.”입니다.

HBase Master UI에서 모든 영역 서버 중 부하가 분산되지 않은 영역 수를 확인할 수 있습니다. 그런 후 `hbase hbck` 명령을 실행하여 영역 체인의 빈 영역을 확인할 수 있습니다.

빈 영역은 오프라인 영역으로 인해 야기된 것일 수 있으므로 먼저 할당을 수정해야 합니다. 

할당되지 않은 영역을 정상 상태로 전환하려면 다음 단계를 수행합니다.

1. SSH를 사용하여 HDInsight HBase 클러스터에 로그인합니다.
2. ZooKeeper 셸에 연결하려면 `hbase zkcli` 명령을 실행합니다.
3. `rmr /hbase/regions-in-transition` 명령 또는 `rmr /hbase-unsecure/regions-in-transition` 명령을 실행합니다.
4. `hbase zkcli` 셸을 종료하려면 `exit` 명령을 사용합니다.
5. Apache Ambari UI를 열고 활성 HBase Master 서비스를 다시 시작합니다.
6. `hbase hbck` 명령을 다시 실행합니다(옵션 제외). 이 명령의 출력을 확인하고 모든 영역이 할당되었는지 확인합니다.


## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>영역 할당에 대해 hbck 명령을 사용하여 시간 제한 문제를 해결하는 방법

### <a name="issue"></a>문제

`hbck` 명령을 사용할 때 시간 초과 문제가 발생하는 잠재적 원인은 오랜 시간 동안 여러 영역이 "전환" 상태에 있다는 것일 수 있습니다. HBase Master UI에서 이러한 영역이 오프라인으로 나타날 수 있습니다. 전환하려는 영역 수가 많기 때문에 HBase Master에서 시간이 초과되어 해당 영역을 온라인 상태로 전환할 수 없습니다.

### <a name="resolution-steps"></a>해결 단계:

1. SSH를 사용하여 HDInsight HBase 클러스터에 로그인합니다.
2. ZooKeeper 셸에 연결하려면 `hbase zkcli` 명령을 실행합니다.
3. `rmr /hbase/regions-in-transition` 또는 `rmr /hbase-unsecure/regions-in-transition` 명령을 실행합니다.
4. `hbase zkcli` 셸을 종료하려면 `exit` 명령을 사용합니다.
5. Ambari UI에서 활성 HBase Master 서비스를 다시 시작합니다.
6. `hbase hbck -fixAssignments` 명령을 다시 실행합니다.

## <a name="how-do-i-force-disable-hdfs-safe-mode-in-a-cluster"></a>클러스터에서 HDFS 안전 모드 사용 안 함을 적용하는 방법

### <a name="issue"></a>문제

로컬 HDFS(Hadoop 분산 파일 시스템)가 HDInsight 클러스터에서 안전 모드 상태로 중단되었습니다.

### <a name="detailed-description"></a>자세한 설명

다음 HDFS 명령을 실행할 때 실패하여 이 오류가 발생할 수 있습니다.

```apache
hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
```

이 명령을 실행하려고 할 때 표시될 수 있는 오류는 다음과 같습니다.

```apache
hdiuser@hn0-spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.mkdirs(FSNamesystem.java:4010)
        at org.apache.hadoop.hdfs.server.namenode.NameNodeRpcServer.mkdirs(NameNodeRpcServer.java:1102)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolServerSideTranslatorPB.mkdirs(ClientNamenodeProtocolServerSideTranslatorPB.java:630)
        at org.apache.hadoop.hdfs.protocol.proto.ClientNamenodeProtocolProtos$ClientNamenodeProtocol$2.callBlockingMethod(ClientNamenodeProtocolProtos.java)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Server$ProtoBufRpcInvoker.call(ProtobufRpcEngine.java:640)
        at org.apache.hadoop.ipc.RPC$Server.call(RPC.java:982)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2313)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2309)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:422)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1724)
        at org.apache.hadoop.ipc.Server$Handler.run(Server.java:2307)
        at org.apache.hadoop.ipc.Client.getRpcResponse(Client.java:1552)
        at org.apache.hadoop.ipc.Client.call(Client.java:1496)
        at org.apache.hadoop.ipc.Client.call(Client.java:1396)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Invoker.invoke(ProtobufRpcEngine.java:233)
        at com.sun.proxy.$Proxy10.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolTranslatorPB.mkdirs(ClientNamenodeProtocolTranslatorPB.java:603)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invokeMethod(RetryInvocationHandler.java:278)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:194)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:176)
        at com.sun.proxy.$Proxy11.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.DFSClient.primitiveMkdir(DFSClient.java:3061)
        at org.apache.hadoop.hdfs.DFSClient.mkdirs(DFSClient.java:3031)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1162)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1158)
        at org.apache.hadoop.fs.FileSystemLinkResolver.resolve(FileSystemLinkResolver.java:81)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirsInternal(DistributedFileSystem.java:1158)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirs(DistributedFileSystem.java:1150)
        at org.apache.hadoop.fs.FileSystem.mkdirs(FileSystem.java:1898)
        at org.apache.hadoop.fs.shell.Mkdir.processNonexistentPath(Mkdir.java:76)
        at org.apache.hadoop.fs.shell.Command.processArgument(Command.java:273)
        at org.apache.hadoop.fs.shell.Command.processArguments(Command.java:255)
        at org.apache.hadoop.fs.shell.FsCommand.processRawArguments(FsCommand.java:119)
        at org.apache.hadoop.fs.shell.Command.run(Command.java:165)
        at org.apache.hadoop.fs.FsShell.run(FsShell.java:297)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:90)
        at org.apache.hadoop.fs.FsShell.main(FsShell.java:350)
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

### <a name="probable-cause"></a>가능한 원인:

HDInsight 클러스터 규모가 매우 적은 수의 노드로 축소되었습니다. 노드 수가 HDFS 복제 계수보다 낮거나 이 계수에 가깝습니다.

### <a name="resolution-steps"></a>해결 단계: 

1. 다음 명령을 실행하여 HDInsight 클러스터에서 HDFS 상태를 가져옵니다.

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
   ```

   ```apache
   hdiuser@hn0-spark2:~$ hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
   Safe mode is ON
   Configured Capacity: 3372381241344 (3.07 TB)
   Present Capacity: 3138625077248 (2.85 TB)
   DFS Remaining: 3102710317056 (2.82 TB)
   DFS Used: 35914760192 (33.45 GB)
   DFS Used%: 1.14%
   Under replicated blocks: 0
   Blocks with corrupt replicas: 0
   Missing blocks: 0
   Missing blocks (with replication factor 1): 0

   -------------------------------------------------
   Live datanodes (8):

   Name: 10.0.0.17:30010 (10.0.0.17)
   Hostname: 10.0.0.17
   Decommission Status : Normal
   Configured Capacity: 421547655168 (392.60 GB)
   DFS Used: 5288128512 (4.92 GB)
   Non DFS Used: 29087272960 (27.09 GB)
   DFS Remaining: 387172253696 (360.58 GB)
   DFS Used%: 1.25%
   DFS Remaining%: 91.85%
   Configured Cache Capacity: 0 (0 B)
   Cache Used: 0 (0 B)
   Cache Remaining: 0 (0 B)
   Cache Used%: 100.00%
   Cache Remaining%: 0.00%
   Xceivers: 2
   Last contact: Wed Apr 05 16:22:00 UTC 2017
   ...

   ```
2. 또한 다음 명령을 실행하여 HDInsight 클러스터에서 HDFS의 무결성을 확인할 수도 있습니다.

   ```apache
   hdiuser@hn0-spark2:~$ hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
   ```

   ```apache
   Connecting to namenode via http://hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net:30070/fsck?ugi=hdiuser&path=%2F
   FSCK started by hdiuser (auth:SIMPLE) from /10.0.0.22 for path / at Wed Apr 05 16:40:28 UTC 2017
   ....................................................................................................

   ....................................................................................................
   ..................Status: HEALTHY
   Total size:    9330539472 B
   Total dirs:    37
   Total files:   2618
   Total symlinks:                0 (Files currently being written: 2)
   Total blocks (validated):      2535 (avg. block size 3680686 B)
   Minimally replicated blocks:   2535 (100.0 %)
   Over-replicated blocks:        0 (0.0 %)
   Under-replicated blocks:       0 (0.0 %)
   Mis-replicated blocks:         0 (0.0 %)
   Default replication factor:    3
   Average block replication:     3.0
   Corrupt blocks:                0
   Missing replicas:              0 (0.0 %)
   Number of data-nodes:          8
   Number of racks:               1
   FSCK ended at Wed Apr 05 16:40:28 UTC 2017 in 187 milliseconds

   The filesystem under path '/' is HEALTHY
   ```

3. 복제된 블록에서 누락되었거나 손상된 부분이 없거나 해당 블록을 무시할 수 있다고 판단되면 다음 명령을 실행하여 이름 노드를 안전 모드에서 제거합니다.

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
   ```


## <a name="how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix"></a>Apache Phoenix를 사용한 JDBC 또는 SQLLine 연결 문제를 해결하는 방법

### <a name="resolution-steps"></a>해결 단계:

Phoenix와 연결하려면 활성 ZooKeeper 노드의 IP 주소를 제공해야 합니다. sqlline.py에서 연결하려는 ZooKeeper 서비스가 실행 중인지 확인합니다.
1. SSH를 사용하여 HDInsight 클러스터에 로그인합니다.
2. 다음 명령을 입력합니다.
                
   ```apache
           "/usr/hdp/current/phoenix-client/bin/sqlline.py <IP of machine where Active Zookeeper is running"
   ```

   > [!Note] 
   > Ambari UI에서 활성 ZooKeeper 노드의 IP 주소를 가져올 수 있습니다. **HBase** > **빠른 링크** > **ZK\*(활성)** > **ZooKeeper 정보**로 이동합니다. 

3. sqlline.py가 Phoenix에 연결되어 있고 시간이 초과되지 않으면 다음 명령을 실행하여 Phoenix의 가용성과 상태에 대한 유효성을 검사합니다.

   ```apache
           !tables
           !quit
   ```      
4. 이 명령이 작동하는 경우 문제가 발생하지 않습니다. 사용자가 제공한 IP 주소가 잘못되었을 수 있습니다. 그러나 이 명령이 오랫동안 일시 중지되었다가 다음 오류를 표시하는 경우 5단계를 계속 진행합니다.

   ```apache
           Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings. 
   ```

5. 헤드 노드(hn0)에서 다음 명령을 실행하여 Phoenix SYSTEM.CATALOG 테이블의 상태를 진단합니다.

   ```apache
            hbase shell
                
           count 'SYSTEM.CATALOG'
   ```

   명령에서 다음과 비슷한 오류 메시지를 반환합니다. 

   ```apache
           ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189) 
   ```
6. Ambari UI에서 다음 단계를 완료하여 모든 ZooKeeper 노드에서 HMaster 서비스를 다시 시작합니다.

    1. HBase의 **요약** 섹션에서 **HBase** > **Active HBase Master**로 이동합니다. 
    2. **구성 요소** 섹션에서 HBase Master 서비스를 다시 시작합니다.
    3. 나머지 모든 **Standby HBase Master** 서비스에 대해 이러한 단계를 반복합니다. 

HBase Master 서비스가 안정화되고 복구 프로세스를 완료하는 데 최대 5분이 걸릴 수 있습니다. 몇 분 후에 sqlline.py 명령을 반복하여 SYSTEM.CATALOG 테이블이 작동되고 쿼리될 수 있는지 확인합니다. 

SYSTEM.CATALOG 테이블이 정상으로 전환되면 Phoenix에 대한 연결 문제가 자동으로 해결됩니다.


## <a name="what-causes-a-master-server-to-fail-to-start"></a>마스터 서버가 시작되지 않는 원인

### <a name="error"></a>오류 

원자성 이름 바꾸기 실패가 발생했습니다.

### <a name="detailed-description"></a>자세한 설명

시작 프로세스 동안 HMaster는 많은 초기화 단계를 완료합니다. 여기에는 스크래치(.tmp) 폴더에서 데이터 폴더로 데이터를 이동하는 것이 포함됩니다. 또한 HMaster는 WAL(미리 쓰기 로그) 폴더를 찾아 응답하지 않는 영역 서버가 있는지 등을 확인합니다. 

시작 동안 HMaster는 이러한 폴더에 대해 기본 `list` 명령을 수행합니다. 언제든지 이러한 폴더에 예기치 않은 파일이 있을 경우 예외가 발생하고 시작되지 않습니다.  

### <a name="probable-cause"></a>가능한 원인:

영역 서버 로그에서 파일 생성 타임라인을 확인한 다음, 파일이 만들어진 시간에 프로세스 충돌이 있었는지 확인합니다. (HBase 지원 서비스에 문의하여 이 작업에 대한 도움을 요청하세요.) 이렇게 하면 이 버그에 연결하지 않도록 방지하고 정상적인 프로세스 종료를 보장하는 보다 강력한 메커니즘을 제공할 수 있습니다.

### <a name="resolution-steps"></a>해결 단계:

호출 스택을 확인하고 문제를 일으킬 수 있는 폴더(예: WAL 폴더 또는 .tmp 폴더)를 확인합니다. 그런 다음 클라우드 탐색기 또는 HDFS 명령을 사용하여 문제가 있는 파일을 찾습니다. 일반적으로 \*-renamePending.json 파일입니다. (\*-renamePending.json 파일은 WASB 드라이버에서 원자성 이름 바꾸기 작업을 구현하는 데 사용되는 저널 파일입니다. 이 구현의 버그 때문에 이러한 파일은 프로세스 충돌 등이 발생한 후에도 남아 있을 수 있습니다.) 클라우드 탐색기 또는 HDFS 명령을 사용하여 이 파일을 강제로 삭제합니다. 

경우에 따라 *$$$.$$$* 같은 임시 파일이 있을 수도 있습니다. 이 파일을 보기 위해 HDFS `ls` 명령을 사용해야 합니다. 클라우드 탐색기에서는 이 파일을 볼 수 없습니다. 이 파일을 삭제하려면 HDFS 명령 `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`을 사용합니다.  

이러한 명령을 실행한 후 HMaster는 즉시 시작됩니다. 

### <a name="error"></a>오류

xxx 영역에 대한 서버 주소가 *hbase: meta*에 나열되지 않습니다.

### <a name="detailed-description"></a>자세한 설명

Linux 클러스터에 *hbase:meta* 테이블이 온라인 상태가 아님을 나타내는 메시지가 표시될 수 있습니다. `hbck`를 실행하면 "모든 영역에서 replicaId 0 hbase: meta 테이블을 찾을 없습니다."라고 보고할 수 있습니다. HBase를 다시 시작한 후에 HMaster를 초기화하지 못하여 이 문제가 발생할 수 있습니다. HMaster 로그에서 "hbase: backup \<영역 이름\>에 대한 서버 주소가 나열되지 않습니다."라는 메시지가 표시될 수 있습니다.  

### <a name="resolution-steps"></a>해결 단계:

1. HBase 셸에서 다음 명령을 입력합니다(해당되는 경우 실제 값 변경).  

   ```apache
   > scan 'hbase:meta'  
   ```

   ```apache
   > delete 'hbase:meta','hbase:backup <region name>','<column name>'  
   ```

2. *hbase: namespace* 항목을 삭제합니다. 이 항목은 *hbase:namespace* 테이블이 검색될 때 보고되는 것과 같은 오류일 수 있습니다.

3. HMaster를 실행 상태로 만들려면 Ambari UI에서 활성 HBase 서비스를 다시 시작합니다.  

4. HBase 셸에서 모든 오프라인 테이블을 가져오려면 다음 명령을 실행합니다.

   ```apache 
   hbase hbck -ignorePreCheckPermission -fixAssignments 
   ```

### <a name="additional-reading"></a>추가 참조 자료

[HBase 테이블을 처리할 수 없음](http://stackoverflow.com/questions/4794092/unable-to-access-hbase-table)


### <a name="error"></a>오류

"java.io.IOException: 네임스페이스 테이블이 할당될 때까지 기다리는 시간(300000ms)을 초과했습니다."와 같은 심각한 예외로 인해 HMaster에서 시간을 초과했습니다.

### <a name="detailed-description"></a>자세한 설명

HMaster 서비스를 다시 시작할 때 플러시되지 않은 많은 테이블 및 영역이 있는 경우 이 문제가 발생할 수 있습니다. 다시 시작이 실패할 수 있으며 위의 오류 메시지가 표시됩니다.  

### <a name="probable-cause"></a>가능한 원인:

HMaster 서비스의 알려진 문제입니다. 일반 클러스터 시작 작업이 오래 걸릴 수 있습니다. 네임스페이스 테이블이 아직 할당되지 않았으므로 HMaster가 종료됩니다. 이 오류는 플러시되지 않은 대량의 데이터가 있고 5분의 제한 시간으로는 부족한 시나리오에서만 발생합니다.
  
### <a name="resolution-steps"></a>해결 단계:

1. Ambari UI에서 **HBase** > **Configs**로 이동합니다. 사용자 지정 hbase-site.xml 파일에서 다음 설정을 추가합니다. 

   ```apache
   Key: hbase.master.namespace.init.timeout Value: 2400000  
   ```

2. 필요한 서비스(HMaster 및 가능한 경우 다른 HBase 서비스)를 다시 시작합니다.  


## <a name="what-causes-a-restart-failure-on-a-region-server"></a>지역 서버에서 다시 시작이 실패하는 원인

### <a name="issue"></a>문제

영역 서버의 다시 시작 실패는 다음의 모범 사례를 통해 방지할 수 있습니다. HBase 영역 서버를 다시 시작하려는 경우 과도한 워크로드 활동을 일시 중지하는 것이 좋습니다. 종료가 진행 중일 때 응용 프로그램이 영역 서버와 계속 연결되면 영역 서버 재시작 작업이 몇 분 정도 더 느려집니다. 또한 먼저 모든 테이블을 플러시하는 것이 좋습니다. 테이블을 플러시하는 방법에 대한 참조는 [HDInsight HBase: 테이블에 플러시하여 HBase 클러스터 다시 시작 시간을 개선하는 방법](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/)을 참조하세요.

Ambari UI에서 HBase 영역 서버에 대한 다시 시작 작업을 시작하면 영역 서버가 다운되지만 바로 다시 시작되지 않는 것을 보게 됩니다. 

백그라운드에서 진행되는 작업은 다음과 같습니다. 

1. Ambari 에이전트가 영역 서버에 중지 요청을 보냅니다.
2. Ambari 에이전트에서 영역 서버가 정상적으로 종료될 때까지 30초 동안 대기합니다. 
3. 응용 프로그램이 영역 서버에 계속 연결될 경우 서버는 즉시 종료되지 않습니다. 종료되기 전에 30초 제한 시간에 만료됩니다. 
4. 30초 후 Ambari 에이전트는 영역 서버에 강제 종료(`kill -9`) 명령을 전송합니다. 이 사항은 ambari-agent 로그(해당 작업자 노드의 /var/log/ 디렉터리)에서 확인할 수 있습니다.

   ```apache
           2017-03-21 13:22:09,171 - Execute['/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/current/hbase-regionserver/conf stop regionserver'] {'only_if': 'ambari-sudo.sh  -H -E t
           est -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1', 'on_timeout': '! ( ambari-sudo.sh  -H -E test -
           f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H 
           -E cat /var/run/hbase/hbase-hbase-regionserver.pid`', 'timeout': 30, 'user': 'hbase'}
           2017-03-21 13:22:40,268 - Executing '! ( ambari-sudo.sh  -H -E test -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >
           /dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid`'. Reason: Execution of 'ambari-sudo.sh su hbase -l -s /bin/bash -c 'export  
           PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/var/lib/ambari-agent ; /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/curre
           nt/hbase-regionserver/conf stop regionserver was killed due timeout after 30 seconds
           2017-03-21 13:22:40,285 - File['/var/run/hbase/hbase-hbase-regionserver.pid'] {'action': ['delete']}
           2017-03-21 13:22:40,285 - Deleting File['/var/run/hbase/hbase-hbase-regionserver.pid']
   ```
이러한 갑작스러운 종료로 인해 영역 서버 프로세스가 중지되더라도 프로세스와 연결된 포트가 해제되지 않을 수 있습니다. 이러한 상황으로 인해 다음 로그와 같이 영역 서버가 시작되는 동안 AddressBindException이 발생할 수 있습니다. 영역 서버가 시작되지 못할 경우 작업자 노드의 /var/log/hbase 디렉터리에 있는 region-server.log에서 이를 확인할 수 있습니다. 

   ```apache

   2017-03-21 13:25:47,061 ERROR [main] regionserver.HRegionServerCommandLine: Region server exiting
   java.lang.RuntimeException: Failed construction of Regionserver: class org.apache.hadoop.hbase.regionserver.HRegionServer
   at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2636)
   at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.start(HRegionServerCommandLine.java:64)
   at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.run(HRegionServerCommandLine.java:87)
   at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
   at org.apache.hadoop.hbase.util.ServerCommandLine.doMain(ServerCommandLine.java:126)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.main(HRegionServer.java:2651)
        
   Caused by: java.lang.reflect.InvocationTargetException
   at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
   at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:57)
   at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
   at java.lang.reflect.Constructor.newInstance(Constructor.java:526)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2634)
   ... 5 more
        
   Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
   at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2497)
   at org.apache.hadoop.hbase.ipc.RpcServer$Listener.<init>(RpcServer.java:580)
   at org.apache.hadoop.hbase.ipc.RpcServer.<init>(RpcServer.java:1982)
   at org.apache.hadoop.hbase.regionserver.RSRpcServices.<init>(RSRpcServices.java:863)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.createRpcServices(HRegionServer.java:632)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.<init>(HRegionServer.java:532)
   ... 10 more
        
   Caused by: java.net.BindException: Address already in use
   at sun.nio.ch.Net.bind0(Native Method)
   at sun.nio.ch.Net.bind(Net.java:463)
   at sun.nio.ch.Net.bind(Net.java:455)
   at sun.nio.ch.ServerSocketChannelImpl.bind(ServerSocketChannelImpl.java:223)
   at sun.nio.ch.ServerSocketAdaptor.bind(ServerSocketAdaptor.java:74)
   at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2495)
   ... 15 more
   ```

### <a name="resolution-steps"></a>해결 단계:

1. HBase 영역 서버의 부하를 줄인 후에 다시 시작을 시작합니다. 
2. 또는 다음 명령을 사용하여 작업자 노드의 영역 서버를 수동으로 다시 시작합니다(1단계가 도움이 되지 않을 경우).

   ```apache
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"   
   ```

### <a name="see-also"></a>참고 항목
[Azure HDInsight를 사용하여 문제 해결](../../hdinsight/hdinsight-troubleshoot-guide.md)
