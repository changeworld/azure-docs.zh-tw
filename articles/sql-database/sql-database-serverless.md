---
title: 無伺服器
description: 本文說明新的無伺服器計算層級，並將它與現有佈建計算層級進行比較
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: test
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein, carlrab
ms.date: 3/11/2020
ms.openlocfilehash: 5c36dbfbe63314ef97edfa3dfbaae34667db002d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268700"
---
# <a name="azure-sql-database-serverless"></a>Azure SQL 資料庫無伺服器

Azure SQL 資料庫無伺服器是單個資料庫的計算層，該資料庫根據工作負載需求和每秒使用的計算量的帳單自動縮放計算。 無伺服器計算層還會在僅計費存儲的非活動期間自動暫停資料庫，並在活動返回時自動復原資料庫。

## <a name="serverless-compute-tier"></a>無伺服器計算層級

單個資料庫的無伺服器計算層由計算自動縮放範圍和自動暫停延遲參數化。  這些參數的配置塑造了資料庫的性能體驗和計算成本。

![無伺服器計費](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance-configuration"></a>性能配置

- **最小 vCore**和**最大 vCore**是可配置的參數，用於定義資料庫可用的計算容量範圍。 記憶體和 IO 限制會與指定的虛擬核心範圍成比例。  
- **自動暫停延遲**是一個可配置參數，用於定義資料庫在自動暫停之前必須處於非活動狀態的時間段。 當下一個登錄或其他活動發生時，資料庫將自動復原。  或者，可以禁用自動暫停。

### <a name="cost"></a>成本

- 無伺服器資料庫的成本是計算成本和存儲成本的總和。
- 當計算用量在配置的最小值和最大值限制之間時，計算成本基於 vCore 和使用的記憶體。
- 當計算用量低於配置的最小限制時，計算成本基於配置的最小 vCore 和最小記憶體。
- 當資料庫暫停時，計算成本為零，並且只產生存儲成本。
- 存儲成本的確定方式與預配計算層中的存儲成本相同。

有關更多成本詳細資訊，請參閱[計費](sql-database-serverless.md#billing)。

## <a name="scenarios"></a>案例

無伺服器的性價針對採用間歇性、無法預測使用模式的單一資料庫最佳化，這些資料庫可接受在閒置使用期間之後，計算準備有些延遲。 相反，預配計算層是針對單個資料庫或彈性池中的多個資料庫優化的性價比，平均使用量較高，無法承受計算預熱的任何延遲。

### <a name="scenarios-well-suited-for-serverless-compute"></a>適合用於無伺服器計算的案例

- 具有間歇性、不可預知的使用模式的單一資料庫穿插于不活動期，並且隨著時間的推移平均計算利用率較低。
- 預配計算層中經常重新縮放的單個資料庫以及更願意將計算重新縮放委託給服務的客戶。
- 新的單個資料庫沒有使用歷史記錄，在 SQL 資料庫中部署之前難以估計計算大小調整或無法估計。

### <a name="scenarios-well-suited-for-provisioned-compute"></a>適合用於佈建計算的案例

- 具有更常規、更可預測的使用模式和隨時間增長的平均計算利用率的單個資料庫。
- 無法容忍以下情況造成效能妥協的資料庫：經常修剪記憶體或從暫停狀態自動繼續以致延遲。
- 具有間歇性、不可預知的使用模式的多個資料庫，這些模式可以合併到彈性池中，從而更好地優化性價比。

## <a name="comparison-with-provisioned-compute-tier"></a>與佈建計算層級比較

下表摘要說明無伺服器計算層級與佈建計算層級之間的差異：

| | **無伺服器計算** | **佈建計算** |
|:---|:---|:---|
|**資料庫使用模式**| 間歇性、不可預知的使用，隨著時間的推移，平均計算利用率較低。 |  更常規的使用模式，隨著時間的推移平均計算利用率較高，或使用彈性池的多個資料庫。|
| **效能管理投入量** |Lower|較高|
|**計算調整**|自動|手動|
|**計算回應性**|在非使用期間後降低|立即|
|**計費細微度**|每秒|每小時|

## <a name="purchasing-model-and-service-tier"></a>購買模型和服務層級

目前只有虛擬核心購買模型中第 5 代硬體的一般用途層級支援 SQL Database 無伺服器。

## <a name="autoscaling"></a>自動調整

### <a name="scaling-responsiveness"></a>調整回應性

通常，無伺服器資料庫在具有足夠容量的電腦上運行，以滿足在最大 vCores 值設置的限制內請求的任何計算量的資源需求，而不會中斷。 有時候，如果電腦無法在幾分鐘內滿足資源需求，負載平衡就會自動發生。 例如，如果資源需求為 4 vCore，但只有 2 個 vCore 可用，則在提供 4 個 vCore 之前，可能需要幾分鐘才能保持負載平衡。 捨棄連線後，除了在作業結束時的短暫期間，資料庫都會在負載平衡期間保持線上狀態。

### <a name="memory-management"></a>記憶體管理

回收無伺服器資料庫的記憶體的頻率高於預配計算資料庫。 此行為對於控制無伺服器成本非常重要，並會影響性能。

#### <a name="cache-reclamation"></a>緩存回收

與預配計算資料庫不同，當 CPU 或緩存利用率較低時，從無伺服器資料庫回收 SQL 緩存的記憶體。

- 當最近使用的緩存條目的總大小在一段時間內低於閾值時，緩存利用率被視為低。
- 觸發緩存回收時，目標緩存大小將逐漸減小到其以前大小的一小部分，並且僅在使用率仍然很低時繼續回收。
- 發生緩存回收時，選擇要驅逐的緩存條目的策略與記憶體壓力高時預配計算資料庫的選擇策略相同。
- 緩存大小永遠不會降低到可配置的最小 vCore 定義的最小記憶體限制以下。

在無伺服器和預配計算資料庫中，如果使用所有可用記憶體，可能會驅逐緩存條目。

#### <a name="cache-hydration"></a>緩存水化

SQL 緩存會隨著資料以與預配資料庫相同的速度和速度從磁片獲取而增長。 當資料庫繁忙時，允許緩存在最大記憶體限制下不受限制地增長。

## <a name="autopausing-and-autoresuming"></a>自動暫停和自動復原

### <a name="autopausing"></a>自動暫停

如果自動暫停延遲期間的所有以下條件都為 true，則觸發自動暫停：

- 工作階段數 = 0
- 在使用者池中運行的使用者工作負載的 CPU = 0

如果需要，提供一個選項以禁用自動暫停。

以下功能不支援自動暫停。  也就是說，如果使用以下任何功能，則無論資料庫不活動持續時間如何，資料庫都會保持線上狀態：

- 異地複製（活動異地複製和自動容錯移轉組）。
- 長期備份保留 （LTR）。
- SQL 資料同步中使用的同步資料庫。 與同步資料庫不同，中心資料庫和成員資料庫支援自動暫停。
- 彈性作業中使用的作業資料庫。

在部署某些需要資料庫處於線上狀態的服務更新期間，臨時會阻止自動暫停。  在這種情況下，在服務更新完成後，將再次允許自動暫停。

### <a name="autoresuming"></a>自動復原

如果任何以下條件在任何時候都為 true，則觸發自動復原：

|功能|自動繼續觸發程序|
|---|---|
|驗證和授權|登入|
|威脅偵測|在資料庫或伺服器級別啟用/禁用威脅檢測設置。<br>修改資料庫或伺服器級別的威脅檢測設置。|
|資料探索與分類|新增、修改、刪除或檢視敏感度標籤|
|稽核|檢視稽核記錄。<br>更新或查看稽核原則。|
|資料遮罩|新增、修改、刪除或檢視資料遮罩處理規則|
|透明資料加密|檢視透明資料加密的狀態|
|查詢 (效能) 資料存放區|修改或查看查詢存儲設置|
|自動微調|自動微調建議的應用和驗證，例如自動編製索引|
|資料庫複製|將資料庫創建為副本。<br>匯出到 BACPAC 檔。|
|SQL 資料同步|按照可設定的排程執行或定期執行中樞與成員資料庫之間的同步|
|修改特定資料庫中繼資料|添加新資料庫標記。<br>更改最大 vCore、最小 vCore 或自動暫停延遲。|
|SQL Server Management Studio (SSMS)|使用早于 18.1 的 SSMS 版本並為伺服器中的任何資料庫打開新的查詢視窗，將恢復同一伺服器中的任何自動暫停資料庫。 如果使用 SSMS 版本 18.1 或更高版本，則不會發生此行為。|

在部署某些需要資料庫處於線上狀態的服務更新期間，還會觸發自動復原。

### <a name="connectivity"></a>連線能力

如果無伺服器資料庫已暫停，則第一個登錄將恢復資料庫並返回一個錯誤，指出該資料庫在錯誤代碼 40613 中不可用。 資料庫一旦繼續，則必須重試登入來建立連線。 具有連線重試邏輯的資料庫用戶端應該不需要修改。

### <a name="latency"></a>Latency

自動復原和自動暫停無伺服器資料庫的延遲通常為 1 分鐘，自動復原為 1-10 分鐘。

### <a name="customer-managed-transparent-data-encryption-byok"></a>客戶託管的透明資料加密 （BYOK）

如果使用[客戶託管的透明資料加密](transparent-data-encryption-byok-azure-sql.md)（BYOK） 和無伺服器資料庫在金鑰刪除或吊銷時自動暫停，則資料庫將保持自動暫停狀態。  在這種情況下，下次嘗試恢復時，資料庫將保持暫停狀態，直到大約 10 分鐘或更少後其狀態轉換到無法訪問。  一旦資料庫變得無法訪問，恢復過程與預配計算資料庫的過程相同。  如果發生金鑰刪除或吊銷時無伺服器資料庫處於線上狀態，則資料庫在大約 10 分鐘或更少後也會變得不可訪問，其方式與預配計算資料庫的方式相同。

## <a name="onboarding-into-serverless-compute-tier"></a>進入無伺服器計算層

創建新資料庫或將現有資料庫移動到無伺服器計算層遵循與在預配計算層中創建新資料庫相同的模式，並涉及以下兩個步驟。

1. 指定服務目標名稱。 服務目標規定了服務層、硬體生成和最大 vCore。 下表顯示服務目標選項：

   |服務目標名稱|服務層|硬體世代|虛擬核心數上限|
   |---|---|---|---|
   |GP_S_Gen5_1|一般用途|Gen5|1|
   |GP_S_Gen5_2|一般用途|Gen5|2|
   |GP_S_Gen5_4|一般用途|Gen5|4|
   |GP_S_Gen5_6|一般用途|Gen5|6|
   |GP_S_Gen5_8|一般用途|Gen5|8|
   |GP_S_Gen5_10|一般用途|Gen5|10|
   |GP_S_Gen5_12|一般用途|Gen5|12|
   |GP_S_Gen5_14|一般用途|Gen5|14|
   |GP_S_Gen5_16|一般用途|Gen5|16|

2. 或者，指定最小 vCore 和自動暫停延遲以更改其預設值。 下表顯示這些參數的可用值。

   |參數|值選擇|預設值|
   |---|---|---|---|
   |最小 vCores|取決於配置的最大 vCore - 請參閱[資源限制](sql-database-vcore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5)。|0.5 個虛擬核心|
   |自動暫停延遲|最少：60分鐘（1小時）<br>最多：10080 分鐘（7 天）<br>增量： 60 分鐘<br>停用自動暫停：-1|60 Minuten|


### <a name="create-new-database-in-serverless-compute-tier"></a>在無伺服器計算層中創建新資料庫 

以下示例在無伺服器計算層中創建新資料庫。 這些示例顯式指定最小 vCore、最大 vCore 和自動暫停延遲。

#### <a name="use-azure-portal"></a>使用 Azure 入口網站

請參閱[快速入門：使用 Azure 門戶在 Azure SQL 資料庫中創建單個資料庫](sql-database-single-database-get-started.md)。


#### <a name="use-powershell"></a>使用 PowerShell

```powershell
New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -ComputeModel Serverless -Edition GeneralPurpose -ComputeGeneration Gen5 `
  -MinVcore 0.5 -MaxVcore 2 -AutoPauseDelayInMinutes 720
```
#### <a name="use-azure-cli"></a>使用 Azure CLI

```azurecli
az sql db create -g $resourceGroupName -s $serverName -n $databaseName `
  -e GeneralPurpose -f Gen5 -min-capacity 0.5 -c 2 --compute-model Serverless --auto-pause-delay 720
```


#### <a name="use-transact-sql-t-sql"></a>使用交易 SQL （T-SQL）

下面的示例在無伺服器計算層中創建一個新資料庫。

```sql
CREATE DATABASE testdb
( EDITION = 'GeneralPurpose', SERVICE_OBJECTIVE = 'GP_S_Gen5_1' ) ;
```

有關詳細資訊，請參閱[創建資料庫](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)。  

### <a name="move-database-from-provisioned-compute-tier-into-serverless-compute-tier"></a>將資料庫從預配計算層移動到無伺服器計算層

以下示例將資料庫從預配計算層移動到無伺服器計算層。 這些示例顯式指定最小 vCore、最大 vCore 和自動暫停延遲。

#### <a name="use-powershell"></a>使用 PowerShell


```powershell
Set-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -Edition GeneralPurpose -ComputeModel Serverless -ComputeGeneration Gen5 `
  -MinVcore 1 -MaxVcore 4 -AutoPauseDelayInMinutes 1440
```

#### <a name="use-azure-cli"></a>使用 Azure CLI

```azurecli
az sql db update -g $resourceGroupName -s $serverName -n $databaseName `
  --edition GeneralPurpose --min-capacity 1 --capacity 4 --family Gen5 --compute-model Serverless --auto-pause-delay 1440
```


#### <a name="use-transact-sql-t-sql"></a>使用交易 SQL （T-SQL）

下面的示例將資料庫從預配計算層移動到無伺服器計算層。

```sql
ALTER DATABASE testdb 
MODIFY ( SERVICE_OBJECTIVE = 'GP_S_Gen5_1') ;
```

有關詳細資訊，請參閱[ALTER 資料庫](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current)。

### <a name="move-database-from-serverless-compute-tier-into-provisioned-compute-tier"></a>將資料庫從無伺服器計算層移動到預配計算層

無伺服器資料庫可以移到佈建計算層級中，方法如同將佈建計算資料庫移到無伺服器計算層級中。

## <a name="modifying-serverless-configuration"></a>修改無伺服器配置

### <a name="use-powershell"></a>使用 PowerShell

使用 PowerShell 中的[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)命令使用`MaxVcore`、`MinVcore`和`AutoPauseDelayInMinutes`參數執行修改最大或最小 vCore 以及自動暫停延遲。

### <a name="use-azure-cli"></a>使用 Azure CLI

使用 Azure CLI 中的[az sql db 更新](/cli/azure/sql/db#az-sql-db-update)命令使用`capacity`、`min-capacity`和`auto-pause-delay`參數執行修改最大或最小 vCore 以及自動暫停延遲。


## <a name="monitoring"></a>監視

### <a name="resources-used-and-billed"></a>使用和計費的資源

無伺服器資料庫的資源由應用包、SQL 實例和使用者資源池實體封裝。

#### <a name="app-package"></a>應用程式套件

不論資料庫是在無伺服器或佈建計算層級中，應用程式套件都是資料庫的最外層資源管理界限。 應用程式套件包含 SQL 執行個體和外部服務，其可一起界定 SQL Database 中資料庫所用的所有使用者和系統資源。 外部服務的範例包括 R 和全文檢索搜尋。 SQL 執行個體通常會支配整個應用程式套件的整體資源使用率。

#### <a name="user-resource-pool"></a>使用者資源集區

不論資料庫是在無伺服器或佈建計算層級中，使用者資源集區都是資料庫的最內層資源管理界限。 使用者資源池為 DDL 查詢（如創建和 ALTER）和 DML 查詢（如 SELECT、插入、更新和刪除）生成的使用者工作負載範圍。 這些查詢通常代表應用程式套件內很大的使用率比例。

### <a name="metrics"></a>計量

下表列出了用於監視無伺服器資料庫的應用包和使用者池的資源使用方式的指標：

|單位|計量|描述|單位|
|---|---|---|---|
|應用程式套件|app_cpu_percent|應用程式所使用的虛擬核心百分比，相對於應用程式所允許的最大虛擬核心數。|百分比|
|應用程式套件|app_cpu_billed|在報告期間內針對應用程式計費的計算數量。 在這段期間所支付的金額為此計量與虛擬核心單價的乘積。 <br><br>彙總一段時間內每秒使用的最大 CPU 與記憶體，即可判斷此計量的值。 如果使用的數量小於依照最小虛擬核心數與最小記憶體所設定的最小佈建數量，就會收取最小佈建數量的費用。為了比較 CPU 與記憶體以供計費用途，記憶體會依每個虛擬核心 3 GB 重新調整記憶體量，藉此規範成虛擬核心單位。|虛擬核心秒數|
|應用程式套件|app_memory_percent|應用程式所使用的記憶體百分比，相對於應用程式所允許的最大記憶體。|百分比|
|使用者集區|cpu_percent|使用者工作負載所使用的虛擬核心百分比，相對於使用者工作負載所允許的最大虛擬核心數。|百分比|
|使用者集區|data_IO_percent|使用者工作負載所使用的資料 IOPS 百分比，相對於使用者工作負載所允許的最大資料 IOPS。|百分比|
|使用者集區|log_IO_percent|使用者工作負載所使用的記錄 MB/s 百分比，相對於使用者工作負載所允許的最大記錄 MB/s。|百分比|
|使用者集區|workers_percent|使用者工作負載所使用的背景工作角色百分比，相對於使用者工作負載所允許的最大背景工作角色數。|百分比|
|使用者集區|sessions_percent|使用者工作負載所使用的工作階段百分比，相對於使用者工作負載所允許的最大工作階段數。|百分比|

### <a name="pause-and-resume-status"></a>暫停與繼續狀態

在 Azure 入口網站中，資料庫狀態會顯示於伺服器的 [概觀] 窗格，其中列出它所包含的資料庫。 資料庫狀態也會顯示在資料庫的 [概觀] 窗格中。

使用以下命令查詢資料庫的暫停和恢復狀態：

#### <a name="use-powershell"></a>使用 PowerShell

```powershell
Get-AzSqlDatabase -ResourceGroupName $resourcegroupname -ServerName $servername -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

#### <a name="use-azure-cli"></a>使用 Azure CLI

```azurecli
az sql db show --name $databasename --resource-group $resourcegroupname --server $servername --query 'status' -o json
```


## <a name="resource-limits"></a>資源限制

有關資源限制，請參閱[無伺服器計算層](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5)。

## <a name="billing"></a>計費

計算數量的計費方式為每秒使用的最大 CPU 與記憶體。 若使用的 CPU 與使用的記憶體數量小於各自的最小佈建數量，就會收取佈建數量的費用。 為了比較 CPU 與記憶體以供計費用途，記憶體會依每個虛擬核心 3 GB 重新調整記憶體量，藉此規範成虛擬核心單位。

- **資源計費**：CPU 和記憶體
- **計費金額**： vCore 單價 = 最大值 （最小 vCore， 使用的 vCore， 最小記憶體 GB = 1/3， 使用的記憶體 GB = 1/3） 
- **計費頻率**：每秒

vCore 單價是每秒每 vCore 的成本。 如需指定區域中的特定單位價格，請參閱 [Azure SQL Database 定價頁面](https://azure.microsoft.com/pricing/details/sql-database/single/)。

計費的計算數量會依下列度量公開：

- **計量**：app_cpu_billed (虛擬核心秒數)
- **定義**：最大值 (最小虛擬核心數, 使用的虛擬核心, 最小記憶體 GB * 1/3, 使用的記憶體 GB * 1/3)
- **報告頻率**：每分鐘

此數量會每秒計算，並彙總超過 1 分鐘。

考慮配置了 1 分鐘 vCore 和 4 個最大 vCore 的無伺服器資料庫。  這對應于大約 3 GB 分鐘記憶體和 12 GB 的最大記憶體。  假設自動暫停延遲設置為 6 小時，資料庫工作負載在 24 小時前 2 小時處於活動狀態，否則處於非活動狀態。    

在這種情況下，資料庫會按前 8 小時的計算和存儲計費。  即使資料庫在第二小時後開始處於非活動狀態，但根據資料庫連線時預配的最小計算，仍按後續 6 小時內計費計算。  在資料庫暫停期間，僅在 24 小時期間的剩餘時間內對存儲計費。

更確切地說，此示例中的計算帳單的計算方式如下：

|時間間隔|vCore 每秒使用|每秒使用的 GB|計費的計算維度|vCore 秒計費時間間隔|
|---|---|---|---|---|
|0:00-1:00|4|9|使用的 vCore|4 個 vCore = 3600 秒 = 14400 vCore 秒|
|1:00-2:00|1|12|使用的記憶體|12 GB = 1/3 = 3600 秒 = 14400 vCore 秒|
|2:00-8:00|0|0|配置的最小記憶體|3 GB = 1/3 = 21600 秒 = 21600 vCore 秒|
|8:00-24:00|0|0|暫停時未計費|0 vCore 秒|
|超過 24 小時計費的 vCore 總秒數||||50400 vCore 秒|

假設計算單價為 $0.000145/vCore/秒。  然後，此 24 小時期間計費的計算是計算單價和 vCore 秒計費的產品： $0.000145/vCore/秒 = 50400 vCore 秒 = $7.31

### <a name="azure-hybrid-benefit-and-reserved-capacity"></a>Azure 混合權益和預留容量

Azure 混合權益 （AHB） 和預留容量折扣不適用於無伺服器計算層。

## <a name="available-regions"></a>可用區域

除以下區域外，無伺服器計算層可在全球提供：中國東部、華北、德國中部、德國東北部、英國北部、英國南部 2、美國中西部和美國政府中部 （Iowa）。

## <a name="next-steps"></a>後續步驟

- 要開始，請參閱[快速入門：使用 Azure 門戶在 Azure SQL 資料庫中創建單個資料庫](sql-database-single-database-get-started.md)。
- 如需資源限制，請參閱[無伺服器計算層級資源限制](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5)。
