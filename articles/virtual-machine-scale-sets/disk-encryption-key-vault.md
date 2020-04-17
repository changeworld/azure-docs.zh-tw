---
title: 建立及設定適用於 Azure 磁碟加密的金鑰保存庫
description: 本文提供建立與設定金鑰保存庫以與 Azure 磁碟加密一起使用的步驟
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 30fa6b910c0241621c2b2cdae9bb9a164f27cedb
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81454538"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>建立及設定適用於 Azure 磁碟加密的金鑰保存庫

Azure 磁碟加密使用 Azure 密鑰保管庫來控制和管理磁碟加密密鑰和機密。  如需金鑰保存庫的詳細資訊，請參閱[開始使用 Azure Key Vault](../key-vault/key-vault-get-started.md) 和[保護金鑰保存庫](../key-vault/general/secure-your-key-vault.md)。

建立與設定用於 Azure 磁碟加密的金鑰保管庫涉及三個步驟:

1. 如果需要,創建資源組。
2. 建立金鑰保管庫。 
3. 設置金鑰保管庫高級訪問策略。

這些步驟在以下快速入門中進行了說明:

如果您願意,還可以生成或導入密鑰加密密鑰 (KEK)。

## <a name="install-tools-and-connect-to-azure"></a>安裝工具並連線到 Azure

本文中的步驟可以使用[Azure CLI、Azure](/cli/azure/) [PowerShell Az 模組](/powershell/azure/overview)或 Azure[門戶](https://portal.azure.com)完成。

### <a name="connect-to-your-azure-account"></a>連線至您的 Azure 帳戶

在使用 Azure CLI 或 Azure PowerShell 之前,必須首先連接到 Azure 訂閱。 為此,請[使用 Azure CLI 登錄](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)、使用 Azure [Powershell 登錄](/powershell/azure/authenticate-azureps?view=azps-2.5.0),或在提示時向 Azure 門戶提供認證。

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
