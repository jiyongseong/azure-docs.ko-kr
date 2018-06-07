---
title: Apache Hive 및 Pig를 포함하는 Python UDF - Azure HDInsight | Microsoft Docs
description: HDInsight의 Hive 및 Pig에서 Python UDF(사용자 정의 함수)를 사용하는 방법, Azure에서의 Hadoop 기술 스택에 대해 알아봅니다.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c44d6606-28cd-429b-b535-235e8f34a664
ms.service: hdinsight
ms.devlang: python
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: c508ffcdd5cccaad5c7b0717d33e2ea1449ab522
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="use-python-user-defined-functions-udf-with-hive-and-pig-in-hdinsight"></a>HDInsight의 Hive 및 Pig에서 Python UDF(사용자 정의 함수) 사용

Azure HDInsight의 Hadoop에서 Apache Hive 및 Pig와 함께 Python UDF(사용자 정의 함수)를 사용하는 방법에 대해 알아보세요.

## <a name="python"></a>HDInsight의 Python

Python2.7은 기본적으로 HDInsight 3.0 이상에 설치됩니다. 스트림 처리를 위해 이 버전의 Python에서 Apache Hive를 사용할 수 있습니다. 스트림 처리는 STDOUT 및 STDIN을 사용하여 Hive와 UDF 간에 데이터를 전달합니다.

HDInsight에는 Java로 작성된 Python 구현인 Jython도 포함되어 있습니다. Jython은 Java Virtual Machine에서 직접 실행되며 스트리밍을 사용하지 않습니다. Jython는 Pig와 함께 Python을 사용할 때 권장되는 Python 인터프리터입니다.

