---
title: 針對 JavaScript web 應用程式的 SDK 載入失敗進行疑難排解-Azure 應用程式見解
description: 如何針對 JavaScript web 應用程式的 SDK 載入失敗進行疑難排解
ms.topic: conceptual
author: MSNev
ms.author: newylie
ms.date: 06/05/2020
ms.custom: devx-track-js
ms.openlocfilehash: 30c7caef4143b1a7cdba959971ff7689f986cb9e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91333251"
---
# <a name="troubleshooting-sdk-load-failure-for-javascript-web-apps"></a>針對 JavaScript web 應用程式的 SDK 載入失敗進行疑難排解

<!-- 
Editor Note: This link name above "SDK Load Failure" has a direct references by https://go.microsoft.com/fwlink/?linkid=2128109 which is embedded in the snippet and from the JavaScript Page. If you change this text you MUST also update these references.
-->

當 JavaScript 程式碼片段偵測到 SDK 腳本無法下載或初始化時，JavaScript 程式碼片段 (v3 或更新版本) 會建立和報告 SDK 載入失敗例外狀況。 有效，您終端使用者的用戶端 (瀏覽器) 無法下載 Application Insights SDK，或從識別的裝載頁面初始化，因此將不會報告任何遙測或事件。

![Azure 入口網站瀏覽器失敗總覽](./media/javascript-sdk-load-failure/overview.png)

