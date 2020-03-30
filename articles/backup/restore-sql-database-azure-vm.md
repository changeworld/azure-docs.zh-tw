---
title: 在 Azure VM 上還原 SQL Server 資料庫
description: 本文介紹如何還原在 Azure VM 上運行且使用 Azure 備份備份的 SQL Server 資料庫。
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: 642476c98ca223da01bda5c6eb79ee9b53732468
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252450"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>在 Azure VM 上還原 SQL Server 資料庫

本文介紹如何還原[Azure 備份](backup-overview.md)服務已備份到 Azure 備份恢復服務保存庫的 Azure 虛擬機器 （VM） 上運行的 SQL Server 資料庫。

本文將說明如何還原 SQL Server 資料庫。 有關詳細資訊，請參閱在[Azure VM 上備份 SQL Server 資料庫](backup-azure-sql-database.md)。

## <a name="restore-to-a-time-or-a-recovery-point"></a>還原到時間或復原點

Azure 備份可以還原在 Azure VM 上運行的 SQL Server 資料庫，如下所示：

- 通過使用事務記錄備份還原到特定日期或時間（第二個）。 Azure 備份會自動確定根據所選時間還原所需的適當完全差異備份和記錄備份鏈。
- 還原特定的完整備份或差異備份以還原到特定復原點。

## <a name="prerequisites"></a>Prerequisites

在還原資料庫之前，請注意以下事項：

- 您可以將資料庫還原至相同 Azure 區域中的 SQL Server 執行個體。
- 目的地伺服器必須註冊到和來源相同的保存庫。
- 要將 TDE 加密資料庫還原到另一個 SQL Server，需要首先[將證書還原到目標伺服器](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017)。
- 在還原"主"資料庫之前，請使用啟動選項 **-m Azure 工作負荷備份**在單使用者模式下啟動 SQL Server 實例。
  - **-m**的值是用戶端的名稱。
  - 只有指定的用戶端名稱才能打開連接。
- 對於所有系統資料庫（模型、主資料庫、msdb），在觸發還原之前停止 SQL 伺服器代理服務。
- 關閉可能嘗試與其中任何一個資料庫連接的任何應用程式。
- 如果伺服器上運行了多個實例，則所有實例都應啟動並運行，否則伺服器將不會顯示在要還原資料庫的目標伺服器清單中。

## <a name="restore-a-database"></a>還原資料庫

要還原，您需要以下許可權：

- 執行還原的保存庫中的**備份操作員**許可權。
- 來源 VM (已備份) 的**參與者 (寫入)** 存取權。
- 目標 VM 的**參與者 (寫入)** 存取權：
  - 如果要還原到同一 VM，則這是源 VM。
  - 如果要還原到備用位置，這是新的目標 VM。

以下列方式進行還原：

