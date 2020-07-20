---
title: 常見問題-在 Azure Vm 上備份 SQL Server 資料庫
description: 尋找有關使用 Azure 備份在 Azure Vm 上備份 SQL Server 資料庫的常見問題解答。
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 11657a5dda79fc550f4c07d4020d75c671335da4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84248255"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>有關在 Azure VM 備份上執行之 SQL Server 資料庫的常見問題

本文會回答有關備份在 Azure 虛擬機器（Vm）上執行的 SQL Server 資料庫，以及使用[Azure 備份](backup-overview.md)服務的常見問題。

## <a name="can-i-use-azure-backup-for-iaas-vm-as-well-as-sql-server-on-the-same-machine"></a>我可以在相同的電腦上使用適用于 IaaS VM 和 SQL Server 的 Azure 備份嗎？

是，您可以在相同的 VM 上同時擁有 VM 備份和 SQL 備份。 在此情況下，我們會內部在 VM 上觸發只複製完整備份，而不會截斷記錄。

## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>解決方案會重試或自動修復備份嗎？

在某些情況下，Azure 備份服務會觸發補救備份。 下列六個條件的任何一項都可能會進行自動修復：

- 如果記錄或差異備份因為 LSN 驗證錯誤而失敗，則會改為將下一個記錄或差異備份轉換成完整備份。
- 如果在記錄或差異備份之前未進行完整備份，則會將該記錄或差異備份改成轉換成完整備份。
- 如果最新的完整備份時間點超過15天，則會改為將下一個記錄或差異備份轉換成完整備份。
- 完成升級並啟動擴充功能之後，會重新觸發因延伸模組升級而取消的所有備份工作。
- 如果您選擇在還原期間覆寫資料庫，則下一個記錄/差異備份會失敗，而會觸發完整備份。
- 在因為資料庫復原模式變更而需要進行完整備份重設記錄鏈的情況下，系統會在下一個排程自動觸發完整的。

預設會針對所有使用者啟用自動修復功能;不過，如果您退出宣告，請執行下列動作：

- 在 SQL Server 實例的 [ *C:\Program Files\Azure 工作負載 Backup\bin* ] 資料夾中，建立或編輯檔案**上的ExtensionSettingsOverrides.js** 。
- 在的**ExtensionSettingsOverrides.js**中，設定 *{"EnableAutoHealer"： false}*。
- 儲存變更並關閉該檔案。
- 在 SQL Server 實例上，開啟 [工作] [**管理**]，然後重新開機**AzureWLBackupCoordinatorSvc**服務。

## <a name="can-i-control-how-many-concurrent-backups-run-on-the-sql-server"></a>我可以控制 SQL server 上執行的並行備份數目嗎？

是。 您可以限制備份原則的執行速率，以儘量降低對 SQL Server 執行個體的影響。 變更設定：

1. 在 SQL Server 實例上的 [ *C:\Program Files\Azure 工作負載 Backup\bin* ] 資料夾中，建立檔案*上的ExtensionSettingsOverrides.js* 。
2. 在 [ *ExtensionSettingsOverrides.js*檔案] 中，將 [ **DefaultBackupTasksThreshold** ] 設定變更為較低的值（例如，5）。 <br>
  `{"DefaultBackupTasksThreshold": 5}`
<br>
DefaultBackupTasksThreshold 的預設值為**20**。

3. 儲存變更並關閉該檔案。
4. 在 SQL Server 執行個體上，開啟 [工作管理員]****。 重新啟動 **AzureWLBackupCoordinatorSvc** 服務。<br/> <br/>
 雖然此方法有助於備份應用程式耗用大量資源，但是 SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor?view=sql-server-2017)是更通用的方法，可指定連入應用程式要求所能使用的 CPU、實體 IO 和記憶體數量限制。

> [!NOTE]
> 在 UX 中，您仍然可以在任何指定的時間排程多個備份，不過，它們會在根據上述範例的滑動視窗中進行處理，例如5。

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>是否可以從次要複本執行完整備份？

根據 SQL 的限制，您可以在次要複本上執行 [只複製完整備份];不過，不允許完整備份。

