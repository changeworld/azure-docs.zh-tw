---
title: 使用私人端點
titleSuffix: Azure Storage
description: 從虛擬網路安全存取儲存體帳戶的私人端點總覽。
services: storage
author: santoshc
ms.service: storage
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: santoshc
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 0da970724a5d6f0ad42ba64939f316ec1ada855b
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905548"
---
# <a name="use-private-endpoints-for-azure-storage"></a>使用私人端點進行 Azure 儲存體

您可以使用 Azure 儲存體帳戶的 [私人端點](../../private-link/private-endpoint-overview.md) ，讓虛擬網路上的用戶端 (VNet) 安全地透過 [Private Link](../../private-link/private-link-overview.md)存取資料。 私人端點會使用來自 VNet 位址空間的 IP 位址來儲存您的儲存體帳戶服務。 VNet 上的用戶端與儲存體帳戶之間的網路流量，會透過 VNet 和 Microsoft 骨幹網路上的私人連結來進行，以消除公用網際網路的暴露。

使用儲存體帳戶的私人端點可讓您：

- 設定儲存體防火牆以封鎖儲存體服務的公用端點上的所有連線，以保護您的儲存體帳戶。
- 藉由讓您封鎖從 VNet 遭到外泄的資料，提高虛擬網路 (VNet) 的安全性。
- 使用 [VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) 或 [expressroute](../../expressroute/expressroute-locations.md) 搭配私人對等互連，安全地從連線到 VNet 的內部部署網路連線到儲存體帳戶。

## <a name="conceptual-overview"></a>概念概觀

