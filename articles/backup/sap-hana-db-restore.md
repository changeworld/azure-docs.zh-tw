---
title: 在 Azure Vm 上還原 SAP Hana 資料庫
description: 在本文中，您將探索如何還原在 Azure 虛擬機器上執行的 SAP Hana 資料庫。 您也可以使用跨區域還原，將資料庫還原到次要區域。
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: c502b7741acd343baefe5e2bf8b95cfc02e46688
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90986116"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>在 Azure Vm 上還原 SAP Hana 資料庫

本文說明如何還原在 Azure 虛擬機器上執行的 SAP Hana 資料庫 (VM) （Azure 備份服務已備份至復原服務保存庫）。 還原可以用來建立適用于開發/測試案例的資料複本，或回到先前的狀態。

如需有關如何備份 SAP Hana 資料庫的詳細資訊，請參閱 [備份 Azure vm 上的 SAP Hana 資料庫](./backup-azure-sap-hana-database.md)。

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>還原至某個時間點或復原點

Azure 備份可以還原在 Azure VM 上執行的 SAP HANA 資料庫，如下所示：

* 使用記錄備份還原至特定日期或時間 (可精確到秒)。 Azure 備份會根據選取的時間，自動決定還原所需的適當完整差異備份和記錄備份鏈結。

* 還原至特定的完整或差異備份以還原至特定復原點。

## <a name="prerequisites"></a>必要條件

在還原資料庫之前，請注意下列事項：

* 您只能將資料庫還原到相同區域中的 SAP Hana 實例。

* 目標實例必須在與來源相同的保存庫中註冊。

* Azure 備份無法在相同的 VM 上識別出兩個不同的 SAP HANA 執行個體。 因此，您無法在相同的 VM 上將資料從某個實例還原到另一個實例。

* 若要確定目標 SAP Hana 實例已準備好進行還原，請檢查其 **備份準備就緒** 狀態：

  1. 開啟註冊目標 SAP Hana 實例的保存庫。

  1. 在保存庫儀表板的 [使用者 **入門**] 下，選擇 [ **備份**]。

      ![保存庫儀表板中的備份](media/sap-hana-db-restore/getting-started-backup.png)

  1. 在 [ **備份**] 的 [ **您要備份什麼？** ] 下，選擇 [ **Azure VM 中的 SAP Hana**]。

      ![選擇 [Azure VM 中的 SAP HANA]](media/sap-hana-db-restore/sap-hana-backup.png)

  1. 在 [ **探索 vm 中的 db**] 下，選取 [ **視圖詳細資料**]。

      ![檢視詳細資料](media/sap-hana-db-restore/view-details.png)

  1. 檢查目標 VM 的 **備份是否就緒** 。

      ![受保護的伺服器](media/sap-hana-db-restore/protected-servers.png)

