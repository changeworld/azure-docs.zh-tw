---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 2917854da679816796a7f7748c3877c062995168
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2020
ms.locfileid: "87512787"
---
本快速入門會使用預先建立的 Azure 金鑰保存庫。 您可以遵循 [Azure CLI 快速入門](/azure/key-vault/general/quick-create-cli)、[Azure PowerShell 快速入門](/azure/key-vault/general/quick-create-powershell)或 [Azure 入口網站快速入門](/azure/key-vault/general/quick-create-portal)中的步驟來建立金鑰保存庫。 

或者，您也可以直接執行下面的 Azure CLI 或 Azure PowerShell 命令。

> [!Important]
> 每個金鑰保存庫必須有唯一的名稱。 在下列範例中，以您的金鑰保存庫名稱取代 <your-unique-keyvault-name>。

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name "<your-unique-keyvault-name>" -g "myResourceGroup"
```

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS

New-AzKeyVault -Name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "EastUS"
```