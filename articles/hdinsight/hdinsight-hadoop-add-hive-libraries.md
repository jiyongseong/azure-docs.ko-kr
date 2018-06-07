---
title: HDInsight 클러스터를 만드는 동안 Hive 라이브러리 추가 - Azure | Microsoft Docs
description: 클러스터를 만드는 동안 HDInsight 클러스터에 Hive 라이브러리(jar 파일)를 추가하는 방법을 알아봅니다.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 2fd74b8d-c006-45c6-a9e2-72ff5d2d978a
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 71e2859085dc4a9f4fa327d88faff4fecf5108ef
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="add-custom-hive-libraries-when-creating-your-hdinsight-cluster"></a>HDInsight 클러스터를 만들 때 사용자 지정 Hive 라이브러리에 추가

HDInsight에 Hive 라이브러리를 미리 로드하는 방법을 알아봅니다. 이 문서에는 클러스터를 만드는 동안 스크립트 작업을 사용하여 라이브러리를 미리 로드하는 방법에 대한 정보가 포함되어 있습니다. 이 문서의 단계를 사용하여 추가된 라이브러리는 Hive에서 전역적으로 사용 가능합니다. 로드하는 데 [ADD JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli)을 사용하지 않아도 됩니다.

## <a name="how-it-works"></a>작동 방법

클러스터를 만들 때 스크립트 작업을 사용하여 생성되는 클러스터 노드를 수정할 수 있습니다. 이 문서의 스크립트는 라이브러리의 위치인 단일 매개 변수를 허용합니다. 이 위치는 Azure Storage 계정에 있어야 하고 라이브러리는 jar 파일로 저장되어야 합니다.

클러스터를 만들 때 스크립트는 파일을 열거하고 헤드 및 작업자 노드의 `/usr/lib/customhivelibs/` 디렉터리에 복사한 다음 `core-site.xml` 파일의 `hive.aux.jars.path` 속성에 추가합니다. 또한 Linux 기반 클러스터에서 파일의 위치로 `hive-env.sh` 파일을 업데이트합니다.

> [!NOTE]
> 이 문서의 스크립트 작업을 사용하여 다음과 같은 시나리오에서 라이브러리를 사용할 수 있도록 합니다.
>
> * **Linux 기반 HDInsight** - Hive 클라이언트, **WebHCat** 및 **HiveServer2**를 사용하는 경우.
> * **Windows 기반 HDInsight** - Hive 클라이언트 및 **WebHCat**을 사용하는 경우.

## <a name="the-script"></a>스크립트

**스크립트 위치**

**Linux 기반 클러스터**: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

**Windows 기반 클러스터**: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

> [!IMPORTANT]
> Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

**요구 사항**

* 스크립트는 **헤드 노드** 및 **작업자 노드** 모두에 적용되어야 합니다.

* 설치하려는 jar을 **단일 컨테이너**의 Azure Blob Storage에 저장해야 합니다.

* 만드는 동안 jar 파일의 라이브러리를 포함하는 저장소 계정을 HDInsight 클러스터에 **연결해야** 합니다. 기본 저장소 계정이거나 __선택적 구성__을 통해 추가된 계정이어야 합니다.

* 컨테이너에 대한 WASB 경로를 스크립트 작업에 대한 매개 변수로 지정해야 합니다. 예를 들어 jar이 **mystorage** 저장소 계정의 **libs** 컨테이너에 저장되는 경우 매개 변수는 **wasb://libs@mystorage.blob.core.windows.net/** 입니다.

  > [!NOTE]
  > 이 문서에서는 사용자가 이미 저장소 계정과 BLOB 컨테이너를 만들고 거기에 파일을 업로드했다고 가정합니다.
  >
  > 저장소 계정을 만들지 않은 경우 [Azure Portal](https://portal.azure.com)을 통해 수행할 수 있습니다. [Azure Storage 탐색기](http://storageexplorer.com/)와 같은 유틸리티를 사용하여 계정에 컨테이너를 만들고 파일을 업로드할 수 있습니다.

## <a name="create-a-cluster-using-the-script"></a>스크립트를 사용하여 클러스터 만들기

> [!NOTE]
> 다음 단계는 Linux 기반 HDInsight 클러스터를 만듭니다. Windows 기반 클러스터를 만들려면 클러스터를 만들 때 **Windows**를 클러스터 OS로 선택하고 bash 스크립트 대신 Windows(PowerShell) 스크립트를 사용합니다.
>
> 또한 이 스크립트를 사용하여 클러스터를 만드는 데 Azure PowerShell 또는 HDInsight.NET SDK를 사용할 수도 있습니다. 이 방법을 사용하는 자세한 내용은 [스크립트 동작을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

1. [Linux에서 HDInsight 클러스터 프로비전](hdinsight-hadoop-provision-linux-clusters.md)의 단계를 사용하여 클러스터 프로비전을 시작하는 한편 프로비전을 완료하지는 마세요.

2. **선택적 구성** 섹션에서 **스크립트 동작**을 선택하고 다음 정보를 제공합니다.

   * **이름**: 스크립트 동작의 이름을 입력합니다.

   * **SCRIPT URI**: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh

   * **HEAD**: 이 옵션을 선택합니다.

   * **WORKER**: 이 옵션을 선택합니다.

   * **ZOOKEEPER**: 이 옵션을 비워둡니다.

   * **매개 변수**: jar을 포함하는 컨테이너 및 저장소 계정에 WASB 주소를 입력합니다. 예를 들어 **wasb://libs@mystorage.blob.core.windows.net/** 입니다.

3. **스크립트 동작**의 아래 쪽에서 **선택** 단추를 사용하여 구성을 저장합니다.

4. **선택적 구성** 섹션에서 **연결된 저장소 계정**을 선택하고 **저장소 키 추가** 연결을 선택합니다. jar를 포함하고 있는 저장소 계정을 선택합니다. 그런 다음 **선택** 단추를 사용하여 설정을 저장하고 **선택적 구성**으로 돌아갑니다.

5. 선택적 구성을 저장하려면 **선택적 구성** 섹션 아래쪽의 **선택** 단추를 사용합니다.

6. [Linux에서 HDInsight 클러스터 프로비전](hdinsight-hadoop-provision-linux-clusters.md)에서 설명한 대로 클러스터를 계속 프로비전합니다.

클러스터 생성이 완료되면 `ADD JAR` 문을 사용하지 않고도 Hive에서 이 스크립트를 통해 추가된 jar을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Hive로 작업하는 방법에 대한 자세한 내용은 [HDInsight로 Hive 사용](hadoop/hdinsight-use-hive.md)
