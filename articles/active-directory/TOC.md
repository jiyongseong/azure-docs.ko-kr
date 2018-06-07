# 개요
## [Azure Active Directory란?](active-directory-whatis.md)
## [Azure ID 관리 정보](identity-fundamentals.md)
## [Azure ID 솔루션 이해](understand-azure-identity-solutions.md)
## [하이브리드 ID 솔루션 선택](choose-hybrid-identity-solution.md)
## [Azure 구독 연결](active-directory-how-subscriptions-associated-directory.md)
## [보존 및 데이터 고려 사항](active-directory-data-storage-eu.md)
## [FAQ](active-directory-faq.md)
## [새로운 기능](whats-new.md)


# 시작하기
## [Azure AD 시작](get-started-azure-ad.md)
## [Azure AD Premium에 등록](active-directory-get-started-premium.md)
## [사용자 지정 도메인 이름 추가](add-custom-domain.md)
## [회사 브랜딩 구성](customize-branding.md)
## [Azure AD에 사용자 추가](add-users-azure-active-directory.md)
## [사용자에게 라이선스 할당](license-users-groups.md)
## [셀프 서비스 암호 재설정 구성](authentication/quickstart-sspr.md)
## [Azure AD에 조직의 개인 정보 추가](active-directory-properties-area.md)


# 방법
## 계획 및 디자인
### [Azure AD 아키텍처 이해](active-directory-architecture.md)
### [Azure Active Directory의 클레임 매핑](active-directory-claims-mapping.md)
### [하이브리드 ID 솔루션 배포](active-directory-hybrid-identity-design-considerations-overview.md)
#### 요구 사항 결정
##### [ID](active-directory-hybrid-identity-design-considerations-business-needs.md)
##### [디렉터리 동기화](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
##### [Multi-Factor Auth](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)
##### [ID 수명 주기 전략](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)
#### [데이터 보안 계획](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)
##### [데이터 보호](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
##### [콘텐츠 관리](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
##### [액세스 제어](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
##### [사고 대응](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)
#### ID 수명 주기 계획
##### [작업](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)
##### [채택 전략](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)
#### [다음 단계](active-directory-hybrid-identity-design-considerations-nextsteps.md)
#### [도구 비교](active-directory-hybrid-identity-design-considerations-tools-comparison.md)

## 사용자 관리
### [Azure AD에 새 사용자 추가](add-users-azure-active-directory.md)
### [사용자 프로필 관리](active-directory-users-profile-azure-portal.md)
### [공유 계정](active-directory-sharing-accounts.md)
### [관리자 역할에 사용자 할당](active-directory-users-assign-role-azure-portal.md)
### [다른 디렉터리(B2B)에서 게스트 사용자 추가](b2b/what-is-b2b.md)
#### [B2B 사용자를 추가하는 관리자](b2b/add-users-administrator.md)
#### [B2B 사용자를 추가하는 지식 근로자](b2b/add-users-information-worker.md)
#### [API 및 사용자 지정](b2b/customize-invitation-api.md)
#### [코드 및 Azure PowerShell 샘플](b2b/code-samples.md)
#### [셀프 서비스 등록 포털 샘플](b2b/self-service-portal.md)
#### [초대 전자 메일](b2b/invitation-email-elements.md)
#### [초대 상환](b2b/redemption-experience.md)
#### [초대 없이 B2B 사용자 추가](b2b/add-user-without-invite.md)
#### [초대 허용 또는 차단](b2b/allow-deny-list.md)
#### [B2B에 대한 조건부 액세스](b2b/conditional-access.md)
#### [B2B 공유 정책](b2b/delegate-invitations.md)
#### [역할에 B2B 사용자 추가](b2b/add-guest-to-role.md)
#### [동적 그룹 및 B2B 사용자](b2b/use-dynamic-groups.md)
#### [조직 나가기](b2b/leave-the-organization.md)
#### [감사 및 보고서](b2b/auditing-and-reporting.md)
#### [하이브리드 조직에 대한 B2B](b2b/hybrid-organizations.md)
##### [B2B 사용자에게 로컬 앱에 대한 액세스 권한 부여](b2b/hybrid-cloud-to-on-premises.md)
##### [로컬 사용자에게 클라우드 앱에 대한 액세스 권한 부여](b2b/hybrid-on-premises-to-cloud.md)
#### [B2B 및 Office 365 외부 공유](b2b/o365-external-user.md)
#### [B2B 라이선스](b2b/licensing-guidance.md)
#### [현재 제한 사항](b2b/current-limitations.md)
#### [FAQ](b2b/faq.md)
#### [B2B 문제 해결](b2b/troubleshoot.md)
#### [B2B 사용자 이해](b2b/user-properties.md)
#### [B2B 사용자 토큰](b2b/user-token.md)
#### [Azure AD용 B2B 통합 앱](b2b/configure-saas-apps.md)
#### [B2B 사용자 클레임 매핑](b2b/claims-mapping.md)
#### [B2B 공동 작업 및 B2C 비교](b2b/compare-with-b2c.md)
#### [B2B에 대한 지원 받기](b2b/get-support.md)

