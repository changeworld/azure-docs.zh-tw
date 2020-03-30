---
title: 瞭解 IoT 中心 IP 位址 |微軟文檔
description: 瞭解如何查詢 IoT 中心 IP 位址及其屬性。 在某些情況下（如災害復原或區域容錯移轉）中，IoT 中心的 IP 位址可能會更改。
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: c609f2a3843481442e97061739a806de60a680b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367562"
---
# <a name="iot-hub-ip-addresses"></a>IoT 中心 IP 位址

IoT 中心公共終結點的 IP 位址首碼定期在_Azure IoTHub_ [服務標記](../virtual-network/service-tags-overview.md)下發布。

> [!NOTE]
> 對於部署在本地網路內的設備，Azure IoT 中心支援 VNET 連接集成與專用終結點。 有關詳細資訊，請參閱[VNET 的 IoT 中心支援](./virtual-network-support.md#ingress-connectivity-to-iot-hub-using-private-endpoints)。


您可以使用這些 IP 位址首碼來控制 IoT 中心與您的設備或網路資產之間的連接，以實現各種網路隔離目標：

| 目標 | 適用的案例 | 方法 |
|------|-----------|----------|
| 確保您的設備和服務僅與 IoT 中心終結點通信 | [設備到雲](./iot-hub-devguide-messaging.md)，[雲到設備](./iot-hub-devguide-messages-c2d.md)消息傳送，[直接方法](./iot-hub-devguide-direct-methods.md)，[設備和模組孿生](./iot-hub-devguide-device-twins.md)和[設備流](./iot-hub-device-streams-overview.md) | 使用_AzureIoTHub_和_EventHub_服務標記來發現 IoT 中心，以及事件中心 IP 位址首碼，並相應地在設備和服務的防火牆設置上配置 ALLOW 規則;將流量丟棄到不希望設備或服務通信的其他目標 IP 位址。 |
| 確保 IoT 中心設備終結點僅接收來自設備和網路資產的連接 | [設備到雲](./iot-hub-devguide-messaging.md)，[雲到設備](./iot-hub-devguide-messages-c2d.md)消息傳送，[直接方法](./iot-hub-devguide-direct-methods.md)，[設備和模組孿生](./iot-hub-devguide-device-twins.md)和[設備流](./iot-hub-device-streams-overview.md) | 使用 IoT 集線器[IP 篩選器功能](iot-hub-ip-filtering.md)允許來自設備和網路資產 IP 位址的連接（請參閱[限制](#limitations-and-workarounds)部分）。 | 
| 確保路由的自訂終結點資源（存儲帳戶、服務匯流排和事件中心）僅從網路資產中可到達 | [訊息路由](./iot-hub-devguide-messages-d2c.md) | 按照資源關於限制連接的指導（例如通過[防火牆規則](../storage/common/storage-network-security.md)、[專用連結](../private-link/private-endpoint-overview.md)或服務[終結點](../virtual-network/virtual-network-service-endpoints-overview.md)）;使用_AzureIoTHub_服務標記來發現 IoT 中心 IP 位址首碼，並為資源的防火牆配置中的這些 IP 首碼添加 ALLOW 規則（請參閱[限制](#limitations-and-workarounds)部分）。 |



## <a name="best-practices"></a>最佳作法

* 在設備的防火牆配置中添加 ALLOW 規則時，最好提供[適用協定使用的特定埠](./iot-hub-devguide-protocols.md#port-numbers)。

* IoT 中心的 IP 位址首碼可能會更改。 這些更改在生效之前通過服務標記定期發佈。 因此，請務必開發進程以定期檢索和使用最新的服務標記。 此過程可以通過[服務標記發現 API](../virtual-network/service-tags-overview.md#service-tags-on-premises)進行自動化。 請注意，服務標記發現 API 仍處於預覽狀態，在某些情況下可能無法生成標記和 IP 位址的完整清單。 在發現 API 通常可用之前，請考慮[使用可下載的 JSON 格式的服務標記](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)。 

* 使用*Azure IoTHub。區功能變數名稱稱** 標記，用於標識 IoT 中心終結點在特定區域中使用的 IP 首碼。 要考慮資料中心災害復原或[區域容錯移轉](iot-hub-ha-dr.md)，還可確保啟用與 IoT 中心地理對區域的 IP 首碼的連接。

* 在 IoT 中心中設置防火牆規則可能會阻止針對 IoT 中心運行 Azure CLI 和 PowerShell 命令所需的連接。 為了避免這種情況，您可以為用戶端的 IP 位址首碼添加 ALLOW 規則，以重新啟用 CLI 或 PowerShell 用戶端以與 IoT 中心通信。  


## <a name="limitations-and-workarounds"></a>限制和因應措施

* IoT 中心 IP 篩選器功能有 10 個規則的限制。 此限制可通過 Azure 客戶支援請求提出。 

* 配置的[IP 篩選規則](iot-hub-ip-filtering.md)僅適用于 IoT 中心 IP 終結點，而不是在 IoT 中心內置事件中心終結點上應用。 如果還需要在存儲郵件的事件中心上應用 IP 篩選，則可以這樣做，從而將您自己的事件中心資源直接配置所需的 IP 篩選規則。 為此，您需要預配自己的事件中心資源並設置[消息路由](./iot-hub-devguide-messages-d2c.md)，以將消息發送到該資源，而不是 IoT 中心的內置事件中心。 最後，如上表所述，為了啟用消息路由功能，您還需要允許從 IoT 中心 IP 位址首碼連接到預配的事件中心資源。

* 路由到存儲帳戶時，僅當存儲帳戶位於 IoT 中心位於其他區域時，才允許來自 IoT 中心 IP 位址首碼的流量。

## <a name="support-for-ipv6"></a>IPv6 的支援 

IoT 中心當前不支援 IPv6。
