---
title: Azure AD 驗證 & 授權錯誤碼
description: 了解從 Azure AD 安全性權杖服務 (STS) 所傳回的 AADSTS 錯誤碼。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: reference
ms.date: 04/30/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 4bd738197c84d7dce36f087d170f61a55d8e9f32
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93241322"
---
# <a name="azure-ad-authentication-and-authorization-error-codes"></a>Azure AD 驗證與授權錯誤碼 \(部分機器翻譯\)

正在尋找有關從 Azure AD 安全性權杖服務 (STS) 所傳回 AADSTS 錯誤碼的資訊嗎？ 請閱讀此文件，以尋找 AADSTS 錯誤描述、修正程式，以及一些建議的因應措施。

> [!NOTE]
> 此資訊為初步資訊，隨時可能變更。 有任何疑問或找不到您要尋找的內容嗎？ 建立 GitHub 問題，或參閱[適用於開發人員的支援和協助選項](./developer-support-help-options.md)，了解您可以取得協助和支援的其他方法。
>
> 本檔適用于開發人員和系統管理員的指引，但絕對不能供用戶端使用。 錯誤碼隨時可能會變更，以提供更細微的錯誤訊息，旨在協助開發人員建立其應用程式。 相依于文字或錯誤碼編號的應用程式將會在一段時間內中斷。

## <a name="handling-error-codes-in-your-application"></a>處理應用程式中的錯誤碼

