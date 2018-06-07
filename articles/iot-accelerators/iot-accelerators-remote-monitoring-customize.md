---
title: 원격 모니터링 솔루션 UI 사용자 지정 - Azure | Microsoft Docs
description: 이 문서에서는 원격 모니터링 솔루션 가속기 UI에 대한 소스 코드에 액세스하고 일부 사용자 지정을 수행하는 방법에 대한 정보를 제공합니다.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/17/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 94bd864215ad80b299db09b6237f2cebe63feb88
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>원격 모니터링 솔루션 가속기 사용자 지정

이 문서에서는 소스 코드에 액세스하고 원격 모니터링 솔루션 가속기 UI를 사용자 지정하는 방법에 대한 정보를 제공합니다. 문서에서는 다음을 설명합니다.

## <a name="prepare-a-local-development-environment-for-the-ui"></a>UI에 대한 로컬 개발 환경 준비

원격 모니터링 솔루션 가속기 UI 코드는 React.js 프레임워크를 사용하여 구현됩니다. GitHub 리포지토리의 [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui)에서 소스 코드를 찾을 수 있습니다.

UI를 변경하기 위해 복사본을 로컬로 실행할 수 있습니다. 로컬 복사본은 배포된 솔루션의 인스턴스에 연결하여 원격 분석 검색과 같은 작업을 수행합니다.

다음 단계는 UI 개발에 대한 로컬 환경을 설정하는 프로세스를 설명합니다.

1. **pcs** CLI를 사용하여 솔루션 가속기의 **기본** 인스턴스를 배포합니다. 가상 머신에 대해 제공한 배포 및 자격 증명의 이름을 기록해 둡니다. 자세한 내용은 [CLI를 사용하여 배포](iot-accelerators-remote-monitoring-deploy-cli.md)를 참조하세요.

