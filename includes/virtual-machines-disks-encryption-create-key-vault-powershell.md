---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c696bea902a25571c173d04e0eaa7304ed657151
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85610422"
---
1. 請確定您已安裝最新的 [Azure PowerShell 版本](/powershell/azure/install-az-ps)，而且已使用 Connect-AzAccount 登入 Azure 帳戶

1. 建立 Azure Key Vault 執行個體和加密金鑰。

    建立 Key Vault 執行個體時，您必須啟用虛刪除和清除保護。 虛刪除可確保 Key Vault 將已刪除的金鑰保留一段指定的保留期間 (預設為 90 天)。 清除保護可確保已刪除的金鑰在保留期限結束之前，無法永久刪除。 這些設定可防止您因為意外刪除而遺失資料。 使用 Key Vault 來加密受控磁碟時，必須使用這些設定。
    
    ```powershell
    $ResourceGroupName="yourResourceGroupName"
    $LocationName="westcentralus"
    $keyVaultName="yourKeyVaultName"
    $keyName="yourKeyName"
    $keyDestination="Software"
    $diskEncryptionSetName="yourDiskEncryptionSetName"

    $keyVault = New-AzKeyVault -Name $keyVaultName -ResourceGroupName $ResourceGroupName -Location $LocationName -EnableSoftDelete -EnablePurgeProtection

    $key = Add-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName -Destination $keyDestination  
    ```

1.    建立 DiskEncryptionSet 的執行個體。 
    
        ```powershell
        $desConfig=New-AzDiskEncryptionSetConfig -Location $LocationName -SourceVaultId $keyVault.ResourceId -KeyUrl $key.Key.Kid -IdentityType SystemAssigned
        
        $des=New-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -InputObject $desConfig 
        ```

1.    將 DiskEncryptionSet 資源存取權授與金鑰保存庫。

        > [!NOTE]
        > Azure 可能需要幾分鐘的時間，才能在您的 Azure Active Directory 中建立 DiskEncryptionSet 的身分識別。 如果您在執行以下命令時收到「找不到 Active Directory 物件」之類的錯誤，請稍候幾分鐘再重試。
        
        ```powershell  
        Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
        ```
