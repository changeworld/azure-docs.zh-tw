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
ms.custom: mqtt
ms.openlocfilehash: 620e0213733d278a28ec1bcad4b031f5764ccda9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733140"
---
# <a name="get-started-with-device-twins-net"></a>開始使用裝置 twins （.NET）

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

在本教學課程中，您會建立下列 .NET 主控台應用程式：

* **CreateDeviceIdentity**。 此應用程式會建立裝置身分識別和相關聯的安全性金鑰，以連接您的模擬裝置應用程式。

* **Addtagsandquery.js**。 此後端應用程式會新增標記和查詢裝置 twins。

* **ReportConnectivity**。 此裝置應用程式會模擬裝置，以使用稍早建立的裝置身分識別連接到您的 IoT 中樞，並報告其連線狀況。

> [!NOTE]
> [Azure IoT SDK](iot-hub-devguide-sdks.md) 一文提供可用來建置裝置和後端應用程式之 Azure IoT SDK 的相關資訊。
>

## <a name="prerequisites"></a>先決條件

* Visual Studio。

* 使用中的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。

* 請確定您的防火牆已開啟連接埠 8883。 本文中的裝置範例使用 MQTT 通訊協定，它會透過埠8883進行通訊。 某些公司和教育網路環境可能會封鎖此連接埠。 如需此問題的詳細資訊和解決方法，請參閱[連線至 IoT 中樞 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中樞註冊新的裝置

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>取得 IoT 中樞連接字串

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>建立服務應用程式

在本節中，您會使用 c # 建立 .NET 主控台應用程式，以將位置中繼資料新增至與**myDeviceId**相關聯的裝置對應項。 接著，它會選取位於美國的裝置來查詢儲存在 IoT 中樞的裝置對應項，再查詢會報告行動電話連線的對應項。

1. 在 Visual Studio 中，選取 [建立新專案]****。 在 [**建立新專案**] 中，選取 [**主控台應用程式（.NET Framework）**]，然後選取 **[下一步]**。

1. 在 [**設定您的新專案**] 中，將專案命名為**addtagsandquery.js**。

    ![設定 Addtagsandquery.js 專案](./media/iot-hub-csharp-csharp-twin-getstarted/config-addtagsandquery-app.png)

1. 在方案總管中，以滑鼠右鍵按一下**addtagsandquery.js**專案，然後選取 [**管理 NuGet 套件**]。

1. 選取 **[流覽]** 並搜尋並選取 [ **Microsoft. Azure 裝置**]。 選取 [安裝]  。

    ![NuGet 封裝管理員視窗](./media/iot-hub-csharp-csharp-twin-getstarted/nuget-package-addtagsandquery-app.png)

   此步驟會下載及安裝 [Azure IoT 服務 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet 套件與其相依項目，並加入對它的參考。

1. 在 Program.cs 檔案`using`的頂端新增下列語句： **Program.cs**

    ```csharp  
    using Microsoft.Azure.Devices;
    ```

1. 將下列欄位新增到 **Program** 類別。 將`{iot hub connection string}`取代為您在[取得 IoT 中樞連接字串](#get-the-iot-hub-connection-string)中複製的 IoT 中樞連接字串。

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

    先前的程式碼在建立**查詢**物件時，指定傳回的最大文件數。 **query** 物件包含 **HasMoreResults** 布林值屬性，可用來多次叫用 **GetNextAsTwinAsync** 方法以擷取所有結果。 稱為**一個 getnextasjson**的方法適用于不是裝置 twins 的結果，例如彙總查詢的結果。

1. 最後，將下列幾行新增至**Main**方法：

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

1. 在 Visual Studio 中，選取 [檔案]   >  [新增]   >  [專案]  。 在 [**建立新專案**] 中，選擇 [**主控台應用程式（.NET Framework）**]，然後選取 **[下一步]**。

1. 在 [**設定您的新專案**] 中，將專案命名為**ReportConnectivity**。 針對 [**方案**]，選擇 [**加入至方案**]，然後選取 [**建立**]。

1. 在方案總管中，以滑鼠右鍵按一下**ReportConnectivity**專案，然後選取 [**管理 NuGet 套件**]。

1. 選取 **[流覽]** 並搜尋並選擇 [ **Microsoft. Azure. 用戶端**]。 選取 [安裝]  。

   此步驟會下載及安裝[Azure IoT 裝置 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet 套件及其相依專案，並新增對它的參考。

1. 在 Program.cs 檔案`using`的頂端新增下列語句： **Program.cs**

    ```csharp  
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. 將下列欄位新增到 **Program** 類別。 將`{device connection string}`取代為您在[IoT 中樞註冊新裝置](#register-a-new-device-in-the-iot-hub)中所記下的裝置連接字串。

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

    **Client** 物件會公開從裝置來與裝置對應項進行互動時所需的所有方法。 上面顯示的程式碼會初始化**用戶端**物件，然後抓取**myDeviceId**的裝置對應項。

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

   上述程式碼會使用連線資訊來更新**myDeviceId**的報告屬性。

1. 最後，將下列幾行新增至**Main**方法：

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

1. 在方案總管中，以滑鼠右鍵按一下您的方案，然後選取 [**設定啟始專案**]。

1. 在 [**通用屬性** > ] [**啟始專案**] 中，選取 [**多個啟始專案**]。 針對 [ **ReportConnectivity**]，選取 [**啟動**] 做為**動作**。 選取 [ **確定** ] 以儲存變更。  

1. 以滑鼠右鍵按一下**ReportConnectivity**專案，然後依序選取 [ **Debug**] 和 [**開始新實例**]，來執行此應用程式。 您應該會看到應用程式取得對應項資訊，然後以***報告屬性***的形式傳送連接。

    ![執行裝置應用程式以回報連線](./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png)

   在裝置回報其連線資訊之後，它應該會出現在這兩個查詢中。

1. 以滑鼠右鍵按一下**addtagsandquery.js**專案，然後選取 [ **Debug** > ] [**開始新實例**] 以再次執行查詢。 這次， **myDeviceId**應該會出現在這兩個查詢結果中。

    ![已成功回報裝置連線](./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png)

## <a name="next-steps"></a>後續步驟

在此教學課程中，您在 Azure 入口網站中設定了新的 IoT 中樞，然後在 IoT 中樞的身分識別登錄中建立了裝置身分識別。 您已從後端應用程式將裝置中繼資料新增為標籤，並撰寫模擬裝置應用程式來報告裝置對應項中的裝置連線資訊。 您也了解如何使 類似 SQL 的 IoT 中樞查詢語言來查詢此資訊。

您可以從下列資源深入瞭解：

* 若要瞭解如何從裝置傳送遙測，請參閱將[遙測從裝置傳送至 IoT 中樞](quickstart-send-telemetry-dotnet.md)教學課程。

* 若要瞭解如何使用裝置對應項的所需屬性來設定裝置，請參閱[使用所需屬性來設定裝置](tutorial-device-twins.md)教學課程。

* 若要瞭解如何以互動方式控制裝置（例如，從使用者控制的應用程式開啟風扇），請參閱[使用直接方法](quickstart-control-device-dotnet.md)教學課程。
