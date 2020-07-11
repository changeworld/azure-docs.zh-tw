---
title: 疑難排解 JavaScript web 應用程式的 SDK 載入失敗-Azure 應用程式深入解析
description: 如何針對 JavaScript web 應用程式的 SDK 載入失敗進行疑難排解
ms.topic: conceptual
author: MSNev
ms.author: newylie
ms.date: 06/05/2020
ms.openlocfilehash: dae6b40e7ec8a2bb6f635a6ffca4886ed09c1364
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2020
ms.locfileid: "86229528"
---
# <a name="troubleshooting-sdk-load-failure-for-javascript-web-apps"></a>針對 JavaScript web 應用程式的 SDK 載入失敗進行疑難排解

<!-- 
Editor Note: This link name above "SDK Load Failure" has a direct references by https://go.microsoft.com/fwlink/?linkid=2128109 which is embedded in the snippet and from the JavaScript Page. If you change this text you MUST also update these references.
-->

當 JavaScript 程式碼片段 (v3 或更新版本) 偵測到 SDK 腳本無法下載或初始化時，會建立並回報 SDK 載入失敗例外狀況。 有效，使用者的用戶端 (瀏覽器) 無法下載 Application Insights SDK，或從識別的裝載頁面初始化，因此不會報告任何遙測或事件。

![Azure 入口網站瀏覽器失敗總覽](./media/javascript-sdk-load-failure/overview.png)

