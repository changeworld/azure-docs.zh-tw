---
title: 使用 Azure IoT 中樞來監視和排解連線中斷問題
description: 了解如何透過 Azure IoT 中樞來監視和排解裝置連線的常見錯誤
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: jlian
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Technical Support'
ms.openlocfilehash: f7073fbf39344fe39e179d55a5a8f395a6ba6240
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357344"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>使用 Azure IoT 中樞來監視、診斷和排解連線中斷問題

IoT 裝置的連線問題可能因為有許多可能的失敗點而難以排解。 應用程式邏輯、實體網路、通訊協定、硬體、IoT 中樞及其他雲端服務全都可能造成問題。 必須有能力偵測和查明問題的來源。 不過，大型的 IoT 解決方案可能有數千部裝置，實際上不可能手動檢查每個裝置。 IoT 中樞會與兩個 Azure 服務整合，以協助您：

* **Azure 監視器** 為了協助您大規模偵測、診斷和疑難排解這些問題，請使用 IoT 中樞透過 Azure 監視器提供的監視功能。 這包括設定警示來觸發發生中斷連線時的通知和動作，以及設定可用於探索造成中斷連線之狀況的記錄。

* **Azure 事件方格** 針對重要的基礎結構和個別裝置中斷連線，請使用 Azure 事件方格來訂閱 IoT 中樞發出的裝置連線和中斷線上活動。

在這兩種情況下，這些功能僅限於 IoT 中樞可以觀察到的內容，因此我們也建議您遵循監視裝置和其他 Azure 服務的最佳做法。

## <a name="event-grid-vs-azure-monitor"></a>事件方格與 Azure 監視器

事件方格提供低延遲、每一裝置的監視解決方案，可讓您用來追蹤重要裝置和基礎結構的裝置連線。 Azure 監視器提供計量連接的 *裝置* ，您可以使用這些裝置來監視連線到 IoT 中樞的裝置數目，並在該數位低於靜態閾值時觸發警示。

決定是否要在特定案例中使用事件方格或 Azure 監視器時，請考慮下列事項：

* 警示延遲： IoT 中樞線上活動會透過事件方格更快速地傳遞。 這可讓事件方格成為需要快速通知的案例更好的選擇。

* 每一裝置的通知：事件方格可讓您追蹤個別裝置的連線和中斷連接。 這讓事件方格更適合您需要監視重要裝置連線的案例。

* 輕量設定： Azure 監視器計量警示可提供輕量的設定體驗，而不需要與其他服務整合，以透過電子郵件、SMS、語音和其他通知傳遞通知。  使用事件方格時，您必須與其他 Azure 服務整合以提供通知。 這兩項服務可以與其他服務整合，以觸發更複雜的動作。

基於生產環境的低延遲、每一裝置功能，強烈建議使用事件方格來監視連接。 當然，選擇不是專屬的，您可以同時使用 Azure 監視器計量警示和事件方格。 無論您選擇何種追蹤中斷連線，您都可能會使用 Azure 監視器資源記錄檔，以協助針對未預期裝置中斷連線的原因進行疑難排解。 下列各節會更詳細地討論這些選項。

## <a name="event-grid-monitor-device-connect-and-disconnect-events"></a>事件方格：監視裝置連線和中斷線上活動

