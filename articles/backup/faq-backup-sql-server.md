---
title: 常見問題-備份 Azure Vm 上的 SQL Server 資料庫
description: 尋找有關使用 Azure 備份備份 Azure Vm 上的 SQL Server 資料庫的常見問題解答。
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: c1efbc44ae1e4bea96c063e4dc734f1cc53a42ac
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2020
ms.locfileid: "89180094"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>有關在 Azure VM 備份上執行的 SQL Server 資料庫的常見問題

本文將針對在 Azure 虛擬機器上執行的 SQL Server 資料庫，提供備份 (Vm) 並使用 [Azure 備份](backup-overview.md) 服務的相關常見問題。

## <a name="can-i-use-azure-backup-for-iaas-vm-as-well-as-sql-server-on-the-same-machine"></a>是否可以在相同電腦上使用 IaaS VM 的 Azure 備份以及 SQL Server？

是的，您可以在相同的 VM 上同時有 VM 備份和 SQL 備份。 在此情況下，我們會在內部觸發 VM 上僅複製的完整備份，而不會截斷記錄。

## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>解決方案會重試或自動修復備份嗎？

在某些情況下，Azure 備份服務會觸發補救備份。 針對下面所述的六個狀況，可能會發生自動修復：

- 如果記錄檔或差異備份因為 LSN 驗證錯誤而失敗，則會改為將下一個記錄或差異備份轉換成完整備份。
- 如果在記錄或差異備份之前未進行完整備份，該記錄或差異備份會改為轉換成完整備份。
- 如果最新的完整備份時間點早于15天，則會改為將下一個記錄或差異備份轉換成完整備份。
- 升級完成並啟動擴充功能之後，會重新觸發所有因為擴充功能更新而取消的備份工作。
- 如果您選擇在還原期間覆寫資料庫，則下一個記錄/差異備份會失敗，而會改為觸發完整備份。
- 如果需要完整備份以重設記錄鏈（因為資料庫復原模式變更），則會在下一個排程時自動觸發完整備份。

預設會為所有使用者啟用自動修復功能。 但是，如果您退出宣告它，請執行下列步驟：

- 在 SQL Server 實例上的 [ *C:\Program Files\Azure 工作負載 Backup\bin* ] 資料夾中，建立或編輯檔案的 **ExtensionSettingsOverrides.js** 。
- 在 [ **ExtensionSettingsOverrides.js**] 中，設定 *{"EnableAutoHealer"： false}*。
- 儲存變更並關閉該檔案。
- 在 SQL Server 實例上，開啟 [ **任務管理** ]，然後重新開機 **AzureWLBackupCoordinatorSvc** 服務。

## <a name="can-i-control-how-many-concurrent-backups-run-on-the-sql-server"></a>我可以控制要在 SQL server 上執行多少並行備份？

是。 您可以限制備份原則的執行速率，以儘量降低對 SQL Server 執行個體的影響。 變更設定：

1. 在 SQL Server 實例的 [ *C:\Program Files\Azure 工作負載 Backup\bin* ] 資料夾中，建立檔案的 *ExtensionSettingsOverrides.js* 。
2. 在 [ *ExtensionSettingsOverrides.js* 檔案] 中，將 **>defaultbackuptasksthreshold** 設定變更為較低的值 (例如 5) 。 <br>
  `{"DefaultBackupTasksThreshold": 5}`
<br>
>defaultbackuptasksthreshold 的預設值為 **20**。

3. 儲存變更並關閉該檔案。
4. 在 SQL Server 執行個體上，開啟 [工作管理員]****。 重新啟動 **AzureWLBackupCoordinatorSvc** 服務。<br/> <br/>
 雖然此方法可協助備份應用程式耗用大量資源，但 SQL Server [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) 是指定傳入應用程式要求可使用的 CPU、實體 IO 和記憶體數量限制的一般方式。

> [!NOTE]
> 在 UX 中，您仍然可以在任何指定時間排程任意數量的備份。 不過，系統會根據上述範例，在5個滑動視窗中處理它們。

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>是否可以從次要複本執行完整備份？

根據 SQL 限制，您可以在次要複本上執行只複製完整備份。 但是，不允許完整備份。

