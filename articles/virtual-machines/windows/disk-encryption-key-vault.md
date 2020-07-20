---
title: 建立及設定適用於 Azure 磁碟加密的金鑰保存庫
description: 本文提供建立及設定金鑰保存庫，以與 Azure 磁碟加密搭配使用的步驟
ms.service: virtual-machines
ms.subservice: security
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 9160ab530037da1f24a2f07903b949b42ecbb34b
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86207073"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>建立及設定適用於 Azure 磁碟加密的金鑰保存庫

Azure 磁碟加密會使用 Azure Key Vault，來控制及管理磁碟加密金鑰與祕密。  如需金鑰保存庫的詳細資訊，請參閱[開始使用 Azure Key Vault](../../key-vault/key-vault-get-started.md) 和[保護金鑰保存庫](../../key-vault/general/secure-your-key-vault.md)。 

> [!WARNING]
> - 如果您先前曾使用 Azure 磁碟加密搭配 Azure AD 來加密 VM，則必須繼續使用此選項來加密您的 VM。 如需詳細資料，請參閱[使用 Azure AD (舊版) 建立和設定 Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault-aad.md)。

建立及設定與 Azure 磁碟加密搭配使用的金鑰保存庫，包含下列步驟：

> [!Note]
> 您必須選取 [Azure Key Vault 存取原則設定] 中的選項，才能存取磁片區加密的 Azure 磁碟加密。

1. 建立資源群組 (如有需要)。
2. 建立金鑰保存庫。 
3. 設定金鑰保存庫進階存取原則。

這些步驟將在下列快速入門中說明：

- [使用 Azure CLI 建立和加密 Windows 虛擬機器](disk-encryption-cli-quickstart.md)
- [使用 Azure PowerShell 建立和加密 Windows 虛擬機器](disk-encryption-powershell-quickstart.md)

如果想要的話，您也可以產生或匯入金鑰加密金鑰 (KEK)。

> [!Note]
> 本文中的步驟會在 [Azure 磁碟加密的必要 CLI 指令碼](https://github.com/ejarvi/ade-cli-getting-started)中和 [Azure 磁碟加密的必要 PowerShell 指令碼](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)中自動化。

## <a name="install-tools-and-connect-to-azure"></a>安裝工具並連線至 Azure

您可以使用 [Azure CLI](/cli/azure/)、[Azure PowerShell Az 模組](/powershell/azure/overview)或 [Azure 入口網站](https://portal.azure.com)來完成本文中的步驟。

雖然可以透過瀏覽器存取入口網站，但 Azure CLI 和 Azure PowerShell 需要本機安裝；請參閱[適用於 Windows 的 Azure 磁碟加密：安裝工具](disk-encryption-windows.md#install-tools-and-connect-to-azure)，以取得詳細資料。

### <a name="connect-to-your-azure-account"></a>連線至您的 Azure 帳戶

在使用 Azure CLI 或 Azure PowerShell 之前，必須先連線到您的 Azure 訂閱。 若要這麼做，請[使用 Azure CLI 登入](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)、[使用 Azure PowerShell 登入](/powershell/azure/authenticate-azureps?view=azps-2.5.0)，或在出現提示時將您的登入資訊提供給 Azure 入口網站。

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>後續步驟

- [Azure 磁碟加密的必要 CLI 指令碼](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure 磁碟加密的必要 PowerShell 指令碼](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- 了解 [Windows VM 上的 Azure 磁碟加密案例](disk-encryption-windows.md)
- 了解如何[對 Azure 磁碟加密進行疑難排解](disk-encryption-troubleshooting.md)
- 閱讀 [Azure 磁碟加密範例指令碼](disk-encryption-sample-scripts.md)
