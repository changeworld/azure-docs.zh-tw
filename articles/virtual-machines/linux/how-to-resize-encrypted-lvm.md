---
title: 如何使用 Azure 磁碟加密調整加密邏輯磁片區管理磁片的大小
description: 本文提供使用邏輯磁片區管理來調整 ADE 加密磁片大小的指示。
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 09/21/2020
ms.openlocfilehash: ba652b9424b8d5ce1b6a2c5b7d70b8fd9e999323
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91342349"
---
# <a name="how-to-resize-logical-volume-management-devices-encrypted-with-azure-disk-encryption"></a>如何調整以 Azure 磁碟加密加密的邏輯磁片區管理裝置大小

本文將逐步說明如何在 Linux 上使用邏輯磁片區管理 (LVM) 來調整 ADE 加密資料磁片的大小，適用于多個案例。

此程式適用于下列環境：

- Linux 散發套件
    - RHEL 7+
    - Ubuntu 16 +
    - SUSE 12 +
- Azure 磁碟加密單一傳遞延伸模組
- Azure 磁碟加密雙重傳遞延伸模組

## <a name="considerations"></a>考量

本檔假設：

1. 有現有的 LVM 設定。
   
   如需在 Linux VM 上設定 LVM 的詳細資訊，請參閱在 [LINUX vm 上設定 lvm](configure-lvm.md) 。

2. 磁片已使用 Azure 磁碟加密檢查 [設定 crypt](how-to-configure-lvm-raid-on-crypt.md) 上的 lvm，以取得如何設定 Lvm 內部 crypt 的資訊。

3. 您有必要的 Linux 和 LVM 專業知識，可以遵循這些範例。

4. 您已瞭解在 Azure 上使用資料磁片的建議，如針對 [裝置名稱問題進行疑難排解](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-device-names-problems)所述，是使用/dev/disk/scsi1/路徑。

## <a name="scenarios"></a>案例

本文中的程式適用于下列案例：

### <a name="for-traditional-lvm-and-lvm-on-crypt-configurations"></a>針對傳統 LVM 和 LVM 內部 Crypt 設定

- 在 VG 中有可用空間時，擴充邏輯磁片區

### <a name="for-traditional-lvm-encryption-the-logical-volumes-are-encrypted-not-the-whole-disk"></a>針對傳統 LVM 加密 (邏輯磁片區會加密，而不是整個磁片) 

- 擴充傳統 LVM 磁片區新增 PV
- 擴充傳統的 LVM 磁片區以調整現有的 PV

### <a name="for-lvm-on-crypt-recommended-method-the-entire-disk-is-encrypted-not-only-the-logical-volume"></a>針對 LVM crypt (建議的方法，會加密整個磁片，而不是只有邏輯磁片區) 

- 擴充 crypt 磁片區上的 LVM 新增 PV
- 擴充 crypt 磁片區上的 LVM 以調整現有的 PV

> [!NOTE]
> 不建議在相同的 VM 上混用傳統 LVM 加密和 LVM 內部 Crypt。

> [!NOTE]
> 這些範例會針對磁片、實體磁片區、磁片區群組、邏輯磁片區、檔案系統、Uuid 和掛接點使用既有的名稱，因此您需要取代這些範例所提供的值，以符合您的環境。

#### <a name="extending-a-logical-volume-when-theres-available-space-in-the-vg"></a>在 VG 中有可用空間時，擴充邏輯磁片區

傳統的方法可用來調整邏輯磁片區的大小，它可以套用至非加密磁片、傳統 lvm 加密磁片區和 LVM Crypt 設定。

1. 確認目前要增加的檔案系統大小：

    ``` bash
    df -h /mountpoint
    ```

    ![scenarioa-檢查-fs1](./media/disk-encryption/resize-lvm/001-resize-lvm-scenarioa-check-fs.png)

2. 確認 VG 有足夠的空間可增加 LV

    ``` bash
    vgs
    ```

    ![scenarioa-檢查-vg](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgs.png)

    您也可以使用 "vgdisplay"

    ``` bash
    vgdisplay vgname
    ```

    ![scenarioa-檢查-vgdisplay](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgdisplay.png)

