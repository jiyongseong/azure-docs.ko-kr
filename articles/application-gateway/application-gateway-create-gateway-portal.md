---
title: Application Gateway 만들기 - Azure Portal | Microsoft Docs
description: Azure Portal을 사용하여 응용 프로그램 게이트웨이를 만드는 방법을 알아봅니다.
services: application-gateway
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: victorh
ms.openlocfilehash: 0df71c445d2c5fc6827b69f708203a3b3e6e2b53
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
---
# <a name="create-an-application-gateway-using-the-azure-portal"></a>Azure Portal을 사용하여 응용 프로그램 게이트웨이 만들기

Azure Portal을 사용하여 응용 프로그램 게이트웨이를 만들거나 관리할 수 있습니다. 이 빠른 시작에서는 네트워크 리소스, 백 엔드 서버 및 응용 프로그램 게이트웨이를 만드는 방법을 보여 줍니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

[http://portal.azure.com](http://portal.azure.com)에서 Azure Portal에 로그인

## <a name="create-an-application-gateway"></a>응용 프로그램 게이트웨이 만들기

가상 네트워크는 사용자가 만든 리소스 간의 통신에 필요합니다. 이 예제에서는 두 개의 서브넷을 만듭니다. 하나는 응용 프로그램 게이트웨이용이고, 다른 하나는 백 엔드 서버용입니다. 응용 프로그램 게이트웨이를 만드는 동시에 가상 네트워크를 만들 수 있습니다.

1. Azure Portal의 왼쪽 위에서 **새로 만들기**를 클릭합니다.
2. **네트워킹**을 선택한 다음, 추천 목록에서 **Application Gateway**를 선택합니다.
3. 응용 프로그램 게이트웨이에 대해 다음 값을 입력합니다.

    - *myAppGateway* - 응용 프로그램 게이트웨이의 이름
    - *myResourceGroupAG* - 새 리소스 그룹의 이름

    ![새 응용 프로그램 게이트웨이 만들기](./media/application-gateway-create-gateway-portal/application-gateway-create.png)

4. 다른 설정에 대한 기본값을 적용한 다음, **확인**을 클릭합니다.
5. **가상 네트워크 선택**을 클릭하고 **새로 만들기**를 클릭한 다음, 가상 네트워크에 대해 다음 값을 입력합니다.

    - *myVNet* - 가상 네트워크 이름
    - *10.0.0.0/16* - 가상 네트워크 주소 공간
    - *myAGSubnet* - 서브넷 이름
    - *10.0.0.0/24* - 서브넷 주소 공간

    ![가상 네트워크 만들기](./media/application-gateway-create-gateway-portal/application-gateway-vnet.png)

6. **확인**을 클릭하여 가상 네트워크 및 서브넷을 만듭니다.
6. **공용 IP 주소 선택**을 클릭하고 **새로 만들기**를 클릭한 다음, 공용 IP 주소의 이름을 입력합니다. 이 예제에서 공용 IP 주소의 이름은 *myAGPublicIPAddress*입니다. 다른 설정에 대한 기본값을 적용한 다음, **확인**을 클릭합니다.
8. 수신기 구성에 대한 기본값을 적용하고, 웹 응용 프로그램 방화벽을 사용하지 않도록 설정한 상태로 둔 다음, **확인**을 클릭합니다.
9. 요약 페이지에서 설정을 검토한 다음, **확인**을 클릭하여 가상 네트워크, 공용 IP 주소 및 응용 프로그램 게이트웨이를 만듭니다. 응용 프로그램 게이트웨이를 만드는 데 몇 분이 걸릴 수 있습니다. 배포가 완료될 때까지 기다린 후 다음 섹션으로 이동합니다.

### <a name="add-a-subnet"></a>서브넷 추가

1. 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음, 리소스 목록에서 **myVNet**을 클릭합니다.
2. **서브넷**을 클릭한 다음, **서브넷**을 클릭합니다.

    ![서브넷 만들기](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

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

    ![사용자 지정 확장 설치](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

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

3. **모든 리소스**를 클릭한 다음, **myAppGateway**를 클릭합니다.
4. **백 엔드 풀**을 클릭합니다. 기본 풀이 응용 프로그램 게이트웨이와 함께 자동으로 만들어졌습니다. **appGatewayBackendPool**을 클릭합니다.
5. **대상 추가**를 클릭하여 만든 가상 머신 각각을 백 엔드 풀에 추가합니다.

    ![백 엔드 서버 추가](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. **저장**을 클릭합니다.

## <a name="test-the-application-gateway"></a>응용 프로그램 게이트웨이 테스트

1. [개요] 화면에서 응용 프로그램 게이트웨이에 대한 공용 IP 주소를 찾습니다. **모든 리소스**를 클릭한 다음, **myAGPublicIPAddress**를 클릭합니다.

    ![응용 프로그램 게이트웨이에 대한 공용 IP 주소 기록](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)

2. 공용 IP 주소를 복사한 다음, 브라우저의 주소 표시줄에 붙여넣습니다.

    ![응용 프로그램 게이트웨이 테스트](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 리소스 그룹, 가상 컴퓨터 및 모든 관련 리소스를 삭제합니다. 이렇게 하려면 응용 프로그램 게이트웨이가 포함된 리소스 그룹을 선택하고 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 리소스 그룹, 네트워크 리소스 및 백 엔드 서버를 만들었습니다. 그런 다음, 이러한 리소스를 사용하여 응용 프로그램 게이트웨이를 만들었습니다. 응용 프로그램 게이트웨이 및 관련 리소스에 대해 자세히 알아보려면 방법 문서를 참조하세요.
