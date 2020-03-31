---
title: 包含檔案
description: 包含檔案
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 03/04/2020
ms.author: labrenne
ms.custom: include file
ms.openlocfilehash: e9460108499ca76d1b149b61cebe3d3081bf6544
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086272"
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

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.Network/virtualNetworks/{network}/subnets/{subnet}
  ```

**權限** - 請確認您對 VNet 的訂用帳戶或資源群組的安全性原則或鎖定是否限制了使用者管理 VNet 的權限。

**其他網路資源** - Batch 會在包含 VNet 的資源群組中自動配置其他網路資源。

> [!IMPORTANT]
>對於每個 50 個專用節點（或每 20 個低優先順序節點），Batch 分配：一個網路安全性群組 （NSG）、一個公共 IP 位址和一個負載等化器。 這些資源會被訂用帳戶的[資源配額](../articles/azure-resource-manager/management/azure-subscription-service-limits.md)所限制。 對於大型池，您可能需要請求增加一個或多個這些資源的配額。

#### <a name="network-security-groups-batch-default"></a>網路安全性群組：批次處理預設值

子網必須允許來自 Batch 服務的入站通信能夠安排計算節點上的任務，以及出站通信，以便根據工作負荷的需求與 Azure 存儲或其他資源進行通信。 對於虛擬機器配置中的池，Batch 在附加到計算節點的網路介面 （NIC） 級別添加 NSG。 這些 NSG 配置了以下附加規則：

* 埠 29876 和 29877 上的入站 TCP 流量來自對應于服務標記`BatchNodeManagement`的 Batch 服務 IP 位址。
* 連接埠 22 (Linux 節點) 或連接埠 3389 (Windows 節點) 上的輸入 TCP 流量，以允許遠端存取。 對於 Linux 上的某些類型的多實例任務（如 MPI），還需要允許在包含 Batch 計算節點的子網路中為 IP 提供 SSH 埠 22 流量。 這可能會根據子網級 NSG 規則阻止（見下文）。
* 任何連接埠上傳至虛擬網路的輸出流量。 可以根據子網級 NSG 規則對此進行修訂（見下文）。
* 任何埠到 Internet 的出站流量。 可以根據子網級 NSG 規則對此進行修訂（見下文）。

> [!IMPORTANT]
> 如果您要在 Batch 設定的 NSG 中修改或新增輸入或輸出規則，請謹慎操作。 如果 NSG 拒絕對所指定子網路中計算節點的通訊，則 Batch 服務會將計算節點的狀態設為 [無法使用]****。 此外，不應將任何資源鎖應用於 Batch 創建的任何資源，否則可能導致由於使用者啟動的操作（如刪除池）而阻止資源清理。

#### <a name="network-security-groups-specifying-subnet-level-rules"></a>網路安全性群組：指定子網級規則

不需要在虛擬網路子網級別指定 NSG，因為 Batch 配置自己的 NSG（見上文）。 如果 NSG 與部署 Batch 計算節點的子網相關聯，或者希望應用自訂 NSG 規則來覆蓋應用的預設值，則必須使用至少入站和出站安全規則配置此 NSG，如下所示表。

僅當需要允許從外部源遠端存取計算節點時，才在埠 3389 （Windows） 或 22 （Linux） 上配置入站流量。 如果需要支援具有某些 MPI 運行時的多實例任務，則可能需要在 Linux 上啟用埠 22 規則。 池計算節點的可用性並非嚴格要求允許這些埠上的流量。

**輸入安全性規則**

| 來源 IP 位址 | 來源服務標籤 | 來源連接埠 | Destination | 目的地連接埠 | 通訊協定 | 動作 |
| --- | --- | --- | --- | --- | --- | --- |
| N/A | `BatchNodeManagement`[服務標記](../articles/virtual-network/security-overview.md#service-tags)（如果使用區域變體，與批次處理帳戶位於同一區域） | * | 任意 | 29876-29877 | TCP | Allow |
| 使用者源 IP 用於遠端存取計算節點和/或計算節點子網，用於 Linux 多實例任務（如果需要）。 | N/A | * | 任意 | 3389 (Windows)、22 (Linux) | TCP | Allow |

**輸出安全性規則**

| 來源 | 來源連接埠 | Destination | 目的地服務標記 | 目的地連接埠 | 通訊協定 | 動作 |
| --- | --- | --- | --- | --- | --- | --- |
| 任意 | * | [服務標籤](../articles/virtual-network/security-overview.md#service-tags) | `Storage`（如果使用區域變體，則與批次處理帳戶位於同一區域） | 443 | TCP | Allow |

### <a name="pools-in-the-cloud-services-configuration"></a>雲端服務組態中的集區

**支援的 VNet** - 僅限傳統 VNet

**子網路識別碼** - 使用 Batch API 指定子網路時，請使用子網路的*資源識別碼*。 子網路識別碼的格式為：

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicNetwork /virtualNetworks/{network}/subnets/{subnet}
  ```

**權限** - 針對指定的 VNet，`Microsoft Azure Batch` 服務主體必須具有 `Classic Virtual Machine Contributor` 角色型存取控制 (RBAC) 角色。

#### <a name="network-security-groups"></a>網路安全性群組

子網路必須允許來自 Batch 服務的輸入通訊，才能在計算節點上排程工作，且必須允許輸出通訊，才能與 Azure 儲存體或其他資源進行通訊。

您不需要指定 NSG，因為 Batch 只會設定從 Batch IP 位址到集區節點的輸入通訊。 不過，如果指定的子網路有相關聯的 NSG 和 (或) 防火牆，請設定輸入和輸出安全性規則，如下列表格所示。 如果 NSG 拒絕對所指定子網路中計算節點的通訊，則 Batch 服務會將計算節點的狀態設為 [無法使用]****。

如果需要允許 RDP 訪問池節點，請為 Windows 埠 3389 配置入站流量。 集區節點不需要此設定即可使用。

**輸入安全性規則**

| 來源 IP 位址 | 來源連接埠 | Destination | 目的地連接埠 | 通訊協定 | 動作 |
| --- | --- | --- | --- | --- | --- |
任意 <br /><br />雖然這實際上需要「全部允許」，但 Batch 服務會在每個節點的層級上套用 ACL 規則，而篩選掉所有非 Batch 服務的 IP 位址。 | * | 任意 | 10100、20100、30100 | TCP | Allow |
| 可選，允許 RDP 訪問計算節點。 | * | 任意 | 3389 | TCP | Allow |

**輸出安全性規則**

| 來源 | 來源連接埠 | Destination | 目的地連接埠 | 通訊協定 | 動作 |
| --- | --- | --- | --- | --- | --- |
| 任意 | * | 任意 | 443  | 任意 | Allow |
