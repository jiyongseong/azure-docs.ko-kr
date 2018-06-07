# 개요
## [시작](get-started.md)
## [인증서](sap-certifications.md)
# Azure(큰 인스턴스)에서 SAP HANA 사용
## [개요 및 아키텍처](hana-overview-architecture.md)
## [인프라 및 연결](hana-overview-infrastructure-connectivity.md)
## [SAP HANA 설치](hana-installation.md)
## [고가용성 및 재해 복구](hana-overview-high-availability-disaster-recovery.md)
## [문제 해결 및 모니터링](troubleshooting-monitoring.md)
## 방법
### [STONITH로 HA 설치](ha-setup-with-stonith.md)
### [Type II SKU용 OS 백업](os-backup-type-ii-skus.md)
### [HANA 대형 인스턴스를 위한 OS 업그레이드](os-upgrade-hana-large-instance.md)
# Azure Virtual Machines의 SAP HANA
## [단일 인스턴스 SAP HANA 설치](hana-get-started.md)
## [S/4 HANA 또는 BW/4 HANA SAP CAL 배포 가이드](cal-s4h.md)
## [Azure 운영 가이드의 SAP HANA](hana-vm-operations.md)
## Azure Virtual Machines의 SAP HANA 가용성
### [Azure의 SAP HANA 가용성 개요](sap-hana-availability-overview.md)
### [단일 Azure 지역 내 Azure의 SAP HANA 가용성](sap-hana-availability-one-region.md)
### [Azure 지역 간 Azure의 SAP HANA 가용성](sap-hana-availability-across-regions.md)
## [Azure VM에서 SAP HANA 시스템 복제 설정](sap-hana-high-availability.md)
## [SAP HANA 백업 개요](sap-hana-backup-guide.md)
## [SAP HANA 파일 수준 백업](sap-hana-backup-file-level.md)
## [SAP HANA 저장소 스냅숏 백업](sap-hana-backup-storage-snapshots.md)
# Azure Virtual Machines의 SAP NetWeaver
## [Windows/SQL Server SAP CAL 배포 가이드에 SAP IDES](cal-ides-erp6-erp7-sp3-sql.md)
## [Azure Linux VM의 SAP NetWeaver](suse-quickstart.md)
## [Azure에서 SAP NetWeaver 계획 및 구현](planning-guide.md)
## Windows 및 Linux의 HA(고가용성)
### [개요](sap-high-availability-guide-start.md)
### 고가용성 아키텍처
#### [HA 아키텍처 및 시나리오](sap-high-availability-architecture-scenarios.md)
#### [고가용성 아키텍처 및 시나리오](sap-higher-availability-architecture-scenarios.md)
#### [(A)SCS 인스턴스에 공유 디스크를 사용하는 Windows의 HA](sap-high-availability-guide-wsfc-shared-disk.md)
#### [(A)SCS 인스턴스에 SOFS 파일 공유를 사용하는 Windows의 HA](sap-high-availability-guide-wsfc-file-share.md)
#### [(A)SCS 인스턴스의 SUSE Linux에 대한 HA](high-availability-guide-suse.md)
### Azure 인프라 준비
#### [(A)SCS 인스턴스에 공유 디스크를 사용하는 Windows](sap-high-availability-infrastructure-wsfc-shared-disk.md)
#### [(A)SCS 인스턴스에 SOFS 파일 공유를 사용하는 Windows](sap-high-availability-infrastructure-wsfc-file-share.md)
#### [SLES의 Azure VM에서 NFS에 대한 고가용성](high-availability-guide-suse-nfs.md)
#### [SLES의 Pacemaker](high-availability-guide-suse-pacemaker.md)
### SAP 설치
#### [(A)SCS 인스턴스에 공유 디스크를 사용하는 Windows](sap-high-availability-installation-wsfc-shared-disk.md)
#### [(A)SCS 인스턴스에 SOFS 파일 공유를 사용하는 Windows](sap-high-availability-installation-wsfc-file-share.md)
#### [(A)SCS 인스턴스에 NFS를 사용하는 SUSE Linux](high-availability-guide-suse.md)
### SAP 다중 SID
#### [(A)SCS 인스턴스에 공유 디스크를 사용하는 Windows](sap-ascs-ha-multi-sid-wsfc-shared-disk.md)
#### [(A)SCS 인스턴스에 SOFS 파일 공유를 사용하는 Windows](sap-ascs-ha-multi-sid-wsfc-file-share.md)
## [배포 가이드](deployment-guide.md)
## [DBMS 배포 가이드](dbms-guide.md)
## [SAP 재해 복구를 위한 Azure Site Recovery](../../../site-recovery/site-recovery-workload.md#protect-sap)
# AAD SAP ID 통합 및 Single-Sign-On
## [SAP 클라우드와 통합](../../../active-directory/active-directory-saas-sap-customer-cloud-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [AAD와 SAP 클라우드 플랫폼 ID 인증 통합](../../../active-directory/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [SAP 클라우드 플랫폼에 Single-Sign-On 설정](../../../active-directory/active-directory-saas-sap-hana-cloud-platform-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [AAD와 SAP NetWeaver 통합](../../../active-directory/active-directory-saas-sap-netweaver-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [AAD와 SAP Business ByDesign 통합](../../../active-directory/active-directory-saas-sapbusinessbydesign-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [AAD와 SAP HANA DBMS 통합](../../../active-directory/active-directory-saas-saphana-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
##[Azure AD로 SAP Fiori Launchpad SAML Single Sign-On](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad)
# 리소스
## [Azure 로드맵](https://azure.microsoft.com/roadmap/)
