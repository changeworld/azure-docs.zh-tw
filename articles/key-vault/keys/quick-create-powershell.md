---
title: 在 Azure Key Vault 中建立和取出金鑰的屬性 - Azure PowerShell
description: 說明如何使用 Azure PowerShell 從 Azure Key Vault 設定及擷取金鑰的快速入門
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 03/30/2020
ms.author: mbaldwin
ms.openlocfilehash: 4ebd3cc605b396f72d063f3fc506df9020ec3a5f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "87061005"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-powershell"></a>快速入門：使用 Azure PowerShell 從 Azure Key Vault 設定及擷取金鑰

在本快速入門中，您會在 Azure Key Vault 中使用 Azure PowerShell 建立金鑰保存庫。 Azure Key Vault 是一項雲端服務，可作為安全的祕密存放區。 您也可以安全地儲存金鑰、密碼、憑證和其他祕密。 如需 Key Vault 的詳細資訊，您可以檢閱[概觀](../general/overview.md)。 Azure PowerShell 可供使用命令列或指令碼來建立和管理 Azure 資源。 一旦完成該作業，您就會儲存金鑰。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 PowerShell，則在執行本教學課程時，您必須使用 Azure PowerShell 模組 1.0.0 版或更新版本。 執行 `$PSVersionTable.PSVersion` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Login-AzAccount` 以建立與 Azure 的連線。

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>建立資源群組

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 來建立 Azure 資源群組。 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

## <a name="create-a-key-vault"></a>建立金鑰保存庫

接下來，您會建立 Key Vault。 執行此步驟時，您需要一些資訊：

雖然我們在此快速入門中使用 "Contoso KeyVault2" 作為我們的 Key Vault 名稱，但您必須使用唯一的名稱。

- **保存庫名稱**：Contoso-Vault2。
- **資源群組名稱**：ContosoResourceGroup。
- **位置**：美國東部。

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vault2' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```

此 Cmdlet 的輸出會顯示新建立金鑰保存庫的屬性。 請記下下列兩個屬性：

* **保存庫名稱**：在此範例中是 **Contoso-Vault2**。 您將在其他金鑰保存庫 Cmdlet 中使用此名稱。
* **保存庫 URI**：在此範例中是 https://Contoso-Vault2.vault.azure.net/ 。 透過其 REST API 使用保存庫的應用程式必須使用此 URI。

在保存庫建立後，您的 Azure 帳戶是唯一能夠在這個新保存庫上執行任何作業的帳戶。

## <a name="add-a-key-to-key-vault"></a>在 Key Vault 中新增金鑰

若要在保存庫中新增金鑰，您只需要另外進行幾個步驟。 此金鑰可供應用程式使用。 

輸入下列命令，以建立名為 **ExampleKey** 的金鑰：

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName 'Contoso-Vault2' -Name 'ExampleKey' -Destination 'Software'
```

透過使用這個金鑰的 URI，您現在可以參照您新增至 Azure Key Vault 的這個金鑰。 使用 **https://Contoso-Vault2.vault.azure.net/keys/ExampleKey** 來取得最新版本。 

若要檢視先前儲存的金鑰：

```azurepowershell-interactive
Get-AzKeyVaultKey -VaultName 'Contoso-Vault2' -KeyName 'ExampleKey'
```

現在，您已建立 Key Vault、儲存金鑰並擷取了金鑰。

## <a name="clean-up-resources"></a>清除資源

此集合中的其他快速入門和教學課程會以本快速入門為基礎。 如果您打算繼續進行後續的快速入門和教學課程，您可以讓這些資源留在原處。
當不再需要時，您可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令來移除資源群組和所有相關資源。 您可以刪除資源，如下所示：

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立 Key Vault 並在其中儲存憑證。 若要深入了解 Key Vault 以及要如何將其與應用程式整合，請繼續閱讀下列文章。

- 閱讀 [Azure Key Vault 概觀](../general/overview.md)
- 請參閱 [Azure PowerShell Key Vault Cmdlet](/powershell/module/az.keyvault/) 的參考
- 檢閱 [Azure Key Vault 最佳做法](../general/best-practices.md)