## [그룹 및 구성원 관리](active-directory-manage-groups.md)
### 그룹 관리
#### [Azure Portal](active-directory-groups-create-azure-portal.md)
#### [Azure PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
### [그룹 구성원 관리](active-directory-groups-members-azure-portal.md)
### [그룹 소유자 관리](active-directory-accessmanagement-managing-group-owners.md)
### [그룹 멤버 자격 관리](active-directory-groups-membership-azure-portal.md)
### [그룹을 사용하여 라이선스 할당](active-directory-licensing-whatis-azure-portal.md)
#### [그룹에 라이선스 할당](active-directory-licensing-group-assignment-azure-portal.md)
#### [그룹에서 라이선스 문제 식별 및 해결](active-directory-licensing-group-problem-resolution-azure-portal.md)
#### [개별 라이선스 사용자를 그룹 기반 라이선스로 마이그레이션](active-directory-licensing-group-migration-azure-portal.md)
#### [그룹 기반 라이선스에 대한 추가 시나리오](active-directory-licensing-group-advanced.md)
#### [그룹 기반 라이선스에 대한 Azure PowerShell 예제](active-directory-licensing-ps-examples.md)
#### [Azure AD의 제품 및 서비스 계획을 위한 참조](active-directory-licensing-product-and-service-plan-reference.md)
### [Office 365 그룹 만료 설정](active-directory-groups-lifecycle-azure-portal.md)
### [모든 그룹 보기](active-directory-groups-view-azure-portal.md)
### [SaaS 앱에 대한 그룹 액세스 추가](active-directory-accessmanagement-group-saasapps.md)
### [삭제된 Office 365 그룹 복원](active-directory-groups-restore-azure-portal.md)
### 그룹 설정 관리
#### [Azure Portal](active-directory-groups-settings-azure-portal.md)
#### [Cmdlet](active-directory-accessmanagement-groups-settings-cmdlets.md)
### 고급 규칙 만들기
#### [Azure Portal](active-directory-groups-dynamic-membership-azure-portal.md)
### [셀프 서비스 그룹 설정](active-directory-accessmanagement-self-service-group-management.md)
### [문제 해결](active-directory-accessmanagement-troubleshooting.md)

