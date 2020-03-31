---
title: 建立及設定適用於 Azure 磁碟加密的金鑰保存庫
description: 本文提供了創建和配置金鑰保存庫以與 Azure 磁片加密一起使用的步驟
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: eec5b42da709ab5e79da42f11600f6ffc81d247e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279007"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>建立及設定適用於 Azure 磁碟加密的金鑰保存庫

Azure 磁片加密使用 Azure 金鑰保存庫來控制和管理磁片加密金鑰和機密。  如需金鑰保存庫的詳細資訊，請參閱[開始使用 Azure Key Vault](../key-vault/key-vault-get-started.md) 和[保護金鑰保存庫](../key-vault/key-vault-secure-your-key-vault.md)。

創建和配置用於 Azure 磁片加密的金鑰保存庫涉及三個步驟：

1. 如果需要，創建資源組。
2. 創建金鑰保存庫。 
3. 設置金鑰保存庫高級訪問策略。

這些步驟在以下快速入門中進行了說明：

如果您願意，還可以生成或導入金鑰加密金鑰 （KEK）。

## <a name="install-tools-and-connect-to-azure"></a>安裝工具並連接到 Azure

本文中的步驟可以使用[Azure CLI、Azure](/cli/azure/) [PowerShell Az 模組](/powershell/azure/overview)或 Azure[門戶](https://portal.azure.com)完成。

### <a name="connect-to-your-azure-account"></a>連線至您的 Azure 帳戶

在使用 Azure CLI 或 Azure PowerShell 之前，必須首先連接到 Azure 訂閱。 為此，請[使用 Azure CLI 登錄](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)、使用 Azure [Powershell 登錄](/powershell/azure/authenticate-azureps?view=azps-2.5.0)，或在提示時向 Azure 門戶提供憑據。

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
