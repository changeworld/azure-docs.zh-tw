---
title: 使用 Azure Cosmos DB 的 ASP.NET Core MVC Web 應用程式教學課程
description: 使用 Azure Cosmos DB 建立 MVC Web 應用程式的 ASP.NET Core MVC 教學課程。 您將在託管於 Azure App Service 的待辦事項應用程式儲存 JSON 和存取資料 - ASP NET Core MVC 教學課程逐步解說。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: sngun
ms.openlocfilehash: 1f2051addfa1266b754d230c3804834c63f89002
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274082"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>教學課程：使用 .NET SDK，透過 Azure Cosmos DB 開發 ASP NET Core MVC Web 應用程式

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)

本教學課程說明如何使用 Azure Cosmos DB，以儲存和存取來自 ASP.NET MVC 應用程式 (裝載在 Azure 上) 的資料。 在本教學課程中，您會使用 .NET SDK V3。 下圖顯示您會藉由使用本文範例來建置的網頁：

![本教學課程所建立待辦事項清單 MVC Web 應用程式的螢幕擷取畫面 - ASP NET Core MVC 教學課程逐步解說](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

如果您沒有時間完成本教學課程，則可以從 [GitHub][GitHub] 下載完整的範例專案。

本教學課程涵蓋下列項目：

> [!div class="checklist"]
>
> * 建立 Azure Cosmos 帳戶
> * 建立 ASP.NET Core MVC 應用程式
> * 將應用程式連線到 Azure Cosmos DB
> * 在資料上執行建立、讀取、更新和刪除 (CRUD) 作業

> [!TIP]
> 本教學課程假設您先前有過使用 ASP.NET Core MVC 和 Azure App Service 的經驗。 如果您不熟悉 ASP.NET Core 或[必備工具](#prerequisites)，建議您從 [GitHub][GitHub] 下載完整的範例專案、新增必要的 NuGet 套件，並加以執行。 專案建置完成後，您可以檢閱本文，以加深對專案內容中程式碼的了解。

## <a name="prerequisites"></a>必要條件

依照本文的指示進行之前，請確定您具備下列資源：

* 使用中的 Azure 帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Visual Studio 2019。 [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

本文中的所有螢幕擷取畫面都是來自 Microsoft Visual Studio Community 2019。 如果您使用不同的版本，您的畫面和選項可能不會完全相符。 如果您符合必要條件，解決方案應可正常執行。

## <a name="create-an-azure-cosmos-account"></a>步驟 1：建立 Azure Cosmos 帳戶

我們將從建立 Azure Cosmos 帳戶開始著手。 如果您已經擁有 Azure Cosmos DB SQL API 帳戶，或如果您正在使用 Azure Cosmos DB 模擬器，請跳到[步驟 2：建立新的 ASP.NET MVC 應用程式](#create-a-new-mvc-application)。

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

在下一節中，您會建立新的 ASP.NET Core MVC 應用程式。

## <a name="create-a-new-mvc-application"></a>步驟 2：建立新的 ASP.NET Core MVC 應用程式

1. 開啟 Visual Studio，然後選取 [建立新專案]  。

1. 在 [建立新專案]  中，針對 C# 尋找並選取 [ASP.NET Core Web 應用程式]  。 選取 [下一步]  以繼續操作。

   ![建立新的 ASP.NET Core Web 應用程式專案](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

1. 在 [設定新的專案]  中，將專案命名為 *todo*，然後選取 [建立]  。

1. 在 [建立新的 ASP.NET Core Web 應用程式]  中，選擇 [Web 應用程式 (Model-View-Controller)]  。 選取 [建立]  繼續作業。

   Visual Studio 會建立空白的 MVC 應用程式。

1. 選取 [偵錯]   > [開始偵錯]  或 F5 以在本機執行 ASP.NET 應用程式。

## <a name="add-nuget-packages"></a>步驟 3：對專案新增 Azure Cosmos DB NuGet 套件

現在我們已經擁有此解決方案所需的大部分 ASP.NET Core MVC 架構程式碼，接下來讓我們新增所需的 NuGet 套件以便連線到 Azure Cosmos DB。

1. 在**方案總管**中，對專案按一下滑鼠右鍵，然後選取 [管理 NuGet 套件]  。

1. 在 [NuGet 套件管理員]  中，搜尋並選取 [Microsoft.Azure.Cosmos]  。 選取 [安裝]  。

   ![安裝 NuGet 套件](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-nuget.png)

   Visual Studio 會下載和安裝 Azure Cosmos DB 套件及其相依性。

   您也可以使用 [套件管理員主控台]  來安裝 NuGet 套件。 若要這麼做，請選取 [工具]   > [NuGet 套件管理員]   > [套件管理員主控台]  。 在出現提示時輸入下列命令：

   ```ps
   Install-Package Microsoft.Azure.Cosmos
   ```
  
## <a name="set-up-the-mvc-application"></a>步驟 4︰設定 ASP.NET Core MVC 應用程式

現在我們可以開始將模型、檢視和控制站新增至此 MVC 應用程式。

### <a name="add-a-model"></a>新增模型

1. 在 [方案總管]  中，以滑鼠右鍵按一下 [Models]  資料夾，選取 [新增]   > [類別]  。

1. 在 [新增項目]  中，將新的類別命名為 *Item.cs*，然後選取 [新增]  。

1. 以下列程式碼取代 *Item.cs* 類別的內容：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs":::

Azure Cosmos DB 使用 JSON 來移動和儲存資料。 您可以使用 `JsonProperty` 屬性來控制 JSON 序列化和還原序列化物件的方式。 `Item` 類別會示範 `JsonProperty` 屬性。 此程式碼會控制進入 JSON 的屬性名稱格式。 它也會將 .NET 屬性重新命名為 `Completed`。

### <a name="add-views"></a>新增檢視

接下來，讓我們建立下列三個檢視。

* 新增清單項目檢視
* 新增項目檢視
* 新增編輯項目檢視

#### <a name="AddItemIndexView"></a>新增清單項目檢視

1. 在 [方案總管]  中，以滑鼠右鍵按一下 [Views]  資料夾，然後選取 [新增]   > [新增資料夾]  。 將資料夾命名為 *Item*。

1. 以滑鼠右鍵按一下空的 [Item]  資料夾，然後選取 [新增]   > [檢視]  。

1. 在 [新增 MVC 檢視]  中提供下列值：

   * 在 [檢視名稱]  中，輸入 [索引]  。
   * 在 [範本]  中選取 [清單]  。
   * 在 [模型類別]  中，選取 [項目 (todo.Models)]  。
   * 選取 [使用版面配置頁面]  ，然後輸入 *~/Views/Shared/_Layout.cshtml*。

   ![顯示 [新增 MVC 檢視] 對話方塊的螢幕擷取畫面](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-mvc-view.png)

1. 新增這些值之後，請選取 [新增]  ，並讓 Visual Studio 建立新的範本檢視。

完成後，Visual Studio 會開啟它所建立的 *cshtml*檔案。 您可以在 Visual Studio 中關閉該檔案。 我們稍後會再回來。

#### <a name="AddNewIndexView"></a>新增新項目檢視

與您建立檢視以列出項目的方法類似，請使用下列步驟建立新的檢視，來建立項目：

1. 在 [方案總管]  中，再次以滑鼠右鍵按一下 [Item]  資料夾，選取 [新增]   > [檢視]  。

1. 在 [新增 MVC 檢視]  中，進行下列變更：

   * 在 [檢視名稱]  中，輸入 [建立]  。
   * 在 [範本]  中選取 [建立]  。
   * 在 [模型類別]  中，選取 [項目 (todo.Models)]  。
   * 選取 [使用版面配置頁面]  ，然後輸入 *~/Views/Shared/_Layout.cshtml*。
   * 選取 [新增]  。

#### <a name="AddEditIndexView"></a>新增編輯項目檢視

最後，請新增檢視以透過下列步驟編輯項目：

1. 在 [方案總管]  中，再次以滑鼠右鍵按一下 [Item]  資料夾，選取 [新增]   > [檢視]  。

1. 在 [新增 MVC 檢視]  中，進行下列變更：

   * 在 [檢視名稱]  方塊中，輸入*編輯*。
   * 在 [範本]  方塊中，選取 [編輯]  。
   * 在 [模型類別]  方塊中，選取 [項目 (todo.Models)]  。
   * 選取 [使用版面配置頁面]  ，然後輸入 *~/Views/Shared/_Layout.cshtml*。
   * 選取 [新增]  。

完成這些步驟後，請將 Visual Studio 中的所有 cshtml  文件關閉，您稍後會回頭使用這些檢視。

### <a name="initialize-services"></a>宣告並初始化服務

首先，我們會新增一個類別，其中包含連線至及使用 Azure Cosmos DB 的邏輯。 在本教學課程中，我們會將此邏輯封裝到名為 `CosmosDBService` 的類別和名為 `ICosmosDBService` 的介面中。 此服務會執行 CRUD 作業。 也會讀取摘要作業，例如列出未完成的項目，以及建立、編輯和刪除項目。

1. 在 [方案總管]  中，以滑鼠右鍵按一下專案，然後選取 [新增]   > [新增資料夾]  。 將資料夾命名為 *Services*。

1. 以滑鼠右鍵按一下 [Services]  資料夾，選取 [新增]   > [類別]  。 將新的類別命名為 *CosmosDBService*，然後選取 [新增]  。

1. 以下列程式碼取代 *CosmosDBService.cs* 的內容：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs":::

1. 以滑鼠右鍵按一下 [Services]  資料夾，選取 [新增]   > [類別]  。 將新的類別命名為 *ICosmosDBService*，然後選取 [新增]  。

1. 將下列程式碼新增至 *ICosmosDBService* 類別：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs":::

1. 開啟解決方案中的 *Startup.cs* 檔案，並將 `ConfigureServices` 方法取代為：

    :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs" id="ConfigureServices":::

    此步驟中的程式碼會根據組態將用戶端初始化為 Singleton 執行個體，以透過 [ASP.NET Core 中的相依性插入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)來插入。

1. 在相同的檔案中，新增以下方法 **InitializeCosmosClientInstanceAsync**，以讀取組態並初始化用戶端。

   [!code-csharp[](~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs?name=InitializeCosmosClientInstanceAsync)]

1. 在專案的 *appsettings.json* 檔案中定義設定，如下列程式碼片段所示：

   :::code language="json" source="~/samples-cosmosdb-dotnet-core-web-app/src/appsettings.json":::

### <a name="add-a-controller"></a>新增控制器

1. 在 [方案總管]  中，以滑鼠右鍵按一下 [Controllers]  資料夾，選取 [新增]   > [控制器]  。

1. 在 [新增 Scaffold]  中，選取 [MVC 控制器 - 空的]  ，然後選取 [新增]  。

   ![在 [新增 Scaffold] 中選取 [MVC 控制器 - 空的]](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. 將新的控制器命名為 *ItemController*。

1. 以下列程式碼取代 *ItemController.cs* 的內容。

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs":::

此處所使用的 **ValidateAntiForgeryToken** 屬性可協助應用程式防止跨網站偽造要求攻擊。 您的檢視也應該使用此防偽權杖。 如需詳細資訊和範例，請參閱[在 ASP.NET MVC 應用程式中避免跨網站偽造要求 (CSRF) 攻擊][Preventing Cross-Site Request Forgery]。 [GitHub][GitHub] 上提供的原始程式碼已有完整實作。

我們也會在方法參數上使用 **Bind** 屬性，以協助防範 over-posting 攻擊。 如需詳細資訊，請參閱[教學課程：使用 ASP.NET MVC 中的 Entity Framework 來實作 CRUD 功能][Basic CRUD Operations in ASP.NET MVC]。

## <a name="run-the-application"></a>步驟 5：在本機執行應用程式

若要在本機電腦測試應用程式，請使用下列步驟：

1. 在 Visual Studio 中按 F5，即可在偵錯模式下建置應用程式。 這樣應該可以建置應用程式，並啟動含有先前所看過之空白方格頁面的瀏覽器：

   ![本教學課程所建立待辦事項清單 Web 應用程式的螢幕擷取畫面](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
   
   如果應用程式改為在開啟後進入首頁，請將 `/Item` 附加至 URL。

1. 選取 [新建]  連結，並在 [名稱]  和 [描述]  欄位中新增值。 讓 [已完成]  核取方塊保持未選取狀態。 如果您選取它，則應用程式會新增已完成狀態的項目。 此項目不再出現於初始清單。

1. 選取 [建立]  。 應用程式會讓您回到 [索引]  檢視，且您的項目會出現在清單中。 您可以再將一些項目新增至 [待辦事項]  清單。

    ![[索引] 檢視的螢幕擷取畫面](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
  
1. 選取清單上 [項目]  旁邊的 [編輯]  。 應用程式會開啟 [編輯]  檢視，您可在此更新物件的任何屬性，包括 [已完成]  旗標。 如果您選取 [已完成]  並選取 [儲存]  ，應用程式會在清單中將 [項目]  顯示為 [已完成]。

   ![[索引] 檢視的螢幕擷取畫面，其中已勾選 [已完成] 方塊](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)

1. 使用 [Cosmos 總管](https://cosmos.azure.com) 或 Azure Cosmos DB 模擬器的資料總管，驗證 Azure Cosmos DB 服務中的資料狀態。

1. 完成測試應用程式後，選取 Ctrl + F5 停止偵錯應用程式。 您現在可以開始進行部署。

## <a name="deploy-the-application-to-azure"></a>步驟 6：部署應用程式

您已經擁有可在 Azure Cosmos DB 正常運作的完整應用程式，我們現在要將此 Web 應用程式部署至 Azure App Service。  

1. 若要發佈此應用程式，請以滑鼠右鍵按一下 [方案總管]  中的專案，然後選取 [發佈]  。

1. 在 [挑選發佈目標]  中，選取 [App Service]  。

1. 若要使用現有的 App Service 設定檔，請選擇 [選取現有]  ，然後選取 [發佈]  。

1. 在 [App Service]  中，選取 [訂用帳戶]  。 使用 [檢視]  篩選器來依資源群組或資源類型排序。

1. 尋找您的設定檔，然後選取 [確定]  。 接著搜尋所需的 Azure App Service，然後選取 [確定]  。

   ![Visual Studio 中的 [App Service] 對話方塊](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service-2019.png)

另一個選項是建立新的設定檔：

1. 如先前的程序，在 [方案總管]  中以滑鼠右鍵按一下專案，再選取 [發佈]  。
  
1. 在 [挑選發佈目標]  中，選取 [App Service]  。

1. 在 [挑選發佈目標]  中，選取 [新建]  ，然後選取 [發佈]  。

1. 在 [App Service]  中，輸入您的 Web 應用程式名稱和適當的訂用帳戶、資源群組和主控方案，然後選取 [建立]  。

   ![Visual Studio 中的 [建立 App Service] 對話方塊](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service-2019.png)

幾秒後，Visual Studio 便會發佈 Web 應用程式並啟動瀏覽器，您可以在瀏覽器中看到您的專案已在 Azure 中執行！

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何建置 ASP.NET Core MVC Web 應用程式。 您的應用程式可以存取 Azure Cosmos DB 中儲存的資料。 您現在可以繼續使用下列資源：

* [Azure Cosmos DB 中的資料分割](./partitioning-overview.md)
* [開始使用 SQL 查詢](./how-to-sql-query.md)
* [如何使用實際範例在 Azure Cosmos DB 上建立資料的模型及加以分割](./how-to-model-partition-example.md)

[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://docs.microsoft.com/aspnet/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
