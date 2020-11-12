---
title: 下游裝置的閘道 - Azure IoT Edge | Microsoft Docs
description: 使用 Azure IoT Edge 來建立透明、不透明或 Proxy 閘道裝置，將資料從多個下游裝置傳送到雲端或在本機處理。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 83e8089073f7e7e7634ddf00f7276e12aaf645b0
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94536433"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>如何使用 IoT Edge 裝置作為閘道

IoT Edge 裝置可以作為閘道運作，以提供網路上其他裝置與 IoT 中樞之間的連線。

IoT Edge hub 模組的運作方式就像是 IoT 中樞，因此可以從任何具有 IoT 中樞身分識別的裝置（包括其他 IoT Edge 裝置）處理連接。 這種類型的閘道模式稱為 *透明* ，因為訊息可以從下游裝置傳遞到 IoT 中樞，如同它們之間沒有閘道。

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
從 IoT Edge 的1.2 版開始，透明閘道可以處理來自其他 IoT Edge 裝置的下游連線。
::: moniker-end

針對不是或無法自行連線到 IoT 中樞的裝置，IoT Edge 閘道可以提供該連線。 這種類型的閘道模式稱為 *轉譯* ，因為 IoT Edge 裝置必須在傳入的下游裝置訊息上執行處理，才能將它們轉送至 IoT 中樞。 這些案例需要 IoT Edge 閘道上的額外模組來處理處理步驟。

透明和轉譯閘道模式不是互斥的。 單一 IoT Edge 裝置可作為透明閘道和轉譯閘道。

所有閘道模式均可提供下列優點：

* **Edge 上的分析** -在本機使用 AI 服務來處理來自下游裝置的資料，而不需要將完整的遙測資料傳送至雲端。 在本機尋找及回應深入解析，而且只將部分資料傳送到 IoT 中樞。
* **下游裝置隔離** – 閘道裝置可以防護所有下游裝置，以免暴露於網際網路中。 它可以放在營運技術 () 網路之間沒有連線能力，而資訊技術 (它) 網路來提供 web 的存取權。 同樣地，沒有能力連線到 IoT 中樞的裝置也可以改為連線到閘道裝置。
* **連線多工作業** - 所有透過 IoT Edge 閘道連線至 IoT 中樞的裝置都會使用相同的基礎連線。
* **流量平滑化** - 如果 IoT 中樞發生節流，IoT Edge 裝置會自動實作指數輪詢，同時在本機保存訊息。 這項權益可使您的方案恢復到流量尖峰。
* **離線支援** -閘道裝置會儲存無法傳遞至 IoT 中樞的訊息和對應項更新。

## <a name="transparent-gateways"></a>透明閘道

在透明閘道模式中，理論上可以連線到 IoT 中樞的裝置可以改為連接到閘道裝置。 下游裝置有自己的 IoT 中樞身分識別，並使用 MQTT 或 AMQP 通訊協定進行連線。 閘道只會在裝置與 IoT 中樞之間傳遞通訊。 裝置和透過 IoT 中樞與其互動的使用者，不會察覺到閘道正在調節其通訊。 缺乏認知表示閘道被視為 *透明* 。

<!-- 1.0.10 -->
::: moniker range="iotedge-2018-06"

IoT Edge 的裝置不能是 IoT Edge 閘道的下游。

![圖表-透明閘道模式](./media/iot-edge-as-gateway/edge-as-gateway-transparent.png)

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

從版本1.2.0 開始，IoT Edge 的裝置可以透過透明閘道連接。

<!-- TODO add a downstream IoT Edge device to graphic -->

::: moniker-end

### <a name="parent-and-child-relationships"></a>父項和子系關聯性

您可以在 IoT 中樞中宣告透明的閘道關聯性，方法是將 IoT Edge 閘道設定為連接到它的下游裝置 *子* 系的 *父系* 。

父/子關聯性會在閘道設定中的三個點上建立：

