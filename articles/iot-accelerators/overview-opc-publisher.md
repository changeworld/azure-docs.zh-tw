---
title: 什麼是 OPC 發行者 - Azure | Microsoft Docs
description: 此文章提供 OPC 發行者的功能概觀。 它可讓您使用 JSON 承載，將已編碼的 JSON 遙測資料發佈至 Azure IoT 中樞。
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 2310a81d3cfaeff203134af9968bc2d5caea3e9c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "73824786"
---
# <a name="what-is-opc-publisher"></a>什麼是 OPC 發行者？

OPC 發行者是可示範如何執行以下工作的參考實作：

- 連線至現有的 OPC UA 伺服器。
- 使用 JSON 承載，從 OPC UA 伺服器將 JSON 編碼的遙測資料以 OPC UA Pub/Sub 格式發佈至 Azure IoT 中樞。

可以使用 Azure IoT 中樞用戶端 SDK 支援的任何傳輸通訊協定：HTTPS、AMQP 和 MQTT。

此參考實作包含：

- OPC UA 用戶端  ，用以連線至您在網路上現有的 OPC UA 伺服器。
- 連接埠 62222 上的 OPC UA 伺服器  ，可讓您用來管理發佈內容，以及提供可執行相同作業的 IoT 中樞直接方法。

您可以從 GitHub 下載 [OPC 發行者參考實作](https://github.com/Azure/iot-edge-opc-publisher)。

此應用程式會使用 .NET Core 技術進行實作，並可在 .NET Core 支援的任何平台上執行。

OPC 發行者可實作重試邏輯，對未回應保持運作要求達特定次數的端點建立連線。 例如，在 OPC UA 伺服器因電源中斷而停止回應時。

針對 OPC UA 伺服器各個相異的發佈間隔，應用程式會建立個別獨立的訂用帳戶，並在訂用帳戶上更新該發佈間隔的所有節點。

OPC 發行者支援將資料批次傳送至 IoT 中樞，以減少網路負載。 只有在達到設定的封包大小時，此批次才會將封包傳送至 IoT 中樞。

此應用程式使用 OPC Foundation 的 OPC UA 參考堆疊作為 NuGet 套件。 請參閱 [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) 提供的授權條款。

### <a name="next-steps"></a>後續步驟

現在您已了解什麼是 OPC 發行者，建議的下一個步驟是了解如何[設定 OPC 發行者](howto-opc-publisher-configure.md)。
