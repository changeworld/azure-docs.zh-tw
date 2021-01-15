---
title: IP 位址 168.63.129.16 是什麼？ | Microsoft Docs
description: 瞭解 IP 位址168.63.129.16，具體來說，它是用來加速通道與 Azure 平臺資源之間的通訊。
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: v-jesits
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/15/2019
ms.author: genli
ms.openlocfilehash: 1e304bc30a48c92fdff576723dae6af1e26ef3da
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222576"
---
# <a name="what-is-ip-address-1686312916"></a>IP 位址 168.63.129.16 是什麼？

IP 位址 168.63.129.16 是虛擬公用 IP 位址，有助於建構 Azure 平台資源的通訊通道。 客戶可以在 Azure 中為其私人虛擬網路定義任何位址空間。 因此，Azure 平台資源必須顯示為唯一的公用 IP 位址。 此虛擬公用 IP 位址有助於達成下列事項：

- 使 VM 代理程式能夠與平台通訊，藉以表示它處於「就緒」狀態。
- 啟用與 DNS 虛擬伺服器的通訊，以提供篩選後的名稱解析給沒有自訂 DNS 伺服器的資源 (例如 VM)。 此篩選可確保客戶只能解析其資源的主機名稱。
- [從 Azure 負載平衡器啟用健康情況探查](../load-balancer/load-balancer-custom-probe-overview.md)，以判斷 vm 的健康情況狀態。
- 允許 VM 從 Azure 中的 DHCP 服務取得動態 IP 位址。
- 為 PaaS 角色啟用客體代理程式活動訊號訊息。

> [!NOTE]
> 在非虛擬網路案例 (傳統) 中，會使用私人 IP 位址而不是168.63.129.16。 透過 DHCP 可動態探索此私人 IP 位址。 168.63.129.16 特定的防火牆規則必須適當地調整。

## <a name="scope-of-ip-address-1686312916"></a>IP 位址 168.63.129.16 的範圍

公用 IP 位址168.63.129.16 用於所有區域和所有國家雲端。 這個特殊的公用 IP 位址是由 Microsoft 所擁有，不會變更。 建議您在 VM) 防火牆原則的任何本機 (中允許此 IP 位址 (輸出方向) 。 此特殊 IP 位址與資源之間的通訊是安全的，因為只有內部 Azure 平台才可獲得來自此 IP 位址的訊息。 如果此位址遭到封鎖，可能在各種情況下發生非預期的行為。 168.63.129.16 是 [主機節點的虛擬 IP](./network-security-groups-overview.md#azure-platform-considerations) ，因此不會受限於使用者定義的路由。

- VM 代理程式需要透過埠 80/tcp 和 32526/tcp 進行輸出通訊，且 WireServer (168.63.129.16) 。 這些應該在 VM 上的本機防火牆中開啟。 這些埠上與168.63.129.16 的通訊不受限於設定的網路安全性群組。

- 168.63.129.16 可提供 DNS 服務給 VM。 如果不想要，168.63.129.16 埠 53/udp 和 53/tcp 的輸出流量可以在 VM 上的本機防火牆中封鎖。

  根據預設，DNS 通訊不受限於設定的網路安全性群組，除非特別以 [AzurePlatformDNS](../virtual-network/service-tags-overview.md#available-service-tags) 服務標籤為目標。 若要封鎖透過 NSG 的 DNS 流量 Azure DNS，請建立輸出規則以拒絕 [AzurePlatformDNS](../virtual-network/service-tags-overview.md#available-service-tags)的流量，並將 "*" 指定為「目的地埠範圍」和「任何」作為通訊協定。

- 當 VM 屬於負載平衡器後端集區的一部分時，應允許 [健康情況探查](../load-balancer/load-balancer-custom-probe-overview.md) 通訊源自于168.63.129.16。 預設的網路安全性群組設定具有允許此通訊的規則。 此規則會利用 [AzureLoadBalancer](../virtual-network/service-tags-overview.md#available-service-tags) 服務標記。 若有需要，您可以設定網路安全性群組來封鎖此流量，但這會導致探查失敗。

## <a name="next-steps"></a>後續步驟

- [安全性群組](./network-security-groups-overview.md)
- [建立、變更或刪除網路安全性群組](manage-network-security-group.md)