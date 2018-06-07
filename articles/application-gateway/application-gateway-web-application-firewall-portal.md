---
title: 웹 응용 프로그램 방화벽이 있는 응용 프로그램 게이트웨이 만들기 - Azure Portal | Microsoft Docs
description: Azure Portal을 사용하여 웹 응용 프로그램 방화벽이 있는 응용 프로그램 게이트웨이를 만드는 방법에 대해 알아봅니다.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: victorh
ms.openlocfilehash: 9967813b193159b68aa0f008dae4440aa6e533dc
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Azure Portal을 사용하여 웹 응용 프로그램 방화벽이 있는 응용 프로그램 게이트웨이를 만듭니다.

Azure Portal을 사용하여 WAF([웹 응용 프로그램 방화벽](application-gateway-web-application-firewall-overview.md))이 있는 [응용 프로그램 게이트웨이](application-gateway-introduction.md)를 만들 수 있습니다. WAF는 [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 규칙을 사용하여 응용 프로그램을 보호합니다. 이러한 규칙에는 SQL 삽입, 사이트 간 스크립팅 공격 및 세션 하이재킹과 같은 공격으로부터의 보호가 포함됩니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * WAF를 사용하는 응용 프로그램 게이트웨이 만들기
> * 백 엔드 서버로 사용되는 가상 머신 만들기
> * 저장소 계정 만들기 및 진단 구성

![웹 응용 프로그램 방화벽 예제](./media/application-gateway-web-application-firewall-portal/scenario-waf.png)

## <a name="log-in-to-azure"></a>Azure에 로그인

[http://portal.azure.com](http://portal.azure.com)에서 Azure Portal에 로그인

## <a name="create-an-application-gateway"></a>응용 프로그램 게이트웨이 만들기

가상 네트워크는 사용자가 만든 리소스 간의 통신에 필요합니다. 이 예제에서는 두 개의 서브넷을 만듭니다. 하나는 응용 프로그램 게이트웨이용이고, 다른 하나는 백 엔드 서버용입니다. 응용 프로그램 게이트웨이를 만드는 동시에 가상 네트워크를 만들 수 있습니다.

1. Azure Portal의 왼쪽 위에서 **새로 만들기**를 클릭합니다.
2. **네트워킹**을 선택한 다음, 추천 목록에서 **Application Gateway**를 선택합니다.
3. 응용 프로그램 게이트웨이에 대해 다음 값을 입력합니다.

    - *myAppGateway* - 응용 프로그램 게이트웨이의 이름
    - *myResourceGroupAG* - 새 리소스 그룹
    - 응용 프로그램 게이트웨이의 계층에 *WAF*를 선택합니다.

    ![새 응용 프로그램 게이트웨이 만들기](./media/application-gateway-web-application-firewall-portal/application-gateway-create.png)

4. 다른 설정에 대한 기본값을 적용한 다음, **확인**을 클릭합니다.
5. **가상 네트워크 선택**을 클릭하고 **새로 만들기**를 클릭한 다음, 가상 네트워크에 대해 다음 값을 입력합니다.

    - *myVNet* - 가상 네트워크 이름
    - *10.0.0.0/16* - 가상 네트워크 주소 공간
    - *myAGSubnet* - 서브넷 이름
    - *10.0.0.0/24* - 서브넷 주소 공간

    ![가상 네트워크 만들기](./media/application-gateway-web-application-firewall-portal/application-gateway-vnet.png)

6. **확인**을 클릭하여 가상 네트워크 및 서브넷을 만듭니다.
7. **공용 IP 주소 선택**을 클릭하고 **새로 만들기**를 클릭한 다음, 공용 IP 주소의 이름을 입력합니다. 이 예제에서 공용 IP 주소의 이름은 *myAGPublicIPAddress*입니다. 다른 설정에 대한 기본값을 적용한 다음, **확인**을 클릭합니다.
8. 수신기 구성에 대한 기본값을 수락하고 웹 응용 프로그램 방화벽을 사용하지 않도록 유지한 다음, **확인**을 클릭합니다.
9. 요약 페이지에서 설정을 검토한 다음, **확인**을 클릭하여 네트워크 리소스와 응용 프로그램 게이트웨이를 만듭니다. 응용 프로그램 게이트웨이가 생성되는 데 몇 분이 걸릴 수 있습니다. 배포가 완료될 때까지 기다렸다가 다음 섹션으로 이동합니다.

### <a name="add-a-subnet"></a>서브넷 추가

1. 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음, 리소스 목록에서 **myVNet**을 클릭합니다.
2. **서브넷**을 클릭한 다음, **서브넷**을 클릭합니다.

    ![서브넷 만들기](./media/application-gateway-web-application-firewall-portal/application-gateway-subnet.png)

3. 서브넷 이름에 *myBackendSubnet*을 입력한 다음, **확인**을 클릭합니다.

## <a name="create-backend-servers"></a>백 엔드 서버 만들기

이 예제에서는 응용 프로그램 게이트웨이에 대한 백 엔드 서버로 사용할 두 개의 가상 머신을 만듭니다. 또한 응용 프로그램 게이트웨이가 성공적으로 만들어 졌는지 확인하기 위해 가상 머신에 IIS를 설치합니다.

### <a name="create-a-virtual-machine"></a>가상 머신 만들기

1. **새로 만들기**를 클릭합니다.
2. **Compute**를 클릭한 다음, 추천 목록에서 **Windows Server 2016 Datacenter**를 선택합니다.
3. 가상 머신에 대해 다음 값을 입력합니다.

    - *myVM* - 가상 머신의 이름
    - *azureuser* - 관리자 사용자 이름
    - *Azure123456!* - 암호
    - **기존 항목 사용**을 선택한 다음, *myResourceGroupAG*를 선택합니다.

4. **확인**을 클릭합니다.
5. 가상 머신의 크기에 대해 **DS1_V2**를 선택하고 **선택**을 클릭합니다.
6. 가상 네트워크에 대해 **myVNet**이 선택되어 있고 서브넷이 **myBackendSubnet**인지 확인합니다. 
7. **사용 안 함**을 클릭하여 부팅 진단을 사용하지 않도록 설정합니다.
8. **확인**을 클릭하고 요약 페이지에서 설정을 검토한 다음, **만들기**를 클릭합니다.

### <a name="install-iis"></a>IIS 설치

1. 대화형 셸을 열고 **PowerShell**로 설정되어 있는지 확인합니다.

    ![사용자 지정 확장 설치](./media/application-gateway-web-application-firewall-portal/application-gateway-extension.png)

2. 다음 명령을 실행하여 가상 머신에 IIS를 설치합니다. 

    ```azurepowershell-interactive
    Set-AzureRmVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. 두 번째 가상 머신을 만들고, 방금 완료한 단계를 사용하여 IIS를 설치합니다. Set-AzureRmVMExtension의 이름 및 VMName에 대해 *myVM2*를 입력합니다.

### <a name="add-backend-servers"></a>백 엔드 서버 추가

1. **모든 리소스**를 클릭한 다음, **myAppGateway**를 클릭합니다.
2. **백 엔드 풀**을 클릭합니다. 기본 풀이 응용 프로그램 게이트웨이와 함께 자동으로 생성되었습니다. **appGateayBackendPool**을 클릭합니다.
3. **대상 추가**를 클릭하여 생성된 각각의 가상 머신을 백 엔드 풀에 추가합니다.

    ![백 엔드 서버 추가](./media/application-gateway-web-application-firewall-portal/application-gateway-backend.png)

4. **저장**을 클릭합니다.

## <a name="create-a-storage-account-and-configure-diagnostics"></a>저장소 계정 만들기 및 진단 구성

## <a name="create-a-storage-account"></a>저장소 계정 만들기

이 자습서에서 응용 프로그램 게이트웨이는 저장소 계정을 사용하여 검색 및 방지 목적으로 데이터를 저장합니다. Log Analytics 또는 Event Hub를 사용하여 데이터를 기록할 수도 있습니다.

1. Azure Portal의 왼쪽 위에 있는 **새로 만들기**를 클릭합니다.
2. **저장소**를 선택한 다음, **저장소 계정 - Blob, 파일, 테이블, 큐**를 선택합니다.
3. 저장소 계정의 이름을 입력하고 리소스 그룹에 **기존 그룹 사용**을 선택한 다음, **myResourceGroupAG**를 선택합니다. 이 예제에서 저장소 계정 이름은 *myagstore1*입니다. 다른 설정에 대한 기본값을 적용한 다음, **만들기**를 클릭합니다.

## <a name="configure-diagnostics"></a>진단 구성

ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog 및 ApplicationGatewayFirewallLog 로그에 데이터를 기록하도록 진단을 구성합니다.

1. 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음, *myAppGateway*를 선택합니다.
2. 모니터링에서 **진단 로그**를 클릭합니다.
3. **진단 설정 추가**를 클릭합니다.
4. 진단 설정의 이름으로 *myDiagnosticsSettings*를 입력합니다.
5. **저장소 계정에 보관**을 선택한 다음, **구성**을 클릭하여 이전에 만든 *myagstore1* 저장소 계정을 선택합니다.
6. 수집하고 유지할 응용 프로그램 게이트웨이 로그를 선택합니다.
7. **저장**을 클릭합니다.

    ![진단 구성](./media/application-gateway-web-application-firewall-portal/application-gateway-diagnostics.png)

## <a name="test-the-application-gateway"></a>응용 프로그램 게이트웨이 테스트

1. [개요] 화면에서 응용 프로그램 게이트웨이에 대한 공용 IP 주소를 찾습니다. **모든 리소스**를 클릭한 다음, **myAGPublicIPAddress**를 클릭합니다.

    ![응용 프로그램 게이트웨이에 대한 공용 IP 주소 기록](./media/application-gateway-web-application-firewall-portal/application-gateway-record-ag-address.png)

2. 공용 IP 주소를 복사한 다음, 브라우저의 주소 표시줄에 붙여넣습니다.

    ![응용 프로그램 게이트웨이 테스트](./media/application-gateway-web-application-firewall-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * WAF를 사용하는 응용 프로그램 게이트웨이 만들기
> * 백 엔드 서버로 사용되는 가상 머신 만들기
> * 저장소 계정 만들기 및 진단 구성

응용 프로그램 게이트웨이 및 관련 리소스에 대해 자세히 알아보려면 방법 문서를 참조하세요.