---
title: 設定常見問題
description: 取得 Azure App Service 設定和管理問題的常見問題解答。
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 867c011cb3086cab736d17501e72bc477d0d2889
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82159995"
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Azure 中 Web 應用程式的設定和管理常見問題集

對於 [Azure App Service 的 Web 應用程式功能](https://azure.microsoft.com/services/app-service/web/)的設定和管理問題，本文章提供常見問題集 (FAQ) 的解答。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>如果我想要移動 App Service 資源，有應該注意的限制嗎？

如果您打算將 App Service 資源移至新的資源群組或訂用帳戶，有一些要注意的限制。 如需詳細資訊，請參閱 [App Service 限制](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)。

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>如何針對 Web 應用程式使用自訂網域名稱？

如需將自訂網域名稱與 Azure Web 應用程式搭配使用的常見問題解答，請參閱我們的七分鐘影片：[新增自訂網域名稱](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name)。 影片提供如何新增自訂網域名稱的逐步解說。 說明如何搭配使用自己的 URL (而不是 *.azurewebsites.net URL) 與 App Service Web 應用程式。 您也可以參閱[如何對應自訂網域名稱](app-service-web-tutorial-custom-domain.md)的詳細逐步解說。


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>如何為 Web 應用程式購買新的自訂網域？

若要深入了解如何購買並設定 App Service Web 應用程式的自訂網域，請參閱[購買自訂網域名稱並且在 App Service 中設定](manage-custom-dns-buy-domain.md)。


## <a name="how-do-i-upload-and-configure-an-existing-tlsssl-certificate-for-my-web-app"></a>如何? 針對我的 web 應用程式上傳和設定現有的 TLS/SSL 憑證嗎？

若要瞭解如何上傳和設定現有的自訂 TLS/SSL 憑證，請參閱[將 TLS/ssl 憑證新增至您的 App Service 應用程式](configure-ssl-certificate.md)。


## <a name="how-do-i-purchase-and-configure-a-new-tlsssl-certificate-in-azure-for-my-web-app"></a>如何? 在 Azure 中針對我的 web 應用程式購買並設定新的 TLS/SSL 憑證嗎？

若要瞭解如何購買及設定 App Service web 應用程式的 TLS/SSL 憑證，請參閱[將 tls/ssl 憑證新增至您的 App Service 應用程式](configure-ssl-certificate.md)。


## <a name="how-do-i-move-application-insights-resources"></a>如何移動 Application Insights 資源？

目前，Azure Application Insights 並不支援移動作業。 如果您的原始資源群組包含 Application Insights 資源，則您無法移動該資源。 如果您在嘗試移動 App Service 應用程式時包含 Application Insights 資源，則整個移動作業會失敗。 不過，Application Insights 和 App Service 方案不需要位於與應用程式相同的資源群組，應用程式就能正確運作。

如需詳細資訊，請參閱 [App Service 限制](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)。

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>可以在哪裡尋找指引檢查清單及深入了解資源移動作業？

[App Service 限制](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)說明如何將資源移至新的訂用帳戶或相同訂用帳戶中新的資源群組。 您可以取得資源移動檢查清單的相關資訊、深入了解哪些服務支援移動作業，以及深入了解 App Service 限制與其他主題。

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>如何設定 Web 應用程式的伺服器時區？

若要設定 Web 應用程式的伺服器時區：

1. 在 Azure 入口網站的 App Service 訂用帳戶中，移至 [應用程式設定]**** 功能表。
2. 在 [應用程式設定]**** 底下，新增以下設定：
    * 索引鍵 = WEBSITE_TIME_ZONE
    * 值 = 您想要的時區**
3. 選取 [儲存]。

針對在 Windows 上執行的應用程式服務，請參閱[預設時區](https://docs.microsoft.com/windows-hardware/manufacture/desktop/default-time-zones)文章中的 [**時區**] 資料行以取得接受的值。 針對在 Linux 上執行的應用程式服務，將[TZ 資料庫名稱](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)設定為時區值。 以下是一個 TZ 資料庫名稱：北美洲/Adak 的範例。

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>為什麼我的持續 WebJobs 有時候會失敗？

根據預設，Web 應用程式如果閒置一段時間，就會卸載。 此舉有助於系統保留資源。 在「基本」和「標準」方案中，您可以開啟 [永遠開啟]**** 設定，讓 Web 應用程式隨時保持載入。 如果您的 Web 應用程式會執行持續的 WebJobs，就應該開啟 [永遠開啟]****，否則 WebJobs 可能無法可靠地執行。 如需詳細資訊，請參閱[建立持續執行 WebJob](webjobs-create.md#CreateContinuous)。

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>如何取得 Web 應用程式的輸出 IP 位址？

若要取得 Web 應用程式的輸出 IP 位址清單：

1. 在 Azure 入口網站的 Web 應用程式刀鋒視窗中，移至 [屬性]**** 功能表。
2. 搜尋**輸出 IP 位址**。

輸出 IP 位址清單隨即出現。

如果您的網站裝載在 App Service Environment，若要深入了解如何取得輸出 IP 位址，請參閱[輸出網路位址](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses)。

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>如何為 Web 應用程式取得保留或專用輸入 IP 位址？

若要為 Azure 應用程式網站的輸入呼叫設定專用或保留的 IP 位址，請安裝並設定以 IP 為基礎的 TLS/SSL 憑證。

請注意，若要針對輸入呼叫使用專用或保留 IP 位址，您的 App Service 方案必須是「基本」或更高版本的服務方案。

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>是否可以將 App Service 憑證匯出以在 Azure 外部使用，例如在其他位置裝載的網站？ 

是，您可以將它們匯出以在 Azure 外部使用。 如需詳細資訊，請參閱 [App Service 憑證和自訂網域的常見問題集](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview)。

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>是否可以匯出我的 App Service 憑證以與其他 Azure 雲端服務搭配使用？

入口網站提供透過 Azure Key Vault 將 App Service 憑證部署至 App Service 應用程式的頂級體驗。 不過，我們已經收到客戶的要求，想要在 App Service 平台外部使用這些憑證，例如，使用 Azure 虛擬機器。 若要深入了解如何建立 App Service 憑證的本機 PFX 複本，以便與其他 Azure 資源搭配使用憑證，請參閱[建立 App Service 憑證的本機 PFX 複本](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/)。

如需詳細資訊，請參閱 [App Service 憑證和自訂網域的常見問題集](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview)。


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>為什麼當我嘗試備份 Web 應用程式時看到「部分成功」訊息？

備份失敗的常見原因是某些檔案正在由應用程式使用中。 您在執行備份時，使用中的檔案會遭到鎖定。 這樣會避免這些檔案進行備份，因此可能導致「部分成功」狀態。 您也許可以藉由從備份程序排除此檔案來防止這個情形發生。 您可以選擇僅備份需要的項目。 如需詳細資訊，請參閱[僅備份網站與 Azure Web 應用程式的重要部分](https://zainrizvi.io/blog/creating-partial-backups-of-your-site-with-azure-web-apps/)。

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>如何從 HTTP 回應移除標題？

若要移除 HTTP 回應中的標頭，請更新網站的 web.config 檔案。 如需詳細資訊，請參閱[在 Azure 網站上移除標準伺服器標題](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)。

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>App Service 是否符合 PCI 標準 3.0 和 3.1 的規範？

目前，Azure App Service 的 Web 應用程式功能符合 PCI 資料安全標準 (DSS) 版本 3.0 層級 1 的規範。 PCI DSS 3.1 版在我們的藍圖中。 如何持續採用最新標準的規劃已在進行中。

PCI DSS 3.1 版憑證需要停用傳輸層安全性 (TLS) 1.0。 目前，停用 TLS 1.0 不是大部分 App Service 方案的選項。 不過，如果您使用 App Service Environment，或願意將您的工作負載移轉至 App Service Environment，您可以掌握對環境更好的控制。 這牽涉到藉由連絡 Azure 支援人員來停用 TLS 1.0。 在不久的將來，我們計劃讓使用者能夠存取這些設定。

如需詳細資訊，請參閱 [Microsoft Azure App Service Web 應用程式符合 PCI 標準 3.0 和 3.1 的規範](https://support.microsoft.com/help/3124528)。

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>如何使用預備環境和部署位置？

在 [標準] 或 [進階] App Service 方案中，當您將 Web 應用程式部署至 App Service 時，可以部署到個別的部署位置，而不是預設的生產位置。 部署位置是具有專屬主機名稱的即時 Web 應用程式。 兩個部署位置 (包括生產位置) 之間的 Web 應用程式內容與設定項目可以互相交換。

如需有關使用部署位置的詳細資訊，請參閱[在 App Service 中設定預備環境](deploy-staging-slots.md)。

## <a name="how-do-i-access-and-review-webjob-logs"></a>如何存取及檢閱 WebJob 記錄？

若要檢閱 WebJob 記錄：

1. 登入您的**Kudu 網站**（ `https://*yourwebsitename*.scm.azurewebsites.net` ）。
2. 選取 WebJob。
3. 選取 [切換輸出]**** 按鈕。
4. 若要下載輸出檔案，請選取 [下載]**** 連結。
5. 針對個別的執行，選取 [個別叫用]****。
6. 選取 [切換輸出]**** 按鈕。
7. 選取下載連結。

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>我正在嘗試使用混合式連線與 SQL Server。 為什麼會看到「System.OverflowException：	數學運算導致溢位」訊息？

如果您使用混合式連線來存取 SQL Server (2016 年 5 月 10 日的 Microsoft.NET 更新)，可能會導致連線失敗。 您可能會看到下列訊息：

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>解決方案

例外狀況是「混合式連線管理員」問題所造成的，該問題在那之後已修正。 請務必[更新混合式連線管理員](https://go.microsoft.com/fwlink/?LinkID=841308)以解決此問題。

## <a name="how-do-i-add-a-url-rewrite-rule"></a>如何? 新增 URL 重寫規則？

若要新增 URL 重寫規則，請在**wwwroot**資料夾中建立具有相關設定專案的 web.config 檔案。 如需詳細資訊，請參閱[Azure App Services：瞭解 URL 重寫](https://blogs.msdn.microsoft.com/madhurabharadwaj/2018/06/01/azure-app-services-understanding-url-re-write/)。

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>如何控制 App Service 的輸入流量？

在網站層級中，您有兩個選項可以用來控制 App Service 的輸入流量：

* 開啟動態 IP 限制。 若要深入了解如何開啟動態 IP 限制，請參閱 [Azure 網站的 IP 和網域限制](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/)。
* 開啟模組安全性。 若要了解如何開啟模組安全性，請參閱 [Azure 網站上的 ModSecurity Web 應用程式防火牆](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/)。

如果您使用 App Service Environment，您可以使用 [Barracuda 防火牆](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/)。

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>如何封鎖 App Service Web 應用程式中的連接埠？

在 App Service 共用租用戶環境中，因為基礎結構的本質，所以無法封鎖特定連接埠。 TCP 埠4020、4022和4024也可能針對 Visual Studio 遠端偵錯程式開啟。

在 App Service Environment 中，您對於輸入與輸出流量有完整控制權。 您可以使用網路安全性群組來限制或封鎖特定連接埠。 如需 App Service Environment 的詳細資訊，請參閱[App Service Environment 簡介](https://azure.microsoft.com/blog/introducing-app-service-environment/)。

## <a name="how-do-i-capture-an-f12-trace"></a>如何擷取 F12 追蹤？

您有兩個選項可以用來擷取 F12 追蹤：

* F12 HTTP 追蹤
* F12 主控台輸出

### <a name="f12-http-trace"></a>F12 HTTP 追蹤

1. 在 Internet Explorer 中，移至您的網站。 請務必在執行下一個步驟之前登入。 否則，F12 追蹤會擷取敏感性登入資料。
2. 按下 F12。
3. 確認 [網路]**** 索引標籤已選取，然後選取綠色 [播放]**** 按鈕。
4. 執行重現問題的步驟。
5. 選取紅色 [停止]**** 按鈕。
6. 選取 [儲存]**** 鈕 (磁片圖示)，並儲存 HAR 檔案 (在 Internet Explorer 和 Microsoft Edge 中) 或者** 以滑鼠右鍵按一下 HAR 檔案，然後選取 [內容另存為 HAR]**** \(在 Chrome 中)。

### <a name="f12-console-output"></a>F12 主控台輸出

1. 選取 [主控台]**** 索引標籤。
2. 對於包含零個以上項目的每個索引標籤，選取索引標籤 (**錯誤**、**警告**或**資訊**)。 如果未選取索引標籤，則當您將游標移離該索引標籤時，索引標籤圖示會是灰色或黑色。
3. 以滑鼠右鍵按一下窗格的訊息區域，然後選取 [全部複製]****。
4. 在檔案中貼上複製的文字，然後儲存檔案。

若要檢視 HAR 檔案，您可以使用 [HAR 檢視器](http://www.softwareishard.com/har/viewer/)。

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>為什麼當我嘗試將 App Service Web 應用程式連線到虛擬網路 (已連線到 ExpressRoute) 時遇到錯誤？

如果您嘗試將 Azure Web 應用程式連線到已連線至 Azure ExpressRoute 的虛擬網路，就會失敗。 會出現下列訊息：「閘道不是 VPN 閘道」。

目前，您對於已連線至 ExpressRoute 的虛擬網路不能使用點對站 VPN 連線。 點對站 VPN 和 ExpressRoute 不能並存在相同的虛擬網路。 如需詳細資訊，請參閱 [ExpressRoute 和站對站 VPN 連線限制](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations)。

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>如何將 App Service Web 應用程式連線至具有靜態路由 (以原則為基礎) 閘道的虛擬網路？

目前，不支援將 App Service Web 應用程式連線至具有靜態路由 (以原則為基礎) 閘道的虛擬網路。 如果目標虛擬網路已存在，您必須先以動態路由閘道器啟用其點對站 VPN，才能使該虛擬網路與應用程式連線。 如果您的閘道設定為靜態路由，您無法啟用點對站 VPN。 

如需詳細資訊，請參閱[將應用程式與 Azure 虛擬網路整合](web-sites-integrate-with-vnet.md)。

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>為什麼我就算有 2 個可用的背景工作角色，卻還是只能在 App Service Environment 中建立一個 App Service 方案？

為了提供容錯功能，App Service Environment 要求每個背景工作角色集區需要至少一個額外的計算資源。 不能對額外的計算資源指派工作負載。

如需詳細資訊，請參閱[如何建立 App Service Environment](environment/app-service-web-how-to-create-an-app-service-environment.md)。

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>為什麼當我嘗試建立 App Service Environment 時看到逾時？

有時候，建立 App Service Environment 會失敗。 在此情況下，您會在活動記錄中看到下列錯誤：
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period."}}
```

若要解決此問題，請確定下列條件均不成立：
* 子網路太小。
* 子網路並非空白。
* ExpressRoute 阻止 App Service Environment 的網路連線需求。
* 不正確的網路安全性群組會阻止 App Service Environment 的網路連線需求。
* 強制通道已開啟。

如需詳細資訊，請參閱[部署 (建立) 新的 Azure App Service Environment 時最常遇到的問題](https://blogs.msdn.microsoft.com/waws/2016/05/13/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase/)。

## <a name="why-cant-i-delete-my-app-service-plan"></a>為什麼我無法刪除 App Service 方案？

如果任何 App Service 應用程式與 App Service 方案相關聯，您就無法刪除 App Service 方案。 刪除 App Service 方案之前，請從 App Service 方案中移除所有相關聯的 App Service 應用程式。

## <a name="how-do-i-schedule-a-webjob"></a>如何排程 WebJob？

您可以使用 Cron 運算式建立已排程 WebJob：

1. 建立 settings.job 檔案。
2. 在此 JSON 檔案中，使用 Cron 運算式以包含排程屬性： 
    ```json
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

如需已排程 WebJobs 的詳細資訊，請參閱[使用 Cron 運算式來建立已排程 WebJob](webjobs-create.md#CreateScheduledCRON)。

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>如何執行 App Service 應用程式的滲透測試？

若要執行滲透測試，請[提交要求](https://portal.msrc.microsoft.com/engage/pentest)。

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>如何針對使用流量管理員的 App Service Web 應用程式設定自訂網域名稱？

若要深入了解如何搭配使用自訂網域名稱與 App Service 應用程式 (使用 Azure 流量管理員)，以進行負載平衡，請參閱[設定具有流量管理員之 Azure Web 應用程式的自訂網域名稱](configure-domain-traffic-manager.md)。

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>我的 App Service 憑證標示為詐騙。 如何解決這個問題？

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

在 App Service 憑證購買的網域驗證期間，您可能會看到下列訊息：

「您的憑證已標示為可能的詐欺。 要求目前正在進行檢閱。 如果憑證在 24 小時內無法成為可使用，請連絡 Azure 支援」。

此訊息表示，這個詐騙驗證程序可能需要 24 小時才能完成。 在此期間，您將會持續看到訊息。

如果您的 App Service 憑證在 24 小時後持續顯示此訊息，請執行下列 PowerShell 指令碼。 指令碼會直接連絡[憑證提供者](https://www.godaddy.com/)以解決此問題。

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>驗證和授權如何在 App Service 中運作？

如需 App Service 中驗證和授權的詳細文件，請參閱各種身分識別提供者登入的文件：
* [Azure Active Directory](configure-authentication-provider-aad.md)
* [Facebook](configure-authentication-provider-facebook.md)
* [Google](configure-authentication-provider-google.md)
* [Microsoft 帳戶](configure-authentication-provider-microsoft.md)
* [Twitter](configure-authentication-provider-twitter.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>如何將預設 *.azurewebsites.net 網域重新導向至我的 Azure Web 應用程式的自訂網域？

當您使用 Azure 中的 Web 應用程式建立新網站時，預設 sitename**.azurewebsites.net 網域會指派至您的網站。 如果您將自訂主機名稱新增至您的網站，而不想讓使用者能夠存取您的預設 *. azurewebsites.net 網域，您可以重新導向預設 URL。 若要深入了解如何將網站預設網域的所有流量重新導向至自訂網域，請參閱[將預設網域重新導向至 Azure Web 應用程式中的自訂網域](https://zainrizvi.io/blog/block-default-azure-websites-domain/)。

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>如何判斷安裝在 App Service 中的是哪一個版本的 .NET？

尋找 App Service 中已安裝之 Microsoft.NET 版本的最快方式是使用 Kudu 主控台。 您可以從入口網站或使用 App Service 應用程式的 URL 來存取 Kudu 主控台。 如需詳細指示，請參閱[判斷 App Service 中安裝的 .NET 版本](https://blogs.msdn.microsoft.com/waws/2016/11/02/how-to-determine-the-installed-net-version-in-azure-app-services/)。

## <a name="why-isnt-autoscale-working-as-expected"></a>為什麼自動調整無法如預期般運作？

如果 Azure 自動調整尚未如您預期地相應縮小或相應放大 Web 應用程式執行個體，您可能會進入我們故意選擇不調整以避免因為「flapping」而造成無限迴圈的案例。 當相應放大或相應縮小閾值之間沒有足夠邊界時，就會發生這種情形。 若要深入了解如何避免「flapping」以及了解其他自動調整最佳做法，請參閱[自動調整最佳做法](../azure-monitor/platform/autoscale-best-practices.md#autoscale-best-practices)。

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>為什麼自動調整有時候只會部分調整？

當計量超出預先設定的界限時，會觸發自動調整。 有時候，您可能會注意到，相較於您的預期，容量只有部分填滿。 無法使用您想要的執行個體數目時，也可能會發生這個情形。 在該案例中，自動調整會部分填滿可用的執行個體數目。 然後自動調整會執行重新平衡邏輯，以取得更多容量。 它會配置剩餘的執行個體。 請注意，這可能需要幾分鐘的時間。

如果您在幾分鐘之後沒有看到預期的執行個體數目，可能是因為部分填滿已足以攜帶界限內的計量。 或者，自動調整可能已經相應減少，因為它已達到計量下限。

如果這些條件都不適用，而且問題持續發生，請提交支援要求。

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>如何針對內容開啟 HTTP 壓縮？

若要同時對靜態和動態內容類型開啟壓縮，請將下列程式碼新增至應用程式層級的 web.config 檔案：

```xml
<system.webServer>
    <urlCompression doStaticCompression="true" doDynamicCompression="true" />
</system.webServer>
```

您也可以指定您想要壓縮的特定動態和靜態 MIME 類型。 如需詳細資訊，請參閱[簡單 Azure 網站上的 httpCompression 設定](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview)中，我們對於論壇問題的回應。

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>如何從內部部署環境移轉至 App Service？

若要將網站從 Windows 和 Linux 網頁伺服器移轉至 App Service，您可以使用 Azure App Service 移轉小幫手。 移轉工具會視需要在 Azure 中建立 Web 應用程式和資料庫，然後發佈內容。 如需詳細資訊，請參閱 [Azure App Service 移轉小幫手](https://appmigration.microsoft.com/)。
