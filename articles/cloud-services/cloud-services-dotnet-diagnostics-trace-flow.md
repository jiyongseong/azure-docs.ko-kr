---
title: "Azure 진단으로 Cloud Services 응용 프로그램의 흐름 추적 | Microsoft Docs"
description: "Azure 응용 프로그램에 추적 메시지를 추가하여 디버깅, 성능 측정, 모니터링, 트래픽 분석 등을 할 수 있습니다."
services: cloud-services
documentationcenter: .net
author: thraka
manager: timlt
editor: 
ms.assetid: 09934772-cc07-4fd2-ba88-b224ca192f8e
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/20/2016
ms.author: adegeo
ms.openlocfilehash: 7551ae2e3a7c5f3ff6010bf34e7600da1028d67f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/22/2018
---
# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>Azure 진단으로 Cloud Services 응용 프로그램의 흐름 추적
추적은 실행되는 동안 응용 프로그램의 실행을 모니터링하는 방법입니다. [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx), [System.Diagnostics.Debug](https://msdn.microsoft.com/library/system.diagnostics.debug.aspx) 및 [System.Diagnostics.TraceSource](https://msdn.microsoft.com/library/system.diagnostics.tracesource.aspx) 클래스를 사용하여 로그의 오류 및 응용 프로그램 실행, 텍스트 파일 또는 차후 분석을 위한 다른 장치에 대한 정보를 기록할 수 있습니다. 추적에 대한 자세한 내용은 [응용 프로그램을 추적 및 계측](https://msdn.microsoft.com/library/zs6s4h68.aspx)을 참조하세요.

## <a name="use-trace-statements-and-trace-switches"></a>추적 문 및 추적 스위치 사용
[DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) 를 응용 프로그램 구성에 추가하고 응용 프로그램 코드에 System.Diagnostics.Trace 또는 System.Diagnostics.Debug에 대해 호출하여 Cloud Services 응용 프로그램에서 추적을 구현합니다. 작업자 역할에 대해 구성 파일 *app.config* 및 웹 역할에 대해 *web.config*를 사용합니다. Visual Studio 템플릿을 사용하여 새 호스티드 서비스를 만드는 경우 Azure 진단이 프로젝트에 자동으로 추가되고 DiagnosticMonitorTraceListener가 추가하는 역할에 대한 적절한 구성 파일에 추가됩니다.

추적 문 배치에 대한 정보는 [방법: 응용 프로그램 코드에 추적 문 추가](https://msdn.microsoft.com/library/zd83saa2.aspx)를 참조하세요.

코드에 [추적 스위치](https://msdn.microsoft.com/library/3at424ac.aspx) 를 배치하여 추적의 발생 여부와 범위를 제어할 수 있습니다. 이렇게 하면 프로덕션 환경에서 응용 프로그램의 상태를 모니터링할 수 있습니다. 여러 컴퓨터에서 실행하는 여러 구성 요소를 사용하는 비즈니스 응용 프로그램에서 특히 중요합니다. 자세한 내용은 [방법: 추적 스위치 구성](https://msdn.microsoft.com/library/t06xyy08.aspx)을 참조하세요.

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Azure 응용 프로그램에서 추적 수신기 구성
추적, 디버그 및 TraceSource는 전송된 메시지를 수집하고 기록할 "수신기" 설정이 필요합니다. 수신기는 추적 메시지를 수집, 저장 및 라우팅합니다. 추적 출력을 로그, 창 또는 텍스트 파일과 같은 적절한 대상에 직접 실행합니다. Azure 진단은 [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) 클래스를 사용합니다.

다음 절차를 완료하기 전에 Azure 진단 모니터를 초기화해야 합니다. 이렇게 하려면 [Microsoft Azure에서 진단 사용](cloud-services-dotnet-diagnostics.md)을 참조하세요.

Visual Studio에서 제공되는 서식 파일을 사용하는 경우 수신기의 구성은 자동으로 추가됩니다.

### <a name="add-a-trace-listener"></a>추적 수신기 추가
1. 사용자의 역할에 대한 web.config 또는 app.config 파일을 엽니다.
2. 파일에 다음 코드를 추가합니다. 참조할 어셈블리의 버전 번호를 사용하려면 버전 특성을 변경합니다. 어셈블리 버전은 업데이트가 없는 경우 각 Azure SDK 릴리스로 변경할 필요는 없습니다.
   
    ```
    <system.diagnostics>
        <trace>
            <listeners>
                <add type="Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener,
                  Microsoft.WindowsAzure.Diagnostics,
                  Version=2.8.0.0,
                  Culture=neutral,
                  PublicKeyToken=31bf3856ad364e35"
                  name="AzureDiagnostics">
                    <filter type="" />
                </add>
            </listeners>
        </trace>
    </system.diagnostics>
    ```
   > [!IMPORTANT]
   > Microsoft.WindowsAzure.Diagnostics 어셈블리에 대한 프로젝트 참조가 있어야 합니다. 참조된 Microsoft.WindowsAzure.Diagnostics 어셈블리의 버전과 일치하도록 위의 xml의 버전 번호를 업데이트합니다.
   > 
   > 
3. 구성 파일을 저장합니다.

수신기에 대한 자세한 내용은 [추적 수신기](https://msdn.microsoft.com/library/4y5y10s7.aspx)를 참조하세요.

수신기를 추가하는 단계를 완료한 후 코드에 추적 문을 추가할 수 있습니다.

### <a name="to-add-trace-statement-to-your-code"></a>코드에 추적 문을 추가하려면
1. 응용 프로그램에 대한 소스 파일을 엽니다. 예를 들어 작업자 역할 또는 웹 역할에 대한 <RoleName>.cs 파일입니다.
2. 아직 추가되지 않은 경우 문을 사용하여 다음을 추가합니다.
    ```
        using System.Diagnostics;
    ```
3. 응용 프로그램의 상태에 대한 정보를 캡처하려는 추적 문을 추가합니다. 다양한 메서드를 사용하여 추적 문의 출력을 포맷할 수 있습니다. 자세한 내용은 [방법: 응용 프로그램 코드에 추적 문 추가](https://msdn.microsoft.com/library/zd83saa2.aspx)를 참조하세요.
4. 소스 파일을 저장합니다.