1. Azure Portal 또는 [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 사용하여 솔루션에서 마이크로 서비스를 호스트하는 가상 머신에 대한 SSH 액세스를 활성화합니다. 예: 

    ```sh
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

1. Azure Portal 또는 [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 사용하여 가상 머신의 이름 및 공용 IP 주소를 찾습니다. 예: 

    ```sh
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. SSH를 사용하여 이전 단계의 IP 주소 및 솔루션을 배포하기 위해 **pcs**를 실행했을 때 제공한 자격 증명을 사용하여 가상 머신에 연결합니다.

1. 로컬 UX를 연결하도록 허용하려면 가상 머신의 bash 셸에서 다음 명령을 실행합니다.

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. 명령이 완료되고 웹 사이트가 시작하는 것을 확인한 후 가상 머신에서 연결을 끊을 수 있습니다.

1. [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) 리포지토리의 로컬 복사본에서 **.env** 파일을 편집하여 배포된 솔루션의 URL을 추가합니다.

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. `azure-iot-pcs-remote-monitoring-webui` 폴더 로컬 복사본의 명령 프롬프트에서 다음 명령을 실행하여 필요한 라이브러리를 설치하고 UI를 로컬로 실행합니다.

    ```cmd/sh
    npm install
    npm start
    ```

1. 이전 명령은 http://localhost:3000/dashboard에서 UI를 로컬로 실행합니다. 사이트가 실행되는 동안 코드를 편집하고 동적으로 업데이트하는 것을 확인할 수 있습니다.

## <a name="customize-the-layout"></a>레이아웃 사용자 지정

원격 모니터링 솔루션의 각 페이지는 소스 코드에서 *패널*이라고 하는 컨트롤의 집합으로 구성됩니다. 예를 들어 **대시보드** 페이지는 개요, 지도, 경보, 원격 분석 및 KPI의 5개 패널로 구성됩니다. [pcs-remote-monitoring-webui](https://github.com/Azure/pcs-remote-monitoring-webui) GitHub 리포지토리에서 각 페이지 및 해당 패널을 정의하는 소스 코드를 찾을 수 있습니다. 예를 들어 **대시보드** 페이지, 해당 레이아웃 및 페이지의 패널을 정의하는 코드는 [src/components/pages/dashboard](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard) 폴더에 있습니다.

패널은 고유의 레이아웃 및 크기 조정을 관리하기 때문에 페이지의 레이아웃을 쉽게 수정할 수 있습니다. 예를 들어 `src/components/pages/dashboard/dashboard.js` 파일의 **PageContent** 요소에 대한 다음 변경 내용은 지도 및 원격 분석 패널의 위치를 교환하고, 맵과 KPI 패널의 상대 너비를 변경합니다.

```nodejs
<PageContent className="dashboard-container" key="page-content">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={kpisIsPending || devicesIsPending}
        error={devicesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-5">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-4">
    <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlarm={devicesInAlarm}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || kpisIsPending}
          error={azureMapsKeyError || devicesError || kpisError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <KpisPanel
        topAlarms={topAlarmsWithName}
        alarmsPerDeviceId={alarmsPerDeviceType}
        criticalAlarmsChange={criticalAlarmsChange}
        warningAlarmsChange={warningAlarmsChange}
        isPending={kpisIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || kpisError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
  </Grid>
</PageContent>
```

![패널 레이아웃 변경](./media/iot-accelerators-remote-monitoring-customize/layout.png)

> [!NOTE]
> 지도는 로컬 배포에서 구성되지 않습니다.

[패널을 복제하고 사용자 지정](#duplicate-and-customize-an-existing-control)하는 경우 동일한 패널의 여러 인스턴스 또는 여러 버전을 추가할 수도 있습니다. 다음 예제는 `src/components/pages/dashboard/dashboard.js` 파일을 편집하여 원격 분석 패널의 두 인스턴스를 추가하는 방법을 보여줍니다.

```nodejs
<PageContent className="dashboard-container" key="page-content">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={kpisIsPending || devicesIsPending}
        error={devicesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-2">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-4">
    <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlarm={devicesInAlarm}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || kpisIsPending}
          error={azureMapsKeyError || devicesError || kpisError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <KpisPanel
        topAlarms={topAlarmsWithName}
        alarmsPerDeviceId={alarmsPerDeviceType}
        criticalAlarmsChange={criticalAlarmsChange}
        warningAlarmsChange={warningAlarmsChange}
        isPending={kpisIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || kpisError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
  </Grid>
</PageContent>
```

그런 다음, 각 패널에서 서로 다른 원격 분석을 볼 수 있습니다.

![여러 원격 분석 패널](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

> [!NOTE]
> 지도는 로컬 배포에서 구성되지 않습니다.

## <a name="duplicate-and-customize-an-existing-control"></a>기존 컨트롤 복제 및 사용자 지정

다음 단계는 기존 패널을 복제하고, 수정하고, 수정된 버전을 사용하는 방법의 예제로 **경보** 패널을 사용하는 방법을 설명합니다.

1. 리포지토리의 로컬 복사본에서 `src/components/pages/dashboard/panels` 폴더에 **경보** 폴더의 복사본을 만듭니다. 새 복사본의 이름을 **cust_alarms**로 지정합니다.

1. **cust_alarms** 폴더의 **alarmsPanel.js** 파일에서 클래스의 이름을 **CustAlarmsPanel**로 편집합니다.

    ```nodejs
    export class CustAlarmsPanel extends Component {
    ```

1. 다음 줄을 `src/components/pages/dashboard/panels/index.js` 파일에 추가합니다.

    ```nodejs
    export * from './cust_alarms';
    ```

1. `src/components/pages/dashboard/dashboard.js` 파일에서 `AlarmsPanel`을 `CustAlarmsPanel`로 대체합니다.

    ```nodejs
    import {
      OverviewPanel,
      CustAlarmsPanel,
      TelemetryPanel,
      KpisPanel,
      MapPanel,
      transformTelemetryResponse,
      chartColors
    } from './panels';

    ...

    <Cell className="col-3">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    ```

이제 **CustAlarms**라는 복사본으로 기존 **경보** 패널을 대체했습니다. 이 복사본은 원본과 동일합니다. 이제 복사본을 수정할 수 있습니다. 예를 들어 **경보** 패널에서 열 순서를 변경하려면:

1. `src/components/pages/dashboard/panels/cust_alarms/alarmsPanel.js` 파일을 엽니다.

1. 다음 코드 조각에 표시된 것과 같이 열 정의를 수정합니다.

    ```nodejs
    this.columnDefs = [
      rulesColumnDefs.severity,
      {
        headerName: 'rules.grid.count',
        field: 'count'
      },
      {
        ...rulesColumnDefs.ruleName,
        minWidth: 200
      },
      rulesColumnDefs.explore
    ];
    ```

다음 스크린샷은 **경보** 패널의 새 버전을 보여줍니다.

![업데이트된 경보 패널](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>원격 분석 차트 사용자 지정

**대시보드** 페이지의 원격 분석 차트는 `src/components/pages/dashboard/panels/telemtry` 폴더의 파일에 의해 정의됩니다. UI는 `src/services/telemetryService.js` 파일의 솔루션 백 엔드에서 원격 분석을 검색합니다. 다음 단계는 원격 분석 차트에 표시된 기간을 15분에서 5분으로 변경하는 방법을 보여줍니다.

1. `src/services/telemetryService.js` 파일에서 **getTelemetryByDeviceIdP15M**이라는 함수를 찾습니다. 이 함수의 복사본을 만들고 해당 복사본을 다음과 같이 수정합니다.

    ```nodejs
    static getTelemetryByDeviceIdP5M(devices = []) {
      return TelemetryService.getTelemetryByMessages({
        from: 'NOW-PT5M',
        to: 'NOW',
        order: 'desc',
        devices
      });
    }
    ```

1. 원격 분석 차트를 채우기 위해 이 새로운 함수를 사용하려면 `src/components/pages/dashboard/dashboard.js` 파일을 엽니다. 원격 분석 스트림을 초기화하는 줄을 찾아 다음과 같이 수정합니다.

    ```node.js
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

이제 원격 분석 차트는 5분의 원격 분석 데이터를 표시합니다.

![1일을 표시하는 원격 분석 차트](./media/iot-accelerators-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>새 KPI 추가

**대시보드** 페이지는 **시스템 KPI** 패널에 KPI를 표시합니다. 이러한 KPI는 `src/components/pages/dashboard/dashboard.js` 파일에서 계산됩니다. KPI는 `src/components/pages/dashboard/panels/kpis/kpisPanel.js` 파일로 렌더링됩니다. 다음 단계는 **대시보드** 페이지에서 새 KPI 값을 계산하고 렌더링하는 방법을 설명합니다. 표시된 예제는 경고 경보 KPI에서 새 백분율 변경 내용을 추가하는 것입니다.

1. `src/components/pages/dashboard/dashboard.js` 파일을 엽니다. 다음과 같이 **warningAlarmsChange** 속성을 포함하도록 **initialState** 개체를 수정합니다.

    ```nodejs
    const initialState = {
      ...

      // Kpis data
      currentActiveAlarms: [],
      topAlarms: [],
      alarmsPerDeviceId: {},
      criticalAlarmsChange: 0,
      warningAlarmsChange: 0,
      kpisIsPending: true,
      kpisError: null,

      ...
    };
    ```

1. 속성으로 **totalWarningCount**를 포함하도록 **currentAlarmsStats** 개체를 수정합니다.

    ```nodejs
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alarmsPerDeviceId: updatedAlarmsPerDeviceId
    };
    ```

1. 새 KPI를 계산합니다. 중요한 경보 개수에 대한 계산을 찾습니다. 코드를 복제하고 복사본을 다음과 같이 수정합니다.

    ```nodejs
    // ================== Warning Alarms Count - START
    const currentWarningAlarms = currentAlarmsStats.totalWarningCount;
    const previousWarningAlarms = previousAlarms.reduce(
      (cnt, { severity }) => severity === 'warning' ? cnt + 1 : cnt,
      0
    );
    const warningAlarmsChange = ((currentWarningAlarms - previousWarningAlarms) / currentWarningAlarms * 100).toFixed(2);
    // ================== Warning Alarms Count - END
    ```

1. KPI 스트림에 새 **warningAlarmsChange** KPI를 포함시킵니다.

    ```nodejs
    return ({
      kpisIsPending: false,

      // Kpis data
      currentActiveAlarms,
      topAlarms,
      criticalAlarmsChange,
      warningAlarmsChange,
      alarmsPerDeviceId: currentAlarmsStats.alarmsPerDeviceId,

      ...
    });

1. Include the new **warningAlarmsChange** KPI in the state data used to render the UI:

    ```nodejs
    const {
      ...

      currentActiveAlarms,
      topAlarms,
      alarmsPerDeviceId,
      criticalAlarmsChange,
      warningAlarmsChange,
      kpisIsPending,
      kpisError,

      ...
    } = this.state;
    ```

1. KPI 패널에 전달되는 데이터를 업데이트합니다.

    ```node.js
    <KpisPanel
      topAlarms={topAlarmsWithName}
      alarmsPerDeviceId={alarmsPerDeviceType}
      criticalAlarmsChange={criticalAlarmsChange}
      warningAlarmsChange={warningAlarmsChange}
      isPending={kpisIsPending || rulesIsPending || devicesIsPending}
      error={devicesError || rulesError || kpisError}
      colors={chartColorObjects}
      t={t} />
    ```

이제 `src/components/pages/dashboard/dashboard.js` 파일에서 변경을 완료했습니다. 다음 단계는 `src/components/pages/dashboard/panels/kpis/kpisPanel.js` 파일에서 변경을 수행하여 새 KPI를 표시하는 것을 설명합니다.

1. 다음 코드 줄을 수정하여 다음과 같이 새 KPI 값을 검색합니다.

    ```nodejs
    const { t, isPending, criticalAlarmsChange, warningAlarmsChange, error } = this.props;
    ```

1. 태그를 수정하여 다음과 같이 새 KPI 값을 표시합니다.

    ```nodejs
    <div className="kpi-cell">
      <div className="kpi-header">{t('dashboard.panels.kpis.criticalAlarms')}</div>
      <div className="critical-alarms">
        {
          criticalAlarmsChange !== 0 &&
            <div className="kpi-percentage-container">
              <div className="kpi-value">{ criticalAlarmsChange }</div>
              <div className="kpi-percentage-sign">%</div>
            </div>
        }
      </div>
      <div className="kpi-header">{t('Warning alarms')}</div>
      <div className="critical-alarms">
        {
          warningAlarmsChange !== 0 &&
            <div className="kpi-percentage-container">
              <div className="kpi-value">{ warningAlarmsChange }</div>
              <div className="kpi-percentage-sign">%</div>
            </div>
        }
      </div>
    </div>
    ```

**대시보드** 페이지는 이제 새 KPI 값을 표시합니다.

![경고 KPI](./media/iot-accelerators-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>맵 사용자 지정

솔루션에서 맵 구성 요소의 자세한 내용은 GitHub의 [맵 사용자 지정](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) 페이지를 참조하세요.

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>기타 사용자 지정 옵션

원격 모니터링 솔루션에서 프레젠테이션 및 시각화 계층을 추가 수정하기 위해 코드를 편집할 수 있습니다. 관련 GitHub 리포지토리는 다음과 같습니다.

* [Azure IoT 솔루션(.NET)에 대한 구성 마이크로 서비스](https://github.com/Azure/pcs-ui-config-dotnet/)
* [Azure IoT 솔루션(Java)에 대한 구성 마이크로 서비스](https://github.com/Azure/pcs-ui-config-java/)
* [Azure IoT PCS 원격 모니터링 웹 UI](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>다음 단계

이 문서에서는 원격 모니터링 솔루션 가속기에서 웹 UI를 사용자 지정하는 데 사용할 수 있는 리소스에 대해 알아보았습니다.

원격 모니터링 솔루션 가속기에 대한 자세한 개념 정보는 [원격 모니터링 아키텍처](iot-accelerators-remote-monitoring-sample-walkthrough.md)를 참조하세요.

원격 모니터링 솔루션 사용자 지정에 대한 자세한 내용은 [마이크로 서비스 사용자 지정 및 재배포](iot-accelerators-microservices-example.md)를 참조하세요.
<!-- Next tutorials in the sequence -->