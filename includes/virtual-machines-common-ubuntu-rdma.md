1. Dapl, rdmacm, ibverbs 및 mlx4 설치

  ```bash
  sudo apt-get update

  sudo apt-get install libdapl2 libmlx4-1

  ```

2. /etc/waagent.conf에서 다음 구성 줄의 주석 처리를 해제하여 RDMA를 사용하도록 설정합니다. 이 파일을 편집하려면 루트 액세스가 필요합니다.
  
  ```
  OS.EnableRDMA=y

  OS.UpdateRdmaDriver=y
  ```

3. /etc/security/limits.conf 파일에서 다음 메모리 설정(KB)을 추가하거나 변경합니다. 이 파일을 편집하려면 루트 액세스가 필요합니다. 테스트를 위해 memlock을 unlimited로 설정할 수 있습니다. 예: `<User or group name>   hard    memlock   unlimited`

  ```
  <User or group name> hard    memlock <memory required for your application in KB>

  <User or group name> soft    memlock <memory required for your application in KB>
  ```
  
4. Intel MPI Library를 설치합니다. Intel에서 이 라이브러리를 [구입 및 다운로드](https://software.intel.com/intel-mpi-library/)하거나 [평가판](https://registrationcenter.intel.com/en/forms/?productid=1740)을 다운로드합니다.

  ```bash
 wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/11595/l_mpi_2017.3.196.tgz
   ```
 
 설치 단계에 대해서는 [Intel MPI Library 설치 가이드](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html)를 참조하세요.

5. 루트가 아닌 비디버거 프로세스에 대해 ptrace를 사용하도록 설정합니다(가장 최신 버전의 Intel MPI에 필요).
 
  ```bash
  echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
  ```
