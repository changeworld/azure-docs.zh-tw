---
title: 快速入門：尋找可用的會議室-Azure 數位 Twins |Microsoft Docs
description: 在本快速入門中，您會執行兩個 .NET Core 應用程式範例，將模擬的動作和二氧化碳遙測資料傳送至 Azure Digital Twins 中的空間。 我們的目標是要在計算雲端中的處理能力後，從管理 API 尋找有新鮮空氣的空閒會議室。
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc seodec18
ms.date: 01/10/2020
ms.openlocfilehash: 6c9c5df27f4a361e534bac2fe21b2c470f8d0186
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78382214"
---
# <a name="quickstart-find-available-rooms-by-using-azure-digital-twins"></a>快速入門：使用 Azure Digital Twins 尋找空閒會議室

Azure Digital Twins 服務可讓您重新建立實體環境的數位影像。 然後，您可以收到環境事件通知，並自訂關於通知的回應。

本快速入門會使用[一組 .NET 範例](https://github.com/Azure-Samples/digital-twins-samples-csharp)將虛構辦公大樓數位化。 文中會示範如何尋找該大樓內的空閒會議室。 使用 Digital Twins，便可將許多感應器關聯至環境。 您還可以借助於模擬的二氧化碳感應器，來了解空閒會議室的空氣品質是否理想。 其中一個應用程式範例會產生隨機的感應器資料，以協助您將此案例視覺化。

下列影片將摘要說明快速入門的設定：

>[!VIDEO https://www.youtube.com/embed/1izK266tbMI]

## <a name="prerequisites"></a>Prerequisites

1. 如果您沒有 Azure 帳戶，請在開始之前[建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

1. 您在此快速入門中執行的兩個主控台應用程式是使用 C# 所撰寫的。 在開發電腦上安裝 [.NET Core SDK 2.1.403 版或更新版本](https://www.microsoft.com/net/download)。 如果您已安裝 .NET Core SDK，請確認開發電腦上目前的 C# 版本。 在命令提示字元中執行 `dotnet --version`。

1. 下載[範例 C# 專案](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip)。 將 digital-twins-samples-csharp-master.zip 封存檔解壓縮。

## <a name="create-a-digital-twins-instance"></a>建立 Digital Twins 執行個體

遵循這一節中的步驟，在[入口網站](https://portal.azure.com)中建立新的 Digital Twins 執行個體。

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="set-permissions-for-your-app"></a>設定應用程式的權限

本節會向 Azure Active Directory (Azure AD) 註冊應用程式範例，讓此範例可以存取 Digital Twins 執行個體。 如果您已經有一個 Azure AD 應用程式註冊，請將它重複使用於您的範例。 請確定其設定和本節所述相同。

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="build-application"></a>建置應用程式

遵循下列步驟來建置會議室使用情形應用程式。

1. 開啟命令提示字元。 移至 `digital-twins-samples-csharp-master.zip` 檔案解壓縮到的資料夾。
1. 執行 `cd occupancy-quickstart/src`。
1. 執行 `dotnet restore`。
1. 編輯 [appSettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) 以更新下列變數：
    - **ClientId**：輸入 Azure AD 應用程式註冊的應用程式識別碼，其記載於上一節。
    - **Tenant**：輸入 Azure AD 租用戶的目錄識別碼，其同樣記載於上一節。
    - **BaseUrl**：Digital Twins 執行個體的管理 API URL 所用的格式為 `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`。 將此 URL 中的預留位置替換為執行個體在上一節的值。

    儲存更新的檔案。

## <a name="provision-graph"></a>佈建圖形

此步驟會佈建含有下列項目的 Digital Twins 空間圖形：

- 數個空間。
- 一個裝置。
- 兩個感應器。
- 一個自訂函式。
- 一個角色指派。

此空間圖形會使用 [provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml) 檔案來加以佈建。

1. 執行 `dotnet run ProvisionSample`。

    >[!NOTE]
    >裝置登入 Azure CLI 工具可用來對 Azure AD 驗證使用者。 使用者必須輸入指定代碼，才能使用 [Microsoft 登入](https://microsoft.com/devicelogin)頁面來進行驗證。 輸入代碼後，請遵循相關步驟來進行驗證。 當工具執行時，使用者必須進行驗證。

    >[!TIP]
    > 如果您在執行此步驟時出現下列錯誤訊息，請確定所複製的變數是否正確：`EXIT: Unexpected error: The input is not a valid Base-64 string ...`

1. 佈建步驟可能需要進行幾分鐘的時間。 它也會在 Digital Twins 執行個體內佈建 IoT 中樞。 這個過程會一直循環進行，直到 IoT 中樞顯示狀態為 `Running`。

    [![佈建範例 - 狀態=正在執行](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-provision-sample.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-provision-sample.png#lightbox)

1. 執行結束時，請複製裝置的 `ConnectionString` 以便用於裝置模擬器範例。 您只需要複製下圖所述的字串。

    [![複製連接字串](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-connection-string.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-connection-string.png#lightbox)

    >[!TIP]
    > 您可以使用 [Azure Digital Twins Graph Viewer](https://github.com/Azure/azure-digital-twins-graph-viewer) 檢視和修改空間圖形。

保持主控台視窗開啟以供稍後使用。

## <a name="send-sensor-data"></a>傳送感應器資料

遵循下列步驟來建置及執行感應器模擬器裝置應用程式。

1. 開啟新的命令提示字元。 移至您在 `digital-twins-samples-csharp-master` 資料夾中下載的專案。
1. 執行 `cd device-connectivity`。
1. 執行 `dotnet restore`。
1. 編輯 [appsettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) 以使用先前的  **來更新 DeviceConnectionString**`ConnectionString`。 儲存更新的檔案。
1. 執行 `dotnet run` 來開始傳送感應器資料。 資料會傳送至 Digital Twins，如下圖所示。

     [![裝置連線能力](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-device-connectivity.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-device-connectivity.png#lightbox)

1. 讓此模擬器執行，以便您可以使用下一個步驟的動作來並排檢視結果。 此視窗會顯示傳送到 Digital Twins 的模擬感應器資料。 下一個步驟會進行即時查詢，以尋找有新鮮空氣的空閒會議室。

    >[!TIP]
    > 如果您在執行此步驟時出現下列錯誤訊息，請確定所複製的 `DeviceConnectionString` 是否正確：`EXIT: Unexpected error: The input is not a valid Base-64 string ...`

## <a name="find-available-spaces-with-fresh-air"></a>尋找有新鮮空氣的空閒空間

感應器範例會模擬兩個感應器的隨機資料值。 它們分別是動作和二氧化碳。 在範例中，有新鮮空氣的空閒會議室定義是會議室內沒有人。 其另外的定義是二氧化碳濃度低於 1,000 ppm。 如果情況不符，就表示沒有空閒會議室，要不然就是空氣品質不良。

1. 開啟稍早用來執行佈建步驟的命令提示字元。
1. 執行 `dotnet run GetAvailableAndFreshSpaces`。
1. 查看這個命令提示字元和並排顯示的感應器資料命令提示字元。

    感應器資料命令提示字元會每隔五秒將模擬的動作和二氧化碳資料傳送至 Digital Twins。 另一個命令提示字元會即時讀取圖形，以根據隨機的模擬資料來找出有新鮮空氣的空閒會議室。 它會根據最後一次傳送的感應器資料，近乎即時地顯示下列其中一個情況：
   - `Room is available and air is fresh`
   - `Room is not available or air quality is poor`

     [![取得有新鮮空氣的空閒空間](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-get-available.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-get-available.png#lightbox)

若要了解本快速入門所發生的情況以及已呼叫的 API，請使用 [ 中找到的程式碼工作區專案來開啟 ](https://code.visualstudio.com/Download)Visual Studio Code`digital-twins-samples-csharp`。 使用下列命令：

```cmd
<path>\occupancy-quickstart\src>code ..\..\digital-twins-samples.code-workspace
```

教學課程會深入探索此程式碼。 其會告訴您如何修改組態資料，以及呼叫了哪些 API。 如需管理 API 的詳細資訊，請移至 Digital Twins Swagger 頁面：

```URL
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| 名稱 | 更換為 |
| --- | --- |
| YOUR_INSTANCE_NAME | Digital Twins 執行個體的名稱 |
| YOUR_LOCATION | 您的執行個體裝載所在的伺服器區域 |

或者，為求方便，瀏覽至 [Digital Twins Swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger)。

## <a name="clean-up-resources"></a>清除資源

教學課程會詳細說明如何：

- 建置應用程式讓設施管理員提高設施是否有人使用的辨別能力。
- 更有效率地管理大樓。

若要繼續進行教學課程，請勿清除在此快速入門中建立的資源。 如果您不打算繼續，請刪除本快速入門建立的所有資源。

1. 刪除下載存放庫範例時所建立的資料夾。
1. 從 [Azure 入口網站](https://portal.azure.com)左側的功能表中，選取 [所有資源]。 然後，選取 Digital Twins 資源。 在 [所有資源] 窗格的頂端，選取 [刪除]。

    > [!TIP]
    > 如果您先前在刪除 Digital Twins 執行個體時遇到問題，已推出的服務更新中具有修正程式。 請重試刪除執行個體。

## <a name="next-steps"></a>後續步驟

本快速入門使用簡單的案例和範例應用程式，示範如何使用 Digital Twins 來尋找有良好工作條件的會議室。 若要深入分析此案例，請閱讀本教學課程：

>[!div class="nextstepaction"]
>[教學課程：部署 Azure Digital Twins 及設定空間圖形](tutorial-facilities-setup.md)
