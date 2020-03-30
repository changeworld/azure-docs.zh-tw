---
title: 使用 Azure IoT 中心監視和排除斷開連接問題
description: 瞭解如何通過 Azure IoT 中心的設備連接監視和排除常見錯誤
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: bed6736fda0c1815964f9017adb1e6fffa9335d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110683"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>使用 Azure IoT 中心監控、診斷和排除斷開連接問題

IoT 裝置的連線問題可能因為有許多可能的失敗點而難以排解。 應用程式邏輯、物理網路、協定、硬體、IoT 中心和其他雲服務都可能導致問題。 檢測和查明問題根源的能力至關重要。 但是，規模的 IoT 解決方案可以有數千台設備，因此手動檢查單個設備是不切實際的。 為了説明大規模檢測、診斷和排除這些問題，請使用 IoT 中心通過 Azure 監視器提供的監視功能。 這些功能僅限於 IoT 中心可以觀察到的內容，因此我們還建議您遵循設備和其他 Azure 服務的監視最佳做法。

## <a name="get-alerts-and-error-logs"></a>取得警示和錯誤記錄

使用 Azure 監視器在設備斷開連接時獲取警報並寫入日誌。

### <a name="turn-on-diagnostic-logs"></a>開啟診斷記錄

若要記錄裝置連線事件和錯誤，請開啟 IoT 中樞的診斷功能。 我們建議儘早打開這些日誌，因為如果未啟用診斷日誌，則當發生設備斷開連接時，您將沒有任何資訊可以解決問題。

1. 登錄到 Azure[門戶](https://portal.azure.com)。

2. 瀏覽至您的 IoT 中樞。

3. 選取 [診斷設定]****。

4. 選取 [開啟診斷]****。

5. 讓 [連線]**** 記錄可供收集。

6. 若要讓分析變得更容易，您應該開啟 [傳送至 Log Analytics]**** ([請參閱定價](https://azure.microsoft.com/pricing/details/log-analytics/))。 請參閱[解決連線錯誤](#resolve-connectivity-errors)下方的範例。

   ![建議的設定](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

若要深入了解，請參閱[監視 Azure IoT 中樞的健康情況並快速診斷問題](iot-hub-monitor-resource-health.md)。

### <a name="set-up-alerts-for-device-disconnect-at-scale"></a>為大規模設備斷開連接設置警報

要在設備斷開連接時獲取警報，請在 **"已連接的設備（預覽）"** 指標上配置警報。

1. 登錄到 Azure[門戶](https://portal.azure.com)。

2. 瀏覽至您的 IoT 中樞。

3. 選取 [警示] ****。

4. 選取 [新增警示規則]****。

5. 選擇 **"添加條件**"，然後選擇"已連接的設備（預覽）"。

6. 設置閾值並通過以下提示發出警報。

要瞭解更多資訊，請參閱[Microsoft Azure 中的警報是什麼？](../azure-monitor/platform/alerts-overview.md)

#### <a name="detecting-individual-device-disconnects"></a>檢測單個設備斷開連接

要檢測*每個設備的*斷開連接（例如，當您需要知道工廠剛剛離線時）請使用[事件網格配置設備斷開連接事件](iot-hub-event-grid.md)。

## <a name="resolve-connectivity-errors"></a>解決連線錯誤

開啟已連線裝置的診斷記錄和警示後，您會在發生錯誤時收到警示。 本節介紹在收到警報時如何查找常見問題。 以下步驟假定您已為診斷日誌設置了 Azure 監視器日誌。

1. 登錄到 Azure[門戶](https://portal.azure.com)。

1. 瀏覽至您的 IoT 中樞。

1. 選擇**日誌**。

1. 若要隔離 IoT 中樞的連線錯誤記錄，請輸入下列查詢，然後選取 [執行]****：

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. 如果有結果，請尋找 `OperationName`、`ResultType` (錯誤碼) 和 `ResultDescription` (錯誤訊息)，以取得關於錯誤的詳細資訊。

   ![錯誤記錄的範例](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

1. 有關最常見的錯誤，請遵循問題解決方法指南：

    - **[404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)**
    - **[401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)**
    - **[409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)**
    - **[500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**
    - **[500008 GenericTimeout](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**

## <a name="i-tried-the-steps-but-they-didnt-work"></a>我嘗試了這些步驟，但它們不起作用

如果前面的步驟不起作用，請嘗試：

* 如果您可以存取有問題的裝置 (實體或遠端 (如 SSH))，請遵循[裝置端疑難排解指南](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices)，繼續進行疑難排解。

* 在 Azure 入口網站 > 您的 IoT 中樞 > IoT 裝置，確認您的裝置 [已啟用]****。

* 如果您的設備使用 MQTT 協定，請驗證埠 8883 是否打開。 有關詳細資訊，請參閱連接到[IoT 中心 （MQTT）](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

* 向 [Azure IoT 中樞論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub)、[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub) 或[Azure 支援](https://azure.microsoft.com/support/options/)取得協助。

為了協助每個人改善文件，如果此指南對您沒有幫助，請在下方的意見反應區段中留下意見。

## <a name="next-steps"></a>後續步驟

* 若要深入了解如何解決暫時性問題，請參閱[暫時性錯誤處理](/azure/architecture/best-practices/transient-faults)。

* 若要深入了解 Azure IoT SDK 以及如何管理重試，請參閱[如何使用 Azure IoT 中樞裝置 SDK 來管理連線能力和可靠傳訊](iot-hub-reliability-features-in-sdks.md#connection-and-retry)。
