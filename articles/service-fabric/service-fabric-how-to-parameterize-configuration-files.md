---
title: Azure Service Fabric에서 구성 파일을 매개 변수화하는 방법 | Microsoft Docs
description: Service Fabric에서 구성 파일을 매개 변수화하는 방법을 보여 줍니다.
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: e5bb2f270cc5a6f288e1e995f4bfa74f4e3551b7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Service Fabric에서 구성 파일을 매개 변수화하는 방법

이 문서에서는 Service Fabric에서 구성 파일을 매개 변수화하는 방법을 보여 줍니다.

## <a name="procedure-for-parameterizing-configuration-files"></a>구성 파일 매개 변수화 절차

이 예제에서는 응용 프로그램 배포에서 매개 변수를 사용하여 구성 값을 재정의합니다.

1. Config\Settings.xml 파일을 엽니다.
1. 다음 XML을 추가하여 구성 매개 변수를 설정합니다.

    ```xml
      <Section Name="MyConfigSection">
        <Parameter Name="CacheSize" Value="25" />
      </Section>
    ```

1. 파일을 저장하고 닫습니다.
1. `ApplicationManifest.xml` 파일을 엽니다.
1. `ConfigOverride` 요소를 추가하여 구성 패키지, 섹션 및 매개 변수를 참조합니다.

      ```xml
        <ConfigOverrides>
          <ConfigOverride Name="Config">
              <Settings>
                <Section Name="MyConfigSection">
                    <Parameter Name="CacheSize" Value="[Stateless1_CacheSize]" />
                </Section>
              </Settings>
          </ConfigOverride>
        </ConfigOverrides>
      ```

1. ApplicationManifest.xml 파일에서 `Parameters` 요소에 매개 변수를 지정합니다.

    ```xml
      <Parameters>
        <Parameter Name="Stateless1_CacheSize" />
      </Parameters>
    ```

1. 그런 후 `DefaultValue`를 정의합니다.

    ```xml
      <Parameters>
        <Parameter Name="Stateless1_CacheSize" DefaultValue="80" />
      </Parameters>
    ```

> [!NOTE]
> ConfigOverride를 추가하는 경우, Service Fabric은 항상 응용 프로그램 매니페스트에 지정된 응용 프로그램 매개 변수 또는 기본값을 선택합니다.
>
>

## <a name="next-steps"></a>다음 단계
이 문서에서 설명하는 핵심 개념 중 일부에 대한 자세한 내용은 [여러 환경에 대한 응용 프로그램 관리](service-fabric-manage-multiple-environment-app-configuration.md) 문서를 참조하세요.

Visual Studio에서 사용할 수 있는 다른 앱 관리 기능에 대한 정보는 [Visual Studio에서 서비스 패브릭 응용 프로그램 관리](service-fabric-manage-application-in-visual-studio.md)를 참조하세요.