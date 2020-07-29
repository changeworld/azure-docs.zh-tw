---
title: 瞭解 IoT 裝置布建服務（DPS）實例的 IP 位址 |Microsoft Docs
description: 瞭解如何查詢您的 IoT 裝置布建服務（DPS）位址及其屬性。 您的 DPS 實例的 IP 位址可能會在特定情況下變更，例如嚴重損壞修復或區域性容錯移轉。
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: f6afd5c4cc5aa0215f943979ae91389b39d449f6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "79284924"
---
# <a name="iot-hub-dps-ip-addresses"></a>IoT 中樞 DPS IP 位址

IoT 中樞裝置佈建服務（DPS）公用端點的 IP 位址首碼會定期發佈在_AzureIoTHub_ [服務](../virtual-network/service-tags-overview.md)標籤底下。 您可以使用這些 IP 位址首碼來控制 IoT DPS 實例和裝置或網路資產之間的連線能力，以執行各種網路隔離目標：

| 目標 | 方法 |
|------|----------|
| 確保您的裝置和服務只能與 IoT 中樞 DPS 端點通訊 | 使用_AzureIoTHub_服務標記來探索 IoT 中樞 DPS 實例。 據此設定這些 IP 位址首碼的 [允許裝置上的規則] 和 [服務] 防火牆設定。 設定規則，將流量捨棄至您不想讓裝置或服務與之通訊的其他目的地 IP 位址。 |
| 確定您的 IoT 中樞 DPS 端點只會從您的裝置和網路資產接收連線 | 使用 IoT DPS [IP 篩選器功能](iot-dps-ip-filtering.md)來建立裝置和 DPS 服務 api 的篩選規則。 這些篩選規則只能用來允許來自您裝置和網路資產 IP 位址的連線（請參閱[限制](#limitations-and-workarounds)一節）。 | 




## <a name="best-practices"></a>最佳作法

* 在裝置的防火牆設定中新增允許規則時，最好提供[適用的通訊協定所使用的特定連接埠](../iot-hub/iot-hub-devguide-protocols.md#port-numbers)。

* IoT DPS 實例的 IP 位址首碼可能會變更。 這些變更會在生效之前透過服務標籤定期發佈。 因此，請務必開發處理序，以定期取得並使用最新的服務標籤。 此處理序可透過[服務標記探索 API](../virtual-network/service-tags-overview.md#service-tags-on-premises) 進行自動化。 服務標籤探索 API 仍處於預覽階段，而且在某些情況下，可能不會產生標記和 IP 位址的完整清單。 在探索 API 正式推出之前，請考慮使用[可下載之 JSON 格式的服務標籤](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)。 

* 使用*AzureIoTHub。 [區功能變數名稱稱]* 標記，用以識別特定區域中 DPS 端點所使用的 IP 首碼。 若要考慮資料中心嚴重損壞修復或[區域性容錯移轉](../iot-hub/iot-hub-ha-dr.md)，請確認您的 DPS 實例地理配對區域的 IP 首碼連線也已啟用。

* 設定 DPS 實例的防火牆規則，可能會封鎖對其執行 Azure CLI 和 PowerShell 命令所需的連線能力。 若要避免這些連線問題，您可以為用戶端的 IP 位址首碼新增允許規則，以重新啟用 CLI 或 PowerShell 用戶端以與 DPS 實例進行通訊。  


## <a name="limitations-and-workarounds"></a>限制和因應措施

* DPS IP 篩選器功能的限制為100個規則。 此限制可透過 Azure 客戶支援的要求來提高。 

* 您設定的[IP 篩選規則](iot-dps-ip-filtering.md)只會套用到您的 DPS 端點，而不會套用至連結的 IoT 中樞端點。 連結的 IoT 中樞的 IP 篩選必須分別設定。 如需詳細資訊，請參閱[IOT 中樞 IP 篩選規則](../iot-hub/iot-hub-ip-filtering.md)。

## <a name="support-for-ipv6"></a>支援 IPv6 

IoT 中樞或 DPS 目前不支援 IPv6。

## <a name="next-steps"></a>後續步驟

若要深入瞭解使用 DPS 的 IP 位址設定，請參閱：

* [設定 IP 篩選](iot-dps-ip-filtering.md)
