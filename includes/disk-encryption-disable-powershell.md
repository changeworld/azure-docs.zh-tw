---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/19/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 1e3cdfccc36549d2aa127636b15b698a4e19ccf0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85610554"
---
您可以使用 Azure PowerShell、Azure CLI 或 Resource Manager 範本來停用加密。 當作業系統和資料磁碟都已加密，在 Windows VM 上停用資料磁碟加密將無法按預期運作。 改為停用所有磁碟上的加密。

- **停用使用 Azure PowerShell 的磁碟加密：** 若要停用加密，請使用 [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) Cmdlet。 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **使用 Azure CLI 停用加密：** 若要停用加密，請使用 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 命令。 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **透過 Resource Manager 範本停用加密：** 

    1. 從[在執行中的 Windows VM 上停用磁碟加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad)範本，按一下 [部署至 Azure]****。
    2. 選取訂用帳戶、資源群組、位置、VM、磁碟區類型、法律條款及合約。
    3.  按一下 [購買]**** 以在執行中的 Windows VM 上停用磁碟加密。 
