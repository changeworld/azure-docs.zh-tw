---
title: 在 Azure 上最佳化 Linux VM
description: 了解一些最佳化提示，確保 Azure 上的 Linux VM 設定可獲得最佳效能。
author: rickstercdn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/06/2016
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: eff512c9d050eb293391233848fcece83e845680
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88654186"
---
# <a name="optimize-your-linux-vm-on-azure"></a>在 Azure 上最佳化 Linux VM
您可以從命令列或入口網站，輕鬆建立 Linux 虛擬機器 (VM)。 本教學課程示範如何在 Microsoft Azure 平台上設定，以確保將其效能最佳化。 本主題會使用 Ubuntu Server VM，但您也可以使用 [自己的映像做為範本](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)來建立 Linux 虛擬機器。  

## <a name="prerequisites"></a>Prerequisites
本主題假設您已具備有效的 Azure 訂用帳戶 ([註冊免費試用版](https://azure.microsoft.com/pricing/free-trial/))，並且已在 Azure 訂用帳戶中佈建 VM。 在[建立 VM](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 之前，請先確定您已安裝最新的 [Azure CLI](/cli/azure/install-az-cli2)，並已使用 [az login](/cli/azure/reference-index) 登入 Azure 訂用帳戶。

## <a name="azure-os-disk"></a>Azure 作業系統磁碟
在 Azure 中建立 Linux VM 後，它有兩個相關聯的磁碟。 **/dev/sda** 是作業系統磁碟， **/dev/sdb** 是暫存磁碟。  請勿將主要作業系統磁碟 ( **/dev/sda**) 用於作業系統以外的用途，因為它已針對快速開啟 VM 進行最佳化，無法為工作負載提供良好的效能。 您會想要將一或多個磁碟連接至 VM，以取得具永續性且經過最佳化的資料儲存空間。 

## <a name="adding-disks-for-size-and-performance-targets"></a>加入磁碟以達成大小和效能目標
根據 VM 大小，您可以在 A 系列機器上連接最多 16 個額外的磁碟、在 D 系列上連接 32 個、在 G 系列上連接 64 個，且每個磁碟的大小高達 32 TB。 您可以根據空間和 IOps 需求加入額外的磁碟。 標準儲存體每個磁碟的效能目標為 500 IOps，而進階儲存體每個磁碟的效能目標最高為 20,000 IOps。

對於快取設定為 **ReadOnly** 或 **None** 的進階儲存體磁碟，若要達到最高 IOps，您必須在 Linux 中掛接檔案系統時停用 **barrier** (阻礙)。 您不需要阻礙，因為這些快取設定的進階儲存體磁碟寫入都是持久的。

* 如果您使用 **reiserFS**，請使用掛接選項 `barrier=none` 停用阻礙 (若要啟用阻礙，請使用 `barrier=flush`)
* 如果您使用 **ext3/ext4**，請使用掛接選項 `barrier=0` 停用阻礙 (若要啟用阻礙，請使用 `barrier=1`)
* 如果您使用 **XFS**，請使用掛接選項 `nobarrier` 停用阻礙 (若要啟用阻礙，請使用 `barrier` 選項)

## <a name="unmanaged-storage-account-considerations"></a>非受控儲存體帳戶考量事項
使用 Azure CLI 建立 VM 時的預設動作是使用 Azure 受控磁碟。  這些磁碟是由 Azure 平台處理，不需要任何準備或位置來儲存它們。  非受控磁碟需要儲存體帳戶，且具有一些額外的效能注意事項。  如需受控磁碟的詳細資訊，請參閱 [Azure 受控磁碟概觀](../managed-disks-overview.md)。  下一節概述使用非受控磁碟時才會有的效能注意事項。  同樣地，預設且建議的儲存體解決方案是使用受控磁碟。

如果您使用非受控磁碟建立 VM，請務必從區域與 VM 相同的儲存體帳戶連結磁碟，以確保高度鄰近性及降低網路延遲。  每個標準儲存體帳戶都有最高 20k 的 IOps 和 500 TB 大小的容量。  此限制大約等同於 40 個頻繁使用的磁碟，包括 OS 磁碟和您建立的任何資料磁碟。 進階儲存體帳戶沒有 IOps 上限，不過有 32 TB 的大小限制。 

在處理 IOps 極高的工作負載，且您已為磁碟選擇標準儲存體時，可能需要將磁碟分割到多個儲存體帳戶，才能避免達到標準儲存體帳戶 20,000 IOps 的限制。 VM 可以混搭來自不同儲存體帳戶和不同儲存體帳戶類型的磁碟，以達到最佳組態。
 

## <a name="your-vm-temporary-drive"></a>VM 暫存磁碟機
根據預設，當您建立 VM 時，Azure 會提供作業系統磁碟 ( **/dev/sda**) 和暫存磁碟 ( **/dev/sdb**)。  您加入的所有額外磁碟會顯示為 **/dev/sdc**、 **/dev/sdd**、 **/dev/sde**，依此類推。 暫存磁碟 ( **/dev/sdb**) 上的所有資料均不具持久性，因此當 VM 調整大小、重新部署或維護等特定事件發生迫使 VM 重新啟動時，資料可能會遺失。  暫存磁碟的類型和大小與您在部署時所選擇的 VM 大小相關。 對於所有進階大小的 VM (DS、G 及 DS_V2 系列)，暫存磁碟機均有本機 SSD 提供支援，因此可以產生最高 48k IOps 的額外效能。 

## <a name="linux-swap-partition"></a>Linux 交換分割區
如果您的 Azure VM 是來自 Ubuntu 或 CoreOS 映像，則您可以使用 CustomData 將 cloud-config 傳送到 cloud-init。 如果您[上傳自訂 Linux 映像](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)，使用 cloud-init，您也可以使用 cloud-init 設定交換資料分割。

在 Ubuntu 雲端映像上，您必須使用 cloud-init 設定交換資料分割。 如需詳細資訊，請參閱 [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions)。

對於沒有 cloud-init 支援的映像，從 Azure Marketplace 部署的 VM 映像具有與作業系統整合的 VM Linux 代理程式。 此代理程式可讓 VM 與各種 Azure 服務進行互動。 假設您從 Azure Marketplace 部署標準映像，需要執行以下操作來正確配置 Linux 交換檔設定︰

找出並修改 **/etc/waagent.conf** 檔案中的兩個項目。 它們控制專用交換檔案的存在和交換檔的大小。 您需要驗證的參數是 `ResourceDisk.EnableSwap` 和 `ResourceDisk.SwapSizeMB` 

若要啟用適當啟用的磁碟和裝載的交換檔，請確定參數具有下列設定：

* ResourceDisk.EnableSwap=Y
* ResourceDisk.SwapSizeMB={符合您需求的大小 (MB)} 

進行變更之後，需要重新啟動 waagent 或重新啟動 Linux VM，以反映這些變更。  當您使用 `free` 命令來檢視可用空間時，可以知道已實作變更並已建立交換檔。 以下範例在修改 **waagent.conf** 檔案後建立了 512 MB 的交換檔：

```bash
azuseruser@myVM:~$ free
            total       used       free     shared    buffers     cached
Mem:       3525156     804168    2720988        408       8428     633192
-/+ buffers/cache:     162548    3362608
Swap:       524284          0     524284
```

## <a name="io-scheduling-algorithm-for-premium-storage"></a>進階儲存體的 I/O 排程演算法
隨著 2.6.18 Linux 核心問世，預設 I/O 排程演算法已從「期限」變更為 CFQ (完全公平佇列演算法)。 對於隨機 I/O 模式，CFQ 與期限之間的效能差異並不明顯。  對於磁碟 I/O 模式以循序為主的 SSD 式磁碟，切換回 NOOP 或期限演算法可以達到更高的 I/O 效能。

### <a name="view-the-current-io-scheduler"></a>檢視目前的 I/O 排程器
使用下列命令：  

```bash
cat /sys/block/sda/queue/scheduler
```

您會看到下列輸出，其表示目前的排程器。  

```bash
noop [deadline] cfq
```

### <a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>變更 I/O 排程演算法的目前裝置 (/dev/sda)
使用下列命令：  

```bash
azureuser@myVM:~$ sudo su -
root@myVM:~# echo "noop" >/sys/block/sda/queue/scheduler
root@myVM:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
root@myVM:~# update-grub
```

> [!NOTE]
> 單獨針對 **/dev/sda** 套用此設定不是很有用。 請對所有 I/O 模式以循序 I/O 為主的資料磁碟進行設定。  

您應會看到下列輸出，表示 **grub.cfg** 已成功重建且預設排程器已更新為 NOOP。  

```bash
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-3.13.0-34-generic
Found initrd image: /boot/initrd.img-3.13.0-34-generic
Found linux image: /boot/vmlinuz-3.13.0-32-generic
Found initrd image: /boot/initrd.img-3.13.0-32-generic
Found memtest86+ image: /memtest86+.elf
Found memtest86+ image: /memtest86+.bin
done
```

針對 Red Hat 散發版本系列，您只需要使用下列命令：   

```bash
echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local
```

搭配 Azure 調整核心的 Ubuntu 18.04 會使用多佇列 I/O 排程器。 在該情節中，`none` 是適當的選擇，而不是 `noop`。 如需詳細資訊，請參閱 [Ubuntu I/O 排程器](https://wiki.ubuntu.com/Kernel/Reference/IOSchedulers)。

## <a name="using-software-raid-to-achieve-higher-iops"></a>使用軟體 RAID 來達到更高的 I/Ops
如果工作負載所需的 IOps 超過單一磁碟可提供的極限，您便需要使用由多個磁碟組成的軟體 RAID 組態。 因為 Azure 已在本機網狀架構層級執行磁碟恢復功能，因此您可以從 RAID-0 等量組態獲得最高層級的效能。  先在 Azure 環境中佈建及建立磁碟、將它們連結至 Linux VM，然後再分割、格式化及掛接磁碟機。  如需在 Azure 中針對 Linux VM 配置軟體 RAID 設定的詳細資訊，請參閱 **[在 Linux 上設定軟體 RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)** 文件。

做為傳統 RAID 組態的替代方案，您也可以選擇安裝邏輯磁碟區管理員 (LVM)，以便將多個實體磁碟設定為單一等量邏輯存放磁碟區。 在此組態中，讀取和寫入會分散到磁碟區群組 (類似 RAID0) 中包含的多個磁碟。 基於效能考量，您可能希望建立等量邏輯磁碟區，如此一來，讀取和寫入就能利用您所有已連結的資料磁碟。  如需在 Azure 中針對 Linux VM 設定等量邏輯磁碟區的詳細資訊，請參閱 **[在 Azure 中針對 Linux VM 設定 LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)** 文件。

## <a name="next-steps"></a>後續步驟
請記住，如同所有最佳化討論內容所述，您需要在變更前後執行測試，以測量變更所造成的影響。  最佳化是需要逐步進行的程序，這些程序會對環境中不同的機器產生不同的結果。  對某項組態有用的做法不見得適用於其他組態。

以下是一些連往其他資源的實用連結：

* [Azure Linux 代理程式使用者指南](../extensions/agent-linux.md)
* [在 Linux 上設定軟體 RAID](configure-raid.md)