## <a name="can-i-protect-availability-groups-on-premises"></a>我可以保護內部部署的可用性群組嗎？

否。 Azure 備份可保護在 Azure 中執行的 SQL Server 資料庫。 如果可用性群組 (AG) 在 Azure 和內部部署機器之間散佈，則只有在主要複本是在 Azure 中執行時，才能保護 AG。 此外，Azure 備份只會保護在與復原服務保存庫相同的 Azure 區域中執行的節點。

## <a name="can-i-protect-availability-groups-across-regions"></a>我可以跨區域保護可用性群組嗎？

Azure 備份復原服務保存庫可以偵測和保護與保存庫位於相同區域中的所有節點。 如果您的 SQL Server Always On 可用性群組跨多個 Azure 區域，請從具有主要節點的區域設定備份。 「Azure 備份」可以依據您的備份喜好設定，偵測並保護可用性群組中的所有資料庫。 當您的備份喜好設定不符合時，備份將會失敗，而且您會收到失敗警示。

## <a name="do-successful-backup-jobs-create-alerts"></a>成功的備份作業是否會建立警示？

否。 成功的備份作業不會產生警示。 只有失敗的備份作業會傳送警示。 [這裡](backup-azure-monitoring-built-in-monitor.md)詳載了入口網站警示的行為。 但是，如果您想要讓警示即使成功，也可以使用 Azure 監視器來進行 [監視](backup-azure-monitoring-use-azuremonitor.md)。

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>是否可以在 [備份作業] 功能表中看到已排程的備份作業？

[ **備份作業** ] 功能表只會顯示隨選備份作業。 若為排程工作，請使用 [Azure 監視器進行監視](backup-azure-monitoring-use-azuremonitor.md)。

## <a name="are-future-databases-automatically-added-for-backup"></a>未來的資料庫會自動加入以進行備份嗎？

是的，您可以使用 [自動保護](backup-sql-server-database-azure-vms.md#enable-auto-protection)來達成這項功能。  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>如果我從 autoprotected 實例中刪除資料庫，備份會發生什麼事？

如果從 autoprotected 實例卸載資料庫，仍會嘗試資料庫備份。 這表示已刪除的資料庫會開始在 [備份項目] 下方顯示為狀況不良，且仍受到保護。

停止保護此資料庫的正確方式，是在此資料庫上使用 [**刪除資料**]**停止備份**。  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>如果我停止 autoprotected 資料庫的備份作業，會有什麼行為？

如果您 **停止備份並保留資料**，則不會進行未來的備份，而且現有的復原點會保持不變。 資料庫仍然會被視為受保護，而且會顯示在 **備份專案**底下。

如果您 **停止備份並刪除資料**，將不會進行未來的備份，而且也會刪除現有的復原點。 資料庫將被視為未受保護，並顯示在 [設定備份] 的實例底下。 不過，與其他可以手動選取或可取得 autoprotected 的受保護資料庫不同，此資料庫會呈現灰色且無法選取。 重新保護此資料庫的唯一方法是在實例上停用自動保護。 您現在可以選取此資料庫並設定其保護，或重新啟用實例的自動保護。

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>如果我在資料庫受到保護之後變更資料庫的名稱，該怎麼辦？

重新命名的資料庫會被視為新的資料庫。 如此一來，服務就會將這種情況視為無法找到資料庫，且備份失敗。

您可以選取資料庫，此資料庫現在已重新命名並設定保護。 如果已在實例上啟用自動保護，將會自動偵測並保護重新命名的資料庫。

## <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>為什麼我無法看到 autoprotected 實例的新增資料庫？

您 [加入至 autoprotected 實例](backup-sql-server-database-azure-vms.md#enable-auto-protection) 的資料庫可能不會立即出現在 [受保護的專案] 下。 這是因為探索通常每隔 8 小時才會執行一次。 但是，如果您選取 [重新探索 **db**] 以手動執行探索，您可以立即探索和保護新的資料庫，如下圖所示：

  ![手動探索新加入的資料庫](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="next-steps"></a>後續步驟

瞭解如何備份在 Azure VM 上 [執行的 SQL Server 資料庫](backup-azure-sql-database.md) 。
