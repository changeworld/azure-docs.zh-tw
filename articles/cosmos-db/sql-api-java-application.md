---
title: 教學課程：使用 Azure Cosmos DB 和 SQL API 來建置 Java Web 應用程式
description: 教學課程：本 Java Web 應用程式教學課程示範如何使用 Azure Cosmos DB 和 SQL API，來儲存和存取 Azure 網站上所託管的 Java 應用程式資料。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 05/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 74a4178cc6b9645f3cb22bf3d1c7c0ee942c3f5f
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92485285"
---
# <a name="tutorial-build-a-java-web-application-using-azure-cosmos-db-and-the-sql-api"></a>教學課程：使用 Azure Cosmos DB 和 SQL API 來建置 Java Web 應用程式

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](./create-sql-api-python.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

本 Java Web 應用程式教學課程示範如何使用 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服務，來儲存和存取 Azure App Service Web Apps 上所託管的 Java 應用程式資料。 在本文中，您將了解：

* 如何在 Eclipse 中建置基本的 JavaServer Pages (JSP) 應用程式。
* 如何透過 [Azure Cosmos DB Java SDK](https://github.com/Azure/azure-documentdb-java)，使用 Azure Cosmos DB 服務。

本 Java 應用程式教學課程會示範如何建立以 Web 為基礎的工作管理應用程式，方便您建立、抓取以及將工作標示為完成，如下圖所示。 在 Azure Cosmos DB 中，[待辦事項] 清單中的每項工作都會以 JSON 文件的形式儲存。

:::image type="content" source="./media/sql-api-java-application/image1.png" alt-text="我的待辦事項清單 Java 應用程式":::

> [!TIP]
> 本應用程式開發教學課程假設您先前已有使用 Java 的經驗。 如果您不熟悉 Java 或[必備工具](#Prerequisites)，我們建議您從 GitHub 下載完整的[待辦事項](https://github.com/Azure-Samples/documentdb-java-todo-app)專案，並使用[本文結尾的指示](#GetProject)開始建置。 建置完成後，您可以檢閱文件，以加深對專案內容中程式碼的了解。  
>

## <a name="prerequisites-for-this-java-web-application-tutorial"></a><a id="Prerequisites"></a>針對此 Java Web 應用程式教學課程的必要條件

開始進行本應用程式開發教學課程之前，您必須具備下列條件：

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Java Development Kit (JDK) 7+](/java/azure/jdk/?view=azure-java-stable&preserve-view=true)。
* [Eclipse IDE for Java EE Developers。](https://www.eclipse.org/downloads/packages/release/luna/sr1/eclipse-ide-java-ee-developers)
* [已啟用某個 Java Runtime Environment (例如 Tomcat 或 Jetty) 的 Azure 網站。](../app-service/quickstart-java.md)

如果您是第一次安裝這些工具，coreservlets.com 提供了安裝程序的逐步解說，請參閱其[教學課程：安裝 TomCat7 並與 Eclipse 搭配使用](http://www.coreservlets.com/Apache-Tomcat-Tutorial/tomcat-7-with-eclipse.html)一文中的 [快速入門] 一節。

## <a name="create-an-azure-cosmos-db-account"></a><a id="CreateDB"></a>建立 Azure Cosmos DB 帳戶

我們將從建立 Azure Cosmos DB 帳戶開始著手。 如果您已經擁有帳戶，或如果您正在使用 Azure Cosmos DB 模擬器來進行本教學課程，可以跳到[步驟 2：建立 Java JSP 應用程式](#CreateJSP)。

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

## <a name="create-the-java-jsp-application"></a><a id="CreateJSP"></a>建立 Java JSP 應用程式

建立 JSP 應用程式：

1. 首先，我們將從建立 Java 專案開始。 啟動 Eclipse，依序按一下 [檔案]、[新增] 和 [動態 Web 專案]。 如果您在可用專案中沒有看到 [動態 Web 專案]，請執行下列動作：依序按一下 [檔案]、[新增]、[專案]，展開 [Web]，按一下 [動態 Web 專案]，然後按 [下一步]。
   
    :::image type="content" source="./media/sql-api-java-application/image10.png" alt-text="我的待辦事項清單 Java 應用程式":::

1. 在 [專案名稱] 方塊中輸入專案名稱，然後在 [目標執行階段] 下拉式選單中，選擇性地選取值 (例如 Apache Tomcat v7.0)，然後按一下 [完成]。 選取目標執行階段可讓您透過 Eclipse 在本機執行專案。

1. 在 Eclipse 的 [專案總管] 檢視中，展開您的專案。 在 [WebContent] 上按一下滑鼠右鍵、按一下 [新增]，然後按一下 [JSP 檔案]。

1. 在 [新增 JSP 檔案] 對話方塊中，將檔案命名為 **index.jsp** 。 將上層資料夾保持為 **WebContent** ，如下圖所示，然後按 [下一步]。
   
    :::image type="content" source="./media/sql-api-java-application/image11.png" alt-text="我的待辦事項清單 Java 應用程式":::

1. 在 [選取 JSP 範本] 對話方塊中，基於本教學課程的目的，選取 [新增 JSP 檔案 (html)]，然後按一下 [完成]。

1. 在 Eclipse 中開啟 index.jsp 檔案時，請加入文字以顯示 **Hello World!** 。 (在現有的 `<body>` 元素內加入)。 已更新的 `<body>` 內容看起來應該與下列程式碼類似：

   ```html
   <body>
     <% out.println("Hello World!"); %>
   </body>
   ```

1. 儲存 index.jsp 檔案。

1. 如果您在步驟 2 中已設定目標執行階段，就可以依序按一下 [專案] 和 [執行]，即可在本機執行您的 JSP 應用程式：

   :::image type="content" source="./media/sql-api-java-application/image12.png" alt-text="我的待辦事項清單 Java 應用程式":::

## <a name="install-the-sql-java-sdk"></a><a id="InstallSDK"></a>安裝 SQL Java SDK

導入 SQL Java SDK 及其相依性的最簡單方式就是透過 [Apache Maven](https://maven.apache.org/)。 若要這樣做，您必須使用下列步驟將專案轉換成 Maven 專案：

1. 在 [專案總管] 中，以滑鼠右鍵按一下您的專案，按一下 [設定]，然後按一下 [轉換成 Maven 專案]。

1. 在 [建立新的 POM] 視窗中，接受預設值，然後按一下 [完成]。

1. 在 [專案總管] 中，開啟 pom.xml 檔案。

1. 在 [相依性] 窗格的 [相依性] 索引標籤中，按一下 [新增]。

1. 在 [選取相依性]  視窗中，執行下列動作：
   
   * 在 [群組識別碼] 方塊中，輸入 `com.azure`。
   * 在 [成品識別碼] 方塊中，輸入 `azure-cosmos`。
   * 在 [版本] 方塊中，輸入 `4.0.1-beta.1`。
  
   或者，您可以將群組識別碼和成品識別碼的相依性 XML 直接新增至 pom.xml 檔案：

   ```xml
   <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-cosmos</artifactId>
      <version>4.0.1-beta.1</version>
   </dependency>
   ```

1. 按一下 [確定]，Maven 將會安裝 SQL Java SDK 或儲存 pom.xml 檔案。

## <a name="use-the-azure-cosmos-db-service-in-your-java-application"></a><a id="UseService"></a>在 Java 應用程式中使用 Azure Cosmos DB 服務

現在讓我們將模型、檢視和控制站新增至您的 Web 應用程式。

### <a name="add-a-model"></a>新增模型

首先，讓我們在新的 TodoItem.java 檔案中定義模型。 `TodoItem` 類別會定義項目的結構描述以及 getter 和 setter 方法：

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/model/TodoItem.java":::

### <a name="add-the-data-access-objectdao-classes"></a>新增資料存取物件 (DAO) 類別

建立資料存取物件 (DAO)，將待辦事項提取保存至 Azure Cosmos DB。 為了將 ToDo 項目儲存至集合，用戶端必須知道要保存至哪個資料庫和集合 (會被自我連結參照)。 一般而言，最好是儘可能快取資料庫和集合，以避免對資料庫進行額外的來回存取。

1. 若要叫用 Azure Cosmos DB 服務，您必須將新的 `cosmosClient`物件具現化。 一般而言，最好是重複使用 `cosmosClient` 物件，而不要針對每個後續要求建構新的用戶端。 您可以在 `cosmosClientFactory` 類別內定義用戶端，以便重複使用。 更新您在[步驟 1](#CreateDB)儲存的 HOST 和 MASTER_KEY 值。 以您的 URI 取代 HOST 變數，並以您的 PRIMARY KEY 取代 MASTER_KEY。 使用下列程式碼，在 *CosmosClientFactory.java* 檔案中建立 `CosmosClientFactory` 類別：

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/dao/CosmosClientFactory.java":::

1. 建立新的 TodoDao.java 檔案並新增 `TodoDao` 類別，以建立、更新、讀取和刪除待辦項目：

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/dao/TodoDao.java":::

1. 建立新的 MockDao.java 檔案並新增 `MockDao` 類別，此類別會實作 `TodoDao` 類別，以對項目執行 CRUD 作業：

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/dao/MockDao.java":::

1. 建立新的 DocDbDao.java 檔案，然後新增 `DocDbDao` 類別。 此類別會定義程式碼以將 TodoItem 保存到容器中、擷取您的資料庫和集合 (如果存在)，或建立新的 (如果不存在的話)。 此範例會使用 [Gson](https://code.google.com/p/google-gson/) 將 TodoItem Plain Old Java Objects (POJO) 序列化及還原序列化成 JSON 文件。 為了將 ToDo 項目儲存至集合，用戶端必須知道要保存至哪個資料庫和集合 (會被自我連結參照)。 這個類別也會定義協助程式函式，依另一個屬性 (例如"ID") 來擷取文件 (而不是自我連結)。 您可以使用協助程式方法來依 ID 擷取 TodoItem JSON 文件，然後再將其還原序列化成 POJO。

   您也可使用 `cosmosClient` 用戶端物件，利用 SQL 查詢取得 TodoItem 的集合或清單。 最後，您可定義 delete 方法以刪除清單中的 TodoItem。 下列程式碼示範 `DocDbDao` 類別的內容：

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/dao/DocDbDao.java":::

1. 接下來，建立新的 TodoDaoFactory.java 檔案，然後新增 `TodoDaoFactory` 類別，以建立新的 DocDbDao 物件：

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/dao/TodoDaoFactory.java":::

### <a name="add-a-controller"></a>新增控制器

將 TodoItemController 控制器新增至您的應用程式。 在此專案中，您會使用 [Project Lombok](https://projectlombok.org/) 來產生建構函式、getter、setter 及產生器。 或者，您也可以手動撰寫此程式碼，或讓 IDE 加以產生：

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/controller/TodoItemController.java":::

### <a name="create-a-servlet"></a>建立 Serlet

接著，建立一個可將 HTTP 要求遞送至控制器的 Servlet。 建立 ApiServlet.java 檔案，並在其下定義下列程式碼：

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/ApiServlet.java":::

## <a name="wire-the-rest-of-the-of-java-app-together"></a><a id="Wire"></a>將 Java 應用程式的其餘部分連在一起

既然我們已經完成主要的部分，剩下的就是建置一個快速的使用者介面，然後將其串接到您的 DAO。

1. 您需要一個可對使用者顯示的 Web 使用者介面。 讓我們使用下列程式碼，重新撰寫稍早建立的 index.jsp：

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/WebContent/index.jsp":::

1. 最後，撰寫一些用戶端 JavaScript，以將 Web 使用者介面與 Servlet 繫結在一起：

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/WebContent/assets/todo.js":::

1. 現在只剩下測試應用程式。 在本機執行應用程式，並填入項目名稱和類別，然後按一下 [ **新增工作** ] 來新增一些待辦事項。 在項目出現後，您可以切換勾選核取方塊，然後按一下 [更新工作]，來更新其完成狀態。

## <a name="deploy-your-java-application-to-azure-web-sites"></a><a id="Deploy"></a>將 Java 應用程式部署至 Azure 網站

Azure 網站讓部署 Java 應用程式變得相當簡單，您只需將應用程式匯出成 WAR 檔案，然後透過原始檔控制 (例如 Git) 或 FTP 上傳它即可。

1. 若要將應用程式匯出成 WAR 檔案，請以滑鼠右鍵按一下您在 **專案總管** 中的專案，按一下 [匯出]，然後按一下 [WAR 檔案]。

1. 在 [WAR 匯出]  視窗中，執行下列動作：
   
   * 在 [Web 專案] 方塊中，輸入 azure-documentdb-java-sample。
   * 在 [目的地] 方塊中，選擇用來儲存 WAR 檔案的目的地。
   * 按一下 [完成] 。

1. 現在您手上已經有了 WAR 檔案，您只需將它上傳至您 Azure 網站的 **webapps** 目錄即可。 如需上傳檔案的相關指示，請參閱[將 Java 應用程式新增至 Azure App Service Web Apps](../app-service/quickstart-java.md)。 將 WAR 檔案上傳至 webapps 目錄之後，執行階段環境便會偵測到您已新增該檔案並自動將其載入。

1. 若要檢視您已完成的產品，請瀏覽至 `http://YOUR\_SITE\_NAME.azurewebsites.net/azure-java-sample/` 並開始新增您的工作！

## <a name="get-the-project-from-github"></a><a id="GetProject"></a>從 GitHub 取得的專案

本教學課程中的所有範例都包含在 GitHub 上的 [待辦事項](https://github.com/Azure-Samples/documentdb-java-todo-app) 專案中。 若要將 todo 專案匯入 Eclipse，請確認您擁有 [必要條件](#Prerequisites) 區段中所列出的軟體和資源，然後執行下列動作：

1. 安裝 [專案 Lombok](https://projectlombok.org/)。 Lombok 可用來在專案中產生建構函式、getter、setter。 下載 lombok.jar 檔案之後，請連按兩下進行安裝，或從命令列進行安裝。

1. 如果 Eclipse 為開啟狀態，請將它關閉並重新啟動以載入 Lombok。

1. 在 Eclipse 的 [檔案] 功能表上，按一下 [匯入]。

1. 在 [匯入] 視窗中，依序按一下 [Git]、[使用 Git 的專案] 和 [下一步]。

1. 在 [選取儲存機制來源] 畫面上，按一下 [複製 URI]。

1. 在 [來源 Git 存放庫] 畫面的 [URI] 方塊中，輸入 https://github.com/Azure-Samples/documentdb-java-todo-app.git ，然後按一下 [下一步]。

1. 在 [分支選取] 畫面上，確定已選取 [主要]，然後按 [下一步]。

1. 在 [本機目的地] 畫面上，按一下 [瀏覽] 以選取可以複製儲存機制的資料夾，然後按 [下一步]。

1. 在 [選取要用於匯入專案的精靈] 畫面上，確定已選取 [匯入現有的專案]，然後按 [下一步]。

1. 在 [匯入專案] 畫面上，取消選取 **DocumentDB** 專案，然後按一下 [完成]。 DocumentDB 專案包含 Azure Cosmos DB Java SDK，我們將會改成新增為相依性。

1. 在 [專案總管] 中，瀏覽至 azure-documentdb-java-sample\src\com.microsoft.azure.documentdb.sample.dao\DocumentClientFactory.java，並將 [主機] 和 [MASTER_KEY] 值取代為您 Azure Cosmos DB 帳戶的 [URI] 和 [主要金鑰]，然後儲存檔案。 如需詳細資訊，請參閱[步驟 1。建立 Azure Cosmos 資料庫帳戶](#CreateDB)。

1. 在 [專案總管] 中，以滑鼠右鍵按一下 **azure-documentdb-java-sample** ，按一下 [組建路徑]，然後按一下 [設定組建路徑]。

1. 在 [Java 組建路徑] 畫面的右側窗格中，選取 [程式庫] 索引標籤，然後按一下 [新增外部 JAR]。 瀏覽至 lombok.jar 檔案的位置，按一下 [開啟]，然後按一下 [確定]。

1. 使用步驟 12 重新開啟 [屬性] 視窗，然後在左側窗格中按一下 [目標執行階段]。

1. 在 [目標執行階段] 畫面上，按一下 [新增]，選取 [Apache Tomcat v7.0]，然後按一下 [確定]。

1. 使用步驟 12 重新開啟 [屬性] 視窗，然後在左側窗格中按一下 [專案 Facet]。

1. 在 [專案 Facet] 畫面上，選取 [動態 Web 模組] 和 [Java]，然後按一下 [確定]。

1. 在螢幕底部的 [伺服器] 索引標籤上，以滑鼠右鍵按一下 [在 localhost 的 Tomcat v7.0 伺服器]，然後按一下 [新增和移除]。

1. 在 [新增和移除] 視窗中，將 [azure-documentdb-java-sample] 移至 [已設定] 方塊，然後按一下 [完成]。

1. 在 [伺服器] 索引標籤上，以滑鼠右鍵按一下 [Tomcat v7.0 Server at localhost] \(在 localhost 的 Tomcat v7.0 伺服器)，然後按一下 [重新啟動]。

1. 在瀏覽器中，瀏覽至 `http://localhost:8080/azure-documentdb-java-sample/`，並開始新增到工作清單。 請注意，如果您之前變更預設的連接埠值，請將 8080 變更為您所選取的值。

1. 若要將您的專案部署至 Azure 網站，請參閱[步驟 6：將應用程式部署至 Azure 網站](#Deploy)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Azure Cosmos DB 來建置 Node.js 應用程式](sql-api-nodejs-application.md)