![Azure 儲存體的私人端點總覽](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

私人端點是您 [虛擬網路](../../virtual-network/virtual-networks-overview.md) 中的 Azure 服務 (VNet) 的特殊網路介面。 當您為儲存體帳戶建立私人端點時，它會在 VNet 上的用戶端與您的儲存體之間提供安全的連線能力。 私人端點會從 VNet 的 IP 位址範圍中指派 IP 位址。 私人端點與儲存體服務之間的連接會使用安全的私用連結。

VNet 中的應用程式可以順暢地透過私人端點連接到儲存體服務， **並使用它們將使用的相同連接字串和授權機制**。 私人端點可搭配儲存體帳戶支援的所有通訊協定使用，包括 REST 和 SMB。

私人端點可以在使用 [服務端點](../../virtual-network/virtual-network-service-endpoints-overview.md)的子網中建立。 因此，子網中的用戶端可以使用私人端點連接到一個儲存體帳戶，而使用服務端點來存取其他帳戶。

當您在 VNet 中為儲存體服務建立私人端點時，便會傳送同意要求給儲存體帳戶擁有者來核准。 如果要求建立私人端點的使用者也是儲存體帳戶的擁有者，則會自動核准此同意要求。

儲存體帳戶擁有者可以透過 [Azure 入口網站](https://portal.azure.com)中儲存體帳戶的 [*私人端點*] 索引標籤，來管理同意要求和私人端點。

> [!TIP]
> 如果您想要限制只能透過私人端點存取您的儲存體帳戶，請將儲存體防火牆設定為拒絕或控制透過公用端點的存取。

您可以藉由設定 [儲存體防火牆](storage-network-security.md#change-the-default-network-access-rule) 預設拒絕透過其公用端點的存取，來保護您的儲存體帳戶僅接受來自 VNet 的連線。 您不需要防火牆規則來允許來自具有私人端點之 VNet 的流量，因為儲存體防火牆只會控制透過公用端點的存取。 私人端點會改為依賴同意流程，將存取權授與儲存體服務的子網。

### <a name="private-endpoints-for-azure-storage"></a>Azure 儲存體的私人端點

建立私人端點時，您必須指定儲存體帳戶及其連接的儲存體服務。 您需要存取的儲存體帳戶中，每個儲存體服務都需要個別的私人[端點，也](../files/storage-files-introduction.md)就是[blob](../blobs/storage-blobs-overview.md)、 [Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md)、檔案、[佇列](../queues/storage-queues-introduction.md)、[資料表](../tables/table-storage-overview.md)或[靜態網站](../blobs/storage-blob-static-website.md)。

> [!TIP]
> 為儲存體服務的次要實例建立個別的私人端點，以獲得更佳的 GRS 帳戶讀取效能。
> 請務必建立一般用途 v2 (Standard 或 Premium) 儲存體帳戶。

若要對次要區域進行讀取存取，並將儲存體帳戶設定為異地多餘的儲存體，您需要服務的主要和次要實例都有個別的私人端點。 您不需要為用於 **容錯移轉** 的次要實例建立私人端點。 私人端點會在容錯移轉之後自動連接到新的主要實例。 如需儲存體冗余選項的詳細資訊，請參閱 [Azure 儲存體冗余](storage-redundancy.md)。

如需有關為您的儲存體帳戶建立私人端點的詳細資訊，請參閱下列文章：

- [從 Azure 入口網站中的儲存體帳戶體驗私下連線至儲存體帳戶](../../private-link/tutorial-private-endpoint-storage-portal.md)
- [使用 Azure 入口網站中的 Private Link 中心建立私人端點](../../private-link/create-private-endpoint-portal.md)
- [使用 Azure CLI 建立私人端點](../../private-link/create-private-endpoint-cli.md)
- [使用 Azure PowerShell 建立私人端點](../../private-link/create-private-endpoint-powershell.md)

### <a name="connecting-to-private-endpoints"></a>連接至私人端點

使用私人端點之 VNet 上的用戶端應該使用與儲存體帳戶相同的連接字串，就像連接到公用端點的用戶端一樣。 我們依賴 DNS 解析，以透過私人連結將 VNet 的連線自動路由傳送到儲存體帳戶。

> [!IMPORTANT]
> 使用相同的連接字串來連接到使用私人端點的儲存體帳戶，如您所使用的一樣。 請勿使用 '*privatelink*' 子域 URL 連接到儲存體帳戶。

根據預設，我們會建立附加至 VNet 的 [私人 DNS 區域](../../dns/private-dns-overview.md) ，並具有私人端點的必要更新。 但是，如果您使用自己的 DNS 伺服器，您可能需要對 DNS 設定進行額外的變更。 下列 [DNS 變更](#dns-changes-for-private-endpoints) 一節說明私人端點所需的更新。

## <a name="dns-changes-for-private-endpoints"></a>私人端點的 DNS 變更

當您建立私人端點時，儲存體帳戶的 DNS CNAME 資源記錄會更新為首碼為 '*privatelink*' 的子域中的別名。 根據預設，我們也會建立對應至 '*privatelink*' 子域的 [私人 dns 區域](../../dns/private-dns-overview.md)，並以 DNS 為私人端點的資源記錄。

當您從 VNet 外部以私人端點解析儲存體端點 URL 時，它會解析為儲存體服務的公用端點。 從裝載私人端點的 VNet 解析時，儲存體端點 URL 會解析為私人端點的 IP 位址。

在上述說明的範例中，儲存體帳戶 ' StorageAccountA ' 的 DNS 資源記錄是從裝載私人端點的 VNet 外部解析時，將會是：

| 名稱                                                  | 類型  | 值                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<storage service public endpoint\>                   |
| \<storage service public endpoint\>                   | A     | \<storage service public IP address\>                 |

如先前所述，您可以透過使用儲存體防火牆的公用端點，拒絕或控制 VNet 外部用戶端的存取。

StorageAccountA 的 DNS 資源記錄（由裝載私人端點的 VNet 中的用戶端解析時）將會是：

| 名稱                                                  | 類型  | 值                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | 10.1.1.5                                              |

這種方法可讓您在裝載私人端點的 VNet 上，以及 VNet 外部用戶端的用戶端， **使用相同的連接字串** 來存取儲存體帳戶。

如果您在網路上使用自訂 DNS 伺服器，用戶端必須能夠將儲存體帳戶端點的 FQDN 解析為私人端點 IP 位址。 您應該將您的 DNS 伺服器設定為將私人連結子域委派給 VNet 的私人 DNS 區域，或設定具有私人端點 IP 位址的 '*StorageAccountA.privatelink.blob.core.windows.net*' A 記錄。

> [!TIP]
> 使用自訂或內部部署 DNS 伺服器時，您應該將 DNS 伺服器設定為將 ' privatelink ' 子域中的儲存體帳戶名稱解析為私人端點 IP 位址。 若要這麼做，您可以將 ' privatelink ' 子域委派給 VNet 的私人 DNS 區域，或在 DNS 伺服器上設定 DNS 區域，然後新增 DNS A 記錄。

適用于儲存體服務之私人端點的建議 DNS 區功能變數名稱稱如下：

| 儲存體服務        | 區功能變數名稱稱                            |
| :--------------------- | :----------------------------------- |
| Blob 服務           | `privatelink.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `privatelink.dfs.core.windows.net`   |
| 檔案服務           | `privatelink.file.core.windows.net`  |
| 佇列服務          | `privatelink.queue.core.windows.net` |
| 表格服務          | `privatelink.table.core.windows.net` |
| 靜態網站        | `privatelink.web.core.windows.net`   |

如需有關設定您自己的 DNS 伺服器以支援私人端點的詳細資訊，請參閱下列文章：

- [Azure 虛擬網路中的資源名稱解析](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [私人端點的 DNS 設定](../../private-link/private-endpoint-overview.md#dns-configuration)

## <a name="pricing"></a>定價

如需定價詳細資料，請參閱 [Azure Private Link 定價](https://azure.microsoft.com/pricing/details/private-link)。

## <a name="known-issues"></a>已知問題

請記住下列 Azure 儲存體私人端點的已知問題。

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>具有私人端點的 Vnet 中用戶端的儲存體存取限制

在 Vnet 中，使用現有私人端點的用戶端會在存取其他具有私人端點的儲存體帳戶時，面對條件約束。 例如，假設 VNet N1 具有適用于 Blob 儲存體之儲存體帳戶 A1 的私人端點。 如果儲存體帳戶 A2 在 VNet N2 中有 Blob 儲存體的私人端點，則 VNet N1 中的用戶端也必須使用私人端點來存取帳戶 A2 中的 Blob 儲存體。 如果儲存體帳戶 A2 沒有任何適用于 Blob 儲存體的私人端點，則 VNet N1 中的用戶端可以存取該帳戶中的 Blob 儲存體，而不需要私人端點。

此條件約束是當帳戶 A2 建立私人端點時，所做的 DNS 變更所產生的結果。

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>具有私人端點的子網路所適用的網路安全性群組規則

您目前無法設定 [網路安全性群組](../../virtual-network/network-security-groups-overview.md) (NSG) 規則和私人端點的使用者定義路由。 套用至裝載私人端點之子網的 NSG 規則只會套用至其他端點， (例如，Nic) 而非私人端點。 此問題的有限因應措施是針對來源子網上的私人端點實行存取規則，但是這種方法可能需要較高的管理負荷。

## <a name="next-steps"></a>後續步驟

- [設定 Azure 儲存體防火牆和虛擬網路](storage-network-security.md)
- [Blob 儲存體的安全性建議](../blobs/security-recommendations.md)
