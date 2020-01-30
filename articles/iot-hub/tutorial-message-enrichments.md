---
title: 教學課程-使用 Azure IoT 中樞 message 擴充
description: 示範如何針對 Azure IoT 中樞訊息使用 message 擴充的教學課程
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: robinsh
ms.openlocfilehash: 2c2ac5d3de37a1a89ebd63b89666f164444e0a63
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773785"
---
# <a name="tutorial-use-azure-iot-hub-message-enrichments"></a>教學課程：使用 Azure IoT 中樞 message 擴充

*Message 擴充*描述在訊息傳送至指定的端點之前，Azure IoT 中樞使用其他資訊來*戳記*訊息的能力。 使用 message 擴充的其中一個原因是要包含可用於簡化下游處理的資料。 例如，使用裝置對應項標記來擴充裝置遙測訊息，可以降低客戶的負載，讓裝置對應項 API 呼叫此資訊。 如需詳細資訊，請參閱[message 擴充的總覽](iot-hub-message-enrichments-overview.md)。

在本教學課程中，您會看到兩種建立和設定測試 IoT 中樞之訊息擴充所需資源的方式。 這些資源包括一個具有兩個儲存體容器的儲存體帳戶。 一個容器會保存擴充的訊息，另一個容器則會保留原始訊息。 也包含 IoT 中樞來接收訊息，並根據是否已擴充，將它們路由傳送至適當的儲存體容器。

