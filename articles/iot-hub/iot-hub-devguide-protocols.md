---
title: Azure IoT 中樞通訊協定和連接埠 | Microsoft Docs
description: 開發人員指南 - 說明裝置到雲端和雲端到裝置通訊所支援的通訊協定，以及必須開啟的連接埠號碼。
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 433e4160972a06ee3652410e062f6602e9ca6767
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82996911"
---
# <a name="reference---choose-a-communication-protocol"></a>參考 - 選擇通訊協定

「IoT 中樞」可讓裝置使用下列通訊協定來進行裝置端通訊：

* [MQTT](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf)
* 透過 WebSocket 的 MQTT
* [AMQP](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
* 透過 WebSocket 的 AMQP
* HTTPS

如需這些通訊協定如何支援特定 IoT 中樞功能的資訊，請參閱[裝置對雲端通訊指引](iot-hub-devguide-d2c-guidance.md)和[雲端對裝置通訊指引](iot-hub-devguide-c2d-guidance.md)。

下表針對您選擇的通訊協定提供概略的建議︰

| 通訊協定 | 應選擇此通訊協定的情況 |
| --- | --- |
| MQTT <br> 透過 WebSocket 的 MQTT |可在不需要透過相同 TLS 連線來連線到多個裝置 (各有自己的每一裝置認證) 的裝置上使用。 |
| AMQP <br> 透過 WebSocket 的 AMQP |在欄位和雲端閘道上使用，以利用跨裝置進行多工的連線。 |
| HTTPS |針對無法支援其他通訊協定的裝置使用。 |

當您選擇裝置端通訊的通訊協定時，考慮下列幾點：

* **雲端到裝置的模式**。 HTTPS 沒有可實作伺服器推送的有效方式。 因此，當您使用 HTTPS 時，裝置會輪詢「IoT 中樞」來了解是否有雲端到裝置的訊息。 這對於裝置和 IoT 中樞而言都沒有效率。 在目前的 HTTPS 指導方針中，每個裝置應該每隔 25 分鐘 (或更久) 進行一次訊息輪詢。 MQTT 和 AMQP 則支援在收到雲端到裝置訊息時進行伺服器推送。 它們能夠將訊息立即從 IoT 中樞推送到裝置。 如果傳遞延遲是一大考量，最好是使用 MQTT 或 AMQP 通訊協定。 針對很少連接的裝置，也適用 HTTPS。

* **現場閘道**。 MQTT 和 HTTPS 僅針對每個 TLS 連線支援單一裝置身分識別（裝置識別碼加認證）。 基於這個理由，這些通訊協定不支援[欄位閘道案例](iot-hub-devguide-endpoints.md#field-gateways)，需要在單一或上游連線集區中使用多個裝置身分識別，才能 IoT 中樞的訊息。 這類閘道可以使用針對其上游流量，針對每個連線（例如 AMQP）支援多個裝置身分識別的通訊協定。

* **低資源裝置**。 MQTT 和 HTTPS 程式庫的磁碟使用量比 AMQP 程式庫小。 因此，如果裝置的資源有限 (例如 RAM 小於 1 MB)，這些通訊協定可能會是唯一可用的通訊協定實作。

* **網路周遊**。 標準的 AMQP 通訊協定會使用連接埠 5671，MQTT 則會接聽連接埠 8883。 使用這些埠可能會導致關閉非 HTTPS 通訊協定的網路發生問題。 在此案例中，請使用「透過 Websocket 的 MQTT」、「透過 Websocket 的 AMQP」或 HTTPS。

* **承載大小**。 MQTT 和 AMQP 是二進位通訊協定，這使得其承載比 HTTPS 更精簡。

> [!WARNING]
> 使用 HTTPS 時，每個裝置應該每隔25分鐘輪詢一次雲端到裝置訊息。 在開發期間，每個裝置可以視需要更頻繁地進行輪詢。

## <a name="port-numbers"></a>連接埠號碼

裝置可以在 Azure 中使用各種通訊協定來與 IoT 中樞通訊。 一般而言，選擇的通訊協定是根據方案的特定需求而定。 下表列出必須要為裝置開啟的輸出連接埠，以使用特定的通訊協定：

| 通訊協定 | Port |
| --- | --- |
| MQTT |8883 |
| 透過 WebSocket 的 MQTT |443 |
| AMQP |5671 |
| 透過 WebSocket 的 AMQP |443 |
| HTTPS |443 |

當您在 Azure 區域中建立 IoT 中樞後，IoT 中樞將在該 IoT 中樞的存留期間保留相同的 IP 位址。 不過，如果 Microsoft 將 IoT 中樞移至不同的縮放單位以維護服務品質，則它會獲派新的 IP 位址。

## <a name="next-steps"></a>後續步驟

若要深入了解 IoT 中樞如何實作 MQTT 通訊協定，請參閱[使用 MQTT 通訊協定來與 IoT 中樞通訊](iot-hub-mqtt-support.md)。
