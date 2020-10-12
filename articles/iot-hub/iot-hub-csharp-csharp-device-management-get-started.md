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
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: 5760c574e64a3b3b4a1df12092cff44374790a90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89018394"
---
# <a name="get-started-with-device-management-net"></a>開始使用裝置管理 (.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

本教學課程說明如何：

* 使用 Azure 入口網站來建立 IoT 中樞，並且在 IoT 中樞建立裝置身分識別。

* 建立模擬裝置應用程式，其包含可將該裝置重新開機的直接方法。 直接方法是從雲端叫用。

* 建立 .NET 主控台應用程式，可透過您的 IoT 中樞在模擬的裝置應用程式中呼叫重新啟動直接方法。

在本教學課程結尾，您會有兩個 .NET 主控台應用程式：

* **SimulateManagedDevice**： 此應用程式會以先前建立的裝置身分識別連線到 IoT 中樞、接收 reboot 直接方法、模擬實體重新開機，以及回報上次重新開機的時間。

* **TriggerReboot**： 此應用程式會在模擬裝置應用程式中呼叫直接方法、顯示回應，以及顯示更新的報告屬性。

## <a name="prerequisites"></a>必要條件

* Visual Studio。

* 使用中的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立 [免費帳戶](https://azure.microsoft.com/pricing/free-trial/) 。

* 請確定您的防火牆已開啟連接埠 8883。 本文中的裝置範例會使用 MQTT 通訊協定，其會透過連接埠 8883 進行通訊。 某些公司和教育網路環境可能會封鎖此連接埠。 如需此問題的詳細資訊和解決方法，請參閱[連線至 IoT 中樞 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中樞註冊新的裝置

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>取得 IoT 中樞連接字串

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>使用直接方法在裝置上觸發遠端重新啟動

在本節中，您會建立 .NET 主控台應用程式 (使用 C#)，此應用程式會使用直接方法起始遠端重新開機。 應用程式使用裝置對應項查詢來探索該裝置的上次重新開機時間。

1. 在 Visual Studio 中，選取 [建立新專案]。

1. 在 [建立新專案] 中，尋找並選取 [主控台應用程式 (.NET Framework)] 專案範本，然後選取 [下一步]。

1. 在 [設定新專案] 中，將專案命名為 *TriggerReboot*，然後選取 .NET Framework 4.5.1 版或更新版本。 選取 [建立]。

    ![新的 Visual C# Windows 傳統桌面專案](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-configure.png)

1. 在 [方案總管] 中，以滑鼠右鍵按一下 [TriggerReboot] 專案，然後選取 [管理 NuGet 套件]。

1. 選取 [瀏覽]，然後搜尋並選取 [Microsoft.Azure.Devices]。 選取 [安裝] 以安裝 **Microsoft.Azure.Devices** 套件。

    ![NuGet 封裝管理員視窗](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-nuget-devices.png)

   此步驟會下載及安裝 [Azure IoT 服務 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet 套件與其相依性，並加入對它的參考。

1. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```

1. 將下列欄位新增到 **Program** 類別。 將 `{iot hub connection string}` 預留位置值取代為先前在 [取得 IoT 中樞連接字串](#get-the-iot-hub-connection-string)內複製的 IoT 中樞連接字串。

   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

1. 將下列方法新增至 **Program** 類別。  此程式碼會取得用於重新啟動裝置的裝置對應項，並輸出報告屬性。

   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```

1. 將下列方法新增至 **Program** 類別。  此程式碼會使用直接方法在裝置上起始重新啟動。

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

1. 最後，將下列幾行新增至 **Main** 方法：

   ```csharp
   registryManager = RegistryManager.CreateFromConnectionString(connString);
   StartReboot().Wait();
   QueryTwinRebootReported().Wait();
   Console.WriteLine("Press ENTER to exit.");
   Console.ReadLine();
   ```

1. 選取 [建置] > [建置方案]。

> [!NOTE]
> 本教學課程只針對裝置的回報屬性執行單一查詢。 在生產環境程式碼中，建議您進行輪詢以偵測回報屬性中的變更。

## <a name="create-a-simulated-device-app"></a>建立模擬裝置應用程式

在本節中，您可：

* 建立 .NET 主控台應用程式，以回應雲端所呼叫的直接方法。

* 觸發模擬裝置重新啟動。

* 使用回報的屬性來啟用裝置對應項查詢，以識別裝置及其上次重新啟動時間。

若要建立模擬裝置應用程式，請遵循下列步驟：

1. 在 Visual Studio 中，於已建立的 TriggerReboot 方案中，選取 [檔案] > [新增] > [專案]。 在 [建立新專案] 中，尋找並選取 [主控台應用程式 (.NET Framework)] 專案範本，然後選取 [下一步]。

1. 在 [設定新專案] 中，將專案命名為 *SimulateManagedDevice*，然後針對 [方案] 選取 [新增至方案]。 選取 [建立]。

    ![命名專案並新增至方案](./media/iot-hub-csharp-csharp-device-management-get-started/configure-device-app.png)

1. 在 [方案總管] 中，以滑鼠右鍵按一下新的 [SimulateManagedDevice] 專案，然後選取 [管理 NuGet 套件]。

1. 選取 [瀏覽]，然後搜尋並選取 [Microsoft.Azure.Devices.Client]。 選取 [安裝]。

    ![NuGet 套件管理員視窗用戶端應用程式](./media/iot-hub-csharp-csharp-device-management-get-started/create-device-nuget-devices-client.png)

   此步驟會下載和安裝 [Azure IoT 裝置 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet 套件及其相依性，並新增其參考。

1. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

1. 將下列欄位新增到 **Program** 類別。 將 `{device connection string}` 預留位置值取代為先前於 [在 IoT 中樞註冊新裝置](#register-a-new-device-in-the-iot-hub)內記下的裝置連接字串。

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

1. 在 [方案總管] 中，以滑鼠右鍵按一下方案，然後選取 [設定啟始專案]。

1. 針對 [通用屬性] > [啟始專案]，選取 [單一啟始專案]，然後選取 [SimulateManagedDevice] 專案。 選取 [ **確定** ] 以儲存變更。

1. 選取 [建置] > [建置方案]。

> [!NOTE]
> 為了簡單起見，本教學課程不會實作任何重試原則。 在生產程式碼中，您應該如 [暫時性錯誤處理](/azure/architecture/best-practices/transient-faults)中所建議來實作重試原則 (例如指數輪詢)。

## <a name="run-the-apps"></a>執行應用程式

您現在可開始執行應用程式。

1. 若要執行 .NET 裝置應用程式 **SimulateManagedDevice**，請在 [方案總管] 中，以滑鼠右鍵按一下 [SimulateManagedDevice] 專案，選取 [偵錯]，然後選取 [開始新執行個體]。 應用程式應該會開始接聽來自 IoT 中樞的方法呼叫。

1. 連線到裝置並等候方法引動過程之後，請以滑鼠右鍵按一下 **TriggerReboot** 專案，選取 [偵錯]，然後選取 [開始新執行個體]。

   您應該會看到 "Rebooting!" 顯示在 **SimulatedManagedDevice** 主控台中，以及看到回報的裝置屬性顯示在 **TriggerReboot** 主控台中，其中包括上次重新啟動時間。

    ![服務和裝置應用程式執行](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
