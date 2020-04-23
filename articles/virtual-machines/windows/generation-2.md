---
title: 對第 2 代 VM 的 Azure 支援
description: 第 2 代 VM 的 Azure 支援概述
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/11/2020
ms.author: jushiman
ms.openlocfilehash: 239e339d1870060d95d6c7e7b602be7d61af5f77
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869631"
---
# <a name="support-for-generation-2-vms-on-azure"></a>支援 Azure 上第 2 代 VM

第 2 代虛擬機 (VM) 的支援現在在 Azure 上可用。 創建虛擬機後無法更改虛擬機器的生成,因此在選擇一代之前請查看此頁面上的注意事項。

第 2 代 VM 支援第 1 代 VM 中不支援的關鍵功能。 這些功能包括記憶體增加、Intel Software Guard Extensions (Intel SGX) 和虛擬化的持續性記憶體 (vPMEM)。 在本地運行的第 2 代 VM 具有 Azure 中尚不支援的功能。 有關詳細資訊,請參閱[功能和功能](#features-and-capabilities)部分。

第 2 代 VM 使用基於 UEFI 的新啟動體系結構,而不是第 1 代 VM 使用的基於 BIOS 的體系結構。 與第 1 代 VM 相比,第 2 代 VM 可能改善了啟動和安裝時間。 有關第 2 代 VM 以及第 1 代和第 2 代之間的一些差異的概述,請參閱[是否應在 Hyper-V 中創建第 1 代或第 2 代虛擬機器?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="generation-2-vm-sizes"></a>第二代 VM 大小

Azure 中的所有 VM 大小都支援第 1 代 VM(Mv2 系列 VM 除外)。 Azure 現在為以下選定的 VM 系列提供第 2 代支援:

* [B 系列](https://docs.microsoft.com/azure/virtual-machines/windows/b-series-burstable)
* [DC 系列](../dcv2-series.md)
* [DSv2 系列](../dv2-dsv2-series.md)與[Dsv3 系列](../dv3-dsv3-series.md)
* [達斯夫4系列](https://docs.microsoft.com/azure/virtual-machines/dav4-dasv4-series)
* [Esv3 系列](../ev3-esv3-series.md)
* [Easv4 系列](https://docs.microsoft.com/azure/virtual-machines/eav4-easv4-series)
* [Fsv2 系列](../fsv2-series.md)
* [GS 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#gs-series)
* [HB 系列](../hb-series.md)
* [HC 系列](../hc-series.md)
* [Ls 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#ls-series)與[Lsv2 系列](../lsv2-series.md)
* [Mv2 系列](../mv2-series.md)
* [NCv2 系列](../ncv2-series.md)與[NCv3 系列](../ncv3-series.md)
* [ND 系列](../nd-series.md)
* [NVv3 系列](../nvv3-series.md)

> [!NOTE]
> 由於 Mv2 系列僅與第 2 代 VM 映射配合使用,因此 Mv2 系列 VM 映像通常適用於 Mv2 系列 VM 映射。 Mv2 系列 VM 不支援第 1 代 VM 映射。 

## <a name="generation-2-vm-images-in-azure-marketplace"></a>在 Azure 應用商店中生成 2 個 VM 映像

第 2 代 VM 支援以下應用商店映射:

* Windows Server 2019、2016、2012 R2、2012
* Windows 10 專業版、Windows 10 企業版
* SUSE Linux 企業伺服器 15 SP1
* SUSE Linux 企業伺服器 12 SP4
* Ubuntu 伺服器 16.04, 18.04, 19.04, 19.10 
* RHEL 8.1、 8.0、 7.7、 7.6、 7.5、 7.4、 7.0
* CENT OS 8.1、 8.0、 7.7、 7.6、 7.5、 7.4
* 甲骨文 Linux 7.7, 7.7-CI

## <a name="on-premises-vs-azure-generation-2-vms"></a>本地與 Azure 產生 2 VM

Azure 目前不支援本地 Hyper-V 支援第 2 代 VM 的某些功能。

| 第二代功能                | 內部部署 Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| 安全啟動                         | :heavy_check_mark:  | :x:   |
| 受防護的 VM                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| 虛擬化安全性 (VBS) | :heavy_check_mark:  | :x:   |
| VHDX 格式                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>特色與功能

### <a name="generation-1-vs-generation-2-features"></a>第一代與第 2 代功能

| 功能 | 第 1 代 | 第 2 代 |
|---------|--------------|--------------|
| Boot             | PCAT                      | UEFI                               |
| 磁碟控制器 | IDE                       | SCSI                               |
| VM 大小         | 所有 VM 大小 | 只支援進階儲存的 VM |

### <a name="generation-1-vs-generation-2-capabilities"></a>第一代與第 2 代功能

| 功能 | 第 1 代 | 第 2 代 |
|------------|--------------|--------------|
| OS 磁碟> 2 TB                    | :x:                | :heavy_check_mark: |
| 自訂磁碟/映像/交換作業系統         | :heavy_check_mark: | :heavy_check_mark: |
| 虛擬機器規模集支援 | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | :heavy_check_mark: |
| 備份/還原                    | :heavy_check_mark: | :heavy_check_mark: |
| 共用影像庫              | :heavy_check_mark: | :heavy_check_mark: |
| Azure 磁碟加密             | :heavy_check_mark: | :x:                |

## <a name="creating-a-generation-2-vm"></a>建立第 2 代 VM

### <a name="marketplace-image"></a>市場映射

在 Azure 門戶或 Azure CLI 中,可以從支援 UEFI 啟動的應用商店映射創建第 2 代 VM。

#### <a name="azure-portal"></a>Azure 入口網站

以下是在 Azure 門戶中創建第 2 代 (Gen2) VM 的步驟。

1. 在 https://portal.azure.com 登入 Azure 入口網站。
1. 選取 [建立資源]  。
1. 按一下左側 Azure 應用商店中的所有 **「查看」。**
1. 選擇支援 Gen 2 的圖像。
1. 按一下頁面底部的 [新增]  。
1. 在 **「進階」** 選項卡中,在 **「VM 產生**」部分下,選擇第 2**代**選項。
1. 在 **'基本'** 選項卡"**實體下詳細資訊**中,轉到**大小**並開啟 **「選擇 VM 大小**」邊欄選項卡。
1. 選擇[支援的 2 代 VM](#generation-2-vm-sizes)。
1. 遍通[Azure 門戶建立流](quick-create-portal.md)以完成創建 VM。

![選擇第 1 代或第 2 代 VM](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

您還可以使用 PowerShell 直接引用第 1 代或第 2 代 SKU 來創建 VM。

例如,使用以下 PowerShell cmdlet 獲取`WindowsServer`產品/服務中的 SKU 清單。

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

或者,可以使用 Azure CLI 查看**發行者**列出的任何可用的第 2 代映射。

```azurecli
az vm image list --publisher Canonical --sku gen2 --output table --all
```

如果要建立具有 Windows Server 2012 作為作業系統的 VM,則選擇第 1 代 (BIOS) 或第 2 代 (UEFI) VM SKU,如下所示:

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

有關當前支援的應用商店映像清單,請參閱[「功能和功能](#features-and-capabilities)」 部分。

### <a name="managed-image-or-managed-disk"></a>託管映像或託管磁碟

可以從託管映射或託管磁碟創建第 2 代 VM,就像創建第 1 代 VM 一樣。

### <a name="virtual-machine-scale-sets"></a>虛擬機器擴展集

您還可以使用虛擬機縮放集創建第 2 代 VM。 在 Azure CLI 中,使用 Azure 縮放集創建第 2 代 VM。

## <a name="frequently-asked-questions"></a>常見問題集

* **第 2 代 VM 在所有 Azure 區域中可用嗎?**  
    是。 但並非所有[第 2 代 VM 大小](#generation-2-vm-sizes)都可用於每個區域。 第 2 代 VM 的可用性取決於 VM 大小的可用性。

* **第 1 代 VM 和第 2 代 VM 之間是否有價格差異?**  
   否。

* **我有一個來自本地第 2 代 VM 的 .vhd 檔。我能否使用 .vhd 檔在 Azure 中建立第 2 代 VM?**
  可以,您可以將第 2 代 .vhd 檔帶到 Azure,並用它來創建第 2 代 VM。 使用以下步驟執行此操作:
    1. 將 .vhd 上傳到要創建 VM 的同一區域中的儲存帳戶。
    1. 從 .vhd 檔案創建託管磁碟。 將超 V 生成屬性設置為 V2。 以下 PowerShell 命令在創建託管磁碟時設置 Hyper-V 生成屬性。

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. 磁碟可用後,通過附加此磁碟創建 VM。 創建的 VM 將是第 2 代 VM。
    建立第 2 代 VM 時,可以選擇通用此 VM 映射。 通過對映射進行一般化,可以使用它創建多個 VM。

* **如何增加操作系統磁碟大小?**  
  大於 2 TB 的 OS 磁碟是第 2 代 VM 的新磁碟。 默認情況下,對於第 2 代 VM,OS 磁碟小於 2 TB。 您可以將磁碟大小增加到建議的最大 4 TB。 使用 Azure CLI 或 Azure 門戶增加 OS 磁碟大小。 有關如何以程式設計方式擴展磁碟的資訊,請參閱[調整磁碟的大小](expand-os-disk.md)。

  要從 Azure 門戶增加 OS 磁碟大小,可以執行以下設定:

  1. 在 Azure 門戶中,轉到 VM 屬性頁。
  1. 要關閉與取消分配 VM,請選擇「**停止」** 按鈕。
  1. 在 **"磁碟'** 部分中,選擇要增加的作業系統磁碟。
  1. 在 **"磁碟'** 部分中,選擇 **"設定**",並將**大小**更新為所需的值。
  1. 傳回 VM 屬性頁並**啟動**VM。
  
  您可能會看到大於 2 TB 的 OS 磁碟的警告。 該警告不適用於第 2 代 VM。 但是,*不建議*大於 4 TB 的 OS 磁碟大小。

* **第 2 代 VM 支援加速網路嗎?**  
    是。 有關詳細資訊,請參閱[創建具有加速網路的 VM。](../../virtual-network/create-vm-accelerated-networking-cli.md)

* **第 2 代是否支援 VHDX?**  
    否,第 2 代 VM 僅支援 VHD。

* **第 2 代 VM 支援 Azure 超磁碟存儲嗎?**  
    是。

* **是否可以將 VM 從第 1 代遷移到第 2 代?**  
    否,在創建 VM 後無法更改它的生成。 如果需要在 VM 代之間切換,請創建另一代的新 VM。

* **為什麼在嘗試創建 Gen2 VM 時,大小選擇器中未啟用 VM 大小?**

    這可以通過執行以下操作來解決:

    1. 認證**VM 產生**屬性是否設定為 **「進階」** 選項卡中的**第 2 代**。
    1. 檢查您正在搜尋支援[Gen2 VM 的 VM 大小](#generation-2-vm-sizes)。

## <a name="next-steps"></a>後續步驟

* 瞭解[Hyper-V 中的第 2 代虛擬機器](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)。

* 瞭解如何準備[VHD](prepare-for-upload-vhd-image.md)以從本地系統上傳到 Azure。
