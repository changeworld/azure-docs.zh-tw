---
title: 規劃預覽環境 - Azure 時間序列見解 |微軟文檔
description: 配置、管理、規劃和部署 Azure 時間序列預覽環境的最佳做法。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/15/2020
ms.custom: seodec18
ms.openlocfilehash: b16d78c9670d05fcec8126c5544d1dd97f6a03bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045714"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>規劃您的 Azure 時間序列深入解析預覽版環境

本文介紹了使用 Azure 時間序列見解預覽快速規劃和入門的最佳做法。

> [!NOTE]
> 有關規劃常規可用性時間序列見解實例的最佳做法，請閱讀["規劃 Azure 時間序列見解"常規可用性環境](time-series-insights-environment-planning.md)。

## <a name="best-practices-for-planning-and-preparation"></a>規劃和準備的最佳做法

以下文章中進一步介紹了有關規劃和準備環境的最佳做法：

* [在預配時序見解預覽環境](#the-preview-environment)時，您將獲得什麼。
* 您的[時間序列 I 和時間戳記屬性是什麼](#configure-time-series-ids-and-timestamp-properties)。
* 新的[時間序列模型是什麼](#understand-the-time-series-model)，以及如何構建您自己的模型。
* 如何在[JSON 中有效地發送事件](#shape-your-events)。
* 時間序列洞察[業務災害復原選項](#business-disaster-recovery)。

Azure 時間序列見解採用即用即付業務模型。 有關費用和容量的詳細資訊，請閱讀[時間序列見解定價](https://azure.microsoft.com/pricing/details/time-series-insights/)。

## <a name="the-preview-environment"></a>預覽環境

當您佈建時間序列深入解析預覽版環境時，您會建立兩個 Azure 資源：

* Azure 時間序列深入解析預覽版環境
* Azure 儲存體一般用途 V1 帳戶

作為預配過程的一部分，您可以指定是否要啟用暖存儲。 暖存儲為您提供分層查詢體驗。 啟用後，必須指定 7 到 30 天的保留期。 在暖存儲保留期內執行的查詢通常提供更快的回應時間。 當查詢跨越暖存儲保留期時，則從冷庫提供服務。

暖存儲上的查詢是免費的，而對冷存儲的查詢會產生成本。 瞭解查詢模式並相應地規劃暖存儲配置非常重要。 我們建議對最新資料的互動式分析駐留在您的暖存儲中，模式分析和長期趨勢應存在於寒冷中。

> [!NOTE]
> 要閱讀有關如何查詢暖資料的更多資訊，請閱讀[API 參考](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters)。

若要開始使用，您需要三個額外項目：

* [時間序列模型](./time-series-insights-update-tsm.md)
* [連線到時間序列深入解析的事件來源](./time-series-insights-how-to-add-an-event-source-iothub.md)
* [流入事件來源的事件](./time-series-insights-send-events.md)，這些事件必須已經對應至該模型，且為有效的 JSON 格式

## <a name="review-preview-limits"></a>查看預覽限制

[!INCLUDE [Review Time Series Insights Preview limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>配置時間序列指示和時間戳記屬性

若要建立新的時間序列深入解析環境，請選取一個時間序列識別碼。 此動作會當作您資料的邏輯分割。 如上述事項，請務必備妥您的時間序列識別碼。

> [!IMPORTANT]
> 時間序列的代號*以後無法更改*。 在最終選取前及第一次使用前，請確認每個識別碼。

您最多可以選擇三個鍵來唯一地區分資源。 如需詳細資訊，請參閱[選擇時間序列識別碼的最佳做法](./time-series-insights-update-how-to-id.md)和[儲存體與輸入](./time-series-insights-update-storage-ingress.md)。

**時間戳記**屬性也很重要。 您可以在新增事件來源時指定此屬性。 每個事件來源都有選擇性的時間戳記屬性，可用來依時間追蹤事件來源。 時間戳記值會區分大小寫，而且格式必須設定為每個事件來源的個別規格。

> [!TIP]
> 請確認您事件來源的格式設定和剖析需求。

保留空白時，系統會使用事件來源內的事件加入佇列時間，作為事件時間戳記。 如果您傳送歷史資料或批次事件，自訂「時間戳記屬性」比預設的「事件加入佇列時間」更有用。 有關詳細資訊，請閱讀有關如何在 Azure [IoT 中心中添加事件源](./time-series-insights-how-to-add-an-event-source-iothub.md)。

## <a name="understand-the-time-series-model"></a>了解時間序列模型

您現在可以設定您時間序列深入解析環境的時間序列模型。 使用新模型很容易就能尋找及分析 IoT 資料。 它能用來鑑藏、維護及擴充時間序列資料，並協助準備取用者可用的資料集。 該模型使用時間序列識別碼，它對應到的執行個體與唯一資源相關聯，且該資源有變數 (稱為類型) 和階層。 了解新的[時間序列模型](./time-series-insights-update-tsm.md)。

模型是動態的，所以隨時可以建置。 若要快速開始使用，請先建置並上傳模型，再將資料推送至時間序列深入解析中。 要構建模型，請閱讀[使用時間序列模型](./time-series-insights-update-how-to-tsm.md)。

對許多客戶而言，時間序列模型對應至現有的資產模型或 ERP 系統。 如果您沒有現有的模型，我們[提供](https://github.com/Microsoft/tsiclient)的預先建置使用者體驗可快速啟動並執行。 若要了解模型能如何協助您，請檢視[範例示範環境](https://insights.timeseries.azure.com/preview/demo)。

## <a name="shape-your-events"></a>塑形您的事件

您可以確認您傳送事件到時間序列深入解析的方式。 在理想情況下，您的事件已妥善且有效率地反正規化。

良好的經驗法則：

* 在時間序列模型中存儲中繼資料。
* 確保時間序列模式、實例欄位和事件僅包含必要的資訊，如時間序列 ID 或時間戳記屬性。

有關詳細資訊，請閱讀[形狀事件](./time-series-insights-send-events.md#supported-json-shapes)。

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>後續步驟

- 查看[Azure 顧問](../advisor/advisor-overview.md)以規劃業務恢復配置選項。
- 在時間序列見解預覽中瞭解有關[存儲和入口](./time-series-insights-update-storage-ingress.md)的更多內容。
- 在時間序列見解預覽中瞭解[資料建模](./time-series-insights-update-tsm.md)。
