---
title: 如何將資源移動到其他區域
description: 瞭解如何將 Azure SQL 資料庫、Azure SQL 彈性池或 Azure SQL 託管實例移動到其他區域。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 851ef49a5c066f12a95baa54daf5e267cb4278c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821440"
---
# <a name="how-to-move-azure-sql-resources-to-another-region"></a>如何將 Azure SQL 資源移動到其他區域

本文介紹有關如何將 Azure SQL 資料庫單個資料庫、彈性池和託管實例移動到新區域的通用工作流。 

## <a name="overview"></a>總覽

在各種方案中，您希望將現有的 Azure SQL 資源從一個區域移動到另一個區域。 例如，您將業務擴展到新區域，並希望為新客戶群進行優化。 或者，出於合規性原因，您需要將操作移動到其他區域。 或者 Azure 發佈了一個全新的區域，可提供更好的接近性並改善客戶體驗。  

本文提供了將資源移動到其他區域的一般工作流。 工作流包括以下步驟： 

- 驗證移動的先決條件 
- 準備移動作用域中的資源
- 監控準備流程
- 測試移動過程
- 啟動實際移動 
- 從源區域中刪除資源 


> [!NOTE]
> 本文適用于 Azure 公共雲中的遷移，或在同一主權雲中的遷移。 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-single-database"></a>移動單個資料庫

### <a name="verify-prerequisites"></a>驗證必要條件 

1. 為每個源伺服器創建目標邏輯伺服器。 
1. 使用[PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)配置具有正確異常的防火牆。  
1. 使用正確的登錄名配置邏輯伺服器。 如果您不是訂閱管理員或 SQL 伺服器管理員，請與管理員一起分配所需的許可權。 有關詳細資訊，請參閱[災害復原後如何管理 Azure SQL 資料庫安全性](sql-database-geo-replication-security-config.md)。 
1. 如果您的資料庫使用 TDE 加密，並在 Azure 金鑰保存庫中使用自己的加密金鑰，請確保在目的地區域中預配了正確的加密材料。 有關詳細資訊，請參閱[Azure 金鑰保存庫中使用客戶管理的金鑰的 Azure SQL 透明資料加密](transparent-data-encryption-byok-azure-sql.md)
1. 如果啟用了資料庫級審核，請禁用它並改為啟用伺服器級審核。 容錯移轉後，資料庫級審核將需要跨區域流量，在移動後不需要或可能進行跨區域流量。 
1. 對於伺服器級審核，請確保：
   - 具有現有稽核記錄的存儲容器、日誌分析或事件中心將移動到目的地區域。 
   - 審核在目標伺服器上配置。 如需詳細資訊，請參閱 [開始使用 SQL 資料庫稽核](sql-database-auditing.md)。 
1. 如果您的實例具有長期保留原則 （LTR），則現有的 LTR 備份將保持與當前伺服器關聯。 由於目標伺服器不同，因此即使已刪除伺服器，您也可以使用源伺服器訪問源區域中較舊的 LTR 備份。 

  > [!NOTE]
  > 這不足以在主權雲和公共區域之間移動。 此類遷移需要將 LTR 備份移動到目標伺服器，而目標伺服器當前不受支援。 

### <a name="prepare-resources"></a>準備資源

