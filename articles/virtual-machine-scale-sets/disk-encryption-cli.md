---
title: 使用 Azure CLI 加密 Azure 擴展集的磁碟
description: 了解如何使用 Azure PowerShell 加密 Windows 虛擬機器擴展集中的 VM 執行個體和已連結的磁碟
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/15/2019
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: dfb8a0dc6c27b042319f43bc5970dd8d1bc2b094
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496945"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli"></a>使用 Azure CLI 加密虛擬機器擴展集中的 OS 與連結的資料磁碟

Azure CLI 可用來從命令列或在指令碼中建立和管理 Azure 資源。 本快速入門示範如何使用 Azure CLI 建立和加密虛擬機器擴展集。 如需將 Azure 磁碟加密套用至虛擬機器擴展集的詳細資訊，請參閱[適用於虛擬機器擴展集的 Azure 磁碟加密](disk-encryption-overview.md)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本教學課程會要求您執行 Azure CLI 2.0.31 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="create-a-scale-set"></a>建立擴展集

請先使用 [az group create](/cli/azure/group) 建立資源群組，才可以建立擴展集。 下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組：

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

現在使用 [az vmss create](/cli/azure/vmss) 建立虛擬機器擴展集。 下列範例會建立名為 myScaleSet 的擴展集，其已設定為在套用變更時自動更新，並在 ~/.ssh/id_rsa 中沒有 SSH 金鑰時產生 SSH 金鑰。 系統會將 32 Gb 資料磁碟連結至每個 VM 執行個體，並使用 Azure [自訂指令碼擴充功能](../virtual-machines/extensions/custom-script-linux.md)搭配 [az vmss extension set](/cli/azure/vmss/extension) 來準備資料磁碟：

```azurecli-interactive
# Create a scale set with attached data disk
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 32

# Prepare the data disk for use with the Custom Script Extension
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

建立及設定所有擴展集資源和 VM 需要幾分鐘的時間。

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>建立針對磁碟加密啟用的 Azure 金鑰保存庫

Azure 金鑰保存庫儲存可讓您安全地在應用程式和服務中實作的金鑰和密碼 (Secret 或 Password)。 密碼編譯金鑰會使用軟體保護功能儲存在 Azure 金鑰保存庫中，或者您可以在 FIPS 140-2 第 2 級標準認證的硬體安全性模組 (HSM) 中匯入或產生金鑰。 這些密碼編譯金鑰用來加密及解密連接到 VM 的虛擬磁碟。 您可保留這些密碼編譯金鑰的控制權，並可稽核其使用情況。

定義您自己的唯一 *keyvault_name*。 接著，在與擴展集相同的訂用帳戶和區域中，使用 [az keyvault create](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-create) 建立 KeyVault，並設定 *--enabled-for-disk-encryption* 存取原則。

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>使用現有的 Key Vault

如果您有想要用於磁碟加密的現有 Key Vault，才需要這個步驟。 如果您已在上一節中建立 Key Vault，請略過此步驟。

定義您自己的唯一 *keyvault_name*。 接著，使用 [az keyvault update](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-update) 更新您的 KeyVault，並設定 *--enabled-for-disk-encryption* 存取原則。

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>啟用加密

若要加密擴展集中的 VM 執行個體，請先使用 [az keyvault show](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-show) 取得 Key Vault 資源識別碼的部分資訊。 然後使用這些變數搭配 [az vmss encryption enable](/cli/azure/vmss/encryption#az-vmss-encryption-enable) 開始執行加密程序：

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

加密程序開始可能需要一兩分鐘的時間。

由於擴展集是在先前步驟設定為「自動」時建立的擴展集上的升級原則，因此 VM 執行個體會自動開始執行加密程序。 在升級原則為手動的擴展集上，使用 [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances) 對 VM 執行個體啟動加密原則。

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>使用 KEK 包裝金鑰來啟用加密

加密虛擬機器擴展集時，您也可以使用金鑰加密金鑰來增加安全性。

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --key-encryption-key myKEK \
    --key-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

> [!NOTE]
>  disk-encryption-keyvault 參數值的語法是完整的識別碼字串：</br>
/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> key-encryption-key 參數值的語法是 KEK 的完整 URI，如下所示：</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## <a name="check-encryption-progress"></a>檢查加密程序

若要檢查磁碟加密的狀態，請使用 [az vmss encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show)：

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

當 VM 執行個體經過加密時，狀態碼會回報 *EncryptionState/encrypted*，如下列範例輸出所示：

```bash
[
  {
    "disks": [
      {
        "encryptionSettings": null,
        "name": "myScaleSet_myScaleSet_0_disk2_3f39c2019b174218b98b3dfae3424e69",
        "statuses": [
          {
            "additionalProperties": {},
            "code": "EncryptionState/encrypted",
            "displayStatus": "Encryption is enabled on disk",
            "level": "Info",
            "message": null,
            "time": null
          }
        ]
      }
    ],
    "id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualMachines/0",
    "resourceGroup": "MYRESOURCEGROUP"
  }
]
```

## <a name="disable-encryption"></a>停用加密

如果您不想再使用加密的 VM 執行個體磁碟，則可以如下所示，使用 [az vmss encryption disable](/cli/azure/vmss/encryption?view=azure-cli-latest#az-vmss-encryption-disable) 來停用加密：

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>後續步驟

- 在此文章中，您使用 Azure CLI 加密虛擬機器擴展集。 您也可以使用 [Azure PowerShell](disk-encryption-powershell.md) 或 [Azure Resource Manager 範本](disk-encryption-azure-resource-manager.md)。
- 如果您想要在佈建其他擴充功能之後套用 Azure 磁碟加密，則可以使用[擴充功能排序](virtual-machine-scale-sets-extension-sequencing.md)。 
- 您可以在[這裡](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat)找到 Linux 擴展集資料磁碟加密的端對端批次檔案範例。 這個範例會建立資源群組和 Linux 擴展集、掛接 5 GB 資料磁碟，並加密虛擬機器擴展集。
