---
title: 使用活動報告將 AD FS 應用程式移至 Azure Active Directory |Microsoft Docs '
description: Active Directory 同盟服務 (AD FS) 應用程式活動報告可讓您快速將應用程式從 AD FS 遷移至 Azure Active Directory (Azure AD) 。 此 AD FS 的遷移工具會識別與 Azure AD 的相容性，並提供遷移指引。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/14/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59502e01a96b603067bd80b92bcf49136f8cef4e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85339157"
---
# <a name="use-the-ad-fs-application-activity-report-preview-to-migrate-applications-to-azure-ad"></a>使用 AD FS 應用程式活動報告 (預覽) 將應用程式遷移至 Azure AD

許多組織都使用 Active Directory 同盟服務 (AD FS) 來提供雲端應用程式的單一登入。 將 AD FS 應用程式移至 Azure AD 進行驗證有很大的好處，尤其是在成本管理、風險管理、生產力、合規性和治理方面。 但是，瞭解哪些應用程式與 Azure AD 相容，並找出特定的遷移步驟可能相當耗時。

Azure 入口網站中的 AD FS 應用程式活動報表 (preview) 可讓您快速識別哪些應用程式可以遷移至 Azure AD。 它會評估所有 AD FS 應用程式的相容性 Azure AD、檢查是否有任何問題，並提供準備個別應用程式以進行遷移的指引。 使用 AD FS 應用程式活動報表，您可以：

* **探索 AD FS 的應用程式，並為您的遷移進行範圍。** [AD FS 應用程式活動] 報表會列出您組織中的所有 AD FS 應用程式，並指出他們是否已準備好遷移至 Azure AD。
* **排定應用程式的優先順序以進行遷移。** 取得過去1天、7天或30天內已登入應用程式的唯一使用者數目，以協助判斷遷移應用程式的重要性或風險。
* **執行遷移測試並修正問題。** 報表服務會自動執行測試，以判斷應用程式是否準備好遷移。 結果會顯示在 [AD FS 應用程式活動] 報表中，作為遷移狀態。 如果發現潛在的遷移問題，您可以取得如何解決問題的特定指引。

AD FS 的應用程式活動資料可供指派任何系統管理員角色的使用者使用：全域管理員、報表讀取者、安全性讀取者、應用程式系統管理員或雲端應用程式系統管理員。

## <a name="prerequisites"></a>必要條件

