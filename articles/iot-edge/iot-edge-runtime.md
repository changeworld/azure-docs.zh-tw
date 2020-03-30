---
title: 了解執行階段如何管理裝置 - Azure IoT Edge | Microsoft Docs
description: 瞭解 IoT Edge 運行時如何管理設備上的模組、安全性、通信和報告
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c184972789c412406f264f725f8b94e1f7f162ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284898"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>了解 Azure IoT Edge 執行階段和架構

IoT Edge 執行階段是將裝置變成 IoT Edge 裝置的程式集合。 總之，IoT Edge 運行時元件使 IoT Edge 設備能夠接收代碼在邊緣運行並傳達結果。

IoT 邊緣運行時負責 IoT 邊緣設備上的以下功能：

* 在裝置上安裝和更新工作負載。
* 在裝置上維護 Azure IoT Edge 安全性標準。
* 確保[IoT 邊緣模組](iot-edge-modules.md)始終運行。
* 將模組健康情況報告至雲端，以便進行遠端監控。
* 管理下游設備和 IoT 邊緣設備之間的通信。
* 管理 IoT 邊緣設備上模組之間的通信。
* 管理 IoT 邊緣設備和雲之間的通信。

![執行階段會將深入見解和模組健康情況傳達到 IoT 中樞](./media/iot-edge-runtime/Pipeline.png)

IoT Edge 執行階段的責任分為兩類：通訊和模組管理。 這兩個角色由作為 IoT Edge 運行時一部分的兩個元件執行。*IoT Edge 中心*負責通信，而*IoT Edge 代理*部署和監視模組。

IoT Edge 中樞和 IoT Edge 代理程式都是模組，就像 IoT Edge 裝置上執行的任何其他模組。 它們有時稱為*運行時模組*。

## <a name="iot-edge-hub"></a>IoT Edge 中樞

IoT Edge 中樞是組成 Azure IoT Edge 執行階段的兩個模組之一。 它藉由公開與 IoT 中樞相同的通訊協定端點來作為 IoT 中樞的本機 Proxy。 此一致性表示用戶端 (不論是裝置或模組) 都可連線到 IoT Edge 執行階段，就像它們對 IoT 中樞所做的。

>[!NOTE]
> IoT 邊緣中心支援使用 MQTT 或 AMQP 連接的用戶端。 並不支援使用 HTTP 的用戶端。

IoT 邊緣中心不是本地運行的 IoT 中心的完整版本。 IoT Edge 中心將一些任務悄悄地委派給 IoT 中心。 例如，當裝置第一次嘗試連線時，IoT Edge 中樞會將驗證要求轉送到 IoT 中樞。 建立第一個連線之後，IoT Edge 中樞就會在本機快取安全性資訊。 允許將來從該設備連接，而無需再次對雲進行身份驗證。

為了減少 IoT Edge 解決方案使用的頻寬，IoT Edge 中心優化了與雲的實際連接數。 IoT Edge 中心從模組或下游設備獲取邏輯連接，併合並這些連接以進行與雲的單個物理連接。 此程序的詳細資料對解決方案的其餘部分而言是透明的。 用戶端認為它們有自己的連線可連至雲端，即使它們全部都會透過相同連線來傳送。

![IoT Edge 中樞是實體裝置與 IoT 中樞之間的閘道](./media/iot-edge-runtime/Gateway.png)

IoT Edge 中樞可以判斷是否已連線到 IoT 中樞。 如果連線中斷，IoT Edge 中樞就會在本機儲存訊息或對應項更新。 一旦連線重新建立之後，就會將所有資料同步。 針對此暫時的快取所使用的位置取決於 IoT Edge 中樞模組對應項的屬性。 快取的大小不會受限，而且只要裝置還有儲存體容量就會持續成長。有關詳細資訊，請參閱[離線功能](offline-capabilities.md)。

### <a name="module-communication"></a>模組通訊

IoT Edge 中樞可促進模組對模組的通訊。 使用 IoT Edge 中樞作為訊息代理程式，讓模組彼此保持獨立。 模組只需指定它們要在其中接受訊息的輸入，以及要將訊息寫入其中的輸出。 解決方案開發人員可以將這些輸入和輸出拼接在一起，以便模組按該解決方案的特定連續處理資料。

![IoT Edge 中樞可促進模組對模組的通訊](./media/iot-edge-runtime/module-endpoints.png)

