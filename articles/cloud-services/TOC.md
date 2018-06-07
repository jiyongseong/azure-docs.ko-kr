# [Cloud Services 설명서](index.md)

# 개요
## [란?](cloud-services-choose-me.md)
## [클라우드 서비스 구성 파일 및 패키징](cloud-services-model-and-package.md)

# 시작
## [.NET 클라우드 서비스 예](cloud-services-dotnet-get-started.md)
## [Visual Studio 클라우드 서비스용 Python 예](cloud-services-python-ptvs.md)
## [Microsoft HPC 팩을 사용하여 하이브리드 HPC 클러스터 설정](cloud-services-setup-hybrid-hpcpack-cluster.md)

# 방법
## 계획
### [가상 머신 크기](cloud-services-sizes-specs.md)
### [업데이트](cloud-services-update-azure-service.md)

## 개발
### [PHP 웹 및 작업자 역할 만들기](../cloud-services-php-create-web-role.md)
### [Node.js 응용 프로그램 빌드 및 배포](cloud-services-nodejs-develop-deploy-app.md)
### [Express를 사용하여 Node.js 웹 응용 프로그램 빌드](cloud-services-nodejs-develop-deploy-express-app.md)
### 저장소 및 Visual Studio
#### [Blob 저장소 및 연결된 서비스](../visual-studio/vs-storage-cloud-services-getting-started-blobs.md)
#### [큐 저장소 및 연결된 서비스](../visual-studio/vs-storage-cloud-services-getting-started-queues.md)
#### [테이블 저장소 및 연결된 서비스](../visual-studio/vs-storage-cloud-services-getting-started-tables.md)
### [역할에 대한 트래픽 규칙 구성](cloud-services-enable-communication-role-instances.md)
### [클라우드 서비스 수명 주기 이벤트 처리](cloud-services-role-lifecycle-dotnet.md)
### [Socket.io(Node.js)](cloud-services-nodejs-chat-app-socketio.md)
### [Twilio를 사용하여 전화 걸기(.NET)](../partner-twilio-cloud-services-dotnet-phone-call-web-role.md)

### 시작 작업 구성
#### [시작 작업 만들기](cloud-services-startup-tasks.md)
#### [일반적인 시작 작업](cloud-services-startup-tasks-common.md)
#### [클라우드 서비스 역할에서 작업을 사용하여 .NET 설치](cloud-services-dotnet-install-dotnet.md)

### 원격 데스크톱 구성
#### [포털](cloud-services-role-enable-remote-desktop-new-portal.md)
#### [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
#### [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

## 배포
### [포털에서 클라우드 서비스 만들기 및 배포](cloud-services-how-to-create-deploy-portal.md)
### [PowerShell에서 빈 클라우드 서비스 컨테이너 만들기](cloud-services-powershell-create-cloud-container.md)
### [사용자 지정 도메인 이름 구성](cloud-services-custom-domain-name-portal.md)
### [사용자 지정 도메인 컨트롤러에 연결](cloud-services-connect-to-custom-domain.md)

## 서비스 관리
### [일반적인 관리 작업](cloud-services-how-to-manage-portal.md)
### [클라우드 서비스 구성](cloud-services-how-to-configure-portal.md)
### [Azure Automation을 사용하여 클라우드 서비스 관리](automation-manage-cloud-services.md)
### [자동 크기 조정 구성](cloud-services-how-to-scale-portal.md)
### [Python으로 Azure 리소스 관리](cloud-services-python-how-to-use-service-management.md)
### [잘못된 실행 완화](mitigate-se.md)

### [게스트 OS 패치](cloud-services-guestos-msrc-releases.md)
### 게스트 OS 사용 중지
#### [사용 중지 정책](cloud-services-guestos-retirement-policy.md)
#### [제품군 1 사용 중지 확인](cloud-services-guestos-family1-retirement.md)
### [게스트 OS 릴리스 뉴스](cloud-services-guestos-update-matrix.md)
### [Cloud Services 역할 구성 XPath 참고 자료](cloud-services-role-config-xpath.md)

