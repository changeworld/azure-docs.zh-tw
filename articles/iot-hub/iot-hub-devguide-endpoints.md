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
ms.openlocfilehash: da6d17e42407048b7ecbcacade67ef48046d7fe1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284638"
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

* **裝置身分識別管理**。 每個 IoT 中樞都會公開一組 HTTPS REST 端點，用來管理裝置身分識別 (建立、擷取、更新和刪除)。 [設備標識](iot-hub-devguide-identity-registry.md)用於設備身份驗證和存取控制。

* **裝置對應項管理**。 每個 IoT 中樞都會公開一組面向服務的 HTTPS REST 端點，用來查詢和更新[裝置對應項](iot-hub-devguide-device-twins.md) (更新標籤和屬性)。

* **作業管理**。 每個 IoT 中樞都會公開一組面向服務的 HTTPS REST 端點，用來查詢和管理[作業](iot-hub-devguide-jobs.md)。

* **裝置端點**。 針對身分識別登錄中的每個裝置，IoT 中樞會公開一組端點：

  * *發送設備到雲的消息*。 裝置會使用這個端點來[傳送裝置到雲端的訊息](iot-hub-devguide-messages-d2c.md)。

  * 接收雲端到裝置的訊息**。 設備使用此終結點接收目標[雲到設備消息](iot-hub-devguide-messages-c2d.md)。

  * *起始檔案上傳*。 設備使用此終結點從 IoT 中心接收 Azure 存儲 SAS URI[來上載檔](iot-hub-devguide-file-upload.md)。

  * *擷取和更新裝置對應項的屬性*。 裝置使用此端點來存取其[裝置對應項](iot-hub-devguide-device-twins.md)的屬性。

  * *接收直接方法要求*。 裝置使用此端點來接聽[直接方法](iot-hub-devguide-direct-methods.md)的要求。

    公開這些端點時，是使用 [MQTT v3.1.1](https://mqtt.org/)、HTTPS 1.1 及 [AMQP 1.0](https://www.amqp.org/) 通訊協定來公開。 您也可以透過連接埠 443 上的 [WebSockets](https://tools.ietf.org/html/rfc6455) 取得 AMQP。

* **服務終結點**。 每個 IoT 中樞都會公開一組端點，供您的解決方案後端用來與您的裝置通訊。 但有一個例外狀況，這些端點只會在使用 [AMQP](https://www.amqp.org/) 通訊協定時公開。 方法引動過程端點會透過 HTTPS 通訊協定來公開。
  
  * *接收設備到雲的消息*。 此端點與 [Azure 事件中樞](https://azure.microsoft.com/documentation/services/event-hubs/)相容。 後端服務可用它來讀取由您的裝置所傳送的[裝置到雲端訊息](iot-hub-devguide-messages-d2c.md)。 除了這個內建端點外，您可以在 IoT 中樞上建立自訂端點。
  
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

您可以使用 REST API[獲取終結點運行狀況](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)來獲取終結點的運行狀況。 我們建議使用與路由消息延遲相關的[IoT 中心指標](iot-hub-metrics.md)來識別和調試終結點運行狀況死或不正常的錯誤，因為我們預計當終結點處於這些狀態之一時，延遲會更高。

|健康情況狀態|描述|
|---|---|
|healthy|終結點按預期接受消息。|
|不良|終結點未按預期接受消息，IoT 中心正在重試將資料發送到此終結點。 當 IoT 中心建立了最終一致的運行狀況時，不正常終結點的狀態將更新為正常。|
|未知|IoT 中心尚未與終結點建立連接。 未向此終結點傳遞或拒絕任何消息。|
|死|在 IoT 中心重試為重審期間發送消息後，終結點不接受消息。|

## <a name="field-gateways"></a>現場閘道器

在 IoT 解決方案中， *現場閘道*位於裝置和 IoT 中樞端點之間。 它通常位於接近您的裝置的位置。 您的裝置會使用裝置所支援的通訊協定，直接與現場閘道器通訊。 現場閘道會使用 IoT 中樞所支援的通訊協定來連線到 IoT 中樞端點。 現場閘道可能是專用的硬體裝置或是執行自訂閘道軟體的低功率電腦。

您可以使用 [Azure IoT Edge](/azure/iot-edge/) 來實作現場閘道。 IoT Edge 提供某些功能，例如對從多個裝置到相同 IoT 中樞連線的通訊進行多工處理。

## <a name="next-steps"></a>後續步驟

此 IoT 中樞開發人員指南中的其他參考主題包括︰

* [裝置對應項、作業和訊息路由的 IoT 中樞查詢語言](iot-hub-devguide-query-language.md)
* [配額和節流](iot-hub-devguide-quotas-throttling.md)
* [IoT 中樞的 MQTT 支援](iot-hub-mqtt-support.md)
* [瞭解 IoT 中心 IP 位址](iot-hub-understand-ip-address.md)