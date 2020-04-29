---
title: 在執行 Linux 的虛擬機器上設定 LVM
description: 了解如何在 Azure 中的 Linux 上設定 LVM
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 09/27/2018
ms.author: guybo
ms.subservice: disks
ms.openlocfilehash: 7f560a1e6266b5f2452bf9442d2d4c983de1236e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80066803"
---
# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>設定 Azure 中 Linux VM 的 LVM
本文將討論如何在 Azure 虛擬機器中設定邏輯磁碟區管理員 (LVM)。 LVM 可能會根據 OS 磁碟或 Azure VM 中的資料磁碟來使用，不過，根據預設，大部分的雲端映像將不會在 OS 磁碟上設定 LVM。 下列步驟將著重於如何針對您的資料磁碟設定 LVM。

## <a name="linear-vs-striped-logical-volumes"></a>線性與等量邏輯磁碟區
LVM 可以用來將數個實體磁碟的結合成單一存放磁碟區。 根據預設 LVM 通常會建立線性邏輯磁碟區，這表示實體儲存體是串連在一起。 在此情況下，讀取/寫入作業通常只會傳送至單一磁碟。 相反地，我們也可以建立等量邏輯磁碟區，將讀取和寫入分散到磁碟區群組 (類似 RAID0) 中的多個磁碟。 基於效能考量，您可能希望建立等量邏輯磁碟區，如此一來，讀取和寫入就能利用您所有已連結的資料磁碟。

本文件將說明如何將數個資料磁碟結合成單一磁碟區群組，然後再建立等量邏輯磁碟區。 下列為一般性步驟，適用於大部分的發行版本。 在大部分情況下，Azure 上用於管理 LVM 的公用程式和工作流程，與其他環境中的基本上都相同。 像往常一樣，也請向您的 Linux 廠商洽詢搭配特定發行版本使用 LVM 的文件和最佳做法。

## <a name="attaching-data-disks"></a>連接資料磁碟
使用 LVM 時，通常一開始會用二個或更多的空資料磁碟。 根據 IO 需求，您可以選擇連接儲存在標準儲存體且一個磁碟最多具有 500 IO/ps 的磁碟，或進階儲存體且一個磁碟最多具有 5000 IO/ps 的磁碟。 本文將不會詳細說明如何佈建資料磁碟以及將其連接至 Linux 虛擬機器。 請參閱 Microsoft Azure 文章[連接磁碟](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)，取得如何在 Azure 上將空白資料磁碟連接至 Linux 虛擬機器的詳細指示。

## <a name="install-the-lvm-utilities"></a>安裝 LVM 公用程式
* **Ubuntu**

    ```bash  
    sudo apt-get update
    sudo apt-get install lvm2
    ```

* **RHEL、CentOS 和 Oracle Linux**

    ```bash   
    sudo yum install lvm2
    ```

* **SLES 12 和 openSUSE**

    ```bash   
    sudo zypper install lvm2
    ```

* **SLES 11**

    ```bash   
    sudo zypper install lvm2
    ```

    在 SLES11 上，您也必須編輯 `/etc/sysconfig/lvm` 並將 `LVM_ACTIVATED_ON_DISCOVERED` 設為 "enable"：

    ```sh   
    LVM_ACTIVATED_ON_DISCOVERED="enable" 
    ```

## <a name="configure-lvm"></a>設定 LVM
本指南假設您已經連接三個資料磁碟，我們會以 `/dev/sdc`、`/dev/sdd` 和 `/dev/sde` 來代表。 這些路徑可能不符合您 VM 中的磁碟路徑名稱。 您可以執行 '`sudo fdisk -l`' 或類似的命令以列出可用的磁碟。

1. 準備實體磁碟區：

    ```bash    
    sudo pvcreate /dev/sd[cde]
    Physical volume "/dev/sdc" successfully created
    Physical volume "/dev/sdd" successfully created
    Physical volume "/dev/sde" successfully created
    ```

