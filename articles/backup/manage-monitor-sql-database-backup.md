---
title: 在 Azure VM 上管理和監視 SQL 伺服器 DB
description: 本文介紹如何管理和監視在 Azure VM 上運行的 SQL Server 資料庫。
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 4daf068e97a08d1a611ef64cb64569cacd5d7420
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172149"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>管理和監視備份的 SQL Server 資料庫

本文介紹用於管理和監視在 Azure 虛擬機器 （VM） 上運行並由[Azure 備份](backup-overview.md)服務備份到 Azure 備份恢復服務保存庫的 SQL Server 資料庫的常見任務。 您將學習如何監視作業和警報、停止和恢復資料庫保護、運行備份作業以及從備份中取消註冊 VM。

如果尚未為 SQL Server 資料庫配置備份，請參閱在[Azure VM 上備份 SQL Server 資料庫](backup-azure-sql-database.md)

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>監視門戶中的手動備份作業

Azure 備份在 **"備份作業"** 門戶中顯示所有手動觸發的作業。 在此門戶中看到的作業包括資料庫發現和註冊以及備份和還原操作。

![備份作業門戶](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> **備份作業**門戶不顯示計畫的備份作業。 請使用 SQL Server Management Studio 來監視已排程的備份作業，如下一節中所述。
>

有關監視方案的詳細資訊，請轉到[Azure 門戶中的監視](backup-azure-monitoring-built-in-monitor.md)和使用[Azure 監視器的監視](backup-azure-monitoring-use-azuremonitor.md)。  

## <a name="view-backup-alerts"></a>檢視備份警示

記錄備份會每隔 15 分鐘出現一次，因此監視備份作業可能會很繁瑣。 Azure 備份通過發送電子郵件警報來簡化監視。 電子郵件提醒包括：

- 為所有備份失敗觸發。
- 按錯誤代碼在資料庫級別合併。
- 僅為資料庫的第一個備份失敗發送。

要監視資料庫備份警報：

1. 登錄到 Azure[門戶](https://portal.azure.com)。

2. 在保存庫儀表板上，選取 [警示與事件]****。

   ![選取 [警示和事件]](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. 在 [警示和事件]**** 中，選取 [備份警示]****。

   ![選取 [備份警示]](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>停止保護 SQL Server 資料庫

您可以透過數種方式停止備份 SQL Server 資料庫：

- 停止所有未來的備份作業並刪除所有復原點。
- 停止所有將來的備份作業，並保留復原點不變。

如果您選擇保留復原點，請記住下列詳細資料：

- 所有復原點會永遠保持不變，而停止保護但保留資料時，所有清除作業都應該停止。
- 受保護實例和消耗的存儲將向您收費。 有關詳細資訊，請參閱[Azure 備份定價](https://azure.microsoft.com/pricing/details/backup/)。
- 如果您刪除資料來源，但沒有停止備份，新的備份將會失敗。

若要停止保護資料庫：

1. 在保存庫儀表板上，選擇 **"備份專案**"。

2. 在**備份管理類型**下，**在 Azure VM 中選擇 SQL。**

    ![選取 Azure VM 中的 SQL](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. 選取您要停止保護的資料庫。

    ![選取要停止保護的資料庫](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. 在資料庫功能表上，選取 [停止備份]****。

    ![選取 [停止備份]](./media/backup-azure-sql-database/stop-db-button.png)

5. 在 **"停止備份"** 功能表上，選擇是保留還是刪除資料。 如果需要，請提供理由和注釋。

    ![保留或刪除"停止備份"功能表上的資料](./media/backup-azure-sql-database/stop-backup-button.png)

6. 選擇 **"停止備份**"。

> [!NOTE]
>
>有關刪除資料選項的詳細資訊，請參閱下面的常見問題解答：
>
>- [如果我從自動保護實例中刪除資料庫，備份將會發生什麼情況？](faq-backup-sql-server.md#if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups)
>- [如果我停止自動保護資料庫的備份操作，其行為將是什麼？](faq-backup-sql-server.md#if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior)
>
>

## <a name="resume-protection-for-a-sql-database"></a>繼續保護 SQL 資料庫

停止 SQL 資料庫保護時，如果選擇"**保留備份資料"** 選項，則可以稍後恢復保護。 如果不保留備份資料，則無法恢復保護。

要恢復 SQL 資料庫的保護，

1. 打開備份項並選擇 **"恢復備份**"。

    ![選取 [繼續備份] 以繼續保護資料庫](./media/backup-azure-sql-database/resume-backup-button.png)

2. 在 [備份原則]**** 功能表上選取原則，然後選取 [儲存]****。

## <a name="run-an-on-demand-backup"></a>執行隨選備份

您可以執行不同類型的隨選備份：

- 完整備份
- 僅複製完整備份
- 差異備份
- 記錄備份

雖然您需要指定僅複製完整備份的保留期限，但按需完整備份的保留範圍將自動設置為目前時間的 45 天。

有關詳細資訊，請參閱[SQL Server 備份類型](backup-architecture.md#sql-server-backup-types)。

## <a name="unregister-a-sql-server-instance"></a>將 SQL Server 執行個體取消註冊

在禁用保護後但在刪除保存庫之前取消註冊 SQL Server 實例：

1. 在保存庫儀表板上，在 **"管理**"下，選擇 **"備份基礎結構**"。  

   ![選取 [備份基礎結構]](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. 在 [管理伺服器]**** 底下，選取 [受保護的伺服器]****。

   ![選取 [受保護的伺服器]](./media/backup-azure-sql-database/protected-servers.png)

3. 在 [受保護的伺服器]**** 中，選取要取消註冊的伺服器。 若要刪除保存庫，您必須將所有伺服器取消註冊。

4. 按右鍵受保護的伺服器，然後選擇 **"取消註冊**"。

   ![選取 [刪除]](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="modify-policy"></a>修改原則

修改策略以更改備份頻率或保留範圍。

> [!NOTE]
> 保留期中的任何更改都將追溯應用於除新復原點之外的所有舊復原點。

在保存庫儀表板中，轉到**管理** > **備份策略**並選擇要編輯的策略。

  ![管理備份策略](./media/backup-azure-sql-database/modify-backup-policy.png)

  ![修改備份策略](./media/backup-azure-sql-database/modify-backup-policy-impact.png)

策略修改將影響所有關聯的備份專案，並觸發相應的**配置保護**作業。

### <a name="inconsistent-policy"></a>不一致的策略

有時，修改策略操作可能會導致某些備份項的策略版本**不一致**。 當觸發修改策略操作後備份項的相應**配置保護**作業失敗時，將發生這種情況。 它在備份項視圖中如下所示：

  ![不一致的策略](./media/backup-azure-sql-database/inconsistent-policy.png)

只需按一下一下，即可修復所有受影響專案的策略版本：

  ![修復不一致的策略](./media/backup-azure-sql-database/fix-inconsistent-policy.png)

## <a name="re-register-extension-on-the-sql-server-vm"></a>在 SQL Server VM 上重新註冊擴展

有時，VM 上的工作負載擴展可能會由於以下或一個原因而受到影響。 在這種情況下，在 VM 上觸發的所有操作將開始失敗。 然後，您可能需要在 VM 上重新註冊擴展。 **重新註冊**操作在 VM 上重新安裝工作負載備份擴展，以便繼續操作。

謹慎使用此選項;在具有已正常運行的擴展的 VM 上觸發時，此操作將導致擴展重新開機。 這可能導致所有正在進行的作業失敗。 請在觸發重新註冊操作之前檢查一個或多個[症狀](backup-sql-server-azure-troubleshoot.md#re-registration-failures)。

## <a name="next-steps"></a>後續步驟

有關詳細資訊，請參閱[SQL Server 資料庫上的疑難備份](backup-sql-server-azure-troubleshoot.md)。
