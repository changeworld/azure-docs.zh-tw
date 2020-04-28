---
title: 規劃 Azure Active Directory 應用程式 Proxy 部署
description: 規劃在組織內部署應用程式 proxy 的端對端指南
services: active-directory
documentationcenter: azure
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: baselden
ms.reviewer: ''
ms.openlocfilehash: edd607c4d708df9fcfd3cbd5fdb71f0a7652d6c0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80330915"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>規劃 Azure AD 應用程式 Proxy 部署

Azure Active Directory （Azure AD）應用程式 Proxy 是適用于內部部署應用程式的安全且符合成本效益的遠端存取解決方案。 它會為「雲端優先」組織提供立即的轉換路徑，以管理尚無法使用新式通訊協定的舊版內部部署應用程式的存取權。 如需其他簡介資訊，請參閱[什麼是應用程式 Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)。

建議使用應用程式 Proxy，讓遠端使用者存取內部資源。 應用程式 Proxy 會取代這些遠端存取使用案例的 VPN 或反向 Proxy 的需求。 不適合公司網路上的使用者。 使用應用程式 Proxy 進行內部網路存取的使用者，可能會遇到不想要的效能問題。

本文包含規劃、操作和管理 Azure AD 應用程式 Proxy 所需的資源。 

## <a name="plan-your-implementation"></a>計劃您的實作

下一節提供主要規劃元素的廣泛觀點，這些專案會為您設定有效率的部署體驗。 

### <a name="prerequisites"></a>Prerequisites

開始執行之前，您必須符合下列必要條件。 在本[教學](application-proxy-add-on-premises-application.md)課程中，您可以查看有關設定環境的詳細資訊，包括這些必要條件。

* **連接器**：連接器是輕量代理程式，您可以將其部署到：
   * 內部部署實體硬體
   * 裝載于任何虛擬機器解決方案內的 VM
   * Azure 中裝載的 VM，可啟用對應用程式 Proxy 服務的輸出連線。

