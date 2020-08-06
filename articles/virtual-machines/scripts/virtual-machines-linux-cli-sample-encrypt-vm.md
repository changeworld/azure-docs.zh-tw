---
title: Azure CLI 指令碼範例 - 將 Linux VM 加密
description: Azure CLI 指令碼範例 - 將 Linux VM 加密
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 610bc83925fb543d835df357960c5977860c0189
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87479616"
---
# <a name="encrypt-a-linux-virtual-machine-in-azure"></a>如何在 Azure 中將 Linux 虛擬機器加密

此指令碼會建立一個安全的 Azure Key Vault、加密金鑰、Azure Active Directory 服務主體，以及 Linux 虛擬機器 (VM)。 然後會使用 Key Vault 中的加密金鑰和服務主體認證將 VM 加密。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/encrypt-disks/encrypt_vm.sh "Encrypt VM disks")]

## <a name="clean-up-deployment"></a>清除部署 

執行下列命令來移除資源群組、VM 和所有相關資源。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來建立資源群組、Azure Key Vault、服務主體、虛擬機器和所有相關資源。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az group create](/cli/azure/group) | 建立用來存放所有資源的資源群組。 |
| [az keyvault create](/cli/azure/keyvault) | 建立 Azure Key Vault 來儲存安全資料，例如加密金鑰。 |
| [az keyvault key create](/cli/azure/keyvault/key) | 在 Key Vault 中建立加密金鑰。 |
| [az ad sp create-for-rbac](/cli/azure/ad/sp) | 建立 Azure Active Directory 服務主體，以安全地進行驗證及控制對加密金鑰的存取。 |
| [az keyvault set-policy](/cli/azure/keyvault) | 在 Key Vault 上設定權限，以授與服務主體對加密金鑰的存取權。 |
| [az vm create](/cli/azure/vm) | 建立虛擬機器，並將它連線到網路卡、虛擬網路、子網路及 NSG。 此命令也會指定要使用的虛擬機器映像和管理認證。  |
| [az vm encryption enable](/cli/azure/vm/encryption) | 使用服務主體認證和加密金鑰在 VM 上啟用加密。 |
| [az vm encryption show](/cli/azure/vm/encryption) | 顯示 VM 加密程序的狀態。 |
| [az group delete](/cli/azure/vm/extension) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。

您可以在 [Azure Linux VM 文件](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中找到其他的虛擬機器 CLI 指令碼範例。
