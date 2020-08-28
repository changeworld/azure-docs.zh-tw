---
title: 使用 Azure IoT 中樞 (.NET/.NET) 排程作業 | Microsoft Docs
description: 如何排定 Azure IoT 中樞作業在多個裝置上叫用直接方法。 您可以使用適用於 .NET 的 Azure IoT 裝置 SDK，實作模擬裝置應用程式和服務應用程式來執行作業。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: d99cc571394ad7a9c85fb7367c672f96a7302362
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018496"
---
# <a name="schedule-and-broadcast-jobs-net"></a>排程及廣播作業 (.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

使用 Azure IoT 中樞排程和追蹤會更新數百萬部裝置的作業。 使用作業以：

* 更新所需屬性

* 更新標籤

* 叫用直接方法

作業會包裝上述其中一個動作，然後針對由裝置對應項查詢所定義的一組裝置，追蹤執行進度。 例如，後端應用程式可以使用作業，以在 10,000 部裝置上叫用重新開機裝置的直接方法。 您以裝置對應項查詢指定一組裝置，並排程在未來的時間執行作業。 作業會在每部裝置接收和執行重新開機直接方法時追蹤進度。

若要一一了解這些功能，請參閱：

* 裝置對應項和屬性：[開始使用裝置對應項](iot-hub-csharp-csharp-twin-getstarted.md)和[教學課程：如何使用裝置對應項屬性](tutorial-device-twins.md)

