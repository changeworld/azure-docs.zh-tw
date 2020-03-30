---
title: 如何克隆 Azure IoT 中心
description: 如何克隆 Azure IoT 中心
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: robinsh
ms.openlocfilehash: c54853717f7e0b234df013e5aee575682d0d3d97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75429160"
---
# <a name="how-to-clone-an-azure-iot-hub-to-another-region"></a>如何將 Azure IoT 中心克隆到另一個區域

本文探討了克隆 IoT 中心的方法，並提供了在開始之前需要回答的一些問題。 以下是您可能想要克隆 IoT 中心的幾個原因：
 
* 您將公司從一個區域移動到另一個區域，例如從歐洲到北美（反之亦然），並且希望資源和資料在地理位置上靠近新位置，因此您需要移動中心。

* 您正在為開發與生產環境設置中心。

* 您希望執行多中心高可用性的自訂實現。 有關詳細資訊，請參閱[如何實現 IoT 中心高可用性和災害復原的跨區域 HA 部分](iot-hub-ha-dr.md#achieve-cross-region-ha)。

* 您希望增加為中心配置[的分區](iot-hub-scaling.md#partitions)數。 這是首次創建集線器時設置的，無法更改。 您可以使用本文中的資訊來克隆中心，創建克隆時，會增加分區數。

要克隆中心，您需要具有對原始中心的管理存取權限的訂閱。 您可以將新中心放在新資源組和地區、與原始中心相同的訂閱中，甚至可以放在新訂閱中。 不能使用相同的名稱，因為中心名稱必須是全域唯一的。

> [!NOTE]
> 此時，沒有可用於自動克隆 IoT 中心的功能。 它主要是一個手動過程，因此相當容易出錯。 克隆中心的複雜性與中心的複雜性成正比。 例如，克隆沒有消息路由的 IoT 中心相當簡單。 如果將消息路由添加為一個複雜性，則克隆中心將至少變得複雜。 如果還移動用於路由終結點的資源，則這是另一個更複雜的放大順序。 

## <a name="things-to-consider"></a>考量事項

在克隆 IoT 中心之前，需要考慮以下幾點。

* 確保原始位置中的所有可用功能在新位置也可用。 某些服務處於預覽狀態，並非所有功能都隨處可見。

* 在創建和驗證克隆版本之前，不要刪除原始資源。 刪除集線器後，它將永遠消失，並且無法恢復它來檢查設置或資料以確保中心被正確複製。

* 許多資源需要全域唯一名稱，因此必須為克隆版本使用不同的名稱。 對於克隆中心所屬的資源組，還應使用不同的名稱。 

* 不會遷移原始 IoT 中心的資料。 這包括遙測消息、雲到設備 （C2D） 命令以及與作業相關的資訊（如計畫和歷史記錄）。 也不會遷移指標和日誌記錄結果。 

* 對於路由到 Azure 存儲的資料或消息，可以將資料保留在原始存儲帳戶中，將資料傳輸到新區域中的新存儲帳戶，或者保留舊資料，並在新資料的新位置創建新存儲帳戶。 有關在 Blob 存儲中移動資料的詳細資訊，請參閱[使用 AzCopy 開始](../storage/common/storage-use-azcopy-v10.md)。

* 無法遷移事件中心和服務匯流排主題和佇列的資料。 這是時間點資料，在消息處理後不會存儲。

* 您需要為遷移安排停機時間。 將設備克隆到新集線器需要時間。 如果使用導入/匯出方法，基準測試顯示移動 500，000 台設備可能需要大約兩個小時，移動 100 萬台設備可能需要 4 小時。 

* 您可以將設備複製到新集線器，而無需關閉或更改設備。 

    * 如果設備最初使用 DPS 預配，則重新預配設備會更新存儲在每台設備中的連接資訊。 
    
    * 否則，您必須使用導入/匯出方法來行動裝置，然後必須修改設備才能使用新集線器。 例如，您可以將設備設置為使用來自雙所需屬性的 IoT 中心主機名稱。 設備將獲取該 IoT 中心主機名稱，斷開設備與舊中心的連接，並將其重新連接到新集線器。
    
* 您需要更新正在使用的任何證書，以便將其與新資源一起使用。 此外，您可能在某處的 DNS 表中定義了中心 - 您需要更新該 DNS 資訊。

## <a name="methodology"></a>方法

這是我們建議將 IoT 中心從一個區域移動到另一個區域的一般方法。 對於消息路由，這假定資源未移動到新區域。 有關詳細資訊，請參閱[消息路由 部分](#how-to-handle-message-routing)。

   1. 將中心及其設置匯出到資源管理器範本。 
   
   1. 對範本進行必要的更改，例如更新名稱的所有匹配項和克隆中心的位置。 對於範本中用於消息路由終結點的任何資源，請更新該資源範本中的鍵。
   
   1. 將範本導入新位置的新資源組。 這將創建克隆。

   1. 根據需要進行調試。 
   
   1. 添加未匯出到範本的任何內容。 
   
       例如，消費者組不會匯出到範本。 您需要手動將消費者組添加到範本中，或在創建中心後使用[Azure 門戶](https://portal.azure.com)。 在"[使用 Azure 資源管理器範本配置 IoT 中心消息路由](tutorial-routing-config-message-routing-rm-template.md)"中，有一個示例將一個消費者組添加到範本中。
       
   1. 將設備從原創組線器複製到克隆。 這部分在[管理註冊到 IoT 中心的設備](#managing-the-devices-registered-to-the-iot-hub)仲介紹。

## <a name="how-to-handle-message-routing"></a>如何處理消息路由

如果中心使用[自訂路由](iot-hub-devguide-messages-read-custom.md)，則匯出集線器的範本包括路由配置，但它不包括資源本身。 您必須選擇是將路由資源移動到新位置，還是將它們保留到位，並繼續"原樣"使用它們。 

例如，假設您在美國西部有一個中心，該中心正在將郵件路由到存儲帳戶（也在美國西部），並且您希望將該集線器移動到美國東部。 您可以移動集線器，使其仍將消息路由到美國西部的存儲帳戶，也可以移動集線器，也可以移動存儲帳戶。 從路由消息到不同區域中的終結點資源，性能可能會受到很小的打擊。

如果不移動用於路由終結點的資源，則可以非常輕鬆地移動使用消息路由的中心。 

如果中心使用消息路由，則有兩種選擇。 

1. 將用於路由終結點的資源移動到新位置。

    * 您必須在[Azure 門戶](https://portal.azure.com)中手動或使用資源管理器範本自行創建新資源。 

    * 在新位置創建資源時，必須重命名所有資源，因為它們具有全域唯一名稱。 
     
    * 在創建新中心之前，必須更新新中心範本中的資源名稱和資源鍵。 創建新中心時，資源應存在。

1. 不要移動用於路由終結點的資源。 "就地"使用它們。

   * 在編輯範本的步驟中，您需要檢索每個路由資源的鍵，並在創建新中心之前將它們放入範本中。 

   * 中心仍引用原始路由資源，並在配置時將消息路由到它們。

   * 由於中心和路由終結點資源不在同一位置，因此性能將受到小影響。

## <a name="prepare-to-migrate-the-hub-to-another-region"></a>準備將中心遷移到其他區域

本節提供有關遷移中心的特定說明。

### <a name="find-the-original-hub-and-export-it-to-a-resource-template"></a>查找原始中心並將其匯出到資源範本。

1. 登錄到[Azure 門戶](https://portal.azure.com)。 

1. 轉到**資源組**並選擇包含要移動的中心的資源組。 您還可以轉到**參考資源**，然後以這種方式查找中心。 選擇中心。

1. 從中心的屬性和設置清單中選擇 **"匯出範本**"。 

   ![顯示用於匯出 IoT 中心範本的命令的螢幕截圖。](./media/iot-hub-how-to-clone/iot-hub-export-template.png)

1. 選擇 **"下載**"以下載範本。 將檔保存在可以再次找到它的地方。 

   ![顯示下載 IoT 中心範本的命令的螢幕截圖。](./media/iot-hub-how-to-clone/iot-hub-download-template.png)

### <a name="view-the-template"></a>檢視範本 

1. 轉到"下載"資料夾（或匯出範本時使用哪個資料夾），然後查找 ZIP 檔案。 打開 ZIP 檔案並找到名為`template.json`的檔。 選擇它，然後選擇 Ctrl_C 以複製範本。 轉到不在 ZIP 檔案中的不同資料夾，然後粘貼檔 （Ctrl+V）。 現在，您可以對其進行編輯。
 
    以下示例適用于沒有路由配置的通用集線器。 它是一個S1層集線器（有1個單位），**在西部地區**稱為**ContosoTestHub29358。** 下面是匯出的範本。

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

在使用範本創建新區域中的新中心之前，必須進行一些更改。 使用[VS 代碼](https://code.visualstudio.com)或文字編輯器編輯範本。

#### <a name="edit-the-hub-name-and-location"></a>編輯中心名稱和位置

1. 刪除頂部的參數部分 - 只需使用中心名稱就簡單得多，因為我們不會有多個參數。 

    ``` json
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
    ```

1. 更改名稱以使用實際 （新） 名稱，而不是從參數（在上一步中刪除該參數）檢索它。 

    對於新中心，使用原始中心的名稱加上字串*克隆*來組成新名稱。 首先清理中心名稱和位置。
    
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

    接下來，您將發現**路徑**的值包含舊中心名稱。 更改它們以使用新。 這些是**事件HubEndpoints**下的路徑值，稱為**事件****和操作監視事件**。

    完成後，事件中心終結點部分應如下所示：

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

#### <a name="update-the-keys-for-the-routing-resources-that-are-not-being-moved"></a>更新未移動的路由資源的鍵

當您匯出配置路由的集線器的資源管理器範本時，您將看到這些資源的金鑰未在匯出的範本中提供 -- 它們的位置用星號表示。 在導入新中心範本並創建中心**之前**，必須訪問門戶中的這些資源並檢索金鑰來填充這些資源。 

1. 檢索任何路由資源所需的金鑰，並將其放入範本中。 可以從[Azure 門戶](https://portal.azure.com)中的資源檢索金鑰。 

   例如，如果要將消息路由到存儲容器，則在門戶中查找存儲帳戶。 在"設置"部分下，選擇 **"便捷鍵**"，然後複製其中一個鍵。 以下是首次匯出範本時金鑰的外觀：

   ```json
   "connectionString": "DefaultEndpointsProtocol=https;
   AccountName=fabrikamstorage1234;AccountKey=****",
   "containerName": "fabrikamresults",
   ```

1. 檢索存儲帳戶的帳戶金鑰後，將其放在子句`AccountKey=****`中的範本中，以代替星號。 

1. 對於服務匯流排佇列，獲取與共享 AccessKeyName 匹配的共用訪問金鑰。 下面是 json 中`SharedAccessKeyName`的鑰匙和：

   ```json
   "connectionString": "Endpoint=sb://fabrikamsbnamespace1234.servicebus.windows.net:5671/;
   SharedAccessKeyName=iothubroutes_FabrikamResources;
   SharedAccessKey=****;
   EntityPath=fabrikamsbqueue1234",
   ```

1. 這同樣適用于服務匯流排主題和事件中心連接。

#### <a name="create-the-new-routing-resources-in-the-new-location"></a>在新位置創建新的路由資源

僅當移動中心用於路由終結點的資源時，此部分才適用。

如果要移動路由資源，則必須在新位置手動設置資源。 可以使用[Azure 門戶](https://portal.azure.com)創建路由資源，也可以通過匯出消息路由所使用的每個資源的資源管理器範本、編輯它們和導入它們來創建它們。 設置資源後，可以導入中心範本（包括路由配置）。

1. 創建路由使用的每個資源。 可以使用[Azure 門戶](https://portal.azure.com)手動執行此操作，或使用資源管理器範本創建資源。 如果要使用範本，請執行以下步驟：

    1. 對於路由使用的每個資源，將其匯出到資源管理器範本。
    
    1. 更新資源的名稱和位置。 

    1. 更新資源之間的任何交叉引用。 例如，如果為新存儲帳戶創建範本，則需要更新該範本中存儲帳戶名稱以及引用該範本的任何其他範本。 在大多數情況下，中心範本中的路由部分是引用資源的唯一其他範本。 

    1. 導入部署每個資源的每個範本。

    設置並運行路由使用的資源後，可以繼續。

1. 在 IoT 中心範本中，將每個路由資源的名稱更改為其新名稱，並根據需要更新位置。 

現在，您有一個範本，它將創建一個與舊中心幾乎完全一樣的新中心，具體取決於您決定如何處理路由。

## <a name="move----create-the-new-hub-in-the-new-region-by-loading-the-template"></a>移動 -- 通過載入範本創建新區域中的新中心

使用範本在新位置創建新集線器。 如果路由資源要移動，則應在新位置設置資源，並將範本中的引用更新為匹配。 如果不移動路由資源，它們應位於範本中，並帶有更新的鍵。

1. 登錄到[Azure 門戶](https://portal.azure.com)。

1. 選擇 **"創建資源**"。 

1. 在搜索框中，放入"範本部署"並選擇"輸入"。

1. 選擇**範本部署（使用自訂範本部署）。** 這將帶您到範本部署的螢幕。 選取 [建立]****。 您會看到這個畫面：

   ![顯示構建您自己的範本的命令的螢幕截圖](./media/iot-hub-how-to-clone/iot-hub-custom-deployment.png)

1. 在**編輯器中選擇"構建您自己的範本**"，這使您能夠從檔上載範本。 

1. 選擇 **"載入檔**"。 

   ![顯示上載範本檔的命令的螢幕截圖](./media/iot-hub-how-to-clone/iot-hub-upload-file.png)

1. 流覽您編輯的新範本並選擇它，然後選擇 **"打開**"。 它將範本載入到編輯視窗中。 選取 [儲存]****。 

   ![顯示載入範本的螢幕截圖](./media/iot-hub-how-to-clone/iot-hub-loading-template.png)

1. 填寫下列欄位。

   **訂閱**：選擇要使用的訂閱。

   **資源組**：在新位置創建新資源組。 如果已設置新設置，則可以選擇它，而不是創建新設置。

   **位置**：如果選擇了現有資源組，則填充此資源組以匹配資源組的位置。 如果創建新的資源組，這將是其位置。

   **我同意核取方塊**：這基本上表明你同意支付你創造的資源。

1. 選擇 **"購買**"按鈕。

門戶現在驗證範本並部署克隆中心。 如果您有路由配置資料，它將包含在新中心中，但將指向前一個位置的資源。

## <a name="managing-the-devices-registered-to-the-iot-hub"></a>管理註冊到 IoT 中心的設備

現在，您已經啟動並運行了克隆，您需要將所有設備從原始中心複製到克隆。 

有多種方法可以實現此目的。 您最初使用[設備預配服務 （DPS）](/azure/iot-dps/about-iot-dps)來預配設備，或者您沒有。 如果你這樣做了，這並不難。 如果沒有，這可能非常複雜。 

如果未使用 DPS 預配設備，則可以跳過下一節，然後從[使用導入/匯出開始將設備移動到新中心](#using-import-export-to-move-the-devices-to-the-new-hub)。

## <a name="using-dps-to-re-provision-the-devices-in-the-new-hub"></a>使用 DPS 重新配置新集線器中的設備

要使用 DPS 將設備移動到新位置，請參閱[如何重新預配設備](../iot-dps/how-to-reprovision.md)。 完成後，可以在[Azure 門戶](https://portal.azure.com)中查看設備並驗證它們位於新位置。

使用[Azure 門戶](https://portal.azure.com)轉到新中心。 選擇集線器，然後選擇**IoT 設備**。 您將看到重新預配到克隆中心的設備。 您還可以查看克隆中心的屬性。 

如果已實現路由，請測試並確保消息正確路由到資源。

### <a name="committing-the-changes-after-using-dps"></a>使用 DPS 後提交更改

此更改已由 DPS 服務提交。

### <a name="rolling-back-the-changes-after-using-dps"></a>使用 DPS 後回滾更改。 

如果要回滾更改，將設備從新集線器重新調配到舊集線器。

您現在已完成遷移集線器及其設備。 您可以跳到[清理](#clean-up)。

## <a name="using-import-export-to-move-the-devices-to-the-new-hub"></a>使用導入匯出將設備移動到新中心

應用程式以 .NET Core 為目標，因此您可以在 Windows 或 Linux 上運行它。 您可以下載示例、檢索連接字串、設置要為其運行位的標誌，然後運行該示例。 無需打開代碼即可執行此操作。

### <a name="downloading-the-sample"></a>下載範例

1. 使用此頁面中的 IoT C# 示例[：C# 的 Azure IoT 示例](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/)。 下載 ZIP 檔案並將其解壓縮到您的電腦上。 

1. 相關代碼位於 ./iot-hub/示例/服務/匯入裝置示例中。 您無需查看或編輯代碼才能運行應用程式。

1. 要運行應用程式，請指定三個連接字串和五個選項。 將此資料作為命令列參數傳遞或使用環境變數，或使用兩者的組合。 我們將將選項作為命令列參數傳遞，將連接字串作為環境變數傳遞。 

   這是因為連接字串很長且笨拙，不太可能更改，但您可能希望更改選項並多次運行應用程式。 要更改環境變數的值，必須關閉命令視窗和 Visual Studio 或 VS 代碼（以您消費者為准）。 

### <a name="options"></a>選項。

以下是運行應用程式時指定的五個選項。 我們將在一分鐘內將這些放在命令列上。

*   **添加設備**（參數 1） -- 如果要添加為您生成的虛擬裝置，將其設置為 true。 這些將添加到源中心。 此外，將**numToAdd（** 參數 2）設置為指定要添加的設備數。 可以註冊到集線器的設備的最大數量為 100 萬台。此選項的目的是用於測試 -- 您可以生成特定數量的設備，然後將它們複製到另一個中心。

*   **copyDevices（** 參數 3） -- 將其設置為 true 以將設備從一個集線器複製到另一個集線器。 

*   **刪除SourceDevices（** 參數 4） -- 將其設置為 true 以刪除註冊到源中心的所有設備。 我們建議您等到您確定所有設備都已傳輸之前，才能運行此項。 刪除設備後，無法將其找回。

*   **刪除"刪除設備**"（參數 5） -- 將其設置為 true 以刪除註冊到目標中心（克隆）的所有設備。 如果要多次複製設備，則可能需要執行此操作。 

基本命令將是*dotnet 運行*-- 這告訴 .NET 生成本地 csproj 檔，然後運行它。 在運行命令列參數之前，將命令列參數添加到末尾。 

命令列將類似于以下示例：

``` console 
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub, or delete them. 
    // The first argument is true, numToAdd is 50, and the other arguments are false.
    dotnet run true 1000 false false false 

    // Copy the devices you just added to the other hub; don't delete anything.
    // The first argument is false, numToAdd is 0, copy-devices is true, and the delete arguments are both false
    dotnet run false 0 true false false 
```

### <a name="using-environment-variables-for-the-connection-strings"></a>對連接字串使用環境變數

1. 要運行該示例，您需要連接到新舊 IoT 中心的連接字串，以及可用於臨時工作檔的存儲帳戶。 我們將在環境變數中存儲這些值。

1. 要獲取連接字串值，請登錄到 Azure[門戶](https://portal.azure.com)。 

1. 將連接字串放在可以檢索它們的地方，如 NotePad。 如果複製以下內容，則可以將連接字串直接粘貼到它們所在的位置。 不要在等號周圍添加空格，否則更改變數名稱。 此外，連接字串周圍不需要雙引號。 如果在存儲帳戶連接字串周圍放置引號，則它不起作用。

   對於 Windows，這是設置環境變數的方式：

   ``` console  
   SET IOTHUB_CONN_STRING=<put connection string to original IoT Hub here>
   SET DEST_IOTHUB_CONN_STRING=<put connection string to destination or clone IoT Hub here>
   SET STORAGE_ACCT_CONN_STRING=<put connection string to the storage account here>
   ```
 
   對於 Linux，這是定義環境變數的方式：

   ``` console  
   export IOTHUB_CONN_STRING="<put connection string to original IoT Hub here>"
   export DEST_IOTHUB_CONN_STRING="<put connection string to destination or clone IoT Hub here>"
   export STORAGE_ACCT_CONN_STRING="<put connection string to the storage account here>"
   ```

1. 對於 IoT 中心連接字串，轉到門戶中的每個中心。 您可以在 **"資源"** 中搜索中心。 如果知道資源組，可以轉到**資源組**，選擇資源組，然後從該資源組中的資產清單中選擇中心。 

1. 從中心的"設置"中選擇 **"共用訪問策略**"，然後選擇**iothub 擁有者**並複製其中一個連接字串。 對目標中心執行相同的操作。 將它們添加到相應的 SET 命令。

1. 對於存儲帳戶連接字串，在**參考資料**中查找存儲帳戶或其**資源組**並打開它。 
   
1. 在"設置"部分下，選擇 **"便捷鍵"** 並複製其中一個連接字串。 將連接字串放在文字檔中，用於相應的 SET 命令。 

現在，您擁有具有 SET 命令的檔中的環境變數，並且知道命令列參數是什麼。 讓我們運行示例。

### <a name="running-the-sample-application-and-using-command-line-arguments"></a>運行應用程式範例並使用命令列參數

1. 開啟 [命令提示字元] 視窗。 選擇"視窗"並`command prompt`鍵入以獲取命令提示視窗。

1. 複製設置環境變數的命令（一次一個，並將它們粘貼到命令提示視窗中，然後選擇 Enter）。 完成後，鍵入`SET`命令提示視窗以查看環境變數及其值。 將它們複製到命令提示視窗中後，除非打開新的命令提示視窗，否則不必再次複製它們。

1. 在命令提示視窗中，更改目錄，直到您輸入 ./ImportExportDeviceSample（其中存在 ImportExportDeviceSample.csproj 檔）。 然後鍵入以下內容，並包括命令列參數。

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
    dotnet run arg1 arg2 arg3 arg4 arg5
    ```

    點網命令生成並運行應用程式。 由於在運行應用程式時傳入選項，因此每次運行應用程式時都可以更改這些選項的值。 例如，您可能希望運行一次並創建新設備，然後再次運行它並將這些設備複製到新集線器，等等。 您還可以在同一運行中執行所有步驟，但我們建議您在確定完成克隆之前不要刪除任何設備。 下面是創建 1000 台設備然後將其複製到其他中心的示例。

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub or delete them. 
    dotnet run true 1000 false false false 

    // Do not add any devices. Copy the ones you just created to the other hub; don't delete anything.
    dotnet run false 0 true false false 
    ```

    驗證設備是否成功複製後，可以從源集線器中刪除設備，如下所示：

   ``` console
   // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
   // Delete the devices from the source hub.
   dotnet run false 0 false true false 
   ```

### <a name="running-the-sample-application-using-visual-studio"></a>使用 Visual Studio 運行應用程式範例

1. 如果要在 Visual Studio 中運行應用程式，請將目前的目錄更改為 IoTHubServiceSample.sln 檔所在的資料夾。 然後在命令提示視窗中運行此命令，以在 Visual Studio 中打開解決方案。 您必須在設置環境變數的同一命令視窗中執行此操作，因此這些變數是已知的。

   ``` console       
   IoTHubServiceSamples.sln
   ```
    
1. 按右鍵專案*ImportExportDeviceSample*並選擇 **"設置為啟動專案**"。    
    
1. 將五個選項的變數設置為"導入匯出裝置示例"資料夾中Program.cs的頂部。

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

1. 選擇 F5 來運行應用程式。 運行完成後，您可以查看結果。

### <a name="view-the-results"></a>View the results 

您可以在[Azure 門戶](https://portal.azure.com)中查看設備並驗證它們位於新位置。

1. 使用[Azure 門戶](https://portal.azure.com)轉到新中心。 選擇集線器，然後選擇**IoT 設備**。 您將看到剛剛從舊集線器複製到克隆中心的設備。 您還可以查看克隆中心的屬性。 

1. 通過訪問[Azure 門戶](https://portal.azure.com)中的 Azure 存儲帳戶並在`devicefiles`容器中查找 的`ImportErrors.log`導入/匯出錯誤。 如果此檔為空（大小為 0），則沒有錯誤。 如果多次嘗試導入同一設備，則會第二次拒絕該設備，並將錯誤訊息添加到日誌檔中。

### <a name="committing-the-changes"></a>提交更改 

此時，您已將集線器複製到新位置，並將設備遷移到新克隆。 現在，您需要進行更改，以便設備使用克隆的集線器。

要提交更改，您需要執行以下步驟： 

* 更新每個設備以更改 IoT 中心主機名稱，將 IoT 中心主機名稱指向新中心。 應使用首次預配設備時使用的方法執行此操作。

* 更改引用舊中心以指向新中心的任何應用程式。

* 完成後，新集線器應啟動並運行。 舊集線器應沒有活動設備，並且處於斷開連接狀態。 

### <a name="rolling-back-the-changes"></a>回滾更改

如果您決定回滾更改，下面是執行的步驟：

* 更新每個設備以更改 IoT 中心主機名稱，以指向舊中心的 IoT 中心主機名稱。 應使用首次預配設備時使用的方法執行此操作。

* 更改引用新中心以指向舊中心的任何應用程式。 例如，如果使用 Azure 分析，則可能需要重新配置[Azure 流分析輸入](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub)。

* 刪除新中心。 

* 如果您有路由資源，則舊集線器上的配置仍應指向正確的路由配置，並且應在重新開機集線器後使用這些資源。

### <a name="checking-the-results"></a>檢查結果 

要檢查結果，請更改 IoT 解決方案以指向新位置的集線器並運行它。 換句話說，對使用前一個集線器執行的新中心執行相同的操作，並確保它們正常工作。 

如果已實現路由，請測試並確保消息正確路由到資源。

## <a name="clean-up"></a>清除

在真正確定新集線器已啟動並運行且設備工作正常之前，不要進行清理。 如果正在使用該功能，請確保測試路由。 準備就緒後，請執行以下步驟清理舊資源：

* 如果尚未刪除舊中心。 這將從集線器中刪除所有活動設備。

* 如果您有移動到新位置的路由資源，則可以刪除舊的路由資源。

## <a name="next-steps"></a>後續步驟

您已將 IoT 中心克隆為新區域中的新中心，並配有設備。 有關針對 IoT 中心中的標識註冊表執行批量操作的詳細資訊，請參閱[大量匯入和匯出 IoT 中心設備標識](iot-hub-bulk-identity-mgmt.md)。

有關 IoT 中心和中心開發的詳細資訊，請參閱以下文章。

* [IoT 中心開發人員指南](iot-hub-devguide.md)

* [IoT 中心路由教程](tutorial-routing.md)

* [IoT 中心裝置管理概述](iot-hub-device-management-overview.md)

* 如果要部署應用程式範例，請參閱[.NET Core 應用程式部署](https://docs.microsoft.com/dotnet/core/deploying/index)。
