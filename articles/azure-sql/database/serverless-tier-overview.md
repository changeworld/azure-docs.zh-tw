---
title: 無伺服器計算層級
description: 本文說明新的無伺服器計算層，並將其與現有的 Azure SQL Database 的已布建計算層進行比較。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: test sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 12/8/2020
ms.openlocfilehash: b0d599b7d52d8a0e93f16761d1983ad25fa45c61
ms.sourcegitcommit: e0ec3c06206ebd79195d12009fd21349de4a995d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97687410"
---
# <a name="azure-sql-database-serverless"></a>Azure SQL Database 無伺服器
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

無伺服器是 Azure SQL Database 中單一資料庫的計算層，可根據工作負載需求和每秒使用的計算量，自動調整計算規模。 無伺服器計算層也會在只有儲存體仍在計費的資料庫非使用中期間自動暫停資料庫，並在有活動傳回時自動繼續執行資料庫。

## <a name="serverless-compute-tier"></a>無伺服器計算層級

Azure SQL Database 中單一資料庫的無伺服器計算層是由計算自動調整範圍和自動暫停延遲參數化。 這些參數的設定會以資料庫效能體驗和計算成本為圖形。

![無伺服器計費](./media/serverless-tier-overview/serverless-billing.png)

### <a name="performance-configuration"></a>效能設定

- **最小虛擬核心** 和 **最大虛擬核心** 是可設定的參數，這些參數會定義資料庫可用的計算容量範圍。 記憶體和 IO 限制會與指定的虛擬核心範圍成比例。  
- **自動暫停延遲** 是可設定的參數，其定義資料庫必須處於非使用狀態一段時間後，才會自動暫停。 當下次登入或其他活動發生時，資料庫就會自動繼續。  或者，您也可以停用 autopausing。

### <a name="cost"></a>Cost

- 無伺服器資料庫的成本是計算成本和儲存體成本的總和。
- 當計算使用量介於設定的最小和最大限制之間時，計算成本會以 vCore 和使用的記憶體為基礎。
- 當計算使用量低於設定的最小限制時，計算成本會以所設定的最小虛擬核心和最小記憶體為基礎。
- 當資料庫暫停時，計算成本為零，而且只會產生儲存體成本。
- 儲存體成本的決定方式與布建計算層中的相同。

