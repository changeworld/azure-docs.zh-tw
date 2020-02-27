---
title: 虛擬核心模型概觀
description: VCore 購買模型可讓您獨立調整計算和儲存體資源、符合內部部署效能，並將價格優化。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/27/2019
ms.openlocfilehash: 9b156193035d87472c462bae37e405e0317d8402
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650294"
---
# <a name="vcore-model-overview"></a>虛擬核心模型概觀

虛擬核心（vCore）模型提供數個優點：

- 較高的計算、記憶體、IO 和儲存體限制。
- 控制硬體世代，以更符合工作負載的計算和記憶體需求。
- [Azure Hybrid Benefit （AHB）](sql-database-azure-hybrid-benefit.md)和[保留實例（RI）](sql-database-reserved-capacity.md)的定價折扣。
- 提高計算能力的硬體詳細資料中有更大的透明度;有助於規劃從內部部署進行遷移。

## <a name="service-tiers"></a>服務層

VCore 模型中的服務層選項包括一般用途、業務關鍵和超大規模資料庫。 服務層通常會定義儲存體架構、空間和 IO 限制，以及與可用性和嚴重損壞修復相關的商務持續性選項。

||**一般用途**|**業務關鍵**|**超大規模資料庫**|
|---|---|---|---|
|適用對象|大部分的商業工作負載。 提供以預算為導向、平衡且可調整的計算和儲存體選項。 |使用數個隔離複本，為商務應用程式提供失敗的最高復原能力，並提供每個資料庫複本最高的 i/o 效能。|具有可高度擴充性的儲存體和讀取規模需求的大多數商務工作負載。  允許設定一個以上的隔離資料庫複本，以提供更高的失敗復原能力。 |
|儲存體|使用遠端存放。<br/>**單一資料庫和彈性**集區已布建計算：<br/>5 GB – 4 TB<br/>**無伺服器計算**：<br/>5 GB-3 TB<br/>**受控執行個體**： 32 GB-8 TB |使用本機 SSD 儲存體。<br/>**單一資料庫和彈性**集區已布建計算：<br/>5 GB – 4 TB<br/>**受控執行個體**：<br/>32 GB - 4 TB |視需要彈性自動成長儲存體。 最多可支援 100 TB 的儲存體。 會針對本機緩衝集區快取和本機資料儲存體使用本機 SSD 儲存體。 使用 Azure 遠端儲存體作為最終長期資料存放區。 |
|IOPS 和輸送量（大約）|**單一資料庫和彈性**集區：請參閱[單一資料庫](../sql-database/sql-database-vcore-resource-limits-single-databases.md)和[彈性](../sql-database/sql-database-vcore-resource-limits-elastic-pools.md)集區的資源限制。<br/>**受控執行個體**：請參閱[Azure SQL Database 受控實例資源限制的總覽](../sql-database/sql-database-managed-instance-resource-limits.md#service-tier-characteristics)。|請參閱[單一資料庫](../sql-database/sql-database-vcore-resource-limits-single-databases.md)和[彈性](../sql-database/sql-database-vcore-resource-limits-elastic-pools.md)集區的資源限制。|超大規模資料庫是多層式架構，在多個層級進行快取。 有效的 IOPS 和輸送量將視工作負載而定。|
|可用性|1個複本、無讀取規模複本|3 個複本、1 個[讀取規模複本](sql-database-read-scale-out.md)、<br/>區域冗余高可用性（HA）|1個讀寫複本，加上 0-4[個讀取規模複本](sql-database-read-scale-out.md)|
|備份|[讀取權限異地多餘儲存體（RA-GRS）](../storage/common/storage-designing-ha-apps-with-ragrs.md)，7-35 天（預設為7天）|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md)、7-35 天 (預設為 7 天)|Azure 遠端儲存體中以快照集為基礎的備份。 還原時可使用這些快照集進行快速復原。 備份是即時的，不會影響計算 i/o 效能。 還原速度很快，而且不是資料大小的作業（需要幾分鐘，而不是小時或數天）。|
|記憶體內|不支援|支援|不支援|
|||


### <a name="choosing-a-service-tier"></a>選擇服務層級

如需針對特定工作負載選取服務層級的詳細資訊，請參閱下列文章：

