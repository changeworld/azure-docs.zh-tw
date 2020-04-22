---
title: 使用 Azure IoT 中樞傳送雲端到裝置訊息 (.NET) | Microsoft Docs
description: 如何使用適用於 .NET 的 Azure IoT SDK，將雲端到裝置訊息從 Azure IoT 中樞傳送至裝置。 您可以修改裝置應用程式，以接收雲端到裝置訊息，也可以修改後端應用程式，以傳送雲端到裝置訊息。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 41c29e55f04f9edf06ba375ad4539e5fb3f82c18
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733424"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>使用 IoT 中樞將訊息從雲端傳送至裝置 (.NET)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT 中樞是一項完全受控的服務，有助於讓數百萬個裝置和一個解決方案後端進行可靠且安全的雙向通訊。 將[遙測數據從設備發送到 IoT 中心](quickstart-send-telemetry-dotnet.md)快速入門顯示如何創建 IoT 中心、在其中預配設備標識以及編寫發送設備到雲端訊息的設備應用。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

這個教學基於[將遙測資料從裝置傳送到 IoT 中心](quickstart-send-telemetry-dotnet.md)。 它展示如何執行以下工作:

* 從您的解決方案後端，透過 IoT 中樞將雲端到裝置訊息傳送給單一裝置。

* 接收裝置上的雲端到裝置訊息。

* 從解決方案後端請求從 IoT 中心發送到設備的消息的傳遞確認 (*回饋*)。

您可以在[使用 IoT 中樞的 D2C 和 C2D 傳訊](iot-hub-devguide-messaging.md)中，找到有關雲端到裝置訊息的詳細資訊。

在本教程結束時,您將運行兩個 .NET 控制台應用。

* **模擬裝置**。 此應用程式連接到IoT中心並接收雲到設備的消息。 此應用程式是在[將遙測數據從設備發送到 IoT 中心](quickstart-send-telemetry-dotnet.md)中創建的應用程式的修訂版本。

* **傳送裝置**。 此應用透過IoT中心向設備應用發送雲到設備的消息,然後接收其傳遞確認。

> [!NOTE]
> IoT 中心透過[Azure IoT 裝置 SDK](iot-hub-devguide-sdks.md)對許多裝置平台和語言(包括 C、Java、Python 和 Javascript)具有 SDK 支援。 如需有關如何將您的裝置與本教學課程中的程式碼連接 (通常是連線至 Azure IoT 中樞) 的逐步指示，請參閱 [Azure IoT 中樞開發人員指南](iot-hub-devguide.md)。
>

## <a name="prerequisites"></a>Prerequisites

* Visual Studio

* 使用中的 Azure 帳戶。 如果您沒有帳戶,只需幾分鐘即可建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。

