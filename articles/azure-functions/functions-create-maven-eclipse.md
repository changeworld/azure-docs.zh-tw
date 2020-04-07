---
title: 使用 Java 與 Eclipse 建立 Azure 函數應用
description: 使用 Java 和 Eclipse 建立 HTTP 觸發的簡單無伺服器應用程式並發佈到 Azure Functions 的操作說明指南。
author: jeffhollan
ms.topic: how-to
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: 42e9ed7c080c9274fad7eda8e4c8af3631ed41f5
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756477"
---
# <a name="create-your-first-function-with-java-and-eclipse"></a>使用 Java 與 Eclipse 建立第一個函式 

本文示範如何使用 Eclipse IDEA 和 Apache Maven 建立[無伺服器](https://azure.microsoft.com/solutions/serverless/)函式專案、測試此專案並加以偵錯，然後將它部署至 Azure Functions。 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>設定開發環境

若要使用 Java 和 Eclipse 開發函數應用程式，您必須安裝下列項目：

-  [Java開發人員工具組](https://www.azul.com/downloads/zulu/),版本8。
-  [阿帕奇馬文](https://maven.apache.org),版本3.0或以上。
-  [Eclipse](https://www.eclipse.org/downloads/packages/)，具有 Java 和 Maven 支援。
-  [Azure CLI](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> JAVA_HOME 環境變數必須設定為 JDK 的安裝位置，才能完成本快速入門。

強烈建議您也要安裝 [Azure Functions Core Tools 第 2 版](functions-run-local.md#v2)，其提供用於執行和偵錯 Azure Functions 的本機環境。 

## <a name="create-a-functions-project"></a>建立 Functions 專案

1. 在 Eclipse 中,選擇 **「檔**」功能表,然後選擇 **「新建 -&gt; Maven 專案**」。 
1. 接受 [新增 Maven 專案]**** 對話方塊中的預設值，然後選取 [下一步]****。
1. 選取 [新增原型]**** 並新增 [azure-functions-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype) 的項目。
    - 原型群組識別碼：com.microsoft.azure
    - 原型構件識別碼：azure-functions-archetype
    - 版本:檢查並使用[中央儲存函式庫](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![Eclipse Maven 建立的最新版本](media/functions-create-first-java-eclipse/functions-create-eclipse.png)  
1. 按一下 **[確定]**，然後按 **[下一步]**。  請務必填寫所有欄位的值,包括`resourceGroup`,`appName``appRegion`以及 (請使用除 fabrikam 函數以外的其他應用程式名稱 **-201709201201928),** 並最終**完成**。
    ![Eclipse Maven 建立2](media/functions-create-first-java-eclipse/functions-create-eclipse2.png)  

Maven 會以 _artifactId_ 名稱在新的資料夾中建立專案檔。 專案中產生的程式碼是 [HTTP 觸發](/azure/azure-functions/functions-bindings-http-webhook)的簡單函式，此函式會回應觸發 HTTP 要求的本文。

## <a name="run-functions-locally-in-the-ide"></a>在 IDE 中本機執行函式

> [!NOTE]
> [Azure Functions Core Tools 第 2 版](functions-run-local.md#v2)必須安裝，才能在本機執行和偵錯函式。

1. 在產生的專案上按一下滑鼠右鍵，然後選擇 [執行身分]**** 並 [Maven 組建]****。
1. 在 [編輯組態]**** 對話方塊的 [目標]**** 和 [名稱]**** 欄位中輸入 `package`，然後選取 [執行]****。 這會建置並封裝函式程式碼。
1. 建置完成後，使用 `azure-functions:run` 做為目標和名稱，建立另一個如上所述的執行組態。 按一下 [執行]****，在 IDE 中執行函式。

當您完成函式測試時，請在主控台視窗中終止執行階段。 一次只能有一個函式主機是作用中且在本機執行。

### <a name="debug-the-function-in-eclipse"></a>在 Eclipse 中進行函式偵錯

在上一個步驟的 [執行身分]**** 組態設定中，將 `azure-functions:run` 變更為 `azure-functions:run -DenableDebug` 並執行更新後的組態，以在偵錯模式中啟動函數應用程式。

選取 [執行]**** 功能表，然後開啟 [偵錯組態]****。 選擇 [遠端 Java 應用程式]**** 並建立新的應用程式。 為您的組態提供名稱，然後填入設定中。 連接埠應該與函式主機所開啟的偵錯連接埠一致，其預設值為 `5005`。 安裝之後，按一下 `Debug` 開始偵錯。

![在 Eclipse 中進行函式偵錯](media/functions-create-first-java-eclipse/debug-configuration-eclipse.PNG)

設定中斷點，並使用 IDE 檢查您函式中的物件。 完成時，停止偵錯工具和執行中的函式主機。 一次只能有一個函式主機是作用中且在本機執行。

## <a name="deploy-the-function-to-azure"></a>將函式部署到 Azure

部署到 Azure Functions 的程序從 Azure CLI 使用帳戶認證。 [使用 Azure CLI 登入](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)之後，才能繼續使用電腦的命令提示字元。

```azurecli
az login
```

在新的 [執行身分]**** 組態中使用 `azure-functions:deploy` Maven 目標，將您的程式碼部署到新的函式應用程式。

部署完成時，您會看到可用來存取 Azure 函式應用程式的 URL：

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

## <a name="next-steps"></a>後續步驟

- 檢閱 [Java 函式開發人員指南](functions-reference-java.md)以了解開發 Java 函式的詳細資訊。
- 使用 `azure-functions:add`Maven 目標，將具有不同觸發程序的其他函式新增至您的專案。