- [選擇一般用途服務層級的時機](sql-database-service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [選擇商務關鍵服務層級的時機](sql-database-service-tier-business-critical.md#when-to-choose-this-service-tier)
- [選擇超大規模資料庫服務層級的時機](sql-database-service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>計算層

VCore 模型中的計算層選項包括布建和無伺服器計算層。


### <a name="provisioned-compute"></a>已布建計算

布建的計算層提供了與工作負載活動無關的特定計算資源量，並以每小時固定價格的計算數量計費。


### <a name="serverless-compute"></a>無伺服器計算

[無伺服器計算層](sql-database-serverless.md)會根據工作負載活動自動調整計算資源，並以每秒使用的計算量來計費。



## <a name="hardware-generations"></a>硬體世代

VCore 模型中的硬體產生選項包括 Gen 4/5、M 系列（預覽）和 Fsv2 系列（預覽）。 硬體世代通常會定義計算和記憶體限制，以及影響工作負載效能的其他特性。

### <a name="gen4gen5"></a>第4代/第5代

- 第4代/第5代硬體提供平衡的計算和記憶體資源，適用于不具較高記憶體、較高 vCore 或更快速的單一 vCore 需求（如 Fsv2 系列或 M 系列所提供）的大部分資料庫工作負載。

如需第4代/第5代可用的區域，請參閱[第4代/第5代 availability](#gen4gen5-1)。

### <a name="fsv2-seriespreview"></a>Fsv2 系列（預覽）

- Fsv2 系列是一種計算優化硬體選項，可為最多 CPU 需求的工作負載提供低 CPU 延遲和高頻率速度。
- 視工作負載而定，Fsv2 系列可以提供比第5代更多的每個 vCore CPU 效能，而 72 vCore 大小可提供比虛擬核心上80第5代更多的 CPU 效能。 
- Fsv2 提供比其他硬體更少的記憶體和 tempdb，因此，這些限制的相關工作負載可能會想要改為考慮第5代或 M 系列。  

如需 Fsv2 系列的可用區域，請參閱[Fsv2 系列可用性](#fsv2-series)。


### <a name="m-seriespreview"></a>M 系列（預覽）

- M 系列是記憶體優化硬體選項，適用于需要更多記憶體和更高計算限制的工作負載，而不是由第5代所提供。
- M 系列提供每 vCore 29 GB 和128虛擬核心，這會將相對於8月第5代的記憶體限制增加到將近 4 TB。

若要啟用訂用帳戶和區域的 M 系列硬體，必須開啟支援要求。 訂用帳戶必須是付費供應專案類型，包括隨用隨付或 Enterprise 合約（EA）。  如果支援要求經過核准，則 M 系列的選擇和布建體驗會遵循與其他硬體世代相同的模式。 如需 M 系列可用的區域，請參閱[m 系列可用性](#m-series)。


### <a name="compute-and-memory-specifications"></a>計算和記憶體規格


|硬體世代  |計算  |記憶體  |
|:---------|:---------|:---------|
|第4代     |-Intel E5-2673 v3 （Haswell） 2.4 GHz 處理器<br>-最多可布建24虛擬核心（1 vCore = 1 個實體核心）  |-每個 vCore 7 GB<br>-布建最多 168 GB|
|Gen5     |**佈建計算**<br>-Intel E5-2673 v4 （Broadwell） 2.3-GHz 和 Intel SP-8160 （Skylake）處理器<br>-最多可布建80虛擬核心（1 vCore = 1 個超執行緒）<br><br>**無伺服器計算**<br>-Intel E5-2673 v4 （Broadwell） 2.3-GHz 和 Intel SP-8160 （Skylake）處理器<br>-自動相應增加至16個虛擬核心（1個 vCore = 1 個超執行緒）|**佈建計算**<br>-每個 vCore 5.1 GB<br>-布建最多 408 GB<br><br>**無伺服器計算**<br>-每個 vCore 自動相應增加至 24 GB<br>-自動調整至最多 48 GB 的最大值|
|Fsv2 系列     |-Intel 更強白金8168（SkyLake）處理器<br>-提供 3.4 GHz 的全部核心 turbo 主頻速度，以及最大的單一核心 turbo 頻率速度（3.7 GHz）。<br>-布建72虛擬核心（1 vCore = 1 個超執行緒）|-每個 vCore 1.9 GB<br>-布建 136 GB|
|M 系列     |-Intel E7-8890 v3 2.5 GHz 處理器<br>-布建128虛擬核心（1 vCore = 1 個超執行緒）|-每 vCore 29 GB<br>-布建 3.7 TB|


如需資源限制的詳細資訊，請參閱[單一資料庫的資源限制（vCore）](sql-database-vcore-resource-limits-single-databases.md)或彈性集區[的資源限制（vCore）](sql-database-vcore-resource-limits-elastic-pools.md)。

### <a name="selecting-a-hardware-generation"></a>選取硬體世代

在 Azure 入口網站中，您可以在建立時選取 SQL 資料庫或集區的硬體世代，也可以變更現有 SQL 資料庫或集區的硬體世代。

**若要在建立 SQL 資料庫或集區時選取硬體世代**

如需詳細資訊，請參閱[建立 SQL database](sql-database-single-database-get-started.md)。

在 [**基本**] 索引標籤上，選取 [**計算 + 儲存體**] 區段中的 [**設定資料庫**] 連結，然後選取 [**變更**設定] 連結：

  ![設定資料庫](media/sql-database-service-tiers-vcore/configure-sql-database.png)

選取所需的硬體世代：

  ![選取硬體](media/sql-database-service-tiers-vcore/select-hardware.png)


**變更現有 SQL 資料庫或集區的硬體世代**

針對資料庫，請在 [總覽] 頁面上選取 [**定價層**] 連結：

  ![變更硬體](media/sql-database-service-tiers-vcore/change-hardware.png)

針對集區，請在 [總覽] 頁面上選取 [**設定**]。

遵循下列步驟來變更設定，然後選取硬體世代，如先前的步驟中所述。

**若要在建立受控實例時選取硬體世代**

如需詳細資訊，請參閱[建立受控實例](sql-database-managed-instance-get-started.md)。

在 [**基本**] 索引標籤上，選取 [**計算 + 儲存體**] 區段中的 [**設定資料庫**] 連結，然後選取 [所需的硬體世代]：

  ![設定受控實例](media/sql-database-service-tiers-vcore/configure-managed-instance.png)
  
**變更現有受控實例的硬體世代**

# <a name="portal"></a>[入口網站](#tab/azure-portal)

從 [受控實例] 頁面，選取放置於 [設定] 區段底下的 [**定價層**] 連結

![變更受控實例硬體](media/sql-database-service-tiers-vcore/change-managed-instance-hardware.png)

在 [**定價層**] 頁面上，您將能夠如先前步驟所述變更硬體世代。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用下列 PowerShell 腳本：

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

如需詳細資訊，請參閱[Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)命令。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用下列 CLI 命令：

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

如需詳細資訊，請參閱[az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update) command。

---

### <a name="hardware-availability"></a>硬體可用性

#### <a name="gen4gen5-1"></a>第4代/第5代

第4代硬體即將[推出](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/)，而不再適用于新的部署。 所有新的資料庫都必須部署在第5代硬體上。

全球大多數區域都有提供第5代。

#### <a name="fsv2-series"></a>Fsv2 系列

Fsv2 系列適用于下欄區域：澳大利亞中部、澳大利亞中部2、澳大利亞東部、澳大利亞東南部、巴西南部、加拿大中部、東亞、美國東部、法國中部、印度中部、印度西部、韓國中部、南韓南部、北部歐洲、南非北部、東南亞、英國南部、英國西部、西歐、美國西部2。


#### <a name="m-series"></a>M 系列

M 系列可在下欄區域使用：美國東部、北歐、西歐、美國西部2。
M 系列在其他區域中可能也會有有限的可用性。 您可以要求與此處所列不同的區域，但可能無法在不同的區域中履行。

若要在訂用帳戶中啟用 M 系列的可用性，您必須[提出新的支援要求](#create-a-support-request-to-enable-m-series)來要求存取權。


##### <a name="create-a-support-request-to-enable-m-series"></a>建立支援要求以啟用 M 系列： 

1. 在入口網站中選取 [說明 **+ 支援**]。
2. 選取 [新增支援要求]。

在 [**基本**] 頁面上，提供下列資訊：

1. 針對 [問題類型]，選取 [服務與訂用帳戶限制 (配額)]。
2. 針對 [**訂**用帳戶 =] 選取要啟用 M 系列的訂用帳戶。
3. 針對 [**配額類型**]，選取 **[SQL database**]。
4. 選取 **[下一步]** 以移至 [**詳細資料**] 頁面。

在 [**詳細資料**] 頁面上，提供下列資訊：

1. 在 [**問題詳細資料**] 區段中，選取 [**提供詳細資料**] 連結。 
2. 針對 [ **SQL Database 配額類型**]，選取 [ **M 系列**]。
3. 針對 [**地區**]，選取要啟用 M 系列的區域。
    如需 M 系列可用的區域，請參閱[m 系列可用性](#m-series)。

核准的支援要求通常會在5個工作天內完成。


## <a name="next-steps"></a>後續步驟

- 若要建立 SQL database，請參閱[使用 Azure 入口網站建立 sql database](sql-database-single-database-get-started.md)。
- 如需單一資料庫可用的特定計算大小和儲存體大小選項，請參閱[SQL Database 單一資料庫的 vCore 為基礎的資源限制](sql-database-vcore-resource-limits-single-databases.md)。
- 如需彈性集區可用的特定計算大小和儲存體大小選項，請參閱彈性集區[SQL Database vCore 為基礎的資源限制](sql-database-vcore-resource-limits-elastic-pools.md)。
- 如需定價詳細資料，請參閱[Azure SQL Database 定價頁面](https://azure.microsoft.com/pricing/details/sql-database/single/)。
