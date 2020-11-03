---
title: 使用 ARM 範本發佈 Azure IoT 中樞、儲存體帳戶、路由訊息
description: 使用 ARM 範本發佈 Azure IoT 中樞、儲存體帳戶、路由訊息
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.date: 08/24/2020
ms.author: robinsh
ms.custom: mvc, subject-armqs
ms.openlocfilehash: 1b9c576ce03d808fe6a4d0cac5196dfcd1b73eab
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545475"
---
# <a name="quickstart-deploy-an-azure-iot-hub-and-a-storage-account-using-an-arm-template"></a>快速入門：使用 ARM 範本部署 Azure IoT 中樞和儲存體帳戶

在本快速入門中，您會使用 Azure Resource Manager 範本 (ARM 範本) 建立可將訊息路由至 Azure 儲存體的 IoT 中樞，以及用來保存訊息的儲存體帳戶。 將虛擬 IoT 裝置手動新增至中樞以提交訊息之後，您會在名為 arm-read-write 的應用程式中設定該連線資訊，以將訊息從裝置提交至中樞。 設定中樞之後，傳送至中樞的訊息就會自動路由至儲存體帳戶。 在本快速入門結束時，您可以開啟儲存體帳戶並查看已傳送的訊息。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 範本會在 Azure 入口網站中開啟。

[![部署至 Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-iothub-auto-route-messages%2Fazuredeploy.json)

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。

## <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本名為 `101-iothub-auto-route-messages`，來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-iothub-auto-route-messages)。

:::code language="json" source="~/quickstart-templates/101-iothub-auto-route-messages/azuredeploy.json":::

範本中定義了兩個 Azure 資源：

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft.Devices/IotHubs](/azure/templates/microsoft.devices/iothubs)

## <a name="deploy-the-template-and-run-the-sample-app"></a>部署範本並執行應用程式範例

本節提供部署範本、建立虛擬裝置，以及執行 arm-read-write 應用程式以傳送訊息的步驟。

