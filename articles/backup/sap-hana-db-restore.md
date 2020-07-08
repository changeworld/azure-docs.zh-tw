---
title: 在 Azure Vm 上還原 SAP Hana 資料庫
description: 在本文中，您將瞭解如何還原在 Azure 虛擬機器上執行 SAP Hana 資料庫。
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: a3db88ca3c995c3c190da051dbf9df6ae5e29530
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85851434"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>在 Azure Vm 上還原 SAP Hana 資料庫

本文說明如何還原在 Azure 備份服務已備份至復原服務保存庫的 Azure 虛擬機器（VM）上執行的 SAP Hana 資料庫。 還原可用於建立開發/測試案例的資料複本，或回到先前的狀態。

如需有關如何備份 SAP Hana 資料庫的詳細資訊，請參閱在[Azure vm 上備份 SAP Hana 資料庫](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)。

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>還原到某個時間點或復原點

Azure 備份可以還原在 Azure VM 上執行的 SAP HANA 資料庫，如下所示：

* 使用記錄備份還原至特定日期或時間 (可精確到秒)。 Azure 備份會根據選取的時間，自動決定還原所需的適當完整差異備份和記錄備份鏈結。

* 還原至特定的完整或差異備份以還原至特定復原點。

## <a name="prerequisites"></a>必要條件

在還原資料庫之前，請注意下列事項：

* 您只能將資料庫還原到相同區域中的 SAP HANA 執行個體

* 目標執行個體必須使用相同的來源保存庫進行註冊

* Azure 備份無法在相同的 VM 上識別出兩個不同的 SAP HANA 執行個體。 因此，您無法在相同的 VM 上將資料從某個實例還原到另一個實例。

* 若要確定目標 SAP Hana 實例已準備好進行還原，請檢查其**備份就緒**狀態：

  1. 開啟用來註冊目標 SAP Hana 實例的保存庫

  1. 在保存庫儀表板的 [**使用者入門] 底下，選擇**[**備份**]

      ![保存庫中的備份儀表板](media/sap-hana-db-restore/getting-started-backup.png)

  1. 在 [**備份**] 中，于 [**您要備份什麼？** ] 下選擇 [**在 Azure VM 中 SAP Hana** ]

      ![選擇 [Azure VM 中的 SAP HANA]](media/sap-hana-db-restore/sap-hana-backup.png)

  1. 在 [**探索 vm 中的 db**] 底下，按一下 [**查看詳細資料**]

      ![檢視詳細資料](media/sap-hana-db-restore/view-details.png)

  1. 審查目標 VM 的**備份就緒**程度

      ![受保護的伺服器](media/sap-hana-db-restore/protected-servers.png)

