---
title: 設定金鑰保存庫
description: 如何設定與虛擬機器搭配使用的 Key Vault。
author: mimckitt
manager: vashan
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/24/2017
ms.author: mimckitt
ms.openlocfilehash: d5e4bb1d3f62685c90e95eebf5f8f07720a23b82
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86086988"
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>為 Azure Resource Manager 中的虛擬機器設定金鑰保存庫

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

在 Azure Resource Manager 堆疊中，密碼/憑證會被塑造成「金鑰保存庫資源提供者」所提供的資源。 若要深入了解「金鑰保存庫」，請參閱 [什麼是 Azure 金鑰保存庫？](../../key-vault/general/overview.md)

> [!NOTE]
> 1. 為了讓「金鑰保存庫」能與 Azure Resource Manager 虛擬機器搭配使用，必須將「金鑰保存庫」上的 **EnabledForDeployment** 屬性設定為 true。 您可以在各種用戶端中執行這項操作。
> 2. 「金鑰保存庫」必須建立在與「虛擬機器」相同的訂用帳戶和位置中。
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>使用 PowerShell 來設定金鑰保存庫
若要使用 PowerShell 建立金鑰保存庫，請參閱[使用 PowerShell 從 Azure Key Vault 設定及擷取祕密](../../key-vault/secrets/quick-create-powershell.md)。

針對新的「金鑰保存庫」，您可以使用下列 PowerShell Cmdlet：

```azurepowershell
New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment
```

針對現有的「金鑰保存庫」，您可以使用下列 PowerShell Cmdlet：

```azurepowershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment
```

## <a name="use-cli-to-set-up-key-vault"></a>使用 CLI 來設定金鑰保存庫
若要使用命令列介面 (CLI) 建立金鑰保存庫，請參閱 [使用 CLI 管理金鑰保存庫](../../key-vault/general/manage-with-cli2.md#create-a-key-vault)。

若使用 CLI，您必須在您指派部署原則之前建立金鑰保存庫。 您可以使用下列命令來達成目的：

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "EastAsia"
```

然後，若要啟用 Key Vault 以搭配範本部署使用，請執行下列命令：

```azurecli
az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
```

## <a name="use-templates-to-set-up-key-vault"></a>使用範本來設定金鑰保存庫
使用範本時，您需要將「金鑰保存庫」資源的 `enabledForDeployment` 屬性設定為 `true`。

```config
{
  "type": "Microsoft.KeyVault/vaults",
  "name": "ContosoKeyVault",
  "apiVersion": "2015-06-01",
  "location": "<location-of-key-vault>",
  "properties": {
    "enabledForDeployment": "true",
    ....
    ....
  }
}
```

如需使用範本來建立金鑰保存庫時可設定的其他選項，請參閱[建立金鑰保存庫](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)。
