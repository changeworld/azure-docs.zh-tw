---
title: 使用 Azure IoT 中心模組標識&模組孿生 （.NET） 開始
description: 了解如何使用適用於 .NET 的 SDK，建立模組身分識別及更新模組對應項。
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: menchi
ms.openlocfilehash: e728d0ef8f52927687d56bd1d4c64f03c53ef401
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73947673"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-net"></a>開始使用 IoT 中心模組標識和模組孿生 （.NET）

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [模組身分識別與模組對應項](iot-hub-devguide-module-twins.md)類似於 Azure IoT 中樞裝置身分識別與裝置對應項，但提供更精細的細微性。 當 Azure IoT 中心設備標識和設備孿生使後端應用程式能夠配置設備並提供設備條件的可見度時，模組標識和模組孿生為設備的單個元件提供這些功能。 在具有多個元件（如基於作業系統的設備或固件設備、模組標識和模組孿生）的功能設備上，允許為每個元件提供隔離的配置和條件。

在本教學課程結尾，您會有兩個 .NET 主控台應用程式：

* **創建標識**。 此應用創建設備標識、模組標識和相關安全金鑰以連接設備和模組用戶端。

* **更新模組孿生報告屬性**。 此應用程式向 IoT 中心發送更新的模組孿生報告屬性。

> [!NOTE]
> 如需可用來建置兩個應用程式，以在裝置與您的解決方案後端執行之 Azure IoT SDK 的相關資訊，請參閱 [Azure IoT SDK](iot-hub-devguide-sdks.md)。

## <a name="prerequisites"></a>Prerequisites

* Visual Studio。

* 使用中的 Azure 帳戶。 如果您沒有帳戶，只需幾分鐘即可創建[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="create-a-hub"></a>建立中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>獲取 IoT 中心連接字串

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]

## <a name="update-the-module-twin-using-net-device-sdk"></a>使用 .NET 裝置 SDK 更新模組對應項

在這一節中，您會在模擬裝置上建立 .NET 主控台應用程式，以便更新模組對應項報告的屬性。

開始之前，獲取模組連接字串。 登錄到 Azure[門戶](https://portal.azure.com/)。 導航到您的中心並選擇**IoT 設備**。 查找**我的第一個設備**。 選擇**myFirstDevice**將其打開，然後選擇**myFirstModule**將其打開。 在**模組標識詳細資訊**中，在以下過程中根據需要複製**連接字串（主鍵）。**

   ![Azure 入口網站模組詳細資料](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-identity-detail.png)

1. 在 Visual Studio 中，通過選擇 **"檔** > **新專案** > **"將**新專案添加到解決方案中。 在創建新專案時，選擇**主控台應用 （.NET 框架），** 然後選擇 **"下一步**"。

1. 將專案命名為 *UpdateModuleTwinReportedProperties*。 對於**解決方案**，選擇 **"添加到解決方案**"。 確定 .NET Framework 為 4.6.1 或更新版本。

    ![建立 Visual Studio 專案](./media/iot-hub-csharp-csharp-module-twin-getstarted/configure-update-twins-csharp1.png)

1. 選擇 **"創建**"以創建專案。

1. 在視覺化工作室中，打開**工具** > **NuGet 包管理器** > **管理 NuGet 包以進行解決方案**。 選取 [瀏覽]**** 索引標籤。

1. 搜索並選擇**Microsoft.Azure.Devices.用戶端**，然後選擇 **"安裝**"。

    ![安裝 Azure IoT 中心 .NET 服務 SDK 當前版本](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-client-sdk.png)

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

1. 將以下行添加到**Main**方法：

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

1. 或者，您可以將這些語句添加到**Main**方法，以便從模組向 IoT 中心發送事件。 將這些線放在塊下方`try catch`。

    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>執行應用程式

您現在可以運行應用。

1. 在視覺化工作室中，在**解決方案資源管理器**中，按右鍵您的解決方案，然後選擇"**設置啟動專案**"。

1. 在 **"通用屬性**"下，選擇**啟動專案。**

1. 選擇**多個啟動專案**，然後選擇 **"開始**"作為應用的操作，**然後選擇"確定**"以接受您的更改。

1. 按**F5**啟動應用。

## <a name="next-steps"></a>後續步驟

若要繼續開始使用 IoT 中樞並瀏覽其他 IoT 案例，請參閱︰

* [開始使用裝置管理](iot-hub-node-node-device-management-get-started.md)

* [開始使用 IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
