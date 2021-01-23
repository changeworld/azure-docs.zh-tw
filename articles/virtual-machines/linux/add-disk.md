---
title: 使用 Azure CLI 將資料磁片新增至 Linux VM
description: 了解如何使用 Azure CLI 在 Linux VM 中新增永續性資料磁碟
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 08/20/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 1155b4274b97f540fd97bf39e51fd41c37bc9627
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730616"
---
# <a name="add-a-disk-to-a-linux-vm"></a>在 Linux VM 中新增磁碟

本文說明如何將持續性磁碟連結到您的 VM，以便您保留資料 - 即使您的 VM 會由於維護或調整大小而重新佈建。


## <a name="attach-a-new-disk-to-a-vm"></a>將新磁碟附加至 VM

如果您想要在 VM 上新增空的新資料磁碟，請使用 [az vm disk attach](/cli/azure/vm/disk) 命令並搭配 `--new` 參數。 如果您的 VM 位於可用性區域，系統會自動在與 VM 相同的區域中建立磁碟。 如需詳細資訊，請參閱[可用性區域概觀](../../availability-zones/az-overview.md)。 下列範例會建立名為 *myDataDisk* 且大小為 50 GB 的磁碟：

```azurecli
az vm disk attach \
   -g myResourceGroup \
   --vm-name myVM \
   --name myDataDisk \
   --new \
   --size-gb 50
```

## <a name="attach-an-existing-disk"></a>連接現有磁碟

若要連結現有磁碟，請找出磁碟識別碼，並將識別碼傳遞到 [az vm disk attach](/cli/azure/vm/disk) 命令。 下列範例會查詢 myResourceGroup 中名為 myDataDisk 的磁碟，然後將其連結至名為 myVM 的 VM：

```azurecli
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)

az vm disk attach -g myResourceGroup --vm-name myVM --name $diskId
```

## <a name="format-and-mount-the-disk"></a>格式化和掛接磁片

若要分割、格式化和掛接新磁碟以供 Linux VM 使用，請使用 SSH 登入您的 VM。 如需詳細資訊，請參閱[如何在 Azure 上搭配使用 SSH 與 Linux](mac-create-ssh-keys.md)。 下列範例會使用 *10.123.123.25* 的公用 IP 位址和使用者名稱 *>azureuser* 來連線至 VM：

```bash
ssh azureuser@10.123.123.25
```

### <a name="find-the-disk"></a>尋找磁碟

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
sdc     3:0:0:0      50G
```

這 `sdc` 是我們想要的磁片，因為它是50G 的。 如果您不確定哪一個磁片是以單獨的大小為基礎，您可以移至入口網站中的 [VM] 頁面，選取 [ **磁片**]，然後檢查 [ **資料磁片**] 下之磁片的 LUN 編號。 


### <a name="format-the-disk"></a>格式化磁片

將磁片格式化 `parted` ，如果磁片大小為 2 tib (TiB) 或更大，則您必須使用 GPT 磁碟分割（如果它位於2TiB 下），然後您就可以使用 MBR 或 GPT 磁碟分割。 

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

現在，使用 `mkdir` 建立用來掛接檔案系統的目錄。 下列範例會在下列位置建立目錄 `/datadrive` ：

```bash
sudo mkdir /datadrive
```

然後，使用 `mount` 掛接檔案系統。 下列範例會將 `/dev/sdc1` 磁碟分割裝載至 `/datadrive` 掛接點：

```bash
sudo mount /dev/sdc1 /datadrive
```

### <a name="persist-the-mount"></a>保存掛接

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

在此範例中，我們使用 nano 編輯器，因此當您完成編輯檔案時，請使用 `Ctrl+O` 寫入檔案並結束 `Ctrl+X` 編輯器。

> [!NOTE]
> 稍後移除資料磁碟而不編輯 fstab，可能會造成 VM 無法開機。 大多數的發行版本會提供 nofail 和 (或) nobootwait fstab 選項。 即使磁碟在開機時無法掛接，這些選項也能讓系統開機。 請查閱散發套件的文件，以取得這些參數的相關資訊。
>
> *Nofail* 選項可確保即使檔案系統已損毀或磁碟在開機時並不存在，仍然會啟動 VM。 若不使用此選項，您可能會遇到[因為 FSTAB 錯誤所以無法 SSH 到 Linux VM](/archive/blogs/linuxonazure/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting) 中所述的行為
>
> 如果修改 fstab 導致開機失敗，則可以使用 Azure VM 序列主控台來存取 VM 的主控台。 [序列主控台檔](../troubleshooting/serial-console-linux.md)提供更多詳細資料。

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

## <a name="troubleshooting"></a>疑難排解

[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>後續步驟

* 若要確保您的 Linux VM 已正確設定，請檢閱 [最佳化您的 Linux 機器效能](/previous-versions/azure/virtual-machines/linux/optimization) 建議。
* 新增其他磁碟以擴充儲存體容量，並 [設定 RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) 以提升效能。