> [!NOTE]
> 所有支援提取 ( # A1 API 或 XMLHttpRequest 的主要瀏覽器都支援此例外狀況。 這會排除 IE 8 和以下的內容，因此除非您的環境包含 fetch polyfill) ，否則您將無法從這些瀏覽器 (報告這種類型的例外狀況。

![瀏覽器例外狀況詳細資料](./media/javascript-sdk-load-failure/exception.png)

堆疊詳細資料包含終端使用者使用的 Url 的基本資訊。

| 名稱                      | 說明                                                                                                  |
|---------------------------|--------------------------------------------------------------------------------------------------------------|
| &lt;CDN &nbsp; 端點&gt; | 用來下載 SDK (和失敗) 的 URL。                                                      |
| &lt;說明 &nbsp; 連結&gt;    | 此頁面)  (連結至疑難排解檔的 URL。                                              |
| &lt;主機 &nbsp; URL&gt;     | 使用者使用之頁面的完整 URL。                                                    |
| &lt;端點 &nbsp; URL&gt; | 用來報告例外狀況的 URL，這個值可能有助於識別是否從公用網際網路或私用雲端存取裝載頁面。

發生此例外狀況的最常見原因如下：

- 間歇性網路連接失敗。
- Application Insights CDN 中斷。
- 載入腳本之後，SDK 無法初始化。
- 已封鎖 Application Insights JavaScript CDN。

[間歇網路連線失敗](#intermittent-network-connectivity-failure) 是此例外狀況最常見的原因，尤其是在使用者間歇中斷網路連線的行動漫遊案例中。

下列各節將說明如何針對此錯誤的每個可能根本原因進行疑難排解。

> [!NOTE]
> 有幾個疑難排解步驟假設您的應用程式可以直接控制程式碼片段 &lt; 腳本/ &gt; 標記，以及在裝載 HTML 網頁中傳回的設定。 如果您不這麼做，則您的案例不會套用這些已識別的步驟。

## <a name="intermittent-network-connectivity-failure"></a>間歇性網路連接失敗

**如果使用者遇到間歇性的網路連線失敗，則可能會有較少的解決方案，而且通常會在短時間內自行解決。** 例如，如果使用者重載您的網站 (重新整理頁面) 檔案將 (最後) 下載並在本機快取，直到發行更新的版本為止。

> [!NOTE]
> 如果這個例外狀況是持續性的，而且是在許多使用者之間發生， (由回報的這個例外狀況的快速且持續等級來診斷) 並減少一般用戶端遙測，則間歇性的網路連線問題 _不可能_ 是問題的真正原因，而且您應該繼續使用其他已知的問題進行診斷。

在您自己的 CDN 上裝載 SDK 的這種情況下，不太可能會提供或減少發生此例外狀況，因為您自己的 CDN 會受到相同問題的影響。

透過 NPM 套件解決方案使用 SDK 也是如此。 不過，從使用者的觀點來看，當整個應用程式無法載入/初始化 (（而不 _只_ 是遙測 SDK）時，他們就不會看到視覺效果) ，因此在完全載入之前，它們很可能會重新整理您的網站。

您也可以嘗試使用 [NPM 套件](#use-npm-packages-to-embed-the-application-insight-sdk) 來內嵌 Application Insights SDK。

為了最小化間歇性網路連線失敗，我們已在所有 CDN 檔案上執行了快取控制標頭，如此一來，當使用者的瀏覽器下載目前版本的 SDK 之後，就不需要再次下載，而瀏覽器會重複使用先前取得的複本 (查看快取的 [運作方式](../../cdn/cdn-how-caching-works.md)) 。 如果快取檢查失敗或有新的版本，則使用者的瀏覽器將需要下載更新的版本。 因此，您可能會在檢查失敗案例中看到「 _雜訊_ 」的背景層級，或在發生新的發行時，暫時出現尖峰， (部署至 CDN) 。
 
## <a name="application-insights-cdn-outage"></a>Application Insights CDN 中斷

您可以嘗試直接從瀏覽器存取 CDN 端點，以確認是否有 Application Insights CDN 中斷 (例如，從您自己的 https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js) 開發電腦的不同位置，而不是您的使用者可能會從您自己的開發電腦， (假設您的組織尚未封鎖此網域) 。

如果您確認發生中斷，您可以 [建立新的支援票證](https://azure.microsoft.com/support/create-ticket/) ，或嘗試變更用來下載 SDK 的 URL。

### <a name="change-the-cdn-endpoint"></a>變更 CDN 端點
  
當您的應用程式在每個產生的頁面中傳回程式碼片段及其設定時，您可以變更程式碼片段設定， `src` 以針對 SDK 使用不同的 URL。 藉由使用這種方法，您可以略過 CDN 封鎖的問題，因為不應封鎖新的 URL。

目前的 Application Insights JavaScript SDK CDN 端點
- `https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js`
- `https://js.monitor.azure.com/scripts/b/ai.2.min.js`

> [!NOTE]
> `https://js.monitor.azure.com/`端點是一個別名，可讓我們在大約5分鐘內切換 CDN 提供者，而不需要變更任何設定。這是為了讓我們能夠更快速地修正偵測到的 CDN 相關問題，如果 CDN 提供者有區域或全球問題，而不需要每個人都能調整其設定。

## <a name="sdk-failed-to-initialize-after-loading-the-script"></a>載入腳本後無法初始化 SDK

當 SDK 無法初始化時， &lt; 腳本/ &gt; 已成功從 CDN 下載，但在初始化期間會失敗。 這可能是因為相依性遺失或無效或某種形式的 JavaScript 例外狀況。

要檢查的第一件事是 SDK 是否成功下載，如果未下載腳本，則此案例 __不__ 是 sdk 載入失敗例外狀況的原因。

快速檢查：使用支援開發人員工具的瀏覽器 (F12) ，在 [網路] 索引標籤上進行驗證，程式碼片段設定中定義的腳本 ```src``` 已下載，回應碼為 200 (成功) 或 304 (未變更) 。 您也可以使用 fiddler 之類的工具來檢查網路流量。

下列各節包含不同的報告選項，建議您在 GitHub 上建立支援票證或提出問題。

 基本報告規則：

- 如果問題只會影響少量的使用者，且 (s 的特定或部分瀏覽器版本)  (查看回報之例外狀況的詳細資料) ，則可能是使用者或環境僅有問題，這可能會要求您的應用程式提供額外的執行 `polyfill` 。 針對這些檔，請 [在 GitHub 上提出問題](https://github.com/Microsoft/ApplicationInsights-JS/issues)。
- 如果這個問題影響到您的整個應用程式，且所有使用者都受到影響，則可能是發行相關的問題，因此您應該 [建立新的支援票證](https://azure.microsoft.com/support/create-ticket/)。

### <a name="javascript-exceptions"></a>JavaScript 例外狀況

首先，使用支援開發人員工具的瀏覽器 (F12) 載入頁面，並檢查是否發生任何例外狀況，來檢查 JavaScript 例外狀況。

如果 SDK 腳本中有回報例外狀況 (例如 ai.2.min.js) ，則這可能表示傳入 SDK 的設定包含未預期或遺失的必要設定，或已將錯誤的版本部署至 CDN。

若要檢查是否有錯誤的設定，請變更傳遞至程式碼片段 (的設定（如果尚未) ），讓它只包含您的檢測金鑰做為字串值。

> src： " https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js "，<br />
> cfg： {<br />
> instrumentationKey： "INSTRUMENTATION_KEY"<br />
> }});<br />

如果使用這項最基本設定，您仍然會在 SDK 腳本中看到 JavaScript 例外狀況，請 [建立新的支援票證](https://azure.microsoft.com/support/create-ticket/) ，因為這可能會需要復原錯誤的組建，因為這可能是新部署版本的問題。

如果例外狀況消失，則問題可能是因為類型不符或未預期的值所造成。 開始將您的設定選項逐一備份並進行測試，直到例外狀況再次發生為止。 然後檢查造成問題之專案的檔。 如果檔不清楚或您需要協助，請 [在 GitHub 上提出問題](https://github.com/Microsoft/ApplicationInsights-JS/issues)。

如果您的設定先前已部署且正常運作，但剛開始回報此例外狀況，則可能是新部署版本的問題，請檢查它是否只影響一小部分的使用者/瀏覽器，並 [在 GitHub 上提出問題](https://github.com/Microsoft/ApplicationInsights-JS/issues) 或  [建立新的支援票證](https://azure.microsoft.com/support/create-ticket/)。

### <a name="enable-console-debugging"></a>啟用主控台調試

假設沒有擲回例外狀況，下一步是藉由將設定新增至設定來啟用主控台偵錯工具 `loggingLevelConsole` ，這會將所有初始化錯誤和警告傳送至瀏覽器主控台， (通常可以透過開發人員工具 (F12) # A3。 任何回報的錯誤應該一目了然，如果您需要進一步協助，請 [在 GitHub 上提出問題](https://github.com/Microsoft/ApplicationInsights-JS/issues)。

> cfg： {<br />
> instrumentationKey： "INSTRUMENTATION_KEY"、<br />
> loggingLevelConsole：2<br />
> }});<br />

> [!NOTE]
> 在初始化期間，SDK 會執行一些已知主要相依性的基本檢查。 如果目前的執行時間未提供這些值，則會將失敗的警告訊息回報給主控台，但前提 `loggingLevelConsole` 是大於零。

如果仍無法初始化，請嘗試啟用 ```enableDebug``` 設定。 這會導致將所有內部錯誤擲回為例外狀況 (導致遙測遺失) 。 因為這是開發人員唯一的設定，所以在某些內部檢查中擲回例外狀況可能會有雜訊，因此您必須檢查每個例外狀況，以判斷哪個問題造成 SDK 失敗。 使用非縮減版本的腳本 (記下其 ".js" 底下的擴充功能，而不是 ".min.js" ) 否則例外狀況將無法讀取。

> [!WARNING]
> 這是僅限開發人員設定，不應在完整的生產環境中啟用，因為您將會遺失遙測。

> src： " https://az416426.vo.msecnd.net/scripts/b/ai.2.js "，<br />
> cfg： {<br />
> instrumentationKey： "INSTRUMENTATION_KEY"、<br />
> enableDebug： true<br />
> }});<br />

如果仍未提供任何見解，您應該在 [GitHub 上提出問題](https://github.com/Microsoft/ApplicationInsights-JS/issues) ，並提供詳細資料和範例網站（如果有的話）。 包含瀏覽器版本、作業系統和 JS 架構詳細資料，以協助找出問題。

## <a name="the-application-insights-javascript-cdn-has-been-blocked"></a>已封鎖 Application Insights JavaScript CDN

當 Application Insights 的 JavaScript SDK CDN 端點已回報及/或識別為不安全時，就可以封鎖 CDN。 發生這種情況時，會導致端點公開封鎖，而這些清單的取用者將會開始封鎖所有存取。

若要解決此問題，您必須擁有 CDN 端點的擁有者，才能使用已將端點標示為不安全的區塊清單實體，讓它可以從相關清單中移除。

檢查 CDN 端點是否已識別為不安全。
- [Google 透明度報表](https://transparencyreport.google.com/safe-browsing/search)
- [VirusTotal](https://www.virustotal.com/gui/home/url)
- [Sucuri SiteCheck](https://sitecheck.sucuri.net/)

視應用程式、防火牆或環境更新這些清單的本機複本的頻率而定，使用者或公司 IT 部門可能需要花費相當長的時間，並（或）要求手動介入，才能強制更新或明確允許 CDN 端點解決問題。

如果 CDN 端點識別為不安全，請 [建立支援票證](https://azure.microsoft.com/support/create-ticket/) ，以確保會儘快解決問題。

若 *要* 更快速地略過此問題，您可以 [變更 SDK CDN 端點](#change-the-cdn-endpoint)。

### <a name="application-insights-javascript-cdn-is-blocked-by-end-user---blocked-by-browser-installed-blocker-personal-firewall"></a>使用者封鎖的 Application Insights JavaScript CDN (由瀏覽器封鎖;已安裝封鎖程式;個人防火牆) 

檢查您的終端使用者是否有：

- 已安裝瀏覽器外掛程式 (通常會有某種形式的 ad、惡意程式碼或快顯封鎖程式) 。
- 封鎖 (或不允許在其瀏覽器或 proxy 中) Application Insights CDN 端點。
- 設定防火牆規則，以封鎖 SDK 的 CDN 網域 (或不) 解析 DNS 專案。

如果他們已設定任何這些選項，您將需要使用這些選項 (或提供檔) 以允許 CDN 端點。

其安裝的外掛程式可能會使用公用封鎖清單。 如果不是這種情況，則很可能是其他手動設定的解決方案，或是使用私人網域封鎖清單。

#### <a name="add-exceptions-for-cdn-endpoints"></a>新增 CDN 端點的例外狀況

請與您的使用者合作，或提供檔，通知他們應該將來自 Application Insights CDN 端點的腳本納入下載，方法是將它們包含在瀏覽器的外掛程式或防火牆規則例外清單中， (會根據使用者的環境) 而有所不同。

以下範例說明如何設定 [Chrome 以允許或封鎖對網站的存取。](https://support.google.com/chrome/a/answer/7532419?hl=en)

### <a name="application-insights-cdn-is-blocked-by-corporate-firewall"></a>公司防火牆會封鎖 Application Insights CDN () 

如果您的使用者是在公司網路上，那麼他們最有可能的是防火牆解決方案，而且其 IT 部門已實行某種形式的網際網路篩選系統。 在此情況下，您將需要使用它們來允許使用者所需的規則。

#### <a name="add-exceptions-for-cdn-endpoints-for-corporations"></a>為公司的 CDN 端點新增例外狀況

  這類似于為 [終端使用者新增例外](#add-exceptions-for-cdn-endpoints)狀況，但您必須與公司的 IT 部門合作，讓他們設定要下載的 Application Insights CDN 端點，方法是在任何網域封鎖清單或允許清單服務中包含 (或移除) 它們。

  > [!WARNING]
  > 如果您的公司使用者使用 [私用雲端](https://azure.microsoft.com/overview/what-is-a-private-cloud/) ，且他們無法啟用任何形式的例外狀況來為其內部使用者提供 cdn 端點的公用存取權，則您將需要使用 [Application Insights NPM 套件](https://www.npmjs.com/package/applicationinsights) ，或在您自己的 CDN 上裝載 Application Insights SDK。  

### <a name="additional-troubleshooting-for-blocked-cdn"></a>封鎖 CDN 的其他疑難排解

> [!NOTE]
> 如果您的使用者使用 [私用雲端](https://azure.microsoft.com/overview/what-is-a-private-cloud/) ，且他們沒有公用網際網路的存取權，您將需要在您自己的 CDN 上裝載 SDK，或使用 NPM 套件。

#### <a name="host-the-sdk-on-your-own-cdn"></a>在您自己的 CDN 上裝載 SDK

 您可以從您自己的 CDN 端點裝載 Application Insights SDK，而不是終端使用者從公用 CDN 下載 Application Insights SDK。 建議您使用特定版本 (的 ai. #. # .min.js) ，以便更輕鬆地識別您所使用的版本。 也請定期更新 ( # A0) 的最新版本，讓您可以利用任何 bug 修正和可用的新功能。

#### <a name="use-npm-packages-to-embed-the-application-insight-sdk"></a>使用 NPM 套件內嵌應用程式深入解析 SDK

您可以使用 [NPM 套件](https://www.npmjs.com/package/applicationinsights) 將 SDK 納入您自己的 JavaScript 檔案中，而不是使用程式碼片段和公用 CDN 端點。 SDK 在您自己的腳本內只會變成另一個套件。

> [!NOTE]
> 建議您在使用 NPM 套件時，也應該使用某種形式的 [JavaScript 搭配程式](https://www.bing.com/search?q=javascript+bundler) 來協助進行程式碼分割和縮制。

如同程式碼片段，您的腳本也有可能 (使用 SDK NPM 套件，) 可能會受到此處所列的相同封鎖問題影響，因此，視您的應用程式、使用者和架構而定，您可能會想要考慮採用類似于程式碼片段中的邏輯來偵測和報告這些問題的內容。


## <a name="next-steps"></a>後續步驟 
- [在 GitHub 上提出問題以取得其他協助](https://github.com/Microsoft/ApplicationInsights-JS/issues)
- [監視網頁使用狀況](javascript.md)
