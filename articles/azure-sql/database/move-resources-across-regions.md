---
title: 將資源移至新區域
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: 瞭解如何將您的資料庫或受控實例移至另一個區域。
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: ae6c87c9eabea837ba9c43676d4ca712caa385cb
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594159"
---
# <a name="move-resources-to-new-region---azure-sql-database--azure-sql-managed-instance"></a>將資源移至新的區域-Azure SQL Database & Azure SQL 受控執行個體
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

本文會教您如何將資料庫或受控實例移至新區域的一般工作流程。

## <a name="overview"></a>總覽

在各種情況下，您會想要將現有的資料庫或受控實例從一個區域移至另一個區域。 例如，您要將企業擴充到新的區域，並想要針對新的客戶群進行優化。 或者，基於合規性理由，您需要將作業移至不同的區域。 或 Azure 發行了新的區域，以提供更好的鄰近性並改善客戶體驗。  

本文提供將資源移至不同區域的一般工作流程。 工作流程包含下列步驟：

1. 確認移動的必要條件。
1. 準備在範圍中移動資源。
1. 監視準備程式。
1. 測試移動流程。
1. 起始實際的移動。
1. 移除來源區域中的資源。

> [!NOTE]
> 本文適用于 Azure 公用雲端內或相同主權雲端內的遷移。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="move-a-database"></a>移動資料庫

### <a name="verify-prerequisites"></a>驗證必要條件

1. 為每部來源伺服器建立目標伺服器。
1. 使用 [PowerShell](scripts/create-and-configure-database-powershell.md)以正確的例外狀況設定防火牆。  
1. 使用正確的登入設定伺服器。 如果您不是訂用帳戶管理員或 SQL server 系統管理員，請與系統管理員合作以指派您所需的許可權。 如需詳細資訊，請參閱 [如何在嚴重損壞修復之後管理 Azure SQL Database 安全性](active-geo-replication-security-configure.md)。
1. 如果您的資料庫使用透明資料加密進行加密，並在 Azure Key Vault 中使用您自己的加密金鑰，請確定已在目的地區域中布建正確的加密資料。 如需詳細資訊，請參閱 [Azure Key Vault 中的使用客戶管理的金鑰進行 AZURE SQL 透明資料加密](transparent-data-encryption-byok-overview.md)。
1. 如果已啟用資料庫層級審核，請停用它，並改為啟用伺服器層級審核。 在容錯移轉之後，資料庫層級的審核將需要跨區域流量，這在移動之後不需要或不可能發生。
1. 若為伺服器層級的審核，請確定：
   - 具有現有審核記錄的儲存體容器、Log Analytics 或事件中樞會移至目的地區域。
   - 在目標伺服器上設定了審核。 如需詳細資訊，請參閱 [開始使用 SQL Database 的審核](../../azure-sql/database/auditing-overview.md)。
1. 如果您的實例具有長期保留原則 (LTR) ，現有的 LTR 備份仍會與目前的伺服器相關聯。 因為目標伺服器不同，所以您可以使用來源伺服器存取來源區域中較舊的 LTR 備份，即使伺服器已刪除也一樣。

      > [!NOTE]
      > 這將不足以在主權雲端與公用區域之間移動。 這類遷移需要將 LTR 備份移至目標伺服器，但目前不支援此功能。

### <a name="prepare-resources"></a>準備資源

