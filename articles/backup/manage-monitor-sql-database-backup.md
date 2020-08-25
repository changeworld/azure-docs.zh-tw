---
title: 管理及監視 Azure VM 上的 SQL Server Db
description: 本文說明如何管理和監視在 Azure VM 上執行的 SQL Server 資料庫。
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 26a1a6cf7bc011edce61a8bb60926dad2cb29a16
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88826628"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>管理和監視備份的 SQL Server 資料庫

本文說明管理和監視在 Azure 虛擬機器上執行的 SQL Server 資料庫 (VM) 的一般工作，以及由 [Azure 備份](backup-overview.md) 服務備份到 Azure 備份復原服務保存庫的作業。 您將瞭解如何監視作業和警示、停止和繼續資料庫保護、執行備份作業，以及從備份取消註冊 VM。

如果您尚未設定 SQL Server 資料庫的備份，請參閱 [備份 Azure vm 上的 SQL Server 資料庫](backup-azure-sql-database.md)

## <a name="monitor-backup-jobs-in-the-portal"></a>在入口網站中監視備份作業

Azure 備份會在入口網站中顯示 **備份作業** 下的所有已排程和隨選作業（排程的記錄備份除外），因為它們可能非常頻繁。 您在此入口網站中看到的工作包括資料庫探索和註冊、設定備份，以及備份和還原作業。

![備份作業入口網站](./media/backup-azure-sql-database/jobs-list.png)

如需監視案例的詳細資訊，請移至 [Azure 入口網站中的 [監視](backup-azure-monitoring-built-in-monitor.md) ]，然後 [使用 Azure 監視器進行監視](backup-azure-monitoring-use-azuremonitor.md)。  

## <a name="view-backup-alerts"></a>檢視備份警示

記錄備份會每隔 15 分鐘出現一次，因此監視備份作業可能會很繁瑣。 Azure 備份傳送電子郵件警示，以簡化監視。 電子郵件警示如下：

- 所有備份失敗都會觸發的警示。
- 在資料庫層級依錯誤碼合併的警示。
- 在資料庫第一次備份失敗時才會傳送的警示。

