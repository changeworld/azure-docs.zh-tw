---
title: 從 VM 窗格備份 SQL Server VM
description: 在本文中，您將瞭解如何從 VM 窗格備份 Azure 虛擬機器上的 SQL Server 資料庫。
ms.topic: conceptual
ms.date: 08/13/2020
ms.openlocfilehash: dd30ca1fb138d3e3bd44633b9d5e71beae6d96be
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227050"
---
# <a name="back-up-a-sql-server-from-the-vm-pane"></a>從 VM 窗格備份 SQL Server

本文說明如何使用 [Azure 備份](backup-overview.md) 服務來備份在 Azure vm 中執行的 SQL Server。 您可以使用兩種方法來備份 SQL Server Vm：

- 單一 SQL Server Azure VM：本文中的指示說明如何直接從 VM view 備份 SQL Server VM。
- 多個 SQL Server Azure Vm：您可以設定復原服務保存庫，並為多個 Vm 設定備份。 請依照 [本文中的指示進行該案例](backup-sql-server-database-azure-vms.md) 。

## <a name="before-you-start"></a>在您開始使用 Intune 之前

1. 使用 [支援矩陣](sql-support-matrix.md)來驗證您的環境。
2. 深入瞭解 SQL Server [VM 的 Azure 備份](backup-azure-sql-database.md) 。
3. 確認 VM 具有[網路連線](backup-sql-server-database-azure-vms.md#establish-network-connectivity)。

## <a name="configure-backup-on-the-sql-server"></a>在 SQL Server 上設定備份

您可以從 VM 的 [ **備份** ] 窗格啟用 SQL Server vm 上的備份。 這個方法會執行兩件事：

- 向 Azure 備份服務註冊 SQL VM，以授與存取權。
- Autoprotects 在 VM 內執行的所有 SQL Server 實例。 這表示備份原則會套用到所有現有的資料庫，以及未來將加入至這些實例的資料庫。

1. 選取頁面頂端的橫幅，以開啟 [SQL Server 備份] 視圖。

    ![SQL Server 備份視圖](./media/backup-sql-server-vm-from-vm-pane/sql-server-backup-view.png)

    >[!NOTE]
    >看不到橫幅嗎？ 只有使用 Azure Marketplace 映射建立的 SQL Server Vm 才會顯示橫幅。 系統會針對使用 Azure VM 備份保護的 Vm，另外顯示此檔案。 若是其他映射，您可以設定備份，如 [這裡](backup-sql-server-database-azure-vms.md)所述。

2. 輸入復原服務保存庫名稱。 保存庫是用來儲存和管理所有備份的邏輯實體。 如果您建立新的保存庫：

    - 它會建立在與您要保護的 SQL Server VM 相同的訂用帳戶和區域中。
    - 系統會使用異地多餘儲存體來建立它， (GRS 的所有備份) 設定。 如果您想要變更冗余類型，您應該在保護 VM 之前先執行此動作。 如需詳細資訊，請參閱[這篇文章](backup-create-rs-vault.md#set-storage-redundancy)。

3. 選擇 **備份原則**。 您可以選擇預設原則，或您在保存庫中建立的任何其他現有原則。 如果您想要建立新的原則，您可以參閱 [這篇文章](backup-sql-server-database-azure-vms.md#create-a-backup-policy) ，以取得逐步指南。

    ![選擇備份原則](./media/backup-sql-server-vm-from-vm-pane/backup-policy.png)

4. 選取 [啟用備份]。 作業可能需要幾分鐘的時間才能完成。

    ![選取 [啟用備份]](./media/backup-sql-server-vm-from-vm-pane/enable-backup.png)

5. 作業完成後，您會在橫幅中看到保存 **庫名稱** 。

    ![保存庫名稱會出現在橫幅中](./media/backup-sql-server-vm-from-vm-pane/vault-name.png)

6. 選取橫幅以前往保存庫視圖，您可以在其中查看所有已註冊的 Vm 及其保護狀態。

    ![具有已註冊 Vm 的保存庫視圖](./media/backup-sql-server-vm-from-vm-pane/vault-view.png)

7. 若為非 marketplace 映射，註冊可能會成功，但在 Azure 備份延伸模組授與 SQL Server 的許可權之前，可能不會觸發 **設定備份** 。 在這種情況下，「 **備份就緒** 」資料行會讀取 [ **未就緒**]。 您必須手動為非 marketplace 映射 [指派適當的許可權](backup-azure-sql-database.md#set-vm-permissions) ，如此才能觸發「設定備份」。

    ![備份準備就緒尚未就緒](./media/backup-sql-server-vm-from-vm-pane/backup-readiness-not-ready.png)

8. 如需您需要在備份 SQL Server VM 上執行的進一步作業或監視，請移至對應的復原服務保存庫。 移至 [ **備份專案** ] 以查看在此保存庫中備份的所有資料庫，並觸發如隨選備份和還原等作業。 同樣地，移至 [ **備份作業** ] 來 [監視](manage-monitor-sql-database-backup.md) 對應于作業的工作，例如設定保護、備份和還原。

    ![查看備份專案中已備份的資料庫](./media/backup-sql-server-vm-from-vm-pane/backup-items.png)

>[!NOTE]
>不會自動在任何新的 SQL Server 實例上設定備份，稍後可能會新增至受保護的 VM。 若要在新增的實例上設定備份，您必須前往已註冊 VM 的保存庫，並遵循 [此處](backup-sql-server-database-azure-vms.md)所列的步驟。

## <a name="next-steps"></a>後續步驟

了解如何：

- [還原 SQL Server 資料庫備份](restore-sql-database-azure-vm.md)
- [管理 SQL Server 資料庫備份](manage-monitor-sql-database-backup.md)