3. 識別需要調整的邏輯磁片區大小

    ``` bash
    lsblk
    ```

    ![scenarioa-檢查-lsblk1](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk1.png)

    針對 LVM Crypt，此輸出上的差異在於，這會顯示加密層在涵蓋整個磁片的加密層上

    ![scenarioa-檢查-lsblk2](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk2.png)

4. 檢查邏輯磁片區大小

    ``` bash
    lvdisplay lvname
    ```

    ![scenarioa-檢查-lvdisplay01](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lvdisplay01.png)

5. 使用 "-r" 針對檔案系統的線上調整大小來增加 LV 大小

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![scenarioa-調整大小-lv](./media/disk-encryption/resize-lvm/003-resize-lvm-scenarioa-resize-lv.png)

6. 確認 LV 和檔案系統的新大小

    ``` bash
    df -h /mountpoint
    ```

    ![scenarioa-檢查-fs](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-fs.png)

    新的大小會反映出來，這表示已成功重設 LV 和檔案系統的大小

7. 您可以再次檢查 LV 資訊，以確認 LV 層級的變更

    ``` bash
    lvdisplay lvname
    ```

    ![scenarioa-檢查-lvdisplay2](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-lvdisplay2.png)

#### <a name="extending-a-traditional-lvm-volume-adding-a-new-pv"></a>擴充傳統 LVM 磁片區新增 PV

適用于您需要新增磁片以增加磁片區群組大小時。

1. 確認目前要增加的檔案系統大小：

    ``` bash
    df -h /mountpoint
    ```

    ![scenariob-檢查-fs](./media/disk-encryption/resize-lvm/005-resize-lvm-scenariob-check-fs.png)

2. 確認目前的實體磁片區設定

    ``` bash
    pvs
    ```

    ![scenariob-檢查-pvs](./media/disk-encryption/resize-lvm/006-resize-lvm-scenariob-check-pvs.png)

3. 檢查目前的 VG 資訊

    ``` bash
    vgs
    ```

    ![scenariob-檢查-vgs](./media/disk-encryption/resize-lvm/007-resize-lvm-scenariob-check-vgs.png)

4. 檢查目前的磁片清單

    您應藉由檢查/dev/disk/azure/scsi1/下的裝置來識別資料磁片

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![scenariob-檢查-scs1](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-scs1.png)

5. 檢查 lsblk 的輸出 

    ``` bash
    lsbk
    ```

    ![scenariob-檢查-lsblk](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-lsblk.png)

6. 將新磁片連結至 VM

    追蹤下列檔的步驟4

   - [將磁片連結至 VM](attach-disk-portal.md)

7. 連接磁片之後，請檢查磁片清單，並注意新的磁片

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![scenariob-檢查-scsi12](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-scsi12.png)

    ``` bash
    lsbk
    ```

    ![scenariob-檢查-lsblk12](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-lsblk1.png)

8. 在新的資料磁片之上建立新的 PV

    ``` bash
    pvcreate /dev/newdisk
    ```

    ![scenariob-pvcreate](./media/disk-encryption/resize-lvm/010-resize-lvm-scenariob-pvcreate.png)

    這個方法會使用整個磁片做為沒有分割區的 PV，選擇性地使用 "fdisk" 來建立資料分割，然後針對 "pvcreate" 使用該分割區。

9. 確認 PV 已新增至 PV 清單。

    ``` bash
    pvs
    ```

    ![scenariob-檢查-pvs1](./media/disk-encryption/resize-lvm/011-resize-lvm-scenariob-check-pvs1.png)

10. 藉由新增新的 PV 來擴充 VG

    ``` bash
    vgextend vgname /dev/newdisk
    ```

    ![scenariob-vg-擴充](./media/disk-encryption/resize-lvm/012-resize-lvm-scenariob-vgextend.png)

11. 檢查新的 VG 大小

    ``` bash
    vgs
    ```

    ![scenariob-檢查-vgs1](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-vgs1.png)

12. 使用 lsblk 來識別需要調整大小的 LV

    ``` bash
    lsblk
    ```

    ![scenariob-檢查-lsblk1](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-lsblk1.png)

13. 使用 "-r" 擴充 LV 大小以線上增加檔案系統

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![scenariob-lvextend](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-lvextend.png) 

