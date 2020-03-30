---
title: 將裝置中的檔案上傳至使用 .NET 的 Azure IoT 中樞 | Microsoft Docs
description: 如何使用適用於 .NET 的 Azure IoT 裝置 SDK 將檔案從裝置上傳至雲端。 上傳的檔案會儲存在 Azure 儲存體 blob 容器中。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/04/2017
ms.author: robinsh
ms.openlocfilehash: b379f158672a9df3056acb09c63c392869a53283
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77108705"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-net"></a>使用 IoT 中心 （.NET） 將檔從設備上載到雲

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

本教程基於[IoT 中心教程中的"發送雲到設備消息](iot-hub-csharp-csharp-c2d.md)"中的代碼，向您展示如何使用 IoT 中心的檔上載功能。 這會說明如何：

* 安全地將 Azure Blob URI 提供給裝置，以便上傳檔案。

* 您可以使用 IoT 中樞檔案上傳通知來觸發在您的應用程式後端中處理此檔案。

通過 IoT 中心教程[將遙測資料從設備發送到 IoT 中心](quickstart-send-telemetry-dotnet.md)快速啟動和[發送雲到設備消息](iot-hub-csharp-csharp-c2d.md)，顯示了 IoT 中心的基本設備到雲和雲到設備消息傳遞功能。 [使用 IoT 中心配置消息路由](tutorial-routing.md)教程介紹了在 Microsoft Azure Blob 存儲中可靠地存放裝置到雲消息的方法。 但是，在某些情況下，您無法輕鬆地將設備發送的資料對應到 IoT 中心接受的相對較小的設備到雲消息中。 例如：

* 包含映像的大型檔案

* 影片

* 取樣高頻率的震動資料

* 某種經前置處理過的資料

這些檔案通常會使用工具 (例如 [Azure Data Factory](../data-factory/introduction.md) 或 [Hadoop](../hdinsight/index.yml) 堆疊) 在雲端中進行批次處理。 當您需要從裝置上傳檔案時，您仍然可以使用安全可靠的 IoT 中樞。

在本教學課程結尾處，您會執行兩個 .NET 主控台應用程式：

* **類比設備**。 此應用程式可以使用 IoT 中樞提供的 SAS URI，將檔案上傳到儲存體。 它是[使用 IoT 中心教程在"將雲到設備消息"](iot-hub-csharp-csharp-c2d.md)中創建的應用的修改版本。

* **閱讀檔上傳通知**。 此應用從 IoT 中心接收檔上載通知。

> [!NOTE]
> IoT 中心通過 Azure IoT 設備 SDK 支援許多設備平臺和語言，包括 C、JAVA、Python 和 JAVAscript。 如需如何將您的裝置連接到 Azure IoT 中樞的逐步指示，請參閱 [Azure IoT 開發人員中心](https://azure.microsoft.com/develop/iot)。

## <a name="prerequisites"></a>Prerequisites

* Visual Studio

* 使用中的 Azure 帳戶。 如果您沒有帳戶，只需幾分鐘即可創建[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。

* 請確定您的防火牆已開啟連接埠 8883。 本文中的設備示例使用 MQTT 協定，該協定通過埠 8883 進行通信。 某些公司和教育網路環境可能會封鎖此連接埠。 如需此問題的詳細資訊和解決方法，請參閱[連線至 IoT 中樞 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>從裝置應用程式上傳檔案

在本節中，您可以修改在[使用 IoT 中心發送雲到設備消息](iot-hub-csharp-csharp-c2d.md)中創建的設備應用，以便從 IoT 中心接收雲到設備消息。

1. 在視覺化工作室解決方案資源管理器中，按右鍵**類比設備**專案，然後選擇"**添加** > **現有專案**"。 查找影像檔並將其包含在專案中。 本教學課程假設影像名稱為 `image.jpg`。

1. 按右鍵圖像，然後選擇 **"屬性**"。 確定 [複製到輸出目錄]**** 是設為 [一律複製]****。

    ![顯示要在其中更新 [複製到輸出目錄] 映像屬性的位置](./media/iot-hub-csharp-csharp-file-upload/image-properties.png)

1. 在 **Program.cs** 檔的頂端，新增下列陳述式：

    ```csharp
    using System.IO;
    ```

1. 將下列方法新增至 Program**** 類別：

    ```csharp
    private static async void SendToBlobAsync()
    {
        string fileName = "image.jpg";
        Console.WriteLine("Uploading file: {0}", fileName);
        var watch = System.Diagnostics.Stopwatch.StartNew();

        using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
        {
            await deviceClient.UploadToBlobAsync(fileName, sourceData);
        }

        watch.Stop();
        Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
    }
    ```

    `UploadToBlobAsync` 方法會取得要上傳之檔案的檔案名稱與資料流來源，然後處理上傳至儲存體的工作。 主控台應用程式會顯示上傳檔案所花費的時間。

1. 在**Main**方法中添加以下行，就在`Console.ReadLine()`： 之前：

    ```csharp
    SendToBlobAsync();
    ```

> [!NOTE]
> 為了簡單起見，本教學課程不會實作任何重試原則。 在生產代碼中，應實現重試策略，如指數回退，如[瞬態故障處理](/azure/architecture/best-practices/transient-faults)中的建議。

## <a name="get-the-iot-hub-connection-string"></a>獲取 IoT 中心連接字串

在本文中，您將創建一個後端服務來接收從在[將遙測資料從設備發送到 IoT 中心](quickstart-send-telemetry-dotnet.md)中創建的 IoT 中心的檔上載通知訊息。 要接收檔上載通知訊息，服務需要**服務連接**許可權。 預設情況下，每個 IoT 中心都使用授予此許可權的名為**服務的**共用訪問策略創建。

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>接收檔案上傳通知

在本節中，您將撰寫 .NET 主控台應用程式，以接收來自 IoT 中樞的檔案上傳通知訊息。

1. 在當前視覺化工作室解決方案中，選擇 **"檔** > **新專案** > **"。** 在**創建新專案中**，選擇**主控台應用程式 （.NET 框架），** 然後選擇 **"下一步**"。

1. 將專案命名為 *ReadFileUploadNotification*。 在 **"解決方案**"下，選擇 **"添加到解決方案**"。 選取 [Create] \(建立\)**** 以建立專案。

    ![在視覺化工作室中配置 ReadFileUpload 通知專案](./media/iot-hub-csharp-csharp-file-upload/read-file-upload-project-configure.png)

1. 在解決方案資源管理器中，按右鍵**ReadFileUpload 通知**專案，然後選擇 **"管理 NuGet 包**"。

1. 在**NuGet 包管理器中**，選擇 **"流覽**"。 搜索並選擇**Microsoft.Azure.Device，** 然後選擇 **"安裝**"。

    此步驟在**ReadFileUpload 通知**專案中下載、安裝並添加對[Azure IoT 服務 SDK NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.Devices/)的引用。

1. 在此專案的**Program.cs**檔中，在檔頂部添加以下語句：

    ```csharp
    using Microsoft.Azure.Devices;
    ```

1. 將下列欄位新增到 **Program** 類別。 將`{iot hub connection string}`預留位置值替換為以前在[獲取 IoT 中心連接字串](#get-the-iot-hub-connection-string)中複製的 IoT 中心連接字串：

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

1. 將下列方法新增至 Program**** 類別：

    ```csharp
    private async static void ReceiveFileUploadNotificationAsync()
    {
        var notificationReceiver = serviceClient.GetFileNotificationReceiver();

        Console.WriteLine("\nReceiving file upload notification from service");
        while (true)
        {
            var fileUploadNotification = await notificationReceiver.ReceiveAsync();
            if (fileUploadNotification == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received file upload notification: {0}", 
              string.Join(", ", fileUploadNotification.BlobName));
            Console.ResetColor();

            await notificationReceiver.CompleteAsync(fileUploadNotification);
        }
    }
    ```

    請注意，此接收模式與用來從裝置應用程式接收雲端到裝置訊息的模式相同。

1. 最後，將以下行添加到**Main**方法：

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>執行應用程式

現在您已經準備好執行應用程式。

1. 在解決方案資源管理器中，按右鍵解決方案，然後選擇 **"設置啟動專案**"。

1. 在 **"常見屬性** > **啟動專案**"中，選擇**多個啟動專案**，然後選擇**ReadFileUpload 通知**和**類比設備的****"啟動**"操作。 選取 [ **確定** ] 以儲存變更。

1. 按 **F5**。 這兩個應用程式應該都會啟動。 您應該會在其中一個主控台應用程式中看到上傳已完成，以及另一個主控台應用程式所收到的上傳通知訊息。 您可以使用 [Azure 入口網站](https://portal.azure.com/)或 Visual Studio 伺服器總管，在您的 Azure 儲存體帳戶中檢查上傳的檔案是否存在。

    ![顯示輸出畫面的螢幕擷取畫面](./media/iot-hub-csharp-csharp-file-upload/run-apps1.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已學到如何使用 IoT 中樞的檔案上傳功能來簡化從裝置上傳檔案。 您可以通過以下文章繼續探索 IoT 中心功能和方案：

* [以程式設計方式建立 IoT 中樞](iot-hub-rm-template-powershell.md)

* [C SDK 簡介](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)

若要進一步探索 IoT 中樞的功能，請參閱︰

* [使用 Azure IoT Edge 將 AI 部署到 Edge 裝置](../iot-edge/tutorial-simulate-device-linux.md)
