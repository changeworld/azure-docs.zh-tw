---
title: 從 Azure VM 備份復原檔案和資料夾
description: 在此文章中，您將了解如何從 Azure 虛擬機器復原點復原檔案和資料夾。
ms.topic: conceptual
ms.date: 03/01/2019
ms.custom: references_regions
ms.openlocfilehash: 7b9d97e518282cf150a8f54225c11d9edcbf8892
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88892570"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>從 Azure 虛擬機器備份復原檔案

Azure 備份可從 Azure 虛擬機器 (VM) 備份 (又稱復原點) 還原 [Azure VM 和磁碟](./backup-azure-arm-restore-vms.md)。 本文說明如何從 Azure VM 備份來復原檔案和資料夾。 只有使用 Resource Manager 模型部署且受復原服務保存庫保護的 Azure Vm，才可還原檔案和資料夾。

> [!NOTE]
> 這項功能適用於使用 Resource Manager 模型部署且受復原服務保存庫保護的 Azure VM。
> 不支援從加密的 VM 備份進行檔案復原。
>

## <a name="mount-the-volume-and-copy-files"></a>掛接磁碟區和複製檔案

若要從復原點還原檔案或資料夾，請移至虛擬機器，然後選擇所需的復原點。

1. 登入 [Azure 入口網站](https://portal.Azure.com) ，然後在左窗格中選取 [ **虛擬機器**]。 從虛擬機器的清單中，選取虛擬機器以開啟該虛擬機器的儀表板。

2. 在虛擬機器的功能表中，選取 [ **備份** ] 以開啟 [備份] 儀表板。

    ![開啟復原服務保存庫備份項目](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. 在 [備份儀表板] 功能表中 **，選取 [** 檔案復原]。

    ![選取檔案復原](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    [檔案復原] 功能表隨即開啟。

    ![[檔案復原] 功能表](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. 從 [選取復原點] 下拉式選單中，選取包含您所需檔案的復原點。 根據預設，已選取最近的復原點。

5. 若要下載用來從復原點複製檔案的軟體，請選取 [下載 Windows Azure Vm 的 **可執行** 檔 (]) 或下載 Linux azure Vm 的 **腳本** (，) 產生 python 腳本。

    ![下載可執行檔](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Azure 會將可執行檔或指令碼下載到本機電腦。

    ![可執行檔或指令碼的下載訊息](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    若要以系統管理員的身分執行可執行檔或指令碼，建議先將下載的檔案儲存到電腦上。

6. 可執行檔或指令碼受到密碼保護，因此需輸入密碼。 在 [檔案復原 **] 功能表中** ，選取 [複製] 按鈕，將密碼載入至記憶體。

    ![產生的密碼](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. 請確定[您擁有適當的電腦](#selecting-the-right-machine-to-run-the-script)可以執行指令碼。 如果適當的電腦就是您下載指令碼所在的相同電腦，則您可以繼續進行下載一節。 從下載位置 (通常是 [下載] 資料夾)，在可執行檔或指令碼上按一下滑鼠右鍵，並以系統管理員認證執行。 出現提示時，請輸入密碼或從記憶體中貼上密碼，然後按 **Enter** 鍵。 輸入有效的密碼後，指令碼會連線至復原點。

    ![可執行檔輸出](./media/backup-azure-restore-files-from-vm/executable-output.png)

8. 針對 Linux 電腦，則會產生 Python 指令碼。 您需要下載指令碼，並將其複製到相關/相容的 Linux 伺服器。 您可能必須修改權限，才能以 ```chmod +x <python file name>``` 執行。 接著，使用 ```./<python file name>``` 執行 Python 檔案。

請參閱[存取需求](#access-requirements)一節，以確定指令碼是否已成功執行。

### <a name="identifying-volumes"></a>識別磁碟區

#### <a name="for-windows"></a>若為 Windows

當您執行可執行檔時，作業系統會掛接新磁碟區並指派磁碟機代號。 您可以使用 Windows 檔案總管或檔案總管來瀏覽這些磁碟機。 指派給磁碟區的磁碟機代號可能與原始虛擬機器為不同的代號。 不過，系統會保留磁碟區名稱。 例如，如果原始虛擬機器上的磁碟區是 "Data Disk (E:`\`)"，則可以在本機電腦上將該磁碟區連結為 "Data Disk ('任何磁碟機代號':`\`)"。 瀏覽指令碼輸出中所提及的所有磁碟區，直到找出您的檔案或資料夾為止。  

   ![已附加的修復磁片區](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-linux"></a>若為 Linux

在 Linux 中，復原點的磁碟區會掛接到執行指令碼的資料夾。 連結的磁碟、磁碟區和對應的掛接路徑會相應顯示。 具有根層級存取權的使用者都看得到這些掛接路徑。 在指令碼輸出中瀏覽所述的磁碟區。

  ![Linux [檔案復原] 功能表](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)

## <a name="closing-the-connection"></a>關閉連線

在識別檔案並將它們複製到本機儲存體位置之後，移除 (或卸載) 其他磁碟機。 若要卸載磁片磁碟機，請在 Azure 入口網站的 [檔案復原 **] 功能表上，選取 [** **卸載磁片**]。

![取消掛接磁碟](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

一旦卸載磁碟後，您會收到一則訊息。 連線可能需要幾分鐘重新整理，以讓您可以移除磁碟。

在 Linux 中，復原點的連線嚴重損毀之後，作業系統並不會自動移除對應的掛接路徑。 這些掛接路徑會以「孤立」磁碟區的形式存在。雖然您看得見，但是存取/寫入檔案時會擲回錯誤。 可以手動移除它們。 指令碼執行時，會從任何先前的復原點識別任何這類現有的磁碟區，並在同意下將它們清除。

> [!NOTE]
> 在還原必要的檔案之後，請確定連接已關閉。 這一點很重要，尤其是在執行腳本的電腦也設定為備份的情況下。 如果連接仍處於開啟狀態，後續備份可能會失敗，並出現錯誤「UserErrorUnableToOpenMount」。 發生這種情況是因為已載入的磁片磁碟機/磁片區會被假設為可用，而且存取它們時可能會失敗，因為基礎儲存體（也就是，iSCSI 目標伺服器可能無法使用）。 清除連接將會移除這些磁片磁碟機/磁片區，因此在備份期間將無法使用它們。

## <a name="selecting-the-right-machine-to-run-the-script"></a>選取適當的電腦以執行指令碼

如果成功下載指令碼，則下一個步驟是確認您打算執行指令碼所在的電腦是否為適當的電腦。 以下是要在電腦上滿足的需求。

### <a name="original-backed-up-machine-versus-another-machine"></a>原始備份的電腦與另一部電腦

1. 如果備份的電腦是大型磁碟 VM (也就是磁碟數目大於 16 個磁碟，或每個磁碟大於 4 TB)，則指令碼  **必須在另一部電腦上執行**且必須符合[這些需求](#file-recovery-from-virtual-machine-backups-having-large-disks)。
1. 即使備份的電腦不是大型磁碟 VM，在[這些案例](#special-configurations)中，指令碼無法在相同的備份 VM 上執行。

### <a name="os-requirements-on-the-machine"></a>電腦的作業系統需求

需要執行指令碼的電腦必須符合[這些作業系統需求](#system-requirements)。

### <a name="access-requirements-for-the-machine"></a>電腦的存取權需求

需要執行指令碼的電腦必須符合[這些存取權需求](#access-requirements)。

## <a name="special-configurations"></a>特殊的組態

### <a name="dynamic-disks"></a>動態磁碟

如果受保護 Azure VM 具有下列之一或兩者特性的磁碟區，則您無法在同一部 VM 上執行可執行的指令碼。

- 跨多個磁碟的磁碟區 (合併或等量磁碟區)
- 動態磁碟上的容錯磁碟區 (鏡像與 RAID-5 磁碟區)

請改為在其他具有相容作業系統的電腦上執行可執行的指令碼。

### <a name="windows-storage-spaces"></a>Windows 儲存空間

Windows 儲存空間是一種可將儲存體虛擬化的 Windows 技術。 您可以透過 Windows 儲存空間，集合一組符合業界標準的磁碟成為儲存體集區。 然後使用儲存體集區中的可用空間來建立虛擬磁碟 (稱為儲存空間)。

如果受保護 Azure VM 使用 Windows 儲存空間，則無法在同一部虛擬機器上執行可執行的指令碼。 相反地，在任何其他具有相容作業系統上的電腦執行可執行的指令碼。

### <a name="lvmraid-arrays"></a>LVM/RAID 陣列

在 Linux 中，邏輯磁碟區管理員 (LVM) 和/或軟體 RAID 陣列會用來管理多個磁碟的邏輯磁碟區。 如果受保護 Linux VM 使用 LVM 和/或 RAID 陣列，則無法在同一部虛擬機器上執行指令碼。 請改為在其他具有相容作業系統且支援受保護 VM 之檔案系統的電腦上執行指令碼。

下列指令碼輸出會顯示 LVM 和/或 RAID 陣列磁碟與磁碟區及其磁碟分割類型。

   ![Linux LVM 輸出功能表](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)

若要讓這些磁碟分割上線，請執行以下小節中的命令。

#### <a name="for-lvm-partitions"></a>若為 LVM 磁碟分割

執行腳本之後，LVM 分割區會裝載于實體磁片區 (s) /disk (s) 在腳本輸出中指定。 流程是

1. 從實體磁片區或磁片取得磁片區組名的唯一清單
2. 然後列出這些磁片區群組中的邏輯磁片區
3. 然後將邏輯磁片區掛接至所需的路徑。

##### <a name="listing-volume-group-names-from-physical-volumes"></a>列出實體磁片區的磁片區組名

列出磁片區組名：

```bash
pvs -o +vguuid
```

此命令會列出所有實體磁片區 (包括執行腳本) 之前的磁片區、對應的磁片區組名，以及磁片區群組的唯一使用者識別碼 (Uuid) 。 命令的範例輸出如下所示。

```bash
PV         VG        Fmt  Attr PSize   PFree    VG UUID

  /dev/sda4  rootvg    lvm2 a--  138.71g  113.71g EtBn0y-RlXA-pK8g-de2S-mq9K-9syx-B29OL6

  /dev/sdc   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sde   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sdf   datavg_db lvm2 a--   <1.50t <396.50g dhWL1i-lcZS-KPLI-o7qP-AN2n-y2f8-A1fWqN

  /dev/sdd   datavg_db lvm2 a--   <1.50t <396.50g dhWL1i-lcZS-KPLI-o7qP-AN2n-y2f8-A1fWqN
```

第一個資料行 (PV) 顯示實體磁片區，後續的資料行則顯示相關的磁片區組名、格式、屬性、大小、可用空間，以及磁片區群組的唯一識別碼。 命令輸出會顯示所有實體磁片區。 請參閱腳本輸出，並找出與備份相關的磁片區。 在上述範例中，腳本輸出會顯示/dev/sdf 和/dev/sdd。 因此， *datavg_db* 磁片區群組屬於腳本，而 *Appvg_new* 磁片區群組則屬於該機器。 最後的概念是確保唯一的磁片區組名應該有一個唯一識別碼。

###### <a name="duplicate-volume-groups"></a>重複的磁片區群組

在某些情況下，磁片區組名在執行腳本之後可以有2個 Uuid。 這表示執行腳本的電腦和備份的 VM 中的磁片區組名相同。 接著，我們必須重新命名備份的 Vm 磁片區群組。 請參閱下列範例。

```bash
PV         VG        Fmt  Attr PSize   PFree    VG UUID

  /dev/sda4  rootvg    lvm2 a--  138.71g  113.71g EtBn0y-RlXA-pK8g-de2S-mq9K-9syx-B29OL6

  /dev/sdc   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sde   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sdg   APPvg_new lvm2 a--  <75.00g  508.00m lCAisz-wTeJ-eqdj-S4HY-108f-b8Xh-607IuC

  /dev/sdh   APPvg_new lvm2 a--  <75.00g  508.00m lCAisz-wTeJ-eqdj-S4HY-108f-b8Xh-607IuC

  /dev/sdm2  rootvg    lvm2 a--  194.57g  127.57g efohjX-KUGB-ETaH-4JKB-MieG-EGOc-XcfLCt
```

腳本輸出會顯示已附加的/dev/sdg、/dev/sdh、/dev/sdm2。 因此，對應的 VG 名稱是 Appvg_new 和 rootvg。 但相同的名稱也會出現在電腦的 VG 清單中。 我們可以確認一個 VG 名稱有兩個 Uuid。

現在，我們需要為以腳本為基礎的磁片區重新命名 VG 名稱，例如：/dev/sdg、/dev/sdh、/dev/sdm2。 若要重新命名磁片區群組，請使用下列命令

```bash
vgimportclone -n rootvg_new /dev/sdm2
vgimportclone -n APPVg_2 /dev/sdg /dev/sdh
```

現在我們已有唯一識別碼的 VG 名稱。

###### <a name="active-volume-groups"></a>作用中磁片區群組

請確定對應到腳本磁片區的磁片區群組是作用中的。 下列命令可用來顯示作用中的磁片區群組。 檢查腳本的相關磁片區群組是否出現在這份清單中。

```bash
vgdisplay -a
```  

否則，請使用下列命令來啟用磁片區群組。

```bash
#!/bin/bash
vgchange –a y  <volume-group-name>
```

##### <a name="listing-logical-volumes-within-volume-groups"></a>列出磁片區群組內的邏輯磁片區

一旦取得與腳本相關的唯一、有效的 VGs 清單，就可以使用下列命令來列出這些磁片區群組中的邏輯磁片區。

```bash
#!/bin/bash
lvdisplay <volume-group-name>
```

此命令會將每個邏輯磁片區的路徑顯示為「LV 路徑」。

##### <a name="mounting-logical-volumes"></a>裝載邏輯磁片區

若要將邏輯磁碟區掛接至您所選擇的路徑：

```bash
#!/bin/bash
mount <LV path from the lvdisplay cmd results> </mountpath>
```

> [!WARNING]
> 請勿使用 ' mount-a '。 此命令會裝載 '/etc/fstab ' 中所述的所有裝置。 這可能表示可以裝載重複的裝置。 資料可以重新導向至腳本所建立的裝置，而不會保存資料，因此可能會導致資料遺失。

#### <a name="for-raid-arrays"></a>若為 RAID 陣列

下列命令會顯示所有 RAID 磁碟的詳細資料：

```bash
#!/bin/bash
mdadm –detail –scan
```

 相關的 RAID 磁碟將顯示為 `/dev/mdm/<RAID array name in the protected VM>`

如果 RAID 磁碟具有實體磁碟區，請使用掛接命令：

```bash
#!/bin/bash
mount [RAID Disk Path] [/mountpath]
```

如果 RAID 磁碟上有設定其他 LVM，則請使用上述適用於 LVM 磁碟分割的程序，但使用磁碟區名稱替代 RAID 磁碟名稱。

## <a name="system-requirements"></a>系統需求

### <a name="for-windows-os"></a>若為 Windows OS

下表顯示伺服器和電腦作業系統之間的相容性。 復原檔案時，無法將檔案還原至之前或之後的作業系統版本。 例如，您無法將 Windows Server 2016 虛擬機器的檔案還原至 Windows Server 2012 或 Windows 8 電腦。 您可以將 VM 的檔案還原至相同的伺服器作業系統，或相容的用戶端作業系統。

|伺服器作業系統 | 相容的用戶端作業系統  |
| --------------- | ---- |
| Windows Server 2019    | Windows 10 |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

### <a name="for-linux-os"></a>若為 Linux 作業系統

在 Linux 中，用來還原檔案的電腦作業系統必須支援受保護虛擬機器的檔案系統。 選取要執行指令碼的電腦時，請確認該電腦具有相容的作業系統，且使用下表中列出的其中一個版本：

|Linux 作業系統 | 版本  |
| --------------- | ---- |
| Ubuntu | 12.04 和更新版本 |
| CentOS | 6.5 和更新版本  |
| RHEL | 6.7 和更新版本 |
| Debian | 7 和更新版本 |
| Oracle Linux | 6.4 和更新版本 |
| SLES | 12 以上 (含) |
| openSUSE | 42.2 和更新版本 |

> [!NOTE]
> 我們發現在具有 SLES 12 SP4 作業系統的電腦上執行檔案復原腳本時發生一些問題，我們正在調查 SLES 團隊。
> 目前，執行檔案復原指令碼的作業是在作業系統版本為 SLES 12 SP2 和 SP3 的電腦上運作。
>

指令碼也需要 Python 和 Bash 元件，才能夠執行並安全地連線至復原點。

|元件 | 版本  |
| --------------- | ---- |
| Bash | 4 和更新版本 |
| Python | 2.6.6 和更新版本  |
| TLS | 應支援 1.2  |

## <a name="access-requirements"></a>存取權需求

如果您在具有限制存取的電腦上執行腳本，請確定有下列存取權：

- `download.microsoft.com`
- 復原服務 Url (地理名稱是指復原服務保存庫所在的區域) 
  - `https://pod01-rec2.geo-name.backup.windowsazure.com` 適用于 Azure 公用區域的 () 
  - `https://pod01-rec2.geo-name.backup.windowsazure.cn` (適用於 Azure China 21Vianet)
  - `https://pod01-rec2.geo-name.backup.windowsazure.us` (適用於 Azure US Government)
  - `https://pod01-rec2.geo-name.backup.windowsazure.de` (適用於 Azure 德國)
- 輸出連接埠 53 (DNS)、443、3260

> [!NOTE]
>
> - 下載的指令碼檔案名稱將會在 URL 中填入 **geo-name**。 例如：下載的腳本名稱開頭為 \' VMname \' \_ \' geoname \' _ \' GUID \' ，例如*ContosoVM_wcus_12345678*
> - URL 會是 <https://pod01-rec2.wcus.backup.windowsazure.com>」
>

若為 Linux，指令碼需要 'open-iscsi' 和 'lshw' 元件來連接到復原點。 如果元件不存在於執行指令碼所在的電腦上，則指令碼會要求安裝元件的權限。 同意安裝必要的元件。

需有 `download.microsoft.com` 的存取權才能下載元件，並使用這些元件在執行指令碼所在的電腦及復原點中的資料之間建立安全通道。

## <a name="file-recovery-from-virtual-machine-backups-having-large-disks"></a>從具有大型磁碟的虛擬機器備份進行檔案復原

本節說明如何從有超過16個磁片的 Azure 虛擬機器備份執行檔復原，或每個磁片大小大於 4 TB。

由於檔案復原程式會將備份中的所有磁片附加到磁片上，因此當大量磁片 ( # B0 16) 或大型磁片 ( # A1 4) TB 時，建議使用下列動作點：

- 保留一部還原伺服器 (Azure VM D2v3 VM) 以進行檔案復原。 您僅能針對檔案復原使用該伺服器，然後在不需要時將其關機。 不建議在原始電腦上還原，因為這對 VM 本身會有顯著的影響。
- 接著，執行指令碼一次，以檢查檔案復原作業是否成功。
- 如果檔案復原程序停止回應 (磁碟無法掛接，或已掛接但磁碟區未出現)，請執行下列步驟。
  - 如果還原伺服器是 Windows VM：
    - 請確定作業系統是 WS 2012 或更新版本。
    - 請確定已在還原伺服器中將登錄機碼設定為以下建議的設定，並務必將伺服器重新開機。 GUID 旁的編號範圍可以從 0001-0005。 在下列範例中是 0004。 瀏覽登錄機碼路徑，直到 [參數] 區段為止。

    ![登錄機碼變更](media/backup-azure-restore-files-from-vm/iscsi-reg-key-changes.png)

```registry
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Disk\TimeOutValue – change this from 60 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\SrbTimeoutDelta – change this from 15 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\EnableNOPOut – change this from 0 to 1
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\MaxRequestHoldTime - change this from 60 to 1200
```

- 如果還原伺服器是 Linux VM：
  - 在檔案 /etc/iscsi/iscsid.conf 中，將設定從：
    - `node.conn[0].timeo.noop_out_timeout = 5`  自 `node.conn[0].timeo.noop_out_timeout = 30`
- 進行上述變更之後，再次執行指令碼。 進行這些變更之後，檔案復原很可能會成功。
- 每次使用者下載指令碼時，Azure 備份會起始準備復原點以進行下載的程序。 若使用大型磁碟，此程序將需要相當長的時間。 如果有連續要求暴增，則目標準備將進入下載螺旋。 因此，建議您從入口網站/PowerShell/CLI 下載指令碼、等待 20-30 分鐘 (啟發學習法)，然後執行該指令碼。 此時，目標應該已準備好從指令碼連線。
- 在檔案復原之後，請務必返回入口網站，並針對無法裝載磁片區的復原點選取 [ **卸載磁片** ]。 基本上，此步驟將會清除任何現有的程序/工作階段，並增加復原的機會。

## <a name="troubleshooting"></a>疑難排解

如果您從虛擬機器復原檔案時遇到問題，請檢查下表中的其他資訊。

| 錯誤訊息 / 案例 | 可能的原因 | 建議的動作 |
| ------------------------ | -------------- | ------------------ |
| Exe 輸出：*連線到目標時攔截到例外狀況* | 指令碼無法存取復原點    | 檢查電腦是否符合[上述的存取權需求](#access-requirements)。 |  
| Exe 輸出：目標已透過 iSCSI 工作階段登入。 | 指令碼已在相同電腦上執行，並已附加磁碟機 | 復原點磁碟區已連接。 它們可能不會使用原始 VM 的相同磁碟機號來裝載。 在檔案總管中瀏覽所有可用的磁碟區，以尋找您的檔案。 |
| Exe 輸出：這個指令碼無效，因為磁碟已透過入口網站/超過 12 小時限制卸載。*從入口網站下載新的指令碼。* |    已從入口網站卸載磁碟或超過 12 小時的限制 | 此特定 exe 目前無效，因此無法執行。 如果您想要存取該復原時間點的檔案，請瀏覽新 exe 的入口網站。|
| 在執行 exe 的電腦上：按一下 [卸載] 按鈕之後不會卸載新的磁碟區 | 電腦上的 iSCSI 啟動器沒有回應/重新整理與目標的連線並維護快取。 |  按一下 [卸載] 後，請稍候幾分鐘。 如果新磁碟區並未卸載，請瀏覽所有磁碟區。 瀏覽所有磁碟區會強制啟動器重新整理連線，且會卸載磁碟區，並出現磁碟無法使用的錯誤訊息。|
| Exe 輸出：指令碼成功執行，但指令碼輸出上不會顯示「已連結新磁碟區」 |    這是暫時性的錯誤    | 磁碟區將已經連結。 開啟檔案總管以瀏覽。 如果您每次都使用同一部電腦執行指令碼，請考慮重新啟動電腦，清單應該會顯示在後續的 exe 執行中。 |
| Linux 特定︰無法檢視所需的磁碟區 | 執行指令碼所在電腦的作業系統可能無法辨識受保護 VM 的底層檔案系統 | 檢查復原點是絕對一致還是檔案一致。 如果是檔案一致，請在其作業系統可辨識受保護 VM 檔案系統的其他電腦上執行指令碼。 |
| Windows 特定︰無法檢視所需的磁碟區 | 可能已連結磁碟，但未設定磁碟區 | 從 [磁碟管理] 畫面上，找出與復原點相關的其他磁碟。 如果有任何磁片處於離線狀態，請在磁片上按一下滑鼠右鍵，然後選取 [ **連線**]，以嘗試將它們上線。|

## <a name="security"></a>安全性

本節討論從 Azure VM 備份實作檔案復原所採取的各種安全性措施。

### <a name="feature-flow"></a>功能流程

此功能是建立來存取 VM 資料，而不需要還原整個 VM 或 VM 磁碟，而且所需採取的步驟最少。 VM 資料的存取是由指令碼所提供 (可在執行時掛接復原磁碟區，如下所示)，而且會形成所有安全性實作的基石：

  ![安全性功能流程](./media/backup-azure-restore-files-from-vm/vm-security-feature-flow.png)

### <a name="security-implementations"></a>安全性實作

#### <a name="select-recovery-point-who-can-generate-script"></a>選取復原點 (可產生指令碼的人員)

指令碼會提供 VM 資料的存取權，因此請務必控管誰可以先產生指令碼。 您必須登入 Azure 入口網站，並獲得 [RBAC 授權](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions)，才能產生指令碼。

檔案復原需要與 VM 還原和磁碟還原所需的相同授權層級。 換句話說，只有獲授權的使用者才可以檢視能夠產生指令碼的 VM 資料。

產生的指令碼會使用 Azure 備份服務的官方 Microsoft 憑證進行簽署。 對指令碼進行的任何竄改都表示簽章已損毀，而且執行指令碼的任何嘗試都會被視為作業系統的潛在風險。

#### <a name="mount-recovery-volume-who-can-run-script"></a>掛接復原磁碟區 (可執行指令碼的人員)

只有系統管理員可以執行指令碼，而且應該在提高權限的模式下執行。 指令碼只會執行一組預先產生的步驟，而且不接受來自任何外部來源的輸入。

若要執行指令碼，只需要在 Azure 入口網站或 PowerShell/CLI 中產生指令碼時，才向獲授權的使用者顯示的密碼。 這是為了確保下載腳本的授權使用者也負責執行腳本。

#### <a name="browse-files-and-folders"></a>瀏覽檔案和資料夾

為瀏覽檔案和資料夾，指令碼會使用電腦中的 iSCSI 啟動器，並連線到設定為 iSCSI 目標的復原點。 在這裡，您可以想像有人嘗試模仿/欺騙任何或所有元件的案例。

我們會使用相互 CHAP 驗證機制，讓每個元件彼此驗證。 也就是說，假的啟動器很難連線到 iSCSI 目標，而且假的目標很難連線到執行指令碼所在的電腦。

復原服務與電腦之間的資料流程會藉由透過 TCP 建立安全的 TLS 通道而受到保護 (執行指令碼所在的電腦[應該支援 TLS 1.2](#system-requirements))。

任何檔案存取控制清單 (ACL) 存在於父/備份 VM 中，也會保留在裝載的檔案系統中。

此指令碼會對復原點提供唯讀存取權，且只在 12 小時內有效。 如果您想要移除先前的存取權，請登入 Azure 入口網站/PowerShell/CLI，並針對該特定復原點執行 **卸載磁片** 。 此指令碼將會立即失效。

## <a name="next-steps"></a>後續步驟

- 對於還原檔案時的任何疑問，請參閱[疑難排解](#troubleshooting)一節
- 了解如何[透過 PowerShell 還原檔案](./backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup)
- 了解如何[透過 Azure CLI 還原檔案](./tutorial-restore-files.md)
- 還原 VM 之後，了解如何[管理備份](./backup-azure-manage-vms.md)
