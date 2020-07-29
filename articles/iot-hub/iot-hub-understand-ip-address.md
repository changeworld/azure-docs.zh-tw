---
title: 了解 IoT 中樞的 IP 位址 | Microsoft Docs
description: 了解如何查詢您的 IoT 中樞 IP 位址及其屬性。 IoT 中樞的 IP 位址可能會在特定情況下變更，例如災害復原或區域性容錯移轉。
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 1bfae8c7afbfdc6e73dd8bb17b94e6543361e9ce
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848223"
---
# <a name="iot-hub-ip-addresses"></a>IoT 中樞 IP 位址

IoT 中樞公用端點的 IP 位址首碼會定期發佈在 _AzureIoTHub_ [服務標籤](../virtual-network/service-tags-overview.md)之下。

> [!NOTE]
> 對於部署在內部部署網路內的裝置，Azure IoT 中樞支持與私人端點的 VNET 連線能力整合。 如需詳細資訊，請參閱[適用於 VNet 的 IoT 中樞支援](./virtual-network-support.md)。


您可以使用這些 IP 位址首碼來控制 IoT 中樞與您的裝置或網路資產之間的連線能力，以實作各種不同的網路隔離目標：

| 目標 | 適用案例 | 方法 |
|------|-----------|----------|
| 確保您的裝置和服務只能與 IoT 中樞端點通訊 | [裝置到雲端的](./iot-hub-devguide-messaging.md)和[雲端到裝置的](./iot-hub-devguide-messages-c2d.md)傳訊、[直接方法](./iot-hub-devguide-direct-methods.md)、[設備和模組對應項](./iot-hub-devguide-device-twins.md)和[裝置串流](./iot-hub-device-streams-overview.md) | 使用 _AzureIoTHub_ 和 _EventHub_ 服務標籤來探索 IoT 中樞和事件中樞 IP 位址首碼，並據此在裝置和服務的防火牆設定中為那些 IP 位址首碼設定允許規則；將流量置放到您不希望裝置或服務與之通訊的其他目的地 IP 位址。 |
| 確保您的 IoT 中樞裝置端點只會從您的裝置和網路資產接收連線 | [裝置到雲端的](./iot-hub-devguide-messaging.md)和[雲端到裝置的](./iot-hub-devguide-messages-c2d.md)傳訊、[直接方法](./iot-hub-devguide-direct-methods.md)、[設備和模組對應項](./iot-hub-devguide-device-twins.md)和[裝置串流](./iot-hub-device-streams-overview.md) | 使用 IoT 中樞 [IP 篩選器功能](iot-hub-ip-filtering.md)，以允許來自您的裝置和網路資產 IP 位址的連線 (請參閱[限制](#limitations-and-workarounds)一節)。 | 
| 確保路由的自訂端點資源 (儲存體帳戶、服務匯流排和事件中樞) 僅可從您的網路資產存取 | [訊息路由](./iot-hub-devguide-messages-d2c.md) | 遵循您的資源指導方針來限制連線能力 (例如，透過[防火牆規則](../storage/common/storage-network-security.md)、[私人連結](../private-link/private-endpoint-overview.md)，或[服務端點](../virtual-network/virtual-network-service-endpoints-overview.md))；使用 _AzureIoTHub_ 服務標籤來探索 IoT 中樞的 IP 位址首碼，以及在資源的防火牆設定上新增那些 IP 首碼的允許規則 (請參閱[限制](#limitations-and-workarounds)一節)。 |



## <a name="best-practices"></a>最佳作法

* 在裝置的防火牆設定中新增允許規則時，最好提供[適用的通訊協定所使用的特定連接埠](./iot-hub-devguide-protocols.md#port-numbers)。

* IoT 中樞的 IP 位址首碼可能會變更。 這些變更會在生效之前透過服務標籤定期發佈。 因此，請務必開發處理序，以定期取得並使用最新的服務標籤。 此處理序可透過[服務標記探索 API](../virtual-network/service-tags-overview.md#service-tags-on-premises) 進行自動化。 請注意，服務標籤探索 API 仍處於預覽階段，而且在某些情況下，可能不會產生標記和 IP 位址的完整清單。 在探索 API 正式推出之前，請考慮使用[可下載之 JSON 格式的服務標籤](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)。 

* 使用 *AzureIoTHub.[region name]* 標籤，以識別特定區域中 IoT 中樞端點所使用的 IP 首碼。 若要考慮資料中心災害復原或[區域性容錯移轉](iot-hub-ha-dr.md)，請確保也已啟用 IoT 中樞地理配對區域的 IP 首碼連線能力。

* 在 IoT 中樞中設定防火牆規則，可能會封鎖針對 IoT 中樞執行 Azure CLI 和 PowerShell 命令所需的連線能力。 若要避免這種情況，您可以針對用戶端的 IP 位址首碼新增允許規則，以重新啟用 CLI 或 PowerShell 用戶端來與您的 IoT 中樞進行通訊。  


## <a name="limitations-and-workarounds"></a>限制和因應措施

* IoT 中樞 IP 篩選器功能的限制為 10 個規則。 此限制可透過 Azure 客戶支援的要求來提高。 

* 您設定的 [IP 篩選規則](iot-hub-ip-filtering.md)僅適用於 IoT 中樞 IP 端點，而不適用於 IoT 中樞的內建事件中樞端點。 如果您也需要在儲存訊息的事件中樞上套用 IP 篩選，則可以帶入自己的事件中樞資源，在其中可以直接設定所需的 IP 篩選規則。 若要這樣做，您必須佈建您自己的事件中樞資源，並設定[訊息路由](./iot-hub-devguide-messages-d2c.md)，將您的訊息傳送至該資源，而不是 IoT 中樞的內建事件中樞。 最後，如上表所述，若要啟用訊息路由功能，您也必須允許從 IoT 中樞的 IP 位址首碼連線到已佈建的事件中樞資源。

* 路由至儲存體帳戶時，只有當儲存體帳戶與 IoT 中樞位於不同的區域時，才允許來自 IoT 中樞之 IP 位址首碼的流量。

## <a name="support-for-ipv6"></a>支援 IPv6 

IoT 中樞目前不支援 IPv6。