## 인증서 관리
### [Cloud Services 및 관리 인증서](cloud-services-certs-create.md)
### [SSL 구성](cloud-services-configure-ssl-certificate-portal.md)

## 모니터
### [클라우드 서비스 모니터링](cloud-services-how-to-monitor.md)
### [성능 카운터 사용](diagnostics-performance-counters.md)
### [성능 테스트](../vs-azure-tools-performance-profiling-cloud-services.md)
#### [Visual Studio 프로파일러를 사용하여 테스트](cloud-services-performance-testing-visual-studio-profiler.md)
### 진단 사용
#### [Azure PowerShell](cloud-services-diagnostics-powershell.md)
#### [.NET](cloud-services-dotnet-diagnostics.md)
#### [Visual Studio](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
### [Azure Storage에서 진단 데이터 저장 및 보기](../monitoring-and-diagnostics/azure-diagnostics-storage.md?toc=%2fazure%2fcloud-services%2ftoc.json )
### [진단을 사용하여 클라우드 서비스 추적](cloud-services-dotnet-diagnostics-trace-flow.md)

## 문제 해결
### 디버그 
#### [클라우드 서비스 옵션](../vs-azure-tools-debugging-cloud-services-overview.md)
#### [Visual Studio로 로컬 클라우드 서비스](../vs-azure-tools-debug-cloud-services-virtual-machines.md)
#### [Visual Studio로 게시된 클라우드 서비스](../vs-azure-tools-intellitrace-debug-published-cloud-services.md)
### [클라우드 서비스 할당 오류](cloud-services-allocation-failures.md)
### [일반적인 클라우드 서비스 역할 재활용 원인](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md)
### [기본 TEMP 폴더 크기가 역할에 비해 너무 작음](cloud-services-troubleshoot-default-temp-folder-size-too-small-web-worker-role.md)
### [일반적인 배포 문제](cloud-services-troubleshoot-deployment-problems.md)
### [역할 시작 실패](cloud-services-troubleshoot-roles-that-fail-start.md)
### [복구 지침](cloud-services-disaster-recovery-guidance.md)
### Cloud Services FAQ
#### [응용 프로그램 및 서비스 가용성 FAQ](cloud-services-application-and-service-availability-faq.md)
#### [구성 및 관리 FAQ](cloud-services-configuration-and-management-faq.md)
#### [연결 및 네트워킹 FAQ](cloud-services-connectivity-and-networking-faq.md)
#### [배포 FAQ](cloud-services-deployment-faq.md)

# 참고 자료
## [코드 샘플](https://azure.microsoft.com/resources/samples/?service=cloud-services)
## [.csdef XML 스키마](schema-csdef-file.md)
### [LoadBalancerProbe 스키마](schema-csdef-loadbalancerprobe.md)
### [WebRole 스키마](schema-csdef-webrole.md)
### [WorkerRole 스키마](schema-csdef-workerrole.md)
### [NetworkTrafficRules 스키마](schema-csdef-networktrafficrules.md)
## [.cscfg XML 스키마](schema-cscfg-file.md)
### [역할 스키마](schema-cscfg-role.md)
### [NetworkConfiguration 스키마](schema-cscfg-networkconfiguration.md)
## [REST](/rest/api/compute/cloudservices/)

# 리소스
## [Azure 로드맵](https://azure.microsoft.com/roadmap/?category=compute)
## [학습 경로](https://azure.microsoft.com/documentation/learning-paths/cloud-services/)
## [MSDN 포럼](https://social.msdn.microsoft.com/Forums/en-us/home?forum=windowsazuredevelopment)
## [가격](https://azure.microsoft.com/pricing/details/cloud-services/)
## [요금 계산기](https://azure.microsoft.com/pricing/calculator/)
## [서비스 업데이트](https://azure.microsoft.com/updates/?product=cloud-services&updatetype=&platform=)
## [비디오](https://azure.microsoft.com/documentation/videos/index/?services=cloud-services)
