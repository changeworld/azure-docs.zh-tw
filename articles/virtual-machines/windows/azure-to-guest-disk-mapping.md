---
title: 如何將 Azure 磁片對應至 Windows VM 來賓磁片
description: 如何判斷基礎 Windows VM 之來賓磁片的 Azure 磁片。
author: timbasham
ms.service: virtual-machines-windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/17/2020
ms.author: tibasham
ms.openlocfilehash: f16e34f372016f284d4af79443e84d9d5cdea957
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523475"
---
# <a name="how-to-map-azure-disks-to-windows-vm-guest-disks"></a>如何將 Azure 磁片對應至 Windows VM 來賓磁片

您可能需要判斷傳回 VM 之來賓磁片的 Azure 磁片。 在某些情況下，您可以比較磁片或磁片區大小與連接的 Azure 磁片大小。 如果有多個相同大小的 Azure 磁片連接到 VM，您必須使用邏輯單元編號 (資料磁片的 LUN) 。 

## <a name="what-is-a-lun"></a>什麼是 LUN？

邏輯單元編號 (LUN) 是用來識別特定存放裝置的數位。 每個儲存裝置都會被指派一個唯一的數值識別碼，從零開始。 裝置的完整路徑會以匯流排號碼、目標識別碼和邏輯單元編號表示， (LUN) 。 

例如： ***匯流排編號0、目標識別碼0、LUN 3***

針對我們的練習，您只需要使用 LUN。

## <a name="finding-the-lun"></a>尋找 LUN

有兩種方法可以尋找 LUN，如果您使用的是 [儲存空間](https://docs.microsoft.com/windows-server/storage/storage-spaces/overview) ，您選擇哪一種方法會取決於您所選擇的 LUN。

### <a name="disk-management"></a>磁碟管理

如果您不是使用儲存集區，則可以使用 [ [磁片管理](https://docs.microsoft.com/windows-server/storage/disk-management/overview-of-disk-management) ] 來尋找 LUN。

1. 連接至 VM，並開啟磁片管理。 以滑鼠右鍵按一下 [開始] 按鈕，然後選擇 [磁片管理]。 您也可以在 `diskmgmt.msc` [開始搜尋] 方塊中輸入
1. 在下方窗格中，以滑鼠右鍵按一下任何磁片，然後選擇 [屬性]
1. LUN 將會列在 [一般] 索引標籤的 [位置] 屬性中

### <a name="storage-pools"></a>儲存集區

1. 連接至 VM 並開啟伺服器管理員
1. 選取「檔案和存放服務」、「磁片區」、「儲存集區」
1. 伺服器管理員的右下角會有一個「實體磁片」區段。 下表列出組成存放集區的磁片，以及每個磁片的 LUN。

## <a name="finding-the-lun-for-the-azure-disks"></a>尋找 Azure 磁片的 LUN

您可以使用 Azure 入口網站、Azure CLI 或 Azure PowerShell 來找出 Azure 磁片的 LUN

### <a name="finding-an-azure-disks-lun-in-the-azure-portal"></a>在 Azure 入口網站中尋找 Azure 磁片的 LUN

1. 在 Azure 入口網站中，選取 [虛擬機器] 以顯示您的虛擬機器清單
1. 選取虛擬機器
1. 選取 [磁片]
1. 從已連接的磁片清單中選取資料磁片。
1. 磁片的 LUN 將會顯示在 [磁片詳細資料] 窗格中。 此處顯示的 LUN 與使用裝置管理員或伺服器管理員在來賓中查閱的 Lun 相關聯。

### <a name="finding-an-azure-disks-lun-using-azure-cli-or-azure-powershell"></a>使用 Azure CLI 或 Azure PowerShell 尋找 Azure 磁片的 LUN

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli-interactive
az vm show -g myResourceGroup -n myVM --query "storageProfile.dataDisks"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
$vm.StorageProfile.DataDisks | ft
```
---
