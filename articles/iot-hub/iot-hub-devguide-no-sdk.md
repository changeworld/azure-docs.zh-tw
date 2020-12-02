---
title: 不使用 Azure IoT SDK 進行開發 |Microsoft Docs
description: 開發人員指南-您可以用來建立裝置應用程式和後端應用程式，而不需使用 Azure IoT SDK 的相關資訊和連結。
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2020
ms.custom:
- mqtt
- amqp
- 'Role: IoT Device'
- 'Role: Cloud Development'
ms.openlocfilehash: ae8b01522a248b8b1dbdd255a9fcd55f16cf2369
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461715"
---
# <a name="develop-without-using-an-azure-iot-hub-sdk"></a>不使用 Azure IoT 中樞 SDK 進行開發

本主題提供實用的資訊和連結，讓開發人員不需使用 Azure IoT Sdk 即可開發裝置或後端應用程式。

Microsoft 強烈建議使用 Azure IoT SDK。 Azure IoT 裝置和服務 Sdk 會在許多常用的平臺上發佈。 Sdk 提供便利的層級，可處理基礎通訊協定的大部分複雜性，包括裝置連線和重新連線，以及重試原則。 Sdk 會定期更新，以提供 IoT 中樞所公開的最新功能，以及安全性更新。 使用 Sdk 可協助您減少開發程式碼維護的時間和時間。 若要深入瞭解 Azure IoT Sdk，請參閱 [Azure Iot 裝置和服務 sdk](iot-hub-devguide-sdks.md)。 如需有關使用 Azure IoT SDK 的優點的詳細資訊，請參閱 [使用 Azure Iot sdk 和陷阱的優點，以避免您](https://azure.microsoft.com/en-us/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) 的 blog 張貼。

雖然 IoT 中樞支援 AMQP、透過 Websocket 的 AMQP、HTTPS、MQTT 和透過 Websocket 的 MQTT 來與裝置通訊，但如果您的裝置支援，我們建議使用 MQTT。

## <a name="development-prerequisites"></a>開發必要條件

開始開發之前，您應該對 IoT 中樞和您想要讓裝置或後端應用程式執行的功能具有徹底的瞭解。 以下是一份非常簡短的主題清單，您應該熟悉：

* 請確定您瞭解 IoT 中樞所公開的端點，以及每個端點所支援的通訊協定。 若要深入瞭解，請參閱 [IoT 中樞端點](iot-hub-devguide-endpoints.md)。

* 針對裝置應用程式所需的通訊協定選擇，我們強烈建議使用 MQTT。 不過，在選擇通訊協定之前，請確定您瞭解每個通訊協定所加諸的限制。 若要深入瞭解，請參閱 [選擇通訊協定](iot-hub-devguide-protocols.md)。

* 若要瞭解 IoT 中樞的驗證，請參閱 [控制 Iot 中樞的存取權](iot-hub-devguide-security.md)。

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="help-on-different-protocols"></a>有關不同通訊協定的說明

如需在沒有 Azure IoT SDK 的情況下使用下列通訊協定的協助：

* **AMQP** 上的裝置或後端應用程式，請參閱 [AMQP 支援](iot-hub-amqp-support.md)。

* **MQTT** 上的裝置應用程式，請參閱 [MQTT 支援](iot-hub-mqtt-support.md)。 本主題大部分會直接使用 MQTT 通訊協定。 它也包含有關使用 [IOT MQTT 範例存放庫](https://github.com/Azure-Samples/IoTMQTTSample)的資訊。 此存放庫包含使用 Eclipse Mosquitto 程式庫將訊息傳送至 IoT 中樞的 C 範例。

* 裝置或 **HTTPS** 上的後端應用程式，請參閱 [Azure IoT 中樞 REST api](/rest/api/iothub/)。 請注意，如 [開發必要條件](#development-prerequisites)中所述，您無法使用 x.509 憑證授權單位單位 (CA) 以 HTTPS 進行驗證。

針對裝置，如果您的裝置支援，強烈建議使用 MQTT。

## <a name="next-steps"></a>後續步驟

* [MQTT 支援](iot-hub-mqtt-support.md)