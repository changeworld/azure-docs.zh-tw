---
title: 教學 - 使用 Azure IoT 中心訊息擴充
description: 展示如何對 Azure IoT 中心訊息使用訊息擴充的教程
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 78aee7829e58feede3360f30f10260903713c52f
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770070"
---
# <a name="tutorial-use-azure-iot-hub-message-enrichments"></a>教學:使用 Azure IoT 中心訊息擴充

*消息擴充*描述 Azure IoT 中心在消息發送到指定終結點之前使用附加資訊*標記*訊息的能力。 使用消息擴充的一個原因是包含可用於簡化下游處理的數據。 例如,使用設備孿生標記豐富設備遙測消息可以減少客戶對此資訊進行設備孿生 API 調用的負載。 有關詳細資訊,請參閱[消息擴充概述](iot-hub-message-enrichments-overview.md)。

在本教學中,您將看到創建和配置測試 IoT 中心的消息擴充所需的資源的兩種方法。 資源包括一個包含兩個儲存容器的存儲帳戶。 一個容器保存富集消息,另一個容器保存原始消息。 還包括一個 IoT 中心,用於接收消息,並根據消息是否豐富而將其路由到相應的儲存容器。

* 第一種方法是使用 Azure CLI 創建資源和配置消息路由。 然後,使用[Azure 門戶](https://portal.azure.com)手動定義擴充。

* 第二種方法是使用 Azure 資源管理器範本為消息路由*和*消息擴充創建資源和配置。

消息路由和消息擴充的配置完成後,可以使用應用程式向IoT中心發送消息。 然後,集線器將它們路由到兩個存儲容器。 只有發送到**豐富**存儲容器的終結點的消息才會豐富。

以下是完成本教學的任務:

**使用 IoT 中心訊息擴充**
> [!div class="checklist"]
> * 第一種方法:使用 Azure CLI 創建資源和配置消息路由。 使用[Azure 門戶](https://portal.azure.com)手動配置消息擴充。
> * 第二種方法:使用資源管理器範本創建資源和配置消息路由和消息擴充。 
> * 運行類比向中心發送消息的IoT設備的應用。
> * 查看結果,並驗證消息擴充是否按預期工作。

## <a name="prerequisites"></a>Prerequisites

* 您必須擁有 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 安裝[視覺工作室](https://www.visualstudio.com/)。

* 請確定您的防火牆已開啟連接埠 8883。 本教學課程中的裝置範例會使用 MQTT 通訊協定，其會透過連接埠 8883 進行通訊。 某些公司和教育網路環境可能會封鎖此連接埠。 如需此問題的詳細資訊和解決方法，請參閱[連線至 IoT 中樞 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-iot-c-samples-repository"></a>檢索 IoT C++ 範例儲存函式庫

從 GitHub 下載[IoT C# 範例](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)並解壓縮它們。 此存儲庫中包含多個應用程式、腳本和資源管理器範本。 本教學中使用的內容如下:

* 對於手動方法,有一個用於創建資源的 CLI 腳本。 此腳本位於 /azure-iot-範例-csharp/iot-hub/教程/路由/類比設備/資源/iothub_msgenrichment_cli.azcli 中。 此文本創建資源並配置消息路由。 執行此文稿後,請使用[Azure 門戶](https://portal.azure.com)手動創建消息擴充。
* 對於自動化方法,有一個 Azure 資源管理器範本。 樣本位於 /azure-iot-範例-csharp/iot-hub/教程/路由/類比設備/資源/template_msgenrichments.json 中。 此範本創建資源,配置消息路由,然後配置消息擴充。
* 您使用的第三個應用程式是設備類比應用,用於向 IoT 中心發送消息並測試消息擴充。

## <a name="manually-set-up-and-configure-by-using-the-azure-cli"></a>使用 Azure CLI 手動設定與設定

除了創建必要的資源外,Azure CLI 腳本還配置到作為獨立存儲容器的終結點的兩個路由。 有關如何設定訊息路由的詳細資訊,請參閱[路由教程](tutorial-routing.md)。 設置資源后,使用[Azure 門戶](https://portal.azure.com)為每個終結點配置消息擴充。 然後繼續測試步驟。

> [!NOTE]
> 所有消息都將路由到兩個終結點,但只有使用配置的消息擴充發送到終結點的消息才會豐富。
>

您可以使用下面的腳本,也可以在下載的儲存庫的 /resources 資料夾中打開腳本。 指令碼會執行下列步驟：

* 建立 IoT 中樞。
* 建立儲存體帳戶。
* 在存儲帳戶中創建兩個容器。 一個容器用於富集郵件,另一個容器用於不富集的消息。
* 為兩個不同的儲存帳戶設定路由:
    * 為每個存儲帳戶容器創建終結點。
    * 創建到每個存儲帳戶容器終結點的路由。

有幾個資源名稱必須全域唯一,例如 IoT 中心名稱和存儲帳戶名稱。 為了簡化腳本的運行,這些資源名稱附加了稱為*隨機值*的隨機字母數值。 隨機值在腳本頂部生成一次。 它在整個腳本中根據需要追加到資源名稱。 如果不希望該值為隨機值,則可以將其設置為空字串或特定值。

如果尚未這樣做,則打開 Azure[雲外殼視窗](https://shell.azure.com)並確保其設置為 Bash。 打開解壓縮儲存庫中的腳本,選擇 Ctrl_A 以選擇所有腳本,然後選擇 Ctrl_C 來複製它。 或者,您可以複製以下 CLI 腳本,或直接在雲殼中打開它。 右鍵按一下命令列並選擇 **「貼上**」,將腳本貼到雲殼視窗中。 該腳本一次運行一個語句。 文本停止運行後,選擇**Enter**以確保它運行最後一個命令。 下面的代碼塊顯示了所使用的腳本,帶有解釋它執行的操作的註釋。

下面是腳本創建的資源。 *富集*意味著資源用於具有富集的消息。 *原始*表示資源用於不富集的郵件。

| 名稱 | 值 |
|-----|-----|
| resourceGroup | 康托索資源MsgEn |
| 容器名稱 | 原始  |
| 容器名稱 | 豐富  |
| IoT 裝置名稱 | 孔托索測試設備 |
| IoT 中樞名稱 | ContosoTestHubMsgEn |
| 儲存帳號名稱 | contoso 儲存 |
| 終結點名稱 1 | Contoso 儲存終結點原始 |
| 端點名稱 2 | Contoso 儲存終結點豐富|
| 路由名稱 1 | Contoso 儲存路由原始 |
| 路由名稱 2 | Contoso 儲存路由豐富 |

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
az extension add --name azure-iot

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

此時,資源全部設置,並配置消息路由。 您可以在門戶中查看消息路由配置,併為發送到**豐富**存儲容器的消息設置消息擴充。

### <a name="manually-configure-the-message-enrichments-by-using-the-azure-portal"></a>使用 Azure 門戶手動配置訊息擴充

1. 通過選擇**資源組**轉到 IoT 中心。 然後選擇為本教學設置的資源組 **(ContosoResourcesMsgEn)。** 在清單中尋找 IoT 中心,然後選擇它。 選擇 IoT 中心**的訊息路由**。

   ![選擇訊息路由](./media/tutorial-message-enrichments/select-iot-hub.png)

   消息路由窗格有三個選項卡標記為**路由**、**自定義終結點**和 **「豐富訊息**」。。 瀏覽前兩個選項卡以查看腳本設置的配置。 使用第三個選項卡添加消息擴充。 讓我們豐富到名為**enrich**的儲存容器的終結點的消息。 填寫名稱和值,然後從下拉清單中選擇終結點**Contoso 儲存的點豐富**。 下面是如何設定將 IoT 中心名稱加入到訊息的擴充範例:

   ![新增第一次濃縮](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. 將這些值添加到 Contoso 儲存終結點的清單中。

   | Key | 值 | 終結點(下拉清單) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | Azure 儲存容器> Contoso 儲存終結點豐富 |
   | 裝置位置 | $twin.標籤.位置 | Azure 儲存容器> Contoso 儲存終結點豐富 |
   |客戶識別碼 | 6ce345b8-1e4a-411e-9398-d34587459a3a | Azure 儲存容器> Contoso 儲存終結點豐富 |

   > [!NOTE]
   > 如果設備沒有孿生,則在此處放入的值將標記為消息擴充中值的字串。 要查看設備孿生資訊,請轉到門戶中的中心並選擇**IoT 裝置**。 選擇您的設備,然後在頁面頂部選擇 **「設備孿生**」。。
   >
   > 您可以編輯孿生資訊以添加標記(如位置)並將其設置為特定值。 有關詳細資訊,請參閱在[IoT 中心中瞭解和使用裝置孿生](iot-hub-devguide-device-twins.md)。

3. 完成後,窗格應類似於此影像:

   ![新增所有擴充的表](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. 選擇 **「應用」** 以儲存變更。 跳至[「測試消息擴充](#test-message-enrichments)」部分。

## <a name="create-and-configure-by-using-a-resource-manager-template"></a>使用資源管理員樣本建立及設定
您可以使用資源管理器範本創建和配置資源、消息路由和消息擴充。

1. 登入 Azure 入口網站。 選擇 **= 建立資源**以啟動搜尋框。 輸入*樣本部署*,然後搜索它。 在結果窗格中,選擇**範本部署(使用自定義範本部署)。**

   ![Azure 門戶中的樣本部署](./media/tutorial-message-enrichments/template-select-deployment.png)

1. 在 **「樣本部署**」窗格中選擇 **"創建**」。

1. 在 **「自訂部署」** 窗格中,在**編輯器中選擇" 構建您自己的範本**。

1. 在 **「編輯樣本」** 窗格中,選擇 **「載入檔**」 。 將顯示 Windows 資源管理員。 在 **/iot-hub/教程/路由/模擬設備/資源**中解壓縮的回購檔中找到**template_messageenrichments.json**檔。 

   ![從本地電腦中選擇樣本](./media/tutorial-message-enrichments/template-select.png)

1. 選擇 **「開啟」** 以從本地電腦載入樣本檔。 它載入並顯示在編輯窗格中。

   此範本設定為透過預設名稱的末尾添加隨機值來使用全域唯一的 IoT 中心名稱和儲存帳戶名稱,因此可以使用範本而不對其進行任何更改。

   下面是通過載入範本創建的資源。 **富集**意味著資源用於具有富集的消息。 **原始**表示資源用於不富集的郵件。 這些值與 Azure CLI 文本中使用的值相同。

   | 名稱 | 值 |
   |-----|-----|
   | resourceGroup | 康托索資源MsgEn |
   | 容器名稱 | 原始  |
   | 容器名稱 | 豐富  |
   | IoT 裝置名稱 | 孔托索測試設備 |
   | IoT 中樞名稱 | ContosoTestHubMsgEn |
   | 儲存帳號名稱 | contoso 儲存 |
   | 終結點名稱 1 | Contoso 儲存終結點原始 |
   | 端點名稱 2 | Contoso 儲存終結點豐富|
   | 路由名稱 1 | Contoso 儲存路由原始 |
   | 路由名稱 2 | Contoso 儲存路由豐富 |

1. 選取 [儲存]  。 「**自訂部署」** 窗格會顯示並顯示樣本使用的所有參數。 需要設定的唯一欄位是**資源群組**。 建立新清單或從下拉清單中選擇一個。

   下面是**自定義部署**窗格的上半部分。 您可以看到在資源組中的填寫位置。

   ![自訂部署窗格的上半部份](./media/tutorial-message-enrichments/template-deployment-top.png)

1. 下面是**自定義部署**窗格的下半部分。 您可以看到其餘的參數和條款和條件。 

   ![自訂部署窗格的下半部份](./media/tutorial-message-enrichments/template-deployment-bottom.png)

1. 選中此複選框以同意條款和條件。 然後選擇 **「購買」** 以繼續範本部署。

1. 等待範本完全部署。 選擇屏幕頂部的鐘形圖示以檢查進度。 完成後,繼續測試[消息擴充](#test-message-enrichments)部分。

## <a name="test-message-enrichments"></a>測試訊息擴充

要查看消息擴充,請選擇 **「資源組**」。。 然後選擇用於本教程的資源組。 從資源清單中選擇 IoT 中心,然後轉到**訊息**。 將顯示消息路由配置和配置的擴充。

現在為終結點配置了消息擴充,請運行模擬設備應用程式以將消息發送到 IoT 中心。 中心設定的設定具有完成以下工作的設定:

* 路由到存儲終結點 Contoso 儲存終結點原始的消息不會豐富,並將存儲在`original`存儲容器中。

* 路由到存儲終結點 Contoso 儲存終結點的消息將豐富並存儲在`enriched`存儲容器 中。

模擬設備應用程式是解壓縮下載中的應用程式之一。 應用程式在[路由教程](tutorial-routing.md)中為每個不同的消息路由方法發送消息,其中包括 Azure 存儲。

雙擊解決方案檔案**IoT_SimulatedDevice.sln**以在 Visual Studio 開啟代碼,然後開啟**Program.cs**。 將 IoT 中心名稱取代為`{your hub name}`標籤 。 IoT 中心主機名稱的格式為 **[您的中心名稱].azure-devices.net**。 在本教程中,中心主機名ContosoTestHubMsgEn.azure-devices.net。 接下來,替換運行腳本時較早保存的設備密鑰,以創建標記`{your device key}`的資源。

如果沒有設備密鑰,則可以從門戶檢索它。 登錄後,轉到**資源組**,選擇資源組,然後選擇 IoT 中心。 在**IoT 設備**下搜尋測試設備,然後選擇您的裝置。 選擇**主鍵**旁邊的複製圖示以將其複製到剪貼簿。

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

### <a name="run-and-test"></a>執行和測試

運行主控台應用程式幾分鐘。 正在發送的消息將顯示在應用程式的主控台螢幕上。

應用程式每一秒就會將最新裝置到雲端的訊息傳送至 IoT 中樞。 訊息包含 JSON 序列化的物件與裝置識別碼、溫度、溼度和訊息層級 (預設位置為 `normal`)。 它隨機分配`critical``storage`或的級別,這將導致消息路由到存儲帳戶或默認終結點。 發送到存儲帳戶中**富集**容器的消息將豐富。

發送多個存儲消息后,查看數據。

1. 選擇**資源群組**。 查找資源組**ContosoResourcesMsgEn,** 然後選擇它。

2. 選擇您的儲存帳戶,這是**contoso 儲存**。 然後在左側窗格中選擇 **「存儲資源管理員」(預覽)。**

   ![選擇儲存資源管理員](./media/tutorial-message-enrichments/select-storage-explorer.png)

   選擇**BLOB 容器**以查看可以使用的兩個容器。

   ![檢視儲存帳戶中的容器](./media/tutorial-message-enrichments/show-blob-containers.png)

容器中稱為 **「豐富**」的消息包含消息中的消息擴充。 容器中稱為**原始**消息的消息具有沒有擴充的原始消息。 向下鑽取到其中一個容器,直到到達底部,然後打開最新的消息檔。 然後,對另一個容器執行相同的操作,以驗證該容器中的消息中是否未添加富集。

當您查看已豐富的消息時,應看到包含中心名稱、位置和客戶 ID 的「我的 IoT 中心」,如下所示:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

下面是一條未豐富的消息。 請注意,"我的IoT中心"、"設備定位"和"客戶ID"不會在此處顯示,因為這些欄位是通過擴充添加的。 此終結點沒有擴充。

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>清除資源

要刪除在本教程中創建的所有資源,請刪除資源組。 此動作會同時刪除群組內含的所有資源。 在本例中，此動作會移除 IoT 中樞、儲存體帳戶和資源群組本身。

### <a name="use-the-azure-cli-to-clean-up-resources"></a>使用 Azure CLI 來清除資源

若要移除資源群組，請使用 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) 命令。 回想本教程`$resourceGroup`開頭設置為**ContosoResourcesMsgEn**的。

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>後續步驟

在本教學中,您使用以下步驟配置並測試了向 IoT 中心消息添加消息擴充:

**使用 IoT 中心訊息擴充**
> [!div class="checklist"]
> * 第一種方法:使用 Azure CLI 創建資源和配置消息路由。 使用[Azure 門戶](https://portal.azure.com)手動配置消息擴充。
> * 第二種方法:使用 Azure 資源管理器範本創建資源和配置消息路由和消息擴充。
> * 運行類比向中心發送消息的IoT設備的應用。
> * 查看結果,並驗證消息擴充是否按預期工作。

有關消息擴充的詳細資訊,請參閱[消息擴充概述](iot-hub-message-enrichments-overview.md)。

有關消息路由的詳細資訊,請參閱以下文章:

* [使用 IoT 中心訊息路由將裝置到雲端的消息傳送到不同的終結點](iot-hub-devguide-messages-d2c.md)
* [教程:IoT 中心路由](tutorial-routing.md)
