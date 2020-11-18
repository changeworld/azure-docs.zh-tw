---
title: 使用時間序列深入解析來儲存和分析您的 Azure IoT 隨插即用裝置遙測 |Microsoft Docs
description: 設定時間序列深入解析環境並與 IoT 中樞連線，以從您的 IoT 隨插即用裝置檢視和分析遙測。
author: lyrana
ms.author: lyhughes
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: aa99b9059fe8e3cd5b0dfe6f7e62bd02012fd144
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422258"
---
# <a name="tutorial-create-and-connect-to-time-series-insights-gen2-to-store-visualize-and-analyze-iot-plug-and-play-device-telemetry"></a>教學課程：建立並連線至時間序列深入解析 Gen2，以儲存、視覺化和分析 IoT 隨插即用裝置遙測

在本教學課程中，您將了解如何建立並正確設定 [Azure 時間序列深入解析 Gen2](https://docs.microsoft.com/azure/time-series-insights/overview-what-is-tsi) (TSI) 環境，以與您的 IoT 隨插即用解決方案整合。 您可以使用 TSI 來收集、處理、儲存、查詢和視覺化物聯網 (IoT) 規模的時間序列資料。

首先，您會佈建 TSI 環境，並將 IoT 中樞連線為串流事件來源。 接著，您將透過模型同步處理來撰寫以 [數位對應項定義語言 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) 為基礎的 TSI 環境時間序列模型，這是您用於溫度控制器和控溫器裝置的範例模型檔案。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

若要避免必須在本機安裝 Azure CLI，您可以使用 Azure Cloud Shell 來設定雲端服務。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="time-series-id-selection"></a>時間序列識別碼選取項目

佈建 TSI 環境時，您需要選取時間序列識別碼。 選取適當的時間序列識別碼是很重要的，因為屬性是不可變的，而且在設定之後就無法變更。 選擇時間序列識別碼就像選擇資料庫的分割區索引鍵一樣。 一般來說，您的 TS 識別碼應該是資產模型的分葉節點。 換句話說，您通常會想選取最細微的資產或發出遙測之感應器的識別碼屬性。

身為 IoT 隨插即用使用者，選取 TS 識別碼的相關問題在於裝置模型中是否有[元件](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#component)。 

![TS 識別碼選取範圍](./media/tutorial-configure-tsi/ts-id-selection-pnp.png)

* 如果您已執行快速入門，而您的 IoT 中樞裝置代表[控溫器](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/Thermostat.json)，請使用 `iot-hub-connection-device-id` 作為您的 TS 識別碼。

* 如果您已執行多個元件 [TemperatureController](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/TemperatureController.json) 的其中一個教學課程，請使用下一節中的複合索引鍵，其撰寫為  `iot-hub-connection-device-id, dt-subject`

## <a name="provision-your-azure-time-series-insights-gen2-environment"></a>佈建 Azure 時間序列深入解析 Gen2 環境

下列命令會執行下列動作：

* 為環境的[冷存放區](https://docs.microsoft.com/azure/time-series-insights/concepts-storage#cold-store)建立 Azure 儲存體帳戶，專為分析長期保留和歷程記錄資料所設計。
  * 以帳戶的唯一名稱取代 `mytsicoldstore`。
* 建立 Azure 時間序列深入解析 Gen2 環境，包括保留期間為 7 天的熱儲存體，以及無限期保留期的冷存放區。 
  * 以 TSI 環境的唯一名稱取代 `my-tsi-env` 
  * 將 `my-pnp-resourcegroup` 取代成您稍早在建立時使用的資源群組名稱
  * 根據上述的選取準則，將 `my-ts-id-property` 取代為您的 TS 識別碼屬性值

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az timeseriesinsights environment longterm create --name my-tsi-env --resource-group $rg --time-series-id-properties my-ts-id-property --sku-name L1 --sku-capacity 1 --data-retention 7 --storage-account-name $storage --storage-management-key $key --location eastus2
```

現在您將設定先前建立的 IoT 中樞作為環境的[事件來源](https://docs.microsoft.com/azure/time-series-insights/concepts-streaming-ingestion-event-sources)。 當事件來源已連線時，TSI 會開始從中樞編製事件的索引，從佇列中最早的事件開始。

首先，在您的 IoT 中樞上為 TSI 環境建立唯一的取用者群組。 以您先前使用的 IoT 中樞名稱取代 `my-pnp-hub`。

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group 
```

連線至 IoT 中樞。 以您自己的值取代 `my-pnp-resourcegroup`、`my-pnp-hub`和 `my-tsi-env`。

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az timeseriesinsights event-source iothub create -g $rg --environment-name $env -n iot-hub-event-source --consumer-group-name tsi-consumer-group  --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id
```
在 [Azure 入口網站](https://portal.azure.com)中瀏覽至您的資源群組，然後選取新建立的時間序列深入解析環境。 造訪執行個體概觀中所顯示的「時間序列深入解析總管 URL」。

![入口網站概觀頁面](./media/tutorial-configure-tsi/view-environment.png)

在總管中，您應該會在「所有階層」底下看到您的兩個控溫器。 接下來，您將根據裝置型號來策展時間序列模型。

![總管視圖 1](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="model-synchronization-between-digital-twins-definition-language-and-time-series-insights-gen2"></a>Digital Twins 定義語言與 Azure 時間序列深入解析 Gen2 之間的模型同步處理

接下來，您會將 DTDL 裝置型號轉譯為 Azure 時間序列深入解析 (TSI) 中的資產模型。 TSI 的時間序列模型是 TSI 中資料 coNtextualization 的語義模型化工具。 時間序列模型有三個主要元件：

* [時間序列模型執行個體](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-instances)。 執行個體是時間序列本身的虛擬標記法。 您可以透過唯一的 TS 識別碼來識別執行個體。
* [時間序列模型階層](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-hierarchies)。 階層藉由指定屬性名稱和其關係來組織執行個體。
* [時間序列模型類型](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-types)。 類型可協助您定義執行計算的[變數](https://docs.microsoft.com/azure/time-series-insights/concepts-variables)或公式。 所有類型都會與特定集區相關聯。

> [!NOTE]
> 下列範例適用於多元件 TemperatureController。

### <a name="define-your-types"></a>定義您的類型

您可以開始將資料內嵌到 Azure 時間序列深入解析 Gen2，而不需要預先定義模型。 當遙測抵達時，TSI 會嘗試根據 TS 識別碼屬性值自動解析時間序列執行個體。 系統會將「預設類型」指派給所有執行個體。 您必須手動建立新的類型來代表您的模型。 下圖描述同步處理 DTDL 模型和 TSM 類型的簡單方法：

![DTDL 至 TSM 類型](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

* 您的數位對應項模型識別碼 (DTMI) 是您的類型識別碼
* 類型名稱可以是模型名稱或顯示名稱
* 模型描述會是類型的描述
* 系統會針對每個具有數值結構描述的遙測元件，至少建立一個類型變數。 
  * 變數只能使用數值資料類型，但如果將值當作可剖析的字串傳送，以 `"0"` 為例，您可以使用[轉換](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions)函式，例如 `toDouble`
* 變數名稱可以是遙測名稱或顯示名稱
* 定義變數時間序列運算式 (TSX)時，請參閱網路上的遙測名稱，以及其資料類型。

> [!NOTE]
> 此範例只會顯示兩個變數，也就是彙總和數值，但每個類型最多可以有 100 個變數。 不同的變數可以參考相同的遙測值，以視需要執行不同的計算。 如需篩選、彙總和純量函式的完整清單，請參閱[時間序列深入解析 Gen2 時間序列運算式語法](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax)文件。

開啟您選擇的文字編輯器，並將下列 JSON 儲存到您的本機磁碟機：

```JSON
{
  "put": [
    {
      "id": "dtmi:com:example:Thermostat;1",
      "name": "Thermostat",
      "description": "Reports current temperature and provides desired temperature control.",
      "variables": {
        "EventCount": {
          "kind": "aggregate",
          "aggregation": {
            "tsx": "count()"
          }
        },
        "Temperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event.temperature.Double"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

在時間序列深入解析總管按一下左側的模型圖示，瀏覽至模型索引標籤。 按一下 [類型]，然後按一下 [上傳 JSON]：

![上傳](./media/tutorial-configure-tsi/upload-type.png)

選取 [選擇檔案]，選取您先前儲存的 JSON，然後按一下 [上傳]

您應該會看到新定義的控溫器類型。

### <a name="optional---create-a-hierarchy"></a>選用 - 若要建立階層

您可以選擇性地建立階層，以組織其 TemeraptureController 父系底下的兩個控溫器元件。

按一下 [階層]，然後選取 [新增階層]。 輸入 `Device Fleet` 作為名稱，並建立一個稱為 `Device Name` 的層級，然後按一下 [儲存]。

![新增階層](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>將您的執行個體指派給正確的類型

接下來，您將變更執行個體的類型，並依需要放置在階層內

選取 [執行個體] 索引標籤，然後按一下最右側的 [編輯] 圖示。

![編輯執行個體](./media/tutorial-configure-tsi/edit-instance.png)

按一下類型下拉式清單，然後選取 `Thermostat`。 

![變更執行個體類型](./media/tutorial-configure-tsi/change-type.png)

如果您已建立階層，請選取 [執行個體欄位] 並選取 `Device Fleet` 方塊。 輸入 `Temperature Controller` 作為其父裝置的值，然後按一下 [儲存]。

![指派給階層](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

針對第二個控溫器重複上述步驟。

### <a name="view-your-data"></a>檢視資料

瀏覽回圖表窗格，並展開所有裝置和 TemperatureController。 按一下 [thermostat1]，選取 `Temperature` 變數，然後按一下 [新增] 來繪製值的圖表。 對 thermostat2 執行相同的動作。

![變更 thermostat2 的執行個體類型](./media/tutorial-configure-tsi/charting-values.png)

## <a name="next-steps"></a>後續步驟

* 若要深入了解各種圖表選項，包括間隔大小和 Y 軸控制項，請參閱 [Azure 時間序列深入解析總管](https://docs.microsoft.com/azure/time-series-insights/concepts-ux-panels)文件。

* 如需環境時間序列模型的深入探討，請參閱[此文章](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview)。

* 若要深入探索查詢 API 和時間序列運算式語法，請[選取](https://docs.microsoft.com/rest/api/time-series-insights/reference-query-apis)。




