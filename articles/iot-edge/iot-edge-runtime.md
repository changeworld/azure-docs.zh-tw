---
title: 了解執行階段如何管理裝置 - Azure IoT Edge | Microsoft Docs
description: 瞭解 IoT Edge 執行時間如何管理裝置上的模組、安全性、通訊和報表
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: amqp, mqtt, devx-track-csharp
ms.openlocfilehash: c0c3a452c93b88483ac7027405665c26ceab8183
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368493"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>了解 Azure IoT Edge 執行階段和架構

IoT Edge 執行階段是將裝置變成 IoT Edge 裝置的程式集合。 IoT Edge 執行時間元件統稱，可讓 IoT Edge 裝置接收在邊緣執行的程式碼，並傳達結果。

IoT Edge 執行時間負責 IoT Edge 裝置上的下列功能：

* 在裝置上安裝和更新工作負載。

* 在裝置上維護 Azure IoT Edge 安全性標準。

* 確定 [IoT Edge 模組](iot-edge-modules.md) 一律在執行中。

* 將模組健康情況報告至雲端，以便進行遠端監控。

* 管理下游裝置與 IoT Edge 裝置之間的通訊。

* 管理 IoT Edge 裝置上模組之間的通訊。

* 管理 IoT Edge 裝置與雲端之間的通訊。
<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
* 管理 IoT Edge 裝置之間的通訊。
::: moniker-end

![執行階段會將深入見解和模組健康情況傳達到 IoT 中樞](./media/iot-edge-runtime/Pipeline.png)

IoT Edge 執行階段的責任分為兩類：通訊和模組管理。 這兩個角色是由屬於 IoT Edge 執行時間一部分的兩個元件所執行。 *IoT Edge 代理程式* 會部署及監視模組，而 *IoT Edge 中樞* 則負責進行通訊。

IoT Edge 代理程式和 IoT Edge 中樞都是模組，就像在 IoT Edge 裝置上執行的任何其他模組一樣。 它們有時稱為 *執行時間模組*。

## <a name="iot-edge-agent"></a>IoT Edge 代理程式

IoT Edge 代理程式是組成 Azure IoT Edge 執行時間的兩個模組之一。 它負責將模組具現化，確保它們會繼續執行，並將模組的狀態回報至 IoT 中樞。 這項設定資料會寫入為 IoT Edge 代理程式模組對應項的屬性。

[IoT Edge 安全性精靈](iot-edge-security-manager.md)會在裝置啟動時啟動 IoT Edge 代理程式。 代理程式會從 IoT 中樞擷取其模組對應項，並檢查部署資訊清單。 部署資訊清單是 JSON 檔案，會宣告需要啟動的模組。

部署資訊清單中的每個專案都包含關於模組的特定資訊，由 IoT Edge 代理程式用來控制模組的生命週期。 如需 IoT Edge 代理程式用來控制模組之所有屬性的詳細資訊，請參閱 [IoT Edge 代理程式和 IoT Edge 中樞模組 twins 的屬性](module-edgeagent-edgehub.md)。

IoT Edge 代理程式會將執行階段回應傳送到 IoT 中樞。 以下是可能回應的清單：
  
* 200 - 確定
* 400 - 部署設定不正確或無效。
* 417-裝置沒有部署設定集。
* 412 - 部署設定中的結構描述版本無效。
* 406 - IoT Edge 裝置已離線或無法傳送狀態報表。
* 500 - IoT Edge 執行階段發生錯誤。

如需有關建立部署資訊清單的詳細資訊，請參閱 [瞭解如何在 IoT Edge 中部署模組及建立路由](module-composition.md)。

### <a name="security"></a>安全性

IoT Edge 代理程式在 IoT Edge 裝置的安全性中扮演了關鍵角色。 例如，它會執行像是先驗證模組的映射再啟動的動作。

如需有關 Azure IoT Edge 安全性架構的詳細資訊，請參閱 [IoT Edge 安全性管理員](iot-edge-security-manager.md)。

## <a name="iot-edge-hub"></a>IoT Edge 中樞

IoT Edge 中樞是組成 Azure IoT Edge 執行時間的另一個模組。 它藉由公開與 IoT 中樞相同的通訊協定端點來作為 IoT 中樞的本機 Proxy。 這種一致性表示用戶端可以連線到 IoT Edge 執行時間，就像對 IoT 中樞一樣。

IoT Edge 中樞不是在本機執行的 IoT 中樞完整版本。 IoT Edge 中樞會以無訊息方式將某些工作委派給 IoT 中樞。 例如，IoT Edge 中樞會在其第一個連線時自動從 IoT 中樞下載授權資訊，讓裝置能夠連線。 建立第一個連線之後，IoT Edge 中樞會在本機快取授權資訊。 來自該裝置的未來連接會獲得授權，而不需要再次從雲端下載授權資訊。

