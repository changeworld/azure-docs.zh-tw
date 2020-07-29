---
title: 如何複製 Azure IoT 中樞
description: 如何複製 Azure IoT 中樞
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: robinsh
ms.openlocfilehash: c54853717f7e0b234df013e5aee575682d0d3d97
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75429160"
---
# <a name="how-to-clone-an-azure-iot-hub-to-another-region"></a>如何將 Azure IoT 中樞複製到另一個區域

本文會探索複製 IoT 中樞的方式，並提供您在開始之前必須回答的一些問題。 以下是您可能想要複製 IoT 中樞的幾個原因：
 
* 您要將公司從一個區域移至另一個地區（例如從歐洲到北美洲（反之亦然），而且您想要讓資源和資料在地理位置靠近您的新地點，因此您需要移動您的中樞。

* 您正在為開發與生產環境設定中樞。

* 您想要進行多中樞高可用性的自訂執行。 如需詳細資訊，請參閱[IoT 中樞高可用性和嚴重損壞修復的如何達成跨區域 HA 一節](iot-hub-ha-dr.md#achieve-cross-region-ha)。

* 您想要增加針對中樞所[設定的](iot-hub-scaling.md#partitions)分割區數目。 這會在您第一次建立中樞時設定，且無法變更。 您可以使用本文中的資訊來複製您的中樞，並在複製建立時增加分割區的數目。

若要複製中樞，您需要具有原始中樞之系統管理存取權的訂用帳戶。 您可以將新的中樞放在新的資源群組和區域、與原始中樞相同的訂用帳戶中，或甚至在新的訂用帳戶中。 您不能使用相同的名稱，因為中樞名稱必須是全域唯一的。

> [!NOTE]
> 目前沒有任何功能可自動複製 IoT 中樞。 這主要是手動程式，因此相當容易出錯。 複製中樞的複雜性與中樞的複雜性直接成正比。 例如，複製不含訊息路由的 IoT 中樞相當簡單。 如果您新增訊息路由只是一個複雜性，則複製中樞的速度會變得越複雜。 如果您也移動用來路由端點的資源，這就是 magniture 更為複雜的另一個順序。 

## <a name="things-to-consider"></a>考量事項

在複製 IoT 中樞之前，有幾件事需要考慮。

* 請確定原始位置中的所有可用功能也都可在新的位置中取得。 有些服務處於預覽狀態，並非所有功能都可在任何地方使用。

* 建立和驗證複製的版本之前，請勿移除原始資源。 一旦您移除中樞，它就會永久消失，而且無法復原它來檢查設定或資料，以確保中樞已正確複寫。

* 許多資源都需要全域唯一的名稱，因此您必須針對複製的版本使用不同的名稱。 針對複製的中樞所屬的資源群組，您也應該使用不同的名稱。 

* 不會遷移原始 IoT 中樞的資料。 這包括遙測訊息、雲端到裝置（C2D）命令，以及作業相關資訊，例如排程和歷程記錄。 也不會遷移計量和記錄結果。 

* 針對路由至 Azure 儲存體的資料或訊息，您可以將資料保留在原始儲存體帳戶中，將該資料傳送至新區域中的新儲存體帳戶，或保留舊資料，並在新的位置中為新的資料建立新的儲存體帳戶。 如需如何在 Blob 儲存體中移動資料的詳細資訊，請參閱[開始使用 AzCopy](../storage/common/storage-use-azcopy-v10.md)。

* 無法遷移事件中樞和服務匯流排主題和佇列的資料。 這是時間點資料，並不會在處理訊息之後儲存。

* 您需要為遷移排程停機時間。 將裝置複製到新的中樞需要一些時間。 如果您使用匯入/匯出方法，基準測試已發現可能需要大約兩個小時才能移動500000裝置，而四個小時則可移動一百萬部裝置。 

* 您可以將裝置複製到新的中樞，而不需要關閉或變更裝置。 

    * 如果裝置最初是使用 DPS 布建，則重新布建它們會更新儲存在每個裝置中的連線資訊。 
    
    * 否則，您必須使用匯入/匯出方法來移動裝置，然後必須修改裝置才能使用新的中樞。 例如，您可以將裝置設定為使用對應項所需屬性中的 IoT 中樞主機名稱。 裝置將會採用該 IoT 中樞主機名稱、中斷裝置與舊中樞的連線，然後重新連線到新集線器。
    
* 您必須更新您所使用的任何憑證，以便將它們用於新的資源。 此外，您可能會在某個位置的 DNS 資料表中定義中樞，您必須更新該 DNS 資訊。

## <a name="methodology"></a>方法

這是我們建議用來將 IoT 中樞從一個區域移至另一個區域的一般方法。 對於訊息路由，這會假設資源不會移至新的區域。 如需詳細資訊，請參閱[訊息路由的一節](#how-to-handle-message-routing)。

   1. 將中樞和其設定匯出至 Resource Manager 範本。 
   
   1. 對範本進行必要的變更，例如更新所有出現的名稱和複製的中樞的位置。 針對用於訊息路由端點的範本中的任何資源，更新該資源的範本中的金鑰。
   
   1. 將範本匯入新位置中的新資源群組。 這會建立複本。

   1. 視需要進行 Debug。 
   
   1. 新增任何未匯出至範本的專案。 
   
       例如，取用者群組不會匯出至範本。 您需要手動將取用者群組新增至範本，或在建立中樞之後使用[Azure 入口網站](https://portal.azure.com)。 在[使用 Azure Resource Manager 範本來設定 IoT 中樞訊息路由](tutorial-routing-config-message-routing-rm-template.md)一文中，有一個將取用者群組新增至範本的範例。
       
   1. 將裝置從原始中樞複製到複本。 這會在[管理向 IoT 中樞註冊的裝置](#managing-the-devices-registered-to-the-iot-hub)一節中討論。

## <a name="how-to-handle-message-routing"></a>如何處理訊息路由

如果您的中樞使用[自訂路由](iot-hub-devguide-messages-read-custom.md)，則匯出中樞的範本包含路由設定，但不包含資源本身。 您必須選擇是否要將路由資源移至新位置，或將其保留在原處，並繼續以「原樣」使用它們。 

例如，假設您在美國西部有一個中樞，其中會將訊息路由傳送至儲存體帳戶（也在美國西部），而您想要將中樞移至美國東部。 您可以移動中樞，並讓它仍然將訊息路由至美國西部的儲存體帳戶，或者您也可以移動中樞並移動儲存體帳戶。 將訊息路由至不同區域中的端點資源可能會有小效能的影響。

如果您也不移動用於路由端點的資源，您可以輕鬆地移動使用訊息路由的中樞。 

如果中樞使用訊息路由，您有兩個選擇。 

1. 將用於路由端點的資源移至新位置。

    * 您必須在[Azure 入口網站](https://portal.azure.com)中，或透過使用 Resource Manager 範本，以手動方式建立新的資源。 

    * 當您在新的位置中建立所有資源時，必須重新命名，因為它們有全域唯一的名稱。 
     
    * 在建立新的中樞之前，您必須先更新新中樞範本中的資源名稱和資源金鑰。 建立新的中樞時，應該會顯示資源。

1. 請勿移動用於路由端點的資源。 使用「就地」。

   * 在編輯範本的步驟中，您必須先抓取每個路由資源的金鑰，然後將它們放在範本中，然後再建立新的中樞。 

   * 中樞仍然會參考原始路由資源，並依設定將訊息路由傳送至它們。

   * 因為中樞和路由端點資源不在相同的位置，所以您會遇到小效能的影響。

## <a name="prepare-to-migrate-the-hub-to-another-region"></a>準備將中樞遷移至另一個區域

本節提供遷移中樞的特定指示。

### <a name="find-the-original-hub-and-export-it-to-a-resource-template"></a>尋找原始中樞，並將它匯出至資源範本。

1. 登入[Azure 入口網站](https://portal.azure.com)。 

1. 移至 [**資源群組**]，然後選取包含您想要移動之中樞的資源群組。 您也可以移至 [**資源**]，並以該方式尋找中樞。 選取中樞。

1. 從中樞的屬性和設定清單中選取 [**匯出範本**]。 

   ![螢幕擷取畫面，顯示用於匯出 IoT 中樞範本的命令。](./media/iot-hub-how-to-clone/iot-hub-export-template.png)

1. 選取 [**下載**] 以下載範本。 將檔案儲存在您可以再次找到的位置。 

   ![螢幕擷取畫面，顯示用於下載 IoT 中樞範本的命令。](./media/iot-hub-how-to-clone/iot-hub-download-template.png)

### <a name="view-the-template"></a>檢視範本 

1. 移至 [下載] 資料夾（或匯出範本時所使用的任何資料夾），然後尋找 zip 檔案。 開啟 zip 檔案，並尋找名為的檔案 `template.json` 。 選取它，然後選取 Ctrl + C 來複製範本。 移至不在 zip 檔案中的不同資料夾，並貼上檔案（Ctrl + V）。 現在您可以編輯它。
 
    下列範例適用于不含路由設定的泛型中樞。 在區域**westus**中，它是一個稱為**ContosoTestHub29358**的 S1 層中樞（具有1個單位）。 以下是匯出的範本。

    ``` json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Devices/IotHubs",
                "apiVersion": "2018-04-01",
                "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
                "location": "westus",
                "sku": {
                    "name": "S1",
                    "tier": "Standard",
                    "capacity": 1
                },
                "properties": {
                    "operationsMonitoringProperties": {
                        "events": {
                            "None": "None",
                            "Connections": "None",
                            "DeviceTelemetry": "None",
                            "C2DCommands": "None",
                            "DeviceIdentityOperations": "None",
                            "FileUploadOperations": "None",
                            "Routes": "None"
                        }
                    },
                    "ipFilterRules": [],
                    "eventHubEndpoints": {
                        "events": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        },
                        "operationsMonitoringEvents": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358-operationmonitoring",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        }
                    },
                    "routing": {
                        "endpoints": {
                            "serviceBusQueues": [],
                            "serviceBusTopics": [],
                            "eventHubs": [],
                            "storageContainers": []
                        },
                        "routes": [],
                        "fallbackRoute": {
                            "name": "$fallback",
                            "source": "DeviceMessages",
                            "condition": "true",
                            "endpointNames": [
                                "events"
                            ],
                            "isEnabled": true
                        }
                    },
                    "storageEndpoints": {
                        "$default": {
                            "sasTtlAsIso8601": "PT1H",
                            "connectionString": "",
                            "containerName": ""
                        }
                    },
                    "messagingEndpoints": {
                        "fileNotifications": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "enableFileUploadNotifications": false,
                    "cloudToDevice": {
                        "maxDeliveryCount": 10,
                        "defaultTtlAsIso8601": "PT1H",
                        "feedback": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "features": "None"
                }
            }
        ]
    }
    ```

### <a name="edit-the-template"></a>編輯範本 

您必須先進行一些變更，才能使用範本在新的區域中建立新的中樞。 使用[VS Code](https://code.visualstudio.com)或文字編輯器來編輯範本。

#### <a name="edit-the-hub-name-and-location"></a>編輯中樞名稱和位置

1. 移除頂端的 parameters 區段--只要使用中樞名稱就會變得更簡單，因為我們不會有多個參數。 

    ``` json
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
    ```

1. 將名稱變更為使用實際（新的）名稱，而不是從參數（您在上一個步驟中移除）中抓取。 

    針對新的中樞，使用原始中樞的名稱加上字串*clone*來組成新名稱。 一開始請先清除中樞名稱和位置。
    
    舊版本：

    ``` json 
    "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
    "location": "westus",
    ```
    
    新版本： 

    ``` json 
    "name": "ContosoTestHub29358clone",
    "location": "eastus",
    ```

    接下來，您會發現**path**的值包含舊的中樞名稱。 將其變更為使用新的帳戶。 這些是**eventHubEndpoints**下的路徑值，稱為**events**和**OperationsMonitoringEvents**。

    當您完成時，您的事件中樞端點區段看起來應該像這樣：

    ``` json
    "eventHubEndpoints": {
        "events": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        },
        "operationsMonitoringEvents": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone-operationmonitoring",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        }
    ```

#### <a name="update-the-keys-for-the-routing-resources-that-are-not-being-moved"></a>更新未移動之路由資源的金鑰

當您匯出已設定路由之中樞的 Resource Manager 範本時，您會看到匯出的範本中未提供這些資源的金鑰，其位置以星號表示。 您必須在入口網站中前往這些資源，並在匯入新中樞的範本並建立中樞**之前，先**取出金鑰，以填入。 

1. 取得任何路由資源所需的金鑰，並將其放在範本中。 您可以從[Azure 入口網站](https://portal.azure.com)中的資源取得金鑰。 

   例如，如果您要將訊息路由至儲存體容器，請在入口網站中尋找儲存體帳戶。 在 [設定] 區段下，選取 [**存取金鑰**]，然後複製其中一個金鑰。 當您第一次匯出範本時，金鑰看起來會像這樣：

   ```json
   "connectionString": "DefaultEndpointsProtocol=https;
   AccountName=fabrikamstorage1234;AccountKey=****",
   "containerName": "fabrikamresults",
   ```

1. 在您取得儲存體帳戶的帳戶金鑰之後，請將它放在子句中的範本中， `AccountKey=****` 以取代星號。 

1. 針對服務匯流排佇列，取得與 SharedAccessKeyName 相符的共用存取金鑰。 以下是 json 中的金鑰和 `SharedAccessKeyName` ：

   ```json
   "connectionString": "Endpoint=sb://fabrikamsbnamespace1234.servicebus.windows.net:5671/;
   SharedAccessKeyName=iothubroutes_FabrikamResources;
   SharedAccessKey=****;
   EntityPath=fabrikamsbqueue1234",
   ```

1. 服務匯流排主題和事件中樞連接也適用相同的情況。

#### <a name="create-the-new-routing-resources-in-the-new-location"></a>在新位置中建立新的路由資源

本節僅適用于您要移動中樞用於路由端點的資源時。

如果您想要移動路由資源，您必須手動設定新位置中的資源。 您可以使用[Azure 入口網站](https://portal.azure.com)建立路由資源，或匯出訊息路由所使用的每個資源的 Resource Manager 範本、編輯它們，然後匯入它們。 設定資源之後，您可以匯入中樞的範本（其中包含路由設定）。

1. 建立路由所使用的每個資源。 您可以使用[Azure 入口網站](https://portal.azure.com)手動執行此動作，或使用 Resource Manager 範本來建立資源。 如果您想要使用範本，以下是要遵循的步驟：

    1. 針對路由所使用的每個資源，將其匯出至 Resource Manager 範本。
    
    1. 更新資源的名稱和位置。 

    1. 更新資源之間的任何交互參照。 例如，如果您為新的儲存體帳戶建立範本，則需要更新該範本中的儲存體帳戶名稱，以及任何其他參考它的範本。 在大部分情況下，中樞範本中的路由區段是唯一參考資源的其他範本。 

    1. 匯入每個範本，以部署每個資源。

    一旦路由所使用的資源已設定且正在執行，您就可以繼續進行。

1. 在 IoT 中樞的範本中，將每個路由資源的名稱變更為新名稱，並視需要更新位置。 

現在您有一個範本，會建立新的中樞，看起來幾乎與舊的中樞一模一樣，視您決定如何處理路由而定。

## <a name="move----create-the-new-hub-in-the-new-region-by-loading-the-template"></a>移動--藉由載入範本，在新的區域中建立新的中樞

使用範本在新的位置中建立新的中樞。 如果您有要移動的路由資源，應該在新的位置設定資源，並更新範本中的參考以符合。 如果您不移動路由資源，它們應該位於具有更新金鑰的範本中。

1. 登入[Azure 入口網站](https://portal.azure.com)。

1. 選取 [建立資源]。 

1. 在搜尋方塊中，放入「範本部署」，然後選取 Enter。

1. 選取**範本部署（使用自訂範本部署）**。 這會帶您前往範本部署的畫面。 選取 [建立]。 您會看到這個畫面：

   ![顯示用來建立自己範本之命令的螢幕擷取畫面](./media/iot-hub-how-to-clone/iot-hub-custom-deployment.png)

1. **在編輯器中選取 [建立您自己的範本**]，這可讓您從檔案上傳您的範本。 

1. 選取 [**載入**檔案]。 

   ![螢幕擷取畫面，顯示用於上傳範本檔案的命令](./media/iot-hub-how-to-clone/iot-hub-upload-file.png)

1. 流覽您編輯的新範本並加以選取，然後選取 [**開啟**]。 它會在 [編輯] 視窗中載入您的範本。 選取 [儲存]。 

   ![顯示載入範本的螢幕擷取畫面](./media/iot-hub-how-to-clone/iot-hub-loading-template.png)

1. 填寫下列欄位。

   **訂**用帳戶：選取要使用的訂用帳戶。

   **資源群組**：在新的位置中建立新的資源群組。 如果您已經設定好一個新的，則可以選取它，而不是建立新的。

   **位置**：如果您選取現有的資源群組，這會填入，讓您符合資源群組的位置。 如果您已建立新的資源群組，這會是其位置。

   **我同意核取方塊**：這基本上表示您同意要為您所建立的資源付費。

1. 選取 [**購買**] 按鈕。

入口網站現在會驗證您的範本，並部署複製的中樞。 如果您有路由設定資料，它會包含在新的中樞內，但會指向先前位置中的資源。

## <a name="managing-the-devices-registered-to-the-iot-hub"></a>管理已向 IoT 中樞註冊的裝置

現在您已啟動並執行您的複製，您需要將所有裝置從原始中樞複製到複本。 

有多種方法可以完成這項操作。 您原本使用的是裝置布建[服務（DPS）](/azure/iot-dps/about-iot-dps)來布建裝置，或您沒有這麼做。 如果您這樣做，這並不容易。 如果您沒有這麼做，這可能非常複雜。 

如果您未使用 DPS 來布建您的裝置，可以略過下一節，並開始[使用匯入/匯出將裝置移至新的中樞](#using-import-export-to-move-the-devices-to-the-new-hub)。

## <a name="using-dps-to-re-provision-the-devices-in-the-new-hub"></a>使用 DPS 在新的中樞重新布建裝置

若要使用 DPS 將裝置移至新位置，請參閱[如何重新布建裝置](../iot-dps/how-to-reprovision.md)。 當您完成時，您可以在[Azure 入口網站](https://portal.azure.com)中查看裝置，並確認它們位於新的位置。

使用[Azure 入口網站](https://portal.azure.com)移至新的中樞。 選取您的中樞，然後選取 [ **IoT 裝置**]。 您會看到已重新布建到複製的中樞的裝置。 您也可以查看複製的中樞的屬性。 

如果您已執行路由，請進行測試，並確定您的訊息已正確地路由傳送至資源。

### <a name="committing-the-changes-after-using-dps"></a>使用 DPS 之後認可變更

DPS 服務已認可這項變更。

### <a name="rolling-back-the-changes-after-using-dps"></a>使用 DPS 後回復變更。 

如果您想要復原變更，請將裝置從新的中樞重新布建到舊的集線器。

您現在已完成遷移中樞和其裝置。 您可以跳過[清除](#clean-up)。

## <a name="using-import-export-to-move-the-devices-to-the-new-hub"></a>使用匯入匯出將裝置移至新的中樞

應用程式是以 .NET Core 為目標，因此您可以在 Windows 或 Linux 上執行。 您可以下載範例、抓取您的連接字串、設定您想要執行之位的旗標，並加以執行。 您可以在不開啟程式碼的情況下執行此動作。

### <a name="downloading-the-sample"></a>下載範例

1. 使用此頁面中的 IoT c # 範例：[適用于 c # 的 Azure Iot 範例](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/)。 下載 zip 檔案，並將它解壓縮到您的電腦上。 

1. 相關的程式碼位於/iot-hub/Samples/service/ImportExportDevicesSample. 中。 您不需要查看或編輯程式碼，即可執行應用程式。

1. 若要執行應用程式，請指定三個連接字串和五個選項。 您會以命令列引數或使用環境變數的方式，將此資料傳入，或使用這兩者的組合。 我們會將中的選項當做命令列引數，以及連接字串當做環境變數來傳遞。 

   這是因為連接字串很長且難看，而且不太可能變更，但您可能會想要變更選項，並多次執行應用程式。 若要變更環境變數的值，您必須關閉命令視窗，並 Visual Studio 或 VS Code （不論您使用哪一種）。 

### <a name="options"></a>選項

以下是您在執行應用程式時指定的五個選項。 我們會將這些資訊放在命令列上一分鐘。

*   **addDevices** （引數1）--如果您想要新增為您產生的虛擬裝置，請將此設定為 true。 這些會新增至來源中樞。 此外，設定**numToAdd** （argument 2）以指定您想要新增的裝置數目。 您可以向中樞註冊的裝置數目上限為1000000。此選項的目的是用於測試--您可以產生特定數目的裝置，然後將它們複製到另一個中樞。

*   **copyDevices** （引數3）--將此設為 true 可將裝置從一個中樞複製到另一個。 

*   **deleteSourceDevices** （引數4）--將此設定為 true，以刪除所有已向來源中樞註冊的裝置。 我們建議等待，直到您確定所有裝置都已轉移，然後再執行此功能。 當您刪除裝置之後，就無法再重新取得它們。

*   **deleteDestDevices** （引數5）--將此設為 true，以刪除所有已向目的地中樞註冊的裝置（複製）。 如果您想要多次複製裝置，您可能會想要這麼做。 

基本命令會是*dotnet run* --這會告訴 .net 建立本機 .csproj 檔案，然後加以執行。 在執行之前，您會將命令列引數新增至結尾。 

您的命令列看起來會像下列範例：

``` console 
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub, or delete them. 
    // The first argument is true, numToAdd is 50, and the other arguments are false.
    dotnet run true 1000 false false false 

    // Copy the devices you just added to the other hub; don't delete anything.
    // The first argument is false, numToAdd is 0, copy-devices is true, and the delete arguments are both false
    dotnet run false 0 true false false 
```

### <a name="using-environment-variables-for-the-connection-strings"></a>使用連接字串的環境變數

1. 若要執行範例，您需要舊版和新 IoT 中樞的連接字串，以及您可以用於暫存工作檔案的儲存體帳戶。 我們會將這些值儲存在環境變數中。

1. 若要取得連接字串值，請登入[Azure 入口網站](https://portal.azure.com)。 

1. 將連接字串放在可取得的位置，例如 [記事本]。 如果您複製下列各項，就可以直接將連接字串貼到它們的位置。 請不要在等號前後加上空格，或變更變數名稱。 此外，您不需要在連接字串前後加上雙引號。 如果您在儲存體帳戶連接字串周圍加上引號，則無法使用。

   對於 Windows，這是您設定環境變數的方式：

   ``` console  
   SET IOTHUB_CONN_STRING=<put connection string to original IoT Hub here>
   SET DEST_IOTHUB_CONN_STRING=<put connection string to destination or clone IoT Hub here>
   SET STORAGE_ACCT_CONN_STRING=<put connection string to the storage account here>
   ```
 
   針對 Linux，這是您定義環境變數的方式：

   ``` console  
   export IOTHUB_CONN_STRING="<put connection string to original IoT Hub here>"
   export DEST_IOTHUB_CONN_STRING="<put connection string to destination or clone IoT Hub here>"
   export STORAGE_ACCT_CONN_STRING="<put connection string to the storage account here>"
   ```

1. 針對 IoT 中樞連接字串，請移至入口網站中的每個中樞。 您可以在**資源**中搜尋中樞。 如果您知道資源群組，您可以移至 [**資源群組**]，選取您的資源群組，然後從該資源群組中的資產清單中選取中樞。 

1. 從中樞的 [設定] 中選取 [**共用存取原則**]，然後選取 [ **iothubowner** ] 並複製其中一個連接字串。 對目的地中樞執行相同的動作。 將它們新增至適當的 SET 命令。

1. 針對儲存體帳戶連接字串，請在 [**資源**] 或其**資源群組**底下尋找儲存體帳戶，然後將它開啟。 
   
1. 在 [設定] 區段下，選取 [**存取金鑰**] 並複製其中一個連接字串。 將連接字串放在您的文字檔中，以取得適當的 SET 命令。 

現在您有一個檔案中的環境變數包含 SET 命令，而且您知道您的命令列引數為何。 讓我們執行範例。

### <a name="running-the-sample-application-and-using-command-line-arguments"></a>執行範例應用程式和使用命令列引數

1. 開啟 [命令提示字元] 視窗。 選取 [Windows] 並輸入， `command prompt` 以取得 [命令提示字元] 視窗。

1. 複製設定環境變數的命令（一次一個），並將其貼到 [命令提示字元] 視窗中，然後選取 Enter 鍵。 當您完成時，請 `SET` 在 [命令提示字元] 視窗中輸入，以查看您的環境變數及其值。 將這些專案複製到 [命令提示字元] 視窗之後，除非您開啟新的 [命令提示字元] 視窗，否則不需要再次複製它們。

1. 在 [命令提示字元] 視窗中，變更目錄，直到您在/ImportExportDevicesSample （其中 ImportExportDevicesSample .csproj 檔案存在）。 然後輸入下列命令列引數，並將其包含在內。

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
    dotnet run arg1 arg2 arg3 arg4 arg5
    ```

    Dotnet 命令會建立並執行應用程式。 因為您在執行應用程式時傳入選項，所以您可以在每次執行應用程式時變更它們的值。 例如，您可能想要只執行一次，然後建立新的裝置，再重新執行並將這些裝置複製到新的中樞，依此類推。 您也可以執行相同回合中的所有步驟，不過，除非您確定已完成複製，否則建議您不要刪除任何裝置。 以下範例會建立1000裝置，然後將它們複製到另一個中樞。

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub or delete them. 
    dotnet run true 1000 false false false 

    // Do not add any devices. Copy the ones you just created to the other hub; don't delete anything.
    dotnet run false 0 true false false 
    ```

    在您確認已成功複製裝置之後，您可以從來源中樞移除裝置，如下所示：

   ``` console
   // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
   // Delete the devices from the source hub.
   dotnet run false 0 false true false 
   ```

### <a name="running-the-sample-application-using-visual-studio"></a>使用 Visual Studio 執行範例應用程式

1. 如果您想要在 Visual Studio 中執行應用程式，請將目前的目錄變更為 IoTHubServiceSamples .sln 檔案所在的資料夾。 然後在 [命令提示字元] 視窗中執行此命令，以在 Visual Studio 中開啟方案。 您必須在設定環境變數的同一個命令視窗中執行此動作，以便知道這些變數。

   ``` console       
   IoTHubServiceSamples.sln
   ```
    
1. 以滑鼠右鍵按一下 [專案*ImportExportDevicesSample* ]，然後選取 [**設定為啟始專案**]。    
    
1. 將 ImportExportDevicesSample 資料夾中 Program.cs 頂端的變數設定為五個選項。

   ``` csharp
   // Add randomly created devices to the source hub.
   private static bool addDevices = true;
   //If you ask to add devices, this will be the number added.
   private static int numToAdd = 0; 
   // Copy the devices from the source hub to the destination hub.
   private static bool copyDevices = false;
   // Delete all of the devices from the source hub. (It uses the IoTHubConnectionString).
   private static bool deleteSourceDevices = false;
   // Delete all of the devices from the destination hub. (Uses the DestIotHubConnectionString).
   private static bool deleteDestDevices = false;
   ```

1. 選取 F5 以執行應用程式。 完成執行之後，您就可以查看結果。

### <a name="view-the-results"></a>View the results 

您可以在[Azure 入口網站](https://portal.azure.com)中查看裝置，並確認它們位於新的位置。

1. 使用[Azure 入口網站](https://portal.azure.com)移至新的中樞。 選取您的中樞，然後選取 [ **IoT 裝置**]。 您會看到剛從舊中樞複製到複製的中樞的裝置。 您也可以查看複製的中樞的屬性。 

1. 前往 [ [Azure 入口網站](https://portal.azure.com)中的 [Azure 儲存體帳戶]，然後查看的容器，以檢查匯入/匯出錯誤 `devicefiles` `ImportErrors.log` 。 如果此檔案是空的（大小為0），則不會發生任何錯誤。 如果您嘗試多次匯入相同的裝置，它會第二次拒絕裝置，並將錯誤訊息新增至記錄檔。

### <a name="committing-the-changes"></a>認可變更 

此時，您已將中樞複製到新的位置，並將裝置遷移至新的複本。 現在您需要進行變更，讓裝置能與複製的中樞搭配使用。

若要認可變更，以下是您需要執行的步驟： 

* 更新每部裝置以變更 IoT 中樞主機名稱，以將 IoT 中樞主機名稱指向新的中樞。 您應該使用第一次布建裝置時所使用的相同方法來執行此動作。

* 變更參照舊中樞的任何應用程式，以指向新的中樞。

* 完成之後，新的中樞應該會啟動並執行。 舊中樞不應該有作用中的裝置，且處於中斷連線的狀態。 

### <a name="rolling-back-the-changes"></a>回復變更

如果您決定復原變更，以下是要執行的步驟：

* 更新每部裝置，將 IoT 中樞主機名稱變更為指向舊中樞的 IoT 中樞主機名稱。 您應該使用第一次布建裝置時所使用的相同方法來執行此動作。

* 變更參照新中樞的任何應用程式，以指向舊的中樞。 例如，如果您使用 Azure 分析，您可能需要重新設定[azure 串流分析輸入](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub)。

* 刪除新的中樞。 

* 如果您有路由資源，舊中樞上的設定仍應指向正確的路由設定，而且應該在中樞重新開機之後使用這些資源。

### <a name="checking-the-results"></a>檢查結果 

若要檢查結果，請變更您的 IoT 解決方案，以指向新位置中的中樞並加以執行。 換句話說，您可以使用與先前中樞執行的新中樞來執行相同的動作，並確定其運作正常。 

如果您已執行路由，請進行測試，並確定您的訊息已正確地路由傳送至資源。

## <a name="clean-up"></a>清除

請不要清除，直到您確定新的中樞已啟動並執行，且裝置運作正常。 如果您使用該功能，也請務必測試路由。 當您準備好時，請執行下列步驟來清除舊的資源：

* 如果您還沒有這麼做，請刪除舊的中樞。 這會從中樞移除所有作用中裝置。

* 如果您有已移至新位置的路由資源，您可以刪除舊的路由資源。

## <a name="next-steps"></a>後續步驟

您已將 IoT 中樞複製到新區域中的新中樞，並使用裝置完成。 如需有關在 IoT 中樞中針對身分識別登錄執行大量作業的詳細資訊，請參閱[大量匯入和匯出 IoT 中樞裝置](iot-hub-bulk-identity-mgmt.md)身分識別。

如需有關中樞 IoT 中樞和開發的詳細資訊，請參閱下列文章。

* [IoT 中樞開發人員指南](iot-hub-devguide.md)

* [IoT 中樞路由教學課程](tutorial-routing.md)

* [IoT 中樞裝置管理總覽](iot-hub-device-management-overview.md)

* 如果您想要部署範例應用程式，請參閱[.Net Core 應用程式部署](https://docs.microsoft.com/dotnet/core/deploying/index)。