若要監視資料庫備份警示：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 在保存庫儀表板上，選取 [警示與事件]****。

   ![選取 [警示和事件]](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. 在 [警示和事件]**** 中，選取 [備份警示]****。

   ![選取 [備份警示]](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>停止保護 SQL Server 資料庫

您可以透過數種方式停止備份 SQL Server 資料庫：

- 停止所有未來的備份作業並刪除所有復原點。
- 停止所有未來的備份作業，並讓復原點保持不變。

如果您選擇保留復原點，請記住下列詳細資料：

- 所有復原點會永遠保持不變，而且所有的剪除都會停止保護，但保留資料。
- 您需支付受保護執行個體和已使用儲存體的費用。 如需詳細資訊，請參閱 [Azure 備份服務定價](https://azure.microsoft.com/pricing/details/backup/)。
- 如果您刪除資料來源，但沒有停止備份，新的備份將會失敗。 舊的復原點會根據原則到期，但最後一個復原點會一直保留，直到您停止備份並刪除資料為止。

若要停止保護資料庫：

1. 在保存庫儀表板上，選取 [備份項目]。

2. 在 [ **備份管理類型**] 下，選取 **[Azure VM 中的 SQL**]。

    ![選取 Azure VM 中的 SQL](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. 選取您要停止保護的資料庫。

    ![選取要停止保護的資料庫](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. 在資料庫功能表上，選取 [停止備份]。

    ![選取 [停止備份]](./media/backup-azure-sql-database/stop-db-button.png)

5. 在 [停止備份] 功能表上，選取是否要保留或刪除資料。 若有需要，可提供原因或註解。

    ![保留或刪除 [停止備份] 功能表上的資料](./media/backup-azure-sql-database/stop-backup-button.png)

6. 選取 [停止備份]。

> [!NOTE]
>
>如需 [刪除資料] 選項的詳細資訊，請參閱下列常見問題：
>
>- [如果我從 autoprotected 實例中刪除資料庫，備份會發生什麼事？](faq-backup-sql-server.md#if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups)
>- [如果我停止 autoprotected 資料庫的備份作業，會有什麼行為？](faq-backup-sql-server.md#if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior)
>
>

## <a name="resume-protection-for-a-sql-database"></a>繼續保護 SQL 資料庫

當您停止保護 SQL 資料庫時，如果您選取 [ **保留備份資料** ] 選項，稍後就可以繼續保護。 如果您未保留備份資料，則無法繼續保護。

若要繼續保護 SQL 資料庫：

1. 開啟備份項目，然後選取 [繼續備份]。

    ![選取 [繼續備份] 以繼續保護資料庫](./media/backup-azure-sql-database/resume-backup-button.png)

2. 在 [備份原則] 功能表上選取原則，然後選取 [儲存]。

## <a name="run-an-on-demand-backup"></a>執行隨選備份

您可以執行不同類型的隨選備份：

- 完整備份
- 僅複製完整備份
- 差異備份
- 記錄備份

當您需要指定僅複製完整備份的保留持續時間時，隨選完整備份的保留範圍將會從目前的時間自動設定為45天。

如需詳細資訊，請參閱 [SQL Server 備份類型](backup-architecture.md#sql-server-backup-types)。

## <a name="modify-policy"></a>修改原則

修改原則以變更備份頻率或保留範圍。

> [!NOTE]
> 除了新的復原點以外，保留期限內的任何變更也會回溯套用。

在保存庫儀表板中，移至 [**管理**  >  **備份原則**]，然後選擇您要編輯的原則。

  ![管理備份原則](./media/backup-azure-sql-database/modify-backup-policy.png)

  ![修改備份原則](./media/backup-azure-sql-database/modify-backup-policy-impact.png)

修改原則會影響所有相關聯的備份項目，並觸發對應的**設定保護**作業。

### <a name="inconsistent-policy"></a>不一致的原則

有時候，修改原則作業可能會導致某些備份專案的原則版本 **不一致** 。 在觸發修改原則作業之後，針對備份項目的對應**設定保護**作業失敗時，就會發生這種情況。 這會在備份項目檢視中顯示如下：

  ![不一致的原則](./media/backup-azure-sql-database/inconsistent-policy.png)

您只要按一下，就可以修正所有受影響項目的原則版本：

  ![修正不一致的原則](./media/backup-azure-sql-database/fix-inconsistent-policy.png)

## <a name="unregister-a-sql-server-instance"></a>將 SQL Server 執行個體取消註冊

在您停用保護之後，但在刪除保存庫之前，取消註冊 SQL Server 實例：

1. 在保存庫儀表板上，選取 [管理] 下的 [備份基礎結構]。  

   ![選取 [備份基礎結構]](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. 在 [管理伺服器]**** 底下，選取 [受保護的伺服器]****。

   ![選取 [受保護的伺服器]](./media/backup-azure-sql-database/protected-servers.png)

3. 在 [受保護的伺服器]**** 中，選取要取消註冊的伺服器。 若要刪除保存庫，您必須將所有伺服器取消註冊。

4. 在受保護的伺服器上按一下滑鼠右鍵，然後選取 [ **取消註冊**]。

   ![選取 [刪除]](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="re-register-extension-on-the-sql-server-vm"></a>在 SQL Server VM 上重新註冊擴充功能

有時候，VM 上的工作負載延伸可能會因為一個原因或另一個原因而受到影響。 在這種情況下，VM 上觸發的所有作業將會開始失敗。 接著，您可能需要在 VM 上重新註冊此延伸模組。 **重新註冊**作業會重新安裝 VM 上的工作負載備份擴充功能，以繼續執行作業。 您可以在復原服務保存庫中的 [ **備份基礎結構** ] 下找到此選項。

![備份基礎結構下受保護的伺服器](./media/backup-azure-sql-database/protected-servers-backup-infrastructure.png)

請謹慎使用此選項。 當在具有狀況良好擴充的 VM 上觸發時，此作業將會導致擴充功能重新開機。 這可能會導致所有進行中的工作失敗。 在觸發重新註冊作業之前，請先檢查一個或多個[徵兆](backup-sql-server-azure-troubleshoot.md#re-registration-failures)：

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱 [疑難排解 SQL Server 資料庫上的備份](backup-sql-server-azure-troubleshoot.md)。
