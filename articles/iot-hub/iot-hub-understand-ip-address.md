---
title: 瞭解 IoT 中樞的 IP 位址 |Microsoft Docs
description: 瞭解如何查詢您的 IoT 中樞 IP 位址及其屬性。 IoT 中樞的 IP 位址可能會在特定情況下變更，例如嚴重損壞修復或區域性容錯移轉。
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: c5040721705b90a981f1f8a45a3a2eb70eefde05
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772155"
---
# <a name="iot-hub-ip-addresses"></a>IoT 中樞 IP 位址

IoT 中樞公用端點的 IP 位址首碼會定期發佈在_AzureIoTHub_ [服務](../virtual-network/service-tags-overview.md)標籤底下。 您可以使用這些 IP 位址首碼來控制 IoT 中樞與您的裝置或網路資產之間的連線，以執行各種不同的網路隔離目標：

| 目標 | 適用案例 | 方法 |
|------|-----------|----------|
| 確保您的裝置和服務只能與 IoT 中樞端點通訊 | [裝置到雲端](./iot-hub-devguide-messaging.md)和[雲端到裝置](./iot-hub-devguide-messages-c2d.md)的訊息、[直接方法](./iot-hub-devguide-direct-methods.md)、[裝置和模組 twins](./iot-hub-devguide-device-twins.md)和[裝置串流](./iot-hub-device-streams-overview.md) | 請使用_AzureIoTHub_和_EventHub_服務標籤來探索 IOT 中樞和事件中樞 ip 位址首碼，並據此設定這些 ip 位址首碼的裝置和服務防火牆設定的允許規則;將流量丟棄至您不想讓裝置或服務與之通訊的其他目的地 IP 位址。 |
| 確保您的 IoT 中樞裝置端點只會從您的裝置和網路資產接收連線 | [裝置到雲端](./iot-hub-devguide-messaging.md)和[雲端到裝置](./iot-hub-devguide-messages-c2d.md)的訊息、[直接方法](./iot-hub-devguide-direct-methods.md)、[裝置和模組 twins](./iot-hub-devguide-device-twins.md)和[裝置串流](./iot-hub-device-streams-overview.md) | 使用 IoT 中樞[IP 篩選器功能](iot-hub-ip-filtering.md)，以允許來自您的裝置和網路資產 IP 位址的連線（請參閱[限制](#limitations-and-workarounds)一節）。 | 
| 確定您的路由的自訂端點資源（儲存體帳戶、服務匯流排和事件中樞）只能從您的網路資產連線 | [訊息路由](./iot-hub-devguide-messages-d2c.md) | 請遵循您的資源指引來限制連線能力（例如透過[防火牆規則](../storage/common/storage-network-security.md)、[私人連結](../private-link/private-endpoint-overview.md)或[服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)）;使用_AzureIoTHub_服務標籤來探索 IoT 中樞 ip 位址首碼，並在資源的防火牆設定中新增這些 ip 首碼的允許規則（請參閱[限制](#limitations-and-workarounds)一節）。 |



## <a name="best-practices"></a>最佳做法

* 在裝置的防火牆設定中新增允許規則時，最好提供[適用的通訊協定所使用的特定埠](./iot-hub-devguide-protocols.md#port-numbers)。

* IoT 中樞的 IP 位址首碼可能會變更。 這些變更會在生效之前透過服務標籤定期發佈。 因此，請務必開發處理常式，以定期取得並使用最新的服務標記。 此程式可透過[服務標記探索 API](../virtual-network/service-tags-overview.md#service-tags-on-premises)來自動化。 請注意，服務標籤探索 API 仍處於預覽階段，而且在某些情況下，可能不會產生標記和 IP 位址的完整清單。 在探索 API 正式推出之前，請考慮使用[可下載 JSON 格式的服務標記](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)。 

* 使用*AzureIoTHub。 [區功能變數名稱稱]* 標記，用以識別特定區域中的 IoT 中樞端點所使用的 IP 首碼。 若要考慮資料中心的嚴重損壞修復，或[區域性容錯移轉](iot-hub-ha-dr.md)，請同時啟用您 IoT 中樞地理配對區域的 IP 首碼連線能力。

* 在 IoT 中樞中設定防火牆規則，可能會封鎖對您的 IoT 中樞執行 Azure CLI 和 PowerShell 命令所需的連線能力。 若要避免這種情況，您可以為用戶端的 IP 位址首碼新增允許規則，以重新啟用 CLI 或 PowerShell 用戶端來與您的 IoT 中樞進行通訊。  


## <a name="limitations-and-workarounds"></a>限制和因應措施

* IoT 中樞 IP 篩選器功能的限制為10個規則。 此限制可透過 Azure 客戶支援的要求來引發。 

* 您設定的[ip 篩選規則](iot-hub-ip-filtering.md)只會套用到您的 IoT 中樞 ip 端點，而不會套用在 IoT 中樞的內建事件中樞端點上。 如果您也需要在您的訊息儲存所在的事件中樞上套用 IP 篩選，您可以帶入自己的事件中樞資源，讓您可以直接設定所需的 IP 篩選規則。 若要這樣做，您必須布建您自己的事件中樞資源，並設定[訊息路由](./iot-hub-devguide-messages-d2c.md)，將您的訊息傳送至該資源，而不是 IoT 中樞的內建事件中樞。 最後，如上表所述，若要啟用訊息路由功能，您也必須允許從 IoT 中樞的 IP 位址首碼連線到已布建的事件中樞資源。

* 路由至儲存體帳戶時，只有當儲存體帳戶位於與您的 IoT 中樞不同的區域時，才可以允許來自 IoT 中樞的 IP 位址首碼的流量。

## <a name="support-for-ipv6"></a>IPv6 的支援 

IoT 中樞目前不支援 IPv6。
