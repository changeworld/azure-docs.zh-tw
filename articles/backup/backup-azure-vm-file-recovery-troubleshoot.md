---
title: 針對 Azure VM 檔復原進行疑難排解
description: 針對從 Azure VM 備份復原檔案和資料夾時所發生的問題進行疑難排解。
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: bd369577e320cf6dca510183948f41e6cf91779b
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97605287"
---
# <a name="troubleshooting-issues-in-file-recovery-of-azure-vm-backup"></a>針對 Azure VM 備份的檔案復原問題進行疑難排解

本文提供疑難排解步驟，可協助您解決從 Azure VM 備份復原檔案和資料夾時，與問題相關的 Azure 備份錯誤。

## <a name="common-error-messages"></a>常見的錯誤訊息

### <a name="exception-caught-while-connecting-to-target"></a>連接到目標時攔截到例外狀況

**可能的原因**：腳本無法存取復原點。

**建議的動作**：檢查電腦是否滿足所有 [存取需求](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script)。

### <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>目標已透過 iSCSI 會話登入

**可能的原因**：腳本已在同一部電腦上執行，而且已連接磁片磁碟機。

**建議動作**：已附加復原點的磁片區。 它們可能不會使用原始 VM 的相同磁碟機號來裝載。 流覽檔案瀏覽器中所有可用的磁片區。

### <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>這個指令碼無效，因為磁碟已透過入口網站/超過 12 小時限制卸載。 從入口網站下載新的腳本

**可能的原因**：已從入口網站卸載磁片或超過12小時的限制。

**建議動作**：腳本自下載和無法執行以來的12小時之後無效。 流覽入口網站並下載新的腳本，以繼續進行檔復原。

## <a name="troubleshooting-common-scenarios"></a>常見案例疑難排解

本節提供在下載及執行檔復原的腳本時，您可能會遇到之問題的疑難排解步驟。

### <a name="cant-download-the-script"></a>無法下載腳本

建議的動作：

