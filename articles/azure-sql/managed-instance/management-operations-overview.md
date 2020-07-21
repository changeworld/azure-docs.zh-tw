---
title: 管理作業
titleSuffix: Azure SQL Managed Instance
description: 瞭解 Azure SQL 受控執行個體管理作業持續時間和最佳作法。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, carlrab, MashaMSFT
ms.date: 07/10/2020
ms.openlocfilehash: 5cff54b1f71d30f7932c4ead722d1dda0a7aec57
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531189"
---
# <a name="overview-of-azure-sql-managed-instance-management-operations"></a>Azure SQL 受控執行個體管理作業總覽
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

## <a name="what-are-management-operations"></a>什麼是管理作業？
Azure SQL 受控執行個體提供管理作業，可讓您在不再需要時，用來自動部署新的受控實例、更新實例屬性和刪除實例。

為了支援[Azure 虛擬網路內的部署](../../virtual-network/virtual-network-for-azure-services.md)，並為客戶提供隔離和安全性，SQL 受控執行個體依賴[虛擬叢集](connectivity-architecture-overview.md#high-level-connectivity-architecture)。 虛擬叢集代表一組在客戶的虛擬網路子網內部署的專屬獨立虛擬機器。 基本上，空白子網中的每個受控實例部署都會產生新的虛擬叢集增建。

針對已部署的受控實例進行的後續作業，可能也會對基礎虛擬叢集造成影響。 這些作業會影響管理作業的持續時間，因為部署額外的虛擬機器時，會有額外負荷，當您規劃新的部署或現有受控實例的更新時，需要加以考慮。

所有管理作業都可以分類如下：

- 實例部署（建立新的實例）。
- 實例更新（變更實例屬性，例如虛擬核心或保留的儲存體）。
- 實例刪除。

## <a name="management-operations-duration"></a>管理作業持續時間
一般而言，虛擬叢集上的作業會花費最長的時間。 虛擬叢集上的作業持續時間：以下是根據現有的服務遙測資料，您通常可以預期的值。。

- **虛擬叢集建立**：建立是實例管理作業中的同步步驟。 **90% 的作業會在4小時內完成**。
- **虛擬叢集調整大小（擴充或壓縮）**：展開是同步步驟，而壓縮是以非同步方式執行（不會影響實例管理作業的持續時間）。 **90% 的叢集擴充在2.5 小時內完成**。
- **虛擬叢集刪除**：刪除是非同步步驟，但也可以在空的虛擬叢集上[手動起始](virtual-cluster-delete.md)，在此情況下，它會以同步方式執行。 **90% 的虛擬叢集刪除在1.5 小時內完成**。

此外，實例的管理也可能包含託管資料庫上的其中一項作業，這會導致較長的持續時間：

- **從 Azure 儲存體附加資料庫**檔案：同步步驟（例如計算（虛擬核心）），或在一般用途服務層級中向上或向下調整的儲存體。 **這些作業的90% 會在5分鐘內完成**。
- **Always On 可用性群組植**入：一個同步步驟（例如計算（虛擬核心）），或業務關鍵服務層級中的儲存體調整，以及將服務層從一般用途變更為業務關鍵（反之亦然）。 這項作業的持續時間與資料庫總大小以及目前資料庫活動（使用中交易數目）成正比。 更新實例時的資料庫活動可能會對總持續時間產生明顯的差異。 **這些作業的90% 是以 220 GB/小時或更高的時間執行**。

下表摘要說明作業和一般的整體持續時間：

|類別  |作業  |長時間執行的區段  |預估的持續時間  |
|---------|---------|---------|---------|
|**部署** |空白子網中的第一個實例|建立虛擬叢集|90% 的作業會在4小時內完成。|
|部署 |非空白子網中另一個硬體世代的第一個實例（例如，第一個 Gen 5 實例，位於具有 Gen 4 實例的子網中）|虛擬叢集建立 *|90% 的作業會在4小時內完成。|
|部署 |在空白或非空白的子網中建立第一個實例4虛擬核心|虛擬叢集建立 * *|90% 的作業會在4小時內完成。|
|部署 |在非空白子網內建立的後續實例（第二、第三等實例）|虛擬叢集調整大小|90% 的作業會在2.5 小時後完成。|
|**更新** |實例屬性變更（系統管理員密碼，Azure AD 登入，Azure Hybrid Benefit 旗標）|N/A|最多1分鐘。|
|更新 |實例儲存體相應增加/減少（一般用途服務層級）|附加資料庫檔案|90% 的作業會在5分鐘內完成。|
|更新 |實例儲存體相應增加/減少（商務關鍵服務層級）|-虛擬叢集調整大小<br>-Always On 可用性群組植入|90% 的作業會在2.5 小時內完成，並將所有資料庫植入的時間（220 GB/小時）。|
|更新 |實例計算（虛擬核心）相應增加和減少（一般用途）|-虛擬叢集調整大小<br>-附加資料庫檔案|90% 的作業會在2.5 小時後完成。|
|更新 |實例計算（虛擬核心）相應增加和減少（業務關鍵）|-虛擬叢集調整大小<br>-Always On 可用性群組植入|90% 的作業會在2.5 小時內完成，並將所有資料庫植入的時間（220 GB/小時）。|
|更新 |實例服務層級變更（一般用途到業務關鍵，反之亦然）|-虛擬叢集調整大小<br>-Always On 可用性群組植入|90% 的作業會在2.5 小時內完成，並將所有資料庫植入的時間（220 GB/小時）。|
|**操作**|執行個體刪除|所有資料庫的記錄尾備份|90% 作業會在最多1分鐘內完成。<br>注意：如果刪除子網中的最後一個實例，此作業會在12小時後排程刪除虛擬叢集。 * *|
|刪除|虛擬叢集刪除（作為使用者起始的作業）|虛擬叢集刪除|90% 的作業會在最多1.5 小時內完成。|

\*虛擬叢集是根據硬體世代而建立的。

\*\*12小時是目前的設定，但未來可能會變更，因此不會對其進行硬相依性。 如果您需要稍早刪除虛擬叢集（例如，若要釋放子網），請參閱刪除[受控實例後刪除子網](virtual-cluster-delete.md)。

## <a name="instance-availability-during-management-operations"></a>管理作業期間的實例可用性

除了更新結束時所發生的容錯移轉所造成的短暫停機以外，**更新作業期間仍可使用**SQL 受控執行個體。 這通常會持續10秒，即使在中斷長時間執行的交易時，也是因為[加速資料庫](../accelerated-database-recovery.md)復原。

> [!IMPORTANT]
> 不建議您調整 Azure SQL 受控執行個體的計算或儲存體，或使用長時間執行的交易（資料匯入、資料處理作業、索引重建等等）來同時變更服務層級。 將在作業結束時執行的資料庫容錯移轉將會取消所有進行中的交易。

在部署和刪除作業期間，用戶端應用程式無法使用 SQL 受控執行個體。

## <a name="management-operations-cross-impact"></a>跨影響的管理作業

受控實例上的管理作業可能會影響放在相同虛擬叢集中之實例的其他管理作業：

- 虛擬叢集中**長時間執行的還原作業**會放在相同子網中的其他實例建立或調整作業。<br/>**範例：** 如果有長時間執行的還原作業，而且在相同的子網中有建立或調整要求，此要求將需要較長的時間才能完成，因為它會等候還原作業完成後，再繼續進行。
    
- **後續的實例建立或調整**作業會由先前起始的實例建立或實例規模（起始虛擬叢集調整大小）暫停。<br/>**範例：** 如果相同虛擬叢集下的相同子網中有多個建立和/或調整要求，而且其中一個會起始虛擬叢集調整大小，則在需要虛擬叢集調整大小的 5 + 分鐘後，已提交的所有要求會持續超過預期，因為這些要求必須等待調整大小才能完成，然後再繼續。

- **在5分鐘的時間範圍內提交的建立/調整作業**會以平行方式進行批次處理和執行。<br/>**範例：** 在5分鐘的時間範圍內提交的所有作業，只會執行一次調整大小的虛擬叢集（從執行第一個作業要求的那一刻開始測量）。 如果提交第一個要求的時間超過5分鐘，則會在執行開始之前，等待虛擬叢集調整大小完成。

> [!IMPORTANT]
> 因為有另一個進行中的作業要繼續進行，所以在符合條件之後，就會自動繼續進行管理。 不需要使用者採取任何動作，即可繼續暫時暫停的管理作業。

## <a name="canceling-management-operations"></a>取消管理作業

下表摘要說明取消特定管理作業的能力，以及一般的整體持續時間：

類別  |作業  |取消  |估計的取消持續時間  |
|---------|---------|---------|---------|
|部署 |實例建立 |否 |  |
|更新 |實例儲存體相應增加/減少（一般用途） |否 |  |
|更新 |實例儲存體相應增加/減少（業務關鍵） |是 |90% 的作業會在5分鐘內完成。 |
|更新 |實例計算（虛擬核心）相應增加和減少（一般用途） |是 |90% 的作業會在5分鐘內完成。 |
|更新 |實例計算（虛擬核心）相應增加和減少（業務關鍵） |是 |90% 的作業會在5分鐘內完成。 |
|更新 |實例服務層級變更（一般用途到業務關鍵，反之亦然） |是 |90% 的作業會在5分鐘內完成。 |
|刪除 |執行個體刪除 |否 |  |
|刪除 |虛擬叢集刪除（作為使用者起始的作業） |否 |  |

# <a name="portal"></a>[入口網站](#tab/azure-portal)

若要取消管理作業，請移至 [總覽] 分頁，然後按一下進行中操作的 [通知] 方塊。 從右側開始會顯示含有進行中作業的畫面，而且將會出現取消作業的按鈕。 第一次按一下之後，系統會要求您再次按一下，並確認您想要取消此操作。

[![取消作業](./media/management-operations-overview/canceling-operation.png)](./media/management-operations-overview/canceling-operation.png#lightbox)

提交並處理取消要求之後，如果取消提交成功，您就會收到通知。

如果提交的取消要求成功，管理作業會在幾分鐘內取消，並導致失敗。

![正在取消操作結果](./media/management-operations-overview/canceling-operation-result.png)

如果取消要求失敗，或 [取消] 按鈕未啟用，則表示管理作業已進入不可取消的狀態，而且會在幾分鐘內完成。 管理作業會繼續執行，直到完成為止。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

如果您尚未安裝 Azure PowerShell，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps)。

若要取消管理作業，您需要指定管理作業名稱。 因此，請先使用 get 命令來抓取作業清單，然後取消特定的作業。

```powershell-interactive
$managedInstance = "yourInstanceName"
$resourceGroup = "yourResourceGroupName"

$managementOperations = Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup

foreach ($mo in $managementOperations ) {
    if($mo.State -eq "InProgress" -and $mo.IsCancellable){
        $cancelRequest = Stop-AzSqlInstanceOperation -ResourceGroupName $resourceGroup -ManagedInstanceName $managedInstance -Name $mo.Name
        Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup -Name $mo.Name
    }
}
```

如需詳細的命令說明，請參閱[AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstanceoperation)和[AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/stop-azsqlinstanceoperation)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

如果您尚未安裝 Azure CLI，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。

若要取消管理作業，您需要指定管理作業名稱。 因此，請先使用 get 命令來抓取作業清單，然後取消特定的作業。

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName --query "[?state=='InProgress' && isCancellable].{Name: name}" -o tsv |
while read -r operationName; do
    az sql mi op cancel -g yourResourceGroupName --mi yourInstanceName -n $operationName
done
```

如需詳細的命令說明，請參閱[az sql mi op](https://docs.microsoft.com/cli/azure/sql/mi/op?view=azure-cli-latest)。

---

## <a name="next-steps"></a>後續步驟
- 若要瞭解如何建立您的第一個受控實例，請參閱[快速入門手冊](instance-create-quickstart.md)。
- 如需功能與比較清單，請參閱 [SQL 的一般功能](../database/features-comparison.md)。
- 如需 VNet 設定的詳細資訊，請參閱[SQL 受控執行個體 vnet](connectivity-architecture-overview.md)設定。
- 如需建立受控實例並從備份檔案還原資料庫的快速入門，請參閱[建立受控實例](instance-create-quickstart.md)。
- 如需使用 Azure 資料庫移轉服務進行遷移的教學課程，請參閱[使用資料庫移轉服務的 SQL 受控執行個體遷移](../../dms/tutorial-sql-server-to-managed-instance.md)。
