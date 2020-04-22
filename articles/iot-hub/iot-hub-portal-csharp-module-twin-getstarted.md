---
title: Azure IoT 中心模組標識&模組孿生(門戶和 .NET)
description: 了解如何使用入口網站和 .NET，建立模組身分識別及更新模組對應項。
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: amqp
ms.openlocfilehash: a3258de2ed7269ab50e6feca3c421d55de5a9d91
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759775"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>透過入口網站和 .NET 裝置開始使用 IoT 中樞模組身分識別和模組對應項

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [模組身分識別與模組對應項](iot-hub-devguide-module-twins.md)類似於 Azure IoT 中樞裝置身分識別與裝置對應項，但提供更精細的細微性。 當 Azure IoT 中心設備識別和設備孿生使後端應用程式能夠配置設備並提供設備條件的可見性時,模組標識和模組孿生為設備的單個元件提供這些功能。 在具有多個元件(如基於作業系統的設備或固件設備、模組標識和模組孿生)的功能設備上,允許為每個元件提供隔離的配置和條件。
>

在本教學課程中，您將了解：

* 如何在入口網站中建立模組身分識別。

* 如何使用 .NET 設備 SDK 從設備更新模組孿生。

> [!NOTE]
> 有關可用於生成在裝置和解決方案後端執行的 Azure IoT SDK 的資訊,請參閱[Azure IoT SDK](iot-hub-devguide-sdks.md)。
>

## <a name="prerequisites"></a>Prerequisites

* Visual Studio。

* 使用中的 Azure 帳戶。 如果您沒有帳戶,只需幾分鐘即可建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="create-a-hub"></a>建立中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-hub"></a>在集線器中註冊新裝置

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>在入口網站中建立模組身分識別

在一個裝置身分識別內，您可以建立最多 20 個模組身分識別。 要添加標識,請按照以下步驟操作:

1. 對於在上一節中創建的設備,請選擇 **「添加模組識別**」以創建第一個模塊標識。

1. 輸入名稱*myFirstModule*。 保存模塊標識。

    ![新增模組識別](./media/iot-hub-portal-csharp-module-twin-getstarted/add-module-identity.png)

    新的模組標識將顯示在螢幕底部。 選擇它以查看模塊標識詳細資訊。

    ![檢視模組識別詳細資訊](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

儲存**連線字串 - 主鍵**。 在下一節中使用它來在設備上設置模組。

## <a name="update-the-module-twin-using-net-device-sdk"></a>使用 .NET 裝置 SDK 更新模組對應項

您已成功在 IoT 中樞中建立模組識別身分。 讓我們嘗試從您的模擬裝置與雲端通訊。 一旦建立模組身分識別，模組對應項會隱含地建立於 IoT 中樞中。 在這一節中，您會在模擬裝置上建立 .NET 主控台應用程式，以便更新模組對應項報告的屬性。

### <a name="create-a-visual-studio-project"></a>建立 Visual Studio 專案

要創建更新模組孿生報告屬性的應用,請按照以下步驟操作:

1. 在可視化工作室中,選擇 **「創建新專案**」,然後選擇**主控台應用程式 (.NET 框架),** 然後選擇 **「下一步**」。

1. 在 **「設定新項目」** 中,輸入*更新模組Twin報告屬性*作為**專案名稱**。 選取 [建立] **** 繼續作業。

    ![設定您是視覺化工作室專案](./media/iot-hub-portal-csharp-module-twin-getstarted/configure-twins-project.png)

### <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>安裝最新的 Azure IoT Hub .NET 裝置 SDK

模組身分識別與模組對應項都處於公開預覽階段。 它僅在 IoT 中心預發佈設備 SDK 中可用。 要安裝它,請按照以下步驟操作:

1. 在視覺化工作室中,開啟**工具** > **NuGet 套件管理員** > **管理 NuGet 套件以進行解決方案**。

1. 選擇 **"瀏覽**",然後選擇 **"包括預發行**"。 搜尋*微軟.Azure.Devices.用戶端*。 選取最新版本並安裝。

    ![安裝 Azure IoT 中心 .NET 服務 SDK 預覽](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

    您現在可以存取所有的模組功能。

### <a name="get-your-module-connection-string"></a>取得模組連接字串

需要控制台應用的模組連接字串。 請遵循下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 導覽到 IoT 中與**IoT 裝置**。 打開**myFirstDevice,** 您看到**我的第一個模組**已成功創建。

1. 在**模組識別**下選擇**我的第一個模組**。 在**模組識別詳細資訊**中,複製**連接字串(主鍵)。**

    ![Azure 入口網站模組詳細資料](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

### <a name="create-updatemoduletwinreportedproperties-console-app"></a>建立 UpdateModuleTwinReportedProperties 主控台應用程式

要建立應用,請按照以下步驟操作:

1. 在**Program.cs**Program.cs`using`檔案 的頂部加入以下語句:

  ```csharp
  using Microsoft.Azure.Devices.Client;
  using Microsoft.Azure.Devices.Shared;
  using Newtonsoft.Json;
  ```

2. 將下列欄位新增到 **Program** 類別。 使用模組連接字串取代預留位置值。

  ```csharp
  private const string ModuleConnectionString = "<Your module connection string>";
  private static ModuleClient Client = null;
  ```

3. 將下列方法 **OnDesiredPropertyChanged** 新增至 **Program** 類別：

  ```csharp
  private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
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

4. 最後,將**Main**方法取代為以下代碼:

  ```csharp
  static void Main(string[] args)
  {
      Microsoft.Azure.Devices.Client.TransportType transport = Microsoft.Azure.Devices.Client.TransportType.Amqp;
  
      try
      {
          Client = ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
          Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
          Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();
  
          Console.WriteLine("Retrieving twin");
          var twinTask = Client.GetTwinAsync();
          twinTask.Wait();
          var twin = twinTask.Result;
          Console.WriteLine(JsonConvert.SerializeObject(twin));
  
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
      Console.ReadKey();
      Client.CloseAsync().Wait();
  }
  
  private static void ConnectionStatusChangeHandler(ConnectionStatus status, ConnectionStatusChangeReason reason)
  {
      Console.WriteLine($"Status {status} changed: {reason}");
  }
  ```
  
  您可以使用**F5**建構與執行此應用程式。

此程式碼範例顯示如何擷取模組對應項，並使用 AMQP 通訊協定更新報告的屬性。 在公開預覽中，我們只支援 AMQP 進行模組對應項作業。

## <a name="next-steps"></a>後續步驟

若要繼續開始使用 IoT 中樞並瀏覽其他 IoT 案例，請參閱︰

* [透過 .NET 備份和 .NET 裝置開始使用 IoT 中樞模組身分識別和模組對應項](iot-hub-csharp-csharp-module-twin-getstarted.md)

* [開始使用 IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
