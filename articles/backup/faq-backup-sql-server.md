---
title: 常見問題解答 - 在 Azure VM 上備份 SQL 伺服器資料庫
description: 查找有關使用 Azure 備份在 Azure VM 上備份 SQL Server 資料庫的常見問題的解答。
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: a973761bf16e2d271d718e4a8b29e08624276987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247705"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>有關在 Azure VM 備份上運行的 SQL Server 資料庫的常見問題解答

本文回答了有關備份在 Azure 虛擬機器 （VM） 上運行並使用[Azure 備份](backup-overview.md)服務的 SQL Server 資料庫的常見問題。

## <a name="can-i-use-azure-backup-for-iaas-vm-as-well-as-sql-server-on-the-same-machine"></a>是否可以在同一台電腦上使用 IaaS VM 的 Azure 備份以及 SQL Server？

可以，可以在同一 VM 上同時具有 VM 備份和 SQL 備份。 在這種情況下，我們在 VM 上觸發僅複製的完整備份，以不截接日誌。

## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>解決方案是重試還是自動修復備份？

在某些情況下，Azure 備份服務將觸發補救備份。 以下六個條件中的任何一個都可能發生自動修復：

- 如果日誌或差異備份由於 LSN 驗證錯誤而失敗，則下一個日誌或差異備份將轉換為完整備份。
- 如果在日誌或差異備份之前未發生完全備份，則該日誌或差異備份將轉換為完整備份。
- 如果最新的完整備份的時間點超過 15 天，則下一個日誌或差異備份將轉換為完整備份。
- 升級完成後，由於擴展升級而取消的所有備份作業將重新觸發。
- 如果選擇在還原期間覆蓋資料庫，則下一個日誌/差異備份將失敗，並觸發完整備份。
- 如果由於資料庫恢復模型的變化而需要完全備份來重置日誌鏈，則在下一個計畫上會自動觸發完整備份。

預設情況下，為所有使用者啟用自動修復為功能;但是，如果您退出宣告，請執行以下操作：

