---
title: 建立及設定適用於 Azure 磁碟加密的金鑰保存庫
description: 本文提供建立和設定金鑰保存庫以搭配使用的步驟 Azure 磁碟加密
ms.service: virtual-machines-linux
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 0038d5fdb38fdcfd4130a710f51d764e0cf9d907
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81459809"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>建立及設定適用於 Azure 磁碟加密的金鑰保存庫

Azure 磁碟加密使用 Azure Key Vault 來控制及管理磁片加密金鑰和密碼。  如需金鑰保存庫的詳細資訊，請參閱[開始使用 Azure Key Vault](../../key-vault/key-vault-get-started.md) 和[保護金鑰保存庫](../../key-vault/general/secure-your-key-vault.md)。 

> [!WARNING]
> - 如果您先前已使用 Azure 磁碟加密搭配 Azure AD 來加密 VM，則必須繼續使用此選項來加密您的 VM。 如需詳細資訊，請參閱[建立和設定具有 Azure AD （舊版） Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault-aad.md)。

建立和設定與 Azure 磁碟加密搭配使用的金鑰保存庫包含三個步驟：

1. 建立資源群組（如有需要）。
2. 建立金鑰保存庫。 
3. 設定金鑰保存庫的先進存取原則。

下列快速入門將說明這些步驟：

- [使用 Azure CLI 來建立和加密 Linux VM](disk-encryption-cli-quickstart.md)
- [使用 Azure PowerShell 建立和加密 Linux 虛擬機器](disk-encryption-cli-quickstart.md)

如果您想要的話，也可以產生或匯入金鑰加密金鑰（KEK）。

> [!Note]
> 本文中的步驟會在[Azure 磁碟加密必要條件 CLI 腳本](https://github.com/ejarvi/ade-cli-getting-started)中自動化，並[Azure 磁碟加密必要條件 PowerShell 腳本](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)。

## <a name="install-tools-and-connect-to-azure"></a>安裝工具並連接至 Azure

您可以使用[Azure CLI](/cli/azure/)、 [Azure PowerShell Az 模組](/powershell/azure/overview)或[Azure 入口網站](https://portal.azure.com)來完成這篇文章中的步驟。 

雖然可以透過瀏覽器存取入口網站，但 Azure CLI 和 Azure PowerShell 需要本機安裝;如需詳細資訊，請參閱[適用于 Linux 的 Azure 磁碟加密：安裝工具](disk-encryption-linux.md#install-tools-and-connect-to-azure)。

### <a name="connect-to-your-azure-account"></a>連線至您的 Azure 帳戶

使用 Azure CLI 或 Azure PowerShell 之前，您必須先連接到您的 Azure 訂用帳戶。 若要這麼做，請[使用 Azure CLI 登入](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)、[使用 Azure Powershell 登入](/powershell/azure/authenticate-azureps?view=azps-2.5.0)，或在出現提示時提供您的認證給 Azure 入口網站。

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 ```
 
## Next steps

- [Azure Disk Encryption prerequisites CLI script](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption prerequisites PowerShell script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Learn [Azure Disk Encryption scenarios on Linux VMs](disk-encryption-linux.md)
- Learn how to [troubleshoot Azure Disk Encryption](disk-encryption-troubleshooting.md)
- Read the [Azure Disk Encryption sample scripts](disk-encryption-sample-scripts.md)
