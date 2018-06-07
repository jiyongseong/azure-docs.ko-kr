이 문서는 확장성, 가용성, 관리 효율성 및 보안에 주의하면서 Azure에서 Linux VM(가상 컴퓨터)을 실행하기 위한 일련의 검증된 작업 방식을 간략하게 설명합니다. Azure에서는 CentOS, Debian, Red Hat Enterprise, Ubuntu 및 FreeBSD를 포함하여 인기 있는 다양한 Linux 배포판을 실행할 수 있습니다. 자세한 내용은 [Azure 및 Linux][azure-linux]를 참조하세요.

> [!NOTE]
> Azure에는 [Resource Manager][resource-manager-overview] 및 클래식이라는 두 가지 서로 다른 배포 모델이 있습니다. 이 문서에서는 새로운 배포를 위해 Microsoft에서 권장하는 리소스 관리자를 사용합니다.
> 
> 

단일 실패 지점을 생성하므로 중요한 작업에 단일 VM을 사용하지 않는 것이 좋습니다. 더 높은 가용성을 위해 [가용성 집합][availability-set]에 여러 VM을 배포합니다. 자세한 내용은 [Azure에서 여러 VM 실행][multi-vm]을 참조하세요. 

## <a name="architecture-diagram"></a>아키텍처 다이어그램
Azure에서 VM을 프로비전할 때에는 VM 자체 이외에도 움직일 부분이 많습니다. 고려해야 하는 계산, 네트워킹 및 저장소 요소가 있습니다.

> 이 아키텍처 다이어그램이 포함된 Visio 문서는 [Microsoft 다운로드 센터][visio-download]에서 다운로드할 수 있습니다. 아 다이어그램은 "계산 - 단일 VM" 페이지에 있습니다.
> 
> 

![[0]][0]

* **리소스 그룹.** [*리소스 그룹*][resource-manager-overview]은 관련된 리소스를 보유하는 컨테이너입니다. 이 VM에 대한 리소스를 보유할 리소스 그룹을 만듭니다.
* **VM**. 게시된 이미지 목록 또는 Azure Blob Storage에 업로드된 VHD(가상 하드 디스크) 파일에서 VM을 프로비전할 수 있습니다.
* **OS 디스크.** OS 디스크는 [Azure Storage][azure-storage]에 저장된 VHD입니다. 즉, 호스트 컴퓨터가 중단되어도 유지됩니다. OS 디스크는 `/dev/sda1`입니다.
* **임시 디스크.** VM은 임시 디스크를 사용하여 만들어집니다. 이 디스크는 호스트 컴퓨터의 실제 드라이브에 저장됩니다. Azure Storage에는 저장되지 *않으며* 다시 부팅되는 동안에 그리고 다른 VM의 수명 주기 이벤트 동안에 삭제될 수 있습니다. 페이지 또는 스왑 파일과 같은 임시 데이터에 대해서만 이 디스크를 사용합니다. 임시 디스크는 `/dev/sdb1`이며 `/mnt/resource` 또는 `/mnt`에 탑재됩니다.
* **데이터 디스크.** [데이터 디스크][data-disk]는 응용 프로그램 데이터에 사용되는 영구 VHD입니다. 데이터 디스크는 OS 디스크와 같은 Azure Storage에 저장됩니다.
* **가상 네트워크(VNet) 및 서브넷.** Azure의 모든 VM은 서브넷으로 세분화되는 VNet에 배포됩니다.
* **공용 IP 주소.** 공용 IP 주소는 예를 들어 SSH를 통해 VM&mdash;과 통신하는 데 필요합니다.
* **NIC(네트워크 인터페이스)**. NIC를 통해 VM을 가상 네트워크와 통신하도록 할 수 있습니다.
* **NSG(네트워크 보안 그룹)**. [NSG][nsg]는 서브넷에 대한 네트워크 트래픽을 허용/거부하는 데 사용됩니다. NSG를 개별 NIC 또는 서브넷에 연결할 수 있습니다. 서브넷에 연결하는 경우 NSG 규칙이 해당 서브넷의 모든 VM에 적용됩니다.
* **진단** VM을 관리 및 문제 해결하는 데 진단 로깅이 중요합니다.

## <a name="recommendations"></a>추천

