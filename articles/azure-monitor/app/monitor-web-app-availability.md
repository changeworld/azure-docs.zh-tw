---
title: 監視任何網站的可用性和回應性 | Microsoft Docs
description: 在 Application Insights 中設定 Web 測試。 如果網站無法使用或回應緩慢，將收到警示。
ms.topic: conceptual
ms.date: 09/16/2019
ms.reviewer: sdash
ms.openlocfilehash: 61358051a8ddc32bc01ec5e231f4c28ebfa18ee0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77670027"
---
# <a name="monitor-the-availability-of-any-website"></a>監視任何網站的可用性

在您部署 web 應用程式/網站之後，您可以設定週期性測試來監視可用性和回應性。 [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) 會將來自全球各地的 Web 要求定期傳送給您的應用程式。 如果您的應用程式沒有回應，或回應太慢，它會發出警示。

您可以為公用網際網路可存取的任何 HTTP 或 HTTPS 端點設定可用性測試。 您不需要對正在測試的網站進行任何變更。 事實上，它甚至不需要是您擁有的網站。 您可以測試服務所依存之 REST API 的可用性。

### <a name="types-of-availability-tests"></a>可用性測試的類型：

可用性測試有三種類型：

* [URL Ping 測試](#create-a-url-ping-test)：您可以在 Azure 入口網站中建立的簡單測試。
* [多步驟 web 測試](availability-multistep.md)：一系列 web 要求的記錄，可以回頭播放以測試更複雜的案例。 多步驟 web 測試會在 Visual Studio Enterprise 中建立，並上傳至入口網站進行執行。
* [自訂追蹤可用性測試](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability?view=azure-dotnet)：如果您決定要建立自訂應用程式來執行可用性測試`TrackAvailability()` ，則可以使用方法，將結果傳送至 Application Insights。

**您最多可以為每個 Application Insights 資源建立100可用性測試。**

## <a name="create-an-application-insights-resource"></a>建立 Application Insights 資源

為了建立可用性測試，您必須先建立 Application Insights 資源。 如果您已經建立資源，請繼續進行下一節，以[建立 URL Ping 測試](#create-a-url-ping-test)。

從 [Azure 入口網站中，選取 [**建立資源** > ] [**開發人員工具** > ] [**Application Insights** ]，然後[建立 Application Insights 資源](create-new-resource.md)。

## <a name="create-a-url-ping-test"></a>建立 URL Ping 測試

「URL ping 測試」這個名稱有點 misnomer。 為了清楚說明，這項測試不會使用 ICMP （網際網路控制訊息通訊協定）來檢查網站的可用性。 相反地，它會使用更先進的 HTTP 要求功能來驗證端點是否有回應。 它也會測量與該回應相關聯的效能，並加入設定自訂成功準則的功能，以及更多的高階功能，例如剖析相依要求，並允許重試。

若要建立您的第一個可用性要求，請開啟 [可用性] 窗格，然後選取 [**建立測試**]。

![Fill at least the URL of your website](./media/monitor-web-app-availability/availability-create-test-001.png)

### <a name="create-a-test"></a>建立測試

|設定| 說明
|----|----|----|
|**URL** |  URL 可以是您想要測試的任何網頁，但必須可從公用網際網路看見它。 URL 可以包含查詢字串。 例如，您可以訓練一下您的資料庫。 如果 URL 解析為重新導向，我們會跟隨它，最多 10 個重新導向。|
|**剖析相依要求**| 測試要求的影像、腳本、樣式檔案以及其他屬於受測網頁的檔案。 記錄的回應時間包含取得這些檔案所需的時間。 如果無法在整個測試的超時時間內成功下載任何這些資源，測試就會失敗。 如果未核取這個選項，測試只會要求您指定之 URL 中的檔案。 啟用此選項會產生更嚴格的檢查。 案例的測試可能會失敗，這在手動流覽網站時可能不明顯。
|**啟用重試**|當測試失敗時，就會在短時間後重試。 只有在連續三次重試失敗後，才會回報失敗。 後續測試則會以一般測試頻率執行。 重試會暫時停止，直到下次成功為止。 此規則可個別套用在每個測試位置。 **我們建議您選擇此選項**。 平均來說，大約 80% 失敗會在重試後消失。|
|**測試頻率**| 設定從每個測試位置執行測試的頻率。 預設頻率為 5 分鐘且有五個測試位置，則您的網站平均每一分鐘會執行測試。|
|**測試位置**| 這是我們的伺服器將 web 要求傳送至您的 URL 的位置。 **建議的測試位置數目下限為 5** ，以確保您可以區分網站中的問題與網路問題。 您最多可以選取 16 個位置。

**如果您的 URL 在公用網際網路中看不到，您可以選擇選擇性地開啟防火牆，只允許透過的測試交易**。 若要深入瞭解可用性測試代理程式的防火牆例外狀況，請參閱[IP 位址指南](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses#availability-tests)。

> [!NOTE]
> 我們強烈建議您從**最少五個位置**的多個位置進行測試。 這樣做可避免因特定位置發生暫時性問題，而造成誤判。 此外，我們也發現最佳設定是讓**測試位置的數目等於警示位置閾值 + 2**。

### <a name="success-criteria"></a>成功準則

|設定| 說明
|----|----|----|
| **測試超時** |降低此值可收到有關回應變慢的警示。 如果未在這段時間內收到您網站的回應，則測試會視為失敗。 如果已選取 [剖析相依要求] ****，則必須在這段時間內收到所有映像、樣式檔、指令碼和其他相依資源。|
| **HTTP 回應** | 視為成功的傳回狀態碼。 200 是表示已傳回標準 Web 網頁的代碼。|
| **內容相符** | 字串，例如「歡迎！」 我們會測試每個回應中的區分大小寫完全相符。 必須是單純字串，不含萬用字元。 別忘了，如果頁面內容變更，則可能需要更新。 **內容相符僅支援英文字元** |

### <a name="alerts"></a>警示

|設定| 說明
|----|----|----|
|**近乎即時（預覽）** | 我們建議使用近乎即時的警示。 設定此類型的警示會在您的可用性測試建立後完成。  |
|**傳統** | 我們不再建議使用傳統警示來進行新的可用性測試。|
|**警示位置閾值**|建議至少為位置數的 3/5。 警示位置閾值與測試位置數目之間的最佳關聯性是測試位置的**警示位置閾值** = （**2），最少五個測試位置。**|

## <a name="see-your-availability-test-results"></a>查看可用性測試結果

您可以使用折線圖和散佈圖視圖來視覺化可用性測試結果。

幾分鐘後 **，按一下 [** 重新整理] 以查看您的測試結果。

![程式行檢視](./media/monitor-web-app-availability/availability-refresh-002.png)

散佈圖視圖會顯示測試結果的樣本，其中包含診斷測試步驟的詳細資料。 測試引擎會儲存失敗測試的診斷詳細資料。 對於成功的測試，系統會儲存執行子集的診斷詳細資料。 將滑鼠停留在任何綠色/紅色點上，以查看測試、測試名稱和位置。

![程式行檢視](./media/monitor-web-app-availability/availability-scatter-plot-003.png)

選取特定測試、位置，或縮短時間週期，以查看更多有關感興趣時間週期的結果。 使用 [搜尋總管] 以查看所有執行的結果，或使用分析查詢對此資料執行自訂報告。

## <a name="inspect-and-edit-tests"></a> 檢查和編輯測試

若要編輯、暫時停用或刪除測試，請按一下測試名稱旁的省略號。 設定變更可能需要20分鐘的時間，才能在進行變更之後傳播到所有測試代理程式。

![查看測試詳細資料。 編輯和停用 web 測試](./media/monitor-web-app-availability/edit.png)

當您對服務執行維護時，您可能會想要停用可用性測試或與其相關聯的警示規則。

## <a name="if-you-see-failures"></a>如果您看到失敗

按一下一個紅點。

![按一下一個紅點](./media/monitor-web-app-availability/open-instance-3.png)

從可用性測試結果中，您可以看到所有元件的交易詳細資料。 您可以在其中：

* 檢查從伺服器收到的回應。
* 使用在處理失敗的可用性測試時收集的相關聯伺服器端遙測來診斷失敗。
* 在 Git 或 Azure Boards 中記錄問題或工作項目來追蹤問題。 Bug 將包含此事件的連結。
* 在 Visual Studio 中開啟 Web 測試結果。

在[這裡](../../azure-monitor/app/transaction-diagnostics.md)深入了解端對端交易診斷體驗。

按一下例外狀況資料列，以查看造成綜合可用性測試失敗的伺服器端例外狀況的詳細資料。 您也可以取得[偵錯快照集](../../azure-monitor/app/snapshot-debugger.md)進行更多樣化的程式碼層級診斷。

![伺服器端診斷](./media/monitor-web-app-availability/open-instance-4.png)

除了原始的結果，您也可以在[計量瀏覽器](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)中查看兩個主要可用性計量：

1. 可用性︰所有測試執行中測試成功的百分比。
2. 測試持續期間︰所有測試執行中的平均測試持續期間。

## <a name="automation"></a>自動化

* [使用 PowerShell 指令碼自動設定可用性測試](../../azure-monitor/app/powershell.md#add-an-availability-test)。
* 設定會在產生警示時呼叫的 [webhook](../../azure-monitor/platform/alerts-webhooks.md)。

## <a name="troubleshooting"></a>疑難排解

專屬的[疑難排解文章](troubleshoot-availability.md)。

## <a name="next-steps"></a>後續步驟

* [可用性警示](availability-alerts.md)
* [多重步驟 Web 測試](availability-multistep.md)


