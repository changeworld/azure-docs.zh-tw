---
title: 針對 Azure VM 檔復原進行疑難排解
description: 針對從 Azure VM 備份復原檔案和資料夾時所發生的問題進行疑難排解。
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: c4d0d233237cb477d72efea0b91d4e5288e2a302
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735872"
---
# <a name="troubleshoot-issues-in-file-recovery-of-an-azure-vm-backup"></a>針對 Azure VM 備份的檔案復原問題進行疑難排解

本文提供疑難排解步驟，可協助您解決從 Azure 虛擬機器 (VM) 備份復原檔案和資料夾的問題。

## <a name="common-error-messages"></a>常見的錯誤訊息

本節提供針對您可能會看到的錯誤訊息進行疑難排解的步驟。

### <a name="exception-caught-while-connecting-to-target"></a>「連接到目標時攔截到例外狀況」

**可能的原因**：腳本無法存取復原點。

**建議動作**：若要解決此問題，請遵循腳本執行中所列的步驟， [但連接失敗](#the-script-runs-but-the-connection-to-the-iscsi-target-failed)。

### <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>「目標已透過 iSCSI 會話登入」

**可能的原因**：腳本已在同一部電腦上執行，而且已連接磁片磁碟機。

**建議動作**：已附加復原點的磁片區。 它們無法使用原始 VM 的相同磁碟機號來掛接。 流覽檔案總管中可用的磁片區。

### <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>「此腳本無效，因為磁片已透過入口網站/超過12小時的限制卸載。 從入口網站下載新的腳本 "

**可能的原因**：已從入口網站卸載磁片或超過12小時的時間限制。

**建議的動作**：下載腳本後的12小時變成無效，而且無法執行。 移至入口網站，然後下載新的腳本以繼續進行檔復原。

### <a name="iscsi_tcp-module-cant-be-loaded-or-iscsi_tcp_module-not-found"></a>無法載入 iscsi_tcp 模組 (或) 找不到 iscsi_tcp_module

**建議的動作**：若要解決此問題，請遵循 [腳本下載成功但無法執行](#the-script-downloads-successfully-but-fails-to-run)的步驟。

## <a name="common-problems"></a>常見問題

本節提供在下載及執行檔復原的腳本時，您可能會遇到之常見問題的疑難排解步驟。

### <a name="you-cant-download-the-script"></a>您無法下載腳本

1. 確定您擁有 [下載腳本的必要許可權](./backup-azure-restore-files-from-vm.md#select-recovery-point-who-can-generate-script)。
1. 確認與 Azure 目標 Ip 的連接。 從提升許可權的命令提示字元中執行下列其中一個命令：

   `nslookup download.microsoft.com`

    或

    `ping download.microsoft.com`

### <a name="the-script-downloads-successfully-but-fails-to-run"></a>腳本下載成功，但無法執行

當您執行專案層級復原的 Python 腳本 (ILR) SUSE Linux Enterprise Server 12 SP4 上，它會失敗，並出現「無法載入 iscsi_tcp 模組」或「找不到 iscsi_tcp_module」的錯誤。

**可能的原因**： ILR 模組使用 **iscsi_tcp** 建立與備份服務的 tcp 連線。 作為 SLES 12 SP4 版本的一部分，SUSE 從 open iscsi 套件中移除 **iscsi_tcp** ，因此 ILR 作業會失敗。

**建議動作**： SUSE 12 SP4 vm 不支援檔案復原腳本執行。 請嘗試在較舊版本的 SUSE 12 SP4 上進行還原作業。

### <a name="the-script-runs-but-the-connection-to-the-iscsi-target-failed"></a>腳本會執行，但與 iSCSI 目標的連線失敗

您可能會看到「連接到目標時攔截到例外狀況」錯誤訊息。

1. 確定執行腳本的電腦符合 [存取需求](./backup-azure-restore-files-from-vm.md#step-4-access-requirements-to-successfully-run-the-script)。
1. 確認與 Azure 目標 Ip 的連接。 從提升許可權的命令提示字元中執行下列其中一個命令：

   `nslookup download.microsoft.com`

   或

   `ping download.microsoft.com`
1. 確定存取 iSCSI 輸出埠3260。
1. 檢查防火牆或 NSG 封鎖流向 Azure 目標 Ip 或復原服務 Url 的流量。
1. 請確定您的防毒軟體未封鎖腳本的執行。

### <a name="youre-connected-to-the-recovery-point-but-the-disks-werent-attached"></a>您已連線到復原點，但磁片未連接

請遵循您作業系統的步驟來解決此問題。

#### <a name="windows-file-recovery-fails-on-server-with-storage-pools"></a>使用存放集區的伺服器上的 Windows 檔案復原失敗

當您第一次在 Windows Server 2012 R2 和 Windows Server 2016 (與存放集區) 執行腳本時，存放集區可能會以唯讀方式附加至 VM。

>[!Tip]
> 確定您有 [正確的電腦可執行腳本](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)。

若要解決此問題，請手動將讀寫存取權指派給存放集區，並連接虛擬磁片：

1. 移至 **伺服器管理員** 檔案  >  **和存放服務**  >  **磁片**  >  **區儲存集區**。

   ![顯示儲存集區選項的螢幕擷取畫面。](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

1. 在 [ **存放集區** ] 視窗中，以滑鼠右鍵按一下可用的存放集區，然後選取 [ **設定 Read-Write 存取**]。

   ![顯示儲存體多工緩衝處理之滑鼠右鍵選項的螢幕擷取畫面。](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

1. 將存放集區指派為讀寫存取之後，在 [ **虛擬磁片** ] 區段中按一下滑鼠右鍵，然後選取 [ **連接虛擬磁片**]。

   ![顯示虛擬磁片之按右鍵選項的螢幕擷取畫面。](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

#### <a name="linux-file-recovery-fails-to-auto-mount-because-the-disk-doesnt-contain-volumes"></a>Linux 檔復原無法自動掛接，因為磁片不包含磁片區

執行檔復原時，備份服務會偵測磁片區和自動掛接。 不過，如果備份的磁片具有原始磁碟分割，則不會自動掛接這些磁片，而且您看不到資料磁片來進行復原。

若要解決此問題，請移至 [從 Azure 虛擬機器備份復原](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms)檔案。

#### <a name="linux-file-recovery-fails-because-the-os-couldnt-identify-the-file-system"></a>Linux 檔復原失敗，因為 OS 無法識別檔案系統

當您執行檔案復原腳本時，資料磁片無法連接。 您會看到「因為 OS 無法識別檔案系統，所以無法掛接下列分割區」錯誤。

若要解決此問題，請檢查磁片區是否已使用協力廠商應用程式加密。 如果已加密，則磁片或 VM 將不會在入口網站上顯示為已加密。

1. 登入已備份的 VM，然後執行此命令：

   `lsblk -f`

   ![螢幕擷取畫面，顯示列出封鎖裝置的命令結果。](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

1. 確認檔案系統和加密。 如果磁片區已加密，則不支援檔復原。 深入瞭解 [AZURE VM 備份的支援矩陣](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas#support-for-file-level-restore)。

### <a name="disks-are-attached-but-the-volumes-arent-mounted"></a>磁片已附加，但未掛接磁片區

請遵循您作業系統的步驟來解決此問題。

#### <a name="windows"></a>Windows

當您執行 Windows 的檔案復原腳本時，您會看到「已附加0個修復磁片區」訊息。 不過，磁片會在 [磁片管理] 主控台中找到。

**可能的原因**：當您透過 iSCSI 連接磁片區時，偵測到某些磁片區已離線。 當 iSCSI 通道在 VM 與服務之間通訊時，它會偵測這些磁片區並將其上線，但不會掛接它們。

   ![顯示已附加0個復原磁片區的螢幕擷取畫面。](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

若要識別並解決此問題，請執行下列步驟：

>[!Tip]
>確定您有 [正確的電腦可執行腳本](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)。

1. 在 [ **cmd** ] 視窗中，執行 **diskmgmt.msc** 以開啟 [ **磁片管理**]。
1. 尋找任何額外的磁片。 在下列範例中， **disk 2** 是額外的磁片。

   ![具有額外磁片的 [磁片管理] 視窗螢幕擷取畫面。](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

1. 在 [ **新磁片** 區] 上按一下滑鼠右鍵，然後選取 [ **變更磁碟機號及路徑**]。

   ![螢幕擷取畫面，顯示其他磁片上的滑鼠右鍵選項。](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

1. 在 [ **變更磁碟機號或路徑** ] 視窗中，選取 **[指派下列磁碟機號**]，指派可用的磁片磁碟機，然後選取 **[確定]**。

   ![變更磁碟機號或路徑視窗的螢幕擷取畫面。](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

1. 開啟檔案總管以查看您選擇的磁片磁碟機，並探索這些檔案。

#### <a name="linux"></a>Linux

>[!Tip]
>確定您有 [正確的電腦可執行腳本](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)。

如果受保護的 Linux VM 使用 LVM 或 RAID 陣列，請依照 [從 Azure 虛擬機器備份復原](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms)檔案中的步驟執行。

### <a name="you-cant-copy-the-files-from-mounted-volumes"></a>您無法從載入的磁片區複製檔案

複製可能會失敗，並出現「0x80070780：無法由系統存取檔案」錯誤。 

檢查來源伺服器是否已啟用磁片重復資料刪除。 如果有的話，請確定還原伺服器也已在磁片磁碟機上啟用重復資料刪除。 您可以將重復資料刪除設定為未設定，如此您就不會刪除還原伺服器上的磁片磁碟機。

## <a name="next-steps"></a>後續步驟

- [從 Azure 虛擬機器備份復原檔案和資料夾](backup-azure-restore-files-from-vm.md)