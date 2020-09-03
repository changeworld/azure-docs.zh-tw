---
title: 教學課程：使用 Azure 轉送的 REST 教學課程
description: 教學課程：建置 Azure 轉送主機應用程式以公開 REST 架構介面。
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.date: 06/23/2020
ms.openlocfilehash: 0620f55650d0e4da0cd7a616649df952f3017455
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88922322"
---
# <a name="tutorial-azure-wcf-relay-rest-tutorial"></a>教學課程：Azure WCF 轉送的 REST 教學課程

本教學課程說明如何建置 Azure 轉送主機應用程式以公開 REST 架構介面。 REST 可讓 Web 用戶端 (例如 Web 瀏覽器) 透過 HTTP 要求存取服務匯流排 API。

本教學課程會使用 Windows Communication Foundation (WCF) REST 程式設計模型，在 Azure 轉送上建構 REST 服務。 如需詳細資訊，請參閱 [WCF REST 程式設計模型](/dotnet/framework/wcf/feature-details/wcf-web-http-programming-model)和[設計與實作服務](/dotnet/framework/wcf/designing-and-implementing-services)。

您將在本教學課程中執行下列工作：

> [!div class="checklist"]
>
> * 安裝本教學課程的必要條件。
> * 建立轉送命名空間。
> * 定義 REST 型 WCF 服務合約。
> * 實作 REST 型 WCF 合約。
> * 裝載及執行 REST 型 WCF 服務。
> * 執行和測試服務。

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要下列必要條件：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前[建立免費帳戶](https://azure.microsoft.com/free/)。
* [Visual Studio 2015 或更新版本](https://www.visualstudio.com)。 本教學課程中的範例使用 Visual Studio 2019。
* 適用於 .NET 的 Azure SDK。 從 [SDK 下載頁面](https://azure.microsoft.com/downloads/)進行安裝。

## <a name="create-a-relay-namespace"></a>建立轉送命名空間

若要開始在 Azure 中使用轉送功能，首先必須建立服務命名空間。 命名空間提供範圍容器，可在應用程式內進行 Azure 資源定址。 請依照[這裡的指示](relay-create-namespace-portal.md)來建立轉送命名空間。

## <a name="define-a-rest-based-wcf-service-contract-to-use-with-azure-relay"></a>定義 REST 架構的 WCF 服務合約以與 Azure 轉送搭配使用

當您建立 WCF REST 樣式服務時，必須定義合約。 合約會指定主機支援哪些作業。 服務作業類似於 Web 服務方法。 透過 C++、C# 或 Visual Basic 介面定義合約。 介面中的每個方法會對應一個特定服務作業。 將 [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) 屬性套用至每個介面，並將 [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) 屬性套用至每個作業。 

> [!TIP]
> 如果介面中具備 [OperationContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) 的方法沒有 [ServiceContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute)，則不會公開該方法。 用來執行這些工作的程式碼會出現在程序後面的範例中。

WCF 合約與 REST 樣式合約之間的主要差異在於，為 [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) 新增了一個屬性：[WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute)。 這個屬性可讓您將介面的方法對應至介面另一端的方法。 此範例使用 [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute) 屬性，以將方法連結至 `HTTP GET`。 此方法可讓服務匯流排正確擷取和解譯傳送至介面的命令。

### <a name="to-create-a-contract-with-an-interface"></a>使用介面建立合約

1. 以系統管理員身分啟動 Microsoft Visual Studio。 若要這樣做，請以滑鼠右鍵按一下 Visual Studio 程式圖示，然後選取 [以系統管理員身分執行]。
1. 在 Visual Studio 中，選取 [建立新專案]。
1. 在 [建立新專案] 中，針對 C# 選擇 [主控台應用程式 (.NET Framework)]，然後選取 [下一步]。
1. 將專案命名為 *ImageListener*。 使用預設的 [位置]，然後選取 [建立]。

   若為 C# 專案，Visual Studio 會建立 *Program.cs* 檔案。 這個類別包含空的 `Main()` 方法，即正確建置主控台應用程式專案所需的方法。

1. 在 [方案總管] 中，以滑鼠右鍵按一下 **ImageListener** 專案，然後選取 [管理 NuGet 套件]。
1. 選取 [瀏覽]，然後搜尋並選擇 **WindowsAzure.ServiceBus**。 選取 [安裝] 並接受使用條款。

    此步驟會新增服務匯流排和 *System.ServiceModel.dll* 的參考。 此套件會自動新增服務匯流排程式庫和 WCF `System.ServiceModel` 的參考。

1. 將 `System.ServiceModel.Web.dll` 的參考明確新增至專案。 在 [方案總管] 中，以滑鼠右鍵按一下專案資料夾下的 [參考]，然後選取 [新增參考]。
1. 在 [新增參考] 中選取 [Framework]，然後在 [搜尋] 中輸入 *System.ServiceModel.Web*。 選取 [System.ServiceModel.Web] 核取方塊，然後按一下 [確定]。

接著，對專案進行下列程式碼變更：

1. 在 *Program.cs* 檔案開頭處新增下列 `using` 陳述式。

    ```csharp
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```

    * [System.ServiceModel](/dotnet/api/system.servicemodel) 是可讓您以程式設計方式存取 WCF 基本功能的命名空間。 WCF 轉送會使用 WCF 的許多物件和屬性來定義服務合約。 您會在大部分轉送應用程式中使用此命名空間。
    * [System.ServiceModel.Channels](/dotnet/api/system.servicemodel.channels) 可協助定義通道，您可透過此物件與 Azure 轉送和用戶端網頁瀏覽器進行通訊。
    * [System.ServiceModel.Web](/dotnet/api/system.servicemodel.web) 包含可讓您建立 Web 型應用程式的類型。

1. 將 `ImageListener` 命名空間重新命名為 `Microsoft.ServiceBus.Samples`。

    ```csharp
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```

1. 在命名空間宣告的左大括號後面緊接著定義名為 `IImageContract` 的新介面，並將 `ServiceContractAttribute` 屬性套用至值為 `https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1` 的介面。 

    ```csharp
    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```

    命名空間值與您的整個程式碼範圍中使用的命名空間不同。 命名空間值是此合約的唯一識別碼，且應該具有版本資訊。 如需詳細資訊，請參閱[服務版本設定](/dotnet/framework/wcf/service-versioning)。 明確指定命名空間可避免將預設命名空間值新增至合約名稱。

1. 在 `IImageContract` 介面中，針對 `IImageContract` 合約在介面中公開的單一作業宣告方法，並將 `OperationContract` 屬性套用至您要公開為公用服務匯流排合約一部分的方法。

    ```csharp
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```

1. 在 `OperationContract` 屬性中，新增 `WebGet` 值。

    ```csharp
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```

   新增 `WebGet` 值可讓轉送服務將 HTTP GET 要求路由傳送至 `GetImage`，以及將 `GetImage` 的傳回值轉譯成 `HTTP GETRESPONSE` 回覆。 稍後在教學課程中，您將使用網頁瀏覽器來存取此方法，並可在瀏覽器中顯示影像。

1. 緊接著 `IImageContract` 定義後面，宣告從 `IImageContract` 和 `IClientChannel` 介面繼承的通道。

    ```csharp
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```

   通道是 WCF 物件，服務和用戶端可透過它彼此傳遞資訊。 稍後，會在主機應用程式中建立通道。 Azure 轉送接著會使用此通道，將 HTTP GET 要求從瀏覽器傳遞至您的 `GetImage` 實作。 轉送也會使用通道來取得 `GetImage` 傳回值，並將其轉譯成用戶端瀏覽器的 `HTTP GETRESPONSE`。

1. 選取 [建置] > [建置方案]，以確認您的工作到目前為止的正確性。

### <a name="example-that-defines-a-wcf-relay-contract"></a>定義 WCF 轉送合約的範例

下列程式碼示範定義 WCF 轉送合約的基本介面。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="implement-the-rest-based-wcf-service-contract"></a>實作 REST 型 WCF 服務合約

若要建立 REST 樣式的 WCF 轉送服務，請先使用介面建立合約。 下一步是實作介面。 此程序牽涉到建立名為 `ImageService` 的類別，該類別會實作使用者定義的 `IImageContract` 介面。 實作合約後，接著可使用 *App.config* 檔案設定介面。 此組態檔包含應用程式的必要資訊。 這項資訊包括服務名稱、合約名稱，以及用來與轉送服務通訊的通訊協定類型。 用來執行這些工作的程式碼會出現在程序後面的範例中。

如同先前的步驟，實作 REST 樣式合約與 WCF 轉送合約之間的差異很小。

### <a name="to-implement-a-rest-style-service-bus-contract"></a>實作 REST 樣式服務匯流排合約

1. 緊接在 `IImageContract` 介面的定義之後，建立名為 `ImageService` 的新類別。 `ImageService` 類別會實作 `IImageContract` 介面。

    ```csharp
    class ImageService : IImageContract
    {
    }
    ```

    類似於其他介面實作，您可以在不同的檔案中實作定義。 不過，在此教學課程中，實作會出現在與介面定義和 `Main()` 方法相同的檔案中。

1. 將 [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) 屬性套用至 `IImageService` 類別，以表示此類別為 WCF 合約的實作。

    ```csharp
    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```

    如前所述，此命名空間不是傳統的命名空間。 它是識別合約的 WCF 架構的一部分。 如需詳細資訊，請參閱[資料合約名稱](/dotnet/framework/wcf/feature-details/data-contract-names/)。

1. 將 *.jpg* 影像新增至您的專案。 這個檔案是此服務在接收瀏覽器中顯示的圖片。

   1. 以滑鼠右鍵按一下專案，然後選取 [新增]。
   1. 然後，選取 [現有項目]。
   1. 使用 [新增現有項目] 瀏覽至適當的 .jpg，然後選取 [新增]。 在新增檔案時，從 [檔案名稱] 旁的下拉式清單中選取 [所有檔案]。

   本教學課程的其餘部分均假設影像的名稱為 *image.jpg*。 如果您有不同的檔案，您必須重新命名映像，或變更您的程式碼來彌補。

1. 為確定執行中的服務能夠找到此影像檔，請在 [方案總管] 中以滑鼠右鍵按一下該影像檔，然後選擇 [屬性]。 在 [屬性] 中，將 [複製到輸出目錄] 設定為 [有更新時才複製]。

1. 使用[使用介面建立合約](#to-create-a-contract-with-an-interface)中的程序，將 *System.Drawing.dll* 組件的參考新增至專案。

1. 新增下列相關聯的 `using` 陳述式：

    ```csharp
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```

1. 在 `ImageService` 類別中新增下列建構函式，以載入點陣圖並準備將其傳送至用戶端瀏覽器：

    ```csharp
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";
   
        Image bitmap;
   
        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }
    }
    ```

1. 在先前的程式碼後面，緊接著在 `ImageService` 類別中新增下列 `GetImage` 方法，以傳回包含該影像的 HTTP 訊息。

    ```csharp
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);
   
        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";
   
        return stream;
    }
    ```

    這個實作會使用 `MemoryStream` 來擷取影像，並將其備妥以串流至瀏覽器。 它會從零起算串流位置，將串流內容宣告為 *.jpg*，並且串流資訊。

1. 選取 [建置] > [建置方案]。

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>為服務匯流排上執行的 Web 服務定義組態

1. 在 [方案總管] 中按兩下 **App.config**，以在 Visual Studio 編輯器中開啟該檔案。

    *App.config* 檔案包含服務名稱、端點和繫結。 端點是 Azure 轉送公開的位置，供用戶端與主機互相通訊。 繫結是用來通訊的通訊協定類型。 此處的主要差異是設定的服務端點會參考 [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) 繫結。

1. `<system.serviceModel>` XML 元素是定義一或多個服務的 WCF 元素。 在此，該元素會用來定義服務名稱和端點。 在 `<system.serviceModel>` 元素底部 (但仍在 `<system.serviceModel>` 內)，新增具有以下內容的 `<bindings>` 元素：

    ```xml
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```

    此內容會定義應用程式中使用的繫結。 您可以定義多個繫結，但在本教學課程中，您只會定義一個。

    上述程式碼會將 `relayClientAuthenticationType` 設定為 `None`，以定義 WCF 轉送 [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) 繫結。 此設定表示使用此繫結的端點不需要用戶端認證。

1. 在 `<bindings>` 元素後方加入 `<services>` 元素。 類似於繫結，您可以在單一組態檔中定義多個服務。 不過，您在本教學課程中只會定義一個。

    ```xml
    <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
            <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IImageContract"
                    binding="webHttpRelayBinding"
                    bindingConfiguration="default"
                    behaviorConfiguration="sbTokenProvider"
                    address="" />
        </service>
    </services>
    ```

    此內容會設定使用先前定義之預設 `webHttpRelayBinding` 的服務。 它也會使用在下一個步驟中定義的預設 `sbTokenProvider`。

1. 在 `<services>` 元素後面，建立具有下列內容的 `<behaviors>` 元素，並將 `SAS_KEY` 取代為共用存取簽章 (SAS) 金鑰。 若要從 [Azure 入口網站][Azure portal]取得 SAS 金鑰，請參閱 [取得管理認證](service-bus-relay-tutorial.md#get-management-credentials)。

    ```xml
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
            </endpointBehaviors>
            <serviceBehaviors>
                <behavior name="default">
                    <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
                </behavior>
            </serviceBehaviors>
    </behaviors>
    ```

1. 同樣在 *App.config* 中，以您先前從入口網站取得的連接字串，取代 `<appSettings>` 元素中的整個連接字串值。

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY"/>
    </appSettings>
    ```

1. 選取 [建置] > [建置方案] 以建置整個方案。

### <a name="example-that-implements-the-rest-based-wcf-service-contract"></a>實作 REST 型 WCF 服務合約的範例

下列程式碼會顯示使用 `WebHttpRelayBinding` 繫結針對在服務匯流排上執行的 REST 型服務所進行的合約和服務實作。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

下列範例會顯示與服務相關聯的 *App.config* 檔案。

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2"/>
    </startup>
    <system.serviceModel>
        <extensions>
            <!-- In this extension section we are introducing all known service bus extensions. User can remove the ones they don't need. -->
            <behaviorExtensions>
                <add name="connectionStatusBehavior"
                    type="Microsoft.ServiceBus.Configuration.ConnectionStatusElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="transportClientEndpointBehavior"
                    type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="serviceRegistrySettings"
                    type="Microsoft.ServiceBus.Configuration.ServiceRegistrySettingsElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </behaviorExtensions>
            <bindingElementExtensions>
                <add name="netMessagingTransport"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingTransportExtensionElement, Microsoft.ServiceBus,  Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="tcpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.TcpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpsRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpsRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="onewayRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.RelayedOnewayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingElementExtensions>
            <bindingExtensions>
                <add name="basicHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.BasicHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="webHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WebHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="ws2007HttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WS2007HttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netOnewayRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetOnewayRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netEventRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetEventRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netMessagingBinding"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingExtensions>
        </extensions>
      <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
          <binding name="default">
            <security relayClientAuthenticationType="None" />
          </binding>
        </webHttpRelayBinding>
      </bindings>
      <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
          <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
        </service>
      </services>
      <behaviors>
        <endpointBehaviors>
          <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
              <tokenProvider>
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
              </tokenProvider>
            </transportClientEndpointBehavior>
          </behavior>
        </endpointBehaviors>
        <serviceBehaviors>
          <behavior name="default">
            <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>
    <appSettings>
        <!-- Service Bus specific app settings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY>"/>
    </appSettings>
</configuration>
```

## <a name="host-the-rest-based-wcf-service-to-use-azure-relay"></a>裝載 REST 架構的 WCF 服務以使用 Azure 轉送

本節說明如何搭配 WCF 轉送使用主控台應用程式來執行 Web 服務。 此程序後的範例會提供在本節中撰寫的完整程式碼清單。

### <a name="to-create-a-base-address-for-the-service"></a>建立服務的基底位址

1. 在 `Main()` 函式宣告中，建立變數來儲存專案的命名空間。 務必以您先前建立的轉送命名空間名稱來取代 `yourNamespace`。

    ```csharp
    string serviceNamespace = "yourNamespace";
    ```

    服務匯流排會使用您的命名空間名稱來建立唯一 URI。

1. 為根據命名空間之服務的基底位址建立 `Uri` 執行個體。

    ```csharp
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>建立和設定 Web 服務主機

同樣在 `Main()` 中，使用本節先前建立的 URI 位址建立 Web 服務主機。
  
```csharp
WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
```

服務主機是具現化主機應用程式的 WCF 物件。 此範例會將您要建立的主機類型 (`ImageService`) 以及要公開主機應用程式的位址傳給它。

### <a name="to-run-the-web-service-host"></a>執行 Web 服務主機

1. 同樣在 `Main()` 中，新增以下一行以開啟服務。

    ```csharp
    host.Open();
    ```

    服務現在正在執行。

1. 顯示訊息，指出服務正在執行，以及如何停止服務。

    ```csharp
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. 完成時，關閉服務主機。

    ```csharp
    host.Close();
    ```

### <a name="example-of-the-service-contract-and-implementation"></a>服務合約和實作的範例

下列範例包括教學課程先前步驟的服務合約和實作，以及在主控台應用程式中主控服務。 將下列程式碼編譯為名為 *ImageListener.exe* 的可執行檔。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

## <a name="run-and-test-the-service"></a>執行及測試服務

建置解決方案後，請執行下列命令以執行應用程式：

1. 選取 F5，或瀏覽至可執行檔的位置 *ImageListener\bin\Debug\ImageListener.exe*，以執行服務。 請讓應用程式保持執行狀態，因為下一個步驟將會用到此應用程式。
1. 將位址從命令提示字元複製並貼到瀏覽器以查看映像。
1. 完成作業後，請在命令提示字元視窗中選取 Enter 鍵以關閉應用程式。

## <a name="next-steps"></a>後續步驟

既然您已經建置了使用 Azure 轉送服務的應用程式，請參閱下列文章以進一步了解：

* [什麼是 Azure 轉送？](relay-what-is-it.md)
* [使用 Azure WCF 轉送向外部用戶端公開內部部署 WCF REST 服務](service-bus-relay-tutorial.md)

[Azure portal]: https://portal.azure.com
