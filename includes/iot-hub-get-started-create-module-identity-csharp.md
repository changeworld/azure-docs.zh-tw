---
title: 包含檔案
description: 包含檔案
services: iot-hub
author: chrissie926
ms.service: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: menchi
ms.custom: include file
ms.openlocfilehash: a5c1ddd085ae65b9920d73f50f993f4646785a69
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "68883776"
---
## <a name="create-a-module-identity"></a>建立模組身分識別

在本節中，您會建立 .NET 主控台應用程式，以在您中樞的身分識別登錄中建立裝置身分識別和模組身分識別。 裝置或模組無法連線到中樞，除非它在身分識別登錄中具有專案。 如需詳細資訊，請參閱《 [IoT 中樞開發人員指南》](../articles/iot-hub/iot-hub-devguide-identity-registry.md)中的身分識別登錄一節。

當您執行此主控台應用程式時，它會針對裝置和模組產生唯一的識別碼和金鑰。 當您的裝置和模組將裝置到雲端訊息傳送至 IoT 中樞時，您的裝置和模組會使用這些值來識別自己。 識別碼會區分大小寫。

1. 開啟 Visual Studio，然後選取 [建立新專案]。

1. 在 [ **建立新專案**] 中，選取 [ **主控台應用程式 ( .NET Framework) **]。

1. 選取 **[下一步]** 以開啟 [ **設定您的新專案**]。 將專案命名為 *CreateIdentities*，將解決方案命名為 *IoTHubGetStarted*。 確定 .NET Framework 為 4.6.1 或更新版本。

    ![輸入 Visual Studio 解決方案的名稱和架構](./media/iot-hub-get-started-create-module-identity-csharp/configure-createidentities-project.png)

1. 在 Visual Studio 中，開啟 [**工具**  >  **nuget] 封裝管理員**[  >  **管理方案的 nuget 套件**]。 選取 [瀏覽] 索引標籤。

1. 搜尋 **Microsoft Azure. 裝置**。 選取該檔案，然後選取 [ **安裝**]。

    ![安裝 Azure IoT 中樞 .NET service SDK 目前版本](./media/iot-hub-get-started-create-module-identity-csharp/install-service-sdk.png)

1. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Common.Exceptions;
   ```

1. 將下列欄位新增到 **Program** 類別。 將預留位置的值替換為您在上一節中為中樞所建立的 IoT 中樞連接字串。

   ```csharp
   const string connectionString = "<replace_with_iothub_connection_string>";
   const string deviceID = "myFirstDevice";
   const string moduleID = "myFirstModule";
   ```

1. 將以下程式碼新增至 **Main** 類別。

   ```csharp
   static void Main(string[] args)
   {
       AddDeviceAsync().Wait();
       AddModuleAsync().Wait();
   }
   ```

1. 將下列方法新增至 **Program** 類別：

    ```csharp
    private static async Task AddDeviceAsync()
    {
       RegistryManager registryManager = 
         RegistryManager.CreateFromConnectionString(connectionString);
       Device device;

       try
       {
           device = await registryManager.AddDeviceAsync(new Device(deviceID));
       }
       catch (DeviceAlreadyExistsException)
        {
            device = await registryManager.GetDeviceAsync(deviceID);
        }

        Console.WriteLine("Generated device key: {0}", 
          device.Authentication.SymmetricKey.PrimaryKey);
    }

    private static async Task AddModuleAsync()
    {
        RegistryManager registryManager = 
          RegistryManager.CreateFromConnectionString(connectionString);
        Module module;

        try
        {
            module = 
              await registryManager.AddModuleAsync(new Module(deviceID, moduleID));
        }
        catch (ModuleAlreadyExistsException)
        {
            module = await registryManager.GetModuleAsync(deviceID, moduleID);
        }

        Console.WriteLine("Generated module key: {0}", module.Authentication.SymmetricKey.PrimaryKey);
    }
    ```

    `AddDeviceAsync`方法會建立識別碼為**myFirstDevice**的裝置身分識別。 如果該裝置識別碼已經存在身分識別登錄中，則程式碼只會抓取現有的裝置資訊。 接著，應用程式會顯示該身分識別的主要金鑰。 您可以在模擬的裝置應用程式中使用此金鑰來連線到您的中樞。

    `AddModuleAsync`方法會在裝置**myFirstDevice**下建立識別碼為 **>myfirstdevice**的模組身分識別。 如果該模組識別碼已經存在於身分識別登錄中，則程式碼只會抓取現有的模組資訊。 接著，應用程式會顯示該身分識別的主要金鑰。 您可以在模擬的模組應用程式中使用此金鑰來連線到您的中樞。

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. 執行此應用程式，並記下裝置金鑰和模組金鑰。

> [!NOTE]
> IoT 中樞身分識別登錄只會儲存裝置和模組身分識別，以啟用對中樞的安全存取。 身分識別登錄會儲存裝置識別碼和金鑰，以作為安全性認證使用。 身分識別登錄也會儲存每個裝置的已啟用/已停用旗標，以便您用來停用該裝置的存取權。 如果您的應用程式需要儲存其他裝置特定的中繼資料，它應該使用應用程式特定的存放區。 模組身分識別沒有啟用/停用旗標。 如需詳細資訊，請參閱 [IoT 中樞開發人員指南](../articles/iot-hub/iot-hub-devguide-identity-registry.md)。
