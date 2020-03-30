---
title: 開始使用 Azure IoT 中樞裝置管理 (.NET/.NET) | Microsoft Docs
description: 如何使用 Azure IoT 中樞裝置管理來起始遠端裝置重新開機。 您可以使用適用於 .NET 的 Azure IoT 裝置 SDK，實作模擬裝置應用程式 (包含直接方法)，也可以使用適用於 .NET 的 Azure IoT 服務 SDK，實作服務應用程式 (叫用直接方法)。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: 3b37d7e049e7daabbbb4fe1a7b49feb654e8accc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110253"
---
# <a name="get-started-with-device-management-net"></a>開始使用裝置管理 （.NET）

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

本教學課程說明如何：

* 使用 Azure 入口網站來建立 IoT 中樞，並且在 IoT 中樞建立裝置身分識別。

* 建立模擬裝置應用程式，其包含可將該裝置重新開機的直接方法。 直接方法是從雲端叫用。

* 建立 .NET 主控台應用程式，可透過您的 IoT 中樞在模擬的裝置應用程式中呼叫重新啟動直接方法。

在本教學課程結尾，您會有兩個 .NET 主控台應用程式：

* **類比託管設備**。 此應用通過較早創建的設備標識連接到 IoT 中心，接收直接重新開機方法，類比物理重新開機，並報告上次重新開機的時間。

* **觸發重新開機**。 此應用在類比設備應用中調用直接方法，顯示回應並顯示更新的報告屬性。

## <a name="prerequisites"></a>Prerequisites

* Visual Studio。

