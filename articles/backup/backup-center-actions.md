---
title: 使用備份中心執行動作
description: 本文說明如何使用備份中心執行動作
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 06229eb0b8cb76482035d3dfe1949c3ab8354f17
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98894570"
---
# <a name="perform-actions-using-backup-center-preview"></a>使用備份中心 (預覽版執行動作) 

備份中心可讓您從中央介面執行金鑰備份相關的動作，而不需要流覽至個別保存庫。 您可以從備份中心執行的某些動作如下：

* 設定資料來源的備份
* 還原備份實例
* 建立新保存庫
* 建立新的備份原則
* 觸發備份實例的隨選備份
* 停止備份實例的備份

## <a name="supported-scenarios"></a>支援的案例

* 目前支援備份中心的 Azure VM 備份、Azure VM 備份中的 SQL、Azure VM 備份中的 SAP Hana、Azure 檔案儲存體備份和適用於 PostgreSQL 的 Azure 資料庫 Server 備份。
* 如需支援和不支援案例的詳細清單，請參閱 [支援矩陣](backup-center-support-matrix.md) 。

## <a name="configure-backup"></a>設定備份

如果您要備份 azure Vm、Azure Vm 中的 SQL、Azure Vm 中的 SAP Hana 或 Azure 檔案儲存體，您應該使用復原服務保存庫。 如果您要備份適用于于 postgresql 伺服器的 Azure 資料庫，您應該使用備份保存庫。 

視您想要備份的資料來源類型而定，遵循如下所述的適當指示。

### <a name="configure-backup-to-a-recovery-services-vault"></a>設定備份到復原服務保存庫

1. 流覽至 [備份中心]，然後選取 [**總覽**] 索引標籤頂端的 [ **+ 備份**]。

    ![備份中心總覽](./media/backup-center-actions/backup-center-overview-configure-backup.png)

2. 選取您要備份的資料來源類型。

    ![選取 datasource 以設定 VM 備份](./media/backup-center-actions/backup-select-datasource-vm.png)

3. 選擇復原服務保存庫，然後選取 [ **繼續**]。 這會引導您到與復原服務保存庫可連線的備份設定體驗。 [深入瞭解如何使用復原服務保存庫來設定 Azure 虛擬機器的備份](tutorial-backup-vm-at-scale.md)。

### <a name="configure-backup-to-a-backup-vault"></a>設定備份至備份保存庫

1. 流覽至 [備份中心]，然後選取 [**總覽**] 索引標籤頂端的 [ **+ 備份**]。
2. 在此情況下，請選取您要 (適用於 PostgreSQL 的 Azure 資料庫 server 備份的資料來源類型) 。

    ![選取 datasource 以設定適用於 PostgreSQL 的 Azure 資料庫 Server 備份](./media/backup-center-actions/backup-select-datasource-type-postgresql.png)

3. 選取 [繼續]。 這會讓您回到備份設定的體驗，使其與備份保存庫可存取的設定相同。 [深入瞭解如何使用備份保存庫來設定適用於 PostgreSQL 的 Azure 資料庫 Server 的備份](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases)。

## <a name="restore-a-backup-instance"></a>還原備份實例

視您想要還原的資料來源類型而定，遵循如下所述的適當指示。

### <a name="if-youre-restoring-from-a-recovery-services-vault"></a>如果您是從復原服務保存庫還原

1. 流覽至 [備份中心]，然後選取 [**總覽**] 索引標籤頂端的 [**還原**]。

    ![還原 VM 的備份中心總覽](./media/backup-center-actions/backup-center-overview-restore.png)

2. 選取您要還原的資料來源類型。

    ![選取 VM 還原的資料來源](./media/backup-center-actions/restore-select-datasource-vm.png)

