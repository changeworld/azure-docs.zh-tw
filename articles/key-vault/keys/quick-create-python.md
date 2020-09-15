---
title: 快速入門 – Azure Key Vault Python 用戶端程式庫 – 管理金鑰
description: 了解如何使用 Python 用戶端程式庫，從 Azure Key Vault 建立、擷取和刪除金鑰
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: 44942067756f82c224decc218de17bf7dbc69734
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2020
ms.locfileid: "89482112"
---
# <a name="quickstart-azure-key-vault-keys-client-library-for-python"></a>快速入門：適用於 Python 的 Azure Key Vault 金鑰用戶端程式庫

開始使用適用於 Python 的 Azure Key Vault 用戶端程式庫。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。 藉由使用 Key Vault 來儲存密碼編譯金鑰，您可以避免將這類金鑰儲存在您的程式碼中，這樣會增加應用程式的安全性。

[API 參考文件](/python/api/overview/azure/keyvault-keys-readme?view=azure-python) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys) | [套件 (Python Package Index)](https://pypi.org/project/azure-keyvault-keys/)

## <a name="set-up-your-local-environment"></a>設定您的本機環境

[!INCLUDE [Set up your local environment](../../../includes/key-vault-python-qs-setup.md)]

7. 安裝 Key Vault 金鑰程式庫：

    ```terminal
    pip install azure-keyvault-keys
    ```

## <a name="create-a-resource-group-and-key-vault"></a>建立資源群組和金鑰保存庫

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

## <a name="give-the-service-principal-access-to-your-key-vault"></a>對服務主體授與金鑰保存庫的存取權

執行下列 [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) 命令，以授權您的服務主體在金鑰上進行刪除、取得、列出及建立作業。 

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az keyvault set-policy --name %KEY_VAULT_NAME% --spn %AZURE_CLIENT_ID% --resource-group KeyVault-PythonQS-rg --key-permissions delete get list create
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az keyvault set-policy --name $KEY_VAULT_NAME --spn $AZURE_CLIENT_ID --resource-group KeyVault-PythonQS-rg --key-permissions delete get list create
```

---

此命令依賴在先前步驟中建立的 `KEY_VAULT_NAME` 和 `AZURE_CLIENT_ID` 環境變數。

如需詳細資訊，請參閱[指派存取原則 - CLI](../general/assign-access-policy-cli.md)

## <a name="create-the-sample-code"></a>建立範例程式碼

適用於 Python 的 Azure Key Vault 用戶端程式庫可讓您管理密碼編譯金鑰和相關資產，例如憑證和祕密。 下列程式碼範例會示範如何建立用戶端、設定祕密、擷取祕密，以及刪除秘密。

建立名為 kv_keys.py 的檔案，其中包含此程式碼。

```python
import os
from azure.keyvault.keys import KeyClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)

keyName = input("Input a name for your key > ")

print(f"Creating a key in {keyVaultName} called '{keyName}' ...")

rsa_key = client.create_rsa_key(keyName, size=2048)

print(" done.")

print(f"Retrieving your key from {keyVaultName}.")

retrieved_key = client.get_key(keyName)

print(f"Key with name '{retrieved_key.name}' was found.")
print(f"Deleting your key from {keyVaultName} ...")

poller = client.begin_delete_key(keyName)
deleted_key = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>執行程式碼

請確定上一節中的程式碼位於名為 kv_keys.py 的檔案中。 使用下列命令來執行程式碼：

```terminal
python kv_keys.py
```

- 如果您遇到權限錯誤，請確定您已執行 [`az keyvault set-policy` 命令](#give-the-service-principal-access-to-your-key-vault)。
- 以相同的金鑰名稱重新執行程式碼可能會產生錯誤「(衝突) 金鑰 <name> 目前處於已刪除但可復原的狀態。」 使用不同的金鑰名稱。

## <a name="code-details"></a>程式碼詳細資料

### <a name="authenticate-and-create-a-client"></a>驗證並建立用戶端

在上述程式碼中，[`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) 物件會使用您為服務主體建立的環境變數。 每當您從 Azure 程式庫建立用戶端物件 (例如 [`KeyClient`](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python))，以及要透過該用戶端使用資源的 URI 時，就會提供此認證：

```python
credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)
```

## <a name="save-a-key"></a>儲存金鑰

取得金鑰保存庫的用戶端物件之後，您可以使用 [create_rsa_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#create-rsa-key-name----kwargs-) 方法來儲存金鑰： 

```python
rsa_key = client.create_rsa_key(keyName, size=2048)
```

您也可以使用 [create_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#create-key-name--key-type----kwargs-) 或 [create_ec_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#create-ec-key-name----kwargs-)。

呼叫 `create` 方法會針對金鑰保存庫產生 Azure REST API 的呼叫。

處理要求時，Azure 會使用您提供給用戶端的認證物件，來驗證呼叫者的身分識別 (服務主體)，

同時也會檢查呼叫者是否有權執行要求的動作。 您先前已使用 [`az keyvault set-policy` 命令](#give-the-service-principal-access-to-your-key-vault)，將此授權授與服務主體。

## <a name="retrieve-a-key"></a>擷取金鑰

若要從 Key Vault 讀取金鑰，請使用 [get_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#get-key-name--version-none----kwargs-) 方法：

```python
retrieved_key = client.get_key(keyName)
 ```

您也可以確認金鑰是否已使用 Azure CLI 命令 [az keyvault key show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show) 來設定。

### <a name="delete-a-key"></a>刪除金鑰

若要刪除金鑰，請使用 [begin_delete_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#begin-delete-key-name----kwargs-) 方法：

```python
poller = client.begin_delete_key(keyName)
deleted_key = poller.result()
```

`begin_delete_key` 方法是非同步，並會傳回輪詢者物件。 呼叫輪詢器的 `result` 方法會等待其完成。

您可以使用 Azure CLI 命令 [az keyvault key show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show)，確認是否已刪除金鑰。

一旦刪除，金鑰會保留為已刪除但可復原的狀態一段時間。 如果再次執行程式碼，請使用不同的金鑰名稱。

## <a name="clean-up-resources"></a>清除資源

如果您也想要實驗[憑證](../certificates/quick-create-python.md)和[祕密](../secrets/quick-create-python.md)，可以重複使用在本文中建立的 Key Vault。

否則，當您完成本文中建立的資源時，請使用下列命令來刪除資源群組及其包含的所有資源：

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>後續步驟

- [Azure 金鑰保存庫概觀](../general/overview.md)
- [Azure Key Vault 開發人員指南](../general/developers-guide.md)
- [Azure Key Vault 最佳做法](../general/best-practices.md)
- [使用 Key Vault 進行驗證](../general/authentication.md)
