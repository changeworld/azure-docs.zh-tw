---
title: 使用 Azure Application Insights Profiler 設定窗格 | Microsoft Docs
description: 查看 Profiler 狀態並開始分析工作階段
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 9d9cc377ead0c297e8334d34255bd2c7c7cd39fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86499404"
---
# <a name="configure-application-insights-profiler"></a>設定 Application Insights Profiler

## <a name="updated-profiler-agent"></a>更新的 Profiler 代理程式
觸發程式功能只適用于2.6 版或更新版本的 profiler 代理程式。 如果您正在執行 Azure App Service，則會自動更新您的代理程式。 如果您移至網站的 Kudu URL，並將 \DiagnosticServices 附加至它的結尾，則可以查看您正在執行的代理程式版本，如下所示：  `https://yourwebsite.scm.azurewebsites.net/diagnosticservices` 。 Application Insights Profiler Webjob 應為2.6 版或更新版本。 您可以重新開機您的 web 應用程式，以強制進行升級。 

如果您在 VM 或雲端服務上執行分析工具，您必須安裝 Windows Azure 診斷 (WAD) 擴充功能版本16.0.4 版或更新版本。 您可以登入 VM 並查看此目錄來檢查 WAD 的版本： C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.16.0.4。 目錄名稱是已安裝的 WAD 版本。 當有新版本可用時，Azure VM 代理程式會自動更新 WAD。

## <a name="profiler-settings-page"></a>Profiler 設定頁面

若要開啟 [Azure 應用程式 Insights Profiler 設定] 窗格，請移至 [Application Insights 效能] 窗格，然後選取 [ **設定** 分析工具] 按鈕。

![開啟 Profiler 設定頁面的連結][configure-profiler-entry]

這會開啟如下所示的頁面：

![Profiler 設定頁面][configure-profiler-page]

[ **設定 Application Insights Profiler** ] 頁面包含下列功能：

| 功能 | 說明 |
|-|-|
立即分析 | 開始為所有連結至此 Application Insights 執行個體的應用程式分析工作階段。
觸發程序 | 可讓您設定觸發程式來執行程式碼剖析工具。 
最近的分析會話 | 顯示過往分析工作階段的相關資訊。

## <a name="profile-now"></a>立即分析
此選項可讓您依需求啟動分析會話。 當您按一下此連結時，傳送資料至此 Application Insights 實例的所有 profiler 代理程式都會開始捕獲設定檔。 5到10分鐘之後，設定檔會話將會顯示在下列清單中。

若要讓使用者手動觸發 profiler 會話，它們至少需要針對 Application Insights 元件的角色進行「寫入」存取。 在大多數情況下，您會自動取得此存取權，不需要額外的工作。 如果您遇到問題，要新增的訂用帳戶範圍角色會是「Application Insights 元件參與者」角色。 [深入瞭解使用 Azure 監視的角色存取控制](./resources-roles-access-control.md)。

## <a name="trigger-settings"></a>觸發程序設定
![觸發程式設定飛出視窗][trigger-settings-flyout]

按一下功能表列上的 [觸發程式] 按鈕，就會開啟 [觸發程式設定] 方塊。 您可以設定觸發程式，以在 CPU 或記憶體使用百分比達到您設定的層級時開始分析。

| 設定 | 說明 |
|-|-|
開啟/關閉按鈕 | On：此觸發程式可以啟動 profiler;Off：此觸發程式不會啟動 profiler。
記憶體閾值 | 當這個百分比的記憶體正在使用中時，將會啟動分析工具。
持續時間 | 設定觸發程式時，profiler 將執行的時間長度。
Cooldown | 設定分析工具在其觸發之後，再次檢查記憶體或 CPU 使用量之前，會等待的時間長度。

## <a name="recent-profiling-sessions"></a>最近的分析會話
頁面的這個區段會顯示最近程式碼剖析會話的相關資訊。 分析會話代表分析工具代理程式在裝載您應用程式的其中一部電腦上取得設定檔的時間期間。 您可以按一下其中一個資料列，從會話開啟設定檔。 在每個會話中，我們會示範：

| 設定 | 說明 |
|-|-|
觸發者 | 會話的啟動方式，可以是觸發程式、立即設定檔或預設取樣。 
應用程式名稱 | 已分析的應用程式名稱。
電腦實例 | Profiler 代理程式執行所在的電腦名稱稱。
時間戳記 | 捕獲設定檔的時間。
Tracee | 附加至個別要求的追蹤數目。
CPU % | 分析工具執行時所使用的 CPU 百分比。
緩存 | Profiler 執行時所使用的記憶體百分比。

## <a name="use-web-performance-tests-to-generate-traffic-to-your-application"></a><a id="profileondemand"></a> 使用 web 效能測試來產生應用程式的流量

您可以按一下滑鼠以手動觸發 Profiler。 假設您正在執行 Web 效能測試。 您將需要追蹤，以協助您瞭解 web 應用程式在負載下執行的方式。 在擷取追蹤時擁有控制權是很重要的，因為這樣您才知道何時會執行負載測試。 但隨機取樣間隔可能會錯過這項資訊。

以下幾節將說明此案例的運作方式：

### <a name="step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>步驟1：啟動 web 效能測試來產生 web 應用程式的流量

如果您的 Web 應用程式已經有連入流量，或您只想要以手動方式產生流量，請略過本節，並繼續執行步驟 2。

1. 在 Application Insights 入口網站中，選取 [**設定**  >  **效能測試**]。 

1. 若要啟動新的效能測試，請選取 [新增]**** 按鈕。

   ![建立新的效能測試][create-performance-test]

1. 在 [新增效能測試]**** 窗格中，設定測試目標 URL。 接受所有預設設定，然後選取 [執行測試]**** 以開始執行負載測試。

    ![設定負載測試][configure-performance-test]

    新的測試會先排入佇列，接著是「進行中」** 的狀態。

    ![提交負載測試並排入佇列][load-test-queued]

    ![負載測試正在執行中][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>步驟2：啟動 Profiler 隨選會話

1. 負載測試開始執行時，請啟動 Profiler，以在 Web 應用程式接收負載時擷取其追蹤。

1. 移至 [設定 Profiler]**** 窗格。


### <a name="step-3-view-traces"></a>步驟 3：檢視追蹤

在 Profiler 執行完成後，請依照通知中的指示移至 [效能] 窗格，並檢視追蹤。

## <a name="next-steps"></a>接下來的步驟
[啟用 Profiler 和檢視追蹤](profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/profiler-settings/Profiler-on-demand.png
[configure-profiler-entry]: ./media/profiler-settings/configure-profiler-entry.png
[configure-profiler-page]: ./media/profiler-settings/configureBlade.png
[trigger-settings-flyout]: ./media/profiler-settings/CPUTrigger.png
[create-performance-test]: ./media/profiler-settings/new-performance-test.png
[configure-performance-test]: ./media/profiler-settings/configure-performance-test.png
[load-test-queued]: ./media/profiler-settings/load-test-queued.png
[load-test-in-progress]: ./media/profiler-settings/load-test-inprogress.png
[enable-app-insights]: ./media/profiler-settings/enable-app-insights-blade-01.png
[update-site-extension]: ./media/profiler-settings/update-site-extension-01.png
[change-and-save-appinsights]: ./media/profiler-settings/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/profiler-settings/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/profiler-settings/check-extension-update-01.png
[profiler-timeout]: ./media/profiler-settings/profiler-timeout.png
