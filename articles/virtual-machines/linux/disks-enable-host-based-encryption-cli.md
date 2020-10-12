---
title: 在主機 Azure CLI 管理的磁片上使用加密來啟用端對端加密
description: 在主機上使用加密來啟用 Azure 受控磁片上的端對端加密。
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: ff56654981ef69648b1fa7ad11a8681c887289f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88816961"
---
# <a name="use-the-azure-cli-to-enable-end-to-end-encryption-using-encryption-at-host"></a>使用 Azure CLI 來啟用在主機使用加密的端對端加密

當您在主機上啟用加密時，儲存在 VM 主機上的資料會在待用時加密，並將流量加密至儲存體服務。 如需在主機上加密以及其他受控磁片加密類型的概念資訊，請參閱 [VM 資料的主機端對端](disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)加密加密。

## <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>支援區域

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>支援的 VM 大小

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

您也可以透過程式設計的方式找到 VM 大小。 若要瞭解如何以程式設計方式取得它們，請參閱 [尋找支援的 VM 大小](#finding-supported-vm-sizes) 一節。

## <a name="prerequisites"></a>Prerequisites

若要能夠針對您的 Vm 或虛擬機器擴展集使用主機加密，您必須在訂用帳戶上啟用此功能。 傳送電子郵件給 encryptionAtHost@microsoft.com 您的訂用帳戶識別碼，以取得訂用帳戶啟用的功能。

### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>建立 Azure Key Vault 和 DiskEncryptionSet

啟用此功能之後，您必須設定 Azure Key Vault 和 DiskEncryptionSet （如果尚未安裝）。

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-cli](../../../includes/virtual-machines-disks-encryption-create-key-vault-cli.md)]

## <a name="examples"></a>範例

### <a name="create-a-vm-with-encryption-at-host-enabled-with-customer-managed-keys"></a>使用客戶管理的金鑰，在主機上建立具有加密功能的 VM。 

使用稍早建立的 DiskEncryptionSet 資源 URI 來建立具有受控磁片的 VM，以使用客戶管理的金鑰來加密作業系統和資料磁片的快取。 暫存磁片會使用平臺管理的金鑰進行加密。 

```azurecli
rgName=yourRGName
vmName=yourVMName
location=eastus
vmSize=Standard_DS2_v2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskEncryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName \
-n $vmName \
-l $location \
--encryption-at-host \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--os-disk-encryption-set $diskEncryptionSetId \
--data-disk-sizes-gb 128 128 \
--data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-a-vm-with-encryption-at-host-enabled-with-platform-managed-keys"></a>使用平臺管理的金鑰，在主機上建立具有加密功能的 VM。 

建立已啟用「在主機加密」的 VM，以使用平臺管理的金鑰來加密作業系統/資料磁片和暫存磁片的快取。 

```azurecli
rgName=yourRGName
vmName=yourVMName
location=eastus
vmSize=Standard_DS2_v2
image=UbuntuLTS 

az vm create -g $rgName \
-n $vmName \
-l $location \
--encryption-at-host \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--data-disk-sizes-gb 128 128 \
```

### <a name="update-a-vm-to-enable-encryption-at-host"></a>更新 VM 以啟用主機的加密。 

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm update -n $vmName \
-g $rgName \
--set securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-vm"></a>檢查 VM 在主機上的加密狀態

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm show -n $vmName \
-g $rgName \
--query [securityProfile.encryptionAtHost] -o tsv
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-customer-managed-keys"></a>使用客戶管理的金鑰，在主機上建立具有加密功能的虛擬機器擴展集。 

使用稍早建立之 DiskEncryptionSet 的資源 URI 來建立具有受控磁片的虛擬機器擴展集，以使用客戶管理的金鑰來加密作業系統和資料磁片的快取。 暫存磁片會使用平臺管理的金鑰進行加密。 

```azurecli
rgName=yourRGName
vmssName=yourVMSSName
location=westus2
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskEncryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--admin-username azureuser \
--generate-ssh-keys \
--os-disk-encryption-set $diskEncryptionSetId \
--data-disk-sizes-gb 64 128 \
--data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-platform-managed-keys"></a>使用平臺管理的金鑰，在主機上建立具有加密功能的虛擬機器擴展集。 

在啟用主機加密的情況下建立虛擬機器擴展集，以使用平臺管理的金鑰來加密作業系統/資料磁片和暫存磁片的快取。 

```azurecli
rgName=yourRGName
vmssName=yourVMSSName
location=westus2
vmSize=Standard_DS3_V2
image=UbuntuLTS 

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--admin-username azureuser \
--generate-ssh-keys \
--data-disk-sizes-gb 64 128 \
```

### <a name="update-a-virtual-machine-scale-set-to-enable-encryption-at-host"></a>更新虛擬機器擴展集，以啟用主機的加密。 

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss update -n $vmssName \
-g $rgName \
--set virtualMachineProfile.securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-virtual-machine-scale-set"></a>檢查虛擬機器擴展集的主機加密狀態

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss show -n $vmssName \
-g $rgName \
--query [virtualMachineProfile.securityProfile.encryptionAtHost] -o tsv
```

## <a name="finding-supported-vm-sizes"></a>尋找支援的 VM 大小

不支援舊版 VM 大小。 您可以透過下列方式尋找支援的 VM 大小清單：

呼叫 [資源 SKU API](/rest/api/compute/resourceskus/list) ，並檢查 `EncryptionAtHostSupported` 功能是否設定為 **True**。

```json
    {
        "resourceType": "virtualMachines",
        "name": "Standard_DS1_v2",
        "tier": "Standard",
        "size": "DS1_v2",
        "family": "standardDSv2Family",
        "locations": [
        "CentralUSEUAP"
        ],
        "capabilities": [
        {
            "name": "EncryptionAtHostSupported",
            "value": "True"
        }
        ]
    }
```

或者，呼叫 [>get-azcomputeresourcesku](/powershell/module/az.compute/get-azcomputeresourcesku?view=azps-3.8.0) PowerShell Cmdlet。

```powershell
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
    foreach($capability in $vmSize.capabilities)
    {
        if($capability.Name -eq 'EncryptionAtHostSupported' -and $capability.Value -eq 'true')
        {
            $vmSize

        }

    }
}
```

## <a name="next-steps"></a>接下來的步驟

現在您已建立並設定這些資源，您可以使用它們來保護受控磁片。 下列連結包含範例腳本，每個都有各自的案例，可讓您用來保護您的受控磁片。

[Azure Resource Manager 範本範例](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
