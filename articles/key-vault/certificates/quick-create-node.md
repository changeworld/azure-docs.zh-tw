---
title: 快速入門 - 適用於 JavaScript (版本 4) 的 Azure Key Vault 憑證用戶端程式庫
description: 了解如何使用 JavaScript 用戶端程式庫，從 Azure Key Vault 建立、擷取和刪除憑證
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: a96c3a2dc3b013f79799f20595bf5b5f575b363b
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/30/2020
ms.locfileid: "97826093"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-javascript-version-4"></a>快速入門：適用於 JavaScript (版本 4) 的 Azure Key Vault 憑證用戶端程式庫

開始使用適用於 JavaScript 的 Azure Key Vault 憑證用戶端程式庫。 [Azure Key Vault](../general/overview.md) 是一項雲端服務，可為憑證提供安全的存放區。 您也可以安全地儲存金鑰、密碼、憑證和其他祕密。 您可以透過 Azure 入口網站建立和管理 Azure 金鑰保存庫。 在本快速入門中，您會了解如何使用 JavaScript 用戶端程式庫，從 Azure Key Vault 建立、擷取和刪除憑證

Key Vault 用戶端程式庫資源：

[API 參考文件](/javascript/api/overview/azure/key-vault-index) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault) | [套件 (npm)](https://www.npmjs.com/package/@azure/keyvault-certificates)

如需有關 Key Vault 及憑證的詳細資訊，請參閱：
- [Key Vault 概觀](../general/overview.md)
- [憑證概觀](about-certificates.md)。

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

從主控台視窗安裝適用於 Node.js 的 Azure Key Vault [憑證程式庫](https://www.npmjs.com/package/@azure/keyvault-certificates)。

```azurecli
npm install @azure/keyvault-certificates
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
$Env:KEY_VAULT_NAME=<your-key-vault-name>
```

macOS 或 Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="grant-access-to-your-key-vault"></a>授與對金鑰保存庫的存取權

建立金鑰保存庫的存取原則，將憑證權限授與服務主體

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --certificate-permissions delete get list create purge
```

## <a name="code-examples"></a>程式碼範例

下列程式碼範例會說明如何建立用戶端、設定憑證、擷取憑證和刪除憑證。 

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
const { CertificateClient } = require("@azure/keyvault-certificates");
```

### <a name="authenticate-and-create-a-client"></a>驗證並建立用戶端

在本快速入門中，已登入的使用者是用來向金鑰保存庫進行驗證，這是本機開發的慣用方法。 針對部署至 Azure 的應用程式，應將受控識別指派給 App Service 或虛擬機器。如需詳細資訊，請參閱[受控識別概觀](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

在下列範例中，金鑰保存庫的名稱會以 "https://\<your-key-vault-name\>.vault.azure.net" 格式，擴充至金鑰保存庫 URI。 這個範例會使用來自 [Azure 身分識別程式庫](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme)的 ['DefaultAzureCredential()'](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) 類別，允許在各種不同的環境中使用相同的程式碼，搭配不同的選項來提供身分識別。 如需對金鑰保存庫進行驗證的詳細資訊，請參閱[開發人員指南](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code)。

將下列程式碼新增至 'main()' 函式

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new Certificate(KVUri, credential);
```

### <a name="save-a-certificate"></a>儲存憑證

現在應用程式已通過驗證，您可以使用 [beginCreateCertificate 方法](/javascript/api/@azure/keyvault-certificates/certificateclient?#beginCreateCertificate_string__CertificatePolicy__BeginCreateCertificateOptions_)將憑證放入您的 keyvault 中。這需要憑證的名稱和憑證原則[憑證原則](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificatepolicy)與[憑證原則屬性](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificatepolicyproperties)

```javascript
const certificatePolicy = {
  issuerName: "Self",
  subject: "cn=MyCert"
};
const createPoller = await client.beginCreateCertificate(certificateName, certificatePolicy);
const certificate = await poller.pollUntilDone();
```

> [!NOTE]
> 如果憑證名稱存在，則上述程式碼會建立該憑證的新版本。
### <a name="retrieve-a-certificate"></a>擷取憑證

您現在可以使用 [getCertificate 方法](/javascript/api/@azure/keyvault-certificates/certificateclient?#getCertificate_string__GetCertificateOption)擷取先前設定的值。

```javascript
const retrievedCertificate = await client.getCertificate(certificateName);
 ```

### <a name="delete-a-certificate"></a>刪除憑證

最後，讓我們從您的金鑰保存庫中，使用[beginDeleteCertificate]https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificateclient?#beginDeleteCertificate_string__BeginDeleteCertificateOptions_) 和 [purgeDeletedCertificate](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificateclient?#purgeDeletedCertificate_string__PurgeDeletedCertificateOptions_) 方法來刪除並清除憑證。

```javascript
const deletePoller = await client.beginDeleteCertificate(certificateName);
await deletePoller.pollUntilDone();
await client.purgeDeletedCertificate(certificateName);
```

## <a name="sample-code"></a>範例程式碼

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { CertificateClient } = require("@azure/keyvault-certificates");

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

  const string certificateName = "myCertificate";
  const keyVaultName = process.env["KEY_VAULT_NAME"];
  const KVUri = "https://" + keyVaultName + ".vault.azure.net";

  const credential = new DefaultAzureCredential();
  const client = new CertificateClient(KVUri, credential);

  console.log("Creating a certificate in " + keyVaultName + " called '" + certificateName +  "` ...");
  const certificatePolicy = {
  issuerName: "Self",
  subject: "cn=MyCert"
  };
  const createPoller = await client.beginCreateCertificate(certificateName, certificatePolicy);
  const certificate = await poller.pollUntilDone();

  console.log("Done.");

  console.log("Retrieving your certificate from " + keyVaultName + ".");

  const retrievedCertificate = await client.getCertificate(certificateName);

  console.log("Your certificate version is '" + retrievedCertificate.properties.version + "'.");

  console.log("Deleting your certificate from " + keyVaultName + " ...");
  const deletePoller = await client.beginDeleteCertificate(certificateName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your certificate from {keyVaultName} ...");
  await client.purgeDeletedCertificate(certificateName);
  
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
Creating a certificate in mykeyvault called 'myCertificate' ... done.
Retrieving your certificate from mykeyvault.
Your certificate version is '8532359bced24e4bb2525f2d2050738a'.
Deleting your certificate from mykeyvault ... done
Purging your certificate from mykeyvault ... done 
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立金鑰保存庫、儲存憑證，並擷取該憑證。 若要深入了解 Key Vault 以及要如何將其與應用程式整合，請繼續閱讀下列文章。

- 閱讀 [Azure Key Vault 概觀](../general/overview.md)
- 閱讀[憑證概觀](about-certificates.md)
- 參閱[從 App Service 應用程式存取 Key Vault 教學課程](../general/tutorial-net-create-vault-azure-web-app.md)
- 參閱[從虛擬機器存取 Key Vault 教學課程](../general/tutorial-net-virtual-machine.md)
- 參閱 [Azure Key Vault 開發人員指南](../general/developers-guide.md)
- 檢閱 [Azure Key Vault 最佳做法](../general/best-practices.md)
