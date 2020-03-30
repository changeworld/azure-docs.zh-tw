---
title: 使用專用終結點
titleSuffix: Azure Storage
description: 用於從虛擬網路安全訪問存儲帳戶的專用終結點概述。
services: storage
author: santoshc
ms.service: storage
ms.topic: article
ms.date: 03/12/2020
ms.author: santoshc
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: c51f2db698f30368c9d4090d3d571fa0c131178a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299051"
---
# <a name="use-private-endpoints-for-azure-storage"></a>使用專用終結點進行 Azure 存儲

可以使用 Azure 存儲帳戶的[專用終結點](../../private-link/private-endpoint-overview.md)，允許虛擬網路 （VNet） 上的用戶端通過[專用鏈路](../../private-link/private-link-overview.md)安全地訪問資料。 專用終結點使用來自 VNet 位址空間的 IP 位址來存儲帳戶服務。 VNet 上的用戶端和存儲帳戶之間的網路流量通過 VNet 和 Microsoft 骨幹網路上的專用鏈路遍歷，從而消除了公共 Internet 的暴露。

為存儲帳戶使用專用終結點使您能夠：

- 通過配置存儲防火牆來阻止存儲服務的公共終結點上的所有連接，從而保護存儲帳戶的安全。
- 通過阻止從 VNet 中挖掘資料，提高虛擬網路 （VNet） 的安全性。
- 使用[VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)或[快速路由](../../expressroute/expressroute-locations.md)（使用專用對等）安全地連接到本地網路的存儲帳戶。

## <a name="conceptual-overview"></a>概念概觀