1. 在源的邏輯伺服器到目標的邏輯伺服器之間創建[容錯移轉組](sql-database-single-database-failover-group-tutorial.md#2---create-the-failover-group)。  
1. 添加要移動到容錯移轉組的資料庫。 
    - 將自動啟動所有添加的資料庫的複製。 有關詳細資訊，請參閱[使用具有單個資料庫的容錯移轉組的最佳做法](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools)。 
 
### <a name="monitor-the-preparation-process"></a>監控準備流程

您可以定期調用[Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup)來監視資料庫從源到目標的複製。 的`Get-AzSqlDatabaseFailoverGroup`輸出物件包括**複製狀態**的屬性 ： 
   - **複製狀態 =** 2（CATCH_UP）表示資料庫已同步，可以安全故障。 
   - **複製狀態 = 0（** 種子化）表示資料庫尚未設定種子，並且嘗試容錯移轉將失敗。 

### <a name="test-synchronization"></a>測試同步

**複製狀態**為`2`後 ，使用輔助終結點`<fog-name>.secondary.database.windows.net`連接到每個資料庫或資料庫子集，並針對資料庫執行任何查詢，以確保連線性、適當的安全配置和資料複製。 

### <a name="initiate-the-move"></a>啟動移動

1. 使用輔助終結點`<fog-name>.secondary.database.windows.net`連接到目標伺服器。
1. 使用[Switch-AzSql 資料庫FailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)將輔助託管實例切換為具有完全同步的主實例。 此操作將成功，或者將回滾。 
1. 使用`nslook up <fog-name>.secondary.database.windows.net`以確定 DNS CNAME 條目指向目的地區域 IP 位址，驗證該命令是否已成功完成。 如果交換器命令失敗，CNAME 將不會更新。 

### <a name="remove-the-source-databases"></a>刪除源資料庫

移動完成後，刪除源區域中的資源以避免不必要的費用。 

1. 使用[刪除-AzSql資料庫容錯移轉組刪除容錯移轉組](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)。 
1. 使用[刪除-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)刪除每個源資料庫，用於源伺服器上的每個資料庫。 這將自動終止異地複製連結。 
1. 使用[刪除-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)刪除源伺服器。 
1. 刪除金鑰保存庫、審核存儲容器、事件中心、AAD 實例和其他從屬資源，以停止為其計費。 

## <a name="move-elastic-pools"></a>移動彈性池

### <a name="verify-prerequisites"></a>驗證必要條件 

1. 為每個源伺服器創建目標邏輯伺服器。 
1. 使用[PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)配置具有正確異常的防火牆。 
1. 使用正確的登錄名配置邏輯伺服器。 如果您不是訂閱管理員或 SQL 伺服器管理員，請與管理員一起分配所需的許可權。 有關詳細資訊，請參閱[災害復原後如何管理 Azure SQL 資料庫安全性](sql-database-geo-replication-security-config.md)。 
1. 如果您的資料庫使用 TDE 加密，並在 Azure 金鑰保存庫中使用自己的加密金鑰，請確保在目的地區域中預配了正確的加密材料。
1. 為每個源彈性池創建目標彈性池，確保池在同一服務層中創建，名稱相同，大小相同。 
1. 如果啟用了資料庫級審核，請禁用它並改為啟用伺服器級審核。 容錯移轉後，資料庫級審核將需要跨區域流量（這是不需要的，或在移動後可能）。 
1. 對於伺服器級審核，請確保：
    - 具有現有稽核記錄的存儲容器、日誌分析或事件中心將移動到目的地區域。
    - 審核配置在目標伺服器上配置。 有關詳細資訊，請參閱[SQL 資料庫審核](sql-database-auditing.md)。
1. 如果您的實例具有長期保留原則 （LTR），則現有的 LTR 備份將保持與當前伺服器關聯。 由於目標伺服器不同，因此即使已刪除伺服器，您也可以使用源伺服器訪問源區域中較舊的 LTR 備份。 

  > [!NOTE]
  > 這不足以在主權雲和公共區域之間移動。 此類遷移需要將 LTR 備份移動到目標伺服器，而目標伺服器當前不受支援。 

### <a name="prepare-to-move"></a>準備移動
 
1.  在源邏輯伺服器上的每個彈性池和目標伺服器上的對應彈性池之間創建單獨的[容錯移轉組](sql-database-elastic-pool-failover-group-tutorial.md#3---create-the-failover-group)。 
1.  將池中的所有資料庫添加到容錯移轉組。 
    - 將自動啟動添加的資料庫的複製。 有關詳細資訊，請參閱[具有彈性池的容錯移轉組的最佳做法](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools)。 

  > [!NOTE]
  > 雖然可以創建包含多個彈性池的容錯移轉組，但我們強烈建議為每個池創建單獨的容錯移轉組。 如果跨多個彈性池具有大量資料庫需要移動，則可以並行運行準備步驟，然後並行啟動移動步驟。 與在同一容錯移轉組中具有多個彈性池相比，此過程將擴展得更好，並且耗時更少。 

### <a name="monitor-the-preparation-process"></a>監控準備流程

您可以定期調用[Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup)來監視資料庫從源到目標的複製。 的`Get-AzSqlDatabaseFailoverGroup`輸出物件包括**複製狀態**的屬性 ： 
   - **複製狀態 =** 2（CATCH_UP）表示資料庫已同步，可以安全故障。 
   - **複製狀態 = 0（** 種子化）表示資料庫尚未設定種子，並且嘗試容錯移轉將失敗。 

### <a name="test-synchronization"></a>測試同步
 
**複製狀態**為`2`後 ，使用輔助終結點`<fog-name>.secondary.database.windows.net`連接到每個資料庫或資料庫子集，並針對資料庫執行任何查詢，以確保連線性、適當的安全配置和資料複製。 

### <a name="initiate-the-move"></a>啟動移動
 
1. 使用輔助終結點`<fog-name>.secondary.database.windows.net`連接到目標伺服器。
1. 使用[Switch-AzSql 資料庫FailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)將輔助託管實例切換為具有完全同步的主實例。 此操作將成功，或者將回滾。 
1. 使用`nslook up <fog-name>.secondary.database.windows.net`以確定 DNS CNAME 條目指向目的地區域 IP 位址，驗證該命令是否已成功完成。 如果交換器命令失敗，CNAME 將不會更新。 

### <a name="remove-the-source-elastic-pools"></a>刪除源彈性池
 
移動完成後，刪除源區域中的資源以避免不必要的費用。 

1. 使用[刪除-AzSql資料庫容錯移轉組刪除容錯移轉組](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)。
1. 使用[Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)刪除源伺服器上的每個源彈性池。 
1. 使用[刪除-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)刪除源伺服器。 
1. 刪除金鑰保存庫、審核存儲容器、事件中心、AAD 實例和其他從屬資源，以停止為其計費。 

## <a name="move-managed-instance"></a>移動託管實例

### <a name="verify-prerequisites"></a>驗證必要條件
 
1. 對於每個源託管實例，在目的地區域中創建具有相同大小的目標託管實例。  
1. 為託管實例配置網路。 有關詳細資訊，請參閱[網路設定](sql-database-howto-managed-instance.md#network-configuration)。
1. 使用正確的登錄名配置目標主資料庫。 如果您不是訂閱管理員或 SQL 伺服器管理員，請與管理員一起分配所需的許可權。 
1. 如果您的資料庫使用 TDE 加密，並在 Azure 金鑰保存庫中使用自己的加密金鑰，請確保具有相同加密金鑰的 AKV 存在於源和目的地區域中。 有關詳細資訊，請參閱[Azure 金鑰保存庫中使用客戶管理金鑰的 TDE。](transparent-data-encryption-byok-azure-sql.md)
1. 如果為實例啟用了審核，請確保：
    - 具有現有日誌的存儲容器或事件中心將移動到目的地區域。 
    - 審核在目標實例上配置。 有關詳細資訊，請參閱[使用託管實例進行審核](sql-database-managed-instance-auditing.md)。
1. 如果您的實例具有長期保留原則 （LTR），則現有的 LTR 備份將保持與當前伺服器關聯。 由於目標伺服器不同，因此即使已刪除伺服器，您也可以使用源伺服器訪問源區域中較舊的 LTR 備份。 

  > [!NOTE]
  > 這不足以在主權雲和公共區域之間移動。 此類遷移需要將 LTR 備份移動到目標伺服器，而目標伺服器當前不受支援。 

### <a name="prepare-resources"></a>準備資源

在每個源實例和相應的目標實例之間創建容錯移轉組。
    - 將自動啟動每個實例上所有資料庫的複製。 有關詳細資訊[，請參閱自動容錯移轉組](sql-database-auto-failover-group.md)。

 
### <a name="monitor-the-preparation-process"></a>監控準備流程

您可以定期調用[Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup?view=azps-2.3.2)來監視資料庫從源到目標的複製。 的`Get-AzSqlDatabaseFailoverGroup`輸出物件包括**複製狀態**的屬性 ： 
   - **複製狀態 =** 2（CATCH_UP）表示資料庫已同步，可以安全故障。 
   - **複製狀態 = 0（** 種子化）表示資料庫尚未設定種子，並且嘗試容錯移轉將失敗。 

### <a name="test-synchronization"></a>測試同步

**複製狀態**為`2`後 ，使用輔助終結點`<fog-name>.secondary.database.windows.net`連接到每個資料庫或資料庫子集，並針對資料庫執行任何查詢，以確保連線性、適當的安全配置和資料複製。 

### <a name="initiate-the-move"></a>啟動移動 

1. 使用輔助終結點`<fog-name>.secondary.database.windows.net`連接到目標伺服器。
1. 使用[Switch-AzSql 資料庫FailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup?view=azps-2.3.2)將輔助託管實例切換為具有完全同步的主實例。 此操作將成功，或者將回滾。 
1. 使用`nslook up <fog-name>.secondary.database.windows.net`以確定 DNS CNAME 條目指向目的地區域 IP 位址，驗證該命令是否已成功完成。 如果交換器命令失敗，CNAME 將不會更新。 

### <a name="remove-the-source-managed-instances"></a>刪除源託管實例
移動完成後，刪除源區域中的資源以避免不必要的費用。 

1. 使用[刪除-AzSql資料庫容錯移轉組刪除容錯移轉組](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)。 這將刪除容錯移轉組配置，並終止兩個實例之間的異地複製連結。 
1. 使用[Remove-AzSql實例](/powershell/module/az.sql/remove-azsqlinstance)刪除源託管實例。 
1. 刪除資源組中的任何其他資源，如虛擬叢集、虛擬網路和安全性群組。 

## <a name="next-steps"></a>後續步驟 

[遷移](sql-database-manage-after-migration.md)Azure SQL 資料庫後對其進行管理。 

