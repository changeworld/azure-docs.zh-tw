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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "68883776"
---
## <a name="create-a-module-identity"></a>建立模組身分識別

在本節中，您將創建一個 .NET 主控台應用，在中心中的標識註冊表中創建設備標識和模組標識。 設備或模組無法連接到集線器，除非它在標識註冊表中具有條目。 有關詳細資訊，請參閱[IoT 中心開發人員指南的標識註冊表部分](../articles/iot-hub/iot-hub-devguide-identity-registry.md)。

當您執行此主控台應用程式時，它會針對裝置和模組產生唯一的識別碼和金鑰。 您的設備和模組在將設備到雲的消息發送到 IoT 中心時使用這些值來標識自己。 識別碼會區分大小寫。

1. 打開視覺化工作室，然後選擇 **"創建新專案**"。

1. 在**創建新專案中**，選擇**主控台應用程式 （.NET 框架）。**

1. 選擇 **"下一步**"以打開**配置新專案**。 將專案命名為 *CreateIdentities*，將解決方案命名為 *IoTHubGetStarted*。 確定 .NET Framework 為 4.6.1 或更新版本。

    ![輸入視覺化工作室解決方案的名稱和框架](./media/iot-hub-get-started-create-module-identity-csharp/configure-createidentities-project.png)

1. 在視覺化工作室中，打開**工具** > **NuGet 包管理器** > **管理 NuGet 包以進行解決方案**。 選取 [瀏覽]**** 索引標籤。

1. 搜索**微軟.Azure.設備**。 選擇它，然後選擇 **"安裝**"。

    ![安裝 Azure IoT 中心 .NET 服務 SDK 當前版本](./media/iot-hub-get-started-create-module-identity-csharp/install-service-sdk.png)

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

    該方法`AddDeviceAsync`創建具有 ID **myFirstDevice**的設備標識。 如果標識註冊表中已存在該裝置識別碼，則代碼只需檢索現有設備資訊即可。 接著，應用程式會顯示該身分識別的主要金鑰。 在類比設備應用中使用此金鑰連接到集線器。

    該方法`AddModuleAsync`在設備**myFirstDevice**下創建 ID **myFirstModule**的模組標識。 如果標識註冊表中已存在該模組 ID，則代碼只需檢索現有模組資訊即可。 接著，應用程式會顯示該身分識別的主要金鑰。 在類比模組應用中使用此金鑰連接到集線器。

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. 運行此應用程式，並記下設備金鑰和模組金鑰。

> [!NOTE]
> IoT 中心標識註冊表僅存放裝置和模組標識，以啟用對中心的安全訪問。 身分識別登錄會儲存裝置識別碼和金鑰，以作為安全性認證使用。 身分識別登錄也會儲存每個裝置的已啟用/已停用旗標，以便您用來停用該裝置的存取權。 如果應用需要存儲其他特定于設備的中繼資料，則應使用特定于應用程式的存儲。 模組身分識別沒有啟用/停用旗標。 如需詳細資訊，請參閱 [IoT 中樞開發人員指南](../articles/iot-hub/iot-hub-devguide-identity-registry.md)。