* 若要深入瞭解 SAP Hana 支援的還原類型，請參閱 SAP Hana 附注 [1642148](https://launchpad.support.sap.com/#/notes/1642148)

## <a name="restore-a-database"></a>還原資料庫

若要還原，您需要下列許可權：

* 您正在進行還原之保存庫中的**備份操作員**許可權。
* 來源 VM (已備份) 的**參與者 (寫入)** 存取權。
* **參與者 (** 將) 存取權寫入目標 VM：
  * 如果您要還原至相同的 VM，這是來源 VM。
  * 如果您要還原至替代位置，這是新的目標 VM。

1. 開啟要用來還原 SAP Hana 資料庫的保存庫

1. 在保存庫儀表板的 [**受保護的專案**] 下，選擇 [**備份專案**]

    ![備份項目](media/sap-hana-db-restore/backup-items.png)

1. 在 [**備份專案**] 的 [**備份管理類型**] 下，選取 [ **Azure VM 中的 SAP Hana** ]

    ![備份管理類型](media/sap-hana-db-restore/backup-management-type.png)

1. 選取要還原的資料庫

    ![要還原的資料庫](media/sap-hana-db-restore/database-to-restore.png)

1. 檢閱資料庫功能表。 它會提供資料庫備份的相關資訊，包括：

    * 最舊和最新的還原點

    * 資料庫過去24和72小時的記錄備份狀態

    ![資料庫功能表](media/sap-hana-db-restore/database-menu.png)

1. 選取 **還原資料庫**

1. 在 [ **還原**設定] 下，指定 (或如何) 還原資料的位置：

    * **替代位置**：將資料庫還原到替代位置，並保留原始源資料庫。

    * **覆寫 DB**：將資料還原至與原始來源相同的 SAP Hana 實例。 此選項會覆寫原始資料庫。

      ![還原設定](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>還原至替代位置

1. 在 [ **還原** 設定] 功能表的 [ **要還原的位置**] 下，選取 [ **替代位置**]。

    ![還原至替代位置](media/sap-hana-db-restore/restore-alternate-location.png)

1. 選取您要用來還原資料庫的 SAP Hana 主控制項名稱和實例名稱。
1. 確認目標 SAP Hana 實例是否已準備好進行還原，方法是確保其 **備份準備就緒。** 如需詳細資訊，請參閱 [必要條件一節](#prerequisites) 。
1. 在 [還原的 DB 名稱]**** 方塊中，輸入目標資料庫的名稱。

    > [!NOTE]
    > 單一資料庫容器 (SDC) 還原必須遵循這些 [檢查](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore)。

1. 如果 **有相同名稱的資料庫已經存在於所選的 HANA 實例上**，請選取 [覆寫]。
1. 選取 [確定]  。

    ![還原設定-最終畫面](media/sap-hana-db-restore/restore-configuration-last.png)

1. 在 [ **選取還原點**] 中，選取 [ **記錄] (時間點) ** [還原至特定時間點](#restore-to-a-specific-point-in-time)。 或者，選取 [ **Full & 差異** ] 以 [還原至特定復原點](#restore-to-a-specific-recovery-point)。

### <a name="restore-and-overwrite"></a>還原並覆寫資料庫

1. 在 [**還原**設定] 功能表的 [**要還原的位置**] 下，選取 [**覆寫 DB**  >  **確定]**。

    ![覆寫 DB](media/sap-hana-db-restore/overwrite-db.png)

1. 在 [ **選取還原點**] 中，選取 [ **記錄] (時間點) ** [還原至特定時間點](#restore-to-a-specific-point-in-time)。 或者，選取 [ **Full & 差異** ] 以 [還原至特定復原點](#restore-to-a-specific-recovery-point)。

### <a name="restore-as-files"></a>還原為檔案

若要將備份資料還原為檔案，而不是資料庫，請選擇 [ **還原為**檔案]。 一旦檔案傾印到指定的路徑，您就可以將這些檔案放到任何 SAP Hana 機器上，並在其中將檔案還原為資料庫。 因為您可以將這些檔案移至任何機器，所以您現在可以在訂用帳戶和區域之間還原資料。

1. 在 [ **還原** 設定] 功能表中，在 [ **要還原的位置和方式**] 下，選取 [ **還原為**檔案]。
1. 選取您要用來還原備份檔案的 **主機** /HANA 伺服器名稱。
1. 在伺服器的 [ **目的地路徑**] 中，輸入在步驟2中選取之伺服器上的資料夾路徑。 這是服務將用來傾印所有必要備份檔案的位置。

    傾印的檔案包括：

    * 資料庫備份檔案
    * 類別目錄檔案
    * JSON 中繼資料檔案 (每個包含的備份檔案都有)

    一般而言，網路共用路徑 (或是指定為目的地路徑的掛接 Azure 檔案路徑) 可讓相同網路中或掛接相同 Azure 檔案共用的其他機器，更輕鬆地存取這些檔案。

    >[!NOTE]
    >若要在目標 (已註冊的 VM) 所掛接的 Azure 檔案共用上還原資料庫備份檔案，請確定根帳戶具有 Azure 檔案共用的讀取/寫入權限。

    ![選擇目的地路徑](media/sap-hana-db-restore/restore-as-files.png)

1. 選取對應的 **還原點** ，所有備份檔案和資料夾都會還原。

    ![選取還原點](media/sap-hana-db-restore/select-restore-point.png)

1. 與所選還原點相關聯的所有備份檔案都會傾印到目的地路徑。
1. 根據選擇的還原點類型 (**時間點**或**完整和差異**)，您會看到目的地路徑中建立一個或多個資料夾。 其中一個名為 `Data_<date and time of restore>` 的資料夾會包含完整和差異備份，而另一個名為 `Log` 的資料夾則會包含記錄備份。
1. 將這些還原的檔案移至您要在其中將檔案還原為資料庫的 SAP Hana 伺服器。
1. 接著，依照下列步驟執行：
    1. 使用下列命令，在儲存備份檔案的資料夾/目錄上設定權限：

        ```bash
        chown -R <SID>adm:sapsys <directory>
        ```

    1. 以 `<SID>adm` 執行下一個命令集

        ```bash
        su - <sid>adm
        ```

    1. 產生用於還原的類別目錄檔案。 從 JSON 中繼資料檔案中擷取用於完整備份的 **BackupId**，稍後在還原作業中會用到。 請確定完整和記錄備份都在不同的資料夾中，並刪除這些資料夾中的類別目錄檔案和 JSON 中繼資料檔案。

        ```bash
        hdbbackupdiag --generate --dataDir <DataFileDir> --logDirs <LogFilesDir> -d <PathToPlaceCatalogFile>
        ```

        在上述命令中：

        * `<DataFileDir>` - 包含完整備份的資料夾
        * `<LogFilesDir>` - 包含記錄備份的資料夾
        * `<PathToPlaceCatalogFile>` - 該資料夾必須放置產生的類別目錄檔案

    1. 透過 Hana Studio 使用新產生的類別目錄來還原，或以這個新產生的目錄執行 HDBSQL 還原查詢。 HDBSQL 查詢如下所列：

    * 還原到某個時間點：

        如果您要建立新的已還原資料庫，請執行 HDBSQL 命令來建立新的資料庫 `<DatabaseName>`，然後停止該資料庫來進行還原。 不過，如果您只是要還原現有的資料庫，請執行 HDBSQL 命令來停止資料庫。

        然後執行下列命令來還原資料庫：

        ```hdbsql
        RECOVER DATABASE FOR <DatabaseName> UNTIL TIMESTAMP '<TimeStamp>' CLEAR LOG USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING LOG PATH (' <LogFileDir>') USING DATA PATH ('<DataFileDir>') USING BACKUP_ID <BackupIdFromJsonFile> CHECK ACCESS USING FILE
        ```

        * `<DatabaseName>` - 您想要還原的新資料庫名稱或現有資料庫名稱
        * `<Timestamp>` - 時間點還原的確切時間戳記
        * `<DatabaseName@HostName>` - 用其備份來進行還原的資料庫名稱，以及此資料庫所在的**主機**/SAP Hana 伺服器名稱。 `USING SOURCE <DatabaseName@HostName>` 選項會指定資料備份 (用於還原) 源自 SID 或名稱與目標 SAP Hana 機器不同的資料庫。 因此，它不需要針對在進行備份的相同 HANA 伺服器上進行的還原進行指定。
        * `<PathToGeneratedCatalogInStep3>`-**步驟 C**中所產生之類別目錄檔案的路徑
        * `<DataFileDir>` - 包含完整備份的資料夾
        * `<LogFilesDir>` - 包含記錄備份的資料夾
        * `<BackupIdFromJsonFile>`-在**步驟 C**中解壓縮的**BackupId**

    * 若要還原至特定的完整或差異備份：

        如果您要建立新的已還原資料庫，請執行 HDBSQL 命令來建立新的資料庫 `<DatabaseName>`，然後停止該資料庫來進行還原。 不過，如果您只是要還原現有的資料庫，請執行 HDBSQL 命令來停止資料庫：

        ```hdbsql
        RECOVER DATA FOR <DatabaseName> USING BACKUP_ID <BackupIdFromJsonFile> USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING DATA PATH ('<DataFileDir>')  CLEAR LOG
        ```

        * `<DatabaseName>` - 您想要還原的新資料庫或現有資料庫的名稱
        * `<Timestamp>` - 時間點還原的確切時間戳記
        * `<DatabaseName@HostName>` - 用其備份來進行還原的資料庫名稱，以及此資料庫所在的**主機**/SAP Hana 伺服器名稱。 `USING SOURCE <DatabaseName@HostName>` 選項會指定資料備份 (用於還原) 源自 SID 或名稱與目標 SAP Hana 機器不同的資料庫。 因此，您不需要針對相同 HANA 伺服器 (備份會從中建立) 上執行的還原加以指定。
        * `<PathToGeneratedCatalogInStep3>`-**步驟 C**中所產生之類別目錄檔案的路徑
        * `<DataFileDir>` - 包含完整備份的資料夾
        * `<LogFilesDir>` - 包含記錄備份的資料夾
        * `<BackupIdFromJsonFile>`-在**步驟 C**中解壓縮的**BackupId**

### <a name="restore-to-a-specific-point-in-time"></a>還原至特定時間點

如果您已選取 [記錄 (時間點)]**** 作為還原類型，請執行下列動作：

1. 從記錄圖形中選取復原點，然後選取 **[確定]** 以選擇還原時間點。

    ![還原點](media/sap-hana-db-restore/restore-point.png)

1. 在 [還原]**** 功能表上，選取 [還原]**** 以啟動還原作業。

    ![選取還原](media/sap-hana-db-restore/restore-restore.png)

1. 在 [ **通知** ] 區域中追蹤還原進度，或選取 [資料庫] 功能表上的 [ **還原作業** ] 來追蹤還原進度。

    ![已成功觸發還原](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>還原至特定復原點

如果您已選取 [完整和差異]**** 作為還原類型，請執行下列動作：

1. 從清單中選取復原點，然後選取 **[確定]** 以選擇還原時間點。

    ![還原特定復原點](media/sap-hana-db-restore/specific-recovery-point.png)

1. 在 [還原]**** 功能表上，選取 [還原]**** 以啟動還原作業。

    ![啟動還原作業](media/sap-hana-db-restore/restore-specific.png)

1. 在 [ **通知** ] 區域中追蹤還原進度，或選取 [資料庫] 功能表上的 [ **還原作業** ] 來追蹤還原進度。

    ![還原進度](media/sap-hana-db-restore/restore-progress.png)

    > [!NOTE]
    > 在多個資料庫容器 (MDC) 在系統資料庫還原至目標實例之後進行還原，而必須再次執行預先註冊腳本。 只有這麼做之後，後續的租用戶 DB 才能還原成功。 若要深入瞭解，請參閱 [疑難排解-MDC 還原](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore)。

## <a name="cross-region-restore"></a>跨區域還原

作為其中一個還原選項，跨區域還原 (CRR) 可讓您在次要區域（也就是 Azure 配對的區域）中，還原裝載于 Azure Vm 上的 SAP Hana 資料庫。

若要在預覽期間登入功能，請閱讀 [ [開始之前] 區段](./backup-create-rs-vault.md#set-cross-region-restore)。

若要查看是否已啟用 CRR，請依照[設定跨區域還原](backup-create-rs-vault.md#configure-cross-region-restore)中的指示進行。

### <a name="view-backup-items-in-secondary-region"></a>查看次要區域中的備份專案

如果已啟用 CRR，您可以在次要區域中查看備份專案。

1. 從入口網站移至 [復原**服務保存庫**  >  **備份專案**]。
1. 選取 **次要區域** 以查看次要區域中的專案。

>[!NOTE]
>清單中只會顯示支援 CRR 功能的備份管理類型。 目前，僅支援將次要區域資料還原至次要區域。

![次要區域中的備份專案](./media/sap-hana-db-restore/backup-items-secondary-region.png)

![次要區域中的資料庫](./media/sap-hana-db-restore/databases-secondary-region.png)

### <a name="restore-in-secondary-region"></a>在次要區域中還原

次要區域還原使用者體驗將類似于主要區域還原使用者體驗。 在 [還原設定] 窗格中設定詳細資料來設定您的還原時，系統會提示您只提供次要區域參數。

![還原的位置和方式](./media/sap-hana-db-restore/restore-secondary-region.png)

>[!NOTE]
>次要區域中的虛擬網路必須是唯一指派的，且不能用於該資源群組中的任何其他 Vm。

![觸發還原進行中的通知](./media/backup-azure-arm-restore-vms/restorenotifications.png)

>[!NOTE]
>
>* 在觸發還原並且在資料傳輸階段中，無法取消還原作業。
>* 在次要區域中還原所需的 Azure 角色與主要區域中的角色相同。

### <a name="monitoring-secondary-region-restore-jobs"></a>監視次要區域還原作業

1. 從入口網站移至復原**服務保存庫**  >  **備份作業**
1. 選取 **次要區域** 以查看次要區域中的專案。

    ![已篩選的備份作業](./media/sap-hana-db-restore/backup-jobs-secondary-region.png)

## <a name="next-steps"></a>後續步驟

* [瞭解如何](sap-hana-db-manage.md) 使用 Azure 備份來管理 SAP Hana 資料庫備份
