---
title: 快速入門 - 適用於 JavaScript (版本 4) 的 Azure Key Vault 金鑰用戶端程式庫
description: 了解如何使用 JavaScript 用戶端程式庫從 Azure Key Vault 建立、擷取和刪除金鑰
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: db6bb5c204bfe79b9d7470f651081aa4f4dcf2ed
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97932702"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-javascript-version-4"></a>快速入門：適用於 JavaScript (版本 4) 的 Azure Key Vault 金鑰用戶端程式庫

開始使用適用於 JavaScript 的 Azure Key Vault 金鑰用戶端程式庫。 [Azure Key Vault](../general/overview.md) 是一項雲端服務，可為金鑰提供安全的存放區。 您也可以安全地儲存金鑰、密碼、憑證和其他祕密。 您可以透過 Azure 入口網站建立和管理 Azure 金鑰保存庫。 在本快速入門中，您會了解如何使用 JavaScript 金鑰用戶端程式庫，從 Azure Key Vault 建立、擷取和刪除金鑰

Key Vault 用戶端程式庫資源：

[API 參考文件](/javascript/api/overview/azure/key-vault-index) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault) | [套件 (npm)](https://www.npmjs.com/package/@azure/keyvault-keys)

如需有關 Key Vault 及金鑰的詳細資訊，請參閱：
- [Key Vault 概觀](../general/overview.md)
- [金鑰概觀](about-keys.md)。

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 適用於您作業系統的 [Node.js](https://nodejs.org) 目前版本。
- [Azure CLI](/cli/azure/install-azure-cli)
- Key Vault - 您可以使用 [Azure 入口網站](../general/quick-create-portal.md)、[Azure CLI](../general/quick-create-cli.md) 或 [Azure PowerShell](../general/quick-create-powershell.md) 建立

本快速入門假設您執行 [Azure CLI](/cli/azure/install-azure-cli)。

## <a name="sign-in-to-azure"></a>登入 Azure

1. 執行 `login` 命令。

    ```azurecli-interactive
    az login
    ```

    如果 CLI 可以開啟預設瀏覽器，它會執行這項操作，並載入 Azure 登入頁面。

    否則，請在 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) 中開啟瀏覽器頁面，並輸入顯示在終端機中的授權碼。

2. 請在瀏覽器中使用您的帳戶認證登入。

## <a name="create-new-nodejs-application"></a>建立新的 Node.js 應用程式

接下來，建立可部署至雲端的 Node.js 應用程式。 

1. 在命令殼層中，建立名為 `key-vault-node-app` 的資料夾：

```azurecli
mkdir key-vault-node-app
```

1. 變更為新建立的 key-vault-node-app 目錄，然後執行 'init' 命令將節點專案初始化：

```azurecli
cd key-vault-node-app
npm init -y
```

## <a name="install-key-vault-packages"></a>安裝 Key Vault 套件

從主控台視窗安裝適用於 Node.js 的 Azure Key Vault [金鑰程式庫](https://www.npmjs.com/package/@azure/keyvault-keys)。

```azurecli
npm install @azure/keyvault-keys
```

安裝 [azure.identity](https://www.npmjs.com/package/@azure/identity) 套件，以向 Key Vault 驗證

```azurecli
npm install @azure/identity
```

## <a name="set-environment-variables"></a>設定環境變數

此應用程式使用金鑰保存庫名稱作為稱為 `KEY_VAULT_NAME` 的環境變數。

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS 或 Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="grant-access-to-your-key-vault"></a>授與對金鑰保存庫的存取權

建立金鑰保存庫的存取原則，將金鑰權限授與服務主體

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --key-permissions delete get list create purge
```

## <a name="code-examples"></a>程式碼範例

下列程式碼範例將說明如何建立用戶端、建立金鑰、擷取金鑰和刪除金鑰。 

### <a name="set-up-the-app-framework"></a>設定應用程式架構

1. 建立新的文字檔，並將儲存為 'index.js'

1. 新增必要呼叫以載入 Azure 和 Node.js 模組

1. 建立程式的結構，包括基本例外狀況處理

```javascript
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
    
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
```

### <a name="add-directives"></a>新增指示詞

將下列指示詞新增至程式碼頂端：

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { KeyClient } = require("@azure/keyvault-keys");
```

### <a name="authenticate-and-create-a-client"></a>驗證並建立用戶端

在本快速入門中，已登入的使用者是用來向金鑰保存庫進行驗證，這是本機開發的慣用方法。 針對部署至 Azure 的應用程式，應將受控識別指派給 App Service 或虛擬機器。如需詳細資訊，請參閱[受控識別概觀](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

在下列範例中，金鑰保存庫的名稱會以 "https://\<your-key-vault-name\>.vault.azure.net" 格式，擴充至金鑰保存庫 URI。 這個範例會使用來自 [Azure 身分識別程式庫](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme)的 ['DefaultAzureCredential()'](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) 類別，允許在各種不同的環境中使用相同的程式碼，搭配不同的選項來提供身分識別。 如需對金鑰保存庫進行驗證的詳細資訊，請參閱[開發人員指南](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code)。

將下列程式碼新增至 'main()' 函式

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new KeyClient(KVUri, credential);
```

### <a name="save-a-key"></a>儲存金鑰

現在，您的應用程式已通過驗證，您可以使用 [createKey 方法](/javascript/api/@azure/keyvault-keys/keyclient?#createKey_string__KeyType__CreateKeyOptions_)將金鑰放入您的 keyvault 中。該方法的參數會接受金鑰名稱和[金鑰類型](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keytype)

```javascript
await client.createKey(keyName, keyType);
```

### <a name="retrieve-a-key"></a>擷取金鑰

您現在可以使用 [getKey 方法](/javascript/api/@azure/keyvault-keys/keyclient?#getKey_string__GetKeyOptions_)來擷取先前設定的值。

```javascript
const retrievedKey = await client.getKey(keyName);
 ```

### <a name="delete-a-key"></a>刪除金鑰

最後，讓我們從您的金鑰保存庫中，使用 [beginDeleteKey](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keyclient?#beginDeleteKey_string__BeginDeleteKeyOptions_) 和 [purgeDeletedKey](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keyclient?#purgeDeletedKey_string__PurgeDeletedKeyOptions_) 方法來刪除並清除金鑰。

```javascript
const deletePoller = await client.beginDeleteKey(keyName);
await deletePoller.pollUntilDone();
await client.purgeDeletedKey(keyName);
```

## <a name="sample-code"></a>範例程式碼

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { KeyClient } = require("@azure/keyvault-keys");

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
  const client = new KeyClient(KVUri, credential);

  const keyName = "myKey";
  
  console.log("Creating a key in " + keyVaultName + " called '" + keyName + "` ...");
  await client.createKey(keyName, "RSA");

  console.log("Done.");

  console.log("Retrieving your key from " + keyVaultName + ".");

  const retrievedKey = await client.getKey(keyName);

  console.log("Your key version is '" + retrievedKey.properties.version + "'.");

  console.log("Deleting your key from " + keyVaultName + " ...");
  const deletePoller = await client.beginDeleteKey(keyName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your key from {keyVaultName} ...");
  await client.purgeDeletedKey(keyName);
  
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));

```

## <a name="test-and-verify"></a>測試和驗證

執行下列命令來執行應用程式。

```azurecli
npm install
npm index.js
```

下列輸出的變化會出現：

```azurecli
Creating a key in <your-unique-keyvault-name> called 'myKey' ... done.
Retrieving your key from mykeyvault.
Your key version is '8532359bced24e4bb2525f2d2050738a'.
Deleting your key from <your-unique-keyvault-name> ... done.  
Purging your key from <your-unique-keyvault-name> ... done.   
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立金鑰保存庫、儲存金鑰，並擷取該金鑰。 若要深入了解 Key Vault 以及要如何將其與應用程式整合，請繼續閱讀下列文章。

- 閱讀 [Azure Key Vault 概觀](../general/overview.md)
- 閱讀 [Azure Key Vault 金鑰的概觀](about-keys.md)
- 如何[針對金鑰保存庫的存取進行保護](../general/secure-your-key-vault.md)
- 參閱 [Azure Key Vault 開發人員指南](../general/developers-guide.md)
- 請參閱 [Key Vault 安全性概觀](../general/security-overview.md)
