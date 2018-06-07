---
title: PowerShell을 사용하여 X.509 인증서를 만드는 방법 | Microsoft Docs
description: PowerShell을 사용하여 로컬에 X.509 인증서를 만들고 시뮬레이션된 환경의 Azure IoT Hub에서 X.509 기반 보안을 사용하는 방법을 설명합니다.
services: iot-hub
documentationcenter: ''
author: dsk-2015
manager: timlt
editor: ''
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/01/2018
ms.author: dkshir
ms.openlocfilehash: 656799c76a87870a19018849dbeffea3b12a356e
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="powershell-scripts-to-manage-ca-signed-x509-certificates"></a>CA 서명 X.509 인증서를 관리하는 PowerShell 스크립트

Azure IoT Hub에서 X.509 인증서 기반 보안을 사용하려면 루트 인증서는 물론 리프 인증서까지 중간 인증서를 포함하는 [X.509 인증서 체인](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification)을 시작해야 합니다. 이 *방법* 가이드에서는 [OpenSSL](https://www.openssl.org/)을 사용하여 X.509 인증서를 만들고 서명하는 샘플 PowerShell 스크립트를 살펴봅니다. 이 가이드에 나오는 여러 단계는 실제 제조 프로세스에서 발생하므로 이 가이드를 실험 용도로만 사용하는 것이 좋습니다. 이러한 인증서를 사용하여 *X.509 인증서 인증*을 사용하는 Azure IoT Hub에서 보안을 시뮬레이션할 수 있습니다. 이 가이드의 단계는 Windows 컴퓨터에 로컬로 인증서를 만듭니다. 

## <a name="prerequisites"></a>필수 조건
이 자습서에서는 사용자에게 OpenSSL 이진 파일이 있는 것으로 가정합니다. 사용자는 두 가지 방법 중 하나를 선택할 수 있습니다.
    - OpenSSL 소스 코드를 다운로드하여 컴퓨터에 이진 파일을 빌드합니다. 
    - [타사 OpenSSL 이진 파일](https://wiki.openssl.org/index.php/Binaries)을 다운로드하여 설치합니다(예: [SourceForge의 이 프로젝트](https://sourceforge.net/projects/openssl/)).

<a id="createcerts"></a>

## <a name="create-x509-certificates"></a>X.509 인증서 만들기
다음 단계에서는 로컬로 X.509 루트 인증서를 만드는 방법의 예를 보여줍니다. 

1. 관리자 권한으로 *PowerShell* 창을 엽니다.  
   **참고:** PowerShell ISE, Visual Studio Code 또는 기본 PowerShell 콘솔을 래핑하는 기타 도구가 아닌 PowerShell 자체에서 열어야 합니다.  비-콘솔 기반 PowerShell을 사용하면 아래의 `openssl` 명령이 중지됩니다.

2. 작업 디렉터리로 이동합니다. 다음 스크립트를 실행하여 전역 변수를 설정합니다. 
    ```PowerShell
    $openSSLBinSource = "<full_path_to_the_binaries>\OpenSSL\bin"
    $errorActionPreference    = "stop"

    # Note that these values are for test purpose only
    $_rootCertCommonName      = "Azure IoT Root CA"
    $_rootCertSubject         = "CN=$_rootCertCommonName"
    $_intermediateCertSubject = "Azure IoT Intermediate {0} CA"
    $_privateKeyPassword      = "123"

    $rootCACerFileName          = "./RootCA.cer"
    $rootCAPemFileName          = "./RootCA.pem"
    $intermediate1CAPemFileName = "./Intermediate1.pem"
    $intermediate2CAPemFileName = "./Intermediate2.pem"
    $intermediate3CAPemFileName = "./Intermediate3.pem"

    $openSSLBinDir              = Join-Path $ENV:TEMP "openssl-bin"

    # Whether to use ECC or RSA.
    $useEcc                     = $true
    ```
3. OpenSSL 이진 파일을 작업 디렉터리로 복사하고 환경 변수를 설정하는 다음 스크립트를 실행합니다.

    ```PowerShell
    function Initialize-CAOpenSSL()
    {
        Write-Host ("Beginning copy of openssl binaries to {0} (and setting up env variables...)" -f $openSSLBinDir)
        if (-not (Test-Path $openSSLBinDir))
        {
            mkdir $openSSLBinDir | Out-Null
        }

        robocopy $openSSLBinSource $openSSLBinDir * /s 
        robocopy $openSSLBinSource . * /s 

        Write-Host "Setting up PATH and other environment variables."
        $ENV:PATH += "; $openSSLBinDir"
        $ENV:OPENSSL_CONF = Join-Path $openSSLBinDir "openssl.cnf"

        Write-Host "Success"
    }
    Initialize-CAOpenSSL
    ```
4. 다음으로, 지정된 *주체 이름*별로 인증서가 설치되어 있는지, 컴퓨터에서 OpenSSL이 올바르게 구성되어 있는지 검색하는 다음 스크립트를 실행합니다.
    ```PowerShell
    function Get-CACertBySubjectName([string]$subjectName)
    {
        $certificates = gci -Recurse Cert:\LocalMachine\ |? { $_.gettype().name -eq "X509Certificate2" }
        $cert = $certificates |? { $_.subject -eq $subjectName -and $_.PSParentPath -eq "Microsoft.PowerShell.Security\Certificate::LocalMachine\My" }
        if ($NULL -eq $cert)
        {
            throw ("Unable to find certificate with subjectName {0}" -f $subjectName)
        }
    
        write $cert
    }
    function Test-CAPrerequisites()
    {
        $certInstalled = $null
        try
        {
            $certInstalled = Get-CACertBySubjectName $_rootCertSubject
        }
        catch {}

        if ($NULL -ne $certInstalled)
        {
            throw ("Certificate {0} already installed.  Cleanup CA certs 1st" -f $_rootCertSubject)
        }

        if ($NULL -eq $ENV:OPENSSL_CONF)
        {
            throw ("OpenSSL not configured on this system.  Run 'Initialize-CAOpenSSL' (even if you've already done so) to set everything up.")
        }
        Write-Host "Success"
    }
    Test-CAPrerequisites
    ```
    모든 것이 올바르게 구성되어 있으면 "성공" 메시지가 표시됩니다. 

<a id="createcertchain"></a>

## <a name="create-x509-certificate-chain"></a>X.509 인증서 체인 만들기
루트 CA를 사용하여 인증서 체인을 만듭니다. 예를 들어 이 샘플에서는 다음 PowerShell 스크립트를 실행하여 "CN=Azure IoT Root CA"를 사용합니다. 또한 이 스크립트는 Windows OS 인증서 저장소를 업데이트하고 작업 디렉터리에 인증서 파일을 만듭니다. 
    1. 다음 스크립트는 지정된 *주체 이름* 및 서명 기관에 대한 자체 서명된 인증서를 만드는 PowerShell 함수를 작성합니다. 
    ```PowerShell
    function New-CASelfsignedCertificate([string]$commonName, [object]$signingCert, [bool]$isASigner=$true)
    {
        # Build up argument list
        $selfSignedArgs =@{"-DnsName"=$commonName; 
                           "-CertStoreLocation"="cert:\LocalMachine\My";
                           "-NotAfter"=(get-date).AddDays(30); 
                          }

        if ($isASigner -eq $true)
        {
            $selfSignedArgs += @{"-KeyUsage"="CertSign"; }
            $selfSignedArgs += @{"-TextExtension"= @(("2.5.29.19={text}ca=TRUE&pathlength=12")); }
        }
        else
        {
            $selfSignedArgs += @{"-TextExtension"= @("2.5.29.37={text}1.3.6.1.5.5.7.3.2,1.3.6.1.5.5.7.3.1", "2.5.29.19={text}ca=FALSE&pathlength=0")  }
        }

        if ($signingCert -ne $null)
        {
            $selfSignedArgs += @{"-Signer"=$signingCert }
        }

        if ($useEcc -eq $true)
        {
            $selfSignedArgs += @{"-KeyAlgorithm"="ECDSA_nistP256";
                             "-CurveExport"="CurveName" }
        }

        # Now use splatting to process this
        Write-Host ("Generating certificate {0} which is for prototyping, NOT PRODUCTION.  It will expire in 30 days." -f $subjectName)
        write (New-SelfSignedCertificate @selfSignedArgs)
    }
    ``` 
    2. 다음 PowerShell 함수는 위의 함수와 OpenSSL 이진 파일을 사용하여 중간 X.509 인증서를 만듭니다. 
    ```PowerShell
    function New-CAIntermediateCert([string]$commonName, [Microsoft.CertificateServices.Commands.Certificate]$signingCert, [string]$pemFileName)
    {
        $certFileName = ($commonName + ".cer")
        $newCert = New-CASelfsignedCertificate $commonName $signingCert
        Export-Certificate -Cert $newCert -FilePath $certFileName -Type CERT | Out-Null
        Import-Certificate -CertStoreLocation "cert:\LocalMachine\CA" -FilePath $certFileName | Out-Null

        # Store public PEM for later chaining
        openssl x509 -inform deer -in $certFileName -out $pemFileName

        del $certFileName
   
        write $newCert
    }  
    ```
    3. 다음 PowerShell 함수는 X.509 인증서 체인을 만듭니다. 자세한 내용은 [인증서 체인](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification)을 참조하세요.
    ```PowerShell
    function New-CACertChain()
    {
        Write-Host "Beginning to install certificate chain to your LocalMachine\My store"
        $rootCACert =  New-CASelfsignedCertificate $_rootCertSubject $null
    
        Export-Certificate -Cert $rootCACert -FilePath $rootCACerFileName  -Type CERT
        Import-Certificate -CertStoreLocation "cert:\LocalMachine\Root" -FilePath $rootCACerFileName

        openssl x509 -inform der -in $rootCACerFileName -out $rootCAPemFileName

        $intermediateCert1 = New-CAIntermediateCert ($_intermediateCertSubject -f "1") $rootCACert $intermediate1CAPemFileName
        $intermediateCert2 = New-CAIntermediateCert ($_intermediateCertSubject -f "2") $intermediateCert1 $intermediate2CAPemFileName
        $intermediateCert3 = New-CAIntermediateCert ($_intermediateCertSubject -f "3") $intermediateCert2 $intermediate3CAPemFileName
        Write-Host "Success"
    }    
    ```
    이 스크립트는 작업 디렉터리에 *RootCA.cer*이라는 파일을 만듭니다. 
    4. 마지막으로, PowerShell 창에서 `New-CACertChain` 명령을 실행하여 위의 PowerShell 함수로 X.509 인증서 체인을 만듭니다. 


<a id="signverificationcode"></a>

## <a name="proof-of-possession-of-your-x509-ca-certificate"></a>X.509 CA 인증서의 소유권 증명

이 스크립트는 X.509 인증서에 대한 *소유 증명* 흐름을 수행합니다. 

데스크톱의 PowerShell 창에서 다음 코드를 실행합니다.
   
   ```PowerShell
   function New-CAVerificationCert([string]$requestedSubjectName)
   {
       $verifyRequestedFileName = ".\verifyCert4.cer"
       $rootCACert = Get-CACertBySubjectName $_rootCertSubject
       Write-Host "Using Signing Cert:::" 
       Write-Host $rootCACert
   
       $verifyCert = New-CASelfsignedCertificate $requestedSubjectName $rootCACert $false

       Export-Certificate -cert $verifyCert -filePath $verifyRequestedFileName -Type Cert
       if (-not (Test-Path $verifyRequestedFileName))
       {
           throw ("Error: CERT file {0} doesn't exist" -f $verifyRequestedFileName)
       }
   
       Write-Host ("Certificate with subject {0} has been output to {1}" -f $requestedSubjectName, (Join-Path (get-location).path $verifyRequestedFileName)) 
   }
   New-CAVerificationCert "<your verification code>"
   ```

이 코드는 지정된 주체 이름을 사용하여 CA의 서명을 받은 인증서를 작업 디렉터리에 *VerifyCert4.cer*라는 파일로 생성합니다. 이 인증서 파일은 이 CA의 서명 권한(즉, 개인 키)이 사용자에게 있는 IoT Hub의 유효성을 검사하는 데 도움이 됩니다.


<a id="createx509device"></a>

## <a name="create-leaf-x509-certificate-for-your-device"></a>장치에 대한 리프 X.509 인증서 만들기

이 섹션에서는 리프 장치 인증서와 해당 인증서 체인을 만드는 PowerShell 스크립트 사용 방법을 보여줍니다. 

로컬 컴퓨터의 PowerShell 창에서 이 장치에 대한 CA 서명 X.509 인증서를 만드는 다음 스크립트를 실행합니다.

   ```PowerShell
   function New-CADevice([string]$deviceName, [string]$signingCertSubject=$_rootCertSubject)
   {
       $newDevicePfxFileName = ("./{0}.pfx" -f $deviceName)
       $newDevicePemAllFileName      = ("./{0}-all.pem" -f $deviceName)
       $newDevicePemPrivateFileName  = ("./{0}-private.pem" -f $deviceName)
       $newDevicePemPublicFileName   = ("./{0}-public.pem" -f $deviceName)
   
       $signingCert = Get-CACertBySubjectName $signingCertSubject ## "CN=Azure IoT CA Intermediate 1 CA"

       $newDeviceCertPfx = New-CASelfSignedCertificate $deviceName $signingCert $false
   
       $certSecureStringPwd = ConvertTo-SecureString -String $_privateKeyPassword -Force -AsPlainText

       # Export the PFX of the cert we've just created.  The PFX is a format that contains both public and private keys.
       Export-PFXCertificate -cert $newDeviceCertPfx -filePath $newDevicePfxFileName -password $certSecureStringPwd
       if (-not (Test-Path $newDevicePfxFileName))
       {
           throw ("Error: CERT file {0} doesn't exist" -f $newDevicePfxFileName)
       }

       # Begin the massaging.  First, turn the PFX into a PEM file which contains public key, private key, and other attributes.
       Write-Host ("When prompted for password by openssl, enter the password as {0}" -f $_privateKeyPassword)
       openssl pkcs12 -in $newDevicePfxFileName -out $newDevicePemAllFileName -nodes

       # Convert the PEM to get formats we can process
       if ($useEcc -eq $true)
       {
           openssl ec -in $newDevicePemAllFileName -out $newDevicePemPrivateFileName
       }
       else
       {
           openssl rsa -in $newDevicePemAllFileName -out $newDevicePemPrivateFileName
       }
       openssl x509 -in $newDevicePemAllFileName -out $newDevicePemPublicFileName
 
       Write-Host ("Certificate with subject {0} has been output to {1}" -f $cnNewDeviceSubjectName, (Join-Path (get-location).path $newDevicePemPublicFileName)) 
   }
   ```

그런 다음 PowerShell 창에서 `New-CADevice "<yourTestDevice>"` 명령을 실행하고, 장치를 만들 때 사용한 친숙한 이름을 사용합니다. CA의 개인 키 암호를 묻는 메시지가 표시되면 "123"을 입력합니다. 그러면 작업 디렉터리에 _<yourTestDevice>.pfx_ 파일이 생성됩니다.

## <a name="clean-up-certificates"></a>인증서 정리

시작 표시줄 또는 **설정** 앱에서 **컴퓨터 인증서 관리**를 검색하고 선택합니다. **Azure IoT CA TestOnly***에서 발급한 인증서를 제거합니다. 이러한 인증서는 다음 세 위치에 있어야 합니다. 

* 인증서 - 로컬 컴퓨터 > 개인 > 인증서
* 인증서 - 로컬 컴퓨터 > 신뢰할 수 있는 루트 인증 기관 > 인증서
* 인증서 - 로컬 컴퓨터 > 중간 인증 기관 > 인증서

   ![Azure IoT CA TestOnly 인증서 제거](./media/iot-hub-security-x509-create-certificates/cleanup.png)
