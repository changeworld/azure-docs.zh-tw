---
title: 尋找及刪除未連結的 Azure 受控和非受控磁碟
description: 如何使用 Azure CLI 尋找及刪除未連結的 Azure 受控和非受控 (VHD/分頁 Blob) 磁碟。
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/30/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 821ba551dc19f92988a352b8f1bab792ce52207b
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978912"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks-using-the-azure-cli"></a>使用 Azure CLI 尋找及刪除未連結的 Azure 受控和非受控磁碟
當您在 Azure 中刪除虛擬機器 (VM) 時，不會刪除任何連結至 VM 的磁碟。 這項功能有助於預防因為不小心刪除 VM 所造成的資料遺失。 刪除 VM 之後，您將繼續支付未連結的磁碟。 本文示範如何尋找及刪除任何未連結的磁碟，並減少不必要的成本。 


## <a name="managed-disks-find-and-delete-unattached-disks"></a>受控磁碟：尋找及刪除未連結的磁碟 

下列指令碼會藉由檢查 **ManagedBy** 屬性的值，以尋找未連結的[受控磁碟](../managed-disks-overview.md)。 如果受控磁碟已連結至 VM，**ManagedBy** 屬性包含 VM 的資源 ID。 如果未連結受控磁碟，則 **ManagedBy** 屬性為 null。 指令碼會檢查 Azure 訂用帳戶中的所有受控磁碟。 當指令碼找到 **ManagedBy** 屬性設為 null 的受控磁碟時，指令碼會判定該磁碟並未連結。

>[!IMPORTANT]
>首先，將 **deleteUnattachedDisks** 變數設為 0 來執行指令碼。 這個動作可讓您尋找和檢視所有未連結的受控磁碟。
>
>檢閱所有未連結磁碟之後，請再次執行指令碼並將 **deleteUnattachedDisks** 變數設為 1。 這個動作可讓您刪除所有未連結的受控磁碟。
>

```azurecli

# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
deleteUnattachedDisks=0

unattachedDiskIds=$(az disk list --query '[?managedBy==`null`].[id]' -o tsv)
for id in ${unattachedDiskIds[@]}
do
    if (( $deleteUnattachedDisks == 1 ))
    then

        echo "Deleting unattached Managed Disk with Id: "$id
        az disk delete --ids $id --yes
        echo "Deleted unattached Managed Disk with Id: "$id

    else
        echo $id
    fi
done
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>非受控磁碟：尋找及刪除未連結的磁碟 

非受控磁碟是 VHD 檔案，會以[分頁 Blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) 的形式儲存在 [Azure 儲存體帳戶](../../storage/common/storage-account-overview.md)中。 下列指令碼會藉由檢查 **LeaseStatus** 屬性的值，以尋找未連結的非受控磁碟 (分頁 Blob)。 如果非受控磁碟已連結至虛擬機器，則 **LeaseStatus** 屬性會設為 **Locked**。 如果未連結非受控磁碟，則 **LeaseStatus** 屬性會設為 **Unlocked**。 指令碼會檢查 Azure 訂用帳戶中所有 Azure 儲存體帳戶的所有非受控磁碟。 當指令碼找到 **LeaseStatus** 屬性設為 **Unlocked** 的非受控磁碟時，指令碼會判定該磁碟並未連結。

>[!IMPORTANT]
>首先，將 **deleteUnattachedVHDs** 變數設為 0 來執行指令碼。 這個動作可讓您尋找和檢視所有未連結的非受控 VHD。
>
>檢閱所有未連結磁碟之後，請再次執行指令碼並將 **deleteUnattachedVHDs** 變數設為 1。 這個動作可讓您刪除所有未連結的非受控 VHD。
>

```azurecli
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the details of the unattached VHDs
deleteUnattachedVHDs=0

storageAccountIds=$(az storage account list --query [].[id] -o tsv)

for id in ${storageAccountIds[@]}
do
    connectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
    containers=$(az storage container list --connection-string $connectionString --query [].[name] -o tsv)

    for container in ${containers[@]}
    do 
        
        blobs=$(az storage blob list -c $container --connection-string $connectionString --query "[?properties.blobType=='PageBlob' && ends_with(name,'.vhd')].[name]" -o tsv)
        
        for blob in ${blobs[@]}
        do
            leaseStatus=$(az storage blob show -n $blob -c $container --connection-string $connectionString --query "properties.lease.status" -o tsv)
            
            if [ "$leaseStatus" == "unlocked" ]
            then 

                if (( $deleteUnattachedVHDs == 1 ))
                then 

                    echo "Deleting VHD: "$blob" in container: "$container" in storage account: "$id

                    az storage blob delete --delete-snapshots include  -n $blob -c $container --connection-string $connectionString

                    echo "Deleted VHD: "$blob" in container: "$container" in storage account: "$id
                else
                    echo "StorageAccountId: "$id" container: "$container" VHD: "$blob
                fi

            fi
        done
    done
done 
```

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱[刪除儲存體帳戶](../../storage/common/storage-account-create.md#delete-a-storage-account)。