1. 在來源伺服器與目標伺服器之間建立 [容錯移轉群組](failover-group-add-single-database-tutorial.md#2---create-the-failover-group) 。  
1. 新增您想要移至容錯移轉群組的資料庫。
  
    所有新增資料庫的複寫都會自動起始。 如需詳細資訊，請參閱 [使用容錯移轉群組搭配單一資料庫的最佳作法](auto-failover-group-overview.md#best-practices-for-sql-database)。

### <a name="monitor-the-preparation-process"></a>監視準備程式

您可以定期呼叫 [AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) ，以監視從來源到目標的資料庫複寫。 的輸出物件 `Get-AzSqlDatabaseFailoverGroup` 包括 **ReplicationState** 的屬性：

- **ReplicationState = 2** (CATCH_UP) 表示資料庫已同步處理，而且可以安全地進行容錯移轉。
- **ReplicationState = 0** (植入) 表示資料庫尚未植入，嘗試容錯移轉將會失敗。

### <a name="test-synchronization"></a>測試同步處理

**ReplicationState** 之後 `2` ，請使用次要端點連接到每個資料庫或資料庫子集， `<fog-name>.secondary.database.windows.net` 並對資料庫執行任何查詢，以確保連線能力、適當的安全性設定和資料複寫。

### <a name="initiate-the-move"></a>起始移動

1. 使用次要端點連接到目標伺服器 `<fog-name>.secondary.database.windows.net` 。
1. 使用 [切換 AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) ，將次要受控實例切換為具有完整同步處理的主要複本。 此作業將會成功，或將會回復。
1. 使用確認命令已順利完成， `nslook up <fog-name>.secondary.database.windows.net` 以確定 DNS CNAME 專案指向目的地區域 IP 位址。 如果交換器命令失敗，則不會更新 CNAME。

### <a name="remove-the-source-databases"></a>移除源資料庫

移動完成之後，請移除來源區域中的資源，以避免不必要的費用。

1. 使用 [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)刪除容錯移轉群組。
1. 針對來源伺服器上的每個資料庫，使用 [>set-azsqldatabase](/powershell/module/az.sql/remove-azsqldatabase) 刪除每個源資料庫。 這會自動終止異地複寫連結。
1. 使用 [Remove->new-azsqlserver](/powershell/module/az.sql/remove-azsqlserver)刪除來源伺服器。
1. 移除金鑰保存庫、audit storage 容器、事件中樞、Azure Active Directory (Azure AD) 實例和其他相依資源，以停止計費。

## <a name="move-elastic-pools"></a>移動彈性集區

### <a name="verify-prerequisites"></a>驗證必要條件

1. 為每部來源伺服器建立目標伺服器。
1. 使用 [PowerShell](scripts/create-and-configure-database-powershell.md)以正確的例外狀況設定防火牆。
1. 使用正確的登入設定伺服器。 如果您不是訂用帳戶管理員或伺服器管理員，請與系統管理員合作以指派您所需的許可權。 如需詳細資訊，請參閱 [如何在嚴重損壞修復之後管理 Azure SQL Database 安全性](active-geo-replication-security-configure.md)。
1. 如果您的資料庫使用透明資料加密進行加密，並在 Azure Key Vault 中使用您自己的加密金鑰，請確定已在目的地區域中布建正確的加密資料。
1. 為每個來源彈性集區建立目標彈性集區，並確定集區是建立在相同的服務層級中，並具有相同的名稱和相同的大小。
1. 如果已啟用資料庫層級審核，請停用它，並改為啟用伺服器層級審核。 在容錯移轉之後，資料庫層級的審核將需要跨區域流量（不需要），或在移動之後可能發生。
1. 若為伺服器層級的審核，請確定：
    - 具有現有審核記錄的儲存體容器、Log Analytics 或事件中樞會移至目的地區域。
    - Audit configuration 是在目標伺服器上設定。 如需詳細資訊，請參閱 [SQL Database 的審核](../../azure-sql/database/auditing-overview.md)。
1. 如果您的實例具有長期保留原則 (LTR) ，現有的 LTR 備份仍會與目前的伺服器相關聯。 因為目標伺服器不同，所以您可以在來源區域中使用來源伺服器來存取較舊的 LTR 備份，即使伺服器已刪除也一樣。

      > [!NOTE]
      > 這將不足以在主權雲端與公用區域之間移動。 這類遷移需要將 LTR 備份移至目標伺服器，但目前不支援此功能。

### <a name="prepare-to-move"></a>準備移動

1. 在來源伺服器上的每個彈性集區，以及在目標伺服器上對應的彈性集區之間，建立個別的 [容錯移轉群組](failover-group-add-elastic-pool-tutorial.md#3---create-the-failover-group) 。
1. 將集區中的所有資料庫新增至容錯移轉群組。

    已新增資料庫的複寫將會自動起始。 如需詳細資訊，請參閱 [具有彈性集區的容錯移轉群組最佳作法](auto-failover-group-overview.md#best-practices-for-sql-database)。

      > [!NOTE]
      > 雖然您可以建立包含多個彈性集區的容錯移轉群組，但強烈建議您為每個集區建立個別的容錯移轉群組。 如果您有大量的資料庫跨越多個需要移動的彈性集區，您可以平行執行準備步驟，然後以平行方式起始移動步驟。 相較于在相同的容錯移轉群組中具有多個彈性集區，此程式將會更好地調整規模，而且會花費較少

### <a name="monitor-the-preparation-process"></a>監視準備程式

您可以定期呼叫 [AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) ，以監視從來源到目標的資料庫複寫。 的輸出物件 `Get-AzSqlDatabaseFailoverGroup` 包括 **ReplicationState** 的屬性：

- **ReplicationState = 2** (CATCH_UP) 表示資料庫已同步處理，而且可以安全地進行容錯移轉。
- **ReplicationState = 0** (植入) 表示資料庫尚未植入，嘗試容錯移轉將會失敗。

### <a name="test-synchronization"></a>測試同步處理

一旦 **ReplicationState** 之後 `2` ，請使用次要端點連接到每個資料庫或資料庫子集， `<fog-name>.secondary.database.windows.net` 並對資料庫執行任何查詢，以確保連線能力、適當的安全性設定和資料複寫。

### <a name="initiate-the-move"></a>起始移動

1. 使用次要端點連接到目標伺服器 `<fog-name>.secondary.database.windows.net` 。
1. 使用 [切換 AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) ，將次要受控實例切換為具有完整同步處理的主要複本。 這項作業可能會成功，或將會回復。
1. 使用確認命令已順利完成， `nslook up <fog-name>.secondary.database.windows.net` 以確定 DNS CNAME 專案指向目的地區域 IP 位址。 如果交換器命令失敗，則不會更新 CNAME。

### <a name="remove-the-source-elastic-pools"></a>移除來源彈性集區

移動完成之後，請移除來源區域中的資源，以避免不必要的費用。

1. 使用 [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)刪除容錯移轉群組。
1. 使用 [AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)來刪除來源伺服器上的每個來源彈性集區。
1. 使用 [Remove->new-azsqlserver](/powershell/module/az.sql/remove-azsqlserver)刪除來源伺服器。
1. 移除金鑰保存庫、audit storage 容器、事件中樞、Azure AD 實例和其他相依資源，以停止對其收費。

## <a name="move-a-managed-instance"></a>移動受控實例

### <a name="verify-prerequisites"></a>驗證必要條件

1. 針對每個來源受控實例，在目的地區域中建立相同大小的 SQL 受控執行個體目標實例。  
1. 設定受控實例的網路。 如需詳細資訊，請參閱 [網路](../managed-instance/how-to-content-reference-guide.md#network-configuration)設定。
1. 使用正確的登入設定目標 master 資料庫。 如果您不是訂用帳戶或 SQL 受控執行個體系統管理員，請與系統管理員合作以指派您所需的許可權。
1. 如果您的資料庫使用透明資料加密進行加密，並在 Azure Key Vault 中使用您自己的加密金鑰，請確定來源和目的地區域中都有相同加密金鑰的 Azure Key Vault。 如需詳細資訊，請參閱 [Azure Key Vault 中客戶管理的金鑰的透明資料加密](transparent-data-encryption-byok-overview.md)。
1. 如果已針對受控實例啟用 audit，請確定：
    - 具有現有記錄檔的儲存體容器或事件中樞會移至目的地區域。
    - 已在目標實例上設定 Audit。 如需詳細資訊，請參閱 [使用 SQL 受控執行個體進行審核](../managed-instance/auditing-configure.md)。
1. 如果您的實例具有長期保留原則 (LTR) ，現有的 LTR 備份仍會與目前的實例相關聯。 因為目標實例不同，所以您可以在來源區域中使用來源實例來存取較舊的 LTR 備份，即使該實例已刪除也一樣。

  > [!NOTE]
  > 這將不足以在主權雲端與公用區域之間移動。 這類遷移需要將 LTR 備份移至目前不支援的目標實例。

### <a name="prepare-resources"></a>準備資源

在每個來源受控實例和 SQL 受控執行個體的對應目標實例之間建立容錯移轉群組。

系統會自動起始每個實例上的所有資料庫複寫。 如需詳細資訊，請參閱 [自動容錯移轉群組](auto-failover-group-overview.md)。

### <a name="monitor-the-preparation-process"></a>監視準備程式

您可以定期呼叫 [AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) ，以監視從來源到目標的資料庫複寫。 的輸出物件 `Get-AzSqlDatabaseFailoverGroup` 包括 **ReplicationState** 的屬性：

- **ReplicationState = 2** (CATCH_UP) 表示資料庫已同步處理，而且可以安全地進行容錯移轉。
- **ReplicationState = 0** (植入) 表示資料庫尚未植入，嘗試容錯移轉將會失敗。

### <a name="test-synchronization"></a>測試同步處理

**ReplicationState** 之後 `2` ，請使用次要端點連接到每個資料庫或資料庫子集， `<fog-name>.secondary.database.windows.net` 並對資料庫執行任何查詢，以確保連線能力、適當的安全性設定和資料複寫。

### <a name="initiate-the-move"></a>起始移動

1. 使用次要端點連接到目標受控實例 `<fog-name>.secondary.database.windows.net` 。
1. 使用 [切換 AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) ，將次要受控實例切換為具有完整同步處理的主要複本。 此作業將會成功，或將會回復。
1. 使用確認命令已順利完成， `nslook up <fog-name>.secondary.database.windows.net` 以確定 DNS CNAME 專案指向目的地區域 IP 位址。 如果交換器命令失敗，則不會更新 CNAME。

### <a name="remove-the-source-managed-instances"></a>移除來源受控實例

移動完成之後，請移除來源區域中的資源，以避免不必要的費用。

1. 使用 [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)刪除容錯移轉群組。 這會捨棄容錯移轉群組設定，並終止兩個實例之間的異地複寫連結。
1. 使用 [Remove-new-azsqlinstance](/powershell/module/az.sql/remove-azsqlinstance)刪除來源受控實例。
1. 移除資源群組中的任何其他資源，例如虛擬叢集、虛擬網路和安全性群組。

## <a name="next-steps"></a>後續步驟

在遷移之後[管理](manage-data-after-migrating-to-database.md)您的資料庫。