如需更多成本的詳細資訊，請參閱 [帳單](serverless-tier-overview.md#billing)。

## <a name="scenarios"></a>案例

無伺服器的性價針對採用間歇性、無法預測使用模式的單一資料庫最佳化，這些資料庫可接受在閒置使用期間之後，計算準備有些延遲。 相反地，佈建計算層級的性價已針對平均使用量較高的單一或彈性集區中多個資料庫最佳化，這些資料庫無法接受計算準備有任何延遲。

### <a name="scenarios-well-suited-for-serverless-compute"></a>適合用於無伺服器計算的案例

- 具有間歇、無法預測的使用模式的單一資料庫，會隨著時間而分散在非使用狀態和較低的平均計算使用率。
- 布建的計算層中的單一資料庫經常重新調整，以及偏好將計算重新調整委派給服務的客戶。
- 新的單一資料庫，沒有使用量歷程記錄，在 SQL Database 中部署之前，計算大小很難或無法預估。

### <a name="scenarios-well-suited-for-provisioned-compute"></a>適合用於佈建計算的案例

- 單一資料庫具有更定期、可預測的使用模式，以及一段時間更高的平均計算使用率。
- 無法容忍以下情況造成效能妥協的資料庫：經常修剪記憶體或從暫停狀態自動繼續以致延遲。
- 具有間歇、無法預測使用模式的多個資料庫，可以合併到彈性集區，以獲得更好的價格效能優化。

## <a name="comparison-with-provisioned-compute-tier"></a>與佈建計算層級比較

下表摘要說明無伺服器計算層級與佈建計算層級之間的差異：

| | **無伺服器計算** | **佈建計算** |
|:---|:---|:---|
|**資料庫使用模式**| 以較低的平均計算使用率隨時間變化的間歇性、無法預測的使用量。 | 更一般的使用模式，其平均計算使用率隨時間更高，或使用彈性集區的多個資料庫。|
| **效能管理投入量** |較低|較高|
|**計算調整**|自動|手動|
|**計算回應性**|在非使用期間後降低|立即|
|**計費細微度**|每秒|每小時|

## <a name="purchasing-model-and-service-tier"></a>購買模型和服務層級

目前只有虛擬核心購買模型中第 5 代硬體的一般用途層級支援 SQL Database 無伺服器。

## <a name="autoscaling"></a>自動調整

### <a name="scaling-responsiveness"></a>調整回應性

一般而言，無伺服器資料庫會在具有足夠容量的機器上執行，以滿足資源需求，而不會中斷 [max 虛擬核心] 值所設定的限制內所要求的任何計算量。 有時候，如果電腦無法在幾分鐘內滿足資源需求，負載平衡就會自動發生。 例如，如果資源需求是4虛擬核心，但只有2個虛擬核心可用，則在提供4個虛擬核心之前，最多可能需要幾分鐘的時間來進行負載平衡。 捨棄連線後，除了在作業結束時的短暫期間，資料庫都會在負載平衡期間保持線上狀態。

### <a name="memory-management"></a>記憶體管理

無伺服器資料庫的記憶體回收比布建的計算資料庫更頻繁。 此行為對於控制無伺服器的成本而言很重要，而且可能會影響效能。

#### <a name="cache-reclamation"></a>快取回收

不同于已布建的計算資料庫，當 CPU 或主動快取使用率很低時，從 SQL 快取的記憶體會從無伺服器資料庫中回收。

- 當最近使用的快取專案大小總計低於閾值一段時間時，使用中的快取使用率會被視為低。
- 觸發快取回收時，會以累加方式將目標快取大小縮減為其先前大小的一部分，而且只有在使用量保持不變時，才會繼續回收。
- 發生快取回收時，選取要收回之快取專案的原則，與布建的計算資料庫的選取原則相同，因為記憶體壓力很高。
- 快取大小絕對不會降到低於 min 虛擬核心（可設定）所定義的最小記憶體限制。

在無伺服器和已布建的計算資料庫中，如果使用所有可用的記憶體，則可能會收回快取專案。

請注意，當 CPU 使用率偏低時，使用中的快取使用率可能會根據使用模式而維持很高，並防止記憶體回收。  此外，在進行記憶體回收之前，使用者活動可能會有額外的延遲，因為定期背景進程會回應先前的使用者活動。  例如，「刪除作業」和「QDS 清除工作」會產生標示為刪除的准刪除記錄，但在准刪除清除程式執行時，可能需要將資料頁面讀入快取中，否則不會實際刪除。

#### <a name="cache-hydration"></a>快取序列化

SQL 快取會隨著資料以相同方式從磁片提取，且速度與布建的資料庫相同。 當資料庫忙碌時，允許快取的成長不受限制，最多可達最大記憶體限制。

## <a name="autopausing-and-autoresuming"></a>Autopausing 和 autoresuming

### <a name="autopausing"></a>Autopausing

如果下列所有條件在自動暫停延遲期間都成立，就會觸發 Autopausing：

- 工作階段數 = 0
- CPU = 0 適用于使用者集區中執行的使用者工作負載

如有需要，會提供選項來停用 autopausing。

下列功能不支援 autopausing，但支援自動調整。  如果使用下列任何一項功能，則應該停用 autopausing，而且不論資料庫閒置的持續時間，資料庫都將保持上線狀態：

- 異地複寫 (主動式異地複寫和自動容錯移轉群組) 。
- 長期備份保留 (LTR) 。
- SQL 資料同步中使用的同步資料庫。 與同步資料庫不同的是，中樞和成員資料庫支援 autopausing。
- DNS 別名
- 彈性工作中使用的工作資料庫 (預覽) 。

在部署需要資料庫上線的某些服務更新期間，會暫時防止 Autopausing。  在這種情況下，autopausing 會在服務更新完成後再次允許。

### <a name="autoresuming"></a>Autoresuming

當下列任何條件成立時，就會觸發 Autoresuming：

|功能|自動繼續觸發程序|
|---|---|
|驗證與授權|登入|
|威脅偵測|啟用/停用資料庫或伺服器層級的威脅偵測設定。<br>修改資料庫或伺服器層級的威脅偵測設定。|
|資料探索與分類|新增、修改、刪除或檢視敏感度標籤|
|稽核|檢視稽核記錄。<br>正在更新或查看稽核原則。|
|資料遮罩|新增、修改、刪除或檢視資料遮罩處理規則|
|透明資料加密|查看透明資料加密的狀態或狀態|
|弱點評估|臨機操作掃描和定期掃描（如果已啟用）|
|查詢 (效能) 資料存放區|修改或查看查詢存放區設定|
|效能建議|查看或套用效能建議|
|自動微調|自動微調建議的應用和驗證，例如自動編製索引|
|資料庫複製|將資料庫建立為複製。<br>匯出至 BACPAC 檔案。|
|SQL 資料同步|按照可設定的排程執行或定期執行中樞與成員資料庫之間的同步|
|修改特定資料庫中繼資料|正在加入新的資料庫標記。<br>變更最大虛擬核心、最小虛擬核心或自動暫停延遲。|
|SQL Server Management Studio (SSMS)|使用早于18.1 的 SSMS 版本，並為伺服器中的任何資料庫開啟新的查詢視窗，將會繼續相同伺服器中任何自動暫停的資料庫。 如果使用 SSMS 18.1 版或更新版本，則不會發生此行為。|

執行上述任何作業的監視、管理或其他解決方案將會觸發自動繼續。

在部署某些需要線上資料庫的服務更新期間，也會觸發 Autoresuming。

### <a name="connectivity"></a>連線能力

如果無伺服器資料庫已暫停，則第一次登入將會繼續執行資料庫，並傳回錯誤訊息，指出資料庫無法使用，錯誤碼為40613。 資料庫一旦繼續，則必須重試登入來建立連線。 具有連線重試邏輯的資料庫用戶端應該不需要修改。

### <a name="latency"></a>延遲

自動繼續和自動暫停無伺服器資料庫的延遲通常是從1分鐘到自動繼續，以及1-10 分鐘到自動暫停的順序。

### <a name="customer-managed-transparent-data-encryption-byok"></a>客戶管理的透明資料加密 (BYOK) 

如果使用 [客戶管理的透明資料加密](transparent-data-encryption-byok-overview.md) (BYOK) 而且當刪除或撤銷金鑰時，無伺服器資料庫會自動暫停，則資料庫會保持在自動暫停狀態。  在此情況下，在資料庫下次繼續之後，資料庫會在大約10分鐘內變成無法存取。  一旦資料庫變成無法存取，復原程式就會與布建的計算資料庫相同。  如果在刪除或撤銷金鑰時，無伺服器資料庫在線上，則資料庫也會在大約10分鐘內變成無法存取，與布建的計算資料庫相同。

## <a name="onboarding-into-serverless-compute-tier"></a>上架到無伺服器計算層

建立新的資料庫或將現有的資料庫移到無伺服器計算層級，會遵循與在布建計算層級中建立新資料庫相同的模式，並包含下列兩個步驟。

1. 指定服務目標。 服務目標會規定服務層級、硬體世代和最大虛擬核心。 如需服務目標選項，請參閱 [無伺服器資源限制](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)


2. （選擇性）指定 min 虛擬核心和自動暫停 delay 來變更其預設值。 下表顯示這些參數的可用值。

   |參數|值選擇|預設值|
   |---|---|---|---|
   |最小虛擬核心|取決於設定的最大虛擬核心-請參閱 [資源限制](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)。|0.5 個虛擬核心|
   |自動暫停延遲|最小值：60分鐘 (1 小時) <br>最大值：10080分鐘 (7 天) <br>增量：10分鐘<br>停用自動暫停：-1|60 Minuten|


### <a name="create-a-new-database-in-the-serverless-compute-tier"></a>在無伺服器計算層中建立新的資料庫

下列範例會在無伺服器計算層中建立新的資料庫。

#### <a name="use-the-azure-portal"></a>使用 Azure 入口網站

請參閱 [快速入門：使用 Azure 入口網站在 Azure SQL Database 中建立單一資料庫](single-database-create-quickstart.md)。


#### <a name="use-powershell"></a>使用 PowerShell

```powershell
New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -ComputeModel Serverless -Edition GeneralPurpose -ComputeGeneration Gen5 `
  -MinVcore 0.5 -MaxVcore 2 -AutoPauseDelayInMinutes 720
```
#### <a name="use-the-azure-cli"></a>使用 Azure CLI

```azurecli
az sql db create -g $resourceGroupName -s $serverName -n $databaseName `
  -e GeneralPurpose -f Gen5 --min-capacity 0.5 -c 2 --compute-model Serverless --auto-pause-delay 720
```


#### <a name="use-transact-sql-t-sql"></a>使用 Transact-sql (T-sql) 

使用 T-sql 時，會針對 min 虛擬核心和自動暫停 delay 套用預設值。

```sql
CREATE DATABASE testdb
( EDITION = 'GeneralPurpose', SERVICE_OBJECTIVE = 'GP_S_Gen5_1' ) ;
```

如需詳細資訊，請參閱 [建立資料庫](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)。  

### <a name="move-a-database-from-the-provisioned-compute-tier-into-the-serverless-compute-tier"></a>將資料庫從已布建的計算層移至無伺服器計算層

下列範例會將資料庫從已布建的計算層移至無伺服器計算層。

#### <a name="use-powershell"></a>使用 PowerShell


```powershell
Set-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -Edition GeneralPurpose -ComputeModel Serverless -ComputeGeneration Gen5 `
  -MinVcore 1 -MaxVcore 4 -AutoPauseDelayInMinutes 1440
```

#### <a name="use-the-azure-cli"></a>使用 Azure CLI

```azurecli
az sql db update -g $resourceGroupName -s $serverName -n $databaseName `
  --edition GeneralPurpose --min-capacity 1 --capacity 4 --family Gen5 --compute-model Serverless --auto-pause-delay 1440
```


#### <a name="use-transact-sql-t-sql"></a>使用 Transact-sql (T-sql) 

使用 T-sql 時，會針對 min 虛擬核心和自動暫停 delay 套用預設值。

```sql
ALTER DATABASE testdb 
MODIFY ( SERVICE_OBJECTIVE = 'GP_S_Gen5_1') ;
```

如需詳細資訊，請參閱 [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current)。

### <a name="move-a-database-from-the-serverless-compute-tier-into-the-provisioned-compute-tier"></a>將資料庫從無伺服器計算層移至布建的計算層

無伺服器資料庫可以移到佈建計算層級中，方法如同將佈建計算資料庫移到無伺服器計算層級中。

## <a name="modifying-serverless-configuration"></a>修改無伺服器設定

### <a name="use-powershell"></a>使用 PowerShell

使用[](/powershell/module/az.sql/set-azsqldatabase) `MaxVcore` 、 `MinVcore` 和引數，在 PowerShell 中使用 >set-azsqldatabase 命令來修改最大或最小虛擬核心，以及自動暫停延遲 `AutoPauseDelayInMinutes` 。

### <a name="use-the-azure-cli"></a>使用 Azure CLI

使用[](/cli/azure/sql/db#az-sql-db-update) `capacity` 、 `min-capacity` 和引數，即可在 Azure CLI 中使用 az sql db update 命令來修改最大或最小虛擬核心，以及自動暫停延遲 `auto-pause-delay` 。


## <a name="monitoring"></a>監視

### <a name="resources-used-and-billed"></a>使用和計費的資源

無伺服器資料庫的資源是由應用程式封裝、SQL 實例和使用者資源集區實體所封裝。

#### <a name="app-package"></a>應用程式套件

不論資料庫是在無伺服器或佈建計算層級中，應用程式套件都是資料庫的最外層資源管理界限。 應用程式套件包含 SQL 執行個體和外部服務，其可一起界定 SQL Database 中資料庫所用的所有使用者和系統資源。 外部服務的範例包括 R 和全文檢索搜尋。 SQL 執行個體通常會支配整個應用程式套件的整體資源使用率。

#### <a name="user-resource-pool"></a>使用者資源集區

不論資料庫是在無伺服器或佈建計算層級中，使用者資源集區都是資料庫的最內層資源管理界限。 使用者資源集區會針對 DDL 查詢產生的使用者工作負載（例如 SELECT、INSERT、UPDATE 和 DELETE）產生 CPU 和 IO 的範圍。 這些查詢通常代表應用程式套件內很大的使用率比例。

### <a name="metrics"></a>計量

下表列出用來監視無伺服器資料庫之應用程式封裝和使用者集區之資源使用量的計量：

|實體|Metric|描述|單位|
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

使用下列命令來查詢資料庫的暫停和繼續狀態：

#### <a name="use-powershell"></a>使用 PowerShell

```powershell
Get-AzSqlDatabase -ResourceGroupName $resourcegroupname -ServerName $servername -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

#### <a name="use-the-azure-cli"></a>使用 Azure CLI

```azurecli
az sql db show --name $databasename --resource-group $resourcegroupname --server $servername --query 'status' -o json
```


## <a name="resource-limits"></a>資源限制

如需資源限制，請參閱 [無伺服器計算層級](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)。

## <a name="billing"></a>計費

計算數量的計費方式為每秒使用的最大 CPU 與記憶體。 若使用的 CPU 與使用的記憶體數量小於各自的最小佈建數量，就會收取佈建數量的費用。 為了比較 CPU 與記憶體以供計費用途，記憶體會依每個虛擬核心 3 GB 重新調整記憶體量，藉此規範成虛擬核心單位。

- **資源計費**： CPU 和記憶體
- **計費金額**： vCore unit price * max (min 虛擬核心、虛擬核心 used、MIN memory GB * 1/3、memory gb used * 1/3)  
- **計費頻率**：每秒

VCore unit price 是每個 vCore 的每秒成本。 如需指定區域中的特定單位價格，請參閱 [Azure SQL Database 定價頁面](https://azure.microsoft.com/pricing/details/sql-database/single/)。

計費的計算數量會依下列度量公開：

- **計量**：app_cpu_billed (虛擬核心秒數)
- **定義**：最大值 (最小虛擬核心數, 使用的虛擬核心, 最小記憶體 GB * 1/3, 使用的記憶體 GB * 1/3)
- **報告頻率**：每分鐘

此數量會每秒計算，並彙總超過 1 分鐘。

### <a name="minimum-compute-bill"></a>最小計算帳單

如果無伺服器資料庫已暫停，則計算帳單為零。  如果無伺服器資料庫未暫停，則最小計算帳單的最小值是根據最大 (最小值虛擬核心、最小記憶體 GB * 1/3) 的虛擬核心量。

範例：

- 假設無伺服器資料庫未暫停，並設定最多8個虛擬核心和1分鐘的 vCore，對應至 3.0 GB 的最小記憶體。  然後，最小計算費用是根據 max (1 vCore、3.0 GB * 1 vCore/3 GB) = 1 vCore。
- 假設無伺服器資料庫未暫停，並設定 4 max 虛擬核心和 0.5 min 虛擬核心，對應至 2.1 GB 的最小記憶體。  然後，最小計算費用是根據 max (0.5 虛擬核心、2.1 GB * 1 vCore/3 GB) = 0.7 虛擬核心。

適用于無伺服器的 [Azure SQL Database 定價計算機](https://azure.microsoft.com/pricing/calculator/?service=sql-database) ，可用來根據設定的最大和最小虛擬核心數，判斷可設定的最小記憶體。  作為規則，如果設定的最小虛擬核心大於0.5 虛擬核心，則最小計算帳單與設定的最小記憶體無關，而且只會根據設定的最小虛擬核心數。

### <a name="example-scenario"></a>範例案例

請考慮設定為1分鐘 vCore 和 4 max 虛擬核心的無伺服器資料庫。  這對應于大約 3 GB 的最小記憶體和 12 GB 的最大記憶體。  假設自動暫停延遲設定為6小時，而且資料庫工作負載在24小時期間的前2小時內處於作用中狀態，否則為非使用中。    

在此情況下，資料庫會在前8小時內針對計算和儲存體計費。  即使在第二個小時後資料庫為非使用中狀態，仍會根據資料庫上線時所布建的最小計算，在後續6小時內支付計算費用。  當資料庫暫停時，在24小時期間的剩餘時間內，只會收取儲存體費用。

更精確地說，此範例中的計算帳單計算方式如下：

|時間間隔|每秒使用的虛擬核心|每秒使用的 GB|計算維度計費|依時間間隔計費的 vCore 秒數|
|---|---|---|---|---|
|0:00-1:00|4|9|使用的虛擬核心|4虛擬核心 * 3600 秒 = 14400 vCore 秒|
|1:00-2:00|1|12|使用的記憶體|12 GB * 1/3 * 3600 秒 = 14400 vCore 秒|
|2:00-8:00|0|0|布建的最小記憶體|3 GB * 1/3 * 21600 秒 = 21600 vCore 秒|
|8:00-24:00|0|0|未在暫停時計算任何計算費用|0 vCore 秒|
|總 vCore 秒數（以24小時計費）||||50400 vCore 的秒數|

假設計算單位價格是 $ 0.000145/vCore/second。  然後，此24小時期間的計算費用是計算單位價格和 vCore 秒數的乘積： $ 0.000145/vCore/second * 50400 vCore 秒 ~ $7.31

### <a name="azure-hybrid-benefit-and-reserved-capacity"></a>Azure Hybrid Benefit 和保留容量

Azure Hybrid Benefit (AHB) 與保留容量折扣並不適用于無伺服器計算層級。

## <a name="available-regions"></a>可用區域

無伺服器計算層級可于全球使用，但下欄區域除外：中國東部、中國北部、德國中部、德國東北部和 US Gov 中部 (愛荷華州) 。

## <a name="next-steps"></a>後續步驟

- 若要開始使用，請參閱[快速入門：使用 Azure 入口網站在 Azure SQL Database 中建立單一資料庫](single-database-create-quickstart.md)。
- 如需資源限制，請參閱[無伺服器計算層級資源限制](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)。
