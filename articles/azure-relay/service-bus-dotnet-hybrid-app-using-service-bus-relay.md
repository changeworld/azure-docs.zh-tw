---
title: Azure Windows Communication Foundation (WCF) 轉送混合式內部部署/雲端應用程式 ( .NET) |Microsoft Docs
description: 了解如何使用 Azure 轉送將內部部署 WCF 服務公開至雲端中的 Web 應用程式
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: a9ac01beb28b1246122f437bcf1c7a7be7a1dfd9
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88079955"
---
# <a name="expose-an-on-premises-wcf-service-to-a-web-application-in-the-cloud-by-using-azure-relay"></a>使用 Azure 轉送將內部部署 WCF 服務公開至雲端中的 Web 應用程式

本文示範如何使用 Microsoft Azure 和 Visual Studio 建置混合式雲端應用程式。 您會在雲端中建立使用多個 Azure 資源的應用程式。 本教學課程可協助您瞭解：

* 如何建立 Web 服務或調整現有的 Web 服務，以供 Web 方案使用。
* 如何使用 Azure Windows Communication Foundation (WCF) 轉送服務，在 Azure 應用程式與其他位置託管的 web 服務之間共用資料。

您將在本教學課程中執行下列工作：

> [!div class="checklist"]
>
> * 安裝本教學課程的必要條件。
> * 檢閱案例。
> * 建立命名空間。
> * 建立內部部署伺服器。
> * 建立 ASP .NET 應用程式。
> * 在本機執行應用程式。
> * 將 web 應用程式部署至 Azure。
> * 在 Azure 上執行應用程式。

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要下列必要條件：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前[建立免費帳戶](https://azure.microsoft.com/free/)。
* [Visual Studio 2015 或更新版本](https://www.visualstudio.com)。 本教學課程中的範例使用 Visual Studio 2019。
* 適用於 .NET 的 Azure SDK。 從 [SDK 下載頁面](https://azure.microsoft.com/downloads/)進行安裝。

## <a name="how-azure-relay-helps-with-hybrid-solutions"></a>Azure 轉送如何透過混合式解決方案提供協助

商務解決方案通常是由自訂程式碼和現有功能的組合所組成。 自訂程式碼會 esposito 著手處理新的和獨特的商務需求。 已備妥的方案和系統會提供現有的功能。

眾多方案架構爭相開始使用雲端，以期能夠更輕鬆地處理擴充需求並降低操作成本。 在這種情況下，他們發現他們想要用來做為解決方案建立區塊的現有服務資產，是在公司防火牆內，而雲端解決方案則不容易接觸到。 許多內部服務並不是以可在公司網路邊緣輕鬆公開的方式來建立或託管。

[Azure 轉送](https://azure.microsoft.com/services/service-bus/)採用現有的 WCF web 服務，並讓那些服務安全地存取公司周邊以外的解決方案，而不需要對公司網路基礎結構進行侵入式變更。 此類轉送服務仍然裝載在其現有環境，但它們會將接聽連入工作階段和要求的工作委派給雲端裝載的轉送服務。 Azure 轉送也會使用[共用存取簽章 (SAS) ](../service-bus-messaging/service-bus-sas.md)驗證，保護這些服務免于未經授權的存取。

## <a name="review-the-scenario"></a>檢閱案例

在本教學課程中，您將建立 ASP.NET 網站，讓您可在產品庫存頁面上看到產品清單。

![案例][0]

此教學課程假設您有現有內部部署系統中的產品資訊，並使用 Azure 轉送來連接該系統。 在簡單主控台應用程式中執行的 web 服務會模擬這種情況。 它包含一組記憶體中的產品。 您可以在自己的電腦上執行此主控台應用程式，並將 web 角色部署至 Azure。 如此一來，您就會看到在 Azure 資料中心內執行的 web 角色如何呼叫您的電腦。 即使您的電腦幾乎會在至少一個防火牆後方，以及 (NAT) 層的網路位址轉譯後，還是會發生此呼叫。

## <a name="set-up-the-development-environment"></a>設定開發環境

開始開發 Azure 應用程式之前，請先下載工具並設定開發環境：

1. 從 SDK [下載頁面](https://azure.microsoft.com/downloads/)安裝 Azure SDK for .NET。
1. 在 [ **.net** ] 資料行中，選擇您要使用的[Visual Studio](https://www.visualstudio.com)版本。 本教學課程使用 Visual Studio 2019。
1. 當系統提示您執行或儲存安裝程式時，請選取 [**執行**]。
1. 在 [ **Web Platform Installer** ] 對話方塊中，選取 [**安裝**] 並繼續進行安裝。

安裝完成後，您就擁有開始開發應用程式所需的所有專案。 SDK 包含可讓您在 Visual Studio 輕易開發 Azure 應用程式的工具。

## <a name="create-a-namespace"></a>建立命名空間

第一個步驟是建立命名空間，並取得[ (SAS) 金鑰的共用存取](../service-bus-messaging/service-bus-sas.md)簽章。 命名空間會為每個透過轉送服務公開的應用程式提供應用程式界限。 建立服務命名空間時，系統會自動產生 SAS 金鑰。 服務命名空間與 SAS 金鑰的組合會提供一個認證，供 Azure 用來驗證對應用程式的存取權。

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-an-on-premises-server"></a>建立內部部署伺服器

首先，您將建置模擬的內部部署產品目錄系統。  此專案是 Visual Studio 主控台應用程式，會使用 [Azure 服務匯流排 NuGet 套件](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)來納入服務匯流排程式庫和組態設定。 <a name="create-the-project"></a>

1. 以系統管理員身分啟動 Microsoft Visual Studio。 若要這樣做，請以滑鼠右鍵按一下 Visual Studio 程式圖示，然後選取 [以系統管理員身分執行]。
1. 在 Visual Studio 中，選取 [建立新專案]。
1. 在 [**建立新專案**] 中，選取 c # 的**主控台應用程式 ( .NET Framework) ** ，然後選取 **[下一步]**。
1. 將專案命名為*ProductsServer* ，然後選取 [**建立**]。

   ![設定您的新專案][11]

1. 在**方案總管**中，以滑鼠右鍵按一下**ProductsServer**專案，然後選取 [**管理 NuGet 套件**]。
1. 選取 [瀏覽]，然後搜尋並選擇 **WindowsAzure.ServiceBus**。 選取 [安裝] 並接受使用條款。

   ![選取 NuGet 套件][13]

   現在會參考必要的用戶端組件。

1. 新增產品連絡人的新類別。 在**方案總管**中，以滑鼠右鍵按一下**ProductsServer**專案，然後選取 [**新增**  >  **類別**]。
1. 在 [**名稱**] 中，輸入名稱*ProductsContract.cs* ，然後選取 [**新增**]。

對您的解決方案進行下列程式碼變更：

1. 在 *ProductsContract.cs* 中，將命名空間定義取代為下列程式碼，以定義服務的連絡人。

    ```csharp
    namespace ProductsServer
    {
        using System.Collections.Generic;
        using System.Runtime.Serialization;
        using System.ServiceModel;

        // Define the data contract for the service
        [DataContract]
        // Declare the serializable properties.
        public class ProductData
        {
            [DataMember]
            public string Id { get; set; }
            [DataMember]
            public string Name { get; set; }
            [DataMember]
            public string Quantity { get; set; }
        }

        // Define the service contract.
        [ServiceContract]
        interface IProducts
        {
            [OperationContract]
            IList<ProductData> GetProducts();

        }

        interface IProductsChannel : IProducts, IClientChannel
        {
        }
    }
    ```

1. 在*Program.cs*中，將命名空間定義取代為下列程式碼，以新增設定檔服務和其主機。

    ```csharp
    namespace ProductsServer
    {
        using System;
        using System.Linq;
        using System.Collections.Generic;
        using System.ServiceModel;

        // Implement the IProducts interface.
        class ProductsService : IProducts
        {

            // Populate array of products for display on website
            ProductData[] products =
                new []
                    {
                        new ProductData{ Id = "1", Name = "Rock",
                                         Quantity = "1"},
                        new ProductData{ Id = "2", Name = "Paper",
                                         Quantity = "3"},
                        new ProductData{ Id = "3", Name = "Scissors",
                                         Quantity = "5"},
                        new ProductData{ Id = "4", Name = "Well",
                                         Quantity = "2500"},
                    };

            // Display a message in the service console application
            // when the list of products is retrieved.
            public IList<ProductData> GetProducts()
            {
                Console.WriteLine("GetProducts called.");
                return products;
            }

        }

        class Program
        {
            // Define the Main() function in the service application.
            static void Main(string[] args)
            {
                var sh = new ServiceHost(typeof(ProductsService));
                sh.Open();

                Console.WriteLine("Press ENTER to close");
                Console.ReadLine();

                sh.Close();
            }
        }
    }
    ```

1. 在 [方案總管] 中按兩下 **App.config**，以在 Visual Studio 編輯器中開啟該檔案。 在元素的底部 `<system.ServiceModel>` ，但仍在內 `<system.ServiceModel>` ，新增下列 XML 程式碼。 請務必以 `yourServiceNamespace` 您的命名空間名稱取代，並以 `yourKey` 您先前從入口網站中抓取的 SAS 金鑰取代：

    ```xml
    <system.serviceModel>
    ...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```

    > [!NOTE]
    > 造成的錯誤 `transportClientEndpointBehavior` 只是警告，不是此範例的封鎖問題。

1. 仍然在*App.config*的元素中 `<appSettings>` ，將連接字串值取代為您先前從入口網站取得的連接字串。

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

1. 選取 Ctrl + Shift + B，或選取 [**組建**] [組建  >  **方案**] 來建立應用程式，並確認您的工作到目前為止是否正確無誤。

## <a name="create-an-aspnet-application"></a>建立 ASP.NET 應用程式

在本節中，您將建置簡單的 ASP.NET 應用程式，來顯示從產品服務擷取的資料。

### <a name="create-the-project"></a>建立專案

1. 確定 Visual Studio 是以系統管理員身分執行。
1. 在 Visual Studio 中，選取 [建立新專案]。
1. 在 [**建立新專案**] 中，選取 [ **ASP.NET Web Application ( .NET Framework** c # 的) ，然後選取 **[下一步]**。
1. 將專案命名為*ProductsPortal* ，然後選取 [**建立**]。
1. 在 [**建立新的 ASP.NET Web 應用程式**] 中選擇 [ **MVC** ]，然後選取 [**驗證**] 底下的 [**變更**

   ![選取 ASP.NET Web 應用程式][16]

1. 在 [**變更驗證**] 中，選擇 [**無驗證**]，然後選取 **[確定]**。 在本教學課程中，您會部署不需要使用者登入的應用程式。

    ![指定驗證][18]

1. 回到 [**建立新的 ASP.NET Web 應用程式**]，選取 [**建立**] 以建立 MVC 應用程式。
1. 設定新 web 應用程式的 Azure 資源。 請遵循[發佈 web 應用程式](../app-service/quickstart-dotnet-framework.md#launch-the-publish-wizard)中的步驟。 然後，返回本教學課程並繼續進行下一個步驟。
1. 在**方案總管**中，以滑鼠右鍵按一下 [**模型**]，然後選取 [**新增**  >  **類別**]。
1. 將類別命名為*Product.cs*，然後選取 [**新增**]。

    ![建立產品型號][17]

### <a name="modify-the-web-application"></a>修改 Web 應用程式

1. 在 Visual Studio 的*Product.cs*檔案中，將現有的命名空間定義取代為下列程式碼：

   ```csharp
    // Declare properties for the products inventory.
    namespace ProductsWeb.Models
    {
       public class Product
       {
           public string Id { get; set; }
           public string Name { get; set; }
           public string Quantity { get; set; }
       }
    }
    ```

1. 在**方案總管**中，展開 [**控制器**]，然後按兩下 [ **HomeController.cs** ] 以在 Visual Studio 中開啟檔案。
1. 在 *HomeController.cs* 檔案中，以下列程式碼取代現有的命名空間定義：

    ```csharp
    namespace ProductsWeb.Controllers
    {
        using System.Collections.Generic;
        using System.Web.Mvc;
        using Models;

        public class HomeController : Controller
        {
            // Return a view of the products inventory.
            public ActionResult Index(string Identifier, string ProductName)
            {
                var products = new List<Product>
                    {new Product {Id = Identifier, Name = ProductName}};
                return View(products);
            }
         }
    }
    ```

1. 在**方案總管**中，展開 [ **Views**]  >  [**共用**]，然後按兩下 [ **_Layout** ]，在 [Visual Studio 編輯器] 中開啟檔案。
1. 將所有出現的變更 `My ASP.NET Application` 為*Northwind 商貿產品*。
1. 移除 `Home` 、 `About` 和 `Contact` 連結。 在下列範例中，刪除反白顯示的程式碼。

    ![刪除產生的清單項目][41]

1. 在 [**方案總管**] 中，展開 [ **Views**  >  **Home**]，然後按兩下 [ **Index. cshtml** ]，在 [Visual Studio 編輯器] 中開啟檔案。 將檔案的整個內容取代為下列程式碼：

   ```html
   @model IEnumerable<ProductsWeb.Models.Product>

   @{
            ViewBag.Title = "Index";
   }

   <h2>Prod Inventory</h2>

   <table>
             <tr>
                 <th>
                     @Html.DisplayNameFor(model => model.Name)
                 </th>
                 <th></th>
                 <th>
                     @Html.DisplayNameFor(model => model.Quantity)
                 </th>
             </tr>

   @foreach (var item in Model) {
             <tr>
                 <td>
                     @Html.DisplayFor(modelItem => item.Name)
                 </td>
                 <td>
                     @Html.DisplayFor(modelItem => item.Quantity)
                 </td>
             </tr>
   }

   </table>
   ```

1. 若要確認您的工作到目前為止是否正確無誤，您可以選取 Ctrl + Shift + B 來建立專案。

### <a name="run-the-app-locally"></a>在本機執行應用程式

執行應用程式以驗證它是否能正常運作。

1. 確定 **ProductsPortal** 為作用中專案。 以滑鼠右鍵按一下**方案總管**中的專案名稱，然後選取 [**設定為啟始專案**]。
1. 在 [Visual Studio 中，選取 F5。

您的應用程式應該就會出現在瀏覽器中並正在執行。

![Web 應用程式][21]

## <a name="put-the-pieces-together"></a>組合在一起

下一步是利用 ASP.NET 應用程式連接內部部署產品伺服器。

1. 如果尚未開啟，請在 [Visual Studio 中，開啟您在[建立 ASP.NET 應用程式](#create-an-aspnet-application)一節中建立的**ProductsPortal**專案。
1. 類似于[建立內部部署伺服器](#create-an-on-premises-server)一節中的步驟，請將 NuGet 套件新增至專案參考。 在**方案總管**中，以滑鼠右鍵按一下**ProductsPortal**專案，然後選取 [**管理 NuGet 套件**]。
1. 搜尋 *WindowsAzure.ServiceBus* 並選取 **WindowsAzure.ServiceBus** 項目。 然後完成安裝並關閉此對話方塊。
1. 在**方案總管**中，以滑鼠右鍵按一下**ProductsPortal**專案，然後選取 [**新增**] [  >  **現有專案**]。
1. 從 *ProductsServer* 主控台專案導覽至 **ProductsContract.cs** 檔案。 反白顯示*ProductsContract.cs*。 選取 [**新增**] 旁邊的向下箭號，然後選擇 [**新增為連結**]。

   ![新增為連結][24]

1. 現在會在 Visual Studio 編輯器中開啟 *HomeController.cs* 檔案，並將命名空間定義取代為下列程式碼。 請務必以 `yourServiceNamespace` 您的服務命名空間名稱取代，並以 `yourKey` 您的 SAS 金鑰取代。 這段程式碼可讓用戶端呼叫內部部署服務，並傳回呼叫的結果。

   ```csharp
   namespace ProductsWeb.Controllers
   {
       using System.Linq;
       using System.ServiceModel;
       using System.Web.Mvc;
       using Microsoft.ServiceBus;
       using Models;
       using ProductsServer;

       public class HomeController : Controller
       {
           // Declare the channel factory.
           static ChannelFactory<IProductsChannel> channelFactory;

           static HomeController()
           {
               // Create shared access signature token credentials for authentication.
               channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                   "sb://yourServiceNamespace.servicebus.windows.net/products");
               channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                   TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                       "RootManageSharedAccessKey", "yourKey") });
           }

           public ActionResult Index()
           {
               using (IProductsChannel channel = channelFactory.CreateChannel())
               {
                   // Return a view of the products inventory.
                   return this.View(from prod in channel.GetProducts()
                                    select
                                        new Product { Id = prod.Id, Name = prod.Name,
                                            Quantity = prod.Quantity });
               }
           }
       }
   }
   ```

1. 在**方案總管**中，以滑鼠右鍵按一下 [ **ProductsPortal** ] 解決方案。 請務必以滑鼠右鍵按一下方案，而不是專案。 選取 [**加入**  >  **現有專案**]。
1. 導覽至 **ProductsServer** 專案，然後按兩下 *ProductsServer.csproj* 方案檔來新增它。
1. **ProductsServer**必須正在執行中，才能在**ProductsPortal**上顯示資料。 在**方案總管**中，以滑鼠右鍵按一下**ProductsPortal**方案，然後選取 [**屬性**] 以顯示**屬性頁**。
1. 選取 [**通用屬性**]  >  [**啟始專案**]，然後選擇 [**多個啟始專案**]。 請確定**ProductsServer**和**ProductsPortal**出現在該順序中，而且兩者的**動作**都是**Start**。

      ![多個啟始專案][25]

1. 選取左側的 [**通用屬性**  >  **] [專案**相依性]。
1. 針對 [**專案**]，選擇 [ **ProductsPortal**]。 確定已選取 [ProductsServer]****。

    ![專案相依性][26]

1. 針對 [**專案**]，選擇 [ **ProductsServer**]。 確定未選取 [ **ProductsPortal** ]，然後選取 **[確定]** 以儲存變更。

## <a name="run-the-project-locally"></a>在本機執行專案

若要在本機測試應用程式，請在 Visual Studio 選取 F5。 內部部署伺服器**ProductsServer**應該會先啟動，然後**ProductsPortal**應用程式應該會在瀏覽器視窗中啟動。 此時，您將看到從產品服務內部部署系統擷取的產品庫存清單資料。

![Web 應用程式][10]

在 [ **ProductsPortal** ]**頁面上選取**[重新整理]。 每次重新整理頁面時，您會看到伺服器應用程式在呼叫來自 **ProductsServer** 的 `GetProducts()` 時顯示一則訊息。

繼續進行下一節之前，請先關閉這兩個應用程式。

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>將 ProductsPortal 專案部署至 Azure Web 應用程式

下一步是重新發佈 Azure Web 應用程式**ProductsPortal**前端：

1. 在**方案總管**中，以滑鼠右鍵按一下**ProductsPortal**專案，然後選取 [**發佈**]。 在 [發佈] 頁面中，選取 [發佈]。

   > [!NOTE]
   > 在部署後自動啟動 **ProductsPortal** Web 專案時，您可在瀏覽器視窗中看到錯誤訊息。 這是預期的，且因為 **ProductsServer** 應用程式尚未執行而出現。
   >

1. 複製已部署 web 應用程式的 URL。 稍後您將需要此 URL。 您也可以從 Visual Studio 的 [ **Azure App Service 活動**] 視窗中取得此 URL：

   ![已部署應用程式的 URL][9]

1. 關閉瀏覽器視窗以停止執行中的應用程式。

<a name="set-productsportal-as-web-app"></a>在雲端中執行應用程式之前，您必須確定**ProductsPortal**是以 web 應用程式的形式從 Visual Studio 中啟動。

1. 在 Visual Studio 中，以滑鼠右鍵按一下**ProductsPortal**專案，然後選取 [**屬性**]。
1. 選取 [Web]。 在 [**起始動作**] 下，選擇 [**開始 URL**]。 輸入您先前部署之 web 應用程式的 URL，在此範例中為 `https://productsportal20190906122808.azurewebsites.net/` 。

    ![起始 URL][27]

1. 選取 **[** 檔案] [  >  **全部儲存**]。
1. 選取 [**組建**  >  **重建解決方案**]。

## <a name="run-the-application"></a>執行應用程式

選取 F5 以建立並執行應用程式。 內部部署伺服器（也就是**ProductsServer**主控台應用程式）應該會先啟動，然後**ProductsPortal**應用程式應該會在瀏覽器視窗中啟動，如下所示：

   ![在 Azure 上執行 Web 應用程式][1]

產品清查會列出從產品服務內部部署系統中取出的資料，並在 web 應用程式中顯示該資料。 檢查 URL，確定 **ProductsPortal** 正在雲端中以 Azure Web 應用程式的形式執行。

   > [!IMPORTANT]
   > **ProductsServer** 主控台應用程式必須正在執行中，而且能夠提供資料給 **ProductsPortal** 應用程式。 如果瀏覽器顯示錯誤，請等候幾秒，讓**ProductsServer**載入並顯示下列訊息，然後重新整理瀏覽器。
   >

在瀏覽器中，重新整理 [ **ProductsPortal** ] 頁面。 每次重新整理頁面時，您會看到伺服器應用程式在呼叫來自 **ProductsServer** 的 `GetProducts()` 時顯示一則訊息。

![已更新的輸出][38]

## <a name="next-steps"></a>後續步驟

前進到下列教學課程：

> [!div class="nextstepaction"]
>[將內部部署 WCF 服務公開至網路外 WCF 用戶端](service-bus-relay-tutorial.md)

[0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
[1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app.png
[NuGet]: https://nuget.org

[11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/configure-productsserver.png
[13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/install-nuget-service-bus-productsserver.png
[15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
[16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/choose-web-application-template.png
[17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/add-class-productsportal.png
[18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/change-authentication.png
[9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/web-publish-activity.png
[10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app-locally.png

[21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app-locally-no-content.png
[24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/add-existing-item-link.png
[25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
[26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
[27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/launch-app-as-web-app.png

[36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
[38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
[41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
[43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
