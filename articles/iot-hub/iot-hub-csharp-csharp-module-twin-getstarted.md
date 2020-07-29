---
title: 開始使用/Azure IoT 中樞模組識別 & 模組對應項（.NET）
description: 了解如何使用適用於 .NET 的 SDK，建立模組身分識別及更新模組對應項。
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: menchi
ms.custom: amqp
ms.openlocfilehash: 919d1e37e6066c78e83d58be4fe4667ec67e45ad
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81733382"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-net"></a>開始使用 IoT 中樞模組身分識別和模組對應項（.NET）

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [模組身分識別與模組對應項](iot-hub-devguide-module-twins.md)類似於 Azure IoT 中樞裝置身分識別與裝置對應項，但提供更精細的細微性。 雖然 Azure IoT 中樞裝置身分識別和裝置對應項可讓後端應用程式設定裝置，並提供裝置狀況的可見度，但模組身分識別和模組對應項會為裝置的個別元件提供這些功能。 在具有多個元件的可用裝置（例如以作業系統為基礎的裝置或固件裝置）上，模組身分識別和模組 twins 允許每個元件的隔離設定和條件。

在本教學課程結尾，您會有兩個 .NET 主控台應用程式：

* **CreateIdentities**。 此應用程式會建立裝置身分識別、模組身分識別，以及相關聯的安全性金鑰，以連接您的裝置和模組用戶端。

* **UpdateModuleTwinReportedProperties**。 此應用程式會將已更新的模組對應項報告屬性傳送至您的 IoT 中樞。

> [!NOTE]
> 如需可用來建置兩個應用程式，以在裝置與您的解決方案後端執行之 Azure IoT SDK 的相關資訊，請參閱 [Azure IoT SDK](iot-hub-devguide-sdks.md)。

## <a name="prerequisites"></a>必要條件

* Visual Studio。

* 使用中的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立 [免費帳戶](https://azure.microsoft.com/pricing/free-trial/) 。

## <a name="create-a-hub"></a>建立中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>取得 IoT 中樞連接字串

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]

## <a name="update-the-module-twin-using-net-device-sdk"></a>使用 .NET 裝置 SDK 更新模組對應項

在這一節中，您會在模擬裝置上建立 .NET 主控台應用程式，以便更新模組對應項報告的屬性。

開始之前，請先取得您的模組連接字串。 登入 [Azure 入口網站](https://portal.azure.com/)。 流覽至您的中樞，然後選取 [ **IoT 裝置**]。 尋找**myFirstDevice**。 選取 [ **myFirstDevice** ] 將其開啟，然後選取 [ **myFirstModule** ] 以開啟它。 在 [**模組身分識別詳細資料**] 中，複製下列程式中所需的**連接字串（主要金鑰）** 。

   ![Azure 入口網站模組詳細資料](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-identity-detail.png)

1. 在 Visual Studio 中，選取 [檔案] [新增] [專案]，**將新的**專案加入至您的方案  >  **New**  >  ** **。 在 [建立新專案] 中，選取 [**主控台應用程式（.NET Framework）**]，然後選取 **[下一步]**。

1. 將專案命名為 *UpdateModuleTwinReportedProperties*。 針對 [**方案**]，選取 [**新增至方案**]。 確定 .NET Framework 為 4.6.1 或更新版本。

    ![建立 Visual Studio 專案](./media/iot-hub-csharp-csharp-module-twin-getstarted/configure-update-twins-csharp1.png)

1. 選取 [**建立**] 以建立您的專案。

1. 在 Visual Studio 中，開啟 [**工具**] [  >  **nuget 套件管理員**] [  >  **管理方案的 nuget 套件**]。 選取 [瀏覽]**** 索引標籤。

1. 搜尋並選取 [ **Microsoft. Azure**]，然後選取 [**安裝**]。

    ![安裝 Azure IoT 中樞 .NET 服務 SDK 目前版本](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-client-sdk.png)

1. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    ```

1. 將下列欄位新增到 **Program** 類別。 使用模組連接字串取代預留位置值。

    ```csharp
    private const string ModuleConnectionString = "<Your module connection string>";
    private static ModuleClient Client = null;
    static void ConnectionStatusChangeHandler(ConnectionStatus status, 
      ConnectionStatusChangeReason reason)
    {
        Console.WriteLine("Connection Status Changed to {0}; the reason is {1}", 
          status, reason);
    }
    ```

1. 將下列方法 **OnDesiredPropertyChanged** 新增至 **Program** 類別：

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
        {
            Console.WriteLine("desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
            Console.WriteLine("Sending current time as reported property");
            TwinCollection reportedProperties = new TwinCollection
            {
                ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
            };

            await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
        }
    ```

1. 將下列幾行新增至**Main**方法：

    ```csharp
    static void Main(string[] args)
    {
        Microsoft.Azure.Devices.Client.TransportType transport = 
          Microsoft.Azure.Devices.Client.TransportType.Amqp;

        try
        {
            Client = 
              ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
            Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

            Console.WriteLine("Retrieving twin");
            var twinTask = Client.GetTwinAsync();
            twinTask.Wait();
            var twin = twinTask.Result;
            Console.WriteLine(JsonConvert.SerializeObject(twin.Properties)); 

            Console.WriteLine("Sending app start time as reported property");
            TwinCollection reportedProperties = new TwinCollection();
            reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;

            Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (AggregateException ex)
        {
            Console.WriteLine("Error in sample: {0}", ex);
        }

        Console.WriteLine("Waiting for Events.  Press enter to exit...");
        Console.ReadLine();
        Client.CloseAsync().Wait();
    }
    ```

    此程式碼範例顯示如何擷取模組對應項，並使用 AMQP 通訊協定更新報告的屬性。 在公開預覽中，我們只支援 AMQP 進行模組對應項作業。

1. （選擇性）您可以將這些語句新增至**Main**方法，以從您的模組將事件傳送至 IoT 中樞。 將這些行放在 `try catch` 區塊底下。

    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>執行應用程式

您現在可以執行應用程式。

1. 在 Visual Studio 的**方案總管**中，以滑鼠右鍵按一下您的方案，然後選取 [**設定啟始專案**]。

1. 在 [**通用屬性**] 下，選取 [**啟始專案]。**

1. 選取 [**多個啟始專案**]，然後選取 [**啟動**] 作為應用程式的動作，然後按一下 **[確定]** 以接受變更。

1. 按**F5**啟動應用程式。

## <a name="next-steps"></a>後續步驟

若要繼續開始使用 IoT 中樞並瀏覽其他 IoT 案例，請參閱︰

* [開始使用裝置管理](iot-hub-node-node-device-management-get-started.md)

* [開始使用 IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
