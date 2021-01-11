---
title: 快速入門 - 適用於 Java 的 Azure Key Vault 金鑰用戶端程式庫
description: 提供適用於 Java 的 Azure Key Vault 金鑰用戶端程式庫的快速入門。
author: msmbaldwin
ms.custom: devx-track-java, devx-track-azurecli
ms.author: mbaldwin
ms.date: 01/05/2021
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.openlocfilehash: cb5abf59c446ef0835375bac45d1e852144a6f28
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935269"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-java"></a>快速入門：適用於 Java 的 Azure Key Vault 金鑰用戶端程式庫
開始使用適用於 Java 的 Azure Key Vault 金鑰用戶端程式庫。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。

其他資源：

* [原始程式碼](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys) \(英文\)
* [API 參考文件](https://azure.github.io/azure-sdk-for-java/keyvault.html)
* [產品文件](index.yml)
* [範例](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys/src/samples/java/com/azure/security/keyvault/keys)

## <a name="prerequisites"></a>必要條件
- Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Java 開發套件 (JDK)](/java/azure/jdk/) 8 版或更新版本
- [Apache Maven](https://maven.apache.org)
- [Azure CLI](/cli/azure/install-azure-cli)

本快速入門假設您是在 Linux 終端機視窗中執行 [Azure CLI](/cli/azure/install-azure-cli) 和 [Apache Maven](https://maven.apache.org)。

## <a name="setting-up"></a>設定
本快速入門會使用 Azure 身分識別程式庫搭配 Azure CLI，向 Azure 服務驗證使用者。 開發人員也可以使用 Visual Studio 或 Visual Studio Code 來驗證其呼叫。如需詳細資訊，請參閱[使用 Azure 身分識別用戶端程式庫驗證用戶端](/java/api/overview/azure/identity-readme)。

### <a name="sign-in-to-azure"></a>登入 Azure
1. 執行 `login` 命令。

    ```azurecli-interactive
    az login
    ```

   如果 CLI 可以開啟預設瀏覽器，它會執行這項操作，並載入 Azure 登入頁面。

   否則，請在 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) 開啟瀏覽器頁面，並輸入顯示在終端機中的授權碼。

2. 請在瀏覽器中使用您的帳戶認證登入。

### <a name="create-a-new-java-console-app"></a>建立新的 Java 主控台應用程式
在主控台視窗中，使用 `mvn` 命令建立名為 `akv-keys-java` 的新 Java 主控台應用程式。

```console
mvn archetype:generate -DgroupId=com.keyvault.keys.quickstart
                       -DartifactId=akv-keys-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

產生專案的輸出會顯示如下：

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.keys.quickstart
[INFO] Parameter: artifactId, Value: akv-keys-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.keys.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.keys.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.keys.quickstart
[INFO] Parameter: artifactId, Value: akv-keys-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-keys-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

將目錄變更為新建立的 `akv-keys-java/` 資料夾。

```console
cd akv-keys-java
```

### <a name="install-the-package"></a>安裝套件
在文字編輯器中開啟 *pom.xml* 檔案。 將下列相依性元素新增至相依性群組。

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-keys</artifactId>
      <version>4.2.3</version>
    </dependency>

    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-identity</artifactId>
      <version>1.2.0</version>
    </dependency>
```

### <a name="create-a-resource-group-and-key-vault"></a>建立資源群組和金鑰保存庫
[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

#### <a name="grant-access-to-your-key-vault"></a>授與對金鑰保存庫的存取權
建立金鑰保存庫的存取原則，將金鑰權限授與服務主體。

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --key-permissions delete get list create purge
```

#### <a name="set-environment-variables"></a>設定環境變數
此應用程式使用您的金鑰保存庫名稱作為名為 `KEY_VAULT_NAME` 的環境變數。

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

## <a name="object-model"></a>物件模型
適用於 Java 的 Azure Key Vault 金鑰用戶端程式庫可讓您管理金鑰。 [程式碼範例](#code-examples) 一節會示範如何建立用戶端、建立金鑰、擷取金鑰，以及刪除金鑰。

整個主控台應用程式[如下](#sample-code)。

## <a name="code-examples"></a>程式碼範例
### <a name="add-directives"></a>新增指示詞
將下列指示詞新增至程式碼頂端：

```java
import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.keys.KeyClient;
import com.azure.security.keyvault.keys.KeyClientBuilder;
import com.azure.security.keyvault.keys.models.DeletedKey;
import com.azure.security.keyvault.keys.models.KeyType;
import com.azure.security.keyvault.keys.models.KeyVaultKey;
```

### <a name="authenticate-and-create-a-client"></a>驗證並建立用戶端
在本快速入門中，已登入的使用者是用來向 Key Vault 進行驗證，這是本機開發的慣用方法。 針對部署至 Azure 的應用程式，應將受控識別指派給 App Service 或虛擬機器。 如需詳細資訊，請參閱[受控識別概觀](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

在下列範例中，金鑰保存庫的名稱會以 "https://\<your-key-vault-name\>.vault.azure.net" 格式，擴充至金鑰保存庫 URI。 這個範例會使用 ['DefaultAzureCredential()'](https://docs.microsoft.com/java/api/com.azure.identity.defaultazurecredential) 類別，允許在各種不同的環境中使用相同的程式碼，搭配不同的選項來提供身分識別。 如需詳細資訊，請參閱[預設 Azure 認證驗證](https://docs.microsoft.com/java/api/overview/azure/identity-readme)。

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

KeyClient keyClient = new KeyClientBuilder()
    .vaultUrl(keyVaultUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="create-a-key"></a>建立金鑰
現在應用程式已通過驗證，您可以使用 `keyClient.createKey` 方法在金鑰保存庫中建立金鑰。 這需要金鑰的名稱和金鑰類型 -- 我們已將 "myKey" 值指派給 `keyName` 變數，並在此範例中使用 RSA `KeyType`。

```java
keyClient.createKey(keyName, KeyType.RSA);
```

您可以確認金鑰是否已使用 [az keyvault key show](/cli/azure/keyvault/key?#az-keyvault-key-show) 命令來加以設定：

```azurecli
az keyvault key show --vault-name <your-unique-key-vault-name> --name myKey
```

### <a name="retrieve-a-key"></a>擷取金鑰
您現在可以使用 `keyClient.getKey` 方法來擷取先前建立的金鑰。

```java
KeyVaultKey retrievedKey = keyClient.getKey(keyName);
 ```

您現在可以使用 `retrievedKey.getProperties`、`retrievedKey.getKeyOperations` 等作業來存取已取出金鑰的詳細資料。

### <a name="delete-a-key"></a>刪除金鑰
最後，我們將使用 `keyClient.beginDeleteKey` 方法從金鑰保存庫中刪除金鑰。

刪除金鑰是長時間執行的作業，您可以輪詢進度或等待其完成。

```java
SyncPoller<DeletedKey, Void> deletionPoller = keyClient.beginDeleteKey(keyName);
deletionPoller.waitForCompletion();
```

您可以確認金鑰是否已使用 [az keyvault key show](/cli/azure/keyvault/key?#az-keyvault-key-show) 命令來加以刪除：

```azurecli
az keyvault key show --vault-name <your-unique-key-vault-name> --name myKey
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
```java
package com.keyvault.keys.quickstart;

import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.keys.KeyClient;
import com.azure.security.keyvault.keys.KeyClientBuilder;
import com.azure.security.keyvault.keys.models.DeletedKey;
import com.azure.security.keyvault.keys.models.KeyType;
import com.azure.security.keyvault.keys.models.KeyVaultKey;

public class App {
    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {
        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and key vault URI = %s \n", keyVaultName, keyVaultUri);

        KeyClient keyClient = new KeyClientBuilder()
                .vaultUrl(keyVaultUri)
                .credential(new DefaultAzureCredentialBuilder().build())
                .buildClient();

        String keyName = "myKey";

        System.out.print("Creating a key in " + keyVaultName + " called '" + keyName + " ... ");

        keyClient.createKey(keyName, KeyType.RSA);

        System.out.print("done.");
        System.out.println("Retrieving key from " + keyVaultName + ".");

        KeyVaultKey retrievedKey = keyClient.getKey(keyName);

        System.out.println("Your key's ID is '" + retrievedKey.getId() + "'.");
        System.out.println("Deleting your key from " + keyVaultName + " ... ");

        SyncPoller<DeletedKey, Void> deletionPoller = keyClient.beginDeleteKey(keyName);
        deletionPoller.waitForCompletion();

        System.out.print("done.");
    }
}
```

## <a name="next-steps"></a>後續步驟
在本快速入門中，您已建立金鑰保存庫、建立金鑰、擷取該金鑰然後刪除。 若要深入了解 Key Vault 以及要如何將其與應用程式整合，請繼續閱讀下列文章。

- 閱讀 [Azure Key Vault 概觀](../general/overview.md)
- 閱讀 [Key Vault 安全性概觀](../general/security-overview.md)
- 參閱 [Azure Key Vault 開發人員指南](../general/developers-guide.md)
- 如何[針對金鑰保存庫的存取進行保護](../general/secure-your-key-vault.md)