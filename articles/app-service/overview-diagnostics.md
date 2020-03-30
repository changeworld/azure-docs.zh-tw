---
title: 診斷和求解工具
description: 瞭解如何使用 Azure 門戶中的診斷和解決工具解決 Azure 應用服務中的應用問題。
keywords: App Service, Azure App Service, 診斷, 支援, Web 應用程式, 疑難排解, 自助
author: jen7714
ms.topic: article
ms.date: 10/18/2019
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: e06e71d4436ac6c64ff2edc876d7849d084482f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74671643"
---
# <a name="azure-app-service-diagnostics-overview"></a>Azure App Service 診斷概觀

當您執行 Web 應用程式時，必須為使用者因 500 錯誤向您反映網站已關閉的所有可能問題作好準備。 應用服務診斷是一種智慧和互動式體驗，可説明您在無需配置時對應用進行故障排除。 當您遇到應用問題時，應用服務診斷會指出問題所在，以指導您獲得正確的資訊，以便更輕鬆、更快速地疑難排解並解決問題。

儘管在過去 24 小時內應用出現問題時，此體驗最有用，但所有診斷圖始終可供您分析。

App Service 診斷不僅適用於 Windows 上您的應用程式，也適用於 [Linux/容器](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro)、[App Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro) 和 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) 上的應用程式。

## <a name="open-app-service-diagnostics"></a>開啟 App Service 診斷

