---
title: 虛擬核心模型概觀
description: vCore 採購模型允許您獨立擴展計算和存儲資源、匹配本地性能並優化價格。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/27/2019
ms.openlocfilehash: 5fd69dcd30292630862887ab5434764ba377b396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481077"
---
# <a name="vcore-model-overview"></a>虛擬核心模型概觀

虛擬核心 （vCore） 模型提供了以下幾個好處：

- 更高的計算、記憶體、IO 和存儲限制。
- 控制硬體生成，以更好地匹配工作負載的計算和記憶體要求。
- [Azure 混合權益 （AHB）](sql-database-azure-hybrid-benefit.md)和[預留實例 （RI）](sql-database-reserved-capacity.md)的定價折扣。
- 提高支援計算的硬體詳細資訊的透明度;便於規劃來自本地部署的遷移。

## <a name="service-tiers"></a>服務層

vCore 模型中的服務層選項包括通用、業務關鍵和超大規模。 服務層通常定義與可用性和災害復原相關的存儲體系結構、空間和 IO 限制以及業務連續性選項。

||**一般用途**|**業務關鍵型**|**超大規模資料庫**|
|---|---|---|---|
|適用對象|大部分的商業工作負載。 提供面向預算、平衡且可擴展的計算和存儲選項。 |通過使用多個獨立副本，商務應用程式能夠對故障具有最高的恢復能力，並且每個資料庫副本提供最高的 I/O 性能。|大多數業務工作負載具有高度可擴展的存儲和讀取規模要求。  允許配置多個獨立資料庫副本，從而提供更高的故障恢復能力。 |
|存放裝置|使用遠端存放。<br/>**單個資料庫和彈性池預配計算**：<br/>5 GB – 4 TB<br/>**無伺服器計算**：<br/>5 GB - 3 TB<br/>**託管實例**： 32 GB - 8 TB |使用本地 SSD 存儲。<br/>**單個資料庫和彈性池預配計算**：<br/>5 GB – 4 TB<br/>**託管實例**：<br/>32 GB - 4 TB |根據需要靈活自動擴展存儲。 支援高達 100 TB 的存儲空間。 將本地 SSD 存儲用於本地緩衝集區緩存和本地資料存儲。 使用 Azure 遠端存放作為最終的長期資料存儲。 |
|IOPS 和輸送量（近似值）|**單個資料庫和彈性池**：請參閱[單個資料庫](../sql-database/sql-database-vcore-resource-limits-single-databases.md)和[彈性池](../sql-database/sql-database-vcore-resource-limits-elastic-pools.md)的資源限制。<br/>**託管實例**：請參閱[概述 Azure SQL 資料庫託管實例資源限制](../sql-database/sql-database-managed-instance-resource-limits.md#service-tier-characteristics)。|請參閱[單個資料庫](../sql-database/sql-database-vcore-resource-limits-single-databases.md)和[彈性池](../sql-database/sql-database-vcore-resource-limits-elastic-pools.md)的資源限制。|超大規模是一種多層架構，具有多個級別的緩存。 有效的 IOPS 和輸送量將取決於工作負載。|
|可用性|1 個副本，沒有讀取縮放副本|3 個複本、1 個[讀取規模複本](sql-database-read-scale-out.md)、<br/>區域冗余高可用性 （HA）|1 個讀寫副本，外加 0-4[個讀寫副本](sql-database-read-scale-out.md)|
|備份|[讀取存取異地冗余存儲 （RA-GRS），](../storage/common/storage-designing-ha-apps-with-ragrs.md)7-35 天 （預設情況下為 7 天）|[RA-GRS，](../storage/common/storage-designing-ha-apps-with-ragrs.md)7-35 天 （預設為 7 天）|Azure 遠端存放中基於快照的備份。 還原時可使用這些快照集進行快速復原。 備份是暫態的，不會影響計算 I/O 性能。 還原速度很快，不是資料大小操作（需要幾分鐘而不是幾小時或幾天）。|
|記憶體內|不支援|支援|不支援|
|||


### <a name="choosing-a-service-tier"></a>選擇服務層級

有關為特定工作負荷選擇服務層的資訊，請參閱以下文章：

- [何時選擇泛型服務層](sql-database-service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [何時選擇關鍵業務服務層](sql-database-service-tier-business-critical.md#when-to-choose-this-service-tier)
- [何時選擇超大規模服務層](sql-database-service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>計算層

vCore 模型中的計算層選項包括預配和無伺服器計算層。


### <a name="provisioned-compute"></a>佈建計算

預配計算層提供與工作負載活動無關的連續預配的特定數量的計算資源，以及按每小時固定價格預配的計算金額的帳單。


### <a name="serverless-compute"></a>無伺服器計算

[無伺服器計算層](sql-database-serverless.md)根據工作負載活動自動縮放計算資源，並記錄每秒使用的計算量。



## <a name="hardware-generations"></a>硬體代數

vCore 模型中的硬體生成選項包括第 4/5 代、M 系列（預覽版）和 Fsv2 系列（預覽）。 硬體生成通常定義計算和記憶體限制以及影響工作負載性能的其他特徵。

### <a name="gen4gen5"></a>第4代/第5代

- Gen4/Gen5 硬體提供平衡的計算和記憶體資源，適用于 Fsv2 系列或 M 系列提供的記憶體較高、vCore 更高或更快的單個 vCore 要求的大多數資料庫工作負載。

有關第 4 代/第 5 代可用的區域，請參閱[第 4 代/第 5 代可用性](#gen4gen5-1)。

### <a name="fsv2-seriespreview"></a>Fsv2 系列（預覽版）

- Fsv2 系列是一種計算優化的硬體選項，可提供低 CPU 延遲和高時脈速度，適用于 CPU 要求最高的工作負載。
- 根據工作負載，Fsv2 系列可以比 Gen5 提供更多的 CPU 性能，72 vCore 大小可以提供比 Gen5 上的 80 vCore 更多的 CPU 性能。 
- 與其他硬體相比，Fsv2 每個 vCore 提供的記憶體和 tempdb 更少，因此對這些限制敏感的工作負載可能希望改為 Gen5 或 M 系列。  

Fsv2 系列僅在通用層中支援。  有關 Fsv2 系列可用的區域，請參閱[Fsv2 系列可用性](#fsv2-series)。


### <a name="m-seriespreview"></a>M 系列（預覽版）

- M 系列是一種記憶體優化的硬體選項，適用于需要比 Gen5 更高的記憶體和更高的計算限制的工作負載。
- M 系列提供每個 vCore 29 GB 和 128 vCore，這使得相對於 Gen5 的記憶體限制增加 8 倍到近 4 TB。

M 系列僅在業務關鍵型層中支援，不支援區域冗余。

要為訂閱和地區啟用 M 系列硬體，必須打開支援請求。 訂閱必須是付費產品/服務類型，包括即用即付或企業協定 （EA）。  如果支援請求獲得批准，則 M 系列的選擇和預配體驗遵循與其他硬體代相同的模式。 有關 M 系列可用的區域，請參閱[M 系列可用性](#m-series)。


### <a name="compute-and-memory-specifications"></a>計算和記憶體規格


|硬體世代  |計算  |記憶體  |
|:---------|:---------|:---------|
|Gen4     |- 英特爾 E5-2673 v3 （哈斯韋爾） 2.4 GHz 處理器<br>- 最多預配 24 個 vCore（1 個 vCore = 1 個物理內核）  |- 每 vCore 7 GB<br>- 預配高達 168 GB|
|Gen5     |**佈建計算**<br>- 英特爾 E5-2673 v4 （Broadwell） 2.3-GHz 和英特爾 SP-8160 （天湖）* 處理器<br>- 預配多達 80 個 vCore（1 個 vCore = 1 個超執行緒）<br><br>**無伺服器計算**<br>- 英特爾 E5-2673 v4 （Broadwell） 2.3-GHz 和英特爾 SP-8160 （天湖）* 處理器<br>- 自動縮放多達 16 個 vCore（1 個 vCore = 1 個超執行緒）|**佈建計算**<br>- 每個 vCore 5.1 GB<br>- 預配高達 408 GB<br><br>**無伺服器計算**<br>- 每個 vCore 自動縮放高達 24 GB<br>- 自動縮放，最大容量可達 48 GB|
|Fsv2 系列     |- 英特爾 Xeon 白金 8168 （天湖） 處理器<br>- 具有持續的所有核心渦輪時脈速度為 3.4 GHz 和最大單芯渦輪時脈速度為 3.7 GHz。<br>- 預配 72 vCore（1 個 vCore = 1 個超執行緒）|- 每個 vCore 1.9 GB<br>- 預配 136 GB|
|M 系列     |- 英特爾至強 E7-8890 v3 2.5 GHz 處理器<br>- 預配 128 vCore（1 個 vCore = 1 個超執行緒）|- 每個 vCore 29 GB<br>- 撥備3.7 TB|

\*在[sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)動態管理檢視中，使用英特爾 SP-8160（Skylake） 處理器的 Gen5 資料庫的硬體生成顯示為 Gen6。 無論處理器類型（Broadwell 或 Skylake），所有 Gen5 資料庫的資源限制都是相同的。

有關資源限制的詳細資訊，請參閱[單個資料庫的資源限制 （vCore）](sql-database-vcore-resource-limits-single-databases.md)或[彈性池的資源限制 （vCore）](sql-database-vcore-resource-limits-elastic-pools.md)。

### <a name="selecting-a-hardware-generation"></a>選擇硬體生成

在 Azure 門戶中，可以選擇 SQL 資料庫或池在創建時生成硬體，也可以更改現有 SQL 資料庫或池的硬體生成。

**在創建 SQL 資料庫或池時選擇硬體生成**

有關詳細資訊，請參閱創建[SQL 資料庫](sql-database-single-database-get-started.md)。

在 **"基本"** 選項卡上，在 **"計算 + 存儲**"部分中選擇 **"設定資料庫**"連結，然後選擇 **"更改配置**"連結：

  ![設定資料庫](media/sql-database-service-tiers-vcore/configure-sql-database.png)

選擇所需的硬體生成：

  ![選擇硬體](media/sql-database-service-tiers-vcore/select-hardware.png)


**更改現有 SQL 資料庫或池的硬體生成**

對於資料庫，在"概述"頁上，選擇 **"定價層**"連結：

  ![變更硬體](media/sql-database-service-tiers-vcore/change-hardware.png)

對於池，在"概述"頁上，選擇 **"配置**"。

按照步驟更改配置，然後按照前面的步驟中所述選擇硬體生成。

**在創建託管實例時選擇硬體生成**

有關詳細資訊，請參閱[創建託管實例](sql-database-managed-instance-get-started.md)。

在 **"基本"** 選項卡上，在 **"計算 + 存儲**"部分中選擇 **"設定資料庫**"連結，然後選擇所需的硬體生成：

  ![配置託管實例](media/sql-database-service-tiers-vcore/configure-managed-instance.png)
  
**更改現有託管實例的硬體生成**

# <a name="portal"></a>[入口網站](#tab/azure-portal)

從託管實例頁面，選擇"設置"部分下的 **"定價層**"連結

![更改託管實例硬體](media/sql-database-service-tiers-vcore/change-managed-instance-hardware.png)

在 **"定價層"** 頁上，您將能夠更改硬體生成，如前面的步驟所述。

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

使用下列 PowerShell 指令碼︰

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

有關詳細資訊，請選中["設置-AzSqlInstance"](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)命令。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用以下 CLI 命令：

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

有關詳細資訊，請查看[az sql mi 更新](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)命令。

---

### <a name="hardware-availability"></a>硬體可用性

#### <a name="gen4gen5"></a><a name="gen4gen5-1"></a>第4代/第5代

Gen4 硬體[正在逐步淘汰](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/)，不再可用於新部署。 所有新資料庫都必須部署在 Gen5 硬體上。

第 5 代在全球大多數地區都有售。

#### <a name="fsv2-series"></a>Fsv2 系列

Fsv2 系列在以下區域提供：澳大利亞中部、澳大利亞中部 2、澳大利亞東部、澳大利亞東南部、巴西南部、加拿大中部、東亞、東美國、法國中部、印度中部、印度西部、韓國中部、韓國南部、北部歐洲、南非北部、東南亞、英國南部、英國西部、西歐、西國2。


#### <a name="m-series"></a>M 系列

M 系列在以下區域提供：美國東部、北歐、西歐、美國西部 2。
M 系列在其他地區的可用性也可能有限。 您可以請求與此處列出的區域不同的區域，但可能無法在不同區域中實現。

要在訂閱中啟用 M 系列可用性，必須通過[提交新的支援請求](#create-a-support-request-to-enable-m-series)來請求訪問。


##### <a name="create-a-support-request-to-enable-m-series"></a>創建支援請求以啟用 M 系列： 

1. 在門戶中選擇 **"説明 + 支援**"。
2. 選取 [新增支援要求]****。

在 **"基礎知識"** 頁上，提供以下內容：

1. 針對 [問題類型]****，選取 [服務與訂用帳戶限制 (配額)]****。
2. 對於**訂閱**= 選擇訂閱以啟用 M 系列。
3. 對於**配額類型**，選擇**SQL 資料庫**。
4. 選擇 **"下一步**"轉到 **"詳細資訊"** 頁。

在 **"詳細資訊"** 頁上，提供以下內容：

1. 在 **"問題詳細資訊**"部分中，選擇"**提供詳細資訊**"連結。 
2. 對於**SQL 資料庫配額類型**，請選擇**M 系列**。
3. 對於**區域**，選擇要啟用 M 系列的區域。
    有關 M 系列可用的區域，請參閱[M 系列可用性](#m-series)。

已批准的支援請求通常在 5 個工作日內完成。


## <a name="next-steps"></a>後續步驟

- 要創建 SQL 資料庫，請參閱[使用 Azure 門戶創建 SQL 資料庫](sql-database-single-database-get-started.md)。
- 有關可用於單個資料庫的特定計算大小和存儲大小選擇，請參閱[單個資料庫的 SQL 資料庫 vCore 資源限制](sql-database-vcore-resource-limits-single-databases.md)。
- 有關彈性池可用的特定計算大小和存儲大小選擇，請參閱[彈性池的 SQL 資料庫 vCore 資源限制](sql-database-vcore-resource-limits-elastic-pools.md)。
- 有關定價詳細資訊，請參閱[Azure SQL 資料庫定價頁](https://azure.microsoft.com/pricing/details/sql-database/single/)。
