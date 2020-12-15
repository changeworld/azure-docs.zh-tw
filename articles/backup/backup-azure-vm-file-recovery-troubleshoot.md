---
title: 針對 Azure VM 檔復原進行疑難排解
description: 針對從 Azure VM 備份復原檔案和資料夾的特定問題進行疑難排解。
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: 96876415405cc5099b4d9ca209c36086b793d6e0
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97513304"
---
# <a name="troubleshooting-issues-in-file-recovery-of-azure-vm-backup"></a>針對 Azure VM 備份的檔案復原問題進行疑難排解

本文提供疑難排解步驟，可協助您解決與從 Azure VM 備份復原檔案和資料夾相關的問題 Azure 備份錯誤。 

## <a name="exception-caught-while-connecting-to-target"></a>連接到目標時攔截到例外狀況

可能的原因：腳本無法存取復原點建議的動作：檢查電腦是否符合所有 [存取需求](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script)。

## <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>目標已透過 iSCSI 會話登入

可能的原因：腳本已在同一部電腦上執行，而且已連接磁片磁碟機。
建議動作：已附加復原點的磁片區。 它們可能不會使用原始 VM 的相同磁碟機號來裝載。 流覽檔案瀏覽器中所有可用的磁片區。

## <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>這個指令碼無效，因為磁碟已透過入口網站/超過 12 小時限制卸載。 從入口網站下載新的腳本

可能的原因：已從入口網站卸載磁片或超過12小時的限制。
建議動作：腳本自下載和無法執行以來的12小時之後無效。 流覽入口網站並下載新的腳本，以繼續進行檔復原。

## <a name="troubleshooting-common-issues"></a>針對常見問題進行疑難排解

本節提供在下載及執行檔復原的腳本時所遇到之問題的疑難排解步驟。

## <a name="cannot-download-the-script"></a>無法下載腳本

建議的動作：

