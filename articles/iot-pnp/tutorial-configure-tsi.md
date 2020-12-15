---
title: 使用 Azure 時間序列深入解析來儲存和分析您的 Azure IoT 隨插即用裝置遙測
description: 設定時間序列深入解析環境並與 IoT 中樞連線，以從您的 IoT 隨插即用裝置檢視和分析遙測。
author: lyrana
ms.author: lyhughes
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5491df61a1198e8eee4ba4701ccfc56154ec75eb
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905075"
---
# <a name="preview-tutorial-create-and-configure-a-time-series-insights-gen2-environment"></a>預覽教學課程：建立和設定時間序列深入解析 Gen2 環境

在本教學課程中，您將了解如何建立和設定 [Azure 時間序列深入解析 Gen2](../time-series-insights/overview-what-is-tsi.md) 環境，以與您的 IoT 隨插即用解決方案整合。 使用時間序列深入解析來收集、處理、儲存、查詢和視覺化物聯網 (IoT) 規模的時間序列資料。

首先，您要佈建時間序列深入解析環境，並將 IoT 中樞連線為串流事件來源。 然後您會透過模型同步來撰寫您的[時間序列模型](../time-series-insights/concepts-model-overview.md)。 您會使用[數位對應項定義語言 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) 範例模型檔案，也就是您用於溫度控制器和控溫器裝置的檔案。

> [!NOTE]
> 時間序列深入解析與 IoT 隨插即用之間的整合目前為預覽狀態。 DTDL 裝置模型對應至「時間序列深入解析」時間序列模型的方式可能會變更。 

## <a name="prerequisites"></a>必要條件

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

至此，您已擁有︰

* Azure IoT 中樞。
* 連結至您 IoT 中樞的裝置佈建服務 (DPS) 執行個體。 DPS 執行個體應該具有 IoT 隨插即用裝置的個別裝置註冊。
* 從串流模擬資料的單一元件裝置或多元件裝置連線到您的 IoT 中樞。

若要避免必須在本機安裝 Azure CLI，您可以使用 Azure Cloud Shell 來設定雲端服務。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-your-event-source"></a>準備您的事件來源

您先前建立的 IoT 中樞將會是時間序列深入解析環境的[事件來源](../time-series-insights/concepts-streaming-ingestion-event-sources.md)。

