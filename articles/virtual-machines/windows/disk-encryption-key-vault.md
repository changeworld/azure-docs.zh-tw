---
title: 建立及設定適用於 Azure 磁碟加密的金鑰保存庫
description: 本文提供建立與設定金鑰保存庫以與 Azure 磁碟加密一起使用的步驟
ms.service: virtual-machines
ms.subservice: security
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: f1317989434cca9375c612c8af0525323c6dda3c
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82081654"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>建立及設定適用於 Azure 磁碟加密的金鑰保存庫

Azure 磁碟加密使用 Azure 密鑰保管庫來控制和管理磁碟加密密鑰和機密。  如需金鑰保存庫的詳細資訊，請參閱[開始使用 Azure Key Vault](../../key-vault/key-vault-get-started.md) 和[保護金鑰保存庫](../../key-vault/general/secure-your-key-vault.md)。 

> [!WARNING]
> - 如果以前使用 Azure 磁碟加密與 Azure AD 一起加密 VM,則必須繼續使用此選項對 VM 進行加密。 有關詳細資訊[,請參閱使用 Azure AD 創建和配置 Azure 磁碟加密的金鑰保管庫(上一版本](disk-encryption-key-vault-aad.md))。

建立與設定用於 Azure 磁碟加密的金鑰保管庫涉及三個步驟:

1. 如果需要,創建資源組。
2. 建立金鑰保管庫。 
3. 設置金鑰保管庫高級訪問策略。

這些步驟在以下快速入門中進行了說明:

- [使用 Azure CLI 建立和加密 Windows 虛擬機器](disk-encryption-cli-quickstart.md)
- [使用 Azure PowerShell 建立和加密 Windows 虛擬機器](disk-encryption-cli-quickstart.md)

如果您願意,還可以生成或導入密鑰加密密鑰 (KEK)。

> [!Note]
> 本文中的步驟在[Azure 磁碟加密先決條件 CLI 腳本](https://github.com/ejarvi/ade-cli-getting-started)和 Azure[磁碟加密先決條件 PowerShell 腳本](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)中自動執行。

## <a name="install-tools-and-connect-to-azure"></a>安裝工具並連線到 Azure

本文中的步驟可以使用[Azure CLI、Azure](/cli/azure/) [PowerShell Az 模組](/powershell/azure/overview)或 Azure[門戶](https://portal.azure.com)完成。

當門戶可透過瀏覽器訪問時,Azure CLI 和 Azure PowerShell 需要本地安裝;但是,Azure CLI 和 Azure PowerShell 需要本地安裝。有關詳細資訊[,請參閱 Windows 的 Azure 磁碟加密:安裝工具](disk-encryption-windows.md#install-tools-and-connect-to-azure)。

### <a name="connect-to-your-azure-account"></a>連線至您的 Azure 帳戶

在使用 Azure CLI 或 Azure PowerShell 之前,必須首先連接到 Azure 訂閱。 為此,請[使用 Azure CLI 登錄](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)、使用 Azure [Powershell 登錄](/powershell/azure/authenticate-azureps?view=azps-2.5.0),或在提示時向 Azure 門戶提供認證。

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>後續步驟

- [Azure 磁碟加密先決條件 CLI 文稿](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure 磁碟加密先決條件 PowerShell 文稿](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- 瞭解[Windows VM 上的 Azure 磁碟加密機制](disk-encryption-windows.md)
- 瞭解如何排除[Azure 磁碟加密的故障](disk-encryption-troubleshooting.md)
- 閱讀[Azure 磁碟加密範例文稿](disk-encryption-sample-scripts.md)
