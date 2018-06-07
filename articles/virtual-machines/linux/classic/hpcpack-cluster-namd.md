---
title: Linux VM에서 Microsoft HPC 팩으로 NAMD 실행 | Microsoft Docs
description: Azure에서 Microsoft HPC 팩을 배포하고 여러 Linux 컴퓨터 노드에서 charmrun으로 NAMD 시뮬레이션을 실행합니다.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: 76072c6b-ac35-4729-ba67-0d16f9443bd7
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 10/13/2016
ms.author: danlep
ms.openlocfilehash: 61dd49d4bd3183b6b9a78036d6d7d01798e4dc89
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="run-namd-with-microsoft-hpc-pack-on-linux-compute-nodes-in-azure"></a>Azure의 Linux 계산 노드에서 Microsoft HPC 팩을 사용하여 NAMD 실행
이 문서는 Azure Virtual Machines에서 Linux HPC(고성능 컴퓨팅) 워크로드를 실행하는 한 가지 방법을 보여줍니다. 여기서는 Linux 계산 노드를 사용하여 Azure에 [Microsoft HPC 팩](https://technet.microsoft.com/library/cc514029) 클러스터를 설정하고 [NAMD](http://www.ks.uiuc.edu/Research/namd/) 시뮬레이션을 실행하여 규모가 큰 생체 분자 시스템을 계산하고 시각화합니다.  

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

* **NAMD**(Nanoscale Molecular Dynamics 프로그램용)는 수백 만 원자를 포함하는 규모가 큰 생체 분자 시스템의 고성능 시뮬레이션을 위해 설계된 병렬 분자 동적 패키지입니다. 이러한 시스템의 예로는 바이러스, 세포 구조, 거대한 단백질 등이 있습니다. NAMD는 일반적인 시뮬레이션의 경우 수백 개의 코어로 확장되며 가장 큰 규모의 시뮬레이션의 경우 500,000 코어 이상까지 확장됩니다.
* **Microsoft HPC 팩**은 온-프레미스 컴퓨터 또는 Azure Virtual Machines의 클러스터에서 대규모 HPC 및 병렬 응용 프로그램을 실행할 수 있는 기능을 제공합니다. 원래 Windows HPC 워크로드용 솔루션으로 개발된 HPC 팩은 이제 HPC 팩 클러스터에 배포된 Linux 계산 노드 VM에서 Linux HPC 응용 프로그램의 실행도 지원합니다. 소개는 [Azure에서 HPC Pack 클러스터의 Linux 계산 노드 시작](hpcpack-cluster.md) 을 참조하세요.

## <a name="prerequisites"></a>필수 조건
* **Linux 계산 노드가 포함된 HPC 팩 클러스터** - [Azure Resource Manager 템플릿](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) 또는 [Azure PowerShell 스크립트](hpcpack-cluster-powershell-script.md)를 사용하여 Azure에서 Linux 계산 노드가 포함된 HPC 팩 클러스터를 배포합니다. 각 옵션 사용 시의 필수 구성 요소 및 단계는 [Azure에서 HPC 팩 클러스터의 Linux 계산 노드 시작](hpcpack-cluster.md) 을 참조하세요. PowerShell 스크립트 배포 옵션을 선택하는 경우 이 문서 끝에 나오는 샘플 파일의 샘플 구성 파일을 참조하세요. 이 파일은 Windows Server 2012 R2 헤드 노드 및 4개 크기의 대형 CentOS 6.6 계산 노드로 구성된 Azure 기반 HPC 팩 클러스터를 구성합니다. 이 파일을 환경에 맞게 사용자 지정합니다.
* **NAMD 소프트웨어 및 자습서 파일** - [NAMD](http://www.ks.uiuc.edu/Research/namd/) 사이트(등록 필요)에서 Linux용 NAMD 소프트웨어를 다운로드합니다. 이 문서는 NAMD 버전 2.10을 기반으로 하며 [Linux-x86_64 (64-bit Intel/AMD with Ethernet)](http://www.ks.uiuc.edu/Development/Download/download.cgi?UserID=&AccessCode=&ArchiveID=1310) 아카이브를 사용합니다. [NAMD 자습서 파일](http://www.ks.uiuc.edu/Training/Tutorials/#namd)도 다운로드합니다. 다운로드는 .tar 파일이며 클러스터 헤드 노드에 파일을 추출하기 위한 Windows 도구가 필요합니다. 파일을 추출하려면 하려면 이 문서 뒷부분의 지침을 따릅니다. 
* **VMD** (선택 사항) - NAMD 작업 결과를 보려면 선택한 컴퓨터에 분자 시각화 프로그램인 [VMD](http://www.ks.uiuc.edu/Research/vmd/) 를 다운로드하여 설치합니다. 현재 버전은 1.9.2입니다. 시작하려면 VMD 다운로드 사이트를 참조하세요.  

## <a name="set-up-mutual-trust-between-compute-nodes"></a>계산 노드 간 상호 트러스트 설정
여러 Linux 노드에서 크로스 노드 작업을 실행하려면 노드가 서로 신뢰해야 합니다(**rsh** 또는 **ssh** 사용). Microsoft HPC 팩 IaaS 배포 스크립트로 HPC 팩 클러스터를 만드는 경우 스크립트에서 사용자가 지정한 관리자 계정에 대해 영구 상호 트러스트를 자동으로 설정합니다. 클러스터의 도메인에 만든 관리자가 아닌 사용자의 경우 작업이 할당될 때 노드 간에 임시 상호 트러스트를 설정합니다. 그런 다음 작업이 완료된 후 관계를 삭제합니다. 사용자마다 이 작업을 수행하려면 HPC 팩에서 트러스트 관계를 설정하는 데 사용할 클러스터에 RSA 키 쌍을 제공합니다. 지침이 제공됩니다.

### <a name="generate-an-rsa-key-pair"></a>RSA 키 쌍 생성
Linux **ssh-keygen** 명령을 실행하여 공개 키 및 개인 키를 포함하는 RSA 키 쌍을 간편하게 생성할 수 있습니다.

1. Linux 컴퓨터에 로그온합니다.
2. 다음 명령 실행:
   
   ```bash
   ssh-keygen -t rsa
   ```
   
   > [!NOTE]
   > 명령이 완료될 때까지 기본 설정을 사용하려면 **Enter** 키를 누릅니다. 여기에 암호를 입력하지 마십시오. 암호를 묻는 메시지가 표시되면 **Enter** 키만 누르십시오.
   > 
   > 
   
   ![RSA 키 쌍 생성][keygen]
3. 디렉터리를 ~/.ssh 디렉터리로 변경합니다. 개인 키는 id_rsa에 저장되고 공개 키는 id_rsa.pub에 저장됩니다.
   
   ![개인 및 공용 키][keys]

### <a name="add-the-key-pair-to-the-hpc-pack-cluster"></a>HPC 팩 클러스터에 키 쌍 추가
1. 클러스터를 배포할 때 제공한 도메인 자격 증명(예: hpc\clusteradmin)을 사용하여 [원격 데스크톱을 통해 헤드 노드에 연결](../../windows/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)합니다. 헤드 노드에서 클러스터를 관리합니다.
2. 표준 Windows Server 절차를 사용하여 클러스터의 Active Directory 도메인에 도메인 사용자 계정을 만듭니다. 예를 들어 헤드 노드에서 Active Directory 사용자 및 컴퓨터 도구를 사용합니다. 이 문서의 예에서는 hpclab 도메인에 hpcuser라는 도메인 사용자를 만든다고 가정합니다(hpclab\hpcuser).
3. HPC 팩 클러스터에 도메인 사용자를 클러스터 사용자로 추가합니다. 지침은 [클러스터 사용자 추가 또는 제거](https://technet.microsoft.com/library/ff919330.aspx)를 참조하세요.
4. C:\cred.xml이라는 이름의 파일을 만들고 RSA 키 데이터를 여기에 복사합니다. 이 문서의 끝에 있는 샘플 파일에서 예제를 확인할 수 있습니다.
   
   ```
   <ExtendedData>
     <PrivateKey>Copy the contents of private key here</PrivateKey>
     <PublicKey>Copy the contents of public key here</PublicKey>
   </ExtendedData>
   ```
5. 명령 프롬프트를 열고 다음 명령을 입력하여 hpclab\hpcuser 계정에 대한 자격 증명 데이터를 설정합니다. 키 데이터를 위해 만든 C:\cred.xml 파일의 이름을 전달하는 데 **extendeddata** 매개 변수를 사용합니다.
   
   ```command
   hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
   ```
   
   이 명령은 출력 없이 성공적으로 완료됩니다. 작업을 실행하는 데 필요한 사용자의 자격 증명을 설정한 후에 cred.xml 파일을 안전한 위치에 저장하거나 삭제합니다.
6. Linux 노드 중 하나에서 RSA 키 쌍을 생성한 경우 사용이 끝난 후 키를 삭제해야 합니다. HPC 팩에서는 기존 id_rsa 파일 또는 id_rsa.pub 파일이 발견된 경우 상호 트러스트를 설정하지 않습니다.

> [!IMPORTANT]
> 공유 클러스터에서 Linux 작업을 클러스터 관리자로 실행하지 않는 것이 좋습니다. 관리자가 제출한 작업이 Linux 노드에서 루트 계정으로 실행되기 때문입니다. 관리자가 아닌 사용자가 제출한 작업은 작업 사용자와 동일한 이름을 가진 로컬 Linux 사용자 계정에서 실행됩니다. 이 경우 HPC 팩은 작업에 할당된 모든 노드 간에 이 Linux 사용자에 대한 상호 트러스트를 설정합니다. 작업을 실행하기 전에 Linux 노드에서 Linux 사용자를 수동으로 설정할 수 있습니다. 또는 HPC 팩에서 작업이 제출될 때 사용자를 자동으로 만듭니다. HPC 팩에서 사용자를 만드는 경우 HPC 팩은 작업이 완료된 후 사용자를 삭제합니다. 보안 위협을 줄이기 위해 노드에서 작업이 완료된 후 키가 제거됩니다.
> 
> 

## <a name="set-up-a-file-share-for-linux-nodes"></a>사용자가 액세스할 파일 공유를 설정합니다.
이제 SMB 파일 공유를 설정하고 모든 Linux 노드에서 공유 폴더를 탑재하여 Linux 노드에서 일반 경로로 NAMD 파일에 액세스할 수 있도록 합니다. 다음은 헤드 노드에 공유 폴더를 탑재하는 단계입니다. 현재 Azure 파일 서비스를 지원하지 않는 CentOS 6.6과 같은 배포의 경우 공유를 사용하는 것이 좋습니다. Linux 노드에서 Azure 파일 공유를 지원하는 경우 [Linux에서 Azure File Storage 사용 방법](../../../storage/files/storage-how-to-use-files-linux.md)을 참조하세요. HPC 팩의 추가 파일 공유 옵션에 대해서는 [Azure에서 HPC 팩 클러스터의 Linux 계산 노드 시작](hpcpack-cluster.md)을 참조하세요.

1. 헤드 노드에서 폴더를 만들고 읽기/쓰기 권한을 설정하여 모든 사용자에게 공유합니다. 이 예에서, \\\\CentOS66HN\Namd는 폴더의 이름이고 여기서 CentOS66HN은 헤드 노드의 호스트 이름입니다.
2. 공유 폴더에 namd2라는 하위 폴더를 만듭니다. namd2에 namdsample이라는 또 다른 하위 폴더를 만듭니다.
3. Windows 버전의 **tar** 또는 .tar 보관 파일에 대해 작동하는 다른 Windows 유틸리티를 사용하여 해당 폴더에 NAMD 파일을 추출합니다. 
   
   * \\\\CentOS66HN\Namd\namd2에 NAMD tar 보관 파일의 압축을 풉니다.
   * \\\\CentOS66HN\Namd\namd2\namdsample 아래에 자습서 파일의 압축을 풉니다.
4. Windows PowerShell 창을 열고 다음 명령을 실행하여 Linux 노드에 공유 폴더를 탑재합니다.
   
    ```command
    clusrun /nodegroup:LinuxNodes mkdir -p /namd2
   
    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS66HN/Namd/namd2 /namd2 -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

첫 번째 명령은 LinuxNodes 그룹의 모든 노드에 /namd2라는 폴더를 만듭니다. 두 번째 명령은 dir_mode 및 file_mode 비트를 777로 설정하여 공유 폴더 //CentOS66HN/Namd/namd2를 폴더에 탑재합니다. 명령의 *username*과 *password*는 헤드 노드 사용자의 자격 증명이어야 합니다.

> [!NOTE]
> 두 번째 명령의 “\`” 기호는 PowerShell의 이스케이프 기호입니다. “\`,”는 ","(쉼표)가 명령의 일부임을 의미합니다.
> 
> 

## <a name="create-a-bash-script-to-run-a-namd-job"></a>NAMD 작업을 실행하기 위한 Bash 스크립트 만들기
NAMD 작업을 수행하려면 NAMD 프로세스를 시작할 때 사용할 노드 수를 확인하기 위한 *charmrun* 용 **nodelist** 파일이 필요합니다. 이 섹션에서는 nodelist 파일을 생성하고 이 nodelist 파일과 함께 **charmrun**을 실행하는 Bash 스크립트를 사용합니다. 그런 다음 이 스크립트를 호출하는 HPC 클러스터 관리자에서 NAMD 작업을 제출할 수 있습니다.

원하는 텍스트 편집기를 사용하여 NAMD 프로그램 파일을 포함하는 /namd2 폴더에 Bash 스크립트를 만들고 hpccharmrun.sh로 이름을 지정합니다. 빠른 개념 증명을 위해 이 문서의 끝에 있는 예제 hpccharmrun.sh 스크립트를 복사하여 [NAMD 작업 제출](#submit-a-namd-job)로 이동합니다.

> [!TIP]
> 스크립트를 Linux 줄 끝(CR LF가 아닌 LF만)을 사용하여 텍스트 파일로 저장하십시오. 이렇게 해야 Linux 노드에서 제대로 실행됩니다.
> 
> 

아래에서는 이 Bash 스크립트가 수행하는 작업에 대해 자세히 설명합니다. 

1. 일부 변수를 정의합니다.
   
   ```bash
   #!/bin/bash
   
   # The path of this script
   SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
   # Charmrun command
   CHARMRUN=${SCRIPT_PATH}/charmrun
   # Argument of ++nodelist
   NODELIST_OPT="++nodelist"
   # Argument of ++p
   NUMPROCESS="+p"
   ```
2. 환경 변수로부터 노드 정보를 가져옵니다. $NODESCORES는 $CCP_NODES_CORES의 분할 단어 목록을 저장합니다. $COUNT는 $NODESCORES의 크기입니다.
   
   ```
   # Get node information from the environment variables
   NODESCORES=(${CCP_NODES_CORES})
   COUNT=${#NODESCORES[@]}
   ```    
   
   $CCP_NODES_CORES 변수의 형식은 다음과 같습니다.
   
   ```
   <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>…
   ```
   
   이 변수는 총 노드 수, 노드 이름 및 작업에 할당되는 각 노드의 코어 수를 나열합니다. 예를 들어 작업에 실행할 코어가 10개 필요한 경우 $CCP_NODES_CORES 값은 다음과 유사합니다.
   
   ```
   3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 2
   ```
3. $CCP_NODES_CORES 변수가 설정되지 않은 경우 **charmrun**을 직접 시작합니다. (이러한 경우는 Linux 노드에서 이 스크립트를 직접 실행하는 경우에만 발생합니다.)
   
   ```
   if [ ${COUNT} -eq 0 ]
   then
     # CCP_NODES is_CORES is not found or is empty, so just run charmrun without nodelist arg.
     #echo ${CHARMRUN} $*
     ${CHARMRUN} $*
   ```
4. 또는 **charmrun**에 대한 nodelist 파일을 만듭니다.
   
   ```
   else
     # Create the nodelist file
     NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$
   
     # Write the head line
     echo "group main" > ${NODELIST_PATH}
   
     # Get every node name and number of cores and write into the nodelist file
     I=1
     while [ ${I} -lt ${COUNT} ]
     do
         echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
         let "I=${I}+2"
     done
   ```
5. nodelist 파일과 함께 **charmrun** 을 실행하고 반환 상태를 가져오고 끝에는 nodelist 파일을 제거합니다.
   
   ${CCP_NUMCPUS}는 HPC 팩 헤드 노드에서 설정하는 다른 환경 변수입니다. 여기에는 이 작업에 할당된 총 코어 수가 저장됩니다. charmrun에 대한 프로세스 수를 지정하려면 사용합니다.
   
   ```
   # Run charmrun with nodelist arg
   #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
   ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
   
   RTNSTS=$?
   rm -f ${NODELIST_PATH}
   fi
   
   ```
6. **charmrun** 반환 상태와 함께 종료합니다.
   
   ```
   exit ${RTNSTS}
   ```

다음은 스크립트에서 생성하는 nodelist 파일의 정보입니다.

```
group main
host <Name of node1> ++cpus <Cores of node1>
host <Name of node2> ++cpus <Cores of node2>
…
```

예: 

```
group main
host CENTOS66LN-00 ++cpus 4
host CENTOS66LN-01 ++cpus 4
host CENTOS66LN-03 ++cpus 2
```

## <a name="submit-a-namd-job"></a>NAMD 작업 제출
이제 HPC 클러스터 관리자에서 NAMD 작업을 제출할 준비가 되었습니다.

1. 클러스터 헤드 노드에 연결하고 HPC 클러스터 관리자를 시작합니다.
2. **리소스 관리**에서 Linux 계산 노드가 **온라인** 상태인지 확인합니다. 온라인 상태가 아닌 경우 노드를 선택하고 **온라인 상태로 전환**을 클릭합니다.
3. **작업 관리**에서 **새 작업**을 클릭합니다.
4. 작업 이름(예: *hpccharmrun*)을 입력합니다.
   
   ![새 HPC 작업][namd_job]
5. **작업 세부 정보** 페이지의 **작업 리소스** 아래에서 리소스 유형을 **노드**로 선택하고 **최소**를 3으로 설정합니다. 세 개의 Linux 노드에서 작업을 실행하며 각 노드에는 4개의 코어가 있습니다.
   
   ![작업 리소스][job_resources]
6. 왼쪽 탐색 영역에서 **작업 편집**을 클릭한 다음 **추가**를 클릭하여 작업(job)에 작업(task)을 추가합니다.    
7. **작업 세부 정보 및 I/O 리디렉션** 페이지에서 다음 값을 설정합니다.
   
   * **명령줄** -
     `/namd2/hpccharmrun.sh ++remote-shell ssh /namd2/namd2 /namd2/namdsample/1-2-sphere/ubq_ws_eq.conf > /namd2/namd2_hpccharmrun.log`
     
     > [!TIP]
     > 위의 명령줄은 줄 바꿈이 없는 하나의 명령이며 **명령줄**에서는 줄이 바뀌어 여러 줄에 표시됩니다.
     > 
     > 
   * **작업 디렉터리** - /namd2
   * **최소** - 3
     
     ![태스크 세부 정보][task_details]
     
     > [!NOTE]
     > **charmrun**은 각 노드에서 동일한 작업 디렉터리로 이동하려고 시도하므로 여기에서 작업 디렉터리를 설정합니다. 작업 디렉터리가 설정되지 않으면 HPC 팩은 Linux 노드 중 하나에 생성된 무작위로 이름이 지정된 폴더에서 명령을 시작합니다. 이로 인해 다른 노드에서 다음과 같은 오류가 발생합니다. `/bin/bash: line 37: cd: /tmp/nodemanager_task_94_0.mFlQSN: No such file or directory.` 이 문제를 방지하려면 모든 노드에서 작업 디렉터리로 액세스할 수 있는 폴더 경로를 지정합니다.
     > 
     > 
8. **확인**을 클릭하고 **제출**을 클릭하여 이 작업을 실행합니다.
   
   기본적으로 HPC 팩은 현재 로그온한 사용자 계정으로 작업을 제출합니다. **제출**을 클릭한 후 사용자 이름 및 암호를 입력하라는 대화 상자가 표시될 수 있습니다.
   
   ![작업 자격 증명][creds]
   
   일부 조건에서 HPC 팩이 사용자가 이전에 입력한 사용자 정보를 저장해 두어 이 대화 상자가 표시되지 않습니다. HPC 팩에서 이 대화 상자를 다시 표시하려면 명령 프롬프트에 다음 명령을 입력한 후 작업을 제출합니다.
   
   ```command
   hpccred delcreds
   ```
9. 작업을 완료하는 데 몇 분 정도 걸릴 수 있습니다.
10. 작업 로그는 \\<headnodeName>\Namd\namd2\namd2_hpccharmrun.log에, 출력 파일은 \\<headnodeName>\Namd\namd2\namdsample\1-2-sphere\.에 있습니다.
11. 필요에 따라 VMD를 시작하여 작업 결과를 확인합니다. NAMD 출력 파일을 시각화하는 단계(이 경우, 물 구체에서 유비퀴틴 단백질 분자)는 이 문서의 범위를 벗어납니다. 자세한 내용은 [NAMD 자습서](http://www.life.illinois.edu/emad/biop590c/namd-tutorial-unix-590C.pdf) 를 참조하세요.
    
    ![작업 결과][vmd_view]

## <a name="sample-files"></a>샘플 파일
### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>PowerShell 스크립트를 통한 클러스터 배포용 샘플 XML 구성 파일
```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
      <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpclab.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS66HN</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS66LN-%00%</VMNamePattern>
    <ServiceName>MyLnxCNService</ServiceName>
     <VMSize>Large</VMSize>
     <NodeCount>4</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-66-20150325</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>    
```

### <a name="sample-credxml-file"></a>샘플 cred.xml 파일
```
<ExtendedData>
  <PrivateKey>-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAxJKBABhnOsE9eneGHvsjdoXKooHUxpTHI1JVunAJkVmFy8JC
qFt1pV98QCtKEHTC6kQ7tj1UT2N6nx1EY9BBHpZacnXmknpKdX4Nu0cNlSphLpru
lscKPR3XVzkTwEF00OMiNJVknq8qXJF1T3lYx3rW5EnItn6C3nQm3gQPXP0ckYCF
Jdtu/6SSgzV9kaapctLGPNp1Vjf9KeDQMrJXsQNHxnQcfiICp21NiUCiXosDqJrR
AfzePdl0XwsNngouy8t0fPlNSngZvsx+kPGh/AKakKIYS0cO9W3FmdYNW8Xehzkc
VzrtJhU8x21hXGfSC7V0ZeD7dMeTL3tQCVxCmwIDAQABAoIBAQCve8Jh3Wc6koxZ
qh43xicwhdwSGyliZisoozYZDC/ebDb/Ydq0BYIPMiDwADVMX5AqJuPPmwyLGtm6
9hu5p46aycrQ5+QA299g6DlF+PZtNbowKuvX+rRvPxagrTmupkCswjglDUEYUHPW
05wQaNoSqtzwS9Y85M/b24FfLeyxK0n8zjKFErJaHdhVxI6cxw7RdVlSmM9UHmah
wTkW8HkblbOArilAHi6SlRTNZG4gTGeDzPb7fYZo3hzJyLbcaNfJscUuqnAJ+6pT
iY6NNp1E8PQgjvHe21yv3DRoVRM4egqQvNZgUbYAMUgr30T1UoxnUXwk2vqJMfg2
Nzw0ESGRAoGBAPkfXjjGfc4HryqPkdx0kjXs0bXC3js2g4IXItK9YUFeZzf+476y
OTMQg/8DUbqd5rLv7PITIAqpGs39pkfnyohPjOe2zZzeoyaXurYIPV98hhH880uH
ZUhOxJYnlqHGxGT7p2PmmnAlmY4TSJrp12VnuiQVVVsXWOGPqHx4S4f9AoGBAMn/
vuea7hsCgwIE25MJJ55FYCJodLkioQy6aGP4NgB89Azzg527WsQ6H5xhgVMKHWyu
Q1snp+q8LyzD0i1veEvWb8EYifsMyTIPXOUTwZgzaTTCeJNHdc4gw1U22vd7OBYy
nZCU7Tn8Pe6eIMNztnVduiv+2QHuiNPgN7M73/x3AoGBAOL0IcmFgy0EsR8MBq0Z
ge4gnniBXCYDptEINNBaeVStJUnNKzwab6PGwwm6w2VI3thbXbi3lbRAlMve7fKK
B2ghWNPsJOtppKbPCek2Hnt0HUwb7qX7Zlj2cX/99uvRAjChVsDbYA0VJAxcIwQG
TxXx5pFi4g0HexCa6LrkeKMdAoGAcvRIACX7OwPC6nM5QgQDt95jRzGKu5EpdcTf
g4TNtplliblLPYhRrzokoyoaHteyxxak3ktDFCLj9eW6xoCZRQ9Tqd/9JhGwrfxw
MS19DtCzHoNNewM/135tqyD8m7pTwM4tPQqDtmwGErWKj7BaNZARUlhFxwOoemsv
R6DbZyECgYEAhjL2N3Pc+WW+8x2bbIBN3rJcMjBBIivB62AwgYZnA2D5wk5o0DKD
eesGSKS5l22ZMXJNShgzPKmv3HpH22CSVpO0sNZ6R+iG8a3oq4QkU61MT1CfGoMI
a8lxTKnZCsRXU1HexqZs+DSc+30tz50bNqLdido/l5B4EJnQP03ciO0=
-----END RSA PRIVATE KEY-----</PrivateKey>
  <PublicKey>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDEkoEAGGc6wT16d4Ye+yN2hcqigdTGlMcjUlW6cAmRWYXLwkKoW3WlX3xAK0oQdMLqRDu2PVRPY3qfHURj0EEellpydeaSekp1fg27Rw2VKmEumu6Wxwo9HddXORPAQXTQ4yI0lWSerypckXVPeVjHetbkSci2foLedCbeBA9c/RyRgIUl227/pJKDNX2Rpqly0sY82nVWN/0p4NAyslexA0fGdBx+IgKnbU2JQKJeiwOomtEB/N492XRfCw2eCi7Ly3R8+U1KeBm+zH6Q8aH8ApqQohhLRw71bcWZ1g1bxd6HORxXOu0mFTzHbWFcZ9ILtXRl4Pt0x5Mve1AJXEKb username@servername;</PublicKey>
</ExtendedData>
```

### <a name="sample-hpccharmrunsh-script"></a>샘플 hpccharmrun.sh 스크립트
```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
# Charmrun command
CHARMRUN=${SCRIPT_PATH}/charmrun
# Argument of ++nodelist
NODELIST_OPT="++nodelist"
# Argument of ++p
NUMPROCESS="+p"

# Get node information from ENVs
# CCP_NODES_CORES=3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 4
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # If CCP_NODES_CORES is not found or is empty, just run the charmrun without nodelist arg.
    #echo ${CHARMRUN} $*
    ${CHARMRUN} $*
else
    # Create the nodelist file
    NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

    # Write the head line
    echo "group main" > ${NODELIST_PATH}

    # Get every node name & cores and write into the nodelist file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the charmrun with nodelist arg
    #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
    ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}
```

 

<!--Image references-->
[keygen]:media/hpcpack-cluster-namd/keygen.png
[keys]:media/hpcpack-cluster-namd/keys.png
[namd_job]:media/hpcpack-cluster-namd/namd_job.png
[job_resources]:media/hpcpack-cluster-namd/job_resources.png
[creds]:media/hpcpack-cluster-namd/creds.png
[task_details]:media/hpcpack-cluster-namd/task_details.png
[vmd_view]:media/hpcpack-cluster-namd/vmd_view.png
