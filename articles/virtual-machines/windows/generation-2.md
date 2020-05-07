---
title: 第2代 Vm 的 Azure 支援
description: 第2代 Vm 的 Azure 支援總覽
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/11/2020
ms.author: jushiman
ms.openlocfilehash: bf690ad3ad38632834a92c4a743b1cb584beaf65
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82838819"
---
# <a name="support-for-generation-2-vms-on-azure"></a>Azure 上第2代 Vm 的支援

第2代虛擬機器（Vm）的支援現已在 Azure 上提供。 建立虛擬機器之後，您就無法變更其層代，因此請先參閱此頁面上的考慮，再選擇世代。

第2代 Vm 支援第1代 Vm 中不支援的重要功能。 這些功能包括記憶體增加、Intel Software Guard Extensions (Intel SGX) 和虛擬化的持續性記憶體 (vPMEM)。 第2代 Vm 執行于內部部署，但在 Azure 中尚不支援某些功能。 如需詳細資訊，請參閱[特性和功能](#features-and-capabilities)一節。

第2代 Vm 使用新的 UEFI 型開機架構，而不是第1代 Vm 所使用的 BIOS 架構。 相較于第1代 Vm，第2代 Vm 可能已改善開機和安裝時間。 如需第2代 Vm 的總覽，以及第1代與第2代的差異，請參閱[我應該在 hyper-v 中建立第1代或第2代虛擬機器嗎？](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)。

## <a name="generation-2-vm-sizes"></a>第2代 VM 大小

Azure 中的所有 VM 大小都支援第1代 Vm （Mv2 系列 Vm 除外）。 Azure 現在為下列選取的 VM 系列提供第2代支援：

* [B 系列](https://docs.microsoft.com/azure/virtual-machines/windows/b-series-burstable)
* [DCsv2 系列](../dcv2-series.md)
* [DSv2 系列](../dv2-dsv2-series.md)和[Dsv3 系列](../dv3-dsv3-series.md)
* [Dasv4 系列](https://docs.microsoft.com/azure/virtual-machines/dav4-dasv4-series)
* [Esv3 系列](../ev3-esv3-series.md)
* [Easv4 系列](https://docs.microsoft.com/azure/virtual-machines/eav4-easv4-series)
* [Fsv2 系列](../fsv2-series.md)
* [GS 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#gs-series)
* [HB 系列](../hb-series.md)
* [HC 系列](../hc-series.md)
* [Ls 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#ls-series)和[Lsv2 系列](../lsv2-series.md)
* [M 系列](../m-series.md)
* [Mv2 系列](../mv2-series.md)<sup>1</sup>
* [NCv2 系列](../ncv2-series.md)和[NCv3 系列](../ncv3-series.md)
* [ND 系列](../nd-series.md)
* [NVv3 系列](../nvv3-series.md)

<sup>1</sup> Mv2 系列不支援第1代 VM 映射，而且只支援第2代映射的子集。 如需詳細資訊，請參閱[Mv2 系列檔](https://docs.microsoft.com/azure/virtual-machines/mv2-series)。

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Azure Marketplace 中的第2代 VM 映射

第2代 Vm 支援下列 Marketplace 映射：

* Windows Server 2019、2016、2012 R2、2012
* Windows 10 專業版、Windows 10 企業版
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4
* Ubuntu Server 16.04、18.04、19.04、19.10 
* RHEL 8.1、8.0、7.7、7.6、7.5、7.4、7。0
* 美分 OS 8.1、8.0、7.7、7.6、7.5、7。4
* Oracle Linux 7.7、7.7-CI

> [!NOTE]
> 特定的虛擬機器大小（如 Mv2 系列）可能只支援這些映射的子集-請參閱相關的虛擬機器大小檔，以取得完整的詳細資料。

## <a name="on-premises-vs-azure-generation-2-vms"></a>內部部署與 Azure 第2代 Vm

Azure 目前不支援內部部署 Hyper-v 針對第2代 Vm 所支援的部分功能。

| 第2代功能                | 內部部署 Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| 安全開機                         | :heavy_check_mark:  | :x:   |
| 受防護的 VM                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| 以虛擬化為基礎的安全性（VBS） | :heavy_check_mark:  | :x:   |
| VHDX 格式                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>特色與功能

### <a name="generation-1-vs-generation-2-features"></a>層代1與第2代功能

| 功能 | 第 1 代 | 第 2 代 |
|---------|--------------|--------------|
| Boot             | PCAT                      | UEFI                               |
| 磁碟控制卡 | IDE                       | SCSI                               |
| VM 大小         | 所有 VM 大小 | 只有支援 premium 儲存體的 Vm |

### <a name="generation-1-vs-generation-2-capabilities"></a>層代1和層代2功能

| 功能 | 第 1 代 | 第 2 代 |
|------------|--------------|--------------|
| OS 磁片 > 2 TB                    | :x:                | :heavy_check_mark: |
| 自訂磁片/映射/交換 OS         | :heavy_check_mark: | :heavy_check_mark: |
| 虛擬機器擴展集支援 | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | :heavy_check_mark: |
| 備份/還原                    | :heavy_check_mark: | :heavy_check_mark: |
| 共用映射資源庫              | :heavy_check_mark: | :heavy_check_mark: |
| Azure 磁碟加密             | :heavy_check_mark: | :x:                |

## <a name="creating-a-generation-2-vm"></a>建立第2代 VM

### <a name="marketplace-image"></a>Marketplace 映射

在 [Azure 入口網站] 或 [Azure CLI] 中，您可以從支援 UEFI 開機的 Marketplace 映射建立第2代 Vm。

#### <a name="azure-portal"></a>Azure 入口網站

以下是在 Azure 入口網站中建立第2代（Gen2） VM 的步驟。

1. 在 https://portal.azure.com 登入 Azure 入口網站。
1. 選取 [建立資源]  。
1. 按一下左側 [Azure Marketplace 的 [**查看全部**]。
1. 選取支援 Gen2 的影像。
1. 按一下 [建立]  。
1. 在 [ **Advanced** ] 索引標籤的 [ **VM 世代**] 區段底下，選取 [ **Gen 2** ] 選項。
1. 在 [**基本**] 索引標籤的 [**實例詳細資料**] 底下，移至 [**大小**]，然後開啟 [**選取 VM 大小**] 分頁。
1. 選取[支援的第2代 VM](#generation-2-vm-sizes)。
1. 請執行[Azure 入口網站建立流程](quick-create-portal.md)，完成 VM 的建立。

![選取第1代或第2代 VM](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

您也可以使用 PowerShell 直接參考第1代或第2代 SKU 來建立 VM。

例如，使用下列 PowerShell Cmdlet 來取得`WindowsServer`供應專案中的 sku 清單。

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

或者，您可以使用 Azure CLI 查看**發行者**所列出的任何可用第2代映射。

```azurecli
az vm image list --publisher Canonical --sku gen2 --output table --all
```

如果您使用 Windows Server 2012 做為作業系統來建立 VM，則會選取第1代（BIOS）或第2代（UEFI） VM SKU，如下所示：

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

如需目前支援的 Marketplace 映射清單，請參閱[特性和功能](#features-and-capabilities)一節。

### <a name="managed-image-or-managed-disk"></a>受控映射或受控磁片

您可以使用與建立第1代 VM 相同的方式，從受控映射或受控磁片建立第2代 VM。

### <a name="virtual-machine-scale-sets"></a>虛擬機器擴展集

您也可以使用虛擬機器擴展集來建立第2代 Vm。 在 Azure CLI 中，使用 Azure 擴展集來建立第2代 Vm。

## <a name="frequently-asked-questions"></a>常見問題集

* **第2代 Vm 可以在所有 Azure 區域中使用嗎？**  
    可以。 但並非所有層[代 2 VM 大小](#generation-2-vm-sizes)都可以在每個區域中使用。 第2代 VM 的可用性取決於 VM 大小的可用性。

* **第1代和第2代 Vm 之間是否有價格差異？**  
   否。

* **我有來自內部部署第2代 VM 的 .vhd 檔案。我可以在 Azure 中使用該 .vhd 檔案來建立第2代 VM 嗎？**
  是，您可以將第2代 .vhd 檔案帶入 Azure，並使用它來建立第2代 VM。 請使用下列步驟來執行這項操作：
    1. 將 .vhd 上傳至您要在其中建立 VM 的相同區域中的儲存體帳戶。
    1. 從 .vhd 檔案建立受控磁片。 將 [Hyper-v 產生] 屬性設為 V2。 下列 PowerShell 命令會在建立受控磁片時設定 Hyper-v 產生屬性。

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. 當磁片可供使用之後，請連接此磁片來建立 VM。 建立的 VM 將會是第2代 VM。
    建立第2代 VM 時，您可以選擇性地將此 VM 的映射一般化。 藉由將映射一般化，您就可以使用它來建立多個 Vm。

* **如何? 增加 OS 磁片大小嗎？**  
  大於 2 TB 的 OS 磁片是第2代 Vm 的新手。 根據預設，第2代 Vm 的 OS 磁片小於 2 TB。 您可以將磁片大小增加到最多 4 TB 的建議。 使用 Azure CLI 或 Azure 入口網站來增加 OS 磁片大小。 如需如何以程式設計方式擴充磁片的相關資訊，請參閱[調整磁片大小](expand-os-disk.md)。

  若要增加 Azure 入口網站的 OS 磁片大小：

  1. 在 Azure 入口網站中，移至 [VM 屬性] 頁面。
  1. 若要關閉和解除配置 VM，請選取 [**停止**] 按鈕。
  1. 在 [**磁片**] 區段中，選取您想要增加的 OS 磁片。
  1. 在 [**磁片**] 區段中 **，選取 [** 設定]，並將**大小**更新為您想要的值。
  1. 回到 VM 的 [屬性] 頁面並**啟動**vm。
  
  對於大於 2 TB 的 OS 磁片，您可能會看到警告。 此警告不適用於第2代 Vm。 不過，*不建議*大於 4 TB 的 OS 磁片大小。

* **第2代 Vm 是否支援加速網路？**  
    可以。 如需詳細資訊，請參閱[建立具有加速網路的 VM](../../virtual-network/create-vm-accelerated-networking-cli.md)。

* **第2代支援 VHDX 嗎？**  
    否，第2代 Vm 僅支援 VHD。

* **第2代 Vm 是否支援 Azure Ultra 磁碟儲存體？**  
    可以。

* **我可以將 VM 從第1代遷移到第2代嗎？**  
    否，您無法在建立 VM 之後變更其產生。 如果您需要在 VM 層代之間切換，請建立不同世代的新 VM。

* **當我嘗試建立 Gen2 VM 時，為什麼大小選取器中未啟用我的 VM 大小？**

    這可以藉由執行下列動作來解決：

    1. 確認 [ **VM 世代**] 屬性在 [ **Advanced** ] 索引標籤中設定為 [ **Gen 2** ]。
    1. 確認您正在搜尋[支援 Gen2 vm 的 VM 大小](#generation-2-vm-sizes)。

## <a name="next-steps"></a>後續步驟

* 瞭解[hyper-v 中的第2代虛擬機器](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)。

* 瞭解如何[準備 VHD](prepare-for-upload-vhd-image.md)以從內部部署系統上傳至 Azure。
