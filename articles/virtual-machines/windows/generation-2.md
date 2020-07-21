---
title: 第 2 代 VM 的 Azure 支援
description: 第 2 代 VM 的 Azure 支援概觀
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/11/2020
ms.author: jushiman
ms.openlocfilehash: 1ebba13de14935d931d5d21ab786889d9a3755da
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86500305"
---
# <a name="support-for-generation-2-vms-on-azure"></a>Azure 上第 2 代 VM 的支援

第 2 代虛擬機器 (VM) 的支援現已在 Azure 上提供。 建立虛擬機器之後，您就無法變更其世代，因此請先檢閱此頁面上的考量事項，再選擇世代。

第 2 代 VM 可支援第 1 代 VM 所不支援的重要功能。 這些功能包括記憶體增加、Intel Software Guard Extensions (Intel SGX) 和虛擬化的持續性記憶體 (vPMEM)。 在內部部署環境執行的第 2 代 VM 擁有一些 Azure 尚未支援的功能。 如需詳細資訊，請參閱[功能](#features-and-capabilities)一節。

第 2 代 VM 捨棄第 1 代 VM 所使用的 BIOS 架構，改用新式的 UEFI 開機架構。 相較於第 1 代 VM，第 2 代 VM 可能已改善開機與安裝時間。 如需第 2 代 VM 的概觀及第 1 代與第 2 代的差異，請參閱[應該在 Hyper-V 中建立第 1 代還是第 2 代虛擬機器？](/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) \(部分機器翻譯\)。

## <a name="generation-2-vm-sizes"></a>第 2 代 VM 大小

Azure 中所有的 VM 大小都支援第 1 代 VM (Mv2 系列 VM 除外)。 Azure 現在為下列選取的 VM 系列提供第 2 代支援：

* [B 系列](../sizes-b-series-burstable.md)
* [DCsv2 系列](../dcv2-series.md)
* [DSv2 系列](../dv2-dsv2-series.md)和 [Dsv3 系列](../dv3-dsv3-series.md)
* [Dasv4 系列](../dav4-dasv4-series.md)
* [Esv3 系列](../ev3-esv3-series.md)
* [Easv4 系列](../eav4-easv4-series.md)
* [Fsv2 系列](../fsv2-series.md)
* [GS 系列](../sizes-previous-gen.md#gs-series)
* [HB 系列](../hb-series.md)
* [HC 系列](../hc-series.md)
* [Ls 系列](../sizes-previous-gen.md#ls-series)和 [Lsv2 系列](../lsv2-series.md)
* [M 系列](../m-series.md)
* [Mv2 系列](../mv2-series.md)<sup>1</sup>
* [NCv2 系列](../ncv2-series.md)和 [NCv3 系列](../ncv3-series.md)
* [ND 系列](../nd-series.md)
* [NVv3 系列](../nvv3-series.md)

<sup>1</sup> Mv2 系列不支援第 1 代 VM 映像，且僅支援第 2 代映像的子集。 如需詳細資訊，請參閱 [Mv2 系列文件](../mv2-series.md) \(部分機器翻譯\)。

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Azure Marketplace 中的第 2 代 VM 映像

第 2 代 VM 支援下列 Marketplace 映像：

* Windows Server 2019、2016、2012 R2、2012
* Windows 10 專業版、Windows 10 企業版
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4
* Ubuntu Server 16.04、18.04、19.04、19.10 
* RHEL 8.1、8.0、7.7、7.6、7.5、7.4、7.0
* Cent OS 8.1、8.0、7.7、7.6、7.5、7.4
* Oracle Linux 7.7、7.7-CI

> [!NOTE]
> 特定的虛擬機器大小 (如 Mv2 系列) 可能僅支援這些映像的子集，請參閱相關虛擬機器大小文件以取得完整詳細資料。

## <a name="on-premises-vs-azure-generation-2-vms"></a>內部部署與Azure 第 2 代 VM 的比較

Azure 目前不支援內部部署 Hyper-V 針對第 2 代 VM 所支援的部分功能。

| 第 2 代功能                | 內部部署 Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| 安全開機                         | :heavy_check_mark:  | :x:   |
| 受防護的 VM                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| 虛擬化型安全性 (VBS) | :heavy_check_mark:  | :x:   |
| VHDX 格式                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>功能

### <a name="generation-1-vs-generation-2-features"></a>第 1 代與第 2 代功能的比較

| 功能 | 第 1 代 | 第 2 代 |
|---------|--------------|--------------|
| Boot             | PCAT                      | UEFI                               |
| 磁碟控制器 | IDE                       | SCSI                               |
| VM 大小         | 所有 VM 大小 | 只有支援進階儲存體的 VM |

### <a name="generation-1-vs-generation-2-capabilities"></a>第 1 代與第 2 代功能的比較

| 功能 | 第 1 代 | 第 2 代 |
|------------|--------------|--------------|
| OS 磁碟 > 2 TB                    | :x:                | :heavy_check_mark: |
| 自訂磁碟/映像/交換 OS         | :heavy_check_mark: | :heavy_check_mark: |
| 虛擬機器擴展集支援 | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | :heavy_check_mark: |
| 備份/還原                    | :heavy_check_mark: | :heavy_check_mark: |
| 共用映像庫              | :heavy_check_mark: | :heavy_check_mark: |
| Azure 磁碟加密             | :heavy_check_mark: | :x:                |

## <a name="creating-a-generation-2-vm"></a>建立第 2 代 VM

### <a name="marketplace-image"></a>Marketplace 映像

在 Azure 入口網站或 Azure CLI 中，您可以從支援 UEFI 開機的 Marketplace 映像建立第 2 代 VM。

#### <a name="azure-portal"></a>Azure 入口網站

以下是在 Azure 入口網站中建立第 2 代 (Gen2) VM 的步驟。

1. 在 https://portal.azure.com 登入 Azure 入口網站。
1. 選取 [建立資源]。
1. 從左側的 Azure Marketplace 按一下 [查看全部]。
1. 選取支援 Gen2 的映像。
1. 按一下頁面底部的 [新增] 。
1. 在 [進階] 索引標籤的 [VM 世代] 區段底下，選取 [Gen 2] 選項。
1. 在 [基本] 索引標籤的 [執行個體詳細資料] 底下，移至 [大小] 並開啟 [選取 VM 大小] 刀鋒視窗。
1. 選取[支援的第 2 代 VM](#generation-2-vm-sizes)。
1. 請瀏覽 [Azure 入口網站建立流程](quick-create-portal.md)以完成建立 VM。

![選取 Gen 1 或 Gen 2 VM](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

您也可以直接參考第 1 代或第 2 代 SKU，使用 PowerShell 來建立 VM。

例如，使用下列 PowerShell Cmdlet 來取得 `WindowsServer` 供應項目中的 SKU 清單。

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

如果您使用 Windows Server 2012 作為作業系統來建立 VM，則您將會選取第 1 代 (BIOS) 或第 2 代 (UEFI) VM SKU，如下所示：

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

如需目前支援的 Marketplace 映像清單，請參閱[功能](#features-and-capabilities)一節。

#### <a name="azure-cli"></a>Azure CLI

或者，您可以使用 Azure CLI 來查看依 [發行者] 列出的任何可用第 2 代映像。

```azurecli
az vm image list --publisher Canonical --sku gen2 --output table --all
```

### <a name="managed-image-or-managed-disk"></a>受控映像或受控磁碟

您可以使用與建立第 1 代 VM 相同的方式，從受控映像或受控磁碟建立第 2 代 VM。

### <a name="virtual-machine-scale-sets"></a>虛擬機器擴展集

您也可以使用虛擬機器擴展集來建立第 2 代 VM。 在 Azure CLI 中，使用 Azure 擴展集來建立第 2 代 VM。

## <a name="frequently-asked-questions"></a>常見問題集

* **第 2 代 VM 是否可在所有 Azure 區域中使用？**  
    是。 但並非所有[第 2 代 VM 大小](#generation-2-vm-sizes)都可供在每個區域使用。 第 2 代 VM 的可用性取決於 VM 大小的可用性。

* **第 1 代和第 2 代 VM 之間是否有價格差異？**  
   否。

* **我有來自內部部署第 2 代 VM 的 .vhd 檔案。我是否可以在 Azure 中使用該 .vhd 檔案來建立第 2 代 VM？**
  是，您可以將第 2 代 .vhd 檔案帶入 Azure，並使用其建立第 2 代 VM。 請使用下列步驟執行此作業：
    1. 將 .vhd 上傳至位於您要在其中建立 VM 之相同區域中的儲存體帳戶。
    1. 從 .vhd 檔案建立受控磁碟。 將 Hyper-V 世代屬性設定為 V2。 下列 PowerShell 命令會在建立受控磁碟時設定 Hyper-V 世代屬性。

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. 當磁碟可供使用之後，請連結此磁碟來建立 VM。 建立的 VM 將會是第 2 代 VM。
    建立第 2 代 VM 時，您可以選擇性地將此 VM 的映像一般化。 透過將映像一般化，您即可用其建立多個 VM。

* **如何增加 OS 磁碟大小？**  
  大於 2 TB 的 OS 磁碟是第 2 代 VM 的新功能。 根據預設，第 2 代 VM 的 OS 磁碟小於 2 TB。 您可以將磁碟大小增加到建議的 4 TB 上限。 使用 Azure CLI 或 Azure 入口網站來增加 OS 磁碟大小。 如需如何以程式設計方式擴充磁碟的相關資訊，請參閱[調整磁碟大小](expand-os-disk.md)。

  若要從 Azure 入口網站增加 OS 磁碟大小：

  1. 在 Azure 入口網站中，移至 VM 屬性頁面。
  1. 若要關閉和解除配置 VM，請選取 [停止] 按鈕。
  1. 在 [磁碟] 區段中，選取您要增加的 OS 磁碟。
  1. 在 [磁碟] 區段中，選取 [設定]，然後將 [大小] 更新為您想要的值。
  1. 回到 VM 屬性頁面，然後**啟動** VM。
  
  針對大於 2 TB 的 OS 磁碟，您可能會看到警告。 此警告不適用於第 2 代 VM。 不過，「不建議」使用大於 4 TB 的 OS 磁碟大小。

* **第 2 代 VM 是否支援加速網路？**  
    是。 如需詳細資訊，請參閱[建立具有加速網路功能的 VM](../../virtual-network/create-vm-accelerated-networking-cli.md)。

* **第2代 Vm 是否支援 Azure 中的安全開機或 vTPM？**
    Azure 中的第1代和第2代 Vm 不支援安全開機或 vTPM。 
    
* **第 2 代 VM 是否支援 VHDX？**  
    否，第 2 代 VM 僅支援 VHD。

* **第 2 代 VM 是否支援 Azure Ultra 磁碟儲存體？**  
    是。

* **我是否可以將 VM 從第 1 代移轉到第 2 代？**  
    否，您無法在建立 VM 之後變更其世代。 如果您需要在 VM 世代之間切換，請建立不同世代的新 VM。

* **當我嘗試建立 Gen2 VM 時，為什麼大小選取器中未啟用我的 VM 大小？**

    這可能可以透過執行下列動作來解決：

    1. 確認在 [進階] 索引標籤中，已將 [VM 世代] 屬性設定為 [Gen 2]。
    1. 確認您正在搜尋[支援 Gen2 VM 的 VM 大小](#generation-2-vm-sizes)。

## <a name="next-steps"></a>後續步驟

* 了解 [Hyper-V 中的第 2 代虛擬機器](/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) \(部分機器翻譯\)。

* 了解如何[準備 VHD](prepare-for-upload-vhd-image.md) 以從內部部署系統上傳到 Azure。