* 直接方法：[IoT 中樞開發人員指南 - 直接方法](iot-hub-devguide-direct-methods.md)和[教學課程：使用直接方法](quickstart-control-device-dotnet.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

本教學課程說明如何：

* 建立實作 **LockDoor** 直接方法的裝置應用程式，後端應用程式可呼叫此方法。

* 建立後端應用程式，以建立作業在多部裝置上呼叫 **lockDoor** 直接方法。 另一項作業會將所需的屬性更新傳送到多部裝置。

在本教學課程結尾，您會有兩個 .NET (C#) 主控台應用程式：

* **SimulateDeviceMethods**： 此應用程式會連線到 IoT 中樞，並實作 **LockDoor** 直接方法。

* **ScheduleJob**： 此應用程式會使用作業來呼叫 **LockDoor** 直接方法，並在多部裝置上更新裝置對應項所需的屬性。

## <a name="prerequisites"></a>必要條件

* Visual Studio。

* 使用中的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立 [免費帳戶](https://azure.microsoft.com/pricing/free-trial/) 。

* 請確定您的防火牆已開啟連接埠 8883。 本文中的裝置範例會使用 MQTT 通訊協定，其會透過連接埠 8883 進行通訊。 某些公司和教育網路環境可能會封鎖此連接埠。 如需此問題的詳細資訊和解決方法，請參閱[連線至 IoT 中樞 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中樞註冊新的裝置

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>建立模擬裝置應用程式

在本節中，您會建立 .NET 主控台應用程式，回應解決方案後端所呼叫的直接方法。

1. 在 Visual Studio 中，選取 [建立新專案]，然後選擇 [主控台應用程式 (.NET Framework)] 專案範本。 選取 [下一步] 以繼續操作。

1. 在 [設定新專案] 中，將專案命名為 *SimulateDeviceMethods*，然後選取 [建立]。

    ![設定 SimulateDeviceMethods 專案](./media/iot-hub-csharp-csharp-schedule-jobs/configure-device-app.png)

1. 在 [方案總管] 中，以滑鼠右鍵按一下 [SimulateDeviceMethods] 專案，然後選取 [管理 NuGet 套件]。

1. 在 [NuGet 套件管理員] 中，選取 [瀏覽]，然後搜尋並選擇 [Microsoft.Azure.Devices.Client]。 選取 [安裝]。

    ![NuGet 套件管理員視窗用戶端應用程式](./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png)

    此步驟會下載和安裝 [Azure IoT 裝置 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet 套件及其相依性，並新增其參考。

1. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. 將下列欄位新增到 **Program** 類別。 將預留位置的值取代為您在上一節中所記下的裝置連接字串：

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;
    ```

1. 新增下列程式碼以在裝置上實作直接方法：

    ```csharp
    static Task<MethodResponse> LockDoor(MethodRequest methodRequest, object userContext)
    {
        Console.WriteLine();
        Console.WriteLine("Locking Door!");
        Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);

        string result = "'Door was locked.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```

1. 新增下列方法以在裝置上實作裝置對應項接聽程式：

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

1. 最後，在 **Main** 方法中新增下列程式碼以開啟 IoT 中樞的連線，並啟動方法接聽程式︰

    ```csharp
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
          TransportType.Mqtt);

        Client.SetMethodHandlerAsync("LockDoor", LockDoor, null);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null);

        Console.WriteLine("Waiting for direct method call and device twin update\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        Client.SetMethodHandlerAsync("LockDoor", null, null);
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

1. 儲存您的工作，並建置您的解決方案。

> [!NOTE]
> 為了簡單起見，本教學課程不會實作任何重試原則。 在生產程式碼中，您應該如 [暫時性錯誤處理](/azure/architecture/best-practices/transient-faults)中所建議來實作重試原則 (例如連線重試)。
>

## <a name="get-the-iot-hub-connection-string"></a>取得 IoT 中樞連接字串

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>排程呼叫直接方法及傳送裝置對應項更新的作業

在本節中，您要建立 .NET 主控台應用程式 (使用 C#)，使用作業呼叫 **LockDoor** 直接方法，並將所需的屬性更新傳送至多部裝置。

1. 在 Visual Studio 中，選取 [檔案] >  [新增] >  [專案]。 在 [建立新專案] 中，選擇 [主控台應用程式 (.NET Framework)]，然後選取 [下一步]。

1. 在 [設定新專案] 中，將專案命名為 *ScheduleJob*。 針對 [方案] 選擇 [新增至方案]，然後選取 [建立]。

    ![命名及設定 ScheduleJob 專案](./media/iot-hub-csharp-csharp-schedule-jobs/config-schedule-job-app.png)

1. 在 [方案總管] 中，以滑鼠右鍵按一下 [ScheduleJob] 專案，然後選取 [管理 NuGet 套件]。

1. 在 [NuGet 套件管理員] 中，選取 [瀏覽]，搜尋並選擇 [Microsoft.Azure.Devices]，然後選取 [安裝]。

   此步驟會下載及安裝 [Azure IoT 服務 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet 套件與其相依性，並加入對它的參考。

1. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：

    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

1. 新增下列 `using` 陳述式 (如果預設陳述式中尚不存在)。

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

1. 將下列欄位新增到 **Program** 類別。 將這些預留位置取代為先前在 [取得 IoT 中樞連接字串](#get-the-iot-hub-connection-string)內複製的 IoT 中樞連接字串，以及裝置名稱。

    ```csharp
    static JobClient jobClient;
    static string connString = "<yourIotHubConnectionString>";
    static string deviceId = "<yourDeviceId>";
    ```

1. 將下列方法加入至 **Program** 類別：

    ```csharp
    public static async Task MonitorJob(string jobId)
    {
        JobResponse result;
        do
        {
            result = await jobClient.GetJobAsync(jobId);
            Console.WriteLine("Job Status : " + result.Status.ToString());
            Thread.Sleep(2000);
        } while ((result.Status != JobStatus.Completed) && 
          (result.Status != JobStatus.Failed));
    }
    ```

1. 將下列方法加入至 **Program** 類別：

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = 
          new CloudToDeviceMethod("LockDoor", TimeSpan.FromSeconds(5), 
          TimeSpan.FromSeconds(5));

        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            $"DeviceId IN ['{deviceId}']",
            directMethod,
            DateTime.UtcNow,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Method Job");
    }
    ```

1. 將另一個方法新增至 **Program** 類別：

    ```csharp
    public static async Task StartTwinUpdateJob(string jobId)
    {
        Twin twin = new Twin(deviceId);
        twin.Tags = new TwinCollection();
        twin.Tags["Building"] = "43";
        twin.Tags["Floor"] = "3";
        twin.ETag = "*";

        twin.Properties.Desired["LocationUpdate"] = DateTime.UtcNow;

        JobResponse createJobResponse = jobClient.ScheduleTwinUpdateAsync(
            jobId,
            $"DeviceId IN ['{deviceId}']", 
            twin, 
            DateTime.UtcNow, 
            (long)TimeSpan.FromMinutes(2).TotalSeconds).Result;

        Console.WriteLine("Started Twin Update Job");
    }
    ```

    > [!NOTE]
    > 如需有關查詢語法的詳細資訊，請參閱 [IoT 中樞查詢語言](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language)。
    >

1. 最後，將下列幾行新增至 **Main** 方法：

    ```csharp
    Console.WriteLine("Press ENTER to start running jobs.");
    Console.ReadLine();

    jobClient = JobClient.CreateFromConnectionString(connString);

    string methodJobId = Guid.NewGuid().ToString();

    StartMethodJob(methodJobId);
    MonitorJob(methodJobId).Wait();
    Console.WriteLine("Press ENTER to run the next job.");
    Console.ReadLine();

    string twinUpdateJobId = Guid.NewGuid().ToString();

    StartTwinUpdateJob(twinUpdateJobId);
    MonitorJob(twinUpdateJobId).Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```

1. 儲存您的工作，並建置您的解決方案。

## <a name="run-the-apps"></a>執行應用程式

您現在可以開始執行應用程式。

1. 在 Visual Studio 的 [方案總管] 中，以滑鼠右鍵按一下方案，然後選取 [設定啟始專案]。

1. 選取 [通用屬性] > [啟始專案]，然後選取 [多個啟始專案]。

1. 請確定 `SimulateDeviceMethods` 位於清單頂端，後面接著 `ScheduleJob`。 將其兩個動作設定為 [啟動]，然後選取 [確定]。

1. 執行專案，方法是按一下 [開始] 或移至 [偵錯] 功能表，然後按一下 [開始偵錯]。

   您會看到來自裝置及後端應用程式的輸出。

    ![執行應用程式以排程作業](./media/iot-hub-csharp-csharp-schedule-jobs/schedule-jobs-console-results.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已使用作業來排定裝置的直接方法，以及更新裝置對應項 (twin) 的屬性。

* 若要繼續開始使用 IoT 中樞和裝置管理模式 (例如遠端無線韌體更新)，請參閱[教學課程：如何進行韌體更新](tutorial-firmware-update.md)。

* 若要深入了解將 AI 部署到具有 Azure IoT Edge 的邊緣裝置，請參閱[開始使用 IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)。
