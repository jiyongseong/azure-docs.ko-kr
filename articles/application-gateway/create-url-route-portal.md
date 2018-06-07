---
title: URL 경로 기반 라우팅 규칙을 사용하여 응용 프로그램 게이트웨이 만들기 - Azure Portal | Microsoft Docs
description: Azure Portal을 사용하여 응용 프로그램 게이트웨이 및 가상 머신 확장 집합에 URL 경로 기반 라우팅 규칙을 만드는 방법을 알아봅니다.
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
ms.openlocfilehash: 6b45b00de53822224afbfb3a15dbc6790deb11ce
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34356344"
---
# <a name="create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Azure Portal을 사용하여 경로 기반 라우팅 규칙을 사용하여 응용 프로그램 게이트웨이 만들기

Azure Portal을 사용하여 [응용 프로그램 게이트웨이](overview.md)를 만들 때 [URL 경로 기반 라우팅 규칙](url-route-overview.md)을 구성할 수 있습니다. 이 자습서에서는 가상 머신을 사용하여 백 엔드 풀을 만듭니다. 그런 다음, 웹 트래픽이 풀의 적절한 서버에 도착하도록 하는 라우팅 규칙을 만듭니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 응용 프로그램 게이트웨이 만들기
> * 백 엔드 서버용 가상 머신 만들기
> * 백 엔드 서버로 백 엔드 풀 만들기
> * 백 엔드 수신기 만들기
> * 경로 기반 라우팅 규칙 만들기

![URL 라우팅 예제](./media/create-url-route-portal/scenario.png)

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

    ![새 응용 프로그램 게이트웨이 만들기](./media/create-url-route-portal/application-gateway-create.png)

4. 다른 설정에 대한 기본값을 적용한 다음, **확인**을 클릭합니다.
5. **가상 네트워크 선택**을 클릭하고 **새로 만들기**를 클릭한 다음, 가상 네트워크에 대해 다음 값을 입력합니다.

    - *myVNet* - 가상 네트워크 이름
    - *10.0.0.0/16* - 가상 네트워크 주소 공간
    - *myAGSubnet* - 서브넷 이름
    - *10.0.0.0/24* - 서브넷 주소 공간

    ![가상 네트워크 만들기](./media/create-url-route-portal/application-gateway-vnet.png)

6. **확인**을 클릭하여 가상 네트워크 및 서브넷을 만듭니다.
7. **공용 IP 주소 선택**을 클릭하고 **새로 만들기**를 클릭한 다음, 공용 IP 주소의 이름을 입력합니다. 이 예제에서 공용 IP 주소의 이름은 *myAGPublicIPAddress*입니다. 다른 설정에 대한 기본값을 적용한 다음, **확인**을 클릭합니다.
8. 수신기 구성에 대한 기본값을 수락하고 웹 응용 프로그램 방화벽을 사용하지 않도록 유지한 다음, **확인**을 클릭합니다.
9. 요약 페이지에서 설정을 검토한 다음, **확인**을 클릭하여 네트워크 리소스와 응용 프로그램 게이트웨이를 만듭니다. 응용 프로그램 게이트웨이가 생성되는 데 몇 분이 걸릴 수 있습니다. 배포가 완료될 때까지 기다렸다가 다음 섹션으로 이동합니다.

### <a name="add-a-subnet"></a>서브넷 추가

1. 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음, 리소스 목록에서 **myVNet**을 클릭합니다.
2. **서브넷**을 클릭한 다음, **서브넷**을 클릭합니다.

    ![서브넷 만들기](./media/create-url-route-portal/application-gateway-subnet.png)

3. 서브넷 이름에 *myBackendSubnet*을 입력한 다음, **확인**을 클릭합니다.

## <a name="create-virtual-machines"></a>가상 머신 만들기

이 예제에서는 응용 프로그램 게이트웨이의 백 엔드 서버로 사용될 세 개의 가상 머신을 만듭니다. 또한 응용 프로그램 게이트웨이가 성공적으로 만들어 졌는지 확인하기 위해 가상 머신에 IIS를 설치합니다.

1. **새로 만들기**를 클릭합니다.
2. **Compute**를 클릭한 다음, 추천 목록에서 **Windows Server 2016 Datacenter**를 선택합니다.
3. 가상 머신에 대해 다음 값을 입력합니다.

    - *myVM1* - 가상 머신의 이름
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

    ![사용자 지정 확장 설치](./media/create-url-route-portal/application-gateway-extension.png)

