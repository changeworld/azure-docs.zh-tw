---
title: 開發適用於 Azure IoT Edge 的模組 | Microsoft Docs
description: 開發可與執行階段和 IoT 中樞通訊的 Azure IoT Edge 自訂模組
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8907af07fff7b315eec263d38b686c17218ed9d2
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445467"
---
# <a name="develop-your-own-iot-edge-modules"></a>開發您自己的 IoT Edge 模組

Azure IoT Edge 模組可以與其他 Azure 服務連線，並提供給您更大的雲端資料管線。 本文說明如何開發模組以便與 IoT Edge 執行階段和 IoT 中樞進行通訊，從而開發 Azure 雲端的其餘部分。

## <a name="iot-edge-runtime-environment"></a>IoT Edge 執行階段環境

IoT Edge 執行階段提供基礎結構來整合多個 IoT Edge 模組的功能，並將其部署至 IoT Edge 裝置。 任何程式都可以封裝為 IoT Edge 模組。 若要充分利用 IoT Edge 通訊和管理功能，在模組中執行的程式可以使用 Azure IoT 裝置 SDK 來連線至本機 IoT Edge 中樞。
::: moniker range=">=iotedge-2020-11"
模組也可以使用任何 MQTT 用戶端連接到本機 IoT Edge hub MQTT 訊息代理程式。
::: moniker-end

### <a name="packaging-your-program-as-an-iot-edge-module"></a>將程式封裝為 IoT Edge 模組

