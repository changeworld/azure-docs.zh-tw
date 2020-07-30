---
title: 規劃 Azure Active Directory 單一登入部署
description: 協助您在組織中規劃、部署和管理 SSO 的指南。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 06/10/2020
ms.author: baselden
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 0ee138ac41557554ae4b8fde8c9178336fd8d5db
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387773"
---
# <a name="plan-a-single-sign-on-deployment"></a>規劃單一登入部署

單一登入（SSO）表示只要使用單一使用者帳戶登入一次，就能存取使用者所需的所有應用程式和資源。 使用 SSO 時，使用者可以存取所有必要的應用程式，而不需要再次驗證。

## <a name="benefits-of-sso"></a>SSO 的優點

當使用者登入 Azure Active Directory （Azure AD）中的應用程式時，單一登入（SSO）可增加安全性和便利性。 

許多組織都依賴軟體即服務（SaaS）應用程式，例如 Office 365、Box 和 Salesforce，以提供使用者生產力。 在過去，IT 人員需要在每個 SaaS 應用程式中個別建立並更新使用者帳戶，而使用者必須記住每個的密碼。

3000 Azure Marketplace 的應用程式具有預先整合的 SSO 連線，可讓您輕鬆地將它們整合到您的租使用者中。

## <a name="licensing"></a>授權

