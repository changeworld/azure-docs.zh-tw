---
title: 快速入門 – Azure Key Vault Python 用戶端程式庫 – 管理金鑰
description: 了解如何使用 Python 用戶端程式庫，從 Azure Key Vault 建立、擷取和刪除金鑰
author: msmbaldwin
ms.author: mbaldwin
ms.date: 3/30/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: c590fea274900f580a5c8415f6b04c2f9108f74f
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87876932"
---
# <a name="quickstart-azure-key-vault-keys-client-library-for-python"></a>快速入門：適用於 Python 的 Azure Key Vault 金鑰用戶端程式庫

開始使用適用於 Python 的 Azure Key Vault 用戶端程式庫。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。

Azure 金鑰保存庫可協助保護雲端應用程式和服務所使用的密碼編譯金鑰和密碼。 使用適用於 Python 的 Key Vault 用戶端程式庫來：

- 提高金鑰和密碼的安全性和控制權。
- 在幾分鐘內建立和匯入加密金鑰。
- 透過雲端規模和全域備援減少延遲。
- 簡化 TLS/SSL 憑證的工作並將其自動化。
- 使用經 FIPS 140-2 Level 2 驗證的 HSM。

[API 參考文件](/python/api/overview/azure/keyvault-keys-readme?view=azure-python) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault) | [套件 (Python Package Index)](https://pypi.org/project/azure-keyvault/)

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- Python 2.7、3.5.3 或更新版本
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 或 [Azure PowerShell](/powershell/azure/)

本快速入門假設您是在 Linux 終端機視窗中執行 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="setting-up"></a>設定

### <a name="install-the-package"></a>安裝套件

從主控台視窗安裝適用於 Python 的 Azure Key Vault 金鑰程式庫。

```console
pip install azure-keyvault-keys
```

在本快速入門中，您也需要安裝 azure.identity 套件：

```console
pip install azure.identity
```

### <a name="create-a-resource-group-and-key-vault"></a>建立資源群組和金鑰保存庫

本快速入門會使用預先建立的 Azure 金鑰保存庫。 您可以遵循 [Azure CLI 快速入門](quick-create-cli.md)、[Azure PowerShell 快速入門](quick-create-powershell.md)或 [Azure 入口網站快速入門](quick-create-portal.md)中的步驟來建立金鑰保存庫。 或者，您也可以執行下列 Azure CLI 命令。

> [!Important]
> 每個金鑰保存庫必須有唯一的名稱。 在下列範例中，以您的金鑰保存庫名稱取代 <your-unique-keyvault-name>。

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>建立服務主體

若要驗證雲端式 .NET 應用程式，最簡單的方法是使用受控識別；如需詳細資訊，請參閱[使用 App Service 受控識別存取 Azure Key Vault](../general/managed-identity.md)。 

但為了簡單起見，本快速入門會建立桌面應用程式，這需要使用服務主體和存取控制原則。 您的服務主體需要以下格式的唯一名稱："http://&lt;my-unique-service-principal-name&gt;"。

請使用 Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 命令來建立服務主體：

```azurecli
az ad sp create-for-rbac -n "http://&lt;my-unique-service-principal-name&gt;" --sdk-auth
```

這項作業會傳回一系列的金鑰/值組。 

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

請記下 clientId 和 clientSecret，因為我們將在下面的[設定環境變數](#set-environmental-variables)步驟中使用。

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>對服務主體授與金鑰保存庫的存取權

建立金鑰保存庫的存取原則，其藉由將 clientId 傳遞至 [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) 命令，將權限授予您的服務主體。 提供金鑰的取得、列出和建立權限給服務主體。

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --key-permissions delete get list create 
```

#### <a name="set-environmental-variables"></a>設定環境變數

應用程式中的 DefaultAzureCredential 方法依賴三個環境變數：`AZURE_CLIENT_ID`、`AZURE_CLIENT_SECRET` 和 `AZURE_TENANT_ID`。 使用 `export VARNAME=VALUE` 格式，將這些變數設定為您在[建立服務主體](#create-a-service-principal)步驟中記下的 clientId、clientSecret 和 tenantId 值 (此方法只會設定您目前殼層的變數，以及從殼層建立的處理序；若要將這些變數永久新增至您的環境，請編輯您的 `/etc/environment ` 檔案)。 

您也需要將金鑰保存庫名稱儲存為稱為 `KEY_VAULT_NAME` 的環境變數。

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>物件模型

適用於 Python 的 Azure Key Vault 用戶端程式庫可讓您管理金鑰和相關資產，例如憑證和祕密。 下列程式碼範例將說明如何建立用戶端、建立金鑰、擷取金鑰和刪除金鑰。

## <a name="code-examples"></a>程式碼範例

### <a name="add-directives"></a>新增指示詞

將下列指示詞新增至程式碼頂端：

```python
import os
from azure.keyvault.keys import KeyClient
from azure.identity import DefaultAzureCredential
```

### <a name="authenticate-and-create-a-client"></a>驗證並建立用戶端

根據上述[設定環境變數](#set-environmental-variables)步驟中的環境變數，驗證您的金鑰保存庫並建立金鑰保存庫用戶端。 金鑰保存庫的名稱會以 "https://<your-key-vault-name>.vault.azure.net" 格式，擴充至金鑰保存庫 URI。

```python
credential = DefaultAzureCredential()

client = KeyClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-key"></a>儲存金鑰

現在，您的應用程式已通過驗證，接下來您可以將金鑰放入金鑰保存庫中。 

```python
rsa_key = client.create_rsa_key(myKey,size=2048)
```

您可以確認金鑰是否已使用 [az keyvault key show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show) 命令來加以設定：

```azurecli
az keyvault key show --vault-name <your-unique-keyvault-name> --name myKey
```

### <a name="retrieve-a-key"></a>擷取金鑰

您現在可以擷取先前建立的金鑰

```python
retrieved_key = client.get_key(keyName)
print(retrieve_key.name)

 ```

您的金鑰現在會儲存為 `retrieved_key`。

### <a name="delete-a-key"></a>刪除金鑰

最後，讓我們從金鑰保存庫中刪除金鑰

```python
client.delete_key(keyName)
```

您可以使用 [az keyvault key show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show) 命令，確認金鑰是否已經消失：

```azurecli
az keyvault key show --vault-name <your-unique-keyvault-name> --name myKey
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
from azure.keyvault.key import KeyClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)

keyName = "myKey"

print("Creating a key in " + keyVaultName + " called '" + keyName  + "` ...")

rsa_key = client.create_rsa_key(myKey,size=2048)

print(" done.")

print("Retrieving your key from " + keyVaultName + ".")

retrieved_key = client.get_key(keyName)

print("Key with name '{0}' was found'.".format(retrieved_key.name))
print("Deleting your key from " + keyVaultName + " ...")

client.begin_delete_key(keyName).result()

print(" done.")
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立金鑰保存庫、儲存金鑰，並擷取該金鑰。 若要深入了解 Key Vault 以及要如何將其與應用程式整合，請繼續閱讀下列文章。

- 閱讀 [Azure Key Vault 概觀](../general/overview.md)
- 參閱 [Azure Key Vault 開發人員指南](../general/developers-guide.md)
- 檢閱 [Azure Key Vault 最佳做法](../general/best-practices.md)
