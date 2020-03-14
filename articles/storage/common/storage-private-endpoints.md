---
title: 使用私用端點
titleSuffix: Azure Storage
description: 針對從虛擬網路對儲存體帳戶進行安全存取的私用端點的總覽。
services: storage
author: santoshc
ms.service: storage
ms.topic: article
ms.date: 03/12/2020
ms.author: santoshc
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: c51f2db698f30368c9d4090d3d571fa0c131178a
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299051"
---
# <a name="use-private-endpoints-for-azure-storage"></a>Azure 儲存體使用私人端點

您可以針對您的 Azure 儲存體帳戶使用[私人端點](../../private-link/private-endpoint-overview.md)，以允許虛擬網路（VNet）上的用戶端透過[私人連結](../../private-link/private-link-overview.md)安全地存取資料。 私人端點會針對您的儲存體帳戶服務使用 VNet 位址空間中的 IP 位址。 VNet 上的用戶端與儲存體帳戶之間的網路流量會流經 VNet，並在 Microsoft 骨幹網路上進行私人連結，以消除公開網際網路的風險。

針對您的儲存體帳戶使用私人端點可讓您：

- 設定儲存體防火牆以封鎖儲存體服務的公用端點上的所有連線，藉此保護您的儲存體帳戶。
- 藉由讓您封鎖從 VNet 外泄的資料，提高虛擬網路（VNet）的安全性。
- 使用[VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)或具有私人對等互連的[ExpressRoutes](../../expressroute/expressroute-locations.md) ，安全地從連線到 VNet 的內部部署網路連線到儲存體帳戶。

## <a name="conceptual-overview"></a>概念概觀

