---
title: 包含檔案
description: 包含檔案
services: batch
documentationcenter: ''
author: JnHs
manager: evansma
editor: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.date: 06/16/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 1b21141a4b3f9ae92cdcf1d5a93a457012cb136a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85506582"
---
### <a name="general-requirements"></a>一般需求

* VNet 必須與您用來建立集區的 Batch 帳戶位於相同的訂用帳戶和區域中。

* 使用 VNet 的集區最多可以有 4096 個節點。

* 針對集區指定的子網路必須有足夠的未指派 IP 位址，可容納目標設為集區的 VM 數目；也就是集區之 `targetDedicatedNodes` 和 `targetLowPriorityNodes` 屬性的總和。 如果子網路沒有足夠的未指派 IP 位址，集區會局部配置計算節點，並發生調整大小錯誤。

* 為 VNet 提供服務的任何自訂 DNS 服務，都必須能解析您的 Azure 儲存體端點。 具體而言，`<account>.table.core.windows.net`、`<account>.queue.core.windows.net` 和 `<account>.blob.core.windows.net` 形式的 URL 應該可解析。

其他的 VNet 需求不盡相同，這取決於 Batch 集區是位於虛擬機器組態還是雲端服務組態中。 對於部署至 VNet 的新集區，建議使用虛擬機器組態。

### <a name="pools-in-the-virtual-machine-configuration"></a>虛擬機器組態中的集區

**支援的 Vnet** - 僅限以 Azure Resource Manager 為基礎的 Vnet

**子網路識別碼** - 使用 Batch API 指定子網路時，請使用子網路的*資源識別碼*。 子網路識別碼的格式為：

`/subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.Network/virtualNetworks/{network}/subnets/{subnet}`

**權限** - 請確認您對 VNet 的訂用帳戶或資源群組的安全性原則或鎖定是否限制了使用者管理 VNet 的權限。

**其他網路資源** - Batch 會在包含 VNet 的資源群組中自動配置其他網路資源。

> [!IMPORTANT]
> 針對每個100專用或低優先順序節點，Batch 會配置：一個網路安全性群組（NSG）、一個公用 IP 位址，以及一個負載平衡器。 這些資源會被訂用帳戶的[資源配額](../articles/azure-resource-manager/management/azure-subscription-service-limits.md)所限制。 針對大型集區，您可能需要要求增加一或多個資源的配額。

#### <a name="network-security-groups-batch-default"></a>網路安全性群組：批次預設值

子網必須允許來自 Batch 服務的輸入通訊，才能在計算節點上排程工作，並在您的工作負載需要時，與 Azure 儲存體或其他資源進行通訊，以進行輸出通訊。 針對虛擬機器設定中的集區，Batch 會在附加至計算節點的網路介面（Nic）層級新增 Nsg。 這些 Nsg 是以下列其他規則設定：

* 埠29876和29877上的輸入 TCP 流量，來自與服務標記對應的 Batch 服務 IP 位址 `BatchNodeManagement` 。
* 連接埠 22 (Linux 節點) 或連接埠 3389 (Windows 節點) 上的輸入 TCP 流量，以允許遠端存取。 針對 Linux 上的特定類型的多重實例工作（例如 MPI），您也必須允許子網中包含 Batch 計算節點之 Ip 的 SSH 埠22流量。 這可能會因為子網層級的 NSG 規則而遭到封鎖（請參閱下文）。
* 任何連接埠上傳至虛擬網路的輸出流量。 這可能會根據子網層級的 NSG 規則進行修改（請參閱下文）。
* 任何埠上的輸出流量到網際網路。 這可能會根據子網層級的 NSG 規則進行修改（請參閱下文）。

> [!IMPORTANT]
> 如果您在批次設定的 Nsg 中修改或新增輸入或輸出規則，請謹慎使用。 如果 NSG 拒絕與所指定子網中計算節點的通訊，則 Batch 服務會將計算節點的狀態設定為 [**無法使用**]。 此外，不應將資源鎖定套用至 Batch 所建立的任何資源，因為這可以避免因使用者起始的動作（例如刪除集區）而清除資源。

#### <a name="network-security-groups-specifying-subnet-level-rules"></a>網路安全性群組：指定子網層級規則