* 如需更詳細的總覽，請參閱[瞭解 Azure AD App Proxy 連接器](application-proxy-connectors.md)。

     * 在安裝連接器之前，必須先[啟用 TLS 1.2 的](application-proxy-add-on-premises-application.md)連接器電腦。

     * 可能的話，請在與後端 web 應用程式伺服器[相同的網路](application-proxy-network-topology.md)和區段中部署連接器。 在您完成應用程式探索後，最好部署連接器。
     * 我們建議每個連接器群組至少有兩個連接器，以提供高可用性和規模。 如果您可能需要在任何時間點服務機器，則有三個連接器是最佳做法。 請參閱[連接器容量表格](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#capacity-planning)，以協助決定要在哪一種類型的電腦上安裝連接器。 電腦愈大，且連接器的效能也愈高。

* **網路存取設定**： Azure AD 應用程式 Proxy 連接器透過[HTTPS （tcp 通訊埠443）和 HTTP （tcp 通訊埠80）連線到 Azure](application-proxy-add-on-premises-application.md)。 

   * 不支援終止連接器 TLS 流量，且會阻止連接器使用其各自的 Azure App Proxy 端點來建立安全通道。

   * 避免在連接器與 Azure 之間的輸出 TLS 通訊上進行所有形式的內嵌檢查。 可以在連接器和後端應用程式之間進行內部檢查，但可能會降低使用者體驗，因此不建議這麼做。

   * 連接器本身的負載平衡也不受支援，甚至是必要的。

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>設定 Azure AD 應用程式 Proxy 之前的重要考慮事項

若要設定及執行 Azure AD 應用程式 Proxy，必須符合下列核心需求。

*  **Azure**上線：在部署應用程式 proxy 之前，使用者身分識別必須從內部部署目錄進行同步處理，或直接在您的 Azure AD 租使用者內建立。 身分識別同步處理可讓 Azure AD 在允許使用者存取 App Proxy 發佈的應用程式之前，先預先驗證使用者，以及具有執行單一登入 (SSO) 所需的使用者識別碼資訊。

* **條件式存取需求**：我們不建議使用應用程式 Proxy 進行內部網路存取，因為這會增加會影響使用者的延遲。 我們建議使用應用程式 Proxy 搭配預先驗證和條件式存取原則，以從網際網路進行遠端存取。  提供內部網路使用條件式存取的方法，就是將應用程式現代化，讓他們可以直接向 AAD 進行驗證。 如需詳細資訊，請參閱將[應用程式遷移至 AAD 的資源](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources)。 

* **服務限制**：若要防止個別租使用者的資源過度耗用，有針對每個應用程式和租使用者設定的節流限制。 若要查看這些限制，請參閱[Azure AD 的服務限制和限制](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions)。 這些節流限制是根據一般使用量的基準測試，並為大部分的部署提供充足的緩衝區。

* **公開憑證**：如果您要使用自訂功能變數名稱，則必須購買 TLS/SSL 憑證。 根據您的組織需求，取得憑證可能需要一些時間，建議您儘早開始處理。 Azure 應用程式 Proxy 支援標準、[萬用字元](application-proxy-wildcard.md)或以 SAN 為基礎的憑證。 如需詳細資訊，請參閱[使用 Azure AD 應用程式 Proxy 來設定自訂網域](application-proxy-configure-custom-domain.md)。

* **網域需求**：使用 Kerberos 限制委派（KCD）的已發佈應用程式的單一登入需要執行連接器的伺服器和執行應用程式的伺服器已加入網域，且屬於相同網域或信任網域。
如需主題的詳細資訊，請參閱使用應用程式 Proxy[進行單一登入的 KCD](application-proxy-configure-single-sign-on-with-kcd.md) 。 Connector 服務在本機系統的內容中執行，不應設定為使用自訂身分識別。

* **Url 的 DNS 記錄**

   * 在應用程式 Proxy 中使用自訂網域之前，您必須在公用 DNS 中建立 CNAME 記錄，讓用戶端能夠將自訂定義的外部 URL 解析為預先定義的應用程式 Proxy 位址。 無法為使用自訂網域的應用程式建立 CNAME 記錄，將會讓遠端使用者無法連線到應用程式。 新增 CNAME 記錄所需的步驟可能會因 DNS 提供者而異，因此，請瞭解如何[使用 Azure 入口網站來管理 DNS 記錄和記錄集](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-portal)。

   * 同樣地，連接器主機必須能夠解析發行之應用程式的內部 URL。

* **系統管理權利和角色**

   * **連接器安裝**需要其安裝所在之 Windows server 的本機系統管理員許可權。 它也需要至少一個*應用程式系統管理員*角色來進行驗證，並向您的 Azure AD 租使用者註冊連接器實例。 

   * **應用程式發行和管理**需要*應用程式系統管理員*角色。 應用程式系統管理員可以管理目錄中的所有應用程式，包括註冊、SSO 設定、使用者和群組指派和授權、應用程式 Proxy 設定，以及同意。 它不會授與管理條件式存取的能力。 *雲端應用程式系統管理員*角色具有應用程式系統管理員的所有功能，但不允許管理應用程式 Proxy 設定。

* **授權**：應用程式 Proxy 可透過 Azure AD Premium 訂用帳戶取得。 如需授權選項和功能的完整清單，請參閱[Azure Active Directory 定價頁面](https://azure.microsoft.com/pricing/details/active-directory/)。  

### <a name="application-discovery"></a>應用程式探索

藉由收集下列資訊，編譯透過應用程式 Proxy 發佈的所有範圍內應用程式的清查：

| 資訊類型| 要收集的資訊 |
|---|---|
| 服務類型| 例如： SharePoint、SAP、CRM、自訂 Web 應用程式、API |
| 應用程式平台 | 例如： Windows IIS、Apache on Linux、Tomcat、NGINX |
| 網域成員資格| 網頁伺服器的完整功能變數名稱（FQDN） |
| 應用程式位置 | Web 服務器或伺服器陣列位於您的基礎結構中 |
| 內部存取 | 在內部存取應用程式時所使用的確切 URL。 <br> 如果是伺服器陣列，使用哪種類型的負載平衡？ <br> 應用程式是否會從本身以外的來源繪製內容。<br> 判斷應用程式是否會透過 Websocket 運作。 |
| 外部存取 | 應用程式已公開至外部的廠商解決方案。 <br> 您想要用於外部存取的 URL。 如果 SharePoint，請確定已根據[本指引](https://docs.microsoft.com/SharePoint/administration/configure-alternate-access-mappings)設定替代存取對應。 如果不是，您就必須定義外部 Url。 |
| 公開憑證 | 如果使用自訂網域，則會採購具有對應主體名稱的憑證。 如果憑證存在，請記下可從中取得的序號和位置。 |
| 驗證類型| 應用程式支援的驗證類型，例如 [基本]、[Windows 整合驗證]、[表單架構]、[標頭型] 和 [宣告]。 <br>如果應用程式設定為在特定網域帳戶下執行，請注意服務帳戶的完整功能變數名稱（FQDN）。<br> 如果是以 SAML 為基礎，則為識別碼和回復 Url。 <br> 如果標頭為基礎，則為廠商解決方案，以及處理驗證類型的特定需求。 |
| 連接器組名 | 連接器群組的邏輯名稱，將指定為提供管道和 SSO 給這個後端應用程式。 |
| 使用者/群組存取 | 將授與應用程式外部存取權的使用者或使用者群組。 |
| 其他需求 | 請注意發行應用程式時應考慮的任何其他遠端存取或安全性需求。 |

您可以下載此[應用程式清查試算表](https://aka.ms/appdiscovery)來清查您的應用程式。

### <a name="define-organizational-requirements"></a>定義組織需求

以下是您應該定義組織商務需求的區域。 每個區域都包含需求的範例

 **存取**

* 已加入網域或 Azure AD 加入裝置的遠端使用者，使用者可以使用無縫單一登入（SSO）安全地存取已發佈的應用程式。

* 已核准的個人裝置的遠端使用者可以安全地存取已發佈的應用程式，前提是它們已在 MFA 中註冊，並已在行動電話上將 Microsoft Authenticator 應用程式註冊為驗證方法。

**控管** 

* 系統管理員可以定義並監視透過應用程式 Proxy 發佈之應用程式的使用者指派生命週期。

**安全性**

* 只有透過群組成員資格或個別指派給應用程式的使用者可以存取這些應用程式。

**效能**

* 相較于從內部網路存取應用程式，應用程式效能不會降低。

**使用者體驗**

* 使用者可以在任何裝置平臺上，使用熟悉的公司 Url 來瞭解如何存取其應用程式。

**稽核**
* 系統管理員可以審核使用者存取活動。


### <a name="best-practices-for-a-pilot"></a>試驗的最佳做法

判斷使用單一登入（SSO）完整委員會單一應用程式進行遠端存取所需的時間和工作量。 若要這麼做，請執行考慮其初始探索、發佈和一般測試的試驗。 使用已針對整合式 Windows 驗證（IWA）預先設定的簡單 IIS 型 web 應用程式，可協助建立基準，因為此安裝程式需要最少的時間才能成功試驗遠端存取和 SSO。

下列設計項目應直接在生產租使用者中增加試驗執行的成功。  

**連接器管理**：  

* 在提供內部部署管道給您的應用程式時，連接器扮演著重要的角色。 使用**預設**的連接器群組，對於發佈的應用程式進行初始試驗測試，再將其委任到生產環境，就已足夠。 已成功測試的應用程式可移至生產連接器群組。

**應用程式管理**：

* 您的工作人員最有可能記住外部 URL 是熟悉且相關的。 避免使用預先定義的 msappproxy.net 或 onmicrosoft.com 尾碼來發行應用程式。 相反地，請提供熟悉的最上層驗證網域，並在前面加上邏輯主機名稱，例如*內部網路。 <customers_domain> .com*。

* 藉由隱藏其在 Azure MyApps 入口網站中的 [啟動] 圖示，將試驗應用程式圖示的可見度限制為試驗群組。 準備好進行生產時，您可以將應用程式的範圍設定為其各自的目標物件（不論是在相同的進入生產階段租使用者中），或是同時在您的生產環境租使用者中發佈應用程式。

**單一登入設定**：某些 SSO 設定具有特定的相依性，可能需要一些時間來進行設定，因此請務必預先解決相依性，以避免變更控制延遲。 這包括加入網域的連接器主機，以使用 Kerberos 限制委派（KCD）來執行 SSO，並負責處理其他耗時的活動。 例如，如果需要以標頭為基礎的 SSO，請設定 PING 存取實例。

**連接器主機與目標應用程式之間的 TLS**：安全性非常重要，因此應該一律使用連接器主機和目標應用程式之間的 tls。 特別是當 web 應用程式設定為以表單為基礎的驗證（FBA）時，就會以純文字有效地傳輸使用者認證。

**以累加方式執行，並測試每個步驟**。 在發佈應用程式之後進行基本的功能測試，以確保符合所有使用者和商務需求，遵循下列指示：

1. 使用預先驗證停用來測試和驗證 web 應用程式的一般存取。
2. 如果成功，則啟用預先驗證並指派使用者和群組。 測試並驗證存取。
3. 然後，為您的應用程式新增 SSO 方法，然後再次測試以驗證存取。
4. 視需要套用條件式存取和 MFA 原則。 測試並驗證存取。

**疑難排解工具**：進行疑難排解時，一律先從連接器主機上的瀏覽器驗證已發行應用程式的存取權開始，並確認應用程式如預期般運作。 您的設定更簡單，判斷根本原因更容易，因此，請考慮嘗試重現最少設定的問題，例如只使用單一連接器，而不使用 SSO。 在某些情況下，像是 Telerik 的 Fiddler 之類的 web 偵錯工具，可能會無法對透過 proxy 存取之應用程式中的存取或內容問題進行疑難排解。 Fiddler 也可以做為 proxy，以協助追蹤和偵測行動平臺（例如 iOS 和 Android）的流量，以及幾乎可設定為透過 proxy 路由傳送的任何專案。 如需詳細資訊，請參閱[疑難排解指南](application-proxy-troubleshoot.md)。

## <a name="implement-your-solution"></a>執行您的解決方案

### <a name="deploy-application-proxy"></a>部署應用程式 Proxy

本教學課程涵蓋部署應用程式 Proxy 的步驟，以[新增遠端存取的內部部署應用程式](application-proxy-add-on-premises-application.md)。 如果安裝不成功，請選取 [針對入口網站中的**應用程式 Proxy 進行疑難排解**]，或使用疑難排解指南[，以瞭解安裝應用程式 Proxy 代理程式連接器的問題](application-proxy-connector-installation-problem.md)。

### <a name="publish-applications-via-application-proxy"></a>透過應用程式 Proxy 發佈應用程式

發行應用程式會假設您已滿足所有必要條件，而且在 [應用程式 Proxy] 頁面中有數個顯示為已註冊和作用中的連接器。

您也可以使用[PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)來發行應用程式。

以下是發佈應用程式時要遵循的一些最佳作法：

* **使用連接器群組**：指派已指定用來發佈每個個別應用程式的連接器群組。 我們建議每個連接器群組至少有兩個連接器，以提供高可用性和規模。 如果您可能需要在任何時間點服務機器，則有三個連接器是最佳做法。 此外，請參閱[使用連接器群組在個別的網路和位置上發佈應用程式](application-proxy-connector-groups.md)，以瞭解您也可以如何使用連接器群組，以網路或位置來分割您的連接器。

* **設定後端應用程式超時**：此設定適用于應用程式可能需要超過75秒來處理用戶端交易的情況。 例如，當用戶端將查詢傳送至作為資料庫前端的 web 應用程式時。 前端會將此查詢傳送至其後端資料庫伺服器並等候回應，但在收到回應時，交談的用戶端會超時。將 timeout 設定為 Long 會提供180秒，讓較長的交易完成。

* **使用適當的 Cookie 類型**

   * **僅限 HTTP Cookie**：藉由讓應用程式 Proxy 在設定 Cookie HTTP 回應標頭中包含 HTTPOnly 旗標，提供額外的安全性。 這種設定有助於減輕攻擊的風險，例如跨網站腳本（XSS）。 對於需要存取會話 cookie 的用戶端/使用者代理程式，請將此設為 [否]。 例如，連接到透過應用程式 Proxy 發佈之遠端桌面閘道的 RDP/MTSC 用戶端。

   * **安全 cookie**：當 Cookie 是以 Secure 屬性設定時，如果透過 TLS 安全通道傳輸要求，則使用者代理程式（用戶端應用程式）只會在 HTTP 要求中包含 cookie。 這有助於降低 cookie 在純文字通道遭到入侵的風險，因此應加以啟用。

   * **持續性 Cookie**：允許應用程式 Proxy 會話 Cookie 在瀏覽器的持續時間之間保存，直到過期或遭到刪除為止。 用於 office 等豐富應用程式存取已發行 web 應用程式中的檔，而不會重新提示使用者進行驗證的情況。 但請謹慎啟用，因為持續性的 cookie 最終可能會讓服務在未經授權的情況下遭到存取，如果不是與其他補償控制項搭配使用。 此設定只應用於無法在進程之間共用 cookie 的繼承應用程式。 最好是更新您的應用程式，以處理進程之間的共用 cookie，而不是使用此設定。

* **轉譯標頭中的 url**：您可以在內部 DNS 無法設定為符合組織的公用命名空間（a. k. 分割的 DNS）時，啟用此功能。 除非您的應用程式需要用戶端要求中的原始主機標頭，否則請將此值設定為 [是]。 替代方式是讓連接器使用內部 URL 中的 FQDN，以路由傳送實際的流量，並將外部 URL 中的 FQDN 當做主機標頭。 在大部分情況下，這種替代方案應該允許應用程式在從遠端存取時正常運作，但是您的使用者會失去在 & 外部 URL 中相符的優勢。

* **轉譯應用程式主體中的 url**：當您想要將來自該應用程式的連結轉譯回用戶端時，請開啟應用程式的應用程式主體連結轉譯。 啟用時，此函式可在轉譯應用程式 Proxy 在 HTML 中找到的所有內部連結，以及傳回給用戶端的 CSS 回應中，提供最大的嘗試。 當發佈的應用程式包含內容中的硬式編碼絕對或 NetBIOS 簡短名稱連結，或具有連結至其他內部部署應用程式內容的應用程式時，這會很有用。

針對已發行應用程式連結至其他已發佈應用程式的案例，請啟用每個應用程式的連結轉譯，讓您能夠控制每個應用層級的使用者體驗。

例如，假設您有三個透過應用程式 Proxy 發佈的應用程式，全都彼此連結：權益、費用和旅遊，加上第四個應用程式、意見反應，而不是透過應用程式 Proxy 發佈。

![圖片1](media/App-proxy-deployment-plan/link-translation.png)

當您啟用權益應用程式的連結轉譯時，費用和旅遊的連結會重新導向至這些應用程式的外部 Url，讓從公司網路外部存取應用程式的使用者可以存取它們。 因為這兩個應用程式尚未啟用連結轉譯，所以來自費用和出差到權益的連結無法使用。 意見反應的連結不會重新導向，因為沒有外部 URL，因此使用權益應用程式的使用者將無法從公司網路外部存取意見反應應用程式。 請參閱[連結轉譯和其他重新導向選項](application-proxy-configure-hard-coded-link-translation.md)的詳細資訊。

### <a name="access-your-application"></a>存取您的應用程式

有數個選項可用來管理應用程式 Proxy 已發佈資源的存取權，因此，請選擇最適合您的特定案例和擴充性需求。 常見的方法包括：使用透過 Azure AD Connect 同步處理的內部部署群組、使用由資源擁有者所管理的自助群組，或所有這些功能的組合，根據使用者屬性在 Azure AD 中建立動態群組。 如需各項優點，請參閱連結的資源。

將應用程式的存取權指派給使用者的最簡單方法，就是從已發佈應用程式的左窗格進入 [**使用者和群組**] 選項，並直接指派群組或個人。

![圖 24](media/App-proxy-deployment-plan/add-user.png)

您也可以藉由指派目前不是成員的群組並設定自助選項，讓使用者能夠自助服務存取您的應用程式。

![圖片25](media/App-proxy-deployment-plan/allow-access.png)

啟用時，使用者就能夠登入 MyApps 入口網站並要求存取權，也可以自動核准並新增到已允許的自助群組，或需要指定核准者的核准。

來賓使用者也可以[受邀存取透過應用程式 Proxy 發佈的內部應用程式 AZURE AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/add-users-information-worker)。

對於通常以匿名方式存取的內部部署應用程式，如果不需要驗證，您可能會想要停用位於應用程式**屬性**中的選項。

![圖片26](media/App-proxy-deployment-plan/assignment-required.png)


將此選項設定為 [否] 可讓使用者在沒有許可權的情況下，透過 Azure AD App Proxy 存取內部部署應用程式，因此請小心使用。

一旦您的應用程式發佈之後，您就可以在瀏覽器中輸入其外部 URL，或在使用[https://myapps.microsoft.com](https://myapps.microsoft.com/)其圖示來存取它。

### <a name="enable-pre-authentication"></a>啟用預先驗證

確認您的應用程式可透過應用程式 Proxy 存取，其方式是經由外部 URL 存取它。 

1. 流覽至**Azure Active Directory** > **企業應用** > 程式] [**所有應用程式**]，然後選擇您想要管理的應用程式。

2. 選取 [應用程式 Proxy]****。

3. 在 [**預先驗證**] 欄位中，使用下拉式清單來選取**Azure Active Directory**，然後選取 [**儲存**]。

啟用預先驗證之後，Azure AD 會先挑戰使用者進行驗證，如果已設定單一登入，後端應用程式也會在授與應用程式存取權之前驗證使用者。 將預先驗證模式從「傳遞」變更為 Azure AD 也會使用 HTTPS 設定外部 URL，因此一開始針對 HTTP 設定的任何應用程式都會使用 HTTPS 來保護。

### <a name="enable-single-sign-on"></a>啟用單一登入

SSO 可提供最佳的使用者體驗和安全性，因為使用者只需要在存取 Azure AD 時登入一次。 一旦使用者經過預先驗證，SSO 就會由應用程式 Proxy 連接器執行，以向內部部署應用程式（代表使用者）進行驗證。 後端應用程式會以使用者本身的身分處理登入。 

選擇 [**傳遞**] 選項可讓使用者存取已發佈的應用程式，而不需要驗證 Azure AD。

只有在 Azure AD 可以識別要求資源存取權的使用者時，才能夠執行 SSO，因此您的應用程式必須設定為在存取 SSO 功能時預先驗證使用者 Azure AD，否則將會停用 SSO 選項。

[在 [Azure AD 中讀取應用程式的單一登入](what-is-single-sign-on.md)，協助您在設定應用程式時，選擇最適當的 SSO 方法。

###  <a name="working-with-other-types-of-applications"></a>使用其他類型的應用程式

Azure AD 應用程式 Proxy 也可以支援已開發的應用程式，以使用我們的 Azure AD 驗證程式庫（[ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)）或 Microsoft Authentication Library （[MSAL](https://azure.microsoft.com/blog/start-writing-applications-today-with-the-new-microsoft-authentication-sdks/)）。 它支援原生用戶端應用程式，方法是使用在用戶端要求的標頭資訊中收到的 Azure AD 發出的權杖，以代表使用者執行預先驗證。

請參閱[發佈原生和行動用戶端應用](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-native-client)程式和以[宣告為基礎的應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-claims-aware-apps)，以瞭解應用程式 Proxy 的可用設定。

### <a name="use-conditional-access-to-strengthen-security"></a>使用條件式存取來加強安全性

應用程式安全性需要一組先進的安全性功能，可保護內部部署和雲端中的複雜威脅，並加以回應。 攻擊者通常會透過弱式、預設或遭竊的使用者認證，取得公司網路的存取權。  Microsoft 身分識別驅動的安全性可透過管理和保護特殊許可權和非特殊許可權的身分識別，來減少使用遭竊的認證。

下列功能可以用來支援 Azure AD 應用程式 Proxy：

* 以使用者和位置為基礎的條件式存取：根據地理位置或具有[位置型條件式存取原則](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations)的 IP 位址限制使用者存取，來保護機密資料。

* 以裝置為基礎的條件式存取：確定只有已註冊、已核准且相容的裝置可以使用[裝置型條件式存取](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications)來存取公司資料。

* 以應用程式為基礎的條件式存取：當使用者不在公司網路上時，不需要停止工作。 [安全存取公司雲端和內部部署應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)，並使用條件式存取來維護控制權。

* 以風險為基礎的條件式存取：使用以[風險為基礎的條件式存取原則](https://www.microsoft.com/cloud-platform/conditional-access)來保護您的資料，使其可套用至所有應用程式和所有使用者，不論是內部部署或雲端。

* Azure AD 存取面板：已部署您的應用程式 Proxy 服務，並安全地發佈應用程式，為您的使用者提供簡單的中樞來探索及存取其所有應用程式。 透過[存取面板](https://aka.ms/AccessPanelDPDownload)，讓自助功能能夠要求存取新的應用程式和群組，或代表其他人管理這些資源的存取權，以提高生產力。

## <a name="manage-your-implementation"></a>管理您的執行

### <a name="required-roles"></a>必要角色

Microsoft 提倡授與最低可能許可權的原則，以 Azure AD 執行所需的工作。 請[查看可用的不同 Azure 角色](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)，並選擇正確的角色以滿足每個角色的需求。 某些角色可能需要暫時套用，並在部署完成後移除。

| 商務角色| 商務任務| Azure AD 角色 |
|---|---|---|
| 技術支援中心系統管理員 | 通常僅限於限定使用者回報的問題和執行有限的工作，例如變更使用者的密碼、使重新整理權杖失效，以及監視服務健康情況。 | 服務台系統管理員 |
| 身分識別管理員| 閱讀 Azure AD 登入報告和 audit 記錄檔，以偵測與應用程式 Proxy 相關的問題。| 安全性讀取者 |
| 應用程式擁有者| 建立及管理企業應用程式、應用程式註冊和應用程式 proxy 設定的所有層面。| 應用程式管理員 |
| 基礎結構系統管理員 | 憑證變換擁有者 | 應用程式管理員 |

將能夠存取安全資訊或資源的人數降到最低，將有助於減少惡意執行者取得未經授權存取的機會，或已授權的使用者不小心影響到敏感性資源。 
 
不過，使用者仍然需要執行特殊許可權作業，因此強制採用及時（JIT）的[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)原則，以提供對 Azure 資源的隨選特殊許可權存取，而 Azure AD 則是有效管理系統管理存取與審核的建議方法。

### <a name="reporting-and-monitoring"></a>報告和監控

Azure AD 透過[audit 記錄和報告，](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)為您組織的應用程式使用量和操作健全狀況提供額外的深入解析。 應用程式 Proxy 也可以讓您輕鬆地從 Azure AD 入口網站和 Windows 事件記錄監視連接器。

#### <a name="application-audit-logs"></a>應用程式稽核記錄

這些記錄會針對以應用程式 Proxy 和裝置以及存取應用程式的使用者所設定的應用程式，提供登入的詳細資訊。 [Audit 記錄](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)檔位於 Azure 入口網站中，以及用於匯出的[audit API](https://docs.microsoft.com/graph/api/resources/directoryaudit?view=graph-rest-beta)中。 此外，您的應用程式也可以使用 [[使用量] 和 [深入](../reports-monitoring/concept-usage-insights-report.md?context=azure/active-directory/manage-apps/context/manage-apps-context)解析] 報告。

#### <a name="application-proxy-connector-monitoring"></a>應用程式 Proxy 連接器監視

連接器和服務會負責所有高可用性的工作。 您可以從 Azure AD 入口網站中的 [應用程式 Proxy] 頁面，監視連接器的狀態。 如需連接器維護的詳細資訊，請參閱[瞭解 Azure AD 應用程式 Proxy 連接器](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#maintenance)。

![範例： Azure AD 應用程式 Proxy 連接器](./media/application-proxy-connectors/app-proxy-connectors.png)

#### <a name="windows-event-logs-and-performance-counters"></a>Windows 事件記錄檔和效能計數器

連接器同時具有系統管理員和會話記錄。 系統管理記錄包含索引鍵事件和其錯誤。 這些工作階段記錄包含所有交易，以及它們的處理詳細資料。 記錄和計數器位於 Windows 事件記錄檔如需詳細資訊，請參閱[瞭解 Azure AD 應用程式 Proxy 連接器](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#under-the-hood)。 遵循此[教學課程來設定 Azure 監視器中的事件記錄檔資料來源](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events)。

### <a name="troubleshooting-guide-and-steps"></a>疑難排解指南和步驟

若要深入瞭解常見問題，以及如何解決這些問題，請使用我們的指南來[疑難排解](application-proxy-troubleshoot.md)錯誤訊息。 

下列文章涵蓋的常見案例，也可以用來為您的支援組織建立疑難排解指南。 

* [顯示應用程式頁面時發生問題](application-proxy-page-appearance-broken-problem.md)
* [應用程式載入時間過長](application-proxy-page-load-speed-problem.md)
* [應用程式頁面連結無效](application-proxy-page-links-broken-problem.md)
* [要開啟哪些適用於應用程式的連接埠](application-proxy-connectivity-ports-how-to.md)
* [適用於應用程式的連接器群組中沒有運作中的連接器](application-proxy-connectivity-no-working-connector.md)
* [在管理入口網站中設定](application-proxy-config-how-to.md)
* [在應用程式中設定單一登入](application-proxy-config-sso-how-to.md)
* [在管理入口網站中建立應用程式時發生問題](application-proxy-config-problem.md)
* [設定 Kerberos 限制委派](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [使用 PingAccess 設定](application-proxy-back-end-ping-access-how-to.md)
* [「無法存取此公司應用程式」錯誤](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [安裝應用程式 Proxy 代理程式連接器時遇到問題](application-proxy-connector-installation-problem.md)
* [登入問題](application-sign-in-problem-on-premises-application-proxy.md)