## [보고서 관리](active-directory-reporting-azure-portal.md)
### [로그인 활동](active-directory-reporting-activity-sign-ins.md)
### [감사 활동](active-directory-reporting-activity-audit-logs.md)
### [위험에 노출된 사용자](active-directory-reporting-security-user-at-risk.md)
### [위험한 로그인](active-directory-reporting-security-risky-sign-ins.md)
### [위험 이벤트](active-directory-reporting-risk-events.md)
### [FAQ](active-directory-reporting-faq.md)
### 작업
#### [명명된 위치 구성](active-directory-named-locations.md)
#### [활동 보고서 보기](active-directory-reporting-migration.md)
#### [Azure Active Directory Power BI 콘텐츠 팩 사용](active-directory-reporting-power-bi-content-pack-how-to.md)
### 참고 자료
#### [보존](active-directory-reporting-retention.md)
#### [대기 시간](active-directory-reporting-latencies-azure-portal.md)
#### [Notifications](active-directory-reporting-notifications.md)
#### [감사 활동 참조](active-directory-reporting-activity-audit-reference.md)
#### [로그인 작업 오류 코드](active-directory-reporting-activity-sign-ins-errors.md)
#### [Multi-Factor Authentication](active-directory-reporting-activity-sign-ins-mfa.md)
### 문제 해결
#### [누락된 감사 데이터](active-directory-reporting-troubleshoot-missing-audit-data.md)
#### [다운로드에서 누락된 데이터](active-directory-reporting-troubleshoot-missing-data-download.md)
#### [Azure Active Directory 활동 로그 콘텐츠 팩 오류](active-directory-reporting-troubleshoot-content-pack.md)
### [프로그래밍 방식 액세스](active-directory-reporting-api-getting-started-azure-portal.md)
#### [감사 참조](active-directory-reporting-api-audit-reference.md)
#### [로그인 참조](active-directory-reporting-api-sign-in-activity-reference.md)
#### [필수 구성 요소](active-directory-reporting-api-prerequisites-azure-portal.md)
#### [감사 샘플](active-directory-reporting-api-audit-samples.md)
#### [로그인 샘플](active-directory-reporting-api-sign-in-activity-samples.md)
#### [인증서 사용](active-directory-reporting-api-with-certificates.md)

## 암호 관리
### [암호 개요](authentication/active-directory-passwords-overview.md)
### 사용자 문서
#### [암호 재설정 또는 변경](active-directory-passwords-update-your-own-password.md)
#### [암호 모범 사례](active-directory-secure-passwords.md)
#### [셀프 서비스 암호 재설정 등록](active-directory-passwords-reset-register.md)
### [SSPR 작동 방법](authentication/concept-sspr-howitworks.md)
### [SSPR 배포 가이드](authentication/howto-sspr-deployment.md)
### [SSPR 및 Windows 10](authentication/tutorial-sspr-windows.md)
### [SSPR 정책](authentication/concept-sspr-policy.md)
### [SSPR 사용자 지정](authentication/concept-sspr-customization.md)
### [SSPR 데이터 요구 사항](authentication/howto-sspr-authenticationdata.md)
### [SSPR 보고](authentication/howto-sspr-reporting.md)
### IT 관리자: 암호 재설정
#### [Azure Portal](active-directory-users-reset-password-azure-portal.md)
### [라이선스 SSPR](authentication/concept-sspr-licensing.md)
### [비밀번호 쓰기 저장](authentication/howto-sspr-writeback.md)
### [문제 해결](authentication/active-directory-passwords-troubleshoot.md)
### [FAQ](authentication/active-directory-passwords-faq.md)


## 장치 관리
### [소개](device-management-introduction.md)
### [Azure Portal 사용](device-management-azure-portal.md)
### [Azure AD 조인 계획](active-directory-azureadjoin-deployment-aadjoindirect.md)
### [FAQ](device-management-faq.md)
### 작업
#### [Azure AD 등록 Windows 10 장치 설정](device-management-azuread-registered-devices-windows10-setup.md)
#### [Azure AD 가입 장치 설정](device-management-azuread-joined-devices-setup.md)
#### [하이브리드 Azure AD 가입 장치 설정](device-management-hybrid-azuread-joined-devices-setup.md)
#### [온-프레미스 배포](active-directory-device-registration-on-premises-setup.md)
#### [Windows 10 첫 실행 경험 중 Azure AD 조인](device-management-azuread-joined-devices-frx.md)
### 문제 해결
#### [하이브리드 Azure AD 가입 Windows 10 및 Windows Server 2016 장치](device-management-troubleshoot-hybrid-join-windows-current.md)
#### [하이브리드 Azure AD 가입 레거시 Windows 장치](device-management-troubleshoot-hybrid-join-windows-legacy.md)

## 앱 관리
### [개요](manage-apps/what-is-application-management.md)
### [시작](manage-apps/plan-an-application-integration.md)
### [SaaS 앱 통합 자습서](active-directory-saas-tutorial-list.md)
### [클라우드 앱 검색](manage-apps/cloud-app-discovery.md)
#### [스냅숏 보고서 만들기](manage-apps/cloud-app-discovery-create-snapshot-reports.md)
#### [연속 보고 구성](https://docs.microsoft.com/cloud-app-security/discovery-docker)
#### [사용자 지정 로그 구문 분석기 사용](https://docs.microsoft.com/cloud-app-security/custom-log-parser)


