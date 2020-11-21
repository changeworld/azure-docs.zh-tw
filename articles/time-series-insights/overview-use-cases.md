---
title: Gen2 使用案例-Azure 時間序列深入解析 Gen2 |Microsoft Docs
description: 瞭解 Azure 時間序列深入解析 Gen2 的使用案例。
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: b8f13a20232fab61dc082c1b12b7ddaa11807554
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016185"
---
# <a name="azure-time-series-insights-gen2-use-cases"></a>Azure 時間序列深入解析 Gen2 使用案例

本文摘要說明 Azure 時間序列深入解析 Gen2 的數個常見使用案例。 本文中的建議可作為開始開發您的應用程式和解決方案 Azure 時間序列深入解析 Gen2 的起點。

具體而言，本文會回答下列問題：

* Azure 時間序列深入解析 Gen2 有哪些常見的使用案例？
* 使用 Azure 時間序列深入解析 Gen2 進行 [資料探索和 visual 異常偵測](#data-exploration-and-visual-anomaly-detection)有哪些優點？
* 使用 Azure 時間序列深入解析 Gen2 來進行 [營運分析和流程效率](#operational-analysis-and-driving-process-efficiency)有哪些優點？
* 使用 Azure 時間序列深入解析 Gen2 進行 [advanced analytics](#advanced-analytics)有哪些優點？

下列各節將說明這些使用案例的總覽。

## <a name="introduction"></a>簡介

Azure 時間序列深入解析 Gen2 是端對端的平臺即服務供應專案。 它可用來收集、處理、儲存、分析及查詢高度內容相關且已進行時間序列最佳化的 IoT 級別資料。 它很適合用於臨機運算元據探索和作業分析。 Azure 時間序列深入解析 Gen2 是可唯一擴充的自訂服務供應專案，可滿足產業 IoT 部署的廣泛需求。

## <a name="data-exploration-and-visual-anomaly-detection"></a>資料探索與視覺化的異常偵測

立即探索與分析數十億個事件，並找出異常行為及發掘資料背後隱藏的趨勢。 Azure 時間序列深入解析 Gen2 可為您的 IoT 和 DevOps 分析工作負載提供近乎即時的效能。

[![資料 explorer](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

大部分的客戶都同意取得深入解析所需的最少時間，是 Azure 時間序列深入解析 Gen2 的其中一項最顯著功能：

* Azure 時間序列深入解析 Gen2 不需要預先準備資料。
* 只要幾分鐘的時間，就能快速將您連線到 Azure IoT 中樞中的數十億個事件或 Azure 事件中樞實例。
* 一旦連線，您就可以視覺化與分析數十億個事件，並找出異常行為及發掘資料背後隱藏的趨勢。

Azure 時間序列深入解析 Gen2 很直覺且容易使用。 您不需撰寫任何一行程式碼，即可與您的資料互動。 您也不需要學習新的語言，雖然 Azure 時間序列深入解析 Gen2 為熟悉 SQL 的 advanced 使用者提供更精細的文字查詢語言。 它還為新手提供了選取及點選探索。

客戶可以利用快速診斷資產相關問題的速度。 他們可以執行 DevOps 分析，以取得 IoT 解決方案中錯誤的根本原因。 它們也可以在資料科學計畫中識別要進一步調查的區域。

有三種主要方式可與 Azure 時間序列深入解析 Gen2 中儲存的資料互動：

* 開始使用的第一個和最簡單的方式是使用 Azure 時間序列深入解析 Gen2 Explorer。 您可以使用它在同一個地方，快速地以視覺化方式檢視您所有的 IoT 資料。 它提供熱度圖之類的工具，可協助您找出資料中的異常。 它還提供了透視圖。 使用它可在單一儀表板中，從一或多個 Azure 時間序列深入解析 Gen2 環境比較最多四個視圖。 儀表板可讓您檢視所有位置的時間序列資料。 深入瞭解 [Azure 時間序列深入解析 Gen2 Explorer](./concepts-ux-panels.md)。 若要規劃您的環境，請閱讀 [Azure 時間序列深入解析 Gen2 規劃](./how-to-plan-your-environment.md)。

* 第二個開始的方法是使用 JavaScript SDK，在您的 web 應用程式中快速內嵌功能強大的圖表和圖形。 只需幾行程式碼，您就可以撰寫功能強大的查詢。 您可以使用它們來填入折線圖、圓形圖、橫條圖、熱度圖、資料方格等等。 透過使用 SDK，所有這些元素都是立即可用。 SDK 也會將 Azure 時間序列深入解析 Gen2 查詢 Api 抽象化。 您可以使用它們來撰寫類似 SQL 的述詞，以查詢要在儀表板上顯示的資料。 針對混合式展示層解決方案，Azure 時間序列深入解析 Gen2 提供參數化的 Url。 他們使用 Azure 時間序列深入解析 Gen2 Explorer 提供順暢的連接點，以便深入探討資料。

  * 請參閱 [JS 用戶端程式庫](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) 和 [範例用戶端](https://github.com/Microsoft/tsiclient) 檔，以深入瞭解 JavaScript SDK。

  * 查看 [Azure 時間序列深入解析 Gen2 Explorer 中的資料視覺化，以](./concepts-ux-panels.md)深入瞭解共用 url 和新的 UI。

* 第三個開始的方法是使用強大的 Api 來查詢 Azure 時間序列深入解析 Gen2 中儲存的資料。 Azure 時間序列深入解析 Gen2 具有時態性運算子 `from` ，例如、 `to` 、 `first` 和 `last` 。 它具有匯總和轉換，例如 `average` 、 `sum` 、 `min` 、 `max` 、 `time-weighted average` 、等等 `time-weighted sum` 。它也允許篩選、算術和布林運算子、純量函數等等。所有這些運算子都可讓下游應用程式快速找出您資料中的有趣趨勢與模式。 您可以使用它們來填入自主視覺效果，以找出異常狀況。

## <a name="operational-analysis-and-driving-process-efficiency"></a>營運分析與推動流程效率

使用 Azure 時間序列深入解析 Gen2 來大規模監視設備的健康情況、使用量和效能，以及測量營運效率。 Azure 時間序列深入解析 Gen2 可協助管理各種和無法預測的 IoT 工作負載，而不會犧牲內嵌或查詢的效能。

[![螢幕擷取畫面顯示 Azure 時間序列深入解析 Gen2 中的 T 裝置/應用程式資料、串流處理、作業效率、智慧/見解和先進分析。](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

如果與正確的技術或解決方案相結合，來自營運流程之資料的串流和連續處理，可以成功地轉換任何業務。 通常這些解決方案是多個系統的組合。 它們可讓您探索及分析不斷變更的資料（尤其是在 IoT 領域中），並共用一般模式。

這些模式通常會從啟用 IoT 的平台開始，這些平台從跨越各種地區設定的裝置和感應器中內嵌數十億個事件。 這些系統會處理並分析資料流資料，以衍生即時的資訊分析與動作。 資料通常會封存至暖和冷存放區，以進行近乎即時和批次分析。

收集的資料會經過一連串的處理，以便針對下游查詢和分析案例來清理資料並加以語境化。 Azure 提供豐富的服務，可套用至像是資產維護和製造等 IoT 案例。 這些服務包括 Azure 時間序列深入解析 Gen2、IoT 中樞、事件中樞、Azure 串流分析、Azure Functions、Azure Logic Apps、Azure Databricks、Azure Machine Learning 和 Power BI。

解決方案架構可以透過下列方式來達成：

* 透過 IoT 中樞或事件中樞內嵌資料，以獲得最佳的安全性、輸送量和延遲。
* 執行資料處理和計算。 漏斗圖透過 Stream Analytics、Logic Apps 和 Azure Functions 等服務內嵌數據。 您所使用的服務取決於特定的資料處理需求。
* 處理管線的計算信號會推送至 Azure 時間序列深入解析 Gen2 以進行儲存和分析。

Azure 時間序列深入解析 Gen2 透過歷程記錄資料，提供近乎即時的資料探索和資產型見解。 根據您的商務需求，MapReduce 和 Hive 作業可透過將 Azure 時間序列深入解析 Gen2 連接至 Azure HDInsight，在儲存于 Azure 時間序列深入解析 Gen2 中的資料上執行。 Azure 時間序列深入解析 Gen2 中儲存的資料，可透過 Azure 時間序列深入解析 Gen2 公用介面查詢 Api，供 Power BI 和其他客戶應用程式使用。 這項資料可用於深入的業務和營運智慧案例。

## <a name="advanced-analytics"></a>進階分析

與 Machine Learning 和 Azure Databricks 這類進階分析服務整合。 Azure 時間序列深入解析 Gen2 從數百萬部裝置 ingresses 原始資料。 它會加入可由 Azure 分析服務套件順暢使用的內容相關資料。

[![分析](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

進階分析和機器學習服務會取用並處理大量的資料。 這項資料用來制定資料導向決策，並執行預測性分析。 在 IoT 使用案例中，進階分析演算法會從來自數百萬個裝置所收集的資料中學習。 這些裝置每秒會傳輸多次資料。 從 IoT 裝置所收集的資料是未經處理的。 它缺少了內容相關資訊，例如裝置的位置和感應器讀取的單位。 因此，未經處理的資料很難直接供進階分析使用。

Azure 時間序列深入解析 Gen2 能以兩種簡單且符合成本效益的方式，來橋接 IoT 資料和先進分析之間的橋樑：

* 首先，Azure 時間序列深入解析 Gen2 會使用 IoT 中樞從數百萬個裝置收集原始遙測資料。 它透過內容相關資訊來充實資料，並將資料轉換成 parquet 格式。 這種格式可以輕鬆地與其他進階分析服務整合，例如機器學習、Azure Databricks 和第三方應用程式。

    Azure 時間序列深入解析 Gen2 可以作為整個組織中所有資料的真實來源。 它為下游分析工作負載建立了一個中央儲存機制。 因為 Azure 時間序列深入解析 Gen2 是近乎即時的儲存體服務，所以先進的分析模型可以從傳入的 IoT 遙測資料中持續學習。 如此一來，模型可以進行更精確的預測。

* 其次，您可以將機器學習和預測模型的輸出送入 Azure 時間序列深入解析 Gen2，以視覺化並儲存其結果。 此程序可協助組織最佳化並調整其模型。 Azure 時間序列深入解析 Gen2 可讓您輕鬆地將相同平面上的串流遙測資料視覺化，如同定型的模型輸出。 如此一來，它可以協助資料科學小組找出異常狀況並識別模式。

## <a name="next-steps"></a>下一步

* 深入瞭解 [Azure 時間序列深入解析 Gen2 Explorer](./concepts-ux-panels.md)。
* 閱讀 [Azure 時間序列深入解析 Gen2 的最佳作法](./how-to-plan-your-environment.md) ，以規劃您的環境。
* 閱讀 [範例用戶端](https://github.com/Microsoft/tsiclient) 檔。