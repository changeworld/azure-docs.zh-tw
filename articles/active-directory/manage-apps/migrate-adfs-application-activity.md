---
title: 使用活動報告將 AD FS 應用程式移至 Azure Active Directory |Microsoft Docs '
description: Active Directory 同盟服務（AD FS）應用程式活動報告可讓您快速地將應用程式從 AD FS 遷移至 Azure Active Directory （Azure AD）。 這個用於 AD FS 的遷移工具會識別與 Azure AD 的相容性，並提供遷移指引。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/14/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 333e440fdd5f5062dda45fb12a83543c63e66c04
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75978024"
---
# <a name="use-the-ad-fs-application-activity-report-preview-to-migrate-applications-to-azure-ad"></a>使用 AD FS 應用程式活動報告（預覽），將應用程式遷移至 Azure AD

許多組織會使用 Active Directory 同盟服務（AD FS）來提供雲端應用程式的單一登入。 將 AD FS 應用程式移至 Azure AD 進行驗證有很大的好處，特別是在成本管理、風險管理、生產力、合規性和治理方面。 但瞭解哪些應用程式與 Azure AD 相容，並識別特定的遷移步驟可能會很耗時。

Azure 入口網站中的 AD FS 應用程式活動報告（預覽）可讓您快速識別哪些應用程式能夠遷移至 Azure AD。 它會評估所有 AD FS 應用程式，以與 Azure AD 相容、檢查是否有任何問題，並提供準備個別應用程式以進行遷移的指引。 透過 AD FS 應用程式活動報告，您可以：

* **探索 AD FS 應用程式並界定您的遷移範圍。** [AD FS 應用程式活動] 報表會列出組織中所有 AD FS 的應用程式，並指出他們是否已準備好遷移至 Azure AD。
* **設定應用程式的優先順序以進行遷移。** 取得過去1、7或30天內已登入應用程式的唯一使用者數目，以協助判斷遷移應用程式的重要性或風險。
* **執行遷移測試並修正問題。** 報表服務會自動執行測試，以判斷應用程式是否已準備好進行遷移。 結果會顯示在 [AD FS 應用程式活動報告] 中作為 [遷移] 狀態。 如果發現潛在的遷移問題，您會取得如何解決問題的特定指引。

AD FS 的應用程式活動資料可供指派給任何系統管理員角色的使用者使用：全域管理員、報告讀取者、安全性讀取者、應用程式系統管理員或雲端應用程式系統管理員。

## <a name="prerequisites"></a>先決條件