- 在 SQL Server 實例中，在*C：\程式檔\Azure 工作負載備份\bin*資料夾中，創建或編輯**擴展設置覆蓋.json**檔。
- 在**擴展設置覆蓋.json**中，設置 *{啟用自動修復器"：false}。*
- 儲存變更並關閉該檔案。
- 在 SQL Server 實例上，打開**任務管理**，然後重新開機**AzureWL 備份協調器Svc**服務。

## <a name="can-i-control-how-many-concurrent-backups-run-on-the-sql-server"></a>是否可以控制 SQL 伺服器上運行的併發備份數？

是。 您可以限制備份原則的執行速率，以儘量降低對 SQL Server 執行個體的影響。 變更設定：

1. 在 SQL Server 實例中，在*C：_程式檔\Azure 工作負載備份\bin*資料夾中，創建*擴展設置覆蓋.json*檔。
2. 在*擴展設置覆蓋.json*檔中，將**預設備份任務閾值**設置更改為較低的值（例如，5）。 <br>
  `{"DefaultBackupTasksThreshold": 5}`
<br>
預設備份任務閾值的預設值為**20**。

3. 儲存變更並關閉該檔案。
4. 在 SQL Server 執行個體上，開啟 [工作管理員]****。 重新啟動 **AzureWLBackupCoordinatorSvc** 服務。<br/> <br/>
 雖然此方法有助於備份應用程式消耗大量資源，但 SQL Server[資源調速器](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor?view=sql-server-2017)是指定傳入應用程式應用程式應用程式可以使用的 CPU、物理 IO 和記憶體量限制的更通用的方法。

> [!NOTE]
> 在 UX 中，您仍然可以在任意給定時間進行盡可能多的備份，但是它們將在例如 5 的滑動視窗中進行處理，如上述示例所示。

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>是否可以從次要複本執行完整備份？

根據 SQL 限制，您可以在輔助副本上運行僅複製完全備份;因此，您可以在輔助副本上運行"僅複製"完全備份。但是，不允許完全備份。

## <a name="can-i-protect-availability-groups-on-premises"></a>我可以保護本地可用性組嗎？

否。 Azure 備份保護在 Azure 中運行的 SQL Server 資料庫。 如果可用性組 （AG） 分佈在 Azure 和本地電腦之間，則僅當主副本在 Azure 中運行時，才能保護 AG。 此外，Azure 備份僅保護與恢復服務保存庫在同一 Azure 區域中運行的節點。

## <a name="can-i-protect-availability-groups-across-regions"></a>我可以跨區域保護可用性群組嗎？

Azure 備份恢復服務保存庫可以檢測和保護與保存庫位於同一區域中的所有節點。 如果 SQL Server 始終打開可用性組跨越多個 Azure 區域，請從具有主節點的地區設定備份。 「Azure 備份」可以依據您的備份喜好設定，偵測並保護可用性群組中的所有資料庫。 未滿足備份首選項時，備份將失敗，您會收到故障警報。

## <a name="do-successful-backup-jobs-create-alerts"></a>成功的備份作業是否會建立警示？

否。 成功的備份作業不會產生警示。 只有失敗的備份作業會傳送警示。 [此處](backup-azure-monitoring-built-in-monitor.md)記錄了門戶警報的詳細行為。 但是，如果您有興趣，即使對於成功的作業，也確實有警報，則可以使用 Azure[監視器 使用監視](backup-azure-monitoring-use-azuremonitor.md)。

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>我可以在"備份作業"功能表中查看計畫的備份作業嗎？

"**備份作業"** 功能表將僅顯示按需備份作業。 對於計畫作業，[請使用 Azure 監視器監視](backup-azure-monitoring-use-azuremonitor.md)。

## <a name="are-future-databases-automatically-added-for-backup"></a>未來的資料庫會自動加入以進行備份嗎？

是的，您可以通過[自動保護](backup-sql-server-database-azure-vms.md#enable-auto-protection)來實現此功能。  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>如果我從自動保護實例中刪除資料庫，備份將會發生什麼情況？

如果資料庫從自動保護實例中刪除，則仍嘗試資料庫備份。 這意味著已刪除的資料庫在 **"備份專案"** 下開始顯示為不正常，並且仍然受到保護。

停止保護此資料庫的正確方法是使用此資料庫上的**刪除資料**執行 **"停止備份**"。  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>如果我停止自動保護資料庫的備份操作，其行為將是什麼？

如果停止**備份保留資料**，則以後不會進行備份，並且現有復原點將保持不變。 資料庫仍將被視為受保護資料庫，並顯示在**備份項**下。

如果停止**使用刪除資料進行備份**，則以後不會進行備份，並且還會刪除現有的復原點。 資料庫將被視為未受保護，並在配置備份中的實例下顯示。 但是，與其他可以手動選擇或可以得到自動保護的上保護資料庫不同，此資料庫顯示為灰色，無法選擇。 重新保護此資料庫的唯一方法是禁用實例上的自動保護。 您現在可以選擇此資料庫並在該資料庫上配置保護，或者再次在實例上重新啟用自動保護。

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>如果我在資料庫受到保護後更改它的名稱，那麼行為將是什麼？

重命名的資料庫被視為新資料庫。 因此，服務將處理這種情況，就像找不到資料庫和備份失敗一樣。

您可以選擇資料庫，該資料庫現已重命名並配置其保護。 如果實例上啟用了自動保護，將自動檢測和保護重命名的資料庫。

## <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>為什麼我看不到自動保護實例添加的資料庫？

[添加到自動保護實例的](backup-sql-server-database-azure-vms.md#enable-auto-protection)資料庫可能不會立即顯示在受保護的項下。 這是因為探索通常每隔 8 小時才會執行一次。 但是，如果通過選擇 **"重新發現 DB"** 手動運行發現，則可以立即發現和保護新資料庫，如下圖所示：

  ![手動發現新添加的資料庫](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="next-steps"></a>後續步驟

瞭解如何[備份在](backup-azure-sql-database.md)Azure VM 上運行的 SQL Server 資料庫。