> [!WARNING]
> 이 문서의 단계에서는 다음과 같이 가정합니다. 
>
> * 로컬 개발 환경에서 Python 스크립트를 만듭니다.
> * 로컬 Bash 세션 또는 제공된 PowerShell 스크립트에서 `scp` 명령을 사용하여 스크립트를 HDInsight에 업로드합니다.
>
> [Azure Cloud Shell(bash)](https://docs.microsoft.com/azure/cloud-shell/overview) 미리 보기를 사용하여 HDInsight와 작동하려면 다음과 같이 해야 합니다.
>
> * Cloud Shell 환경 내에서 스크립트를 만듭니다.
> * `scp`를 사용하여 Cloud Shell에서 HDInsight로 파일을 업로드합니다.
> * Cloud Shell에서 `ssh`를 사용하여 HDInsight에 연결하고 예제를 실행합니다.

## <a name="hivepython"></a>Hive UDF

Python은 HiveQL `TRANSFORM` 문을 통해 Hive의 UDF로 사용할 수 있습니다. 예를 들어 다음 HiveQL은 클러스터의 기본 Azure Storage 계정에 저장된 `hiveudf.py` 파일을 호출합니다.

**Linux 기반 HDInsight**

```hiveql
add file wasb:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

**Windows 기반 HDInsight**

```hiveql
add file wasb:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'D:\Python27\python.exe hiveudf.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

> [!NOTE]
> Windows 기반 HDInsight 클러스터에서는 `USING` 절에서 python.exe의 전체 경로를 지정해야 합니다.

다음은 이 예제에서 수행하는 작업입니다.

1. 파일의 시작 부분에 있는 `add file` 문이 분산 캐시에 `hiveudf.py` 파일을 추가하므로, 클러스터의 모든 노드에서 액세스할 수 있습니다.
2. `SELECT TRANSFORM ... USING` 문은 `hivesampletable`에서 데이터를 선택합니다. 또한 clientid, devicemake 및 devicemodel 값을 `hiveudf.py` 스크립트에 전달합니다.
3. `AS` 절은 `hiveudf.py`에서 반환된 필드를 설명합니다.

<a name="streamingpy"></a>

### <a name="create-the-hiveudfpy-file"></a>hiveudf.py 파일 만들기


개발 환경에서 `hiveudf.py`라는 텍스트 파일을 만듭니다. 이 파일의 내용으로 다음 코드를 사용합니다.

```python
#!/usr/bin/env python
import sys
import string
import hashlib

while True:
    line = sys.stdin.readline()
    if not line:
        break

    line = string.strip(line, "\n ")
    clientid, devicemake, devicemodel = string.split(line, "\t")
    phone_label = devicemake + ' ' + devicemodel
    print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])
```

이 스크립트는 다음 작업을 수행합니다.

1. STDIN에서 데이터 줄을 읽습니다.
2. `string.strip(line, "\n ")`를 사용하여 후행 줄 바꿈 문자를 제거합니다.
3. 스트림 처리를 할 때 모든 값과 각 값 사이의 탭 문자가 한 줄에 포함됩니다. 따라서 `string.split(line, "\t")` 를 사용하여 각 탭의 입력을 분할하여 필드만 반환할 수 있습니다.
4. 처리가 완료되면 출력을 단일 행(각 필드 사이에 탭 포함)으로 STDOUT에 작성해야 합니다. 예: `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`
5. `while` 루프는 `line`이 읽히지 않을 때까지 반복됩니다.

스크립트 출력은 `devicemake` 및 `devicemodel`의 입력 값과 연결된 값의 해시를 연결합니다.

HDInsight 클러스터에서 이 예제를 실행하는 방법에 대해서는 [예제 실행](#running) 을 참조하세요.

## <a name="pigpython"></a>Pig UDF

`GENERATE` 문을 통해 Python 스크립트를 Pig의 UDF로 사용할 수 있습니다. Jython 또는 C Python을 사용하여 스크립트를 실행할 수 있습니다.

* Jython은 JVM에서 실행되고, 기본적으로 Pig에서 호출될 수 있습니다.
* C Python은 외부 프로세스이므로 JVM에서 Pig의 데이터는 Python 프로세스에서 실행 중인 스크립트로 전송됩니다. Python 스크립트의 출력이 Pig로 다시 전송됩니다.

Python 인터프리터를 지정하려면 Python 스크립트를 참조할 때 `register`를 사용합니다. 다음 예제에서는 Pig as `myfuncs`를 사용하여 스크립트를 등록합니다.

* **Jython 사용**: `register '/path/to/pigudf.py' using jython as myfuncs;`
* **C Python 사용**: `register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]
> Jython을 사용할 경우 pig_jython 파일에 대한 경로는 로컬 경로 또는 WASB:// 경로입니다. 그러나 C Python을 사용할 경우에는 Pig 작업을 제출하는 데 사용하는 노드의 로컬 파일 시스템에 있는 파일을 참조해야 합니다.

등록하기 전이라면 두 경우에 대한 이 예제의 Pig Latin은 다음과 같이 모두 동일합니다.

```pig
LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

다음은 이 예제에서 수행하는 작업입니다.

1. 첫 번째 줄은 `LOGS`에 `sample.log` 샘플 데이터 파일을 로드합니다. 각 레코드를 `chararray`로도 정의합니다.
2. 다음 줄에서는 모든 Null 값을 필터링하여 `LOG`에 작업 결과를 저장합니다.
3. 그런 다음 `LOG`의 레코드에 대해 반복하고 `GENERATE`를 사용하여 `create_structure` 메서드를 호출합니다. 이 메서드는 `myfuncs`로 로드된 Python/Jython 스크립트에 포함되어 있습니다. `LINE`은 현재 레코드를 함수에 전달하는 데 사용됩니다.
4. 마지막으로 출력은 `DUMP` 명령을 사용하여 STDOUT로 덤프됩니다. 이 명령은 작업이 완료된 후 결과를 표시합니다.

### <a name="create-the-pigudfpy-file"></a>pigudf.py 파일 만들기

개발 환경에서 `pigudf.py`라는 텍스트 파일을 만듭니다. 이 파일의 내용으로 다음 코드를 사용합니다.

<a name="streamingpy"></a>

```python
# Uncomment the following if using C Python
#from pig_util import outputSchema

@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
def create_structure(input):
    if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
    date, time, classname, level, detail = input.split(' ', 4)
    return date, time, classname, level, detail
```

Pig Latin 예제에서는 입력에 대한 일관된 스키마가 없으므로 `LINE` 입력이 chararray로 정의되었습니다. Python 스크립트는 데이터를 출력에 대한 일관된 스키마로 변환합니다.

1. `@outputSchema` 문은 Pig에 반환되는 데이터의 형식을 정의합니다. 이 경우 Pig 데이터 형식은 **데이터 모음**입니다. 모음에는 모두 chararray(문자열)인 다음과 같은 필드가 포함됩니다.

   * date - 로그 항목이 생성된 날짜
   * time - 로그 항목이 생성된 시간
   * classname - 항목이 생성된 클래스 이름
   * level - 로그 수준
   * detail - 로그 항목에 대한 세부 정보

2. 그런 다음 `def create_structure(input)`가 Pig에서 줄 항목을 전달할 함수를 정의합니다.

3. 예제 데이터인 `sample.log`는 대개 date, time, classname, level 및 detail 스키마를 준수합니다. 그러나 `*java.lang.Exception*`으로 시작하는 몇 개의 줄이 포함됩니다. 이러한 줄은 스키마와 일치하도록 수정해야 합니다. `if` 문이 이러한 줄을 확인한 후 `*java.lang.Exception*` 문자열을 끝으로 이동하고, 원하는 출력 스키마에 따라 인라인 데이터를 가져오는 입력 데이터를 전달합니다.

4. 그런 다음 `split` 명령을 사용하여 첫 4개 공백 문자에서 데이터를 분할합니다. 출력은 `date`, `time`, `classname`, `level` 및 `detail`에 할당됩니다.

5. 마지막으로 값은 Pig로 반환됩니다.

데이터가 Pig로 반환되면 `@outputSchema` 문에 정의된 것과 일관된 스키마를 포함합니다.

## <a name="running"></a>예제 업로드 및 실행

> [!IMPORTANT]
> **SSH** 단계는 Linux 기반 HDInsight 클러스터에만 작동합니다. **PowerShell** 단계는 Linux 또는 Windows 기반 HDInsight 클러스터에서 작동하지만 Windows 클라이언트가 필요합니다.

### <a name="ssh"></a>SSH

SSH를 사용하는 방법에 대한 자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

1. `scp` 를 사용하여 파일을 HDInsight 클러스터에 복사합니다. 예를 들어 다음 명령은 **mycluster**라는 클러스터에 파일을 복사합니다.

    ```bash
    scp hiveudf.py pigudf.py myuser@mycluster-ssh.azurehdinsight.net:
    ```

2. SSH를 사용하여 클러스터에 연결합니다.

    ```bash
    ssh myuser@mycluster-ssh.azurehdinsight.net
    ```

    자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md) 문서를 참조하세요.

3. SSH 세션에서 이전에 업로드된 python 파일을 클러스터의 WASB 저장소에 추가합니다.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    hdfs dfs -put pigudf.py /pigudf.py
    ```

파일을 업로드한 후 다음 단계를 사용하여 Hive 및 Pig 작업을 실행합니다.

#### <a name="use-the-hive-udf"></a>Hive UDF 사용

1. Hive에 연결하려면 다음 명령을 사용합니다.

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    이 명령은 Beeline 클라이언트를 시작합니다.

2. `0: jdbc:hive2://headnodehost:10001/>` 프롬프트에서 다음 쿼리를 입력합니다.

   ```hive
   add file wasb:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. 마지막 줄을 입력하면 작업이 시작됩니다. 작업이 완료되면 다음 예제와 유사한 출력을 반환합니다.

        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

4. Beeline을 끝내려면 다음 명령을 사용합니다.

    ```hive
    !q
    ```

#### <a name="use-the-pig-udf"></a>Pig UDF 사용

1. pig에 연결하려면 다음 명령을 사용합니다.

    ```bash
    pig
    ```

2. `grunt>` 프롬프트에 다음 문을 입력합니다.

   ```pig
   Register wasb:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. 다음 줄을 입력하면 작업이 시작됩니다. 작업이 완료되면 다음 데이터와 유사한 출력이 반환됩니다.

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. `quit`를 사용하여 Grunt 셸을 종료한 후 다음을 사용하여 로컬 파일 시스템에 있는 pigudf.py 파일을 편집합니다.

    ```bash
    nano pigudf.py
    ```

5. 편집기에서 다음 줄의 시작 부분에 있는 `#` 문자를 제거하여 해당 줄의 주석 처리를 제거합니다.

    ```bash
    #from pig_util import outputSchema
    ```

    이 줄은 Jython 대신 C Python과 함께 작동하도록 Python 스크립트를 수정합니다. 변경했으면 **Ctrl+X**를 사용하여 편집기를 종료합니다. **Y**를 선택한 다음 **Enter** 키를 눌러 변경 내용을 저장합니다.

6. `pig` 명령을 사용하여 셸을 다시 시작합니다. `grunt>` 프롬프트에서 다음 문을 사용하여 Jython 인터프리터를 사용하는 Python 스크립트를 실행합니다.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    이 작업이 완료되면 이전에 Jython을 사용하여 스크립트를 실행한 때와 같은 출력이 표시됩니다.

### <a name="powershell-upload-the-files"></a>PowerShell: 파일 업로드

PowerShell을 사용하여 HDInsight 서버에 파일을 업로드할 수 있습니다. 다음 스크립트를 사용하여 Python 파일을 업로드합니다.

> [!IMPORTANT] 
> 이 섹션의 단계에서는 Azure PowerShell을 사용합니다. Azure PowerShell 사용에 관한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)을 참조하세요.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=5-41)]