![Azure 儲存體的私用端點總覽](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

私人端點是[虛擬網路](../../virtual-network/virtual-networks-overview.md)（VNet）中 Azure 服務的特殊網路介面。 當您建立儲存體帳戶的私人端點時，它會在 VNet 上的用戶端與您的儲存體之間提供安全的連線。 私人端點會從 VNet 的 IP 位址範圍指派 IP 位址。 私人端點與儲存體服務之間的連接會使用安全的私用連結。

VNet 中的應用程式可以**使用相同的連接字串和授權機制**，順暢地透過私人端點連接到儲存體服務，否則會使用它們。 私人端點可以與儲存體帳戶支援的所有通訊協定搭配使用，包括 REST 和 SMB。

私人端點可以在使用[服務端點](../../virtual-network/virtual-network-service-endpoints-overview.md)的子網中建立。 因此，子網中的用戶端可以使用私人端點連接到一個儲存體帳戶，同時使用服務端點來存取其他使用者。

當您在 VNet 中為儲存體服務建立私人端點時，便會傳送同意要求給儲存體帳戶擁有者來核准。 如果要求建立私人端點的使用者也是儲存體帳戶的擁有者，則會自動核准此同意要求。

儲存體帳戶擁有者可以透過[Azure 入口網站](https://portal.azure.com)中儲存體帳戶的 [*私人端點*] 索引標籤，來管理同意要求和私人端點。

> [!TIP]
> 如果您想要僅透過私人端點來限制對儲存體帳戶的存取，請設定儲存防火牆以拒絕或透過公用端點控制存取。

您可以藉由設定[儲存防火牆](storage-network-security.md#change-the-default-network-access-rule)預設拒絕透過其公用端點的存取，來保護您的儲存體帳戶，使其僅接受來自 VNet 的連線。 您不需要防火牆規則，就可以允許來自具有私人端點之 VNet 的流量，因為儲存體防火牆只會控制透過公用端點的存取。 私用端點會改為依賴同意流程來授與對儲存體服務的子網存取權。

### <a name="private-endpoints-for-azure-storage"></a>Azure 儲存體的私用端點

建立私用端點時，您必須指定儲存體帳戶和它所連接的儲存體服務。 在您需要存取的儲存體帳戶中，每個儲存體服務都需要個別的私用[端點，也](../files/storage-files-introduction.md)就是[blob](../blobs/storage-blobs-overview.md)、 [Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md)、檔案、[佇列](../queues/storage-queues-introduction.md)、[資料表](../tables/table-storage-overview.md)或[靜態網站](../blobs/storage-blob-static-website.md)。

> [!TIP]
> 為儲存體服務的次要實例建立個別的私用端點，以在遠端協助 GRS 帳戶上獲得更佳的讀取效能。

若要使用針對異地多餘儲存體設定的儲存體帳戶進行次要區域的讀取存取，您需要服務之主要和次要實例的個別私人端點。 您不需要為次要實例建立私人端點來進行**容錯移轉**。 私人端點會在容錯移轉之後自動連接到新的主要實例。 如需儲存體冗余選項的詳細資訊，請參閱[Azure 儲存體冗余](storage-redundancy.md)。

如需有關為您的儲存體帳戶建立私人端點的詳細資訊，請參閱下列文章：

- [從 Azure 入口網站中的儲存體帳戶體驗，私下連接至儲存體帳戶](../../private-link/create-private-endpoint-storage-portal.md)
- [在 Azure 入口網站中使用私人連結中心建立私人端點](../../private-link/create-private-endpoint-portal.md)
- [使用 Azure CLI 建立私人端點](../../private-link/create-private-endpoint-cli.md)
- [使用 Azure PowerShell 建立私人端點](../../private-link/create-private-endpoint-powershell.md)

### <a name="connecting-to-private-endpoints"></a>連接到私人端點

使用私用端點之 VNet 上的用戶端，應使用與連線到公用端點的用戶端相同的儲存體帳戶連接字串。 我們依賴 DNS 解析，透過私人連結自動將 VNet 的連線路由至儲存體帳戶。

> [!IMPORTANT]
> 使用相同的連接字串來連接到使用私人端點的儲存體帳戶，如您所使用的其他方式。 請不要使用其 '*privatelink*' 子域 URL 連線到儲存體帳戶。

根據預設，我們會建立連結至 VNet 的[私人 DNS 區域](../../dns/private-dns-overview.md)，並具有私人端點的必要更新。 不過，如果您是使用自己的 DNS 伺服器，您可能需要對 DNS 設定進行其他變更。 下列[DNS 變更](#dns-changes-for-private-endpoints)章節說明私人端點所需的更新。

## <a name="dns-changes-for-private-endpoints"></a>私人端點的 DNS 變更

當您建立私人端點時，儲存體帳戶的 DNS CNAME 資源記錄會更新為前置詞為 '*privatelink*' 之子域中的別名。 根據預設，我們也會建立與 '*privatelink*' 子域對應的[私人 DNS 區域](../../dns/private-dns-overview.md)，並以 DNS 做為私人端點的資源記錄。

當您從具有私用端點的 VNet 外部解析儲存體端點 URL 時，它會解析為儲存體服務的公用端點。 從裝載私用端點的 VNet 解析時，儲存體端點 URL 會解析為私人端點的 IP 位址。

針對上述範例，當從裝載私人端點的 VNet 外部解析時，儲存體帳戶 ' StorageAccountA ' 的 DNS 資源記錄將會是：

| 名稱                                                  | 類型  | 值                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<儲存體服務公用端點\>                   |
| \<儲存體服務公用端點\>                   | A     | \<儲存體服務公用 IP 位址\>                 |

如先前所述，您可以透過使用儲存體防火牆的公用端點，拒絕或控制 VNet 外部的用戶端存取。

StorageAccountA 的 DNS 資源記錄（當由裝載私用端點的 VNet 中的用戶端解析時）會是：

| 名稱                                                  | 類型  | 值                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | 10.1.1.5                                              |

這種方法可讓您**使用相同的連接字串**來存取儲存體帳戶，以用於裝載私人端點的 vnet 上的用戶端，以及 vnet 外的用戶端。

如果您在網路上使用自訂 DNS 伺服器，用戶端必須能夠將儲存體帳戶端點的 FQDN 解析為私人端點 IP 位址。 您應該將 DNS 伺服器設定為將私人連結子域委派給 VNet 的私人 DNS 區域，或使用私人端點 IP 位址來設定 '*StorageAccountA.privatelink.blob.core.windows.net*' 的 A 記錄。

> [!TIP]
> 使用自訂或內部部署 DNS 伺服器時，您應該將 DNS 伺服器設定為將 ' privatelink ' 子域中的儲存體帳戶名稱解析為私人端點 IP 位址。 若要這麼做，您可以將 ' privatelink ' 子域委派給 VNet 的私人 DNS 區域，或在 DNS 伺服器上設定 DNS 區域，並新增 DNS A 記錄。

針對儲存體服務的私人端點，建議的 DNS 區功能變數名稱稱為：

| 儲存體服務        | 區功能變數名稱稱                            |
| :--------------------- | :----------------------------------- |
| Blob 服務           | `privatelink.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `privatelink.dfs.core.windows.net`   |
| 檔案服務           | `privatelink.file.core.windows.net`  |
| 佇列服務          | `privatelink.queue.core.windows.net` |
| 表格服務          | `privatelink.table.core.windows.net` |
| 靜態網站        | `privatelink.web.core.windows.net`   |

如需有關設定您自己的 DNS 伺服器以支援私人端點的詳細資訊，請參閱下列文章：

- [Azure 虛擬網路中的資源名稱解析](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [私人端點的 DNS 設定](/azure/private-link/private-endpoint-overview#dns-configuration)

## <a name="pricing"></a>價格

如需定價詳細資料，請參閱 [Azure Private Link 定價](https://azure.microsoft.com/pricing/details/private-link)。

## <a name="known-issues"></a>已知問題

請記住，Azure 儲存體的私用端點的下列已知問題。

### <a name="copy-blob-support"></a>複製 Blob 支援

如果儲存體帳戶受到防火牆保護，且帳戶是透過私人端點來存取，則該帳戶無法作為[複製 Blob](/rest/api/storageservices/copy-blob)作業的來源。

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>具有私人端點的 Vnet 中用戶端的儲存體存取條件約束

Vnet 中具有現有私用端點的用戶端，在存取其他具有私人端點的儲存體帳戶時，會面臨條件約束。 例如，假設 VNet N1 具有儲存體帳戶 A1 的私人端點，適用于 Blob 儲存體。 如果儲存體帳戶 A2 在 VNet 中有適用于 Blob 儲存體的私用端點，則 VNet N1 中的用戶端也必須使用私用端點來存取帳戶 A2 中的 Blob 儲存體。 如果儲存體帳戶 A2 沒有 Blob 儲存體的任何私人端點，則 VNet N1 中的用戶端可以存取該帳戶中的 Blob 儲存體，而不需要私用端點。

此條件約束是當帳戶 A2 建立私人端點時，所做的 DNS 變更結果。

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>具有私人端點之子網的網路安全性群組規則

目前，您無法為私人端點設定[網路安全性群組](../../virtual-network/security-overview.md)（NSG）規則和使用者定義的路由。 套用至裝載私用端點之子網的 NSG 規則會套用至私用端點。 此問題的有限因應措施是在來源子網上為私人端點執行存取規則，不過這種方法可能需要較高的管理負荷。

## <a name="next-steps"></a>後續步驟

- [設定 Azure 儲存體防火牆和虛擬網路](storage-network-security.md)
- [Blob 儲存體的安全性建議](../blobs/security-recommendations.md)
