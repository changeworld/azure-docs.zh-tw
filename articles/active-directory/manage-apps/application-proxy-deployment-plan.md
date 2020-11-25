---
title: 規劃 Azure Active Directory 應用程式 Proxy 部署
description: 規劃組織內應用程式 proxy 部署的端對端指南
services: active-directory
documentationcenter: azure
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: kenwith
ms.openlocfilehash: 9736e1b71f5e129989aba9a045581bae8a1c5f93
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997540"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>規劃 Azure AD 應用程式 Proxy 部署

Azure Active Directory (Azure AD) 應用程式 Proxy 是適用于內部部署應用程式的安全且符合成本效益的遠端存取解決方案。 它會針對「雲端優先」組織提供立即的轉換路徑，以管理尚無法使用新式通訊協定的舊版內部部署應用程式的存取權。 如需其他簡介資訊，請參閱 [什麼是應用程式 Proxy](./application-proxy.md)。

建議應用程式 Proxy 讓遠端使用者存取內部資源。 應用程式 Proxy 會取代這些遠端存取使用案例的 VPN 或反向 Proxy 需求。 它不適合公司網路上的使用者。 使用應用程式 Proxy 進行內部網路存取的使用者，可能會遇到不理想的效能問題。

本文包含規劃、操作及管理 Azure AD 應用程式 Proxy 所需的資源。

## <a name="plan-your-implementation"></a>計劃您的實作

下一節提供主要規劃專案的廣泛觀點，這些專案將會為您設定有效率的部署體驗。

### <a name="prerequisites"></a>Prerequisites

開始執行之前，您必須符合下列必要條件。 您可以在本 [教學](application-proxy-add-on-premises-application.md)課程中看到有關設定環境的詳細資訊，包括這些必要條件。

* **連接器**：連接器是您可以部署到的輕量代理程式：
   * 內部部署實體硬體
   * 裝載于任何管理程式解決方案內的 VM
   * 裝載在 Azure 中的 VM，可啟用對應用程式 Proxy 服務的輸出連線。

