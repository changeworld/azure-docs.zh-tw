---
title: 規劃您的 Gen2 環境-Azure 時間序列深入解析 |Microsoft Docs
description: 設定、管理、規劃和部署 Azure 時間序列深入解析 Gen2 環境的最佳作法。
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/30/2020
ms.custom: seodec18
ms.openlocfilehash: 1788eba0ef9be781fb7cf23f1eb86b48c9c360e1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91287404"
---
# <a name="plan-your-azure-time-series-insights-gen2-environment"></a>規劃 Azure 時間序列深入解析 Gen2 環境

本文說明使用 Azure 時間序列深入解析 Gen2 來規劃和快速入門的最佳作法。

## <a name="best-practices-for-planning-and-preparation"></a>規劃和準備的最佳做法

下列文章將進一步說明規劃和準備環境的相關最佳做法：

* 當您布建 [Azure 時間序列深入解析 Gen2 環境](#the-gen2-environment)時，會得到什麼結果。
* 您的 [時間序列識別碼和時間戳記屬性是](#configure-time-series-ids-and-timestamp-properties)什麼。
* 新的 [時間序列模型是](#understand-the-time-series-model)什麼，以及如何建立您自己的時間序列模型。
* 如何 [有效率地以 JSON 傳送事件](#shape-your-events)。
* Azure 時間序列深入解析的商務嚴重損壞 [修復選項](#business-disaster-recovery)。

Azure 時間序列深入解析採用隨用隨付的商務模型。 如需有關費用和容量的詳細資訊，請參閱 [Azure 時間序列深入解析定價](https://azure.microsoft.com/pricing/details/time-series-insights/)。

## <a name="the-gen2-environment"></a>Gen2 環境

當您布建 Azure 時間序列深入解析 Gen2 環境時，您會建立兩個 Azure 資源：


* Azure 時間序列深入解析 Gen2 環境
* Azure 儲存體帳戶

在布建過程中，您可以指定是否要啟用暖存放區。 暖存放區可為您提供階層式查詢體驗。 啟用時，您必須指定介於7到30天之間的保留期限。 在暖存放區保留期限內執行的查詢，通常會提供更快速的回應時間。 當查詢橫跨暖存放區保留期限時，就會從冷存放區提供服務。

對於暖存放區的查詢是免費的，而冷存放區上的查詢則會產生成本。 請務必瞭解您的查詢模式，並據以規劃您的暖存放區設定。 建議您將最新資料的互動式分析放在您的暖存放區和模式分析和長期趨勢中。

> [!NOTE]
> 若要閱讀更多有關如何查詢暖資料的資訊，請參閱 [API 參考](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#uri-parameters)。

若要開始使用，您需要三個額外項目：

* [時間序列模型](./concepts-model-overview.md)
* [連線到時間序列深入解析的事件來源](./concepts-streaming-ingestion-event-sources.md)
* [流入事件來源的事件](./time-series-insights-send-events.md)，這些事件必須已經對應至該模型，且為有效的 JSON 格式

## <a name="review-azure-time-series-insights-gen2-limits"></a>複習 Azure 時間序列深入解析 Gen2 限制

[!INCLUDE [Review Azure Time Series Insights Gen2 limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>設定時間序列識別碼和時間戳記屬性

若要建立新的 Azure 時間序列深入解析環境，請選取時間序列識別碼。 此動作會當作您資料的邏輯分割。 如上述事項，請務必備妥您的時間序列識別碼。

> [!IMPORTANT]
> 時間序列識別碼 *稍後無法變更*。 在最終選取前及第一次使用前，請確認每個識別碼。

您最多可以選取三個金鑰，以唯一區分您的資源。 如需詳細資訊，請參閱 [選擇時間序列識別碼](./time-series-insights-update-how-to-id.md) 和內嵌 [規則](concepts-json-flattening-escaping-rules.md)的最佳作法。

**時間戳記**屬性也很重要。 您可以在新增事件來源時指定此屬性。 每個事件來源都有選擇性的時間戳記屬性，可用來依時間追蹤事件來源。 時間戳記值會區分大小寫，而且格式必須設定為每個事件來源的個別規格。

如果保留空白，則會使用事件加入 IoT 中樞或事件中樞的時間，作為事件時間戳記。 一般情況下，使用者應該選擇自訂時間戳記屬性，並使用感應器或標記產生讀取的時間，而不是中樞排入佇列的時間。 如需詳細資訊，以及閱讀時區位移讀取 [事件來源時間戳記](./concepts-streaming-ingestion-event-sources.md#event-source-timestamp)的相關資訊。

## <a name="understand-the-time-series-model"></a>了解時間序列模型

您現在可以設定 Azure 時間序列深入解析環境的時間序列模型。 使用新模型很容易就能尋找及分析 IoT 資料。 它能用來鑑藏、維護及擴充時間序列資料，並協助準備取用者可用的資料集。 該模型使用時間序列識別碼，它對應到的執行個體與唯一資源相關聯，且該資源有變數 (稱為類型) 和階層。 若要深入瞭解，請參閱「 [時間序列模型](./concepts-model-overview.md) 」總覽。

模型是動態的，所以隨時可以建置。 若要快速開始使用，請先建立並上傳，然後再將資料推送至 Azure 時間序列深入解析。 若要建立模型，請參閱 [使用時間序列模型](/azure/time-series-insights/concepts-model-overview)。

對許多客戶而言，時間序列模型對應至現有的資產模型或 ERP 系統。 如果您沒有現有的模型，我們[提供](https://github.com/Microsoft/tsiclient)的預先建置使用者體驗可快速啟動並執行。 若要了解模型能如何協助您，請檢視[範例示範環境](https://insights.timeseries.azure.com/preview/demo)。

## <a name="shape-your-events"></a>塑形您的事件

您可以驗證將事件傳送至 Azure 時間序列深入解析的方式。 在理想情況下，您的事件已妥善且有效率地反正規化。

良好的經驗法則：

* 將中繼資料儲存在您的時間序列模型中。
* 確定時間序列模式、實例欄位和事件只包含必要的資訊，例如時間序列識別碼或時間戳記屬性。

如需詳細資訊，並瞭解如何簡維和儲存事件，請閱讀 [JSON 簡維和轉義規則](./concepts-json-flattening-escaping-rules.md)。

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure Advisor](../advisor/advisor-overview.md) ，以規劃您的商務恢復設定選項。
- 請參閱 [Azure Advisor](../advisor/advisor-overview.md) ，以規劃您的商務恢復設定選項。
- 深入瞭解 Azure 時間序列深入解析 Gen2 中的 [資料](./concepts-ingestion-overview.md) 內嵌。
- 請參閱 Azure 時間序列深入解析 Gen2 中有關 [資料儲存體](./concepts-storage.md) 的文章。
- 瞭解 Azure 時間序列深入解析 Gen2 中的 [資料模型](./concepts-model-overview.md) 。