* 請確定您的防火牆已開啟連接埠 8883。 本文中的設備示例使用 MQTT 協定,該協定通過埠 8883 進行通信。 某些公司和教育網路環境可能會封鎖此連接埠。 如需此問題的詳細資訊和解決方法，請參閱[連線至 IoT 中樞 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

## <a name="receive-messages-in-the-device-app"></a>在裝置應用程式中接收訊息

在本節中,修改您在[將遙測數據從設備發送到 IoT 中心](quickstart-send-telemetry-dotnet.md)以接收來自 IoT 中心的雲端到設備訊息中創建的設備應用。

1. 在 Visual Studio 的 **SimulatedDevice** 專案中，將下列方法新增 [程式]**** 類別。

   ```csharp
    private static async void ReceiveC2dAsync()
    {
        Console.WriteLine("\nReceiving cloud to device messages from service");
        while (true)
        {
            Message receivedMessage = await s_deviceClient.ReceiveAsync();
            if (receivedMessage == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received message: {0}", 
            Encoding.ASCII.GetString(receivedMessage.GetBytes()));
            Console.ResetColor();

            await s_deviceClient.CompleteAsync(receivedMessage);
        }
    }
   ```

1. 將下列方法新增到 **Main** 方法中緊接在 `Console.ReadLine()` 行前面：

   ```csharp
   ReceiveC2dAsync();
   ```

`ReceiveAsync` 方法會以非同步方式，在裝置收到訊息時，傳回收到的訊息。 它在指定超時期間後返回*null。* 在此示例中,使用預設值一分鐘。 當應用程式收到 *null* 時，應繼續等待新訊息。 這項要求是 `if (receivedMessage == null) continue` 行的的原因。

對 `CompleteAsync()` 的呼叫會通知 IoT 中樞，說明已成功處理訊息。 可以安全地從裝置佇列中移除該訊息。 如果因故導致裝置應用程式無法完成訊息處理作業，「IoT 中樞」將會重新傳遞該訊息。 設備應用中的消息處理邏輯必須是*冪等*的,以便多次接收同一消息會產生相同的結果。

應用程式也可以暫時放棄訊息，這會使得「IoT 中樞」將訊息保留在佇列中以供未來取用。 或者，應用程式可以拒絕訊息，這會將訊息從佇列中永久移除。 如需有關雲端到裝置訊息生命週期的詳細資訊，請參閱[使用 IoT 中樞的 D2C 和 C2D 傳訊](iot-hub-devguide-messaging.md)。

   > [!NOTE]
   > 使用 HTTPS 而不使用 MQTT 或 AMQP 作為傳輸時，`ReceiveAsync` 方法會立即傳回。 使用 HTTPS 時，針對雲端到裝置訊息支援的模式是裝置以間歇方式連接而不常檢查訊息 (低於每 25 分鐘一次)。 發出更多 HTTPS 接收會導致「IoT 中樞」對要求進行節流。 如需有關 MQTT、AMQP 和 HTTPS 支援之間的差異，以及「IoT 中樞」節流的詳細資訊，請參閱[使用 IoT 中樞的 D2C 和 C2D 傳訊](iot-hub-devguide-messaging.md)。
   >

## <a name="get-the-iot-hub-connection-string"></a>取得 IoT 中心連接字串

在本文中,您將創建一個後端服務,透過在[將遙測資料從裝置發送到 IoT 中心](quickstart-send-telemetry-dotnet.md)中建立的 IoT 中心發送雲端到裝置的消息。 要發送雲到設備的消息,您的服務需要**服務連接**許可權。 默認情況下,每個 IoT 中心都使用授予此許可權的名為**服務的**共享存取策略創建。

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>傳送雲端到裝置訊息

現在,您編寫一個 .NET 主控台應用,向設備應用發送雲到設備的消息。

1. 在當前可視化工作室解決方案中,選擇 **「檔** > **新專案** > **」。。** 在**建立新項目中**,選擇**主控台應用程式 (.NET 框架),** 然後選擇 **「下一步**」 。

1. 將專案命名為 *SendCloudToDevice*。 在 **「解決方案**」下,選擇 **「添加到解決方案」** 並接受 .NET 架構的最新版本。 選取 [Create] \(建立\)**** 以建立專案。

   ![在可視化工作室中設定新專案](./media/iot-hub-csharp-csharp-c2d/sendcloudtodevice-project-configure.png)

1. 在解決方案資源管理器中,右鍵單擊新解決方案,然後選擇 **「管理 NuGet 包**」。

1. 在 **'管理 NuGet 套件'** 中,選擇 **「瀏覽**」,然後搜尋並選擇**Microsoft.Azure.Device**。 選擇 **「安裝**」 。

   此步驟下載、安裝並添加對[Azure IoT 服務 SDK NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.Devices/)的引用。

1. 在**Program.cs**Program.cs`using`文件的頂部添加以下語句。

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

1. 將下列欄位新增到 **Program** 類別。 將佔位符值替換為以前在[獲取 IoT 中心連接字串](#get-the-iot-hub-connection-string)中複製的 IoT 中心連接字串。

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   ```

1. 將下列方法新增至 **Program** 類別。 將設備名稱設置為在[將遙測數據從設備發送到IoT中心](quickstart-send-telemetry-dotnet.md)中定義裝置時使用的內容。

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync("myFirstDevice", commandMessage);
   }
   ```

   這個方法會將新的雲端到裝置訊息傳送給識別碼為 `myFirstDevice`的裝置。 僅當將此參數從[將遙測數據從設備發送到 IoT 中心](quickstart-send-telemetry-dotnet.md)時,才更改它。

1. 最後，將下列幾行新增到 **Main** 方法中。

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

1. 在解決方案資源管理器中,右鍵單擊解決方案,然後選擇 **「設置啟動專案**」。

1. 在 **「常見屬性** > **啟動專案**」中,選擇**多個啟動專案**,然後為 **「讀取裝置到雲訊息**」、**模擬裝置**以及**發送CloudToDevice**選擇 **「啟動**」操作。 選取 [ **確定** ] 以儲存變更。

1. 按 **F5**。 三個應用程式應該全部都會啟動。 選取 [SendCloudToDevice]**** 視窗，然後按 **Enter**。 您應該會看到裝置應用程式正在接收訊息。

   ![正在接收訊息的應用程式](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

## <a name="receive-delivery-feedback"></a>接收傳遞意見反應

可以請求從 IoT 中心對每個雲端到設備訊息的傳遞(或過期)確認。 這個選項可讓解決方案後端輕鬆地通知重試或補償邏輯。 如需有關雲端到裝置意見反應的詳細資訊，請參閱[使用 IoT 中樞的 D2C 和 C2D 傳訊](iot-hub-devguide-messaging.md)。

在本節中，您會修改 **SendCloudToDevice** 應用程式以要求意見反應，然後從 IoT 中樞接收意見反應。

1. 在 Visual Studio 的 **SendCloudToDevice** 專案中，將下列方法加入至 **Program** 類別。

   ```csharp
   private async static void ReceiveFeedbackAsync()
   {
        var feedbackReceiver = serviceClient.GetFeedbackReceiver();

        Console.WriteLine("\nReceiving c2d feedback from service");
        while (true)
        {
            var feedbackBatch = await feedbackReceiver.ReceiveAsync();
            if (feedbackBatch == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received feedback: {0}",
              string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
            Console.ResetColor();

            await feedbackReceiver.CompleteAsync(feedbackBatch);
        }
    }
    ```

    請注意，此接收模式與用來從裝置應用程式接收雲端到裝置訊息的模式相同。

1. 在**Main**方法中添加以下行`serviceClient = ServiceClient.CreateFromConnectionString(connectionString)`,在之後。

   ``` csharp
   ReceiveFeedbackAsync();
   ```

1. 為了要求雲端到裝置訊息傳遞狀況的意見反應，您必須在 **SendCloudToDeviceMessageAsync** 方法中指定屬性。 在`var commandMessage = new Message(...);`行之後添加以下行。

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

1. 按 **F5**來執行應用程式。 您應該會看到三個應用程式全部都啟動。 選取 [SendCloudToDevice]**** 視窗，然後按 **Enter**。 您應該會看到裝置應用程式正在接收訊息，而幾秒之後，則會看到您的 **SendCloudToDevice** 應用程式正在接收意見反應訊息。

   ![正在接收訊息的應用程式](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> 為簡單起見,本教程不實現任何重試策略。 在生產代碼中,應實現重試策略,如指數回退,如[瞬態故障處理](/azure/architecture/best-practices/transient-faults)中的建議。
>

## <a name="next-steps"></a>後續步驟

在本操作說明中，您已了解如何傳送和接收雲端到裝置的訊息。

若要查看使用 IoT 中樞的完整端對端解決方案範例，請參閱 [Azure IoT 遠端監視解決方案加速器](https://docs.microsoft.com/azure/iot-suite/)。

若要深入了解如何使用 IoT 中樞開發解決方案，請參閱 [IoT 中樞開發人員指南](iot-hub-devguide.md)。
