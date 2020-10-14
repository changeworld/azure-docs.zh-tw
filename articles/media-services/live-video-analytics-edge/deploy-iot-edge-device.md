---
title: 在 IoT Edge 裝置上部署即時影片分析-Azure
description: 本文列出可協助您在 IoT Edge 裝置上部署即時影片分析的步驟。 例如，如果您有本機 Linux 電腦的存取權，以及（或）先前已建立 Azure 媒體服務帳戶，您就可以這麼做。
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: a64fc11d7afa70d5200fdbd24bd3facdb8a95a7e
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019574"
---
# <a name="deploy-live-video-analytics-on-an-iot-edge-device"></a>在 IoT Edge 裝置上部署即時影片分析

本文列出可協助您在 IoT Edge 裝置上部署即時影片分析的步驟。 例如，如果您有本機 Linux 電腦的存取權，以及（或）先前已建立 Azure 媒體服務帳戶，您就可以這麼做。

> [!NOTE]
> ARM64 裝置的支援可在 IoT Edge 組建和更新版本的即時影片分析中取得 `1.0.4` 。
> 在 ARM64 裝置上執行 Azure IoT Edge 執行階段的支援處於[公開預覽](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)狀態。

## <a name="prerequisites"></a>必要條件

* 執行其中一種[支援的 Linux 作業系統](../../iot-edge/support.md#operating-systems)的 x86-64 或 ARM64 裝置
* 您具有擁有者[許可權](../../role-based-access-control/built-in-roles.md#owner)的 Azure 訂用帳戶
* [建立並設定 IoT 中樞](../../iot-hub/iot-hub-create-through-portal.md)
* [註冊 IoT Edge 裝置](../../iot-edge/how-to-register-device.md)
* [在以 Debian 為基礎的 Linux 系統上安裝 Azure IoT Edge 執行時間](../../iot-edge/how-to-install-iot-edge-linux.md)
* [建立 Azure 媒體服務帳戶](../latest/create-account-howto.md)

    * 使用下列其中一種區域：美國東部2、美國東部、美國中部、美國中北部、日本東部、美國西部、美國西部2、美國中西部、加拿大東部、英國南部、法國中部、法國南部、瑞士北部、瑞士西部和日本西部。
    * 建議您使用一般用途 v2 (GPv2) 儲存體帳戶

## <a name="configuring-azure-resources-for-using-live-video-analytics"></a>設定 Azure 資源以使用即時影片分析

### <a name="create-custom-azure-resource-manager-role"></a>建立自訂 Azure Resource Manager 角色

請參閱 [建立自訂 Azure Resource Manager 角色](create-custom-azure-resource-manager-role-how-to.md) 並將其指派給服務主體，以供即時影片分析使用。

### <a name="set-up-a-premium-streaming-endpoint"></a>設定 premium 串流端點

如果您想要使用即時影片分析將影片持續記錄到雲端，並在播放之前使用 [查詢 api](playback-recordings-how-to.md#query-api) ，則建議您更新媒體服務以使用 [premium 串流端點](../latest/streaming-endpoint-concept.md#types)。  

這是選擇性步驟。 您可以使用此 Azure CLI 命令來執行此動作：

```azurecli
az ams streaming-endpoint scale --resource-group $RESOURCE_GROUP --account-name $AMS_ACCOUNT -n default --scale-units 1
```

您可以使用此命令來啟動串流端點 

> [!IMPORTANT]
> 您的訂用帳戶將會開始計費。

```azurecli
az ams streaming-endpoint start --resource-group $RESOURCE_GROUP --account-name $AMS_ACCOUNT -n default --no-wait
```

遵循本文中的步驟，以取得存取媒體服務 Api 的認證： [存取媒體服務 api](../latest/access-api-howto.md?tabs=portal) ，然後選取 [入口網站] 索引標籤。

## <a name="create-and-use-local-user-account-for-deployment"></a>建立和使用本機使用者帳戶以進行部署
若要在 IoT Edge 模組上執行即時影片分析，請建立具有最少許可權的本機使用者帳戶。 例如，在您的 Linux 電腦上執行下列命令：

```
sudo groupadd -g 1010 localuser
sudo adduser --home /home/edgeuser --uid 1010 -gid 1010 edgeuser
```

## <a name="granting-permissions-to-device-storage"></a>授與許可權給裝置存放裝置

現在您已建立本機使用者帳戶， 

* 您將需要本機資料夾來儲存應用程式設定資料。 使用下列命令建立資料夾，並將許可權授與 localuser 帳戶寫入該資料夾：

```
sudo mkdir /var/lib/azuremediaservices
sudo chown -R edgeuser /var/lib/azuremediaservices
```

* 您也需要一個資料夾，以將影片 [記錄到本機](event-based-video-recording-concept.md#video-recording-based-on-events-from-other-sources)檔案。 使用下列命令來建立相同的本機資料夾：

```
sudo mkdir /var/media
sudo chown -R edgeuser /var/media
```

## <a name="deploy-live-video-analytics-edge-module"></a>部署即時影片分析 Edge 模組

IoT Edge 上的即時影片分析會公開模組對應項設定 [架構](module-twin-configuration-schema.md)中記載的模組對應項屬性。 

### <a name="deploy-using-the-azure-portal"></a>使用 Azure 入口網站部署

Azure 入口網站會引導您建立部署資訊清單，並將部署推送至 IoT Edge 裝置。  
#### <a name="select-your-device-and-set-modules"></a>選取您的裝置並設定模組

1. 登入 [Azure 入口網站](https://ms.portal.azure.com/)，然後瀏覽至 IoT 中樞。
1. 選取功能表中的 [IoT Edge]****。
1. 按一下裝置清單中目標裝置的識別碼。
1. 選取 [ **設定模組**]。

#### <a name="configure-a-deployment-manifest"></a>設定部署資訊清單

部署資訊清單為 JSON 文件，說明應部署的模組、資料如何在模組之間流動，以及想要的模組對應項需要的屬性。 此 Azure 入口網站有一個嚮導，可逐步引導您建立部署資訊清單。 它會將三個步驟分成多個索引標籤： **模組**、 **路由**和 **審核 + 建立**。

#### <a name="add-modules"></a>新增模組

1. 在頁面的 [ **IoT Edge 模組** ] 區段中，按一下 [ **新增** ] 下拉式清單，然後選取 [ **IoT Edge 模組** ]，以顯示 [ **新增 IoT Edge 模組** ] 頁面。
1. 在 [ **模組設定** ] 索引標籤上，提供模組的名稱，然後指定容器映射 URI：   
    範例：
    
    * **IoT Edge 模組名稱**： lvaEdge
    * **映射 URI**： mcr.microsoft.com/media/live-video-analytics:1。0    
    
    ![螢幕擷取畫面顯示 [模組設定] 索引標籤。](./media/deploy-iot-edge-device/add.png)
    
    > [!TIP]
    > 除非您已在 [**模組設定**]、[**容器建立選項**] 和 [模組對應項**設定**] 索引標籤上指定值，如下列程式所述，請勿選取 [**新增**]。
    
    > [!WARNING]
    > 當您對模組進行呼叫時，Azure IoT Edge 會區分大小寫。 記下您用來作為模組名稱的確切字串。

1. 開啟 [ **環境變數** ] 索引標籤。
   
   在您看到環境變數的輸入方塊中新增下列值 ![](./media/deploy-iot-edge-device/environment-variables.png) 

1. 開啟 [ **容器建立選項** ] 索引標籤。

    ![容器建立選項](./media/deploy-iot-edge-device/container-create-options.png)
 
    將下列 JSON 複製並貼到 box 中，以限制模組所產生的記錄檔大小。
    
    ```    
    {
        "HostConfig": {
            "LogConfig": {
                "Type": "",
                "Config": {
                    "max-size": "10m",
                    "max-file": "10"
                }
            },
            "Binds": [
               "/var/lib/azuremediaservices:/var/lib/azuremediaservices",
               "/var/media:/var/media"
            ]
        }
    }
    ````
   
   JSON 中的「系結」區段有2個專案：
   1. "/var/lib/azuremediaservices：/var/lib/azuremediaservices"：這是用來系結容器中的持續性應用程式設定資料，並將它儲存在 edge 裝置上。
   1. "/var/media：/var/media"：這會系結 edge 裝置與容器之間的媒體檔案夾。 當您執行可支援在 edge 裝置上儲存影片剪輯的 media graph 拓撲時，會使用此功能來儲存影片錄製。
   
1. 在 [ **模組** 對應項設定] 索引標籤上，複製下列 JSON，並將它貼到方塊中。
 
    ![對應項設定](./media/deploy-iot-edge-device/twin-settings.png)

    IoT Edge 上的即時影片分析需要一組必要的對應項屬性，才能執行，如模組對應項設定 [架構](module-twin-configuration-schema.md)中所列。  

    您需要在模組對應項設定編輯方塊中輸入的 JSON 看起來會像這樣：    
    ```
    {
        "applicationDataDirectory": "/var/lib/azuremediaservices",
        "azureMediaServicesArmId": "/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{AMS-account-name}",
        "aadTenantId": "{the-ID-of-your-tenant}",
        "aadServicePrincipalAppId": "{the-ID-of-the-service-principal-app-for-ams-account}",
        "aadServicePrincipalSecret": "{secret}"
    }
    ```
    這些是 **必要** 的屬性以及上述的 JSON  
    * {subscriptionID}-這是您的 Azure 訂用帳戶識別碼
    * {resourceGroupName}-此為媒體服務帳戶所屬的資源群組
    * {AMS-帳戶-名稱}-這是您的媒體服務帳戶名稱
    
    若要取得其他值，請參閱 [存取 AZURE 媒體服務 API](../latest/access-api-howto.md?tabs=portal) ，然後選取 [入口網站] 索引標籤。  
    * aadTenantId-這是您租使用者的識別碼，與上述連結中的 "AadTenantId" 相同。
    * aadServicePrincipalAppId-這是媒體服務帳戶的服務主體的應用程式識別碼，與上述連結中的 "AadClientId" 相同。
    * aadServicePrincipalSecret-這是服務主體的密碼，與上述連結中的 "AadSecret" 相同。

    以下是一些可新增至 JSON 的其他 **建議** 屬性，並將有助於監視模組。 如需詳細資訊，請參閱 [監視和記錄](monitoring-logging.md)：
    
    ```
    "diagnosticsEventsOutputName": "lvaEdgeDiagnostics",
    "OperationalEventsOutputName": "lvaEdgeOperational",
    "logLevel": "Information",
    "logCategories": "Application,Events"
    ```
    
    以下是一些您可以在 JSON 中新增的 **選擇性** 屬性：
    
    ```
    "aadEndpoint": "https://login.microsoftonline.com",
    "aadResourceId": "https://management.core.windows.net/",
    "armEndpoint": "https://management.azure.com/",
    "allowUnsecuredEndpoints": true
    ```

   > [!Note]
   > 對應項屬性 **allowUnsecuredEndpoints** 會設定為 true，以供教學課程和快速入門之用。   
   在生產環境中執行時，您應該將此屬性設定為 **false** 。 這可確保應用程式會封鎖所有不安全的端點，而且為了執行圖形拓撲，將需要有效的連接認證。  
   
    選取 [新增] 以新增模組對應項屬性。
1. 選取 **[下一步：路由]** 以繼續前往 [路由] 區段。
    指定路由。

保留預設路由，然後選取 **[下一步：檢查 + 建立]** 以繼續進行審核區段。

#### <a name="review-deployment"></a>檢閱部署

檢閱區段會顯示 JSON 部署資訊清單，該清單會根據您在前兩個區段中的選項而建立。 另外還有兩個模組宣告您未新增： $edgeAgent 和 $edgeHub。 這兩個模組組成 IoT Edge 執行階段，在每個部署中都是必要的預設值。

檢查您的部署資訊，然後選取 [建立]。

### <a name="verify-your-deployment"></a>驗證您的部署

建立部署之後，您會返回 IoT 中樞的 [IoT Edge] 頁面。

1. 選取您使用部署設定為目標的 IoT Edge 裝置，以開啟其詳細資料。
2. 在裝置詳細資料中，確認 Blob 儲存體模組列為 [指定於部署中]** 和 [由裝置回報]**。

模組在裝置上啟動然後向 IoT 中樞回報可能需要一點時間。 重新整理頁面來查看更新狀態。
狀態碼：200– OK 表示 [IoT Edge 運行](../../iot-edge/iot-edge-runtime.md) 時間狀況良好且正常運作。

![螢幕擷取畫面顯示 IoT Edge 執行時間的狀態值。](./media/deploy-iot-edge-device/status.png)

#### <a name="invoke-a-direct-method"></a>叫用直接方法

接下來，請叫用直接方法來測試範例。 閱讀 [IoT Edge 的即時影片分析直接方法](direct-methods.md) ，瞭解 lvaEdge 課程模組提供的所有直接方法。

1. 按一下您建立的 edge 模組，將會帶您前往其設定頁面。  

    ![螢幕擷取畫面：顯示 edge 模組的設定頁面。](./media/deploy-iot-edge-device/modules.png)
1. 按一下 [直接方法] 功能表選項。

    > [!NOTE] 
    > 您將需要在 [連接字串] 區段中加入值，如您在目前頁面上所見。 您不需要在 [ **設定名稱** ] 區段中隱藏或變更任何內容。 可以讓其成為公用。

    ![直接方法](./media/deploy-iot-edge-device/module-details.png)
1. 接下來，在 [方法名稱] 方塊中輸入 "GraphTopologyList"。
1. 接下來，在 [裝載] 方塊中複製並貼上下列 JSON 承載。
    
    ```
    {
        "@apiVersion" : "1.0"
    }
    ```
1. 按一下頁面頂端的 [叫用方法] 選項

    ![直接方法](./media/deploy-iot-edge-device/direct-method.png)
1. 您應該會在 [結果] 方塊中看到狀態200訊息

    ![狀態200訊息](./media/deploy-iot-edge-device/connection-timeout.png) 

## <a name="next-steps"></a>後續步驟

試用 [快速入門：開始使用 IoT Edge 的即時影片分析](get-started-detect-motion-emit-events-quickstart.md#deploy-modules-on-your-edge-device)

> [!TIP]
> 如果您繼續進行上述快速入門，使用 Visual Studio Code 叫用直接方法時，您將使用透過本文新增至 IoT 中樞的裝置，而不是預設值 `lva-sample-device` 。