* 您的組織目前必須使用 AD FS 來存取應用程式。
* 您必須在 Azure AD 租使用者中啟用 Azure AD Connect Health。
* 必須安裝 AD FS agent 的 Azure AD Connect Health。
   * [深入瞭解 Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)
   * [開始設定 Azure AD Connect Health，並安裝 AD FS 代理程式](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>探索可遷移的 AD FS 應用程式 

AD FS 的應用程式活動報告可在 Azure AD Azure 入口網站的 [ **使用** 方式] & [見解] 報表中取得。 AD FS 的應用程式活動報表會分析每個 AD FS 的應用程式，以判斷它是否可以依原樣遷移，或是否需要額外的評論。 

1. 使用具有 AD FS 應用程式活動資料存取權的系統管理員角色登入 [Azure 入口網站](https://portal.azure.com) (全域管理員、報表讀取者、安全性讀取者、應用程式系統管理員或雲端應用程式系統管理員) 。

2. 選取 **Azure Active Directory**，然後選取 [ **企業應用程式**]。

3. 在 [ **活動**] 下，選取 [ **使用量 & 見解] ([預覽]) **，然後選取 [ **AD FS 應用程式活動** ] 以開啟您組織中所有 AD FS 應用程式的清單。

   ![AD FS 應用程式活動](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. 針對 AD FS 應用程式活動清單中的每個應用程式，查看 **遷移狀態**：

   * **準備好遷移** 表示 Azure AD 完全支援 AD FS 的應用程式設定，並可依原樣遷移。

   * **需要審查** 表示可以將部分應用程式的設定遷移至 Azure AD，但您必須檢查無法依原樣遷移的設定。

   * **需要額外的步驟** ，表示 Azure AD 不支援部分應用程式的設定，所以無法在目前的狀態中遷移應用程式。

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>評估應用程式是否準備好進行遷移 

1. 在 [AD FS 應用程式活動] 清單中，按一下 [ **遷移狀態** ] 資料行中的狀態以開啟 [遷移詳細資料]。 您將會看到傳遞的設定測試摘要，以及任何可能的遷移問題。

   ![移轉詳細資料](media/migrate-adfs-application-activity/migration-details.png)

2. 按一下訊息以開啟其他的 [遷移規則詳細資料]。 如需測試過的屬性完整清單，請參閱下方的 [AD FS 應用程式設定測試](#ad-fs-application-configuration-tests) 表格。

   ![遷移規則詳細資料](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>AD FS 應用程式設定測試

下表列出在 AD FS 應用程式上執行的所有設定測試。

|結果  |Pass/警告/失敗  |描述  |
|---------|---------|---------|
|Test-ADFSRPAdditionalAuthenticationRules <br> 偵測到至少有一個非可移轉規則的 AdditionalAuthentication。       | Pass/警告          | 信賴憑證者有規則，可提示 (MFA) 的多重要素驗證。 若要移至 Azure AD，請將這些規則轉譯成條件式存取原則。 如果您使用的是內部部署 MFA，建議您移至 Azure MFA。 [深入瞭解條件式存取](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)。        |
|Test-ADFSRPAdditionalWSFedEndpoint <br> 信賴憑證者的 AdditionalWSFedEndpoint 設為 true。       | 通過/失敗          | AD FS 中的信賴憑證者允許多個 WS-Fed 判斷提示端點。Azure AD 目前僅支援一個。如果您有此結果正在封鎖遷移的案例，請 [讓我們知道](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints)。     |
|Test-ADFSRPAllowedAuthenticationClassReferences <br> 信賴憑證者已設定 AllowedAuthenticationClassReferences。       | 通過/失敗          | AD FS 中的這項設定可讓您指定是否將應用程式設定為只允許特定的驗證類型。 我們建議使用條件式存取來達成這項功能。 如果您有此結果正在封鎖遷移的案例，請 [讓我們知道](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication)。  [深入瞭解條件式存取](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)。          |
|Test-ADFSRPAlwaysRequireAuthentication <br> AlwaysRequireAuthenticationCheckResult      | 通過/失敗          | AD FS 中的這項設定可讓您指定是否要將應用程式設定為忽略 SSO cookie，並 **一律提示進行驗證**。 在 Azure AD 中，您可以使用條件式存取原則來管理驗證會話，以達成類似的行為。 [深入瞭解如何使用條件式存取設定驗證會話管理](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)。          |
|Test-ADFSRPAutoUpdateEnabled <br> 信賴憑證者的 AutoUpdateEnabled 設為 true       | Pass/警告          | AD FS 中的這項設定可讓您指定是否將 AD FS 設定為根據同盟中繼資料內的變更自動更新應用程式。 Azure AD 目前不支援此項，但不應該封鎖將應用程式遷移至 Azure AD。           |
|Test-ADFSRPClaimsProviderName <br> 信賴憑證者已啟用多個 Claimsprovider       | 通過/失敗          | AD FS 中的這項設定會呼叫信賴憑證者接受宣告的身分識別提供者。 在 Azure AD 中，您可以使用 Azure AD B2B 來啟用外部共同作業。 [深入瞭解 AZURE AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)。          |
|Test-ADFSRPDelegationAuthorizationRules      | 通過/失敗          | 應用程式已定義自訂委派授權規則。 這是 Azure AD 使用新式驗證通訊協定（例如 OpenID Connect 和 OAuth 2.0）支援的 WS-Trust 概念。 [深入瞭解 Microsoft 身分識別平臺](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc)。          |
|Test-ADFSRPImpersonationAuthorizationRules       | Pass/警告          | 應用程式已定義自訂模擬授權規則。這是 Azure AD 使用新式驗證通訊協定（例如 OpenID Connect 和 OAuth 2.0）支援的 WS-Trust 概念。 [深入瞭解 Microsoft 身分識別平臺](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc)。          |
|Test-ADFSRPIssuanceAuthorizationRules <br> 偵測到至少有一個非可移轉規則的 IssuanceAuthorization。       | Pass/警告          | 應用程式已在 AD FS 中定義了自訂的發佈授權規則。Azure AD 透過 Azure AD 條件式存取支援此功能。 [深入瞭解條件式存取](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)。 <br> 您也可以依指派給應用程式的使用者或群組，限制對應用程式的存取。 [深入瞭解如何指派使用者和群組以存取應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups)。            |
|Test-ADFSRPIssuanceTransformRules <br> 偵測到至少有一個非可移轉規則的 IssuanceTransform。       | Pass/警告          | 應用程式已在 AD FS 中定義了自訂發行轉換規則。 Azure AD 支援自訂權杖中所發出的宣告。 若要深入瞭解，請參閱 [針對企業應用程式自訂在 SAML 權杖中發出的宣告](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。           |
|Test-ADFSRPMonitoringEnabled <br> 信賴憑證者的 MonitoringEnabled 設為 true。       | Pass/警告          | AD FS 中的這項設定可讓您指定是否將 AD FS 設定為根據同盟中繼資料內的變更自動更新應用程式。 Azure AD 目前不支援此項，但不應該封鎖將應用程式遷移至 Azure AD。           |
|Test-ADFSRPNotBeforeSkew <br> NotBeforeSkewCheckResult      | Pass/警告          | AD FS 允許以 SAML 權杖中的 NotBefore 和 NotOnOrAfter 時間為基礎的時間誤差。 Azure AD 預設會自動處理此情況。          |
|Test-ADFSRPRequestMFAFromClaimsProviders <br> 信賴憑證者的 RequestMFAFromClaimsProviders 設為 true。       | Pass/警告          | AD FS 中的這項設定會決定當使用者來自不同的宣告提供者時，MFA 的行為。 在 Azure AD 中，您可以使用 Azure AD B2B 來啟用外部共同作業。 然後，您可以套用條件式存取原則來保護來賓存取。 深入瞭解 [AZURE AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) 和 [條件式存取](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)。          |
|Test-ADFSRPSignedSamlRequestsRequired <br> 信賴憑證者的 SignedSamlRequestsRequired 設為 true       | 通過/失敗          | 應用程式會在 AD FS 中設定，以驗證 SAML 要求中的簽章。 Azure AD 接受已簽署的 SAML 要求;不過，它不會驗證簽章。 Azure AD 有不同的方法可防止惡意呼叫。 例如，Azure AD 使用在應用程式中設定的回復 Url 來驗證 SAML 要求。 Azure AD 只會傳送權杖給針對應用程式設定的回復 Url。 如果您有此結果正在封鎖遷移的案例，請 [讓我們知道](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature)。          |
|Test-ADFSRPTokenLifetime <br> TokenLifetimeCheckResult        | Pass/警告         | 應用程式會設定為自訂權杖存留期。 AD FS 預設值為一小時。Azure AD 使用條件式存取支援此功能。 若要深入瞭解，請參閱 [使用條件式存取設定驗證會話管理](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)。          |
|信賴憑證者設定為加密宣告。 Azure AD 支援此功能       | 通過          | 使用 Azure AD，您就可以加密傳送至應用程式的權杖。 若要深入瞭解，請參閱 [設定 AZURE AD SAML 權杖加密](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)。          |
|EncryptedNameIdRequiredCheckResult      | 通過/失敗          | 應用程式會設定為加密 SAML 權杖中的 nameID 宣告。使用 Azure AD，您就可以加密傳送至應用程式的整個權杖。尚未支援特定宣告的加密。 若要深入瞭解，請參閱 [設定 AZURE AD SAML 權杖加密](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)。         |

## <a name="check-the-results-of-claim-rule-tests"></a>檢查宣告規則測試的結果

如果您已在 AD FS 中設定應用程式的宣告規則，此體驗將會為所有宣告規則提供細微的分析。 您將會看到哪些宣告規則可移至 Azure AD，哪些宣告規則需要進一步審核。

1. 在 [AD FS 應用程式活動] 清單中，按一下 [ **遷移狀態** ] 資料行中的狀態以開啟 [遷移詳細資料]。 您將會看到傳遞的設定測試摘要，以及任何可能的遷移問題。

2. 在 [ **遷移規則詳細資料** ] 頁面上，展開結果以顯示潛在遷移問題的詳細資料，並取得其他指引。 如需所有測試之宣告規則的詳細清單，請參閱下方的 [檢查宣告規則測試的結果](#check-the-results-of-claim-rule-tests) 表格。

   下列範例顯示 IssuanceTransform 規則的遷移規則詳細資料。 它會列出宣告的特定部分，在您可以將應用程式遷移至 Azure AD 之前，必須先進行審核和解決。

   ![遷移規則詳細說明其他指引](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>宣告規則測試

下表列出在 AD FS 應用程式上執行的所有宣告規則測試。

|屬性  |描述  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | Condition 語句會使用正則運算式來評估宣告是否符合特定模式。若要在 Azure AD 中達成類似的功能，您可以使用預先定義的轉換（例如 IfEmpty ( # A1、StartWith ( # A3），其中包含 ( # A5 等等。 如需詳細資訊，請參閱 [針對企業應用程式自訂 SAML 權杖中發出的宣告](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。          |
|UNSUPPORTED_CONDITION_CLASS      | 條件陳述式具有多個必須在執行發行語句之前評估的條件。Azure AD 可能會使用宣告的轉換函式來支援這項功能，您可以在其中評估多個宣告值。如需詳細資訊，請參閱 [針對企業應用程式自訂 SAML 權杖中發出的宣告](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。          |
|UNSUPPORTED_RULE_TYPE      | 無法辨識宣告規則。 如需有關如何在 Azure AD 中設定宣告的詳細資訊，請參閱 [針對企業應用程式自訂在 SAML 權杖中發出的宣告](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | Condition 語句使用 Azure AD 中不支援的簽發者。目前，Azure AD 不是 Active Directory 或 Azure AD 不同的存放區的來源宣告。 如果這會封鎖您將應用程式遷移至 Azure AD，請 [讓我們知道](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)。         |
|UNSUPPORTED_CONDITION_FUNCTION      | 不論相符專案數目為何，條件陳述式都會使用彙總函式來發出或加入單一宣告。在 Azure AD 中，您可以評估使用者的屬性，以決定要使用哪些函式作為宣告的值，例如 IfEmpty ( # A1、StartWith ( # A3、包含 ( # A5，還有其他。如需詳細資訊，請參閱 [針對企業應用程式自訂 SAML 權杖中發出的宣告](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。          |
|RESTRICTED_CLAIM_ISSUED      | Condition 語句使用 Azure AD 中受限制的宣告。 您可以發出受限的宣告，但無法修改其來源或套用任何轉換。 如需詳細資訊，請參閱 [Azure AD 中的針對特定應用程式自訂權杖所發出的宣告](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping)。          |
|EXTERNAL_ATTRIBUTE_STORE      | 此發行語句使用 Active Directory 的屬性存放區。 目前，Azure AD 不是 Active Directory 或 Azure AD 不同的存放區的來源宣告。 如果此結果封鎖您將應用程式遷移至 Azure AD，請 [讓我們知道](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)。          |
|UNSUPPORTED_ISSUANCE_CLASS      | 此發行語句使用 ADD，將宣告新增至連入宣告集。 在 Azure AD 中，這可以設定為多個宣告轉換。如需詳細資訊，請參閱 [針對企業應用程式自訂 SAML 權杖中發出的宣告](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping)。         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | 發行語句會使用正則運算式來轉換要發出的宣告值。若要在 Azure AD 中達成類似的功能，您可以使用預先定義的轉換，例如將 ( # A1、Trim ( # A3、ToLower 和其他專案之間的解壓縮。 如需詳細資訊，請參閱 [針對企業應用程式自訂 SAML 權杖中發出的宣告](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。          |


## <a name="next-steps"></a>後續步驟

- [影片：如何使用 AD FS 活動報表來遷移應用程式](https://www.youtube.com/watch?v=OThlTA239lU)
- [使用 Azure Active Directory 管理應用程式](what-is-application-management.md)
- [管理應用程式的存取權](what-is-access-management.md)
- [Azure AD Connect 同盟](../hybrid/how-to-connect-fed-whatis.md)
