---
title: 強大的身份基礎的四個步驟 - Azure AD
description: 本主題介紹混合標識客戶為構建強大的身份基礎而可以採取的四個步驟。
services: active-directory
author: martincoetzer
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2019
ms.subservice: hybrid
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3eb98f543e17981be0d5b9ab08fa4e146659b47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74206787"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>使用 Azure 活動目錄建立強大的標識基礎的四個步驟

由於應用快速向雲移動，管理對應用和資料的訪問不能再依賴于傳統的網路安全邊界策略，如周邊網路和防火牆。 現在，組織必須信任其身份解決方案，以控制對組織應用和資料具有存取權限的人員和內容。 越來越多的組織允許員工自帶設備工作，並從任何可以連接到 Internet 的地方使用設備。 確保這些設備符合要求和安全已成為組織選擇實施的標識解決方案中的一個重要考慮因素。 在當今的數位工作場所中，身份是任何遷移到雲的組織[的主要控制平面](https://www.microsoft.com/security/technology/identity-access-management?rtc=1)。

在採用 Azure 活動目錄 （Azure AD） 混合標識解決方案時，組織可以訪問高級功能，這些功能通過自動化、委派、自助服務和單一登入功能來解鎖工作效率。 它允許您的員工從需要工作的地方訪問公司資源，同時允許您的 IT 團隊通過確保合適的人員能夠正確訪問正確的資源來建立安全生產力來管理這種訪問。

根據我們的學習，此最佳實踐清單將説明您快速部署建議的操作，在您的組織中建立*牢固的*身份基礎：

* 輕鬆連接到應用
* 自動為每個使用者建立一個標識
* 為您的使用者安全提供支援
* 操作您的見解

## <a name="step-1---connect-to-apps-easily"></a>步驟 1 - 輕鬆連接到應用

通過將應用與 Azure AD 連接，可以通過啟用單一登入 （SSO） 並執行使用者預配來提高最終使用者的工作效率和安全性。 通過在單個位置 Azure AD 管理應用，可以最大限度地減少管理開銷，並為安全和合規性策略實現單一控制點。

本節介紹用於管理使用者訪問應用、啟用對內部應用的安全遠端存取以及將應用遷移到 Azure AD 的好處的選項。

### <a name="make-apps-available-to-your-users-seamlessly"></a>無縫為使用者提供應用

Azure AD 使管理員能夠將[應用程式添加到](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal) [Azure 門戶](https://portal.azure.com/)中的企業應用程式庫。 將應用程式添加到企業應用程式庫可以更輕鬆地將應用程式佈建為使用 Azure AD 作為標識提供程式。 它還允許您使用條件訪問策略管理使用者對應用程式的訪問，並將單一登入 （SSO） 配置為應用程式，以便使用者不必重複輸入其密碼，並自動登入到本地和基於雲的應用程式。

將應用程式添加到 Azure AD 庫後，使用者可以查看分配給它們的應用，並根據需要搜索和請求其他應用。 Azure AD 為使用者提供[了幾種](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences)訪問其應用的方法：

* 訪問面板/我的應用程式
* Office 365 應用程式啟動器
* 直接登入同盟應用程式
* 直接登入連結

要瞭解有關使用者訪問應用的更多資訊，請參閱本文**中的步驟 3 -- 增強使用者的能力**。

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>將應用從活動目錄聯合服務遷移到 Azure AD

將單一登入配置從活動目錄聯合服務 （ADFS） 遷移到 Azure AD 可實現安全性、更一致的可管理性和協作性的其他功能。 為獲得最佳結果，我們建議您將應用從 AD FS 遷移到 Azure AD。 將應用程式身份驗證和授權引入 Azure AD 可為您提供以下好處：

* 管理成本
* 管理風險
* 提高生產力
* 解決合規性和治理問題

要瞭解更多資訊，請參閱[將應用程式遷移到 Azure 活動目錄](https://aka.ms/migrateapps/whitepaper)的白皮書。

### <a name="enable-secure-remote-access-to-apps"></a>啟用對應用的安全遠端存取

[Azure AD 應用程式代理](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy)為組織提供了一個簡單的解決方案，以便為需要以安全方式訪問內部應用的遠端使用者將本地應用發佈到雲。 對 Azure AD 進行單個登錄後，使用者可以通過外部 URL 或內部應用程式門戶訪問雲和本地應用程式。

Azure AD 應用程式代理具有以下優點：

* 將 Azure AD 擴充至內部部署資源
  * 雲端級別安全性和保護
  * 易於啟用的條件訪問和多重要素驗證等功能
* 周邊網路中沒有元件，如 VPN 和傳統反向代理解決方案
* 沒有所需的輸入連線
* 雲和本地中的設備、資源和應用的單一登入 （SSO）
* 使最終使用者隨時隨地高效工作

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>使用 Microsoft 雲應用安全性發現影子 IT

現代企業 IT 部門通常不會知道使用者用來執行其工作的所有雲端應用程式。 當 IT 管理員被問到他們認為員工使用多少雲應用時，他們平均說 30 或 40 個雲應用。 實際上，組織中的員工平均使用超過 1，000 個單獨的應用。 80% 的員工使用未經批准的應用，但沒有人審查過這些應用，並且可能不符合您的安全和合規性政策。

[Microsoft 雲應用安全](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)（MCAS） 可以説明您識別受 IT 可能會批准並添加到企業應用程式庫的使用者歡迎的有用應用，以便使用者從 SSO 和條件訪問等功能中受益。

<em>**"雲應用安全**可説明我們確保我們的員工正確使用我們的雲和 SaaS 應用程式，以支援有助於保護埃森哲的基本安全性原則。</em> --- [約翰·布拉西，埃森哲資訊安全董事總經理](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

除了檢測影子 IT 之外，MCAS 還可以確定應用的風險級別，防止未經授權訪問公司資料、可能資料洩露以及應用程式固有的其他安全風險。

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>步驟 2 - 自動為每個使用者建立一個標識

在 Azure AD 混合標識解決方案中整合本地目錄和基於雲的目錄，將允許您通過在雲中預配現有標識來重用現有的本地活動目錄投資。 該解決方案與 Azure AD 同步本地標識，而 IT 使本地活動目錄與任何現有治理解決方案一起運行，作為標識的主要真實來源。 Microsoft 的 Azure AD 混合標識解決方案跨越本地和基於雲的功能，創建通用使用者標識，以便對所有資源進行身份驗證和授權，而不管它們位於何處。

將本地目錄與 Azure AD 集成，可提高使用者工作效率，並通過提供用於訪問雲和本地資源的通用標識，防止使用者跨應用和服務使用多個帳戶。 對於最終使用者和 IT 來說，使用多個帳戶都是一個難題。 從最終使用者的角度來看，擁有多個帳戶意味著必須記住多個密碼。 為了避免這種情況，許多使用者為每個帳戶重用相同的密碼，從安全形度來看，這是不好的。 從 IT 角度來看，重用通常會導致更多的密碼重設和説明台成本以及最終使用者投訴。

Azure AD 連接是用於將本地標識同步到 Azure AD 的工具，然後可用於訪問雲應用程式。 標識在 Azure AD 中後，它們可以預配到 SaaS 應用程式（如 Salesforce 或 Concur）。

在本節中，我們列出了為雲提供高可用性、現代身份驗證和減少本地佔用空間的建議。

> [!NOTE]
> 如果要瞭解有關 Azure AD 連接的更多內容，請參閱[什麼是 Azure AD 連接同步？](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>為 Azure AD 連接設置暫存伺服器，並使其保持最新

Azure AD 連接在預配過程中扮演著關鍵角色。 如果同步伺服器由於任何原因離線，則對本地的更改將不會在雲中更新，並導致使用者訪問問題。 請務必定義容錯移轉策略，允許管理員在同步伺服器離線後快速恢復同步。

為了在主 Azure AD Connect 伺服器離線時提供高可用性，建議為 Azure AD Connect 部署單獨的[暫存伺服器](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-staging-server)。 部署伺服器允許管理員通過簡單的配置開關將暫存伺服器"提升"到生產。 在暫存模式下配置待命伺服器還允許您測試和部署新的配置更改，並在舊伺服器停用時引入新伺服器。

> [!TIP]
> Azure AD 連接會定期更新。 因此，強烈建議您保持暫存伺服器的電流，以便利用每個新版本提供的性能改進、錯誤修復和新功能。

### <a name="enable-cloud-authentication"></a>啟用雲身份驗證

具有本地活動目錄的組織應使用 Azure AD 連接將其目錄擴展到 Azure AD 並配置適當的身份驗證方法。 為組織[選擇正確的身份驗證方法是](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn)將應用遷移到雲的第一步。 它是一個關鍵元件，因為它控制對所有雲資料和資源的訪問。

為 Azure AD 中的本地目錄物件啟用雲身份驗證的最簡單和推薦的方法是啟用[密碼雜湊同步](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization)（PHS）。 或者，某些組織可能會考慮啟用[直通身份驗證](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start)（PTA）。

無論您選擇小靈通還是 PTA，不要忘記啟用[無縫單一登入](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)功能，以允許使用者在使用公司網路上的 Windows 7 和 8 設備時，無需在應用中不斷輸入使用者名和密碼即可訪問雲應用。 如果沒有單一登入，使用者必須記住特定于應用程式的密碼並登錄到每個應用程式。 同樣，IT 人員需要為每個應用程式（如 Office 365、Box 和 Salesforce）創建和更新使用者帳戶。 使用者需要記住其密碼，以及花時間登錄到每個應用程式。 為整個企業提供標準化的單一登入機制對於最佳使用者體驗、降低風險、報告能力和治理至關重要。

對於已經使用 AD FS 或其他本地身份檢查器提供者的組織，遷移到 Azure AD 作為標識提供程式可以減少複雜性並提高可用性。 除非您有使用聯合的特定用例，否則我們建議您從識別身分同盟遷移到小靈通和無縫 SSO 或 PTA 和無縫 SSO，以享受減少本地佔用空間的優勢以及雲提供的靈活性改進的使用者體驗。 有關詳細資訊，請參閱[Azure 活動目錄從聯合遷移到密碼雜湊同步](https://docs.microsoft.com/azure/active-directory/hybrid/plan-migrate-adfs-password-hash-sync)。

### <a name="enable-automatic-deprovisioning-of-accounts"></a>啟用自動取消預配帳戶

為應用程式啟用自動預配和取消預配是管理跨多個系統標識生命週期的最佳策略。 Azure AD 支援[自動、基於策略的預配和取消預配](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal)使用者帳戶，以各種流行的 SaaS 應用程式（如 ServiceNow 和 Salesforce）以及實現[SCIM 2.0 協定](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)的其他應用程式進行預配和取消預配。 與需要自訂代碼或手動上載 CSV 檔的傳統預配解決方案不同，預配服務託管在雲中，並具有可以使用 Azure 門戶進行設置和管理的預集成連接器。 自動取消預配的一個關鍵好處是，它有助於保護組織的安全，當使用者在離開組織時立即從關鍵的 SaaS 應用中刪除他們的身份。

如需深入了解自動化使用者佈建，請參閱 [自動化使用 Azure Active Directory 對於 SaaS 應用程式的使用者佈建和解除佈建](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。

## <a name="step-3---empower-your-users-securely"></a>第 3 步 - 安全地為使用者提供支援

在當今的數位工作場所中，在安全性與生產力之間取得平衡非常重要。 但是，最終使用者通常會拒絕採取安全措施，降低工作效率和對雲應用的訪問。 為了説明解決這個問題，Azure AD 提供了自助服務功能，使使用者能夠保持工作效率，同時最大限度地減少管理開銷。

本節列出了通過增強使用者能力來消除組織摩擦的建議，同時保持警惕。

### <a name="enable-self-service-password-reset-for-all-users"></a>為所有使用者啟用自助服務密碼重設

Azure 的[自助服務密碼重設](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)（SSPR） 為 IT 管理員提供了一種簡單的方法，允許使用者重置和解鎖其密碼或帳戶，而無需管理員干預。 系統包含詳細的報告，可追蹤使用者何時存取系統與通知來警示您誤用或濫用。

根據預設，Azure AD 可以在執行密碼重設時解除鎖定帳戶。 但是，當您[在本地](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#on-premises-integration)啟用 Azure AD Connect 集成時，您還可以選擇分隔這兩個操作，這使使用者能夠解鎖其帳戶，而無需重置密碼。

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>確保所有使用者都註冊了 MFA 和 SSPR

Azure 提供可供您和您的組織使用的報表，以確保使用者註冊 MFA 和 SSPR。 尚未註冊的使用者可能需要接受有關該過程的教育。

MFA[登錄報告](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting)包含有關 MFA 使用方式的資訊，並讓您深入瞭解 MFA 在組織中的工作方式。 訪問 Azure AD 的登錄活動（以及審核和風險檢測）對於故障排除、使用方式分析和取證調查至關重要。

同樣，[自助服務密碼管理報告](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting)可用於確定誰已註冊（或尚未）註冊 SSPR。

### <a name="self-service-app-management"></a>自助服務應用管理

在使用者可以從訪問面板中自行發現應用程式之前，您需要啟用[自助服務應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-manage-self-service-access)對您希望允許使用者自行發現和請求訪問的任何應用程式的存取權限。 自助服務應用程式訪問是允許使用者自行發現應用程式以及允許業務組批准對這些應用程式的訪問的好方法。 您可以允許業務組直接從訪問面板管理分配給這些使用者的憑據，以便使用[密碼單簽名應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery#configure-password-sso-for-an-azure-ad-gallery-app)。

### <a name="self-service-group-management"></a>自助式群組管理

在使用組時，最好映射將使用者分配給應用程式，因為它們具有極大的靈活性和大規模管理能力：

* 使用動態組成員身份基於屬性
* 委派給應用擁有者

Azure AD 可讓您使用安全性群組和 Office 365 群組來管理資源的存取權。 這些組可以由組擁有者管理，該擁有者可以批准或拒絕成員身份請求和委派組成員身份控制。 此功能稱為[自助服務組管理](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management)，它允許未分配管理角色的組擁有者創建和管理組，而無需依賴管理員來處理其請求，從而節省時間。

## <a name="step-4---operationalize-your-insights"></a>第 4 步 - 操作您的見解

審核和記錄與安全相關的事件和相關警報是高效策略的重要組成部分，可確保使用者保持高效工作，並確保您的組織安全。 安全性記錄檔和報告可以説明回答以下問題：

* 您是否在使用您支付的報酬？
* 我的租戶中是否發生了可疑或惡意事件？
* 誰在安全事件中受到影響？

安全性記錄和報告會提供可疑活動的電子記錄，並協助您偵測可能指出從外部嘗試或成功滲透網路以及內部攻擊的模式。 您可以使用審核來監視使用者活動、記錄法規遵從性、進行取證分析等。 警示會提供安全性事件通知。

### <a name="assign-least-privileged-admin-roles-for-operations"></a>為操作分配最低特權的管理員角色

在考慮操作方法時，需要考慮幾個級別的管理。 第一個級別將管理負擔置於全域管理員身上。 始終使用全域管理員角色，可能適用于較小的公司。 但對於具有説明台人員和負責特定任務的管理員的大型組織，分配全域管理員的角色可能會帶來安全風險，因為它使這些人員能夠管理超出和超越的任務他們應該能做什麼。

在這種情況下，應考慮下一級別的管理。 使用 Azure AD，可以將最終使用者指定為"受限管理員"，他們可以以較低許可權的角色管理工作。 例如，您可以為説明台人員分配[安全讀取器](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#security-reader)角色，以為他們提供通過唯讀訪問管理安全相關功能的能力。 或者，將[身份驗證管理員](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#authentication-administrator)角色指派給個人以授予他們重置非密碼憑據或讀取和配置 Azure 服務運行狀況的能力可能很有意義。

要瞭解詳細資訊，請參閱[Azure 活動目錄中的管理員角色許可權](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)。

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>使用 Azure AD 連接運行狀況監視混合元件（Azure AD 連接同步、AD FS）

Azure AD 連接和 AD FS 是關鍵元件，可能會中斷生命週期管理和身份驗證，最終導致中斷。 因此，應部署 Azure AD 連接運行狀況，以便監視和報告這些元件。

要瞭解更多資訊，請轉到[使用 Azure AD 連接運行狀況讀取監視器 AD FS。](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>使用 Azure 監視器收集用於分析的資料日誌

[Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview)是所有 Azure AD 日誌的統一監視門戶，可提供深入的見解、高級分析和智慧型機器學習。 使用 Azure 監視器，您可以在門戶中並通過 API 使用指標和日誌，以更好地瞭解資源的狀態和性能。 它在門戶內實現單一玻璃體驗，同時通過支援傳統協力廠商 SIEM 系統的資料匯出選項實現廣泛的產品集成。 Azure 監視器還使您能夠配置警報規則以獲得通知，或對影響資源的問題執行自動操作。

![Azure 監視器](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>為您的領導和日常工作創建自訂儀表板

沒有 SIEM 解決方案的組織可以下載 Azure AD[的 Power BI 內容包](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-power-bi-content-pack)。 Power BI 內容包包含預先構建的報告，可説明您瞭解使用者如何採用和使用 Azure AD 功能，從而使您能夠深入瞭解目錄中的所有活動。 您還可以創建自己的[自訂儀表板](https://docs.microsoft.com/power-bi/service-dashboards)，並與領導團隊共用以報告日常活動。 儀表板是監視您的業務並一目了然地查看所有最重要的指標的好方法。 儀表板上的視覺效果可能來自一或多個基礎資料集，以及來自一或多份基礎報表。 儀表板將內部部署和雲端的資料結合在一起，提供不受資料位置限制的合併檢視。

![電源 BI 自訂儀表板](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>瞭解您的支援呼叫驅動程式

實現本文中概述的混合標識解決方案時，最終應注意到支援呼叫的減少。 通過實施 Azure 的自助服務密碼重設，緩解忘記密碼和帳戶鎖定等常見問題，同時啟用自助服務應用程式訪問允許使用者自行發現應用程式並請求訪問應用程式，而無需依賴您的 IT 員工。

如果您未觀察到支援呼叫的減少，我們建議您分析支援呼叫驅動程式，以嘗試確認 SSPR 或自助服務應用程式訪問是否配置正確，或者是否有任何可以系統地出現任何其他新問題解決。

*"在我們的數位化轉型之旅中，我們需要一個可靠的身份和訪問管理供應商，以促進我們、合作夥伴和雲服務提供者之間的無縫安全集成，從而實現有效的生態系統;Azure AD 是提供我們所需的功能和可見度的最佳選項，使我們能夠檢測和回應風險。* --- [亞贊·阿爾馬斯裡，阿拉梅斯全球資訊安全總監](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>監控應用使用方式，推動洞察

除了發現影子 IT 之外，使用[Microsoft 雲應用安全性](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)監視整個組織的應用使用方式還可以説明您組織充分利用雲應用程式的承諾。 通過提高對活動的可見度，並增強對雲應用程式關鍵資料的保護，它可以説明您控制資產。 使用 MCAS 監視組織中的應用使用方式可以説明您回答以下問題：

* 員工使用哪些未經批准的應用來存儲資料？
* 敏感性資料存儲在雲中的何時何地？
* 誰在雲中訪問敏感性資料？

*"借助雲應用安全性，我們可以快速發現異常並採取行動。* --- [埃裡克·勒彭斯克，埃森哲資訊安全高級經理](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>總結

實現混合標識解決方案有許多方面，但此四步檢查表將説明您快速完成身份基礎結構，使使用者能夠提高工作效率和安全性。

* 輕鬆連接到應用
* 自動為每個使用者建立一個標識
* 為您的使用者安全提供支援
* 操作您的見解

我們希望本文檔是為您的組織建立牢固的身份基礎的有用路線圖。

## <a name="identity-checklist"></a>身份清單

我們建議您在開始組織中更穩固的身份基礎時列印以下清單供參考。

### <a name="today"></a>Today

|完成了嗎？|Item|
|:-|:-|
||針對組的試用自助服務密碼重設 （SSPR）|
||使用 Azure AD 連接運行狀況監視混合元件|
||為操作分配最低特權的管理員角色|
||使用 Microsoft 雲應用安全性發現影子 IT|
||使用 Azure 監視器收集資料日誌進行分析|

### <a name="next-two-weeks"></a>接下來的兩周

|完成了嗎？|Item|
|:-|:-|
||使應用可供使用者使用|
||為選擇的 SaaS 應用進行試用 Azure AD 預配|
||為 Azure AD 連接設置暫存伺服器，並使其保持最新|
||開始將應用從 ADFS 遷移到 Azure AD|
||為您的領導和日常工作創建自訂儀表板|

### <a name="next-month"></a>下個月

|完成了嗎？|Item|
|:-|:-|
||監控應用使用方式，推動洞察|
||試用安全遠端存取應用|
||確保所有使用者都註冊了 MFA 和 SSPR|
||啟用雲身份驗證|

### <a name="next-three-months"></a>接下來的三個月

|完成了嗎？|Item|
|:-|:-|
||啟用自助服務應用管理|
||啟用自助服務組管理|
||監控應用使用方式，推動洞察|
||瞭解您的支援呼叫驅動程式|

## <a name="next-steps"></a>後續步驟

瞭解如何使用 Azure 活動目錄的功能和以下五個步驟檢查表（[保護身份基礎結構的五個步驟](https://aka.ms/securitysteps)）來增強安全狀態。

瞭解 Azure AD 中的標識功能如何通過提供解決方案和功能，使組織能夠快速採用其標識管理並將其從傳統的本地系統移動到 Azure AD - [Azure AD 如何為本地工作負載提供雲治理管理](https://aka.ms/cloudgoverned)，從而説明您加快向雲治理管理的過渡。