대부분의 시나리오의 경우 다음 권장 사항을 적용합니다. 이러한 권장 사항을 재정의하라는 특정 요구 사항이 있는 경우가 아니면 따릅니다. 

### <a name="vm-recommendations"></a>VM 권장 사항

Azure에서 다양한 크기의 가상 컴퓨터를 제공하지만 DS 및 GS 시리즈의 컴퓨터 크기가 [Premium Storage][premium-storage]를 지원하므로 이러한 시리즈를 사용하는 것이 좋습니다. 고성능 컴퓨팅과 같은 특수한 워크로드가 발생하지 않는다면 이러한 컴퓨터 크기 중 하나를 선택합니다. 자세한 내용은 [가상 컴퓨터 크기][virtual-machine-sizes]를 참조하세요.

기존 워크로드를 Azure로 이동하는 경우 온-프레미스 서버와 가장 근접하게 일치하는 VM 크기부터 사용하기 시작합니다. 그런 다음 CPU, 메모리, 디스크 IOPS(초당 입력/출력 작업 수)에 따라 실제 워크로드의 성능을 측정하고 필요에 따라 크기를 조정합니다. VM에 대해 여러 NIC가 필요한 경우 최대 NIC 수는 [VM 크기][vm-size-tables]의 함수입니다.

VM 및 기타 리소스를 프로비전할 경우 지역을 지정해야 합니다. 일반적으로 내부 사용자 또는 고객에게 가장 가까운 지역을 선택합니다. 그러나 일부 지역에서는 일부 VM 크기를 사용할 수 없을 수 있습니다. 자세한 내용은 [지역별 서비스][services-by-region]를 참조하세요. 지정된 지역에서 사용할 수 있는 VM 크기를 나열하려면 다음 Azure CLI(명령줄 인터페이스) 명령을 실행합니다.

```
azure vm sizes --location <location>
```

게시된 VM 이미지를 선택하는 방법에 대한 자세한 내용은 [Azure CLI를 사용하여 Linux VM 이미지 선택][select-vm-image]을 참조하세요.

### <a name="disk-and-storage-recommendations"></a>디스크 및 저장소 권장 사항

최상의 디스크 I/O 성능을 위해서는 SSD(반도체 드라이브)에 데이터를 저장하는 [프리미엄 저장소][premium-storage]가 권장됩니다. 비용은 프로비전된 디스크 크기를 기준으로 산정됩니다. IOPS 및 처리량(즉, 데이터 전송 속도)도 디스크 크기에 따라 달라지므로 디스크를 프로비전할 때 세 가지 요소(용량, IOPS, 처리량)를 모두 고려합니다. 

저장소 계정에 대한 IOPS 제한에 도달하지 않도록 하기 위해 VHD(가상 하드 디스크)를 보유하는 각 VM에 대한 별도 Azure Storage 계정을 만듭니다. 

하나 이상의 데이터 디스크를 추가합니다. VHD를 만들 때 형식은 지정되지 않습니다. 디스크를 포맷하려면 VM에 로그인합니다. Linux 셸에서 데이터 디스크는 `/dev/sdc`, `/dev/sdd` 등으로 표시됩니다. `lsblk` 를 실행하여 디스크를 포함하는 블록 장치를 나열할 수 있습니다. 데이터 디스크를 사용하려면 파티션 및 파일 시스템을 만들고 디스크를 탑재합니다. 예:

```bat
# Create a partition.
sudo fdisk /dev/sdc     # Enter 'n' to partition, 'w' to write the change.     

# Create a file system.
sudo mkfs -t ext3 /dev/sdc1

# Mount the drive.
sudo mkdir /data1
sudo mount /dev/sdc1 /data1
```

데이터 디스크 수가 많은 경우 저장소 계정의 총 I/O 제한에 주의해야 합니다. 자세한 내용은 [가상 컴퓨터 디스크 제한][vm-disk-limits]을 참조하세요.

데이터 디스크를 추가하면 디스크에 LUN(논리 단위 번호) ID가 할당됩니다. 예를 들어, 디스크를 교체하고 동일한 LUN ID를 유지하거나 특정 LUN ID를 검색하는 응용 프로그램이 있는 경우 필요에 따라 LUN ID &mdash;을(를) 지정할 수 있습니다. 그렇지만 LUN ID는 디스크마다 고유해야 합니다.