* 第一種方法是使用 Azure CLI 來建立資源，並設定訊息路由。 然後，您可以使用[Azure 入口網站](https://portal.azure.com)手動定義擴充。

* 第二種方法是使用 Azure Resource Manager 範本來建立訊息路由和訊息擴充的資源*和*設定。

訊息路由和訊息擴充的設定完成後，您可以使用應用程式將訊息傳送至 IoT 中樞。 然後，中樞會將它們路由傳送至這兩個儲存體容器。 只有針對擴充**的儲存體容器**傳送至端點的訊息才會擴充。

以下是完成本教學課程所執行的工作：

**使用 IoT 中樞 message 擴充**
> [!div class="checklist"]
> * 第一個方法：使用 Azure CLI 建立資源及設定訊息路由。 使用[Azure 入口網站](https://portal.azure.com)手動設定訊息擴充。
> * 第二種方法：使用 Resource Manager 範本來建立資源及設定訊息路由和訊息擴充。 
> * 執行模擬 IoT 裝置的應用程式，將訊息傳送至中樞。
> * 查看結果，並確認訊息擴充如預期般運作。

## <a name="prerequisites"></a>必要條件

* 您必須擁有 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 安裝 [Visual Studio](https://www.visualstudio.com/)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-iot-c-samples-repository"></a>取出 IoT C#範例存放庫

從 GitHub[下載C# IoT 範例](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)，並將其解壓縮。 此存放庫中有數個應用程式、腳本和 Resource Manager 範本。 本教學課程所要使用的內容如下：

* 針對手動方法，有一個用來建立資源的 CLI 腳本。 此腳本位於/azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli. azcli。 此腳本會建立資源，並設定訊息路由。 執行此腳本之後，請使用[Azure 入口網站](https://portal.azure.com)手動建立訊息擴充。
* 針對自動化方法，有一個 Azure Resource Manager 範本。 此範本為/azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/template_msgenrichments. json。 此範本會建立資源、設定訊息路由，然後設定訊息擴充。
* 您使用的第三個應用程式是裝置模擬應用程式，您可以用來將訊息傳送至 IoT 中樞並測試訊息擴充。

## <a name="manually-set-up-and-configure-by-using-the-azure-cli"></a>使用 Azure CLI 以手動方式安裝和設定

除了建立必要的資源之外，Azure CLI 腳本也會將兩個路由設定為個別儲存體容器的端點。 如需有關如何設定訊息路由的詳細資訊，請參閱[路由教學](tutorial-routing.md)課程。 設定資源之後，請使用[Azure 入口網站](https://portal.azure.com)來為每個端點設定訊息擴充。 然後繼續進行測試步驟。

> [!NOTE]
> 所有訊息都會路由傳送至這兩個端點，但只有使用已設定之 message 擴充的訊息才會擴充。
>

您可以使用下列腳本，也可以在所下載存放庫的/resources 資料夾中開啟腳本。 腳本會執行下列步驟：

* 建立 IoT 中樞。
* 建立儲存體帳戶。
* 在儲存體帳戶中建立兩個容器。 一個容器適用于擴充的訊息，另一個容器適用于未擴充的訊息。
* 設定兩個不同儲存體帳戶的路由：
    * 建立每個儲存體帳戶容器的端點。
    * 建立每個儲存體帳戶容器端點的路由。

有數個資源名稱必須是全域唯一的，例如 IoT 中樞名稱和儲存體帳戶名稱。 為了讓腳本更容易執行，這些資源名稱會附加一個隨機的英數位元值，稱為*randomValue*。 隨機值會在腳本頂端產生一次。 它會在整個腳本中視需要附加至資源名稱。 如果您不想要將該值設為隨機值，可以將它設定為空字串或特定值。

如果您尚未這麼做，請開啟 [Azure [Cloud Shell] 視窗](https://shell.azure.com)，並確定它已設定為 [Bash]。 在解壓縮的存放庫中開啟腳本，選取 Ctrl + A 來選取所有檔案，然後選取 Ctrl + C 加以複製。 或者，您可以複製下列 CLI 腳本，或直接在 Cloud Shell 中開啟它。 在 [Cloud Shell] 視窗中，以滑鼠右鍵按一下命令列，然後選取 [**貼**上]，將腳本貼上。 腳本會一次執行一個語句。 在腳本停止執行之後，請選取**Enter**以確定它會執行最後一個命令。 下列程式碼區塊會顯示所使用的腳本，並提供說明其所執行作業的批註。

以下是腳本所建立的資源。 擴充*表示資源*適用于具有擴充的訊息。 *原始*表示資源適用于未擴充的訊息。

| 名稱 | 值 |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| 容器名稱 | 原始  |
| 容器名稱 | 豐富  |
| IoT 裝置名稱 | Contoso-測試-裝置 |
| IoT 中樞名稱 | ContosoTestHubMsgEn |
| 儲存體帳戶名稱 | contosostorage |
| 端點名稱1 | ContosoStorageEndpointOriginal |
| 端點名稱2 | ContosoStorageEndpointEnriched|
| 路由名稱1 | ContosoStorageRouteOriginal |
| 路由名稱2 | ContosoStorageRouteEnriched |

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing queries.
subscriptionID=$(az account show --query id -o tsv)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity.
az extension add --name azure-cli-iot-ext

# Set the values for the resource names that
#   don't have to be globally unique.
location=westus2
resourceGroup=ContosoResourcesMsgEn
containerName1=original
containerName2=enriched
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique,
#   so add a random value to the end.
iotHubName=ContosoTestHubMsgEn$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# You need a storage account that will have two containers
#   -- one for the original messages and
#   one for the enriched messages.
# The storage account name must be globally unique,
#   so add a random value to the end.
storageAccountName=contosostorage$randomValue
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key.
#    You need this to create the containers.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the containers in the storage account.
az storage container create --name $containerName1 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

az storage container create --name $containerName2 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
# If you are using Cloud Shell, you can scroll the window back up to retrieve this value.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

##### ROUTING FOR STORAGE #####

# You're going to have two routes and two endpoints.
# One route points to the first container ("original") in the storage account
#   and includes the original messages.
# The other points to the second container ("enriched") in the same storage account
#   and includes the enriched versions of the messages.

endpointType="azurestoragecontainer"
endpointName1="ContosoStorageEndpointOriginal"
endpointName2="ContosoStorageEndpointEnriched"
routeName1="ContosoStorageRouteOriginal"
routeName2="ContosoStorageRouteEnriched"

# for both endpoints, retrieve the messages going to storage
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)

# Create the routing endpoints and routes.
# Set the encoding format to either avro or json.

# This is the endpoint for the first container, for endpoint messages that are not enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName1 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName1 \
  --resource-group $resourceGroup \
  --encoding json

# This is the endpoint for the second container, for endpoint messages that are enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName2 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName2 \
  --resource-group $resourceGroup \
  --encoding json

# This is the route for messages that are not enriched.
# Create the route for the first storage endpoint.
az iot hub route create \
  --name $routeName1 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName1 \
  --enabled \
  --condition $condition

# This is the route for messages that are enriched.
# Create the route for the second storage endpoint.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

此時，所有資源都已設定完成，並已設定訊息路由。 您可以在入口網站中查看訊息路由設定，並針對傳送至**擴充儲存體容器的訊息**設定訊息擴充。

### <a name="manually-configure-the-message-enrichments-by-using-the-azure-portal"></a>使用 Azure 入口網站手動設定訊息擴充

1. 選取 [**資源群組**]，移至您的 IoT 中樞。 然後選取在此教學課程中設定的資源群組（**ContosoResourcesMsgEn**）。 在清單中尋找 IoT 中樞並加以選取。 選取 IoT 中樞的**訊息路由**。

   ![選取訊息路由](./media/tutorial-message-enrichments/select-iot-hub.png)

   [訊息路由] 窗格有三個標示為**路由**、**自訂端點**和擴充**訊息**的索引標籤。 流覽前兩個索引標籤，以查看腳本所設定的設定。 使用第三個索引標籤來新增訊息擴充。 讓我們**針對稱為「** 擴充」的儲存體容器，擴充前往端點的訊息。 填寫 [名稱] 和 [值]，然後從下拉式清單中選取端點**ContosoStorageEndpointEnriched** 。 以下範例說明如何設定擴充，以將 IoT 中樞名稱新增至訊息：

   ![新增第一個擴充](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. 將這些值新增至 ContosoStorageEndpointEnriched 端點的清單。

   | 索引鍵 | 值 | 端點（下拉式清單） |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | DeviceLocation | $twin 標記。位置 | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |Id | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > 如果您的裝置沒有對應項，您在這裡放入的值將會被標記為 [訊息] 擴充中值的字串。 若要查看裝置對應項資訊，請在入口網站中移至您的中樞，然後選取 [ **IoT 裝置**]。 選取您的裝置，然後選取頁面頂端的 [**裝置**對應項]。
   >
   > 您可以編輯對應項資訊來新增標籤（例如 location），並將它設定為特定值。 如需詳細資訊，請參閱[了解和使用 Azure IoT 中樞的裝置對應項](iot-hub-devguide-device-twins.md)。

3. 當您完成時，您的窗格看起來應該如下圖所示：

   ![已新增所有擴充的資料表](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. 選取 **[** 套用] 以儲存變更。 跳至[測試訊息擴充](#test-message-enrichments)一節。

## <a name="create-and-configure-by-using-a-resource-manager-template"></a>使用 Resource Manager 範本建立和設定
您可以使用 Resource Manager 範本來建立和設定資源、訊息路由和訊息擴充。

1. 登入 Azure 入口網站。 選取 [ **+ 建立資源**] 以顯示搜尋方塊。 輸入*範本部署*，然後進行搜尋。 在結果窗格中，選取 [**範本部署（使用自訂範本部署）** ]。

   ![Azure 入口網站中的範本部署](./media/tutorial-message-enrichments/template-select-deployment.png)

1. 在 [**範本部署**] 窗格中選取 [**建立**]。

1. 在 [**自訂部署**] 窗格中，選取 **[在編輯器中建立您自己的範本**]。

1. 在 [**編輯範本**] 窗格中，選取 [**載入**檔案]。 Windows Explorer 隨即出現。 在 **/iot-hub/Tutorials/Routing/SimulatedDevice/resources**中解壓縮的存放庫檔案中找出**template_messageenrichments 的 json**檔案。 

   ![從本機電腦選取範本](./media/tutorial-message-enrichments/template-select.png)

1. 選取 [**開啟**]，從本機電腦載入範本檔案。 它會載入並出現在 [編輯] 窗格中。

   此範本設定為使用全域唯一的 IoT 中樞名稱和儲存體帳戶名稱，方法是在預設名稱的結尾加上隨機值，讓您可以使用範本，而不需要對它進行任何變更。

   以下是載入範本所建立的資源。 擴充**表示資源**適用于具有擴充的訊息。 **原始**表示資源適用于未擴充的訊息。 這些是 Azure CLI 腳本中使用的相同值。

   | 名稱 | 值 |
   |-----|-----|
   | resourceGroup | ContosoResourcesMsgEn |
   | 容器名稱 | 原始  |
   | 容器名稱 | 豐富  |
   | IoT 裝置名稱 | Contoso-測試-裝置 |
   | IoT 中樞名稱 | ContosoTestHubMsgEn |
   | 儲存體帳戶名稱 | contosostorage |
   | 端點名稱1 | ContosoStorageEndpointOriginal |
   | 端點名稱2 | ContosoStorageEndpointEnriched|
   | 路由名稱1 | ContosoStorageRouteOriginal |
   | 路由名稱2 | ContosoStorageRouteEnriched |

1. 選取 [儲存]。 [**自訂部署**] 窗格隨即出現，並顯示範本所使用的所有參數。 唯一需要設定的欄位是 [**資源群組**]。 請建立一個新的，或從下拉式清單中選取一個。

   以下是 [**自訂部署**] 窗格的上半部。 您可以看到填入資源群組的位置。

   ![自訂部署窗格的上半部](./media/tutorial-message-enrichments/template-deployment-top.png)

1. 以下是 [**自訂部署**] 窗格的下半部。 您可以看到其餘的參數和條款及條件。 

   ![自訂部署窗格的下半部](./media/tutorial-message-enrichments/template-deployment-bottom.png)

1. 選取此核取方塊以同意條款及條件。 然後選取 [**購買**] 以繼續進行範本部署。

1. 等待範本完全部署完成。 選取畫面頂端的鐘圖示來檢查進度。 完成後，請繼續進行[測試訊息擴充](#test-message-enrichments)一節。

## <a name="test-message-enrichments"></a>測試訊息擴充

若要查看 message 擴充，請選取 [**資源群組**]。 然後選取您在本教學課程中使用的資源群組。 從資源清單中選取 [IoT 中樞]，然後移至 [**訊息**]。 訊息路由設定和設定的擴充隨即出現。

既然已針對端點設定了 message 擴充，請執行模擬裝置應用程式，將訊息傳送至 IoT 中樞。 中樞已設定完成下列工作的設定：

* 路由至儲存體端點 ContosoStorageEndpointOriginal 的訊息將不會擴充，且會儲存在儲存體容器 `original`。

* 路由傳送至儲存體端點 ContosoStorageEndpointEnriched 的訊息將會擴充並儲存在儲存體容器 `enriched`。

模擬裝置應用程式是解壓縮下載中的其中一個應用程式。 應用程式會在[路由教學](tutorial-routing.md)課程中針對每個不同的訊息路由方法傳送訊息，其中包含 Azure 儲存體。

按兩下方案檔**IoT_SimulatedDevice .sln** ，在 Visual Studio 中開啟程式碼，然後開啟**Program.cs**。 以 IoT 中樞名稱取代標記 `{your hub name}`。 IoT 中樞主機名稱的格式為 **{您的中樞名稱}. azure-devices.net**。 在本教學課程中，中樞主機名稱是 ContosoTestHubMsgEn.azure-devices.net。 接下來，請取代您稍早在執行腳本時儲存的裝置金鑰，以建立標記 `{your device key}`的資源。

如果您沒有裝置金鑰，您可以從入口網站取得它。 登入之後，請移至 [**資源群組**]，選取您的資源群組，然後選取您的 IoT 中樞。 查看測試裝置的 [ **IoT 裝置**]，然後選取您的裝置。 選取 [**主要金鑰**] 旁邊的複製圖示，將它複製到剪貼簿。

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

### <a name="run-and-test"></a>執行和測試

執行主控台應用程式幾分鐘。 正在傳送的訊息會顯示在應用程式的主控台畫面上。

應用程式每一秒就會將最新裝置到雲端的訊息傳送至 IoT 中樞。 訊息包含 JSON 序列化的物件與裝置識別碼、溫度、溼度和訊息層級 (預設位置為 `normal`)。 它會隨機指派一層 `critical` 或 `storage`，這會導致訊息路由傳送至儲存體帳戶或預設端點。 傳送至儲存體帳戶中**擴充容器的**訊息將會擴充。

傳送數個儲存體訊息之後，請查看資料。

1. 選取 [資源群組]。 尋找您的資源群組， **ContosoResourcesMsgEn**，然後選取它。

2. 選取您的儲存體帳戶，也就是**contosostorage**。 然後，在左窗格中選取 [**儲存體總管（預覽）** ]。

   ![選取儲存體總管](./media/tutorial-message-enrichments/select-storage-explorer.png)

   選取 [ **BLOB 容器**]，以查看可使用的兩個容器。

   ![查看儲存體帳戶中的容器](./media/tutorial-message-enrichments/show-blob-containers.png)

名為「擴充」的容器中的訊息會在訊息**中包含訊息**擴充。 容器中的訊息（稱為「**原始**」）具有未經擴充的原始訊息。 向下切入到其中一個容器，直到到達底部，並開啟最新的訊息檔案。 然後對另一個容器執行相同動作，以確認該容器中的訊息未新增任何擴充。

當您查看已擴充的訊息時，您應該會看到「我的 IoT 中樞」，其中包含中樞名稱和位置和客戶識別碼，如下所示：

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

以下是 unenriched 訊息。 請注意，[我的 IoT 中樞]、[devicelocation] 和 [customerID] 不會顯示在這裡，因為擴充會新增這些欄位。 此端點沒有擴充。

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>清除資源

若要移除您在本教學課程中建立的所有資源，請刪除資源群組。 此動作會同時刪除群組內含的所有資源。 在本例中，此動作會移除 IoT 中樞、儲存體帳戶和資源群組本身。

### <a name="use-the-azure-cli-to-clean-up-resources"></a>使用 Azure CLI 來清除資源

若要移除資源群組，請使用 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) 命令。 回想一下，`$resourceGroup` 在本教學課程一開始就設定為**ContosoResourcesMsgEn** 。

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已使用下列步驟來設定和測試將 message 擴充新增至 IoT 中樞訊息：

**使用 IoT 中樞 message 擴充**
> [!div class="checklist"]
> * 第一個方法：使用 Azure CLI 建立資源及設定訊息路由。 使用[Azure 入口網站](https://portal.azure.com)手動設定訊息擴充。
> * 第二種方法：使用 Azure Resource Manager 範本來建立資源及設定訊息路由和訊息擴充。
> * 執行模擬 IoT 裝置的應用程式，將訊息傳送至中樞。
> * 查看結果，並確認訊息擴充如預期般運作。

如需有關 message 擴充的詳細資訊，請參閱[message 擴充的總覽](iot-hub-message-enrichments-overview.md)。

如需訊息路由的詳細資訊，請參閱下列文章：

* [使用 IoT 中樞訊息路由將裝置到雲端訊息傳送至不同的端點](iot-hub-devguide-messages-d2c.md)
* [教學課程： IoT 中樞路由](tutorial-routing.md)