#### <a name="cloud-identities"></a>雲端身分識別

透明閘道案例中的所有裝置都需要雲端身分識別，才能向 IoT 中樞進行驗證。 當您建立或更新裝置身分識別時，可以設定裝置的父系或子裝置。 這項設定會授權父閘道裝置處理其子裝置的驗證。

子裝置只能有一個父系。 每個父系最多可有100個子系。

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
IoT Edge 裝置可以是透明閘道關聯性中的父代和子系。 您可以建立多個向彼此報告的 IoT Edge 裝置階層。 閘道階層的最上層節點最多可以有五代的子系。 例如，IoT Edge 裝置可以有五層的 IoT Edge 裝置連結為其下的子系。 但是第五代的 IoT Edge 裝置不能有任何子系，IoT Edge 或其他。
::: moniker-end

#### <a name="gateway-discovery"></a>閘道探索

子裝置必須能夠在區域網路上找到其父裝置。 使用 **主機名稱** (FQDN) 的完整功能變數名稱或 IP 位址來設定閘道裝置，以供其子裝置用來尋找它。

在下游 IoT 裝置上，使用連接字串中的 **gatewayHostname** 參數指向父裝置。

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
在下游 IoT Edge 裝置上，使用 yaml 檔案中的 **parent_hostname** 參數來指向父裝置。
::: moniker-end

#### <a name="secure-connection"></a>安全連線

父裝置和子系裝置也需要驗證彼此的連接。 每個裝置都需要一個共用根 CA 憑證的複本，子裝置會用它來確認它們是否連線到正確的閘道。

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
當多個 IoT Edge 閘道在閘道階層中彼此連接時，階層中的所有裝置都應使用單一憑證鏈。
::: moniker-end

### <a name="device-capabilities-behind-transparent-gateways"></a>透明閘道後方的裝置功能

搭配 IoT Edge 的訊息管線使用的所有 IoT 中樞基本專案也支援透明閘道案例。 每個 IoT Edge 閘道都有訊息的儲存和轉送功能。

使用下表查看與閘道後方裝置相較之下，裝置支援不同 IoT 中樞功能的方式。

<!-- 1.0.10 -->
::: moniker range="iotedge-2018-06"