您不需要在虛擬網路子網層級指定 Nsg，因為 Batch 會設定自己的 Nsg （請參閱上面的）。 如果您的 NSG 與用來部署 Batch 計算節點的子網相關聯，或者您想要套用自訂 NSG 規則來覆寫套用的預設值，則必須至少使用下表所示的輸入和輸出安全性規則來設定此 NSG。

只有當您需要允許從外部來源對計算節點進行遠端存取時，才在埠3389（Windows）或22（Linux）上設定輸入流量。 如果您需要支援某些 MPI 執行時間的多重實例工作，您可能需要在 Linux 上啟用埠22規則。 不一定要允許這些埠上的流量，讓集區計算節點可供使用。

**輸入安全性規則**

| 來源 IP 位址 | 來源服務標籤 | 來源連接埠 | Destination | 目的地連接埠 | 通訊協定 | 動作 |
| --- | --- | --- | --- | --- | --- | --- |
| N/A | `BatchNodeManagement`[服務](../articles/virtual-network/security-overview.md#service-tags)標籤（如果使用區域性 variant，在與 Batch 帳戶相同的區域中） | * | 任意 | 29876-29877 | TCP | 允許 |
| 使用者來源 Ip，用於遠端存取 Linux 多重實例工作的計算節點和/或計算節點子網（如有需要）。 | N/A | * | 任意 | 3389 (Windows)、22 (Linux) | TCP | 允許 |

> [!WARNING]
> Batch 服務 IP 位址可能會隨著時間變更。 因此，強烈建議您 `BatchNodeManagement` 針對 NSG 規則使用服務標籤（或區域變化）。 避免使用特定的 Batch 服務 IP 位址填入 NSG 規則。

**輸出安全性規則**

| 來源 | 來源連接埠 | Destination | 目的地服務標記 | 目的地連接埠 | 通訊協定 | 動作 |
| --- | --- | --- | --- | --- | --- | --- |
| 任意 | * | [服務標記](../articles/virtual-network/security-overview.md#service-tags) | `Storage`（如果使用區域性 variant，在與 Batch 帳戶相同的區域中） | 443 | TCP | 允許 |

### <a name="pools-in-the-cloud-services-configuration"></a>雲端服務組態中的集區

**支援的 VNet** - 僅限傳統 VNet

**子網路識別碼** - 使用 Batch API 指定子網路時，請使用子網路的*資源識別碼*。 子網路識別碼的格式為：

`/subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicNetwork /virtualNetworks/{network}/subnets/{subnet}`

**權限** - 針對指定的 VNet，`Microsoft Azure Batch` 服務主體必須具有 `Classic Virtual Machine Contributor` 角色型存取控制 (RBAC) 角色。

#### <a name="network-security-groups"></a>網路安全性群組

子網路必須允許來自 Batch 服務的輸入通訊，才能在計算節點上排程工作，且必須允許輸出通訊，才能與 Azure 儲存體或其他資源進行通訊。

您不需要指定 NSG，因為 Batch 只會設定從 Batch IP 位址到集區節點的輸入通訊。 不過，如果指定的子網路有相關聯的 NSG 和 (或) 防火牆，請設定輸入和輸出安全性規則，如下列表格所示。 如果 NSG 拒絕與所指定子網中計算節點的通訊，則 Batch 服務會將計算節點的狀態設定為 [**無法使用**]。

如果您需要允許對集區節點的 RDP 存取，請在適用于 Windows 的埠3389上設定輸入流量。 若要讓集區節點可供使用，則不需要這麼做。

**輸入安全性規則**

| 來源 IP 位址 | 來源連接埠 | Destination | 目的地連接埠 | 通訊協定 | 動作 |
| --- | --- | --- | --- | --- | --- |
任意 <br /><br />雖然這實際上需要「全部允許」，但 Batch 服務會在每個節點的層級上套用 ACL 規則，而篩選掉所有非 Batch 服務的 IP 位址。 | * | 任意 | 10100、20100、30100 | TCP | 允許 |
| 選擇性，允許 RDP 存取計算節點。 | * | 任意 | 3389 | TCP | 允許 |

**輸出安全性規則**

| 來源 | 來源連接埠 | Destination | 目的地連接埠 | 通訊協定 | 動作 |
| --- | --- | --- | --- | --- | --- |
| 任意 | * | 任意 | 443  | 任意 | Allow |
