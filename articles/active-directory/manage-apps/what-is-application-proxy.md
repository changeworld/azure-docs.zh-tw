---
title: 使用 Azure AD 應用程式 Proxy 來發佈內部部署應用程式
description: 了解為何要使用應用程式 Proxy 來將內部部署 Web 應用程式對外發佈給遠端使用者。 了解應用程式 Proxy 的架構、連接器、驗證方法和安全性優點。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/31/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 68ccf3518a7011c0c39003d8e447a4e1d233fd91
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88164575"
---
# <a name="using-azure-ad-application-proxy-to-publish-on-premises-apps-for-remote-users"></a>使用 Azure AD 應用程式 Proxy 來為遠端使用者發佈內部部署應用程式

Azure Active Directory (Azure AD) 提供了許多功能來保護雲端和內部部署環境中的使用者、應用程式和資料。 特別的是，想要將內部部署 Web 應用程式對外發佈的 IT 專業人員也可以實作Azure AD 應用程式 Proxy 功能。 然後，需要存取內部應用程式的遠端使用者便可以透過安全的方式存取這些應用程式。

在現代職場中，能夠從網路外部安全地存取內部應用程式已變得更加重要。 在 BYOD (攜帶您自己的裝置) 和行動裝置等案例中，IT 專業人員面臨了要符合下列兩個目標的挑戰：

* 讓終端使用者隨時隨地都具有生產力
* 隨時保護公司資產

許多組織認為，當資源位於其公司網路界限內時，便可以由其掌控並獲得保護。 但在現今的數位職場中，該界限已擴大，納入了受控行動裝置和雲端資源與服務。 現在，對於為了保護使用者儲存在其裝置和應用程式中的身分識別和資料所衍生的複雜性，您需要加以管理。

或許您已使用 Azure AD 來管理雲端中的使用者，這些使用者需要存取 Office 365 和其他 SaaS 應用程式，以及裝載在內部部署環境的 Web 應用程式。 如果您已有 Azure AD，便可以其作為控制平面，來允許使用者對內部部署應用程式進行順暢且安全的存取。 或者，您可能還在思考是否要移至雲端。 如果是這樣，您可以藉由實作應用程式 Proxy 並邁出第一步來建置強大的身分識別基礎，以開始您的雲端旅程。

下列清單雖不完整，但會說明一些您可以藉由在混合式共存案例中實作應用程式 Proxy 來實現的事項：

* 以簡化的方式將內部部署 Web 應用程式對外發佈，而不需要 DMZ
* 在雲端和內部部署環境中跨裝置、資源和應用程式支援單一登入 (SSO)
* 針對雲端和內部部署環境中的應用程式支援多重要素驗證
* 透過 Microsoft Cloud 的安全性來快速利用雲端功能
* 集中管理使用者帳戶
* 集中控制身分識別和安全性
* 根據群組成員資格來自動新增或移除使用者的應用程式存取權

本文說明 Azure AD 和應用程式 Proxy 如何對遠端使用者提供單一登入 (SSO) 體驗。 使用者可以安全地連線到內部部署應用程式，而不需要透過 VPN 或雙重主伺服器和防火牆規則。 本文可協助您了解應用程式 Proxy 如何將雲端的功能和安全性優點帶入到內部部署 Web 應用程式。 文中也會說明可行的架構和拓撲。

## <a name="remote-access-in-the-past"></a>以往的遠端存取

以前，在方便遠端使用者存取的同時，用於保護內部資源免於遭受攻擊者入侵的控制平面，全都放在 DMZ (即周邊網路) 內。 不過，DMZ 內所部署供外部用戶端用來存取公司資源的 VPN 和反向 Proxy 解決方案，並不適合雲端世界。 這些解決方案一般會遇到下列缺點：

* 硬體成本
* 維護安全性 (修補、監視連接埠等)
* 在邊緣驗證使用者
* 向周邊網路內的 Web 伺服器驗證使用者
* 藉由散發和設定 VPN 用戶端軟體來讓遠端使用者保有 VPN 存取權。 此外，還會在 DMZ 中保有已加入網域的伺服器，因而容易遭受外部攻擊。

