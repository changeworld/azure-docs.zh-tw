---
title: 尋找及刪除未連結的 Azure 受控和非受控磁碟
description: 如何使用 Azure PowerShell 尋找及刪除未連接的 Azure 受控和非受控 (VHD/分頁 Blob) 磁碟。
author: roygara
ms.service: virtual-machines-windows
ms.topic: how-to
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: ea3a4452b87fbb1c8663a66ef29c4e9fb891a6b3
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.contentlocale: zh-TW
ms.lasthandoff: 06/02/2020
ms.locfileid: "84300632"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>尋找及刪除未連結的 Azure 受控和非受控磁碟

當您在 Azure 中刪除虛擬機器 (VM) 時，不會刪除任何連結至 VM 的磁碟。 這項功能有助於預防因為不小心刪除 VM 所造成的資料遺失。 刪除 VM 之後，您將繼續支付未連結的磁碟。 本文示範如何尋找及刪除任何未連結的磁碟，並減少不必要的成本。

## <a name="managed-disks-find-and-delete-unattached-disks"></a>受控磁碟：尋找及刪除未連結的磁碟

下列指令碼會藉由檢查 **ManagedBy** 屬性的值，以尋找未連結的[受控磁碟](managed-disks-overview.md)。 如果受控磁碟已連結至 VM，**ManagedBy** 屬性包含 VM 的資源 ID。 如果未連結受控磁碟，則 **ManagedBy** 屬性為 null。 指令碼會檢查 Azure 訂用帳戶中的所有受控磁碟。 當指令碼找到 **ManagedBy** 屬性設為 null 的受控磁碟時，指令碼會判定該磁碟並未連結。

>[!IMPORTANT]
>首先，將 **deleteUnattachedDisks** 變數設為 0 來執行指令碼。 這個動作可讓您尋找和檢視所有未連結的受控磁碟。
>
>檢閱所有未連結磁碟之後，請再次執行指令碼並將 **deleteUnattachedDisks** 變數設為 1。 這個動作可讓您刪除所有未連結的受控磁碟。

```azurepowershell-interactive
# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
$deleteUnattachedDisks=0
$managedDisks = Get-AzDisk
foreach ($md in $managedDisks) {
    # ManagedBy property stores the Id of the VM to which Managed Disk is attached to
    # If ManagedBy property is $null then it means that the Managed Disk is not attached to a VM
    if($md.ManagedBy -eq $null){
        if($deleteUnattachedDisks -eq 1){
            Write-Host "Deleting unattached Managed Disk with Id: $($md.Id)"
            $md | Remove-AzDisk -Force
            Write-Host "Deleted unattached Managed Disk with Id: $($md.Id) "
        }else{
            $md.Id
        }
    }
 }
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>非受控磁碟：尋找及刪除未連結的磁碟

非受控磁碟是 VHD 檔案，會以[分頁 Blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) 的形式儲存在 [Azure 儲存體帳戶](../../storage/common/storage-account-overview.md)中。 下列指令碼會藉由檢查 **LeaseStatus** 屬性的值，以尋找未連結的非受控磁碟 (分頁 Blob)。 如果非受控磁碟已連結至虛擬機器，則 **LeaseStatus** 屬性會設為 **Locked**。 如果未連結非受控磁碟，則 **LeaseStatus** 屬性會設為 **Unlocked**。 指令碼會檢查 Azure 訂用帳戶中所有 Azure 儲存體帳戶的所有非受控磁碟。 當指令碼找到 **LeaseStatus** 屬性設為 **Unlocked** 的非受控磁碟時，指令碼會判定該磁碟並未連結。

>[!IMPORTANT]
>首先，將 **deleteUnattachedVHDs** 變數設為 0 來執行指令碼。 這個動作可讓您尋找和檢視所有未連結的非受控 VHD。
>
>檢閱所有未連結磁碟之後，請再次執行指令碼並將 **deleteUnattachedVHDs** 變數設為 1。 這個動作可讓您刪除所有未連結的非受控 VHD。

```azurepowershell-interactive
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=0
$storageAccounts = Get-AzStorageAccount
foreach($storageAccount in $storageAccounts){
    $storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.StorageAccountName)[0].Value
    $context = New-AzStorageContext -StorageAccountName $storageAccount.StorageAccountName -StorageAccountKey $storageKey
    $containers = Get-AzStorageContainer -Context $context
    foreach($container in $containers){
        $blobs = Get-AzStorageBlob -Container $container.Name -Context $context
        #Fetch all the Page blobs with extension .vhd as only Page blobs can be attached as disk to Azure VMs
        $blobs | Where-Object {$_.BlobType -eq 'PageBlob' -and $_.Name.EndsWith('.vhd')} | ForEach-Object { 
            #If a Page blob is not attached as disk then LeaseStatus will be unlocked
            if($_.ICloudBlob.Properties.LeaseStatus -eq 'Unlocked'){
                    if($deleteUnattachedVHDs -eq 1){
                        Write-Host "Deleting unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                        $_ | Remove-AzStorageBlob -Force
                        Write-Host "Deleted unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                    }
                    else{
                        $_.ICloudBlob.Uri.AbsoluteUri
                    }
            }
        }
    }
}
```

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱[刪除儲存體帳戶](../../storage/common/storage-account-create.md#delete-a-storage-account)和[使用 PowerShell 識別孤立的磁碟](https://blogs.technet.microsoft.com/ukplatforms/2018/02/21/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell/)
