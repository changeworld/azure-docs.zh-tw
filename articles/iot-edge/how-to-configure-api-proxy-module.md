---
title: 設定 API proxy 模組-Azure IoT Edge |Microsoft Docs
description: 瞭解如何自訂 IoT Edge 閘道階層的 API proxy 模組。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 894398d63e326db3c6ee9de9bebc426a6e621600
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024665"
---
# <a name="configure-the-api-proxy-module-for-your-gateway-hierarchy-scenario-preview"></a>為您的閘道階層案例 (預覽版設定 API proxy 模組) 

API proxy 模組可讓 IoT Edge 裝置透過閘道傳送 HTTP 要求，而不是直接連接到雲端服務。 本文將逐步解說設定選項，讓您可以自訂模組以支援您的閘道階層需求。

>[!NOTE]
>這項功能需要目前處於公開預覽狀態，且執行 Linux 容器的 IoT Edge 1.2 版本。

在某些網路架構中，閘道後方的 IoT Edge 裝置無法直接存取雲端。 任何嘗試連接到雲端服務的模組都會失敗。 API proxy 模組可透過將模組連線重新路由傳送至閘道階層的各層，來支援此設定中的下游 IoT Edge 裝置。 它提供安全的方式，讓用戶端透過 HTTPS 與多個服務進行通訊，而不需要進行通道處理，而是在每一層終止連接。 API proxy 模組可作為閘道階層中 IoT Edge 裝置之間的 proxy 模組，直到到達最上層的 IoT Edge 裝置為止。 屆時，在最上層執行的服務 IoT Edge 裝置處理這些要求，且 API proxy 模組會透過單一端口，將來自本機服務的所有 HTTP 流量 proxy 到雲端。

API proxy 模組可以針對閘道階層啟用許多案例，包括允許較低層的裝置提取容器映射或將 blob 推送至儲存體。 Proxy 規則的設定會定義資料的路由方式。 本文討論數個常見的設定選項。

## <a name="deploy-the-proxy-module"></a>部署 proxy 模組

API proxy 模組可從 Microsoft Container Registry (MCR) ： `mcr.microsoft.com/azureiotedge-api-proxy:latest` 。

您也可以直接從 Azure Marketplace： [IOT EDGE Api proxy](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview)部署 api proxy 模組。

## <a name="understand-the-proxy-module"></a>瞭解 proxy 模組

API proxy 模組利用 nginx 的反向 proxy，透過網路層來路由資料。 Proxy 內嵌在模組中，這表示模組映射需要支援 proxy 設定。 例如，如果 proxy 正在接聽特定埠，則模組必須開啟該埠。

Proxy 會以內嵌于模組中的預設設定檔開始。 您可以使用其 [模組](../iot-hub/iot-hub-devguide-module-twins.md)對應項，將新的設定傳遞至雲端中的模組。 此外，您可以在部署期間使用環境變數來開啟或關閉設定。

本文將先討論預設的設定檔，以及如何使用環境變數來啟用其設定。 接下來，我們會討論自訂設定檔的結尾。

### <a name="default-configuration"></a>預設組態

API proxy 模組隨附的預設設定可支援常見案例，並允許進行自訂。 您可以透過模組的環境變數來控制預設設定。

目前，預設環境變數包括：