在現今的雲端優先世界中，Azure AD 最適合用來控制可以進入網路的人員和內容。 Azure AD 應用程式 Proxy 整合了新式的驗證和雲端式技術，例如 SaaS 應用程式和識別提供者。 此一整合可讓使用者從任何地方存取應用程式。 應用程式 Proxy 不只更適合現今的數位職場，還比 VPN 和反向 Proxy 解決方案更安全且更容易實作。 遠端使用者可以使用和 O365 以及其他與 Azure AD 整合之 SaaS 應用程式相同的存取方式，來存取內部部署應用程式。 您不需要變更或更新應用程式，即可使用應用程式 Proxy。 此外，應用程式 Proxy 不需要您穿過防火牆開啟輸入連線。 使用應用程式 Proxy 時，只要完成設定就不用再加以理會。

## <a name="the-future-of-remote-access"></a>遠端存取的未來

在現今的數位職場中，使用者會在任何地方使用多個裝置和應用程式來工作。 唯一不變的就是使用者身分識別。 因此，現今要打造安全網路環境的第一步，就是使用 [Azure AD 的身分識別管理](https://docs.microsoft.com/azure/security/security-identity-management-overview)功能來作為安全性控制平面。 使用身分識別作為控制平面的模型一般會由下列元件組成：

* 識別提供者，用來追蹤使用者和使用者相關資訊。
* 裝置目錄，用來維護可存取公司資源的裝置清單。 此目錄包含對應的裝置資訊 (例如，裝置類型、完整性等)。
* 原則評估服務，用來判斷使用者和裝置是否符合安全性管理員所規定的原則。
* 授與或拒絕對組織資源存取的能力。

使用應用程式 Proxy 時，Azure AD 會追蹤需要存取內部部署環境和雲端中所發佈 Web 應用程式的使用者。 其可讓您集中管理這些應用程式。 雖非必要，但建議您也啟用 Azure AD 條件式存取。 藉由定義使用者要如何進行驗證和獲得存取權的條件，您可以進一步確保只有適當的人員能存取應用程式。

**注意：** 請務必了解，Azure AD 應用程式 Proxy 的目的是要用來為需要存取內部資源的漫遊 (或遠端) 使用者取代 VPN 或反向 Proxy。 其適用對象並非是公司網路上的內部使用者。 沒必要卻使用應用程式 Proxy 的內部使用者會造成非預期且不想要的效能問題。

![Azure Active Directory 和您的所有應用程式](media/what-is-application-proxy/azure-ad-and-all-your-apps.png)

### <a name="an-overview-of-how-app-proxy-works"></a>應用程式 Proxy 運作方式的概觀

應用程式 Proxy 是在 Azure 入口網站中設定的 Azure AD 服務。 其可讓您在 Azure Cloud 中發佈外部的公用 HTTP/HTTPS URL 端點，這些端點會連線到組織中的內部應用程式伺服器 URL。 這些內部部署 Web 應用程式可與 Azure AD 整合以支援單一登入。 然後，終端使用者就可以使用和用來存取 Office 365 及其他 SaaS 應用程式的相同方式，來存取內部部署 Web 應用程式。

這項功能的元件包括應用程式 Proxy 服務 (在雲端中執行)、應用程式 Proxy 連接器 (在內部部署伺服器上執行的輕量型代理程式) 和 Azure AD (作為識別提供者)。 這三個元件一起運作，便可為使用者提供用來存取內部部署 Web 應用程式的單一登入體驗。

外部使用者登入之後，可藉由從其桌面或 iOS/MAC 裝置使用熟悉的 URL 或 [MyApps 存取面板](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)來存取內部部署 Web 應用程式。 例如，應用程式 Proxy 可以為遠端桌面、SharePoint 網站、Tableau、Qlik、Outlook 網頁版和企業營運 (LOB) 應用程式提供遠端存取和單一登入。

![Azure AD 應用程式 Proxy 的架構](media/what-is-application-proxy/azure-ad-application-proxy-architecture.png)

### <a name="authentication"></a>驗證

有數種方式可用來為應用程式設定單一登入，要選取什麼方式則取決於應用程式所使用的驗證。 應用程式 Proxy 支援下列類型的應用程式：

* Web 應用程式
* 您想要公開給不同裝置上豐富應用程式的 Web API
* 裝載在遠端桌面閘道之後的應用程式
* 與 [Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/v2-overview) 整合的豐富型用戶端應用程式

應用程式 Proxy 適用於使用下列原生驗證通訊協定的應用程式：

* **[整合式 Windows 驗證 (IWA)](application-proxy-configure-single-sign-on-with-kcd.md)。** 針對 IWA，應用程式 Proxy 連接器會使用 Kerberos 限制委派 (KCD) 來向 Kerberos 應用程式驗證使用者。

應用程式 Proxy 也支援搭配第三方整合或在特定的設定案例中使用下列驗證通訊協定：

* [**標頭型驗證**](application-proxy-configure-single-sign-on-with-ping-access.md)。 此登入方法會使用名為 PingAccess 的第三方驗證服務，使用時機則在應用程式使用標頭進行驗證時。 在此案例中，驗證會由 PingAccess 處理。
* [**表單或密碼型驗證**](application-proxy-configure-single-sign-on-password-vaulting.md)。 若使用此驗證方法，使用者在第一次存取應用程式時，要以使用者名稱和密碼登入應用程式。 第一次登入之後，Azure AD 就會向應用程式提供使用者名稱和密碼。 在此案例中，驗證會由 Azure AD 處理。
* [**SAML 驗證**](application-proxy-configure-single-sign-on-on-premises-apps.md)。 使用 SAML 2.0 或 WS-同盟通訊協定的應用程式都支援 SAML 型單一登入。 使用 SAML 單一登入時，Azure AD 會使用使用者的 Azure AD 帳戶向應用程式驗證。

如需有關支援的方法的詳細資訊，請參閱[選擇單一登入方法](sso-options.md#choosing-a-single-sign-on-method)。

### <a name="security-benefits"></a>安全性優點

應用程式 Proxy 和 Azure AD 所提供的遠端存取解決方案支援幾項安全性優點可供客戶利用，包括：

* **已驗證的存取**。 應用程式 Proxy 最適合用來發佈具有[預先驗證](application-proxy-security.md#authenticated-access)的應用程式，以確保只有已驗證的連線能夠連上您的網路。 對於使用預先驗證發佈的應用程式，不允許沒有有效權杖的流量通過應用程式 Proxy 服務而到達您的內部部署環境。 預先驗證 (就其本質) 會封鎖大量目標式攻擊，因為只有已驗證的身分識別才能存取後端應用程式。
* **條件式存取**。 在建立通往您網路的連線之前，可以先套用更豐富的原則控制。 使用條件式存取，就可以對允許連上後端應用程式的流量來定義限制。 您可以位置、驗證強度和使用者風險狀況作為基礎，來建立限制登入的原則。 條件式存取隨著發展新增了更多的控制以提供額外的安全性，例如與 Microsoft Cloud App Security (MCAS) 整合。 MCAS 整合可讓您藉由利用條件式存取來根據條件式存取原則即時監視並控制工作階段，而為內部部署應用程式設定[即時監視](application-proxy-integrate-with-microsoft-cloud-application-security.md)。
* **流量終止**。 在向後端伺服器重新建立工作階段時，雲端中的應用程式 Proxy 服務會終止所有流向後端應用程式的流量。 此連線策略表示您的後端伺服器不會對直接 HTTP 流量公開。 由於您的防火牆不會遭受攻擊，因此這些伺服器能更好地防禦目標型 DoS (拒絕服務) 攻擊。
* **所有存取都是輸出**。 應用程式 Proxy 連接器只會透過連接埠 80 和 443，來使用目的地為雲端中應用程式 Proxy 服務的輸出連線。 由於沒有輸入連線，因此不需要為 DMZ 中的連入連線或元件開啟防火牆連接埠。 所有連線皆為輸出且透過安全通道進行。
* **以安全性分析和 Machine Learning (ML) 為基礎的情報**。 因為應用程式 Proxy 是 Azure Active Directory 的一部分，所以可以利用 [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview) (需要 [Premium P2 授權](https://azure.microsoft.com/pricing/details/active-directory/))。 Azure AD Identity Protection 會結合機器學習安全情報與來自 Microsoft [數位犯罪防治中心](https://news.microsoft.com/stories/cybercrime/index.html)和 [Microsoft Security Response Center](https://www.microsoft.com/msrc) 的資料摘要，來主動識別遭到入侵的帳戶。 Identity Protection 可即時防範高風險的登入。其會考量各種因素 (如來自受感染裝置的存取、透過匿名網路的存取，或來自非典型與虛假位置的存取)，以增加工作階段的風險概況。 此風險概況可用於提供即時保護。 這些報告和事件中有許多已可透過 API 與 SIEM 系統整合。

* **遠端存取即服務**。 您不必煩惱要如何維護及修補內部部署伺服器以實現遠端存取。 應用程式 Proxy 是 Microsoft 自有的網際網路級別服務，因此您永遠會獲得最新的安全性修補程式和升級。 未更新的軟體仍需負責處理大量攻擊。 根據美國國土安全部的調查，多達 [85% 的目標型攻擊是可以預防的](https://www.us-cert.gov/ncas/alerts/TA15-119A)。 使用此服務模型，您就不必再艱辛管理邊緣伺服器並視需要加以修補。

* **Intune 整合**。 使用 Intune 時，公司流量會與個人流量分開進行路由。 應用程式 Proxy 可確保公司流量會經過驗證。 [應用程式 Proxy 與 Intune Managed Browser](https://docs.microsoft.com/intune/app-configuration-managed-browser#how-to-configure-application-proxy-settings-for-protected-browsers) 功能也可一起使用，而讓遠端使用者能夠安全地從 iOS 和 Android 裝置存取內部網站。

### <a name="roadmap-to-the-cloud"></a>雲端藍圖

實作應用程式 Proxy 的另一個主要優點是可以將 Azure AD 延伸到內部部署環境。 事實上，實作應用程式 Proxy 是讓組織和應用程式移至雲端的關鍵步驟。 藉由移至雲端並遠離內部部署驗證，您可以減少內部部署的磁碟使用量，並使用 Azure AD 的身分識別管理功能來作為控制平面。 不用對現有應用程式進行多少更新，您就可以存取單一登入、多重要素驗證和集中管理等雲端功能。 在應用程式 Proxy 內安裝必要元件是很簡單的程序，可用於建立遠端存取架構。 而且藉由移至雲端，您也可以存取最新的 Azure AD 功能和更新，例如高可用性和災害復原。

若要深入了解如何將應用程式遷移至 Azure AD，請參閱[將應用程式遷移至 Azure Active Directory](https://aka.ms/migrateapps/whitepaper) 白皮書。

## <a name="architecture"></a>架構

下圖說明在一般情況下，Azure AD 驗證服務和應用程式 Proxy 會如何搭配運作，來為終端使用者提供內部部署應用程式的單一登入。

![Azure AD 應用程式 Proxy 的驗證流程](media/what-is-application-proxy/azure-ad-application-proxy-authentication-flow.png)

1. 使用者透過端點存取應用程式之後，系統會將使用者重新導向至 Azure AD 登入頁面。 如果您已設定條件式存取原則，此時便會檢查具體條件以確保您符合組織的安全性需求。
2. 成功登入之後，Azure AD 會向使用者的用戶端裝置傳送權杖。
3. 用戶端會將權杖傳送至應用程式 Proxy 服務，該服務會取出權杖的使用者主體名稱 (UPN) 和安全性主體名稱 (SPN)。
4. 應用程式 Proxy 會轉送要求 (要求會由應用程式 Proxy [連接器](application-proxy-connectors.md)接收)。
5. 連接器會代表使用者執行任何額外的必要驗證 (選擇性功能，視驗證方法而定**)、要求應用程式伺服器的內部端點，並將要求傳送至內部部署應用程式。
6. 來自該應用程式伺服器的回應會透過連接器傳送給應用程式 Proxy 服務。
7. 回應會從應用程式 Proxy 服務傳送給使用者。

|**元件**|**說明**|
|:-|:-|
|端點|端點可以是 URL 或 [終端使用者入口網站](end-user-experiences.md)。 使用者可以藉由存取外部 URL，來連線網路外部的應用程式。 您網路內的使用者可以透過 URL 或使用者入口網站存取應用程式。 使用者存取這些端點的其中一個時，會在 Azure AD 中進行驗證，然後透過連接器路由至內部部署應用程式。|
|Azure AD|Azure AD 使用儲存在雲端中的租用戶目錄執行驗證。|
|應用程式 Proxy 服務|此應用程式 Proxy 服務作為 Azure AD 的一部分在雲端中執行。 它會將登入權杖從使用者傳遞至應用程式 Proxy 連接器時。 應用程式 Proxy 在請求上轉送任何可存取的標頭，並根據其通訊協定將標頭設定為用戶端 IP 位址。 如果對 Proxy 的連入要求中已經有該標頭，則將用戶端 IP 位址加入到以逗號分隔清單的結尾，該用戶端 IP 位址是標頭的值。|
|應用程式 Proxy 連接器|連接器是位於網路內部 Windows 伺服器上執行的輕量型代理程式。 連接器管理雲端中的應用程式 Proxy 服務與內部部署應用程式之間的通訊。 連接器僅使用輸出連線，因此您不需要開啟任何輸入連接埠，或在 DMZ 中放置任何物件。 連接器是無狀態的，且在必要時會從雲端提取資訊。 如需連接器的詳細資訊，例如如何負載平衡和驗證，請參閱 [了解 Azure AD 應用程式 Proxy 連接器](application-proxy-connectors.md)。|
|Active Directory (AD)|Active Directory 在內部部署執行以對網域帳戶執行驗證。 設定單一登入之後，連接器將與 AD 通訊以執行所需的任何額外的驗證。|
|內部部署應用程式|最後，使用者就能夠存取內部部署應用程式。|

Azure AD 應用程式 Proxy 是由雲端式應用程式 Proxy 服務和內部部署連接器所組成的。 連接器會接聽來自應用程式 Proxy 服務的要求，並處理對內部應用程式的連線。 請務必請注意，所有通訊都會透過 TLS 發生，且一律源自於至應用程式 Proxy 服務的連接器。 也就是說，通訊只會對外輸出。 連接器會使用用戶端憑證來驗證所有呼叫的應用程式 Proxy 服務。 連線安全性的唯一例外是可供建立用戶端憑證的初始設定步驟。 如需詳細資訊，請參閱應用程式 Proxy [運作原理](application-proxy-security.md#under-the-hood)。

### <a name="application-proxy-connectors"></a>應用程式 Proxy 連接器

[應用程式 Proxy 連接器](application-proxy-connectors.md)是部署於內部部署環境的輕量型代理程式，可推動雲端中應用程式 Proxy 服務的輸出連線。 連接器必須安裝在可存取後端應用程式的 Windows 伺服器上。 使用者會連線至應用程式 Proxy 雲端服務，此服務會透過連接器將使用者的流量路由傳送至應用程式，如下圖所示。

![Azure AD 應用程式 Proxy 的網路連線](media/what-is-application-proxy/azure-ad-application-proxy-network-connections.png)

連接器與應用程式 Proxy 服務之間的設定和註冊會如下所示來完成：

1. IT 系統管理員會對輸出流量開啟連接埠 80 和 443，並允許連接器、應用程式 Proxy 服務和 Azure AD 對數個 URL 進行所需的存取。
2. 系統管理員會登入 Azure 入口網站，並執行可執行檔以在內部部署 Windows 伺服器上安裝連接器。
3. 連接器會開始「接聽」應用程式 Proxy 服務。
4. 系統管理員會對 Azure AD 新增內部部署應用程式，並進行相關設定，例如使用者為了連線至其應用程式所需要的 URL。

如需詳細資訊，請參閱[規劃 Azure AD 應用程式 Proxy 部署](application-proxy-deployment-plan.md)。

建議您一律部署多個連接器以提供備援和縮放能力。 連接器搭配服務來使用時，會負責進行所有高可用性工作，並可加以動態新增或移除。 每當有新的要求抵達時，系統便會將其路由傳送至其中一個可用的連接器。 當連接器執行時，它在連接到服務時會保持作用中。 如果連接器暫時無法使用，則不會回應此流量。 未使用的連接器會標記為非作用中，且將在未作用 10 天之後移除。

連接器也會輪詢伺服器，以尋找是否有較新版的連接器。 雖然您可以手動更新，但只要應用程式 Proxy 連接器更新程式服務正在執行，您的連接器便會自動更新。 對於具有多個連接器的租用戶，自動更新會一次以每個群組中的一個連接器為目標，以免您的環境發生停機。

**注意**：您可以藉由訂閱 RSS 摘要，來監視要在發行了更新時收到通知的應用程式 Proxy [版本歷程記錄頁面](application-proxy-release-version-history.md)。

每個應用程式 Proxy 連接器都會指派給[連接器群組](application-proxy-connector-groups.md)。 位於相同連接器群組內的連接器會作為一個整體來提供高可用性及負載平衡功能。 您可以建立新的群組、在 Azure 入口網站中將連接器指派給這些新群組，然後指派特定連接器來服務特定應用程式。 建議您在每個連接器群組內至少放入兩個連接器，以提供高可用性。

當您需要支援下列案例時，連接器群組會很有用：

* 地理位置應用程式發佈
* 應用程式分割/隔離
* 發佈會在雲端或內部部署環境中執行的 Web 應用程式

如需如何選擇要在哪裡安裝連接器並將網路最佳化的詳細資訊，請參閱[使用 Azure Active Directory 應用程式 Proxy 時的網路拓撲考量](application-proxy-network-topology.md)。

## <a name="other-use-cases"></a>其他使用案例

到目前為止，我們的焦點一直放在使用應用程式 Proxy 來對外發佈內部部署應用程式，同時讓所有雲端和內部部署應用程式實現單一登入。 不過，另外還有一些值得一提的應用程式 Proxy 使用案例。 其中包括：

* **安全發佈 REST API**。 當您讓商務邏輯或 API 在內部部署環境中執行，或裝載於雲端中的虛擬機器時，應用程式 Proxy 會提供用來進行 API 存取的公用端點。 API 端點存取可讓您控制驗證和授權，而不需要有連入連接埠。 其可透過 Azure AD Premium 功能 (例如，使用 Intune 的桌面、iOS、MAC 和 Android 裝置，會有多重要素驗證和裝置型條件式存取) 來提供額外的安全性。 若要深入了解，請參閱[如何讓原生用戶端應用程式能與 Proxy 應用程式互動](application-proxy-configure-native-client-application.md)以及[使用 OAuth 2.0 搭配 Azure Active Directory 與 API 管理來保護 API](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)。
* **遠端桌面服務** **(RDS)** 。 需要使用開啟的輸入連線才能進行標準 RDS 部署。 不過，[使用應用程式 Proxy 的 RDS 部署](application-proxy-integrate-with-remote-desktop-services.md)具有來自連接器服務執行所在伺服器的永久輸出連線。 如此一來，您就可以透過遠端桌面服務發佈內部部署應用程式，藉此來提供更多應用程式給終端使用者。 您也可以向 RDS 提供一組有限的雙步驟驗證和條件式存取控制，來減少部署的受攻擊面。
* **發佈使用 Websocket 來連線的應用程式**。 [Qlik Sense](application-proxy-qlik.md) 支援目前為公開預覽狀態，未來會擴展至其他應用程式。
* **讓原生用戶端應用程式與 Proxy 應用程式互動**。 您可以使用 Azure AD 應用程式 Proxy 來發佈 Web 應用程式，但其也可用來發佈使用 Azure AD 驗證程式庫 (ADAL) 所設定的[原生用戶端應用程式](application-proxy-configure-native-client-application.md)。 原生用戶端應用程式與 Web 應用程式不同，因為這種應用程式會安裝在裝置上，而 Web 應用程式則是透過瀏覽器存取。

## <a name="conclusion"></a>結論

我們的工作方式和使用的工具變化得很快。 隨著越來越多的員工攜帶自己的裝置來工作並普遍使用軟體即服務 (SaaS) 應用程式，組織用來管理及保護其資料的方式也必須跟著演化。 公司無法再關起門來獨自營運。 比起以往，資料會移動到更多位置，蹤跡橫跨內部部署和雲端環境。 這樣的發展不僅幫助使用者提高生產力和共同作業能力，同時也讓敏感性資料變得更難以保護。

無論您目前正使用 Azure AD 來管理混合式共存案例中的使用者，還是想要開始您的雲端旅程，實作 Azure AD 應用程式 Proxy 都可藉由提供遠端存取服務來協助您減少內部部署使用量的大小。

組織應該立即開始利用應用程式 Proxy 來利用下列優勢：

* 可對外發佈內部部署應用程式，而不必為了維護傳統 VPN 或其他內部部署 Web 發佈解決方案和 DMZ 方法而衍生相關額外負荷
* 對所有應用程式進行單一登入，不論是 Office 365 或其他 SaaS 應用程式，內部部署應用程式也包括在內
* 可獲得雲端級別的安全性，Azure AD 會運用 Office 365 遙測來防止未經授權進行存取
* Intune 整合，可確保公司流量會經過驗證
* 集中管理使用者帳戶
* 自動更新，以確保您有最新的安全性修補程式
* 獲得新發行的功能；最新功能是支援 SAML 單一登入和更加細微的應用程式 Cookie 管理

## <a name="next-steps"></a>後續步驟

* 如需關於規劃、操作和管理 Azure AD 應用程式 Proxy 的資訊，請參閱[規劃 Azure AD 應用程式 Proxy 部署](application-proxy-deployment-plan.md)。
* 若要安排現場示範或取得 90 天免費試用以便進行評估，請參閱[開始使用 Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial)。
