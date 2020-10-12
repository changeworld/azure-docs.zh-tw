---
title: 下游裝置的閘道 - Azure IoT Edge | Microsoft Docs
description: 使用 Azure IoT Edge 來建立透明、不透明或 Proxy 閘道裝置，將資料從多個下游裝置傳送到雲端或在本機處理。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/21/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 0589779de2ddb0bc75dde3b57d6444634b879f86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89017017"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>如何使用 IoT Edge 裝置作為閘道

IoT Edge 解決方案中的閘道可為 IoT 裝置提供裝置連線能力和邊緣分析，否則就不會有這些功能。 Azure IoT Edge 可以用來滿足 IoT 閘道的任何需求，不論它是否與連線能力、身分識別或邊緣分析相關。 本文中的閘道模式只是指下游裝置連線能力和裝置身分識別的特性，而非在閘道上處理裝置資料的方式。

## <a name="patterns"></a>模式

使用 IoT Edge 裝置作為閘道的模式有三種：透明、通訊協定轉譯和身分識別轉譯。

模式之間的主要差異在於透明閘道會在下游裝置與 IoT 中樞之間傳遞訊息，而不需要任何額外的處理。 不過，通訊協定轉譯和身分識別轉譯需要在閘道上進行處理，才能啟用通訊。

任何閘道都可以在將訊息從下游裝置傳遞到 IoT 中樞之前，使用 IoT Edge 模組在邊緣執行分析或預先處理。

![圖表 - 透明、通訊協定和身分識別閘道模式](./media/iot-edge-as-gateway/edge-as-gateway.png)

### <a name="transparent-pattern"></a>透明模式

在 *透明* 的閘道模式中，理論上可以連線到 IoT 中樞的裝置可以改為連線到閘道裝置。 下游裝置有自己的 IoT 中樞身分識別，且會使用 MQTT、AMQP 或 HTTP 通訊協定的任一個。 閘道只會在裝置與 IoT 中樞之間傳遞通訊。 裝置和透過 IoT 中樞與其互動的使用者，不會察覺到閘道正在調節其通訊。 缺乏認知表示閘道被視為 *透明*。

IoT Edge 執行時間包含透明的閘道功能。 如需詳細資訊，請參閱[設定 IoT Edge 裝置作為透明閘道](how-to-create-transparent-gateway.md)。

### <a name="protocol-translation-pattern"></a>通訊協定轉譯模式

*通訊協定轉譯*閘道也稱為不*透明*閘道，相較于透明閘道模式。 在此模式中，不支援 MQTT、AMQP 或 HTTP 的裝置可以使用閘道裝置來代表其將資料傳送給 IoT 中樞。 閘道了解下游裝置所使用的通訊協定，並且是唯一在 IoT 中樞內具有身分識別的裝置。 所有資訊看似來自一部裝置 (也就是閘道)。 如果雲端應用程式想要以每個裝置為基礎來分析資料，則下游裝置必須在其訊息中內嵌額外的識別資訊。 此外，IoT 中樞基本類型 (例如對應項和方法) 僅適用於閘道裝置，而不適用於下游裝置。

IoT Edge 執行時間不包含通訊協定轉譯功能。 此模式需要自訂或協力廠商模組，通常是使用的硬體和通訊協定專用。 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) 包含數個可供選擇的通訊協定轉譯模組。

### <a name="identity-translation-pattern"></a>身分識別轉譯模式

在身分 *識別轉譯* 閘道模式中，無法連線到 IoT 中樞的裝置可以改為連線到閘道裝置。 閘道會代表下游裝置提供 IoT 中樞身分識別和通訊協定轉譯。 閘道很聰明，足以了解下游裝置所使用的通訊協定、為這些裝置提供身分識別，以及轉譯 IoT 中樞基本類型。 下游裝置會以具有對應項和方法的第一級裝置形式出現在 IoT 中樞內。 使用者可與 IoT 中樞內的裝置互動，而且不會察覺到中繼閘道裝置的存在。

IoT Edge 執行時間不包含身分識別轉譯功能。 此模式需要自訂或協力廠商模組，通常是使用的硬體和通訊協定專用。 如需使用身分識別轉譯模式的範例，請參閱 [Azure IoT Edge LoRaWAN 入門套件](https://github.com/Azure/iotedge-lorawan-starterkit)。

## <a name="use-cases"></a>使用案例

所有閘道模式均可提供下列優點：

* **Edge 上的分析** -在本機使用 AI 服務來處理來自下游裝置的資料，而不需要將完整的遙測資料傳送至雲端。 在本機尋找及回應深入解析，而且只將部分資料傳送到 IoT 中樞。
* **下游裝置隔離** – 閘道裝置可以防護所有下游裝置，以免暴露於網際網路中。 它可位於沒有連線能力的 OT 網路與可供存取 Web 的 IT 網路之間。
* **連線多工作業** - 所有透過 IoT Edge 閘道連線至 IoT 中樞的裝置都會使用相同的基礎連線。
* **流量平滑化** - 如果 IoT 中樞發生節流，IoT Edge 裝置會自動實作指數輪詢，同時在本機保存訊息。 這項權益可使您的方案恢復到流量尖峰。
* **離線支援** -閘道裝置會儲存無法傳遞至 IoT 中樞的訊息和對應項更新。

進行通訊協定轉譯的閘道可支援現有裝置和資源受限的新裝置。 許多現有裝置會產生可增強商業深入解析的資料，不過其設計並未考量雲端連線能力。 不透明閘道可讓此資料解除鎖定，並在 IoT 解決方案中使用。

進行身分識別轉譯的閘道可提供通訊協定轉譯的優點，此外還可從雲端進行下游裝置的完整管理。 不論 IoT 解決方案中的所有裝置使用的通訊協定為何，全都會出現在 IoT 中樞中。

## <a name="cheat-sheet"></a>功能提要

以下的速查表提供使用透明、不透明 (通訊協定) 和 Proxy 閘道時的 IoT 中樞基本項目比較。

| Primitive | 透明閘道 | 通訊協定翻譯 | 身分識別協定翻譯 |
|--------|-------------|--------|--------|
| 儲存在 IoT 中樞身分識別登錄中的身分識別 | 所有已連線裝置的身分識別 | 僅限閘道裝置的身分識別 | 所有已連線裝置的身分識別 |
| 裝置對應項 | 每個已連線的裝置都有自己的裝置對應項 | 只有閘道有裝置和模組對應項 | 每個已連線的裝置都有自己的裝置對應項 |
| 直接方法及雲端到裝置訊息 | 雲端可以個別處理每個連線的裝置 | 雲端只可以處理閘道裝置 | 雲端可以個別處理每個連線的裝置 |
| [IoT 中樞節流與配額](../iot-hub/iot-hub-devguide-quotas-throttling.md) | 套用至每個裝置 | 套用至閘道裝置 | 套用至每個裝置 |

使用不透明的閘道 (通訊協定轉譯) 模式時，所有透過該閘道連線的裝置會共用相同的雲端到裝置佇列，其中可以包含最多 50 則訊息。 因此，不透明閘道模式應僅在少數裝置透過每個現場閘道連線，及其雲端到裝置流量較低時使用。

## <a name="next-steps"></a>後續步驟

瞭解設定透明閘道的三個步驟：

* [設定 IoT Edge 裝置作為透明閘道](how-to-create-transparent-gateway.md)
* [向 Azure IoT 中樞驗證下游裝置](how-to-authenticate-downstream-device.md)
* [將下游裝置連線到 Azure IoT Edge 閘道](how-to-connect-downstream-device.md)
