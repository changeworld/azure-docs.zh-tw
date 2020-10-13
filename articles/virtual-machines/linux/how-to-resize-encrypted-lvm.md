---
title: 如何使用 Azure 磁碟加密來調整加密邏輯磁片區管理磁片的大小
description: 本文提供使用邏輯磁片區管理來調整 ADE 加密磁片大小的指示。
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 09/21/2020
ms.openlocfilehash: 8e32e4960776084669c8f2f7743b66b9d0f9a6ae
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978470"
---
# <a name="how-to-resize-logical-volume-management-devices-that-use-azure-disk-encryption"></a>如何調整使用 Azure 磁碟加密的邏輯磁片區管理裝置大小

在本文中，您將瞭解如何調整使用 Azure 磁碟加密的資料磁片大小。 若要調整磁片大小，您將使用 Linux 上 (LVM) 的邏輯磁片區管理。 這些步驟適用于多個案例。

您可以在下列環境中使用這種調整大小的進程：

- Linux 發行版本：
    - Red Hat Enterprise Linux (RHEL) 7 或更新版本
    - Ubuntu 16 或更新版本
    - SUSE 12 或更新版本
- Azure 磁碟加密版本： 
    - 單一傳遞延伸模組
    - 雙重傳遞延伸模組

## <a name="prerequisites"></a>必要條件

本文假設您已經：

- 現有的 LVM 設定。 如需詳細資訊，請參閱 [在 LINUX VM 上設定 LVM](configure-lvm.md)。

- Azure 磁碟加密已加密的磁片。 如需詳細資訊，請參閱 [在加密裝置上設定 LVM 和 RAID](how-to-configure-lvm-raid-on-crypt.md)。

- 體驗使用 Linux 和 LVM。