프리미엄 저장소 계정에서 VM의 디스크가 SSD이기 때문에 I/O 스케줄러를 변경하여 SSD의 성능을 최적화하려고 할 수 있습니다. SSD에 NOOP 스케줄러를 사용하는 것이 일반적으로 권장되지만 [iostat] 와 같은 도구를 사용하여 특정 워크로드에 대한 디스크 I/O 성능을 모니터링할 수 있습니다.

최상의 성능을 위해 진단 로그를 저장할 별도의 저장소 계정을 만듭니다. 표준 LRS(로컬 중복 저장소) 계정은 진단 로그에 충분합니다.

### <a name="network-recommendations"></a>네트워크 권장 사항

이 공용 IP 주소는 동적 또는 정적일 수 있습니다. 기본값은 동적입니다.

* 변경되지 않는 고정 IP 주소가 필요한 경우 [정적 IP 주소][static-ip]를 예약합니다(예를 들어 DNS에 A 레코드를 만들어야 하는 경우 또는 안전한 목록에 추가될 IP 주소가 필요한 경우).
* IP 주소의 FQDN(정규화된 도메인 이름)을 만들 수도 있습니다. 그런 후 FQDN을 가리키는 DNS에 [CNAME 레코드][cname-record]를 등록할 수 있습니다. 자세한 내용은 [Azure Portal에서 정규화된 도메인 이름 만들기][fqdn]를 참조하세요.

모든 NSG에는 모든 인바운드 인터넷 트래픽을 차단하는 규칙을 포함하여 [기본 규칙][nsg-default-rules] 집합이 있습니다. 기본 규칙은 삭제할 수 없으나 다른 규칙으로 재정의할 수 있습니다. 인터넷 트래픽을 사용하도록 설정하려면 특정 포트(예: HTTP용 포트 80)로의 인바운드 트래픽을 허용하는 규칙을 만듭니다.  

SSH를 사용하도록 설정하려면 TCP 포트 22에 인바운드 트래픽을 허용하는 규칙을 NSG에 추가합니다.

## <a name="scalability-considerations"></a>확장성 고려 사항

확장 또는 축소하려면 [VM 크기를 변경합니다][vm-resize]. 

규모를 확장하려면 부하 분산 장치를 기준으로 두 개 이상의 VM을 가용성 집합에 추가합니다. 자세한 내용은 [Azure에서 여러 VM 실행][multi-vm]을 참조하세요.

## <a name="availability-considerations"></a>가용성 고려 사항

더 높은 가용성을 위해 가용성 집합에 여러 VM을 배포합니다. 또한 더 높은 SLA([서비스 수준 약정][vm-sla])를 제공합니다. 

사용자의 VM은 [계획된 유지 관리][planned-maintenance] 또는 [계획되지 않은 유지 관리][manage-vm-availability]의 영향을 받을 수 있습니다. [VM 다시 부팅 로그][reboot-logs]를 사용하여 VM 재부팅이 계획된 유지 관리로 발생했는지 여부를 결정할 수 있습니다.

VHD는 [Azure Storage][azure-storage]에 저장되며 Azure Storage는 내구성 및 가용성을 위해 복제됩니다.

정상 작업 중 실수로 인한 데이터 손실(예: 사용자 오류 때문에 발생)을 막기 위해 [Blob 스냅숏][blob-snapshot] 또는 다른 도구를 사용하여 지정 시간 백업도 구현해야 합니다.

## <a name="manageability-considerations"></a>관리 효율성 고려 사항

**리소스 그룹** 동일한 수명 주기를 공유하는 긴밀하게 결합된 리소스를 동일한 [리소스 그룹][resource-manager-overview]에 추가합니다. 리소스 그룹을 사용하여 리소스를 그룹 단위로 배포 및 모니터링하고, 리소스 그룹별로 비용을 청구할 수 있습니다. 리소스를 하나의 집합으로 삭제할 수도 있습니다. 이러한 기능은 테스트 배포에서 매우 유용합니다. 리소스에 의미 있는 이름을 지정합니다. 이렇게 하면 보다 쉽게 특정 리소스를 찾고 역할을 이해할 수 있습니다. [Azure 리소스에 대한 권장되는 명명 규칙][naming conventions]을 참조하세요.