1. 藉由部署 ARM 範本來建立資源。

    > [!TIP]
    > 選取下方按鈕以開始部署範本。 當其執行時，設定要執行的 arm-read-write 應用程式。

    [![部署至 Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-iothub-auto-route-messages%2Fazuredeploy.json)

1. 下載並解壓縮 [IoT C# 範例](/samples/azure-samples/azure-iot-samples-csharp/azure-iot-samples-for-csharp-net/)。

1. 開啟命令視窗，並移至您將 IoT C# 解壓縮的所在資料夾。 尋找具有 arm-read-write.csproj 檔案的資料夾。 您會在此命令視窗中建立環境變數。 登入 [Azure 入口網站](https://portal.azure.com)以取得金鑰。 選取 [資源群組]，然後選取此快速入門使用的資源群組。

   ![選取資源群組](./media/horizontal-arm-route-messages/01-select-resource-group.png)

1. 您會看到您部署 ARM 範本時所建立的 IoT 中樞和儲存體帳戶。 請等到範本完全部署完成，再繼續下一步。 然後選取您的資源群組，以查看您的資源。

   ![檢視資源群組中的資源](./media/horizontal-arm-route-messages/02-view-resources-in-group.png)

1. 您需要 **中樞名稱** 。 在資源清單中選取中樞。 將 IoT 中樞區段頂端的中樞名稱複製到 Windows 剪貼簿。

   ![複製中樞名稱](./media/horizontal-arm-route-messages/03-copy-hub-name.png)

    取代此命令中標註的中樞名稱，然後在命令視窗中執行此命令：

    ```cmd
    SET IOT_HUB_URI=<hub name goes here>.azure-devices-net;
    ```

   這會看起來像此範例：

   ```cmd
   SET IOT_HUB_URI=ContosoTestHubdlxlud5h.azure-devices-net;
   ```

1. 下一個環境變數是 IoT 裝置金鑰。 從中樞的 [IoT 中樞] 功能表中選取 [IOT 裝置]，將新裝置新增至中樞。

   ![選取 IoT 裝置](./media/horizontal-arm-route-messages/04-select-iot-devices.png)

1. 在畫面右側，選取 [+ 新增] 來新增裝置。

   填入新的裝置名稱。 本快速入門會使用以 **Contoso-Test-Device** 開頭的名稱。 儲存裝置，然後再次開啟該畫面以擷取裝置金鑰。 (當您關閉窗格時，系統就會為您產生金鑰。)選取主要或次要金鑰，並將其複製到 Windows 剪貼簿。 在命令視窗中，設定要執行的命令，然後按 **Enter** 。 此命令看起來應如下所示，但會貼上裝置金鑰：

   ```cmd
   SET IOT_DEVICE_KEY=<device-key-goes-here>
   ```

1. 最後一個環境變數是 **裝置識別碼** 。 在命令視窗中，設定並執行命令。

   ```cms
   SET IOT_DEVICE_ID=<device-id-goes-here>
   ```

   這會看起來像此範例：

   ```cmd
   SET IOT_DEVICE_ID=Contoso-Test-Device
   ```

1. 若要查看您已定義的環境變數，請在命令列上輸入 SET 並按 **Enter** ，然後尋找以 **IoT** 開頭的變數。

   ![查看環境變數](./media/horizontal-arm-route-messages/06-environment-variables.png)

    現在，您已設定環境變數，請從相同的命令視窗執行應用程式。 由於您使用相同視窗，因此當您執行應用程式時，系統會在記憶體中存取變數。

1. 若要執行應用程式，請在命令視窗中輸入下列命令，然後按 **Enter** 。

    `dotnet run arm-read-write`

   應用程式會在將每則訊息傳送至 IoT 中樞時，在主控台上產生並顯示訊息。 該中樞已在 ARM 範本中設定為自動路由。 包含 `level = storage` 文字的訊息會自動路由至儲存體帳戶。 讓應用程式執行 10 到 15 分鐘，然後按 **Enter** 一次或兩次，直到其停止執行為止。

## <a name="review-deployed-resources"></a>檢閱已部署的資源

1. 登入 [Azure 入口網站](https://portal.azure.com)並選取資源群組，然後選取儲存體帳戶。

1. 向下切入至儲存體帳戶，直到您找到檔案為止。

   ![查看儲存體帳戶檔案](./media/horizontal-arm-route-messages/07-see-storage.png)

1. 選取其中一個檔案，然後選取 [下載]，並將檔案下載到您稍後可以找到的位置。 其名稱會是數值，例如 47。 在結尾加上 .txt，然後在檔案上按兩下來開啟檔案。

1. 當您開啟檔案時，每個資料列都是不同的訊息；每則訊息的本文也會經過加密。 其必須按照順序，才能讓您對訊息本文執行查詢。

   ![查看傳送的訊息](./media/horizontal-arm-route-messages/08-messages.png)

   > [!NOTE]
   > 這些訊息會以 UTF-32 和 base64 編碼。 如果您回讀訊息，則必須將其從 base64 和 UTF-32 中解碼，才能以 ASCII 的形式讀取。 如果您有興趣，可以使用路由教學課程中的 ReadOneRowFromFile 方法，從這些訊息檔案中讀取其中一個檔案，並將其解碼成 ASCII。 ReadOneRowFromFile 位於您為本快速入門解壓縮的 IoT C# 範例存放庫中。 以下是該資料夾頂端的路徑： *./iot-hub/Tutorials/Routing/SimulatedDevice/Program.cs。* 將布林值 `readTheFile` 設定為 true，並對磁碟上的檔案路徑進行硬式編碼，這會開啟並轉譯檔案中的第一個資料列。

您已部署 ARM 範本來建立 IoT 中樞和儲存體帳戶，並執行程式以將訊息傳送至中樞。 然後，這些訊息會自動儲存在儲存體帳戶中，您可以在其中檢視訊息。

## <a name="clean-up-resources"></a>清除資源

若要移除在本快速入門期間新增的資源，請登入 [Azure 入口網站](https://portal.azure.com)。 選取 [資源群組]，然後尋找您在本快速入門中使用的資源群組。 選取資源群組，然後選取 [刪除]。 這將會刪除群組中的所有資源。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：建立及部署您的第一個 ARM 範本](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