2. 다음 명령을 실행하여 가상 머신에 IIS를 설치합니다. 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/davidmu1/samplescripts/master/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzureRmVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName myVM1 `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. 가상 머신을 두 개 더 만들고 방금 완료한 단계를 사용하여 IIS를 설치합니다. Set-AzureRmVMExtension에서 VMName의 값과 이름에 *myVM2* 및 *myVM3*의 이름을 입력합니다.

## <a name="create-backend-pools-with-the-virtual-machines"></a>가상 머신으로 백 엔드 풀 만들기

1. **모든 리소스**를 클릭한 다음, **myAppGateway**를 클릭합니다.
2. **백 엔드 풀**을 클릭합니다. 기본 풀이 응용 프로그램 게이트웨이와 함께 자동으로 생성되었습니다. **appGateayBackendPool**을 클릭합니다.
3. **대상 추가**를 클릭하여 *myVM1*을 appGatewayBackendPool에 추가합니다.

    ![백 엔드 서버 추가](./media/create-url-route-portal/application-gateway-backend.png)

4. **저장**을 클릭합니다.
5. **백엔드 풀**을 클릭한 다음, **추가**를 클릭합니다.
6. *imagesBackendPool*의 이름을 입력하고 **대상 추가**를 사용하여 *myVM2*를 추가합니다.
7. **확인**을 클릭합니다.
8. **추가**를 다시 클릭하여 이름이 *videoBackendPool*인 또 다른 백 엔드 풀을 추가하고 여기에 *myVM3*을 추가합니다.

## <a name="create-a-backend-listener"></a>백 엔드 수신기 만들기

1. **수신기**를 클릭한 다음, **기본**을 클릭합니다.
2. 이름에 *myBackendListener*를 입력하고 프런트 엔드 포트의 이름에 *myFrontendPort*를 입력한 수신기의 포트에 *8080*을 입력합니다.
3. **확인**을 클릭합니다.

## <a name="create-a-path-based-routing-rule"></a>경로 기반 라우팅 규칙 만들기

1. **규칙**을 클릭한 다음, **경로 기반**을 클릭합니다.
2. 이름에 *rule2*를 입력합니다.
3. 첫 번째 경로의 이름에 *Images*를 입력합니다. 경로에 */images/**를 입력합니다. 백 엔드 풀에 **imagesBackendPool**을 선택합니다.
4. 두 번째 경로의 이름에 *Video*를 입력합니다. 경로에 */video/**를 입력합니다. 백 엔드 풀에 **videoBackendPool**을 선택합니다.

    ![경로 기반 규칙 만들기](./media/create-url-route-portal/application-gateway-route-rule.png)

5. **확인**을 클릭합니다.

## <a name="test-the-application-gateway"></a>응용 프로그램 게이트웨이 테스트

1. **모든 리소스**를 클릭한 다음, **myAGPublicIPAddress**를 클릭합니다.

    ![응용 프로그램 게이트웨이 공용 IP 주소 기록](./media/create-url-route-portal/application-gateway-record-ag-address.png)

2. 공용 IP 주소를 복사하여 브라우저의 주소 표시줄에 붙여넣습니다. 예: http://http://40.121.222.19

    ![응용 프로그램 게이트웨이의 기준 URL 테스트](./media/create-url-route-portal/application-gateway-iistest.png)

3. http://&lt;ip-address&gt;:8080/video/test.htm에서 &lt;ip-address&gt;를 사용자의 IP 주소로 대체하여 URL을 변경하면 다음 예제와 같은 내용이 표시됩니다.

    ![응용 프로그램 게이트웨이의 이미지 URL 테스트](./media/create-url-route-portal/application-gateway-iistest-images.png)

4. http://&lt;ip-address&gt;:8080/video/test.htm에서 &lt;ip-address&gt;를 사용자의 IP 주소로 대체하여 URL을 변경하면 다음 예제와 같은 내용이 표시됩니다.

    ![응용 프로그램 게이트웨이의 비디오 URL 테스트](./media/create-url-route-portal/application-gateway-iistest-video.png)

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 응용 프로그램 게이트웨이 만들기
> * 백 엔드 서버용 가상 머신 만들기
> * 백 엔드 서버로 백 엔드 풀 만들기
> * 백 엔드 수신기 만들기
> * 경로 기반 라우팅 규칙 만들기

응용 프로그램 게이트웨이 및 관련 리소스에 대해 자세히 알아보려면 방법 문서를 참조하세요.