### <a name="cloud-communication"></a>雲端通訊

為了降低 IoT Edge 解決方案所使用的頻寬，IoT Edge 中樞會將對雲端進行的實際連線數目優化。 IoT Edge 中樞會從模組或下游裝置取得邏輯連線，並將它們結合成單一實體連線至雲端。 此程序的詳細資料對解決方案的其餘部分而言是透明的。 用戶端認為它們有自己的連線可連至雲端，即使它們全部都會透過相同連線來傳送。 IoT Edge 中樞可以使用 AMQP 或 MQTT 通訊協定，與下游裝置所使用的通訊協定分開與雲端通訊。 不過，IoT Edge hub 目前僅支援使用 AMQP 做為上游通訊協定和其多工功能，將邏輯連線合併為單一實體連線。 AMQP 是預設的上游通訊協定。

![IoT Edge 中樞是實體裝置與 IoT 中樞之間的閘道](./media/iot-edge-runtime/gateway-communication.png)

IoT Edge 中樞可以判斷是否已連線到 IoT 中樞。 如果連線中斷，IoT Edge 中樞就會在本機儲存訊息或對應項更新。 一旦連線重新建立之後，就會將所有資料同步。 此暫存快取所使用的位置取決於 IoT Edge 中樞模組對應項的屬性。 快取的大小不會受限，而且只要裝置還有儲存體容量就會持續成長。 如需詳細資訊，請參閱 [離線功能](offline-capabilities.md)。

<!-- <1.1> -->
::: moniker range="iotedge-2018-06"

### <a name="module-communication"></a>模組通訊

IoT Edge 中樞可促進模組對模組的通訊。 使用 IoT Edge 中樞作為訊息代理程式，讓模組彼此保持獨立。 模組只需指定它們要在其中接受訊息的輸入，以及要將訊息寫入其中的輸出。 方案開發人員可以將這些輸入和輸出結合在一起，讓模組依該解決方案的特定連續處理資料。

![IoT Edge 中樞可促進模組對模組的通訊](./media/iot-edge-runtime/module-endpoints.png)