* 如需更詳細的總覽，請參閱 [瞭解 Azure AD App Proxy 連接器](application-proxy-connectors.md) 。

     * 必須先 [啟用 TLS 1.2 的](application-proxy-add-on-premises-application.md) 連接器電腦，才能安裝連接器。

     * 可能的話，請將連接器部署在與後端 web 應用程式伺服器 [相同的網路](application-proxy-network-topology.md) 和區段中。 在您完成應用程式探索後，最好先部署連接器。
     * 我們建議每個連接器群組都至少有兩個連接器，以提供高可用性和規模。 如果您在任何時間點都可能需要為機器提供服務，則有三個連接器是最佳的。 請參閱 [連接器容量表](./application-proxy-connectors.md#capacity-planning) ，以協助決定要安裝連接器的電腦類型。 電腦愈大，連接器將會是更多的緩衝區。

* **網路存取設定**： Azure AD 應用程式 Proxy 連接器透過 [HTTPS 連接到 Azure (tcp 埠 443) 和 HTTP (tcp 埠 80)](application-proxy-add-on-premises-application.md)。

   * 不支援終止連接器 TLS 流量，並且會防止連接器使用其各自的 Azure App Proxy 端點來建立安全通道。

   * 避免在連接器與 Azure 之間的輸出 TLS 通訊上進行所有形式的內嵌檢查。 連接器和後端應用程式之間的內部檢查是可行的，但可能會降低使用者體驗，因此不建議使用。

   * 連接器本身的負載平衡也不受支援，或甚至是必要的。

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>設定 Azure AD 應用程式 Proxy 之前的重要考慮

必須符合下列核心需求，才能設定和執行 Azure AD 應用程式 Proxy。

*  **Azure** 上線：在部署應用程式 proxy 之前，使用者身分識別必須從內部部署目錄同步處理，或直接在您的 Azure AD 租使用者中建立。 身分識別同步處理可讓 Azure AD 在允許使用者存取 App Proxy 發佈的應用程式之前，先預先驗證使用者，以及具有執行單一登入 (SSO) 所需的使用者識別碼資訊。

* **條件式存取需求**：我們不建議使用應用程式 Proxy 進行內部網路存取，因為這樣會增加影響使用者的延遲。 建議您使用應用程式 Proxy 搭配預先驗證和條件式存取原則，以從網際網路進行遠端存取。  為內部網路用途提供條件式存取的方法是將應用程式現代化，以便直接向 AAD 進行驗證。 如需詳細資訊，請參閱將 [應用程式遷移至 AAD 的資源](./migration-resources.md) 。

* **服務限制**：為了保護個別租使用者的資源過度耗用，有針對每個應用程式和租使用者設定的節流限制。 若要查看這些限制，請參閱 [Azure AD 服務限制和限制](../enterprise-users/directory-service-limits-restrictions.md)。 這些節流限制是以比一般使用量更高的基準效能為基礎，而且會針對大部分的部署提供大量的緩衝區。

* **公開憑證**：如果您使用自訂功能變數名稱，您必須購買 TLS/SSL 憑證。 根據您的組織需求，取得憑證可能需要一些時間，建議您儘早開始處理。 Azure 應用程式 Proxy 支援以標準、 [萬用字元](application-proxy-wildcard.md)或 SAN 為基礎的憑證。 如需詳細資訊，請參閱 [使用 Azure AD 應用程式 Proxy 設定自訂網域](application-proxy-configure-custom-domain.md)。

* **網域需求**：使用 Kerberos 限制委派來單一登入已發佈的應用程式 (KCD) 要求執行連接器的伺服器和執行應用程式的伺服器都已加入網域，而且屬於相同網域或信任網域。
如需主題的詳細資訊，請參閱使用應用程式 Proxy [進行單一登入的 KCD](application-proxy-configure-single-sign-on-with-kcd.md) 。 連接器服務會在本機系統的內容中執行，而且不應該設定為使用自訂身分識別。

* **Url 的 DNS 記錄**

   * 在應用程式 Proxy 中使用自訂網域之前，您必須在公用 DNS 中建立 CNAME 記錄，以允許用戶端將自訂定義的外部 URL 解析為預先定義的應用程式 Proxy 位址。 無法為使用自訂網域的應用程式建立 CNAME 記錄，將會讓遠端使用者無法連線到應用程式。 新增 CNAME 記錄所需的步驟可能會因 DNS 提供者而異，因此請瞭解如何 [使用 Azure 入口網站來管理 dns 記錄和記錄集](../../dns/dns-operations-recordsets-portal.md)。

   * 同樣地，連接器主機必須能夠解析所發佈之應用程式的內部 URL。

* **系統管理許可權和角色**

   * **連接器安裝** 需要本機系統管理員許可權，才能安裝在其上的 Windows server。 它也需要至少一個 *應用程式系統管理員* 角色來進行驗證，並向您的 Azure AD 租使用者註冊連接器實例。

   * **應用程式發佈和管理** 需要 *應用程式系統管理員* 角色。 應用程式系統管理員可以管理目錄中的所有應用程式，包括註冊、SSO 設定、使用者和群組指派和授權、應用程式 Proxy 設定，以及同意。 它不會授與管理條件式存取的能力。 *雲端應用程式系統管理員* 角色具有應用程式系統管理員的所有能力，但它不允許管理應用程式 Proxy 設定。

* **授權**：透過 Azure AD Premium 訂用帳戶可取得應用程式 Proxy。 如需授權選項和功能的完整清單，請參閱 [Azure Active Directory 定價頁面](https://azure.microsoft.com/pricing/details/active-directory/) 。

### <a name="application-discovery"></a>應用程式探索

藉由收集下列資訊，編譯透過應用程式 Proxy 發佈的所有範圍內應用程式清查：

| 資訊類型| 要收集的資訊 |
|---|---|
| 服務類型| 例如： SharePoint、SAP、CRM、自訂 Web 應用程式、API |
| 應用程式平台 | 例如： Windows IIS、Linux 上的 Apache、Tomcat、NGINX |
| 網域成員資格| Web 服務器的完整功能變數名稱 (FQDN)  |
| 應用程式位置 | Web 服務器或伺服器陣列位於您基礎結構中的位置 |
| 內部存取 | 在內部存取應用程式時所使用的正確 URL。 <br> 如果是伺服器陣列，使用何種類型的負載平衡？ <br> 應用程式是否從本身以外的來源繪製內容。<br> 判斷應用程式是否透過 Websocket 運作。 |
| 外部存取 | 應用程式已向外部公開的廠商解決方案。 <br> 您要用於外部存取的 URL。 如果是 SharePoint，請確定已根據 [本指南](/SharePoint/administration/configure-alternate-access-mappings)設定替代存取對應。 如果沒有，您將需要定義外部 Url。 |
| 公開憑證 | 如果使用自訂網域，則購買具有對應主體名稱的憑證。 如果有憑證存在，請注意可從中取得的序號和位置。 |
| 驗證類型| 應用程式支援所支援的驗證類型，例如基本、Windows 整合驗證、以表單為基礎、以標頭為基礎的宣告。 <br>如果應用程式設定為在特定網域帳戶下執行，請注意服務帳戶 (FQDN) 的完整功能變數名稱。<br> 如果是以 SAML 為基礎的，則識別碼和回復 Url。 <br> 如果是以標頭為基礎，則為廠商解決方案，以及處理驗證類型的特定需求。 |
| 連接器組名 | 將指定為此後端應用程式提供管道和 SSO 之連接器群組的邏輯名稱。 |
| 使用者/群組存取 | 將授與應用程式外部存取權的使用者或使用者群組。 |
| 其他需求 | 請注意發行應用程式時應考慮的任何其他遠端存取或安全性需求。 |

您可以下載此 [應用程式清查試算表](https://aka.ms/appdiscovery) 來清查您的應用程式。

### <a name="define-organizational-requirements"></a>定義組織需求

以下是您應定義組織商務需求的區域。 每個區域都包含需求範例

 **存取**

* 已加入網域或 Azure AD 加入裝置的遠端使用者，使用者可以使用無縫單一登入 (SSO) 安全地存取已發佈的應用程式。

* 具有已核准個人裝置的遠端使用者可以安全地存取發佈的應用程式，前提是他們已在 MFA 中註冊，並已在行動電話上將 Microsoft Authenticator 應用程式註冊為驗證方法。

**監管**

* 系統管理員可以定義和監視透過應用程式 Proxy 發佈之應用程式的使用者指派生命週期。

**安全性**

* 只有透過群組成員資格或個別指派給應用程式的使用者可以存取這些應用程式。

**效能**

* 相較于從內部網路存取應用程式，應用程式效能不會降低。

**使用者體驗**

* 使用者知道如何使用任何裝置平臺上熟悉的公司 Url 來存取他們的應用程式。

**稽核**
* 系統管理員可以審核使用者存取活動。


### <a name="best-practices-for-a-pilot"></a>試驗的最佳作法

使用單一登入 (SSO) ，判斷為單一應用程式進行遠端存取所需的時間和工作量。 若要這麼做，請執行考慮其初始探索、發行和一般測試的試驗。 使用已針對整合式 Windows 驗證 (IWA) 預先設定的簡單 IIS 型 web 應用程式，可協助建立基準，因為這項設定需要極短的精力才能成功試驗遠端存取和 SSO。

下列設計項目應直接在生產環境租使用者中提高試驗執行的成功。

**連接器管理**：

* 連接器在提供內部部署管道給您的應用程式時，扮演著重要的角色。 使用 **預設** 連接器群組適用于已發行應用程式的初始試驗測試，然後才能將它們委任到生產環境。 成功測試後的應用程式可移至生產連接器群組。

**應用程式管理**：

* 您的員工最有可能記住，外部 URL 是很熟悉且相關的。 請避免使用預先定義的 msappproxy.net 或 onmicrosoft.com 尾碼來發佈您的應用程式。 相反地，請提供熟悉的最上層驗證網域，並以邏輯主機名稱（例如內部網路）作為前置詞。 *<customers_domain> .com*。

* 藉由在 Azure MyApps 入口網站中隱藏其啟動圖示，以將試驗應用程式圖示的可見度限制為試驗群組。 當您準備好進行生產時，您可以將應用程式的範圍設定為其各自的目標物件（在相同的進入生產階段前租使用者中），或同時在生產環境租使用者中發佈應用程式。

**單一登入設定**：某些 SSO 設定具有特定的相依性，可能需要一些時間才能進行設定，因此請事先確定是否有相依性的問題，以避免變更控制延遲。 這包括加入網域的連接器主機，以使用 Kerberos 限制委派來執行 SSO (KCD) 並負責處理其他耗時的活動。 例如，如果需要以標頭為基礎的 SSO，請設定 PING 存取實例。

**連接器主機和目標應用程式之間的 tls**：安全性極為重要，因此應該一律使用連接器主機和目標應用程式之間的 tls。 尤其是，如果 web 應用程式設定為以表單為基礎的驗證 (FBA) ，則使用者認證會以純文字方式有效傳輸。

**以累加方式執行並測試每個步驟**。
在發行應用程式後進行基本功能測試，以確保符合所有使用者和商務需求，請遵循下列指示：

1. 使用預先驗證停用，測試及驗證 web 應用程式的一般存取。
2. 如果成功啟用預先驗證，並指派使用者和群組。 測試和驗證存取。
3. 然後，為您的應用程式新增 SSO 方法，然後再次測試以驗證存取。
4. 視需要套用條件式存取和 MFA 原則。 測試和驗證存取。

**疑難排解工具**：進行疑難排解時，一律會從連接器主機上的瀏覽器驗證已發佈應用程式的存取權開始，並確認應用程式如預期般運作。 更簡單的設定，可讓您更輕鬆地判斷根本原因，因此請考慮嘗試以最基本的設定重現問題，例如只使用單一連接器，而不使用 SSO。 在某些情況下，web 偵錯工具（例如 Telerik 的 Fiddler）可以在透過 proxy 存取的應用程式中，對存取或內容問題進行疑難排解是不可或缺的。 Fiddler 也可以做為 proxy，以協助追蹤和偵測行動平臺（例如 iOS 和 Android）的流量，以及幾乎可設定為透過 proxy 路由傳送的任何資訊。 如需詳細資訊，請參閱 [疑難排解指南](application-proxy-troubleshoot.md) 。

## <a name="implement-your-solution"></a>實行您的解決方案

### <a name="deploy-application-proxy"></a>部署應用程式 Proxy

本教學課程將說明部署應用程式 Proxy 的步驟，以 [新增用於遠端存取的內部部署應用程式](application-proxy-add-on-premises-application.md)。 如果安裝不成功，請在入口網站中選取 [針對  **應用程式 Proxy 進行疑難排解**  ]，或使用疑難排解指南 [來解決安裝應用程式 Proxy 代理程式連接器的問題](application-proxy-connector-installation-problem.md)。

### <a name="publish-applications-via-application-proxy"></a>透過應用程式 Proxy 發佈應用程式

發佈應用程式時，假設您已滿足所有必要條件，而且您在 [應用程式 Proxy] 頁面中有數個顯示為已註冊和作用中的連接器。

您也可以使用 [PowerShell](/powershell/module/azuread/?view=azureadps-2.0-preview)來發佈應用程式。

以下是發佈應用程式時要遵循的一些最佳作法：

* **使用連接器群組**：指派已指定發佈每個個別應用程式的連接器群組。 我們建議每個連接器群組都至少有兩個連接器，以提供高可用性和規模。 如果您在任何時間點都可能需要為機器提供服務，則有三個連接器是最佳的。 此外，請參閱 [使用連接器群組在個別的網路和位置上發佈應用程式](application-proxy-connector-groups.md) ，以瞭解您如何也可以使用連接器群組，依網路或位置來劃分您的連接器。

* **設定後端應用程式超時**：此設定在應用程式可能需要超過75秒的時間處理用戶端交易的情況下很有用。 例如，當用戶端將查詢傳送至作為資料庫前端的 web 應用程式時。 前端會將此查詢傳送到其後端資料庫伺服器，並等候回應，但在收到回應時，交談的用戶端就會結束。將 timeout 設定為 Long 可提供180秒，讓較長的交易完成。

* **使用適當的 Cookie 類型**

   * **僅限 HTTP Cookie**：讓應用程式 Proxy 在設定 COOKIE 的 HTTP 回應標頭中包含 HTTPOnly 旗標，以提供額外的安全性。 這種設定有助於減輕攻擊，例如跨網站腳本 (XSS) 。 對於需要存取會話 cookie 的用戶端/使用者代理程式，請將此設定保留為 [否]。 例如，連線到透過應用程式 Proxy 發佈之遠端桌面閘道的 RDP/MTSC 用戶端。

   * **安全 Cookie**：當 cookie 設定為安全屬性時，如果要求是透過 TLS 安全通道傳輸，則使用者代理程式 (用戶端應用程式) 只會在 HTTP 要求中包含 Cookie。 這有助於降低 cookie 在純文字通道上遭到入侵的風險，因此應該啟用。

   * **持續性 Cookie**：允許應用程式 Proxy 會話 Cookie 在瀏覽器關閉之間保存，直到其到期或已刪除為止。 用於豐富型應用程式（例如 office）在已發佈的 web 應用程式記憶體取檔的案例，而不會重新提示使用者進行驗證。 不過，請特別注意，因為持續性 cookie 最終可能會讓服務具有未授權存取的風險，如果未與其他補償控制項一起使用的話。 此設定只能用於無法在進程之間共用 cookie 的繼承應用程式。 最好是將您的應用程式更新為處理處理常式之間的 cookie，而不是使用此設定。

* **轉譯標頭中的 url**：當內部 DNS 無法設定為符合組織的公用命名空間時，您可以在標頭中將 url 設定為符合 (的分割 DNS) 。 除非您的應用程式需要用戶端要求中的原始主機標頭，否則請將此值設定為 [是]。 替代方式是讓連接器在內部 URL 中使用 FQDN 來路由傳送實際的流量，並使用外部 URL 中的 FQDN 作為主機標頭。 在大部分情況下，此替代方法應該允許應用程式在遠端存取時正常運作，但您的使用者會失去在 URL 以外的 & 內有相符的優點。

* **轉譯應用程式主體中的 url**：當您想要將來自該應用程式的連結轉譯回用戶端時，開啟應用程式的應用程式主體連結轉譯。 若已啟用，此函式會在將應用程式 Proxy 在 HTML 和 CSS 回應中找到的所有內部連結，以傳回到用戶端的方式，提供最大的嘗試。 當您發佈的應用程式中包含硬式編碼的絕對或 NetBIOS shortname 連結，或連結至其他內部部署應用程式之內容的應用程式時，這非常有用。

針對已發佈的應用程式連結至其他已發佈應用程式的案例，請啟用每個應用程式的連結轉譯，讓您能夠控制每個應用層級的使用者體驗。

例如，假設您有三個透過應用程式 Proxy 發佈的應用程式，而這些應用程式都是透過應用程式 Proxy 發佈的權益、費用和旅遊，加上第四個應用程式（意見反應）。

![圖1](media/App-proxy-deployment-plan/link-translation.png)

當您啟用權益應用程式的連結轉譯時，會將費用和旅遊的連結重新導向至這些應用程式的外部 Url，讓使用者從公司網路外部存取應用程式的使用者可以存取這些應用程式。 因為這兩個應用程式尚未啟用連結轉譯，所以從費用和旅遊回復至權益的連結無法運作。 因為沒有外部 URL，所以不會重新導向意見反應的連結，因此使用權益應用程式的使用者將無法從公司網路外部存取意見反應應用程式。 請參閱 [連結轉譯和其他重新導向選項](application-proxy-configure-hard-coded-link-translation.md)的詳細資訊。

### <a name="access-your-application"></a>存取您的應用程式

有幾個選項可用來管理對應用程式 Proxy 發佈資源的存取，因此請選擇最適合您的特定案例和擴充性需求的選項。 常見的方法包括：使用透過 Azure AD Connect 同步處理的內部部署群組、使用由資源擁有者所管理的自助群組，根據使用者屬性在 Azure AD 中建立動態群組，或使用由資源擁有者所管理的自助群組，或所有這些群組的組合。 請參閱連結的資源，以瞭解每個資源的優點。

將應用程式的存取權指派給使用者的最簡單方式，就是從您已發佈應用程式的左窗格中，移至 [ **使用者和群組** ] 選項，並直接指派群組或個人。

![圖 24](media/App-proxy-deployment-plan/add-user.png)

您也可以將應用程式的存取權指派給其目前不是成員的群組，並設定自助選項，以允許使用者透過自助方式存取您的應用程式。

![圖25](media/App-proxy-deployment-plan/allow-access.png)

如果啟用，使用者將能夠登入 MyApps 入口網站並要求存取權，並可自動核准並新增至已允許的自助群組，或需要指定核准者的核准。

來賓使用者也可以 [受邀透過 AZURE AD B2B 存取透過應用程式 Proxy 發佈的內部應用程式](../external-identities/add-users-information-worker.md)。

對於通常可以匿名存取的內部部署應用程式，如果不需要驗證，您可能會想要停用位於應用程式 **屬性** 中的選項。

![圖26](media/App-proxy-deployment-plan/assignment-required.png)


將此選項設為 [否] 可讓使用者在沒有許可權的情況下透過 Azure AD App Proxy 存取內部部署應用程式，因此請小心使用。

當您的應用程式發佈之後，您就可以在瀏覽器中輸入其外部 URL 或其在上的圖示，以存取應用程式 [https://myapps.microsoft.com](https://myapps.microsoft.com/) 。

### <a name="enable-pre-authentication"></a>啟用預先驗證

確認您的應用程式可透過透過外部 URL 存取它的應用程式 Proxy 存取。

1. 流覽至 **Azure Active Directory**  >  **企業應用** 程式  >  的 **所有應用程式**]，然後選擇您想要管理的應用程式。

2. 選取 [應用程式 Proxy]。

3. 在 [ **預先驗證** ] 欄位中，使用下拉式清單選取 **Azure Active Directory**，然後選取 [ **儲存**]。

啟用預先驗證之後，Azure AD 會先挑戰使用者進行驗證，而且如果已設定單一登入，則後端應用程式也會在授與應用程式存取權之前先驗證使用者。 將預先驗證模式從傳遞變更為 Azure AD 也會使用 HTTPS 設定外部 URL，因此一開始針對 HTTP 設定的任何應用程式，現在都會以 HTTPS 來保護。

### <a name="enable-single-sign-on"></a>啟用單一登入

SSO 能提供最佳的使用者體驗和安全性，因為使用者只需要在存取 Azure AD 時登入一次。 一旦使用者經過預先驗證，SSO 就會由應用程式 Proxy 連接器對內部部署應用程式進行驗證，並代表使用者進行驗證。 後端應用程式會像是使用者本身一樣處理登入。

選擇 [ **傳遞** ] 選項可讓使用者存取已發佈的應用程式，而不需要向 Azure AD 進行驗證。

只有在 Azure AD 可以識別要求存取資源的使用者時，才可以執行 SSO，因此您的應用程式必須設定為在 SSO 運作的存取權 Azure AD 之前驗證使用者，否則會停用 SSO 選項。

[在 Azure AD 中讀取應用程式的單一登入](what-is-single-sign-on.md)，以協助您在設定應用程式時選擇最適當的 SSO 方法。

###  <a name="working-with-other-types-of-applications"></a>使用其他類型的應用程式

Azure AD 應用程式 Proxy 也可以支援已開發的應用程式，以使用 [Microsoft 驗證程式庫 (MSAL) ](../develop/v2-overview.md)。 它支援原生用戶端應用程式，方法是使用在用戶端要求的標頭資訊中收到的 Azure AD 發行權杖，以代表使用者執行預先驗證。

閱讀 [發佈原生和行動用戶端應用](./application-proxy-configure-native-client-application.md) 程式和 [宣告式應用程式](./application-proxy-configure-for-claims-aware-applications.md) ，以瞭解應用程式 Proxy 的可用設定。

### <a name="use-conditional-access-to-strengthen-security"></a>使用條件式存取來加強安全性

應用程式安全性需要一組豐富的安全性功能，可防止和回應內部部署和雲端中的複雜威脅。 攻擊者最常透過弱式、預設或遭竊的使用者認證取得公司網路存取權。  Microsoft 身分識別導向的安全性可管理及保護特殊許可權和非特殊許可權的身分識別，藉此減少使用遭竊的認證。

下列功能可以用來支援 Azure AD 應用程式 Proxy：

* 以位置為基礎的 [條件式存取原則](../conditional-access/location-condition.md)，根據地理位置或 IP 位址限制使用者存取，來保護敏感性資料的保護。

* 以裝置為基礎的條件式存取：確定只有已註冊、已核准且符合規範的裝置可以使用 [裝置型條件式存取](../conditional-access/require-managed-devices.md)來存取公司資料。

* 以應用程式為基礎的條件式存取：當使用者不在公司網路上時，不需要停止工作。 [安全存取公司雲端和內部部署應用程式](../conditional-access/app-based-conditional-access.md) ，並使用條件式存取維持控制。

* 以風險為基礎的條件式存取：利用以 [風險為基礎的條件式存取原則](https://www.microsoft.com/cloud-platform/conditional-access) ，保護您的資料免于惡意駭客的攻擊，不論是在內部部署或雲端中，都可以套用至所有應用程式和所有使用者。

* Azure AD 我的應用程式：已部署應用程式 Proxy 服務，並已安全發佈應用程式，可為您的使用者提供簡單的中樞，以探索並存取其所有應用程式。 透過 [我的應用程式](https://aka.ms/AccessPanelDPDownload)，透過自助功能來提升生產力，例如能夠要求存取新的應用程式和群組，或管理這些資源的存取權。

## <a name="manage-your-implementation"></a>管理您的實作為

### <a name="required-roles"></a>必要的角色

Microsoft 提倡授與最低可能許可權的原則，以 Azure AD 執行所需的工作。 [檢查可用的不同 Azure 角色](../roles/permissions-reference.md) ，然後選擇正確的角色來滿足每個角色的需求。 某些角色可能需要暫時套用，並在部署完成後移除。

| 商務角色| 商務工作| Azure AD 角色 |
|---|---|---|
| 技術支援中心管理員 | 通常僅限於合格的使用者回報問題，以及執行有限的工作，例如變更使用者的密碼、使重新整理權杖失效，以及監視服務健康狀態。 | 服務台系統管理員 |
| 身分識別管理員| 讀取 Azure AD 登入報表和審核記錄，以偵測應用程式 Proxy 的相關問題。| 安全性讀取者 |
| 應用程式擁有者| 建立和管理企業應用程式、應用程式註冊和應用程式 proxy 設定的所有層面。| 應用程式管理員 |
| 基礎結構系統管理員 | 憑證變換擁有者 | 應用程式管理員 |

將可以存取安全資訊或資源的人數降到最低，將有助於降低惡意執行者取得未授權存取的機會，或已授權的使用者不小心影響到敏感性資源。

不過，使用者仍然需要執行日常的特殊許可權作業，因此強制使用即時 (JIT) 型 [Privileged Identity Management](../privileged-identity-management/pim-configure.md) 原則，以提供對 Azure 資源的隨選特殊許可權存取，而 Azure AD 則是有效管理管理存取和審核的建議方法。

### <a name="reporting-and-monitoring"></a>報告和監控

Azure AD 透過 [審核記錄和報表，](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)提供您組織應用程式使用量和操作健全狀況的其他深入解析。 應用程式 Proxy 也可讓您輕鬆地從 Azure AD 入口網站和 Windows 事件記錄檔監視連接器。

#### <a name="application-audit-logs"></a>應用程式稽核記錄

這些記錄提供有關使用應用程式 Proxy 設定的應用程式登入的詳細資訊，以及裝置與存取應用程式的使用者。 [Audit 記錄](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 檔位於 Azure 入口網站和 [audit API](/graph/api/resources/directoryaudit?view=graph-rest-beta) for export 中。 此外，您的應用程式也可以使用 [使用量和深入解析報表](../reports-monitoring/concept-usage-insights-report.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 。

#### <a name="application-proxy-connector-monitoring"></a>應用程式 Proxy 連接器監視

連接器和服務會負責所有高可用性的工作。 您可以從 Azure AD 入口網站的 [應用程式 Proxy] 頁面監視連接器的狀態。 如需連接器維護的詳細資訊，請參閱 [瞭解 Azure AD 應用程式 Proxy 連接器](./application-proxy-connectors.md#maintenance)。

![範例： Azure AD 應用程式 Proxy 連接器](./media/application-proxy-connectors/app-proxy-connectors.png)

#### <a name="windows-event-logs-and-performance-counters"></a>Windows 事件記錄檔和效能計數器

連接器同時具有系統管理員和會話記錄。 系統管理記錄包含索引鍵事件和其錯誤。 這些工作階段記錄包含所有交易，以及它們的處理詳細資料。 記錄檔和計數器位於 Windows 事件記錄檔中，如需詳細資訊，請參閱 [瞭解 Azure AD 應用程式 Proxy 連接器](./application-proxy-connectors.md#under-the-hood)。 遵循本 [教學課程，在 Azure 監視器中設定事件記錄檔資料來源](../../azure-monitor/platform/data-sources-windows-events.md)。

### <a name="troubleshooting-guide-and-steps"></a>疑難排解指南和步驟

深入瞭解常見的問題，以及如何解決錯誤訊息的 [疑難排解](application-proxy-troubleshoot.md) 指南。

下列文章涵蓋的常見案例也可以用來為您的支援組織建立疑難排解指南。

* [顯示應用程式頁面時發生問題](application-proxy-page-appearance-broken-problem.md)
* [應用程式載入時間過長](application-proxy-page-load-speed-problem.md)
* [應用程式頁面連結無效](application-proxy-page-links-broken-problem.md)
* [要開啟哪些適用於應用程式的連接埠](application-proxy-add-on-premises-application.md)
* [適用於應用程式的連接器群組中沒有運作中的連接器](application-proxy-connectivity-no-working-connector.md)
* [在管理入口網站中設定](application-proxy-config-how-to.md)
* [在應用程式中設定單一登入](application-proxy-config-sso-how-to.md)
* [在管理入口網站中建立應用程式時發生問題](application-proxy-config-problem.md)
* [設定 Kerberos 限制委派](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [使用 PingAccess 設定](/azure/active-directory/manage-apps/application-proxy-ping-access-publishing-guide)
* [「無法存取此公司應用程式」錯誤](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [安裝應用程式 Proxy 代理程式連接器時遇到問題](application-proxy-connector-installation-problem.md)
* [登入問題](application-sign-in-problem-on-premises-application-proxy.md)