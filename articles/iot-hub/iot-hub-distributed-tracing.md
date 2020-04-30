---
title: 將相互關聯識別碼新增至具有分散式追蹤的 IoT 訊息（預先）
description: 瞭解如何使用分散式追蹤功能來追蹤解決方案所使用的 Azure 服務中的 IoT 訊息。
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: jlian
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 2b1dc7873140f885ec3efac11dec5fbf6aab7aa9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732567"
---
# <a name="trace-azure-iot-device-to-cloud-messages-with-distributed-tracing-preview"></a>透過分散式追蹤來追蹤 Azure IoT 裝置到雲端的訊息 (預覽)

Microsoft Azure IoT 中樞目前支援分散式追蹤作為[預覽功能](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

IoT 中樞是其中一項最先支援分散式追蹤的 Azure 服務。 隨著更多 Azure 服務支援分散式追蹤，您將能夠在您解決方案的相關 Azure 服務中追蹤 IoT 訊息。 如需了解分散式追蹤的背景，請參閱[分散式追蹤](../azure-monitor/app/distributed-tracing.md)。

啟用 IoT 中樞的分散式的追蹤，讓您能夠：

- 使用[追蹤內容](https://github.com/w3c/trace-context)透過 IoT 中樞精確地監視每則訊息的流程。 此追蹤內容包括相互關聯識別碼，其可讓某個元件的事件與另一個元件的事件相互關聯。 使用[裝置對應項](iot-hub-devguide-device-twins.md)，可以針對部分或所有的 IoT 裝置訊息套用該追蹤內容。
- 自動將追蹤內容記錄到 [Azure 監視器診斷記錄](iot-hub-monitor-resource-health.md)。
- 測量並了解訊息流程，以及從裝置到 IoT 中樞和路由端點的延遲。
- 開始考慮您要在 IoT 解決方案中如何實作非 Azure 服務的分散式追蹤。

在本文中，您會使用 [適用於 C 的 Azure IoT 裝置 SDK](iot-hub-device-sdk-c-intro.md) 搭配分散式追蹤。 其他 SDK 的分散式追蹤支援仍在進行中。

## <a name="prerequisites"></a>先決條件

- 分散式追蹤的預覽版目前僅支援在下列區域建立的 IoT 中樞：

  - **北歐**
  - **東南亞**
  - **美國西部 2**

- 本文假設您已熟悉將遙測訊息傳送至 IoT 中樞。 請確定您已完成[傳送遙測 C 快速入門](quickstart-send-telemetry-c.md)。

- 向您的 IoT 中樞註冊裝置 (每個快速入門都會提供相關步驟)，並記下連接字串。

- 安裝最新版的 [Git](https://git-scm.com/download/)。

## <a name="configure-iot-hub"></a>設定 IoT 中樞

在本節中，您會設定 IoT 中樞以記錄分散式追蹤屬性 (相互關聯識別碼和時間戳記)。

1. 在[Azure 入口網站](https://portal.azure.com/)中，流覽至您的 IoT 中樞。

1. 在 IoT 中樞的左窗格中，向下捲動至 [監視]**** 區段，然後按一下 [診斷設定]****。

1. 如果尚未開啟診斷設定，請按一下 [開啟診斷]****。 如果已經啟用診斷設定，請按一下 [新增診斷設定]****。

1. 在 [名稱]**** 欄位中，輸入新診斷設定的名稱。 例如，**DistributedTracingSettings**。

1. 選擇下列其中一個或多個選項，以決定記錄要傳送到哪裡：

    - 封存**至儲存體帳戶**：設定儲存體帳戶以包含記錄資訊。
    - **串流至事件中樞**：將事件中樞設定為包含記錄資訊。
    - **傳送至 Log analytics**：將 Log analytics 工作區設定為包含記錄資訊。

1. 在 [記錄]**** 區段中，選取您希望記錄資訊用於的作業。

    請務必包含 **DistributedTracing**，並針對您想要保留記錄的天數設定 [保留期]****。 記錄保留期會影響儲存成本。

    ![螢幕擷取畫面，其中顯示 IoT 診斷設定的 DistributedTracing 類別](./media/iot-hub-distributed-tracing/diag-logs.png)

1. 針對新設定按一下 [儲存]****。

1. (選擇性) 若要查看流向不同地方的訊息，請設定[送到至少兩個不同端點的路由規則](iot-hub-devguide-messages-d2c.md)。

一旦開啟記錄功能，在下列任何情況下遇到包含有效追蹤屬性的訊息時，IoT 中樞就會記載記錄：

- 訊息抵達 IoT 中樞的閘道。
- 訊息已由 IoT 中樞處理。
- 訊息已路由傳送到自訂端點。 必須啟用路由。

若要深入了解這些記錄及其結構描述，請參閱 [IoT 中樞診斷記錄中的分散式追蹤](iot-hub-monitor-resource-health.md#distributed-tracing-preview)。

## <a name="set-up-device"></a>設定裝置

在本節中，您會準備可搭配 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 使用的開發環境。 接著，您會修改其中一個範例，以在您裝置的遙測訊息上啟用分散式追蹤。

這些指示適用於在 Windows 上建置範例。 若為其他環境，請參閱[編譯 C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compile) 或[特定平台開發的預先封裝 C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#prepackaged-c-sdk-for-platform-specific-development)。

### <a name="clone-the-source-code-and-initialize"></a>複製原始程式碼並初始化

1. 安裝 Visual Studio 2019 的「[使用 c + + 進行桌面開發」工作負載](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2019)。 也支援 Visual Studio 2017 和2015。

1. 安裝 [CMake](https://cmake.org/)。 在命令提示字元中輸入 `cmake -version`，確定它位於您的 `PATH` 中。

1. 開啟命令提示字元或 Git Bash 殼層。 執行下列命令，以複製最新版本的[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 存放庫：

    ```cmd
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    預期此作業需要幾分鐘的時間才能完成。

1. 在 git 存放庫的根目錄中建立 `cmake` 子目錄，並瀏覽至該資料夾。 從 `azure-iot-sdk-c` 目錄執行下列命令：

    ```cmd
    mkdir cmake
    cd cmake
    cmake ..
    ```

    如果 `cmake` 找不到 C++ 編譯器，您在執行上述命令時，可能會收到建置錯誤。 如果發生這種情況，請嘗試在 [Visual Studio 命令提示字元](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)中執行此命令。 

    建置成功後，最後幾行輸出會類似於下列輸出：

    ```cmd
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

### <a name="edit-the-send-telemetry-sample-to-enable-distributed-tracing"></a>編輯傳送遙測範例以啟用分散式追蹤

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/azure-iot-distributed-tracing-sample/blob/master/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c" target="_blank">取得 GitHub 上的範例</a>

1. 使用編輯器來開啟 `azure-iot-sdk-c/iothub_client/samples/iothub_ll_telemetry_sample/iothub_ll_telemetry_sample.c` 來源檔案。

1. 尋找 `connectionString` 常數的宣告：

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=2)]

    使用您在[傳送遙測 C 快速入門](./quickstart-send-telemetry-c.md)的[註冊裝置](./quickstart-send-telemetry-c.md#register-a-device)一節中記下的裝置連接字串，取代 `connectionString` 常數的值。

1. 將 `MESSAGE_COUNT` 定義變更為 `5000`：

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=3)]

1. 尋找呼叫 `IoTHubDeviceClient_LL_SetConnectionStatusCallback` 的程式碼行，以註冊傳送訊息迴圈之前的連線狀態回呼函式。 如下所示，新增該行底下的程式碼來呼叫 `IoTHubDeviceClient_LL_EnablePolicyConfiguration`，以啟用裝置的分散式追蹤：

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_tracing&highlight=5)]

    `IoTHubDeviceClient_LL_EnablePolicyConfiguration` 函式可針對透過[裝置對應項](./iot-hub-devguide-device-twins.md)設定的特定 IoT 中樞功能啟用原則。 使用上述程式碼行啟用 `POLICY_CONFIGURATION_DISTRIBUTED_TRACING` 後，裝置的追蹤行為會反映對裝置對應項進行的分散式追蹤變更。

1. 為了讓範例應用程式繼續執行，而不會用盡您所有的配額，請在傳送訊息迴圈的結尾新增一秒鐘的延遲：

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_sleep&highlight=8)]

### <a name="compile-and-run"></a>編譯和執行

1. 從您先前建立的 CMake 目錄 (`azure-iot-sdk-c/cmake`) 瀏覽至 *iothub_ll_telemetry_sample* 專案目錄，然後編譯範例：

    ```cmd
    cd iothub_client/samples/iothub_ll_telemetry_sample
    cmake --build . --target iothub_ll_telemetry_sample --config Debug
    ```

1. 執行應用程式。 裝置會傳送遙測支援分散式追蹤。

    ```cmd
    Debug/iothub_ll_telemetry_sample.exe
    ```

1. 保持應用程式執行。 藉由查看主控台視窗，選擇性觀察傳送至 IoT 中樞的訊息。

<!-- For a client app that can receive sampling decisions from the cloud, check out [this sample](https://aka.ms/iottracingCsample).  -->

### <a name="workaround-for-third-party-clients"></a>協力廠商用戶端的因應措施

不需要使用 C SDK，就**能**輕鬆預覽分散式追蹤功能。 因此，不建議使用此方法。

首先，您必須遵循開發指南[建立和讀取 IoT 中樞訊息](iot-hub-devguide-messages-construct.md)的方式，在您的訊息中執行所有 IoT 中樞的通訊協定基本專案。 然後，編輯 MQTT/AMQP 訊息中的通訊協定屬性，以`tracestate`新增為**系統屬性**。 具體而言，

* 針對 MQTT，將`%24.tracestate=timestamp%3d1539243209`新增至訊息主題，其中`1539243209`應該以 unix 時間戳記格式的訊息建立時間來取代。 如需範例，請參閱[C SDK 中](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/iothubtransport_mqtt_common.c#L761)的實作為
* 針對 AMQP，新增`key("tracestate")`和`value("timestamp=1539243209")`作為訊息注釋。 如需參考的執行方式，請參閱[這裡](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/uamqp_messaging.c#L527)。

若要控制包含此屬性的訊息百分比，請實作邏輯以接聽雲端起始的事件，例如對應項更新。

## <a name="update-sampling-options"></a>更新取樣選項 

若要變更要從雲端追蹤的訊息百分比，您必須更新裝置對應項。 您可以使用多種方式完成此作業，包括入口網站中的 JSON 編輯器和 IoT 中樞服務 SDK。 下列各小節提供相關範例。

### <a name="update-using-the-portal"></a>使用入口網站更新

1. 在 [Azure 入口網站](https://portal.azure.com/)中瀏覽至您的 IoT 中樞，然後按一下 [IoT 裝置]****。

1. 按一下您的裝置。

1. 尋找 [啟用分散式追蹤 (預覽)]****，然後選取 [啟用]****。

    ![在 Azure 入口網站中啟用分散式追蹤](./media/iot-hub-distributed-tracing/azure-portal.png)

1. 選擇介於 0%和 100%之間的 [取樣率]****。

1. 按一下 **[儲存]** 。

1. 等候幾秒鐘，然後按 [重新整理]****，如果由裝置成功認可，隨即出現具有核取記號的同步圖示。

1. 回到遙測訊息應用程式的主控台視窗。 您會看到正在傳送的訊息在應用程式屬性中包含 `tracestate`。

    ![追蹤狀態](./media/iot-hub-distributed-tracing/MicrosoftTeams-image.png)

1. (選擇性) 將取樣率變更為不同的值，並觀察訊息在應用程式屬性中包含 `tracestate` 的頻率變更。

### <a name="update-using-azure-iot-hub-for-vs-code"></a>使用 VS Code 的 Azure IoT 中樞進行更新

1. 安裝 VS Code，然後從[這裡](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)安裝適用于 VS Code 的最新版本 Azure IoT 中樞。

1. 開啟 VS Code 並[設定 IoT 中樞連接字串](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit#user-content-prerequisites)。

1. 展開裝置並尋找 [分散式追蹤設定 (預覽)]****。 在其下方，按一下子節點的 [更新分散式追蹤設定 (預覽)]****。

    ![啟用 Azure IoT 中樞延伸模組中的分散式追蹤](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-1.png)

1. 在快顯視窗中，選取 [啟用]****，然後按 Enter 以確認取樣率為 100。

    ![更新取樣模式](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-2.png)

    ![更新取樣率](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-3.png)

### <a name="bulk-update-for-multiple-devices"></a>大量更新多個裝置

若要更新多個裝置的分散式追蹤取樣組態，請使用[自動裝置組態](iot-hub-auto-device-config.md)。 務必遵循此對應項結構描述：

```json
{
    "properties": {
        "desired": {
            "azureiot*com^dtracing^1": {
                "sampling_mode": 1,
                "sampling_rate": 100
            }
        }
    }
}
```

| 元素名稱 | 必要 | 類型 | 描述 |
|-----------------|----------|---------|-----------------------------------------------------|
| `sampling_mode` | 是 | 整數 | 目前支援兩個模式值來開啟和關閉取樣。 `1` 為開啟，而 `2` 為關閉。 |
| `sampling_rate` | 是 | 整數 | 這個值是百分比。 只允許從 `0` 到 `100` (含) 的值。  |

## <a name="query-and-visualize"></a>查詢並以視覺方式呈現

若要查看 IoT 中樞所記錄的全部追蹤，請查詢您在診斷設定中選取的記錄存放區。 本節逐步解說幾個不同的選項。

### <a name="query-using-log-analytics"></a>使用 Log Analytics 查詢

如果您已設定[具有診斷記錄的 Log Analytics](../azure-monitor/platform/resource-logs-collect-storage.md)，請藉由尋找 `DistributedTracing` 類別中的記錄進行查詢。 例如，此查詢會顯示已記錄的所有追蹤：

```Kusto
// All distributed traces 
AzureDiagnostics 
| where Category == "DistributedTracing" 
| project TimeGenerated, Category, OperationName, Level, CorrelationId, DurationMs, properties_s 
| order by TimeGenerated asc  
```

Log Analytics 所顯示的範例記錄：

| TimeGenerated | OperationName | 類別 | 層級 | CorrelationId | DurationMs | 屬性 |
|--------------------------|---------------|--------------------|---------------|---------------------------------------------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-02-22T03:28:28.633Z | DiagnosticIoTHubD2C | DistributedTracing | 資訊 | 00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01 |  | {"deviceId":"AZ3166","messageSize":"96","callerLocalTimeUtc":"2018-02-22T03:27:28.633Z","calleeLocalTimeUtc":"2018-02-22T03:27:28.687Z"} |
| 2018-02-22T03:28:38.633Z | DiagnosticIoTHubIngress | DistributedTracing | 資訊 | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 20 | {"isRoutingEnabled":"false","parentSpanId":"0144d2590aacd909"} |
| 2018-02-22T03:28:48.633Z | DiagnosticIoTHubEgress | DistributedTracing | 資訊 | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 23 | {"endpointType":"EventHub","endpointName":"myEventHub", "parentSpanId":"0144d2590aacd909"} |

若要了解不同類型的記錄，請參閱 [Azure IoT 中樞診斷記錄](iot-hub-monitor-resource-health.md#distributed-tracing-preview)。

### <a name="application-map"></a>應用程式對應

若要以視覺化方式呈現 IoT 訊息的流程，請設定應用程式對應範例應用程式。 範例應用程式會使用 Azure 函式和事件中樞，將分散式追蹤記錄傳送到[應用程式對應](../application-insights/app-insights-app-map.md)。

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/e2e-diagnostic-provision-cli" target="_blank">取得 GitHub 上的範例</a>

下圖以包含三個路由端點的「應用程式對應」顯示分散式追蹤：

![應用程式對應中的 IoT 分散式追蹤](./media/iot-hub-distributed-tracing/app-map.png)

## <a name="understand-azure-iot-distributed-tracing"></a>了解 Azure IoT 分散式追蹤

### <a name="context"></a>Context

許多 IoT 解決方案 (包括我們自己的[參考架構](https://aka.ms/iotrefarchitecture) (僅限英文)) 通常都會遵循[微服務架構](https://docs.microsoft.com/azure/architecture/microservices/)的變體。 隨著 IoT 解決方案日益複雜，您終究會使用數十個或更多微服務。 這些微服務或許來自 Azure。 指出 IoT 訊息卸除或變慢的位置可能變得很有挑戰性。 例如，您有使用 5 個不同 Azure 服務和 1500 個作用中裝置的 IoT 解決方案。 每個裝置每秒會傳送 10 則裝置到雲端的訊息 (總計每秒 15,000 則訊息)，但您會注意到您的 Web 應用程式每秒只看到 10,000 則訊息。 問題出在哪裡？ 如何找到罪魁禍首？

### <a name="distributed-tracing-pattern-in-microservice-architecture"></a>微服務架構中的分散式追蹤模式

若要跨越不同的服務重新建構 IoT 訊息的流程，則每項服務都應該傳播可唯一識別訊息的「相互關聯識別碼」**。 收集到集中式系統後，相互關聯識別碼即可讓您查看訊息流程。 這種方法稱為[分散式追蹤模式](https://docs.microsoft.com/azure/architecture/microservices/logging-monitoring#distributed-tracing)。

為了支持更廣泛的分散式追蹤採用，Microsoft 一直致力於提供[分散式追蹤的 W3C 標準提案](https://w3c.github.io/trace-context/)。

### <a name="iot-hub-support"></a>IoT 中樞支援

一旦啟用，IoT 中樞的分散式追蹤支援就會遵循此流程：

1. IoT 裝置上會產生一則訊息。
1. IoT 裝置決定 (利用雲端的說明) 應透過追蹤內容指派此訊息。
1. SDK 會將 `tracestate` 新增至訊息應用程式屬性，其中包含訊息建立時間戳記。
1. IoT 裝置會將此訊息傳送至 IoT 中樞。
1. 訊息抵達 IoT 中樞閘道。
1. IoT 中樞會在訊息應用程式屬性中尋找 `tracestate`，並查看它是否為正確格式。
1. 若是如此，IoT 中樞會為訊息產生`trace-id`全域唯一的， `span-id`針對「躍點」，則會將其記錄到作業下 Azure 監視器診斷記錄`DiagnosticIoTHubD2C`中。
1. 訊息處理完成之後，IoT 中樞會產生另一個`span-id` ，並將它與現有`trace-id`的作業一起記錄`DiagnosticIoTHubIngress`在一起。
1. 如果啟用訊息的路由功能，IoT 中樞將訊息寫入到自訂端點，並將另一個 `span-id` 與相同的 `trace-id` 記錄在 `DiagnosticIoTHubEgress` 類別之下。
1. 上述步驟會針對所產生的每則訊息重複執行。

## <a name="public-preview-limits-and-considerations"></a>公開預覽限制和考量

- W3C 追蹤內容標準的提案目前能在草擬階段。
- 目前，用戶端 SDK 唯一支援的開發語言是 C。
- 雲端到裝置的對應項功能不適用於 [IoT 中樞基本層](iot-hub-scaling.md#basic-and-standard-tiers)。 不過，如果 IoT 中樞發現正確撰寫的追蹤內容標頭，仍會記錄到 Azure 監視器。
- 為了確保有效運作，IoT 中樞會對記錄速率加以節流，這可能是分散式追蹤的一部分。

## <a name="next-steps"></a>後續步驟

- 若要深入了解微服務中的一般分散式追蹤模式，請參閱[微服務架構模式：分散式追蹤](https://microservices.io/patterns/observability/distributed-tracing.html)。
- 若要設定可將分散式追蹤設定套用至大量裝置的組態，請參閱[設定和監視大規模的 IoT 裝置](iot-hub-auto-device-config.md)。
- 若要深入了解 Azure 監視器，請參閱[什麼是 Azure 監視器？](../azure-monitor/overview.md)。
