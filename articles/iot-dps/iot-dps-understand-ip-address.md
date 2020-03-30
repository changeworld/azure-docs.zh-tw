---
title: 瞭解 IoT 設備佈建服務 （DPS） 實例的 IP 位址 |微軟文檔
description: 瞭解如何查詢 IoT 設備佈建服務 （DPS） 位址及其屬性。 在某些情況下（如災害復原或區域容錯移轉）中，DPS 實例的 IP 位址可能會更改。
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: f6afd5c4cc5aa0215f943979ae91389b39d449f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284924"
---
# <a name="iot-hub-dps-ip-addresses"></a>IoT 中心 DPS IP 位址

IoT 中心設備佈建服務 （DPS） 的公共終結點的 IP 位址首碼定期在_AzureIoTHub_ [服務標記](../virtual-network/service-tags-overview.md)下發布。 您可以使用這些 IP 位址首碼來控制 IoT DPS 實例與設備或網路資產之間的連接，以實現各種網路隔離目標：

| 目標 | 方法 |
|------|----------|
| 確保您的設備和服務僅與 IoT 中心 DPS 終結點通信 | 使用_AzureIoTHub_服務標記來發現 IoT 中心 DPS 實例。 相應地為這些 IP 位址首碼配置設備和服務的防火牆設置上的 ALLOW 規則。 配置規則以將流量丟棄到不希望設備或服務通信的其他目標 IP 位址。 |
| 確保您的 IoT 中心 DPS 終結點僅接收來自設備和網路資產的連接 | 使用 IoT [DPS IP 篩選器功能](iot-dps-ip-filtering.md)為設備和 DPS 服務 API 創建篩選器規則。 這些篩選器規則可用於僅允許來自設備和網路資產 IP 位址的連接（請參閱[限制](#limitations-and-workarounds)部分）。 | 




## <a name="best-practices"></a>最佳作法

* 在設備的防火牆配置中添加 ALLOW 規則時，最好提供[適用協定使用的特定埠](../iot-hub/iot-hub-devguide-protocols.md#port-numbers)。

* IoT DPS 實例的 IP 位址首碼可能會更改。 這些更改在生效之前通過服務標記定期發佈。 因此，請務必開發進程以定期檢索和使用最新的服務標記。 此過程可以通過[服務標記發現 API](../virtual-network/service-tags-overview.md#service-tags-on-premises)進行自動化。 服務標記發現 API 仍處於預覽狀態，在某些情況下可能無法生成標記和 IP 位址的完整清單。 在發現 API 通常可用之前，請考慮[使用可下載的 JSON 格式的服務標記](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)。 

* 使用*Azure IoTHub。區功能變數名稱稱** 標記，用於標識特定區域中的 DPS 終結點使用的 IP 首碼。 要考慮資料中心災害復原或[區域容錯移轉](../iot-hub/iot-hub-ha-dr.md)，請確保還啟用了與 DPS 實例地理對區域的 IP 首碼的連接。

* 為 DPS 實例設置防火牆規則可能會阻止針對它運行 Azure CLI 和 PowerShell 命令所需的連接。 為了避免這些連接問題，您可以為用戶端的 IP 位址首碼添加 ALLOW 規則，以重新啟用 CLI 或 PowerShell 用戶端以與 DPS 實例通信。  


## <a name="limitations-and-workarounds"></a>限制和因應措施

* DPS IP 篩選器功能的限制為 100 個規則。 此限制可通過 Azure 客戶支援請求提出。 

* 配置的[IP 篩選規則](iot-dps-ip-filtering.md)僅適用于 DPS 終結點，不適用於連結的 IoT 中心終結點。 連結的 IoT 中心的 IP 篩選必須單獨配置。 有關詳細資訊，請參閱[IoT 中心 IP 篩選規則](../iot-hub/iot-hub-ip-filtering.md)。

## <a name="support-for-ipv6"></a>IPv6 的支援 

IPv6 目前不支援 IoT 中心或 DPS。

## <a name="next-steps"></a>後續步驟

要瞭解有關使用 DPS 的 IP 位址配置的更多資訊，請參閱：

* [設定 IP 篩選](iot-dps-ip-filtering.md)
