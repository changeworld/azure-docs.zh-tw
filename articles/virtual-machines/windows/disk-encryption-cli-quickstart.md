---
title: 使用 Azure CLI 建立和加密 Windows 虛擬機器
description: 在本快速入門中，您將了解如何使用 Azure CLI 建立和加密 Windows 虛擬機器
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0156a1d7cea477a1725b60a5e1de229e76d2664c
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94517983"
---
# <a name="quickstart-create-and-encrypt-a-windows-vm-with-the-azure-cli"></a>快速入門：使用 Azure CLI 建立和加密 Windows 虛擬機器

Azure CLI 可用來從命令列或在指令碼中建立和管理 Azure 資源。 本快速入門示範如何使用 Azure CLI 建立和加密 Windows Server 2016 虛擬機器 (VM)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 2.0.30 版或更新版本的 Azure CLI。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。

## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create) 命令來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 下列範例會在 eastus  位置建立名為 myResourceGroup  的資源群組：

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>建立虛擬機器

使用 [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create) 建立 VM。 下列範例會建立名為 myVM  的 VM。 此範例會以 azureuser  作為系統管理使用者名稱並以 myPassword12  作為密碼。

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword12
```

建立虛擬機器和支援資源需要幾分鐘的時間。 下列範例輸出顯示 VM 建立作業成功。

```console
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
> 每個金鑰保存庫必須有唯一的名稱。 下列範例會建立名為 *myKV* 的金鑰保存庫，但您必須為自己的金鑰保存庫命名不同名稱。

```azurecli-interactive
az keyvault create --name "myKV" --resource-group "myResourceGroup" --location eastus --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>將虛擬機器加密

使用 [az vm encryption](/cli/azure/vm/encryption?view=azure-cli-latest) 加密您的 VM，提供您唯一的金鑰保存庫名稱給 --disk-encryption-keyvault 參數。

```azurecli-interactive
az vm encryption enable -g MyResourceGroup --name MyVM --disk-encryption-keyvault myKV
```

您可以確認您的 VM 上啟用加密[az vm show](/cli/azure/vm/encryption#az-vm-encryption-show)

```azurecli-interactive
az vm encryption show --name MyVM -g MyResourceGroup
```

您也會在傳回的輸出中看到下列文字：

```console
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以使用 [az group delete](/cli/azure/group) 命令來移除資源群組、VM 和金鑰保存庫。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您會建立虛擬機器、建立為加密金鑰啟用的金鑰保存庫，並加密 VM。  繼續閱讀下一篇文章，以深入了解 IaaS VM 的 Azure 磁碟加密先決條件。

> [!div class="nextstepaction"]
> [Azure 磁碟加密概觀](disk-encryption-overview.md)
