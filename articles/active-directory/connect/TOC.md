# 개요
## [Azure AD Connect의 정의](active-directory-aadconnect.md)
## [Azure AD Connect 동기화란?](active-directory-aadconnectsync-whatis.md)
### [사용자 및 연락처](active-directory-aadconnectsync-understanding-users-and-contacts.md)
### [Architecture](active-directory-aadconnectsync-understanding-architecture.md)
### [선언적 프로비전](active-directory-aadconnectsync-understanding-declarative-provisioning.md)
#### [선언적 프로비전 식](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
### [기본 구성](active-directory-aadconnectsync-understanding-default-configuration.md)
## [Azure AD Connect 및 페더레이션이란?](active-directory-aadconnectfed-whatis.md)


# 시작하기
## [필수 구성 요소](active-directory-aadconnect-prerequisites.md)
## [Azure AD Connect 설치](active-directory-aadconnect-select-installation.md)
### [Express 설정](active-directory-aadconnect-get-started-express.md)
### [사용자 지정 설정](active-directory-aadconnect-get-started-custom.md)
### [DirSync에서 업그레이드](active-directory-aadconnect-dirsync-upgrade-get-started.md)
### [이전 버전에서 업그레이드](active-directory-aadconnect-upgrade-previous-version.md)
### [기존 ADSync 데이터베이스를 사용하여 설치](active-directory-aadconnect-existing-database.md)
### [SQL 위임된 관리자 권한을 사용하여 설치](active-directory-aadconnect-sql-delegation.md)

# 방법
## 계획 및 디자인
### [설계 개념](active-directory-aadconnect-design-concepts.md)
### [Azure AD Connect에 대한 토폴로지](active-directory-aadconnect-topologies.md)
### [Azure의 Active Directory Federation Services](active-directory-aadconnect-azure-adfs.md)
### [인스턴스에 대한 특별 고려 사항](active-directory-aadconnect-instances.md)
### [Azure AD가 이미 있는 경우](active-directory-aadconnect-existing-tenant.md)
## [Azure AD Connect 관리](active-directory-aadconnect-whats-next.md)
### [O365 및 Azure AD에 대한 인증서 갱신](active-directory-aadconnect-o365-certs.md)
### [AD FS(Active Directory Federation Services) 팜에 대한 SSL 인증서 업데이트](active-directory-aadconnectfed-ssl-update.md)

### [장치 옵션](active-directory-azure-ad-connect-device-options.md)
#### [장치 쓰기 저장 사용](active-directory-aadconnect-feature-device-writeback.md)
#### [하이브리드 Azure AD 조인 사후 작업](active-directory-azure-ad-connect-hybrid-azure-ad-join-post-config-tasks.md)

### [사용자 로그인 옵션](active-directory-aadconnect-user-signin.md)
#### [Seamless Single Sign-On](active-directory-aadconnect-sso.md)
##### [빠른 시작](active-directory-aadconnect-sso-quick-start.md)
##### [작동 원리](active-directory-aadconnect-sso-how-it-works.md)
##### [질문과 대답](active-directory-aadconnect-sso-faq.md)
##### [문제 해결](active-directory-aadconnect-troubleshoot-sso.md)
##### [사용자 개인 정보 및 Azure AD 원활한 Single Sign-on](active-directory-aadconnect-sso-gdpr.md)
#### [통과 인증](active-directory-aadconnect-pass-through-authentication.md)
##### [빠른 시작](active-directory-aadconnect-pass-through-authentication-quick-start.md)
##### [현재 제한 사항](active-directory-aadconnect-pass-through-authentication-current-limitations.md)
##### [작동 원리](active-directory-aadconnect-pass-through-authentication-how-it-works.md)
##### [미리 보기 에이전트 업그레이드](active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md)
##### [스마트 잠금](active-directory-aadconnect-pass-through-authentication-smart-lockout.md)
##### [질문과 대답](active-directory-aadconnect-pass-through-authentication-faq.md)
##### [문제 해결](active-directory-aadconnect-troubleshoot-pass-through-authentication.md)
##### [보안 심층 분석](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md)
##### [사용자 개인 정보 및 Azure Active Directory 통과 인증](active-directory-aadconnect-pass-through-authentication-gdpr.md)
### [페더레이션에 대한 여러 도메인 지원](active-directory-aadconnect-multiple-domains.md)
### [자동 업그레이드](active-directory-aadconnect-feature-automatic-upgrade.md)
### [Single Sign-On에 SAML 2.0 IdP(ID 공급자) 사용](active-directory-aadconnect-federation-saml-idp.md)



