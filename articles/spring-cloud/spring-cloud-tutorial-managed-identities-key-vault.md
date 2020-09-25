---
title: 教學課程 - 將 Key Vault 連線至 Azure Spring Cloud 應用程式的受控識別
description: 設定受控識別，以將 Key Vault 連線至 Azure Spring Cloud 應用程式
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/08/2020
ms.custom: devx-track-java
ms.openlocfilehash: 646b95e7e106b8657f8aeec2426b88cd6da20357
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90885653"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-spring-cloud-app"></a>教學課程：使用受控識別將 Key Vault 連線至 Azure Spring Cloud 應用程式

**本文適用於：** ✔️ Java

本文說明如何建立 Azure Spring Cloud 應用程式的受控識別，並用其來存取 Azure Key Vault。

Azure Key Vault 可用來安全地儲存應用程式的權杖、密碼、憑證、API 金鑰和其他祕密，並嚴密控制其存取。 您可以在 Azure Active Directory (AAD) 中建立受控識別，並向任何支援 AAD 驗證的服務 (包括 Key Vault) 進行驗證，而不需要在您的程式碼中顯示認證。

## <a name="prerequisites"></a>必要條件

* [註冊 Azure 訂用帳戶](https://azure.microsoft.com/free/)
* [安裝 Azure CLI 2.0.67 版或更新版本](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)
* [安裝 Maven 3.0 或更新版本](https://maven.apache.org/download.cgi) \(英文\)

## <a name="create-a-resource-group"></a>建立資源群組
資源群組是在其中部署與管理 Azure 資源的邏輯容器。 使用 [az group create](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-create) 命令建立用來包含 Key Vault 和 Spring Cloud 的資源群組：

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="set-up-your-key-vault"></a>設定您的 Key Vault
若要建立 Key Vault，請使用 [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest&preserve-view=true#az-keyvault-create) 命令：

> [!Important]
> 每個金鑰保存庫必須有唯一的名稱。 在下列範例中，以您的 Key Vault 名稱取代 <your-keyvault-name>。

```azurecli-interactive
az keyvault create --name "<your-keyvault-name>" -g "myResourceGroup"
```

記下傳回的 `vaultUri`，其格式為 "https://<your-keyvault-name>.vault.azure.net"。 這會在下列步驟中用到。

您現在可以使用 [az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest&preserve-view=true#az-keyvault-secret-set) 命令將秘密放在 Key Vault 中：

```azurecli-interactive
az keyvault secret set --vault-name "<your-keyvault-name>" \
    --name "connectionString" \
    --value "jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;"
```

## <a name="create-azure-spring-cloud-service-and-app"></a>建立 Azure Spring Cloud 服務和應用程式
安裝對應的擴充功能之後，請使用 Azure CLI 命令 `az spring-cloud create` 建立 Azure Spring Cloud 執行個體。 

```azurecli-interactive
az extension add --name spring-cloud
az spring-cloud create -n "myspringcloud" -g "myResourceGroup"
```
下列範例會根據 `--assign-identity`參數的要求，建立具有系統指派受控識別且名為 `springapp` 的虛擬機器。

```azurecli
az spring-cloud app create -n "springapp" -s "myspringcloud" -g "myResourceGroup" --is-public true --assign-identity
export SERVICE_IDENTITY=$(az spring-cloud app show --name "springapp" -s "myspringcloud" -g "myResourceGroup" | jq -r '.identity.principalId')
```

記下傳回的 `url`，其格式為 "https://<your-app-name>.azuremicroservices.io"。 這會在下列步驟中用到。


## <a name="grant-your-app-access-to-key-vault"></a>將您的應用程式存取權授與 Key Vault
使用 `az keyvault set-policy`，在 Key Vault 中為您的應用程式授與適當存取權。
```azurecli
az keyvault set-policy --name "<your-keyvault-name>" --object-id ${SERVICE_IDENTITY} --secret-permissions set get list
```

## <a name="build-a-sample-spring-boot-app-with-spring-boot-starter"></a>使用 Spring Boot Starter 建立 Spring Boot 應用程式範例
此應用程式將具有從 Azure Key Vault 取得祕密的存取權。 使用 Starter 應用程式：[Azure Key Vault 祕密的 Spring Boot Starter](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-keyvault-secrets)。  Azure Key Vault 會新增為 Spring **PropertySource** 的執行個體。  儲存在 Azure Key Vault 中的祕密可方便存取和使用，且用法與任何外部化設定屬性一樣 (例如檔案中的屬性)。 

1. 使用 Azure Key Vault Spring Starter 從 start.spring.io 產生範例專案。 
    ```azurecli
    curl https://start.spring.io/starter.tgz -d dependencies=web,azure-keyvault-secrets -d baseDir=springapp -d bootVersion=2.3.1.RELEASE -d javaVersion=1.8 | tar -xzvf -
    ```

2. 在應用程式中指定您的 Key Vault。 

    ```azurecli
    cd springapp
    vim src/main/resources/application.properties
    ```

    若要使用Azure Spring Cloud 應用程式的受控識別，請將具有下列內容的屬性新增至 src/main/resources/application.properties。

    ```
    azure.keyvault.enabled=true
    azure.keyvault.uri=https://<your-keyvault-name>.vault.azure.net
    ```
    > [!Note] 
    > 必須在上述 `application.properties` 中新增金鑰保存庫 URL。 否則，執行時間期間可能無法捕捉到金鑰保存庫 URL。

3. 將程式碼範例新增至 src/main/java/com/example/demo/DemoApplication.java。 其會從 Key Vault 擷取連接字串。 

    ```Java
    package com.example.demo;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.boot.CommandLineRunner;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;

    @SpringBootApplication
    @RestController
    public class DemoApplication implements CommandLineRunner {

        @Value("${connectionString}")
        private String connectionString;

        public static void main(String[] args) {
          SpringApplication.run(DemoApplication.class, args);
        }

        @GetMapping("get")
        public String get() {
          return connectionString;
        }

        public void run(String... varl) throws Exception {
          System.out.println(String.format("\nConnection String stored in Azure Key Vault:\n%s\n",connectionString));
        }
      }
    ```

    如果您開啟 pom.xml，您會看到 `azure-keyvault-secrets-spring-boot-starter` 的相依性。 將此相依性新增至您在 pom.xml 中的專案。 

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-keyvault-secrets-spring-boot-starter</artifactId>
    </dependency>
    ```

4. 封裝您的範例應用程式。 

    ```azurecli
    mvn clean package
    ```

5. 現在您可以使用 Azure CLI 命令 `az spring-cloud app deploy`，將您的應用程式部署至 Azure。 

    ```azurecli
    az spring-cloud app deploy -n "springapp" -s "myspringcloud" -g "myResourceGroup" --jar-path target/demo-0.0.1-SNAPSHOT.jar
    ```

6.  若要測試您的應用程式，請存取公用端點或測試端點。

    ```azurecli
    curl https://myspringcloud-springapp.azuremicroservices.io/get
    ```

    您將會看到訊息：「已成功從 Key Vault https://<your-keyvault-name>.vault.azure.net/: jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE; 取得祕密 connectionString 的值」。

## <a name="build-sample-spring-boot-app-with-java-sdk"></a>使用 Java SDK 建立 Spring Boot 應用程式範例

此範例可以設定並取得 Azure Key Vault 中的祕密。 [適用於 Java 的 Azure Key Vault 祕密用戶端程式庫](https://docs.microsoft.com/java/api/overview/azure/security-keyvault-secrets-readme?view=azure-java-stablelibrary&preserve-view=true)會在 Azure SDK 上提供 Azure Active Directory 權杖驗證支援。 其會提供一組 **TokenCredential** 的實作，以用來建構可支援 AAD 權杖驗證的 Azure SDK 用戶端。

Azure Key Vault 祕密用戶端程式庫可讓您安全地儲存及控制權杖、密碼、API 金鑰和其他祕密的存取權。 此程式庫提供建立、擷取、更新、刪除、清除、備份、還原和列出秘密及其版本的作業。

1. 複製範例專案。 

    ```azurecli
    git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
    ```

2. 在應用程式中指定您的 Key Vault。 

    ```azurecli
    cd Azure-Spring-Cloud-Samples/managed-identity-keyvault
    vim src/main/resources/application.properties
    ```

    若要使用Azure Spring Cloud 應用程式的受控識別，請將具有下列內容的屬性新增至 src/main/resources/application.properties。

    ```
    azure.keyvault.enabled=true
    azure.keyvault.uri=https://<your-keyvault-name>.vault.azure.net
    ```

3. 請包含 [ManagedIdentityCredentialBuilder](https://docs.microsoft.com/java/api/com.azure.identity.managedidentitycredentialbuilder?view=azure-java-stable&preserve-view=true)，以從 Azure Active Directory 取得權杖，以及包含 [SecretClientBuilder](https://docs.microsoft.com/java/api/com.azure.security.keyvault.secrets.secretclientbuilder?view=azure-java-stable&preserve-view=true)，以在程式碼中設定或取得來自 Key Vault 的祕密。

    在已複製的範例專案中，從 [MainController.java](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/managed-identity-keyvault/src/main/java/com/microsoft/azure/MainController.java#L28) 取得範例。

    請同時包含 `azure-identity` 和 `azure-security-keyvault-secrets` 來作為 pom.xml 的相依性。 在已複製的範例專案中，從 [pom.xml](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/managed-identity-keyvault/pom.xml#L21) 取得範例。 

4. 封裝您的範例應用程式。 

    ```azurecli
    mvn clean package
    ```

5. 現在，使用 Azure CLI 命令 `az spring-cloud app deploy` 將應用程式部署至 Azure。 

    ```azurecli
    az spring-cloud app deploy -n "springapp" -s "myspringcloud" -g "myResourceGroup" --jar-path target/asc-managed-identity-keyvault-sample-0.1.0.jar
    ```

6. 存取公用端點或測試端點，以測試您的應用程式。 

    首先，取得您在 Azure Key Vault 中設定的祕密值。 
    ```azurecli
    curl https://myspringcloud-springapp.azuremicroservices.io/secrets/connectionString
    ```

    您將會看到訊息：「已成功從 Key Vault https://<your-keyvault-name>.vault.azure.net/: jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE; 取得祕密 connectionString 的值」。

    現在，請建立祕密，然後使用 Java SDK 來加以擷取。 
    ```azurecli
    curl -X PUT https://myspringcloud-springapp.azuremicroservices.io/secrets/test?value=success

    curl https://myspringcloud-springapp.azuremicroservices.io/secrets/test
    ```

    您將會看到訊息：「已成功從 Key Vault https://<your-keyvault-name>.vault.azure.net: success 取得祕密測試值」。 

## <a name="next-steps"></a>後續步驟

* [如何在 Azure Spring Cloud 中使用受控識別存取儲存體 Blob](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [如何針對 Azure Spring Cloud 應用程式啟用系統指派的受控識別](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity)
* [深入了解 Azure 資源受控識別](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [在 GitHub Actions 中使用 Key Vault 驗證 Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-github-actions-key-vault)