若要在生產環境中監視裝置連線和中斷線上活動，建議您訂閱事件方格中的 [ **DeviceConnected** 和 **DeviceDisconnected** 事件](iot-hub-event-grid.md#event-types)，以觸發警示並監視裝置線上狀態。 事件方格可提供比 Azure 監視器更低的事件延遲，而且您可以根據每個裝置來監視，而不是針對已連線的裝置總數進行監視。 這些因素讓事件方格成為監視重要裝置和基礎結構的慣用方法。

當您使用事件方格來監視或觸發裝置中斷連線的警示時，請確定您已建立一種方式，以篩選出在使用 Azure IoT Sdk 的裝置上更新 SAS 權杖所造成的定期中斷連線。 若要深入瞭解，請參閱 [使用 Azure IoT Sdk MQTT 裝置中斷連線行為](#mqtt-device-disconnect-behavior-with-azure-iot-sdks)。

探索下列主題，以深入瞭解如何使用事件方格監視裝置線上活動：

* 如需搭配使用事件方格與 IoT 中樞的總覽，請參閱使用 [事件方格來回應 IoT 中樞事件](iot-hub-event-grid.md)。 特別注意 [裝置連線和裝置中斷線上活動的限制](iot-hub-event-grid.md#limitations-for-device-connected-and-device-disconnected-events) 一節。

* 如需有關訂購裝置線上活動的教學課程，請參閱 [使用 Azure Cosmos DB 從 Azure IoT 中樞訂購裝置線上活動](iot-hub-how-to-order-connection-state-events.md)。

* 如需傳送電子郵件通知的教學課程，請參閱在事件方格檔中 [使用事件方格和 Logic Apps 傳送有關 Azure IoT 中樞事件的電子郵件通知](/azure/event-grid/publish-iot-hub-events-to-logic-apps) 。

## <a name="azure-monitor-route-connection-events-to-logs"></a>Azure 監視器：將連接事件路由至記錄

IoT 中樞會持續發出數個作業類別的資源記錄。 不過，若要收集此記錄資料，您必須建立診斷設定，以將其路由至可進行分析或封存的目的地。 其中一個這類目的地是透過 Log Analytics 工作區 Azure 監視器記錄 ([請參閱定價](https://azure.microsoft.com/pricing/details/log-analytics/)) ，您可以在其中使用 Kusto 查詢來分析資料。

IoT 中樞 [資源記錄連線類別](monitor-iot-hub-reference.md#connections) 會發出與裝置連線有關的作業和錯誤。 下列螢幕擷取畫面顯示可將這些記錄路由至 Log Analytics 工作區的診斷設定：

:::image type="content" source="media/iot-hub-troubleshoot-connectivity/create-diagnostic-setting.png" alt-text="將連線記錄傳送至 Log Analytics 工作區的建議設定。":::

我們建議您在建立 IoT 中樞之後儘早建立診斷設定，因為雖然 IoT 中樞一律會發出資源記錄，但 Azure 監視器不會收集它們，直到您將它們路由傳送至目的地為止。

若要深入瞭解將記錄路由傳送至目的地的詳細資訊，請參閱 [收集和路由](monitor-iot-hub.md#collection-and-routing)。 如需建立診斷設定的詳細指示，請參閱 [使用計量和記錄教學](tutorial-use-metrics-and-diags.md)課程。

## <a name="azure-monitor-set-up-metric-alerts-for-device-disconnect-at-scale"></a>Azure 監視器：設定大規模裝置中斷連線的計量警示

您可以根據 IoT 中樞發出的平臺計量來設定警示。 您可以使用計量警示，通知個人有感興趣的條件，同時觸發可自動回應該條件的動作。

[*連線的裝置 (預覽版)*](monitor-iot-hub-reference.md#device-metrics)計量會告訴您有多少裝置連線到您的 IoT 中樞。 您可以建立警示，以在此計量低於臨界值時觸發：

:::image type="content" source="media/iot-hub-troubleshoot-connectivity/configure-alert-logic.png" alt-text="已連線裝置度量的警示邏輯設定。":::

您可以使用計量警示規則大規模監視裝置中斷連線異常。 也就是說，當大量裝置意外中斷連線時。 當偵測到這種情況時，您可以查看記錄檔，以協助針對問題進行疑難排解。 監視重要裝置的每個裝置中斷連線和中斷連線;不過，您必須使用事件方格。 事件方格也提供比 Azure 計量更即時的體驗。

若要深入瞭解 IoT 中樞的警示，請參閱 [監視 Iot 中樞內的警示](monitor-iot-hub.md#alerts)。 如需在 IoT 中樞中建立警示的逐步解說，請參閱 [使用計量和記錄教學](tutorial-use-metrics-and-diags.md)課程。 如需更詳細的警示總覽，請參閱 Azure 監視器檔 [中 Microsoft Azure 的警示總覽](../azure-monitor/platform/alerts-overview.md) 。

## <a name="azure-monitor-use-logs-to-resolve-connectivity-errors"></a>Azure 監視器：使用記錄來解決連線錯誤

當您偵測到裝置中斷連線時，無論是 Azure 監視器計量警示或事件方格，您都可以使用記錄來協助疑難排解原因。 本節說明如何在 Azure 監視器記錄檔中尋找常見的問題。 下列步驟假設您已建立 [診斷設定](#azure-monitor-route-connection-events-to-logs) ，以將 IoT 中樞連接記錄傳送至 Log Analytics 工作區。

建立診斷設定以將 IoT 中樞資源記錄路由至 Azure 監視器記錄檔之後，請遵循下列步驟來查看 Azure 入口網站中的記錄。

1. 在 [Azure 入口網站](https://portal.azure.com)中流覽至您的 IoT 中樞。

1. 在 IoT 中樞左窗格的 [ **監視** ] 底下，選取 [ **記錄** ]。

1. 若要隔離 IoT 中樞的連線錯誤記錄，請在查詢編輯器中輸入下列查詢，然後選取 [ **執行** ：

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. 如果有結果，請尋找 `OperationName`、`ResultType` (錯誤碼) 和 `ResultDescription` (錯誤訊息)，以取得關於錯誤的詳細資訊。

   ![錯誤記錄的範例](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

一旦您找到錯誤，請遵循問題解決指南以取得最常見錯誤的說明：

* [400027 ConnectionForcefullyClosedOnNewConnection](iot-hub-troubleshoot-error-400027-connectionforcefullyclosedonnewconnection.md)

* [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)

* [401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)

* [409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)

* [500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)

* [500008 GenericTimeout](iot-hub-troubleshoot-error-500xxx-internal-errors.md)

## <a name="mqtt-device-disconnect-behavior-with-azure-iot-sdks"></a>使用 Azure IoT Sdk MQTT 裝置中斷連線行為

Azure IoT 裝置 Sdk 會中斷與 IoT 中樞的連線，然後在透過 MQTT (更新 SAS 權杖時重新連線，並透過 Websocket) 通訊協定進行 MQTT。 在記錄中，這會顯示為資訊裝置中斷連線和線上活動，有時會伴隨錯誤事件。

根據預設，所有 Sdk 的權杖生命週期為60分鐘;不過，開發人員可以在某些 Sdk 中變更它。 下表摘要說明每個 Sdk 的權杖生命週期、權杖更新和權杖更新行為：

| SDK | 權杖存留期 | 權杖更新 | 續約行為 |
|-----|----------|---------------------|---------|
| .NET | 60分鐘，可設定 | 85% 的存留期（可設定） | SDK 會在權杖存留期加上10分鐘的寬限期內進行連線和中斷連接。 記錄中產生的資訊事件和錯誤。 |
| Java | 60分鐘，可設定 | 85% 的存留期，無法設定 | SDK 會在權杖存留期加上10分鐘的寬限期內進行連線和中斷連接。 記錄中產生的資訊事件和錯誤。 |
| Node.js | 60分鐘，可設定 | 配置 | SDK 會在權杖更新時進行連線和中斷連接。 記錄中只會產生資訊事件。 |
| Python | 60分鐘，無法設定 | -- | SDK 會在權杖存留期連線並中斷連接。 |

下列螢幕擷取畫面顯示不同 Sdk Azure 監視器記錄中的權杖更新行為。 權杖的存留期和更新閾值已從其預設值變更為已注明。

* .NET 裝置 SDK 1200 秒 (20 分鐘) 權杖存留期和更新設定為在90% 的存留期內發生。 每隔30分鐘就會中斷連線：

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/net-mqtt.png" alt-text="使用 .NET SDK 在 Azure 監視器記錄中透過 MQTT 更新權杖的錯誤行為。":::

* 具有300秒的 JAVA SDK (5 分鐘) 權杖存留期和預設85% 的存留期更新。 每隔15分鐘會中斷連線：

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/java-mqtt.png" alt-text="使用 JAVA SDK 在 Azure 監視器記錄中透過 MQTT 更新權杖的錯誤行為。":::

* 節點 SDK 300 秒 (5 分鐘) 權杖存留期和權杖更新設定為在3分鐘內發生。 權杖更新時發生中斷連線。 此外，也不會發生任何錯誤，只會發出參考連接/中斷線上活動：

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/node-mqtt.png" alt-text="使用 Node SDK Azure 監視器記錄中，透過 MQTT 更新權杖的錯誤行為。":::

下列查詢是用來收集結果。 此查詢會從屬性包中解壓縮 SDK 名稱和版本;若要深入瞭解，請參閱 [IoT 中樞記錄中的 SDK 版本](monitor-iot-hub.md#sdk-version-in-iot-hub-logs)。

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s)
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId) , Protocol =  tostring(parsed_json.protocol)
| distinct TimeGenerated, OperationName, Level, ResultType, ResultDescription, DeviceId, Protocol, SDKVersion

```

如果您是 IoT 解決方案開發人員或操作員，則必須留意此行為，才能在記錄中解讀連接/中斷線上活動和相關錯誤。 如果您想要變更裝置的權杖存留期或更新行為，請檢查裝置是否可執行裝置對應項設定或可進行這項操作的裝置方法。

如果您要監視與事件中樞的裝置連線，請確定您是以某種方式來篩選出因為 SAS 權杖更新而導致的定期中斷連接;例如，在特定時間範圍內，只要中斷連接事件後面接著 connect 事件，就不會根據中斷連線觸發動作。

## <a name="i-tried-the-steps-but-they-didnt-work"></a>我試過這些步驟，但沒有用

如果上述步驟無法解決問題，請嘗試：

* 如果您可以存取有問題的裝置 (實體或遠端 (如 SSH))，請遵循[裝置端疑難排解指南](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices)，繼續進行疑難排解。

* 在 Azure 入口網站 > 您的 IoT 中樞 > IoT 裝置，確認您的裝置 [已啟用]。

* 如果您的裝置使用 MQTT 通訊協定，請確認連接埠 8883 已開啟。 如需詳細資訊，請參閱[連線至 IoT 中樞 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

* 從 [Microsoft 的 Azure IoT 中樞問與答頁面](/answers/topics/azure-iot-hub.html)、[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub) 或 [Azure 支援](https://azure.microsoft.com/support/options/)取得協助。

為了協助每個人改善文件，如果此指南對您沒有幫助，請在下方的意見反應區段中留下意見。

## <a name="next-steps"></a>後續步驟

* 若要深入了解如何解決暫時性問題，請參閱[暫時性錯誤處理](/azure/architecture/best-practices/transient-faults)。

* 若要深入了解 Azure IoT SDK 以及如何管理重試，請參閱[如何使用 Azure IoT 中樞裝置 SDK 來管理連線能力和可靠傳訊](iot-hub-reliability-features-in-sdks.md#connection-and-retry)。