### [앱 프록시를 사용하여 원격으로 앱에 액세스](manage-apps/application-proxy.md)
#### 시작하기
##### [앱 프록시 사용](manage-apps/application-proxy-enable.md)
##### [앱 게시](manage-apps/application-proxy-publish-azure-portal.md)
##### [사용자 지정 도메인](manage-apps/application-proxy-configure-custom-domain.md)
#### [Single Sign-On](manage-apps/application-proxy-single-sign-on.md)
##### [KCD와 SSO](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
##### [헤더와 SSO](manage-apps/application-proxy-configure-single-sign-on-with-ping-access.md)
##### [암호 보관을 사용하는 SSO](manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md)
#### 개념
##### [커넥터](manage-apps/application-proxy-connectors.md)
##### [보안](manage-apps/application-proxy-security.md)
##### [네트워크](manage-apps/application-proxy-network-topology.md)


##### [TMG 또는 UAG에서 업그레이드](manage-apps/application-proxy-migration.md)

#### 고급 구성
##### [별도 네트워크에 게시](manage-apps/application-proxy-connector-groups.md)
##### [프록시 서버](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)
##### [클레임 인식 앱](manage-apps/application-proxy-configure-for-claims-aware-applications.md)
##### [네이티브 클라이언트 앱](manage-apps/application-proxy-configure-native-client-application.md)
##### [자동 설치](manage-apps/application-proxy-register-connector-powershell.md)
##### [사용자 지정 홈 페이지](manage-apps/application-proxy-configure-custom-home-page.md)
##### [인라인 링크 변환](manage-apps/application-proxy-configure-hard-coded-link-translation.md)
#### 게시 연습
##### [원격 데스크톱](manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
##### [SharePoint](manage-apps/application-proxy-integrate-with-sharepoint-server.md)
##### [Microsoft 팀](application-proxy-teams.md)
##### [Tableau](active-directory-application-proxy-tableau.md)

#### [문제 해결](active-directory-application-proxy-troubleshoot.md)

### 엔터프라이즈 앱 관리
#### [사용자 할당](active-directory-coreapps-assign-user-azure-portal.md)
#### [브랜딩 사용자 지정](active-directory-coreapps-change-app-logo-user-azure-portal.md)
#### [사용자 로그인 비활성화](active-directory-coreapps-disable-app-azure-portal.md)
#### [사용자 제거](active-directory-coreapps-remove-assignment-azure-portal.md)
#### [내 앱 모두 보기](active-directory-coreapps-view-azure-portal.md)
#### [사용자 계정 프로비전 관리](active-directory-enterprise-apps-manage-provisioning.md)
#### [엔터프라이즈 앱에 대한 Single Sign-On 관리](active-directory-enterprise-apps-manage-sso.md)
#### [SAML 앱에 대한 고급 인증서 서명](active-directory-enterprise-apps-advance-certificate-options.md)
#### [사용자의 환경에서 타사 앱 숨기기](active-directory-coreapps-hide-third-party-app.md)
### [HRD 정책을 사용하여 로그인 자동 가속 구성](active-directory-auto-acceleration-using-hrd.md)

### [앱에 대한 액세스 관리](active-directory-managing-access-to-apps.md)
#### [SSO 액세스](manage-apps/what-is-single-sign-on.md)
#### [SSO 인증서](active-directory-sso-certs.md)
#### [테넌트 제한 사항](active-directory-tenant-restrictions.md)
#### [SCIM 프로비전 사용자 사용](active-directory-scim-provisioning.md)

