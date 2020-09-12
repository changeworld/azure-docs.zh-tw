---
title: 使用入口網站或 PowerShell 建立虛擬硬碟的快照集
description: 瞭解如何建立 Azure VM 複本作為備份，或使用入口網站或 PowerShell 針對問題進行疑難排解。
author: roygara
manager: twooley
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/08/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: c0fddca70ca09f0a3abb3c84672e20a42551ecd5
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89322308"
---
# <a name="create-a-snapshot-using-the-portal-or-powershell"></a>使用入口網站或 PowerShell 建立快照集

快照集是完整的虛擬硬碟 (VHD) 唯讀複本。 您可以製作 OS 或資料磁碟 VHD 的快照集當作備份，或用來針對虛擬機器 (VM) 問題進行疑難排解。

如果您將使用快照集來建立新 VM，建議您完全關閉 VM 後再建立快照集，以清除所有進行中的處理序。

## <a name="use-the-azure-portal"></a>使用 Azure 入口網站 

若要建立快照集，請完成下列步驟： 
1.  在 [ [Azure 入口網站](https://portal.azure.com)上，選取 [ **建立資源**]。
2. 搜尋並選取 [ **快照**集]。
3. 在 [快照集]**** 視窗中，選取 [建立]****。 [建立快照集]**** 視窗隨即出現。
4. 輸入快照集的 [名稱]****。
5. 選取現有的[資源群組](../../azure-resource-manager/management/overview.md#resource-groups)，或輸入新資源群組的名稱。 
6. 選取 Azure 資料中心的 [位置]****。  
7. 在 [來源磁碟]**** 中，選取要建立快照集的受控磁碟。
8. 選取用來儲存快照集的 [帳戶類型]****。 除非需要將快照集存放在高效能磁碟上，否則選取 [Standard_HDD]****。
9. 選取 [建立]。

## <a name="use-powershell"></a>使用 PowerShell

下列步驟示範如何複製 VHD 磁片並建立快照集設定。 然後，您可以使用 [>new-azsnapshot](/powershell/module/az.compute/new-azsnapshot) Cmdlet 來建立磁片的快照集。 

 

1. 設定一些參數： 

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

2. 取得 VM：

   ```azurepowershell-interactive
   $vm = Get-AzVM `
       -ResourceGroupName $resourceGroupName `
       -Name $vmName
   ```

3. 建立快照集組態。 在此範例中，快照集屬於 OS 磁碟：

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig `
       -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
       -Location $location `
       -CreateOption copy
   ```
   
   > [!NOTE]
   > 如果您想要將快照集儲存於區域復原的儲存體中，請在支援[可用性區域](../../availability-zones/az-overview.md)且包含 `-SkuName Standard_ZRS` 參數的區域中建立它。   
   
4. 製作快照集：

   ```azurepowershell-interactive
   New-AzSnapshot `
       -Snapshot $snapshot `
       -SnapshotName $snapshotName `
       -ResourceGroupName $resourceGroupName 
   ```


## <a name="next-steps"></a>接下來的步驟

從快照集建立受控磁碟，然後連結新的受控磁碟作為 OS 磁碟，以從快照集建立虛擬機器。 如需詳細資訊，請參閱[使用 PowerShell 從快照集建立 VM](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)。
