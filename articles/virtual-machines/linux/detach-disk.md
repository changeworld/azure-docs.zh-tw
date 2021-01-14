---
title: 從 Linux VM 卸離資料磁碟
description: 了解如何使用 Azure CLI 或 Azure 入口網站，從 Azure 中的虛擬機器中斷資料磁碟連結。
author: roygara
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 07/18/2018
ms.author: rogarana
ms.subservice: disks
ms.custom: devx-track-azurecli
ms.openlocfilehash: 96586be8be466acf09121518fb71ea1b8ba9d983
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98203196"
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>如何從 Linux 虛擬機器中斷資料磁碟連結

當不再需要某個連接至虛擬機器的資料磁碟時，卸離此資料磁碟很簡單。 這會將磁碟從虛擬機器中卸離，但這不會將它從儲存體中移除。 在本文中，我們會使用 Ubuntu LTS 16.04 散發套件。 如果您使用不同的散發套件，取消掛接磁碟的指示可能不同。

> [!WARNING]
> 將磁碟中斷連結時，並不會自動將它刪除。 如果您已訂閱「進階」儲存體，您將會繼續因該磁碟而導致產生儲存體費用。 如需詳細資訊，請參閱[使用進階儲存體時的定價和計費](https://azure.microsoft.com/pricing/details/storage/page-blobs/)。

如果您想要再次使用磁碟上現有的資料，您可以將磁碟重新連接至相同或其他虛擬機器。  


## <a name="connect-to-the-vm-to-unmount-the-disk"></a>連線至 VM，以將磁碟取消掛接

您必須先將磁碟取消掛接並從 fstab 檔案中移除其參考，才可以使用 CLI 或入口網站中斷磁碟的連結。

連線至 VM。 在此範例中，VM 的公用 IP 位址是 10.0.1.4，其使用者名稱為 azureuser： 

```bash
ssh azureuser@10.0.1.4
```

首先，尋找您想要中斷連結的資料磁碟。 下列範例會使用 dmesg 來篩選 SCSI 磁碟：

```bash
dmesg | grep SCSI
```

輸出類似於下列範例：

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

在這裡，sdc 是我們想要中斷連結的磁碟。 您也應該抓取磁碟的 UUID。

```bash
sudo -i blkid
```

輸出大致如下列範例所示：

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```


編輯 /etc/fstab 檔案來移除磁碟的參考。 

> [!NOTE]
> 不當編輯 **/etc/fstab** 檔案會導致系統無法開機。 如果不確定，請參閱散發套件的文件，以取得如何適當編輯此檔案的相關資訊。 在編輯之前，也建議先備份 /etc/fstab 檔案。

在文字編輯器中開啟 /etc/fstab 檔案，如下所示：

```bash
sudo vi /etc/fstab
```

在此範例中，必須從 /etc/fstab 檔案中刪除下面這一行：

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

使用 `umount` 將磁碟取消掛接。 下列範例會從 /datadrive 掛接點將 /dev/sdc1 磁碟分割取消掛接：

```bash
sudo umount /dev/sdc1 /datadrive
```


## <a name="detach-a-data-disk-using-azure-cli"></a>使用 Azure CLI 中斷資料磁碟連結 

此範例會使 myDataDisk 磁碟與 myResourceGroup 中名為 myVM 的 VM 中斷連結。

```azurecli
az vm disk detach \
    -g myResourceGroup \
    --vm-name myVm \
    -n myDataDisk
```

磁碟仍留在儲存體中，但不再連結至虛擬機器。


## <a name="detach-a-data-disk-using-the-portal"></a>使用入口網站來中斷資料磁碟連結

1. 在左窗格中，選取 [虛擬機器]。
1. 在 [虛擬機器] 刀鋒視窗中，選取 [磁碟]。
1. 在 [磁碟] 刀鋒視窗頂端，選取 [編輯]。
1. 在 [磁碟] 刀鋒視窗中，在您想要中斷連結的資料磁碟最右側，按一下 ![中斷連結按鈕影像](./media/detach-disk/detach.png) 中斷連結按鈕。
1. 移除磁碟之後，按一下刀鋒視窗頂端的 [儲存]。

磁碟仍留在儲存體中，但不再連結至虛擬機器。



## <a name="next-steps"></a>後續步驟
如果您想要重複使用該資料磁碟，只要[將它連結至另一個 VM](add-disk.md)。

