---
title: 快速入門 – Azure Key Vault Python 用戶端程式庫 – 管理祕密
description: 了解如何使用 Python 用戶端程式庫，從 Azure Key Vault 建立、擷取和刪除秘密
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: b31f22f9a3909df308fdcfa994833887828f2539
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87876354"
---
# <a name="quickstart-azure-key-vault-secrets-client-library-for-python"></a>快速入門：適用於 Python 的 Azure Key Vault 祕密用戶端程式庫

開始使用適用於 Python 的 Azure Key Vault 用戶端程式庫。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。

Azure 金鑰保存庫可協助保護雲端應用程式和服務所使用的密碼編譯金鑰和密碼。 使用適用於 Python 的 Key Vault 用戶端程式庫來：

- 提高金鑰和密碼的安全性和控制權。
- 在幾分鐘內建立和匯入加密金鑰。
- 透過雲端規模和全域備援減少延遲。
- 簡化 TLS/SSL 憑證的工作並將其自動化。
- 使用經 FIPS 140-2 Level 2 驗證的 HSM。

[API 參考文件](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault) | [套件 (Python Package Index)](https://pypi.org/project/azure-keyvault/)

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- Python 2.7、3.5.3 或更新版本
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 或 [Azure PowerShell](/powershell/azure/)

本快速入門假設您是在 Linux 終端機視窗中執行 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="setting-up"></a>設定

### <a name="install-the-package"></a>安裝套件

從主控台視窗安裝適用於 Python 的 Azure Key Vault 秘密程式庫。

```console
pip install azure-keyvault-secrets
```

在本快速入門中，您也需要安裝 azure.identity 套件：

```console
pip install azure.identity
```

### <a name="create-a-resource-group-and-key-vault"></a>建立資源群組和金鑰保存庫

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

### <a name="create-a-service-principal"></a>建立服務主體

[!INCLUDE [Create a service principal](../../../includes/key-vault-sp-creation.md)]

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>對服務主體授與金鑰保存庫的存取權

[!INCLUDE [Give the service principal access to your key vault](../../../includes/key-vault-sp-kv-access.md)]

#### <a name="set-environmental-variables"></a>設定環境變數

[!INCLUDE [Set environmental variables](../../../includes/key-vault-set-environmental-variables.md)]

## <a name="object-model"></a>物件模型

適用於 Python 的 Azure Key Vault 用戶端程式庫可讓您管理金鑰和相關資產，例如憑證和祕密。 以下的程式碼範例會示範如何建立用戶端、設定祕密、擷取祕密，以及刪除秘密。

整個主控台應用程式可於 https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app 取得。

## <a name="code-examples"></a>程式碼範例

### <a name="add-directives"></a>新增指示詞

將下列指示詞新增至程式碼頂端：

```python
import os
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential
```

### <a name="authenticate-and-create-a-client"></a>驗證並建立用戶端

根據上述[設定環境變數](#set-environmental-variables)步驟中的環境變數，驗證您的金鑰保存庫並建立金鑰保存庫用戶端。 金鑰保存庫的名稱會以 "https://<your-key-vault-name>.vault.azure.net" 格式，擴充至金鑰保存庫 URI。

```python
credential = DefaultAzureCredential()

client = SecretClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>儲存秘密

既然應用程式已經過驗證，您可以使用 client.SetSecret 方法 (/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync)，將祕密放入金鑰保存庫中。這需要秘密的名稱，在此範例中，我們將使用 "mySecret"。  

```python
client.set_secret(secretName, secretValue)
```

您可以確認祕密是否已使用 [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) 命令來加以設定：

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>擷取祕密

您現在可以使用 [client.GetSecret 方法](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)擷取先前設定的值。

```python
retrieved_secret = client.get_secret(secretName)
 ```

您的祕密現在已另存為 `retrieved_secret.value`。

### <a name="delete-a-secret"></a>刪除祕密

最後，讓我們使用 [client.DeleteSecret 方法](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)，從您的金鑰保存庫中刪除秘密。

```python
client.delete_secret(secretName)
```

您可以使用 [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) 命令，確認祕密是否已經消失：

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以使用 Azure CLI 或 Azure PowerShell 來移除金鑰保存庫和對應的資源群組。

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>範例程式碼

```python
import os
import cmd
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = f"https://{keyVaultName}.vault.azure.net"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)

secretName = "mySecret"

print("Input the value of your secret > ")
secretValue = raw_input()

print(f"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}` ...")

client.set_secret(secretName, secretValue)

print(" done.")

print("Forgetting your secret.")
secretValue = ""
print(f"Your secret is {secretValue}.")

print(f"Retrieving your secret from {keyVaultName}.")

retrieved_secret = client.get_secret(secretName)

print(f"Your secret is '{retrieved_secret.value}'.")
print(f"Deleting your secret from {keyVaultName} ...")

client.delete_secret(secretName)

print(" done.")
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立金鑰保存庫、儲存秘密，並擷取該秘密。 若要深入了解 Key Vault 以及要如何將其與應用程式整合，請繼續閱讀下列文章。

- 閱讀 [Azure Key Vault 概觀](../general/overview.md)
- 參閱 [Azure Key Vault 開發人員指南](../general/developers-guide.md)
- 檢閱 [Azure Key Vault 最佳做法](../general/best-practices.md)
