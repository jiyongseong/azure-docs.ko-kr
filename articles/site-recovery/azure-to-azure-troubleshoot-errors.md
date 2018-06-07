---
title: Azure Site Recovery의 Azure 간 복제 문제 및 오류 해결 | Microsoft Docs
description: 재해 복구를 위해 Azure 가상 머신을 복제할 때 오류 및 문제 해결
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: sujayt
ms.openlocfilehash: e6f694e601c506adae217e6348b4bf388cc24390
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="troubleshoot-azure-to-azure-vm-replication-issues"></a>Azure 간 VM 복제 문제 해결

이 문서에서는 지역 간에 Azure 가상 머신을 복제 및 복구할 때 Azure Site Recovery에서 발생하는 일반적인 문제와 해결 방법을 설명합니다. 지원되는 구성에 대한 자세한 내용은 [Azure VM을 복제하기 위한 지원 매트릭스](site-recovery-support-matrix-azure-to-azure.md)를 참조하세요.

## <a name="azure-resource-quota-issues-error-code-150097"></a>Azure 리소스 할당량 문제(오류 코드 150097)
재해 복구 지역으로 사용할 대상 위치에 Azure VM을 만들려면 구독이 사용되도록 설정되어 있어야 합니다. 또한 구독은 특정 크기의 VM을 만들 수 있도록 할당량이 충분해야 합니다. 기본적으로 Site Recovery는 대상 VM에 대해 원본 VM과 동일한 크기를 선택합니다. 일치하는 크기를 사용할 수 없는 경우 가능한 가장 근접한 크기가 자동으로 선택됩니다. 원본 VM 구성을 지원하는 일치하는 크기가 없는 경우 다음 오류 메시지가 나타납니다.

**오류 코드** | **가능한 원인** | **권장 사항**
--- | --- | ---
150097<br></br>**메시지**: 가상 머신 VmName에 대해 복제를 사용하도록 설정할 수 없습니다. | - 대상 영역 위치에 VM을 만드는 구독 ID가 사용하도록 설정되어 있지 않을 수 있습니다.</br></br>- 대상 지역 위치에 특정 VM 크기를 만드는 구독 ID가 사용하도록 설정되어 있지 않거나 할당량이 충분하지 않습니다.</br></br>- 대상 지역 위치에서 구독 ID에 대해 원본 VM NIC 수(2)와 일치하는 적합한 대상 VM 크기를 찾을 수 없습니다.| [Azure 청구 지원](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)에 문의하여 대상 위치에서 구독에 대해 필요한 VM 크기의 VM 만들기를 사용하도록 설정할 수 있습니다. 사용하도록 설정되면 실패한 작업은 다시 시도하세요.

### <a name="fix-the-problem"></a>문제 해결
[Azure 청구 지원](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)에 문의하여 구독을 사용하도록 설정하면 대상 위치에 필요한 크기의 VM을 만들 수 있습니다.

대상 위치에 용량 제약 조건이 있으면 복제를 사용하지 않도록 설정하고 필요한 크기의 VM을 만들기에 충분한 할당량이 구독에 있는 다른 위치에 대해 복제를 사용하도록 설정합니다.

## <a name="trusted-root-certificates-error-code-151066"></a>신뢰할 수 있는 루트 인증서(오류 코드 151066)

최신의 신뢰할 수 있는 루트 인증서 중 일부만 VM에 있는 경우 "복제를 사용하도록 설정"하는 작업이 실패할 수 있습니다. 인증서가 없으면 VM에서 Site Recovery 서비스 호출의 인증 및 권한 부여가 실패합니다. 실패한 "복제를 사용하도록 설정"하는 Site Recovery 작업에 대한 오류 메시지가 나타납니다.