14. 驗證新的 LV 和 filesystem 大小

    ``` bash
    df -h /mountpoint
    ```

    ![scenariob-檢查-fs1](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-fs1.png)

    請務必瞭解當 ADE 用於傳統 LVM 設定時，加密層是在 LV 層級建立，而不是在磁片層級建立。

    此時，加密層會擴充到新的磁片。
    實際的資料磁片在平台層級沒有任何加密設定，因此其加密狀態不會更新。

    >[!NOTE]
    >這些是 Crypt LVM 的一些原因，是建議的方法。 

15. 從入口網站檢查加密資訊：

    ![scenariob-檢查-portal1](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal1.png)

    您必須加入新的 LV 並在 VM 上啟用擴充功能，才能更新磁片上的加密設定。
    
16. 新增 LV、在其上建立檔案系統，並將它新增至/etc/fstab

17. 再次設定加密擴充功能，以在平台層級的新資料磁片上戳記加密設定。

    範例：

    CLI

    ``` bash
    az vm encryption enable -g ${RGNAME} --name ${VMNAME} --disk-encryption-keyvault "<your-unique-keyvault-name>"
    ```

18. 從入口網站檢查加密資訊：

    ![scenariob-檢查-portal2](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal2.png)

19. 更新加密設定之後，您可以刪除新的 LV，您也必須從為它建立的/etc/fstab 和/etc/crypttab 中刪除專案。

    ![scenariob-delete-fstab-crypttab](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-delete-fstab-crypttab.png)

20. 卸載邏輯磁片區

    ``` bash
    umount /mountpoint
    ```

21. 關閉磁片區的加密層

    ``` bash
    cryptsetup luksClose /dev/vgname/lvname
    ```

22. 刪除 LV

    ``` bash
    lvremove /dev/vgname/lvname
    ```

#### <a name="extending-a-traditional-lvm-volume-resizing-an-existing-pv"></a>擴充傳統的 LVM 磁片區以調整現有的 PV

某些案例或限制需要您調整現有磁片的大小。

1. 識別您的加密磁片

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![scenarioc-檢查-scsi1](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-scsi1.png)

    ``` bash
    lsblk -fs
    ```

    ![scenarioc-檢查-lsblk](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-lsblk.png)

2. 檢查 pv 資訊

    ``` bash
    pvs
    ```

    ![scenarioc-檢查-pvs](./media/disk-encryption/resize-lvm/016-resize-lvm-scenarioc-check-pvs.png)

    目前使用的所有 PVs 上的所有空間

3. 檢查 VGs 資訊

    ``` bash
    vgs
    vgdisplay -v vgname
    ```

    ![scenarioc-檢查-vgs](./media/disk-encryption/resize-lvm/017-resize-lvm-scenarioc-check-vgs.png)

4. 檢查磁片大小，您可以使用 fdisk 或 lsblk 來列出磁片磁碟機大小

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![scenarioc 檢查-fdisk](./media/disk-encryption/resize-lvm/018-resize-lvm-scenarioc-check-fdisk.png)

    我們已識別出哪些 PVs 與使用 lsblk-fs 的 Lvs) 相關聯，您也可以藉由執行 "lvdisplay" 來加以識別。

    ``` bash
    lvdisplay --maps VG/LV
    lvdisplay --maps datavg/datalv1
    ```

    ![檢查-lvdisplay](./media/disk-encryption/resize-lvm/019-resize-lvm-scenarioc-check-lvdisplay.png)

    在此情況下，所有4個數據磁片磁碟機都屬於相同 VG 和單一 LV 的一部分，您的設定可能會與此範例不同。

5. 檢查目前的檔案系統使用量：

    ``` bash
    df -h /datalvm*
    ```

    ![scenarioc-檢查-df](./media/disk-encryption/resize-lvm/020-resize-lvm-scenarioc-check-df.png)

6. 調整資料磁片的大小 (s) ：

    您可以參考 [Linux 擴充磁片](expand-disks.md) (只需要參考磁片調整大小) ，您可以使用入口網站、CLI 或 PowerShell 來執行此步驟。

    >[!NOTE]
    >請考慮在執行的 VM 上無法執行虛擬磁片上的調整大小作業。 您必須將 VM 解除配置，才能進行此步驟

