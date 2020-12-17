---
title: 什麼是 Azure Digital Twins？
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 的用途的概觀。
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: overview
ms.service: digital-twins
ms.openlocfilehash: 49b7bae1a0cc224b92f292b891fae210f2cffa4e
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400602"
---
# <a name="what-is-azure-digital-twins"></a>什麼是 Azure Digital Twins？

**Azure Digital Twins** 是一種平台即服務 (PaaS) 供應項目，可讓您根據整個環境的數位模型建立知識圖表。 這些環境可能是大樓、工廠、農場、能源網路、鐵路、運動場等，甚至是整個城市。 這些數位模型可用來取得深入解析以推動更優秀的產品、優化作業、降低成本，以及突破性的客戶體驗。

在 Azure Digital Twins 上利用您所屬領域的專業知識，建立自訂的連線解決方案，如下所示：
* 建立任何環境的模型，並以可擴充且安全的方式將數位分身融入生活
* 連線資產，例如 IoT 裝置和現有的商用系統
* 使用健全的事件系統來建立動態商務邏輯和資料處理
* 與 Azure 資料、分析和 AI 服務整合，協助您追蹤過去，然後預測未來

## <a name="azure-digital-twins-capabilities"></a>Azure Digital Twins 功能

以下是 Azure Digital Twins 所提供的功能摘要。

### <a name="open-modeling-language"></a>開啟模型化語言

在 Azure Digital Twins 中，您可以使用稱為 [**模型**](concepts-models.md) 的自訂對應項類型，來定義代表實體環境中人員、地點和事物的數位實體。 

您可以將這些模型定義視為專門的詞彙來描述您的業務。 例如，針對建築物管理解決方案，您可以定義模型 (例如「建築物」、「樓層」和「電梯」)。 然後，您可以根據這些模型建立 **數位分身**，藉此代表您的特定環境。

模型是以類似 JSON 的語言定義，稱為 [數位分身定義語言 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)，並以其狀態屬性、遙測事件、命令、元件和關聯性來描述對應項。
* 模型會定義實體之間的語義 **關聯性**，讓您可以將對應項 連線至反映其互動的知識圖表。 您可以在現實環境的描述中，將模型視為名詞，並將關聯性視為動詞。
* 您也可以使用模型繼承將對應項加以特殊化。 一個模型可以繼承自另一個。

DTDL 是其他 Azure IoT 服務中全程使用的資料模型，包括 [IoT 隨插即用 (PnP)](../iot-pnp/overview-iot-plug-and-play.md) 和 [時間序列深入解析 (TSI)](../time-series-insights/overview-what-is-tsi.md)。 這可協助您保持 Azure Digital Twins 解決方案的連線，並與 Azure 生態系統的其他部分相容。

### <a name="live-execution-environment"></a>即時執行環境

Azure Digital Twins 中的數位模型是現實環境即時的最新表示。 您可以使用自訂 DTDL 模型中的關聯性，將對應項連線至代表您環境的 **即時圖表**。

您可以透過範例應用程式 ([**Azure Digital Twins 總管**](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)) 的說明，檢視 Azure Digital Twins 圖表的視覺效果。

範例視覺效果看起來就像這樣：

:::image type="content" source="media/includes/azure-digital-twins-explorer.png" alt-text="Azure Digital Twins 總管範例應用程式的螢幕擷取畫面，其中顯示代表 Digital Twins 的節點圖表" lightbox="media/includes/azure-digital-twins-explorer.png":::

Azure Digital Twins 提供豐富的 **事件系統**，讓圖表保持在最新的資料處理和商務邏輯。 您可以連線外部計算資源 (例如 [Azure Functions](../azure-functions/functions-overview.md))，以彈性的自訂方式來推動此資料處理流程。

您也可以使用 Azure Digital Twins 功能強大的 **查詢 API**，從即時執行環境中擷取深入解析。 此 API 可讓您藉由豐富的搜尋條件來進行查詢，包括屬性值、關聯性、關聯性屬性、模型資訊等等。 您也可以結合查詢、收集有關您環境的各種深入解析，以及回答對您很重要的自訂問題。

