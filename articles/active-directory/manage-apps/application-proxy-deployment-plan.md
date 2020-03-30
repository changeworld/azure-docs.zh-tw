---
title: 規劃 Azure 活動目錄應用程式代理部署
description: 規劃組織內應用程式代理部署的端到端指南
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330915"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>規劃 Azure AD 應用程式代理部署

Azure 活動目錄 （Azure AD） 應用程式代理是本地應用程式的安全且經濟高效的遠端存取解決方案。 它為"雲優先"組織提供了一個即時過渡路徑，用於管理對尚未能夠使用現代協定的舊式本地應用程式的訪問。 有關其他介紹性資訊，請參閱[什麼是應用程式代理](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)。

建議使用應用程式代理來允許遠端使用者訪問內部資源。 應用程式代理取代了這些遠端存取用例對 VPN 或反向代理的需求。 它不適用於公司網路上的使用者。 這些使用應用程式代理進行 Intranet 訪問的使用者可能會遇到不良的性能問題。

本文包括規劃、操作和管理 Azure AD 應用程式代理所需的資源。 

## <a name="plan-your-implementation"></a>計劃您的實作

以下部分提供了關鍵規劃元素的廣泛視圖，這些元素將為您設置高效的部署體驗。 

### <a name="prerequisites"></a>Prerequisites

在開始實現之前，您需要滿足以下先決條件。 在[本教程](application-proxy-add-on-premises-application.md)中，您可以看到有關設置環境的詳細資訊，包括這些先決條件。

* **連接器**：連接器是羽量級代理，您可以部署到：
   * 本地物理硬體
   * 託管在任何虛擬機器管理程式解決方案中的 VM
   * 託管在 Azure 中的 VM，用於啟用到應用程式代理服務的出站連接。