7. 當磁片調整大小為所需的值時，請使用 fdisk 啟動 VM 並檢查新的大小

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![scenarioc-檢查-fdisk1](./media/disk-encryption/resize-lvm/021-resize-lvm-scenarioc-check-fdisk1.png)

    在此特定案例中，/dev/sdd 已從5G 調整為20G

8. 檢查目前的 PV 大小

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![scenarioc-檢查-pvdisplay](./media/disk-encryption/resize-lvm/022-resize-lvm-scenarioc-check-pvdisplay.png)
    
    即使磁片已調整大小，pv 仍具有先前的大小。

9. 調整 PV 的大小

    ``` bash
    pvresize /dev/resizeddisk
    ```

    ![scenarioc-檢查-pvresize](./media/disk-encryption/resize-lvm/023-resize-lvm-scenarioc-check-pvresize.png)


10. 檢查 PV 大小

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![scenarioc-檢查-pvdisplay1](./media/disk-encryption/resize-lvm/024-resize-lvm-scenarioc-check-pvdisplay1.png)

    針對您要調整大小的所有磁片套用相同的程式。

11. 檢查 VG 資訊

    ``` bash
    vgdisplay vgname
    ```

    ![scenarioc-檢查-vgdisplay1](./media/disk-encryption/resize-lvm/025-resize-lvm-scenarioc-check-vgdisplay1.png)

    VG 現在有空間可配置給 Lvs) 

12. 調整 LV 的大小

    ``` bash
    lvresize -r -L +5G vgname/lvname
    lvresize -r -l +100%FREE /dev/datavg/datalv01
    ```

    ![scenarioc-檢查-lvresize1](./media/disk-encryption/resize-lvm/031-resize-lvm-scenarioc-check-lvresize1.png)

13. 檢查 FS 大小

    ``` bash
    df -h /datalvm2
    ```

    ![scenarioc-檢查-df3](./media/disk-encryption/resize-lvm/032-resize-lvm-scenarioc-check-df3.png)

#### <a name="extending-an-lvm-on-crypt-volume-adding-a-new-pv"></a>擴充 LVM 內部 Crypt 磁片區新增 PV

此方法會嚴格遵循在 [crypt 上設定 LVM](how-to-configure-lvm-raid-on-crypt.md) 來新增磁片的步驟，並在 crypt 設定下進行設定。

您可以使用這個方法將空間新增至已存在的 LV，或改為建立新的 VGs 或 Lvs) 。

1. 確認 VG 的目前大小

    ``` bash
    vgdisplay vgname
    ```

    ![scenarioe-檢查-vg01](./media/disk-encryption/resize-lvm/033-resize-lvm-scenarioe-check-vg01.png)

2. 確認您想要增加的 fs 和 lv 的大小

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![scenarioe-檢查-lv01](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-lv01.png)

    ``` bash
    df -h mountpoint
    ```

    ![scenarioe-檢查-fs01](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-fs01.png)

3. 將新的資料磁片新增至 VM，並加以識別。

    請先檢查磁片再新增磁片

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![scenarioe-檢查-newdisk01](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk01.png)

    請先檢查磁片再新增磁片

    ``` bash
    lsblk
    ```

    ![scenarioe-檢查-newdisk002](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk02.png)

    使用 PowerShell、Azure CLI 或 Azure 入口網站來新增磁片。 查看如何在將磁片新增至 VM 時， [連接磁片](attach-disk-portal.md) 以供參考。

    在裝置的核心名稱配置之後，新的磁片磁碟機通常會被指派下一個可用的字母，在此特定案例中，會 sdd 新新增的磁片。

4. 新增新磁片之後，請檢查磁片

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![scenarioe-檢查-newdisk02](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk02.png)-

    ``` bash
    lsblk
    ```

    ![scenarioe-檢查-newdisk003](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk03.png)

5. 在最近新增的磁片上建立檔案系統

    在/dev/disk/azure/scsi1/上將最近新增的磁片與連結的裝置進行比對

    ``` bash
    ls -la /dev/disk/azure/scsi1/
    ```

    ![scenarioe-檢查-newdisk03](./media/disk-encryption/resize-lvm/037-resize-lvm-scenarioe-check-newdisk03.png)

    ``` bash
    mkfs.ext4 /dev/disk/azure/scsi1/${disk}
    ```

    ![scenarioe-mkfs01](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-mkfs01.png)