要訪問應用服務診斷，請導航到[Azure 門戶](https://portal.azure.com)中的應用服務 Web 應用或應用服務環境。 在左方導覽列中，按一下 [診斷並解決問題]****。

對於 Azure 函數，導航到函數應用，並在頂部導航中按一下**平臺功能**，然後從 **"資源管理**"部分選擇 **"診斷並解決問題**"。

在"應用服務診斷主頁"中，您可以使用每個主頁磁貼中的關鍵字選擇最能描述應用問題的類別。 此外，此頁面是您可以找到 Windows 應用**的診斷工具**的位置。 請參閱[診斷工具（僅適用于 Windows 應用）。](#diagnostic-tools-only-for-windows-app)

![首頁](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

## <a name="interactive-interface"></a>互動式介面

選擇最適合應用問題的主頁類別後，應用服務診斷的互動式介面 Genie 可以指導您完成應用的診斷和解決問題。 您可以使用 Genie 提供的磁貼快捷方式查看您感興趣的問題類別的完整診斷報告。 磁貼快捷方式提供了訪問診斷指標的直接方法。

![圖格快速鍵](./media/app-service-diagnostics/tile-shortcuts-2.png)

按一下這些磁貼後，您可以看到與磁貼中描述的問題相關的主題清單。 這些主題提供了完整報告中的顯著資訊片段。 您可以按一下這些主題中的任何一個以進一步調查問題。 此外，您還可以按一下"**查看完整報告**"以流覽單個頁面上的所有主題。

![主題](./media/app-service-diagnostics/application-logs-insights-3.png)

![查看完整報告](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>診斷報告

選擇通過按一下主題進一步調查問題後，您可以查看有關該主題的更多詳細資訊，這些詳細資訊通常輔以圖形和標記。 診斷報告可以是一個強大的工具，可以查明應用的問題。

![診斷報告](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>健康狀態檢查

如果您不知道應用出了什麼問題，或者不知道從哪裡開始解決問題，則運行狀況檢查是一個很好的開始。 運行狀況檢查會分析您的應用程式，為您提供快速的互動式概述，指出什麼是健康的和出了什麼問題，並告訴您在哪裡調查問題。 其智慧型及互動式的介面可提供您進行疑難排解程序的指導方針。 運行狀況檢查與適用于 Windows 應用的精靈體驗和 Linux 應用的 Web 應用向下診斷報告集成。

### <a name="health-checkup-graphs"></a>運行狀況檢查圖表

運行狀況檢查中有四個不同的圖形。

- **請求和錯誤：** 顯示過去 24 小時內發出的請求數以及 HTTP 伺服器錯誤的圖表。
- **應用性能：** 顯示各種百分位陣列的過去 24 小時的回應時間的圖形。
- **CPU 使用率：** 顯示過去 24 小時內每個實例的總 CPU 使用率的圖形。  
- **記憶體使用方式：** 顯示過去 24 小時內每個實例的實體記憶體使用率的總體百分比的圖形。

![健康狀態檢查](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>調查應用程式代碼問題（僅適用于 Windows 應用）

因為許多應用程式問題與您應用程式程式碼中的問題相關，App Service 診斷與 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) 整合，以反白顯示要與所選停機狀況相互關聯的例外狀況和相依性問題。 必須單獨啟用應用程式見解。

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

要查看應用程式見解的例外項和依賴項，**請選擇 Web 應用或** **Web 應用慢磁貼**快捷方式。

### <a name="troubleshooting-steps-only-for-windows-app"></a>故障排除步驟（僅適用于 Windows 應用）

如果在過去 24 小時內檢測到特定問題類別的問題，您可以查看完整的診斷報告，應用服務診斷可能會提示您查看更多故障排除建議和後續步驟，以便獲得更引導的體驗。

![應用程式見解和故障排除以及後續步驟](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>診斷工具（僅適用于 Windows 應用）

診斷工具組括更高級的診斷工具，可説明您調查應用程式代碼問題、速度慢、連接字串等。 和主動工具，説明您緩解 CPU 使用率、請求和記憶體問題。

### <a name="proactive-cpu-monitoring"></a>主動 CPU 監控

主動 CPU 監視為您提供了在應用的應用或子進程消耗高 CPU 資源時採取操作的簡便、主動的方法。 您可以設置自己的 CPU 閾值規則，以暫時緩解高 CPU 問題，直到找到意外問題的真正原因。 有關詳細資訊，請參閱在[CPU 問題發生之前緩解這些問題](https://azure.github.io/AppService/2019/10/07/Mitigate-your-CPU-problems-before-they-even-happen.html)。

![主動 CPU 監控](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="auto-healing-and-proactive-auto-healing"></a>自動修復和主動自動修復

自動修復是應用發生意外行為時可以執行的緩解操作。 您可以根據請求計數、慢速請求、記憶體限制和 HTTP 狀態碼設置自己的規則，以觸發緩解操作。 使用該工具暫時緩解意外行為，直到找到根本原因。 有關詳細資訊，請參閱[在應用服務診斷中宣佈新的自動修復體驗](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html)。

![自動修復](./media/app-service-diagnostics/auto-healing-10.png)

與主動 CPU 監視一樣，主動自動修復是緩解應用意外行為的交鑰匙解決方案。 當應用服務確定你的應用處于不可恢復狀態時，主動自動修復將重新開機應用。 有關詳細資訊，請參閱[引入主動自動修復](https://azure.github.io/AppService/2017/08/17/Introducing-Proactive-Auto-Heal.html)。

## <a name="navigator-and-change-analysis-only-for-windows-app"></a>導航器和更改分析（僅適用于 Windows 應用）

在具有持續集成的大型團隊中，以及你的應用具有許多依賴關係的地方，很難確定導致不正常行為的特定更改。 導航器通過自動呈現應用的依賴項映射以及同一訂閱中的所有資源，説明獲取應用拓撲的可見度。 導航器允許您查看應用及其依賴項所做的更改的整合清單，並縮小導致不正常行為的更改的範圍。 它可以通過主頁磁貼**導航器**進行訪問，並且需要在第一次使用之前啟用它。 有關詳細資訊，請參閱使用[Navigator 查看應用的依賴項](https://azure.github.io/AppService/2019/08/06/Bring-visibility-to-your-app-and-its-dependencies-with-Navigator.html)。

![導航器預設頁](./media/app-service-diagnostics/navigator-default-page-11.png)

![差異視圖](./media/app-service-diagnostics/diff-view-12.png)

可以通過磁貼快捷方式、**應用程式更改**和**應用程式崩潰**訪問應用更改分析 **，** 以便與其他指標同時使用。 在使用該功能之前，必須首先啟用它。 有關詳細資訊，請參閱[在應用服務診斷中宣佈新的更改分析體驗](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html)。

通過在標題中添加"[Diag]"，在[UserVoice](https://feedback.azure.com/forums/169385-web-apps)上發佈您的問題或回饋。