3. 選擇備份實例，然後選取 [ **繼續**]。 這會引導您到與復原服務保存庫可連線的還原設定體驗。 [深入瞭解如何使用復原服務保存庫來還原 Azure 虛擬機器](backup-azure-arm-restore-vms.md#before-you-start)。

### <a name="if-youre-restoring-from-a-backup-vault"></a>如果您是從備份保存庫還原

1. 流覽至 [備份中心]，然後選取 [**總覽**] 索引標籤頂端的 [**還原**]。
2. 在此情況下，請選取您想要還原的資料來源類型 (適用於 PostgreSQL 的 Azure 資料庫 Server) 。

    ![選取適用於 PostgreSQL 的 Azure 資料庫 Server 還原的 datasource](./media/backup-center-actions/restore-select-datasource-postgresql.png)

3. 選擇備份實例，然後選取 [ **繼續**]。 這會引導您到與復原服務保存庫可連線的還原設定體驗。 [深入瞭解如何使用備份保存庫還原適用於 PostgreSQL 的 Azure 資料庫的伺服器](backup-azure-database-postgresql.md#restore)。

## <a name="create-a-new-vault"></a>建立新保存庫

您可以流覽至 [備份中心]，然後選取 [**總覽**] 索引標籤頂端的 [ **+ 保存庫**]，以建立新的保存庫。

![建立保存庫](./media/backup-center-actions/backup-center-create-vault.png)

* [深入瞭解如何建立復原服務保存庫](backup-create-rs-vault.md)
* [深入瞭解如何建立備份保存庫](backup-vault-overview.md)

## <a name="create-a-new-backup-policy"></a>建立新的備份原則

視您想要備份的資料來源類型而定，遵循以下所述的適當指示。

### <a name="if-youre-backing-up-to-a-recovery-services-vault"></a>如果您要備份到復原服務保存庫

1. 流覽至 [備份中心]，然後選取 [**總覽**] 索引標籤頂端的 [ **+ 原則**]。

    ![備份原則的備份中心總覽](./media/backup-center-actions/backup-center-overview-policy.png)

2. 選取您要備份的資料來源類型。

    ![為 VM 備份選取原則的 datasource](./media/backup-center-actions/policy-select-datasource-vm.png)

3. 選擇復原服務保存庫，然後選取 [ **繼續**]。 這會引導您使用與復原服務保存庫可連線的原則建立體驗。 [深入瞭解如何為具有復原服務保存庫的 Azure 虛擬機器建立新的備份原則](backup-azure-arm-vms-prepare.md#create-a-custom-policy)。

### <a name="if-youre-backing-up-to-a-backup-vault"></a>如果您要備份至備份保存庫

1. 流覽至 [備份中心]，然後選取 [**總覽**] 索引標籤頂端的 [ **+ 原則**]。
2. 在此情況下，請選取您要 (適用於 PostgreSQL 的 Azure 資料庫 Server 備份的資料來源類型) 。

    ![選取適用於 PostgreSQL 的 Azure 資料庫 Server 備份原則的 datasource](./media/backup-center-actions/policy-select-datasource-postgresql.png)

3. 選取 [繼續]。 這會讓您前往原則建立體驗，使其與備份保存庫中可存取的原則相同。 [深入瞭解如何使用備份保存庫建立新的備份原則](backup-azure-database-postgresql.md#create-backup-policy)。

## <a name="execute-an-on-demand-backup-for-a-backup-instance"></a>針對備份實例執行隨選備份

備份中心可讓您在備份資產上搜尋備份實例，並視需要執行備份作業。

若要觸發隨選備份，請流覽至 [備份中心]，然後選取 [ **備份實例** ] 功能表項目。 選取此項，可讓您查看您有權存取之所有備份實例的詳細資料。 您可以搜尋您想要備份的備份實例。 以滑鼠右鍵按一下方格中的專案，就會開啟可用動作的清單。 選取 [ **立即備份** ] 選項，以執行隨選備份。

![隨選備份](./media/backup-center-actions/backup-center-on-demand-backup.png)

[深入瞭解如何針對 Azure 虛擬機器執行隨選備份](backup-azure-manage-vms.md#run-an-on-demand-backup)。

[深入瞭解如何執行適用於 PostgreSQL 的 Azure 資料庫 Server 的隨選備份](backup-azure-database-postgresql.md#on-demand-backup)。

## <a name="stop-backup-for-a-backup-instance"></a>停止備份實例的備份

在某些情況下，您可能會想要停止備份實例的備份，例如，當備份的基礎資源不再存在時。

若要觸發隨選備份，請流覽至 [備份中心]，然後選取 [ **備份實例** ] 功能表項目。 選取此項，可讓您查看您有權存取之所有備份實例的詳細資料。 您可以搜尋您想要備份的備份實例。 以滑鼠右鍵按一下方格中的專案，就會開啟可用動作的清單。 選取 [ **停止備份** ] 選項以停止備份實例的備份。

![停止保護](./media/backup-center-actions/backup-center-stop-protection.png)

[深入瞭解如何停止 Azure 虛擬機器的備份](backup-azure-manage-vms.md#stop-protecting-a-vm)。

[深入瞭解如何停止適用於 PostgreSQL 的 Azure 資料庫 Server 的備份](backup-azure-database-postgresql.md#stop-protection)

## <a name="next-steps"></a>後續步驟

* [監視和操作備份](backup-center-monitor-operate.md)
* [管理您的備份資產](backup-center-govern-environment.md)
* [取得您的備份見解](backup-center-obtain-insights.md)