為了將資料傳送至 IoT Edge 中樞，模組會呼叫 SendEventAsync 方法。 第一個引數會指定在哪個輸出上傳送訊息。 下列虛擬虛擬會在 **output1** 上傳送訊息：

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings);
   await client.OpenAsync();
   await client.SendEventAsync("output1", message);
   ```

若要接收訊息，請註冊會處理來自特定輸入之訊息的回呼。 下列虛擬程式碼會註冊函式 messageProcessor，以用來處理 **input1** 上收到的所有訊息：

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

如需 ModuleClient 類別及其通訊方法的詳細資訊，請參閱您慣用 SDK 語言的 API 參考： [c #](/dotnet/api/microsoft.azure.devices.client.moduleclient)、 [c](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)、 [Python](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient)、 [JAVA](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)或 [Node.js](/javascript/api/azure-iot-device/moduleclient)。

解決方案開發人員會負責指定規則，以判斷 IoT Edge 中樞在模組之間傳遞訊息的方式。 路由規則會定義于雲端中，並在其模組對應項中向下推送至 IoT Edge 中樞。 適用於 IoT 中樞路由的相同語法，可用來定義 Azure IoT Edge 中模組之間的路由。 如需詳細資訊，請參閱[了解如何在 IoT Edge 中部署模組及建立路由](module-composition.md)。

![模組之間的路由會經由 IoT Edge 中樞](./media/iot-edge-runtime/module-endpoints-routing.png)
::: moniker-end

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

### <a name="local-communication"></a>本機通訊

IoT Edge hub 可加速本機通訊。 它透過代理訊息來讓裝置與模組彼此獨立，讓裝置對模組、模組對模組的裝置對裝置通訊更加容易。

>[!NOTE]
> MQTT 訊息代理程式功能處於公開預覽狀態，IoT Edge 1.2 版。 它必須明確地啟用。

IoT Edge hub 支援兩種代理機制：

1. [IoT 中樞和所支援的訊息路由功能](../iot-hub/iot-hub-devguide-messages-d2c.md)
2. 符合[MQTT standard v 3.1.1](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html)的一般用途 MQTT 訊息代理程式

#### <a name="using-routing"></a>使用路由

第一個代理機制會利用與 IoT 中樞相同的路由功能，來指定在裝置或模組之間傳遞訊息的方式。 第一個裝置或模組會指定其接受訊息的輸入，以及它們寫入訊息的輸出。 然後解決方案開發人員可以在來源（例如輸出）和目的地（例如輸入）之間路由傳送訊息，並提供可能的篩選準則。

![模組之間的路由會經由 IoT Edge 中樞](./media/iot-edge-runtime/module-endpoints-routing.png)

透過 AMQP 或 MQTT 通訊協定，透過 Azure IoT 裝置 Sdk 建立的裝置或模組可以使用路由。 支援所有訊息 IoT 中樞基本專案，例如遙測、直接方法、C2D、twins，但不支援透過使用者定義的主題進行通訊。

如需路由的詳細資訊，請參閱 [瞭解如何在 IoT Edge 中部署模組及建立路由](module-composition.md)

#### <a name="using-the-mqtt-broker"></a>使用 MQTT 訊息代理程式

第二個代理機制是以標準 MQTT 訊息代理程式為基礎。 MQTT 是輕量的訊息傳輸通訊協定，可確保資源受限裝置的最佳效能，而且是受歡迎的發佈和訂閱標準。 裝置或模組會訂閱主題，以接收其他裝置或模組所發佈的訊息。 IoT Edge 中樞會執行其專屬的 MQTT 訊息代理程式，其遵循 [MQTT 版本3.1.1 的規格](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html)。

相較于路由：本機廣播和點對點通訊，MQTT broker 可提供兩種額外的通訊模式。 當某個裝置或模組需要在本機警示多個其他裝置或模組時，本機廣播就很有用。 點對點通訊可讓兩個 IoT Edge 裝置或兩個 IoT 裝置在本機進行通訊，而不需要來回存取雲端。

![使用 IoT Edge hub 在本機發佈和訂閱](./media/iot-edge-runtime/local-communnication-mqtt-broker.png)

使用 Azure IoT 裝置 Sdk （透過 MQTT 通訊協定或任何一般用途 MQTT 用戶端進行通訊）所建立的裝置或模組可以使用 MQTT 訊息代理程式。 除了 C2D 所有訊息 IoT 中樞基本專案（例如遙測、直接方法、twins）之外。 Iot 中樞基本專案所使用的 IoT 中樞特殊主題受支援，因此是使用者定義的主題。
本主題可以是 IoT 中樞的特殊主題或使用者自訂主題。

與路由機制不同的是，訊息的排序只是最適合的工作，而且不保證訊息篩選和訊息篩選不受訊息代理程式支援。 但是，缺乏這些功能會讓 MQTT 訊息代理程式比路由更快。

如需 MQTT 訊息代理程式的詳細資訊，請參閱 [發佈和訂閱 IoT Edge](how-to-publish-subscribe.md)

#### <a name="comparison-between-brokering-mechanisms"></a>代理機制的比較

以下是每個代理機制的可用功能：

|特性  | 路由  | MQTT broker  |
|---------|---------|---------|
|D2C 遙測    |     &#10004;    |         |
|本機遙測     |     &#10004;    |    &#10004;     |
|DirectMethods     |    &#10004;     |    &#10004;     |
|雙     |    &#10004;     |    &#10004;     |
|適用于裝置的 C2D     |   &#10004;      |         |
|排序     |    &#10004;     |         |
|篩選     |     &#10004;    |         |
|使用者定義主題     |         |    &#10004;     |
|裝置到裝置     |         |    &#10004;     |
|本機廣播     |         |    &#10004;     |
|效能     |         |    &#10004;     |

### <a name="connecting-to-the-iot-edge-hub"></a>連接到 IoT Edge 中樞

IoT Edge 中樞會接受來自裝置或模組用戶端的連線，而不是透過 MQTT 通訊協定或 AMQP 通訊協定。

>[!NOTE]
> IoT Edge hub 支援使用 MQTT 或 AMQP 連線的用戶端。 並不支援使用 HTTP 的用戶端。

當用戶端連線到 IoT Edge 中樞時，會發生下列情況：

1. 如果使用傳輸層安全性 (TLS)  (建議的) ，則會建立 TLS 通道以建立用戶端與 IoT Edge 中樞之間的加密通訊。
2. 驗證資訊會從用戶端傳送到 IoT Edge hub 以識別自己的身分。
3. IoT Edge 中樞會根據其授權原則來授權或拒絕連接。

#### <a name="secure-connections-tls"></a> (TLS) 的安全連線

根據預設，IoT Edge hub 只接受以傳輸層安全性 (TLS) 保護的連線，例如協力廠商無法解密的加密連線。

如果用戶端連線到 IoT Edge 中樞的埠 8883 (MQTTS) 或 5671 (AMQPS) ，則必須建立 TLS 通道。 在 TLS 信號交換期間，IoT Edge 中樞會將用戶端需要驗證的憑證鏈傳送給它。 為了驗證憑證鏈，IoT Edge 中樞的根憑證必須安裝為用戶端上的受信任憑證。 如果根憑證不受信任，IoT Edge 中樞將會拒絕用戶端程式庫，憑證驗證錯誤。

「 [透明閘道](how-to-create-transparent-gateway.md) 」和「 [準備下游裝置](how-to-connect-downstream-device.md#prepare-a-downstream-device) 」檔中會說明在裝置用戶端上安裝此代理程式根憑證的步驟。 模組可以利用 IoT Edge daemon API，來使用與 IoT Edge 中樞相同的根憑證。

#### <a name="authentication"></a>驗證

IoT Edge 中樞只接受來自具有 IoT 中樞身分識別的裝置或模組的連線，例如已在 IoT 中樞註冊，且具有 IoT 中樞支援的三種用戶端驗證方法之一來證明其身分識別： [對稱金鑰驗證](how-to-authenticate-downstream-device.md#symmetric-key-authentication)（ [x.509 自我簽署驗證](how-to-authenticate-downstream-device.md#x509-self-signed-authentication)、 [x.509 CA 簽署的驗證](how-to-authenticate-downstream-device.md#x509-ca-signed-authentication)）。  IoT Edge 中樞可以在本機驗證這些 IoT 中樞身分識別，以便在離線時仍可進行連接。

注意：

* IoT Edge 模組目前僅支援對稱金鑰驗證。
* IoT Edge hub MQTT 訊息代理程式不接受只有本機使用者名稱和密碼的 MQTT 用戶端，他們必須使用 IoT 中樞身分識別。

#### <a name="authorization"></a>授權

經過驗證後，IoT Edge 中樞有兩種授權用戶端連線的方式：

* 藉由確認用戶端屬於 IoT 中樞內定義的一組受信任用戶端。 這組受信任的用戶端是藉由在 IoT 中樞設定父系/子系或裝置/模組關聯性來指定。 在 IoT Edge 中建立模組時，會自動在此模組與其 IoT Edge 裝置之間建立信任關係。 這是路由代理機制唯一支援的授權模型。

* 藉由設定授權原則。 此授權原則是一份檔，列出可存取 IoT Edge hub 上資源的所有授權用戶端身分識別。 這是 IoT Edge hub MQTT 訊息代理程式所使用的主要授權模型，但 MQTT broker for IoT 中樞主題也可以瞭解父系/子系和裝置/模組關聯性。

### <a name="remote-configuration"></a>遠端設定

IoT Edge 中樞完全由雲端控制。 它會透過其 [模組](iot-edge-modules.md#module-twins)對應項從 IoT 中樞取得其設定。 其中包括：

* 路由設定
* 在命名空間層級設定的授權原則
* MQTT 橋接器設定

此外，您可以設定 [IoT Edge hub 上的環境變數](https://github.com/Azure/iotedge/blob/master/doc/EnvironmentVariables.md)來完成數個設定。
<!-- </1.2> -->
::: moniker-end

## <a name="runtime-quality-telemetry"></a>執行時間品質遙測

IoT Edge 會從主機執行時間和系統模組收集匿名遙測，以改善產品品質。 這項資訊稱為「執行時間品質遙測」。 收集的遙測會定期從 IoT Edge 代理程式以裝置到雲端訊息傳送至 IoT 中樞。 這些訊息不會出現在客戶的一般遙測中，也不會使用任何訊息配額。

IoT Edge 代理程式和中樞會產生您可以收集的計量，以瞭解裝置效能。 IoT Edge 代理程式會收集這些計量的子集，作為執行時間品質遙測的一部分。 針對執行時間品質遙測收集的計量會標示標記 `ms_telemetry` 。 如需所有可用計量的詳細資訊，請參閱 [存取內建計量](how-to-access-built-in-metrics.md)。

在上傳之前，會先移除任何個人或組織識別資訊（例如裝置和模組名稱），以確保執行時間品質遙測的匿名性質。

IoT Edge 代理程式每小時會收集遙測資料，每隔24小時就會將一則訊息傳送至 IoT 中樞。

如果您想要退出從裝置傳送執行時間遙測，有兩種方式可以執行這項操作：

* 設定 `SendRuntimeQualityTelemetry` edgeAgent 的環境變數 `false` ， 或
* 在部署期間取消選取 [Azure 入口網站中的選項。

## <a name="next-steps"></a>後續步驟

* [了解 Azure IoT Edge 模組](iot-edge-modules.md)
* [了解如何在 IoT Edge 中部署模組及建立路由](module-composition.md)
* [瞭解如何發佈和訂閱 IoT Edge](how-to-publish-subscribe.md)
* [瞭解 IoT Edge 執行時間計量](how-to-access-built-in-metrics.md)