2. 建立磁碟區群組。 我們在此範例中呼叫磁碟區群組 `data-vg01`：

    ```bash    
    sudo vgcreate data-vg01 /dev/sd[cde]
    Volume group "data-vg01" successfully created
    ```

3. 建立一或多個邏輯磁碟區。 以下命令會建立名為 `data-lv01` 的單一邏輯磁碟區，以橫跨整個磁碟區群組，但是請留意，在磁碟區群組中建立多個邏輯磁碟區也是可行的。

    ```bash   
    sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
    Logical volume "data-lv01" created.
    ```

4. 格式化邏輯磁碟區

    ```bash  
    sudo mkfs -t ext4 /dev/data-vg01/data-lv01
    ```
   
   > [!NOTE]
   > SLES11 請使用 `-t ext3` 而不是 ext4。 SLES11 對於 ext4 檔案系統只支援唯讀權限。

## <a name="add-the-new-file-system-to-etcfstab"></a>將新的檔案系統新增至 /etc/fstab
> [!IMPORTANT]
> 不當編輯 `/etc/fstab` 檔案會導致系統無法開機。 如果不確定，請參閱散發套件的文件，以取得如何適當編輯此檔案的相關資訊。 在編輯之前，也建議先備份 `/etc/fstab` 檔案。

1. 建立新檔案系統所需的掛接點，例如：

    ```bash  
    sudo mkdir /data
    ```

2. 找出邏輯磁碟區的路徑

    ```bash    
    lvdisplay
    --- Logical volume ---
    LV Path                /dev/data-vg01/data-lv01
    ....
    ```

3. 在文字編輯器中開啟 `/etc/fstab`，並為新檔案系統新增項目，例如：

    ```bash    
    /dev/data-vg01/data-lv01  /data  ext4  defaults  0  2
    ```   
    然後，儲存並關閉 `/etc/fstab`。

4. 測試 `/etc/fstab` 項目是否正確：

    ```bash    
    sudo mount -a
    ```

    如果此命令會產生錯誤訊息，請檢查 `/etc/fstab` 檔案中的語法。
   
    接下來，執行 `mount` 命令，以確保已掛接檔案系統：

    ```bash    
    mount
    ......
    /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)
    ```

5. (選擇性) 保全 `/etc/fstab`中的開機參數
   
    許多散發套件包含 `nobootwait` 或 `nofail` 掛接參數，可加入至 `/etc/fstab` 檔案。 這些參數容許發生掛接特定檔案系統失敗，並容許 Linux 系統繼續開機，即使它無法正確地掛接 RAID 檔案系統。 請參閱散發套件的文件，以取得這些參數的相關資訊。
   
    範例 (Ubuntu)：

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
    ```

## <a name="trimunmap-support"></a>TRIM/UNMAP 支援
有些 Linux 核心會支援 TRIM/UNMAP 作業以捨棄磁碟上未使用的區塊。 這些作業主要是在標準儲存體中相當實用，可用來通知 Azure 已刪除的頁面已不再有效而可予以捨棄。 如果您建立大型檔案，然後再將它們刪除，捨棄頁面可以節省成本。

有兩種方式可在 Linux VM 中啟用 TRIM 支援。 像往常一樣，請參閱您的散發套件以了解建議的方法︰

- 在 `/etc/fstab` 中使用 `discard` 掛接選項，例如：

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,discard  0  2
    ```

- 在某些情況下，`discard` 選項可能會影響效能。 或者，您也可以從命令列手動執行 `fstrim` 命令，或將它新增到 crontab 來定期執行︰

    **Ubuntu**

    ```bash 
    # sudo apt-get install util-linux
    # sudo fstrim /datadrive
    ```

    **RHEL、CentOS 和 Oracle Linux**

    ```bash 
    # sudo yum install util-linux
    # sudo fstrim /datadrive
    ```
