---
title: 還原 Azure VM 上的 SQL Server 資料庫
description: 本文說明如何還原在 Azure VM 上執行，並使用 Azure 備份備份的 SQL Server 資料庫。 您也可以使用跨區域還原，將資料庫還原到次要區域。
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: 7dd8d8d54fa7d33bb4a0935357597d19dd2368c5
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734397"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>在 Azure VM 上還原 SQL Server 資料庫

本文說明如何還原在 Azure 虛擬機器上執行的 SQL Server 資料庫 (VM) [Azure 備份](backup-overview.md) 服務已備份至 Azure 備份復原服務保存庫。

本文將說明如何還原 SQL Server 資料庫。 如需詳細資訊，請參閱 [備份 Azure vm 上的 SQL Server 資料庫](backup-azure-sql-database.md)。

## <a name="restore-to-a-time-or-a-recovery-point"></a>還原至某個時間或復原點

Azure 備份可以還原在 Azure Vm 上執行的 SQL Server 資料庫，如下所示：

- 使用交易記錄備份還原至特定日期或時間 (到第二個) 。 Azure 備份會根據選取的時間，自動決定還原所需的適當完整差異備份和記錄備份鏈。
- 還原特定的完整或差異備份，以還原至特定復原點。

## <a name="restore-prerequisites"></a>還原必要條件

在還原資料庫之前，請注意下列事項：

