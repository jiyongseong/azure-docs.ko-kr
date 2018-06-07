---
title: HDInsight에서 Mono 설치 또는 업데이트 - Azure | Microsoft Docs
description: HDInsight 클러스터에서 특정 버전의 Mono를 사용하는 방법을 알아봅니다. Mono는 Linux 기반 HDInsight 클러스터에서 .NET 응용 프로그램을 실행하는 데 사용됩니다.
services: hdinsight
documentationCenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/10/2018
ms.author: larryfr
ms.custom: hdinsightactive
ms.openlocfilehash: 165f1d8175c7c7b58a5eec02a208b81fe73cb5f9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="install-or-update-mono-on-hdinsight"></a>HDInsight에서 Mono 설치 또는 업데이트

HDInsight 3.4 이상에서 특정 버전의 [Mono](https://www.mono-project.com)를 설치하는 방법을 알아봅니다.

Mono는 HDInsight 3.4 이상에서 설치되고 .NET 응용 프로그램을 실행하는 데 사용됩니다. 각 HDInsight 버전에 포함된 Mono 버전에 대한 자세한 내용은 [HDInsight 구성 요소 버전 관리](hdinsight-component-versioning.md)를 참조하세요. 클러스터에서 다른 버전을 설치하려면 이 문서의 스크립트 동작을 사용합니다. 

## <a name="how-it-works"></a>작동 방법

이 스크립트는 다음 매개 변수를 사용합니다.

* __Mono 버전 번호__: 설치할 Mono의 버전입니다. 이 버전은 [https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/](https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/)에서 사용할 수 있습니다.

스크립트는 다음 Mono 패키지를 설치합니다.

* __mono-complete__

* __ca-certificates-mono__

## <a name="the-script"></a>스크립트

__스크립트 위치__: [https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash](https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash)

__요구 사항__:

* 스크립트는 __헤드 노드__ 및 __작업자 노드__에 적용되어야 합니다.

## <a name="to-use-the-script"></a>스크립트 사용

HDInsight에서 이 스크립트를 사용하는 방법에 대한 자세한 내용은 [스크립트 작업을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster) 문서를 참조하세요. Azure Portal, Azure PowerShell 또는 Azure CLI에서 이 스크립트를 사용할 수 있습니다.

스크립트 동작 문서를 진행하는 동안 다음 URI를 사용합니다.

    https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash

설치된 Mono 버전을 지정하려면 __매개 변수__ 필드의 버전 번호를 사용합니다. 예를 들어, `5.4`를 입력하여 Mono 5.4를 설치합니다.

> [!NOTE]
> 이 스크립트로 HDInsight를 구성할 때 스크립트를 __Persisted__로 표시합니다. 이 설정을 사용하면 HDInsight에서 해당 스크립트가 크기 조정 작업을 통해 추가된 작업자 노드에 적용될 수 있습니다.

## <a name="next-steps"></a>다음 단계

HDInsight에서 특정 버전의 Mono를 업그레이드하거나 설치하는 방법을 배웠습니다. HDInsight의 Mono에서 .NET 응용 프로그램을 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [HDInsight의 MapReduce 스트리밍에 .NET 사용](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [HDInsight에서 Hive 및 Pig와 함께 .NET 사용](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)
* [HDInsight에서 Storm으로 C# 솔루션 개발](storm/apache-storm-develop-csharp-visual-studio-topology.md)
* [Linux 기반 HDInsight로 .NET 솔루션 마이그레이션](hdinsight-hadoop-migrate-dotnet-to-linux.md)

스크립트 동작 사용에 대한 자세한 내용은 [스크립트 동작을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.