6. 為新新增的磁片建立新的暫存掛接點

    ``` bash
    newmount=/data4
    mkdir ${newmount}
    ```

7. 將最近建立的檔案系統新增至/etc/fstab

    ``` bash
    blkid /dev/disk/azure/scsi1/lun4| awk -F\" '{print "UUID="$2" '${newmount}' "$4" defaults,nofail 0 0"}' >> /etc/fstab
    ```

8. 使用掛接掛接新建立的 fs-a

    ``` bash
    mount -a
    ```

9. 確認已裝載新增的 FS

    ``` bash
    df -h
    ```

    ![調整大小-lvm-scenarioe-df](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df.png)

    ``` bash
    lsblk
    ```

    ![調整大小-lvm-scenarioe-lsblk](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk.png)

10. 重新開機先前為數據磁片磁碟機啟動的加密

    針對 LVM Crypt，建議使用 EncryptFormatAll，否則在設定其他磁片時可能會發生雙重加密。

    如需使用方式的詳細資訊，請參閱 [在 crypt 上設定 LVM](how-to-configure-lvm-raid-on-crypt.md)。

    範例：

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

    加密完成後，您會在新加入的磁片上看到 crypt 圖層。

    ``` bash
    lsblk
    ```

    ![scenarioe-lsblk2](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk2.png)

11. 卸載新磁片的加密層

    ``` bash
    umount ${newmount}
    ```

12. 檢查目前的 pvs 資訊

    ``` bash
    pvs
    ```

    ![scenarioe-currentpvs](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-currentpvs.png)

13. 在磁片的加密層級上建立 PV

    從先前的 lsblk 命令抓取裝置名稱，並在裝置名稱前面新增/dev/mapper 以建立 pv

    ``` bash
    pvcreate /dev/mapper/mapperdevicename
    ```

    ![scenarioe-pvcreate](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-pvcreate.png)

    您將會看到關於抹除目前 ext4 fs 簽章的警告，這是預期的答案，此問題的答案為 y

14. 確認新的 PV 已新增至 lvm 設定

    ``` bash
    pvs
    ```

    ![scenarioe-newpv](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-newpv.png)

15. 將新的 PV 新增至您需要增加的 VG

    ``` bash
    vgextend vgname /dev/mapper/nameofhenewpv
    ```

    ![scenarioe-vgextent](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgextent.png)

16. 確認 VG 的新大小和可用空間

    ``` bash
    vgdisplay vgname
    ```

    ![scenarioe-vgdisplay](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgdisplay.png)

    請注意，總 PE 計數和可用 PE/大小的增加

17. 使用 lvextend 上的-r 選項來增加 lv 和檔案系統的大小 (在此範例中，我們會取得 VG 中的總可用空間，並將其新增至指定的邏輯磁片區) 

    ``` bash
    lvextend -r -l +100%FREE /dev/vgname/lvname
    ```

    ![scenarioe-lvextend](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvextend.png)

18. 確認 LV 的大小

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![scenarioe-lvdisplay](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvdisplay.png)

19. 確認您剛剛調整大小的檔案系統大小

    ``` bash
    df -h mountpoint
    ```

    ![scenarioe-df1](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df1.png)

20. 確認 LVM 層是在加密層級上建立的

    ``` bash
    lsblk
    ```

    ![scenarioe-lsblk3](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk3.png)

    使用沒有選項的 lsblk 會顯示掛接點多次，因為它會依裝置和邏輯磁片區排序，因此您可能會想要使用 lsblk-fs，-s 會反轉排序，因此掛接點會顯示一次，然後會多次顯示磁片。

    ``` bash
    lsblk -fs
    ```

    ![scenarioe-lsblk4](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk4.png)

#### <a name="extending-an-lvm-on-crypt-volume-resizing-an-existing-pv"></a>擴充 crypt 磁片區上的 LVM 以調整現有的 PV

1. 識別您的加密磁片

    ``` bash
    lsblk
    ```

    ![scenariof-lsblk01](./media/disk-encryption/resize-lvm/039-resize-lvm-scenariof-lsblk01.png)

    ``` bash
    lsblk -s
    ```

    ![scenariof-lsblk012](./media/disk-encryption/resize-lvm/040-resize-lvm-scenariof-lsblk012.png)

