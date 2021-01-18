---
title: 使用 IntelliJ 在 Azure Functions 中建立 Kotlin 函式
description: 了解如何使用 IntelliJ 建立簡單的 HTTP 觸發 Kotlin 函式，然後將其發佈至 Azure 以在無伺服器環境中執行。
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.openlocfilehash: 0207e4af9f845343866714ec207ca306cb327b36
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98035167"
---
# <a name="create-your-first-kotlin-function-in-azure-using-intellij"></a>使用 IntelliJ 在 Azure 中建立第一個 Kotlin 函式

本文說明如何在 IntelliJ IDEA 專案中建立 HTTP 觸發的 Java 函式、在整合式開發環境 (IDE) 中執行專案並進行偵錯，最後將函式專案部署至 Azure 中的函式應用程式。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>設定開發環境

若要使用 IntelliJ 建立 Kotlin 函式並將其發佈至 Azure，請安裝下列軟體：

- [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support) (JDK) 第 8 版
- [Apache Maven](https://maven.apache.org) 3.0 版或更高版本
- [IntelliJ IDEA](https://www.jetbrains.com/idea/download) Community 或 Ultimate 版 (含 Maven)
- [Azure CLI](/cli/azure)
- [2.x 版](functions-run-local.md#v2) 的 Azure Functions Core Tools。 其可提供撰寫、執行和偵錯 Azure Functions 的本機開發環境。

> [!IMPORTANT]
> JAVA_HOME 環境變數必須設定為 JDK 的安裝位置，才能完成本文步驟。

## <a name="create-a-function-project"></a>建立函式專案

1. 在 IntelliJ IDEA 中，選取 [建立新專案]  。  
1. 在 [新增專案]  視窗中，從左窗格選取 [Maven]  。
1. 選取 [從 Archetype 建立]  核取方塊，並針對 [azure-functions-kotlin-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype) 選取 [新增 Archetype]  。
1. 在 [新增 Archetype]  視窗中，完成如下欄位：
    - GroupId  ：com.microsoft.azure
    - _ArtifactId_：azure-functions-kotlin-archetype
    - _版本_：使用來自[中央存放庫](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype)的最新版本
    ![在 IntelliJ IDEA 中從 Archetype 建立 Maven 專案](media/functions-create-first-kotlin-intellij/functions-create-intellij.png)  
1. 依序選取 [確定]  和 [下一步]  。
1. 輸入目前專案的詳細資料，然後選取 [完成]  。

Maven 會在和 [ArtifactId]  值具有相同名稱的新資料夾中建立專案檔。 專案所產生的程式碼是 [HTTP 觸發](./functions-bindings-http-webhook.md)的簡單函式，此函式會回應觸發 HTTP 要求的本文。

## <a name="run-project-locally-in-the-ide"></a>在 IDE 本機執行專案

> [!NOTE]
> 若要在本機執行專案並進行偵錯，請確定您已安裝 [Azure Functions Core Tools 第 2 版](functions-run-local.md#v2)。

1. 手動匯入變更，或啟用[自動匯入](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html)。
1. 開啟 [Maven 專案]  工具列。
1. 展開 [生命週期]  ，然後開啟 [套件]  。 解決方案會建置並封裝在新建立的目標目錄中。
1. 展開 [外掛程式]   > [azure-functions]  ，然後開啟 [azure-functions:run]  以啟動 Azure Functions 本機執行階段。  
  ![適用於 Azure Functions 的 Maven 工具列](media/functions-create-first-kotlin-intellij/functions-intellij-kotlin-maven-toolbar.png)  

1. 當您完成測試函式時，關閉執行對話方塊。 一次只能有一個函式主機是作用中且在本機執行。

## <a name="debug-the-project-in-intellij"></a>在 IntelliJ 中進行專案偵錯

1. 若要以偵錯模式啟動函式主機，請新增 **-DenableDebug** 作為您執行函式時的引數。 您可以變更 [maven 目標](https://www.jetbrains.com/help/idea/maven-support.html#run_goal)中的組態，也可以在終端機視窗中執行下列命令：  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   此命令會讓函式主機開啟 5005 偵錯連接埠。

1. 在 [執行]  功能表中選取 [編輯組態]  。
1. 選取 **(+)** 以新增 [遠端]  。
1. 完成 [名稱]  和 [設定]  欄位，然後選取 [確定]  以儲存組態。
1. 設定完成後，選取 [對 <遠端組態名稱> 進行偵錯]  或在鍵盤上按 Shift+F9 來開始偵錯。

   ![在 IntelliJ 中進行專案偵錯](media/functions-create-first-kotlin-intellij/debug-configuration-intellij.PNG)

1. 完成時，停止偵錯工具和執行中處理序。 一次只能有一個函式主機是作用中且在本機執行。

## <a name="deploy-the-project-to-azure"></a>將專案部署至 Azure

1. 您必須先[使用 Azure CLI 進行登入](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)，才可以將專案部署到 Azure 中的函式應用程式。

   ``` azurecli
   az login
   ```

1. 使用 `azure-functions:deploy` Maven 目標，將您的程式碼部署到新的函式應用程式。 您也可以在 [Maven 專案] 視窗中選取 [azure-functions:deploy]  選項。

   ```
   mvn azure-functions:deploy
   ```

1. 成功部署函式應用程式之後，請在 Azure CLI 輸出中尋找 HTTP 觸發程序函式的 URL。

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>後續步驟

既然您已將第一個 Kotlin 函式應用程式部署至 Azure，請檢閱 [Azure Functions Java 開發人員指南](functions-reference-java.md)，以取得開發 Java 和 Kotlin 函式的詳細資訊。
- 使用 `azure-functions:add` Maven 目標，將具有不同觸發程序的其他函式應用程式新增至專案。
