---
title: vCore 購買模型總覽
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: VCore 購買模型可讓您獨立調整計算和儲存體資源、符合內部部署效能，並將 Azure SQL Database 和 Azure SQL 受控執行個體的價格優化。
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 01/15/2021
ms.openlocfilehash: 6589f451d4db8f2ed77ce70a2bdfa9d76927c1e2
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251211"
---
# <a name="vcore-model-overview---azure-sql-database-and-azure-sql-managed-instance"></a>vCore 模型總覽-Azure SQL Database 和 Azure SQL 受控執行個體 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database 和 Azure SQL 受控執行個體所使用的虛擬核心 (vCore) 購買模型可提供數個優點：

- 更高的計算、記憶體、i/o 和儲存體限制。
- 控制硬體世代，以更符合工作負載的計算和記憶體需求。
- [Azure Hybrid Benefit (AHB) ](../azure-hybrid-benefit.md)和[保留實例 (RI) ](reserved-capacity-overview.md)的定價折扣。
- 更清楚的硬體詳細資料，可加速計算，以促進從內部部署部署進行遷移的規劃。

## <a name="service-tiers"></a>服務層

VCore 模型中的服務層選項包括一般用途、業務關鍵和超大規模。 服務層通常會定義與可用性和嚴重損壞修復相關的儲存體架構、空間和 i/o 限制，以及商務持續性選項。