**오류 코드** | **가능한 원인** | **권장 사항**
--- | --- | ---
151066<br></br>**메시지**: Site Recovery 구성이 실패했습니다. | 권한 부여 및 인증에 사용되는 신뢰할 수 있는 필요한 루트 인증서가 시스템에 없습니다. | - Windows 운영 체제를 실행하는 VM의 경우 신뢰할 수 있는 루트 인증서가 컴퓨터에 있는지 확인합니다. 자세한 내용은 [신뢰할 수 있는 루트 및 허용되지 않는 인증서 구성](https://technet.microsoft.com/library/dn265983.aspx)을 참조하세요.<br></br>- Linux 운영 체제를 실행하는 VM의 경우 Linux 운영 체제 버전 배포자가 게시한 신뢰할 수 있는 루트 인증서에 대한 지침을 따릅니다.

### <a name="fix-the-problem"></a>문제 해결
**Windows**

모든 신뢰할 수 있는 루트 인증서가 컴퓨터에 있도록 VM에서 모든 최신 Windows 업데이트를 설치합니다. 연결이 끊어진 환경에 있는 경우 인증서를 받는 조직에서 표준 Windows 업데이트 프로세스를 따릅니다. 필요한 인증서가 VM에 없는 경우 보안상의 이유로 Site Recovery 서비스에 대한 호출이 실패합니다.

조직의 일반적인 Windows 업데이트 관리 또는 인증서 업데이트 관리 프로세스에 따라 VM의 모든 최신 루트 인증서와 업데이트된 인증서 해지 목록을 가져옵니다.

문제가 해결되었는지 확인하려면 VM의 브라우저에서 login.microsoftonline.com으로 이동합니다.

**Linux**

Linux 배포자가 제공한 지침에 따라 VM의 신뢰할 수 있는 최신 루트 인증서 및 최신 인증서 해지 목록을 가져옵니다.

SuSE Linux는 symlink를 사용하여 인증서 목록을 유지 관리하므로 다음 단계를 따릅니다.

1.  루트 사용자로 로그인합니다.

2.  디렉터리를 변경하려면 이 명령을 실행합니다.

      ``# cd /etc/ssl/certs``

3. Symantec 루트 CA 인증서가 있는지 확인합니다.

      ``# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

4. Symantec 루트 CA 인증서를 찾을 수 없으면 다음 명령을 실행하여 파일을 다운로드합니다. 모든 오류를 확인하고 네트워크 오류에 대한 권장 조치를 수행합니다.

      ``# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

5. Baltimore 루트 CA 인증서가 있는지 확인합니다.

      ``# ls Baltimore_CyberTrust_Root.pem``

6. Baltimore 루트 CA 인증서를 찾을 수 없으면 인증서를 다운로드합니다.  

    ``# wget http://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem``

7. DigiCert_Global_Root_CA 인증서가 있는지 확인합니다.

    ``# ls DigiCert_Global_Root_CA.pem``

8. DigiCert_Global_Root_CA 인증서를 찾을 수 없으면 다음 명령을 실행하여 인증서를 다운로드합니다.

    ``# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt``

    ``# openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem``

9. 다시 해시 스크립트를 실행하여 새로 다운로드한 인증서에 대한 인증서 주체 해시를 업데이트합니다.

    ``# c_rehash``

10. 인증서에 대한 주체 해시가 symlink로 생성되는지 확인합니다.

    - 명령

      ``# ls -l | grep Baltimore``

    - 출력

      ``lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem
      -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem``

    - 명령

      ``# ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5``

    - 출력

      ``-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
      lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

    - 명령

      ``# ls -l | grep DigiCert_Global_Root``

    - 출력

      ``lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem
      -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem``

11. 파일 이름을 b204d74a.0으로 하여 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem 파일 복사본 만들기

    ``# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0``

12. 파일 이름을 653b494a.0으로 하여 Baltimore_CyberTrust_Root.pem 파일 복사본 만들기

    ``# cp Baltimore_CyberTrust_Root.pem 653b494a.0``

13. 파일 이름을 3513523f.0으로 하여 DigiCert_Global_Root_CA.pem 파일 복사본 만들기

    ``# cp DigiCert_Global_Root_CA.pem 3513523f.0``  


14. 파일이 있는지 확인합니다.  

    - 명령

      ``# ls -l 653b494a.0 b204d74a.0 3513523f.0``

    - 출력

      ``-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0
      -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0
      -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0``


## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Site Recovery URL 또는 IP 범위에 대한 아웃바운드 연결(오류 코드 151037 또는 151072)

Site Recovery 복제가 작동하려면 VM에서 특정 URL 또는 IP 범위에 대한 아웃바운드 연결이 필요합니다. VM이 방화벽 뒤에 있거나 NSG(네트워크 보안 그룹) 규칙을 사용하여 아웃바운드 연결을 제어하는 경우 이러한 오류 메시지 중 하나가 표시될 수 있습니다.

**오류 코드** | **가능한 원인** | **권장 사항**
--- | --- | ---
151037<br></br>**메시지**: Site Recovery에 Azure 가상 머신을 등록하지 못했습니다. | - VM에서 NSG를 사용하여 아웃바운드 액세스를 제어하고 있으며 아웃바운드 액세스에 필요한 IP 범위가 허용 목록에 없습니다.</br></br>- 타사 방화벽 도구를 사용하고 있으며 필요한 IP 범위/URL이 허용 목록에 없습니다.</br>| - 방화벽 프록시를 사용하여 VM에서 아웃바운드 네트워크 연결을 제어하는 경우 필수 구성 요소 URL 또는 데이터 센터 IP 범위가 허용 목록에 있는지 확인합니다. 자세한 내용은 [방화벽 프록시 지침](https://aka.ms/a2a-firewall-proxy-guidance)을 참조하세요.</br></br>- NSG 규칙을 사용하여 VM에서 아웃바운드 네트워크 연결을 제어하는 경우 필수 구성 요소 데이터 센터 IP 범위가 허용 목록에 있는지 확인합니다. 자세한 내용은 [네트워크 보안 그룹 지침](https://aka.ms/a2a-nsg-guidance)을 참조하세요.
151072<br></br>**메시지**: Site Recovery 구성이 실패했습니다. | Site Recovery 서비스 끝점에 연결할 수 없습니다. | - 방화벽 프록시를 사용하여 VM에서 아웃바운드 네트워크 연결을 제어하는 경우 필수 구성 요소 URL 또는 데이터 센터 IP 범위가 허용 목록에 있는지 확인합니다. 자세한 내용은 [방화벽 프록시 지침](https://aka.ms/a2a-firewall-proxy-guidance)을 참조하세요.</br></br>- NSG 규칙을 사용하여 VM에서 아웃바운드 네트워크 연결을 제어하는 경우 필수 구성 요소 데이터 센터 IP 범위가 허용 목록에 있는지 확인합니다. 자세한 내용은 [네트워크 보안 그룹 지침](https://aka.ms/a2a-nsg-guidance)을 참조하세요.

### <a name="fix-the-problem"></a>문제 해결
[필요한 URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) 또는 [필요한 IP 범위](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges)가 허용 목록에 있도록 하려면 [네트워킹 지침 문서](site-recovery-azure-to-azure-networking-guidance.md)의 단계에 따릅니다.

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>컴퓨터에서 디스크를 찾을 수 없음(오류 코드 150039)

VM에 연결된 새 디스크는 초기화되어야 합니다.

**오류 코드** | **가능한 원인** | **권장 사항**
--- | --- | ---
150039<br></br>**메시지**: LUN(논리 단위 번호)이 (LUNValue)인 Azure 데이터 디스크(DiskName) (DiskURI)이(가) LUN 값이 동일한 VM 내에서 보고되는 해당 디스크에 매핑되지 않았습니다. | - 새 데이터 디스크가 VM에 연결되었지만 초기화되지 않았습니다.</br></br>- VM 내의 데이터 디스크는 디스크가 VM에 연결된 LUN 값을 올바르게 보고하지 않습니다.| 데이터 디스크가 초기화되었는지 확인하고 작업을 다시 시도합니다.</br></br>Windows의 경우: [새 디스크 연결 및 초기화](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal)를 수행합니다.</br></br>Linux의 경우: [Linux에서 새 데이터 디스크 초기화](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk)를 수행합니다.

### <a name="fix-the-problem"></a>문제 해결
데이터 디스크가 초기화되었는지 확인하고 작업을 다시 시도합니다.

- Windows의 경우: [새 디스크 연결 및 초기화](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal)를 수행합니다.
- Linux의 경우: [Linux에 새 데이터 디스크를 추가](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk)합니다.

문제가 계속되면 지원에 문의하세요.


## <a name="unable-to-see-the-azure-vm-for-selection-in-enable-replication"></a>"복제를 사용하도록 설정"에서 선택할 Azure VM을 표시할 수 없음

복제를 사용하려는 VM이 보이지 않는 경우 Azure VM에 남아 있는 부실한 Site Recovery 구성이 그 원인일 수 있습니다. 다음과 같은 경우 Azure VM에 부실 구성이 남겨질 수 있습니다.

- Site Recovery를 사용하여 Azure VM에 대해 복제를 사용하도록 설정한 후 VM에서 명시적으로 복제를 사용하지 않도록 설정하지 않으면서 Site Recovery 자격 증명 모음을 삭제했습니다.
- Site Recovery를 사용하여 Azure VM에 대해 복제를 사용하도록 설정한 후 VM에서 명시적으로 복제를 사용하지 않도록 설정하지 않으면서 Site Recovery 자격 증명 모음이 포함된 리소스 그룹을 삭제했습니다.

### <a name="fix-the-problem"></a>문제 해결

[부실 ASR 구성 스크립트를 제거](https://gallery.technet.microsoft.com/Azure-Recovery-ASR-script-3a93f412)하고 Azure VM의 부실 Site Recovery 구성을 제거할 수 있습니다. 부실 구성을 제거하면 VM이 보일 것입니다.

## <a name="vms-provisioning-state-is-not-valid-error-code-150019"></a>VM의 프로비전 상태가 잘못되었습니다(오류 코드 150019).

VM에서 복제를 사용하도록 설정하려면 프로비전 상태가 **성공**이어야 합니다. 다음 단계를 수행하여 VM 상태를 확인할 수 있습니다.

1.  Azure Portal의 **모든 서비스**에서 **리소스 탐색기**를 선택합니다.
2.  **구독** 목록을 확장하고 본인의 구독을 선택합니다.
3.  **리소스 그룹** 목록을 확장하고 VM의 리소스 그룹을 선택합니다.
4.  **리소스** 목록을 확장하고 사용하는 가상 머신을 선택합니다.
5.  오른쪽의 인스턴스 보기에서 **provisioningState** 필드를 선택합니다.

### <a name="fix-the-problem"></a>문제 해결

- **provisioningState**가 **실패**이면 지원 서비스에 문제 해결을 위한 세부 정보를 문의합니다.
- **provisioningState**가 **업데이트 중**이면 또 다른 확장을 배포할 수 있습니다. VM에서 계속 진행되는 작업이 있는지 확인한 후, 해당 작업이 완료될 때까지 기다렸다가 실패한 Site Recovery **복제 사용** 작업을 다시 시도합니다.


## <a name="comvolume-shadow-copy-service-error-error-code-151025"></a>COM+/볼륨 섀도 복사본 서비스 오류(오류 코드 151025)
**오류 코드** | **가능한 원인** | **권장 사항**
--- | --- | ---
151025<br></br>**메시지**: 사이트 복구 확장 설치 실패 | - 'COM+ 시스템 응용 프로그램' 서비스가 비활성화되었습니다.</br></br>- '볼륨 섀도 복사본' 서비스가 비활성화되었습니다.| 'COM+ 시스템 응용 프로그램' 및 '볼륨 섀도 복사본' 서비스를 자동 또는 수동 시작 모드로 설정하세요.

### <a name="fix-the-problem"></a>문제 해결

'서비스' 콘솔을 열고, 'COM+ 시스템 응용 프로그램' 및 '볼륨 섀도 복사본'이 '시작 유형'에 대해 '사용 안 함'으로 설정되지 않았는지 확인합니다.
  ![com-error](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="next-steps"></a>다음 단계
[Azure 가상 머신 복제](site-recovery-replicate-azure-to-azure.md)
