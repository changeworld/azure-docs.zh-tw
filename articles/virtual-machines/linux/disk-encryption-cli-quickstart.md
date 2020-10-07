---
title: 使用 Azure CLI 來建立和加密 Linux VM
description: 在本快速入門中，您將了解如何使用 Azure CLI 建立和加密 Linux 虛擬機器
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: b07ddd3ec98e2094eb3a463501f42d65e15370e6
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "87502353"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-with-the-azure-cli"></a>快速入門：使用 Azure CLI 來建立和加密 Linux VM

Azure CLI 可用來從命令列或在指令碼中建立和管理 Azure 資源。 本快速入門示範如何使用 Azure CLI 建立和加密 Linux 虛擬機器 (VM)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

如果您選擇在本機安裝和使用 Azure CLI，本快速入門會要求您執行 Azure CLI 2.0.30 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create) 命令來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 下列範例會在 eastus  位置建立名為 myResourceGroup  的資源群組：

```azurecli-interactive
az group create --name "myResourceGroup" --location "eastus"
```

## <a name="create-a-virtual-machine"></a>建立虛擬機器

使用 [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create) 建立 VM。 下列範例會建立名為 myVM  的 VM。

```azurecli-interactive
az vm create \
    --resource-group "myResourceGroup" \
    --name "myVM" \
    --image "Canonical:UbuntuServer:16.04-LTS:latest" \
    --size "Standard_D2S_V3"\
    --generate-ssh-keys
```

建立虛擬機器和支援資源需要幾分鐘的時間。 下列範例輸出顯示 VM 建立作業成功。

```
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>建立為加密金鑰設定的金鑰保存庫

Azure 磁碟加密會將其加密金鑰儲存在 Azure Key Vault 中。 使用 [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) 建立金鑰保存庫。 若要啟用金鑰保存庫來儲存加密金鑰，請使用 --enabled-for-disk-encryption 參數。

> [!Important]
> 每個金鑰保存庫都必須有在 Azure 中唯一的名稱。 在下列範例中，請將 <your-unique-keyvault-name> 取代為您選擇的名稱。

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>將虛擬機器加密

使用 [az vm encryption](/cli/azure/vm/encryption?view=azure-cli-latest) 加密您的 VM，提供您唯一的金鑰保存庫名稱給 --disk-encryption-keyvault 參數。

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>"
```

隨後會傳回處理程序，「已接受加密要求。 請使用 'show' 命令來監視進度。」 "show" 命令是 [az vm show](/cli/azure/vm/encryption#az-vm-encryption-show)。

```azurecli-interactive
az vm show --name "myVM" -g "MyResourceGroup"
```

啟用加密時，您會在傳回的輸出中看到下列：

```
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以使用 [az group delete](/cli/azure/group) 命令來移除資源群組、VM 和金鑰保存庫。 

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您會建立虛擬機器、建立為加密金鑰啟用的金鑰保存庫，並加密 VM。  繼續閱讀下一篇文章，以深入了解 Linux VM 的 Azure 磁碟加密。

> [!div class="nextstepaction"]
> [Azure 磁碟加密概觀](disk-encryption-overview.md)