- 適用于預先整合之 SaaS 應用程式的**Azure AD 授權**-SSO 是免費的。 不過，目錄中的物件數目和您想要部署的功能可能需要額外的授權。 如需授權需求的完整清單，請參閱[Azure Active Directory 定價](https://azure.microsoft.com/pricing/details/active-directory/)。
- **應用程式授權**-您需要有適當的授權，您的 SaaS 應用程式才能符合您的商務需求。 請與應用程式擁有者合作，以判斷指派給應用程式的使用者在應用程式內是否具有適當的角色授權。 如果 Azure AD 管理以角色為基礎的自動布建，Azure AD 中指派的角色必須符合應用程式中所擁有的授權數目。 應用程式所擁有的授權數目不正確，可能會在使用者布建/更新期間導致錯誤。

## <a name="plan-your-sso-team"></a>規劃您的 SSO 小組

- **吸引適當的專案關係人**-當技術專案失敗時，通常是因為對影響、結果和責任的期望不符。 若要避免這些錯誤，請[確定您參與的是正確的專案關係人](https://aka.ms/deploymentplans)，而且專案關係人瞭解他們的角色。
- **規劃通訊**-通訊對於任何新服務都是成功的關鍵。 主動與您的使用者溝通他們的經驗如何改變、何時會變更，以及如何在遇到問題時取得支援。 請參閱[使用者如何存取啟用 SSO 之應用程式](end-user-experiences.md)的選項，並製作您的通訊以符合您的選擇。 

## <a name="plan-your-sso-protocol"></a>規劃您的 SSO 通訊協定

以同盟通訊協定為基礎的 SSO 執行可改善安全性、可靠性和使用者體驗，而且更容易實行。 許多應用程式已預先整合在 Azure AD 中，[提供逐步指南](../saas-apps/tutorial-list.md)。 您可以在我們的[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/)中找到它們。 如需每個 SSO 方法的詳細資訊，請參閱[Azure Active Directory 中的應用程式的單一登入一](what-is-single-sign-on.md)文。

有兩種主要方式可讓您的使用者單一登入您的應用程式：

- **使用同盟單一登入**Azure AD 使用其 Azure AD 帳戶向應用程式驗證使用者。 支援像是 SAML 2.0、WS-同盟或 OpenID Connect 等通訊協定的應用程式支援此方法，而且是單一登入的最豐富模式。 我們建議在應用程式支援時，搭配 Azure AD 使用同盟 SSO，而不是以密碼為基礎的 SSO 和 ADFS。

- **使用密碼型單一登入**時，使用者會在第一次存取應用程式時，先使用使用者名稱和密碼登入。 第一次登入之後，Azure AD 就會向應用程式提供使用者名稱和密碼。 密碼式單一登入可以使用網頁瀏覽器擴充功能或行動應用程式，安全儲存應用程式的密碼以及重新執行。 此選項會利用應用程式提供的現有登入程式，讓系統管理員能夠管理密碼，而不需要使用者知道密碼。

### <a name="considerations-for-federation-based-sso"></a>同盟型 SSO 的考慮

- **使用 OpenID connect 和 OAuth** -如果您要連線的應用程式支援它，請使用 OIDC/OAuth 2.0 方法，讓您的 SSO 能夠連到該應用程式。 此方法需要較少的設定，並可提供更豐富的使用者體驗。 如需詳細資訊，請參閱[OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)、 [OpenID connect 1.0](../develop/v2-protocols-oidc.md)和[Azure Active Directory 開發人員指南](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)。
- **Saml 型 SSO 的端點**設定-如果您使用 saml，在設定應用程式之前，您的開發人員將需要特定資訊。 如需詳細資訊，請參閱[設定 SAML 型單一登入](configure-saml-single-sign-on.md)。
- **SAML 型 sso 的憑證管理**-當您為應用程式啟用同盟 SSO 時，Azure AD 會建立預設有效期限為三年的憑證。 如有需要，您可以自訂該憑證的到期日。 請確定您已備妥在到期前更新憑證的處理常式。 若要深入瞭解，請參閱[管理憑證 Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)。

### <a name="considerations-for-password-based-sso"></a>密碼型 SSO 的考慮

使用密碼 SSO 的 Azure AD 需要部署瀏覽器延伸模組，以安全地抓取認證並填寫登入表單。 定義以[支援的瀏覽器](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)進行大規模部署擴充功能的機制。 這些選項包括：

- [Internet Explorer 的群組原則](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)
- [Internet Explorer 的 Configuration Manager](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)
- [適用于 Chrome、Firefox、Microsoft Edge 或 IE 的使用者導向下載和設定](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

若要深入瞭解，請參閱[如何設定密碼單一登入](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery)。

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>針對不在資源庫中的應用程式，捕獲登入表單中繼資料

Microsoft 支援在 web 應用程式上捕獲中繼資料，以用於密碼保存庫（捕獲使用者名稱和密碼欄位）。 在設定應用程式以捕獲表單中繼資料的過程中，流覽至 [登入 URL]。 要求應用程式擁有者輸入確切的登入 URL。 此資訊會在登入程式期間使用，在登入期間將 Azure AD 認證對應至應用程式。

若要深入瞭解，請參閱[什麼是應用程式存取和 SSO 搭配 Azure AD？–密碼型 SSO](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/)。

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>指示表單中的中繼資料必須重新擷取

當應用程式變更其 HTML 版面配置時，您可能需要重新取得中繼資料，以針對變更進行調整。 指出 HTML 版面配置有變更的常見徵兆包括：

- 報表按一下應用程式的使用者會在登入頁面中「停滯」
- 報告使用者名稱或密碼未填入的使用者

#### <a name="shared-accounts"></a>共用帳戶

從登入的觀點來看，具有共用帳戶的應用程式不會與針對個別使用者使用密碼 SSO 的資源庫應用程式不同。 不過，規劃和設定應用程式以使用共用帳戶時，需要一些額外的步驟：

1. 與應用程式商務使用者合作以記載下列各項：
   1. 將使用應用程式之組織中的使用者集合
   1. 與一組使用者相關聯之應用程式中的現有認證集 
1. 針對每個使用者集合和認證的組合，根據您的需求在雲端或內部部署中建立安全性群組。
1. 重設共用認證。 在 Azure AD 中部署應用程式之後，個人就不需要共用帳戶的密碼。 因為 Azure AD 會儲存密碼，請考慮將它設定為非常長且複雜。 
1. 設定自動變換密碼（如果應用程式支援的話）。 如此一來，即使是初始安裝的系統管理員，也不會知道共用帳戶的密碼。 

## <a name="plan-your-authentication-method"></a>規劃您的驗證方法

選擇正確的驗證方法是設定 Azure AD 混合式身分識別解決方案的第一項重要決策。 請實作使用 Azure AD Connect 所設定的驗證方法，這也會在雲端佈建使用者。

若要選擇驗證方法，需要考慮實作您選取項目的時間、現有基礎結構、複雜度及成本。 這些因素對每個組織而言不同，並可能隨時間改變。 您應該選擇最符合您特定案例的應用程式。 如需詳細資訊，請參閱[為您的 Azure Active Directory 混合式身分識別解決方案選擇正確的驗證方法](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn)。

## <a name="plan-your-security-and-governance"></a>規劃您的安全性與治理 

身分識別是新的主要樞紐分析表，可用於安全性的考慮和投資，因為網路周邊越來越漏洞日益增加，而降低了 BYOD 裝置和雲端應用程式的激增。 

### <a name="plan-access-reviews"></a>規劃存取權審查

[存取審查](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)可讓組織有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 您應該規劃定期審查使用者存取權，以確保只有適當的人員可以繼續存取。

設定存取權審查時，需要規劃的一些重要主題包括：

1. 識別符合您商務需求的存取審查步調。 這可以是每週、每月、每年，或作為隨選練習的頻率。

1. 建立代表應用程式存取報表審查者的群組。 您必須確保專案關係人最熟悉應用程式及其目標使用者和使用案例，這是您的存取權審查中的參與者

1. 完成存取權審查包括將應用程式存取權限移至不再需要存取權的使用者。 規劃處理拒絕使用者的潛在支援要求。 已刪除的使用者 Azure AD 在30天內仍會繼續刪除，在這段期間內，系統管理員可以視需要加以還原。 經過 30 天後，該使用者將永久刪除。 使用 Azure Active Directory 入口網站，全域管理員可以在達到該時間週期之前明確地永久刪除最近刪除的使用者。

### <a name="plan-auditing"></a>規劃審核

Azure AD 提供[包含技術和商業深入](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/)解析的報表。 

安全性和活動報告均可供使用。 安全性報告會顯示標示有風險的使用者和有風險的登入。活動報告藉由詳述登入活動並提供所有登入的審核線索，協助您瞭解組織中使用者的行為。 您可以使用報告來管理風險、提高生產力，以及監視合規性。

| 報表類型 | 存取權審查 | 安全性報告 | 登入報告 |
|-------------|---------------|------------------|----------------|
| 使用來審查 | 應用程式許可權和使用方式。 | 可能遭盜用的帳戶 | 誰正在存取應用程式 |
| 可能的動作 | Audit access;撤銷許可權 | 撤銷存取權;強制安全性重設 | [撤銷存取權] |

Azure AD 會保留最多30天的審核資料，並透過 Azure 系統管理員入口網站或 API 提供資料，以供您下載至您的分析系統。

### <a name="consider-using-microsoft-cloud-application-security"></a>考慮使用 Microsoft Cloud 的應用程式安全性

Microsoft Cloud App Security （MCAS）是一種雲端存取安全性代理程式（CASB）解決方案。 它可讓您瞭解您的雲端應用程式和服務，提供精密的分析來識別和對抗網路威脅，並可讓您控制資料的傳輸方式。

部署 MCAS 可讓您：

- 使用 Cloud Discovery 來對應和識別您的雲端環境，以及貴組織正在使用的雲端應用程式。
- 在雲端中批准和取消批准應用程式
- 使用易於部署的應用程式連接器，利用提供者 Api 來可見度及管理您所連線的應用程式
- 使用條件式存取應用程式控制保護，取得雲端應用程式內的存取和活動的即時可見度和控制
- 藉由設定，然後持續微調原則，協助您持續掌控。

Microsoft Cloud 的應用程式安全性（MCAS）會話控制適用于任何作業系統上任何主要平臺上的任何瀏覽器。 您也可以封鎖或允許行動應用程式和傳統型應用程式。 藉由以原生方式與 Azure AD 整合，可支援任何以 SAML 設定的應用程式，或在 Azure AD 中使用單一登入的 Open ID Connect 應用程式，包括[數個精選應用](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)程式。

如需 MCAS 的詳細資訊，請參閱[Microsoft Cloud App Security 總覽](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)。 MCAS 是以使用者為基礎的訂用帳戶服務。 您可以在[MCAS 授權資料表](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO)中查看授權詳細資料。

### <a name="use-conditional-access"></a>使用條件式存取

透過條件式存取，您可以將雲端應用程式的準則型存取控制決策自動化。

完成第一個要素驗證之後，即會強制執行條件式存取原則。 因此，條件式存取不適合做為拒絕服務（DoS）攻擊等案例的第一行防線，但可以使用這些事件的信號來判斷存取權。 例如，您可以使用登入風險層級、要求的位置等等。 如需條件式存取的詳細資訊，請參閱[總覽](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access)和[部署計畫](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access)。

## <a name="azure-sso-technical-requirements"></a>Azure SSO 技術需求

下一節詳細說明設定特定應用程式的需求，包括所需的環境、端點、宣告對應、必要的屬性、憑證和使用的通訊協定。 您將需要這項資訊，才能在[Azure AD 入口網站](https://portal.azure.com/)中設定 SSO。

### <a name="authentication-mechanism-details"></a>驗證機制詳細資料

針對所有預先整合的 SaaS 應用程式，Microsoft 提供教學課程，您不需要此資訊。 如果應用程式不在我們的應用程式 marketplace/資源庫中，您可能需要收集下列資料片段：

- **應用程式用於 SSO 的目前識別提供者（如果適用**），例如： AD FS、PingFederate、Okta
- **目標應用程式所支援的通訊協定**，例如 SAML 2.0、OpenID Connect、OAuth、表單架構驗證、Ws-送出、ws-trust
- **使用 Azure AD 設定的通訊協定**-例如，SAML 2.0 或1.1、OpenID Connect、OAuth、以表單為基礎的 WS-送出

### <a name="attribute-requirements"></a>屬性需求

在 Azure AD 使用者物件和每個 SaaS 應用程式的使用者物件之間，有一組預先設定的屬性和屬性對應。 某些應用程式會管理其他類型的物件，例如群組。 規劃將使用者屬性從 Azure AD 到應用程式的對應，並根據您的業務需求[自訂預設的屬性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)對應。

### <a name="certificate-requirements"></a>憑證需求

應用程式的憑證必須是最新的，或使用者無法存取應用程式的風險。 大部分的 SaaS 應用程式憑證都適用于36個月。 您在應用程式分頁中變更了該憑證的持續時間。 請務必記錄到期日，並瞭解您將如何管理憑證更新。 

有兩種方式可以管理您的憑證。 

- **自動憑證變換**-Microsoft 支援[在 Azure AD 中簽署金鑰變換](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover)。 雖然這是管理憑證的慣用方法，但並非所有 ISV 都支援此案例。

- **手動更新**-每個應用程式都有自己的憑證，它會根據其定義的方式過期。 在應用程式的憑證到期之前，請先建立新的憑證，並將它傳送給 ISV。 這種資訊可以從同盟中繼資料提取。 [如需深入瞭解同盟中繼資料，請參閱這裡。](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata)

## <a name="implement-sso"></a>執行 SSO

使用下列階段在您的組織中規劃和部署您的解決方案：

### <a name="user-configuration-for-sso"></a>SSO 的使用者設定

- **識別您的測試使用者**

   請洽詢應用程式擁有者，並要求他們在應用程式內建立最少三個測試使用者。 請確定您將用來做為主要識別碼的資訊已正確填入，且符合 Azure AD 中可用的屬性。 在大部分情況下，這會對應至 SAML 型應用程式的「NameID」。 對於 JWT 權杖，這是「preferred_username」。
   
   Azure AD 以以雲端為基礎的使用者身分以手動方式建立使用者，或使用 Azure AD Connect 同步處理引擎從內部部署同步處理使用者。 請確定資訊符合傳送至應用程式的宣告。

- **設定 SSO**

   從[應用程式清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)中，找出並開啟應用程式的 SSO 教學課程，然後依照教學課程的步驟，成功設定您的 SaaS 應用程式。

   如果找不到您的應用程式，請參閱[自訂應用程式檔](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery)。 這會逐步引導您瞭解如何新增不在 Azure AD 資源庫中的應用程式。

   （選擇性）您可以使用[Microsoft 的指引檔](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)，針對企業應用程式的 SAML 權杖中發出的宣告。 請確定這會對應到您預期會在應用程式的 SAML 回應中收到的內容。 如果您在設定期間遇到問題，請使用我們的指引[來進行 SSO 整合的調試](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)。

### <a name="provide-sso-change-communications-to-end-users"></a>為使用者提供 SSO 變更通訊

實行您的通訊計畫。 請確定您是讓終端使用者知道變更即將推出、何時抵達、現在該怎麼做，以及如何尋求協助。

### <a name="verify-end-user-scenarios-for-sso"></a>確認 SSO 的終端使用者案例

您可以使用下列測試案例來進行公司擁有和個人裝置上的測試，以確保您的 SSO 設定如預期般運作。 下列案例假設使用者流覽至應用程式 URL，並通過服務提供者（SP 起始的驗證流程）所起始的驗證流程。

| 狀況 | 由使用者在 SP 起始的驗證流程上預期的結果 |
|----------|---------------------------------------------------|
| 在公司網路上使用 IE 登入應用程式。 | 整合式 Windows 驗證（IWA）會發生，而且不會出現任何額外的提示。 |
| 透過新的登入嘗試，在公司網路中登入具有 IE 的應用程式。 | AD FS 伺服器上以表單為基礎的提示。 使用者成功登入 MFA 的瀏覽器提示。 |
| 使用目前的會話來登入具有 IE 的應用程式，但從未執行過 MFA。 | 使用者不會收到第一個因素的提示。 使用者收到 MFA 的提示。 |
| 以目前的會話關閉公司網路，並在此會話中執行 MFA 時，使用 IE 登入應用程式。 | 使用者不會收到第一個因素的提示。 使用者未收到 MFA。 使用者 SSOs 到應用程式。 |
| 以目前的會話關閉公司網路，並在此會話中執行 MFA 時，以 Chrome/Firefox/Safari 登入應用程式。 | 使用者不會收到第一個因素的提示。 使用者未收到 MFA。 使用者 SSO 的應用程式。 |
| 透過新的登入嘗試，以 Chrome/Firefox/Safari 登入應用程式，同時關閉公司網路。 | AD FS 伺服器上以表單為基礎的提示。 使用者成功登入 MFA 的瀏覽器提示。 |
| 在具有目前會話的公司網路上，使用 Chrome/Firefox 登入應用程式。 | 使用者不會收到第一個因素的提示。 使用者未收到 MFA。 使用者 SSO 的應用程式。 |
| 以具有新登入嘗試的應用程式行動應用程式登入應用程式。 | AD FS 伺服器上以表單為基礎的提示。 使用者成功登入和 ADAL 用戶端提示以進行 MFA。 |
| 未經授權的使用者嘗試使用登入 URL 登入應用程式。 | AD FS 伺服器上以表單為基礎的提示。 使用者無法以第一個因素登入。 |
| 授權的使用者嘗試登入，但輸入的密碼不正確。 | 使用者流覽至應用程式 URL，並收到錯誤的使用者名稱/密碼錯誤。 |
| 已授權的使用者按一下電子郵件中的連結，並已通過驗證。 | 使用者按一下 [URL] 並登入應用程式，而不會出現其他提示。 |
| 已授權的使用者按一下電子郵件中的連結，但尚未經過驗證。 | 使用者按一下 URL，系統會提示您使用第一個因素進行驗證。 |
| 已授權的使用者使用應用程式行動應用程式（SP 起始）登入應用程式，並嘗試新的登入。 | AD FS 伺服器上以表單為基礎的提示。 使用者成功登入和 ADAL 用戶端提示以進行 MFA。 |
| 未經授權的使用者嘗試使用登入 URL （SP 起始）登入應用程式。 | AD FS 伺服器上以表單為基礎的提示。 使用者無法以第一個因素登入。 |
| 授權的使用者嘗試登入，但輸入的密碼不正確。| 使用者流覽至應用程式 URL，並收到錯誤的使用者名稱/密碼錯誤。 |
| 已授權的使用者登出後再重新登入。 | 如果已設定登出 URL，則使用者會登出所有服務，並提示您進行驗證。 |
| 已授權的使用者登出後再重新登入。 | 如果未設定 [登出 URL]，則會使用現有的 Azure AD 瀏覽器會話中現有的權杖自動重新登入使用者。 |
| 已授權的使用者按一下電子郵件中的連結，並已通過驗證。 | 使用者按一下 [URL] 並登入應用程式，而不會出現其他提示。 |
| 已授權的使用者按一下電子郵件中的連結，但尚未經過驗證。 | 使用者按一下 URL，系統會提示您使用第一個因素進行驗證。 |

## <a name="manage-sso"></a>管理 SSO

本節概述成功管理 SSO 的需求和建議。

### <a name="required-administrative-roles"></a>必要的系統管理角色

請一律使用具備最少許可權的角色，以在 Azure Active Directory 內完成必要的工作。 Microsoft 建議您[檢查可用的不同角色](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)，並選擇正確的角色來解決此應用程式每個角色的需求。 在完成部署後，可能需要暫時套用一些角色並加以移除。

| 生活| 角色 | Azure AD 角色（如有必要） |
|--------|-------|-----------------------------|
| 技術支援中心系統管理員 | 第1層支援 | 無 |
| 身分識別管理員 | 在問題影響時設定和調試 Azure AD | 全域系統管理員 |
| 應用程式管理員 | 應用程式中的使用者證明，具有許可權的使用者設定 | 無 |
| 基礎結構管理員 | 憑證變換擁有者 | 全域系統管理員 |
| 商務擁有者/專案關係人 | 應用程式中的使用者證明，具有許可權的使用者設定 | 無 |

我們建議使用[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) （PIM）來管理您的角色，以針對具有目錄許可權的使用者提供額外的審核、控制和存取權審查。

### <a name="sso-certificate-lifecycle-management"></a>SSO 憑證生命週期管理

請務必找出正確的角色和電子郵件通訊群組清單，負責管理 Azure AD 和使用單一登入設定之應用程式之間的簽署憑證生命週期。 以下是我們建議您備妥的一些重要角色：

- 在應用程式中更新使用者屬性的擁有者
- 應用程式中斷/修正支援的擁有者-呼叫
- 與憑證相關的變更通知密切受監視的電子郵件通訊群組清單

憑證的最長存留期為三年。 我們建議您建立如何處理 Azure AD 與應用程式之間憑證變更的流程。 這有助於防止或減少因憑證過期或強制憑證變換而造成的中斷。

### <a name="rollback-process"></a>復原進程

當您根據測試案例完成測試之後，就可以使用您的應用程式移至生產環境。 移至生產環境表示您將在生產環境租使用者中執行規劃和測試的設定，並將其推出給您的使用者。 不過，如果您的部署未依照規劃執行，請務必規劃該怎麼做。 如果 SSO 設定在部署期間失敗，您必須瞭解如何減輕任何中斷情況，並降低對使用者的影響。

應用程式內的驗證方法可用性會決定您的最佳策略。 請務必確定您有應用程式擁有者的詳細檔，以瞭解您的部署是否遇到問題時，如何回到原始的登入設定狀態。

- **如果您的應用程式支援多個身分識別提供者**（例如 LDAP 和 AD FS 和 Ping），請不要在推出期間刪除現有的 SSO 設定。 相反地，在遷移期間停用它，以防您稍後需要將其切換回來。 

- **如果您的應用程式不支援多個 idp** ，但允許使用者使用表單架構驗證（使用者名稱/密碼）登入，請確定在新的 SSO 設定推出失敗時，使用者可以切換回這種方法。

### <a name="access-management"></a>存取管理

我們建議您在管理資源的存取權時選擇調整的方法。 常見的方法包括利用內部部署群組，方法是透過 Azure AD Connect 同步處理、[根據使用者屬性在 Azure AD 中建立動態群組](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal)，或在資源擁有者所管理的 Azure AD 中建立[自助群組](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)。

### <a name="monitor-security"></a>監視安全性

我們建議您定期設定，以查看 SaaS 應用程式安全性的不同層面，並執行所需的任何補救動作。

### <a name="troubleshooting"></a>疑難排解

下列連結呈現疑難排解案例。 您可能會想要建立支援人員的特定指南，其中包含這些案例和修正它們的步驟。

#### <a name="consent-issues"></a>同意問題

- [未預期的同意錯誤](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-prompt)

- [使用者同意錯誤](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-error)

#### <a name="sign-in-issues"></a>登入問題

- [從自訂入口網站登入時發生的問題](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-deeplink)

- [從存取面板登入時發生問題](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-access-panel)

- [應用程式登入頁面發生錯誤](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-application-error)

- [登入 Microsoft 應用程式時發生問題](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-first-party-microsoft)

#### <a name="sso-issues-for-applications-listed-in-the-azure-application-gallery"></a>Azure 應用程式資源庫中所列應用程式的 SSO 問題

- [Azure 應用程式資源庫中所列之應用程式的密碼 SSO 問題](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery) 

- [Azure 應用程式資源庫中所列之應用程式的同盟 SSO 問題](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-gallery)   

#### <a name="sso-issues-for-applications-not-listed-in-the-azure-application-gallery"></a>Azure 應用程式資源庫中未列出之應用程式的 SSO 問題

- [Azure 應用程式資源庫中未列出之應用程式的密碼 SSO 問題](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-non-gallery) 

- [Azure 應用程式資源庫中未列出之應用程式的同盟 SSO 問題](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-non-gallery)

## <a name="next-steps"></a>後續步驟

[偵錯 SAML 型 SSO](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

[透過 PowerShell 進行應用程式的宣告對應](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

[自訂 SAML 權杖中發出的宣告](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

[單一登入 SAML 通訊協定](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

[單一登出 SAML 通訊協定](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-out-protocol-reference)

[AZURE AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) （適用于合作夥伴和廠商等外部使用者）

[Azure AD 條件式存取](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

[Azure 身分識別保護](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

[SSO 存取](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

[應用程式 SSO 教學課程](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)