- 您可以將資料庫還原至相同 Azure 區域中的 SQL Server 執行個體。
- 目的地伺服器必須註冊到和來源相同的保存庫。
- 如果您在伺服器上有多個實例正在執行，則所有實例都應該啟動並執行。 否則，伺服器將不會出現在目的地伺服器清單中，以便您將資料庫還原到其中。 如需詳細資訊，請參閱 [疑難排解步驟](backup-sql-server-azure-troubleshoot.md#faulty-instance-in-a-vm-with-multiple-sql-server-instances)。
- 若要將 TDE 加密的資料庫還原到另一個 SQL Server，您必須先將 [憑證還原至目的地伺服器](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server)。
- 啟用[CDC](/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server)的資料庫應該使用 [[還原為](#restore-as-files)檔案] 選項來還原。
- 在還原 "master" 資料庫之前，請使用啟動選項 **-m AzureWorkloadBackup**，在單一使用者模式中啟動 SQL Server 實例。
  - **-M** 的值是用戶端的名稱。
  - 只有指定的用戶端名稱可以開啟連接。
- 針對所有系統資料庫 (model、master、msdb) ，請在觸發還原之前停止 SQL Server Agent 服務。
- 關閉任何可能嘗試連接到這些資料庫的任何應用程式。

## <a name="restore-a-database"></a>還原資料庫

若要還原，您需要下列許可權：

- 您正在進行還原之保存庫中的 **備份操作員** 許可權。
- 來源 VM (已備份) 的 **參與者 (寫入)** 存取權。
- 目標 VM 的 **參與者 (寫入)** 存取權：
  - 如果您要還原至相同的 VM，這是來源 VM。
  - 如果您要還原至替代位置，這是新的目標 VM。

以下列方式進行還原：

1. 開啟其中有已註冊 SQL Server VM 的保存庫。
2. 在保存庫儀表板中的 [使用量] 底下，選取 [備份項目]。
3. 在 [備份項目] 中的 [備份管理類型] 底下，選取 **Azure VM 中的 SQL**。

    ![選取 Azure VM 中的 SQL](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. 選取要還原的資料庫。

    ![選取要還原的資料庫](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. 檢閱資料庫功能表。 其中有資料庫備份的資訊，包括：

    - 最早和最近的還原點。
    - 過去24小時的記錄備份狀態，適用于完整和大量記錄復原模式的資料庫，而且已針對交易記錄備份進行設定。

6. 選取 [還原]。

    ![選取 [還原]](./media/backup-azure-sql-database/restore-db.png)

7. 在 [ **還原** 設定] 中，指定還原資料的 (或) 方式：
   - **替代位置**：將資料庫還原到替代位置，並保留原始源資料庫。
   - **覆寫 DB**：將資料還原至與原始來源相同的 SQL Server 執行個體。 此選項會覆寫原始資料庫。

        > [!IMPORTANT]
        > 如果選取的資料庫屬於 AlwaysOn 可用性群組，SQL Server 不允許覆寫資料庫。 只有 [替代位置] 可用。
        >
   - **還原為** 檔案：您可以稍後在使用 SQL Server Management Studio 的檔案所在的任何電腦上還原可作為資料庫復原的備份檔案，而不是還原為資料庫。
     ![[還原設定] 功能表](./media/backup-azure-sql-database/restore-configuration.png)

### <a name="restore-to-an-alternate-location"></a>還原至替代位置

1. 在 [ **還原** 設定] 功能表的 [ **要還原的位置**] 下，選取 [ **替代位置**]。
1. 選取您要在其中還原資料庫的目的地 SQL Server 名稱和執行個體。
1. 在 [還原的 DB 名稱] 方塊中，輸入目標資料庫的名稱。
1. 如果適用的話，請選取 [Overwrite if the DB with the same name already exists on selected SQL instance] \(若選取的 SQL 執行個體上已存在相同名稱的 DB 則覆寫\)。
1. 選取 [ **還原點**]，然後選取要 [還原至特定的時間點](#restore-to-a-specific-point-in-time) ，或 [還原至特定的復原點](#restore-to-a-specific-restore-point)。

    ![選取還原點](./media/backup-azure-sql-database/select-restore-point.png)

    ![還原至時間點](./media/backup-azure-sql-database/restore-to-point-in-time.png)

1. 在 [進階設定] 功能表上：

    - 如果您想要在還原之後讓資料庫保持 nonoperational，請啟用 [ **使用 NORECOVERY 還原**]。
    - 如果您想要變更目的地伺服器上的還原位置，請輸入新的目標路徑。

        ![輸入目標路徑](./media/backup-azure-sql-database/target-paths.png)

1. 選取 **[確定]** 以觸發還原。 在 [ **通知** ] 區域中追蹤還原進度，或在保存庫的 [ **備份作業** ] 視圖下追蹤。

    > [!NOTE]
    > 還原時間點僅適用于具有完整和大量記錄復原模式的資料庫記錄備份。

### <a name="restore-and-overwrite"></a>還原並覆寫資料庫

1. 在 [**還原** 設定] 功能表的 [**要還原的位置**] 下，選取 [**覆寫 DB**  >  **確定]**。

    ![選取 [覆寫 DB]](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. 在 [ **選取還原點**] 中，選取 [ **記錄] (時間點)** [還原至特定時間點](#restore-to-a-specific-point-in-time)。 或者，選取 [ **Full & 差異** ] 以還原至 [特定復原點](#restore-to-a-specific-restore-point)。

    > [!NOTE]
    > 還原時間點僅適用于具有完整和大量記錄復原模式的資料庫記錄備份。

### <a name="restore-as-files"></a>還原為檔案

若要將備份資料還原為 .bak 檔案而非資料庫，請選擇 [ **還原為** 檔案]。 一旦檔案傾印到指定的路徑，您就可以將這些檔案放在您想要將它們還原為資料庫的任何電腦上。 由於您可以將這些檔案移至任何電腦上，因此您現在可以跨訂用帳戶和區域還原資料。

1. 在 [ **要還原的位置和方式**] 下，選取 [ **還原為** 檔案]。
1. 選取您要用來還原備份檔案的 SQL Server 名稱。
1. 在 **伺服器的目的地路徑** 中，輸入在步驟2中選取之伺服器上的資料夾路徑。 這是服務將用來傾印所有必要備份檔案的位置。 一般而言，網路共用路徑 (或是指定為目的地路徑的掛接 Azure 檔案路徑) 可讓相同網路中或掛接相同 Azure 檔案共用的其他機器，更輕鬆地存取這些檔案。<BR>

    >若要在裝載于目標已註冊 VM 上的 Azure 檔案共用上還原資料庫備份檔案，請確定 NT AUTHORITY\SYSTEM 具有檔案共用的存取權。 您可以執行下列步驟，將讀取/寫入權限授與 VM 上裝載的 AFS：
    >
    >- 執行 `PsExec -s cmd` 以進入 NT AUTHORITY\SYSTEM shell
    >   - 執行 `cmdkey /add:<storageacct>.file.core.windows.net /user:AZURE\<storageacct> /pass:<storagekey>`
    >   - 使用驗證存取 `dir \\<storageacct>.file.core.windows.net\<filesharename>`
    >- 從備份保存庫以檔案形式啟動還原作為 `\\<storageacct>.file.core.windows.net\<filesharename>` 路徑<BR>
    您可以從 [ [Sysinternals](/sysinternals/downloads/psexec) ] 頁面下載 PsExec。

1. 選取 [確定]。

    ![選取還原為檔案](./media/backup-azure-sql-database/restore-as-files.png)

1. 選取 [ **還原點**]，然後選取要 [還原至特定的時間點](#restore-to-a-specific-point-in-time) ，或 [還原至特定的復原點](#restore-to-a-specific-restore-point)。

1. 與所選復原點相關聯的所有備份檔案都會傾印到目的地路徑。 您可以使用 SQL Server Management Studio，將這些檔案還原為其存在的任何電腦上的資料庫。

    ![還原目的地路徑中的備份檔案](./media/backup-azure-sql-database/sql-backup-files.png)

### <a name="restore-to-a-specific-point-in-time"></a>還原至特定時間點

如果您已選取 [記錄 (時間點)] 作為還原類型，請執行下列動作：

1. 在 [ **還原日期/時間**] 下，開啟行事曆。 在行事曆上，具有復原點的日期會以粗體顯示，而目前的日期會反白顯示。
1. 選取具有復原點的日期。 您無法選取沒有復原點的日期。

    ![開啟行事曆](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. 選取日期之後，時間軸圖表會顯示連續範圍中可用的復原點。
1. 在時間軸圖形上指定復原的時間，或選取時間。 然後選取 [確定]。

### <a name="restore-to-a-specific-restore-point"></a>還原至特定還原點

如果您已選取 [完整和差異] 作為還原類型，請執行下列動作：

1. 從清單中選取復原點，然後選取 [確定] 以完成還原點程序。

    ![選擇完整復原點](./media/backup-azure-sql-database/choose-full-recovery-point.png)

    >[!NOTE]
    > 依預設，會顯示過去30天的復原點。 您可以選取 [ **篩選** ] 並選取自訂範圍，以顯示30天之前的復原點。

### <a name="restore-databases-with-large-number-of-files"></a>還原含有大量檔案的資料庫

如果資料庫中檔案的總字串大小超過 [特定限制](backup-sql-server-azure-troubleshoot.md#size-limit-for-files)，Azure 備份將資料庫檔案的清單儲存在不同的 pit 元件中，如此您就無法在還原作業期間設定目標還原路徑。 這些檔案會改為還原至 SQL 預設路徑。

  ![使用大型檔案還原資料庫](./media/backup-azure-sql-database/restore-large-files.jpg)

## <a name="cross-region-restore"></a>跨區域還原

作為其中一個還原選項，跨區域還原 (CRR) 可讓您將 Azure Vm 上裝載的 SQL 資料庫還原到次要區域（即 Azure 配對的區域）中。

若要在預覽期間登入功能，請閱讀 [ [開始之前] 區段](./backup-create-rs-vault.md#set-cross-region-restore)。

若要查看是否已啟用 CRR，請依照[設定跨區域還原](backup-create-rs-vault.md#configure-cross-region-restore)中的指示進行。

### <a name="view-backup-items-in-secondary-region"></a>查看次要區域中的備份專案

如果已啟用 CRR，您可以在次要區域中查看備份專案。

1. 從入口網站移至 [復原 **服務保存庫**  >  **備份專案**]。
1. 選取 **次要區域** 以查看次要區域中的專案。

>[!NOTE]
>清單中只會顯示支援 CRR 功能的備份管理類型。 目前，僅支援將次要區域資料還原至次要區域。

![次要區域中的備份專案](./media/backup-azure-sql-database/backup-items-secondary-region.png)

![次要區域中的資料庫](./media/backup-azure-sql-database/databases-secondary-region.png)

### <a name="restore-in-secondary-region"></a>在次要區域中還原

次要區域還原使用者體驗將類似于主要區域還原使用者體驗。 在 [還原設定] 窗格中設定詳細資料來設定您的還原時，系統會提示您只提供次要區域參數。

![還原的位置和方式](./media/backup-azure-sql-database/restore-secondary-region.png)

>[!NOTE]
>次要區域中的虛擬網路必須是唯一指派的，且不能用於該資源群組中的任何其他 Vm。

![觸發還原進行中的通知](./media/backup-azure-arm-restore-vms/restorenotifications.png)

>[!NOTE]
>
>- 在觸發還原並且在資料傳輸階段中，無法取消還原作業。
>- 在次要區域中還原所需的 Azure 角色與主要區域中的角色相同。

### <a name="monitoring-secondary-region-restore-jobs"></a>監視次要區域還原作業

1. 從入口網站移至復原 **服務保存庫**  >  **備份作業**
1. 選取 **次要區域** 以查看次要區域中的專案。

    ![已篩選的備份作業](./media/backup-azure-sql-database/backup-jobs-secondary-region.png)

## <a name="next-steps"></a>後續步驟

[管理與監視](manage-monitor-sql-database-backup.md) SQL Server 由 Azure 備份備份的資料庫。
