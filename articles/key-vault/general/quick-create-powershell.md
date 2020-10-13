---
title: 快速入門 - 使用 Azure PowerShell 建立 Azure Key Vault
description: 說明如何使用 Azure PowerShell 建立 Azure Key Vault 的快速入門
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 11/08/2019
ms.author: mbaldwin
ms.openlocfilehash: 41254accbfff8f1d68a8bbef4d74ed01c64891b9
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91803886"
---
# <a name="quickstart-create-a-key-vault-using-powershell"></a>快速入門：使用 PowerShell 建立金鑰保存庫

Azure Key Vault 是一項雲端服務，可為[金鑰](../keys/index.yml)、[祕密](../secrets/index.yml)和[憑證](../certificates/index.yml)提供安全的存放區。 如需有關 Key Vault 的詳細資訊，請參閱[關於 Azure Key Vault](overview.md)；如需可以儲存在金鑰保存庫中項目的詳細資訊，請參閱[關於金鑰、祕密和憑證](about-keys-secrets-certificates.md)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

在本快速入門中，您會使用 [Azure PowerShell](/powershell/azure/) 建立金鑰保存庫。 如果您選擇在本機安裝和使用 PowerShell，則在執行本教學課程時，您必須使用 Azure PowerShell 模組 1.0.0 版或更新版本。 執行 `$PSVersionTable.PSVersion` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Login-AzAccount` 以建立與 Azure 的連線。

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>建立資源群組

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 來建立 Azure 資源群組。 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 

```azurepowershell-interactive
New-AzResourceGroup -Name 'myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>建立金鑰保存庫

在上一個步驟中建立的資源群組中建立 Key Vault。 您必須提供一些資訊：

- 金鑰保存庫名稱：由 3 到 24 個字元組成的字串，只能包含數字 (0-9)、字母 (a-z、A-Z) 和連字號 (-)

  > [!Important]
  > 每個金鑰保存庫必須有唯一的名稱。 在下列範例中，以您的金鑰保存庫名稱取代 <your-unique-keyvault-name>。

- 資源群組名稱：**myResourceGroup**。
- 位置：**EastUS**。

```azurepowershell-interactive
New-AzKeyVault -Name <your-unique-key-vault-name> -ResourceGroupName "myResourceGroup" -Location "East US"
```

此 Cmdlet 的輸出會顯示新建立金鑰保存庫的屬性。 請記下下列兩個屬性：

- **保存庫名稱**：您提供給上述 --name 參數的名稱。
- **保存庫 URI**：在此範例中，這是 https://<your-unique-keyvault-name>.vault.azure.net/。 透過其 REST API 使用保存庫的應用程式必須使用此 URI。

此時，您的 Azure 帳戶是唯一獲得授權在此新保存庫上執行任何作業的帳戶。

## <a name="clean-up-resources"></a>清除資源

此集合中的其他快速入門和教學課程會以本快速入門為基礎。 如果您打算繼續進行其他快速入門和教學課程，您可以讓這些資源留在原處。

當不再需要時，您可以使用 Azure PowerShell [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令來移除資源群組和所有相關資源。

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立 Key Vault 並在其中儲存祕密。 若要深入了解 Key Vault 以及要如何將其與應用程式整合，請繼續閱讀下列文章。

- 閱讀 [Azure Key Vault 概觀](overview.md)
- 請參閱 [Azure PowerShell Key Vault Cmdlet](/powershell/module/az.keyvault/) 的參考
- 檢閱 [Azure Key Vault 最佳做法](best-practices.md)
