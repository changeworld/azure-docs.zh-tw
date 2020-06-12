---
title: 使用 Azure Application Insights 設定 ASP.NET 的 Web 應用程式分析 | Microsoft Docs
description: 針對裝載在內部部署環境或 Azure 的 ASP.NET 網站，設定效能、可用性及使用者行為分析工具。
ms.topic: conceptual
ms.date: 05/08/2019
ms.openlocfilehash: aa4449a0229ba156dbeed37c10ed21f8b1b5f201
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83698493"
---
# <a name="set-up-application-insights-for-your-aspnet-website"></a>設定 ASP.NET 網站的 Application Insights

此程序會設定 ASP.NET web 應用程式以將遙測傳送至 [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) 服務。 這適用於在您擁有的內部部署 IIS 伺服器或雲端中託管的 ASP.NET 應用程式。 取得圖表和功能強大的查詢語言可幫助您了解您應用程式的效能以及人員如何使用它，再加上發生失敗或效能問題時的自動警示。 許多開發人員發現這些功能本身就很棒，但是您也可以視需要擴充和自訂遙測。

在 Visual Studio 中只需按幾下滑鼠即可進行安裝。 您可以選擇限制遙測的磁碟區來避免產生費用。 此功能可讓您實驗和偵錯，或監視沒有很多使用者的網站。 當您決定要繼續監視您的生產網站時，很容易在稍後提升限制。

## <a name="prerequisites"></a>Prerequisites
若要將 Application Insights 新增至您的 ASP.NET 網站，您必須：