**SSH**. Linux VM을 만들기 전에 2048비트 RSA 공개-개인 키 쌍을 생성합니다. VM을 만들 때 공개 키 파일을 사용합니다. 자세한 내용은 [Azure에서 Linux 및 Mac과 함께 SSH를 사용하는 방법][ssh-linux]을 참조하세요.

**VM 진단.** 기본 상태 메트릭, 진단 인프라 로그 및 [부팅 진단][boot-diagnostics]을 모니터링 및 진단을 사용하도록 설정할 수 있습니다. 부팅 진단은 VM이 부팅할 수 없는 상태로 전환되는 경우 부팅 오류를 진단하는 데 도움이 될 수 있습니다. 자세한 내용은 [모니터링 및 진단 사용][enable-monitoring]을 참조하세요.  

다음 CLI 명령을 통해 진단을 사용할 수 있습니다.

```
azure vm enable-diag <resource-group> <vm-name>
```

**VM 중지.** Azure에서는 "중지됨"과 "할당 취소됨" 상태를 구분합니다. VM 상태가 중지되면 요금이 청구되지만 VM 할당이 취소되면 청구되지 않습니다.

VM을 할당 취소하려면 다음 CLI 명령을 사용합니다.

```
azure vm deallocate <resource-group> <vm-name>
```

Azure Portal에서 **중지** 버튼은 VM 할당을 취소합니다. 그러나 로그인한 상태에서 OS를 통해 종료하면 VM은 중지되지만 할당 취소되지 *않으므로* 비용이 계속 청구됩니다.

**VM 삭제.** VM을 삭제하는 경우 VHD는 삭제되지 않습니다. 즉, 데이터 손실 없이 안전하게 VM을 삭제할 수 있습니다. 그러나 저장소에 대한 비용은 계속 청구됩니다. VHD를 삭제하려면 [Blob 저장소][blob-storage]에서 파일을 삭제합니다.

실수로 삭제하지 않도록 하려면 [리소스 잠금][resource-lock]을 사용하여 전체 리소스 그룹을 잠그거나 VM과 같은 개별 리소스를 잠급니다. 

## <a name="security-considerations"></a>보안 고려 사항

[OSPatching] VM 확장을 사용하여 OS 업데이트를 자동화합니다. VM을 프로비전할 때 이 확장을 설치합니다. 패치 설치 빈도 및 패치 후 다시 부팅 여부를 지정할 수 있습니다.

RBAC([역할 기반 액세스 제어][rbac])를 사용하여 배포하는 Azure 리소스에 대한 액세스를 제어합니다. RBAC를 통해 DevOps 팀의 구성원에게 권한 역할을 할당할 수 있습니다. 예를 들어 읽기 권한자 역할은 Azure 리소스를 볼 수 있지만 만들거나 관리하거나 삭제할 수는 없습니다. 일부 역할은 특정 Azure 리소스 유형에 따라 다릅니다. 예를 들어 Virtual Machine 참가자 역할은 VM을 다시 시작하거나 할당을 취소하고, 관리자 암호를 재설정하고, VM을 만드는 등의 작업을 수행할 수 있습니다. 이 참조 아키텍처에 유용할 수 있는 기타 [기본 제공 RBAC 역할][rbac-roles]에는 [DevTest Labs 사용자][rbac-devtest] 및 [네트워크 참가자][rbac-network]가 포함됩니다. 

한 명의 사용자가 여러 역할에 할당될 수 있으며 좀 더 세분화된 권한의 사용자 지정 역할을 만들 수도 있습니다.

d> [!NOTE]
> RBAC는 VM에 로그온한 사용자가 수행할 수 있는 작업을 제한하지 않습니다. 이러한 사용 권한은 게스트 OS의 계정 유형에 따라 결정됩니다.   
> 
> 

[감사 로그][audit-logs]를 사용하여 프로비전 동작 및 기타 VM 이벤트를 확인합니다.

OS 및 데이터 디스크를 암호화해야 하는 경우 [Azure Disk Encryption][disk-encryption]을 고려하세요. 

## <a name="solution-deployment"></a>솔루션 배포
m 이 참조 아키텍처에 대한 배포는 [GitHub][github-folder]에서 사용할 수 있습니다. VNet, NSG 및 단일 VM을 포함합니다. 아키텍처를 배포하려면 다음 단계를 따르세요. 