## Azure AD Connect 동기화 관리
### [사용자 개인 정보 및 Azure AD Connect](active-directory-aadconnect-gdpr.md)
### [O365 리소스의 기본 데이터 위치](active-directory-aadconnectsync-feature-preferreddatalocation.md)
### [실수로 인한 삭제 방지](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
### [암호 해시 동기화](active-directory-aadconnectsync-implement-password-hash-synchronization.md)
### [Azure AD 서비스 계정](active-directory-aadconnectsync-howto-azureadaccount.md)
### [설치 마법사](active-directory-aadconnectsync-installation-wizard.md)
### [UserPrincipalName 채워지는 방법](active-directory-aadconnect-userprincipalname.md)
### [기본 구성 변경](active-directory-aadconnectsync-best-practices-changing-default-configuration.md)
### [필터링 구성](active-directory-aadconnectsync-configure-filtering.md)
### [Scheduler](active-directory-aadconnectsync-feature-scheduler.md)
### [디렉터리 확장](active-directory-aadconnectsync-feature-directory-extensions.md)

### [Azure AD Sync 서비스 계정 암호 변경](active-directory-aadconnectsync-change-serviceacct-pass.md)
### [AD DS 계정 암호 변경](active-directory-aadconnectsync-change-addsacct-pass.md)
### [AD 휴지통 사용](active-directory-aadconnectsync-recycle-bin.md)

### [Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md)
#### [작업](active-directory-aadconnectsync-service-manager-ui-operations.md)
#### [커넥터](active-directory-aadconnectsync-service-manager-ui-connectors.md)
#### [메타버스 디자이너](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md)
#### [메타 버스 검색](active-directory-aadconnectsync-service-manager-ui-mvsearch.md)


## 페더레이션 서비스 관리
### [관리 및 사용자 지정](active-directory-aadconnect-federation-management.md)
### [Azure AD의 여러 인스턴스를 AD FS의 단일 인스턴스로 페더레이션](active-directory-aadconnectfed-single-adfs-multitenant-federation.md)


## 문제 해결
### [Azure AD Connect로 Azure AD 연결](active-directory-aadconnect-troubleshoot-connectivity.md)
### [SQL 연결](active-directory-aadconnect-tshoot-sql-connectivity.md)
### [동기화 중 오류](active-directory-aadconnect-troubleshoot-sync-errors.md)
### [개체가 동기화되지 않음](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md)
### [문제 해결 작업을 사용한 개체 동기화](active-directory-aadconnect-troubleshoot-objectsync.md)
### [암호 해시 동기화](active-directory-aadconnectsync-troubleshoot-password-hash-synchronization.md)
### [userCertificate로 인해 발생한 LargeObject 오류](active-directory-aadconnectsync-largeobjecterror-usercertificate.md)
### [LocalDB 10GB 제한에서 복구하는 방법](active-directory-aadconnect-recover-from-localdb-10gb-limit.md)

# 참고 자료
## [코드 샘플](https://azure.microsoft.com/resources/samples/?service=active-directory)
## [ID 동기화 및 중복 특성 복원력](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)
## [포트 및 프로토콜이 필요한 하이브리드 ID](active-directory-aadconnect-ports.md)
## [미리 보기의 기능](active-directory-aadconnect-feature-preview.md)
## [버전 기록](active-directory-aadconnect-version-history.md)
## [계정 및 사용 권한](active-directory-aadconnect-accounts-permissions.md)

## Azure AD Connect 동기화
### [Azure Active Directory에 동기화된 특성](active-directory-aadconnectsync-attributes-synchronized.md)
### [커넥터 버전 릴리스 내역](active-directory-aadconnectsync-connector-version-history.md)
### [함수 참조](active-directory-aadconnectsync-functions-reference.md)
### [운영 작업 및 고려 사항](active-directory-aadconnectsync-operations.md)
### [Azure AD 페더레이션 호환성 목록](active-directory-aadconnect-federation-compatibility.md)
### [기술 개념](active-directory-aadconnectsync-technical-concepts.md)
### [서비스 기능](active-directory-aadconnectsyncservice-features.md)




# 관련 항목
## [온-프레미스 ID 인프라 및 클라우드 동기화 서비스를 모니터링합니다.](../connect-health/active-directory-aadconnect-health.md)
## [하이브리드 ID 디자인 가이드](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/)


# 리소스
## [Azure 로드맵](https://azure.microsoft.com/roadmap/?category=security-identity)
##[Azure AD Connect FAQ](active-directory-aadconnect-faq.md)
##[DirSync 사용 중단](active-directory-aadconnect-dirsync-deprecated.md)
## [요금 계산기](https://azure.microsoft.com/pricing/calculator/)

