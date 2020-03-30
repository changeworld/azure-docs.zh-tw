---
title: IP 位址 168.63.129.16 是什麼？ | Microsoft Docs
description: 了解 IP 位址 168.63.129.16 及其與您的資源搭配運作的方式。
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
ms.openlocfilehash: 287f881fb17dd84357f540ee562e21c66c11ab95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77114354"
---
# <a name="what-is-ip-address-1686312916"></a>IP 位址 168.63.129.16 是什麼？

IP 位址 168.63.129.16 是虛擬公用 IP 位址，有助於建構 Azure 平台資源的通訊通道。 客戶可以在 Azure 中為其私人虛擬網路定義任何位址空間。 因此，Azure 平台資源必須顯示為唯一的公用 IP 位址。 此虛擬公用 IP 位址有助於達成下列事項：

- 使 VM 代理程式能夠與平台通訊，藉以表示它處於「就緒」狀態。
- 啟用與 DNS 虛擬伺服器的通訊，以提供篩選後的名稱解析給沒有自訂 DNS 伺服器的資源 (例如 VM)。 此篩選可確保客戶只能解析其資源的主機名稱。
- 啟用[Azure 負載等化器的運行狀況探測](../load-balancer/load-balancer-custom-probe-overview.md)以確定 VM 的運行狀況狀態。
- 允許 VM 從 Azure 中的 DHCP 服務取得動態 IP 位址。
- 為 PaaS 角色啟用客體代理程式活動訊號訊息。

## <a name="scope-of-ip-address-1686312916"></a>IP 位址 168.63.129.16 的範圍

公共 IP 位址 168.63.129.16 用於所有區域和所有國家雲。 此特殊的公共 IP 位址歸 Microsoft 所有，不會更改。 我們建議您在任何本地（VM）防火牆策略（出站方向）中允許此 IP 位址。 此特殊 IP 位址與資源之間的通訊是安全的，因為只有內部 Azure 平台才可獲得來自此 IP 位址的訊息。 如果此位址遭到封鎖，可能在各種情況下發生非預期的行為。 168.63.129.16 是[主機節點的虛擬 IP，](../virtual-network/security-overview.md#azure-platform-considerations)因此不受使用者定義的路由的約束。

- VM 代理需要通過通過 WireServer （168.63.129.16） 的埠 80、443、32526 進行出站通信。 這些應在 VM 上的本地防火牆中打開。 這些埠上的通信與 168.63.129.16 不受配置的網路安全性群組的約束。
- 168.63.129.16 可以為 VM 提供 DNS 服務。 如果不需要這樣做，可以在 VM 上的本地防火牆中阻止此流量。 預設情況下，DNS 通信不受配置的網路安全性群組的約束，除非專門針對使用[AzurePlatformDNS](../virtual-network/service-tags-overview.md#available-service-tags)服務標記。
- 當 VM 是負載等化器後端池的一部分時，應允許[運行狀況探測](../load-balancer/load-balancer-custom-probe-overview.md)通信源自 168.63.129.16。 預設網路安全性群組配置具有允許此通信的規則。 此規則利用[Azure Load 平衡器](../virtual-network/service-tags-overview.md#available-service-tags)服務標記。 如果需要，可以通過配置網路安全性群組來阻止此流量，但這將導致探測器失敗。

在非虛擬網路方案（經典版）中，運行狀況探測來自專用 IP，不使用 168.63.129.16。

## <a name="next-steps"></a>後續步驟

- [安全性群組](security-overview.md)
- [建立、變更或刪除網路安全性群組](manage-network-security-group.md)
