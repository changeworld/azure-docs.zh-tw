---
title: 從 Azure VM 備份復原檔案和資料夾
description: 在本文中，您將瞭解如何從 Azure 虛擬機器復原點復原檔案和資料夾。
ms.topic: conceptual
ms.date: 03/01/2019
ms.openlocfilehash: 0e3061ea8fc26adcf39fe415cd9a662de739543a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78363704"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>從 Azure 虛擬機器備份復原檔案

Azure 備份可從 Azure 虛擬機器 (VM) 備份 (又稱復原點) 還原 [Azure VM 和磁碟](./backup-azure-arm-restore-vms.md)。 本文說明如何從 Azure VM 備份來復原檔案和資料夾。 只有使用 Resource Manager 模型部署且受復原服務保存庫保護的 Azure VM，才能還原檔案和資料夾。

> [!NOTE]
> 這項功能適用於使用 Resource Manager 模型部署且受復原服務保存庫保護的 Azure VM。
> 不支援從加密 VM 備份的檔案復原。
>

## <a name="mount-the-volume-and-copy-files"></a>掛接磁碟區和複製檔案

若要從復原點還原檔案或資料夾，請移至虛擬機器，然後選擇所需的復原點。

1. 登入 [Azure 入口網站](https://portal.Azure.com)，按一下左窗格中的 [虛擬機器]。 從虛擬機器的清單中，選取虛擬機器以開啟該虛擬機器的儀表板。

2. 在虛擬機器的功能表中，按一下 [備份] 來開啟 [備份] 儀表板。

    ![開啟復原服務保存庫備份項目](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. 在 [備份] 儀表板功能表中，按一下 [檔案復原]。

    ![[檔案復原] 按鈕](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    [檔案復原] 功能表隨即開啟。

    ![[檔案復原] 功能表](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. 從 [選取復原點] 下拉式選單中，選取包含您所需檔案的復原點。 根據預設，已選取最近的復原點。

5. 若要下載用來從復原點複製檔案的軟體，請按一下 [**下載可執行檔**] （適用于 Windows azure vm）或 [**下載腳本**] （針對 Linux Azure vm，會產生 python 腳本）。

    ![產生的密碼](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Azure 會將可執行檔或指令碼下載到本機電腦。

    ![可執行檔或指令碼的下載訊息](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    若要以系統管理員身分執行可執行檔或腳本，建議您將下載的檔案儲存到您的電腦。

6. 可執行檔或指令碼受到密碼保護，因此需輸入密碼。 在 [檔案復原] 功能表上，按一下 [複製] 按鈕以將密碼載入記憶體中。

    ![產生的密碼](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. 於下載位置中 (通常是 [下載] 資料夾)，在可執行檔或指令碼上按一下滑鼠右鍵，並以系統管理員認證來執行。 出現提示時，輸入密碼或將密碼貼到記憶體中，然後按**enter**。 輸入有效的密碼後，指令碼會連線至復原點。

    ![[檔案復原] 功能表](./media/backup-azure-restore-files-from-vm/executable-output.png)

8. 針對 Linux 機器，會產生 python 腳本。 其中一個需要下載腳本，並將它複製到相關/相容的 Linux 伺服器。 您可能必須修改許可權以 ```chmod +x <python file name>```執行。 然後，使用 ```./<python file name>```執行 python 檔案。

請參閱[存取需求](#access-requirements)一節，以確定腳本已順利執行。

### <a name="identifying-volumes"></a>識別磁片區

#### <a name="for-windows"></a>若為 Windows

當您執行可執行檔時，作業系統會掛接新磁碟區並指派磁碟機代號。 您可以使用 Windows 檔案總管或檔案總管來瀏覽這些磁碟機。 指派給磁片區的磁碟機號可能與原始虛擬機器的字母不同。 不過，會保留磁片區名稱。 例如，如果原始虛擬機器上的磁片區是「資料磁片（E：`\`）」，則可以在本機電腦上將該磁片區附加為「資料磁片」（「任何字母」：`\`）。 流覽腳本輸出中提及的所有磁片區，直到找到您的檔案或資料夾。  

   ![[檔案復原] 功能表](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-linux"></a>若為 Linux

在 Linux 中，復原點的磁碟區會掛接到執行指令碼的資料夾。 連結的磁碟、磁碟區和對應的掛接路徑會相應顯示。 具有根層級存取權的使用者都看得到這些掛接路徑。 在指令碼輸出中瀏覽所述的磁碟區。

  ![Linux [檔案復原] 功能表](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)

## <a name="closing-the-connection"></a>關閉連線

在識別檔案並將它們複製到本機儲存體位置之後，移除 (或卸載) 其他磁碟機。 若要卸載磁碟機，請在 Azure 入口網站的 [檔案復原] 功能表中，按一下 [卸載磁碟]。

![取消掛接磁碟](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

一旦卸載磁碟後，您會收到一則訊息。 連線可能需要幾分鐘重新整理，以讓您可以移除磁碟。

在 Linux 中，復原點的連線嚴重損毀之後，作業系統並不會自動移除對應的掛接路徑。 掛接路徑會以「孤立」磁片區的形式存在且可見，但當您存取/寫入檔案時，會擲回錯誤。 可以手動移除它們。 指令碼執行時，會從任何先前的復原點識別任何這類現有的磁碟區，並在同意下將它們清除。

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

#### <a name="for-lvm-partitions"></a>針對 LVM 磁碟分割

若要列出實體磁片區下的磁片區組名：

```bash
#!/bin/bash
pvs <volume name as shown above in the script output>
```

列出磁片區群組中的所有邏輯磁片區、名稱及其路徑：

```bash
#!/bin/bash
lvdisplay <volume-group-name from the pvs command's results>
```

若要將邏輯磁片區掛接至您選擇的路徑：

```bash
#!/bin/bash
mount <LV path> </mountpath>
```

#### <a name="for-raid-arrays"></a>適用于 RAID 陣列

下列命令會顯示所有 raid 磁片的詳細資料：

```bash
#!/bin/bash
mdadm –detail –scan
```

 相關的 RAID 磁碟將顯示為 `/dev/mdm/<RAID array name in the protected VM>`

如果 RAID 磁片具有實體磁片區，請使用掛接命令：

```bash
#!/bin/bash
mount [RAID Disk Path] [/mountpath]
```

如果 RAID 磁片已設定另一個 LVM，請針對 LVM 磁碟分割使用上述程式，但使用磁片區名稱來取代 RAID 磁片名稱。

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
> 我們發現在具有 SLES 12 SP4 OS 的電腦上執行檔案復原腳本時發生一些問題，我們正向 SLES 團隊進行調查。
> 目前，執行檔案復原腳本的作業是在具有 SLES 12 SP2 和 SP3 OS 版本的電腦上運作。
>

指令碼也需要 Python 和 Bash 元件，才能夠執行並安全地連線至復原點。

|元件 | 版本  |
| --------------- | ---- |
| Bash | 4 和更新版本 |
| Python | 2.6.6 和更新版本  |
| TLS | 應支援 1.2  |

## <a name="access-requirements"></a>存取需求

如果您在具有限制存取的電腦上執行指令碼，請確定可存取︰

- `download.microsoft.com`
- 復原服務 URL (geo-name 是指復原服務保存庫所在的區域)
  - `https://pod01-rec2.geo-name.backup.windowsazure.com` (適用於 Azure 公用地區)
  - `https://pod01-rec2.geo-name.backup.windowsazure.cn` （適用于 Azure 中國世紀）
  - `https://pod01-rec2.geo-name.backup.windowsazure.us` (適用於 Azure US Government)
  - `https://pod01-rec2.geo-name.backup.windowsazure.de` (適用於 Azure 德國)
- 輸出埠53（DNS）、443、3260

> [!NOTE]
>
> - 下載的指令檔名將會在 URL 中填入**地區名稱**。 針對範例：下載的腳本名稱開頭為 \'VMname\'\_\'geoname\'_\'GUID\'，例如*ContosoVM_wcus_12345678*
> - URL 會是 <https://pod01-rec2.wcus.backup.windowsazure.com>」
>

若為 Linux，指令碼需要 'open-iscsi' 和 'lshw' 元件來連接到復原點。 如果元件不存在於執行腳本的電腦上，腳本會要求安裝元件的許可權。 同意安裝必要的元件。

必須要有 `download.microsoft.com` 的存取權，才能下載用來在執行腳本的電腦與復原點中的資料之間建立安全通道的元件。

您可以在任何具有與備份 VM 相的 (或相容) 作業系統的電腦上執行指令碼。 請參閱[相容作業系統資料表](backup-azure-restore-files-from-vm.md#system-requirements)以查看相容的作業系統。 如果受保護的 Azure 虛擬機器使用 Windows 儲存空間 (適用於 Windows Azure 虛擬機器) 或 LVM/RAID 陣列 (適用於 Linux 虛擬機器)，則您無法在同一部虛擬機器上執行可執行檔或指令碼。 請改為在其他具有相容作業系統的電腦上執行可執行或指令碼。

## <a name="file-recovery-from-virtual-machine-backups-having-large-disks"></a>從具有大型磁片的虛擬機器備份進行檔復原

本節說明如何從具有超過16個磁片的 Azure 虛擬機器備份執行檔案復原，而每個磁片大小大於 32 TB。

因為檔案復原程式會從備份附加所有磁片，所以當使用大量磁片（> 16）或大型磁片（每個 > 32 TB）時，建議採用下列動作點：

- 保留個別的還原伺服器（Azure VM D2v3 Vm）以進行檔復原。 您只能針對檔案復原使用該檔案，然後在不需要時將它關閉。 不建議在原始電腦上還原，因為它對 VM 本身會有顯著的影響。
- 然後執行腳本一次，檢查檔案復原操作是否成功。
- 如果檔案復原程式停止回應（磁片永遠不會掛接，或已掛接但未出現磁片區），請執行下列步驟。
  - 如果還原伺服器是 Windows VM：
    - 請確定作業系統是 WS 2012 或更高版本。
    - 請確定已在還原伺服器中將登錄機碼設為建議的設定，並務必重新開機伺服器。 GUID 旁的數位範圍可以從0001-0005。 在下列範例中，它是 .0004。 在 [參數] 區段之前，流覽登錄機碼路徑。

    ![iscsi-reg-key-changes .png](media/backup-azure-restore-files-from-vm/iscsi-reg-key-changes.png)

```registry
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Disk\TimeOutValue – change this from 60 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\SrbTimeoutDelta – change this from 15 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\EnableNOPOut – change this from 0 to 1
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\MaxRequestHoldTime - change this from 60 to 1200
```

- 如果還原伺服器是 Linux VM：
  - 在 [檔案/etc/iscsi/iscsid.conf] 中，將設定從：
    - node.js [0]. timeo. noop_out_timeout = 5 到 node. conn [0]. timeo. noop_out_timeout = 30
- 進行上述變更之後，再次執行腳本。 有了這些變更，很可能會成功復原檔。
- 每次使用者下載腳本時，Azure 備份會起始準備復原點以進行下載的程式。 若使用大型磁片，此程式將需要相當長的時間。 如果有連續的要求高載，則目標準備將會進入下載螺旋。 因此，建議您從入口網站/Powershell/CLI 下載腳本，等待20-30 分鐘（啟發學習法），然後加以執行。 此時，目標應該已準備好從腳本連接。
- 在檔案復原之後，請務必返回入口網站，然後按一下 [**卸載磁片**] 以取得無法裝載磁片區的復原點。 基本上，此步驟會清除任何現有的處理常式/會話，並增加復原的機會。

## <a name="troubleshooting"></a>疑難排解

如果您從虛擬機器復原檔案時遇到問題，請檢查下表中的其他資訊。

| 錯誤訊息 / 案例 | 可能的原因 | 建議的動作 |
| ------------------------ | -------------- | ------------------ |
| Exe 輸出：*連接到目標時發生例外*狀況 | 腳本無法存取復原點    | 檢查機器是否滿足先前的[存取需求](#access-requirements)。 |  
| Exe 輸出︰目標已透過 iSCSI 工作階段登入。 | 指令碼已在相同電腦上執行，並已附加磁碟機 | 復原點磁碟區已連接。 它們可能未使用原始 VM 的相同磁碟機代號裝載。 流覽檔案瀏覽器中所有可用的磁片區，以尋找您的檔案。 |
| Exe 輸出：*此腳本無效，因為磁片已透過入口網站/超過12小時限制卸載。從入口網站下載新的腳本。* |    已從入口網站卸載磁片或超過12小時的限制 | 這個特定的 exe 現在無效，無法執行。 如果您想要存取該恢復時間點的檔案，請造訪入口網站以取得新的 exe。|
| 在執行 exe 的電腦上：按一下 [卸載] 按鈕之後，不會卸載新的磁片區 | 機器上的 iSCSI 啟動器沒有回應/重新整理其與目標的連接，以及維護快取。 |  按一下 [卸載] 後，請稍候幾分鐘。 如果未卸載新的磁片區，請流覽所有磁片區。 流覽所有磁片區會強制啟動器重新整理連線，而且會卸載磁片區，並顯示無法使用磁片的錯誤訊息。|
| Exe 輸出：腳本已順利執行，但腳本輸出上並未顯示「附加的新磁片區」 |    這是暫時性的錯誤    | 磁片區已經連接。 開啟檔案總管以瀏覽。 如果您每次都使用同一部電腦來執行腳本，請考慮重新開機電腦，且清單應該會顯示在後續的 exe 執行中。 |
| Linux 特定︰無法檢視所需的磁碟區 | 執行指令碼所在電腦的作業系統可能無法辨識受保護 VM 的底層檔案系統 | 檢查復原點是否為損毀一致或檔案一致。 如果檔案一致，請在作業系統可識別受保護 VM 檔案系統的另一部電腦上執行腳本。 |
| Windows 特定︰無法檢視所需的磁碟區 | 磁片可能已附加，但尚未設定磁片區 | 從 [磁碟管理] 畫面上，找出與復原點相關的其他磁碟。 如果其中有任何磁片處於離線狀態，請嘗試以滑鼠右鍵按一下磁片，然後按一下 [**線上**]，讓它們上線。|

## <a name="security"></a>安全性

本節討論從 Azure VM 備份執行檔復原所採取的各種安全性措施。

### <a name="feature-flow"></a>功能流程

這項功能的建立是用來存取 VM 資料，而不需要還原整個 VM 或 VM 磁片，而且至少有幾個步驟。 VM 資料的存取是由腳本所提供（執行如下所示的掛接復原磁片區），而且會形成所有安全性實現的基石：

  ![安全性功能流程](./media/backup-azure-restore-files-from-vm/vm-security-feature-flow.png)

### <a name="security-implementations"></a>安全性實現

#### <a name="select-recovery-point-who-can-generate-script"></a>選取復原點（可產生腳本的人員）

此腳本會提供 VM 資料的存取權，因此請務必控管誰可以先進行產生。 您必須登入 Azure 入口網站，並以[RBAC 授權](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions)產生腳本。

檔案復原需要 VM 還原和磁片還原所需的相同授權層級。 換句話說，只有授權的使用者可以查看 VM 資料才能產生腳本。

產生的腳本會使用 Azure 備份服務的官方 Microsoft 憑證進行簽署。 任何對腳本進行的篡改都表示簽章已損毀，而任何執行腳本的嘗試都會被視為作業系統的潛在風險。

#### <a name="mount-recovery-volume-who-can-run-script"></a>掛接復原磁片區（可執行腳本的人員）

只有系統管理員可以執行腳本，而且應該在提高許可權的模式下執行。 此腳本只會執行一組預先產生的步驟，而且不接受任何外部來源的輸入。

若要執行腳本，只需要在 Azure 入口網站或 PowerShell/CLI 中產生腳本時，向授權的使用者顯示密碼。 這是為了確保下載腳本的授權使用者也會負責執行腳本。

#### <a name="browse-files-and-folders"></a>流覽檔案和資料夾

為了流覽檔案和資料夾，腳本會使用電腦中的 iSCSI 啟動器，並聯機至設定為 iSCSI 目標的復原點。 在這裡，您可以想像其中一個嘗試模仿/所有元件的案例。

我們會使用相互 CHAP 驗證機制，讓每個元件彼此驗證。 這表示很難讓假的啟動器連接到 iSCSI 目標，並讓假的目標連接到執行腳本的電腦。

復原服務與電腦之間的資料流程會透過 TCP 建立安全的 TLS 通道來保護（執行腳本的電腦[應支援 tls 1.2](#system-requirements) ）。

所有存在於父系/備份 VM 中的檔案存取控制清單（ACL）也會保留在掛接的檔案系統中。

此腳本會提供復原點的唯讀存取權，且只適用于12小時。 如果您想要移除先前的存取權，請登入 Azure 入口網站/PowerShell/CLI，並針對該特定復原點執行**卸載磁片**。 腳本會立即失效。

## <a name="next-steps"></a>後續步驟

- 針對還原檔案時的任何問題，請參閱[疑難排解](#troubleshooting)一節
- 瞭解如何透過[Powershell 還原檔](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#restore-files-from-an-azure-vm-backup)
- 瞭解如何透過[Azure CLI 還原檔](https://docs.microsoft.com/azure/backup/tutorial-restore-files)
- 還原 VM 之後，瞭解如何[管理備份](https://docs.microsoft.com/azure/backup/backup-azure-manage-vms)
