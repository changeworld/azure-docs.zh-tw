---
title: 了解 Azure IoT 中樞端點 | Microsoft Docs
description: 開發人員指南 - IoT 中樞裝置對向端點和服務對向端點的相關參考資訊。
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/10/2019
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: System Architecture'
ms.openlocfilehash: 9ae21b2a6d470b959f5e869e8a9edda9c56bec73
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2020
ms.locfileid: "90019558"
---
# <a name="reference---iot-hub-endpoints"></a>參考 - IoT 中樞端點

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>IoT 中樞名稱

您可以在入口網站中，於 IoT 中樞的 [概觀]**** 頁面上找到端點裝載所在的 IoT 中樞主機名稱。 根據預設，IoT 中樞的 DNS 名稱看起來像：`{your iot hub name}.azure-devices.net`。

## <a name="list-of-built-in-iot-hub-endpoints"></a>內建 IoT 中樞端點清單

Azure IoT 中樞是一項多租用戶服務，可將其功能公開給各種動作項目。 下圖顯示 IoT 中樞公開的各種端點。

![IoT 中樞端點](./media/iot-hub-devguide-endpoints/endpoints.png)

下列清單說明這些端點：

* **資源提供者**。 IoT 中樞資源提供者會公開 [Azure Resource Manager](../azure-resource-manager/management/overview.md) 介面。 此介面可讓 Azure 訂用帳戶擁有者建立和刪除 IoT 中樞，以及更新 IoT 中樞屬性。 IoT 中樞屬性用來管理[中樞層級的安全性原則](iot-hub-devguide-security.md#access-control-and-permissions) (相對於裝置層級的存取控制) 和雲端到裝置與裝置到雲端傳訊的功能選項。 IoT 中樞資源提供者也可讓您[匯出裝置身分識別](iot-hub-devguide-identity-registry.md#import-and-export-device-identities)。

* **裝置身分識別管理**。 每個 IoT 中樞都會公開一組 HTTPS REST 端點，用來管理裝置身分識別 (建立、擷取、更新和刪除)。 [裝置](iot-hub-devguide-identity-registry.md) 身分識別用於裝置驗證和存取控制。

* **裝置對應項管理**。 每個 IoT 中樞都會公開一組面向服務的 HTTPS REST 端點，用來查詢和更新[裝置對應項](iot-hub-devguide-device-twins.md) (更新標籤和屬性)。 

* **作業管理**。 每個 IoT 中樞都會公開一組面向服務的 HTTPS REST 端點，用來查詢和管理[作業](iot-hub-devguide-jobs.md)。

* **裝置端點**。 針對身分識別登錄中的每個裝置，IoT 中樞會公開一組端點。 除非有注明，否則這些端點會使用 [MQTT v 3.1.1](https://mqtt.org/)、HTTPS 1.1 和 [AMQP 1.0](https://www.amqp.org/) 通訊協定來公開。 AMQP 和 MQTT 也適用于埠443上的 [websocket](https://tools.ietf.org/html/rfc6455) 。

  * *傳送裝置到雲端的訊息*。 裝置會使用這個端點來[傳送裝置到雲端的訊息](iot-hub-devguide-messages-d2c.md)。

  * *接收雲端到裝置的訊息*。 裝置使用此端點來接收目標的 [雲端到裝置訊息](iot-hub-devguide-messages-c2d.md)。

  * *起始檔案上傳*。 裝置使用此端點來接收來自 IoT 中樞的 Azure 儲存體 SAS URI，以 [上傳](iot-hub-devguide-file-upload.md)檔案。

  * *擷取和更新裝置對應項的屬性*。 裝置使用此端點來存取其[裝置對應項](iot-hub-devguide-device-twins.md)的屬性。 不支援 HTTPS。

  * *接收直接方法要求*。 裝置使用此端點來接聽[直接方法](iot-hub-devguide-direct-methods.md)的要求。 不支援 HTTPS。

  [!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

* **服務端點**。 每個 IoT 中樞都會公開一組端點，供您的解決方案後端用來與您的裝置通訊。 有一個例外狀況，這些端點只會使用 [AMQP](https://www.amqp.org/) 和 AMQP over websocket 通訊協定來公開。 直接方法調用端點會透過 HTTPS 通訊協定來公開。
  
  * *接收裝置到雲端的訊息*。 此端點與 [Azure 事件中樞](https://azure.microsoft.com/documentation/services/event-hubs/)相容。 後端服務可用它來讀取由您的裝置所傳送的[裝置到雲端訊息](iot-hub-devguide-messages-d2c.md)。 除了這個內建端點外，您可以在 IoT 中樞上建立自訂端點。
  
  * *傳送雲端到裝置的訊息及接收傳遞通知*。 這些端點可讓您的解決方案後端傳送可靠的[雲端到裝置訊息](iot-hub-devguide-messages-c2d.md)，以及接收相對應的傳遞或到期通知。
  
  * *接收檔案通知*。 此訊息的端點可讓您接收您的裝置已成功上傳檔案的通知。 
  
  * *直接方法叫用*。 此端點可讓後端服務在裝置上叫用[直接方法](iot-hub-devguide-direct-methods.md)。
  
  * *接收作業監視事件*。 如果已設定 IoT 中樞要發出接收作業監視事件，此端點可讓您這些事件。 如需詳細資訊，請參閱 [IoT 中樞作業監視](iot-hub-operations-monitoring.md)。

[Azure IoT SDK](iot-hub-devguide-sdks.md) 一文說明您可用來存取這些端點的各種方式。

所有的 IoT 中樞端點都使用 [TLS](https://tools.ietf.org/html/rfc5246) 通訊協定，且絕不會在未加密/不安全的通道上公開任何端點。

## <a name="custom-endpoints"></a>自訂端點

您可以將訂用帳戶中的現有 Azure 服務連結至 IoT 中樞，以便做為訊息路由傳送的端點。 這些端點可做為服務端點，並當作訊息路由的接收器。 裝置無法直接寫入至其他端點。 深入了解[訊息路由](../iot-hub/iot-hub-devguide-messages-d2c.md)。

IoT 中樞目前支援下列 Azure 服務做為額外的端點︰

* Azure 儲存體容器
* 事件中樞
* 服務匯流排佇列
* 服務匯流排主題

如需您可以新增的端點數目限制，請參閱[配額和節流](iot-hub-devguide-quotas-throttling.md)。

## <a name="endpoint-health"></a>端點健全狀況

[!INCLUDE [iot-hub-endpoint-health](../../includes/iot-hub-include-endpoint-health.md)]

## <a name="field-gateways"></a>現場閘道器

在 IoT 解決方案中， *現場閘道*位於裝置和 IoT 中樞端點之間。 它通常位於接近您的裝置的位置。 您的裝置會使用裝置所支援的通訊協定，直接與現場閘道器通訊。 現場閘道會使用 IoT 中樞所支援的通訊協定來連線到 IoT 中樞端點。 現場閘道可能是專用的硬體裝置或是執行自訂閘道軟體的低功率電腦。

您可以使用 [Azure IoT Edge](/azure/iot-edge/) 來實作現場閘道。 IoT Edge 提供某些功能，例如對從多個裝置到相同 IoT 中樞連線的通訊進行多工處理。

## <a name="next-steps"></a>接下來的步驟

此 IoT 中樞開發人員指南中的其他參考主題包括︰

* [裝置對應項、作業和訊息路由的 IoT 中樞查詢語言](iot-hub-devguide-query-language.md)
* [配額和節流](iot-hub-devguide-quotas-throttling.md)
* [IoT 中樞的 MQTT 支援](iot-hub-mqtt-support.md)
* [瞭解您的 IoT 中樞 IP 位址](iot-hub-understand-ip-address.md)