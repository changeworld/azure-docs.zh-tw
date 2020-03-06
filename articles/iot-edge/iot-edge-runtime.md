---
title: 了解執行階段如何管理裝置 - Azure IoT Edge | Microsoft Docs
description: 瞭解 IoT Edge 執行時間如何管理裝置上的模組、安全性、通訊和報告
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c184972789c412406f264f725f8b94e1f7f162ce
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78358670"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>了解 Azure IoT Edge 執行階段和架構

IoT Edge 執行時間是將裝置變成 IoT Edge 裝置的程式集合。 IoT Edge 執行時間元件可讓 IoT Edge 裝置接收在邊緣執行的程式碼，並傳達結果。

IoT Edge 執行時間負責 IoT Edge 裝置上的下列功能：

* 在裝置上安裝和更新工作負載。
* 在裝置上維護 Azure IoT Edge 安全性標準。
* 確定[IoT Edge 模組](iot-edge-modules.md)一律執行中。
* 將模組健康情況報告至雲端，以便進行遠端監控。
* 管理下游裝置與 IoT Edge 裝置之間的通訊。
* 管理 IoT Edge 裝置上的模組之間的通訊。
* 管理 IoT Edge 裝置與雲端之間的通訊。

![執行階段會將深入見解和模組健康情況傳達到 IoT 中樞](./media/iot-edge-runtime/Pipeline.png)

IoT Edge 執行階段的責任分為兩類：通訊和模組管理。 這兩個角色是由 IoT Edge 執行時間之一部分的兩個元件所執行。 *IoT Edge 中樞*負責通訊，而*IoT Edge 代理程式*則會部署和監視模組。

IoT Edge 中樞和 IoT Edge 代理程式都是模組，就像 IoT Edge 裝置上執行的任何其他模組。 有時也稱為*執行時間模組*。

## <a name="iot-edge-hub"></a>IoT Edge 中樞

IoT Edge 中樞是組成 Azure IoT Edge 執行階段的兩個模組之一。 它藉由公開與 IoT 中樞相同的通訊協定端點來作為 IoT 中樞的本機 Proxy。 此一致性表示用戶端 (不論是裝置或模組) 都可連線到 IoT Edge 執行階段，就像它們對 IoT 中樞所做的。

>[!NOTE]
> IoT Edge 中樞支援使用 MQTT 或 AMQP 連接的用戶端。 並不支援使用 HTTP 的用戶端。

IoT Edge 中樞不是在本機執行 IoT 中樞的完整版本。 IoT Edge 中樞會以無訊息方式將一些工作委派給 IoT 中樞。 例如，當裝置第一次嘗試連線時，IoT Edge 中樞會將驗證要求轉送到 IoT 中樞。 建立第一個連線之後，IoT Edge 中樞就會在本機快取安全性資訊。 允許來自該裝置的未來連線，而不需要再次向雲端驗證。

為了減少 IoT Edge 解決方案所使用的頻寬，IoT Edge 中樞會優化對雲端所做的實際連線數目。 IoT Edge 中樞會接受來自模組或下游裝置的邏輯連線，並將它們結合成單一實體連線到雲端。 此程序的詳細資料對解決方案的其餘部分而言是透明的。 用戶端認為它們有自己的連線可連至雲端，即使它們全部都會透過相同連線來傳送。

![IoT Edge 中樞是實體裝置與 IoT 中樞之間的閘道](./media/iot-edge-runtime/Gateway.png)

IoT Edge 中樞可以判斷是否已連線到 IoT 中樞。 如果連線中斷，IoT Edge 中樞就會在本機儲存訊息或對應項更新。 一旦連線重新建立之後，就會將所有資料同步。 針對此暫時的快取所使用的位置取決於 IoT Edge 中樞模組對應項的屬性。 快取的大小不會受限，而且只要裝置還有儲存體容量就會持續成長。 如需詳細資訊，請參閱[離線功能](offline-capabilities.md)。

### <a name="module-communication"></a>模組通訊

IoT Edge 中樞可促進模組對模組的通訊。 使用 IoT Edge 中樞作為訊息代理程式，讓模組彼此保持獨立。 模組只需指定它們要在其中接受訊息的輸入，以及要將訊息寫入其中的輸出。 解決方案開發人員可以將這些輸入和輸出結合在一起，讓模組以該解決方案特定的連續處理資料。

![IoT Edge 中樞可促進模組對模組的通訊](./media/iot-edge-runtime/module-endpoints.png)

