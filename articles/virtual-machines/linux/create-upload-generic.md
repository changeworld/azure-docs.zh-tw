---
title: 建立和上傳 Linux VHD
description: 了解如何建立及上傳包含 Linux 作業系統的 Azure 虛擬硬碟 (VHD)。
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 10/08/2018
ms.author: guybo
ms.openlocfilehash: ef4175d24cfd02bb5cb6470b6334fea190b5bec2
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500592"
---
# <a name="information-for-non-endorsed-distributions"></a>非背書的發行版本相關資訊

只有使用其中一個[背書散發套件](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)時，Azure 平台 SLA 才適用於執行 Linux OS 的虛擬機器。 對於這些背書的發行版本，預先設定的 Linux 映像均可在 Azure Marketplace 中取得。

* [Linux on Azure 背書的散發套件](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [支援 Microsoft Azure 中的 Linux 映像](https://support.microsoft.com/kb/2941892)

在 Azure 上執行的所有發行版本都有一些必要條件。 本文無法完整詳述，因為每個發行版本都不同。 即使您符合下列所有準則，還是可能需要對您的 Linux 系統進行大幅調整，以使其正常執行。

建議您從其中一個 [Azure 上的 Linux 背書發行版本](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)開始。 下列文章將示範如何準備 Azure 上所支援之各種背書的 Linux 發行版本：

- [CentOS 型發行版本](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar Container Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES 和 openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)

本文將著重於在 Azure 上執行 Linux 發行版本時的一般指導。

## <a name="general-linux-installation-notes"></a>一般 Linux 安裝注意事項
* Azure 中不支援 Hyper-V 虛擬硬碟 (VHDX) 格式，只支援「固定 VHD」。  您可以使用 Hyper-v 管理員或 [轉換 vhd](/powershell/module/hyper-v/convert-vhd) Cmdlet，將磁片轉換為 VHD 格式。 如果您使用的是 VirtualBox，即會在建立磁碟時選取 [固定大小] 而不是預設值 (動態配置的)。
* Azure 支援 Gen1 (BIOS 開機) & Gen2 (UEFI 開機) 虛擬機器。
* 允許的 VHD 大小上限為 1023 GB。
* 安裝 Linux 系統時，建議您使用標準磁碟分割而不是邏輯磁碟區管理員 (LVM)，此為許多安裝的預設值。 使用標準磁碟分割將可避免 LVM 名稱與複製的 VM 發生衝突，特別是為了疑難排解而一律要將 OS 磁碟連接至另一個相同的 VM 時。 如果願意，您可以在資料磁碟上使用 [LVM](/previous-versions/azure/virtual-machines/linux/configure-raid?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 或 [RAID](/previous-versions/azure/virtual-machines/linux/configure-lvm?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 需要裝載 UDF 檔案系統的核心支援。 在 Azure 上第一次開機時，會使用連接客體的 UDF 格式媒體，將佈建設定傳遞至 Linux VM。 Azure Linux 代理程式必須裝載 UDF 檔案系統，才能讀取其設定並佈建 VM。
* 早於 2.6.37 的 Linux 核心版本不支援具較大 VM 大小之 Hyper-V 上的 NUMA。 這個問題主要會影響使用上游 Red Hat 2.6.32 核心的較舊發行版本，而且已在 Red Hat Enterprise Linux (RHEL) 6.6 (kernel-2.6.32-504) 中加以修正。 執行的自訂核心是 2.6.37 以前版本的系統，或 2.6.32-504 以前的 RHEL 型核心必須在 grub.conf 的核心命令列上設定開機參數 `numa=off`。 如需詳細資訊，請參閱 [Red Hat KB 436883](https://access.redhat.com/solutions/436883) \(英文\)。
* 請勿在 OS 磁碟上設定交換磁碟分割。 您可以設定 Linux 代理程式，在暫存資源磁碟上建立交換檔，如下列步驟中所述。
* Azure 上的所有 VHD 必須具有與 1 MB 對應的虛擬大小。 從原始磁碟轉換為 VHD 時，您必須在轉換前先確定原始磁碟大小是 1 MB 的倍數，如下列步驟中所述。

### <a name="installing-kernel-modules-without-hyper-v"></a>安裝不含 Hyper-V 的核心模組
Azure 會在 Hyper-V Hypervisor 上執行，因此 Linux 要求在 Azure 中執行某些核心模組。 如果您的 VM 是在 Hyper-V 外部建立的，除非 VM 偵測到其執行環境為 Hyper-V 環境，否則 Linux 安裝程式在初始的 ramdisk (initrd 或 initramfs) 中可能不會包含 Hyper-V 的驅動程式。 使用不同的虛擬化系統 (例如 VirtualBox、KVM 等) 來準備您的 Linux 映射時，您可能需要重建 initrd，才能讓初始 ramdisk 上至少使用 hv_vmbus 和 hv_storvsc 核心模組。  這個已知問題適用於以上游 Red Hat 發行版本為基礎的系統，而且可能還有其他系統。

重新建置 initrd 或 initramfs 映像的機制會根據散發套件而有所不同。 請參閱散發套件的文件或洽支援人員，以了解適當程序。  以下是使用 `mkinitrd` 公用程式重新建置 initrd 的範例之一：

1. 備份現有的 initrd 映像：

    ```
    cd /boot
    sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak
    ```

2. 使用 hv_vmbus 和 hv_storvsc 核心模組重新建置 initrd：

    ```
    sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`
    ```

### <a name="resizing-vhds"></a>調整 VHD 的大小
Azure 上的 VHD 映像必須具有與 1 MB 對齊的虛擬大小。  一般而言，使用 Hyper-V 建立的 VHD 均會正確地對應儲存。  如果 VHD 並未正確地對應儲存，則當您嘗試從 VHD 建立映像時，可能會收到類似下面的錯誤訊息。

* VHD HTTP： \/ / \<mystorageaccount> . blob.core.windows.net/vhds/MyLinuxVM.vhd 的虛擬大小不受支援21475270656個位元組。 大小必須是整數 (以 MB 為單位)。

在此案例中，您可以使用 Hyper-V 管理員主控台或 [Resize-VHD](/powershell/module/hyper-v/resize-vhd?view=win10-ps) \(英文\) PowerShell Cmdlet 來調整 VM 的大小。  如果您不是在 Windows 環境中執行，建議使用 `qemu-img` 來轉換 VHD (如果需要) 並調整其大小。

> [!NOTE]
> [qemu-img 版本 >=2.2.1 中已知的 Bug](https://bugs.launchpad.net/qemu/+bug/1490611) \(英文\) 會導致 VHD 的格式不正確。 此問題已在 QEMU 2.6 中修正。 我們建議使用 `qemu-img` 2.2.0 或更早版本，或是 2.6 或更新版本。
> 

1. 直接使用工具 (例如 `qemu-img` 或 `vbox-manage`) 調整 VHD 的大小，可能會導致 VHD 無法開機。  我們建議先將 VHD 轉換為 RAW 磁碟映像。  如果已將 VM 映像建立為 RAW 磁碟映像 (有些 Hypervisor 的預設值，例如 KVM)，則您可以省略此步驟。
 
    ```
    qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw
    ```

1. 計算所需的磁碟映像大小，如此一來，虛擬大小即會對應儲存為 1 MB。  下列 Bash 殼層指令碼會使用 `qemu-img info` 來判斷磁碟映像的虛擬大小，然後計算到下一個 1 MB 的大小。

    ```bash
    rawdisk="MyLinuxVM.raw"
    vhddisk="MyLinuxVM.vhd"

    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "$rawdisk" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    rounded_size=$(((($size+$MB-1)/$MB)*$MB))
    
    echo "Rounded Size = $rounded_size"
    ```

3. 使用 `$rounded_size` 調整原始磁碟，如上方所設定。

    ```bash
    qemu-img resize MyLinuxVM.raw $rounded_size
    ```

4. 現在，將 RAW 磁碟轉換回固定大小的 VHD。

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

   或者，使用 qemu 版本 2.6+，包含 `force_size` 選項。

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

## <a name="linux-kernel-requirements"></a>Linux Kernel 需求

適用於 Hyper-V 和 Azure 的 Linux Integration Services (LIS) 驅動程式會直接提供給上游 Linux Kernel。 許多包括最新 Linux 核心版本 (例如 3.x) 的發行版本已經有這些驅動程式可供使用，或透過其核心來提供這些驅動程式的向後移植版本。  在上游核心中會透過新的修正和功能不斷更新這些驅動程式，因此，如果可能，建議您執行包含這些修正與更新的[背書發行版本](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

如果您執行的是 Red Hat Enterprise Linux 版本 6.0 到 6.3 的變體，則您必須安裝 [Hyper-V 的最新 LIS 驅動程式](https://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409)。 從 RHEL 6.4+ (及衍生項目) 開始，核心就已經隨附 LIS 驅動程式，因此無需額外的安裝套件。

如果需要自訂核心，建議您使用最新的核心版本 (例如 3.8+)。 針對發行版本或自行維護核心的廠商，您需要定期將 LIS 驅動程式從上游核心向後移植到您的自訂核心。  即使您已經在執行相對較新的核心版本，還是強烈建議您持續追蹤 LIS 驅動程式中的任何上游修正，並視需要向後移植它們。 LIS 驅動程式原始程式檔的位置均指定於 Linux 核心來源樹狀目錄的 [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) 檔案中：
```
    F:    arch/x86/include/asm/mshyperv.h
    F:    arch/x86/include/uapi/asm/hyperv.h
    F:    arch/x86/kernel/cpu/mshyperv.c
    F:    drivers/hid/hid-hyperv.c
    F:    drivers/hv/
    F:    drivers/input/serio/hyperv-keyboard.c
    F:    drivers/net/hyperv/
    F:    drivers/scsi/storvsc_drv.c
    F:    drivers/video/fbdev/hyperv_fb.c
    F:    include/linux/hyperv.h
    F:    tools/hv/
```
下列修補程式必須隨附於核心中。 對於所有發行版本而言，此清單並不完整。

* [ata_piix：依預設將磁碟委託給 Hyper-V 驅動程式](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc：負責 RESET 路徑中的在途封包](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc：避免使用 WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc：針對 RAID 和虛擬主機介面卡驅動程式停用 WRITE SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc：NULL 指標取值修正](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc：信號緩衝區失敗可能導致 I/O 凍結](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs︰防範 __scsi_remove_device 雙重執行](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>Azure Linux 代理程式
[Azure Linux 代理程式](../extensions/agent-linux.md) `waagent` 會在 Azure 中佈建 Linux 虛擬機器。 您可以在 [Linux 代理程式 GitHub 存放庫](https://github.com/Azure/WALinuxAgent) \(英文\) 中取得最新版本、檔案問題或提交提取要求。

* Linux 代理程式已在 Apache 2.0 授權下發行。 許多散發套件已提供代理程式的 RPM 或 deb 套件，而且可以輕鬆地安裝和更新這些套件。
* Azure Linux 代理程式需要 Python v2.6+。
* 代理程式還需要 python-pyasn1 模組。 大多數的發行版本都會以可個別安裝的套件形式提供此模組。
* 在某些情況下，Azure Linux 代理程式可能與 NetworkManager 不相容。 散發套件提供的許多 RPM/deb 套件會將 NetworkManager 設定為與 waagent 套件衝突。 在這些情況下，當您安裝 Linux 代理程式套件時，將會解除安裝 NetworkManager。
* Azure Linux 代理程式必須等於或高於[最小支援版本](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)。

## <a name="general-linux-system-requirements"></a>一般的 Linux 系統需求

1. 修改 GRUB 或 GRUB2 中的核心開機行以包含下列參數，如此便會將所有主控台訊息傳送到第一個序列埠。 這些訊息均可協助 Azure 支援來偵錯任何問題。
    ```  
    console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
    ```
    我們也建議「移除」下列參數 (如果有的話)。
    ```  
    rhgb quiet crashkernel=auto
    ```
    在雲端環境中，我們想要將所有記錄傳送到序列埠，因此不適合使用圖形化和無訊息開機。 您可以視需要保留 `crashkernel` 選項的設定，但請注意，此參數會減少 VM 中至少 128 MB 的可用記憶體數量，這在較小的 VM 大小中可能是個問題。

1. 安裝 Azure Linux 代理程式。
  
    如需在 Azure 上佈建 Linux 映像，您需要 Azure Linux 代理程式。  許多散發套件都會以 RPM 或 deb 套件的形式提供代理程式， (套件通常稱為 WALinuxAgent 或 WALinuxAgent) 。  您也可以遵循 [Linux 代理程式指南](../extensions/agent-linux.md)中的步驟來手動安裝代理程式。

1. 確定 SSH 伺服器已安裝並設定為在開機時啟動。  此設定通常是預設值。

1. 請不要在 OS 磁碟上建立交換空間。
  
    Azure Linux 代理程式可在 VM 佈建於 Azure 後，使用附加至 VM 的本機資源磁碟自動設定交換空間。 本機資源磁碟是「暫存」磁碟，可能會在 VM 取消佈建時清空。 安裝 Azure Linux 代理程式 (上述步驟 2) 之後，視需要在 /etc/waagent.conf 中修改下列參數。
    ```  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: Set this to your desired size.
    ```
1. 執行下列命令以取消佈建虛擬機器。
  
     ```
     sudo waagent -force -deprovision
     export HISTSIZE=0
     logout
     ```  
   > [!NOTE]
   > 在 Virtualbox 上，您可能會在執行 `waagent -force -deprovision` 之後看到下列錯誤，指出 `[Errno 5] Input/output error`。 此錯誤訊息並不重要，您可以忽略。

* 關閉虛擬機器，並將 VHD 上傳至 Azure。