1. 아래 단추를 마우스 오른쪽 단추로 클릭하고 "새 탭에서 링크 열기" 또는 "새 창에서 링크 열기"를 선택합니다.
   [![Azure에 배포](../articles/guidance/media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-single-vm%2Fazuredeploy.json)
2. Azure 포털에서 링크가 열렸으면 일부 설정에 대한 값을 입력해야 합니다. 
   
   * **리소스 그룹** 이름이 매개 변수 파일에 이미 정의되어 있으므로 **새로 만들기**를 선택하고 텍스트 상자에 `ra-single-vm-rg`를 입력합니다.
   * **위치** 드롭다운 상자에서 하위 지역을 선택합니다.
   * **템플릿 루트 Uri** 또는 **매개 변수 루트 Uri** 텍스트 상자를 편집하지 마세요.
을 참조하세요.   *OS 유형** 드롭다운 상자에서 *linux**를 선택합니다*.
   * 사용 약관을 검토한 후 **위에 명시된 사용 약관에 동의함** 확인란을 클릭합니다.
   * **구입** 단추를 클릭합니다.
3. 배포가 완료될 때가지 기다립니다.
4. 매개 변수 파일에는 하드 코딩된 관리자 사용자 이름 및 암호가 포함되며 둘 다 즉시 변경하는 것이 좋습니다. Azure Portal에서 `ra-single-vm0 `으로 명명된 VM을 클릭합니다. 그런 다음 **지원 + 문제 해결** 섹션에서 **암호 재설정**을 클릭합니다. **모드** 드롭다운 상자에서 **암호 재설정**을 선택한 후 새 **사용자 이름** 및 **암호**를 선택합니다. **업데이트** 단추를 클릭하여 새 사용자 이름 및 암호를 보존합니다.

## <a name="next-steps"></a>다음 단계
더 높은 가용성을 위해 부하 분산 장치 뒤에 둘 이상의 VM을 배포합니다. 자세한 내용은 [Azure에서 여러 VM 실행][multi-vm]을 참조하세요.

<!-- links -->

[audit-logs]: https://azure.microsoft.com/en-us/blog/analyze-azure-audit-logs-in-powerbi-more/
[availability-set]:../articles/virtual-machines/windows/create-availability-set.md
[azure-cli]: /cli/azure/get-started-with-az-cli2
[azure-linux]:../articles/virtual-machines/linux/overview.md
[azure-storage]:../articles/storage/common/storage-introduction.md
[blob-snapshot]:../articles/storage/blobs/storage-blob-snapshots.md
[blob-storage]:../articles/storage/common/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]:../articles/virtual-machines/linux/about-disks-and-vhds.md
[disk-encryption]: ../articles/security/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/monitoring-and-diagnostics/insights-how-to-use-diagnostics.md
[fqdn]:../articles/virtual-machines/linux/portal-create-fqdn.md
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm/
[iostat]: https://en.wikipedia.org/wiki/Iostat
[manage-vm-availability]:../articles/virtual-machines/linux/manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[OSPatching]: https://github.com/Azure/azure-linux-extensions/tree/master/OSPatching
[planned-maintenance]:../articles/virtual-machines/linux/planned-maintenance.md
[premium-storage]:../articles/storage/common/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-labs-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/en-us/blog/viewing-vm-reboot-logs/
[Resize-VHD]: https://technet.microsoft.com/en-us/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/en-us/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/azure-resource-manager/resource-group-overview.md
[select-vm-image]:../articles/virtual-machines/linux/cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[ssh-linux]:../articles/virtual-machines/linux/mac-create-ssh-keys.md
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-account-limits]: ../articles/azure-subscription-service-limits.md#storage-limits
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[virtual-machine-sizes]:../articles/virtual-machines/linux/sizes.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]:../articles/virtual-machines/linux/change-vm-size.md
[vm-size-tables]:../articles/virtual-machines/windows/sizes.md#size-tables
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines
[readme]: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm
[components]: #Solution-components
[blocks]: https://github.com/mspnp/template-building-blocks
[0]: ./media/guidance-blueprints/compute-single-vm.png "Azure의 단일 Linux VM 아키텍처"