|-|**一般用途**|**業務關鍵**|**超大規模資料庫**|
|---|---|---|---|
|適用對象|大部分的商業工作負載。 提供以預算為導向、平衡且可調整的計算和儲存體選項。 |使用數個隔離的複本，為商務應用程式提供失敗的最高復原能力，並為每個資料庫複本提供最高的 i/o 效能。|大部分具有可高度擴充之儲存體和讀取規模需求的商務工作負載。  藉由允許設定一個以上的獨立資料庫複本，提供更高的失敗復原能力。 |
|儲存體|使用遠端儲存體。<br/>**SQL Database 布建的計算**：<br/>5 GB – 4 TB<br/>**無伺服器計算**：<br/>5 GB-3 TB<br/>**SQL 受控執行個體**： 32 GB-8 TB |使用本機 SSD 儲存體。<br/>**SQL Database 布建的計算**：<br/>5 GB – 4 TB<br/>**SQL 受控執行個體**：<br/>32 GB - 4 TB |視需要彈性地自動成長儲存體。 最多可支援 100 TB 的儲存體。 使用本機 SSD 儲存體進行本機緩衝集區快取和本機資料儲存。 使用 Azure 遠端儲存體作為最終長期資料存放區。 |
|IOPS 和輸送量 (大約) |**SQL Database**：請參閱 [單一資料庫](resource-limits-vcore-single-databases.md) 和 [彈性](resource-limits-vcore-elastic-pools.md)集區的資源限制。<br/>**SQL 受控執行個體**：請參閱 [總覽 Azure SQL 受控執行個體資源限制](../managed-instance/resource-limits.md#service-tier-characteristics)。|查看 [單一資料庫](resource-limits-vcore-single-databases.md) 和 [彈性](resource-limits-vcore-elastic-pools.md)集區的資源限制。|超大規模是多層式架構，可在多個層級進行快取。 有效的 IOPS 和輸送量會視工作負載而定。|
|可用性|1個複本，沒有讀取規模複本|3 個複本、1 個[讀取規模複本](read-scale-out.md)、<br/>區域冗余高可用性 (HA) |1個讀寫複本，加上0-4 個 [讀取規模複本](read-scale-out.md)|
|備份|[讀取權限異地冗余儲存體 (GRS) ](../../storage/common/geo-redundant-design.md)，7-35 天 (預設為7天) |[GRS](../..//storage/common/geo-redundant-design.md)、7-35 天 (預設為7天) |Azure 遠端儲存體中以快照集為基礎的備份。 還原時可使用這些快照集進行快速復原。 備份是瞬間的，不會影響計算 i/o 效能。 還原速度很快，而且不是資料大小的作業 (需要幾分鐘的時間，而不是) 。|
|記憶體內|不支援|支援|不支援|
|||


### <a name="choosing-a-service-tier"></a>選擇服務層級

如需為您的特定工作負載選取服務層級的詳細資訊，請參閱下列文章：

- [選擇一般用途服務層級的時機](service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [選擇商務關鍵服務層級的時機](service-tier-business-critical.md#when-to-choose-this-service-tier)
- [選擇超大規模服務層級的時機](service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>計算層

VCore 模型中的計算層選項包括布建和無伺服器計算層級。


### <a name="provisioned-compute"></a>佈建計算

布建的計算層提供了特定數量的計算資源，這些資源會隨著工作負載活動而持續布建，並針對每小時固定價格布建的計算數量計費。


### <a name="serverless-compute"></a>無伺服器計算

[無伺服器計算層](serverless-tier-overview.md)會根據工作負載活動自動調整計算資源，並依每秒使用的計算量來計費。



## <a name="hardware-generations"></a>硬體世代

VCore 模型中的硬體世代選項包括 Gen 4/5、M 系列、Fsv2 系列和 DC 系列。 硬體世代通常會定義計算和記憶體限制，以及影響工作負載效能的其他特性。

### <a name="gen4gen5"></a>第4代/第5代

- 第4代/第5代硬體提供平衡的計算和記憶體資源，適用于大部分的資料庫工作負載，這些工作負載沒有較高的記憶體、更高的 vCore，或是 Fsv2 系列或 M 系列所提供的單一 vCore 需求更快。

如需第4代/第5代的可用區域，請參閱 [第4代/第5代可用性](#gen4gen5-1)。

### <a name="fsv2-series"></a>Fsv2 系列

- Fsv2 系列是一種計算優化的硬體選項，可針對 CPU 需求最高的工作負載提供低 CPU 延遲和高頻率速度。
- 根據工作負載，Fsv2 系列可提供比第5代更多的每個 vCore CPU 效能，而 72 vCore 大小可提供比虛擬核心上的80第5代更高的 CPU 效能。 
- Fsv2 比其他硬體提供的每個 vCore 較少的記憶體和 tempdb，因此對這些限制敏感的工作負載可能會想要改用第5代或 M 系列。  

只有一般用途層才支援 Fsv2 系列。 如需 Fsv2 系列的可用區域，請參閱 [Fsv2 系列可用性](#fsv2-series-1)。

### <a name="m-series"></a>M 系列

- M 系列是記憶體優化的硬體選項，適用于需要更多記憶體和更高計算限制的工作負載，但第5代不提供。
- M 系列提供每個 vCore 29 GB 和最高128虛擬核心，這會將相對於 8 GB 的記憶體限制增加至將近 4 TB。

只有在業務關鍵層中才支援 M 系列，且不支援區域冗余。  如需 M 系列的可用區域，請參閱 [m 系列可用性](#m-series-1)。

#### <a name="azure-offer-types-supported-by-m-series"></a>M 系列支援的 Azure 供應專案類型

若要存取 M 系列，訂用帳戶必須是付費供應專案類型，包括隨用隨付或 Enterprise 合約 (EA) 。  如需 M 系列所支援之 Azure 供應專案類型的完整清單，請參閱目前的供應專案， [但不含消費限制](https://azure.microsoft.com/support/legal/offer-details)。

<!--
To enable M-series hardware for a subscription and region, a support request must be opened. The subscription must be a paid offer type including Pay-As-You-Go or Enterprise Agreement (EA).  If the support request is approved, then the selection and provisioning experience of M-series follows the same pattern as for other hardware generations. For regions where M-series is available, see [M-series availability](#m-series).
-->

### <a name="dc-series"></a>DC 系列

> [!NOTE]
> DC 系列目前處於 **公開預覽** 狀態。

- DC 系列硬體使用具有軟體防護延伸模組的 Intel 處理器 (Intel SGX) 技術。
- [使用安全記憶體保護區](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-enclaves)（不支援其他硬體設定）的 ALWAYS ENCRYPTED 需要 DC 系列。
- DC 系列是針對處理敏感性資料的工作負載所設計，而且需要機密查詢處理功能（由 Always Encrypted 與安全記憶體保護區提供）。
- DC 系列硬體提供平衡的計算和記憶體資源。

只有布建的計算 (不支援無伺服器的) ，且不支援區域冗余。 如需 DC 系列的可用區域，請參閱 [dc 系列的可用性](#dc-series-1)。

#### <a name="azure-offer-types-supported-by-dc-series"></a>DC 系列支援的 Azure 供應專案類型

若要存取 DC 系列，訂用帳戶必須是付費供應專案類型，包括隨用隨付或 Enterprise 合約 (EA) 。  如需 DC 系列所支援的 Azure 供應專案類型完整清單，請參閱目前的供應專案， [但不含消費限制](https://azure.microsoft.com/support/legal/offer-details)。

### <a name="compute-and-memory-specifications"></a>計算和記憶體規格


|硬體世代  |計算  |記憶體  |
|:---------|:---------|:---------|
|Gen4     |-Intel® E5-2673 v3 (Haswell) 2.4 GHz 處理器<br>-最多可布建至24個虛擬核心 (1 vCore = 1 個實體核心)   |-每個 vCore 7 GB<br>-布建高達 168 GB|
|Gen5     |**佈建計算**<br>-Intel® E5-2673 v4 (Broadwell) 2.3-GHz、Intel® SP-8160 (Skylake) \* 和 intel® 8272CL (Cascade Lake) 2.5 GHz \* 處理器<br>-布建高達80虛擬核心 (1 vCore = 1 超執行緒) <br><br>**無伺服器計算**<br>-Intel® E5-2673 v4 (Broadwell) 2.3-GHz 和 Intel® SP-8160 (Skylake) * 處理器<br>-自動擴大至40虛擬核心 (1 vCore = 1 超執行緒) |**佈建計算**<br>-每個 vCore 5.1 GB<br>-布建高達 408 GB<br><br>**無伺服器計算**<br>-自動調整為每個 vCore 24 GB<br>-自動調整高達 120 GB 上限|
|Fsv2 系列     |-Intel® 8168 (Skylake) 處理器<br>-採用最大的核心 turbo 頻率速度 3.4 GHz，以及最大單一核心 turbo 頻率速度 3.7 g h z。<br>-布建高達72虛擬核心 (1 vCore = 1 超執行緒) |-每個 vCore 1.9 GB<br>-布建高達 136 GB|
|M 系列     |-Intel® E7-8890 v3 2.5 GHz 和 Intel® 8280M 2.7 GHz (Cascade Lake) 處理器<br>-布建高達128虛擬核心 (1 vCore = 1 超執行緒) |-每個 vCore 29 GB<br>-布建高達 3.7 TB|
|DC 系列     | -Intel 2288G 處理器<br>-搭載 Intel Software Guard 延伸模組 (Intel SGX) # A2<br>-布建最多8個虛擬核心 (1 vCore = 1 個實體核心)  | 每個 vCore 4.5 GB |

\* 在 [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) 動態管理檢視中，使用 INTEL® SP-8160 (Skylake) 處理器之資料庫的硬體世代會顯示為 Gen6，而使用 INTEL® 8272CL (Cascade Lake) 的資料庫產生硬體時，會顯示為 Gen7。 無論處理器類型 (Broadwell、Skylake 或 Cascade Lake) ，所有第5代資料庫的資源限制都相同。

如需資源限制的詳細資訊，請參閱 [單一資料庫的資源限制 (vCore) ](resource-limits-vcore-single-databases.md)，或彈性集區 [的資源限制 (vCore) ](resource-limits-vcore-elastic-pools.md)。

### <a name="selecting-a-hardware-generation"></a>選取硬體世代

在 Azure 入口網站中，您可以在建立時選取 SQL Database 資料庫或集區的硬體世代，也可以變更現有資料庫或集區的硬體世代。

**若要在建立 SQL Database 或集區時選取硬體世代**

如需詳細資訊，請參閱 [建立 SQL Database](single-database-create-quickstart.md)。

在 [**基本**] 索引標籤上，選取 [**計算 + 儲存體**] 區段中的 [**設定資料庫**] 連結，然後選取 [**變更** 設定] 連結：

  ![設定資料庫](./media/service-tiers-vcore/configure-sql-database.png)

選取所需的硬體世代：

  ![選取硬體](./media/service-tiers-vcore/select-hardware.png)


**變更現有 SQL Database 或集區的硬體世代**

針對資料庫，請在 [總覽] 頁面上選取 [ **定價層** ] 連結：

  ![變更硬體](./media/service-tiers-vcore/change-hardware.png)

針對集區，請在 [總覽] 頁面上選取 [ **設定**]。

依照步驟來變更設定，然後選取硬體世代（如先前步驟中所述）。

**若要在建立 SQL 受控執行個體時選取硬體世代**

如需詳細資訊，請參閱 [建立 SQL 受控執行個體](../managed-instance/instance-create-quickstart.md)。

在 [**基本**] 索引標籤上，選取 [**計算 + 儲存體**] 區段中的 [**設定資料庫**] 連結，然後選取所需的硬體世代：

  ![設定 SQL 受控執行個體](./media/service-tiers-vcore/configure-managed-instance.png)
  
**變更現有 SQL 受控執行個體的硬體世代**

# <a name="the-azure-portal"></a>[Azure 入口網站](#tab/azure-portal)

從 [SQL 受控執行個體] 頁面的 [設定] 區段下，選取 [ **定價層** ] 連結

![變更 SQL 受控執行個體硬體](./media/service-tiers-vcore/change-managed-instance-hardware.png)

在 [定價層] 頁面上，您將能夠如先前步驟中所述變更硬體世代。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用下列 PowerShell 指令碼︰

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

如需詳細資訊，請參閱 [new-azsqlinstance](/powershell/module/az.sql/set-azsqlinstance) 命令。

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用下列 CLI 命令：

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

如需詳細資料，請查看 [az sql mi update](/cli/azure/sql/mi#az-sql-mi-update) 命令。

---

### <a name="hardware-availability"></a>硬體可用性

#### <a name="gen4gen5"></a><a name="gen4gen5-1"></a> 第4代/第5代

第4代硬體即將 [推出](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) ，且不再適用于新的部署。 所有新的資料庫都必須部署在第5代硬體上。

全球大部分區域都有提供第5代。

#### <a name="fsv2-series"></a>Fsv2 系列

Fsv2 系列適用于下欄區域：澳大利亞中部、澳大利亞中部2、澳大利亞東部、澳大利亞東南部、巴西南部、加拿大中部、東亞、美國東部、法國中部、印度中部、韓國中部、南韓南部、歐洲北部、南非北部、東南亞、英國南部、英國西部、西歐、美國西部2。


#### <a name="m-series"></a>M 系列

M 系列適用于下欄區域：美國東部、北歐、西歐、美國西部2。
<!--
M-series may also have limited availability in additional regions. You can request a different region than listed here, but fulfillment in a different region may not be possible.

To enable M-series availability in a subscription, access must be requested by [filing a new support request](#create-a-support-request-to-enable-m-series).


##### Create a support request to enable M-series: 

1. Select **Help + support** in the portal.
2. Select **New support request**.

On the **Basics** page, provide the following:

1. For **Issue type**, select **Service and subscription limits (quotas)**.
2. For **Subscription** = select the subscription to enable M-series.
3. For **Quota type**, select **SQL database**.
4. Select **Next** to go to the **Details** page.

On the **Details** page, provide the following:

1. In the **PROBLEM DETAILS** section select the **Provide details** link. 
2. For **SQL Database quota type** select **M-series**.
3. For **Region**, select the region to enable M-series.
    For regions where M-series is available, see [M-series availability](#m-series).

Approved support requests are typically fulfilled within 5 business days.
-->

#### <a name="dc-series"></a>DC 系列

> [!NOTE]
> DC 系列目前處於 **公開預覽** 狀態。

以下區域提供 DC 系列：加拿大中部、加拿大東部、美國東部、北歐、英國南部、西歐和美國西部。

如果您在目前不支援的區域中需要 DC 系列，請遵循[Azure SQL Database 和 SQL 受控執行個體的要求配額增加](quota-increase-request.md)中的指示來[提交支援票證](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>後續步驟

若要開始使用，請參閱： 
- [使用 Azure 入口網站建立 SQL Database](single-database-create-quickstart.md)
- [使用 Azure 入口網站建立 SQL 受控執行個體](../managed-instance/instance-create-quickstart.md)

如需定價詳細資料，請參閱 [Azure SQL Database 定價頁面](https://azure.microsoft.com/pricing/details/sql-database/single/)。

如需一般用途和業務關鍵服務層級中可用的特定計算和儲存體大小的詳細資訊，請參閱：

- [Azure SQL Database 的 vCore 為基礎的資源限制](resource-limits-vcore-single-databases.md)。
- 集區[Azure SQL Database 的 vCore 型資源限制](resource-limits-vcore-elastic-pools.md)。
- [適用于 AZURE SQL 受控執行個體的 vCore 為基礎的資源限制](../managed-instance/resource-limits.md)。