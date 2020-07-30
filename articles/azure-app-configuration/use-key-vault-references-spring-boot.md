---
title: 在 Java Spring Boot 應用程式中使用 Azure 應用程式組態 Key Vault 參考的教學課程 | Microsoft Docs
description: 在此教學課程中，您將了解如何從 Java Spring Boot 應用程式中使用 Azure 應用程式組態的 Key Vault 參考
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 12/16/2019
ms.author: lcozzens
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 31aaa0134ffe34d0424868221f01b68b64e4b088
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87371153"
---
# <a name="tutorial-use-key-vault-references-in-a-java-spring-app"></a>教學課程：在 Java Spring 應用程式中使用 Key Vault 參考

在此教學課程中，您將了解如何搭配 Azure Key Vault 一起使用 Azure 應用程式組態服務。 應用程式組態與 Key Vault 是互補服務，將在大部分的應用程式部署中並行使用。

應用程式組態可協助您一起使用這些服務，方法是建立參考 Key Vault 中所儲存之值的金鑰。 當應用程式組態建立此類金鑰時，它會儲存 Key Vault 值的 URI，而不是值本身。

您的應用程式會使用應用程式組態用戶端提供者擷取 Key Vault 參考，就像是儲存在應用程式組態中的其他任何金鑰一樣。 在此案例中，儲存在應用程式組態中的值是參考 Key Vault 中之值的 URI。 它們不是 Key Vault 值或認證。 用戶端提供者會將金鑰視為 Key Vault 參考，因此它會使用 Key Vault 擷取其值。

您的應用程式會負責適當地驗證應用程式組態和 Key Vault。 這兩個服務不會直接通訊。

此教學課程說明如何在程式碼中實作 Key Vault 參考。 本文會以快速入門中介紹的 Web 應用程式作為基礎。 繼續之前，請先完成[使用應用程式設定建立 Java Spring 應用程式](./quickstart-java-spring-app.md)。

