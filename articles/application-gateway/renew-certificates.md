---
title: Azure Application Gateway 인증서 갱신
description: 응용 프로그램 게이트웨이 수신기와 연결된 인증서를 갱신하는 방법을 알아봅니다.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.date: 05/17/2018
ms.author: victorh
ms.openlocfilehash: b125f707e8de17764701e981736a53492e5e756c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34356946"
---
# <a name="renew-application-gateway-certificates"></a>Application Gateway 인증서 갱신

응용 프로그램 게이트웨이에 SSL 암호화를 구성한 경우 어느 시점이 되면 인증서를 갱신해야 합니다.

Azure Portal 또는 Azure PowerShell을 사용하여 수신기와 연결된 인증서를 갱신할 수 있습니다.

## <a name="azure-portal"></a>Azure portal

포털에서 수신기 인증서를 갱신하려면 응용 프로그램 게이트웨이 수신기로 이동합니다. 인증서를 갱신해야 하는 수신기를 클릭한 다음, **선택한 인증서 갱신 또는 편집**을 클릭합니다.

![인증서 갱신](media/renew-certificate/ssl-cert.png)

새 PFX 인증서를 업로드하고, 이름을 지정하고, 암호를 입력한 다음, **저장**을 클릭합니다.

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell을 사용하여 인증서를 갱신하려면 다음 cmdlet을 사용합니다.

```PowerShell
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-azureRmApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password
```

## <a name="next-steps"></a>다음 단계

Azure Application Gateway를 사용하여 SSL 오프로드를 구성하는 방법은 [SSL 오프로드 구성](application-gateway-ssl-portal.md)
