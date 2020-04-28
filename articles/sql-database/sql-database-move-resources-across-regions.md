---
title: 如何將資源移至另一個區域
description: 瞭解如何將您的 Azure SQL Database、Azure SQL 彈性集區或 Azure SQL 受控實例移至另一個區域。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "73821440"
---
# <a name="how-to-move-azure-sql-resources-to-another-region"></a>如何將 Azure SQL 資源移至另一個區域

本文會教您如何將 Azure SQL Database 單一資料庫、彈性集區和受控實例移至新區域的一般工作流程。 

## <a name="overview"></a>概觀

在許多情況下，您會想要將現有的 Azure SQL 資源從一個區域移至另一個區域。 例如，您將企業擴充到新的區域，並想要針對新的客戶群將其優化。 或者，您必須將作業移至不同的區域，以符合合規性因素。 或 Azure 發行了全新的區域，可提供更好的鄰近性並改善客戶體驗。  

本文提供將資源移至不同區域的一般工作流程。 工作流程是由下列步驟所組成： 

- 確認移動的必要條件 
- 準備移動範圍內的資源
- 監視準備程式
- 測試移動進程
- 起始實際移動 
- 從來源區域移除資源 


> [!NOTE]
> 本文適用于 Azure 公用雲端內或相同主權雲端內的遷移。 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-single-database"></a>移動單一資料庫

### <a name="verify-prerequisites"></a>驗證必要條件 

1. 為每部來源伺服器建立目標邏輯伺服器。 
1. 使用[PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)設定具有正確例外狀況的防火牆。  
1. 使用正確的登入設定邏輯伺服器。 如果您不是訂用帳戶管理員或 SQL server 系統管理員，請與系統管理員合作以指派您所需的許可權。 如需詳細資訊，請參閱[如何在嚴重損壞修復後管理 AZURE SQL database 安全性](sql-database-geo-replication-security-config.md)。 
1. 如果您的資料庫使用 TDE 加密，並在 Azure key vault 中使用您自己的加密金鑰，請確定已在目的地區域中布建正確的加密資料。 如需詳細資訊，請參閱[Azure Key Vault 中客戶管理的金鑰的 AZURE SQL 透明資料加密](transparent-data-encryption-byok-azure-sql.md)
1. 如果已啟用資料庫層級的 audit，請將它停用，並改為啟用伺服器層級的審核。 在容錯移轉之後，資料庫層級的審核將需要跨區域流量，這在移動之後不需要或無法使用。 
1. 針對伺服器層級的審核，請確定：
   - 具有現有 audit 記錄的儲存體容器、Log Analytics 或事件中樞，會移至目的地區域。 
   - 在目標伺服器上設定了 [審核]。 如需詳細資訊，請參閱 [開始使用 SQL 資料庫稽核](sql-database-auditing.md)。 
1. 如果您的實例具有長期保留原則（LTR），現有的 LTR 備份將會與目前的伺服器保持關聯。 因為目標伺服器不同，所以您可以使用來源伺服器存取來源區域中較舊的 LTR 備份，即使刪除伺服器也一樣。 

  > [!NOTE]
  > 這對於在主權雲端與公用區域之間移動的不足。 這類遷移需要將 LTR 備份移至目前不支援的目標伺服器。 

### <a name="prepare-resources"></a>準備資源