* 您的組織目前必須使用 AD FS 來存取應用程式。
* 必須在您的 Azure AD 租使用者中啟用 Azure AD Connect Health。
   * [深入瞭解 Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)
   * [開始設定 Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>探索可遷移 AD FS 應用程式 

[AD FS 應用程式活動] 報告可在 Azure 入口網站的 [Azure AD**使用量] & [深入**解析] 報告中取得。 AD FS 的應用程式活動報表會分析每個 AD FS 應用程式，以判斷是否可以依需求遷移，或是否需要額外的審查。 

1. 使用可存取 AD FS 應用程式活動資料的系統管理員角色（全域管理員、報告讀取者、安全性讀取者、應用程式系統管理員或雲端應用程式系統管理員）登入[Azure 入口網站](https://portal.azure.com)。

2. 選取 [ **Azure Active Directory**]，然後選取 [**企業應用程式**]。

3. 在 [**活動**] 底下，選取 **[Usage & Insights （預覽）**]，然後選取 [ **AD FS 應用程式活動**] 以開啟組織中所有 AD FS 應用程式的清單。

   ![AD FS 應用程式活動](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. 針對 [AD FS 應用程式活動] 清單中的每個應用程式，查看 [**遷移狀態**]：

   * **準備好進行遷移**表示 AD FS 的應用程式設定在 Azure AD 中受到完整支援，而且可以依需求進行遷移。

   * [**需要審核**] 表示部分應用程式的設定可以遷移至 Azure AD，但您必須檢查無法依需求遷移的設定。

   * **需要的其他步驟**表示 Azure AD 不支援部分應用程式的設定，因此無法以目前的狀態遷移應用程式。

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>評估應用程式是否已準備好進行遷移 

1. 在 [AD FS 應用程式活動] 清單中，按一下 [**遷移狀態**] 欄中的狀態，以開啟 [遷移詳細資料]。 您會看到所傳遞之設定測試的摘要，以及任何可能的遷移問題。

   ![移轉詳細資料](media/migrate-adfs-application-activity/migration-details.png)

2. 按一下訊息以開啟其他的遷移規則詳細資料。 如需已測試屬性的完整清單，請參閱下方的[AD FS 應用程式設定測試](#ad-fs-application-configuration-tests)資料表。

   ![遷移規則詳細資料](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>AD FS 應用程式設定測試

下表列出在 AD FS 應用程式上執行的所有設定測試。

|結果  |通過/警告/失敗  |說明  |
|---------|---------|---------|
|測試-ADFSRPAdditionalAuthenticationRules <br> 在 AdditionalAuthentication 中偵測到至少一個非可移轉的規則。       | 通過/警告          | 信賴憑證者的規則會提示多重要素驗證（MFA）。 若要移至 Azure AD，請將這些規則轉譯為條件式存取原則。 如果您使用的是內部部署 MFA，建議您移至 Azure MFA。 [深入瞭解條件式存取](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)。        |
|測試-ADFSRPAdditionalWSFedEndpoint <br> 信賴憑證者的 AdditionalWSFedEndpoint 設為 true。       | 通過/失敗          | AD FS 中的信賴憑證者允許多個 WS-ADDRESSING 宣告端點。目前，Azure AD 只支援一個。如果您有此結果封鎖遷移的案例，請[讓我們知道](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints)。     |
|測試-ADFSRPAllowedAuthenticationClassReferences <br> 信賴憑證者已設定 AllowedAuthenticationClassReferences。       | 通過/失敗          | AD FS 中的這項設定可讓您指定是否將應用程式設定為只允許特定的驗證類型。 我們建議使用條件式存取來達成這項功能。 如果您有此結果封鎖遷移的案例，請[讓我們知道](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication)。  [深入瞭解條件式存取](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)。          |
|測試-ADFSRPAlwaysRequireAuthentication <br> AlwaysRequireAuthenticationCheckResult      | 通過/失敗          | AD FS 中的這項設定可讓您指定是否將應用程式設定為忽略 SSO cookie，並**一律提示進行驗證**。 在 Azure AD 中，您可以使用條件式存取原則來管理驗證會話，以達到類似的行為。 [深入瞭解如何使用條件式存取來設定驗證會話管理](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)。          |
|測試-ADFSRPAutoUpdateEnabled <br> 信賴憑證者將 AutoUpdateEnabled 設定為 true       | 通過/警告          | AD FS 中的這項設定可讓您指定是否將 AD FS 設定為根據同盟中繼資料內的變更自動更新應用程式。 Azure AD 目前不支援這種方式，但不應該封鎖將應用程式遷移至 Azure AD。           |
|測試-ADFSRPClaimsProviderName <br> 信賴憑證者已啟用多個 ClaimsProviders       | 通過/失敗          | AD FS 中的這項設定會呼叫信賴憑證者接受宣告的身分識別提供者。 在 Azure AD 中，您可以使用 Azure AD B2B 啟用外部共同作業。 [深入瞭解 AZURE AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)。          |
|測試-ADFSRPDelegationAuthorizationRules      | 通過/失敗          | 應用程式已定義自訂委派授權規則。 這是使用新式驗證通訊協定（例如 OpenID Connect 和 OAuth 2.0） Azure AD 支援的 WS-TRUST 概念。 [深入瞭解 Microsoft 身分識別平臺](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc)。          |
|測試-ADFSRPImpersonationAuthorizationRules       | 通過/警告          | 應用程式已定義自訂的模擬授權規則。這是使用新式驗證通訊協定（例如 OpenID Connect 和 OAuth 2.0） Azure AD 支援的 WS-TRUST 概念。 [深入瞭解 Microsoft 身分識別平臺](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc)。          |
|測試-ADFSRPIssuanceAuthorizationRules <br> 在 IssuanceAuthorization 中偵測到至少一個非可移轉的規則。       | 通過/警告          | 應用程式具有在 AD FS 中定義的自訂發佈授權規則。Azure AD 透過 Azure AD 條件式存取來支援這種功能。 [深入瞭解條件式存取](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)。 <br> 您也可以根據指派給應用程式的使用者或群組，限制應用程式的存取權。 [深入瞭解如何指派使用者和群組來存取應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups)。            |
|測試-ADFSRPIssuanceTransformRules <br> 在 IssuanceTransform 中偵測到至少一個非可移轉的規則。       | 通過/警告          | 應用程式具有在 AD FS 中定義的自訂發行轉換規則。 Azure AD 支援自訂在權杖中發出的宣告。 若要深入瞭解，請參閱[針對企業應用程式自訂 SAML 權杖中發出的宣告](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。           |
|測試-ADFSRPMonitoringEnabled <br> 信賴憑證者的 MonitoringEnabled 設為 true。       | 通過/警告          | AD FS 中的這項設定可讓您指定是否將 AD FS 設定為根據同盟中繼資料內的變更自動更新應用程式。 Azure AD 目前不支援這種方式，但不應該封鎖將應用程式遷移至 Azure AD。           |
|測試-ADFSRPNotBeforeSkew <br> NotBeforeSkewCheckResult      | 通過/警告          | AD FS 允許根據 SAML 權杖中的 NotBefore 和 NotOnOrAfter 時間來進行時間誤差。 Azure AD 預設會自動處理這種情況。          |
|測試-ADFSRPRequestMFAFromClaimsProviders <br> 信賴憑證者的 RequestMFAFromClaimsProviders 設為 true。       | 通過/警告          | AD FS 中的這項設定會決定當使用者來自不同的宣告提供者時，MFA 的行為。 在 Azure AD 中，您可以使用 Azure AD B2B 啟用外部共同作業。 然後，您可以套用條件式存取原則來保護來賓存取。 深入瞭解[AZURE AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)和[條件式存取](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)。          |
|測試-ADFSRPSignedSamlRequestsRequired <br> 信賴憑證者將 SignedSamlRequestsRequired 設定為 true       | 通過/失敗          | 應用程式會在 AD FS 中設定，以驗證 SAML 要求中的簽章。 Azure AD 接受已簽署的 SAML 要求;不過，它不會驗證簽章。 Azure AD 有不同的方法可防範惡意的呼叫。 例如，Azure AD 會使用在應用程式中設定的回復 Url 來驗證 SAML 要求。 Azure AD 只會將權杖傳送至為應用程式設定的回復 Url。 如果您有此結果封鎖遷移的案例，請[讓我們知道](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature)。          |
|測試-ADFSRPTokenLifetime <br> TokenLifetimeCheckResult        | 通過/警告         | 應用程式會針對自訂權杖存留期進行設定。 AD FS 預設值為一小時。Azure AD 使用條件式存取來支援這種功能。 若要深入瞭解，請參閱[使用條件式存取來設定驗證會話管理](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)。          |
|信賴憑證者設定為加密宣告。 這受到 Azure AD 的支援       | 通過          | 使用 Azure AD，您可以加密傳送給應用程式的權杖。 若要深入瞭解，請參閱[設定 AZURE AD SAML 權杖加密](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)。          |
|EncryptedNameIdRequiredCheckResult      | 通過/失敗          | 應用程式已設定為加密 SAML 權杖中的 nameID 宣告。使用 Azure AD，您可以加密傳送至應用程式的整個權杖。尚未支援特定宣告的加密。 若要深入瞭解，請參閱[設定 AZURE AD SAML 權杖加密](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)。         |

## <a name="check-the-results-of-claim-rule-tests"></a>檢查宣告規則測試的結果

如果您已在 AD FS 中為應用程式設定宣告規則，體驗將會針對所有宣告規則提供細微的分析。 您將會看到哪些宣告規則可移至 Azure AD，哪些則需要進一步審查。

1. 在 [AD FS 應用程式活動] 清單中，按一下 [**遷移狀態**] 欄中的狀態，以開啟 [遷移詳細資料]。 您會看到所傳遞之設定測試的摘要，以及任何可能的遷移問題。

2. 在 [**遷移規則詳細資料**] 頁面上，展開結果以顯示潛在遷移問題的詳細資料，並取得其他指引。 如需所有已測試之宣告規則的詳細清單，請參閱下方的[檢查宣告規則測試的結果](#check-the-results-of-claim-rule-tests)資料表。

   下列範例顯示 IssuanceTransform 規則的遷移規則詳細資料。 它會列出宣告的特定部分，必須先進行檢查並加以處理，才能將應用程式遷移至 Azure AD。

   ![遷移規則詳細資料其他指引](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>宣告規則測試

下表列出在 AD FS 應用程式上執行的所有宣告規則測試。

|屬性  |說明  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | 條件陳述式會使用正則運算式來評估宣告是否符合特定模式。若要在 Azure AD 中達到類似的功能，您可以使用預先定義的轉換，例如 Defaultifempty （）、與 startwith （）、Contains （），以及其他專案。 如需詳細資訊，請參閱[針對企業應用程式自訂 SAML 權杖中發出的宣告](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。          |
|UNSUPPORTED_CONDITION_CLASS      | 條件陳述式具有多個必須在執行發行語句之前評估的條件。Azure AD 可以使用宣告的轉換函式來支援這項功能，您可以在其中評估多個宣告值。如需詳細資訊，請參閱[針對企業應用程式自訂 SAML 權杖中發出的宣告](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。          |
|UNSUPPORTED_RULE_TYPE      | 無法辨識宣告規則。 如需如何在 Azure AD 中設定宣告的詳細資訊，請參閱[針對企業應用程式自訂 SAML 權杖中發出的宣告](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | 條件陳述式會使用 Azure AD 中不支援的簽發者。目前，Azure AD 不是存放區中 Active Directory 或 Azure AD 不同的來源宣告。 如果這種情況導致您無法將應用程式遷移至 Azure AD，請[讓我們知道](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)。         |
|UNSUPPORTED_CONDITION_FUNCTION      | 條件陳述式會使用彙總函式來發出或加入單一宣告，而不論相符專案的數目為何。在 Azure AD 中，您可以使用 Defaultifempty （）、與 startwith （）、Contains （）等函數，評估使用者的屬性，以決定要用於宣告的值。如需詳細資訊，請參閱[針對企業應用程式自訂 SAML 權杖中發出的宣告](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。          |
|RESTRICTED_CLAIM_ISSUED      | 條件陳述式會使用 Azure AD 中限制的宣告。 您可以發行受限制的宣告，但無法修改其來源或套用任何轉換。 如需詳細資訊，請參閱[在 Azure AD 中自訂特定應用程式在權杖中發出的宣告](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping)。          |
|EXTERNAL_ATTRIBUTE_STORE      | 發行語句會使用 Active Directory 的屬性存放區。 目前，Azure AD 不是存放區中 Active Directory 或 Azure AD 不同的來源宣告。 如果此結果封鎖您將應用程式遷移至 Azure AD，請[讓我們知道](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)。          |
|UNSUPPORTED_ISSUANCE_CLASS      | 發行語句會使用 ADD 將宣告新增至傳入的宣告集。 在 Azure AD 中，這可能會設定為多個宣告轉換。如需詳細資訊，請參閱[針對企業應用程式自訂 SAML 權杖中發出的宣告](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping)。         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | 發行語句會使用正則運算式來轉換要發出的宣告值。若要在 Azure AD 中達到類似的功能，您可以使用預先定義的轉換，例如 [解壓縮（）]、[Trim （）]、[ToLower] 和其他。 如需詳細資訊，請參閱[針對企業應用程式自訂 SAML 權杖中發出的宣告](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。          |


## <a name="next-steps"></a>後續步驟

- [影片：如何使用 AD FS 活動報告來遷移應用程式](https://www.youtube.com/watch?v=OThlTA239lU)
- [使用 Azure Active Directory 管理應用程式](what-is-application-management.md)
- [管理應用程式的存取權](what-is-access-management.md)
- [Azure AD Connect 同盟](../hybrid/how-to-connect-fed-whatis.md)