* 若要深入瞭解 SAP Hana 支援的還原類型，請參閱 SAP Hana 附注[1642148](https://launchpad.support.sap.com/#/notes/1642148)

## <a name="restore-a-database"></a>還原資料庫

若要還原，您需要下列許可權：

* 執行還原之保存庫中的**備份操作員**許可權。
* 來源 VM (已備份) 的**參與者 (寫入)** 存取權。
* 目標 VM 的**參與者（寫入**）存取權：
  * 如果您要還原至相同的 VM，這就是來源 VM。
  * 如果您要還原至替代位置，這就是新的目標 VM。

1. 開啟要用來還原 SAP Hana 資料庫的保存庫

1. 在保存庫儀表板的 [**受保護的專案**] 底下，選擇 [**備份專案**]

    ![備份項目](media/sap-hana-db-restore/backup-items.png)

1. 在 [備份**專案**] 的 [**備份管理類型**] 底下，選取 [ **Azure VM 中的 SAP Hana** ]

    ![備份管理類型](media/sap-hana-db-restore/backup-management-type.png)

1. 選取要還原的資料庫

    ![要還原的資料庫](media/sap-hana-db-restore/database-to-restore.png)

1. 檢閱資料庫功能表。 它提供資料庫備份的相關資訊，包括：

    * 最舊和最新的還原點

    * 資料庫過去24和72小時的記錄備份狀態

    ![資料庫功能表](media/sap-hana-db-restore/database-menu.png)

1. 選取**還原資料庫**

1. 在 [**還原**設定] 底下，指定還原資料的位置（或如何）：

    * **替代位置**：將資料庫還原至替代位置，並保留原始源資料庫。

    * **覆寫 DB**：將資料還原至與原始來源相同的 SAP Hana 實例。 此選項會覆寫原始資料庫。

      ![還原設定](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>還原至替代位置

1. 在 [**還原**設定] 功能表的 [**要還原的位置**] 底下，選取 [**替代位置**]。

    ![還原至替代位置](media/sap-hana-db-restore/restore-alternate-location.png)

1. 選取您要還原資料庫的 SAP Hana 主機名稱和實例名稱。
1. 確認目標 SAP Hana 實例是否已準備好進行還原，方法是確保其**備份準備就緒。** 如需詳細資訊，請參閱[必要條件一節](#prerequisites)。
1. 在 [還原的 DB 名稱]**** 方塊中，輸入目標資料庫的名稱。

    > [!NOTE]
    > 單一資料庫容器（SDC）還原必須遵循這些[檢查](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore)。

1. 如果適用的話，請選取 [**覆寫] （如果已有相同名稱的資料庫存在於所選的 HANA 實例上**）。
1. 選取 [確定]。

    ![還原設定-最終畫面](media/sap-hana-db-restore/restore-configuration-last.png)

1. 在 [**選取還原點**] 中，選取 **[記錄（時間點）** ] 以[還原至特定時間點](#restore-to-a-specific-point-in-time)。 或選取 [**完整 & 差異**]，以[還原至特定復原點](#restore-to-a-specific-recovery-point)。

### <a name="restore-and-overwrite"></a>還原並覆寫資料庫

1. 在 [**還原**設定] 功能表的 [**要還原的位置**] 底下，選取 [**覆寫 DB**  >  **正常]**。

    ![覆寫 DB](media/sap-hana-db-restore/overwrite-db.png)

1. 在 [**選取還原點**] 中，選取 **[記錄（時間點）** ] 以[還原至特定時間點](#restore-to-a-specific-point-in-time)。 或選取 [**完整 & 差異**]，以[還原至特定復原點](#restore-to-a-specific-recovery-point)。

### <a name="restore-as-files"></a>還原為檔案

若要將備份資料還原為檔案，而不是資料庫，請選擇 [**還原為**檔案]。 一旦檔案傾印到指定的路徑，您就可以將這些檔案放到您想要將它們還原為資料庫的任何 SAP Hana 電腦上。 因為您可以將這些檔案移至任何機器，所以您現在可以在訂用帳戶和區域之間還原資料。

1. 在 [**還原**設定] 功能表的 [在**何處和如何還原**] 底下，選取 [**還原為**檔案]。
1. 選取您要還原備份檔案的**主機**/HANA 伺服器名稱。
1. 在**伺服器的目的地路徑**中，輸入在步驟2中選取之伺服器上的資料夾路徑。 這是服務將會傾印所有必要備份檔案的位置。

    傾印的檔案包括：

    * 資料庫備份檔案
    * 類別目錄檔案
    * JSON 中繼資料檔案（針對每個相關的備份檔案）

    一般而言，當指定為目的地路徑時，已掛接之 Azure 檔案共用的網路共用路徑或路徑，可讓相同網路中的其他電腦或掛接在其上的相同 Azure 檔案共用，更輕鬆地存取這些檔案。

    >[!NOTE]
    >若要在裝載于目標的已註冊 VM 上的 Azure 檔案共用上還原資料庫備份檔案，請確定根帳號具有 Azure 檔案共用的讀取/寫入權限。

    ![選擇目的地路徑](media/sap-hana-db-restore/restore-as-files.png)

1. 選取對應至將還原所有備份檔案和資料夾的**還原點**。

    ![選取還原點](media/sap-hana-db-restore/select-restore-point.png)

1. 與所選還原點相關聯的所有備份檔案都會傾印到目的地路徑。
1. 根據所選擇的還原點類型（**時間點**或**完整 & 差異**），您會看到在目的地路徑中建立的一或多個資料夾。 其中一個名為的資料夾 `Data_<date and time of restore>` 包含完整和差異備份，而另一個名為的資料夾 `Log` 包含記錄備份。
1. 將這些還原的檔案移至您想要將它們還原為資料庫的 SAP Hana 伺服器。
1. 接著，遵循下列步驟：
    1. 使用下列命令，在儲存備份檔案的資料夾/目錄上設定許可權：

        ```bash
        chown -R <SID>adm:sapsys <directory>
        ```

    1. 執行下一組命令，如下所示`<SID>adm`

        ```bash
        su - <sid>adm
        ```

    1. 產生用於還原的類別目錄檔案。 從 JSON 中繼資料檔案解壓縮**BackupId** ，以取得完整備份，稍後將在還原作業中使用。 請確定完整和記錄備份都在不同的資料夾中，並刪除這些資料夾中的類別目錄檔案和 JSON 中繼資料檔案。

        ```bash
        hdbbackupdiag --generate --dataDir <DataFileDir> --logDirs <LogFilesDir> -d <PathToPlaceCatalogFile>
        ```

        在上述命令中：

        * `<DataFileDir>`-包含完整備份的資料夾
        * `<LogFilesDir>`-包含記錄備份的資料夾
        * `<PathToPlaceCatalogFile>`-必須放置產生之類別目錄檔案的資料夾

    1. 透過 HANA Studio 使用新產生的類別目錄來還原，或以這個新產生的目錄執行到 HDBSQL restore 查詢。 到 HDBSQL 查詢如下所列：

    * 若要還原到某個時間點：

        如果您要建立新的還原資料庫，請執行到 HDBSQL 命令來建立新的資料庫， `<DatabaseName>` 然後停止資料庫進行還原。 不過，如果您只是要還原現有的資料庫，請執行到 HDBSQL 命令來停止資料庫。

        然後執行下列命令來還原資料庫：

        ```hdbsql
        RECOVER DATABASE FOR <DatabaseName> UNTIL TIMESTAMP '<TimeStamp>' CLEAR LOG USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING LOG PATH (' <LogFileDir>') USING DATA PATH ('<DataFileDir>') USING BACKUP_ID <BackupIdFromJsonFile> CHECK ACCESS USING FILE
        ```

        * `<DatabaseName>`-您想要還原的新資料庫或現有資料庫的名稱
        * `<Timestamp>`-時間點還原的確切時間戳記
        * `<DatabaseName@HostName>`-用來還原備份的資料庫名稱，以及此資料庫所在的**主機**/SAP Hana 伺服器名稱。 `USING SOURCE <DatabaseName@HostName>`選項指定資料備份（用於還原）的資料庫，其 SID 或名稱與目標 SAP Hana 機不同。 因此，您不需要針對在備份建立所在的同一 HANA 伺服器上完成的還原進行指定。
        * `<PathToGeneratedCatalogInStep3>`-在**步驟 C**中產生之類別目錄檔案的路徑
        * `<DataFileDir>`-包含完整備份的資料夾
        * `<LogFilesDir>`-包含記錄備份的資料夾
        * `<BackupIdFromJsonFile>`-在**步驟 C**中解壓縮的**BackupId**

    * 若要還原至特定的完整或差異備份：

        如果您要建立新的還原資料庫，請執行到 HDBSQL 命令來建立新的資料庫， `<DatabaseName>` 然後停止資料庫進行還原。 不過，如果您只是要還原現有的資料庫，請執行到 HDBSQL 命令來停止資料庫：

        ```hdbsql
        RECOVER DATA FOR <DatabaseName> USING BACKUP_ID <BackupIdFromJsonFile> USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING DATA PATH ('<DataFileDir>')  CLEAR LOG
        ```

        * `<DatabaseName>`-您想要還原的新資料庫或現有資料庫的名稱
        * `<Timestamp>`-時間點還原的確切時間戳記
        * `<DatabaseName@HostName>`-用來還原備份的資料庫名稱，以及此資料庫所在的**主機**/SAP Hana 伺服器名稱。 `USING SOURCE <DatabaseName@HostName>`選項指定資料備份（用於還原）的資料庫，其 SID 或名稱與目標 SAP Hana 機不同。 因此，您不需要在建立備份的同一 HANA 伺服器上指定進行還原。
        * `<PathToGeneratedCatalogInStep3>`-在**步驟 C**中產生之類別目錄檔案的路徑
        * `<DataFileDir>`-包含完整備份的資料夾
        * `<LogFilesDir>`-包含記錄備份的資料夾
        * `<BackupIdFromJsonFile>`-在**步驟 C**中解壓縮的**BackupId**

### <a name="restore-to-a-specific-point-in-time"></a>還原至特定時間點

如果您已選取 [記錄 (時間點)]**** 作為還原類型，請執行下列動作：

1. 從記錄圖形中選取復原點，然後選取 **[確定]** 以選擇還原點。

    ![還原點](media/sap-hana-db-restore/restore-point.png)

1. 在 [還原]**** 功能表上，選取 [還原]**** 以啟動還原作業。

    ![選取還原](media/sap-hana-db-restore/restore-restore.png)

1. 在 [**通知**] 區域中追蹤還原進度，或選取 [資料庫] 功能表上的 [**還原作業**] 加以追蹤。

    ![已成功觸發還原](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>還原至特定復原點

如果您已選取 [完整和差異]**** 作為還原類型，請執行下列動作：

1. 從清單中選取復原點，然後選取 **[確定]** 以選擇還原點。

    ![還原特定復原點](media/sap-hana-db-restore/specific-recovery-point.png)

1. 在 [還原]**** 功能表上，選取 [還原]**** 以啟動還原作業。

    ![啟動還原作業](media/sap-hana-db-restore/restore-specific.png)

1. 在 [**通知**] 區域中追蹤還原進度，或選取 [資料庫] 功能表上的 [**還原作業**] 加以追蹤。

    ![還原進度](media/sap-hana-db-restore/restore-progress.png)

    > [!NOTE]
    > 在將系統資料庫還原至目標實例之後，在多個資料庫容器（MDC）還原中，需要再次執行預先註冊腳本。 只有這麼做之後，後續的租用戶 DB 才能還原成功。 若要深入瞭解，請參閱[疑難排解-MDC 還原](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore)。

## <a name="next-steps"></a>後續步驟

* [瞭解如何](sap-hana-db-manage.md)管理使用 Azure 備份備份的 SAP Hana 資料庫
