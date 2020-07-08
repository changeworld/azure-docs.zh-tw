---
title: 將資源移至新區域
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: 瞭解如何將您的資料庫或受控實例移至另一個區域。
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 46b95c438830a488494d50308d71a115d6f0da42
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85982137"
---
# <a name="move-resources-to-new-region---azure-sql-database--azure-sql-managed-instance"></a>將資源移至新區域-Azure SQL Database & Azure SQL 受控執行個體
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

本文會教您如何將您的資料庫或受控實例移至新區域的一般工作流程。

## <a name="overview"></a>總覽

在許多情況下，您會想要將現有的資料庫或受控實例從一個區域移至另一個區域。 例如，您要將您的企業擴充到新的區域，並想要針對新的客戶群進行優化。 或者，您必須將作業移至不同的區域，以符合合規性因素。 或 Azure 發行了新的區域，可提供更好的鄰近性並改善客戶體驗。  

本文提供將資源移至不同區域的一般工作流程。 工作流程是由下列步驟所組成：

1. 確認移動的必要條件。
1. 準備移動範圍內的資源。
1. 監視準備程式。
1. 測試移動進程。
1. 起始實際移動。
1. 從來源區域移除資源。

> [!NOTE]
> 本文適用于 Azure 公用雲端內或相同主權雲端內的遷移。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="move-a-database"></a>移動資料庫

### <a name="verify-prerequisites"></a>驗證必要條件

1. 建立每部來源伺服器的目標伺服器。
1. 使用[PowerShell](scripts/create-and-configure-database-powershell.md)設定具有正確例外狀況的防火牆。  
1. 以正確的登入設定伺服器。 如果您不是訂用帳戶管理員或 SQL server 系統管理員，請與系統管理員合作以指派您所需的許可權。 如需詳細資訊，請參閱[如何在嚴重損壞修復後管理 Azure SQL Database 安全性](active-geo-replication-security-configure.md)。
1. 如果您的資料庫使用透明資料加密進行加密，並在 Azure Key Vault 中使用您自己的加密金鑰，請確定已在目的地區域中布建正確的加密資料。 如需詳細資訊，請參閱[Azure Key Vault 中的 AZURE SQL 透明資料加密與客戶管理的金鑰](transparent-data-encryption-byok-overview.md)。
1. 如果已啟用資料庫層級的 audit，請將它停用，並改為啟用伺服器層級的審核。 在容錯移轉之後，資料庫層級的審核將需要跨區域流量，這在移動之後不需要或無法使用。
1. 針對伺服器層級的審核，請確定：
   - 具有現有 audit 記錄的儲存體容器、Log Analytics 或事件中樞，會移至目的地區域。
   - 在目標伺服器上設定了 [審核]。 如需詳細資訊，請參閱[開始使用 SQL Database 的審核](../../azure-sql/database/auditing-overview.md)。
1. 如果您的實例具有長期保留原則（LTR），現有的 LTR 備份將會與目前的伺服器保持關聯。 因為目標伺服器不同，所以您可以使用來源伺服器存取來源區域中較舊的 LTR 備份，即使刪除伺服器也一樣。

      > [!NOTE]
      > 這對於在主權雲端與公用區域之間移動的不足。 這類遷移需要將 LTR 備份移至目前不支援的目標伺服器。

### <a name="prepare-resources"></a>準備資源