> [!IMPORTANT]
> `C:\path\to` 값을 해당 개발 환경의 파일 경로로 변경하세요.

이 스크립트는 HDInsight 클러스터의 정보를 검색한 후 계정 및 기본 저장소 계정의 키를 추출하고 컨테이너의 루트에 파일을 업로드합니다.

> [!NOTE]
> 파일 업로드에 대한 자세한 내용은 [HDInsight에서 Hadoop 작업용 데이터 업로드](../hdinsight-upload-data.md) 문서를 참조하세요.

#### <a name="powershell-use-the-hive-udf"></a>PowerShell: Hive UDF 사용

PowerShell을 사용하여 Hive 쿼리를 원격으로 실행할 수도 있습니다. 다음 PowerShell 스크립트를 사용하여 **hiveudf.py** 스크립트를 사용하는 Hive 쿼리를 실행합니다.

> [!IMPORTANT]
> 실행하기 전에 스크립트에서 HDInsight 클러스터의 HTTPs/관리자 계정 정보를 입력하라는 메시지를 표시합니다.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=45-94)]

**Hive** 작업의 출력은 다음 예제와 유사하게 표시됩니다.

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

#### <a name="pig-jython"></a>Pig (Jython)

PowerShell을 사용하여 Pig Latin 작업을 실행할 수도 있습니다. **pigudf.py** 스크립트를 사용하는 Pig Latin 작업을 실행하려면 다음 PowerShell 스크립트를 사용합니다.

