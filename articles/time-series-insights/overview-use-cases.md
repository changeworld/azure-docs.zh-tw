---
title: Gen2 使用案例-Azure 時間序列深入解析 Gen2 |Microsoft Docs
description: 深入瞭解 Azure 時間序列深入解析 Gen2 使用案例。
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: seodec18
ms.openlocfilehash: d2d3c94985f099c7a0ab92efab564415f5cd81bb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87097358"
---
# <a name="azure-time-series-insights-gen2-use-cases"></a>Azure 時間序列深入解析 Gen2 使用案例

本文摘要說明 Azure 時間序列深入解析 Gen2 的數個常見使用案例。 本文中的建議可作為開始使用 Azure 時間序列深入解析 Gen2 來開發應用程式和解決方案的起點。

具體而言，本文會回答下列問題：

* Azure 時間序列深入解析 Gen2 有哪些常見的使用案例？
* 使用 Azure 時間序列深入解析 Gen2 進行[資料探索和視覺化異常偵測](#data-exploration-and-visual-anomaly-detection)有哪些優點？
* 使用 Azure 時間序列深入解析 Gen2 進行[操作分析和處理效率](#operational-analysis-and-driving-process-efficiency)的優點為何？
* 使用 Azure 時間序列深入解析 Gen2 進行[advanced analytics](#advanced-analytics)有哪些優點？

下列各節將說明這些使用案例的總覽。

## <a name="introduction"></a>簡介

Azure 時間序列深入解析 Gen2 是端對端的平臺即服務供應專案。 它可用來收集、處理、儲存、分析及查詢高度內容相關且已進行時間序列最佳化的 IoT 級別資料。 這適用于臨機運算元據探索和作業分析。 Azure 時間序列深入解析 Gen2 是唯一可擴充、自訂的服務供應專案，符合產業 IoT 部署的廣泛需求。

## <a name="data-exploration-and-visual-anomaly-detection"></a>資料探索與視覺化的異常偵測

立即探索與分析數十億個事件，並找出異常行為及發掘資料背後隱藏的趨勢。 Azure 時間序列深入解析 Gen2 為您的 IoT 和 DevOps 分析工作負載提供近乎即時的效能。

[![資料瀏覽器](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

大部分的客戶都同意取得深入解析所需的最短時間量是 Azure 時間序列深入解析 Gen2 的最顯著功能之一：

* Azure 時間序列深入解析 Gen2 不需要預先準備資料。
* 只要幾分鐘的時間，就能快速將您連接到 Azure IoT 中樞或 Azure 事件中樞實例中的數十億個事件。
* 一旦連線，您就可以視覺化與分析數十億個事件，並找出異常行為及發掘資料背後隱藏的趨勢。

Azure 時間序列深入解析 Gen2 是直覺且簡單的使用方式。 您不需撰寫任何一行程式碼，即可與您的資料互動。 您也不需要學習任何新語言，雖然 Azure 時間序列深入解析 Gen2 會針對熟悉 SQL 的 advanced 使用者提供更精細的文字型查詢語言。 它還為新手提供了選取及點選探索。

客戶可以利用速度快速地診斷資產相關問題。 他們可以執行 DevOps 分析，以取得 IoT 解決方案中 bug 的根本原因。 它們也可以識別要做為其資料科學計畫一部分進行進一步調查的區域。

有三種主要方式可與 Azure 時間序列深入解析 Gen2 中儲存的資料互動：

* 第一個最簡單的入門方法是使用 Azure 時間序列深入解析 Gen2 Explorer。 您可以使用它在同一個地方，快速地以視覺化方式檢視您所有的 IoT 資料。 它提供的工具如熱度圖，可協助您找出資料中的異常狀況。 它還提供了透視圖。 最多可在單一儀表板中，從一個或多個 Azure 時間序列深入解析 Gen2 環境中比較四個視圖。 儀表板可讓您檢視所有位置的時間序列資料。 深入瞭解[Azure 時間序列深入解析 Gen2 Explorer](./time-series-insights-update-explorer.md)。 若要規劃您的環境，請閱讀[Azure 時間序列深入解析 Gen2 規劃](./time-series-insights-update-plan.md)。

* 第二種開始的方式是使用 JavaScript SDK，在您的 web 應用程式中快速內嵌強大的圖表和圖形。 只需幾行程式碼，您就可以撰寫功能強大的查詢。 您可以使用它們來填入折線圖、圓形圖、橫條圖、熱度圖、資料格等等。 透過使用 SDK，所有這些元素都是立即可用。 SDK 也會將 Azure 時間序列深入解析 Gen2 查詢 Api 抽象化。 您可以使用它們來撰寫類似 SQL 的述詞，以查詢要在儀表板上顯示的資料。 針對混合式展示層解決方案，Azure 時間序列深入解析 Gen2 提供參數化的 Url。 它們會提供 Azure 時間序列深入解析 Gen2 Explorer 的無縫連接點，以深入探討資料。

  * 閱讀有關[JS 用戶端程式庫](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)和[範例用戶端](https://github.com/Microsoft/tsiclient)檔的資訊，以深入瞭解 JavaScript SDK。

  * 若要深入瞭解如何共用 Url 和新的 UI，請查看[Azure 時間序列深入解析 Gen2 Explorer 中的視覺化資料](time-series-insights-update-explorer.md)。

* 第三種開始的方式是使用強大的 Api 來查詢儲存在 Azure 時間序列深入解析 Gen2 中的資料。 Azure 時間序列深入解析 Gen2 具有時態性運算子 `from` ，例如、 `to` 、 `first` 和 `last` 。 它具有匯總和轉換，例如 `average` 、 `sum` 、 `min` 、 `max` 、、等 `time-weighted average` `time-weighted sum` 。它也允許篩選、算術和布林運算子、純量函數等。所有這些運算子都可讓下游應用程式在您的資料中快速尋找有趣的趨勢和模式。 您可以使用它們來填入自主的視覺效果，以找出異常狀況。

## <a name="operational-analysis-and-driving-process-efficiency"></a>營運分析與推動流程效率

使用 Azure 時間序列深入解析 Gen2，以大規模監視設備的健康情況、使用方式和效能，並測量營運效率。 Azure 時間序列深入解析 Gen2 可協助管理各種和無法預測的 IoT 工作負載，而不會犧牲內嵌或查詢效能。

[![總覽](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

如果與正確的技術或解決方案相結合，來自營運流程之資料的串流和連續處理，可以成功地轉換任何業務。 通常這些解決方案是多個系統的組合。 它們可讓您探索和分析經常變更的資料（尤其是在 IoT 領域中），並共用常見的模式。

這些模式通常會從啟用 IoT 的平台開始，這些平台從跨越各種地區設定的裝置和感應器中內嵌數十億個事件。 這些系統會處理並分析資料流資料，以衍生即時的資訊分析與動作。 資料通常會封存到暖和冷存放區，以進行近乎即時和批次分析。

收集的資料會經過一連串的處理，以便針對下游查詢和分析案例來清理資料並加以語境化。 Azure 提供豐富的服務，可套用至像是資產維護和製造等 IoT 案例。 這些服務包括 Azure 時間序列深入解析 Gen2、IoT 中樞、事件中樞、Azure 串流分析、Azure Functions、Azure Logic Apps、Azure Databricks、Azure Machine Learning 和 Power BI。

解決方案架構可以透過下列方式來達成：

* 透過 IoT 中樞或事件中樞內嵌資料，以獲得最佳的安全性、輸送量和延遲。
* 執行資料處理和計算。 漏斗圖透過 Stream Analytics、Logic Apps 和 Azure Functions 等服務內嵌數據。 您所使用的服務取決於特定的資料處理需求。
* 來自處理管線的計算信號會推送至 Azure 時間序列深入解析 Gen2，以供儲存和分析。

Azure 時間序列深入解析 Gen2 提供近乎即時的資料探索，以及歷程記錄資料的資產型深入解析。 根據您的商務需求，MapReduce 和 Hive 作業可以將 Azure 時間序列深入解析 Gen2 連接到 Azure HDInsight，以在 Azure 時間序列深入解析 Gen2 中儲存的資料上執行。 儲存在 Azure 時間序列深入解析 Gen2 中的資料可透過 Azure 時間序列深入解析 Gen2 公用介面查詢 Api，供 Power BI 和其他客戶應用程式使用。 這項資料可用於深入的業務和營運智慧案例。

## <a name="advanced-analytics"></a>進階分析

與 Machine Learning 和 Azure Databricks 這類進階分析服務整合。 Azure 時間序列深入解析 Gen2 從數百萬部裝置會輸入原始資料。 它會加入可由 Azure 分析服務套件順暢使用的內容相關資料。

[![分析](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

進階分析和機器學習服務會取用並處理大量的資料。 這項資料用來制定資料導向決策，並執行預測性分析。 在 IoT 使用案例中，進階分析演算法會從來自數百萬個裝置所收集的資料中學習。 這些裝置每秒會傳輸多次資料。 從 IoT 裝置所收集的資料是未經處理的。 它缺少了內容相關資訊，例如裝置的位置和感應器讀取的單位。 因此，未經處理的資料很難直接供進階分析使用。

Azure 時間序列深入解析 Gen2 以兩種簡單且符合成本效益的方式，將 IoT 資料和先進分析之間的橋樑橋樑：

* 首先，Azure 時間序列深入解析 Gen2 會使用 IoT 中樞，收集來自數百萬部裝置的原始遙測資料。 它透過內容相關資訊來充實資料，並將資料轉換成 parquet 格式。 這種格式可以輕鬆地與其他進階分析服務整合，例如機器學習、Azure Databricks 和第三方應用程式。

    Azure 時間序列深入解析 Gen2 可以做為整個組織所有資料的真實來源。 它為下游分析工作負載建立了一個中央儲存機制。 因為 Azure 時間序列深入解析 Gen2 是近乎即時的儲存體服務，所以先進的分析模型可以從傳入的 IoT 遙測資料中持續學習。 如此一來，模型可以進行更精確的預測。

* 其次，機器學習和預測模型的輸出可以送到 Azure 時間序列深入解析 Gen2，以視覺化和儲存其結果。 此程序可協助組織最佳化並調整其模型。 Azure 時間序列深入解析 Gen2 可讓您輕鬆地在與定型模型輸出相同的平面上，將串流遙測資料視覺化。 如此一來，它可以協助資料科學小組找出異常狀況並識別模式。

## <a name="next-steps"></a>後續步驟

* 深入瞭解[Azure 時間序列深入解析 Gen2 Explorer](./time-series-insights-update-explorer.md)。
* 閱讀[Azure 時間序列深入解析 Gen2 的最佳作法](./time-series-insights-update-plan.md)，以規劃您的環境。
* 閱讀[範例用戶端](https://github.com/Microsoft/tsiclient)檔。
