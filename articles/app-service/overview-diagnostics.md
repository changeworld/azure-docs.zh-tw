---
title: 診斷和解決工具
description: 瞭解如何使用 Azure 入口網站的診斷和解決工具，在 Azure App Service 中疑難排解應用程式的問題。
keywords: App Service, Azure App Service, 診斷, 支援, Web 應用程式, 疑難排解, 自助
author: jen7714
ms.topic: article
ms.date: 10/18/2019
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: d58341979b0bbe0699a5ca293b20394c43cde1d1
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962803"
---
# <a name="azure-app-service-diagnostics-overview"></a>Azure App Service 診斷概觀

當您執行 Web 應用程式時，必須為使用者因 500 錯誤向您反映網站已關閉的所有可能問題作好準備。 App Service 診斷是一種智慧型且互動式的體驗，可協助您針對應用程式進行疑難排解，而不需要設定。 當您的應用程式發生問題時，App Service 診斷會指出錯誤的原因，以引導您更輕鬆且快速地針對問題進行疑難排解並加以解決。

雖然當您的應用程式在過去24小時內遇到問題時，這項體驗非常有説明，但所有的診斷圖表一律可供您分析。

App Service 診斷不僅適用於 Windows 上您的應用程式，也適用於 [Linux/容器](./overview.md#app-service-on-linux)、[App Service Environment](./environment/intro.md) 和 [Azure Functions](../azure-functions/functions-overview.md) 上的應用程式。

## <a name="open-app-service-diagnostics"></a>開啟 App Service 診斷

若要存取 App Service 診斷，請流覽至您的 App Service web 應用程式或 [Azure 入口網站](https://portal.azure.com)中的 App Service 環境。 在左方導覽列中，按一下 [診斷並解決問題]。

針對 Azure Functions，請流覽至您的函數應用程式，然後在頂端導覽中，按一下 [**平臺功能**]，然後從 [**資源管理**] 區段選取 [**診斷並解決問題**]。

在 App Service 診斷首頁中，您可以使用每個首頁磚中的關鍵字，選擇最能描述應用程式問題的類別。 此外，您可以在此頁面找到 Windows 應用程式的 **診斷工具** 。 請參閱 [ (僅適用于 Windows 應用程式) 的診斷工具 ](#diagnostic-tools-only-for-windows-app)。

![首頁](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

> [!NOTE]
> 如果您的應用程式已關閉或執行速度很慢，您可以 [收集分析追蹤](https://azure.github.io/AppService/2018/06/06/App-Service-Diagnostics-Profiling-an-ASP.NET-Web-App-on-Azure-App-Service.html) ，以找出問題的根本原因。 分析是輕量，且是針對生產案例而設計。
>

## <a name="interactive-interface"></a>互動式介面

一旦您選取最符合應用程式問題的首頁類別，App Service 診斷的互動式介面瓶精靈可引導您完成診斷和解決應用程式的問題。 您可以使用瓶精靈提供的磚快速鍵來查看您感興趣之問題類別的完整診斷報告。 圖格快速鍵可讓您直接存取診斷計量。

![圖格快速鍵](./media/app-service-diagnostics/tile-shortcuts-2.png)

按一下這些圖格之後，您就可以看到與圖格中所述問題相關的主題清單。 這些主題會提供完整報告中值得注意的資訊片段。 您可以按一下任何這些主題，進一步調查問題。 此外，您也可以按一下 [ **View Full Report** ] 來探索單一頁面上的所有主題。

![主題](./media/app-service-diagnostics/application-logs-insights-3.png)

![View Full Report](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>診斷報告

當您按一下某個主題來進一步調查問題之後，就可以查看有關此主題的更多詳細資料，通常會使用圖形和 markdown 來補充。 診斷報告可以是用來查明應用程式問題的強大工具。

![診斷報告](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>健康狀態檢查

如果您不知道您的應用程式有什麼問題，或是不知道要從何處著手針對您的問題進行疑難排解，則健全狀況檢查是不錯的開端。 健全狀況檢查會分析您的應用程式，為您提供快速且互動式的總覽，指出狀況良好、有什麼問題，並告訴您要在何處調查問題。 其智慧型及互動式的介面可提供您進行疑難排解程序的指導方針。 健全狀況檢查已與適用于 Linux 應用程式的 Windows 應用程式和 web 應用程式的瓶精靈體驗進行整合，並已關閉診斷報告。

### <a name="health-checkup-graphs"></a>健全狀況檢查圖表

健全狀況檢查中有四個不同的圖形。

- **要求和錯誤：** 顯示過去24小時內所提出的要求數目以及 HTTP 伺服器錯誤的圖形。
- **應用程式效能：** 顯示過去24小時內各種百分位數群組回應時間的圖形。
- **CPU 使用量：** 此圖表顯示過去24小時內每個實例的整體 CPU 使用率百分比。  
- **記憶體使用量：** 此圖表顯示過去24小時內每個實例的整體實體記憶體使用量百分比。

![健康狀態檢查](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>只針對 Windows 應用程式) 調查應用程式程式碼問題 (

因為許多應用程式問題與您應用程式程式碼中的問題相關，App Service 診斷與 [Application Insights](../azure-monitor/app/app-insights-overview.md) 整合，以反白顯示要與所選停機狀況相互關聯的例外狀況和相依性問題。 Application Insights 必須分開啟用。

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

若要查看 Application Insights 的例外狀況和相依性，請選取 [ **web 應用程式關閉** ] 或 [ **web 應用程式緩慢** ] 磚快速鍵

### <a name="troubleshooting-steps-only-for-windows-app"></a> (僅適用于 Windows 應用程式) 的疑難排解步驟

如果在過去24小時內偵測到有特定問題類別的問題，您可以查看完整的診斷報告，App Service 診斷可能會提示您查看更多的疑難排解建議和後續步驟，以提供更多引導式體驗。

![Application Insights 和疑難排解和後續步驟](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>診斷工具 (僅適用于 Windows 應用程式) 

診斷工具組含更先進的診斷工具，可協助您調查應用程式程式碼問題、緩慢、連接字串等等。 和主動式工具可協助您降低 CPU 使用量、要求和記憶體的問題。

### <a name="proactive-cpu-monitoring"></a>主動式 CPU 監視

主動式 CPU 監視可讓您在應用程式的應用程式或子進程耗用大量的 CPU 資源時，以簡單主動的方式採取動作。 您可以設定自己的 CPU 閾值規則，以暫時降低高 CPU 問題，直到找到未預期問題的真正原因為止。 如需詳細資訊，請參閱在 [CPU 問題發生前加以緩和](https://azure.github.io/AppService/2019/10/07/Mitigate-your-CPU-problems-before-they-even-happen.html)。

![主動式 CPU 監視](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="auto-healing-and-proactive-auto-healing"></a>自動修復和主動式自動修復

當您的應用程式發生非預期的行為時，自動修復是您可以採取的緩和措施。 您可以根據要求計數、慢速要求、記憶體限制和 HTTP 狀態碼來設定您自己的規則，以觸發緩和措施。 在您找出根本原因之前，請使用此工具暫時緩和非預期的行為。 如需詳細資訊，請參閱 [宣佈 app service 診斷中的新自動修復體驗](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html)。

![自動修復](./media/app-service-diagnostics/auto-healing-10.png)

主動自動修復就像主動式 CPU 監視一樣，是減少應用程式非預期行為的轉換關鍵解決方案。 當 App Service 判斷您的應用程式處於無法復原的狀態時，主動式自動修復會重新開機您的應用程式。 如需詳細資訊，請參閱 [主動式自動修復簡介](https://azure.github.io/AppService/2017/08/17/Introducing-Proactive-Auto-Heal.html)。

## <a name="navigator-and-change-analysis-only-for-windows-app"></a>僅適用于 Windows 應用程式) 的導覽器和變更分析 (

在持續整合的大型小組中，以及您的應用程式有許多相依性的情況下，可能很難找出造成狀況不良行為的特定變更。 導覽器會自動呈現您應用程式的相依性對應，以及相同訂用帳戶中的所有資源，以協助您瞭解應用程式的拓撲。 [導覽器] 可讓您查看應用程式及其相依性所做之變更的匯總清單，並縮小造成狀況不良行為的變更。 您可以透過首頁磚導覽來存取它，並在第一次使用 **之前，必須** 先啟用。 如需詳細資訊，請參閱 [使用導覽器瞭解應用程式的相依性](https://azure.github.io/AppService/2019/08/06/Bring-visibility-to-your-app-and-its-dependencies-with-Navigator.html)。

![導覽器預設頁面](./media/app-service-diagnostics/navigator-default-page-11.png)

![差異視圖](./media/app-service-diagnostics/diff-view-12.png)

應用程式變更的變更分析可透過磚快速鍵來存取、 **應用程式變更** 和 **應用程式** 在 **可用性和效能** 上損毀，讓您可以同時與其他計量一起使用。 在使用此功能之前，您必須先啟用它。 如需詳細資訊，請參閱 [宣佈 App Service 診斷中的新變更分析體驗](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html)。

在標題中新增 "[診斷]"，以在 [UserVoice](https://feedback.azure.com/forums/169385-web-apps) 張貼您的問題或意見反應。