### <a name="input-from-iot-and-business-systems"></a>IoT 和商用系統的輸入

若要讓 Azure Digital Twins 的即時執行環境與現實環境保持在最新狀態，您可以使用 [IoT 中樞](../iot-hub/about-iot-hub.md)，將您的解決方案連線至 IoT 和 IoT Edge 裝置。 這些受中樞管理的裝置會以做為對應項圖表的一部分的方式表示，並提供驅動模型的資料。

您可以藉由 Azure Digital Twins 建立新的 IoT 中樞，或連線現有的 IoT 中樞和已經由其管理的裝置。

您也可以使用 REST API 或其他服務 (如 [Logic Apps](../logic-apps/logic-apps-overview.md)) 的連接器，透過其他資料來源驅動 Azure Digital Twins。

### <a name="output-to-tsi-storage-and-analytics"></a>TSI、儲存體和分析的輸出

您的 Azure Digital Twins 模型中的資料可路由傳送至下游 Azure 服務，進行額外的分析或儲存。 這是透過 **事件路由**，其使用 [事件中樞](../event-hubs/event-hubs-about.md)、[事件方格](../event-grid/overview.md) 或 [服務匯流排](../service-bus-messaging/service-bus-messaging-overview.md) 來驅動您想要的資料流程。

您可以處理的事件路由執行的事項包括：
* 將 Azure Digital Twins 資料儲存在 [Azure Data Lake](../storage/blobs/data-lake-storage-introduction.md)
* 藉由 [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 或其他 Microsoft 資料分析工具來分析 Azure Digital Twins 資料
* 藉由 Logic Apps 整合大型工作流程
* 將 Azure Digital Twins 連線到時間序列深入解析，藉此追蹤每個對應項的時間序列歷程記錄
* 將 Azure Digital Twins 中的來源與時間序列深入解析中的時間序列模型搭配使用

這是 Azure Digital Twins 可以連線到大型解決方案的另一種方式，並支援您的自訂需求，以便繼續使用這些深入解析。

## <a name="azure-digital-twins-in-a-solution-context"></a>解決方案內容中的 Azure Digital Twins

Azure Digital Twins 通常會與其他 Azure 服務搭配使用，以做為大型 IoT 解決方案的一部分。 

使用 Azure Digital Twins 的完整解決方案可能包含以下部分：
* Azure Digital Twins 服務執行個體。 這會儲存您的對應項模型和對應項圖表及其狀態，並協調事件處理流程。
* 一或多個用戶端應用程式，可透過設定模型、建立拓撲，以及從對應項圖表中擷取深入解析，藉此驅動 Azure Digital Twins 執行個體。
* 一或多個外部計算資源，用來處理 Azure Digital Twins 所產生的事件，或連線的資料來源 (例如裝置)。 提供計算資源的常見方式之一是透過 [Azure Functions](../azure-functions/functions-overview.md)。
* 提供裝置管理和 IoT 資料流功能的 IoT 中樞。
* 下游服務，用來處理工作流程整合之類的工作 (例如 [Logic Apps](../logic-apps/logic-apps-overview.md)、冷儲存體、時間序列整合或分析)。

下圖顯示 Azure Digital Twins 位於大行 Azure IoT 解決方案的內容中。

:::image type="content" source="media/overview/solution-context.png" alt-text="此圖顯示輸入來源、輸出服務，以及與用戶端應用程式和外部計算資源之間的雙向通訊。" border="false" lightbox="media/overview/solution-context.png":::

## <a name="service-limits"></a>服務限制

如需 Azure Digital Twins 限制清單，請參閱 [*參考：服務限制：*](reference-service-limits.md)。

## <a name="next-steps"></a>後續步驟

如果您使用第一個預覽版本的 Azure Digital Twins (2018 年 10 月)，請了解變更後的內容：[*概觀：與第一版之間的差異*](overview-differences.md)。

或者，藉由快速入門來深入探索 Azure Digital Twins 的使用：[*快速入門：探索範例案例*](quickstart-adt-explorer.md)。