* 使用中的 Azure 帳戶。 如果您沒有帳戶，只需幾分鐘即可創建[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。

* 請確定您的防火牆已開啟連接埠 8883。 本文中的設備示例使用 MQTT 協定，該協定通過埠 8883 進行通信。 某些公司和教育網路環境可能會封鎖此連接埠。 如需此問題的詳細資訊和解決方法，請參閱[連線至 IoT 中樞 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中樞註冊新的裝置

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>獲取 IoT 中心連接字串

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>使用直接方法在裝置上觸發遠端重新啟動

在本節中，您將使用 C# 創建 .NET 主控台應用，該應用使用直接方法在設備上啟動遠端重新開機。 應用程式使用裝置對應項查詢來探索該裝置的上次重新開機時間。

1. 在 Visual Studio 中，選取 [建立新專案]****。

1. 在 **"創建新專案"** 中，查找並選擇**主控台應用 （.NET 框架）** 專案範本，然後選擇 **"下一步**"。

1. 在 **"配置新專案"** 中，命名專案*觸發器重新開機*，然後選擇 .NET 框架版本 4.5.1 或更高版本。 選取 [建立]****。

    ![新的 Visual C# Windows 傳統桌面專案](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-configure.png)

1. 在**解決方案資源管理器**中，按右鍵**觸發器重新開機**專案，然後選擇 **"管理 NuGet 包**"。

1. 選擇 **"流覽**"，然後搜索並選擇**Microsoft.Azure.Device**。 選擇 **"安裝**"以安裝**Microsoft.Azure.設備**包。

    ![NuGet 封裝管理員視窗](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-nuget-devices.png)

   此步驟會下載及安裝 [Azure IoT 服務 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet 套件與其相依項目，並加入對它的參考。

1. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```

1. 將下列欄位新增到 **Program** 類別。 將`{iot hub connection string}`預留位置值替換為以前在[獲取 IoT 中心連接字串](#get-the-iot-hub-connection-string)中複製的 IoT 中心連接字串。

   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

1. 將下列方法新增至 Program**** 類別。  此程式碼會取得用於重新啟動裝置的裝置對應項，並輸出報告屬性。

   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```

1. 將下列方法新增至 Program**** 類別。  此程式碼會使用直接方法在裝置上起始重新啟動。

   ```csharp
   public static async Task StartReboot()
   {
       client = ServiceClient.CreateFromConnectionString(connString);
       CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
       method.ResponseTimeout = TimeSpan.FromSeconds(30);

       CloudToDeviceMethodResult result = await 
         client.InvokeDeviceMethodAsync(targetDevice, method);

       Console.WriteLine("Invoked firmware update on device.");
   }
   ```

1. 最後，將以下行添加到**Main**方法：

   ```csharp
   registryManager = RegistryManager.CreateFromConnectionString(connString);
   StartReboot().Wait();
   QueryTwinRebootReported().Wait();
   Console.WriteLine("Press ENTER to exit.");
   Console.ReadLine();
   ```

1. 選擇**生成** > **生成解決方案**。

> [!NOTE]
> 本教學課程只針對裝置的回報屬性執行單一查詢。 在生產環境程式碼中，建議您進行輪詢以偵測回報屬性中的變更。

## <a name="create-a-simulated-device-app"></a>建立模擬裝置應用程式

在本節中，您可：

* 建立 .NET 主控台應用程式，以回應雲端所呼叫的直接方法。

* 觸發模擬裝置重新啟動。

* 使用回報的屬性來啟用裝置對應項查詢，以識別裝置及其上次重新啟動時間。

要創建類比設備應用，請按照以下步驟操作：

1. 在視覺化工作室中，在已創建的觸發器重新開機解決方案中，選擇 **"檔** > **新專案** > **"。** 在 **"創建新專案"** 中，查找並選擇**主控台應用 （.NET 框架）** 專案範本，然後選擇 **"下一步**"。

1. 在 **"配置新專案"** 中，命名專案*類比管理設備*，並為 **"解決方案**"選擇 **"添加到解決方案**"。 選取 [建立]****。

    ![命名專案並將其添加到解決方案](./media/iot-hub-csharp-csharp-device-management-get-started/configure-device-app.png)

1. 在解決方案資源管理器中，按右鍵新的**類比管理設備**專案，然後選擇 **"管理 NuGet 包**"。

1. 選擇 **"流覽**"，然後搜索並選擇**Microsoft.Azure.Devices.用戶端**。 選取 [安裝]****。

    ![NuGet 套件管理員視窗用戶端應用程式](./media/iot-hub-csharp-csharp-device-management-get-started/create-device-nuget-devices-client.png)

   此步驟下載、安裝並添加對[Azure IoT 設備 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet 包及其依賴項的引用。

1. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

1. 將下列欄位新增到 **Program** 類別。 將`{device connection string}`預留位置值替換為以前在[IoT 中心中註冊新設備](#register-a-new-device-in-the-iot-hub)中記錄的設備連接字串。

    ```csharp
    static string DeviceConnectionString = "{device connection string}";
    static DeviceClient Client = null;
    ```

1. 新增下列項目以在裝置上實作直接方法：

   ```csharp
   static Task<MethodResponse> onReboot(MethodRequest methodRequest, object userContext)
   {
       // In a production device, you would trigger a reboot 
       //   scheduled to start after this method returns.
       // For this sample, we simulate the reboot by writing to the console
       //   and updating the reported properties.
       try
       {
           Console.WriteLine("Rebooting!");

           // Update device twin with reboot time. 
           TwinCollection reportedProperties, reboot, lastReboot;
           lastReboot = new TwinCollection();
           reboot = new TwinCollection();
           reportedProperties = new TwinCollection();
           lastReboot["lastReboot"] = DateTime.Now;
           reboot["reboot"] = lastReboot;
           reportedProperties["iothubDM"] = reboot;
           Client.UpdateReportedPropertiesAsync(reportedProperties).Wait();
       }
       catch (Exception ex)
       {
           Console.WriteLine();
           Console.WriteLine("Error in sample: {0}", ex.Message);
       }

       string result = @"{""result"":""Reboot started.""}";
       return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
   }
   ```

1. 最後，在 **Main** 方法中新增下列程式碼以開啟 IoT 中樞的連線，並啟動方法接聽程式︰

   ```csharp
   try
   {
       Console.WriteLine("Connecting to hub");
       Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
         TransportType.Mqtt);

       // setup callback for "reboot" method
       Client.SetMethodHandlerAsync("reboot", onReboot, null).Wait();
       Console.WriteLine("Waiting for reboot method\n Press enter to exit.");
       Console.ReadLine();

       Console.WriteLine("Exiting...");

       // as a good practice, remove the "reboot" handler
       Client.SetMethodHandlerAsync("reboot", null, null).Wait();
       Client.CloseAsync().Wait();
   }
   catch (Exception ex)
   {
       Console.WriteLine();
       Console.WriteLine("Error in sample: {0}", ex.Message);
   }
   ```

1. 在"解決方案資源管理器"中，按右鍵解決方案，然後選擇"**設置啟動專案**"。

1. 對於**常見屬性** > **啟動專案**，選擇**單個啟動專案**，然後選擇**類比管理設備**專案。 選取 [ **確定** ] 以儲存變更。

1. 選擇**生成** > **生成解決方案**。

> [!NOTE]
> 為了簡單起見，本教學課程不會實作任何重試原則。 在生產代碼中，應實現重試策略（如指數回退），如[瞬態故障處理](/azure/architecture/best-practices/transient-faults)中的建議。

## <a name="run-the-apps"></a>執行應用程式

現在，您可以運行這些應用。

1. 要在解決方案資源管理器中運行 .NET 設備應用**類比託管設備**，請按右鍵 **"類比託管設備**"專案，選擇 **"調試**"，然後選擇 **"啟動新實例**"。 應用應開始偵聽來自 IoT 中心的方法調用。

1. 連接設備並等待方法調用後，按右鍵**觸發器重新開機**專案，選擇 **"調試**"，然後選擇 **"啟動新實例**"。

   您應該會看到 "Rebooting!" 顯示在 **SimulatedManagedDevice** 主控台中，以及看到回報的裝置屬性顯示在 **TriggerReboot** 主控台中，其中包括上次重新啟動時間。

    ![服務和裝置應用程式執行](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
