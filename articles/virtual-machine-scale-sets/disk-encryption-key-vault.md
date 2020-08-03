---
title: 建立及設定適用於 Azure 磁碟加密的金鑰保存庫
description: 本文提供建立及設定金鑰保存庫，以與 Azure 磁碟加密搭配使用的步驟
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: acd2ae54d81fb508d5f8c02262cf8c2f0f071fb5
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080602"
---
# <a name="create-and-configure-a-key-vault-for-azure-disk-encryption"></a>建立及設定適用於 Azure 磁碟加密的金鑰保存庫

Azure 磁碟加密會使用 Azure Key Vault，來控制及管理磁碟加密金鑰與祕密。  如需金鑰保存庫的詳細資訊，請參閱[開始使用 Azure Key Vault](../key-vault/general/overview.md) 和[保護金鑰保存庫](../key-vault/general/secure-your-key-vault.md)。

建立及設定與 Azure 磁碟加密搭配使用的金鑰保存庫，包含下列步驟：

1. 建立資源群組 (如有需要)。
2. 建立金鑰保存庫。 
3. 設定金鑰保存庫進階存取原則。

這些步驟將在下列快速入門中說明：

如果想要的話，您也可以產生或匯入金鑰加密金鑰 (KEK)。

## <a name="install-tools-and-connect-to-azure"></a>安裝工具並連線至 Azure

您可以使用 [Azure CLI](/cli/azure/)、[Azure PowerShell Az 模組](/powershell/azure/)或 [Azure 入口網站](https://portal.azure.com)來完成本文中的步驟。

### <a name="connect-to-your-azure-account"></a>連線至您的 Azure 帳戶

在使用 Azure CLI 或 Azure PowerShell 之前，必須先連線到您的 Azure 訂用帳戶。 若要這麼做，請[使用 Azure CLI 登入](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)、[使用 Azure PowerShell 登入](/powershell/azure/authenticate-azureps?view=azps-2.5.0)，或在出現提示時將您的登入資訊提供給 Azure 入口網站。

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>後續步驟

- [Azure 磁碟加密概觀](disk-encryption-overview.md)
- [使用 Azure CLI 將虛擬機器擴展集加密](disk-encryption-cli.md)
- [使用 Azure PowerShell 將虛擬機器擴展集加密](disk-encryption-powershell.md)