[Oauth 2.0 規格](https://tools.ietf.org/html/rfc6749#section-5.2)提供如何在驗證期間使用錯誤回應的部分處理錯誤的指引 `error` 。 

以下是錯誤回應的範例：

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://example.contoso.com/activity.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7", 
  "error_uri":"https://login.microsoftonline.com/error?code=70011"
}
```

| 參數         | Description    |
|-------------------|----------------|
| `error`       | 錯誤碼字串，可用來分類所發生錯誤的類型，並使用該字串來回應錯誤。 |
| `error_description` | 協助開發人員識別驗證錯誤根本原因的特定錯誤訊息。 請勿使用這個欄位來回應程式碼中的錯誤。 |
| `error_codes` | 有助於診斷的 STS 特定錯誤碼清單。  |
| `timestamp`   | 發生錯誤的時間。 |
| `trace_id`    | 有助於診斷的要求唯一識別碼。 |
| `correlation_id` | 有助於跨元件診斷的要求唯一識別碼。 |
| `error_uri` |  錯誤查閱頁面的連結，其中包含錯誤的其他相關資訊。  這僅供開發人員使用，請勿將它呈現給使用者。  只有在錯誤查閱系統有關于錯誤的其他資訊時才會出現，而不是所有錯誤都有提供其他資訊。|

此 `error` 欄位有幾個可能的值-請參閱通訊協定檔連結和 OAuth 2.0 規格，以深入瞭解特定錯誤 (例如， `authorization_pending` 在 [裝置程式碼流程](v2-oauth2-device-code.md) 中) 以及如何對其做出反應。  以下列出一些常見的專案：

| 錯誤碼         | 描述        | 用戶端動作    |
|--------------------|--------------------|------------------|
| `invalid_request`  | 通訊協定錯誤，例如遺漏必要的參數。 | 修正並重新提交要求。|
| `invalid_grant`    | 某些驗證資料 (驗證碼、重新整理權杖、存取權杖、PKCE 挑戰) 無效、無法剖析、遺失或無法使用 | 嘗試對端點提出新的要求 `/authorize` ，以取得新的授權碼。  請考慮檢查並驗證應用程式的通訊協定使用。 |
| `unauthorized_client` | 未授權驗證用戶端使用此授權授與類型。 | 這通常會在用戶端應用程式未在 Azure AD 中註冊，或未加入至使用者的 Azure AD 租用戶時發生。 應用程式可以對使用者提示關於安裝應用程式，並將它加入至 Azure AD 的指示。 |
| `invalid_client` | 用戶端驗證失敗。  | 用戶端認證無效。 若要修正，應用程式系統管理員必須更新認證。   |
| `unsupported_grant_type` | 授權伺服器不支援授權授與類型。 | 變更要求中的授與類型。 這種類型的錯誤應該只會在開發期間發生，並且會在初始測試期間偵測出來。 |
| `invalid_resource` | 目標資源無效，因為它不存在、Azure AD 無法找到它，或是它並未正確設定。 | 這表示尚未在租用戶中設定資源 (如果存在)。 應用程式可以對使用者提示關於安裝應用程式，並將它加入至 Azure AD 的指示。  在開發期間，這通常表示未正確設定測試租使用者，或所要求的範圍名稱中出現錯誤。 |
| `interaction_required` | 要求需要使用者互動。 例如，必須進行其他驗證步驟。 | 以互動方式使用相同的資源重試要求，讓使用者可以完成任何所需的挑戰。  |
| `temporarily_unavailable` | 伺服器暫時過於忙碌而無法處理要求。 | 重試要求。 用戶端應用程式可能會向使用者解釋其回應因暫時性狀況而延遲。 |

## <a name="lookup-current-error-code-information"></a>查閱目前的錯誤碼資訊
錯誤碼和訊息可能會變更。  如需最新資訊，請查看 [https://login.microsoftonline.com/error](https://login.microsoftonline.com/error) 頁面以尋找 AADSTS 錯誤描述、修正，以及一些建議的因應措施。  

搜尋傳回錯誤碼的數值部分。  例如，如果您收到錯誤碼 "AADSTS16000"，請在中搜尋 [https://login.microsoftonline.com/error](https://login.microsoftonline.com/error) "16000"。  您也可以直接連結到特定的錯誤，方法是將錯誤碼號碼新增至 URL： [https://login.microsoftonline.com/error?code=16000](https://login.microsoftonline.com/error?code=16000) 。

## <a name="aadsts-error-codes"></a>AADSTS 錯誤碼

| 錯誤 | 描述 |
|---|---|
| AADSTS16000 | SelectUserAccount - 這是 Azure AD 所擲回的插斷，會導致 UI 允許使用者從多個有效的 SSO 工作階段進行選取。 此錯誤很常見，如果指定 `prompt=none`，則可能會傳回給應用程式。 |
| AADSTS16001 | UserAccountSelectionInvalid - 如果使用者按一下工作階段選取邏輯已遭拒絕的圖格，則會看到此錯誤。 若已觸發，此錯誤可讓使用者藉由從已更新的圖格/工作階段清單進行挑選，或選擇另一個帳戶來復原。 此錯誤可能因為程式碼缺失或競爭條件而發生。 |
| AADSTS16002 | AppSessionSelectionInvalid - 不符合應用程式指定的 SID 需求。  |
| AADSTS16003 | SsoUserAccountNotFoundInResourceTenant - 表示使用者尚未明確新增至租用戶。 |
| AADSTS17003 | CredentialKeyProvisioningFailed - Azure AD 無法佈建使用者金鑰。 |
| AADSTS20001 | WsFedSignInResponseError - 同盟識別提供者有問題。 請連絡 IDP 以解決此問題。 |
| AADSTS20012 | WsFedMessageInvalid - 同盟識別提供者有問題。 請連絡 IDP 以解決此問題。 |
| AADSTS20033 | FedMetadataInvalidTenantName - 同盟識別提供者有問題。 請連絡 IDP 以解決此問題。 |
| AADSTS40008 | OAuth2IdPUnretryableServerError - 同盟識別提供者有問題。 請連絡 IDP 以解決此問題。 |
| AADSTS40009 | OAuth2IdPRefreshTokenRedemptionUserError - 同盟識別提供者有問題。 請連絡 IDP 以解決此問題。 |
| AADSTS40010 | OAuth2IdPRetryableServerError - 同盟識別提供者有問題。 請連絡 IDP 以解決此問題。 |
| AADSTS40015 | OAuth2IdPAuthCodeRedemptionUserError - 同盟識別提供者有問題。 請連絡 IDP 以解決此問題。 |
| AADSTS50000 | TokenIssuanceError - 登入服務有問題。 請[開啟支援票證](../fundamentals/active-directory-troubleshooting-support-howto.md)以解決此問題。 |
| AADSTS50001 | InvalidResource - 資源已停用或不存在。 請檢查您應用程式的程式碼，確保您已經為您嘗試存取的資源指定確切的資源 URL。  |
| AADSTS50002 | NotAllowedTenant - 登入失敗，因為租用戶上的 Proxy 存取受限。 如果此為您自己的租用戶原則，則可變更受限的租用戶設定以解決此問題。 |
| AADSTS50003 | MissingSigningKey - 登入失敗，因為遺漏簽署金鑰或憑證。 這可能是因為應用程式中未設定任何簽署金鑰。 查看此處概述的 [解決方案。未設定/manage-apps/application-sign-in-problem-federated-sso-gallery.md # 憑證或金鑰](../manage-apps/application-sign-in-problem-federated-sso-gallery.md#certificate-or-key-not-configured)。 如果還是有問題，請連絡應用程式擁有者或應用程式管理員。 |
| AADSTS50005 | DevicePolicyError-使用者嘗試透過條件式存取原則從目前不支援的平臺登入裝置。 |
| AADSTS50006 | InvalidSignature - 簽章驗證失敗，因為簽章無效。 |
| AADSTS50007 | PartnerEncryptionCertificateMissing - 找不到此應用程式的夥伴加密憑證。 請向 Microsoft [開啟支援票證](../fundamentals/active-directory-troubleshooting-support-howto.md)以修正此問題。 |
| AADSTS50008 | InvalidSamlToken - 權杖中的 SAML 判斷提示遺漏或設定不正確。 請連絡同盟提供者。 |
| AADSTS50010 | AudienceUriValidationFailed - 應用程式的對象 URI 驗證失敗，因為未設定權杖對象。 |
| AADSTS50011 | InvalidReplyTo - 回覆地址遺漏、設定不正確或不符合針對應用程式所設定的回覆地址。  解決方法是確保將此遺漏的回復位址新增至 Azure Active Directory 應用程式，或讓具有管理應用程式許可權的人員 Active Directory 為您完成此動作。|
| AADSTS50012 | AuthenticationFailed - 驗證因為下列其中一個原因而失敗：<ul><li>簽署憑證的主旨名稱未獲授權</li><li>已授權的主體名稱找不到相符的受信任授權原則</li><li>憑證鏈結無效</li><li>簽署憑證無效</li><li>未在租用戶上設定原則</li><li>簽署憑證的指紋未獲授權</li><li>用戶端判斷提示包含無效的簽章</li></ul> |
| AADSTS50013 | InvalidAssertion - 判斷提示因為各種原因而無效：權杖簽發者不符合其有效時間範圍內的 API 版本；過期；格式不正確；判斷提示中的重新整理權杖不是主要的重新整理權杖。 |
| AADSTS50014 | GuestUserInPendingState - 使用者的兌換處於擱置狀態。 來賓使用者帳戶建立尚未完成。 |
| AADSTS50015 | ViralUserLegalAgeConsentRequiredState - 使用者需要法定年齡層同意。 |
| AADSTS50017 | CertificateValidationFailed - 憑證驗證失敗，原因如下：<ul><li>在受信任的憑證清單中找不到發行憑證</li><li>找不到預期的 CrlSegment</li><li>在受信任的憑證清單中找不到發行憑證</li><li>在設定差異 CRL 發佈點時未指定對應的 CRL 發佈點</li><li>由於逾時問題而無法擷取有效的 CRL 區段</li><li>無法下載 CRL</li></ul>請連絡租用戶管理員。 |
| AADSTS50020 | UserUnauthorized - 使用者未獲授權呼叫此端點。 |
| AADSTS50027 | InvalidJwtToken - JWT 權杖無效，原因如下：<ul><li>未包含 nonce 宣告、子宣告</li><li>主旨識別碼不相符</li><li>idToken 宣告中的宣告重複</li><li>未預期的簽發者</li><li>未預期的對象</li><li>不在其有效時間範圍內 </li><li>權杖格式不正確</li><li>來自簽發者的外部 ID 權杖未能通過簽章驗證。</li></ul> |
| AADSTS50029 | URI 無效 - 網域名稱包含無效字元。 請連絡租用戶管理員。 |
| AADSTS50032 | WeakRsaKey - 表示錯誤的使用者嘗試使用弱式 RSA 金鑰。 |
| AADSTS50033 | RetryableError - 表示與資料庫作業無關的暫時性錯誤。 |
| AADSTS50034 | UserAccountNotFound - 若要登入此應用程式，必須將帳戶新增至目錄。 |
| AADSTS50042 | UnableToGeneratePairwiseIdentifierWithMissingSalt-主體中遺漏產生成對識別碼所需的 salt。 請連絡租用戶管理員。 |
| AADSTS50043 | UnableToGeneratePairwiseIdentifierWithMultipleSalts |
| AADSTS50048 | SubjectMismatchesIssuer - 主體與用戶端判斷提示中的簽發者宣告不符。 請連絡租用戶管理員。 |
| AADSTS50049 | NoSuchInstanceForDiscovery - 未知或無效的執行個體。 |
| AADSTS50050 | MalformedDiscoveryRequest - 要求格式不正確。 |
| AADSTS50053 | IdsLocked - 帳戶遭到鎖定，因為使用者嘗試使用不正確的使用者識別碼或密碼登入太多次。 |
| AADSTS50055 | InvalidPasswordExpiredPassword - 密碼已過期。 |
| AADSTS50056 | 密碼無效或為 Null - 存放區中沒有此使用者的密碼。 |
| AADSTS50057 | UserDisabled - 使用者帳戶已停用。 系統管理員已停用使用者帳戶。 |
| AADSTS50058 | UserInformationNotProvided - 這表示使用者並未登入。 這是常見的錯誤，當使用者未經驗證且尚未登入時就會發生。</br>如果使用者先前登入的 SSO 內容中促成此錯誤，這表示 SSO 工作階段找不到或無效。</br>如果已指定 prompt=none，此錯誤可能會傳回給應用程式。 |
| AADSTS50059 | MissingTenantRealmAndNoUserInformationProvided - 在要求中找不到租用戶識別資訊，或任何提供的認證均未隱含租用戶識別資訊。 使用者可以連絡租用戶管理員，協助解決問題。 |
| AADSTS50061 | SignoutInvalidRequest - 登出要求無效。 |
| AADSTS50064 | CredentialAuthenticationError - 使用者名稱或密碼上的認證驗證失敗。 |
| AADSTS50068 | SignoutInitiatorNotParticipant-登出失敗。 起始登出的應用程式不是目前會話中的參與者。 |
| AADSTS50070 | SignoutUnknownSessionIdentifier-登出失敗。 登出要求指定的名稱識別碼不符合現有的會話 (s) 。 |
| AADSTS50071 | SignoutMessageExpired - 登出要求已過期。 |
| AADSTS50072 | UserStrongAuthEnrollmentRequiredInterrupt - 使用者需要註冊第二個因素驗證 (互動式)。 |
| AADSTS50074 | UserStrongAuthClientAuthNRequiredInterrupt - 需要強大的驗證，而且使用者並未通過 MFA 挑戰。 |
| AADSTS50076 | UserStrongAuthClientAuthNRequired - 由於系統管理員所做的設定變更，或因為您已移至新位置，所以使用者必須使用多重要素驗證來存取資源。 使用資源的新授權要求進行重試。 |
| AADSTS50079 | UserStrongAuthEnrollmentRequired - 由於系統管理員所做的組態變更，或因為使用者已移至新位置，所以使用者需要使用多重要素驗證。 |
| AADSTS50085 | 重新整理權杖需要社交 IDP 登入。 讓使用者嘗試以使用者名稱/密碼重新登入 |
| AADSTS50086 | SasNonRetryableError |
| AADSTS50087 | SasRetryableError - 服務暫時無法使用。 然後再試一次。 |
| AADSTS50089 | 流程權杖過期 - 驗證失敗。 讓使用者嘗試以使用者名稱/密碼重新登入。 |
| AADSTS50097 | DeviceAuthenticationRequired - 需要裝置驗證。 |
| AADSTS50099 | PKeyAuthInvalidJwtUnauthorized - JWT 簽章無效。 |
| AADSTS50105 | EntitlementGrantsNotFound - 未將登入的使用者指派給所登入應用程式的角色。 請將使用者指派給應用程式。 如需詳細資訊：[.。。/manage-apps/application-sign-in-problem-federated-sso-gallery.md # 使用者-非指派-角色](../manage-apps/application-sign-in-problem-federated-sso-gallery.md#user-not-assigned-a-role)。 |
| AADSTS50107 | InvalidRealmUri - 所要求的同盟領域物件不存在。 請連絡租用戶管理員。 |
| AADSTS50120 | ThresholdJwtInvalidJwtFormat - JWT 標頭有問題。 請連絡租用戶管理員。 |
| AADSTS50124 | ClaimsTransformationInvalidInputParameter - 宣告轉換所包含的輸入參數無效。 請連絡租用戶管理員以更新原則。 |
| AADSTS50125 | PasswordResetRegistrationRequiredInterrupt - 因為密碼重設或密碼註冊項目，登入作業已中斷。 |
| AADSTS50126 | InvalidUserNameOrPassword - 驗證認證時發生錯誤，因為使用者名稱或密碼無效。 |
| AADSTS50127 | BrokerAppNotInstalled - 使用者必須安裝訊息代理程式應用程式來存取此內容。 |
| AADSTS50128 | 網域名稱無效 - 在要求中找不到租用戶識別資訊，或任何提供的認證均未隱含租用戶識別資訊。 |
| AADSTS50129 | DeviceIsNotWorkplaceJoined - 註冊裝置需加入工作場所網路。 |
| AADSTS50131 | ConditionalAccessFailed-指出各種條件式存取錯誤，例如 Windows 裝置狀態不良、要求因可疑活動、存取原則或安全性原則決策而遭到封鎖。 |
| AADSTS50132 | SsoArtifactInvalidOrExpired - 工作階段因為密碼到期或近期密碼變更而無效。 |
| AADSTS50133 | SsoArtifactRevoked - 工作階段因為密碼到期或近期密碼變更而無效。 |
| AADSTS50134 | DeviceFlowAuthorizeWrongDatacenter - 資料中心錯誤。 若要授權由 OAuth 2.0 裝置流程中的應用程式所起始的要求，授權方必須位於與原始要求所在相同的資料中心。 |
| AADSTS50135 | PasswordChangeCompromisedPassword - 帳戶有風險，因此必須變更密碼。 |
| AADSTS50136 | RedirectMsaSessionToApp - 偵測到單一 MSA 工作階段。 |
| AADSTS50139 | SessionMissingMsaOAuth2RefreshToken - 工作階段無效，因為遺漏外部重新整理權杖。 |
| AADSTS50140 | KmsiInterrupt - 由於在使用者登入時出現「讓我保持登入」插斷，所以發生此錯誤。 請使用相互關聯識別碼、要求識別碼和錯誤碼[開啟支援票證](../fundamentals/active-directory-troubleshooting-support-howto.md)，以取得更多詳細資料。 |
| AADSTS50143 | 工作階段不符 - 工作階段無效，原因是由於資源不同而導致使用者租用戶與網域提示不符。 請使用相互關聯識別碼、要求識別碼和錯誤碼[開啟支援票證](../fundamentals/active-directory-troubleshooting-support-howto.md)，以取得更多詳細資料。 |
| AADSTS50144 | InvalidPasswordExpiredOnPremPassword - 使用者的 Active Directory 密碼已到期。 為使用者產生新密碼，或讓使用者使用自助式重設工具來重設其密碼。 |
| AADSTS50146 | MissingCustomSigningKey - 在設定此應用程式時，必須使用應用程式專屬的簽署金鑰。 在設定應用程式時未使用這樣的金鑰，或該金鑰已過期或尚未生效。 |
| AADSTS50147 | MissingCodeChallenge - 程式碼挑戰參數大小無效。 |
| AADSTS50155 | DeviceAuthenticationFailed - 此使用者的裝置驗證失敗。 |
| AADSTS50158 | ExternalSecurityChallenge - 不符合外部安全性挑戰。 |
| AADSTS50161 | InvalidExternalSecurityChallengeConfiguration - 外部提供者所傳送的宣告不足，或是遺漏向外部提供者所要求的宣告。 |
| AADSTS50166 | ExternalClaimsProviderThrottled - 無法將要求傳送給宣告提供者。 |
| AADSTS50168 | ChromeBrowserSsoInterruptRequired - 用戶端能夠透過 Windows 10 帳戶擴充功能取得 SSO 權杖，但是在要求中找不到權杖，或提供的權杖已過期。 |
| AADSTS50169 | InvalidRequestBadRealm - 此領域不是當前服務命名空間所設定的領域。 |
| AADSTS50170 | MissingExternalClaimsProviderMapping - 遺漏外部控制項對應。 |
| AADSTS50177 | ExternalChallengeNotSupportedForPassthroughUsers - 未針對傳遞使用者支援外部挑戰。 |
| AADSTS50178 | SessionControlNotSupportedForPassthroughUsers - 未針對傳遞使用者支援工作階段控制。 |
| AADSTS50180 | WindowsIntegratedAuthMissing - 需要整合式 Windows 驗證。 為租用戶啟用無縫 SSO。 |
| AADSTS50187 | DeviceInformationNotProvided - 服務無法執行裝置驗證。 |
| AADSTS50196 | LoopDetected-偵測到用戶端迴圈。 檢查應用程式的邏輯，以確保會執行權杖快取，並正確地處理錯誤狀況。  應用程式在太短的一段時間內進行了太多的相同要求，表示它處於錯誤狀態或 abusively 要求權杖。 |
| AADSTS50197 | ConflictingIdentities-找不到使用者。 請嘗試重新登入。 |
| AADSTS50199 | CmsiInterrupt-基於安全考慮，此要求需要使用者確認。  因為這是「interaction_required」錯誤，所以用戶端應該進行互動式驗證。 發生這種情況是因為系統使用程式設計用來要求原生應用程式的權杖，所以必須提示使用者詢問這是否真的是他們想要登入的應用程式。 若要避免這個提示，重新導向 URI 應該是下列安全清單的一部分： <br />http://<br />https://<br />msauth:// (僅限 iOS) <br />msauthv2：// (僅限 iOS) <br />chrome-擴充：// (桌面 Chrome 瀏覽器僅)  |
| AADSTS51000 | RequiredFeatureNotEnabled - 此功能已停用。 |
| AADSTS51001 | DomainHintMustbePresent - 網域提示必須與內部部署安全性識別碼或 內部部署 UPN 一起顯示。 |
| AADSTS51004 | UserAccountNotInDirectory - 目錄中不存在使用者帳戶。 |
| AADSTS51005 | TemporaryRedirect - 相當於 HTTP 狀態 307，這表示所要求的資訊位於位置標頭中指定的 URI。 當您收到此狀態時，請遵循與回應相關聯的位置標頭。 當原始的要求方法是 POST 時，重新導向的要求也將使用 POST 方法。 |
| AADSTS51006 | ForceReauthDueToInsufficientAuth - 需要整合式 Windows 驗證。 使用遺漏整合式 Windows 驗證宣告的工作階段權杖來登入的使用者。 請要求使用者再次登入。 |
| AADSTS52004 | DelegationDoesNotExistForLinkedIn - 使用者尚未同意存取 LinkedIn 資源。 |
| AADSTS53000 | DeviceNotCompliant-條件式存取原則需要相容的裝置，且裝置不符合規範。 使用者必須向 Intune 等獲得核准的 MDM 提供者註冊其裝置。 |
| AADSTS53001 | DeviceNotDomainJoined-條件式存取原則需要已加入網域的裝置，且裝置未加入網域。 請使用者使用已加入網域的裝置。 |
| AADSTS53002 | ApplicationUsedIsNotAnApprovedApp-使用的應用程式不是條件式存取的核准應用程式。 使用者所使用的應用程式，必須是已核准使用的應用程式清單其中一個，才能取得存取權。 |
| AADSTS53003 | BlockedByConditionalAccess-條件式存取原則已封鎖存取。 存取原則不允許發行權杖。 |
| AADSTS53004 | ProofUpBlockedDueToRisk - 使用者必須先完成多重要素驗證註冊程序，才能存取此內容。 使用者應該註冊多重要素驗證。 |
| AADSTS54000 | MinorUserBlockedLegalAgeGroupRule |
| AADSTS65001 | DelegationDoesNotExist - 使用者或系統管理員尚未同意使用識別碼為 X 的應用程式。針對此使用者和資源傳送互動式授權要求。 |
| AADSTS65004 | UserDeclinedConsent - 使用者不同意存取應用程式。 讓使用者重試登入，並同意應用程式|
| AADSTS65005 | MisconfiguredApplication - 應用程式所需資源存取清單不包含資源可探索的應用程式，或用戶端應用程式已要求存取未在其所需資源存取清單中指定的資源，或 Graph 服務傳回不正確的要求或找不到資源。 如果應用程式支援 SAML，則可能是您在設定應用程式時所使用的識別碼 (實體) 不正確。 使用下列連結來試用針對 SAML 所列的解析： [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#no-resource-in-requiredresourceaccess-list](../manage-apps/application-sign-in-problem-federated-sso-gallery.md?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav) |
| AADSTS650052 | 應用程式需要存取 `(\"{name}\")` 您的組織尚未 `\"{organization}\"` 訂閱或啟用的服務。 請洽詢您的 IT 系統管理員，以檢查服務訂用帳戶的設定。 |
| AADSTS67003 | ActorNotValidServiceIdentity |
| AADSTS70000 | InvalidGrant - 驗證失敗。 重新整理權杖無效。 錯誤原因如下：<ul><li>權杖繫結標頭是空的</li><li>權杖繫結雜湊不相符</li></ul> |
| AADSTS70001 | UnauthorizedClient - 應用程式已停用。 |
| AADSTS70002 | InvalidClient - 驗證認證時發生錯誤。 指定的 client_secret 與此用戶端預期的值不符。 請修正 client_secret，然後再試一次。 如需詳細資訊，請參閱[使用授權碼來要求存取權杖](v2-oauth2-auth-code-flow.md#request-an-access-token)。 |
| AADSTS70003 | UnsupportedGrantType - 應用程式傳回的授與類型不受支援。 |
| AADSTS70004 | InvalidRedirectUri - 應用程式傳回的重新導向 URI 無效。 用戶端所指定的重新導向位址，與已設定的位址或 OIDC 核准清單上的位址全都不符。 |
| AADSTS70005 | UnsupportedResponseType - 應用程式傳回的回應類型不受支援，原因如下：<ul><li>未對應用程式啟用 'token' 回應類型</li><li>'id_token' 回應類型需要 'OpenID' 範圍 - 在編碼的 wctx 中包含不受支援的 OAuth 參數值</li></ul> |
| AADSTS70007 | UnsupportedResponseMode - 應用程式在要求權杖時傳回的 `response_mode` 值不受支援。  |
| AADSTS70008 | ExpiredOrRevokedGrant - 重新整理權杖因為非使用狀態而過期。 此權杖是在 XXX 發出，而且有一段時間為非使用中。 |
| AADSTS70011 | InvalidScope - 應用程式所要求的範圍無效。 |
| AADSTS70012 | MsaServerError - 在驗證 MSA (取用者) 使用者時，發生伺服器錯誤。 然後再試一次。 如果持續失敗，請[開啟支援票證](../fundamentals/active-directory-troubleshooting-support-howto.md) |
| AADSTS70016 | AuthorizationPending - OAuth 2.0 裝置流程錯誤。 授權已擱置。 裝置會重試輪詢要求。 |
| AADSTS70018 | BadVerificationCode - 驗證碼無效，因為使用者針對裝置碼流程所輸入的使用者代碼錯誤。 授權未獲核准。 |
| AADSTS70019 | CodeExpired - 驗證碼過期。 請讓使用者重試登入。 |
| AADSTS75001 | BindingSerializationError - SAML 訊息繫結期間發生錯誤。 |
| AADSTS75003 | UnsupportedBindingError - 應用程式傳回了與未支援繫結相關的錯誤 (無法透過 HTTP POST 以外的繫結傳送 SAML 通訊協定回應)。 |
| AADSTS75005 | Saml2MessageInvalid - Azure AD 不支援應用程式針對 SSO 傳送的 SAML 要求。 |
| AADSTS7500514 | 找不到支援的 SAML 回應類型。 支援的回應類型為 XML 命名空間 ' urn： oasis： names： tc： SAML：2.0： protocol ' ) 中的 ' Response ' (，或 XML 命名空間 ' urn： oasis： names： tc： SAML：2.0： assert ' ) 中的 ' Assertion ' (。 應用程式錯誤-開發人員將會處理此錯誤。|
| AADSTS75008 | RequestDeniedError - 應用程式所提出的要求遭拒，因為 SAML 要求有未預期的目的地。 |
| AADSTS75011 | NoMatchedAuthnContextInOutputClaims - 使用者用來向服務進行驗證的驗證方法，與所要求的驗證方法不符。 |
| AADSTS75016 | Saml2AuthenticationRequestInvalidNameIDPolicy - SAML2 驗證要求所具有的 NameIdPolicy 無效。 |
| AADSTS80001 | OnPremiseStoreIsNotAvailable - 驗證代理程式無法連線至 Active Directory。 確定代理程式伺服器和必須驗證其密碼的使用者都是相同 AD 樹系的成員，而且都能連線到 Active Directory。 |
| AADSTS80002 | OnPremisePasswordValidatorRequestTimedout-密碼驗證要求超時。請確定 Active Directory 可供使用，並且會回應來自代理程式的要求。 |
| AADSTS80005 | OnPremisePasswordValidatorUnpredictableWebException - 處理驗證代理程式所傳來的回應時，發生未知錯誤。 重試要求。 如果持續失敗，請[開啟支援票證](../fundamentals/active-directory-troubleshooting-support-howto.md)，以取得有關錯誤的詳細資料。 |
| AADSTS80007 | OnPremisePasswordValidatorErrorOccurredOnPrem - 驗證代理程式無法驗證使用者的密碼。 請檢查代理程式記錄以了解詳細資訊，並確認 Active Directory 如預期般運作。 |
| AADSTS80010 | OnPremisePasswordValidationEncryptionException - 驗證代理程式無法將密碼解密。 |
| AADSTS80012 | OnPremisePasswordValidationAccountLogonInvalidHours - 使用者在允許的時間外 (此值是在 AD 中指定的) 嘗試登入。 |
| AADSTS80013 | OnPremisePasswordValidationTimeSkew - 無法完成驗證嘗試，因為執行驗證代理程式的機器和 AD 之間有時間差異。 請修正時間同步問題。 |
| AADSTS81004 | DesktopSsoIdentityInTicketIsNotAuthenticated - Kerberos 驗證嘗試失敗。 |
| AADSTS81005 | DesktopSsoAuthenticationPackageNotSupported - 不支援驗證套件。 |
| AADSTS81006 | DesktopSsoNoAuthorizationHeader - 找不到授權標頭。 |
| AADSTS81007 | DesktopSsoTenantIsNotOptIn - 租用戶未啟用無縫 SSO。 |
| AADSTS81009 | DesktopSsoAuthorizationHeaderValueWithBadFormat - 無法驗證使用者的 Kerberos 票證。 |
| AADSTS81010 | DesktopSsoAuthTokenInvalid - 無縫式 SSO 失敗，因為使用者的 Kerberos 票證已過期或無效。 |
| AADSTS81011 | DesktopSsoLookupUserBySidFailed - 找不到以使用者的 Kerberos 票證中資訊為基礎的使用者物件。 |
| AADSTS81012 | DesktopSsoMismatchBetweenTokenUpnAndChosenUpn - 嘗試登入 Azure AD 的使用者與登入裝置的使用者不同。 |
| AADSTS90002 | InvalidTenantName - 資料存放區中找不到租用戶名稱。 檢查以確定您有正確的租用戶識別碼。 |
| AADSTS90004 | InvalidRequestFormat - 要求格式不正確。 |
| AADSTS90005 | InvalidRequestWithMultipleRequirements - 無法完成要求。 要求無效，因為不能同時使用識別碼和登入提示。  |
| AADSTS90006 | ExternalServerRetryableError - 服務暫時無法使用。|
| AADSTS90007 | InvalidSessionId - 要求不正確。 無法剖析傳遞的工作階段識別碼。 |
| AADSTS90008 | TokenForItselfRequiresGraphPermission - 使用者或系統管理員尚未同意使用應用程式。 應用程式至少需要透過指定登入和讀取使用者設定檔權限來存取 Azure AD。 |
| AADSTS90009 | TokenForItselfMissingIdenticalAppIdentifier - 應用程式正在為其本身要求權杖。 只有在指定的資源使用以 GUID 為基礎的應用程式識別碼時，才支援此案例。 |
| AADSTS90010 | NotSupported - 無法建立演算法。 |
| AADSTS90012 | RequestTimeout - 要求已逾時。 |
| AADSTS90013 | InvalidUserInput - 使用者的輸入無效。 |
| AADSTS90014 | MissingRequiredField - 當認證中未出現預期的欄位時，此錯誤碼可能會出現於各種情況。 |
| AADSTS90015 | QueryStringTooLong - 查詢字串太長。 |
| AADSTS90016 | MissingRequiredClaim - 存取權杖無效。 遺漏必要宣告。 |
| AADSTS90019 | MissingTenantRealm - Azure AD 無法判斷要求中的租用戶識別碼。 |
| AADSTS90022 | AuthenticatedInvalidPrincipalNameFormat - 主體名稱格式無效，或不符合預期的 `name[/host][@realm]` 格式。 主體名稱為必要項目，主機和領域是選擇性項目，而且可能設定為 null。 |
| AADSTS90023 | InvalidRequest - 驗證服務要求無效。 |
| AADSTS9002313 | InvalidRequest-要求格式不正確或無效。 -此處的問題是因為特定端點的要求發生問題。 此問題的建議是取得發生錯誤的 fiddler 追蹤，並查看要求的格式是否確實正確。 |
| AADSTS90024 | RequestBudgetExceededError - 發生暫時性錯誤。 然後再試一次。 |
| AADSTS90033 | MsodsServiceUnavailable - Microsoft Online 目錄服務 (MSODS) 無法使用。 |
| AADSTS90036 | MsodsServiceUnretryableFailure - MSODS 託管的 WCF 服務發生未預期且無法重試的錯誤。 請[開啟支援票證](../fundamentals/active-directory-troubleshooting-support-howto.md)，以取得有關錯誤的詳細資料。 |
| AADSTS90038 | NationalCloudTenantRedirection - 指定的租用戶 'Y' 屬於國家雲端 'X'。 目前的雲端執行個體 'Z' 不會與 X 同盟。傳回了雲端重新導向錯誤。 |
| AADSTS90043 | NationalCloudAuthCodeRedirection - 此功能已停用。 |
| AADSTS90051 | InvalidNationalCloudId - 全國性雲端識別碼包含無效的雲端識別碼。 |
| AADSTS90055 | TenantThrottlingError - 有太多傳入要求。 此例外狀況因為封鎖的租用戶而擲回。 |
| AADSTS90056 | BadResourceRequest - 若要兌換存取權杖的程式碼，應用程式應將 POST 要求傳送至 `/token` 端點。 此外，在此之前，您應該提供授權碼並以 POST 要求將它傳送到 `/token` 端點。 請參閱這篇文章，以瞭解 OAuth 2.0 授權碼流程的總覽： [.。。/azuread-dev/v1-protocols-oauth-code.md](../azuread-dev/v1-protocols-oauth-code.md)。 將使用者導向 `/authorize` 端點，這會傳回 authorization_code。 將要求張貼至 `/token` 端點，使用者就會取得存取權杖。 登入 Azure 入口網站，然後檢查 [應用程式註冊] > [端點]  ，確認已正確設定兩個端點。 |
| AADSTS90072 | PassThroughUserMfaError - 使用者登入的外部帳戶不存在於已登入的租用戶，因此使用者不符合租用戶的 MFA 需求。 如果使用者已同步處理，但 ImmutableID (sourceAnchor) 屬性 Active Directory 和 Azure AD 之間有不相符的情況，也可能會發生此錯誤。 必須先在租用戶中將帳戶新增為外部使用者。 請登出並使用其他 Azure AD 使用者帳戶登入。 |
| AADSTS90081 | OrgIdWsFederationMessageInvalid - 服務嘗試處理 WS-同盟訊息時發生錯誤。 訊息無效。 |
| AADSTS90082 | OrgIdWsFederationNotSupported - 目前不支援針對要求選取的驗證原則。 |
| AADSTS90084 | OrgIdWsFederationGuestNotAllowed - 此網站不允許來賓帳戶。 |
| AADSTS90085 | OrgIdWsFederationSltRedemptionFailed - 服務無法發行權杖，因為尚未佈建公司物件。 |
| AADSTS90086 | OrgIdWsTrustDaTokenExpired - 使用者 DA 權杖已過期。 |
| AADSTS90087 | OrgIdWsFederationMessageCreationFromUriFailed - 從 URI 建立 WS-同盟訊息時發生錯誤。 |
| AADSTS90090 | GraphRetryableError - 服務暫時無法使用。 |
| AADSTS90091 | GraphServiceUnreachable |
| AADSTS90092 | GraphNonRetryableError |
| AADSTS90093 | GraphUserUnauthorized - 傳回的圖表包含要求的禁止錯誤碼。 |
| AADSTS90094 | AdminConsentRequired - 需要系統管理員同意。 |
| AADSTS900382 | 跨雲端要求不支援機密用戶端。 |
| AADSTS90099 | 尚未授權租使用者 ' {tenant} ' 中的應用程式 ' {appId} ' ( {appName} ) 。 應用程式必須獲得授權，才能存取客戶租使用者，合作夥伴委派的系統管理員可以使用這些應用程式。 提供預先同意，或執行適當的合作夥伴中心 API 來授權應用程式。 |
| AADSTS90100 | InvalidRequestParameter - 參數空白或無效。 |
| AADSTS901002 | AADSTS901002：不支援 ' resource ' 要求參數。 |
| AADSTS90101 | InvalidEmailAddress - 所提供的資料不是有效的電子郵件地址。 電子郵件地址的格式必須是 `someone@example.com`。 |
| AADSTS90102 | InvalidUriParameter - 值必須是有效的絕對 URI。 |
| AADSTS90107 | InvalidXml - 要求無效。 請確定您的資料不會有無效的字元。|
| AADSTS90114 | InvalidExpiryDate - 大量權杖到期時間戳記會導致發行過期的權杖。 |
| AADSTS90117 | InvalidRequestInput |
| AADSTS90119 | InvalidUserCode - 使用者程式碼為 Null 或空白。|
| AADSTS90120 | InvalidDeviceFlowRequest - 已授權或拒絕要求。 |
| AADSTS90121 | InvalidEmptyRequest - 空白要求無效。|
| AADSTS90123 | IdentityProviderAccessDenied - 無法發行權杖，因為識別或宣告發行提供者已拒絕要求。 |
| AADSTS90124 | V1ResourceV2GlobalEndpointNotSupported - `/common` 或 `/consumers` 端點上不支援資源。 請改用 `/organizations` 或租用戶專屬端點。 |
| AADSTS90125 | DebugModeEnrollTenantNotFound - 使用者不在系統中。 請確定您輸入的使用者名稱正確。 |
| AADSTS90126 | DebugModeEnrollTenantNotInferred - 此端點上不支援使用者類型。 系統無法從使用者名稱推斷使用者的租用戶。 |
| AADSTS90130 | NonConvergedAppV2GlobalEndpointNotSupported - `/common` 或 `/consumers` 端點上不支援應用程式。 請改用 `/organizations` 或租用戶專屬端點。 |
| AADSTS120000 | PasswordChangeIncorrectCurrentPassword |
| AADSTS120002 | PasswordChangeInvalidNewPasswordWeak |
| AADSTS120003 | PasswordChangeInvalidNewPasswordContainsMemberName |
| AADSTS120004 | PasswordChangeOnPremComplexity |
| AADSTS120005 | PasswordChangeOnPremSuccessCloudFail |
| AADSTS120008 | PasswordChangeAsyncJobStateTerminated - 發生無法重試的錯誤。|
| AADSTS120011 | PasswordChangeAsyncUpnInferenceFailed |
| AADSTS120012 | PasswordChangeNeedsToHappenOnPrem |
| AADSTS120013 | PasswordChangeOnPremisesConnectivityFailure |
| AADSTS120014 | PasswordChangeOnPremUserAccountLockedOutOrDisabled |
| AADSTS120015 | PasswordChangeADAdminActionRequired |
| AADSTS120016 | PasswordChangeUserNotFoundBySspr |
| AADSTS120018 | PasswordChangePasswordDoesnotComplyFuzzyPolicy |
| AADSTS120020 | PasswordChangeFailure |
| AADSTS120021 | PartnerServiceSsprInternalServiceError |
| AADSTS130004 | NgcKeyNotFound - 使用者主體未設定 NGC 識別碼金鑰。 |
| AADSTS130005 | NgcInvalidSignature - NGC 金鑰簽章驗證失敗。|
| AADSTS130006 | NgcTransportKeyNotFound - 裝置上未設定 NGC 傳輸金鑰。 |
| AADSTS130007 | NgcDeviceIsDisabled - 此裝置已停用。 |
| AADSTS130008 | NgcDeviceIsNotFound - 找不到 NGC 金鑰所參考的裝置。 |
| AADSTS135010 | KeyNotFound |
| AADSTS140000 | InvalidRequestNonce - 未提供要求 nonce。 |
| AADSTS140001 | InvalidSessionKey - 工作階段金鑰無效。|
| AADSTS165900 | InvalidApiRequest - 要求無效。 |
| AADSTS220450 | UnsupportedAndroidWebViewVersion - 不支援 Chrome WebView 版本。 |
| AADSTS220501 | InvalidCrlDownload |
| AADSTS221000 | DeviceOnlyTokensNotSupportedByResource - 此資源並未設定為接受僅限裝置的權杖。 |
| AADSTS240001 | BulkAADJTokenUnauthorized - 使用者未經授權，無法在 Azure AD 中註冊裝置。 |
| AADSTS240002 | RequiredClaimIsMissing - 無法使用 id_token 作為 `urn:ietf:params:oauth:grant-type:jwt-bearer` 授與。|
| AADSTS530032 | BlockedByConditionalAccessOnSecurityPolicy-租使用者系統管理員已設定封鎖此要求的安全性原則。 檢查租用戶層級上定義的安全性原則，以判斷您的要求是否符合原則需求。 |
| AADSTS700016 | UnauthorizedClient_DoesNotMatchRequest - 在目錄/租用戶中找不到應用程式。 如果租用戶的系統管理員尚未安裝此應用程式或租用戶中的任何使用者尚未同意使用此應用程式，也可能會發生此錯誤。 可能是針對應用程式所設定的識別碼值不正確，或將驗證要求傳送給錯誤的租用戶。 |
| AADSTS700020 | InteractionRequired - 存取權授與需要互動。 |
| AADSTS700022 | InvalidMultipleResourcesScope - 所提供的輸入參數範圍值無效，因為它包含多個資源。 |
| AADSTS700023 | InvalidResourcelessScope - 要求存取權杖時，所提供的輸入參數範圍值無效。 |
| AADSTS7000215 | 提供的用戶端祕密無效。 開發人員錯誤-應用程式嘗試在沒有必要或正確的驗證參數的情況下登入。|
| AADSTS7000222 | InvalidClientSecretExpiredKeysProvided-提供的用戶端秘密金鑰已過期。 請造訪 Azure 入口網站為您的應用程式建立新的金鑰，或考慮使用憑證認證來提高安全性： [https://aka.ms/certCreds](https://aka.ms/certCreds) |
| AADSTS700005 | InvalidGrantRedeemAgainstWrongTenant 提供的授權碼適用于其他租使用者，因此遭到拒絕。 OAuth2 授權碼必須針對 (/common 或/{tenant-ID} 所取得的相同租使用者兌換（適當)  |
| AADSTS1000000 | UserNotBoundError - 繫結 API 需要 Azure AD 使用者也向外部 IDP 進行驗證，但尚未發生。 |
| AADSTS1000002 | BindCompleteInterruptError - 繫結已順利完成，但必須通知使用者。 |
| AADSTS7000112 | UnauthorizedClientApplicationDisabled-應用程式已停用。 |
| AADSTS7000114| 應用程式 ' appIdentifier ' 不允許進行應用程式的呼叫。|
| AADSTS7500529 | 值 ' SAMLId-Guid ' 不是有效的 SAML 識別碼-Azure AD 使用這個屬性來填入所傳迴響應的 InResponseTo 屬性。 識別碼的開頭不能是數字，因此常見的策略是在 GUID 的字串表示法前面加上 "id" 等字串。 例如，id6c1c178c166d486687be4aaf5e482730 是有效的識別碼。 |

## <a name="next-steps"></a>下一步

* 有任何疑問或找不到您要尋找的內容嗎？ 建立 GitHub 問題，或參閱[適用於開發人員的支援和協助選項](./developer-support-help-options.md)，了解您可以取得協助和支援的其他方法。
