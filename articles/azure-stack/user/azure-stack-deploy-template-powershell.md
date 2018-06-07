---
title: Azure 스택에서 PowerShell을 사용 하 여 템플릿을 배포 | Microsoft Docs
description: PowerShell을 사용 하 여 Azure 스택에 서식 파일을 배포 합니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 12fe32d7-0a1a-4c02-835d-7b97f151ed0f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 4af82deef029120aa2699e7c69c501ae61a1e8bd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="deploy-a-template-to-azure-stack-using-powershell"></a>PowerShell을 사용 하 여 Azure 스택에 서식 파일을 배포 합니다.

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

PowerShell을 사용 하 여 Azure 스택에서 Azure 리소스 관리자 템플릿을 배포할 수 있습니다. 이 문서에서는 서식 파일을 배포 하려면 PowerShell을 사용 하는 방법을 보여 줍니다.

## <a name="run-azurerm-powershell-cmdlets"></a>AzureRM PowerShell cmdlet 실행

이 예제에서는 AzureRM PowerShell cmdlet 및 GitHub에 저장 된 템플릿을 사용 합니다. 이 템플릿은 Windows Server 2012 R2 Datacenter 가상 컴퓨터를 만듭니다.

>[!NOTE]
>이 예제를 시도 하기 전에 한 있습니다 있는지 확인 [PowerShell 구성](azure-stack-powershell-configure-user.md) Azure 스택 사용자에 대 한 합니다.

1. 로 이동 <http://aka.ms/AzureStackGitHub> 했는데는 **단순 windows vm 101** 템플릿. 이 위치에 템플릿을 저장: c:\\템플릿\\azuredeploy-101-단순-windows-vm.json 합니다.
2. 관리자 PowerShell 명령 프롬프트를 엽니다.
3. 대체 *username* 및 *암호* 사용자 이름 및 암호와 다음 스크립트를 실행 된 다음 스크립트에 있습니다.

   ```PowerShell
       # Set Deployment Variables
       $myNum = "001" #Modify this per deployment
       $RGName = "myRG$myNum"
       $myLocation = "local"
   
       # Create Resource Group for Template Deployment
       New-AzureRmResourceGroup -Name $RGName -Location $myLocation
   
       # Deploy Simple IaaS Template
       New-AzureRmResourceGroupDeployment `
           -Name myDeployment$myNum `
           -ResourceGroupName $RGName `
           -TemplateFile c:\templates\azuredeploy-101-simple-windows-vm.json `
           -NewStorageAccountName mystorage$myNum `
           -DnsNameForPublicIP mydns$myNum `
           -AdminUsername <username> `
           -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force) `
           -VmName myVM$myNum `
           -WindowsOSVersion 2012-R2-Datacenter
   ```

   >[!IMPORTANT]
   >이 스크립트를 실행할 때마다 배포를 덮어쓰지 않도록 "$myNum" 매개 변수 값이 증가 합니다.

4. Azure 스택 포털에서 선택 **찾아보기**를 선택한 후 **가상 컴퓨터** 새 가상 컴퓨터를 찾을 수 (*myDeployment001*).

## <a name="next-steps"></a>다음 단계

[Visual Studio를 사용하여 템플릿 배포](azure-stack-deploy-template-visual-studio.md)
