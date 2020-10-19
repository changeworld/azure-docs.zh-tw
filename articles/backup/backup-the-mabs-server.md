---
title: 備份 MABS 伺服器
description: 瞭解如何備份 Microsoft Azure 備份 Server (MABS) 。
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: 81a6ee005e15b1d7ab7b11a938b8ab14143818f4
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92172126"
---
# <a name="back-up-the-mabs-server"></a>備份 MABS 伺服器

若要確保在 Microsoft Azure 備份 Server (MABS) 失敗時可以復原資料，您需要有備份 MABS 伺服器的策略。 如果未備份，您將需要在失敗之後手動重建，而磁片型復原點將無法復原。 您可以藉由備份 MABS 資料庫來備份 MABS 伺服器。

## <a name="back-up-the-mabs-database"></a>備份 MABS 資料庫

作為 MABS 備份策略的一部分，您必須備份 MABS 資料庫。 MABS 資料庫的名稱為 DPMDB。 此資料庫包含 MABS 設定，以及有關 MABS 備份的資料。 如果發生嚴重損壞狀況，您可以使用資料庫的最新備份來重建 MABS 伺服器的大部分功能。 假設您可以還原資料庫，以磁帶為基礎的備份可供存取，而且它們會維護所有保護群組設定和備份排程。 如果 MABS 存放集區磁片不受中斷影響，則重建後也可使用磁片型備份。 您可以使用幾種不同的方法來備份資料庫。