| 環境變數 | 描述 |
| -------------------- | ----------- |
| `PROXY_CONFIG_ENV_VAR_LIST` | 以逗號分隔的清單，列出您想要更新的所有變數。 此步驟可防止不小心修改錯誤的設定。
| `NGINX_DEFAULT_PORT` | 變更 nginx proxy 接聽的埠。 如果您更新此環境變數，請確定您選取的埠也會在模組 dockerfile 中公開，並在部署資訊清單中宣告為埠系結。<br><br>預設值為443。<br><br>從 Azure Marketplace 部署時，預設埠會更新為8000，以防止與 edgeHub 模組發生衝突。 如需詳細資訊，請參閱 [最小化開啟的埠](#minimize-open-ports)。 |
| `DOCKER_REQUEST_ROUTE_ADDRESS` | 路由傳送 docker 要求的位址。 在最上層裝置上修改此變數以指向登錄模組。<br><br>預設值為父主機名稱。 |
| `BLOB_UPLOAD_ROUTE_ADDRESS` | 路由 blob 登錄要求的位址。 在最上層裝置上修改此變數，以指向 blob 儲存體模組。<br><br>預設值為父主機名稱。 |

## <a name="minimize-open-ports"></a>最小化開啟的埠

為了將開啟的埠數目降至最低，API proxy 模組應該將所有的 HTTPS 流量轉送 (埠 443) ，包括以 edgeHub 模組為目標的流量。 預設會設定 API proxy 模組，以重新路由傳送埠443上的所有 edgeHub 流量。

使用下列步驟來設定您的部署，以最小化開啟的埠：

1. 將 edgeHub 模組設定更新為不在埠443上系結，否則會發生埠系結衝突。 根據預設，edgeHub 模組會系結到埠443、5671和8883。 刪除埠443系結，並將其他兩個內容保持在原處：

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. 將 API proxy 模組設定為在埠443上系結。

   1. 將 **NGINX_DEFAULT_PORT** 環境變數的值設定為 `443` 。
   1. 更新容器建立選項，以在埠443上系結。

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

如果您不需要將開啟的埠降至最低，您可以讓 edgeHub 模組使用埠443，並將 API proxy 模組設定為在另一個埠上接聽。 例如，API proxy 模組可以在埠8000上接聽，方法是將 **NGINX_DEFAULT_PORT** 環境變數的值設定為 `8000` ，並建立埠8000的埠系結。

## <a name="enable-container-image-download"></a>啟用容器映射下載

API proxy 模組的常見使用案例是讓較低層的 IoT Edge 裝置提取容器映射。 此案例使用 [Docker 登錄模組](https://hub.docker.com/_/registry) 從雲端取出容器映射，並在最上層進行快取。 API proxy 會轉送所有 HTTPS 要求，以從較低的層級下載容器映射，以供最上層的登錄模組服務。

此案例要求下游 IoT Edge 裝置必須指向功能變數名稱， `$upstream` 後面接著 API Proxy 模組埠號碼，而不是映射的容器登錄。 例如：`$upstream:8000/azureiotedge-api-proxy:1.0`。

本教學課程示範如何 [使用閘道建立 IoT Edge 裝置](tutorial-nested-iot-edge.md)的階層架構。

在 **最上層** 設定下列模組：

* Docker 登錄模組
  * 使用容易記住的 *名稱（例如* 登錄）設定模組，並在模組中公開端口以接收要求。
  * 設定模組以對應至您的容器登錄。
* API proxy 模組
  * 設定下列環境變數：

    | Name | 值 |
    | ---- | ----- |
    | `DOCKER_REQUEST_ROUTE_ADDRESS` | 登錄模組名稱和開啟的埠。 例如： `registry:5000` 。 |
    | `NGINX_DEFAULT_PORT` | Nginx proxy 針對來自下游裝置的要求所接聽的埠。 例如： `8000` 。 |

  * 設定下列 createOptions：

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

在此案例的任何 **較低層** 設定下列模組：

* API proxy 模組
  * 設定下列環境變數：

    | Name | 值 |
    | ---- | ----- |
    | `NGINX_DEFAULT_PORT` | Nginx proxy 針對來自下游裝置的要求所接聽的埠。 例如： `8000` 。 |

  * 設定下列 createOptions：

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```  

## <a name="enable-blob-upload"></a>啟用 blob 上傳

API proxy 模組的另一個使用案例是讓較低層的 IoT Edge 裝置上傳 blob。 此使用案例可在較低層裝置上啟用疑難排解功能，例如上傳模組記錄或上傳支援配套。

此案例會使用最上層 [IoT Edge 模組上的 Azure Blob 儲存體](https://azuremarketplace.microsoft.com/marketplace/apps/azure-blob-storage.edge-azure-blob-storage) 來處理 Blob 的建立和上傳。

在 **最上層** 設定下列模組：

* IoT Edge 模組上的 Azure Blob 儲存體。
* API proxy 模組
  * 設定下列環境變數：

    | Name | 值 |
    | ---- | ----- |
    | `BLOB_UPLOAD_ROUTE_ADDRESS` | Blob 儲存體模組名稱和開啟的埠。 例如： `azureblobstorageoniotedge:1102` 。 |
    | `NGINX_DEFAULT_PORT` | Nginx proxy 針對來自下游裝置的要求所接聽的埠。 例如： `8000` 。 |

  * 設定下列 createOptions：

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

在此案例的任何 **較低層** 設定下列模組：

* API proxy 模組
  * 設定下列環境變數：

    | Name | 值 |
    | ---- | ----- |
    | `NGINX_DEFAULT_PORT` | Nginx proxy 針對來自下游裝置的要求所接聽的埠。 例如： `8000` 。 |

  * 設定下列 createOptions：

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

使用下列步驟，將支援套件組合或記錄檔上傳至位於最上層的 blob 儲存體模組：

1. 使用 Azure 儲存體總管或 REST Api 建立 blob 容器。 如需詳細資訊，請參閱 [IoT Edge 上的 Azure Blob 儲存體將資料儲存在邊緣](how-to-store-data-blob.md)。
1. 根據 [IoT Edge 部署取出記錄](how-to-retrieve-iot-edge-logs.md)檔中的步驟，要求記錄或支援套件組合上傳，但使用功能變數名稱 `$upstream` 和開放式 proxy 埠來取代 blob 儲存體模組位址。 例如：

   ```json
   {
      "schemaVersion": "1.0",
      "sasUrl": "https://$upstream:8000/myBlobStorageName/myContainerName?SAS_key",
      "since": "2d",
      "until": "1d",
      "edgeRuntimeOnly": false
   }
   ```

## <a name="edit-the-proxy-configuration"></a>編輯 proxy 設定

預設設定檔內嵌在 API proxy 模組中，但您可以使用模組對應項，透過雲端將新的設定傳遞給模組。

當您撰寫自己的設定時，仍然可以使用環境來調整每個部署的設定。 使用下列語法：

* 用 `${MY_ENVIRONMENT_VARIABLE}` 來取出環境變數的值。
* 使用條件陳述式，根據環境變數的值開啟或關閉設定：

   ```conf
   #if_tag ${MY_ENVIRONMENT_VARIABLE}
       statement to execute if environment variable evaluates to 1
   #endif_tag ${MY_ENVIRONMENT_VARIABLE}

   #if_tag !${MY_ENVIRONMENT_VARIABLE}
       statement to execute if environment variable evaluates to 0
   #endif_tag !${MY_ENVIRONMENT_VARIABLE}
   ```

當 API proxy 模組剖析 proxy 設定時，它會先使用替代來取代中列出的所有環境變數 `PROXY_CONFIG_ENV_VAR_LIST` 及其提供的值。 然後， `#if_tag` 會取代和配對之間的所有專案 `#endif_tag` 。 接著，模組會將剖析的設定提供給 nginx 反向 proxy。

若要動態更新 proxy 設定，請使用下列步驟：

1. 撰寫您的設定檔。 您可以使用此預設範本作為參考： [nginx_default_config. 會議](https://github.com/Azure/iotedge/blob/master/edge-modules/api-proxy-module/templates/nginx_default_config.conf)
1. 複製設定檔的文字，並將其轉換為 base64。
1. 貼上編碼的設定檔，做為 `proxy_config` 模組對應項中所需屬性的值。

   ![貼上編碼的設定檔做為 proxy_config 屬性的值](./media/how-to-configure-api-proxy-module/change-config.png)

## <a name="next-steps"></a>下一步

使用 API proxy 模組將 [下游 IoT Edge 裝置連線到 Azure IoT Edge 閘道](how-to-connect-downstream-iot-edge-device.md)。