1. 確定您擁有 [下載腳本的所有必要許可權](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#select-recovery-point-who-can-generate-script)。
2. 請確定有連線到 Azure 目標 IP (s) 。
若要確認連線，請從提升許可權的命令提示字元執行下列命令。 
    - *nslookup download.microsoft.com* 或
    - *ping download.microsoft.com*

## <a name="the-script-downloads-successfully-but-fails-to-run"></a>腳本下載成功，但無法執行

### <a name="for-sles-12-sp4-os-linux"></a>針對 SLES 12 SP4 作業系統 (Linux) 

錯誤：找不到 iscsi_tcp_module

可能的原因：執行 Python 腳本以進行 SUSE Linux OS 版本12sp4 上的專案層級復原時，它會失敗並出現錯誤 ***iscsi_tcp 模組無法載入** _。 ILR 模組使用 ' iscsi_tcp ' 來建立與備份服務的 tcp 連線，在12SP4 版本中移除從開放式 iscsi 封裝 iscsi_tcp 的一部分，因此 ILR 作業會失敗。

建議動作： SUSE 12SP4 Vm 不支援執行檔案復原腳本，請嘗試在較舊版本的 SUSE 12SP4 上進行還原作業。

## <a name="the-script-runs-but-connection-to-iscsi-target-failed"></a>腳本會執行，但與 iSCSI 目標的連線失敗

錯誤：連接至目標時攔截到例外狀況

1. 確定執行腳本的電腦符合所有 [存取需求](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script)。
2. 檢查與 Azure 目標 IP (s) 的連線能力。
若要確認連線，請從提升許可權的命令提示字元執行下列命令。 
    - _nslookup download.microsoft.com * 或<br>
    - *ping download.microsoft.com*
3. 確定有 iSCSI 輸出埠3260的存取權。
4. 請確定沒有任何防火牆/NSG 封鎖 Azure 目標 IP (s) 或復原服務 Url 的流量。
5. 檢查防毒軟體是否封鎖腳本的執行。

## <a name="connected-to-recovery-point-but-disks-did-not-get-attached"></a>已連線到復原點，但磁片未附加

確定您有 [正確的電腦可執行腳本](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)

### <a name="on-windows-vm"></a>在 Windows VM 上

**VM 上的存放集區會以唯讀模式附加** 若為 Windows 2012 R2 和 Windows 2016 (與存放集區) ，則在第一次執行腳本時，連結至 VM 的儲存集區可能會進入唯讀狀態。

若要解決此問題，我們需要手動將 Read-Write 存取儲存集區，並連接虛擬磁片，請遵循下列步驟：

1. 設定 Read-Write 存取]。
流覽至伺服器管理員 > 的檔案和存放服務 > 磁片區 > 儲存集區。

   ![Windows 儲存體](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

2. 在 [ **存放集區** ] 視窗中，以滑鼠右鍵按一下可用的存放集區，然後選取 [ **設定 Read-Write 存取** ] 選項。

   ![Windows 儲存體讀寫](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

3. 使用讀取/寫入存取權指派儲存集區之後，我們必須連接虛擬磁片。
流覽至 [虛擬磁片] 區段下的 **伺服器管理員 UI**，> 以滑鼠右鍵按一下以選取 [ **連接虛擬磁片** ] 選項。

   ![伺服器管理員虛擬磁片](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

### <a name="on-linux-vm"></a>在 Linux VM 上

#### <a name="file-recovery-fails-to-auto-mount-because-disk-does-not-contain-volumes"></a>因為磁片不包含磁片區，所以檔案復原無法自動掛接

在執行檔復原時，備份服務會偵測磁片區和自動裝載。 不過，如果備份的磁片具有原始磁碟分割，則不會自動掛接這些磁片，我們將無法看到資料磁片來進行復原。

若要解決此問題，請 [遵循本文中](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms)所述的步驟。
 
#### <a name="os-could-not-identify-the-filesystem-causing-linux-file-recovery-to-fail-while-mountings-disks"></a>OS 無法識別檔案系統造成 Linux 檔復原在 mountings 磁片時失敗

執行檔案復原腳本時，資料磁片無法附加，因為發生下列錯誤：無法 *掛接下列磁碟分割，因為 OS 無法識別檔案系統*

- 若要解決此問題，請檢查磁片區是否已使用協力廠商應用程式加密。 如果已加密，則磁片或 VM 將不會在入口網站上顯示為已加密。
1. 登入已備份的 VM 並執行下列命令： *lsblk-f*<br>

   ![沒有磁片區的磁片](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

2. 驗證檔案系統和加密。
3. 如果磁片區已加密，則不支援檔復原。 [深入了解](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas#support-for-file-level-restore)。

## <a name="disks-are-attached-but-unable-to-mount-volumes"></a>磁片已附加，但無法掛接磁片區

- 確定您有 [正確的電腦可執行腳本](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)。

### <a name="on-windows-vm"></a>在 windows VM 上

執行 windows 的檔案復原腳本時，有一則訊息，指出 **已連接 * 0** 磁片區，但磁片會在磁片管理主控台中找到。 透過 iSCSI 連接磁片區時，會偵測到某些磁片區會進入離線狀態。 當 iSCSI 通道在 VM 與服務之間通訊時，它會偵測這些磁片區，並使其上線，但不會掛接它們。

   ![磁片未附加](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

若要識別並解決此問題，請執行下列步驟：

1. 在 cmd 視窗中執行 **diskmgmt.msc** 命令，以移至 [*磁片管理*]。
2. 檢查是否顯示任何額外的磁片。 在下列範例中，Disk 2 是額外的磁片。

   ![磁片 management0](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

3. 以滑鼠右鍵按一下 **磁片** 區，然後選取 [ **變更磁碟機號及路徑**]。

   ![磁片 management1](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

4. 在 [ **新增磁碟機號或路徑** ] 視窗中，選取 [ **指派下列磁碟機號** ] 並指派可用的磁片磁碟機，然後按一下 **[確定]**。 

   ![磁片 management2](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

5. 您可以從 [檔案瀏覽器] 查看磁片磁碟機。

   ![磁片 management3](./media/backup-azure-restore-files-from-vm/disk-management-10.png)

6. 應該附加新的磁片區。  

   ![磁片未掛接](./media/backup-azure-restore-files-from-vm/disk-not-mounting-11.png)

7. 在 [檔案瀏覽器] 中，重新執行腳本之後，就會顯示新的磁片區。

   ![磁片未 mounting1](./media/backup-azure-restore-files-from-vm/disk-not-mounting-12.png)

   ![磁片未 mounting2](./media/backup-azure-restore-files-from-vm/disk-not-mounting-13.png)

#### <a name="on-linux-vms"></a>在 Linux Vm 上 

- 確定您有 [正確的電腦可執行腳本](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)。
- 如果受保護的 Linux VM 使用 LVM 和/或 RAID 陣列，請遵循本文所列的[步驟。](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms)

## <a name="cannot-copy-the-files-from-mounted-volumes"></a>無法從載入的磁片區複製檔案

複製可能會失敗，並出現錯誤0x80070780：系統無法存取檔案。 在此情況下，請檢查來源伺服器是否已啟用磁片重復資料刪除。 然後確定還原伺服器也已在磁片磁碟機上啟用重復資料刪除。 您可以讓重復資料刪除角色保持未設定，如此您就不會刪除還原伺服器上的磁片磁碟機。