|資料庫備份方法|優點|缺點|
|--------------------------|--------------|-----------------|
|[備份至 Azure](#back-up-to-azure)|<li>在 MABS 中輕鬆設定及監視。<li>備份資料庫檔案位於多處。<li>雲端儲存體提供強固的災害復原解決方案。<li>資料庫的儲存體非常安全。<li>支援 120 個線上復原點。|<li>需要 Azure 帳戶和其他 MABS 設定。 會產生一些 Azure 儲存體成本。<li> 需要具有 Azure 代理程式的 Windows Server 系統支援版本，才能存取儲存在 Azure 備份保存庫中的 MABS 備份。 這不能是另一個 MABS 伺服器。<li>如果資料庫裝載在本機，而且您想要啟用次要保護，則不適用這個選項。 <li>會產生一些額外的準備和復原時間。|
|[藉由備份 MABS 存放集區來備份資料庫](#back-up-the-database-by-backing-up-the-mabs-storage-pool)|<li>容易設定和監視。<li>備份會保留在 MABS 存放集區磁片上，而且很容易在本機存取。<li>MABS 排程備份支援512快速完整備份。 如果您每小時備份一次，您將會有21天的完整保護。|<li>不是很適合用於災害復原。 如果 MABS 伺服器或存放集區磁片故障，復原可能無法如預期般運作。<li>如果資料庫裝載在本機，而且您想要啟用次要保護，則不適用這個選項。 <li>如果 MABS 服務或主控台未執行或無法運作，則需要進行一些準備和特殊步驟，才能取得復原點的存取權。|
|[利用原生 SQL Server 備份來備份到本機磁碟](#back-up-with-native-sql-server-backup-to-a-local-disk)|<li>內建到 SQL Server 中。<li>備份會保留在可輕鬆存取的本機磁片上。<li>可以依您要的執行頻率排程。<li>完全獨立于 MABS。<li>您可以排程備份檔案清理。|<li>除非將備份複製到遠端位置，否則不是很適合用於災害復原。<li>需要本機儲存體進行備份，這可能會限制保留和頻率。|
|[使用原生 SQL backup 和 MABS 保護備份至受 MABS 保護的共用](#back-up-to-a-share-protected-by-mabs)|<li>在 MABS 中輕鬆監視。<li>備份資料庫檔案位於多處。<li>可輕易地從網路上的任何 Windows 電腦存取。<li>可能是最快的復原方法。|<li>僅支援 64 個復原點。<li>不是很適合用於網站災害復原。 MABS 伺服器或 MABS 存放集區磁片故障可能會妨礙復原工作。<li>如果 MABS DB 裝載在本機，而且您想要啟用次要保護，則不是選項。 <li>需要進行一些額外的準備工作，才能加以設定和測試。<li>如果 MABS 伺服器本身已關閉，但 MABS 的存放集區磁片正常，則需要一些額外的準備和復原時間。|

- 如果您使用 MABS 保護群組進行備份，建議您針對資料庫使用唯一的保護群組。

    > [!NOTE]
    > 為了進行還原，您要使用 MABS 資料庫還原的 MABS 安裝必須符合 MABS 資料庫本身的版本。  例如，如果您想要復原的資料庫是從 MABS V3 安裝更新彙總套件1，則 MABS 伺服器必須執行更新彙總套件1的相同版本。 這表示您可能必須先卸載再重新安裝具有相容版本的 MABS，然後再還原資料庫。  若要檢查資料庫版本，您可能需要手動將其裝載到暫存資料庫名稱，然後針對資料庫執行 SQL 查詢，根據主要與次要版本檢查最新安裝的更新彙總套件。

- 若要檢查 MABS 資料庫版本，請遵循下列步驟：

    1. 若要執行查詢，請開啟 SQL Management Studio，然後連接到執行 MABS 資料庫的 SQL 實例。

    2. 選取 [MABS] 資料庫，然後啟動新的查詢。

    3. 將下列 SQL 查詢貼入查詢窗格中執行：

        `Select distinct MajorVersionNumber,MinorVersionNumber ,BuildNumber, FileName FROM dbo.tbl\_AM\_AgentPatch order byMajorVersionNumber,MinorVersionNumber,BuildNumber`

    如果查詢結果中未傳回任何內容，或 MABS 伺服器是從舊版升級，但自那時起未安裝任何新的更新彙總套件，則不會有主要、次要的 MABS 專案。 若要檢查與更新彙總套件相關聯的 MABS 版本，請參閱 [MABS 的組建編號清單](https://social.technet.microsoft.com/wiki/contents/articles/36381.microsoft-azure-backup-server-list-of-build-numbers.aspx)。

### <a name="back-up-to-azure"></a>備份至 Azure

1. 開始之前，您必須執行腳本來取出 MABS 複本磁片區掛接點路徑，讓您知道哪一個復原點包含 MABS 備份。 請在 Azure 備份完成初始複寫後執行這項操作。 在腳本中， `dplsqlservername%` 以裝載 MABS 資料庫的 SQL Server 實例名稱取代。

    ```SQL
    Select ag.NetbiosName as ServerName,ds.DataSourceName,vol.MountPointPath
    from tbl_IM_DataSource as ds
    join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
    join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
    join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
    and vol.Usage =1
    and lr.Validity in (1,2)
    where ds.datasourcename like '%dpmdb%'
    and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB
    ```

    請確定您已安裝 Azure 復原服務代理程式時所指定的密碼，而且已在 Azure 備份保存庫中註冊 MABS 伺服器。 您將需有此密碼，才能還原備份。

2. 建立 Azure 備份保存庫，下載 Azure 備份代理程式安裝檔案和保存庫認證。 執行安裝檔案以在 MABS 伺服器上安裝代理程式，並使用保存庫認證在保存庫中註冊 MABS 伺服器。 [深入了解](backup-azure-microsoft-azure-backup.md)。

3. 設定好保存庫之後，請設定包含 MABS 資料庫的 MABS 保護群組。 選取即可將它備份到磁片和 Azure。

#### <a name="recover-the-mabs-database-from-azure"></a>從 Azure 復原 MABS 資料庫

您可以使用 Azure 備份保存庫中註冊的任何 MABS 伺服器，從 Azure 復原資料庫，如下所示：

1. 在 MABS 主控台中 **，選取 [** 復原  >  **新增外部 MABS**]。

2. 提供保存庫認證 (從 Azure 備份保存庫) 下載。 請注意，認證的有效期僅有兩天。

3. 在 [選取要復原的 **外部 MABS**] 中，選取您要復原資料庫的 MABS 伺服器，輸入加密複雜密碼，然後選取 **[確定]。**

4. 從可用復原點清單中選取您要使用的復原點。 選取 [ **清除外部 MABS** ] 以返回本機 MABS 視圖。

## <a name="back-up-the-mabs-database-to-mabs-storage-pool"></a>將 MABS 資料庫備份至 MABS 存放集區

> [!NOTE]  
> 此選項適用于 Modern Backup Storage 的 MABS。

1. 在 MABS 主控台中，選取 [**保護**  >  **建立保護群組**]。
2. 在 [選擇保護群組類型]  頁面上，選取 [伺服器]  。
3. 在 [ **選擇群組成員** ] 頁面上，選取 [ **DPM 資料庫**]。 展開 MABS 伺服器，然後選取 [DPMDB]。
4. 在 [選擇資料保護方式]  頁面上，選取 [我想要使用磁碟短期保護]  。 指定短期保護原則選項。
5. 在 MABS 資料庫的初始複寫之後，請執行下列 SQL 腳本：

```SQL
select AG.NetbiosName, DS.DatasourceName, V.AccessPath, LR.PhysicalReplicaId from tbl_IM_DataSource DS
join tbl_PRM_LogicalReplica as LR
on DS.DataSourceId = LR.DataSourceId
join tbl_AM_Server as AG
on DS.ServerId=AG.ServerId
join tbl_PRM_ReplicaVolume RV
on RV.ReplicaId = LR.PhysicalReplicaId
join tbl_STM_Volume V
on RV.StorageId = V.StorageId
where datasourcename like N'%dpmdb%' and ds.ProtectedGroupId is not null
and LR.Validity in (1,2)
and AG.ServerName like N'%<dpmsqlservername>%' -- <dpmsqlservername> is a placeholder, put netbios name of server hosting DPMDB
```

### <a name="recover-mabs-database"></a>復原 MABS 資料庫

若要使用相同 DB 來重建您的 MABS，您必須先復原 MABS 資料庫，並將它與全新安裝的 MABS 同步處理。

#### <a name="use-the-following-steps"></a>使用下列步驟

1. 開啟系統管理命令提示字元並執行 `psexec.exe -s powershell.exe` ，以在系統內容中啟動 PowerShell 視窗。
2. 依您要復原的資料庫決定位置：

#### <a name="to-copy-the-database-from-the-last-backup"></a>若要從最後一次備份中複製資料庫

1. 瀏覽至複本 VHD 路徑 `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PhysicalReplicaId\>`
2. 使用命令掛接 disk0.vhdx 中存在的 **.vhdx。** `mount-vhd disk0.vhdx`
3. 裝載複本 VHD 之後，請使用將 `mountvol.exe` 磁碟機號指派給使用 SQL 腳本輸出中的實體複本識別碼的複本磁片區。 例如：`mountvol X: \?\Volume{}\`

#### <a name="to-copy-the-database-from-a-previous-recovery-point"></a>若要從先前的復原點複製資料庫

1. 流覽至 DPMDB 容器目錄  `\<MABSServer FQDN\>\<PhysicalReplicaId\>` 。 您將會在其下看到多個具有唯一 GUID 識別碼的目錄，對應 MABS DB 所取得的復原點。 其他目錄代表 PIT/復原點。
2. 流覽至任何 PIT vhd 路徑，例如， `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PITId\>` 使用命令掛接**disk0.vhdx。** `mount-vhd disk0.vhdx`
3. 裝載複本 VHD 之後，請使用 `mountvol.exe` 來指派磁碟機號給複本磁片區，並使用來自 SQL 腳本輸出的實體複本識別碼。 例如：`mountvol X: \?\Volume{}\`

   在上述步驟中，以角括弧顯示的所有詞彙都是預留位置。 將其取代為適當的值，如下所示：
   - **>refsvolume** -從 SQL 腳本輸出存取路徑
   - **MABSSERVER FQDN** -MABS 伺服器的完整限定名稱
   - **>physicalreplicaid>** -SQL 腳本輸出中的實體複本識別碼
   - **>pitid>** -容器目錄中的實體複本識別碼以外的 GUID 識別碼。
4. 開啟另一個系統管理命令提示字元並執行 `psexec.exe -s cmd.exe` ，以在系統內容中啟動命令提示字元。
5. 將目錄變更為 X：磁片磁碟機，然後流覽至 MABS 資料庫檔案的位置。
6. 請將它們複製到可輕鬆執行還原的位置。 複製之後，結束 psexec cmd 視窗。
7. 移至在步驟1中開啟的 psexec PowerShell 視窗，流覽至 VHDX 路徑，然後使用命令卸載 VHDX `dismount-vhd disk0.vhdx` 。
8. 重新安裝 MABS 伺服器之後，您可以藉由執行命令，使用還原的 DPMDB 附加至 MABS 伺服器 `DPMSYNC-RESTOREDB` 。
9. 執行 `DPMSYNC-SYNC` 一次 `DPMSYNC-RESTOREDB` 完成。

### <a name="back-up-the-database-by-backing-up-the-mabs-storage-pool"></a>藉由備份 MABS 存放集區來備份資料庫

> [!NOTE]
> 此選項適用于具有舊版儲存的 MABS。

開始之前，您必須執行腳本來取出 MABS 複本磁片區掛接點路徑，讓您知道哪一個復原點包含 MABS 備份。 請在 Azure 備份完成初始複寫後執行這項操作。 在腳本中， `dplsqlservername%` 以裝載 MABS 資料庫的 SQL Server 實例名稱取代。

```SQL
Select ag.NetbiosName as ServerName,ds.DataSourceName,vol.MountPointPath
from tbl_IM_DataSource as ds
join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
and vol.Usage =1
and lr.Validity in (1,2)
where ds.datasourcename like '%dpmdb%'
and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB
```

1. 在 MABS 主控台中，選取 [**保護**  >  **建立保護群組**]。

2. 在 [選擇保護群組類型]  頁面上，選取 [伺服器]  。

3. 在 [ **選擇群組成員** ] 頁面上，選取 MABS 資料庫。 展開 [MABS 伺服器] 專案，然後選取 [ **DPMDB**]。

4. 在 [  **選擇資料保護方式** ] 頁面上，選取 [ **我想要使用磁片短期保護**]。 指定短期保護原則選項。 建議 MABS 資料庫的保留範圍為兩周。

#### <a name="recover-the-database"></a>復原資料庫

如果 MABS 伺服器仍可運作且存放集區保持不變 (例如 MABS 服務或主控台) 的問題，請從複本磁片區或陰影複製複製資料庫，如下所示：

1. 決定您要復原資料庫的時間。

    - 如果您想要從直接取自 MABS 複本磁片區的上次備份中複製資料庫，請使用 **mountvol.exe** 將磁碟機號指派給使用 SQL 腳本輸出中 GUID 的複本磁片區。 例如：`C:\Mountvol X: \\?\Volume{d7a4fd76\-a0a8\-11e2\-8fd3\-001c23cb7375}\`

    - 如果您想要從先前的復原點複製資料庫 (陰影複製) ，您必須使用 SQL 腳本輸出中的磁片區 GUID，列出複本的所有陰影複製。 此命令會列出該磁片區的陰影複製： `C:\>Vssadmin list shadows /for\=\\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\` 。 請記下您想要復原的建立時間和陰影複製識別碼。

2. 然後使用 **diskshadow.exe** 將陰影複製掛接到未使用的磁碟機號 X：使用陰影複製識別碼，以便您可以複製資料庫檔案。

3. 開啟系統管理命令提示字元並執行， `psexec.exe -s cmd.exe` 以在系統內容中啟動命令提示字元，因此您有權流覽至複本磁片區 (X： ) 並複製檔案。

4. 將 CD 移至 X：磁片磁碟機，然後流覽至 MABS 資料庫檔案的位置。 請將它們複製到可輕鬆執行還原的位置。 複製完成之後，會有 psexec cmd 視窗，然後執行 **diskshadow.exe** 並 diskshadow.exe 取消公開 X： volume。

5. 現在您可以使用 SQL Management Studio 或執行 **DPMSYNC \- RESTOREDB**來還原資料庫檔案。

## <a name="back-up-with-native-sql-server-backup-to-a-local-disk"></a>利用原生 SQL Server 備份來備份到本機磁碟

您可以使用原生 SQL Server 備份，將 MABS 資料庫備份至本機磁片，而不需要 MABS。

- 取得 SQL Server 備份的[概觀](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)。

- [深入了解](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)如何將 SQL Server 備份至雲端。

## <a name="back-up-to-a-share-protected-by-mabs"></a>備份至受 MABS 保護的共用

此備份選項使用原生 SQL 將 MABS 資料庫備份至共用、使用 MABS 保護共用，以及使用舊版 Windows VSS 來加速還原。

### <a name="before-you-start"></a>在您開始使用 Intune 之前

1. 在 SQL Server 上，讓磁片磁碟機上的資料夾具有足夠的可用空間，以容納單一備份複本。 例如： `C:\MABSBACKUP` 。

1. 共用此資料夾。 例如，將 `C:\MABSBACKUP` 資料夾共用為 *DPMBACKUP*。

1. 將下列 OSQL 命令複製並貼到 [記事本]，並將它儲存到名為的檔案中 `C:\MABSACKUP\bkupdb.cmd` 。 請確定沒有副檔名 .txt。 修改 SQL_Instance_name 並 DPMDB_NAME，使其符合 MABS 伺服器所使用的實例和 DPMDB 名稱。

    ```SQL
    OSQL -E -S localhost\SQL_INSTANCE_NAME -Q "BACKUP DATABASE DPMDB_NAME TO DISK='C:\DPMBACKUP\dpmdb.bak' WITH FORMAT"
    ```

1. 使用 [記事本] 開啟位於 MABS 伺服器之資料夾下的 **ScriptingConfig.xml** 檔案 `C:\Program Files\Microsoft System Center\DPM\DPM\Scripting` 。

1. 修改 **ScriptingConfig.xml** ，並 **將 [DPMDBBACKUP] 變更為** 包含 [] 資料夾/共用的磁碟機號。 將 PreBackupScript 專案變更為在步驟3中儲存之 **bkupdb** 的完整路徑和名稱。

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ScriptConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="https://schemas.microsoft.com/2003/dls/ScriptingConfig.xsd">
    <DatasourceScriptConfig DataSourceName="C:">
    <PreBackupScript>C:\MABSDBBACKUP\bkupdb.cmd</PreBackupScript>
    <TimeOut>120</TimeOut>
    </DatasourceScriptConfig>
    </ScriptConfiguration>
    ```

1. 將變更儲存至 **ScriptingConfig.xml**。

1. 使用 MABS 保護 C:\MABSBACKUP 資料夾或 `\sqlservername\MABSBACKUP` 共用，並等候建立初始複本。 C:\MABSBACKUP 資料夾中應該會有 **dpmdb** ，因為備份前腳本正在執行，然後再複製到 MABS 複本。

1. 如果您未啟用自助式復原，則需要一些額外的步驟來共用複本上的 MABSBACKUP 資料夾：

    1. 在 MABS 主控台 > **保護**] 中，找出 MABSBACKUP 資料來源並加以選取。 在 [詳細資料] 區段中，選取 [ **按一下] 以查看** 複本路徑連結的詳細資料，並將路徑複製到 [記事本]。 移除來源路徑並保留目的地路徑。 路徑看起來應該像下面這樣： `C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP` 。

    2. 使用共用名稱 **MABSSERVERNAME-DPMDB**來建立該路徑的共用。 您可以在系統管理命令提示字元中使用以下 Net Share 命令。

        ```cmd
        Net Share MABSSERVERNAME-dpmdb="C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

### <a name="configure-the-backup"></a>設定備份

您可以使用 SQL Server 原生備份來備份 MABS 資料庫，就像使用任何其他 SQL Server 資料庫一樣。

- 取得 SQL Server 備份的[概觀](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)。

- [深入了解](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)如何將 SQL Server 備份至雲端。

### <a name="recover-the-mabs-database"></a>復原 MABS 資料庫

1. `\\MABSServer\MABSSERVERNAME-dpmdb`從任何 Windows 電腦使用 Explorer 連接到共用。

2. 以滑鼠右鍵按一下 **dpmdb .bak** 檔案以查看屬性。 [舊版]  索引標籤會顯示您可以選取和複製的所有備份。 此外，最後一個仍位於 C:\MABSBACKUP 資料夾中的備份也可輕易存取。

3. 如果您需要將 SAN 連接的 MABS 存放集區磁片移到另一部伺服器，以便從複本磁片區讀取，或要重新安裝 Windows 以讀取本機連接的磁片，您必須事先知道 MABS 複本磁片區掛接點路徑或磁片區 GUID，才能知道哪個磁片區保留資料庫備份。 在初始的保護之後，但需要還原之前，您可以使用下列 SQL 指令碼隨時擷取該資訊。 將取代為 `%dpmsqlservername%` 裝載資料庫的 SQL Server 名稱。

    ```sql
    Select ag.NetbiosName as
    ServerName,ds.DataSourceName,vol.MountPointPath,vol.GuidName
    from tbl_IM_DataSource as ds
    join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
    join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
    join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
    and vol.Usage =1
    and lr.Validity in (1,2)
    where ds.datasourcename like '%C:\%' -- volume drive letter for DPMBACKUP
    and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB

    ```

4. 如果您需要在移動 MABS 存放集區磁片或 MABS 伺服器重建之後復原：

    1. 您有磁片區 GUID，所以該磁片區應掛接在另一部 Windows server 上或在 MABS 伺服器重建之後，請使用 **mountvol.exe** 指派使用 SQL 腳本輸出中磁片區 GUID 的磁碟機號： `C:\Mountvol X: \\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\` 。

    2. 使用磁碟機號以及代表資料夾結構的複本路徑部分，再次共用複本磁片區上的 MABSBACKUP 資料夾。

        ```cmd
        net share SERVERNAME-DPMDB="X:\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

    3. `\\SERVERNAME\MABSSERVERNAME-dpmdb`從任何 Windows 電腦使用 Explorer 連接到共用。

    4. 以滑鼠右鍵按一下 **dpmdb .bak** 檔案以查看屬性。 [舊版]  索引標籤會顯示您可以選取和複製的所有備份。

## <a name="using-dpmsync"></a>使用 DPMSync

**DpmSync** 是一種命令列工具，可讓您將 MABS 資料庫與存放集區中的磁片狀態以及已安裝的保護代理程式進行同步處理。 DpmSync 會還原 MABS 資料庫、同步處理 MABS 資料庫與存放集區中的複本、還原報表資料庫，以及重新配置遺失的複本。

### <a name="parameters"></a>參數

| 參數      | 說明    |
|----------------|-----------------------------|
| **-RestoreDb**                       | 從指定的位置還原 MABS 資料庫。|
| **-Sync**                            | 同步處理已還原的資料庫。 還原資料庫之後，您必須執行 **DpmSync – Sync** 。 執行 **DpmSync – Sync**之後，某些複本可能仍會標示為遺失。 |
| **-DbLoc** *位置*                | 識別 MABS 資料庫備份的位置。|
| **-InstanceName** <br/>*伺服器 \ 實例*     | DPMDB 必須還原的執行個體。|
| **-ReallocateReplica**         | 重新配置所有遺失的複本磁碟區，不進行同步處理。 |
| **-DataCopied**                      | 指出您已經完成將資料載入到剛配置的複本磁碟區中。 這只適用於用戶端電腦。 |

**範例1：** 若要從 MABS 伺服器上的本機備份媒體還原 MABS 資料庫，請執行下列命令：

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak
```

在您還原 MABS 資料庫之後，若要同步處理資料庫，請執行下列命令：

```cmd
DpmSync -Sync
```

在您還原和同步處理 MABS 資料庫之後，以及在還原複本之前，請執行下列命令來重新配置複本的磁碟空間：

```cmd
DpmSync -ReallocateReplica
```

**範例2：** 若要從遠端資料庫還原 MABS 資料庫，請在遠端電腦上執行下列命令：

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak –InstanceName contoso\ms$dpm
```

在您還原 MABS 資料庫之後，若要同步處理資料庫，請在 MABS 伺服器上執行下列命令：

```cmd
DpmSync -Sync
```

在您還原和同步處理 MABS 資料庫之後，以及在還原複本之前，請在 MABS 伺服器上執行下列命令，以重新配置複本的磁碟空間：

```cmd
DpmSync -ReallocateReplica
```

## <a name="next-steps"></a>後續步驟

- [MABS 支援矩陣](backup-support-matrix-mabs-dpm.md)
- [MABS 常見問題](backup-azure-dpm-azure-server-faq.md)