- 使用下列工作負載，安裝 [Visual Studio 2019 for Windows](https://www.visualstudio.com/downloads/)：
    - ASP.NET 和 Web 開發 (請不要取消核取選用元件)
    - Azure 開發

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="step-1-add-the-application-insights-sdk"></a><a name="ide"></a> 步驟 1：新增 Application Insights SDK

> [!IMPORTANT]
> 此範例中的螢幕擷取畫面是以 Visual Studio 2017 版本 15.9.9 版和更新版本為基礎。 新增 Application Insights 的體驗會因各種 Visual Studio 版本以及 ASP.NET 範本類型而有所不同。 舊版可能會有替代文字，例如「設定 Application Insights」。

在 [方案總管] 中以滑鼠右鍵按一下 Web 應用程式名稱，然後選擇 [新增] > [Application Insights 遙測]

![醒目提示 [設定 Application Insights] 的 [方案總管] 螢幕擷取畫面](./media/asp-net/add-telemetry-new.png)

(根據您的 Application Insights SDK 版本，系統可能會提示您升級至最新的 SDK 版本。 如果出現提示，請選取 [更新 SDK]。)

![螢幕擷取畫面：有新版的 Microsoft Application Insights SDK 可供使用。 醒目提示的更新 SDK](./media/asp-net/0002-update-sdk.png)

Application Insights 設定畫面：

選取 **[馬上開始]** 。

![[向 Application Insights 註冊您的應用程式] 頁面的螢幕擷取畫面](./media/asp-net/00004-start-free.png)

如果您想要設定資源群組，或資料的儲存位置，請按一下 [進行設定]。 資源群組用來控制資料的存取。 例如，如果您有數個應用程式組成相同系統時，您可能會將其 Application Insights 資料放在相同的資源群組中。

 選取 [註冊]。

![[向 Application Insights 註冊您的應用程式] 頁面的螢幕擷取畫面](./media/asp-net/00005-register-ed.png)

 選取 [專案] > [管理 NuGet 套件] > [套件來源: nuget.org] > [確認]，以確認您有最新穩定版本的 Application Insights SDK。

 在偵錯期間和您發佈應用程式之後，遙測會傳送至 [Azure 入口網站](https://portal.azure.com)。
> [!NOTE]
> 如果您不想在您偵錯時將遙測傳送到入口網站，而只將 Application Insights SDK 新增至您的應用程式，但不在入口網站中設定資源。 您在偵錯時可在 Visual Studio 中檢視遙測。 稍後，您可以回到此設定頁面，或等到部署您的應用程式之後，[在執行階段開啟遙測](../../azure-monitor/app/monitor-performance-live-website-now.md)。

## <a name="step-2-run-your-app"></a><a name="run"></a> 步驟 2：執行您的應用程式
按 F5 執行您的應用程式。 開啟不同的頁面來產生一些遙測。

在 Visual Studio 中，您會看見已記錄的事件計數。

![Visual Studio 的螢幕擷取畫面。 偵錯期間會顯示 Application Insights 按鈕。](./media/asp-net/00006-Events.png)

## <a name="step-3-see-your-telemetry"></a>步驟 3：查看遙測
您可以在 Visual Studio 或 Application Insights Web 入口網站中看到遙測。 在 Visual Studio 中搜尋遙測可協助您偵錯應用程式。 當系統作用中時監視效能及 web 入口網站的使用量。 

### <a name="see-your-telemetry-in-visual-studio"></a>在 Visual Studio 中查看遙測

在 Visual Studio 中，若要檢視 Application Insights 資料：  選取 [方案總管] > [已連線的服務] > 以滑鼠右鍵按一下 [Application Insights]，然後按一下 [搜尋即時遙測]。

在 Visual Studio 的 [Application Insights 搜尋] 視窗中，您可以在應用程式的資料中查看應用程式的伺服器端所產生的遙測。 試驗篩選器，然後按一下任何事件以查看更多詳細資料。

![[Application Insights] 視窗中 [偵錯工作階段中的資料] 檢視的螢幕擷取畫面。](./media/asp-net/55.png)

> [!Tip]
> 如果您沒有看到任何資料，請確定時間範圍正確，然後按一下 [搜尋] 圖示。

[深入了解 Visual Studio 中的 Application Insights 工具](../../azure-monitor/app/visual-studio.md)。

<a name="monitor"></a>
### <a name="see-telemetry-in-web-portal"></a>請參閱 web 入口網站中的遙測

您也可以在 Application Insights Web 入口網站中看到遙測 (除非您選擇只安裝 SDK)。 此入口網站中的圖表、分析工具和跨元件檢視比 Visual Studio 還多。 此入口網站也提供警示。

開啟 Application Insights 資源。 您可以登入 [Azure 入口網站](https://portal.azure.com/)加以尋找，或選取 [方案總管] > [已連線的服務] > 以滑鼠右鍵按一下 [Application Insights] > [開啟 Application Insights 入口網站]，而移至該處。

入口網站會從您的應用程式開啟遙測檢視。

![Application Insights 概觀頁面的螢幕擷取畫面](./media/asp-net/007.png)

在入口網站中，按一下任何圖格或圖表以查看詳細資料。

## <a name="step-4-publish-your-app"></a>步驟 4：發佈您的應用程式
將您的應用程式發佈至 IIS 伺服器或 Azure。 監看 [即時計量串流](../../azure-monitor/app/live-stream.md) 以確定一切順利執行。

您的遙測會累積在 Application Insights 入口網站，您可以在此監視計量，搜尋您的遙測。 您也可以使用功能強大的 [Kusto 查詢語言](/azure/kusto/query/)，分析使用狀況和效能或尋找特定事件。

您也可以繼續在 [Visual Studio](../../azure-monitor/app/visual-studio.md) 中，以診斷搜尋和[趨勢](../../azure-monitor/app/visual-studio-trends.md)等工具來分析您的遙測。

> [!NOTE]
> 如果應用程式傳送足夠的遙測資料達到[節流限制](../../azure-monitor/app/pricing.md#limits-summary)，則會切換為自動[取樣](../../azure-monitor/app/sampling.md)。 取樣可減少從應用程式傳送的遙測數量，同時為供診斷之用保留相互關聯的資料。
>
>

## <a name="youre-all-set"></a><a name="land"></a> 您全都準備好了

恭喜！ 您在應用程式中安裝了 Application Insights 套件，並將其設定為將遙測傳送至 Azure 上的 Application Insights 服務。

接收您應用程式遙測的 Azure 資源會由檢測金鑰識別。 您可以在 ApplicationInsights.config 檔案中找到此金鑰。


## <a name="upgrade-to-future-sdk-versions"></a>升級至未來的 SDK 版本
若要升級至[新版的 SDK](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases)，請開啟 **NuGet 套件管理員**，並篩選已安裝的套件。 選取 **Microsoft.ApplicationInsights.Web**，然後選擇 [升級]。

如果您已對 ApplicationInsights.config 進行任何的自訂，請在升級前儲存複本。 然後，將您的變更合併至新版本中。

## <a name="next-steps"></a>後續步驟

如果您對下列內容感興趣，請查看其他主題︰

* [在執行階段檢測 Web 應用程式](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure 雲端服務](../../azure-monitor/app/cloudservices.md)

### <a name="more-telemetry"></a>更多遙測

* **[瀏覽器和頁面載入資料](../../azure-monitor/app/javascript.md)** - 在您的網頁中插入程式碼片段。
* **[取得更詳細的相依性和例外狀況監視](../../azure-monitor/app/monitor-performance-live-website-now.md)** - 在您的伺服器上安裝狀態監視器。
* **[撰寫自訂事件](../../azure-monitor/app/api-custom-events-metrics.md)** 以計數、計時或測量使用者動作。
* **[取得記錄資料](../../azure-monitor/app/asp-net-trace-logs.md)** - 將記錄資料與您的遙測相互關聯。

### <a name="analysis"></a>分析

* **[在 Visual Studio 中使用 Application Insights](../../azure-monitor/app/visual-studio.md)**<br/>包括使用遙測來偵錯、診斷搜尋及鑽研程式碼的相關資訊。
* **[分析 - ](../../azure-monitor/log-query/get-started-portal.md)** - 功能強大的查詢語言。

### <a name="alerts"></a>警示

* [可用性測試](../../azure-monitor/app/monitor-web-app-availability.md)：建立測試，以確保網路上看得見您的網站。
* [智慧型診斷](../../azure-monitor/app/proactive-diagnostics.md)︰這些測試會自動執行，您不需要採取任何動作來設定它們。 它們會讓您知道應用程式是否有不尋常的失敗要求率。
* [計量警示](../../azure-monitor/platform/alerts-log.md)：設定警示以在計量超出臨界值時警告您。 您可以在撰寫於程式碼中的自訂度量上設定它們。

### <a name="automation"></a>自動化

* [自動建立 Application Insights 資源](../../azure-monitor/app/powershell.md)