若要在 IoT Edge 裝置上部署您的程式，必須先將程式容器化，並使用與 Docker 相容的引擎來執行。 IoT Edge 使用 [Moby](https://github.com/moby/moby)（docker 背後的開放原始碼專案）作為 docker 相容引擎。 您用於 Docker 的相同參數可以傳遞給 IoT Edge 模組。 如需詳細資訊，請參閱 [如何設定 IoT Edge 模組的容器建立選項](how-to-use-create-options.md)。

## <a name="using-the-iot-edge-hub"></a>使用 IoT Edge 中樞

IoT Edge 中樞提供兩個主要功能：Proxy 到 IoT 中樞，以及本機通訊。

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>從模組連線到 IoT Edge 中樞

從模組連接到本機 IoT Edge 中樞，會牽涉到與任何用戶端相同的連接步驟。 如需詳細資訊，請參閱 [連接到 IoT Edge 中樞](iot-edge-runtime.md#connecting-to-the-iot-edge-hub)。

若要在 AMQP 或 MQTT 上使用 IoT Edge 路由，您可以從 Azure IoT SDK 使用 ModuleClient。 建立 ModuleClient 執行個體，以便將您的模組連接到裝置上執行的 IoT Edge 中樞，這與 DeviceClient 執行個體將 IoT 裝置連接到 IoT 中樞的方式類似。 如需 ModuleClient 類別及其通訊方法的詳細資訊，請參閱您慣用 SDK 語言的 API 參考： [c #](/dotnet/api/microsoft.azure.devices.client.moduleclient)、 [c](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)、 [Python](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient)、 [JAVA](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)或 [Node.js](/javascript/api/azure-iot-device/moduleclient)。

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

若要使用 IoT Edge MQTT 訊息代理程式，您必須攜帶自己的 MQTT 用戶端，並使用您從 IoT Edge daemon 工作負載 API 中取出的資訊自行起始連接。 <!--Need to add details here-->

如需有關在路由或發佈/訂閱 MQTT 訊息代理程式之間進行選擇的詳細資訊，請參閱 [本機通訊](iot-edge-runtime.md#local-communication)。

### <a name="mqtt-broker-primitives"></a>MQTT broker 基本專案

#### <a name="send-a-message-on-a-user-defined-topic"></a>在使用者定義的主題上傳送訊息

使用 IoT Edge MQTT 訊息代理程式，您可以在任何使用者定義的主題上發佈訊息。 若要這樣做，請授權您的模組在特定主題上發佈，然後從工作負載 API 取得權杖，以在連線到 MQTT 訊息代理程式時做為密碼，最後使用您選擇的 MQTT 用戶端，在授權的主題上發佈訊息。

#### <a name="receive-messages-on-a-user-defined-topic"></a>在使用者定義的主題上接收訊息

使用 IoT Edge MQTT 訊息代理程式時，接收訊息很類似。 首先，請確定您的模組已獲得訂閱特定主題的授權，然後從工作負載 API 取得權杖，以在連線到 MQTT 訊息代理程式時做為密碼，最後使用您所選的 MQTT 用戶端來訂閱授權主題上的訊息。

::: moniker-end

### <a name="iot-hub-primitives"></a>IoT 中樞基本項目

IoT 中樞會看見一個類似裝置的模組執行個體，這表示：

* 它會傳送[裝置到雲端的訊息](../iot-hub/iot-hub-devguide-messaging.md)；
* 它可以接收特別在其身分識別上設為目標的[直接方法](../iot-hub/iot-hub-devguide-direct-methods.md)。
* 它有不同的模組對應項，並與 [裝置](../iot-hub/iot-hub-devguide-device-twins.md) 對應項和該裝置的其他模組 twins 隔離;

目前，模組無法接收雲端到裝置的訊息，或使用檔案上傳功能。

撰寫模組時，您可以連線至 IoT Edge 中樞，並使用 iot 中樞基本專案，如同使用 IoT 中樞搭配裝置應用程式時所做的一樣。 IoT Edge 模組和 IoT 裝置應用程式之間的唯一差異在於，您必須參考模組身分識別，而不是裝置身分識別。

#### <a name="device-to-cloud-messages"></a>裝置到雲端的訊息

IoT Edge 模組可透過作為本機訊息代理程式的 IoT Edge 中樞，將訊息傳送至雲端，並將訊息傳播至雲端。 若要啟用裝置到雲端訊息的複雜處理，IoT Edge 模組也可以攔截並處理其他模組或裝置傳送至其本機 IoT Edge 中樞的訊息，並以已處理的資料傳送新訊息。 因此，您可以建立 IoT Edge 模組的鏈，以建立本機處理管線。

若要使用路由傳送裝置到雲端的遙測訊息，請使用 Azure IoT SDK 的 ModuleClient。 使用 Azure IoT SDK 時，每個模組都有模組 *輸入* 和 *輸出* 端點的概念，這些端點會對應到特殊的 MQTT 主題。 使用 `ModuleClient.sendMessageAsync` 方法，它會在您模組的輸出端點上傳送訊息。 然後在 edgeHub 中設定路由，以將此輸出端點傳送至 IoT 中樞。

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

使用 MQTT broker 傳送裝置到雲端的遙測訊息，類似于在使用者定義的主題上發佈訊息，但針對您的模組使用下列 IoT 中樞特殊主題： `devices/<device_name>/<module_name>/messages/events` 。 授權必須適當地設定。 MQTT 橋接器也必須設定為將本主題的訊息轉送至雲端。

::: moniker-end

若要使用路由來處理訊息，請先設定路由，將來自另一個端點 (模組或裝置) 的訊息傳送至模組的輸入端點，然後在模組的輸入端點上接聽訊息。 每次傳回新的訊息時，Azure IoT SDK 都會觸發回呼函式。 使用這個回呼函式來處理您的訊息，並選擇性地在您的模組端點佇列上傳送新訊息。

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

使用 MQTT 訊息代理程式處理訊息類似于訂閱使用者定義主題的訊息，但使用模組輸出佇列的 IoT Edge 特殊主題： `devices/<device_name>/<module_name>/messages/events` 。 授權必須適當地設定。 （選擇性）您可以在您選擇的主題上傳送新訊息。

::: moniker-end

#### <a name="twins"></a>對應項

Twins 是 IoT 中樞所提供的其中一個基本專案。 有一些 JSON 檔會儲存狀態資訊，包括中繼資料、設定和條件。 每個模組或裝置都有自己的對應項。

若要取得 Azure IoT SDK 的模組對應項，請呼叫 `ModuleClient.getTwin` 方法。

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

若要取得 MQTT 用戶端的模組對應項，需要稍微多一點的工作，因為取得對應項並不是一般的 MQTT 模式。 此模組必須先訂閱 IoT 中樞特殊主題 `$iothub/twin/res/#` 。 此主題名稱會繼承自 IoT 中樞，而且所有裝置/模組都必須訂閱相同的主題。 這並不表示裝置會接收彼此的對應項。 IoT 中樞和 edgeHub 知道應該在何處傳遞對應項，即使所有裝置都會接聽相同的主題名稱。 一旦建立訂用帳戶，模組就必須將訊息發佈到下列具有要求識別碼的 IoT 中樞特殊主題，以要求對應項 `$iothub/twin/GET/?$rid=1234` 。 此要求識別碼是 (的任意識別碼，也就是 GUID) ，IoT 中樞將會連同要求的資料一起傳送回來。 這是用戶端可以將其要求與回應配對的方式。 結果碼是類似 HTTP 的狀態碼，其中成功的編碼為200。

::: moniker-end

若要使用 Azure IoT SDK 來接收模組對應項修補程式，請執行回呼函式，並向 Azure IoT SDK 中的方法註冊它， `ModuleClient.moduleTwinCallback` 以便在每次對應項修補程式進入時觸發您的回呼函式。

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

若要接收 MQTT 用戶端的模組對應項修補程式，此程式與接收完整 twins 非常類似：用戶端必須訂閱特殊的 IoT 中樞主題 `$iothub/twin/PATCH/properties/desired/#` 。 進行訂用帳戶之後，當 IoT 中樞傳送對應項所需區段的變更時，用戶端會收到該對應項的變更。

::: moniker-end

#### <a name="receive-direct-methods"></a>接收直接方法

若要使用 Azure IoT SDK 來接收直接方法，請執行回呼函式，並使用 `ModuleClient.methodCallback` 來自 Azure IOT sdk 的方法來註冊它，以便在每次直接方法進入時觸發您的回呼函數。

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

若要接收任何 MQTT 用戶端的直接方法，此程式與接收對應項修補程式非常類似。 用戶端需要確認它已收到呼叫，並且可以同時傳回部分資訊。 要訂閱的特殊 IoT 中樞主題為 `$iothub/methods/POST/#` 。

::: moniker-end

## <a name="language-and-architecture-support"></a>語言和架構支援

IoT Edge 支援多種作業系統、裝置架構和開發語言，因此您可以建立符合您需求的案例。 您可以使用本節來瞭解開發自訂 IoT Edge 模組的選項。 您可以在 [準備 IoT Edge 的開發和測試環境](development-environment.md)中，深入瞭解每種語言的工具支援和需求。

### <a name="linux"></a>Linux

針對下表中的所有語言，IoT Edge 支援 AMD64 和 ARM32 Linux 裝置的開發。

| 開發語言 | 開發工具 |
| -------------------- | ----------------- |
| C | Visual Studio Code<br>Visual Studio 2017/2019 |
| C# | Visual Studio Code<br>Visual Studio 2017/2019 |
| Java | Visual Studio Code |
| Node.js | Visual Studio Code |
| Python | Visual Studio Code |

>[!NOTE]
>ARM64 Linux 裝置的開發和偵錯工具支援處於 [公開預覽](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)狀態。 如需詳細資訊，請參閱[在 Visual Studio Code (預覽)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview) 中開發和偵錯 ARM64 IoT Edge 模組。

### <a name="windows"></a>Windows

針對下表中的所有語言，IoT Edge 支援 AMD64 Windows 裝置的開發。

| 開發語言 | 開發工具 |
| -------------------- | ----------------- |
| C | Visual Studio 2017/2019 |
| C# | Visual Studio Code (沒有任何調試功能) <br>Visual Studio 2017/2019 |

## <a name="next-steps"></a>後續步驟

[準備 IoT Edge 適用的開發和測試環境](development-environment.md)

[使用 Visual Studio 開發適用于 IoT Edge 的 c # 模組](how-to-visual-studio-develop-module.md)

[使用 Visual Studio Code 開發 IoT Edge 的模組](how-to-vs-code-develop-module.md)

[了解和使用 Azure IoT 中樞 SDK](../iot-hub/iot-hub-devguide-sdks.md)