### [문제 해결](active-directory-application-troubleshoot-content-map.md)
#### [응용 프로그램 개발](active-directory-application-dev-troubleshoot-content-map.md)
##### [구성 및 등록](active-directory-application-dev-config-content-map.md)
##### [개발](active-directory-application-dev-development-content-map.md)
#### [응용 프로그램 관리](active-directory-application-management-troubleshoot-content-map.md)
##### [구성](active-directory-application-config-content-map.md)
##### [로그인](active-directory-application-sign-in-content-map.md)
##### [프로비전](active-directory-application-provisioning-content-map.md)
##### [액세스 관리](active-directory-application-access-content-map.md)
##### [액세스 패널](active-directory-application-access-panel-content-map.md)
##### [응용 프로그램 프록시](active-directory-application-proxy-content-map.md)
##### [조건부 액세스](active-directory-application-conditional-access-content-map.md)
### [앱 개발](active-directory-applications-guiding-developers-for-lob-applications.md)
### [문서 라이브러리](active-directory-apps-index.md)

## 디렉터리 관리
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### 사용자 지정 도메인 이름
#### [빠른 시작](add-custom-domain.md)
#### [사용자 지정 도메인 이름 추가](active-directory-domains-manage-azure-portal.md)
### [디렉터리 관리](active-directory-administer.md)
### [여러 디렉터리](active-directory-licensing-directory-independence.md)
### [셀프 서비스 등록](active-directory-self-service-signup.md)
### [관리되지 않는 디렉터리 인수](domains-admin-takeover.md)
### [엔터프라이즈 상태 로밍](active-directory-windows-enterprise-state-roaming-overview.md)
#### [사용](active-directory-windows-enterprise-state-roaming-enable.md)
#### [그룹 정책 설정](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
#### [Windows 10 설정](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
#### [FAQ](active-directory-windows-enterprise-state-roaming-faqs.md)
#### [문제 해결](active-directory-windows-enterprise-state-roaming-troubleshooting.md)


### [Azure AD Connect를 사용하여 온-프레미스 ID 통합](./connect/active-directory-aadconnect.md)

## [Azure에 대한 액세스 관리](../role-based-access-control/toc.yml)

## 리소스에 대한 액세스 위임
### [관리자 역할](active-directory-assign-admin-roles-azure-portal.md)
#### [관리자 역할 할당](active-directory-users-assign-role-azure-portal.md)
#### [기본 사용자 권한](users-default-permissions.md)
### [관리 단위](active-directory-administrative-units-management.md)
### [토큰 수명](active-directory-configurable-token-lifetimes.md)
### [긴급 액세스 관리 계정 관리](active-directory-admin-manage-emergency-access-accounts.md)
### [권한 있는 역할 보안](admin-roles-best-practices.md)

## 액세스 검토
### [액세스 검토 개요](active-directory-azure-ad-controls-access-reviews-overview.md)
### [액세스 검토 완료](active-directory-azure-ad-controls-complete-access-review.md)
### [액세스 검토 만들](active-directory-azure-ad-controls-create-access-review.md)
### [액세스 검토 수행 방법](active-directory-azure-ad-controls-perform-access-review.md)
### [액세스 검토 방법](active-directory-azure-ad-controls-how-to-review-your-access.md)
### [액세스 검토로 게스트 액세스](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
### [검토로 사용자 액세스 관리](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
### [프로그램 및 컨트롤 관리](active-directory-azure-ad-controls-manage-programs-controls.md)
### [액세스 검토 결과 검색](active-directory-azure-ad-controls-retrieve-access-review.md)

## ID 보안
### [조건부 액세스](active-directory-conditional-access-azure-portal.md)
#### [조건](active-directory-conditional-access-conditions.md)
#### [위치 조건](active-directory-conditional-access-locations.md)
#### [컨트롤](active-directory-conditional-access-controls.md)
#### [시작](active-directory-conditional-access-azure-portal-get-started.md)
#### [모범 사례](active-directory-conditional-access-best-practices.md)
#### [Office 365 서비스에 대한 장치 정책 이해](active-directory-conditional-access-device-policies.md)
#### [클래식 정책 마이그레이션](active-directory-conditional-access-migration.md)
#### [가상 도구](active-directory-conditional-access-whatif.md)
#### 빠른 시작
##### [클라우드 앱 MFA별 구성](active-directory-conditional-access-app-based-mfa.md)
#### 작업
##### [클래식 MFA 정책 마이그레이션](active-directory-conditional-access-migration-mfa.md)
##### [장치 기반 조건부 액세스 설정](active-directory-conditional-access-policy-connected-applications.md)
##### [앱 기반 조건부 액세스 설정](active-directory-conditional-access-mam.md)
##### [사용자 및 앱에 대한 사용 약관 제공](active-directory-tou.md)
##### [VPN 연결 설정](active-directory-conditional-access-vpn-connectivity-windows10.md)
##### [SharePoint 및 Exchange Online 설정](active-directory-conditional-access-no-modern-authentication.md)
##### [재구성](active-directory-conditional-access-device-remediation.md)
#### [기술 참조](active-directory-conditional-access-technical-reference.md)
#### [FAQ](active-directory-conditional-faqs.md)