## <a name="can-i-protect-availability-groups-on-premises"></a>我可以保護內部部署的可用性群組嗎？

否。 Azure 備份保護在 Azure 中執行 SQL Server 資料庫。 如果可用性群組（AG）分散在 Azure 和內部部署機器之間，只有在主要複本在 Azure 中執行時，才可以保護 AG。 此外，Azure 備份只會保護在與復原服務保存庫相同的 Azure 區域中執行的節點。

## <a name="can-i-protect-availability-groups-across-regions"></a>我可以跨區域保護可用性群組嗎？

Azure 備份復原服務保存庫可以偵測並保護與保存庫位於相同區域中的所有節點。 如果您的 SQL Server Always On 可用性群組跨越多個 Azure 區域，請從具有主要節點的區域設定備份。 「Azure 備份」可以依據您的備份喜好設定，偵測並保護可用性群組中的所有資料庫。 當您的備份喜好設定不符合時，備份會失敗，而且您會收到失敗警示。

## <a name="do-successful-backup-jobs-create-alerts"></a>成功的備份作業是否會建立警示？

否。 成功的備份作業不會產生警示。 只有失敗的備份作業會傳送警示。 [這裡](backup-azure-monitoring-built-in-monitor.md)詳載了入口網站警示的行為。 不過，如果您有興趣取得成功作業的警示，您可以使用 Azure 監視器來進行[監視](backup-azure-monitoring-use-azuremonitor.md)。

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>是否可以在 [備份作業] 功能表中看到已排程的備份作業？

[**備份作業**] 功能表只會顯示隨選備份作業。 針對排程工作，使用[Azure 監視器的監視](backup-azure-monitoring-use-azuremonitor.md)。

## <a name="are-future-databases-automatically-added-for-backup"></a>未來的資料庫會自動加入以進行備份嗎？

是，您可以透過[自動保護](backup-sql-server-database-azure-vms.md#enable-auto-protection)來達成此功能。  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>如果我從 autoprotected 實例中刪除資料庫，備份會發生什麼事？

如果從 autoprotected 實例中卸載資料庫，仍然會嘗試資料庫備份。 這表示已刪除的資料庫會開始在 [備份項目] 下方顯示為狀況不良，且仍受到保護。

停止保護此資料庫的正確方式，是在此資料庫上執行 [**刪除資料**] 的 [**停止備份**]。  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>如果我停止 autoprotected 資料庫的備份作業，會有何行為？

如果您**使用 [保留資料] 停止備份**，則不會進行任何未來的備份，而且現有的復原點仍會保持不變。 資料庫仍會被視為受保護，並顯示在**備份專案**底下。

如果您**停止備份並刪除資料**，將不會進行任何未來的備份，也會刪除現有的復原點。 資料庫將被視為未受保護，並顯示在 [設定備份] 中的實例底下。 不過，不同于其他可手動選取或可以取得 autoprotected 的受保護資料庫，此資料庫會呈現灰色且無法選取。 重新保護此資料庫的唯一方法是在實例上停用自動保護。 您現在可以選取此資料庫，並在其上設定保護，或重新啟用實例上的自動保護。

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>如果我在保護資料庫之後變更其名稱，會有何種行為？

重新命名的資料庫會被視為新的資料庫。 因此，服務會將這種情況視為資料庫找不到，而且備份失敗。

您可以選取現在已重新命名的資料庫，並設定其保護。 如果已在實例上啟用自動保護，則會自動偵測並保護重新命名的資料庫。

## <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>為什麼我看不到 autoprotected 實例的新增資料庫？

您[加入至 autoprotected 實例](backup-sql-server-database-azure-vms.md#enable-auto-protection)的資料庫可能不會立即出現在 [受保護的專案] 底下。 這是因為探索通常每隔 8 小時才會執行一次。 不過，如果您藉由選取 [重新探索資料庫] 來手動執行**探索，則**可以立即探索並保護新的資料庫，如下圖所示：

  ![手動探索新增的資料庫](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="next-steps"></a>後續步驟

瞭解如何備份在 Azure VM 上[執行的 SQL Server 資料庫](backup-azure-sql-database.md)。