1. 開啟其中有已註冊 SQL Server VM 的保存庫。
2. 在保存庫儀表板中的 [使用量]**** 底下，選取 [備份項目]****。
3. 在 [備份項目]**** 中的 [備份管理類型]**** 底下，選取 **Azure VM 中的 SQL**。

    ![選取 Azure VM 中的 SQL](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. 選取要還原的資料庫。

    ![選取要還原的資料庫](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. 檢閱資料庫功能表。 其中有資料庫備份的資訊，包括：

    - 最早和最近的還原點。
    - 對於處於完整和批量記錄復原模式且配置為事務記錄備份的資料庫的過去 24 小時的記錄備份狀態。

6. 選取 [還原]****。

    ![選取 [還原]](./media/backup-azure-sql-database/restore-db.png)

7. 在**還原配置**中 ，指定還原資料的位置（或方式）：
   - **備用位置**：將資料庫還原到備用位置並保留原始源資料庫。
   - **覆寫 DB**：將資料還原至與原始來源相同的 SQL Server 執行個體。 此選項會覆寫原始資料庫。

    > [!IMPORTANT]
    > 如果選取的資料庫屬於 AlwaysOn 可用性群組，SQL Server 不允許覆寫資料庫。 只有 [替代位置]**** 可用。
    >
   - **還原為檔**：而不是作為資料庫還原，而是還原備份檔案，這些檔稍後可以在任何存在檔的電腦上使用 SQL Server 管理工作室作為資料庫恢復。
     ![[還原設定] 功能表](./media/backup-azure-sql-database/restore-configuration.png)

### <a name="restore-to-an-alternate-location"></a>還原至替代位置

1. 在 **"還原配置"** 功能表中，在 **"還原位置"** 下，選擇 **"備用位置**"。
2. 選取您要在其中還原資料庫的目的地 SQL Server 名稱和執行個體。
3. 在 [還原的 DB 名稱]**** 方塊中，輸入目標資料庫的名稱。
4. 如果適用的話，請選取 [Overwrite if the DB with the same name already exists on selected SQL instance] \(若選取的 SQL 執行個體上已存在相同名稱的 DB 則覆寫\)****。
5. 選取 [確定]****。

    ![提供 [還原設定] 功能表的值](./media/backup-azure-sql-database/restore-configuration.png)

6. 在 **"選擇還原點"** 中，選擇是[還原到特定時間點](#restore-to-a-specific-point-in-time)還是[還原到特定復原點](#restore-to-a-specific-restore-point)。

    > [!NOTE]
    > 時間點還原僅適用于處於完整和批量記錄復原模式的資料庫的記錄備份。

### <a name="restore-and-overwrite"></a>還原並覆寫資料庫

1. 在 **"還原配置"** 功能表中，在 **"還原位置"** 下，選擇 **"覆蓋 DB** > **確定**"。

    ![選取 [覆寫 DB]](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. 在 **"選擇還原點**"**中，選擇"日誌"（時間點）** 以[還原到特定時間點](#restore-to-a-specific-point-in-time)。 或者選擇 **"完全&差"** 以還原到[特定復原點](#restore-to-a-specific-restore-point)。

    > [!NOTE]
    > 時間點還原僅適用于處於完整和批量記錄復原模式的資料庫的記錄備份。

### <a name="restore-as-files"></a>恢復為檔

要將備份資料還原為 .bak 檔而不是資料庫，請選擇"**還原為檔**"。 將檔轉儲到指定的路徑後，可以將這些檔帶到要將它們還原為資料庫的任何電腦。 由於能夠將這些檔移動到任何電腦，您現在可以跨訂閱和區域還原資料。

1. 在 **"還原配置"** 功能表中，在 **"還原位置"** 下，選擇 **"還原為檔**"。
2. 選擇要還原備份檔案的 SQL Server 名稱。
3. 在**伺服器上的目標路徑**中輸入步驟 2 中選擇的伺服器上的資料夾路徑。 這是服務將轉儲所有必要的備份檔案的位置。 通常，網路共用路徑或裝載的 Azure 檔共用的路徑（如果指定為目標路徑），可以更輕鬆地由同一網路中的其他電腦或裝載于這些檔上的 Azure 檔共用訪問這些檔。<BR>

    >要還原安裝在目標已註冊 VM 上的 Azure 檔共用上的資料庫備份檔案，請確保 NT AUTHORITY_SYSTEM 有權訪問檔共用。 您可以執行以下步驟，向安裝在 VM 上的 AFS 授予讀取/寫入權限：
    >
    >- 運行`PsExec -s cmd`以進入 NT 授權_SYSTEM 外殼
    >   - 執行 `cmdkey /add:<storageacct>.file.core.windows.net /user:AZURE\<storageacct> /pass:<storagekey>`
    >   - 使用`dir \\<storageacct>.file.core.windows.net\<filesharename>`
    >- 將還原作為檔從備份保存庫啟動為`\\<storageacct>.file.core.windows.net\<filesharename>`路徑<BR>
    您可以通過下載 Psexec<https://docs.microsoft.com/sysinternals/downloads/psexec>

4. 選取 [確定]****。

    ![選擇"還原為檔"](./media/backup-azure-sql-database/restore-as-files.png)

5. 選擇將還原所有可用的 .bak 檔的**還原點**。

    ![選擇還原點](./media/backup-azure-sql-database/restore-point.png)

6. 與所選復原點關聯的所有備份檔案都轉儲到目標路徑中。 您可以使用 SQL 伺服器管理工作室將檔案還原為任何電腦上的資料庫。

    ![目標路徑中已還原的備份檔案](./media/backup-azure-sql-database/sql-backup-files.png)

### <a name="restore-to-a-specific-point-in-time"></a>還原至特定時間點

如果您已選取 [記錄 (時間點)]**** 作為還原類型，請執行下列動作：

1. 在 **"還原日期/時間**"下，打開日曆。 在日曆上，具有復原點的日期以粗體顯示，當前日期突出顯示。
1. 選擇具有復原點的日期。 不能選擇沒有復原點的日期。

    ![打開日曆](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. 選取日期之後，時間軸圖表會顯示連續範圍中可用的復原點。
1. 在時間表圖上指定恢復的時間，或選擇時間。 然後選擇 **"確定**"。

    ![選擇還原時間](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

1. 在 **"高級配置"** 功能表上，如果要在還原後保持資料庫不運行，請**啟用"使用 NORECOVERY 還原**"。
1. 如果您要變更目的地伺服器上的還原位置，請輸入新的目標路徑。
1. 選取 [確定]****。

    ![進階設定功能表](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. 在 [還原]**** 功能表上，選取 [還原]**** 以啟動還原作業。
1. 跟蹤 **"通知"** 區域中的還原進度，或通過在資料庫功能表上選擇 **"還原作業**"來跟蹤它。

    ![還原作業進度](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-to-a-specific-restore-point"></a>還原至特定還原點

如果您已選取 [完整和差異]**** 作為還原類型，請執行下列動作：

1. 從清單中選取復原點，然後選取 [確定]**** 以完成還原點程序。

    ![選擇完整復原點](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

    >[!NOTE]
    > 預設情況下，將顯示過去 30 天的復原點。 您可以通過按一下 **"篩選"** 並選擇自訂範圍來顯示超過 30 天的復原點。

1. 在 **"高級配置"** 功能表上，如果要在還原後保持資料庫不運行，請**啟用"使用 NORECOVERY 還原**"。
1. 如果您要變更目的地伺服器上的還原位置，請輸入新的目標路徑。
1. 選取 [確定]****。

    ![進階設定功能表](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. 在 [還原]**** 功能表上，選取 [還原]**** 以啟動還原作業。
1. 跟蹤 **"通知"** 區域中的還原進度，或通過在資料庫功能表上選擇 **"還原作業**"來跟蹤它。

    ![還原作業進度](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-databases-with-large-number-of-files"></a>還原包含大量檔的資料庫

如果資料庫中檔的總字串大小大於[特定限制](backup-sql-server-azure-troubleshoot.md#size-limit-for-files)，Azure 備份會將資料庫檔案清單存儲在不同的 pit 元件中，這樣您將無法在還原操作期間設置目標還原路徑。 檔將改為還原到 SQL 預設路徑。

  ![使用大型檔還原資料庫](./media/backup-azure-sql-database/restore-large-files.jpg)

## <a name="next-steps"></a>後續步驟

[管理和監控](manage-monitor-sql-database-backup.md)由 Azure 備份備份的 SQL Server 資料庫。