為了將資料傳送至 IoT Edge 中樞，模組會呼叫 SendEventAsync 方法。 第一個引數會指定在哪個輸出上傳送訊息。 以下偽代碼在**輸出1**上發送消息：

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings);
   await client.OpenAsync();
   await client.SendEventAsync("output1", message);
   ```

若要接收訊息，請註冊會處理來自特定輸入之訊息的回呼。 以下偽代碼註冊函數消息處理器，用於處理**在 input1**上收到的所有消息：

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

有關 ModuleClient 類及其通信方法的詳細資訊，請參閱首選 SDK 語言的 API 引用：C#、C、Python、JAVA 或[Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)。 [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet) [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h) [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python) [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)

解決方案開發人員會負責指定規則，以判斷 IoT Edge 中樞在模組之間傳遞訊息的方式。 路由規則在雲中定義，並將其推送到其模組孿生中的 IoT Edge 中心。 適用於 IoT 中樞路由的相同語法，可用來定義 Azure IoT Edge 中模組之間的路由。 有關詳細資訊，請參閱[瞭解如何在 IoT 邊緣中部署模組和建立路由](module-composition.md)。

![模組之間的路由會經由 IoT Edge 中樞](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>IoT Edge 代理程式

IoT Edge 代理程式是另一個組成 Azure IoT Edge 執行階段的模組。 它負責將模組具現化，確保它們會繼續執行，並將模組的狀態回報至 IoT 中樞。 此配置資料編寫為 IoT Edge 代理模組孿生的屬性。

[IoT Edge 安全性精靈](iot-edge-security-manager.md)會在裝置啟動時啟動 IoT Edge 代理程式。 代理程式會從 IoT 中樞擷取其模組對應項，並檢查部署資訊清單。 部署資訊清單是 JSON 檔案，會宣告需要啟動的模組。

部署資訊清單中的每個項目都包含關於模組的特定資訊，而 IoT Edge 代理程式可用其來控制模組的生命週期。 以下提供一些更令人感興趣的屬性：

* **settings.image**：IoT Edge 代理程式用來啟動模組的容器映像。 如果映像受到密碼保護，IoT Edge 代理程式就必須使用適用於容器登錄的認證來設定。 若要從遠端設定容器登錄的認證，您可以藉由使用部署資訊清單來進行，也可以在 IoT Edge 裝置本身上藉由在 IoT Edge 方案資料夾中更新 `config.yaml` 檔案來進行。
* **設置.createOptions** = 在啟動模組的容器時直接傳遞到 Moby 容器守護進程的字串。 在此屬性中添加選項允許高級配置，如埠轉發或將卷安裝到模組的容器中。  
* **status**：IoT Edge 代理程式放置模組的狀態。 通常，此值設置為*運行*，因為大多數人希望 IoT Edge 代理立即啟動設備上的所有模組。 不過，您可以指定要停止之模組的初始狀態，並等候一段時間，以告知 IoT Edge 代理程式啟動模組。IoT Edge 代理程式會在回報的屬性中，將每個模組的狀態回報至雲端。 所需屬性和回報的屬性之間的差異可能表示裝置異常。 支援的狀態如下：

  * 正在下載
  * 執行中
  * 狀況不良
  * 失敗
  * 已停止

* **restartPolicy**：IoT Edge 代理程式重新啟動模組的方式。 可能的值包括：
  
  * `never`• IoT 邊緣代理永遠不會重新開機模組。
  * `on-failure`- 如果模組崩潰，IoT 邊緣代理將重新開機它。 如果模組乾淨地關閉，IoT Edge 代理不會重新開機它。
  * `on-unhealthy`- 如果模組崩潰或被視為不正常，IoT Edge 代理將重新開機它。
  * `always`- 如果模組崩潰、被視為不正常或以任何方式關閉，IoT Edge 代理將重新開機它。

* **圖像拉取策略**- IoT 邊緣代理是否嘗試自動提取模組的最新映射。 如果不指定值，則預設值為 *"創建*"。 可能的值包括：

  * `on-create`- 啟動模組或更新基於新部署清單的模組時，IoT Edge 代理將嘗試從容器註冊表中提取模組映射。
  * `never`- IoT 邊緣代理永遠不會嘗試從容器註冊表中提取模組映射。 使用此配置，您負責將模組映射連接到設備並管理任何映射更新。

IoT Edge 代理程式會將執行階段回應傳送到 IoT 中樞。 以下是可能回應的清單：
  
* 200 - 確定
* 400 - 部署設定不正確或無效。
* 417 - 設備沒有部署組態集。
* 412 - 部署設定中的結構描述版本無效。
* 406 - IoT Edge 裝置已離線或無法傳送狀態報表。
* 500 - IoT Edge 執行階段發生錯誤。

有關詳細資訊，請參閱[瞭解如何在 IoT 邊緣中部署模組和建立路由](module-composition.md)。

### <a name="security"></a>安全性

IoT Edge 代理程式在 IoT Edge 裝置的安全性中扮演了關鍵角色。 例如，它會執行像是啟動前先驗證模組映像的動作。

有關 Azure IoT 邊緣安全框架的詳細資訊，請閱讀有關[IoT Edge 安全管理器](iot-edge-security-manager.md)的資訊。

## <a name="next-steps"></a>後續步驟

[了解 Azure IoT Edge 模組](iot-edge-modules.md)