1. 確定您擁有 [下載腳本的所有必要許可權](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#select-recovery-point-who-can-generate-script)。
1. 確定 Azure 目標 Ip 有連線能力

若要驗證連線，請從提升許可權的命令提示字元中執行下列其中一個命令。

`nslookup download.microsoft.com`

或

`ping download.microsoft.com`

### <a name="the-script-downloads-successfully-but-fails-to-run"></a>腳本下載成功，但無法執行

#### <a name="for-sles-12-sp4-os-linux"></a>針對 SLES 12 SP4 作業系統 (Linux) 

**錯誤**：找不到 iscsi_tcp_module

**可能的原因**：執行專案層級復原的 Python 腳本 (ILR) 在 SUSE Linux OS 版本12sp4 上，它會失敗並出現錯誤 **_iscsi_tcp 模組無法載入_* _。

ILR 模組會使用 _ *iscsi_tcp** 來建立與備份服務的 tcp 連線。 在12SP4 版本中，SUSE 從開放式 iscsi 套件中移除 **iscsi_tcp** ，因此 ILR 作業會失敗。

**建議動作**： SUSE 12SP4 vm 不支援執行檔案復原腳本。 請嘗試在較舊版本的 SUSE 12SP4 上進行還原作業。

### <a name="the-script-runs-but-connection-to-iscsi-target-failed"></a>腳本會執行，但與 iSCSI 目標的連線失敗

**錯誤**：連接至目標時攔截到例外狀況

1. 確定執行腳本的電腦符合所有 [存取需求](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script)。
1. 檢查 Azure 目標 Ip 的連線能力。
若要驗證連線，請從提升許可權的命令提示字元中執行下列其中一個命令。

   `nslookup download.microsoft.com`

   或

   `ping download.microsoft.com`
1. 確定有 iSCSI 輸出埠3260的存取權。
1. 請確定沒有任何防火牆或 NSG 封鎖 Azure 目標 Ip 或復原服務 Url 的流量。
1. 檢查防毒軟體是否封鎖腳本的執行。

### <a name="connected-to-recovery-point-but-disks-didnt-get-attached"></a>連線到復原點但未連接磁片

確定您有 [正確的電腦可執行腳本](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)

#### <a name="on-a-windows-vm"></a>在 Windows VM 上

**Vm 上的存放集區會以唯讀模式連接**：在 Windows 2012 R2 和 windows 2016 (與存放集區) ，當您第一次執行腳本時，連結至 VM 的儲存集區可能會進入唯讀狀態。

若要解決此問題，我們需要手動將 Read-Write 存取儲存集區，並連接虛擬磁片。 遵循下列步驟：

1. 設定 Read-Write 存取]。

   流覽至 **伺服器管理員** 檔案  >  **和存放服務**  >  **磁片**  >  **區的存放集區**。

   ![Windows 儲存體](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

1. 在 [ **存放集區** ] 視窗中，以滑鼠右鍵按一下可用的存放集區，然後選取 [ **設定 Read-Write 存取** ] 選項。

   ![Windows 儲存體讀寫](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

1. 使用讀取/寫入存取權指派儲存集區之後，請連接虛擬磁片。

   流覽至 **伺服器管理員 UI**。 在 [ **虛擬磁片** ] 區段底下 > 以滑鼠右鍵按一下以選取 [ **連接虛擬磁片** ] 選項。

   ![伺服器管理員虛擬磁片](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

#### <a name="on-a-linux-vm"></a>在 Linux VM 上

##### <a name="file-recovery-fails-to-auto-mount-because-disk-doesnt-contain-volumes"></a>因為磁片不包含磁片區，所以檔案復原無法自動掛接

執行檔復原時，備份服務會偵測磁片區和 automounts。 不過，如果備份的磁片具有原始磁碟分割，則這些磁片不會 automounted，而且您看不到資料磁片來進行復原。

若要解決此問題，請 [遵循本文中](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms)所述的步驟。

##### <a name="the-os-couldnt-identify-the-filesystem-causing-linux-file-recovery-to-fail-while-mountings-disks"></a>OS 無法識別檔案系統導致 Linux 檔復原在 mountings 磁片時失敗

執行檔案復原腳本時，資料磁片無法附加，並出現下列錯誤：

「因為 OS 無法識別檔案系統，所以無法掛接下列磁碟分割」

若要解決此問題，請檢查磁片區是否已使用協力廠商應用程式加密。 如果已加密，則磁片或 VM 將不會在入口網站上顯示為已加密。

1. 登入已備份的 VM，然後執行下列命令：

   `*lsblk -f*`

   ![沒有磁片區的磁片](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

2. 驗證檔案系統和加密。
3. 如果磁片區已加密，則不支援檔復原。 [深入了解](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas#support-for-file-level-restore)。

### <a name="disks-are-attached-but-unable-to-mount-volumes"></a>磁片已附加，但無法掛接磁片區

- 確定您有 [正確的電腦可執行腳本](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)。

#### <a name="on-windows-vms"></a>在 Windows Vm 上

執行 Windows 的檔案復原腳本時，有一則訊息，指出 **已附加 * 0 磁片** 區 _。 不過，磁片會在 [磁片管理] 主控台中找到。 透過 iSCSI 連接磁片區時，某些偵測到的磁片區會進入離線狀態。 當 iSCSI 通道在 VM 與服務之間通訊時，它會偵測這些磁片區並將其上線，但不會掛接它們。

   ![磁片未附加](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

若要識別並解決此問題，請執行下列步驟：

1. 在 cmd 視窗中執行 **diskmgmt.msc** 命令，以移至 _ [*磁片管理*]。
1. 檢查是否顯示任何額外的磁片。 在下列範例中，Disk 2 是額外的磁片。

   ![磁片 management0](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

1. 以滑鼠右鍵按一下 **新的磁片** 區，然後選取 [ **變更磁碟機號及路徑**]。

   ![磁片 management1](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

1. 在 [ **新增磁碟機號或路徑** ] 視窗中，選取 **[指派下列磁碟機號** ] 並指派可用的磁片磁碟機，然後選取 **[確定]**。

   ![磁片 management2](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

1. 從 [檔案瀏覽器] 中，查看您選擇的磁碟機號，然後流覽檔案。

#### <a name="on-linux-vms"></a>在 Linux Vm 上

- 確定您有 [正確的電腦可執行腳本](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)。
- 如果受保護的 Linux VM 使用 LVM 或 RAID 陣列，請遵循本文所列的[步驟。](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms)

### <a name="cant-copy-the-files-from-mounted-volumes"></a>無法從載入的磁片區複製檔案

複製可能會失敗，並出現錯誤 **0x80070780：系統無法存取檔案。** 在此情況下，請檢查來源伺服器是否已啟用磁片重復資料刪除。 然後確定還原伺服器也已在磁片磁碟機上啟用重復資料刪除。 您可以讓重復資料刪除角色保持未設定，如此您就不會刪除還原伺服器上的磁片磁碟機。

## <a name="next-steps"></a>後續步驟

- [從 Azure 虛擬機器備份復原檔案和資料夾](backup-azure-restore-files-from-vm.md)
