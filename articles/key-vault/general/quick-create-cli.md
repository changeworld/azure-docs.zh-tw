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
ms.openlocfilehash: d84f500560031322cff32005b537037dc897919e
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94516759"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-cli"></a>快速入門：使用 Azure CLI 來建立金鑰保存庫

Azure Key Vault 是一項雲端服務，可為[金鑰](../keys/index.yml)、[祕密](../secrets/index.yml)和[憑證](../certificates/index.yml)提供安全的存放區。 如需有關 Key Vault 的詳細資訊，請參閱[關於 Azure Key Vault](overview.md)；如需可以儲存在金鑰保存庫中項目的詳細資訊，請參閱[關於金鑰、祕密和憑證](about-keys-secrets-certificates.md)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 本快速入門需要 2.0.4 版或更新版本的 Azure CLI。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。

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

若不再需要，您可以使用 Azure CLI [az group delete](/cli/azure/group) 命令來移除資源群組和所有相關資源：

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立 Key Vault 並且刪除。 若要深入了解 Key Vault 以及要如何將其與應用程式整合，請繼續閱讀下列文章。

- 閱讀 [Azure Key Vault 概觀](overview.md)
- 請參閱 [Azure CLI az keyvault 命令](/cli/azure/keyvault?view=azure-cli-latest)的參考
- 檢閱 [Azure Key Vault 最佳做法](best-practices.md)