### Windows Hello
#### [암호 없이 인증](active-directory-azureadjoin-passport.md)
#### [비즈니스용 Windows Hello 사용](active-directory-azureadjoin-passport-deployment.md)
### 인증서 기반 인증
#### [Android](active-directory-certificate-based-authentication-android.md)
#### [iOS](active-directory-certificate-based-authentication-ios.md)
#### [시작](active-directory-certificate-based-authentication-get-started.md)

### [Azure AD ID 보호](active-directory-identityprotection.md)
#### [사용](active-directory-identityprotection-enable.md)
#### [취약점 감지](active-directory-identityprotection-vulnerabilities.md)
#### [위험 이벤트](active-directory-identity-protection-risk-events.md)
#### [Notifications](active-directory-identityprotection-notifications.md)
#### [로그인 환경](active-directory-identityprotection-flows.md)
#### [위험 이벤트 시뮬레이션](active-directory-identityprotection-playbook.md)
#### [사용자 차단 해제](active-directory-identityprotection-unblock-howto.md)
#### [FAQ](active-directory-identity-protection-faqs.md)
#### [용어](active-directory-identityprotection-glossary.md)
#### [Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)
### [Privileged Identity Management](active-directory-privileged-identity-management-configure.md)

## [Azure AD와 다른 서비스 통합]()
### [LinkedIn 통합 사용](linkedin-integration.md)

## [Azure에서 AD FS 배포](active-directory-aadconnect-azure-adfs.md)
### [고가용성](active-directory-adfs-in-azure-with-azure-traffic-manager.md)
### [서명 해시 알고리즘 변경](active-directory-federation-sha256-guidance.md)

## [문제 해결](active-directory-troubleshooting-support-howto.md)

## Azure AD 개념 증명(PoC) 배포
### [PoC 플레이 북: 소개](active-directory-playbook-intro.md)
### [PoC 플레이 북: 재료](active-directory-playbook-ingredients.md)
### [PoC 플레이 북: 구현](active-directory-playbook-implementation.md)
### [PoC 플레이 북: 구성 요소](active-directory-playbook-building-blocks.md)


# 참고 자료
## [코드 샘플](https://azure.microsoft.com/resources/samples/?service=active-directory)
## [Azure PowerShell cmdlet](/powershell/azure/overview)
## [Java API 참조](/java/api)
## [.NET API](/active-directory/adal/microsoft.identitymodel.clients.activedirectory)
## [서비스 제한 및 제한 사항](active-directory-service-limits-restrictions.md)

# 관련 항목
## [Multi-Factor Authentication](/azure/multi-factor-authentication/)
## [Azure AD Connect](./connect/active-directory-aadconnect.md)
## [Azure AD Connect Health](./connect-health/active-directory-aadconnect-health.md)
## [개발자용 Azure AD](./develop/active-directory-how-to-integrate.md)
## [Azure AD Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

# 리소스
## [Azure 피드백 포럼](https://feedback.azure.com/forums/169401-azure-active-directory)
## [Azure 로드맵](https://azure.microsoft.com/roadmap/?category=security-identity)
## [MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [가격](https://azure.microsoft.com/pricing/details/active-directory/)
## [요금 계산기](https://azure.microsoft.com/pricing/calculator/)
## [서비스 업데이트](https://azure.microsoft.com/updates/?product=active-directory)
## [스택 오버플로](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [비디오](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
