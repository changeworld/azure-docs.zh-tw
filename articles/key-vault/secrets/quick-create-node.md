---
title: 快速入門 - 適用於 JavaScript (v4) 的 Azure Key Vault 用戶端程式庫
description: 了解如何使用 JavaScript 用戶端程式庫從 Azure Key Vault 建立、擷取和刪除秘密
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 35713a2e854cf65054d162ce0191bf2dfde4e90b
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92786134"
---
# <a name="quickstart-azure-key-vault-client-library-for-javascript-v4"></a>快速入門：適用於 JavaScript (v4) 的 Azure Key Vault 用戶端程式庫

開始使用適用於 JavaScript 的 Azure Key Vault 秘密用戶端程式庫。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。

[API 參考文件](/javascript/api/overview/azure/key-vault-index) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault) | [套件 (npm)](https://www.npmjs.com/package/@azure/keyvault-secrets)

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 適用於您作業系統的 [Node.js](https://nodejs.org) 目前版本。
- [Azure CLI](/cli/azure/install-azure-cli)

本快速入門假設您是在 Linux 終端機視窗中執行 [Azure CLI](/cli/azure/install-azure-cli)。

## <a name="setting-up"></a>設定
本快速入門會使用 Azure 身分識別程式庫搭配 Azure CLI，向 Azure 服務驗證使用者。 開發人員也可以使用 Visual Studio 或 Visual Studio Code 來驗證其呼叫。如需詳細資訊，請參閱[使用 Azure 身分識別用戶端程式庫驗證用戶端](/javascript/api/overview/azure/identity-readme)。

### <a name="sign-in-to-azure"></a>登入 Azure

1. 執行 `login` 命令。

    ```azurecli-interactive
    az login
    ```

    如果 CLI 可以開啟預設瀏覽器，它會執行這項操作，並載入 Azure 登入頁面。

    否則，請在 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) 中開啟瀏覽器頁面，並輸入顯示在終端機中的授權碼。

2. 請在瀏覽器中使用您的帳戶認證登入。

### <a name="install-the-package"></a>安裝套件

從主控台視窗安裝適用於 Node.js 的 Azure Key Vault 秘密程式庫。

```console
npm install @azure/keyvault-secrets
```

在本快速入門中，您也需要安裝 azure.identity 套件：

```console
npm install @azure/identity
```

### <a name="create-a-resource-group-and-key-vault"></a>建立資源群組和金鑰保存庫

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

#### <a name="grant-access-to-your-key-vault"></a>授與對金鑰保存庫的存取權

建立金鑰保存庫的存取原則，將祕密權限授與服務主體。

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set
```

#### <a name="set-environment-variables"></a>設定環境變數

此應用程式使用金鑰保存庫名稱作為稱為 `KEY_VAULT_NAME` 的環境變數。

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME=<your-key-vault-name>
```

macOS 或 Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>物件模型

適用於 JavaScript 的 Azure Key Vault 秘密用戶端程式庫可讓您管理秘密。 以下的程式碼範例會示範如何建立用戶端、設定祕密、擷取祕密，以及刪除秘密。

## <a name="code-examples"></a>程式碼範例

### <a name="add-directives"></a>新增指示詞

將下列指示詞新增至程式碼頂端：

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");
```

### <a name="authenticate-and-create-a-client"></a>驗證並建立用戶端

在本快速入門中，已登入的使用者是用來向金鑰保存庫進行驗證，這是本機開發的慣用方法。 針對部署至 Azure 的應用程式，應將受控識別指派給 App Service 或虛擬機器。如需詳細資訊，請參閱[受控識別概觀](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

在下列範例中，金鑰保存庫的名稱會以 "https://\<your-key-vault-name\>.vault.azure.net" 格式，擴充至金鑰保存庫 URI。 這個範例會使用 ['DefaultAzureCredential()'](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) 類別，允許在各種不同的環境中使用相同的程式碼，搭配不同的選項來提供身分識別。 如需詳細資訊，請參閱[預設 Azure 認證驗證](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme)。 

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new SecretClient(KVUri, credential);
```

### <a name="save-a-secret"></a>儲存秘密

既然應用程式已經過驗證，您可以使用 [client.setSecret 方法](/javascript/api/@azure/keyvault-secrets/secretclient?#setsecret-string--string--setsecretoptions-)，將祕密放入金鑰保存庫中。這需要秘密的名稱，我們在此範例中使用 "mySecret"。  

```javascript
await client.setSecret(secretName, secretValue);
```

您可以確認祕密是否已使用 [az keyvault secret show](/cli/azure/keyvault/secret?#az-keyvault-secret-show) 命令來加以設定：

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>擷取祕密

您現在可以使用 [client.getSecret 方法](/javascript/api/@azure/keyvault-secrets/secretclient?#getsecret-string--getsecretoptions-)擷取先前設定的值。

```javascript
const retrievedSecret = await client.getSecret(secretName);
 ```

您的祕密現在已另存為 `retrievedSecret.value`。

### <a name="delete-a-secret"></a>刪除祕密

最後，讓我們使用 [client.beginDeleteSecret method](/javascript/api/@azure/keyvault-secrets/secretclient?#begindeletesecret-string--begindeletesecretoptions-)，從您的金鑰保存庫中刪除秘密。

```javascript
await client.beginDeleteSecret(secretName)
```

您可以使用 [az keyvault secret show](/cli/azure/keyvault/secret?#az-keyvault-secret-show) 命令，確認祕密是否已經消失：

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

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");

const readline = require('readline');

function askQuestion(query) {
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    return new Promise(resolve => rl.question(query, ans => {
        rl.close();
        resolve(ans);
    }))
}

async function main() {

  const keyVaultName = process.env["KEY_VAULT_NAME"];
  const KVUri = "https://" + keyVaultName + ".vault.azure.net";

  const credential = new DefaultAzureCredential();
  const client = new SecretClient(KVUri, credential);

  const secretName = "mySecret";
  var secretValue = await askQuestion("Input the value of your secret > ");

  console.log("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");
  await client.setSecret(secretName, secretValue);

  console.log("Done.");

  console.log("Forgetting your secret.");
  secretValue = "";
  console.log("Your secret is '" + secretValue + "'.");

  console.log("Retrieving your secret from " + keyVaultName + ".");

  const retrievedSecret = await client.getSecret(secretName);

  console.log("Your secret is '" + retrievedSecret.value + "'.");
  console.log("Deleting your secret from " + keyVaultName + " ...");

  await client.beginDeleteSecret(secretName);

  console.log("Done.");

}

main()

```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立金鑰保存庫、儲存秘密，並擷取該秘密。 若要深入了解 Key Vault 以及要如何將其與應用程式整合，請繼續閱讀下列文章。

- 閱讀 [Azure Key Vault 概觀](../general/overview.md)
- 如何[針對金鑰保存庫的存取進行保護](../general/secure-your-key-vault.md)
- 參閱 [Azure Key Vault 開發人員指南](../general/developers-guide.md)
- 檢閱 [Azure Key Vault 最佳做法](../general/best-practices.md)