為了將資料傳送至 IoT Edge 中樞，模組會呼叫 SendEventAsync 方法。 第一個引數會指定在哪個輸出上傳送訊息。 下列虛擬代碼會在**output1**上傳送訊息：

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings);
   await client.OpenAsync();
   await client.SendEventAsync("output1", message);
   ```

若要接收訊息，請註冊會處理來自特定輸入之訊息的回呼。 下列虛擬程式碼會註冊要用來處理**input1**上所接收之所有訊息的 messageProcessor 函數：

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

如需 ModuleClient 類別及其通訊方法的詳細資訊，請參閱您慣用 SDK 語言的 API [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)參考：、 [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)、 [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python)、 [JAVA](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)或[node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)。

解決方案開發人員會負責指定規則，以判斷 IoT Edge 中樞在模組之間傳遞訊息的方式。 路由規則會在雲端中定義，並在其模組對應項中向下推送至 IoT Edge 中樞。 適用於 IoT 中樞路由的相同語法，可用來定義 Azure IoT Edge 中模組之間的路由。 如需詳細資訊，請參閱[瞭解如何在 IoT Edge 中部署模組及建立路由](module-composition.md)。

![模組之間的路由會經由 IoT Edge 中樞](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>IoT Edge 代理程式

IoT Edge 代理程式是另一個組成 Azure IoT Edge 執行階段的模組。 它負責將模組具現化，確保它們會繼續執行，並將模組的狀態回報至 IoT 中樞。 此設定資料會寫入為 IoT Edge 代理程式模組對應項的屬性。

[IoT Edge 安全性精靈](iot-edge-security-manager.md)會在裝置啟動時啟動 IoT Edge 代理程式。 代理程式會從 IoT 中樞擷取其模組對應項，並檢查部署資訊清單。 部署資訊清單是 JSON 檔案，會宣告需要啟動的模組。

部署資訊清單中的每個項目都包含關於模組的特定資訊，而 IoT Edge 代理程式可用其來控制模組的生命週期。 以下提供一些更令人感興趣的屬性：

* **settings.image**：IoT Edge 代理程式用來啟動模組的容器映像。 如果映像受到密碼保護，IoT Edge 代理程式就必須使用適用於容器登錄的認證來設定。 若要從遠端設定容器登錄的認證，您可以藉由使用部署資訊清單來進行，也可以在 IoT Edge 裝置本身上藉由在 IoT Edge 方案資料夾中更新 `config.yaml` 檔案來進行。
* **createOptions** –啟動模組的容器時，直接傳遞至 Moby 容器背景程式的字串。 在此屬性中新增選項，可讓您進行像是埠轉送或將磁片區掛接到模組容器的先進設定。  
* **status**：IoT Edge 代理程式放置模組的狀態。 此值通常會設定為 [*執行中]，因為大多數*人都想要 IoT Edge 代理程式立即啟動裝置上的所有模組。 不過，您可以指定要停止之模組的初始狀態，並等候一段時間，以告知 IoT Edge 代理程式啟動模組。 IoT Edge 代理程式會在回報的屬性中，將每個模組的狀態報表回雲端。 所需屬性和回報的屬性之間的差異可能表示裝置異常。 支援的狀態如下：

  * 下載
  * 正在執行
  * 狀況不良
  * 失敗
  * 已停止

* **restartPolicy**：IoT Edge 代理程式重新啟動模組的方式。 可能的值包括：
  
  * `never` – IoT Edge 代理程式永遠不會重新開機模組。
  * `on-failure`-如果模組損毀，IoT Edge 代理程式就會重新開機它。 如果模組完全關閉，IoT Edge 代理程式不會將它重新開機。
  * `on-unhealthy`-如果模組損毀或被視為狀況不良，IoT Edge 代理程式就會重新開機它。
  * `always`-如果模組損毀、被視為狀況不良，或以任何方式關閉，IoT Edge 代理程式就會重新開機它。

* **imagePullPolicy** -IoT Edge 代理程式是否會嘗試自動提取模組的最新影像。 如果您未指定值，則預設值為*onCreate*。 可能的值包括：

  * `on-create`-啟動模組或根據新的部署資訊清單更新模組時，IoT Edge 代理程式會嘗試從容器登錄提取模組映射。
  * `never`-IoT Edge 代理程式永遠不會嘗試從容器登錄提取模組映射。 使用此設定時，您必須負責將模組映射放在裝置上，並管理任何映射更新。

IoT Edge 代理程式會將執行階段回應傳送到 IoT 中樞。 以下是可能回應的清單：
  
* 200 - 確定
* 400 - 部署設定不正確或無效。
* 417-裝置沒有部署設定集。
* 412 - 部署設定中的結構描述版本無效。
* 406 - IoT Edge 裝置已離線或無法傳送狀態報表。
* 500 - IoT Edge 執行階段發生錯誤。

如需詳細資訊，請參閱[瞭解如何在 IoT Edge 中部署模組及建立路由](module-composition.md)。

### <a name="security"></a>安全性

IoT Edge 代理程式在 IoT Edge 裝置的安全性中扮演了關鍵角色。 例如，它會執行像是啟動前先驗證模組映像的動作。

如需有關 Azure IoT Edge 安全性架構的詳細資訊，請參閱[IoT Edge 安全性管理員](iot-edge-security-manager.md)。

## <a name="next-steps"></a>後續步驟

[了解 Azure IoT Edge 模組](iot-edge-modules.md)