> [!NOTE]
> PowerShell을 사용하는 작업을 원격으로 제출하는 경우 C Python을 인터프리터로사용할 수 없습니다.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=98-144)]

**Pig** 작업의 출력은 다음 데이터와 유사하게 표시됩니다.

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a>문제 해결

### <a name="errors-when-running-jobs"></a>작업 실행 중 오류 발생

하이브 작업 실행 중 다음 텍스트와 유사한 오류가 발생할 수 있습니다.

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

이 문제는 Python 파일의 줄 끝 때문에 발생할 수 있습니다. 많은 Windows 편집기에서는 기본적으로 CRLF를 줄 끝으로 사용하지만 Linux 응용 프로그램에서는 보통 LF를 사용합니다.

파일을 HDInsight로 업로드하기 전에 CR 문자를 제거하기 위해 다음 PowerShell 문을 사용할 수 있습니다.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>PowerShell 스크립트

이 예제를 실행하는 데 사용된 두 가지 예제 PowerShell 스크립트는 작업의 오류 출력을 표시하는 주석 처리된 줄을 포함합니다. 작업의 필요한 출력이 표시되지 않으면 다음 줄의 주석 처리를 제거하고 오류 정보가 문제를 나타내는지 확인합니다.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

오류 정보(STDERR) 및 작업의 결과(STDOUT)도 HDInsight 저장소에 로깅됩니다.

| 이 작업의 경우 | Blob 컨테이너에서 이러한 파일을 찾습니다. |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next"></a>다음 단계

기본적으로 제공되지 않는 Python 모듈을 로드해야 하는 경우 [Azure HDInsight에 모듈을 배포하는 방법](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx)(영문)을 참조하세요.

Pig 및 Hive를 사용하고 MapReduce 사용에 대해 배우는 다른 방법은 다음 문서를 참조하세요.

* [HDInsight에서 하이브 사용](hdinsight-use-hive.md)
* [HDInsight에서 Pig 사용](hdinsight-use-pig.md)
* [HDInsight와 함께 MapReduce 사용](hdinsight-use-mapreduce.md)
