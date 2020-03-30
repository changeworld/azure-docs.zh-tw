---
title: 開始使用 Azure IoT 中樞裝置對應項 (.NET/.NET) | Microsoft Docs
description: 如何使用 Azure IoT 中樞裝置對應項來新增標籤，然後使用 IoT 中樞查詢。 您可以使用適用於 .NET 的 Azure IoT 裝置 SDK，實作模擬的裝置應用程式，也可以使用適用於 .NET 的 Azure IoT 服務 SDK，實作服務應用程式，以新增標籤和執行 IoT 中樞查詢。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: robinsh
ms.openlocfilehash: 426430c075cfcb084cfe3238ebd83a19e909369b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110763"
---
# <a name="get-started-with-device-twins-net"></a>開始使用設備孿生 （.NET）

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

在本教程中，您將創建以下 .NET 主控台應用：

* **創建設備標識**。 此應用創建設備標識和相關安全金鑰以連接類比設備應用。

* **委任標記和查詢**。 此後端應用委任標記和查詢設備孿生。

* **報告連線性**。 此設備應用類比使用較早創建的設備標識連接到 IoT 中心的設備，並報告其連接條件。

> [!NOTE]
> [Azure IoT SDK](iot-hub-devguide-sdks.md) 一文提供可用來建置裝置和後端應用程式之 Azure IoT SDK 的相關資訊。
>

## <a name="prerequisites"></a>Prerequisites

* Visual Studio。

