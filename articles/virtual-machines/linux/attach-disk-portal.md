---
title: 將資料磁碟連結至 Linux VM
description: 使用入口網站將新的或現有的資料磁碟附加至 Linux VM。
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 08/28/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 565b781b5015f82cafe4e47be2170f2327660821
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91971586"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>使用入口網站將資料磁碟附加至 Linux VM 
本文示範如何透過 Azure 入口網站將新的及現有的磁碟連結到 Linux 虛擬機器。 您也可以[在 Azure 入口網站中將資料磁碟連結到 Windows VM](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 

將磁碟附加至 VM 之前，請檢閱下列提示︰

* 虛擬機器的大小會控制您可以連接的資料磁碟數目。 如需詳細資訊，請參閱 [虛擬機器的大小](../sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 附加至虛擬機器的磁碟實際上是 Azure 中儲存的 .vhd 檔案。 如需詳細資料，請參閱我們的[受控磁碟簡介](../managed-disks-overview.md?toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json)。
* 連接磁碟之後，您必須[連線到 Linux VM 才能掛接新的磁碟](#connect-to-the-linux-vm-to-mount-the-new-disk)。


## <a name="find-the-virtual-machine"></a>尋找虛擬機器
1. 移至 [Azure 入口網站](https://portal.azure.com/)尋找 VM。 搜尋並選取 [虛擬機器]。
2. 從清單中選擇 VM。
3. 在 [ **虛擬機器** ] 頁面的 [ **設定**] 底下，選擇 [ **磁片**]。


## <a name="attach-a-new-disk"></a>附加新的磁碟

1. 在 [ **磁片** ] 窗格的 [ **資料磁片**] 底下，選取 [ **建立並連結新的磁片**]。

1. 輸入受控磁碟的名稱。 查看預設設定，並視需要更新 **儲存體類型**、 **大小 (GiB) **、 **加密** 和 **主機** 快取。
   
   :::image type="content" source="./media/attach-disk-portal/create-new-md.png" alt-text="檢查磁片設定。":::


1. 當您完成時，請選取頁面頂端的 [ **儲存** ]，以建立受控磁片並更新 VM 設定。


## <a name="attach-an-existing-disk"></a>連接現有磁碟
1. 在 [ **磁片** ] 窗格的 [ **資料磁片**] 底下，選取 [  **連接現有磁片**]。
1. 按一下 [ **磁片名稱** ] 的下拉式功能表，然後從可用的受控磁片清單中選取磁片。 

1. 按一下 [儲存] 以附加現有的受控磁碟並更新 VM 組態︰
   

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>連接到 Linux VM 以掛接新磁碟
若要分割、格式化和掛接新磁碟以供 Linux VM 使用，請使用 SSH 登入您的 VM。 如需詳細資訊，請參閱[如何在 Azure 上搭配使用 SSH 與 Linux](mac-create-ssh-keys.md)。 下列範例會使用 *10.123.123.25* 的公用 IP 位址和使用者名稱 *>azureuser*來連線至 VM： 

```bash
ssh azureuser@10.123.123.25
```

## <a name="find-the-disk"></a>尋找磁碟

連線至您的 VM 之後，您需要尋找磁片。 在此範例中，我們會使用 `lsblk` 來列出磁片。 

```bash
lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i "sd"
```

輸出類似於下列範例：

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0       4G
```

在此範例中，我新增的磁片是 `sdc` 。 它是 LUN 0，而且是4GB。

如需更複雜的範例，以下是在入口網站中的多個資料磁片外觀：

:::image type="content" source="./media/attach-disk-portal/create-new-md.png" alt-text="檢查磁片設定。":::

在映射中，您可以看到3個數據磁片： LUN 0 上的 4 GB、LUN 1 的 16 GB，以及位於 LUN 2 的32G。

以下是可能使用的樣子 `lsblk` ：

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0       4G
sdd     3:0:0:1      16G
sde     3:0:0:2      32G
```

從的輸出中， `lsblk` 您可以看到 lun 0 的 4 gb 磁片是 `sdc` ，位於 lun 1 的 16 gb 磁片是 `sdd` ，而 Lun 2 的32G 磁片是 `sde` 。

### <a name="partition-a-new-disk"></a>分割新的磁碟

如果您使用現有的磁碟，請略過此步驟直接裝載磁碟。 如果您要安裝新磁碟，您需要分割磁碟。

`parted`公用程式可以用來分割及格式化資料磁片。

> [!NOTE]
> 建議您使用可供發行版本使用的最新版本 `parted` 。
> 如果磁片大小是 2 tib (TiB) 或更大，您必須使用 GPT 磁碟分割。 如果磁片大小低於 2 TiB，您可以使用 MBR 或 GPT 磁碟分割。  


下列範例會使用 `parted` on `/dev/sdc` ，也就是第一個資料磁片通常會在大部分的 vm 上。 將取代為 `sdc` 您的磁片的正確選項。 我們也會使用 [XFS](https://xfs.wiki.kernel.org/) 檔案系統將它格式化。

```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
sudo mkfs.xfs /dev/sdc1
sudo partprobe /dev/sdc1
```

使用 [`partprobe`](https://linux.die.net/man/8/partprobe) 公用程式可確保核心知道新的磁碟分割和檔案系統。 若無法使用， `partprobe` 可能會導致 blkid 或 lslbk 命令無法立即傳回新檔案系統的 UUID。

### <a name="mount-the-disk"></a>裝載磁碟

使用 `mkdir` 建立用來掛接檔案系統的目錄。 下列範例會在下列位置建立目錄 `/datadrive` ：

```bash
sudo mkdir /datadrive
```

然後，使用 `mount` 掛接檔案系統。 下列範例會將 */dev/sdc1* 磁碟分割裝載至 `/datadrive` 掛接點：

```bash
sudo mount /dev/sdc1 /datadrive
```

為了確保重新開機之後自動重新掛接磁碟機，必須將磁碟機新增至 /etc/fstab 檔案。 此外，強烈建議您在 */etc/fstab* 中使用 (通用唯一識別碼) 的 UUID 來參考磁片磁碟機，而不只是裝置名稱 (例如 */dev/sdc1*) 。 如果作業系統在開機期間偵測到磁碟錯誤，使用 UUID 可避免將不正確的磁碟掛接到指定的位置。 其餘的資料磁碟則會被指派這些相同的裝置識別碼。 若要尋找新磁碟機的 UUID，請使用 `blkid` 公用程式：

```bash
sudo blkid
```

輸出大致如下列範例所示：

```bash
/dev/sda1: LABEL="cloudimg-rootfs" UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4" PARTUUID="1a1b1c1d-11aa-1234-1a1a1a1a1a1a"
/dev/sda15: LABEL="UEFI" UUID="BCD7-96A6" TYPE="vfat" PARTUUID="1e1g1cg1h-11aa-1234-1u1u1a1a1u1u"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4" TYPE="ext4" PARTUUID="1a2b3c4d-01"
/dev/sda14: PARTUUID="2e2g2cg2h-11aa-1234-1u1u1a1a1u1u"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="xfs" PARTLABEL="xfspart" PARTUUID="c1c2c3c4-1234-cdef-asdf3456ghjk"
```

> [!NOTE]
> 不當編輯 **/etc/fstab** 檔案會導致系統無法開機。 如果不確定，請參閱散發套件的文件，以取得如何適當編輯此檔案的相關資訊。 在編輯之前，也建議先備份 /etc/fstab 檔案。

接下來，在文字編輯器中開啟 /etc/fstab 檔案，如下所示：

```bash
sudo nano /etc/fstab
```

在此範例中，使用 `/dev/sdc1` 在先前步驟中建立之裝置的 UUID 值，以及的掛接點 `/datadrive` 。 將下行新增至 `/etc/fstab` 檔案結尾：

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   xfs   defaults,nofail   1   2
```

我們使用 nano 編輯器，因此當您完成編輯檔案時，請使用 `Ctrl+O` 寫入檔案並結束 `Ctrl+X` 編輯器。

> [!NOTE]
> 稍後移除資料磁碟而不編輯 fstab，可能會造成 VM 無法開機。 大多數的發行版本會提供 nofail 和 (或) nobootwait fstab 選項。 即使磁碟在開機時無法掛接，這些選項也能讓系統開機。 請查閱散發套件的文件，以取得這些參數的相關資訊。
> 
> *Nofail* 選項可確保即使檔案系統已損毀或磁碟在開機時並不存在，仍然會啟動 VM。 若不使用此選項，您可能會遇到[因為 FSTAB 錯誤所以無法 SSH 到 Linux VM](/archive/blogs/linuxonazure/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting) 中所述的行為


## <a name="verify-the-disk"></a>確認磁片

您現在可以 `lsblk` 再次使用它來查看磁片和掛接點。

```bash
lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i "sd"
```

輸出會看起來類似這樣：

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0       4G
└─sdc1                4G /datadrive
```

您可以看到 `sdc` 現在已裝載于 `/datadrive` 。

### <a name="trimunmap-support-for-linux-in-azure"></a>Azure 中 Linux 的 TRIM/UNMAP 支援

有些 Linux 核心會支援 TRIM/UNMAP 作業以捨棄磁碟上未使用的區塊。 此功能主要是在標準儲存體中相當實用，可用來通知 Azure 已刪除的頁面已不再有效而可予以捨棄，而且如果您建立大型檔案，然後再將它們刪除，也可以節省成本。

有兩種方式可在 Linux VM 中啟用 TRIM 支援。 像往常一樣，請參閱您的散發套件以了解建議的方法︰

* 在 /etc/fstab 中使用 `discard` 掛接選項，例如：

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   xfs   defaults,discard   1   2
    ```
* 在某些情況下，`discard` 選項可能會影響效能。 或者，您也可以從命令列手動執行 `fstrim` 命令，或將它新增到 crontab 來定期執行︰
  
    **Ubuntu**
  
    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```
  
    **RHEL/CentOS**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="next-steps"></a>後續步驟
您也可以使用 Azure CLI [附加資料磁碟](add-disk.md)。