| 功能 | IoT 裝置 | 閘道後方的 IoT |
| ---------- | ---------- | -------------------- |
| [裝置到雲端 (D2C) 訊息](../iot-hub/iot-hub-devguide-messages-d2c.md) |  ![是-IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![是-子 IoT D2C](./media/iot-edge-as-gateway/check-yes.png) |
| [雲端到裝置 (C2D) 訊息](../iot-hub/iot-hub-devguide-messages-c2d.md) | ![是-IoT C2D](./media/iot-edge-as-gateway/check-yes.png) | ![是-IoT 子 C2D](./media/iot-edge-as-gateway/check-yes.png) |
| [直接方法](../iot-hub/iot-hub-devguide-direct-methods.md) | ![是-IoT 直接方法](./media/iot-edge-as-gateway/check-yes.png) | ![是-子 IoT 直接方法](./media/iot-edge-as-gateway/check-yes.png) |
| [裝置 twins](../iot-hub/iot-hub-devguide-device-twins.md) 和 [模組 twins](../iot-hub/iot-hub-devguide-module-twins.md) | ![是-IoT twins](./media/iot-edge-as-gateway/check-yes.png) | ![是-子 IoT twins](./media/iot-edge-as-gateway/check-yes.png) |
| [檔案上傳](../iot-hub/iot-hub-devguide-file-upload.md) | ![是-IoT 檔案上傳](./media/iot-edge-as-gateway/check-yes.png) | ![否-IoT 子檔案上傳](./media/iot-edge-as-gateway/crossout-no.png) |

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

| 功能 | IoT 裝置 | 閘道後方的 IoT | IoT Edge 裝置 | 閘道後方的 IoT Edge |
| ---------- | ---------- | --------------------------- | --------------- | -------------------------------- |
| [裝置到雲端 (D2C) 訊息](../iot-hub/iot-hub-devguide-messages-d2c.md) |  ![是-IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![是-子 IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![是-IoT Edge D2C](./media/iot-edge-as-gateway/check-yes.png) | ![是-子 IoT Edge D2C](./media/iot-edge-as-gateway/check-yes.png) |
| [雲端到裝置 (C2D) 訊息](../iot-hub/iot-hub-devguide-messages-c2d.md) | ![是-IoT C2D](./media/iot-edge-as-gateway/check-yes.png) | ![是-IoT 子 C2D](./media/iot-edge-as-gateway/check-yes.png) | ![無 IoT Edge C2D](./media/iot-edge-as-gateway/crossout-no.png) | ![無 IoT Edge 子 C2D](./media/iot-edge-as-gateway/crossout-no.png) |
| [直接方法](../iot-hub/iot-hub-devguide-direct-methods.md) | ![是-IoT 直接方法](./media/iot-edge-as-gateway/check-yes.png) | ![是-子 IoT 直接方法](./media/iot-edge-as-gateway/check-yes.png) | ![Yes-IoT Edge 直接方法](./media/iot-edge-as-gateway/check-yes.png) | ![是-子 IoT Edge 直接方法](./media/iot-edge-as-gateway/check-yes.png) |
| [裝置 twins](../iot-hub/iot-hub-devguide-device-twins.md) 和 [模組 twins](../iot-hub/iot-hub-devguide-module-twins.md) | ![是-IoT twins](./media/iot-edge-as-gateway/check-yes.png) | ![是-子 IoT twins](./media/iot-edge-as-gateway/check-yes.png) | ![是-IoT Edge twins](./media/iot-edge-as-gateway/check-yes.png) | ![是-子 IoT Edge twins](./media/iot-edge-as-gateway/check-yes.png) |
| [檔案上傳](../iot-hub/iot-hub-devguide-file-upload.md) | ![是-IoT 檔案上傳](./media/iot-edge-as-gateway/check-yes.png) | ![否-IoT 子檔案上傳](./media/iot-edge-as-gateway/crossout-no.png) | ![無 IoT Edge 檔案上傳](./media/iot-edge-as-gateway/crossout-no.png) | ![無 IoT Edge 的子檔案上傳](./media/iot-edge-as-gateway/crossout-no.png) |
| 容器映射提取 |   |   | ![是-IoT Edge 容器提取](./media/iot-edge-as-gateway/check-yes.png) | ![是-子 IoT Edge 容器提取](./media/iot-edge-as-gateway/check-yes.png) |
| Blob 上傳 |   |   | ![是-IoT Edge blob 上傳](./media/iot-edge-as-gateway/check-yes.png) | ![是-子 IoT Edge blob 上傳](./media/iot-edge-as-gateway/check-yes.png) |

您可以從父裝置下載、儲存和傳遞 **容器映射** 到子裝置。

**Blob** （包括支援配套和記錄）可以從子裝置上傳至父裝置。

::: moniker-end

## <a name="translation-gateways"></a>轉譯閘道

如果下游裝置無法連線到 IoT 中樞，則 IoT Edge 閘道需要作為 translator。 通常，不支援 MQTT、AMQP 或 HTTP 的裝置需要此模式。 因為這些裝置無法連線到 IoT 中樞，所以它們也無法連線至 IoT Edge 中樞模組，而不需要進行一些前置處理。

通常會針對下游裝置的硬體或通訊協定特定的自訂或協力廠商模組，必須部署到 IoT Edge 閘道。 這些轉譯模組會接受內送訊息，並將其轉換為 IoT 中樞可理解的格式。

轉譯閘道有兩種模式： *通訊協定轉譯* 和身分 *識別轉譯* 。

![圖表-轉譯閘道模式](./media/iot-edge-as-gateway/edge-as-gateway-translation.png)

### <a name="protocol-translation"></a>通訊協定翻譯

在通訊協定轉譯閘道模式中，只有 IoT Edge 閘道具有 IoT 中樞的身分識別。 轉譯模組會接收來自下游裝置的訊息、將它們轉譯成支援的通訊協定，然後 IoT Edge 裝置會代表下游裝置傳送訊息。 所有資訊看似來自一部裝置 (也就是閘道)。 如果雲端應用程式想要以每個裝置為基礎來分析資料，則下游裝置必須在其訊息中內嵌額外的識別資訊。 此外，只有閘道裝置（而非下游裝置）支援 IoT 中樞基本類型（例如 twins 和直接方法）。 相較于透明閘道，此模式中的閘道會視為不 *透明* ，因為它們會混淆下游裝置的身分識別。

通訊協定轉譯支援資源受限的裝置。 許多現有裝置會產生可增強商業深入解析的資料，不過其設計並未考量雲端連線能力。 不透明閘道可讓此資料解除鎖定，並在 IoT 解決方案中使用。

### <a name="identity-translation"></a>身分識別協定翻譯

身分識別轉譯閘道模式是以通訊協定轉譯為基礎，但 IoT Edge 閘道也會代表下游裝置提供 IoT 中樞裝置身分識別。 轉譯模組負責瞭解下游裝置所使用的通訊協定，提供它們的身分識別，並將其訊息轉譯成 IoT 中樞基本專案。 下游裝置會以具有對應項和方法的第一級裝置形式出現在 IoT 中樞內。 使用者可與 IoT 中樞內的裝置互動，而且不會察覺到中繼閘道裝置的存在。

身分識別轉譯提供了通訊協定轉譯的優點，此外還可讓您從雲端進行下游裝置的完整管理。 不論 IoT 解決方案中的所有裝置使用的通訊協定為何，全都會出現在 IoT 中樞中。

### <a name="device-capabilities-behind-translation-gateways"></a>轉譯閘道後方的裝置功能

下表說明 IoT 中樞功能如何延伸至兩個轉譯閘道模式中的下游裝置。

| 功能 | 通訊協定翻譯 | 身分識別協定翻譯 |
| ---------- | -------------------- | -------------------- |
| 儲存在 IoT 中樞身分識別登錄中的身分識別 | 僅限閘道裝置的身分識別 | 所有已連線裝置的身分識別 |
| 裝置對應項 | 只有閘道有裝置和模組對應項 | 每個已連線的裝置都有自己的裝置對應項 |
| 直接方法及雲端到裝置訊息 | 雲端只可以處理閘道裝置 | 雲端可以個別處理每個連線的裝置 |
| [IoT 中樞節流與配額](../iot-hub/iot-hub-devguide-quotas-throttling.md) | 套用至閘道裝置 | 套用至每個裝置 |

使用通訊協定轉譯模式時，所有透過該閘道連線的裝置會共用相同的雲端到裝置佇列，其中最多可包含50的訊息。 只有在少數裝置透過每個現場閘道連線，而且其雲端到裝置的流量很低時，才使用此模式。

IoT Edge 執行時間不包含通訊協定或身分識別轉譯功能。 這些模式需要自訂或協力廠商模組，通常是使用的硬體和通訊協定專用。 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) 包含數個可供選擇的通訊協定轉譯模組。 如需使用身分識別轉譯模式的範例，請參閱 [Azure IoT Edge LoRaWAN 入門套件](https://github.com/Azure/iotedge-lorawan-starterkit)。

## <a name="next-steps"></a>後續步驟

瞭解設定透明閘道的三個步驟：

* [設定 IoT Edge 裝置作為透明閘道](how-to-create-transparent-gateway.md)
* [向 Azure IoT 中樞驗證下游裝置](how-to-authenticate-downstream-device.md)
* [將下游裝置連線到 Azure IoT Edge 閘道](how-to-connect-downstream-device.md)