> [!NOTE]
> 所有支援 fetch ( # A1 API 或 XMLHttpRequest 的主要瀏覽器都支援此例外狀況。 這會排除 IE 8 和以下的內容，因此除非您的環境包含 fetch polyfill) ，否則不會從這些瀏覽器回報這類例外狀況 (。

![瀏覽器例外狀況詳細資料](./media/javascript-sdk-load-failure/exception.png)

堆疊詳細資料包含使用者所使用之 Url 的基本資訊。

| 名稱                      | 描述                                                                                                  |
|---------------------------|--------------------------------------------------------------------------------------------------------------|
| &lt;CDN &nbsp; 端點&gt; | 用來下載 SDK (和失敗) 的 URL。                                                      |
| &lt;說明 &nbsp; 連結&gt;    |  (此頁面) 連結到疑難排解檔的 URL。                                              |
| &lt;主機 &nbsp; URL&gt;     | 使用者所使用之網頁的完整 URL。                                                    |
| &lt;端點 &nbsp; URL&gt; | 用來報告例外狀況的 URL，這個值可能有助於識別是否從公用網際網路或私人雲端存取裝載頁面。

發生此例外狀況的最常見原因如下：

- 間歇網路連線失敗。
- Application Insights CDN 中斷。
- SDK 在載入腳本之後無法初始化。
- 已封鎖 Application Insights JavaScript CDN。

[間歇網路連線失敗](#intermittent-network-connectivity-failure)是此例外狀況最常見的原因，特別是在行動漫遊案例中，使用者會偶爾中斷網路連線。

下列各節將說明如何針對此錯誤的每個可能根本原因進行疑難排解。

> [!NOTE]
> 其中幾個疑難排解步驟假設您的應用程式可以直接控制程式碼片段 &lt; 腳本/ &gt; 標記，並將它的設定當做裝載 HTML 網頁的一部分傳回。 如果您不這樣做，這些已識別的步驟將不適用您的案例。

## <a name="intermittent-network-connectivity-failure"></a>間歇網路連線失敗

**如果使用者遇到間歇性的網路連線失敗，則可能的解決方案較少，而且通常會在短時間內自行解決。** 例如，如果使用者重載您的網站 (重新整理頁面) 檔案最後會 () 下載並在本機快取，直到發行更新的版本為止。

> [!NOTE]
> 如果這個例外狀況持續存在，而且在許多使用者的情況下， (診斷出此) 例外狀況的快速且持續性層級，並減少一般用戶端遙測，則間歇性的網路連線問題_不可能_是問題的真正原因，而且您應該繼續診斷其他已知的可能問題。

在這種情況下，在您的 CDN 上裝載 SDK 不太可能提供或減少此例外狀況的發生次數，因為您自己的 CDN 會受到相同的問題影響。

透過 NPM 套件解決方案使用 SDK 也是如此。 不過，從使用者的觀點來看，當這種情況發生時，您的整個應用程式無法載入/初始化 (，而不_只是_遙測) SDK，而是在完全載入之前就重新整理您的網站。

您也可以嘗試使用[NPM 封裝](#use-npm-packages-to-embed-the-application-insight-sdk)來內嵌 Application Insights SDK。

為了盡可能減少間歇性的網路連線失敗，我們已在所有 CDN 檔案上執行快取控制標頭，因此，一旦使用者的瀏覽器下載目前版本的 SDK 之後，瀏覽器將不需要再次下載，而且瀏覽器將會重複使用先前取得的複本 (查看快取的[運作方式](https://docs.microsoft.com/azure/cdn/cdn-how-caching-works)) 。 如果快取檢查失敗或已有新的版本，則使用者的瀏覽器將需要下載更新的版本。 因此，您可能會在檢查失敗案例中看到「_雜訊_」的背景層級，或在新發行發生時，暫時尖峰， (部署到 CDN) 。
 
## <a name="application-insights-cdn-outage"></a>Application Insights CDN 中斷

您可以嘗試直接從瀏覽器存取 CDN 端點，以確認是否有 Application Insights 的 CDN 中斷 (例如， https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js) 從與您的使用者可能來自您自己的開發電腦不同的位置， (假設貴組織尚未封鎖此網域) 。

如果您確認中斷，您可以[建立新的支援票證](https://azure.microsoft.com/support/create-ticket/)，或嘗試變更用來下載 SDK 的 URL。

### <a name="change-the-cdn-endpoint"></a>變更 CDN 端點
  
當您的應用程式在每個產生的頁面中傳回程式碼片段和其設定時，您可以變更程式碼片段設定， `src` 以針對 SDK 使用不同的 URL。 藉由使用這種方法，您可以略過 CDN 封鎖的問題，因為不應封鎖新的 URL。

目前 Application Insights JavaScript SDK CDN 端點
- `https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js`
- `https://js.monitor.azure.com/scripts/b/ai.2.min.js`

> [!NOTE]
> `https://js.monitor.azure.com/`端點是一個別名，可讓我們在大約5分鐘內于 CDN 提供者之間切換，而不需要變更任何設定。這可讓我們在 CDN 提供者有區域或全域問題，而不需要每個人調整其設定的情況下，更快速地修正偵測到的 CDN 相關問題。

## <a name="sdk-failed-to-initialize-after-loading-the-script"></a>SDK 在載入腳本之後無法初始化

當 SDK 無法初始化時， &lt; 腳本/ &gt; 已成功從 CDN 下載，但在初始化期間失敗。 這可能是因為相依性遺失或無效，或某種形式的 JavaScript 例外狀況。

要檢查的第一件事是 SDK 是否已成功下載，如果未下載腳本，則此案例__不__是 sdk 載入失敗例外狀況的原因。

快速檢查：使用支援開發人員工具 (F12) 的瀏覽器，在 [網路] 索引標籤上進行驗證，其程式碼片段設定中定義的腳本 ```src``` 已下載，回應碼為 200 (成功) 或 304 (未變更) 。 您也可以使用 fiddler 之類的工具來檢查網路流量。

下列各節包含不同的報告選項，它會建議在 GitHub 上建立支援票證或提出問題。

 基本報告規則：

- 如果問題只會影響少數使用者，且特定或子集的瀏覽器版本 (s)  (檢查回報的例外狀況) 的詳細資料，則可能是使用者或僅限環境的問題，這可能會要求您的應用程式提供額外的執行 `polyfill` 。 為此，請[在 GitHub 上提出問題](https://github.com/Microsoft/ApplicationInsights-JS/issues)。
- 如果此問題影響到您的整個應用程式，且您的所有使用者都受到影響，則可能是發行相關的問題，因此您應該[建立新的支援票證](https://azure.microsoft.com/support/create-ticket/)。

### <a name="javascript-exceptions"></a>JavaScript 例外狀況

首先，我們會使用支援開發人員工具 (F12) 載入頁面並檢查是否發生任何例外狀況的瀏覽器，來檢查 JavaScript 例外狀況。

如果 SDK 腳本中有回報例外狀況 (例如 ai.2.min.js) ，這可能表示傳入 SDK 的設定包含未預期或遺失的必要設定，或已將錯誤版本部署至 CDN。

若要檢查是否有錯誤的設定，請變更傳遞至程式碼片段的設定 (如果尚未) ，使其只包含您的檢測金鑰做為字串值。

> src： " https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js "，<br />
> cfg： {<br />
> instrumentationKey： "INSTRUMENTATION_KEY"<br />
> }});<br />

如果使用這項最小設定時，您仍然會在 SDK 腳本中看到 JavaScript 例外狀況，請[建立新的支援票證](https://azure.microsoft.com/support/create-ticket/)，因為這將需要復原錯誤的組建，因為這可能是新部署版本的問題。

如果例外狀況消失，則問題可能是因為類型不符或非預期的值所造成。 開始逐一新增您的設定選項，並進行測試，直到例外狀況再次發生為止。 然後查看造成問題之專案的檔。 如果檔不清楚或您需要協助，請[在 GitHub 上提出問題](https://github.com/Microsoft/ApplicationInsights-JS/issues)。

如果您的設定先前已部署且正常運作，但剛開始回報此例外狀況，則可能是新部署版本的問題，請檢查它是否只影響一小部分的使用者/瀏覽器，並[在 GitHub 上提出問題](https://github.com/Microsoft/ApplicationInsights-JS/issues)，或[建立新的支援票證](https://azure.microsoft.com/support/create-ticket/)。

### <a name="enable-console-debugging"></a>啟用主控台的調試

假設沒有擲回例外狀況，則下一個步驟是藉由將設定新增至設定來啟用主控台的偵錯工具 `loggingLevelConsole` ，這會將所有初始化錯誤和警告傳送至瀏覽器主控台， (通常可透過開發人員工具 (F12) # A3 取得。 任何報告的錯誤應該一目了然，如果您需要進一步協助，請[在 GitHub 上提出問題](https://github.com/Microsoft/ApplicationInsights-JS/issues)。

> cfg： {<br />
> instrumentationKey： "INSTRUMENTATION_KEY"，<br />
> loggingLevelConsole：2<br />
> }});<br />

> [!NOTE]
> 在初始化期間，SDK 會針對已知的主要相依性執行一些基本檢查。 如果這些不是由目前的執行時間所提供，則會將失敗回報為主控台的警告訊息，但只有在大於零的情況下才會報告 `loggingLevelConsole` 。

如果仍然無法初始化，請嘗試啟用 ```enableDebug``` 設定。 這會導致所有內部錯誤擲回為例外狀況 (這會導致) 遺失遙測。 因為這是開發人員的唯一設定，所以在某些內部檢查中擲回例外狀況時，可能會收到雜訊，因此您必須檢查每個例外狀況，以判斷哪個問題造成 SDK 失敗。 使用非縮減版本的腳本 (注意其 ".js" 底下的延伸模組，而不是 ".min.js" ) 否則例外狀況將無法讀取。

> [!WARNING]
> 這是僅限開發人員的設定，不應在完整的生產環境中啟用，因為您將會遺失遙測。

> src： " https://az416426.vo.msecnd.net/scripts/b/ai.2.js "，<br />
> cfg： {<br />
> instrumentationKey： "INSTRUMENTATION_KEY"，<br />
> enableDebug： true<br />
> }});<br />

如果仍然未提供任何見解，您應該在[GitHub 上提出問題](https://github.com/Microsoft/ApplicationInsights-JS/issues)，其中包含詳細資料和範例網站（如果有的話）。 包含瀏覽器版本、作業系統和 JS 架構詳細資料，以協助找出問題。

## <a name="the-application-insights-javascript-cdn-has-been-blocked"></a>已封鎖 Application Insights JavaScript CDN

當 Application Insights JavaScript SDK CDN 端點已回報及（或）識別為不安全時，可能會封鎖該 CDN。 發生這種情況時，會導致端點公開封鎖列出，而這些清單的取用者將開始封鎖所有存取。

若要解決此問題，必須要有 CDN 端點的擁有者，才能使用已將端點標示為不安全的封鎖清單實體，讓它可以從相關清單中移除。

檢查 CDN 端點是否已識別為不安全。
- [Google 透明度報表](https://transparencyreport.google.com/safe-browsing/search)
- [VirusTotal](https://www.virustotal.com/gui/home/url)
- [Sucuri SiteCheck](https://sitecheck.sucuri.net/)

根據應用程式、防火牆或環境更新這些清單本機複本的頻率，使用者或公司的 IT 部門可能需要花費相當長的時間，並（或）需要手動介入，才能強制更新或明確允許 CDN 端點解決此問題。

如果將 CDN 端點識別為不安全，請[建立支援票證](https://azure.microsoft.com/support/create-ticket/)，以確保儘快解決問題。

若*要*更快速地略過這個問題，您可以[變更 SDK CDN 端點](#change-the-cdn-endpoint)。

### <a name="application-insights-javascript-cdn-is-blocked-by-end-user---blocked-by-browser-installed-blocker-personal-firewall"></a>使用者封鎖的 Application Insights JavaScript CDN (由瀏覽器封鎖;已安裝封鎖程式;個人防火牆) 

檢查您的終端使用者是否具備：

- 安裝瀏覽器外掛程式 (通常會) 某種形式的 ad、惡意程式碼或快顯封鎖程式。
- 封鎖 (或不允許在其瀏覽器或 proxy 中) Application Insights CDN 端點。
- 已設定防火牆規則，使 SDK 的 CDN 網域遭到封鎖 (或無法解析) 的 DNS 專案。

如果他們已設定其中任何選項，您將需要使用它們 (或提供檔) 以允許 CDN 端點。

其已安裝的外掛程式可能會使用公用封鎖清單。 如果不是這種情況，則很可能是其他手動設定的解決方案，或其使用的是私人網域封鎖清單。

#### <a name="add-exceptions-for-cdn-endpoints"></a>新增 CDN 端點的例外狀況

請與您的使用者合作，或提供檔通知他們，他們應該在瀏覽器的外掛程式或防火牆規則例外清單中包含腳本，以允許下載 Application Insights 的 CDN 端點， (會根據使用者的環境) 而有所不同。

以下範例說明如何設定[Chrome 來允許或封鎖網站的存取。](https://support.google.com/chrome/a/answer/7532419?hl=en)

### <a name="application-insights-cdn-is-blocked-by-corporate-firewall"></a>公司防火牆會封鎖 Application Insights CDN () 

如果您的使用者是在公司網路上，則最有可能是他們的防火牆解決方案，而且其 IT 部門已實作為某種形式的網際網路篩選系統。 在此情況下，您將需要使用它們來允許您的終端使用者所需的規則。

#### <a name="add-exceptions-for-cdn-endpoints-for-corporations"></a>為公司的 CDN 端點新增例外狀況

  這類似于為[終端使用者新增例外](#add-exceptions-for-cdn-endpoints)狀況，但您必須與公司的 IT 部門合作，讓他們設定要下載的 Application Insights CDN 端點，包括 (或移除任何網域封鎖清單或允許清單服務中的) 。

  > [!WARNING]
  > 如果您的公司使用者使用[私人雲端](https://azure.microsoft.com/overview/what-is-a-private-cloud/)，而他們無法啟用任何形式的例外狀況來為其內部使用者提供 CDN 端點的公用存取權，則您將需要使用[Application Insights NPM 套件](https://www.npmjs.com/package/applicationinsights)，或在您自己的 CDN 上裝載 Application Insights SDK。  

### <a name="additional-troubleshooting-for-blocked-cdn"></a>封鎖 CDN 的其他疑難排解

> [!NOTE]
> 如果您的使用者使用[私人雲端](https://azure.microsoft.com/overview/what-is-a-private-cloud/)，但他們無法存取公用網際網路，您就必須在自己的 CDN 上裝載 SDK，或使用 NPM 套件。

#### <a name="host-the-sdk-on-your-own-cdn"></a>在您自己的 CDN 上裝載 SDK

 您可以從您自己的 CDN 端點裝載 Application Insights SDK，而不是您的終端使用者從公用 CDN 下載 Application Insights SDK。 建議您使用特定版本 (ai. #. # .min.js) ，以更輕鬆地識別您所使用的版本。 也請定期將其更新為目前的版本 ( # A0) ，讓您可以利用任何錯誤修正和可用的新功能。

#### <a name="use-npm-packages-to-embed-the-application-insight-sdk"></a>使用 NPM 套件來內嵌應用程式深入解析 SDK

與其使用程式碼片段和公用 CDN 端點，您可以使用[NPM 套件](https://www.npmjs.com/package/applicationinsights)來包含 SDK，做為您自己的 JavaScript 檔案的一部分。 SDK 只會成為您自己的腳本內的另一個套件。

> [!NOTE]
> 建議使用 NPM 封裝時，您也應該使用某種形式的[JavaScript 搭配程式](https://www.bing.com/search?q=javascript+bundler)來協助程式碼分割和縮制。

如同此程式碼片段，您自己的腳本 (，或不使用 SDK NPM 套件) 可能會受到此處所列的相同封鎖問題影響，因此，根據您的應用程式、使用者和架構，您可能會想要考慮執行類似于程式碼片段中的邏輯，以偵測並回報這些問題。


## <a name="next-steps"></a>後續步驟 
- [在 GitHub 上提出問題以取得其他協助](https://github.com/Microsoft/ApplicationInsights-JS/issues)
- [監視網頁使用狀況](javascript.md)