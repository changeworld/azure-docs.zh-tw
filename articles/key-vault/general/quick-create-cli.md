---
title: 快速入門 - 使用 Azure CLI 建立 Azure Key Vault
description: 說明如何使用 Azure CLI 建立 Azure Key Vault 的快速入門
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 2c71e96dc1c92fc94d403e7c721b2b9f3149b8e9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87100755"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-cli"></a>快速入門：使用 Azure CLI 來建立金鑰保存庫

Azure Key Vault 是一項雲端服務，可為[金鑰](../keys/index.yml)、[祕密](../secrets/index.yml)和[憑證](../certificates/index.yml)提供安全的存放區。 如需有關 Key Vault 的詳細資訊，請參閱[關於 Azure Key Vault](overview.md)；如需可以儲存在金鑰保存庫中項目的詳細資訊，請參閱[關於金鑰、祕密和憑證](about-keys-secrets-certificates.md)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

在本快速入門中，您會使用 [Azure CLI](/cli/azure/) 來建立金鑰保存庫。 Azure CLI 可供使用命令列或指令碼來建立和管理 Azure 資源。  如果您選擇在本機安裝和使用 CLI，本快速入門需要有 Azure CLI 2.0.4 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

若要使用 CLI 登入 Azure，您可以輸入：

```azurecli
az login
```

若要進一步了解透過 CLI 的登入選項，請參閱[使用 Azure CLI 進行登入](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

## <a name="create-a-resource-group"></a>建立資源群組

資源群組是在其中部署與管理 Azure 資源的邏輯容器。 下列範例會在 eastus  位置建立名為 ContosoResourceGroup  的資源群組。

```azurecli
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="create-a-key-vault"></a>建立金鑰保存庫

在上一個步驟中建立的資源群組中建立 Key Vault。 您必須提供一些資訊：

- 金鑰保存庫名稱：由 3 到 24 個字元組成的字串，只能包含數字 (0-9)、字母 (a-z、A-Z) 和連字號 (-)

  > [!Important]
  > 每個金鑰保存庫必須有唯一的名稱。 在下列範例中，以您的金鑰保存庫名稱取代 <your-unique-keyvault-name>。

- 資源群組名稱：**myResourceGroup**。
- 位置：**EastUS**。

```azurecli
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "EastUS"
```

此 Cmdlet 的輸出會顯示新建立金鑰保存庫的屬性。 請記下下列兩個屬性：

- **保存庫名稱**：您提供給上述 --name 參數的名稱。
- **保存庫 URI**：在此範例中是 https://&lt;your-unique-keyvault-name&gt;.vault.azure.net/。 透過其 REST API 使用保存庫的應用程式必須使用此 URI。

此時，您的 Azure 帳戶是唯一獲得授權在此新保存庫上執行任何作業的帳戶。

## <a name="clean-up-resources"></a>清除資源

此集合中的其他快速入門和教學課程會以本快速入門為基礎。 如果您打算繼續進行後續的快速入門和教學課程，您可以讓這些資源留在原處。

若不再需要，您可以使用 [az group delete](/cli/azure/group) 命令來移除資源群組和所有相關資源。 您可以刪除資源，如下所示：

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立 Key Vault 並且刪除。 若要深入了解 Key Vault 以及要如何將其與應用程式整合，請繼續閱讀下列文章。

- 閱讀 [Azure Key Vault 概觀](overview.md)
- 請參閱 [Azure CLI az keyvault 命令](/cli/azure/keyvault?view=azure-cli-latest)的參考
- 檢閱 [Azure Key Vault 最佳做法](best-practices.md)