> [!IMPORTANT]
> 請停用任何現有的 IoT 中樞路由。 使用已設定[路由](../iot-hub/iot-hub-devguide-messages-d2c.md#routing-endpoints)的 IoT 中樞時，有一個已知的問題。 請暫時停用任何路由端點。 當 IoT 中樞連線到時間序列深入解析時，您可以再重新啟用端點。

在 IoT 中樞上，建立唯一的取用者群組，以供時間序列深入解析使用。 在下列範例中，將 `my-pnp-hub` 取代為您先前使用的 IoT 中樞名稱。

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group
```

## <a name="choose-a-time-series-id"></a>選擇時間序列識別碼

佈建時間序列深入解析環境時，必須選取 *時間序列識別碼*。 請務必選取適當的時間序列識別碼。 此屬性是不可變的，在設定之後就無法變更。 時間序列識別碼就像是資料庫分割區索引鍵。 時間序列識別碼會作為時間序列模型的主索引鍵。 如需詳細資訊，請參閱[選擇時間序列識別碼的最佳做法](../time-series-insights/how-to-select-tsid.md)。

身為 IoT 隨插即用使用者，請為時間序列識別碼指定包含 `iothub-connection-device-id` 和 `dt-subject` 的 _複合索引鍵_。 IoT 中樞會分別新增包含 IoT 隨插即用裝置識別碼的這些系統屬性以及裝置元件名稱。

即使 IoT 隨插即用裝置型號目前未使用元件，仍應將 `dt-subject` 納入為複合索引鍵的一部分，以便日後可以使用元件。 時間序列識別碼是不可變的，因此 Microsoft 建議您啟用此選項，以防日後需要用到。

> [!NOTE]
> 本文中的範例適用於多元件 `TemperatureController` 裝置。 但無元件 `Thermostat` 裝置的概念也相同。

## <a name="provision-your-time-series-insights-environment"></a>佈建時間序列深入解析環境

本節說明如何佈建 Azure 時間序列深入解析 Gen2 環境。

執行下列命令可讓您：

* 為環境的[冷存放區](../time-series-insights/concepts-storage.md#cold-store)建立 Azure 儲存體帳戶。 此帳戶是針對記錄資料的長期保留和分析所設計的。
  * 在您的程式碼中，以冷儲存體帳戶的唯一名稱取代 `mytsicoldstore`。
* 建立 Azure 時間序列深入解析 Gen2 環境。 系統會使用保留期間為七天的暖儲存體來建立環境。 冷儲存體帳戶將會針對無限期保留目的而附加。
  * 在您的程式碼中，將 `my-tsi-env` 取代為時間序列深入解析環境的唯一名稱。
  * 在您的程式碼中，以您稍早在設定時使用的資源群組名稱取代 `my-pnp-resourcegroup`。
  * 您的時間序列識別碼屬性為 `iothub-connection-device-id, dt-subject`。

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az timeseriesinsights environment longterm create --name my-tsi-env --resource-group $rg --time-series-id-properties iothub-connection-device-id, dt-subject --sku-name L1 --sku-capacity 1 --data-retention 7 --storage-account-name $storage --storage-management-key $key --location eastus2
```

連接 IoT 中樞事件來源。 以您選擇的值取代 `my-pnp-resourcegroup`、`my-pnp-hub` 和 `my-tsi-env`。 下列命令會參考先前所建立時間序列深入解析的取用者群組：

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az timeseriesinsights event-source iothub create -g $rg --environment-name $env -n iot-hub-event-source --consumer-group-name tsi-consumer-group  --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id
```

在 [Azure 入口網站](https://portal.azure.com)中瀏覽至您的資源群組，然後選取新的時間序列深入解析環境。 移至執行個體概觀中所顯示的「時間序列深入解析總管 URL」：

![入口網站概觀頁面的螢幕擷取畫面。](./media/tutorial-configure-tsi/view-environment.png)

在總管中，您會看到三個執行個體：

* &lt;您的 pnp 裝置識別碼&gt;：thermostat1
* &lt;您的 pnp 裝置識別碼&gt;：thermostat2
* &lt;您的 pnp 裝置識別碼&gt;：`null`

> [!NOTE]
> 第三個標籤代表來自 `TemperatureController` 本身的遙測，例如裝置記憶體的工作集。 因為這是最上層屬性，所以元件名稱的值是 null。 在後續步驟中，您可以將此名稱改得更好記。

![在總管中顯示三個執行個體的螢幕擷取畫面。](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="configure-model-translation"></a>設定模型轉譯

接下來，您要將 DTDL 裝置型號轉譯為 Azure 時間序列深入解析中的資產模型。 在時間序列深入解析中，時間序列模型是資料 coNtextualization 的語義模型化工具。 模型有三個主要元件：

* [時間序列模型執行個體](../time-series-insights/concepts-model-overview.md#time-series-model-instances)是時間序列本身的虛擬標記法。 執行個體可透過時間序列識別碼來唯一識別。
* [時間序列模型階層](../time-series-insights/concepts-model-overview.md#time-series-model-hierarchies)會藉由指定屬性名稱和其關係來組織執行個體。
* [時間序列模型類型](../time-series-insights/concepts-model-overview.md#time-series-model-types)可協助您定義計算的[變數](../time-series-insights/concepts-variables.md)或公式。 所有類型都會與特定集區相關聯。

### <a name="define-your-types"></a>定義類型

您可以開始將資料內嵌到 Azure 時間序列深入解析 Gen2，而不需要預先定義模型。 當遙測抵達時，時間序列深入解析會嘗試根據時間序列識別碼屬性值來自動解析時間序列執行個體。 所有執行個體都會獲得系統指派的「預設類型」。 您必須手動建立新的類型，以正確分類執行個體。 

下列詳細資料會概述可讓裝置 DTDL 模型與時間序列模型類型進行同步的最簡單方法：

* 數位對應項模型識別碼會變成類型識別碼。
* 類型名稱可以是模型名稱或顯示名稱。
* 模型描述會變成類型的描述。
* 系統會針對每個具有數值結構描述的遙測，至少建立一個類型變數。
  * 變數只能使用數值資料類型，但如果將值以其他可轉換的類型形式進行傳送，以 `"0"` 為例，您就可以使用[轉換](/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions)函式，例如 `toDouble`。
* 變數名稱可以是遙測名稱或顯示名稱。
* 定義時間序列運算式變數時，請參閱網路上的遙測名稱及其資料類型。

| DTDL JSON | 時間序列模型類型 JSON | 範例值 |
|-----------|------------------|-------------|
| `@id` | `id` | `dtmi:com:example:TemperatureController;1` |
| `displayName`    | `name`   |   `Temperature Controller`  |
| `description`  |  `description`  |  `Device with two thermostats and remote reboot.` |  
|`contents` (陣列)| `variables` (物件)  | 請參閱下列範例。

![顯示 DTDL 對應至時間序列模型類型的螢幕擷取畫面。](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

> [!NOTE]
> 這個範例會顯示三個變數，但每個類型最多只能有 100 個變數。 不同的變數可以參考相同的遙測值，以視需要執行不同的計算。 如需篩選、彙總和純量函式的完整清單，請參閱[時間序列深入解析 Gen2 時間序列運算式語法](/rest/api/time-series-insights/reference-time-series-expression-syntax)。

開啟文字編輯器，並將下列 JSON 儲存到您的本機磁碟機。

```JSON
{
  "put": [
    {
      "id": "dtmi:com:example:TemperatureController;1",
      "name": "Temperature Controller",
      "description": "Device with two thermostats and remote reboot.",
      "variables": {
        "workingSet": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.workingSet.Long, toLong($event.workingSet.Double))"
          }, 
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "temperature": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.temperature.Long, toLong($event.temperature.Double))"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "eventCount": {
          "kind": "aggregate",
          "aggregation": {
            "tsx": "count()"
          }
        }
      }
    }
  ]
}
```

在時間序列深入解析總管中，選取左側的 [模型] 圖示以開啟 [模型] 索引標籤。依序選取 [類型] 和 [上傳 JSON]：

![顯示如何上傳 JSON 的螢幕擷取畫面。](./media/tutorial-configure-tsi/upload-type.png)

選取 [選擇檔案]，選取您先前儲存的 JSON，然後選取 [上傳]。

您會看到新定義的 **溫度控制器** 類型。

### <a name="create-a-hierarchy"></a>建立階層

建立階層將標籤組織到其 `TemperatureController` 父系底下。 下列簡單範例只有單一層級，但 IoT 解決方案一般會有許多巢狀層級，以便在標籤位於組織內的實體和語義位置中設定標籤的情境。

選取 [階層]，然後選取 [新增階層]。 在 [名稱] 中，輸入「裝置群」。 建立一個名為「裝置名稱」的層級。 接著，選取 [儲存]。

![顯示如何新增參階層螢幕擷取畫面。](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>將您的執行個體指派給正確的類型

接下來，您要變更執行個體的類型，並將其放入階層中。

選取 [執行個體] 索引標籤。尋找代表裝置工作集的執行個體，然後選取最右側的 [編輯] 圖示。

![顯示如何編輯執行個體的螢幕擷取畫面。](./media/tutorial-configure-tsi/edit-instance.png)

開啟 [類型] 下拉式功能表，然後選取 [溫度控制器]。 輸入 *defaultComponent, <your device name>* ，以更新執行個體 (代表與裝置相關聯的所有最上層標籤) 的名稱。

![顯示如何變更執行個體類型的螢幕擷取畫面。](./media/tutorial-configure-tsi/change-type.png)

選取 [儲存] 之前，請先選取 [執行個體欄位] 索引標籤，然後選取 [裝置群]。 若要將遙測群組在一起，請輸入 *\<your device name> - Temp Controller*。 接著，選取 [儲存]。

![顯示如何將執行個體指派給階層的螢幕擷取畫面](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

重複上述步驟，為控溫器標籤指派正確的類型和階層。

## <a name="view-your-data"></a>檢視資料

回到 [圖表] 窗格，並展開 [裝置群] > [您的裝置]。 選取 [thermostat1]、選取 [溫度] 變數，然後選取 [新增] 來繪製值的圖表。 針對 **thermostat2** 和 **defaultComponent** **workingSet** 值執行相同的動作。

![顯示如何變更 thermostat2 執行個體類型的螢幕擷取畫面。](./media/tutorial-configure-tsi/charting-values.png)

## <a name="next-steps"></a>後續步驟

* 若要深入了解各種圖表選項，包括間隔大小和 Y 軸控制項，請參閱 [Azure 時間序列深入解析總管](../time-series-insights/concepts-ux-panels.md)。

* 如需環境的時間序列模型深入概觀，請參閱 [Azure 時間序列深入解析 Gen2 中的時間序列模型](../time-series-insights/concepts-model-overview.md)。

* 若要深入探索查詢 API 和時間序列運算式語法，請參閱 [Azure 時間序列深入解析 Gen2 查詢 API](/rest/api/time-series-insights/reference-query-apis)。
