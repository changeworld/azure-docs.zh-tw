---
title: 監視 Azure Functions
description: 了解如何使用 Azure Application Insights 搭配 Azure Functions 來監視函式執行。
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.topic: conceptual
ms.date: 10/14/2020
ms.custom: devx-track-csharp, fasttrack-edit, contperfq2, devx-track-js, devx-track-azurecli
ms.openlocfilehash: b27fb14341e07683d66418485158a94c18e7a997
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92748164"
---
# <a name="monitor-azure-functions"></a>監視 Azure Functions

[Azure Functions](functions-overview.md) 提供與 [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) 的內建整合來監視函式。 本文概述 Azure 針對監視 Azure Functions 所提供的監視功能。

Application Insights 會收集記錄檔、效能和錯誤資料。 藉由自動偵測效能異常並提供強大的分析工具，您可以更輕鬆地診斷問題，並進一步瞭解您的函式的使用方式。 這些工具的設計目的是協助您持續改善函式的效能和可用性。 您甚至可以在本機函式應用程式專案開發期間使用 Application Insights。 如需詳細資訊，請參閱 [Application Insights 是什麼？](../azure-monitor/app/app-insights-overview.md)

在 Azure Functions 內建 Application Insights 檢測時，您需要有效的檢測金鑰，才能將函數應用程式連線到 Application Insights 資源。 當您在 Azure 中建立函數應用程式資源時，會將檢測金鑰新增至您的應用程式設定。 如果您的函式應用程式還沒有這個金鑰，您可以[手動設定](configure-monitoring.md#enable-application-insights-integration)。  

## <a name="application-insights-pricing-and-limits"></a>Application Insights 定價和限制

您可以免費試用與 Azure Functions 的 Application Insights 整合，每日限制可免費處理多少資料。

如果您在開發期間啟用了 Application Insights，則在測試期間可能會達到此限制。 當您趨近每日限制時，Azure 會提供入口網站及電子郵件通知。 如果您錯過那些警示並達到該限制，Application Insights 查詢中將不會出現新記錄。 請留意限制，以避免不必要的疑難排解時間。 如需詳細資訊，請參閱[管理 Application Insights 中的價格和資料磁碟區](../azure-monitor/app/pricing.md)。

> [!IMPORTANT]
> Application Insights 具有[取樣](../azure-monitor/app/sampling.md)功能，可以提供保護，以避免在尖峰負載期間完成的執行中產生過多的遙測資料。 取樣預設為啟用。 如果您似乎有資料遺失，可能就需要調整取樣設定，以符合您特定的監視案例。 若要深入了解，請參閱[設定取樣](configure-monitoring.md#configure-sampling)。

可供函式應用程式使用的 Application Insights 功能完整清單，詳述於[適用於 Azure Functions 的 Application Insights 支援的功能](../azure-monitor/app/azure-functions-supported-features.md)中。

## <a name="application-insights-integration"></a>Application Insights 整合

當您建立函數應用程式時，通常會建立 Application Insights 實例。 在此情況下，整合所需的檢測金鑰已經設定為 *APPINSIGHTS_INSTRUMENTATIONKEY* 的應用程式設定。 如果您的函式應用程式因為某些原因而未設定檢測金鑰，您必須 [啟用 Application Insights 整合](configure-monitoring.md#enable-application-insights-integration)。  

## <a name="collecting-telemetry-data"></a>收集遙測資料

啟用 Application Insights 整合時，會將遙測資料傳送至連線的 Application Insights 實例。 這項資料包括函數主機所產生的記錄、從您的函式程式碼撰寫的追蹤，以及效能資料。 

>[!NOTE]
>除了函式和函式主機中的資料之外，您也可以從 [函數縮放控制器](#scale-controller-logs)收集資料。   

### <a name="log-levels-and-categories"></a>記錄層級和分類

當您從應用程式程式碼寫入追蹤時，您應該將記錄層級指派給追蹤。 記錄層級可讓您限制從追蹤收集的資料量。  

[!INCLUDE [functions-log-levels](../../includes/functions-log-levels.md)]

若要深入瞭解記錄層級，請參閱 [設定記錄層級](configure-monitoring.md#configure-log-levels)。

藉由將記錄的專案指派給類別，您可以更充分地控制在函數應用程式中從特定來源產生的遙測。 類別可讓您更輕鬆地針對收集的資料執行分析。 從您的函式程式碼寫入的追蹤會根據函數名稱指派給個別類別。 若要深入瞭解類別，請參閱 [設定類別目錄](configure-monitoring.md#configure-categories)。

### <a name="custom-telemetry-data"></a>自訂遙測資料

在 [c #](functions-dotnet-class-library.md#log-custom-telemetry-in-c-functions) 和 [JavaScript](functions-reference-node.md#log-custom-telemetry)中，您可以使用 Application Insights SDK 來撰寫自訂遙測資料。

### <a name="dependencies"></a>相依性

從2.x 版的函式開始，執行時間會自動收集使用特定用戶端 Sdk 之系結的相關資料。 Application Insights 會收集下列相依性的相關資料：

+ Azure Cosmos DB 
+ Azure 事件中樞
+ Azure 服務匯流排
+ Azure 儲存體服務 (Blob、佇列和資料表) 

此外也會捕捉使用的 HTTP 要求和資料庫呼叫 `SqlClient` 。 如需 Application Insights 所支援相依性的完整清單，請參閱 [自動追蹤](../azure-monitor/app/asp-net-dependencies.md#automatically-tracked-dependencies)的相依性。

Application Insights 會產生所收集相依性資料的 _應用程式對應_ 。 以下是 HTTP 觸發程式函式與佇列儲存體輸出系結的應用程式對應範例。  

![具有相依性的應用程式對應](./media/functions-monitoring/app-map.png)

相依性會在 `Information` 層級寫入。 如果您篩選或更新版本 `Warning` ，就不會看到相依性資料。 此外，自動收集相依性會在非使用者範圍進行。 若要捕獲相依性資料，請確定您的主機中的層級至少設定為 `Information` 使用者範圍以外的 (`Function.<YOUR_FUNCTION_NAME>.User`) 。

除了自動相依性資料收集之外，您也可以使用其中一種特定語言的 Application Insights Sdk，將自訂的相依性資訊寫入記錄檔。 如需如何撰寫自訂相依性的範例，請參閱下列其中一種特定語言的範例：

+ [在 C# 函式中記錄自訂遙測](functions-dotnet-class-library.md#log-custom-telemetry-in-c-functions)
+ [在 JavaScript 函式中記錄自訂遙測](functions-reference-node.md#log-custom-telemetry) 

## <a name="writing-to-logs"></a>寫入至記錄檔 

您寫入記錄的方式和使用的 Api 取決於函數應用程式專案的語言。   
請參閱您的語言開發人員指南，以深入瞭解如何從您的函式撰寫記錄。

+ [C # ( .NET 類別庫) ](functions-dotnet-class-library.md#logging)
+ [Java](functions-reference-java.md#logger)
+ [JavaScript](functions-reference-node.md#write-trace-output-to-logs) 
+ [PowerShell](functions-reference-powershell.md#logging)
+ [Python](functions-reference-python.md#logging)

## <a name="streaming-logs"></a>串流記錄

開發應用程式時，您通常會想要查看在 Azure 中執行時，以近乎即時的方式寫入記錄檔。

有兩種方式可以查看您的函數執行所產生的記錄資料串流。

* **內建記錄資料流** ：App Service 平台可讓您檢視應用程式記錄檔的資料流。 當您在 [本機開發](functions-develop-local.md) 期間，以及在入口網站中使用 [ **測試** ] 索引標籤時，此資料流程相當於您在調試函式時所看到的輸出。 隨即會顯示所有以記錄為基礎的資訊。 如需詳細資訊，請參閱[串流處理記錄](../app-service/troubleshoot-diagnostic-logs.md#stream-logs)。 此串流處理方法僅支援單一執行個體，且不能與在使用量方案中的 Linux 上執行的應用程式搭配使用。

* **即時計量資料流** ：當您的函式應用程式 [連線到 Application Insights](configure-monitoring.md#enable-application-insights-integration)時，您可以使用 [即時計量資料流](../azure-monitor/app/live-stream.md)，在 Azure 入口網站中近乎即時地查看記錄資料和其他度量。 當您在使用量方案中監視在多個執行個體或 Linux 上執行的函式時，請使用此方法。 此方法會使用[取樣資料](configure-monitoring.md#configure-sampling)。

您可以在入口網站和大部分的本機開發環境中檢視記錄資料流。 若要瞭解如何啟用記錄資料流程，請參閱 [Azure Functions 中的啟用串流執行記錄](streaming-logs.md)。

## <a name="diagnostic-logs"></a>診斷記錄

_這項功能目前為預覽狀態。_ 

Application Insights 可讓您將遙測資料匯出至長期儲存或其他 analysis services。  

因為函式也會與 Azure 監視器整合，所以您也可以使用診斷設定，將遙測資料傳送至不同的目的地，包括 Azure 監視器記錄。 若要深入了解，請參閱[使用 Azure 監視器記錄監視 Azure Functions](functions-monitor-log-analytics.md)。

## <a name="scale-controller-logs"></a>調整控制器記錄

_這項功能目前為預覽狀態。_ 

[Azure Functions 調整控制器](./functions-scale.md#runtime-scaling)會監視您的應用程式執行所在 Azure Functions 主機的實例。 此控制器會決定何時要根據目前的效能來新增或移除實例。 您可以讓調整控制器發出記錄來 Application Insights，以深入瞭解調整控制器針對您的函數應用程式所做的決策。 您也可以將產生的記錄儲存在 Blob 儲存體中，以供其他服務進行分析。 

若要啟用這項功能，請將名為的應用程式設定新增 `SCALE_CONTROLLER_LOGGING_ENABLED` 至函數應用程式設定。 若要深入瞭解，請參閱 [設定調整控制器記錄](configure-monitoring.md#configure-scale-controller-logs)檔。

## <a name="report-issues"></a>報告問題

若要回報關於 Functions 中 Application Insights 整合的問題，或是提出建議或要求，請[在 GitHub 中建立問題](https://github.com/Azure/Azure-Functions/issues/new) \(英文\)。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源：

* [Application Insights](/azure/application-insights/)
* [ASP.NET Core 記錄](/aspnet/core/fundamentals/logging/)
