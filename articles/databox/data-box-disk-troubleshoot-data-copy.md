---
title: Azure 資料盒磁片故障排除資料複製問題*微軟文檔
description: 介紹如何使用日誌解決 Azure 資料框磁片中資料複製期間看到的問題。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/13/2019
ms.author: alkohli
ms.openlocfilehash: 760f5c6c929aa082993683d7a466a71c6484289a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67148344"
---
# <a name="troubleshoot-data-copy-issues-in-azure-data-box-disk"></a>解決 Azure 資料盒磁片中的資料複製問題

本文適用于 Microsoft Azure 資料盒磁片，並介紹如何在將資料複製到磁片時看到的任何問題進行故障排除。 本文還介紹了使用拆分複製工具時的問題。


## <a name="data-copy-issues-when-using-a-linux-system"></a>使用 Linux 系統時的資料複製問題

本節詳細介紹了使用 Linux 用戶端將資料複製到磁片時所面臨的一些首要問題。

### <a name="issue-drive-getting-mounted-as-read-only"></a>問題：磁碟機以唯讀裝
 
**原因** 

原因可能是未整理的檔案系統。

將磁碟機重新掛接為讀寫不適用於資料箱磁碟。 此情節不適用於由 dislocker 解密的磁碟機。 您可能已經使用下列命令成功重新掛接磁碟機：

    `# mount -o remount, rw /mnt/DataBoxDisk/mountVol1`

雖然已經成功重新掛接，但資料不會保存。

**解析度**

在 Linux 系統上執行以下步驟：

1. 安裝`ntfsprogs`ntfsfix 實用程式的包。
2. 卸載解鎖工具為磁碟機提供的安裝點。 磁碟機的裝載點數會有所不同。

    ```
    unmount /mnt/DataBoxDisk/mountVol1
    ```

3. 在`ntfsfix`相應的路徑上運行。 突出顯示的數位應與步驟 2 相同。

    ```
    ntfsfix /mnt/DataBoxDisk/bitlockerVol1/dislocker-file
    ```

4. 運行以下命令以刪除可能導致裝載問題的休眠中繼資料。

    ```
    ntfs-3g -o remove_hiberfile /mnt/DataBoxDisk/bitlockerVol1/dislocker-file /mnt/DataBoxDisk/mountVol1
    ```

5. 進行乾淨卸載。

    ```
    ./DataBoxDiskUnlock_x86_64 /unmount
    ```

6. 進行清潔解鎖和安裝。
7. 通過寫入檔測試裝載點。
8. 卸載並重新裝入以驗證檔持久性。
9. 繼續資料複製。
 
### <a name="issue-error-with-data-not-persisting-after-copy"></a>問題：複製後資料未保留的錯誤
 
**原因** 

如果您發現磁碟機卸載之後沒有資料 (雖然資料有複製到其中)，則您可能在磁碟機掛接為唯讀之後，將它重新掛接為讀寫。

**解析度**
 
如果是此情況，請參閱[磁碟機掛接為唯讀](#issue-drive-getting-mounted-as-read-only)的解決方法。

如果不是此情況，請複製有資料箱磁碟解除鎖定工具的資料夾之中的記錄，並[連絡 Microsoft 支援服務](data-box-disk-contact-microsoft-support.md)。


## <a name="data-box-disk-split-copy-tool-errors"></a>資料箱磁碟分割複製工具錯誤

下表匯總了使用拆分複製工具在多個磁片上拆分資料時看到的問題。

|錯誤訊息/警告 |建議 |
|---------|---------|
|[資訊]檢索音量的 BitLocker 密碼：m <br>[錯誤]檢索卷 m 的 BitLocker 金鑰時捕獲的異常：<br> 序列未包含項目。|如果目的地資料箱磁碟處於離線狀態，就會擲回這個錯誤。 <br> 對線上磁碟使用 `diskmgmt.msc` 工具。|
|[錯誤] 擲回例外狀況：WMI 作業失敗：<br> Method=UnlockWithNumericalPassword, ReturnValue=2150694965, <br>Win32Message=所提供的復原密碼格式無效。 <br>BitLocker 修復原密碼是 48 位數字。 <br>請確認復原密碼的格式正確，然後再試一次。|先使用資料箱磁碟解除鎖定工具將磁碟解除鎖定，然後重試命令。 如需詳細資訊，請移至 <li> [解除鎖定適用於 Windows 用戶端的資料箱磁碟](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client)。 </li><li> [解除鎖定適用於 Linux 用戶端的資料箱磁碟](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client)。 </li>|
|[錯誤] 擲回例外狀況：目標磁碟機上有 DriveManifest.xml 檔案。 <br> 這表示目標磁碟機可能已透過不同的日誌檔案來準備。 <br>若要將更多資料新增至相同磁碟機，請使用先前的日誌檔案。 要刪除現有資料並重用新導入作業的目標磁碟機，請刪除磁碟機上的*DriveManifest.xml。* 以新的日誌檔案重新執行此命令。| 當您嘗試對多個匯入工作階段使用同一組磁碟機時，就會收到這個錯誤。 <br> 一組磁碟機僅適用於一個分割及複製工作階段。|
|[錯誤] 擲回例外狀況：CopySessionId importdata-sept-test-1 代表前一個複製工作階段，而且無法用於新的複製工作階段。|如果嘗試使用前一個已成功完成的作業名稱來作為新作業的名稱，就會回報此錯誤。<br> 請為您的新作業指派唯一名稱。|
|[資訊] 目的地檔案或目錄名稱超過 NTFS 長度限制。 |當目的檔案因為檔案路徑太長而重新命名時，就會回報此訊息。<br> 請修改 `config.json` 檔案中的配置選項，以控制此行為。|
|[錯誤] 擲回例外狀況：不正確的 JSON 逸出序列。 |當 config.json 的格式無效時，就會回報此訊息。 <br> 請在儲存檔案之前，先確認您的 `config.json` 是使用 [JSONlint](https://jsonlint.com/)。|


## <a name="next-steps"></a>後續步驟

- 瞭解如何[排除驗證工具問題](data-box-disk-troubleshoot.md)。
