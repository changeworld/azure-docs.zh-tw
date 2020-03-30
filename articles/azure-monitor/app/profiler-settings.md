---
title: 使用 Azure Application Insights Profiler 設定窗格 | Microsoft Docs
description: 查看 Profiler 狀態並開始分析工作階段
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: f66a23c0562ec9f1987bd119a45b7b767f2dfe46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671625"
---
# <a name="configure-application-insights-profiler"></a>設定 Application Insights Profiler

## <a name="updated-profiler-agent"></a>更新的探查代理
觸發器功能僅適用于探測器代理的版本 2.6 或較新版本。 如果運行 Azure 應用服務，則代理將自動更新。 如果您轉到網站的 Kudu URL 並將 @診斷服務追加到網站的末尾，您可以查看正在運行的代理版本，如下所示： https://yourwebsite.scm.azurewebsites.net/diagnosticservices。 應用程式見解探測器 Webjob 應版本 2.6 或更新。 您可以通過重新開機 Web 應用來強制升級。 

如果在 VM 或雲服務上運行探測器，則需要安裝 Windows Azure 診斷 （WAD） 擴展版本 16.0.4 或更新版本。 您可以通過登錄到 VM 並查看此目錄來檢查 WAD 的版本：C：\包\外掛程式\Microsoft.Azure.診斷.IaaS診斷\1.16.0.4。 目錄名稱是已安裝的 WAD 版本。 當新版本可用時，Azure VM 代理將自動更新 WAD。

## <a name="profiler-settings-page"></a>探測器設置頁

要打開 Azure 應用程式見解探測器設置窗格，請轉到"應用程式見解性能"窗格，然後選擇 **"配置探測器"** 按鈕。

![連結到打開的探測器設置頁][configure-profiler-entry]

這將打開一個如下所示的頁面：

![探測器設置頁][configure-profiler-page]

**配置應用程式見解探測器**頁具有以下功能：

| | |
|-|-|
立即設定檔 | 開始為所有連結至此 Application Insights 執行個體的應用程式分析工作階段。
觸發程序 | 允許您配置導致探測器運行的觸發器。 
最近的分析會話 | 顯示過往分析工作階段的相關資訊。

## <a name="profile-now"></a>立即設定檔
此選項允許您按需啟動分析會話。 按一下此連結時，向此應用程式見解實例發送資料的所有探查代理將開始捕獲設定檔。 5 到 10 分鐘後，設定檔會話將顯示在下面的清單中。

對於使用者手動觸發探測器會話，他們至少需要對其應用程式見解元件的角色進行"寫入"訪問。 在大多數情況下，您會自動獲得此存取權限，無需執行其他工作。 如果遇到問題，要添加的訂閱範圍角色將是"應用程式見解元件參與者"角色。 [有關 Azure 監視的角色存取控制，請參閱更多](https://docs.microsoft.com/azure/azure-monitor/app/resources-roles-access-control)。

## <a name="trigger-settings"></a>觸發程序設定
![觸發設置快顯視窗][trigger-settings-flyout]

按一下功能表列上的"觸發器"按鈕將打開"觸發器設置"框。 您可以設置觸發器，以便當 CPU 或記憶體使用百分比達到您設置的級別時啟動分析。

| | |
|-|-|
開/關按鈕 | 開：此觸發器可以啟動探測器;關閉：此觸發器不會啟動探測器。
記憶體閾值 | 使用此記憶體百分比時，將啟動探測器。
Duration | 設置探測器在觸發時將運行的時間長度。
Cooldown | 設置探測器在觸發後再次檢查記憶體或 CPU 使用方式之前等待的時間長度。

## <a name="recent-profiling-sessions"></a>最近的分析會話
頁面的這一部分顯示有關最近分析會話的資訊。 分析會話表示探測器代理在託管應用程式的一台電腦上獲取設定檔的時間段。 您可以通過按一下其中一行從會話中打開設定檔。 對於每個會話，我們顯示：

| | |
|-|-|
由 | 會話如何啟動，由觸發器、"立即設定檔"或預設採樣啟動。 
應用程式名稱 | 已分析的應用程式的名稱。
機器實例 | 探測器代理運行的電腦的名稱。
時間戳記 | 捕獲設定檔的時間。
特雷西 | 附加到單個請求的跟蹤數。
CPU % | 在探測器運行時使用的 CPU 百分比。
記憶體 % | 在探測器運行時正在使用的記憶體百分比。

## <a name="use-web-performance-tests-to-generate-traffic-to-your-application"></a><a id="profileondemand"></a>使用 Web 效能測試生成應用程式的流量

您可以按一下滑鼠以手動觸發 Profiler。 假設您正在執行 Web 效能測試。 您需要跟蹤來説明瞭解 Web 應用在負載下運行的方式。 在擷取追蹤時擁有控制權是很重要的，因為這樣您才知道何時會執行負載測試。 但隨機取樣間隔可能會錯過這項資訊。

以下幾節將說明此案例的運作方式：

### <a name="step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>第 1 步：通過啟動 Web 效能測試生成 Web 應用的流量

如果您的 Web 應用程式已經有連入流量，或您只想要以手動方式產生流量，請略過本節，並繼續執行步驟 2。

1. 在應用程式見解門戶中，選擇 **"配置** > **效能測試**"。 

1. 若要啟動新的效能測試，請選取 [新增]**** 按鈕。

   ![建立新的效能測試][create-performance-test]

1. 在 [新增效能測試]**** 窗格中，設定測試目標 URL。 接受所有預設設定，然後選取 [執行測試]**** 以開始執行負載測試。

    ![設定負載測試][configure-performance-test]

    新的測試會先排入佇列，接著是「進行中」** 的狀態。

    ![提交負載測試並排入佇列][load-test-queued]

    ![負載測試正在執行中][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>第 2 步：啟動探測器按需會話

1. 負載測試開始執行時，請啟動 Profiler，以在 Web 應用程式接收負載時擷取其追蹤。

1. 移至 [設定 Profiler]**** 窗格。


### <a name="step-3-view-traces"></a>步驟 3：檢視追蹤

在 Profiler 執行完成後，請依照通知中的指示移至 [效能] 窗格，並檢視追蹤。

## <a name="next-steps"></a>後續步驟
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