- 體驗在 Azure 上使用資料磁片的 */dev/disk/scsi1/* 路徑。 如需詳細資訊，請參閱針對 [LINUX VM 裝置名稱問題進行疑難排解](../troubleshooting/troubleshoot-device-names-problems.md)。 

## <a name="scenarios"></a>案例

本文中的程式適用于下列案例：

- 傳統 LVM 和 LVM 內部 crypt 設定
- 傳統 LVM 加密 
- LVM-內部 crypt 

### <a name="traditional-lvm-and-lvm-on-crypt-configurations"></a>傳統 LVM 和 LVM 內部 crypt 設定

傳統 LVM 和 LVM 內部 crypt 設定會在磁片區群組 (VG) 具有可用空間時，擴充邏輯磁片區 (LV) 。

### <a name="traditional-lvm-encryption"></a>傳統 LVM 加密 

在傳統 LVM 加密中，Lvs) 會加密。 整個磁片未加密。

使用傳統 LVM 加密，您可以：

- 當您加入新的實體磁片區 (PV) 時，請擴充 LV。
- 當您調整現有 PV 的大小時，請擴充 LV。

### <a name="lvm-on-crypt"></a>LVM-內部 crypt 

磁片加密的建議方法是使用 LVM 加密。 這個方法會加密整個磁片，而不只是 LV。

藉由使用 LVM 內部 crypt，您可以： 

- 當您加入新的 PV 時，請擴充 LV。
- 當您調整現有 PV 的大小時，請擴充 LV。

> [!NOTE]
> 我們不建議將傳統 LVM 加密和 LVM crypt 混合在相同的 VM 上。

下列各節提供如何使用 LVM 和 LVM 內部 crypt 的範例。 這些範例會針對磁片、PVs、VGs、Lvs) 、檔案系統、通用唯一識別碼 (Uuid) 和掛接點使用預先存在的值。 將這些值取代為您自己的值，以符合您的環境。

#### <a name="extend-an-lv-when-the-vg-has-available-space"></a>當 VG 具有可用空間時，擴充 LV

將 Lvs) 調整大小的傳統方式是在 VG 具有可用空間時，擴充 LV。 您可以將此方法用於非加密磁片、傳統 LVM 加密磁片區和 LVM crypt 設定。

1. 確認您要增加的檔案系統目前大小：

    ``` bash
    df -h /mountpoint
    ```

    ![顯示檢查檔案系統大小之程式碼的螢幕擷取畫面。 命令和結果會反白顯示。](./media/disk-encryption/resize-lvm/001-resize-lvm-scenarioa-check-fs.png)

2. 確認 VG 有足夠的空間可增加 LV：

    ``` bash
    vgs
    ```

    ![顯示檢查 VG 空間之程式碼的螢幕擷取畫面。 命令和結果會反白顯示。](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgs.png)

    您也可以使用 `vgdisplay` ：

    ``` bash
    vgdisplay vgname
    ```

    ![顯示 V G 顯示程式碼的螢幕擷取畫面，可檢查 VG 上的空間。 命令和結果會反白顯示。](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgdisplay.png)

3. 找出需要調整大小的 LV：

    ``` bash
    lsblk
    ```

    ![顯示 l s b l k 命令之結果的螢幕擷取畫面。 命令和結果會反白顯示。](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk1.png)

    針對 LVM crypt，差別在於此輸出顯示加密層級位於磁片層級。

    ![顯示 l s b l k 命令之結果的螢幕擷取畫面。 輸出會反白顯示。 它會顯示加密的圖層。](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk2.png)

4. 檢查 LV 大小：

    ``` bash
    lvdisplay lvname
    ```

    ![顯示檢查邏輯磁片區大小之程式碼的螢幕擷取畫面。 命令和結果會反白顯示。](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lvdisplay01.png)

5. 使用 `-r` 將檔案系統的大小調整為線上，以增加 LV 大小：

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![顯示增加邏輯磁片區大小之程式碼的螢幕擷取畫面。 命令和結果會反白顯示。](./media/disk-encryption/resize-lvm/003-resize-lvm-scenarioa-resize-lv.png)

6. 確認 LV 和檔案系統的新大小：

    ``` bash
    df -h /mountpoint
    ```

    ![顯示驗證 LV 和檔案系統大小之程式碼的螢幕擷取畫面。 命令和結果會反白顯示。](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-fs.png)

    大小輸出表示已成功調整 LV 和檔案系統的大小。

您可以再次檢查 LV 資訊，以確認 LV 層級的變更：

``` bash
lvdisplay lvname
```

![顯示確認新大小之程式碼的螢幕擷取畫面。 這些大小會反白顯示。](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-lvdisplay2.png)

#### <a name="extend-a-traditional-lvm-volume-by-adding-a-new-pv"></a>藉由新增新的 PV 來擴充傳統 LVM 磁片區

當您需要新增磁片以增加 VG 大小時，請新增新的 PV 以擴充您的傳統 LVM 磁片區。

1. 確認您要增加的檔案系統目前大小：

    ``` bash
    df -h /mountpoint
    ```

    ![顯示檢查檔案系統目前大小之程式碼的螢幕擷取畫面。 命令和結果會反白顯示。](./media/disk-encryption/resize-lvm/005-resize-lvm-scenariob-check-fs.png)

2. 確認目前的 PV 設定：

    ``` bash
    pvs
    ```

    ![顯示檢查目前 PV 設定之程式碼的螢幕擷取畫面。 命令和結果會反白顯示。](./media/disk-encryption/resize-lvm/006-resize-lvm-scenariob-check-pvs.png)

3. 檢查目前的 VG 資訊：

    ``` bash
    vgs
    ```

    ![顯示檢查目前磁片區群組資訊之程式碼的螢幕擷取畫面。 命令和結果會反白顯示。](./media/disk-encryption/resize-lvm/007-resize-lvm-scenariob-check-vgs.png)

4. 檢查目前的磁片清單。 藉由在 */dev/disk/azure/scsi1/* 中檢查裝置來識別資料磁片。

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![顯示檢查目前磁片清單之程式碼的螢幕擷取畫面。 命令和結果會反白顯示。](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-scs1.png)

5. 檢查 `lsblk` 下列輸出： 

    ``` bash
    lsbk
    ```

    ![螢幕擷取畫面，顯示檢查 l s b l k 輸出的程式碼。 命令和結果會反白顯示。](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-lsblk.png)

6. 遵循 [將資料磁片連接至 LINUX vm](attach-disk-portal.md)中的指示，將新的磁片連結至 VM。

7. 檢查磁片清單，並注意新的磁片。

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![顯示檢查磁片清單之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-scsi12.png)

    ``` bash
    lsbk
    ```

    ![螢幕擷取畫面，顯示使用 l s b l k 來檢查磁片清單的程式碼。 命令和結果會反白顯示。](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-lsblk1.png)

8. 在新的資料磁片之上建立新的 PV：

    ``` bash
    pvcreate /dev/newdisk
    ```

    ![顯示建立新 PV 之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/010-resize-lvm-scenariob-pvcreate.png)

    這個方法會使用整個磁片做為不含分割區的 PV。 或者，您可以使用 `fdisk` 建立資料分割，然後使用該資料分割 `pvcreate` 。

9. 確認 PV 已新增至 PV 清單：

    ``` bash
    pvs
    ```

    ![顯示顯示實體磁片區清單之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/011-resize-lvm-scenariob-check-pvs1.png)

10. 藉由新增新的 PV 來擴充 VG：

    ``` bash
    vgextend vgname /dev/newdisk
    ```

    ![顯示擴充磁片區群組之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/012-resize-lvm-scenariob-vgextend.png)

11. 檢查新的 VG 大小：

    ``` bash
    vgs
    ```

    ![顯示檢查磁片區群組大小之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-vgs1.png)

12. 用 `lsblk` 來識別需要調整大小的 LV：

    ``` bash
    lsblk
    ```

    ![螢幕擷取畫面，顯示可識別需要調整大小之本機磁片區的程式碼。 結果會反白顯示。](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-lsblk1.png)

13. 使用來擴充 LV 大小， `-r` 以在線上增加檔案系統：

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![顯示程式碼的螢幕擷取畫面，此程式碼會增加線上檔案系統的大小。 結果會反白顯示。](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-lvextend.png) 

14. 確認 LV 和檔案系統的新大小：

    ``` bash
    df -h /mountpoint
    ```

    ![顯示檢查本機磁片區和檔案系統大小之程式碼的螢幕擷取畫面。 命令和結果會反白顯示。](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-fs1.png)

    >[!IMPORTANT]
    >在傳統 LVM 設定上使用 Azure 資料加密時，會在 LV 層級（而不是在磁片層級）建立加密層。
    >
    >此時，加密層會擴充到新的磁片。 實際的資料磁片在平台層級沒有任何加密設定，因此其加密狀態不會更新。
    >
    >這些是 crypt LVM 的一些原因，是建議的方法。 

15. 從入口網站檢查加密資訊：

    ![顯示入口網站中加密資訊的螢幕擷取畫面。 磁片名稱和加密會反白顯示。](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal1.png)

    若要更新磁片上的加密設定，請新增 LV，並在 VM 上啟用擴充功能。
    
16. 新增 LV、在其上建立檔案系統，並將它新增至 `/etc/fstab` 。

17. 再次設定加密擴充功能。 這一次，您會在平台層級的新資料磁片上，戳記加密設定。 以下是 CLI 範例：

    ``` bash
    az vm encryption enable -g ${RGNAME} --name ${VMNAME} --disk-encryption-keyvault "<your-unique-keyvault-name>"
    ```

18. 從入口網站檢查加密資訊：

    ![顯示入口網站中加密資訊的螢幕擷取畫面。 系統會反白顯示磁片名稱和加密資訊。](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal2.png)

更新加密設定之後，您可以刪除新的 LV。 也請從您建立的和中刪除專案 `/etc/fstab` `/etc/crypttab` 。

![顯示刪除新邏輯磁片區之程式碼的螢幕擷取畫面。 [已刪除的 F S 索引標籤和 crypt] 索引標籤會醒目提示。](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-delete-fstab-crypttab.png)

請遵循下列步驟來完成清除：

1. 卸載 LV：

    ``` bash
    umount /mountpoint
    ```

1. 關閉磁片區的加密層：

    ``` bash
    cryptsetup luksClose /dev/vgname/lvname
    ```

1. 刪除 LV：

    ``` bash
    lvremove /dev/vgname/lvname
    ```

#### <a name="extend-a-traditional-lvm-volume-by-resizing-an-existing-pv"></a>調整現有的 PV 以擴充傳統 LVM 磁片區

Im 某些案例中，您的限制可能會要求您調整現有磁片的大小。 其做法如下：

1. 識別您的加密磁片：

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![顯示可識別加密磁片之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-scsi1.png)

    ``` bash
    lsblk -fs
    ```

    ![螢幕擷取畫面，顯示可識別加密磁片的替代程式碼。 結果會反白顯示。](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-lsblk.png)

2. 檢查 PV 資訊：

    ``` bash
    pvs
    ```

    ![顯示檢查實體磁片區相關資訊之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/016-resize-lvm-scenarioc-check-pvs.png)

    影像中的結果顯示目前使用所有 PVs 上的所有空間。

3. 檢查 VG 資訊：

    ``` bash
    vgs
    vgdisplay -v vgname
    ```

    ![顯示檢查磁片區群組相關資訊之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/017-resize-lvm-scenarioc-check-vgs.png)

4. 檢查磁片大小。 您可以使用 `fdisk` 或 `lsblk` 來列出磁片磁碟機大小。

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![顯示檢查磁片大小之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/018-resize-lvm-scenarioc-check-fdisk.png)

    在這裡，我們識別出哪些 PVs 與使用的 Lvs) 相關聯 `lsblk -fs` 。 您可以藉由執行來識別關聯 `lvdisplay` 。

    ``` bash
    lvdisplay --maps VG/LV
    lvdisplay --maps datavg/datalv1
    ```

    ![螢幕擷取畫面，顯示識別與本機磁片區的實體磁片區關聯的替代方法。 結果會反白顯示。](./media/disk-encryption/resize-lvm/019-resize-lvm-scenarioc-check-lvdisplay.png)

    在此情況下，所有四個數據磁片磁碟機都屬於相同 VG 和單一 LV 的一部分。 您的設定可能會有所不同。

5. 檢查目前的檔案系統使用量：

    ``` bash
    df -h /datalvm*
    ```

    ![顯示檢查檔案系統使用量之程式碼的螢幕擷取畫面。 命令和結果會反白顯示。](./media/disk-encryption/resize-lvm/020-resize-lvm-scenarioc-check-df.png)

6. 遵循 [擴充 Azure 受控磁片](expand-disks.md#expand-an-azure-managed-disk)中的指示，調整資料磁片的大小。 您可以使用入口網站、CLI 或 PowerShell。

    >[!IMPORTANT]
    >當 VM 正在執行時，您無法調整虛擬磁片的大小。 將您的 VM 解除配置，以進行此步驟。

7. 啟動 VM，並使用來檢查新的大小 `fdisk` 。

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![顯示檢查磁片大小之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/021-resize-lvm-scenarioc-check-fdisk1.png)

    在此情況下， `/dev/sdd` 已從 5 g 調整為 20 g。

8. 檢查目前的 PV 大小：

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![顯示檢查 P V 大小之程式碼的螢幕擷取畫面。結果會反白顯示。](./media/disk-encryption/resize-lvm/022-resize-lvm-scenarioc-check-pvdisplay.png)
    
    即使磁片已調整大小，但 PV 仍具有先前的大小。

9. 調整 PV 的大小：

    ``` bash
    pvresize /dev/resizeddisk
    ```

    ![顯示調整實體磁片區之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/023-resize-lvm-scenarioc-check-pvresize.png)


10. 檢查 PV 大小：

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![顯示檢查實體磁片區大小之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/024-resize-lvm-scenarioc-check-pvdisplay1.png)

    針對您要調整大小的所有磁片套用相同的程式。

11. 檢查 VG 資訊。

    ``` bash
    vgdisplay vgname
    ```

    ![顯示檢查磁片區群組資訊之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/025-resize-lvm-scenarioc-check-vgdisplay1.png)

    VG 現在有足夠的空間可配置給 Lvs) 。

12. 調整 LV 的大小：

    ``` bash
    lvresize -r -L +5G vgname/lvname
    lvresize -r -l +100%FREE /dev/datavg/datalv01
    ```

    ![顯示調整 L V 大小之程式碼的螢幕擷取畫面。結果會反白顯示。](./media/disk-encryption/resize-lvm/031-resize-lvm-scenarioc-check-lvresize1.png)

13. 檢查檔案系統的大小：

    ``` bash
    df -h /datalvm2
    ```

    ![顯示檢查檔案系統大小之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/032-resize-lvm-scenarioc-check-df3.png)

#### <a name="extend-an-lvm-on-crypt-volume-by-adding-a-new-pv"></a>藉由新增新的 PV 來擴充 LVM 內部 crypt 磁片區

您也可以藉由新增新的 PV 來擴充 LVM 內部 crypt 磁片區。 此方法會嚴格遵循在 [加密裝置上設定 LVM 和 RAID](how-to-configure-lvm-raid-on-crypt.md#general-steps)的步驟。 請參閱說明如何新增磁片並在 crypt 設定中設定它的章節。

您可以使用這個方法將空間新增至現有的 LV。 或者，您可以建立新的 VGs 或 Lvs) 。

1. 確認 VG 的目前大小：

    ``` bash
    vgdisplay vgname
    ```

    ![顯示檢查磁片區群組大小之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/033-resize-lvm-scenarioe-check-vg01.png)

2. 確認您要擴充的檔案系統和 LV 的大小：

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![顯示檢查本機磁片區大小之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-lv01.png)

    ``` bash
    df -h mountpoint
    ```

    ![顯示檢查檔案系統大小之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-fs01.png)

3. 將新的資料磁片新增至 VM，並加以識別。

    在您新增新的磁片之前，請先檢查磁片：

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![顯示檢查磁片大小之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk01.png)

    以下是在新增新磁片之前檢查磁片的另一種方式：

    ``` bash
    lsblk
    ```

    ![螢幕擷取畫面，顯示檢查磁片大小的替代程式碼。 結果會反白顯示。](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk02.png)

    若要新增新的磁片，您可以使用 PowerShell、Azure CLI 或 Azure 入口網站。 如需詳細資訊，請參閱 [將資料磁片連接至 LINUX VM](attach-disk-portal.md)。

    核心名稱配置會套用至新加入的裝置。 通常會將下一個可用的字母指派給新的磁片磁碟機。 在此情況下，新增的磁片為 `sdd` 。

4. 請檢查磁片以確定已新增新磁片：

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![顯示列出磁片之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk02.png)

    ``` bash
    lsblk
    ```

    ![螢幕擷取畫面，顯示輸出中新增的磁片。](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk03.png)

5. 在最近新增的磁片之上建立檔案系統。 將磁片與上的連結裝置進行比對 `/dev/disk/azure/scsi1/` 。

    ``` bash
    ls -la /dev/disk/azure/scsi1/
    ```

    ![顯示建立檔案系統之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/037-resize-lvm-scenarioe-check-newdisk03.png)

    ``` bash
    mkfs.ext4 /dev/disk/azure/scsi1/${disk}
    ```

    ![顯示額外程式碼的螢幕擷取畫面，可建立檔案系統並將磁片與連結的裝置比對。 結果會反白顯示。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-mkfs01.png)

6. 建立新新增磁片的暫存掛接點：

    ``` bash
    newmount=/data4
    mkdir ${newmount}
    ```

7. 將最近建立的檔案系統新增至 `/etc/fstab` 。

    ``` bash
    blkid /dev/disk/azure/scsi1/lun4| awk -F\" '{print "UUID="$2" '${newmount}' "$4" defaults,nofail 0 0"}' >> /etc/fstab
    ```

8. 掛接新建立的檔案系統：

    ``` bash
    mount -a
    ```

9. 確認已裝載新的檔案系統：

    ``` bash
    df -h
    ```

    ![顯示驗證檔案系統是否已掛接之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df.png)

    ``` bash
    lsblk
    ```

    ![顯示額外程式碼的螢幕擷取畫面，以確認已掛接檔案系統。 結果會反白顯示。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk.png)

10. 重新開機您先前為數據磁片磁碟機啟動的加密。

    >[!TIP]
    >針對 LVM-crypt，我們建議您使用 `EncryptFormatAll` 。 否則，您可能會在設定其他磁片時看到雙重加密。
    >
    >如需詳細資訊，請參閱 [在加密裝置上設定 LVM 和 RAID](how-to-configure-lvm-raid-on-crypt.md)。

    以下為範例：

    ``` bash
    az vm encryption enable \
    --resource-group ${RGNAME} \
    --name ${VMNAME} \
    --disk-encryption-keyvault ${KEYVAULTNAME} \
    --key-encryption-key ${KEYNAME} \
    --key-encryption-keyvault ${KEYVAULTNAME} \
    --volume-type "DATA" \
    --encrypt-format-all \
    -o table
    ```

    加密完成時，您會在新加入的磁片上看到 crypt 圖層：

    ``` bash
    lsblk
    ```

    ![顯示檢查 crypt 圖層之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk2.png)

11. 卸載新磁片的加密層：

    ``` bash
    umount ${newmount}
    ```

12. 檢查目前的 PV 資訊：

    ``` bash
    pvs
    ```

    ![顯示檢查實體磁片區相關資訊之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-currentpvs.png)

13. 在磁片的加密層級上建立 PV。 從上一個命令取得裝置名稱 `lsblk` 。 `/dev/`在裝置名稱前方新增對應工具，以建立 PV：

    ``` bash
    pvcreate /dev/mapper/mapperdevicename
    ```

    ![顯示在加密層級上建立實體磁片區程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-pvcreate.png)

    您會看到關於抹除目前簽章的警告 `ext4 fs` 。 這是預期的警告。 使用來回答這個問題 `y` 。

14. 確認新的 PV 已新增至 LVM 設定：

    ``` bash
    pvs
    ```

    ![顯示驗證實體磁片區是否已新增至 LVM 設定之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-newpv.png)

15. 將新的 PV 新增至您需要增加的 VG。

    ``` bash
    vgextend vgname /dev/mapper/nameofhenewpv
    ```

    ![顯示將實體磁片區新增至磁片區群組之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgextent.png)

16. 確認 VG 的新大小和可用空間：

    ``` bash
    vgdisplay vgname
    ```

    ![顯示驗證磁片區群組大小和可用空間的程式碼螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgdisplay.png)

    請注意 `Total PE` 計數和的增加 `Free PE / Size` 。

17. 增加 LV 和檔案系統的大小。 使用 `-r` 上的選項 `lvextend` 。 在此範例中，我們會將 VG 中的總可用空間新增至指定的 LV。

    ``` bash
    lvextend -r -l +100%FREE /dev/vgname/lvname
    ```

    ![顯示增加本機磁片區和檔案系統大小之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvextend.png)

遵循後續步驟來驗證您的變更。

1. 確認 LV 的大小：

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![顯示驗證本機磁片區新大小之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvdisplay.png)

1. 確認檔案系統的新大小：

    ``` bash
    df -h mountpoint
    ```

    ![顯示驗證檔案系統新大小之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df1.png)

1. 確認 LVM 層位於加密層級之上：

    ``` bash
    lsblk
    ```

    ![螢幕擷取畫面，顯示驗證 LVM 層位於加密層上方的程式碼。 結果會反白顯示。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk3.png)

    如果您 `lsblk` 不使用任何選項，則會看到掛接點多次。 此命令會依裝置和 Lvs) 排序。 

    您可能會想要使用 `lsblk -fs` 。 在此命令中， `-fs` 反轉排序次序，使掛接點顯示一次。 磁片會顯示多次。

    ``` bash
    lsblk -fs
    ```

    ![顯示替代程式碼的螢幕擷取畫面，可驗證 LVM 層是否位於加密層的最上層。 結果會反白顯示。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk4.png)

#### <a name="extend-an-lvm-on-a-crypt-volume-by-resizing-an-existing-pv"></a>調整現有的 PV 以擴充 crypt 磁片區上的 LVM

1. 識別您的加密磁片：

    ``` bash
    lsblk
    ```

    ![顯示可識別加密磁片之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/039-resize-lvm-scenariof-lsblk01.png)

    ``` bash
    lsblk -s
    ```

    ![顯示用來識別已加密磁片之替代程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/040-resize-lvm-scenariof-lsblk012.png)

2. 檢查您的 PV 資訊：

    ``` bash
    pvs
    ```

    ![顯示檢查實體磁片區資訊之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/041-resize-lvm-scenariof-pvs.png)

3. 檢查您的 VG 資訊：

    ``` bash
    vgs
    ```

    ![顯示檢查磁片區群組資訊之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/042-resize-lvm-scenariof-vgs.png)

4. 檢查您的 LV 資訊：

    ``` bash
    lvs
    ```

    ![顯示檢查本機磁片區資訊之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/043-resize-lvm-scenariof-lvs.png)

5. 檢查檔案系統使用率：

    ``` bash
    df -h /mountpoint(s)
    ```

    ![顯示程式碼的螢幕擷取畫面，此程式碼會檢查使用的檔案系統數量。 結果會反白顯示。](./media/disk-encryption/resize-lvm/044-resize-lvm-scenariof-fs.png)

6. 檢查您磁片的大小：

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![顯示檢查磁片大小之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/045-resize-lvm-scenariof-fdisk01.png)

7. 調整資料磁片的大小。 您可以使用入口網站、CLI 或 PowerShell。 如需詳細資訊，請參閱 [擴充 LINUX VM 上的虛擬硬碟](expand-disks.md#expand-an-azure-managed-disk)中的磁片大小調整一節。 

    >[!IMPORTANT]
    >當 VM 正在執行時，您無法調整虛擬磁片的大小。 將您的 VM 解除配置，以進行此步驟。

8. 檢查您的磁片大小：

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![顯示檢查磁片大小之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/046-resize-lvm-scenariof-fdisk02.png)

    在此情況下，這兩個磁片的大小為 2 GB 到 4 GB。 但是檔案系統、LV 和 PV 的大小維持不變。

9. 檢查目前的 PV 大小。 請記住，在 crypt 上，PV 是 `/dev/mapper/` 裝置，而不是 `/dev/sd*` 裝置。

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![顯示檢查目前實體磁片區大小之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/047-resize-lvm-scenariof-pvs.png)

10. 調整 PV 的大小：

    ``` bash
    pvresize /dev/mapper/devicemappername
    ```

    ![顯示調整實體磁片區之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/048-resize-lvm-scenariof-resize-pv.png)

11. 檢查新的 PV 大小：

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![顯示檢查實體磁片區大小之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/049-resize-lvm-scenariof-pv.png)

12. 在 PV 上調整加密層：

    ``` bash
    cryptsetup resize /dev/mapper/devicemappername
    ```

    針對您要調整大小的所有磁片套用相同的程式。

13. 檢查您的 VG 資訊：

    ``` bash
    vgdisplay vgname
    ```

    ![顯示檢查磁片區群組資訊之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/050-resize-lvm-scenariof-vg.png)

    VG 現在有足夠的空間可配置給 Lvs) 。

14. 檢查 LV 資訊：

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![顯示檢查本機磁片區資訊之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/051-resize-lvm-scenariof-lv.png)

15. 檢查檔案系統使用率：

    ``` bash
    df -h /mountpoint
    ```

    ![顯示檢查檔案系統使用量之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/052-resize-lvm-scenariof-fs.png)

16. 調整 LV 的大小：

    ``` bash
    lvresize -r -L +2G /dev/vgname/lvname
    ```

    ![顯示調整本機磁片區大小之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/053-resize-lvm-scenariof-lvresize.png)

    在此，我們使用 `-r` 選項來調整檔案系統的大小。

17. 檢查 LV 資訊：

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![顯示可取得本機磁片區相關資訊之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/054-resize-lvm-scenariof-lvsize.png)

18. 檢查檔案系統使用率：

    ``` bash
    df -h /mountpoint
    ```

    ![顯示檢查檔案系統使用量之程式碼的螢幕擷取畫面。 結果會反白顯示。](./media/disk-encryption/resize-lvm/055-resize-lvm-scenariof-fs.png)

將相同的調整大小程式套用至任何其他需要它的 LV。

## <a name="next-steps"></a>後續步驟

[針對 Azure 磁碟加密進行疑難排解](disk-encryption-troubleshooting.md)