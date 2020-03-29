---
title: 規劃 Azure 活動目錄單一登入部署
description: 説明您在組織中規劃、部署和管理 SSO 的指南。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92496fa572c5c1cae4588f82ac61c18de3024045
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76512822"
---
# <a name="plan-a-single-sign-on-deployment"></a>規劃單一登入部署

單一登入 （SSO） 意味著僅使用單個使用者帳戶登錄一次即可訪問使用者需要的所有應用程式和資源。 使用 SSO，使用者可以訪問所有必需的應用程式，而無需進行第二次身份驗證。

## <a name="benefits-of-sso"></a>SSO 的優勢

當使用者登錄到 Azure 活動目錄 （Azure AD） 中的應用程式時，單一登入 （SSO） 增加了安全性和便利性。 

許多組織依賴軟體即服務 （SaaS） 應用程式（如 Office 365、Box 和 Salesforce）來提高最終使用者的工作效率。 從歷史上看，IT 人員需要在每個 SaaS 應用程式中單獨創建和更新使用者帳戶，使用者需要記住每個應用程式的密碼。

Azure 應用商店具有 3000 多個具有預集成 SSO 連接的應用程式，因此可以輕鬆地將它們集成到租戶中。

## <a name="licensing"></a>授權

- **Azure AD 許可**- 用於預集成 SaaS 應用程式的 SSO 是免費的。 但是，目錄中的物件數和要部署的功能可能需要其他許可證。 有關許可證要求的完整清單，請參閱[Azure 活動目錄定價](https://azure.microsoft.com/pricing/details/active-directory/)。
- **應用程式許可**- 您需要為 SaaS 應用程式提供適當的許可證，以滿足您的業務需求。 與應用程式擁有者合作，確定分配給應用程式的使用者是否具有適合其在應用程式中角色的許可證。 如果 Azure AD 根據角色管理自動預配，則 Azure AD 中分配的角色必須與應用程式中擁有的許可證數一致。 應用程式中擁有的許可證數量不正確可能會導致使用者預配/更新期間出錯。

## <a name="plan-your-sso-team"></a>規劃您的 SSO 團隊

- **吸引合適的利益相關者**- 當技術專案失敗時，通常是由於對影響、結果和責任的期望不匹配。 為了避免這些陷阱，[請確保您與合適的利益相關者接洽](https://aka.ms/deploymentplans)，並確保利益相關者瞭解他們的角色。
- **計畫通信**- 通信對於任何新服務的成功都至關重要。 主動向使用者傳達他們的體驗將如何變化、何時更改以及遇到問題時如何獲得支援。 查看[最終使用者如何訪問其啟用 SSO 的應用程式](end-user-experiences.md)的選項，並製作您的通信以匹配您的選擇。 

## <a name="plan-your-sso-protocol"></a>規劃您的 SSO 協定

基於聯合協定的 SSO 實現提高了安全性、可靠性和最終使用者體驗，並且更易於實現。 許多應用程式都預先集成在 Azure AD 中，並提供[分步指南](../saas-apps/tutorial-list.md)。 您可以在我們的[Azure 應用商店](https://azuremarketplace.microsoft.com/marketplace/)中找到它們。 有關每個 SSO 方法的詳細資訊，請參閱 Azure[活動目錄中的應用程式單一登入](what-is-single-sign-on.md)一文。

可通過兩種主要方式使使用者能夠單一登入應用：

- **具有聯合單登錄**Azure AD 使用其 Azure AD 帳戶對使用者進行身份驗證。 此方法支援支援 SAML 2.0、WS-聯合或 OpenID 連接等協定的應用程式，並且是單一登入最豐富的模式。 我們建議在應用程式支援 Azure AD 時將聯合 SSO 與 Azure AD 一起使用，而不是使用基於密碼的 SSO 和 ADFS。

- **使用基於密碼的單一登入**使用者首次使用使用者名和密碼登錄到應用程式時，他們可以訪問該應用程式。 第一次登入之後，Azure AD 就會向應用程式提供使用者名稱和密碼。 密碼式單一登入可以使用網頁瀏覽器擴充功能或行動應用程式，安全儲存應用程式的密碼以及重新執行。 此選項利用應用程式提供的現有登錄過程，使管理員能夠管理密碼，並且不需要使用者知道密碼。

### <a name="considerations-for-federation-based-sso"></a>基於聯合的 SSO 的注意事項

- **使用 OpenID 連接和 OAuth** - 如果您要連接到的應用程式支援它，請使用 OIDC/OAuth 2.0 方法將您的 SSO 啟用該應用程式。 此方法需要較少的配置，並可實現更豐富的使用者體驗。 有關詳細資訊，請參閱[OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md) [、OpenID 連接 1.0](../develop/v2-protocols-oidc.md)和[Azure 活動目錄開發人員指南](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)。
- **基於 SAML 的端點配置**- 如果您使用 SAML，則開發人員在配置應用程式之前將需要特定資訊。 有關詳細資訊，請參閱[編輯基本 SAML 配置](configure-single-sign-on-non-gallery-applications.md)。
- **基於 SAML 的 SSO 證書管理**- 當您為應用程式啟用聯合 SSO 時，Azure AD 會創建預設有效期為三年的證書。 如果需要，您可以自訂該證書的到期日期。 確保您有在證書過期之前續訂證書的流程。 要瞭解更多資訊，請參閱[Azure AD 管理證書](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)。

### <a name="considerations-for-password-based-sso"></a>基於密碼的 SSO 的注意事項

使用 Azure AD 進行基於密碼的 SSO 需要部署瀏覽器延伸，該擴展將安全地檢索憑據並填寫登錄表單。 定義一種機制，以便使用[受支援的瀏覽器](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)大規模部署擴展。 選項包括：

- [Internet 資源管理器的群組原則](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)
- [互聯網資源管理器的組態管理員](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)
- [Chrome、火狐、微軟邊緣或 IE 的使用者驅動下載和配置](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

要瞭解更多資訊，請參閱[如何配置密碼單一登入](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery)。

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>捕獲不在庫中的應用程式的登錄表單中繼資料

Microsoft 支援在 Web 應用程式上捕獲中繼資料以進行密碼保管（捕獲使用者名和密碼欄位）。 在配置應用程式以捕獲表單中繼資料的過程中導航到登錄 URL。 向應用程式擁有者詢問確切的登錄 URL。 此資訊在登錄過程中使用，在登錄期間將 Azure AD 憑據映射到應用程式。

要瞭解更多資訊，請參閱[什麼是使用 Azure AD 的應用程式訪問和 SSO？](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/)

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>指示需要重新捕獲表單中的中繼資料

當應用程式更改其 HTML 佈局時，您可能需要重新捕獲中繼資料以調整更改。 指示 HTML 佈局已更改的常見症狀包括：

- 使用者報告按一下應用程式會"卡住"登錄頁
- 報告使用者名或密碼未填充的使用者

#### <a name="shared-accounts"></a>共用帳戶

從登錄的角度來看，具有共用帳戶的應用程式與為單個使用者使用密碼 SSO 的庫應用程式沒有區別。 但是，在規劃和配置旨在使用共用帳戶的應用程式時，還需要執行一些其他步驟：

1. 與應用程式企業用戶合作記錄以下內容：
   1. 組織中將使用應用程式的使用者集
   1. 與使用者集關聯的應用程式中的現有憑據集 
1. 對於使用者集和憑據的每個組合，根據您的要求在雲或本地創建安全性群組。
1. 重置共用憑據。 在 Azure AD 中部署應用後，個人不需要共用帳戶的密碼。 由於 Azure AD 將存儲密碼，因此請考慮將其設置為非常長且複雜。 
1. 如果應用程式支援密碼，則配置密碼的自動翻轉。 這樣，即使是進行初始設置的管理員也不會知道共用帳戶的密碼。 

## <a name="plan-your-authentication-method"></a>規劃身份驗證方法

選擇正確的驗證方法是設定 Azure AD 混合式身分識別解決方案的第一項重要決策。 請實作使用 Azure AD Connect 所設定的驗證方法，這也會在雲端佈建使用者。

若要選擇驗證方法，需要考慮實作您選取項目的時間、現有基礎結構、複雜度及成本。 這些因素對每個組織而言不同，並可能隨時間改變。 您應該選擇最符合特定方案的方案。 有關詳細資訊，請參閱為[Azure 活動目錄混合標識解決方案選擇正確的身份驗證方法](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn)。

## <a name="plan-your-security-and-governance"></a>規劃安全性和治理 

身份是安全關注和投資的新的主要支點，因為隨著 BYOD 設備和雲應用程式的爆炸式發展，網路週邊變得越來越多孔，效率也越來越低。 

### <a name="plan-access-reviews"></a>規劃訪問審核

[訪問審核](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)使組織能夠高效地管理組成員身份、訪問企業應用程式和角色指派。 您應該計畫定期查看使用者存取權限，以確保只有合適的人員才能繼續訪問。

設置訪問審核時要規劃的一些關鍵主題包括：

1. 確定適合您業務需求的訪問審核的節奏。 這可能像每週、每月、每年或按需練習一樣頻繁。

1. 創建表示應用訪問報表檢閱者的組。 您需要確保最熟悉應用及其目標使用者和用例的利益相關者是訪問審核的參與者

1. 完成訪問審核包括取消不再需要存取權限的使用者的應用存取權限。 計畫處理來自被拒絕使用者的潛在支援請求。 已刪除的使用者將在 Azure AD 中保留 30 天，在此期間，如有必要，管理員可以還原這些使用者。 經過 30 天後，該使用者將永久刪除。 使用 Azure 活動目錄門戶，全域管理員可以在達到該時間段之前顯式永久刪除最近刪除的使用者。

### <a name="plan-auditing"></a>計畫審核

Azure AD 提供[包含技術和業務見解的報告](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/)。 

安全和活動報告都可用。 安全報告顯示標記為風險的使用者，以及風險登錄。 活動報告通過詳細說明登錄活動並提供所有登錄的審核跟蹤，説明您瞭解組織中使用者的行為。 您可以使用報表來管理風險、提高工作效率和監控合規性。

| 報表類型 | 訪問審核 | 安全性報告 | 登錄報告 |
|-------------|---------------|------------------|----------------|
| 用於查看 | 應用程式許可權和使用方式。 | 可能受損的帳戶 | 誰正在訪問應用程式 |
| 潛在行動 | 審計訪問;撤銷許可權 | 撤銷存取權限;強制安全重置 | [撤銷存取權] |

Azure AD 會保留大多數審核資料 30 天，並通過 Azure 監管中心或 API 提供資料，以便下載到分析系統中。

### <a name="consider-using-microsoft-cloud-application-security"></a>考慮使用微軟雲應用程式安全性

微軟雲應用安全 （MCAS） 是雲訪問安全代理 （CASB） 解決方案。 它使您能夠瞭解雲應用和服務，提供複雜的分析以識別和打擊網路威脅，並使您能夠控制資料傳輸方式。

部署 MCAS 使您能夠：

- 使用"雲發現"映射和標識雲環境以及您的組織正在使用的雲應用。
- 在雲中制裁和取消制裁應用
- 使用易於部署的應用連接器，利用供應商 API，查看和治理您連接到的應用
- 使用條件訪問應用控制保護，獲得即時可見度並控制雲應用中的訪問和活動
- 通過設置策略，然後持續微調策略，説明您獲得持續控制。

Microsoft 雲應用程式安全 （MCAS） 會話控制項可用於任何作業系統上任何主要平臺上的任何瀏覽器。 您也可以封鎖或允許行動應用程式和傳統型應用程式。 通過本機與 Azure AD 集成，可以支援任何配置 SAML 的應用，或支援在 Azure AD 中單一登入的"打開 ID 連接"應用，包括[多個特色應用](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)。

有關 MCAS 的資訊，請參閱[Microsoft 雲應用安全概述](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)。 MCAS 是一種基於使用者的訂閱服務。 您可以在[MCAS 許可資料表中](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO)查看許可詳細資訊。

### <a name="use-conditional-access"></a>使用條件式存取

借助條件訪問，您可以自動為雲應用做出基於條件的存取控制決策。

完成第一個要素驗證之後，即會強制執行條件式存取原則。 因此，條件訪問不是針對拒絕服務 （DoS） 攻擊等方案的第一線防禦，而是可以使用這些事件發出的信號來確定訪問。 例如，可以使用登錄風險級別、請求的位置等。 有關條件訪問的詳細資訊[，請參閱概述](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access)和[部署計畫](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access)。

## <a name="azure-sso-technical-requirements"></a>Azure SSO 技術要求

以下部分詳細介紹了配置特定應用程式的要求，包括必要的環境、終結點、宣告對應、所需屬性、證書和所使用的協定。 您需要此資訊才能在[Azure AD 門戶](https://portal.azure.com/)中配置 SSO。

### <a name="authentication-mechanism-details"></a>身份驗證機制詳細資訊

對於所有預集成的 SaaS 應用，Microsoft 提供了教程，您不需要此資訊。 如果應用程式不在我們的應用程式市場/庫中，您可能需要收集以下資料片段：

- **應用程式為 SSO 使用的當前標識提供程式（如果適用）** - 例如：AD FS、Ping Federate、Okta
- **目標應用程式支援的協定**- 例如，SAML 2.0、OpenID 連接、OAuth、基於表單的 Auth、WS-Fed、WS-Trust
- **協定配置 Azure AD** - 例如，SAML 2.0 或 1.1、OpenID 連接、OAuth、基於表單的 WS-Fed

### <a name="attribute-requirements"></a>屬性要求

Azure AD 使用者物件和每個 SaaS 應用的使用者物件之間有一組預先配置的屬性和屬性對應。 某些應用管理其他類型的物件，如組。 規劃從 Azure AD 到應用程式的使用者屬性對應，並根據業務需求[自訂預設屬性映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)。

### <a name="certificate-requirements"></a>憑證需求

應用程式的證書必須是最新的，否則使用者可能會無法訪問該應用程式。 大多數 SaaS 應用程式證書的 36 個月都適用。 在應用程式邊欄選項卡中更改該證書持續時間。 請務必記錄過期情況，並瞭解如何管理證書續訂。 

有兩種方法可以管理證書。 

- **自動證書滾動**- 微軟支援[在 Azure AD 中簽名金鑰滾動。](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover) 雖然這是我們管理證書的首選方法，但並非所有 ISV 都支援此方案。

- **手動更新**- 每個應用程式都有自己的證書，該證書會根據定義方式過期。 在應用程式的證書過期之前，請創建新證書並將其發送到 ISV。 可以從聯合中繼資料提取此資訊。 [在此處閱讀有關聯合中繼資料的更多內容。](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata)

## <a name="implement-sso"></a>實現 SSO

使用以下階段規劃和部署組織中的解決方案：

### <a name="user-configuration-for-sso"></a>SSO 的使用者配置

- **識別測試使用者**

   聯繫應用擁有者，要求他們在應用程式中至少創建三個測試使用者。 確保正確填充主識別碼時使用的資訊，並匹配 Azure AD 中可用的屬性。 在大多數情況下，這將映射到基於 SAML 的應用程式的"NameID"。 對於 JWT 權杖，它是"preferred_username"。
   
   在 Azure AD 中手動創建使用者作為基於雲的使用者，或使用 Azure AD 連接同步引擎從本地同步使用者。 確保資訊與發送到應用程式的聲明匹配。

- **設定 SSO**

   從[應用程式清單中](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)，查找並打開應用程式的 SSO 教程，然後按照本教程的步驟成功配置 SaaS 應用程式。

   如果找不到應用程式，請參閱[自訂應用程式文檔](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery)。 這將引導您瞭解如何添加不在 Azure AD 庫中的應用程式。

   或者，您可以使用[Microsoft 的指導文檔](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)為企業應用程式使用 SAML 權杖中發出的聲明。 確保此映射到您希望在應用程式的 SAML 回應中收到的內容。 如果您在配置過程中遇到問題，請使用有關如何[調試 SSO 集成](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)的指導。

自訂應用程式載入是 Azure AD 高級 P1 或 P2 許可證功能。

### <a name="provide-sso-change-communications-to-end-users"></a>向最終使用者提供 SSO 更改通信

實施溝通計畫。 確保您讓最終使用者知道更改即將出現、更改何時到達、現在該怎麼辦以及如何尋求説明。

### <a name="verify-end-user-scenarios-for-sso"></a>驗證 SSO 的最終使用者方案

您可以使用以下測試案例對公司擁有的設備和個人設備進行測試，以確保 SSO 配置按預期工作。 以下方案假定使用者正在導航到應用程式 URL 並遍達由服務提供者啟動的身份驗證流（SP 啟動身份驗證流）。

| 狀況 | 按使用者對 SP 啟動的 auth 流的預期結果 |
|----------|---------------------------------------------------|
| 在公司網路上登錄 IE 應用程式。 | 集成的 Windows 身份驗證 （IWA） 在沒有其他提示時發生。 |
| 使用 IE 登錄到應用程式，同時使用新的登錄嘗試關閉公司網路。 | AD FS 伺服器上的基於表單的提示。 使用者成功登錄並提示 MFA。 |
| 使用 IE 登錄到應用程式，同時使用當前會話關閉公司網路，並且從未執行 MFA。 | 使用者不會收到第一個因素的提示。 使用者收到 MFA 的提示。 |
| 使用 IE 登錄到應用程式，同時使用當前會話關閉公司網路，並且已在此會話中執行 MFA。 | 使用者不會收到第一個因素的提示。 使用者不會收到 MFA。 使用者 SSO 進入應用程式。 |
| 使用 Chrome/Firefox/Safari 登錄應用程式，同時使用當前會話關閉公司網路，並且已在此會話中執行 MFA。 | 使用者不會收到第一個因素的提示。 使用者不會收到 MFA。 使用者 SSO 進入應用程式。 |
| 登錄進入應用程式與Chrome/火狐/Safari，而關閉公司網路與新的登錄嘗試。 | AD FS 伺服器上的基於表單的提示。 使用者成功登錄並提示 MFA。 |
| 在商業網路上使用 Chrome/Firefox 在當前會話登錄應用程式。 | 使用者不會收到第一個因素的提示。 使用者不會收到 MFA。 使用者 SSO 進入應用程式。 |
| 使用新的登錄嘗試使用應用程式移動應用程式登錄到應用程式。 | AD FS 伺服器上的基於表單的提示。 使用者成功登錄，ADAL 用戶端提示 MFA。 |
| 未經授權的使用者嘗試使用登錄 URL 登錄應用程式。 | AD FS 伺服器上的基於表單的提示。 使用者無法使用第一個因素登錄。 |
| 授權使用者嘗試登錄但輸入不正確的密碼。 | 使用者導航到應用程式 URL 並收到錯誤的使用者名/密碼錯誤。 |
| 授權使用者按一下電子郵件中的連結，並已通過身份驗證。 | 使用者按一下 URL 並登錄到應用程式中，沒有其他提示。 |
| 授權使用者按一下電子郵件中的連結，但尚未經過身份驗證。 | 使用者按一下 URL 並提示使用第一個因素進行身份驗證。 |
| 授權使用者使用應用程式移動應用程式 （SP 啟動） 登錄應用程式，並嘗試新的登錄。 | AD FS 伺服器上的基於表單的提示。 使用者成功登錄，ADAL 用戶端提示 MFA。 |
| 未經授權的使用者嘗試使用登錄 URL （SP 啟動） 登錄應用程式。 | AD FS 伺服器上的基於表單的提示。 使用者無法使用第一個因素登錄。 |
| 授權使用者嘗試登錄但輸入不正確的密碼。| 使用者導航到應用程式 URL 並收到錯誤的使用者名/密碼錯誤。 |
| 授權使用者登出，然後再次登錄。 | 如果配置了登出 URL，則使用者將登出所有服務並提示進行身份驗證。 |
| 授權使用者登出，然後再次登錄。 | 如果未配置登出 URL，使用者將自動使用現有 Azure AD 瀏覽器會話中的現有權杖重新登錄。 |
| 授權使用者按一下電子郵件中的連結，並已通過身份驗證。 | 使用者按一下 URL 並登錄到應用程式中，沒有其他提示。 |
| 授權使用者按一下電子郵件中的連結，但尚未經過身份驗證。 | 使用者按一下 URL 並提示使用第一個因素進行身份驗證。 |

## <a name="manage-sso"></a>管理 SSO

本節概述了成功管理 SSO 的要求和建議。

### <a name="required-administrative-roles"></a>所需的管理角色

始終使用具有最少可用許可權的角色，以完成 Azure 活動目錄中所需的任務。 Microsoft 建議[查看可用的不同角色，](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)並選擇正確的角色來解決此應用程式每個角色的需求。 某些角色可能需要臨時應用並在部署完成後刪除。

| 角色| 角色 | Azure AD 角色（如果需要） |
|--------|-------|-----------------------------|
| 説明台管理員 | 第 1 層支援 | None |
| 身份管理員 | 當問題影響 Azure AD 時配置和調試 | 全域管理員 |
| 應用程式管理員 | 使用者在應用程式中的證明，對具有許可權的使用者的配置 | None |
| 基礎架構管理員 | 證書滾存擁有者 | 全域管理員 |
| 企業主/利益相關者 | 使用者在應用程式中的證明，對具有許可權的使用者的配置 | None |

我們建議使用[特權標識管理](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)（PIM） 來管理您的角色，為具有目錄許可權的使用者提供其他審核、控制和訪問審查。

### <a name="sso-certificate-lifecycle-management"></a>SSO 證書生命週期管理

確定負責管理 Azure AD 和正在配置單一登入的應用程式之間的簽章憑證生命週期的正確角色和電子郵件通訊群組清單非常重要。 以下是我們建議的一些關鍵角色：

- 用於更新應用程式中的使用者屬性的擁有者
- 使用者隨叫隨到，以尋求應用程式中斷/修復支援
- 密切監視與證書相關的更改通知的電子郵件通訊群組清單

證書的最大存留期為三年。 我們建議建立一個過程，介紹如何處理 Azure AD 和應用程式之間的證書更改。 這有助於防止或最小化由於證書過期或強制證書滾動而中斷。

### <a name="rollback-process"></a>回滾過程

完成基於測試案例的測試後，是時候隨應用程式進入生產。 遷移到生產意味著您將在生產租戶中實現計畫配置和測試組態，並將其推廣到使用者。 但是，請務必規劃在部署未按計劃進行的情況下該怎麼做。 如果 SSO 配置在部署期間失敗，您必須瞭解如何緩解任何中斷並減少對使用者的影響。

應用程式中身份驗證方法的可用性將決定最佳策略。 始終確保為應用擁有者提供詳細的文檔，說明在部署遇到問題時如何回到原始登錄配置狀態。

- **如果應用支援多個識別提供程式**（例如 LDAP 和 AD FS 和 Ping），則在推出期間不要刪除現有的 SSO 配置。 而是在遷移期間禁用它，以防以後需要將其切換回來。 

- **如果應用不支援多個 IDP，** 但允許使用者使用基於表單的身份驗證（使用者名/密碼）登錄，請確保使用者可以在新的 SSO 配置推出失敗時回退此方法。

### <a name="access-management"></a>存取管理

我們建議在管理對資源的訪問時選擇縮放的方法。 常見方法包括通過 Azure AD 連接同步來利用本機群組，[根據使用者屬性在 Azure AD 中創建動態組](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal)，或在資源擁有者管理的 Azure AD 中創建[自助服務組](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)。

### <a name="monitor-security"></a>監控安全性

我們建議設置常規節奏，其中查看 SaaS 應用安全性的不同方面，並執行所需的任何補救措施。

### <a name="troubleshooting"></a>疑難排解

以下連結存在故障排除方案。 您可能需要為支援人員創建一個特定的指南，其中包含這些方案和修復它們的步驟。

#### <a name="consent-issues"></a>同意問題

- [意外同意錯誤](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-prompt)

- [使用者同意錯誤](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-error)

#### <a name="sign-in-issues"></a>登入問題

- [從自訂門戶登錄時遇到問題](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-deeplink)

- [從存取面板登入時發生問題](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-access-panel)

- [應用程式登入頁面發生錯誤](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-application-error)

- [登錄到 Microsoft 應用程式時出現問題](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-first-party-microsoft)

#### <a name="sso-issues-for-applications-listed-in-the-azure-application-gallery"></a>Azure 應用程式庫中列出的應用程式的 SSO 問題

- [Azure 應用程式庫中列出的應用程式的密碼 SSO 問題](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery) 

- [Azure 應用程式庫中列出的應用程式的聯合 SSO 問題](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-gallery)   

#### <a name="sso-issues-for-applications-not-listed-in-the-azure-application-gallery"></a>Azure 應用程式庫中未列出的應用程式的 SSO 問題

- [Azure 應用程式庫中未列出的應用程式的密碼 SSO 問題](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-non-gallery) 

- [Azure 應用程式庫中未列出的應用程式的聯合 SSO 問題](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-non-gallery)

## <a name="next-steps"></a>後續步驟

[偵錯 SAML 型 SSO](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

[通過 PowerShell 應用的宣告對應](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

[自訂在 SAML 權杖中發出的聲明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

[單登錄 SAML 協定](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

[單一登出 SAML 通訊協定](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-out-protocol-reference)

[Azure AD B2B（](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)適用于外部使用者（如合作夥伴和供應商）

[Azure AD 條件訪問](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

[Azure 標識保護](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

[SSO 存取](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

[應用程式 SSO 教程](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