* 使用中的 Azure 帳戶。 如果您沒有帳戶，只需幾分鐘即可創建[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。

* 請確定您的防火牆已開啟連接埠 8883。 本文中的設備示例使用 MQTT 協定，該協定通過埠 8883 進行通信。 某些公司和教育網路環境可能會封鎖此連接埠。 如需此問題的詳細資訊和解決方法，請參閱[連線至 IoT 中樞 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中樞註冊新的裝置

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>獲取 IoT 中心連接字串

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>建立服務應用程式

在本節中，您將使用 C# 創建 .NET 主控台應用，將位置中繼資料添加到與**myDeviceId**關聯的設備孿生。 接著，它會選取位於美國的裝置來查詢儲存在 IoT 中樞的裝置對應項，再查詢會報告行動電話連線的對應項。

1. 在 Visual Studio 中，選取 [建立新專案]****。 在 **"創建新專案**"中，選擇**主控台應用 （.NET 框架），** 然後選擇 **"下一步**"。

1. 在**配置新專案**中，命名專案**AddTag 和Query**。

    ![配置委任標記和查詢專案](./media/iot-hub-csharp-csharp-twin-getstarted/config-addtagsandquery-app.png)

1. 在解決方案資源管理器中，按右鍵**AddTags 和查詢**專案，然後選擇 **"管理 NuGet 包**"。

1. 選擇 **"流覽**"和"搜索"並選擇**Microsoft.Azure.Device**。 選取 [安裝]****。

    ![NuGet 封裝管理員視窗](./media/iot-hub-csharp-csharp-twin-getstarted/nuget-package-addtagsandquery-app.png)

   此步驟會下載及安裝 [Azure IoT 服務 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet 套件與其相依項目，並加入對它的參考。

1. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：

    ```csharp  
    using Microsoft.Azure.Devices;
    ```

1. 將下列欄位新增到 **Program** 類別。 替換為`{iot hub connection string}`在[獲取 IoT 中心連接字串中複製的 IoT 中心連接字串](#get-the-iot-hub-connection-string)。

    ```csharp  
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

1. 將下列方法新增至 Program**** 類別：

    ```csharp  
    public static async Task AddTagsAndQuery()
    {
        var twin = await registryManager.GetTwinAsync("myDeviceId");
        var patch =
            @"{
                tags: {
                    location: {
                        region: 'US',
                        plant: 'Redmond43'
                    }
                }
            }";
        await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);

        var query = registryManager.CreateQuery(
          "SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
        var twinsInRedmond43 = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43: {0}", 
          string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));

        query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
        var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43 using cellular network: {0}", 
          string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
    }
    ```

    **RegistryManager** 類別會公開從服務來與裝置對應項進行互動時所需的所有方法。 先前的程式碼會先初始化 **registryManager** 物件，然後擷取 **myDeviceId** 的裝置對應項，最後會以所需的位置資訊來更新其標籤。

    在更新後，它會執行兩個查詢：第一個只選取位於 **Redmond43** 工廠的裝置對應項，第二個會修改查詢，只選取也透過行動電話網路來連接的裝置。

    先前的程式碼在建立**查詢**物件時，指定傳回的最大文件數。 **query** 物件包含 **HasMoreResults** 布林值屬性，可用來多次叫用 **GetNextAsTwinAsync** 方法以擷取所有結果。 名為**GetNextAsJson**的方法可用於不是設備孿生的結果，例如彙總查詢的結果。

1. 最後，將以下行添加到**Main**方法：

    ```csharp  
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddTagsAndQuery().Wait();
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. 以滑鼠右鍵按一下 **AddTagsAndQuery** 專案，並選取 [偵錯]****，後面接著 [開始新執行個體]**** 來執行此應用程式。 如果是查詢所有位於 **Redmond43** 中的裝置，您在結果中會看到一個裝置，而如果查詢將結果限於使用行動電話網路的裝置，則您不會看到任何裝置。

    ![視窗中的查詢結果](./media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png)

在下一節，您將建立一個裝置應用程式，以報告連線資訊並變更上一節的查詢結果。

## <a name="create-the-device-app"></a>建立裝置應用程式

在本節中，您將建立一個 .NET 主控台應用程式，此應用程式會以 **myDeviceId** 來連線到您的中樞，然後更新其回報屬性，以包含資訊來指出目前使用行動電話通訊網路來連線。

1. 在視覺化工作室中，選擇 **"檔** > **新專案** > **"。** 在**創建新專案**中，選擇**主控台應用程式 （.NET 框架），** 然後選擇 **"下一步**"。

1. 在 **"配置新專案"** 中，命名專案**報告連接。** 對於**解決方案**，選擇 **"添加到解決方案**"，然後選擇 **"創建**"。

1. 在解決方案資源管理器中，按右鍵**報表連接**專案，然後選擇 **"管理 NuGet 包**"。

1. 選擇 **"流覽**"和"搜索"並選擇**Microsoft.Azure.Devices.用戶端**。 選取 [安裝]****。

   此步驟下載、安裝並添加對[Azure IoT 設備 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet 包及其依賴項的引用。

1. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：

    ```csharp  
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. 將下列欄位新增到 **Program** 類別。 替換為`{device connection string}`您在[在 IoT 中心中註冊新設備中注意到的設備](#register-a-new-device-in-the-iot-hub)連接字串。

    ```csharp  
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

1. 將下列方法新增至 Program**** 類別：

    ```csharp
    public static async void InitClient()
    {
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
              TransportType.Mqtt);
            Console.WriteLine("Retrieving twin");
            await Client.GetTwinAsync();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

    **Client** 物件會公開從裝置來與裝置對應項進行互動時所需的所有方法。 上面顯示的代碼初始化**了用戶端**物件，然後檢索**myDeviceId**的設備孿生。

1. 將下列方法新增至 Program**** 類別：

    ```csharp  
    public static async void ReportConnectivity()
    {
        try
        {
            Console.WriteLine("Sending connectivity data as reported property");

            TwinCollection reportedProperties, connectivity;
            reportedProperties = new TwinCollection();
            connectivity = new TwinCollection();
            connectivity["type"] = "cellular";
            reportedProperties["connectivity"] = connectivity;
            await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

   上面的代碼使用連接資訊更新**myDeviceId**的報告屬性。

1. 最後，將以下行添加到**Main**方法：

    ```csharp
    try
    {
        InitClient();
        ReportConnectivity();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. 在解決方案資源管理器中，按右鍵您的解決方案，然後選擇 **"設置啟動專案**"。

1. 在 **"常見屬性** > **啟動專案"中**，選擇**多個啟動專案**。 對於**報表連接，** 選擇 **"開始**作為**操作**"。 選取 [ **確定** ] 以儲存變更。  

1. 通過按右鍵**報表連接**專案並選擇 **"調試**"來運行此應用程式，然後**選擇"啟動新實例**"。 您應該看到應用程式獲取孿生資訊，然後作為***報告的屬性***發送連接。

    ![執行裝置應用程式以回報連線](./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png)

   設備報告其連接資訊後，應出現在兩個查詢中。

1. 按右鍵**AddTags 和查詢**專案，然後選擇 **"調試** > **啟動"新實例**以再次執行查詢。 這一次，**我的DeviceId**應出現在兩個查詢結果中。

    ![已成功回報裝置連線](./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png)

## <a name="next-steps"></a>後續步驟

在此教學課程中，您在 Azure 入口網站中設定了新的 IoT 中樞，然後在 IoT 中樞的身分識別登錄中建立了裝置身分識別。 您已從後端應用程式將裝置中繼資料新增為標籤，並撰寫模擬裝置應用程式來報告裝置對應項中的裝置連線資訊。 您也了解如何使 類似 SQL 的 IoT 中樞查詢語言來查詢此資訊。

您可以從以下資源中瞭解更多資訊：

* 要瞭解如何從設備發送遙測資料，請參閱[將遙測資料從設備發送到 IoT 中心](quickstart-send-telemetry-dotnet.md)教程。

* 要瞭解如何使用設備孿生所需的屬性配置設備，請參閱[使用所需屬性配置設備](tutorial-device-twins.md)教程。

* 要瞭解如何以對話模式控制設備（例如從使用者控制的應用打開風扇），請參閱[使用直接方法](quickstart-control-device-dotnet.md)教程。