![Azure 存儲專用終結點概述](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

專用終結點是[虛擬網路](../../virtual-network/virtual-networks-overview.md)（VNet） 中 Azure 服務的特殊網路介面。 為存儲帳戶創建專用終結點時，它將在 VNet 上的用戶端和存儲中的用戶端之間提供安全連線。 從 VNet 的 IP 位址範圍為專用終結點分配了 IP 位址。 專用終結點和存儲服務之間的連接使用安全的專用鏈路。

VNet 中的應用程式可以使用**它們使用的相同連接字串和授權機制**，通過專用終結點無縫連接到存儲服務。 專用終結點可用於存儲帳戶支援的所有協定，包括 REST 和 SMB。

私有終結點可以在使用[服務終結點](../../virtual-network/virtual-network-service-endpoints-overview.md)的子網中創建。 因此，子網中的用戶端可以使用專用終結點連接到一個存儲帳戶，同時使用服務終結點訪問其他存儲帳戶。

當您在 VNet 中為儲存體服務建立私人端點時，便會傳送同意要求給儲存體帳戶擁有者來核准。 如果請求創建專用終結點的使用者也是存儲帳戶的擁有者，則此同意請求將自動獲得批准。

存儲帳戶擁有者可以通過[Azure 門戶](https://portal.azure.com)中的存儲帳戶的"*專用終結點*"選項卡管理同意請求和專用終結點。

> [!TIP]
> 如果只想通過專用終結點限制對存儲帳戶的訪問，請將存儲防火牆配置為拒絕或控制通過公共終結點的訪問。

通過[配置存儲防火牆](storage-network-security.md#change-the-default-network-access-rule)以預設拒絕通過其公共終結點進行訪問，可以保護存儲帳戶僅接受來自 VNet 的連接。 您不需要防火牆規則來允許來自具有專用終結點的 VNet 的流量，因為存儲防火牆僅控制通過公共終結點的訪問。 相反，專用終結點依賴于同意流來授予對存儲服務的子網存取權限。

### <a name="private-endpoints-for-azure-storage"></a>用於 Azure 存儲的專用終結點

創建專用終結點時，必須指定存儲帳戶及其連接到的存儲服務。 您需要訪問的存儲帳戶中的每個存儲服務都需要單獨的專用終結點，即[Blob、](../blobs/storage-blobs-overview.md)[資料存儲 Gen2、](../blobs/data-lake-storage-introduction.md)[檔](../files/storage-files-introduction.md)、[佇列](../queues/storage-queues-introduction.md)、[表](../tables/table-storage-overview.md)或[靜態網站](../blobs/storage-blob-static-website.md)。

> [!TIP]
> 為存儲服務的輔助實例創建單獨的專用終結點，以在 RA-GRS 帳戶上提高讀取性能。

對於已配置為異地冗余存儲的存儲帳戶的次要區域的讀取存取，需要為服務的主實例和輔助實例單獨使用專用終結點。 不需要為故障**轉移**為輔助實例創建專用終結點。 容錯移轉後，專用終結點將自動連接到新的主實例。 有關存儲冗余選項的詳細資訊，請參閱[Azure 存儲冗余](storage-redundancy.md)。

有關為存儲帳戶創建專用終結點的更多詳細資訊，請參閱以下文章：

- [從 Azure 門戶中的存儲帳戶體驗私下連接到存儲帳戶](../../private-link/create-private-endpoint-storage-portal.md)
- [使用 Azure 門戶中的專用連結中心創建專用終結點](../../private-link/create-private-endpoint-portal.md)
- [使用 Azure CLI 建立私人端點](../../private-link/create-private-endpoint-cli.md)
- [使用 Azure PowerShell 創建專用終結點](../../private-link/create-private-endpoint-powershell.md)

### <a name="connecting-to-private-endpoints"></a>連接到專用終結點

使用專用終結點的 VNet 上的用戶端應對存儲帳戶使用相同的連接字串，作為連接到公共終結點的用戶端。 我們依靠 DNS 解析通過專用鏈路自動將連接從 VNet 路由到存儲帳戶。

> [!IMPORTANT]
> 使用相同的連接字串使用專用終結點連接到存儲帳戶，否則可以使用。 請不要使用其"*專用連結*"子域 URL 連接到存儲帳戶。

預設情況下，我們創建一個連接到 VNet 的[專用 DNS 區域](../../dns/private-dns-overview.md)，並包含專用終結點的必要更新。 但是，如果您使用的是自己的 DNS 伺服器，則可能需要對 DNS 配置進行其他更改。 下面有關[DNS 更改](#dns-changes-for-private-endpoints)的部分介紹了專用終結點所需的更新。

## <a name="dns-changes-for-private-endpoints"></a>專用終結點的 DNS 更改

創建專用終結點時，存儲帳戶的 DNS CNAME 資源記錄將更新為子域中的別名，其首碼為"*專用連結*"。 預設情況下，我們還創建一個[專用 DNS 區域](../../dns/private-dns-overview.md)，對應于"*專用連結*"子域，並帶有專用終結點的 DNS A 資源記錄。

當您使用專用終結點從 VNet 外部解析存儲終結點 URL 時，它將解析為存儲服務的公共終結點。 從託管專用終結點的 VNet 解析時，存儲終結點 URL 解析為專用終結點的 IP 位址。

對於上面所示的示例，存儲帳戶"StorageAccountA"的 DNS 資源記錄（從託管專用終結點的 VNet 外部解析）將為：

| 名稱                                                  | 類型  | 值                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<存儲服務公共終結點\>                   |
| \<存儲服務公共終結點\>                   | A     | \<存儲服務公共 IP 位址\>                 |

如前所述，您可以使用存儲防火牆拒絕或控制 VNet 外部用戶端的訪問。這些訪問通過公共終結點。

存儲帳戶A 的 DNS 資源記錄，當由託管專用終結點的 VNet 中的用戶端解析時，將為：

| 名稱                                                  | 類型  | 值                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | 10.1.1.5                                              |

此方法允許對承載專用終結點的 VNet 上的用戶端以及 VNet 外部的用戶端**使用相同的連接字串**訪問存儲帳戶。

如果您在網路上使用自訂 DNS 伺服器，則用戶端必須能夠將存儲帳戶終結點的 FQDN 解析為專用終結點 IP 位址。 應配置 DNS 伺服器以將專用連結子域委派為 VNet 的專用 DNS 區域，或使用專用終結點 IP 位址配置 *"StorageAccountA.privatelink.blob.core.windows.net"* 的 A 記錄。

> [!TIP]
> 使用自訂或本地 DNS 伺服器時，應配置 DNS 伺服器以解析"專用連結"子域中到專用終結點 IP 位址的存儲帳戶名稱。 為此，可以將"專用連結"子域委派到 VNet 的專用 DNS 區域，或者配置 DNS 伺服器上的 DNS 區域並添加 DNS A 記錄。

存儲服務的專用終結點的推薦 DNS 區功能變數名稱稱包括：

| 存儲服務        | 區功能變數名稱稱                            |
| :--------------------- | :----------------------------------- |
| Blob 服務           | `privatelink.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `privatelink.dfs.core.windows.net`   |
| 檔案服務           | `privatelink.file.core.windows.net`  |
| 佇列服務          | `privatelink.queue.core.windows.net` |
| 表服務          | `privatelink.table.core.windows.net` |
| 靜態網站        | `privatelink.web.core.windows.net`   |

有關配置自己的 DNS 伺服器以支援專用終結點的詳細資訊，請參閱以下文章：

- [Azure 虛擬網路中的資源名稱解析](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [專用終結點的 DNS 配置](/azure/private-link/private-endpoint-overview#dns-configuration)

## <a name="pricing"></a>定價

如需定價詳細資料，請參閱 [Azure Private Link 定價](https://azure.microsoft.com/pricing/details/private-link)。

## <a name="known-issues"></a>已知問題

請記住有關 Azure 存儲專用終結點的以下已知問題。

### <a name="copy-blob-support"></a>複製 Blob 支援

如果存儲帳戶受防火牆保護，並且該帳戶通過專用終結點訪問，則該帳戶不能用作[複製 Blob](/rest/api/storageservices/copy-blob)操作的源。

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>具有專用終結點的 VNet 中用戶端的存儲訪問約束

訪問具有專用終結點的其他存儲帳戶時，VNet 中具有現有專用終結點的用戶端將面臨約束。 例如，假設 VNet N1 具有用於 Blob 存儲的存儲帳戶 A1 的專用終結點。 如果存儲帳戶 A2 在用於 Blob 存儲的 VNet N2 中具有專用終結點，則 VNet N1 中的用戶端還必須使用專用終結點存取帳戶 A2 中的 Blob 存儲。 如果存儲帳戶 A2 沒有任何用於 Blob 存儲的專用終結點，則 VNet N1 中的用戶端可以訪問該帳戶中的 Blob 存儲，而無需專用終結點。

此約束是帳戶 A2 創建專用終結點時進行的 DNS 更改的結果。

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>具有私人端點的子網路所適用的網路安全性群組規則

目前，您無法為專用終結點配置[網路安全性群組](../../virtual-network/security-overview.md)（NSG） 規則和使用者定義的路由。 應用於託管專用終結點的子網的 NSG 規則將應用於專用終結點。 此問題的有限解決方法是實現源子網上專用終結點的訪問規則，儘管此方法可能需要更高的管理開銷。

## <a name="next-steps"></a>後續步驟

- [配置 Azure 存儲防火牆和虛擬網路](storage-network-security.md)
- [Blob 存儲的安全建議](../blobs/security-recommendations.md)
