---
title: 快速入門：從 Azure Key Vault 設定及擷取祕密
description: 說明如何使用 Azure CLI 從 Azure Key Vault 設定及擷取祕密的快速入門
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 7b41c401a411abf47579f74fdd612ca431130ecd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421492"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-azure-cli"></a>快速入門：使用 Azure CLI 從 Azure Key Vault 設定及擷取祕密

在本快速入門中，您會在 Azure Key Vault 中使用 Azure CLI 建立金鑰保存庫。 Azure Key Vault 是一項雲端服務，可作為安全的祕密存放區。 您也可以安全地儲存金鑰、密碼、憑證和其他祕密。 如需 Key Vault 的詳細資訊，您可以檢閱[概觀](../general/overview.md)。 Azure CLI 可供使用命令列或指令碼來建立和管理 Azure 資源。 一旦完成該作業，您就會儲存祕密。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本快速入門需要有 Azure CLI 2.0.4 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

若要使用 CLI 登入 Azure，您可以輸入：

```azurecli
az login
```

若要進一步了解透過 CLI 的登入選項，請參閱[使用 Azure CLI 進行登入](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

## <a name="create-a-resource-group"></a>建立資源群組

資源群組是在其中部署與管理 Azure 資源的邏輯容器。 下列範例會在 eastus  位置建立名為 ContosoResourceGroup  的資源群組。

```azurecli
az group create --name "ContosoResourceGroup" --location eastus
```

## <a name="create-a-key-vault"></a>建立金鑰保存庫

接下來，您會在上一個步驟中建立的資源群組中建立 Key Vault。 您必須提供一些資訊：

- 在本快速入門中，我們使用 **Contoso-vault2**。 您必須在測試中提供唯一的名稱。
- 資源群組名稱：**ContosoResourceGroup**。
- 位置：**美國東部**。

```azurecli
az keyvault create --name "Contoso-Vault2" --resource-group "ContosoResourceGroup" --location eastus
```

此 Cmdlet 的輸出會顯示新建立 Key Vault 的屬性。 請記下下列兩個屬性：

- **保存庫名稱**：在此範例中是 **Contoso-Vault2**。 您將在其他 Key Vault 命令中使用此名稱。
- **保存庫 URI**：在此範例中是 https://contoso-vault2.vault.azure.net/ 。 透過其 REST API 使用保存庫的應用程式必須使用此 URI。

此時，您的 Azure 帳戶是唯一獲得授權在此新保存庫上執行任何作業的帳戶。

## <a name="add-a-secret-to-key-vault"></a>將祕密新增至 Key Vault

若要將祕密新增至保存庫，您只需要採取一些額外步驟。 應用程式可以使用此密碼。 此密碼會稱為 **ExamplePassword**，且其中會儲存 **hVFkk965BuUv** 值。

輸入下列命令，以在 Key Vault 中建立稱為 **ExamplePassword** 並將儲存 **hVFkk965BuUv** 值的祕密：

```azurecli
az keyvault secret set --vault-name "Contoso-Vault2" --name "ExamplePassword" --value "hVFkk965BuUv"
```

透過使用其 URI，您現在可以參照您新增至 Azure Key Vault 的密碼。 使用 **https://Contoso-Vault2.vault.azure.net/secrets/ExamplePassword** 來取得最新版本。 

若要以純文字檢視包含在祕密中的值：

```azurecli
az keyvault secret show --name "ExamplePassword" --vault-name "Contoso-Vault2"
```

現在，您已建立 Key Vault，儲存祕密，並擷取它。

## <a name="clean-up-resources"></a>清除資源

此集合中的其他快速入門和教學課程會以本快速入門為基礎。 如果您打算繼續進行後續的快速入門和教學課程，您可以讓這些資源留在原處。
若不再需要，您可以使用 [az group delete](/cli/azure/group) 命令來移除資源群組和所有相關資源。 您可以刪除資源，如下所示：

```azurecli
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立 Key Vault 並在其中儲存祕密。 若要深入了解 Key Vault 以及要如何將其與應用程式整合，請繼續閱讀下列文章。

- 閱讀 [Azure Key Vault 概觀](../general/overview.md)
- 請參閱 [Azure CLI az keyvault 命令](/cli/azure/keyvault?view=azure-cli-latest)的參考
- 檢閱 [Azure Key Vault 最佳做法](../general/best-practices.md)
