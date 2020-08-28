---
title: 'Azure IoT 中樞模組身分識別 & 模組對應項 (入口網站和 .NET) '
description: 了解如何使用入口網站和 .NET，建立模組身分識別及更新模組對應項。
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: amqp, devx-track-csharp
ms.openlocfilehash: a10c8b21da1e4dc08a186ccb6ba6528b7a655943
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89022066"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>透過入口網站和 .NET 裝置開始使用 IoT 中樞模組身分識別和模組對應項

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [模組身分識別與模組對應項](iot-hub-devguide-module-twins.md)類似於 Azure IoT 中樞裝置身分識別與裝置對應項，但提供更精細的細微性。 雖然 Azure IoT 中樞裝置身分識別和裝置對應項可讓後端應用程式設定裝置，並提供裝置條件的可見度，但模組身分識別和模組對應項會為裝置的個別元件提供這些功能。 在具有多個元件（例如作業系統型裝置或固件裝置）的可用裝置上，模組身分識別和模組 twins 允許針對每個元件進行隔離的設定和條件。
>

在本教學課程中，您將了解：

* 如何在入口網站中建立模組身分識別。

* 如何使用 .NET 裝置 SDK 從您的裝置更新模組對應項。

> [!NOTE]
> 如需您可以用來建立這兩個應用程式以在裝置和您的解決方案後端上執行的 Azure IoT Sdk 詳細資訊，請參閱 [Azure Iot sdk](iot-hub-devguide-sdks.md)。
>

## <a name="prerequisites"></a>必要條件

* Visual Studio。

* 使用中的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立 [免費帳戶](https://azure.microsoft.com/pricing/free-trial/) 。

## <a name="create-a-hub"></a>建立中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-hub"></a>在中樞註冊新裝置

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>在入口網站中建立模組身分識別

在一個裝置身分識別內，您可以建立最多 20 個模組身分識別。 若要新增身分識別，請遵循下列步驟：

1. 針對您在上一節中建立的裝置，選擇 [ **新增模組身分識別** ] 來建立您的第一個模組身分識別。

1. 輸入名稱 *>myfirstdevice*。 儲存您的模組身分識別。

    ![新增模組身分識別](./media/iot-hub-portal-csharp-module-twin-getstarted/add-module-identity.png)

    新的模組身分識別會出現在畫面底部。 請選取它以查看模組身分識別詳細資料。

    ![請參閱模組身分識別詳細資料](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

儲存 **連接字串-主要金鑰**。 您將在下一節中使用它來設定裝置上的模組。

## <a name="update-the-module-twin-using-net-device-sdk"></a>使用 .NET 裝置 SDK 更新模組對應項

您已成功在 IoT 中樞中建立模組識別身分。 讓我們嘗試從您的模擬裝置與雲端通訊。 一旦建立模組身分識別，模組對應項會隱含地建立於 IoT 中樞中。 在這一節中，您會在模擬裝置上建立 .NET 主控台應用程式，以便更新模組對應項報告的屬性。

### <a name="create-a-visual-studio-project"></a>建立 Visual Studio 專案

若要建立可更新模組對應項報告屬性的應用程式，請遵循下列步驟：

1. 在 Visual Studio 中，選取 [ **建立新專案**]，然後選擇 [ **主控台應用程式] ( .NET Framework) **，然後選取 **[下一步]**。

1. 在 [ **設定您的新專案**] 中，輸入 *UpdateModuleTwinReportedProperties* 做為 **專案名稱**。 選取 [建立]  繼續作業。

    ![設定您是 visual studio 專案](./media/iot-hub-portal-csharp-module-twin-getstarted/configure-twins-project.png)

### <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>安裝最新的 Azure IoT Hub .NET 裝置 SDK

模組身分識別與模組對應項都處於公開預覽階段。 它僅適用于 IoT 中樞發行前版本裝置 Sdk。 若要安裝它，請遵循下列步驟：

1. 在 Visual Studio 中，開啟 [**工具**  >  **nuget] 封裝管理員**[  >  **管理方案的 nuget 套件**]。

1. 選取 **[流覽]**，然後選取 [ **包含發行**前版本]。 搜尋 *Microsoft Azure. 用戶端*。 選取最新版本並安裝。

    ![安裝 Azure IoT 中樞 .NET 服務 SDK 預覽](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

    您現在可以存取所有的模組功能。

### <a name="get-your-module-connection-string"></a>取得模組連接字串

您需要主控台應用程式的模組連接字串。 請遵循這些步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 流覽至您的 IoT 中樞，然後選取 [ **Iot 裝置**]。 開啟 **myFirstDevice** ，您會看到 **>myfirstdevice** 已成功建立。

1. 選取 [**模組**身分識別] 下的 [ **>myfirstdevice** ]。 在 [ **模組身分識別詳細資料**] 中，複製 ** (主鍵) 的連接字串 **。

    ![Azure 入口網站模組詳細資料](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

### <a name="create-updatemoduletwinreportedproperties-console-app"></a>建立 UpdateModuleTwinReportedProperties 主控台應用程式

若要建立您的應用程式，請遵循下列步驟：

1. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：

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

4. 最後，以下列程式碼取代 **Main** 方法：

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
  
  您可以使用 **F5**來建立並執行此應用程式。

此程式碼範例顯示如何擷取模組對應項，並使用 AMQP 通訊協定更新報告的屬性。 在公開預覽中，我們只支援 AMQP 進行模組對應項作業。

## <a name="next-steps"></a>後續步驟

若要繼續開始使用 IoT 中樞並瀏覽其他 IoT 案例，請參閱︰

* [透過 .NET 備份和 .NET 裝置開始使用 IoT 中樞模組身分識別和模組對應項](iot-hub-csharp-csharp-module-twin-getstarted.md)

* [開始使用 IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
