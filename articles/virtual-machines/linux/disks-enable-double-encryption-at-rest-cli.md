---
title: 啟用靜態加密-Azure CLI 受控磁片
description: 使用 Azure CLI，為您的受控磁片資料啟用靜態加密。
author: roygara
ms.date: 07/10/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 25f4f99d2bd80034d2f337aa649417d2d06026be
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87082523"
---
# <a name="azure-cli---enable-double-encryption-at-rest-on-your-managed-disks"></a>Azure CLI-在受控磁片上啟用靜止的雙重加密

Azure 磁碟儲存體支援適用于受控磁片的雙重靜態加密。 如需有關靜態加密和其他受控磁片加密類型的概念資訊，請參閱磁片加密一文中的[雙重加密](disk-encryption.md#double-encryption-at-rest)待用一節。

## <a name="supported-regions"></a>支援的區域

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](../../../includes/virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="prerequisites"></a>先決條件

安裝最新的[Azure CLI](/cli/azure/install-az-cli2) ，並使用[az login](/cli/azure/reference-index)來登入 Azure 帳戶。

## <a name="getting-started"></a>開始使用

1. 建立 Azure Key Vault 執行個體和加密金鑰。

    建立 Key Vault 執行個體時，您必須啟用虛刪除和清除保護。 虛刪除可確保 Key Vault 將已刪除的金鑰保留一段指定的保留期間 (預設為 90 天)。 清除保護可確保已刪除的金鑰在保留期限結束之前，無法永久刪除。 這些設定可防止您因為意外刪除而遺失資料。 使用 Key Vault 來加密受控磁碟時，這些設定是必要的。

    
    ```azurecli
    subscriptionId=yourSubscriptionID
    rgName=yourResourceGroupName
    location=westcentralus
    keyVaultName=yourKeyVaultName
    keyName=yourKeyName
    diskEncryptionSetName=yourDiskEncryptionSetName
    diskName=yourDiskName

    az account set --subscription $subscriptionId

    az keyvault create -n $keyVaultName -g $rgName -l $location --enable-purge-protection true --enable-soft-delete true

    az keyvault key create --vault-name $keyVaultName -n $keyName --protection software
    ```

1.    建立 DiskEncryptionSet，並將 encryptionType 設定為 EncryptionAtRestWithPlatformAndCustomerKeys。 在 Azure Resource Manager （ARM）範本中使用 API 版本**2020-05-01** 。 
    
        ```azurecli
        az group deployment create -g $rgName \
       --template-uri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/DoubleEncryption/CreateDiskEncryptionSetForDoubleEncryption.json" \
        --parameters "diskEncryptionSetName=$diskEncryptionSetName" "encryptionType=EncryptionAtRestWithPlatformAndCustomerKeys" "keyVaultId=$keyVaultId" "keyVaultKeyUrl=$keyVaultKeyUrl" "region=$location"
        ```

1.    將 DiskEncryptionSet 資源存取權授與金鑰保存庫。 

        > [!NOTE]
        > Azure 可能需要幾分鐘的時間，才能在您的 Azure Active Directory 中建立 DiskEncryptionSet 的身分識別。 如果您在執行以下命令時收到「找不到 Active Directory 物件」之類的錯誤，請稍候幾分鐘再重試。

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)

        az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get
        ```

## <a name="next-steps"></a>後續步驟

既然您已建立並設定這些資源，您可以使用它們來保護您的受控磁片。 下列連結包含範例腳本，每個都有個別的案例，可讓您用來保護受控磁片。

- [Azure Resource Manager 範本範例](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
- [使用伺服器端加密來啟用客戶管理的金鑰-範例](disks-enable-customer-managed-keys-cli.md#examples)