1. 在來源伺服器與目標伺服器之間建立[容錯移轉群組](failover-group-add-single-database-tutorial.md#2---create-the-failover-group)。  
1. 新增您想要移至容錯移轉群組的資料庫。
  
    所有新增的資料庫的複寫都會自動起始。 如需詳細資訊，請參閱[使用容錯移轉群組搭配單一資料庫的最佳作法](auto-failover-group-overview.md#best-practices-for-sql-database)。

### <a name="monitor-the-preparation-process"></a>監視準備程式

您可以定期呼叫[AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) ，以監視從來源到目標的資料庫複寫。 的輸出物件 `Get-AzSqlDatabaseFailoverGroup` 包含**ReplicationState**的屬性：

- **ReplicationState = 2** （CATCH_UP）表示資料庫已同步處理，而且可以安全地進行故障切換。
- **ReplicationState = 0** （植入）表示尚未植入資料庫，而且嘗試故障切換將會失敗。

### <a name="test-synchronization"></a>測試同步處理

在**ReplicationState**之後 `2` ，使用次要端點連接到每個資料庫或資料庫子集， `<fog-name>.secondary.database.windows.net` 並對資料庫執行任何查詢，以確保連線能力、適當的安全性設定和資料複寫。

### <a name="initiate-the-move"></a>起始移動

1. 使用次要端點連接到目標伺服器 `<fog-name>.secondary.database.windows.net` 。
1. 使用[AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) ，將次要受控實例切換為主要的「完整同步處理」。 此作業將會成功，否則會復原。
1. 確認命令已順利完成，方法是使用 `nslook up <fog-name>.secondary.database.windows.net` 來確定 DNS CNAME 專案指向目的地區域的 IP 位址。 如果切換命令失敗，則 CNAME 不會更新。

### <a name="remove-the-source-databases"></a>移除源資料庫

移動完成後，請移除來源區域中的資源，以避免不必要的費用。

1. 使用[AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)刪除容錯移轉群組。
1. 為來源伺服器上的每個資料庫，使用[set-azsqldatabase 搭配](/powershell/module/az.sql/remove-azsqldatabase)刪除每個源資料庫。 這會自動終止異地複寫連結。
1. 使用[AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)刪除來源伺服器。
1. 移除「金鑰保存庫」、「審核存放裝置容器」、「事件中樞」、Azure Active Directory （Azure AD）實例，以及其他要停止計費的相依資源。

## <a name="move-elastic-pools"></a>移動彈性集區

### <a name="verify-prerequisites"></a>驗證必要條件

1. 建立每部來源伺服器的目標伺服器。
1. 使用[PowerShell](scripts/create-and-configure-database-powershell.md)設定具有正確例外狀況的防火牆。
1. 以正確的登入設定伺服器。 如果您不是訂用帳戶管理員或伺服器管理員，請與系統管理員合作以指派您所需的許可權。 如需詳細資訊，請參閱[如何在嚴重損壞修復後管理 Azure SQL Database 安全性](active-geo-replication-security-configure.md)。
1. 如果您的資料庫使用透明資料加密進行加密，並在 Azure Key Vault 中使用您自己的加密金鑰，請確定已在目的地區域中布建正確的加密資料。
1. 為每個來源彈性集區建立目標彈性集區，並確定已在相同的服務層級中建立集區，並具有相同的名稱和相同的大小。
1. 如果已啟用資料庫層級的 audit，請將它停用，並改為啟用伺服器層級的審核。 在容錯移轉之後，資料庫層級的審核將需要跨區域流量，這在移動之後不需要或可能發生。
1. 針對伺服器層級的審核，請確定：
    - 具有現有 audit 記錄的儲存體容器、Log Analytics 或事件中樞，會移至目的地區域。
    - 在目標伺服器上設定 Audit configuration。 如需詳細資訊，請參閱[SQL Database 的審核](../../azure-sql/database/auditing-overview.md)。
1. 如果您的實例具有長期保留原則（LTR），現有的 LTR 備份將會與目前的伺服器保持關聯。 因為目標伺服器不同，所以您可以使用來源伺服器存取來源區域中較舊的 LTR 備份，即使已刪除伺服器也一樣。

      > [!NOTE]
      > 這對於在主權雲端與公用區域之間移動的不足。 這類遷移需要將 LTR 備份移至目前不支援的目標伺服器。

### <a name="prepare-to-move"></a>準備移動

1. 在來源伺服器上的每個彈性集區及其目標伺服器上的對應彈性集區之間，建立個別的[容錯移轉群組](failover-group-add-elastic-pool-tutorial.md#3---create-the-failover-group)。
1. 將集區中的所有資料庫新增至容錯移轉群組。

    已新增的資料庫將會自動起始。 如需詳細資訊，請參閱具有彈性集區之[容錯移轉群組的最佳作法](auto-failover-group-overview.md#best-practices-for-sql-database)。

      > [!NOTE]
      > 雖然您可以建立包含多個彈性集區的容錯移轉群組，但我們強烈建議您為每個集區建立個別的容錯移轉群組。 如果您需要移動多個彈性集區上的大量資料庫，您可以平行執行準備步驟，然後平行起始移動步驟。 相較于在相同的容錯移轉群組中擁有多個彈性集區，此程式將會更佳地調整，而且需要更少的時間

### <a name="monitor-the-preparation-process"></a>監視準備程式

您可以定期呼叫[AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) ，以監視從來源到目標的資料庫複寫。 的輸出物件 `Get-AzSqlDatabaseFailoverGroup` 包含**ReplicationState**的屬性：

- **ReplicationState = 2** （CATCH_UP）表示資料庫已同步處理，而且可以安全地進行故障切換。
- **ReplicationState = 0** （植入）表示尚未植入資料庫，而且嘗試故障切換將會失敗。

### <a name="test-synchronization"></a>測試同步處理

一旦**ReplicationState**之後 `2` ，使用次要端點連接到每個資料庫或資料庫子集， `<fog-name>.secondary.database.windows.net` 並對資料庫執行任何查詢，以確保連線能力、適當的安全性設定和資料複寫。

### <a name="initiate-the-move"></a>起始移動

1. 使用次要端點連接到目標伺服器 `<fog-name>.secondary.database.windows.net` 。
1. 使用[AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) ，將次要受控實例切換為主要的「完整同步處理」。 這項作業將會成功，否則會復原。
1. 確認命令已順利完成，方法是使用 `nslook up <fog-name>.secondary.database.windows.net` 來確定 DNS CNAME 專案指向目的地區域的 IP 位址。 如果切換命令失敗，則 CNAME 不會更新。

### <a name="remove-the-source-elastic-pools"></a>移除來源彈性集區

移動完成後，請移除來源區域中的資源，以避免不必要的費用。

1. 使用[AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)刪除容錯移轉群組。
1. 在來源伺服器上使用[AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)刪除每個來源彈性集區。
1. 使用[AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)刪除來源伺服器。
1. 移除金鑰保存庫、審核存放裝置容器、事件中樞、Azure AD 實例及其他相依資源，以停止對其計費。

## <a name="move-a-managed-instance"></a>移動受控實例

### <a name="verify-prerequisites"></a>驗證必要條件

1. 針對每個來源受控實例，在目的地區域中建立相同大小的 SQL 受控執行個體目標實例。  
1. 設定受控實例的網路。 如需詳細資訊，請參閱[網路](../managed-instance/how-to-content-reference-guide.md#network-configuration)設定。
1. 以正確的登入設定目標 master 資料庫。 如果您不是訂用帳戶或 SQL 受控執行個體系統管理員，請與系統管理員合作以指派您所需的許可權。
1. 如果您的資料庫使用透明資料加密進行加密，並在 Azure Key Vault 中使用您自己的加密金鑰，請確定具有相同加密金鑰的 Azure Key Vault 同時存在於來源和目的地區域中。 如需詳細資訊，請參閱[Azure Key Vault 中客戶管理的金鑰的透明資料加密](transparent-data-encryption-byok-overview.md)。
1. 如果已針對受控實例啟用 audit，請確定：
    - 具有現有記錄的儲存體容器或事件中樞會移至目的地區域。
    - 已在目標實例上設定 Audit。 如需詳細資訊，請參閱[使用 SQL 受控執行個體進行審核](../managed-instance/auditing-configure.md)。
1. 如果您的實例具有長期保留原則（LTR），現有的 LTR 備份將會與目前的實例保持關聯。 因為目標實例不同，所以您可以使用來源實例，在來源區域中存取較舊的 LTR 備份，即使已刪除實例也一樣。

  > [!NOTE]
  > 這對於在主權雲端與公用區域之間移動的不足。 這類遷移需要將 LTR 備份移至目前不支援的目標實例。

### <a name="prepare-resources"></a>準備資源

在每個來源受控實例與 SQL 受控執行個體的對應目標實例之間建立容錯移轉群組。

每個實例上的所有資料庫複寫都會自動起始。 如需詳細資訊，請參閱[自動容錯移轉群組](auto-failover-group-overview.md)。

### <a name="monitor-the-preparation-process"></a>監視準備程式

您可以定期呼叫[AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup?view=azps-2.3.2) ，以監視從來源到目標的資料庫複寫。 的輸出物件 `Get-AzSqlDatabaseFailoverGroup` 包含**ReplicationState**的屬性：

- **ReplicationState = 2** （CATCH_UP）表示資料庫已同步處理，而且可以安全地進行故障切換。
- **ReplicationState = 0** （植入）表示尚未植入資料庫，而且嘗試故障切換將會失敗。

### <a name="test-synchronization"></a>測試同步處理

一旦**ReplicationState**之後 `2` ，請使用次要端點連接到每個資料庫或資料庫的子集， `<fog-name>.secondary.database.windows.net` 並對資料庫執行任何查詢，以確保連線能力、適當的安全性設定和資料複寫。

### <a name="initiate-the-move"></a>起始移動

1. 使用次要端點連接到目標受控實例 `<fog-name>.secondary.database.windows.net` 。
1. 使用[AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup?view=azps-2.3.2) ，將次要受控實例切換為主要的「完整同步處理」。 此作業將會成功，或將會復原。
1. 確認命令已順利完成，方法是使用 `nslook up <fog-name>.secondary.database.windows.net` 來確定 DNS CNAME 專案指向目的地區域的 IP 位址。 如果切換命令失敗，則 CNAME 不會更新。

### <a name="remove-the-source-managed-instances"></a>移除來源受控實例

移動完成後，請移除來源區域中的資源，以避免不必要的費用。

1. 使用[AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)刪除容錯移轉群組。 這會卸載容錯移轉群組設定，並終止兩個實例之間的地理複寫連結。
1. 使用[AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance)刪除來源受控實例。
1. 移除資源群組中的任何其他資源，例如虛擬叢集、虛擬網路和安全性群組。

## <a name="next-steps"></a>後續步驟

在遷移之後[管理](manage-data-after-migrating-to-database.md)您的資料庫。