2. 檢查您的 pv 資訊

    ``` bash
    pvs
    ```

    ![scenariof-pvs1](./media/disk-encryption/resize-lvm/041-resize-lvm-scenariof-pvs.png)

3. 檢查您的 vg 資訊

    ``` bash
    vgs
    ```

    ![scenariof-vgs](./media/disk-encryption/resize-lvm/042-resize-lvm-scenariof-vgs.png)

4. 檢查您的 lv 資訊

    ``` bash
    lvs
    ```

    ![scenariof-lvs) ](./media/disk-encryption/resize-lvm/043-resize-lvm-scenariof-lvs.png)

5. 檢查檔案系統使用量

    ``` bash
    df -h /mountpoint(s)
    ```

    ![lvm-scenariof-fs](./media/disk-encryption/resize-lvm/044-resize-lvm-scenariof-fs.png)

6. 檢查您的磁片大小

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![scenariof-fdisk01](./media/disk-encryption/resize-lvm/045-resize-lvm-scenariof-fdisk01.png)

7. 調整資料磁碟的大小

    您可以參考 [Linux 擴充磁片](expand-disks.md) (只需要參考磁片調整大小) ，您可以使用入口網站、CLI 或 PowerShell 來執行此步驟。

    >[!NOTE]
    >請考慮在執行的 VM 上無法執行虛擬磁片上的調整大小作業。 您必須將 VM 解除配置，才能進行此步驟

8. 檢查您的磁片大小

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![scenariof-fdisk02](./media/disk-encryption/resize-lvm/046-resize-lvm-scenariof-fdisk02.png)

    請注意，在此案例中 () 兩個磁片的大小調整為 2 gb，但 FS、LV 和 PV 大小維持不變。

9. 檢查目前的 pv 大小

    請記住，在 Crypt 上，pv 是/dev/mapper/裝置，而不是/dev/sd * 裝置

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![scenariof-pvs](./media/disk-encryption/resize-lvm/047-resize-lvm-scenariof-pvs.png)

10. 調整 pv 的大小

    ``` bash
    pvresize /dev/mapper/devicemappername
    ```

    ![scenariof-調整大小-pv](./media/disk-encryption/resize-lvm/048-resize-lvm-scenariof-resize-pv.png)

11. 在調整大小後檢查 pv 大小

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![scenariof-pv](./media/disk-encryption/resize-lvm/049-resize-lvm-scenariof-pv.png)

12. 在 PV 上調整加密層的大小

    ``` bash
    cryptsetup resize /dev/mapper/devicemappername
    ```

    針對您要調整大小的所有磁片套用相同的程式。

13. 檢查您的 vg 資訊

    ``` bash
    vgdisplay vgname
    ```

    ![scenariof-vg](./media/disk-encryption/resize-lvm/050-resize-lvm-scenariof-vg.png)

    VG 現在有空間可配置給 Lvs) 

14. 檢查 lv 資訊

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![scenariof-lv](./media/disk-encryption/resize-lvm/051-resize-lvm-scenariof-lv.png)

15. 檢查 fs 使用率

    ``` bash
    df -h /mountpoint
    ```

    ![scenariof-fs](./media/disk-encryption/resize-lvm/052-resize-lvm-scenariof-fs.png)

16. 調整 lv 的大小

    ``` bash
    lvresize -r -L +2G /dev/vgname/lvname
    ```

    ![scenariof-lvresize](./media/disk-encryption/resize-lvm/053-resize-lvm-scenariof-lvresize.png)

    我們使用-r 選項也可以執行 FS 調整大小

17. 檢查 lv 資訊

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![scenariof-lvsize](./media/disk-encryption/resize-lvm/054-resize-lvm-scenariof-lvsize.png)

18. 檢查檔案系統使用量

    ``` bash
    df -h /mountpoint
    ```

    ![建立檔案系統](./media/disk-encryption/resize-lvm/055-resize-lvm-scenariof-fs.png)

    將相同的調整大小程式套用至任何其他需要它的 lv

## <a name="next-steps"></a>後續步驟

- [Azure 磁碟加密的疑難排解](disk-encryption-troubleshooting.md)