1. 在來源的邏輯伺服器與目標的邏輯伺服器之間建立[容錯移轉群組](sql-database-single-database-failover-group-tutorial.md#2---create-the-failover-group)。  
1. 新增您想要移至容錯移轉群組的資料庫。 
    - 所有新增的資料庫的複寫都會自動起始。 如需詳細資訊，請參閱[使用容錯移轉群組搭配單一資料庫的最佳作法](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools)。 
 
### <a name="monitor-the-preparation-process"></a>監視準備程式

您可以定期呼叫[AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) ，以監視從來源到目標的資料庫複寫。 的輸出物件`Get-AzSqlDatabaseFailoverGroup`包含**ReplicationState**的屬性： 
   - **ReplicationState = 2** （CATCH_UP）表示資料庫已同步處理，而且可以安全地進行故障切換。 
   - **ReplicationState = 0** （植入）表示尚未植入資料庫，而且嘗試容錯移轉將會失敗。 

### <a name="test-synchronization"></a>測試同步處理

一旦**ReplicationState**之後`2`，請使用次要端點`<fog-name>.secondary.database.windows.net`連接到每個資料庫或資料庫的子集，並對資料庫執行任何查詢，以確保連線能力、適當的安全性設定和資料複寫。 

### <a name="initiate-the-move"></a>起始移動

1. 使用次要端點`<fog-name>.secondary.database.windows.net`連接到目標伺服器。
1. 使用[AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) ，將次要受控實例切換為主要的「完整同步處理」。 這項作業將會成功，否則會復原。 
1. 確認命令已順利完成，方法是使用`nslook up <fog-name>.secondary.database.windows.net`來確定 DNS CNAME 專案指向目的地區域的 IP 位址。 如果切換命令失敗，則 CNAME 不會更新。 

### <a name="remove-the-source-databases"></a>移除源資料庫

移動完成後，請移除來源區域中的資源，以避免不必要的費用。 

1. 使用[AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)刪除容錯移轉群組。 
1. 為來源伺服器上的每個資料庫，使用[set-azsqldatabase 搭配](/powershell/module/az.sql/remove-azsqldatabase)刪除每個源資料庫。 這會自動終止異地複寫連結。 
1. 使用[AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)刪除來源伺服器。 
1. 移除金鑰保存庫、審核存放裝置容器、事件中樞、AAD 實例和其他相依資源，以停止對其計費。 

## <a name="move-elastic-pools"></a>移動彈性集區

### <a name="verify-prerequisites"></a>驗證必要條件 

1. 為每部來源伺服器建立目標邏輯伺服器。 
1. 使用[PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)設定具有正確例外狀況的防火牆。 
1. 使用正確的登入設定邏輯伺服器。 如果您不是訂用帳戶管理員或 SQL server 系統管理員，請與系統管理員合作以指派您所需的許可權。 如需詳細資訊，請參閱[如何在嚴重損壞修復後管理 AZURE SQL database 安全性](sql-database-geo-replication-security-config.md)。 
1. 如果您的資料庫使用 TDE 加密，並在 Azure key vault 中使用您自己的加密金鑰，請確定已在目的地區域中布建正確的加密資料。
1. 為每個來源彈性集區建立目標彈性集區，並確定已在相同的服務層級中建立集區，並具有相同的名稱和相同的大小。 
1. 如果已啟用資料庫層級的 audit，請將它停用，並改為啟用伺服器層級的審核。 在容錯移轉之後，資料庫層級的審核將需要跨區域流量，這在移動之後不需要或可能發生。 
1. 針對伺服器層級的審核，請確定：
    - 具有現有 audit 記錄的儲存體容器、Log Analytics 或事件中樞，會移至目的地區域。
    - 在目標伺服器上設定 Audit configuration。 如需詳細資訊，請參閱[SQL database 審核](sql-database-auditing.md)。
1. 如果您的實例具有長期保留原則（LTR），現有的 LTR 備份將會與目前的伺服器保持關聯。 因為目標伺服器不同，所以您可以使用來源伺服器存取來源區域中較舊的 LTR 備份，即使刪除伺服器也一樣。 

  > [!NOTE]
  > 這對於在主權雲端與公用區域之間移動的不足。 這類遷移需要將 LTR 備份移至目前不支援的目標伺服器。 

### <a name="prepare-to-move"></a>準備移動
 
1.  在來源邏輯伺服器上的每個彈性集區及其目標伺服器上對應的彈性集區之間，建立個別的[容錯移轉群組](sql-database-elastic-pool-failover-group-tutorial.md#3---create-the-failover-group)。 
1.  將集區中的所有資料庫新增至容錯移轉群組。 
    - 已新增的資料庫將會自動起始。 如需詳細資訊，請參閱具有彈性集區之[容錯移轉群組的最佳作法](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools)。 

  > [!NOTE]
  > 雖然您可以建立包含多個彈性集區的容錯移轉群組，但我們強烈建議您為每個集區建立個別的容錯移轉群組。 如果您需要移動多個彈性集區上的大量資料庫，您可以平行執行準備步驟，然後平行起始移動步驟。 相較于在相同的容錯移轉群組中擁有多個彈性集區，此程式將會更佳地調整，而且需要更少的時間 

### <a name="monitor-the-preparation-process"></a>監視準備程式

您可以定期呼叫[AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) ，以監視從來源到目標的資料庫複寫。 的輸出物件`Get-AzSqlDatabaseFailoverGroup`包含**ReplicationState**的屬性： 
   - **ReplicationState = 2** （CATCH_UP）表示資料庫已同步處理，而且可以安全地進行故障切換。 
   - **ReplicationState = 0** （植入）表示尚未植入資料庫，而且嘗試容錯移轉將會失敗。 

### <a name="test-synchronization"></a>測試同步處理
 
一旦**ReplicationState**之後`2`，請使用次要端點`<fog-name>.secondary.database.windows.net`連接到每個資料庫或資料庫的子集，並對資料庫執行任何查詢，以確保連線能力、適當的安全性設定和資料複寫。 

### <a name="initiate-the-move"></a>起始移動
 
1. 使用次要端點`<fog-name>.secondary.database.windows.net`連接到目標伺服器。
1. 使用[AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) ，將次要受控實例切換為主要的「完整同步處理」。 這項作業將會成功，否則會復原。 
1. 確認命令已順利完成，方法是使用`nslook up <fog-name>.secondary.database.windows.net`來確定 DNS CNAME 專案指向目的地區域的 IP 位址。 如果切換命令失敗，則 CNAME 不會更新。 

### <a name="remove-the-source-elastic-pools"></a>移除來源彈性集區
 
移動完成後，請移除來源區域中的資源，以避免不必要的費用。 

1. 使用[AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)刪除容錯移轉群組。
1. 在來源伺服器上使用[AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)刪除每個來源彈性集區。 
1. 使用[AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)刪除來源伺服器。 
1. 移除金鑰保存庫、審核存放裝置容器、事件中樞、AAD 實例和其他相依資源，以停止對其計費。 

## <a name="move-managed-instance"></a>移動受控實例

### <a name="verify-prerequisites"></a>驗證必要條件
 
1. 針對每個來源受控實例，在目的地區域中建立相同大小的目標受控實例。  
1. 設定受控實例的網路。 如需詳細資訊，請參閱[網路](sql-database-howto-managed-instance.md#network-configuration)設定。
1. 以正確的登入設定目標 master 資料庫。 如果您不是訂用帳戶管理員或 SQL server 系統管理員，請與系統管理員合作以指派您所需的許可權。 
1. 如果您的資料庫使用 TDE 加密，並在 Azure key vault 中使用您自己的加密金鑰，請確定來源和目的地區域中都有相同加密金鑰的 AKV 存在。 如需詳細資訊，請參閱[在 Azure Key Vault 中使用客戶管理的金鑰 TDE](transparent-data-encryption-byok-azure-sql.md)。
1. 如果已針對實例啟用 audit，請確定：
    - 具有現有記錄的儲存體容器或事件中樞會移至目的地區域。 
    - 已在目標實例上設定 Audit。 如需詳細資訊，請參閱[使用受控實例進行審核](sql-database-managed-instance-auditing.md)。
1. 如果您的實例具有長期保留原則（LTR），現有的 LTR 備份將會與目前的伺服器保持關聯。 因為目標伺服器不同，所以您可以使用來源伺服器存取來源區域中較舊的 LTR 備份，即使刪除伺服器也一樣。 

  > [!NOTE]
  > 這對於在主權雲端與公用區域之間移動的不足。 這類遷移需要將 LTR 備份移至目前不支援的目標伺服器。 

### <a name="prepare-resources"></a>準備資源

在每個來源實例和對應的目標實例之間建立容錯移轉群組。
    - 每個實例上的所有資料庫複寫都會自動起始。 如需詳細資訊，請參閱[自動容錯移轉群組](sql-database-auto-failover-group.md)。

 
### <a name="monitor-the-preparation-process"></a>監視準備程式

您可以定期呼叫[AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup?view=azps-2.3.2) ，以監視從來源到目標的資料庫複寫。 的輸出物件`Get-AzSqlDatabaseFailoverGroup`包含**ReplicationState**的屬性： 
   - **ReplicationState = 2** （CATCH_UP）表示資料庫已同步處理，而且可以安全地進行故障切換。 
   - **ReplicationState = 0** （植入）表示尚未植入資料庫，而且嘗試容錯移轉將會失敗。 

### <a name="test-synchronization"></a>測試同步處理

一旦**ReplicationState**之後`2`，請使用次要端點`<fog-name>.secondary.database.windows.net`連接到每個資料庫或資料庫的子集，並對資料庫執行任何查詢，以確保連線能力、適當的安全性設定和資料複寫。 

### <a name="initiate-the-move"></a>起始移動 

1. 使用次要端點`<fog-name>.secondary.database.windows.net`連接到目標伺服器。
1. 使用[AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup?view=azps-2.3.2) ，將次要受控實例切換為主要的「完整同步處理」。 這項作業將會成功，否則會復原。 
1. 確認命令已順利完成，方法是使用`nslook up <fog-name>.secondary.database.windows.net`來確定 DNS CNAME 專案指向目的地區域的 IP 位址。 如果切換命令失敗，則 CNAME 不會更新。 

### <a name="remove-the-source-managed-instances"></a>移除來源受控實例
移動完成後，請移除來源區域中的資源，以避免不必要的費用。 

1. 使用[AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)刪除容錯移轉群組。 這會卸載容錯移轉群組設定，並終止兩個實例之間的地理複寫連結。 
1. 使用[AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance)刪除來源受控實例。 
1. 移除資源群組中的任何其他資源，例如虛擬叢集、虛擬網路和安全性群組。 

## <a name="next-steps"></a>後續步驟 

在遷移之後[管理](sql-database-manage-after-migration.md)您的 Azure SQL Database。 