* 有關更詳細的概述[，請參閱瞭解 Azure AD 應用代理連接器](application-proxy-connectors.md)。

     * 在安裝連接器之前，[必須為 TLS 1.2 啟用](application-proxy-add-on-premises-application.md)連接器電腦。

     * 如果可能，請將連接器部署在同一[網路中](application-proxy-network-topology.md)，並將連接器與後端 Web 應用程式伺服器進行段化。 最好在完成應用程式的發現後部署連接器。
     * 我們建議每個連接器組至少有兩個連接器，以提供高可用性和擴展。 擁有三個連接器是最佳選擇，以防您隨時需要維修機器。 查看[連接器容量表](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#capacity-planning)，以説明確定要安裝連接器的電腦類型。 機器越大，緩衝器和執行力就越大。

* **網路訪問設置**：Azure AD 應用程式代理連接器[通過 HTTPS（TCP 埠 443）和 HTTP（TCP 埠 80）連接到 Azure。](application-proxy-add-on-premises-application.md) 

   * 不支援終止連接器 TLS 流量，並將阻止連接器使用其各自的 Azure 應用代理終結點建立安全通道。

   * 避免對連接器和 Azure 之間的出站 TLS 通信進行各種形式的內聯檢查。 連接器和後端應用程式之間可以進行內部檢查，但可能會降低使用者體驗，因此不建議這樣做。

   * 連接器本身的負載平衡也不受支援，甚至是必需的。

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>配置 Azure AD 應用程式代理之前的重要注意事項

為了配置和實現 Azure AD 應用程式代理，必須滿足以下核心要求。

*  **Azure 載入**：在部署應用程式代理之前，必須從本地目錄同步使用者標識，或直接在 Azure AD 租戶中創建。 身分識別同步處理可讓 Azure AD 在允許使用者存取 App Proxy 發佈的應用程式之前，先預先驗證使用者，以及具有執行單一登入 (SSO) 所需的使用者識別碼資訊。

* **條件訪問要求**：我們不建議使用應用程式代理進行 Intranet 訪問，因為這會增加影響使用者的延遲。 我們建議將應用程式代理用於從 Internet 進行遠端存取的預身份驗證和條件訪問策略。  為 Intranet 使用提供條件訪問的方法是使應用程式現代化，以便它們可以直接使用 AAD 進行身份驗證。 有關詳細資訊，請參閱[參考參考資料將應用程式遷移到 AAD。](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) 

* **服務限制**：為了防止單個租戶過度消耗資源，每個應用程式和租戶都設置了限制限制。 要查看這些限制，請參閱[Azure AD 服務限制和限制](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions)。 這些限制限制基於遠高於典型使用量的基準，並為大多數部署提供了充足的緩衝區。

* **公共證書**：如果您使用的是自訂功能變數名稱，則必須採購 TLS/SSL 憑證。 根據您的組織要求，獲取證書可能需要一些時間，我們建議儘早開始該過程。 Azure 應用程式代理支援基於 SAN 的標準證書、[萬用字元](application-proxy-wildcard.md)或基於 SAN 的證書。 有關詳細資訊，請參閱[使用 Azure AD 應用程式代理配置自訂域](application-proxy-configure-custom-domain.md)。

* **域要求**：使用 Kerberos 限制委派 （KCD） 對已發佈的應用程式進行單一登入，要求運行連接器的伺服器和運行應用的伺服器都加入域，並加入同一域或信任域的一部分。
有關該主題的詳細資訊，請參閱[KCD，瞭解使用應用程式代理的單次登錄](application-proxy-configure-single-sign-on-with-kcd.md)。 連接器服務在本地系統的上下文中運行，不應配置為使用自訂標識。

* **URL 的 DNS 記錄**

   * 在應用程式代理中使用自訂域之前，必須在公共 DNS 中創建 CNAME 記錄，允許用戶端將自訂定義的外部 URL 解析為預定義的應用程式代理位址。 未能為使用自訂域的應用程式創建 CNAME 記錄將阻止遠端使用者連接到該應用程式。 添加 CNAME 記錄所需的步驟可能因 DNS 提供程式而異，因此瞭解如何使用[Azure 門戶管理 DNS 記錄和記錄集](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-portal)。

   * 同樣，連接器主機必須能夠解析要發佈的應用程式的內部 URL。

* **行政權利和角色**

   * **連接器安裝**需要對正在安裝它的 Windows 伺服器的本地管理員許可權。 它還需要至少應用程式*管理員*角色來對連接器實例進行身份驗證並將註冊到 Azure AD 租戶。 

   * **應用程式發佈和管理**需要*應用程式管理員*角色。 應用程式管理員可以管理目錄中的所有應用程式，包括註冊、SSO 設置、使用者和組分配和許可、應用程式代理設置和同意。 它不會授與管理條件式存取的能力。 *雲應用程式管理員*角色具有應用程式管理員的所有功能，只不過它不允許管理應用程式代理設置。

* **許可**：應用程式代理可通過 Azure AD 高級訂閱提供。 有關許可選項和功能的完整清單，請參閱[Azure 活動目錄定價頁](https://azure.microsoft.com/pricing/details/active-directory/)。  

### <a name="application-discovery"></a>應用程式發現

通過收集以下資訊，編制通過應用程式代理髮布的所有在冊應用程式清單：

| 資訊類型| 要收集的資訊 |
|---|---|
| 服務類型| 例如：共用點、SAP、CRM、自訂 Web 應用程式、API |
| 應用程式平台 | 例如：Windows IIS、Linux 上的 Apache、Tomcat、NGINX |
| 網域成員資格| Web 服務器完全限定的功能變數名稱 （FQDN） |
| 應用程式位置 | Web 服務器或伺服器場位於基礎結構中的位置 |
| 內部存取 | 在內部訪問應用程式時使用的確切 URL。 <br> 如果伺服器場正在使用哪種類型的負載平衡？ <br> 應用程式是否從自身以外的源提取內容。<br> 確定應用程式是否通過 WebSocket 運行。 |
| 外部存取 | 應用程式已對外公開的供應商解決方案。 <br> 要用於外部訪問的 URL。 如果 SharePoint，請確保根據[本指南](https://docs.microsoft.com/SharePoint/administration/configure-alternate-access-mappings)配置備用存取對應。 如果沒有，則需要定義外部 URL。 |
| 公開憑證 | 如果使用自訂域，則採購具有相應主題名稱的證書。 如果存在證書，請注意序號和位置，從何處可以獲取證書。 |
| 驗證類型| 應用程式支援支援的身份驗證類型，如基本驗證、Windows 集成身份驗證、基於表單、基於標頭和聲明。 <br>如果應用程式佈建為在特定域帳戶下運行，請注意服務帳戶的完全限定功能變數名稱 （FQDN）。<br> 如果基於 SAML，則識別碼和回復 URL。 <br> 如果基於標頭，則供應商解決方案和處理身份驗證類型的特定要求。 |
| 連接器組名稱 | 將指定為此後端應用程式提供導管和 SSO 的連接器組的邏輯名稱。 |
| 使用者/組訪問 | 將被授予應用程式外部存取權限的使用者或使用者組。 |
| 其他需求 | 請注意在發佈應用程式時應考慮的任何其他遠端存取或安全要求。 |

您可以下載此[應用程式清單試算表](https://aka.ms/appdiscovery)來清點您的應用程式。

### <a name="define-organizational-requirements"></a>定義組織要求

以下是您應定義組織的業務需求的領域。 每個區域都包含需求示例

 **存取**

* 具有域加入或 Azure AD 聯接設備的遠端使用者可以通過無縫的單一登入 （SSO） 安全地訪問已發佈的應用程式。

* 具有已批准個人設備的遠端使用者可以安全地訪問已發佈的應用程式，前提是他們已註冊在 MFA 中，並在其行動電話上註冊了 Microsoft 身份驗證器應用作為身份驗證方法。

**治理** 

* 管理員可以定義和監視使用者分配到通過應用程式代理髮布的應用程式的生命週期。

**安全性**

* 只有通過組成員身份或單獨分配給應用程式的使用者可以訪問這些應用程式。

**效能**

* 與從內部網路訪問應用程式相比，應用程式性能沒有下降。

**使用者體驗**

* 使用者知道如何在任何設備平臺上使用熟悉的公司 URL 來訪問其應用程式。

**審計**
* 管理員能夠審核使用者訪問活動。


### <a name="best-practices-for-a-pilot"></a>飛行員的最佳做法

確定使用單一登入 （SSO） 完全調試單個應用程式進行遠端存取所需的時間和精力。 為此，運行一個考慮其初始發現、發佈和常規測試的試驗。 使用已預配置為集成 Windows 身份驗證 （IWA） 的簡單基於 IIS 的 Web 應用程式將有助於建立基線，因為此設置只需很少的努力才能成功引導遠端存取和 SSO。

以下設計項目應直接在生產租戶中提高試驗實現的成功。  

**連接器管理**：  

* 連接器在為您的應用程式提供本地管道方面發揮著關鍵作用。 使用**預設**連接器組在將已發佈應用程式調試到生產之前，足以進行初始試驗測試。 然後，可以將成功測試的應用程式移動到生產連接器組。

**應用程式管理**：

* 您的員工最有可能記住外部 URL 是熟悉且相關的。 避免使用預定義的msappproxy.net或onmicrosoft.com尾碼發佈應用程式。 相反，提供一個熟悉的頂級驗證域，預綴于邏輯主機名稱，如*intranet.<customers_domain>.com*。

* 通過將引導應用程式圖示隱藏在 Azure MyApps 門戶中的啟動圖示，將試用應用程式圖示的可見度限制為試驗組。 準備生產時，可以將應用限定為其各自的目標受眾（在同一生產前租戶中）或在生產租戶中發佈應用程式。

**單一登入設置**：某些 SSO 設置具有特定的依賴項，可能需要一些時間來設置，因此通過確保提前解決依賴項來避免更改控制延遲。 這包括域聯接連接器主機，用於使用 Kerberos 限制委派 （KCD） 執行 SSO，並處理其他耗時的活動。 例如，如果需要基於標頭的 SSO，則設置 PING Access 實例。

**連接器主機和目標應用程式之間的 TLS：** 安全性至關重要，因此應始終使用連接器主機和目標應用程式之間的 TLS。 特別是如果 Web 應用程式配置為基於表單的身份驗證 （FBA），因為使用者憑據隨後以明文有效傳輸。

**逐步實現並測試每個步驟**。 發佈應用程式後執行基本功能測試，以確保滿足所有使用者和業務需求，請按照以下說明操作：

1. 在禁用預身份驗證後，測試和驗證對 Web 應用程式的總體訪問。
2. 如果成功啟用預身份驗證並分配使用者和組。 測試和驗證訪問。
3. 然後為應用程式添加 SSO 方法，然後再次測試以驗證訪問。
4. 根據需要應用條件訪問和 MFA 策略。 測試和驗證訪問。

**故障排除工具**：故障排除時，始終從連接器主機上的瀏覽器驗證對已發佈應用程式的存取權限，並確認應用程式按預期運行。 設置越簡單，就越容易確定根本原因，因此請考慮嘗試使用最小配置重現問題，例如僅使用單個連接器和不使用 SSO。 在某些情況下，Web 調試工具（如 Telerik 的 Fiddler）對於解決通過代理訪問的應用程式中的訪問或內容問題進行故障排除可能是必不可少的。 Fiddler 還可以充當代理，説明跟蹤和調試移動平臺（如 iOS 和 Android）的流量，以及幾乎任何可以配置為通過代理路由的流量。 有關詳細資訊，請參閱[故障排除指南](application-proxy-troubleshoot.md)。

## <a name="implement-your-solution"></a>實施您的解決方案

### <a name="deploy-application-proxy"></a>部署應用程式代理

本教程仲介紹了部署應用程式代理的步驟[，用於添加用於遠端存取的本地應用程式](application-proxy-add-on-premises-application.md)。 如果安裝不成功，請在門戶中選擇 **"疑難排解應用程式代理"，** 或使用故障排除指南[解決安裝應用程式代理代理連接器時的問題](application-proxy-connector-installation-problem.md)。

### <a name="publish-applications-via-application-proxy"></a>通過應用程式代理髮布應用程式

發佈應用程式假定您已滿足所有先決條件，並且在應用程式代理頁中顯示多個連接器為已註冊和活動。

您還可以使用[PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)發佈應用程式。

以下是發佈應用程式時應遵循的一些最佳做法：

* **使用連接器組**：分配已指定用於發佈每個子應用程式的連接器組。 我們建議每個連接器組至少有兩個連接器，以提供高可用性和擴展。 擁有三個連接器是最佳選擇，以防您隨時需要維修機器。 此外，請參閱[使用連接器組在單獨的網路和位置上發佈應用程式](application-proxy-connector-groups.md)，瞭解如何使用連接器組按網路或位置對連接器進行分段。

* **設置後端應用程式超時**：此設置在應用程式可能需要超過 75 秒來處理用戶端事務的情況下非常有用。 例如，當用戶端向充當資料庫前端的 Web 應用程式發送查詢時。 前端將此查詢發送到其後端資料庫伺服器並等待回應，但當它收到回應時，會話的用戶端超時。將超時設置為 Long 提供了 180 秒才能完成的較長事務。

* **使用適當的 Cookie 類型**

   * **HTTP-僅 Cookie**： 通過讓應用程式代理在設置 Cookie HTTP 回應標頭中包含 HTTPOnly 標誌來提供其他安全性。 此設置有助於緩解跨網站腳本 （XSS） 等漏洞。 對於確實需要訪問會話 Cookie 的用戶端/使用者代理，請保留此集為"否"。 例如，RDP/MTSC 用戶端連接到通過應用代理髮布的遠端桌面閘道。

   * **安全 Cookie：** 當使用 Secure 屬性設置 Cookie 時，使用者代理（用戶端應用）將僅在通過 TLS 安全通道傳輸請求時在 HTTP 要求中包含 Cookie。 這有助於降低 Cookie 在明文通道上洩露的風險，因此應啟用。

   * **持久性 Cookie**： 允許應用程式代理會話 Cookie 在瀏覽器關閉之間保留，直到過期或被刪除。 用於大量應用程式（如 Office）訪問已發佈的 Web 應用程式中的文檔，而無需重新提示使用者進行身份驗證的情況。 但是，請謹慎啟用，因為持久性 Cookie 最終可能會使服務面臨未經授權的訪問風險，如果不與其他補償控制一起使用。 此設置應僅用於無法在進程之間共用 Cookie 的舊應用程式。 最好更新應用程式以處理進程之間的共用 Cookie，而不是使用此設置。

* **在標頭中翻譯 URL：** 對於無法將內部 DNS 配置為與組織的公共命名空間（即拆分 DNS）匹配的方案啟用此選項。 除非應用程式在用戶端請求中需要原始主機標頭，否則請將此值設置為"是"。 另一種方法是讓連接器使用內部 URL 中的 FQDN 路由實際流量，在外部 URL 中使用 FQDN 作為主機頭。 在大多數情況下，此替代方法應允許應用程式在遠端存取時正常運行，但使用者失去了在外部 URL 中匹配&的好處。

* **在應用程式正文中翻譯 URL：** 當您希望將該應用程式中的連結翻譯成回應返回用戶端時，請打開應用的應用程式正文連結轉換。 如果啟用，此函數將盡力嘗試翻譯應用代理在 HTML 中找到的所有內部連結，並將 CSS 回應返回給用戶端。 在發佈包含內容中硬編碼絕對或 NetBIOS 短名稱連結的應用或包含連結到其他本地應用程式的內容的應用時，它非常有用。

對於已發佈的應用連結到其他已發佈應用的方案，請為每個應用程式啟用連結轉換，以便您能夠控制每個應用級別的使用者體驗。

例如，假設您有三個應用程式通過應用程式代理髮布，所有這些應用程式都相互連結：福利、費用和差旅，以及第四個應用程式"回饋"，這些應用程式不是通過應用程式代理髮布的。

![圖片 1](media/App-proxy-deployment-plan/link-translation.png)

當您為"福利"應用啟用連結轉換時，指向"支出"和"差旅"的連結將重定向到這些應用的外部 URL，以便從公司網路外部訪問應用程式的使用者可以訪問它們。 從"支出"和"旅行"到"福利"的連結不起作用，因為尚未為這兩個應用啟用連結轉換。 回饋的連結不會重定向，因為沒有外部 URL，因此使用"福利"應用的使用者將無法從公司網路外部訪問回饋應用。 請參閱[有關連結翻譯和其他重定向選項](application-proxy-configure-hard-coded-link-translation.md)的詳細資訊。

### <a name="access-your-application"></a>訪問您的應用程式

存在幾種選項來管理對應用代理已發佈資源的存取權限，因此請選擇最適合給定方案和可伸縮性需求的選項。 常見方法包括：使用通過 Azure AD Connect 同步的本機群組、基於使用者屬性在 Azure AD 中創建動態組、使用資源擁有者管理的自助服務組，或所有這些組合。 有關每個資源的好處，請參閱連結資源。

向使用者分配對應用程式的存取權限的最直接方法是從已發佈應用程式的左側窗格進入 **"使用者和組"** 選項，並直接分配組或個人。

![圖 24](media/App-proxy-deployment-plan/add-user.png)

還可以允許使用者通過分配他們當前不是其成員的組來自助訪問應用程式，並配置自助服務選項。

![圖片 25](media/App-proxy-deployment-plan/allow-access.png)

如果啟用，使用者將能夠登錄到 MyApps 門戶並請求訪問，並且可以自動批准並添加到已允許的自助服務組，或者需要指定審批方的批准。

還可以邀請來賓使用者[訪問通過 Azure AD B2B 通過應用程式代理髮布的內部應用程式](https://docs.microsoft.com/azure/active-directory/b2b/add-users-information-worker)。

對於通常匿名存取且不需要身份驗證的本地應用程式，您可能更喜歡禁用位於應用程式**屬性**中的選項。

![圖片 26](media/App-proxy-deployment-plan/assignment-required.png)


將此選項設置為"否"允許使用者在沒有許可權的情況下通過 Azure AD 應用代理訪問本地應用程式，因此請謹慎使用。

應用程式發佈後，應在瀏覽器中鍵入其外部 URL 或在其圖示上[https://myapps.microsoft.com](https://myapps.microsoft.com/)訪問它。

### <a name="enable-pre-authentication"></a>啟用預身份驗證

驗證應用程式是否可通過應用程式代理通過外部 URL 訪問它。 

1. 導航到**Azure 活動目錄** > **企業應用程式** > **所有應用程式**並選擇要管理的應用。

2. 選取 [應用程式 Proxy]****。

3. 在 **"預身份驗證"** 欄位中，使用下拉清單選擇**Azure 活動目錄**，然後選擇 **"保存**"。

啟用預身份驗證後，Azure AD 將首先向使用者發起身份驗證挑戰，如果配置了單一登入，則後端應用程式還將在授予對應用程式的訪問之前驗證使用者。 將預身份驗證模式從"直通"更改為 Azure AD 還會使用 HTTPS 配置外部 URL，因此現在使用 HTTPS 保護最初為 HTTPS 配置的任何應用程式。

### <a name="enable-single-sign-on"></a>啟用單一登入

SSO 提供了最佳的使用者體驗和安全性，因為使用者在訪問 Azure AD 時只需登錄一次。 使用者經過預身份驗證後，SSO 將由應用程式代理連接器代表使用者執行對本地應用程式進行身份驗證。 後端應用程式處理登錄，就好像它是使用者本身一樣。 

選擇 **"傳遞"** 選項允許使用者訪問已發佈的應用程式，而無需對 Azure AD 進行身份驗證。

僅當 Azure AD 能夠標識請求訪問資源的使用者時，才能執行 SSO，因此，應用程式必須在訪問 SSO 時將應用程式佈建為使用 Azure AD 對使用者進行預身份驗證才能運行，否則將禁用 SSO 選項。

讀取[Azure AD 中的應用程式的單一登入](what-is-single-sign-on.md)，以説明您在配置應用程式時選擇最合適的 SSO 方法。

###  <a name="working-with-other-types-of-applications"></a>使用其他類型的應用程式

Azure AD 應用程式代理還可以支援已開發以使用 Azure AD 身份驗證庫[（ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)） 或 Microsoft 身份驗證庫[（MSAL）](https://azure.microsoft.com/blog/start-writing-applications-today-with-the-new-microsoft-authentication-sdks/)的應用程式。 它支援本機用戶端應用，使用用戶端請求標頭資訊中收到的 Azure AD 頒發的權杖，以便代表使用者執行預身份驗證。

閱讀[發佈本機和移動用戶端應用](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-native-client)以及[宣告式應用程式程式](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-claims-aware-apps)，瞭解應用程式代理的可用配置。

### <a name="use-conditional-access-to-strengthen-security"></a>使用條件訪問加強安全性

應用程式安全需要一套高級的安全功能，這些功能可以抵禦和回應本地和雲中的複雜威脅。 攻擊者通常通過弱、預設或被盜的使用者憑據獲得公司網路存取權限。  Microsoft 身份驅動的安全性通過管理和保護特權和非特權標識來減少被盜憑據的使用。

以下功能可用於支援 Azure AD 應用程式代理：

* 基於地理位置的條件訪問：通過限制基於地理位置的使用者訪問或具有[基於位置的條件訪問策略的](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations)IP 位址來保護敏感性資料。

* 基於設備的條件訪問：確保只有已註冊、已批准和合規的設備才能使用[基於設備的條件訪問](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications)訪問訪問公司資料。

* 基於應用程式的條件訪問：當使用者不在公司網路上時，不必停止工作。 [通過條件訪問安全訪問企業雲和本地應用](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)並保持控制。

* 基於風險的條件訪問：使用[基於風險的條件訪問策略](https://www.microsoft.com/cloud-platform/conditional-access)保護您的資料免受惡意駭客的攻擊，該策略可應用於所有應用和所有使用者，無論是本地還是雲中。

* Azure AD 訪問面板：部署應用程式代理服務並安全地發佈應用程式後，為使用者提供一個簡單的中心來發現和訪問其所有應用程式。 通過自助服務功能提高工作效率，例如通過[訪問面板](https://aka.ms/AccessPanelDPDownload)請求訪問新應用和組或代表他人管理對這些資源的訪問。

## <a name="manage-your-implementation"></a>管理您的實現

### <a name="required-roles"></a>所需角色

Microsoft 主張授予使用 Azure AD 執行所需任務的許可權盡可能少的原則。 [查看可用的不同 Azure 角色，](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)並選擇正確的角色來滿足每個角色的需求。 某些角色可能需要臨時應用並在部署完成後刪除。

| 商務角色| 業務任務| Azure AD 角色 |
|---|---|---|
| 説明台管理員 | 通常僅限於限定最終使用者報告的問題和執行有限的任務，如更改使用者的密碼、無效刷新權杖和監視服務運行狀況。 | 服務台系統管理員 |
| 身份管理員| 讀取 Azure AD 登錄報告和稽核記錄以調試應用代理相關問題。| 安全性讀取者 |
| 應用程式擁有者| 創建和管理企業應用程式、應用程式註冊和應用程式代理設置的所有方面。| 應用程式管理員 |
| 基礎設施管理員 | 證書滾動擁有者 | 應用程式管理員 |

儘量減少有權訪問安全資訊或資源的人數將有助於減少惡意參與者獲取未經授權的存取權限或授權使用者無意中影響敏感資源的可能性。 
 
但是，使用者仍然需要執行日常的特權操作，因此強制實施基於即時 （JIT）[的特權標識管理](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)策略，以便按需對 Azure 資源和 Azure AD 提供特權訪問，是我們有效管理管理訪問和審核的建議方法。

### <a name="reporting-and-monitoring"></a>報告和監控

Azure AD 通過[稽核記錄和報告](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)，提供了對組織應用程式使用方式和操作運行狀況的其他見解。 應用程式代理還使從 Azure AD 門戶和 Windows 事件日誌監視連接器變得非常容易。

#### <a name="application-audit-logs"></a>應用程式稽核記錄

這些日誌提供有關使用應用程式代理配置的應用程式以及設備和訪問應用程式的使用者登錄的詳細資訊。 [稽核記錄](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)位於 Azure 門戶和審核[API](https://docs.microsoft.com/graph/api/resources/directoryaudit?view=graph-rest-beta)中，用於匯出。 此外，[您的應用程式還提供使用方式和見解報告](../reports-monitoring/concept-usage-insights-report.md?context=azure/active-directory/manage-apps/context/manage-apps-context)。

#### <a name="application-proxy-connector-monitoring"></a>應用程式代理連接器監控

連接器和服務會負責所有高可用性的工作。 可以從 Azure AD 門戶中的應用程式代理頁監視連接器的狀態。 有關連接器維護的詳細資訊，[請參閱 Azure AD 應用程式代理連接器](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#maintenance)。

![示例：Azure AD 應用程式代理連接器](./media/application-proxy-connectors/app-proxy-connectors.png)

#### <a name="windows-event-logs-and-performance-counters"></a>Windows 事件日誌和效能計數器

連接器同時具有管理員和會話日誌。 系統管理記錄包含索引鍵事件和其錯誤。 這些工作階段記錄包含所有交易，以及它們的處理詳細資料。 日誌和計數器位於 Windows 事件日誌中，有關詳細資訊，[請參閱瞭解 Azure AD 應用程式代理連接器](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#under-the-hood)。 請按照[本教程在 Azure 監視器 中配置事件日誌資料來源](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events)。

### <a name="troubleshooting-guide-and-steps"></a>故障排除指南和步驟

通過我們的[錯誤訊息故障排除](application-proxy-troubleshoot.md)指南，詳細瞭解常見問題以及如何解決這些問題。 

以下文章介紹常見方案，這些方案還可用於為支援組織創建故障排除指南。 

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