您可以使用任何程式碼編輯器來進行本教學課程中的步驟。 例如，[Visual Studio Code](https://code.visualstudio.com/) 是適用於 Windows、macOS 與 Linux 作業系統的跨平台程式碼編輯器。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立可參考 Key Vault 中儲存之值的應用程式組態金鑰。
> * 從 Java Spring 應用程式存取此金鑰的值。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
* 受支援的 [Java 開發套件 (JDK)](https://docs.microsoft.com/java/azure/jdk) 第 8 版。
* [Apache Maven](https://maven.apache.org/download.cgi) 3.0 版或更新版本。

## <a name="create-a-vault"></a>建立保存庫

1. 選取 Azure 入口網站左上角的 [建立資源]  選項：

    ![Key Vault 建立完成後的輸出](./media/quickstarts/search-services.png)
1. 在 [搜尋] 方塊中輸入 **Key Vault**。
1. 從結果清單，選取左側的 [金鑰保存庫]  。
1. 在 [金鑰保存庫]  中，選取 [新增]  。
1. 在 [建立金鑰保存庫]  的右側，提供下列資訊：
    * 選取 [訂用帳戶]  以選擇訂用帳戶。
    * 在 [資源群組]  中，選取 [新建]  ，然後輸入資源群組名稱。
    * 在 [金鑰保存庫名稱]  中，需要唯一的名稱。 針對此教學課程，請輸入 **Contoso-vault2**。
    * 在 [區域]  下拉式清單中，選擇一個位置。
1. 將其他 [建立金鑰保存庫]  選項維持為預設值。
1. 選取 [建立]  。

此時，您的 Azure 帳戶是唯一獲得授權可存取此新保存庫的帳戶。

![Key Vault 建立完成後的輸出](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>將祕密新增至 Key Vault

若要將祕密新增至保存庫，您只需要採取一些額外步驟。 在此案例中，請新增一則您可以用來測試 Key Vault 擷取的訊息。 此訊息稱為 **Message**，而您要將 "Hello from Key Vault" 的值儲存於其中。

1. 從 Key Vault 屬性頁面，選取 [祕密]  。
1. 選取 [產生/匯入]  。
1. 在 [建立祕密]  窗格中，輸入下列值：
    * **上傳選項**：輸入**手動**。
    * **Name**：輸入**訊息**。
    * **值**：輸入 **Hello from Key Vault**。
1. 保留其他 [建立祕密]  屬性的預設值。
1. 選取 [建立]  。

## <a name="add-a-key-vault-reference-to-app-configuration"></a>將 Key Vault 參考新增至應用程式組態

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [所有資源]  ，然後選取您在快速入門中建立的應用程式組態存放區執行個體。

1. 選取 [組態總管]  。

1. 選取 [+ 建立]   > [金鑰保存庫參考]  ，然後指定下列值：
    * **金鑰**：選取 [/application/config.keyvaultmessage] 
    * **標籤**︰將此值保留空白。
    * **訂用帳戶**、**資源群組**與**金鑰保存庫**：輸入與您在上一節所建立金鑰保存庫中的值相對應的值。
    * **祕密**：選取您在上一節中所建立、名為 **Message** 的祕密。

## <a name="connect-to-key-vault"></a>連線到 Key Vault

1. 在此教學課程中，您將使用服務主體來驗證 Key Vault。 若要建立此服務主體，請使用 Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 命令：

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    此作業會傳回一系列的金鑰/值組：

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

1. 執行下列命令，讓服務主體存取您的金鑰保存庫：

    ```console
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get
    ```

1. 執行下列命令以取得您的物件識別碼，然後將其新增至應用程式組態。

    ```console
    az ad sp show --id <clientId-of-your-service-principal>
    az role assignment create --role "App Configuration Data Reader" --assignee-object-id <objectId-of-your-service-principal> --resource-group <your-resource-group>
    ```

1. 使用上一個步驟中顯示的服務主體值，建立下列環境變數：

    * **AZURE_CLIENT_ID**：*clientId*
    * **AZURE_CLIENT_SECRET**：*clientSecret*
    * **AZURE_TENANT_ID**：*tenantId*

> [!NOTE]
> 這些 Key Vault 認證只會在您的應用程式中使用。  您的應用程式會使用這些認證直接向 Key Vault 驗證，而不會涉及應用程式組態服務。  Key Vault 會為您的應用程式和應用程式組態服務提供驗證，而不需共用或公開金鑰。

## <a name="update-your-code-to-use-a-key-vault-reference"></a>更新您的程式碼以使用 Key Vault 參考

1. 建立稱為 **APP_CONFIGURATION_ENDPOINT**的環境變數。 將其值設定為應用程式組態存放區的端點。 您可以在 Azure 入口網站中的 [存取金鑰]  刀鋒視窗上找到此端點。

1. 在 *resources* 資料夾中開啟 *bootstrap.properties*。 更新這個檔案，以使用應用程式組態端點，而不是連接字串。

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].endpoint= ${APP_CONFIGURATION_ENDPOINT}
    ```

1. 開啟 MessageProperties.java  。 新增名為 keyVaultMessage  的新變數：

    ```java
    private String keyVaultMessage;

    public String getKeyVaultMessage() {
        return keyVaultMessage;
    }

    public void setKeyVaultMessage(String keyVaultMessage) {
        this.keyVaultMessage = keyVaultMessage;
    }
    ```

1. 開啟 Hellocontroller.java  。 更新 getMessage  方法，以包含從 Key Vault 擷取到的訊息。

    ```java
    @GetMapping
    public String getMessage() {
        return "Message: " + properties.getMessage() + "\nKey Vault message: " + properties.getKeyVaultMessage();
    }
    ```

1. 建立名為 *AzureCredentials.java* 的新檔案並新增下列程式碼。

    ```java
    package com.example.demo;

    import com.azure.core.credential.TokenCredential;
    import com.azure.identity.EnvironmentCredentialBuilder;
    import com.microsoft.azure.spring.cloud.config.AppConfigurationCredentialProvider;
    import com.microsoft.azure.spring.cloud.config.KeyVaultCredentialProvider;

    public class AzureCredentials implements AppConfigurationCredentialProvider, KeyVaultCredentialProvider{

        @Override
        public TokenCredential getKeyVaultCredential(String uri) {
            return getCredential();
        }

        @Override
        public TokenCredential getAppConfigCredential(String uri) {
            return getCredential();
        }

        private TokenCredential getCredential() {
            return new EnvironmentCredentialBuilder().build();
        }

    }
    ```

1. 建立名為 *AppConfiguration.java*的新檔案。 以下新增下列程式碼。

    ```java
    package com.example.demo;

    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;

    @Configuration
    public class AppConfiguration {

        @Bean
        public AzureCredentials azureCredentials() {
            return new AzureCredentials();
        }
    }
    ```

1. 在資源的 META-INF 目錄中建立名為 *spring.factories* 的新檔案並新增。

    ```factories
    org.springframework.cloud.bootstrap.BootstrapConfiguration=\
    com.example.demo.AppConfiguration
    ```

1. 使用 Maven 建置 Spring Boot 應用程式並加以執行；例如：

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. 在您的應用程式執行之後，使用 *curl* 來測試您的應用程式；例如：

      ```shell
      curl -X GET http://localhost:8080/
      ```

    您會看到您在應用程式組態存放區中輸入的訊息。 您也會看到已在 Key Vault 中輸入的訊息。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>後續步驟

在此教學課程中，您建立了參考 Key Vault 中儲存之值的應用程式組態金鑰。 若要了解如何在 Java Spring 應用程式中使用功能旗標，請繼續進行下一個教學課程。

> [!div class="nextstepaction"]
> [受控識別整合](./quickstart-feature-flag-spring-boot.md)
